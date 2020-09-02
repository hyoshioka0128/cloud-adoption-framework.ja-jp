---
title: アプリケーションを Azure VM と SQL Server Always On 可用性グループに移行してリホストする
description: Contoso がオンプレミス アプリを Azure VM および SQL Server Always On 可用性グループに移行して再ホストする方法について説明します。
author: deltadan
ms.author: abuck
ms.date: 07/01/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: migrate
services: azure-migrate
ms.openlocfilehash: e1ce476ead23470b9300594d9195b310f4aa8b01
ms.sourcegitcommit: 07d56209d56ee199dd148dbac59671cbb57880c0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88878012"
---
<!-- cSpell:ignore WEBVM SQLVM contosohost vcenter contosodc AOAG SQLAOG SQLAOGAVSET contosoadmin contosocloudwitness MSSQLSERVER BEPOOL contosovmsacc SHAOG NSGs inetpub iisreset -->

# <a name="rehost-an-on-premises-application-with-azure-vms-and-sql-server-always-on-availability-groups"></a>Azure VM および SQL Server Always On 可用性グループでオンプレミス アプリケーションをリホストする

この記事では、Contoso という架空の会社が、Azure への移行の一環として、VMware 仮想マシン (VM) 上で実行される 2 階層の Windows .NET アプリケーションをリホストする方法を説明します。 Contoso は、アプリケーションのフロントエンド VM を Azure VM に、アプリケーションのデータベースを Azure SQL Server VM に移行し、SQL Server Always On 可能性グループを使用して Windows Server フェールオーバー クラスター内で実行するようにします。

この例で使用される SmartHotel360 アプリケーションは、オープン ソースとして提供されています。 独自のテスト目的に使用する場合は、[GitHub](https://github.com/Microsoft/SmartHotel360) からダウンロードしてください。

## <a name="business-drivers"></a>ビジネス ドライバー

IT リーダーシップ チームは、ビジネス パートナーと密接に連絡を取り合い、彼らがこの移行で何を達成しようとしているのかを理解しました。 目的は次のとおりです。

- **ビジネスの成長への対応。** Contoso は成長を続けています。そのため、オンプレミスのシステムとインフラストラクチャに負荷がかかっています。
- **効率化。** Contoso では、不要な手順を取り除き、開発者とユーザーのプロセスを効率化する必要があります。 ビジネス部門は、顧客の要求により迅速に対応するために、IT 部門に対して、時間やコストを無駄にせず、迅速に作業を行うことを求めています。
- **機敏性の向上。** Contoso IT は、ビジネス部門の要求に対して、対応力を向上させる必要があります。 また、グローバル経済で成功を収めるために、市場の変化よりも迅速に対応する必要があります。 ビジネスの妨げや障害にならないようにする必要があります。
- **スケール。** ビジネスが順調に成長している中で、Contoso IT 部門は、同じペースで拡張するシステムを提供する必要があります。

## <a name="migration-goals"></a>移行の目標

Contoso クラウド チームは、この移行の目標を設定しました。 これらの目標を使用して、最良の移行方法を決定しました。

- 移行後、Azure 内のアプリケーションは、現在の VMware における場合と同じパフォーマンスを発揮できる必要があります。 このアプリケーションは、オンプレミスにあるときと同様に、クラウドでも引き続き非常に重要です。
- Contoso は、このアプリケーションに投資することを望んでいません。 これはビジネスにとって重要なものですが、現状のまま確実にクラウドに移すことだけを望んでいます。
- このアプリケーション用のオンプレミス データベースでは、可用性の問題が発生していました。 Contoso の希望は、フェールオーバー機能を備えた高可用性クラスターとしてこのデータベースを Azure にデプロイすることです。
- Contoso は、現在の SQL Server 2008 R2 プラットフォームを SQL Server 2017 にアップグレードしたいと考えています。
- Contoso は、このアプリケーションに Azure SQL Database を使用したくないと考えており、別の方法を探しています。

## <a name="solution-design"></a>ソリューション設計

Contoso は、会社の目標と要件を明確にした後、デプロイ ソリューションを設計してレビューし、移行プロセスを確認します。 また、移行に使用する Azure サービスも確認します。

### <a name="current-architecture"></a>現在のアーキテクチャ

- アプリケーションは 2 つの VM (`WEBVM` と `SQLVM`) に階層化されています。
- これらの VM は、VMware ESXi ホスト `contosohost1.contoso.com` (バージョン 6.5) 上に配置されています。
- VMware 環境は、VM 上で実行されている vCenter Server 6.5 (`vcenter.contoso.com`) によって管理されています。
- Contoso には、オンプレミスのデータセンター (`contoso-datacenter`) があり、これにはオンプレミスのドメイン コントローラー (`contosodc1`) が含まれています。

### <a name="proposed-architecture"></a>提案されたアーキテクチャ

このシナリオでは:

- Contoso は、アプリケーション フロントエンド `WEBVM` を、Azure IaaS (サービスとしてのインフラストラクチャ) VM に移行します。
  - このフロントエンド VM は、Azure の `ContosoRG` リソース グループ (運用リソースで使用される) にデプロイされます。
  - その配置先は、プライマリ リージョン (`East US 2`) にある Azure 運用ネットワーク (`VNET-PROD-EUS2`) です。
- アプリケーション データベースは、Azure SQL Server VM に移行されます。
  - 配置先は、プライマリ リージョン (`East US 2`) にある Contoso の Azure データベース ネットワーク (`PROD-DB-EUS2`) です。
  - SQL Server Always On 可用性グループを使用する、2 ノードの Windows Server フェールオーバー クラスターに配置されます。
  - Azure の `ContosoRG` リソース グループには、このクラスター内の 2 つの SQL Server VM ノードがデプロイされます。
  - これらの VM ノードの配置先は、プライマリ リージョン (`East US 2`) にある Azure 運用ネットワーク (`VNET-PROD-EUS2`) です。
  - VM では、Windows Server 2016 と SQL Server 2017 Enterprise エディションが実行されます。 Contoso は、このオペレーティング システムのライセンスを所有していません。 会社の Azure Enterprise Agreement コミットメントに対して請求されるライセンスを提供する、Azure Marketplace のイメージを使用します。
  - 両方の VM は、一意の名前を除いて同じ設定を使用します。
- Contoso は、クラスターのトラフィックをリッスンし、それを適切なクラスター ノードに転送する内部ロード バランサーをデプロイします。
  - 内部ロード バランサーは、`ContosoNetworkingRG` (ネットワーク リソースに使用) にデプロイされます。
- Contoso データセンター内のオンプレミス VM は、移行が行われた後に使用停止にされます。

    ![シナリオのアーキテクチャの図を示すスクリーンショット。](./media/contoso-migration-rehost-vm-sql-ag/architecture.png)

### <a name="database-considerations"></a>データベースの考慮事項

Contoso はソリューション設計プロセスの一環として、Azure SQL Database と SQL Server の機能を比較しました。 次の点を考慮した結果、SQL Server を実行する Azure IaaS VM を使用することにしました。

- Contoso がオペレーティング システムまたはデータベース サーバーをカスタマイズしなければならなくなった場合や、同じ VM にサードパーティのアプリケーションを併置して実行することが必要になった場合、SQL Server を実行する Azure VM を使用することが最適な解決策であると考えられる。

### <a name="solution-review"></a>ソリューションのレビュー

Contoso は、長所と短所の一覧をまとめて、提案された設計を評価します。

| 考慮事項 | 詳細 |
| --- | --- |
| **長所** | `WEBVM` は変更なしで Azure に移されるので、移行が簡単になります。 <br><br> SQL Server 層は、SQL Server 2017 と Windows Server 2016 で実行されます。 これにより、現在の Windows Server 2008 R2 オペレーティング システムは廃止されます。 SQL Server 2017 を実行することで、Contoso の技術的な要件と目標がサポートされます。 SQL Server 2008 R2 から移行しても、完全な互換性が提供されます。 <br><br> Contoso は、Azure ハイブリッド特典を使用することで、ソフトウェア アシュアランスへの投資を活用できます。 <br><br> Azure への高可用性 SQL Server のデプロイによってフォールト トレランスが得られるので、アプリケーションのデータ層が単一フェールオーバー ポイントではなくなります。 |
| **短所** | `WEBVM` では、Windows Server 2008 R2 が実行されています。 このオペレーティング システムは、特定のロールを対象に Azure でサポートされます (2018 年 7 月)。 詳細については、「[Microsoft Azure 仮想マシンのマイクロソフト サーバー ソフトウェアのサポート](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)」をご覧ください。 <br><br> アプリケーションの Web 層は、依然として単一フェールオーバー ポイントになります。 <br><br> Contoso は、Azure App Service などのマネージド サービスに移行するのではなく、Azure VM として Web 層を引き続きサポートする必要があります。 <br><br> このソリューションを選択した場合、Contoso は、Azure SQL Managed Instance などのマネージド プラットフォームに移行するのではなく、引き続き 2 つの SQL Server VM を管理する必要があります。 また、ソフトウェア アシュアランスにより、Contoso は既存のライセンスと引き換えに、Azure SQL Managed Instance を割引料金で利用できます。 |

### <a name="azure-services"></a>Azure サービス

| サービス | 説明 | コスト |
| --- | --- | --- |
| [Azure Database Migration Service](/azure/dms/dms-overview) | Azure Database Migration Service を使用すると、複数のデータベース ソースから Azure データ プラットフォームに、ダウンタイムを最小限に抑えながらシームレスに移行できます。 | [サポートされているリージョン](/azure/dms/dms-overview#regional-availability)と [Azure Database Migration Service の価格](https://azure.microsoft.com/pricing/details/database-migration)に関する情報をご覧ください。 |
| [Azure Migrate](/azure/migrate/migrate-overview) | Contoso は、Azure Migrate を使用して VMware VM を評価します。 Azure Migrate は、マシンの移行適合性を評価します。 そのうえで、Azure で実行するための、サイズとコストの見積もりを提供します。 | Azure Migrate は、追加料金なしで利用できます。 評価と移行に使用することにしたツール (ファーストパーティまたは独立系ソフトウェア ベンダー) によっては料金が発生する場合があります。 [Azure Migrate の価格](https://azure.microsoft.com/pricing/details/azure-migrate)について、詳しくはこちらを参照してください。 |

## <a name="migration-process"></a>移行プロセス

Contoso の管理者は、アプリケーション VM を Azure に移行します。

- Azure Migrate を使用して、フロントエンド VM を Azure VM に移行します。
  - 最初の手順として、Azure コンポーネントを準備してセットアップし、オンプレミスの VMware インフラストラクチャを準備します。
  - すべての準備ができたら、VM のレプリケートを開始できます。
  - レプリケーションが有効になり、機能するようになったら、Azure Migrate を使用して VM を移行します。
- データベースの検証が完了したら、Azure Database Migration Service を使用して、Azure の SQL Server クラスターにデータベースを移行します。
  - 最初の手順として、Azure の SQL Server VM をプロビジョニングし、クラスターと内部ロード バランサーをセットアップして、Always On 可用性グループを構成する必要があります。
  - このように配置された状態で、データベースを移行することができます。
- 移行後は、データベースの Always On 可用性グループを有効にします。

    ![移行プロセスの図を示すスクリーンショット。](./media/contoso-migration-rehost-vm-sql-ag/migration-process.png)

## <a name="prerequisites"></a>前提条件

このシナリオで Contoso が行うべきことを以下に示します。

| 必要条件 | 詳細 |
| --- | --- |
| **Azure サブスクリプション** | このシリーズの先行する記事の中で、Contoso はサブスクリプションを既に作成しています。 Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/free)を作成してください。 <br><br> 無料アカウントを作成する場合、サブスクリプションの管理者としてすべてのアクションを実行できます。 <br><br> 既存のサブスクリプションを使用する場合に、自分が管理者でないようであれば、管理者と連携して所有者または共同作成者のアクセス許可を自分に割り当てます。 <br><br> |
| **Azure インフラストラクチャ** | Contoso は、[移行のための Azure インフラストラクチャ](./contoso-migration-infrastructure.md)についての記事で説明されているように、Azure インフラストラクチャを設定します。 <br><br> Azure Migrate: Server Migration の具体的な[前提条件](./contoso-migration-devtest-to-iaas.md#prerequisites)の要件の詳細について確認してください。Server Migration に関するエラーのトラブルシューティングに役立つ情報を提供しています。 |
| **オンプレミスのサーバー** | オンプレミスの vCenter Server では、バージョン 5.5、6.0、6.5、または 6.7 が実行されている必要があります。 <br><br> バージョン 5.5、6.0、6.5、または 6.7 が実行されている ESXi ホスト。 <br><br> ESXi ホスト上で実行している 1 つ以上の VMware VM。 |
| **オンプレミスの VM** | Azure での実行が保証されている [Linux マシンを確認します](/azure/virtual-machines/linux/endorsed-distros)。 |

## <a name="scenario-steps"></a>シナリオのステップ

Contoso が移行を実行する方法を次に示します。

> [!div class="checklist"]
>
> - **ステップ 1:SQL Server Always On 可用性グループ クラスターを準備する。** 2 つの SQL Server VM ノードを Azure にデプロイするためのクラスターを作成します。
> - **手順 2:クラスターをデプロイして設定する。** Azure SQL Server クラスターを準備します。 データベースは、この既存のクラスターに移行されます。
> - **ステップ 3:Azure Load Balancer をデプロイする。** 複数の SQL Server ノードにトラフィックの負荷を分散するロード バランサーをデプロイします。
> - **手順 4:Azure Migrate 用に Azure を準備する。** レプリケートされたデータを保持する Azure Storage アカウントを作成します。
> - **手順 5:Azure Migrate 用のオンプレミス VMware を準備する。** VM を検出し、エージェントをインストールするためのアカウントを準備します。 移行後にユーザーが Azure VM に接続できるように、オンプレミスの VM を準備します。
> - **手順 6:オンプレミスの VM を Azure にレプリケートする。** Azure への VM レプリケーションを有効にします。
> - **手順 7: Azure Database Migration Service を介してデータベースを移行する。** Azure Database Migration Service を使用してデータベースを Azure に移行します。
> - **手順 8: SQL Server Always On を使用してデータベースを保護する。** クラスターの Always On 可用性グループを作成します。
> - **手順 9:Azure Migrate を使用して VM を移行する。** すべてが想定どおりに動作していることを確認するためにテスト移行を実行します。 次に、Azure への移行を実行します。

## <a name="step-1-prepare-a-sql-server-always-on-availability-group-cluster"></a>手順 1:SQL Server Always On 可用性グループ クラスターを準備する

クラスターを設定するには、Contoso の管理者が次の手順を実行します。

1. Azure Marketplace で SQL Server 2017 Enterprise Windows Server 2016 イメージを選択して、2 つの SQL Server VM を作成します。

    ![SQL VM SKU を示すスクリーンショット。](./media/contoso-migration-rehost-vm-sql-ag/sql-vm-sku.png)

1. **仮想マシンの作成ウィザード** >  **[基本]** で、次のように構成します。

    - VM の名前: `SQLAOG1` と `SQLAOG2`。
    - マシンは Business Critical であるため、VM ディスクの種類で SSD を有効にします。
    - マシンの資格情報を指定します。
    - プライマリ リージョン (`East US 2`) の `ContosoRG` リソース グループに VM をデプロイします。

1. **[サイズ]** については、両方の VM で `D2S v3` インスタンスから開始します。 必要に応じて後でスケーリングします。
1. **[設定]** で、次の操作を実行します。

    - これらの VM はアプリケーションの重要なデータベースであるため、マネージド ディスクを使用します。
    - プライマリ リージョン (`East US 2`) にある運用ネットワーク (`VNET-PROD-EUS2`) のデータベース サブネット (`PROD-DB-EUS2`) にそれらのマシンを配置します。
    - 2 つの障害ドメインと 5 つの更新ドメインを含む新しい可用性セット (`SQLAOGAVSET`) を作成します。

      ![新しい可用性セットを示すスクリーンショット。](./media/contoso-migration-rehost-vm-sql-ag/sql-vm-settings.png)

1. **[SQL Server の設定]** で、SQL 接続を既定のポート 1433 の仮想ネットワーク (プライベート) に制限します。 認証については、オンサイトで使用するものと同じ資格情報 (`contosoadmin`) を使用します。

    ![[SQL Server の設定] を示すスクリーンショット。](./media/contoso-migration-rehost-vm-sql-ag/sql-vm-db.png)

**さらにサポートが必要な場合**

- [SQL Server VM をプロビジョニングする](/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision#1-configure-basic-settings)方法に関するヘルプを参照してください。
- [さまざまな SQL Server SKU の VM を構成する](/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-prereq#create-sql-server-vms)方法をご覧ください。

## <a name="step-2-deploy-and-set-up-the-cluster"></a>手順 2:クラスターをデプロイして設定する

クラスターを設定するには、Contoso の管理者が次の手順を実行します。

1. クラウド監視として機能するように Azure Storage アカウントを設定します。
1. SQL Server VM を、Contoso オンプレミス データセンターの Active Directory ドメインに追加します。
1. Azure でクラスターを作成します。
1. クラウド監視を構成します。
1. SQL Always On 可用性グループを有効にします。

### <a name="set-up-a-storage-account-as-a-cloud-witness"></a>ストレージ アカウントをクラウド監視として設定する

Contoso は、クラウド監視を設定するために、クラスターの調停に使用される BLOB ファイルを保持する Azure Storage アカウントが必要です。 複数のクラスターのクラウド監視の設定に同じストレージ アカウントを使用できます。

ストレージ アカウントを作成するには、Contoso の管理者が次の手順を実行します。

1. アカウントのわかりやすい名前 (`contosocloudwitness`) を指定します。
1. LRS の汎用アカウントをデプロイします。
1. アカウントをサード リージョン (`South Central US`) に配置します。 リージョンで障害が発生した際にも使用できるように、プライマリ リージョンやセカンダリ リージョン以外に配置します。
1. インフラストラクチャ リソースを保持するリソース グループ (`ContosoInfraRG`) に配置します。

    ![クラウド監視アカウント名を示すスクリーンショット。](./media/contoso-migration-rehost-vm-sql-ag/witness-storage.png)

1. ストレージ アカウントを作成すると、対応するプライマリおよびセカンダリ アクセス キーが生成されます。 クラウド監視を作成するにはプライマリ アクセス キーが必要です。 このキーはストレージ アカウント名の下の **[アクセス キー]** に表示されます。

    ![アクセス キーを示すスクリーンショット。](./media/contoso-migration-rehost-vm-sql-ag/access-key.png)

<!-- docsTest:casing "Failover Cluster feature" -->

### <a name="add-sql-server-vms-to-contoso-domain"></a>Contoso ドメインに SQL Server VM を追加する

1. Contoso は、`SQLAOG1` と `SQLAOG2` を `contoso.com` ドメインに追加します。
1. 各 VM に Windows フェールオーバー クラスター機能およびツールをインストールします。

### <a name="set-up-the-cluster"></a>クラスターをセットアップする

Contoso の管理者は、クラスターを設定する前に、各マシン上の OS ディスクのスナップショットを取得します。

![[スナップショットの作成] ペインを示すスクリーンショット。](./media/contoso-migration-rehost-vm-sql-ag/snapshot.png)

1. Windows フェールオーバー クラスターを作成するスクリプトを実行します。

    ![Windows フェールオーバー クラスターを作成するスクリプトを示すスクリーンショット。](./media/contoso-migration-rehost-vm-sql-ag/create-cluster1.png)

1. クラスターを作成したら、VM がクラスター ノードとして表示されることを確認します。

     ![クラスターが作成されたことを示すスクリーンショット。](./media/contoso-migration-rehost-vm-sql-ag/create-cluster2.png)

<!--docsTest:casing "Failover Cluster Manager" -->

### <a name="configure-the-cloud-witness"></a>クラウド監視を構成する

1. Contoso の管理者は、フェールオーバー クラスター マネージャーの**クォーラム構成ウィザード**を使用してクラウド監視を構成します。
1. ウィザードで、ストレージ アカウントでクラウド監視を作成することを選択します。
1. クラウド監視が構成されると、フェールオーバー クラスター マネージャー スナップインに表示されます。

    ![クラウド監視が構成されたことを示すスクリーンショット。](./media/contoso-migration-rehost-vm-sql-ag/cloud-witness.png)

### <a name="enable-sql-server-always-on-availability-groups"></a>SQL Server Always On 可用性グループを有効にする

これで、Contoso の管理者は Always On 可用性グループを有効にすることができます。

1. SQL Server 構成マネージャーで、**SQL Server (MSSQLSERVER)** サービスについて **Always On 可用性グループ**を有効にします。

    ![[Always On 可用性グループを有効にする] チェック ボックスを示すスクリーンショット。](./media/contoso-migration-rehost-vm-sql-ag/enable-always-on.png)

1. 変更を有効にするためにサービスを再起動します。

Always On 可用性グループが有効になると、Contoso は SmartHotel360 データベースを保護する Always On 可用性グループを設定できます。

**さらにサポートが必要な場合**

- [クラウド監視とそのストレージ アカウントの設定](/windows-server/failover-clustering/deploy-cloud-witness)に関する記事をご覧ください。
- [クラスターの設定と可用性グループの作成](/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-tutorial)の手順を参照してください。

## <a name="step-3-deploy-azure-load-balancer"></a>手順 3:Azure Load Balancer をデプロイする

Contoso の管理者は、クラスター ノードの前に配置される内部ロード バランサーをデプロイしたいと考えています。 ロード バランサーはトラフィックをリッスンし、適切なノードに転送します。

![負荷分散を示す図。](./media/contoso-migration-rehost-vm-sql-ag/architecture-lb.png)

ロード バランサーを作成するには、Contoso の管理者が次の手順を実行します。

1. Azure portal で、 **[ネットワーク]**  >  **[ロード バランサー]** の順に移動し、新しい内部ロード バランサー (`ILB-PROD-DB-EUS2-SQLAOG`) を設定します。
1. 運用ネットワーク (`VNET-PROD-EUS2`) のデータベース サブネット (`PROD-DB-EUS2`) にロード バランサーを配置します。
1. それに静的 IP アドレス (`10.245.40.100`) を割り当てます。
1. ネットワーク要素として、ロード バランサーをネットワーク リソース グループ `ContosoNetworkingRG` にデプロイします。

    ![[ロード バランサーの作成] ペインを示すスクリーンショット。](./media/contoso-migration-rehost-vm-sql-ag/lb-create.png)

内部ロード バランサーがデプロイされたら、Contoso の管理者はそれを設定する必要があります。 バックエンド アドレス プールを作成し、正常性プローブを設定し、負荷分散規則を構成します。

### <a name="add-a-back-end-pool"></a>バックエンド プールを追加する

Contoso の管理者は、クラスターの VM にトラフィックを分散させるために、ロード バランサーからネットワーク トラフィックを受け取る VM の NIC の IP アドレスを含むバックエンド アドレス プールを設定します。

1. ポータルのロード バランサー設定で、Contoso は次のバックエンド プールを追加します: `ILB-PROD-DB-EUS-SQLAOG-BEPOOL`
1. このプールを可用性セット `SQLAOGAVSET` に関連付けます。 セット内の VM (`SQLAOG1` および `SQLAOG2`) がプールに追加されます。

    ![[バックエンド プールの追加] 画面を示すスクリーンショット。](./media/contoso-migration-rehost-vm-sql-ag/backend-pool.png)

### <a name="create-a-health-probe"></a>正常性プローブの作成

Contoso の管理者は正常性プローブを作成して、ロード バランサーがアプリケーションの正常性を監視できるようにします。 プローブは、正常性チェックへの VM の応答に基づき、ロード バランサーのローテーションに対して VM を動的に追加または削除します。

プローブを作成するには、Contoso の管理者が次の手順を実行します。

1. ポータルのロード バランサー設定で、正常性プローブ **SQLAlwaysOnEndPointProbe** を作成します。
1. TCP ポート 59999 で VM を監視するようにプローブを設定します。
1. プローブの間隔を 5 秒、しきい値を 2 に設定します。 プローブが 2 回失敗すると、VM が異常と見なされます。

    ![[正常性プローブの追加] 画面を示すスクリーンショット。](./media/contoso-migration-rehost-vm-sql-ag/nlb-probe.png)

### <a name="configure-the-load-balancer-to-receive-traffic"></a>トラフィックを受信するロード バランサーを構成する

次に、Contoso の管理者は、複数の VM にトラフィックを分散する方法を定義するロード バランサー規則を設定します。

- フロントエンド IP アドレスが受信トラフィックを処理します。
- バックエンド IP プールがトラフィックを受信します。

規則を作成するには、Contoso の管理者が次の手順を実行します。

1. ポータルのロード バランサー設定で、新しい規則として `SQLAlwaysOnEndPointListener` を追加します。
1. TCP ポート 1433 で受信する SQL クライアント トラフィックを受け取るフロントエンド リスナーを設定します。
1. トラフィックのルーティング先のバックエンド プールと、VM がトラフィックをリッスンするポートを指定します。
1. フローティング IP (Direct Server Return) を有効にします。これは、SQL Server Always On では常に必要となります。

    ![正常性プローブの設定を示すスクリーンショット。](./media/contoso-migration-rehost-vm-sql-ag/nlb-probe.png)

**さらにサポートが必要な場合**

- [Azure Load Balancer](/azure/load-balancer/load-balancer-overview) の概要をご覧ください。
- [ロード バランサーを作成する](/azure/load-balancer/tutorial-load-balancer-basic-internal-portal)方法をご覧ください。

## <a name="step-4-prepare-azure-for-azure-migrate"></a>手順 4:Azure Migrate 用に Azure を準備する

Contoso が Azure Migrate をデプロイする必要がある Azure コンポーネントは次のとおりです。

- 移行される VM が配置される仮想ネットワーク。
- レプリケートされたデータを保持する Azure Storage アカウント。

Contoso の管理者は、これらのコンポーネントを次のように設定します。

1. Contoso は、[Azure インフラストラクチャをデプロイ](./contoso-migration-rehost-vm-sql-ag.md)したときに、Azure Migrate に使用できるネットワークとサブネットを既に作成しています。

    - SmartHotel360 アプリケーションは運用アプリケーションであり、`WEBVM` はプライマリ リージョン (`East US 2`) の Azure 運用ネットワーク (`VNET-PROD-EUS2`) に移行されます。
    - `WEBVM` は、運用リソースのために使用される `ContosoRG` リソース グループと、運用サブネット (`PROD-FE-EUS2`) に配置されます。

1. Contoso の管理者は、プライマリ リージョンに Azure Storage アカウント (`contosovmsacc20180528`) を作成します。

    - Standard ストレージと LRS レプリケーションが設定された汎用アカウントを使用します。

## <a name="step-5-prepare-on-premises-vmware-for-azure-migrate"></a>手順 5:Azure Migrate 用のオンプレミス VMware を準備する

Contoso の管理者は、オンプレミスで以下を準備します。

- VM の検出を自動化するための vCenter Server または vSphere ESXi ホストのアカウント。
- 移行後にレプリケートされた Azure VM に Contoso が接続できるようにするためのオンプレミスの VM の設定。

### <a name="prepare-an-account-for-automatic-discovery"></a>自動検出用のアカウントを準備する

Azure Migrate では、次のことを実行するために、VMware サーバーへのアクセスが必要です。

- VM を自動的に検出します。
- レプリケーションと移行を調整します。
- 少なくとも読み取り専用のアカウントが必要です。 ディスクを作成および削除する、VM の電源をオンにするなどの操作を実行できるアカウントが必要です。

アカウントを設定するには、Contoso の管理者が次の手順を実行します。

1. vCenter レベルでロールを作成します。
1. そのロールに必要なアクセス許可を割り当てます。

### <a name="prepare-to-connect-to-azure-vms-after-migration"></a>移行後に Azure VM に接続するための準備をします。

Contoso は移行後、Azure VM に接続し、Azure で VM を管理できるようにしたいと考えています。 そのために、Contoso の管理者は移行前に以下の作業を行います。

1. インターネットでアクセスする場合:

    - 移行前に、オンプレミス VM で RDP または SSH を有効にします。
    - **パブリック** プロファイルに TCP 規則と UDP 規則が追加されていることを確認します。
    - オペレーティング システムのファイアウォールで RDP または SSH が許可されていることを確認します。

1. サイト間 VPN でアクセスする場合:

    - 移行前に、オンプレミス VM で RDP または SSH を有効にします。
    - オペレーティング システムのファイアウォールで RDP または SSH が許可されていることを確認します。
    - Windows の場合、オンプレミス VM 上のオペレーティング システムの SAN ポリシーを **OnlineAll** に設定します。

1. Azure エージェントをインストールします。

    - [Azure Linux エージェント](/azure/virtual-machines/extensions/agent-linux)
    - [Azure Windows エージェント](/azure/virtual-machines/extensions/agent-windows)

1. その他

   - Windows の場合、移行をトリガーするときに、VM 上に保留中の Windows 更新プログラムがあってはいけません。 ある場合、Contoso の管理者は更新が完了するまで VM にサインインすることはできません。
   - 移行後、**ブート診断**を調べて、VM のスクリーンショットを確認できます。 これが機能しない場合は、VM が実行中であることを確認し、こちらの[トラブルシューティングのヒント](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)を参照する必要があります。

**さらにサポートが必要な場合**

[移行に向けて VM を準備する](/azure/migrate/prepare-for-migration)方法をご覧ください。

## <a name="step-6-replicate-the-on-premises-vms-to-azure"></a>手順 6:オンプレミスの VM を Azure にレプリケートする

Contoso の管理者は、Azure への移行を実行する前に、レプリケーションを設定して有効にする必要があります。

検出が完了したら、Azure への VMware VM のレプリケーションを開始できます。

1. Azure Migrate プロジェクトで、 **[サーバー]**  >  **[Azure Migrate: Server Migration]** の順に移動し、 **[レプリケート]** を選択します。

    ![[レプリケート] オプションを示すスクリーンショット。](./media/contoso-migration-rehost-vm/select-replicate.png)

1. **[レプリケート]**  >  **[ソースの設定]**  >  **[マシンは仮想化されていますか?]** で、 **[はい (VMware vSphere の場合)]** を選択します。

1. **[オンプレミスのアプライアンス]** で、設定済みの Azure Migrate アプライアンスの名前を選択し、 **[OK]** を選択します。

    ![[ソースの設定] タブを示すスクリーンショット。](./media/contoso-migration-rehost-vm/source-settings.png)

1. **[仮想マシン]** で、レプリケートするマシンを選択します。
    - Contoso の管理者は、VM の評価を実行した場合、評価結果から VM のサイズ設定とディスクの種類 (Premium または Standard) の推奨事項を適用できます。 **[Azure Migrate Assessment から移行設定をインポートしますか?]** で、 **[はい]** オプションを選択します。
    - 評価を実行しなかった場合、または評価の設定を使用しない場合は、 **[いいえ]** オプションを選択します。
    - 評価の使用を選択した場合は、VM グループと評価名を選択します。

    ![評価の選択を示すスクリーンショット。](./media/contoso-migration-rehost-vm/select-assessment.png)

1. **[仮想マシン]** で、必要に応じて VM を検索し、移行する各 VM を確認します。 次に、 **[次のステップ: ターゲット設定]** をクリックします。

1. **[ターゲット設定]** で、サブスクリプションと、移行先となるターゲット リージョンを選択し、移行後に Azure VM が配置されるリソース グループを指定します。 **[仮想ネットワーク]** で、移行後に Azure VM の参加先となる Azure 仮想ネットワークまたはサブネットを選択します。

1. **[Azure ハイブリッド特典]** で、Contoso の管理者は次の操作を行います。

    - Azure ハイブリッド特典を適用しない場合は、 **[いいえ]** を選択します。 次に、 **[次へ]** を選択します。
    - アクティブなソフトウェア アシュアランスまたは Windows Server サブスクリプションの対象となっている Windows Server マシンがあり、移行するマシンに特典を適用する場合は、 **[はい]** を選択します。 次に、 **[次へ]** を選択します。

1. **[コンピューティング]** で、VM の名前、サイズ、OS ディスクの種類、可用性セットを確認します。 VM は [Azure の要件](/azure/migrate/migrate-support-matrix-vmware#vmware-requirements)に準拠している必要があります。

    - **VM サイズ:** 評価の推奨事項を使用している場合は、[VM サイズ] ドロップダウン リストに推奨サイズが表示されます。 それ以外の場合は、Azure Migrate によって、Azure サブスクリプション内の最も近いサイズが選択されます。 または、 **[Azure VM サイズ]** でサイズを手動で選択することもできます。
    - **OS ディスク:** VM の OS (ブート) ディスクを指定します。 OS ディスクは、オペレーティング システムのブートローダーとインストーラーがあるディスクです。
    - **可用性セット:** 移行後に VM を Azure 可用性セットに配置する必要がある場合は、セットを指定します。 このセットは、移行用に指定されたターゲット リソース グループに含まれている必要があります。

1. **[ディスク]** で、VM ディスクを Azure にレプリケートするかどうかを指定します。 Azure でのディスクの種類 (Standard SSD または HDD、あるいは Premium マネージド ディスク) を選択し、 **[次へ]** を選択します。
    - レプリケーションからディスクを除外できます。
    - ディスクを除外すると、移行後に Azure VM 上に存在しなくなります。

1. **[レプリケーションの確認と開始]** で、設定を確認します。 次に、 **[レプリケート]** を選択して、サーバーの初期レプリケーションを開始します。

> [!NOTE]
> レプリケーションを開始する前であれば、 **[管理]**  >  **[マシンのレプリケート]** でレプリケーションの設定をいつでも更新できます。 レプリケーションの開始後は、設定を変更することができません。

## <a name="step-7-migrate-the-database-via-azure-database-migration-service"></a>手順 7: Azure Database Migration Service を使用してデータベースを移行する

Contoso の管理者は、[ステップバイステップの移行チュートリアル](/azure/dms/tutorial-sql-server-azure-sql-online)に従って、Azure Database Migration Service を使用してデータベースを移行します。 オンライン、オフライン、およびハイブリッド (プレビュー) の移行を実行できます。

まとめると、次の作業を行う必要があります。

- Premium 価格レベルを使用して、仮想ネットワークに接続する Azure Database Migration Service インスタンスを作成します。
- そのインスタンスから仮想ネットワーク経由でリモート SQL Server に確実にアクセスできるようにします。 仮想ネットワーク レベル、ネットワーク VPN、および SQL Server をホストするマシンで、Azure から SQL Server へのすべての受信ポートが許可されていることを確認します。
- インスタンスを構成します。
  - 移行プロジェクトを作成します。
  - ソース (オンプレミス データベース) を追加します。
  - ターゲットを選択します。
  - 移行するデータベースを選択します。
  - 詳細設定を構成します。
  - レプリケーション を開始します。
  - すべてのエラーを解決します。
  - 最終的なカットオーバーを実行します。

## <a name="step-8-protect-the-database-with-sql-server-always-on"></a>手順 8: SQL Server Always On を使用してデータベースを保護する

アプリケーション データベースが `SQLAOG1` 上で実行されており、Contoso の管理者は Always On 可用性グループを使用してこれを保護できます。 SQL Server Management Studio を使用して SQL Server Always On を構成し、Windows クラスタリングを使用してリスナーを割り当てます。

### <a name="create-an-always-on-availability-group"></a>Always On 可用性グループを作成する

1. SQL Server Management Studio で、 **[Always On 高可用性]** を選択したまま (または右クリックして)、**新しい可用性グループ ウィザード**を開始します。
1. **[オプションの指定]** で、可用性グループ `SHAOG` を指定します。 **[データベースの選択]** で、`SmartHotel360` データベースを選択します。

    ![[データベースの選択] ペインを示すスクリーンショット。](./media/contoso-migration-rehost-vm-sql-ag/aog-1.png)

1. **[レプリカの指定]** で、2 つの SQL ノードを可用性レプリカとして追加し、同期コミットを備えた自動フェールオーバーを提供するようにこれらを構成します。

     ![[レプリカ] タブを示すスクリーンショット。](./media/contoso-migration-rehost-vm-sql-ag/aog-2.png)

1. グループ (`SHAOG`) のリスナーとポートを構成します。 内部ロード バランサーの IP アドレスは、静的 IP アドレス (`10.245.40.100`) として追加されます。

    ![[可用性グループ リスナーの作成] オプションを示すスクリーンショット。](./media/contoso-migration-rehost-vm-sql-ag/aog-3.png)

1. **[データ同期の選択]** で、自動シード処理を有効にします。 このオプションでは、SQL Server によって、グループ内のすべてのデータベースのセカンダリ レプリカが自動的に作成されるため、Contoso がバックアップと復元を手動で実行する必要はありません。 検証後、可用性グループが作成されます。

    ![Always On 可用性グループが作成されたことを示すスクリーンショット。](./media/contoso-migration-rehost-vm-sql-ag/aog-4.png)

1. Contoso がグループを作成するときに問題が発生しました。 Active Directory Windows 統合セキュリティを使用していないので、SQL ログインにアクセス許可を付与して、Windows フェールオーバー クラスター ロールを作成する必要があります。

    ![SQL ログインへのアクセス許可の付与を示すスクリーンショット。](./media/contoso-migration-rehost-vm-sql-ag/aog-5.png)

1. グループが作成されると、SQL Server Management Studio に表示されます。

### <a name="configure-a-listener-on-the-cluster"></a>クラスターでリスナーを構成する

SQL デプロイを設定する最後の手順として、Contoso の管理者は内部ロード バランサーをクラスター上のリスナーとして構成し、リスナーをオンラインにします。 このタスクを実行するスクリプトを使用します。

![クラスターのリスナーを示すスクリーンショット。](./media/contoso-migration-rehost-vm-sql-ag/cluster-listener.png)

### <a name="verify-the-configuration"></a>構成を確認する

このとき、すべての設定が終了し、機能する可用性グループが Azure に存在し、移行したデータベースを使用しています。 管理者は、SQL Server Management Studio で内部ロード バランサーに接続して構成を確認します。

![内部ロード バランサーの接続を示すスクリーンショット。](./media/contoso-migration-rehost-vm-sql-ag/ilb-connect.png)

**さらにサポートが必要な場合**

- [可用性グループ](/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-tutorial#create-the-availability-group)と[リスナー](/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-tutorial#configure-listener)を作成する方法をご覧ください。
- 手動で[クラスターがロード バランサーの IP アドレスを使用するように設定](/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener#configure-the-cluster-to-use-the-load-balancer-ip-address)します。
- [SAS を作成して使用する](/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2)方法の詳細をご覧ください。

## <a name="step-9-migrate-the-vm-with-azure-migrate"></a>手順 9:Azure Migrate で VM を移行する

Contoso の管理者は、簡単なテスト フェールオーバーを実行してから VM を移行します。

### <a name="run-a-test-migration"></a>テスト移行を実行する

テスト移行を実行すると、移行の前に、すべてが正常に機能していることを保証する助けになります。 Contoso の管理者が次の手順を実行します。

1. 利用可能な最新の時点 (`Latest processed`) へのテスト フェールオーバーを実行します。
1. **[フェールオーバーを開始する前にマシンをシャットダウンします]** を選択します。これにより、Azure Migrate は、フェールオーバーをトリガーする前にソース VM のシャットダウンを試みます。 仮にシャットダウンが失敗したとしても、フェールオーバーは続行されます。
1. テスト フェールオーバーでは、以下が実行されます。

    - 移行を実行するために必要なすべての条件が満たされていることを確認する前提条件チェックが実行されます。
    - Azure VM を作成できるように、フェールオーバーによってデータが処理されます。 最新の復旧ポイントが選択された場合は、データから復旧ポイントが作成されます。
    - 前の手順で処理されたデータを使用して、Azure VM が作成されます。

1. フェールオーバーが完了すると、Azure Portal にはレプリカの Azure VM が表示されます。 VM が適切なサイズであること、適切なネットワークに接続されていること、実行中であることを確認します。
1. 確認したら、フェールオーバーをクリーンアップして、確認された事柄をすべて記録して保存します。

### <a name="run-a-failover"></a>フェールオーバーの実行

1. テスト フェールオーバーが予想どおりに機能することを確認したら、移行のための復旧計画を作成し、`WEBVM` を計画に追加します。

     ![[復旧計画の作成] を示すスクリーンショット](./media/contoso-migration-rehost-vm-sql-ag/recovery-plan.png)

1. 計画に基づいてフェールオーバーを実行します。 最新の復旧ポイントを選択します。 Azure Migrate が、フェールオーバーをトリガーする前にオンプレミスの VM のシャットダウンを試みる必要があることを指定します。

    ![[フェールオーバー] ペインを示すスクリーンショット。](./media/contoso-migration-rehost-vm-sql-ag/failover1.png)

1. フェールオーバーが完了したら、Azure VM が想定どおりに Azure Portal に表示されることを確認します。

    ![仮想マシンの概要ペインを示すスクリーンショット。](./media/contoso-migration-rehost-vm-sql-ag/failover2.png)

1. Azure の VM を検査したら、移行を完了して移行プロセスを終了し、VM のレプリケーションを停止して、VM の Azure Migrate の課金を停止します。

    ![[移行の完了] 項目を示すスクリーンショット。](./media/contoso-migration-rehost-vm-sql-ag/failover3.png)

### <a name="update-the-connection-string"></a>接続文字列を更新する

Contoso の管理者は、移行プロセスの最後の手順として、`SHAOG` リスナーで実行されている移行されたデータベースを指すように、アプリケーションの接続文字列を更新します。 この構成は、現在 Azure で実行されている `WEBVM` 上で変更されます。 この構成は ASP.NET アプリケーションの `web.config` にあります。

1. Contoso の管理者は、`C:\inetpub\SmartHotelWeb\web.config` でファイルを見つけ、Always On 可用性グループの FQDN (`shaog.contoso.com`) を反映するようにサーバーの名前を変更します。

    ![Always On 可用性グループの FQDN を示すスクリーンショット。](./media/contoso-migration-rehost-vm-sql-ag/failover4.png)

1. ファイルを更新して保存した後、`WEBVM` 上で IIS を再起動します。 コマンド プロンプトから `iisreset /restart` を使用します。
1. IIS の再起動後、マネージド インスタンス上で実行されているデータベースがアプリケーションで使用されるようになります。

**さらにサポートが必要な場合**

- [テスト フェールオーバーを実行する](/azure/site-recovery/tutorial-dr-drill-azure)方法をご覧ください。
- [復旧計画を作成する方法については、こちらを参照してください](/azure/site-recovery/site-recovery-create-recovery-plans)。
- [Azure へのフェールオーバー](/azure/site-recovery/site-recovery-failover)に関する説明を参照します。

### <a name="clean-up-after-migration"></a>移行後にクリーンアップする

移行後、SmartHotel360 アプリケーションは Azure VM 上で実行されています。 SmartHotel360 データベースは Azure SQL クラスターに配置されています。

次に、Contoso は、以下のクリーンアップ手順を完了する必要があります。

- vCenter のインベントリからオンプレミスの VM を削除します。
- ローカルのバックアップ ジョブからから VM を削除します。
- VM の新しい場所と IP アドレスを示すように社内ドキュメントを更新します。
- 使用停止になった VM とやりとりするリソースがあれば確認します。 すべての関連する設定またはドキュメントを更新して新しい構成を反映します。
- 2 つの新しい VM (`SQLAOG1` と `SQLAOG2`) を、運用監視システムに追加します。

### <a name="review-the-deployment"></a>デプロイを再調査する

リソースを Azure に移行したら、新しいインフラストラクチャを完全に操作可能にして、セキュリティで保護する必要があります。

### <a name="security"></a>セキュリティ

Contoso のセキュリティ チームは、`WEBVM`、`SQLAOG1`、`SQLAOG2` の各仮想マシンを再調査して、セキュリティの問題を特定します。 次のことを行う必要があります。

- VM がアクセスを制御するためのネットワーク セキュリティ グループ (NSG) を見直します。 NSG は、アプリケーションに対して許可されるトラフィックのみが通過できるようにするために使用されます。
- Azure Disk Encryption と Azure Key Vault を使用して、ディスク上のデータをセキュリティで保護することを検討します。
- Transparent Data Encryption を評価します。 その後、新しい Always On 可用性グループで実行されている SmartHotel360 データベースでそれを有効にします。 [Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017) の詳細をご覧ください。

詳細については、「[Azure における IaaS ワークロードのセキュリティに関するベスト プラクティス](/azure/security/fundamentals/iaas)」を参照してください。

## <a name="business-continuity-and-disaster-recovery"></a>事業継続とディザスター リカバリー

事業継続とディザスター リカバリーのために、Contoso は次のアクションを実施します。

- データの安全性を確保するために、Contoso は、[Azure VM Backup](/azure/backup/backup-azure-vms-introduction) を介して `WEBVM`、`SQLAOG1`、`SQLAOG2` の各 VM 上のデータをバックアップします。
- また、Contoso は、Azure Storage を使用して SQL Server を Azure Blob Storage に直接バックアップする方法について確認します。 [Azure Storage を使用した SQL Server のバックアップと復元](/azure/virtual-machines/windows/sql/virtual-machines-windows-use-storage-sql-server-backup-restore)の方法の詳細をご覧ください。
- アプリケーションの稼働状態を維持するために、Contoso は Site Recovery を使用して、Azure 内のアプリケーション VM をセカンダリ リージョンにレプリケートします。 [Azure VM のセカンダリ Azure リージョンへのディザスター リカバリーを設定する](/azure/site-recovery/azure-to-azure-quickstart)方法の詳細をご覧ください。

### <a name="licensing-and-cost-optimization"></a>ライセンスとコストの最適化

- Contoso は WEBVM の既存のライセンスを所有しているので、Azure ハイブリッド特典を活用します。 Contoso は、この特典を活用するために、既存の Azure VM を変換します。
- Contoso は [Azure Cost Management および Billing](/azure/cost-management-billing/cost-management-billing-overview) を使用して、IT リーダーが定めた予算内に確実に収まるようにします。

## <a name="conclusion"></a>まとめ

この記事では、Contoso が Azure Migrate を使用して、アプリケーション フロントエンド VM を Azure に移行することで、SmartHotel360 アプリケーションを Azure にリホストしました。 Contoso は、Azure Database Migration Service を使用して、Azure でプロビジョニングされた SQL Server クラスターにアプリケーション データベースを移行し、それを SQL Server Always On 可用性グループで保護しました。
