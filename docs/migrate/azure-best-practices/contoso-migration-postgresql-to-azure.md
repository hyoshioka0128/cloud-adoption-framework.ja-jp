---
title: PostgreSQL データベースを Azure に移行する
description: Contoso が、オンプレミスの PostgreSQL データベースを Azure にどのように移行したかについて説明します。
author: deltadan
ms.author: abuck
ms.date: 07/01/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: migrate
ms.openlocfilehash: 9f9dfee1aca21acbbf0f840b79d61501ad90c73a
ms.sourcegitcommit: 8b82889dca0091f3cc64116f998a3a878943c6a1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2020
ms.locfileid: "89603870"
---
<!-- cSpell:ignore BYOK postgres psql dvdrental vpngateways -->

# <a name="migrate-postgresql-databases-to-azure"></a>PostgreSQL データベースを Azure に移行する

この記事では、Contoso という架空の会社が、オンプレミスの PostgreSQL オープンソース データベース プラットフォームの Azure への移行をどのように計画し、実行したかについて説明します。

## <a name="business-drivers"></a>ビジネス ドライバー

IT リーダーシップ チームは、ビジネス パートナーと密接に連絡を取り合い、彼らがこの移行で何を達成しようとしているのかを理解しました。 目的は次のとおりです。

- **ビッグ データの自動化。** Contoso では、ビッグ データおよび AI イニシアチブのいくつかに PostgreSQL を使用しています。 同社は、これらの分析ワークロードの多くを自動化するために、スケーラブルで反復可能なパイプラインを構築したいと考えています。
- **効率化。** Contoso では、不要な手順を取り除き、開発者とユーザーのプロセスを効率化する必要があります。 ビジネス部門は、顧客の要求により迅速に対応するために、IT 部門に対して、時間やコストを無駄にせず、迅速に作業を行うことを求めています。
- **機敏性の向上。** Contoso IT は、ビジネス部門の要求に対して、対応力を向上させる必要があります。 また、グローバル経済での成功を実現し、ビジネスの障害にならないように、市場の変化に遅れることなく対応する必要があります。
- **スケール。** ビジネスが順調に成長している中で、Contoso IT 部門は、同じペースで拡張できるシステムを提供する必要があります。
- **セキュリティを強化する。** Contoso は、規制上の問題により、監査、ログ、コンプライアンスの要件に基づいてオンプレミス戦略の調整が必要になることを認識しています。

## <a name="migration-goals"></a>移行の目標

Contoso クラウド チームは、この移行の目標を明確にしました。それらを使用して最適な移行方法を決定します。

| 必要条件 | 詳細 |
| --- | --- |
| **アップグレード** | Contoso では、利用可能な場合は最新の修正プログラムを確実にインストールしたいと考えていますが、これらの更新を管理することは望んでいません。 |
| **統合** | Contoso では、データベース内のデータを、機械学習用のデータおよび AI パイプラインと統合したいと考えています。 |
| **バックアップと復元** | Contoso では、データの更新に失敗した場合や、なんらかの理由でデータが破損した場合に、ポイントインタイム リストアを実行する機能を求めています。 |
| **Azure** | Contoso では、システムを監視し、パフォーマンスとセキュリティに基づいてアラートを発生させたいと考えています。 |
| **パフォーマンス** | 場合によっては、異なる地理的リージョンに並列データ処理パイプラインを設定し、それらのリージョンからデータを読み取る必要があります。 |

## <a name="solution-design"></a>ソリューション設計

Contoso は、目標と要件を明確にした後、デプロイ ソリューションを設計してレビューし、移行プロセスを確認します。 移行に使用するツールとサービスも確認します。

### <a name="current-environment"></a>現在の環境

PostgreSQL 9.6.7 は、Contoso データセンターの物理的な Linux マシン (`sql-pg-01.contoso.com`) で実行されています。 Contoso には、オンプレミスのデータセンター ネットワークへのサイト間仮想ネットワーク ゲートウェイを含む Azure サブスクリプションが既にあります。

### <a name="proposed-solution"></a>提案されるソリューション

- Azure Database Migration Service を使用して、データベースを Azure Database for PostgreSQL インスタンスに移行します。
- 新しい Azure Database for PostgreSQL インスタンスを使用するように、すべてのアプリケーションとプロセスを変更します。
- Azure Database for PostgreSQL インスタンスに接続する Azure Data Factory を使用して、新しいデータ処理パイプラインを構築します。

### <a name="database-considerations"></a>データベースの考慮事項

ソリューション設計プロセスの一環として、Contoso では PostgreSQL データをホストするための Azure の機能を確認しました。 Azure の使用を決定する際に、次の考慮事項が役立ちました。

- Azure SQL Database と同様に、Azure Database for PostgreSQL ではファイアウォール規則がサポートされます。
- Azure Database for PostgreSQL を仮想ネットワークで使用して、インスタンスにパブリックにアクセスできないようにすることができます。
- Azure Database for PostgreSQL には、Contoso が満たす必要のある必須のコンプライアンス認定資格があります。
- DevOps と Azure Data Factory との統合によって、自動化されたデータ処理パイプラインを構築できるようになります。
- 読み取りレプリカを使用して、処理のパフォーマンスを向上させることができます。
- データ暗号化のための Bring Your Own Key (BYOK) のサポート。
- Azure Private Link を使用して、サービスを内部ネットワーク トラフィックだけに公開できます (パブリック アクセスなし)。
- 選択されたゲートウェイ (Azure ExpressRoute またはサイト間 VPN) に基づいて、アプリケーションからデータベースへの[帯域幅と待機時間](/azure/vpn-gateway/vpn-gateway-about-vpngateways)が十分に確保されます。

### <a name="solution-review"></a>ソリューションのレビュー

Contoso は、長所と短所の一覧をまとめて、提案された設計を評価します。

| 考慮事項 | 詳細 |
|--- | --- |
| **長所** | 現在の必須および使用中の機能はすべて、Azure Database for PostgreSQL でご利用いただけます。 <br><br> |
| **短所** | Contoso は、PostgreSQL のメジャー バージョンからの移行を引き続き手動で実行する必要があります。 |

## <a name="proposed-architecture"></a>提案されたアーキテクチャ

![シナリオ アーキテクチャの図。](./media/contoso-migration-postgresql-to-azure/architecture.png)

"_図 1:シナリオのアーキテクチャ_

### <a name="migration-process"></a>移行プロセス

#### <a name="preparation"></a>準備

Contoso は、PostgreSQL データベースを移行する前に、自社のインスタンスが、移行を成功させるための Azure の前提条件をすべて満たしていることを確認します。

#### <a name="supported-versions"></a>サポートされているバージョン

同じバージョンまたはそれ以降のバージョンへの移行のみがサポートされます。 PostgreSQL 9.5 から Azure Database for PostgreSQL 9.6 または 10 への移行はサポートされますが、PostgreSQL 11 から PostgreSQL 9.6 への移行はサポートされません。

Microsoft では、Azure Database for PostgreSQL - 単一サーバーでの PostgreSQL エンジンの _n-2_ バージョンのサポートを予定しています。 バージョンは、Azure の現在のメジャー バージョン (_n_) とその前の 2 つのメジャー バージョン ( _-2_) です。

サポートされているバージョンの最新情報については、「[サポートされる PostgreSQL のメジャー バージョン](/azure/postgresql/concepts-supported-versions)」をご覧ください。

> [!NOTE]
> メジャー バージョンの自動アップグレードはサポートされていません。 たとえば、PostgreSQL 9.5 から PostgreSQL 9.6 への自動アップグレードはありません。 次のメジャー バージョンにアップグレードするには、データベースをダンプし、ターゲット エンジンのバージョンで作成されたサーバーに復元します。

#### <a name="network"></a>ネットワーク

Contoso は、オンプレミス環境から、Azure Database for PostgreSQL データベースが配置されている仮想ネットワークへの仮想ネットワーク ゲートウェイ接続を設定する必要があります。 この接続により、オンプレミスのアプリケーションからデータベースにアクセスできるようになりますが、クラウドに移行することはできません。

#### <a name="assessment"></a>評価

Contoso では、レプリケーションの問題について、現在のデータベースを評価する必要があります。 次のような問題があります。

- 移行するソース データベースのバージョンは、ターゲット データベースのバージョンと互換性があります。
- レプリケートするすべてのテーブルに主キーが存在する必要があります。
- データベース名にセミコロン (`;`) を含めることはできません。
- 同じ名前であっても大文字と小文字が異なる複数のテーブルを移行すると、予期しない動作が発生する可能性があります。

  ![移行プロセスの図。](./media/contoso-migration-postgresql-to-azure/migration-process.png)
  _図 2: 移行プロセス_

#### <a name="migration"></a>移行

Contoso は、いくつかの方法で移行を実行できます。

- [ダンプと復元](/azure/postgresql/howto-migrate-using-dump-and-restore)
- [Azure Database Migration Service](/azure/dms/tutorial-postgresql-azure-postgresql-online)
- [Import/Export](/azure/postgresql/howto-migrate-using-export-and-import)

Contoso は、メジャーからメジャーへのアップグレードを実行する必要があるときに移行プロジェクトを常に再利用できるように、Azure Database Migration Service を選択しました。 1 つの Database Migration Service アクティビティは最大 4 つのデータベースにしか対応しないため、Contoso は次の手順を使用して複数のジョブを設定します。

準備するには、データベースにアクセスするための仮想ネットワークを設定します。 さまざまな方法で、[VPN ゲートウェイ](/azure/vpn-gateway/vpn-gateway-about-vpngateways)を使用して仮想ネットワーク接続を作成します。

### <a name="create-an-azure-database-migration-service-instance"></a>Azure Database Migration Service インスタンスを作成する

1. [Azure portal](https://portal.azure.com) で、 **[リソースの追加]** を選択します。
1. **Azure Database Migration Service** を検索して選択します。
1. **[+ 追加]** を選択します。
1. サービス用のサブスクリプションとリソース グループを選択します。
1. インスタンスの名前を入力します。
1. Contoso データセンターまたは VPN ゲートウェイに最も近い場所を選択します。
1. サービス モードについては、 **[Azure]** を選択します。
1. 価格レベルを選択します。
1. **[Review + create]\(レビュー + 作成\)** を選択します。

    ![[移行サービスの作成] 画面のスクリーンショット。](./media/contoso-migration-postgresql-to-azure/azure_migration_service_create.png)
    "_図 3:確認と作成_

1. **［作成］** を選択します

### <a name="create-an-azure-database-for-postgresql-instance"></a>Azure Database for PostgreSQL インスタンスを作成する

1. オンプレミス サーバーで、`postgresql.conf` ファイルを構成します。

1. サーバーとデータベースへのアクセスに Azure Database Migration Service によって使用される適切な IP アドレスをリッスンするように、サーバーを設定します。
    - `listen_addresses` 変数を設定します。
1. SSL を有効にします。
    1. `ssl=on` 変数を設定します。
    1. TLS 1.2 をサポートするサーバーに対して、公的に署名された SSL 証明書を使用していることを確認します。 それ以外の場合、Database Migration Service ツールでエラーが発生します。
1. `pg_hba.conf` ファイルを更新します。
    - Database Migration Service インスタンスに固有のエントリを追加します。
1. 各サーバーの `postgresql.conf` ファイルの値を変更して、ソース サーバーで論理レプリケーションを有効にする必要があります。
    1. `wal_level` = `logical`
    1. `max_replication_slots` = [少なくとも、移行するデータベースの最大数]
        - たとえば、4 つのデータベースを移行する場合は、値を 4 に設定します。
    1. `max_wal_senders` = [同時に実行されているデータベースの数]
        - 推奨値は 10 です。

1. 移行 `User` には、ソース データベースに対する `REPLICATION` ロールが必要です。

1. `PostgreSQLpg_hba.conf` ファイルに、Database Migration Service インスタンスの IP アドレスを追加します。

1. データベース スキーマをエクスポートするには、次のコマンドを実行します。

    ```cmd
    pg_dump -U postgres -s dvdrental > dvdrental_schema.sql
    ```

1. ファイルをコピーし、そのコピーに `dvdrental_schema_foreign.sql` という名前を付けて、すべての非外部キーおよびトリガー関連項目を削除します。
1. `dvdrental_schema.sql` ファイルから、すべての外部キーおよびトリガー関連項目を削除します。

1. データベース スキーマをインポートします (手順 1)。

      ```cmd
        psql -h {host}.postgres.database.azure.com -d dvdrental -U username -f dvdrental_schema.sql
      ```

### <a name="migration"></a>移行

1. Azure portal で、Database Migration Service リソースに移動します。
1. サービスが開始されていない場合は、 **[サービスの開始]** を選択します。
1. **[新しい移行プロジェクト]** を選択します。

    ![[新しい移行プロジェクト] オプションが強調表示されているスクリーンショット。](./media/contoso-migration-postgresql-to-azure/azure_migration_service_new_project.png)

    "_図 4:新しい移行の開始。_

1. **[新しいアクティビティ]**  >  **[オンライン データの移行]** の順に選択します。
1. 名前を入力します。
1. ソースとして、 **[PostgreSQL]** を選択します。
1. ターゲットについては、 **[Azure Database for PostgreSQL]** を選択し、 **[保存]** を選択します。

    ![[新しい移行プロジェクト] ペインを示すスクリーンショット。](./media/contoso-migration-postgresql-to-azure/azure_migration_service_new_project02.png)

    _図 5:新しい移行プロジェクトが強調表示されています。_

1. ソース情報を入力し、 **[保存]** を選択します。

    ![ソース情報の入力を示すスクリーンショット。](./media/contoso-migration-postgresql-to-azure/azure_migration_service_source.png)
    _図 6:ソース情報の入力。_

1. ターゲット情報を入力し、 **[保存]** を選択します。

    ![ターゲット情報の選択を示すスクリーンショット。](./media/contoso-migration-postgresql-to-azure/azure_migration_service_target.png)
    _図 7:ターゲット情報の選択。_

1. 移行するデータベースを選択します。 各データベースのスキーマは、以前に移行されているはずです。 次に、 **[保存]** を選択します。

    ![データベースの選択を示すスクリーンショット。](./media/contoso-migration-postgresql-to-azure/azure_migration_service_db.png)
    _図 8:データベースの選択。_

1. 詳細設定を構成し、 **[保存]** を選択します。

    ![詳細設定の構成を示すスクリーンショット。](./media/contoso-migration-postgresql-to-azure/azure_migration_service_advanced.png)
    _図 9:詳細設定の構成。_

1. アクティビティに名前を付け、 **[実行]** を選択します。

    ![アクティビティの名前付けと実行を示すスクリーンショット。](./media/contoso-migration-postgresql-to-azure/azure_migration_service_summary.png)
    _図 10:アクティビティの名前付けと実行。_

1. 移行を監視する。 なんらかのエラーが発生した場合は再試行します。 たとえば、外部キー参照が見つからない場合などです。
1. `Full load completed` がテーブル数と一致したら、 **[一括で開始]** を選択します。

    ![一括を開始するための移行の監視を示すスクリーンショット。](./media/contoso-migration-postgresql-to-azure/azure_migration_service_complete.png)
    _図 11:カットオーバーを開始するための移行の監視。_

1. ソース サーバーからのすべてのトランザクションを停止します。
1. **[確認]** チェック ボックスをオンにし、 **[適用]** を選択します。

    ![一括の実行を示すスクリーンショット。](./media/contoso-migration-postgresql-to-azure/azure_migration_service_cutover.png)
    _図 12:カットオーバーの実行。_

1. カットオーバーが完了するまで待ちます。

    ![一括の完了を示すスクリーンショット。](./media/contoso-migration-postgresql-to-azure/azure_migration_service_finished.png)
    _図 13:カットオーバーの完了。_

      > [!NOTE]
      > 上記の Database Migration Service の手順は、[Azure CLI](/azure/dms/tutorial-postgresql-azure-postgresql-online) を使用して実行することもできます。

1. データベース スキーマをインポートします (手順 2)。

      ```cmd
        psql -h {host}.postgres.database.azure.com -d dvdrental -U username -f dvdrental_schema_foreign.sql
      ```

1. オンプレミスのデータベースを使用するすべてのアプリケーションまたはプロセスを、新しい Azure Database for PostgreSQL データベース インスタンスを指すように再構成します。

1. 移行後について、Contoso は、移行が完了したら必要に応じて、リージョンをまたがる読み取りレプリカを設定することもできます。

## <a name="clean-up-after-migration"></a>移行後にクリーンアップする

移行後に、Contoso では保有目的でオンプレミス データベースをバックアップし、クリーンアップ プロセスの一環として古い PostgreSQL サーバーを廃止する必要があります。

## <a name="review-the-deployment"></a>デプロイを再調査する

リソースを Azure に移行したら、新しいインフラストラクチャを完全に操作可能にして、セキュリティで保護する必要があります。

### <a name="security"></a>セキュリティ

Contoso は次のことを行う必要があります。

- 新しい Azure Database for PostgreSQL インスタンスとデータベースを確実にセキュリティで保護します。 詳細については、「[Azure Database for PostgreSQL のセキュリティ - 単一サーバー](/azure/postgresql/concepts-security)」をご覧ください。
- [ファイアウォール規則](/azure/postgresql/concepts-firewall-rules)と仮想ネットワーク構成を確認して、接続を必要とするアプリケーションのみに接続が制限されていることを確認します。
- データの暗号化のために [BYOK](/azure/postgresql/concepts-data-encryption-postgresql) を実装します。
- データベースへの [SSL 接続を要求する](/azure/postgresql/concepts-ssl-connection-security)ように、すべてのアプリケーションを更新します。
- [Private Link](/azure/postgresql/concepts-data-access-and-security-private-link) を設定して、すべてのデータベース トラフィックが Azure とオンプレミス ネットワーク内に保持されるようにします。
- [Azure Advanced Threat Protection (ATP)](/azure/postgresql/concepts-data-access-and-security-threat-protection) を有効にします。
- 対象のセキュリティおよびログ エントリを監視し、アラートを生成するように Log Analytics を構成します。

### <a name="backups"></a>バックアップ

geo リストアを使用して、Azure Database for PostgreSQL データベースが確実にバックアップされるようにします。 これにより、リージョン規模の障害が発生した場合は、ペアのリージョンにあるバックアップを使用できます。

> [!IMPORTANT]
> Azure Database for PostgreSQL リソースが削除されないように、リソース ロックが設定されていることを確認します。 削除されたサーバーを復元することはできません。

### <a name="licensing-and-cost-optimization"></a>ライセンスとコストの最適化

- Azure Database for PostgreSQL は、スケールアップまたはスケールダウンすることができます。 コストを最小限に抑えながら、ニーズを確実に満たすために、サーバーとデータベースのパフォーマンスを監視することが重要です。
- CPU とストレージの両方がコストに関連します。 選択できる価格レベルはいくつかあります。 データ ワークロードに適した価格プランが選択されていることを確認してください。
- 各読み取りレプリカは、選択されたコンピューティングとストレージに基づいて課金されます。

## <a name="conclusion"></a>まとめ

この記事では、Contoso が PostgreSQL データベースを Azure Database for PostgreSQL インスタンスに移行しました。
