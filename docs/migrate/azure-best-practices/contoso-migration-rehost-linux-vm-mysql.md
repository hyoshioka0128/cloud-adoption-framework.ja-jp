---
title: Linux サービス デスクトップ アプリを Azure と Azure Database for MySQL にリホストする
description: Contoso が、オンプレミスの Linux アプリを Azure VM と Azure Database for MySQL に移行することによって、どのようにこのアプリをリホストするかを説明します。
author: givenscj
ms.author: abuck
ms.date: 04/01/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: migrate
services: azure-migrate
ms.openlocfilehash: ca635542f68a69d4e823b2917f69646074438550
ms.sourcegitcommit: 6fef15cc3a8af725dc743e19f127513bc58dd257
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2020
ms.locfileid: "84023527"
---
<!-- cSpell:ignore givenscj OSTICKETWEB OSTICKETMYSQL contosohost vcenter contosodc contosoosticket osticket InnoDB binlog systemctl NSGs -->

# <a name="rehost-an-on-premises-linux-app-to-azure-vms-and-azure-database-for-mysql"></a>オンプレミスの Linux アプリを Azure VM と Azure Database for MySQL にリホストする

この記事では、架空の会社である Contoso が、2 階層の Linux ベースの Apache/MySQL/PHP (LAMP) アプリをリホストし、オンプレミスから Azure に移行して Azure VM と Azure Database for MySQL を使用する方法を示します。

この例で使用される osTicket (サービス デスク アプリ) は、オープン ソースとして提供されています。 ご自身のテストでそれを使用したい場合は、[GitHub](https://github.com/osTicket/osTicket) からダウンロードできます。

## <a name="business-drivers"></a>ビジネス ドライバー

IT リーダーシップ チームは、ビジネス パートナーと密接に連絡を取り合い、彼らが何を達成しようとしているのかを理解しました。

- **ビジネスの成長への対応。** Contoso は成長を続けています。その結果、オンプレミスのシステムとインフラストラクチャに負荷がかかっています。
- **リスクの限定。** サービスデスク アプリはビジネスにとって非常に重要です。 Contoso は、リスクを負うことなく Azure に移行したいと考えています。
- **拡張。** Contoso は、今すぐアプリに変更を加えることは望んでいません。 アプリの安定性を維持したいだけです。

## <a name="migration-goals"></a>移行の目標

Contoso クラウド チームは、最適な移行方法を決定するために、この移行の目標を明確にしました。

- 移行後の Azure 内のアプリは、現在のオンプレミス VMware 環境と同じパフォーマンスを発揮できる必要があります。 このアプリは、オンプレミスの場合と同様に、クラウド内にあっても非常に重要であり続けます。
- Contoso は、このアプリへの投資を望んでいません。 これはビジネスにとって重要なものですが、Contoso は現状のまま確実にクラウドに移すことだけを望んでいます。
- Windows アプリの移行を何件か完了したら、Contoso は Azure で Linux ベースのインフラストラクチャを使用する方法を学びたいと考えています。
- Contoso は、アプリケーションがクラウドに移された後、データベース管理タスクを最小限にしたいと考えています。

## <a name="proposed-architecture"></a>提案されたアーキテクチャ

このシナリオでは:

- 現在アプリは 2 つの VM (`OSTICKETWEB` と `OSTICKETMYSQL`) に階層化されています。
- これらの VM は、VMware ESXi ホスト `contosohost1.contoso.com` (バージョン 6.5) 上に配置されています。
- VMware 環境は、VM で実行中のvCenter Server 6.5 (`vcenter.contoso.com`) によって管理されています。
- Contoso には、オンプレミスのデータセンター (`contoso-datacenter`) があり、オンプレミスのドメイン コントローラー (`contosodc1`) が含まれています。
- `OSTICKETWEB` 上の Web 階層アプリは、Azure IaaS VM に移行されます。
- アプリのデータベースは、Azure Database for MySQL の PaaS サービスに移行されます。
- Contoso は運用ワークロードを移行しようとしているため、リソースは、運用リソース グループ `ContosoRG` 内に存在することになります。
- `OSTICKETWEB` リソースはプライマリ リージョン (米国東部 2) にレプリケートされ、運用ネットワーク (`VNET-PROD-EUS2`) 内に配置されます。
  - Web VM は、フロントエンド サブネット (`PROD-FE-EUS2`) 内に存在することになります。
- アプリのデータベースは、[Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview) を使用して Azure Database for MySQL に移行されます。
- Contoso データセンター内のオンプレミス VM は、移行が行われた後に使用停止にされます。

    ![シナリオのアーキテクチャ](./media/contoso-migration-rehost-linux-vm-mysql/architecture.png)

## <a name="migration-process"></a>移行プロセス

Contoso は、次のようにして移行プロセスを完了します。

Web VM を移行するには:

- 最初の手順として、Contoso は、Azure Migrate をデプロイするために必要な Azure とオンプレミスのインフラストラクチャを設定します。
- [Azure インフラストラクチャ](./contoso-migration-infrastructure.md)は既に用意されているので、Contoso では、Azure Migrate: Server Migration ツールを使用して VM のレプリケーションの追加と構成を行うだけで済みますサーバー移行ツール。
- すべての準備ができたら、VM のレプリケートを開始できます。
- レプリケーションを有効にしたら、Contoso は Azure Migrate を使用して移動を完了します。

データベースを移行するには:

1. Contoso は、Azure で MySQL インスタンスをプロビジョニングします。
2. Contoso は Azure Database Migration Service (DMS) を設定し、オンプレミスのデータベース サーバーへのアクセスを保証します。
3. Contoso は、データベースを Azure Database for MySQL に移行します。

    ![移行プロセス](./media/contoso-migration-rehost-linux-vm-mysql/migration-process.png)

### <a name="azure-services"></a>Azure サービス

**サービス** | **説明** | **コスト**
--- | --- | ---
[Azure Migrate](https://docs.microsoft.com/azure/migrate/migrate-services-overview) | Contoso は、Azure Migrate サービスを使用して VMware VM を評価します。 Azure Migrate は、マシンの移行適合性を評価します。 そのうえで、Azure で実行するための、サイズとコストの見積もりを提供します。 | [Azure Migrate](https://azure.microsoft.com/pricing/details/azure-migrate) は追加料金なしでご利用いただけますが、評価と移行に使用するツール (ファースト パーティまたは ISV) によっては料金が発生する場合があります。
[Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview) | Azure Database Migration Service を使用すると、複数のデータベース ソースから Azure データ プラットフォームに、ダウンタイムを最小限に抑えながらシームレスに移行できます。 | [サポートされているリージョン](https://docs.microsoft.com/azure/dms/dms-overview#regional-availability)に関する情報と、[Database Migration Service の価格](https://azure.microsoft.com/pricing/details/database-migration)に関する情報をご覧ください。
[Azure Database for MySQL](https://docs.microsoft.com/azure/mysql) | データベースは、オープン ソースの MySQL Server エンジンに基づいています。 これは、アプリの開発とデプロイ向けのフルマネージドのエンタープライズ対応コミュニティ MySQL データベースです。 | Azure Database for MySQL の[価格](https://azure.microsoft.com/pricing/details/mysql)とスケーラビリティのオプションに関する情報をご覧ください。

## <a name="prerequisites"></a>前提条件

このシナリオで Contoso に必要なことを以下に示します。

<!-- markdownlint-disable MD033 -->

**必要条件** | **詳細**
--- | ---
**Azure サブスクリプション** | Contoso は前の記事でサブスクリプションを作成しました。 Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/pricing/free-trial)を作成してください。 <br><br> 無料アカウントを作成する場合、サブスクリプションの管理者としてすべてのアクションを実行できます。 <br><br> 既存のサブスクリプションを使用しており、管理者でない場合は、管理者に依頼して所有者アクセス許可または共同作成者アクセス許可を割り当ててもらう必要があります。 <br><br> さらに詳細なアクセス許可が必要な場合は、[こちらの記事](https://docs.microsoft.com/azure/site-recovery/site-recovery-role-based-linked-access-control)をご覧ください。
**Azure インフラストラクチャ** | Contoso は、[移行のための Azure インフラストラクチャ](./contoso-migration-infrastructure.md)についての記事で説明されているように、Azure インフラストラクチャを設定します。
**オンプレミスのサーバー** | オンプレミス vCenter Server は、バージョン 5.5、6.0、6.5、または 6.7 を実行している必要があります。 <br><br> バージョン 5.5、6.0、6.5、または 6.7 を実行している ESXi ホスト。 <br><br> ESXi ホスト上で実行している 1 つ以上の VMware VM。
**オンプレミスの VM** | Azure での実行が保証されている [Linux マシンを確認します](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)。

<!-- markdownlint-enable MD033 -->

## <a name="scenario-steps"></a>シナリオのステップ

Contoso 管理者は、次のようにして移行を完了します。

> [!div class="checklist"]
>
> - **ステップ 1:Azure Migrate: Server Migration 用の Azure を準備する。** Azure Migrate プロジェクトに Server Migration ツールを追加します。
> - **手順 2:Azure Migrate: Server Migration 用のオンプレミス VMware を準備する。** VM 検出用のアカウントを準備し、移行後に Azure VM に接続するための準備をします。
> - **ステップ 3:VM をレプリケートする。** レプリケーションを設定し、Azure Storage への VM のレプリケーションを開始します。
> - **手順 4:Azure Migrate: Server Migration でアプリの VM を移行する。** テスト移行を実行してすべてが機能していることを確認した後、完全移行を実行して VM を Azure に移動します。
> - **手順 5:データベースを移行する。** Azure Data Migration Service (DMS) を使用して移行を設定します。

## <a name="step-1-prepare-azure-for-the-azure-migrate-server-migration-tool"></a>手順 1:Azure Migrate: Server Migration ツール用の Azure を準備するServer Migration ツール

Contoso が VM を Azure に移行するには、以下の Azure コンポーネントが必要です。

- 移行中に作成される Azure VM が配置される VNet。
- Azure Migrate: Server Migration ツール (OVA) (プロビジョニングおよび構成済みのもの)。

これらを次のように設定します。

1. ネットワークを設定します - Contoso は、[Azure インフラストラクチャのデプロイ](./contoso-migration-infrastructure.md)を行ったときに、Azure Migrate: Server Migration に使用できるネットワークを既にセットアップしています

2. Azure Migrate: Server Migration ツールをプロビジョニングしますサーバー移行ツール。

    - Azure Migrate から、OVA イメージをダウンロードし、VMware にインポートします。

        ![OVA ファイルをダウンロードする](./media/contoso-migration-rehost-vm/migration-download-ova.png)

    - インポートしたイメージを起動し、次のステップを含めてツールを構成します。

      - 前提条件を設定します。

        ![ツールを構成する](./media/contoso-migration-rehost-vm/migration-setup-prerequisites.png)

      - ツールを Azure サブスクリプションにポイントします。

        ![ツールを構成する](./media/contoso-migration-rehost-vm/migration-register-azure.png)

      - VMware vCenter の資格情報を設定します。

        ![ツールを構成する](./media/contoso-migration-rehost-vm/migration-vcenter-server.png)

      - 検出用の Linux ベースの資格情報を追加します。

        ![ツールを構成する](./media/contoso-migration-rehost-vm/migration-credentials.png)

3. 構成の完了後、ツールによってすべての仮想マシンが列挙されるまでに時間がかかることがあります。 完了すると、Azure の Azure Migrate ツールにそれらの情報が入力されていることがわかります。

**さらにサポートが必要な場合**

[Azure Migrate: Server Migration ツール](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-server-migration-tool)の設定についてご確認ください。

## <a name="step-2-prepare-on-premises-vmware-for-azure-migrate-server-migration"></a>手順 2:Azure Migrate: Server Migration 用のオンプレミス VMware を準備するServer Migration

Contoso は、Azure への移行後に、Azure 内のレプリケートされた VM に接続できることを望んでいます。 これを行うには、Contoso の管理者がいくつかのことを実行する必要があります。

- Azure VM にアクセスするには、移行前に、オンプレミス Linux VM 上で SSH を有効にします。 Ubuntu の場合、これは `sudo apt-get ssh install -y` コマンドを使用して完了させることができます。

- 移行を実行したら、**ブート診断**を調べて、VM のスクリーンショットを表示できます。

- これが機能しない場合は、その VM が実行中であることを確認し、これらの[トラブルシューティングのヒント](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)を見直してください。

- [Azure Linux エージェント](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux)をインストールします。

**さらにサポートが必要な場合**

- [移行用の VM の準備](https://docs.microsoft.com/azure/migrate/contoso-migration-rehost-linux-vm#prepare-vms-for-migration)についてご確認ください。

## <a name="step-3-replicate-vm"></a>手順 3:VM のレプリケート

Contoso の管理者は、Azure への移行を実行する前に、レプリケーションを設定して有効にする必要があります。

検出が完了したら、Azure へのアプリ VM のレプリケーションを開始できます。

1. Azure Migrate プロジェクトの **[サーバー]** の **[Azure Migrate: Server Migration]** で、 **[レプリケート]** をクリックします。

    ![VM をレプリケートする](./media/contoso-migration-rehost-linux-vm/select-replicate.png)

2. **[レプリケート]** > **[ソースの設定]**  >  **[マシンは仮想化されていますか?]** で、 **[はい (VMware vSphere の場合)]** を選択します。

3. **[オンプレミスのアプライアンス]** で、自分が設定した Azure Migrate アプライアンスの名前、 **[OK]** の順に選択します。

    ![ソースの設定](./media/contoso-migration-rehost-linux-vm/source-settings.png)

4. **[仮想マシン]** で、レプリケートしたいマシンを選択します。
    - VM の評価を実行した場合は、評価結果から VM のサイズ設定とディスクの種類 (Premium または Standard) の推奨事項を適用できます。 これを行うには、 **[Azure Migrate Assessment から移行設定をインポートしますか?]** で **[はい]** オプションを選択します。
    - 評価を実行しなかった場合、または評価の設定を使用しない場合は、 **[いいえ]** オプションを選択します。
    - 評価の使用を選択した場合は、VM グループと評価名を選択します。

    ![評価の選択](./media/contoso-migration-rehost-linux-vm/select-assessment.png)

5. **[仮想マシン]** で、必要に応じて VM を検索し、移行したい各 VM を確認します。 その後、 **[次へ:ターゲット設定]** をクリックします。

6. **[ターゲット設定]** で、サブスクリプションと、移行先となるターゲット リージョンを選択し、移行後に Azure VM が配置されるリソース グループを指定します。 **[仮想ネットワーク]** で、移行後に Azure VM の参加先となる Azure VNet およびサブネットを選択します。

7. **[Azure ハイブリッド特典]** で、次のように選択します。

    - Azure ハイブリッド特典を適用しない場合は、 **[いいえ]** を選択します。 続けて、 **[次へ]** をクリックします。

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

## <a name="step-4-migrate-the-vm-with-azure-migrate-server-migration"></a>手順 4:Azure Migrate: Server Migration で VM を移行するServer Migration

Contoso の管理者は、クイックテスト移行を実行し、その後、完全な移行を実行して web VM を移動します。

### <a name="run-a-test-migration"></a>テスト移行を実行する

1. **[移行の目標]**  >  **[サーバー]**  >  **[Azure Migrate: Server Migration]** で、 **[移行されたサーバーをテストします]** をクリックします。

     ![移行されたサーバーのテスト](./media/contoso-migration-rehost-linux-vm/test-migrated-servers.png)

2. テストする VM を右クリックし、 **[テスト移行]** をクリックします。

    ![テスト移行](./media/contoso-migration-rehost-linux-vm/test-migrate.png)

3. **[テスト移行]** で、移行後に Azure VM が配置される Azure VNet を選択します。 非運用環境の VNet を使用することをお勧めします。
4. **テスト移行**ジョブが開始されます。 ポータルの通知でジョブを監視します。
5. 移行の完了後、Azure portal の **[仮想マシン]** で、移行された Azure VM を確認します。 マシン名には、サフィックス **-Test** が含まれています。
6. テストが完了したら、 **[マシンのレプリケート]** で Azure VM を右クリックし、 **[テスト移行をクリーンアップ]** をクリックします。

    ![移行のクリーンアップ](./media/contoso-migration-rehost-linux-vm/clean-up.png)

### <a name="migrate-the-vm"></a>VM の移行

Contoso の管理者は、ここで、移動を完了する完全移行を実行します。

1. Azure Migrate プロジェクトの **[サーバー]**  >  **[Azure Migrate: Server Migration]** で、 **[サーバーをレプリケートしています]** をクリックします。

    ![サーバーをレプリケートしています](./media/contoso-migration-rehost-linux-vm/replicating-servers.png)

2. **[マシンのレプリケート]** で VM を右クリックし、 **[移行]** を選択します。
3. **[移行]**  >  **[仮想マシンをシャットダウンし、データ損失のない計画された移行を実行しますか]** で、 **[はい]**  >  **[OK]** の順に選択します。
    - 既定では、Azure Migrate によってオンプレミスの VM がシャットダウンされ、前回のレプリケーションが発生した後に発生したすべての VM の変更を同期するためにオンデマンド レプリケーションが実行されます。 こうすることで、データ損失がなくなります。
    - VM をシャットダウンしたくない場合は、 **[いいえ]** を選択します。
4. VM に対して移行ジョブが開始されます。 Azure 通知でジョブを追跡します。
5. ジョブが完了したら、 **[仮想マシン]** ページで VM を表示して管理できます。

## <a name="step-5-provision-azure-database-for-mysql"></a>手順 5:Azure Database for MySQL をプロビジョニングする

Contoso 管理者は、MySQL データベース インスタンスを、プライマリの米国東部 2 リージョン内にプロビジョニングします。

1. Azure Portal で、Azure Database for MySQL のリソースを作成します。

    ![MySQL](./media/contoso-migration-rehost-linux-vm-mysql/mysql-1.png)

2. Azure データベースの名前 **contosoosticket** を追加します。 運用リソース グループ **ContosoRG** にデータベースを追加し、そのための資格情報を指定します。
3. オンプレミスの MySQL データベースはバージョン 5.7 なので、互換性のためにこのバージョンが選択されています。 既定のサイズを使用しますが、それらはデータベースの要件に適合しています。

     ![MySQL](./media/contoso-migration-rehost-linux-vm-mysql/mysql-2.png)

4. **バックアップ冗長オプション**については、**Geo 冗長**を使用することを選択しています。 このオプションでは、停止が発生した場合、セカンダリの米国中部リージョンでデータベースを復元できます。 このオプションは、データベースをプロビジョニングするときにのみ構成できます。

     ![冗長性](./media/contoso-migration-rehost-linux-vm-mysql/db-redundancy.png)

5. **VNET-PROD-EUS2** ネットワークの **サービス エンドポイント**で、SQL サービスのサービス エンドポイント (データベース サブネット) を追加します。

    ![MySQL](./media/contoso-migration-rehost-linux-vm-mysql/mysql-3.png)

6. サブネットの追加後に、運用ネットワーク内のデータベース サブネットからのアクセスを許可する仮想ネットワーク規則を作成します。

    ![MySQL](./media/contoso-migration-rehost-linux-vm-mysql/mysql-4.png)

## <a name="step-6-migrate-the-database"></a>手順 6:データベースを移行する

MySQL データベースは、次の方法で移動できます。 それぞれで、ターゲットに対して Azure DB for MySQL インスタンスを作成する必要があります。 作成したら、次の 2 つのパスを使用して移行を実行できます。

- 6a:Azure Database Migration Service
- 6b:MySQL Workbench のバックアップと復元

### <a name="step-6a-migrate-the-database-azure-database-migration-service"></a>手順 6a:データベースの移行 (Azure Database Migration Service)

Contoso の管理者は、[ステップバイステップの移行チュートリアル](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online)を使い、Azure Database Migration Service を使用してデータベースを移行します。 MySQL 5.6 または 5.7 を使用して、オンライン、オフライン、およびハイブリッド (プレビュー) の両方の移行を実行できます。

> [!NOTE]
> MySQL 8.0 は Azure Database for MySQL でサポートされていますが、DMS ツールはまだそのバージョンをサポートしていません。

まとめると、次を実行する必要があります。

- 次のすべての移行の前提条件が満たされていることを確認します。

  - MySQL サーバーのソースは、Azure Database for MySQL でサポートされているバージョンと一致する必要があります。 Azure Database for MySQL は、MySQL Community Edition と InnoDB エンジンをサポートし、同じバージョンのソースとターゲット間の移行をサポートしています。
  - my.ini (Windows) または my.cnf (Unix) のバイナリ ログを有効にします。 この操作を行わないと、移行ウィザードの実行中に `Error in binary logging. Variable binlog_row_image has value 'minimal'. Please change it to 'full. For more information, see https://go.microsoft.com/fwlink/?linkid=873009` エラーが発生します。
  - ユーザーは `ReplicationAdmin` ロールを持っている必要があります。
  - 外部キーとトリガーを使用せずにデータベース スキーマを移行します。

- ExpressRoute または VPN を介してオンプレミス ネットワークに接続する仮想ネットワークを作成します。

- VNet に接続されている `Premium` SKU を持つ Azure Database Migration Service を作成します。

- Azure Database Migration Service が仮想ネットワーク経由で MySQL データベースにアクセスできることを確認します。 これには、仮想ネットワーク レベル、ネットワーク VPN、および MySQL をホストするマシンで、 Azure から MySQL にすべての受信ポートが許可されていることの確認が伴います。

- Azure Database Migration Service ツールを実行する:

  - 移行プロジェクトを作成します。

    ![MySQL](./media/contoso-migration-rehost-linux-vm-mysql/migration-dms-new-project.png)

    ![MySQL](./media/contoso-migration-rehost-linux-vm-mysql/migration-dms-new-project-02.png)

  - ソース (オンプレミス データベース) を追加します。

    ![MySQL](./media/contoso-migration-rehost-linux-vm-mysql/migration-dms-source.png)

  - ターゲットを選択します。

    ![MySQL](./media/contoso-migration-rehost-linux-vm-mysql/migration-dms-target.png)

  - 移行するデータベースを選択します。

    ![MySQL](./media/contoso-migration-rehost-linux-vm-mysql/migration-dms-databases.png)

  - 詳細設定を構成します。

    ![MySQL](./media/contoso-migration-rehost-linux-vm-mysql/migration-dms-settings.png)

  - レプリケーションを開始し、エラーを解決します。

    ![MySQL](./media/contoso-migration-rehost-linux-vm-mysql/migration-dms-monitor.png)

  - 最終的なカットオーバーを実行します。
  
    ![MySQL](./media/contoso-migration-rehost-linux-vm-mysql/migration-dms-cutover.png)

    ![MySQL](./media/contoso-migration-rehost-linux-vm-mysql/migration-dms-cutover-complete.png)

    ![MySQL](./media/contoso-migration-rehost-linux-vm-mysql/migration-dms-cutover-complete-02.png)
  
  - 外部キーとトリガーをすべて復帰させます。

  - 新しいデータベースを使用するようにアプリケーションを変更します。

    ![MySQL](./media/contoso-migration-rehost-linux-vm-mysql/migration-dms-cutover-apps.png)

### <a name="step-6b-migrate-the-database-mysql-workbench"></a>手順 6b:データベースの移行 (MySQL Workbench)

Contoso 管理者は、MySQL ツールでバックアップと復元を使用して、データベースを移行します。 MySQL Workbench をインストールし、OSTICKETMYSQL からデータベースをバックアップして、Azure Database for MySQL Server に復元します。

### <a name="install-mysql-workbench"></a>MySQL Workbench のインストール

1. [MySQL Workbench の前提条件を確認してダウンロード](https://dev.mysql.com/downloads/workbench/?utm_source=tuicool)します。

2. [インストールの指示](https://dev.mysql.com/doc/workbench/en/wb-installing.html)に従って、MySQL Workbench for Windows をインストールします。

3. MySQL Workbench で、OSTICKETMYSQL への MySQL 接続を作成します。

    ![MySQL Workbench](./media/contoso-migration-rehost-linux-vm-mysql/workbench1.png)

4. データベースを **osticket** として、ローカルの自己完結型ファイルにエクスポートします。

    ![MySQL Workbench](./media/contoso-migration-rehost-linux-vm-mysql/workbench2.png)

5. データベースがローカルにバックアップされたら、Azure Database for MySQL インスタンスへの接続を作成します。

    ![MySQL Workbench](./media/contoso-migration-rehost-linux-vm-mysql/workbench3.png)

6. これで、自己完結型ファイルから、Azure Database for MySQL インスタンス内にあるデータベースをインポート (復元) できます。 このインスタンスのために新しいスキーマ (osticket) が作成されます。

    ![MySQL Workbench](./media/contoso-migration-rehost-linux-vm-mysql/workbench4.png)

### <a name="connect-the-vm-to-the-database"></a>VM をデータベースに接続する

移行プロセスの最終ステップとして、Contoso の管理者は、**OSTICKETMYSQL** VM で実行されているアプリ データベースを指すようにアプリケーションの接続文字列を更新します。

1. Putty または別の SSH クライアントを使用して、**OSTICKETWEB** VM への SSH 接続を作成します。 この VM はプライベートなので、プライベート IP アドレスを使用して接続します。

    ![データベースへの接続](./media/contoso-migration-rehost-linux-vm/db-connect.png)

    ![データベースへの接続](./media/contoso-migration-rehost-linux-vm/db-connect2.png)

2. **OSTICKETWEB** VM が **OSTICKETMYSQL** VM と通信できることを確認する必要があります。 構成は、この時点で、オンプレミスの IP アドレス 172.16.0.43 を使用してハードコードされています。

    **更新の前:**

    ![IP の更新](./media/contoso-migration-rehost-linux-vm/update-ip1.png)

    **更新の後:**

    ![IP の更新](./media/contoso-migration-rehost-linux-vm/update-ip2.png)

3. `systemctl restart apache2` でサービスを再起動します。

    ![Restart](./media/contoso-migration-rehost-linux-vm/restart.png)

4. 最後に、いずれかの Contoso ドメイン コント ローラー上で、**OSTICKETWEB** と **OSTICKETMYSQL** の DNS レコードを更新します。

    ![DNS の更新](./media/contoso-migration-rehost-linux-vm-mysql/update-dns.png)

    ![DNS の更新](./media/contoso-migration-rehost-linux-vm-mysql/update-dns.png)

**さらにサポートが必要な場合**

- [テスト移行の実行](https://docs.microsoft.com/azure/migrate/tutorial-migrate-vmware#run-a-test-migration)に関する説明を参照します。
- [VM の Azure への移行](https://docs.microsoft.com/azure/migrate/tutorial-migrate-vmware#migrate-vms)に関する説明を参照します。

## <a name="review-the-deployment"></a>デプロイを再調査する

アプリが実行されるようになり、Contoso は新しいインフラストラクチャを完全に操作可能にして、セキュリティで保護する必要があります。

## <a name="clean-up-after-migration"></a>移行後にクリーンアップする

移行が完了すると、Azure VM で osTicket のアプリ層が実行されています。

ここで、以下のことを行う必要があります。

- VMware VM を vCenter インベントリから削除します。
- ローカルのバックアップ ジョブからからオンプレミスの VM を削除します。
- 社内のドキュメントを更新して、新しい場所と IP アドレスを示します。
- こうしたオンプレミスの VM と対話しているリソースがないか確認し、新しい構成が反映されるように、関連する設定やドキュメントをすべて更新します。
- Contoso では、依存関係のマッピングを指定して Azure Migrate サービスを使用し、移行のための **OSTICKETWEB** VM を評価しました。

### <a name="security"></a>Security

Contoso のセキュリティ チームは VM とデータベースを再調査して、セキュリティの問題を特定します。

- VM がアクセスを制御するためのネットワーク セキュリティ グループ (NSG) を見直します。 NSG は、アプリケーションに対して許可されるトラフィックのみが通過できるようにするために使用されます。
- ディスクの暗号化と Azure Key Vault を使用した、VM ディスク上のデータのセキュリティ保護を検討します。
- VM とデータベース インスタンス間の通信は、SSL 用に構成されていません。 データベース トラフィックをハッキングできないようにするため、これを行う必要があります。

詳細については、「[Azure における IaaS ワークロードのセキュリティに関するベスト プラクティス](https://docs.microsoft.com/azure/security/fundamentals/iaas)」を参照してください。

### <a name="bcdr"></a>BCDR

事業継続とディザスター リカバリーのために、Contoso は次のアクションを実施します。

- **データの安全性を確保する。** Contoso は、[Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview) サービスを使用して、アプリの VM 上のデータをバックアップします。 データベースのバックアップを構成する必要はありません。 Azure Database for MySQL は、サーバーのバックアップを自動的に作成して保存します。 彼らはデータベースのために geo 冗長性を使用することを選択したため、このデータベースは耐障害性があり、運用準備ができています。

- **アプリの稼働状態を維持する。** Contoso は、Site Recovery を使用して、Azure 内のアプリの VM をセカンダリ リージョンにレプリケートします。 詳細については、「[クイック スタート: Azure VM のセカンダリ Azure リージョンへのディザスター リカバリーの設定](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-quickstart)」を参照してください。

### <a name="licensing-and-cost-optimization"></a>ライセンスとコストの最適化

- Contoso は、リソースのデプロイ後、[Azure インフラストラクチャ](./contoso-migration-infrastructure.md#set-up-tagging)のデプロイ中に下した決定に従って Azure タグを割り当てます。
- Contoso Ubuntu サーバーにライセンスの問題はありません。
- Contoso は [Azure Cost Management](https://azure.microsoft.com/services/cost-management) を使用して、IT リーダーが定めた予算内に確実に収まるようにします。
