---
title: Machine Learning
description: このチェックリストとリソースは、アプリケーションの開発とデプロイを計画するのに役立ちます。
author: v-hanki
ms.author: janet
ms.date: 07/14/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: innovate
ms.openlocfilehash: 3cbc697e979f741342c3a75f38de8f4086cb4871
ms.sourcegitcommit: 9163a60a28ffce78ceb5dc8dc4fa1b83d7f56e6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2020
ms.locfileid: "86452698"
---
<!-- cSpell:ignore scikit RLlib ONNX Jupyter -->

# <a name="machine-learning"></a>Machine Learning

Azure には、高度な機械学習機能が用意されています。 Azure Machine Learning を使用することにより、独自の機械学習モデルの構築、トレーニング、デプロイを迅速かつ簡単に行えます。 Azure Machine Learning は、従来の機械学習からディープ ラーニング、教師あり学習、教師なし学習まで、あらゆる種類の機械学習に使用できます。 Python または R のコードを記述するか、または[デザイナー](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-train-score)などのコード不要またはローコード オプションを使用するかにかかわらず、Azure Machine Learning ワークスペースで非常に正確な機械学習およびディープ ラーニング モデルの構築、トレーニング、追跡を行えます。

ローカル コンピューターでトレーニングを開始し、その後、クラウドにスケールアウトすることもできます。 また、このサービスは、PyTorch、TensorFlow、scikit-learn、Ray および RLlib など、ディープ ラーニングや強化のための一般的なオープンソース ツールと連携します。

[Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/) の利用を開始してみましょう。 [機械学習の実験を初めて行う](https://docs.microsoft.com/azure/machine-learning/tutorial-1st-experiment-sdk-setup)ためのチュートリアルも用意されています。 機械学習のオープンソース モデル形式とランタイムの詳細については、[ONNX Runtime](http://onnxruntime.ai) を参照してください。

機械学習ソリューションを使用する一般的なシナリオを以下に示します。

- 予測的なメンテナンス
- 在庫管理
- 不正行為の検出
- 需要予測
- インテリジェント レコメンデーション
- 売上予測

## <a name="checklist"></a>チェック リスト

- **まず、Azure Machine Learning についての理解を深め**てから、最初に作成するエクスペリエンスを選択します。 Python を使用した Jupyter Notebook、ビジュアル ドラッグ アンド ドロップ エクスペリエンス、自動機械学習 (AutoML) を使用する手順に従うことができます。

  - [Azure Machine Learning の概要](https://docs.microsoft.com/azure/machine-learning/overview-what-is-azure-ml)
  - [Python で Jupyter Notebook を使用して初めての機械学習の実験を作成する](https://docs.microsoft.com/azure/machine-learning/tutorial-1st-experiment-sdk-setup)
  - [ビジュアル ドラッグアンドドロップの実験](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-train-score)
  - [AutoML UI を使用する](https://docs.microsoft.com/azure/machine-learning/tutorial-first-experiment-automated-ml)
  - [開発環境を構成する](https://docs.microsoft.com/azure/machine-learning/how-to-configure-environment)

- **より高度なチュートリアル**で、タクシー料金の予測、イメージの分類、バッチ スコアリング用パイプラインの構築を実験します。

  - [AutoML を使用してタクシー料金を予測する](https://docs.microsoft.com/azure/machine-learning/tutorial-auto-train-models)
  - [scikit-learn を使用してイメージを分類する](https://docs.microsoft.com/azure/machine-learning/tutorial-train-models-with-aml)
  - [バッチ スコアリング用の Azure Machine Learning パイプラインを構築する](https://docs.microsoft.com/azure/machine-learning/tutorial-pipeline-batch-scoring-classification)

- **ビデオ チュートリアルに従う**ことにより、コード不要のモデル構築、MLOps、ONNX Runtime、モデルの解釈可能性と透過性など、Azure Machine Learning のさまざまな利点について詳しく知ることができます。

  - [Azure Machine Learning の新機能](https://channel9.msdn.com/Shows/AI-Show/Allup-Azure-ML)
  - [AutoML を使用してモデルを構築する](https://aka.ms/automlvideo)
  - [Azure Machine Learning デザイナーを使用してコード不要のモデルを構築する](https://aka.ms/studioanddesigner)
  - [エンドツーエンド ライフサイクルを管理するための MLOps](https://aka.ms/mlopsvideo)
  - [ONNX Runtime をモデルに組み込む](https://www.youtube.com/watch?v=qy7X2JGLUC4)
  - [モデルの解釈可能性と透過性](https://aka.ms/azuremlinterpret)
  - [R を使用したモデル構築](https://aka.ms/Rmodels)

- [AI ソリューションの参照用アーキテクチャを確認する](https://docs.microsoft.com/azure/architecture/browse/#ai--machine-learning)

## <a name="next-steps"></a>次のステップ

その他の AI ソリューション カテゴリを調べる:

- [AI アプリケーションとエージェント](./ai-applications.md)
- [ナレッジ マイニング](./knowledge-mining.md)
