---
title: Ubuntu Server 18.04 用の VMware vSphere テンプレートを作成する
description: Ubuntu Server 18.04 用の VMware vSphere テンプレートを作成します。
author: likamrat
ms.author: brblanch
ms.date: 01/29/2021
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: operate
ms.custom: think-tank, e2e-hybrid
ms.openlocfilehash: 1fac9dcacad7ff5f6230cdfdc9e18910a9792109
ms.sourcegitcommit: b8f8b7631aabaab28e9705934bf67dad15e3a179
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101801150"
---
# <a name="create-a-vmware-vsphere-template-for-ubuntu-server-1804"></a>Ubuntu Server 18.04 用の VMware vSphere テンプレートを作成する

この記事では、Ubuntu Server 18.04 VMware vSphere 仮想マシン テンプレートを作成するためのガイダンスを提供します。

## <a name="prerequisites"></a>前提条件

> [!NOTE]
> このガイドは、VMware vSphere に多少精通している読者を想定しています。 また、VMware や Ubuntu のベスト プラクティスをレビューすることを目的としたものではありません。

- [最新の Ubuntu Server 18.04 の ISO ファイルをダウンロードする](https://releases.ubuntu.com/18.04/)

- VMware vSphere 6.5 以上

- ファイルはローカルで使用することもできますが、デプロイを高速にするため、vSphere データストアまたは vCenter コンテンツ ライブラリにアップロードします。

## <a name="creating-ubuntu-1804-vm-template"></a>Ubuntu 18.04 VM テンプレートの作成

### <a name="deploying-and-installing-ubuntu"></a>Ubuntu のデプロイとインストール

- 新しい仮想マシンのデプロイ

    ![新しい VMware vSphere 仮想マシンを作成する方法のスクリーンショット。](./media/vmware-template/ubuntu-template-new-vm-1.png)

    ![新しい VMware vSphere 仮想マシンを作成する方法の 2 番目のスクリーンショット。](./media/vmware-template/ubuntu-template-new-vm-2.png)

    ![新しい VMware vSphere 仮想マシンを作成する方法の 3 番目のスクリーンショット。](./media/vmware-template/ubuntu-template-new-vm-3.png)

    ![新しい VMware vSphere 仮想マシンを作成する方法の 4 番目のスクリーンショット。](./media/vmware-template/ubuntu-template-new-vm-4.png)

    ![新しい VMware vSphere 仮想マシンを作成する方法の 5 番目のスクリーンショット。](./media/vmware-template/ubuntu-template-new-vm-5.png)

    ![新しい VMware vSphere 仮想マシンを作成する方法の 6 番目のスクリーンショット。](./media/vmware-template/ubuntu-template-new-vm-6.png)

- ゲスト OS として **[Ubuntu Linux (64 ビット)]** を必ず選択してください。

    ![Ubuntu Linux (64 ビット) のゲスト OS のスクリーンショット。](./media/vmware-template/ubuntu-template-guest-os.png)

- Ubuntu Server ISO ファイルの場所を示します。

    ![新しい VMware vSphere 仮想マシンを作成する方法の 7 番目のスクリーンショット。](./media/vmware-template/ubuntu-template-new-vm-7.png)

    ![新しい VMware vSphere 仮想マシンを作成する方法の 8 番目のスクリーンショット。](./media/vmware-template/ubuntu-template-new-vm-8.png)

- VM の電源を入れ、Ubuntu のインストールを開始します。 以下の点を除き、具体的な手順はありません:

  - (省略可能:) 静的 IP アドレスの使用を検討する
  - OpenSSH サーバーをインストールする

    ![Ubuntu のインストールの最初のスクリーンショット](./media/vmware-template/ubuntu-template-installation-1.png)

    ![Ubuntu のインストールの 2 番目のスクリーンショット。](./media/vmware-template/ubuntu-template-installation-2.png)

    ![Ubuntu のインストールの 3 番目のスクリーンショット。](./media/vmware-template/ubuntu-template-installation-3.png)

    ![Ubuntu のインストールの 4 番目のスクリーンショット。](./media/vmware-template/ubuntu-template-installation-4.png)

    ![Ubuntu のインストールの 5 番目のスクリーンショット。](./media/vmware-template/ubuntu-template-installation-5.png)

    ![Ubuntu のインストールの 6 番目のスクリーンショット。](./media/vmware-template/ubuntu-template-installation-6.png)

    ![Ubuntu のインストールの 7 番目のスクリーンショット。](./media/vmware-template/ubuntu-template-installation-7.png)

    ![Ubuntu のインストールの 8 番目のスクリーンショット。](./media/vmware-template/ubuntu-template-installation-8.png)

    ![Ubuntu のインストールの 9 番目のスクリーンショット。](./media/vmware-template/ubuntu-template-installation-9.png)

    ![Ubuntu のインストールの 10 番目のスクリーンショット。](./media/vmware-template/ubuntu-template-installation-10.png)

    ![Ubuntu のインストールの 11 番目のスクリーンショット。](./media/vmware-template/ubuntu-template-installation-11.png)

    ![Ubuntu のインストールの 12 番目のスクリーンショット。](./media/vmware-template/ubuntu-template-installation-12.png)

    ![Ubuntu のインストールの 13 番目のスクリーンショット。](./media/vmware-template/ubuntu-template-installation-13.png)

    ![Ubuntu のインストールの 14 番目のスクリーンショット。](./media/vmware-template/ubuntu-template-installation-14.png)

    ![Ubuntu のインストールの 15 番目のスクリーンショット。](./media/vmware-template/ubuntu-template-installation-15.png)

    ![Ubuntu のインストールの 16 番目のスクリーンショット。](./media/vmware-template/ubuntu-template-installation-16.png)

    ![Ubuntu のインストールの 17 番目のスクリーンショット。](./media/vmware-template/ubuntu-template-installation-17.png)

    ![Ubuntu のインストールの 18 番目のスクリーンショット。](./media/vmware-template/ubuntu-template-installation-18.png)

    ![Ubuntu のインストールの 19 番目のスクリーンショット。](./media/vmware-template/ubuntu-template-installation-19.png)

    ![Ubuntu のインストールの 20 番目のスクリーンショット。](./media/vmware-template/ubuntu-template-installation-20.png)

    ![Ubuntu のインストールの 21 番目のスクリーンショット。](./media/vmware-template/ubuntu-template-installation-21.png)

### <a name="post-installation"></a>インストール後

VM をテンプレートに変換する前に、いくつかの操作を行う必要があります。

- OS パッケージを最新の状態にしておくことをお勧めします

    ```console
    sudo apt-get update
    sudo apt-get upgrade -y
    ```

- cloudconfig に元のホスト名が保持され、ホスト名がリセットされることを防ぎます

    ```console
    sudo sed -i 's/preserve_hostname: false/preserve_hostname: true/g' /etc/cloud/cloud.cfg
    sudo truncate -s0 /etc/hostname
    sudo hostnamectl set-hostname localhost
    ```

- 現在のネットワーク構成を削除します

    ```console
    sudo rm /etc/netplan/50-cloud-init.yaml
    ```

- シェルの履歴を消去して VM をシャットダウンします

    ```console
    cat /dev/null > ~/.bash_history && history -c
    sudo shutdown now
    ```

### <a name="convert-to-template"></a>テンプレートに変換する

VM の CPU 数とメモリのリソースを最小限に減らし、CD/DVD ドライブをクライアント デバイスに切り替えて、さらに切断し、VM をテンプレートに変換します。

![仮想マシンの CPU 数とメモリを削減する方法のスクリーンショット。](./media/vmware-template/ubuntu-template-reduce.png)

![仮想マシンをテンプレートに変換する方法のスクリーンショット。](./media/vmware-template/ubuntu-template-convert.png)
