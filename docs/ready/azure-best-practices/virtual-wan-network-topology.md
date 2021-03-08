---
title: Virtual WAN のネットワーク トポロジ
description: Microsoft Azure の仮想ワイド エリア ネットワークに関連する主な設計上の考慮事項と推奨事項について確認します。
author: JefferyMitchell
ms.author: brblanch
ms.date: 02/18/2021
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.custom: think-tank
ms.openlocfilehash: 44d8ba71ced1ad925cec5df17ef17e0862233857
ms.sourcegitcommit: b8f8b7631aabaab28e9705934bf67dad15e3a179
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101801373"
---
<!-- docutune:casing "Local, Standard, or Premium SKU" "ExpressRoute Standard or Premium circuits"-->

# <a name="virtual-wan-network-topology-microsoft-managed"></a>Virtual WAN ネットワーク トポロジ (Microsoft 管理)

Microsoft Azure の仮想ワイド エリア ネットワーク (Virtual WAN) に関連する主な設計上の考慮事項と推奨事項について説明します。

![Virtual WAN ネットワーク トポロジを示す図。](./media/virtual-wan-topology.png)

"*図 1:Virtual WAN のネットワーク トポロジ。*

**設計上の考慮事項:**

- [Azure Virtual WAN](/azure/virtual-wan/virtual-wan-about) は Microsoft が管理するソリューションであり、エンド ツー エンドでグローバルな動的トランジット接続が既定で提供されます。 Virtual WAN ハブにより、ネットワーク接続を手動で構成する必要がなくなります。 たとえば、お客様は、グローバルなトランジット接続を有効にするためにユーザー定義ルート (UDR) またはネットワーク仮想アプライアンス (NVA) を管理する必要はありません。

- Virtual WAN を使用すると、[ハブ アンド スポークのネットワーク アーキテクチャ](/azure/virtual-wan/virtual-wan-global-transit-network-architecture)が作成されるため、Azure 内、およびオンプレミスから Azure へのエンド ツー エンドのネットワーク接続が簡素化されます。 次の図に示すように、このアーキテクチャは、複数の Azure リージョンとオンプレミスの場所をサポートする (Any-to-Any 接続) ように容易にスケーリングできます。

  ![Virtual WAN でのグローバル トランジット ネットワークを示す図。](./media/global-transit.png)

 "*図 2:Virtual WAN でのグローバル トランジット ネットワーク。*

- Virtual WAN の Any-to-Any の推移的な接続では、(同じリージョン内およびリージョン間での) 次のパスがサポートされています。

  - 仮想ネットワークから仮想ネットワーク
  - 仮想ネットワークからブランチ
  - ブランチから仮想ネットワーク
  - ブランチからブランチ

- Virtual WAN ハブは、Microsoft が管理するリソースのデプロイに限定されます。 その中にデプロイできるリソースは、仮想ネットワーク ゲートウェイ (ポイント対サイト VPN、サイト間 VPN、Azure ExpressRoute)、Firewall Manager を介した Azure Firewall、ルート テーブル、ベンダー固有の SD-WAN 機能用の[一部のネットワーク仮想アプライアンス (NVA)](/azure/virtual-wan/about-nva-hub) だけです。

- Virtual WAN は、[Virtual WAN のいくつかの Azure サブスクリプションの制限](/azure/azure-resource-manager/management/azure-subscription-service-limits#virtual-wan-limits)による限界があります。

- ネットワーク間の推移的な接続 (リージョン内、およびハブ間を経由したリージョン間) は、現在一般提供 (GA) されています。

- すべての仮想ハブに Microsoft が管理するルーティング機能が存在するため、Standard Virtual WAN 内の仮想ネットワーク間のトランジット接続が有効になります。 各ハブでは、VNet 間のトラフィックに対して最大 50 Gbps の合計スループットがサポートされます。

- 1 つの Azure Virtual WAN ハブでは、直接接続されているすべての VNet にわたり、特定の最大数の VM ワークロードがサポートされます。これについては、[Azure Virtual WAN の制限](/azure/azure-resource-manager/management/azure-subscription-service-limits#virtual-wan-limits)に関するセクションで説明されています。

- Virtual WAN は、さまざまな [SD-WAN プロバイダー](/azure/virtual-wan/virtual-wan-locations-partners)と統合されています。

- 多くの管理サービス プロバイダーから、Virtual WAN 向けの[管理サービス](/azure/networking/networking-partners-msp)が提供されています。

- Virtual WAN 内のユーザー VPN (ポイント対サイト) ゲートウェイは、仮想ハブあたり最大 20 Gbps の合計スループットと 10,000 件のクライアント接続までスケールアップすることができます。

- Virtual WAN 内のサイト間 VPN ゲートウェイは、最大 20 Gbps の合計スループットまでスケールアップすることができます。

- Local、Standard、Premium のいずれかの SKU を使用した ExpressRoute 回線は、Virtual WAN ハブに接続できます。

- ExpressRoute Global Reach でサポートされている場所にある ExpressRoute の Standard または Premium 回路を Virtual WAN ExpressRoute ゲートウェイに接続し、Virtual WAN のすべての転送機能 (VPN 間、VPN、ExpressRoute) を利用できます。 Global Reach でサポートされていない場所にある ExpressRoute の Standard または Premium 回線は、Azure リソースには接続できますが、Virtual WAN の転送機能は使用できません。

- ExpressRoute Local が Azure Virtual WAN ハブでサポートされるのは、Virtual WAN ハブに接続されたスポーク VNet が Virtual WAN ハブと同じリージョンに存在する場合です。

- 現在一般提供されている Azure Firewall Manager を使用すると、Virtual WAN ハブに Azure Firewall をデプロイできます。 [Azure Firewall Manager の概要](/azure/firewall-manager/overview)で、セキュリティ保護付き仮想ハブと最新の[制約](/azure/firewall-manager/overview#known-issues)をご確認ください。

- Azure Firewall が Virtual WAN ハブ自体 (セキュリティ保護付き仮想ハブ) の内側にデプロイされている場合、Azure Firewall を経由した Virtual WAN のハブ間トラフィックは現在サポートされていません。 要件に応じていくつかの回避策があります。たとえば、[Azure Firewall をスポーク仮想ネットワークに](/azure/virtual-wan/scenario-route-through-nva)配置したり、トラフィックのフィルター処理に NSG を使用したりします。

**設計上の推奨事項:**

- Azure リージョンとオンプレミスの場所にわたってグローバルなトランジット接続を必要とする、Azure での新しく大規模な、またはグローバルなネットワーク デプロイには Virtual WAN をお勧めします。 そのようにすると、Azure ネットワークに対して推移的なルーティングを手動で設定する必要がなくなります。

  次の図では、ヨーロッパと米国にデータセンターが分散している、グローバルなエンタープライズのデプロイの例を示します。 また、このデプロイには、両方のリージョンに多数のブランチ オフィスもあります。 この環境は、Azure Virtual WAN と [ExpressRoute Global Reach](/azure/expressroute/expressroute-global-reach) によってグローバルに接続されています。

  ![ネットワーク トポロジの例の図。](./media/global-reach-topology.png)

 "*図 3:ネットワーク トポロジのサンプル。*

- Azure リージョンごとに Virtual WAN ハブを使用し、共通のグローバル Azure Virtual WAN を経由して、Azure リージョンをまたいで複数のランディング ゾーンを接続します。

- VNet およびブランチ間でトラフィックをさらに分割するには、[仮想ハブのルーティング](/azure/virtual-wan/about-virtual-hub-routing)機能を使用します。

- ExpressRoute を使用して、Virtual WAN ハブをオンプレミスのデータセンターに接続します。

- DNS サーバーなどの必要な共有サービスを、専用のスポーク仮想ネットワークにデプロイします。 お客様がデプロイした共有リソースを Virtual WAN ハブ自体の内側にデプロイすることはできません。

- サイト間 VPN を介してブランチとリモート拠点を最も近い Virtual WAN ハブに接続するか、SD-WAN パートナー ソリューションを使用してブランチを Virtual WAN に接続できるようにします。

- ポイント対サイト VPN を使用して、ユーザーを Virtual WAN ハブに接続します。

- リソースが異なるリージョンにある場合でも、Azure 内のリソース間の通信が Microsoft のバックボーン ネットワーク経由で行われるように、"Azure 内のトラフィックは Azure 内に留まる" 原則に従います。

- インターネット送信の保護とフィルター処理を行うには、仮想ハブに Azure Firewall をデプロイすることを検討してください。

- 東部/西部または南部/北部のトラフィック保護とフィルター処理にパートナー NVA が必要な場合、Azure Virtual WAN では仮想ハブにそのようなセキュリティ NVA をデプロイすることが許可されていないため、[こちら](/azure/virtual-wan/scenario-route-through-nva)で説明されているようにそれらの NVA を別個のスポーク仮想ネットワークにデプロイして静的ルーティングを構成することが要件を満たしているかどうかを評価してください。 代わりの方法として、ハブ アンド スポーク モデルに基づく従来のネットワーク トポロジを検討してください。この場合、パートナー NVA を通常のハブ仮想ネットワークにデプロイすることができます。

- パートナーのネットワーク テクノロジと NVA をデプロイするときは、パートナー ベンダーのガイダンスに従って、Azure ネットワークと競合する構成がないようにします。

- Virtual WAN に基づいていないハブ アンド スポーク ネットワーク トポロジから移行するブラウンフィールドのシナリオについては、「[Azure Virtual WAN に移行する](/azure/virtual-wan/migrate-from-hub-spoke-topology)」を参照してください。

- 接続サブスクリプション内に Azure Virtual WAN リソースと Azure Firewall リソースを作成します。

- Virtual WAN の仮想ハブごとに、500 より多くの仮想ネットワーク接続を作成しないでください。

- デプロイを慎重に計画し、ネットワーク アーキテクチャが [Azure Virtual WAN の制限](/azure/azure-resource-manager/management/azure-subscription-service-limits#virtual-wan-limits)内であることを確認します。

- [Azure Monitor で Virtual WAN に関する分析情報 (プレビュー)](/azure/virtual-wan/azure-monitor-insights) を使用して、Virtual WAN のエンドツーエンド トポロジのほか、状態と[主要指標](/azure/virtual-wan/azure-monitor-insights#detailed)を監視します。
