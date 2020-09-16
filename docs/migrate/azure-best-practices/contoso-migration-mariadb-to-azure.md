---
title: MariaDB データベースを Azure に移行する
description: Contoso が、オンプレミスの MariaDB データベースを Azure にどのように移行したかについて説明します。
author: deltadan
ms.author: abuck
ms.date: 07/01/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: migrate
ms.openlocfilehash: 650b41a6f27c4343422f0d185f2f3255b5f7a42d
ms.sourcegitcommit: 8b82889dca0091f3cc64116f998a3a878943c6a1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2020
ms.locfileid: "89605026"
---
<!-- cSpell:ignore  mysqldump Navicat phpMyAdmin -->

# <a name="migrate-mariadb-databases-to-azure"></a>MariaDB データベースを Azure に移行する

この記事では、Contoso という架空の会社が、オンプレミスの MariaDB オープンソース データベース プラットフォームの Azure への移行をどのように計画し、実行したかについて説明します。

Contoso では、次の理由により、MySQL ではなく MariaDB が使用されています。

- 多数のストレージ エンジン。
- キャッシュとインデックスのパフォーマンス。
- 機能と拡張機能によるオープンソースのサポート。
- 分析用の ColumnStore のサポート。

会社の移行の目標は、MariaDB をサポートするために必要な環境の管理について心配せずに、これを引き続き使用することです。

## <a name="business-drivers"></a>ビジネス ドライバー

IT リーダーシップ チームは、ビジネス パートナーと密接に連絡を取り合い、彼らがこの移行で何を達成しようとしているのかを理解しました。 目的は次のとおりです。

- **可用性の向上。** Contoso は、オンプレミスの MariaDB 環境で可用性の問題を抱えていました。 ビジネス部門は、このデータ ストアを使用するアプリケーションの信頼性を高めることを求めています。
- **効率化。** Contoso では、不要な手順を排除し、開発者とユーザーのプロセスを効率化する必要があります。 ビジネス部門は、顧客の要求により迅速に対応するために、IT 部門に対して、時間やコストを無駄にせず、迅速に作業を行うことを求めています。
- **機敏性の向上。** Contoso IT は、ビジネス部門の要求に対して、対応力を向上させる必要があります。 また、グローバル経済で成功を収めるために、市場の変化よりも迅速に対応する必要があります。 ビジネスの妨げや、障害にならないようにする必要があります。
- **スケール。** ビジネスが順調に成長している中で、Contoso IT 部門は、同じペースで拡張するシステムを提供する必要があります。

## <a name="migration-goals"></a>移行の目標

Contoso クラウド チームは、この移行の目標を設定しました。 これらの目標を使用して、最良の移行方法を決定しました。

| 必要条件 | 詳細 |
| --- | --- |
| **可用性** | 現在、社内スタッフは、MariaDB インスタンスのホスティング環境で苦労しています。 Contoso は、データベース レイヤーの可用性を 99.99% に近づけたいと考えています。 |
| **スケーラビリティ** | オンプレミスのデータベース ホストの容量が急速に減っています。 Contoso は、現在の制限を超えてインスタンスをスケーリングする方法や、ビジネス環境が変化した場合にインスタンスをスケールダウンしてコストを節約する方法を必要としています。 |
| **パフォーマンス** | Contoso の人事 (HR) 部門には、毎日、毎週、および毎月実行される複数のレポートがあります。 これらのレポートを実行すると、従業員向けアプリケーションで重大なパフォーマンスの問題が発生することがわかりました。 アプリケーションのパフォーマンスに影響を与えずに、レポートを実行する必要があります。 |
| **Security** | Contoso は、データベースに内部アプリケーションだけがアクセスでき、インターネット経由で表示およびアクセスできないことがわかっている必要があります。 |
| **Monitoring** | 現在、Contoso では、ツールを使用して MariaDB データベースのメトリックを監視し、CPU、メモリ、またはストレージに問題があるときに通知されるようにしています。 会社は、Azure でもこの同じ機能を利用したいと考えています。 |
| **ビジネス継続性** | HR データ ストアは Contoso の日常業務の重要な部分です。 破損したり、復元が必要になったりした場合に、ダウンタイムを最小限に抑える必要があります。 |
| **Azure** | Contoso は、アプリケーションを VM で実行せずに Azure に移行することを望んでいます。 Contoso の要件には、データ層に Azure のサービスとしてのプラットフォーム (PaaS) サービスを使用することが示されています。 |

## <a name="solution-design"></a>ソリューション設計

Contoso は、目標と要件を明確にした後、デプロイ ソリューションを設計してレビューし、移行プロセスを確認します。 移行に使用するツールとサービスも識別されます。

### <a name="current-application"></a>現在のアプリケーション

MariaDB データベースでは、会社の HR 部門のあらゆる側面に使用される従業員データがホストされています。 [LAMP ベース](https://wikipedia.org/wiki/LAMP_(software_bundle))のアプリケーションは、従業員の HR 要求を処理するフロントエンドとして使用されています。 Contoso は世界中に 100,000 人の従業員を抱えているので、データベースのアップタイムが重要となります。

### <a name="proposed-solution"></a>提案されるソリューション

- 移行の互換性について環境を評価します。
- 一般的なオープンソース ツールを使用して、データベースを Azure Database for MariaDB インスタンスに移行します。
- 新しい Azure Database for MariaDB インスタンスを使用するように、すべてのアプリケーションとプロセスを変更します。

### <a name="database-considerations"></a>データベースの考慮事項

ソリューション設計プロセスの一環として、Contoso は MariaDB データベースをホストするための Azure の機能を確認しました。 Azure の使用を決定する際に、次の考慮事項が役立ちました。

- Azure SQL Database と同様に、Azure Database for MariaDB では[ファイアウォール規則](/azure/mariadb/concepts-firewall-rules)を使用できます。
- インスタンスにパブリックにアクセスできないように、Azure Database for MariaDB を [Azure Virtual Network](/azure/mariadb/concepts-data-access-security-vnet) で使用できます。
- Azure Database for MariaDB には、Contoso が監査担当者のために満たす必要がある、必須のコンプライアンス認定とプライバシー認定があります。
- 読み取りレプリカを使用すると、レポートおよびアプリケーション処理のパフォーマンスが向上します。
- [Azure Private Link](/azure/mariadb/concepts-data-access-security-private-link) を使用して、サービスを内部ネットワーク トラフィックだけに公開できます (パブリック アクセスなし)。
- Contoso では、将来、MariaDB ColumnStore および GraphDBMS データベース モデルを使用する可能性を検討しているため、Azure Database for MySQL に移行しないことを選択しました。
- 選択されたゲートウェイ (Azure ExpressRoute またはサイト間 VPN) に基づいて、アプリケーションからデータベースへの[帯域幅と待機時間](/azure/vpn-gateway/vpn-gateway-about-vpngateways)が十分に確保されます。

### <a name="solution-review"></a>ソリューションのレビュー

Contoso は、長所と短所の一覧をまとめて、提案されたデザインを評価します。

| 考慮事項 | 詳細 |
| --- | --- |
| **長所** | Azure Database for MariaDB は、金銭的な補償を伴う 99.99% のサービス レベル アグリーメント (SLA) で[高可用性](/azure/mariadb/concepts-high-availability)を提供します。 <br><br> Azure では、四半期ごとの負荷のピーク時にスケールアップまたはスケールダウンする機能が提供されます。 Contoso は、[予約容量](/azure/mariadb/concept-reserved-pricing)を購入することでさらに節約できます。 <br><br> Azure では、Azure Database for MariaDB のポイントインタイム リストア機能と geo リストア機能が提供されます。 <br><br> |
| **短所** | Azure でサポートされている MariaDB リリース バージョン (現在、10.2 および 10.3) に限定されます。 <br><br> Azure Database for MariaDB には、ストレージのスケールダウンなどの[制限事項](/azure/mariadb/concepts-limits)があります。 |

## <a name="proposed-architecture"></a>提案されたアーキテクチャ

![シナリオのアーキテクチャを示す図。](./media/contoso-migration-mariadb-to-azure/architecture.png)
_図 1: シナリオのアーキテクチャ_

### <a name="migration-process"></a>移行プロセス

#### <a name="preparation"></a>準備

MariaDB データベースを移行する前に、移行を成功させるために、これらのインスタンスが Azure のすべての前提条件を満たしていることを確認する必要があります。

サポートされているバージョン:

- MariaDB では、x.y.z の名前付けスキームを使用します。 たとえば、x はメジャー バージョン、y はマイナー バージョン、z はパッチ バージョンです。
- Azure では、現在、10.2.25 と 10.3.16 がサポートされています。
- Azure では、パッチの更新プログラムのアップグレードが自動的に管理されます。 10.2.21 から 10.2.23 のような場合です。 マイナー バージョンとメジャー バージョンのアップグレードはサポートされていません。 たとえば、MariaDB 10.2 から MariaDB 10.3 へのアップグレードはサポートされていません。 10.2 から 10.3 にアップグレードする場合は、データベース ダンプを取得し、ターゲット エンジンのバージョンで作成されたサーバーにそれを復元します。

ネットワーク:

Contoso は、オンプレミス環境から、MariaDB データベースが配置されている仮想ネットワークへの仮想ネットワーク ゲートウェイ接続を設定する必要があります。 この接続より、接続文字列が更新されたときに、オンプレミス アプリケーションはゲートウェイを介してデータベースにアクセスできるようになります。

  ![移行プロセスを示す図。](./media/contoso-migration-mariadb-to-azure/migration-process.png)
  "_図 2:移行プロセス_

#### <a name="migration"></a>移行

MariaDB は MySQL に似ているため、Contoso は MySQL Workbench、mysqldump、Toad、Navicat などの同じ一般的なユーティリティとツールを使用して、Azure Database for MariaDB に接続し、データを移行できます。

Contoso では、次の手順を使用してデータベースを移行しました。

- 次のコマンドを実行し、出力を確認して、オンプレミスの MariaDB バージョンを確認します。 ほとんどの場合、スキーマとデータ ダンプに関しては、バージョンはそれほど重要ではありません。 アプリケーション レベルで機能を使用する場合は、それらのアプリケーションが Azure のターゲット バージョンと互換性があることを確認します。

  ```cmd
    mysql -h localhost -u root -P
  ```

  ![オンプレミスの MariaDB のバージョンを確認する方法を示すスクリーンショット。](./media/contoso-migration-mariadb-to-azure/mariadb_version.png)
  _図 3: オンプレミスの MariaDB バージョンの確認_

- Azure で新しい MariaDB インスタンスを作成します。

  - [Azure Portal](https://portal.azure.com)を開きます。
  - **[リソースの追加]** を選択します。
  - `MariaDB` を検索します。

    ![Azure の新しい MariaDB インスタンスを示すスクリーンショット。](./media/contoso-migration-mariadb-to-azure/azure-mariadb-create.png)
    _図 4: Azure の新しい MariaDB インスタンス_

  - **［作成］** を選択します
  - サブスクリプションとリソース グループを選択します。
  - サーバー名と場所を選択します。
  - ターゲットのバージョン (10.2 または 10.3) を選択します。
  - コンピューティングとストレージを選択します。
  - 管理者のユーザー名とパスワードを入力します。
  - **[Review + create]\(レビュー + 作成\)** を選択します。

    ![MariaDB サーバーの作成画面を示すスクリーンショット。](./media/contoso-migration-mariadb-to-azure/azure_mariadb_create.png)
    _図 5:確認と作成_

  - **［作成］** を選択します
  - サーバーのホスト名、ユーザー名、パスワードを記録します。
  - **[接続のセキュリティ]** を選択します。
  - **[クライアント IP の追加]** (データベースの復元元の IP) を選択します。
  - **[保存]** を選択します。

- 次のコマンドを実行して、`Employees` というデータベースをエクスポートします。 データベースごとにこれを繰り返します。

    ```cmd
    mysqldump -h localhost -u root -p -–skip-triggers -–single-transaction –-extended-insert -–order-by-primary -–disable-keys Employees > Employees.sql
    ```

- データベースを復元します。 Azure Database for MariaDB インスタンスのエンドポイントとユーザー名に置き換えます。

  ```cmd
  mysql -h {name}.mariadb.database.azure.com -u user@{name} -p –ssl
  create database employees;
  use database employees;
  source employees.sql;
  ```

- phpMyAdmin または同様のツール (MySQL Workbench、Toad、Navicat など) を使用し、各テーブルのレコード数をチェックして復元を確認します。
- 移行したデータベースを参照するように、すべてのアプリケーション接続文字列を更新します。
- すべてのアプリケーションが正しく動作するかどうかをテストします。

## <a name="clean-up-after-migration"></a>移行後にクリーンアップする

Contoso は、移行が正常に完了したことを確認したら、保持の目的でオンプレミス データベースをバックアップし、バックアップ ファイルを保存する必要があります。 オンプレミスの MariaDB サーバーを廃止します。

## <a name="review-the-deployment"></a>デプロイを再調査する

リソースを Azure に移行したら、新しいインフラストラクチャを完全に操作可能にして、セキュリティで保護する必要があります。

### <a name="security"></a>セキュリティ

Contoso は次のことを行う必要があります。

- 新しい Azure Database for MariaDB インスタンスとデータベースを確実にセキュリティで保護します。 詳細については、「[Azure Database for MariaDB のセキュリティ](/azure/mariadb/concepts-security)」をご覧ください。
- [ファイアウォール規則](/azure/mariadb/concepts-firewall-rules)と仮想ネットワーク構成を確認して、接続を必要とするアプリケーションのみに接続が制限されていることを確認します。
- MariaDB [ゲートウェイ IP アドレス](/azure/mariadb/concepts-connectivity-architecture)への接続を許可するように、送信 IP 要件を構成します。
- データベースへの [SSL 接続を要求する](/azure/mariadb/concepts-ssl-connection-security)ように、すべてのアプリケーションを更新します。
- [Private Link](/azure/mariadb/concepts-data-access-security-private-link) を設定して、すべてのデータベース トラフィックが Azure とオンプレミス ネットワーク内に保持されるようにします。
- [Azure Advanced Threat Protection (ATP)](/azure/mariadb/concepts-data-access-and-security-threat-protection) を有効にします。
- 対象のセキュリティおよびログ エントリを監視し、アラートを送信するように Log Analytics を構成します。

### <a name="backups"></a>バックアップ

geo リストアを使用して、Azure Database for MariaDB データベースが確実にバックアップされるようにします。 これにより、リージョン規模の障害が発生した場合は、ペアのリージョンにあるバックアップを使用できます。

> [!IMPORTANT]
> Azure Database for MariaDB リソースが削除されないように、[リソース ロック](/azure/azure-resource-manager/management/lock-resources)が設定されていることを確認します。 削除されたサーバーを復元することはできません。

### <a name="licensing-and-cost-optimization"></a>ライセンスとコストの最適化

- Azure Database for MariaDB は、スケールアップまたはスケールダウンすることができます。 サーバーとデータベースのパフォーマンスを監視することは、ニーズを満たすためだけではなく、コストを最小限に維持するためにも重要です。
- CPU とストレージの両方がコストに関連します。 選択できる価格レベルはいくつかあります。 データ ワークロードに適した価格プランが選択されていることを確認してください。
- 各読み取りレプリカは、選択されたコンピューティングとストレージに基づいて課金されます。
- 予約容量を使用してコストを節約します。

## <a name="conclusion"></a>まとめ

この記事では、Contoso が MariaDB データベースを Azure Database for MariaDB インスタンスに移行しました。
