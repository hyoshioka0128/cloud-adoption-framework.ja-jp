---
title: データ オプションを確認する
description: Azure 向けのクラウド導入フレームワークを使用して、ワークロードのホストに必要なデータ要件を判断する方法を学習します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 05/15/2019
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.openlocfilehash: 9ad7787dd44314e0c83b55d5d799a111de021f6c
ms.sourcegitcommit: 07d56209d56ee199dd148dbac59671cbb57880c0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88885067"
---
# <a name="review-your-data-options"></a>データ オプションを確認する

クラウド導入のためのランディング ゾーン環境を準備するとき、ワークロードをホストするためのデータ要件を決定する必要があります。 Azure のデータベース製品とサービスは、さまざまなデータ ストレージのシナリオと機能をサポートしています。 データ要件に対応するためのランディング ゾーン環境の構成方法は、ワークロードのガバナンス、技術的要件、ビジネス要件によって異なります。

## <a name="identify-data-services-requirements"></a>データ サービスの要件を特定する

ランディング ゾーンの評価と準備の一環として、ランディング ゾーンでサポートする必要があるデータ ストアを特定する必要があります。 このプロセスには、データ ストレージ要件とアクセス要件を決定するためにワークロードを構成する各アプリケーションとサービスを評価する作業が含まれます。 これらの要件を特定して文書化すると、ランディング ゾーンのポリシーを作成して、ワークロードのニーズに基づいて許可されるリソースの種類を制御できます。

ランディング ゾーン環境にデプロイする各アプリケーションまたはサービスに対しては、次のデシジョン ツリーを出発点として使用すると、適切なデータ ストア サービスを決定して使用するのに役立ちます。

![Azure データベース サービスのデシジョン ツリー](../../_images/ready/data-decision-tree.png)
_図 1: Azure データベース サービスのデシジョン ツリー。_

### <a name="key-questions"></a>主な質問

ワークロードに関する次の質問に答えると、Azure データベース サービスのデシジョン ツリーに基づいて意思決定を行うのに役立ちます。

- **データベース ソフトウェアやホスト OS の完全な制御または所有権が必要ですか?** 一部のシナリオでは、データベース ワークロードのソフトウェア構成とホスト サーバーに関する高度な制御または所有権が必要です。 これらのシナリオでは、データ サービスのデプロイと構成を完全に制御できるカスタムのサービスとしてのインフラストラクチャ (IaaS) 仮想マシンをデプロイできます。 このような要件がない場合は、サービスとしてのプラットフォーム (PaaS) データベース サービスを使用すると、管理コストと運用コストが削減される可能性があります。
- **ワークロードでリレーショナル データベース テクノロジを使用しますか?** そうである場合は、どのようなテクノロジを使用する予定ですか? Azure では、[Azure SQL Database](/azure/sql-database/sql-database-technical-overview)、[MySQL](/azure/mysql/overview)、[PostgreSQL](/azure/postgresql/overview)、[MariaDB](/azure/mariadb/overview) に対する PaaS のマネージド データベース機能を提供します。
- **ワークロードで SQL Server を使用しますか?** Azure では、[Azure Virtual Machines 上の SQL Server](/azure/azure-sql/virtual-machines) (IaaS ベース) または [Azure SQL Database ホステッド サービス](/azure/sql-database/sql-database-technical-overview) (PaaS ベース) でワークロードを実行できます。 どちらのオプションを使用するかは、主に、データベースの管理、修正プログラムの適用、バックアップの実行を自分で行うか、あるいはこれらの操作を Azure に委任するかどうかの問題です。 シナリオによっては、互換性の問題により、IaaS でホストされる SQL Server の使用が必要になることがあります。 ワークロードに適したオプションを選択する方法の詳細については、「[Azure で適切な SQL Server オプションを選択する](/azure/sql-database/sql-database-paas-vs-sql-server-iaas)」を参照してください。
- **ワークロードでキー/値データベース ストレージを使用しますか?** [Azure Cache for Redis](/azure/azure-cache-for-redis/cache-overview) は、高速でスケーラブルなアプリケーションを実現できる、高性能のキャッシュ キー/値データ ストレージ ソリューションを提供します。 [Azure Cosmos DB](/azure/cosmos-db/introduction) も、汎用的なキー/値ストレージ機能を提供します。
- **ワークロードでドキュメントまたはグラフ データを使用しますか?** [Azure Cosmos DB](/azure/cosmos-db/introduction) は、さまざまなデータ型と API をサポートするマルチモデル データベース サービスです。 Azure Cosmos DB には、ドキュメントおよびグラフ データベースの機能もあります。
- **ワークロードで列ファミリ データを使用しますか?** [Azure HDInsight の Apache HBase](/azure/hdinsight/hbase/apache-hbase-overview) は、Apache Hadoop 上に構築されています。 列ファミリ別に編成された、スキーマレス データベース内の大量の非構造化データおよび半構造化データがサポートされています。
- **ワークロードに大容量のデータ分析機能が必要ですか?** [Azure SQL Data Warehouse](/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) を使用すると、構造化されたペタバイト規模のデータを効率的に格納し、クエリを実行できます。 非構造化ビッグ データ ワークロードの場合、[Azure Data Lake](https://azure.microsoft.com/solutions/data-lake) を使用すると、ペタバイト規模のファイルと数兆個のオブジェクトを保存して分析できます。
- **ワークロードに検索エンジンの機能が必要ですか?** [Azure Search](/azure/search/search-what-is-azure-search) を使用すると、アプリケーションに統合できる、AI で強化されたクラウドベースの検索インデックスを作成できます。
- **ワークロードには時系列データを使用しますか?** [Azure Time Series Insights](/azure/time-series-insights/time-series-insights-overview) は、IoT デバイスによって生成されたデータなどの大量の時系列データの格納、視覚化、クエリ実行のために構築されています。

> [!NOTE]
> 各アプリケーションまたはサービスのデータベース オプションを評価する方法の詳細については、「[Azure アプリケーション アーキテクチャ ガイド](/azure/architecture/guide/technology-choices/data-store-comparison)」で確認してください。

## <a name="common-database-scenarios"></a>一般的なデータベース シナリオ

次の表は、いくつかの一般的な使用シナリオの要件と、その処理に推奨されるデータベース サービスを示しています。

| シナリオ  | データ サービス |
|---|---|
| NoSQL の選択をサポートする、グローバルに分散されたマルチモデル データベースが必要です。 | [Azure Cosmos DB](/azure/cosmos-db/introduction) |
| 迅速なプロビジョニングと即座のスケーリングが可能な、インテリジェンスとセキュリティを組み込んだ、フル マネージド リレーショナル データベースが必要です。 | [Azure SQL Database](/azure/sql-database/sql-database-technical-overview) |
| 高可用性とセキュリティを追加料金なしで組み込んだ、フル マネージドかつスケーラブルな MySQL リレーショナル データベースが必要です。 | [Azure Database for MySQL](/azure/mysql/overview) |
| 高可用性とセキュリティを追加料金なしで組み込んだ、フル マネージドかつスケーラブルな PostgreSQL リレーショナル データベースが必要です。 | [Azure Database for PostgreSQL](/azure/postgresql/overview) |
| クラウドでエンタープライズ SQL Server アプリをホストし、サーバー OS を完全に制御する予定です。 | [SQL Server on Virtual Machines](/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview) |
| 広範なセキュリティが追加料金なしであらゆるレベルに組み込まれた、フル マネージドのエラスティック データ ウェアハウスが必要です。 | [Azure SQL Data Warehouse](/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) |
| Hadoop クラスターまたは HDFS データをサポートするデータ レイク用のストレージ リソースが必要です。 | [Azure Data Lake](https://azure.microsoft.com/solutions/data-lake) |
| 高速でスケーラブルなアプリケーションをサポートするために、高スループットで、一貫性があり、待機時間の短いデータ アクセスが必要です。 | [Azure Cache for Redis](/azure/azure-cache-for-redis/cache-overview) |
| 高可用性とセキュリティを追加料金なしで組み込んだ、フル マネージドかつスケーラブルな MariaDB リレーショナル データベースが必要です。 | [Azure Database for MariaDB](/azure/mariadb/overview) |

## <a name="regional-availability"></a>リージョン別の提供状況

Azure を利用すれば、**場所を問わず**顧客とパートナーを獲得するために必要なスケールでサービスを提供できます。 クラウドのデプロイを計画する際の重要な要素として、ワークロード リソースをホストする Azure リージョンの決定があります。

ほとんどのデータベース サービスは、ほとんどの Azure リージョンで一般提供されています。 しかし、これらの製品のサブセットのみをサポートするリージョンもいくつかあります (ほとんどの場合は政府機関の顧客が対象となります)。 どのリージョンにデータベース リソースをデプロイするかを決定する前に、[リージョン ページ](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=data-factory,sql-server-stretch-database,redis-cache,database-migration,sql-data-warehouse,postgresql,mariadb,cosmos-db,mysql,sql-database)を参照して、利用可能なリージョンの最新の状態を確認することをお勧めします。

Azure のグローバル インフラストラクチャの詳細については、[Azure リージョンのページ](https://azure.microsoft.com/global-infrastructure/regions)を参照してください。 また、各 Azure リージョンで利用可能なサービス全体の具体的な詳細については、[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=all)のページで確認できます。

## <a name="data-residency-and-compliance-requirements"></a>データの保存場所とコンプライアンスの要件

多くの場合は、データ ストレージに関連した法律上および契約上の要件がワークロードに適用されます。 これらの要件は、組織の場所、データ ストアをホストしている物理的な資産の管轄区域、および該当する事業部門によって異なることがあります。 考慮すべきデータに関する義務の要素には、データ分類、データの場所、および共同責任モデルに基づくデータ保護に関するそれぞれの責任が含まれます。 これらの要件の理解に役立つ情報として、ホワイト ペーパー「[Azure を使用して基準に準拠しているデータの保存場所とセキュリティを実現する](https://azure.microsoft.com/resources/achieving-compliant-data-residency-and-security-with-azure)」をご覧ください。

コンプライアンスの取り組みの一環として、データベース リソースが物理的に配置されている場所の制御が必要になる場合があります。 Azure リージョンは地域と呼ばれるグループに編成されています。 [Azure の地域](https://azure.microsoft.com/global-infrastructure/geographies)では、データの保存場所、主権、コンプライアンス、回復性に関する要件が地域的および政治的な境界内で確実に遵守されます。 ワークロードがデータ主権やその他のコンプライアンス要件の影響を受ける場合は、要件に準拠している Azure 地域のリージョンにストレージ リソースをデプロイする必要があります。

## <a name="establish-controls-for-database-services"></a>データベース サービスの制御を設定する

ランディング ゾーン環境を準備するときに、ユーザーがデプロイできるデータ ストアを制限する制御を設定できます。 この制御は、開発者や IT チームがワークロードをサポートするために必要なリソースを引き続きデプロイおよび構成できるようにしながら、コストを管理し、セキュリティ リスクを制限するのに役立ちます。

ランディング ゾーンの要件を特定して文書化したら、[Azure Policy](/azure/governance/policy/overview) を使用して、ユーザーに作成を許可するデータベース リソースを制御できます。 これらの制御は、[データベース リソースの種類の作成を許可または拒否する](/azure/governance/policy/samples/allowed-resource-types)という形をとることができます。 たとえば、ユーザーが Azure SQL Database リソースのみを作成するように制限することができます。 また、ポリシーを使用して、リソースが作成されるときに指定可能なオプションを制御することもできます。たとえば、[プロビジョニングできる SQL Database SKU を制限](/azure/governance/policy/samples/allowed-sql-db-skus)したり、IaaS VM へのインストールを[特定バージョンの SQL Server のみ許可](/azure/governance/policy/samples/require-sql-12)したりできます。

ポリシーは、リソース、リソース グループ、サブスクリプション、および管理グループにスコープを設定できます。 これらのポリシーを [Azure Blueprint](/azure/governance/blueprints/overview) の定義に含め、クラウド資産全体で繰り返し適用できます。
