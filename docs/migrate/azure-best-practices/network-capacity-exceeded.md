---
title: 移行作業中にデータ要件がネットワーク容量を超える場合のベスト プラクティス
description: 移行作業中にデータ要件がネットワーク容量を超える場合のベスト プラクティス
author: BrianBlanchard
ms.author: brblanch
ms.date: 07/01/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: migrate
ms.custom: internal
ms.openlocfilehash: 1a493c8624d1cbcd4fbf9d4c9a4a6fb559b5d4c1
ms.sourcegitcommit: 54f01dd0eafa23c532e54c821954ba682357f686
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/13/2021
ms.locfileid: "98174813"
---
<!-- cSpell:ignore HDFS databox VHDX -->

# <a name="best-practices-when-data-requirements-exceed-network-capacity-during-a-migration-effort"></a>移行作業中にデータ要件がネットワーク容量を超える場合のベスト プラクティス

クラウド移行では、資産をレプリケートし、既存のデータセンターとクラウド間でネットワーク経由で同期します。 さまざまなワークロードの既存のデータ サイズ要件が、ネットワーク容量を超えることは珍しくありません。 このようなシナリオでは、移行プロセスはきわめて低速になったり、場合によっては完全に停止することがあります。 次のガイダンスは、ネットワーク制限に対処するソリューションを提供する [Azure 移行ガイド](../azure-migration-guide/index.md)の範囲を拡大するものです。

## <a name="general-scope-expansion"></a>全般的な範囲の拡大

この範囲の拡大に必要な作業のほとんどは、移行の前提条件の計画、評価、および移行フェーズ中に発生します。

## <a name="suggested-prerequisites"></a>推奨される前提条件

**ネットワーク容量のリスクの検証:** [デジタル資産の合理化](../../digital-estate/rationalize.md)は、特に使用可能なネットワーク容量への過負荷に関する問題がある場合に、強く推奨される前提条件です。 デジタル資産の合理化中に、[デジタル資産のインベントリ](../../digital-estate/inventory.md)を収集します。 そのインベントリには、デジタル資産全体の既存のストレージ要件を含める必要があります。

「[レプリケーションのリスク - レプリケーションの物理的特性](../migration-considerations/migrate/replicate.md#replication-risks---physics-of-replication)」で説明しているように、そのインベントリは、移行データの合計サイズを評価するために使用できます。これは、利用可能な移行帯域幅の合計と比較できます。 その比較がビジネスの変更までの必要な時間に整合しない場合、この記事は、移行速度を速めて、データセンターを移行するために必要な時間を短縮する場合に役立ちます。

**独立したデータ ストアのオフライン転送:** 次の図は、Azure Data Box とのオンラインおよびオフラインの両方のデータ転送の例を示しています。 これらのアプローチは、ワークロードの移行の前にクラウドへ大量のデータを移送するために使用できます。 オフラインのデータ転送では、ソース データをコピーした Azure Data Box が、Azure Storage アカウントにファイルまたは BLOB として転送するために物理的に Microsoft に発送されます。 他の移行作業の前に、このプロセスを使用して、特定のワークロードに直接関連付けられていないデータを移送できます。 こうすることで、ネットワーク経由で移送する必要のあるデータの量が減少し、ネットワークの制約内での移行の完了がサポートされます。

このアプローチは、HDFS、バックアップ、アーカイブ、ファイル サーバー、アプリケーションなどからのデータ転送に使用することができます。 既存の技術的なガイダンスでは、このアプローチを使用して、[HDFS ストア](/azure/storage/blobs/data-lake-storage-migrate-on-premises-hdfs-cluster)から、または [SMB](/azure/databox/data-box-deploy-copy-data)、[NFS](/azure/databox/data-box-deploy-copy-data-via-nfs)、[REST](/azure/databox/data-box-deploy-copy-data-via-rest)、[データ コピー サービス](/azure/databox/data-box-deploy-copy-data-via-copy-service)を使用してディスクから Data Box にデータを転送する方法について説明しています。

また、移行のために Azure Data Box を使用するサードパーティのパートナー ソリューションもあります。 これらのソリューションでは、大量のデータをオフライン転送を介して移動しますが、後からネットワーク経由でより低いスケールで同期します。

![Azure Data Box でのオフラインおよびオンラインのデータ転送を示す図。](../../_images/migrate/data-box.png)

## <a name="assess-process-changes"></a>プロセス変更の評価

1 つまたは複数のワークロードのストレージ要件がネットワーク容量を超えている場合でも、オンラインのデータ転送で Azure Data Box を使用できます。

ネットワークが使用できない場合を除き、ネットワーク転送が推奨される方法です。 一般に、ネットワーク経由でのデータ転送の速度は、帯域幅が制限されている場合でも、オフライン転送メカニズムを使用してデータを物理的に移送するよりも高速です。

Azure への接続が利用できる場合、特にワークロードの移行に時間の制約があるときは、Data Box を使用する前に分析を行う必要があります。 Data Box は、必要なデータを転送する時間が、データを入力、移送、および復元する時間を超える場合にのみお勧めします。

### <a name="suggested-action-during-the-assess-process"></a>評価プロセス中に推奨されるアクション

**ネットワーク容量の分析:** ワークロードに関連するデータ転送の要件がネットワーク容量を超える危険があるとき、クラウド導入チームは、ネットワーク容量分析と呼ばれる追加の分析タスクを評価プロセスに加えます。 この分析中に、そのチームのメンバーが、使用可能なネットワーク容量と必要なデータ転送時間を見積もります。 このチーム メンバーは、ローカル ネットワークとネットワーク接続に関する専門知識を持っている必要があることにご注意ください。

利用可能な容量と、現在のリリース中に移行するすべての資産のストレージ要件を比較します。 ストレージ要件が使用可能な帯域幅を超えた場合、ワークロードをサポートする資産はオフライン転送の対象として選択されます。

> [!IMPORTANT]
> 分析の最後に、オフラインで転送する資産の移送、復元、および同期に必要な時間を反映するように、リリース計画を更新することが必要な場合があります。

**ずれの分析:** オフラインで転送される各資産を、ストレージおよび構成のずれについて分析します。 "_ストレージのずれ_" は、時間の経過に伴う基盤となるストレージの変化の量です。 "_構成のずれ_" は、時間の経過に伴う資産の構成における変化です。 ストレージがコピーされた時点から、資産が運用環境に昇格される時点までに、いずれかのずれが失われる可能性があります。 移行された資産にそのずれを反映する必要がある場合は、ローカル資産と移行された資産を同期する必要があります。 移行の実行中に考慮するためのフラグをこれに設定します。

## <a name="migration-process-changes"></a>移行プロセスの変更

オフライン転送メカニズムを使用している場合、[レプリケーション プロセス](../migration-considerations/migrate/replicate.md) は通常必要ありませんが、[同期プロセス](../migration-considerations/migrate/replicate.md)はまだ必要である場合があります。 資産がオフラインで転送されている場合、評価プロセスでのずれの分析結果を理解すると、移行中に必要となるタスクがわかります。

### <a name="suggested-action-during-the-migration-process"></a>移行プロセス中に推奨されるアクション

**ストレージのコピー:** このアプローチは、HDFS、バックアップ、アーカイブ、ファイル サーバー、またはアプリケーションのデータの転送に使用することができます。 既存の技術的なガイダンスでは、このアプローチを使用して、[HDFS ストア](/azure/storage/blobs/data-lake-storage-migrate-on-premises-hdfs-cluster)から、または [SMB](/azure/databox/data-box-deploy-copy-data)、[NFS](/azure/databox/data-box-deploy-copy-data-via-nfs)、[REST](/azure/databox/data-box-deploy-copy-data-via-rest)、[データ コピー サービス](/azure/databox/data-box-deploy-copy-data-via-copy-service)を使用してディスクから Data Box にデータを転送する方法について説明しています。

また、移行のために Azure Data Box を使用するサードパーティのパートナー ソリューションもあります。 これらのソリューションでは、大量のデータをオフライン転送を介して移動しますが、後からネットワーク経由でより低いスケールで同期します。

**デバイスの配布:** データをコピーした後、[デバイスを Microsoft に発送する](/azure/databox/data-box-deploy-picked-up)ことができます。 データは、受信およびインポートされた後、Azure Storage アカウントで使用できます。

**資産の復元:** ストレージ アカウントで [データが使用できることを確認](/azure/databox/data-box-deploy-picked-up#verify-data-upload-to-azure)します。 そうであれば、データを BLOB として、または Azure Files で使用できます。 データが VHD または VHDX ファイルの場合は、ファイルをマネージド ディスクに変換できます。 これらのマネージド ディスクは続いて、仮想マシンをインスタンス化するために使用でき、この仮想マシンが元のオンプレミス資産のレプリカを作成します。

**同期:** ずれの同期が移行した資産の要件である場合、資産が復元されるまで、ファイルの同期にいずれかのサードパーティ パートナー ソリューションを使用できます。

## <a name="optimize-and-promote-process-changes"></a>最適化および昇格プロセスの変更

最適化アクティビティはおそらく、スコープ内のこの変更の影響を受けません。

## <a name="secure-and-manage-process-changes"></a>セキュリティ保護プロセスと管理プロセスの変更

セキュリティおよび管理のアクティビティはおそらく、スコープ内のこの変更の影響を受けません。

## <a name="next-steps"></a>次のステップ

チェックリストに戻り、移行方法が完全に調整されていることを確認します。

> [!div class="nextstepaction"]
> [移行のベスト プラクティスのチェックリスト](./index.md)
