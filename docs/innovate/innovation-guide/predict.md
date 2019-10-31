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
ms.openlocfilehash: b2ed1b072d5226649e5248e350edfa3578978c4c
ms.sourcegitcommit: 910efd3e686bd6b9bf93951d84253b43d4cc82b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2019
ms.locfileid: "72769255"
---
::: zone target="docs"

# <a name="azure-innovation-guide-predict-and-influence"></a>Azure イノベーション ガイド:予測と影響

::: zone-end

::: zone target="chromeless"

# <a name="predict-and-influence"></a>予測と影響

::: zone-end

イノベーターである、あなたの会社には、顧客ベースのデータ、行動、ニーズに関する分析情報があります。 それらの分析情報を研究することにより、顧客がそれらのニーズを認識する前でも、ニーズを予測することができます。 この記事では、予測ソリューションを提供するためのいくつかの方法を紹介します。 後のセクションでは、顧客の行動に影響を与えるために、それらの予測をお使いのソリューションに戻して統合する方法について説明します。

次の表は、実装のニーズに基づいて最適なソリューションを見つけることができるように設計されています。

|Service  |事前に構築されているモデル  |ビルドと実験  |Python でのトレーニングとビルド|必要なスキル|
|---------|---------|---------|---------|---------|
|Cognitive Services|はい|いいえ|いいえ|API と開発者のスキル|
|Azure Machine Learning Studio|はい|はい|いいえ|予測アルゴリズムに関する一般的な知識|
|Azure Machine Learning サービス|はい|はい|はい|データ サイエンティスト|

## <a name="azure-cognitive-servicestabcognitiveservices"></a>[Azure Cognitive Services](#tab/CognitiveServices)

最も迅速かつ簡単に予測できるのは、Azure Cognitive Services です。 Cognitive Services を使用すると、追加でトレーニングを行う必要なく、既存のモデルを使用して予測を行うことができます。 これらのサービスは、予測モデルをトレーニングするデータ科学者がスタッフにいない場合に最適です。 一部のサービスでは、トレーニングは不要です。 他では最小限のトレーニングのみ必要です。

利用可能なサービスと必要なトレーニングの量の一覧については、「[Cognitive Services と機械学習](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-and-machine-learning#service-requirements-for-the-data-model)」を参照してください。

### <a name="action"></a>Action

Cognitive Services の API を使用するには:

1. **[Cognitive Services]** に移動します。
2. **[追加 +]** をクリックして、マーケットプレースから Cognitive Service を探します。
3. 使用するサービスの名前がわかっている場合は、 **[Marketplace を検索]** テキスト ボックスにサービス名を入力します。
4. または、Cognitive Services のヘッダーの横にある **[もっと見る]** リンクをクリックして、Cognitive Services の一覧を表示することもできます。

::: zone target="chromeless"

<!-- markdownlint-disable DOCSMD001 -->

::: form action="OpenBlade[#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.CognitiveServices%2Faccounts]" submitText="Go to Cognitive Services" :::

<!-- markdownlint-enable DOCSMD001 -->

::: zone-end

::: zone target="docs"

[Azure portal](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.CognitiveServices%2Faccounts) で Cognitive Services に直接進みます。

::: zone-end

## <a name="azure-machine-learning-studiotabmachinelearningstudio"></a>[Azure Machine Learning Studio](#tab/MachineLearningStudio)

Cognitive Services 内の既存のモデルが目的の予測に合わない場合、データ科学者の高いスキルなしに Azure Machine Learning Studio で、目的の予測を構築することができる場合があります。

<!-- markdownlint-disable MD024 -->

### <a name="action"></a>Action

次の方法で、Azure Machine Learning Studio を使用してモデルを作成し、そのモデルを試すことができます。

1. **[Azure Machine Learning Studio]** に移動します。
2. **[Create Machine Learning Studio Workspace]** \(Machine Learning Studio ワークスペースの作成\) をクリックして、プロンプトに従いワークスペースを作成します。
3. 新しいワークスペースには、詳細なトレーニングの代わりに、モデルを構築して実験するドラッグ アンド ドロップ インターフェイスがあります。

::: zone target="chromeless"

<!-- markdownlint-disable DOCSMD001 -->

::: form action="OpenBlade[#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.MachineLearning%2Fworkspaces]" submitText="Go to Azure Machine Learning Studio" :::

<!-- markdownlint-enable DOCSMD001 -->

::: zone-end

::: zone target="docs"

[Azure portal](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.MachineLearning%2Fworkspaces) で Azure Machine Learning Studio に直接進みます。

::: zone-end

## <a name="azure-machine-learning-servicetabmachinelearningservice"></a>[Azure Machine Learning サービス](#tab/MachineLearningService)

Azure Machine Learning service には、顧客のデータ セットをより詳細にトレーニングするために必要な、より詳細なコードベースの手段があります。 データ科学者は、Python などの言語を使用し、顧客のニーズを予測するアルゴリズムをトレーニングおよび構築できます。

### <a name="action"></a>Action

データ科学者は、次のように Azure Machine Learning Service を使用し、Python などの高度な言語でモデルをトレーニングおよび構築することができます。

1. **[Azure Machine Learning Service]** に移動します。
2. **[Create Machine Learning service workspaces]** \(Machine Learning service ワークスペースの作成\) をクリックして、プロンプトに従いワークスペースを作成します。
3. 新しいワークスペースには、モデルをトレーニングおよび構築する、データ科学者向けのコードを使用する方法が用意されています。これでは、顧客のニーズを正確に予測するためにより高度な分析がされている必要があります。

::: zone target="chromeless"

<!-- markdownlint-disable DOCSMD001 -->

::: form action="OpenBlade[#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.MachineLearningServices%2Fworkspaces]" submitText="Go to Azure Machine Learning Service" :::

<!-- markdownlint-enable DOCSMD001 -->

::: zone-end

::: zone target="docs"

[Azure portal](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.MachineLearningServices%2Fworkspaces) で Azure Machine Learning Studio に直接進みます。

::: zone-end

## <a name="influence"></a>影響

上記のすべての方法で、予測モデルをアプリケーションに公開する API が生成されます。 これらのいずれかの方法をソリューションで使用すると、顧客から収集したデータを予測 API にフィードできます。 その後、推奨される次の手順として、結果をカスタマー エクスペリエンスに統合できます。

それらの次の手順では、顧客の行動パターンが形成されるようにし、顧客の反応に影響を与えます。 推奨される次の手順は予測アルゴリズムに基づいているため、以前の顧客と使用可能なデータが使用され、しばしば顧客がそのようなニーズがあることを認識する前でも、顧客のニーズを予測し、そのニーズを満たします。
