---
title: ID 管理とアクセス管理
description: エンタープライズ環境での ID およびアクセス管理に関連する設計上の考慮事項と推奨事項を検討します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 06/15/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.openlocfilehash: 8c634942d867bdc55bba84b15ac0789a4abd27a0
ms.sourcegitcommit: bcc73d194c6d00c16ae2e3c7fb2453ac7dbf2526
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2020
ms.locfileid: "86194682"
---
# <a name="identity-and-access-management"></a>ID 管理とアクセス管理

ID がセキュリティ保証の大部分の基礎を提供します。 クラウド サービスで ID の認証と承認の制御に基づいてアクセスを管理することで、データやリソースを保護し、どの要求を許可するかを決定します。

ID およびアクセス管理 (IAM) はパブリック クラウドの境界セキュリティであり、セキュリティで保護され、完全に準拠したパブリック クラウド アーキテクチャの基盤として扱う必要があります。 Microsoft Azure には、組織が安全性と運用効率に優れた環境を構築できるようにするための、サービス、ツール、リファレンス アーキテクチャの包括的なセットが用意されています。ここでは、これらの概要について説明します。

このセクションでは、エンタープライズ環境での ID およびアクセス管理に関連する設計上の考慮事項と推奨事項を検討します。

## <a name="why-we-need-identity-and-access-management"></a>ID とアクセスの管理が必要な理由

企業の技術的な展望は、複雑で異種混合になりつつあります。 この環境のコンプライアンスとセキュリティを管理するために、IAM を使用して、適切な個人が適切な理由で適切なタイミングで適切なリソースにアクセスできるようにします。

### <a name="planning-for-identity-and-access-management"></a>ID およびアクセス管理の計画

通常、エンタープライズ組織では、操作のアクセスに対して最小限の特権のアプローチに従います。 このモデルは、Azure AD のロールベースのアクセス制御 (RBAC) とカスタム ロール定義によって、Azure を考慮するように拡張する必要があります。 Azure 内のリソースに対するコントロールおよびデータ プレーン アクセスの管理方法を計画することが重要です。 IAM および RBAC のすべての設計が承認されるには、規制、セキュリティ、および運用の要件を満たしている必要があります。

ID およびアクセス管理は、ID 統合の慎重な計画と、レガシ認証のブロックや最新のパスワードの計画など、その他のセキュリティの考慮事項を含む、複数の手順からなるプロセスです。 ステージング計画には、企業間 (B2B) または企業と消費者間 (B2C) の ID およびアクセス管理の選択も含まれます。 これらの要件はさまざまですが、エンタープライズ ランディング ゾーンについて考慮すべき一般的な設計上の考慮事項と推奨事項があります。

![ID 管理とアクセス管理](./media/iam.png)

"_図 1:ID およびアクセス管理。_

**設計上の考慮事項:**

- IAM とガバナンスに関するフレームワークをレイアウトするときに考慮する必要があるカスタム ロールとロールの割り当ての数には制限があります。 これらについては、次のページを参照してください。[Azure RBAC サービスの制限](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#role-based-access-control-limits)
- サブスクリプションごとに 2000 のカスタム RBAC ロール割り当ての制限があります。

- 管理グループごとに 500 のカスタム RBAC ロール割り当ての制限があります。

- リソース所有権の一元化とフェデレーション。

  - 共有リソース、またはネットワークなどのセキュリティ境界を実装または適用する環境の側面は、一元的に管理する必要があります。 これは、多くの規制フレームワークの要件であるだけでなく、機密または重要なビジネス リソースへのアクセスを許可または拒否する組織の標準的な方法でもあります。

  - セキュリティ境界や、セキュリティとコンプライアンスの維持に必要なその他の側面に違反していないアプリケーション リソースの管理は、アプリケーション チームに委任することができます。 安全に管理された環境内でユーザーがリソースをプロビジョニングできるようにすると、組織ではクラウドのアジャイルな性質を活用し、さらに重要なセキュリティやガバナンスの境界の違反を防ぐことができます。

<!-- docsTest:ignore Azure-AD-only Azure-AD-managed NetOps SecOps AppOps -->

**設計上の推奨事項:**

- 可能な限り、Azure AD [RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview) を使用してリソース (Azure Key Vault、ストレージ アカウント、SQL Database など) へのデータ プレーン アクセスを管理します。

- Azure 環境への権限を持つすべてのユーザーに対して Azure AD 条件付きアクセス ポリシーをデプロイします。 こうすることで、管理対象の Azure 環境を不正アクセスから保護するためのもう 1 つのメカニズムが実現します。

- Azure 環境への権限を持つすべてのユーザーに対して多要素認証を適用します。 これは多くのコンプライアンス フレームワークの要件であり、資格情報の盗難や不正アクセスのリスクが大幅に低減されます。

- [Azure AD Privileged Identity Management (PIM)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) を使用して、ゼロ スタンディング アクセスと最小限の特権を確立します。 組織のロールを、必要な最小限レベルのアクセスにマップします。 Azure AD PIM は、既存のツールとプロセスの拡張機能にするか、前述のように Azure ネイティブ ツールを利用するか、必要に応じてこの両方を使用することができます。

- リソースへのアクセスを許可する場合は、Azure AD PIM の Azure コントロール プレーン リソースに Azure AD のみのグループを使用します。

  - グループ管理システムが既に配置されている場合は、オンプレミス グループを Azure AD のみのグループに追加します。

- Azure AD PIM アクセス レビューを使用して、リソースのエンタイトルメントを定期的に検証します。 アクセス レビューは、多くのコンプライアンス フレームワークの一部であるため、多くの組織では、この要件に対処するためのプロセスが既に配置されています。

- Azure AD ログをプラットフォーム中心の [Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-activity-logs-azure-monitor) と統合します。 Azure Monitor は、Azure のログと監視データに関して信頼できる唯一の情報源として機能し、ログの収集と保持に関する要件を満たすクラウドネイティブのオプションが組織に提供されます。

- データ主権要件が存在する場合は、カスタム ユーザー ポリシーをデプロイして適用することができます。

- 次の主要なロールを考慮しながら、Azure AD テナント内でカスタムの RBAC ロール定義を使用します。

| Role | 使用法 | Actions | アクションなし |
|---|---|---|---|
| Azure プラットフォーム所有者               | 管理グループとサブスクリプションのライフサイクル管理                                                           | `*`                                                                                                                                                                                                                  |                                                                                                                                                                                         |
| ネットワーク管理 (NetOps)        | プラットフォーム全体のグローバル接続管理:VNet、UDR、NSG、NVA、VPN、ExpressRoute など            | `*/read`、`Microsoft.Authorization/*/write`、`Microsoft.Network/vpnGateways/*`、`Microsoft.Network/expressRouteCircuits/*`、`Microsoft.Network/routeTables/write`、`Microsoft.Network/vpnSites/*`                              |                                                                                                                                                                               |
| セキュリティ操作 (SecOps)       | Azure 資産と Azure Key Vault の消去ポリシー全体の水平方向のビューを持つセキュリティ管理者ロール | `*/read`, `*/register/action`, `Microsoft.KeyVault/locations/deletedVaults/purge/action`, `Microsoft.Insights/alertRules/*`, `Microsoft.Authorization/policyDefinitions/*`, `Microsoft.Authorization/policyAssignments/*`, `Microsoft.Authorization/policySetDefinitions/*`, `Microsoft.PolicyInsights/*`, `Microsoft.Security/*` |                                                                            |
| サブスクリプションの所有者                 | サブスクリプション所有者のロールから派生したサブスクリプション所有者の委任されたロール                                       | `*`                                                                                                                                                                                                                  | `Microsoft.Authorization/*/write`、`Microsoft.Network/vpnGateways/*`、`Microsoft.Network/expressRouteCircuits/*`、`Microsoft.Network/routeTables/write`、`Microsoft.Network/vpnSites/*` |
| アプリケーションの所有者 (DevOps/AppOps) | リソース グループ レベルでアプリケーションおよび運用チームに付与される共同作成者ロール                                 |                                                                                                                                                                                                                    | `Microsoft.Network/publicIPAddresses/write`、`Microsoft.Network/virtualNetworks/write`、`Microsoft.KeyVault/locations/deletedVaults/purge/action`                                         |

- Azure Security Center の Just-In-Time (JIT) アクセスをすべてのサービスとしてのインフラストラクチャ (IaaS) リソースに使用して、IaaS 仮想マシンへの一時的なユーザー アクセスに対してネットワークレベルの保護を有効にします。

- Azure リソースに Azure AD のマネージド ID を使用して、ユーザー名とパスワードに基づく認証を回避します。 パブリック クラウド リソースのセキュリティ違反の多くは、コードまたはその他のテキスト ソースに埋め込まれた資格情報の盗難に起因しているため、プログラムによるアクセスにマネージド ID を適用することで、資格情報の盗難を大幅に減らすことができます。

- 管理者特権でのアクセス許可を必要とする Automation Runbook に特権 ID を使用します。 重要なセキュリティ境界に違反する自動ワークフローは、同等の特権を持つユーザーと同じツールとポリシーで管理する必要があります。

- ユーザーを Azure リソース スコープに直接追加しないでください。 このように一元管理がないと、制限されたデータに対する不正アクセスを防ぐために必要な管理が大幅に増加します。

### <a name="planning-for-authentication-inside-a-landing-zone"></a>ランディング ゾーン内の認証の計画

企業組織が Azure を導入する際に下す必要がある重要な設計上の判断は、既存のオンプレミス ID ドメインを Azure に拡張するか、まったく新しいものを作成するかということです。 ランディング ゾーン内の認証の要件を十分に評価し、Windows Server 上の Active Directory Domain Services (AD DS)、Azure AD Domain Services、またはその両方をデプロイする計画に組み込む必要があります。 ほとんどの Azure 環境では、少なくとも Azure ファブリック認証および AD DS ローカル ホスト認証と、グループ ポリシー管理に Azure AD を使用します。

**設計上の考慮事項:**

- ランディング ゾーン内にデプロイされたリソースを管理するために、一元化され、委任された責任を検討します。

- ドメイン サービスに依存し、以前のプロトコルを使用するアプリケーションでは、[Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services) を使用できます。

**設計上の推奨事項:**

- 一元化され、さらに委任された責任を使用して、ロールとセキュリティの要件に基づいて、ランディング ゾーン内にデプロイされたリソースを管理します。

- サービス プリンシパル オブジェクトの作成、Azure AD へのアプリケーションの登録、証明書またはワイルドカード証明書の取得と処理などの特権操作には、特別なアクセス許可が必要です。 そのような要求を処理するユーザーと、必要な注意を払ってアカウントをセキュリティで保護および監視する方法を検討してください。

- 統合 Windows 認証を使用するアプリケーションに Azure AD 経由でリモート アクセスする必要があるシナリオが組織にある場合は、[Azure AD Application Proxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) の使用を検討します。

- Azure AD、Azure AD Domain Services、および Windows Server 上で実行されている AD DS には違いがあります。 アプリケーションのニーズを評価し、それぞれに使用される認証プロバイダーを理解してドキュメント化します。 すべてのアプリケーションに応じて計画を立てます。

- Windows Server 上の AD DS と Azure AD Domain Services のワークロードの互換性を評価します。

- ネットワーク設計では、ローカル認証と管理のために Windows Server 上の AD DS を必要とするリソースが、適切なドメイン コントローラーにアクセスできるようにします。

  - Windows Server 上の AD DS については、大規模な企業全体のネットワーク コンテキストでローカル認証とホスト管理を提供する共有サービス環境を検討します。

- このサービスは 1 つのサブスクリプションにのみ投影できるため、プライマリ リージョン内に Azure AD Domain Services をデプロイします。

- Azure サービスに対する認証には、サービス プリンシパルではなくマネージド ID を使用します。 これにより、資格情報の盗難の危険性が軽減します。
