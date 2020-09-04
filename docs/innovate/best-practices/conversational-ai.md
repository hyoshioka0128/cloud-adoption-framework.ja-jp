---
title: AI エージェントとは
description: AI エージェントと会話型インターフェイスについて説明します。 ボットを計画、構築、テスト、発行、接続、および評価します。
author: v-hanki
ms.author: janet
ms.date: 07/14/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: innovate
ms.openlocfilehash: 1cdf730339c30f681fc1dc39cad85fcd9c8a909f
ms.sourcegitcommit: 07d56209d56ee199dd148dbac59671cbb57880c0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88884523"
---
<!-- docsTest:casing "natural language understanding" -->
<!-- cSpell:ignore Twilio -->

# <a name="what-are-ai-agents"></a>AI エージェントとは

ユーザーは会話型インターフェイスにより多く関わるようになってきており、ここでは、人間が自然言語を使って自身のニーズを表現し、タスクをすばやく完了するより自然な体験が実現されます。 多くの企業にとって、会話型 AI アプリケーションは、競争上の差別化要因になりつつあります。 多くの組織が戦略的に、顧客が時間を費やす同様のメッセージング プラットフォーム内でボットを利用できるようにしています。

世界中の組織が会話型 AI によってビジネスを変革しており、これにより、その顧客と従業員の両方とのより効率的で自然な対話が促進されます。 一般的なユース ケースには、次のようなものがあります。

- カスタマー サポート
- エンタープライズ アシスタント
- コール センターの最適化
- 社内音声アシスタント

## <a name="build-a-bot"></a>ボットを作成する

Azure Bot Service および Bot Framework は、このプロセスを支援するために、統合された一連のツールとサービスを提供します。 ご自身の好みの開発環境やコマンド ライン ツールを選んで、ボットを作成できます。 C#、JavaScript、TypeScript、および Python 用の SDK が存在します。 Java 用の SDK は開発中です。 ボット開発のさまざまなステージ用にツールが用意されており、ボットの設計と構築に役立てることができます。

![ボット開発のさまざまな段階向けのツールを示す図。](../../_images/ai-bot-dev-tools.png)

### <a name="plan"></a>プラン

目標、プロセス、およびユーザーのニーズを完全に理解することが、成功するボットを作成するプロセスにとって重要です。 コードを書く前に、ボットの[デザイン ガイドライン](/azure/bot-service/bot-service-design-principles?view=azure-bot-service-4.0)でベスト プラクティスを確認し、作成するボットに必要な事柄を見極めてください。 単純なボットを作成することも、音声、自然言語の解釈、質問への応答などの高度な機能を含めることもできます。

計画フェーズでボットを設計する場合は、次の点を考慮してください。

- ボットのペルソナの定義:
  - ボットの外観は?
    - 名前は?
    - ボットのパーソナリティは? 性別を設定するか?
    - 難しい状況や質問をボットはどのように処理するか?
- 会話フローの設計:
  - 自身のユース ケースで予測される会話はどのようなものか?
- 評価計画の定義:
  - 成功の測定方法
  - サービスを向上させるために使用する測定値

ボットを設計する方法の詳細については、「[ボット設計の原則](/azure/bot-service/bot-service-design-principles?view=azure-bot-service-4.0)」を参照してください。

### <a name="build"></a>Build

作成するボットは会話型インターフェイスを実装した Web サービスであり、Bot Framework サービスとやり取りして、メッセージとイベントを送受信します。 Bot Framework Service は、Azure Bot Service および Bot Framework のコンポーネントの 1 つです。 任意の数の環境と言語でボットを作成できます。 [Azure portal](/azure/bot-service/bot-service-quickstart?view=azure-bot-service-4.0) 内でボット開発を開始することも、[C#、JavaScript、または Python](/azure/bot-service/dotnet/bot-builder-dotnet-sdk-quickstart?view=azure-bot-service-4.0) のテンプレートを使用してローカルで開発することもできます。 また、SDK を通じて利用できる機能の多くを紹介しているさまざまな[サンプル](https://github.com/microsoft/botbuilder-samples)にアクセスすることもできます。 これらのサンプルは、始めから豊富な機能を求めている開発者に最適です。

Azure Bot Service および Bot Framework の一環として、ボットの機能拡張に使用できる追加コンポーネントが用意されています。

| 特徴量 | 説明 | Link |
| --- | --- | --- |
| 自然言語処理を追加する | ボットによる自然言語の解釈、スペル ミスの理解、音声の使用、ユーザーの意図の認識を可能にします。 | [LUIS](/azure/bot-service/bot-builder-howto-v4-luis?view=azure-bot-service-4.0) の使用方法 |
| 質問に答える | ナレッジ ベースを追加して、より自然な会話方法でユーザーの質問に答えます。 | [QnA Maker](/azure/bot-service/bot-builder-howto-qna?view=azure-bot-service-4.0) の使用方法 |
| 複数のモデルを管理する | LUIS や QnA Maker などの複数のモデルを使用している場合、ボットの会話中に、いつ、どのモデルを使用するかをインテリジェントに判断します。 | [ディスパッチ](/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0) ツール |
| カードとボタンを追加する | グラフィックス、メニュー、カードなど、テキスト以外のメディアでユーザー エクスペリエンスを強化します。 | [カードを追加する](/azure/bot-service/bot-builder-howto-add-media-attachments?view=azure-bot-service-4.0)方法 |

> [!NOTE]
> この表は、包括的なリストではありません。 詳細については、「[Azure Bot Service のドキュメント](/azure/bot-service/)」を参照してください。

### <a name="test"></a>テスト

ボットは、連携する多くの異なる部分を持つ複雑なアプリケーションです。 他の複雑なアプリケーションの場合と同様、この複雑さのために変わったバグが発生したり、期待どおりにボットが動作しなかったりすることがあります。 ボットを発行する前に、テストしてください。 ボットを使用できるようにリリースする前に、ボットをテストする方法をいくつか用意しています。

- [エミュレーター](/azure/bot-service/bot-service-debug-emulator?view=azure-bot-service-4.0)を使ってボットをローカルでテストする。 Bot Framework Emulator は、チャット インターフェイスだけでなく、ボットの動作方法とその動作の理由を理解するのに役立つデバッグ ツールと質問ツールも提供するスタンドアロン アプリケーションです。 このエミュレーターは、開発中のボット アプリケーションと共にローカルで実行できます。
- [Web](/azure/bot-service/bot-service-manage-test-webchat?view=azure-bot-service-4.0) 上でボットをテストする。 ボットは、Azure portal から構成した後、Web チャット インターフェイスを介してアクセスすることもできます。 Web チャット インターフェイスは、実行されているコードに直接アクセスできないテスターなどに、ボットへのアクセスを許可する優れた方法です。
- Bot Framework SDK の 7 月の更新を使用して[ボットの単体テスト](/azure/bot-service/unit-test-bots)を実行する。

### <a name="publish"></a>発行

ボットを Web 上で使用する準備が整ったら、[それを Azure に発行](/azure/bot-service/bot-builder-howto-deploy-azure?view=azure-bot-service-4.0)するか、独自の Web サービスまたはデータセンターに発行します。 パブリック インターネット上のアドレスを取得することが、ボットをサイト上やチャット チャネル内で稼働させる第一歩です。

### <a name="connect"></a>接続する

ボットを、Facebook、Messenger、Kik、Skype、Slack、Microsoft Teams、Telegram、SMS 送信、Twilio、Cortana、Skype などのチャネルに接続できます。 Bot Framework は、こうしたさまざまなプラットフォームすべてとの間でメッセージを送受信するために必要な作業のほとんどを行います。 ボット アプリケーションは、接続されているチャネルの数や種類に関係なく、統一され、正規化されたメッセージのストリームを受信します。 チャネルを追加する方法の詳細については、[チャネル](/azure/bot-service/bot-service-manage-channels?view=azure-bot-service-4.0)に関するページを参照してください。

### <a name="evaluate"></a>Evaluate

Azure portal で収集されたデータを使用し、ボットの機能とパフォーマンスを強化する機会を特定します。 トラフィック、待ち時間、統合などのサービス レベルのデータやインストルメンテーション データを取得できます。 Analytics によって、ユーザー、メッセージ、チャネル データに関する会話レベルのレポートも提供されます。 詳しくは、[分析情報の収集方法](/azure/bot-service/bot-service-manage-analytics?view=azure-bot-service-4.0)に関するページをご覧ください。

### <a name="patterns-for-common-use-cases"></a>一般的なユースケースのパターン

会話型 AI アプリケーションの実装に使用される一般的なパターンは次のとおりです。

- **サポート技術情報:** 事実上あらゆる主題に関する情報を提供するナレッジ ボットを設計できます。 たとえば、1 つのナレッジ ボットでイベントに関する質問に回答できます。たとえば、"このカンファレンスではどんなボット イベントがありますか?" または "次のレゲエのショーはいつ?" などです。 別のボットで、"オペレーティング システムを更新するにはどうすればよいか?" といった IT 関連の質問にも回答できます。 また別のボットでは、"john doe とは誰ですか?" または "jane doe のメール アドレスは?" のような連絡先についての質問にも答えます。

   ナレッジ ボットの設計要素の詳細については、「[ナレッジ ボットを設計する](/azure/bot-service/bot-service-design-pattern-knowledge-base?view=azure-bot-service-4.0)」を参照してください。

- **人間に引き継ぐ:** ボットがどれくらいの AI を持っているかにかかわらず、会話を人間に引き継ぐ必要があることがあります。 そのような場合、ボットは、いつ引き継ぎが必要であるかを認識し、ユーザーにスムーズな移行を提供する必要があります。

   引き継ぐパターンの詳細については、「[会話をボットから人間に移行する](/azure/bot-service/bot-service-design-pattern-handoff-human?view=azure-bot-service-4.0)」を参照してください。

- **ボットをアプリケーションに埋め込む:** 多くの場合、ボットはアプリケーションの外部に存在しますが、アプリケーションに統合することもできます。 たとえば、ユーザーが情報を検索するのに役立つように[ナレッジ ボット](/azure/bot-service/bot-service-design-pattern-knowledge-base?view=azure-bot-service-4.0)をアプリケーション内に埋め込むことができます。 また、受信ユーザー要求に対する最初のレスポンダーとして機能するように、ヘルプ デスク アプリケーションにボットを埋め込むこともできます。 簡単な問題はボットが自力で解決し、複雑な問題は人のエージェントに[ハンドオフ](/azure/bot-service/bot-service-design-pattern-handoff-human?view=azure-bot-service-4.0)されます。

   アプリケーション内にボットを統合する方法については、「[ボットをアプリに埋め込む](/azure/bot-service/bot-service-design-pattern-embed-app?view=azure-bot-service-4.0)」を参照してください。

- **ボットを Web サイトに埋め込む:** ボットをアプリケーション内に埋め込むのと同様に、ボットを Web サイト内に埋め込んで、複数のチャネル間で複数の通信モードを有効にすることもできます。

   Web サイト内にボットを統合する方法については、「[ボットを Web サイトに埋め込む](/azure/bot-service/bot-service-design-pattern-embed-web-site?view=azure-bot-service-4.0)」を参照してください。

## <a name="next-steps"></a>次の手順

- [Azure Bot Service](https://azure.microsoft.com/resources/whitepapers/search/?service=bot-service) に関する機械学習のホワイトペーパーと e-Book を確認します。
- [AI + 機械学習アーキテクチャ](/azure/architecture/browse/)について確認します。
- [コグニティブ API を使用してインテリジェントなアプリを構築する](https://azure.microsoft.com/resources/building-intelligent-apps-with-cognitive-apis/) (e-Book)。
- [FAQ チャットボットのアーキテクチャ](https://azure.microsoft.com/resources/faq-chatbot-architecture/)。
