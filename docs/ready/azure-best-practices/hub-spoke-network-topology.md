---
title: ハブ アンド スポーク ネットワーク トポロジ
titleSuffix: Microsoft Cloud Adoption Framework for Azure
description: ハブおよびスポーク ネットワーク トポロジについて説明します。
author: tracsman
ms.author: jonor
ms.date: 05/10/2019
ms.topic: guide
ms.service: cloud-adoption-framework
ms.subservice: ready
manager: rossort
tags: azure-resource-manager
ms.custom: virtual-network
ms.openlocfilehash: 166c45feddd1b6e1ccc17b5301b99e91a3d18e0e
ms.sourcegitcommit: bf9be7f2fe4851d83cdf3e083c7c25bd7e144c20
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73566781"
---
# <a name="hub-and-spoke-network-topology"></a>ハブ アンド スポーク ネットワーク トポロジ

*ハブ アンド スポーク*は、一般的な通信またはセキュリティ要件をより効率的に管理するためのネットワーク モデルです。 Azure サブスクリプションの制限の回避にも役立ちます。 このモデルでは、次の懸念事項に対処します。

- **コストの削減と管理の効率**。 複数のワークロードで共有できるサービス (ネットワーク仮想アプライアンス (NVA) や DNS サーバーなど) を 1 か所に集めることで、IT は過剰なリソースと管理作業を最小限にすることができます。
- **サブスクリプションの制限の克服**。 大規模なクラウドベースのワークロードでは、単一の Azure サブスクリプション内で許可されるリソースよりも多くのリソースの使用が求められる場合があります。 さまざまなサブスクリプションから中央のハブへのワークロード仮想ネットワークのピアリングで、こうした制限を克服できます。 詳細については、[サブスクリプションの制限](https://docs.microsoft.com/azure/azure-subscription-service-limits)に関するページを参照してください。
- **懸念事項の分離**。 中央の IT チームとワークロード チームの間で個々 のワークロードをデプロイすることができます。

小さなクラウド資産は、このモデルによって追加で提供される構造と機能の恩恵を受けない場合があります。 しかし、大規模なクラウド導入作業で、上記のいずれかの懸念事項が 1 つでもあれば、ハブおよびスポーク ネットワーク アーキテクチャの実装を検討してください。

> [!NOTE]
> Azure 参照アーキテクチャ サイトには、独自のハブおよびスポーク ネットワークを実装するための基礎として使用できるサンプル テンプレートが含まれています。
>
> - [ハブ アンド スポーク ネットワーク トポロジを Azure 上に実装する](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)
> - [共有サービスを含むハブ アンド スポーク ネットワーク トポロジを Azure 上に実装する](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/shared-services)

## <a name="overview"></a>概要

![ハブおよびスポーク ネットワーク トポロジの例][1]

図に示したように、Azure では 2 種類のハブおよびスポーク設計がサポートされます。 通信、共有リソース、一元的セキュリティ ポリシーに対するものと (図の "VNet Hub")、ブランチとブランチの間またはブランチと Azure の間の大規模な通信に対する仮想 WAN タイプ (図の "Virtual WAN") がサポートされます。

ハブは、ゾーン (インターネット、オンプレミス、スポーク) 間のイングレスまたはエグレス トラフィックを制御および検査する中央ネットワーク ゾーンです。 ハブ アンド スポークのトポロジでは、IT 部門は一元化された場所でセキュリティ ポリシーを効率的に適用できます。 また、構成の誤りや露出の可能性も低減されます。

多くの場合、ハブには、スポークによって消費される共通のサービス コンポーネントが含まれます。 共通の中央サービスの例を次に示します。

- 信頼されていないネットワークからアクセスするサード パーティがスポーク内のワークロードにアクセスする前のユーザー認証に必要な Windows Server Active Directory インフラストラクチャ。 これには、関連する Active Directory フェデレーション サービス (AD FS) が含まれます。
- オンプレミスおよびインターネット上のリソースにアクセスするための、スポーク内のワークロードの名前付けを解決する DNS サービス ([Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview) が使用されていない場合)。
- ワークロードにシングル サインオンを実装するための公開キー基盤 (PKI)。
- スポーク ネットワーク ゾーンとインターネット間の TCP/UDP トラフィックのフロー制御。
- スポークとオンプレミス間のフロー制御。
- (必要に応じて) スポーク間のフロー制御。

共有ハブ インフラストラクチャを使用して複数のスポークをサポートすることで、冗長性を最小限に抑え、管理を合理化し、全体的なコストを削減できます。

各スポークのロールは、異なる種類のワークロードをホストできます。 スポークは、同じワークロードの反復可能なデプロイのためのモジュール方式のアプローチも提供します。 例として、開発とテスト、ユーザー受け入れテスト、ステージング環境、運用環境があります。

スポークでは、組織内のさまざまなグループを分離して有効にすることもできます。 例として Azure DevOps グループがあります。 スポーク内には、基本的なワークロードや、階層間のトラフィック制御を伴う複雑な多層ワークロードをデプロイできます。

## <a name="subscription-limits-and-multiple-hubs"></a>サブスクリプションの制限と複数のハブ

Azure では、種類に関係なく、すべてのコンポーネントが Azure サブスクリプションにデプロイされます。 異なる Azure サブスクリプションに Azure コンポーネントを分離することで、差別化されたレベルのアクセスと承認の設定など、さまざまな業種の要件を満たすことができます。

単一のハブおよびスポーク実装で、多数のスポークにスケールアップできます。 ただし、他の IT システムと同様に、プラットフォームの制限があります。 ハブのデプロイは特定の Azure サブスクリプションにバインドされており、サブスクリプションには制約と制限があります。 その一例が、仮想ネットワーク ピアリングの最大数です。 詳細については、「[Azure サブスクリプションとサービスの制限、クォータ、制約](https://docs.microsoft.com/azure/azure-subscription-service-limits)」をご覧ください。

制限が問題になる可能性がある場合は、単一のハブアンドスポークからハブアンドスポークのクラスターにモデルを拡張することによって、アーキテクチャをさらにスケールアップできます。 仮想ネットワーク ピアリング、Azure ExpressRoute、仮想 WAN、またはサイト間 VPN を使用して、1 つ以上の Azure リージョン内の複数のハブを相互接続できます。

![ハブとスポークのクラスター][2]

複数のハブを導入すると、システムのコストと管理のオーバーヘッドが増加します。 これが妥当なのは、ユーザーのパフォーマンスやディザスター リカバリーのために、スケーラビリティ、システムの制限、または冗長性やリージョン レプリケーションが必要な場合だけです。 複数のハブが必要なシナリオでは、運用を容易にするため、すべてのハブで同じサービスのセットを提供する必要があります。

## <a name="interconnection-between-spokes"></a>スポーク間の相互接続

1 つのスポーク内に、複雑な多層ワークロードを実装できます。 多階層の構成は、同じ仮想ネットワーク内のサブネット (すべての階層に対して 1 つ) を使い、ネットワーク セキュリティ グループを使ってフローをフィルター処理することによって実装できます。

アーキテクトは、複数の仮想ネットワークに多層ワークロードをデプロイできます。 仮想ネットワーク ピアリングにより、スポークは同じハブまたは異なるハブの他のスポークに接続できます。

このシナリオの一般的な例は、アプリケーション処理サーバーが 1 つのスポーク (仮想ネットワーク) にあるケースです。 データベースは別のスポーク (仮想ネットワーク) にデプロイされています。 この場合、仮想ネットワーク ピアリングでスポークを相互接続することによって、ハブの通過を簡単に回避できます。 ハブをバイパスすることで、ハブにしか存在しない重要なセキュリティ ポイントや監査ポイントがバイパスされることがないように、アーキテクチャとセキュリティを慎重に見直すことが解決策となります。

![スポークの相互接続とハブへの接続][3]

スポークは、ハブとして機能するスポークに相互接続することもできます。 この方法では 2 レベルの階層が作成され、上位レベル (レベル 0) のスポークが、階層の下位レベル (レベル 1) のスポークのハブになります。 ハブ アンド スポーク実装のスポークは、トラフィックがオンプレミス ネットワークまたはパブリック インターネットのいずれかで送信先に到達できるように、中央のハブにトラフィックを転送する必要があります。 2 レベルのハブのアーキテクチャでは複雑なルーティングが導入され、シンプルなハブおよびスポーク関係のメリットが失われます。

<!-- images -->

[0]: ../../_images/azure-best-practices/network-redundant-equipment.png "コンポーネントのオーバーラップの例"
[1]: ../../_images/azure-best-practices/network-hub-spoke-high-level.png "概要レベルのハブ アンド スポークの例"
[2]: ../../_images/azure-best-practices/network-hub-spokes-cluster.png "ハブとスポークのクラスター"
[3]: ../../_images/azure-best-practices/network-spoke-to-spoke.png "スポーク間"
[4]: ../../_images/azure-best-practices/network-hub-spoke-block-level-diagram.png "ハブ アンド スポークのブロックレベルの図"
[5]: ../../_images/azure-best-practices/network-users-groups-subscriptions.png "ユーザー、グループ、サブスクリプション、およびプロジェクト"
[6]: ../../_images/azure-best-practices/network-infrastructure-high-level.png "インフラストラクチャの概要図"
[7]: ../../_images/azure-best-practices/network-high-level-perimeter-networks.png "インフラストラクチャの概要図"
[8]: ../../_images/azure-best-practices/network-vnet-peering-perimeter-networks.png "仮想ネットワーク ピアリングと境界ネットワーク"
[9]: ../../_images/azure-best-practices/network-high-level-diagram-monitoring.png "監視の概要図"
[10]: ../../_images/azure-best-practices/network-high-level-workloads.png "ワークロードの概要図"

<!-- links -->

[PrivateDNS]: https://docs.microsoft.com/azure/dns/private-dns-overview
[VNetPeering]: https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview
[user-defined-routes]: https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview
[RBAC]: https://docs.microsoft.com/azure/role-based-access-control/overview
[azure-ad]: https://docs.microsoft.com/azure/active-directory/active-directory-whatis
[VPN]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways
[ExR]: https://docs.microsoft.com/azure/expressroute/expressroute-introduction
[ExRD]: https://docs.microsoft.com/azure/expressroute/expressroute-erdirect-about
[vWAN]: https://docs.microsoft.com/azure/virtual-wan/virtual-wan-about
[NVA]: https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha
[AzFW]: https://docs.microsoft.com/azure/firewall/overview
[SubMgmt]: ../../reference/azure-scaffold.md
[RGMgmt]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview
[DMZ]: https://docs.microsoft.com/azure/best-practices-network-security
[ALB]: https://docs.microsoft.com/azure/load-balancer/load-balancer-overview
[PIP]: https://docs.microsoft.com/azure/virtual-network/resource-groups-networking#public-ip-address
[AFD]: https://docs.microsoft.com/azure/frontdoor/front-door-overview
[AppGW]: https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction
[WAF]: https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview
[Monitor]: https://docs.microsoft.com/azure/monitoring-and-diagnostics/
[ActLog]: https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs
[DiagLog]: https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs
[nsg-log]: https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log
[OMS]: https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview
[NPM]: https://docs.microsoft.com/azure/log-analytics/log-analytics-network-performance-monitor
[NetWatch]: https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview
[WebApps]: https://docs.microsoft.com/azure/app-service/
[HDI]: https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-introduction
[EventHubs]: https://docs.microsoft.com/azure/event-hubs/event-hubs-what-is-event-hubs
[ServiceBus]: https://docs.microsoft.com/azure/service-bus-messaging/service-bus-messaging-overview
[traffic-manager]: https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview
