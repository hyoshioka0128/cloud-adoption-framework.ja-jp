---
title: VMware PowerCLI を使用して、VMware vSphere Linux 仮想マシンのオンボードを Azure Arc にスケーリングする
description: VMware PowerCLI を使用して、VMware vSphere Linux 仮想マシンのオンボードを Azure Arc にスケーリングします。
author: likamrat
ms.author: brblanch
ms.date: 01/29/2021
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: operate
ms.custom: think-tank, e2e-hybrid
ms.openlocfilehash: 539c364ad0a6863ce82035a54c7a47031b5213be
ms.sourcegitcommit: b8f8b7631aabaab28e9705934bf67dad15e3a179
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101801061"
---
# <a name="use-vmware-powercli-to-scale-onboarding-vmware-vsphere-linux-virtual-machines-to-azure-arc"></a>VMware PowerCLI を使用して、VMware vSphere Linux 仮想マシンのオンボードを Azure Arc にスケーリングする

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
    > このガイドは、掲載時の最新バージョンである PowerCLI (12.0.0) でテストされていますが、それ以前のバージョンでも想定通り動作します。

    - **サポートされる PowerShell バージョン:** VMware PowerCLI 12.0.0 は、以下の PowerShell バージョンと互換性があります。
        - Windows PowerShell 5.1
        - PowerShell 7
        - 詳細なインストール手順については、[こちら](https://docs.vmware.com/en/VMware-vSphere/7.0/com.vmware.esxi.install.doc/GUID-F02D0C2D-B226-4908-9E5C-2E783D41FE2D.html)を参照してください。最も簡単な方法は、次のコマンドを使用して PowerShell ギャラリーから `VMware.PowerCLI` モジュールを使用する方法です。

          ```powershell
          Install-Module -Name VMware.PowerCLI
          ```

4. vCenter から VM のインベントリを読み取り、VM の OS レベルでスクリプトを呼び出せるようにするには、次のアクセス許可が必要です。

    - [`VirtualMachine.GuestOperations`](https://docs.vmware.com/en/VMware-vSphere/7.0/com.vmware.vsphere.security.doc/GUID-6A952214-0E5E-4CCF-9D2A-90948FF643EC.html) ユーザー アカウント

    - [読み取り専用ロール](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.security.doc/GUID-93B962A7-93FA-4E96-B68F-AE66D3D6C663.html)が割り当てられている VMware vCenter Server ユーザー

5. Azure サービス プリンシパルを作成します。

    VMware vSphere 仮想マシンを Azure Arc に接続するには、共同作成者ロールが割り当てられた Azure サービス プリンシパルが必要です。 作成するには、自分の Azure アカウントにサインインして、次のコマンドを実行します。 このコマンドは、[Azure Cloud Shell](https://shell.azure.com/) 内で実行することもできます。

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

このシナリオの自動化フローには、次の手順が含まれます。

1. [`vars.ps1`](https://github.com/microsoft/azure_arc/blob/main/azure_arc_servers_jumpstart/vmware/scaled_deployment/powercli/linux/vars.ps1) PowerCLI スクリプトを編集します。

2. [`scale-deploy.ps1`](https://github.com/microsoft/azure_arc/blob/main/azure_arc_servers_jumpstart/vmware/scaled_deployment/powercli/linux/scale_deploy.ps1) PowerShell スクリプトを実行すると、以下が行われます。

    - スクリプトにより、ユーザーの Azure 環境変数を基に `vars.sh` シェル スクリプトが自動生成されます。

    - スクリプトが実行されると、vCenter に対する認証が開始され、Azure Arc の候補となる VM が配置されている場所のターゲット VM フォルダーがスキャンされます。自動生成された `vars.sh` および `install-azure-arc-agent.sh` シェル スクリプトの両方が、`/vmware/scaled-deploy/powercli/linux` に配置されている VM Linux OS の VM フォルダー内の各 VM にコピーされます。

3. `install-azure-arc-agent.sh` シェル スクリプトが VM ゲスト OS 上で実行され、VM を Azure Arc にオンボードするために Azure Arc が接続されたマシン エージェントにインストールされます。

## <a name="predeployment"></a>展開前

このシナリオの前後を示すため、以下のスクリーンショットに空の専用 Azure リソース グループ、候補の VM がある vCenter VM フォルダー、およびエージェントがインストールされていないことを示す `/var/opt/` ディレクトリを示しています。

![空の Azure リソース グループのスクリーンショット。](./media/vmware-scale-powercli/cli-linux-empty.png)

![Azure Arc エージェントを使用しない、バニラの VMware vSphere 仮想マシンを示すスクリーンショット。](./media/vmware-scale-powercli/cli-linux-vanilla-1.png)

![Azure Arc エージェントを使用しない、バニラの VMware vSphere 仮想マシンを示す別のスクリーンショット。](./media/vmware-scale-powercli/cli-linux-vanilla-2.png)

## <a name="deployment"></a>デプロイ

PowerCLI スクリプトを実行する前に、`install-azure-arc-agent.sh` スクリプトによって使用される[環境変数](https://github.com/microsoft/azure_arc/blob/main/azure_arc_servers_jumpstart/vmware/scaled_deployment/powercli/linux/vars.ps1)を設定する必要があります。 これらの変数は、先ほど作成した Azure サービス プリンシパル、Azure サブスクリプションとテナント、VMware vSphere の資格情報とデータに基づいています。

- `az account list` コマンドを使用して、Azure サブスクリプション ID とテナント ID を取得します

- 「前提条件」セクションで作成した Azure サービス プリンシパル ID とパスワードを使用します。

    ![環境変数のエクスポートを示すスクリーンショット。](./media/vmware-scale-powercli/cli-linux-export-variables.png)

- 管理者として `azure_arc_servers_jumpstart\vmware\scaled-deploy\powercli\linux` フォルダーから PowerShell セッションを開き、`scale-deploy.ps1` スクリプトを実行します。

    ![PowerShell スクリプトを使用して拡大デプロイする方法を示すスクリーンショット。](./media/vmware-scale-powercli/cli-linux-scale-deploy-1.png)

    ![PowerShell スクリプトを使用して拡大デプロイする方法を示す 2 つ目のスクリーンショット。](./media/vmware-scale-powercli/cli-linux-scale-deploy-2.png)

    ![PowerShell スクリプトを使用して拡大デプロイする方法を示す 3 つ目のスクリーンショット。](./media/vmware-scale-powercli/cli-linux-scale-deploy-3.png)

- 完了すると、VM には Azure Arc に接続されたマシン エージェントがインストールされ、Azure リソース グループには新しい Azure Arc 対応サーバーが設定されます。

    ![Azure Arc エージェントがインストールされているマシンのスクリーンショット。](./media/vmware-scale-powercli/cli-linux-agent.png)

    ![Azure リソース グループ内の新しい Azure Arc 対応サーバーのスクリーンショット。](./media/vmware-scale-powercli/cli-linux-servers-1.png)

    ![Azure リソース グループ内の新しい Azure Arc 対応サーバーの別のスクリーンショット。](./media/vmware-scale-powercli/cli-linux-servers-2.png)
