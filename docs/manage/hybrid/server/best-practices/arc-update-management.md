---
title: Azure Automation の Update Management を使用して、Azure Arc 対応サーバーのオペレーティング システムの更新プログラムを管理する
description: Azure Arc 対応サーバーを Azure Automation の Update Management にオンボードする方法について説明します。
author: likamrat
ms.author: brblanch
ms.date: 01/29/2021
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: operate
ms.custom: think-tank, e2e-hybrid
ms.openlocfilehash: ca1ae4422dbfc245fe85befa8fd66762808b5565
ms.sourcegitcommit: 9e4bc0e233a24642853f5e8acbeb9746b2444024
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102112247"
---
# <a name="use-update-management-in-azure-automation-to-manage-operating-system-updates-for-azure-arc-enabled-servers"></a>Azure Automation の Update Management を使用して、Azure Arc 対応サーバーのオペレーティング システムの更新プログラムを管理する

この記事では、Azure Arc 対応サーバーを [Azure Automation の Update Management](/azure/automation/update-management/overview) にオンボードする方法について説明します。これにより、Windows または Linux を実行する Azure Arc 対応サーバーのオペレーティング システムの更新を管理できるようになります。

次の手順では、以下の手順を実行して、Azure Arc 対応サーバーの Update Management をサポートするための Azure Automation アカウントと Log Analytics ワークスペースを作成して構成します。

- 新しい Log Analytics ワークスペースと Azure Automation アカウントを設定する。
- Azure Arc 対応サーバーで Update Management を有効にする。

> [!IMPORTANT]
> この記事の手順では、既に VM、またはオンプレミスや他のクラウドで実行されているサーバーをデプロイ済みで、それらが Azure Arc に接続されていることを前提としています。まだ行っていない場合は、次の情報を参照して自動化を行ってください。

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

2. 前述のように、このガイドでは、VM またはベアメタル サーバーがすでに Azure Arc にデプロイされ接続済みの段階から開始します。このシナリオでは、すでに Azure Arc に接続されており、Azure のリソースとして表示されているアマゾン ウェブ サービス (AWS) の EC2 インスタンスを使用します。

    ![アマゾン ウェブ サービス クラウド コンソールの EC2 のスクリーンショット。](./media/arc-update-management/aws-ec2-instance.png)

    ![Azure portal 内の Azure Arc 対応サーバーのスクリーンショット。](./media/arc-update-management/arc-enabled-server.png)

3. [Azure CLI をインストールするか更新します](/cli/azure/install-azure-cli)。 Azure CLI は、バージョン 2.14 以降を実行している必要があります。 現在インストールされているバージョンを確認するには、`az --version` を使用します。

## <a name="configure-update-management"></a>Update Management の構成

Update Management は、Log Analytics エージェントを使用して Windows および Linux のサーバー ログファイルを収集し、収集されたデータは Log Analytics ワークスペースに格納されます。

1. この [Azure Resource Manager テンプレート (ARM テンプレート)](https://github.com/microsoft/azure_arc/blob/main/azure_arc_servers_jumpstart/updateManagement/law-template.json) を使用して、Log Analytics ワークスペースを作成します。 これにより、新しい Log Analytics ワークスペースが作成され、Update Management ソリューションが定義され、ワークスペースで使用できるようになります。

2. 次のコマンドを実行して Log Analytics ワークスペースの新しいリソース グループを作成します。角かっこ内の値は実際の値に置き換えてください。

    ```console
    az group create --name <Name for your resource group> \
    --location <Location for your resources> \
    --tags "Project=jumpstart_azure_arc_servers"
    ```

    ![`az group create` コマンドのスクリーンショット。](./media/arc-update-management/az-group-create.png)

3. ARM テンプレートの[パラメーター ファイル](https://github.com/microsoft/azure_arc/blob/main/azure_arc_servers_jumpstart/updateManagement/law-template.parameters.json)を編集して、Log Analytics ワークスペースの名前、場所、および Azure Automation アカウントの名前を指定します。 また、次の例に示すように、Azure Arc 対応サーバーの名前と、Azure Arc 対応サーバーが含まれているリソース グループの名前を指定する必要があります。

    ![ARM テンプレートのスクリーンショット。](./media/arc-update-management/arm-template.png)

4. ARM テンプレートをデプロイする。 [デプロイ フォルダー](https://github.com/microsoft/azure_arc/tree/main/azure_arc_servers_jumpstart/updateManagement)に移動し、次のコマンドを実行します。

    ```console
    az deployment group create --resource-group <Name of the Azure resource group you created> \
        --template-file law-template.json \
        --parameters law-template.parameters.json
    ```

   ![`az deployment group` コマンドのスクリーンショット。](./media/arc-update-management/az-deployment-group.png)

5. デプロイが完了すると、Log Analytics ワークスペース、Automation アカウント、Update Management ソリューションを含むリソース グループが Azure portal に表示されます。 Log Analytics ワークスペースの **[ソリューション]** タブにドリルダウンすると、 **[Update Management]** ソリューションが見つかります。

    ![Azure portal の Log Analytics ワークスペースのスクリーンショット。](./media/arc-update-management/log-analytics-workspace.png)

## <a name="confirm-that-the-update-management-solution-is-deployed-on-your-azure-arc-enabled-server"></a>Update Management ソリューションが Azure Arc 対応サーバーにデプロイされていることを確認する

1. Log Analytics ワークスペースで **[ソリューション]** をクリックし、一覧から **[更新プログラム]** ソリューションを選択して Update Management 評価の進行状況を確認します。

    ![Log Analytics ワークスペースの [ソリューション] タブのスクリーンショット。](./media/arc-update-management/solutions-tab.png)

   Update Management が VM の評価を示すのに十分なデータを収集するには、数時間かかることがあります。 次のページで、評価が実行されていることを確認できます。

   ![Log Analytics ワークスペース内の [概要] タブと [更新プログラム] ビューのスクリーンショット。](./media/arc-update-management/overview-tab.png)

   評価が完了すると、[Update Management] タブに **[概要の表示]** オプションが表示されます。

   ![Log Analytics ワークスペース内の更新プログラム ビューの概要のスクリーンショット。](./media/arc-update-management/updates-summary.png)

2. **[概要の表示]** を選択してからもう一度選択することで、[Update Management] にドリルダウンすることができます。 次の例では、Azure Arc 対応サーバーに不足している更新プログラムがあることがわかります。

    ![Azure Arc 対応サーバーで不足している更新プログラムのスクリーンショット。](./media/arc-update-management/updates-missing.png)

## <a name="schedule-an-update"></a>更新プログラムのスケジュール

Update Management ソリューションの構成が完了したので、Azure Arc 対応サーバーに設定したスケジュールで更新プログラムをデプロイできます。

1. 先ほど作成した Automation アカウントに移動し、次のスクリーンショットに示すように [Update Management] タブを選択します。 Azure Arc 対応サーバーが一覧に表示されています。

    ![Azure Automation アカウントのスクリーンショット。](./media/arc-update-management/azure-automation-account.png)

1. **[更新プログラムの展開のスケジュール]** を選択します。 次のページで、Azure Arc 対応サーバーで使用されているオペレーティング システムを選択し、次のスクリーンショットに示すように、 **[更新するマシン]** を選択します。

    ![[更新プログラムの展開] での更新プログラムのスケジュールを示すスクリーンショット。](./media/arc-update-management/schedule-an-update.png)

1. **[種類]** ドロップダウンから **[マシン]** を選択し、サーバーを選択して **[OK]** を選択します。

    ![[更新プログラムの展開] でスケジュールされた更新プログラムの [種類] と [サーバー] を示すスクリーンショット。](./media/arc-update-management/type-update.png)

1. **[スケジュール設定]** をクリックし、任意のスケジュールを指定します。

    ![スケジュール設定を構成するための [更新プログラムの展開] のフィールドのスクリーンショット。](./media/arc-update-management/config-schedule-settings.png)

    ![[更新プログラムの展開] 内のスケジュール設定のフィールドのスクリーンショット。](./media/arc-update-management/schedule-settings.png)

1. 最後に、デプロイの名前を指定してから **[作成]** を選択します。

    ![[更新プログラムの展開] での更新プログラムの名前付けを示すスクリーンショット。](./media/arc-update-management/naming-update.png)

1. Automation アカウントの [Update Management] タブ内にある **[展開スケジュール]** タブで、スケジュールされた更新プログラムの展開を確認できます。

    ![[Update Management] のスケジュールされた更新プログラムを示すスクリーンショット。](./media/arc-update-management/scheduled-update.png)

この Update Management ソリューションを使用すると、定義したスケジュールに基づいて、展開ウィンドウ内の Azure Arc 対応サーバーが更新されます。 このシナリオの範囲外にある [Update Management](/azure/automation/update-management/overview) では、さらに多くのことが可能です。 詳細については、[ドキュメント](/azure/automation/update-management/overview)を参照してください。

## <a name="clean-up-your-environment"></a>環境をクリーンアップする

環境をクリーンアップするには、次の手順を実行します。

1. 各環境から仮想マシンを削除するには、各ガイドに記載されている破棄手順に従ってください。

    - [GCP Ubuntu インスタンス](./gcp-terraform-ubuntu.md)および [GCP Windows インスタンス](./gcp-terraform-windows.md)
    - [AWS Ubuntu EC2 インスタンス](./aws-terraform-ubuntu.md)
    - [VMware vSphere Ubuntu VM](./vmware-terraform-ubuntu.md) および [VMware vSphere Windows Server VM](./vmware-terraform-windows.md)
    - [Vagrant Ubuntu box](./local-vagrant-ubuntu.md) および [Vagrant Windows box](./local-vagrant-windows.md)

1. リソース グループを削除します。

    ```console
    az group delete --name <Name of your resource group>
    ```

    ![`az group delete` コマンドのスクリーンショット。](./media/arc-update-management/az-group-delete.png)
