---
title: MySQL データベースを Azure に移行する
description: Contoso が、オンプレミスの MySQL データベースを Azure にどのように移行したかについて説明します。
author: deltadan
ms.author: abuck
ms.date: 07/01/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: migrate
ms.openlocfilehash: 1a331e02dcf51123fbf0c0977cf75e9dfd4bae81
ms.sourcegitcommit: 4e12d2417f646c72abf9fa7959faebc3abee99d8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2020
ms.locfileid: "90775872"
---
<!-- cSpell:ignore mysqldump InnoDB binlog Navicat -->

# <a name="migrate-mysql-databases-to-azure"></a>MySQL データベースを Azure に移行する

この記事では、Contoso という架空の会社が、オンプレミスの MySQL オープンソース データベース プラットフォームの Azure への移行をどのように計画し、実行したかについて説明します。

## <a name="business-drivers"></a>ビジネス ドライバー

IT リーダーシップ チームは、ビジネス パートナーと密接に連絡を取り合い、彼らがこの移行で何を達成しようとしているのかを理解しました。 目的は次のとおりです。

- **可用性の向上。** Contoso は、オンプレミスの MySQL 環境で可用性の問題を抱えていました。 ビジネス部門は、このデータ ストアを使用するアプリケーションの信頼性を高めることを求めています。
- **効率化。** Contoso では、不要な手順を排除し、開発者とユーザーのプロセスを効率化する必要があります。 ビジネス部門は、顧客の要求により迅速に対応するために、IT 部門に対して、時間やコストを無駄にせず、迅速に作業を行うことを求めています。
- **機敏性の向上。** Contoso IT は、ビジネス部門の要求に対して、対応力を向上させる必要があります。 また、グローバル経済で成功を収めるために、市場の変化よりも迅速に対応する必要があります。 ビジネスの妨げにならないようにする必要があります。
- **スケール。** ビジネスが順調に成長している中で、Contoso IT 部門は、同じペースで拡張するシステムを提供する必要があります。

## <a name="migration-goals"></a>移行の目標

Contoso クラウド チームは、この移行の目標を設定しました。 これらの目標を使用して、最良の移行方法を決定しました。

| 必要条件 | 詳細 |
| --- | --- |
| **可用性** | 現在、社内スタッフは、MySQL インスタンスのホスティング環境で苦労しています。 Contoso は、データベース レイヤーの可用性を 99.99% に近づけたいと考えています。 |
| **スケーラビリティ** | オンプレミスのデータベース ホストの容量が急速に減っています。 Contoso は、現在の制限を超えてインスタンスをスケーリングする方法や、ビジネス環境が変化した場合にインスタンスをスケールダウンしてコストを節約する方法を必要としています。 |
| **パフォーマンス** | Contoso の人事 (HR) 部門では、毎日、毎週、および毎月さまざまなレポートを実行しています。 これらのレポートを実行すると、従業員向けアプリケーションで重大なパフォーマンスの問題が発生します。 アプリケーションのパフォーマンスに影響を与えずに、レポートを実行する必要があります。 |
| **Security** | Contoso は、データベースに内部アプリケーションだけがアクセスでき、インターネット経由で表示およびアクセスできないことがわかっている必要があります。 |
| **Monitoring** | 現在、Contoso では、ツールを使用して MySQL データベース サーバーのメトリックを監視し、CPU、メモリ、またはストレージに問題があるときに通知されるようにしています。 会社は、Azure でもこの同じ機能を利用したいと考えています。 |
| **ビジネス継続性** | HR データ ストアは Contoso の日常業務の重要な部分です。 破損したり、復元が必要になったりした場合に、可能な限りダウンタイムを最小限に抑える必要があります。 |
| **Azure** | Contoso は、アプリケーションを VM で実行せずに Azure に移行することを望んでいます。 Contoso は、データ層に Azure のサービスとしてのプラットフォーム (PaaS) サービスを使用したいと考えています。 |

## <a name="solution-design"></a>ソリューション設計

Contoso は、目標と要件を明確にした後、デプロイ ソリューションを設計してレビューし、移行プロセスを確認します。 移行に使用するツールとサービスも識別されます。

### <a name="current-application"></a>現在のアプリケーション

MySQL データベースには、会社の HR 部門のあらゆる側面に使用される従業員データが格納されています。 [LAMP ベース](https://wikipedia.org/wiki/LAMP_(software_bundle))のアプリケーションは、従業員の HR 要求を処理するフロントエンドとして使用されています。 Contoso は世界中に 100,000 人の従業員を抱えているので、アップタイムが重要になります。

### <a name="proposed-solution"></a>提案されるソリューション

Azure Database Migration Service を使用して、データベースを Azure Database for MySQL インスタンスに移行します。 新しい Azure Database for MySQL インスタンスを使用するように、すべてのアプリケーションとプロセスを変更します。

### <a name="database-considerations"></a>データベースの考慮事項

<!-- TODO: Verify GraphDBMS term -->
<!-- docutune:casing ColumnStore "Graph DBMS" -->

ソリューションの設計プロセスの一環として、Contoso では MySQL データをホストするための Azure の機能を確認しました。 Azure の使用を決定する際に、次の考慮事項が役立ちました。

- Azure SQL Database と同様に、Azure Database for MySQL では[ファイアウォール規則](/azure/mysql/concepts-firewall-rules)を使用できます。
- インスタンスにパブリックにアクセスできないように、Azure Database for MySQL を [Azure Virtual Network](/azure/mysql/concepts-data-access-and-security-vnet) で使用できます。
- Azure Database for MySQL には、Contoso が監査担当者のために満たす必要がある、必須のコンプライアンス認定とプライバシー認定があります。
- 読み取りレプリカを使用すると、レポートおよびアプリケーション処理のパフォーマンスが向上します。
- [Azure Private Link](/azure/mysql/concepts-data-access-security-private-link) を使用して、サービスを内部ネットワーク トラフィックだけに公開できます (パブリック アクセスなし)。
- Contoso では、将来、MariaDB ColumnStore および Graph DBMS データベース モデルを使用する可能性を検討しているため、Azure Database for MySQL に移行しないことを選択しました。
- MySQL の機能は別として、Contoso は真のオープンソース プロジェクトを支持しており、MySQL を使用しないことを選択しました。
- 選択されたゲートウェイ (Azure ExpressRoute またはサイト間 VPN) に基づいて、アプリケーションからデータベースへの[帯域幅と待機時間](/azure/vpn-gateway/vpn-gateway-about-vpngateways)が十分に確保されます。

### <a name="solution-review"></a>ソリューションのレビュー

Contoso は、長所と短所の一覧をまとめて、提案されたデザインを評価します。

| 考慮事項 | 詳細 |
| --- | --- |
| **長所** | Azure Database for MySQL は、金銭的な補償を伴う 99.99% のサービス レベル アグリーメント (SLA) で[高可用性](/azure/mysql/concepts-high-availability)を提供します。 <br><br> Azure では、四半期ごとの負荷のピーク時にスケールアップまたはスケールダウンする機能が提供されます。 Contoso は、[予約容量](/azure/mysql/concept-reserved-pricing)を購入することでさらに節約できます。 <br><br> Azure では、Azure Database for MySQL のポイントインタイム リストア機能と geo リストア機能が提供されます。 <br><br> |
| **短所** | Azure でサポートされている MySQL リリース バージョン (現在、10.2 および 10.3) に限定されます。 <br><br> Azure Database for MySQL には、ストレージのスケールダウンなどの[制限事項](/azure/mysql/concepts-limits)があります。 |

## <a name="proposed-architecture"></a>提案されたアーキテクチャ

![シナリオのアーキテクチャを示す図。](./media/contoso-migration-mysql-to-azure/architecture.png)
_図 1: シナリオのアーキテクチャ_

### <a name="migration-process"></a>移行プロセス

#### <a name="preparation"></a>準備

MySQL データベースを移行する前に、移行を成功させるために、これらのインスタンスが Azure のすべての前提条件を満たしていることを確認する必要があります。

#### <a name="supported-versions"></a>サポートされているバージョン

MySQL では、X.Y.Z のバージョン管理スキームを使用します。 たとえば、X はメジャー バージョン、Y はマイナー バージョン、Z はパッチ バージョンです。

Azure では、現在、10.2.25 と 10.3.16 がサポートされています。

Azure では、パッチの更新プログラムのアップグレードが自動的に管理されます。 10.2.21 から 10.2.23 のような場合です。 マイナー バージョンとメジャー バージョンのアップグレードはサポートされていません。 たとえば、MySQL 10.2 から MySQL 10.3 へのアップグレードはサポートされていません。 10.2 から 10.3 にアップグレードする場合は、ダンプを取得し、新しいエンジンのバージョンで作成されたサーバーにそれを復元します。

#### <a name="network"></a>ネットワーク

Contoso は、オンプレミス環境から、MySQL データベースが配置されている仮想ネットワークへの仮想ネットワーク ゲートウェイ接続を設定する必要があります。 この接続より、接続文字列が更新されたときに、オンプレミス アプリケーションはゲートウェイを介してデータベースにアクセスできるようになります。

![移行プロセスを示す図。](./media/contoso-migration-mysql-to-azure/migration-process.png)
_図 2: 移行プロセス_

#### <a name="migration"></a>移行

Contoso の管理者は、Azure Database Migration Service を使用し、[ステップバイステップの移行チュートリアル](/azure/dms/tutorial-mysql-azure-mysql-online)に従って、データベースを移行します。 MySQL 5.6 または 5.7 を使用して、オンライン、オフライン、およびハイブリッド (プレビュー) の移行を実行できます。

> [!NOTE]
> Azure Database for MySQL では、MySQL 8.0 がサポートされています。 Database Migration Service ツールでは、そのバージョンはまだサポートされていません。

まとめると、次のタスクを実行する必要があります。

- 次のすべての移行の前提条件が満たされていることを確認します。
  - MySQL データベース サーバー ソースは、Azure Database for MySQL でサポートされているバージョンと一致する必要があります。 Azure Database for MySQL では、MySQL Community Edition および InnoDB ストレージ エンジンがサポートされていると共に、同じバージョンのソースとターゲット間の移行がサポートされています。
  - `my.ini` (Windows) または `my.cnf` (Unix) でバイナリ ログを有効にします。 バイナリ ログを有効にしないと、移行ウィザードで以下のエラーが発生します。"バイナリ ログにエラーがあります。 変数 binlog_row_image の値が 'minimal' です。 その値を 'full' に変更してください。"詳細については、[MySQL の Web サイト](https://go.microsoft.com/fwlink/?linkid=873009`)を参照してください。
  - ユーザーは `ReplicationAdmin` ロールを持っている必要があります。
  - 外部キーとトリガーを使用せずにデータベース スキーマを移行します。
- ExpressRoute または VPN を介してオンプレミス ネットワークに接続する仮想ネットワークを作成します。
- 仮想ネットワークに接続されている `Premium` SKU を使用して、Azure Database Migration Service インスタンスを作成します。
- そのインスタンスから、仮想ネットワーク経由で MySQL データベースに確実にアクセスできるようにします。 仮想ネットワーク レベル、ネットワーク VPN、および MySQL をホストするマシンで、Azure から MySQL にすべての受信ポートが許可されていることを確認します。
- 新しい Database Migration Service プロジェクトを作成します。

  ![新しい Database Migration Service プロジェクトを作成する方法を示すスクリーンショット](./media/contoso-migration-mysql-to-azure/migration-dms-new-project.png)
  _図 3: Azure Database Migration Service プロジェクト。_

#### <a name="migration-by-using-native-tools"></a>ネイティブ ツールを使用した移行

Azure Database Migration Service を使用する代わりに、Contoso は MySQL Workbench、mysqldump、Toad、Navicat などの一般的なユーティリティとツールを使用して、Azure Database for MySQL に接続し、データを移行できます。

- mysqldump を使用したダンプと復元:
  - mysqldump で exclude-triggers オプションを使用します。これにより、インポート中にトリガーが実行されなくなり、パフォーマンスが向上します。
  - single-transaction オプションを使用して、変換分離モードを `REPEATABLE READ` に設定し、データをダンプする前に `START TRANSACTION` SQL ステートメントを送信します。
  - mysqldump で disable-keys オプションを使用して、読み込み前に外部キー制約を無効にします。 制約を削除すると、パフォーマンスが向上します。
  - Azure Blob Storage を使用してバックアップ ファイルを格納し、そこから復元を実行して復元を高速化します。
  - アプリケーション接続文字列を更新します。
  - データベースが移行されたら、Contoso は新しい Azure Database for MySQL を参照するように接続文字列を更新する必要があります。

## <a name="clean-up-after-migration"></a>移行後にクリーンアップする

移行後に、Contoso は保持の目的でオンプレミス データベースをバックアップし、オンプレミスの MySQL データベース サーバーを廃止する必要があります。

## <a name="review-the-deployment"></a>デプロイを再調査する

リソースを Azure に移行したら、新しいインフラストラクチャを完全に操作可能にして、セキュリティで保護する必要があります。

### <a name="security"></a>セキュリティ

Contoso は次のことを行う必要があります。

- 新しい Azure Database for MySQL インスタンスとデータベースを確実にセキュリティで保護します。 詳細については、「[Azure Database for MySQL のセキュリティ](/azure/mysql/concepts-security)」をご覧ください。
- ファイアウォールと仮想ネットワークの構成を確認します。
- Private Link を設定して、すべてのデータベース トラフィックが Azure とオンプレミスのネットワーク内に保持されるようにします。
- Azure Advanced Threat Protection を有効にします。

### <a name="backups"></a>バックアップ

geo リストアを使用して、Azure Database for MySQL インスタンスが確実にバックアップされるようにします。 これにより、リージョン規模の障害が発生した場合は、ペアのリージョンにあるバックアップを使用できます。

> [!IMPORTANT]
> Azure Database for MySQL リソースが削除されないように、リソース ロックが設定されていることを確認します。 削除されたサーバーを復元することはできません。

### <a name="licensing-and-cost-optimization"></a>ライセンスとコストの最適化

- Azure Database for MySQL は、スケールアップまたはスケールダウンすることができます。 コストを最小限に抑えながら要件を満たすことができるように、サーバーとデータベースのパフォーマンスを監視することが重要です。
- CPU とストレージの両方にコストが関連付けられています。 複数の価格レベルを利用できます。 各データ ワークロードに適した価格プランが選択されていることを確認してください。
- 各読み取りレプリカは、選択されたコンピューティングとストレージに基づいて課金されます。
- 予約容量を使用してコストを節約します。

## <a name="conclusion"></a>まとめ

この記事では、Contoso が MySQL データベースを Azure Database for MySQL インスタンスに移行しました。
