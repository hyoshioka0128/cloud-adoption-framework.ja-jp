---
title: Azure での ID ベースライン ツール
description: ID ベースライン規範をサポートするポリシーとプロセスを成熟させるのに、Azure ネイティブ ツールがどのように役立つかについて説明します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 09/17/2019
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: govern
ms.custom: governance
ms.openlocfilehash: 1a5dc85aba4b5bd37bc65682bd039ecf57658624
ms.sourcegitcommit: bd9872320b71245d4e9a359823be685e0f4047c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/26/2020
ms.locfileid: "83862332"
---
# <a name="identity-baseline-tools-in-azure"></a>Azure での ID ベースライン ツール

[ID ベースライン規範](./index.md)は、[クラウド ガバナンスの 5 つの規範](../governance-disciplines.md)のうちの 1 つです。 この規範では、アプリケーションまたはワークロードをホストするクラウド プロバイダーに関係なく、ユーザー ID の一貫性と継続性を保証するポリシーを確立する方法に焦点を当てています。

ハイブリッド ID の検出ガイドには、次のツールが含まれています。

**Active Directory (オンプレミス):** Active Directory は、ユーザーの資格情報を保存および検証するために企業で使用されることが最も多い ID プロバイダーです。

**Azure Active Directory:** サービスとしてのソフトウェア (SaaS)。Active Directory と同等の機能を持ち、オンプレミスの Active Directory とのフェデレーションが可能です。

**Active Directory (IaaS):** Azure の仮想マシンで実行されている Active Directory アプリケーションのインスタンス。

ID は IT セキュリティのコントロール プレーンです。 したがって、認証は、クラウドに対する組織のアクセスの保護です。 組織には、セキュリティを強化し、クラウド アプリを侵入者から保護する ID コントロール プレーンが必要です。

## <a name="cloud-authentication"></a>クラウド認証

正しい認証方法の選択は、クラウドにアプリを移行しようとしている組織にとって最大の関心事です。

この方法を選ぶと、Azure AD がユーザーのサインイン プロセスを処理します。 シームレスなシングル サインオン (SSO) と組み合わせることで、ユーザーは資格情報を再入力しなくてもクラウド アプリにサインインできます。 クラウド認証では、2 つのオプションから選ぶことができます。

**Azure AD のパスワード ハッシュ同期:** Azure AD でオンプレミスのディレクトリ オブジェクトの認証を有効にする最も簡単な方法です。 この方法は、オンプレミスのサーバーがダウンした場合のバックアップ フェールオーバー認証方法として、任意の方法と一緒に使用することもできます。

**Azure AD パススルー認証:** 1 つ以上のオンプレミス サーバーで実行されているソフトウェア エージェントを使用して、Azure AD 認証サービスに永続的なパスワード検証を提供します。

<!-- docsTest:ignore "the pass-through authentication method" -->

> [!NOTE]
> オンプレミスのユーザー アカウントの状態、パスワード ポリシー、およびサインイン時間をすぐに適用するセキュリティ要件のある企業では、パススルー認証方法を検討してください。

**フェデレーション認証:**

この方法を選択した場合、Azure AD では、別の信頼された認証システム (オンプレミスの Active Directory フェデレーション サービス (AD FS) や信頼できるサード パーティのフェデレーション プロバイダーなど) に、ユーザーのパスワードを検証する認証プロセスが渡されます。

[Azure Active Directory 用の適切な認証方法の選択](https://docs.microsoft.com/azure/active-directory/hybrid/choose-ad-authn)に関する記事には、組織に最適なソリューションを選択するためのデシジョン ツリーが含まれています。

次の表は、この規範をサポートするポリシーとプロセスを成熟させるのに役立つネイティブ ツールの一覧です。

<!-- markdownlint-disable MD033 -->
<!-- docsTest:ignore UserPrincipalName SamAccountName -->

<!-- TODO: Fix link for Azure Active Directory admin center -->

| 考慮事項 | パスワード ハッシュ同期 + シームレス SSO | パススルー認証 + シームレス SSO | AD FS とのフェデレーション |
| --- | --- | --- | --- |
| 認証が行われる場所 | クラウド内 | クラウド内で、オンプレミスの認証エージェントとのセキュリティで保護されたパスワード検証の交換後 | オンプレミス |
| プロビジョニング システム以外のオンプレミスのサーバーの要件: Azure AD Connect | なし | 追加の認証エージェントごとに 1 つのサーバー | 2 つ以上の AD FS サーバー <br><br> 境界/DMZ ネットワークに 2 つ以上の WAP サーバー |
| プロビジョニング システムを超えたオンプレミスのインターネットおよびネットワークの要件は何ですか? | なし | 認証エージェントを実行しているサーバーからの[発信インターネット アクセス](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-quick-start) | 境界の WAP サーバーへの[着信インターネット アクセス](https://docs.microsoft.com/windows-server/identity/ad-fs/overview/ad-fs-requirements) <br><br> 境界の WAP サーバーから AD FS サーバーへの着信ネットワーク アクセス <br><br> ネットワークの負荷分散 |
| SSL 証明書の要件 | いいえ | いいえ | はい |
| 正常性の監視ソリューション | 必要なし | エージェントの状態は [Azure Active Directory 管理センター](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-pass-through-authentication)によって提供される | [Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-adfs) |
| 会社のネットワーク内のドメインに参加しているデバイスからクラウドのリソースへのユーザーのシングル サインオン | [シームレス SSO](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso) を使用して実行 | [シームレス SSO](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso) を使用して実行 | はい |
| サポートされているサインインの種類 | UserPrincipalName + パスワード <br><br>  [シームレス SSO](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso) による統合 Windows 認証 <br><br> [代替ログイン ID](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-custom) | UserPrincipalName + パスワード <br><br> [シームレス SSO](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso) による統合 Windows 認証 <br><br> [代替ログイン ID](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-faq) | UserPrincipalName + パスワード <br><br> SamAccountName とパスワード <br><br> 統合 Windows 認証 <br><br> [証明書とスマート カード認証](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-user-certificate-authentication) <br><br> [代替ログイン ID](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id) |
| Windows Hello for Business のサポート | [キー信頼モデル](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification) <br><br> [Intune での証明書信頼モデル](https://microscott.azurewebsites.net/2017/12/16/setting-up-windows-hello-for-business-with-intune) | [キー信頼モデル](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification) <br><br> [Intune での証明書信頼モデル](https://microscott.azurewebsites.net/2017/12/16/setting-up-windows-hello-for-business-with-intune) | [キー信頼モデル](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification) <br><br> [証明書信頼モデル](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-key-trust-adfs) |
| 多要素認証のオプション | [Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication) <br><br> [条件付きアクセスを使用するカスタム コントロール*](https://docs.microsoft.com/azure/active-directory/conditional-access/controls#custom-controls-preview) | [Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication) <br><br> [条件付きアクセスを使用するカスタム コントロール*](https://docs.microsoft.com/azure/active-directory/conditional-access/controls#custom-controls-preview) | [Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication) <br><br> [Azure Multi-Factor Authentication サーバー](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy) <br><br> [サードパーティの多要素認証](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-additional-authentication-methods-for-ad-fs) <br><br> [条件付きアクセスを使用するカスタム コントロール*](https://docs.microsoft.com/azure/active-directory/conditional-access/controls#custom-controls-preview) |
| サポートされるユーザー アカウントの状態 | 無効なアカウント <br> (最大 30 分の遅延) | 無効なアカウント <br><br> アカウントのロックアウト <br><br> アカウント期限切れ <br><br> パスワード期限切れ <br><br> サインイン時間 | 無効なアカウント <br><br> アカウントのロックアウト <br><br> アカウント期限切れ <br><br> パスワード期限切れ <br><br> サインイン時間 |
| 条件付きアクセスのオプション | [Azure AD 条件付きアクセス](https://docs.microsoft.com/azure/active-directory/conditional-access/overview) | [Azure AD 条件付きアクセス](https://docs.microsoft.com/azure/active-directory/conditional-access/overview) | [Azure AD 条件付きアクセス](https://docs.microsoft.com/azure/active-directory/conditional-access/overview) <br><br> [AD FS の要求規則](https://adfshelp.microsoft.com/AadTrustClaims/ClaimsGenerator) |
| サポートされる従来のプロトコルのブロック | [はい](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-baseline-protection) | [はい](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-baseline-protection) | [はい](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/access-control-policies-w2k12) |
| サインイン ページのロゴ、イメージ、説明のカスタマイズ可能性 | [Azure AD Premium を使用して可能](https://docs.microsoft.com/azure/active-directory/customize-branding) | [Azure AD Premium を使用して可能](https://docs.microsoft.com/azure/active-directory/customize-branding) | [はい](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-federation-management#customlogo) |
| サポートされる高度なシナリオ | [Smart Password Lockout](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks) <br><br> [漏洩した資格情報レポート](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events) | [Smart Password Lockout](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-smart-lockout) | 複数サイトの低待機時間の認証システム <br><br> [AD FS エクストラネットのロックアウト](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection) <br><br> [サード パーティの ID システムとの統合](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-federation-compatibility) |

<!-- markdownlint-enable MD033 -->

> [!NOTE]
> Azure AD の条件付きアクセスでのカスタム コントロールは、現時点ではデバイスの登録をサポートしていません。

## <a name="next-steps"></a>次のステップ

<!-- TODO: The download button for this whitepaper returns 404. -->

<!-- docsTest:ignore "Hybrid Identity Digital Transformation Framework" -->

[ハイブリッド ID デジタル変換フレームワークに関するホワイトペーパー](https://resources.office.com/ww-landing-M365E-EMS-IDAM-Hybrid-Identity-WhitePaper.html)では、これらの各コンポーネントを選択および統合するための組み合わせとソリューションについて説明しています。

[Azure AD Connect ツール](https://aka.ms/aadconnectwiz)は、オンプレミスのディレクトリを Azure AD と統合するために役立ちます。
