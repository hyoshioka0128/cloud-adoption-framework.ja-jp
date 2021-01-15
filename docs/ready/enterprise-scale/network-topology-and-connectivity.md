---
title: ネットワーク トポロジと接続
description: Microsoft Azure との間や、その内部でのネットワークと接続に関する主要な設計上の考慮事項と推奨事項について説明します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 01/08/2021
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.custom: think-tank
ms.openlocfilehash: 15f72f6899ba9ae3cf5a9bf9a804c7e0e1363d9c
ms.sourcegitcommit: 32a958d1dd2d688cb112e9d1be1706bd1e59c505
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/12/2021
ms.locfileid: "98123563"
---
<!-- docutune:casing "Azure VPN Gateway" L7 -->
<!-- cSpell:ignore autoregistration BGPs MACsec MPLS MSEE onprem privatelink VPNs -->

# <a name="network-topology-and-connectivity"></a>ネットワーク トポロジと接続

この記事では、Microsoft Azure との間や、その内部でのネットワークと接続に関する主要な設計上の考慮事項と推奨事項について説明します。

## <a name="plan-for-ip-addressing"></a>IP アドレス指定を計画する

組織では、オンプレミスの場所と Azure リージョンの間で IP アドレス空間が重複しないように、Azure での IP アドレスを計画することが重要です。

**設計上の考慮事項:**

- オンプレミスと Azure リージョンの間で IP アドレス空間が重複すると、競合の大きな問題が生じます。

- 仮想ネットワークを作成した後で、アドレス空間を追加できます。 このプロセスでは、ピアリングを削除して再作成する必要があるため、既に仮想ネットワークが仮想ネットワーク ピアリングを使用して別の仮想ネットワークに接続されている場合、停止が必要になります。

- Azure では、各サブネット内に 5 つの IP アドレスが予約されています。 仮想ネットワークとそれに含まれるサブネットのサイズを決定するときは、それを考慮してください。

- 一部の Azure サービスでは、[専用のサブネット](/azure/virtual-network/virtual-network-for-azure-services#services-that-can-be-deployed-into-a-virtual-network)が必要になります。 このようなサービスとしては、Azure Firewall や Azure VPN Gateway などがあります。

- サブネットを特定のサービスにデリゲートして、サブネット内にサービスのインスタンスを作成することができます。

**設計上の推奨事項:**

- Azure リージョンとオンプレミスの場所の間で、重複しない IP アドレス空間を事前に計画します。

- プライベート インターネットに対するアドレス割り当てから IP アドレスを使用します (RFC 1918)。

- 使用できるプライベート IP アドレスに制限がある環境では (RFC 1918)、IPv6 の使用を検討します。

- IP アドレス空間が無駄にならないよう、不必要に大きな仮想ネットワーク (例: `/16`) を作成しないでください。

- 前もって必要なアドレス空間を計画することなく仮想ネットワークを作成することはやめてください。 アドレス空間を追加すると、仮想ネットワーク ピアリングを介して仮想ネットワークに接続した後で、停止が発生します。

- 仮想ネットワークにはパブリック IP アドレスを使用しないでください。パブリック IP アドレスがお客様の組織に属していない場合は特にそうです。

## <a name="configure-dns-and-name-resolution-for-on-premises-and-azure-resources"></a>オンプレミスと Azure のリソースに対して DNS と名前解決を構成する

ドメイン ネーム システム (DNS) は、エンタープライズ規模のアーキテクチャ全体における重要な設計トピックです。 DNS への既存の投資を使用することを望む組織があります。 または、クラウドの導入を、内部 DNS インフラストラクチャを最新化し、Azure のネイティブ機能を使用する機会と考える組織もあります。

**設計上の考慮事項:**

- DNS リゾルバーを Azure プライベート DNS と組み合わせて使用し、クロスプレミスの名前解決を実現できます。

- お客様は、オンプレミスと Azure の間で既存の DNS ソリューションの使用を必須とすることもできます。

- 自動登録を使用して仮想ネットワークをリンクできるプライベート DNS ゾーンの最大数は 1 です。

- 自動登録を有効にしないで仮想ネットワークをリンクできるプライベート DNS ゾーンの最大数は 1,000 です。

**設計上の推奨事項:**

- Azure での名前解決だけが必要な環境の場合は、解決のための Azure プライベート DNS を使用します。 名前解決用にデリゲートされたゾーンを作成します (`azure.contoso.com` など)。

- Azure とオンプレミスの間で名前解決が必要な環境では、少なくとも 2 つの仮想マシン (VM) にデプロイされた既存の DNS インフラストラクチャ (Active Directory 統合 DNS など) を使用します。 それらの DNS サーバーを使用するように、仮想ネットワークで DNS の設定を構成します。

- その場合でも、Azure プライベート DNS ゾーンを仮想ネットワークにリンクし、オンプレミスの DNS サーバーを使用することで、オンプレミスの DNS 名 (`onprem.contoso.com` など) への条件付き転送によるハイブリッド リゾルバーとして、DNS サーバーを使用することができます。 Azure プライベート DNS ゾーン (`azure.contoso.com` など) に対する Azure 内のリゾルバー VM への条件付きフォワーダーを使用して、オンプレミスのサーバーを構成できます。

- 独自の DNS (Red Hat OpenShift など) をデプロイする必要がある特殊なワークロードでは、優先 DNS 解決を使用する必要があります。

- 仮想ネットワーク内にデプロイされている仮想マシンの DNS レコードのライフサイクルを自動的に管理するには、Azure DNS に対する自動登録を有効にします。

- Azure プライベート DNS でのクロスプレミスの DNS 解決には、リゾルバーとして仮想マシンを使用します。

- グローバル接続サブスクリプション内に Azure プライベート DNS ゾーンを作成します。 他の Azure プライベート DNS ゾーンを作成する場合があります (たとえば、Azure Private Link 用の `privatelink.database.windows.net` や `privatelink.blob.core.windows.net`)。

## <a name="define-an-azure-network-topology"></a>Azure のネットワーク トポロジを定義する

ネットワーク トポロジは、アプリケーションの相互通信方法が定義されているため、エンタープライズ規模のアーキテクチャの重要な要素です。 このセクションでは、Azure デプロイのためのテクノロジとトポロジのアプローチについて説明します。 2 つの主要なアプローチである、Azure Virtual WAN に基づくトポロジと従来のトポロジに焦点を当てます。

Virtual WAN は、大規模な相互接続の要件を満たすために使用されます。 Microsoft の管理サービスであるため、ネットワークの全体的な複雑さが軽減され、組織のネットワークの近代化にも役立ちます。 次のいずれかの点で要件を満たしている場合は、Virtual WAN ベースのトポロジが最も適している可能性があります。

- 組織では複数の Azure リージョンにリソースをデプロイする予定であり、これらの Azure リージョン内の VNet と複数のオンプレミスの場所の間でグローバルに接続する必要があります。
- 組織では、ソフトウェアによる WAN (SD WAN) デプロイを通じて、またはネイティブ IPsec 終端に 30 個を超えるブランチ サイトを必要とする、大規模なブランチ ネットワークを直接 Azure に統合する予定です。
- VPN と ExpressRoute の間に推移的なルーティングが必要です。 例: サイト間 VPN を介して接続されたリモート ブランチ、またはポイント対サイト VPN 経由を介して接続されたリモート ユーザーは、DC に接続された ExpressRoute に Azure 経由で接続する必要があります。

従来のハブ アンド スポークのネットワーク トポロジは、顧客が管理するルーティングとセキュリティを使用して、カスタマイズされた安全な大規模ネットワークを Azure で構築するのに役立ちます。 次のいずれかの点で要件を満たしている場合は、従来のトポロジが最も適している可能性があります。

- 組織は 1 つまたは複数の Azure リージョンにリソースをデプロイする予定です。複数の Azure リージョンをまたいだトラフィック (たとえば、2 つの異なる Azure リージョンをまたいだ 2 つの仮想ネットワーク間のトラフィック) が想定されていますが、すべての Azure リージョンにわたるフル メッシュ ネットワークは必要ありません。
- リージョンあたりのリモートまたはブランチの場所が少ない。 つまり、必要な IP セキュリティ (IPsec) のサイト間トンネルが 30 未満。
- Azure ネットワークのルーティング ポリシーを手動で構成するために、完全にきめ細かく制御する必要がある。

## <a name="virtual-wan-network-topology-microsoft-managed"></a>Virtual WAN ネットワーク トポロジ (Microsoft 管理)

![Virtual WAN ネットワーク トポロジを示す図。](./media/virtual-wan-topology.png)
_図 1:Virtual WAN のネットワーク トポロジ。_

**設計上の考慮事項:**

- [Azure Virtual WAN](/azure/virtual-wan/virtual-wan-about) は Microsoft が管理するソリューションであり、エンド ツー エンドでグローバルな動的トランジット接続が既定で提供されます。 Virtual WAN ハブにより、ネットワーク接続を手動で構成する必要がなくなります。 たとえば、お客様は、グローバルなトランジット接続を有効にするためにユーザー定義ルート (UDR) またはネットワーク仮想アプライアンス (NVA) を管理する必要はありません。

- Virtual WAN を使用すると、[ハブ アンド スポークのネットワーク アーキテクチャ](/azure/virtual-wan/virtual-wan-global-transit-network-architecture)が作成されるため、Azure 内、およびオンプレミスから Azure へのエンド ツー エンドのネットワーク接続が簡素化されます。 次の図に示すように、このアーキテクチャは、複数の Azure リージョンとオンプレミスの場所をサポートする (Any-to-Any 接続) ように容易に拡張できます。

  ![Virtual WAN でのグローバル トランジット ネットワークを示す図。](./media/global-transit.png)
  _図 2:Virtual WAN でのグローバル トランジット ネットワーク。_

- Virtual WAN の Any-to-Any の推移的な接続では、(同じリージョン内およびリージョン間での) 次のパスがサポートされています。

  - 仮想ネットワークから仮想ネットワーク
  - 仮想ネットワークからブランチ
  - ブランチから仮想ネットワーク
  - ブランチからブランチ

- Virtual WAN ハブは、Microsoft が管理するリソースのデプロイに限定されます。 その中にデプロイできるリソースは、仮想ネットワーク ゲートウェイ (ポイント対サイト VPN、サイト間 VPN、Azure ExpressRoute)、Firewall Manager を介した Azure Firewall、ルート テーブル、ベンダー固有の SD-WAN 機能用の[一部のネットワーク仮想アプライアンス (NVA)](/azure/virtual-wan/about-nva-hub) だけです。

- Virtual WAN には、いくつかの Azure サブスクリプションの制限が適用されます。これについては、[こちら](/azure/azure-resource-manager/management/azure-subscription-service-limits#virtual-wan-limits)の記事に記載されています。

- ネットワーク間の推移的な接続 (リージョン内、およびハブ間を経由したリージョン間) は、現在一般提供 (GA) されています。

- すべての仮想ハブに Microsoft が管理するルーティング機能が存在するため、Standard Virtual WAN 内の仮想ネットワーク間のトランジット接続が有効になります。 各ハブでは、VNet 間のトラフィックに対して最大 50 Gbps の合計スループットがサポートされます。

- 1 つの Azure Virtual WAN ハブでは、直接接続されているすべての VNet にわたり、特定の最大数の VM ワークロードがサポートされます。これについては、[Azure Virtual WAN の制限](/azure/azure-resource-manager/management/azure-subscription-service-limits#virtual-wan-limits)に関する記事で説明されています。

- Virtual WAN は、さまざまな [SD-WAN プロバイダー](/azure/virtual-wan/virtual-wan-locations-partners)と統合されています。

- 多くの管理サービス プロバイダーから、Virtual WAN 向けの[管理サービス](/azure/networking/networking-partners-msp)が提供されています。

- Virtual WAN 内のユーザー VPN (ポイント対サイト) ゲートウェイは、仮想ハブあたり最大 20 Gbps の合計スループットと 10,000 件のクライアント接続までスケールアップすることができます。

- Virtual WAN 内のサイト間 VPN ゲートウェイは、最大 20 Gbps の合計スループットまでスケールアップすることができます。

- Virtual WAN ハブに接続するには、SKU が Local、Standard、または Premium の ExpressRoute 回線を使用できます。

- ExpressRoute Global Reach がサポートされている場所にある ExpressRoute の Standard または Premium 回路を Virtual WAN ExpressRoute ゲートウェイに接続し、Virtual WAN のあらゆる転送機能 (VPN 間、VPN、ExpressRoute) を利用できます。 Global Reach の場所ではないところにある ExpressRoute の Standard または Premium 回線は Azure リソースに接続できますが、Virtual WAN の転送機能は使用できません。

- ExpressRoute Local が Azure Virtual WAN ハブでサポートされるのは、Virtual WAN ハブに接続されたスポーク VNET が Virtual WAN ハブと同じリージョンに存在する場合です。

- 現在一般提供されている Azure Firewall Manager を使用すると、Virtual WAN ハブに Azure Firewall をデプロイできます。 セキュリティ保護付き仮想ハブの概要と最新の[制約](/azure/firewall-manager/overview#known-issues)については、Azure Firewall Manager の[製品ページ](/azure/firewall-manager/overview)をご覧ください。

- Azure Firewall が VWAN ハブ自体 (セキュリティ保護付き仮想ハブ) の内側にデプロイされている場合、Azure Firewall を経由した Virtual WAN のハブ間トラフィックは現在サポートされていません。 要件に応じていくつかの回避策があります。たとえば、[Azure Firewall をスポーク仮想ネットワークに](/azure/virtual-wan/scenario-route-through-nva)配置したり、トラフィックのフィルター処理に NSG を使用したりします。

**設計上の推奨事項:**

- Azure リージョンとオンプレミスの場所にわたってグローバルなトランジット接続を必要とする、Azure での新しく大規模な、またはグローバルなネットワーク デプロイには Virtual WAN をお勧めします。 そのようにすると、Azure ネットワークに対して推移的なルーティングを手動で設定する必要がなくなります。

  次の図では、ヨーロッパと米国にデータセンターが分散している、グローバルなエンタープライズのデプロイの例を示します。 また、このデプロイには、両方のリージョンに多数のブランチ オフィスもあります。 この環境は、Azure Virtual WAN と [ExpressRoute Global Reach](/azure/expressroute/expressroute-global-reach) によってグローバルに接続されています。

  ![ネットワーク トポロジの例の図](./media/global-reach-topology.png)
  _図 3:ネットワーク トポロジのサンプル。_

- Azure リージョンごとに Virtual WAN ハブを使用し、共通のグローバル Azure Virtual WAN を経由して、Azure リージョンをまたいで複数のランディング ゾーンを接続します。

- VNet およびブランチ間でトラフィックをさらに分割するには、[仮想ハブのルーティング](/azure/virtual-wan/about-virtual-hub-routing)機能を使用します。

- ExpressRoute を使用して、Virtual WAN ハブをオンプレミスのデータセンターに接続します。

- DNS サーバーなどの必要な共有サービスを、専用のスポーク仮想ネットワークにデプロイします。 お客様がデプロイした共有リソースを Virtual WAN ハブ自体の内側にデプロイすることはできません。

- サイト間 VPN を介してブランチとリモート拠点を最も近い Virtual WAN ハブに接続するか、SD-WAN パートナー ソリューションを使用してブランチを Virtual WAN に接続できるようにします。

- ポイント対サイト VPN を使用して、ユーザーを Virtual WAN ハブに接続します。

- リソースが異なるリージョンにある場合でも、Azure 内のリソース間の通信が Microsoft のバックボーン ネットワーク経由で行われるように、"Azure 内のトラフィックは Azure 内に留まる" の原則に従います。

- インターネット送信の保護とフィルター処理を行うには、仮想ハブに Azure Firewall をデプロイすることを検討してください。

- 東部/西部または南部/北部のトラフィック保護とフィルター処理にパートナー NVA が必要な場合、Azure Virtual WAN では仮想ハブにそのようなセキュリティ NVA をデプロイすることが許可されていないため、[こちら](/azure/virtual-wan/scenario-route-through-nva)の記事で説明されているようにそれらの NVA を別個のスポーク仮想ネットワークにデプロイして静的ルーティングを構成することが要件を満たしているかどうか評価してください。 代わりの方法として、ハブ アンド スポーク モデルに基づく従来のネットワーク トポロジを検討してください。この場合、パートナー NVA を通常のハブ仮想ネットワークにデプロイすることができます。

- パートナーのネットワーク テクノロジと NVA をデプロイするときは、パートナー ベンダーのガイダンスに従って、Azure ネットワークと競合する構成がないようにします。

- Virtual WAN に基づいていないハブ アンド スポーク ネットワーク トポロジから移行するブラウンフィールドのシナリオについては、「[Azure Virtual WAN に移行する](/azure/virtual-wan/migrate-from-hub-spoke-topology)」を参照してください。

- 接続サブスクリプション内に Azure Virtual WAN リソースと Azure Firewall リソースを作成します。

- Virtual WAN の仮想ハブごとに、500 より多くの仮想ネットワーク接続を作成しないでください。

- デプロイを慎重に計画し、ネットワーク アーキテクチャが [Azure Virtual WAN の制限](/azure/azure-resource-manager/management/azure-subscription-service-limits#virtual-wan-limits)内であることを確認します。

- [Virtual WAN に関する Azure Monitor の分析情報 (プレビュー)](/azure/virtual-wan/azure-monitor-insights) を使用して、Virtual WAN のエンド ツー エンド トポロジのほか、状態と主要[メトリック](/azure/virtual-wan/azure-monitor-insights#detailed)を監視します。

## <a name="traditional-azure-networking-topology"></a>従来の Azure ネットワーク トポロジ

![従来の Azure ネットワーク トポロジを示す図。](./media/customer-managed-topology.png)

"_図 4:従来の Azure ネットワーク トポロジ。_

**設計上の考慮事項:**

- さまざまなネットワーク トポロジで、複数のランディング ゾーン仮想ネットワークを接続できます。 たとえば、1 つの大きなフラット仮想ネットワーク、複数の ExpressRoute 回線または接続で接続された複数の仮想ネットワーク、ハブ アンド スポーク、フル メッシュ、ハイブリッドなどです。

- 仮想ネットワークがサブスクリプションの境界を横断することはできません。 ただし、仮想ネットワーク ピアリング、ExpressRoute 回線、または VPN ゲートウェイを使用することで、異なるサブスクリプションをまたいだ仮想ネットワーク間の接続を実現できます。

- 仮想ネットワーク ピアリングは、Azure で仮想ネットワークを接続するための推奨される方法です。 仮想ネットワーク ピアリングを使用して、同じリージョン内で、異なる Azure リージョン間で、および異なる Azure Active Directory (Azure AD) テナント間で、仮想ネットワークを接続できます。

- 仮想ネットワーク ピアリングとグローバル仮想ネットワーク ピアリングは推移的ではありません。 トランジット ネットワークを可能にするには UDR と NVA が必要です。 詳細については、「[Azure のハブスポーク ネットワーク トポロジ](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)」を参照してください。

- ExpressRoute 回線を使用すると、同じ地政学的地域内の仮想ネットワーク間の接続や、Premium アドオンを使用して異なる地政学的地域間の接続を確立することができます。 次の点に留意してください。

  - ネットワーク間のトラフィックでは、Microsoft Enterprise Edge (MSEE) ルーターでトラフィックが折り返す必要があるため、待機時間が長くなる可能性があります。

  - 帯域幅は、ExpressRoute ゲートウェイの SKU に制限されます。

  - 仮想ネットワーク間のトラフィックの検査やログ記録が必要な場合は、やはり UDR をデプロイして管理する必要があります。

- Border Gateway Protocol (BGP) を使用する VPN ゲートウェイは、Azure 内とオンプレミスでは推移的ですが、ExpressRoute 経由で接続されたネットワークには推移的なアクセスを提供しません。

- 複数の ExpressRoute 回線が同じ仮想ネットワークに接続されている場合は、接続の重みと BGP 手法を使用して、オンプレミスと Azure の間のトラフィックに最適なパスになるようにします。 詳細については、「[ExpressRoute ルーティングの最適化](/azure/expressroute/expressroute-optimize-routing)」を参照してください。

- ExpressRoute のルーティングに影響を与える BGP メトリックの使用は、Azure プラットフォームの外部で行われる構成変更です。 お客様の組織またはその接続プロバイダーは、それに応じてオンプレミスのルーターを構成する必要があります。

- Premium アドオンを使用する ExpressRoute 回線では、グローバルな接続が提供されます。

- ExpressRoute には特定の制限が適用されます。たとえば、ExpressRoute ゲートウェイごとの ExpressRoute 接続の最大数や、ExpressRoute プライベート ピアリングを介して Azure からオンプレミスにアドバタイズできるルートの最大数などです。 このような制限については、[ExpressRoute の制限](/azure/azure-resource-manager/management/azure-subscription-service-limits#expressroute-limits)に関する記事で説明されています。

- VPN ゲートウェイの最大集約スループットは 10 Gbps です。 最大 30 個のサイト間トンネルまたはネットワーク間トンネルがサポートされます。

**設計上の推奨事項:**

- 次のシナリオでは、従来のハブ アンド スポーク ネットワーク トポロジに基づくネットワーク設計を検討してください。

  - 1 つの Azure リージョン内にデプロイされたネットワーク アーキテクチャ。

  - ネットワーク アーキテクチャは複数の Azure リージョンにまたがり、リージョンをまたぐランディング ゾーンに対する仮想ネットワーク間の推移的な接続は必要ない。

  - ネットワーク アーキテクチャは複数の Azure リージョンにまたがり、グローバル VNet ピアリングを使用して、Azure リージョン間で仮想ネットワークを接続できる。

  - VPN 接続と ExpressRoute 接続の間の推移的な接続は必要ない。

  - 使用されている主要なハイブリッド接続の方法が ExpressRoute であり、VPN ゲートウェイあたりの VPN 接続の数が 30 未満である。

  - 集中型 NVA ときめ細かいルーティングに依存している。

- リージョン デプロイの場合は、主にハブ アンド スポーク トポロジが使用されます。 ExpressRoute 経由のクロスプレミス接続、ブランチ接続用の VPN、NVA と UDR を介したスポーク間接続、Azure Firewall またはサード パーティ NVA によるインターネット送信保護のためには、中央ハブ仮想ネットワークに対して仮想ネットワーク ピアリングを使用して接続するランディング ゾーン仮想ネットワークを使用します。 次の図はこのワークフローを示したものです。 これにより、適切なトラフィック制御によって、セグメント化と検査のためのほとんどの要件を満たすことができます。

  ![ハブ アンド スポーク ネットワーク トポロジを示す図。](./media/hub-and-spoke-topology.png)
  _図 5:ハブ アンド スポーク ネットワーク トポロジ。_

- 次のいずれかの条件に該当する場合は、複数の ExpressRoute 回線で接続された複数の仮想ネットワークのトポロジを使用します。

  - 高いレベルの分離が必要である。

  - 特定の事業単位用に専用の ExpressRoute 帯域幅が必要である。

  - ExpressRoute ゲートウェイあたりの接続の最大数に達しました (最大数については、[ExpressRoute の制限](/azure/azure-resource-manager/management/azure-subscription-service-limits#expressroute-limits)に関する記事をご覧ください)。

次の図はこのワークフローを示したものです。

  ![複数の ExpressRoute 回線で接続された複数の仮想ネットワークを示す図。](./media/vnet-multiple-circuits.png)
  _図 6:複数の ExpressRoute 回線で接続された複数の仮想ネットワーク。_

- ExpressRoute ゲートウェイ、VPN ゲートウェイ (必要な場合)、Azure Firewall、またはパートナーの NVA (必要な場合) が含まれる最小限の共有サービスのセットを、中央ハブの仮想ネットワークにデプロイします。 必要に応じて、Active Directory ドメイン コントローラーと DNS サーバーもデプロイします。

- East/West または South/North のトラフィックの保護とフィルター処理のための Azure Firewall またはパートナーの NVA を、中央ハブの仮想ネットワークにデプロイします。

- パートナーのネットワーク テクノロジまたは NVA として展開するときは、パートナー ベンダーのガイダンスに従って次のことを確認します。

  - ベンダーによってデプロイがサポートされる。

  - 高可用性と最大パフォーマンスのためのガイダンスが設計されている。

  - Azure ネットワークと競合する構成がない。

- 中央ハブ仮想ネットワークの共有サービスとして、Azure Application Gateway などの L7 受信 NVA をデプロイしないでください。 代わりに、それらは、それぞれのランディング ゾーンにアプリと一緒にデプロイします。

- ブランチの拠点を本社に接続するには、既存のネットワーク (MPLS および SD-WAN) を使用します。 ExpressRoute ゲートウェイと VPN ゲートウェイの間では Azure の推移はサポートされていません。

- Azure リージョンをまたぐ複数のハブ アンド スポーク トポロジがあるネットワーク アーキテクチャでは、リージョン間で通信する必要があるランディング ゾーンの数が少ない場合は、グローバル仮想ネットワーク ピアリングを使用してランディング ゾーンの仮想ネットワークを接続します。 このアプローチにより、VM SKU で許可されるような、グローバル仮想ネットワーク ピアリングによる高ネットワーク帯域幅などのメリットが提供されます。 ただし、トラフィックの検査やフィルター処理が必要な場合は、中央の NVA はバイパスされます。 これは、[グローバル仮想ネットワーク ピアリングに対する制限](/azure/virtual-network/virtual-network-peering-overview#constraints-for-peered-virtual-networks)の対象にもなります。

- お客様が 2 つの Azure リージョンにハブ アンド スポーク ネットワーク アーキテクチャをデプロイし、リージョンをまたぐすべてのランディング ゾーン間にトランジット接続が必要な場合は、ExpressRoute とデュアル回線を使用して、Azure リージョンをまたぐランディング ゾーン仮想ネットワークにトランジット接続を提供します。 このシナリオでは、ランディング ゾーンは、リージョン内ではローカル ハブ仮想ネットワーク内の NVA 経由で、リージョン間では ExpressRoute 回線を経由して推移できます。 トラフィックは MSEE ルーターで折り返す必要があります。 次の図ではこの設計を示します。

  ![ランディング ゾーンの接続の設計を示す図。](./media/vnet-dual-circuits.png)
  _図 7:ランディング ゾーンの接続の設計。_

- お客様の組織が、2 つを超える Azure リージョン間でのハブ アンド スポーク ネットワーク アーキテクチャを必要としており、Azure リージョンをまたぐランディング ゾーン仮想ネットワーク間でグローバルなトランジット接続が必要な場合。 グローバル仮想ネットワーク ピアリングで中央ハブ仮想ネットワークを相互に接続し、UDR と NVA を使用してグローバル トランジット ルーティングを有効にすることで、このアーキテクチャを実装できます。 複雑度と管理オーバーヘッドが高いため、Virtual WAN を使用したグローバル トランジット ネットワーク アーキテクチャを評価することをお勧めします。

- Azure 上のネットワークのエンド ツー エンドの状態を監視するには、[Azure Monitor for Networks (プレビュー)](/azure/azure-monitor/insights/network-insights-overview) を使用します。

- スポーク仮想ネットワークを中央ハブ仮想ネットワークに接続する場合は、次の 2 つの[制限](/azure/azure-resource-manager/management/azure-subscription-service-limits)を考慮する必要があります。仮想ネットワークあたりの仮想ネットワーク ピアリング接続の最大数と、プライベート ピアリングを使用して ExpressRoute 経由で Azure からオンプレミスにアドバタイズされるプレフィックスの最大数。 ハブ仮想ネットワークに接続されているスポーク仮想ネットワークの数が、これらの制限値を超えていないことを確認してください。

## <a name="connectivity-to-azure"></a>Azure への接続性

このセクションでは、ネットワーク トポロジを拡張し、オンプレミスの場所から Azure に接続するために推奨されるモデルを検討します。

**設計上の考慮事項:**

- Azure ExpressRoute は、オンプレミスの場所から Azure のサービスとしてのインフラストラクチャ (IaaS) およびサービスとしてのプラットフォーム (PaaS) 機能への専用プライベート接続を提供します。

- Private Link を使用して、プライベート ピアリングによる ExpressRoute 経由で PaaS サービスへの接続を確立できます。

- 複数の仮想ネットワークが同じ ExpressRoute 回線に接続されている場合、それらは同じルーティング ドメインの一部になり、帯域幅はすべての仮想ネットワークで共有されます。

- ExpressRoute Global Reach (使用可能な場合) を使用すると、お客様は、ExpressRoute 回線を通してオンプレミスの場所をまとめて接続し、Microsoft のバックボーン ネットワーク経由でトラフィックを転送することができます。

- ExpressRoute Global Reach は、多くの [ExpressRoute ピアリングの場所](/azure/expressroute/expressroute-global-reach#availability)で利用できます。

- ExpressRoute Direct では、ExpressRoute Direct のポート容量 (10 Gbps または 100 Gbps) まで、追加コストなしで複数の ExpressRoute 回線を作成することができます。 また、Microsoft の ExpressRoute ルーターに直接接続することもできます。 100 Gbps SKU の場合、最小の回線帯域幅は 5 Gbps です。 10 Gbps SKU の場合、最小の回線帯域幅は 1 Gbps です。

**設計上の推奨事項:**

- オンプレミス ネットワークを Azure に接続するためのプライマリ接続チャネルとして、ExpressRoute を使用します。 VPN をバックアップ接続のソースとして使用して、接続の回復性を高めることができます。

- オンプレミスの場所を Azure の仮想ネットワークに接続するときは、異なるピアリングの場所からのデュアル ExpressRoute 回線を使用します。 このようにセットアップすると、オンプレミスと Azure の間に単一障害点がなくなり、Azure への冗長なパスが確保されます。

- 複数の ExpressRoute 回線を使用すると、[BGP のローカル設定と AS PATH プリペンドにより ExpressRoute のルーティングが最適化されます](/azure/expressroute/expressroute-optimize-routing#solution-use-as-path-prepending)。

- 帯域幅とパフォーマンスの要件に基づいて、ExpressRoute と VPN ゲートウェイに適切な SKU を使用していることを確認します。

- サポートされている Azure リージョンに、ゾーン冗長 ExpressRoute ゲートウェイをデプロイします。

- 10 Gbps より高い帯域幅または専用の 10/100 Gbps ポートを必要とするシナリオでは、ExpressRoute Direct を使用します。

- 待機時間を短くする必要がある場合、またはオンプレミスから Azure へのスループットを 10 Gbps より大きくする必要がある場合は、FastPath を有効にして、データ パスから ExpressRoute ゲートウェイをバイパスします。

- ブランチまたはリモートの場所を Azure に接続するには、VPN ゲートウェイを使用します。 回復力を高めるには、ゾーン冗長ゲートウェイをデプロイします (使用可能な場合)。

- ExpressRoute 経由で Azure に接続されている大規模なオフィス、地域本社、またはデータセンターを接続するには、ExpressRoute Global Reach を使用します。

- トラフィックの分離または専用の帯域幅が必要な場合は (運用環境と非運用環境を分離する場合など)、異なる ExpressRoute 回線を使用します。 ルーティング ドメインを分離し、迷惑な隣人のリスクを軽減するのに役立ちます。

- Network Performance Monitor を使用して ExpressRoute 回線を先行して監視します。

- 単一のピアリングの場所からは、ExpressRoute 回線を明示的に使用しないでください。 このようにすると、単一障害点が作成され、組織がピアリングの場所の停止による影響を受けやすくなります。

- 迷惑な隣人のリスクを回避するため、分離または専用の帯域幅を必要とする複数の環境を、同じ ExpressRoute 回線を使用して接続しないでください。

## <a name="connectivity-to-azure-paas-services"></a>Azure PaaS サービスへの接続

これまでの接続セクションを基にして、このセクションでは、Azure PaaS サービスを使用するための推奨される接続方法について説明します。

**設計上の考慮事項:**

- Azure PaaS サービスは通常、パブリック エンドポイントを介してアクセスされます。 ただし、Azure プラットフォームには、そのようなエンドポイントをセキュリティで保護する機能や、完全にプライベートにする機能が用意されています。

  - 仮想ネットワーク インジェクションでは、サポートされるサービスに対する専用のプライベート デプロイが提供されます。 ただし、管理プレーンのトラフィックは、パブリック IP アドレスを通して流れます。

  - [Private Link](/azure/private-link/private-endpoint-overview#private-link-resource) では、プライベート IP アドレスを使用して、Azure PaaS インスタンス、または Azure Load Balancer Standard レベルの内側にあるカスタム サービスへの専用アクセスが提供されます。

  - 仮想ネットワーク サービス エンドポイントでは、選択されたサブネットから選択された PaaS サービスへのサービス レベルのアクセスが提供されます。

- エンタープライズは多くの場合、PaaS サービスに対するパブリック エンドポイントに関する問題があり、適切に軽減する必要があります。

- [サポートされているサービス](/azure/private-link/private-link-overview#availability)の場合、Private Link により、サービス エンドポイントに関するデータの窃盗の問題は解決されます。 代わりの方法として、NVA による送信フィルターを使用して、データの窃盗を緩和するための手順を提供できます。

**設計上の推奨事項:**

- サポートされている Azure サービスに対する仮想ネットワーク インジェクションを使用して、仮想ネットワーク内からそれらを使用できるようにします。

- 仮想ネットワークに挿入された Azure PaaS サービスでは、パブリック IP アドレスを使用して管理プレーンの操作がまだ実行されます。 UDR と NSG を使用して、この通信が仮想ネットワーク内にロックダウンされるようにします。

- [使用可能な場合](/azure/private-link/private-link-overview#availability)は、Private Link を共有 Azure PaaS サービスに対して使用します。 Private Link は、一部のサービスについては一般提供されており、多くのサービスではパブリック プレビュー段階です。

- ExpressRoute プライベート ピアリングを介して、オンプレミスから Azure PaaS サービスにアクセスします。 専用の Azure サービスに対する仮想ネットワーク インジェクション、または使用可能な共有 Azure サービスに対する Azure Private Link を使用します。 仮想ネットワーク インジェクションまたは Private Link を使用できない場合にオンプレミスから Azure PaaS サービスにアクセスするには、Microsoft ピアリングで ExpressRoute を使用します。 この方法により、パブリック インターネット経由での推移を回避できます。

- 仮想ネットワーク内から Azure PaaS サービスへのアクセスをセキュリティで保護するには、仮想ネットワーク サービス エンドポイントを使用しますが、Private Link を使用できず、データ流出の問題が存在しない場合に限ります。 サービス エンドポイントでのデータ窃盗に関する問題に対処するには、NVA のフィルター処理を使用するか、Azure Storage に対する仮想ネットワーク サービス エンドポイント ポリシーを使用します。

- すべてのサブネットにおいて既定で仮想ネットワーク サービス エンドポイントを有効にしないでください。

- NVA フィルター処理を使用している場合を除き、データ窃盗の問題が存在する場合は、仮想ネットワーク サービス エンドポイントを使用しないでください。

- Azure から Azure リソースへの通信を可能にするために強制トンネリングを実装することは推奨されません。

## <a name="plan-for-inbound-and-outbound-internet-connectivity"></a>受信と送信のインターネット接続を計画する

このセクションでは、パブリック インターネットに対する受信接続と送信接続に対して推奨される接続モデルについて説明します。

**設計上の考慮事項:**

- Azure Firewall、Azure Application Gateway 上の Azure Web Application Firewall (WAF)、Azure Front Door などの Azure ネイティブのネットワーク セキュリティ サービスは、フル マネージド サービスです。 そのため、大規模な場合は複雑になる可能性のある、インフラストラクチャのデプロイに関連する運用コストと管理コストが発生することはありません。

- 組織が NVA の使用を望む場合や、ネイティブ サービスでは組織に固有の要件が満たされない場合、エンタープライズ規模のアーキテクチャは、パートナーの NVA と完全に互換性があります。

**設計上の推奨事項:**

- 次のものを管理するには Azure Firewall を使用します。

  - インターネットへの Azure 送信トラフィック。

  - HTTP/S ではない受信接続。

  - East/West トラフィックのフィルター処理 (組織で必要な場合)。

- Virtual WAN ハブ間またはハブ仮想ネットワーク内に Azure Firewall をデプロイして管理するには、Firewall Manager と Virtual WAN を使用します。 Firewall Manager は、Virtual WAN と通常の仮想ネットワークの両方で、現在一般提供されています。

- グローバル ネットワーク環境全体でセキュリティ体制を管理するには、グローバル Azure Firewall ポリシーを作成して、すべての Azure Firewall インスタンスに割り当てます。 Azure ロールベースのアクセス制御を使用してローカル セキュリティ チームに増分ファイアウォール ポリシーをデリゲートすることにより、特定のリージョンの要件を満たすきめ細かいポリシーを設定できます。

- 組織のご希望に応じて、サポートされているパートナーの SaaS セキュリティ プロバイダーを Firewall Manager 内で構成し、そのようなソリューションを使用して送信接続を保護できます。

- インターネットからの受信 HTTP/S トラフィックを保護するには、ランディング ゾーンの仮想ネットワーク内で WAF を使用します。

- Azure リージョン間でランディング ゾーンへの受信 HTTP/S 接続に対するグローバルな保護を提供するには、Azure Front Door と WAF ポリシーを使用します。

- Azure Front Door と Azure Application Gateway を使用して HTTP/S アプリを保護する場合は、Azure Front Door で WAF ポリシーを使用します。 Azure Application Gateway をロックダウンして、Azure Front Door からのトラフィックのみを受信します。

- East/West または South/North のトラフィックの保護とフィルター処理のために、パートナーの NVA が必要な場合:

  - Virtual WAN ネットワーク トポロジの場合は、別の仮想ネットワーク (NVA 仮想ネットワークなど) に NVA をデプロイします。 それを、リージョンの Virtual WAN ハブと、NVA にアクセスする必要があるランディング ゾーンに接続します。 [こちらの記事](/azure/virtual-wan/virtual-wan-route-table-portal)でプロセスについて説明されています。
  - Virtual WAN 以外のネットワーク トポロジの場合は、パートナーの NVA を中央ハブの仮想ネットワークにデプロイします。

- 受信 HTTP/S 接続にパートナーの NVA が必要な場合は、ランディング ゾーンの仮想ネットワーク内に、保護してインターネットに公開するアプリと共に、これらをデプロイします。

- 仮想ネットワーク内でホストされているすべてのパブリック エンドポイントを保護するには、[Azure DDoS Protection Standard 保護プラン](/azure/virtual-network/ddos-protection-overview)を使用します。

- オンプレミスの境界ネットワークの概念とアーキテクチャを Azure に複製しないでください。 同様のセキュリティ機能を Azure で利用できますが、実装とアーキテクチャをクラウドに適合させる必要があります。

## <a name="plan-for-app-delivery"></a>アプリの配信を計画する

このセクションでは、内部および外部に公開されるアプリを、安全で拡張性と可用性の高い方法で配信するための主な推奨事項について説明します。

**設計上の考慮事項:**

- Azure Load Balancer (内部およびパブリック) では、リージョン レベルでのアプリの配信に対して高可用性が提供されます。

- Azure Application Gateway を使用すると、リージョン レベルで HTTP/S アプリを安全に配信できます。

- Front Door を使用すると、Azure リージョン間で高可用性 HTTP/S アプリを安全に配信できます。

- Azure Traffic Manager を使用すると、グローバル アプリを配信できます。

**設計上の推奨事項:**

- 内部に公開されるアプリと外部に公開されるアプリの両方について、ランディング ゾーン内でアプリの配信を実行します。

- HTTP/S アプリのセキュリティ保護された配信を行うには、Application Gateway v2 を使用し、WAF の保護とポリシーを有効にします。

- HTTP/S アプリのセキュリティに Application Gateway v2 を使用できない場合は、パートナーの NVA を使用します。

- 受信 HTTP/S 接続に使用される Azure Application Gateway v2 またはパートナーの NVA は、セキュリティ保護対象のアプリと共に、ランディング ゾーンの仮想ネットワーク内にデプロイします。

- ランディング ゾーン内のすべてのパブリック IP アドレスに対し、DDoS Standard 保護プランを使用します。

- 複数の Azure リージョンにまたがるグローバル HTTP/S アプリを配信し、保護するには、Azure Front Door と WAF ポリシーを使用します。

- Front Door と Application Gateway を使用して HTTP/S アプリを保護する場合は、Front Door で WAF ポリシーを使用します。 Application Gateway をロックダウンして、Front Door からのトラフィックのみを受信します。

- HTTP/S 以外のプロトコルにまたがるグローバル アプリを配信するには、Traffic Manager を使用します。

## <a name="plan-for-landing-zone-network-segmentation"></a>ランディング ゾーンのネットワーク セグメント化を計画する

このセクションでは、ネットワークのゼロ トラスト実装を促進するために、ランディング ゾーン内で高度にセキュリティ保護された内部ネットワーク セグメント化を提供する場合の、主な推奨事項について説明します。

**設計上の考慮事項:**

- ゼロ トラスト モデルでは、違反状態が想定され、各要求が制御されていないネットワークから送信されたかのように検証されます。

- 高度なゼロ トラスト ネットワークの実装では、完全に分散されたイングレスおよびエグレス クラウド マイクロ境界と、より深いマイクロ セグメント化が採用されています。

- ネットワーク セキュリティ グループでは、Azure サービス タグを使用して、Azure PaaS サービスへの接続を容易にすることができます。

- アプリケーション セキュリティ グループでは、仮想ネットワークをまたがって保護が提供されることはありません。

- NSG フロー ログは、Azure Resource Manager テンプレートを使用してサポートされるようになりました。

**設計上の推奨事項:**

- サブネットの作成をランディング ゾーンの所有者にデリゲートします。 これにより、サブネットをまたいでワークロードをセグメント化する方法を定義できます (1 つの大きなサブネット、多層アプリケーション、ネットワークに挿入されたアプリケーションなど)。 プラットフォーム チームは、Azure Policy を使用して、特定の規則 (インターネットからの受信 SSH または RDP を拒否する、ランディング ゾーンをまたぐトラフィックを許可またはブロックする、など) が設定された NSG を、拒否のみのポリシーを持つサブネットと常に関連付けることができます。

- サブネット間のトラフィックおよびプラットフォームを横断する East/West トラフィック (ランディング ゾーン間のトラフィック) を保護するには、NSG を使用します。

- アプリケーション チームは、サブネット レベルの NSG でアプリケーション セキュリティ グループを使用して、ランディング ゾーン内の多層 VM を保護する必要があります。

- NSG とアプリケーション セキュリティ グループを使用して、ランディング ゾーン内のトラフィックをマイクロセグメント化し、中央の NVA を使用してトラフィック フローをフィルター処理しないようにします。

- NSG フロー ログを有効にし、それらを Traffic Analytics にフィードして、内部および外部のトラフィック フローに関する分析情報を取得します。

- ランディング ゾーン間の接続を選択的に許可するには、NSG を使用します。

- Virtual WAN トポロジでは、お客様の組織がランディング ゾーン間を流れるトラフィックに対するフィルター処理とログの機能を必要とする場合、Azure Firewall を介してランディング ゾーン間のトラフィックをルーティングします。

## <a name="define-network-encryption-requirements"></a>ネットワーク暗号化の要件を定義する

このセクションでは、オンプレミスと Azure の間、および Azure リージョン間で、ネットワーク暗号化を実現するための主な推奨事項について説明します。

**設計上の考慮事項:**

- コストと使用可能な帯域幅は、エンドポイント間の暗号化トンネルの長さに反比例します。

- VPN を使用して Azure に接続する場合、インターネット上のトラフィックは IPsec トンネルによって暗号化されます。

- プライベート ピアリングで ExpressRoute を使用する場合、現在は、トラフィックは暗号化されません。

- ExpressRoute プライベート ピアリング経由のサイト間 VPN 接続の構成は、現在[プレビュー段階](/azure/vpn-gateway/site-to-site-vpn-private-peering)です。

- [Media Access Control Security (MACsec)](/azure/expressroute/expressroute-howto-MACsec) 暗号化を ExpressRoute Direct に適用して、ネットワーク暗号化を実現できます。

- Azure のトラフィックが (Microsoft または Microsoft の代理によって制御されていない物理的境界の外部で) データセンター間を移動する場合、基になるネットワーク ハードウェア上では [MACsec データリンク層暗号化](/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit)が使用されます。 これは、VNet ピアリング トラフィックに適用されます。

**設計上の推奨事項:**

![暗号化のフローを示す図。](./media/enc-flows.png)

_図 8:暗号化のフロー。_

- VPN ゲートウェイを使用してオンプレミスから Azure への VPN 接続を確立すると、トラフィックは IPsec トンネルを通してプロトコル レベルで暗号化されます。 上の図では、フロー `A` でこの暗号化が示されています。

- ExpressRoute Direct を使用するときは、組織のルーターと MSEE の間のレイヤー 2 レベルでトラフィックを暗号化するために、[MACsec](/azure/expressroute/expressroute-howto-MACsec) を構成します。 図では、フロー `B` でこの暗号化が示されています。

- MACsec がオプションではない (たとえば、ExpressRoute Direct を使用していない) Virtual WAN のシナリオでは、Virtual WAN VPN ゲートウェイを使用して、[ExpressRoute プライベート ピアリング経由で IPsec トンネル](/azure/virtual-wan/vpn-over-expressroute)を確立します。 図では、フロー `C` でこの暗号化が示されています。

- Virtual WAN 以外のシナリオで、MACsec がオプションではない場合 (たとえば、ExpressRoute Direct を使用していない場合)、オプションは以下のものだけです。

  - パートナーの NVA を使用して、ExpressRoute プライベート ピアリング経由で IPsec トンネルを確立します。
  - Microsoft ピアリングを使用して ExpressRoute 経由で VPN トンネルを確立します。
  - ExpressRoute プライベート ピアリング経由のサイト間 VPN 接続を構成する ([プレビュー](/azure/vpn-gateway/site-to-site-vpn-private-peering)) 機能を評価します。

- Azure リージョン間のトラフィックを暗号化する必要がある場合は、グローバル VNet ピアリングを使用してリージョン間で仮想ネットワークを接続します。

- Azure のネイティブ ソリューション (図のフロー `B` と `C` で示されているもの) で要件が満たされない場合は、Azure でパートナーの NVA を使用して、ExpressRoute プライベート ピアリング経由でトラフィックを暗号化します。

## <a name="plan-for-traffic-inspection"></a>トラフィックの検査を計画する

多くの業界では、組織は、詳細な検査と分析のために、Azure のトラフィックがネットワーク パケット コレクターにミラー化されることを望みます。 この要件では、通常、受信と送信のインターネット トラフィックに焦点が当てられています。 このセクションでは、Azure Virtual Network 内のトラフィックをミラーリングまたはタップするときの主な考慮事項と推奨される方法について説明します。

**設計上の考慮事項:**

<!-- docutune:ignore TAP -->

- [Azure 仮想ネットワーク ターミナル アクセス ポイント (TAP)](/azure/virtual-network/virtual-network-tap-overview) はプレビュー段階です。 可用性の詳細については、`azurevnettap@microsoft.com` にお問い合わせください。

- Azure Network Watcher でのパケット キャプチャは一般提供されていますが、キャプチャの最大期間は 5 時間に制限されています。

**設計上の推奨事項:**

Azure 仮想ネットワーク TAP の代わりの手段としては、次のオプションを評価してください。

- キャプチャ ウィンドウは制限されていますが、Network Watcher パケットを使用してキャプチャします。

- 最新バージョンの NSG フロー ログで、必要な詳細レベルが提供されるかどうかを評価します。

- ディープ パケット インスペクションが必要なシナリオには、パートナー ソリューションを使用します。

- トラフィックをミラー化するためのカスタム ソリューションを開発しないでください。 このアプローチは小規模なシナリオでは許容されるかもしれませんが、複雑さとサポートの問題が発生する可能性があるため、大規模に使用することは推奨されません。
