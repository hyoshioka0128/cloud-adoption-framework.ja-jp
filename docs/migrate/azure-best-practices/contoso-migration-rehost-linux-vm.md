---
title: オンプレミスの Linux アプリケーションを Azure VM にリホストする
description: Contoso が、Azure VM に移行することによってオンプレミス Linux アプリをどのようにリホストするかを説明します。
author: deltadan
ms.author: abuck
ms.date: 07/01/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: migrate
services: azure-migrate
ms.openlocfilehash: 583cc72e4066d2cb41162bd04f641356518b2386
ms.sourcegitcommit: 84d7bfd11329eb4c151c4c32be5bab6c91f376ed
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2020
ms.locfileid: "86234448"
---
<!-- cSpell:ignore givenscj OSTICKETWEB OSTICKETMYSQL OSTICKETWEB OSTICKETMYSQL contosohost vcenter contosodc osTicket binlog systemctl NSGs distros -->

# <a name="rehost-an-on-premises-linux-application-to-azure-vms"></a>オンプレミスの Linux アプリケーションを Azure VM にリホストする

この記事では、架空の会社 Contoso が、2 階層の [LAMP ベースの](https://wikipedia.org/wiki/LAMP_(software_bundle))アプリケーションを Azure IaaS VM を使用してどのようにリホストするかを説明します。

この例で使用される osTicket (サービス デスク アプリケーション) は、オープンソースとして提供されています。 独自のテスト目的に沿って使用する場合は、[GitHub](https://github.com/osTicket/osTicket) からダウンロードできます。

## <a name="business-drivers"></a>ビジネス ドライバー

IT リーダーシップ チームは、ビジネス パートナーと密接に連絡を取り合い、彼らがこの移行で何を達成しようとしているのかを理解しました。

- **ビジネスの成長への対応。** Contoso は成長を続けています。その結果、オンプレミスのシステムとインフラストラクチャに負荷がかかっています。
- **リスクの限定。** このサービス デスク アプリケーションは、Contoso のビジネスにとって非常に重要です。 Contoso は、リスクを負うことなく Azure に移行したいと考えています。
- **拡張。** Contoso は、アプリケーションに今すぐ変更を加えることは望んでいません。 ただアプリケーションの安定性を確保することが望みです。

## <a name="migration-goals"></a>移行の目標

Contoso クラウド チームは、この移行の目標を明確にして、最適な移行方法を決定しました。

- 移行後の Azure 内のアプリケーションは、現在のオンプレミス VMware 環境と同じパフォーマンスを発揮できる必要があります。 このアプリケーションは、オンプレミスにあるときと同様に、クラウドでも引き続き非常に重要です。
- Contoso は、このアプリケーションに投資することは望んでいません。 これはビジネスにとって重要なものですが、現状のまま確実にクラウドに移すことだけを Contoso は望んでいます。
- Contoso は、このアプリケーションの操作モデルを変更したくありません。 今と同じ方法のままクラウドでアプリケーションを操作したいのです。
- Contoso は、アプリケーションの機能を変更したくありません。 変えるのはアプリケーションの場所のみです。
- Windows アプリケーションの移行を何件か完了したら、Contoso は Azure で Linux ベースのインフラストラクチャを使用する方法を学びたいと考えています。

## <a name="solution-design"></a>ソリューション設計

Contoso は目標と要件を決定した後、デプロイ ソリューションを設計およびレビューし、移行に使用する Azure サービスを含め、移行プロセスを決めます。

### <a name="current-application"></a>現在のアプリケーション

- osTicket アプリケーションは 2 つの VM (`OSTICKETWEB` と `OSTICKETMYSQL`) に階層化されています。
- これらの VM は、VMware ESXi ホスト `contosohost1.contoso.com` (バージョン 6.5) 上に配置されています。
- VMware 環境は、VM で実行中のvCenter Server 6.5 (`vcenter.contoso.com`) によって管理されています。
- Contoso には、オンプレミスのデータセンター (`contoso-datacenter`) があり、これにはオンプレミスのドメイン コントローラー (`contosodc1`) が含まれています。

### <a name="proposed-architecture"></a>提案されたアーキテクチャ

- このアプリケーションは運用ワークロードであるため、Azure 内の VM は、運用リソース グループ `ContosoRG` 内に存在することになります。
- VM はプライマリ リージョン (米国東部 2) に移行され、運用ネットワーク (`VNET-PROD-EUS2`) 内に配置されます。
  - Web VM は、フロントエンド サブネット (`PROD-FE-EUS2`) 内に存在することになります。
  - データベース VM は、データベース サブネット (`PROD-DB-EUS2`) 内に存在することになります。
- Contoso データセンター内のオンプレミス VM は、移行が行われた後に使用停止にされます。

![シナリオのアーキテクチャ](./media/contoso-migration-rehost-linux-vm/architecture.png)

### <a name="solution-review"></a>ソリューションのレビュー

Contoso は、長所と短所の一覧をまとめて、提案されたデザインを評価します。

| 考慮事項 | 詳細 |
| --- | --- |
| **長所** | アプリケーションの VM はどちらも変更を加えることなく Azure に移されるため、移行が簡単で済みます。 <br><br> Contoso は両方のアプリケーションの VM にリフトアンドシフト アプローチを使用するため、アプリケーション データベース用に特別な構成や移行ツールは不要です。 <br><br> Contoso は、Azure で引き続きアプリケーションの VM を完全に制御できます。 <br><br> アプリケーションの VM では、動作保証済みの Linux ディストリビューション Ubuntu 16.04-TLS が実行されています。 [詳細については、こちらを参照してください](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)。 |
| **短所** | アプリケーションの Web 層とデータ層は、依然として単一フェールオーバー ポイントになります。 <br><br> Contoso は今後も、Azure App Service や Azure Database for MySQL といったマネージド サービスに移行するのではなく、Azure VM としてアプリケーションをサポートしていく必要があります。 <br><br> リフトアンドシフトの VM 移行によって単純化すると、[Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/overview) の特長 (ビルトインの高可用性、予測可能なパフォーマンス、単純なスケーリング、自動バックアップ、ビルトインのセキュリティ) がフル活用できないことに Contoso は気づいています。 |

### <a name="migration-process"></a>移行プロセス

Contoso は次のように移行されます。

- 最初の手順として、Azure Migrate:Server Migration 用の Azure コンポーネントを準備および設定し、オンプレミスの VMware インフラストラクチャを準備します。
- [Azure インフラストラクチャ](./contoso-migration-infrastructure.md)は既に用意されているので、Contoso では、Azure Migrate: Server Migration ツールを使用して VM のレプリケーションの構成を行うだけで済みますサーバー移行ツール。
- すべての準備ができたら、VM のレプリケートを開始できます。
- レプリケーションを有効にしたら、Azure へのフェールオーバーによって VM を移行します。

![移行プロセス](./media/contoso-migration-rehost-linux-vm/migration-process-az-migrate.png)

### <a name="azure-services"></a>Azure サービス

| サービス | 説明 | コスト |
| --- | --- | --- |
| [Azure Migrate: Server Migration](https://docs.microsoft.com/azure/migrate/contoso-migration-rehost-linux-vm) | このサービスは、オンプレミスのアプリケーションとワークロード、AWS または GCP の VM インスタンスの移行を統制し、管理します。 | Azure へのレプリケーションの間に、Azure Storage の料金が発生します。 移行が行われると、Azure VM が作成されて料金がかかります。 料金と価格について[詳しくはこちら](https://azure.microsoft.com/pricing/details/azure-migrate)をご覧ください。 |

## <a name="prerequisites"></a>前提条件

このシナリオで Contoso に必要なことを以下に示します。

必要条件 | 詳細 |
| --- | --- |
| **Azure サブスクリプション** | このシリーズの先行する記事の中で、Contoso はサブスクリプションを作成しました。 Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/free)を作成してください。 <br><br> 無料アカウントを作成する場合、サブスクリプションの管理者としてすべてのアクションを実行できます。 <br><br> 既存のサブスクリプションを使用しており、管理者でない場合は、管理者に依頼して所有者アクセス許可または共同作成者アクセス許可を割り当ててもらう必要があります。 <br><br> さらに詳細なアクセス許可が必要な場合は、[こちらの記事](https://docs.microsoft.com/azure/site-recovery/site-recovery-role-based-linked-access-control)をご覧ください。 |
| **Azure インフラストラクチャ** | [Contoso で Azure インフラストラクチャを設定する方法](./contoso-migration-infrastructure.md)を確認してください。 <br><br> 以下についての具体的な[前提条件](./contoso-migration-devtest-to-iaas.md#prerequisites)をご確認ください。Azure Migrate:Server Migration に関するエラーのトラブルシューティングに役立つ情報を提供しています。 |
| **オンプレミスのサーバー** | オンプレミス vCenter Server は、バージョン 5.5、6.0、または 6.5 を実行している必要があります。 <br><br> バージョン 5.5、6.0、または 6.5 を実行している ESXi ホスト。 <br><br> ESXi ホスト上で実行している 1 つ以上の VMware VM。 |
| **オンプレミスの VM** | Azure での実行が保証されている [Linux ディストリビューションを確認します](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)。 |

## <a name="scenario-steps"></a>シナリオのステップ

Contoso は、次のようにして移行を完了します。

> [!div class="checklist"]
>
> - **ステップ 1:Azure Migrate: Server Migration 用の Azure を準備するServer Migration.** Azure Migrate:Server Migration ツールを Azure Migrate プロジェクトに追加します。
> - **手順 2:Azure Migrate: Server Migration 用のオンプレミス VMware を準備する。** VM 検出用のアカウントを準備し、移行後に Azure VM に接続するための準備をします。
> - **ステップ 3:VM をレプリケートする。** レプリケーションを設定し、Azure Storage への VM のレプリケーションを開始します。
> - **手順 4:Azure Migrate: Server Migration で VM を移行する。** テスト移行を実行してすべてが機能していることを確認した後、移行を実行して VM を Azure に移動します。

## <a name="step-1-prepare-azure-for-the-azure-migrate-server-migration-tool"></a>手順 1:Azure Migrate: Server Migration ツール用の Azure を準備するServer Migration ツール

Contoso が VM を Azure に移行するには、以下の Azure コンポーネントが必要です。

- 移行中に作成される Azure VM が配置される VNet。
- Azure Migrate: Server Migration ツール (プロビジョニング済みのもの)。

これらのコンポーネントを次のように設定します。

1. **ネットワークを設定する:** Contoso は、[Azure インフラストラクチャのデプロイ](./contoso-migration-infrastructure.md)を行ったときに、Azure Migrate: Server Migration に使用できるネットワークを既にセットアップしています

    - SmartHotel360 アプリケーションは運用アプリケーションであり、VM はプライマリ リージョン (`East US 2`) の Azure 運用ネットワーク (`VNET-PROD-EUS2`) に移行されます。
    - 両方の VM は、運用リソースのために使用される `ContosoRG` リソース グループに配置されます。
    - アプリケーションのフロントエンド VM (`OSTICKETWEB`) は、運用ネットワーク内のフロントエンド サブネット (`PROD-FE-EUS2`) に移行されます。
    - アプリケーション データベース VM (`OSTICKETMYSQL`) は、運用ネットワーク内のデータベース サブネット (`PROD-DB-EUS2`) に移行されます。

2. **Azure Migrate:Server Migration ツールをプロビジョニングする:** ネットワークとストレージ アカウントが用意できたので、Recovery Services コンテナー (`ContosoMigrationVault`) を作成し、それをプライマリ リージョン (`East US 2`) の `ContosoFailoverRG` リソース グループ内に配置します。

    ![Azure Migrate Server Migration ツール](./media/contoso-migration-rehost-linux-vm/server-migration-tool.png)

**さらにサポートが必要な場合**

Azure Migrate: Server Migration ツールの設定について[ご確認ください](https://docs.microsoft.com/azure/migrate)サーバー移行ツール。

## <a name="step-2-prepare-on-premises-vmware-for-azure-migrate-server-migration"></a>手順 2:Azure Migrate: Server Migration 用のオンプレミス VMware を準備するServer Migration

Contoso は、Azure への移行後に、Azure 内のレプリケートされた VM に接続できることを望んでいます。 これを行うには、Contoso の管理者がいくつかのことを実行する必要があります。

- Azure VM にインターネット経由でアクセスするには、移行前に、オンプレミス Linux VM 上で SSH を有効にします。 Ubuntu の場合、これは `sudo apt-get ssh install -y` コマンドを使用して完了させることができます。
- [Azure Linux エージェント](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux)をインストールします。
- 移行を実行したら、**ブート診断**を調べて、VM のスクリーンショットを表示できます。
- これが機能しない場合は、その VM が実行中であることを確認し、これらの[トラブルシューティングのヒント](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)を見直してください。

**さらにサポートが必要な場合**

- [移行用の VM の準備](https://docs.microsoft.com/azure/migrate/prepare-for-migration)についてご確認ください。

## <a name="step-3-replicate-the-on-premises-vms"></a>手順 3:オンプレミスの VM をレプリケートする

Contoso の管理者は、Azure への移行を実行する前に、レプリケーションを設定して有効にする必要があります。

検出が完了したら、Azure への VMware VM のレプリケーションを開始できます。

1. Azure Migrate プロジェクトの **[サーバー]** の **[Azure Migrate: Server Migration]** で、 **[レプリケート]** を選択します。

    ![VM をレプリケートする](./media/contoso-migration-rehost-linux-vm/select-replicate.png)

2. **[レプリケート]**  >  **[ソースの設定]**  >  **[マシンは仮想化されていますか?]** で、 **[はい (VMware vSphere の場合)]** を選択します。

3. **[オンプレミスのアプライアンス]** で、自分が設定した Azure Migrate アプライアンスの名前、 **[OK]** の順に選択します。

    ![ソースの設定](./media/contoso-migration-rehost-linux-vm/source-settings.png)

4. **[仮想マシン]** で、レプリケートしたいマシンを選択します。
    - VM の評価を実行した場合は、評価結果から VM のサイズ設定とディスクの種類 (Premium または Standard) の推奨事項を適用できます。 これを行うには、 **[Azure Migrate Assessment から移行設定をインポートしますか?]** で **[はい]** オプションを選択します。
    - 評価を実行しなかった場合、または評価の設定を使用しない場合は、 **[いいえ]** オプションを選択します。
    - 評価の使用を選択した場合は、VM グループと評価名を選択します。

    ![評価の選択](./media/contoso-migration-rehost-linux-vm/select-assessment.png)

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

## <a name="step-4-migrate-the-vms"></a>手順 4:VM を移行する

Contoso の管理者は、クイックテスト移行を実行し、その後、移行を実行して VM を移動します。

### <a name="run-a-test-migration"></a>テスト移行を実行する

1. **[移行の目標]**  >  **[サーバー]**  >  **[Azure Migrate: Server Migration]** で、 **[移行されたサーバーをテストします]** を選択します。

     ![移行されたサーバーのテスト](./media/contoso-migration-rehost-linux-vm/test-migrated-servers.png)

2. テストする VM を選択したまま (または右クリックし)、 **[テスト移行]** を選択します。

    ![テスト移行](./media/contoso-migration-rehost-linux-vm/test-migrate.png)

3. **[テスト移行]** で、移行後に Azure VM が配置される Azure VNet を選択します。 非運用環境の VNet を使用することをお勧めします。
4. **テスト移行**ジョブが開始されます。 ポータルの通知でジョブを監視します。
5. 移行の完了後、Azure portal の **[仮想マシン]** で、移行された Azure VM を確認します。 マシン名には、サフィックス **-Test** が含まれています。
6. テストが完了したら、 **[マシンのレプリケート]** で Azure VM を選択したまま (または右クリックし)、 **[テスト移行をクリーンアップ]** を選択します。

    ![移行のクリーンアップ](./media/contoso-migration-rehost-linux-vm/clean-up.png)

### <a name="migrate-the-vms"></a>VM を移行する

Contoso の管理者は、ここで、移動を完了する完全移行を実行します。

1. Azure Migrate プロジェクトの **[サーバー]**  >  **[Azure Migrate: Server Migration]** で、 **[サーバーをレプリケートしています]** を選択します。

    ![サーバーをレプリケートしています](./media/contoso-migration-rehost-linux-vm/replicating-servers.png)

2. **[マシンのレプリケート]** で VM を選択したまま (または右クリックし)、 **[移行]** を選択します。
3. **[移行]**  >  **[仮想マシンをシャットダウンし、データ損失のない計画された移行を実行しますか]** で、 **[はい]**  >  **[OK]** の順に選択します。
    - 既定では、Azure Migrate によってオンプレミスの VM がシャットダウンされ、前回のレプリケーションが発生した後に発生したすべての VM の変更を同期するためにオンデマンド レプリケーションが実行されます。 こうすることで、データ損失がなくなります。
    - VM をシャットダウンしたくない場合は、 **[いいえ]** を選択します。
4. VM に対して移行ジョブが開始されます。 Azure 通知でジョブを追跡します。
5. ジョブが完了したら、 **[仮想マシン]** ページで VM を表示して管理できます。

### <a name="connect-the-vm-to-the-database"></a>VM をデータベースに接続する

移行プロセスの最終ステップとして、Contoso の管理者は、`OSTICKETMYSQL` VM で実行されているアプリケーション データベースを指すようにアプリケーションの接続文字列を更新します。

1. PuTTY または別の SSH クライアントを使用して、`OSTICKETWEB` VM への SSH 接続を確立します。 この VM はプライベートなので、プライベート IP アドレスを使用して接続します。

    ![データベースへの接続](./media/contoso-migration-rehost-linux-vm/db-connect.png)

    ![データベースへの接続](./media/contoso-migration-rehost-linux-vm/db-connect2.png)

2. `OSTICKETWEB` VM が `OSTICKETMYSQL` VM と通信できることを確認する必要があります。 構成は、この時点で、オンプレミスの IP アドレス `172.16.0.43` を使用してハードコードされています。

    **更新の前:**

    ![IP の更新](./media/contoso-migration-rehost-linux-vm/update-ip1.png)

    **更新の後:**

    ![IP の更新](./media/contoso-migration-rehost-linux-vm/update-ip2.png)

3. **systemctl restart apache2** と指定してサービスを再起動します。

    ![Restart](./media/contoso-migration-rehost-linux-vm/restart.png)

4. 最後に、Contoso ドメイン コントローラーのいずれかで、`OSTICKETWEB` と `OSTICKETMYSQL` の DNS レコードを更新します。

    ![DNS の更新](./media/contoso-migration-rehost-linux-vm-mysql/update-dns.png)

    ![DNS の更新](./media/contoso-migration-rehost-linux-vm-mysql/update-dns.png)

**さらにサポートが必要な場合**

- テスト移行の実行に関する[説明を参照します](https://docs.microsoft.com/azure/migrate/tutorial-migrate-vmware#run-a-test-migration)。
- VM の Azure への移行に関する[説明を参照します](https://docs.microsoft.com/azure/migrate/tutorial-migrate-vmware#migrate-vms)。

## <a name="clean-up-after-migration"></a>移行後にクリーンアップする

移行が完了すると、osTicket のアプリケーション層は Azure VM で実行されるようになります。

ここで、以下のようにクリーンアップを実行する必要があります。

- vCenter のインベントリからオンプレミスの VM を削除します。
- ローカルのバックアップ ジョブからからオンプレミスの VM を削除します。
- 内部ドキュメントを更新して、`OSTICKETWEB` および `OSTICKETMYSQL` の新しい場所と IP アドレスを記します。
- VM と対話しているリソースを確認し、すべての関連する設定またはドキュメントを新しい構成を反映するように更新します。
- Contoso では、管理 VM で Azure Migrate サービスを使用し、移行のための VM を評価しました。 管理者は、VMware ESXi Server から移行 VM と Web VM を削除する必要があります。

## <a name="review-the-deployment"></a>デプロイを再調査する

アプリケーションが実行されるようになり、Contoso は新しいインフラストラクチャを完全に操作可能にして、セキュリティで保護する必要があります。

### <a name="security"></a>セキュリティ

Contoso のセキュリティ チームは、OSTICKETWEB と OSTICKETMYSQL の VM を再調査して、セキュリティの問題を特定します。

- アクセスを制御するために、VM のネットワーク セキュリティ グループ (NSG) を見直します。 NSG は、アプリケーションに対して許可されるトラフィックのみが通過できるようにするために使用されます。
- チームでは、Azure Disk Encryption と Azure Key Vault を使用して VM ディスク上のデータをセキュリティで保護することも検討します。

詳細については、「[Azure における IaaS ワークロードのセキュリティに関するベスト プラクティス](https://docs.microsoft.com/azure/security/fundamentals/iaas)」を参照してください。

<!-- docsTest:ignore "Quickstart: Set" -->

### <a name="bcdr"></a>BCDR

事業継続とディザスター リカバリーのために、Contoso は次のアクションを実施します。

- **データの安全性を確保する。** Contoso は、[Azure VM バックアップ](https://docs.microsoft.com/azure/backup/backup-azure-vms-introduction)を使用して VM 上のデータをバックアップします。
- **アプリケーションの稼働を維持する。** Contoso は、Site Recovery を使用して、Azure 内のアプリケーション VM をセカンダリ リージョンにレプリケートします。 詳細については、「[クイック スタート: Azure VM のセカンダリ Azure リージョンへのディザスター リカバリーの設定](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-quickstart)」を参照してください。

### <a name="licensing-and-cost-optimization"></a>ライセンスとコストの最適化

- Contoso は、リソースのデプロイ後、[Azure インフラストラクチャのデプロイ](./contoso-migration-infrastructure.md#set-up-tagging)中に定義されたとおりに Azure タグを割り当てます。
- Contoso に、Ubuntu サーバーとのライセンスの問題はありません。
- Contoso は [Azure Cost Management と Billing](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview) を使用して、IT リーダーが定めた予算内に確実に収まるようにします。
