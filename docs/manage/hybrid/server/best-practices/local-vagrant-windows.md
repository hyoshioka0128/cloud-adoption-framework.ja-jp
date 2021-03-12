---
title: Vagrant によってホストされるローカルの Windows Server インスタンスをデプロイして Azure Arc に接続する
description: Vagrant によってホストされるローカルの Windows Server インスタンスをデプロイして、Azure Arc に接続します。
author: likamrat
ms.author: brblanch
ms.date: 01/29/2021
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: operate
ms.custom: think-tank, e2e-hybrid
ms.openlocfilehash: f2a0dc375146f9f35bb3b1d91cf2cb039510fb56
ms.sourcegitcommit: b8f8b7631aabaab28e9705934bf67dad15e3a179
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101800646"
---
# <a name="deploy-a-local-windows-server-instance-hosted-by-vagrant-and-connect-it-to-azure-arc"></a>Vagrant によってホストされるローカルの Windows Server インスタンスをデプロイして Azure Arc に接続する

次の記事では、[Vagrant](https://www.vagrantup.com/) を使用してローカルの **Windows 10** 仮想マシンをデプロイし、それを Azure Arc 対応サーバー リソースとして接続するためのガイダンスを提供します。

## <a name="prerequisites"></a>前提条件

1. Azure Arc Jumpstart リポジトリをクローンします。

    ```console
    git clone https://github.com/microsoft/azure_arc.git
    ```

2. [バージョン 2.7 以降の Azure CLI をインストールするか、更新します](/cli/azure/install-azure-cli)。 次のコマンドを使用して、現在インストールされているバージョンを確認します。

    ```console
    az --version
    ```

3. Vagrant は、基になるハイパーバイザーに依存しています。 このガイドでは、Oracle VM VirtualBox を使用します。

    1. [VirtualBox](https://www.virtualbox.org/wiki/Downloads) をインストールします。

        - macOS ユーザーの場合は、`brew cask install virtualbox` を実行します。
        - Windows ユーザーの場合は、[Chocolatey パッケージ](https://chocolatey.org/packages/virtualbox)を使用できます。
        - Linux ユーザーの場合は、すべてのパッケージのインストール方法を[こちら](https://www.virtualbox.org/wiki/Linux_Downloads)でご覧ください。

    2. [Vagrant](https://www.vagrantup.com/docs/installation) をインストールします。

        - macOS ユーザーの場合は、`brew cask install vagrant` を実行します。
        - Windows ユーザーの場合は、[Chocolatey パッケージ](https://chocolatey.org/packages/vagrant)を使用できます。
        - Linux ユーザーの場合は、[こちら](https://www.vagrantup.com/downloads)をご覧ください。

4. Azure サービス プリンシパルを作成します。

    Vagrant 仮想マシンを Azure Arc に接続するには、共同作成者ロールが割り当てられた Azure サービス プリンシパルが必要です。 これを作成するには、自分の Azure アカウントにサインインして、次のコマンドを実行します。 このコマンドは、[Azure Cloud Shell](https://shell.azure.com/) で実行することもできます。

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
    > サービス プリンシパルのスコープを特定の [Azure サブスクリプションとリソース グループ](/cli/azure/ad/sp)に設定することを強くお勧めします。

- vagrantfile は、必要なすべての成果物をインストールし、環境変数を挿入するためのスクリプトを VM OS 上で実行します。 [`scripts/vars.ps1`](https://github.com/microsoft/azure_arc/blob/main/azure_arc_servers_jumpstart/local/vagrant/windows/scripts/vars.ps1) PowerShell スクリプトを、作成した Azure サービス プリンシパルに合わせて編集します。

  - `subscriptionId` = お使いの Azure サブスクリプション ID
  - `appId` = お使いの Azure サービス プリンシパル名
  - `password` = お使いの Azure サービス プリンシパル パスワード
  - `tenantId` = お使いの Azure テナント ID
  - `resourceGroup` = Azure リソース グループ名
  - `location` = Azure リージョン

## <a name="deployment"></a>デプロイ

Vagrant のすべてのデプロイと同様に、[vagrantfile](https://github.com/microsoft/azure_arc/blob/main/azure_arc_servers_jumpstart/local/vagrant/windows/Vagrantfile) と [Vagrant box](https://www.vagrantup.com/docs/boxes) が必要です。 大まかに、デプロイでは以下が行われます。

- Windows 10 イメージ ファイル [Vagrant box](https://app.vagrantup.com/StefanScherer/boxes/windows_10) ダウンロードする
- Azure Arc インストール スクリプトを実行する

`scripts/vars.ps1` スクリプトをお使いの環境に合わせて編集した後で、`Vagrantfile` フォルダーから `vagrant up` を実行します。 今回 VM を初めて作成するため、この最初の実行では、その後に実行する場合より **はるかに時間がかかります**。 これは、デプロイによって Windows 10 ボックスが初めてダウンロードされるためです。

![`vagrant up` コマンドを実行中のスクリーンショット。](./media/local-vagrant/vagrant-windows-cmd.png)

ダウンロードが完了すると、実際のプロビジョニングが開始します。 次のスクリーンショットに示されているように、このプロセスには 7 分 から 10 分かかります。

![完了した `vagrant up` コマンドのスクリーンショット。](./media/local-vagrant/vagrant-windows-complete.png)

完了すると、ローカルの Windows 10 VM がデプロイされ、新しいリソース グループ内の新しい Azure Arc 対応サーバーとして接続されます。

![Azure portal 内の Azure Arc 対応サーバーのスクリーンショット。](./media/local-vagrant/vagrant-windows-server.png)

![Azure portal 内の Azure Arc 対応サーバーの詳細のスクリーンショット。](./media/local-vagrant/vagrant-windows-server-details.png)

## <a name="semi-automated-deployment-optional"></a>半自動デプロイ (オプション)

実行の最後の手順では、VM を新しい Azure Arc 対応サーバー リソースとして登録します。

![完了した `vagrant up` コマンドの別のスクリーンショット。](./media/local-vagrant/vagrant-windows-complete-2.png)

実際の登録プロセスをデモンストレーションまたはコントロールする場合は、次の手順を行います。

1. [`install_arc_agent`](https://github.com/microsoft/azure_arc/blob/main/azure_arc_servers_jumpstart/local/vagrant/windows/scripts/install_arc_agent.ps1) PowerShell スクリプトで、`run connect command` セクションをコメント アウトしてファイルを保存します。 リソース グループの作成をコメント アウトまたは変更することもできます。

    ![`install_arc_agent` PowerShell スクリプトのスクリーンショット。](./media/local-vagrant/vagrant-windows-install-arc-agent.png)

    ![`az group create` コマンドのスクリーンショット。](./media/local-vagrant/vagrant-windows-az-group-create.png)

2. `vagrant rdp` コマンドを使用して VM を RDP 接続します。 `vagrant/vagrant` をユーザー名/パスワードとして使用します。

    ![Vagrant サーバーに Microsoft リモート デスクトップ プロトコルでアクセスする際のスクリーンショット。](./media/local-vagrant/vagrant-windows-rdp.png)

3. PowerShell ISE を **管理者として** 開き、`C:\runtime\vars.ps1` ファイルを環境変数で編集します。

    ![Windows PowerShell ISE のスクリーンショット。](./media/local-vagrant/vagrant-windows-ise.png)

4. `Invoke-Expression "C:\runtime\vars.ps1"` コマンド、`az group create --location $env:location --name $env:resourceGroup --subscription $env:subscriptionId` コマンド、`azcmagent connect` コマンドを貼り付けて、スクリプトを実行します。

    ![スクリプトを実行している PowerShell ISE のスクリーンショット。](./media/local-vagrant/vagrant-windows-ise-script.png)

## <a name="delete-the-deployment"></a>デプロイの削除

デプロイ全体を削除するには、`vagrant destroy -f` コマンドを実行します。 vagrantfile には、実際の VM を破棄する前に Azure リソース グループを削除するコマンドを実行する `before: destroy` Vagrant トリガーが含まれています。

![`vagrant destroy` コマンドのスクリーンショット。](./media/local-vagrant/vagrant-windows-vagrant-destroy.png)
