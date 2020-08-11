---
title: AI アプリケーションとは
description: コグニティブ検索とは
author: v-hanki
ms.author: janet
ms.date: 07/14/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: innovate
ms.openlocfilehash: 7d35679a95dd91cd563fd6fe4be45519895e0fd3
ms.sourcegitcommit: 26aee3c6f596bb8a9f1e16af93cdf94e41a61dee
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2020
ms.locfileid: "87400464"
---
<!-- cSpell:ignore Personalizer -->

# <a name="what-are-ai-applications"></a>AI アプリケーションとは

Azure では、既に組み込まれている AI と任意のツールとテクノロジを使用して、インテリジェントなアプリケーションを短時間でビルドできます。

- **簡単にビルドして任意の場所にデプロイする:** チームの既存のスキル セットと愛用のツールを使用して、インテリジェントなアプリケーションをビルドし、コードの変更なしでデプロイできます。 1 回のビルドで、任意の場所 (クラウド、オンプレミス、およびエッジ デバイス) にデプロイできます。他のプロバイダーよりも多くのデータセンターへのグローバル分散を確実に行うことができます。

- **オープン プラットフォームを使用して影響をもたらす:** オープン ソースを含む、お気に入りのテクノロジを選択できます。 Azure では、多様なデプロイ オプション、一般的なスタックと言語、および広範囲にわたるデータ エンジンがサポートされています。 この柔軟性に加え、Microsoft のテクノロジによって提供されるパフォーマンス、スケール、およびセキュリティを活用して、あらゆるシナリオに対応するアプリケーションをビルドできます。

- **組み込みのインテリジェンスを使用してアプリケーションを開発する:** Azure を使用したインテリジェントなアプリケーションのビルドは簡単です。理由は、他のプラットフォームでは、データの場所や使用言語を問わずに分析とネイティブ AI を利用することはできないからです。 豊富な[コグニティブ API](https://azure.microsoft.com/services/cognitive-services/) を利用して、人間に近いインテリジェンスの新しいエクスペリエンスをアプリケーションに簡単に組み込むことができます。

## <a name="what-are-azure-cognitive-services"></a>Azure Cognitive Services とは

Azure Cognitive Services を使用すると、アプリケーションに AI を導入でき、数行のシンプルなコードを使用して AI の最新のブレークスルーを利用できます。 それらによって、見たり、聞いたり、話したり、理解したり、ビジネス プロセスで推論を開始したりするアプリケーションを作成する能力がもたらされます。 Azure Cognitive Services では、使いやすくアプリケーションに簡単に組み込むことができる形で AI インテリジェンスが提供されます。

Azure Cognitive Services は、開発者が直接的な AI またはデータ サイエンスのスキルや知識がなくてもインテリジェントなアプリケーションを構築するために使用できる API、SDK、およびサービスです。 Azure Cognitive Services によって、開発者は簡単にアプリケーションに Cognitive 機能を追加できます。 Azure Cognitive Services の目標は、開発者が、聞いたり、話したり、理解したり、推論し始めたりできるアプリケーションの作成を支援することです。 Azure Cognitive Services 内のサービス カタログは、5 つの主要な柱 (視覚、音声、言語、Web 検索、および意思決定) に分類できます。

### <a name="vision-apis"></a>Vision API

| [サービス名] | サービスの説明 |
| --- | --- |
| [Computer Vision](https://docs.microsoft.com/azure/cognitive-services/computer-vision/) | Computer Vision サービスを使用すると、イメージを処理して情報を返すための高度なアルゴリズムにアクセスできます。 |
| [Custom Vision](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/home) | Custom Vision サービスを使用すると、カスタム画像分類器を構築できます。 |
| [Face](https://docs.microsoft.com/azure/cognitive-services/face/) | Face サービスは、顔属性の検出と認識を有効にする、高度な顔アルゴリズムへのアクセスを提供します。 |
| [Form Recognizer](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/) (プレビュー) | Form Recognizer では、キーと値のペアおよびテーブル データを識別して form ドキュメントから抽出し、その後、元のファイルにあるリレーションシップを含む構造化データを出力します。 |
| [Ink Recognizer](https://docs.microsoft.com/azure/cognitive-services/ink-recognizer/) (プレビュー) | Ink Recognizer では、デジタル インク ストローク データ、シェイプ、および手書きのコンテンツを認識して分析し、認識されたすべてのエンティティを備えたドキュメント構造を出力します。 |
| [Video Indexer](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview) | Video Indexer では、ビデオから分析情報を抽出できます。 |

### <a name="speech-apis"></a>Speech API

| [サービス名] | サービスの説明 |
| --- | --- |
| [Speech](https://docs.microsoft.com/azure/cognitive-services/speech-service/) | Speech サービスは、音声対応の機能をアプリケーションに追加します。 |
| [Speaker Recognition](https://docs.microsoft.com/azure/cognitive-services/speaker-recognition/home "Speaker Recognition API") (プレビュー) | Speaker Recognition API では、話者識別と検証のアルゴリズムが提供されます。 |
| [Bing Speech](https://docs.microsoft.com/azure/cognitive-services/speech/home) (廃止) | Bing Speech API は、アプリケーションに音声対応の機能を作成する簡単な方法を提供します。 |
| [Translator Speech](https://docs.microsoft.com/azure/cognitive-services/translator-speech/) (廃止) | Translator Speech は、機械翻訳サービスです。 |

### <a name="language-apis"></a>Language API

| [サービス名] | サービスの説明 |
| --- | -- |
| [Language Understanding (LUIS)](https://docs.microsoft.com/azure/cognitive-services/luis/) | Language Understanding サービス (LUIS) を使用すると、アプリケーションで人の発話の意図を認識できるようになります。 |
| [QnA Maker](https://docs.microsoft.com/azure/cognitive-services/qnamaker/index "QnA Maker") | QnA Maker を使用すると、半構造化コンテンツから質疑応答サービスを構築できます。 |
| [Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/) | Text Analytics は、未加工のテキストに、センチメント分析、キー フレーズ抽出、および言語検出のための自然言語処理を提供します。 |
| [Translator](https://docs.microsoft.com/azure/cognitive-services/translator/) | Translator は、ほぼリアルタイムでのマシン ベースのテキスト翻訳を提供します。 |

### <a name="decision-apis"></a>Decision API

| [サービス名] | サービスの説明 |
| --- | --- |
| [Anomaly Detector](https://docs.microsoft.com/azure/cognitive-services/anomaly-detector/) (プレビュー) | Anomaly Detector では、時系列データを監視し、その中の異常を検出できます。 |
| [Content Moderator](https://docs.microsoft.com/azure/cognitive-services/content-moderator/overview "Content Moderator") | Content Moderator は、不快感を与えたり、望ましくなかったり、危険であったりする可能性があるコンテンツを監視します。 |
| [Personalizer](https://docs.microsoft.com/azure/cognitive-services/personalizer/) | Personalizer は、ユーザーのリアルタイムの動作から学習し、ユーザーに表示する最良のエクスペリエンスを選択できるようにします。 |

### <a name="supported-cultural-languages"></a>サポートされる言語 (カルチャ)

Cognitive Services では、さまざまなカルチャ言語がサービス レベルでサポートされています。 各 API で利用可能な言語は、[サポート対象の言語一覧](https://docs.microsoft.com/azure/cognitive-services/language-support)に関するページで検索できます。

### <a name="securing-resources"></a>リソースのセキュリティ

Azure Cognitive Services では、Azure Active Directory の資格情報を使用した[認証](https://docs.microsoft.com/azure/cognitive-services/authentication)、有効なリソース キー、および [Azure Virtual Networks](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-virtual-networks) を含む多層型のセキュリティ モデルが採用されています。

### <a name="container-support"></a>コンテナー サポート

Cognitive Services には、Azure クラウドまたはオンプレミスへのデプロイ用にコンテナーが用意されています。 詳細については、[Cognitive Services のコンテナー](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-container-support)に関するページを参照してください。

<!-- docsTest:ignore "HIPAA BAA" "CSA STAR" -->

### <a name="certifications-and-compliance"></a>認定資格とコンプライアンス

Cognitive Services には、CSA STAR Certification、FedRAMP Moderate、HIPAA BAA などの認定資格が与えられています。

自分で監査したり、セキュリティを確認する目的で認定資格を[ダウンロード](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)できます。

プライバシーやデータ管理を理解するには、[Microsoft セキュリティ センター](https://servicetrust.microsoft.com/)を参照してください。

## <a name="how-are-cognitive-services-and-azure-machine-learning-similar"></a>Cognitive Services と Azure Machine Learning の類似点

実現方法はそれぞれのオファリングで異なりますが、どちらも AI を業務の強化に応用するという最終目標があります。 一般に、対象ユーザーは異なります。

- Cognitive Services は機械学習の経験がない開発者を対象としています。
- Azure Machine Learning はデータ サイエンティスト向けに特化されています。

## <a name="how-is-a-cognitive-service-different-from-machine-learning"></a>コグニティブ サービスと機械学習の違い

コグニティブ サービスでは、ユーザーに対してトレーニング済みのモデルが提供されます。 これはデータとアルゴリズムを統合したもので、REST API や SDK から利用できます。 シナリオによっては、このサービスを数分で実装できます。 コグニティブ サービスでは、テキスト内のキー フレーズや画像内の項目識別といった一般的な問題が解決されます。

機械学習は、通常、適切に実装するために長時間を要するプロセスです。 この時間は、コグニティブ サービスと同等の機能を実現するためのデータ収集、クリーニング、変換、アルゴリズムの選択、モデルのトレーニング、およびデプロイのために費やされます。 機械学習では、きわめて特殊な問題や具体的な問題を解決することが可能です。 機械学習の問題では、専門知識だけでなく、検討対象の問題に固有のテーマやデータについての理解が必要です。

## <a name="next-steps"></a>次のステップ

- [Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/) の詳細を確認する
- [AI アーキテクチャのベスト プラクティス](https://docs.microsoft.com/azure/architecture/solution-ideas/articles/ai-at-the-edge)を見つける
