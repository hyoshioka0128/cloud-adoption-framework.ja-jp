---
title: Azure のネットワーク トポロジを定義する
description: Azure 内のネットワーク トポロジに関する主要な設計上の考慮事項と推奨事項について確認します。
author: JefferyMitchell
ms.author: brblanch
ms.date: 02/18/2021
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.custom: think-tank
ms.openlocfilehash: d9c6e3d2561a0252ff586feb5771b48675cbc34a
ms.sourcegitcommit: b8f8b7631aabaab28e9705934bf67dad15e3a179
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101801341"
---
# <a name="define-an-azure-network-topology"></a>Azure のネットワーク トポロジを定義する

ネットワーク トポロジは、アプリケーションの相互通信方法が定義されているため、エンタープライズ規模のアーキテクチャの重要な要素です。 このセクションでは、Azure デプロイのためのテクノロジとトポロジのアプローチについて説明します。 2 つの主要なアプローチである、Azure Virtual WAN に基づくトポロジと従来のトポロジに焦点を当てます。

[Virtual WAN は、大規模な相互接続の要件を満たすために使用されます](../azure-best-practices/virtual-wan-network-topology.md)。 Microsoft の管理サービスであるため、ネットワークの全体的な複雑さが軽減され、組織のネットワークの近代化にも役立ちます。 次のいずれかの点で要件を満たしている場合は、Virtual WAN トポロジが最も適している可能性があります。

- 組織では複数の Azure リージョンにリソースをデプロイする予定であり、これらの Azure リージョン内の VNet と複数のオンプレミスの場所の間でグローバルに接続する必要があります。
- 組織では、ソフトウェアによる WAN (SD WAN) デプロイを通じて、またはネイティブ IPsec 終端に 30 個を超えるブランチ サイトを必要とする、大規模なブランチ ネットワークを直接 Azure に統合する予定です。
- VPN と ExpressRoute の間に推移的なルーティングが必要です。 例: サイト間 VPN を介して接続されたリモート ブランチ、またはポイント対サイト VPN 経由を介して接続されたリモート ユーザーは、DC に接続された ExpressRoute に Azure 経由で接続する必要があります。

[従来のハブおよびスポークのネットワーク トポロジ](../azure-best-practices/traditional-azure-networking-topology.md)は、顧客が管理するルーティングとセキュリティを使用して、カスタマイズされた安全な大規模ネットワークを Azure で構築するのに役立ちます。 次のいずれかの点で要件を満たしている場合は、従来のトポロジが最も適している可能性があります。

- 組織は 1 つまたは複数の Azure リージョンにリソースをデプロイする予定です。複数の Azure リージョンをまたいだトラフィック (たとえば、2 つの異なる Azure リージョンをまたいだ 2 つの仮想ネットワーク間のトラフィック) が想定されていますが、すべての Azure リージョンにわたるフル メッシュ ネットワークは必要ありません。
- リージョンあたりのリモートまたはブランチの場所が少ない。 つまり、必要な IPsec サイト間トンネルが 30 未満。
- Azure ネットワークのルーティング ポリシーを手動で構成するために、完全にきめ細かく制御する必要がある。

## <a name="virtual-wan-network-topology-microsoft-managed"></a>Virtual WAN ネットワーク トポロジ (Microsoft 管理)

![Virtual WAN ネットワーク トポロジを示す図。](./media/virtual-wan-topology.png)

"*図 1:Virtual WAN のネットワーク トポロジ。*

## <a name="traditional-azure-networking-topology"></a>従来の Azure ネットワーク トポロジ

![従来の Azure ネットワーク トポロジを示す図。](./media/customer-managed-topology.png)

*図 2:従来の Azure ネットワーク トポロジ。*
