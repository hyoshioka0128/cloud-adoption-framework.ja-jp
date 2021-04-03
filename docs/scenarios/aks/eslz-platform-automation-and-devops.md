---
title: エンタープライズ規模の Azure Kubernetes Service (AKS) プラットフォームの自動化と DevOps
description: Azure Kubernetes Service (AKS) プラットフォームの自動化と DevOps に関する設計上の推奨事項と考慮事項について説明します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 03/01/2021
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.custom: think-tank, e2e-aks
ms.openlocfilehash: af081d354b1d07701360e9355feacef1253acf87
ms.sourcegitcommit: 5716a8165934bd69d02d9d3641785039196aee3a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "105733823"
---
# <a name="aks-enterprise-scale-platform-automation-and-devops"></a>エンタープライズ規模の AKS プラットフォームの自動化と DevOps

クラウドネイティブ構造である Kubernetes には、デプロイと運用にクラウドネイティブのアプローチが必要です。 Azure と Kubernetes はオープンで拡張可能なプラットフォームです。適切に設計された API が豊富に用意され、自動化する機会と機能を存分に利用できます。 自動化および一般的な DevOps のベスト プラクティスに基づいて、DevOps と高度に自動化されたアプローチを計画します。

## <a name="design-considerations"></a>設計上の考慮事項

AKS プラットフォームの自動化と DevOps の設計上の考慮事項を次に示します。

- エンジニアリングと自動化のアプローチを決定する場合は、[Azure サービスの制限](/azure/azure-resource-manager/management/azure-subscription-service-limits)と継続的インテグレーション/継続的デリバリー (CI/CD) 環境について検討してください。 別の例については、[GitHub の使用制限](https://docs.github.com/actions/reference/usage-limits-billing-and-administration)を参照してください。

- 開発、テスト、Q&A、運用環境へのアクセスをセキュリティで保護する場合は、CI/CD の観点からセキュリティ オプションを検討してください。 デプロイは自動的に行われるので、それに応じてアクセス制御をマップします。

- デプロイされたすべてのリソースを一意に識別するための規則を明確に定義して、プレフィックスとサフィックスを使用することを検討してください。 これらの名前付け規則により、ソリューションを隣接してデプロイする場合の競合を回避し、チームの機敏性とスループット全体を向上させます。

- 通常および Digital Rebar Provision (DRP) の方式でソリューションをエンジニアリング、更新、デプロイするときにサポートするワークフローの一覧を作成します。 これらのワークフローに従ってパイプラインをマッピングし、習熟度と生産性を最大化することを検討してください。

  検討対象のシナリオとパイプラインの例を次に示します。
  - クラスターのデプロイ、修正プログラムの適用、アップグレード
  - アプリケーションのデプロイとアップグレード
  - アドオンのデプロイとメンテナンス
  - ディザスター リカバリーのフェールオーバー
  - ブルーグリーン デプロイ
  - カナリア環境のメンテナンス

- [サービス メッシュ](/azure/aks/servicemesh-about)を使用して、セキュリティ、暗号化、ログ機能をワークロードに追加することを検討してください。

- デプロイと関連アーティファクトを追跡およびトレースすることで、サブスクリプション、タグ付け、ラベルなどの他のリソースをデプロイして、DevOps エクスペリエンスをサポートすることを検討してください。

- "*家畜対ペット*" のパラダイム シフトの影響について検討してください。 ポッドと Kubernetes のその他の側面を一過性のものであると予想して、自動化とパイプラインのインフラストラクチャを適切に調整します。 IP アドレスやその他のリソースを固定または永続的に使用しないでください。

## <a name="design-recommendations"></a>設計の推奨事項

AKS プラットフォームの自動化と DevOps の設計上の推奨事項を次に示します。

- パイプラインまたはアクションに依存して、次のことを行います。
  - チーム全体に適用するプラクティスを最大化します。
  - 車輪の再発明に伴う負担の多くを取り除きます。
  - 全体的な品質と機敏性に関する予測可能性と分析情報を提供します。

- トリガーベースおよびスケジュールされたパイプラインを使用して、早い段階で頻繁にデプロイします。 トリガーベースのパイプラインは、変更が適切に検証されることを保証し、スケジュールされたパイプラインは、変化する環境での動作を管理します。

- インフラストラクチャのデプロイをアプリケーションのデプロイから分離します。 コア インフラストラクチャの変更は、アプリケーションよりも少なくなります。 それぞれの種類のデプロイを別のフローとパイプラインとして扱います。

- [クラウドネイティブ](/dotnet/architecture/cloud-native/introduction)の概念を使用してデプロイします。 [Infrastructure as Code](/azure/devops/learn/what-is-infrastructure-as-code) を使用して、コントロール プレーンを含むインフラストラクチャをデプロイし、[Helm](https://helm.sh/) と [Kubernetes オペレーター](https://kubernetes.io/docs/concepts/extend-kubernetes/operator/)を使用して、Kubernetes ネイティブ コンポーネントをデプロイおよび維持します。

- [GitOps](/azure/azure-arc/kubernetes/tutorial-use-gitops-connected-cluster) を使用して、アプリケーションをデプロイおよび維持します。 GitOps によって Git リポジトリが信頼できる唯一の情報源として使用されることで、構成ドリフトを回避し、ロールバックや関連手順実行中の生産性と信頼性を向上させます。

- シークレットやその他の機密性の高いアーティファクトを GitHub シークレットに格納し、実行中に必要に応じてアクションやその他のワークフロー パーツから読み取ることができるようにします。

- 構成項目と設定をハードコードしないようにして、デプロイの同時実行性を最大化するように努めます。

- インフラストラクチャとアプリケーションに関連するデプロイ全体で、よく知られている規則に依存します。 [アドミッション コントローラー](https://kubernetes.io/blog/2019/03/21/a-guide-to-kubernetes-admission-controllers/)を [gatekeeper](https://github.com/open-policy-agent/gatekeeper) と組み合わせて使用して、他の定義済みポリシー間の規則を検証し、適用します。

- 以下を使用した[シフト レフト](/azure/devops/learn/devops-at-microsoft/shift-left-make-testing-fast-reliable)を一貫して採用します。
  - セキュリティ。パイプラインの初期段階で、コンテナー スキャンなどの脆弱性スキャン ツールを追加します。
  - ポリシー。Policy as Code を使用し、アドミッション コントローラーを介してクラウドネイティブ方式でポリシーを適用します。

- すべての障害、エラー、または停止を、ソリューション全体の品質を自動化し、向上させる機会として扱います。 このアプローチを左シフトおよび[サイト信頼性エンジニアリング (SRE)](/azure/site-reliability-engineering/) フレームワークに統合します。
