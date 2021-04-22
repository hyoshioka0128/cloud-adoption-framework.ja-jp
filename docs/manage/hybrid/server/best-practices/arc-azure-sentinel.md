---
title: Azure Arc 対応サーバーを Azure Sentinel に接続する
description: Azure Arc 対応サーバーを Azure Sentinel にオンボードする方法について説明します。
author: likamrat
ms.author: brblanch
ms.date: 01/29/2021
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: operate
ms.custom: think-tank, e2e-hybrid
ms.openlocfilehash: 6bb394c4ab7d40590affe87ca58ac141182a3593
ms.sourcegitcommit: 51565dc4d3a1858bd62f708f2e4c082fbd4c6fe4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/20/2021
ms.locfileid: "107748114"
---
# <a name="connect-azure-arc-enabled-servers-to-azure-sentinel"></a>Azure Arc 対応サーバーを Azure Sentinel に接続する

この記事では、Azure Arc 対応サーバーを [Azure Sentinel](/azure/sentinel/) にオンボードする方法に関するガイダンスを提供します。 これにより、セキュリティ関連のイベントの収集を開始し、他のデータ ソースとそれらの関連付けを開始できます。

次の手順を行うと、Azure サブスクリプションで Azure Sentinel を有効にして構成できます。 このプロセスには次のものが含まれます。

- 分析と収集のためにログとイベントが集計される Log Analytics ワークスペースを設定する。
- ワークスペースで Azure Sentinel を有効にする。
- 拡張機能の管理機能と Azure Policy を使用して Azure Sentinel で Azure Arc 対応サーバーをオンボードする。

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

1. 前述のように、このガイドは、既に VM またはベアメタル サーバーをデプロイして Azure Arc に接続している段階から開始します。このシナリオでは、既に Azure Arc に接続されており、Azure のリソースとして表示されている Google Cloud Platform (GCP) インスタンスを使用します。 次のスクリーン ショットのようになります。

    ![Azure portal 内の Azure Arc 対応サーバーの概要を示すスクリーンショット。](./media/arc-azure-sentinel/sentinel-1.png)

    ![Azure portal 内の Azure Arc サーバーの詳細を示すスクリーンショット。](./media/arc-azure-sentinel/sentinel-2.png)

1. [Azure CLI をインストールするか更新します](/cli/azure/install-azure-cli)。 Azure CLI は、バージョン 2.7 以降を実行している必要があります。 現在インストールされているバージョンを確認するには、`az --version` を使用します。

1. Azure サービス プリンシパルを作成します。

    VM またはベアメタル サーバーを Azure Arc に接続するには、共同作成者ロールが割り当てられた Azure サービス プリンシパルが必要です。 これを作成するには、自分の Azure アカウントにサインインして、次のコマンドを実行します。 代わりに、これを [Azure Cloud Shell](https://shell.azure.com/) で実行することもできます。

    ```console
    az login
    az ad sp create-for-rbac -n "<Unique SP Name>" --role contributor
    ```

    次に例を示します。

    ```console
    az ad sp create-for-rbac -n "http://AzureArcServers" --role contributor
    ```

    出力は次のようになります。

    ```json
    {
      "appId": "XXXXXXXXXXXXXXXXXXXXXXXXXXXX",
      "displayName": "AzureArcServers",
      "name": "http://AzureArcServers",
      "password": "XXXXXXXXXXXXXXXXXXXXXXXXXXXX",
      "tenant": "XXXXXXXXXXXXXXXXXXXXXXXXXXXX"
    }
    ```

> [!NOTE]
> サービス プリンシパルのスコープを、特定の [Azure サブスクリプションとリソース グループ](/cli/azure/ad/sp)に限定することを強くお勧めします。

## <a name="onboard-azure-sentinel"></a>Azure Sentinel のオンボード

Azure Sentinel では、Log Analytics エージェントを使用して Windows および Linux サーバーのログ ファイルが収集され、Azure Sentinel に転送されます。 収集されたデータは、Log Analytics ワークスペースに格納されます。 Azure Security Center によって作成された既定のワークスペースを使用できないため、カスタムのものが必要です。 Azure Sentinel と同じカスタム ワークスペース内で Azure Security Center の未加工のイベントとアラートを保持できます。

1. 専用の Log Analytics ワークスペースを作成し、その上で Azure Sentinel ソリューションを有効にします。 この [Azure Resource Manager テンプレート (ARM テンプレート)](https://github.com/microsoft/azure_arc/blob/main/azure_arc_servers_jumpstart/azuresentinel/arm/sentinel-template.json) を使用して、新しい Log Analytics ワークスペースを作成し、Azure Sentinel ソリューションを定義して、それをワークスペースに対して有効にします。 デプロイを自動化するには、ARM テンプレート [パラメーター ファイル](https://github.com/microsoft/azure_arc/blob/main/azure_arc_servers_jumpstart/azuresentinel/arm/sentinel-template.parameters.json)を編集し、ワークスペースの名前と場所を指定します。

    ![ARM テンプレートのスクリーンショット。](./media/arc-azure-sentinel/sentinel-3.png)

1. ARM テンプレートをデプロイする。 [デプロイ フォルダー](https://github.com/microsoft/azure_arc/tree/main/azure_arc_servers_jumpstart/azuresentinel/arm)に移動し、次のコマンドを実行します。

  ```console
  az deployment group create --resource-group <Name of the Azure resource group> \
  --template-file <The `sentinel-template.json` template file location> \
  --parameters <The `sentinel-template.parameters.json` template file location>
  ```

次に例を示します。

   !["az deployment group create" コマンドのスクリーンショット。](./media/arc-azure-sentinel/sentinel-4.png)

## <a name="onboard-azure-arc-enabled-vms-on-azure-sentinel"></a>Azure Sentinel で Azure Arc 対応 VM をオンボードする

Log Analytics ワークスペースに Azure Sentinel をデプロイしたら、それにデータ ソースを接続する必要があります。

Microsoft サービス用のコネクタと、セキュリティ製品エコシステムのサードパーティ ソリューションがあります。 Common Event Format (CEF)、syslog、または REST API を使用して、お使いのデータ ソースを Azure Sentinel に接続することもできます。

サーバーと VM には、Log Analytics エージェント (MMA) エージェント、またはログを収集して Azure Sentinel に送信する Azure Sentinel エージェントをインストールできます。 Azure Arc を使用すると、複数の方法でエージェントをデプロイできます。

- [拡張機能の管理](./arc-vm-extension-mma.md): Azure Arc 対応サーバーのこの機能を使用すると、Azure 以外の Windows または Linux VM に MMA エージェントの VM 拡張機能をデプロイできます。 Azure portal、Azure CLI、ARM テンプレート、PowerShell スクリプトを使用して、Azure Arc 対応サーバーへの拡張機能のデプロイを管理できます。

- [Azure Policy](./arc-policies-mma.md): ポリシーを割り当てて、Azure Arc 対応サーバーに MMA エージェントがインストールされているかどうかを監査できます。 エージェントがインストールされていない場合は、この拡張機能を使用して、Azure VM と比較する登録エクスペリエンスである修復タスクによって、VM への自動デプロイを行うことができます。

## <a name="clean-up-your-environment"></a>環境をクリーンアップする

ご利用の環境をクリーンアップするには、次の手順を実行します。

1. 次の各ガイドに記載されている破棄手順を使用して、各環境から仮想マシンを削除します。

   - [GCP Ubuntu インスタンス](./gcp-terraform-ubuntu.md)および [GCP Windows インスタンス](./gcp-terraform-windows.md)
   - [AWS Ubuntu EC2 インスタンス](./aws-terraform-ubuntu.md)
   - [VMware vSphere Ubuntu VM](./vmware-terraform-ubuntu.md) および [VMware vSphere Windows Server VM](./vmware-terraform-windows.md)
   - [Vagrant Ubuntu box](./local-vagrant-ubuntu.md) および [Vagrant Windows box](./local-vagrant-windows.md)

2. Azure CLI で次のスクリプトを実行して、Log Analytics ワークスペースを削除します。 Log Analytics ワークスペースの作成時に使用したワークスペース名を指定します。

  ```console
  az monitor log-analytics workspace delete --resource-group <Name of the Azure resource group> --workspace-name <Log Analytics Workspace Name> --yes
  ```
