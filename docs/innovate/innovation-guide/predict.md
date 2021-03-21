---
title: Azure での AI によるイノベーション
description: お客様のニーズを予測し、ビジネス プロセスを自動化し、非構造化データに潜む情報を発見し、より優れたエクスペリエンスを提供する新しい方法でお客様と関わり合うための Azure ソリューションについて説明します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 01/27/2021
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: innovate
ms.localizationpriority: high
ms.custom: internal, fasttrack-edit, AQC, seo-caf-innovate
keywords: ビジネス プロセスの自動化, AI イノベーション, 機械学習, ナレッジ マイニング
ms.openlocfilehash: c9d9fecd738d098d42fb4f03343a16d28d55664e
ms.sourcegitcommit: c167c45b66cc7324b60c88b8b7aac439f956b65d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102208576"
---
<!-- cSpell:ignore ONNX -->

# <a name="innovate-with-ai-in-azure"></a>Azure での AI によるイノベーション

会社には、イノベーターとしてビジネスとお客様に関する豊富な情報があります。 AI イノベーションを利用すると、お客様の会社は:

- 顧客のニーズに関して予測できます。
- ビジネス プロセスを自動化できます。
- 構造化されていないデータに隠れている情報を発見できます。
- 新しい方法で顧客と交流し、より良い体験を提供できます。

 この記事では、AI を使用した革新的なアプローチをいくつか紹介します。 イノベーションにより、お客様の会社のビジネス分析情報を既存のデータにまで拡張できます。 次の表は、実装のニーズに最適なソリューションを見つけるうえで役立ちます。

| ソリューションのカテゴリ | 説明                                                                                                                              | 必要なスキル              |
|-----------------------------|------------------------------------------------------------------------------------------------------------------------------------------|------------------------------|
| 機械学習            | **Azure Machine Learning** <br> 独自の機械学習モデルを構築、デプロイ、管理します。                                                       | データ サイエンティストと開発者 |
| AI アプリケーションとエージェント             | **Azure Cognitive Services** <br> データを使用してカスタマイズできるビジョン、音声、言語、および意思決定にドメイン固有の AI モデルを使用します。 <br><br> **Azure Bot Service** <br> アプリケーションや Web サイトにボットを追加して、顧客エンゲージメントを向上させます。 | Developer                    |
| ナレッジ マイニング            | **Azure Cognitive Search** <br> ドキュメント、契約、画像、その他のデータの種類を含め、コンテンツに隠れている分析情報を明らかにします。      | Developer                    |

## <a name="machine-learning"></a>機械学習

Azure には高度な機械学習機能が用意されています。 Azure Machine Learning を使用すると、クラウドとエッジ全体で機械学習モデルを構築、トレーニング、デプロイできます。 自動機械学習を使用すると、モデルを短時間で開発できます。 拘束されることなく、任意のツールとフレームワークを使用できます。

詳細については、[Azure Machine Learning の概要](/azure/machine-learning/overview-what-is-azure-ml)と[初めての機械学習実験の概要](/azure/machine-learning/tutorial-1st-experiment-sdk-setup)に関するページを参照してください。 機械学習のオープンソース モデルの形式とランタイムの詳細については、[ONNX Runtime](https://www.onnxruntime.ai/) を参照してください。

<!-- markdownlint-disable MD024 -->

### <a name="action"></a>アクション

データ サイエンティストは、Azure Machine Learning を使用すると、Python や R などの高度な言語を使用し、ドラッグアンドドロップのビジュアル エクスペリエンスも使用してモデルをトレーニングし、構築できます。 Azure Machine Learning の使用を開始するには:

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

## <a name="ai-applications-and-agents"></a>AI アプリケーションとエージェント

Azure には Cognitive Services という一連の事前構築済み AI サービスが用意されており、AI アプリケーションを構築できます。 さらに、Azure にはボット サービスが用意されており、開発者はお客様と従業員のエンゲージメントを向上させる会話型 AI エージェントを構築することができます。

### <a name="ai-applications"></a>AI アプリケーション

Cognitive Services を使用すると、ビジョン、音声、言語、意思決定の AI 機能をアプリケーションに組み込むことができます。 ほとんどの予測モデルには追加のトレーニングが必要ありません。 これらのサービスは、予測モデルをトレーニングするデータ サイエンティストがスタッフにいない場合に役立ちます。 他のサービスでは、最小限のトレーニングを必要とします。

必要となる可能性のあるトレーニングと、ビジョン、音声、言語、意思決定全体で利用可能なサービスの一覧については、[Cognitive Services](/azure/cognitive-services/cognitive-services-and-machine-learning#service-requirements-for-the-data-model) のドキュメントを参照してください。

#### <a name="action"></a>アクション

Cognitive Services API の使用を開始するには:

1. Azure portal で、**Cognitive Services** を検索して選択します。

1. **[追加]** を選択して、Azure Marketplace で Cognitive Service API を探します。

1. サービスを検索して選択します。

    - 使用するサービスの名前がわかっている場合は、 **[Marketplace を検索]** に名前を入力します。 次に、サービスを選択します。

    - Cognitive Services API の一覧については、 **[Cognitive Services]** 見出しの横にある **[さらに参照する]** を選択します。 次に、サービスを選択します。

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

Bot Framework と Azure Bot Service を利用した会話エクスペリエンスを通じて、お客様とより自然に対話し、顧客エンゲージメントを向上させます。 加えて、Language Understanding (LUIS)、QnA Maker、Speech サービスなど、Cognitive Services API を使用します。 これらはお客様が一般的な作業をこなす上で役立ち、コール センターの担当者は、微妙な違いが多く含まれ、価値の高い案件に集中できます。

ボットの構築方法の詳細については、[Azure Bot Service](/learn/paths/create-bots-with-the-azure-bot-service/) に関するページを参照してください。

#### <a name="action"></a>アクション

Azure Bot Service の使用を開始するには:

1. Azure portal で、**Bot Services** を検索して選択します。

1. **[追加]** を選択し、 **[Web アプリ ボット]** または **[ボット チャンネル登録]** を選択します。

1. **［作成］** を選択します 次に、ポータルの手順に従ってサービスをプロビジョニングします。

::: zone target="chromeless"

<!-- markdownlint-disable DOCSMD001 -->

::: form action="OpenBlade[#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.BotService%2FBotServices]" submitText="Go to Azure Bot Service" :::

<!-- markdownlint-enable DOCSMD001 -->

::: zone-end

::: zone target="docs"

[Azure portal](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.BotService%2FBotServices) で Azure Bot Service に直接進みます。

::: zone-end

## <a name="knowledge-mining"></a>ナレッジ マイニング

ナレッジ マイニングでは、AI を使用して、膨大な量の非構造化、半構造化、構造化情報に対するコンテンツの解釈を促進します。 Azure Cognitive Search を使用すると、ドキュメント、画像、メディアなどのコンテンツに潜む分析情報を明らかにすることができます。 コンテンツに含まれるパターンや関係を発見したり、センチメントを理解したり、キー フレーズを抽出したりできます。

<!-- docutune:ignore "Azure Search" -->

Azure Cognitive Search では、Bing や Microsoft Office で使用されているものと同じ自然言語スタックが使用されます。 イノベーションに時間をかけ、複雑なクラウド検索ソリューションの保守にかける時間を減らすことができます。

詳細については、「[Azure Cognitive Search とは](/azure/search/search-what-is-azure-search)」を参照してください。

### <a name="action"></a>アクション

作業を開始するには:

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
