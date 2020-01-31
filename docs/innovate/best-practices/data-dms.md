---
title: クラウド イノベーション:Azure Database Migration Service
description: クラウド イノベーション - Azure Database Migration Service
author: BrianBlanchard
ms.author: brblanch
ms.date: 10/17/2019
ms.topic: guide
ms.service: cloud-adoption-framework
ms.subservice: innovate
ms.openlocfilehash: 44ebe7e28eea56d1b7e61b5926a9588f4c985ae1
ms.sourcegitcommit: 2362fb3154a91aa421224ffdb2cc632d982b129b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2020
ms.locfileid: "76808670"
---
# <a name="collect-data-through-the-migration-and-modernization-of-existing-data-sources"></a>既存のデータ ソースの移行と最新化によってデータを収集する

多くの場合、企業は、[民主化](../considerations/data.md)できるさまざまな種類の既存データを持っています。 顧客の仮説で、既存のデータを使用して最新のソリューションを構築する必要がある場合、最初の手順は、発明とイノベーションを準備するためのデータの移行と最新化になる可能性があります。 クラウド導入計画に含まれる既存の移行作業に合わせるために、移行と最新化は、[移行の方法論](../../migrate/index.md)の中で簡単に実行できます。

## <a name="use-of-this-article"></a>この記事の利用方法

この記事では、移行プロセスに合わせた一連のアプローチについて説明します。 これらのアプローチを、標準的な移行ツールチェーンに合わせることができます。

移行の方法論の中での評価プロセス中に、クラウド導入チームは、移行される資産の現在の状態と、求められている将来の状態を評価します。 このプロセスがイノベーションの取り組みの一部である場合は、どちらのクラウド導入チームも、この記事を使用して、これらの評価を行うことができます。

## <a name="primary-toolset"></a>プライマリ ツールセット

オンプレミスのデータを移行して最新化する場合、最も一般的な Azure ツールの選択肢は [Azure Database Migration Service](https://docs.microsoft.com/azure/dms) です。 このサービスは、より広範な [Azure Migrate](https://docs.microsoft.com/azure/migrate/migrate-services-overview) ツールチェーンの一部です。 既存の SQL Server データ ソースについては、[Data Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview) を使用して、少数のデータ構造の評価と移行を行うことができます。

Oracle と NoSQL の移行をサポートするために、特定の種類のソース データベースのターゲット データベースへの移行に対して、[Database Migration Service](https://docs.microsoft.com/azure/dms) を使用することもできます。 例として、Oracle から PostgreSQL への 移行や、MongoDB から Cosmos DB への移行があります。 一般的に、導入チームは、パートナー ツールまたはカスタム スクリプトを使用して、Azure Cosmos DB、Azure HDInsight、またはサービスとしてのインフラストラクチャ (IaaS) に基づく仮想マシンのオプションに移行します。

## <a name="considerations-and-guidance"></a>考慮事項とガイダンス

データの移行と最新化のために Azure Database Migration Service を使用する場合は、次の点を理解しておくことが重要です。

- データ ソースをホストしている現在のプラットフォーム。
- 現在のバージョン。
- 顧客の仮説または目標を最もよくサポートする将来のプラットフォームとバージョン。

次の表に、移行チームで検討するソースとターゲットのペアを示します。 各ペアには、ツールの選択肢と関連するガイドへのリンクが含まれています。

### <a name="migration-type"></a>移行の種類

オフライン移行では、アプリケーションのダウンタイムが、移行の開始時に開始されます。 オンライン移行では、ダウンタイムが移行の最後の切り替え時だけに限定されます。

許容できるビジネスのダウンタイムを決定し、オフライン移行をテストすることをお勧めします。 これを行って、復元時間が許容されるダウンタイムを満たしているかどうかを確認します。 復元時間を許容できない場合は、オンライン移行を実行します。

|source  |移行先  |ツール  |移行の種類  |ガイダンス  |
|---------|---------|---------|---------|---------|
|SQL Server|Azure SQL データベース|Database Migration Service|オフライン|[チュートリアル](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-azure-sql)|
|SQL Server|Azure SQL データベース|Database Migration Service|オンライン|[チュートリアル](https://docs.microsoft.com/azure/dms/tutorial-sql-server-azure-sql-online)|
|SQL Server|Azure SQL Database マネージド インスタンス|Database Migration Service|オフライン|[チュートリアル](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance)|
|SQL Server|Azure SQL Database マネージド インスタンス|Database Migration Service|オンライン|[チュートリアル](https://docs.microsoft.com/azure/dms/tutorial-sql-server-managed-instance-online)|
|RDS SQL Server|Azure SQL Database または Azure SQL Database マネージド インスタンス|Database Migration Service|オンライン|[チュートリアル](https://docs.microsoft.com/azure/dms/tutorial-rds-sql-server-azure-sql-and-managed-instance-online)|
|MySQL|Azure Database for MySQL|Database Migration Service|オンライン|[チュートリアル](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online)|
|PostgreSQL|Azure Database for PostgreSQL|Database Migration Service|オンライン|[チュートリアル](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online)|
|MongoDB|Azure Cosmos DB Mongo API|Database Migration Service|オフライン|[チュートリアル](https://docs.microsoft.com/azure/dms/tutorial-mongodb-cosmos-db)|
|MongoDB|Azure Cosmos DB Mongo API|Database Migration Service|オンライン|[チュートリアル](https://docs.microsoft.com/azure/dms/tutorial-mongodb-cosmos-db-online)|
|Oracle|さまざまなサービスとしてのプラットフォーム (PaaS) と IaaS オプション|パートナーのツールまたは Azure Migrate|オフラインまたはオンライン|[デシジョン ツリー](../../migrate/expanded-scope/data-oracle-migration.md)|
|さまざまな NoSQL DB オプション|Cosmo DB オプションまたは IaaS オプション|手続き型の移行または Azure Migrate|オフラインまたはオンライン|[デシジョン ツリー](../../migrate/expanded-scope/data-no-sql-migration.md)|
