---
title: 手動による Moodle 移行を開始する方法
description: 手動による Moodle 移行を開始する方法について説明します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 11/06/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: plan
ms.openlocfilehash: 2fbaf59de2effb689d160aa22a41c51ed291b98e
ms.sourcegitcommit: a7eb2f6c4465527cca2d479edbfc9d93d1e44bf1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2020
ms.locfileid: "94714903"
---
# <a name="how-to-start-a-manual-moodle-migration"></a>手動による Moodle 移行を開始する方法

Moodle 移行を開始するには、デプロイの完了後に [Azure](https://portal.azure.com/) にログインします。 作成したリソース グループにアクセスすると、作成されたすべてのリソースが見つかります。 次の図は、リソースがどのように作成されるかを示しています。

[リソースの概要](./images/resource-creation-overview.png)

## <a name="controller-virtual-machine"></a>コントローラー仮想マシン

- 無料のオープン ソースのターミナル エミュレーターまたはシリアル コンソール ツールを使用して、コントローラー コンピューターにログインします。
- ホスト名として使用するコントローラー仮想マシンのパブリック IP をコピーします。
- ナビゲーション パネルで **[SSH]** を展開し、 **[認証]** を選択し、Azure Resource Manager テンプレートを使用した Azure インフラストラクチャのデプロイの SSH キー ファイルを見つけます。
- **[Open (開く)]** を選択します。 ユーザー名の入力を求められます。 「**azureadmin**」と入力します (これがテンプレートにハードコーディングされているため)。

[PuTTY ログイン ページ。](./images/putty-login.png)

[PuTTY キー条件。](./images/putty-key-criteria.png)

- SSH キーを参照して選択し、[開く] ボタンをクリックします。

PuTTY の詳細については、[PuTTY の一般的な FAQ/トラブルシューティングの質問](https://documentation.help/PuTTY/faq.html)に関するページを参照してください。

ログインした後、次の一連のコマンドを実行して移行します。

## <a name="download-and-install-azcopy"></a>AzCopy のダウンロードとインストール

次のコマンドを実行して AzCopy をインストールします。

  ```bash
  sudo -s
  wget https://aka.ms/downloadazcopy-v10-linux
  tar -xvf downloadazcopy-v10-linux
  sudo rm /usr/bin/azcopy
  sudo cp ./azcopy_linux_amd64_*/azcopy /usr/bin/
  ```

## <a name="copy-the-backup"></a>バックアップをコピーする

Azure Resource Manager デプロイからコントローラー仮想マシン インスタンスにバックアップ アーカイブをコピーするには、次のようにします。

- 圧縮されたバックアップ ファイル (storage.tar.gz) を、BLOB ストレージからコントローラー仮想マシンの場所 (/home/azureadmin/) にダウンロードします。

  ```bash
  sudo -s
  cd /home/azureadmin/
  azcopy copy `https://storageaccount.blob.core.windows.net/container/BlobDirectoryName<SASToken>` `/home/azureadmin/`
  ```

  例: `azcopy copy 'https://onpremisesstorage.blob.core.windows.net/migration/storage.tar.gz?sv=2019-12-12&ss=' /home/azureadmin/storage.tar.gz`

- 圧縮されたコンテンツをディレクトリに抽出します。

  ```bash
  d /home/azureadmin
  ar -zxvf storage.tar.gz
  ```

## <a name="how-to-migrate-and-configure-a-moodle-application"></a>Moodle アプリケーションを移行して構成する方法

移行する前に、現在の構成をバックアップします。 バックアップ ディレクトリは `storage/at/home/azureadmin` として抽出されます。 このストレージ ディレクトリには、Moodle、moodledata、構成ディレクトリ、データベース バックアップ ファイルが含まれています。 これらは必要な場所にコピーされます。

- バックアップ ディレクトリを作成します。

  ```bash
  cd /home/azureadmin/
  mkdir -p backup
  mkdir -p backup/moodle
  mkdir -p backup/moodle/html
  ```

- Moodle と moodledata のディレクトリのバックアップを作成します。

  ```bash
  mv /moodle/html/moodle /home/azureadmin/backup/moodle/html/moodle
  mv /moodle/moodledata /home/azureadmin/backup/moodle/moodledata
  ```

- オンプレミスの Moodle と moodledata のディレクトリを共有の場所 (`/moodle`) にコピーします。

  ```bash
  cp -rf /home/azureadmin/storage/moodle /moodle/html/
  cp -rf /home/azureadmin/storage/moodledata /moodle/moodledata
  ```

- Moodle データベースを Azure にインポートします。 Azure Database for MySQL インスタンスは、ファイアウォールによって保護されます。 既定では、サーバーとサーバー内部のデータベースに対する接続はすべて拒否されます。 初めて Azure Database for MySQL に接続する前に、ファイアウォールを構成し、クライアント マシンのパブリック ネットワーク IP アドレスまたは IP アドレス範囲を追加します。

  ```bash
  az mysql server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
  ```

- 新しい MySQL サーバーを選択し、 **[接続のセキュリティ]** を選択します。

![**[接続のセキュリティ]** を選択する。](./images/database-connection-security.png)

- ここで IP の追加や、ファイアウォール規則の構成ができます。 規則を作成した後、 **[保存]** を選択します。

これで、mysql コマンド ライン ツールまたは MySQL Workbench ツールを使用してサーバーに接続できます。 接続情報を取得するには、 **[MySQL Server リソース]** ページから、**サーバー名** と **サーバー管理者ログイン名** をコピーします。 この操作を行うには、各フィールドの横にあるコピー ボタンを選択します。

![新しい接続を設定する。](images/database-connection.png)

たとえば、サーバー名が `mydemoserver.mysql.database.azure.com` で、サーバー管理者ログイン名が `myadmin@mydemoserver` の場合、次のようになります。

- データベースをインポートする前に、Azure Database for MySQL サーバーの詳細情報の準備ができていることを確認してください。
- Azure portal に移動し、作成されたリソース グループに移動します。
- Azure Database for MySQL サーバー リソースを選択します。
- [概要] パネルで、Azure Database for MySQL サーバーの詳細 (サーバー名、サーバー管理者ログイン名など) を探します。
- ページの上部にある [パスワードのリセット] ボタンをクリックして、パスワードをリセットします。

これらのデータベース サーバーの詳細を、次のコマンドで使用します。

- オンプレミス データベースを Azure Database for MySQL にインポートします。

- オンプレミス データベースをインポートするデータベースを作成します。

  ```bash
  mysql -h $server_name -u $server_admin_login_name -p$admin_password -e "CREATE DATABASE $moodledbname CHARACTER SET utf8;"
  ```

- 適切なアクセス許可をデータベースに割り当てます。

  ```bash
  mysql -h $server_name -u $server_admin_login_name -p$admin_password -e "GRANT ALL ON $moodledbname.* TO `$server_admin_login_name` IDENTIFIED BY `$admin_password`;"
  ```

- データベースをインポートします。

  ```bash
  mysql -h $server_name -u $server_admin_login_name -p$admin_password $moodledbname < /home/azureadmin/storage/database.sql
  ```

- Moodle 構成ファイル (/moodle/config.php) 内のデータベースの詳細を更新します。

config.php 内のパラメーターを更新します。 このタスクの DNS 名を必ず保存してください。

- Azure portal に移動し、リソース グループを見つけます。

- **ロード バランサーのパブリック IP** を見つけ、 **[概要]** パネルから DNS 名を取得します。

  dbhost、dbname、dbuser、dbpass、dataroot、wwwroot

  ```bash
  cd /moodle/html/moodle/
  nano config.php

- Update the database details, and save the file.

  For example:

  - $CFG->dbhost    = `localhost`;                - Change `localhost` to the server name.
  - $CFG->dbname    = `moodle`;                   - Change `moodle` to the newly created database name.
  - $CFG->dbuser    = `root`;                     - Change `root` to the server admin login name.
  - $CFG->dbpass    = `password`;                 - Change `password` to the server admin login password.
  - $CFG->wwwroot   = `https://on-premises.com`;  - Change `on-premises` to the DNS name.
  - $CFG->dataroot  = `/var/moodledata`;          - Change the path to `/moodle/moodledata`.

The on-premises `dataroot` directory can be stored at any location. After making the changes, save the file. Press `CTRL+O` to save and `CTRL+X` to exit.

Configure directory permissions.

- Set 755 and www-data owner:group permissions to the Moodle directory.

  ```bash
  sudo chmod 755 /moodle/html/moodle sudo chown -R www-data:www-data /moodle/html/moodle
   ```

- 770 と www-data の所有者:グループのアクセス許可を moodledata ディレクトリに設定します。

  ```bash
  sudo chmod 770 /moodle/moodledata sudo chown -R www-data:www-data /moodle/moodledata
  ```

- nginx.conf ファイルを更新します。

  ```bash
  sudo mv /etc/nginx/sites-enabled/*.conf /home/azureadmin/backup/
  cd /home/azureadmin/storage/configuration/
  sudo cp -rf nginx/sites-enabled/*.conf /etc/nginx/sites-enabled/
  ```

- PHP conf ファイルを更新します。

  ```bash
  _PHPVER=`/usr/bin/php -r "echo PHP_VERSION;" | /usr/bin/cut -c 1,2,3`
  echo $_PHPVER
  sudo mv /etc/php/$_PHPVER/fpm/pool.d/www.conf /home/azureadmin/backup/www.conf
  sudo cp -rf /home/azureadmin/storage/configuration/php/$_PHPVER/fpm/pool.d/www.conf /etc/php/$_PHPVER/fpm/pool.d/
  ```

- 不足している PHP 拡張機能をインストールします。 Azure Resource Manager テンプレートによって、次の PHP 拡張機能がインストールされます: fpm、cli、curl、zip、pear、mbstring、dev、mcrypt、soap、json、redis、bcmath、gd、mysql、xmlrpc、intl、xml、bz2。 オンプレミスにインストールされている PHP 拡張機能の一覧を取得するには、次のコマンドを実行します。

  ```bash
  php -m
  ```

- コントローラー仮想マシンに存在しない追加の PHP 拡張機能がオンプレミスにある場合は、これらを手動でインストールできます。

  ```bash
  sudo apt-get install -y php-<extensionName>
  ```

- DNS 名とルート ディレクトリの場所を更新します。 オンプレミスの DNS 名を使用して Azure クラウドの DNS 名を更新します。 このコマンドによって、構成ファイルが開きます。

  ```bash
  nano /etc/nginx/sites-enabled/*.conf
  ```

- Azure Resource Manager テンプレートのデプロイでは、nginx サーバーがポート 81 で設定されます。 サーバー ポートが 81 でない場合は、81 に更新します。

- サーバー名を更新します。 たとえば、server_name on-premises.com の場合は、DNS 名を使用して on-premises.com を更新します。 ほとんどの場合、DNS は移行で変わらない可能性があります。

- HTML ルート ディレクトリの場所を更新します。 たとえば、`root /var/www/html/moodle;` の場合は、`root /moodle/html/moodle;` になるように更新します。

- オンプレミスのルート ディレクトリは、任意の場所に配置できます。

- 変更が完了したら、`CTRL+O` を押してファイルを保存し、`CTRL+X` で終了します。

- Web サーバーを再起動します。

  ```bash
  sudo systemctl restart nginx
  sudo systemctl restart php$_PHPVER-fpm  
  ```

- Web サーバーを停止します。 要求が Azure Load Balancer に達すると、その要求は仮想マシン スケール セットのインスタンスにリダイレクトされますが、コントローラー仮想マシンにはリダイレクトされません。

  ```bash
  sudo systemctl stop nginx
  sudo systemctl stop php$_PHPVER-fpm  
  ```

## <a name="how-to-copy-configuration-files"></a>構成ファイルをコピーする方法

PHP および Web サーバーの構成ファイルを共有の場所にコピーします。 構成ファイルは、共有の場所から仮想マシン スケール セットのインスタンスにコピーできます。

共有の場所に構成用のディレクトリを作成するには、次のようにします。

```bash
mkdir -p /moodle/config
mkdir -p /moodle/config/php
mkdir -p /moodle/config/nginx
```

PHP および Web サーバーの構成ファイルを構成ディレクトリにコピーするには、次のようにします。

```bash
cp /etc/nginx/sites-enabled/* /moodle/config/nginx
cp /etc/php/$_PHPVER/fpm/pool.d/www.conf /moodle/config/php
```

## <a name="next-steps"></a>次のステップ

Moodle 移行プロセスの次の手順については、[Moodle コントローラー インスタンスとワーカー ノードを設定する方法 (Azure インフラストラクチャ構成)](./azure-infra-config.md) に関するページを参照してください。
