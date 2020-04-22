---
title: ネットワーク オプションを確認する
description: Azure ワークロードをサポートするためにランディング ゾーンで必要となるネットワーク機能の特定方法を、Azure 向けのクラウド導入フレームワークを使用して学習します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 05/15/2019
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.openlocfilehash: bc14bb03f048ab335cd836c6a4a520b8df0ccad8
ms.sourcegitcommit: 7d3fc1e407cd18c4fc7c4964a77885907a9b85c0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2020
ms.locfileid: "80997970"
---
<!-- cSpell:ignore paas NVAs VPNs -->

# <a name="review-your-network-options"></a>ネットワーク オプションを確認する

Azure のネットワーク機能の設計と実装は、クラウド導入作業の重要な部分です。 クラウドでホストされたワークロードやサービスを適切にサポートするには、ネットワーク設計に関する意思決定を行う必要があります。 Azure のネットワーク製品とサービスは、さまざまなネットワーク機能をサポートしています。 これらのサービスと選択したネットワーク アーキテクチャをどのように構成するかは、組織のワークロード、ガバナンス、および接続の要件によって異なります。

## <a name="identify-workload-networking-requirements"></a>ワークロードのネットワーク要件を特定する

ランディング ゾーンの評価と準備の一部として、ランディング ゾーンでサポートする必要があるネットワーク機能を特定する必要があります。 このプロセスには、ワークロードを構成する各アプリケーションやサービスを評価して、それらの接続ネットワーク制御の要件を特定する作業が含まれます。 これらの要件を特定して文書化した後、ワークロードのニーズに基づいて、許可されたネットワーク リソースおよび構成を制御するためのランディング ゾーンのポリシーを作成できます。

ランディング ゾーン環境にデプロイするアプリケーションまたはサービスごとに、使用するネットワーク ツールまたはサービスを決定するために役立つ開始点として次のデシジョン ツリーを使用してください。

![Azure ネットワーク サービスのデシジョン ツリー](../../_images/ready/network-decision-tree.png)

### <a name="key-questions"></a>主な質問

Azure ネットワーク サービスのデシジョン ツリーに基づいた意思決定に役立てるために、ワークロードに関する次の質問に答えてください。

- **ワークロードには仮想ネットワークが必要ですか?** マネージド PaaS (サービスとしてのプラットフォーム) のリソースの種類では、必ずしも仮想ネットワークを必要としない基になるプラットフォーム ネットワーク機能を使用します。 ワークロードが高度なネットワーク機能を必要とせず、サービスとしてのインフラストラクチャ (IaaS) リソースをデプロイする必要がない場合は、既定の [PaaS リソースによって提供されるネイティブなネットワーク機能](../../decision-guides/software-defined-network/paas-only.md)がワークロード接続とトラフィック管理の要件を満たす可能性があります。
- **ワークロードには仮想ネットワークとオンプレミスのデータセンターの間の接続が必要ですか?** Azure には、ハイブリッド ネットワーク機能を確立するためのソリューションとして Azure VPN Gateway と Azure ExpressRoute の 2 つが用意されています。 [Azure VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) は、リモート ブランチ オフィスを設定してそこに接続する場合と同様に、サイト間 VPN 経由でオンプレミス ネットワークを Azure に接続します。 VPN Gateway は 10 Gbps の最大帯域幅を備えています。 [Azure ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) は、Azure とオンプレミス インフラストラクチャの間のプライベート接続を使用して、より高い信頼性とより短い待機時間を提供します。 ExpressRoute の帯域幅オプションは 50 Mbps ～ 100 Gbps です。
- **オンプレミスのネットワーク デバイスを使用して送信トラフィックを検査および監査する必要がありますか?** クラウド ネイティブなワークロードの場合は、[Azure Firewall](https://docs.microsoft.com/azure/firewall/overview) またはクラウドでホストされたサードパーティの[ネットワーク仮想アプライアンス (NVA)](https://azure.microsoft.com/solutions/network-appliances) を使用して、パブリック インターネットとの間で送受信されるトラフィックを検査および監査できます。 ただし、多くのエンタープライズ IT セキュリティ ポリシーでは、インターネットへの送信トラフィックが組織のオンプレミス環境内の一元管理されたデバイスを通過する必要があります。 [強制トンネリング](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview)は、これらのシナリオをサポートします。 すべてのマネージド サービスが強制トンネリングをサポートしているわけではありません。 [Azure App Service 内の App Service Environment](https://docs.microsoft.com/azure/app-service/environment/intro)、[Azure API Management](https://docs.microsoft.com/azure/api-management/api-management-key-concepts)、[Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/intro-kubernetes)、[Azure SQL Database 内のマネージド インスタンス](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index)、[Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/what-is-azure-databricks)、[Azure HDInsight](https://docs.microsoft.com/azure/hdinsight) などのサービスおよび機能は、このサービスまたは機能が仮想ネットワーク内にデプロイされている場合にこの構成をサポートします。
- **複数の仮想ネットワークを接続する必要がありますか?** [仮想ネットワーク ピアリング](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)を使用して、[Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) の複数のインスタンスを接続できます。 ピアリングはサブスクリプション間およびリージョン間の接続をサポートできます。 複数のサブスクリプション間で共有されるサービスを提供するか、または多数のネットワーク ピアリングを管理する必要があるシナリオの場合は、[ハブ アンド スポーク ネットワーク アーキテクチャ](../../decision-guides/software-defined-network/hub-spoke.md)を導入するか、または [Azure Virtual WAN](https://docs.microsoft.com/azure/virtual-wan/virtual-wan-about) を使用することを検討してください。 仮想ネットワーク ピアリングは、2 つのピアリングされたネットワーク間の接続のみを提供します。 既定では、複数のピアリングにまたがる推移的な接続は提供されません。
- **インターネット経由でワークロードにアクセスできますか?** Azure には、アプリケーションやサービスへの外部アクセスの管理およびセキュリティ保護に役立つように設計された次のサービスが用意されています。
  - [Azure Firewall](https://docs.microsoft.com/azure/firewall/overview)
  - [ネットワーク アプライアンス](https://azure.microsoft.com/solutions/network-appliances)
  - [Azure Front Door Service](https://docs.microsoft.com/azure/frontdoor/front-door-overview)
  - [Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway)
  - [Azure の Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview)
- **カスタム DNS 管理をサポートする必要がありますか?** [Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview) は DNS ドメインのホスティング サービスです。 Azure DNS は、Azure インフラストラクチャを使用した名前解決を提供します。 ワークロードに、Azure DNS によって提供される機能を超えた名前解決が必要な場合は、追加のソリューションのデプロイが必要になることがあります。 ワークロードに Active Directory サービスも必要な場合は、[Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/overview) を使用して Azure DNS の機能を拡張することを検討してください。 機能を追加するために、[カスタムの IaaS 仮想マシンをデプロイ](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances)して要件をサポートすることもできます。

## <a name="common-networking-scenarios"></a>一般的なネットワーク シナリオ

Azure ネットワークは、さまざまなネットワーク機能を提供する複数の製品およびサービスで構成されています。 ネットワーク設計プロセスの一部として、ワークロードの要件を次の表のネットワーク シナリオと比較することにより、これらのネットワーク機能を提供するために使用できる Azure ツールまたはサービスを特定できます。

<!-- markdownlint-disable MD033 -->

| **シナリオ** | **ネットワーク製品またはサービス** |
| --- | --- |
| 仮想マシンから受信 VPN 接続までのすべてを接続するためのネットワーク インフラストラクチャが必要です。 | [Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network) |
| 受信および送信接続とアプリケーションまたはサービスへの要求のバランスをとる必要があります。 | [Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer) |
| アプリケーション サーバー ファームからの配信を最適化しながら、Web アプリケーション ファイアウォールでアプリケーションのセキュリティを向上させたいと考えています。 | [Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway) <br/>[Azure Front Door Service](https://docs.microsoft.com/azure/frontdoor) |
| 高パフォーマンスの VPN ゲートウェイ経由でインターネットを安全に使用して Azure Virtual Network にアクセスする必要があります。 | [Azure VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway) |
| すべてのドメイン ニーズに対して超高速の DNS 応答と超高可用性を確保したいと考えています。 | [Azure DNS](https://docs.microsoft.com/azure/dns) |
| アプリケーションや格納されたコンテンツからストリーミング ビデオまで、高帯域幅コンテンツの世界中の顧客への配信を高速化する必要があります。 | [Azure Content Delivery Network](https://docs.microsoft.com/azure/cdn) |
| Azure アプリケーションを DDoS 攻撃から保護する必要があります。 | [Azure DDoS Protection](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview) |
| トラフィックをグローバル Azure リージョンにまたがるサービスに最適に分散させながら、高可用性と応答性を提供する必要があります。 | [Azure の Traffic Manager](https://docs.microsoft.com/azure/traffic-manager)<br/>[Azure Front Door Service](https://docs.microsoft.com/azure/frontdoor) |
| Microsoft のクラウド サービスがオンプレミスの独自のデータセンターに存在するかのように、企業ネットワークからそれらのサービスにアクセスするプライベート ネットワーク接続を追加する必要があります。 | [Azure ExpressRoute](https://docs.microsoft.com/azure/expressroute) |
| 状態をネットワーク シナリオのレベルで監視および診断したいと考えています。 | [Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher) |
| 組み込みの高可用性、無制限のクラウド スケーラビリティ、およびメンテナンス不要を備えたネイティブなファイアウォール機能が必要です。 | [Azure Firewall](https://docs.microsoft.com/azure/firewall) |
| ビジネス オフィス、小売の場所、およびサイトを安全に接続する必要があります。 | [Azure Virtual WAN](https://docs.microsoft.com/azure/virtual-wan) |
| グローバルなマイクロサービス ベースの Web アプリケーションのためのスケーラブルで、かつセキュリティが強化された配信ポイントが必要です。 | [Azure Front Door Service](https://docs.microsoft.com/azure/frontdoor) |

<!-- markdownlint-enable MD033 -->

## <a name="choose-a-networking-architecture"></a>ネットワーク アーキテクチャを選択する

ワークロードをサポートするために必要な Azure のネットワーク サービスを特定したら、これらのサービスを組み合わせてランディング ゾーンのクラウド ネットワーク インフラストラクチャを提供するアーキテクチャも設計する必要があります。 クラウド導入フレームワークの[ソフトウェア定義ネットワーク意思決定ガイド](../../decision-guides/software-defined-network/index.md)では、Azure で使用される最も一般的なネットワーク アーキテクチャ パターンのいくつかに関する詳細が提供されています。

次の表は、これらのパターンでサポートされる主なシナリオをまとめたものです。

| **シナリオ**                                                                                                                                                                                                                                                                                                                        | **推奨されるネットワーク アーキテクチャ**                                                  |
|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------|
| ランディング ゾーンにデプロイされた Azure でホストされるワークロードはすべて完全に PaaS ベースであり、仮想ネットワークを必要とせず、また IaaS リソースを含むより広範囲なクラウド導入作業の一部でもありません。                                                                                                                        | [PaaS のみ](../../decision-guides/software-defined-network/paas-only.md)            |
| Azure でホストされるワークロードは、仮想マシンなどの IaaS ベースのリソースをデプロイするか、そうでない場合は仮想ネットワークを必要としますが、オンプレミス環境への接続は必要ありません。                                                                                                                                          | [クラウドネイティブ](../../decision-guides/software-defined-network/cloud-native.md)      |
| Azure でホストされるワークロードには、オンプレミス リソースへの制限付きアクセスが必要ですが、クラウド接続を信頼できないものとして扱う必要があります。                                                                                                                                                                                           | [クラウド DMZ](../../decision-guides/software-defined-network/cloud-dmz.md)            |
| Azure でホストされるワークロードでは、オンプレミス リソースへのアクセスに制限が必要であるため、クラウドとオンプレミス環境の間で成熟したセキュリティ ポリシーと安全な接続を実装することを計画します。                                                                                                                         | [ハイブリッド](../../decision-guides/software-defined-network/hybrid.md)                  |
| [Azure サブスクリプションの制限](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits)を超える可能性のある多数の VM とワークロードをデプロイして管理する必要があるか、サブスクリプション間でサービスを共有する必要があるか、またはロール、アプリケーション、アクセス許可の分離のためのよりセグメント化された構造が必要です。 | [ハブ アンド スポーク](../../decision-guides/software-defined-network/hub-spoke.md)        |
| 互いに接続したり、Azure に接続したりする必要のある多数のブランチ オフィスがあります。                                                                                                                                                                                                                                                       | [Azure Virtual WAN](https://docs.microsoft.com/azure/virtual-wan/virtual-wan-about) |

### <a name="azure-virtual-datacenter"></a>Azure 仮想データセンター

これらのアーキテクチャ パターンのいずれかの使用に加えて、エンタープライズ IT グループが大規模なクラウド環境を管理する場合は、Azure ベースのクラウド インフラストラクチャを設計するときに [Azure 仮想データセンターのガイダンス](../../reference/vdc.md)を参照することを検討してください。 組織が次の基準を満たしている場合、Azure 仮想データセンターは、ネットワーク、セキュリティ、管理、およびインフラストラクチャへの結合されたアプローチを提供します。

- 企業が、集中管理された監視および監査機能を必要とする規制コンプライアンスに従っている。
- クラウド資産が、10,000 個を超える IaaS VM または同等の規模の PaaS サービスで構成される。
- 開発者チームと運用チームをサポートするためのワークロードの俊敏なデプロイ機能を有効にしながら、共通のポリシーとガバナンスのコンプライアンスおよびコア サービスに対する集中的な IT 制御を維持する必要がある。
- 所属する業界が、各分野の深い専門知識 (たとえば、金融、石油およびガス、製造など) を必要とする複雑なプラットフォームに依存している。
- 初期段階の導入時であっても、既存の IT ガバナンス ポリシーに既存の機能とのより厳密な同等性が必要である。

## <a name="follow-azure-networking-best-practices"></a>Azure ネットワークのベスト プラクティスに従う

ネットワーク設計プロセスの一部として、次の記事を参照してください。

- [仮想ネットワークの計画](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm?toc=https://docs.microsoft.com/azure/cloud-adoption-framework/toc.json&bc=https://docs.microsoft.com/azure/cloud-adoption-framework/_bread/toc.json)。 分離、接続、場所の要件に基づいて仮想ネットワークを計画する方法について説明します。
- [Azure のネットワーク セキュリティのベスト プラクティス](https://docs.microsoft.com/azure/security/fundamentals/network-best-practices?toc=https://docs.microsoft.com/azure/cloud-adoption-framework/toc.json&bc=https://docs.microsoft.com/azure/cloud-adoption-framework/_bread/toc.json)。 ネットワーク セキュリティの強化に役立つ Azure のベスト プラクティスについて説明します。
- [Azure に移行されたワークロードのネットワークの設定に関するベスト プラクティス](https://docs.microsoft.com/azure/migrate/migrate-best-practices-networking?toc=https://docs.microsoft.com/azure/cloud-adoption-framework/toc.json&bc=https://docs.microsoft.com/azure/cloud-adoption-framework/_bread/toc.json)。 IaaS ベースおよび PaaS ベースのワークロードをサポートするために Azure のネットワークを実装する方法に関する追加のガイダンスを入手します。
