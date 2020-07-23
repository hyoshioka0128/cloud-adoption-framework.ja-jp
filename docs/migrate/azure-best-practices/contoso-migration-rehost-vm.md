---
title: Azure Migrate を使用してアプリケーションを Azure VM にリホストする
description: Azure Migrate サービスを使用したオンプレミス マシンの Azure へのリフトアンドシフト移行によって、Contoso がオンプレミス アプリを再ホストする方法を説明します。
author: givenscj
ms.author: abuck
ms.date: 07/01/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: migrate
services: azure-migrate
ms.openlocfilehash: 0ba6ea1cf22b2a13ba17f43e356d657e574396e9
ms.sourcegitcommit: 84d7bfd11329eb4c151c4c32be5bab6c91f376ed
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2020
ms.locfileid: "86234329"
---
<!-- cSpell:ignore givenscj WEBVM SQLVM OSTICKETWEB OSTICKETMYSQL contosohost vcenter contosodc NSGs agentless -->

# <a name="rehost-an-on-premises-application-on-azure-vms-with-azure-migrate"></a>Azure Migrate を使用してオンプレミス アプリケーションを Azure VM にリホストする

この記事では、Contoso という架空の会社が、アプリケーション VM を Azure VM に移行することによって、VMware VM 上で実行されている 2 層の Windows .NET フロントエンド アプリケーションをリホストする方法について説明します。

この例で使用される SmartHotel360 アプリケーションは、オープンソースとして提供されています。 独自のテスト目的に沿って使用する場合は、[GitHub](https://github.com/Microsoft/SmartHotel360) からダウンロードできます。

## <a name="business-drivers"></a>ビジネス ドライバー

IT リーダーシップ チームは、ビジネス パートナーと密接に連絡を取り合い、彼らがこの移行で何を達成しようとしているのかを理解しました。

- **ビジネスの成長への対応。** Contoso は成長を続けています。そのため、オンプレミスのシステムとインフラストラクチャに負荷がかかっています。
- **リスクの限定。** SmartHotel360 アプリケーションは、Contoso のビジネスに不可欠です。 同社は、リスクを伴わずにこのアプリケーションを Azure に移行したいと考えています。
- **拡張。** Contoso は、アプリケーションを変更することは望んでいませんが、その安定性を確保したいと考えています。

## <a name="migration-goals"></a>移行の目標

Contoso クラウド チームは、この移行の目標を設定しました。 これらの目標を使用して、最良の移行方法を決定します。

- 移行後、Azure 内のアプリケーションは、現在の VMware の場合と同じパフォーマンスを発揮できる必要があります。 このアプリケーションは、オンプレミスにあるときと同様に、クラウドでも引き続き非常に重要です。
- Contoso は、このアプリケーションに投資することを望んでいません。 これはビジネスにとって重要なものですが、現状のまま確実にクラウドに移すことだけを Contoso は望んでいます。
- Contoso は、このアプリケーションの操作モデルを変更することを望んでいません。 クラウドでは、今行っているのと同じ方法で操作したいのです。
- Contoso は、アプリケーションの機能を変更することを望んでいません。 変わるのはアプリケーションの場所だけです。

## <a name="solution-design"></a>ソリューション設計

Contoso は目標と要件を決定した後、デプロイ ソリューションを設計およびレビューし、移行に使用する Azure サービスを含め、移行プロセスを決めます。

### <a name="current-application"></a>現在のアプリケーション

- アプリケーションは 2 つの VM (`WEBVM` と `SQLVM`) に階層化されています。
- これらの VM は、VMware ESXi ホスト `contosohost1.contoso.com` (バージョン 6.5) 上に配置されています。
- VMware 環境は、VM で実行中のvCenter Server 6.5 (`vcenter.contoso.com`) によって管理されています。
- Contoso には、オンプレミスのデータセンター (`contoso-datacenter`) があり、オンプレミスのドメイン コントローラー (`contosodc1`) が含まれています。

### <a name="proposed-architecture"></a>提案されたアーキテクチャ

- このアプリケーションは運用ワークロードであるため、Azure のアプリケーション VM は、運用リソース グループ `ContosoRG` に配置されます。
- アプリケーション VM はプライマリ Azure リージョン (米国東部 2) に移行され、運用ネットワーク (`VNET-PROD-EUS2`) に配置されます。
- Web フロントエンド VM は、運用ネットワークのフロントエンド サブネット (`PROD-FE-EUS2`) に配置されます。
- データベース VM は、運用ネットワークのデータベース サブネット (`PROD-DB-EUS2`) に配置されます。
- Contoso データセンター内のオンプレミス VM は、移行が行われた後に使用停止にされます。

![シナリオのアーキテクチャ](./media/contoso-migration-rehost-vm/architecture.png)

### <a name="database-considerations"></a>データベースの考慮事項

Contoso はソリューション設計プロセスの一環として、Azure SQL Database と SQL Server の機能を比較しました。 Azure IaaS VM 上で実行される SQL Server を使用することを決定する際に、次の考慮事項が役立ちました。

- Contoso がオペレーティング システムとデータベースをカスタマイズする必要がある場合や、同じ VM にサードパーティのアプリケーションを併置して実行する場合は、SQL Server を実行する Azure VM を使用するのが最適なソリューションと考えられます。
- ソフトウェア アシュアランスに基づき、将来、Contoso は、SQL Managed Instance で SQL Server 用の Azure ハイブリッド特典を使用して、既存のライセンスを割引料金のライセンスに交換できます。 これにより、SQL Managed Instance を最大 30% 節約できます。

### <a name="solution-review"></a>ソリューションのレビュー

Contoso は、長所と短所の一覧をまとめて、提案されたデザインを評価します。

| 考慮事項 | 詳細 |
| --- | --- |
| **長所** | アプリケーション VM はどちらも変更なしで Azure に移行されるので、移行が簡単になります。 <br><br> Contoso は両方のアプリケーション VM にリフトアンドシフト アプローチを使用するため、アプリケーション データベース用に特別な構成や移行ツールは不要です。 <br><br> Contoso は、Azure ハイブリッド特典を使用して、ソフトウェア アシュアランスへの投資を活かすことができます。 <br><br> Contoso は、Azure でアプリケーション VM の完全な制御を維持できます。 |
| **短所** | `WEBVM` と `SQLVM` では、Windows Server 2008 R2 が実行されています。 このオペレーティング システムは、特定のロールを対象に Azure でサポートされます。 [詳細については、こちらを参照してください](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)。 <br><br> アプリケーションの Web 層とデータ層は、引き続き単一障害点となります。 <br><br> SQLVM が動作する SQL Server 2008 R2はメインストリーム サポートの対象外になっています。 ただし、Azure VM ではサポートされています。 [詳細については、こちらを参照してください](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-2008-eos-extend-support)。 <br><br> Contoso は、Azure App Service や Azure SQL Database などのマネージド サービスに移行するのではなく、引き続き Azure VM でアプリケーションをサポートする必要があります。 |

### <a name="migration-process"></a>移行プロセス

Contoso は、Azure Migrate: Server Migration ツールのエージェントレス方式を使用して、アプリのフロントエンドとデータベースの VM を Azure VM に移行します。

- 最初の手順として、Azure Migrate:Server Migration 用の Azure コンポーネントを準備および設定し、オンプレミスの VMware インフラストラクチャを準備します。
- [Azure インフラストラクチャ](./contoso-migration-infrastructure.md)は既に用意されているので、Contoso では、Azure Migrate: Server Migration ツールを使用して VM のレプリケーションの追加と構成を行うだけで済みますサーバー移行ツール。
- すべての準備ができたら、VM のレプリケートを開始できます。
- レプリケーションを有効にしたら、移行をテストし、それに成功したら Azure へのフェールオーバーによって VM を移行します。

![移行プロセス](./media/contoso-migration-rehost-vm/migration-process-az-migrate.png)

### <a name="azure-services"></a>Azure サービス

| サービス | 説明 | コスト |
| --- | --- | --- |
| [Azure Migrate: Server Migration](https://docs.microsoft.com/azure/migrate/contoso-migration-rehost-vm) | このサービスは、オンプレミスのアプリケーションとワークロード、および AWS/GCP VM インスタンスの移行を調整および管理します。 | Azure へのレプリケーションの間に、Azure Storage の料金が発生します。 移行が行われ、VM が Azure で実行されている場合、Azure VM が作成され、料金が発生します。 料金と価格について[詳しくはこちら](https://azure.microsoft.com/pricing/details/azure-migrate)をご覧ください。 |

## <a name="prerequisites"></a>前提条件

このシナリオを実行するために Contoso に必要なものを以下に示します。

| 必要条件 | 詳細 |
| --- | --- |
| **Azure サブスクリプション** | このシリーズの先行する記事の中で、Contoso はサブスクリプションを作成しました。 Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/free)を作成してください。 <br><br> 無料アカウントを作成する場合、サブスクリプションの管理者としてすべてのアクションを実行できます。 <br><br> 既存のサブスクリプションを使用しており、管理者でない場合は、管理者に依頼して所有者アクセス許可または共同作成者アクセス許可を割り当ててもらう必要があります。 <br><br> さらに詳細なアクセス許可が必要な場合は、[こちらの記事](https://docs.microsoft.com/azure/site-recovery/site-recovery-role-based-linked-access-control)をご覧ください。 |
| **Azure インフラストラクチャ** | [Contoso で Azure インフラストラクチャを設定する方法](./contoso-migration-infrastructure.md)を確認してください。 <br><br> Azure Migrate: Server Migration の具体的な[前提条件](./contoso-migration-devtest-to-iaas.md#prerequisites)の要件の詳細について確認してくださいServer Migration に関するエラーのトラブルシューティングに役立つ情報を提供しています。 |
| **オンプレミスのサーバー** | オンプレミスの vCenter Server は、バージョン 5.5、6.0、6.5、または 6.7 を実行している必要があります。 <br><br> ESXi ホストは、バージョン 5.5、6.0、6.5 または 6.7 を実行している必要があります。 <br><br> ESXi ホスト上で 1 つ以上の VMware VM が実行されている必要があります。 |

## <a name="scenario-steps"></a>シナリオのステップ

Contoso の管理者が移行を実行する方法を次に示します。

> [!div class="checklist"]
>
> - **ステップ 1:Azure Migrate: Server Migration 用の Azure を準備するServer Migration.** Azure Migrate プロジェクトにサーバー移行ツールを追加します。
> - **手順 2:Azure Migrate: Server Migration 用のオンプレミス VMware を準備する。** VM 検出用のアカウントを準備し、移行後に Azure VM に接続するための準備をします。
> - **ステップ 3:VM をレプリケートする。** レプリケーションを設定し、Azure Storage への VM のレプリケーションを開始します。
> - **手順 4:Azure Migrate: Server Migration で VM を移行する。** テスト移行を実行してすべてが機能していることを確認した後、完全移行を実行して VM を Azure に移動します。

## <a name="step-1-prepare-azure-for-the-azure-migrate-server-migration-tool"></a>手順 1:Azure Migrate: Server Migration ツール用の Azure を準備するServer Migration ツール

Contoso が VM を Azure に移行するには、以下の Azure コンポーネントが必要です。

- 移行中に作成される Azure VM が配置される VNet。
- Azure Migrate: Server Migration ツール (OVA) (プロビジョニングおよび構成済みのもの)。

これらを次のように設定します。

1. ネットワークを設定します - Contoso は、[Azure インフラストラクチャのデプロイ](./contoso-migration-infrastructure.md)を行ったときに、Azure Migrate: Server Migration に使用できるネットワークを既にセットアップしています

    - SmartHotel360 アプリケーションは運用アプリケーションであり、VM はプライマリ リージョン (`East US 2`) の Azure 運用ネットワーク (`VNET-PROD-EUS2`) に移行されます。
    - どちらの VM も、運用リソースに使用される `ContosoRG` リソース グループに配置されます。
    - アプリケーションのフロントエンド VM (`WEBVM`) は、運用ネットワークのフロントエンド サブネット (`PROD-FE-EUS2`) に移行されます。
    - アプリケーション データベース VM (`SQLVM`) は、運用ネットワークのデータベース サブネット (`PROD-DB-EUS2`) に移行されます。

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

      - 検出用の Windows ベースの資格情報を追加します。

        ![ツールを構成する](./media/contoso-migration-rehost-vm/migration-credentials.png)

3. 構成の完了後、ツールによってすべての仮想マシンが列挙されるまでに時間がかかることがあります。 完了すると、Azure の Azure Migrate ツールにそれらの情報が入力されていることがわかります。

**さらにサポートが必要な場合**

[Azure Migrate: Server Migration ツール](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-server-migration-tool)の設定についてご確認ください。

### <a name="prepare-on-premises-vms"></a>オンプレミスの VM を準備する

Contoso は移行後、Azure VM に接続し、Azure で VM を管理できるようにしたいと考えています。 そのため、Contoso の管理者は、移行前に以下の操作を行います。

1. インターネットでアクセスする場合:

    - 移行前に、オンプレミス VM で RDP または SSH を有効にします。
    - **パブリック** プロファイルに TCP 規則と UDP 規則が追加されていることを確認します。
    - オペレーティング システムのファイアウォールで RDP または SSH が許可されていることを確認します。

2. サイト間 VPN 経由でアクセスする場合:

    - 移行前に、オンプレミス VM で RDP または SSH を有効にします。
    - オペレーティング システムのファイアウォールで RDP または SSH が許可されていることを確認します。
    - Windows の場合、オンプレミス VM 上のオペレーティング システムの SAN ポリシーを **OnlineAll** に設定します。

3. Azure エージェントをインストールします。

    - [Azure Windows エージェント](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows)

4. その他の考慮事項

   - Windows の場合、移行をトリガーするときに、VM 上に保留中の Windows 更新プログラムがあってはいけません。 ある場合は、更新が完了するまで、VM にログインすることはできません。
   - 移行後、**ブート診断**を調べて、VM のスクリーンショットを確認できます。 これが機能しない場合は、VM が実行中であることを確認し、こちらの[トラブルシューティングのヒント](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)を参照してください。

**さらにサポートが必要な場合**

- [移行用の VM の準備](https://docs.microsoft.com/azure/migrate/prepare-for-migration)についてご確認ください。

## <a name="step-2-replicate-the-on-premises-vms"></a>手順 2:オンプレミスの VM をレプリケートする

Contoso の管理者は、Azure への移行を実行する前に、レプリケーションを設定して有効にする必要があります。

検出が完了したら、Azure への VMware VM のレプリケーションを開始できます。

1. Azure Migrate プロジェクトの **[サーバー]** の **[Azure Migrate: Server Migration]** で、 **[レプリケート]** を選択します。

    ![VM をレプリケートする](./media/contoso-migration-rehost-vm/select-replicate.png)

2. **[レプリケート]**  >  **[ソースの設定]**  >  **[マシンは仮想化されていますか?]** で、 **[はい (VMware vSphere の場合)]** を選択します。

3. **[オンプレミスのアプライアンス]** で、自分が設定した Azure Migrate アプライアンスの名前、 **[OK]** の順に選択します。

    ![ソースの設定](./media/contoso-migration-rehost-vm/source-settings.png)

4. **[仮想マシン]** で、レプリケートしたいマシンを選択します。
    - VM の評価を実行した場合は、評価結果から VM のサイズ設定とディスクの種類 (Premium または Standard) の推奨事項を適用できます。 これを行うには、 **[Azure Migrate Assessment から移行設定をインポートしますか?]** で **[はい]** オプションを選択します。
    - 評価を実行しなかった場合、または評価の設定を使用しない場合は、 **[いいえ]** オプションを選択します。
    - 評価の使用を選択した場合は、VM グループと評価名を選択します。

    ![評価の選択](./media/contoso-migration-rehost-vm/select-assessment.png)

5. **[仮想マシン]** で、必要に応じて VM を検索し、移行したい各 VM を確認します。 次に、 **[次のステップ: ターゲット設定]** をクリックします。

6. **[ターゲット設定]** で、サブスクリプションと、移行先となるターゲット リージョンを選択し、移行後に Azure VM が配置されるリソース グループを指定します。 **[仮想ネットワーク]** で、移行後に Azure VM の参加先となる Azure VNet およびサブネットを選択します。

7. **[Azure ハイブリッド特典]** で、次のように選択します。

    - Azure ハイブリッド特典を適用しない場合は、 **[いいえ]** を選択します。 **[次へ]** を選択します。
    - アクティブなソフトウェア アシュアランスまたは Windows Server のサブスクリプションの対象となっている Windows Server マシンがあり、移行中のマシンにその特典を適用する場合は、 **[はい]** を選択します。 **[次へ]** を選択します。

8. **[コンピューティング]** で、VM の名前、サイズ、OS ディスクの種類、可用性セットを確認します。 VM は [Azure の要件](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#vmware-requirements)に準拠している必要があります。

    - **VM サイズ:** 評価の推奨事項を使用している場合は、[VM サイズ] ドロップダウンに推奨サイズが表示されます。 それ以外の場合は、Azure Migrate によって、Azure サブスクリプション内の最も近いサイズが選択されます。 または、 **[Azure VM サイズ]** でサイズを手動で選択します。
    - **OS ディスク:** VM の OS (ブート) ディスクを指定します。 OS ディスクは、オペレーティング システムのブートローダーとインストーラーがあるディスクです。
    - **可用性セット:** 移行後に VM を Azure 可用性セットに配置する必要がある場合は、セットを指定します。 このセットは、移行用に指定するターゲット リソース グループ内に存在する必要があります。

9. **[ディスク]** で、VM ディスクを Azure にレプリケートするかどうかを指定し、Azure でのディスクの種類 (Standard SSD か HDD、または Premium マネージド ディスク) を選択します。 **[次へ]** を選択します。
    - レプリケーションからディスクを除外できます。
    - ディスクは除外すると、移行後に Azure VM 上に存在しなくなります。

10. **[レプリケーションの確認と開始]** で設定を確認し、 **[レプリケート]** を選択して、サーバーの初期レプリケーションを開始します。

> [!NOTE]
> レプリケーションを開始する前であれば、 **[管理]**  >  **[マシンのレプリケート]** でレプリケーションの設定をいつでも更新できます。 レプリケーションの開始後は、設定を変更することができません。

## <a name="step-3-migrate-the-vms"></a>手順 3:VM を移行する

Contoso の管理者は、クイックテスト移行を実行し、その後、完全な移行を実行して VM を移行します。

### <a name="run-a-test-migration"></a>テスト移行を実行する

1. **[移行の目標]**  >  **[サーバー]**  >  **[Azure Migrate: Server Migration]** で、 **[移行されたサーバーをテストします]** を選択します。

     ![移行されたサーバーのテスト](./media/contoso-migration-rehost-vm/test-migrated-servers.png)

2. テストする VM を選択したまま (または右クリックし)、 **[テスト移行]** を選択します。

    ![テスト移行](./media/contoso-migration-rehost-vm/test-migrate.png)

3. **[テスト移行]** で、移行後に Azure VM が配置される Azure VNet を選択します。 非運用環境の VNet を使用することをお勧めします。
4. **テスト移行**ジョブが開始されます。 ポータルの通知でジョブを監視します。
5. 移行の完了後、Azure portal の **[仮想マシン]** で、移行された Azure VM を確認します。 マシン名には、サフィックス **-Test** が含まれています。
6. テストが完了したら、 **[マシンのレプリケート]** で Azure VM を選択したまま (または右クリックし)、 **[テスト移行をクリーンアップ]** を選択します。

    ![移行のクリーンアップ](./media/contoso-migration-rehost-vm/clean-up.png)

### <a name="migrate-the-vms"></a>VM を移行する

次に、Contoso の管理者は、完全移行を実行します。

1. Azure Migrate プロジェクトの **[サーバー]**  >  **[Azure Migrate: Server Migration]** で、 **[サーバーをレプリケートしています]** を選択します。

    ![サーバーをレプリケートしています](./media/contoso-migration-rehost-vm/replicating-servers.png)

2. **[マシンのレプリケート]** で VM を選択したまま (または右クリックし)、 **[移行]** を選択します。
3. **[移行]**  >  **[仮想マシンをシャットダウンし、データ損失のない計画された移行を実行しますか]** で、 **[はい]**  >  **[OK]** の順に選択します。
    - 既定では、Azure Migrate によってオンプレミスの VM がシャットダウンされ、前回のレプリケーションが発生した後に発生したすべての VM の変更を同期するためにオンデマンド レプリケーションが実行されます。 こうすることで、データ損失がなくなります。
    - VM をシャットダウンしたくない場合は、 **[いいえ]** を選択します。
4. VM に対して移行ジョブが開始されます。 Azure 通知でジョブを追跡します。
5. ジョブが完了したら、 **[仮想マシン]** ページで VM を表示して管理できます。

**さらにサポートが必要な場合**

- [テスト移行の実行](https://docs.microsoft.com/azure/migrate/tutorial-migrate-vmware#run-a-test-migration)に関する説明を参照します。
- [VM の Azure への移行](https://docs.microsoft.com/azure/migrate/tutorial-migrate-vmware#migrate-vms)に関する説明を参照します。

## <a name="clean-up-after-migration"></a>移行後にクリーンアップする

移行が完了し、SmartHotel360 のアプリケーション層が Azure VM 上で実行されています。

次に、Contoso は、以下のクリーンアップ手順を完了する必要があります。

- 移行が完了したら、レプリケーションを停止します。
- `WEBVM` マシンを vCenter インベントリから削除します。
- `SQLVM` マシンを vCenter インベントリから削除します。
- ローカル バックアップ ジョブから `WEBVM` と `SQLVM` を削除します。
- VM の新しい場所と IP アドレスを示すように内部ドキュメントを更新します。
- VM と対話しているリソースを確認し、すべての関連する設定またはドキュメントを新しい構成を反映するように更新します。

## <a name="review-the-deployment"></a>デプロイを再調査する

これでアプリケーションが実行されるようになったので、Contoso は、Azure でアプリケーションを完全に操作可能にし、セキュリティで保護する必要があります。

### <a name="security"></a>セキュリティ

Contoso のセキュリティ チームは、Azure VM を再調査して、セキュリティの問題を特定します。

- アクセスを制御するために、VM のネットワーク セキュリティ グループ (NSG) を見直します。 NSG は、許可されたトラフィックだけがアプリケーションに到達できるようにするために使用されます。
- また、このチームは、ディスク上のデータ保護のために、Azure Disk Encryption と Key Vault の使用も検討します。

詳細については、「[Azure における IaaS ワークロードのセキュリティに関するベスト プラクティス](https://docs.microsoft.com/azure/security/fundamentals/iaas)」を参照してください。

## <a name="business-continuity-and-disaster-recovery"></a>事業継続とディザスター リカバリー

事業継続とディザスター リカバリー (BCDR) のために、Contoso は次のアクションを実施します。

- データの安全性を確保する: Contoso は、[Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview) を使用して VM 上のデータをバックアップします。
- アプリケーションの稼働を維持する: Contoso は、[Site Recovery を使用して、Azure のアプリケーション VM をセカンダリ リージョンにレプリケート](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-quickstart)します。

### <a name="licensing-and-cost-optimization"></a>ライセンスとコストの最適化

- Contoso は、VM の既存のライセンスを所有しており、Azure ハイブリッド特典を活用します。 Contoso は、この特典を活用するために、既存の Azure VM を変換します。
- Contoso は、[Azure Cost Management および Billing](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview) を有効にして、Azure リソースの監視と管理を支援します。

## <a name="conclusion"></a>まとめ

この記事では、Contoso が Azure Migrate: Server Migration ツールを使用してアプリケーション VM を Azure VM に移行することによって、SmartHotel360 アプリケーションを Azure にリホストしました。サーバー移行ツール。
