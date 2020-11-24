---
title: オンプレミスの Linux アプリケーションを Azure VM にリホストする
description: Contoso が、Azure VM に移行することによってオンプレミス Linux アプリをどのようにリホストするかを説明します。
author: deltadan
ms.author: abuck
ms.date: 07/01/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: migrate
ms.openlocfilehash: 4738ff138df700047cffb478518d40a8aa6d4ff0
ms.sourcegitcommit: a7eb2f6c4465527cca2d479edbfc9d93d1e44bf1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2020
ms.locfileid: "94713260"
---
<!-- cSpell:ignore OSTICKETWEB OSTICKETMYSQL OSTICKETWEB OSTICKETMYSQL contosohost vcenter contosodc osTicket binlog systemctl NSGs distros -->

# <a name="rehost-an-on-premises-linux-application-to-azure-vms"></a>オンプレミスの Linux アプリケーションを Azure VM にリホストする

この記事では、Contoso という架空の会社が、Azure IaaS (サービスとしてのインフラストラクチャ) 仮想マシン (VM) を使用して、[LAMP ベース](https://wikipedia.org/wiki/LAMP_(software_bundle))の 2 層アプリケーションをリホストする方法について説明します。

この例で使用されるサービス デスク アプリケーションである osTicket は、オープンソースとして提供されています。 独自のテスト目的に沿って使用する場合は、[GitHub](https://github.com/osTicket/osTicket) からダウンロードできます。

## <a name="business-drivers"></a>ビジネス ドライバー

IT リーダーシップ チームは、ビジネス パートナーと密接に連絡を取り合い、彼らがこの移行で何を達成しようとしているのかを理解しました。

- **ビジネスの成長への対応。** Contoso は成長を続けています。その結果、オンプレミスのシステムとインフラストラクチャに負荷がかかっています。
- **リスクの限定。** このサービス デスク アプリケーションは、Contoso のビジネスにとって非常に重要です。 Contoso は、リスクを負うことなく Azure に移行したいと考えています。
- **拡張。** Contoso は、アプリケーションに今すぐ変更を加えることは望んでいません。 アプリケーションの安定性を確保することを望んでいます。

## <a name="migration-goals"></a>移行の目標

Contoso クラウド チームは、最適な移行方法を決定するために、この移行の目標を明確にしました。

- 移行後、Azure 内のアプリケーションは、現在のオンプレミスの VMware 環境内と同じパフォーマンスを発揮できる必要があります。 このアプリケーションは、オンプレミスにあるときと同様に、クラウドでも引き続き非常に重要です。
- Contoso は、このアプリケーションに投資することは望んでいません。 これはビジネスにとって重要なものですが、現状のまま確実にクラウドに移すことだけを望んでいます。
- Contoso は、このアプリケーションの操作モデルを変更したくありません。 現在と同様に、クラウドでアプリケーションを操作したいと考えています。
- Contoso は、アプリケーションの機能を変更したくありません。 変えるのはアプリケーションの場所のみです。
- Windows アプリケーションの移行を何件か完了したら、Contoso は Azure で Linux ベースのインフラストラクチャを使用する方法を学びたいと考えています。

## <a name="solution-design"></a>ソリューション設計

Contoso は、目標と要件を明確にした後、デプロイ ソリューションを設計してレビューし、移行プロセスを確認します。 Contoso が移行に使用する Azure サービスも確認します。

### <a name="current-application"></a>現在のアプリケーション

- osTicket アプリケーションは、2 つの VM (`OSTICKETWEB` と `OSTICKETMYSQL`) に階層化されています。
- これらの VM は、VMware ESXi ホスト `contosohost1.contoso.com` (バージョン 6.5) 上に配置されています。
- VMware 環境は vCenter Server 6.5 (`vcenter.contoso.com`) によって管理され、VM 上で実行されています。
- Contoso には、オンプレミスのデータセンター (`contoso-datacenter`) があり、これにはオンプレミスのドメイン コントローラー (`contosodc1`) が含まれています。

### <a name="proposed-architecture"></a>提案されたアーキテクチャ

- このアプリケーションは運用ワークロードであるため、Azure 内の VM は運用リソース グループ `ContosoRG` に配置されます。
- VM はプライマリ リージョン (米国東部 2) に移行され、運用ネットワーク (`VNET-PROD-EUS2`) 内に配置されます。
  - Web VM は、フロントエンド サブネット (`PROD-FE-EUS2`) 内に存在することになります。
  - データベース VM は、データベース サブネット (`PROD-DB-EUS2`) 内に存在することになります。
- Contoso データセンター内のオンプレミス VM は、移行が行われた後に使用停止にされます。

![シナリオ アーキテクチャの図。](./media/contoso-migration-rehost-linux-vm/architecture.png)

### <a name="solution-review"></a>ソリューションのレビュー

Contoso は、長所と短所の一覧をまとめて、提案されたデザインを評価します。

| 考慮事項 | 詳細 |
| --- | --- |
| **長所** | アプリケーション VM はどちらも変更なしで Azure に移されるので、移行が簡単になります。 <br><br> Contoso は両方のアプリケーション VM にリフトアンドシフト アプローチを使用するため、アプリケーション データベース用に特別な構成や移行ツールは不要です。 <br><br> Contoso は、Azure で引き続きアプリケーションの VM を完全に制御できます。 <br><br> アプリケーションの VM では、動作保証済みの Linux ディストリビューション Ubuntu 16.04-TLS が実行されています。 [Azure で動作保証済みの Linux ディストリビューション](/azure/virtual-machines/linux/endorsed-distros)の詳細をご覧ください。 |
| **短所** | アプリケーションの Web 層とデータ層は、依然として単一フェールオーバー ポイントになります。 <br><br> Contoso は、Azure App Service や Azure Database for MySQL などのマネージド サービスに移行するのではなく、引き続き Azure VM としてアプリケーションをサポートする必要があります。 <br><br> Contoso は、リフトアンドシフトの VM 移行によって単純化すると、[Azure Database for MySQL](/azure/mysql/overview) の特長を最大限に活用できないことを認識しています。 これらの特長には、組み込みの高可用性、予測可能なパフォーマンス、単純なスケーリング、自動バックアップ、組み込みのセキュリティなどがあります。 |

### <a name="migration-process"></a>移行プロセス

Contoso は、次のようにして移行プロセスを完了します。

- 最初の手順として、Azure Migrate:Server Migration 用の Azure コンポーネントを準備および設定し、オンプレミスの VMware インフラストラクチャを準備します。
- 同社には、[Azure インフラストラクチャ](./contoso-migration-infrastructure.md)が既に用意されているので、Azure Migrate: Server Migration ツールを使用して VM のレプリケーションを構成するだけで済みます。
- すべての準備ができたら、VM のレプリケートを開始できます。
- レプリケーションを有効にしたら、Azure へのフェールオーバーによって VM を移行します。

![移行プロセスの図。](./media/contoso-migration-rehost-linux-vm/migration-process-az-migrate.png)

### <a name="azure-services"></a>Azure サービス

| サービス | 説明 | コスト |
| --- | --- | --- |
| [Azure Migrate: Server Migration](/azure/migrate/contoso-migration-rehost-linux-vm) | このサービスは、オンプレミスのアプリケーションとワークロード、および Amazon Web Services (AWS) と Google Cloud Platform (GCP) VM のインスタンスの移行を調整、管理します。 | Azure へのレプリケーションの間に、Azure Storage の料金が発生します。 移行が行われると、Azure VM が作成されて料金がかかります。 [料金と価格](https://azure.microsoft.com/pricing/details/azure-migrate)の詳細をご覧ください。 |

## <a name="prerequisites"></a>前提条件

このシナリオで Contoso に必要なことを以下に示します。

必要条件 | 詳細 |
| --- | --- |
| **Azure サブスクリプション** | このシリーズの先行する記事の中で、Contoso はサブスクリプションを作成しました。 Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/free)を作成してください。 <br><br> 無料アカウントを作成する場合、サブスクリプションの管理者としてすべてのアクションを実行できます。 <br><br> 既存のサブスクリプションを使用する場合に、自分が管理者でないようであれば、管理者と連携して所有者または共同作成者のアクセス許可を自分に割り当てます。 <br><br> より詳細なアクセス許可が必要な場合は、[ロールベースのアクセス制御 (RBAC) を使用した Azure Site Recovery のアクセスの管理](/azure/site-recovery/site-recovery-role-based-linked-access-control)に関する記事をご覧ください。 |
| **Azure インフラストラクチャ** | [Contoso が Azure インフラストラクチャをセットアップする](./contoso-migration-infrastructure.md)方法を確認します。 <br><br> 以下についての具体的な[前提条件](./contoso-migration-devtest-to-iaas.md#prerequisites)をご確認ください。Azure Migrate:Server Migration に関するエラーのトラブルシューティングに役立つ情報を提供しています。 |
| **オンプレミスのサーバー** | オンプレミス vCenter Server では、バージョン 5.5、6.0、または 6.5 を実行している必要があります。 <br><br> バージョン 5.5、6.0、または 6.5 を実行している ESXi ホスト。 <br><br> ESXi ホスト上で実行している 1 つ以上の VMware VM。 |
| **オンプレミスの VM** | Azure での実行が保証されている [Linux ディストリビューションを確認します](/azure/virtual-machines/linux/endorsed-distros)。 |

## <a name="scenario-steps"></a>シナリオのステップ

Contoso は、次のようにして移行を完了します。

> [!div class="checklist"]
>
> - **ステップ 1:Azure Migrate: Server Migration 用の Azure を準備するServer Migration.** Azure Migrate:Server Migration ツールを Azure Migrate プロジェクトに追加します。
> - **手順 2:Azure Migrate: Server Migration 用のオンプレミス VMware を準備するServer Migration.** VM 検出用のアカウントを準備し、移行後に Azure VM に接続するための準備をします。
> - **ステップ 3:VM をレプリケートする。** レプリケーションを設定し、Azure Storage への VM のレプリケーションを開始します。
> - **手順 4:Azure Migrate: Server Migration で VM を移行するServer Migration.** テスト移行を実行してすべてが機能していることを確認した後、移行を実行して VM を Azure に移動します。

## <a name="step-1-prepare-azure-for-the-azure-migrate-server-migration-tool"></a>手順 1:Azure Migrate: Server Migration ツール用の Azure を準備するServer Migration ツール

Contoso が VM を Azure に移行するには、以下の Azure コンポーネントが必要です。

- 移行中に作成される Azure VM が配置される仮想ネットワーク。
- Azure Migrate: Server Migration ツール (プロビジョニング済みのもの)。

これらのコンポーネントを次のように設定します。

1. ネットワークをセットアップします。 Contoso は、[Azure インフラストラクチャをデプロイ](./contoso-migration-infrastructure.md)したときに、Azure Migrate: Server Migration に使用できるネットワークを既にセットアップしています。

    - SmartHotel360 アプリケーションは運用アプリケーションです。 VM は、プライマリ リージョン (`East US 2`) の Azure 運用ネットワーク (`VNET-PROD-EUS2`) に移行されます。
    - 両方の VM は、運用リソースのために使用される `ContosoRG` リソース グループに配置されます。
    - アプリケーション フロントエンド VM (`OSTICKETWEB`) は、運用ネットワークのフロントエンド サブネット (`PROD-FE-EUS2`) に移行されます。
    - アプリケーション データベース VM (`OSTICKETMYSQL`) は、運用ネットワークのデータベース サブネット (`PROD-DB-EUS2`) に移行されます。

1. Azure Migrate: Server Migration ツールをプロビジョニングしますサーバー移行ツール。 ネットワークとストレージ アカウントが用意できたので、Recovery Services コンテナー (`ContosoMigrationVault`) を作成し、それをプライマリ リージョン (`East US 2`) の `ContosoFailoverRG` リソース グループに配置します。

    ![Azure Migrate Server Migration ツールを示すスクリーンショット](./media/contoso-migration-rehost-linux-vm/server-migration-tool.png)

**さらにサポートが必要な場合**

[Azure Migrate: Server Migration ツール](/azure/migrate)の設定についてご確認ください。

## <a name="step-2-prepare-on-premises-vmware-for-azure-migrate-server-migration"></a>手順 2:Azure Migrate: Server Migration 用のオンプレミス VMware を準備するServer Migration

Contoso は、Azure への移行後に、Azure 内のレプリケートされた VM に接続できることを望んでいます。 Contoso の管理者は次のことを行う必要があります。

- Azure VM にインターネット経由でアクセスするには、移行前に、オンプレミス Linux VM 上で SSH を有効にします。 Ubuntu の場合、`sudo apt-get ssh install -y` コマンドを使用してこの手順を実行できます。
- [Azure Linux エージェント](/azure/virtual-machines/extensions/agent-linux)をインストールします。
- 移行を実行したら、**ブート診断** を調べて、VM のスクリーンショットを表示できます。
- これが機能しない場合は、VM が実行中であることを確認し、こちらの[トラブルシューティングのヒント](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)を参照する必要があります。

**さらにサポートが必要な場合**

[移行に向けて VM を準備する](/azure/migrate/prepare-for-migration)方法をご覧ください。

## <a name="step-3-replicate-the-on-premises-vms"></a>手順 3:オンプレミスの VM をレプリケートする

Contoso の管理者は、Azure への移行を実行する前に、レプリケーションを設定して有効にする必要があります。

検出が完了したら、Azure への VMware VM のレプリケーションを開始します。

1. Azure Migrate プロジェクトで、 **[サーバー]**  >  **[Azure Migrate:Server Migration]** の順に移動し、 **[レプリケート]** を選択します。

    ![[レプリケート] オプションを示すスクリーンショット。](./media/contoso-migration-rehost-linux-vm/select-replicate.png)

2. **[レプリケート]**  >  **[ソースの設定]**  >  **[マシンは仮想化されていますか?]** で、 **[はい (VMware vSphere の場合)]** を選択します。

3. **[オンプレミスのアプライアンス]** で、設定した Azure Migrate アプライアンスの名前を選択し、 **[OK]** を選択します。

    ![[ソースの設定] タブを示すスクリーンショット。](./media/contoso-migration-rehost-linux-vm/source-settings.png)

4. **[仮想マシン]** で、レプリケートしたいマシンを選択します。
    - VM の評価を実行した場合は、評価結果から VM のサイズ設定とディスクの種類 (Premium または Standard) の推奨事項を適用できます。 **[Azure Migrate Assessment から移行設定をインポートしますか?]** で、 **[はい]** オプションを選択します。
    - 評価を実行しなかった場合、または評価の設定を使用しない場合は、 **[いいえ]** オプションを選択します。
    - 評価の使用を選択した場合は、VM グループと評価名を選択します。

    ![評価の選択を示すスクリーンショット。](./media/contoso-migration-rehost-linux-vm/select-assessment.png)

5. **[仮想マシン]** で、必要に応じて VM を検索し、移行する各 VM を選択します。 次に、 **[次のステップ: ターゲット設定]** をクリックします。

6. **[ターゲット設定]** で、移行先のサブスクリプションとターゲット リージョンを選択します。 移行後に Azure VM が属するリソース グループを指定します。 **[仮想ネットワーク]** で、移行後に Azure VM の参加先となる Azure 仮想ネットワークまたはサブネットを選択します。

7. **[Azure ハイブリッド特典]** で、

    - Azure ハイブリッド特典を適用しない場合は、 **[いいえ]** を選択します。 **[次へ]** を選択します。
    - アクティブなソフトウェア アシュアランスまたは Windows Server サブスクリプションの対象となっている Windows Server マシンがあり、移行するマシンに特典を適用する場合は、 **[はい]** を選択します。 **[次へ]** を選択します。

8. **[コンピューティング]** で、VM の名前、サイズ、OS ディスクの種類、可用性セットを確認します。 VM は [Azure の要件](/azure/migrate/migrate-support-matrix-vmware#vmware-requirements)に準拠している必要があります。

    - **VM サイズ:** 評価の推奨事項を使用している場合は、[VM サイズ] ドロップダウン リストに推奨サイズが表示されます。 それ以外の場合は、Azure Migrate によって、Azure サブスクリプション内の最も近いサイズが選択されます。 または、 **[Azure VM サイズ]** でサイズを手動で選択します。
    - **OS ディスク:** VM の OS (ブート) ディスクを指定します。 OS ディスクは、オペレーティング システムのブートローダーとインストーラーがあるディスクです。
    - **可用性セット:** 移行後に VM を Azure 可用性セットに配置する必要がある場合は、セットを指定します。 このセットは、移行用に指定するターゲット リソース グループ内に存在する必要があります。

9. **[ディスク]** で、VM ディスクを Azure にレプリケートするかどうかを指定します。 Azure でのディスクの種類 (Standard SSD または HDD、あるいは Premium マネージド ディスク) を選択します。 **[次へ]** を選択します。
    - レプリケーションからディスクを除外できます。
    - ディスクを除外すると、移行後に Azure VM 上に存在しなくなります。

10. **[レプリケーションの確認と開始]** で、設定を確認します。 次に、 **[レプリケート]** を選択して、サーバーの初期レプリケーションを開始します。

> [!NOTE]
> レプリケーションを開始する前であれば、 **[管理]**  >  **[マシンのレプリケート]** でレプリケーションの設定をいつでも更新できます。 レプリケーションの開始後は、設定を変更することができません。

## <a name="step-4-migrate-the-vms"></a>手順 4:VM を移行する

Contoso の管理者は、クイック テスト移行を実行した後、移行を実行して VM を移動します。

### <a name="run-a-test-migration"></a>テスト移行を実行する

1. **[移行の目標]**  >  **[サーバー]**  >  **[Azure Migrate: Server Migration]** で、 **[移行されたサーバーをテストします]** を選択します。

     ![[移行されたサーバーをテストします] オプションを示すスクリーンショット。](./media/contoso-migration-rehost-linux-vm/test-migrated-servers.png)

1. テストする VM を選択したままにします (または右クリックします)。 次に、 **[テスト移行]** を選択します。

    ![[テスト移行] 項目を示すスクリーンショット。](./media/contoso-migration-rehost-linux-vm/test-migrate.png)

1. **[テスト移行]** で、移行後に Azure VM が配置される Azure 仮想ネットワークを選択します。 非運用環境の仮想ネットワークを使用することをお勧めします。
1. **テスト移行** ジョブが開始されます。 ポータルの通知でジョブを監視します。
1. 移行の完了後、Azure portal の **[仮想マシン]** で、移行された Azure VM を確認します。 マシン名には、サフィックス **-Test** が含まれています。
1. テストが完了したら、 **[マシンのレプリケート]** で Azure VM を選択したままにします (または右クリックします)。 次に、 **[テスト移行をクリーンアップ]** を選択します。

    ![[テスト移行をクリーンアップ] 項目を示すスクリーンショット。](./media/contoso-migration-rehost-linux-vm/clean-up.png)

### <a name="migrate-the-vms"></a>VM を移行する

Contoso の管理者は、ここで、移動を完了する完全移行を実行します。

1. Azure Migrate プロジェクトで、 **[サーバー]**  >  **[Azure Migrate:Server Migration]** の順に移動し、 **[サーバーをレプリケートしています]** を選択します。

    ![[サーバーをレプリケートしています] オプションを示すスクリーンショット。](./media/contoso-migration-rehost-linux-vm/replicating-servers.png)

1. **[マシンのレプリケート]** で VM を選択したまま (または右クリックし)、 **[移行]** を選択します。
1. **[移行]**  >  **[仮想マシンをシャットダウンし、データ損失のない計画された移行を実行しますか]** で、 **[はい]**  >  **[OK]** の順に選択します。
    - 既定では、Azure Migrate によってオンプレミス VM がシャットダウンされ、前回のレプリケーションが発生した後に行われた VM の変更を同期するためにオンデマンド レプリケーションが実行されます。 このアクションにより、データ損失が発生しなくなります。
    - VM をシャットダウンしたくない場合は、 **[いいえ]** を選択します。
1. VM に対して移行ジョブが開始されます。 Azure 通知でジョブを追跡します。
1. ジョブが完了したら、 **[仮想マシン]** ページで VM を表示して管理できます。

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

1. **systemctl restart apache2** でサービスを再起動します。

    ![サービスの再起動を示すスクリーンショット。](./media/contoso-migration-rehost-linux-vm/restart.png)

1. 最後に、Contoso ドメイン コントローラーのいずれかで、`OSTICKETWEB` と `OSTICKETMYSQL` の DNS レコードを更新します。

    ![DNS レコードの更新を示すスクリーンショット。](./media/contoso-migration-rehost-linux-vm-mysql/update-dns.png)

    ![DNS レコードの更新を示すスクリーンショット。](./media/contoso-migration-rehost-linux-vm-mysql/update-dns.png)

**さらにサポートが必要な場合**

- [テスト移行を実行する](/azure/migrate/tutorial-migrate-vmware#run-a-test-migration)方法をご覧ください。
- [VM を Azure に移行する](/azure/migrate/tutorial-migrate-vmware#migrate-vms)方法をご覧ください。

## <a name="clean-up-after-migration"></a>移行後にクリーンアップする

移行が完了すると、osTicket のアプリケーション層は Azure VM で実行されるようになります。

ここで、Contoso は次の作業を行う必要があります。

- vCenter のインベントリからオンプレミスの VM を削除します。
- ローカルのバックアップ ジョブからからオンプレミスの VM を削除します。
- `OSTICKETWEB` および `OSTICKETMYSQL` の新しい場所と IP アドレスを示すように内部ドキュメントを更新します。
- VM とやり取りするリソースを確認します。 すべての関連する設定またはドキュメントを更新して新しい構成を反映します。
- Contoso では、管理 VM で Azure Migrate サービスを使用し、移行のための VM を評価しました。 管理者は、VMware ESXi Server から移行 VM と Web VM を削除する必要があります。

## <a name="review-the-deployment"></a>デプロイを再調査する

アプリケーションが実行されるようになったので、Contoso は新しいインフラストラクチャを完全に操作可能にし、セキュリティで保護する必要があります。

### <a name="security"></a>Security

Contoso のセキュリティ チームは、OSTICKETWEB と OSTICKETMYSQL の VM を再調査して、セキュリティの問題を特定します。

- アクセスを制御するために、VM のネットワーク セキュリティ グループ (NSG) を見直します。 NSG は、アプリケーションに対して許可されるトラフィックのみが通過できるようにするために使用されます。
- チームは、Azure Disk Encryption と Azure Key Vault を使用して、VM ディスク上のデータをセキュリティで保護することも検討します。

詳細については、「[Azure における IaaS ワークロードのセキュリティに関するベスト プラクティス](/azure/security/fundamentals/iaas)」を参照してください。

### <a name="business-continuity-and-disaster-recovery"></a>事業継続とディザスター リカバリー

事業継続とディザスター リカバリーのために、Contoso は次のアクションを実施します。

- **データの安全性を確保する。** Contoso は、[Azure VM バックアップ](/azure/backup/backup-azure-vms-introduction)を使用して VM 上のデータをバックアップします。
- **アプリケーションの稼働を維持する。** Contoso は、Site Recovery を使用して、Azure 内のアプリケーション VM をセカンダリ リージョンにレプリケートします。 詳細については、「[クイック スタート: Azure VM のセカンダリ Azure リージョンへのディザスター リカバリーの設定](/azure/site-recovery/azure-to-azure-quickstart)」を参照してください。

### <a name="licensing-and-cost-optimization"></a>ライセンスとコストの最適化

- Contoso は、リソースのデプロイ後、[Azure インフラストラクチャのデプロイ](./contoso-migration-infrastructure.md#set-up-tagging)中に定義されたとおりに Azure タグを割り当てます。
- Contoso に、Ubuntu サーバーとのライセンスの問題はありません。
- Contoso は [Azure Cost Management および Billing](/azure/cost-management-billing/cost-management-billing-overview) を使用して、IT リーダーが定めた予算内に確実に収まるようにします。
