---
title: 他のクラウド プロバイダーへの接続
description: Azure エンタープライズ規模のランディング ゾーン アーキテクチャを Oracle Cloud Infrastructure (OCI) に統合するために、さまざまな接続方法に関連する主な設計上の考慮事項と推奨事項を確認します。
author: alexandreweiss
ms.author: brblanch
ms.date: 02/18/2021
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.custom: think-tank
ms.openlocfilehash: 210a097d8e03a66e9951e7803de628d3fd5913a6
ms.sourcegitcommit: 9e4bc0e233a24642853f5e8acbeb9746b2444024
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102117526"
---
# <a name="connectivity-to-other-cloud-providers"></a>他のクラウド プロバイダーへの接続

Azure エンタープライズ規模のランディング ゾーン アーキテクチャをその他のクラウド プロバイダーに統合するために、さまざまな接続方法に関連する主な設計上の考慮事項と推奨事項を確認します。

## <a name="oracle-cloud-infrastructure-oci"></a>Oracle Cloud Infrastructure (OCI)

このセクションでは、Azure エンタープライズ規模のランディング ゾーン アーキテクチャを Oracle Cloud Infrastructure (OCI) に統合するためのさまざまな接続方法について説明します。

**設計上の考慮事項:**

- ExpressRoute と FastConnect を使用すると、プライベート IP アドレス空間が重複していない場合、お客様は Azure の仮想ネットワークを OCI の仮想クラウド ネットワークと接続できます。 この接続が確立されると、Azure 仮想ネットワーク内のリソースは、OCI 仮想クラウド ネットワーク内のリソースと、どちらも同じネットワーク内にあるかのように通信できます。

- Azure ExpressRoute [FastPath](/azure/expressroute/about-fastpath) は、オンプレミスと Azure の 2 つのネットワーク (このシナリオでは OCI と Azure) の間でデータ パスのパフォーマンスを向上させるように設計されています。 FastPath を有効にすると、ExpressRoute ゲートウェイはバイパスされ、ネットワーク トラフィックが仮想ネットワーク内の仮想マシンに直接送信されます。

  - FastPath はすべての ExpressRoute 回線で使用できます。

  - FastPath により、仮想ネットワーク ゲートウェイは、ルート交換のため、引き続き作成される必要があります。 仮想ネットワーク ゲートウェイから、ExpressRoute ゲートウェイ用に Ultra Performance SKU または ErGw3AZ SKU のいずれかを使用して、ルート管理を有効にする必要があります。

- ExpressRoute FastPath には、Azure Virtual WAN ハブや VNet ピアリングなど、現在[サポートされていない](/azure/expressroute/about-fastpath#supported-features)機能があります。

- [ExpressRoute Global Reach](/azure/expressroute/expressroute-global-reach) を使用して、ExpressRoute 回線を介してオンプレミスから OCI への通信を有効にすることができますが、追加の帯域幅コスト ([Azure 料金計算ツール](https://azure.microsoft.com/pricing/calculator/)を使用して計算可能) が発生する場合があります。 これは、ExpressRoute 回線を使用して、オンプレミスから Oracle に大量のデータを移行する場合に特に重要な考慮事項です。

- [Availability Zones](/azure/availability-zones/az-overview#availability-zones) をサポートする Azure リージョンで、Azure のワークロードをいずれかのゾーンに配置すると、待機時間にわずかな影響を与える場合があります。 可用性とパフォーマンスの要件のバランスを取るようにアプリケーションを設計します。

- Azure と OCI 間の相互通信は、[特定のリージョン](/azure/virtual-machines/workloads/oracle/oracle-oci-overview#region-availability)でのみ使用できます。

- Azure と OCI 間の相互通信に関する詳細なドキュメントについては、[Microsoft Azure と Oracle Cloud Infrastructure を統合した Oracle アプリケーション ソリューション](/azure/virtual-machines/workloads/oracle/oracle-oci-overview)に関する記事、または [Oracle のドキュメント](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/azure.htm)を参照してください。

**設計上の推奨事項:**

- **接続** サブスクリプションで Azure と OCI の相互接続に使用される ExpressRoute 回線を作成します。

- 次の図に示すように、Azure を OCI に相互接続するために使用される ExpressRoute 回線をハブ VNet または Virtual WAN ハブに接続することで、従来のハブおよびスポークのアーキテクチャまたは Azure Virtual WAN ベースのネットワーク トポロジに基づいて Azure ネットワーク アーキテクチャを相互接続できます。

  ![ハブおよびスポークを使用した Azure から OCI への接続を示す図。](./media/azure-oci-hub-and-spoke.png)

  *図 1: ExpressRoute を介した Azure と OCI の相互通信*

- アプリケーションで Azure と OCI の間の待機時間を最小限に抑える必要がある場合は、ExpressRoute ゲートウェイと FastPath を有効にした単一の VNet にアプリケーションをデプロイすることを検討してください。

  ![単一の VNet を使用した Azure から OCI への接続を示す図。](./media/azure-oci-one-vnet.png)

  *図 2: 単一の VNet を使用した Azure と OCI の相互通信*

- Availability Zones 全体に Azure リソースをデプロイする場合は、異なる Availability Zones にある Azure VM から OCI リソースへの待機時間テストを実行して、3 つの Availability Zones のどれが OCI リソースへの待機時間が最小になるのかを把握します。

- Azure のリソースとテクノロジを使用して OCI でホストされている Oracle リソースを操作するには、次の方法があります。

  - **Azure から:** スポーク VNet にジャンプボックスをデプロイします。 次の図に示すように、ジャンプボックスから、OCI の仮想クラウド ネットワークにアクセスできます。

    ![ジャンプボックスと VNet を使用した Azure から OCI への接続を示す図。](./media/azure-oci-jumpbox-one-vnet.png)

    *図 3: Azure からジャンプボックスを介した OCI リソースの管理*

  - **オンプレミスから:** ExpressRoute Global Reach を使用して、(オンプレミスから Azure に接続する) 既存の ExpressRoute 回線を OCI ExpressRoute 回線 (Azure と OCI を相互接続する) にバインドします。 このようにして、Microsoft Enterprise Edge (MSEE) ルーターが両方の ExpressRoute 回線の間の中央のルーティング ポイントになります。

    ![Global Reach を介した Azure から OCI への接続を示す図。](./media/azure-oci-gr-hub-and-spoke.png)

  *図 4: オンプレミスから ExpressRoute Global Reach を使用した OCI リソースの管理*
