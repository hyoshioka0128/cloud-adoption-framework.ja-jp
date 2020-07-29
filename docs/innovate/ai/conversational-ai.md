---
title: 会話型 AI
description: 会話型 AI のために、Azure では Azure Bot Service および Bot Framework SDK とツールが用意されており、開発者はこれらを使用して豊富な会話エクスペリエンスを構築できます。
author: v-hanki
ms.author: janet
ms.date: 07/14/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: innovate
ms.openlocfilehash: b73d7243ec3db4b6f36066674b140adcb20b9219
ms.sourcegitcommit: 9163a60a28ffce78ceb5dc8dc4fa1b83d7f56e6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2020
ms.locfileid: "86452714"
---
# <a name="conversational-ai"></a>会話型 AI

Microsoft の Azure AI プラットフォームは、開発者がプロジェクトをイノベーションして加速できるように支援することを目的としています。 具体的には、会話型 AI のために、Azure では Azure Bot Service および Bot Framework SDK とツールが用意されており、開発者はこれらを使用して豊富な会話エクスペリエンスを構築できます。 さらに、開発者は Language Understanding (LUIS)、QnA Maker、Speech サービスなどの Azure Cognitive Services (API として使用可能なドメイン固有の AI サービス) を使用して、チャットボットがエンド ユーザーの発話を理解して会話できるようにする機能を追加できます。

会話 AI やチャットボットのソリューションが使用される一般的なシナリオを以下に示します。

- 情報提供 Q&A チャットボット
- カスタマー サービスまたはサポート チャットボット
- IT ヘルプ デスクまたは HR チャットボット
- eコマースまたはセールス チャットボット
- 音声対応デバイス

> [!NOTE]
> Microsoft では、基本的にコードが不要かわずかなコードしか使用しないエクスペリエンスでチャットボットを構築することを望む開発者向けに、Bot Framework 上に構築された Power Virtual Agents を提供しています。 また、開発者は、ボットを自分でホストしたり、Cognitive Services を使用して自然言語やその他の AI モデルを制御したりする必要はありません。

## <a name="checklist"></a>チェック リスト

Azure Bot Service と Microsoft Bot Framework についての理解を深めます。

- Bot Framework は、C#、JavaScript、Python、Java で使用可能な SDK を提供するオープンソース オファリングで、お客様によるボットの設計、構築、テストを支援します。 さらに、Bot Framework Composer の無料の視覚的作成キャンバスと、Bot Framework Emulator のテスト ツールも用意されています。
- Azure Bot Service は Azure 内の専用サービスで、これを使用することにより、お客様は Azure でボットをホストまたは公開したり、人気のあるチャネルに接続したりできます。

- [Azure Bot Service と Bot Framework の概要を学習します](https://docs.microsoft.com/azure/bot-service/bot-service-overview-introduction?view=azure-bot-service-4.0)
- [ボット設計の原則](https://docs.microsoft.com/azure/bot-service/bot-service-design-principles?view=azure-bot-service-4.0)
- [Bot Framework SDK とツールの最新バージョンを確認します](https://docs.microsoft.com/azure/bot-service/what-is-new?view=azure-bot-service-4.0)

作業を開始する最も簡単な方法の 1 つは、Azure Cognitive Services の一部である QnA Maker を使用することです。これを使用すると、FAQ ドキュメントや Web サイトをインテリジェントに変換して、Q&A エクスペリエンスを数分で作成できます。

- [QnA Maker を使用して Q&A 機能を備えたボットをスピーディに作成する方法を学習します](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-add-qna?view=azure-bot-service-4.0&tabs=csharp)
- [QnA Maker サービスを直接試してみます](https://www.qnamaker.ai/)

ボット開発用の Bot Framework SDK とツールをダウンロードして使用します

- [Bot Framework Composer の 5 分間のクイック スタート](https://docs.microsoft.com/composer/)
- [Bot Framework SDK でボットを構築してテストします (C#、JavaScript、Python)](https://docs.microsoft.com/azure/bot-service/dotnet/bot-builder-dotnet-sdk-quickstart?view=azure-bot-service-4.0)

Cognitive Services を追加してボットをさらにインテリジェントにする方法を学習します。

- [AI アプリケーションを構築するための開発者ガイド](https://www.oreilly.com/library/view/a-developers-guide/9781492080619/) (電子書籍)
- [Cognitive Services の詳細情報](https://docs.microsoft.com/azure/cognitive-services/)

Bot Framework ソリューション アクセラレータを使用して独自の仮想アシスタントを構築する方法と、カレンダー、電子メール、目的地、To Do などの一般的なスキルのセットを選択する方法について学習します。

- [Bot Framework の仮想アシスタント ソリューション](https://microsoft.github.io/botframework-solutions/index)

## <a name="next-steps"></a>次のステップ

[ナレッジ マイニング](./knowledge-mining.md)