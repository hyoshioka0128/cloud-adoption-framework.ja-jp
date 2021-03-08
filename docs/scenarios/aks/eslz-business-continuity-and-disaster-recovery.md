---
title: Azure Kubernetes Service に対するエンタープライズ規模の事業継続とディザスター リカバリー
description: このエンタープライズ規模のシナリオで、Azure Kubernetes Service の事業継続とディザスター リカバリーを向上させる方法について説明します。
author: JeffMitchell
ms.author: jemitche
ms.date: 02/18/2021
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.openlocfilehash: fa72f05e479ff56a7163a747ea5cca49c08e7199
ms.sourcegitcommit: b8f8b7631aabaab28e9705934bf67dad15e3a179
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101800726"
---
# <a name="enterprise-scale-business-continuity-and-disaster-recovery-for-azure-kubernetes-service"></a>Azure Kubernetes Service に対するエンタープライズ規模の事業継続とディザスター リカバリー

組織は、その特定要件を満たすために、適切な Azure Kubernetes Service (AKS) プラットフォームレベルの機能を設計する必要があります。 これらのアプリケーション サービスには、目標復旧時間 (RTO) と回復ポイントの目標 (RPO) に関連する要件があります。 AKS ディザスター リカバリーには、対処すべき複数の考慮事項があります。 最初の手順で、インフラストラクチャとアプリケーションのサービス レベル アグリーメント (SLA) を定義します。 [Azure Kubernetes Service (AKS) の SLA](https://azure.microsoft.com/support/legal/sla/kubernetes-service/v1_1/) について学習します。 月間稼働率の計算の詳細については、**SLA の詳細** セクションを参照してください。

## <a name="design-considerations"></a>設計上の考慮事項

次の要因について検討します。

- AKS クラスターからノード プール内の複数のノードを使用して、アプリケーションの最小レベルの可用性を提供する必要があります。

- [ポッドの要求と制限](/azure/aks/developer-best-practices-resource-management#define-pod-resource-requests-and-limits)を設定します。 これらの制限を設定すると、Kubernetes から次のことができます。

  - ポッドに CPU およびメモリ リソースを効率的に提供します。

  - ノードのコンテナー密度を高くします。

  また、制限を設定することで、ハードウェアをうまく利用できるようになるため、コストを削減しながら信頼性を高めることができます。

- AKS の [Availability Zones](/azure/aks/availability-zones) または可用性セットに対する適合性。

  - 可用性ゾーンをサポートしているリージョンを選択します。

  - Availability Zones は、ノード プールの作成時にのみ設定でき、後で変更することはできません。 複数ゾーンのサポートは、ノード プールにのみ適用されます。

  - ゾーンを完全に活用するには、すべてのサービス依存関係でもゾーンがサポートされている必要があります。 依存サービスでゾーンがサポートされていない場合、ゾーンの障害によってそのサービスが失敗する可能性があります。

  - Availability Zones が達成できる以上の高可用性を実現するには、複数の AKS クラスターを異なるペアのリージョンで実行します。 Azure リソースで geo 冗長がサポートされている場合は、冗長サービスのセカンダリ リージョンが維持される場所を指定します。

- AKS の[ディザスター リカバリーに関するガイドライン](/azure/aks/operator-best-practices-multi-region)に注意する必要があります。 次に、それらが Azure Dev Spaces に使用する AKS クラスターに適用されるかどうかを検討します。

- アプリケーションとデータのバックアップを一貫して作成します。

  - ステートフルでないサービスは、効率的にレプリケートできます。

  - クラスター内に状態を格納する必要がある場合 (推奨されません)、ペアになっているリージョンで頻繁にデータをバックアップしてください。

- クラスターの更新とメンテナンス。

  - クラスターを常に最新の状態に保ちます。

  - リリースと非推奨化プロセスに注意します。

  - 更新とメンテナンスを事前に計画します。

- フェールオーバーが発生した場合のネットワーク接続。

  - 要件に応じて、ゾーンまたはリージョン間でトラフィックを分散できるトラフィック ルーターを選択します。 Web 以外のトラフィックをゾーン間で分散できるため、このアーキテクチャを使用して [Azure Load Balancer](/azure/load-balancer/load-balancer-overview) をデプロイします。

  - リージョン間でトラフィックを分散する必要がある場合は、[Azure Front Door](/azure/frontdoor/front-door-overview) の使用を検討してください。

- 計画的および計画外のフェールオーバー。

  - 各 Azure サービスを設定するときに、ディザスター リカバリーをサポートする機能を選択します。 たとえば、このアーキテクチャでは、geo レプリケーションに対して [Azure Container Registry](/azure/container-registry/container-registry-intro) を有効にします。 リージョンがダウンした場合でも、レプリケートされたリージョンから引き続きイメージをプルできます。

- サービスレベル目標を達成するために DevOps のエンジニアリング能力を維持します。

- Kubernetes API サーバー エンドポイントに対して、[利用料金に基づく SLA](/azure/aks/uptime-sla) が必要かどうかを判断します。

## <a name="design-recommendations"></a>設計の推奨事項

設計のベスト プラクティスを次に示します。

- システム ノード プールに 3 つのノードを使用します。 ユーザー ノード プールの場合、2 つ以上のノードから開始します。 より高い可用性が必要な場合は、さらに多くのノードを設定します。

- アプリケーションを別のノード プールに配置してシステム サービスから分離します。 このように、Kubernetes サービスは専用ノードで実行され、他のサービスと競合しません。 ワークロードをスケジューリングするノード プールを識別するために、[タグ、ラベル、テイント](/azure/aks/use-multiple-node-pools#specify-a-taint-label-or-tag-for-a-node-pool)を使用します。

- 信頼性を確保するには、クラスターの定期的な保守 (タイムリーな更新を行うなど) が不可欠です。 [AKS でサポートされている Kubernetes バージョンの対象期間](/azure/aks/supported-kubernetes-versions)に注意し、更新を事前に計画します。 また、ポッドの正常性をプローブを介して監視することをお勧めします。

- 可能な限り、[サービスの状態をコンテナー内から削除します](/azure/aks/operator-best-practices-multi-region#remove-service-state-from-inside-containers)。 代わりに、マルチリージョン レプリケーションをサポートする Azure のサービスとしてのプラットフォーム (PaaS) を使用します。

- ポッド リソースを確保する。 デプロイにポッド リソースの要件を指定することを強くお勧めします。 そうすることで、スケジューラでポッドを適切にスケジューリングできます。 ポッドがスケジュールされていない場合、信頼性が大幅に低下します。

- ハードウェア障害などの中断に対処するために、デプロイに複数のレプリカを設定します。 更新やアップグレードなどの計画されたイベントに備えて、中断バジェットを使用して、予想されるアプリケーション負荷を処理するために必要な数のポッド レプリカを確保できます。

- お使いのアプリケーションのデータに [Azure Storage](/azure/aks/operator-best-practices-multi-region#create-a-storage-migration-plan) が使用されている場合があります。 アプリケーションは異なるリージョンの複数の AKS クラスターに分散しているため、ストレージの同期を保つ必要があります。 ストレージをレプリケートする 2 つの一般的な方法は次のとおりです。

  - インフラストラクチャベースの非同期レプリケーション

  - アプリケーションベースの非同期レプリケーション

- [ポッドの制限](/azure/aks/quotas-skus-regions#service-quotas-and-limits)を見積もります。 ベースラインをテストして確定します。 要求と制限に等しい値を使用して開始します。 次に、それらの値を徐々に調整しながら、クラスターを不安定にするおそれがあるしきい値を確定します。 ポッドの制限は、配置マニフェストに指定できます。

  組み込み機能として、サービス アーキテクチャの障害や中断を処理する複雑なタスクに対するソリューションが提供されています。 これらの構成は、設計とデプロイ自動化の両方を簡略化するのに役立ちます。 SLA、RTO、および RPO の標準を定義しておくことで、組織は Kubernetes と Azure への組み込みサービスを使用して、ビジネス目標を達成できます。

- [ポッド中断バジェット](/azure/aks/operator-best-practices-scheduler#plan-for-availability-using-pod-disruption-budgets)を設定します。 この設定により、デプロイ内で、更新またはアップグレード イベント中に削除できるレプリカの数がチェックされます。

- サービス名前空間で[リソース クォータを適用します](/azure/aks/operator-best-practices-scheduler#enforce-resource-quotas)。 名前空間のリソース クォータによって、デプロイにポッドの要求と制限が適切に設定されます。

  - クラスター レベルでリソース クォータを設定すると、適切な要求と制限のないパートナー サービスをデプロイするときに問題が発生するおそれがあります。

- 最新バージョンの[`kube-advisor`オープンソース ツール](/azure/aks/operator-best-practices-scheduler#regularly-check-for-cluster-issues-with-kube-advisor)を定期的に実行して、クラスター内の問題を検出します。

- コンテナー イメージを [Azure Container Registry](/azure/aks/operator-best-practices-multi-region#enable-geo-replication-for-container-images) に格納し、レジストリを各 AKS リージョンに geo レプリケーションします。

- AKS は無料のサービスとして使用できますが、そのレベルでは、金銭的な補償を伴う SLA は提供されません。 その SLA を取得するには、購入したものにアップタイム SLA を追加する必要があります。 すべての運用クラスターでこのオプションを使用することをお勧めします。 このオプションを使用していないクラスターを運用前のクラスター用に用意します。 Availability Zones と組み合わせると、Kubernetes API サーバーの SLA は 99.95% に向上します。 ノード プールとその他のリソースは、それぞれ専用の SLA の対象になっています。

- [ExpressRoute](/azure/expressroute/expressroute-introduction) 接続には、複数のリージョンとピアリングの場所を使用します。

  Azure リージョンまたはピアリング プロバイダーの場所に影響する停止が発生した場合に、ハイブリッド ネットワーク アーキテクチャが冗長になっていると、中断のないクロスプレミス接続を確保できます。

- [グローバル仮想ネットワーク ピアリング](/azure/aks/operator-best-practices-multi-region#interconnect-regions-with-global-virtual-network-peering)を使用してリージョンを相互接続します。 クラスターが相互に通信する必要がある場合、両方の仮想ネットワークを相互に接続するには、仮想ネットワーク ピアリングを使用して実現できます。 このテクノロジは、異なる地理的リージョンにまたがっていても、Microsoft のバックボーン ネットワーク全体で高帯域幅を提供して仮想ネットワークを相互に接続します。

- Azure Front Door から[スプリット TCP ベースのエニーキャスト プロトコル](/azure/aks/operator-best-practices-multi-region#application-routing-with-azure-front-door-service)を使用して、エンド ユーザーが Front Door の最も近いポイント オブ プレゼンスにすぐに接続されるように保証します。 Azure Front Door のその他の機能は次のとおりです。

  - TLS 終了

  - カスタム ドメイン

  - Web アプリケーション ファイアウォール

  - URL 書き換え

  - セッション アフィニティ

  アプリケーション トラフィックのニーズを確認して、どのソリューションが最も適切かを検討してください。
