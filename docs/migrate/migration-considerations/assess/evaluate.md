---
title: ワークロードの準備状況を評価する
description: クラウドに移行するワークロードの準備状況を評価するために何が必要かを説明します。 すべての資産とそれに関連する依存関係を検証する方法について見ていきましょう。
author: BrianBlanchard
ms.author: brblanch
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: migrate
ms.openlocfilehash: ddeea58c9e50327f318640160317556191c26d7f
ms.sourcegitcommit: 60d8b863d431b5d7c005f2f14488620b6c4c49be
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2020
ms.locfileid: "83216148"
---
# <a name="evaluate-workload-readiness"></a>ワークロードの準備状況を評価する

このアクティビティは、クラウドに移行するワークロードの準備状況の評価に重点を置いています。 このアクティビティ中に、クラウド導入チームは、すべての資産および関連付けられた依存関係が、選択されたデプロイ モデルおよびクラウド プロバイダーと互換性があることを検証します。 このプロセス中に、チームは、互換性の問題を[修復する](../migrate/remediate.md)ために必要なすべての作業を文書化します。

## <a name="evaluation-assumptions"></a>評価の前提条件

クラウド導入フレームワークでの原則を説明しているほとんどの内容は、クラウドに依存しません。 ただし、準備状況評価のプロセスは、特定の各クラウド プラットフォームに大きく依存します。 次のガイダンスでは、Azure に移行する意図を前提にしています。 また、[レプリケーション アクティビティ](../migrate/replicate.md)のための Azure Migrate (Azure Site Recovery とも呼ばれます) の使用も前提にしています。 代わりのツールについては、「[レプリケーション オプション](../migrate/replicate-options.md)」を参照してください。

この記事では、可能性のあるすべての評価アクティビティをキャプチャすることはありません。 ここでは、各環境およびビジネス成果によって特定の要件が規定されると仮定しています。 これらの要件の作成の高速化に役立つように、この記事の残りでは、[インフラストラクチャ](#common-infrastructure-evaluation-activities)、[データベース](#common-database-evaluation-activities)、および[ネットワーク](#common-network-evaluation-activities)評価に関連したいくつかの一般的な評価アクティビティを共有します。

## <a name="common-infrastructure-evaluation-activities"></a>一般的なインフラストラクチャ評価アクティビティ

- VMware の要件: [VMware に対する Azure Site Recovery の要件を確認します](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix)。
- Hyper-V の要件: [Hyper-V に対する Azure Site Recovery の要件を確認します](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-support-matrix)。

ホスト構成、レプリケートされた VM 構成、ストレージ要件、またはネットワーク構成での不一致をすべて文書化するようにしてください。

## <a name="common-database-evaluation-activities"></a>一般的なデータベース評価アクティビティ

- 現在のデータベース デプロイの目標復旧時点 (RPO) と目標復旧時間 (RTO) を文書化します。 これらは、意思決定を支援するために[アーキテクチャ アクティビティ](./architect.md)で使用されます。
- 高可用性構成の要件をすべて文書化します。 SQL Server の要件を理解するには、[SQL Server 高可用性ソリューション ガイド](https://docs.microsoft.com/sql/sql-server/failover-clusters/high-availability-solutions-sql-server)に関するページを参照してください。
- PaaS 互換性を評価します。 [Azure データ移行ガイド](https://datamigration.microsoft.com)では、オンプレミス データベースが [MySQL](https://docs.microsoft.com/azure/mysql)、[PostgreSQL](https://docs.microsoft.com/azure/postgresql)、または [MariaDB](https://docs.microsoft.com/azure/mariadb) 用の [Cosmos DB](https://docs.microsoft.com/azure/cosmos-db) や [Azure DB](https://docs.microsoft.com/azure/sql-database) などの互換性のある Azure PaaS ソリューションにマップされています。
- PaaS 互換性がオプションであり、修復の必要がない場合は、[アーキテクチャ アクティビティ](./architect.md)を担当するチームに問い合わせてください。 PaaS 移行によって、大幅な時間短縮と、ほとんどのクラウド ソリューションの総保有コスト (TCO) の削減が生み出される可能性があります。
- PaaS 互換性がオプションであるが、修復が必要な場合は、[アーキテクチャ アクティビティ](./architect.md)と[修復アクティビティ](../migrate/remediate.md)を担当するチームに問い合わせてください。 多くのシナリオでは、修復時間の増加よりデータベース ソリューションの PaaS 移行の利点の方が上回ります。
- 移行されるデータベースごとに変更のサイズと比率を文書化します。
- 可能な場合は、各データベースへの呼び出しを行うアプリケーションまたはその他の資産をすべて文書化します。

> [!NOTE]
> 資産を同期すると、レプリケーション プロセス中に帯域幅が消費されます。 非常に一般的な落とし穴は、資産をレプリケーションの時点とリリースの間で同期された状態に維持するために必要な帯域幅の消費を見落とすことです。 データベースはリリース サイクル中の帯域幅の一般的なコンシューマーであり、ストレージ フットプリントの大きな、または変更の比率の高いデータベースが特に問題になります。 ユーザー受け入れテスト (UAT) とリリースの前に、制御された更新を使用してデータ構造をレプリケートするアプローチを検討してください。 このようなシナリオでは、Azure Site Recovery に代わる方法の方がより適切である可能性があります。 詳しくは、「[Azure Database 移行ガイド](https://datamigration.microsoft.com)」をご覧ください。

## <a name="common-network-evaluation-activities"></a>一般的なネットワーク評価アクティビティ

- リリースまでのイテレーション中にレプリケートされるすべての VM の合計ストレージを計算します。
- リリースまでのイテレーション中にレプリケートされるすべての VM のストレージのドリフトまたは変更の比率を計算します。
- ストレージとドリフトの合計を加算することによって、各イテレーションに必要な帯域幅要件を計算します。
- イテレーションの配置ごとに検証するために現在のネットワークで使用可能な未使用の帯域幅を計算します。
- 予測される移行速度に達するために必要な帯域幅を文書化します。 必要な帯域幅を提供するために何からの修復が必要な場合は、[修復アクティビティ](../migrate/remediate.md)を担当するチームに通知します。

> [!NOTE]
> 合計ストレージは、初期レプリケーション中の帯域幅要件に直接影響を与えます。 ただし、ストレージ ドリフトはレプリケーションの時点からリリースまで続きます。 つまり、ドリフトには使用可能な帯域幅への累積効果があります。

## <a name="next-steps"></a>次のステップ

システムの評価が完了すると、出力により、新しい[クラウド アーキテクチャ](./architect.md)の開発が可能になります。

> [!div class="nextstepaction"]
> [移行の前のアーキテクト ワークロード](./architect.md)
