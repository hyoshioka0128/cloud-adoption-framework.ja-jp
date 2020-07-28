---
title: ネットワーク トポロジと接続
description: Microsoft Azure との間や、その内部でのネットワークと接続に関する主要な設計上の考慮事項と推奨事項について説明します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 06/15/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.openlocfilehash: c085c3b197306b4774daa5fcc5d9f0501db30eef
ms.sourcegitcommit: 71a4f33546443d8c875265ac8fbaf3ab24ae8ab4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2020
ms.locfileid: "86479485"
---
<!-- cSpell:ignore autoregistration BGPs MACsec MPLS MSEE onprem privatelink VPNs -->

# <a name="network-topology-and-connectivity"></a>ネットワーク トポロジと接続

このセクションでは、Microsoft Azure に対する、Microsoft Azure からの、および Microsoft Azure 内でのネットワークと接続に関する主要な設計上の考慮事項と推奨事項について説明します。

## <a name="planning-for-ip-addressing"></a>IP アドレスの計画

お客様の組織が、考慮されているオンプレミスの場所と Azure リージョンで IP アドレス空間が重複しないように、Azure での IP アドレスを計画することは重要です。

**設計上の考慮事項:**

- オンプレミスと Azure リージョンの間で IP アドレス空間が重複すると、競合の大きな問題が生じます。

- 作成後に仮想ネットワーク (VNet) のアドレス空間を追加することはできますが、仮想ネットワーク ピアリングを使用して VNet が既に別の VNet に接続されている場合、ピアリングを削除して再作成する必要があるため、このプロセスでは停止が必要になります。

- Azure では各サブネット内に 5 つの IP アドレスが予約されているため、VNet とそれに含まれるサブネットのサイズを決定するときは、それを考慮する必要があります。

- 一部の Azure サービスには、Azure Firewall や VNet Gateway などの[専用サブネット](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services#services-that-can-be-deployed-into-a-virtual-network)が必要になります。

- サブネットを特定のサービスにデリゲートして、サブネット内にそのサービスのインスタンスを作成することができます。

**設計上の推奨事項:**

- Azure リージョンとオンプレミスの場所の間で、重複しない IP アドレス空間を事前に計画します。

- プライベート インターネットに対するアドレス割り当てから IP アドレスを使用します (RFC 1918)。

- 使用できるプライベート IP アドレス (RFC 1918) に制限がある環境では、IPv6 の使用を検討します。

- IP アドレス空間が無駄にならないよう、不必要に大きな VNet (例: `/16`) を作成しないでください。

- 事前に必要なアドレス空間を計画せずに VNet を作成しないでください。これは、VNet が仮想ネットワーク ピアリング経由で接続された後では、アドレス空間を追加するときに停止が発生するためです。

- VNet にはパブリック IP アドレスを使用しないでください。パブリック IP アドレスがお客様の組織に属していない場合は特にそうです。

## <a name="configure-the-domain-name-system-dns-and-name-resolution-for-on-premises-and-azure-resources"></a>オンプレミスと Azure のリソースに対してドメイン ネーム システム (DNS) と名前解決を構成する

DNS は、エンタープライズ規模のアーキテクチャ全体において重要な設計トピックです。DNS への既存の投資を使用することを望むか、それともクラウドを導入すれば内部 DNS インフラストラクチャを最新化して Azure のネイティブ機能を使用するのによい機会になると考えるかは、組織によって異なります。

**設計上の考慮事項:**

- DNS リゾルバーを Azure プライベート DNS と組み合わせて使用し、クロスプレミスの名前解決を実現できます。

- お客様は、オンプレミスと Azure の間で既存の DNS ソリューションの使用を必須とすることもできます。

- VNet が自動登録を使用してリンクできるプライベート DNS ゾーンの最大数は 1 です。

- VNet が自動登録を有効にしないでリンクできるプライベート DNS ゾーンの最大数は 1,000 です。

**設計上の推奨事項:**

- Azure での名前解決だけが必要な環境の場合は、名前解決用にデリゲートされたゾーンを作成することにより、解決のための Azure プライベート DNS を使用します (`azure.contoso.com` など)。

- Azure とオンプレミスの間で名前解決が必要な環境では、少なくとも 2 つの Azure VM にデプロイされた既存の DNS インフラストラクチャ (たとえば Active Directory 統合 DNS) を使用し、それらの DNS サーバーを使用するように VNet で DNS の設定を構成します。

- Azure プライベート DNS ゾーンが VNet にリンクされていて、オンプレミスの DNS サーバーを使用したオンプレミスの DNS 名 (`onprem.contoso.com` など) への条件付き転送で、ハイブリッド リゾルバーとして DNS サーバーが使用されている場合は、Azure プライベート DNS を使用することもできます。 オンプレミスのサーバーでは、Azure プライベート DNS ゾーン (`azure.contoso.com` など) に対する Azure 内のリゾルバー VM への条件付きフォワーダーを構成できます。

- 独自の DNS (Red Hat OpenShift など) をデプロイする必要がある特殊なワークロードでは、優先 DNS 解決を使用する必要があります。

- VNet 内にデプロイされている仮想マシンの DNS レコードのライフサイクルを自動的に管理するには、Azure DNS に対する自動登録を有効にする必要があります。

- Azure プライベート DNS でのクロスプレミスの DNS 解決には、リゾルバーとして仮想マシンを使用します。

- グローバル接続サブスクリプション内に Azure プライベート DNS ゾーンを作成します。 追加の Azure プライベート DNS ゾーンが作成される場合があります (たとえば、Azure Private Link 用の `privatelink.database.windows.net` や `privatelink.blob.core.windows.net`)。

## <a name="define-an-azure-network-topology"></a>Azure のネットワーク トポロジを定義する

ネットワーク トポロジは、アプリケーションの相互通信方法が最終的に定義されるため、エンタープライズ規模のアーキテクチャの重要な基本要素です。 このセクションでは、Azure Virtual WAN に基づくトポロジと従来のトポロジの 2 つの主要なアプローチに重点を置いて、エンタープライズ Azure デプロイに関連するテクノロジとトポロジのアプローチについて説明します。

Azure Virtual WAN に基づくネットワーク トポロジは、大規模なマルチリージョンのデプロイに適したエンタープライズ規模のアプローチであり、グローバルに広がる拠点を Azure とオンプレミスの両方に接続する必要がある組織にお勧めです。 組織が Azure と完全に統合されたソフトウェアによる WAN (SD-WAN) デプロイを使用する場合も常に、Virtual WAN のネットワーク トポロジを使用する必要があります。 Virtual WAN は、大規模な相互接続の要件を満たすために使用されます。 Microsoft の管理サービスであるため、ネットワークの全体的な複雑さが軽減され、組織のネットワークの近代化にも役立ちます。

組織が少数の Azure リージョンにだけリソースをデプロイする場合、グローバルに相互接続されたネットワークが必要ない場合、リージョンあたりのリモートまたはブランチ拠点が少ない場合 (必要な IPsec トンネルが 30 未満)、または Azure ネットワークを手動で構成できるように完全な制御と細分性が必要な場合は、従来の Azure ネットワーク トポロジを使用する必要があります。 この従来のトポロジは、Azure でセキュリティ保護されたネットワーク基盤を構築するのに役立ちます。

## <a name="virtual-wan-network-topology-microsoft-managed"></a>Virtual WAN ネットワーク トポロジ (Microsoft 管理)

![ネットワーク トポロジと接続](./media/virtual-wan-topology.png)
_図 1: Virtual WAN のネットワーク トポロジ。_

**設計上の考慮事項:**

- [Azure Virtual WAN](https://docs.microsoft.com/azure/virtual-wan/virtual-wan-about) は Microsoft のマネージド ソリューションであり、エンド ツー エンドのグローバル トランジット接続が既定で提供されます。 Virtual WAN ハブにより、ネットワーク接続を手動で構成する必要がなくなります。 たとえば、お客様は、グローバル トランジット接続を有効にするために、ユーザー定義ルーティング (UDR) またはネットワーク仮想アプライアンス (NVA) を設定する必要はありません。

- Virtual WAN を使用すると、次の図に示すように、何もしなくても複数の Azure リージョンとオンプレミスの場所にまたがる (Any-to-Any 接続) [ハブ アンド スポークのネットワーク アーキテクチャ](https://docs.microsoft.com/azure/virtual-wan/virtual-wan-global-transit-network-architecture)が作成されるので、Azure とクロスプレミスでのエンドツーエンドのネットワーク接続が大幅に簡素化されます。

![ネットワーク トポロジと接続](./media/global-transit.png)
_図 2: Virtual WAN でのグローバル トランジット ネットワーク。_

- Virtual WAN の Any-to-Any の推移的な接続では、(同じリージョン内およびリージョン間での) 次のパスがサポートされています。

  - 仮想ネットワークから仮想ネットワーク
  - 仮想ネットワークからブランチ
  - ブランチから仮想ネットワーク
  - ブランチからブランチ

- Virtual WAN ハブはロックダウンされており、お客様がその中にデプロイできるリソースは、仮想ネットワーク ゲートウェイ (ポイント対サイト VPN、サイト間 VPN、ExpressRoute)、Firewall Manager を介した Azure Firewall、ルート テーブルだけです。

- Virtual WAN では、ExpressRoute プライベート ピアリングを介して Azure からオンプレミスにアドバタイズされるプレフィックスに対する最大 200 の制限が、Virtual WAN ハブあたり 10,000 プレフィックスに増えます。 10,000 プレフィックスの制限には、サイト間 VPN とポイント対サイト VPN も含まれます。

- VNet 対 VNet のトランジット接続 (リージョン内およびリージョン間) は、現在一般提供 (GA) されています。

- Virtual WAN のハブ間接続は現在一般提供 (GA) されています。

- すべての仮想ハブにはルーターが存在するため、Standard Virtual WAN の VNet 間でトランジット接続が有効になります。 各仮想ハブ ルーターは、最大 50 Gbps の集約スループットをサポートしています。

- Virtual WAN は、さまざまな [SD-WAN プロバイダー](https://docs.microsoft.com/azure/virtual-wan/virtual-wan-locations-partners)と統合されています。

- 多くの管理サービス プロバイダーから、Virtual WAN 向けの[管理サービス](https://docs.microsoft.com/azure/networking/networking-partners-msp)が提供されています。

- Virtual WAN 内の VPN ゲートウェイは、仮想ハブあたり最大 20 Gbps および 20,000 接続までスケールアップできます。

- Premium アドオンを使用する、ExpressRoute Global Reach の場所からの ExpressRoute 回線が必要です。

- 現在一般提供 (GA) されている Azure Firewall Manager を使用すると、Virtual WAN ハブに Azure ファイアウォールをデプロイできます。

- Azure Firewall を経由する Virtual WAN のハブ間のトラフィックは、現在はサポートされていません。 その代わりに、Virtual WAN でのネイティブ ハブ間トランジット ルーティング機能を使用し、ハブ間の VNet トラフィックを許可またはブロックするには NSG を使用します。

**設計上の推奨事項:**

- Azure ネットワーク トランジット ルーティングを手動で設定する必要なしに、Azure リージョンとオンプレミスの場所の間のグローバルなトランジット接続を使用したいお客様の場合、Azure での新しい大規模またはグローバル ネットワークのデプロイには、Virtual WAN を強くお勧めします。

  次の図では、ヨーロッパと米国にデータセンターが分散し、両方のリージョンに多数のブランチ オフィスがある、グローバルなエンタープライズのデプロイのサンプルを示します。 環境は、Virtual WAN と ExpressRoute Global Reach によってグローバルに接続されています。

![ネットワーク トポロジのサンプル](./media/global-reach-topology.png)
_図 3: ネットワーク トポロジのサンプル。_

- Virtual WAN をグローバル接続リソースとして使用し、Azure リージョンごとのローカルな Virtual WAN ハブを経由して、Azure リージョン間で複数のランディング ゾーンを接続します。

- ExpressRoute を使用して、Virtual WAN ハブをオンプレミスのデータセンターに接続します。

- サイト間 VPN を介してブランチとリモート拠点を最も近い Virtual WAN ハブに接続するか、SD-WAN パートナー ソリューションを使用してブランチを Virtual WAN に接続できるようにします。

- ポイント対サイト VPN を使用して、エンド ユーザーを Virtual WAN ハブに接続します。

- リソースが異なるリージョンにある場合でも、Azure 内のリソース間の通信が Microsoft のバックボーン ネットワーク経由で行われるように、"Azure 内のトラフィックは Azure 内に留まる" の原則に従います。

- Azure リージョン内での East-West トラフィックおよび South-North トラフィックの保護とフィルター処理のために、Virtual WAN ハブに Azure Firewall をデプロイします。

- East-West トラフィックと South-North トラフィックの保護とフィルター処理のためにサードパーティの NVA が必要な場合は、別の VNet (NVA VNet など) に NVA をデプロイし、リージョンの Virtual WAN ハブと、NVA にアクセスする必要のあるランディング ゾーンに、それを接続します。 詳細については、[NVA 用の Virtual WAN ハブ ルート テーブルの作成](https://docs.microsoft.com/azure/virtual-wan/virtual-wan-route-table-portal)に関するページを参照してください。

- サードパーティのネットワーク テクノロジと NVA をデプロイする場合は、サードパーティ ベンダーのガイダンスに従って、Azure ネットワークと競合する構成がないようにします。

- サードパーティの NVA の使用可能化など、トランジット ネットワーク トポロジに関するすべての要件は Virtual WAN によって満たされるので、Azure Virtual WAN の上にトランジット ネットワークを構築しないでください。

- Azure のネットワーク テクノロジでは、Microsoft のバックボーンを通じて複数のリージョンにわたる Azure リソースの相互接続がサポートされているため、Azure リージョン間の Azure リソースの接続には、Multiprotocol Label Switching (MPLS) などの既存のオンプレミス ネットワークを使用しないでください。

- Virtual WAN に基づいていないハブ アンド スポーク ネットワーク トポロジから移行するブラウンフィールドのシナリオについては、「[Azure Virtual WAN に移行する](https://docs.microsoft.com/azure/virtual-wan/migrate-from-hub-spoke-topology)」を参照してください。

- Azure Virtual WAN リソースと Azure Firewall リソースは、接続サブスクリプション内に作成する必要があります。

- Virtual WAN 仮想ハブごとに、500 より多くの VNet 接続を作成しないでください。

## <a name="traditional-azure-networking-topology"></a>従来の Azure ネットワーク トポロジ

Virtual WAN では幅広い強力な機能が提供されていますが、従来の Azure ネットワーク アプローチが最適な場合もあります。

- 複数の Azure リージョンにまたがる、またはクロスプレミスの、グローバルなトランジット ネットワークが必要ない場合 (ヨーロッパの仮想ネットワークに接続する必要がある米国内のブランチなど)。

- VPN または SD-WAN ソリューションとの統合を介して多数のリモートの場所に接続する必要がない場合。

- お客様の組織が Azure でネットワーク トポロジをセットアップするときにきめ細かく制御および構成することを望まれる場合。

![ネットワーク トポロジと接続](./media/customer-managed-topology.png)
_図 4: 従来の Azure ネットワーク トポロジ。_

**設計上の考慮事項:**

- さまざまなネットワーク トポロジは複数のランディング ゾーン VNet に接続できます。1 つの大きなフラット VNet、複数の ExpressRoute 回線または接続で接続された複数の VNet、ハブ アンド スポーク、フル メッシュ、ハイブリッドなどです。

- VNet がサブスクリプションの境界を横断することはありませんが、仮想ネットワーク ピアリング、ExpressRoute 回線、または VPN ゲートウェイを使用することで、異なるサブスクリプションの VNet 間の接続を実現できます。

- 仮想ネットワーク ピアリングを使用して、同じリージョン内にある VNet、異なる Azure リージョン内にある VNet、異なる Azure AD テナント内にある VNet 間を接続できます。

- 仮想ネットワーク ピアリングとグローバル VNet ピアリングは推移的ではありません。 そのため、トランジット ネットワークを可能にするには UDR と NVA が必要です。 詳細については、「[Azure のハブスポーク ネットワーク トポロジ](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)」を参照してください。

- ExpressRoute 回線を使用すると、同じ地政学的地域内の VNet 間の接続や、Premium アドオンを使用して異なる地政学的地域間の接続を確立することができます。

  - VNet 間のトラフィックでは、Microsoft エンタープライズ エッジ (MSEE) ルーターでトラフィックが折り返す必要があるため、待機時間が長くなる可能性があります。

  - 帯域幅は、ExpressRoute ゲートウェイの SKU に制限されます。

  - また、VNet 間のトラフィックの検査やログ記録が必要な場合は、依然として UDR をデプロイして管理する必要があります。

- Border Gateway Protocol (BGP) を使用する VPN ゲートウェイは、Azure 内およびオンプレミス内では推移的ですが、ExpressRoute ゲートウェイ間では推移されません。

- 複数の ExpressRoute 回線が同じ VNet に接続されている場合は、接続の重みと BGP 手法を使用して、オンプレミスと Azure の間のトラフィックに最適なパスになるようにする必要があります。 詳細については、「[ExpressRoute ルーティングの最適化](https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing)」を参照してください。

- ExpressRoute のルーティングに影響を与える BGP の手法の使用は、Azure クラウドの外部での構成です。 そのため、お客様の組織またはその接続プロバイダーは、それに応じてオンプレミスのルーターを構成する必要があります。

- Premium アドオンを使用する ExpressRoute 回線では、グローバルな接続が提供されますが、ExpressRoute ゲートウェイあたりの ExpressRoute 接続の最大数は 4 です。

- VNet あたりの仮想ネットワーク ピアリング接続の最大数は 500 ですが、ExpressRoute プライベート ピアリング経由で Azure からオンプレミスにアドバタイズできるルートの最大数は 200 です。

- VPN ゲートウェイの最大合計スループットは 10 Gbps で、サイト間トンネルまたは VNet 対 VNet トンネルが最大 30 個までサポートされます。

**設計上の推奨事項:**

- 次のシナリオでは、仮想 WAN 以外のテクノロジを使用するハブ アンド スポーク ネットワーク トポロジに基づくネットワーク設計を検討する必要があります。

  - トラフィックの境界が Azure リージョン内にある Azure のデプロイ。

  - リージョンをまたぐランディング ゾーン VNet 間のトランジット接続が必要な、最大 2 つの Azure リージョンが含まれるネットワーク アーキテクチャ。

  - 複数の Azure リージョンにまたがり、リージョンをまたぐランディング ゾーン VNet 間のトランジット接続を必要としない、ネットワーク アーキテクチャ。

  - VPN 接続と ExpressRoute 接続の間のトランジット接続が必要ない。

  - メインのクロスプレミス接続チャネルが ExpressRoute であり、VPN ゲートウェイあたりの VPN 接続の数が 30 未満である。

  - 集中型 NVA と複雑できめ細かいルーティングに大きく依存する。

- 次の図に示すように、リージョン デプロイの場合は、主にハブ アンド スポーク トポロジが使用され、ExpressRoute 経由のクロスプレミス接続、ブランチ接続用の VPN、NVA と UDR を介したスポーク間接続、NVA によるインターネット送信保護には、中央ハブ VNet への仮想ネットワーク ピアリングによるランディング ゾーン VNet 接続が使用されます。

![ネットワーク トポロジと接続](./media/hub and spoke-topology.png)

_図 5:ハブ アンド スポーク ネットワーク トポロジ。_

- 高レベルの分離が必要な場合、特定の部署に対する専用の ExpressRoute 帯域幅が必要な場合、または ExpressRoute ゲートウェイごとの最大接続数 (最大 4 つ) に達した場合は、次の図に示すように、複数の ExpressRoute 回線トポロジで接続された複数の VNet を使用します。

![ネットワーク トポロジと接続](./media/vnet-multiple-circuits.png)
_図 6: 複数の ExpressRoute 回線で接続された複数の仮想ネットワーク。_

- ExpressRoute ゲートウェイ、VPN ゲートウェイ (必要な場合)、Azure Firewall またはサードパーティの NVA (必要な場合) を含む最小限の共有サービスのセットを、中央ハブの VNet にデプロイします。 必要に応じて、Active Directory ドメイン コントローラーと DNS サーバーも。

- East-West と South-North のトラフィックの保護とフィルター処理のための Azure Firewall またはサードパーティの NVA を、中央ハブの VNet にデプロイします。

- サードパーティのネットワーク テクノロジや NVA をデプロイする場合は、サードパーティ ベンダーのガイダンスに従って、デプロイがベンダーによってサポートされており、デプロイが高可用性と最大のパフォーマンスを実現するように設計されていて、Azure ネットワークと競合する構成がないようにします。

- 中央ハブ VNet の共有サービスとして、L7 受信 NVA (Azure Application Gateway など) をデプロイしないでください。 代わりに、それらは、それぞれのランディング ゾーンにアプリと一緒にデプロイする必要があります。

- ブランチの拠点を本社に接続するには、既存のネットワーク (MPLS および SD-WAN) を使用します。 ExpressRoute ゲートウェイと VPN ゲートウェイの間では Azure の推移はサポートされていません。

- Azure リージョンをまたぐ複数のハブ アンド スポーク トポロジがあるネットワーク アーキテクチャでは、リージョン間で通信する必要があるランディング ゾーンの数が少ない場合は、グローバル VNet ピアリングを使用してランディング ゾーンの VNet を接続します。 このアプローチには、(VM SKU で許可されている) グローバル VNet ピアリングによる高ネットワーク帯域幅などのメリットがありますが、中央の NVA はバイパスされます (トラフィックの検査またはフィルター処理が必要な場合)。 また、これは[グローバル VNet ピアリングの制限](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#constraints-for-peered-virtual-networks)の対象にもなります。

- お客様が 2 つの Azure リージョンにハブ アンド スポーク ネットワーク アーキテクチャをデプロイし、リージョンをまたぐすべてのランディング ゾーン間にトランジット接続が必要な場合は、ExpressRoute とデュアル回線を使用して、Azure リージョンをまたぐランディング ゾーン VNet にトランジット接続を提供します。 このシナリオでは、ランディング ゾーンは、リージョン内ではローカル ハブ VNet 内の NVA 経由で、リージョン間では ExpressRoute 回線を経由して推移でき、トラフィックは Microsoft エンタープライズ エッジ (MSEE) ルーターで折り返す必要があります。 この設計は次の図のようになります。

![ネットワーク トポロジと接続](./media/vnet-dual-circuits.png)
_図 7: ランディング ゾーンの接続の設計。_

- お客様の組織が、2 つを超える Azure リージョン間でのハブ アンド スポーク ネットワーク アーキテクチャを必要としており、Azure リージョンをまたぐランディング ゾーン VNet 間でグローバルなトランジット接続が必要な場合。 このアーキテクチャは、中央ハブ VNet をグローバル VNet ピアリングと相互接続し、UDR と NVA を使用してグローバルなトランジット ルーティングを有効にすることで実装できますが、複雑さや管理上の負担は高くなります。 代わりに、Virtual WAN を使用してグローバル トランジット ネットワーク アーキテクチャをデプロイすることをお勧めします。

- Azure 上のネットワークのエンド ツー エンドの状態を監視するには、[Azure Monitor ネットワーク分析情報](https://docs.microsoft.com/azure/azure-monitor/insights/network-insights-overview) (現在プレビュー段階) を使用します。

- 中央ハブ VNet あたり 200 個より多くのピアリング接続を作成しないでください。 VNet では最大 500 個のピアリング接続がサポートされていますが、プライベート ピアリングを使用する ExpressRoute では、Azure からオンプレミスへのアドバタイズに対してサポートされるプレフィックスが最大 200 個です。

## <a name="connectivity-to-azure"></a>Azure への接続性

このセクションでは、ネットワーク トポロジを拡張し、オンプレミスの場所から Azure に接続するために推奨されるモデルを検討します。

**設計上の考慮事項:**

- Azure ExpressRoute では、オンプレミスの場所から Microsoft Azure のサービスとしてのインフラストラクチャ (IaaS) 機能およびサービスとしてのプラットフォーム (PaaS) 機能に対する専用のプライベート接続が提供されています。

- Private Link を使用すると、プライベート ピアリングを使用して ExpressRoute 経由で PaaS サービスへの接続を確立できます。

- 複数の VNet が同じ ExpressRoute 回線に接続されている場合、それらは同じルーティング ドメインの一部になり、帯域幅はすべての VNet で共有されます。

- ExpressRoute Global Reach (使用可能な場合) を使用すると、お客様は、ExpressRoute 回線を使用してオンプレミスの場所をまとめて接続し、Microsoft のバックボーン ネットワーク経由でトラフィックを転送することができます。

- ExpressRoute Global Reach は、多くの [ExpressRoute ピアリングの場所](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach#availability)で利用できます。

- ExpressRoute Direct では、ExpressRoute Direct のポート容量 (10 Gbps または 100 Gbps) まで、追加コストなしで複数の ExpressRoute 回線を作成し、Microsoft の ExpressRoute ルーターに直接接続することができます。 100 Gbps SKU の場合、最小の回線帯域幅は 5 Gbps です。 10 Gbps SKU の場合、最小の回線帯域幅は 1 Gbps です。

<!-- cSpell:ignore prepending -->
<!-- docsTest:ignore "AS PATH prepending -->

**設計上の推奨事項:**

- オンプレミス ネットワークを Microsoft Azure に接続するためのプライマリ接続チャネルとして、ExpressRoute を使用します。 VPN をバックアップ接続のソースとして使用して、接続の回復性を高めることができます。

- オンプレミスの場所を Azure の VNet に接続するときは、異なるピアリングの場所からのデュアル ExpressRoute 回線を使用します。 このようにセットアップすると、Azure への冗長なパスが確保され、オンプレミスと Azure の間に単一障害点がなくなります。

- 複数の ExpressRoute 回線を使用すると、[BGP のローカル設定と AS PATH プリペンドにより ExpressRoute のルーティングが最適化されます](https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#solution-use-as-path-prepending)。

- 帯域幅とパフォーマンスの要件に基づいて、ExpressRoute と VPN ゲートウェイに適切な SKU が使用されていることを確認します。

- サポートされている Azure リージョンに、ゾーン冗長 ExpressRoute ゲートウェイをデプロイします。

- 10 Gbps より高い帯域幅または専用の 10/100 Gbps ポートを必要とするシナリオでは、ExpressRoute Direct を使用します。

- 待機時間を短くする必要がある場合、またはオンプレミスから Azure へのスループットを 10 Gbps より大きくする必要がある場合は、FastPath を有効にして、データ パスから ExpressRoute ゲートウェイをバイパスします。

- ブランチまたはリモートの場所を Azure に接続するには、VPN ゲートウェイを使用します。 回復力を高めるには、ゾーン冗長ゲートウェイをデプロイします (使用可能な場合)。

- ExpressRoute 経由で Azure に接続されている大規模なオフィス、地域本社、データセンターを接続するには、ExpressRoute Global Reach を使用します。

- トラフィックの分離または専用の帯域幅が必要な場合は (運用環境と非運用環境を分離する場合など)、異なる ExpressRoute 回線を使用して、ルーティング ドメインを分離し、迷惑な隣人のリスクを軽減する必要があります。

- Network Performance Monitor を使用して ExpressRoute 回線を先行して監視します。

- 単一のピアリングの場所からは ExpressRoute 回線を明示的に使用しないでください。このようにすると、単一障害点が作成され、組織はピアリングの場所の停止による影響を受けやすくなります。

- 迷惑な隣人のリスクを回避するため、分離または専用の帯域幅を必要とする複数の環境を、同じ ExpressRoute 回線を使用して接続しないでください。

## <a name="connectivity-to-azure-paas-services"></a>Azure PaaS サービスへの接続

これまでの接続セクションを基にして、このセクションでは、Azure PaaS サービスを使用するときに推奨される接続方法について説明します。

**設計上の考慮事項:**

- Azure PaaS サービスには一般にパブリック エンドポイント経由でアクセスしますが、Azure プラットフォームには、そのようなエンドポイントをセキュリティで保護する機能や、完全にプライベートにする機能が用意されています。

  - VNet インジェクションでは、サポートされるサービスに対する専用のプライベート デプロイが提供されます。 ただし、管理プレーンのトラフィックは、パブリック IP アドレスを通して流れます。

  - [Private Link](https://docs.microsoft.com/azure/private-link/private-endpoint-overview#private-link-resource) では、Azure PaaS インスタンスに対するプライベート IP アドレスを使用した専用アクセス、または Azure Load Balancer Standard の内側にあるカスタム サービスへの専用アクセスが提供されます。

  - VNet サービスのエンドポイントでは、選択されたサブネットから選択された PaaS サービスへのサービス レベルのアクセスが提供されます。

- エンタープライズは多くの場合、PaaS サービスに対するパブリック エンドポイントに関する問題があり、適切に軽減する必要があります。

- [サポートされているサービス](https://docs.microsoft.com/azure/private-link/private-link-overview#availability)の場合、Private Link により、サービス エンドポイントに関するデータの窃盗の問題は解決されます。 または、NVA による送信フィルターを使用して、データの窃盗を緩和するための手順を提供できます。

**設計上の推奨事項:**

- サポートされている Azure サービスに対する VNet インジェクションを使用して、VNet 内からそれらを使用できるようにします。

- VNet に挿入された Azure PaaS サービスでは、パブリック IP アドレスを使用して管理プレーンの操作がまだ実行されます。 UDR と NSG を使用して、この通信が VNet 内にロックダウンされるようにします。

- 使用可能な場合は、Private Link を共有 Azure PaaS サービスに対して使用します。 Private Link は、一部のサービスについては一般提供されており、多くのサービスではパブリック プレビュー段階です。 Private Link の使用可能性の詳細については、[こちら](https://docs.microsoft.com/azure/private-link/private-link-overview#availability)を参照してください。

- ExpressRoute プライベート ピアリング経由でオンプレミスから Azure PaaS サービスにアクセスするには、専用の Azure サービスに対しては VNet インジェクションを使用し、使用可能な共有 Azure サービスに対しては Azure Private Link を使用します。 VNet インジェクションまたは Private Link を使用できない場合にオンプレミスから Azure PaaS サービスにアクセスするには、Microsoft ピアリングで ExpressRoute を使用します。 これにより、パブリック インターネット経由での推移を回避できます。

- VNet 内から Azure PaaS サービスへのアクセスをセキュリティで保護するには、VNet サービス エンドポイントを使用しますが、Private Link を使用できず、データ流出の問題が存在しない場合に限ります。 サービス エンドポイントでのデータ窃盗に関する問題に対処するには、NVA のフィルター処理を使用するか、Azure Storage に対する VNet サービス エンドポイント ポリシーを使用します。

- すべてのサブネットにおいて既定で VNet サービス エンドポイントを有効にしないでください。

- NVA フィルター処理を使用している場合を除き、データ窃盗の問題が存在する場合は、VNet サービス エンドポイントを使用しないでください。

- Azure から Azure リソースへの通信を可能にするために強制トンネリングを実装することは推奨されません。

## <a name="planning-for-inbound-and-outbound-internet-connectivity"></a>受信と送信のインターネット接続の計画

このセクションでは、パブリック インターネットに対する受信接続と送信接続に対して推奨される接続モデルについて説明します。

**設計上の考慮事項:**

- Azure Firewall、Azure Application Gateway 上の Azure Web アプリケーション ファイアウォール (WAF)、Azure Front Door などの Azure ネイティブのネットワーク セキュリティ サービスはフル マネージド サービスであるため、大規模な場合は複雑になりかねない、インフラストラクチャのデプロイに関連する運用コストと管理コストが発生することはありません。

- お客様の組織が NVA の使用を望む場合や、ネイティブ サービスでは組織に固有の要件が満たされない場合、エンタープライズ規模のアーキテクチャは、サードパーティの NVA と完全に互換性があります。

**設計上の推奨事項:**

- 次のものを管理するには Azure Firewall を使用します。

  - インターネットへの Azure 送信トラフィック

  - HTTP/S ではない受信接続

  - East-West トラフィックのフィルター処理 (お客様の組織が必要とする場合)

- Virtual WAN ハブ間またはハブ VNet 内に Azure Firewall をデプロイして管理するには、Firewall Manager と Virtual WAN を使用します。 Firewall Manager は、現在、Virtual WAN と通常の VNet の両方について一般提供 (GA) されています。

- グローバル ネットワーク環境全体でセキュリティ体制を管理するには、グローバル Azure Firewall ポリシーを作成して、すべての Azure Firewall インスタンスに割り当てます。 RBAC を使用してローカル セキュリティ チームに増分ファイアウォール ポリシーをデリゲートすることにより、特定のリージョンの要件を満たすきめ細かいポリシーを設定できます。

- お客様の組織のご希望に応じて、サポートされているサードパーティの SaaS セキュリティ プロバイダーを Firewall Manager 内で構成し、そのようなソリューションを使用して送信接続を保護できます。

- インターネットからの受信 HTTP/S トラフィックを保護するには、ランディング ゾーンの VNet 内で WAF を使用します。

- Azure リージョン間でランディング ゾーンへの受信 HTTP/S 接続に対するグローバルな保護を提供するには、Azure Front Door と WAF ポリシーを使用します。

- Azure Front Door と Azure Application Gateway を使用して HTTP/S アプリを保護するときは、Azure Front Door で WAF ポリシーを使用し、Azure Application Gateway をロックダウンして、Azure Front Door からのトラフィックのみを受信します。

- East-West および South-North のトラフィックの保護とフィルター処理のために、サードパーティの NVA が必要な場合:

   - Virtual WAN ネットワーク トポロジの場合は、別の VNet (NVA VNet など) に NVA をデプロイし、リージョンの Virtual WAN ハブと、NVA へのアクセスを必要とするランディング ゾーンに、それを接続します (こちらの[記事](https://docs.microsoft.com/azure/virtual-wan/virtual-wan-route-table-portal)を参照)。
   - Virtual WAN 以外のネットワーク トポロジの場合は、サードパーティの NVA を中央ハブの VNet にデプロイします。

- 受信 HTTP/S 接続にサードパーティの NVA が必要な場合は、ランディング ゾーンの VNet 内に、保護してインターネットに公開するアプリと共に、これらをデプロイする必要があります。

- [Azure DDoS Protection Standard 保護プラン](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview)を使用して、お客様の VNet 内でホストされているすべてのパブリック エンドポイントを保護します。

- オンプレミスの境界ネットワークの概念とアーキテクチャを Azure に複製しないでください。 同様のセキュリティ機能を Azure で利用できますが、実装とアーキテクチャをクラウドに適合させる必要があります。

## <a name="planning-for-app-delivery"></a>アプリの配信の計画

このセクションでは、内部および外部に公開されるアプリを、安全で拡張性と可用性の高い方法で配信するための主な推奨事項について説明します。

**設計上の考慮事項:**

- Azure Load Balancer (内部およびパブリック) では、リージョン レベルでのアプリの配信に対して高可用性が提供されます。

- Azure Application Gateway を使用すると、リージョン レベルで HTTP/S アプリを安全に配信できます。

- Azure Front Door を使用すると、Azure リージョン間で高可用性の HTTP/S アプリを安全に配信できます。

- Azure Traffic Manager を使用すると、グローバル アプリを配信できます。

**設計上の推奨事項:**

- 内部と外部の両方に公開されるアプリの配信は、ランディング ゾーン内で実行する必要があります。

- HTTP/S アプリのセキュリティ保護された配信を行うには、App Gateway v2 を使用し、WAF の保護とポリシーを有効にします。

- HTTP/S アプリのセキュリティに Application Gateway v2 を使用できない場合は、サードパーティの NVA を使用します。

- 受信 HTTP/S 接続に使用される Azure Application Gateway v2 またはサードパーティの NVA は、セキュリティ保護対象のアプリと共に、ランディング ゾーンの VNet 内にデプロイする必要があります。

- ランディング ゾーン内のすべてのパブリック IP アドレスは、DDoS Standard 保護プランで保護されている必要があります。

- 複数の Azure リージョンにまたがるグローバル HTTP/S アプリは、Azure Front Door と WAF ポリシーを使用して配信および保護する必要があります。

- Front Door と Application Gateway を使用して HTTP/S アプリを保護するときは、Azure Front Door で WAF ポリシーを使用し、Application Gateway をロックダウンして、Front Door からのトラフィックのみを受信します。

- HTTP/S 以外のプロトコルにまたがるグローバル アプリは、Traffic Manager を使用して配信する必要があります。

## <a name="planning-for-landing-zone-network-segmentation"></a>ランディング ゾーンのネットワーク セグメント化の計画

このセクションでは、ネットワークのゼロ トラスト実装を促進するために、ランディング ゾーン内で高度にセキュリティ保護された内部ネットワーク セグメント化を提供する場合の、主な推奨事項について説明します。

**設計上の考慮事項:**

- ゼロ トラスト モデルでは、違反状態が想定され、各要求が制御されていないネットワークから送信されたかのように検証されます。

- 高度なゼロ トラスト ネットワークの実装では、完全に分散されたイングレスおよびエグレス クラウド マイクロ境界と、より深いマイクロ セグメント化が採用されています。

- ネットワーク セキュリティ グループでは、Azure サービス タグを使用して、Azure PaaS サービスへの接続を容易にすることができます。

- アプリのセキュリティ グループでは、VNet をまたがって保護が提供されることはありません。

- NSG フロー ログは、Azure Resource Manager テンプレートを使用してサポートされるようになりました。

**設計上の推奨事項:**

- サブネットの作成をランディング ゾーンの所有者にデリゲートします。 これにより、サブネットをまたいでワークロードをセグメント化する方法を定義できます (たとえば、1 つの大きいサブネット、多層アプリ、VNet に挿入されたアプリなど)。 プラットフォーム チームは、Azure Policy を使用して、特定の規則 (インターネットからの受信 SSH または RDP を拒否する、ランディング ゾーンをまたぐトラフィックを許可またはブロックする、など) が設定された NSG を、拒否のみのポリシーを持つサブネットと常に関連付けることができます。

- サブネット間のトラフィックおよびプラットフォームを横断する East-West トラフィック (ランディング ゾーン間トラフィック) を保護するには、NSG を使用する必要があります。

- アプリ チームは、サブネット レベルの NSG でアプリ セキュリティ グループを使用して、ランディング ゾーン内の多層 VM を保護する必要があります。

- ランディング ゾーン内のマイクロセグメント トラフィックには NSG とアプリ セキュリティ グループを使用し、中央の NVA を使用してトラフィック フローをフィルター処理しないようにします。

- NSG フロー ログを有効にし、それらを Traffic Analytics にフィードして、内部および外部のトラフィック フローに関する分析情報を取得します。

- NSG を使用して、ランディング ゾーン間の接続を選択的に許可します。

- Virtual WAN トポロジでは、お客様の組織がランディング ゾーン間を流れるトラフィックに対するフィルター処理とログの機能を必要とする場合、Azure Firewall を介してランディング ゾーン間のトラフィックをルーティングします。

## <a name="define-network-encryption-requirements"></a>ネットワーク暗号化の要件を定義する

このセクションでは、オンプレミスと Azure の間、および Azure リージョン間で、ネットワーク暗号化を実現するための主な推奨事項について説明します。

**設計上の考慮事項:**

- コストと使用可能な帯域幅は、エンドポイント間の暗号化トンネルの長さに反比例します。

- VPN を使用して Azure に接続する場合、インターネット上のトラフィックは IP セキュリティ (IPsec) トンネルによって暗号化されます。

- プライベート ピアリングで ExpressRoute を使用する場合、現在は、トラフィックは暗号化されません。

- MACsec 暗号化を ExpressRoute Direct に適用して、ネットワーク暗号化を実現できます。

- 現在、Azure ではグローバル VNet ピアリングに対するネイティブ暗号化は提供されていません。 現時点で、Azure リージョン間の暗号化が必要な場合は、グローバル VNet ピアリングではなく VPN ゲートウェイを使用して VNet を接続することができます。

**設計上の推奨事項:** ![暗号化のフロー](./media/enc-flows.png)
_図 8: 暗号化のフロー。_

- VPN ゲートウェイを使用してオンプレミスから Azure への VPN 接続を確立すると、上記の図の `Flow A` で示されているように、トラフィックは IPsec トンネルを使用してプロトコル レベルで暗号化されます。

- ExpressRoute Direct を使用するときは、上の図の `Flow B` で示されているように、お客様の組織のルーターと MSEE の間のレイヤー 2 レベルでトラフィックを暗号化するために、[メディア アクセス制御セキュリティ (MACsec)](https://docs.microsoft.com/azure/expressroute/expressroute-howto-MACsec) を構成します。

- MACsec がオプションではない (たとえば、ExpressRoute Direct を使用していない) Virtual WAN のシナリオでは、Virtual WAN VPN ゲートウェイを使用して、ExpressRoute プライベート ピアリング経由で IPsec トンネルを確立します。 これは、上の図の `Flow C` に示されています。

- 仮想 WAN 以外のシナリオで、MACsec がオプションではない場合 (たとえば、ExpressRoute Direct を使用していない場合)、現在の唯一のオプションは、サードパーティの NVA 使用して ExpressRoute プライベート ピアリング経由で IPsec トンネルを確立すること、または Microsoft ピアリングを使用して ExpressRoute 経由で VPN トンネルを確立することです。

- Azure リージョン間のトラフィックを暗号化する必要がある場合は、VPN ゲートウェイを使用してリージョン間で VNet を接続します。

- Azure で提供されるネイティブ ソリューション (上記の Flow B および C で示されているもの) がお客様の要件を満たしていない場合に、ExpressRoute プライベート ピアリング経由のトラフィックを暗号化するには、Azure でサードパーティの NVA を使用します。

## <a name="planning-for-traffic-inspection"></a>トラフィック検査の計画

多くの業界では、組織は、詳細な検査と分析のために、Azure のトラフィック (特に受信と送信のインターネット トラフィック) がネットワーク パケット コレクターにミラー化されることを望みます。 このセクションでは、Azure Virtual Network 内のトラフィックをミラーリングまたはタップするときの主な考慮事項と推奨される方法について説明します。

**設計上の考慮事項:**

<!-- docsTest:ignore TAP -->

- [Azure Virtual Network TAP](https://docs.microsoft.com/azure/virtual-network/virtual-network-tap-overview) はプレビュー段階ですが、使用可能かどうかについては `azurevnettap@microsoft.com` にお問い合わせください。

- Network Watcher でのパケット キャプチャは一般提供されていますが、キャプチャの最大期間は 5 時間に制限されています。

**設計上の推奨事項:**

Azure Virtual Network TAP の代わりの手段としては、次のオプションを評価してください。

- キャプチャ ウィンドウは制限されていますが、Network Watcher パケットを使用してキャプチャします。

- NSG フロー ログ v2 で必要な詳細レベルが提供されるかどうかを評価します。

- 継続的なディープ パケット検査が必要なシナリオでは、サードパーティのソリューションを使用します。

- トラフィックをミラー化するためのカスタム ソリューションを開発しないでください。 これは小規模なシナリオでは許容されるかもしれませんが、複雑さとサポートの問題が発生する可能性があるため、このアプローチを大規模に使用することは推奨されません。
