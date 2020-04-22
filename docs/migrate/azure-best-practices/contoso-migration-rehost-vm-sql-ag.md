---
title: アプリを Azure VM と SQL Server Always On 可用性グループに移行して再ホストする
description: Contoso がオンプレミス アプリを Azure VM および SQL Server Always On 可用性グループに移行して再ホストする方法について説明します。
author: givenscj
ms.author: abuck
ms.date: 04/02/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: migrate
services: azure-migrate
ms.openlocfilehash: 6d59781c94355a6b5ca980950ca4f1e73108e818
ms.sourcegitcommit: 7d3fc1e407cd18c4fc7c4964a77885907a9b85c0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2020
ms.locfileid: "80996804"
---
<!-- cSpell:ignore givenscj WEBVM SQLVM contosohost vcenter contosodc AOAG SQLAOG SQLAOGAVSET contosoadmin contosocloudwitness MSSQLSERVER BEPOOL contosovmsacc SHAOG NSGs inetpub iisreset -->

# <a name="rehost-an-on-premises-app-with-azure-virtual-machines-and-sql-server-always-on-availability-groups"></a>Azure Virtual Machines と SQL Server Always On 可用性グループでオンプレミス アプリを再ホストする

この記事では、Contoso という架空の会社が、Azure への移行の一環として、VMware 仮想マシン (VM) 上で実行される 2 階層の Windows .NET アプリを再ホストする方法を説明します。 アプリのフロントエンド VM を Azure VM、アプリのデータベースを Azure SQL Server VM に移行し、SQL Server Always On 可能性グループを含む Windows Server フェールオーバー クラスターで実行するようにします。

この例で使用される SmartHotel360 アプリは、オープン ソースとして提供されています。 独自のテスト目的に沿って使用する場合は、[GitHub](https://github.com/Microsoft/SmartHotel360) からダウンロードできます。

## <a name="business-drivers"></a>ビジネス ドライバー

IT リーダーシップ チームは、ビジネス パートナーと密接に連絡を取り合い、彼らがこの移行で何を達成しようとしているのかを理解しました。

- **ビジネスの成長への対応。** Contoso は成長を続けています。そのため、オンプレミスのシステムとインフラストラクチャに負荷がかかっています。

- **効率化。** Contoso では、不要な手順を取り除き、開発者とユーザーのプロセスを効率化する必要があります。 ビジネス部門は IT に対して、時間やコストを無駄にせず、迅速に作業を行ってもらう必要があります。これは、例えば、顧客の要求に素早く対応するためです。

- **機敏性の向上。** Contoso IT は、ビジネス部門の要求に対して、対応力を向上させる必要があります。 また、グローバル経済で成功を収めるために、市場の変化に対して、より迅速な対応ができる必要があります。 ビジネスの妨げになったり、ビジネスの機会を壊すようなことがあってはなりません。

- **スケール。** ビジネスが順調に成長していく中で、Contoso IT は、同じペースで拡張できるシステムを提供する必要があります。

## <a name="migration-goals"></a>移行の目標

Contoso クラウド チームは、この移行の目標を設定しました。 これらの目標を使用して、最良の移行方法を決定しました。

- 移行しても、Azure 内のアプリは、現在の VMware と同じパフォーマンスを発揮できる必要があります。 このアプリは、オンプレミスの場合と同様に、クラウド内にあっても非常に重要であり続けます。

- Contoso は、このアプリへの投資を望んでいません。 これはビジネスにとって重要なものですが、Contoso は現状のまま確実にクラウドに移すことだけを望んでいます。

- このアプリのオンプレミス データベースでは、可用性の問題が発生しています。 Contoso の希望は、フェールオーバー機能を備えた高可用性クラスターとしてこのデータベースを Azure にデプロイすることです。

- Contoso は、現在の SQL Server 2008 R2 プラットフォームを SQL Server 2017 にアップグレードしようとしています。

- Contoso は、このアプリに Azure SQL Database を使用したくないと考えており、別のデータベースを探しています。

## <a name="solution-design"></a>ソリューション設計

Contoso は目標と要件を決定した後、デプロイ ソリューションを設計およびレビューし、移行に使用する Azure サービスを含め、移行プロセスを決めます。

### <a name="current-architecture"></a>現在のアーキテクチャ

- アプリは 2つの VM (WEBVM および SQLVM) に階層化されています。
- VM は、VMware ESXi ホスト **contosohost1.contoso.com** (バージョン 6.5) 上に配置されます。
- VMware 環境は、VM 上で実行中の vCenter Server 6.5 (**vcenter.contoso.com**) によって管理されています。
- Contoso にはオンプレミスのデータセンター (contoso-datacenter) があり、そこにオンプレミスのドメイン コントローラー (**contosodc1**) が含まれています。

### <a name="proposed-architecture"></a>提案されたアーキテクチャ

このシナリオでは:

- Contoso は、アプリのフロントエンド WEBVM を Azure IaaS VM に移行します。
  - このフロントエンド VM は、Azure の ContosoRG リソース グループ (運用リソースで使用される) にデプロイされます。
  - 配置先は、プライマリ リージョンである米国東部 2 の Azure 運用ネットワーク (VNET-PROD-EUS2) です。
- アプリ データベースは、Azure SQL Server VM に移行されます。
  - 配置先は、プライマリ リージョンである米国東部 2 にある Contoso の Azure データベース ネットワーク (PROD-DB-EUS2) です。
  - SQL Server Always On 可用性グループを使った、2 ノードの Windows Server フェールオーバー クラスターに置かれます。
  - Azure の ContosoRG リソース グループには、このクラスター内の 2 つの SQL Server VM ノードがデプロイされます。
  - これらの VM ノードの配置先は、プライマリ リージョンである米国東部 2 の Azure 運用ネットワーク (VNET-PROD-EUS2) です。
  - VM では、Windows Server 2016 と SQL Server 2017 Enterprise Edition が実行されます。 Contoso はこのオペレーティング システムのライセンスを所有していないため、ライセンスを提供する Azure Marketplace のイメージを使用します (これは Azure EA コミットメントに請求されます)。
  - 両方の VM は、一意の名前を除いて同じ設定を使用します。
- Contoso は、クラスター上のトラフィックをリッスンする内部ロード バランサーをデプロイし、適切なクラスター ノードにトラフィックをリダイレクトします。
  - 内部ロード バランサーは、ContosoNetworkingRG (ネットワーク リソースで使用される) にデプロイされます。
- Contoso データセンター内のオンプレミス VM は、移行が行われた後に使用停止にされます。

    ![シナリオのアーキテクチャ](./media/contoso-migration-rehost-vm-sql-ag/architecture.png)

### <a name="database-considerations"></a>データベースの考慮事項

Contoso はソリューション設計プロセスの一環として、Azure SQL Database と SQL Server の機能を比較しました。 次の事柄を検討した結果、同社は、SQL Server を実行する Azure IaaS VM を採用することに決定しました。

- Contoso がオペレーティング システムまたはデータベース サーバーをカスタマイズしなければならなくなった場合や、同じ VM にサードパーティのアプリを併置して実行することが必要になった場合、SQL Server を実行する Azure VM を使用することが最適な解決策であると考えられる。

### <a name="solution-review"></a>ソリューションのレビュー

Contoso は、長所と短所の一覧をまとめて、提案されたデザインを評価します。

<!-- markdownlint-disable MD033 -->

**考慮事項** | **詳細**
--- | ---
**長所** | WEBVM は変更することなく Azure に移されるため、移行が簡単で済みます。<br/><br/> SQL Server 層は、SQL Server 2017 と Windows Server 2016 で実行されます。 これによって現在の Windows Server 2008 R2 オペレーティング システムは廃止されることになりますが、Contoso の技術的要件と目標は、SQL Server 2017 を実行することで満たすことができます。 SQL Server 2008 R2 から移行しても 100% の互換性が確保されます。<br/><br/> Contoso は、Azure ハイブリッド特典を使用して、ソフトウェア アシュアランスへの投資を活かすことができます。<br/><br/> Azure への高可用性 SQL Server のデプロイによってフォールト トレランスが得られるので、アプリのデータ層が単一フェールオーバー ポイントではなくなります。
**短所** | WEBVM では、Windows Server 2008 R2 が実行されています。 このオペレーティング システムは、特定のロールを対象に Azure でサポートされます (2018 年 7 月)。 [詳細については、こちらを参照してください](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)。<br/><br/> アプリの Web 層は、依然として単一フェールオーバー ポイントになります。<br/><br/> Contoso は、Azure App Service といったマネージド サービスにアプリを移行するのではなく、Azure VM として Web 層を引き続きサポートする必要があります。<br/><br/> このソリューションを選択した場合、Contoso は、Azure SQL Database Managed Instance などのマネージド プラットフォームに移行するのではなく、引き続き 2 つの SQL Server VM を管理する必要があります。 また、ソフトウェア アシュアランスに基づき、Contoso は、既存のライセンスを Azure SQL Database Managed Instance の割引料金のライセンスに交換することができます。

<!-- markdownlint-enable MD033 -->

### <a name="azure-services"></a>Azure サービス

**サービス** | **説明** | **コスト**
--- | --- | ---
[Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview) | Azure Database Migration Service を使用すると、複数のデータベース ソースから Azure データ プラットフォームに、ダウンタイムを最小限に抑えながらシームレスに移行できます。 | [サポートされているリージョン](https://docs.microsoft.com/azure/dms/dms-overview#regional-availability)に関する情報と、[Database Migration Service の価格](https://azure.microsoft.com/pricing/details/database-migration)に関する情報をご覧ください。
[Azure Migrate](https://docs.microsoft.com/azure/migrate/migrate-overview) | Contoso は、Azure Migrate サービスを使用して VMware VM を評価します。 Azure Migrate は、マシンの移行適合性を評価します。 そのうえで、Azure で実行するための、サイズとコストの見積もりを提供します。 | 2018 年 5 月の時点で Azure Migrate は無料サービスです。

## <a name="migration-process"></a>移行プロセス

Contoso の管理者はアプリの VM を Azure に移行します。

- フロントエンド VM は、Azure Migrate を使用して Azure VM に移行します。
  - 最初の手順として、Azure コンポーネントを準備およびセットアップし、オンプレミス VMware インフラストラクチャを準備します。
  - すべての準備ができたら、VM のレプリケートを開始できます。
  - レプリケーションが有効になり作動したら、VM を Azure にフェールオーバーすることによって移行します。
- データベースを検証すると、Data Migration Service (DMS) を使用して、Azure の SQL Server クラスターにデータベースを移行します。
  - 最初の手順として、Azure の SQL Server VM をプロビジョニングし、クラスターと内部ロード バランサーをセットアップし、Always On 可用性グループを構成します。
  - このように配置された状態で、データベースを移行することができます。
- 移行後に、データベースの Always On 保護を有効にします。

    ![移行プロセス](./media/contoso-migration-rehost-vm-sql-ag/migration-process.png)

## <a name="prerequisites"></a>前提条件

このシナリオで Contoso が行うべきことを以下に示します。

<!-- markdownlint-disable MD033 -->

**必要条件** | **詳細**
--- | ---
**Azure サブスクリプション** | このシリーズの先行する記事の中で、Contoso は既にサブスクリプションを作成しました。 Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/pricing/free-trial)を作成してください。<br/><br/> 無料アカウントを作成する場合、サブスクリプションの管理者としてすべてのアクションを実行できます。<br/><br/> 既存のサブスクリプションを使用しており、管理者でない場合は、管理者に依頼して所有者アクセス許可または共同作成者アクセス許可を割り当ててもらう必要があります。<br/><br/>
**Azure インフラストラクチャ** | Contoso は、[移行のための Azure インフラストラクチャ](./contoso-migration-infrastructure.md)についての記事で説明されているように、Azure インフラストラクチャを設定します。<br/><br/> Azure Migrate Server Migration の具体的な[前提条件](https://docs.microsoft.com/azure/migrate/contoso-migration-rehost-linux-vm#prerequisites)の要件の詳細について確認してください。
**オンプレミスのサーバー** | オンプレミス vCenter Server は、バージョン 5.5、6.0、6.5、または 6.7 を実行している必要があります<br/><br/> バージョン 5.5、6.0、6.5、または 6.7 を実行している ESXi ホスト<br/><br/> ESXi ホスト上で実行している 1 つ以上の VMware VM。
**オンプレミスの VM** | Azure での実行が保証されている [Linux マシンを確認します](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)。

<!-- markdownlint-enable MD033 -->

## <a name="scenario-steps"></a>シナリオのステップ

Contoso が移行を実行する方法を次に示します。

> [!div class="checklist"]
>
> - **ステップ 1:AOAG クラスターを準備する。** 2 つの SQL Server VM ノードを Azure にデプロイするためのクラスターを作成します。
> - **手順 2:クラスターをデプロイして設定する。** Azure SQL Server クラスターを準備します。 データベースは、この既存のクラスターに移行されます。
> - **ステップ 3:Azure Load Balancer をデプロイする。** 複数の SQL Server ノードにトラフィックの負荷を分散するロード バランサーをデプロイします。
> - **手順 4:Azure Migrate 用に Azure を準備する。** レプリケートされたデータを保持する Azure Storage アカウントを作成します。
> - **手順 5:Azure Migrate 用のオンプレミス VMware を準備する。** VM を検出し、エージェントをインストールするためのアカウントを準備します。 移行後にユーザーが Azure VM に接続できるように、オンプレミスの VM を準備します。
> - **手順 6:VM をレプリケートする。** Azure への VM レプリケーションを有効にします。
> - **手順 7:Data Migration Service (DMS) を使用してデータベースを移行する。** Database Migration Service を使用してデータベースを移行します。
> - **手順 8: データベースを保護する。** クラスターの Always On 可用性グループを作成します。
> - **手順 9:Azure Migrate で VM を移行する**テスト フェールオーバーを実行して、すべて想定どおりに動作していることを確認します。 その後、Azure への完全なフェールオーバーを実行します。

## <a name="step-1-prepare-a-sql-server-always-on-availability-group-cluster"></a>手順 1:SQL Server Always On 可用性グループ クラスターを準備する

Contoso の管理者は、クラスターを以下のように設定します。

1. Azure Marketplace で SQL Server 2017 Enterprise Windows Server 2016 イメージを選択して、2 つの SQL Server VM を作成します。

    ![SQL VM SKU](./media/contoso-migration-rehost-vm-sql-ag/sql-vm-sku.png)

2. **仮想マシンの作成ウィザード** >  **[基本]** で、次のように構成します。

    - VM の名前: **SQLAOG1** および **SQLAOG2**。
    - マシンは Bus Critical であるため、VM ディスクの種類について SSD を有効にします。
    - コンピューターの資格情報を指定します。
    - VM を、米国東部 2 プライマリ リージョンおよび ContosoRG リソース グループにデプロイします。

3. **[サイズ]** については、両方の VM で D2s_V3 SKU から開始します。 必要に応じて後から拡大縮小します。
4. **[設定]** では、次のようにします。

    - これらの VM はアプリの重要なデータベースであるため、マネージド ディスクを使用します。
    - 米国東部 2 プライマリ リージョン (**VNET-PROD-EUS2**) の運用ネットワーク、データベース サブネット (**PROD-DB-EUS2**) にマシンを配置します。
    - 2 つの障害ドメインと 5 つの更新ドメインを含む新しい可用性セット **SQLAOGAVSET** を作成します。

      ![SQL VM](./media/contoso-migration-rehost-vm-sql-ag/sql-vm-settings.png)

5. **[SQL Server の設定]** で、SQL 接続をデフォルト ポート 1433 上の仮想ネットワーク (プライベート) に制限します。 認証については、オンサイトで使用するのと同じ資格情報 (**contosoadmin**) を使用します。

    ![SQL VM](./media/contoso-migration-rehost-vm-sql-ag/sql-vm-db.png)

**さらにサポートが必要な場合**

- SQL Server VM のプロビジョニングの[手順を参照](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision#1-configure-basic-settings)します。
- さまざまな SQL Server SKU の VM の構成方法を[参照します](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-prereq#create-sql-server-vms)。

## <a name="step-2-deploy-and-set-up-the-cluster"></a>手順 2:クラスターをデプロイして設定する

Contoso の管理者がクラスターを設定する方法は次のとおりです。

1. クラウド監視として動作するように Azure Storage アカウントを設定します。
2. SQL Server VM を Contoso オンプレミス データセンターの Active Directory ドメインに追加します。
3. Azure においてクラスターを作成します。
4. クラウド監視を構成します。
5. 最後に SQL Always On 可用性グループを有効にします。

### <a name="set-up-a-storage-account-as-cloud-witness"></a>ストレージ アカウントをクラウド監視として設定する

Contoso は、クラウド監視を設定するために、クラスターの調停に使用される BLOB ファイルを保持する Azure Storage アカウントが必要です。 複数のクラスターのクラウド監視の設定に同じストレージ アカウントを使用できます。

Contoso の管理者は、次のようにストレージ アカウントを作成します。

1. アカウントのわかりやすい名前 (**contosocloudwitness**) を指定します。
2. LRS の汎用アカウントをデプロイします。
3. サード リージョン (米国中南部) にアカウントを配置します。 リージョンで障害が発生した際にも使用できるように、プライマリ リージョンやセカンダリ リージョン以外に配置します。
4. インフラストラクチャ リソースを保持するリソース グループ (**ContosoInfraRG**) に配置します。

    ![クラウド監視](./media/contoso-migration-rehost-vm-sql-ag/witness-storage.png)

5. ストレージ アカウントを作成すると、対応するプライマリおよびセカンダリ アクセス キーが生成されます。 クラウド監視を作成するにはプライマリ アクセス キーが必要です。 このキーはストレージ アカウント名の下の **[アクセス キー]** に表示されます。

    ![アクセス キー](./media/contoso-migration-rehost-vm-sql-ag/access-key.png)

### <a name="add-sql-server-vms-to-contoso-domain"></a>Contoso ドメインに SQL Server VM を追加する

1. Contoso は SQLAOG1 および SQLAOG2 を contoso.com domain に追加します。
2. 次に、各 VM に Windows フェールオーバー クラスター機能およびツールをインストールします。

### <a name="set-up-the-cluster"></a>クラスターをセットアップする

クラスターを設定する前に、Contoso の管理者は、各マシン上の OS ディスクのスナップショットを取得します。

![スナップショットの作成](./media/contoso-migration-rehost-vm-sql-ag/snapshot.png)

1. 次に、Windows フェールオーバー クラスターを作成するためにまとめたスクリプトを実行します。

    ![クラスターの作成](./media/contoso-migration-rehost-vm-sql-ag/create-cluster1.png)

2. クラスターを作成したら、VM がクラスター ノードとして表示されることを確認します。

     ![クラスターの作成](./media/contoso-migration-rehost-vm-sql-ag/create-cluster2.png)

### <a name="configure-the-cloud-witness"></a>クラウド監視を構成する

1. Contoso の管理者は、フェールオーバー クラスター マネージャーの**クォーラム構成ウィザード**を使用してクラウド監視を構成します。
2. ウィザードで、ストレージ アカウントでクラウド監視を作成することを選択します。
3. クラウド監視が構成されると、フェールオーバー クラスター マネージャー スナップインに表示されます。

    ![クラウド監視](./media/contoso-migration-rehost-vm-sql-ag/cloud-witness.png)

### <a name="enable-sql-server-always-on-availability-groups"></a>SQL Server Always On 可用性グループを有効にする

これで、Contoso の管理者は Always On を有効にできます。

1. SQL Server 構成マネージャーで、**SQL Server (MSSQLSERVER)** サービスについて **Always On 可用性グループ**を有効にします。

    ![Always On を有効にする](./media/contoso-migration-rehost-vm-sql-ag/enable-always-on.png)

2. 変更を有効にするためにサービスを再起動します。

Always On を有効にすると、Contoso は SmartHotel360 データベースを保護する Always On 可用性グループを設定できます。

**さらにサポートが必要な場合**

- クラウド監視とそのためのストレージ アカウントの設定について[こちらをお読みください](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness)。
- クラスターの設定と可用性グループの作成の[手順はこちらを参照してください](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-tutorial)。

## <a name="step-3-deploy-the-azure-load-balancer"></a>手順 3:Azure Load Balancer をデプロイする

ここで、Contoso の管理者は、クラスター ノードの前に置く内部ロード バランサーをデプロイしようとします。 ロード バランサーは、トラフィックをリッスンし、適切なノードにリダイレクトします。

![負荷分散](./media/contoso-migration-rehost-vm-sql-ag/architecture-lb.png)

次のようにロード バランサーを作成します。

1. Azure portal で **[ネットワーク]**  >  **[ロード バランサー]** に移動し、新しい内部ロード バランサー**ILB-PROD-DB-EUS2-SQLAOG** を設定します。
2. 運用ネットワーク **VNET-PROD-EUS2**、データベース サブネット **PROD-DB-EUS2** にロード バランサーを配置します。
3. 静的 IP アドレス 10.245.40.100 を割り当てます。
4. ロード バランサーをネットワークの要素としてネットワーク リソース グループ **ContosoNetworkingRG** にデプロイします。

    ![負荷分散](./media/contoso-migration-rehost-vm-sql-ag/lb-create.png)

内部ロード バランサーがデプロイされたら、その設定を行う必要があります。 バックエンド アドレス プールを作成し、正常性プローブを設定し、負荷分散規則を構成します。

### <a name="add-a-back-end-pool"></a>バックエンド プールを追加する

Contoso の管理者は、クラスターの VM にトラフィックを分散させるために、ロード バランサーからネットワーク トラフィックを受け取る VM の NIC の IP アドレスを含むバックエンド アドレス プールを設定します。

1. ポータルのロード バランサー設定で、Contoso はバックエンド プールを追加します。**ILB-PROD-DB-EUS-SQLAOG-BEPOOL** を追加します。
2. このプールには可用性セット SQLAOGAVSET を関連付けます。 セット内の VM (**SQLAOG1** および **SQLAOG2**) がプールに追加されます。

    ![バックエンド プール](./media/contoso-migration-rehost-vm-sql-ag/backend-pool.png)

### <a name="create-a-health-probe"></a>正常性プローブの作成

Contoso の管理者は正常性プローブを作成して、ロード バランサーがアプリの正常性を監視できるようにします。 プローブは、正常性チェックへの VM の応答に基づき、ロード バランサーのローテーションに対して VM を動的に追加または削除します。

次のようにプローブを作成します。

1. ポータルのロード バランサー設定で、正常性プローブ **SQLAlwaysOnEndPointProbe** を作成します。
2. TCP ポート 59999 上で VM を監視するようにプローブを設定します。
3. プローブの間隔は 5 秒、しきい値は 2 に設定します。 プローブが 2 回失敗すると、VM が異常と見なされます。

    ![プローブ](./media/contoso-migration-rehost-vm-sql-ag/nlb-probe.png)

### <a name="configure-the-load-balancer-to-receive-traffic"></a>トラフィックを受信するロード バランサーを構成する

ここで、Contoso の管理者は、VM に対するトラフィックの分散方法を定義するロード バランサー規則を設定する必要があります。

- フロントエンド IP アドレスが受信トラフィックを処理します。
- バックエンド IP プールがトラフィックを受信します。

次のように規則を作成します。

1. ポータルのロード バランサー設定で、Contoso は新しいルールを追加します。**SQLAlwaysOnEndPointListener** を追加します。
2. フロントエンド リスナーが TCP 1433 に着信する SQL クライアント トラフィックを受信するように設定します。
3. トラフィックのルーティング先のバックエンド プールと、VM がトラフィックをリッスンするポートを指定します。
4. [フローティング IP (ダイレクト サーバー リターン)] を有効にします。 これは SQL Always On では常に必要です。

    ![プローブ](./media/contoso-migration-rehost-vm-sql-ag/nlb-probe.png)

**さらにサポートが必要な場合**

- Azure Load Balancer の[概要を参照してください](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)。
- ロード バランサーを作成する方法を[参照します](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-basic-internal-portal)。

## <a name="step-4-prepare-azure-for-azure-migrate"></a>手順 4:Azure Migrate 用に Azure を準備する

Contoso が Azure Migrate をデプロイする必要がある Azure コンポーネントは次のとおりです。

- フェールオーバー時に作成される VM が配置される VNet。
- レプリケートされたデータを保持する Azure Storage アカウント。

Contoso の管理者は、これらを次のように設定します。

1. Contoso は、[Azure インフラストラクチャのデプロイ](./contoso-migration-rehost-vm-sql-ag.md)を行ったときに、Azure Migrate 用に使用できるネットワークとサブネットを既に作成しています。

    - SmartHotel360 アプリは運用アプリであり、WEBVM はプライマリ リージョンである米国東部 2 の Azure 運用ネットワーク (VNET-PROD-EUS2) に移行されます。
    - WEBVM は、運用リソースのために使用される ContosoRG リソース グループと、運用サブネット (PROD-FE-EUS2) に配置されます。

2. Contoso の管理者は、プライマリ リージョンの中に Azure のストレージ アカウント (contosovmsacc20180528) を作成します。

    - 標準ストレージと LRS レプリケーション付きの汎用アカウントを使用します。

## <a name="step-5-prepare-on-premises-vmware-for-azure-migrate"></a>手順 5:Azure Migrate 用のオンプレミス VMware を準備する

Contoso の管理者は、オンプレミスで以下を準備します。

- VM の検出を自動化するための vCenter サーバーまたは vSphere ESXi ホストのアカウント。
- フェールオーバー後にレプリケートされた Azure VM に Contoso が接続できるようにするためのオンプレミスの VM の設定。

### <a name="prepare-an-account-for-automatic-discovery"></a>自動検出用のアカウントを準備する

Azure Migrate では、次のことを実行するために、VMware サーバーへのアクセスが必要です。

- VM を自動的に検出します。
- レプリケーション、フェールオーバー、およびフェールバックを調整します。
- 少なくとも読み取り専用のアカウントが必要です。 ディスクを作成および削除する、VM の電源をオンにするなどの操作を実行できるアカウントが必要です。

Contoso の管理者は、アカウントを以下のように設定します。

1. vCenter レベルでロールを作成します。
2. そのロールに必要なアクセス許可を割り当てます。

### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>フェールオーバー後に Azure VM に接続するための準備をする

Contoso は移行後、Azure VM に接続し、Azure で VM を管理できるようにしたいと考えています。 そのため、Contoso の管理者は、移行前に以下の操作を行います。

1. インターネットでアクセスする場合:

    - 移行前に、オンプレミス VM で RDP または SSH を有効にします。
    - **パブリック** プロファイルに TCP 規則と UDP 規則が追加されていることを確認します。
    - オペレーティング システムのファイアウォールで RDP または SSH が許可されていることを確認します。

2. サイト間 VPN でアクセスする場合:

    - 移行前に、オンプレミス VM で RDP または SSH を有効にします。
    - オペレーティング システムのファイアウォールで RDP または SSH が許可されていることを確認します。
    - Windows の場合、オンプレミス VM 上のオペレーティング システムの SAN ポリシーを **OnlineAll** に設定します。

3. Azure エージェントをインストールします。

    - [Azure Linux エージェント](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux)
    - [Azure Windows エージェント](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows)

4. その他

   - Windows の場合、移行をトリガーするときに、VM 上に保留中の Windows 更新プログラムがあってはいけません。 ある場合は、更新が完了するまで、VM にログインすることはできません。
   - 移行後、**ブート診断**を調べて、VM のスクリーンショットを確認できます。 これが機能しない場合は、VM が実行中であることを確認し、こちらの[トラブルシューティングのヒント](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)を参照してください。

5. 他に支援が必要でしょうか。

   - 移行用の VM の準備について[ご確認ください](https://docs.microsoft.com/azure/migrate/contoso-migration-rehost-vm#prepare-vms-for-migration)。

## <a name="step-6-replicate-the-on-premises-vms-to-azure"></a>手順 6:オンプレミスの VM を Azure にレプリケートする

Contoso の管理者は、Azure への移行を実行する前に、レプリケーションを設定して有効にする必要があります。

検出が完了したら、Azure への VMware VM のレプリケーションを開始できます。

1. Azure Migrate プロジェクトの **[サーバー]** の **[Azure Migrate: Server Migration]** で、 **[レプリケート]** をクリックします。

    ![VM をレプリケートする](./media/contoso-migration-rehost-vm/select-replicate.png)

2. **[レプリケート]** > **[ソースの設定]**  >  **[マシンは仮想化されていますか?]** で、 **[はい (VMware vSphere の場合)]** を選択します。

3. **[オンプレミスのアプライアンス]** で、自分が設定した Azure Migrate アプライアンスの名前、 **[OK]** の順に選択します。

    ![ソースの設定](./media/contoso-migration-rehost-vm/source-settings.png)

4. **[仮想マシン]** で、レプリケートしたいマシンを選択します。
    - VM の評価を実行した場合は、評価結果から VM のサイズ設定とディスクの種類 (Premium または Standard) の推奨事項を適用できます。 これを行うには、 **[Azure Migrate Assessment から移行設定をインポートしますか?]** で **[はい]** オプションを選択します。
    - 評価を実行しなかった場合、または評価の設定を使用しない場合は、 **[いいえ]** オプションを選択します。
    - 評価の使用を選択した場合は、VM グループと評価名を選択します。

    ![評価の選択](./media/contoso-migration-rehost-vm/select-assessment.png)

5. **[仮想マシン]** で、必要に応じて VM を検索し、移行したい各 VM を確認します。 その後、 **[次へ:ターゲット設定]** をクリックします。

6. **[ターゲット設定]** で、サブスクリプションと、移行先となるターゲット リージョンを選択し、移行後に Azure VM が配置されるリソース グループを指定します。 **[仮想ネットワーク]** で、移行後に Azure VM の参加先となる Azure VNet およびサブネットを選択します。

7. **[Azure ハイブリッド特典]** で、次のように選択します。

    - Azure ハイブリッド特典を適用しない場合は、 **[いいえ]** を選択します。 続けて、 **[次へ]** をクリックします。
    - アクティブなソフトウェア アシュアランスまたは Windows Server のサブスクリプションの対象となっている Windows Server マシンがあり、移行中のマシンにその特典を適用する場合は、 **[はい]** を選択します。 続けて、 **[次へ]** をクリックします。

8. **[コンピューティング]** で、VM の名前、サイズ、OS ディスクの種類、可用性セットを確認します。 VM は [Azure の要件](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#vmware-requirements)に準拠している必要があります。

    - **VM サイズ:** 評価の推奨事項を使用している場合は、[VM サイズ] ドロップダウンに推奨サイズが表示されます。 それ以外の場合は、Azure Migrate によって、Azure サブスクリプション内の最も近いサイズが選択されます。 または、 **[Azure VM サイズ]** でサイズを手動で選択します。
    - **OS ディスク:** VM の OS (ブート) ディスクを指定します。 OS ディスクは、オペレーティング システムのブートローダーとインストーラーがあるディスクです。
    - **可用性セット:** 移行後に VM を Azure 可用性セットに配置する必要がある場合は、セットを指定します。 このセットは、移行用に指定するターゲット リソース グループ内に存在する必要があります。

9. **[ディスク]** で、VM ディスクを Azure にレプリケートするかどうかを指定し、Azure でのディスクの種類 (Standard SSD か HDD、または Premium マネージド ディスク) を選択します。 続けて、 **[次へ]** をクリックします。
    - レプリケーションからディスクを除外できます。
    - ディスクは除外すると、移行後に Azure VM 上に存在しなくなります。

10. **[レプリケーションの確認と開始]** で、設定を確認し、 **[レプリケート]** をクリックして、サーバーの初期レプリケーションを開始します。

> [!NOTE]
> レプリケーションを開始する前であれば、 **[管理]**  >  **[マシンのレプリケート]** でレプリケーションの設定をいつでも更新できます。 レプリケーションの開始後は、設定を変更することができません。

## <a name="step-7-migrate-the-database-with-azure-database-migration-service-dms"></a>手順 7:Azure Database Migration Service (DMS) でデータベースを移行する

Contoso の管理者は、[ステップバイステップの移行チュートリアル](https://docs.microsoft.com/azure/dms/tutorial-sql-server-azure-sql-online)を使い、Azure Database Migration Service (DMS) を使用して移行します。 オンライン、オフライン、およびハイブリッド (プレビュー) の移行を実行できます。

まとめると、次を実行する必要があります。

- VNet に接続されている `Premium` SKU を持つ Azure Database Migration Service (DMS) を作成します。
- Azure Database Migration Service (DMS) が仮想ネットワーク経由でリモート SQL Server にアクセスできることを確認します。 これには、仮想ネットワーク レベル、ネットワーク VPN、および SQL Server をホストするマシンで、 Azure から SQL サーバーにすべての受信ポートが許可されていることの確認が伴います。
- Azure Database Migration Service を構成する:
  - 移行プロジェクトを作成します。
  - ソース (オンプレミス データベース) を追加します。
  - ターゲットを選択します。
  - 移行するデータベースを選択します。
  - 詳細設定を構成します。
  - レプリケーション を開始します。
  - すべてのエラーを解決します。
  - 最終的なカットオーバーを実行します。

## <a name="step-8-protect-the-database-with-always-on"></a>手順 8:Always On を使用してデータベースを保護する

アプリのデータベースが **SQLAOG1** 上で実行されており、Contoso の管理者は Always On 可用性グループを使用してこれを保護できます。 SQL Management Studio を使用して Always On を構成し、Windows クラスタリングを使用してリスナーを割り当てます。

### <a name="create-an-always-on-availability-group"></a>Always On 可用性グループを作成する

1. SQL Management Studio で **[Always On 高可用性]** を右クリックして、**新しい可用性グループ ウィザード**を開始します。
2. **[オプションの指定]** で、可用性グループ **SHAOG** を指定します。 **[データベースの選択]** で、SmartHotel360 データベースを選択します。

    ![Always On 可用性グループ](./media/contoso-migration-rehost-vm-sql-ag/aog-1.png)

3. **[レプリカの指定]** で、2 つの SQL ノードを可用性レプリカとして追加し、同期コミットを備えた自動フェールオーバーを提供するように構成します。

     ![Always On 可用性グループ](./media/contoso-migration-rehost-vm-sql-ag/aog-2.png)

4. グループ (**SHAOG**) のリスナーとポートを構成します。 内部ロード バランサーの IP アドレスは、静的 IP アドレス (10.245.40.100) として追加されます。

    ![Always On 可用性グループ](./media/contoso-migration-rehost-vm-sql-ag/aog-3.png)

5. **[データ同期の選択]** で、自動シード処理を有効にします。 このオプションでは、グループ内のすべてのデータベースのセカンダリ レプリカが SQL Server によって自動的に作成されるため、Contoso がバックアップと復元を手動で行う必要はありません。 検証後、可用性グループが作成されます。

    ![Always On 可用性グループ](./media/contoso-migration-rehost-vm-sql-ag/aog-4.png)

6. Contoso がグループを作成するときに問題が発生しました。 Active Directory Windows 統合セキュリティを使用していないため、SQL ログインにアクセス許可を付与して、Windows フェールオーバー クラスター ロールを作成する必要があります。

    ![Always On 可用性グループ](./media/contoso-migration-rehost-vm-sql-ag/aog-5.png)

7. グループが作成されると、Contoso は SQL Management Studio で確認できます。

### <a name="configure-a-listener-on-the-cluster"></a>クラスターでリスナーを構成する

SQL デプロイを設定する最後のステップとして、Contoso の管理者は内部ロード バランサーをクラスター上のリスナーとして構成し、リスナーをオンラインにします。 これにはスクリプトを使用します。

![クラスターのリスナー](./media/contoso-migration-rehost-vm-sql-ag/cluster-listener.png)

### <a name="verify-the-configuration"></a>構成を確認する

このとき、すべての設定が終了し、機能する可用性グループが Azure に存在し、移行したデータベースを使用しています。 これを確認するには、管理者が SQL Management Studio で内部ロード バランサーに接続します。

![ILB 接続](./media/contoso-migration-rehost-vm-sql-ag/ilb-connect.png)

**さらにサポートが必要な場合**

- [可用性グループ](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-tutorial#create-the-availability-group)と[リスナー](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-tutorial#configure-listener)を作成する方法を参照します。
- 手動で[クラスターがロード バランサーの IP アドレスを使用するように設定](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener#configure-the-cluster-to-use-the-load-balancer-ip-address)します。
- SAS の作成および使用については、[こちら](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2)をご覧ください。

## <a name="step-9-migrate-the-vm-with-azure-migrate"></a>手順 9:Azure Migrate で VM を移行する

Contoso の管理者は、簡単なテスト フェールオーバーを実行してから VM を移行します。

### <a name="run-a-test-failover"></a>テスト フェールオーバーの実行

テスト フェールオーバーを実行すると、移行の前に、すべてが正常に機能していることを確認する助けになります。

1. テスト フェールオーバーを実行し、選択可能な最新の時点 (**最後に処理された時点**) を復帰させます。
2. **[Shut down machine before beginning failover]\(フェールオーバー前にマシンをシャットダウンする\)** を選択します。これにより、Azure Migrate は、フェールオーバーをトリガーする前にソース VM のシャットダウンを試みます。 仮にシャットダウンが失敗したとしても、フェールオーバーは続行されます。
3. テスト フェールオーバーでは、以下が実行されます。

    - 移行を実行するために必要なすべての条件が満たされていることを確認する前提条件チェックが実行されます。
    - Azure VM を作成できるように、フェールオーバーによってデータが処理されます。 最新の復旧ポイントを選択した場合は、データから復旧ポイントが作成されます。
    - 前の手順で処理されたデータを使用して、Azure VM が作成されます。

4. フェールオーバーが完了すると、Azure Portal にはレプリカの Azure VM が表示されます。 VM が適切なサイズであること、適切なネットワークに接続されていること、実行中であることを確認します。
5. 確認したら、フェールオーバーをクリーンアップして、確認された事柄をすべて記録して保存します。

### <a name="run-a-failover"></a>フェールオーバーの実行

1. テスト フェールオーバーが正常に機能することを確認したら、移行のための復旧計画を作成し、WEBVM を計画に追加します。

     ![復旧計画](./media/contoso-migration-rehost-vm-sql-ag/recovery-plan.png)

2. 計画に基づいてフェールオーバーを実行します。 最新の復旧ポイントを選択し、Azure Migrate では、フェールオーバーをトリガーする前にオンプレミス VM のシャットダウンを試みる必要があることを指定します。

    ![[フェールオーバー]](./media/contoso-migration-rehost-vm-sql-ag/failover1.png)

3. フェールオーバーが完了したら、Azure VM が想定どおりに Azure Portal に表示されることを確認します。

    ![復旧計画](./media/contoso-migration-rehost-vm-sql-ag/failover2.png)

4. Azure の VM を検査したら、移行を完了して移行プロセスを終了し、VM のレプリケーションを停止して、VM の Azure Migrate の課金を停止します。

    ![[フェールオーバー]](./media/contoso-migration-rehost-vm-sql-ag/failover3.png)

### <a name="update-the-connection-string"></a>接続文字列を更新する

Contoso の管理者は、移行プロセスの最終ステップとして、アプリケーションの接続文字列を更新して、SHAOG リスナーで実行されている移行されたデータベースを指すようにします。 この構成は、現在 Azure で実行されている WEBVM 上で変更します。 この構成は ASP アプリケーションの web.config にあります。

1. `C:\inetpub\SmartHotelWeb\web.config` でファイルを見つけます。 サーバーの名前が、AOG の FQDN (shaog.contoso.com) を反映するように変更します。

    ![[フェールオーバー]](./media/contoso-migration-rehost-vm-sql-ag/failover4.png)

2. ファイルを更新して保存した後、WEBVM 上で IIS を再起動します。 これを行うには、コマンド プロンプトから `iisreset /restart` を使用します。
3. IIS が再起動すると、アプリケーションは SQL MI 上で実行されているデータベースを使用しています。

**さらにサポートが必要な場合**

- テスト フェールオーバーの実行に関する[説明を参照します](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure)。
- 復旧計画の作成方法に関する[説明を参照します](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans)。
- Azure へのフェールオーバーに関する[説明を参照します](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover)。

### <a name="clean-up-after-migration"></a>移行後にクリーンアップする

移行後、SmartHotel360 アプリは Azure VM 上で実行されており、SmartHotel360 データベースは Azure SQL クラスター上にあります。

次に、Contoso は、以下のクリーンアップ手順を完了する必要があります。

- vCenter のインベントリからオンプレミスの VM を削除します。
- ローカルのバックアップ ジョブからから VM を削除します。
- VM の新しい場所と IP アドレスを示すように社内ドキュメントを更新します。
- 使用停止されている VM と対話しているリソースがないか確認します。また、関連する設定やドキュメントがあれば、更新して新しい構成を反映します。
- 2 つの新しい VM (SQLAOG1 および SQLAOG2) を運用監視システムに追加します。

### <a name="review-the-deployment"></a>デプロイを再調査する

リソースを Azure に移行したら、新しいインフラストラクチャを完全に操作可能にして、セキュリティ保護する必要があります。

### <a name="security"></a>Security

Contoso のセキュリティ チームは、Azure VM (WEBVM、SQLAOG1 および SQLAOG2) を再調査して、セキュリティの問題を特定します。

- アクセスを制御するために、VM のネットワーク セキュリティ グループ (NSG) を見直します。 NSG は、アプリケーションに対して許可されるトラフィックのみが通過できるようにするために使用されます。
- チームは、ディスク上のデータ保護のために、Azure Disk Encryption と Key Vault の使用も検討します。
- チームは、Transparent Data Encryption (TDE) を評価してから、新しい SQL AOG で実行されている SmartHotel360 データベース上で有効にします。 [詳細については、こちらを参照してください](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017)。

詳細については、「[Azure における IaaS ワークロードのセキュリティに関するベスト プラクティス](https://docs.microsoft.com/azure/security/fundamentals/iaas)」を参照してください。

## <a name="business-continuity-and-disaster-recovery"></a>事業継続とディザスター リカバリー

事業継続とディザスター リカバリー (BCDR) のために、Contoso は次のアクションを実施します。

- データの安全性を確保するために、Contoso は、Azure Backup サービスを使用して、WEBVM、SQLAOG1、SQLAOG2 の各 VM 上のデータをバックアップします。 [詳細については、こちらを参照してください](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=/azure/virtual-machines/linux/toc.json)。
- また、Contoso は、Azure Storage を使用して SQL Server を直接 Blob Storage にバックアップする方法を把握する必要があります。 [詳細については、こちらを参照してください](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-use-storage-sql-server-backup-restore)。
- アプリの稼働状態を維持するために、Contoso は、Site Recovery を使用して、Azure 内のアプリの VM をセカンダリ リージョンにレプリケートします。 [詳細については、こちらを参照してください](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-quickstart)。

### <a name="licensing-and-cost-optimization"></a>ライセンスとコストの最適化

- Contoso は、WEBVM の既存のライセンスを所有しており、Azure ハイブリッド特典を活用します。 Contoso は、この特典を活用するために、既存の Azure VM を変換します。
- Contoso は [Azure Cost Management](https://azure.microsoft.com/services/cost-management) を使用して、IT リーダーが定めた予算内に確実に収まるようにします。

## <a name="conclusion"></a>まとめ

この記事では、Contoso が Azure Migrate サービスを使用して、アプリ フロントエンド VM を Azure に移行し、Azure 内で SmartHotel360 アプリを再ホストしました。 Azure Database Migration Service を使用して Azure 内にプロビジョニングされた SQL Server クラスターにアプリのデータベースを移行して、SQL Server Always On 可用性グループ内で保護しました。
