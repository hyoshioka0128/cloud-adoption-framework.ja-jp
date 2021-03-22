---
title: 仮想マシン拡張機能と Azure Resource Manager テンプレートを使用して Azure Arc Linux および Windows サーバーにカスタム スクリプトをデプロイする
description: デプロイ後の構成と自動化タスクを提供する仮想マシン拡張機能を使用して、Azure Arc 対応サーバーにカスタム スクリプトを実行する方法について説明します。
author: likamrat
ms.author: brblanch
ms.date: 01/29/2021
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: operate
ms.custom: think-tank, e2e-hybrid
ms.openlocfilehash: b8ac9e7f9041fe94d0644e06e342a1a1f6978d8a
ms.sourcegitcommit: 9e4bc0e233a24642853f5e8acbeb9746b2444024
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102112009"
---
# <a name="use-virtual-machine-extensions-and-an-azure-resource-manager-template-to-deploy-custom-scripts-to-azure-arc-linux-and-windows-servers"></a>仮想マシン拡張機能と Azure Resource Manager テンプレートを使用して Azure Arc Linux および Windows サーバーにカスタム スクリプトをデプロイする

この記事では、仮想マシン拡張機能を使用して Azure Arc 対応サーバーにカスタム スクリプトを実行する方法に関するガイダンスを提供します。 仮想マシン拡張機能は、ソフトウェアのインストール、ウイルス対策保護、またはカスタム スクリプトを実行するためのメカニズムなど、デプロイ後の構成および自動化タスクを提供する小規模なアプリケーションです。

Azure portal、Azure CLI、Azure Resource Manager テンプレート (ARM テンプレート)、PowerShell または Linux シェル スクリプト、または Azure ポリシーを使用して、Azure Arc 対応サーバーへの拡張機能のデプロイを管理できます。 次の手順では、ARM テンプレートを使用してカスタム スクリプト拡張機能をデプロイします。 この拡張機能により、スクリプトがダウンロードされて仮想マシン上で実行されます。 これはデプロイ後の構成、ソフトウェアのインストール、その他の構成や管理タスクに役立ちます。

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
    git clone https://github.com/microsoft/azure_arc.git
    ```

2. 前述のように、このガイドは、既に VM またはサーバーをデプロイして Azure Arc に接続している段階から開始します。次のスクリーンショットでは、GCP サーバーが既に Azure Arc に接続されており、Azure のリソースとして表示されています。

    ![Azure Arc 対応サーバーのリソース グループを示すスクリーンショット。](./media/arc-vm-extension-custom-script/resource-group.png)

    ![Azure Arc 対応サーバーの接続済み状態のスクリーンショット。](./media/arc-vm-extension-custom-script/connected-status.png)

3. [Azure CLI をインストールまたは更新します](/cli/azure/install-azure-cli)。 Azure CLI は、バージョン 2.7 以降を実行している必要があります。 現在インストールされているバージョンを確認するには、`az --version` を使用します。

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
> サービス プリンシパルのスコープを、特定の [Azure サブスクリプションとリソース グループ](/cli/azure/ad/sp)に設定することを強くお勧めします。

カスタム スクリプト拡張機能を試行するには、以下の Linux スクリプトと Windows スクリプトを使用します。

- [Linux](https://github.com/microsoft/azure_arc/blob/main/azure_arc_servers_jumpstart/scripts/custom_script_linux.sh): スクリプトによって、オペレーティング システム上でその日のメッセージが変更されます。
- [Windows](https://github.com/microsoft/azure_arc/blob/main/azure_arc_servers_jumpstart/scripts/custom_script_windows.ps1): スクリプトによって、Windows ターミナル、Microsoft Edge、7-Zip、Visual Studio Code [Chocolatey](https://chocolatey.org/) パッケージが VM にインストールされます。

## <a name="azure-arc-enabled-servers-custom-script-extension-deployment"></a>Azure Arc 対応サーバーのカスタム スクリプト拡張機能のデプロイ

1. [Windows](https://github.com/microsoft/azure_arc/blob/main/azure_arc_servers_jumpstart/extensions/arm/customscript-templatewindows.parameters.json) または [Linux](https://github.com/microsoft/azure_arc/blob/main/azure_arc_servers_jumpstart/extensions/arm/customscript-templatelinux.parameters.json) の拡張機能のパラメーター ファイルを編集します

   ![ARM テンプレート パラメーター ファイルのスクリーンショット。](./media/arc-vm-extension-custom-script/parameters-file.png)

2. 環境の構成に合わせて、次の情報を指定します。

    - Azure Arc に登録されている VM の名前。

    ![Azure Arc 対応サーバーのマシン名のスクリーンショット。](./media/arc-vm-extension-custom-script/machine-name.png)

    - Azure Arc 対応サーバーを登録したリソース グループの場所。

    ![Azure リージョンのスクリーンショット。](./media/arc-vm-extension-custom-script/azure-region.png)

    - サーバーで実行するスクリプトのパブリック URI。この場合は、スクリプトの URL を未加工の形式で使用します。
      - Windows の場合: [パブリック URI](https://raw.githubusercontent.com/microsoft/azure_arc/main/azure_arc_servers_jumpstart/scripts/custom_script_windows.ps1)
      - Linux の場合: [パブリック URI](https://raw.githubusercontent.com/microsoft/azure_arc/main/azure_arc_servers_jumpstart/scripts/custom_script_linux.sh)

3. いずれかのスクリプトを実行するには、次のコマンドを使用します。

    - Windows:

         ```powershell
         powershell -ExecutionPolicy Unrestricted -File custom_script_windows.ps1
         ```

    - Linux:

         ```bash
         ./custom_script_linux.sh
         ```

4. Linux または Windows 用の ARM テンプレートをデプロイするには、[デプロイ フォルダー](https://github.com/microsoft/azure_arc/tree/main/azure_arc_servers_jumpstart/extensions/arm) に移動し、オペレーティング システムと一致するテンプレートを使用して次のコマンドを実行します。

    ```bash
    az deployment group create --resource-group <Name of the Azure resource group> \
    --template-file <The `customscript-template.json` template file location for Linux or Windows> \
    --parameters <The `customscript-template.parameters.json` template file location>
    ```

5. テンプレートのデプロイの実行が完了すると、次のような出力が表示されます。

    ![ARM テンプレートからの出力のスクリーンショット。](./media/arc-vm-extension-custom-script/output.png)

6. Azure portal の **[拡張機能]** の設定を選択して、Azure Arc 対応サーバーでデプロイが成功したことを確認します。 カスタム スクリプト拡張機能がインストールされていることを確認します。

    ![カスタム スクリプト拡張機能のスクリーンショット。](./media/arc-vm-extension-custom-script/custom-script-extension.png)

カスタム スクリプトの実行が成功したかどうかを確認するもう 1 つの方法は、VM に接続し、オペレーティング システムが構成されていることを確認することです。

- Linux VM の場合は、SSH を使用して VM に接続し、スクリプトによってカスタマイズされたその日のメッセージを確認します。

  ![更新されたその日のメッセージのスクリーンショット。](./media/arc-vm-extension-custom-script/daily-message.png)

- RDP 経由で Windows VM に接続し、追加のソフトウェア (Microsoft Edge、7-Zip、Visual Studio Code) がインストールされていることを確認します。

  ![追加のソフトウェアがインストールされているスクリーンショット。](./media/arc-vm-extension-custom-script/additional-software.png)

## <a name="clean-up-your-environment"></a>環境をクリーンアップする

ご利用の環境をクリーンアップするには、次の手順を実行します。

各環境から仮想マシンを削除するには、各ガイドに記載されている破棄手順に従ってください。

- [GCP Ubuntu インスタンス](./gcp-terraform-ubuntu.md)および [GCP Windows インスタンス](./gcp-terraform-windows.md)
- [AWS Ubuntu EC2 インスタンス](./aws-terraform-ubuntu.md)
- [VMware vSphere Ubuntu VM](./vmware-terraform-ubuntu.md) および [VMware vSphere Windows Server VM](./vmware-terraform-windows.md)
- [Vagrant Ubuntu box](./local-vagrant-ubuntu.md) および [Vagrant Windows box](./local-vagrant-windows.md)
