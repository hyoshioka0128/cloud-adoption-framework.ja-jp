---
title: Linux サービス デスクトップ アプリを Azure と Azure Database for MySQL にリホストする
titleSuffix: Microsoft Cloud Adoption Framework for Azure
description: Contoso が、オンプレミスの Linux アプリを Azure VM と Azure Database for MySQL に移行することによって、どのようにこのアプリをリホストするかを説明します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: migrate
ms.openlocfilehash: a2a695af758ae7e99a7c2257f3adf4ce5058ae3d
ms.sourcegitcommit: 50788e12bb744dd44da14184b3e884f9bddab828
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/18/2019
ms.locfileid: "74160329"
---
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

- アプリは 2 つの VM (`OSTICKETWEB` と `OSTICKETMYSQL`) に階層化されています。
- これらの VM は、VMware ESXi ホスト `contosohost1.contoso.com` (バージョン 6.5) 上に配置されています。
- VMware 環境は、VM で実行中のvCenter Server 6.5 (`vcenter.contoso.com`) によって管理されています。
- Contoso には、オンプレミスのデータセンター (`contoso-datacenter`) があり、オンプレミスのドメイン コントローラー (`contosodc1`) が含まれています。
- `OSTICKETWEB` 上の Web 階層アプリは、Azure IaaS VM に移行されます。
- アプリのデータベースは、Azure Database for MySQL の PaaS サービスに移行されます。
- Contoso は運用ワークロードを移行しようとしているため、リソースは、運用リソース グループ `ContosoRG` 内に存在することになります。
- リソースはプライマリ リージョン (米国東部 2) にレプリケートされ、運用ネットワーク (`VNET-PROD-EUS2`) 内に配置されます。
  - Web VM は、フロントエンド サブネット (`PROD-FE-EUS2`) 内に存在することになります。
  - データベース インスタンスは、データベース サブネット (`PROD-DB-EUS2`) 内に存在することになります。
- アプリのデータベースは、MySQL ツールを使用して Azure Database for MySQL に移行されます。
- Contoso データセンター内のオンプレミス VM は、移行が行われた後に使用停止にされます。

![シナリオのアーキテクチャ](./media/contoso-migration-rehost-linux-vm-mysql/architecture.png)

## <a name="migration-process"></a>移行プロセス

Contoso は、次のようにして移行プロセスを完了します。

Web VM を移行するには:

1. 最初の手順として、Contoso は、Site Recovery をデプロイするために必要な Azure とオンプレミスのインフラストラクチャを設定します。
2. Azure とオンプレミスのコンポーネントを準備したら、Web VM のレプリケーションを設定し、有効にします。
3. レプリケーションが有効になり、実行中となったら、Contoso は Azure へのフェールオーバーによって VM を移行します。

データベースを移行するには:

1. Contoso は、Azure で MySQL インスタンスをプロビジョニングします。
2. Contoso は MySQL ワークベンチを設定し、データベースはローカルでバックアップします。
3. その後、Contoso はローカルのバックアップからデータベースを Azure に復元します。

![移行プロセス](./media/contoso-migration-rehost-linux-vm-mysql/migration-process.png)

### <a name="azure-services"></a>Azure サービス

**サービス** | **説明** | **コスト**
--- | --- | ---
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery) | このサービスは、Azure VM、オンプレミス VM、物理サーバーの移行とディザスター リカバリーを調整および管理します。 | Azure へのレプリケーションの間に、Azure Storage の料金が発生します。 フェールオーバーが発生すると、Azure VM が作成されて料金がかかります。 料金と価格について[詳しくはこちら](https://azure.microsoft.com/pricing/details/site-recovery)をご覧ください。
[Azure Database for MySQL](https://docs.microsoft.com/azure/mysql) | データベースは、オープン ソースの MySQL Server エンジンに基づいています。 これは、アプリの開発とデプロイに向けたサービスとしての、フルマネージドのエンタープライズ対応コミュニティ MySQL データベースです。

## <a name="prerequisites"></a>前提条件

このシナリオで Contoso に必要なことを以下に示します。

<!-- markdownlint-disable MD033 -->

**要件** | **詳細**
--- | ---
**Azure サブスクリプション** | Contoso は前の記事でサブスクリプションを作成しました。 Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/pricing/free-trial)を作成してください。<br/><br/> 無料アカウントを作成する場合、サブスクリプションの管理者としてすべてのアクションを実行できます。<br/><br/> 既存のサブスクリプションを使用しており、管理者でない場合は、管理者に依頼して所有者アクセス許可または共同作成者アクセス許可を割り当ててもらう必要があります。<br/><br/> さらに詳細なアクセス許可が必要な場合は、[こちらの記事](https://docs.microsoft.com/azure/site-recovery/site-recovery-role-based-linked-access-control)をご覧ください。
**Azure インフラストラクチャ** | Contoso は、[移行のための Azure インフラストラクチャ](./contoso-migration-infrastructure.md)についての記事で説明されているように、Azure インフラストラクチャを設定します。<br/><br/> Site Recovery 用の[ネットワーク](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network)と[ストレージ](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage)の具体的な要件の詳細を確認してください。
**オンプレミスのサーバー** | オンプレミス vCenter Server は、バージョン 5.5、6.0、または 6.5 を実行している必要があります<br/><br/> バージョン 5.5、6.0、または 6.5 を実行している ESXi ホスト<br/><br/> ESXi ホスト上で実行している 1 つ以上の VMware VM。
**オンプレミスの VM** | Site Recovery を使用した移行がサポートされる [Linux VM の要件を確認](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#replicated-machines)します。<br/><br/> サポートされる [Linux のファイル システムとストレージ システム](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#linux-file-systemsguest-storage)を確認します。<br/><br/> VM は [Azure の要件](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements)を満たす必要があります。

<!-- markdownlint-enable MD033 -->

## <a name="scenario-steps"></a>シナリオのステップ

Contoso 管理者は、次のようにして移行を完了します。

> [!div class="checklist"]
>
> - **手順 1:Site Recovery のために Azure を準備する。** レプリケートされたデータを保持するための Azure ストレージ アカウントを作成し、Recovery Services コンテナーを作成します。
> - **手順 2:Site Recovery のためにオンプレミスの VMware を準備する。** フェールオーバー後に Azure VM に接続するために、VM 検出およびエージェント インストール用のアカウントを準備します。
> - **手順 3:データベースをプロビジョニングする。** Azure で、Azure Database for MySQL のインスタンスをプロビジョニングします。
> - **手順 4:VM をレプリケートする。** Site Recovery のソース環境とターゲット環境を構成し、レプリケーション ポリシーを設定して、Azure のストレージへの VM のレプリケーションを開始します。
> - **手順 5:データベースを移行する。** MySQL ツールを使用して移行を設定します。
> - **手順 6:Site Recovery を使用して VM を移行する。** 最後に、テスト フェールオーバーを実行してすべてが機能していることを確認した後、完全フェールオーバーを実行して VM を Azure に移行します。

## <a name="step-1-prepare-azure-for-the-site-recovery-service"></a>手順 1:Site Recovery サービス用に Azure を準備する

Site Recovery のために、Contoso には数個の Azure コンポーネントが必要です。

- フェールオーバーされるリソースが配置された VNet。 Contoso は、[Azure インフラストラクチャのデプロイ](./contoso-migration-infrastructure.md)時に既に VNet を作成しています。
- レプリケートされたデータを保持する Azure の新しいストレージ アカウント。
- Azure 内の Recovery Services コンテナー。

Contoso 管理者は、次のようにストレージ アカウントとコンテナーを作成します。

1. 米国東部 2 リージョンの中にストレージ アカウント (**contosovmsacc20180528**) を作成します。

    - ストレージ アカウントは、Recovery Services コンテナーと同じリージョンに存在する必要があります。
    - Standard ストレージと LRS レプリケーション付きの汎用アカウントを使用します。

    ![Site Recovery ストレージ](./media/contoso-migration-rehost-linux-vm-mysql/asr-storage.png)

2. ネットワークとストレージ アカウントを準備したら、コンテナー (ContosoMigrationVault) を作成し、それをプライマリの米国東部 2 リージョン内の **ContosoFailoverRG** リソース グループ内に配置します。

    ![Recovery Services コンテナー](./media/contoso-migration-rehost-linux-vm-mysql/asr-vault.png)

**さらにサポートが必要な場合**

Site Recovery のために Azure を設定することについての[説明を参照します](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure)。

## <a name="step-2-prepare-on-premises-vmware-for-site-recovery"></a>手順 2: Site Recovery のためにオンプレミスの VMware を準備する

Contoso の管理者は、次のようにオンプレミスの VMware インフラストラクチャを準備します。

- VM の検出を自動化するために、vCenter サーバー上にアカウントを作成します。
- レプリケートする VMware VM 上にモビリティ サービスを自動インストールできるアカウントを作成します。
- オンプレミスの VM を準備して、それらの VM が、移行後に Azure VM が作成されたらそこへ接続できるようにします。

### <a name="prepare-an-account-for-automatic-discovery"></a>自動検出用のアカウントを準備する

Site Recovery では、次のことを実行するために、VMware サーバーへのアクセスが必要です。

- VM を自動的に検出します。 少なくとも読み取り専用のアカウントが必要です。
- レプリケーション、フェールオーバー、およびフェールバックを調整します。 ディスクを作成および削除する、VM の電源をオンにするなどの操作を実行できるアカウントが必要です。

Contoso の管理者は、アカウントを以下のように設定します。

1. vCenter レベルでロールを作成します。
2. そのロールに必要なアクセス許可を割り当てます。

### <a name="prepare-an-account-for-mobility-service-installation"></a>モビリティ サービスのインストール用のアカウントを準備する

モビリティ サービスは、Contoso が移行しようとしてうる各 VM にインストールする必要があります。

- VM のレプリケーションが有効になっているときには、Site Recovery はこのコンポーネントの自動プッシュ インストールを行うことができます。
- 自動インストールの場合。 Site Recovery には、VM にアクセスするアクセス許可を持っているアカウントが必要です。
- アカウントの詳細が、レプリケーションのセットアップ中の入力です。
- このアカウントには、インストールのアクセス許可を持っていれば、ドメインまたはローカルのアカウントを指定できます。

### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>フェールオーバー後に Azure VM に接続するための準備をする

Contoso は、Azure へのフェールオーバー後に、Azure VM に接続できることを望んでいます。 そのため、Contoso 管理者は次の手順を実行する必要があります。

- インターネット経由でアクセスするには、移行前に、オンプレミス Linux VM 上で SSH を有効にします。 Ubuntu の場合、これは次のコマンドを使用して完了させることができます: **Sudo apt-get ssh install -y**。
- フェールオーバー後、**ブート診断**を調べて VM のスクリーンショットを確認する必要があります。
- これが機能しない場合は、その VM が実行中であることを確認し、これらの[トラブルシューティングのヒント](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)を見直す必要があります。

**さらにサポートが必要な場合**

- 自動検出のためにロールを作成して割り当てることについての[説明を参照します](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery)。
- モビリティ サービスのプッシュ インストールのためにアカウントを作成することについての[説明を参照します](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation)。

## <a name="step-3-provision-azure-database-for-mysql"></a>手順 3:Azure Database for MySQL をプロビジョニングする

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

## <a name="step-4-replicate-the-on-premises-vms"></a>手順 4:オンプレミスの VM をレプリケートする

Web VM を Azure に移行する前に、Contoso の管理者はレプリケーションを設定し、有効にします。

### <a name="set-a-protection-goal"></a>保護の目標を設定する

1. コンテナー内のコンテナー名 (ContosoVMVault) の下で、レプリケーションの目標を設定します ( **[作業の開始]**  >  **[Site Recovery]**  >  **[Prepare infrastructure]** (インフラストラクチャの準備))。
2. マシンがオンプレミスに配置されていること、それらのマシンは VMware VM であること、およびレプリケート先は Azure にすることを指定します。

    ![レプリケーションの目標](./media/contoso-migration-rehost-linux-vm-mysql/replication-goal.png)

### <a name="confirm-deployment-planning"></a>展開の計画を確認する

続行するには、 **[Yes, I have done it]** (はい、完了しました) を選択して、デプロイ計画が完了したことを確認します。 このシナリオで Contoso が移行するのは 1 つの VM だけなので、デプロイ計画は不要です。

### <a name="set-up-the-source-environment"></a>ソース環境をセットアップする

Contoso 管理者は次にソース環境を構成します。 これを行うには、OVF テンプレートを使用して、Site Recovery 構成サーバーを高可用性オンプレミス VMware VM としてデプロイします。 構成サーバーを起動して実行したら、それをコンテナーに登録します。

構成サーバーでは、いくつかのコンポーネントが実行されます。

- オンプレミスと Azure の間の通信を調整し、データのレプリケーションを管理する構成サーバー コンポーネント。
- プロセス サーバー: レプリケーション ゲートウェイとして機能します。 レプリケーション データを受信し、そのデータをキャッシュ、圧縮、暗号化によって最適化して、Azure Storage に送信します。
- また、プロセス サーバーは、レプリケートする VM へのモビリティ サービスのインストールや、オンプレミスの VMware VM の自動検出も行います。

Contoso 管理者は次の手順でこれを実行します。

1. OVF テンプレートを **[Prepare Infrastructure]** (インフラストラクチャの準備) >  **[ソース]**  >  **[構成サーバー]** からダウンロードします。

    ![OVF をダウンロードする](./media/contoso-migration-rehost-linux-vm-mysql/add-cs.png)

2. VMware にテンプレートをインポートして VM を作成し、その VM をデプロイします。

    ![OVF テンプレート](./media/contoso-migration-rehost-linux-vm-mysql/vcenter-wizard.png)

3. 初めて VM をオンにすると、Windows Server 2016 インストール環境が起動します。 使用許諾契約書に同意し、管理者パスワードを入力します。
4. インストールの完了後に、管理者として VM にサインインします。 初めてサインインしたときは、Azure Site Recovery 構成ツールが既定で実行されます。
5. このツールでは、構成サーバーをコンテナーに登録するために使用する名前を指定します。
6. このツールは、VM が Azure に接続できることを確認します。
7. 接続が確立されたら、Azure サブスクリプションにサインインします。 資格情報は、構成サーバーを登録するコンテナーにアクセスできる必要があります。

    ![構成サーバーの登録](./media/contoso-migration-rehost-linux-vm-mysql/config-server-register2.png)

8. ツールがいくつかの構成タスクを実行した後、再起動されます。
9. マシンに再度サインインすると、構成サーバーの管理ウィザードが自動的に起動されます。
10. ウィザードで、レプリケーション トラフィックを受信する NIC を選択します。 この設定は、構成後に変更することはできません。
11. サブスクリプション、リソース グループ、および構成サーバーを登録するコンテナーを選択します。

    ![Recovery Services コンテナーの選択](./media/contoso-migration-rehost-linux-vm-mysql/cswiz1.png)

12. ここで、MySQL Server と VMware PowerCLI をダウンロードしてインストールします。
13. 検証が終わったら、vCenter サーバーまたは vSphere ホストの FQDN または IP アドレスを指定します。 ポートは既定のままにして、vCenter サーバーには、わかりやすい名前を指定します。
14. 自動検出のために作成したアカウントと、モビリティ サービスを自動的にインストールするために Site Recovery が使用する資格情報を入力します。

    ![vCenter](./media/contoso-migration-rehost-linux-vm-mysql/cswiz2.png)

15. 登録が完了したら、Azure portal で、構成サーバーと VMware サーバーがコンテナーの **[ソース]** ページの一覧に表示されていることを確認します。 検出には 15 分以上かかる場合があります。
16. すべての準備ができると、Site Recovery は VMware サーバーに接続し、VM を検出します。

### <a name="set-up-the-target"></a>ターゲットをセットアップする

次に、Contoso の管理者は、ターゲットのレプリケーション設定を入力します。

1. **[インフラストラクチャの準備]** (インフラストラクチャの準備) >  **[ターゲット]** で、ターゲットの設定を選択します。
2. Site Recovery は、指定されたターゲットに Azure のストレージ アカウントとネットワークが存在することを確認します。

### <a name="create-a-replication-policy"></a>レプリケーション ポリシーを作成する

ソースとターゲットが設定されたら、Contoso 管理者がレプリケーション ポリシーを作成する準備が整います。

1. **[インフラストラクチャの準備]**  >  **[レプリケーション設定]**  >  **[レプリケーション ポリシー]**  >   **[作成と関連付け]** で、ポリシー **ContosoMigrationPolicy** を作成します。

2. 以下の既定の設定を使用します。
    - **RPO しきい値:** 既定値は 60 分です。 この値で、復旧ポイントの作成頻度を指定します。 継続的なレプリケーションがこの制限を超えると、アラートが生成されます。
    - **復旧ポイントのリテンション期間:** 既定値は 24 時間です。 この値は、各復旧ポイントのリテンション期間の長さを指定します。 レプリケートされた VM は、期間内の任意の時点に復旧できます。
    - **アプリ整合性スナップショットの頻度:** 既定値は 1 時間です。 この値は、アプリケーション整合性スナップショットが作成される頻度を指定します。

        ![レプリケーション ポリシーの作成](./media/contoso-migration-rehost-linux-vm-mysql/replication-policy.png)

3. このポリシーは自動的に構成サーバーに関連付けられます。

    ![レプリケーション ポリシーを関連付ける](./media/contoso-migration-rehost-linux-vm-mysql/replication-policy2.png)

**さらにサポートが必要な場合**

- これらすべてのステップの詳細な手順については、[オンプレミス VMware VM のディザスター リカバリーの設定に関する記事](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial)を参照してください。
- [ソース環境の設定](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source)、[構成サーバーのデプロイ](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server)、および[レプリケーション設定の構成](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication)に役立つ詳細な手順が記載されています。
- Linux 用の Azure ゲスト エージェントの詳細については、[こちら](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux)を参照してください。

### <a name="enable-replication-for-the-web-vm"></a>Web VM のレプリケーションを有効にする

これで Contoso の管理者が、**OSTICKETWEB** VM のレプリケートを開始できるようになりました。

1. **[アプリケーションのレプリケート]**  >  **[ソース]**  >  **[+Replicate]\(+ レプリケート\)** で、ソースの設定を選択します。
2. vCenter サーバーと構成サーバーを含めて、仮想マシンを有効にすることを指定し、ソース設定を選択します。

    ![レプリケーションを有効にする](./media/contoso-migration-rehost-linux-vm-mysql/enable-replication-source.png)

3. ここでターゲットの設定を指定します。 これには、フェールオーバー後に Azure VM が配置されるリソース グループとネットワークのほか、レプリケートされたデータが保存されるストレージ アカウントが含まれます。

     ![レプリケーションを有効にする](./media/contoso-migration-rehost-linux-vm-mysql/enable-replication2.png)

4. レプリケーションのために **OSTICKETWEB** を選択します。

    ![レプリケーションを有効にする](./media/contoso-migration-rehost-linux-vm-mysql/enable-replication3.png)

5. VM のプロパティで、モビリティ サービスを VM に自動的にインストールするために使用する必要があるアカウントを選択します。

     ![モビリティ サービス](./media/contoso-migration-rehost-linux-vm-mysql/linux-mobility.png)

6. **[レプリケーションの設定]**  >  **[レプリケーション設定の構成]** で、正しいレプリケーション ポリシーが適用されていることを確認し、 **[レプリケーションを有効にする]** を選択します。 モビリティ サービスが自動的にインストールされます。
7. レプリケーションの進行状況は、 **[ジョブ]** で追跡します。 **保護の最終処理**ジョブが実行されると、マシンはフェールオーバーできる状態になります。

**さらにサポートが必要な場合**

これらのすべてのステップの詳細な手順は、「[レプリケーションを有効にする](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication)」で確認できます。

## <a name="step-5-migrate-the-database"></a>手順 5:データベースを移行する

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

## <a name="step-6-migrate-the-vms-with-site-recovery"></a>手順 6: Site Recovery を使用して VM を移行する:

最後に、Contoso の管理者は、簡単なテスト フェールオーバーを実行してから VM を移行します。

### <a name="run-a-test-failover"></a>テスト フェールオーバーの実行

テスト フェールオーバーを実行すると、移行の前に、すべてが正常に機能していることを確認する助けになります。

1. テスト フェールオーバーを実行し、選択可能な最新の時点 (**最後に処理された時点**) を復帰させます。
2. **[Shut down machine before beginning failover]\(フェールオーバー前にマシンをシャットダウンする\)** を選択します。これにより、Site Recovery は、フェールオーバーをトリガーする前にソース VM のシャットダウンを試みます。 仮にシャットダウンが失敗したとしても、フェールオーバーは続行されます。
3. テスト フェールオーバーでは、以下が実行されます。

    - 移行を実行するために必要なすべての条件が満たされていることを確認する前提条件チェックが実行されます。
    - Azure VM を作成できるように、フェールオーバーによってデータが処理されます。 最新の復旧ポイントを選択した場合は、データから復旧ポイントが作成されます。
    - 前の手順で処理されたデータを使用して、Azure VM が作成されます。

4. フェールオーバーが完了すると、Azure Portal にはレプリカの Azure VM が表示されます。 VM が適切なサイズであること、適切なネットワークに接続されていること、実行中であることを確認します。
5. 確認したら、フェールオーバーをクリーンアップして、確認された事柄をすべて記録して保存します。

### <a name="migrate-the-vm"></a>VM の移行

VM を移行するために、Contoso 管理者は VM を含む復旧計画を作成し、計画に従って Azure にフェールオーバーします。

1. プランを作成し、それに **OSTICKETWEB** を追加します。

    ![復旧計画](./media/contoso-migration-rehost-linux-vm-mysql/recovery-plan.png)

2. 計画に基づいてフェールオーバーを実行します。 最新の復旧ポイントを選択し、Site Recovery では、フェールオーバーをトリガーする前にオンプレミス VM のシャットダウンを試みる必要があることを指定します。 フェールオーバーの進行状況は **[ジョブ]** ページで確認できます。

    ![フェールオーバー](./media/contoso-migration-rehost-linux-vm-mysql/failover1.png)

3. フェールオーバー中に、vCenter Server は、ESXi ホストで実行されている 2 つの VM を停止するコマンドを発行します。

    ![フェールオーバー](./media/contoso-migration-rehost-linux-vm-mysql/vcenter-failover.png)

4. フェールオーバーが完了したら、Azure VM が想定どおりに Azure Portal に表示されることを確認します。

    ![フェールオーバー](./media/contoso-migration-rehost-linux-vm-mysql/failover2.png)

5. VM を確認したら、移行を完了します。 これで、VM のレプリケーションが停止し、その VM に対する Site Recovery の課金が停止します。

    ![フェールオーバー](./media/contoso-migration-rehost-linux-vm-mysql/failover3.png)

**さらにサポートが必要な場合**

- テスト フェールオーバーの実行に関する[説明を参照します](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure)。
- 復旧計画の作成方法に関する[説明を参照します](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans)。
- Azure へのフェールオーバーに関する[説明を参照します](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover)。

### <a name="connect-the-vm-to-the-database"></a>VM をデータベースに接続する

移行プロセスの最終ステップとして、Contoso 管理者は Azure Database for MySQL を指すようにアプリの接続文字列を更新します。

1. Putty または別の SSH クライアントを使用して、OSTICKETWEB VM への SSH 接続を作成します。 この VM はプライベートなので、プライベート IP アドレスを使用して接続します。

    ![データベースへの接続](./media/contoso-migration-rehost-linux-vm-mysql/db-connect.png)

    ![データベースへの接続](./media/contoso-migration-rehost-linux-vm-mysql/db-connect2.png)

2. **OSTICKETWEB** VM が **OSTICKETMYSQL** データベースと通信できるように設定を更新します。 構成は、この時点で、オンプレミスの IP アドレス 172.16.0.43 を使用してハードコードされています。

    **更新の前:**

    ![IP の更新](./media/contoso-migration-rehost-linux-vm-mysql/update-ip1.png)

    **更新の後:**

    ![IP の更新](./media/contoso-migration-rehost-linux-vm-mysql/update-ip2.png)

    ![IP の更新](./media/contoso-migration-rehost-linux-vm-mysql/update-ip3.png)

3. **systemctl restart apache2** と指定してサービスを再起動します。

    ![Restart](./media/contoso-migration-rehost-linux-vm-mysql/restart.png)

4. 最後に、いずれかの Contoso ドメイン コント ローラー上で、**OSTICKETWEB** の DNS レコードを更新します。

    ![DNS の更新](./media/contoso-migration-rehost-linux-vm-mysql/update-dns.png)

## <a name="clean-up-after-migration"></a>移行後にクリーンアップする

移行が完了すると、Azure VM で osTicket のアプリ層が実行されています。

ここで、以下のことを行う必要があります。

- VMware VM を vCenter インベントリから削除します。
- ローカルのバックアップ ジョブからからオンプレミスの VM を削除します。
- 社内のドキュメントを更新して、新しい場所と IP アドレスを示します。
- こうしたオンプレミスの VM と対話しているリソースがないか確認し、新しい構成が反映されるように、関連する設定やドキュメントをすべて更新します。
- Contoso では、依存関係のマッピングを指定して Azure Migrate サービスを使用し、移行のための **OSTICKETWEB** VM を評価しました。 次に、この目的のためにインストールしたエージェント (Microsoft Monitoring Agent と Microsoft Dependency Agent) を VM から削除する必要があります。

## <a name="review-the-deployment"></a>デプロイを再調査する

アプリが実行されるようになり、Contoso は新しいインフラストラクチャを完全に操作可能にして、セキュリティで保護する必要があります。

### <a name="security"></a>セキュリティ

Contoso のセキュリティ チームは VM とデータベースを再調査して、セキュリティの問題を特定します。

- VM がアクセスを制御するためのネットワーク セキュリティ グループ (NSG) を見直します。 NSG は、アプリケーションに対して許可されるトラフィックのみが通過できるようにするために使用されます。
- ディスクの暗号化と Azure Key Vault を使用した、VM ディスク上のデータのセキュリティ保護を検討します。
- VM とデータベース インスタンス間の通信は、SSL 用に構成されていません。 データベース トラフィックをハッキングできないようにするため、これを行う必要があります。

詳細については、「[Azure における IaaS ワークロードのセキュリティに関するベスト プラクティス](https://docs.microsoft.com/azure/security/fundamentals/iaas)」を参照してください。

### <a name="bcdr"></a>BCDR

事業継続とディザスター リカバリーのために、Contoso は次のアクションを実施します。

- **データの安全性を確保する。** Contoso は、Azure Backup サービスを使用して、アプリの VM 上のデータをバックアップします。 [詳細情報](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 データベースのバックアップを構成する必要はありません。 Azure Database for MySQL は、サーバーのバックアップを自動的に作成して保存します。 彼らはデータベースのために geo 冗長性を使用することを選択したため、このデータベースは耐障害性があり、運用準備ができています。
- **アプリの稼働状態を維持する。** Contoso は、Site Recovery を使用して、Azure 内のアプリの VM をセカンダリ リージョンにレプリケートします。 [詳細情報](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-quickstart)。

### <a name="licensing-and-cost-optimization"></a>ライセンスとコストの最適化

- Contoso は、リソースのデプロイ後、[Azure インフラストラクチャ](./contoso-migration-infrastructure.md#set-up-tagging)のデプロイ中に下した決定に従って Azure タグを割り当てます。
- Contoso Ubuntu サーバーにライセンスの問題はありません。
- Contoso は、Microsoft の子会社である Cloudyn からライセンスが供与される Azure Cost Management を有効にします。 Azure やその他のクラウド リソースの利用や管理に役立つ、マルチクラウド対応のコスト管理ソリューションです。 Azure Cost Management の詳細については、[こちら](https://docs.microsoft.com/azure/cost-management/overview)を参照してください。
