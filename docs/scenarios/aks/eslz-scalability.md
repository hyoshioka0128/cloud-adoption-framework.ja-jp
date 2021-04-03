---
title: AKS のエンタープライズ規模のスケーラビリティ
description: AKS のエンタープライズ規模のスケーラビリティに関するガイドライン
author: xstabel
ms.author: ctalaver
ms.date: 03/01/2021
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: migrate
ms.custom: think-tank, e2e-aks
ms.openlocfilehash: 44d215c990f1666997d8ea8199b28778e3fe06bd
ms.sourcegitcommit: 5716a8165934bd69d02d9d3641785039196aee3a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "105733799"
---
<!-- docutune:casing "HPA" -->

# <a name="aks-enterprise-scale-scalability"></a>AKS のエンタープライズ規模のスケーラビリティ

AKS は、GPU などの特殊な機能を備えたノード プールを追加することでインフラストラクチャのニーズ (容量の増減が必要) に応じて、またはアプリケーションのニーズに応じてスケールイン/アウトすることができます。この場合、コンカレント接続の数と速度、要求数、AKS アプリケーションでのバックエンド待機時間などのいくつか点を考慮します。

AKS の最も一般的なスケーラビリティ オプションには、クラスター自動スケーラー (CPU とメモリの使用率に基づいてノードを自動的に追加または削除する)、またはアプリケーションを CPU とメモリの使用率およびさらに高度なメトリックに基づいてスケールイン/アウトする HPA (水平ポッド自動スケーラー) があります。

## <a name="design-considerations"></a>設計上の考慮事項

次に、考慮すべき重要な点をいくつか示します。

- アプリケーションで、迅速なスケーラビリティが必要とされているか (時間が切迫している)。
  - ポッドを迅速にプロビジョニングするには、仮想ノードを使用します。これは、Linux ノード/ポッドでのみサポートされています。
- ワークロードに時間的な制約がなく、中断が処理可能であるか。 [スポット VM](/azure/aks/spot-node-pool) の使用を検討します。
- 基盤となるインフラストラクチャ (ネットワーク プラグイン、IP 範囲、サブスクリプションの制限、クォータなど) はスケールアウトに対応しているか。
- スケーラビリティの自動化を検討する

  - クラスターの自動スケールを有効にすると、ノードの数をスケーリングできます。 [クラスターの自動スケール](/azure/aks/cluster-autoscaler)と [0 へのスケーリング](/azure/aks/scale-cluster#scale-user-node-pools-to-0)を検討します。
  - 水平ポッド自動スケーラーでは、ポッドの数が自動的にスケーリングされます。
- マルチゾーンとノード プールでのスケーラビリティを検討する
  - ノード プールを作成する際には、[AKS を使用して Availability Zones](/azure/aks/availability-zones) を設定することを検討します。
  - さまざまな要件があるアプリケーションをサポートするために、複数のノード プールを使用することを検討します。
  - クラスター自動スケーラーを使用してノード プールをスケーリングします。
  - ユーザー ノード プールを 0 にスケーリングすることができます。 [制限事項](/azure/aks/use-multiple-node-pools#limitations)を参照してください。

## <a name="design-recommendations"></a>設計の推奨事項

設計に関する次のベスト プラクティスに従ってください。

- 仮想マシン スケール セットを使用します。これは、自動スケール、複数のノード プール、Windows ノード プールのサポートなどのシナリオに必要です。
  - Azure portal において、または Azure CLI を使用して、スケーラビリティの設定を手動で有効にしたり、編集したりしないでください。
- 高速バースト自動スケールを行う必要がある場合は、迅速かつ無制限のスケーラビリティと秒単位の請求のために、Container Instances と[仮想ノード](/azure/aks/virtual-nodes-portal)を使用して AKS クラスターからバーストすることを選択します。
- VM ベースのワーカー ノードを使用して予測可能なスケーラビリティを実現するには、[クラスター自動スケーラー](/azure/aks/cluster-autoscaler)を使用し、[0 にスケーリング](/azure/aks/scale-cluster#scale-user-node-pools-to-0)します。
- [クラスター自動スケーラー](/azure/aks/cluster-autoscaler)を有効にして、アプリケーションの要求を満たします。
  - [複数のノード プール](/azure/aks/cluster-autoscaler#use-the-cluster-autoscaler-with-multiple-node-pools-enabled)に対して自動スケールを有効にすることができます。
- [水平ポッド自動スケーラー (HPA)](/azure/aks/concepts-scale#horizontal-pod-autoscaler) を有効にして、アプリケーションのビジー時間を短縮します。
  - すべてのコンテナーとポッドでは、リソース要求と制限が定義されている必要があります。
  - HPA では、観測されたリソース制限 (CPU/メモリまたはカスタム メトリック) に基づいて、ポッドの数が自動的にスケーリングされます。
- [コンテナーの Azure Monitor](/azure/azure-monitor/containers/container-insights-overview) とライブ監視を有効にして、クラスターとワークロードの使用率を監視します。
- アプリケーションのリソース要件が異なる場合は、複数のノード プールを使用します。
  - [ノード プールの VM サイズを指定](/azure/aks/use-multiple-node-pools#specify-a-vm-size-for-a-node-pool)できることに注意してください。
- 中断と削除を処理可能な、時間的な制約がないワークロードには、[スポット VM ベースのノード プール](/azure/aks/spot-node-pool)を検討します。
