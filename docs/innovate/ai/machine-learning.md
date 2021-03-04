---
title: 機械学習
description: このチェックリストとリソースは、アプリケーションの開発とデプロイを計画するのに役立ちます。
author: v-hanki
ms.author: janet
ms.date: 07/14/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: innovate
ms.custom: think-tank
ms.openlocfilehash: acbb9fd8744b720848ad5043cbd71080dba4c8fb
ms.sourcegitcommit: b8f8b7631aabaab28e9705934bf67dad15e3a179
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101791090"
---
<!-- cSpell:ignore scikit RLlib ONNX Jupyter -->

# <a name="machine-learning"></a>機械学習

Azure には、高度な機械学習機能が用意されています。 Azure Machine Learning を使用すると、独自の機械学習モデルの構築、トレーニング、デプロイを迅速かつ簡単に行えます。 Machine Learning は、従来のものからディープ ラーニング、教師あり学習と教師なし学習まで、あらゆる種類の機械学習に使用できます。 Python または R のコードを記述するか、または[デザイナー](/azure/machine-learning/tutorial-designer-automobile-price-train-score)などのコード不要またはローコード オプションを使用するかにかかわらず、Machine Learning ワークスペースで非常に正確な機械学習およびディープ ラーニング モデルの構築、トレーニング、追跡を行えます。

ローカル コンピューターでトレーニングを開始し、その後、クラウドにスケールアウトすることもできます。 また、このサービスは、PyTorch、TensorFlow、scikit-learn、Ray および RLlib など、ディープ ラーニングや強化のための一般的なオープンソース ツールと連携します。

[Machine Learning](/azure/machine-learning/) を開始します。 [機械学習の実験の開始](/azure/machine-learning/tutorial-1st-experiment-sdk-setup)方法についてのチュートリアルも用意されています。 機械学習のオープンソース モデル形式とランタイムの詳細については、[ONNX Runtime](https://www.onnxruntime.ai/) を参照してください。

機械学習ソリューションを使用する一般的なシナリオを以下に示します。

- 予測的なメンテナンス
- 在庫管理
- 不正行為の検出
- 需要予測
- インテリジェント レコメンデーション
- 売上予測

## <a name="checklist"></a>チェック リスト

- **まず、Machine Learning についての理解を深め** てから、最初に作成するエクスペリエンスを選択します。 Python を使用した Jupyter Notebook、ビジュアル ドラッグ アンド ドロップ エクスペリエンス、自動機械学習 (AutoML) を使用する手順に従うことができます。

  - [Machine Learning の概要](/azure/machine-learning/overview-what-is-azure-ml)
  - [Python で Jupyter Notebook を使用して初めての機械学習の実験を作成する](/azure/machine-learning/tutorial-1st-experiment-sdk-setup)
  - [ビジュアル ドラッグアンドドロップの実験](/azure/machine-learning/tutorial-designer-automobile-price-train-score)
  - [AutoML UI を使用する](/azure/machine-learning/tutorial-first-experiment-automated-ml)
  - [開発環境を構成する](/azure/machine-learning/how-to-configure-environment)

- **より高度なチュートリアル** で、タクシー料金の予測、イメージの分類、バッチ スコアリング用パイプラインの構築を実験します。

  - [AutoML を使用してタクシー料金を予測する](/azure/machine-learning/tutorial-auto-train-models)
  - [scikit-learn を使用してイメージを分類する](/azure/machine-learning/tutorial-train-models-with-aml)
  - [バッチ スコアリング用の Machine Learning パイプラインを構築する](/azure/machine-learning/tutorial-pipeline-batch-scoring-classification)

- **ビデオ チュートリアルに従って**、コード不要のモデル構築、機械学習の運用 (MLOps)、ONNX Runtime、モデルの解釈可能性と透過性など、Machine Learning の利点の詳細を学びます。

  - [Machine Learning の新機能](https://channel9.msdn.com/Shows/AI-Show/Allup-Azure-ML)
  - [AutoML を使用してモデルを構築する](https://aka.ms/automlvideo)
  - [Machine Learning デザイナーを使用してコード不要のモデルを構築する](https://aka.ms/studioanddesigner)
  - [エンドツーエンド ライフサイクルを管理するための MLOps](https://aka.ms/mlopsvideo)
  - [ONNX Runtime をモデルに組み込む](https://www.youtube.com/watch?v=qy7X2JGLUC4)
  - [モデルの解釈可能性と透過性](https://aka.ms/azuremlinterpret)
  - [R を使用したモデル構築](https://aka.ms/Rmodels)

- [AI ソリューションの参照用アーキテクチャを確認する](/azure/architecture/browse/#ai--machine-learning)

## <a name="next-steps"></a>次のステップ

その他の AI ソリューション カテゴリを調べる:

- [AI アプリケーションとエージェント](./ai-applications.md)
- [ナレッジ マイニング](./knowledge-mining.md)
