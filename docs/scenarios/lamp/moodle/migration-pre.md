---
title: Moodle 移行のための準備を行う方法
description: Moodle 移行のための準備を行う方法について説明します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 11/06/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: plan
ms.openlocfilehash: 71990470e04f68f78b0bfffc2b1d837c7f0f29ad
ms.sourcegitcommit: a7eb2f6c4465527cca2d479edbfc9d93d1e44bf1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2020
ms.locfileid: "94714946"
---
# <a name="how-to-prepare-for-a-moodle-migration"></a>Moodle 移行のための準備を行う方法

## <a name="pre-migration-tasks"></a>移行前のタスク

オンプレミスから Azure にデータをエクスポートするには、次のタスクを実行する必要があります。

- Azure CLI をインストールします。
- サブスクリプションの作成。
- リソース グループを作成します。
- ストレージ アカウントを作成します。
- オンプレミスのデータをバックアップします。
- AzCopy をダウンロードしてインストールします。
- アーカイブされたファイルを Azure Blob にコピーします。

## <a name="install-the-azure-cli"></a>Azure CLI のインストール

- Azure 関連のすべてのタスクのために、オンプレミスのインフラストラクチャ内のホストに Azure CLI をインストールします。

  ```bash
   curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
  ```

- Azure アカウントにログインします。

  ```bash
  az login
  ```

- az login コマンド:Azure CLI によって、既定の Web ブラウザー内でインスタンスまたはタブが起動され、Microsoft アカウントを使用して Azure にログインするように求められる可能性があります。 ブラウザーが起動されない場合は、[https://aka.ms/devicelogin](https://aka.ms/devicelogin) で新しいページを開き、お使いの端末に表示されている認証コードを入力します。

- コマンド ラインを使用するには、次のコマンドを入力します。

  ```bash
  az login -u <username> -p <password>
  ```

## <a name="create-a-subscription"></a>サブスクリプションの作成

サブスクリプションを持っている場合は、この手順をスキップします。 サブスクリプションを持っていない場合は、[Azure portal 内で作成する](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)か、[従量課金制](https://azure.microsoft.com/offers/ms-azr-0003p/) サブスクリプションを選択することができます。

- Azure portal を使用してサブスクリプションを作成するには、 **[ホーム]** セクションの **[サブスクリプション]** に移動します。

  ![Azure サブスクリプション。](./images/subscriptions.png)

- このコマンドにより、サブスクリプションが設定されます。

  ```bash
  az account set --subscription "Subscription Name"

  Example: az account set --subscription "ComputePM LibrarySub"
  ```

## <a name="create-a-resource-group"></a>リソース グループを作成する

サブスクリプションがセットアップされたら、リソース グループを作成する必要があります。 1 つの選択肢として、Azure portal を使用して作成する方法があります。 **[ホーム]** セクションに移動し、 **[リソース グループ]** を検索して選択し、必須フィールドに入力して、 **[作成]** を選択します。

![リソース グループ:リソース グループを作成します。](./images/resource-group.png)

また、Azure CLI を使用して、リソース グループを作成することもできます。

- 前の手順と同じ既定の場所を指定します。

  ```bash
  az group create -l location -n name -s Subscription_NAME_OR_ID
  ```

- サンプル テスト アカウントを使用して、スクリーンショットとサブスクリプション名を更新します。

  例: `az group create -l eastus -n manual_migration -s ComputePM LibrarySub`

- 前の手順では、リソース グループが "manual_migration" として作成されました。 追加の手順でも、同じリソース グループを使用します。

詳細については、[Azure での場所](https://azure.microsoft.com/global-infrastructure/data-residency/)に関するページを参照してください。

## <a name="create-a-storage-account"></a>ストレージ アカウントの作成

次の手順では、作成したリソース グループ内に[ストレージ アカウントを作成します](https://ms.portal.azure.com/#create/Microsoft.StorageAccount)。 ストレージ アカウントは、Azure portal または Azure CLI を使用して作成できます。

- portal で作成するには、そちらに移動し、ストレージ アカウントを検索して、 **[追加]** ボタンを選択します。 必須フィールドに入力したら、 **[作成]** を選択します。

  ![ストレージ アカウントの作成。](./images/create-storage-account.png)

- 別の方法として、Azure CLI を使用することもできます。

  ```bash
  az storage account create -n storageAccountName -g resourceGroupName --sku Standard_LRS --kind BlobStorage -l location
  ```

  例: `az storage account create -n onpremisesstorage -g manual_migration --sku Standard_LRS --kind BlobStorage -l eastus`

- 前のコマンドの `--kind` は、ストレージ アカウントの種類を示しています。 `onpremisesstorage` アカウントが作成されると、それがオンプレミスのバックアップ先になります。

## <a name="back-up-on-premises-data"></a>オンプレミスのデータをバックアップする

- オンプレミスのデータをバックアップする前に、Moodle サイトの **メンテナンス モード** を有効にします。 オンプレミスの仮想マシンから次のコマンドを実行します。

  ```bash
  sudo /usr/bin/php admin/cli/maintenance.php --enable
  ```

- Moodle サイトの状態を確認するには、次のコマンドを実行します。

  ```bash
  sudo /usr/bin/php admin/cli/maintenance.php
  ```

- オンプレミスの Moodle と moodledata のファイル、構成、およびデータベースをバックアップする場合は、1 つのディレクトリにバックアップします。 次の図に、この概要を示します。

  ![Moodle バックアップ ディレクトリ構造。](./images/directory-structure.png)

- すべてのデータをコピーするには、目的の場所に空のストレージ ディレクトリを作成します。

  ```bash
  sudo -s
  ```

  たとえば、場所は `/home/azureadmin` です。

  ```bash
  cd /home/azureadmin
  mkdir storage
  ```

### <a name="back-up-moodle-and-moodledata"></a>Moodle と moodledata をバックアップする

- Moodle ディレクトリは、サイトの HTML コンテンツで構成されています。 Moodledata にはMoodle サイト データが含まれています。

- Moodle と moodledata をコピーするコマンドは次のとおりです。

  ```bash
  cp -R /var/www/html/moodle /home/azureadmin/storage/
  cp -R /var/moodledata /home/azureadmin/storage/
  ```

### <a name="backup-php-and-web-server-configurations"></a>PHP と Web サーバーの構成をバックアップする

- `php-fpm.conf`、`php.ini`、`pool.d`、`conf.d` ディレクトリなどの PHP 構成ファイルを `configuration` ディレクトリの下の `phpconfig` ディレクトリにコピーします。

- `nginx.conf` や `sites-enabled/dns.conf` などの ngnix 構成を `configuration` ディレクトリの下の `nginxconfig` ディレクトリにコピーします。

  ```bash
  cd /home/azureadmin/storage mkdir configuration
  ```

- nginx と PHP の構成をコピーするコマンドは次のとおりです。

  ```bash
  cp -R /etc/nginx /home/azureadmin/storage/configuration/nginx
  cp -R /etc/php /home/azureadmin/storage/configuration/php
  ```

### <a name="create-a-backup-of-the-database"></a>データベースのバックアップを作成する

- mysql-client が既にインストールされている場合は、これをインストールする手順をスキップします。 mysql-client がインストールされていない場合は、ここでインストールします。

  ```bash
  sudo -s
  ```

- mysql-client がインストールされているかどうかを確認するには、次のコマンドを実行します。

  ```bash
  mysql -V
  ```

- mysql-client がインストールされていない場合は、次のコマンドを実行します。

  ```bash
  sudo apt-get install mysql-client
  ```

- このコマンドでは、データベースをバックアップできます。

  ```bash
  mysqldump -h dbServerName -u dbUserId -pdbPassword dbName > /home/azureadmin/storage/database.sql
  ```

- dbServerName、dbUserId、dbPassword、dbName をオンプレミス データベースの詳細に置き換えます。

- バックアップ ディレクトリのアーカイブ `storage.tar.gz` ファイルを作成します。

  ```bash
  cd /home/azureadmin/ tar -zcvf storage.tar.gz storage
  ```

## <a name="download-and-install-azcopy"></a>AzCopy のダウンロードとインストール

次のコマンドを実行して AzCopy をインストールします。

  ```bash
  sudo -s
  wget https://aka.ms/downloadazcopy-v10-linux
  tar -xvf downloadazcopy-v10-linux
  sudo rm /usr/bin/azcopy
  sudo cp ./azcopy_linux_amd64_*/azcopy /usr/bin/
  ```

## <a name="copy-archived-files-to-azure-blob"></a>アーカイブされたファイルを Azure Blob にコピーする

AzCopy を使用して、アーカイブされたオンプレミス ファイルを Azure Blob にコピーします。

- AzCopy を使用するには、最初に SAS トークンを生成します。 作成した **ストレージ アカウント リソース** に移動し、左側のパネルの **[Shared access signature]** に移動します。

  ![ストレージ アカウントのサンプル。](./images/storage-account-created.png)

- **[コンテナー]** とチェックボックスをオンにし、SAS トークンの開始日と有効期限を設定します。 **[SAS と接続文字列を生成する]** を選択します。

  ![アクセス トークンの生成。](images/SAS-token-generation.png)

- 将来使用するために SAS トークンをコピーして保存します。

- ストレージ アカウントにコンテナーを作成するためのコマンド:

  ```bash
  az storage container create --account-name <storageAccountName> --name <containerName> --auth-mode login
  ```

  例: `az storage container create --account-name onpremisesstorage --name migration --auth-mode login`

  `--auth-mode login` は、ログイン時の認証モードを意味します。 ログインすると、コンテナーが作成されます。

- Azure portal を使用してコンテナーを作成することもできます。 作成された同じストレージ アカウントを選択し、コンテナーを選択し、 **[追加]** ボタンを選択します。

- 必須のコンテナー名を入力したら、 **[作成]** ボタンを選択します。

  ![新しいコンテナー。](images/new-container.png)

- アーカイブ ファイルを Azure Blob アカウントにコピーするためのコマンド:

  ```bash
  sudo azcopy copy /home/azureadmin/storage.tar.gz 'https://<storageAccountName>.blob.core.windows.net/<containerName>/<SAStoken>'
  ```

  例: `azcopy copy /home/azureadmin/storage.tar.gz 'https://onpremisesstorage.blob.core.windows.net/migration/?sv=2019-12-12&ss='`

  ![Azure Blob でのアーカイブ。](images/archive-in-blob.png)

- これで、Azure Blob アカウント内にアーカイブのコピーがあるはずです。

## <a name="next-steps"></a>次のステップ

Moodle 移行プロセスの詳細については、「[Moodle の移行タスク、アーキテクチャ、テンプレート](./migration-arch.md)」に進みます。
