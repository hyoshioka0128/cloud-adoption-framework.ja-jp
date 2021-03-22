---
title: Azure Arc 対応サーバーにインベントリのタグ付けを適用する
description: Azure Arc 対応サーバーを使用してハイブリッド マルチクラウドおよびオンプレミスの環境全体にサーバー インベントリ管理機能を提供する方法について説明します
author: likamrat
ms.author: brblanch
ms.date: 01/29/2021
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: operate
ms.custom: think-tank, e2e-hybrid
ms.openlocfilehash: 25c0ecd913633a80db36cb3eb49eb85db635b290
ms.sourcegitcommit: 9e4bc0e233a24642853f5e8acbeb9746b2444024
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102114593"
---
# <a name="apply-inventory-tagging-to-azure-arc-enabled-servers"></a>Azure Arc 対応サーバーにインベントリのタグ付けを適用する

この記事では、Azure Arc 対応サーバーを使用してハイブリッド マルチクラウドおよびオンプレミスの環境全体にサーバー インベントリ管理機能を提供する方法に関するガイダンスを提供します。

Azure Arc 対応サーバーを使用すると、企業ネットワークや他のクラウド プロバイダー上の Azure の外部でホストされている Windows および Linux のマシンを管理できます。 これは、Azure でネイティブ仮想マシンを管理する方法に似ています。 ハイブリッド マシンは、Azure に接続されると接続済みマシンになり、Azure 内のリソースとして扱われます。 接続済みマシンにはそれぞれリソース ID があり、サブスクリプション内のリソース グループの一部として管理されます。また、Azure Policy やタグの適用などの標準的な Azure のコンストラクトを利用できます。 管理エンジンとして Azure を使用してサーバー インベントリを簡単に整理および管理できることにより、管理の複雑さが大幅に軽減され、ハイブリッドおよびマルチクラウドの環境に一貫した戦略を提供できます。

次の手順では、[Resource Graph エクスプローラー](/azure/governance/resource-graph/first-query-portal)と [Azure CLI](/cli/azure/install-azure-cli) を使用して、Azure の 1 つのペインから複数のクラウドにわたってサーバー インベントリに対するタグ付けおよびクエリを実行する方法を示します。

> [!IMPORTANT]
> この記事の手順では、既に VM、またはオンプレミスや他のクラウドで実行されているサーバーをデプロイ済みで、それらが Azure Arc に接続されていることを前提としています。まだ行っていない場合は、これを自動化するのに次の情報が役立ちます。

- [GCP Ubuntu インスタンス](./gcp-terraform-ubuntu.md)
- [GCP Windows インスタンス](./gcp-terraform-windows.md)
- [AWS Ubuntu EC2 インスタンス](./aws-terraform-ubuntu.md)
- [AWS Amazon Linux 2 EC2 インスタンス](./aws-terraform-al2.md)
- [VMware vSphere Ubuntu VM](./vmware-terraform-ubuntu.md)
- [VMware vSphere Windows Server VM](./vmware-terraform-windows.md)
- [Vagrant Ubuntu box](./local-vagrant-ubuntu.md)
- [Vagrant Windows box](./local-vagrant-windows.md)

## <a name="prerequisites"></a>前提条件

1. Azure Arc Jumpstart リポジトリを複製します。

   ```console
   git clone https://github.com/microsoft/azure_arc
   ```

2. [バージョン 2.7 以降の Azure CLI をインストールするか、更新します](/cli/azure/install-azure-cli)。 次のコマンドを使用して、現在インストールされているバージョンを確認します。

   ```console
   az --version
   ```

## <a name="verify-that-your-azure-arc-connected-servers-are-ready-for-tagging"></a>Azure Arc に接続されたサーバーでタグ付けの準備ができていることを確認する

Resource Graph エクスプローラーを使用して、Azure のリソースに対してクエリを実行し、表示します。

1. Azure portal の上部の検索バーに「**Resource Graph エクスプローラー**」と入力し、それを選択します。

    ![Azure portal の Resource Graph エクスプローラーのスクリーンショット。](./media/inventory-tagging/resource-graph-explorer.png)

1. クエリ ウィンドウで、次のクエリを入力し、 **[クエリの実行]** を選択します。

    ```kusto
    Resources
    | where type =~ 'Microsoft.HybridCompute/machines'
    ```

1. Azure Arc 対応サーバーが正しく作成されている場合は、Resource Graph エクスプローラーの結果ペインに一覧表示されます。 Azure Arc 対応サーバーは、Azure portal からも確認できます。

    ![Resource Graph エクスプローラーのクエリのスクリーンショット。](./media/inventory-tagging/run-query.png)

    ![Azure portal で Azure Arc 対応サーバーの詳細が示されているスクリーンショット。](./media/inventory-tagging/arc-server.png)

## <a name="create-a-basic-azure-tag-taxonomy"></a>基本的な Azure タグ分類を作成する

Azure CLI を開き、次のコマンドを実行して、基本的な分類構造を作成します。これにより、サーバー リソースがホストされている場所 (Azure、AWS、GCP、またはオンプレミス) で簡単にクエリを実行して報告できようになります。 タグ分類の作成の詳細については、「[リソースの名前付けとタグ付けの意思決定ガイド](../../../../decision-guides/resource-tagging/index.md)」を参照してください。

```console
az tag create --name "Hosting Platform"
az tag add-value --name "Hosting Platform" --value "Azure"
az tag add-value --name "Hosting Platform" --value "AWS"
az tag add-value --name "Hosting Platform" --value "GCP"
az tag add-value --name "Hosting Platform" --value "On-premises"
```

!["az tag create" コマンドの出力を示すスクリーンショット。](./media/inventory-tagging/az-tag-create.png)

## <a name="tag-your-azure-arc-resources"></a>Azure Arc リソースにタグを付ける

基本的な分類構造を作成したら、Azure Arc 対応サーバー リソースにタグを適用します。 次の手順では、AWS と GCP の両方におけるリソースのタグ付けを示します。 これらのうち、どちらかのプロバイダーのリソースしかない場合は、AWS または GCP の該当するセクションにスキップできます。

### <a name="tag-the-azure-arc-connected-aws-ubuntu-ec2-instance"></a>Azure Arc に接続された AWS Ubuntu EC2 インスタンスにタグを付ける

CLI で次のコマンドを実行して、AWS の Azure Arc 対応サーバーに `Hosting Platform : AWS` タグを適用します。

> [!NOTE]
> [Azure チュートリアル](./aws-terraform-ubuntu.md)で説明されていない方法を使用して AWS EC2 インスタンスを接続した場合は、`awsResourceGroup` と `awsMachineName` の値を、お使いの環境に固有の値に一致するように調整する必要があります。

```console
export awsResourceGroup="arc-aws-demo"
export awsMachineName="arc-aws-demo"
export awsMachineResourceId="$(az resource show --resource-group $awsResourceGroup --name $awsMachineName --resource-type "Microsoft.HybridCompute/machines" --query id)"
export awsMachineResourceId="$(echo $awsMachineResourceId | tr -d "\"" | tr -d '\r')"
az resource tag --ids $awsMachineResourceId --tags "Hosting Platform"="AWS"
```

!["az resource tag" コマンドの出力を示すスクリーンショット。](./media/inventory-tagging/az-resource-tag-1.png)

### <a name="tag-azure-arc-connected-gcp-ubuntu-server"></a>Azure Arc に接続された GCP Ubuntu サーバーにタグを付ける

CLI で次のコマンドを実行して、GCP の Azure Arc 対応サーバーに `Hosting Platform : GCP` タグを適用します。

> [!NOTE]
> 関連する [Azure Arc の Terraform に関するチュートリアル](./gcp-terraform-ubuntu.md)で説明されていない方法を使用して GCP インスタンスを接続した場合は、`gcpResourceGroup` と `gcpMachineName` の値を、お使いの環境に固有の値に一致するように調整する必要があります。

```console
export gcpResourceGroup="arc-gcp-demo"
export gcpMachineName="arc-gcp-demo"
export gcpMachineResourceId="$(az resource show --resource-group $gcpResourceGroup --name $gcpMachineName --resource-type "Microsoft.HybridCompute/machines" --query id)"
export gcpMachineResourceId="$(echo $gcpMachineResourceId | tr -d "\"" | tr -d '\r')"
az resource tag --resource-group $gcpResourceGroup --ids $gcpMachineResourceId --tags "Hosting Platform"="GCP"
```

!["az resource tag" コマンドの別の出力を示すスクリーンショット。](./media/inventory-tagging/az-resource-tag-2.png)

## <a name="query-resources-by-tag-using-resource-graph-explorer"></a>Resource Graph エクスプローラーを使用して、タグでリソースのクエリを実行する

複数のクラウドでホストされているリソースに対してタグを適用したら、Resource Graph エクスプローラーを使用してクエリを実行し、マルチクラウド ランドスケープに関する分析情報を取得します。

1. クエリ ウィンドウに次のクエリを入力します。

   ```kusto
   Resources
   | where type =~ 'Microsoft.HybridCompute/machines'
   | where isnotempty(tags['Hosting Platform'])
   | project name, location, resourceGroup, tags
   ```

   ![Resource Graph エクスプローラーのクエリの詳細を示すスクリーンショット。](./media/inventory-tagging/run-query-details.png)

2. **[クエリの実行]** をクリックし、 **[書式設定された結果]** トグルを選択します。 正常に実行されると、すべての Azure Arc 対応サーバーとそれに割り当てられた `Hosting Platform` タグ値が表示されます。

   ![Resource Graph エクスプローラーのクエリの結果を示すスクリーンショット。](./media/inventory-tagging/run-query-results.png)

   プロジェクションされたサーバーに対するタグは、Azure portal からも確認できます。

   ![Azure Arc 対応サーバーのタグ セットを示すスクリーンショット。](./media/inventory-tagging/tags-1.png)

   ![Azure Arc 対応サーバーの別のタグ セットを示すスクリーンショット。](./media/inventory-tagging/tags-2.png)

## <a name="clean-up-your-environment"></a>環境をクリーンアップする

ご利用の環境をクリーンアップするには、次の手順を実行します。

1. 各環境から仮想マシンを削除するには、各ガイドに記載されている破棄手順に従ってください。

   - [GCP Ubuntu インスタンス](./gcp-terraform-ubuntu.md)および [GCP Windows インスタンス](./gcp-terraform-windows.md)
   - [AWS Ubuntu EC2 インスタンス](./aws-terraform-ubuntu.md)
   - [VMware vSphere Ubuntu VM](./vmware-terraform-ubuntu.md) および [VMware vSphere Windows Server VM](./vmware-terraform-windows.md)
   - [Vagrant Ubuntu box](./local-vagrant-ubuntu.md) および [Vagrant Windows box](./local-vagrant-windows.md)

1. Azure CLI で次のスクリプトを実行して、このガイドの一環として作成したタグを削除します。

   ```console
   az tag remove-value --name "Hosting Platform" --value "Azure"
   az tag remove-value --name "Hosting Platform" --value "AWS"
   az tag remove-value --name "Hosting Platform" --value "GCP"
   az tag remove-value --name "Hosting Platform" --value "On-premises"
   az tag create --name "Hosting Platform"
   ```
