---
title: クラスターの設計と操作
description: クラスターの設計と操作用のクラウド導入フレームワークにおける Kubernetes について説明します。
author: sabbour
ms.author: brblanch
ms.date: 12/16/2019
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: innovate
ms.custom: think-tank
ms.openlocfilehash: a0194f1acffc325c4975ab49822a241ded6ab16e
ms.sourcegitcommit: be12e8910e96bde100778e76f2eea24687b2885b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/25/2021
ms.locfileid: "105549511"
---
<!-- cSpell:ignore autoscaler PDBs -->

# <a name="cluster-design-and-operations"></a>クラスターの設計と操作

クラスター構成とネットワーク設計を特定します。 インフラストラクチャのプロビジョニングを自動化することで、将来のスケーラビリティに対応します。 事業継続とディザスター リカバリーを計画することで、高可用性を維持します。

## <a name="plan-train-and-proof"></a>計画、トレーニング、および証明

開始時に、以下のチェックリストとリソースを使用するとクラスターの設計の計画に役立ちます。 次の質問に答えてください。

> [!div class="checklist"]
>
>- クラスターのネットワーク設計要件を特定しましたか?
>- さまざまな要件を含むワークロードはありますか? 使用予定のノード プールの数はいくつですか?

**チェックリスト:**

- **ネットワーク設計に関する考慮事項を特定する。** クラスター ネットワークの設計上の考慮事項を理解して、ネットワーク モデルを比較し、ニーズに合った Kubernetes ネットワーク プラグインを選択します。 Azure Container Networking Interface (CNI) ネットワークについては、ノードあたりの最大ポッド数 (既定値は 30) とノード数の倍数として、必要な IP アドレスの数を検討してください。 アップグレード中に必要なノードを 1 つ追加します。 ロード バランサー サービスを選択する場合に、公開されるエンドポイントの数を減らすためのサービスが多すぎる場合は、イングレス コントローラーの使用を検討してください。 Azure CNI で適切なルーティングを確保するには、サービス CIDR が仮想ネットワークと接続されているすべての仮想ネットワークで一意である必要があります。

  詳細については、次を参照してください。
  - [Kubenet と Azure CNI ネットワーク](/azure/aks/concepts-network#azure-virtual-networks)
  - [Azure Kubernetes Service (AKS) の独自の IP アドレス範囲で kubenet ネットワークを使用する](/azure/aks/configure-kubenet)
  - [Azure Kubernetes Service (AKS) で Azure CNI ネットワークを構成する](/azure/aks/configure-azure-cni)
  - [AKS クラスターのセキュリティで保護されたネットワーク設計](https://github.com/Azure/sg-aks-workshop/blob/master/cluster-design/NetworkDesign.md)

- **複数のノード プールを作成する。** コンピューティングまたはストレージのさまざまな要件があるアプリケーションをサポートするには、必要に応じて複数のノード プールでクラスターを構成します。 たとえば、より多くのノード プールを使用して、コンピューティング集中型アプリケーションに GPU を提供したり、高パフォーマンスな SSD ストレージへのアクセスを提供したりします。 詳細については、「[Azure Kubernetes Service のクラスターで複数のノード プールを作成および管理する](/azure/aks/use-multiple-node-pools)」を参照してください。&nbsp;&nbsp;&nbsp;

- **可用性の要件を決定する。** Azure Kubernetes Service の背後に少なくとも 2 つのポッドがあれば、ポッドのエラーや再起動があった場合のアプリケーションの高可用性が確保されます。 ポッドのエラーや再起動時の負荷を処理するには、3 つ以上のポッドを使用してください。
クラスター構成では、99.95% のサービス レベル アグリーメントを満たすために、可用性セットまたは仮想マシン スケール セット内に少なくとも 2 つのノードが必要です。 ノードのエラー時と再起動時のポッドのスケジューリングを保証するには、少なくとも 3 つのポッドを使用します。

  アプリケーションにより高いレベルの可用性を提供するために、クラスターは可用性ゾーンにまたがって分散させることができます。 これらのゾーンは、特定のリージョン内の物理的に分離されたデータセンターです。 クラスター コンポーネントが複数のゾーンに分散されている場合、クラスターは、それらのゾーンのいずれかで障害が発生しても許容できます。 アプリケーションと管理操作は、1 つのデータセンター全体で障害が発生した場合でも継続して利用できます。 詳細については、「[可用性ゾーンを使用する Azure Kubernetes Service (AKS) クラスターを作成する](/azure/aks/availability-zones)」を参照してください。

## <a name="go-to-production-and-apply-best-practices"></a>運用環境へ移行してベスト プラクティスを適用する

運用環境向けにアプリケーションを準備するときには、ベスト プラクティスの最小セットを実装する必要があります。 この段階で、以下のチェックリストを使用します。 次の質問に答えてください。

> [!div class="checklist"]
>
>- クラスター インフラストラクチャを自信を持って再デプロイできますか?
>- リソース クォータを適用していますか?

**チェックリスト:**

- **クラスターのプロビジョニングを自動化する。** コードとしてのインフラストラクチャを使用することで、インフラストラクチャのプロビジョニングを自動化して災害時の回復性を高め、必要に応じて迅速にインフラストラクチャを再デプロイする機敏性を得ることができます。 詳細については、「[Terraform を使用して Azure Kubernetes Service で Kubernetes クラスターを作成する](/azure/developer/terraform/create-k8s-cluster-with-tf-and-aks)」を参照してください。

- **ポッド中断バジェットを使用して可用性を計画する。** アプリケーションの可用性を維持するには、ポッド中断バジェット (PDB) を定義して、ハードウェア障害またはクラスターのアップグレード中に最小限の数のポッドがクラスターで使用できるようにします。 詳細については、「[ポッド中断バジェットを使用して可用性を計画する](/azure/aks/operator-best-practices-scheduler#plan-for-availability-using-pod-disruption-budgets)」を参照してください。&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;

- **名前空間にリソース クォータを適用する。** 名前空間レベルでリソース クォータを計画して適用します。 クォータは、コンピューティング リソース、ストレージ リソース、およびオブジェクト数に設定できます。 詳細については、「[リソース クォータを適用する](/azure/aks/operator-best-practices-scheduler#enforce-resource-quotas)」を参照してください。

## <a name="optimize-and-scale"></a>最適化とスケーリング

アプリケーションの運用開始後、ワークフローの最適化と、アプリケーションとチームでのスケーリングの準備はどのようにして行うことができますか? 最適化とスケーリングのチェックリストを使用して準備してください。 次の質問に答えてください。

> [!div class="checklist"]
>
>- 事業継続とディザスター リカバリーの計画はありますか?
>- クラスターでアプリケーションの需要に合わせてスケーリングできますか?
>- クラスターとアプリケーションの正常性を監視し、アラートを受け取ることができますか?

**チェックリスト:**

- **アプリケーションの需要を満たすためにクラスターを自動的にスケーリングする。** アプリケーションの需要に対応するには、クラスター オートスケーラーを使用してワークロードを自動的に実行するノードの数を調整することが必要な場合があります。 詳細については、[Kubernetes クラスター オートスケーラーの構成に関するページ](/azure/aks/cluster-autoscaler)を参照してください。

- **事業継続とディザスター リカバリーを計画する。** 複数リージョンのデプロイを計画して、ストレージ移行計画を作成し、コンテナー イメージの geo レプリケーションを有効にします。 詳細については、[リージョン デプロイのベスト プラクティスに関するページ](/azure/aks/operator-best-practices-multi-region)と [Azure Container Registry の geo レプリケーションに関するページ](/azure/container-registry/container-registry-geo-replication)を参照してください。 - 

- **大規模な監視およびトラブルシューティングを構成する。** Kubernetes でアプリケーションのアラートと監視を設定します。 既定の構成、より高度なメトリックを統合する方法、アプリケーションを動作させるためにカスタム監視およびアラートを追加する方法について説明します。

    詳細については、次を参照してください。

  - [Kubernetes の監視とアラートの概要 (ビデオ)](https://www.youtube.com/watch?v=W7aN_z-cyUw&list=PLLasX02E8BPCrIhFrc_ZiINhbRkYMKdPT&index=16)
  - [コンテナーに対する Azure Monitor を使用してアラートを構成する](/azure/azure-monitor/containers/container-insights-overview)
  - [マスター コンポーネントの&nbsp;診断&nbsp;ログ&nbsp;を確認する](/azure/aks/view-control-plane-logs)
  - [Azure Kubernetes Service (AKS) 診断](/azure/aks/concepts-diagnostics)
