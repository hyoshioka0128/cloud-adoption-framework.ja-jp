---
title: データの収集と移行
description: クラウドの発明とイノベーションのためにデータを最新化する Azure Database Migration Service とその他のデータ移行ツールについて説明します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 03/18/2021
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: innovate
ms.custom: seo-caf-innovate
keywords: データの収集, データの移行, データ移行, データの最新化
ms.openlocfilehash: 2105edeba384fe185997e892477383f281fdf795
ms.sourcegitcommit: 51565dc4d3a1858bd62f708f2e4c082fbd4c6fe4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/20/2021
ms.locfileid: "107747978"
---
# <a name="collect-data-through-the-migration-and-data-modernization-of-existing-sources"></a>既存のソースの移行とデータの最新化によってデータを収集する

多くの場合、企業は、[民主化](../considerations/data.md)できるさまざまな種類の既存データを持っています。 顧客の仮説で、既存のデータを使用して最新のソリューションを構築する必要がある場合、最初の手順は、発明とイノベーションを準備するためのデータ移行とデータの最新化になる可能性があります。 クラウド導入計画に含まれる既存の移行作業に合わせるために、移行と最新化は、[移行の方法論](../../migrate/index.md)を使用して実行できます。

## <a name="use-of-this-article"></a>この記事の利用方法

この記事では、移行手法に合わせた一連のアプローチについて説明します。 これらのアプローチを、標準的なデータベースの移行ツールチェーンに合わせることができます。

ワークロード評価フェーズ中に、クラウド導入チームは、移行される資産の現在の状態と将来の状態を評価します。 このプロセスがイノベーションの取り組みの一部である場合は、どちらのクラウド導入チームも、この記事を使用して、これらの評価を行うことができます。

## <a name="primary-toolset"></a>プライマリ ツールセット

オンプレミスのデータを移行して最新化する場合、最も一般的な Azure ツールの選択肢は [Azure Database Migration Service](/azure/dms/) です。 このサービスは、より広範な [Azure Migrate ハブ](/azure/migrate/migrate-services-overview) ツールチェーンの一部です。 既存の SQL Server データ ソースについては、[Data Migration Assistant](/sql/dma/dma-overview) がデータ構造の評価と移行に役立ちます。

Oracle と NoSQL のデータ移行をサポートするために、特定の種類のソース データベースのターゲット データベースへの移行に対して、Azure Database Migration Service を使用することもできます。 たとえば、Oracle データベースを PostgreSQL に移行する場合や、MongoDB データベースを Azure Cosmos DB に移行する場合などがあります。 一般的に、導入チームは、パートナー ツールまたはカスタム スクリプトを使用して、Azure Cosmos DB、Azure HDInsight、またはサービスとしてのインフラストラクチャ (IaaS) に基づく仮想マシンのオプションに移行します。

## <a name="considerations-and-guidance"></a>考慮事項とガイダンス

データ移行とデータの最新化のために Azure Database Migration Service を使用する場合は、次の点を理解しておくことが重要です。

- データ ソースをホストしている現在のプラットフォーム。
- 現在のバージョン。
- 顧客の仮説または目標を最もよくサポートする将来のプラットフォームとバージョン。

### <a name="data-migration-type"></a>データ移行の種類

オフライン移行では、アプリケーションのダウンタイムが、移行の開始時に開始されます。 オンライン移行の場合は、ダウンタイムが移行の最後の切り替え時だけに限定されます。

オフライン移行をテストして、ダウンタイムが許容範囲内かどうか判別してください。 復元時間を許容できない場合は、オンライン移行を実行します。

データ移行の種類に関する次の表に、移行チームで検討するソースとターゲットのペアを示します。 各ペアには、ツールの選択肢と関連するチュートリアルへのリンクが含まれています。

| source | 移行先 | ツール | 移行の種類 | ガイダンス |
|--|--|--|--|--|
| SQL Server | Azure SQL データベース | Database Migration Service | オフライン | [チュートリアル](/azure/dms/tutorial-sql-server-to-azure-sql) |
| SQL Server | Azure SQL データベース | Database Migration Service | オンライン | [チュートリアル](/azure/dms/tutorial-sql-server-to-azure-sql) |
| SQL Server | Azure SQL Managed Instance | Database Migration Service | オフライン | [チュートリアル](/azure/dms/tutorial-sql-server-to-managed-instance) |
| SQL Server | Azure SQL Managed Instance | Database Migration Service | オンライン | [チュートリアル](/azure/dms/tutorial-sql-server-managed-instance-online) |
| RDS SQL Server | Azure SQL Database または Azure SQL Managed Instance | Database Migration Service | オンライン | [チュートリアル](/azure/dms/tutorial-rds-sql-server-azure-sql-and-managed-instance-online) |
| MySQL | Azure Database for MySQL | Database Migration Service | オンライン | [チュートリアル](/azure/dms/tutorial-mysql-azure-mysql-online) |
| PostgreSQL | Azure Database for PostgreSQL | Database Migration Service | オンライン | [チュートリアル](/azure/dms/tutorial-postgresql-azure-postgresql-online) |
| MongoDB | MongoDB 用 Azure Cosmos DB API | Database Migration Service | オフライン | [チュートリアル](/azure/dms/tutorial-mongodb-cosmos-db) |
| MongoDB | MongoDB 用 Azure Cosmos DB API | Database Migration Service | オンライン | [チュートリアル](/azure/dms/tutorial-mongodb-cosmos-db-online) |
