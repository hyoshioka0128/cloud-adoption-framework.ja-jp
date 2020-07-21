---
title: PostgreSQL データベースを Microsoft Azure に移行する
description: Contoso によって、オンプレミスの PostgreSQL データベースが Azure にどのように移行されたかについて学習します。
author: deltadan
ms.author: abuck
ms.date: 07/01/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: migrate
services: azure-migrate
ms.openlocfilehash: 8de202b5f6f8f0420541792898bf609360e79d28
ms.sourcegitcommit: bcc73d194c6d00c16ae2e3c7fb2453ac7dbf2526
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2020
ms.locfileid: "86199172"
---
<!-- cSpell:ignore BYOK postgres psql dvdrental -->

# <a name="migrate-postgresql-databases-to-microsoft-azure"></a>PostgreSQL データベースを Microsoft Azure に移行する

この記事では、架空の会社である Contoso によって、オンプレミスの PostgreSQL オープン ソース データベース プラットフォームの Azure への移行がどのように計画および実行されたかについて説明します。

## <a name="business-drivers"></a>ビジネス ドライバー

IT リーダーシップ チームは、ビジネス パートナーと密接に連絡を取り合い、彼らがこの移行で何を達成しようとしているのかを理解しました。

- **ビッグ データ:** Contoso では、ビッグ データと AI イニシアチブのいくつかに PostgreSQL を使用しており、これらの分析ワークロードの多くを自動化するために、スケーラブルで反復可能なパイプラインを構築できるようにしたいと考えています。
- **効率を高める:** Contoso では、不要な手順を取り除き、開発者とユーザーのプロセスを効率化する必要があります。 ビジネス部門では、IT 部門に対して、時間やコストを無駄にせず、迅速に作業を行い、顧客の要求によりすばやく対応することを求めています。
- **機敏性を高める:** Contoso IT は、ビジネス部門の要求に対して、対応力を向上させる必要があります。 また、グローバル経済での成功を実現し、ビジネスの障害にならないように、市場の変化に遅れることなく迅速に対応できる必要があります。
- **スケール:** ビジネスが順調に成長していく中で、Contoso IT は、同じペースで拡張できるシステムを提供する必要があります。
- **セキュリティの強化:** Contoso では、規制上の問題により、監査、ログ、コンプライアンスの要件に基づいてオンプレミスの戦略が調整されることを認識しています。

## <a name="migration-goals"></a>移行の目標

Contoso のクラウド チームは、この移行の目標を明確にし、最適な移行方法を決定するためにそれらを使用します。

| 必要条件 | 詳細 |
| --- | --- |
| **アップグレード** | Contoso では、利用可能な場合は最新の修正プログラムを確実にインストールしたいと考えていますが、これらの更新を管理したくはありません。 |
| **統合** | Contoso では、データベース内のデータを、Machine Learning 用のデータおよび AI パイプラインと統合したいと考えています。 |
| **バックアップと復元** | Contoso では、データを更新できなかった場合や、何らかの理由でデータが破損した場合に、ポイントインタイム リストアを行う機能を探しています。 |
| **Azure** | システムを監視し、パフォーマンスとセキュリティに基づいてアラートを発生させられるようにしたいと考えています。 |
| **パフォーマンス** | 場合によっては、異なる地理的リージョンに並列データ処理パイプラインを設定し、それらのリージョンからデータを読み取る必要があります。 |

## <a name="solution-design"></a>ソリューション設計

Contoso では目標と要件を決定した後、デプロイ ソリューションを設計および確認し、移行に使用するツールとサービスを含め、移行プロセスを特定します。

### <a name="current-environment"></a>現在の環境

PostgreSQL 9.6.7 は、Contoso データセンターの物理的な Linux マシン (`sql-pg-01.contoso.com`) で実行されています。 Contoso には、オンプレミスのデータセンター ネットワークへのサイト間仮想ネットワーク ゲートウェイを含む Azure サブスクリプションが既にあります。

### <a name="proposed-solution"></a>提案されるソリューション

- Azure Database Migration Service を使用して、データベースを Azure Database for PostgreSQL インスタンスに移行します。
- 新しい Azure Database for PostgreSQL インスタンスを使用するように、すべてのアプリケーションとプロセスを変更します。
- Azure Database for PostgreSQL インスタンスに接続する Azure Data Factory を使用して、新しいデータ処理パイプラインを構築します。

### <a name="database-considerations"></a>データベースの考慮事項

ソリューションの設計プロセスの一環として、Contoso では PostgreSQL データをホストするための Azure の機能を確認しました。 Azure の使用を決定するのに、次の考慮事項が役立ちました。

- Azure SQL Database と同様に、Azure Database for PostgreSQL ではファイアウォール規則がサポートされます。
- Azure Database for PostgreSQL を仮想ネットワークで使用して、インスタンスにパブリックにアクセスできないようにすることができます。
- Azure Database for PostgreSQL には、Contoso が満たす必要のある必須のコンプライアンス認定資格があります。
- DevOps と Azure Data Factory との統合によって、自動化されたデータ処理パイプラインを構築できるようになります。
- 読み取りレプリカを使用して、処理のパフォーマンスを向上させることができます。
- データ暗号化のための Bring Your Own Key (BYOK) のサポート。
- Private Link を使用して (パブリック アクセスなし)、サービスを内部ネットワーク トラフィックのみに公開する機能。
- 選択されたゲートウェイ (ExpressRoute またはサイト間 VPN) に応じて、アプリケーションからデータベースへの[帯域幅と待機時間](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)が十分になります。

### <a name="solution-review"></a>ソリューションのレビュー

Contoso は、長所と短所の一覧をまとめて、提案されたデザインを評価します。

| 考慮事項 | 詳細 |
|--- | --- |
| **長所** | 現在の必須および使用中の機能はすべて、Azure Database for PostgreSQL でご利用いただけます。 <br><br> |
| **短所** | Contoso では引き続き、PostgreSQL のメジャー バージョンから手動で移行する必要があります。 |

## <a name="proposed-architecture"></a>提案されたアーキテクチャ

![シナリオのアーキテクチャ](./media/contoso-migration-postgresql-to-azure/architecture.png)
_図 1: シナリオのアーキテクチャ。_

### <a name="migration-process"></a>移行プロセス

#### <a name="preparation"></a>準備

PostgreSQL データベースを移行する前に、移行を成功させるために、これらのインスタンスが確実にすべての Azure の前提条件を満たすようにしてください。

#### <a name="supported-versions"></a>サポートされているバージョン

同じバージョンまたはそれ以降のバージョンへの移行のみがサポートされます。 PostgreSQL 9.5 から Azure Database for PostgreSQL 9.6 または 10 への移行はサポートされますが、PostgreSQL 11 から PostgreSQL 9.6 への移行はサポートされません。

Microsoft では、Azure Database for PostgreSQL - 単一サーバーでの PostgreSQL エンジンの _n-2_ バージョンのサポートを予定しています。 バージョンは、Azure の現在のメジャー バージョン (_n_) とその前の 2 つのメジャー バージョン ( _-2_) です。

サポートされているバージョンの最新情報については、[こちらを参照](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions)してください。

> [!NOTE]
> メジャー バージョンの自動アップグレードはサポートされていません。 たとえば、PostgreSQL 9.5 から PostgreSQL 9.6 への自動アップグレードはありません。 次のメジャー バージョンにアップグレードするには、データベースをダンプし、ターゲット エンジンのバージョンで作成されたサーバーに復元します。

#### <a name="network"></a>ネットワーク

Contoso では、オンプレミス環境から、Azure Database for PostgreSQL データベースが配置されている仮想ネットワークへの仮想ネットワーク ゲートウェイ接続を設定する必要があります。 これにより、オンプレミスのアプリケーションからデータベースにアクセスできるようになりますが、クラウドに移行することはできません。

#### <a name="assessment"></a>評価

Contoso では、レプリケーションの問題について、現在のデータベースを評価する必要があります。 次のような問題があります。

- 移行するソース データベースのバージョンは、ターゲット データベースのバージョンと互換性があります。
- レプリケートするすべてのテーブルに主キーが確実に存在するようにします。
- データベース名にセミコロン (`;`) を含めることはできません。
- 同じ名前でも大文字と小文字が異なる複数のテーブルを移行すると、予期しない動作が発生する可能性があります。

  ![移行プロセス](./media/contoso-migration-postgresql-to-azure/migration-process.png)
  _図 2: 移行プロセス。_

#### <a name="migration"></a>移行

Contoso では、次のようないくつかの方法で移行を実行できます。

- [ダンプと復元](https://docs.microsoft.com/azure/postgresql/howto-migrate-using-dump-and-restore)

- [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online)

- [Import/Export](https://docs.microsoft.com/azure/postgresql/howto-migrate-using-export-and-import)

Contoso では、メジャーからメジャーへのアップグレードを実行する必要がある場合は必ず、移行プロジェクトを再利用できるように、Azure Database Migration Service を選択しました。 1 つの Database Migration Service アクティビティでは最大 4 つのデータベースにしか対応しないため、Contoso ではこれらの手順を使用して複数のジョブを設定します。

準備するには、データベースにアクセスするための仮想ネットワーク (VNet) を設定します。 さまざまな方法で、[VPN ゲートウェイ](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)を使用して VNet 接続を作成することができます。

<!-- docsTest:ignore "Azure Database Migration Services" -->

- 次のように、Azure Database Migration Service インスタンスを作成します。
  - [Azure portal](https://portal.azure.com) で、 **[リソースの追加]** を選択します。
  - **[Azure Database Migration Service]** を検索して選択します。
  - **[+ 追加]** を選択します。
  - サービス用のサブスクリプションとリソース グループを選択します。
  - インスタンスの名前を入力します。
  - ご利用のデータセンターまたは VPN ゲートウェイに最も近い場所を選択します。
  - サービス モードについては、 **[Azure]** を選択します。
  - 価格レベルを選択します。
  - **[Review + create]\(レビュー + 作成\)** を選択します。

    ![移行プロセス](./media/contoso-migration-postgresql-to-azure/azure_migration_service_create.png)
    _図 3: 確認と作成。_

  - **［作成］** を選択します

- Azure Database for PostgreSQL インスタンスを作成します。

- オンプレミス サーバーで、`postgresql.conf` ファイルを構成します。

  - サーバーとデータベースへのアクセスに Azure Database Migration Service によって使用される適切な IP アドレスをリッスンするように、サーバーを設定します。
    - `listen_addresses` 変数を設定します。
  - SSL を有効にします。
    - `ssl=on` 変数を設定します。
    - TLS 1.2 をサポートするサーバーに対して、公的に署名された SSL 証明書を使用していることを確認します。 それ以外の場合、Database Migration Service ツールでエラーが発生します。
  - `pg_hba.conf` ファイルを更新します。
    - Database Migration Service インスタンスに固有のエントリを追加します。
  - 各サーバーの `postgresql.conf` ファイルの値を変更し、ソース サーバーで論理レプリケーションを有効にする必要があります。
    - `wal_level` = `logical`
    - `max_replication_slots` = [少なくとも、移行するデータベースの最大数]
      - たとえば、4 つのデータベースを移行する場合は、値を 4 に設定します。
    - `max_wal_senders` = [同時に実行されているデータベースの数]
      - 推奨値は 10 です。

- 移行 `User` には、ソース データベースに対する `REPLICATION` ロールが必要です。

- `PostgreSQLpg_hba.conf` ファイルに、Database Migration Service インスタンスの IP アドレスを追加します。

- データベース スキーマをエクスポートします。

  - 次のコマンドを実行します。

    ```cmd
    pg_dump -U postgres -s dvdrental > dvdrental_schema.sql
    ```

  - ファイルをコピーし、そのコピーに `dvdrental_schema_foreign.sql` という名前を付けて、すべての非外部キーおよびトリガー関連項目を削除します。
  - `dvdrental_schema.sql` ファイルから、すべての外部キーおよびトリガー関連項目を削除します。

- データベース スキーマをインポートします (手順 1)。

  ```cmd
    psql -h {host}.postgres.database.azure.com -d dvdrental -U username -f dvdrental_schema.sql
    ```

- 移行:

  - Azure portal で、ご利用の Database Migration Service リソースに移動します。
  - サービスが開始されていない場合は、 **[サービスの開始]** を選択します。
  - **[新しい移行プロジェクト]** を選択します。

    ![新しい移行プロジェクトが強調表示されている](./media/contoso-migration-postgresql-to-azure/azure_migration_service_new_project.png)
    _図 4: 新しい移行の開始。_

  - **[新しいアクティビティ]**  >  **[オンライン データの移行]** の順に選択します。
  - 名前を入力します。
  - ソースとして、 **[PostgreSQL]** を選択します。
  - ターゲットについては、 **[Azure Database for PostgreSQL]** を選択します。

    ![新しい移行プロジェクトが強調表示されている](./media/contoso-migration-postgresql-to-azure/azure_migration_service_new_project02.png)
    _図 5: 新しい移行プロジェクトが強調表示されています。_

  - **[保存]** を選択します。
  - ソース情報を入力します。

    ![新しい移行プロジェクトが強調表示されている](./media/contoso-migration-postgresql-to-azure/azure_migration_service_source.png)
    _図 6: ソース情報の入力。_

  - **[保存]** を選択します。
  - ターゲット情報を入力します。

    ![新しい移行プロジェクトが強調表示されている](./media/contoso-migration-postgresql-to-azure/azure_migration_service_target.png)
    _図 7: ターゲット情報の選択。_

  - **[保存]** を選択します。
  - 移行するデータベースを選択します。 各データベースのスキーマは、以前に移行されているはずです。

    ![新しい移行プロジェクトが強調表示されている](./media/contoso-migration-postgresql-to-azure/azure_migration_service_db.png)
    _図 8: データベースの選択。_

  - **[保存]** を選択します。
  - 詳細設定を構成します。

    ![新しい移行プロジェクトが強調表示されている](./media/contoso-migration-postgresql-to-azure/azure_migration_service_advanced.png)
    _図 9: 詳細設定の構成。_

  - **[保存]** を選択します。
  - アクティビティに名前を付け、 **[実行]** を選択します。

    ![新しい移行プロジェクトが強調表示されている](./media/contoso-migration-postgresql-to-azure/azure_migration_service_summary.png)
    _図 10: アクティビティの名前付けと実行。_

  - 移行を監視する。 何らかのエラーが発生した場合 (外部キー参照が欠落していた場合など) は、再試行が必要になることがあります。
  - `Full load completed` がテーブルの数と一致したら、 **[一括で開始]** を選択します。

    ![新しい移行プロジェクトが強調表示されている](./media/contoso-migration-postgresql-to-azure/azure_migration_service_complete.png)
    _図 11: カットオーバーを開始するための移行の監視。_

  - ソース サーバーからのすべてのトランザクションを停止します。
  - **[確認]** チェック ボックスをオンにしてから、 **[適用]** を選択します。

    ![新しい移行プロジェクトが強調表示されている](./media/contoso-migration-postgresql-to-azure/azure_migration_service_cutover.png)
    _図 12: カットオーバーの実行。_

  - カットオーバーが完了するまで待ちます。

    ![新しい移行プロジェクトが強調表示されている](./media/contoso-migration-postgresql-to-azure/azure_migration_service_finished.png)
    _図 13: カットオーバーの完了。_

  > [!NOTE]
  > 上記の Database Migration Service の手順は、[Azure CLI](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online) を介して行うこともできます。

- データベース スキーマをインポートします (手順 2)。

  ```cmd
    psql -h {host}.postgres.database.azure.com -d dvdrental -U username -f dvdrental_schema_foreign.sql
    ```

- オンプレミスのデータベースを使用するすべてのアプリケーションまたはプロセスを、新しい Azure Database for PostgreSQL データベース インスタンスを指すように再構成します。

- 移行後は、移行が完了したら必要に応じて、リージョン間の読み取りレプリカも確実に設定するようにしてください。

## <a name="clean-up-after-migration"></a>移行後にクリーンアップする

移行後に、Contoso では保有目的でオンプレミス データベースをバックアップし、クリーンアップ プロセスの一環として古い PostgreSQL サーバーを廃止する必要があります。

## <a name="review-the-deployment"></a>デプロイを再調査する

リソースを Azure に移行したら、新しいインフラストラクチャを完全に操作可能にして、セキュリティ保護する必要があります。

### <a name="security"></a>セキュリティ

- Contoso では、新しい Azure Database for PostgreSQL インスタンスとデータベースを確実にセキュリティで保護する必要があります。 [詳細については、こちらを参照してください](https://docs.microsoft.com/azure/postgresql/concepts-security)。
- Contoso では、[ファイアウォール規則](https://docs.microsoft.com/azure/postgresql/concepts-firewall-rules)と仮想ネットワーク構成を確認し、接続が必要なアプリケーションのみに制限されていることを検証する必要があります。
- データの暗号化のために [BYOK](https://docs.microsoft.com/azure/postgresql/concepts-data-encryption-postgresql) を実装します。
- データベースへの [SSL 接続を要求する](https://docs.microsoft.com/azure/postgresql/concepts-ssl-connection-security)ように、すべてのアプリケーションを更新します。
- [Private Link](https://docs.microsoft.com/azure/postgresql/concepts-data-access-and-security-private-link) を設定し、すべてのデータベース トラフィックが Azure とオンプレミスのネットワーク内に保持されるようにします。
- [Azure Advanced Threat Protection (ATP)](https://docs.microsoft.com/azure/postgresql/concepts-data-access-and-security-threat-protection) を有効にします。
- 対象のセキュリティとログのエントリを監視してアラートを生成するように、Log Analytics を構成します。

### <a name="backups"></a>バックアップ

geo リストアを使用して、Azure Database for PostgreSQL データベースが確実にバックアップされるようにします。 これにより、リージョンで障害が発生した場合に、ペアのリージョンでバックアップを使用できるようになります。

> [!IMPORTANT]
> Azure Database for PostgreSQL リソースが削除されないようにするために、確実にリソース ロックが設定されるようにします。 削除されたサーバーは復元できません。

### <a name="licensing-and-cost-optimization"></a>ライセンスとコストの最適化

- Azure Database for PostgreSQL は、スケールアップまたはスケールダウンすることができます。 サーバーとデータベースのパフォーマンスを監視することは、コストを最小限に抑えながら、ニーズを確実に満たすために重要なことです。
- CPU とストレージの両方がコストに関連します。 選択できる価格レベルはいくつかあります。 データ ワークロードに適した価格プランが選択されていることを確認してください。
- 各読み取りレプリカは、選択されたコンピューティングとストレージに基づいて課金されます。

## <a name="conclusion"></a>まとめ

この記事では、Contoso によって、PostgreSQL データベースが Azure Database for PostgreSQL インスタンスに移行されました。
