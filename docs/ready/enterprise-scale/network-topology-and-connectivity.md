---
title: ネットワーク トポロジと接続の概要
description: Microsoft Azure との間や、その内部でのネットワークと接続に関する主要な設計上の考慮事項とベスト プラクティスについて確認します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 02/18/2021
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.custom: internal
ms.openlocfilehash: 7fe77aaade7da6a4eabcc935c0d26678ac07deb4
ms.sourcegitcommit: 9e4bc0e233a24642853f5e8acbeb9746b2444024
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102112794"
---
<!-- docutune:casing "Azure VPN Gateway" L7 -->
<!-- cSpell:ignore autoregistration BGPs MACsec MPLS MSEE onprem privatelink VPNs -->

# <a name="network-topology-and-connectivity-overview"></a>ネットワーク トポロジと接続の概要

この一連の記事では、Microsoft Azure との間や、その内部でのネットワークと接続に関する主要な設計上の考慮事項とベスト プラクティスについて確認します。

## <a name="plan-for-ip-addressing"></a>IP アドレス指定を計画する

組織は、オンプレミスの場所と Azure リージョンの間で IP アドレス空間が重複しないように、Azure での IP アドレスを計画することが重要です。
[このセクションでは、ハイブリッド実装の IP アドレス指定の計画に関するガイダンスを提供します。](../azure-best-practices/plan-for-ip-addressing.md)

## <a name="configure-dns-and-name-resolution-for-on-premises-and-azure-resources"></a>オンプレミスと Azure のリソースに対して DNS と名前解決を構成する

ドメイン ネーム システム (DNS) は、エンタープライズ規模のアーキテクチャ全体における重要な設計トピックです。 DNS への既存の投資を使用することを望む組織があります。 または、クラウドの導入を、内部 DNS インフラストラクチャを最新化し、Azure のネイティブ機能を使用する機会と考える組織もあります。
[このセクションでは、ハイブリッド実装の DNS および名前解決の計画に関するガイダンスを紹介します。](../azure-best-practices/dns-for-on-premises-and-azure-resources.md)

## <a name="define-an-azure-network-topology"></a>Azure のネットワーク トポロジを定義する

ネットワーク トポロジは、アプリケーションの相互通信方法が定義されているため、エンタープライズ規模のアーキテクチャの重要な要素です。 [このセクションでは、Azure デプロイのためのテクノロジとトポロジのアプローチについて説明します。](../azure-best-practices/define-an-azure-network-topology.md) 2 つの主要なアプローチである、Azure Virtual WAN に基づくトポロジと従来のトポロジに焦点を当てます。

## <a name="virtual-wan-network-topology"></a>Virtual WAN のネットワーク トポロジ

[このセクションでは、Azure Virtual WAN ネットワーク トポロジを実装するためのオプションについて説明します。](../azure-best-practices/virtual-wan-network-topology.md)

## <a name="traditional-azure-networking-topology"></a>従来の Azure ネットワーク トポロジ

[このセクションでは、従来の Azure ネットワーク トポロジを実装するためのオプションについて説明します。](../azure-best-practices/traditional-azure-networking-topology.md)

## <a name="connectivity-to-azure"></a>Azure への接続性

[このセクションでは、ネットワーク トポロジを拡張し、オンプレミスの場所から Azure に接続するために推奨されるモデルを検討します。](../azure-best-practices/connectivity-to-azure.md)

## <a name="private-link-and-dns-integration-at-scale"></a>Private Link と DNS の大規模な統合

[このセクションでは、ハブおよびスポークのネットワーク アーキテクチャで、PaaS サービス用の Azure Private Link を Azure プライベート DNS ゾーンと統合する方法について説明します。](../azure-best-practices/private-link-and-dns-integration-at-scale.md)

## <a name="connectivity-to-azure-paas-services"></a>Azure PaaS サービスへの接続

これまでの接続セクションを基にして、[このセクションでは、Azure PaaS サービスを使用するための推奨される接続方法について説明します。](../azure-best-practices/connectivity-to-azure-paas-services.md)

## <a name="plan-for-inbound-and-outbound-internet-connectivity"></a>受信と送信のインターネット接続を計画する

[このセクションでは、パブリック インターネットに対する受信接続と送信接続に対して推奨される接続モデルについて説明します。](../azure-best-practices/plan-for-inbound-and-outbound-internet-connectivity.md)

## <a name="plan-for-application-delivery"></a>アプリケーション配信の計画

[このセクションでは、内部および外部に公開されるアプリケーションを、安全で拡張性と可用性の高い方法で配信するための主な推奨事項について説明します。](../azure-best-practices/plan-for-app-delivery.md)

## <a name="plan-for-landing-zone-network-segmentation"></a>ランディング ゾーンのネットワーク セグメント化を計画する

[このセクションでは、ネットワークのゼロ トラスト実装を促進するために、ランディング ゾーン内で高度にセキュリティ保護された内部ネットワーク セグメント化を提供する場合の、主な推奨事項について説明します。](../azure-best-practices/plan-for-landing-zone-network-segmentation.md)

## <a name="define-network-encryption-requirements"></a>ネットワーク暗号化の要件を定義する

[このセクションでは、オンプレミスと Azure の間、および Azure リージョン間で、ネットワーク暗号化を実現するための主な推奨事項について説明します。](../azure-best-practices/define-network-encryption-requirements.md)

## <a name="plan-for-traffic-inspection"></a>トラフィックの検査を計画する

多くの業界では、組織は、詳細な検査と分析のために、Azure のトラフィックがネットワーク パケット コレクターにミラー化されることを望みます。 この要件では、通常、受信と送信のインターネット トラフィックに焦点が当てられています。 [このセクションでは、Azure Virtual Network 内のトラフィックをミラーリングまたはタップするときの主な考慮事項と推奨される方法について説明します。](../azure-best-practices/plan-for-traffic-inspection.md)

## <a name="connectivity-to-other-cloud-providers"></a>他のクラウド プロバイダーへの接続

[このセクションでは、Azure エンタープライズ規模のランディング ゾーン アーキテクチャを他のクラウド プロバイダーに統合するためのさまざまな接続方法について説明します。](../azure-best-practices/connectivity-to-other-providers.md)
