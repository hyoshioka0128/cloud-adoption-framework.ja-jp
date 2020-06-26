---
title: 資産の移行
description: ネイティブ ツール、サードパーティ製ツール、プロジェクト管理ツールなど、使用する適切なツールを特定して、Azure への移行を開始します。
author: matticusau
ms.author: mlavery
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: migrate
ms.custom: fasttrack-new, AQC
ms.localizationpriority: high
ms.openlocfilehash: d8b0e83352fdaf0151f93e5fcd85036d843ce886
ms.sourcegitcommit: 9b183014c7a6faffac0a1b48fdd321d9bbe640be
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/19/2020
ms.locfileid: "85075250"
---
<!-- cSpell:ignore Cloudamize agentless uncontained SSMA Carbonite Movere -->

# <a name="deploy-workloads-and-assets-infrastructure-apps-and-data"></a>ワークロードと資産 (インフラストラクチャ、アプリ、データ) のデプロイ

このフェーズでは、評価フェーズの出力を使用して環境の移行を開始します。 このガイドは、ネイティブ ツール、サード パーティ製ツール、プロジェクト管理ツールなど、完了状態に到達するための適切なツールを識別するのに役立ちます。

<!-- markdownlint-disable MD025 -->

# <a name="native-migration-tools"></a>[ネイティブ移行ツール](#tab/Tools)

以下のセクションでは、移行を実施または支援するために使用できるネイティブ Azure ツールについて説明します。 移行作業をサポートする適切なツールの選定に関する情報については、[クラウド導入フレームワークの移行ツール決定ガイド](../../decision-guides/migrate-decision-guide/index.md)を参照してください。

## <a name="azure-migrate"></a>Azure Migrate

Azure Migrate では、統合型の拡張可能な移行エクスペリエンスが提供されます。 Azure Migrate は、評価と Azure への移行の各フェーズにわたって移行の道のりを追跡するための、ワンストップの専用エクスペリエンスを提供します。 好みのツールを使用し、これらのツール間で移行の進行状況を追跡するためのオプションを提供します。

Azure Migrate は以下の機能を提供します。

1. 強化された評価および移行機能:
    - Hyper-V 評価。
    - 改良された VMware 評価。
    - VMware 仮想マシンの Azure へのエージェントレス移行。
1. 評価、移行、進捗状況を統合的に追跡。
1. ISV 統合 (Cloudamize など) による拡張可能なアプローチ。

Azure Migrate を使用して移行を実行するには、次の手順に従います。

1. **[すべてのサービス]** で Azure Migrate を検索します。 **[Azure Migrate]** を選択して続行します。
1. **[ツールの追加]** を選択して移行プロジェクトを開始します。
1. 移行をホストするサブスクリプション、リソース グループ、および地域を選択します。
1. 次を選択します: **[評価ツールの選択]**  >  **[Azure Migrate:Server Assessment]**  >   **[次へ]** 。
1. **[ツールの確認と追加]** を選択し、構成を確認します。 **[ツールの追加]** を選択して、移行プロジェクトの作成と選択したソリューションの登録のジョブを開始します。

### <a name="learn-more"></a>詳細情報

- [Azure Migrate のチュートリアル - 物理または仮想サーバーを Azure に移行する](https://docs.microsoft.com/azure/migrate/tutorial-migrate-physical-virtual-machines)

## <a name="azure-site-recovery"></a>Azure Site Recovery

Azure Site Recovery サービスは、オンプレミス リソースの Azure への移行を管理できます。 また、オンプレミス マシンと Azure VM のディザスター リカバリーを管理および調整して、事業継続とディザスター リカバリー (BCDR) の目的を達成することもできます。

以下の手順は、Site Recovery を使用して移行するプロセスの概要です。

> [!TIP]
> シナリオによっては、以下の手順は若干異なる場合があります。 詳細については、[オンプレミスのマシンの Azure への移行](https://docs.microsoft.com/azure/site-recovery/migrate-tutorial-on-premises-azure)に関する記事を参照してください。

### <a name="prepare-azure-site-recovery-service"></a>Azure Site Recovery サービスを準備する

1. Azure portal で、 **[+リソースの作成] > [管理ツール] > [Backup and Site Recovery]\(バックアップと Site Recovery\)** の順に選択します。
1. まだ復旧コンテナーを作成していない場合は、ウィザードを完了して **Recovery Services コンテナー** リソースを作成します。
1. **[リソース]** メニューで、 **[Site Recovery] > [インフラストラクチャの準備] > [保護の目標]** の順に選択します。
1. **[保護の目標]** で、何を移行するかを選択します。
    1. **VMware:** **[To Azure]\(Azure へ\) > [はい。VMware vSphere Hypervisor を使用します]** の順に選択します。
    1. **物理マシン:** **[To Azure]\(Azure へ\) > [非仮想化/その他]** の順に選択します。
    1. **Hyper-V:** **[To Azure]\(Azure へ\) > [はい。Hyper-V を使用します]** の順に選択します。 Hyper-V VM が VMM で管理される場合は **[はい]** を選択します。

### <a name="configure-migration-settings"></a>移行の設定の構成

1. ソース環境を適宜設定します。
1. ターゲット環境を設定します。
    1. **[インフラストラクチャの準備] > [ターゲット]** の順に選択し、使用する Azure サブスクリプションを選択します。
    1. Resource Manager デプロイ モデルを指定します。
    1. Site Recovery によって、互換性のある Azure ストレージ アカウントとネットワークが 1 つ以上あるかどうかが確認されます。
1. レプリケーション ポリシーを設定します。
1. レプリケーションを有効にします。
1. テスト移行 (テスト フェールオーバー) を実行します。

### <a name="migrate-to-azure-using-failover"></a>フェールオーバーを使用して Azure に移行する

1. **[設定] > [レプリケートされたアイテム]** で、[マシン] > **[フェールオーバー]** の順に選択します。
1. **[フェールオーバー]** で、フェールオーバーする **[復旧ポイント]** を選択します。 最新の復旧ポイントを選択します。
1. 必要に応じて、暗号化キーの設定を構成します。
1. **[フェールオーバーを開始する前にマシンをシャットダウンします]** を選択します。 Site Recoverty は、仮想マシンのシャットダウンを試行してからフェールオーバーをトリガーします。 仮にシャットダウンが失敗したとしても、フェールオーバーは続行されます。 フェールオーバーの進行状況は [ジョブ] ページで確認できます。
1. 想定どおりに Azure VM が Azure に表示されることを確認します。
1. **[レプリケートされたアイテム]** で VM を右クリックし、 **[移行の完了]** を選択します。
1. 必要に応じて、移行後の手順を実行します (このガイドの関連情報を参照)。

::: zone target="chromeless"

::: form action="Create[#create/Microsoft.RecoveryServices]" submitText="Create a Recovery Services vault" :::

::: zone-end

::: zone target="docs"

詳細については、次を参照してください。

- [オンプレミスのマシンを Azure に移行する](https://docs.microsoft.com/azure/site-recovery/migrate-tutorial-on-premises-azure)

::: zone-end

## <a name="azure-database-migration-service"></a>Azure Database Migration Service

Azure Database Migration Service は、複数のデータベース ソースから Azure データ プラットフォームへのシームレスな移行を最小限のダウンタイムで実現するフル マネージドのサービスです (オンライン移行)。 Azure Database Migration Service は、すべての必要な手順を実行します。 Microsoft が推奨するベスト プラクティスをプロセスで利用することが保証されている状態で移行プロジェクトを開始できます。

### <a name="create-an-azure-database-migration-service-instance"></a>Azure Database Migration Service インスタンスを作成する

Azure Database Migration Service を初めて使用する場合は、Azure サブスクリプションのリソース プロバイダーを登録する必要があります。

1. **[すべてのサービス]** 、 **[サブスクリプション]** の順に選択し、ターゲット サブスクリプションを選択します。
1. **[リソース プロバイダー]** を選択します。
1. `migration` を検索し、**Microsoft.DataMigration** の右側にある **[登録]** を選択します。

::: zone target="chromeless"

::: form action="OpenBlade[#blade/Microsoft_Azure_Billing/SubscriptionsBlade]" submitText="Go to Subscriptions" :::

::: zone-end

リソース プロバイダーを登録した後は、Azure Database Migration Service のインスタンスを作成できます。

1. **[+リソースの作成]** を選択し、マーケットプレースで **Azure Database Migration Service** を検索します。
1. **[移行サービスの作成]** ウィザードを完了し、 **[作成]** を選択します。

これでサービスによって、サポートされているソース データベース (SQL Server、MySQL、PostgreSQL、MongoDB など) を移行する準備ができました。

::: zone target="chromeless"

::: form action="Create[#create/Microsoft.AzureDMS]" submitText="Create an Azure Database Migration Service instance" :::

::: zone-end

::: zone target="docs"

詳細については、次を参照してください。

- [Azure Database Migration Service の概要](https://docs.microsoft.com/azure/dms/dms-overview)
- [Azure Database Migration Service のインスタンスの作成](https://docs.microsoft.com/azure/dms/quickstart-create-data-migration-service-portal)
- [Azure portal での Azure Migrate](https://portal.azure.com/#blade/Microsoft_Azure_ManagementGroups/HierarchyBlade)
- [Azure portal: 移行プロジェクトを作成する](https://portal.azure.com/#create/Microsoft.AzureMigrate)

::: zone-end

## <a name="data-migration-assistant"></a>Data Migration Assistant

Data Migration Assistant (DMA) を使用すると、SQL Server または Azure SQL Database の新しいバージョンでデータベースの機能に影響する可能性がある互換性の問題を検出することによって、最新のデータ プラットフォームにアップグレードすることができます。 DMA は、ターゲット環境のパフォーマンスと信頼性を高めるための推奨事項を提案し、スキーマ、データ、非コンテナー化オブジェクトをソース サーバーからターゲット サーバーに移動できるようにします。

> [!NOTE]
> (データベースの数とサイズを基準にした) 大規模な移行の場合、大規模なデータベースを移行できる Azure Database Migration Service を使用することをお勧めします。
>

次の手順で Data Migration Assistant の使用を開始します。

1. [Microsoft ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=53595)から Data Migration Assistant をダウンロードしてインストールします。
1. **[新規作成]\(+\)** アイコンを選択し、 **[評価]** プロジェクト タイプを選択して評価を作成します。
1. ソースとターゲットのサーバーの種類を設定し、 **[作成]** を選択します。
1. 必要に応じて評価オプションを構成します (すべて既定値をお勧めします)。
1. 評価するデータベースを追加します。
1. **[次へ]** を選択して評価を開始します。
1. Data Migration Assistant ツール セット内で結果を確認します。

企業では、「[DMA で企業を評価し評価レポートを統合する](https://docs.microsoft.com/sql/dma/dma-consolidatereports)」で説明されている方法に従って、複数のサーバーを評価し、レポートを結合した後、提供された Power BI レポートを使用して結果を分析することをお勧めします。

詳しい使用手順などの詳細については、以下を参照してください。

- [Data Migration Assistant の概要](https://docs.microsoft.com/sql/dma/dma-overview)
- [DMA で企業を評価し評価レポートを統合する](https://docs.microsoft.com/sql/dma/dma-consolidatereports)
- [Data Migration Assistant によって作成された統合評価レポートを Power BI で分析する](https://docs.microsoft.com/sql/dma/dma-powerbiassesreport)

## <a name="sql-server-migration-assistant"></a>SQL Server Migration Assistant

Microsoft SQL Server Migration Assistant (SSMA) は、Microsoft Access、DB2、MySQL、Oracle、および SAP ASE から SQL Server へのデータベース移行を自動化するために設計されたツールです。 一般的な概念は、これらのツールを使用して収集、評価、その後レビューを行うというものですが、それぞれのソース システムでプロセスに違いがあるため、詳細な [SQL Server Migration Assistant のドキュメント](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant)を確認することをお勧めします。

詳細については、次を参照してください。

- [SQL Server Migration Assistant の概要](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant)

## <a name="database-experimentation-assistant"></a>Database Experimentation Assistant

Database Experimentation Assistant (DEA) は、SQL Server アップグレード用の新しい A/B テストソリューションです。 特定のワークロード用の SQL のターゲット バージョンを評価する際に役立ちます。 SQL Server の以前のバージョン (SQL Server 2005 以降) から SQL Server の新しいバージョンにアップグレードするお客様は、これらの分析メトリックを使用できます。

Database Experimentation Assistant には、以下のワークフロー アクティビティが含まれています。

- **キャプチャ:** SQL Server A/B テストの最初のステップでは、ソース サーバーでトレースをキャプチャします。 通常、ソース サーバーは実稼働サーバーです。
- **再生:** SQL Server A/B テストの 2 番目のステップでは、ターゲット サーバーでキャプチャしたトレース ファイルを再生します。 次に、分析のために、再生から大量のトレースを収集します。
- **分析:** 最後のステップでは、再生トレースを使用して分析レポートを生成します。 分析レポートは、提案された変更がパフォーマンスに与える影響についての分析情報を得るために役立ちます。

詳細については、次を参照してください。

- [Database Experimentation Assistant の概要](https://docs.microsoft.com/sql/dea/database-experimentation-assistant-overview)

## <a name="azure-cosmos-db-data-migration-tool"></a>Azure Cosmos DB データ移行ツール

Azure Cosmos DB データ移行ツールでは、さまざまなソースからのデータを Azure Cosmos DB コレクションおよびテーブルにインポートできます。 JSON ファイル、CSV ファイル、SQL、MongoDB、Azure Table Storage、Amazon DynamoDB、さらには Azure Cosmos DB SQL API コレクションからインポートすることができます。 データ移行ツールは、SQL API の 1 つの単一パーティション コレクションから複数パーティション コレクションに移行する場合にも使用できます。

詳細については、次を参照してください。

- [Azure Cosmos DB データ移行ツール](https://docs.microsoft.com/azure/cosmos-db/import-data)

<!-- markdownlint-disable MD025 -->

# <a name="third-party-migration-tools"></a>[サード パーティの移行ツール](#tab/third-party-tools)

移行プロセスを支援する、サード パーティの各種移行ツールおよび ISV サービスを利用できます。 それぞれ利点や長所が異なります。 これには以下のツールが含まれます。

## <a name="unifycloud"></a>UnifyCloud

UnifyCloud は、評価、移行、および最新化の自動化ツールを提供する ISV サービスです。

[詳細情報](https://www.unifycloud.com)

## <a name="cloudamize"></a>Cloudamize

Cloudamize は、移行戦略のすべてのフェーズに対応した ISV サービスです。

[詳細情報](https://www.cloudamize.com)

## <a name="zerto"></a>Zerto

Zerto は、Microsoft Hyper-V 環境と VMware vSphere 環境の両方に対応した仮想レプリケーションを提供します。

[詳細情報](https://www.zerto.com/modernize)

## <a name="carbonite"></a>Carbonite

Carbonite は、あらゆる物理環境、仮想環境、またはクラウド ベース環境の間でワークロードを移行するためのサーバーおよびデータ移行ソリューションを提供します。

[詳細情報](https://www.carbonite.com/data-protection/data-migration-software)

## <a name="movere"></a>Movere

Movere は、クラウド移行の計画と IT 環境の継続的な最適化、監視、分析を自信を持って行うために必要なデータや分析情報を提供する検出ソリューションです。

[詳細情報](https://www.movere.io)

## <a name="azure-cosmos-db-partners"></a>Azure Cosmos DB パートナー

NoSQL データベースの要件に合わせて Azure Cosmos DB の移行をサポートするために、多様で経験豊富なシステム インテグレーターのパートナーやツールから選択できます。

[詳細情報](https://docs.microsoft.com/azure/cosmos-db/partners-migration-cosmosdb#migration-tools)

[Azure Migration Center](https://azure.microsoft.com/migration/support) にアクセスして、お客様の移行シナリオに合わせてすぐに使えるパートナー テクノロジ ソリューションを提供する組織を探したり、その他のサード パーティの移行ツールやサポート サービスの情報を入手してください。

さまざまなデータベース移行オプションと、ネイティブおよびパートナーとのステップバイステップ ガイダンスについては、「[Azure データベース移行ガイド](https://datamigration.microsoft.com)」を参照してください。

# <a name="project-management-tools"></a>[プロジェクト管理ツール](#tab/project-management-tools)

追跡も管理もされていないプロジェクトでは、問題が発生しやすくなります。 確実に成功を収めるためには、お客様がプロジェクト管理ツールを使用することが重要であると Microsoft では考えています。 利用可能なさまざまなツールがあり、組織のプロジェクト マネージャーが気に入っているツールが既にあるかもしれません。

Azure DevOps は、クラウド移行時のプロジェクト管理に推奨されるツールです。 Azure DevOps の使用を高速化するために、クラウド導入フレームワークには、プロジェクト テンプレートを自動的にデプロイするためのツールが含まれています。 このテンプレートには、移行作業中に一般的に実行されるタスクが含まれています。 [クラウド導入プランと Azure DevOps](https://docs.microsoft.com/azure/architecture/cloud-adoption/plan/template) の手順に従って、テンプレートをデプロイします。 これで、移行する[ワークロード](https://docs.microsoft.com/azure/architecture/cloud-adoption/plan/workloads)と[資産](https://docs.microsoft.com/azure/architecture/cloud-adoption/plan/assets)を反映するようにテンプレートを変更できるようになります。

Microsoft では以下のプロジェクト管理ツールも用意しており、これらを併用してより広範な機能を提供できます。

- [Microsoft Planner](https://tasks.office.com):チームワークを組織化するためのシンプルで視覚的な方法。
- [Microsoft Project](https://products.office.com/project/project-and-portfolio-management-software):プロジェクトおよびポートフォリオ管理、リソース キャパシティ管理、財務管理、タイムシートおよびスケジュール管理。
- [Microsoft Teams](https://products.office.com/microsoft-teams):チームのコラボレーションとコミュニケーションのためのツール。 Teams を Planner やその他のツールと統合し、コラボレーションを強化することもできます。
- [Azure DevOps Services](https://azure.microsoft.com/services/devops):Azure DevOps を使用するために、クラウド導入フレームワークの計画テンプレートは必要ありません。 テンプレートを使用せずにサービスを使用して、コードとしてインフラストラクチャを管理することや、作業項目とボードを使用してプロジェクト管理を実行することができます。 発展に合わせて、組織で CI/CD 機能を利用できます。

利用可能なツールはこれらに限られません。 他にも多くのサード パーティ製ツールが、プロジェクト管理コミュニティで広く使用されています。

## <a name="set-up-for-devops"></a>DevOps 用の設定

クラウド テクノロジへの移行は、DevOps や CI/CD に合わせて組織を再編するまたとない機会です。 組織で管理するのがインフラストラクチャだけの場合でも、インフラストラクチャをコードとして管理し、DevOps 向けの業界のパターンやプラクティスを使用するようになれば、CI/CD パイプラインによって機敏性の向上に着手し、変化、成長、リリース、さらには復旧の各シナリオにより迅速に適応できるようになります。

Azure DevOps では、すべての必須機能と、Azure、オンプレミス環境、さらには他のクラウドとの統合を提供します。 詳細については、[Azure DevOps](https://azure.microsoft.com/services/devops) のページをご覧ください。 ガイド付きのトレーニングについては、[Azure DevOps を使用した CI と CD のクイック スタート](https://microsoft.github.io/PartsUnlimited/pandp/200.1x-PandP-CICDQuickstartwithVSTS.html)をご覧ください。

## <a name="suggested-skills"></a>推奨されるスキル

Microsoft Learn は学習に対する新しいアプローチです。 クラウド導入に伴う新たなスキル上の責任に対する準備は容易にできるものではありません。 Microsoft Learn では、目標を早く達成するのに役立つ、実践的な学習に対するより価値あるアプローチを提供します。 ポイントとレベルを獲得し、さらなる達成を目指しましょう。

ここでは、クラウド導入フレームワークにおける DevOps ガイダンスのセットアップを補完する Microsoft Learn のカスタマイズされたラーニング パスの例を示します。

[Azure DevOps でアプリケーションを構築する](https://docs.microsoft.com/learn/paths/build-applications-with-azure-devops):Azure Pipelines と GitHub を使って、他の人と共同でアプリケーションを構築します。 パイプラインで自動テストを実行して、コードの品質を検証します。 潜在的な脆弱性がないかどうかソース コードとサードパーティ コンポーネントをスキャンします。 アプリケーションを構築するために連携させる複数のパイプラインを定義します。 Microsoft がホストするエージェントと独自のビルド エージェントの両方を使用して、アプリケーションを構築します。

# <a name="cost-management"></a>[コスト管理](#tab/ManageCost)

リソースをクラウド環境に移行するときは、定期的なコスト分析を実行することが重要です。 移行プロセスによって追加の使用量要件がサービスに適用される可能性があるため、想定外の使用量料金をこれにより回避できます。 コストとワークロードのバランスを取るために、必要に応じてリソースのサイズを変更することもできます (詳しくは、[最適化と変換](./optimize-and-transform.md)** に関する記事をご覧ください)。
