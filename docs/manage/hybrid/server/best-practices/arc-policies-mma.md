---
title: Azure ポリシーを管理し、Azure Arc Linux および Windows サーバーに Azure 監視エージェント拡張機能をデプロイする
description: Azure Arc 対応サーバーを使用して Azure 外部の VM (オンプレミスにあるか他のクラウドにあるかは問いません) に Azure ポリシーを割り当てる方法について説明します。
author: likamrat
ms.author: brblanch
ms.date: 01/29/2021
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: operate
ms.custom: think-tank, e2e-hybrid
ms.openlocfilehash: d9a9c08f361f3988e55cfd2d9789c911c872cb2b
ms.sourcegitcommit: 51565dc4d3a1858bd62f708f2e4c082fbd4c6fe4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/20/2021
ms.locfileid: "107748080"
---
# <a name="manage-azure-policies-and-deploy-the-azure-monitoring-agent-extension-to-azure-arc-linux-and-windows-servers"></a>Azure ポリシーを管理し、Azure Arc Linux および Windows サーバーに Azure 監視エージェント拡張機能をデプロイする

この記事では、Azure Arc 対応サーバーを使用して Azure 外部の VM (オンプレミスにあるか他のクラウドにあるかは問いません) に Azure ポリシーを割り当てる方法に関するガイダンスを提供します。 この機能により、Azure Policy を使用して、Azure Arc 対応サーバーのオペレーティング システムの設定を監査できます。設定が準拠していない場合は、修復タスクをトリガーすることもできます。

ここでは、Azure Arc に接続されたマシンに Log Analytics エージェントがインストールされているかどうかを監査するポリシーを割り当てます。 されていない場合は、Azure VM を同じレベルにする登録エクスペリエンスである拡張機能を使用して、VM にそれを自動的にデプロイします。 このアプローチを使用すると、すべてのサーバーが確実に Azure Monitor、Azure Security Center、Azure Sentinel などのサービスにオンボードされるようにすることができます。

Azure portal、Azure Resource Manager テンプレート (ARM テンプレート)、または PowerShell スクリプトを使用して、Azure サブスクリプションまたはリソース グループにポリシーを割り当てることができます。 次の手順では、ARM テンプレートを使用して組み込みのポリシーを割り当てます。

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

[Azure Monitor のサポート対象 OS のドキュメント](/azure/azure-monitor/vm/vminsights-enable-overview#supported-operating-systems)を参照し、これらの手順で使用する VM がサポートされていることを確認してください。 Linux VM の場合は、Linux ディストリビューションとカーネルの両方をチェックして、サポートされている構成を使用していることを確認します。

## <a name="prerequisites"></a>前提条件

1. Azure Arc Jumpstart リポジトリを複製します。

   ```console
   git clone https://github.com/microsoft/azure_arc
   ```

2. 前述のように、このガイドは、既に VM またはサーバーをデプロイして Azure Arc に接続している段階から開始します。次のスクリーンショットでは、Google Cloud Platform (GCP) サーバーが既に Azure Arc に接続されており、Azure のリソースとして表示されています。

   ![Azure Arc 対応サーバーのリソース グループのスクリーンショット。](./media/arc-policies-mma/resource-group.png)

   ![Azure Arc 対応サーバーの接続済み状態のスクリーンショット。](./media/arc-policies-mma/connected-status.png)

3. [Azure CLI をインストールするか更新します](/cli/azure/install-azure-cli)。 Azure CLI は、バージョン 2.7 以降を実行している必要があります。 現在インストールされているバージョンを確認するには、`az --version` を使用します。

4. Azure サービス プリンシパルを作成します。

   VM またはベアメタル サーバーを Azure Arc に接続するには、共同作成者ロールが割り当てられた Azure サービス プリンシパルが必要です。 これを作成するには、自分の Azure アカウントにサインインして、次のコマンドを実行します。 このコマンドは [Azure Cloud Shell](https://shell.azure.com/) で実行することもできます。

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

また、Log Analytics ワークスペースもデプロイしておく必要があります。 デプロイを自動化するには、ARM テンプレート [パラメーター ファイル](https://github.com/microsoft/azure_arc/blob/main/azure_arc_servers_jumpstart/policies/arm/log_analytics-template.parameters.json)を編集し、ワークスペースの名前と場所を指定します。

![ARM テンプレート パラメーター ファイルのスクリーンショット。](./media/arc-policies-mma/parameter-file-1.png)

ARM テンプレートをデプロイするには、[デプロイ フォルダー](https://github.com/microsoft/azure_arc/tree/main/azure_arc_servers_jumpstart/policies/arm)に移動し、次のコマンドを実行します。

```console
az deployment group create --resource-group <Name of the Azure resource group> \
--template-file <The `log_analytics-template.json` template file location> \
--parameters <The `log_analytics-template.parameters.json` template file location>
```

## <a name="assign-policies-to-azure-arc-connected-machines"></a>Azure Arc に接続されたマシンにポリシーを割り当てる

すべての前提条件を設定した後、Azure Arc に接続されたマシンにポリシーを割り当てることができます。 [パラメーター ファイル](https://github.com/microsoft/azure_arc/blob/main/azure_arc_servers_jumpstart/policies/arm/policy.json)を編集して、サブスクリプション ID と Log Analytics ワークスペースを指定します。

![別の ARM テンプレート パラメーター ファイルのスクリーンショット。](./media/arc-policies-mma/parameter-file-2.png)

1. デプロイを開始するために、次のコマンドを使用します。

   ```console
   az policy assignment create --name 'Enable Azure Monitor for VMs' \
   --scope '/subscriptions/<Your subscription ID>/resourceGroups/<Name of the Azure resource group>' \
   --policy-set-definition '55f3eceb-5573-4f18-9695-226972c6d74a' \
   -p <The *policy.json* template file location> \
   --assign-identity --location <Azure Region>
   ```

   `policy-set-definition` フラグは、イニシアチブ `Enable Azure Monitor` 定義 ID を指します。

2. イニシアチブが割り当てられてから、定義されたスコープに割り当てが適用されるまでに約 30 分 かかります。 その後、Azure Policy によって、Azure Arc に接続されたマシンに対して評価サイクルが開始されます。このマシンは Log Analytics エージェント構成がまだデプロイされていないため、非準拠と認識されます。 これを確認するには、[ポリシー] セクションの下の Azure Arc に接続されたマシンにアクセスします。

   ![非準拠の Azure Policy 状態を示すスクリーンショット。](./media/arc-policies-mma/noncompliant-policy.png)

3. ここで、準拠の状態になるように、非準拠リソースに修復タスクを割り当てます。

   ![Azure Policy 修復タスクの作成を示すスクリーンショット。](./media/arc-policies-mma/create-remediation-task.png)

4. **[修復するポリシー]** で、 **[\[Preview]: Deploy Log Analytics Agent to Linux Azure Arc machines]\([プレビュー]: Linux Azure Arc マシンに Log Analytics エージェントを デプロイする\)** を選択し、 **[修復]** を選択します。 この修復タスクは、`DeployIfNotExists` 効果を実行し、Azure Arc 拡張機能の管理機能を使用して VM に Log Analytics エージェントをデプロイするように Azure Policy に指示しています。

   ![修復タスク内の Azure Policy 修復アクションのスクリーンショット。](./media/arc-policies-mma/remediation-action.png)

5. 修復タスクを割り当てると、ポリシーが再び評価されます。 GCP 上のサーバーが準拠していることと、Log Analytics エージェントが Azure Arc マシンにインストールされていることが表示されます。

   ![修復タスクの構成を示すスクリーンショット。](./media/arc-policies-mma/task-config.png)

   ![準拠している Azure Policy 状態を示すスクリーンショット。](./media/arc-policies-mma/compliant-status.png)

## <a name="clean-up-your-environment"></a>環境をクリーンアップする

ご利用の環境をクリーンアップするには、次の手順を実行します。

1. 各環境から仮想マシンを削除するには、各ガイドに記載されている破棄手順に従ってください。

   - [GCP Ubuntu インスタンス](./gcp-terraform-ubuntu.md)および [GCP Windows インスタンス](./gcp-terraform-windows.md)
   - [AWS Ubuntu EC2 インスタンス](./aws-terraform-ubuntu.md)
   - [VMware vSphere Ubuntu VM](./vmware-terraform-ubuntu.md) および [VMware vSphere Windows Server VM](./vmware-terraform-windows.md)
   - [Vagrant Ubuntu box](./local-vagrant-ubuntu.md) および [Vagrant Windows box](./local-vagrant-windows.md)

2. Azure CLI で次のスクリプトを実行して、Azure Policy 割り当てを削除します。

   ```console
   az policy assignment delete --name 'Enable Azure Monitor for VMs' --resource-group <resource-group>
   ```

3. Azure CLI で次のスクリプトを実行して、Log Analytics ワークスペースを削除します。 Log Analytics ワークスペースの作成時に使用したワークスペース名を指定します。

   ```console
   az monitor log-analytics workspace delete --resource-group <Name of the Azure resource group> --workspace-name <Log Analytics workspace Name> --yes
   ```
