---
title: オンプレミスの Linux アプリケーションを Azure VM と Azure Database for MySQL にリホストする
description: Contoso が、オンプレミスの Linux アプリを Azure VM と Azure Database for MySQL に移行することによって、どのようにこのアプリをリホストするかを説明します。
author: givenscj
ms.author: abuck
ms.date: 07/01/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: migrate
services: azure-migrate
ms.openlocfilehash: a5bf1edc6d10d3a0f7f4b3e77fccfa85b6247f89
ms.sourcegitcommit: 26aee3c6f596bb8a9f1e16af93cdf94e41a61dee
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2020
ms.locfileid: "87400566"
---
<!-- cSpell:ignore givenscj OSTICKETWEB OSTICKETMYSQL contosohost vcenter contosodc contosoosticket osticket InnoDB binlog systemctl NSGs -->

# <a name="rehost-an-on-premises-linux-application-to-azure-vms-and-azure-database-for-mysql"></a>オンプレミスの Linux アプリケーションを Azure VM と Azure Database for MySQL にリホストする

この記事では、Contoso という架空の会社が、[LAMP ベース](https://wikipedia.org/wiki/LAMP_(software_bundle))の 2 層アプリケーションをリホストし、Azure Virtual Machines (VM) と Azure Database for MySQL を使用して、それをオンプレミスから Azure に移行する方法について説明します。

この例で使用されるサービス デスク アプリケーションである osTicket は、オープンソースとして提供されています。 独自のテストに使用する場合は、[GitHub](https://github.com/osTicket/osTicket) からダウンロードできます。

## <a name="business-drivers"></a>ビジネス ドライバー

IT リーダーシップ チームは、ビジネス パートナーと緊密に連携して、彼らが何を達成したいと望んでいるかを理解しています。

- **ビジネスの成長への対応。** Contoso は成長を続けています。その結果、オンプレミスのシステムとインフラストラクチャに負荷がかかっています。
- **リスクの限定。** サービス デスク アプリケーションはビジネスにとって非常に重要です。 Contoso は、リスクを負うことなく Azure に移行したいと考えています。
- **拡張。** Contoso は、アプリケーションに今すぐ変更を加えることは望んでいません。 アプリケーションの安定性を維持することを望んでいます。

## <a name="migration-goals"></a>移行の目標

Contoso クラウド チームは、最適な移行方法を決定するために、この移行の目標を明確にしました。

- 移行後、Azure 内のアプリケーションは、現在のオンプレミスの VMware 環境内と同じパフォーマンスを発揮できる必要があります。 このアプリケーションは、オンプレミスにあるときと同様に、クラウド内でも非常に重要であり続けます。
- Contoso は、このアプリケーションに投資することは望んでいません。 これはビジネスにとって重要なものですが、現状のまま確実にクラウドに移すことだけを望んでいます。
- Windows アプリケーションの移行をいくつか完了した後、Contoso は Azure で Linux ベースのインフラストラクチャを使用する方法を学びたいと考えています。
- Contoso は、アプリケーションがクラウドに移された後、データベース管理タスクを最小限にしたいと考えています。

## <a name="proposed-architecture"></a>提案されたアーキテクチャ

このシナリオでは:

- 現在、アプリケーションは 2 つの VM (`OSTICKETWEB` と `OSTICKETMYSQL`) に階層化されています。
- これらの VM は、VMware ESXi ホスト `contosohost1.contoso.com` (バージョン 6.5) 上に配置されています。
- VMware 環境は vCenter Server 6.5 (`vcenter.contoso.com`) によって管理され、VM 上で実行されています。
- Contoso には、オンプレミスのデータセンター (`contoso-datacenter`) があり、オンプレミスのドメイン コントローラー (`contosodc1`) が含まれています。
- `OSTICKETWEB` 上の Web アプリケーションは、Azure IaaS (サービスとしてのインフラストラクチャ) VM に移行されます。
- アプリケーション データベースは、Azure Database for MySQL PaaS に移行されます。
- Contoso は運用ワークロードを移行しようとしているため、リソースは運用リソース グループ `ContosoRG` に配置されます。
- `OSTICKETWEB` リソースはプライマリ リージョン (米国東部 2) にレプリケートされ、運用ネットワーク (`VNET-PROD-EUS2`) に配置されます。
  - Web VM は、フロントエンド サブネット (`PROD-FE-EUS2`) 内に存在することになります。
- アプリケーション データベースは、[Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview) を使用して Azure Database for MySQL に移行されます。
- Contoso データセンター内のオンプレミス VM は、移行が行われた後に使用停止にされます。

    ![シナリオ アーキテクチャの図。](./media/contoso-migration-rehost-linux-vm-mysql/architecture.png)

## <a name="migration-process"></a>移行プロセス

Contoso は、次のようにして移行プロセスを完了します。

Web VM を移行するには:

- 最初の手順として、Contoso は、Azure Migrate をデプロイするために必要な Azure とオンプレミスのインフラストラクチャを設定します。
- 同社には、[Azure インフラストラクチャ](./contoso-migration-infrastructure.md)が既に用意されているので、Azure Migrate を使用して VM のレプリケーションを追加および構成するだけで済みます。サーバー移行ツール。
- すべての準備ができたら、VM のレプリケートを開始できます。
- レプリケーションが有効になり、機能するようになったら、Contoso は Azure Migrate を使用して移動を完了します。

データベースを移行するには:

1. Contoso は、Azure で MySQL インスタンスをプロビジョニングします。
1. Contoso は Database Migration Service を設定して、オンプレミスのデータベース サーバーへのアクセスを確保します。
1. Contoso は、データベースを Azure Database for MySQL に移行します。

    ![移行プロセスの図。](./media/contoso-migration-rehost-linux-vm-mysql/migration-process.png)

### <a name="azure-services"></a>Azure サービス

| サービス | 説明 | コスト |
| --- | --- | --- |
| [Azure Migrate](https://docs.microsoft.com/azure/migrate/migrate-services-overview) | Contoso は、Azure Migrate を使用して VMware VM を評価します。 Azure Migrate は、マシンの移行適合性を評価します。 そのうえで、Azure で実行するための、サイズとコストの見積もりを提供します。 | [Azure Migrate](https://azure.microsoft.com/pricing/details/azure-migrate) は、追加料金なしで利用できます。 評価と移行に使用することにしたツール (ファーストパーティまたは ISV) によっては料金が発生する場合があります。 |
| [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview) | Database Migration Service を使用すると、ダウンタイムを最小限に抑えながら、複数のデータベース ソースから Azure データ プラットフォームにシームレスに移行できます。 | [サポートされているリージョン](https://docs.microsoft.com/azure/dms/dms-overview#regional-availability)に関する情報と、[Database Migration Service の価格](https://azure.microsoft.com/pricing/details/database-migration)に関する情報をご覧ください。 |
| [Azure Database for MySQL](https://docs.microsoft.com/azure/mysql) | このデータベースは、オープン ソースの MySQL データベース エンジンに基づいています。 フルマネージドのエンタープライズ対応コミュニティ MySQL データベースが提供され、アプリケーションの開発とデプロイに使用できます。 | Azure Database for MySQL の[価格](https://azure.microsoft.com/pricing/details/mysql)とスケーラビリティのオプションに関する情報をご覧ください。 |

## <a name="prerequisites"></a>前提条件

このシナリオで Contoso に必要なことを以下に示します。

| 必要条件 | 詳細 |
| --- | --- |
| **Azure サブスクリプション** | Contoso は前の記事でサブスクリプションを作成しました。 Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/free)を作成してください。 <br><br> 無料アカウントを作成する場合、サブスクリプションの管理者としてすべてのアクションを実行できます。 <br><br> 既存のサブスクリプションを使用する場合に、自分が管理者でないようであれば、管理者と連携して所有者または共同作成者のアクセス許可を自分に割り当てます。 <br><br> より詳細なアクセス許可が必要な場合は、[ロールベースのアクセス制御 (RBAC) を使用した Azure Site Recovery のアクセスの管理](https://docs.microsoft.com/azure/site-recovery/site-recovery-role-based-linked-access-control)に関する記事をご覧ください。 |
| **Azure インフラストラクチャ** | Contoso は、[移行のための Azure インフラストラクチャ](./contoso-migration-infrastructure.md)についての記事で説明されているように、Azure インフラストラクチャを設定します。 |
| **オンプレミスのサーバー** | オンプレミスの vCenter Server では、バージョン 5.5、6.0、6.5、または 6.7 が実行されている必要があります。 <br><br> バージョン 5.5、6.0、6.5、または 6.7 が実行されている ESXi ホスト。 <br><br> ESXi ホスト上で実行している 1 つ以上の VMware VM。 |
| **オンプレミスの VM** | Azure での実行が保証されている [Linux マシンを確認します](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)。 |

## <a name="scenario-steps"></a>シナリオのステップ

Contoso 管理者は、次のようにして移行を完了します。

> [!div class="checklist"]
>
> - **ステップ 1:Azure Migrate: Server Migration 用の Azure を準備するServer Migration.** Server Migration ツールを Azure Migrate プロジェクトに追加します。
> - **手順 2:Azure Migrate: Server Migration 用のオンプレミス VMware を準備するServer Migration.** VM 検出用のアカウントを準備し、移行後に Azure Virtual Machines に接続するための準備をします。
> - **ステップ 3:VM をレプリケートする。** レプリケーションを設定し、Azure Storage への VM のレプリケーションを開始します。
> - **手順 4:Azure Migrate: Server Migration を使用してアプリケーション VM を移行する。Server Migration.** テスト移行を実行してすべてが機能していることを確認した後、完全移行を実行して VM を Azure に移動します。
> - **手順 5:データベースを移行する。** Azure Database Migration Service を使用して移行を設定します。

## <a name="step-1-prepare-azure-for-the-azure-migrate-server-migration-tool"></a>手順 1:Azure Migrate: Server Migration ツール用の Azure を準備するServer Migration ツール

Contoso が VM を Azure に移行するには、以下の Azure コンポーネントが必要です。

- 移行中に作成される Azure VM が配置される仮想ネットワーク。
- Azure Migrate: Server Migration ツール (OVA) (プロビジョニングおよび構成済みのもの)。

コンポーネントを設定するには、Contoso の管理者が次の手順を実行します。

1. ネットワークをセットアップします。 Contoso は、[Azure インフラストラクチャのデプロイ](./contoso-migration-infrastructure.md)を行ったときに、Azure Migrate: Server Migration に使用できるネットワークを既にセットアップしています。

1. Azure Migrate: Server Migration ツールをプロビジョニングしますサーバー移行ツール。

    1. Azure Migrate から、OVA イメージをダウンロードし、VMware にインポートします。

        ![OVA ファイルのダウンロードを示すスクリーンショット。](./media/contoso-migration-rehost-vm/migration-download-ova.png)

    1. インポートしたイメージを起動し、次の手順を使用してツールを構成します。

       1. 前提条件を設定します。

          ![[前提条件のセットアップ] 画面を示すスクリーンショット。](./media/contoso-migration-rehost-vm/migration-setup-prerequisites.png)

       1. ツールを Azure サブスクリプションにポイントします。

          ![サブスクリプションの構成を示すスクリーンショット](./media/contoso-migration-rehost-vm/migration-register-azure.png)

       1. VMware vCenter の資格情報を設定します。

          ![資格情報の構成を示すスクリーンショット。](./media/contoso-migration-rehost-vm/migration-vcenter-server.png)

       1. 検出用の Linux ベースの資格情報を追加します。

          ![Linux ベースの資格情報の構成を示すスクリーンショット。](./media/contoso-migration-rehost-vm/migration-credentials.png)

1. ツールの構成後、ツールによってすべての仮想マシンが列挙されるまでに少し時間がかかります。 このプロセスが完了すると、Azure の Azure Migrate ツールに VM が設定されます。

**さらにサポートが必要な場合**

[Azure Migrate: Server Migration ツール](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-server-migration-tool)の設定についてご確認ください。

## <a name="step-2-prepare-on-premises-vmware-for-azure-migrate-server-migration"></a>手順 2:Azure Migrate: Server Migration 用のオンプレミス VMware を準備するServer Migration

Contoso は、Azure への移行後に、Azure 内のレプリケートされた VM に接続できることを望んでいます。 Contoso の管理者は次のことを行う必要があります。

- Azure VM にアクセスするには、移行前に、オンプレミスの Linux VM で SSH を有効にします。 Ubuntu の場合、`sudo apt-get ssh install -y` コマンドを使用してこの手順を実行できます。
- 移行の実行後、**ブート診断**を調べて、VM のスクリーンショットを表示できます。
- これが機能しない場合は、VM が実行中であることを確認し、こちらの[トラブルシューティングのヒント](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)を参照する必要があります。
- [Azure Linux エージェント](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux)をインストールします。

**さらにサポートが必要な場合**

[移行に向けて VM を準備する](https://docs.microsoft.com/azure/migrate/prepare-for-migration)方法をご覧ください。

## <a name="step-3-replicate-vms"></a>手順 3:VM をレプリケートする

Contoso の管理者は、Azure への移行を実行する前に、レプリケーションを設定して有効にする必要があります。

検出が完了したら、Azure へのアプリケーション VM のレプリケーションを開始できます。

1. Azure Migrate プロジェクトで、 **[サーバー]**  >  **[Azure Migrate:Server Migration]** の順に移動し、 **[レプリケート]** を選択します。

    ![[レプリケート] オプションを示すスクリーンショット。](./media/contoso-migration-rehost-linux-vm/select-replicate.png)

1. **[レプリケート]**  >  **[ソースの設定]**  >  **[マシンは仮想化されていますか?]** で、 **[はい (VMware vSphere の場合)]** を選択します。

1. **[オンプレミスのアプライアンス]** で、設定した Azure Migrate アプライアンスの名前を選択し、 **[OK]** を選択します。

    ![[ソースの設定] タブを示すスクリーンショット。](./media/contoso-migration-rehost-linux-vm/source-settings.png)

1. **[仮想マシン]** で、レプリケートするマシンを選択します。
    - VM の評価を実行した場合は、評価結果から VM のサイズ設定とディスクの種類 (Premium または Standard) の推奨事項を適用できます。 **[Azure Migrate Assessment から移行設定をインポートしますか?]** で、 **[はい]** オプションを選択します。
    - 評価を実行しなかった場合、または評価の設定を使用しない場合は、 **[いいえ]** オプションを選択します。
    - 評価の使用を選択した場合は、VM グループと評価名を選択します。

    ![評価の選択を示すスクリーンショット。](./media/contoso-migration-rehost-linux-vm/select-assessment.png)

1. **[仮想マシン]** で、必要に応じて VM を検索し、移行する各 VM を選択します。 次に、 **[次のステップ: ターゲット設定]** をクリックします。

1. **[ターゲット設定]** で、移行先のサブスクリプションとターゲット リージョンを選択します。 移行後に Azure VM が属するリソース グループを指定します。 **[仮想ネットワーク]** で、移行後に Azure VM の参加先となる Azure 仮想ネットワークまたはサブネットを選択します。

1. **[Azure ハイブリッド特典]** で、

    - Azure ハイブリッド特典を適用しない場合は、 **[いいえ]** を選択します。 **[次へ]** を選択します。

1. **[コンピューティング]** で、VM の名前、サイズ、OS ディスクの種類、可用性セットを確認します。 VM は [Azure の要件](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#vmware-requirements)に準拠している必要があります。

    - **VM サイズ:** 評価の推奨事項を使用している場合は、[VM サイズ] ドロップダウン リストに推奨サイズが表示されます。 それ以外の場合は、Azure Migrate によって、Azure サブスクリプション内の最も近いサイズが選択されます。 または、 **[Azure VM サイズ]** でサイズを手動で選択します。
    - **OS ディスク:** VM の OS (ブート) ディスクを指定します。 OS ディスクは、オペレーティング システムのブートローダーとインストーラーがあるディスクです。
    - **可用性セット:** 移行後に VM を Azure 可用性セットに配置する必要がある場合は、セットを指定します。 このセットは、移行用に指定するターゲット リソース グループ内に存在する必要があります。

1. **[ディスク]** で、VM ディスクを Azure にレプリケートするかどうかを指定します。 Azure でのディスクの種類 (Standard SSD または HDD、あるいは Premium マネージド ディスク) を選択し、 **[次へ]** を選択します。
    - レプリケーションからディスクを除外できます。
    - ディスクを除外すると、移行後に Azure VM 上に存在しなくなります。

1. **[レプリケーションの確認と開始]** で、設定を確認します。 次に、 **[レプリケート]** を選択して、サーバーの初期レプリケーションを開始します。

> [!NOTE]
> レプリケーションを開始する前であれば、 **[管理]**  >  **[マシンのレプリケート]** でレプリケーションの設定をいつでも更新できます。 レプリケーションの開始後は、設定を変更することができません。

## <a name="step-4-migrate-the-vm-with-azure-migrate-server-migration"></a>手順 4:Azure Migrate: Server Migration で VM を移行するServer Migration

Contoso の管理者は、クイック テスト移行を実行した後、完全移行を実行して Web VM を移動します。

### <a name="run-a-test-migration"></a>テスト移行を実行する

1. **[移行の目標]**  >  **[サーバー]**  >  **[Azure Migrate: Server Migration]** で、 **[移行されたサーバーをテストします]** を選択します。

     ![[移行されたサーバーをテストします] オプションを示すスクリーンショット。](./media/contoso-migration-rehost-linux-vm/test-migrated-servers.png)

1. テストする VM を選択したまま (または右クリックし)、 **[テスト移行]** を選択します。

    ![[テスト移行] 項目を示すスクリーンショット。](./media/contoso-migration-rehost-linux-vm/test-migrate.png)

1. **[テスト移行]** で、移行後に Azure VM が配置される Azure 仮想ネットワークを選択します。 非運用環境の仮想ネットワークを使用することをお勧めします。
1. **テスト移行**ジョブが開始されます。 ポータルの通知でジョブを監視します。
1. 移行の完了後、Azure portal の **[仮想マシン]** で、移行された Azure VM を確認します。 マシン名には、サフィックス **-Test** が含まれています。
1. テストが完了したら、 **[マシンのレプリケート]** で Azure VM を選択したままにします (または右クリックします)。 次に、 **[テスト移行をクリーンアップ]** を選択します。

    ![[テスト移行をクリーンアップ] 項目を示すスクリーンショット。](./media/contoso-migration-rehost-linux-vm/clean-up.png)

### <a name="migrate-the-vm"></a>VM の移行

Contoso の管理者は、ここで、移動を完了する完全移行を実行します。

1. Azure Migrate プロジェクトで、 **[サーバー]**  >  **[Azure Migrate:Server Migration]** の順に移動し、 **[サーバーをレプリケートしています]** を選択します。

    ![[サーバーをレプリケートしています] オプションを示すスクリーンショット。](./media/contoso-migration-rehost-linux-vm/replicating-servers.png)

1. **[マシンのレプリケート]** で VM を選択したまま (または右クリックし)、 **[移行]** を選択します。
1. **[移行]**  >  **[仮想マシンをシャットダウンし、データ損失のない計画された移行を実行しますか]** で、 **[はい]**  >  **[OK]** の順に選択します。
    - 既定では、Azure Migrate によってオンプレミス VM がシャットダウンされ、前回のレプリケーションが発生した後に行われた VM の変更を同期するためにオンデマンド レプリケーションが実行されます。 このアクションにより、データ損失が発生しなくなります。
    - VM をシャットダウンしたくない場合は、 **[いいえ]** を選択します。
1. VM に対して移行ジョブが開始されます。 Azure 通知でジョブを追跡します。
1. ジョブが完了したら、 **[仮想マシン]** ページで VM を表示して管理できます。

## <a name="step-5-provision-azure-database-for-mysql"></a>手順 5:Azure Database for MySQL をプロビジョニングする

Contoso の管理者は、MySQL データベース インスタンスを、プライマリ リージョン (`East US 2`) 内にプロビジョニングします。

1. Azure portal で、Azure Database for MySQL リソースを作成します。

    ![[Azure Database for MySQL] オプションを示すスクリーンショット。](./media/contoso-migration-rehost-linux-vm-mysql/mysql-1.png)

1. Azure データベースの名前 (`contosoosticket`) を追加します。 運用リソース グループ `ContosoRG` にデータベースを追加し、その資格情報を指定します。
1. オンプレミスの MySQL データベースはバージョン 5.7 であるため、互換性のためにこのバージョンを選択します。 データベースの要件に適合する、既定のサイズを使用します。

     ![MySQL の資格情報を示すスクリーンショット。](./media/contoso-migration-rehost-linux-vm-mysql/mysql-2.png)

1. **[バックアップ冗長オプション]** で、 **[geo 冗長]** を選択します。 このオプションを使用すると、停止が発生した場合に、セカンダリ リージョン (`Central US`) にデータベースを復元できます。 このオプションは、データベースをプロビジョニングするときにのみ構成できます。

     ![[geo 冗長] オプションを示すスクリーンショット。](./media/contoso-migration-rehost-linux-vm-mysql/db-redundancy.png)

1. `VNET-PROD-EUS2` ネットワークで、 **[サービス エンドポイント]** に移動し、SQL サービスのサービス エンドポイント (データベース サブネット) を追加します。

    ![サービス エンドポイントの追加を示すスクリーンショット。](./media/contoso-migration-rehost-linux-vm-mysql/mysql-3.png)

1. サブネットを追加したら、運用ネットワークのデータベース サブネットからのアクセスを許可する仮想ネットワーク規則を作成します。

    ![仮想ネットワーク規則の作成を示すスクリーンショット。](./media/contoso-migration-rehost-linux-vm-mysql/mysql-4.png)

## <a name="step-6-migrate-the-database"></a>手順 6:データベースを移行する

MySQL データベースは、次の方法で移動できます。 各オプションで、Contoso の管理者はターゲットに対して Azure Database for MySQL インスタンスを作成する必要があります。 作成後、次の手順で説明する 2 つのパスを使用して移行を実行できます。

- 6a:Database Migration Service
- 6b:MySQL Workbench のバックアップと復元

### <a name="step-6a-migrate-the-database-via-database-migration-service"></a>手順 6a:Database Migration Service を使用してデータベースを移行する

Contoso の管理者は、[ステップバイステップの移行チュートリアル](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online)に従って、Database Migration Service を使用してデータベースを移行します。 MySQL 5.6 または 5.7 を使用して、オンライン、オフライン、およびハイブリッド (プレビュー) の移行を実行できます。

> [!NOTE]
> MySQL 8.0 は Azure Database for MySQL でサポートされていますが、Database Migration Service ツールではこのバージョンはまだサポートされていません。

まとめると、Contoso の管理者は次の作業を行う必要があります。

- 次のすべての移行の前提条件が満たされていることを確認します。
  - MySQL サーバーのデータベース ソースは、Azure Database for MySQL でサポートされているバージョンと一致する必要があります。 Azure Database for MySQL では、MySQL Community Edition、InnoDB ストレージ エンジン、同じバージョンのソースとターゲット間の移行がサポートされています。
  - `my.ini` (Windows) または `my.cnf` (Unix) でバイナリ ログを有効にします。 これを行わないと、移行ウィザードで次のエラーが発生します。"バイナリ ログにエラーがあります。 変数 binlog_row_image の値が 'minimal' です。 その値を 'full' に変更してください。" 詳細については、[MySQL の Web サイト](https://go.microsoft.com/fwlink/?linkid=873009`)を参照してください。
  - ユーザーは `ReplicationAdmin` ロールを持っている必要があります。
  - 外部キーとトリガーを使用せずにデータベース スキーマを移行します。
- Azure ExpressRoute または VPN を介してオンプレミス ネットワークに接続する仮想ネットワークを作成します。
- 仮想ネットワークに接続されている `Premium` SKU を使用して、Database Migration Service インスタンスを作成します。
- そのインスタンスから、仮想ネットワーク経由で MySQL データベースに確実にアクセスできるようにします。 仮想ネットワーク レベル、ネットワーク VPN、および MySQL をホストするマシンで、Azure から MySQL へのすべての受信ポートが許可されていることを確認します。
- Database Migration Service ツールを実行します。
  - 移行プロジェクトを作成します。

    ![移行プロジェクトの作成を示すスクリーンショット。](./media/contoso-migration-rehost-linux-vm-mysql/migration-dms-new-project.png)

    ![[新しい移行プロジェクト] ペインを示すスクリーンショット。](./media/contoso-migration-rehost-linux-vm-mysql/migration-dms-new-project-02.png)

  - ソース (オンプレミス データベース) を追加します。

    ![[ソースの追加に関する詳細] ペインを示すスクリーンショット。](./media/contoso-migration-rehost-linux-vm-mysql/migration-dms-source.png)

  - ターゲットを選択します。

    ![[ターゲットの詳細] ペインを示すスクリーンショット。](./media/contoso-migration-rehost-linux-vm-mysql/migration-dms-target.png)

  - 移行するデータベースを選択します。

    ![[ターゲット データベースへマッピング] ペインを示すスクリーンショット。](./media/contoso-migration-rehost-linux-vm-mysql/migration-dms-databases.png)

  - 詳細設定を構成します。

    ![[移行の設定] ペインを示すスクリーンショット。](./media/contoso-migration-rehost-linux-vm-mysql/migration-dms-settings.png)

  - レプリケーションを開始し、エラーを解決します。

    ![エラーの解決を示すスクリーンショット。](./media/contoso-migration-rehost-linux-vm-mysql/migration-dms-monitor.png)

  - 最終的なカットオーバーを実行します。 ![最終的な一括を示すスクリーンショット。](./media/contoso-migration-rehost-linux-vm-mysql/migration-dms-cutover.png)

    ![[一括を完了する] ペインを示すスクリーンショット。](./media/contoso-migration-rehost-linux-vm-mysql/migration-dms-cutover-complete.png)

    ![[移行アクティビティ] 一覧を示すスクリーンショット。](./media/contoso-migration-rehost-linux-vm-mysql/migration-dms-cutover-complete-02.png)

  - 外部キーとトリガーをすべて復帰させます。
  - 新しいデータベースを使用するようにアプリケーションを変更します。

    ![新しいデータベースを使用するためのアプリケーションの変更を示すスクリーンショット。](./media/contoso-migration-rehost-linux-vm-mysql/migration-dms-cutover-apps.png)

### <a name="step-6b-migrate-the-database-mysql-workbench"></a>手順 6b:データベースの移行 (MySQL Workbench)

Contoso の管理者は、MySQL ツールでバックアップと復元を使用してデータベースを移行します。 MySQL Workbench をインストールし、データベースを `OSTICKETMYSQL` からバックアップして Azure Database for MySQL に復元します。

### <a name="install-mysql-workbench"></a>MySQL Workbench のインストール

1. [前提条件を確認し、MySQL Workbench をダウンロード](https://dev.mysql.com/downloads/workbench/?utm_source=tuicool)します。

1. [インストール手順](https://dev.mysql.com/doc/workbench/en/wb-installing.html)に従って、MySQL Workbench for Windows をインストールします。

1. MySQL Workbench で、OSTICKETMYSQL への MySQL 接続を作成します。

    ![[接続] タブを示すスクリーンショット。](./media/contoso-migration-rehost-linux-vm-mysql/workbench1.png)

1. データベースを `osticket` として、ローカルの自己完結型ファイルにエクスポートします。

    ![[データ エクスポート] 画面を示すスクリーンショット。](./media/contoso-migration-rehost-linux-vm-mysql/workbench2.png)

1. データベースがローカルにバックアップされたら、Azure Database for MySQL インスタンスへの接続を作成します。

    ![接続の成功を通知するポップアップ メッセージを示すスクリーンショット。](./media/contoso-migration-rehost-linux-vm-mysql/workbench3.png)

1. 次に、自己完結型ファイルから、Azure Database for MySQL インスタンス内のデータベースをインポート (復元) します。 このインスタンスに新しいスキーマ (`osticket`) が作成されます。

    ![[Import from Self-Contained File]\(自己完結型ファイルからのインポート\) オプションを示すスクリーンショット。](./media/contoso-migration-rehost-linux-vm-mysql/workbench4.png)

### <a name="connect-the-vm-to-the-database"></a>VM をデータベースに接続する

移行プロセスの最終ステップとして、Contoso の管理者は、`OSTICKETMYSQL` VM で実行されているアプリケーション データベースを指すようにアプリケーションの接続文字列を更新します。

1. PuTTY または別の SSH クライアントを使用して、`OSTICKETWEB` VM への SSH 接続を行います。 この VM はプライベートであるため、プライベート IP アドレスを使用して接続します。

    ![[仮想マシンに接続する] ペインを示すスクリーンショット。](./media/contoso-migration-rehost-linux-vm/db-connect.png)

    ![データベースへの接続を示すスクリーンショット。](./media/contoso-migration-rehost-linux-vm/db-connect2.png)

1. `OSTICKETWEB` VM が `OSTICKETMYSQL` VM と通信できることを確認します。 この時点で、構成はオンプレミスの IP アドレス `172.16.0.43` でハードコードされています。

    **更新の前:**

    ![更新前の IP を示すスクリーンショット。](./media/contoso-migration-rehost-linux-vm/update-ip1.png)

    **更新の後:**

    ![更新後の IP を示すスクリーンショット。](./media/contoso-migration-rehost-linux-vm/update-ip2.png)

1. `systemctl restart apache2` でサービスを再起動します。

    ![サービスの再起動を示すスクリーンショット。](./media/contoso-migration-rehost-linux-vm/restart.png)

1. 最後に、Contoso ドメイン コントローラーのいずれかで、`OSTICKETWEB` と `OSTICKETMYSQL` の DNS レコードを更新します。

    ![DNS レコードの更新を示すスクリーンショット。](./media/contoso-migration-rehost-linux-vm-mysql/update-dns.png)

    ![DNS レコードの更新を示すスクリーンショット。](./media/contoso-migration-rehost-linux-vm-mysql/update-dns.png)

**さらにサポートが必要な場合**

- [テスト移行を実行する](https://docs.microsoft.com/azure/migrate/tutorial-migrate-vmware#run-a-test-migration)方法をご覧ください。
- [VM を Azure に移行する](https://docs.microsoft.com/azure/migrate/tutorial-migrate-vmware#migrate-vms)方法をご覧ください。

## <a name="review-the-deployment"></a>デプロイを再調査する

アプリケーションが実行されるようになったので、Contoso は新しいインフラストラクチャを完全に操作可能にし、セキュリティで保護する必要があります。

## <a name="clean-up-after-migration"></a>移行後にクリーンアップする

移行が完了し、osTicket アプリケーション層が Azure VM で実行されています。

ここで、Contoso は次の作業を行う必要があります。

- VMware VM を vCenter インベントリから削除します。
- ローカルのバックアップ ジョブからからオンプレミスの VM を削除します。
- 新しい場所と IP アドレスを示すように内部ドキュメントを更新します。
- オンプレミスの VM とやり取りするリソースを確認します。 すべての関連する設定またはドキュメントを更新して新しい構成を反映します。
- Contoso は、依存関係マッピングを指定して Azure Migrate を使用し、`OSTICKETWEB` VM の移行を評価しました。

### <a name="security"></a>セキュリティ

Contoso セキュリティ チームは、VM とデータベースを調査してセキュリティの問題を特定します。

- VM がアクセスを制御するためのネットワーク セキュリティ グループ (NSG) を見直します。 NSG は、アプリケーションに対して許可されるトラフィックのみが通過できるようにするために使用されます。
- Azure Disk Encryption と Azure Key Vault を使用して、VM ディスク上のデータをセキュリティで保護することを検討します。
- VM とデータベース インスタンス間の通信は、SSL 用に構成されていません。 データベース トラフィックをハッキングできないように、SSL を構成する必要があります。

詳細については、「[Azure における IaaS ワークロードのセキュリティに関するベスト プラクティス](https://docs.microsoft.com/azure/security/fundamentals/iaas)」を参照してください。

<!-- docsTest:ignore "Quickstart: Set" -->

### <a name="business-continuity-and-disaster-recovery"></a>事業継続とディザスター リカバリー

事業継続とディザスター リカバリーのために、Contoso は次のアクションを実施します。

- **データの安全性を確保する。** Contoso は、[Azure VM バックアップ](https://docs.microsoft.com/azure/backup/backup-azure-vms-introduction)を使用して、アプリケーション VM 上のデータをバックアップします。 データベースのバックアップを構成する必要はありません。 Azure Database for MySQL は、サーバーのバックアップを自動的に作成して保存します。 Contoso はデータベースに geo 冗長性を使用することを選択したので、データベースは回復性があり、運用準備ができています。
- **アプリケーションの稼働を維持する。** Contoso は、Site Recovery を使用して、Azure 内のアプリケーション VM をセカンダリ リージョンにレプリケートします。 詳細については、「[クイック スタート: Azure VM のセカンダリ Azure リージョンへのディザスター リカバリーの設定](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-quickstart)」を参照してください。

### <a name="licensing-and-cost-optimization"></a>ライセンスとコストの最適化

- Contoso は、リソースのデプロイ後、[Azure インフラストラクチャ](./contoso-migration-infrastructure.md#set-up-tagging)のデプロイ中に定義されたとおりに Azure タグを割り当てます。
- Contoso Ubuntu サーバーにライセンスの問題はありません。
- Contoso は [Azure Cost Management および Billing](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview) を使用して、IT リーダーが定めた予算内に確実に収まるようにします。
