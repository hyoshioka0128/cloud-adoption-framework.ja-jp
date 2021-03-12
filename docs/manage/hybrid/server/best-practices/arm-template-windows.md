---
title: Azure Resource Manager テンプレートを使用して Azure 仮想マシンを Azure Arc にデプロイして接続する
description: Azure Resource Manager テンプレートを使用して、Azure 仮想マシンを Azure Arc にデプロイして接続します。
author: likamrat
ms.author: brblanch
ms.date: 01/29/2021
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: operate
ms.custom: think-tank, e2e-hybrid
ms.openlocfilehash: 0205f46467b336f78d89412250b4bf1d53a730b6
ms.sourcegitcommit: b8f8b7631aabaab28e9705934bf67dad15e3a179
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101800793"
---
# <a name="use-an-azure-resource-manager-template-to-deploy-and-connect-an-azure-virtual-machine-to-azure-arc"></a>Azure Resource Manager テンプレートを使用して Azure 仮想マシンを Azure Arc にデプロイして接続する

この記事では、[Azure Resource Manager テンプレート (ARM テンプレート)](/azure/azure-resource-manager/templates/overview) を使用して、Windows を実行中の Azure 仮想マシン (Azure VM) を Azure Arc に自動的にオンボードするためのガイダンスを提供します。提供されている ARM テンプレートを使用すると、Azure リソースが作成され、VM 上で Azure Arc オンボード スクリプトが実行されます。

Azure VM では、既定で [Azure Instance Metadata Service (IMDS)](/azure/virtual-machines/windows/instance-metadata-service) が使用されています。 Azure VM を Azure Arc 対応サーバーとして投影すると、IMDS が使用されているときに Azure Arc サーバー リソースをその 1 つとして表現できなくなる "*競合*" が発生します。 その代わり、Azure Arc サーバーはネイティブ Azure VM として "機能" します。

このガイドでは、**デモ目的でのみ** Azure VM を使用して Azure Arc にオンボードすることができます。 Azure の外部 ("オンプレミス" や他のクラウド プラットフォームなど) にデプロイされているサーバーをシミュレートすることができます。

> [!NOTE]
> Azure VM は Azure Arc 対応サーバーである必要はありません。 下のシナリオはサポートされていません。これは、デモとテストの目的でのみ使用してください。

## <a name="prerequisites"></a>前提条件

1. Azure Arc Jumpstart リポジトリを複製します。

    ```console
    git clone https://github.com/microsoft/azure_arc.git
    ```

2. [バージョン 2.7 以降の Azure CLI をインストールするか、更新します。](/cli/azure/install-azure-cli) 現在インストールされているバージョンを確認するには、次のコマンドを使用してください。

    ```console
    az --version
    ```

3. Azure サブスクリプション: Azure サブスクリプションをお持ちでない場合は、[無料の Azure アカウントを作成できます](https://azure.microsoft.com/free/)。

4. Azure サービス プリンシパルを作成します。

    ARM テンプレートを使用して Azure リソースをデプロイするには、共同作成者ロールが割り当てられた Azure サービス プリンシパルが必要です。 これを作成するには、自分の Azure アカウントにサインインして、次のコマンドを実行します。 このコマンドは、[Azure Cloud Shell](https://shell.azure.com/) 内で実行することもできます。

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

## <a name="automation-flow"></a>自動化フロー

自動化とデプロイのフローについて理解を深めるには、下の説明を参照してください。

1. ユーザーが ARM テンプレート パラメーター ファイルを編集します (1 回限りの編集)。 これらのパラメーター値は、デプロイ全体で使用されます。

2. ARM テンプレートには、[`install_arc_agent.ps1`](https://github.com/microsoft/azure_arc/blob/main/azure_arc_servers_jumpstart/azure/windows/arm_template/scripts/install_arc_agent.ps1) PowerShell スクリプトをデプロイする Azure VM カスタム スクリプト拡張機能が含まれています。

3. Azure VM が Azure Arc 対応サーバーとして正常に投影されるようにするために、スクリプトによって以下が行われます。

    1. ローカル OS 環境変数が設定されます。

    2. *LogonScript.ps1* という名前のローカル OS サインイン スクリプトが生成されます。 このスクリプトでは、次のことが行われます。

        - *LogonScript.Log* ファイルを作成します。

        - Windows Azure ゲスト エージェント サービスが停止され、無効になります。

        - 新しい Windows ファイアウォール規則が作成され、`169.254.169.254` リモート アドレスへの Azure IMDS 送信トラフィックがブロックされます。

        - サインイン スクリプトの Windows スケジュール タスクが登録解除され、最初のサインイン後は実行されないようにします。

    3. Windows サーバー マネージャーを無効にして起動時に実行されないようにします。

4. ユーザーは RDP 経由で Windows VM に接続します。これにより、*LogonScript.ps1* の実行が開始され、VM が Azure Arc にオンボードされます。

## <a name="deployment"></a>デプロイ

前述のように、このデプロイでは ARM テンプレートを使用します。 ユーザーは、1 つのリソース グループ内のすべての Azure リソースの作成と、作成した VM を Azure Arc にオンボードする役割を持つ 1 つのテンプレートをデプロイします。

1. ARM テンプレートをデプロイする前に、Azure CLI で `az login` コマンドを使用して Azure にサインインします。

2. このデプロイには、ARM テンプレート パラメーター ファイルが使用されています。 デプロイを開始する前に、ローカルに複製されたリポジトリ フォルダー内の [`azuredeploy.parameters.json`](https://github.com/microsoft/azure_arc/blob/main/azure_arc_servers_jumpstart/azure/windows/arm_template/azuredeploy.parameters.json) ファイルを編集します。 パラメーター ファイルの例については、[こちら](https://github.com/microsoft/azure_arc/blob/main/azure_arc_servers_jumpstart/azure/windows/arm_template/azuredeploy.parameters.example.json)を参照してください。

3. ARM テンプレートをデプロイするには、ローカルに複製された[デプロイ フォルダー](https://github.com/microsoft/azure_arc/tree/main/azure_arc_servers_jumpstart/azure/windows/arm_template)に移動し、次のコマンドを実行します。

    ```console
    az group create --name <Name of the Azure resource group> --location <Azure Region> --tags "Project=jumpstart-azure-arc-servers"
    az deployment group create \
    --resource-group <Name of the Azure resource group> \
    --name <The name of this deployment> \
    --template-uri https://raw.githubusercontent.com/microsoft/azure-arc/main/azure_arc_servers_jumpstart/azure/windows/arm_template/azuredeploy.json \
    --parameters <The `azuredeploy.parameters.json` parameters file location>
    ```

    > [!NOTE]
    > `azuredeploy.parameters.json` ファイルで使用したものと同じ Azure リソース グループ名を使用していることを確認します。

    次に例を示します。

    ```console
    az group create --name Arc-Servers-Win-Demo --location "East US" --tags "Project=jumpstart-azure-arc-servers"
    az deployment group create \
    --resource-group Arc-Servers-Win-Demo \
    --name arcwinsrvdemo \
    --template-uri https://raw.githubusercontent.com/microsoft/azure-arc/main/azure_arc_servers_jumpstart/azure/windows/arm_template/azuredeploy.json \
    --parameters azuredeploy.parameters.json
    ```

4. Azure リソースがプロビジョニングされると、Azure portal で確認できます。

    ![ARM テンプレートからの出力のスクリーンショット。](./media/arm-template/template-windows-output.png)

    ![リソース グループ内のリソースのスクリーンショット。](./media/arm-template/template-windows-resources.png)

## <a name="windows-sign-in-and-post-deployment"></a>Windows のサインインとデプロイ後

1. Windows Server VM が作成されたので、次の手順ではこれに接続します。 そのパブリック IP アドレスを使用して、VM に RDP 接続します。

    ![Azure VM パブリック IP アドレスのスクリーンショット。](./media/arm-template/template-windows-ip.png)

2. 初回サインイン時には、「[自動化フロー](#automation-flow)」セクションに記載されているように、サインイン スクリプトが実行されます。 このスクリプトは、自動デプロイ プロセスの一環として作成されます。

3. スクリプトが実行されている間は、PowerShell セッションを **閉じない** でください。 セッションの完了時に、自動的に閉じられます。

    > [!NOTE]
    > スクリプトの実行時間は約 1 分から 2 分です。

    ![スクリプト出力の 1 つ目の種類を示すスクリーンショット。](./media/arm-template/template-windows-script-1.png)

    ![スクリプト出力の 2 つ目の種類を示すスクリーンショット。](./media/arm-template/template-windows-script-2.png)

    ![スクリプト出力の 3 つ目の種類を示すスクリーンショット。](./media/arm-template/template-windows-script-3.png)

    ![スクリプト出力の 4 つ目の種類を示すスクリーンショット。](./media/arm-template/template-windows-script-4.png)

4. 正常に完了すると、新しい Azure Arc 対応サーバーがリソース グループに追加されます。

![Azure Arc 対応サーバーのリソース グループを示すスクリーンショット。](./media/arm-template/template-windows-resource-gp.png)

![Azure Arc 対応サーバーの詳細を示すスクリーンショット。](./media/arm-template/template-windows-server-details.png)

## <a name="cleanup"></a>クリーンアップ

デプロイを完全に削除するには、Azure portal からリソース グループを削除します。

![リソース グループを削除する方法を示すスクリーンショット。](./media/arm-template/template-windows-delete.png)
