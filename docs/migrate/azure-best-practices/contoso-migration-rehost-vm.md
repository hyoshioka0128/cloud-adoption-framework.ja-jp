---
title: Azure Site Recovery を使用した Azure VM への移行によってアプリを再ホストする
titleSuffix: Microsoft Cloud Adoption Framework for Azure
description: Azure Site Recovery サービスを使用したオンプレミス マシンの Azure への "リフトアンドシフト" 移行によって、Contoso がオンプレミス アプリを再ホストする方法を説明します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 10/11/2018
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: migrate
services: site-recovery
ms.openlocfilehash: d0fff6d3c88d62ed3efc9f4cf2140981e8be331a
ms.sourcegitcommit: 35c162d2d09ec1c4a57d3d57a5db1d56ee883806
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/17/2019
ms.locfileid: "72547212"
---
# <a name="rehost-an-on-premises-app-to-azure-vms"></a>オンプレミス アプリを Azure VM にリホストする

この記事では、Contoso という架空の会社が、アプリの VM を Azure VM に移行して、VMware VM 上で実行される 2 階層の Windows .NET フロントエンド アプリを再ホストする方法を説明します。

この例で使用される SmartHotel360 アプリは、オープン ソースとして提供されます。 独自のテスト目的に沿って使用する場合は、[GitHub](https://github.com/Microsoft/SmartHotel360) からダウンロードできます。

## <a name="business-drivers"></a>ビジネス ドライバー

IT リーダーシップ チームは、ビジネス パートナーと密接に連絡を取り合い、彼らがこの移行で何を達成しようとしているのかを理解しました。

- **ビジネスの成長への対応。** Contoso は成長を続けています。そのため、オンプレミスのシステムとインフラストラクチャに負荷がかかっています。
- **リスクの限定。** SmartHotel360 アプリは、Contoso のビジネスにとって非常に重要です。 同社は、リスクを負うことなく Azure にこのアプリを移行したいと考えています。
- **拡張。** Contoso は、アプリを変更することは望んでいません。ただその安定を確保することが望みです。

## <a name="migration-goals"></a>移行の目標

Contoso クラウド チームは、この移行の目標を設定しました。 これらの目標を使用して、最良の移行方法を決定します。

- 移行しても、Azure 内のアプリは、現在の VMware と同じパフォーマンスを発揮できる必要があります。 このアプリは、オンプレミスの場合と同様に、クラウド内にあっても非常に重要であり続けます。
- Contoso は、このアプリへの投資を望んでいません。 これはビジネスにとって重要なものですが、現状のまま確実にクラウドに移すことだけを Contoso は望んでいます。
- Contoso は、このアプリの操作モデルを変更したくありません。 クラウドでは、今行っているのと同じ方法で操作したいのです。
- Contoso は、アプリの機能を変更したくありません。 変えるのはアプリの場所のみです。

## <a name="solution-design"></a>ソリューション設計

Contoso は目標と要件を決定した後、デプロイ ソリューションを設計およびレビューし、移行に使用する Azure サービスを含め、移行プロセスを決めます。

### <a name="current-app"></a>現在のアプリ

- アプリは 2 つの VM (**WEBVM** と **SQLVM**) に階層化されています。
- VM は、VMware ESXi ホスト **contosohost1.contoso.com** (バージョン 6.5) 上に配置されています。
- VMware 環境は、VM 上で実行中の vCenter Server 6.5 (**vcenter.contoso.com**) によって管理されています。
- Contoso にはオンプレミスのデータセンター (contoso-datacenter) があり、そこにオンプレミスのドメイン コントローラー (**contosodc1**) が含まれています。

### <a name="proposed-architecture"></a>提案されたアーキテクチャ

- このアプリは運用のワークロードであるため、Azure 内のアプリの VM は、運用リソース グループ ContosoRG 内に存在することになります。
- アプリの VM はプライマリ Azure リージョン (米国東部 2) に移行され、運用ネットワーク (VNET-PROD-EUS2) 内に配置されます。
- Web のフロントエンド VM は、運用ネットワーク内のフロントエンド サブネット (PROD-FE-EUS2) に置かれます。
- データベース VM は、運用ネットワーク内のデータベース サブネット (PROD-DB-EUS2) 内に存在することになります。
- Contoso データセンター内のオンプレミス VM は、移行が行われた後に使用停止にされます。

![シナリオのアーキテクチャ](./media/contoso-migration-rehost-vm/architecture.png)

### <a name="database-considerations"></a>データベースの考慮事項

Contoso はソリューション設計プロセスの一環として、Azure SQL Database と SQL Server の機能を比較しました。 次の事柄を検討した結果、同社は、Azure IaaS VM 上で稼働する SQL Server を採用することに決定しました。

- Contoso がオペレーティング システムまたはデータベース サーバーをカスタマイズしなければならなくなった場合や、同じ VM にサードパーティのアプリを併置して実行することが必要になった場合、SQL Server を実行する Azure VM を使用することが最適な解決策であると考えられる。
- SQL Database マネージド インスタンスでは、Contoso が将来、ソフトウェア アシュアランスに基づき、SQL Server 用の Azure ハイブリッド使用特典を利用して、既存のライセンスを割引料金のライセンスに交換することができる。 これによって、Managed Instance を最大 30% 節約することができます。

### <a name="solution-review"></a>ソリューションのレビュー

Contoso は、長所と短所の一覧をまとめて、提案されたデザインを評価します。

<!-- markdownlint-disable MD033 -->

**考慮事項** | **詳細**
--- | ---
**長所** | アプリの VM はどちらも変更を加えることなく Azure に移されるため、移行が簡単で済みます。<br/><br/> Contoso は両方のアプリの VM に "リフトアンドシフト" を使用するため、アプリのデータベース用に特別な構成や移行ツールは不要です。<br/><br/> Contoso は、Azure ハイブリッド特典を使用して、ソフトウェア アシュアランスへの投資を活かすことができます。<br/><br/> Contoso は、Azure で引き続き アプリの VM を完全に制御できます。
**短所** | WEBVM と SQLVM では Windows Server 2008 R2 が実行されています。 このオペレーティング システムは、特定のロールを対象に Azure でサポートされます (2018 年 7 月)。 [詳細情報](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)。<br/><br/> アプリの Web 層とデータ層は、依然として単一フェールオーバー ポイントになります。<br/><br/> SQLVM が動作する SQL Server 2008 R2はメインストリーム サポートの対象外です。 ただし、Azure VM ではサポートされています (2018 年 7 月)。 [詳細情報](https://support.microsoft.com/help/956893)。<br/><br/> Contoso は今後も、Azure App Service や Azure SQL Database といったマネージド サービスにアプリを移行するのではなく、Azure VM としてアプリをサポートしていく必要があります。

<!-- markdownlint-enable MD033 -->

### <a name="migration-process"></a>移行プロセス

Contoso は、Azure Migrate Server Migration ツールのエージェントレス方式を使用して、アプリのフロントエンドとデータベースの VM を Azure VM に移行します。

- 最初の手順として、Azure Migrate Server Migration 用の Azure コンポーネントを準備してセットアップし、オンプレミスの VMware インフラストラクチャを準備します。
- [Azure インフラストラクチャ](./contoso-migration-infrastructure.md)は既に用意されているので、Contoso では、Azure Migrate Server Migration ツールを使用して VM のレプリケーションの構成を追加するだけで済みます。
- すべての準備ができたら、VM のレプリケートを開始できます。
- レプリケーションを有効にしたら、Azure へのフェールオーバーによって VM を移行します。

![移行プロセス](./media/contoso-migration-rehost-vm/migration-process-az-migrate.png)

### <a name="azure-services"></a>Azure サービス

**サービス** | **説明** | **コスト**
--- | --- | ---
[Azure Migrate Server Migration](https://docs.microsoft.com/azure/migrate/contoso-migration-rehost-vm) | このサービスは、オンプレミスのアプリとワークロード、AWS/GCP VM インスタンスの移行を統制し、管理します。 | Azure へのレプリケーションの間に、Azure Storage の料金が発生します。 フェールオーバーが発生すると、Azure VM が作成されて料金がかかります。 料金と価格について[詳しくはこちら](https://azure.microsoft.com/pricing/details/azure-migrate)をご覧ください。

## <a name="prerequisites"></a>前提条件

このシナリオを実行するために Contoso に必要なものを以下に示します。

<!-- markdownlint-disable MD033 -->

**要件** | **詳細**
--- | ---
**Azure サブスクリプション** | このシリーズの先行する記事の中で、Contoso はサブスクリプションを作成しました。 Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/pricing/free-trial)を作成してください。<br/><br/> 無料アカウントを作成する場合、サブスクリプションの管理者としてすべてのアクションを実行できます。<br/><br/> 既存のサブスクリプションを使用しており、管理者でない場合は、管理者に依頼して所有者アクセス許可または共同作成者アクセス許可を割り当ててもらう必要があります。<br/><br/> さらに詳細なアクセス許可が必要な場合は、[こちらの記事](https://docs.microsoft.com/azure/site-recovery/site-recovery-role-based-linked-access-control)をご覧ください。
**Azure インフラストラクチャ** | [Contoso で Azure インフラストラクチャを設定する方法](./contoso-migration-infrastructure.md)を確認してください。<br/><br/> Azure Migrate Server Migration の具体的な[前提条件](https://docs.microsoft.com/azure/migrate/contoso-migration-rehost-vm#prerequisites)の要件の詳細について確認してください。
**オンプレミスのサーバー** | オンプレミスの vCenter Server は、バージョン 5.5、6.0、または 6.5 を実行している必要があります。<br/><br/> ESXi ホストは、バージョン 5.5、6.0 または 6.5 を実行している必要があります。<br/><br/> ESXi ホスト上で 1 つ以上の VMware VM が実行されている必要があります。

<!-- markdownlint-enable MD033 -->

## <a name="scenario-steps"></a>シナリオのステップ

Contoso の管理者が移行を実行する方法を次に示します。

> [!div class="checklist"]
>
> - **手順 1:Azure Migrate Server Migration 用の Azure の準備。** Azure Migrate プロジェクトに Server Migration ツールを追加します。
> - **手順 2:Azure Migrate Server Migration 用のオンプレミス VMware の準備。** VM 検出用のアカウントを準備し、フェールオーバー後に Azure VM に接続するための準備をします。
> - **手順 3:VM をレプリケートする。** レプリケーションを設定し、Azure Storage への VM のレプリケーションを開始します。
> - **手順 4:Azure Migrate Server Migration による VM の移行。** テスト フェールオーバーを実行してすべてが機能していることを確認した後、完全フェールオーバーを実行して VM を Azure に移行します。

## <a name="step-1-prepare-azure-for-the-azure-migrate-server-migration-tool"></a>手順 1:Azure Migrate Server Migration ツール用の Azure の準備

Contoso が VM を Azure に移行するには、以下の Azure コンポーネントが必要です。

- フェールオーバー中に作成される Azure VM が配置される VNet。
- プロビジョニング済みの Azure Migrate Server Migration ツール。

これらを次のように設定します。

1. ネットワークを設定する。Contoso は、[Azure インフラストラクチャのデプロイ](./contoso-migration-infrastructure.md)を行ったときに、Azure Migrate Server Migration に使用できるネットワークを既にセットアップしています。

    - SmartHotel360 アプリは運用アプリであり、VM はプライマリ リージョンである米国東部 2 の Azure 運用ネットワーク (VNET-PROD-EUS2) に移行されます。
    - 両方の VM は、運用リソースのために使用される ContosoRG リソース グループに配置されます。
    - アプリのフロントエンド VM (WEBVM) は、運用ネットワーク内のフロントエンド サブネット (PROD-FE-EUS2) に移行されます。
    - アプリのデータベース VM (SQLVM) は、運用ネットワーク内のデータベース サブネット (PROD-DB-EUS2) に移行されます。

2. Azure Migrate Server Migration ツールをプロビジョニングする。ネットワークとストレージ アカウントが用意できたので、Recovery Services コンテナー (ContosoMigrationVault) を作成し、それをプライマリの米国東部 2 リージョンの ContosoFailoverRG リソース グループ内に配置します。

    ![Azure Migrate Server Migration ツール](./media/contoso-migration-rehost-vm/server-migration-tool.png)

**さらにサポートが必要な場合**

Azure Migrate Server Migration ツールのセットアップについて[ご確認ください](https://docs.microsoft.com/azure/migrate)。

### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>フェールオーバー後に Azure VM に接続するための準備をする

Contoso は、フェールオーバー後に Azure VM に接続することを望んでいます。 そのため、Contoso の管理者は、移行前に以下の操作を行います。

1. インターネットでアクセスする場合:

    - フェールオーバーの前に、オンプレミスの VM で RDP を有効にします。
    - **パブリック** プロファイルに TCP 規則と UDP 規則が追加されていることを確認します。
    - **[Windows ファイアウォール]**  >  **[許可されたアプリ]** で、すべてのプロファイルで RDP が許可されていることを確認します。

2. サイト間 VPN でアクセスする場合:

    - オンプレミスのマシンで RDP を有効にします。
    - **[Windows ファイアウォール]**  ->  **[Allowed apps and features]\(許可されたアプリと機能\)** で、**ドメイン ネットワークとプライベート ネットワーク**での RDP を許可します。
    - オンプレミスの VM 上のオペレーティング システムの SAN ポリシーを **[OnlineAll]** に設定します。

さらに、フェールオーバーを実行するときに、以下を確認する必要があります。

- フェールオーバーをトリガーするときに、VM 上に保留中の Windows 更新プログラムがないようにします。 ある場合は、更新が完了するまで、VM にログインすることはできません。
- フェールオーバー後、**ブート診断**を調べて、VM のスクリーンショットを確認できます。 これが機能しない場合は、VM が実行中であることを確認し、こちらの[トラブルシューティングのヒント](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)を参照してください。

**さらにサポートが必要な場合**

- 移行用の VM の準備について[ご確認ください](https://docs.microsoft.com/azure/migrate/contoso-migration-rehost-vm#prepare-vms-for-migration)

## <a name="step-3-replicate-the-on-premises-vms"></a>手順 3:オンプレミスの VM をレプリケートする

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

7. **[Azure ハイブリッド特典]** で、

    - Azure ハイブリッド特典を適用しない場合は、 **[いいえ]** を選択します。 その後、 **[次へ]** をクリックします。
    - アクティブなソフトウェア アシュアランスまたは Windows Server のサブスクリプションの対象となっている Windows Server マシンがあり、移行中のマシンにその特典を適用する場合は、 **[はい]** を選択します。 その後、 **[次へ]** をクリックします。

8. **[コンピューティング]** で、VM の名前、サイズ、OS ディスクの種類、可用性セットを確認します。 VM は [Azure の要件](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#agentless-migration-vmware-vm-requirements)に準拠している必要があります。

    - **VM サイズ**: 評価の推奨事項を使用している場合は、[VM サイズ] ドロップダウンに推奨サイズが表示されます。 それ以外の場合は、Azure Migrate によって、Azure サブスクリプション内の最も近いサイズが選択されます。 または、 **[Azure VM サイズ]** でサイズを手動で選択します。
    - **OS ディスク**:VM の OS (ブート) ディスクを指定します。 OS ディスクは、オペレーティング システムのブートローダーとインストーラーがあるディスクです。
    - **可用性セット**:移行後に VM を Azure 可用性セットに配置する必要がある場合は、セットを指定します。 このセットは、移行用に指定するターゲット リソース グループ内に存在する必要があります。

9. **[ディスク]** で、VM ディスクを Azure にレプリケートするかどうかを指定し、Azure でのディスクの種類 (Standard SSD か HDD、または Premium マネージド ディスク) を選択します。 その後、 **[次へ]** をクリックします。
    - レプリケーションからディスクを除外できます。
    - ディスクは除外すると、移行後に Azure VM 上に存在しなくなります。

10. **[レプリケーションの確認と開始]** で、設定を確認し、 **[レプリケート]** をクリックして、サーバーの初期レプリケーションを開始します。

> [!NOTE]
> レプリケーションを開始する前であれば、 **[管理]**  >  **[マシンのレプリケート]** でレプリケーションの設定をいつでも更新できます。 レプリケーションの開始後は、設定を変更することができません。

## <a name="step-4-migrate-the-vms"></a>手順 4:VM を移行する

Contoso の管理者は、簡単なテスト フェールオーバーを実行した後、VM を移行するための完全フェールオーバーを実行します。

### <a name="run-a-test-failover"></a>テスト フェールオーバーの実行

1. **[移行の目標]**  >  **[サーバー]**  >  **[Azure Migrate: Server Migration]** で、 **[移行されたサーバーをテストします]** をクリックします。

     ![移行されたサーバーのテスト](./media/contoso-migration-rehost-vm/test-migrated-servers.png)

2. テストする VM を右クリックし、 **[テスト移行]** をクリックします。

    ![テスト移行](./media/contoso-migration-rehost-vm/test-migrate.png)

3. **[テスト移行]** で、移行後に Azure VM が配置される Azure VNet を選択します。 非運用環境の VNet を使用することをお勧めします。
4. **テスト移行**ジョブが開始されます。 ポータルの通知でジョブを監視します。
5. 移行の完了後、Azure portal の **[仮想マシン]** で、移行された Azure VM を確認します。 マシン名には、サフィックス **-Test** が含まれています。
6. テストが完了したら、 **[マシンのレプリケート]** で Azure VM を右クリックし、 **[テスト移行をクリーンアップ]** をクリックします。

    ![移行のクリーンアップ](./media/contoso-migration-rehost-vm/clean-up.png)

### <a name="migrate-the-vms"></a>VM を移行する

次に、Contoso の管理者は、移行を完了する完全フェールオーバーを実行します。

1. Azure Migrate プロジェクトの **[サーバー]**  >  **[Azure Migrate: Server Migration]** で、 **[サーバーをレプリケートしています]** をクリックします。

    ![サーバーをレプリケートしています](./media/contoso-migration-rehost-vm/replicating-servers.png)

2. **[マシンのレプリケート]** で VM を右クリックし、 **[移行]** を選択します。
3. **[移行]**  >  **[仮想マシンをシャットダウンし、データ損失のない計画された移行を実行しますか]** で、 **[はい]**  >  **[OK]** の順に選択します。
    - 既定では、Azure Migrate によってオンプレミスの VM がシャットダウンされ、前回のレプリケーションが発生した後に発生したすべての VM の変更を同期するためにオンデマンド レプリケーションが実行されます。 こうすることで、データ損失がなくなります。
    - VM をシャットダウンしたくない場合は、 **[いいえ]** を選択します
4. VM に対して移行ジョブが開始されます。 Azure 通知でジョブを追跡します。
5. ジョブが完了したら、 **[仮想マシン]** ページで VM を表示して管理できます。

**さらにサポートが必要な場合**

- テスト フェールオーバーの実行に関する[説明を参照します](https://docs.microsoft.com/azure/migrate/tutorial-migrate-vmware#run-a-test-migration)。
- VM の Azure への移行に関する[説明を参照します](https://docs.microsoft.com/azure/migrate/tutorial-migrate-vmware#migrate-vms)。

## <a name="clean-up-after-migration"></a>移行後にクリーンアップする

移行が完了し、SmartHotel360 のアプリ層が Azure VM 上で実行されています。

次に、Contoso は、以下のクリーンアップ手順を完了する必要があります。

- 移行が完了したら、レプリケーションを停止します。
- WEBVM マシンを vCenter インベントリから削除します。
- SQLVM マシンを vCenter インベントリから削除します。
- WEBVM と SQLVM をローカル バックアップ ジョブから削除します。
- VM の新しい場所と IP アドレスを示すように内部ドキュメントを更新します。
- VM と対話しているリソースを確認し、すべての関連する設定またはドキュメントを新しい構成を反映するように更新します。

## <a name="review-the-deployment"></a>デプロイを再調査する

これでアプリが実行されるようになったので、Contoso は、アプリを完全に操作可能にし、セキュリティで保護する必要があります。

### <a name="security"></a>セキュリティ

Contoso のセキュリティ チームは、Azure VM を再調査して、セキュリティの問題を特定します。

- アクセスを制御するために、VM のネットワーク セキュリティ グループ (NSG) を見直します。 NSG は、許可されたトラフィックのみがアプリに到達できるようにするために使用されます。
- また、このチームは、ディスク上のデータ保護のために、Azure Disk Encryption と Key Vault の使用も検討します。

VM に関するセキュリティの実務の[詳細については、こちら](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms)を参照してください。

## <a name="bcdr"></a>BCDR

事業継続とディザスター リカバリー (BCDR) のために、Contoso は次のアクションを実施します。

- データの安全性を確保する: Contoso は、Azure Backup サービスを使用して VM 上のデータをバックアップします。 [詳細情報](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
- アプリの稼働状態を維持する: Contoso は、Site Recovery を使用して、Azure 内のアプリの VM をセカンダリ リージョンにレプリケートします。 [詳細情報](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-quickstart)。

### <a name="licensing-and-cost-optimization"></a>ライセンスとコストの最適化

1. Contoso は、VM の既存のライセンスを所有しており、Azure ハイブリッド特典を活用します。 Contoso は、この特典を活用するために、既存の Azure VM を変換します。
2. Contoso は、Microsoft の子会社である Cloudyn からライセンスが供与される Azure Cost Management を有効にします。 Azure やその他のクラウド リソースの利用や管理に役立つ、マルチクラウド対応のコスト管理ソリューションです。 Azure Cost Management の詳細については、[こちら](https://docs.microsoft.com/azure/cost-management/overview)を参照してください。

## <a name="conclusion"></a>まとめ

この記事では、Contoso が Azure Migrate Server Migration ツールを使用してアプリ VM を Azure VM に移行することで、SmartHotel360 アプリを Azure 内で再ホストしました。
