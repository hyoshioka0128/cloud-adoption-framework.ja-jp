---
title: 境界ネットワーク
description: Azure 向けのクラウド導入フレームワークを使用して、組織に合った効果的な Azure のセットアップ方法を学習します。
author: tracsman
ms.author: jonor
ms.date: 05/10/2019
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: ready
manager: rossort
ms.custom: virtual-network
ms.openlocfilehash: ae5506d6d09289b2c41cd5ece887b2ec5692c267
ms.sourcegitcommit: a7eb2f6c4465527cca2d479edbfc9d93d1e44bf1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2020
ms.locfileid: "94712852"
---
# <a name="perimeter-networks"></a>境界ネットワーク

[境界ネットワーク][perimeter-network]により、クラウド ネットワークと物理的なオンプレミスのデータセンターのネットワークの間で安全な接続が可能になり、また、インターネットの双方向接続も可能になります。 境界ネットワークは、スクリーン サブネットまたは DMZ と呼ばれることもあります。

境界ネットワークを有効にするには、受信パケットは、バック エンド サーバーに到達する前に、セキュア サブネットでホストされているセキュリティ アプライアンスを通過する必要があります。 例としては、ファイアウォール、侵入検出システム、侵入防止システムなどがあります。 ワークロードからインターネットへのパケットも、ネットワークから送信される前に、境界ネットワーク内のセキュリティ アプライアンスを通過する必要があります。 このフローは、ポリシーの適用、検査、監査を目的としています。

境界ネットワークは、次の Azure の機能とサービスを利用します。

- [仮想ネットワーク][virtual-networks]、[ユーザー定義ルート][user-defined-routes]、および[ネットワーク セキュリティ グループ][network-security-groups]
- [ネットワーク仮想アプライアンス (NVA)][network-virtual-appliances]
- [Azure Load Balancer][alb]
- [Azure Application Gateway][appgw] と [Web アプリケーション ファイアウォール (WAF)][appgwwaf]
- [パブリック IP][PIP]
- [Azure Front Door][afd] と [Web アプリケーション ファイアウォール][afdwaf]
- [Azure Firewall][Azure-firewall]

> [!NOTE]
> Azure の参照アーキテクチャには、独自の境界ネットワークを実装するために使用できるサンプル テンプレートが含まれています。
>
> - [Azure とオンプレミス データセンター間に境界ネットワークを実装する](/azure/architecture/reference-architectures/dmz/secure-vnet-dmz)
> - [Azure とインターネット間に境界ネットワークを実装する](/azure/architecture/reference-architectures/dmz/secure-vnet-dmz?toc=/azure/cloud-adoption-framework/toc.json&bc=/azure/cloud-adoption-framework/_bread/toc.json)

通常、中央 IT チームとセキュリティ チームは、境界ネットワークを運用するための要件定義を担当します。

![ハブ アンド スポーク ネットワーク トポロジの例](../../_images/azure-best-practices/network-high-level-perimeter-networks.png)
_図 1:ハブ アンド スポーク ネットワーク トポロジの例_

上の図では、インターネットとオンプレミス ネットワークにアクセスする 2 つの境界の適用を実装する[ハブ アンド スポーク ネットワーク](./hub-spoke-network-topology.md)の例が示されています。 どちらの境界も DMZ ハブに存在します。 DMZ ハブでは、WAF と Azure Firewall インスタンスからなるファームを複数利用してスポーク仮想ネットワークを保護すると、多数の基幹業務をサポートするよう、インターネットへの境界ネットワークをスケールアップできます。 ハブは、必要に応じて VPN または Azure ExpressRoute 経由で接続することもできます。

## <a name="virtual-networks"></a>仮想ネットワーク

通常、境界ネットワークは複数のサブネットを含む[仮想ネットワーク][virtual-networks]を使用して構築され、NVA、WAF、Azure Application Gateway インスタンスを介してインターネットとの間でやりとりされるトラフィックをフィルター処理および検査するさまざまな種類のサービスをホストします。

## <a name="user-defined-routes"></a>ユーザー定義のルート

[ユーザー定義ルート][user-defined-routes]を使用すると、顧客はファイアウォール、侵入検出システム、侵入防止システム、およびその他の仮想アプライアンスをデプロイでき ます。 お客様はこれらのセキュリティ アプライアンスを経由してネットワーク トラフィックをルーティングして、セキュリティ境界ポリシーを適用、監査、検査できます。 ユーザー定義ルートを作成して、特定のカスタム VM、NVA、ロード バランサーをトラフィックが通過することが保証されるようにすることができます。

ハブ アンド スポーク ネットワークの例で、スポーク内に存在する仮想マシンによって生成されたトラフィックがハブ内の正しい仮想アプライアンスを通過することが保証されるようにするには、ユーザー定義ルートをスポークのサブネット内で定義することが必要です。 このルートは、内部ロード バランサーのフロントエンド IP アドレスを次ホップとして設定します。 内部ロード バランサーは、内部トラフィックを仮想アプライアンス (ロード バランサーのバックエンド プール) に分散させます。

## <a name="azure-firewall"></a>Azure Firewall

[Azure Firewall][Azure-firewall] とは、Azure Virtual Network リソースの保護に役立つクラウドベースのマネージド サービスです。 これは、組み込みの高可用性とクラウドによる無制限のスケーラビリティを備えた、完全にステートフルなマネージド ファイアウォールです。 サブスクリプションと仮想ネットワークをまたいでアプリケーションとネットワークの接続ポリシーを一元的に作成、適用、記録できます。

Azure Firewall では、仮想ネットワーク リソースに静的パブリック IP アドレスが使用されます。 これにより、仮想ネットワークから送信されるトラフィックを外部のファイアウォールで識別できます。 サービスはログ記録と分析を行うために Azure Monitor と相互運用されます。

## <a name="network-virtual-appliances"></a>ネットワーク仮想アプライアンス

インターネットにアクセスする境界ネットワークは、通常、Azure Firewall インスタンスあるいはファイアウォールまたは [Web アプリケーション ファイアウォール][afdwaf]のファームによって管理されます。

一般に、さまざまな基幹業務で多くの Web アプリケーションが使用されます。 これらのアプリケーションは、さまざまな脆弱性や潜在的な悪用の影響を受ける傾向があります。 Web アプリケーション ファイアウォールでは、Web アプリケーション (HTTP/S) に対する攻撃が汎用ファイアウォールよりも詳細に検出されます。 従来のファイアウォール テクノロジと比較すると、Web アプリケーション ファイアウォールは脅威から内部 Web サーバーを保護するのに役立つ特定の機能セットを備えています。

Azure Firewall と "ネットワーク仮想アプライアンス" (NVA) ファイアウォールでは共通管理プレーンが使用され、一連のセキュリティ規則により、スポークでホストされているワークロードが保護され、オンプレミスのネットワークへのアクセスを制御するのに役立ちます。 Azure Firewall にはスケーラビリティが組み込まれているのに対して、NVA ファイアウォールはロード バランサーの背後で手動でスケーリングできます。

ファイアウォール ファームは一般的に、WAF ほど特化したソフトウェアではありませんが、エグレスおよびイングレスのあらゆる種類のトラフィックをフィルター処理して検査する、より広範なアプリケーション スコープを備えています。 NVA アプローチを使用する場合は、Azure Marketplace からソフトウェアを検索してデプロイできます。

インターネットから送信されるトラフィックとオンプレミスから送信されるトラフィックには、それぞれ異なる Azure Firewall インスタンス (または NVA) のセットを使用してください。 両方にファイアウォールの同じセットを使用すると、2 つのネットワーク トラフィック セットの間にセキュリティ境界が提供されないため、セキュリティ リスクになります。 個別のファイアウォール レイヤーを使用すると、セキュリティ規則のチェックの複雑さが軽減され、規則と受信ネットワーク要求の対応が明確になります。

## <a name="azure-load-balancer"></a>Azure Load Balancer

[Azure Load Balancer][alb] が提供する高可用性レイヤー 4 (TCP/UDP) サービスは、負荷分散セットで定義されているサービス インスタンス間に受信トラフィックを分散できます。 フロントエンド エンドポイント (パブリック IP エンドポイントまたはプライベート IP エンドポイント) からロード バランサーに送信されたトラフィックは、アドレス変換をして、またはしないで、バックエンド IP アドレスのプール (NVA または VM など) に再配信できます。

Azure Load Balancer では、さまざまなサーバー インスタンスの正常性をプローブすることもできます。 インスタンスがプローブに応答しない場合、ロード バランサーは異常なインスタンスへのトラフィックの送信を停止します。

ハブ アンド スポーク ネットワーク トポロジを使用する例としては、ハブとスポークの両方に外部ロード バランサーをデプロイできます。 ハブでは、ロード バランサーによってスポーク内のサービスにトラフィックが効率的にルーティングされます。 スポークでは、ロード バランサーによってアプリケーション トラフィックが管理されます。

## <a name="azure-front-door"></a>Azure Front Door

[Azure Front Door][afd] は、Microsoft による高可用性かつスケーラブルな Web アプリケーション アクセラレーション プラットフォームとグローバル HTTPS ロード バランサーです。 Azure Front Door を使用し、動的 Web アプリケーションと静的コンテンツを構築、運用、スケールアウトできます。 Microsoft のグローバル ネットワークのエッジにある 100 を超える場所で実行されます。

Azure Front Door サービスから、リージョナル/スタンプ メンテナンス自動化、BCDR 自動化、統合クライアント/ユーザー情報、キャッシュ、サービス分析情報がアプリケーションに提供されます。 プラットフォームからは、パフォーマンス、信頼性、サポート SLA が提供されます。 また、コンプライアンス認定資格と、Azure でネイティブに開発、運用、サポートされる監査可能なセキュリティ プラクティスが提供されます。

## <a name="azure-application-gateway"></a>Azure Application Gateway

[Azure Application Gateway][appgw] とは、マネージド アプリケーション配信コントローラーを提供する専用の仮想アプライアンスです。 これは、レイヤー 7 のさまざまな負荷分散機能をお客様のアプリケーションに提供します。

<!-- docutune:casing "the application gateway" "single application gateway" -->

CPU 負荷の高い SSL ターミネーションを Azure Application Gateway にオフロードすることによって、Web ファームの生産性を最大限に高めることができます。 また、着信トラフィックのラウンド ロビン分散、Cookie ベースのセッション アフィニティ、URL パス ベースのルーティング、単一のアプリケーション ゲートウェイの背後で複数の Web サイトをホストする機能など、レイヤー 7 のその他のルーティング機能も用意されています。

Azure Application Gateway の WAF SKU には、Web アプリケーション ファイアウォールが含まれています。 この SKU は、一般的な Web の脆弱性や悪用から Web アプリケーションを保護します。 Azure Application Gateway は、インターネットに接続するゲートウェイ、内部のみのゲートウェイ、あるいは両方の組み合わせとして構成できます。

## <a name="public-ips"></a>パブリック IP

Azure の一部の機能を使用して、インターネットからリソースにアクセスできるように、サービス エンドポイントを[パブリック IP][PIP] アドレスに関連付けることができます。 このエンドポイントでは、ネットワーク アドレス変換 (NAT) を使用して、Azure Virtual Network 上の内部アドレスとポートにトラフィックをルーティングします。 これは、外部トラフィックが仮想ネットワークに到達するための主な経路です。 パブリック IP アドレスを構成すると、仮想ネットワークに渡されるトラフィックと、そのトラフィックが仮想ネットワークのどこでどのように変換されるのかを決定できます。

## <a name="azure-ddos-protection-standard"></a>Azure DDoS Protection Standard

[Azure DDoS Protection Standard][DDoS] は、[Basic サービス][DDoS] レベルに加えて、特に Azure Virtual Network リソースに対してチューニングされた追加の軽減機能を提供します。 DDoS Protection Standard の有効化は簡単であり、アプリケーションの変更は不要です。

保護ポリシーは、専用のトラフィック監視や機械学習アルゴリズムによってチューニングできます。 ポリシーは、仮想ネットワークにデプロイされたリソースに関連付けられているパブリック IP アドレスに適用されます。 例として、Azure Load Balancer、Application Gateway、および Service Fabric のインスタンスがあります。

攻撃中および履歴目的の両方で使用するために、Azure Monitor ビューからリアルタイムのテレメトリを使用できます。 アプリケーション層の保護は、Azure Application Gateway の Web アプリケーション ファイアウォールを使用することによって追加できます。 IPv4 の Azure パブリック IP アドレスに対して保護が提供されます。

<!-- links -->

[Virtual-networks]: /azure/virtual-network/virtual-networks-overview
[Network-security-groups]: /azure/virtual-network/virtual-networks-nsg
[User-defined-routes]: /azure/virtual-network/virtual-networks-udr-overview
[Network-virtual-appliances]: /azure/architecture/reference-architectures/dmz/nva-ha
[Azure-firewall]: /azure/firewall/overview
[Perimeter-network]: /azure/best-practices-network-security
[Alb]: /azure/load-balancer/load-balancer-overview
[DDoS]: /azure/virtual-network/ddos-protection-overview
[PIP]: /azure/virtual-network/virtual-network-public-ip-address
[Afd]: /azure/frontdoor/front-door-overview
[Afdwaf]: /azure/frontdoor/waf-overview
[Appgw]: /azure/application-gateway/application-gateway-introduction
[Appgwwaf]: /azure/application-gateway/application-gateway-web-application-firewall-overview
