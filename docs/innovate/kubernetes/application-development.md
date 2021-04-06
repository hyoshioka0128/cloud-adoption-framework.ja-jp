---
title: アプリケーションの開発とデプロイ
description: アプリケーション開発とアーキテクチャのクラウド導入フレームワークで Kubernetes を使用する方法について説明します。
author: sabbour
ms.author: brblanch
ms.date: 03/20/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: innovate
ms.custom: think-tank
ms.openlocfilehash: 3876a878566e57ad15e3fe5fe1b19ec49010bebb
ms.sourcegitcommit: be12e8910e96bde100778e76f2eea24687b2885b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/25/2021
ms.locfileid: "105549528"
---
<!-- cSpell:ignore autoscaler Istio Linkerd -->

# <a name="application-development-and-deployment"></a>アプリケーションの開発とデプロイ

アプリケーション開発のパターンとプラクティスを確認し、Azure Pipelines を構成し、サイト信頼性エンジニアリング (SRE) のベスト プラクティスを実装します。

## <a name="plan-train-and-proof"></a>計画、トレーニング、および証明

開始時に、以下のチェックリストとリソースを使用するとアプリケーションの開発とデプロイを計画するのに役立ちます。 次の質問に答えてください。

> [!div class="checklist"]
>
> - 開発環境と設定ワークフローを準備しましたか?
> - Kubernetes アプリケーションの開発をサポートするためのプロジェクト フォルダーの構造はどのようにしますか?
> - アプリケーションの状態、構成、およびストレージの要件を特定しましたか?

<!-- docutune:casing "AAD Pod Identity" -->

**チェックリスト:**

- **開発環境を準備する。** コンテナーの作成と開発ワークフローの設定に必要なツールを使用して、環境を構成します。

  詳細については、次を参照してください。
  - [Visual Studio Code での Docker の操作](https://code.visualstudio.com/docs/containers/overview) <br>
  - [Visual Studio Code での Kubernetes の操作](https://code.visualstudio.com/docs/azure/kubernetes) <br>
  - [Azure Dev Spaces の概要](/azure/dev-spaces/about)

- **アプリケーションをコンテナー化する。** アプリケーション スキャフォールディング、内部ループのワークフロー、アプリケーション管理フレームワーク、CI/CD パイプライン、ログの集計、監視、アプリケーション メトリックなど、エンドツーエンドの Kubernetes 開発エクスペリエンスについてよく理解します。

  詳細については、次を参照してください。
  - [Docker と Kubernetes でアプリケーションをコンテナーに入れる (電子書籍)](https://azure.microsoft.com/resources/containerize-your-apps-with-docker-and-kubernetes/) <br>
  - [Azure でのエンドツーエンドの Kubernetes 開発エクスペリエンス (ウェビナー)](https://info.microsoft.com/AU-AzureApp-WBNR-FY20-11Nov-12-ContainerizeYourApplicationswithKubernetesonAzure-SRDEM10557_LP02OnDemandRegistration-ForminBody.html)

- **一般的な Kubernetes シナリオを確認する。** Kubernetes はマイクロサービスを提供するためのプラットフォームと見なされることが多いですが、はるかに広範なプラットフォームになりつつあります。 バッチ分析やワークフローなどの一般的な Kubernetes シナリオについては、こちらのビデオをご覧ください ([Kubernetes を使用する一般的なシナリオ (ビデオ)](https://www.youtube.com/watch?list=PLLasX02E8BPCrIhFrc_ZiINhbRkYMKdPT&v=zd8vYhrFXp4&index=7))。&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;

- **Kubernetes 用にアプリケーションを準備する。** Kubernetes 用にアプリケーションのファイル システム レイアウトを準備し、週単位または日単位のリリース用に整理します。 Kubernetes のデプロイ プロセスによって、信頼性のあるゼロダウンタイムのアップグレードがどのように実現されるか理解します。

  詳細については、次を参照してください。
  - [Kubernetes アプリケーションを成功させるためのプロジェクトの設計とレイアウト (ウェビナー)](https://info.microsoft.com/ww-OnDemandRegistration-successful-kubernetes-applications-webinar.html) <br>
  - [Kubernetes デプロイのしくみ (ビデオ)](https://www.youtube.com/watch?list=PLLasX02E8BPCrIhFrc_ZiINhbRkYMKdPT&v=mNK14yXIZF4&index=3) <br>
  - [AKS ワークショップの詳細](/learn/modules/aks-workshop/)

- **アプリケーション ストレージを管理する。** 適切なストレージ オプションを提供できるように、パフォーマンスのニーズとポッドのアクセス方法を理解します。 接続されたストレージをバックアップし、その復元プロセスをテストする方法を計画する必要もあります。

  詳細については、次を参照してください。

  - [Kubernetes のステートフル アプリケーションの基本 (ビデオ)](https://www.youtube.com/watch?list=PLLasX02E8BPCrIhFrc_ZiINhbRkYMKdPT&v=GieXzb91I40&index=9) <br>
  - [Docker アプリケーションの状態とデータ](/dotnet/architecture/microservices/architect-microservice-container-applications/docker-application-state-data) <br>
  - [Azure Kubernetes Service のストレージ オプション](/azure/aks/operator-best-practices-storage)

- **アプリケーション シークレットを管理する。** アプリケーション コードに資格情報を保存しないでください。 キーと資格情報を保存および取得するには、キー コンテナーを使用する必要があります。

  詳細については、次を参照してください。
  - [Kubernetes と構成管理のしくみ (ビデオ)](https://www.youtube.com/watch?list=PLLasX02E8BPCrIhFrc_ZiINhbRkYMKdPT&v=vRcQOZLnKUk&index=11) <br>
  - [Kubernetes でのシークレット管理について (ビデオ)](https://www.youtube.com/watch?list=PLLasX02E8BPCrIhFrc_ZiINhbRkYMKdPT&v=KmhM33j5WYk&index=10) <br>
  - [Kubernetes での Azure Key Vault の使用](https://github.com/azure/kubernetes-keyvault-flexvol) <br>
  - [Azure AD ポッド ID を使用した Azure リソースの認証とアクセス](https://github.com/azure/aad-pod-identity)

## <a name="deploy-to-production-and-apply-best-practices"></a>運用環境へのデプロイとベスト プラクティスの適用

運用環境向けにアプリケーションを準備するときには、ベスト プラクティスの最小セットを実装する必要があります。 この段階で、以下のチェックリストを使用します。 次の質問に答えてください。

> [!div class="checklist"]
>
> - アプリケーションのすべての側面を監視できますか?
> - アプリケーションのリソース要件を定義しましたか? スケーリング要件はどのようになりますか?
> - 運用システムに影響を与えることなく、アプリケーションの新しいバージョンをデプロイできますか?

<!-- -->

**チェックリスト:**

- **readiness と liveness の正常性チェックを構成する。** Kubernetes は readiness と liveness のチェックを使用して、アプリケーションがトラフィックを受信する準備ができたこと、およびアプリケーションの再起動が必要になるタイミングを確認します。 このようなチェックを定義しないと、Kubernetes はアプリケーションが起動して実行されているかどうかを判断できません。 詳細については、[liveness と readiness の確認](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/)に関するページを参照してください。

- **ログ記録、アプリケーションの監視、およびアラートを構成する。** コンテナーの監視は、複数のアプリケーションを含む大規模な運用クラスターを実行するときは特に重要です。 コンテナー化されたアプリケーションに推奨されるログ記録方法は、標準出力 (`stdout`) と標準エラー (`stderr`) ストリームに書き込みを行うことです。

  詳細については、次を参照してください。
  - [Kubernetes でのログ記録](https://kubernetes.io/docs/concepts/cluster-administration/logging/) <br>
  - [Kubernetes の監視とアラートの概要 (ビデオ)](https://www.youtube.com/watch?list=PLLasX02E8BPCrIhFrc_ZiINhbRkYMKdPT&v=W7aN_z-cyUw&index=16) <br> [コンテナーに対する Azure Monitor](/azure/azure-monitor/containers/container-insights-overview) <br>
  - [Azure Kubernetes Service (AKS) での Kubernetes マスター ノード ログの有効化とレビュー](/azure/aks/view-control-plane-logs) <br>
  - [Kubernetes のログ、イベント、ポッド メトリックをリアルタイムで表示する](/azure/azure-monitor/containers/container-insights-livedata-overview)

- **アプリケーションのリソース要件を定義する。** Kubernetes クラスター内のコンピューティング リソースを管理する主な方法は、ポッドの要求と制限を使用する方法です。 これらの要求と制限により、Kubernetes スケジューラは、どのコンピューティング リソースにポッドを割り当てる必要があるか認識できます。 詳細については、「[ポッドのリソースの要求と制限を定義する](/azure/aks/developer-best-practices-resource-management)」を参照してください。&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;

- **アプリケーションのスケーリング要件を構成する。** Kubernetes はポッドの水平自動スケーリングをサポートしており、CPU 使用率などの選ばれたメトリックに応じて、デプロイのポッドの数を調整します。 オートスケーラーを使用するには、ポッド内のすべてのコンテナーで CPU の要求と制限が定義されている必要があります。 詳細については、[ポッドの自動スケーリングの構成に関するページ](/azure/aks/tutorial-kubernetes-scale#autoscale-pods)を参照してください。

- **自動化されたパイプラインと DevOps を使用してアプリケーションをデプロイする。** コードのコミットから運用環境へのデプロイまでのすべての手順を完全に自動化することで、チームはコードの構築に専念でき、手動による日常的な手順でのオーバーヘッドと潜在的な人的エラーを解消できます。 新しいコードのデプロイが迅速になり、リスクが少なくなるため、チームは俊敏性と生産性を向上させ、実行中のコードについて自信を深めることができます。

  詳細については、次を参照してください。
  - [DevOps のプラクティスを進化させる](/learn/paths/evolve-your-devops-practices/) <br>
  - [Kubernetes ビルド パイプラインの設定 (ビデオ)](https://www.youtube.com/watch?list=PLLasX02E8BPCrIhFrc_ZiINhbRkYMKdPT&v=5irsAdKoEBU&index=6) <br>
  - [Azure Kubernetes Service のデプロイ センター](/azure/aks/deployment-center-launcher) <br>
  - [Azure Kubernetes Service にデプロイするための GitHub のアクション](/azure/aks/kubernetes-action) <br>
  - [Jenkins を使用した Azure Kubernetes Service への CI/CD](/azure/developer/jenkins/deploy-from-github-to-aks)

## <a name="optimize-and-scale"></a>最適化とスケーリング

アプリケーションの運用開始後、ワークフローの最適化と、アプリケーションとチームでのスケーリングの準備はどのように行うことができますか? 最適化とスケーリングのチェックリストを使用して準備してください。 次の質問に答えてください。

> [!div class="checklist"]
>
> - 横断的なアプリケーションの問題はアプリケーションから抽象化されていますか?
> - 新しい機能やバージョンを繰り返し適用する一方で、システムとアプリケーションの信頼性を維持できますか?

<!-- docutune:casing Consul -->

**チェックリスト:**

- **API ゲートウェイをデプロイする。** マイクロサービスへのエントリ ポイントとして機能する API ゲートウェイにより、マイクロサービスからクライアントが分離され、別のセキュリティ レイヤーが追加され、横断的問題を処理する負担を排除することでマイクロサービスの複雑さが軽減されます。 詳細については、「[Azure Kubernetes Service にデプロイされたマイクロサービスで Azure API Management を使用する](/azure/api-management/api-management-kubernetes)」を参照してください。

- **サービス メッシュをデプロイする。** サービス メッシュは、ワークロードに対するトラフィック管理、回復性、ポリシー、セキュリティ、強力な ID、可観測性などの機能を提供します。 アプリケーションはこれらの運用上の機能から切り離され、サービス メッシュがそれらをアプリケーション レイヤーからインフラストラクチャ レイヤーに移動します。

  詳細については、次を参照してください。
  - [Kubernetes でのサービス メッシュのしくみ (ビデオ)](https://www.youtube.com/watch?list=PLLasX02E8BPCrIhFrc_ZiINhbRkYMKdPT&v=izVWk7rYqWI&index=15)&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <br>
  - [サービス メッシュの詳細](/azure/aks/servicemesh-about) <br>
  - [Azure Kubernetes Service での Istio の使用](/azure/aks/servicemesh-istio-about) <br>
  - [Azure Kubernetes Service での Linkerd の使用](/azure/aks/servicemesh-linkerd-about) <br>
  - [Azure Kubernetes Service での Consul の使用](/azure/aks/servicemesh-consul-about)

- **サイト信頼性エンジニアリング (SRE) のプラクティスを実施する。** サイト信頼性エンジニアリング (SRE) は、マーケットプレースで要求される速度で反復を行う一方で、重要なシステムとアプリケーションの信頼性を維持できる実証済みの手法です。

  詳細については、次を参照してください。
  - [サイト信頼性エンジニアリング (SRE) の概要](/learn/modules/intro-to-site-reliability-engineering/) <br>
  - [Microsoft での DevOps: ゲーム ストリーミングの SRE](https://azure.microsoft.com/resources/devops-at-microsoft-game-streaming-sre/)
