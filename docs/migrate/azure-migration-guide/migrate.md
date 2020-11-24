---
title: 資産の移行
description: ネイティブ ツール、サードパーティ製ツール、プロジェクト管理ツールなど、使用する適切なツールを特定して、Azure への移行を開始します。
author: matticusau
ms.author: mlavery
ms.date: 09/02/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: migrate
ms.custom: fasttrack-new, AQC
ms.localizationpriority: high
ms.openlocfilehash: 2abab02b5d73bd69b33c8124fb4500129e97e545
ms.sourcegitcommit: a7eb2f6c4465527cca2d479edbfc9d93d1e44bf1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2020
ms.locfileid: "94712427"
---
# <a name="deploy-workloads-and-assets-infrastructure-apps-and-data"></a>ワークロードと資産 (インフラストラクチャ、アプリ、データ) のデプロイ

このフェーズでは、評価フェーズの出力を使用して環境の移行を開始します。 このガイドは、完了状態に至るための適切なツールの特定に役立ちます。 ネイティブ ツール、サードパーティ製ツール、およびプロジェクト管理ツールについて確認します。

## <a name="native-migration-tools"></a>[ネイティブ移行ツール](#tab/Tools)

以下のセクションでは、移行を実施または支援するために使用できるネイティブ Azure ツールについて説明します。 移行作業をサポートする適切なツールの選択については、[クラウド導入フレームワークの移行ツール決定ガイド](../../decision-guides/migrate-decision-guide/index.md)をご覧ください。

### <a name="azure-migrate"></a>Azure Migrate

Azure Migrate では、統合型の拡張可能な移行エクスペリエンスが提供されます。 Azure Migrate は、評価と Azure への移行の各フェーズにわたって移行の道のりを追跡するための、ワンストップの専用エクスペリエンスを提供します。 好みのツールを使用し、これらのツール間で移行の進行状況を追跡するためのオプションを提供します。

Azure Migrate は、オンプレミスのサーバー、インフラストラクチャ、アプリケーション、およびデータの評価と Azure への移行を行うための一元的なハブです。 WCF インターセプターには、次の機能があります。

- 評価、移行、および進行状況の追跡の機能を備えた統合プラットフォーム。
- 強化された評価および移行機能:
  - Hyper-V と VMware を含むオンプレミスのサーバー。
  - VMware 仮想マシンの Azure へのエージェントレス移行。
  - Azure SQL Database または SQL Managed Instance へのデータベースの移行
  - Web アプリケーション
  - Azure の Windows Virtual Desktop に対する仮想デスクトップ インフラストラクチャ (VDI)
  - Azure Data Box 製品を使用した大規模なデータ収集
- ISV 統合 (Cloudamize など) による拡張可能なアプローチ。

Azure Migrate を使用して移行を実行するには、次の手順に従います。

1. **[すべてのサービス]** で Azure Migrate を検索します。 **[Azure Migrate]** を選択して続行します。
1. **[ツールの追加]** を選択して移行プロジェクトを開始します。
1. 移行をホストするサブスクリプション、リソース グループ、および地域を選択します。
1. 次を選択します: **[評価ツールの選択]**  >  **[Azure Migrate:Server Assessment]**  >  **[次へ]** 。
1. **[ツールの確認と追加]** を選択し、構成を確認します。 **[ツールの追加]** を選択して、移行プロジェクトの作成と選択したソリューションの登録のジョブを開始します。

> [!NOTE]
> 特定のシナリオに固有のガイダンスについては、チュートリアルと Azure Migrate の [ドキュメント](/azure/migrate/migrate-services-overview)を参照してください。
>

#### <a name="learn-more"></a>詳細情報

- [Azure Migrate について](/azure/migrate/migrate-services-overview)
- [Azure Migrate のチュートリアル - 物理または仮想サーバーを Azure に移行する](/azure/migrate/tutorial-migrate-physical-virtual-machines)

### <a name="azure-database-migration-service"></a>Azure Database Migration Service

Azure Database Migration Service は、ダウンタイムを最小限に抑えながら、複数のデータベース ソースから Azure データ プラットフォームへのシームレスな移行 (オンライン移行) を実現するフル マネージド サービスです。 Database Migration Service は、必要なすべての手順を実行します。 Microsoft が推奨するベスト プラクティスをプロセスで利用することが保証されている状態で移行プロジェクトを開始できます。

#### <a name="create-an-azure-database-migration-service-instance"></a>Azure Database Migration Service インスタンスを作成する

Azure Database Migration Service を初めて使用する場合は、Azure サブスクリプションのリソース プロバイダーを登録する必要があります。

1. **[すべてのサービス]**  >  **[サブスクリプション]** の順に選択し、ターゲット サブスクリプションを選択します。
1. **[リソース プロバイダー]** を選択します。
1. `migration` を検索し、**Microsoft.DataMigration** の右側にある **[登録]** を選択します。

::: zone target="chromeless"

::: form action="OpenBlade[#blade/Microsoft_Azure_Billing/SubscriptionsBlade]" submitText="Go to Subscriptions" :::

::: zone-end

リソース プロバイダーを登録した後は、Azure Database Migration Service のインスタンスを作成できます。

1. **[+ リソースの作成]** を選択し、マーケットプレースで **Azure Database Migration Service** を検索します。
1. 移行サービスの作成ウィザードを完了し、 **[作成]** を選択します。

これでサービスによって、サポートされているソース データベースをターゲット プラットフォーム (SQL Server、MySQL、PostgreSQL、MongoDB など) に移行する準備ができました。

::: zone target="chromeless"

::: form action="Create[#create/Microsoft.AzureDMS]" submitText="Create an Azure Database Migration Service instance" :::

::: zone-end

::: zone target="docs"

詳細については、次を参照してください。

- [Azure Database Migration Service の概要](/azure/dms/dms-overview)
- [Azure Database Migration Service のインスタンスを作成する](/azure/dms/quickstart-create-data-migration-service-portal)
- [Azure portal での Azure Migrate](https://portal.azure.com/#blade/Microsoft_Azure_ManagementGroups/HierarchyBlade)
- [Azure portal: 移行プロジェクトを作成する](https://portal.azure.com/#create/Microsoft.AzureMigrate)

::: zone-end

### <a name="azure-app-service-migration-assistant"></a>Azure App Service Migration Assistant

Azure App Service Migration Assistant は、組織のクラウドへの移行を支援する[大規模なアプリケーション スイート](https://azure.microsoft.com/services/azure-migrate/)の一部です。 Migration Assistant には、2 つのタスクを実行する、ウィザードに似たユーザー エクスペリエンスが用意されています。

1. Web アプリの移行前互換性チェックを実行して、Windows Server にインストールされている特定の Web アプリの評価を実行し、Web アプリに変更を加えることなく Azure App Service に移行できるかどうかを判断します。
1. この評価によって、Web アプリが移行可能であることが証明された場合、Migration Assistant により移行が行われます。 Azure アカウントへのアクセスを Migration Assistant に提供し、使用するリソース グループを選択して、Web アプリの名前を選び、その他の同様の詳細を指定する必要があります。

または、Migration Assistant によって、より自動化された反復可能な方法で Web アプリを移行するために使用できる Azure Resource Manager テンプレートが生成されます。

#### <a name="migrate-a-web-app-to-azure-app-service"></a>Web アプリを Azure App Service に移行する

Migration Assistant では、Azure アカウントに関する重要な詳細情報を収集することから移行プロセスが開始され、移行が実行されます。

まず、Azure アカウントにサインインし、一意のコードを使用して Migration Assistant セッションをアカウントに関連付けます。 次に、サブスクリプション、リソース グループ、Web サイトのドメイン名を選択します。 新しい Azure App Service プランを作成してアプリをホストするか、既存のプランを選択するかを選択できます。 その選択は、アプリがホストされる地理的なリージョンに影響します。 さらに、この移行作業を既存の Azure Migrate プロジェクトに関連付けることもできます 最後に、データベースのセットアップをスキップするか、ハイブリッド接続を設定してデータベース接続を有効にするかを選択できます。

Migration Assistant によって選択内容が収集および検証されると、選択したリージョンとリソース グループに必要な Azure App Service リソースが作成されます。 Web アプリのソース ファイルが zip され、Azure App Service のデプロイ API を使用してデプロイされます。 最後に、ハイブリッド接続の設定の支援など、オプションの移行手順が実行されます。

移行が正常に完了したら、移行後のタスクを実行する必要があります。 たとえば、次のようなものがあります。

- web.config ファイル内のアプリケーションの設定と接続文字列を Azure App Service に手動で移動する。
- オンプレミスの SQL Server インスタンスから Azure SQL データベースにデータを移行する。
- SSL 証明書を設定する。
- カスタム ドメイン名を設定する。
- Azure Active Directory でアクセス許可を設定する。

また、Azure App Service のホスティング プランや、自動スケールやデプロイ スロットなどのその他の設定を変更することもできます。

詳細情報

[ASP.NET アプリを Azure に移行する](/learn/paths/migrate-dotnet-apps-azure)

### <a name="data-migration-assistant"></a>Data Migration Assistant

Data Migration Assistant (DMA) を使用すると、SQL Server または Azure SQL Database の新しいバージョンでデータベースの機能に影響する可能性がある互換性の問題を検出することで、最新のデータ プラットフォームへのアップグレードを支援します。 DMA は、ターゲット環境のパフォーマンスと信頼性を高めるための推奨事項を提案し、スキーマ、データ、非コンテナー化オブジェクトをソース サーバーからターゲット サーバーに移動できるようにします。

Data Migration Assistant は Azure Migrate と統合されているため、Azure Migrate ダッシュボードですべての評価の進行状況を追跡できます。 Azure Migrate: Database Assessment ツールを追加して Azure Migrate から DMA を起動し、DMA の [Azure Migrate にアップロードする] ボタンを選択してデータベース評価を Azure Migrate に追加します。

> [!NOTE]
> (データベースの数とサイズの観点から) 大規模な移行の場合、データベースを大規模に移行できる Azure Database Migration Service を使用することをお勧めします。
>

次の手順に従って、Data Migration Assistant の使用を開始します。

1. [Microsoft ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=53595)から Data Migration Assistant をダウンロードしてインストールします。
1. **[新規作成]\(+\)** アイコンを選択し、 **[評価]** プロジェクト タイプを選択して評価を作成します。
1. ソースとターゲットのサーバーの種類を設定し、 **[作成]** を選択します。
1. 必要に応じて評価オプションを構成します (すべて既定値をお勧めします)。
1. 評価するデータベースを追加します。
1. **[次へ]** を選択して評価を開始します。
1. Data Migration Assistant で結果を表示します。

企業では、「[DMA で企業を評価し評価レポートを統合する](/sql/dma/dma-consolidatereports)」で説明されている方法に従って、複数のサーバーを評価し、レポートを結合した後、提供された Power BI レポートを使用して結果を分析することをお勧めします。

詳しい使用手順などの詳細については、以下を参照してください。

- [Data Migration Assistant の概要](/sql/dma/dma-overview)
- [DMA で企業を評価し評価レポートを統合する](/sql/dma/dma-consolidatereports)
- [Data Migration Assistant によって作成された統合評価レポートを Power BI で分析する](/sql/dma/dma-powerbiassesreport)

### <a name="sql-server-migration-assistant"></a>SQL Server Migration Assistant

Microsoft SQL Server Migration Assistant (SSMA) は、Microsoft Access、DB2、MySQL、Oracle、SAP ASE から SQL Server へのデータベース移行を自動化することを目的としたツールです。 一般的な概念は、これらのツールを使用して収集、評価、レビューを行うことですが、ソース システムごとにプロセスが異なるため、詳細な [SQL Server Migration Assistant のドキュメント](/sql/ssma/sql-server-migration-assistant)を確認することをお勧めします。

詳細については、次を参照してください。

- [SQL Server Migration Assistant の概要](/sql/ssma/sql-server-migration-assistant)

### <a name="database-experimentation-assistant"></a>Database Experimentation Assistant

Database Experimentation Assistant (DEA) は、SQL Server アップグレード用の新しい A/B テスト ソリューションです。 特定のワークロード用の SQL のターゲット バージョンを評価する際に役立ちます。 SQL Server の以前のバージョン (SQL Server 2005 以降) から SQL Server の新しいバージョンにアップグレードするお客様は、これらの分析メトリックを使用できます。

Database Experimentation Assistant には、次のワークフロー アクティビティが含まれています。

- **キャプチャ:** SQL Server A/B テストの最初のステップでは、ソース サーバーでトレースをキャプチャします。 通常、ソース サーバーは実稼働サーバーです。
- **再生:** SQL Server A/B テストの 2 番目のステップでは、キャプチャされたトレース ファイルをターゲット サーバーで再生します。 次に、分析のために、再生から大量のトレースを収集します。
- **分析:** 最後のステップでは、再生トレースを使用して分析レポートを生成します。 分析レポートは、提案された変更がパフォーマンスに与える影響についての分析情報を得るために役立ちます。

詳細については、次を参照してください。

- [Database Experimentation Assistant の概要](/sql/dea/database-experimentation-assistant-overview)

### <a name="azure-cosmos-db-data-migration-tool"></a>Azure Cosmos DB データ移行ツール

Azure Cosmos DB データ移行ツールでは、さまざまなソースから Azure Cosmos DB コレクションおよびテーブルにデータをインポートできます。 JSON ファイル、CSV ファイル、SQL、MongoDB、Azure Table Storage、Amazon DynamoDB、さらには Azure Cosmos DB SQL API コレクションからインポートすることができます。 データ移行ツールは、SQL API の単一パーティション コレクションから複数パーティション コレクションに移行する際にも使用できます。

詳細については、次を参照してください。

- [Azure Cosmos DB データ移行ツール](/azure/cosmos-db/import-data)

## <a name="third-party-migration-tools"></a>[サード パーティの移行ツール](#tab/third-party-tools)

移行プロセスを支援する、サード パーティの各種移行ツールおよび ISV サービスを利用できます。 それぞれ利点や長所が異なります。 これには以下のツールが含まれます。

### <a name="unifycloud"></a>UnifyCloud

UnifyCloud は、評価、移行、および最新化の自動化ツールを提供する ISV サービスです。

[詳細情報](https://www.unifycloud.com)

### <a name="cloudamize"></a>Cloudamize

Cloudamize は、移行戦略のすべてのフェーズに対応した ISV サービスです。

[詳細情報](https://www.cloudamize.com)

### <a name="zerto"></a>Zerto

Zerto は、Microsoft Hyper-V 環境と VMware vSphere 環境の両方に対応した仮想レプリケーションを提供します。

[詳細情報](https://www.zerto.com/modernize)

### <a name="carbonite"></a>Carbonite

Carbonite は、あらゆる物理環境、仮想環境、またはクラウド ベース環境の間でワークロードを移行するためのサーバーおよびデータ移行ソリューションを提供します。

[詳細情報](https://www.carbonite.com/data-protection/data-migration-software)

### <a name="movere"></a>Movere

Movere は、クラウド移行の計画と IT 環境の継続的な最適化、監視、分析を自信を持って行うために必要なデータや分析情報を提供する検出ソリューションです。

[詳細情報](https://www.movere.io)

### <a name="azure-cosmos-db-partners"></a>Azure Cosmos DB パートナー

NoSQL データベースの要件に合わせて Azure Cosmos DB の移行をサポートするために、多様で経験豊富なシステム インテグレーターのパートナーやツールから選択できます。

[詳細情報](/azure/cosmos-db/partners-migration-cosmosdb#migration-tools)

[Azure Migration Center](https://azure.microsoft.com/migration/support) では、移行シナリオに合わせてすぐに使えるパートナー テクノロジ ソリューションを提供する組織を見つけたり、サード パーティのその他の移行ツールやサポート サービスの詳細を確認したりできます。

さまざまなデータベース移行オプションと、ネイティブおよびパートナーとのステップバイステップ ガイダンスについては、「[Azure データベース移行ガイド](https://datamigration.microsoft.com)」を参照してください。

## <a name="project-management-tools"></a>[プロジェクト管理ツール](#tab/project-management-tools)

追跡も管理もされていないプロジェクトでは、問題が発生しやすくなります。 確実に成功を収めるためには、お客様がプロジェクト管理ツールを使用することが重要であると Microsoft では考えています。 利用可能なさまざまなツールがあり、組織のプロジェクト マネージャーが気に入っているツールが既にあるかもしれません。

Azure DevOps は、クラウド移行時のプロジェクト管理に推奨されるツールです。 Azure DevOps の使用を高速化するために、クラウド導入フレームワークには、プロジェクト テンプレートを自動的にデプロイするためのツールが含まれています。 このテンプレートには、移行作業中に一般的に実行されるタスクが含まれています。 [クラウド導入プランと Azure DevOps](/azure/architecture/cloud-adoption/plan/template) の手順に従って、テンプレートをデプロイします。 これで、移行する[ワークロード](/azure/architecture/cloud-adoption/plan/workloads)と[資産](/azure/architecture/cloud-adoption/plan/assets)を反映するようにテンプレートを変更できるようになります。

Microsoft では以下のプロジェクト管理ツールも用意しており、これらを併用してより広範な機能を提供できます。

- [Microsoft Planner](https://tasks.office.com): チームワークを組織化するためのシンプルで視覚的な方法。
- [Microsoft Project](https://products.office.com/project/project-and-portfolio-management-software):プロジェクトおよびポートフォリオ管理、リソース容量管理、財務管理、タイムシートおよびスケジュール管理。
- [Microsoft Teams](https://products.office.com/microsoft-teams): チームのコラボレーションとコミュニケーションのためのツール。 Teams を Planner や他のツールと統合して、コラボレーションを強化することもできます。
- [Azure DevOps Services](/azure/devops/user-guide/what-is-azure-devops?view=azure-devops):Azure DevOps を使用するために、クラウド導入フレームワークの計画テンプレートは必要ありません。 テンプレートを使用せずにサービスを使用して、コードとしてインフラストラクチャを管理することや、作業項目とボードを使用してプロジェクト管理を実行することができます。 発展に合わせて、組織で CI/CD 機能を利用できます。

使用可能なプロジェクト管理ツールはこれらのツールに限られません。 他にも多くのサード パーティ製ツールが、プロジェクト管理コミュニティで広く使用されています。

### <a name="set-up-for-devops"></a>DevOps 用の設定

クラウド テクノロジへの移行は、DevOps や CI/CD に合わせて組織を再編するまたとない機会です。 組織で管理するのがインフラストラクチャだけの場合でも、インフラストラクチャをコードとして管理し、DevOps 向けの業界のパターンやプラクティスを使用するようになれば、CI/CD パイプラインによって機敏性の向上に着手し、変化、成長、リリース、さらには復旧の各シナリオにより迅速に適応できるようになります。

Azure DevOps では、必須機能が用意されているほか、Azure、オンプレミス環境、さらには他のクラウドとの統合が実現されます。 詳細については、[Azure DevOps](https://azure.microsoft.com/services/devops) のページをご覧ください。 ガイド付きトレーニングについては、[クイックスタート: Azure DevOps を使用した CI/CD](https://microsoft.github.io/PartsUnlimited/pandp/200.1x-PandP-CICDQuickstartwithVSTS.html) に関するページを参照してください。

### <a name="suggested-skills"></a>推奨されるスキル

Microsoft Learn は学習に対する新しいアプローチです。 クラウド導入に伴う新たなスキル上の責任に対する準備は容易にできるものではありません。 Microsoft Learn では、目標を早く達成するのに役立つ、実践的な学習に対するより価値あるアプローチを提供します。 ポイントとレベルを獲得し、さらなる達成を目指しましょう。

ここでは、クラウド導入フレームワークにおける DevOps ガイダンスのセットアップを補完する Microsoft Learn のカスタマイズされたラーニング パスの例を示します。

[Azure DevOps でアプリケーションを構築する](/learn/paths/build-applications-with-azure-devops):Azure Pipelines と GitHub を使って、他の人と共同でアプリケーションを構築します。 パイプラインで自動テストを実行して、コードの品質を検証します。 潜在的な脆弱性がないかどうかソース コードとサードパーティ コンポーネントをスキャンします。 アプリケーションを構築するために連携させる複数のパイプラインを定義します。 Microsoft がホストするエージェントと独自のビルド エージェントの両方を使用して、アプリケーションを構築します。

## <a name="cost-management"></a>[コスト管理](#tab/ManageCost)

リソースをクラウド環境に移行するときは、定期的なコスト分析を実行することが重要です。 移行プロセスによって追加の使用量要件がサービスに適用される場合があるため、定期的なコスト分析により想定外の使用料金の発生を回避できます。 コストとワークロードのバランスを取るために、必要に応じてリソースのサイズを変更することもできます。詳細については、[最適化と変換](./optimize-and-transform.md)に関するセクションをご覧ください。
