---
title: SAP on Azure のエンタープライズ規模のセキュリティ、ガバナンス、およびコンプライアンス
description: 共有責任モデルについて理解し、SAP on Azure のセキュリティ、コンプライアンス、ガバナンスの設計に関する推奨事項と考慮事項について説明します。
author: deepakonics
ms.author: brblanch
ms.date: 03/01/2021
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.custom: think-tank, e2e-sap
ms.openlocfilehash: 46fbdd8c59cb7eea5fc5a6347e9f71009aedac79
ms.sourcegitcommit: 5716a8165934bd69d02d9d3641785039196aee3a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "105734605"
---
<!-- docutune:casing "Enterprise Edition" SOAR -->

# <a name="enterprise-scale-security-governance-and-compliance-for-sap-on-azure"></a>SAP on Azure のエンタープライズ規模のセキュリティ、ガバナンス、およびコンプライアンス

SAP は、今日の最も重要なワークロードで多くの組織が使用する共通テクノロジです。 SAP アーキテクチャを計画するときは、アーキテクチャが堅牢で安全であることを保証するために、特に注意を払う必要があります。 この記事の目的は、エンタープライズ規模の SAP on Azure のセキュリティ、コンプライアンス、ガバナンスの設計基準を文書化することです。 この記事では、フィールドからの入力を反映する設計上の推奨事項、ベストプラクティス、および設計上の考慮事項について説明します。

クラウド ソリューションは、最初は単一の比較的分離されたアプリケーションをホストしていました。 クラウド ソリューションのメリットが明らかになったため、クラウドでは、SAP on Azure などの大規模なワークロードが多数ホストされています。 クラウド サービスのライフサイクル全体を通じて、1 つ以上のリージョンでのデプロイのセキュリティ、信頼性、パフォーマンス、コストの問題に対処することが不可欠になりました。

Azure における SAP エンタープライズ規模のランディング ゾーンのセキュリティ、コンプライアンス、ガバナンスのビジョンは、リスクを防ぎ、効果的な意思決定を行うためのツールとプロセスを組織に提供することです。 企業規模のランディング ゾーンでは、セキュリティ、ガバナンス、およびコンプライアンスの役割と責任が定義されているため、すべてのユーザーが想定している内容を把握できます。

## <a name="shared-responsibility-model"></a>共同責任モデル

パブリック クラウド サービスを評価する際には、クラウド プロバイダーと顧客が処理するタスクを理解しておくことが重要です。 共有責任モデルでは、クラウド プロバイダーとその顧客の間の責任の分担は、ワークロードのクラウド ホスティング モデル (サービスとしてのソフトウェア (SaaS)、サービスとしてのプラットフォーム (PaaS)、または サービスとしてのインフラストラクチャ (IaaS)) によって決まります。 お客様は、クラウド デプロイ モデルに関係なく、常にデータ、エンドポイント、およびアカウントとアクセスの管理を担当します。

次の図は、Microsoft のクラウド共有責任モデルにおける責任ゾーン間でのタスクの分割を示しています。

![SaaS、PaaS、IaaS、オンプレミスのデプロイ モデルにおけるお客様またはクラウド プロバイダーの役割図。](./media/responsibility-zones.png)

共有責任モデルの詳細については、「[クラウドでの共有責任](/azure/security/fundamentals/shared-responsibility)」を参照してください。

## <a name="security-design-recommendations"></a>セキュリティ設計に関する推奨事項

セキュリティは Microsoft と顧客の間で分担されます。 独自の仮想マシン (VM) とデータベース イメージを Azure にアップロードすることも、Azure Marketplace のイメージを使用することもできます。 ただし、これらのイメージには、アプリケーションと組織の要件を満たすセキュリティ制御が必要です。 オペレーティング システム、データ、SAP アプリケーション層には、お客様固有のセキュリティ制御を適用する必要があります。

一般に受け入れられているセキュリティガイダンスについては、「Internet Security (CI) センターにおける [サイバー セキュリティのベストプラクティス](https://www.cisecurity.org/cybersecurity-best-practices/)」を参照してください。

### <a name="enable-azure-security-center"></a>Azure Security Center を有効にする

ハブスポーク ネットワーク トポロジを使用する企業では、多くの場合、複数の Azure サブスクリプションにクラウド アーキテクチャ パターンをデプロイします。 以下のクラウド デプロイ図では、セキュリティ ギャップが赤枠で強調表示されています。 黄色の枠は、ワークロードとサブスクリプション間でネットワーク仮想アプライアンスを最適化する機会を示しています。

![セキュリティの問題があるエンタープライズ クラウド デプロイを示す図。](./media/security-issues.png)

[Azure Security Center](/azure/security-center/security-center-introduction) は、脅威に対する保護を提供し、企業のセキュリティ体制全体を総合的に把握できるようにします。

SAP on Azure サブスクリプションの Azure Security Center (ASC) Standard を有効にします。

- データセンターのセキュリティ体制を強化し、Azure や他のクラウド全体でオンプレミスとハイブリッドのワークロードに高度な脅威保護を提供します。

- SAP on Azure サブスクリプション間のすべてのセキュリティ体制を確認し、SAP VM、ディスク、アプリケーション間のリソース セキュリティの検疫をご覧ください。

- [Just-In-Time アクセス](/azure/security-center/just-in-time-explained)を使用して SAP 管理者カスタム役割を委任します。

SAP の Azure Security Center Standard を有効にする場合は、エンドポイント保護をインストールするすべてのポリシーから SAP データベースサーバーを除外してください。

次のスクリーンショットは、Azure portal の Azure Security Center ダッシュボードを示しています。

![Azure Security Center ダッシュボード](./media/security-center-dashboard.png)

### <a name="enable-azure-sentinel"></a>Azure Sentinel を有効にする

[Azure Sentinel](/azure/sentinel/overview) は、スケーラブルでクラウドネイティブのセキュリティ情報イベント管理 (SIEM) およびセキュリティ オーケストレーション自動応答 (SOAR) ソリューションです。 Azure Sentinel は、高度なセキュリティ分析と脅威インテリジェンスを企業全体で実現し、アラートの検出、脅威の可視性、予防的な捜索、および脅威への対応のための 1 つのソリューションを提供します。

### <a name="secure-authentication"></a>セキュリティで保護された認証

シングル サインオン (SSO) は、SAP と Microsoft 製品を統合するための基礎となります。 Active Directory からの Kerberos トークンとサードパーティのセキュリティ製品との組み合わせにより、SAP GUI と Web ブラウザー ベースのアプリケーションの両方で長年にわたってこの機能が有効になりました。 ユーザーがワークステーションにサインインし、正常に認証されると、Active Directory Kerberos トークンに発行されます。 サードパーティのセキュリティ製品では、Kerberos トークンを使用して、ユーザーが再認証することなく、SAP アプリケーションへの認証を処理します。

また、サードパーティのセキュリティ製品とセキュリティで保護されたネットワーク通信 (SNC) for DIAG (SAP GUI)、RFC、および SPNEGO を HTTPS 用に統合することで、ユーザーのフロントエンドから SAP アプリケーションへの転送中のデータを暗号化することもできます。

SAML 2.0 を使用する Azure Active Directory (Azure AD) は、SAP NetWeaver、SAP HANA、SAP Cloud Platform などのさまざまな SAP アプリケーションやプラットフォームに SSO を提供することもできます。

### <a name="harden-operating-systems"></a>オペレーティング システムの堅牢化

SAP データベースの攻撃につながる可能性がある脆弱性を根絶するように、オペレーティングシステムを強化してください。

いくつかの追加のチェックで、安全な基本インストールが保証されるようになります。

- システム ファイルの整合性を定期的に確認します。
- オペレーティング システムへのアクセスを制限します。
- サーバーへの物理アクセスを制限します。
- ネットワーク レベルでサーバーへのアクセスを保護します。

### <a name="isolate-virtual-networks"></a>仮想ネットワークを切り分ける

ネットワーク サービスとプロトコルへのアクセスを分離し、制限します。 より厳密な制御を行うために、いくつかの Azure セキュリティ メカニズムを使用して、推奨されるハブアンドスポーク ネットワーク アーキテクチャをセキュリティで保護することができます。

- 仮想ネットワーク ピアリングによってスポーク ネットワークに接続されているハブ仮想ネットワークを介してすべてのトラフィックを渡すことによって、SAP アプリケーションとデータベース サーバーをインターネットまたはオンプレミス ネットワークから分離します。 ピアリングされた仮想ネットワークは、SAP on Azure ソリューションがパブリック インターネットから分離されていることを保証します。

- Azure Monitor、 [Azure ネットワークセキュリティグループ (NSG)](/azure/virtual-network/network-security-groups-overview)、またはアプリケーション セキュリティ グループを使用して、トラフィックの監視とフィルター処理を行います。

- [Azure Firewall](/azure/firewall/overview)または市場で利用可能なネットワーク仮想アプライアンス (NVA) を使用します。

- ネットワーク セキュリティ対策の詳細については、 [ネットワーク DMZ](/azure/architecture/reference-architectures/dmz/secure-vnet-dmz)を実装してください。 詳細については、「[Azure とオンプレミス データセンターの間にDMZを実装する](/azure/architecture/reference-architectures/dmz/secure-vnet-dmz)」をご覧ください。

- 他の SAP 資産の他の部分から DMZ と NVA を分離し、Azure プライベート リンクを構成して、SAP on Azure リソースを安全に管理および制御します。

次のアーキテクチャ図は、NSG を使用してネットワーク サービスとプロトコルを分離および制限することで、Azure 仮想ネットワーク トポロジを管理する方法を示しています。 ネットワーク セキュリティが組織のセキュリティ ポリシー要件に準拠していることを確認します。

![SAP ネットワークのセキュリティ アーキテクチャを示す図。](./media/network-architecture.png)

SAP on Azure ネットワーク セキュリティの詳細については、「[Azure での SAP セキュリティ操作](https://blogs.sap.com/2019/07/21/sap-security-operations-on-azure/)」を参照してください。

### <a name="encrypt-data-at-rest"></a>保存データを暗号化

保存データには、物理メディア上の永続的ストレージに存在する、あらゆるデジタル形式の情報が含まれます。 これには、磁気メディアまたは光学メディア上のファイル、アーカイブされたデータ、データのバックアップなどが含まれます。 Azure には、ファイル、ディスク、BLOB、テーブルなどのさまざまなデータ ストレージ ソリューションが用意されています。 保存時の Azure Storage データ暗号化には、既定でオプションの顧客構成が使用されます。 詳細については、「保存 [時の Azure データの暗号化](/azure/security/fundamentals/encryption-atrest) 」と「[Azure の暗号化の概要](/azure/security/fundamentals/encryption-overview)」を参照してください。

SAP on Azure のサーバーサイド暗号化 (SSE) によってデータが保護され、組織のセキュリティおよびコンプライアンス コミットメントを満たすのに役立ちます。 SSE は、クラウドにデータを保持するときに、Azure で管理される OS およびデータ ディスク上の保存データを自動的に暗号化します。 SSE は、Azure マネージド ディスク内のデータを、利用できる最も強力なブロック暗号の 1 つである 256 ビット [AES 暗号化](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)を使って透過的に暗号化します。これは FIPS 140-2 に準拠しています。 SSE は、マネージド ディスクのパフォーマンスには影響しません。追加のコストはかかりません。 Azure マネージド ディスクの基になっている暗号化モジュールについて詳しくは、「[暗号化 API:次世代](/windows/win32/seccng/cng-portal)」を参照してください。

Azure Storage 暗号化は、Resource Manager と従来のストレージ アカウントの両方を含む、すべてのストレージ アカウントで有効になります。 データは既定で保護されるので、Azure Storage 暗号化を利用するために、コードまたはアプリケーションを変更する必要はありません。

SAP データベース サーバーの暗号化には、SAP HANA ネイティブ暗号化テクノロジを使用します。 Azure SQL Database を使用している場合は、DBMS プロバイダーによって提供される[ Transparent Data Encryption (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption) を使用して、データとログのファイルをセキュリティで保護し、バックアップも暗号化されるようにします。

### <a name="secure-data-in-transit"></a>転送中のデータを安全に保護する

データは、内部設置型の場所から別の場所に移動するときに、内部設置型または Azure 内であるか、またはインターネット経由でエンドユーザーに対して外部に移動するかにかかわらず、転送中または処理中です。 Azure には、データを転送中にプライベートに保つためのメカニズムがいくつか用意されています。 すべてのメカニズムで、暗号化などの保護方法を使用できます。 その例を次に示します。

- IPsec/IKE 暗号化を使用した仮想プライベートネットワーク (VPN) 経由の通信
- Azure Application Gateway やAzure Front Door などの Azure コンポーネントを使用したトランスポート層セキュリティ (TLS) 1.2 以降
- Azure VM で利用できるプロトコル (Windows IPsec や SMB など)

データリンク層の IEEE 標準である MACsec を使用した暗号化は、Azure データセンター間のすべての Azure トラフィックに対して自動的に有効になります。 この暗号化により、顧客データの機密性と整合性が確保されます。 詳細については、「[Azure 顧客データ保護](/azure/security/fundamentals/protection-customer-data)」を参照してください。

### <a name="manage-keys-and-secrets"></a>キーとシークレットの管理

HANA 以外の Windows オペレーティング システムおよび Windows 以外のオペレーティング システムのディスク暗号化キーとシークレットを制御および管理するには、Azure Key Vault を使用します。 Key Vault には、SSL/TLS 証明書をプロビジョニングおよび管理する機能があります。 また、ハードウェア セキュリティ モジュール (HSM) を使用してシークレットを保護することもできます。 SAP HANA は Azure Key Vault ではサポートされていないため、SAP ABAP や SSH キーなどの別の方法を使用する必要があります。

### <a name="secure-web-and-mobile-applications"></a>セキュリティで保護された Web アプリケーションとモバイル アプリケーション

SAP Fiori のようなインターネットに接続されたアプリケーションの場合は、セキュリティ レベルを維持しながら、アプリケーションの要件ごとに負荷を分散してください。 レイヤー 7 のセキュリティでは、Azure Marketplace で利用可能なサードパーティの Web アプリケーション ファイアウォール (WAF) を使用できます。

モバイル アプリの場合、 [Microsoft Enterprise Mobility + Security](https://www.microsoft.com/microsoft-365/enterprise-mobility-security) は、組織の保護とセキュリティ保護に役立ち、従業員が新しい柔軟な方法で作業できるようにするために、SAP インターネットに接続されたアプリケーションを統合できます。

### <a name="securely-manage-traffic"></a>トラフィックを安全に管理する

インターネットに接続するアプリケーションの場合は、セキュリティ レベルを維持しながら、アプリケーションの要件に従って負荷を分散させる必要があります。 負荷分散とは、複数のコンピューティング リソース間でのワークロードの分散を指します。 負荷分散は、リソースの使用を最適化し、スループットを最大化し、応答時間を最小化し、1 つのリソースの過負荷を回避することを目的としています。 負荷分散は、冗長なコンピューティング リソース間でワークロードを共有することで、可用性を向上させることもできます。

ロード バランサーは、アプリケーション サブネット内の仮想マシンにトラフィックを転送します。 高可用性を実現するために、この例では SAP Webディスパッチャと Azure Standard Load Balancer を使用しています。 この 2 つのサービスでは、スケールアウトによる容量拡張もサポートされています。また、トラフィックの種類と必要な機能 (Secure Sockets Layer (SSL) 終了や SSL 転送など) に応じて、Azure Application Gateway や他のパートナー製品を使用できます。

Azure の負荷分散サービスは、グローバルかリージョンか、また HTTP(S) か非 HTTP(S) かという 2 つの次元で分類できます。

- グローバルな負荷分散サービスは、リージョンのバックエンド、クラウド、またはハイブリッド オンプレミス サービス間でトラフィックを分散します。 これらのサービスは、エンドユーザーのトラフィックを、最も近い使用可能なバックエンドにルーティングします。 サービスの信頼性またはパフォーマンスの変化にも反応することで、可用性とパフォーマンスも最大化します。 これらのサービスは、異なるリージョン/地理的地域にまたがってホストされているアプリケーションのスタンプ、エンドポイント、またはスケール ユニット間で負荷を分散するシステムと考えることができます。

- リージョンの負荷分散サービスは、仮想ネットワーク内のトラフィックを、リージョン内の仮想マシン (VM) 間、またはゾーンおよびゾーン冗長のサービス エンドポイント間で分散します。 これらのサービスは、仮想ネットワークにあるリージョン内の VM、コンテナー、またはクラスター間で負荷を分散するシステムと考えることができます。

- HTTP/S 負荷分散サービスは、HTTP/S トラフィックのみを受け入れるレイヤー7ロードバランサーであり、Web アプリケーションやその他の HTTP/S エンドポイントを対象としています。 HTTP/S 負荷分散サービスには、SSL オフロード、WAF、パスベースの負荷分散、セッション アフィニティなどの機能が含まれています。

- 非 HTTP/S の負荷分散サービスは、HTTP(S) 以外のトラフィックを処理でき、Web 以外のワークロードに推奨されます。

次の表は、これらのカテゴリ別に Azure の負荷分散サービスをまとめたものです。

| サービス | グローバルまたはリージョン | 推奨されるトラフィック |
|---|---|---|
| Azure Front Door | グローバル | HTTP/S |
| Traffic Manager | グローバル | HTTP/S 以外 |
| Application Gateway | 地域 | HTTP/S |
| Azure Load Balancer | 地域 | HTTP/S 以外 |

- [Front Door](/azure/frontdoor/front-door-overview) は、Web アプリケーション向けのグローバル負荷分散およびサイト アクセラレーション サービスを提供するアプリケーション配信ネットワークです。 Front Door は、SSL オフロード、パスベースのルーティング、高速フェールオーバー、キャッシュなどのレイヤー 7 機能を提供して、アプリケーションのパフォーマンスと可用性を向上させます。

- [Traffic Manager](/azure/traffic-manager/traffic-manager-overview) は、世界中の Azure リージョン間でサービスへのトラフィックを最適に配分しつつ、高可用性と応答性を実現する DNS ベースのトラフィック ロード バランサーです。 Traffic Manager は DNS ベースの負荷分散サービスであるため、負荷分散はドメイン レベルでのみ行われます。 そのため、DNS キャッシュに関連した、また DNS TTL を遵守しないシステムに関連した一般的な課題が理由で、Front Door ほど高速なフェールオーバーはできません。

- [Application Gateway](/azure/application-gateway/overview) は、さまざまなレイヤー 7 負荷分散機能で管理されたアプリケーション配信コントローラーを利用できるようにします。 Application Gateway を使用すれば、CPU を集中的に使用する SSL 終了を ゲートウェイにオフロードして、Web ファームの生産性を最適化できます。

- [Azure Load Balancer](/azure/load-balancer/load-balancer-overview) は、すべての UDP と TCP プロトコル向けの高パフォーマンス、超低待機時間のレイヤー 4 負荷分散サービス (受信および送信) です。 Load Balancer は 1 秒あたり数百万の要求を処理します。 Azure Load Balancer は、ゾーン冗長であるため、Availability Zones 全体で高可用性を確保します。

SAP on Azure アプリケーションの負荷分散の決定には、次のデシジョン ツリーを参照してください。

![Azure での負荷分散のデシジョン ツリー。](./media/load-balancer-decision-tree.png)

すべての SAP アプリケーションには固有の要件があるため、より詳細な評価を行うために、前のフローチャートと推奨事項を開始点として扱います。 SAP アプリケーションが複数のワークロードで構成されている場合は、それぞれのワークロードを個別に評価します。 完全なソリューションに、複数の負荷分散ソリューションを組み込むことができます。

### <a name="monitor-security"></a>セキュリティを監視する

[SAP ソリューションの Azure Monitor](/azure/virtual-machines/workloads/sap/azure-monitor-overview) は、 [SAP on Azure の仮想マシン](/azure/virtual-machines/workloads/sap/hana-get-started)と [HANA インスタンス](/azure/virtual-machines/workloads/sap/hana-overview-architecture)の両方で動作する SAP ランドスケープの Azure ネイティブ監視製品です。 SAP ソリューション向け Azure Monitor を使用すると、Azure インフラストラクチャとデータベースからの利用統計情報を 1 か所で収集し、迅速なトラブルシューティングのために利用統計情報を視覚的に関連付けることができます。

### <a name="security-scoping-decisions"></a>セキュリティ スコープの決定

次の推奨事項は、さまざまなセキュリティ シナリオに対応しています。 スコープ内の要件は、セキュリティ ソリューションがコスト効率と拡張性に優れていることを前提としています。

| スコープ (シナリオ) | 推奨 | Notes |
|---|---|---|
| すべての Azure とオンプレミスのセキュリティ体制の統合ビューをご覧ください。 | Azure Security Center の標準 | Azure Security Center Standard は、オンプレミスとクラウドから Windows マシンと Linux マシンをオンボードするのに役立ち、統合されたセキュリティ体制を示しています。 |
| すべての SAP on Azure データベースを暗号化して、規制要件に対応します。 | ネイティブ暗号化と SQL TDE の SAP HANA | データベースの場合は、SAP HANA ネイティブ暗号化テクノロジを使用します。 SQL Database を使用している場合は、TDE を有効にします。 |
| HTTPS トラフィックを持つグローバルユーザー用に SAP Fiori アプリケーションをセキュリティで保護します。 | Azure Front Door | Front Door は、Web アプリケーション向けのグローバル負荷分散およびサイト アクセラレーション サービスを提供するアプリケーション配信ネットワークです。 |

## <a name="compliance-and-governance-design-recommendations"></a>コンプライアンスとガバナンスの設計に関する推奨事項

[Azure Advisor](/azure/advisor/advisor-overview) は無料で、SAP on Azure サブスクリプション間で統合されたビューを取得するのに役立ちます。 信頼性、回復性、セキュリティ、パフォーマンス、コスト、および運用上の優れた設計に関する推奨事項については、Azure Advisor の推奨事項を参照してください。

### <a name="use-azure-resource-naming-and-tagging-conventions"></a>Azure リソースの名前付け規則とタグ付け規則を使用する

クラウド導入フレームワークの [準備メソドロジ](../../ready/index.md)に合わせるには、リソース名とメタデータ タグのコンポーネントとして、ビジネスおよび操作上の詳細を含む名前付けとタグ付けの戦略を実装します。

この戦略のビジネス面では、リソースの名前とタグに、チームを識別するために必要な組織情報が確実に含まれるようにします。 リソース コストを担当するビジネス オーナーと共にリソース名を使用します。 運用の面では、名前とタグに、ワークロード、アプリケーション、環境、重要度、およびリソースの管理に役立つその他の情報を識別するために IT チームが使用する情報が確実に含まれるようにします。

名前を付けるリソースには、VM、ロードバランサー、DNS ラベル、可用性セット、仮想ネットワーク、サブネット、ExpressRoute、NSGs、アプリケーション セキュリティ グループ、タグ、ルートテーブル、マネージド ディスク、パブリック IP などがあります。 サンプルのユース ケースでは、すべての Azure 開発 VM にタグ`Dev` を付けることができます。 これにより、レポートと請求操作が容易になり、すべての処理`Dev`についてレポートを取得できます。 詳細については、「[Azure リソースの名前付けおよびタグ付けの戦略を作成する](../../ready/azure-best-practices/naming-and-tagging.md)」を参照してください。

### <a name="use-azure-policy"></a>Azure Policy を使用する

Azure Policy は、コンプライアンスのダッシュボードを通じて組織の標準を適用し、コンプライアンスを大規模に評価するのに役立ちます。 Azure Policy は、リソースごと、およびポリシーごとの粒度でドリルダウンできる機能が備わっており、環境の全体的な状態を評価するための集計ビューを提供します。

Azure Policy は、既存のリソースの一括修復と新しいリソースの自動修復を使用して、お客様のリソースでコンプライアンスを実現するのにも役立ちます。 サンプルの Azure ポリシーでは、許可された場所を管理グループに適用し、タグとリソースの値を要求し、マネージド ディスクを使用して VM を作成するか、名前付けポリシーを使用します。

### <a name="manage-sap-on-azure-costs"></a>SAP on Azure コストを管理する

コスト管理は非常に重要です。 Microsoft では、予約、適切なサイズ変更、スヌーズなど、コストを最適化するためのさまざまな方法を提供しています。 [Azure のコスト使用制限](/azure/cost-management-billing/manage/spending-limit)に関するアラートを理解し、設定することが重要です。 この監視を拡張して、全体的な IT サービス管理 (ITSM) ソリューションと統合することができます。

### <a name="automate-sap-deployments"></a>SAP デプロイの自動化

SAP のデプロイを自動化して時間を節約し、エラーを削減します。 複雑な SAP ランドスケープをパブリック クラウドにデプロイすることは簡単な作業ではありません。 SAP の基本的なチームは、オンプレミスの SAP システムをインストールして構成する従来のタスクに精通している場合があります。 クラウド デプロイの設計、構築、テストには、多くの場合、追加のドメイン ナレッジが必要です。 詳細については、「[SAP エンタープライズ規模のプラットフォームのオートメーションと DevOps](./eslz-platform-automation-and-devops.md)」を参照してください。

### <a name="lock-resources-for-production-workloads"></a>運用環境のワークロードのリソースをロックする

SAP プロジェクトの開始時に必要な Azure リソースを作成します。 すべての追加、移動、および変更が完了し、SAP on Azure のデプロイが動作している場合は、すべてのリソースをロックします。 スーパー管理者のみがロックを解除し、VM などのリソースを変更できるようにすることができます。 詳細については、「[リソースのロックによる予期せぬ変更の防止](/azure/azure-resource-manager/management/lock-resources)」を参照してください。

### <a name="implement-role-based-access-control"></a>ロールベースのアクセス制御を実装する方法

SAP on Azure スポーク サブスクリプションのロールベースのアクセス制御 (RBAC) ロールをカスタマイズして、誤ったネットワーク関連の変更を回避します。 SAP on Azure インフラストラクチャ チームのメンバーが Azure 仮想ネットワークに VM をデプロイし、ハブサブスクリプションにピアリングされた仮想ネットワーク上で何も変更できないようにすることができます。 一方、ネットワーク チームのメンバーは、仮想ネットワークの作成と構成を行うことができますが、SAP アプリケーションが実行されている仮想ネットワーク内の VM のデプロイや構成は禁止されています。

### <a name="use-azure-connector-for-sap-lama"></a>SAP LaMa 用の Azure コネクタの使用

一般的な SAP 資産では、多くの場合、ERP、SCM、BW など、いくつかのアプリケーション ランドスケープがデプロイされ、SAP システムのコピーと SAP システムの更新を実行する必要があります。 たとえば、技術またはアプリケーションのリリース用に新しい SAP プロジェクトを作成したり、運用環境のコピーから QA システムを定期的に更新したりすることができます。 SAP システムのコピーと更新を行うためのエンドツーエンドのプロセスには、時間と労力がかかることがあります。

SAP ランドスケープ管理 (LaMa) のエンタープライズ エディションでは、SAP システムのコピーまたは更新に関連するいくつかの手順を自動化することで、運用効率をサポートできます。 [Azure Connector For LaMa](/azure/virtual-machines/workloads/sap/lama-installation) を使用すると、Azure でマネージド ディスクのコピー、削除、再配置が可能になり、SAP オペレーション チームが SAP システムのコピーやシステムの更新を迅速に実行し、手動による作業を減らすことができます。

VM 操作の場合、Azure Connector for LaMa を使用すると、Azure での SAP 資産の実行コストを削減できます。 SAP VM を停止するか、割り当てを解除して開始することができます。これにより、使用率の低いプロファイルで特定のワークロードを実行できます。 たとえば、LaMa インターフェイスを使用すると、24 時間ではなく、SAP S/4HANA サンドボックス VM が 08:00～18:00 から、1 日に 10 時間、オンラインになるようにスケジュールできます。 また、Azure Connector for LaMa では、LaMa 内からパフォーマンスの需要が直接発生した場合に VM のサイズを変更することもできます。

### <a name="compliance-and-governance-scoping-decisions"></a>コンプライアンスとガバナンスのスコープの決定

次の推奨事項は、さまざまなコンプライアンスとガバナンスのシナリオに適用されます。 スコープ内の要件は、ソリューションがコスト効率と拡張性に優れていることを前提としています。

| スコープ (シナリオ) | 推奨 | Notes |
|---|---|---|---|
| 標準の名前付け規則のガバナンス モデルを構成し、コスト センターに基づいてレポートをプルします。 | Azure Policy と Azure タグ | Azure Policy とタグ付けを使用して、要件を満たすことができます。 |
| Azure リソースを誤って削除しないようにします。 | Azure のリソース ロック | Azure リソース ロックが原因で、リソースを誤って削除することはできません。 |
| コストの最適化、回復性、セキュリティ、オペレーショナル、および SAP on Azure リソースのパフォーマンスに関する営業案件の領域の統合ビューを取得します | Azure Advisor | Azure Advisor は無料で、SAP on Azure サブスクリプション間で統合されたビューを取得するのに役立ちます。 |

## <a name="next-steps"></a>次の手順

- [Azure セキュリティの概要](/azure/security/fundamentals/overview)
- [SAP on Azure アーキテクチャ ガイド](/azure/architecture/reference-architectures/sap/sap-overview)
- [Azure での SAP ワークロードの計画とデプロイに関するチェックリスト](/azure/virtual-machines/workloads/sap/sap-deployment-checklist)
- [SAP アプリケーションを Azure に移行する際のベスト プラクティス - パート1](https://azure.microsoft.com/blog/best-practices-in-migrating-sap-applications-to-azure-part-1/)
- [SAP on Azure: 効率と操作のための設計](https://azure.microsoft.com/blog/sap-on-azure-designing-for-efficiency-operations/)
- [SAP NetWeaver のための Azure Virtual Machines の計画と実装](/azure/virtual-machines/workloads/sap/planning-guide)
