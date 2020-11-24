---
title: 仮想ネットワーク ゲートウェイを作成してプライベート IP 経由で接続する方法
description: 仮想ネットワーク ゲートウェイを作成してプライベート IP 経由で接続する方法について説明します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 11/06/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: plan
ms.openlocfilehash: f4b3c36fcbebda0501a05f4b0751832202be9bd2
ms.sourcegitcommit: a7eb2f6c4465527cca2d479edbfc9d93d1e44bf1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2020
ms.locfileid: "94714907"
---
# <a name="how-to-create-a-virtual-network-gateway-and-connect-through-a-private-ip"></a>仮想ネットワーク ゲートウェイを作成してプライベート IP 経由で接続する方法

このドキュメントでは、Azure で仮想ネットワーク ゲートウェイを設定する方法について説明します。

## <a name="getting-started"></a>作業の開始

次の手順を使用して、Azure portal に仮想ネットワーク ゲートウェイを作成します。

- **[仮想ネットワーク ゲートウェイ]** を検索して選択します。
- **[作成]** を選択してウィンドウを開きます。
- **名前、リージョン、ゲートウェイの種類、SKU**、**VNet** などの各フィールドに入力します。 残りは既定値のままにします。
- 同じリソース グループに作成された仮想マシンに関連付けられている仮想ネットワークを選択します。
- **[作成]** を選択してデプロイを開始します。

![仮想ネットワーク ゲートウェイを作成する。](images/vpn-gateway.png)

- 次の Azure CLI コマンドを使用して、仮想ネットワーク ゲートウェイを作成します。

    ```bash
    az network vnet-gateway create -g MyResourceGroup -n MyVnetGateway --public-ip-address MyGatewayIp --vnet MyVnet --gateway-type Vpn --sku VpnGw1 --vpn-type RouteBased --no-wait
    ```

## <a name="generate-certificates"></a>証明書の生成

- ルートと子の証明書用に Windows PowerShell ISE を開きます。

- ルート証明書を生成するコマンド:

  ```bash
  $cert = New-SelfSignedCertificate -Type Custom -KeySpec Signature `
  -Subject "CN=P2SRootCert" -KeyExportPolicy Exportable `
  -HashAlgorithm sha256 -KeyLength 2048 `
   -CertStoreLocation "Cert:\CurrentUser\My" -KeyUsageProperty Sign -KeyUsage CertSign
  ```

- 子証明書を生成するコマンド:

  ```bash
  New-SelfSignedCertificate -Type Custom -DnsName P2SChildCert -KeySpec Signature `
  -Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
  -HashAlgorithm sha256 -KeyLength 2048 `
  -CertStoreLocation "Cert:\CurrentUser\My" `
  -Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")
  ```

## <a name="export-certificates"></a>証明書のエクスポート

こちらの手順を実行すると、ローカル システムに証明書を正常にインストールできるようになります。

- Microsoft 管理コンソールを開き、証明書をエクスポートします。
- **[ファイル名を指定して実行]** に移動します。 「**MMC**」と入力して証明書を開きます。
- **[個人]** フォルダーの下にある **[証明書]** を選択して、ページを開きます。
- ページを更新して **ルートと子の証明書** を見つけます。

証明書の種類:

**ルート証明書をエクスポートするには:**

- ルート証明書を選択し、証明書を選択したまま (または右クリックし)、 **[すべてのタスク]** に移動します。
- 新しいウィンドウで **[エクスポート]** を選択して、 **[次へ]** を選択します。
- **[いいえ、秘密キーをエクスポートしません]** を選択して、 **[次へ]** を選択します。
- **[Base 64 encoded X.509 (.cer)]** を選択して、 **[次へ]** をクリックします。
- **[参照]** を選択してパスを選択し、名前を入力して、 **[次へ]** を選択します。
- 次のメッセージが表示されます:**正しくエクスポートされました**。

**子の証明書をエクスポートするには:**

- クライアント証明書を選択し、証明書を選択したまま (または右クリックし)、 **[すべてのタスク]** に移動します。
- 新しいウィンドウで **[エクスポート]** を選択して、 **[次へ]** を選択します。
- **[はい**、**秘密キーをエクスポートします]** を選択して、 **[次へ]** を選択します。
- **[Personal Information Exchange]** (**PKCS**) を選択して、 **[次へ]** を選択します。
- パスワード チェックボックスをオンにして、パスワードを入力します。
- 暗号化を TripleDES-SHA1 に設定し、 **[次へ]** を選択します。
- **[参照]** を選択してパスを選択し、名前を入力して、 **[次へ]** を選択します。
- 次のメッセージが表示されます:**正しくエクスポートされました**。
- 任意のエディターでルート証明書ファイルを開き、コードをコピーします。

## <a name="configure-the-virtual-network-gateway"></a>仮想ネットワーク ゲートウェイを構成する

- 仮想ネットワーク ゲートウェイが作成されたリソース グループに移動します。
- 左側のパネルで、ポイント対サイト構成に移動します。
- 中央のパネルで [今すぐ構成] をクリックします。
- アドレス プールを追加します (例: 192.168.xx.0/24)。
- **[トンネルの種類]** で、 **[IKEv2]** を選択します。
- 認証の種類を **[Azure 認定]** に設定します。
- コピーしたルート証明書コードをポータルに貼り付け、**Root** という名前を付けて、 **[保存]** を選択します。

## <a name="download-and-connect-to-the-vpn-client"></a>VPN クライアントをダウンロードして接続する

- ポータルから構成を保存した後、VPN クライアントをダウンロードします。
- ダウンロードした VPN クライアントの zip ファイルを開き、`WindowsAMD64` フォルダーを開いて、`VPNClinetsetupAMD64` ファイルをインストールします。
- **[コントロール パネル]\[ネットワークとインターネット]\[ネットワーク接続]** に移動し、インストールした VPN を表示します。
- VPN を右クリックし、 **[接続]** を選択します。
- 新しいウィンドウが表示されます。 **[接続]** ボタンを選択して接続します。

VPN ゲートウェイ接続が確立されます。

## <a name="log-in-to-the-virtual-machine"></a>仮想マシンにログインします。

- SSH キーを使用して、プライベート IP で仮想マシンにログインします。

- こちらのコマンドを実行して、パスワード認証を設定します。

  ```bash
  sudo vi /etc/ssh/sshd_config
  ```

- これらのパラメーターを更新します。パスワード認証の種類を **no** から **yes** に変更し、コメント化された UserLogin を見つけて **#** のコメントを削除し、**yes** に変更します。

- `ESC` キーを押し、「`:wq!`」と入力して変更を保存します。

- SSHD を再起動します。

  ```bash
  sudo systemctl restart sshd
  ```

- パスワードは 14 文字までにする必要があります。 次のコマンドを使用して、パスワードを設定します。

  ```bash
  sudo passwd <username>
  ```

  例: `sudo passwd azureadmin`

パスワード認証が完了しました。

## <a name="log-in-to-virtual-machine-instance-from-a-controller-virtual-machine"></a>コントローラー仮想マシンから仮想マシン インスタンスにログインする

- クライアント仮想マシンにログインします。

- こちらのコマンドを実行して、プライベート仮想マシンに接続します。

  ```bash
  sudo ssh <username>@<private_IP>
  ```

例: `sudo ssh azureadmin@102.xx.xx.xx`

- プロンプトに従ってパスワードを入力します。

## <a name="next-steps"></a>次のステップ

Moodle 移行プロセスの次のステップについては、「[手動による Moodle 移行を開始する方法](./migration-start.md)」に進んでください。
