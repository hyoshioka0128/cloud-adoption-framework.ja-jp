---
title: AKS のエンタープライズ規模のネットワーク トポロジと接続性
description: このエンタープライズ規模のシナリオで、Azure Kubernetes Service (AKS) のネットワークトポロジと接続性を向上させる方法について説明します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 03/01/2021
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.openlocfilehash: 695d46a7641ec300aaf7956537f2b8cd2a3363fa
ms.sourcegitcommit: b8f8b7631aabaab28e9705934bf67dad15e3a179
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101800709"
---
# <a name="network-topology-and-connectivity-for-azure-kubernetes-service-aks-enterprise-scale-scenario"></a>Azure Kubernetes Service (AKS) のエンタープライズ規模のシナリオのネットワーク トポロジと接続性

<!-- docutune:casing "Basic and Standard" -->

## <a name="design-considerations"></a>設計上の考慮事項

- AKS では、kubernet と Azure Container Networking Interface (CNI) の 2 つのネットワーク モデルがサポートされています。
  - CNI では、IP アドレスの追加の計画が必要です。
  - Windows サーバー ノードとネットワーク ポリシー プールをサポートしているのは CNI だけです。
  - kubernet では、ユーザー定義ルート (UDR) を手動で適用する必要があります。
  - 各 CNI プラグインによってサポートされている機能の[現在の一覧](/azure/aks/concepts-network#compare-network-models)を確認してください。
- クラスターのスケーリングをサポートするために、IP アドレス指定と仮想ネットワーク サブネットのサイズを慎重に計画する必要があります。 たとえば、ノードをさらに追加できます。
- 仮想ノードは、クラスターの迅速なスケーリングに使用できますが、いくつかの[既知の制限](/azure/aks/virtual-nodes-portal)があります。
- AKS クラスターでは、Basic と Standard の Azure Load Balancer SKU がサポートされています。
- AKS サービスは、パブリックまたは内部ロード バランサーを使用して公開できます。 内部ロード バランサーは、Kubernetes ノードと同じサブネットまたは専用サブネット内に構成できます。
- Azure Policy と [AKS 用 Azure Policy アドオン](/azure/governance/policy/concepts/policy-for-kubernetes "AKS の Azure Policy")により、クラスター内のパブリック IP アドレスの作成の拒否など、AKS クラスターに作成されるオブジェクトを制御および制限できます。
- AKS では CoreDNS を使用して、クラスターで実行されているポッドに名前解決が提供されます。
  - CoreDNS により、クラスター内部ドメインが直接解決されます。
  - その他のドメインは、Azure Virtual Network に構成された DNS サーバーに転送されます。これは、既定の Azure DNS リゾルバー、または仮想ネットワーク レベルで構成された任意のカスタム DNS サーバーのいずれかになります。
- 送信 (エグレス) ネットワーク トラフィックは、Azure Firewall またはネットワーク仮想アプライアンス クラスター経由で送信できます。
  - 既定で、AKS クラスターのエグレス インターネット アクセスは無制限です。
  - AKS クラスターからのエグレス トラフィックは、AKS サブネットに UDR を構成することによって、Azure Firewall またはネットワーク仮想アプライアンス クラスター経由で送信できます。
- 既定では、AKS クラスター内のすべてのポッドは制限なしにトラフィックを送受信できます。 Kubernetes ネットワーク ポリシーを使用して、AKS クラスター内のポッド間のセキュリティを向上させ、ネットワーク トラフィックをフィルター処理できます。 AKS では、2 つの[ネットワーク ポリシー モデル](/azure/aks/use-network-policies#network-policy-options-in-aks)を使用できます。
- サービス メッシュには、トラフィック管理、回復性、ポリシー、セキュリティ、強力な ID、可観測性などの機能が備えられています。 詳しくは、「[選択条件](/azure/aks/servicemesh-about#selection-criteria)」をご覧ください。
- [Azure Traffic Manager](/azure/traffic-manager/traffic-manager-overview) や [Azure Front Door](/azure/frontdoor/front-door-overview) などのグローバル負荷分散メカニズムでは、異なる Azure リージョンにある可能性がある複数のクラスター間でトラフィックをルーティングすることによって、回復性を高めています。

### <a name="private-clusters"></a>プライベート クラスター

AKS クラスター IP の可視性は、パブリックまたはプライベートにすることができます。 [プライベート クラスター](/azure/aks/private-clusters)では、プライベート IP アドレス経由で Kubernetes API が公開されますが、パブリック IP アドレス経由では公開されません。 このプライベート IP アドレスは実際には、AKS 仮想ネットワークで[プライベート エンドポイント](/azure/private-link/private-endpoint-overview)を介して表されます。 Kubernetes API には、その IP アドレスを使用するのではなく、その完全修飾ドメイン名 (FQDN) によってアクセスする必要があります。 Kubernetes API FQDN からその IP アドレスへの解決は、通常、[Azure プライベート DNS ゾーン](/azure/dns/private-dns-overview)によって実行されます。 この DNS ゾーンは、Azure によって [AKS ノード リソース グループ](/azure/aks/faq#why-are-two-resource-groups-created-with-aks)に作成することも、[既存の DNS ゾーン](/azure/aks/private-clusters#no-private-dns-zone-prerequisites)を指定することもできます。

エンタープライズ規模の実証済みプラクティスに従って、Azure ワークロードの DNS 解決は、ハブ仮想ネットワークまたは Azure Virtual WAN に接続された共有サービス仮想ネットワーク内の、接続サブスクリプションにデプロイされた中央管理 DNS サーバーによって提供されます。 これらのサーバーにより、Azure DNS を使用して Azure 固有の名前とパブリック名 (IP アドレス `168.63.129.16`) のほか、企業の DNS サーバーを使用してプライベート名が条件付きで解決されます。 ただし、これらの中央管理 DNS サーバーでは、AKS クラスター用に作成された DNS プライベート ゾーンに接続されるまで、AKS API FQDN を解決できません。 ゾーン名の前にランダムな GUID が付加されるため、各 AKS で、一意の DNS プライベート ゾーンが使用されることに注意してください。 そのため、新しい AKS クラスターごとに、その対応するプライベート DNS ゾーンを、中央 DNS サーバーが配置されている仮想ネットワークに接続する必要があります。

すべての仮想ネットワークで、名前解決にこれらの中央 DNS サーバーを使用するように構成する必要があります。 ただし、中央 DNS サーバーを使用するように AKS 仮想ネットワークが構成されていて、これらがプライベート DNS ゾーンにまだ接続されていない場合、AKS ノードで Kubernetes API の FQDN を解決できず、AKS クラスターの作成が失敗します。 クラスターの作成後にのみ、中央の DNS サーバーを使用するように、AKS 仮想ネットワークを構成する必要があります。

クラスターが作成されると、DNS プライベート ゾーンと、中央 DNS サーバーがデプロイされている仮想ネットワークの間に接続が作成されます。 AKS 仮想ネットワークも接続サブスクリプションの中央 DNS サーバーを使用するように構成され、AKS Kubernetes API への管理者アクセスは次のフローに従います。

![プライベート クラスター](./media/network-private-cluster.png)

> [!NOTE]
> この記事の画像は、従来のハブ アンド スポーク接続モデルを使用した設計を反映しています。 エンタープライズ規模のランディング ゾーンでは、Virtual WAN 接続モデルを選択できます。このモデルでは、中央 DNS サーバーは、Virtual WAN ハブに接続されている共有サービス仮想ネットワークに配置されます。

1. 管理者は、Kubernetes API の FQDN を解決します。 オンプレミスの DNS サーバーでは、権限のあるサーバー (Azure の DNS リゾルバー) に要求が転送されます。 これらのサーバーでは、Azure DNS サーバー (`168.63.129.16`) に要求が転送されます。これにより、Azure プライベート DNS ゾーンから IP アドレスが検出されます。
2. IP アドレスの解決後、接続モデルに応じて、Kubernetes API へのトラフィックがオンプレミスから Azure の VPN または ExpressRoute ゲートウェイにルーティングされます。
3. プライベート エンドポイントには、ハブ仮想ネットワーク内の `/32` ルートが案内されるため、VPN および ExpressRoute ゲートウェイでは、AKS 仮想ネットワークにデプロイされた Kubernetes API プライベート エンドポイントにトラフィックが直接送信されます。

### <a name="traffic-from-application-users-to-the-cluster"></a>アプリケーション ユーザーからクラスターへのトラフィック

受信 (イングレス) コントローラーを使用して、AKS クラスターで実行されているアプリケーションを公開できます。

- イングレス コントローラーでは、わずかな複雑さの増加を犠牲にして、アプリケーションレベルのルーティングが提供されます。
- イングレス コントローラーには、Web アプリケーション ファイアウォール (WAF) 機能を組み込むことができます。
- イングレス コントローラーは、クラスター外およびクラスター内で実行できます。
  - クラスター外のイングレス コントローラーによって、コンピューティング (HTTP トラフィックのルーティングや TLS 終端など) を、[Azure Application Gateway イングレス コントローラー (AGIC) アドオン](/azure/application-gateway/ingress-controller-overview)のような AKS の外部の別のサービスにオフロードします。
  - クラスター内ソリューションでは、コンピューティング (HTTP トラフィックのルーティングや TLS 終端など) に AKS クラスター リソースが使用されます。 クラスター内イングレス コントローラーでは、コストを低く抑えることができますが、慎重なリソース計画とメンテナンスが必要になります。
- 基本の HTTP アプリケーション ルーティング アドオンは非常に簡単に使えますが、「[HTTP アプリケーション ルーティング](/azure/aks/http-application-routing)」に記載されているように、いくつかの制限があります。

イングレス コントローラーでは、パブリックまたはプライベート IP アドレスでアプリケーションと API を公開できます。

- 構成をエグレス フィルター処理設計に合わせて、非対称ルーティングを回避する必要があります。
- TLS 終端が必要な場合は、TLS 証明書の管理を考慮する必要があります。

アプリケーション トラフィックは、オンプレミスまたはパブリック インターネットから送信されてくる可能性があります。 次の図は、オンプレミスとパブリック インターネットの両方からクラスターへのリバースプロキシ接続を行うように [Azure Application Gateway](/azure/application-gateway/overview) が構成されている例を示しています。

![アプリケーション トラフィック](./media/network-app-access.png)

オンプレミスからのトラフィックは、前の図の番号付きの青い吹き出しのフローに従います。

1. クライアントでは、接続サブスクリプションにデプロイされた DNS サーバーまたはオンプレミス DNS サーバーを使用して、アプリケーションに割り当てられている FQDN が解決されます。
2. アプリケーションの FQDN が IP アドレス (アプリケーション ゲートウェイのプライベート IP アドレス) に解決されると、トラフィックは VPN または ExpressRoute ゲートウェイ経由でルーティングされます。
3. ゲートウェイ サブネットのルーティングは、要求を Web アプリケーション ファイアウォールに送信するように構成されます。
4. Web アプリケーション ファイアウォールでは、有効な要求が AKS クラスターで実行されているワークロードに送信されます。

この例の Azure Application Gateway は、AKS クラスターと同じサブスクリプションにデプロイできます。これは、その構成が AKS にデプロイされたワークロードときわめて密接に関連しているため、同じアプリケーション チームによって管理されるからです。 インターネットからのアクセスは、前の図の番号付きの緑色の吹き出しのフローに従います。

1. パブリック インターネットからのクライアントでは、[Azure Traffic Manager](/azure/traffic-manager/traffic-manager-overview) を使用してアプリケーションの DNS 名が解決されます。 または、[Azure Front Door](/azure/frontdoor/front-door-overview) のような他のグローバル負荷分散テクノロジを使用することもできます。
2. アプリケーションのパブリック FQDN は、Traffic Manager によって、クライアントがパブリック インターネット経由でアクセスするアプリケーション ゲートウェイのパブリック IP アドレスに解決されます。
3. アプリケーション ゲートウェイによって、AKS にデプロイされたワークロードにアクセスされます。

> [!NOTE]
> これらのフローは、Web アプリケーションにのみ有効です。 非 Web アプリケーションはこの記事の範囲外であり、ハブ仮想ネットワーク内の Azure Firewall、または仮想 WAN 接続モデルを使用している場合はセキュリティで保護された仮想ハブ経由で公開できます。

または、接続サブスクリプション内の Azure Firewall と AKS 仮想ネットワーク内の WAF の両方を走査する Web ベースのアプリケーションのトラフィック フローを作成することもできます。 このアプローチには、[Azure Firewall インテリジェンスベースのフィルター処理](/azure/firewall/threat-intel)を使用して、インターネットの既知の悪意のある IP アドレスからのトラフィックを削除するなど、追加の保護を提供する利点があります。 ただし、いくつの欠点もあります。 たとえば、元のクライアント IP アドレスの損失や、アプリケーションを公開するときにファイアウォールとアプリケーション チームの間で追加の調整が必要になります。 これは、Azure Firewall で、宛先ネットワーク アドレス変換 (DNAT) 規則が必要になるためです。

### <a name="traffic-from-the-aks-pods-to-backend-services"></a>AKS ポッドからバックエンド サービスへのトラフィック

AKS クラスターの内部で実行されているポッドで、Azure Storage、Azure SQL データベース、Azure Cosmos DB NoSQL データベースなどのバックエンド サービスにアクセスする必要がある場合があります。 [仮想ネットワーク サービス エンドポイント](/azure/virtual-network/virtual-network-service-endpoints-overview)と [Private Link](/azure/private-link/private-link-overview) を使用して、これらの Azure マネージド サービスへの接続をセキュリティで保護することができます。

バックエンド トラフィックに Azure プライベート エンドポイントを使用している場合は、Azure プライベート DNS ゾーンを使用して Azure サービスの DNS 解決を実行できます。 環境全体の DNS リゾルバーはハブ仮想ネットワーク (または、Virtual WAN 接続モデルを使用している場合は共有サービス仮想ネットワーク) 内にあるため、これらのプライベート ゾーンは接続サブスクリプションに作成する必要があります。 プライベート サービスの FQDN を解決するために必要な A レコードを作成するには、プライベート DNS ゾーン (接続サブスクリプション内) をプライベート エンドポイント (アプリケーション サブスクリプション内) に関連付けることができます。 この操作では、それらの各サブスクリプションで特定の権限が必要です。

A レコードを手動で作成することはできますが、プライベート DNS ゾーンをプライベート エンドポイントに関連付けると、セットアップで誤った構成になる可能性が低くなります。

プライベート エンドポイント経由で公開される AKS ポッドから Azure PaaS サービスへのバックエンド接続は、次のシーケンスに従います。

![バックエンド トラフィック](./media/network-backend-access.png)

1. AKS ポッドでは、AKS 仮想ネットワークでカスタム DNS サーバーとして定義されている、接続サブスクリプション内の中央 DNS サーバーを使用して、Azure サービスとしてのプラットフォーム (PaaS) の FQDN が解決されます。
2. 解決された IP は、プライベート エンドポイントのプライベート IP アドレスになり、AKS ポッドから直接アクセスされます。

AKS クラスターが [Azure Firewall によるエグレス フィルター処理](/azure/aks/limit-egress-traffic)用に構成されている場合でも、AKS ポッドとプライベート エンドポイント間のトラフィックは、既定で、ハブ仮想ネットワーク (または仮想 WAN を使用する場合はセキュリティで保護された仮想ハブ) 内の Azure Firewall を通過しません。 その理由は、プライベート エンドポイントによって、AKS がデプロイされているアプリケーション仮想ネットワークのサブネットに、`/32` ルートが作成されるためです。

## <a name="design-recommendations"></a>設計の推奨事項

- セキュリティ ポリシーで、プライベート IP アドレス (パブリック IP アドレスではなく) で Kubernetes API を使用することが必須とされている場合は、[プライベート AKS クラスターをデプロイ](/azure/aks/private-clusters)します。
  - プライベート クラスターの作成時に、作成プロセスで[システム プライベート DNS ゾーン](/azure/aks/private-clusters#configure-private-dns-zone)を使用させるのではなく、カスタム プライベート DNS ゾーンを使用します。
- AKS クラスターに割り当てることができる IP アドレスの範囲が限られている場合を除き、ネットワーク モデルとして Azure Container Networking Interface (CNI) を使用します。
  - CNI による [IP アドレス計画](/azure/aks/configure-azure-cni#plan-ip-addressing-for-your-cluster)に関するドキュメントに従ってください。
  - Windows Server ノードプールと仮想ノードを使用して最終的な制限を確認するには、[Windows AKS のサポート FAQ](/azure/aks/windows-faq)を参照してください。
- Azure DDoS Protection 標準を使用して、AKS クラスターに使用する仮想ネットワークを保護します。
- Azure Virtual WAN またはハブ アンド スポーク アーキテクチャ、Azure DNS ゾーン、独自の DNS インフラストラクチャによる全体的なネットワーク設定にリンクされた DNS 構成を使用します。
- Private Link を使用してネットワーク接続をセキュリティで保護し、Private Link をサポートする他のマネージド Azure サービス (Azure Storage、Azure Container Registry、Azure SQL Database、Azure Key Vault など) にプライベート IP ベースの接続を使用します。
- イングレス コントローラーを使用して、高度な HTTP ルーティングとセキュリティを提供し、アプリケーションに単一のエンドポイントを提供します。
- AKS クラスターのコンピューティング リソースとストレージ リソースを節約するには、クラスター外のイングレス コントローラーを使用します。
  - [Azure Application Gateway イングレス コントローラー (AGIC)](/azure/application-gateway/ingress-controller-overview) アドオンを使用します。これは、ファーストパーティ マネージド Azure サービスです。
  - AGIC では、AKS クラスターごとに専用の Azure Application Gateway をデプロイし、複数の AKS クラスターで同じ Application Gateway を共有しないでください。
  - リソースまたは操作上の制約がない場合、または AGIC で必要な機能を提供しない場合は、NGINX、Traefik、またはその他の Kubernetes でサポートされている任意のソリューションなどのクラスター内イングレス コントローラー ソリューションを使用します。
- インターネットに接続され、セキュリティが重要な内部 Web アプリケーションの場合は、イングレス コントローラーを備えた Web アプリケーション ファイアウォールを使用します。
  - Azure Application Gateway と Azure Front Door にはどちらも、[Azure WAF](/azure/web-application-firewall/ag/ag-overview) が統合され、Web ベースのアプリケーションが保護されます。
- セキュリティ ポリシーで AKS クラスターで生成されるすべてのインターネット送信トラフィックの検査が必須とされている場合は、Azure Firewall または (マネージド) ハブ仮想ネットワークにデプロイされたサードパーティ ネットワーク仮想アプライアンス (NVA) を使用して、エグレス ネットワーク トラフィックをセキュリティ保護します。 詳細については、[エグレス トラフィックの制限](/azure/aks/limit-egress-traffic)に関するページを参照してください。
