---
title: Azure Arc 対応サーバーを Azure Security Center に接続する
description: Azure Arc 対応サーバーを Azure Security Center にオンボードする方法について説明します。
author: likamrat
ms.author: brblanch
ms.date: 01/15/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: operate
ms.custom: think-tank, e2e-hybrid
ms.openlocfilehash: 0829a67a8ddc00d3db73ff2ac67d781b7e52f01e
ms.sourcegitcommit: b8f8b7631aabaab28e9705934bf67dad15e3a179
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101801422"
---
# <a name="connect-azure-arc-enabled-servers-to-azure-security-center"></a>Azure Arc 対応サーバーを Azure Security Center に接続する

この記事では、Azure Arc 対応サーバーを [Azure Security Center (Azure Security Center)](/azure/security-center/) にオンボードする方法に関するガイダンスを提供します。 これはセキュリティ関連の構成とイベント ログの収集を開始するのに役立つため、対策を推奨し、Azure のセキュリティ態勢全体を向上させることができます。

次の手順では、Azure サブスクリプションで Azure Security Center Standard レベルを有効にして構成します。 これにより、脅威に対する保護 (ATP) と検出の高度な機能が提供されるようになります。 このプロセスには次のものが含まれます。

- 分析のためにログとイベントが集計される Log Analytics ワークスペースを設定します。
- Security Center の既定のセキュリティ ポリシーを割り当てます。
- Azure Security Center の推奨事項を確認します。
- **クイック修正** による修復を使用して、Azure Arc 対応サーバーに推奨される構成を適用します。

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

1. Azure Arc Jumpstart リポジトリをクローンします。

    ```console
    git clone https://github.com/microsoft/azure_arc
    ```

2. 前述のように、このガイドでは、既に VM またはベアメタル サーバーをデプロイして Azure Arc に接続している段階から開始します。このシナリオでは、既に Azure Arc に接続されており、Azure のリソースとして表示されている Google Cloud Platform (GCP) インスタンスを使用します。 次のスクリーン ショットのようになります。

    ![Azure portal 内の Azure Arc 対応サーバーを示すスクリーンショット。](./media/arc-security-center/arc-overview.png)

    ![Azure portal 内の Azure Arc 対応サーバーの詳細を示すスクリーンショット。](./media/arc-security-center/arc-status.png)

3. [Azure CLI をインストールするか更新します](/cli/azure/install-azure-cli)。 Azure CLI は、バージョン 2.7 以降を実行している必要があります。 現在インストールされているバージョンを確認するには、`az --version` を使用します。

4. Azure サービス プリンシパルを作成します。

    VM またはベアメタル サーバーを Azure Arc に接続するには、共同作成者ロールが割り当てられた Azure サービス プリンシパルが必要です。 これを作成するには、自分の Azure アカウントにサインインして、次のコマンドを実行します。 このコマンドは、[Azure Cloud Shell](https://shell.azure.com/) 内で実行することもできます。

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

## <a name="onboard-azure-security-center"></a>Azure Security Center をオンボードする

1. Azure Security Center によって収集されたデータは、Log Analytics ワークスペースに保存されます。 Azure Security Center によって作成された既定のものを使用することも、自分で作成したカスタムのものを使用することもできます。 専用のワークスペースを作成する場合は、Azure Resource Manager テンプレート (ARM テンプレート) [パラメーター ファイル](https://github.com/microsoft/azure_arc/blob/main/azure_arc_servers_jumpstart/securitycenter/arm/log_analytics-template.parameters.json)を編集して、ワークスペースの名前と場所を指定することで、デプロイを自動化できます。

   ![ARM テンプレートのスクリーンショット。](./media/arc-security-center/arm-template.png)

2. ARM テンプレートをデプロイするには、[デプロイ フォルダー](https://github.com/microsoft/azure_arc/tree/main/azure_arc_servers_jumpstart/securitycenter/arm)に移動し、次のコマンドを実行します。

   ```console
   az deployment group create --resource-group <Name of the Azure resource group> \
   --template-file <The `log_analytics-template.json` template file location> \
   --parameters <The `log_analytics-template.parameters.json` template file location>
   ```

3. ユーザー定義のワークスペースを使用する場合は、既定のものの代わりにそれを使用するように Security Center に指示する必要があり、次のコマンドを使用します。

   ```console
   az security workspace-setting create --name default \
   --target-workspace '/subscriptions/<Your subscription ID>/resourceGroups/<Name of the Azure resource group>/providers/Microsoft.OperationalInsights/workspaces/<Name of the Log Analytics Workspace>'
   ```

4. Azure Security Center レベルを選択します。 Free レベルは、すべての Azure サブスクリプションで既定で有効になっており、継続的なセキュリティ評価と、実行可能なセキュリティに関する推奨事項を提供します。 このガイドでは、Azure Virtual Machines の Standard レベルを使用します。これは、これらの機能が拡張されたものであり、ハイブリッド クラウド ワークロード全体にわたって統合されたセキュリティ管理と脅威に対する保護を提供します。 VM に対して Azure Security Center の Standard レベルを有効にするには、次のコマンドを実行します。

    ```console
    az security pricing create -n VirtualMachines --tier 'standard'
    ```

5. 既定の Security Center ポリシーのイニシアティブを割り当てます。 Azure Security Center では、ポリシーに基づいてセキュリティに関する推奨事項が作成されます。 Security Center ポリシーを定義 ID `1f3afdf9-d0c9-4c3d-847f-89da613e70a8` でグループ化する特定のイニシアティブがあります。 次のコマンドにより、Azure Security Center イニシアティブがサブスクリプションに割り当てられます。

    ```console
    az policy assignment create --name 'Azure Security Center Default <Your subscription ID>' \
    --scope '/subscriptions/<Your subscription ID>' \
    --policy-set-definition '1f3afdf9-d0c9-4c3d-847f-89da613e70a8'
    ```

## <a name="azure-arc-and-azure-security-center-integration"></a>Azure Arc と Azure Security Center の統合

Azure Security Center を正常にオンボードすると、Azure Arc 対応サーバーなどのリソースの保護に役立つ推奨事項が得られます。 Azure Security Center では、Azure リソースのセキュリティの状態が定期的に分析されて、潜在的なセキュリティ脆弱性が特定されます。

Azure Security Center では、 **[VM およびサーバー]** の下の **[計算とアプリ]** セクションで、Azure VM、Azure クラシック VM、サーバー、Azure Arc マシンなど、VM とコンピューターについて検出されたすべてのセキュリティに関する推奨事項の概要が提供されます。

![Azure Security Center 内の [計算とアプリ] のスクリーンショット。](./media/arc-security-center/compute-apps.png)

Azure Arc 対応サーバーでは、Azure Security Center により Log Analytics エージェントのインストールが推奨されます。 各推奨事項には以下も含まれます。

- 推奨事項の簡単な説明。
- セキュリティ スコアの影響 (この例では **[高い]** の状態)。
- 推奨事項を実装するために実行する修復手順。

次のスクリーンショットに示すような特定の推奨事項には、複数のリソースに対する推奨事項を迅速に修復できる **[クイック修正]** も取得します。

  ![Azure Arc 対応サーバーに関する Azure Security Center 推奨事項のスクリーンショット。](./media/arc-security-center/recommendation-quick-fix.png)

  ![Log Analytics のインストールを促す Azure Security Center 推奨事項のスクリーンショット。](./media/arc-security-center/recommendation-remediate.png)

次の修復 **[クイック修正]** では、ARM テンプレートを使用して、Azure Arc マシンに Microsoft Monitoring Agent 拡張機能がデプロイされます。

  ![Azure Security Center の [クイック修正] ARM テンプレートのスクリーンショット。](./media/arc-security-center/quick-fix-template.png)

Azure Security Center ダッシュボードから ARM テンプレートを使用して修復をトリガーするには、Azure Security Center に使用する Log Analytics ワークスペースを選択し、 **[1 個のリソースの修復]** を選択します。

  ![Azure Security Center の修復手順をトリガーする方法のスクリーンショット。](./media/arc-security-center/remediation-trigger.png)

Azure Arc 対応サーバーに推奨事項を適用すると、そのリソースは正常としてマークされます。

  ![正常な Azure Arc 対応サーバーのスクリーンショット。](./media/arc-security-center/healthy-server.png)

## <a name="clean-up-your-environment"></a>環境をクリーンアップする

ご利用の環境をクリーンアップするには、次の手順を実行します。

1. 各環境から仮想マシンを削除するには、各ガイドに記載されている破棄手順に従ってください。

    - [GCP Ubuntu インスタンス](./gcp-terraform-ubuntu.md)および [GCP Windows インスタンス](./gcp-terraform-windows.md)
    - [AWS Ubuntu EC2 インスタンス](./aws-terraform-ubuntu.md)
    - [VMware vSphere Ubuntu VM](./vmware-terraform-ubuntu.md) / [VMware vSphere Windows Server VM](./vmware-terraform-windows.md)
    - [Vagrant Ubuntu box](./local-vagrant-ubuntu.md) / [Vagrant Windows box](./local-vagrant-windows.md)

2. Azure CLI で次のスクリプトを実行して、Log Analytics ワークスペースを削除します。 Log Analytics ワークスペースの作成時に使用したワークスペース名を指定します。

  ```console
  az monitor log-analytics workspace delete --resource-group <Name of the Azure resource group> --workspace-name <Log Analytics Workspace Name> --yes
  ```
