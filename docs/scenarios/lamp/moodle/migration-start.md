---
title: 手動による Moodle 移行の手順
description: 以下の手順に従って、Moodle のオンプレミスのバックアップ アーカイブを Azure リソースにインポートし、Moodle アプリケーションを構成します。
author: UmakanthOS
ms.author: brblanch
ms.date: 11/30/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: plan
ms.custom: internal
ms.openlocfilehash: 905c1bff0dc31615c7b736f58beaf7aa51956054
ms.sourcegitcommit: b6f2b4f8db6c3b1157299ece1f044cff56895919
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2020
ms.locfileid: "97025607"
---
# <a name="moodle-manual-migration-steps"></a>手動による Moodle 移行の手順

この記事では、オンプレミスの Moodle アーカイブを Azure Database for MySQL にインポートし、Azure Moodle アプリケーションを構成する手順について説明します。

このプロセスを開始する前に、次の記事に記載されているすべての手順を確実に完了してください。

- [Moodle 移行のための準備を行う方法](migration-pre.md)
- [Moodle 移行のアーキテクチャとテンプレート](migration-arch.md)
- [仮想ネットワーク ゲートウェイを作成して VM に接続する](vpn-gateway.md)

Azure Resource Manager (ARM) テンプレートのデプロイが完了したら、[Azure portal](https://portal.azure.com/) にサインインし、テンプレートが作成されたリソース グループにアクセスして、作成されたすべてのインフラストラクチャ リソースを表示します。 作成されたリソースは、使用した ARM テンプレートにもよりますが、次の図のようになります。

![Moodle 移行リソース グループに作成されたインフラストラクチャ リソースを示すスクリーンショット。](images/resource-creation-overview.png)

## <a name="copy-the-moodle-archive"></a>Moodle アーカイブをコピーする

Moodle バックアップ アーカイブを Azure Blob Storage からコントローラー仮想マシン (VM) にコピーします。

### <a name="sign-in-to-the-controller-virtual-machine"></a>コントローラー仮想マシンにサインインする

1. 無料のオープンソースのターミナル エミュレーターまたはシリアル コンソール ツール ([PuTTY](https://www.putty.org/) など) を使用して、コントローラー仮想マシン (VM) にサインインします。

1. **[PuTTY Configuration]\(PuTTY の構成\)** で、コントローラー VM のパブリック IP アドレスを **ホスト名** として入力します。

1. 左側のナビゲーションで、 **[SSH]** を展開します。

   ![[PuTTY Configuration]\(PuTTY の構成\) ページのスクリーンショット。](images/putty-configuration.png)

1. **[Auth]\(認証\)** を選択し、ARM テンプレートを使用して Azure インフラストラクチャをデプロイしたときに使用した SSH キー ファイルを見つけます。

1. **[Open]** を選択します。 ユーザー名として「**azureadmin**」と入力します (これがテンプレートにハードコーディングされているため)。

   ![SSH 認証設定を示す PuTTY 構成ページのスクリーンショット。](images/putty-ssh-key.png)

PuTTY の詳細については、[PuTTY の一般的な FAQ/トラブルシューティングの質問](https://documentation.help/PuTTY/faq.html)に関するページをご覧ください。

### <a name="download-and-install-azcopy-on-the-controller-vm"></a>コントローラー VM に AzCopy をダウンロードしてインストールする

コントローラー VM にサインインしたら、次のコマンドを実行して AzCopy をインストールします。

  ```bash
  sudo -s
  wget https://aka.ms/downloadazcopy-v10-linux
  tar -xvf downloadazcopy-v10-linux
  sudo rm /usr/bin/azcopy
  sudo cp ./azcopy_linux_amd64_*/azcopy /usr/bin/
  ```

### <a name="copy-the-archive-to-the-controller-vm"></a>アーカイブをコントローラー VM にコピーする

1. 次のコマンドを実行して、圧縮された `storage.tar.gz` バックアップ ファイルを Azure Blob Storage からコントローラー VM の `/home/azureadmin/` ディレクトリにダウンロードします。

   ```bash
   sudo -s
   cd /home/azureadmin/
   azcopy copy 'https://<storageaccount>.blob.core.windows.net/container/BlobDirectoryName<SAStoken>' '/home/azureadmin/'
   ```

ご自身のストレージ アカウントと SAS トークンの値に置き換えます。 次に例を示します。

   `azcopy copy 'https://onpremisesstorage.blob.core.windows.net/migration/storage.tar.gz?sv=2019-12-12&ss=' /home/azureadmin/storage.tar.gz`

1. 圧縮されたファイルをディレクトリに抽出します。

   ```bash
   d /home/azureadmin
   ar -zxvf storage.tar.gz
   ```

### <a name="back-up-the-current-configuration"></a>現在の構成をバックアップする

移行する前に、現在の構成をバックアップします。 バックアップ ディレクトリは `home/azureadmin` で `storage` として抽出されます。 この `storage` ディレクトリには、`moodle`、`moodledata`、および構成ディレクトリと、データベース バックアップ ファイルが含まれています。これらを必要な場所にコピーします。

1. バックアップ ディレクトリを作成します。

   ```bash
   cd /home/azureadmin/
   mkdir -p backup
   mkdir -p backup/moodle
   mkdir -p backup/moodle/html
   ```

1. `moodle` ディレクトリと `moodledata` ディレクトリのバックアップを作成します。

   ```bash
   mv /moodle/html/moodle /home/azureadmin/backup/moodle/html/moodle
   mv /moodle/moodledata /home/azureadmin/backup/moodle/moodledata
   ```

1. `moodle` ディレクトリと `moodledata` ディレクトリを共有の場所 `/moodle` にコピーします。

   ```bash
   cp -rf /home/azureadmin/storage/moodle /moodle/html/
   cp -rf /home/azureadmin/storage/moodledata /moodle/moodledata
   ```

## <a name="import-the-moodle-database-to-azure"></a>Moodle データベースを Azure にインポートする

Azure Database for MySQL サーバーに接続し、オンプレミスの Moodle データベース アーカイブを Azure Database for MySQL にインポートします。

### <a name="connect-to-the-mysql-server"></a>MySQL サーバーに接続する

Azure Database for MySQL インスタンスは、ファイアウォールによって保護されます。 既定では、サーバーとサーバー内部のデータベースに対する接続はすべて拒否されます。 初めて Azure Database for MySQL に接続する場合は、その前に、コントローラー VM のパブリック IP アドレスまたは IP アドレス範囲のアクセスを許可するようにファイアウォールを構成します。

ファイアウォールを構成するには、Azure コマン ドライン (Azure CLI) または Azure portal を使用します。

次の Azure CLI コマンドを実行します。プレースホルダーはご自身の値に置き換えてください。

```azurecli
az mysql server firewall-rule create --resource-group <myresourcegroup> --server <mydemoserver> --name <AllowMyIP> --start-ip-address <192.168.0.1> --end-ip-address <192.168.0.1>
```

または、Azure portal 内で、デプロイされているご自身の Moodle インフラストラクチャ リソースから Azure Database for MySQL サーバーを選択します。 サーバー ページの左側のナビゲーションで、 **[接続のセキュリティ]** を選択します。

ここで、許可されている IP アドレスを追加し、ファイアウォール規則を構成できます。 規則を作成した後、 **[保存]** を選択します。

![Azure Database for MySQL サーバーの [接続セキュリティ] ウィンドウのスクリーンショット。](images/database-connection-security.png)

これで、[mysql](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) コマンド ライン ツールまたは [MySQL Workbench](https://dev.mysql.com/doc/workbench/en/) を使用して、MySQL サーバーに接続することができます。

![MySQL Workbench の [Setup New Connection]\(新しい接続の設定\) 画面のスクリーンショット。](images/database-connection.png)

接続情報を取得するには、Azure portal の MySQL サーバーの **[概要]** ページに移動します。 各フィールドの横のコピー アイコンを使用して、**サーバー名** と **サーバー管理者ログイン名** をコピーします。

たとえば、サーバー名は `mydemoserver.mysql.database.azure.com`、サーバー管理者ログイン名は `myadmin@mydemoserver` の場合があります。

また、パスワードも必要です。 パスワードをリセットする必要がある場合は、メニュー バーの **[パスワードのリセット]** を選択します。

これらのデータベース サーバーの詳細は、次のセクションで使用します。

### <a name="import-the-moodle-database-to-azure-database-for-mysql"></a>Moodle データベースを Azure Database for MySQL にインポートする

1. オンプレミス データベースをインポートする MySQL データベースを作成します。

   ```bash
   mysql -h $server_name -u $server_admin_login_name -p$admin_password -e "CREATE DATABASE $moodledbname CHARACTER SET utf8;"
   ```

1. 正しいアクセス許可をデータベースに割り当てます。

   ```bash
   mysql -h $server_name -u $server_admin_login_name -p$admin_password -e "GRANT ALL ON $moodledbname.* TO '$server_admin_login_name' IDENTIFIED BY '$admin_password';"
   ```

1. データベースをインポートします。

   ```bash
   mysql -h $server_name -u $server_admin_login_name -p$admin_password $moodledbname < /home/azureadmin/storage/database.sql
   ```

## <a name="update-configurations"></a>構成を更新する

オンプレミスの Moodle データベース アーカイブを Azure Database for MySQL にインポートしたら、必要に応じて、コントローラー VM 上で次の構成を更新します。

- Moodle 構成ファイルを更新する。
- ディレクトリのアクセス許可を構成する。
- PHP と nginx Web サーバーを構成する。
- DNS 名とその他の変数を更新する。
- 不足している PHP 拡張機能をインストールする。
- Web サーバーを再起動して停止する。
- 構成ファイルを仮想マシン スケール セットにコピーするために共有の場所にコピーする。

### <a name="update-the-moodle-config-file"></a>Moodle 構成ファイルを更新する

Moodle 構成ファイル `/moodle/config.php` 内のデータベースの詳細パラメーターを更新します。

このタスクの DNS 名を取得するには:

1. Azure portal 内で、デプロイされているご自身の Moodle インフラストラクチャ リソースから **ロード バランサーのパブリック IP アドレス** を選択します。

1. **[概要]** ページで、**DNS 名** の横のコピー アイコンを選択します。 `config.php` ファイルを更新するには:

1. 次のコマンドを入力して、`nano` エディターで `config.php` を編集します。

   ```bash
   cd /moodle/html/moodle/
   nano config.php
   ```

1. Azure portal からコピーした値を使用して、ファイル内のデータベースの詳細を更新します。

   ```php
   $CFG->dbhost    = 'localhost';                // Change 'localhost' to the server name.
   $CFG->dbname    = 'moodle';                   // Change 'moodle' to the newly created database name.
   $CFG->dbuser    = 'root';                     // Change 'root' to the server admin login name.
   $CFG->dbpass    = 'password';                 // Change 'password' to the server admin login password.
   $CFG->wwwroot   = 'https://on-premises.com';  // Change 'on-premises' to the DNS name.
   $CFG->dataroot  = '/var/moodledata';          // Change the path to '/moodle/moodledata'.
   ```

1. 変更後、Ctrl キーを押しながら O キーを押してファイルを保存し、Ctrl キーを押しながら X キーを押してエディターを終了します。

オンプレミスの `dataroot` ディレクトリを任意の場所に格納できます。

### <a name="configure-directory-permissions"></a>ディレクトリのアクセス許可を構成する

- 755 と www-data の所有者:グループのアクセス許可を `moodle` ディレクトリに割り当てます。

  ```bash
  sudo chmod 755 /moodle/html/moodle sudo chown -R www-data:www-data /moodle/html/moodle
  ```

- 770 と www-data の所有者:グループのアクセス許可を `moodledata` ディレクトリに割り当てます。

  ```bash
  sudo chmod 770 /moodle/moodledata sudo chown -R www-data:www-data /moodle/moodledata
  ```

### <a name="update-web-config-files"></a>Web 構成ファイルを更新する

nginx `conf` ファイルをバックアップして更新します。

```bash
sudo mv /etc/nginx/sites-enabled/*.conf /home/azureadmin/backup/
cd /home/azureadmin/storage/configuration/
sudo cp -rf nginx/sites-enabled/*.conf /etc/nginx/sites-enabled/
```

PHP `www.conf` ファイルをバックアップして更新します。

```bash
_PHPVER='/usr/bin/php -r "echo PHP_VERSION;" | /usr/bin/cut -c 1,2,3'
echo $_PHPVER
sudo mv /etc/php/$_PHPVER/fpm/pool.d/www.conf /home/azureadmin/backup/www.conf
sudo cp -rf /home/azureadmin/storage/configuration/php/$_PHPVER/fpm/pool.d/www.conf /etc/php/$_PHPVER/fpm/pool.d/
```

### <a name="update-nginx-configuration-variables"></a>nginx 構成の変数を更新する

Azure クラウドの DNS 名を、オンプレミスの Moodle アプリケーションの DNS 名に更新します。

1. nginx 構成ファイルを開きます。

   ```bash
   nano /etc/nginx/sites-enabled/*.conf
   ```

1. ARM テンプレートのデプロイによって、nginx サーバーがポート 81 に設定されます。 81 でない場合は、ファイル内の `SERVER_PORT` を 81 に更新します。

1. `server_name` を更新します。 たとえば、`server_name on-premises.com` の場合は、DNS 名を使用して `on-premises.com` を更新します。 ほとんどの場合、DNS 名は移行時に変更されません。

1. HTML `root` ディレクトリの場所を更新します。 たとえば、`root /var/www/html/moodle;` を `root /moodle/html/moodle;` に更新します。 オンプレミスのルート ディレクトリは、任意の場所に配置できます。

1. 変更後、Ctrl キーを押しながら O キーを押してファイルを保存し、Ctrl キーを押しながら X キーを押して終了します。

### <a name="install-any-missing-php-extensions"></a>不足している PHP 拡張機能をインストールする

ARM デプロイ テンプレートによって、PHP 拡張機能である fpm、cli、curl、zip、pear、mbstring、dev、mcrypt、soap、json、redis、bcmath、gd、mysql、xmlrpc、intl、xml、bz2 がインストールされます。 オンプレミスの Moodle アプリケーションに、コントローラー VM 上にない PHP 拡張機能がある場合は、手動でインストールできます。

オンプレミス アプリケーション内の PHP 拡張機能の一覧を取得するには、次を実行します。

```bash
php -m
```

不足している拡張機能をインストールするには、次を実行します。

```bash
sudo apt-get install -y php-<extension>
```

### <a name="restart-and-stop-the-web-servers"></a>Web サーバーを再起動して停止する

1. Web サーバーを再起動します。

   ```bash
   sudo systemctl restart nginx
   sudo systemctl restart php$_PHPVER-fpm
   ```

1. Web サーバーを停止します。

   ```bash
   sudo systemctl stop nginx
   sudo systemctl stop php$_PHPVER-fpm
   ```

要求が Azure Load Balancer に達すると、その要求は仮想マシン スケール セットのインスタンスにリダイレクトされます。コントローラー VM にはリダイレクトされません。

### <a name="copy-configuration-files"></a>構成ファイルをコピーする

PHP および Web サーバーの構成ファイルを、後で仮想マシン スケール セットのインスタンスにコピーするために、共有の場所にコピーします。

共有の場所に構成用ディレクトリを作成するには、次を実行します。

```bash
mkdir -p /moodle/config
mkdir -p /moodle/config/php
mkdir -p /moodle/config/nginx
```

PHP および Web サーバーの構成ファイルを共有ディレクトリにコピーするには、次を実行します。

```bash
cp /etc/nginx/sites-enabled/* /moodle/config/nginx
cp /etc/php/$_PHPVER/fpm/pool.d/www.conf /moodle/config/php
```

## <a name="next-steps"></a>次の手順

[Moodle コントローラー インスタンスとワーカー ノードの設定](azure-infra-config.md)に関するページに進み、Moodle 移行プロセスの次の手順を実行します。
