---
title: SQL の移行によって移行作業を促進する
titleSuffix: Microsoft Cloud Adoption Framework for Azure
description: SQL の移行によって移行作業を促進する
author: BrianBlanchard
ms.author: brblanch
ms.date: 10/10/2019
ms.topic: guide
ms.service: cloud-adoption-framework
ms.subservice: migrate
ms.openlocfilehash: 4af94af91874ac666f45a917eed003b3cf881c51
ms.sourcegitcommit: 35c162d2d09ec1c4a57d3d57a5db1d56ee883806
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/17/2019
ms.locfileid: "72558613"
---
# <a name="accelerate-migration-with-a-sql-migration"></a>SQL の移行によって移行作業を促進する

SQL Server インスタンス全体を移行すると、ワークロードの移行作業を高速化できます。 以下のガイダンスでは、ワークロードに重点を置いた移行作業とは別に SQL Server を移行することによって、[Azure 移行ガイド](../azure-migration-guide/index.md)の範囲を拡大します。 この方法では、複数ワークロードの移行を促進する手段として、単一データプラットフォームの移行を実施することができます。

## <a name="general-scope-expansion"></a>全般的な範囲の拡大

この範囲の拡大で必要となる作業の大半は、移行作業の前提条件、評価、移行、最適化のプロセスで発生します。

<!-- markdownlint-disable MD026 -->

## <a name="is-this-expanded-scope-right-for-you"></a>この拡大された範囲は現状に適しているか

[Azure 移行ガイド](../azure-migration-guide/index.md)では、各データ構造を、関連付けられたワークロードと共に 1 回の移行作業の一環として移行する方法が推奨されています。 この反復的な移行方法により、阻害要因を形成して事業価値のリターンを滞らせる可能性のあるタスク (検出や評価など) が軽減されます。

ただし、一部のデータ構造は、個別のデータプラットフォームの移行によってより効率的に移行できます。 次に、範囲が拡大された本ガイダンスの使用につながる可能性のある例をいくつか紹介します。

- **サービス終了:** SQL Server インスタンスを速やかに移行してサービス終了の問題を回避することは、標準的な移行作業の他に、このガイドを用いるだけの妥当な理由となりえます。
- **SQL Server のサービス:** データ構造が、仮想マシン上で稼働している SQL Server を必要とする、より広範なソリューションの一部になっています。 これは、SQL Server Reporting Services、SQL Server Integration Services、SQL Server Analysis Services など、SQL Server のサービスを活用するソリューションでよく見られます。
- **高密度、低使用率のデータベース:** SQL Server でデータベースの密度が高くなっています。 その各データベースでは、トランザクションの量が少ないため、コンピューティング リソースをほとんど必要としません。 他の最新のソリューションを検討すべきではありますが、IaaS のアプローチは、運用コストの大幅な削減につながる可能性があります。
- **総保有コスト:** 該当する場合には、表示価格に [Azure ハイブリッド特典](https://azure.microsoft.com/pricing/hybrid-benefit)を適用して、SQL Server の保有コストを最小限に抑えることができます。 これは特に、マルチクラウドのシナリオで SQL Server をホストするお客様にとって一般的です。
- **移行アクセラレータ:** SQL Server インスタンスのリフトアンドシフト移行では、1 回の反復で複数のデータベースを移動できます。 場合によっては、その後の反復で、特にアプリケーションと VM を中心に、さらに多くのワークロードを 1 回の反復で移行することもできます。
- **VMWare の移行:** 一般的なオンプレミス アーキテクチャでは、アプリケーションと VM が仮想ホストに、データベースがベア メタルに配置されます。 この一般的なアーキテクチャを移行する際は、VMware ホストを Azure VMWare Service (AVS) への移行がこのガイドでは推奨されており、それらの仮想ホストをサポートするために SQL Server インスタンス全体を移行します。 推奨されるガイダンスについては、[VMWare ホストの移行](./vmware-host.md)に関するページを参照してください。

上記の条件がいずれもこの移行に当てはまらない場合は、[標準移行プロセス](../index.md)を続行することをお勧めします。 標準プロセスでは、データ構造が各ワークロードと一緒に反復的に移行されます。

このガイドが実際の条件と合致する場合は、[標準移行プロセス](../index.md)内の作業として、この拡大範囲ガイドを引き続き参照してください。 この作業は、前提条件フェーズで全体的な導入計画に統合することができます。

## <a name="suggested-prerequisites"></a>推奨される前提条件

SQL Server の移行を実施する前に、データ資産を追加して**デジタル資産**の拡大に着手します。 データ資産には、移行の候補となるデータ資産のインベントリが記録されます。 次の表に、データ資産の記録方法を示します。

### <a name="server-inventory"></a>サーバー インベントリ

SQL Server のサーバー インベントリの例を次に示します。

|SQL Server|目的|Version|[重要度](../../manage/considerations/criticality.md)|[機密度](../../govern/policy-compliance/data-classification.md)|データベース数|SSIS|SSRS|SSAS|クラスター|ノードの数|
|---------|---------|---------|---------|---------|---------|---------|---------|
|sql-01|コア アプリ|2016|ミッション クリティカル|極秘|40|該当なし|該当なし|該当なし|はい|3|
|sql-02|コア アプリ|2016|ミッション クリティカル|極秘|40|該当なし|該当なし|該当なし|はい|3|
|sql-03|コア アプリ|2016|ミッション クリティカル|極秘|40|該当なし|該当なし|該当なし|はい|3|
|sql-04|BI|2012|高|XX|6|該当なし|機密|はい - 多次元キューブ|いいえ|1|
|sql-05|統合|2008 R2|低|全般|20|はい|該当なし|該当なし|いいえ|1|

### <a name="database-inventory"></a>データベース インベントリ

上記の SQL Server の 1 つについてのデータベース インベントリの例を次に示します。

|サーバー|Database|[重要度](../../manage/considerations/criticality.md)|[機密度](../../govern/policy-compliance/data-classification.md)|DMA の結果|DMA の修復|ターゲット プラットフォーム|
|---------|---------|---------|---------|---------|---------|---------|
|sql-01|DB-1|ミッション クリティカル|極秘|互換性あり|該当なし|Azure SQL Database|
|sql-01|DB-2|高|機密|スキーマの変更が必要|変更実施済み|Azure SQL Database|
|sql-01|DB-1|高|全般|互換性あり|該当なし|Azure SQL Managed Instance|
|sql-01|DB-1|低|極秘|スキーマの変更が必要|変更予定|Azure SQL Managed Instance|
|sql-01|DB-1|ミッション クリティカル|全般|互換性あり|該当なし|Azure SQL Managed Instance|
|sql-01|DB-2|高|機密|互換性あり|該当なし|Azure SQL Database|

### <a name="integration-with-the-cloud-adoption-plan"></a>クラウド導入計画との統合

検出の完了後、その計画を[クラウド導入計画](../../plan/template.md)に含めることができます。 クラウド導入計画内では、移行対象となる各 SQL Server インスタンスを[個別のワークロード](../../plan/workloads.md)として追加します。 各ワークロード内では、データベースとサービス (SSIS、SSAS、SSRS) をそれぞれ、[資産](../../plan/workloads.md)として追加できます。 ワークロードと資産を導入計画に一括で追加する方法については、[Excel を使用した作業項目の追加と編集](https://docs.microsoft.com/azure/devops/boards/backlogs/office/bulk-add-modify-work-items-excel?view=azure-devops)に関するページを参照してください。

ワークロードと資産を計画に追加した後、チームは、作業の指針となる導入計画を使用して、標準移行プロセスを続行することができます。 導入チームは、評価、移行、最適化のプロセスに進むと、次の変更を作業に考慮する必要があります。

## <a name="assessment-process-changes"></a>評価プロセスの変更

計画内のデータベースを PaaS (サービスとしてのプラットフォーム) データ プラットフォームに移行できる場合は、選択したデータベースの互換性を Data Migration Assistant を使用して評価します。 データベースでスキーマ変換が必要な場合、移行パイプラインの中断を回避するために、この変換を評価プロセスの一環として実行することをお勧めします。

### <a name="suggested-action-during-the-assessment-process"></a>評価プロセス中に推奨されるアクション

PaaS ソリューションに移行できるデータベースについては、評価プロセス中に次のアクションを実行することが考えられます。

- **Data Migration Assistant を使用した評価:** Data Migration Assistant を使用すると、対象となる Azure SQL Database マネージド インスタンスでデータベースの機能に影響を与える可能性がある互換性の問題を検出できます。また、パフォーマンスと信頼性を向上させるための提案が提供され、スキーマ、データ、包含されていないオブジェクトがソース サーバーからターゲット サーバーに移動されます。 詳細については、[Data Migration Assistant](/sql/dma/dma-overview) に関するページを参照してください。
- **修復と変換:** Data Migration Assistant の出力内容に基づいて、ソース データ スキーマを変換し、互換性の問題を修復します。 変換されたデータ スキーマは、依存するアプリケーションを使用してテストします。

## <a name="migrate-process-changes"></a>移行プロセスの変更

移行時には、ツールと手法の選択肢が複数あります。 ただし、それぞれの手法が踏襲するプロセスはシンプルです。つまりスキーマ、データ、オブジェクトの移行です。 ターゲット データ ソースにデータを同期させます。

データ構造とサービスのターゲットとソースによって、この 2 つのステップがかなり複雑化する場合があります。 実際の移行の決定事項に基づく最適なツールの選択肢については、以降の各セクションを参照してください。

### <a name="suggested-action-during-the-migrate-process"></a>移行プロセスで推奨されるアクション

移行と同期に推奨される手法では、以下の 3 つのツールを組み合わせて使用します。 以降のセクションでは、さまざまなターゲット ソリューションとソース ソリューションを考慮した、より複雑な移行および同期のオプションについて説明しています。

|移行オプション|目的|
|---------|---------|
|[Azure Database Migration Service](/sql/dma/dma-overview)|Azure DMS は、SQL Server 2005、SQL Server 2008、SQL Server 2008 R2、SQL Server 2012、SQL Server 2014、SQL Server 2016、SQL Server 2017 から Azure SQL Database マネージド インスタンスへの大規模なオンライン移行 (ダウンタイムが最小) とオフライン移行 (1 回限り) をサポートします。|
|[トランザクション レプリケーション](/sql/relational-databases/replication/administration/enhance-transactional-replication-performance)|SQL Server 2012 (SP2 CU8、SP3 以降)、SQL Server 2014 (RTM CU10 以降または SP1 CU3 以降)、SQL Server 2016、SQL Server 2017 からの移行では、Azure SQL Database マネージド インスタンスへのトランザクション レプリケーションがサポートされます。|
|[一括読み込み](/sql/t-sql/statements/bulk-insert-transact-sql)|SQL Server 2005、SQL Server 2008、SQL Server 2008 R2、SQL Server 2012、SQL Server 2014、SQL Server 2016、SQL Server 2017 に格納されているデータについては、Azure SQL Database マネージド インスタンスへの一括読み込みを使用します。|

### <a name="guidance-and-tutorials-for-suggested-migration-process"></a>推奨される移行プロセスに関するガイダンスとチュートリアル

DMS を使用した移行に最適なガイダンスの選択は、対象となるソースおよびターゲット プラットフォームに左右されます。 次の表は、DMS を使用して SQL データベースを移行するための標準的な手法ごとに対応するチュートリアルをまとめたものです。

|source  |ターゲット  |ツール  |移行の種類  |ガイダンス  |
|---------|---------|---------|---------|---------|
|SQL Server|Azure SQL Database|DMS|オフライン|[チュートリアル](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-azure-sql)|
|SQL Server|Azure SQL Database|DMS|オンライン|[チュートリアル](https://docs.microsoft.com/azure/dms/tutorial-sql-server-azure-sql-online)|
|SQL Server|Azure SQL Database マネージド インスタンス|DMS|オフライン|[チュートリアル](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance)|
|SQL Server|Azure SQL Database マネージド インスタンス|DMS|オンライン|[チュートリアル](https://docs.microsoft.com/azure/dms/tutorial-sql-server-managed-instance-online)|
|RDS SQL Server|Azure SQL Database (または Managed Instance)|DMS|オンライン|[チュートリアル](https://docs.microsoft.com/azure/dms/tutorial-rds-sql-server-azure-sql-and-managed-instance-online)|

### <a name="guidance-and-tutorials-for-various-services-to-equivalent-paas-solutions"></a>同等の PaaS ソリューションに対する各種サービスのガイダンスとチュートリアル

SQL サーバーから DMS にデータベースを移動した後は、多数の PaaS ソリューションでスキーマとデータをリホストすることができます。 ただし、他の必要なサービスがまだそのサーバーで動作されている可能性があります。 次の 3 つのチュートリアルは、SSIS、SSAS、SSRS を Azure 上の同等の PaaS サービスに移動する際に役立ちます。

|source  |ターゲット  |ツール  |移行の種類  |ガイダンス  |
|---------|---------|---------|---------|---------|
|SQL Server Integration Service|Data Factory Integration Runtime|Azure Data Factory|オフライン|[チュートリアル](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)|
|SQL Server Analysis Services - 表形式モデル|Azure Analysis Service|SSDT|オフライン|[チュートリアル](https://docs.microsoft.com/azure/analysis-services/analysis-services-deploy)|
|SQL Server Reporting Services|Power BI レポート サーバー|Power BI|オフライン|[チュートリアル](/power-bi/report-server/migrate-report-server)|

### <a name="guidance-and-tutorials-for-migration-from-sql-server-to-an-iaas-instance-of-sql-server"></a>SQL Server から IaaS インスタンスの SQL Server に移行するためのガイダンスとチュートリアル

データベースとサービスを PaaS インスタンスに移行した後でも、PaaS との互換性がないデータ構造やサービスがまだ存在する可能性があります。 既存の制約によってデータ構造やサービスの移行が妨げられた場合は、データ ポートフォリオに含まれる各種の資産を Azure IaaS ソリューションに移行する際に以下のチュートリアルが役立ちます。

この方法は、SQL Server 上のデータベース、またはソース SQL Server 上で動作しているサービスを移行する際に使用できます。

|source  |ターゲット  |ツール  |移行の種類  |ガイダンス  |
|---------|---------|---------|---------|---------|
|単一インスタンス SQL Server|IaaS 上の SQL Server|多様|オフライン|[チュートリアル](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-migrate-sql)|

## <a name="optimization-process-changes"></a>最適化プロセスの変更

最適化の段階では、データ構造、サービス、SQL Server インスタンスをそれぞれテストして最適化し、運用環境に昇格させることができます。 これが、ワークロード単位の移行モデルの枠を超えることの最大の影響です。

依存するワークロード、アプリケーション、VM は、SQL Server インスタンスと同じ反復内で移行するのが理想です。 その理想的なシナリオであれば、ワークロードをデータ ソースと一緒にテストすることができます。 テストが完了したら、データ構造を運用環境に昇格させて、同期プロセスを終了することができます。

残念ながら、データベースの移行とワークロードの移行の間で大きな時間差があると、ワークロードに基づかない移行の際に、最適化プロセスに対して最大の変更が必要になります。 SQL Server の移行の一環として複数のデータベースを移行するとき、作業を複数回繰り返す中で、それらのデータベースがクラウドとオンプレミスの両方に共存する可能性があります。 その期間中は、依存している資産の移行、テスト、昇格が完了するまで、データの同期状態を維持しなければなりません。

依存するワークロードがすべて昇格されるまでは、ソース システムからターゲット システムへのデータの同期のサポートをこのチームが担当することになります。 この同期では、ネットワーク帯域幅、クラウド コストのほか、最も重要なものとして人的リソースの時間が消費されます。 SQL Server の移行 "ワークロード" と依存するすべてのワークロードやアプリケーションで導入計画を適切に調整すれば、このコストのかかるオーバーヘッドは削減されます。

### <a name="suggested-action-during-the-optimization-process"></a>最適化プロセス中に推奨されるアクション

最適化プロセスでは、データ構造とサービスがすべて運用環境に昇格されるまで反復するたびに次のタスクを実行する必要があります。

1. データの同期を確認します。
2. 移行したアプリケーションをテストします。
3. アプリケーションとデータ構造を最適化してコストを調整します。
4. アプリケーションを運用環境に昇格させます。
5. オンプレミス データベースに対して継続するオンプレミス トラフィックがないかテストします。
6. 運用環境に昇格されたデータの同期を終了します。
7. 元のソース データベースを終了します。

手順 5 が終わるまで、データベースと同期を終了することはできません。 SQL Server 上のすべてのデータベースで手順 1 から 7 を完了するまでは、オンプレミスの SQL Server を運用環境として扱い、すべての同期を維持する必要があります。

## <a name="next-steps"></a>次の手順

[拡張スコープ チェックリスト](./index.md)に戻り、移行方法が完全に調整されていることを確認します。

> [!div class="nextstepaction"]
> [拡張スコープ チェックリスト](./index.md)
