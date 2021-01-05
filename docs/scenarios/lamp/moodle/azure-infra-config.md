---
title: Moodle ワーカー ノードを設定する方法
description: Moodle 用の仮想マシン スケール セットを構成する方法について説明します。 プライベート IP アドレスを使用してコントローラーからスケール セットにアクセスする方法を説明します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 11/30/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: plan
ms.openlocfilehash: 902c7b93e88725ec7c37ddaac09f92db8287af49
ms.sourcegitcommit: 32e8e7a835a688eea602f2af1074aa926ab150c3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/18/2020
ms.locfileid: "97687637"
---
# <a name="how-to-set-up-moodle-worker-nodes"></a>Moodle ワーカー ノードを設定する方法

次の手順に従って、Moodle 用の仮想マシン スケールセットつまりワーカー ノードを構成します。

## <a name="virtual-machine-scale-set-instances"></a>仮想マシン スケール セットのインスタンス

仮想マシン スケール セットのインスタンスには、プライベート IP アドレスが割り当てられます。 この IP アドレスには、IP アドレスと同じ仮想ネットワーク内にあるコントローラー仮想マシンでのみアクセスできます。 この記事では、その IP アドレスを設定した後、Moodle の移行で作成される Azure 仮想マシン スケール セットを構成する方法について説明します。

### <a name="access-the-virtual-machine-scale-set"></a>仮想マシン スケール セットにアクセスする

仮想マシン スケール セットにアクセスするには、次の手順のようにします。

1. Azure で仮想マシン スケール セットのインスタンスに使用されるプライベート IP アドレスを特定します。

   1. [Azure portal](https://ms.portal.azure.com/#home) にサインインし、デプロイで作成されたリソース グループを見つけます。

   1. 仮想マシン スケール セット リソースのページを開きます。

   1. 左側のパネルで **[インスタンス]** を選択します。

   1. 実行中のインスタンスを開きます。 **[概要]** セクションで、そのインスタンスに関連付けられているプライベート IP アドレスをコピーします。

1. 次のコマンドを入力して、コントローラー仮想マシンから仮想マシン スケール セットにサインインします。

   ```bash
   sudo -s
   sudo ssh azureadmin@<private IP address>
   ```

   コマンドで、`<private IP address>` は仮想マシン スケール セットのプライベート IP アドレスです。 たとえば、次のように入力します。

   ```bash
   sudo -s
   sudo ssh azureadmin@172.31.X.X
   ```

### <a name="create-a-backup-directory"></a>バックアップ ディレクトリを作成する

`/home/azureadmin` の `storage` という名前のディレクトリに、[移行プロセスの前のステップでバックアップ ファイルを抽出しました](./migration-start.md#back-up-the-current-configuration)。 この `storage` ディレクトリには、`moodle` と `moodledata` ディレクトリ、構成ディレクトリ、およびデータベース バックアップ ファイルが格納されています。 スケール セットの仮想マシン インスタンスにサインインした後、次のコマンドを入力して、これらのファイル用のバックアップ ディレクトリを作成します。

```bash
cd /home/azureadmin/
mkdir -p backup
mkdir -p backup/moodle
```

### <a name="configure-the-php-and-web-server"></a>PHP と Web サーバーを構成する
PHP と Web サーバーを構成するには、次の手順のようにします。

1. PHP のバージョンを変数に設定します。

   ```bash
   _PHPVER=`/usr/bin/php -r "echo PHP_VERSION;" | /usr/bin/cut -c 1,2,3`
   echo $_PHPVER
   ```

1. PHP と Web サーバーの構成のバックアップを作成します。

   ```bash
   sudo mv /etc/nginx/sites-enabled/*.conf /home/azureadmin/backup/
   sudo mv /etc/php/$_PHPVER/fpm/pool.d/www.conf /home/azureadmin/backup/www.conf  
   ```

1. PHP と Web サーバーの構成ファイルをコピーします。

   ```bash
   sudo cp /moodle/config/nginx/*.conf  /etc/nginx/sites-enabled/
   sudo cp /moodle/config/php/www.conf /etc/php/$_PHPVER/fpm/pool.d/
   ```

### <a name="install-missing-extensions"></a>足りない拡張機能をインストールする

足りない拡張機能をインストールするには、次の手順のようにします。

1. オンプレミスにインストールされている PHP 拡張機能の一覧を取得するには、オンプレミスの仮想マシンで次のコマンドを入力します。

   ```bash
   php -m
   ```

1. Azure Resource Manager テンプレートを使用して、次の PHP 拡張機能をインストールします: fpm、cli、curl、zip、pear、mbstring、dev、mcrypt、soap、json、redis、bcmath、gd、mysql、xmlrpc、intl、xml、bz2。

1. オンプレミスの Moodle アプリケーションに、コントローラー仮想マシンにない追加の PHP 拡張機能がある場合は、次のコマンドを使用して手動でインストールします。

   ```bash
   sudo apt-get install -y php-<extension name>
   ```

## <a name="next-steps"></a>次のステップ

引き続き、「[Moodle 移行後のフォローアップ方法](./migration-post.md)」を参照してください。
