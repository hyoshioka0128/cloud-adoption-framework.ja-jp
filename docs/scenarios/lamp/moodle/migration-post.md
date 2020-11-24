---
title: Moodle 移行後のフォローアップ方法
description: Moodle 移行後のフォローアップ方法について説明します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 11/06/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: plan
ms.openlocfilehash: 8594919772cfccf3dd02769a14d62f64cb1ad995
ms.sourcegitcommit: a7eb2f6c4465527cca2d479edbfc9d93d1e44bf1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2020
ms.locfileid: "94714906"
---
# <a name="how-to-follow-up-after-a-moodle-migration"></a>Moodle 移行後のフォローアップ方法

## <a name="post-migration-tasks"></a>移行後のタスク

移行後のタスクは最終的なアプリケーション構成で、ログ出力先、SSL 証明書、およびスケジュールされたタスク/cron ジョブの設定などがあります。 このタスクの範囲は次のとおりです。

- アプリケーションの構成。
- 仮想マシン スケール セット インスタンス内のログのパスの更新。
- 仮想マシン スケール セット インスタンスのサーバーの再起動。
- 証明書の更新。
- 証明書の場所の更新。
- HTML ローカル コピーの更新。
- PHP および nginx のサーバーの再起動。
- Azure Load Balancer IP への DNS 名のマッピング。

## <a name="controller-virtual-machine-scale-set"></a>コントローラー仮想マシン スケール セット

### <a name="log-paths"></a>ログのパス

オンプレミスには、Azure ログのパスで更新する必要がある様々なログのパスの場所が存在する場合があります。 たとえば、/var/log/syslogs/moodle/access.log や /var/log/syslogs/moodle/error.log があります。

- ログ ファイルの場所を更新します。 このコマンドによって、構成ファイルが開きます。

  ```bash
  nano /etc/nginx/nginx.conf
  ```

- ログのパスの場所を変更します。

- `access_log` と `error_log` を見つけて、ログのパスを更新します。

- `CTRL+O` を押して保存し、`CTRL+X` で終了します。

### <a name="restart-servers"></a>サーバーを再起動する

nginx サーバーと php-fpm サーバーを再起動します。

  ```bash
  sudo systemctl restart nginx
  sudo systemctl restart php<phpVersion>-fpm
  ```

## <a name="controller-virtual-machine"></a>コントローラー仮想マシン

### <a name="certificates"></a>証明書

- 証明書にアクセスするには、コントローラー仮想マシンにログインします。

- Moodle アプリケーションの証明書は /moodle/certs に存在します。

- .crt および .key の各ファイルを /moodle/certs/ にコピーします。 構成されている nginx サーバーで認識されるようにするには、ファイル名を nginx.crt および nginx.key に変更する必要があります。 ローカル環境によっては、ユーティリティ SCP や WinSCP などのツールを使用して、これらのファイルをコントローラー仮想マシンにコピーすることもできます。

- 証明書の名前を変更するコマンド。

  ```bash
  cd /path/to/certs/location
  mv /path/to/certs/location/*.key /moodle/certs/nginx.key
  mv /path/to/certs/location/*.crt /moodle/certs/nginx.crt
  ```

- 自己署名証明書を生成することもできます。これは、テストにのみ役立ちます。

  ```bash
  openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
  -keyout /moodle/certs/nginx.key \
   -out /moodle/certs/nginx.crt \
  -subj "/C=US/ST=WA/L=Redmond/O=IT/CN=mydomain.com"
  ```

- 証明書ファイルを所有者に対して読み取り専用にし、これらのファイルが `www-data:www-data` によって所有されるようにすることをお勧めします。

  ```bash
  chown www-data:www-data /moodle/certs/nginx.*
   chmod 400 /moodle/certs/nginx.*
  ```

- 証明書の場所を更新し、構成ファイルを開くには、次のようにします。

  ```bash
  nano /etc/nginx/sites-enabled/*.conf
  ```

- 証明書のパスの場所を変更するには、`ssl_certificate` を見つけます。 下のようにして証明書のパスを更新します。

```bash
   /moodle/certs/moodle/certs/nginx.crt;
   /moodle/certs/nginx.key;
```

- `CTRL+O` を押してファイルを保存し、`CTRL+X` で終了します。

### <a name="update-the-local-html-copy"></a>ローカル HTML コピーを更新する

Moodle html サイト (`/moodle/html/moodle`) のコンテンツのローカル コピーが、`/var/www/html/moodle` の仮想マシン スケール セット内に作成されます。 このローカル コピーは、タイムスタンプに更新がある場合にのみ更新されます。 コントローラー仮想マシンから次のコマンドを実行して、タイムスタンプを更新します。

  ```bash
  sudo -s
  /usr/local/bin/update_last_modified_time.moodle_on_azure.sh
  ```

- 最後に変更されたタイムスタンプ ファイル (`/moodle/html/moodle/last_modified_time.moodle_on_azure`) のスクリプトが実行されるたびに、`/moodle/html/moodle` ディレクトリの内容がローカル コピー (`/var/www/html`) 内で更新されます。

### <a name="restart-servers"></a>サーバーを再起動する

- `nginx` と `php-fpm` のサーバーを再起動します。

  ```bash
  sudo systemctl restart nginx
  sudo systemctl restart php<phpVersion>-fpm
  ```

### <a name="map-the-dns-name-to-the-azure-load-balancer-ip"></a>DNS 名を Azure Load Balancer IP にマップする

- Azure Load Balancer IP への DNS 名のマッピングは、ホスティング プロバイダー レベルで行う必要があります。

- Moodle Web サイトで **メンテナンス モード** を無効にします。

- コントローラー仮想マシンで、次のコマンドを実行します。

  ```bash
  sudo /usr/bin/php admin/cli/maintenance.php --disable
  ```

- Moodle サイトの状態を確認するには、次のコマンドを実行します。

  ```bash
  sudo /usr/bin/php admin/cli/maintenance.php
  ```

- DNS 名を入力すると、移行された Moodle Web ページが表示されます。

## <a name="frequently-asked-questions-and-troubleshooting"></a>よく寄せられる質問とトラブルシューティング

1. エラー:データベース接続が失敗しました:"_データベース接続が失敗しました_" または "_指定したデータベースに接続できませんでした_" などのエラーについては、次のようないくつかの原因と解決策が考えられます。

    - データベース サーバーがインストールされていないか、実行されていません。 このことを MySQL で確認するには、次のコマンドを入力してみてください。

      ```bash
      $telnet database_host_name 3306
      ```

    - MySQL サーバーのバージョン番号を含む、暗号のような応答が返されます。

    - 異なるポートで Moodle の 2 つのインスタンスを実行しようとしている場合は、 `$CFG->dbhost` 設定で (localhost ではなく) ホストの IP アドレスを使用します。たとえば、`$CFG->dbhost = 127.0.0.1:3308` のようにします。

    - Moodle データベースが作成されていないか、これにアクセスするための適切な特権を持つユーザーが割り当てられていません。

    - Moodle データベースの設定が正しくありません。 Moodle 構成ファイル `config.php` 内のデータベース名、データベース ユーザー、またはデータベース ユーザー パスワードが正しくありません。

    - MySQL のユーザー名またはパスワードにアポストロフィや英字以外の文字がないことを確認します。

2. エラー:"_500 - 内部サーバー エラー_" :このエラーには、次のような複数の原因が考えられます。 まず、Web サーバーのエラー ログを確認します。より詳しい説明があるはずです。 次に既知の可能性をいくつか示します。

    - `.htaccess` または `httpd.conf` ファイルに構文エラーがあります。 ディレクティブの書き込み方法は、使用しているファイルによって異なります。 次のコマンドを使用して、nginx ファイルの構成エラーをテストできます。

      `nginx -t`

    - Web サーバーはユーザー自身のユーザー名で実行され、すべてのファイルのアクセス許可レベルは最大の 755 です。 Moodle ディレクトリにこれが設定されていることをコントロール パネルで確認するか、シェルにアクセスできる場合はこちらのコマンドを使用します。

      ```bash
      chmod -R 755 moodle
      ```

3. エラー:_403:Forbidden_。

    このエラーは、PHP の memory_limit 値が PHP スクリプトに対して十分ではないことを意味します。 memory_limit 値は許可されているメモリ サイズで、上記の例では `64M` です (67108864 バイト/1024 = 65536 KB。 65536 KB / 1024 = 64 MB)。 このメッセージが表示されなくなるまで、PHP memory_limit の値を増やす必要があります。 これを行うには、次の 2 つの方法があります。

    方法 1:ホストされているインストールの場合は、これを行う方法をホストのサポートに問い合わせてください。 多くの場合、`.htaccess` ファイルが許可されます。 これに該当する場合は、次の行を `.htaccess` ファイルに追加するか、まだ存在しない場合は Moodle ディレクトリにこれを作成します。

      ```bash
      php_value memory_limit <value>M
      Example: php_value memory_limit 40M
      ```

    方法 2:シェルにアクセスできる独自のサーバーがある場合は、`php.ini` ファイルを編集します。 `phpinfo` の出力をチェックして、正しいものであることを確認します。

      ```bash
      memory_limit <value>M
      Example: memory_limit 40M
      ```

    `php.ini` への変更を有効にするには、Web サーバーを再起動する必要があることに注意してください。 別の方法として、コマンド 'memory_limit 0' を使用して `memory_limit` を無効にすることもできます。

4. ログインできない。ログイン画面から進まない。

    これは、次が表示される場合にも該当します。"_Your session has timed out. \(セッションがタイムアウトしました。\)Please log in again. \(ログインし直してください。\)_ " または "_A server error that affects your login session was detected. \(ログイン セッションに影響を与えるサーバー エラーが検出されました。\)Please log in again or restart your browser. \(もう一度ログインするか、ブラウザーを再起動してください。\)_ " 次に、考えられる原因と、これを解決するために実行できる対処法を示します。

    - まず、メインの管理者アカウント (もう 1 つの手動アカウント) にも問題があるか確認します。 ユーザーが外部の認証方法 (LDAP など) を使用している場合は、これが問題になる可能性があります。 原因を特定し、それが Moodle によるものであることを確認してから、先に進んでください。

    - ハード ディスクがいっぱいになっていないこと、サーバーが共有ホスティングを使用していること、およびディスク領域のクォータに達していないことを確認してください。 これに該当する場合、新しいセッションが作成されなくなり、誰もログインできなくなります。

    - `moodledata` 領域のアクセス許可を慎重に確認してください。 Web サーバーは、`sessions` サブディレクトリに書き込むことができる必要があります。

5. 致命的なエラー: " _$CFG->dataroot is not writable. \($CFG-> データルートは書き込み可能ではありません。\)The admin has to fix directory permissions! \(管理者はディレクトリのアクセス許可を修正する必要があります。\)Exiting. \(終了します。\)_ "

    - Moodle と moodledata のアクセス許可が www-data:www-data のみであることを確認します。 それ以外の場合は、グループと所有権のアクセス許可を変更します。 次のコマンドで、アクセス許可が更新されます。

      ```bash
      sudo chown -R /moodle/moodledata
      ```

6. Couldn't find a top-level course. \(トップレベル コースが見つからない。\)

    - Moodle をインストールしようとした直後にこれが表示される場合は、インストールが完了していない可能性があります。 インストールが完了する場合、終了する直前に、管理者プロファイルと、サイトを指定することが求められます。 ログでエラーを確認してから、データベースを削除して再度開始します。 Web ベースのインストーラーを使用していた場合は、コマンド ラインのものを使用してください。

7. ログイン リンクがログイン時に変更されない。 ログインしていますが、自由に移動できません。 `$CFG->wwwroot` 設定の URL が、サイトへのアクセスに実際に使用しているものとまったく同じであることを確認します。

8. ファイルのアップロード中のエラー:

    - ファイルのアップロード時に "_File not found \(ファイルが見つかりません\)_ ” というエラーが表示される場合は、スラッシュ引数が Web サーバーで有効になっていないことを示します。 これを有効にしてみてください。

    - Web サーバーがスラッシュ引数をサポートしていない場合は、[Administration]\(管理\) > [Site administration]\(サイト管理\) > [Server]\(サーバー\) > [HTTP] の **[Use slash arguments]\(スラッシュ引数を使用する\)** チェックボックスをオフにすると、Moodle でこれを無効にできます。

      > [!WARNING]
      > スラッシュ引数を無効にすると、SCORM パッケージが動作しなくなり、スラッシュ引数の警告が表示されます。

9. サイトが **メンテナンス モード** でスタックしている。 場合によっては、無効にしようとしたときに Moodle が **メンテナンス モード** でスタックし、"_This site is undergoing maintenance and is currently unavailable \(このサイトはメンテナンス中のため現在使用できません\)_ " というメッセージが表示されることがあります。 Moodle を **メンテナンス モード** にすると、サイト ファイルのディレクトリである `moodledata/maintenance.html` に `maintenance.html` ファイルが作成されます。 これを解決するには、次を実行します。

    - Web サーバー ユーザーが moodledata ディレクトリに対する書き込みアクセス許可を持っていることを確認します。
    - `maintenance.html` ファイルを手動で削除します。

10. ログの場所:

    - Syslog:
      - 他のユーザーがページにアクセスしている間に、エラーまたはアクセスのログが生成されます。
      - これらは場所 `/var/log/nginx/` でキャプチャされます。

    - Cron ログ:
      - Cron ジョブが実行され、インスタンス内のローカル コピーが更新されます。
      - パスは `/var/log/sitelogs/moodle/cron.log` です。
