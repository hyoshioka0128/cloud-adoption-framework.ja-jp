---
title: クラウド イノベーション:データ移行サービス
titleSuffix: Microsoft Cloud Adoption Framework for Azure
description: クラウドイノベーション - データ移行サービス
author: BrianBlanchard
ms.author: brblanch
ms.date: 10/17/2019
ms.topic: guide
ms.service: cloud-adoption-framework
ms.subservice: innovate
ms.openlocfilehash: c75efe3576bb61ecb116ab22e4946b8d87da3d4a
ms.sourcegitcommit: f3371811a36e12533ecbc3aa936e2a68e0cee25f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/21/2019
ms.locfileid: "72683433"
---
# <a name="collect-data-through-the-migration-and-modernization-of-existing-data-sources"></a>既存のデータ ソースの移行と最新化によってデータを収集する

多くの場合、企業は、[民主化](../considerations/data.md)できる多様な既存のデータを所有しています。 顧客の仮説で、最新のソリューションを構築するには既存のデータを使用する必要がある場合、最初の手順は、発明とイノベーションを準備するためのデータの移行と最新化になる可能性があります。 クラウド導入計画に含まれる既存の移行作業に合わせるために、移行と最新化は、[移行の方法論](../../migrate/index.md)の中で簡単に実行できます。

## <a name="use-of-this-article"></a>この記事の利用方法

この記事では、移行プロセスに合わせた一連のアプローチについて説明していますが、最も適しているのは、標準的な移行ツールチェーンである可能性があります。 移行の方法論の中での評価プロセス中に、クラウド導入チームは、移行される資産の現在の状態を評価し、将来の状態を求めます。 このプロセスがイノベーションの取り組みの一部である場合は、どちらのクラウド導入チームも、この記事を使用して、これらの意思決定を行うことができます。

## <a name="primary-toolset"></a>プライマリ ツールセット

現在オンプレミスに存在するデータの移行と最新化を行う場合、最も一般的な Azure ツールの選択肢は[データ移行サービス (DMS)](https://docs.microsoft.com/azure/dms) です。これは、より広範な [Azure Migrate](https://docs.microsoft.com/azure/migrate/migrate-services-overview) ツールチェーンの一部です。 既存の SQL Server データ ソースについては、[Data Migration Assistant (DMA)](/sql/dma/dma-overview) も、少数のデータ構造の評価と移行に関するサポートを提供する可能性があります。

Oracle と NoSQL の移行をサポートするために、データベースをターゲットとする特定の種類のソースに対して、[データ移行サービス (DMS)](https://docs.microsoft.com/azure/dms) を使用することもできます (たとえば、Oracle から PostgreSQL へ、MongoDB から Cosmos DB へ)。 導入チームにとってもっと一般的なのは、サードパーティのツールやカスタム移行スクリプトを活用して、Cosmos DB、HDInsight、または IaaS ベースの VM オプションを移行することです。

## <a name="considerations-and-guidance"></a>考慮事項とガイダンス

データの移行と最新化のために DMS を活用する場合は、データ (ソース) をホストしている現在のプラットフォームとバージョン、および顧客の仮説 (ターゲット) を最もサポートする将来のプラットフォームとバージョンを理解することが重要です。 移行チームで検討するソースとターゲットのペアの一覧を次に示します。 それぞれに、ツールの選択と、それらの考慮事項に基づくガイドへのリンクが含まれています。

**移行の種類:** オフライン移行では、アプリケーションのダウンタイムが、移行の開始時に開始されます。 オンライン移行では、ダウンタイムが移行の最後の切り替え時だけに限定されます。 許容されるビジネス ダウンタイムを理解し、オフライン移行をテストして、復元時間がこれを満たしているかどうかを確認することをお勧めします。そうでない場合は、オンライン移行を行います。

|source  |ターゲット  |ツール  |移行の種類  |ガイダンス  |
|---------|---------|---------|---------|---------|
|SQL Server|Azure SQL Database|DMS|オフライン|[チュートリアル](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-azure-sql)|
|SQL Server|Azure SQL Database|DMS|オンライン|[チュートリアル](https://docs.microsoft.com/azure/dms/tutorial-sql-server-azure-sql-online)|
|SQL Server|Azure SQL Database マネージド インスタンス|DMS|オフライン|[チュートリアル](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance)|
|SQL Server|Azure SQL Database マネージド インスタンス|DMS|オンライン|[チュートリアル](https://docs.microsoft.com/azure/dms/tutorial-sql-server-managed-instance-online)|
|RDS SQL Server|Azure SQL Database (または Managed Instance)|DMS|オンライン|[チュートリアル](https://docs.microsoft.com/azure/dms/tutorial-rds-sql-server-azure-sql-and-managed-instance-online)|
|MySQL|Azure Database for MySQL|DMS|オンライン|[チュートリアル](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online)|
|PostgreSQL|Azure Database for PostgreSQL|DMS|オンライン|[チュートリアル](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online)|
|MondoDB|Azure Cosmos DB Mongo API|DMS|オフライン|[チュートリアル](https://docs.microsoft.com/azure/dms/tutorial-mongodb-cosmos-db)|
|MongoDB|Azure Cosmos DB Mongo API|DMS|オンライン|[チュートリアル](https://docs.microsoft.com/azure/dms/tutorial-mongodb-cosmos-db-online)|
|Oracle|PaaS オプションと IaaS オプションの範囲|サードパーティまたは Azure Migrate|各種|[デシジョン ツリー](../../migrate/expanded-scope/data-oracle-migration.md)|
|さまざまな NoSQL DB|Cosmo DB オプションまたは IaaS オプション|手続き型の移行または Azure Migrate|各種|[デシジョン ツリー](../../migrate/expanded-scope/data-no-sql-migration.md)|
