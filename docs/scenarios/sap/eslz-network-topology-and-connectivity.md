---
title: SAP 移行のエンタープライズ規模のネットワーク トポロジと接続性
description: Microsoft Azure と SAP デプロイの間や、その内部でのネットワークと接続に関する主要な設計上の考慮事項とベスト プラクティスについて確認します。
author: JefferyMitchell
ms.author: brblanch
ms.date: 03/01/2021
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.custom: think-tank, e2e-sap
ms.openlocfilehash: 856f9aebd4527c20e24d6b47627a8d378546b35a
ms.sourcegitcommit: 5716a8165934bd69d02d9d3641785039196aee3a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "105734503"
---
# <a name="enterprise-scale-network-topology-and-connectivity-for-an-sap-migration"></a>SAP 移行のエンタープライズ規模のネットワーク トポロジと接続性

この記事では、Microsoft Azure と SAP デプロイの間や、その内部でのネットワークと接続に関する主要な設計上の考慮事項とベスト プラクティスについて検証しています。

## <a name="plan-for-ip-addressing"></a>IP アドレス指定を計画する

次のことを保証するために、Azure で IP アドレス指定を計画することが重要です。

- IP アドレス空間は、オンプレミスの場所と Azure リージョン間で重複しません。
- 仮想ネットワーク (VNet) には、正しいアドレス空間が含まれています。
- サブネット構成の適切な計画が事前に行われています。

次のアーキテクチャの図は、SAP on Azure 構築セットにおけるエンタープライズ規模のネットワークの考慮事項を示しています。

 ![SAP on Azure 構築セットにおけるエンタープライズ規模のネットワークの考慮事項を示す図。](./media/ntc-architecture.png#lightbox)

*図 1: SAP on Azure 構築セットにおけるエンタープライズ規模のネットワークの考慮事項を示す図。*

**SAP 実装の設計に関する考慮事項:**

サブネットを特定のサービスに [専用](/azure/virtual-network/virtual-network-for-azure-services#services-that-can-be-deployed-into-a-virtual-network) に委任してから、それらのサービスのインスタンスをサブネット内に作成することができます。 Azure では、VNet に複数の委任されたサブネットを作成することができますが、VNet 内に存在できる委任されたサブネットは 1 つだけです (Azure NetApp Files)。 Azure NetApp Files 用に委任されたサブネットを複数使用している場合、新しいボリュームを作成しようとすると失敗します。

**ユース ケース:**

委任されたサブネットは Azure NetApp Files を実装するために必要です。これは、共有ファイルシステムを使用した SAP のデプロイ時によく使用されます。 委任されたサブネットが必要になるのは、負荷分散や SAP BusinessObjects ビジネスインテリジェンス (負荷分散 SAP Web アプリケーションサーバー) などの Azure アプリケーションゲートウェイ SAP シナリオでの Application Gateway に限られます。

## <a name="configure-dns-and-name-resolution-for-on-premises-and-azure-resources"></a>オンプレミスと Azure のリソースに対して DNS と名前解決を構成する

ドメイン ネーム システム (DNS) は、エンタープライズ規模のアーキテクチャ全体における重要な設計トピックです。 DNS への既存の投資を使用することを望む組織があります。 または、クラウドの導入を、内部 DNS インフラストラクチャを最新化し、Azure のネイティブ機能を使用する機会と考える組織もあります。

**SAP 実装の設計に関する推奨事項:**

次の推奨事項は、移行中に仮想マシンの DNS または仮想名が変更されない場合に使用します。

**ユース ケース:**

- バックグラウンド DNS と仮想名は、SAP ランドスケープで多くのシステム インターフェイスを接続します。顧客は、開発者が時間の経過と共に定義するインターフェイスを認識するだけである場合があります。 移行後に仮想名または DNS 名が変更されると、さまざまなシステム間で接続の問題が発生します。このような問題を回避するには、DNS 別名を保持することをお勧めします。

- さまざまな DNS ゾーンを使用して、各環境 (サンドボックス、開発、運用前、および運用) を相互に区別します。 ただし、独自の VNet を使用した SAP デプロイの場合は例外です。ここでは、プライベート DNS ゾーンが不要な場合があります。

## <a name="define-an-azure-network-topology"></a>Azure のネットワーク トポロジを定義する

エンタープライズ規模のランディング ゾーンでは、2 つのネットワーク トポロジがサポートされています。1 つは、Azure Virtual WAN に基づくトポロジで、もう 1 つはハブとスポークのアーキテクチャに基づく従来のネットワーク トポロジです。 このセクションでは、両方のデプロイ モデルの SAP の構成と方法をお勧めします。

組織で次のことを計画している場合は、仮想 WAN に基づくネットワーク トポロジを使用します。

- 複数の Azure リージョンにリソースをデプロイし、Azure とオンプレミスの両方にグローバルな場所から接続します。
- ソフトウェアで定義された WAN デプロイを Azure と完全に統合します。
- 1 つの Azure Virtual WAN ハブに接続されたすべての VNet で最大 2,000 の仮想マシン ワークロードをデプロイすることを予定している。

大規模な相互接続の要件を満たすために Virtual WAN を使用します。 Microsoft はこのサービスを管理しており、全体的なネットワークの複雑さを軽減し、組織のネットワークを最新化するのに役立ちます。

ハブとスポークのアーキテクチャに基づいた従来の Azure ネットワーク トポロジは、次のような組織で使用します。

- 選択した Azure リージョンでのみリソースをデプロイする計画
- グローバルに相互接続されたネットワークは不要
- リージョンごとにいくつかのリモートまたはブランチの場所があり、30 未満の IP セキュリティ (IPsec) トンネルが必要
- Azure ネットワークを手動で構成するには、完全な制御と粒度が必要です

**SAP 実装の設計に関する推奨事項:**

- Azure リージョンとオンプレミスの場所にわたってグローバルなトランジット接続を必要とする、新しく大規模な、またはグローバルなネットワークで Azure をデプロイする際に Virtual WAN を使用します。 この方法では、Azure ネットワーク用に手動で推移的なルーティングを設定する必要はなく、SAP on Azure デプロイの標準に従うことができます。

- パートナー NVA が使用されている場合にのみ、リージョン間にネットワーク仮想アプライアンス (NVA) をデプロイすることを検討してください。 ネイティブの NVA が存在する場合、リージョン間または VNet 間の NVA は必要ありません。 パートナーのネットワーク テクノロジと NVA をデプロイするときは、ベンダーのガイダンスに従って、Azure ネットワークと競合する構成がないようにします。

- Virtual WAN では、Virtual WAN ベースのトポロジ用にスポーク VNet 間の接続を管理します (ユーザー定義のルーティングを設定する必要はありません。 [UDR] または NVA として設定する必要はありません)。同じ仮想ハブ内の VNet 間トラフィックの最大ネットワーク スループットは50ギガビット/秒です。 必要に応じて、SAP ランディング ゾーンで VNet ピアリングを使用して他のランディング ゾーンに接続し、この帯域幅の制限を克服することができます。

- SAP アプリケーションのデータベース サーバーへの NVA のデプロイはサポートされていません。

- ローカル VNet とグローバル VNet のピアリングにより接続が得られ、これは、複数の Azure リージョンにまたがる SAP デプロイのためのランディング ゾーン間の接続を確保するために推奨されるアプローチです。

## <a name="plan-for-in--and-outbound-internet-connectivity"></a>受信と送信のインターネット接続計画

このセクションでは、パブリック インターネットに対する受信接続と送信接続に対する接続モデルを推奨します。 Azure Firewall、Application Gateway 上の Azure Web Application Firewall (WAF)、Azure Front Door などの Azure ネイティブのネットワーク セキュリティ サービスはフル マネージド サービスであるため、大規模な場合は複雑になりかねない、インフラストラクチャのデプロイに関連する運用コストと管理コストが発生することはありません。

**SAP 実装の設計に関する推奨事項:**

- グローバルなフットプリントを使用しているお客様の場合、[Azure Front Door](/azure/frontdoor/front-door-overview) は、Azure Web Application Firewall ポリシーを使用して Azure リージョン全体でグローバルな HTTP/S アプリケーションを提供および保護することにより、SAP のデプロイを支援します。

- このサービスと Application Gateway を使用して　HTTP/S アプリケーションを保護している場合は、Azure Front Door の Web アプリケーションファイアウォール ポリシーを活用してください。 Application Gateway をロックダウンして、Azure Front Door からのトラフィックのみを受信するようにします。

- Application Gateway、SAP Web Dispatcher、サードパーティサービス NetScaler との比較で示されるように、Application Gateway と Web アプリケーション ファイアウォールは、Application Gateway が SAP Web アプリのリバース プロキシとして機能する場合に制限があります。

SAP Web Dispatcher と NetScaler は Application Gateway よりもインテリジェントであるため、このサービスに置き換えるには広範なテストが必要です。 可能であれば、最新の状態を確認し、サポートされている (またはテストされていない) シナリオをすべて一覧表示します。

- Web アプリケーション ファイアウォールを使用して、インターネットに公開されたときのトラフィックをスキャンします。 別の方法として、ロードバランサー、または Application Gateway やサードパーティのソリューションなどの組み込みのファイアウォール機能を備えたリソースを使用することもできます。

- データ漏えいを防ぐには、Azure Private Link を使用して、Azure Blob Storage、Azure Files、Azure Data Lake Storage Gen2、Azure Data Factory などのサービス リソースとしてプラットフォームに安全にアクセスします。 Azure プライベート エンドポイントは、VNet とサービス (Azure Storage、Azure Backup など) 間のトラフィックをセキュリティで保護するのにも役立ちます。 VNet とプライベートエンドポイントに対応したサービス間のトラフィックは、Microsoft グローバル ネットワークを経由して送信されるため、パブリック インターネット上に公開されることはありません。

## <a name="define-network-encryption-requirements"></a>ネットワーク暗号化の要件を定義する

このセクションでは、オンプレミスと Azure の間、および Azure リージョン間でネットワークを暗号化するための主な推奨事項について説明します。

**SAP 実装の設計に関する考慮事項:**

- Azure ExpressRoute を使用してプライベート ピアリングを構成する際、現在、トラフィックは暗号化されていません。

- SAP デプロイで ExpressRoute 経由のトラフィックを暗号化する必要はありません。 SAP トラフィックは、通常、大量の帯域幅を消費し、パフォーマンスに影響します。 IPsec トンネルは、既定でインターネット トラフィックを暗号化します。暗号化または復号化は、トラフィックのパフォーマンスに悪影響を及ぼす可能性があります。

- SAP トラフィックを暗号化する必要があるかどうかは、お客様が決定します。 [ネットワークトポロジと接続](../../ready/enterprise-scale/network-topology-and-connectivity.md#define-network-encryption-requirements)を探索して、エンタープライズ規模のランディング ゾーンでのネットワーク暗号化オプションを理解します。

## <a name="segregate-systems"></a>システムを分離する

SAP シナリオでは、開発、テスト、品質、実稼働前、運用環境などのさまざまな環境が存在するため、これらのシステムは、セキュリティとコンプライアンスの標準に準拠するように論理/物理構造に分類される傾向があります。 1 つの共通リソース グループで同じライフサイクルを持つすべてのシステムを管理し、サブスクリプションまたはリソース グループ レベルなどのさまざまなレベルでグループ化を定義することをお勧めします。 また、SAP ランドスケープのリソースをグループ化するときに、セキュリティとポリシーの構造も考慮する必要があります。 ただし、開発、テスト、品質、運用の間で SAP トランスポートをフローさせるには、次のものが必要になることがあります。

- VNet　ピアリングと関連コスト
- VNet 間のファイアウォール ポートの開放
- UDR とネットワーク セキュリティ グループ (NSG) のルール

データベース管理システム (DBMS) と SAP システムのアプリケーション レイヤーを異なる VNet でホストし、それらを VNet ピアリングで接続することは推奨されていません。これは、レイヤー間の過剰なネットワーク トラフィックによって生まれるかなりのコストが原因です。

**SAP 実装に関する詳細な推奨事項:**

- ピアリングされていない別々の Azure VNet に SAP アプリケーション レイヤーと SAP DBMS を配置することは、サポートされていません。

- アプリケーション セキュリティ グループ (ASG) と NSG ルールを使用して、SAP アプリケーション層と DBMS 層の間にネットワーク セキュリティ アクセス制御リストを定義できます。 ASG は、セキュリティを管理するための仮想マシンをグループ化します。

- SAP アプリケーション レイヤーおよび DBMS レイヤーで使用される VM 上で、Azure 高速ネットワークが有効になっていることを確認します。 さまざまなオペレーティング システム レベルで Azure の高速ネットワークがサポートされていることを考慮してください。

  - Windows Server 2012 R2 以降
  - SUSE Linux 12 SP3 以降。
  - Red Hat Enterprise Linux 7.4 以降
  - Oracle Linux 7.5: Red Hat Enterprise Linux と互換性のあるカーネルを使用するには、Release 3.10.0-862.13.1.el7 が必要です。 リリース 5 は、Oracle の分割されていないエンタープライズ カーネルに必要です。

- Azure Load Balancer の内部デプロイが Direct Server Return (DSR) を使用するように設定されていることを確認します。 DBMS レイヤーでの高可用性構成用に内部ロード バランサー構成 が使用されている場合、この設定により待機時間が短縮されます。

- ロード バランサーを Linux ゲスト オペレーティング システムと共に使用している場合、Linux ネットワーク パラメーター `net.ipv4.tcp_timestamps` が `0` に設定されていることを確認します。

- SAP アプリケーションで最適なネットワーク大気時間を実現するには、[Azure 近接配置グループ](/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios)の使用を検討してください。

- SAP の実装の詳細については、[SAP サポートポータル](https://support.sap.com/en/index.html) と [SAP Note 2391465](https://launchpad.support.sap.com/#/notes/2931465) に関するページを参照してください。
