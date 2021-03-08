---
title: 受信と送信のインターネット接続を計画する
description: パブリック インターネットに対する受信接続と送信接続に対して推奨される接続モデルについて説明します。
author: JefferyMitchell
ms.author: brblanch
ms.date: 02/18/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.custom: think-tank, csu
ms.openlocfilehash: 7a2d6126f5d529c466d70b6d65a1ced10e7bf34c
ms.sourcegitcommit: b8f8b7631aabaab28e9705934bf67dad15e3a179
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101800101"
---
# <a name="plan-for-inbound-and-outbound-internet-connectivity"></a>受信と送信のインターネット接続を計画する

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

- Azure Front Door と Azure Application Gateway を使用して HTTP/S アプリケーションを保護する場合は、Azure Front Door で WAF ポリシーを使用します。 Azure Application Gateway をロックダウンして、Azure Front Door からのトラフィックのみを受信します。

- East/West または South/North のトラフィックの保護とフィルター処理のために、パートナーの NVA が必要な場合:

  - Virtual WAN ネットワーク トポロジの場合は、別の仮想ネットワーク (NVA 仮想ネットワークなど) に NVA をデプロイします。 それを、リージョンの Virtual WAN ハブと、NVA にアクセスする必要があるランディング ゾーンに接続します。 [こちらの記事](/azure/virtual-wan/scenario-route-through-nva)でプロセスについて説明されています。
  - Virtual WAN 以外のネットワーク トポロジの場合は、パートナーの NVA を中央ハブの仮想ネットワークにデプロイします。

- 受信 HTTP/S 接続にパートナーの NVA が必要な場合は、ランディング ゾーンの仮想ネットワーク内に、保護してインターネットに公開するアプリケーションと共に、これらをデプロイします。

- 仮想ネットワーク内でホストされているすべてのパブリック エンドポイントを保護するには、[Azure DDoS Protection Standard 保護プラン](/azure/ddos-protection/ddos-protection-overview)を使用します。

- オンプレミスの境界ネットワークの概念とアーキテクチャを Azure に複製しないでください。 同様のセキュリティ機能を Azure で利用できますが、実装とアーキテクチャをクラウドに適合させる必要があります。
