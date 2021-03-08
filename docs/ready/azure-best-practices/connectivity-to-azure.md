---
title: Azure への接続性
description: オンプレミスと Azure を接続するネットワーク トポロジに関する主要な設計上の考慮事項と推奨事項について説明します。
author: JefferyMitchell
ms.author: brblanch
ms.date: 02/18/2021
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.custom: think-tank
ms.openlocfilehash: 5989d9b92cf3387cd4cf5e82d74212b50132aa7c
ms.sourcegitcommit: b8f8b7631aabaab28e9705934bf67dad15e3a179
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101800108"
---
# <a name="connectivity-to-azure"></a>Azure への接続性

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
