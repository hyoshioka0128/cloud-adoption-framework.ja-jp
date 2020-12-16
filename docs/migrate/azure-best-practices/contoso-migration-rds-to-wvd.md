---
title: オンプレミスのリモート デスクトップ サービスを Azure の Windows Virtual Desktop に移行するシナリオ
description: Contoso が、Windows Virtual Desktop を使用してオンプレミスの RDS を Azure に移行した方法について説明します。
author: benstegink
ms.author: abuck
ms.date: 07/01/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: migrate
ms.custom: think-tank
ms.openlocfilehash: 6cd6c05e390275bf52f4b39dc3f2d901117cdb46
ms.sourcegitcommit: b6f2b4f8db6c3b1157299ece1f044cff56895919
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2020
ms.locfileid: "97014642"
---
<!-- docutune:casing Lakeside SysTrack -->
<!-- cSpell:ignore msiexec Logix Robocopy UPDs -->

# <a name="move-on-premises-remote-desktop-services-to-azure-windows-virtual-desktop-scenario"></a>オンプレミスのリモート デスクトップ サービスを Azure の Windows Virtual Desktop に移行するシナリオ

Windows Virtual Desktop は、クラウドで実行される包括的なデスクトップおよびアプリケーション仮想化サービスです。 これは、簡素化された管理、Windows 10 Enterprise マルチセッション、Microsoft 365 Apps for enterprise 向けの最適化、リモート デスクトップ サービス (RDS) 環境のサポートを提供する、唯一の仮想デスクトップ インフラストラクチャ (VDI) です。 Windows デスクトップおよびアプリケーションを Azure に数分でデプロイしてスケーリングし、組み込みのセキュリティおよびコンプライアンス機能を利用できます。

| 移行オプション | 結果 |
|--- | --- |
| [Azure Migrate](/azure/migrate/migrate-services-overview) | オンプレミスの RDS 環境を評価し、移行する。 <br><br> Azure で Windows Virtual Desktop を使用してワークロードを実行する。 <br><br> [Windows Virtual Desktop 管理 UX](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux) を使用して Windows Virtual Desktop を管理する。 |

> [!NOTE]
> この記事では、Azure の Windows Virtual Desktop を使用して、オンプレミスの RDS 環境を Azure に移行する方法について重点的に説明します。

## <a name="business-drivers"></a>ビジネス ドライバー

Contoso IT チームは、ビジネス パートナーと緊密に連携して、Azure への VDI の移行に関するビジネス ドライバーを定義します。 このような促進要因には、次のものが含まれます。

- **現在の環境の有効期間の終了:** データセンターは、リースが終了するか、停止すると、容量が不足します。 クラウドに移行すると、実質的に無制限の容量が提供されます。 また、現在のソフトウェアの有効期間が終了し、Contoso の現在の VDI ソリューションを実行しているそのソフトウェアをアップグレードすることが必要になる場合もあります。
- **マルチセッション Windows 10 VDI:** クラウドで仮想化され、あらゆるデバイスで利用できるスケーラビリティに優れた最新の、唯一のマルチセッション Windows 10 デスクトップを Contoso のユーザーに提供します。
- **Microsoft 365 Apps for enterprise に最適化:** エンタープライズ エクスペリエンスに最適な Microsoft 365 アプリを提供します。マルチセッション仮想デスクトップのシナリオでは、Contoso のユーザーに最も生産性の高い仮想化エクスペリエンスを提供できます。
- **数分でデプロイしてスケーリング:** Azure portal での統合管理により、最新および従来のデスクトップ アプリケーションをすばやく仮想化し、数分でクラウドにデプロイできます。
- **Azure と Microsoft 365 での安全性と生産性:** すべてのユーザーの創造力を高め、コラボレーションを強化する完全なインテリジェント ソリューションをデプロイします。 Microsoft 365 に移行すると、Office 365、Windows 10、Enterprise Mobility + Security を入手できます。

## <a name="rds-on-premises-to-windows-virtual-desktop-in-the-cloud-goals"></a>オンプレミスの RDS からクラウドの Windows Virtual Desktop への移行の目標

Contoso は、ビジネス ドライバーを念頭に置いて、この移行の目標を明確にしました。

- クラウドの仮想デスクトップ環境を最新化する。
- 既存の Microsoft 365 ライセンスを活用する。
- ユーザーがリモートで作業する場合の企業データのセキュリティを強化する。
- 新しい環境をコストと成長に最適化する。

これらの目標は、Windows Virtual Desktop を使用するという決定を裏付け、それが Contoso にとって最良の移行方法であることを立証します。

## <a name="benefits-of-running-windows-virtual-desktop-in-azure"></a>Azure で Windows Virtual Desktop を実行する利点

Azure で Windows Virtual Desktop を使用すると、Contoso は VDI ソリューションをシームレスにすばやく簡単に実行、管理、スケーリングできるようになります。 また、会社は、最適化されたマルチセッション Windows 10 環境をユーザーに提供することもできます。

Contoso は、Azure の規模、パフォーマンス、セキュリティ、イノベーションを利用しながら、既存の Microsoft 365 ライセンスを活用できます。

次のような利点もあります。

- どこからでも Windows Virtual Desktop にアクセス可能。
- エンタープライズ環境に最適化された Microsoft 365 アプリ。
- Dev/Test 環境向けの Windows Virtual Desktop。

## <a name="solutions-design"></a>ソリューションの設計

Contoso は、目標と要件を明確にした後、デプロイ ソリューションを設計してレビューし、移行プロセスを確認します。

### <a name="current-architecture"></a>現在のアーキテクチャ

RDS は、オンプレミスのデータセンターにデプロイされています。 Microsoft 365 がライセンス供与され、組織によって使用されています。

### <a name="proposed-architecture"></a>提案されたアーキテクチャ

- Active Directory または Active Directory Domain Services を同期します。
- Windows Virtual Desktop を Azure にデプロイします。
- オンプレミスの RDS サーバーを Azure に移行します。
- ユーザー プロファイル ディスク (UPD) を FSLogix プロファイル コンテナーに変換します。

  ![提案されたアーキテクチャを示す図。](./media/contoso-migration-rds-to-wvd/proposed-architecture.png)
  "_図 1: 提案されたアーキテクチャ_

## <a name="solution-review"></a>ソリューションのレビュー

Contoso は、長所と短所の一覧をまとめて、提案されたデザインを評価します。

| 考慮事項 | 詳細 |
| --- | --- |
| **長所** | Windows 10 Enterprise マルチセッション環境。 <br><br> クラウドベースであるため、どこからでもアクセス可能。 <br><br> Windows Virtual Desktop 環境内で、Azure Files などの他の Azure サービスを利用。 <br><br> Microsoft のモダン デスクトップに最適化。 |
| **短所** | Azure に完全に最適化するために、Contoso はマルチユーザー セッションに最適化された Windows 10 イメージを再構築する必要がある。 <br><br> Windows Virtual Desktop ではユーザー プロファイル ディスクがサポートされていないため、UPD を FSLogix プロファイル コンテナーに移行する必要がある。 |

## <a name="migration-process"></a>移行プロセス

Contoso は、Lakeside 評価ツールと Azure Migrate を使用して、VM を Azure の Windows Virtual Desktop に移動します。 Contoso では次のことを行う必要があります。

- オンプレミスの RDS インフラストラクチャに対して評価ツールを実行し、Azure での Windows Virtual Desktop デプロイの規模を設定します。
- Windows 10 Enterprise マルチセッションまたは永続仮想マシンを使用して、Windows Virtual Desktop に移行します。
- 必要に応じてスケールアップとスケールダウンを行って Windows Virtual Desktop マルチセッションを最適化し、コストを管理します。
- アプリケーションを仮想化し、必要に応じてユーザーを割り当てて、引き続き Windows Virtual Desktop 環境をセキュリティで保護し、管理します。

  ![移行プロセスを示す図。](./media/contoso-migration-rds-to-wvd/migration-process-01.png)
  _図 2: 移行プロセス_

## <a name="scenario-steps"></a>シナリオのステップ

1. 現在の RDS 環境を評価します。
2. Azure に VDI と新しいイメージを作成し、VM を Azure に移行して永続化します。
3. UPD を FSLogix プロファイル コンテナーに変換します。
4. 永続 VM を Azure にレプリケートします。

## <a name="step-1-assess-the-current-on-premises-environment"></a>手順 1:現在のオンプレミス環境を評価する

Contoso は、`East US 2` Azure リージョンに Windows Virtual Desktop サービスをプロビジョニングします。 Windows Virtual Desktop を使用することで、Contoso は仮想マシンとホスト プールをプロビジョニングし、アプリケーション グループを作成できます。 また、Windows Virtual Desktop では、Windows Virtual Desktop ソリューションの内のすべてのサーバーに対して可用性セットも構成されます。 Windows Virtual Desktop を使用するとこにより、Contoso は高可用性 VDI 環境を作成し、必要に応じてすばやくスケールアップとスケールダウンができるようになります。

> [!NOTE]
> 評価時に、Contoso は RDS のマルチセッション (共有) インスタンスと永続 (またはユーザー専用) 仮想マシンという 2 つのシナリオを再検討します。

1. ドメイン サービス (Active Directory または Azure Active Directory Domain Services のいずれか) が、Azure Active Directory (Azure AD) と同期されていることを確認します。 Windows Virtual Desktop がデプロイされる Azure サブスクリプションと仮想ネットワークから、このドメイン サービスにアクセスできることを確認してください。

    > [!NOTE]
    > オンプレミスの Active Directory を Azure AD と同期するために、[Azure AD Connect](/azure/active-directory/hybrid/how-to-connect-install-express) の詳細を確認してください。

    <!-- -->

    > [!NOTE]
    > [Azure Active Directory Domain Services](/azure/active-directory-domain-services/tutorial-create-instance) をプロビジョニングし、Azure AD を同期する方法を確認してください。

1. 新しい Azure Migrate プロジェクトを作成します。

   ![新しい Azure Migrate プロジェクトの作成を示すスクリーンショット。](./media/contoso-migration-rds-to-wvd/new-azure-migrate.png)
   "_図 3: 新しい Azure Migrate プロジェクトの作成_

1. サーバーを評価して移行するオプションを選択し、**VDI** を選択して、ツールを追加します。

   ![VDI に関する Azure Migrate の目標を示すスクリーンショット。](./media/contoso-migration-rds-to-wvd/azure-migrate-goals-vdi.png)

   "_図 4:_ ターゲットの Azure Migrate の目標

1. 移行ジョブ データのサブスクリプション、リソース グループ、プロジェクト名、地域を設定します。

   ![Azure Migrate プロジェクトにジョブ データを追加する方法を示すスクリーンショット。](./media/contoso-migration-rds-to-wvd/add-a-tool.png)
   "_図 5: 移行へのジョブ データの追加_

    > [!IMPORTANT]
    > この場所は、新しい Windows Virtual Desktop 環境がデプロイされる場所ではありません。 ここには、Azure Migrate プロジェクトの関連データのみが格納されます。

1. 評価ツールとして、 **[Lakeside: SysTrack]** を選択します。

1. 移行ツールとして、 **[Azure Migrate: Server Migration]** を選択します。

1. これらのツールを移行プロジェクトに追加します。

   ![プロジェクトにツールを追加する方法を示すスクリーンショット。](./media/contoso-migration-rds-to-wvd/add-tools.png)
   "_図 6: 移行へのツールの追加_

1. Lakeside ツールで **[Azure Migrate への登録]** を選択して、現在の環境についての評価を開始します。

   ![Lakeside での Azure Migrate への登録をの示すスクリーンショット。](./media/contoso-migration-rds-to-wvd/lakeside-register-with-azure-migrate.png)

   _図 7:現在の環境の評価_

1. Contoso は、Azure Migrate と Lakeside を接続し、要求されたアクセス許可を受け入れます。

   ![Azure と Lakeside を接続するためのサインインを示すスクリーンショット。](./media/contoso-migration-rds-to-wvd/lakeside-login.png)
   "_図 8: Lakeside への Azure の接続_"

1. Contoso は、引き続き Lakeside ツールを使用して新しいテナントを作成し、現在のオンプレミス RDS 環境の評価を開始します。 ダッシュボードから、デプロイ ガイドにアクセスし、評価クライアントをダウンロードして現在の環境に展開します。これらのエージェントから収集されたデータを確認できます。

   ![スクリーンショットは、Lakeside ダッシュボードを示しています。](./media/contoso-migration-rds-to-wvd/lakeside-new-tenant-dashboard.png)
   "_図 9: Lakeside ダッシュボード_
1. 十分な量のデータを取得したら、Contoso は評価データを確認して最適な移行パスを決定します。 この評価データには、デスクトップ データからの生の評価データと、さまざまなユーザー ペルソナに分割されたデータが含まれています。 この情報には、次の内容が含まれます。

- 各ペルソナのユーザー数。
- ユーザーが使用しているアプリケーション。
- ユーザーによるリソース消費量。
- ユーザー ペルソナごとのリソース使用率の平均。
- VDI サーバーのパフォーマンス データ。
- 同時ユーザー レポート。
- 使用中の上位ソフトウェア パッケージ。

    ![Lakeside ダッシュボードのレポートが表示されたスクリーンショット。](./media/contoso-migration-rds-to-wvd/lakeside-dashboard-reports.png)
    _図 10:Lakeside ダッシュボード レポート_

Contoso はこのデータを分析して、プールされた Windows Virtual Desktop リソースと個人の Windows Virtual Desktop リソースの両方について最もコスト効果の高い使用法を決定します。

> [!NOTE]
> また、アプリケーション サーバーを Azure に移行することで、会社を Windows Virtual Desktop 環境に近づけて、ユーザーのネットワーク待ち時間を減らす必要もあります。

## <a name="step-2-create-the-windows-virtual-desktop-environment-for-pooled-desktops"></a>手順 2:プールされたデスクトップの Windows Virtual Desktop 環境を作成する

Contoso は、Azure portal を使用して、プールされたリソースに使用する Windows Virtual Desktop 環境を作成します。 後で、移行手順を実行して、個人用デスクトップをこの同じ環境に接続します。

1. 適切なサブスクリプションを選択し、新しい Windows Virtual Desktop ホスト プールを作成します。

   ![Windows Virtual Desktop ホスト プールのプロビジョニングを示すスクリーンショット。](./media/contoso-migration-rds-to-wvd/wvd-new-host-pool.png)
  "_図 11: 新しい Windows Virtual Desktop ホスト プール_

1. サブスクリプション、リソース グループ、リージョンを指定します。 次に、ホスト プールの名前、デスクトップの種類、既定のデスクトップ ユーザーを選択します。 Contoso は一部のユーザー向けの新しい共有環境から開始するため、デスクトップの種類は **[プール済み]** に設定されています。 既定のデスクトップ ユーザーは空白のままでもかまいません。 仮想マシンの構成に進みます。

   ![仮想マシンを構成するための前提条件を示すスクリーンショット。](./media/contoso-migration-rds-to-wvd/wvd-new-host-pool-basics-alt.png)
   "_図 12: 仮想マシンを構成するための前提条件_

   - Contoso は VM を構成し、 **[サイズの変更]** を選択してカスタム サイズを選ぶか、既定値を使用します。
   - これらのプールされたデスクトップの VM 名のプレフィックスとして、Windows Virtual Desktop が選択されています。
   - Contoso では、仮想マシンの設定で、新しい Windows 10 Enterprise マルチセッション機能を使用するために、プールされたサーバーを作成しているので、イメージ ソースの設定を **[ギャラリー]** のままにしておきます。 このオプションを使用すると、Contoso は VM として Windows 10 Enterprise マルチセッション イメージを選択できます。
   - Contoso は、Lakeside 評価のユーザーのペルソナに基づいて、ユーザー総数を **150** に設定します。
   - 他の設定として、ディスクの種類、AD ドメイン参加UPN フィールド、管理者パスワード、マシンの追加先の OU パス (省略可能)、仮想ネットワーク、サーバーを追加するためのサブネットなどがあります。

   ![仮想マシンの構成を示すスクリーンショット。](./media/contoso-migration-rds-to-wvd/wvd-new-host-pool-configure-virtual-machines-alt.png)
   "_図 13: 仮想マシンの構成_

    > [!NOTE]
    > Contoso は、この手順で新しい仮想ネットワークを作成することはできません。 この手順に進む前に、Contoso では Active Directory にアクセスできる仮想ネットワークを作成しておく必要があります。
    <!-- -->
    > [!NOTE]
    > Contoso は、多要素認証を必要とするユーザー アカウントをこの手順で使用することはできません。 ユーザーに多要素認証を使用する予定である場合は、この目的のためにサービス プリンシパルを作成する必要があります。

1. Contoso は、Windows Virtual Desktop 設定についてもう 1 つの検証を実行し、プールされた Windows Virtual Desktop 仮想マシンという新しい環境を作成します。

   ![仮想マシンの確認と作成を示すスクリーンショット。](./media/contoso-migration-rds-to-wvd/wvd-new-host-pool-review-create.png)
   "_図 14: 仮想マシンの確認と作成_

## <a name="step-3-convert-the-upds-to-fslogix-profile-containers"></a>手順 3:UPD を FSLogix プロファイル コンテナーに変換する

Windows Virtual Desktop ではユーザー プロファイル ディスク (UPD) がサポートされていないため、Contoso は [FSLogixMigration PowerShell モジュール](https://aka.ms/FSLogixMigrationPreviewModule)を使用して、すべての UPD を FSLogix に変換する必要があります。

<!-- docutune:casing FSLogixMigration -->

FSLogixMigration モジュールをインポートしたら、次の PowerShell コマンドレットを実行して、UPD から FSLogix に移行します。

> [!IMPORTANT]
> Hyper-V、Active Directory、および Pester 用のPowerShell モジュールは、コマンドレットを実行して UPD を FSLogix に変換するための前提条件です。

UDP の変換:

```powershell
Convert-RoamingProfile -ParentPath "C:\Users\" -Target "\\Server\FSLogixProfiles$" -MaxVHDSize 20 -VHDLogicalSectorSize 512
```

ローミング プロファイルの変換:

```powershell
Convert-RoamingProfile -ProfilePath "C:\Users\User1" -Target "\\Server\FSLogixProfiles$" -MaxVHDSize 20 -VHDLogicalSectorSize 512 -VHD -IncludeRobocopyDetails -LogPath C:\temp\Log.txt
```

この時点で、プールされたリソースを Windows 10 Enterprise マルチセッションで使用して移行が可能になりました。 Contoso は、Windows 10 Enterprise マルチセッションを使用するユーザーに必要なアプリケーションのデプロイを開始できます。

ただし、永続仮想マシンを Azure に移行する必要があります。

## <a name="step-4-replicate-and-persist-vms-to-windows-virtual-desktop"></a>手順 4:VM を Windows Virtual Desktop にレプリケートして永続化する

Contoso の移行プロセスの次の手順では、永続仮想マシンを Windows Virtual Desktop に移行します。 これを行うには、このプロセスの最初に作成した Azure Migrate: Server Migration ジョブに戻ります。

1. まず、Azure Migrate: Server Migration ツールで **[検出]** を選択します。

   ![Azure Migrate: Server Migration の [検出] オプションを示すスクリーンショット。](./media/contoso-migration-rds-to-wvd/wvd-persistent-discover.png)
   "_図 15: サーバー移行の検出_

1. Windows Virtual Desktop へのマシンのレプリケーションを管理する環境で、アプライアンスを変換します。 ターゲット リージョンが、Windows Virtual Desktop 環境が作成された `East US 2` に設定されていることを確認します。

   ![レプリケーションを管理するためのアプライアンスの作成を示すスクリーンショット。](./media/contoso-migration-rds-to-wvd/wvd-persistent-appliance.png)
   "_図 16: アプライアンスの変換_

1. Azure へのレプリケーションを開始するために、レプリケーション プロバイダーがダウンロードされ、インストールされて、Azure Migrate プロジェクトに登録されます。

   ![レプリケーションのダウンロードと構成を示すスクリーンショット。](./media/contoso-migration-rds-to-wvd/wvd-persistent-replication.png)
   "_図 17: Azure にレプリケートするための前提条件_

1. これで、Azure BLOB ストレージへのホストのレプリケーションが開始されます。 Contoso は、VM をテストして運用環境に移行する準備が整うまで、レプリケーションを継続できます。
   - Azure でマシンの実行が開始されたら、Contoso は各マシンに [Windows Virtual Desktop VM エージェント](https://aka.ms/WVDVMAgent)をインストールする必要があります。
   - インストールの一環として、Windows Virtual Desktop 環境の登録トークンを入力して、サーバーを適切な環境に関連付けます。

1. 登録トークンは、次のコマンドを使用して取得できます。

    ```powershell
    Export-RDSRegistrationInfo -TenantName "Contoso" -HostPoolName "ContosoWVD" | Select-Object -ExpandProperty Token > .\registration-token.txt
    ```

    > [!NOTE]
    > Contoso は、`msiexec` コマンドを使用し、登録トークンを変数として渡すことで、このプロセスを自動化することもできます。

1. 最終的な移行前の最後の手順として、Contoso は Azure Windows Virtual Desktop 設定の **[ユーザー]** の項目を選択し、サーバーをそれぞれのユーザーとグループにマップします。

   ![Windows Virtual Desktop リソースをユーザーとグループに割り当てる方法を示すスクリーンショット。](./media/contoso-migration-rds-to-wvd/wvd-persistent-user-mapping.png)
   "_図 18: 最終的な移行の前の最後の手順_

ホスト プールがユーザーに割り当てられたら、Contoso はそれらのマシンの移行の最終処理を実行し、引き続き、残りのオンプレミス VDI ホストを Azure に徐々に移行します。

## <a name="review-the-deployment"></a>デプロイを再調査する

仮想デスクトップとアプリケーション サーバーが Azure で実行されるようになったので、Contoso はデプロイを完全に運用化し、セキュリティで保護する必要があります。

### <a name="security"></a>セキュリティ

Contoso セキュリティ チームは、Azure VM を調査して、セキュリティの問題を特定します。 アクセスを制御するために、VM のネットワーク セキュリティ グループ (NSG) を見直します。 NSG は、許可されたトラフィックだけがアプリケーションに到達できるようにするために使用されます。 チームは、Azure Disk Encryption と Azure Key Vault を使用して、ディスク上のデータをセキュリティで保護することも検討します。

詳細については、「[Azure における IaaS ワークロードのセキュリティに関するベスト プラクティス](/azure/security/fundamentals/iaas)」を参照してください。

## <a name="business-continuity-and-disaster-recovery"></a>事業継続とディザスター リカバリー

事業継続とディザスター リカバリー (BCDR) のために、Contoso は Azure Backup を使用して VM 上のデータをバックアップすることでデータを保護します。 詳細については、「[Azure VM バックアップの概要](/azure/backup/backup-azure-vms-introduction)」をご覧ください。

### <a name="licensing-and-cost-optimization"></a>ライセンスとコストの最適化

- デスクトップ デプロイには、[Microsoft 365 ライセンス](https://azure.microsoft.com/pricing/details/virtual-desktop/)を使用します。
- Contoso は、[Azure Cost Management + Billing](/azure/cost-management-billing/cost-management-billing-overview) を有効にして、Azure リソースの監視と管理を支援します。
- Contoso には VM の既存のライセンスがあるため、アプリケーション サーバーの Azure ハイブリッド特典を利用します。 Contoso は、この特典を活用するために、既存の Azure VM を変換します。

## <a name="conclusion"></a>まとめ

この記事では、Contoso が RDS デプロイを、Azure でホストされている Windows Virtual Desktop に移動しました。
