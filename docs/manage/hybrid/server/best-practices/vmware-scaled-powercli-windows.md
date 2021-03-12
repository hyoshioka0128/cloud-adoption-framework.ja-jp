---
title: VMware PowerCLI を使用して、VMware vSphere Windows Server 仮想マシンのオンボードを Azure Arc にスケーリングする
description: VMware PowerCLI を使用して、VMware vSphere Windows Server 仮想マシンのオンボードを Azure Arc にスケーリングします。
author: likamrat
ms.author: brblanch
ms.date: 01/29/2021
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: operate
ms.custom: think-tank, e2e-hybrid
ms.openlocfilehash: bee19608b038200fbb4cb0b1418bcfe45385c583
ms.sourcegitcommit: 9e4bc0e233a24642853f5e8acbeb9746b2444024
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102114321"
---
# <a name="use-vmware-powercli-to-scale-onboarding-vmware-vsphere-windows-server-virtual-machines-to-azure-arc"></a>VMware PowerCLI を使用して、VMware vSphere Windows Server 仮想マシンのオンボードを Azure Arc にスケーリングする

この記事では、提供されている [VMware PowerCLI](https://code.vmware.com/web/dp/tool/vmware-powercli/) スクリプトを使用して、複数の VMware vSphere 仮想マシンで Azure Arc が接続されているマシン エージェントの拡大デプロイを自動で実行し、最終的にこれらの VM を Azure Arc 対応サーバーとしてオンボードするためのガイダンスを提供します。

このガイドでは、VMware 仮想マシンのインベントリが既に存在し、PowerCLI PowerShell モジュールを使用して、Azure Arc への VM のオンボード プロセスを自動化することを前提としています。

## <a name="prerequisites"></a>前提条件

1. Azure Arc Jumpstart リポジトリを複製します。

    ```console
    git clone https://github.com/microsoft/azure_arc.git
    ```

2. [バージョン 2.7 以降の Azure CLI をインストールするか、更新します。](/cli/azure/install-azure-cli) 現在インストールされているバージョンを確認するには、次のコマンドを使用してください。

    ```console
    az --version
    ```

3. VMware PowerCLI をインストールします。

    > [!NOTE]
    > このガイドは、現時点での最新バージョンである PowerCLI (12.0.0) でテストされていますが、それ以前のバージョンでも想定通り動作します

    - **サポートされる PowerShell バージョン:** VMware PowerCLI 12.0.0 は、以下の PowerShell バージョンと互換性があります。
      - Windows PowerShell 5.1
      - PowerShell 7
      - 詳細なインストール手順については、[こちら](https://docs.vmware.com/en/VMware-vSphere/7.0/com.vmware.esxi.install.doc/GUID-F02D0C2D-B226-4908-9E5C-2E783D41FE2D.html)を参照してください。最も簡単な方法は、次のコマンドを使用して PowerShell ギャラリーから VMware.PowerCLI モジュールを使用する方法です。

        ```powershell
        Install-Module -Name VMware.PowerCLI
        ```

4. vCenter から VM のインベントリを読み取り、VM の OS レベルでスクリプトを呼び出せるようにするには、次のアクセス許可が必要です。

    - [`VirtualMachine.GuestOperations`](https://docs.vmware.com/en/VMware-vSphere/7.0/com.vmware.vsphere.security.doc/GUID-6A952214-0E5E-4CCF-9D2A-90948FF643EC.html) ユーザー アカウント

    - [読み取り専用ロール](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.security.doc/GUID-93B962A7-93FA-4E96-B68F-AE66D3D6C663.html)が割り当てられている VMware vCenter Server ユーザー

5. Azure サービス プリンシパルを作成します。

    VMware vSphere 仮想マシンを Azure Arc に接続するには、共同作成者ロールが割り当てられた Azure サービス プリンシパルが必要です。 これを作成するには、Azure アカウントにサインインして、次のコマンドを実行します。 このコマンドは、[Azure Cloud Shell](https://shell.azure.com/) 内で実行することもできます。

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

このシナリオの自動化フローは次のとおりです。

1. ユーザーが `vars.ps1` PowerCLI スクリプトを編集します。

2. `scale_deploy.ps1` スクリプトが実行されると、vCenter に対する認証が開始され、Azure Arc の候補となる VM が配置されている場所のターゲット VM フォルダーがスキャンされます。その後、`vars.ps1` および `install_azure_arc_agent.ps1` PowerCLI スクリプトの両方が、[このフォルダー](https://github.com/microsoft/azure_arc/tree/main/azure_arc_servers_jumpstart/vmware/scaled_deployment/powercli/windows)に配置されている VM Windows OS の VM フォルダー内の各 VM にコピーされます。

3. `install-azure_arc_agent.ps1` PowerCLI スクリプトが VM ゲスト OS 上で実行され、VM を Azure Arc にオンボードするために Azure Arc が接続されたマシン エージェントにインストールされます

## <a name="predeployment"></a>展開前

このシナリオの前後を示すため、以下のスクリーンショットに空の専用 Azure リソース グループ、候補の VM がある vCenter VM フォルダー、およびエージェントがインストールされていないことを示す **アプリと機能** ビューを示しています。

![空の Azure リソース グループのスクリーンショット。](./media/vmware-scale-powercli/cli-windows-empty.png)

![Azure Arc エージェントを使用しない、バニラの VMware vSphere 仮想マシンを示すスクリーンショット。](./media/vmware-scale-powercli/cli-windows-vanilla-1.png)

![Azure Arc エージェントを使用しない、バニラの VMware vSphere 仮想マシンを示す別のスクリーンショット。](./media/vmware-scale-powercli/cli-windows-vanilla-2.png)

## <a name="deployment"></a>デプロイ

PowerCLI スクリプトを実行する前に、`install_arc_agent.ps1` スクリプトによって使用される[環境変数](https://github.com/microsoft/azure_arc/blob/main/azure_arc_servers_jumpstart/vmware/scaled_deployment/powercli/windows/vars.ps1)を設定する必要があります。 これらの変数は、先ほど作成した Azure サービス プリンシパル、Azure サブスクリプションとテナント、VMware vSphere の資格情報とデータに基づいています。

1. `az account list` コマンドを使用して、Azure サブスクリプション ID とテナント ID を取得します

2. 「前提条件」セクションで作成した Azure サービス プリンシパル ID とパスワードを使用します。

    ![環境変数のエクスポートを示すスクリーンショット。](./media/vmware-scale-powercli/cli-windows-export-variables.png)

3. 管理者として `azure_arc_servers_jumpstart\vmware\scaled-deploy\powercli\windows` フォルダーから PowerShell セッションを開き、`scale-deploy.ps1` スクリプトを実行します。

    ![PowerShell スクリプトを使用して拡大デプロイする方法を示すスクリーンショット。](./media/vmware-scale-powercli/cli-windows-scale-deploy-1.png)

    ![PowerShell スクリプトを使用して拡大デプロイする方法を示す 2 つ目のスクリーンショット。](./media/vmware-scale-powercli/cli-windows-scale-deploy-2.png)

    ![PowerShell スクリプトを使用して拡大デプロイする方法を示す 3 つ目のスクリーンショット。](./media/vmware-scale-powercli/cli-windows-scale-deploy-3.png)

4. 完了すると、VM には Azure Arc に接続されたマシン エージェントがインストールされ、Azure リソース グループには新しい Azure Arc 対応サーバーが設定されます。

    ![Azure Arc エージェントがインストールされているマシンのスクリーンショット。](./media/vmware-scale-powercli/cli-windows-agent.png)

    ![Azure リソース グループ内の新しい Azure Arc 対応サーバーのスクリーンショット。](./media/vmware-scale-powercli/cli-windows-servers-1.png)

    ![Azure リソース グループ内の新しい Azure Arc 対応サーバーの別のスクリーンショット。](./media/vmware-scale-powercli/cli-windows-servers-2.png)
