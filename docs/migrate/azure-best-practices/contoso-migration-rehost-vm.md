---
title: Azure Migrate を使用してアプリケーションを Azure VM にリホストする
description: Contoso が、Azure Migrate サービスを使用して Azure へのオンプレミス マシンのリフトアンドシフト移行を実行し、オンプレミス アプリをリホストする方法について説明します。
author: givenscj
ms.author: abuck
ms.date: 07/01/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: migrate
ms.custom: think-tank
ms.openlocfilehash: 6a05051287765528ea34774b4a5cbfb12e575639
ms.sourcegitcommit: 54f01dd0eafa23c532e54c821954ba682357f686
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/13/2021
ms.locfileid: "98174405"
---
<!-- cSpell:ignore WEBVM SQLVM OSTICKETWEB OSTICKETMYSQL contosohost vcenter contosodc NSGs agentless -->

# <a name="rehost-an-on-premises-application-on-azure-vms-by-using-azure-migrate"></a>Azure Migrate を使用してオンプレミス アプリケーションを Azure VM にリホストする

この記事では、Contoso という架空の会社が、アプリケーション VM を Azure VM に移行することによって、VMware 仮想マシン (VM) 上で実行されている 2 層の Windows .NET フロントエンド アプリケーションをリホストする方法について説明します。

この例で使用される SmartHotel360 アプリケーションは、オープンソースとして提供されています。 独自のテスト目的に沿って使用する場合は、[GitHub](https://github.com/Microsoft/SmartHotel360) からダウンロードできます。

## <a name="business-drivers"></a>ビジネス ドライバー

IT リーダーシップ チームは、ビジネス パートナーと密接に連絡を取り合い、彼らがこの移行で何を達成しようとしているのかを理解しました。 目的は次のとおりです。

- **ビジネスの成長への対応。** Contoso は成長を続けています。そのため、会社のオンプレミスのシステムとインフラストラクチャに負荷がかかっています。
- **リスクの限定。** SmartHotel360 アプリケーションは、Contoso のビジネスに不可欠です。 同社は、リスクを伴わずにこのアプリケーションを Azure に移行したいと考えています。
- **拡張。** Contoso は、アプリケーションを変更することは望んでいませんが、アプリケーションの安定性を確保したいと考えています。

## <a name="migration-goals"></a>移行の目標

Contoso クラウド チームは、この移行の目標を設定しました。 これらの目標を使用して、最適な移行方法を決定しました。

- 移行後、Azure 内のアプリケーションは、現在の VMware の場合と同じパフォーマンスを発揮できる必要があります。 このアプリケーションは、オンプレミスにあるときと同様に、クラウドでも引き続き非常に重要です。
- このアプリケーションは Contoso にとって重要ですが、現時点では、同社はこれに投資することを望んでいません。 Contoso は、アプリケーションを現状のまま確実にクラウドに移すことを望んでいます。
- Contoso は、このアプリケーションの操作モデルを変更したくありません。 現在と同様に、クラウドで操作したいと考えています。
- Contoso は、アプリケーションの機能を変更することを望んでいません。 変わるのはアプリケーションの場所だけです。

## <a name="solution-design"></a>ソリューション設計

Contoso は、目標と要件を設定した後、デプロイ ソリューションを設計してレビューします。 また、移行に使用する Azure サービスなど、移行プロセスを確認します。

### <a name="current-application"></a>現在のアプリケーション

- アプリケーションは 2 つの VM (`WEBVM` と `SQLVM`) に階層化されています。
- これらの VM は、VMware ESXi ホスト `contosohost1.contoso.com` (バージョン 6.5) 上に配置されています。
- VMware 環境は、VM 上で実行中の vCenter Server 6.5 (`vcenter.contoso.com`) によって管理されています。
- Contoso には、オンプレミスのデータセンター (`contoso-datacenter`) があり、これにはオンプレミスのドメイン コントローラー (`contosodc1`) が含まれています。

### <a name="proposed-architecture"></a>提案されたアーキテクチャ

- このアプリケーションは運用ワークロードであるため、Azure のアプリケーション VM は、運用リソース グループ `ContosoRG` に配置されます。
- アプリケーション VM はプライマリ Azure リージョン (米国東部 2) に移行され、運用ネットワーク (`VNET-PROD-EUS2`) に配置されます。
- Web フロントエンド VM は、運用ネットワークのフロントエンド サブネット (`PROD-FE-EUS2`) に配置されます。
- データベース VM は、運用ネットワークのデータベース サブネット (`PROD-DB-EUS2`) に配置されます。
- Contoso データセンター内のオンプレミス VM は、移行が行われた後に使用停止にされます。

![シナリオのアーキテクチャを示す図。](./media/contoso-migration-rehost-vm/architecture.png)

### <a name="database-considerations"></a>データベースの考慮事項

Contoso はソリューション設計プロセスの一環として、Azure SQL Database と SQL Server の機能を比較しました。 次の点を考慮した結果、Azure IaaS VM 上で実行される SQL Server を使用することにしました。

- Contoso がオペレーティング システムとデータベースをカスタマイズする必要がある場合や、同じ VM 上にパートナーのアプリケーションを併置して実行する必要がある場合は、SQL Server を実行する Azure VM を使用するのが最適なソリューションと考えられます。
- ソフトウェア アシュアランスにより、Contoso は、SQL Server 向け Azure ハイブリッド特典を使用して、後で既存のライセンスと引き換えに、Azure SQL Managed Instance を割引料金で利用できます。 これにより、SQL Managed Instance を最大 30% 節約できます。

### <a name="solution-review"></a>ソリューションのレビュー

Contoso は、長所と短所の一覧をまとめて、提案されたデザインを評価します。

| 考慮事項 | 詳細 |
| --- | --- |
| **長所** | アプリケーション VM はどちらも変更なしで Azure に移行されるので、移行が簡単になります。 <br><br> Contoso は両方のアプリケーション VM にリフトアンドシフト アプローチを使用するため、アプリケーション データベース用に特別な構成や移行ツールは不要です。 <br><br> Contoso は、Azure ハイブリッド特典を使用することで、ソフトウェア アシュアランスへの投資を活用できます。 <br><br> Contoso は、Azure でアプリケーション VM の完全な制御を維持できます。 |
| **短所** | `WEBVM` と `SQLVM` では、Windows Server 2008 R2 が実行されています。 Azure では、特定のロールを対象にこのオペレーティング システムがサポートされます。 [詳細については、こちらを参照してください](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)。 <br><br> アプリケーションの Web 層とデータ層は、引き続き単一障害点となります。 <br><br> `SQLVM` は、SQL Server 2008 R2 で実行されています。 SQL Server 2008 R2 はメインストリーム サポートの対象外になりましたが、Azure VM ではサポートされています。 [詳細については、こちらを参照してください](/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-2008-eos-extend-support)。 <br><br> Contoso は、Azure App Service や Azure SQL Database などのマネージド サービスに移行するのではなく、引き続き Azure VM 上でアプリケーションをサポートする必要があります。 |

### <a name="migration-process"></a>移行プロセス

Contoso は、Azure Migrate のエージェントレス方式を使用して、アプリケーション フロントエンド VM とアプリケーション データベース VM を Azure VM に移行します。サーバー移行ツール。

- 最初の手順として、Azure Migrate:Server Migration 用の Azure コンポーネントを準備および設定し、オンプレミスの VMware インフラストラクチャを準備します。
- [Azure インフラストラクチャ](./contoso-migration-infrastructure.md)があるので、Contoso では、Azure Migrate: Server Migration ツールを使用して VM のレプリケーションの構成を行うだけで済みますサーバー移行ツール。
- すべての準備ができたら、VM のレプリケートを開始できます。
- レプリケーションが有効になり、機能するようになったら、Contoso は移行をテストし、成功したら Azure にフェールオーバーして、VM を移行します。

![移行プロセスの手順を示す図。](./media/contoso-migration-rehost-vm/migration-process-az-migrate.png)

### <a name="azure-services"></a>Azure サービス

| サービス | 説明 | コスト |
| --- | --- | --- |
| [Azure Migrate: Server Migration](/azure/migrate/contoso-migration-rehost-vm) | このサービスでは、オンプレミスのアプリケーションとワークロード、および Amazon Web Services (AWS) と Google Cloud Platform (GCP) VM のインスタンスの移行を調整、管理します。 | Azure へのレプリケーションの間に、Azure Storage の料金が発生します。 移行が行われ、VM が Azure で実行されている場合、Azure VM が作成され、料金が発生します。 [料金と価格](https://azure.microsoft.com/pricing/details/azure-migrate)の詳細をご覧ください。  |

## <a name="prerequisites"></a>前提条件

Contoso と他のユーザーは、このシナリオの次の前提条件を満たす必要があります。

| 必要条件 | 詳細 |
| --- | --- |
| **Azure サブスクリプション** | このシリーズの先行する記事の中で、Contoso はサブスクリプションを作成しました。 Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/free)を作成してください。 <br><br> 無料アカウントを作成する場合、サブスクリプションの管理者としてすべてのアクションを実行できます。 <br><br> 既存のサブスクリプションを使用する場合に、自分が管理者でないようであれば、管理者と連携して所有者または共同作成者のアクセス許可を自分に割り当てます。 <br><br> より詳細なアクセス許可が必要な場合は、[Azure のロールベースのアクセス制御を使用した Site Recovery のアクセス管理](/azure/site-recovery/site-recovery-role-based-linked-access-control)に関する記事を参照してください。 |
| **Azure インフラストラクチャ** | Contoso が [Azure インフラストラクチャを設定する方法](./contoso-migration-infrastructure.md)を確認します。 <br><br> 以下についての具体的な[前提条件](./contoso-migration-devtest-to-iaas.md#prerequisites)をご確認ください。Azure Migrate:Server Migration に関するエラーのトラブルシューティングに役立つ情報を提供しています。 |
| **オンプレミスのサーバー** | オンプレミスの vCenter Server は、バージョン 5.5、6.0、6.5、または 6.7 を実行している必要があります。 <br><br> ESXi ホストは、バージョン 5.5、6.0、6.5、または 6.7 を実行している必要があります。 <br><br> ESXi ホスト上で 1 つ以上の VMware VM が実行されている必要があります。 |

## <a name="scenario-steps"></a>シナリオのステップ

Contoso の管理者が移行を実行する方法を次に示します。

> [!div class="checklist"]
>
> - **ステップ 1:Azure Migrate: Server Migration 用の Azure を準備するServer Migration.** Azure Migrate プロジェクトにサーバー移行ツールを追加します。
> - **手順 2:オンプレミスの VM をレプリケートする。** レプリケーションを設定し、Azure Storage への VM のレプリケーションを開始します。
> - **ステップ 3:Azure Migrate: Server Migration で VM を移行する。** テスト移行を実行してすべてが機能していることを確認した後、完全移行を実行して VM を Azure に移動します。

## <a name="step-1-prepare-azure-for-azure-migrate-server-migration"></a>手順 1:Azure Migrate: Server Migration 用の Azure を準備するServer Migration

VM を Azure に移行するには、移行中に作成される Azure VM が配置される仮想ネットワークが必要です。 また、プロビジョニングおよび構成済みの Azure Migrate: Server Migration ツール (OVA ファイル) も必要です。

1. ネットワークをセットアップします。 Contoso は、[Azure インフラストラクチャのデプロイ](./contoso-migration-infrastructure.md)を行ったときに、Azure Migrate: Server Migration に使用できるネットワークを既にセットアップしています。

    - SmartHotel360 アプリケーションは運用アプリケーションであり、VM はプライマリ リージョン (`East US 2`) の Azure 運用ネットワーク (`VNET-PROD-EUS2`) に移行されます。
    - どちらの VM も、運用リソースに使用される `ContosoRG` リソース グループに配置されます。
    - アプリケーション フロントエンド VM (`WEBVM`) は、運用ネットワークのフロントエンド サブネット (`PROD-FE-EUS2`) に移行されます。
    - アプリケーション データベース VM (`SQLVM`) は、運用ネットワークのデータベース サブネット (`PROD-DB-EUS2`) に移行されます。

1. Azure Migrate: Server Migration ツールをプロビジョニングしますサーバー移行ツール。

    1. Azure Migrate から、OVA イメージをダウンロードし、VMware にインポートします。

       ![OVA ファイルのダウンロード ボタンを示すスクリーンショット。](./media/contoso-migration-rehost-vm/migration-download-ova.png)

    1. インポートしたイメージを起動し、次のステップを含めてツールを構成します。

       - 前提条件を設定します。

         ![前提条件となるライセンス条項を設定するための領域を示すスクリーンショット。](./media/contoso-migration-rehost-vm/migration-setup-prerequisites.png)

       - ツールを Azure サブスクリプションにポイントします。

         ![Azure Migrate に登録するための選択項目を示すスクリーンショット。](./media/contoso-migration-rehost-vm/migration-register-azure.png)

       - VMware vCenter の資格情報を設定します。

         ![vCenter サーバーを指定するための選択項目を示すスクリーンショット。](./media/contoso-migration-rehost-vm/migration-vcenter-server.png)

       - 検出用の Windows ベースの資格情報を追加します。

         ![仮想マシン上のアプリケーションと依存関係を検出するための領域のスクリーンショット。](./media/contoso-migration-rehost-vm/migration-credentials.png)

構成が完了すると、ツールによりすべての VM が列挙されます。 このプロセスが完了すると、Azure の Azure Migrate ツールにそれらが設定されていることがわかります。

**さらにサポートが必要な場合**

[Azure Migrate: Server Migration ツール](/azure/migrate/migrate-services-overview#azure-migrate-server-migration-tool)の設定についてご確認ください。

### <a name="prepare-on-premises-vms"></a>オンプレミスの VM を準備する

Contoso は移行後、Azure VM に接続し、Azure で VM を管理できるようにしたいと考えています。 Contoso の管理者は、移行前に次の手順を実行する必要があります。

1. インターネット経由でアクセスする場合:

    - 移行前に、オンプレミス VM で RDP または SSH を有効にします。
    - **パブリック** プロファイルに TCP 規則と UDP 規則が追加されていることを確認します。
    - オペレーティング システムのファイアウォールで RDP または SSH が許可されていることを確認します。

2. サイト間 VPN 経由でアクセスする場合:

    - 移行前に、オンプレミス VM で RDP または SSH を有効にします。
    - オペレーティング システムのファイアウォールで RDP または SSH が許可されていることを確認します。
    - Windows の場合、オンプレミス VM 上のオペレーティング システムの SAN ポリシーを **OnlineAll** に設定します。

3. [Azure Windows エージェント](/azure/virtual-machines/extensions/agent-windows)をインストールします。

その他の考慮事項

- Windows の場合、移行をトリガーするときに、VM 上に保留中の Windows 更新プログラムが存在しないようにする必要があります。 ある場合、管理者は更新が完了するまで、VM にログインすることはできません。
- 移行後、管理者は **ブート診断** を調べて、VM のスクリーンショットを確認できます。 これが機能しない場合は、VM が実行中であることを確認し、こちらの[トラブルシューティングのヒント](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)を参照してください。

**さらにサポートが必要な場合**

[移行に向けて VM を準備する](/azure/migrate/prepare-for-migration)方法をご覧ください。

## <a name="step-2-replicate-the-on-premises-vms"></a>手順 2:オンプレミスの VM をレプリケートする

Contoso の管理者は、Azure への移行を実行する前に、レプリケーションを設定して有効にする必要があります。

検出が完了したら、Azure への VMware VM のレプリケーションを開始できます。

1. Azure Migrate プロジェクトで、 **[サーバー]**  >  **[Azure Migrate:Server Migration]** に移動します。 次に、 **[レプリケート]** を選択します。

    ![仮想マシンをレプリケートするための選択項目のスクリーンショット。](./media/contoso-migration-rehost-vm/select-replicate.png)

2. **[レプリケート]**  >  **[ソースの設定]**  >  **[マシンは仮想化されていますか?]** で、 **[はい (VMware vSphere の場合)]** を選択します。

3. **[オンプレミスのアプライアンス]** で、設定した Azure Migrate アプライアンスの名前を選択し、 **[OK]** を選択します。

    ![ソースの設定を示すスクリーンショット。](./media/contoso-migration-rehost-vm/source-settings.png)

4. **[仮想マシン]** で、レプリケートするマシンを選択します。
    - VM の評価を実行した場合は、評価結果から VM のサイズ設定とディスクの種類 (Premium または Standard) の推奨事項を適用できます。 これを行うには、 **[Azure Migrate Assessment から移行設定をインポートしますか?]** で **[はい]** オプションを選択します。
    - 評価を実行しなかった場合、または評価の設定を使用しない場合は、 **[いいえ]** オプションを選択します。
    - 評価の使用を選択した場合は、VM グループと評価名を選択します。

    ![移行する仮想マシンを選択するためのボックスを示すスクリーンショット。](./media/contoso-migration-rehost-vm/select-assessment.png)

5. **[仮想マシン]** で、必要に応じて VM を検索し、移行する各 VM を確認します。 次に、 **[次のステップ: ターゲット設定]** をクリックします。

6. **[ターゲット設定]** で、移行先のサブスクリプションとターゲット リージョンを選択します。 次に、移行後に Azure VM が属するリソース グループを指定します。 **[仮想ネットワーク]** で、移行後に Azure VM の参加先となる Azure 仮想ネットワークまたはサブネットを選択します。

7. **[Azure ハイブリッド特典]** で、

    - Azure ハイブリッド特典を適用しない場合は、 **[いいえ]** を選択します。 **[次へ]** を選択します。
    - アクティブなソフトウェア アシュアランスまたは Windows Server サブスクリプションの対象となっている Windows Server マシンがあり、移行するマシンに特典を適用する場合は、 **[はい]** を選択します。 **[次へ]** を選択します。

8. **[コンピューティング]** で、VM の名前、サイズ、OS ディスクの種類、可用性セットを確認します。 VM は [Azure の要件](/azure/migrate/migrate-support-matrix-vmware#vmware-requirements)に準拠している必要があります。

    - **VM サイズ:** 評価の推奨事項を使用している場合は、[VM サイズ] ドロップダウン リストに推奨サイズが表示されます。 それ以外の場合は、Azure Migrate によって、Azure サブスクリプション内の最も近いサイズが選択されます。 または、 **[Azure VM サイズ]** でサイズを手動で選択します。
    - **OS ディスク:** VM の OS (ブート) ディスクを指定します。 OS ディスクには、オペレーティング システムのブートローダーとインストーラーが含まれます。
    - **可用性セット:** 移行後に VM を Azure 可用性セットに配置する必要がある場合は、セットを指定します。 このセットは、移行用に指定するターゲット リソース グループ内に存在する必要があります。

9. **[ディスク]** で、VM ディスクを Azure にレプリケートするかどうかを指定し、Azure でのディスクの種類 (Standard SSD か HDD、または Premium マネージド ディスク) を選択します。 **[次へ]** を選択します。

   レプリケーションからディスクを除外できます。 ディスクを除外すると、移行後に Azure VM 上に存在しなくなります。

10. **[レプリケーションの確認と開始]** で設定を確認し、 **[レプリケート]** を選択して、サーバーの初期レプリケーションを開始します。

> [!NOTE]
> レプリケーションを開始する前であれば、 **[管理]**  >  **[マシンのレプリケート]** でレプリケーションの設定をいつでも更新できます。 レプリケーションの開始後は、設定を変更することができません。

## <a name="step-3-migrate-the-vms-with-azure-migrate-server-migration"></a>手順 3:Azure Migrate: Server Migration で VM を移行するServer Migration

Contoso の管理者は、クイック テスト移行を実行した後、完全移行を実行して VM を移行します。

### <a name="run-a-test-migration"></a>テスト移行を実行する

1. **[移行の目標]**  >  **[サーバー]**  >  **[Azure Migrate: Server Migration]** で、 **[移行されたサーバーをテストします]** を選択します。

    ![移行されたサーバーのテストを開始するためのボタンのスクリーンショット。](./media/contoso-migration-rehost-vm/test-migrated-servers.png)

2. テストする VM を選択したまま (または右クリックし)、 **[テスト移行]** を選択します。

    ![選択された仮想マシントと移行テストを開始するためのボタンのスクリーンショット。](./media/contoso-migration-rehost-vm/test-migrate.png)

3. **[テスト移行]** で、移行後に Azure VM が配置される Azure 仮想ネットワークを選択します。 非運用環境の仮想ネットワークを使用することをお勧めします。
4. **テスト移行** ジョブが開始されます。 ポータルの通知でジョブを監視します。
5. 移行の完了後、Azure portal の **[仮想マシン]** で、移行された Azure VM を確認します。 マシン名には、 **-Test** サフィックスが含まれています。
6. テストが完了したら、 **[マシンのレプリケート]** で Azure VM を選択したまま (または右クリックし)、 **[テスト移行をクリーンアップ]** を選択します。

    ![移行をクリーンアップするための選択項目のスクリーンショット。](./media/contoso-migration-rehost-vm/clean-up.png)

### <a name="migrate-the-vms"></a>VM を移行する

次に、Contoso の管理者は、完全移行を実行します。

1. Azure Migrate プロジェクトで、 **[サーバー]**  >  **[Azure Migrate:Server Migration]**  >  **[サーバーをレプリケートしています]** の順に選択します。

    ![サーバーをレプリケートするための選択項目のスクリーンショット。](./media/contoso-migration-rehost-vm/replicating-servers.png)

2. **[マシンのレプリケート]** で VM を選択したまま (または右クリックし)、 **[移行]** を選択します。
3. **[移行]**  >  **[仮想マシンをシャットダウンし、データ損失のない計画された移行を実行しますか]** で、 **[はい]**  >  **[OK]** の順に選択します。

    既定では、Azure Migrate によってオンプレミスの VM がシャットダウンされ、前回のレプリケーションの後に行われた VM の変更を同期するためにオンデマンド レプリケーションが実行されます。 こうすることで、データ損失がなくなります。 VM をシャットダウンしたくない場合は、 **[いいえ]** を選択します。
4. VM に対して移行ジョブが開始されます。 Azure 通知でジョブを追跡します。
5. ジョブが完了したら、 **[仮想マシン]** ページで VM を表示して管理できます。

**さらにサポートが必要な場合**

- [テスト移行を実行する](/azure/migrate/tutorial-migrate-vmware#run-a-test-migration)方法をご覧ください。
- [VM を Azure に移行する](/azure/migrate/tutorial-migrate-vmware#migrate-vms)方法をご覧ください。

## <a name="clean-up-after-migration"></a>移行後にクリーンアップする

移行が完了し、SmartHotel360 のアプリケーション層が Azure VM 上で実行されています。

次に、Contoso は、以下のクリーンアップ手順を実行する必要があります。

- 移行が完了したら、レプリケーションを停止します。
- `WEBVM` マシンを vCenter インベントリから削除します。
- `SQLVM` マシンを vCenter インベントリから削除します。
- ローカル バックアップ ジョブから `WEBVM` と `SQLVM` を削除します。
- VM の新しい場所と IP アドレスを示すように内部ドキュメントを更新します。
- VM と対話しているリソースを確認し、すべての関連する設定またはドキュメントを新しい構成を反映するように更新します。

## <a name="review-the-deployment"></a>デプロイを再調査する

アプリケーションが実行されるようになったので、Contoso は、Azure でアプリケーションを完全に操作可能にし、セキュリティで保護する必要があります。

### <a name="security"></a>セキュリティ

Contoso セキュリティ チームは、Azure VM を調査して、セキュリティの問題を特定します。 アクセスを制御するために、VM のネットワーク セキュリティ グループ (NSG) を見直します。 NSG は、許可されたトラフィックだけがアプリケーションに到達できるようにするために使用されます。 チームは、Azure Disk Encryption と Key Vault を使用して、ディスク上のデータをセキュリティで保護することも検討します。

詳細については、「[Azure における IaaS ワークロードのセキュリティに関するベスト プラクティス](/azure/security/fundamentals/iaas)」を参照してください。

## <a name="business-continuity-and-disaster-recovery"></a>事業継続とディザスター リカバリー

事業継続とディザスター リカバリーのために、Contoso は次のアクションを実施します。

- データの安全性を確保する: Contoso は、[Azure Backup を使用して VM 上のデータをバックアップ](/azure/backup/backup-overview)します。
- アプリケーションの稼働を維持する: Contoso は、[Azure Site Recovery を使用して、Azure 内のアプリケーション VM をセカンダリ リージョンにレプリケート](/azure/site-recovery/azure-to-azure-quickstart)します。

### <a name="licensing-and-cost-optimization"></a>ライセンスとコストの最適化

Contoso は VM の既存のライセンスを所有しているので、Azure ハイブリッド特典を活用します。 Contoso は、この特典を活用するために、既存の Azure VM を変換します。

Contoso は、[Azure Cost Management および Billing](/azure/cost-management-billing/cost-management-billing-overview) を有効にして、Azure リソースの監視と管理を支援します。

## <a name="conclusion"></a>まとめ

この記事では、Contoso が SmartHotel360 アプリケーションを Azure にリホストしました。 管理者は、Azure Migrate を使用して、アプリケーション VM を Azure VM に移行しました。サーバー移行ツール。
また、[DevOps ジェネレーター](https://aka.ms/adopt/plan/generator)で公開されている Azure DevOps プロジェクトを参照することもできます。 ジェネレーターで、クラウド導入フレームワークのナビゲーションの下にある[サーバー移行プロジェクト](https://azuredevopsdemogenerator.azurewebsites.net/?name=servermigration)をダウンロードします。 
