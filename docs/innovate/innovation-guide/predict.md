---
title: Azure イノベーション ガイド:予測と影響
titleSuffix: Microsoft Cloud Adoption Framework for Azure
description: Azure を使用した予測と影響について説明します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 10/17/2019
ms.topic: article
ms.service: cloud-adoption-framework
ms.subservice: innovate
ms.custom: fasttrack-edit, AQC
ms.localizationpriority: high
ms.openlocfilehash: bee593827b58f1ccca6e82a3ae36d2b6352f0754
ms.sourcegitcommit: 50788e12bb744dd44da14184b3e884f9bddab828
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/18/2019
ms.locfileid: "74160402"
---
::: zone target="docs"

# <a name="azure-innovation-guide-predict-and-influence"></a>Azure イノベーション ガイド:予測と影響

::: zone-end

::: zone target="chromeless"

# <a name="predict-and-influence"></a>予測と影響

::: zone-end

イノベーターとして、会社には、顧客ベースのデータ、行動、ニーズに関する分析情報があります。 それらの分析情報を研究することで、場合によっては顧客自身がニーズを認識する前に、顧客のニーズを予測できます。 この記事では、予測ソリューションを提供するアプローチをいくつか紹介します。 最後のセクションでは、顧客の行動に影響を与えるために、予測をソリューションに統合するアプローチについて説明します。

次の表は、実装のニーズに基づいて最適なソリューションを見つけるうえで役立ちます。

|Service  |事前構築済みのモデル  |ビルドと実験  |Python でのトレーニングと構築|必要なスキル|
|---------|---------|---------|---------|---------|
|Azure Cognitive Services|はい|いいえ|いいえ|API と開発者のスキル|
|Azure Machine Learning Studio|はい|はい|いいえ|予測アルゴリズムに関する一般的な知識|
|Azure Machine Learning サービス|はい|はい|はい|データ サイエンティスト|

## <a name="azure-cognitive-servicestabcognitiveservices"></a>[Azure Cognitive Services](#tab/CognitiveServices)

顧客のニーズを最も迅速かつ簡単に予測できるのは、Azure Cognitive Services です。 Cognitive Services を使用すると、追加でトレーニングを行う必要なく、既存のモデルを使用して予測を行うことができます。 これらのサービスは、予測モデルをトレーニングするデータ サイエンティストがスタッフにいない場合に最適かつ効果的です。 一部のサービスでは、トレーニングは不要です。 最小限のトレーニングのみを必要とするサービスもあります。

利用可能なサービスと必要と考えられるトレーニングの量の一覧については、「[Cognitive Services と機械学習](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-and-machine-learning#service-requirements-for-the-data-model)」をご覧ください。

### <a name="action"></a>Action

Cognitive Services API を使用するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.CognitiveServices%2Faccounts) で **[Cognitive Services]** に移動します。
2. **[追加]** を選択して、Azure Marketplace で Cognitive Service API を探します。
3. 次のいずれかを実行します。
   - 使用するサービスの名前がわかっている場合は、 **[Marketplace を検索]** ボックスに名前を入力します。
   - Cognitive Services APIs の一覧については、Cognitive Services の見出しの横の **[もっと見る]** リンクを選択します。

::: zone target="chromeless"

<!-- markdownlint-disable DOCSMD001 -->

::: form action="OpenBlade[#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.CognitiveServices%2Faccounts]" submitText="Go to Cognitive Services" :::

<!-- markdownlint-enable DOCSMD001 -->

::: zone-end

::: zone target="docs"

[Azure portal](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.CognitiveServices%2Faccounts) で Cognitive Services に直接進みます。

::: zone-end

## <a name="azure-machine-learning-studiotabmachinelearningstudio"></a>[Azure Machine Learning Studio](#tab/MachineLearningStudio)

Cognitive Services 内の既存のモデルが目的の予測に合わない場合は、データ サイエンティストの高いスキルがなくても、Azure Machine Learning Studio を使用して目的の予測を構築できます。

<!-- markdownlint-disable MD024 -->

### <a name="action"></a>Action

Azure Machine Learning Studio で、次の手順に従ってモデルを作成し、モデルを試すことができます。

1. [Azure portal](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.MachineLearning%2Fworkspaces) で、**Azure Machine Learning Studio** に直接移動します。
2. **[Create Machine Learning Studio Workspace]\(Machine Learning Studio ワークスペースの作成\)** を選択し、プロンプトに従ってワークスペースを作成します。

   新しいワークスペースには、詳細なトレーニングの代わりに、モデルを作成して試すためのドラッグ アンド ドロップ インターフェイスが用意されています。

::: zone target="chromeless"

<!-- markdownlint-disable DOCSMD001 -->

::: form action="OpenBlade[#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.MachineLearning%2Fworkspaces]" submitText="Go to Azure Machine Learning Studio" :::

<!-- markdownlint-enable DOCSMD001 -->

::: zone-end

::: zone target="docs"

[Azure portal](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.MachineLearning%2Fworkspaces) で Azure Machine Learning Studio に直接進みます。

::: zone-end

## <a name="azure-machine-learning-servicetabmachinelearningservice"></a>[Azure Machine Learning サービス](#tab/MachineLearningService)

Azure Machine Learning service では、顧客データ セットのより詳細なトレーニングに必要となる、詳細なコードベースのアプローチが提供されます。 データ サイエンティストは、Python などの言語を使用して、顧客のニーズを予測するアルゴリズムをトレーニングおよび構築できます。

### <a name="action"></a>Action

データ サイエンティストは、Azure Machine Learning service を使用して、Python などの高度な言語でモデルをトレーニングおよび構築できます。

1. **[Azure Machine Learning service]** に移動します。
2. **[Create Machine Learning service workspaces]\(Machine Learning service ワークスペースの作成\)** を選択し、プロンプトに従ってワークスペースを作成します。
3. 新しいワークスペースでは、データ サイエンティストが、顧客のニーズを正確に予測するためにより高度な分析を必要とするモデルをトレーニングおよび構築するためのコード駆動型のアプローチが提供されます。

::: zone target="chromeless"

<!-- markdownlint-disable DOCSMD001 -->

::: form action="OpenBlade[#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.MachineLearningServices%2Fworkspaces]" submitText="Go to Azure Machine Learning service" :::

<!-- markdownlint-enable DOCSMD001 -->

::: zone-end

::: zone target="docs"

[Azure portal](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.MachineLearningServices%2Fworkspaces) で Azure Machine Learning Studio に直接進みます。

::: zone-end

## <a name="influence"></a>影響

前述のどのアプローチでも、予測モデルをアプリケーションに公開する API が生成されます。 ソリューション内でいずれかのアプローチを使用して、顧客から収集したデータを予測 API に提供します。 その後、推奨される次の手順として、結果をカスタマー エクスペリエンスに統合できます。

それらの手順では、顧客の行動パターンを形成し、顧客の反応に影響を与えることができます。 推奨される次の手順は予測アルゴリズムに基づいているため、顧客の以前のニーズと利用可能なデータを使って、多くの場合、顧客自身がニーズの存在を認識する前に、顧客の将来のニーズを予測し、そのニーズを満たします。
