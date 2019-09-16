---
title: ハブアンドスポーク ネットワーク トポロジ
titleSuffix: Microsoft Cloud Adoption Framework for Azure
description: ハブアンドスポーク ネットワーク トポロジ
author: tracsman
ms.author: jonor
ms.date: 05/10/2019
ms.topic: guide
ms.service: cloud-adoption-framework
ms.subservice: ready
manager: rossort
tags: azure-resource-manager
ms.custom: virtual-network
ms.openlocfilehash: 48f73d7c7f2e7f3bba8183464c786a3e0744807c
ms.sourcegitcommit: 5846ed4d0bf1b6440f5e87bc34ef31ec8b40b338
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/11/2019
ms.locfileid: "70905483"
---
# <a name="hub-and-spoke-network-topology"></a>ハブアンドスポーク ネットワーク トポロジ

*ハブ アンド スポーク*は、一般的な通信またはセキュリティ要件をより効率的に管理するためのネットワーク モデルです。 Azure サブスクリプションの制限の回避にも役立ちます。 このモデルでは、次の懸念事項に対処します。

- **コストの削減と管理の効率**。 複数のワークロードで共有できるサービス (ネットワーク仮想アプライアンス (NVA) や DNS サーバーなど) を 1 か所に集めることで、IT は過剰なリソースと管理作業を最小限にすることができます。
- **サブスクリプションの制限の克服**。 大規模なクラウドベースのワークロードでは、単一の Azure サブスクリプション内で許可されるリソースよりも多くのリソースの使用が求められる場合があります。 ([サブスクリプションの制限][Limits]を参照してください。)さまざまなサブスクリプションから中央のハブへのワークロード仮想ネットワークのピアリングで、こうした制限を克服できます。
- **懸念事項の分離**。 中央の IT チームとワークロード チームの間で個々 のワークロードをデプロイすることができます。

小さなクラウド資産は、このモデルによって追加で提供される構造と機能の恩恵を受けない場合があります。 しかし、大規模なクラウド導入作業で、上記のいずれかの懸念事項が 1 つでもあれば、ハブアンドスポーク ネットワーク アーキテクチャの実装を検討してください。

> [!NOTE]
> Azure の参照アーキテクチャのサイトには、独自のハブ アンド スポーク ネットワークを実装するための基礎として使用できるサンプル テンプレートが含まれています。
>
> - [Azure にハブアンドスポーク ネットワーク トポロジを実装する](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)
> - [Azure に共有サービスを含むハブアンドスポーク ネットワーク トポロジを実装する](/azure/architecture/reference-architectures/hybrid-networking/shared-services)

## <a name="overview"></a>概要

![ハブアンドスポーク ネットワーク トポロジの例][1]

図に示したように、Azure では 2 種類のハブアンドスポーク設計がサポートされます。 通信、共有リソース、一元的セキュリティ ポリシーに対するものと (図の "VNet Hub")、ブランチとブランチの間またはブランチと Azure の間の大規模な通信に対する仮想 WAN タイプ (図の "Virtual WAN") がサポートされます。

ハブは、ゾーン (インターネット、オンプレミス、スポーク) 間のイングレスまたはエグレス トラフィックを制御および検査する中央ネットワーク ゾーンです。 ハブアンドスポークのトポロジでは、IT 部門は一元化された場所でセキュリティ ポリシーを効率的に適用できます。 また、構成の誤りや露出の可能性も低減されます。

多くの場合、ハブには、スポークによって消費される共通のサービス コンポーネントが含まれます。 共通の中央サービスの例を次に示します。

- 信頼されていないネットワークからアクセスするサード パーティがスポーク内のワークロードにアクセスする前のユーザー認証に必要な Windows Server Active Directory インフラストラクチャ。 これには、関連する Active Directory フェデレーション サービス (AD FS) が含まれます。
- オンプレミスおよびインターネット上のリソースにアクセスするための、スポーク内のワークロードの名前付けを解決する DNS サービス ([Azure DNS][DNS] が使用されていない場合)。
- ワークロードにシングル サインオンを実装するための公開キー基盤 (PKI)。
- スポーク ネットワーク ゾーンとインターネット間の TCP/UDP トラフィックのフロー制御。
- スポークとオンプレミス間のフロー制御。
- (必要に応じて) スポーク間のフロー制御。

共有ハブ インフラストラクチャを使用して複数のスポークをサポートすることで、冗長性を最小限に抑え、管理を合理化し、全体的なコストを削減できます。

各スポークのロールは、異なる種類のワークロードをホストできます。 スポークは、同じワークロードの反復可能なデプロイのためのモジュール方式のアプローチも提供します。 例として、開発とテスト、ユーザー受け入れテスト、ステージング環境、運用環境があります。

スポークでは、組織内のさまざまなグループを分離して有効にすることもできます。 例として Azure DevOps グループがあります。 スポーク内には、基本的なワークロードや、階層間のトラフィック制御を伴う複雑な多層ワークロードをデプロイできます。

## <a name="subscription-limits-and-multiple-hubs"></a>サブスクリプションの制限と複数のハブ

Azure では、種類に関係なく、すべてのコンポーネントが Azure サブスクリプションにデプロイされます。 異なる Azure サブスクリプションに Azure コンポーネントを分離することで、差別化されたレベルのアクセスと承認の設定など、さまざまな業種の要件を満たすことができます。

1 つのハブアンドスポーク実装で多数のスポークにスケールアップできます。 ただし、他の IT システムと同様に、プラットフォームの制限があります。 ハブのデプロイは特定の Azure サブスクリプションにバインドされており、サブスクリプションには制約と制限があります。 たとえば、仮想ネットワーク ピアリングの最大数が設定されています。 詳細については、「[Azure サブスクリプションとサービスの制限、クォータ、制約][Limits]」を参照してください。

制限が問題になる可能性がある場合は、単一のハブアンドスポークからハブアンドスポークのクラスターにモデルを拡張することによって、アーキテクチャをさらにスケールアップできます。 仮想ネットワーク ピアリング、Azure ExpressRoute、仮想 WAN、またはサイト間 VPN を使用して、1 つ以上の Azure リージョン内の複数のハブを相互接続できます。

![ハブとスポークのクラスター][2]

複数のハブを導入すると、システムのコストと管理のオーバーヘッドが増加します。 これが妥当なのは、ユーザーのパフォーマンスやディザスター リカバリーのために、スケーラビリティ、システムの制限、または冗長性やリージョン レプリケーションが必要な場合だけです。 複数のハブが必要なシナリオでは、運用を容易にするため、すべてのハブで同じサービスのセットを提供する必要があります。

## <a name="interconnection-between-spokes"></a>スポーク間の相互接続

1 つのスポーク内に、複雑な多層ワークロードを実装できます。 多階層の構成は、同じ仮想ネットワーク内のサブネット (すべての階層に対して 1 つ) を使い、ネットワーク セキュリティ グループを使ってフローをフィルター処理することによって実装できます。

アーキテクトは、複数の仮想ネットワークに多層ワークロードをデプロイできます。 仮想ネットワーク ピアリングにより、スポークは同じハブまたは異なるハブの他のスポークに接続できます。

このシナリオの一般的な例は、アプリケーション処理サーバーが 1 つのスポーク (仮想ネットワーク) にあるケースです。 データベースは別のスポーク (仮想ネットワーク) にデプロイされています。 この場合、仮想ネットワーク ピアリングでスポークを相互接続することによって、ハブの通過を簡単に回避できます。 ハブをバイパスすることで、ハブにしか存在しない重要なセキュリティ ポイントや監査ポイントがバイパスされることがないように、アーキテクチャとセキュリティを慎重に見直すことが解決策となります。

![スポークの相互接続とハブへの接続][3]

スポークは、ハブとして機能するスポークに相互接続することもできます。 この方法では 2 レベルの階層が作成され、上位レベル (レベル 0) のスポークが、階層の下位レベル (レベル 1) のスポークのハブになります。 ハブアンドスポーク実装のスポークは、トラフィックがオンプレミス ネットワークまたはパブリック インターネットのいずれかで送信先に到達できるように、中央のハブにトラフィックを転送する必要があります。 2 レベルのハブのアーキテクチャでは複雑なルーティングが導入され、シンプルなハブアンドスポーク関係のメリットが失われます。

<!-- images -->

[0]: ./images/network-redundant-equipment.png "コンポーネントのオーバーラップの例"
[1]: ./images/network-hub-spoke-high-level.png "概要レベルのハブ アンド スポークの例"
[2]: ./images/network-hub-spokes-cluster.png "ハブとスポークのクラスター"
[3]: ./images/network-spoke-to-spoke.png "スポーク間"
[4]: ./images/network-hub-spoke-block-level-diagram.png "ハブ アンド スポークのブロックレベルの図"
[5]: ./images/network-users-groups-subsciptions.png "ユーザー、グループ、サブスクリプション、およびプロジェクト"
[6]: ./images/network-infrastructure-high-level.png "インフラストラクチャの概要図"
[7]: ./images/network-highlevel-perimeter-networks.png "インフラストラクチャの概要図"
[8]: ./images/network-vnet-peering-perimeter-neworks.png "仮想ネットワーク ピアリングと境界ネットワーク"
[9]: ./images/network-high-level-diagram-monitoring.png "監視の概要図"
[10]: ./images/network-high-level-workloads.png "ワークロードの概要図"

<!-- links -->

[Limits]: /azure/azure-subscription-service-limits
[Roles]: /azure/role-based-access-control/built-in-roles
[VNet]: /azure/virtual-network/virtual-networks-overview
[network-security-groups]: /azure/virtual-network/virtual-networks-nsg
[DNS]: /azure/dns/dns-overview
[PrivateDNS]: /azure/dns/private-dns-overview
[VNetPeering]: /azure/virtual-network/virtual-network-peering-overview
[user-defined-routes]: /azure/virtual-network/virtual-networks-udr-overview
[RBAC]: /azure/role-based-access-control/overview
[azure-ad]: /azure/active-directory/active-directory-whatis
[VPN]: /azure/vpn-gateway/vpn-gateway-about-vpngateways
[ExR]: /azure/expressroute/expressroute-introduction
[ExRD]: /azure/expressroute/expressroute-erdirect-about
[vWAN]: /azure/virtual-wan/virtual-wan-about
[NVA]: /azure/architecture/reference-architectures/dmz/nva-ha
[AzFW]: /azure/firewall/overview
[SubMgmt]: /azure/architecture/cloud-adoption/appendix/azure-scaffold
[RGMgmt]: /azure/azure-resource-manager/resource-group-overview
[DMZ]: /azure/best-practices-network-security
[ALB]: /azure/load-balancer/load-balancer-overview
[PIP]: /azure/virtual-network/resource-groups-networking#public-ip-address
[AFD]: /azure/frontdoor/front-door-overview
[AppGW]: /azure/application-gateway/application-gateway-introduction
[WAF]: /azure/application-gateway/application-gateway-web-application-firewall-overview
[Monitor]: /azure/monitoring-and-diagnostics/
[ActLog]: /azure/monitoring-and-diagnostics/monitoring-overview-activity-logs
[DiagLog]: /azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs
[nsg-log]: /azure/virtual-network/virtual-network-nsg-manage-log
[OMS]: /azure/operations-management-suite/operations-management-suite-overview
[NPM]: /azure/log-analytics/log-analytics-network-performance-monitor
[NetWatch]: /azure/network-watcher/network-watcher-monitoring-overview
[WebApps]: /azure/app-service/
[HDI]: /azure/hdinsight/hdinsight-hadoop-introduction
[EventHubs]: /azure/event-hubs/event-hubs-what-is-event-hubs
[ServiceBus]: /azure/service-bus-messaging/service-bus-messaging-overview
[traffic-manager]: /azure/traffic-manager/traffic-manager-overview
