---
title: Azure Migrate を使用してオンプレミスの Dev/Test 環境を Azure Virtual Machines にリホストする
description: Contoso がリフトアンドシフト アプローチと Azure Migrate サービスを使用して、オンプレミス マシンを Azure に移行することによって、オンプレミスの Dev/Test 環境をリホストする方法について説明します。
author: deltadan
ms.author: abuck
ms.date: 07/1/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: migrate
services: azure-migrate
ms.openlocfilehash: 9343c415110812557f70dc286f6633f23c283f93
ms.sourcegitcommit: 71a4f33546443d8c875265ac8fbaf3ab24ae8ab4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2020
ms.locfileid: "86478584"
---
<!-- docsTest:ignore SmartHotel360 -->
<!-- cSpell:ignore vcenter contosohost contosodc NSGs agentless osTicket WEBVMDEV SQLVMDEV OSTICKETWEBDEV OSTICKETMYSQLDEV -->

# <a name="rehost-an-on-premises-devtest-environment-on-azure-virtual-machines-via-azure-migrate"></a>Azure Migrate を使用してオンプレミスの Dev/Test 環境を Azure Virtual Machines にリホストする

この記事では、Contoso という架空の会社が、Azure Virtual Machines に移行することによって、VMware 仮想マシン (VM) で実行されている 2 つのアプリケーションの Dev/Test 環境をリホストする方法について説明します。

この例で使用されている [SmartHotel360](https://github.com/Microsoft/SmartHotel360) および [osTicket](https://github.com/osTicket/osTicket) アプリケーションはオープンソースです。 独自のテストの目的でこれらをダウンロードできます。

## <a name="migration-options"></a>移行オプション

Contoso には、Dev/Test 環境を Azure に移行するときに使用できるオプションがいくつかあります。

| 移行オプション | 結果 |
| --- | --- |
| [Azure Migrate](https://docs.microsoft.com/azure/migrate/migrate-services-overview) | オンプレミスの VM を[評価](https://docs.microsoft.com/azure/migrate/tutorial-assess-vmware)し、[移行](https://docs.microsoft.com/azure/migrate/tutorial-migrate-vmware)する。 <br><br> Azure のサービスとしてのインフラストラクチャ (IaaS) を使用して、開発およびテスト サーバーを実行する。 <br><br> [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager) を使用して VM を管理する。 |
| [Azure DevTest Labs](https://docs.microsoft.com/azure/devtest-labs/devtest-lab-overview) | 開発およびテスト環境を迅速にプロビジョニングします。 <br><br> クォータとポリシーを使用して無駄を最小限に抑える。 <br><br> 自動シャットダウンを設定してコストを最小限に抑える。 <br><br> Windows および Linux 環境を構築する。 |

> [!NOTE]
> Contoso が [DevTest Labs を使用して開発およびテスト環境を Azure に移行した方法](./contoso-migration-devtest-to-labs.md)をご覧ください。

## <a name="business-drivers"></a>ビジネス ドライバー

開発リーダーシップ チームは、この移行で達成したいことを簡単にまとめました。 チームは、Dev/Test 機能をオンプレミスのデータセンターから迅速に移行し、ソフトウェアを開発するためのハードウェアを今後は購入しないことを目指しています。 また、IT 部門が関与しなくても、開発者が各自の環境を作成して実行できるようにしたいと考えています。

> [!NOTE]
> Contoso は、[開発テスト用の従量課金制サブスクリプション プラン](https://azure.microsoft.com/offers/ms-azr-0023p)を環境で使用します。 チームのアクティブな各 Visual Studio サブスクライバーは、サブスクリプションの仮想マシンに含まれている Microsoft ソフトウェアを、追加料金なしで Dev/Test に使用できます。 Contoso は、実行する VM の Linux 料金だけを支払います。 それには、通常であればさらに高い料金がかかる SQL Server、SharePoint Server、その他のソフトウェアの VM が含まれます。

## <a name="migration-goals"></a>移行の目標

Contoso 開発チームは、この移行の目標を明確にしました。 これらの目標を使用して、最良の移行方法を決定します。

- Contoso は、オンプレミスの Dev/Test 環境から迅速に移行したいと考えています。
- 移行後、Azure の Dev/Test 環境は、VMware の現在のシステムよりも機能が強化されている必要があります。
- IT 部門によるプロビジョニングから、セルフサービス プロビジョニングを使用する DevOps に運用モデルを移行します。

## <a name="solution-design"></a>ソリューション設計

Contoso は、目標と要件を明確にした後、デプロイ ソリューションを設計してレビューし、移行プロセスを確認します。 このプロセスには、Contoso が移行に使用する Azure サービスが含まれています。

### <a name="current-application"></a>現在のアプリケーション

- 2 つのアプリケーションの Dev/Test VM は、VM (`WEBVMDEV`、`SQLVMDEV`、`OSTICKETWEBDEV`、`OSTICKETMYSQLDEV`) 上で実行されています。 これらの VM は、コードが運用 VM に昇格される前の開発に使用されます。
- これらの VM は、VMware ESXi ホスト `contosohost1.contoso.com` (バージョン 6.5) 上に配置されています。
- VMware 環境は、VM で実行中のvCenter Server 6.5 (`vcenter.contoso.com`) によって管理されています。
- Contoso には、オンプレミスのデータセンター (`contoso-datacenter`) があり、これにはオンプレミスのドメイン コントローラー (`contosodc1`) が含まれています。

### <a name="proposed-architecture"></a>提案されたアーキテクチャ

- VM は Dev/Test に使用されるので、Azure の `ContosoDevRG` リソース グループに配置されます。
- VM はプライマリ Azure リージョン (`East US 2`) に移行され、開発用仮想ネットワーク (`VNET-DEV-EUS2`) に配置されます。
- Web フロントエンド VM は、開発用ネットワークのフロントエンド サブネット (`DEV-FE-EUS2`) に配置されます。
- データベース VM は、開発用ネットワークのデータベース サブネット (`DEV-DB-EUS2`) に配置されます。
- Contoso データセンター内のオンプレミス VM は、移行が行われた後に使用停止にされます。

  ![オンプレミスと仮想マシンに関して提案されたシナリオ アーキテクチャの図。](./media/contoso-migration-devtest-to-iaas/architecture.png)
  
  "_図 1:提案されたアーキテクチャ_

### <a name="database-considerations"></a>データベースの考慮事項

進行中の開発をサポートするために、Contoso は既存の VM を引き続き使用し、それらを Azure に移行することにしました。 今後、Contoso は [Azure SQL Database](https://docs.microsoft.com/azure/azure-sql/azure-sql-iaas-vs-paas-what-is-overview) や [Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/overview) などのサービスとしてのプラットフォーム (PaaS) サービスの使用を推進します。

- データベース VM は変更なしでそのまま移行されます。
- Azure Dev/Test サブスクリプション プランを使用すると、Windows Server と SQL Server が実行されているコンピューターにはライセンス料金がかからなくなります。 料金がかからないと、コンピューティング コストを最小限に抑えることができます。
- 将来的に、Contoso は開発を PaaS サービスと統合することを検討します。

### <a name="solution-review"></a>ソリューションのレビュー

Contoso は、長所と短所の一覧をまとめて、提案されたデザインを評価します。

| 考慮事項 | 詳細 |
| --- | --- |
| **長所** | すべての開発 VM は変更なしで Azure に移行されるので、移行が簡単になります。 <br><br> Contoso は VM の両方のセットにリフトアンドシフト アプローチを使用するため、アプリケーション データベース用に特別な構成や移行ツールは不要です。 <br><br> Contoso は、Azure Dev/Test サブスクリプションへの投資を利用してライセンス料金を節約できます。 <br><br> Contoso は、Azure でアプリケーション VM の完全な制御を維持します。 <br><br> IT 部門が要求に対応するのを待たずに新しいリソースを作成できるように、開発者にはサブスクリプションに対する権限が与えられます。 |
| **短所** | 移行では VM だけが移行され、開発用の PaaS サービスにはまだ移行されません。 つまり、Contoso は、セキュリティ更新プログラムなど、VM の操作のサポートを開始する必要があります。 これは、これまで IT 部門によって管理されていたので、Contoso ではこの新しい運用タスクのソリューションを見つける必要があります。 <br><br> クラウドベースのソリューションは開発者を支援しますが、システムのオーバープロビジョニングに対する保護手段を備えていません。 開発者はシステムを即座にプロビジョニングできますが、予算に含まれていない、コストがかかるリソースを作成する可能性があります。 |

> [!NOTE]
> Contoso は、[DevTest Labs](https://docs.microsoft.com/azure/devtest-labs/devtest-lab-overview) を使用することで、この一覧の短所に対処できます。

### <a name="migration-process"></a>移行プロセス

Contoso では、Azure Migrate のエージェントレスの方法を使用して、開発フロントエンドとデータベースを Azure VM に移行します。サーバー移行ツール。

- Contoso は Azure Migrate:Server Migration 用の Azure コンポーネントを準備および設定し、オンプレミスの VMware インフラストラクチャを準備します。
- [Azure インフラストラクチャ](./contoso-migration-infrastructure.md)があるので、Contoso では、Azure Migrate: Server Migration ツールを使用して VM のレプリケーションの構成を行うだけで済みますサーバー移行ツール。
- すべての準備ができたら、VM のレプリケートを開始できます。
- レプリケーションを有効にした後、移行をテストし、成功したら、Azure にフェールオーバーして VM を移行します。
- Azure で開発 VM が稼働状態になったら、Contoso は Azure で実行されるようになった VM を参照するように開発ワークステーションを再構成します。

![移行プロセスの図。](./media/contoso-migration-devtest-to-iaas/migration-process-az-migrate.png)

"_図 2:移行プロセスの概要_

### <a name="azure-services"></a>Azure サービス

| サービス | 説明 | コスト |
| --- | --- | --- |
| [Azure Migrate: Server Migration](https://docs.microsoft.com/azure/migrate) | このサービスは、オンプレミスのアプリケーションとワークロード、および AWS または GCP VM インスタンスの移行を調整、管理します。 | Azure へのレプリケーションの間に、Azure Storage の料金が発生します。 移行が行われ、VM が Azure で実行されると、Azure VM が作成され、料金が発生します。 料金と価格について[詳しくはこちら](https://azure.microsoft.com/pricing/details/azure-migrate)をご覧ください。 |

## <a name="prerequisites"></a>前提条件

このシナリオを実行するために Contoso に必要なものを以下に示します。

| 必要条件 | 詳細 |
| --- | --- |
| **Azure Dev/Test サブスクリプション** | Contoso は、[Azure Dev/Test サブスクリプション](https://azure.microsoft.com/offers/ms-azr-0023p)を作成して、最大 80% のコスト削減を活用します。 <br><br> Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/free)を作成してください。 <br><br> 無料アカウントを作成した場合、サブスクリプションの管理者としてすべてのアクションを実行できます。 <br><br> 既存のサブスクリプションを使用し、管理者ではない場合は、管理者と協力して所有者または共同作成者のアクセス許可を割り当てます。 <br><br> より詳細なアクセス許可が必要な場合は、「[ロールベースのアクセス制御 (RBAC) を使用して Site Recovery のアクセスを管理する](https://docs.microsoft.com/azure/site-recovery/site-recovery-role-based-linked-access-control)」をご覧ください。 |
| **Azure インフラストラクチャ** | Contoso が [Azure インフラストラクチャを設定する方法](./contoso-migration-infrastructure.md)を確認します。 <br><br> 以下についての具体的な[前提条件](#prerequisites)をご確認ください。Azure Migrate:Server Migration に関するエラーのトラブルシューティングに役立つ情報を提供しています。 |
| **オンプレミスのサーバー** | オンプレミスの vCenter Server は、バージョン 5.5、6.0、6.5、または 6.7 を実行している必要があります。 <br><br> ESXi ホストは、バージョン 5.5、6.0、6.5、または 6.7 を実行している必要があります。 <br><br> ESXi ホスト上で 1 つ以上の VMware VM が実行されている必要があります。 |

## <a name="scenario-steps"></a>シナリオのステップ

Contoso の管理者が移行を実行する方法を次に示します。

> [!div class="checklist"]
>
> - **ステップ 1:Azure Migrate: Server Migration 用の Azure を準備するServer Migration.** Azure Migrate プロジェクトにサーバー移行ツールを追加します。
> - **手順 2:Azure Migrate: Server Migration 用のオンプレミス VMware を準備するServer Migration.** VM 検出用のアカウントを準備し、移行後に Azure VM に接続するための準備をします。
> - **ステップ 3:VM をレプリケートする。** レプリケーションを設定し、Azure Storage への VM のレプリケーションを開始します。
> - **手順 4:Azure Migrate: Server Migration で VM を移行するServer Migration.** テスト移行を実行してすべてが機能していることを確認した後、完全な移行を実行して VM を Azure に移行します。

## <a name="step-1-prepare-azure-for-the-azure-migrate-server-migration-tool"></a>手順 1:Azure Migrate: Server Migration ツール用の Azure を準備するServer Migration ツール

Contoso では、Azure Migrate:Server Migration ツールによって Azure VM が作成、プロビジョニング、構成される仮想ネットワークに、VM を移行する必要があります。サーバー移行ツール。

1. ネットワークをセットアップする: Contoso は、[Azure インフラストラクチャのデプロイ](./contoso-migration-infrastructure.md)を行ったときに、Azure Migrate: Server Migration に使用できるネットワークを既にセットアップしています。

    - 移行する VM は開発に使用されます。 これらは、米国東部 2 プライマリ リージョンの Azure 開発用仮想ネットワーク (`VNET-DEV-EUS2`) に移行されます。
    - どちらの VM も、開発リソースに使用される `ContosoDevRG` リソース グループに配置されます。
    - アプリケーション フロントエンド VM (`WEBVMDEV` と `OSTICKETWEBDEV`) は、開発用仮想ネットワークのフロントエンド サブネット (`DEV-FE-EUS2`) に移行されます。
    - アプリケーション データベース VM (`SQLVMDEV` と `OSTICKETMYSQLDEV`) は、開発用仮想ネットワークのデータベース サブネット (`DEV-DB-EUS2`) に移行されます。

2. Azure Migrate: Server Migration ツールをプロビジョニングしますサーバー移行ツール。

    1. Azure Migrate から、.OVA イメージをダウンロードし、VMware にインポートします。

       ![.OVA ファイルをダウンロードする画面のスクリーンショット。](./media/contoso-migration-devtest-to-iaas/migration-download-ova.png)
      
       "_図 3:.OVA file ファイルのダウンロード_

    1. インポートしたイメージを起動し、次のステップを含めてツールを構成します。

       - 前提条件を設定します。

         ![前提条件を設定するためのセクションのスクリーンショット。](./media/contoso-migration-devtest-to-iaas/migration-setup-prerequisites.png)
         
         "_図 4:前提条件の設定_

       - ツールを Azure サブスクリプションにポイントします。

         ![Azure Migrate の検出を設定するためのセクションのスクリーンショット。](./media/contoso-migration-devtest-to-iaas/migration-register-azure.png)
         
         _図 5:Azure サブスクリプション_

       - VMware vCenter の資格情報を設定します。

         ![VMware vCenter 資格情報を設定するためのセクションのスクリーンショット。](./media/contoso-migration-devtest-to-iaas/migration-vcenter-server.png)
        
         _図 6:VMware vCenter の資格情報の設定_

       - 検出用の Windows ベースの資格情報を追加します。

         ![VM アプリケーションと依存関係を検出するためのセクションのスクリーンショット。](./media/contoso-migration-devtest-to-iaas/migration-credentials.png)
         
         _図 7:検出用の Windows ベースの資格情報の追加_

3. 構成が完了すると、ツールによりすべての VM が列挙されます。 このプロセスが完了すると、Azure の Azure Migrate ツールにそれらが設定されていることがわかります。

**さらにサポートが必要な場合**

[Azure Migrate: Server Migration ツール](https://docs.microsoft.com/azure/migrate)の設定についてご確認ください。

### <a name="prepare-on-premises-vms"></a>オンプレミスの VM を準備する

Contoso は移行後、Azure VM に接続し、Azure で VM を管理できるようにしたいと考えています。 そのため、Contoso の管理者は、移行前に以下の操作を行います。

1. インターネットでアクセスする場合:

    - 移行前に、オンプレミス VM で RDP または SSH を有効にします。
    - `Public` プロファイルに TCP および UDP 規則が追加されていることを確認します。
    - オペレーティング システムのファイアウォールで RDP または SSH が許可されていることを確認します。
    - `sudo apt-get ssh install -y` コマンドを使用して SSH をインストールします。

2. サイト間 VPN でアクセスする場合:

    - 移行前に、オンプレミス VM で RDP または SSH を有効にします。
    - オペレーティング システムのファイアウォールで RDP または SSH が許可されていることを確認します。
    - Windows の場合、オンプレミス VM 上のオペレーティング システムの SAN ポリシーを `OnlineAll` に設定します。

3. [Azure Windows エージェント](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows)と [Azure Linux エージェント](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux)をインストールします。

Windows の場合、移行をトリガーするときに、VM 上に保留中の Windows 更新プログラムが存在しないようにする必要があります。 ある場合、管理者は更新が完了するまで、VM にログインすることはできません。 移行後、管理者は**ブート診断**を調べて、VM のスクリーンショットを確認できます。 これが機能しない場合は、VM が実行中であることを確認し、こちらの[トラブルシューティングのヒント](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)を参照してください。

**さらにサポートが必要な場合**

[移行のために VM を準備する](https://docs.microsoft.com/azure/migrate/prepare-for-migration)方法を参照してください。

## <a name="step-3-replicate-the-on-premises-vms"></a>手順 3:オンプレミスの VM をレプリケートする

Contoso の管理者は、Azure への移行を実行する前に、レプリケーションを設定して有効にする必要があります。 検出が完了したら、Azure への VMware VM のレプリケーションを開始できます。

1. Azure Migrate プロジェクトで、 **[サーバー]**  >  **[Azure Migrate:Server Migration]** に移動します。 次に、 **[レプリケート]** を選択します。

    ![移行ツールの [レプリケート] ボタンを示すスクリーンショット。](./media/contoso-migration-devtest-to-iaas/select-replicate.png)
   
    _図 8:VM のレプリケーション_

2. **[レプリケート]**  >  **[ソースの設定]**  >  **[マシンは仮想化されていますか?]** で、 **[はい (VMware vSphere の場合)]** を選択します。

3. **[オンプレミスのアプライアンス]** で、設定した Azure Migrate アプライアンスの名前を選択し、 **[OK]** を選択します。

    ![ソースの設定とアプライアンス名のボックスを示すスクリーンショット。](./media/contoso-migration-devtest-to-iaas/source-settings.png)
    
    _図 9:ソースの設定_

4. **[仮想マシン]** で、レプリケートするマシンを選択します。
    - VM の評価を実行した場合は、評価結果から VM のサイズ設定とディスクの種類 (Premium または Standard) の推奨事項を適用できます。 これを行うには、 **[Azure Migrate Assessment から移行設定をインポートしますか?]** で **[はい]** オプションを選択します。
    - 評価を実行しなかった場合、または評価の設定を使用しない場合は、 **[いいえ]** オプションを選択します。
    - 評価の使用を選択した場合は、VM グループと評価名を選択します。

      ![仮想マシンの評価の選択を示すスクリーンショット。](./media/contoso-migration-devtest-to-iaas/select-assessment.png)
      
      _図 10:前提条件の設定方法_

5. **[仮想マシン]** で、必要に応じて VM を検索し、移行する各 VM を確認します。 次に、 **[次のステップ: ターゲット設定]** をクリックします。

6. **[ターゲット設定]** で、移行先のサブスクリプションとターゲット リージョンを選択します。 次に、移行後に Azure VM が属するリソース グループを指定します。 **[仮想ネットワーク]** で、移行後に Azure VM の参加先となる Azure 仮想ネットワークまたはサブネットを選択します。

7. **[Azure ハイブリッド特典]** で、Azure ハイブリッド特典を適用しない場合は、 **[いいえ]** を選択します。 **[次へ]** を選択します。 アクティブなソフトウェア アシュアランスまたは Windows Server サブスクリプションの対象となっている Windows Server マシンがあり、移行するマシンに特典を適用する場合は、 **[はい]** を選択します。 **[次へ]** を選択します。

      > [!NOTE]
      > Contoso の場合、これは Azure Dev/Test サブスクリプションであるため、管理者は Azure ハイブリッド特典に対して **[いいえ]** を選択します。 これにより、コンピューティングにのみ課金されます。 [Azure ハイブリッド特典](https://azure.microsoft.com/pricing/hybrid-benefit)は、ソフトウェア アシュアランスの特典が適用された運用システムにのみ使用します。

8. **[コンピューティング]** で、VM の名前、サイズ、OS ディスクの種類、可用性セットを確認します。 VM は [Azure の要件](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#vmware-requirements)に準拠している必要があります。

    - **VM サイズ:** 評価の推奨事項を使用している場合は、このドロップダウン リストに推奨サイズが表示されます。 それ以外の場合は、Azure Migrate によって、Azure サブスクリプション内の最も近いサイズが選択されます。 代わりに、 **[Azure VM サイズ]** でサイズを手動で選択することもできます。
    - **OS ディスク:** VM の OS (ブート) ディスクを指定します。 OS ディスクには、オペレーティング システムのブートローダーとインストーラーが含まれます。
    - **可用性セット:** 移行後に VM を Azure 可用性セットに配置する必要がある場合は、セットを指定します。 このセットは、移行用に指定するターゲット リソース グループ内に存在する必要があります。

9. **[ディスク]** で、VM ディスクを Azure にレプリケートするかどうかを指定し、Azure でのディスクの種類 (Standard SSD/HDD または Premium マネージド ディスク) を選択します。 **[次へ]** を選択します。 レプリケーションからディスクを除外できます。 除外すると、移行後に Azure VM 上に存在しなくなります。

10. **[レプリケーションの確認と開始]** で、設定を確認し、 **[レプリケート]** を選択して、サーバーの初期レプリケーションを開始します。

> [!NOTE]
> レプリケーションを開始する前であれば、 **[管理]**  >  **[マシンのレプリケート]** でレプリケーションの設定をいつでも更新できます。 レプリケーションの開始後は、設定を変更することができません。

## <a name="step-4-migrate-the-vms"></a>手順 4:VM を移行する

Contoso の管理者は、クイック テスト移行を実行し、その後、完全な移行を実行して VM を移行します。

### <a name="run-a-test-migration"></a>テスト移行を実行する

1. **[移行の目標]**  >  **[サーバー]**  >  **[Azure Migrate: Server Migration]** で、 **[移行されたサーバーをテストします]** を選択します。

    ![移行されたサーバーをテストするための選択を示すスクリーンショット。](./media/contoso-migration-devtest-to-iaas/test-migrated-servers.png)
    
    _図 11:移行されたサーバーのテスト_

2. テストする VM を選択したまま (または右クリックし)、 **[テスト移行]** を選択します。

    ![移行テスト用のボタンを示すスクリーンショット。](./media/contoso-migration-devtest-to-iaas/test-migrate.png)
    
    _図 12:移行のテスト_

3. **[テスト移行]** で、移行後に Azure VM が配置される Azure 仮想ネットワークを選択します。 非運用環境の仮想ネットワークを使用することをお勧めします。
4. **テスト移行**ジョブが開始されます。 ポータルの通知でジョブを監視します。
5. 移行の完了後、Azure portal の **[仮想マシン]** で、移行された Azure VM を確認します。 マシン名には、 **-Test** サフィックスが含まれています。
6. テストが完了したら、 **[マシンのレプリケート]** で Azure VM を選択したまま (または右クリックし)、 **[テスト移行をクリーンアップ]** を選択します。

    ![テスト移行をクリーンアップするための選択内容を示すスクリーンショット。](./media/contoso-migration-devtest-to-iaas/clean-up.png)
    
    _図 13:テスト移行のクリーンアップ_

### <a name="migrate-the-vms"></a>VM を移行する

次に、Contoso の管理者は、完全移行を実行します。

1. Azure Migrate プロジェクトで、 **[サーバー]**  >  **[Azure Migrate:Server Migration]**  >  **[サーバーをレプリケートしています]** の順に選択します。

    ![サーバーをレプリケートするための選択を示すスクリーンショット。](./media/contoso-migration-devtest-to-iaas/replicating-servers.png)
    
    _図 14: サーバーのレプリケート_

2. **[マシンのレプリケート]** で VM を選択したまま (または右クリックし)、 **[移行]** を選択します。
3. **[移行]**  >  **[仮想マシンをシャットダウンし、データ損失のない計画された移行を実行しますか]** で、 **[はい]**  >  **[OK]** の順に選択します。 既定では、Azure Migrate によってオンプレミス VM がシャットダウンされ、前回のレプリケーションが発生した後に行われた VM の変更を同期するためにオンデマンド レプリケーションが実行されます。 こうすることで、データ損失がなくなります。 VM をシャットダウンしたくない場合は、 **[いいえ]** を選択します。
4. VM に対して移行ジョブが開始されます。 Azure 通知でジョブを追跡します。
5. ジョブが完了したら、 **[仮想マシン]** ページで VM を表示して管理できます。

**さらにサポートが必要な場合**

[テスト移行を実行する](https://docs.microsoft.com/azure/migrate/tutorial-migrate-vmware#run-a-test-migration)方法、および [VM を Azure に移行する](https://docs.microsoft.com/azure/migrate/tutorial-migrate-vmware#migrate-vms)方法を参照してください。

## <a name="clean-up-after-migration"></a>移行後にクリーンアップする

移行が完了すると、Azure VM 上で、SmartHotel360 および osTicket アプリケーションの開発 VM の実行が開始されます。

次に、Contoso は、以下のクリーンアップ手順を完了する必要があります。

- 移行が完了したら、レプリケーションを停止します。
- `WEBVMDEV`、`SQLVMDEV`、`OSTICKETWEBDEV`、`OSTICKETMYSQLDEV` の各 VM を vCenter インベントリから削除します。
- ローカル バックアップ ジョブからすべての VM を削除します。
- VM の新しい場所と IP アドレスを示すように内部ドキュメントを更新します。
- VM と対話しているリソースを確認し、すべての関連する設定またはドキュメントを新しい構成を反映するように更新します。

## <a name="review-the-deployment"></a>デプロイを再調査する

これでアプリケーションが実行されるようになったので、Contoso は、Azure でアプリケーションを完全に操作可能にし、セキュリティで保護する必要があります。

### <a name="security"></a>セキュリティ

Contoso セキュリティ チームは、Azure VM を調査して、セキュリティの問題を特定します。 アクセスを制御するために、VM のネットワーク セキュリティ グループ (NSG) を見直します。 NSG は、許可されたトラフィックだけがアプリケーションに到達できるようにするために使用されます。 チームは、Azure Disk Encryption と Azure Key Vault を使用して、ディスク上のデータをセキュリティで保護することも検討します。

詳細については、「[Azure における IaaS ワークロードのセキュリティに関するベスト プラクティス](https://docs.microsoft.com/azure/security/fundamentals/iaas)」を参照してください。

## <a name="business-continuity-and-disaster-recovery"></a>事業継続とディザスター リカバリー

事業継続とディザスター リカバリーのために、Contoso はデータを保護します。 Contoso は、Azure Backup サービスを使用して VM 上のデータをバックアップします。 詳細については、「[Azure VM バックアップの概要](https://docs.microsoft.com/azure/backup/backup-azure-vms-introduction)」をご覧ください。

### <a name="licensing-and-cost-optimization"></a>ライセンスとコストの最適化

Contoso は、すべての Azure 開発リソースがこの Dev/Test サブスクリプションを使用して作成されるようにすることで、コストを 80% 削減できます。 管理者は、[Azure Cost Management および Billing](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview) を有効にして、Azure リソースの監視と管理を支援します。

## <a name="conclusion"></a>まとめ

この記事で、Contoso は、Azure で SmartHotel360 および osTicket アプリケーションに使用されていた開発 VM を再ホストしました。 管理者は、Azure Migrate を使用して、アプリケーション VM を Azure VM に移行しました。サーバー移行ツール。
