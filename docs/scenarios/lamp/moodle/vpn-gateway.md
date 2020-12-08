---
title: 仮想ネットワーク ゲートウェイを作成して VM に接続する
description: 仮想ネットワーク ゲートウェイ、証明書、VPN を作成し、プライベート IP アドレスとパスワードを使用して SSH で仮想マシン スケール セット インスタンスに接続します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 11/30/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: plan
ms.openlocfilehash: db82bc164af0cc6b34235d307bbcb36db2f53939
ms.sourcegitcommit: 18f3ee8fcd8838f649cb25de1387b516aa23a5a0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/30/2020
ms.locfileid: "96327918"
---
# <a name="create-a-virtual-network-gateway-and-connect-to-vms"></a>仮想ネットワーク ゲートウェイを作成して VM に接続する

Azure Moodle リソースをデプロイした後は、プライベート IP アドレスを使用して Moodle 仮想マシン スケール セット インスタンスに接続できるように、Azure 仮想ネットワーク ゲートウェイを作成します。

## <a name="create-a-virtual-network-gateway"></a>仮想ネットワーク ゲートウェイの作成

仮想ネットワーク ゲートウェイは、Azure portal または Azure コマンド ライン インターフェイス (Azure CLI) を使用して作成できます。

[Azure ポータル](https://portal.azure.com)で次の操作を行います。

1. **[仮想ネットワーク ゲートウェイ]** を見つけて選択します。
   
1. **[仮想ネットワーク ゲートウェイの作成]** を選択します。
   
1. **[仮想ネットワーク ゲートウェイの作成]** ページで、次のフィールドに入力します。
   - **サブスクリプション** を選択します。
   - ゲートウェイの **[名前]** を入力します。
   - Moodle Azure Resource Manager (ARM) テンプレートによってデプロイされた **[仮想ネットワーク]** を選択します。
   - **[パブリック IP アドレス名]** を入力します。
   
1. それ以外のフィールドは、既定の入力値をそのまま使用します。
   
1. **[確認および作成]** を選択し、検証で問題がなければ、 **[作成]** を選択します。

![Azure portal の [仮想ネットワーク ゲートウェイの作成] 画面を示すスクリーンショット。](images/vpn-gateway.png)

または、次の Azure CLI コマンドを実行して、ゲートウェイを作成します。

```azurecli
az network vnet-gateway create -g <moodle resource group> -n <new virtual network gateway name> --public-ip-address <new gateway public ip address name> --vnet <moodle virtual network> --gateway-type Vpn --sku VpnGw1 --vpn-type RouteBased --no-wait
```

## <a name="generate-certificates"></a>証明書の生成

ルートと子の証明書を生成するには、Windows PowerShell ISE を使用します。

- ルート証明書を生成するには、次のコマンドを実行します。

  ```powershell
  $cert = New-SelfSignedCertificate -Type Custom -KeySpec Signature `
  -Subject "CN=P2SRootCert" -KeyExportPolicy Exportable `
  -HashAlgorithm sha256 -KeyLength 2048 `
  -CertStoreLocation "Cert:\CurrentUser\My" -KeyUsageProperty Sign -KeyUsage CertSign
  ```

- 子証明書を生成するには、次のコマンドを実行します。

  ```powershell
  New-SelfSignedCertificate -Type Custom -DnsName P2SChildCert -KeySpec Signature `
  -Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
  -HashAlgorithm sha256 -KeyLength 2048 `
  -CertStoreLocation "Cert:\CurrentUser\My" `
  -Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")
  ```

## <a name="export-the-certificates"></a>証明書をエクスポートする

システムに証明書をインストールするには、それをエクスポートします。

1. Windows の [スタート] メニューから **[ファイル名を指定して実行]** を選択し、「**mmc**」と入力します。
   
1. Microsoft 管理コンソールの左側のナビゲーション ペインで、 **[個人用]** フォルダーの下にある **[証明書]** を選択します。
   
**P2SRootCert** および **P2SChildCert** 証明書を検索します。

ルート証明書をエクスポートするには:

1. **P2SRootCert** を右クリックするか長押しし、 **[すべてのタスク]** をポイントして **[エクスポート]** を選択します。
1. **証明書のエクスポート ウィザード** で **[次へ]** を選択します。
1. **[いいえ、秘密キーをエクスポートしません]** を選択して、 **[次へ]** を選択します。
1. **[Base 64 encoded X.509 (.cer)]** を選択して、 **[次へ]** を選択します。
1. ファイル名を入力して、 **[次へ]** を選択します。
1. **[完了]** を選択します。
1. "**エクスポートに成功しました**" というメッセージが表示されます。 **[OK]** を選択します。

子証明書をエクスポートするには:

1. **P2SChildCert** を右クリックするか長押しし、 **[すべてのタスク]** をポイントして **[エクスポート]** を選択します。
1. **証明書のエクスポート ウィザード** で **[次へ]** を選択します。
1. **[はい、秘密キーをエクスポートします]** を選択し、 **[次へ]** を選択します。
1. **[Personal information exchange - PKCS #12 (.PFX)]** を選択して、 **[次へ]** を選択します。
1. **[パスワード]** チェック ボックスをオンにし、パスワードを入力して確認します。
1. **[暗号化]** で **[TripleDES-SHA1]** を選択して、 **[次へ]** を選択します。
1. ファイル名を入力して、 **[次へ]** を選択します。
1. **[完了]** を選択します。
1. "**エクスポートに成功しました**" というメッセージが表示されます。 **[OK]** を選択します。

## <a name="configure-the-virtual-network-gateway"></a>仮想ネットワーク ゲートウェイを構成する

1. エクスポートしたルート証明書ファイルをテキスト エディターで開き、内容をコピーします。
1. Azure portal で仮想ネットワーク ゲートウェイに移動します。
1. 左側のナビゲーションで、 **[ポイント対サイト構成]** を選択します。
1. **[今すぐ構成]** を選択します。
1. **[アドレス プール]** に、GatewaySubnet アドレスプールを入力します (例: `192.168.xx.0/24`)。
1. **[トンネルの種類]** で、 **[IKEv2]** を選択します。
1. **[認証の種類]** で **[Azure certification]\(Azure 認定\)** を選択します。
1. **[ルート証明書]** で次のようにします。
   - **[名前]** に「**Root**」と入力します。
   - コピーしたルート証明書のコードを **[公開証明書データ]** に貼り付けます。
1. **[保存]** を選択します。

## <a name="download-and-connect-through-the-vpn-client"></a>VPN クライアントをダウンロードし、それを使用して接続する

VPN ゲートウェイ接続を確立するには:

1. 仮想プライベート ネットワークの構成を保存した後、メニュー バーで **[VPN クライアントのダウンロード]** を選択します。
1. ダウンロードした VPN クライアントの zip ファイルの内容を抽出し、`WindowsAMD64` フォルダーを開き、`VpnClientSetupAmd64.exe` ファイルを実行して VPN クライアントをインストールします。
1. Windows で **[コントロール パネル]**  >  **[ネットワークとインターネット]**  >  **[ネットワーク接続]** に移動し、インストールした VPN を表示します。
1. VPN を右クリックし、 **[接続]** を選択します。
1. **[VPN]** ウィンドウで、 **[接続]** を選択します。

## <a name="configure-ssh-password-authentication"></a>SSH パスワード認証を構成する

コントローラー仮想マシン (VM) からパスワード認証を構成するには:

1. 編集のために `sshd` 構成ファイルを開きます。
   
   ```bash
   sudo vi /etc/ssh/sshd_config
   ```
   
1. 次のパラメーターを更新します。
   
   - `PasswordAuthentication` を `no` から `yes` に変更します。
   - コメント化された `UseLogin` を見つけて、`#` を削除し、値を `yes` に変更します。
   
1. Esc キーを押して「`:wq!`」と入力し、変更を保存します。
   
1. 次のコマンドを実行して `sshd` を再起動します。
   
   ```bash
   sudo systemctl restart sshd
   ```
   
1. 次のコマンドを実行してパスワードを設定します。
   
   ```bash
   sudo passwd <username>
   ```
   
   たとえば、コマンド `sudo passwd azureadmin` を実行すると、ユーザー `azureadmin` のパスワードが設定されます。
   
1. プロンプトで、パスワードを入力して再入力します。

## <a name="sign-in-to-vms-from-the-controller-vm"></a>コントローラー VM から VM にサインインする

SSH 経由でプライベート IP アドレスを使用して、スケール セットの VM にサインインします。

1. コントローラー VM にサインインします。
   
1. 次のコマンドを実行して、プライベート VM に接続します。
   
   ```bash
   sudo ssh <username>@<private IP address>
   ```
   
   例: `sudo ssh azureadmin@102.xx.xx.xx`
   
1. プロンプトでパスワードを入力します。

## <a name="next-steps"></a>次のステップ

Moodle 移行プロセスの次の手順として、[Moodle 手動移行手順](migration-start.md)を続けます。
