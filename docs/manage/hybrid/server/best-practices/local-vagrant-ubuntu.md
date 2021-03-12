---
title: Vagrant でホストされているローカル Ubuntu サーバーをデプロイし、それを Azure Arc に接続する
description: Vagrant でホストされているローカルの Ubuntu サーバーをデプロイし、それを Azure Arc に接続します。
author: likamrat
ms.author: brblanch
ms.date: 01/29/2021
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: operate
ms.custom: think-tank, e2e-hybrid
ms.openlocfilehash: 367b08aa62b68120def9e9788129cbe21b483d6c
ms.sourcegitcommit: 9e4bc0e233a24642853f5e8acbeb9746b2444024
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102111975"
---
# <a name="deploy-a-local-ubuntu-server-hosted-with-vagrant-and-connect-it-to-azure-arc"></a>Vagrant でホストされているローカル Ubuntu サーバーをデプロイし、それを Azure Arc に接続する

この記事では、[Vagrant](https://www.vagrantup.com/) を使用してローカルの **Ubuntu** 仮想マシンをデプロイし、それを Azure Arc 対応サーバー リソースとして接続するためのガイダンスを提供します。

## <a name="prerequisites"></a>前提条件

1. Azure Arc Jumpstart リポジトリをクローンします。

    ```console
    git clone https://github.com/microsoft/azure_arc.git
    ```

2. [バージョン 2.7 以降の Azure CLI をインストールするか、更新します](/cli/azure/install-azure-cli)。 次のコマンドを使用して、現在インストールされているバージョンを確認します。

    ```console
    az --version
    ```

3. Vagrant は、基になるハイパーバイザーに依存しています。 このガイドでは、"Oracle VM VirtualBox" を使用していきます。

    1. [VirtualBox](https://www.virtualbox.org/wiki/Downloads) をインストールします。

        - macOS ユーザーのかたは、`brew cask install virtualbox` を実行します。
        - Windows ユーザーのかたは、[Chocolatey パッケージ](https://chocolatey.org/packages/virtualbox)を使用できます。
        - Linux ユーザーのかたは、すべてのパッケージ インストール方法を[ここ](https://www.virtualbox.org/wiki/Linux_Downloads)でご覧ください。

    2. [Vagrant](https://www.vagrantup.com/docs/installation) をインストールします。

        - macOS ユーザーのかたは、`brew cask install vagrant` を実行します。
        - Windows ユーザーのかたは、[Chocolatey パッケージ](https://chocolatey.org/packages/vagrant)を使用できます。
        - Linux ユーザーのかたは、[こちら](https://www.vagrantup.com/downloads)をご覧ください。

4. Azure サービス プリンシパルを作成します。

    Vagrant 仮想マシンを Azure Arc に接続するには、共同作成者ロールが割り当てられた Azure サービス プリンシパルが必要です。 これを作成するには、自分の Azure アカウントにサインインして、次のコマンドを実行します。 このコマンドは、[Azure Cloud Shell](https://shell.azure.com/) 内で実行することもできます。

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

5. Vagrant ファイルは、必要なすべてのアーティファクトをインストールするため、そして環境変数を挿入するためのスクリプトを VM OS 上で実行します。 [`scripts/vars.sh`](https://github.com/microsoft/azure_arc/blob/main/azure_arc_servers_jumpstart/local/vagrant/ubuntu/scripts/vars.sh) シェル スクリプトを、作成した Azure サービス プリンシパルに合わせて編集します。

    - `subscriptionId` = お使いの Azure サブスクリプション ID
    - `appId` = お使いの Azure サービス プリンシパル名
    - `password` = お使いの Azure サービス プリンシパル パスワード
    - `tenantId` = お使いの Azure テナント ID
    - `resourceGroup` = Azure リソース グループ名
    - `location` = Azure リージョン

## <a name="deployment"></a>デプロイ

Vagrant のデプロイと同様に、[vagrantfile](https://github.com/microsoft/azure_arc/blob/main/azure_arc_servers_jumpstart/local/vagrant/ubuntu/Vagrantfile) と [Vagrant box](https://www.vagrantup.com/docs/boxes) が必要です。 大まかに言えば、デプロイでは以下が行われます。

- Ubuntu 16.04 イメージ ファイル [Vagrant box](https://app.vagrantup.com/ubuntu/boxes/xenial64) をダウンロードする
- インストール スクリプトを実行する

`scripts/vars.sh` スクリプトを環境に合わせて編集した後で、`Vagrantfile` フォルダーから、`vagrant up` を実行します。 VM を作成するのはこのときが初めてなので、最初の実行ではデプロイによって Ubuntu box が初めてダウンロードされるため、その後に実行する場合より **はるかに時間がかかります**。

![`vagrant up` コマンドのスクリーンショット。](./media/local-vagrant/vagrant-ubuntu-vagrant-up.png)

ダウンロードが完了すると、プロビジョニングが始まります。 次のスクリーンショットに示すように、プロセスは 3 分以内に完了します。

![完了した `vagrant up` コマンドのスクリーンショット。](./media/local-vagrant/vagrant-ubuntu-vagrant-up-complete.png)

完了すると、ローカルの Ubuntu VM がデプロイされ、新しいリソース グループ内の新しい Azure Arc 対応サーバーとして接続されます。

![Azure portal 内の Azure Arc 対応サーバーのスクリーンショット。](./media/local-vagrant/vagrant-ubuntu-server.png)

![Azure portal 内の Azure Arc 対応サーバーの詳細のスクリーンショット。](./media/local-vagrant/vagrant-ubuntu-server-details.png)

## <a name="semi-automated-deployment-optional"></a>半自動デプロイ (省略可能)

最後の段階では、VM を新しい Azure Arc 対応サーバー リソースとして登録します。

![`vagrant up` コマンドの別のスクリーンショット。](./media/local-vagrant/vagrant-ubuntu-vagrant-up-2.png)

実際の登録プロセスをデモンストレーションまたはコントロールする場合は、次の手順を行います。

1. [`install_arc_agent`](https://github.com/microsoft/azure_arc/blob/main/azure_arc_servers_jumpstart/local/vagrant/ubuntu/scripts/install_arc_agent.sh) シェル スクリプトで、`run connect command` セクションをコメント アウトしてファイルを保存します。 リソース グループの作成をコメント アウトまたは変更することもできます。

    ![`azcmagent connect` コマンドのスクリーンショット。](./media/local-vagrant/vagrant-ubuntu-azcmagent.png)

    ![`az group create` コマンドのスクリーンショット。](./media/local-vagrant/vagrant-ubuntu-azgroup-create.png)

2. `vagrant ssh` コマンドを使用して VM に SSH 接続します。

    ![Vagrant マシンに接続する SSH キーのスクリーンショット。](./media/local-vagrant/vagrant-ubuntu-ssh.png)

3. 環境変数を使用して、コメント アウトした同じ `azcmagent connect` コマンドを実行します。

    ![`azcmagent connect` コマンドの別のスクリーンショット。](./media/local-vagrant/vagrant-ubuntu-azcmagent-2.png)

## <a name="delete-the-deployment"></a>デプロイの削除

デプロイ全体を削除するには、`vagrant destroy -f` コマンドを実行します。 vagrantfile には、実際の VM を破棄する前に Azure リソース グループを削除するスクリプトを実行する `before: destroy` Vagrant トリガーが含まれています。

![`vagrant destroy` コマンドのスクリーンショット。](./media/local-vagrant/vagrant-ubuntu-vagrant-destroy.png)
