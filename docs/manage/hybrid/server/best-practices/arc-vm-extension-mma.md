---
title: Microsoft Monitoring Agent を Azure Arc Linux および Windows サーバーにデプロイする
description: 拡張機能を管理し、Azure Resource Manager テンプレートを使用して Microsoft Monitoring Agent を Azure Arc Linux および Windows サーバーにデプロイする方法について説明します。
author: likamrat
ms.author: brblanch
ms.date: 01/29/2021
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: operate
ms.custom: think-tank, e2e-hybrid
ms.openlocfilehash: c1555d43bfe245f307284b0702742c41f6030cbc
ms.sourcegitcommit: 9e4bc0e233a24642853f5e8acbeb9746b2444024
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102112180"
---
# <a name="manage-extensions-and-use-an-azure-resource-manager-template-to-deploy-microsoft-monitoring-agent-to-azure-arc-linux-and-windows-servers"></a>拡張機能を管理し、Azure Resource Manager テンプレートを使用して Microsoft Monitoring Agent を Azure Arc Linux および Windows サーバーにデプロイする

この記事では、Azure Arc 対応サーバーの拡張機能を管理する方法に関するガイダンスを提供します。 仮想マシン拡張機能は、ソフトウェアのインストール、ウイルス対策保護、またはカスタム スクリプトを実行するためのメカニズムなど、デプロイ後の構成および自動化タスクを提供する小規模なアプリケーションです。

Azure Arc 対応サーバーを使用すると、Azure VM 拡張機能を Azure 以外の Windows および Linux VM にデプロイできます。これにより、Azure VM と同等のハイブリッドまたはマルチクラウド管理エクスペリエンスが提供されます。

Azure portal、Azure CLI、Azure Resource Manager テンプレート (ARM テンプレート)、PowerShell スクリプト、または Azure ポリシーを使用して、Linux と Windows の両方で Azure Arc 対応サーバーへの拡張機能のデプロイを管理できます。 次の手順では、ARM テンプレートを使用して、Microsoft Monitoring Agent (MMA) をサーバーにデプロイします。 これにより、このエージェントを使用する Azure サービス (Azure Monitor、Azure Security Center、Azure Sentinel など) にそれらがオンボードされます。

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

[Azure Monitor のサポート対象 OS のドキュメント](/azure/azure-monitor/vm/vminsights-enable-overview#supported-operating-systems)を参照して、こちらの演習で使用する VM がサポートされていることを確認してください。 Linux VM の場合は、Linux ディストリビューションとカーネルの両方をチェックして、サポートされている構成を使用していることを確認します。

## <a name="prerequisites"></a>前提条件

1. Azure Arc Jumpstart リポジトリをクローンします。

    ```console
    git clone https://github.com/microsoft/azure_arc.git
    ```

2. 前述のように、このガイドは、既に VM またはベアメタル サーバーをデプロイして Azure Arc に接続している段階から開始します。このシナリオでは、既に Azure Arc に接続されており、Azure のリソースとして表示されている Google Cloud Platform (GCP) インスタンスを使用します。 次のスクリーンショットのようになります。

    ![Azure Arc 対応サーバーのリソース グループのスクリーンショット。](./media/arc-vm-extension-mma/mma-resource-group.png)

    ![Azure Arc 対応サーバーの接続済み状態のスクリーンショット。](./media/arc-vm-extension-mma/mma-connected-status.png)

3. [Azure CLI をインストールまたは更新します](/cli/azure/install-azure-cli)。 Azure CLI は、バージョン 2.7 以降を実行している必要があります。 現在インストールされているバージョンを確認するには、`az --version` を使用します。

4. Azure サービス プリンシパルを作成します。

    VM またはベアメタル サーバーを Azure Arc に接続するには、共同作成者ロールが割り当てられた Azure サービス プリンシパルが必要です。 これを作成するには、Azure アカウントにサインインして、次のコマンドを実行します。 このコマンドは、[Azure Cloud Shell](https://shell.azure.com/) 内で実行することもできます。

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

5. また、Log Analytics ワークスペースもデプロイしておく必要があります。 デプロイを自動化するには、ARM テンプレート [パラメーター ファイル](https://github.com/microsoft/azure_arc/blob/main/azure_arc_servers_jumpstart/extensions/arm/log_analytics-template.parameters.json)を編集し、ワークスペースの名前と場所を指定します。

    ![ARM テンプレート パラメーター (名前と場所) のスクリーンショット。](./media/arc-vm-extension-mma/parameters-file-1.png)

6. ARM テンプレートをデプロイするには、`../extensions/arm` デプロイ フォルダーに移動し、次のコマンドを実行します。

    ```console
    az deployment group create --resource-group <Name of the Azure resource group> \
    --template-file <The `log_analytics-template.json` template file location> \
    --parameters <The `log_analytics-template.parameters.json` template file location>
    ```

## <a name="azure-arc-enabled-servers-microsoft-monitoring-agent-extension-deployment"></a>Azure Arc 対応サーバーへの Microsoft Monitoring Agent 拡張機能のデプロイ

1. [拡張機能のパラメーター ファイル](https://github.com/microsoft/azure_arc/blob/main/azure_arc_servers_jumpstart/extensions/arm/mma-template.parameters.json)を編集します

    ![ARM 拡張機能のパラメーター ファイルのスクリーンショット。](./media/arc-vm-extension-mma/parameters-file-2.png)

    構成を一致させるには、次の情報を指定する必要があります。

    - Azure Arc に登録されている VM の名前。

      ![Azure Arc 対応サーバーのマシン名のスクリーンショット。](./media/arc-vm-extension-mma/mma-machine-name.png)

    - Azure Arc 対応サーバーを登録したリソース グループの場所。

      ![Azure リージョンのスクリーンショット。](./media/arc-vm-extension-mma/mma-azure-region.png)

    - 以前に作成した Log Analytics ワークスペースに関する情報 (ワークスペース ID とキー)。 これらのパラメーターは、MMA エージェントを構成するために使用されます。 この情報を確認するには、Log Analytics ワークスペースに移動し、 **[設定]** で **[Agents management]\(エージェント管理\)** を選択します。

      ![Azure Arc 対応サーバー内のエージェント管理のスクリーンショット。](./media/arc-vm-extension-mma/agents-management.png)

      ![ワークスペース構成のスクリーンショット。](./media/arc-vm-extension-mma/mma-workspace-config.png)

2. オペレーティング システム ([Windows](https://github.com/microsoft/azure_arc/blob/main/azure_arc_servers_jumpstart/extensions/arm/mma-template-windows.json) または [Linux](https://github.com/microsoft/azure_arc/blob/main/azure_arc_servers_jumpstart/extensions/arm/mma-template-linux.json)) に一致する ARM テンプレートを選択し、次のコマンドを実行してテンプレートをデプロイします。

    ```console
    az deployment group create --resource-group <Name of the Azure resource group> \
    --template-file <The `mma-template.json` template file location> \
    --parameters <The `mma-template.parameters.json` template file location>
    ```

3. テンプレートの実行が完了すると、次のような出力が表示されます。

    ![ARM テンプレートからの出力のスクリーンショット。](./media/arc-vm-extension-mma/mma-output.png)

4. Microsoft Monitoring Agent が Windows または Linux システムにデプロイされ、選択した Log Analytics ワークスペースに報告されます。 確認するには、ワークスペース内の **[Agents Management]\(エージェント管理\)** に戻り、 **[Windows]** または **[Linux]** を選択します。 追加の接続された VM が表示されます。

    ![Windows サーバー用の接続されたエージェントのスクリーンショット。](./media/arc-vm-extension-mma/windows-agents.png)

    ![Linux サーバー用の接続されたエージェントのスクリーンショット。](./media/arc-vm-extension-mma/linux-agents.png)

## <a name="clean-up-your-environment"></a>環境をクリーンアップする

ご利用の環境をクリーンアップするには、次の手順を実行します。

1. 各環境から仮想マシンを削除するには、各ガイドに記載されている破棄手順に従ってください。

    - [GCP Ubuntu インスタンス](./gcp-terraform-ubuntu.md)および [GCP Windows インスタンス](./gcp-terraform-windows.md)
    - [AWS Ubuntu EC2 インスタンス](./aws-terraform-ubuntu.md)
    - [VMware vSphere Ubuntu VM](./vmware-terraform-ubuntu.md) および [VMware vSphere Windows Server VM](./vmware-terraform-windows.md)
    - [Vagrant Ubuntu box](./local-vagrant-ubuntu.md) および [Vagrant Windows box](./local-vagrant-windows.md)

2. Azure CLI で次のコマンドを実行して、Log Analytics ワークスペースを削除します。 Log Analytics ワークスペースの作成時に使用したワークスペース名を指定します。

    ```console
    az monitor log-analytics workspace delete --resource-group <Name of the Azure resource group> --workspace-name <Log Analytics Workspace Name> --yes
    ```
