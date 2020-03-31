---
title: Azure イノベーション:アプリを使用した連携
description: 既存の Web アプリと API アプリを簡単に最新化し、クラウドネイティブ アプリを構築するのに役立つ Azure サービスについて説明します。
author: billyclaymyersmsft
ms.author: wimyers
ms.date: 10/17/2019
ms.topic: guide
ms.service: cloud-adoption-framework
ms.subservice: innovate
ms.custom: fasttrack-edit, AQC
ms.localizationpriority: high
ms.openlocfilehash: 79235ed1a467c9b8316f935f99894218d3b40432
ms.sourcegitcommit: ea63be7fa94a75335223bd84d065ad3ea1d54fdb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "80356680"
---
<!-- cSpell:ignore billyclaymyersmsft wimyers functionapp -->

::: zone target="docs"

# <a name="azure-innovation-guide-engage-customers-through-apps"></a>Azure イノベーション ガイド:アプリを使用したカスタマー エンゲージメントの構築

::: zone-end

::: zone target="chromeless"

# <a name="engage-customers-through-apps"></a>アプリを使用したカスタマー エンゲージメントの構築

::: zone-end

アプリによるイノベーションには、オンプレミスでホストされている既存のアプリの最新化と、コンテナーまたはサーバーレス テクノロジを使用したクラウドネイティブなアプリの構築の両方が含まれます。 Azure では、.NET、.NET Core、Java、Node.js、Ruby、Python、または PHP で記述された既存の Web アプリや API アプリを簡単に最新化して Azure にデプロイできるよう支援する PaaS サービス (Azure App Service など) が提供されます。

オープン標準のコンテナー モデルにより、Azure Kubernetes Services、Azure Container Instances、Web App for Containers などのマネージド サービスを使用したマイクロサービスの構築、既存のアプリのコンテナー化、Azure へのそれらのデプロイが簡単になります。 Azure Functions や Azure Logic Apps などのサーバーレス テクノロジでは、従量課金モデル (使用した分だけ支払う) が使用され、インフラストラクチャのデプロイと管理ではなく、アプリケーションの構築に集中できます。

<!-- markdownlint-disable MD025 -->

# <a name="deliver-value-faster"></a>[より短期間で価値を届ける](#tab/DeliverValueFaster)

クラウドベースのソリューションの利点の 1 つは、フィードバックを迅速に収集し、エンド ユーザーに価値を提供できることです。 そのユーザーが外部顧客であっても、社内のユーザーであっても、アプリケーションに関するフィードバックが迅速に得られるに越したことはありません。

## <a name="azure-app-service"></a>Azure App Service

Azure App Service からはアプリケーションのホスティング環境が提供され、それによってインフラストラクチャ管理と OS パッチ適用の負担が取り除かれます。 ユーザーの需要に合わせてスケーリングを自動化し、定義された上限で制約してコストを抑えることができます。

Azure App Service では、ASP.NET、ASP.NET Core、Java、Ruby、Node.js、PHP、Python などの言語が最高レベルでサポートされています。 別のランタイム スタックをホストする必要がある場合は、Web App for Containers を使用すると、App Service 内で Docker コンテナーを迅速かつ簡単にホストできるので、サーバーの管理が不要な環境でカスタム コード スタックをホストできます。

### <a name="action"></a>アクション

Azure App Service のデプロイを構成または監視するには:

1. **[App Service]** に移動します。
2. 新しいサービスを構成する: **[追加]** を選択し、プロンプトに従います。
3. 既存のサービスを管理する:ホストされているアプリケーションの一覧から目的のアプリを選択します。

::: zone target="chromeless"

<!-- markdownlint-disable DOCSMD001 -->

::: form action="OpenBlade[#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Web%2FSites]" submitText="Go to App Services" :::

<!-- markdownlint-enable DOCSMD001 -->

::: zone-end

## <a name="azure-cognitive-services"></a>Azure Cognitive Services

Azure Cognitive Services では、Microsoft がサポートする AI と機械学習アルゴリズムを利用できる一連の API を介して、高度なインテリジェンスをアプリに直接取り込むことができます。

<!-- markdownlint-disable MD024 -->

### <a name="action"></a>アクション

Azure Cognitive Service デプロイを構成または監視するには、次の手順に従います。

1. **[Cognitive Services]** に移動します。
2. 新しいサービスを構成する: **[追加]** を選択し、プロンプトに従います。
3. 既存のサービスを管理する:ホステッド サービスの一覧から目的のサービスを選択します。

::: zone target="chromeless"

<!-- markdownlint-disable DOCSMD001 -->

::: form action="OpenBlade[#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.CognitiveServices%2FAccounts]" submitText="Go to Cognitive Services" :::

<!-- markdownlint-enable DOCSMD001 -->

::: zone-end

## <a name="azure-bot-service"></a>Azure Bot Service

Azure Bot Service では、AI と機械学習を使用して、顧客と対話する新たな方法を生み出すナチュラル ボット インターフェイスを追加することで、標準のアプリケーションを拡張できます。

### <a name="action"></a>アクション

Azure Bot Services デプロイを構成または監視するには:

1. **[Bot Services]** に移動します。
2. 新しいサービスを構成する: **[追加]** を選択し、プロンプトに従います。
3. 既存のサービスを管理する:ホステッド サービスの一覧から目的のボットを選択します。

::: zone target="chromeless"

<!-- markdownlint-disable DOCSMD001 -->

::: form action="OpenBlade[#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.BotService%2FBotServices]" submitText="Go to Bot Services" :::

<!-- markdownlint-enable DOCSMD001 -->

::: zone-end

## <a name="azure-devops"></a>Azure DevOps

イノベーションの過程では、最終的に DevOps に向かって進むことになります。 Microsoft には長い間、Team Foundation Server (TFS) と呼ばれるオンプレミス製品がありました。 Microsoft は独自のイノベーションの過程で、リリースのさまざまな言語や目的に対応するビルド ツールとリリース ツールを提供するクラウドベースのサービスである Azure DevOps を開発しました。 詳細については、[Azure DevOps](https://docs.microsoft.com/azure/devops) のページをご覧ください。

## <a name="visual-studio-app-center"></a>Visual Studio App Center

モバイル アプリの人気が上がり続ける中、さまざまな構成を持つ実際のデバイスでテストを自動化できるプラットフォームに対するニーズが増えています。 Visual Studio App Center は、iOS、Android、Windows、macOS でアプリケーションをテストできる場所を提供するだけではありません。 Azure Application Insights を使用してテレメトリを迅速かつ簡単に分析できる監視プラットフォームも提供します。 詳細については、[Visual Studio App Center の概要](https://docs.microsoft.com/appcenter)に関するページをご覧ください。

Visual Studio App Center では、1 回の呼び出しによって複数のプラットフォームでアプリに通知を送信できる通知サービスも提供されます。各通知サービスに個別に連絡する必要はありません。 詳細については、「[Visual Studio App Center Push (ACP)](https://docs.microsoft.com/appcenter/push)」を参照してください。

### <a name="learn-more"></a>詳細情報

- [App Service の概要](https://docs.microsoft.com/azure/app-service/overview)
- [Web App for Containers: カスタム コンテナーの実行](https://docs.microsoft.com/azure/app-service/containers/quickstart-docker)
- [Azure Functions の概要](https://docs.microsoft.com/azure/azure-functions/functions-overview)
- [.NET および .NET Core 開発者向けの Azure](https://docs.microsoft.com/dotnet/azure/?view=azure-dotnet)
- [Azure SDK for Python のドキュメント](https://docs.microsoft.com/azure/python)
- [Java クラウド開発者向けの Azure](https://docs.microsoft.com/azure/java/?view=azure-java-stable)
- [Azure に PHP Web アプリを作成する](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-php)
- [Azure SDK for JavaScript のドキュメント](https://docs.microsoft.com/azure/javascript)
- [Azure SDK for Go のドキュメント](https://docs.microsoft.com/azure/go)
- [DevOps ソリューション](https://azure.microsoft.com/solutions/devops)

# <a name="create-cloud-native-apps"></a>[クラウドネイティブなアプリの作成](#tab/CloudNative)

<!-- markdownlint-disable MD026 -->

## <a name="what-are-cloud-native-applications"></a>クラウドネイティブ アプリケーションとは

クラウドネイティブ アプリケーションは一から構築され、クラウドの規模やパフォーマンスに合わせて最適化されます。 マイクロサービスのアーキテクチャに基づいて疎結合されており、マネージド サービスを使用し、観察可能です。また、継続的デリバリーを利用して高い信頼性を実現し、市場投入までの時間を短縮します。 一般に移植可能であり、パブリック クラウド、プライベート クラウド、ハイブリッド クラウドなどの動的な環境で実行できます。 通常、クラウドネイティブなアプリケーションは、次の 1 つ以上の方法で構築されます。

- マイクロサービス
- サーバーレス
- Containers

## <a name="microservices"></a>マイクロサービス

マイクロサービスは、独立した小さなモジュールで構成されるアプリが、明確に定義された API コントラクトを使用して相互にやり取りするソフトウェア アーキテクチャ スタイルです。 サービスのモジュールは単一の機能を実装した小さなブロックであり、それぞれが高度に切り離されています。 マイクロサービスは次のことを支援します。

- サービスを個別に構築する。
- サービスを個別にスケーリングする。
- デプロイとプログラミング言語に最適なアプローチを使用する。
- 障害点を分離する。
- 短期間で価値を届ける。

### <a name="azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS)

フル マネージド Kubernetes サービスを使用して、クラスター リソースのオンデマンドでのプロビジョニング、アップグレード、スケーリングを処理します。 AKS を利用すると、コンテナー化されたアプリケーションを簡単にデプロイし、管理できます。 サーバーレスの Kubernetes、統合された継続的インテグレーションと継続的デリバリー (CI/CD) エクスペリエンス、エンタープライズ レベルのセキュリティとガバナンスが提供されます。 開発チームと運用チームを単一のプラットフォーム上で統合し、迅速かつ確実にアプリケーションをビルド、デリバリー、スケーリングします。

#### <a name="action"></a>アクション

Azure Kubernetes Service を構成または監視するには、次の手順に従います。

1. **Azure Kubernetes Services** に移動します。
2. 新しいサービスを構成する: **[追加]** を選択し、プロンプトに従います。
3. 既存のサービスを管理する:一覧から目的の Kubernetes サービスを選択します。

::: zone target="chromeless"

<!-- markdownlint-disable DOCSMD001 -->

::: form action="OpenBlade[#blade/HubsExtension/BrowseResource/resourceType/Microsoft.ContainerService%2FManagedClusters]" submitText="Go to Azure Kubernetes services" :::

<!-- markdownlint-enable DOCSMD001 -->

::: zone-end

## <a name="event-based-solutions"></a>イベントベースのソリューション

### <a name="azure-functions"></a>Azure Functions

Azure Functions では、小さなコード単位 (関数) をクラウドで実行するためのプラットフォームが提供されます。 コードをマイクロサービス アーキテクチャにリファクタリングするとき、Functions から始めることができます。

Azure Functions ランタイムでは、C#、Java、JavaScript、Python など、さまざまな言語がサポートされています。 一覧については、「[Azure Functions でサポートされている言語](https://docs.microsoft.com/azure/azure-functions/supported-languages)」をご覧ください。

関数のもう 1 つの利点は、HTTPTriggers、TimerTriggers、他の Azure サービス (Blob Storage、EventGrid、ServiceBus など) からのトリガーなど、さまざまなアクションやイベントによってトリガーできることです。 トリガーとバインドの詳細については、「[Azure Functions でのトリガーとバインドの概念](https://docs.microsoft.com/azure/azure-functions/functions-triggers-bindings)」をご覧ください。

#### <a name="action"></a>アクション

Azure Functions デプロイを構成または監視するには:

1. **[Function App]** に移動します。
2. 新しいアプリを構成する: **[追加]** を選択し、プロンプトに従います。
3. 既存のアプリを管理する:関数アプリの一覧から目的のアプリを選択します。

::: zone target="chromeless"

<!-- markdownlint-disable DOCSMD001 -->

::: form action="OpenBlade[#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Web%2FSites/kind/functionapp]" submitText="Go to Azure Functions" :::

<!-- markdownlint-enable DOCSMD001 -->

::: zone-end

## <a name="serverless-solutions"></a>サーバーレス ソリューション

スケーリング、可用性、パフォーマンスが自動的に処理されるフル マネージド プラットフォームを使用して、インフラストラクチャのプロビジョニングや管理を行わずに、クラウドネイティブなアプリを構築します。 Azure サーバーレス ソリューションの利点:

- 開発者のベロシティを高める。
- チームのパフォーマンスを向上させる。
- 組織的影響を改善する。

### <a name="azure-logic-apps"></a>Azure Logic Apps

異なるシステム間で必要になる複雑な統合コードを記述せずに、データとアプリを統合します。 Azure Logic Apps によりサーバーレス ワークフローを視覚的に作成し、独自の API やサーバーレス関数のほか、Salesforce、Microsoft Office 365、Dropbox など、すぐに使えるサービスとしてのソフトウェア (SaaS) コネクタを使用できます。

#### <a name="action"></a>アクション

Azure Logic Apps を構成または監視するには、次の手順に従います。

1. **[Logic Apps]** に移動します。
2. 新しいアプリを構成する: **[追加]** を選択し、プロンプトに従います。
3. 既存のアプリを管理する:一覧で目的のロジック アプリを選択します。

::: zone target="chromeless"

<!-- markdownlint-disable DOCSMD001 -->

::: form action="OpenBlade[#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Logic%2FWorkflows]" submitText="Go to Azure Logic Apps" :::

<!-- markdownlint-enable DOCSMD001 -->

::: zone-end

### <a name="serverless-api-management"></a>サーバーレス API 管理

サーバーレス アプリケーションに自然に適合するように設計および実装された使用モデルを提供するフル マネージド サービスである Azure API Management を使用して、API の発行、セキュリティ保護、変換、メンテナンス、監視を行います。

#### <a name="action"></a>アクション

API Management サービスを構成または監視するには:

1. **[API Management サービス]** に移動します。
2. 新しいサービスを構成する: **[追加]** を選択し、プロンプトに従います。
3. 既存のサービスを管理する:一覧から目的のサービスを選択します。

::: zone target="chromeless"

<!-- markdownlint-disable DOCSMD001 -->

::: form action="OpenBlade[#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ApiManagement%2FService]" submitText="Go to API Management services" :::

<!-- markdownlint-enable DOCSMD001 -->

::: zone-end

## <a name="containers"></a>Containers

アプリケーション ポートフォリオを最新化する場合、Azure には、既存のアプリをコンテナーに移行し、短期間でユーザーに価値を提供できるように、クラウドネイティブなマイクロサービス アプリを構築するためのさまざまなコンテナー サービスが用意されています。 エンドツーエンドの開発者向けツールと CI/CD ツールを利用し、コンテナー化されたアプリケーションを開発、更新、デプロイします。 Azure Active Directory と統合されるフル マネージド Kubernetes コンテナー オーケストレーション サービスを利用し、大規模にコンテナーを管理します。 アプリを最新化する過程のどの段階にあっても、セキュリティ要件を満たしながら、コンテナー化アプリケーションの開発を加速します。

### <a name="azure-container-instances"></a>Azure Container Instances

サーバーレスなマネージド Azure 環境内で Docker コンテナーをオンデマンドで実行します。 Azure Container Instances は、オーケストレーションを必要とせずに分離コンテナー内で運用できる、あらゆるシナリオに対応するソリューションです。 Container Instances でワークロードを実行すると、アプリケーションを実行するインフラストラクチャの管理ではなく、アプリケーションの設計と構築に集中できます。

### <a name="action"></a>アクション

コンテナー インスタンスを構成または監視するには、次の手順に従います。

1. **[Container Instances]** に移動します。
2. 新しいコンテナー インスタンスを構成する: **[追加]** を選択し、プロンプトに従います。
3. 既存のコンテナー インスタンスを管理する:一覧から目的のコンテナー インスタンスを選択します。

::: zone target="chromeless"

<!-- markdownlint-disable DOCSMD001 -->

::: form action="OpenBlade[#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ContainerInstance%2FContainerGroups]" submitText="Go to Container instances" :::

<!-- markdownlint-enable DOCSMD001 -->

::: zone-end

### <a name="azure-red-hat-openshift"></a>Azure Red Hat OpenShift

Azure Red Hat OpenShift では、フル マネージドの OpenShift クラスターをセルフサービスで柔軟にデプロイすることが可能です。 マスター ノード、インフラストラクチャ ノード、アプリケーション ノードの修正プログラムの適用、更新、監視が Microsoft と Red Hat の両社によって行われるので、規制コンプライアンスを維持し、アプリケーション開発に集中できます。 独自のレジストリ、ネットワーク、ストレージ、CI/CD のソリューションを選択できます。 また、ソース コード管理、コンテナーとアプリケーションのビルド、デプロイ、スケーリング、正常性管理などが自動化された組み込みのソリューションを使用してすぐに開始することもできます。

**[[Azure Red Hat OpenShift]](https://docs.microsoft.com/azure/openshift/intro-openshift) に移動します。**

# <a name="isolate-points-of-failure"></a>[障害点を分離する](#tab/IsolatePointsOfFailure)

初期テスト フェーズからの移行を開始するときに、障害点を分離し、除去する方法を評価します。 Azure クラウド プラットフォームの分散性により、障害を最小限に抑えると同時に、パフォーマンスも改善するようにアプリケーションを設計できます。

## <a name="azure-front-door-service"></a>Azure Front Door Service

Azure Front Door Service では、世界中にアプリケーションを配信するために使用できる安全かつスケーラブルなエントリ ポイントが提供されます。 Azure Front Door Service では、最適なパフォーマンスを得るためのトラフィックの最適化と、即時グローバル フェールオーバーが組み合わされます。 トランスポート層セキュリティ (TLS) プロトコル終端 (SSL オフロード) または HTTP/HTTPS 要求ごとのアプリケーション層の処理が必要な場合は、Azure Traffic Manager ではなく、Azure Front Door Service を使用してください。

### <a name="action"></a>アクション

フロント ドアを構成または監視するには、次の手順に従います。

1. **[フロント ドア]** に移動します。
2. 新しいフロント ドアを構成する: **[追加]** を選択し、プロンプトに従います。
3. 既存のフロント ドアを管理する: 一覧から目的のフロント ドアを選択します。

::: zone target="chromeless"

<!-- markdownlint-disable DOCSMD001 -->

::: form action="OpenBlade[#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Network%2FFrontDoors]" submitText="Go to Front Doors" :::

<!-- markdownlint-enable DOCSMD001 -->

::: zone-end

## <a name="traffic-manager"></a>Traffic Manager

Traffic Manager では、さまざまなルールに基づいてルーティングできる DNS ベースの負荷分散が提供されます。 この機能により、デプロイ済みのサービスで障害が発生した場合の回復性を確保できます。 また、Traffic Manager をスタックして、障害ベースのルーティングとパフォーマンス ベースのルーティングの両方を使用することで、地理的な場所に基づいて最適なエクスペリエンスを提供することもできます。

### <a name="action"></a>アクション

Traffic Manager プロファイルを構成または監視するには:

1. **[Traffic Manager プロファイル]** に移動します。
2. 新しいプロファイルを構成する: **[追加]** を選択し、プロンプトに従います。
3. 既存のプロファイルを管理する:一覧から目的のプロファイルを選択します。

::: zone target="chromeless"

<!-- markdownlint-disable DOCSMD001 -->

::: form action="OpenBlade[#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Network%2FTrafficManagerProfiles]" submitText="Go to Traffic Manager profiles" :::

<!-- markdownlint-enable DOCSMD001 -->

::: zone-end

## <a name="azure-content-delivery-network"></a>Azure Content Delivery Network

Azure では、資産をユーザーの近くにキャッシュすることで資産のタイムリーな配信を可能にする分散型コンテンツ配信ネットワーク (CDN) が提供されます。 このキャッシュは、顧客のエクスペリエンスの向上に役立ちます。 また、コンテンツのダウンロード時に、CDN エンドポイントとアプリケーションをホストしているデータセンター間で発生するネットワークの問題に起因する問題を防ぐこともできます。 Content Delivery Network は、Azure でホストされていないアプリケーションで使用することも可能です。

### <a name="action"></a>アクション

Content Delivery Network プロファイルを構成または監視するには、次の手順に従います。

1. **[CDN プロファイル]** に移動します。
2. 新しいプロファイルを構成する: **[追加]** を選択し、プロンプトに従います。
3. 既存のプロファイルを管理する:一覧から目的のプロファイルを選択します。

::: zone target="chromeless"

<!-- markdownlint-disable DOCSMD001 -->

::: form action="OpenBlade[#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Cdn%2FProfiles]" submitText="Go to CDN profiles" :::

<!-- markdownlint-enable DOCSMD001 -->

::: zone-end

### <a name="learn-more"></a>詳細情報

- [Azure Front Door](https://docs.microsoft.com/azure/frontdoor/front-door-overview)
- [Traffic Manager](https://docs.microsoft.com/azure/traffic-manager)
- [Content Delivery Network](https://docs.microsoft.com/azure/cdn)
