---
title: Azure Machine Learning を使用した MLOps
description: 継続的インテグレーション、継続的デリバリー、継続的デプロイなど、ワークフローの効率を向上させる機械学習の運用 (MLOps) の原則と実践について説明します。
author: v-hanki
ms.author: janet
ms.date: 07/14/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: operate
ms.custom: think-tank
ms.openlocfilehash: 5dd750bcff3e2db84c709db2f4eaf853811d345a
ms.sourcegitcommit: 9d76f709e39ff5180404eacd2bd98eb502e006e0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/17/2021
ms.locfileid: "100631714"
---
# <a name="machine-learning-operations-with-azure-machine-learning"></a>Azure Machine Learning を使用した機械学習の運用

機械学習の運用 (MLOps) は、継続的インテグレーション、継続的デリバリー、継続的デプロイなど、ワークフローの効率を向上させる DevOps の原則と実践に基づいています。 MLOps では、次を行うために、これらの原則を機械学習プロセスに適用します。

- より迅速にモデルの実験と開発を行います。
- より迅速にモデルのデプロイと運用を行います。
- 品質保証の実践と調整を行います。

Azure Machine Learning で提供される MLOps 機能は次のとおりです。

- **再現可能パイプラインを作成する。** 機械学習パイプラインを使用すると、データの準備、トレーニング、およびスコア付けプロセスに対して、反復可能で再利用可能な手順を定義できます。
- モデルのトレーニングとデプロイのために **再利用可能なソフトウェア環境を作成する**。
- **どこからでもモデルの登録、パッケージ化、デプロイを行う。** モデルを使用するために必要な関連メタデータを追跡できます。
- **エンドツーエンド ライフサイクルのガバナンス データを取得する。** ログに記録される情報には、モデルを公開しているユーザー、変更が行われた理由、モデルがいつ実稼働環境にデプロイされたか、または使用されたかが含まれます。
- **ライフサイクルにおけるイベントについて通知とアラートを行う。** たとえば、実験の完了、モデルの登録、モデル デプロイ、データ ドリフトの検出などに対するアラートを取得します。
- **運用と機械学習に関連する問題についてアプリケーションを監視する。** トレーニングと推論の間でのモデル入力の比較、モデル固有のメトリックの調査、および機械学習インフラストラクチャに対する監視とアラートの提供を行います。
- **Azure Machine Learning と Azure Pipelines を使用して、エンドツーエンドの機械学習ライフサイクルを自動化する。** パイプラインの使用により、頻繁にモデルを更新し、新しいモデルをテストし、他のアプリケーションやサービスと併せて新しい機械学習モデルを継続的にロールアウトすることができます。

## <a name="best-practices-for-mlops-with-azure-machine-learning"></a>Azure Machine Learning を使用した MLOps のベスト プラクティス

モデルは、寿命が有機的であり、維持されないと劣化するため、コードとは異なります。 デプロイ後は、実際のビジネス価値を追加できるようになり、標準のエンジニアリング プラクティスを採用するためのツールがデータ サイエンティストに提供されると、これを簡単に行うことができるようになります。

Azure で MLOps を使用すると、次の場合に役立ちます。

- 再現可能なモデルと再利用可能なトレーニング パイプラインを作成します。
- 品質管理や A/B テストに向けたモデルのパッケージ化、検証、デプロイを簡素化する。
- モデルの動作を説明および観察し、再トレーニング プロセスを自動化する。

MLOps によって、機械学習ソリューションの品質と一貫性が向上します。 Azure Machine Learning を使用して、モデルのライフサイクルを管理する方法の詳細については、「[MLOps: Azure Machine Learning を使用したモデル管理、デプロイ、および監視](/azure/machine-learning/concept-model-management-and-deployment)」をご覧ください。

## <a name="next-steps"></a>次のステップ

次のリソースをじっくり読んで理解を深めましょう。

- [MLOps: Azure Machine Learning を使用したモデル管理、デプロイ、および監視](/azure/machine-learning/concept-model-management-and-deployment)
- [Azure Machine Learning を使用したモデルのデプロイ](/azure/machine-learning/how-to-deploy-and-where)方法とデプロイ先
- チュートリアル: [Azure Container Instances に画像分類モデルをデプロイする](/azure/machine-learning/tutorial-deploy-models-with-aml)
- [エンドツーエンドの MLOps サンプル リポジトリ](https://github.com/microsoft/MLOps)
- [Azure Pipelines を使用した機械学習モデルの CI/CD](/azure/devops/pipelines/targets/azure-machine-learning)
- [デプロイされたモデルを利用する](/azure/machine-learning/how-to-consume-web-service)クライアントを作成する
- [大規模な機械学習](/azure/architecture/data-guide/big-data/machine-learning-at-scale)
- [Azure AI リファレンス アーキテクチャとベスト プラクティスのリポジトリ](https://github.com/microsoft/AI)
