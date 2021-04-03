---
title: Azure ランディング ゾーンの最新のコンテナーの確認
description: シナリオの Azure ランディング ゾーン設計に与える影響について説明する
author: BrianBlanchard
ms.author: brblanch
ms.date: 03/01/2021
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.custom: think-tank, e2e-aks
ms.openlocfilehash: a4a277ab11c719d55b0cf8e4fc584c64053b9209
ms.sourcegitcommit: 5716a8165934bd69d02d9d3641785039196aee3a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "105733840"
---
# <a name="azure-landing-zone-review-for-modern-containers"></a>Azure ランディング ゾーンの最新のコンテナーの確認

[クラウド導入フレームワークの準備方法](../../ready/index.md)は、すべての Azure 環境を [Azure ランディング ゾーン](../../ready/landing-zone/index.md)を使用して作成する方法について説明しています。 Azure ランディング ゾーンにより、一連の[共通設計領域](../../ready/landing-zone/design-areas.md)を中心にして構築される[実装オプション](../../ready/landing-zone/implementation-options.md)が数多く提供されます。

Azure ランディング ゾーンを使用すると、小規模な実装から始めて、時間をかけて拡張することができます。 より高度な環境では、エンタープライズ規模の実装オプションから始めることができます。 どの実装オプションを選択する場合でも、最新のコンテナー ソリューションに使用するランディング ゾーンを評価する必要があります。

## <a name="environmental-considerations-for-orchestrated-containers"></a>オーケストレーションされるコンテナーの環境に関する考慮事項

Azure ランディング ゾーンの実装方法をまだ選択していない場合は、[Azure ランディング ゾーン](../../ready/landing-zone/index.md)の記事シリーズを確認します。 次に、そのランディング ゾーン オプションを最新のコンテナーシナリオにどのように合わせるのが最適なのかを確認します。

**小規模で開始するオプション:** Azure Kubernetes Service (AKS) を使用したコンテナー オーケストレーションには、いくつかの環境構成が必要です。 [Azure ランディング ゾーン用の Azure Kubernetes Service (AKS) クラスターのベースライン アーキテクチャ](/azure/architecture/reference-architectures/containers/aks/secure-baseline-aks?toc=/azure/cloud-adoption-framework/toc.json&bc=/azure/cloud-adoption-framework/_bread/toc.json)では、ネットワーク、コンピューティング、ID、セキュリティ、BCDR、運用に関する考慮事項が提供されています。

**エンタープライズ規模のオプション:** AKS のエンタープライズ規模の構築セットには、AKS プラットフォームをサポートするために、エンタープライズ規模のランディング ゾーンをデプロイするための参照実装が含まれています。 構築セットのドキュメントに含まれている一連のベスト プラクティスは、AKS 運用環境の準備状況を評価するために使用できます。 これらの考慮事項は、小規模で開始するランディング ゾーン実装を選択した場合にも適用されます。 AKS 環境を評価するには、次の記事を参照してください。

- [ID 管理とアクセス管理](eslz-identity-and-access-management.md)
- [ネットワーク トポロジと接続](eslz-network-topology-and-connectivity.md)
- [管理と監視](eslz-management-and-monitoring.md)
- [事業継続とディザスター リカバリー](eslz-business-continuity-and-disaster-recovery.md)
- [セキュリティ、ガバナンス、コンプライアンス](eslz-security-governance-and-compliance.md)
- [プラットフォームの自動化と DevOps](eslz-platform-automation-and-devops.md)

上記の 2 つのオプションは、Azure リソース、サブスクリプション トポロジ、ガバナンスのための Azure Policy の使用に関して、職務分離の表現と実装の方法が大きく異なります。 組織の計画について、集中型と分散型の運用の違いと、ご自分の組織のワークロードにはどちらが最適であるかを把握します。 どちらのモデルも、組織とワークロードが必要とするエクスペリエンスが正確に提供されるように柔軟に対応できますが、定義した戦略に最も近い方を使って開始することをお勧めします。 すべてのワークロード チームが、すべての IT グループとメンバーに必要な運用モデルと職務を確実に理解するようにします。

## <a name="environmental-considerations-for-non-orchestrated-container-solutions"></a>オーケストレーションされないコンテナー ソリューションの環境に関する考慮事項

次のコンテナー サービスは、Platform as a Service ソリューションとして実行され、必要な環境構成が少なくて済みます。 ただし、構成要件が減ると、コンテナー オーケストレーションとソリューション固有の構成に対する制御が減り、ワークロードが VM や他のコンテナーなどの他の資産に統合されます。 これらのオーケストレーションされないソリューションは、ワークロード中心の運用戦略に役立つ傾向があります。

以下の各製品ドキュメント リンクで概念と攻略ガイドを確認して、オーケストレーションされないコンテナーの種類に対するさまざまな種類の環境構成を評価します。

- [App Service](/azure/app-service/)
- [Azure Functions](/azure/azure-functions/functions-overview)
- [Container Instances](/azure/container-instances/container-instances-overview)
- [Batch](/azure/batch/batch-technical-overview)

## <a name="next-step-migrate-workload-to-modern-containers"></a>次のステップ: ワークロードを最新のコンテナーに移行する

次の一連の記事では、クラウド導入作業において、クラウド導入シナリオの成功に役立つ特定のポイントについてのガイダンスが説明されています。

- [最新のコンテナーへのワークロードの移行](./migrate.md)
- [最新のコンテナー ソリューションを使用したイノベーション](/azure/architecture/reference-architectures/containers/aks-start-here?toc=/azure/cloud-adoption-framework/toc.json&bc=/azure/cloud-adoption-framework/_bread/toc.json)
- [最新のコンテナー ソリューションのガバナンス](./govern.md)
- [最新のコンテナー ソリューションの管理](./manage.md)
