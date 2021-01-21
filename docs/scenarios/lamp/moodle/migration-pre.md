---
title: Moodle 移行のための準備を行う方法
description: Moodle 移行のための準備を行う方法について説明します。 Moodle ファイルをバックアップし、移行に必要なリソースを作成する方法について説明します。
author: UmakanthOS
ms.author: brblanch
ms.date: 11/30/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: plan
ms.custom: think-tank
ms.openlocfilehash: c5d8319f1f181848e70d303c7315403a5b52f7c0
ms.sourcegitcommit: 54f01dd0eafa23c532e54c821954ba682357f686
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/13/2021
ms.locfileid: "98175204"
---
# <a name="how-to-prepare-for-a-moodle-migration"></a>Moodle 移行のための準備を行う方法

Moodle アプリケーションをオンプレミス環境から Azure に移行する前に、データをエクスポートする必要があります。 このガイドでは、エクスポート プロセスの手順について説明します。

## <a name="install-the-azure-cli"></a>Azure CLI のインストール

次の手順のようにして、オンプレミス環境で Azure CLI を設定します。

1. Azure タスクに使用できるホストで、次のコマンドを入力して Azure CLI をインストールします。

   ```bash
   curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
   ```

1. Azure CLI で、次のコマンドを入力して Azure アカウントにサインインします。

   ```bash
   az login -u <username> -p <password>
   ```

1. Azure CLI によってブラウザーのウィンドウまたはタブが開かれる場合は、Microsoft アカウントを使用して Azure にサインインします。 ブラウザーのウィンドウが開かれない場合は、[https://aka.ms/devicelogin](https://aka.ms/devicelogin) にアクセスし、ターミナルに表示されている認証コードを入力します。

## <a name="create-a-subscription"></a>サブスクリプションの作成

既に Azure サブスクリプションがある場合は、このステップをスキップできます。

Azure サブスクリプションがない場合は、[無料で作成する](https://azure.microsoft.com/free/)ことができます。 また、[従量課金制サブスクリプション](https://azure.microsoft.com/offers/ms-azr-0003p/)を設定するか、Azure portal でサブスクリプションを作成することもできます。

- Azure portal を使用してサブスクリプションを作成するには、[ **[サブスクリプション]**](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) を開き、 **[追加]** を選択して、必要な情報を入力します。

  ![Azure portal の [サブスクリプション] ページのスクリーンショット。](./images/azure-subscriptions-page.png)

- Azure CLI を使用してサブスクリプションを作成するには、次のコマンドを入力します。

  ```azurecli
  az account set --subscription '<subscription name>'
  ```

 次にコマンドの例を示します。

  `az account set --subscription 'ComputePM LibrarySub'`

## <a name="create-a-resource-group"></a>リソース グループを作成する

Azure サブスクリプションの設定が終わったら、Azure portal または Azure CLI を使用して、Azure にリソース グループを作成します。

- Azure portal を使用する場合は、次の手順のようにします。

  1. [ **[リソース グループ]**](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups) を開き、 **[追加]** を選択します。

  1. サブスクリプション名、リソース グループ名、リージョンを入力します。 利用可能なリージョンの一覧については、「[Azure でのデータ所在地](https://azure.microsoft.com/global-infrastructure/data-residency/)」を参照してください。 後の手順で使用できるように、入力したリソース グループの名前を記録しておきます。

  1. **[Review + create]\(レビュー + 作成\)** を選択します。

  ![Azure portal の [リソース グループの作成] ページのスクリーンショット。サブスクリプション、リソース グループ、リージョンの各ボックスと、[確認および作成] ボタンがある。](./images/resource-group.png)

- Azure CLI を使用してリソース グループを作成するには、次のコマンドを入力します。

  ```azurecli
  az group create -l <region> -n <resource group name> -s '<subscription name>'
  ```

  たとえば、次のように入力します。

  `az group create -l eastus -n manual_migration -s 'ComputePM LibrarySub'`

  `-l` パラメーターの値では、既定の場所を指定します。 前の手順で使用したものと同じ場所を使用します。 作成したリソース グループの名前を記録しておき、後の手順でその名前を使用します。

## <a name="create-a-storage-account"></a>ストレージ アカウントを作成する

次に、作成したリソース グループ内にストレージ アカウントを作成します。 このストレージ アカウントを使用して、オンプレミスの Moodle データをバックアップします。

Azure portal または Azure CLI のいずれかを使用して、ストレージ アカウントを作成できます。

- Azure portal を使用する場合は、次の手順のようにします。

  1. [ **[ストレージ アカウントの作成]** ](https://ms.portal.azure.com/#create/Microsoft.StorageAccount) に移動します。

  1. 次の情報を入力します。

     - お使いの Azure サブスクリプション名
     - 先ほど作成したリソース グループの名前
     - ストレージ アカウント名
     - 自分のリージョン

  1. **[アカウントの種類]** については、ドロップダウン リストから **[BlobStorage]** を選択します。

  1. **[レプリケーション]** については、ドロップダウン リストから **[読み取りアクセス geo 冗長ストレージ (RA-GRS)]** を選択します。

  1. **[Review + create]\(レビュー + 作成\)** を選択します。

  ![複数の入力ボックスと [確認および作成] ボタンがある、Azure portal の [ストレージ アカウントの作成] ページのスクリーンショット。](./images/create-storage-account.png)

- Azure CLI を使用してストレージ アカウントを作成するには、次のコマンドを入力します。

  ```azurecli
  az storage account create -n <storage account name> -g <resource group name> --sku <storage account SKU> --kind <storage account type> -l <region>
  ```

  次にコマンドの例を示します。

  `az storage account create -n onpremisesstorage -g manual_migration --sku Standard_LRS --kind BlobStorage -l eastus`

  `--kind` パラメーターでは、ストレージ アカウントの種類を指定します。

## <a name="back-up-on-premises-data"></a>オンプレミスのデータをバックアップする

オンプレミスの Moodle データをバックアップする前に、次の手順のようにして、Moodle の Web サイトで **メンテナンス モード** を有効にします。

1. オンプレミス環境の Moodle インスタンスから、次のコマンドを入力します。

   ```bash
   sudo /usr/bin/php admin/cli/maintenance.php --enable
   ```

2. Moodle Web サイトの状態を確認するには、次のコマンドを入力します。

   ```bash
   sudo /usr/bin/php admin/cli/maintenance.php
   ```

オンプレミスの Moodle と moodledata のファイル、構成、およびデータベースを 1 つのディレクトリにバックアップする必要があります。 次の図は、この推奨事項をまとめたものです。

![Moodle のバックアップ ストレージ ディレクトリの構造を示す図。](./images/directory-structure.png)

### <a name="create-a-storage-directory"></a>ストレージ ディレクトリを作成する

データをコピーする前に、コピー先の場所に空のストレージ ディレクトリを作成します。 たとえば、場所が `/home/azureadmin` である場合は、次のコマンドを入力します。

  ```bash
  sudo -s
  cd /home/azureadmin
  mkdir storage
  ```

### <a name="back-up-moodle-directories"></a>Moodle のディレクトリをバックアップする

オンプレミス環境の `moodle` ディレクトリには、Web サイトの HTML コンテンツが含まれています。 `moodledata` ディレクトリには、Moodle Web サイトのデータが含まれています。

次のコマンドを入力して、`moodle` および `moodledata` ディレクトリからストレージ ディレクトリにファイルをコピーします。

  ```bash
  cp -R /var/www/html/moodle /home/azureadmin/storage/
  cp -R /var/moodledata /home/azureadmin/storage/
  ```

### <a name="back-up-php-and-web-server-configurations"></a>PHP と Web サーバーの構成をバックアップする

構成ファイルをバックアップするには、次の手順のようにします。

1. 次のコマンドを入力して、ストレージ ディレクトリに新しいディレクトリを作成します。

   ```bash
   cd /home/azureadmin/storage
   mkdir configuration
   ```

2. 次のコマンドを入力して、PHP と NGINX の構成ファイルをコピーします。

   ```bash
   cp -R /etc/php /home/azureadmin/storage/configuration/
   cp -R /etc/nginx /home/azureadmin/storage/configuration/
   ```

   `php` ディレクトリには、`php-fpm.conf`、`php.ini`、`pool.d`、`conf.d` などの PHP の構成ファイルが格納されています。 `nginx` ディレクトリには、`nginx.conf` や `sites-enabled/dns.conf` などの ngnix の構成ファイルが格納されています。

### <a name="back-up-the-database"></a>データベースをバックアップする

データベースをバックアップするには、次の手順のようにします。

1. 次のコマンドを入力して、mysql-client がインストールされているかどうかを確認します。

   ```bash
   sudo -s
   mysql -V
   ```

2. mysql-client がインストールされている場合は、このステップをスキップします。 それ以外の場合は、次のコマンドを入力して mysql-client をインストールします。

   ```bash
   sudo apt-get install mysql-client
   ```

3. 次のコマンドを入力して、データベースをバックアップします。

   ```bash
   mysqldump -h <database server name> -u <database user ID> -p<database password> <database name> > /home/azureadmin/storage/database.sql
   ```

   `<database server name>`、`<database user ID>`、`<database password>`、`<database name>` については、オンプレミスのデータベースで使用されている値を使用します。

### <a name="create-an-archive"></a>アーカイブを作成する

次のコマンドを入力して、バックアップ ディレクトリにアーカイブ ファイル `storage.tar.gz` を作成します。

```bash
cd /home/azureadmin/ tar -zcvf storage.tar.gz storage
```

## <a name="download-and-install-azcopy"></a>AzCopy のダウンロードとインストール

次のコマンドを入力して、AzCopy をインストールします。

```bash
sudo -s
wget https://aka.ms/downloadazcopy-v10-linux
tar -xvf downloadazcopy-v10-linux
sudo rm /usr/bin/azcopy
sudo cp ./azcopy_linux_amd64_*/azcopy /usr/bin/
```

## <a name="copy-archived-files-to-azure-blob-storage"></a>アーカイブされたファイルを Azure Blob Storage にコピーする

以下の手順に従い、AzCopy を使用して、アーカイブされたオンプレミスのファイルを Azure Blob Storage にコピーします。

### <a name="generate-a-security-token"></a>セキュリティ トークンを生成する

AzCopy 用の Shared Access Signature (SAS) トークンを生成するには、次の手順のようにします。

1. Azure portal で、前に作成したストレージ アカウントのページに移動します。

1. 左側のパネルで、 **[Shared Access Signature]** を選択します。

   ![左側のパネルで Shared Access Signature が強調表示されている、Azure portal でのストレージ アカウントのページのスクリーンショット。](./images/new-storage-account-page.png)

1. **[使用できるリソースの種類]** で、 **[コンテナー]** を選択します。

1. **[開始日時と有効期限の日時]** で、SAS トークンの開始と終了の日時を入力します。

1. **[SAS と接続文字列を生成する]** を選択します。

   ![ストレージ アカウントの Shared Access Signature のページが表示されている Azure portal のスクリーンショット。](./images/shared-access-signature-page.png)

1. 後の手順で使用するため、SAS トークンのコピーを作成します。

### <a name="create-a-container"></a>コンテナーを作成する

ストレージ アカウントにコンテナーを作成します。 このステップでは、Azure CLI または Azure portal を使用できます。

- Azure CLI を使用する場合は、次のコマンドを入力します。

  ```bash
  az storage container create --account-name <storage account name> --name <container name> --auth-mode login
  ```

  次にコマンドの例を示します。

  `az storage container create --account-name onpremisesstorage --name migration --auth-mode login`

  `--auth-mode` パラメーターを使用して値 `login` を指定すると、Azure で認証にユーザーの資格情報が使用された後、コンテナーが作成されます。

- Azure portal を使用してコンテナーを作成する場合は、次の手順のようにします。

  1. ポータルで、前に作成したストレージ アカウントのページに移動します。

  1. **[コンテナー]** を選択して、 **[追加]** を選択します。

  1. コンテナーの名前を入力して、 **[作成]** を選択します。

     ![[名前] ボックスと [作成] ボタンがある、Azure portal の新規コンテナー作成ダイアログ ボックスのスクリーンショット。](./images/new-container.png)

### <a name="copy-the-archive-file-to-azure-blob-storage"></a>アーカイブ ファイルを Azure Blob Storage にコピーする

次のコマンドを入力して、Blob Storage に作成したコンテナーにアーカイブ ファイルをコピーします。

```bash
sudo azcopy copy /home/azureadmin/storage.tar.gz 'https://<storage account name>.blob.core.windows.net/<container name>/<SAS token>'
```

次にコマンドの例を示します。

`azcopy copy /home/azureadmin/storage.tar.gz 'https://onpremisesstorage.blob.core.windows.net/migration/?sv=2019-12-12&ss='`

これで、Blob Storage アカウントにアーカイブのコピーが含まれるようになります。

![Blob Storage アカウントを示す Azure portal のページのスクリーンショット。 ストレージ ディレクトリの圧縮された tar ファイルが表示されます。](./images/archive-in-blob-storage.png)

## <a name="next-steps"></a>次の手順

引き続き、「[Moodle 移行のアーキテクチャとテンプレート](./migration-arch.md)」を参照してください。
