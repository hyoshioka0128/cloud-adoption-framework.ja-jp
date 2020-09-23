---
title: SQL Server データベースの Azure への移行
description: Contoso がオンプレミスの SQL データベースを Azure に移行する方法について説明します。
author: deltadan
ms.author: abuck
ms.date: 07/01/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: migrate
ms.openlocfilehash: 071e6d8f33ea299dac78136f71f5bbce7badb95d
ms.sourcegitcommit: 4e12d2417f646c72abf9fa7959faebc3abee99d8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2020
ms.locfileid: "90775226"
---
<!-- cSpell:ignore BACPAC FILESTREAM -->

# <a name="migrate-sql-server-databases-to-azure"></a>SQL Server データベースの Azure への移行

この記事では、架空の会社である Contoso が、さまざまなオンプレミス SQL Server データベースの Azure への移行をどのように評価、計画し、実行したかについて説明します。

Azure への移行を検討する際、Contoso では技術的および財務的な評価を実行して、オンプレミスのワークロードがクラウドへの移行に適しているかどうかを確認する必要があります。 Contoso 社のチームは特に、移行対象のマシンおよびデータベースの互換性を評価する必要があります。 また、Contoso のリソースを Azure 内で実行するための容量とコストを見積もる必要があります。

## <a name="business-drivers"></a>ビジネス ドライバー

Contoso では、自社のネットワーク上に存在する多種多様なバージョンの SQL Server ワークロードすべてを維持するために、さまざまな問題が発生しています。 最近の投資家との会合の後、CFO と CTO は、これらすべてのワークロードを Azure に移行することを決定しました。 これにより、構造化された資本費用モデルから流動的な運用費用モデルに移行することができます。

IT リーダーシップ チームは、ビジネス パートナーと密接に連絡を取り合い、ビジネス面および技術面での要件を次のように理解しました。

- **セキュリティを強化する:** Contoso では、すべてのデータ リソースを、よりタイムリーかつ効率的な方法で監視および保護できる必要があります。 また、データベース アクセス パターンに対して、より集中管理されたレポート システムをセットアップしたいとも考えています。

- **コンピューティング リソースの最適化:** Contoso では、大規模なオンプレミスのサーバー インフラストラクチャをデプロイしてきました。 SQL Server インスタンスの中には、割り当てられた基になる CPU、メモリ、ディスクを消費してはいるものの、実際には効率的に使用していないものがいくつかあります。

- **効率化:** Contoso では、不要な手順を取り除き、開発者とユーザーのプロセスを効率化する必要があります。 ビジネス部門は IT に対して、時間やコストを無駄にせず、迅速に作業を行ってもらう必要があります。これは、例えば、顧客の要求に素早く対応するためです。 移行後のデータベース管理は、削減するか、最小限に抑える必要があります。

- **機敏性の向上:** Contoso IT は、ビジネス部門の要求に対して、対応力を向上させる必要があります。 また、グローバル経済で成功を収めるために、市場の変化に対して、より迅速な対応ができる必要があります。 ビジネスの妨げになったり、ビジネスの機会を壊すようなことがあってはなりません。

- **スケール:** ビジネスが順調に成長していく中で、Contoso IT は、同じペースで拡張できるシステムを提供する必要があります。 いくつかのレガシ ハードウェア環境は、アップグレードすることができず、サポートが終了しているか、終了間近になっています。

- **コスト:** ビジネスおよびアプリケーションの所有者は、オンプレミスでアプリケーションを実行するよりもクラウドのコストが高くなって行き詰まることを避けたいと考えています。

## <a name="migration-goals"></a>移行の目標

Contoso のクラウド チームは、さまざまな移行の目標を設定しました。 これらの目標を使用して、最良の移行方法を決定しました。

| 必要条件 | 詳細 |
| --- | --- |
| **パフォーマンス** | 移行後も、Azure 内のアプリケーションは、Contoso のオンプレミス環境で現在提供されているのと同じパフォーマンスを発揮できる必要があります。 クラウドに移行するのは、そのアプリケーションのパフォーマンスが重要性が低いからではありません。 |
| **互換性** | Contoso では、自社のアプリケーションおよびデータベースと Azure との互換性を理解する必要があります。 また、Azure ホスティングのオプションについても理解する必要があります。 |
| **[データ ソース]** | すべてのデータベースは例外なく Azure に移動します。 使用中の SQL 機能のデータベースおよびアプリケーション分析に基づいて、PaaS、IaaS、またはマネージド インスタンスに移動します。 すべてのデータベースを移動する必要があります。 |
| **Application** | アプリケーションは、可能な限りクラウドに移動する必要があります。 移動できない場合、移行されたデータベースには Azure ネットワーク経由でプライベート接続を使用してのみ接続することが許可されます。 |
| **コスト** | Contoso は移行オプションだけでなく、インフラストラクチャをクラウドに移動した後のインフラストラクチャに関連するコストも理解する必要があります。 |
| **管理** | さまざまな部門に対してリソース管理グループを作成し、移行されたすべての SQL データベースを管理するためにリソース グループを作成する必要があります。 すべてのリソースには、チャージバック要件のために部門の情報をタグ付けする必要があります。 |
| **制限事項** | 最初は、アプリケーションを実行するすべての支店に Azure への直接 ExpressRoute リンクがあるわけではないため、それらの支店からは仮想ネットワーク ゲートウェイ経由で接続する必要があります。 |

## <a name="solution-design"></a>ソリューション設計

Contoso では、[Azure Migrate](/azure/migrate/migrate-services-overview) を使用して、自社のデジタル資産の[移行評価](../..//plan/contoso-migration-assessment.md)を既に実行しました。

この評価では、複数のワークロードを複数の部門に分散するという結果になりました。 移行プロジェクト全体のサイズからすると、コミュニケーション、リソース、スケジュールの計画の詳細を管理するために、完全なプロジェクト管理オフィス (PMO) が必要になります。

![移行プロセス](./media/contoso-migration-sql-server-db-to-azure/migration-process.png)

### <a name="solution-review"></a>ソリューションのレビュー

Contoso は、長所と短所の一覧をまとめて、提案されたデザインを評価します。

| 考慮事項 | 詳細 |
| --- | --- |
| **長所** | Azure では、データベース ワークロードを一元的に確認できます <br><br> コストは Azure Cost Management および Billing によって監視されます。 <br><br> ビジネスのチャージバック課金は、Azure Billing API シリーズを使用して簡単に実行できます。 <br><br> サーバーとソフトウェアのメンテナンスは、IaaS ベースの環境のみに削減されます。 |
| **短所** | IaaS ベースの仮想マシンが必要になるため、これらのマシン上のソフトウェアを管理する必要性が残ります。 |

### <a name="budget-and-management"></a>予算と管理

移行を実行する前に、ソリューションの管理と課金の側面をサポートするために、必要な Azure 構造を整える必要があります。

管理要件については、組織の構造をサポートするために、いくつかの[管理グループ](/azure/governance/management-groups/overview)を作成しました。

課金要件については、各 Azure リソースに、該当する課金タグを[タグ付け](/azure/azure-resource-manager/management/tag-resources)します。

### <a name="migration-process"></a>移行プロセス

データの移行は、標準的な反復可能パターンに従います。 これには [Microsoft のベスト プラクティス](https://datamigration.microsoft.com/)に基づく次の手順が含まれます。

- 移行前:
  - **検出:** インベントリ データベース資産とアプリケーション スタック。
  - **評価:** ワークロードを評価し、推奨事項を修正します。
  - **変換:** ソース スキーマをターゲットで動作するように変換します。
- 移行:
  - **移行:** ソース スキーマ、ソース データ、オブジェクトをターゲットに移行します。
  - **データの同期:** データを同期します (ダウンタイムを最小限にするため)。
  - **一括:** ソースからターゲットに一括移行します。
- 移行後:
  - **アプリケーションの修復:** アプリケーションに対して、必要な変更を繰り返し実行します。
  - **テストの実行:** 機能およびパフォーマンス テストを繰り返し実行します。
  - **最適化:** テストに基づいて、パフォーマンスの問題に対処し、パフォーマンスの向上を確認するために再テストします。
  - **資産の廃止:** 前の VM とホスティング環境をバックアップし、廃止します。

#### <a name="step-1-discovery"></a>手順 1:探索

Contoso では、Azure Migrate を使用して、Contoso 環境全体の依存関係を明らかにしました。 Azure Migrate により、Windows および Linux システム上のアプリケーション コンポーネントが自動的に検出され、サービス間の通信がマップされました。 Contoso サーバー間の接続、プロセス、インバウンド接続とアウトバウンド接続の待ち時間、TCP 接続アーキテクチャ全体のポートも Azure Migrate によって明らかになりました。

また、Azure Migrate プロジェクトに Data Migration Assistant も追加しました。 このツールを選択することで、Azure への移行についてデータベースを評価できるようになります。

![Data Migration Assistant](./media/contoso-migration-sql-server-db-to-azure/add-dma.png)

#### <a name="step-2-application-assessment"></a>手順 2:アプリケーション評価

<!-- docutune:casing "mainly .NET-based" "non-.NET-based" -->

この評価の結果として、Contoso が主として利用しているのが .NET ベースのアプリケーションであることがわかりました。ただし、年月の経過とともにさまざまなプロジェクトで PHP や Node.js などの他のテクノロジが使用されるようになりました。 ベンダーが購入したシステムでも、非 .NET ベースのアプリケーションが導入されました。 次のものを特定しました。

- 約 800 個の Windows .NET アプリケーション
- 約 50 個の PHP アプリケーション
- 25 個の Node.js アプリケーション
- 10 個の Java アプリケーション

#### <a name="step-3-database-assessment"></a>手順 3:データベースの評価

各データベース ワークロードを検出した上で、Data Migration Assistant (DMA) ツールを実行して、使用されていた機能を特定しました。 DMA を使用すると、新しいバージョンの SQL Server または Azure SQL Database でデータベースの機能に影響を与える可能性がある互換性の問題を検出することにより、Azure へのデータベースの移行を評価できます。

Contoso では、次の手順に従ってデータベースを評価し、結果データを Azure Migrate にアップロードしました。

1. DMA をダウンロードします。
1. 評価プロジェクトを作成します。
1. DMA で、Azure Migrate プロジェクトにログオンし、評価の概要を同期します。

![Azure Migrate と DMA](./media/contoso-migration-sql-server-db-to-azure/azure-migrate-dma.png)

DMA からは、ターゲット環境のパフォーマンスと信頼性を向上させるための推奨事項が提供されます。これにより、スキーマ、データ、包含されていないオブジェクトをソース サーバーからターゲット サーバーに移動できます。

[Data Migration Assistant](/sql/dma/dma-assesssqlonprem?view=sql-server-2017) に関する詳細情報をご覧ください

Contoso では、DMA を使用して評価を実行し、Azure Migrate にデータを直接アップロードしました。

![DMA から Azure Migrate にアップロードする](./media/contoso-migration-sql-server-db-to-azure/upload-db-data.png)

データベース情報が Azure Migrate に読み込まれたので、Contoso では、移行する必要がある 1,000 個を超えるデータベース インスタンスを特定できました。 これらのインスタンスのうち約 40% は、Azure の SQL Database に移動できます。 残りの 60% は、Azure Virtual Machines 上で実行されている SQL Server か、または Azure SQL Managed Instance のいずれかに移動する必要があります。 この 60% のうち約 10% は仮想マシン ベースのアプローチが必要であり、残りのインスタンスは Azure SQL Managed Instance に移動できます。

データ ソースで DMA を実行できなかった場合は、次のガイドラインに従ってデータベースを移行しました。

> [!NOTE]
> 評価フェーズの一環として、Contoso ではさまざまなオープンソース データベースを検出しました。 これらについては、別途、[このガイド](./contoso-migration-oss-db-to-azure.md)に従って移行計画を立てました。

<!-- docutune:casing "custom .NET" -->

#### <a name="step-4-migration-planning"></a>手順 4:移行の計画

Contoso では、この情報を参考に、次のガイドラインを使用して、各データベースに使用する移行方法を決定します。

| 移行先 | データベースの使用状況 | 詳細 | オンライン移行 | オフライン移行 | 最大サイズ | 移行ガイド |
| --- | --- | --- | --- | ---| --- | --- |
| Azure SQL Database (PaaS) | SQL Server (データのみ) | これらのデータベースでは、単純に、基本的なテーブル、列、ストアド プロシージャ、関数を使用します | [Data Migration Assistant](/sql/dma/dma-overview)、[トランザクション レプリケーション](/azure/sql-database/sql-database-managed-instance-transactional-replication) | [BACPAC](/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database)、[bcp](/sql/tools/bcp-utility?view=sql-server-ver15) | 1 TiB | [リンク](/azure/dms/tutorial-sql-server-to-azure-sql) |
| Azure SQL Managed Instance | SQL Server (高度な機能) | これらのデータベースでは、トリガーおよびその他の[高度な概念](/azure/sql-database/sql-database-managed-instance-transact-sql-information#service-broker) (カスタム .NET 型、サービス ブローカーなど) が使用されます。 | [Data Migration Assistant](/sql/dma/dma-overview)、[トランザクション レプリケーション](/azure/sql-database/sql-database-managed-instance-transactional-replication) | [BACPAC](/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database)、[bcp](/sql/tools/bcp-utility?view=sql-server-ver15)、[ネイティブのバックアップと復元](/azure/sql-database/sql-database-managed-instance-get-started-restore) | 2 TiB - 8 TiB | [リンク](/azure/dms/tutorial-sql-server-managed-instance-online) |
| Azure Virtual Machines 上の SQL Server (IaaS) | SQL Server (サードパーティによる統合) | データベースのアクティビティをサポートするため、SQL Server には、[サポートされていない SQL Managed Instance 機能](/azure/sql-database/sql-database-managed-instance-transact-sql-information#service-broker) (インスタンス間のサービス ブローカー、暗号化プロバイダー、バッファー プール、100 未満の互換性レベル、データベース ミラーリング、FILESTREAM、PolyBase、ファイル共有や外部スクリプトや拡張ストアド プロシージャへのアクセスが必要な機能、その他)、またはサードパーティ製ソフトウェアがインストールされている必要があります。 | [トランザクション レプリケーション](/azure/sql-database/sql-database-managed-instance-transactional-replication) | [BACPAC](/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database)、[bcp](/sql/tools/bcp-utility?view=sql-server-ver15)、[スナップショット レプリケーション](/azure/sql-database/sql-database-managed-instance-transactional-replication)、[ネイティブのバックアップと復元](/azure/sql-database/sql-database-managed-instance-get-started-restore)、物理マシンから VM への変換 | 4 GiB - 64 TiB | [リンク](/azure/virtual-machines/windows/sql/virtual-machines-windows-migrate-sql) |

データベースの数が多いため、Contoso ではプロジェクト管理オフィス (PMO) を作成して、すべてのデータベース移行インスタンスを追跡しました。 各ビジネスおよびアプリケーション チームには、[説明責任と責任](../..//migrate/migration-considerations/assess/index.md)が割り当てられました。

Contoso では、[ワークロードの準備状況のレビュー](../..//migrate/migration-considerations/assess/evaluate.md)も実行しました。 このレビューでは、インフラストラクチャ、データベース、ネットワークのコンポーネントについて調査しました。

#### <a name="step-5-test-migrations"></a>手順 5:移行のテスト

移行準備の最初の部分には、設定前の環境への各データベースのテスト移行が含まれていました。 時間を節約するために、移行のすべての操作をスクリプト化し、それぞれの時間を記録しました。 移行を高速化するために、同時に実行できる移行操作を特定しました。

予期しないエラーが発生した場合のために、データベースの各ワークロードについてロールバック手順を特定しました。

IaaS ベースのワークロードについては、必要なすべてのサードパーティ製ソフトウェアを事前に設定しました。

テスト移行の後、Contoso ではさまざまな Azure [コスト見積もりツール](../..//migrate/migration-considerations/assess/estimate.md)を使用して、移行の将来の運用コストをより正確に把握することができました。

#### <a name="step-6-migration"></a>手順 6:移行

運用環境への移行では、Contoso は、すべてのデータベースの移行の時間枠と、週末の時間枠 (金曜日の午前 0 時から日曜日の午前 0 時まで) 内で十分に実行できる作業内容を特定し、ビジネスのダウンタイムを最小限に抑えました。

ドキュメント化したテスト手順に基づいて、可能な限りスクリプトを使用して各移行を実行し、手動のタスクを減らしてエラーを最小限に抑えます。

時間枠の間に移行が失敗した場合は、作業をロールバックし、次の移行時間枠に再スケジュールします。

### <a name="clean-up-after-migration"></a>移行後にクリーンアップする

Contoso では、すべてのデータベース ワークロードのアーカイブ時間枠を特定しました。 時間枠の期限が切れると、リソースはオンプレミスのインフラストラクチャから削除されます。

これには次のものが含まれます

- オンプレミスのサーバーから運用データを削除する。
- 最後のワークロード時間枠の期限が切れた時点で、ホスティング サーバーを廃止する。

### <a name="review-the-deployment"></a>デプロイを再調査する

リソースを Azure に移行したら、新しいインフラストラクチャを完全に操作可能にして、セキュリティ保護する必要があります。

#### <a name="security"></a>セキュリティ

- Contoso では、新しい Azure データベース ワークロードを確実にセキュリティで保護する必要があります。 [詳細については、こちらを参照してください](/azure/sql-database/sql-database-security-overview)。
- 特に、ファイアウォールと仮想ネットワークの構成を確認する必要があります。
- [Private Link](/azure/azure-sql/database/private-endpoint-overview) をセットアップして、すべてのデータベース トラフィックが Azure とオンプレミスのネットワーク内に保持されるようにします。
- Azure SQL Database のための [Azure Advanced Threat Protection](/azure/azure-sql/database/threat-detection-overview) を有効にします。

#### <a name="backups"></a>バックアップ

- geo リストアを使用して、Azure データベースが確実にバックアップされるようにします。 これにより、リージョンで障害が発生した場合に、ペアのリージョンでバックアップを使用できるようになります。
- **重要:** Azure リソースが削除されないようにするため、[リソース ロック](/azure/azure-resource-manager/management/lock-resources)を保持していることを確認します。 削除したサーバーを復元することはできません。

#### <a name="licensing-and-cost-optimization"></a>ライセンスとコストの最適化

- 多くの Azure データベース ワークロードはスケールアップまたはスケールダウンできるので、ニーズを満たすだけでなく、コストを最小限に抑えるために、サーバーとデータベースのパフォーマンスを監視することが重要です。
- CPU とストレージの両方にコストが関連付けられています。 選択できる価格レベルはいくつかあります。 データ ワークロードに適した価格プランが選択されていることを確認してください。
- [エラスティック プール](/azure/sql-database/sql-database-service-tiers-dtu)は、互換性のあるリソース使用状況パターンを持つデータベースに対して実装されます。
- 各読み取りレプリカは、選択されたコンピューティングとストレージに基づいて課金されます
- 予約容量を使用してコストを節約します。

## <a name="conclusion"></a>まとめ

この記事では、Contoso が、Microsoft SQL Server ワークロードの Azure への移行を評価し、計画し、実行しました。
