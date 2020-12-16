---
title: Moodle 移行後のフォローアップ方法
description: Moodle 移行後のフォローアップ方法について説明します。 ログのパスを更新する方法、サーバーを再起動する方法、および移行を完了するために必要なその他の手順を実行する方法について説明します。
author: UmakanthOS
ms.author: brblanch
ms.date: 11/30/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: plan
ms.custom: internal
ms.openlocfilehash: 05ad1f31a2ac8e04abb7aa2942d0b564748f870b
ms.sourcegitcommit: b6f2b4f8db6c3b1157299ece1f044cff56895919
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2020
ms.locfileid: "97025709"
---
# <a name="how-to-follow-up-after-a-moodle-migration"></a>Moodle 移行後のフォローアップ方法

## <a name="post-migration-tasks"></a>移行後のタスク

Moodle に移行した後、移行後のタスクを実行して構成を完了する必要があります。 次のようなタスクがあります。

- 仮想マシン スケール セット インスタンスのログのパスの更新。
- 仮想マシン スケール セット インスタンスのサーバーの再起動。
- 証明書の更新。
- 証明書の場所の更新。
- HTML ローカル コピーの更新。
- PHP および nginx のサーバーの再起動。
- Azure Load Balancer の IP アドレスへの DNS 名のマッピング。

## <a name="controller-virtual-machine-scale-set"></a>コントローラー仮想マシン スケール セット

仮想マシン スケール セットの構成を完了するには、次の手順を実行します。

### <a name="update-log-paths"></a>ログのパスを更新する

オンプレミス環境と Azure で、ログ ファイルの格納場所が異なる場合があります。 たとえば、次のログのパスの更新が必要になる場合があります。

- `/var/log/syslogs/moodle/access.log`
- `/var/log/syslogs/moodle/error.log`

ログ ファイルの場所を更新するには、次の手順のようにします。

1. 次のコマンドを入力して、構成ファイルを開きます。

   ```bash
   nano /etc/nginx/nginx.conf
   ```

2. `access_log` と `error_log` を見つけて、ログのパスを更新します。

3. Ctrl + O キーを押して変更を保存し、Ctrl + X キーを押してファイルを閉じます。

### <a name="restart-servers"></a>サーバーを再起動する

次のコマンドを入力して、`nginx` および `php-fpm` サーバーを再起動します。

```bash
sudo systemctl restart nginx
sudo systemctl restart php<php version>-fpm
```

## <a name="controller-virtual-machine"></a>コントローラー仮想マシン

コントローラー仮想マシンの構成を完了するには、次の手順のようにします。

### <a name="update-security-certificates"></a>セキュリティ証明書を更新する

1. コントローラー仮想マシンにサインインします。 Moodle アプリケーションの証明書は、`/moodle/certs` フォルダーにあります。

1. `.crt` と `.key` ファイルを `/moodle/certs/` にコピーします。 構成された nginx サーバーによって認識されるように、ファイル名をそれぞれ `nginx.crt` と `nginx.key` に変更します。 ローカル環境で SCP ユーティリティまたは WinSCP などのツールがサポートされている場合は、これらのツールを使用して、これらのファイルをコントローラー仮想マシンにコピーできます。 それ以外の場合は、次のコマンドを使用します。

   ```bash
   cd /<path to certs location>
   mv /<path to certs location>/*.key /moodle/certs/nginx.key
   mv /<path to certs location>/*.crt /moodle/certs/nginx.crt
   ```

   ファイルをコピーする代わりに、次のコマンドを使用して自己署名証明書を生成します。

   ```bash
   openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
   -keyout /moodle/certs/nginx.key \
   -out /moodle/certs/nginx.crt \
   -subj "/C=US/ST=WA/L=Redmond/O=IT/CN=mydomain.com"
   ```

   自己署名証明書はテストにのみ使用できます。

1. 証明書ファイルは `www-data:www-data` で所有し、所有者には読み取り専用にすることをお勧めします。 それらの変更を行うには、次のコマンドを入力します。

   ```bash
   chown www-data:www-data /moodle/certs/nginx.*
   chmod 400 /moodle/certs/nginx.*
   ```

1. 次の手順のようにして、証明書の場所を更新します。

   1. 次のコマンドを入力して、構成ファイルを開きます。

      ```bash
      nano /etc/nginx/sites-enabled/*.conf
      ```

   1. ファイルで `ssl_certificate` を見つけます。

   1. 証明書のパスを次の値に置き換えます。

      ```bash
      /moodle/certs/moodle/certs/nginx.crt;
      /moodle/certs/nginx.key;
      ```

   1. Ctrl + O キーを押して変更を保存し、Ctrl + X キーを押してファイルを閉じます。

### <a name="update-the-local-html-copy"></a>ローカル HTML コピーを更新する

Moodle HTML サイト `/moodle/html/moodle` の内容のローカル コピーが、仮想マシン スケール セットのフォルダー `/var/www/html/moodle` に作成されます。 ローカル コピーは、タイムスタンプが変更された場合にのみ更新されます。 タイムスタンプを更新するには、コントローラー仮想マシンで次のコマンドを入力します。

```bash
sudo -s
/usr/local/bin/update_last_modified_time.moodle_on_azure.sh
```

最後に変更されたタイムスタンプ ファイル `/moodle/html/moodle/last_modified_time.moodle_on_azure` に、スクリプトが含まれています。 スクリプトを実行するたびに、`/moodle/html/moodle` ディレクトリの内容がローカル コピー `/var/www/html` で更新されます。

### <a name="restart-servers"></a>サーバーを再起動する

次のコマンドを入力して、`nginx` および `php-fpm` サーバーを再起動します。

```bash
sudo systemctl restart nginx
sudo systemctl restart php<php version>-fpm
```

### <a name="map-the-dns-name-to-the-azure-load-balancer-ip-address"></a>Azure Load Balancer の IP アドレスに DNS 名をマップする

ホスティング プロバイダー レベルで次の手順のようにして、DNS 名を Azure Load Balancer の IP にマップします。

1. コントローラー仮想マシンで次のコマンドを入力し、Moodle Web サイトでメンテナンス モードをオフにします。

   ```bash
   sudo /usr/bin/php admin/cli/maintenance.php --disable
   ```

1. 次のコマンドを入力して、Moodle サイトの状態を確認します。

   ```bash
   sudo /usr/bin/php admin/cli/maintenance.php
   ```

1. DNS 名に移動すると、移行された Moodle Web ページが表示されます。

## <a name="frequently-asked-questions-and-troubleshooting"></a>よく寄せられる質問とトラブルシューティング

Moodle の移行について不明な点がある場合は、以下の情報を参照してください。 問題のトラブルシューティングには、これらのログ ファイルも役に立ちます。

- Syslog ファイル:

  - ユーザーが Web ページに移動するたびに、システムによってエラー ログまたはアクセス ログが生成されます。
  - それらは次のフォルダーにあります: `/var/log/nginx/`。

- Cron ログ ファイル:
  - Cron ジョブを実行すると、ログ ファイルのローカル コピーが更新されます。
  - そのファイルは次のフォルダーにあります: `/var/log/sitelogs/moodle/cron.log`。

### <a name="database-connection-failure"></a>データベース接続エラー

"*データベース接続が失敗しました*" または "*指定したデータベースに接続できませんでした*" などのエラーについては、次のようないくつかの原因と解決策が考えられます。

- データベース サーバーがインストールされていないか、実行されていません。 MySQL でこの状況を確認するには、次のコマンドを入力します。

  ```bash
  $telnet database_host_name 3306
  ```

  データベースが実行されている場合は、MySQL サーバーのバージョン番号を含む応答が返されます。

- ホスト アドレスが正しく構成されていません。 異なるポートで Moodle の 2 つのインスタンスを実行している場合は、`localhost` ではなくホストの IP アドレスを、`$CFG->dbhost` の設定で使用します。 たとえば、次のようなサービスを使います。

  `$CFG->dbhost = 127.0.0.1:3308`

- Moodle データベースが作成されていません。 または、データベースにアクセスするための適切なアクセス許可が割り当てられていません。 データベースと、付与したアクセス許可を確認します。

- Moodle データベースの設定が正しくありません。 たとえば、Moodle 構成ファイル `config.php` のデータベース名、ユーザー名、またはパスワードが正しくありません。 MySQL のユーザー名とパスワードに、アポストロフィまたは英数字以外の文字が含まれていないことを確認します。

### <a name="internal-server-error"></a>内部サーバー エラー

次のエラーには、複数の原因が考えられます: "*500: 内部サーバー エラー*"。 まず、Web サーバーのエラー ログを確認します。詳細な説明が含まれているはずです。 次のような可能性があります。

- `.htaccess` または `httpd.conf` ファイルに構文エラーがあります。 ディレクティブの正しい構文は、使用しているファイルによって異なります。 次のコマンドを使用して、nginx ファイルの構成エラーをテストします。

  ```bash
  `nginx -t`
  ```

- Web サーバーがユーザー自身のユーザー名で実行されていて、アクセス許可が正しくありません。 この場合、すべてのファイルに最大のアクセス許可レベル 755 が必要です。 コントロール パネルで、Moodle ディレクトリにこのレベルが設定されていることを確認します。 または、シェルにアクセスできる場合は、次のコマンドを使用してレベルを設定します。

  ```bash
  chmod -R 755 moodle
  ```

### <a name="memory-limit-error"></a>メモリ制限エラー

"*403: 許可されていません*" エラーが発生するときは、PHP の `memory_limit` の値が PHP スクリプトに十分な大きさではありません。 `memory_limit` の値は、許可されているメモリ サイズです。 メッセージが表示されなくなるまで、PHP の `memory_limit` の値を少しずつ増やします。 次のいずれかの方法を使います。

- ホストされたインストールの場合は、値を増やす方法をホストのサポートに問い合わせます。 多くの環境では、`.htaccess` ファイルが使用されます。 お使いのインストールがそうである場合は、`.htaccess` ファイルに次の行を追加します。

  ```bash
  php_value memory_limit <value>M
  ```

  たとえば、値を 40 メガバイトに増やすには、次のように入力します。

  `php_value memory_limit 40M`

  `.htaccess` ファイルが存在しない場合は、その行を含むものを Moodle ディレクトリに作成します。

- シェルにアクセスできる独自のサーバーがある場合は、`php.ini` ファイルを編集します。 その後、Web サーバーを再起動し、`php.ini` で行った変更を適用します。 値が正しく更新されたことを確認するには、次のコマンドを入力します。

  ```bash
  `phpinfo`
  ```

  `phpinfo` からの出力に、次のような行が含まれている必要があります。

  ```bash
  memory_limit <value>M
  ```

  たとえば、次のような行が含まれる場合があります。

  `memory_limit 40M`

`memory_limit` の値を増やす以外の方法としては、次のコマンドを入力してメモリ制限を無効にします。

  ```bash
memory_limit 0
```

### <a name="sign-in-errors"></a>サインイン エラー

サインインできない場合、または次のいずれかのメッセージが表示される場合があります。

- "*セッションがタイムアウトしました。Please log in again. \(ログインし直してください。\)* "

- "*A server error that affects your login session was detected. (ログイン セッションに影響を与えるサーバー エラーが検出されました。) Please log in again or restart your browser. \(もう一度ログインするか、ブラウザーを再起動してください。\)* "

認証方法に問題がある可能性があります (特に、LDAP などの外部の方法を使用してユーザーを認証している場合)。 メインの管理者アカウントなど、別の手動アカウントにサインインしてみてください。 サインインできない場合は、認証を確認します。 他のアカウントにはサインインできる場合は、Moodle サインインの問題の考えられる原因と解決策を次に示します。

- ハード ディスクに空きがない可能性があります。 この場合、Moodle で新しいセッションを作成できず、ユーザーはサインインできません。 ハード ディスクがいっぱいになっていないこと、サーバーが共有ホスティングを使用していること、およびディスク領域のクォータに達していないことを確認してください。

- Web サーバーが `sessions` サブディレクトリに書き込むことができません。 `moodledata` 領域のアクセス許可を慎重に確認してください。

### <a name="fatal-errors"></a>致命的なエラー

次のエラーが表示される場合は、Moodle と moodledata のアクセス許可が正しくない可能性があります。"*Fatal error: $CFG->dataroot is not writable. (致命的なエラー: $CFG-> データルートは書き込み可能ではありません。) The admin has to fix directory permissions! \(管理者はディレクトリのアクセス許可を修正する必要があります。\)Exiting. \(終了します。\)* "

これらのアクセス許可が `www-data:www-data` のみであることを確認します。 アクセス許可のレベルが異なる場合は、次のコマンドを使用して、グループと所有権のアクセス許可を変更します。

```bash
sudo chown -R /moodle/moodledata
```

### <a name="top-level-course-errors"></a>トップレベル コースのエラー

Moodle をインストールした直後にトップレベルのコースが見つからない場合は、インストールが完了しなかった可能性があります。 インストールが完了する直前に、Moodle によって管理者プロファイルが要求され、サイトの名前の指定を求められます。 これらの手順がなかった場合は、ログでエラーを確認します。 その後、データベースを再起動します。 Web ベースのインストーラーを使用した場合は、コマンド ラインを使用して Moodle をもう一度インストールします。

### <a name="navigation-errors"></a>ナビゲーション エラー

サインインした後で Moodle 内を自由に移動できない場合は、URL の構成が正しくない可能性があります。 `$CFG->wwwroot` の設定の URL が、サイトへのアクセスに使用したものと同じであることを確認します。

### <a name="file-upload-errors"></a>ファイルのアップロード エラー

ファイルをアップロードするときに "*ファイルが見つからない*" というエラーが発生する場合は、Web サーバーでスラッシュ引数が有効になっていない可能性があります。

- お使いの Web サーバーでスラッシュ引数がサポートされている場合は、それをオンにします。

- Web サーバーでスラッシュ引数がサポートされていない場合は、 **[Administration]\(管理\)**  >  **[Site administration]\(サイト管理\)**  >  **[Server]\(サーバー\)**  >  **[HTTP]** で **[Use slash arguments]\(スラッシュ引数を使用する\)** チェック ボックスをオフにして、Moodle でそれを無効にします。 このメッセージが表示される場合があります。

  > [!WARNING]
  > スラッシュ引数を無効にすると、SCORM パッケージが動作しなくなり、スラッシュ引数の警告が表示されます。

### <a name="maintenance-mode-errors"></a>メンテナンス モード エラー

Moodle がメンテナンス モードになっていて、そのモードを終了しようとすると、次のメッセージが表示されることがあります。*This site is undergoing maintenance and is currently unavailable* (このサイトはメンテナンス中のため現在使用できません)。 この状況は、Moodle がメンテナンス モードになるときに `moodledata` フォルダーに作成される `maintenance.html` ファイルに問題がある場合に発生します。 この場合は、次の手順のようにします。

- Web サーバー ユーザーが `moodledata` ディレクトリへの書き込みアクセス許可を持っていることを確認します。
- `maintenance.html` ファイルを手動で削除します。

## <a name="next-steps"></a>次のステップ

- [Azure Database for MySQL のドキュメント](/azure/mysql/)
- [仮想マシン スケール セットとは](/azure/virtual-machine-scale-sets/overview)
- [ストレージ アカウントの概要](/azure/storage/common/storage-account-overview)
