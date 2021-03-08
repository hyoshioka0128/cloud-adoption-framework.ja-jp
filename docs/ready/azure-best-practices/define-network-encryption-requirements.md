---
title: ネットワーク暗号化の要件を定義する
description: オンプレミスと Azure 間のネットワーク暗号化に関する主要な設計上の考慮事項と推奨事項について説明します。
author: JefferyMitchell
ms.author: brblanch
ms.date: 02/18/2021
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.custom: think-tank
ms.openlocfilehash: fbe328ba0c9c9922c9f4cb7ba0ae53f2b61f6d6f
ms.sourcegitcommit: b8f8b7631aabaab28e9705934bf67dad15e3a179
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101801337"
---
# <a name="define-network-encryption-requirements"></a>ネットワーク暗号化の要件を定義する

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

*図 1: 暗号化のフロー。*

- VPN ゲートウェイを使用してオンプレミスから Azure への VPN 接続を確立すると、トラフィックは IPsec トンネルを通してプロトコル レベルで暗号化されます。 上の図では、フロー `A` でこの暗号化が示されています。

- ExpressRoute Direct を使用するときは、組織のルーターと MSEE の間のレイヤー 2 レベルでトラフィックを暗号化するために、[MACsec](/azure/expressroute/expressroute-howto-MACsec) を構成します。 図では、フロー `B` でこの暗号化が示されています。

- MACsec がオプションではない (たとえば、ExpressRoute Direct を使用していない) Virtual WAN のシナリオでは、Virtual WAN VPN ゲートウェイを使用して、[ExpressRoute プライベート ピアリング経由で IPsec トンネル](/azure/virtual-wan/vpn-over-expressroute)を確立します。 図では、フロー `C` でこの暗号化が示されています。

- Virtual WAN 以外のシナリオで、MACsec がオプションではない場合 (たとえば、ExpressRoute Direct を使用していない場合)、オプションは以下のものだけです。

  - パートナーの NVA を使用して、ExpressRoute プライベート ピアリング経由で IPsec トンネルを確立します。
  - Microsoft ピアリングを使用して ExpressRoute 経由で VPN トンネルを確立します。
  - ExpressRoute プライベート ピアリング経由のサイト間 VPN 接続を構成する ([プレビュー](/azure/vpn-gateway/site-to-site-vpn-private-peering)) 機能を評価します。

- Azure リージョン間のトラフィックを暗号化する必要がある場合は、グローバル VNet ピアリングを使用してリージョン間で仮想ネットワークを接続します。

- Azure のネイティブ ソリューション (図のフロー `B` と `C` で示されているもの) で要件が満たされない場合は、Azure でパートナーの NVA を使用して、ExpressRoute プライベート ピアリング経由でトラフィックを暗号化します。
