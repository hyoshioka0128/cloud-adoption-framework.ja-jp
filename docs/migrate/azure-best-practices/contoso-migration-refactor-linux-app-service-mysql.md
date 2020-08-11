---
title: Linux アプリケーションを Azure App Service、Traffic Manager、および Azure Database for MySQL にリファクターする
description: Azure 向けのクラウド導入フレームワークを使用し、Linux サービス デスク アプリを Azure App Service および Azure Database for MySQL にリファクターする方法について説明します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 07/01/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: migrate
ms.openlocfilehash: e22a33e3de09c3e1dfb9f4adc8bfed023ea1b821
ms.sourcegitcommit: 580a6f66a0d0f3f5b755c68d757a84b2351a432f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2020
ms.locfileid: "87473133"
---
<!-- cSpell:ignore WEBVM SQLVM contosohost vcenter contosodc OSTICKETWEB OSTICKETMYSQL osTicket contosoosticket trafficmanager InnoDB binlog DBHOST DBUSER CNAME -->

# <a name="refactor-a-linux-application-by-using-azure-app-service-traffic-manager-and-azure-database-for-mysql"></a>Azure App Service、Traffic Manager、Azure Database for MySQL を使用して Linux アプリケーションをリファクターする

この記事では、架空の会社である Contoso が、[LAMP ベース](https://wikipedia.org/wiki/LAMP_(software_bundle))の 2 層のアプリケーションをリファクターし、Azure App Service と GitHub の統合、および Azure Database for MySQL を使用して、オンプレミスから Azure に移行する方法を示します。

この例で使用される osTicket (サービス デスク アプリケーション) は、オープンソースとして提供されています。 独自のテストを目的としてこれを使用する場合は、[GitHub の osTicket リポジトリ](https://github.com/osTicket/osTicket)からダウンロードできます。

## <a name="business-drivers"></a>ビジネス ドライバー

IT リーダーシップ チームは、ビジネス パートナーと緊密に連携して、彼らが何を達成したいと望んでいるかを理解しています。

- **ビジネスの成長への対応**。 Contoso は成長し続けており、新しい市場に進出しつつあります。 そのため顧客サービス担当者を追加する必要があります。
- **スケール**。 Contoso は、事業規模が拡大したときに、顧客サービス担当者を追加できるように、ソリューションを構築する必要があります。
- **回復性の向上**。 過去にシステムで発生した問題は、内部ユーザーのみに影響していました。 新しいビジネス モデルでは、外部ユーザーも影響を受けるため、Contoso は、アプリケーションを常時稼働させる必要があります。

## <a name="migration-goals"></a>移行の目標

最適な移行方法を判断するために、Contoso クラウド チームは、この移行に関して目標を設定しました。

- アプリケーションは、現在のオンプレミスのキャパシティとパフォーマンスを越えるようにスケーリングする必要があります。 Contoso は、Azure のオンデマンド スケーリングを活用するためにアプリケーションを移行します。
- Contoso は、アプリケーションのコード ベースを継続的デリバリー パイプラインに移したいと考えています。 アプリケーションの変更が GitHub にプッシュされたときに、Contoso は運用スタッフのタスクなしでその変更をデプロイしたいと考えています。
- アプリケーションは、成長とフェールオーバーに対応するための機能を備えた回復力のあるものにする必要があります。 Contoso は 2 つの異なる Azure リージョンにアプリケーションをデプロイし、自動的にスケーリングするように設定したいと考えています。
- Contoso は、アプリケーションがクラウドに移された後、データベース管理タスクを最小限にしたいと考えています。

## <a name="solution-design"></a>ソリューション設計

Contoso は目標と要件を決定した後、デプロイ ソリューションを設計およびレビューし、移行に使用する Azure サービスを含め、移行プロセスを決めます。

## <a name="current-architecture"></a>現在のアーキテクチャ

- アプリケーションは 2 つの仮想マシン (VM) (`OSTICKETWEB` と `OSTICKETMYSQL`) に階層化されています。
- これらの VM は、VMware ESXi ホスト `contosohost1.contoso.com` (バージョン 6.5) 上に配置されています。
- VMware 環境は、VM で実行中のvCenter Server 6.5 (`vcenter.contoso.com`) によって管理されています。
- Contoso には、オンプレミスのデータセンター (`contoso-datacenter`) があり、オンプレミスのドメイン コントローラー (`contosodc1`) が含まれています。

![現在のアーキテクチャの図。](./media/contoso-migration-refactor-linux-app-service-mysql/current-architecture.png)

## <a name="proposed-architecture"></a>提案されたアーキテクチャ

次のアーキテクチャが提案されます。

- `OSTICKETWEB` 上の Web 層のアプリは、Azure App Service Web アプリを 2 つの Azure リージョン内にビルドすることで移行されます。 Contoso チームは、PHP 7.0 Docker コンテナーを使用して Azure App Service for Linux を実装します。
- アプリケーション コードは GitHub に移動され、Azure App Service Web アプリが GitHub を使用した継続的デリバリー用に構成されます。
- Azure App Service は、プライマリ リージョン (`East US 2`) とセカンダリ リージョン (`Central US`) の両方にデプロイされます。
- 両方のリージョンで、2 つの Web アプリの前に Azure Traffic Manager が設定されます。
- Traffic Manager は、トラフィックを強制的に `East US 2` を通過させるために、優先モードで構成されます。
- `East US 2` 内の Azure アプリ サーバーがオフラインになった場合でも、ユーザーは、`Central US` にフェールオーバーされたアプリケーションにアクセスできます。
- アプリケーションのデータベースは、Azure Database Migration Service を使用して Azure Database for MySQL サービスに移行されます。 オンプレミスのデータベースはローカルでバックアップされ、Azure Database for MySQL に直接復元されます。
- このデータベースは、運用ネットワーク (`VNET-PROD-EUS2`) のデータベース サブネット (`PROD-DB-EUS2`) のプライマリ リージョン (`East US 2`) に存在することになります。
- 運用ワークロードを移行しようとしているため、アプリケーション用の Azure リソースは、運用リソース グループ `ContosoRG` 内に存在することになります。
- Traffic Manager リソースは、Contoso のインフラストラクチャ リソース グループ `ContosoInfraRG` にデプロイされます。
- Contoso データセンター内のオンプレミス VM は、移行が行われた後に使用停止にされます。

![シナリオ アーキテクチャの図。](./media/contoso-migration-refactor-linux-app-service-mysql/proposed-architecture.png)

## <a name="migration-process"></a>移行プロセス

Contoso は、次のようにして移行プロセスを完了します。

1. 最初のステップとして、Contoso 管理者は、Azure インフラストラクチャをセットアップします。これには、Azure App Service のプロビジョニング、Traffic Manager の設定、および Azure Database for MySQL インスタンスのプロビジョニングが含まれます。
2. Azure インフラストラクチャを準備した後、Azure Database Migration Service を使用してデータベースを移行します。
3. Azure でデータベースが実行されたら、継続的デリバリーを使用して Azure App Service 用の GitHub プライベート リポジトリをアップロードし、それに osTicket アプリケーションを読み込みます。
4. Azure portal で、Azure App Service を実行することによって、Docker コンテナーに GitHub からアプリケーションを読み込みます。
5. DNS の設定を調整し、アプリケーションの自動スケーリングを構成します。

![Contoso の移行プロセスの図。](./media/contoso-migration-refactor-linux-app-service-mysql/migration-process.png)

### <a name="azure-services"></a>Azure サービス

| サービス | 説明 | コスト |
| --- | --- | --- |
| [Azure App Service](https://azure.microsoft.com/services/app-service) | このサービスでは、Web サイト向けの Azure PaaS (サービスとしてのプラットフォーム) サービスを使用してアプリケーションを実行およびスケーリングします。 | 価格は、インスタンスのサイズと必要な機能に基づきます。 [詳細については、こちらを参照してください](https://azure.microsoft.com/pricing/details/app-service/windows)。 |
| [Azure の Traffic Manager](https://azure.microsoft.com/services/traffic-manager) | ドメイン ネーム システム (DNS) を使用して、Azure、外部 Web サイト、またはサービスにユーザーを振り分けるロード バランサー。 | 価格は、受信した DNS クエリの数と監視対象のエンドポイントの数に基づきます。 | [詳細については、こちらを参照してください](https://azure.microsoft.com/pricing/details/traffic-manager)。 |
| [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview) | Azure Database Migration Service を使用すると、複数のデータベース ソースから Azure データ プラットフォームに、ダウンタイムを最小限に抑えながらシームレスに移行できます。 | [サポートされているリージョン](https://docs.microsoft.com/azure/dms/dms-overview#regional-availability)に関する情報と、[Database Migration Service の価格](https://azure.microsoft.com/pricing/details/database-migration)に関する情報をご覧ください。 |
| [Azure Database for MySQL](https://docs.microsoft.com/azure/mysql) | このデータベースは、オープン ソースの MySQL データベース エンジンに基づいています。 これは、フルマネージドのエンタープライズ対応コミュニティ MySQL データベースであり、アプリケーションの開発とデプロイに使用できます。 | 価格は、コンピューティング、ストレージ、バックアップ要件に基づきます。 [詳細については、こちらを参照してください](https://azure.microsoft.com/pricing/details/mysql)。 |

## <a name="prerequisites"></a>前提条件

このシナリオを実行するには、Contoso は次の前提条件を満たす必要があります。

| 必要条件 | 詳細 |
| --- | --- |
| **Azure サブスクリプション** | Contoso は、この記事シリーズの前の記事でサブスクリプションを作成しました。 Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/free)を作成してください。 <br><br> 無料アカウントを作成する場合、サブスクリプションの管理者としてすべてのアクションを実行できます。 <br><br> 既存のサブスクリプションを使用しており、管理者でない場合は、管理者に依頼して所有者アクセス許可または共同作成者アクセス許可を割り当ててもらう必要があります。 |
| **Azure インフラストラクチャ** | Contoso は、[移行のための Azure インフラストラクチャ](./contoso-migration-infrastructure.md)についての記事で説明されているように、Azure インフラストラクチャを設定します。 |

## <a name="scenario-steps"></a>シナリオのステップ

移行を完了するための Contoso のプランは次のとおりです。

> [!div class="checklist"]
>
> - **ステップ 1:Azure App Service をプロビジョニングする**。 Contoso 管理者は、プライマリ リージョンとセカンダリ リージョンに Web Apps をプロビジョニングします。
> - **手順 2:Traffic Manager を設定する**。 トラフィックのルーティングと負荷分散を行う ために、Traffic Manager を Web アプリの前に設定します。
> - **ステップ 3:Azure Database for MySQL をプロビジョニングする**。 Azure で、Azure Database for MySQL のインスタンスをプロビジョニングします。
> - **手順 4:データベースを移行する**。 Azure Database Migration Service を使用してデータベースを移行します。
> - **手順 5:GitHub を設定する**。 アプリケーションの Web サイトとコード用のローカル GitHub リポジトリを設定します。
> - **手順 6:Web アプリを構成する**。 Web アプリと osTicket Web サイトを構成します。

## <a name="step-1-provision-azure-app-service"></a>手順 1:Azure App Service をプロビジョニングする

Contoso 管理者は、Azure App Service を使用して 2 つの Web アプリを (各リージョンに 1 つずつ) プロビジョニングします。

1. Azure Marketplace を介してプライマリ リージョン (`East US 2`) 内に Web アプリ リソース (`osticket-eus2`) を作成します。
2. 運用リソース グループ `ContosoRG` にリソースを配置します。

    ![Linux に Web アプリを作成するための [Web App] ペインのスクリーンショット。](./media/contoso-migration-refactor-linux-app-service-mysql/azure-app1.png)

3. プライマリ リージョンに、Standard サイズを使用して App Service プラン (**APP-SVP-EUS2**) を作成します。

     ![App Service プランを作成するための [新しい App Service プラン] ペインのスクリーンショット。](./media/contoso-migration-refactor-linux-app-service-mysql/azure-app2.png)

4. Contoso は、Docker コンテナーである PHP 7.0 ランタイム スタックを使用する Linux OS を選択します。

    ![Linux OS、米国東部 2 という場所、PHP 7.0 が選択された [Web App] ペインのスクリーンショット。](./media/contoso-migration-refactor-linux-app-service-mysql/azure-app3.png)

5. **米国中部**で、2 つ目の Web アプリ (**osticket-cus**) と Azure App Service プランを作成します。

    ![Linux OS、米国中部リージョン、PHP 7.0 が選択された [Web App] ペインのスクリーンショット。](./media/contoso-migration-refactor-linux-app-service-mysql/azure-app4.png)

**さらにサポートが必要な場合**

- [Azure App Service Web アプリ](https://docs.microsoft.com/azure/app-service/overview)に関する記事を参照します。
- 「[Azure App Service on Linux の概要](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-intro)」を参照します。

## <a name="step-2-set-up-traffic-manager"></a>手順 2:Traffic Manager を設定する

Contoso 管理者は、インバウンド Web 要求を osTicket Web 層で実行中の Web アプリに送る Traffic Manager を設定します。

1. Azure Marketplace で、Traffic Manager リソース (**osticket.trafficmanager.net**) を作成します。 **米国東部 2** がプライマリ サイトになるように、優先順位によるルーティングを使用します。 そのリソースを、Contoso の既存のインフラストラクチャ リソース グループ (**ContosoInfraRG**) 内に配置します。 Traffic Manager はグローバルであり、特定の場所にバインドされないことに注意してください。

    ![[Traffic Manager プロファイルの作成] ペインのスクリーンショット。](./media/contoso-migration-refactor-linux-app-service-mysql/traffic-manager1.png)

2. Traffic Manager のエンドポイントを構成します。 米国東部 2 の Web アプリをプライマリ サイト (**osticket-eus2**) として追加し、米国中部の Web アプリをセカンダリ サイト (**osticket-cus**) として追加します。

    ![Traffic Manager の [エンドポイントの追加] ペインのスクリーンショット。](./media/contoso-migration-refactor-linux-app-service-mysql/traffic-manager2.png)

3. エンドポイントを追加した後、管理者がそれらを監視できます。

    ![Traffic Manager のエンドポイントを監視するための [エンドポイント] ペインのスクリーンショット。](./media/contoso-migration-refactor-linux-app-service-mysql/traffic-manager3.png)

**さらにサポートが必要な場合**

- 「[Traffic Manager の概要](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview)」を参照します。
- [優先順位の高いエンドポイントへのトラフィックのルーティング](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-configure-priority-routing-method)に関する記事を参照します。

## <a name="step-3-provision-azure-database-for-mysql"></a>手順 3:Azure Database for MySQL をプロビジョニングする

Contoso の管理者は、MySQL データベース インスタンスを、プライマリ リージョン (米国東部 2) 内にプロビジョニングします。

1. Azure Portal で、Azure Database for MySQL のリソースを作成します。

    ![Azure portal の Azure Database for MySQL リンクのスクリーンショット。](./media/contoso-migration-refactor-linux-app-service-mysql/mysql-1.png)

2. Azure データベースの名前 **contosoosticket** を追加します。 運用リソース グループ **ContosoRG** にデータベースを追加し、そのための資格情報を指定します。
3. オンプレミスの MySQL データベースはバージョン 5.7 なので、互換性のためにこのバージョンが選択されています。 既定のサイズを使用しますが、それらはデータベースの要件に適合しています。

    ![バージョン 5.7 が選択された [MySQL サーバー] ペインのスクリーンショット。](./media/contoso-migration-refactor-linux-app-service-mysql/mysql-2.png)

4. **バックアップ冗長オプション**には **[geo 冗長]** を選択します。 このオプションでは、障害が発生した場合、セカンダリ リージョン (米国中部) でデータベースを復元できます。 このオプションは、データベースをプロビジョニングするときにのみ構成できます。

    ![[geo 冗長] オプションを選択した [バックアップ冗長オプション] ペインのスクリーンショット。](./media/contoso-migration-refactor-linux-app-service-mysql/db-redundancy.png)

5. 接続のセキュリティを設定します。 データベースの **[接続のセキュリティ]** を選択し、Azure サービスへのアクセスをデータベースに許可するファイアウォール ルールを設定します。

6. 開始 IP アドレスと終了 IP アドレスに、ローカル ワークステーションのクライアント IP アドレスを追加します。 これにより、Web アプリが MySQL データベースと、移行を実行するデータベース クライアントにアクセスできるようになります。

    ![Azure サービスへのアクセスを有効にし、クライアントの IP アドレスを選択した [接続のセキュリティ] ペインのスクリーンショット。](./media/contoso-migration-refactor-linux-app-service-mysql/mysql-3.png)

## <a name="step-4-migrate-the-database"></a>手順 4:データベースを移行する

MySQL データベースは、次の方法で移動できます。 それぞれのオプションで、Contoso の管理者はターゲットに対して Azure Database for MySQL インスタンスを作成する必要があります。 インスタンスの作成後、2 つのいずれかの方法を使用してデータベースを移行できます。

- 手順 4a: Azure Database Migration Service
- 手順 4b: MySQL Workbench のバックアップと復元

### <a name="step-4a-migrate-the-database-via-azure-database-migration-service"></a>手順 4a: Azure Database Migration Service を使用してデータベースを移行する

Contoso の管理者は、[ステップバイステップの移行チュートリアル](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online)に従って、Azure Database Migration Service を使用してデータベースを移行します。 MySQL 5.6 または 5.7 を使用して、オンライン、オフライン、およびハイブリッド (プレビュー) の移行を実行できます。

> [!NOTE]
> MySQL 8.0 は Azure Database for MySQL でサポートされていますが、Database Migration Service ツールではこのバージョンはまだサポートされていません。

Contoso が行う作業の概要は次のとおりです。

- 移行の前提条件がすべて満たされていることを確認します。
  - MySQL データベース サーバー ソースは、Azure Database for MySQL でサポートされているバージョンと一致する必要があります。 Azure Database for MySQL では、MySQL Community Edition および InnoDB ストレージ エンジンがサポートされていると共に、同じバージョンのソースとターゲット間の移行がサポートされています。  
  - `my.ini` (Windows) または `my.cnf` (Unix) でバイナリ ログを有効にします。 これを行わないと、移行ウィザードで次のエラーが発生します。  
      
    "バイナリ ログにエラーがあります。 変数 binlog_row_image の値が "minimal" です。 その値を "full" に変更してください。 詳細については、`https://go.microsoft.com/fwlink/?linkid=873009` をご覧ください。"
    
  - ユーザーは `ReplicationAdmin` ロールを持っている必要があります。  
  - 外部キーとトリガーを使用せずにデータベース スキーマを移行します。  
- ExpressRoute または仮想プライベート ネットワーク (VPN) を介してオンプレミス ネットワークに接続する VPN を作成します。  
- 仮想ネットワークに接続されている Premium SKU を使用して、Azure Database Migration Service インスタンスを作成します。  
- Azure Database Migration Service が仮想ネットワーク経由で MySQL データベースに確実にアクセスできるようにします。 これには、仮想ネットワーク レベル、ネットワーク VPN、および MySQL をホストするマシンで、Azure から MySQL にすべての受信ポートが許可されていることの確認が伴います。  
- Database Migration Service ツールを実行し、次の作業を行います。  

  a. Premium SKU に基づいて移行プロジェクトを作成します。

    ![MySQL の [概要] ペインのスクリーンショット (移行サービスが正常に作成されたことを示すメッセージが表示されている)。](./media/contoso-migration-refactor-linux-app-service-mysql/migration-dms-new-project.png)

    ![MySQL の [新しい移行プロジェクト] ペインのスクリーンショット。](./media/contoso-migration-refactor-linux-app-service-mysql/migration-dms-new-project-02.png)

  b. ソース (オンプレミス データベース) を追加します。

    ![移行ウィザードの [ソースの追加に関する詳細] ペインのスクリーンショット。](./media/contoso-migration-refactor-linux-app-service-mysql/migration-dms-source.png)

  c. ターゲットを選択します。

    ![移行ウィザードの [ターゲットの詳細] ペインのスクリーンショット。](./media/contoso-migration-refactor-linux-app-service-mysql/migration-dms-target.png)

  d. 移行するデータベースを選択します。

    ![移行ウィザードの [ターゲット データベースへマッピング] ペインのスクリーンショット。](./media/contoso-migration-refactor-linux-app-service-mysql/migration-dms-databases.png)

  e. 詳細設定を構成します。

    ![移行ウィザードの [移行の設定] ペインのスクリーンショット。](./media/contoso-migration-refactor-linux-app-service-mysql/migration-dms-settings.png)

  f. レプリケーションを開始し、エラーを解決します。

    ![サーバーの詳細ペインのスクリーンショット。](./media/contoso-migration-refactor-linux-app-service-mysql/migration-dms-monitor.png)

  g. 最終的なカットオーバーを実行します。

    ![osTicket の詳細ペインのスクリーンショット。](./media/contoso-migration-refactor-linux-app-service-mysql/migration-dms-cutover.png)

    ![[一括を完了する] ペインのスクリーンショット。](./media/contoso-migration-refactor-linux-app-service-mysql/migration-dms-cutover-complete.png)

    ![移行アクティビティの状態を表示するテーブルのスクリーンショット。](./media/contoso-migration-refactor-linux-app-service-mysql/migration-dms-cutover-complete-02.png)

  h. 外部キーとトリガーをすべて復帰させます。

  i. 新しいデータベースを使用するようにアプリケーションを変更します。

    !["移行アクティビティ" テーブルのスクリーンショット。](./media/contoso-migration-refactor-linux-app-service-mysql/migration-dms-cutover-apps.png)

### <a name="step-4b-migrate-the-database-mysql-workbench"></a>手順 4b: データベースの移行 (MySQL Workbench)

1. Contoso の管理者が[前提条件を確認して MySQL Workbench をダウンロード](https://dev.mysql.com/downloads/workbench/?utm_source=tuicool)します。
2. [インストールの指示](https://dev.mysql.com/doc/workbench/en/wb-installing.html)に従って、MySQL Workbench for Windows をインストールします。 MySQL Workbench のインストール先となるマシンは、OSTICKETMYSQL VM と Azure にインターネット経由でアクセス可能である必要があります。
3. MySQL Workbench で、OSTICKETMYSQL への MySQL 接続を作成します。

    ![MySQL Workbench の接続の詳細ペインのスクリーンショット。](./media/contoso-migration-refactor-linux-app-service-mysql/workbench1.png)

4. データベースを `osticket` として、ローカルの自己完結型ファイルにエクスポートします。

    ![MySQL Workbench の [Data Export]\(データのエクスポート\) ペインのスクリーンショット。](./media/contoso-migration-refactor-linux-app-service-mysql/workbench2.png)

5. 管理者は、データベースをローカルにバックアップした後、Azure Database for MySQL インスタンスへの接続を作成します。

    ![MySQL Workbench の [Setup New Connection]\(新しい接続の設定\) ペイン。](./media/contoso-migration-refactor-linux-app-service-mysql/workbench3.png)

6. これで、自己完結型ファイルから、Azure Database for MySQL インスタンス内にあるデータベースをインポート (復元) できます。 このインスタンスに新しいスキーマ (`osticket`) が作成されます。

    ![MySQL Workbench の [Data Import]\(データのインポート\) ペインのスクリーンショット。](./media/contoso-migration-refactor-linux-app-service-mysql/workbench4.png)

7. 管理者は、データの復元後、MySQL Workbench を使用してデータを照会することができます。 そのデータは Azure portal に表示されます。

    ![復元されたデータを表示する Azure portal のスクリーンショット。](./media/contoso-migration-refactor-linux-app-service-mysql/workbench5.png)

    ![復元されたデータを表示する Azure portal のスクリーンショット。](./media/contoso-migration-refactor-linux-app-service-mysql/workbench6.png)

8. 管理者が Web アプリ上のデータベース情報を更新します。 MySQL インスタンスで、 **[接続文字列]** を開きます。

    ![MySQL インスタンスの [接続文字列] リンクのスクリーンショット。](./media/contoso-migration-refactor-linux-app-service-mysql/workbench7.png)

9. 接続文字列の一覧で、Web アプリの設定を選択し、 **[クリックしてコピー]** を選択してコピーします。

    ![MySQL インスタンスにおける Web アプリの設定のスクリーンショット。](./media/contoso-migration-refactor-linux-app-service-mysql/workbench8.png)

10. 新しいファイルをメモ帳で開いて文字列を貼り付け、osTicket データベース、MySQL インスタンス、資格情報の設定と一致するように文字列を更新します。

    ![メモ帳ファイルに貼り付けた接続文字列のスクリーンショット。](./media/contoso-migration-refactor-linux-app-service-mysql/workbench9.png)

11. Azure portal で MySQL インスタンスの **[概要]** ペインからサーバー名とログインを確認できます。

    ![サーバー名とサーバー管理者ログイン名が表示されたリソース グループ ペインのスクリーンショット。](./media/contoso-migration-refactor-linux-app-service-mysql/workbench10.png)

## <a name="step-5-set-up-github"></a>手順 5:GitHub を設定する

Contoso 管理者は、新しいプライベート GitHub リポジトリを作成し、Azure Database for MySQL の osTicket データベースへの接続を設定します。 次に、Web アプリを Azure App Service に読み込みます。

1. osTicket ソフトウェアのパブリック GitHub リポジトリを参照し、Contoso GitHub アカウントにフォークします。

    ![[Fork]\(フォーク\) ボタンが強調表示されている GitHub リポジトリ ページのスクリーンショット。](./media/contoso-migration-refactor-linux-app-service-mysql/github1.png)

2. リポジトリをフォークしたら、*include* フォルダーに移動し、*ost-config.php* ファイルを見つけて選択します。

    ![GitHub における PHP ファイルのスクリーンショット。](./media/contoso-migration-refactor-linux-app-service-mysql/github2.png)

3. ブラウザーでファイルを開いて編集します。

    ![GitHub におけるファイル編集 (鉛筆) アイコンのスクリーンショット。](./media/contoso-migration-refactor-linux-app-service-mysql/github3.png)

4. エディターで、管理者がデータベースの詳細 (具体的には `DBHOST` と `DBUSER` に関して) を更新します。

    ![GitHub におけるファイル編集ペインのスクリーンショット。](./media/contoso-migration-refactor-linux-app-service-mysql/github4.png)

5. 変更をコミットします。

    ![[Commit changes]\(変更のコミット\) ボタンを強調表示した編集ペインのスクリーンショット。](./media/contoso-migration-refactor-linux-app-service-mysql/github5.png)

6. それぞれの Web アプリ (osticket-eus2 と osticket-cus) について、Azure portal の左ペインで **[アプリケーションの設定]** を選択し、設定を編集します。

    ![Azure portal の [アプリケーションの設定] リンクを強調表示した画面のスクリーンショット。](./media/contoso-migration-refactor-linux-app-service-mysql/github6.png)

7. `osticket` という名前で接続文字列を入力し、メモ帳から**領域の値**に文字列をコピーします。 文字列の横のドロップダウン リストから **[MySQL]** を選択し、設定を保存します。

    ![osTicket の接続文字列が強調表示されている [接続文字列] ペインのスクリーンショット。](./media/contoso-migration-refactor-linux-app-service-mysql/github7.png)

## <a name="step-6-configure-the-web-apps"></a>手順 6:Web アプリを構成する

移行プロセスの最後のステップとして、Contoso 管理者は、Web アプリと osTicket Web サイトを構成します。

1. プライマリ Web アプリ (osticket-eus2) で **[デプロイ オプション]** を開き、ソースを **[GitHub]** に設定します。

    ![ソースとして GitHub が選択された [デプロイ オプション] ペインのスクリーンショット。](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app1.png)

2. デプロイ オプションを選択します。

    ![[デプロイ オプション] ペインに表示されるオプション詳細のスクリーンショット。](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app2.png)

3. オプションを設定すると、その構成が Azure portal に "*保留中*" として表示されます。

    ![サイトの状態が保留中と表示されている [デプロイ オプション] ペインのスクリーンショット。](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app3.png)

4. 構成が更新され、GitHub から、Azure App Service を実行中の Docker コンテナーに osTicket Web アプリが読み込まれたら、サイトが "*アクティブ*" として表示されます。

    ![[デプロイ オプション] ペインのスクリーンショット。](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app4.png)

5. セカンダリ Web アプリ (osticket-cus) に対して、上記のステップを繰り返します。
6. サイトが構成されたら、Traffic Manager プロファイルを使用してアクセスできます。 DNS 名は、osTicket アプリケーションの新しい場所になります。 [詳細については、こちらを参照してください](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain#map-a-cname-record)。

    ![DNS 名を表示する [Traffic Manager プロファイル] ペインのスクリーンショット。](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app5.png)

7. Contoso は、覚えやすい DNS 名を使用したいと考えています。 **[新しいリソース レコード]** ペインで、Traffic Manager の名前を指すエイリアス (**CNAME**) と完全修飾ドメイン名 (**osticket.contoso.com**) をドメイン コントローラーの DNS に作成します。

    ![Traffic Manager のポインターとエイリアス名を表示する [新しいリソース レコード] ペインのスクリーンショット。](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app6.png)

8. カスタム ホスト名を許可するように osticket-eus2 と osticket-cus Web アプリの両方を構成します。

    ![[Validate]\(検証\) ボタンが強調表示された [ホスト名の追加] ペインのスクリーンショット。](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app7.png)

### <a name="set-up-autoscaling"></a>自動スケーリングを設定する

最後に、Contoso の管理者がアプリケーションの自動スケーリングを設定します。 自動スケーリングにより、エージェントがアプリケーションを使用するときに、ビジネス ニーズに応じてアプリケーションのインスタンスが増減することが保証されます。

1. App Service **APP-SVP-EUS2** で、 **[スケール ユニット]** を開きます。
2. 現在のインスタンスの CPU 使用率が 10 分の間 70% を超える場合はインスタンス数を 1 つ増加させるという単一のルールを含んだ新しい自動スケーリング設定を構成します。

    ![1 つ目のリージョンの [自動スケーリング設定] ページのスクリーンショット。](./media/contoso-migration-refactor-linux-app-service-mysql/autoscale1.png)

3. 同じ設定を **APP-SVP-CUS** でも構成して、アプリがセカンダリ リージョンにフェールオーバーした場合に、同じ動作が確実に適用されるようにします。 唯一の違いは、これは目的がフェールオーバーのみであるため、既定のインスタンスを 1 に設定することです。

   ![2 つ目のリージョンの [自動スケーリング設定] ページのスクリーンショット。](./media/contoso-migration-refactor-linux-app-service-mysql/autoscale2.png)

## <a name="clean-up-after-migration"></a>移行後にクリーンアップする

移行が完了すると、osTicket アプリケーションは、プライベート GitHub リポジトリを使用して継続的デリバリーを行う Azure App Service Web アプリで実行されるようにリファクターされます。 アプリケーションは 2 つのリージョンで実行されることで、回復性が向上します。 PaaS プラットフォームへの移行後、osTicket データベースは Azure Database for MySQL 内で実行されます。

移行後にクリーンアップするために、Contoso は以下を行います。

- VMware VM を vCenter インベントリから削除します。
- ローカルのバックアップ ジョブからからオンプレミスの VM を削除します。
- 社内のドキュメントを更新して、新しい場所と IP アドレスを示します。
- こうしたオンプレミスの VM と対話しているリソースがないか確認し、新しい構成が反映されるように、関連する設定やドキュメントをすべて更新します。
- `osticket-trafficmanager.net` URL を指すように監視を再構成することで、アプリケーションが稼働状態にあることを追跡します。

## <a name="review-the-deployment"></a>デプロイを再調査する

アプリケーションが実行されるようになり、Contoso は新しいインフラストラクチャを完全に操作可能にして、セキュリティで保護する必要があります。

### <a name="security"></a>セキュリティ

Contoso のセキュリティ チームは、アプリケーションの確認を行い、セキュリティの問題を特定します。 osTicket アプリケーションと MySQL データベース インスタンスの間の通信が SSL 用に構成されていないことを認識します。 データベース トラフィックをハッキングできないようにするのがその目的です。 [詳細については、こちらを参照してください](https://docs.microsoft.com/azure/mysql/howto-configure-ssl)。

### <a name="backups"></a>バックアップ

- OsTicket web アプリには状態データが含まれていないため、バックアップは必要ありません。
- Contoso チームは、データベースのバックアップを構成する必要はありません。 Azure Database for MySQL は、サーバーのバックアップを自動的に作成して保存します。 チームはデータベースのために geo 冗長性を使用することを選択したため、これは耐障害性があり、運用準備ができています。 バックアップを使用すると、サーバーを特定の時点に復元できます。 [詳細については、こちらを参照してください](https://docs.microsoft.com/azure/mysql/concepts-backup)。

### <a name="licensing-and-cost-optimization"></a>ライセンスとコストの最適化

- PaaS のデプロイにライセンスの問題はありません。
- Contoso は [Azure Cost Management と Billing](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview) を使用して、IT リーダーが定めた予算内に確実に収まるようにします。
