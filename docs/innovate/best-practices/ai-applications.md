---
title: AI アプリケーションとは
description: AI アプリケーションとは Azure Cognitive Services を使用して AI のアプリケーションと機能をアプリに統合する方法について説明します。
author: v-hanki
ms.author: janet
ms.date: 01/26/2021
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: innovate
ms.custom: think-tank, seo-caf-innovate
keywords: AI アプリケーション, AI アプリケーションとは何か, Speech Recognition API, Computer Vision API, Decision Logic API
ms.openlocfilehash: 39d664452d14b13514f7e1b0ff78c0cf66e59468
ms.sourcegitcommit: a0b808d5602e09c6cd7f3485fd28bb592692ea8d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/15/2021
ms.locfileid: "100526019"
---
# <a name="what-are-ai-applications"></a>AI アプリケーションとは

AI アプリケーションとは、Speech Recognition API、Computer Vision API、Decision Logic API、および人間の理性を模倣するその他の種類のインテリジェント システムなどのことをいいます。 AI アプリケーションは、より自然な方法でエンド ユーザーとやり取りすることができ、より優れたユーザー エクスペリエンスに貢献できるため、現在の市場における多くのソフトウェア製品に不可欠な機能となっています。 Azure を使用すると、AI アプリケーションをどこへでもロールアウトできるため、お客様やお客様のチームが時間を節約するのに役立ちます。

Azure では、任意のツールおよびテクノロジと組み込みの AI を使用して、インテリジェントなアプリケーションをより短時間でビルドできます。

- **簡単にビルドして任意の場所にデプロイする:** チームの既存のスキル セットと使い慣れたツールを使用して、インテリジェントなアプリケーションをビルドし、コードの変更なしでデプロイできます。 1 回のビルドで、クラウド、オンプレミス、エッジ デバイスにデプロイすることができます。 他のプロバイダーを利用するよりも多くのデータセンターへのグローバルな分散を確実に行うことができます。
- **オープン プラットフォームを使用して影響をもたらす:** お気に入りのテクノロジを選択します。オープンソースでもかまいません。 Azure では、多様なデプロイ オプション、一般的なスタックと言語、および広範囲にわたるデータ エンジンがサポートされています。 この柔軟性に加え、Microsoft のテクノロジによって提供されるパフォーマンス、スケール、およびセキュリティを活用して、あらゆるシナリオに対応するアプリケーションをビルドできます。
- **組み込みのインテリジェンスを使用してアプリケーションを開発する:** Azure を使用したインテリジェントなアプリケーションのビルドは簡単です。理由は、他のプラットフォームでは、データの場所や使用言語を問わずに分析とネイティブ AI を利用することはできないからです。 豊富な[コグニティブ API](https://azure.microsoft.com/services/cognitive-services/) を利用して、人間に近いインテリジェンスの新しいエクスペリエンスをアプリケーションに簡単に組み込むことができます。

## <a name="what-is-azure-cognitive-services"></a>Azure Cognitive Services とは

Azure Cognitive Services を使用すると、数行のシンプルなコード行によって、AI の機能とブレークスルーをアプリケーションに統合する方法を簡素化できます。 それにより、見たり、聞いたり、話したり、理解したり、ビジネス プロセス間で推論を開始したりするアプリケーションの作成が支援されます。 Cognitive Services によって、使いやすくアプリケーションに簡単に組み込むことができる形で AI インテリジェンスが提供されます。

Cognitive Services は、開発者が直接的な AI またはデータ サイエンスのスキルや知識がなくてもインテリジェントなアプリケーションを構築するために使用できる API、SDK、サービスで構成されています。 Cognitive Services によって、開発者は簡単にアプリケーションにコグニティブ機能を追加できます。 Cognitive Services に含まれるサービスのカタログは、視覚、音声、言語、Web 検索、意思決定という 5 つの主要な部分に分類できます。

### <a name="computer-vision-apis"></a>Computer Vision API

| [サービス名] | サービスの説明 |
| --- | --- |
| [Computer Vision](/azure/cognitive-services/computer-vision/) | Computer Vision API を使用すると、画像を処理して情報を返すための高度なアルゴリズムにアクセスできます。 |
| [Custom Vision](/azure/cognitive-services/custom-vision-service/home) | Custom Vision を使用すると、カスタム画像分類器を構築できます。 |
| [Face](/azure/cognitive-services/face/) | Face サービスを使用すると、顔属性を検出して認識する高度な顔アルゴリズムにアクセスできます。 |
| [Form Recognizer](/azure/cognitive-services/form-recognizer/) (プレビュー) | Form Recognizer を使用すると、フォーム ドキュメントからキーと値のペアおよびテーブル データを識別して抽出できます。 その後、元のファイル内の関係を含む構造化データが出力されます。 |
| [Ink Recognizer](/azure/cognitive-services/ink-recognizer/) (プレビュー) | Ink Recognizer を使用すると、デジタル インク ストローク データ、シェイプ、および手書きのコンテンツを認識して分析し、認識されたすべてのエンティティを備えたドキュメント構造を出力できます。 |
| [Video Indexer](/azure/cognitive-services/video-indexer/video-indexer-overview) | Video Indexer では、ビデオから分析情報を抽出できます。 |

### <a name="speech-recognition-apis"></a>Speech Recognition API

| [サービス名] | サービスの説明 |
| --- | --- |
| [Speech](/azure/cognitive-services/speech-service/) | Speech サービスは、音声対応の機能をアプリケーションに追加します。 |
| [Speaker Recognition](/azure/cognitive-services/speaker-recognition/home "Speaker Recognition API") (プレビュー) | Speaker Recognition API では、話者識別と検証のアルゴリズムが提供されます。 |
| [Bing Speech](/azure/cognitive-services/speech/home) (廃止) | Bing Speech API は、アプリケーションに音声対応の機能を作成する簡単な方法を提供します。 |
| [Translator Speech](/azure/cognitive-services/translator-speech/) (廃止) | Translator Speech は、機械翻訳サービスです。 |

### <a name="language-apis"></a>Language API

| [サービス名] | サービスの説明 |
|--|--|
| [Language Understanding (LUIS)](/azure/cognitive-services/luis/) | Language Understanding サービス (LUIS) を使用すると、アプリケーションで人の発話の意図を認識できるようになります。 |
| [QnA Maker](/azure/cognitive-services/qnamaker/index "QnA Maker") | QnA Maker を使用すると、半構造化コンテンツから質疑応答サービスを構築できます。 |
| [Text Analytics](/azure/cognitive-services/text-analytics/) | Text Analytics によって、未加工のテキストに対して、感情分析、キー フレーズ抽出、および言語検出のための自然言語処理が提供されます。 |
| [Translator](/azure/cognitive-services/translator/) | Translator によって、ほぼリアルタイムでのマシン ベースのテキスト翻訳が提供されます。 |

### <a name="decision-logic-apis"></a>Decision Logic API

| [サービス名] | サービスの説明 |
| --- | --- |
| [Anomaly Detector](/azure/cognitive-services/anomaly-detector/) (プレビュー) | Anomaly Detector では、時系列データを監視し、その中の異常を検出できます。 |
| [Content Moderator](/azure/cognitive-services/content-moderator/overview "Content Moderator") | Content Moderator は、不快感を与えたり、望ましくなかったり、危険であったりする可能性があるコンテンツを監視します。 |
| [Personalizer](/azure/cognitive-services/personalizer/) | Personalizer を使用すると、ユーザー向けの最もカスタマイズされたエクスペリエンスを選択するために、ユーザーのリアルタイムの行動から学習することができます。 |

### <a name="supported-cultural-languages"></a>サポートされる言語 (カルチャ)

Cognitive Services では、さまざまなカルチャ言語がサービス レベルでサポートされています。 各 API で利用可能な言語は、[サポート対象の言語一覧](/azure/cognitive-services/language-support)に関するページで検索できます。

### <a name="secure-resources"></a>セキュリティで保護されたリソース

Cognitive Services によって、Azure Active Directory の資格情報を使用した[認証](/azure/cognitive-services/authentication)、有効なリソース キー、[Azure Virtual Network](/azure/cognitive-services/cognitive-services-virtual-networks) を含む多層型のセキュリティ モデルが提供されています。

### <a name="container-support"></a>コンテナー サポート

Cognitive Services には、クラウドまたはオンプレミスへのデプロイ用にコンテナーが用意されています。 詳細については、[Cognitive Services のコンテナー](/azure/cognitive-services/cognitive-services-container-support)に関するページを参照してください。

<!-- docutune:casing "HIPAA BAA" "CSA STAR" -->

### <a name="certifications-and-compliance"></a>認定資格とコンプライアンス

Cognitive Services には、CSA STAR Certification、FedRAMP Moderate、HIPAA BAA などの認定資格が与えられています。

自分で監査したり、セキュリティを確認する目的で認定資格を[ダウンロード](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)できます。

プライバシーやデータ管理を理解するには、[Microsoft セキュリティ センター](https://servicetrust.microsoft.com/)を参照してください。

## <a name="how-are-cognitive-services-and-azure-machine-learning-similar"></a>Cognitive Services と Azure Machine Learning の類似点

Cognitive Services と Azure Machine Learning には、AI を適用してビジネスの運営を強化するという共通の目標があります。 それぞれのオファリングでこの機能がどのように提供されているかについては、違いがあります。 一般に、対象ユーザーは異なります。

- Cognitive Services は機械学習の経験がない開発者を対象としています。
- Machine Learning はデータ サイエンティスト向けに調整されています。

## <a name="how-is-a-cognitive-service-different-from-machine-learning"></a>コグニティブ サービスと機械学習の違い

コグニティブ サービスでは、ユーザーに対してトレーニング済みのモデルが提供されます。 このモデルはデータとアルゴリズムを統合したもので、REST API や SDK から利用できます。 シナリオによっては、このサービスを数分で実装できます。 コグニティブ サービスでは、テキスト内のキー フレーズや画像内の項目識別といった一般的な問題が解決されます。

機械学習は、通常、適切に実装するために長時間を要するプロセスです。 この時間は、コグニティブ サービスと同等の機能を実現するためのデータ収集、クリーニング、変換、アルゴリズムの選択、モデルのトレーニング、およびデプロイのために費やされます。 機械学習では、きわめて特殊な問題や具体的な問題を解決することが可能です。 機械学習の問題に対しては、検討する対象の問題に固有の主題とデータについての理解、および専門知識が必要とされます。

## <a name="next-steps"></a>次の手順

- [Cognitive Services](/azure/cognitive-services/) の詳細について確認します。
- [AI アーキテクチャのベスト プラクティス](/azure/architecture/solution-ideas/articles/ai-at-the-edge)を見つけます。
