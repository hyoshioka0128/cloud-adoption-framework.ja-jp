---
title: ネットワーク容量の超過
titleSuffix: Microsoft Cloud Adoption Framework for Azure
description: ストレージ要件が移行作業中にネットワーク容量を超えています。
author: BrianBlanchard
ms.author: brblanch
ms.date: 04/04/2019
ms.topic: guide
ms.service: cloud-adoption-framework
ms.subservice: migrate
ms.openlocfilehash: eb6a5adeac25293539edd5d97c816fad2865345c
ms.sourcegitcommit: a26c27ed72ac89198231ec4b11917a20d03bd222
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/06/2019
ms.locfileid: "70836705"
---
# <a name="storage-requirements-exceed-network-capacity-during-a-migration-effort"></a>ストレージ要件が移行作業中にネットワーク容量を超えている

クラウド移行では、資産がレプリケートされ、既存のデータセンターとクラウド間でネットワーク経由で同期されます。 さまざまなワークロードの既存のデータ サイズ要件が、ネットワーク容量を超えることは珍しくありません。 このようなシナリオでは、移行プロセスはきわめて低速になったり、場合によっては完全に停止することがあります。 次のガイダンスは、ネットワーク制限に対処するソリューションを提供する [Azure 移行ガイド](../azure-migration-guide/index.md)の範囲を拡大します。

## <a name="general-scope-expansion"></a>全般的な範囲の拡大

この範囲の拡大によって要求される作業の大半は、移行の前提条件、アクセス、および移行プロセス中に発生します。

## <a name="suggested-prerequisites"></a>推奨される前提条件

**ネットワーク容量のリスクの検証:** [デジタル資産の合理化](../../digital-estate/rationalize.md)は、特に使用可能なネットワーク容量への過負荷に関する問題がある場合に、強く推奨される前提条件です。 デジタル資産の合理化中に、[デジタル資産のインベントリ](../../digital-estate/inventory.md)が収集されます。 そのインベントリには、デジタル資産全体の既存のストレージ要件を含める必要があります。 「[レプリケーションのリスク: レプリケーションの物理的特性](../migration-considerations/migrate/replicate.md#replication-risks---physics-of-replication)」で説明しているように、そのインベントリは、**移行データの合計サイズ**を評価するために使用できます。これは、**利用可能な移行帯域幅**の合計と比較できます。 その比較が**ビジネスの変更までの必要な時間**に整合しない場合、この記事は、移行速度を速めて、データセンターを移行するために必要な時間を短縮する場合に役立ちます。

**独立したデータ ストアのオフライン転送:** Azure Data Box とのオンラインおよびオフラインの両方のデータ転送の例が、次の図に示されています。 これらのアプローチは、ワークロードの移行の前にクラウドへ大量のデータを配布するために使用できます。 オフラインのデータ転送では、ソース データは Azure Data Box にコピーされ、続いて Azure Data Box が、Azure ストレージ アカウントにファイルまたは BLOB として転送するために物理的に Microsoft に配布されます。 このプロセスは、その他の移行作業の前に、特定のワークロードに直接関連付けられていないデータを配布するために使用できます。 そのようにすることで、ネットワーク上の制約内での移行を完了するために、ネットワーク経由で配布する必要のあるデータの量が減少します。

このアプローチは、データ HDFS、バックアップ、アーカイブ、ファイル サーバー、アプリケーションなどの転送に使用されることがあります。 既存の技術的なガイダンスでは、このアプローチを使用して、[HDFS ストア](/azure/storage/blobs/data-lake-storage-migrate-on-premises-hdfs-cluster)から、または [SMB](/azure/databox/data-box-deploy-copy-data)、[NFS](/azure/databox/data-box-deploy-copy-data-via-nfs)、[REST](/azure/databox/data-box-deploy-copy-data-via-rest)、[データ コピー サービス](/azure/databox/data-box-deploy-copy-data-via-copy-service)を使用したディスクから Data Box にデータを転送する方法について説明します。

大量のデータがオフライン転送を介して移動されるが、後からネットワーク経由でより低いスケールで同期される「シードおよびフィード」移行に Azure Data Box を使用する[サード パーティ パートナー ソリューション](https://azuremarketplace.microsoft.com/campaigns/databox/azure-data-box)もあります。

![Azure Data Box でのオフラインおよびオンラインのデータ転送](../../_images/migration/databox.png)

## <a name="assess-process-changes"></a>プロセス変更の評価

1 つまたは複数のワークロードのストレージ要件がネットワーク容量を超えた場合、Azure Data Box はまだオンラインのデータ転送で使用できます。

一般的な Microsoft の立場は、ネットワークが利用できない場合を除き、ネットワーク転送が推奨の方法であるというものです。 この推奨事項は、転送速度によるものです。 一般に、ネットワーク経由でのデータ転送は (帯域幅が制限される場合でも)、Data Box などのオフライン転送メカニズムを使用して同量のデータを物理的に配布する場合よりも高速になります。

Azure への接続が利用できる場合、特にワークロードの移行が時間の制約があるときに、Data Box を使用する前に分析を行う必要があります。 Data Box は、必要なデータを転送する時間が、Data Box を使用してデータを入力、配布、および復元する時間を超える場合にのみお勧めします。

### <a name="suggested-action-during-the-assess-process"></a>評価プロセス中に推奨されるアクション

**ネットワーク容量の分析:** ワークロードに関連するデータ転送の要件は、ネットワーク容量を超える危険があるとき、クラウド導入チームは、ネットワーク容量分析と呼ばれる評価プロセスにさらなる分析タスクを追加します。 この分析中に、ローカル ネットワークとネットワーク接続に関する専門知識を持つチームのメンバーは、使用可能なネットワーク容量と必要なデータ転送時間の量を推定します。 その利用可能な容量は、現在のリリース中に移行するすべての資産のストレージ要件と比較されます。 ストレージ要件が使用可能な帯域幅を超えた場合、ワークロードをサポートする資産はオフライン転送に選択されます。

> [!IMPORTANT]
> 分析の最後に、オフラインで転送する資産の配布、復元、および同期に必要な時間を反映するように、リリース計画を更新する必要があります。

**ずれの分析:** オフラインで転送される各資産は、ストレージおよび構成のずれについて分析する必要があります。 ストレージのずれは、時間の経過での基盤となるストレージの変化量です。 構成のずれは、時間の経過での資産の構成における変化です。 ストレージがコピーされた時点から、資産が運用環境に昇格される時点まで、すべてのずれが失われる可能性があります。 移行した資産でそのずれを反映する必要がある場合、ローカル資産と移行した資産の間で、一部の形式の同期が必要になります。 これには、移行の実行中の考慮事項に関するフラグを設定する必要があります。

## <a name="migrate-process-changes"></a>移行プロセスの変更

オフラインの転送メカニズムを使用する場合、[レプリケーション プロセス](../migration-considerations/migrate/replicate.md)はおそらく必要ではありません。 ただし、[同期プロセス](../migration-considerations/migrate/replicate.md)はまだ要件になっていることがあります。 評価プロセス中に完了したずれの分析結果を理解すると、資産がオフラインで転送されている場合、移行中に必要となるタスクがわかります。

### <a name="suggested-action-during-the-migrate-process"></a>移行プロセス中に推奨されるアクション

**ストレージのコピー:** このアプローチは、データ HDFS、バックアップ、アーカイブ、ファイル サーバー、アプリケーションなどの転送に使用されることがあります。 既存の技術的なガイダンスでは、このアプローチを使用して、[HDFS ストア](/azure/storage/blobs/data-lake-storage-migrate-on-premises-hdfs-cluster)から、または [SMB](/azure/databox/data-box-deploy-copy-data)、[NFS](/azure/databox/data-box-deploy-copy-data-via-nfs)、[REST](/azure/databox/data-box-deploy-copy-data-via-rest)、[データ コピー サービス](/azure/databox/data-box-deploy-copy-data-via-copy-service)を使用したディスクから Data Box にデータを転送する方法について説明します。

大量のデータがオフライン転送を介して移動されるが、後からネットワーク経由でより低いスケールで同期される「シードおよび同期」移行に Azure Data Box を使用する[サード パーティ パートナー ソリューション](https://azuremarketplace.microsoft.com/campaigns/databox/azure-data-box)もあります。

**デバイスの配布:** データをコピーすると、デバイスは [Microsoft に配布](/azure/databox/data-box-deploy-picked-up)できます。 受信およびインポートされた後、データは Azure ストレージ アカウントで使用できます。

**資産の復元:** [データの検証](/azure/databox/data-box-deploy-picked-up#verify-data-upload-to-azure)はストレージ アカウントで使用できます。 検証されると、データは、BLOB として、または Azure Files で使用できます。 データが VHD/VHDX ファイルの場合、ファイルをマネージド ディスクに変換できます。 これらのマネージド ディスクは続いて、仮想マシンをインスタンス化するために使用でき、この仮想マシンが元のオンプレミス資産のレプリカを作成します。

**同期:** ずれの同期が移行した資産の要件である場合、資産が復元されるまで、ファイルの同期にいずれかの[サードパーティ パートナー ソリューション](https://azuremarketplace.microsoft.com/campaigns/databox/azure-data-box)を使用できます。

## <a name="optimize-and-promote-process-changes"></a>最適化および昇格プロセスの変更

最適化アクティビティはおそらく、スコープ内のこの変更の影響を受けません。

## <a name="secure-and-manage-process-changes"></a>セキュリティ保護および管理プロセスの変更

セキュリティ保護および管理アクティビティはおそらく、スコープ内のこの変更の影響を受けません。

## <a name="next-steps"></a>次の手順

[拡張スコープ チェックリスト](./index.md)に戻り、移行方法が完全に調整されていることを確認します。

> [!div class="nextstepaction"]
> [拡張スコープ チェックリスト](./index.md)
