---
title: AKS のエンタープライズ規模の管理と監視
description: このエンタープライズ規模のシナリオで AKS の管理と監視を向上させる方法について説明します。
author: BrianBlanchard
ms.author: pidebrui
ms.date: 03/01/2021
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.custom: think-tank, e2e-aks
ms.openlocfilehash: 2883b695062064f409a7bfd662d827b763806c88
ms.sourcegitcommit: 5716a8165934bd69d02d9d3641785039196aee3a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "105733798"
---
# <a name="management-and-monitoring-for-aks-enterprise-scale-scenario"></a>AKS のエンタープライズ規模のシナリオの管理と監視

Kubernetes は比較的若いテクノロジで、急速に進化しており、優れたエコシステムを備えています。 そのため、管理することが困難な場合があります。 管理と監視を念頭に置いてソリューションを適切に設計することで、オペレーショナル エクセレンスと顧客の成功に向けて作業を行うことができます。

## <a name="design-considerations"></a>設計上の考慮事項

次の要因について検討します。

- [AKS の制限](/azure/aks/quotas-skus-regions)に注意します。 複数の AKS インスタンスを使用して、これらの制限を超えてスケーリングします。
- ワークロードをクラスター内で論理的に分離する方法、別々のクラスターに物理的に分離する方法に注意します。
- ワークロードのリソース消費を制御する方法に注意します。
- Kubernetes がワークロードの正常性を理解するために役立つ方法に注意します。
- さまざまな仮想マシンのサイズと、そのいずれかを使用した場合の影響に注意します。 より大きな VM では、より多くの負荷を処理できます。 より小さな VM は、計画済みおよび計画外のメンテナンスで使用できなくても、より簡単に他の VM に置き換えることができます。 また、スケール セット内の VM であるノード プールの概念にも注意します。これにより、同じクラスター内の異なるサイズの仮想マシンを持つことができます。
- AKS を監視してログに記録する方法に注意します。 Kubernetes はさまざまなコンポーネントで構成されており、監視とログ記録によって、その正常性、傾向、および潜在的な問題に関する洞察を提供します。
- 監視とログ記録に基づいて構築すると、Kubernetes やそこで実行されるアプリケーションで生成されるイベントが多くなる場合があります。 アラートを使用すると、履歴目的のログ エントリと、早急に対処する必要があるログ エントリを区別できます。
- 必要な更新プログラムやアップグレードに注意します。 Kubernetes レベルでは、メジャー バージョン、マイナー バージョン、およびパッチ バージョンがあります。 アップストリーム Kubernetes のポリシーに従って、サポートされている状態を維持するために、顧客はこれらの更新プログラムを適用する必要があります。 ワーカー ホスト レベルの OS カーネル修正プログラムでは、再起動や新しい OS バージョンへのアップグレードが必要になる場合があります。再起動は顧客が行う必要があります。
- クラスターのリソースの制限と個々のワークロードに注意します。
- ベスト プラクティスの使用を監視します。
- Azure を使用して複数のクラスターを管理するためのオプションと、Azure の外部の Kubernetes 認定クラスターを管理するためのオプションに注意します。

## <a name="design-recommendations"></a>設計の推奨事項

- AKS の制限について理解します。
  - [クォータと地域の制限](/azure/aks/quotas-skus-regions)
  - [AKS サービスの制限](/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-kubernetes-service-limits)
- 名前空間レベルで論理的な分離を使用して、アプリケーション、チーム、環境、事業単位を分離します。 [マルチテナントとクラスター分離](/azure/aks/operator-best-practices-cluster-isolation)。 ノード プールは、ノードの仕様が異なるノードでも Kubernetes が[複数のノード プール](/azure/aks/use-multiple-node-pools)をアップグレードするなどのメンテナンスでも役立ちます。
- 名前空間レベルでリソース クォータを計画して適用します。 ポッドでリソースの要求と制限が定義されていない場合は、デプロイを拒否します。 リソースの使用状況を監視し、必要に応じてクォータを調整します。 [スケジューラの基本的な機能](/azure/aks/operator-best-practices-scheduler)
- 正常性プローブをサービスに追加します。 ポッドに、`livenessProbe`、`readinessProbe` および `startupProbe` [AKS 正常性プローブ](/azure/application-gateway/ingress-controller-add-health-probes)が含まれていることを確認します。
- 密度向上による利点が得られるように、複数のコンテナー インスタンスを格納するのに十分な数の VM サイズを使用します。ただし、障害が発生しているノードのワークロードをクラスターで処理できなくなるほど大きくしないようにします。
- 監視ソリューションを使用します。 [Azure Monitor for containers](/azure/azure-monitor/containers/container-insights-overview) は既定で設定され、多くの洞察への簡単なアクセスを提供します。 より深く掘り下げたい場合や、Prometheus を使用した経験がある場合は、[Prometheus 統合](/azure/azure-monitor/containers/container-insights-prometheus-integration)を使用できます。 AKS で監視アプリケーションも実行したい場合は、Azure Monitor を使用してそのアプリケーションを監視する必要もあります。
- アラート システムを使用して、直接アクションが必要なときに通知を提供します。 [メトリック アラート](/azure/azure-monitor/containers/container-insights-metric-alerts)
- アプリケーションの需要を満たし、ピーク時の負荷を軽減するために、[ノード プールの自動スケーリング](/azure/aks/cluster-autoscaler)機能を[ポッドの水平オートスケーラー](/azure/aks/concepts-scale#horizontal-pod-autoscaler)と共に使用します。
- Azure Advisor を使用して、コスト、セキュリティ、信頼性、オペレーショナル エクセレンス、およびパフォーマンスに関するベスト プラクティスの推奨事項を取得します。 また、[Azure Security Center](/azure/security-center/defender-for-kubernetes-introduction) を使用して、イメージの脆弱性などの脅威を防止および検出します。
- [Azure Arc](/azure/azure-arc/kubernetes/overview) を使用すると、Azure Policy、Security Center、GitOps などを使用して Kubernetes で Azure 内の非 AKS Kubernetes クラスターを管理できます。
