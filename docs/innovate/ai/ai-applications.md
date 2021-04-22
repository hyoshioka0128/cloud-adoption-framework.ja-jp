---
title: AI アプリケーション
description: AI をアプリケーション開発に注入する AI の適用分野について説明します。 Microsoft Azure Cognitive Services を使用すれば、エンタープライズに対応できます。
author: v-hanki
ms.author: janet
ms.date: 07/14/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: innovate
ms.custom: think-tank, seo-caf-innovate
keywords: AI アプリケーション, AI アプリケーションの例, AI アプリケーションのチェックリスト, AI の適用分野, AI エージェント
ms.openlocfilehash: d9f7a2e4ebdf28cb1831ae67e58c960770f4c0b1
ms.sourcegitcommit: 51565dc4d3a1858bd62f708f2e4c082fbd4c6fe4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/20/2021
ms.locfileid: "107747162"
---
# <a name="ai-applications-and-agents"></a>AI アプリケーションとエージェント

AI の適用分野は、電子取引プラットフォーム、リモート センシング、ロボット制御、医療診断など、さまざまなアプリケーションをカバーしています。 アプリケーションへの AI の導入は、困難かつ時間がかかる作業になる場合があります。 最近までは、機械学習を深く理解することに加え、データの取得、モデルのトレーニング、大規模なデプロイを行うために数か月をかけて開発する必要がありました。 それでも、成功は保証されていませんでした。 その道のりには、AI 投資の価値をチームが実感できなくなる障害物や落とし穴が数多くありました。

## <a name="ai-applications"></a>AI アプリケーション

Microsoft Azure Cognitive Services は、API として使用できるドメイン固有の AI サービスです。 これらのサービスは、アプリケーションに AI を組み入れるという課題に対処し、生産性、エンタープライズ対応性、信頼性を高めるように設計されています。 独自のモデルを構築してデプロイしなくても、AI の最新の進歩を基に構築することが可能になります。それどころか、数行のコードを使用するだけで AI モデルをデプロイすることができます。 大規模なデータ サイエンス チームがなくても、見る、聞く、話す、理解する、さらには推論を始めるための AI アプリケーションを迅速に作成できます。

AI アプリケーションの一般的な例を次に示します。

- センチメント分析
- オブジェクトの検出
- 翻訳
- パーソナル化
- ロボット プロセスの自動化

AI アプリケーションの開発とデプロイを計画するには、次のガイドラインに従ってください。

- Azure Cognitive Services 内で提供されている多数の機能とサービスについてよく理解し、どれを使用するかを決定します。
- AI モデルをトレーニングしてカスタマイズするためのカスタム データがあるかどうかを判断します。 いくつかの Cognitive Services はカスタマイズできます。
- Azure Cognitive Services のクイックスタート チュートリアルを参照して、SDK と REST API の使用方法を確認します。 Cognitive Services SDK は、C#、Python、Java、JavaScript、Go を含む、多くの一般的な開発言語で利用できます。
- これらの Cognitive Services をコンテナーにデプロイする必要があるかどうかを判断します。

## <a name="ai-applications-checklist"></a>AI アプリケーションのチェックリスト

まず、ビジョン、スピーチ、言語、意思決定、Web 検索など、Azure Cognitive Services 内のさまざまなカテゴリとサービスについて理解を深めてください。 製品のページにアクセスして詳細を確認し、デモを操作します。 REST API と SDK について、ページの各カテゴリで、一連のクイック スタート、チュートリアル、ハウツー ガイドが提供されています。 また、一般的なシナリオを紹介し、Cognitive Services を利用して最初の AI アプリケーションを作成する方法について示した電子書籍を読むこともできます。

- [Cognitive Services のドキュメント](/azure/cognitive-services/)を読みます。
- [製品やサービス全体の対話型デモのページ](https://azure.microsoft.com/services/cognitive-services/)にアクセスします。
- 電子書籍『[コグニティブ API を使用してインテリジェントなアプリを構築する](https://azure.microsoft.com/resources/building-intelligent-apps-with-cognitive-apis/)』を読みます。
- [インテリジェント キオスク](https://github.com/Microsoft/Cognitive-Samples-IntelligentKiosk)をダウンロードし、サービスを体験してデモを実行します。 <!-- docutune:casing "Intelligent Kiosk" -->
- 詳細について、[Azure Cognitive Services のコンテナー サポートに関するページ](/azure/cognitive-services/cognitive-services-container-support)を参照します。
- [AI ソリューションの参照アーキテクチャ](/azure/architecture/browse/#ai--machine-learning)を確認します。

## <a name="ai-agents"></a>AI エージェント

Microsoft Azure AI プラットフォームは、開発者がプロジェクトを変革し推進できるように支援することを目的としています。 会話型 AI のために、Azure では Azure Bot Service および Bot Framework SDK とツールが用意されており、開発者はこれらを使用して豊富な会話エクスペリエンスを構築できます。 さらに、開発者は Language Understanding (LUIS)、QnA Maker、音声サービスなどの Azure Cognitive Services を使用して、エンド ユーザーの発話を理解して会話するチャットボットの機能を追加することが可能です。

会話 AI やチャットボットのソリューションが使用される一般的なシナリオを以下に示します。

- 情報提供 Q&A チャットボット
- カスタマー サービスまたはサポート チャットボット
- IT ヘルプ デスクまたは HR チャットボット
- 販売または eコマース チャットボット
- 音声対応デバイス

> [!NOTE]
> Microsoft では、基本的にコードが不要かわずかなコードしか使用しないエクスペリエンスでチャットボットを構築することを望む開発者向けに、Bot Framework 上に構築された Power Virtual Agents を提供しています。 このシナリオでは、開発者がボットを自分でホストしたり、自然言語やその他の AI モデルを Cognitive Services で制御したりする必要はありません。

## <a name="ai-agents-checklist"></a>AI エージェントのチェックリスト

Azure Bot Service と Microsoft Bot Framework には次の特性があります。

- Bot Framework は、ボットの設計、構築、テストを支援する SDK を提供するオープンソース オファリングです。 この SDK は、C#、JavaScript、Python、Java で使用できます。 また、Bot Framework Composer での無料のビジュアル作成キャンバスと、Bot Framework Emulator でのテスト ツールも提供されています。
- Azure Bot Service は Azure 内の専用サービスで、これを使用することにより、お客様は Azure でボットをホストまたは公開したり、人気のあるチャネルに接続したりできます。

Azure Bot Service と Bot Framework の詳細については、以下を参照してください。

- [Azure Bot Service および Bot Framework の概要](/azure/bot-service/bot-service-overview-introduction)
- [ボット設計の原則](/azure/bot-service/bot-service-design-principles)

作業を開始する最も簡単な方法の 1 つは、Azure Cognitive Services の一部である QnA Maker を使用することです。これを使用すると、FAQ ドキュメントや Web サイトをインテリジェントに変換して、Q&A エクスペリエンスを数分で作成できます。 QnA Maker を使用するには、以下を参照してください。

- [チュートリアル:ボットで QnA Maker を使用して質問に回答する](/azure/bot-service/bot-builder-tutorial-add-qna)
- [QnA Maker サービスを試してみる](https://www.qnamaker.ai/)

ボット開発用の Bot Framework SDK とツールをダウンロードして使用するには、以下を参照してください。

- [Bot Framework SDK とツールの最新バージョン](/azure/bot-service/what-is-new)
- [最初のボットの作成](/composer/quickstart-create-bot)
- [Bot Framework SDK for .NET を使用したボットの作成](/azure/bot-service/dotnet/bot-builder-dotnet-sdk-quickstart)

Cognitive Services を追加してボットをさらにインテリジェントにする方法を学習するには、以下を参照してください。

- [AI アプリケーションを構築するための開発者ガイド (電子書籍)](https://www.oreilly.com/library/view/a-developers-guide/9781492080619/)
- [Cognitive Services のドキュメント](/azure/cognitive-services/)

Bot Framework ソリューション アクセラレータを使用して独自の仮想アシスタントを構築する方法と、カレンダー、メール、目的地、To Do などの一般的なスキルのセットを選択する方法について学習するには、[Bot Framework ソリューションのドキュメント](https://microsoft.github.io/botframework-solutions/index)を参照してください。

## <a name="next-steps"></a>次のステップ

その他の AI ソリューション カテゴリを調べる:

- [機械学習](./machine-learning.md)
- [ナレッジ マイニング](./knowledge-mining.md)
