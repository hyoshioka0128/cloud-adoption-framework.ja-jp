---
title: Windows Server 2019 用の VMware vSphere テンプレートを作成する
description: Windows Server 2019 用の VMware vSphere テンプレートを作成します。
author: likamrat
ms.author: brblanch
ms.date: 01/29/2021
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: operate
ms.custom: think-tank, e2e-hybrid
ms.openlocfilehash: 16a110dd380149aaa72cbb316cbb549eec12ef42
ms.sourcegitcommit: 9e4bc0e233a24642853f5e8acbeb9746b2444024
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102114270"
---
# <a name="create-a-vmware-vsphere-template-for-windows-server-2019"></a>Windows Server 2019 用の VMware vSphere テンプレートを作成する

この記事では、Windows Server 2019 VMware vSphere 仮想マシン テンプレートを作成するためのガイダンスを提供します。

## <a name="prerequisites"></a>前提条件

> [!NOTE]
> このガイドは、VMware vSphere にある程度精通し、Windows Server のインストール方法に関する知識がある読者を想定しています。 また、VMware と Windows のベスト プラクティスについても確認しません。

- [最新の Windows Server ISO ファイルをダウンロードする](https://www.microsoft.com/windows-server/trial)

- VMware vSphere 6.5 以上

- ファイルはローカルで使用することもできますが、デプロイを高速にするには、vSphere データストアまたは vCenter コンテンツ ライブラリにアップロードすることをお勧めします。

## <a name="creating-windows-server-2019-vm-template"></a>Windows Server 2019 VM テンプレートの作成

### <a name="deploying-and-installing-windows-server"></a>Windows Server のデプロイとインストール

1. 新しい仮想マシンをデプロイします。

    ![新しい VMware vSphere 仮想マシンを作成する方法を示すスクリーンショット。](./media/vmware-template/windows-template-new-vm-1.png)

    ![新しい VMware vSphere 仮想マシンを作成する方法を示す 2 番目のスクリーンショット。](./media/vmware-template/windows-template-new-vm-2.png)

    ![新しい VMware vSphere 仮想マシンを作成する方法を示す 3 番目のスクリーンショット。](./media/vmware-template/windows-template-new-vm-3.png)

    ![新しい VMware vSphere 仮想マシンを作成する方法を示す 4 番目のスクリーンショット。](./media/vmware-template/windows-template-new-vm-4.png)

    ![新しい VMware vSphere 仮想マシンを作成する方法を示す 5 番目のスクリーンショット。](./media/vmware-template/windows-template-new-vm-5.png)

    ![新しい VMware vSphere 仮想マシンを作成する方法を示す 6 番目のスクリーンショット。](./media/vmware-template/windows-template-new-vm-6.png)

2. ゲスト OS として **[Microsoft Windows Server 2016 or later (64-bit)]\(Microsoft Windows Server 2016 以降 (64 ビット)\)** を必ず選択してください。

    ![Windows Server ゲスト OS のスクリーンショット。](./media/vmware-template/windows-template-guest-os.png)

3. Windows Server ISO ファイルの場所を指定します。

    ![新しい VMware vSphere 仮想マシンを作成する方法を示す 7 番目のスクリーンショット。](./media/vmware-template/windows-template-new-vm-7.png)

    ![新しい VMware vSphere 仮想マシンを作成する方法を示す 8 番目のスクリーンショット。](./media/vmware-template/windows-template-new-vm-8.png)

- VM の電源を入れ、Windows Server のインストールを開始します。

    ![Windows Server のインストールを示す最初のスクリーンショット。](./media/vmware-template/windows-template-installation-1.png)

    ![Windows Server のインストールを示す 2 番目のスクリーンショット。](./media/vmware-template/windows-template-installation-2.png)

    ![Windows Server のインストールを示す 3 番目のスクリーンショット。](./media/vmware-template/windows-template-installation-3.png)

    ![Windows Server のインストールを示す 4 番目のスクリーンショット。](./media/vmware-template/windows-template-installation-4.png)

    ![Windows Server のインストールを示す 5 番目のスクリーンショット。](./media/vmware-template/windows-template-installation-5.png)

    ![Windows Server のインストールを示す 6 番目のスクリーンショット。](./media/vmware-template/windows-template-installation-6.png)

    ![Windows Server のインストールを示す 7 番目のスクリーンショット。](./media/vmware-template/windows-template-installation-7.png)

### <a name="post-installation"></a>インストール後

VM をテンプレートに変換する前に、いくつかの操作が必要です。

1. VMware ツールをインストールし、再起動します。

    ![VMware ツールのインストールを示す最初のスクリーンショット。](./media/vmware-template/windows-template-tools-1.png)

    ![VMware ツールのインストールを示す 2 番目のスクリーンショット。](./media/vmware-template/windows-template-tools-2.png)

    ![VMware ツールのインストールを示す 3 番目のスクリーンショット。](./media/vmware-template/windows-template-tools-3.png)

    ![VMware ツールのインストールを示す 4 番目のスクリーンショット。](./media/vmware-template/windows-template-tools-4.png)

    ![VMware ツールのインストールを示す 5 番目のスクリーンショット。](./media/vmware-template/windows-template-tools-5.png)

    ![VMware ツールのインストールを示す 6 番目のスクリーンショット。](./media/vmware-template/windows-template-tools-6.png)

    ![VMware ツールのインストールを示す 7 番目のスクリーンショット。](./media/vmware-template/windows-template-tools-7.png)

    ![VMware ツールのインストールを示す 8 番目のスクリーンショット。](./media/vmware-template/windows-template-tools-8.png)

    ![VMware ツールのインストールを示す 9 番目のスクリーンショット。](./media/vmware-template/windows-template-tools-9.png)

2. Windows 更新プログラムを実行します。

3. Powershell で`Set-ExecutionPolicy -ExecutionPolicy Bypass` コマンドを実行して、PowerShell 実行ポリシーを `Bypass` に変更します (後でグループ ポリシーまたは PowerShell スクリプトを使用して調整可能)。

4. [`allow_winrm`](https://github.com/microsoft/azure_arc/blob/main/azure_arc_servers_jumpstart/vmware/winsrv/terraform/scripts/allow_winrm.ps1) PowerShell スクリプトを実行して、OS への WinRM 通信を許可 します。

5. 下記のいずれも必須ではありませんが、Windows テンプレート向けに検討する必要があります。

    - ユーザー アカウント制御の無効化 (後でグループ ポリシーまたは PowerShell スクリプトを使用して調整可能)
    - Windows Defender ファイアウォールをオフにする (後でグループ ポリシーまたは PowerShell スクリプトを使用して調整可能)
    - Internet Explorer セキュリティ強化の構成 (ESC) の無効化 (後でグループ ポリシーまたは PowerShell スクリプトを使用して調整可能)
    - リモート デスクトップの有効化
    - PowerShell で [Chocolatey](https://chocolatey.org/install) をインストールする

      ```powershell
      Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))
      ```

    - テンプレートに含める可能性のあるすべてのベースライン アプリケーションをインストールする

### <a name="convert-to-template"></a>テンプレートに変換する

VM の CPU 数とメモリのリソースを最小限に減らし、CD/DVD ドライブをクライアント デバイスに切り替えて、さらに切断し、VM をテンプレートに変換します。

![仮想マシンの CPU 数とメモリを削減する方法のスクリーンショット。](./media/vmware-template/windows-template-reduce.png)

![仮想マシンをテンプレートに変換する方法のスクリーンショット。](./media/vmware-template/windows-template-convert.png)
