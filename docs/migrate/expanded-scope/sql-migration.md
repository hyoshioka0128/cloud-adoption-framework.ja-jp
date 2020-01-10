---
title: SQL Server のインスタンスを移行して移行を高速化する
titleSuffix: Microsoft Cloud Adoption Framework for Azure
description: SQL Server インスタンス全体を移行すると、ワークロードの移行作業を高速化できます。
author: BrianBlanchard
ms.author: brblanch
ms.date: 10/10/2019
ms.topic: guide
ms.service: cloud-adoption-framework
ms.subservice: migrate
ms.openlocfilehash: e499e499cf1639bf9ce1118dcb93254268e9cb54
ms.sourcegitcommit: 3c325764ad8229b205d793593ff344dca3a0579b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/23/2019
ms.locfileid: "75328924"
---
# <a name="accelerate-migration-by-migrating-multiple-databases-or-entire-sql-servers"></a>複数のデータベースまたは SQL Server 全体を移行して移行を高速化する

SQL Server インスタンス全体を移行すると、ワークロードの移行作業を高速化できます。 以下のガイダンスでは、ワークロードに重点を置いた移行作業とは別に SQL Server のインスタンスを移行することによって、[Azure 移行ガイド](../azure-migration-guide/index.md)の範囲を拡大します。 この方法では、複数ワークロードの移行を促進する手段として、単一データプラットフォームの移行を実施することができます。 この範囲の拡大で必要となる作業の大半は、移行作業の前提条件、評価、移行、最適化のプロセスで発生します。

<!-- markdownlint-disable MD026 -->

## <a name="is-this-expanded-scope-right-for-you"></a>この拡大された範囲は現状に適しているか

[Azure 移行ガイド](../azure-migration-guide/index.md)で推奨されている方法では、各データ構造を、関連付けられたワークロードと共に 1 回の移行作業の一環として移行します。 この反復的な移行方法により、阻害要因を形成して事業価値のリターンを滞らせる可能性のあるタスク (検出や評価など) が軽減されます。

ただし、一部のデータ構造は、個別のデータプラットフォームの移行によってより効率的に移行できます。 以下に例を示します。

- **サービス終了:** より大きな移行作業で、SQL Server インスタンスを分離された反復としてすばやく移行すると、サービス終了の課題を回避できます。 このガイドは、広範な移行プロセスに SQL Server の移行を統合するのに役立ちます。 ただし、その他のクラウドの導入作業とは無関係に SQL Server を移行またはアップグレードする場合は、「[SQL Server サービス終了の概要](/sql/sql-server/end-of-support/sql-server-end-of-life-overview)」または [SQL Server 移行に関するドキュメント](/sql/sql-server/migrate/index)の方が、より明確なガイダンスを得られる場合があります。
- **SQL Server のサービス:** データ構造が、仮想マシン上で稼働している SQL Server を必要とする、より広範なソリューションの一部になっています。 これは、SQL Server Reporting Services、SQL Server Integration Services、SQL Server Analysis Services など、SQL Server のサービスを使用するソリューションでよく見られます。
- **高密度、低使用率のデータベース:** SQL Server のインスタンスでデータベースの密度が高くなっています。 その各データベースでは、トランザクションの量が少ないため、コンピューティング リソースをほとんど必要としません。 他の最新のソリューションを検討すべきではありますが、サービスとしてのインフラストラクチャ (IaaS) のアプローチは、運用コストの大幅な削減につながる可能性があります。
- **総保有コスト:** 該当する場合には、表示価格に [Azure ハイブリッド特典](https://azure.microsoft.com/pricing/hybrid-benefit)を適用して、SQL Server のインスタンスの保有コストを最小限に抑えることができます。 これは特に、マルチクラウドのシナリオで SQL Server をホストするお客様にとって一般的です。
- **移行アクセラレータ:** SQL Server インスタンスの "リフトアンドシフト" 移行では、1 回の反復で複数のデータベースを移動できます。 場合によっては、その後の反復で、特にアプリケーションと VM を中心にする、つまり、1 回の反復でより多くのワークロードを移行することができます。
- **VMware の移行:** 一般的なオンプレミス アーキテクチャでは、アプリケーションと VM が仮想ホストに、データベースがベア メタルに配置されます。 このシナリオでは、VMware ホストを Azure VMWare Service に移行するために、SQL Server インスタンス全体を移行することができます。 詳細については、[VMware ホストの移行](./vmware-host.md)に関するページを参照してください。

上記の条件がいずれもこの移行に当てはまらない場合は、[標準移行プロセス](../index.md)を続行することをお勧めします。 標準プロセスでは、データ構造が各ワークロードと一緒に反復的に移行されます。

このガイドが実際の条件と合致する場合は、[標準移行プロセス](../index.md)内の作業として、この拡大範囲ガイドを引き続き参照してください。 前提条件フェーズの間に、この作業を全体的な導入計画に統合することができます。

## <a name="suggested-prerequisites"></a>推奨される前提条件

SQL Server の移行を実施する前に、データ資産を追加してデジタル資産の拡大に着手します。 データ資産には、移行を検討しているデータ資産のインベントリが記録されます。 次の表に、データ資産の記録方法を示します。

### <a name="server-inventory"></a>サーバー インベントリ

サーバー インベントリの例を次に示します。

|SQL Server|目的|Version|[重要度](../../manage/considerations/criticality.md)|[機密度](../../govern/policy-compliance/data-classification.md)|データベース数|SSIS|SSRS|SSAS|クラスター|ノードの数|
|---------|---------|---------|---------|---------|---------|---------|---------|---------|---------|---------|
|sql-01|コア アプリ|2016|ミッション クリティカル|極秘|40|該当なし|該当なし|該当なし|はい|3|
|sql-02|コア アプリ|2016|ミッション クリティカル|極秘|40|該当なし|該当なし|該当なし|はい|3|
|sql-03|コア アプリ|2016|ミッション クリティカル|極秘|40|該当なし|該当なし|該当なし|はい|3|
|sql-04|BI|2012|高|XX|6|該当なし|機密|はい - 多次元キューブ|いいえ|1|
|sql-05|統合|2008 R2|低|全般|20|はい|該当なし|該当なし|いいえ|1|

### <a name="database-inventory"></a>データベース インベントリ

上記のサーバーの 1 つについてのデータベース インベントリの例を次に示します。

|サーバー|データベース|[重要度](../../manage/considerations/criticality.md)|[機密度](../../govern/policy-compliance/data-classification.md)|Data Migration Assistant (DMA) の結果|DMA の修復|ターゲット プラットフォーム|
|---------|---------|---------|---------|---------|---------|---------|
|sql-01|DB-1|ミッション クリティカル|極秘|互換性あり|該当なし|Azure SQL データベース|
|sql-01|DB-2|高|機密|スキーマの変更が必要|変更実施済み|Azure SQL データベース|
|sql-01|DB-3|高|全般|互換性あり|該当なし|Azure SQL マネージド インスタンス|
|sql-01|DB-4|低|極秘|スキーマの変更が必要|変更予定|Azure SQL マネージド インスタンス|
|sql-01|DB-5|ミッション クリティカル|全般|互換性あり|該当なし|Azure SQL マネージド インスタンス|
|sql-01|DB-6|高|機密|互換性あり|該当なし|Azure SQL データベース|

### <a name="integration-with-the-cloud-adoption-plan"></a>クラウド導入計画との統合

この検出プロセスは、完了後、[クラウド導入計画](../../plan/template.md)に含めることができます。 クラウド導入計画内では、移行対象となる各 SQL Server インスタンスを[個別のワークロード](../../plan/workloads.md)として追加します。 各ワークロード内では、データベースとサービス (SSIS、SSAS、SSRS) をそれぞれ、[資産](../../plan/workloads.md)として追加できます。 ワークロードと資産を導入計画に一括で追加する方法については、[Excel を使用した作業項目の追加と編集](https://docs.microsoft.com/azure/devops/boards/backlogs/office/bulk-add-modify-work-items-excel?view=azure-devops)に関するページを参照してください。

ワークロードと資産を計画に追加した後、作業者とそのチームは、導入計画を使用して標準移行プロセスを続行することができます。 導入チームが評価、移行、最適化のプロセスに進むときは、以下のセクションで説明する変更を考慮に入れます。

## <a name="assessment-process-changes"></a>評価プロセスの変更

計画内のデータベースを PaaS (サービスとしてのプラットフォーム) データ プラットフォームに移行できる場合は、選択したデータベースの互換性を DMA を使用して評価します。 データベースでスキーマ変換が必要な場合、移行パイプラインの中断を回避するために、評価プロセスの一環としてこの変換を実行する必要があります。

### <a name="suggested-action-during-the-assessment-process"></a>評価プロセス中に推奨されるアクション

PaaS ソリューションに移行できるデータベースについては、評価プロセス中に次のアクションを実行します。

- **DMA を使用して評価する:** Data Migration Assistant を使用して、移行先の Azure SQL Database マネージド インスタンス内のデータベース機能に影響を与える可能性がある互換性の問題を検出します。 DMA を使用して、パフォーマンスと信頼性の向上に関する推奨事項を確認し、スキーマ、データ、非コンテナー化オブジェクトをソース サーバーからターゲット サーバーに移動します。 詳細については、[Data Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview) に関するページを参照してください。
- **修復と変換:** DMA の出力内容に基づいて、ソース データ スキーマを変換し、互換性の問題を修復します。 変換されたデータ スキーマは、依存するアプリケーションを使用してテストします。

## <a name="migrate-process-changes"></a>移行プロセスの変更

移行中は、さまざまなツールやアプローチの中から選択できます。 ただし、それぞれの手法が踏襲するプロセスはシンプルです。つまりスキーマ、データ、オブジェクトの移行です。 その後、ターゲット データ ソースにデータを同期させます。

データ構造とサービスのターゲットとソースによって、この 2 つのステップがかなり複雑化する場合があります。 以下のセクションは、実際の移行の決定事項に基づく最適なツールの選択肢について理解するために役立ちます。

### <a name="suggested-action-during-the-migrate-process"></a>移行プロセスで推奨されるアクション

移行と同期に推奨される手法では、以下の 3 つのツールを組み合わせて使用します。 以降のセクションでは、さまざまなターゲット ソリューションとソース ソリューションを考慮した、より複雑な移行および同期のオプションについて説明しています。

|移行オプション|目的|
|---------|---------|
|[Azure Database Migration Service](https://docs.microsoft.com/sql/dma/dma-overview)|Azure SQL Database マネージド インスタンスへの大規模なオンライン (最小ダウンタイム) 移行とオフライン (1 回限り) 移行をサポートします。 サポートされる移行元: SQL Server 2005、SQL Server 2008 および SQL Server 2008 R2、SQL Server 2012、SQL Server 2014、SQL Server 2016、および SQL Server 2017。|
|[トランザクション レプリケーション](https://docs.microsoft.com/sql/relational-databases/replication/administration/enhance-transactional-replication-performance)|次の移行元については、Azure SQL Database マネージド インスタンスへのトランザクション レプリケーションがサポートされています: SQL Server 2012 (SP2 CU8、SP3、またはそれ以降)、SQL Server 2014 (RTM CU10 以降、または SP1 CU3 以降)、SQL Server 2016、SQL Server 2017。|
|[一括読み込み](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql)|次の場所に格納されているデータについては、Azure SQL Database マネージド インスタンスへの一括読み込みを使用します: SQL Server 2005、SQL Server 2008 および SQL Server 2008 R2、SQL Server 2012、SQL Server 2014、SQL Server 2016、および SQL Server 2017。|

### <a name="guidance-and-tutorials-for-suggested-migration-process"></a>推奨される移行プロセスに関するガイダンスとチュートリアル

Azure Database Migration Service を使用した移行に最適なガイダンスの選択は、対象となるソースおよびターゲット プラットフォームに左右されます。 次の表は、Azure Database Migration Service を使用して SQL データベースを移行する標準的な手法ごとに対応したチュートリアルへのリンクです。

|source  |移行先  |ツール  |移行の種類  |ガイダンス  |
|---------|---------|---------|---------|---------|
|SQL Server|Azure SQL データベース|Database Migration Service|オフライン|[チュートリアル](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-azure-sql)|
|SQL Server|Azure SQL データベース|Database Migration Service|オンライン|[チュートリアル](https://docs.microsoft.com/azure/dms/tutorial-sql-server-azure-sql-online)|
|SQL Server|Azure SQL Database マネージド インスタンス|Database Migration Service|オフライン|[チュートリアル](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance)|
|SQL Server|Azure SQL Database マネージド インスタンス|Database Migration Service|オンライン|[チュートリアル](https://docs.microsoft.com/azure/dms/tutorial-sql-server-managed-instance-online)|
|RDS SQL Server|Azure SQL Database (またはマネージド インスタンス)|Database Migration Service|オンライン|[チュートリアル](https://docs.microsoft.com/azure/dms/tutorial-rds-sql-server-azure-sql-and-managed-instance-online)|

### <a name="guidance-and-tutorials-for-various-services-to-equivalent-paas-solutions"></a>同等の PaaS ソリューションに対する各種サービスのガイダンスとチュートリアル

SQL Server のインスタンスから Azure Database Migration Service にデータベースを移動した後は、多数の PaaS ソリューションでスキーマとデータを再ホストすることができます。 ただし、他の必要なサービスがまだそのサーバーで実行されている可能性があります。 次の 3 つのチュートリアルは、SSIS、SSAS、SSRS を Azure 上の同等の PaaS サービスに移動する際に役立ちます。

|source  |移行先  |ツール  |移行の種類  |ガイダンス  |
|---------|---------|---------|---------|---------|
|SQL Server Integration Services|Azure Data Factory 統合ランタイム|Azure Data Factory|オフライン|[チュートリアル](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)|
|SQL Server Analysis Services - 表形式モデル|Azure Analysis Services|SQL Server Data Tools|オフライン|[チュートリアル](https://docs.microsoft.com/azure/analysis-services/analysis-services-deploy)|
|SQL Server Reporting Services|Power BI Report Server|Power BI|オフライン|[チュートリアル](https://docs.microsoft.com/power-bi/report-server/migrate-report-server)|

### <a name="guidance-and-tutorials-for-migration-from-sql-server-to-an-iaas-instance-of-sql-server"></a>SQL Server から IaaS インスタンスの SQL Server に移行するためのガイダンスとチュートリアル

データベースとサービスを PaaS インスタンスに移行した後でも、PaaS との互換性がないデータ構造やサービスが残っている可能性があります。 既存の制約によってデータ構造やサービスの移行が妨げられた場合は、データ ポートフォリオに含まれる各種の資産を Azure IaaS ソリューションに移行する際に以下のチュートリアルが役立ちます。

SQL Server のインスタンス上のデータベースまたはその他のサービスを移行するには、この方法を使用します。

|source  |移行先  |ツール  |移行の種類  |ガイダンス  |
|---------|---------|---------|---------|---------|
|単一インスタンス SQL Server|IaaS 上の SQL Server|多様|オフライン|[チュートリアル](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-migrate-sql)|

## <a name="optimization-process-changes"></a>最適化プロセスの変更

最適化の間、それぞれのデータ構造、サービス、または SQL Server インスタンスをテストし、最適化し、運用環境に昇格させることができます。 これが、ワークロード単位の移行モデルの枠を超えることの最大の影響です。

依存するワークロード、アプリケーション、VM は、SQL Server インスタンスと同じ反復内で移行するのが理想です。 その理想的なシナリオであれば、ワークロードをデータ ソースと一緒にテストすることができます。 テスト後、データ構造を運用環境に昇格させ、同期プロセスを終了することができます。

ここで、データベースの移行とワークロードの移行の間に大きな時間差があるシナリオを考えてみましょう。 残念ながら、ワークロード主導ではない移行の間、これは最適化プロセスにとって最大の変更になる可能性があります。 SQL Server の移行の一環として複数のデータベースを移行するとき、作業を複数回繰り返す中で、それらのデータベースがクラウドとオンプレミスの両方に共存する可能性があります。 その期間中は、依存している資産の移行、テスト、昇格が完了するまで、データの同期状態を維持する必要があります。

依存するワークロードがすべて昇格されるまでは、ソース システムからターゲット システムへのデータの同期のサポートを、作業者とそのチームが担当することになります。 この同期では、ネットワーク帯域幅、クラウド コストのほか、最も重要なものとして人的リソースの時間が消費されます。 SQL Server の移行ワークロードと、依存するすべてのワークロードやアプリケーションの間で導入計画を適切に調整すれば、このコストのかかるオーバーヘッドを削減できます。

### <a name="suggested-action-during-the-optimization-process"></a>最適化プロセス中に推奨されるアクション

最適化プロセスでは、すべてのデータ構造とサービスが運用環境に昇格されるまでの間、反復のたびに次のタスクを実行します。

1. データの同期を確認します。
2. 移行したアプリケーションをテストします。
3. アプリケーションとデータ構造を最適化してコストを調整します。
4. アプリケーションを運用環境に昇格させます。
5. オンプレミス データベースに対して継続するオンプレミス トラフィックがないかテストします。
6. 運用環境に昇格されたデータの同期を終了します。
7. 元のソース データベースを終了します。

手順 5 が終わるまで、データベースと同期を終了することはできません。 SQL Server のインスタンス上のすべてのデータベースで 7 つの手順をすべて完了するまでは、SQL Server のオンプレミス インスタンスを運用環境として扱う必要があります。 すべての同期を維持する必要があります。

## <a name="next-steps"></a>次のステップ

[拡張スコープ チェックリスト](./index.md)に戻り、移行方法が完全に調整されていることを確認します。

> [!div class="nextstepaction"]
> [範囲拡大チェックリスト](./index.md)
