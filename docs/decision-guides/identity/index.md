---
title: ID 決定ガイド
description: ID およびアクセス管理 (IAM) サービスを使用して、クラウドでアクセス制御を管理する方法について説明します。
author: rotycenh
ms.author: abuck
ms.date: 02/11/2019
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: decision-guide
ms.custom: governance
ms.openlocfilehash: ed4c1c3cf7958f4e96db7eba71fe1f008bc81eb2
ms.sourcegitcommit: 60d8b863d431b5d7c005f2f14488620b6c4c49be
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2020
ms.locfileid: "83222846"
---
<!-- cSpell:ignore Kerberos NTLM SAML -->

# <a name="identity-decision-guide"></a>ID 決定ガイド

オンプレミス環境、ハイブリッド環境、クラウドのみの環境など、どのような環境であっても、IT 部門はリソースへのアクセス権を持つ管理者、ユーザー、およびグループを制御する必要があります。 ID 管理とアクセス管理 (IAM) サービスを使用すると、クラウドでアクセス制御を管理できます。

![ID オプションを単純なものから複雑なものへと順番に配置し、その下にジャンプ先へのリンクを示します](../../_images/decision-guides/decision-guide-identity.png)

ジャンプ先:[ID 統合の要件の決定](#determine-identity-integration-requirements) | [クラウド ベースライン](#cloud-baseline) | [ディレクトリ同期](#directory-synchronization) | [クラウドでホストされるドメイン サービス](#cloud-hosted-domain-services) | [Active Directory フェデレーション サービス](#active-directory-federation-services) | [詳細情報](#learn-more)

クラウド環境で ID を管理するためのオプションがいくつかあります。 これらのオプションは、コストと複雑さによって異なります。 クラウド ベースの ID サービスを構築する際の重要な要素は、既存のオンプレミス ID インフラストラクチャで必要とされる統合のレベルです。

Azure Active Directory (Azure AD) は Azure リソースを対象にした基本レベルのアクセス制御と ID 管理を提供します。 ただし、組織のオンプレミスの Active Directory インフラストラクチャに複雑なフォレスト構造やカスタマイズされた組織単位 (OU) があると、クラウドベースのワークロードでは、オンプレミス環境とクラウド環境の間で一貫性のある ID、グループ、およびロールを保持するために、Azure AD とのディレクトリ同期が必要となる場合があります。 さらに、レガシ認証メカニズムに依存しているアプリケーションをサポートするために、クラウドへの Active Directory Domain Services (AD DS) のデプロイが必要になる場合もあります。

クラウドベースの ID 管理は反復的なプロセスです。 最初のデプロイは、クラウドネイティブ ソリューションを使用して、ユーザーと対応するロールの少数のセットで開始できます。 移行が進むにつれて、ディレクトリ同期を使用した ID ソリューションの統合や、クラウドへのデプロイの一部としてのドメイン サービスの追加が必要になる場合があります。 移行プロセスを繰り返すたびに、ID 戦略を見直してください。

## <a name="determine-identity-integration-requirements"></a>ID 統合の要件の決定

| Question | クラウド ベースライン | ディレクトリ同期 | クラウドでホストされたドメイン サービス | Active Directory フェデレーション サービス (AD FS) |
|------|------|------|------|------|
| 現在、オンプレミス ディレクトリ サービスがない | はい | いいえ | いいえ | いいえ |
| ワークロードがクラウド環境とオンプレミス環境の間で共通のユーザーおよびグループのセットを使用する必要がある | いいえ | はい | いいえ | いいえ |
| ワークロードがレガシ認証メカニズム (Kerberos や NTLM など) に依存している | いいえ | いいえ | はい | はい |
| 複数の ID プロバイダーにわたってシングル サインオンが必要である | いいえ | いいえ | いいえ | はい |

Azure への移行を計画する際には、既存の ID 管理とクラウド ID サービスを最適に統合する方法を決定する必要があります。 一般的な統合シナリオを以下に示します。

### <a name="cloud-baseline"></a>クラウド ベースライン

Azure AD は、ユーザーとグループに Azure プラットフォーム上の管理機能へのアクセス許可を付与するための、ネイティブの ID とアクセス管理 (IAM) システムです。 有効なオンプレミス ID ソリューションを持たず、クラウドベースの認証メカニズムとの互換性を持たせるためにワークロードの移行を計画する組織は、Azure AD を基盤として使用しながら自身の ID インフラストラクチャの構築を開始する必要があります。

**クラウドでのベースラインの前提条件:** 完全にクラウドネイティブな ID インフラストラクチャを使用するには、次の事項が前提となります。

- クラウドベースのリソースは、オンプレミス ディレクトリ サービスや Active Directory サーバーに従属しないこと。または、そのような従属性を取り除くためにワークロードを変更できること。
- 移行するアプリケーションまたはサービスのワークロードが Azure AD と互換性のある認証メカニズムをサポートしていること。または、そのようなメカニズムをサポートするために容易に変更可能であること。 Azure AD は、SAML、OAuth、OpenID Connect などのインターネット対応の認証メカニズムを使用します。 Kerberos や NTLM などのプロトコルを使用するレガシ認証方法に依存する既存のワークロードは、クラウドに移行する前に、クラウドのベースライン パターンを使用してリファクタリングしなければならない場合があります。

> [!TIP]
> ID サービスを Azure AD に完全に移行することで、独自の ID インフラストラクチャを維持する必要がなくなり、IT 管理が大幅に簡素化されます。
>
> ただし、Azure AD は従来のオンプレミスの Active Directory インフラストラクチャに完全に置き換わるものではありません。 レガシ認証方法、コンピューター管理、グループ ポリシーなどのディレクトリ機能は、クラウドに追加のツールやサービスをデプロイしないと利用できません。
>
> オンプレミスの ID またはドメイン サービスをクラウドのデプロイと統合する必要があるシナリオの場合、以下で説明するディレクトリ同期およびクラウドでホストされるドメイン サービスのパターンを参照してください。

### <a name="directory-synchronization"></a>ディレクトリ同期

多くの場合、既存のオンプレミス Active Directory インフラストラクチャを持つ組織においては、既存のユーザーとアクセス管理を保持すると同時に、クラウド リソースの管理に必要な IAM 機能を提供するためには、ディレクトリ同期が最適なソリューションです。 このプロセスでは、Azure AD とオンプレミスのディレクトリ サービスの間でディレクトリ情報を継続的にレプリケートし、ユーザーの共通資格情報と、一貫性のある ID、ロール、アクセス許可のシステムを組織全体で可能にします。

> [!NOTE]
> Office 365 を導入済みの組織では、オンプレミス Active Directory インフラストラクチャと Azure Active Directory の間に[ディレクトリ同期](https://docs.microsoft.com/office365/enterprise/set-up-directory-synchronization)を既に実装している場合があります。

**ディレクトリ同期の前提条件:** 同期済み ID ソリューションを使用するには、次の事項が前提となります。

- クラウドとオンプレミスの IT インフラストラクチャ全体で、ユーザー アカウントとグループの共通セットを保持する必要がある。
- オンプレミス ID サービスが Azure AD とのレプリケーションをサポートしている。

> [!TIP]
> オンプレミスの Active Directory サーバーによって提供され、Azure AD によってサポートされないレガシ認証メカニズムに依存するすべてのクラウドベース ワークロードでは、これらのサービスを提供するクラウド環境のオンプレミス ドメイン サービスまたは仮想サーバーのいずれかへの接続が引き続き必要となります。 オンプレミス ID サービスを使用すると、クラウド ネットワークとオンプレミス ネットワーク間の接続性にも依存します。

### <a name="cloud-hosted-domain-services"></a>クラウドでホストされたドメイン サービス

Kerberos や NTLM などの従来のプロトコルを使用する要求ベースの認証に依存するワークロードがあり、SAML や OAuth、OpenID Connect などの最新の認証プロトコルを許可するようにそれらのワークロードをリファクタリングできない場合、クラウドをデプロイする際に、一部のドメイン サービスをクラウドに移行しなければならない場合があります。

このパターンでは、クラウド上のリソース用の Active Directory Domain Services (AD DS) を提供するために、Active Directory を実行する仮想マシンをクラウドベース仮想ネットワークにデプロイすることが必要になります。 クラウド ネットワークに移行する既存のすべてのアプリケーションとサービスは、マイナーな変更によって、これらのクラウドでホストされたディレクトリ サーバーを使用できるようにする必要があります。

既存のディレクトリとドメイン サービスは、オンプレミス環境で引き続き使用される場合が多いと思われます。 そのようなシナリオでは、クラウド環境とオンプレミス環境の両方で共通のユーザーとロールを提供するために、ディレクトリ同期も使用することをお勧めします。

**クラウドでホストされるドメイン サービスの前提条件:** ディレクトリの移行を実行するには、次の事項が前提となります。

- ワークロードが Kerberos や NTLM などのプロトコルを使用する要求ベース認証に依存している。
- Active Directory グループ ポリシーの管理または利用のために、ワークロード仮想マシンをドメインに参加させる必要がある。

> [!TIP]
> クラウドでホストされたドメイン サービスを伴うディレクトリ移行では、既存のワークロードの移行において優れた柔軟性がありますが、これらのサービスを提供するためにクラウド仮想ネットワーク内に仮想マシンをホストすることにより、IT 管理タスクが複雑化します。 クラウド移行に慣れてきたら、これらのサーバーをホスティングする長期的なメンテナンスの要件を確認します。 Azure Active Directory などのクラウド ID プロバイダとの互換性のために既存のワークロードをリファクタリングすることで、クラウドでホストされたこれらのサーバーの必要性を削減することが可能かどうかを検討します。

### <a name="active-directory-federation-services"></a>Active Directory フェデレーション サービス (AD FS)

ID フェデレーションは、複数の ID 管理システムにわたって信頼関係を確立し、共通の認証機能と許可機能を可能にします。 これにより、組織内の複数のドメインや、お客様またはビジネス パートナーによって管理される ID システム全体でシングル サインオン機能をサポートできるようになります。

Azure AD は、[Active Directory フェデレーション サービス (AD FS)](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-fed-whatis) を使用するオンプレミスの Active Directory ドメインのフェデレーションをサポートします。 これを Azure に実装する方法の詳細については、「[AD FS を Azure に拡張する](https://docs.microsoft.com/azure/architecture/reference-architectures/identity/adfs)」を参照してください。

## <a name="learn-more"></a>詳細情報

Azure での ID サービスの詳細については、以下を参照してください。

- **[Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)。** Azure AD は、クラウド ベースの ID サービスを提供します。 Azure リソースへのアクセスの管理と、ID 管理、デバイス登録、ユーザー プロビジョニング、アプリケーション アクセス制御、およびデータ保護の制御を可能にします。
- **[Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-hybrid-identity)。** Azure AD Connect ツールを使用すると、既存の ID 管理ソリューションを使用して Azure AD インスタンスに接続でき、クラウド上の既存ディレクトリの同期が可能になります。
- **[ロールベースのアクセス制御 (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview)。** Azure AD は、管理プレーン内のリソースへのアクセスを効果的かつ安全に管理するための RBAC を提供します。 ジョブと責任はロールに編成され、ユーザーにはこれらのロールが割り当てられます。 RBAC により、リソースへアクセス可能なユーザーと、そのリソースに実行可能なアクションを制御できます。
- **[Azure AD Privileged Identity Management (PIM)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure)。** PIM は、リソース アクセス特権の露出時間を短縮し、レポートとアラートを介して特権の使用への可視性を高めます。 「ジャスト イン タイム」 (JIT) での特権の取得や、短期間での特権の割り当て (割り当て後に自動的に特権が取り消される) により、ユーザーを制限します。
- **[オンプレミスの Active Directory ドメインと Azure Active Directory を統合する](https://docs.microsoft.com/azure/architecture/reference-architectures/identity/azure-ad)。** この参照アーキテクチャでは、オンプレミス Active Directory ドメインと Azure AD の間のディレクトリ同期の例を示します。
- **[Active Directory Domain Services (AD DS) を Azure に拡張する](https://docs.microsoft.com/azure/architecture/reference-architectures/identity/adds-extend-domain)。** この参照アーキテクチャでは、ドメイン サービスをクラウドベース リソースに拡張するために AD DS サーバーをデプロイする例を示します。
- **[Active Directory フェデレーション サービス (AD FS) を Azure に拡張する](https://docs.microsoft.com/azure/architecture/reference-architectures/identity/adfs)。** この参照アーキテクチャでは、Azure AD ディレクトリでのフェデレーション認証と許可を実行するために Active Directory フェデレーション サービス (AD FS) を構成する方法を示します。

## <a name="next-steps"></a>次のステップ

ID は、クラウド導入プロセスでのアーキテクチャに関する意思決定で要求されるコア インストラクチャ コンポーネントの 1 つにすぎません。 他の種類のインフラストラクチャの設計に関する決定を行うときに使用される代替パターンまたはモデルについては、[決定ガイドの概要](../index.md)に関するページを参照してください。

> [!div class="nextstepaction"]
> [アーキテクチャの意思決定ガイド](../index.md)
