---
title: Azure イノベーション:AI によるイノベーション
description: お客様のニーズを予測し、ビジネス プロセスを自動化し、非構造化データに潜む情報を発見し、より優れたエクスペリエンスを提供する新しい方法でお客様と関わり合うための Azure ソリューションについて説明します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: innovate
ms.custom: fasttrack-edit, AQC
ms.localizationpriority: high
ms.openlocfilehash: 74db213a6d03eaf3df75f8eed88260bb1e457cee
ms.sourcegitcommit: 9163a60a28ffce78ceb5dc8dc4fa1b83d7f56e6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2020
ms.locfileid: "86448862"
---
<!-- cSpell:ignore ONNX -->

::: zone target="docs"

# <a name="azure-innovation-guide-innovate-with-ai"></a>Azure イノベーション ガイド:AI によるイノベーション

::: zone-end

::: zone target="chromeless"

# <a name="innovate-with-ai"></a>AI によるイノベーション

::: zone-end

会社には、イノベーターとしてビジネスとお客様に関する豊富な情報があります。 会社で AI を使用すると、お客様のニーズを予測し、ビジネス プロセスを自動化し、非構造化データに潜む情報を発見し、より優れたエクスペリエンスを提供する新しい方法でお客様と関わり合うことができます。 この記事では、AI を使用した革新的なアプローチをいくつか紹介します。 次の表は、実装のニーズに基づいて最適なソリューションを見つけるうえで役立ちます。

| ソリューションのカテゴリ | 説明                                                                                                                              | 必要なスキル              |
|-----------------------------|------------------------------------------------------------------------------------------------------------------------------------------|------------------------------|
| 機械学習            | **Azure Machine Learning** <br> 独自の機械学習モデルを構築、デプロイ、管理します。                                                       | データ サイエンティストと開発者 |
| AI アプリケーションとエージェント             | **Azure Cognitive Services** <br> データを使用してカスタマイズできるビジョン、音声、言語、および意思決定にドメイン固有の AI モデルを使用します。 <br><br> **Azure Bot Service** <br> アプリケーションや Web サイトにボットを追加して、顧客エンゲージメントを向上させます。 | Developer                    |
| ナレッジ マイニング            | **Azure Cognitive Search** <br> ドキュメント、契約、画像、その他のデータの種類を含め、コンテンツに潜む分析情報を明らかにします。      | Developer                    |

## <a name="machine-learning"></a>[機械学習](#tab/MachineLearning)

Azure には高度な機械学習機能が用意されています。 Azure Machine Learning を使用すると、クラウドとエッジ全体で機械学習モデルをすばやく簡単に構築、トレーニング、デプロイすることができます。 自動機械学習を使用すると、モデルをより短時間で開発できます。 拘束されることなく、任意のツールとフレームワークを使用できます。

Azure Machine Learning の概要については、[Azure Machine Learning の概要](https://docs.microsoft.com/azure/machine-learning/overview-what-is-azure-ml)と[初めての機械学習実験の概要](https://docs.microsoft.com/azure/machine-learning/tutorial-1st-experiment-sdk-setup)に関するページを参照してください。 機械学習のオープン ソース モデルの形式とランタイムの詳細については、[ONNX Runtime](http://onnxruntime.ai) のドキュメントを参照してください。

<!-- markdownlint-disable MD024 -->

### <a name="action"></a>アクション

データ サイエンティストは、Azure Machine Learning を使用すると、Python や R などの高度な言語を使用し、ドラッグアンドドロップのビジュアル エクスペリエンスも使用してモデルをトレーニングおよび構築することができます。 Azure Machine Learning の使用を開始するには:

1. Azure portal で **Machine Learning** を検索して選択します。

1. **[追加]** を選択し、ポータルの手順に従ってワークスペースを作成します。

1. 新しいワークスペースには、データ サイエンティストがモデルのトレーニング、構築、デプロイ、管理に使用できるロー コード型とコード駆動型の両方のアプローチが用意されています。

::: zone target="chromeless"

<!-- markdownlint-disable DOCSMD001 -->

::: form action="OpenBlade[#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.MachineLearningServices%2FWorkspaces]" submitText="Go to Azure Machine Learning resources" :::

<!-- markdownlint-enable DOCSMD001 -->

::: zone-end

::: zone target="docs"

[Azure portal](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.MachineLearningServices%2FWorkspaces) で Azure Machine Learning リソースに直接進みます。

::: zone-end

## <a name="ai-applications-and-agents"></a>[AI アプリケーションとエージェント](#tab/AIAppsAndAgents)

Azure には Cognitive Services という一連の事前構築済み AI サービスが用意されており、AI アプリケーションを簡単に構築できます。 さらに、Azure にはボット サービスが用意されており、開発者はお客様と従業員のエンゲージメントを向上させる会話型 AI エージェントを構築することができます。

### <a name="ai-applications"></a>AI アプリケーション

Cognitive Services を使用すると、予測モデルに追加のトレーニングを実行することなく、ビジョン、音声、言語、および意思決定の AI 機能をアプリケーションに組み込むことができます。 これらのサービスは、予測モデルをトレーニングするデータ サイエンティストがスタッフにいない場合に最適かつ効果的です。 サービスによっては、トレーニングが不要な場合と、最小限のトレーニングのみを必要とする場合があります。

ビジョン、音声、言語、意思決定全体で利用可能なサービスの一覧と、必要となる可能性のあるトレーニングの量については、[Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-and-machine-learning#service-requirements-for-the-data-model) のドキュメントを参照してください。

#### <a name="action"></a>アクション

Cognitive Services API の使用を開始するには:

1. Azure portal で、**Cognitive Services** を検索して選択します。

1. **[追加]** を選択して、Azure Marketplace で Cognitive Service API を探します。

1. サービスを検索して選択します。

    - 使用するサービスの名前がわかっている場合は、 **[Marketplace を検索]** に名前を入力し、サービスを選択します。

    - Cognitive Services API の一覧については、 **[Cognitive Services]** 見出しの横にある **[さらに参照する]** を選択し、サービスを選択します。

1. **[作成]** を選択し、ポータルの手順に従ってサービスをプロビジョニングします。

::: zone target="chromeless"

<!-- markdownlint-disable DOCSMD001 -->

::: form action="OpenBlade[#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.CognitiveServices%2FAccounts]" submitText="Go to Cognitive Services" :::

<!-- markdownlint-enable DOCSMD001 -->

::: zone-end

::: zone target="docs"

[Azure portal](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.CognitiveServices%2FAccounts) で Cognitive Services に直接進みます。

::: zone-end

### <a name="ai-agents"></a>AI エージェント

Bot Framework と Azure Bot Service を利用した会話エクスペリエンスを通じて、お客様とより自然に対話し、顧客エンゲージメントを向上させます。 さらに、Language Understanding (LUIS)、QnA Maker、音声サービスなどの Cognitive Services API を使用して、お客様が一般的なタスクを自力で実行できるようにすることで、コール センターのエージェントはより繊細で高い価値のケースに集中できるようになります。

ボットの構築方法の詳細については、[Azure Bot Service](https://docs.microsoft.com/learn/paths/create-bots-with-the-azure-bot-service/) のラーニング パスに関するページを参照してください。

#### <a name="action"></a>アクション

Azure Bot Service の使用を開始するには:

1. Azure portal で、**Bot Services** を検索して選択します。

1. **[追加]** を選択し、 **[Web アプリ ボット]** または **[ボット チャンネル登録]** を選択します。

1. **[作成]** を選択し、ポータルの手順に従ってサービスをプロビジョニングします。

::: zone target="chromeless"

<!-- markdownlint-disable DOCSMD001 -->

::: form action="OpenBlade[#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.BotService%2FBotServices]" submitText="Go to Azure Bot Service" :::

<!-- markdownlint-enable DOCSMD001 -->

::: zone-end

::: zone target="docs"

[Azure portal](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.BotService%2FBotServices) で Azure Bot Service に直接進みます。

::: zone-end

## <a name="knowledge-mining"></a>[ナレッジ マイニング](#tab/KnowledgeMining)

Azure Cognitive Search を使用すると、ドキュメント、画像、メディアなどのコンテンツに潜む分析情報を明らかにすることができます。 AI 機能が組み込まれたこの唯一のクラウド検索サービスを使用すると、コンテンツのパターンとリレーションシップを発見し、センチメントを理解し、キーフレーズを抽出することができます。

<!-- docsTest:ignore "Azure Search" -->

Azure Cognitive Search (旧称 Azure Search) には、Bing と Microsoft Office に 10 年以上にわたって使用されてきたものと同じ統合された Microsoft 自然言語スタックと、ビジョン、言語、および音声を対象とする AI サービスが使用されています。 イノベーションに時間をかけ、複雑なクラウド検索ソリューションの保守にかける時間を減らすことができます。

詳細については、「[Azure Cognitive Search とは](https://docs.microsoft.com/azure/search/search-what-is-azure-search)」を参照してください。

### <a name="action"></a>アクション

Azure Cognitive Search の使用を開始するには:

1. Azure portal で、**Azure Cognitive Search** を検索して選択します。

1. ポータルの手順に従ってサービスをプロビジョニングします。

::: zone target="chromeless"

<!-- markdownlint-disable DOCSMD001 -->

::: form action="OpenBlade[#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FSearchServices]" submitText="Go to Azure Cognitive Search" :::

<!-- markdownlint-enable DOCSMD001 -->

::: zone-end

::: zone target="docs"

[Azure portal](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FSearchServices) で Azure Cognitive Search に直接進みます。

::: zone-end

---
