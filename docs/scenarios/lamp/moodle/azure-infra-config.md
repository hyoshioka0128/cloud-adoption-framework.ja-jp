---
title: Moodle コントローラー インスタンスとワーカー ノードを設定する方法
description: Moodle コントローラー インスタンスとワーカー ノードを設定する方法 (Azure インフラストラクチャ構成) について説明します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 11/06/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: plan
ms.openlocfilehash: 9a40bc5b34d85a94641e2381c7773621dfe09e9f
ms.sourcegitcommit: a7eb2f6c4465527cca2d479edbfc9d93d1e44bf1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2020
ms.locfileid: "94714954"
---
# <a name="how-to-set-up-moodle-controller-instance-and-worker-nodes"></a>Moodle コントローラー インスタンスとワーカー ノードを設定する方法

## <a name="virtual-machine-scale-set-instances"></a>仮想マシン スケール セットのインスタンス

仮想マシン スケール セットのインスタンスは、同じ仮想ネットワーク内にあるコントローラー仮想マシンでのみアクセスできるプライベート IP を使用して割り当てられます。 ゲートウェイで仮想マシン スケール セットのインスタンスをプライベート IP に接続できるようにし、「[仮想ネットワーク ゲートウェイを作成してプライベート IP 経由で接続する方法](./vpn-gateway.md)」に従って、仮想マシン スケール セットのインスタンスにゲートウェイを介してアクセスできるようにします。 仮想マシン スケール セットにアクセスする前に、パスワードが有効になるように設定します。

仮想マシン スケール セットのインスタンスのプライベート IP を作成するには、次のようにします。

- [Azure portal](https://ms.portal.azure.com/#home) にログインし、作成されたリソース グループを見つけます。
- 仮想マシン スケール セットを見つけて、それに移動します。
- 左側のパネルで **[インスタンス]** を選択します。
- 実行中のインスタンスに移動し、 **[概要]** セクションで、そのインスタンスに関連付けられているプライベート IP を見つけます。

仮想マシン スケール セットとコントローラー仮想マシンにログインします。こちらのコマンドを実行します。

```bash
 sudo -s
 sudo ssh azureadmin@172.31.X.X
```

172.31.X.X は、仮想マシン スケール セットのインスタンスのプライベート IP です。

仮想マシン スケール セットのインスタンスにログインします。 次の手順に従います。

- バックアップ ディレクトリは、/home/azureadmin で storage/ として抽出されます。 このストレージ ディレクトリには、Moodle、moodledata、構成ディレクトリ、さらにデータベース バックアップ ファイルが含まれています。 これらは必要な場所にコピーされます。

- バックアップ ディレクトリを作成します。

  ```bash
  cd /home/azureadmin/
  mkdir -p backup
  mkdir -p backup/moodle
  ```

- PHP と Web サーバーを構成するには、PHP と Web サーバーの構成のバックアップを作成し、PHP のバージョンを変数に設定します。

   ```bash
  _PHPVER=`/usr/bin/php -r "echo PHP_VERSION;" | /usr/bin/cut -c 1,2,3`
  echo $_PHPVER
   ```

  ```bash
  sudo mv /etc/nginx/sites-enabled/*.conf  /home/azureadmin/backup/
  sudo mv /etc/php/$_PHPVER/fpm/pool.d/www.conf /home/azureadmin/backup/www.conf  
  ```

- PHP と Web サーバーの構成ファイルをコピーします。

  ```bash
  sudo cp /moodle/config/nginx/*.conf  /etc/nginx/sites-enabled/
  sudo  cp /moodle/config/php/www.conf /etc/php/$_PHPVER/fpm/pool.d/
  ```

- 不足している PHP 拡張機能をインストールし、Azure Resource Manager テンプレートを使用して、次の PHP 拡張機能をインストールします: fpm、cli、curl、zip、pear、mbstring、dev、mcrypt、soap、json、redis、bcmath、gd、mysql、xmlrpc、intl、xml、および bz2。

- オンプレミスにインストールされている PHP 拡張機能の一覧を取得するには、オンプレミスの仮想マシンで次のコマンドを実行します。

  ```bash
  php -m
  ```

- コントローラー仮想マシンにない追加の PHP 拡張機能がオンプレミスにある場合は、これらを手動でインストールできます。

  ```bash
  sudo apt-get install -y php-<extensionName>
  ```

## <a name="next-steps"></a>次のステップ

Moodle 移行プロセスの次のステップについては、「[Moodle の移行後のフォローアップの方法](./migration-post.md)」に進んでください。
