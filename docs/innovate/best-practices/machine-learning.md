---
title: 機械学習とは
description: 機械学習とは
author: v-hanki
ms.author: janet
ms.date: 07/14/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: innovate
ms.openlocfilehash: 5f6ba299ad4eddd9a299aac151ad85ed31ac7a0f
ms.sourcegitcommit: 011525720bd9e2d9bcf03a76f371c4fc68092c45
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88568381"
---
<!-- cSpell:ignore scikit RLlib Jupyter MLflow Kubeflow -->

# <a name="what-is-machine-learning"></a>機械学習とは

機械学習は、コンピューターで既存のデータを使って、将来の動き、結果、傾向を予測できるデータ サイエンスの手法の 1 つです。 機械学習を使用することで、明示的にプログラムすることなく、コンピューターが学習します。

機械学習からの予想や予測によってアプリケーションやデバイスの機能性を高めることができます。 たとえばオンライン ショッピングでは、利用者が欲しいと考えそうな他の商品を、過去の購入履歴に基づいてお勧めするのに機械学習が役立っています。 また、クレジット カードの読み取り時に、機械学習では、トランザクションをトランザクションのデータベースと比較することで不正の検出を支援します。 また、ロボット掃除機が部屋を掃除するとき、機械学習は、作業が行われているかどうかを判断するのを支援します。

## <a name="machine-learning-tools-to-fit-each-task"></a>各タスクに適合する機械学習ツール

Azure Machine Learning には、次のような、開発者やデータ科学者が機械学習ワークフロー用に必要とするすべてのツールが用意されています。

- [Azure Machine Learning デザイナー (プレビュー)](/azure/machine-learning/tutorial-designer-automobile-price-train-score):ドラッグアンドドロップ モジュールを使用して実験を構築し、パイプラインをデプロイします。
- Jupyter ノートブック: [サンプル ノートブック](https://github.com/Azure/MachineLearningNotebooks)を使用するか、SDK for Python のサンプルを使用する独自のノートブックを作成します。
- R スクリプトまたはノートブックでは、[SDK for R](https://azure.github.io/azureml-sdk-for-r/reference/index.html) を使用して独自のコードを書いたり、デザイナーで R モジュールを使用したりします。
- [多数モデル ソリューション アクセラレータ (プレビュー)](https://github.com/microsoft/solution-accelerator-many-models) は Azure Machine Learning 上に構築されており、数百または数千もの機械学習モデルをトレーニング、操作、および管理できます。
- [Visual Studio Code 拡張機能](/azure/machine-learning/tutorial-setup-vscode-extension)。
- [Machine Learning CLI](/azure/machine-learning/reference-azure-machine-learning-cli)。
- PyTorch、TensorFlow、scikit-learn などの、オープンソースのフレームワーク
- Ray RLlib を使用した[強化学習](/azure/machine-learning/how-to-use-reinforcement-learning)。

[MLflow を使用してメトリックを追跡し、モデルをデプロイする](/azure/machine-learning/how-to-use-mlflow)ことや、[Kubeflow](https://www.kubeflow.org/docs/azure/) を使用してエンドツーエンドのワークフロー パイプラインを構築することもできます。

## <a name="build-machine-learning-models-in-python-or-r"></a>Python または R で機械学習モデルを構築する

Azure Machine Learning の [Python SDK](/python/api/overview/azure/ml/?view=azure-ml-py) または [R SDK](https://azure.github.io/azureml-sdk-for-r/reference/index.html) を使用してローカル コンピューターでトレーニングを開始します。 その後、クラウドにスケールアウトすることができます。 クラウドのパワーと、Azure Machine Learning コンピューティングや [Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks) などの利用可能な多数の[コンピューティング先](/azure/machine-learning/how-to-set-up-training-targets)と[高度なハイパーパラメーター調整サービス](/azure/machine-learning/how-to-tune-hyperparameters)を活用することで、高品質のモデルを手早く構築できます。 SDK を使用して、[モデルのトレーニングと調整を自動化](/azure/machine-learning/tutorial-auto-train-models)することもできます。

## <a name="build-machine-learning-models-with-no-code-tools"></a>ノーコード ツールを使用して機械学習モデルを構築する

コード不要またはわずかなコードしか使用しないトレーニングとデプロイを行うには、以下をお試しください。

- Azure Machine Learning デザイナー (プレビュー)

  デザイナーを使用すると、コードを書かなくても、データの準備、機械学習モデルのトレーニング、テスト、デプロイ、管理、追跡を行うことができます。 プログラミングは必要ありません。データセットとモジュールを視覚的に接続してモデルを構築します。 [デザイナーのチュートリアル](/azure/machine-learning/tutorial-designer-automobile-price-train-score)をお試しください。

  詳細については、[Azure Machine Learning デザイナーの概要に関する記事](/azure/machine-learning/concept-designer)を参照してください。
- 自動機械学習 (AutoML) UI

  使いやすいインターフェイスで [AutoML 実験](/azure/machine-learning/tutorial-first-experiment-automated-ml)を作成する方法を確認できます。

## <a name="mlops-deploy-and-lifecycle-management"></a>MLOps:デプロイとライフサイクル管理

Machine Learning 操作 (MLOps) は、ワークフローの効率を向上させる [DevOps](https://azure.microsoft.com/overview/what-is-devops/) の原則と実践に基づいています。 たとえば、継続的インテグレーション、配信、デプロイです。 MLOps では、次のことを目標に、これらの原則を機械学習プロセスに適用します。

- モデルのより迅速な実験と開発
- 実稼働環境へのモデルのより迅速なデプロイ
- 品質保証

適切なモデルがあれば、Web サービス、IoT デバイス、または Power BI で簡単に使用できます。 詳細については、[デプロイする方法と場所](/azure/machine-learning/how-to-deploy-and-where)に関する記事を参照してください。

デプロイされたモデルは、[Azure Machine Learning SDK for Python](/python/api/overview/azure/ml/?view=azure-ml-py)、[Azure Machine Learning Studio (classic)](https://ml.azure.com/)、または [Machine Learning CLI](/azure/machine-learning/reference-azure-machine-learning-cli) を使用して管理できます。

これらのモデルを使用して、[リアルタイム](/azure/machine-learning/how-to-consume-web-service)で予測を返したり、データが大量の場合は[非同期](/azure/machine-learning/how-to-use-parallel-run-step)で予測を返したりすることができます。

さらに、高度な[機械学習パイプライン](/azure/machine-learning/concept-ml-pipelines)を使用して、データの準備、モデルのトレーニングと評価、およびデプロイの各手順で共同で作業することができます。 パイプラインを使用すると、次のことができます。

- クラウドでエンドツーエンドの機械学習プロセスを自動化する
- コンポーネントを再利用し、必要なときにのみステップを再実行する
- ステップごとに異なるコンピューティング リソースを使用する
- バッチ スコアリング タスクを実行する

スクリプトを使用して機械学習ワークフローを自動化する場合、[Machine Learning CLI](/azure/machine-learning/reference-azure-machine-learning-cli) には、トレーニング実行の送信やモデルのデプロイなどの一般的なタスクを実行するコマンドライン ツールが用意されています。

Azure Machine Learning の基本的な使い方については、「[次の手順](/azure/machine-learning/overview-what-is-azure-ml#next-steps)」を参照してください。

## <a name="automated-machine-learning"></a>自動化された機械学習

データ サイエンティストは、実験フェーズ中にモデルを反復処理するのに膨大な時間を費やします。 許容可能なモデルが構築されるまで、異なるアルゴリズムとハイパーパラメーターの組み合わせを試すプロセスは、全体が単調で課題を伴わない質の作業であるため、データ サイエンティストには重い負担がかかります。 これは、モデルの有効性に関して大幅なメリットをもたらす演習ですが、時間とリソースの面でコストがかかりすぎる場合があり、それによって投資収益率 (ROI) がマイナスになる可能性があります。

ここで、自動機械学習 (AutoML) が登場します。 ここでは、確率論的な行列因子分解に関する研究論文の概念が使用され、特定の問題またはシナリオを考慮しながら、提示されたデータのヒューリスティックに基づいて、インテリジェントに選択されたアルゴリズムとハイパーメーター設定を試す自動化されたパイプラインが実装されます。 このパイプラインの結果は、特定の問題とデータセットに最も適した一連のモデルです。

AutoML の詳細については、「[Azure Machine Learning の AutoML と MLOps](https://azure.microsoft.com/blog/automated-machine-learning-and-mlops-with-azure-machine-learning/)」を参照してください。

## <a name="responsible-ml"></a>信頼できる ML

AI システムの開発と使用を通して、信頼をその中心にする必要があります。 プラットフォーム、プロセス、モデルでの信頼。 社会構造に統合される AI や自律システムが増えているため、これらのテクノロジの望ましくない結果を事前に予測して軽減するための努力を積極的に行うことが重要です。

- モデルの理解と公平性の構築: モデルの動作について説明し、予測に最も影響を与える特徴を明らかにします。 モデルのトレーニングと推論の際に、ガラスボックス モデルとブラックボックス モデルの両方に対して、組み込みの Explainer を使用します。 対話型の視覚化を使用してモデルを比較し、what-if 分析を実行してモデルの精度を高めます。 最先端のアルゴリズムを使用して、モデルの公平性をテストします。 機械学習のライフサイクル全体で不公平性を軽減し、軽減されたモデルを比較し、必要に応じて意図する公平性と精度のトレードオフを図ります。
- データのプライバシーと機密性の保護: 機密性の高いプライバシーに関する最新のイノベーションを使用して、プライバシーが維持されるモデルを構築します。このモデルでは、機密情報の暴露を制限するために、詳細レベルの統計的ノイズがデータに挿入されます。 データの漏洩を識別し、露出リスクを管理するために、繰り返されるクエリをインテリジェントに制限します。 特に機械学習向けに設計された暗号化および機密機械学習 (近日公開予定) の手法を使用して、機密データを使用するモデルを安全に構築します。
- 機械学習プロセスのすべての手順の制御と管理: 組み込みの機能にアクセスして、機械学習のライフサイクル中、常に系列を自動的に追跡し、監査証跡を作成します。 データセット、モデル、実験、コードなどを追跡することで、機械学習プロセスを完全に可視化します。 カスタム タグを使用して、モデル データ シートを実装し、キー モデルのメタデータを文書化し、アカウンタビリティを向上させ、信頼できるプロセスを保証します。

詳細については、[信頼できる ML](/azure/machine-learning/concept-responsible-ml) を実装する方法を参照してください。

## <a name="integration-with-other-services"></a>他のサービスとの統合

Azure Machine Learning は、Azure プラットフォーム上の他のサービスとの連携に加え、Git や MLFlow といったオープン ソース ツールとの統合にも対応します。

- コンピューティング先 (Azure Kubernetes Service、Azure Container Instances、Azure Databricks、Azure Data Lake Analytics、Azure HDInsight など)。 コンピューティング先の詳細については、[コンピューティング先の概要](/azure/machine-learning/concept-compute-target)に関するページを参照してください。
- Azure Event Grid。 詳細については、[Azure Machine Learning イベントを使用する方法](/azure/machine-learning/how-to-use-event-grid)に関するページを参照してください。
- Azure Monitor。 詳細については、「[Azure Machine Learning の監視](/azure/machine-learning/monitor-azure-machine-learning)」を参照してください。
- データ ストア (Azure Storage アカウント、Azure Data Lake Storage、Azure SQL Database、Azure Database for PostgreSQL、Azure Open Datasets など)。 詳細については、[Azure Storage サービスのデータへのアクセス](/azure/machine-learning/how-to-access-data)に関する記事と「[Azure Open Datasets を使用してデータセットを作成する](/azure/machine-learning/how-to-create-register-datasets#create-datasets-with-azure-open-datasets)」を参照してください。
- Azure Virtual Network。 詳細については、[仮想ネットワークでの実験と推論の安全な実行](/azure/machine-learning/how-to-enable-virtual-network)に関するページを参照してください。
- Azure Pipelines 詳細については、「[機械学習モデルのトレーニングとデプロイ](/azure/devops/pipelines/targets/azure-machine-learning?tabs=yaml&view=azure-devops)」を参照してください。
- Git リポジトリのログ。 詳細については、「[Git 統合](/azure/machine-learning/concept-train-model-git-integration)」を参照してください。
- MLFlow。 詳細については、[メトリックを追跡し、モデルをデプロイするための MLflow](/azure/machine-learning/how-to-use-mlflow) に関するページを参照してください。
- Kubeflow。 詳細については、[エンド ツー エンドのワークフロー パイプラインの構築](https://www.kubeflow.org/docs/azure/)に関するページを参照してください。
- セキュリティで保護された通信。 Azure Storage アカウントやコンピューティング先などのリソースは、仮想ネットワーク内で安全に使用してモデルをトレーニングし、推論を実行することができます。 詳細については、[仮想ネットワークでの実験と推論の安全な実行](/azure/machine-learning/how-to-enable-virtual-network)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

- 機械学習に関するホワイトペーパーと、[Machine Learning Studio](https://azure.microsoft.com/resources/whitepapers/search/?service=machine-learning-studio) と [Machine Learning service](https://azure.microsoft.com/resources/whitepapers/search/?service=machine-learning-service) に関する電子書籍を確認します。
- [AI + 機械学習アーキテクチャ](/azure/architecture/browse/)について確認します。
