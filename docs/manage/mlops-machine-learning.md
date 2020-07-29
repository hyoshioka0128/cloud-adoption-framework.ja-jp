---
title: Azure Machine Learning を使用した MLOps
description: Azure 向けのクラウド導入フレームワークを使用して、クラウドでの運用管理を実現するために行うべきさまざまな移行について理解します。
author: v-hanki
ms.author: janet
ms.date: 07/14/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: operate
ms.openlocfilehash: e5320b72d89e89c4034480614272c526e61e97b1
ms.sourcegitcommit: 9163a60a28ffce78ceb5dc8dc4fa1b83d7f56e6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2020
ms.locfileid: "86452662"
---
# <a name="mlops-with-azure-machine-learning"></a>Azure Machine Learning を使用した MLOps

Machine Learning 操作 (MLOps) は、ワークフローの効率を向上させる DevOps の原則と実践に基づいています。 たとえば、継続的インテグレーション、配信、デプロイです。 MLOps では、次のことを目標に、これらの原則を機械学習プロセスに適用します。

- モデルのより迅速な実験と開発
- 実稼働環境へのモデルのより迅速なデプロイ
- 品質保証

Azure Machine Learning で提供される MLOps 機能は次のとおりです。

- **再現可能な ML パイプラインを作成する**。 Machine Learning パイプラインを使用すると、データの準備、トレーニング、およびスコア付けプロセスに対して、反復可能かつ再利用可能な手順を定義できます。
- モデルのトレーニングとデプロイのために**再利用可能なソフトウェア環境を作成する** 。
- **どこからでもモデルの登録、パッケージ化、デプロイを行う**。 モデルを使用するために必要な関連メタデータを追跡することもできます。
- **エンドツーエンドの ML ライフサイクルのために管理データを取得する**。 ログに記録される情報には、モデルを公開しているユーザー、変更が行われた理由、モデルがいつ実稼働環境にデプロイされたか、または使用されたかが含まれます。
- **ML ライフサイクルにおけるイベントについて通知とアラートを行う**。 たとえば、実験の完了、モデルの登録、モデル デプロイ、データ ドリフトの検出などです。
- **運用および ML 関連の問題について ML アプリケーションを監視する**。 トレーニングと推論の間でのモデル入力の比較、モデル固有のメトリックの調査、および ML インフラストラクチャに関する監視とアラートの提供を行います。
- **Azure Machine Learning と Azure Pipelines を使用して、エンド ツー エンドの ML ライフサイクルを自動化する**。 パイプラインの使用により、頻繁にモデルを更新し、新しいモデルをテストし、他のアプリケーションやサービスとともに新しい ML モデルを継続的にロールアウトすることができます。

## <a name="best-practices-for-mlops-with-azure-machine-learning"></a>Azure Machine Learning を使用した MLOps のベスト プラクティス

モデルは、寿命が有機的であり、維持されないと劣化するため、コードとは異なります。 デプロイすると、実際のビジネス価値を追加できるようになり、標準のエンジニアリング プラクティスを採用するためのツールがデータ サイエンティストに提供されると、それが容易になります。

「当社の技術スタックを統合し、ビッグ データおよびオンライン ソフトウェアのエンジニアを、データ サイエンティストと結び付けることで、開発期間を数か月からほんの数週間に短縮しました。」 "*Naeem Khedarun プリンシパル ソフトウェア エンジニア、ASOS*"

Azure 上で MLOps を使用すると、モデルの価値を迅速に把握し、その価値を長期にわたって維持できます。

- 再現可能なモデルと再利用可能なトレーニング パイプラインを作成します。
- 品質管理や A/B テストなどのために、モデルのパッケージ化、検証、デプロイを簡略化します。
- モデルの動作を説明および観察し、再トレーニング プロセスを自動化します。

MLOps によって、機械学習ソリューションの品質と一貫性が向上します。 Azure Machine Learning を使用して、モデルのライフサイクルを管理する方法の詳細については、「[MLOps: Azure Machine Learning を使用したモデル管理、デプロイ、および監視](https://docs.microsoft.com/azure/machine-learning/concept-model-management-and-deployment)」を参照してください。

## <a name="next-steps"></a>次のステップ

次のリソースをじっくり読んで理解を深めましょう。

- [MLOps: Azure Machine Learning を使用したモデル管理、デプロイ、および監視](https://docs.microsoft.com/azure/machine-learning/concept-model-management-and-deployment)
- [Azure Machine Learning を使用したモデルのデプロイ](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where)方法とデプロイ先
- チュートリアル: [ACI に画像分類モデルをデプロイする](https://docs.microsoft.com/azure/machine-learning/tutorial-deploy-models-with-aml)
- [エンドツーエンドの MLOps サンプル リポジトリ](https://github.com/microsoft/MLOps)
- [Azure Pipelines を使用した ML モデルの CI/CD](https://docs.microsoft.com/azure/devops/pipelines/targets/azure-machine-learning?view=azure-devops&tabs=yaml)
-  [デプロイされたモデルを利用する](https://docs.microsoft.com/azure/machine-learning/how-to-consume-web-service)クライアントを作成する
- [大規模な機械学習](https://docs.microsoft.com/azure/architecture/data-guide/big-data/machine-learning-at-scale)
- [Azure AI リファレンス アーキテクチャとベスト プラクティスのリポジトリ](https://github.com/microsoft/AI)
