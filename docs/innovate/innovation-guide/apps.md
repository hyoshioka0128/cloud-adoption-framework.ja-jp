---
title: Azure イノベーション:アプリケーションを介したエンゲージメント
description: 既存の Web アプリと API アプリを簡単に最新化し、クラウドネイティブ アプリを構築するのに役立つ Azure サービスについて説明します。
author: billyclaymyersmsft
ms.author: wimyers
ms.date: 10/17/2019
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: innovate
ms.custom: fasttrack-edit, AQC
ms.localizationpriority: high
ms.openlocfilehash: 5a874c26835b17e2aa30a2230af3afe79dfd6628
ms.sourcegitcommit: 8b5fdb68127c24133429b4288f6bf9004a1d1253
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2020
ms.locfileid: "88848144"
---
<!-- cSpell:ignore billyclaymyersmsft wimyers functionapp -->

# <a name="engage-customers-through-applications"></a>アプリケーションを介したカスタマー エンゲージメント

アプリケーションによるイノベーションには、オンプレミスでホストされている既存のアプリケーションの最新化と、コンテナーまたはサーバーレス テクノロジを使用したクラウドネイティブなアプリケーションの構築の両方が含まれます。 Azure では、.NET、.NET Core、Java、Node.js、Ruby、Python、または PHP で記述された既存の Web アプリや API アプリを簡単に最新化して Azure にデプロイできるよう支援する PaaS サービス (Azure App Service など) が提供されます。

オープン標準のコンテナー モデルにより、Azure Kubernetes Services、Azure Container Instances、Web App for Containers などのマネージド サービスを使用したマイクロサービスの構築、既存のアプリケーションのコンテナー化、Azure へのそれらのデプロイが簡単になります。 Azure Functions や Azure Logic Apps などのサーバーレス テクノロジでは、従量課金モデル (使用した分だけ支払う) が使用され、インフラストラクチャのデプロイと管理ではなく、アプリケーションの構築に集中できます。

## <a name="deliver-value-faster"></a>[より短期間で価値を届ける](#tab/DeliverValueFaster)

クラウドベースのソリューションの利点の 1 つは、フィードバックを迅速に収集し、エンド ユーザーに価値を提供できることです。 そのユーザーが外部顧客であっても、社内のユーザーであっても、アプリケーションに関するフィードバックが迅速に得られるに越したことはありません。

### <a name="azure-app-service"></a>Azure App Service

Azure App Service からはアプリケーションのホスティング環境が提供され、それによってインフラストラクチャ管理と OS パッチ適用の負担が取り除かれます。 ユーザーの需要に合わせてスケーリングを自動化し、定義された上限で制約してコストを抑えることができます。

Azure App Service では、ASP.NET、ASP.NET Core、Java、Ruby、Node.js、PHP、Python などの言語が最高レベルでサポートされています。 別のランタイム スタックをホストする必要がある場合は、Web App for Containers を使用すると、App Service 内で Docker コンテナーを迅速かつ簡単にホストできるので、サーバーの管理が不要な環境でカスタム コード スタックをホストできます。

#### <a name="action"></a>アクション

Azure App Service のデプロイを構成または監視するには:

1. **[App Service]** に移動します。
2. 新しいサービスを構成する: **[追加]** を選択し、画面の指示に従います。
3. 既存のサービスを管理する: ホストされているアプリケーションの一覧から目的のアプリケーションを選択します。

::: zone target="chromeless"

<!-- markdownlint-disable DOCSMD001 -->

::: form action="OpenBlade[#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Web%2FSites]" submitText="Go to App Services" :::

<!-- markdownlint-enable DOCSMD001 -->

::: zone-end

### <a name="azure-cognitive-services"></a>Azure Cognitive Services

Azure Cognitive Services では、Microsoft がサポートする AI と機械学習アルゴリズムを利用できる一連の API を介して、高度なインテリジェンスをアプリケーションに直接取り込むことができます。

<!-- markdownlint-disable MD024 -->

#### <a name="action"></a>アクション

Azure Cognitive Service デプロイを構成または監視するには、次の手順に従います。

1. **[Cognitive Services]** に移動します。
2. 新しいサービスを構成する: **[追加]** を選択し、画面の指示に従います。
3. 既存のサービスを管理する: ホストされているサービスの一覧から目的のサービスを選択します。

::: zone target="chromeless"

<!-- markdownlint-disable DOCSMD001 -->

::: form action="OpenBlade[#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.CognitiveServices%2FAccounts]" submitText="Go to Cognitive Services" :::

<!-- markdownlint-enable DOCSMD001 -->

::: zone-end

### <a name="azure-bot-service"></a>Azure Bot Service

Azure Bot Service では、AI と機械学習を使用して、顧客と対話する新たな方法を生み出すナチュラル ボット インターフェイスを追加することで、標準のアプリケーションを拡張できます。

#### <a name="action"></a>アクション

Azure Bot Service デプロイを構成または監視するには、次の手順に従います。

1. **[Bot Services]** に移動します。
2. 新しいサービスを構成する: **[追加]** を選択し、画面の指示に従います。
3. 既存のサービスを管理する: ホストされているサービスの一覧から目的のボットを選択します。

::: zone target="chromeless"

<!-- markdownlint-disable DOCSMD001 -->

::: form action="OpenBlade[#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.BotService%2FBotServices]" submitText="Go to Bot Services" :::

<!-- markdownlint-enable DOCSMD001 -->

::: zone-end

### <a name="azure-devops"></a>Azure DevOps

イノベーションの過程では、最終的に DevOps に向かって進むことになります。 Microsoft には長い間、Team Foundation Server (TFS) と呼ばれるオンプレミス製品がありました。 Microsoft は独自のイノベーションの過程で、リリースのさまざまな言語や目的に対応するビルド ツールとリリース ツールを提供するクラウドベースのサービスである Azure DevOps を開発しました。 詳細については、[Azure DevOps](/azure/devops) のページをご覧ください。

### <a name="visual-studio-app-center"></a>Visual Studio App Center

モバイル アプリの人気が上がり続ける中、さまざまな構成を持つ実際のデバイスでテストを自動化できるプラットフォームに対するニーズが増えています。 Visual Studio App Center は、iOS、Android、Windows、macOS をまたいでアプリケーションをテストできる場所だけでなく、Azure Application Insights を使用し、テレメトリをすばやく簡単に分析できる監視プラットフォームも提供します。 詳細については、[Visual Studio App Center](/appcenter) に関するページを参照してください。

Visual Studio App Center からは通知サービスも提供されます。1 回の呼び出しでプラットフォームに関係なく通知をアプリケーションに送信できます。通知サービス別に連絡する必要がありません。 詳細については、「[Visual Studio App Center Push (ACP)](/appcenter/push)」を参照してください。

### <a name="learn-more"></a>詳細情報

- [App Service の概要](/azure/app-service/overview)
- [Web App for Containers: カスタム コンテナーの実行](/azure/app-service/containers/quickstart-docker)
- [Azure Functions の概要](/azure/azure-functions/functions-overview)
- [.NET および .NET Core 開発者向けの Azure](/dotnet/azure/?view=azure-dotnet)
- [Azure SDK for Python のドキュメント](/azure/python)
- [Java クラウド開発者向けの Azure](/azure/java/?view=azure-java-stable)
- [Azure に PHP Web アプリを作成する](/azure/app-service/app-service-web-get-started-php)
- [Azure SDK for JavaScript のドキュメント](/azure/javascript)
- [Azure SDK for Go のドキュメント](/azure/go)
- [DevOps ソリューション](https://azure.microsoft.com/solutions/devops)

## <a name="create-cloud-native-applications"></a>[クラウドネイティブ アプリケーションの作成](#tab/CloudNative)

### <a name="what-are-cloud-native-applications"></a>クラウドネイティブ アプリケーションとは

クラウドネイティブ アプリケーションは一から構築され、クラウドの規模やパフォーマンスに合わせて最適化されます。 マイクロサービスのアーキテクチャに基づいて疎結合されており、マネージド サービスを使用し、観察可能です。また、継続的デリバリーを利用して高い信頼性を実現し、市場投入までの時間を短縮します。 一般に移植可能であり、パブリック クラウド、プライベート クラウド、ハイブリッド クラウドなどの動的な環境で実行できます。 通常、クラウドネイティブなアプリケーションは、次の 1 つ以上の方法で構築されます。

- マイクロサービス
- サーバーレス
- Containers

### <a name="microservices"></a>マイクロサービス

マイクロサービスは、独立した小さなモジュールで構成されるアプリケーションが、明確に定義された API コントラクトを使用して相互にやり取りするソフトウェア アーキテクチャ スタイルです。 サービスのモジュールは単一の機能を実装した小さなブロックであり、それぞれが高度に切り離されています。 マイクロサービスは次のことを支援します。

- サービスを個別に構築する。
- サービスを個別にスケーリングする。
- デプロイとプログラミング言語に最適なアプローチを使用する。
- 障害点を分離する。
- 短期間で価値を届ける。

### <a name="microservices-azure-kubernetes-service-aks"></a>マイクロサービス:Azure Kubernetes Service (AKS)

フル マネージド Kubernetes サービスを使用して、クラスター リソースのオンデマンドでのプロビジョニング、アップグレード、スケーリングを処理します。 AKS を利用すると、コンテナー化されたアプリケーションを簡単にデプロイし、管理できます。 サーバーレスの Kubernetes、統合された継続的インテグレーションと継続的デリバリー (CI/CD) エクスペリエンス、エンタープライズ レベルのセキュリティとガバナンスが提供されます。 開発チームと運用チームを単一のプラットフォーム上で統合し、迅速かつ確実にアプリケーションをビルド、デリバリー、スケーリングします。

#### <a name="action"></a>アクション

AKS サービスを構成または監視するには:

1. **Azure Kubernetes Services** に移動します。
2. 新しいサービスを構成する: **[追加]** を選択し、画面の指示に従います。
3. 既存のサービスを管理する: 一覧から目的の Kubernetes サービスを選択します。

::: zone target="chromeless"

<!-- markdownlint-disable DOCSMD001 -->

::: form action="OpenBlade[#blade/HubsExtension/BrowseResource/resourceType/Microsoft.ContainerService%2FManagedClusters]" submitText="Go to Azure Kubernetes Services" :::

<!-- markdownlint-enable DOCSMD001 -->

::: zone-end

### <a name="serverless-solutions"></a>サーバーレス ソリューション

スケーリング、可用性、パフォーマンスが自動的に処理されるフル マネージド プラットフォームを使用して、インフラストラクチャのプロビジョニングや管理を行わずに、クラウドネイティブなアプリを構築します。 Azure サーバーレス ソリューションの利点:

- 開発者のベロシティを高める。
- チームのパフォーマンスを向上させる。
- 組織的影響を改善する。

### <a name="serverless-solutions-azure-functions"></a>サーバーレス ソリューション:Azure Functions

Azure Functions では、小さなコード単位 (関数) をクラウドで実行するためのプラットフォームが提供されます。 コードをマイクロサービス アーキテクチャにリファクタリングするとき、Functions から始めることができます。

Azure Functions ランタイムでは、C#、Java、JavaScript、Python など、さまざまな言語がサポートされています。 一覧については、「[Azure Functions でサポートされている言語](/azure/azure-functions/supported-languages)」をご覧ください。

関数のもう 1 つの利点は、HTTP トリガー、タイマー トリガー、他の Azure サービス (Blob Storage、Event Grid、Service Bus など) からのトリガーなど、さまざまなアクションやイベントによってトリガーできることです。 トリガーとバインドの詳細については、「[Azure Functions でのトリガーとバインドの概念](/azure/azure-functions/functions-triggers-bindings)」を参照してください。

#### <a name="action"></a>アクション

Azure Functions デプロイを構成または監視するには:

1. **[Function App]** に移動します。
2. 新しい関数アプリを構成します。 **[追加]** を選択し、プロンプトに従います。
3. 既存の関数アプリを管理します。一覧から目的の関数アプリを選択します。

::: zone target="chromeless"

<!-- markdownlint-disable DOCSMD001 -->

::: form action="OpenBlade[#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Web%2FSites/kind/functionapp]" submitText="Go to Azure Functions" :::

<!-- markdownlint-enable DOCSMD001 -->

::: zone-end

### <a name="serverless-solutions-azure-logic-apps"></a>サーバーレス ソリューション:Azure Logic Apps

異なるシステム間で必要になる複雑な統合コードを記述せずに、データとアプリケーションを統合します。 Azure Logic Apps を使用してサーバーレス ワークフローを視覚的に作成し、独自の API やサーバーレス関数のほか、Salesforce、Microsoft 365、Dropbox など、すぐに使えるサービスとしてのソフトウェア (SaaS) コネクタを使用できます。

#### <a name="action"></a>アクション

Azure Logic Apps を構成または監視するには:

1. **[Logic Apps]** に移動します。
2. 新しいロジック アプリを構成します。 **[追加]** を選択し、プロンプトに従います。
3. 既存のロジック アプリを管理します。一覧で目的のロジック アプリを選択します。

::: zone target="chromeless"

<!-- markdownlint-disable DOCSMD001 -->

::: form action="OpenBlade[#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Logic%2FWorkflows]" submitText="Go to Azure Logic Apps" :::

<!-- markdownlint-enable DOCSMD001 -->

::: zone-end

### <a name="serverless-solutions-api-management"></a>サーバーレス ソリューション:API 管理

サーバーレス アプリケーションに自然に適合するように設計および実装された使用モデルを提供するフル マネージド サービスである Azure API Management を使用して、API の発行、セキュリティ保護、変換、メンテナンス、監視を行います。

#### <a name="action"></a>アクション

API Management サービスを構成または監視するには:

1. **[API Management サービス]** に移動します。
2. 新しいサービスを構成する: **[追加]** を選択し、画面の指示に従います。
3. 既存のサービスを管理する: 一覧から目的のサービスを選択します。

::: zone target="chromeless"

<!-- markdownlint-disable DOCSMD001 -->

::: form action="OpenBlade[#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ApiManagement%2FService]" submitText="Go to API Management services" :::

<!-- markdownlint-enable DOCSMD001 -->

::: zone-end

### <a name="containers"></a>Containers

アプリケーション ポートフォリオを最新化する場合、Azure には、既存のアプリケーションをコンテナーに移行し、短期間でユーザーに価値を提供できるように、クラウドネイティブなマイクロサービス アプリケーションを構築するためのさまざまなコンテナー サービスが用意されています。 エンドツーエンドの開発者向けツールと CI/CD ツールを利用し、コンテナー化されたアプリケーションを開発、更新、デプロイします。 Azure Active Directory と統合されるフル マネージド Kubernetes コンテナー オーケストレーション サービスを利用し、大規模にコンテナーを管理します。 アプリケーションを最新化する過程のどの段階にあっても、セキュリティ要件を満たしながら、コンテナー化アプリケーションの開発を加速します。

### <a name="containers-azure-container-instances"></a>コンテナー: Azure Container Instances

サーバーレスなマネージド Azure 環境内で Docker コンテナーをオンデマンドで実行します。 Azure Container Instances は、オーケストレーションを必要とせずに分離コンテナー内で運用できる、あらゆるシナリオに対応するソリューションです。 コンテナー インスタンスでワークロードを実行すると、アプリケーションを実行するインフラストラクチャの管理ではなく、アプリケーションの設計と構築に集中できます。

#### <a name="action"></a>アクション

コンテナー インスタンスを構成または監視するには、次の手順に従います。

1. **[Container Instances]** に移動します。
2. 新しいコンテナー インスタンスを構成する: **[追加]** を選択し、画面の指示に従います。
3. 既存のコンテナー インスタンスを管理する: 一覧から目的のコンテナー インスタンスを選択します。

::: zone target="chromeless"

<!-- markdownlint-disable DOCSMD001 -->

::: form action="OpenBlade[#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ContainerInstance%2FContainerGroups]" submitText="Go to Container instances" :::

<!-- markdownlint-enable DOCSMD001 -->

::: zone-end

### <a name="containers-azure-red-hat-openshift"></a>コンテナー: Azure Red Hat OpenShift

Azure Red Hat OpenShift では、フル マネージドの OpenShift クラスターをセルフサービスで柔軟にデプロイすることが可能です。 マスター ノード、インフラストラクチャ ノード、アプリケーション ノードの修正プログラムの適用、更新、監視が Microsoft と Red Hat の両社によって行われるので、規制コンプライアンスを維持し、アプリケーション開発に集中できます。 独自のレジストリ、ネットワーク、ストレージ、CI/CD のソリューションを選択できます。 また、ソース コード管理、コンテナーとアプリケーションのビルド、デプロイ、スケーリング、正常性管理などが自動化された組み込みのソリューションを使用してすぐに開始することもできます。

#### <a name="learn-more"></a>詳細情報

- [Azure Red Hat OpenShift](/azure/openshift/intro-openshift)

## <a name="isolate-points-of-failure"></a>[障害点を分離する](#tab/IsolatePointsOfFailure)

初期テスト フェーズからの移行を開始するときに、障害点を分離し、除去する方法を評価します。 Azure クラウド プラットフォームの分散性により、障害を最小限に抑えると同時に、パフォーマンスも改善するようにアプリケーションを設計できます。

### <a name="azure-front-door"></a>Azure Front Door

Azure Front Door により、世界中にアプリケーションを配信するために使用できる安全かつスケーラブルなエントリ ポイントが提供されます。 Azure Front Door では、トラフィックの最適化を組み合わせることで最良のパフォーマンスと瞬間的なグローバル フェールオーバーが得られます。 トランスポート層セキュリティ (TLS) プロトコルの終端 (SSL オフロード) または HTTP/HTTPS 要求ごとのアプリケーション層の処理が必要な場合は、Azure Traffic Manager ではなく、Azure Front Door を使用する必要があります。

#### <a name="action"></a>アクション

Front Door インスタンスを構成または監視するには、次の手順に従います。

1. **[フロント ドア]** に移動します。
2. 新しい Front Door インスタンスを構成する: **[追加]** を選択し、画面の指示に従います。
3. 既存の Front Door を管理する: 一覧から目的の Front Door インスタンスを選択します。

::: zone target="chromeless"

<!-- markdownlint-disable DOCSMD001 -->

::: form action="OpenBlade[#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Network%2FFrontDoors]" submitText="Go to Front Doors" :::

<!-- markdownlint-enable DOCSMD001 -->

::: zone-end

### <a name="traffic-manager"></a>Traffic Manager

Traffic Manager では、さまざまなルールに基づいてルーティングできる DNS ベースの負荷分散が提供されます。 この機能により、デプロイ済みのサービスで障害が発生した場合の回復性を確保できます。 また、Traffic Manager をスタックして、障害ベースのルーティングとパフォーマンス ベースのルーティングの両方を使用することで、地理的な場所に基づいて最適なエクスペリエンスを提供することもできます。

#### <a name="action"></a>アクション

Traffic Manager プロファイルを構成または監視するには:

1. **[Traffic Manager プロファイル]** に移動します。
2. 新しいプロファイルを構成する: **[追加]** を選択し、画面の指示に従います。
3. 既存のプロファイルを管理する: 一覧から目的のプロファイルを選択します。

::: zone target="chromeless"

<!-- markdownlint-disable DOCSMD001 -->

::: form action="OpenBlade[#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Network%2FTrafficManagerProfiles]" submitText="Go to Traffic Manager profiles" :::

<!-- markdownlint-enable DOCSMD001 -->

::: zone-end

### <a name="azure-content-delivery-network"></a>Azure Content Delivery Network

Azure では、資産をユーザーの近くにキャッシュすることで資産のタイムリーな配信を可能にする分散型コンテンツ配信ネットワーク (CDN) が提供されます。 このキャッシュは、顧客のエクスペリエンスの向上に役立ちます。 また、コンテンツのダウンロード時に、CDN エンドポイントとアプリケーションをホストしているデータセンター間で発生するネットワークの問題に起因する問題を防ぐこともできます。 Azure CDN は、Azure でホストされていないアプリケーションで使用することもできます。

#### <a name="action"></a>アクション

Azure CDN プロファイルを構成または監視するには:

1. **[CDN プロファイル]** に移動します。
2. 新しいプロファイルを構成する: **[追加]** を選択し、画面の指示に従います。
3. 既存のプロファイルを管理する: 一覧から目的のプロファイルを選択します。

::: zone target="chromeless"

<!-- markdownlint-disable DOCSMD001 -->

::: form action="OpenBlade[#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Cdn%2FProfiles]" submitText="Go to CDN profiles" :::

<!-- markdownlint-enable DOCSMD001 -->

::: zone-end

### <a name="learn-more"></a>詳細情報

- [Azure Front Door](/azure/frontdoor/front-door-overview)
- [Traffic Manager](/azure/traffic-manager)
- [Azure Content Delivery Network](/azure/cdn)
