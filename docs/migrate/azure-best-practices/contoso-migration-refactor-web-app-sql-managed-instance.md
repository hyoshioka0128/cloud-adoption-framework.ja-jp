---
title: Azure App Service と Azure SQL Database Managed Instance に移行してアプリをリファクタリングする
description: Contoso がオンプレミス アプリを Azure App Service Web アプリと Azure SQL Server Database Managed Instance に移行して、オンプレミス アプリをリホストする方法について説明します。
author: givenscj
ms.author: abuck
ms.date: 04/02/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: migrate
services: azure-migrate
ms.openlocfilehash: 0e288b29077d68ee4b0c0522537abe0baaa276ac
ms.sourcegitcommit: 60d8b863d431b5d7c005f2f14488620b6c4c49be
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2020
ms.locfileid: "83223696"
---
<!-- cSpell:ignore givenscj WEBVM SQLVM contosohost vcenter contosodc smarthotel SQLMI SHWCF SHWEB -->

# <a name="refactor-an-on-premises-app-to-an-azure-app-service-web-app-and-azure-sql-managed-instance"></a>Azure App Service Web アプリと Azure SQL Managed Instance にオンプレミス アプリをリファクタリングする

この記事では、Contoso という架空の会社が、Azure への移行の一環として、VMware VM 上で実行される 2 層の Windows .NET アプリをリファクターする方法を示します。 アプリのフロントエンド VM を、Azure App Service の Web アプリに移行します。 また、Contoso がアプリ データベースを Azure SQL Database Managed Instance に移行する方法も説明します。

この例で使用される SmartHotel360 アプリは、オープン ソースとして提供されています。 独自のテスト目的に沿って使用する場合は、[GitHub](https://github.com/Microsoft/SmartHotel360) からダウンロードできます。

## <a name="business-drivers"></a>ビジネス ドライバー

IT リーダーシップ チームは、ビジネス パートナーと密接に連絡を取り合い、彼らがこの移行で何を達成しようとしているのかを理解しました。

- **ビジネスの成長への対応。** Contoso は成長を続けており、オンプレミスのシステムとインフラストラクチャに負荷がかかっています。
- **効率化。** Contoso では、不要な手順を取り除き、開発者とユーザーのプロセスを効率化する必要があります。 ビジネス部門は IT に対して、時間やコストを無駄にせず、迅速に作業を行ってもらう必要があります。これは、例えば、顧客の要求に素早く対応するためです。
- **機敏性の向上。**  Contoso IT は、ビジネス部門の要求に対して、対応力を向上させる必要があります。 また、グローバル経済で成功を収めるために、市場の変化に対して、より迅速な対応ができる必要があります。 ビジネスの妨げになったり、ビジネスの機会を壊すようなことがあってはなりません。
- **スケール。** ビジネスが順調に成長していく中で、Contoso IT は、同じペースで拡張できるシステムを提供する必要があります。
- **コストを削減する。** Contoso はライセンス コストを最小限に抑えたいと考えています。

## <a name="migration-goals"></a>移行の目標

Contoso クラウド チームは、この移行の目標を設定しました。 これらの目標を使用して、最良の移行方法を決定しました。

<!-- markdownlint-disable MD033 -->

**必要条件** | **詳細**
--- | ---
**App** | このアプリは、Azure でも現在と同じくクリティカルです。 <br><br> 現在の VMWare と同等のパフォーマンス機能が必要です。 <br><br> チームはアプリへの投資を望んでいません。 現在のところ、管理者は単にアプリをクラウドに安全に移行します。 <br><br> チームは、アプリが現在実行されている Windows Server 2008 R2 のサポートを終了したいと考えています。 <br><br> また、SQL Server 2008 R2 から最新の PaaS Database プラットフォームに移行したいと考えています。これにより、管理の必要性を最小限に抑えられます。 <br><br> Contoso では、可能であれば、SQL Server ライセンスとソフトウェア アシュアランスへの投資を活かしたいと考えています。 <br><br> さらに、Contoso は Web 層の単一障害点を軽減したいと考えています。
**制限事項** | このアプリは、同じ VM 上で実行されている ASP.NET アプリと WCF サービスで構成されています。 Azure App Service を使用して 2 つの Web アプリに分割したいと考えています。
**Azure** | Contoso は Azure にアプリを移行したいと考えていますが、VM 上では実行したくありません。 Contoso では、Azure PaaS サービスを Web 層とデータ層の両方に利用したいと考えています。
**DevOps** | Contoso は、ビルドとリリース パイプラインに Azure DevOps を使用して DevOps モデルに移行したいと考えています。

<!-- markdownlint-enable MD033 -->

## <a name="solution-design"></a>ソリューション設計

Contoso は目標と要件を決定した後、デプロイ ソリューションを設計およびレビューし、移行に使用する Azure サービスを含め、移行プロセスを決めます。

### <a name="current-app"></a>現在のアプリ

- SmartHotel360 オンプレミス アプリは 2 つの VM (WEBVM と SQLVM) に階層化されています。
- VM は、VMware ESXi ホスト **contosohost1.contoso.com** (バージョン 6.5) 上に配置されます。
- VMware 環境は、VM 上で実行中の vCenter Server 6.5 (**vcenter.contoso.com**) によって管理されています。
- Contoso にはオンプレミスのデータセンター (contoso-datacenter) があり、そこにオンプレミスのドメイン コントローラー (**contosodc1**) が含まれています。
- Contoso データセンター内のオンプレミス VM は、移行が行われた後に使用停止にされます。

### <a name="proposed-solution"></a>提案されるソリューション

- アプリの Web 層については、Azure App Service を使用することに決めました。 この PaaS サービスにより、わずかな構成変更だけでアプリをデプロイできます。 Visual Studio を使用して変更を加え、2 つの Web アプリをデプロイします。 1 つは Web サイト用、もう 1 つは WCF サービス用です。
- DevOps パイプラインの要件を満たすため、Contoso はソース コード管理のために、Git リポジトリと共に Azure DevOps を選択しました。 コードをビルドして Azure App Service へデプロイするには、自動ビルドとリリースが使用されます。

### <a name="database-considerations"></a>データベースの考慮事項

Contoso はソリューション設計プロセスの一環として、Azure SQL Database と SQL Server Managed Instance の機能を比較しました。 Managed Instance にすることを決定するにあたり、次の考慮事項が役に立ちました。

- Managed Instance では、最新のオンプレミス SQL Server バージョンとのほぼ 100% の互換性を提供することが目的とされています。 Microsoft は、SQL Server をオンプレミスまたは IaaS VM で実行していて、最小限の設計変更で完全に管理されたサービスにアプリを移行することを望んでいるお客様に、Managed Instance を推奨しています。
- Contoso は、多数のアプリをオンプレミスから IaaS に移行することを計画しています。 それらの多くは、ISV から提供されたものです。 Contoso は、サポートされていない可能性がある SQL Database を使用するのではなく、Managed Instance を使用すると、これらのアプリのデータベース互換性を確保するのに役立つことを理解しています。
- Contoso は、完全に自動化された Azure Database Migration Service を使用して、Managed Instance へのリフトアンドシフト移行を簡単に実行できます。 このサービスを導入すると、Contoso は将来のデータベース移行にそれを再利用できます。
- SQL Managed Instance では、SmartHotel360 アプリの重要な問題である SQL Server エージェントがサポートされています。 Contoso ではこの互換性が必要です。互換性がないと、アプリで必要なメンテナンス プランを再設計する必要があります。
- ソフトウェア アシュアランスに基づき、Contoso は、SQL Database Managed Instance で SQL Server 用の Azure ハイブリッド特典を利用して、既存のライセンスを割引料金のライセンスに交換することができます。 これによって、Contoso は Managed Instance を最大 30% 節約することができます。
- SQL Managed Instance は仮想ネットワークに完全に含まれるため、Contoso のデータに対して高い分離性とセキュリティが提供されます。 Contoso は、パブリック インターネットから分離された環境を維持しながら、パブリック クラウドのメリットを得ることができます。
- Managed Instance では、常時暗号化、動的データ マスキング、行レベルのセキュリティ、脅威検出など、多くのセキュリティ機能がサポートされています。

### <a name="solution-review"></a>ソリューションのレビュー

Contoso は、長所と短所の一覧をまとめて、提案されたデザインを評価します。

<!-- markdownlint-disable MD033 -->

**考慮事項** | **詳細**
--- | ---
**長所** | Azure に移行するために SmartHotel360 アプリ コードを変更する必要はありません。 <br><br> Contoso では、SQL Server と Windows Server の両方に Azure ハイブリッド特典を使用して、ソフトウェア アシュアランスへの投資を活かすことができます。 <br><br> 移行後は、Windows Server 2008 R2 をサポートする必要がなくなります。 詳細については、「[Microsoft ライフサイクル ポリシー](https://aka.ms/lifecycle)」を参照してください。 <br><br> Contoso は複数のインスタンスがあるアプリの Web 層を構成することができたので、単一障害点ではなくなりました。 <br><br> データベースは古い SQL Server 2008 R2 に依存しなくなります。 <br><br> SQL Managed Instance では、Contoso の技術面の要件と目標がサポートされています。 <br><br> Managed Instance では、SQL Server 2008 R2 から移行するときに、現在の展開との 100% の互換性が提供されます。 <br><br> ソフトウェア アシュアランスと、SQL Server および Windows Server の Azure ハイブリッド特典を使用して、投資を活用できます。 <br><br> 将来の移行では、Azure Database Migration Service を再利用できます。 <br><br> SQL Managed Instance には、Contoso が構成する必要のないフォールト トレランスが組み込まれています。 そのため、データ層がフェールオーバーの単一ポイントではなくなります。
**短所** | Azure App Services は、各 Web アプリに対して 1 つのアプリのデプロイのみをサポートしています。 これは、2 つの Web アプリをプロビジョニングする必要がある (Web サイト用に 1 つと WCF サービス用に 1 つ) ことを意味します。 <br><br> データ層については、Contoso がオペレーティング システムやデータベース サーバーをカスタマイズしたい場合、または SQL Server と共にサードパーティ製アプリを実行したい場合は、Managed Instance は最適なソリューションではない可能性があります。 IaaS VM で SQL Server を実行すると、このような柔軟性が提供されます。

<!-- markdownlint-enable MD033 -->

## <a name="proposed-architecture"></a>提案されたアーキテクチャ

![シナリオのアーキテクチャ](./media/contoso-migration-refactor-web-app-sql-managed-instance/architecture.png)

### <a name="migration-process"></a>移行プロセス

1. Contoso は、Azure SQL Database Managed Instance をプロビジョニングし、Azure Database Migration Service (DMS) を使用して SmartHotel360 データベースをそれに移行します。
2. Contoso は Web アプリをプロビジョニングして構成し、そこに SmartHotel360 アプリをデプロイします。

    ![移行プロセス](./media/contoso-migration-refactor-web-app-sql-managed-instance/migration-process.png)

### <a name="azure-services"></a>Azure サービス

**サービス** | **説明** | **コスト**
--- | --- | ---
[Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview) | Azure Database Migration Service を使用すると、複数のデータベース ソースから Azure データ プラットフォームに、ダウンタイムを最小限に抑えながらシームレスに移行できます。 | [サポートされているリージョン](https://docs.microsoft.com/azure/dms/dms-overview#regional-availability)に関する情報と、[Database Migration Service の価格](https://azure.microsoft.com/pricing/details/database-migration)に関する情報をご覧ください。
[Azure SQL Database マネージド インスタンス](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) | Managed Instance は、Azure クラウド内の完全に管理された SQL Server インスタンスを表すマネージド データベース サービスです。 最新バージョンの SQL Server データベース エンジンと同じコードを使用し、最新の機能、パフォーマンスの向上、およびセキュリティ更新プログラムが適用されています。 | Azure で実行されている SQL Database Managed Instance を使用すると、容量に基づく料金がかかります。 [Managed Instance の価格の詳細については、こちらを参照してください](https://azure.microsoft.com/pricing/details/sql-database/managed)。
[Azure App Service](https://docs.microsoft.com/azure/app-service/overview) | フルマネージド プラットフォームで強力なクラウド アプリを作成 | サイズ、場所、使用時間に基づくコスト。 [詳細については、こちらを参照してください](https://azure.microsoft.com/pricing/details/app-service/windows)。
[Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/what-is-azure-pipelines) | 継続的インテグレーションと継続的デプロイ (CI/CD) パイプラインをアプリの開発に提供します。 パイプラインは、アプリ コードを管理する Git リポジトリで始まり、パッケージおよびその他のビルド成果物を生成するためのビルド システム、開発、テスト、および運用環境での変更を配置するリリース管理システムへと続きます。

## <a name="prerequisites"></a>前提条件

このシナリオを実行するために Contoso に必要なものを以下に示します。

<!-- markdownlint-disable MD033 -->

**必要条件** | **詳細**
--- | ---
**Azure サブスクリプション** | Contoso は前の記事でサブスクリプションを作成しました。 Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/pricing/free-trial)を作成してください。 <br><br> 無料アカウントを作成する場合、サブスクリプションの管理者としてすべてのアクションを実行できます。 <br><br> 既存のサブスクリプションを使用しており、管理者でない場合は、管理者に依頼して所有者アクセス許可または共同作成者アクセス許可を割り当ててもらう必要があります。
**Azure インフラストラクチャ** | [Contoso で Azure インフラストラクチャを設定する方法](./contoso-migration-infrastructure.md)を確認してください。

<!--markdownlint-enable MD033 -->

## <a name="scenario-steps"></a>シナリオのステップ

Contoso が移行を実行する方法を次に示します。

> [!div class="checklist"]
>
> - **ステップ 1:SQL Database Managed Instance を設定する。** Contoso では、オンプレミス SQL Server データベースの移行先となる既存のマネージド インスタンスが必要です。
> - **手順 2:Azure Database Migration Service (DMS) を使用して移行する。** Contoso は、Azure Data Migration Service を使用してアプリのデータベースを移行します。
> - **ステップ 3:Web アプリをプロビジョニングする。** Contoso は 2 つの Web アプリをプロビジョニングします。
> - **手順 4:Azure DevOps を設定する。** Contoso は新しい Azure DevOps プロジェクトを作成し、Git リポジトリをインポートします。
> - **手順 5:接続文字列を構成する。** Contoso は Web 層 Web アプリ、WCF サービス Web アプリ、および SQL インスタンスが通信できるように接続文字列を構成します。
> - **手順 6:ビルドとリリース パイプラインを設定する。** 最後の手順として、Contoso はアプリを作成するようにビルドとリリース パイプラインを設定し、それらを 2 つの個別の Web アプリにデプロイします。

## <a name="step-1-set-up-a-sql-database-managed-instance"></a>手順 1:SQL Database Managed Instance を設定する

Azure SQL Database Managed Instance を設定するため、Contoso には次の要件を満たしているサブネットが必要です。

- サブネットは専用でなければなりません。 また、サブネットは空で他のクラウド サービスを含んでいない必要があります。 サブネットはゲートウェイ サブネットであってはなりません。
- Managed Instance を作成した後は、サブネットにリソースを追加することはできません。
- サブネットにネットワーク セキュリティ グループを関連付けることはできません。
- サブネットにはユーザー定義のルート テーブルが必要です。 割り当てられている唯一のルートが 0.0.0.0/0 の次ホップ インターネットである必要があります。
- 仮想ネットワーク用に省略可能なカスタム DNS が指定されている場合、Azure 内の再帰的なリゾルバーの仮想 IP アドレス `168.63.129.16` をリストに追加する必要があります。 [Azure SQL Database Managed Instance のカスタム DNS を構成する](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-custom-dns)方法についてご確認ください。
- サブネットにサービス エンドポイント (ストレージまたは SQL) を関連付けることはできません。 仮想ネットワークではサービス エンドポイントを無効にする必要があります。
- サブネットには 16 個以上の IP アドレスが必要です。 [Managed Instance サブネットのサイズを指定する方法については、こちらを参照してください](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vnet-subnet)。
- Contoso のハイブリッド環境では、カスタム DNS 設定が必要です。 Contoso は、自社の 1 つ以上の Azure DNS サーバーを使用するように DNS 設定を構成します。 [DNS のカスタマイズの詳細については、こちらを参照してください](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-custom-dns)。

### <a name="set-up-a-virtual-network-for-the-managed-instance"></a>Managed Instance 用の仮想ネットワークを設定する

Contoso の管理者は仮想ネットワークを次のように設定します。

1. 米国東部 2 プライマリ リージョンに新しい仮想ネットワーク (**VNET-SQLMI-EU2**) を作成します。 その仮想ネットワークを **ContosoNetworkingRG** リソース グループに追加します。
2. 10.235.0.0/24 のアドレス空間を割り当てます。 その範囲がエンタープライズ内の他のどのネットワークとも重複しないことを確認します。
3. ネットワークに以下の 2 つのサブネットを追加します。
    - **SQLMI-DS-EUS2** (10.235.0.0.25)
    - **SQLMI-SAW-EUS2** (10.235.0.128/29) このサブネットは、Managed Instance にディレクトリを接続するために使用されます。

      ![Managed Instance - 仮想ネットワークの作成](./media/contoso-migration-rehost-vm-sql-managed-instance/mi-vnet.png)

4. 仮想ネットワークとサブネットがデプロイされた後、ネットワークを以下のようにピアリングします。

    - **VNET-SQLMI-EUS2** と **VNET-HUB-EUS2** (米国東部 2 のハブ仮想ネットワーク) とピアリングします。
    - **VNET-SQLMI-EUS2** と **VNET-PROD-EUS2** (実稼働ネットワーク) をピアリングします。

      ![ネットワーク ピアリング](./media/contoso-migration-rehost-vm-sql-managed-instance/mi-peering.png)

5. Contoso は、カスタム DNS 設定を行います。 DNS は、最初は Contoso の Azure ドメイン コント ローラーを指しています。 Azure DNS はセカンダリです。 Contoso Azure ドメイン コントローラーは、以下のように配置されます。

    - 配置先は **PROD-DC-EUS2** サブネットで、これは米国東部 2 の実稼働ネットワーク (**VNET-PROD-EUS2**) 内にあります。
    - **CONTOSODC3** アドレス: 10.245.42.4
    - **CONTOSODC4** アドレス: 10.245.42.5
    - Azure DNS リゾルバー: 168.63.129.16

      ![ネットワークの DNS サーバー](./media/contoso-migration-rehost-vm-sql-managed-instance/mi-dns.png)

**さらにサポートが必要な場合**

- [SQL Database Managed Instance の概要については、こちらを参照してください](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance)。
- [SQL Database Managed Instance 用の仮想ネットワークを作成する方法については、こちらを参照してください](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vnet-subnet)。
- [ピアリングを設定する方法については、こちらを参照してください](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering)。
- [Azure Active Directory DNS 設定を更新する方法については、こちらを参照してください](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance)。

### <a name="set-up-routing"></a>ルーティングを設定する

Managed Instance は、プライベート仮想ネットワーク内に配置されます。 仮想ネットワークが Azure Management Service と通信するためのルート テーブルが必要です。 仮想ネットワークが、仮想ネットワークを管理するサービスと通信できない場合、仮想ネットワークへのアクセスができなくなります。

Contoso は以下の点を考慮します。

- ルート テーブルには、Managed Instance から送信されたパケットを仮想ネットワーク内でルーティングする方法を指定した一連のルール (ルート) が含まれています。
- ルート テーブルは、Managed Instance がデプロイされているサブネットに関連付けられています。 サブネットから離れる各パケットは、関連付けられたルート テーブルに基づいて処理されます。
- 1 つのサブネットは、1 つのルート テーブルにのみ関連付けることができます。
- Microsoft Azure では、ルート テーブルの作成に追加料金はかかりません。

 Contoso の管理者は次のようにしてルーティングを設定します。

1. **ContosoNetworkingRG** リソース グループ内にユーザー定義ル－ト テーブルを作成します。

    ![ルート テーブル](./media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table.png)

2. Managed Instance の要件に準拠するため、ルート テーブル (**MIRouteTable**) を展開した後、アドレス プレフィックスが 0.0.0.0/0 のルートを追加します。 **[次ホップの種類]** オプションは **[インターネット]** に設定します。

    ![ルート テーブルのプレフィックス](./media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table-prefix.png)

3. (**VNET-SQLMI-EUS2** ネットワーク内の) **SQLMI-DB-EUS2** サブネットにルート テーブルを関連付けます。

    ![ルート テーブルのサブネット](./media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table-subnet.png)

**さらにサポートが必要な場合**

[Managed Instance 用のルートを設定する方法については、こちらを参照してください](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started)。

### <a name="create-a-managed-instance"></a>マネージド インスタンスを作成する

以上で Contoso の管理者は、SQL Database Managed Instance をプロビジョニングできるようになりました。

1. Managed Instance はビジネス アプリにサービスを提供するため、米国東部 2 プライマリ リージョンに Managed Instance をデプロイします。 その Managed Instance を **ContosoRG** リソース グループに追加します。
2. 価格レベルを選択し、インスタンスのコンピューティングとストレージのサイズを設定します。 [Managed Instance の価格の詳細については、こちらを参照してください](https://azure.microsoft.com/pricing/details/sql-database/managed)。

    ![マネージド インスタンス](./media/contoso-migration-rehost-vm-sql-managed-instance/mi-create.png)

3. Managed Instance をデプロイすると、**ContosoRG** リソース グループ内に 2 つの新しいリソースが表示されます。

    - 仮想クラスター (複数の Managed Instance が存在する場合)
    - SQL Server Database Managed Instance

      ![マネージド インスタンス](./media/contoso-migration-rehost-vm-sql-managed-instance/mi-resources.png)

**さらにサポートが必要な場合**

[Managed Instance をプロビジョニングする方法については、こちらを参照してください](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started)。

## <a name="step-2-migrate-with-azure-database-migration-service-dms"></a>手順 2:Azure Database Migration Service (DMS) を使用して移行する

Contoso の管理者は、[ステップバイステップの移行チュートリアル](https://docs.microsoft.com/azure/dms/tutorial-sql-server-azure-sql-online)を使い、Azure Database Migration Service (DMS) を使用して移行します。 オンライン、オフラインの両方、およびハイブリッド (プレビュー) の移行を実行できます。

まとめると、次を実行する必要があります。

- VNet に接続されている `Premium` SKU を持つ Azure Database Migration Service (DMS) を作成します。
- Azure Database Migration Service (DMS) が仮想ネットワーク経由でリモート SQL Server にアクセスできることを確認します。 これには、仮想ネットワーク レベル、ネットワーク VPN、および SQL Server をホストするマシンで、 Azure から SQL サーバーにすべての受信ポートが許可されていることの確認が伴います。
- Azure Database Migration Service を構成する:
  - 移行プロジェクトを作成します。
  - ソース (オンプレミス データベース) を追加します。
  - ターゲットを選択します。
  - 移行するデータベースを選択します。
  - 詳細設定を構成します。
  - レプリケーション を開始します。
  - すべてのエラーを解決します。
  - 最終的なカットオーバーを実行します。

## <a name="step-3-provision-web-apps"></a>手順 3:Web アプリをプロビジョニングする

データベースの移行が完了し、Contoso 管理者は 2 つの Web アプリをプロビジョニングできるようになりました。

1. ポータルで **[Web アプリ]** を選択します。

    ![Web アプリ](./media/contoso-migration-refactor-web-app-sql-managed-instance/web-app1.png)

2. アプリ名 (**SHWEB-EUS2**) を指定し、Windows 上で実行し、運用リソース グループ **ContosoRG** に配置します。 新しい Web アプリと Azure App Service プランを作成します。

    ![Web アプリ](./media/contoso-migration-refactor-web-app-sql-managed-instance/web-app2.png)

3. Web アプリがプロビジョニングされた後、WCF サービス (**SHWCF-EUS2**) 用に Web アプリを作成するプロセスを繰り返します

    ![Web アプリ](./media/contoso-migration-refactor-web-app-sql-managed-instance/web-app3.png)

4. 完了したら、アプリのアドレスを参照して、アプリが正常に作成されたことを確認します。

## <a name="step-4-set-up-azure-devops"></a>手順 4:Azure DevOps を設定する

Contoso は、アプリケーションのために DevOps インフラストラクチャとパイプラインを構築する必要があります。 これを行うために、Contoso 管理者は新しい DevOps プロジェクトを作成し、コードをインポートしてから、ビルドとリリース パイプラインを設定します。

1. Contoso Azure DevOps アカウントで、新しいプロジェクト (**ContosoSmartHotelRefactor**) を作成し、バージョン コントロールに **[Git]** を選択します。

    ![新しいプロジェクト](./media/contoso-migration-refactor-web-app-sql-managed-instance/vsts1.png)

2. アプリ コードを現在保持している Git リポジトリをインポートします。 [GitHub のパブリック リポジトリ](https://github.com/Microsoft/SmartHotel360-Registration)内にあるので、ダウンロードすることができます。

    ![アプリ コードをダウンロードする](./media/contoso-migration-refactor-web-app-sql-managed-instance/vsts2.png)

3. コードをインポートしたら、Visual Studio をリポジトリに接続し、チーム エクスプローラーを使用してコードを複製します。

    ![プロジェクトに接続する](./media/contoso-migration-refactor-web-app-sql-managed-instance/devops1.png)

4. リポジトリが開発者のコンピューターに複製された後に、アプリ用のソリューション ファイルを開きます。 Web アプリと wcf サービスは、ファイル内にそれぞれ別のプロジェクトを持っています。

    ![ソリューション ファイル](./media/contoso-migration-refactor-web-app-sql-managed-instance/vsts4.png)

## <a name="step-5-configure-connection-strings"></a>手順 5:接続文字列の構成

Contoso 管理者は、Web アプリとデータベースがすべて通信できることを確認する必要があります。 そのために、コードと Web アプリで接続文字列を構成します。

1. WCF サービス (**SHWCF-EUS2**) の Web アプリで、 **[設定]**  >  **[アプリケーションの設定]** の順に選択し、**DefaultConnection** という新しい接続文字列を追加します。
2. 接続文字列は **SmartHotel-Registration** データベースからプルされ、正しい資格情報で更新する必要があります。

    ![接続文字列](./media/contoso-migration-refactor-web-app-sql-managed-instance/string1.png)

3. Visual Studio を使用して、ソリューション ファイルから **SmartHotel.Registration.wcf** プロジェクトを開きます。 WCF サービス **SmartHotel.Registration.Wcf** の web.config ファイルの **connectionStrings** セクションを、接続文字列を指定して更新する必要があります。

     ![接続文字列](./media/contoso-migration-refactor-web-app-sql-managed-instance/string2.png)

4. **SmartHotel.Registration.Web** の web.config ファイルの **client** セクションは、WCF サービスの新しい場所を指すように変更する必要があります。 これは、サービス エンドポイントをホストする WCF Web アプリの URL です。

    ![接続文字列](./media/contoso-migration-refactor-web-app-sql-managed-instance/strings3.png)

5. コードの変更が完了したら、管理者はその変更をコミットする必要があります。 Visual Studio でチーム エクスプローラーを使用して、コミットと同期を行います。

## <a name="step-6-set-up-build-and-release-pipelines-in-azure-devops"></a>手順 6:ビルドとリリース パイプラインを Azure DevOps で設定する

次に、Contoso 管理者は、Azure DevOps を構成して、ビルドおよびリリース プロセスを実行します。

1. Azure DevOps 内で、 **[Build and release]\(ビルドとリリース\)**  >  **[新しいパイプライン]** の順に選択します。

    ![新しいパイプライン](./media/contoso-migration-refactor-web-app-sql-managed-instance/pipeline1.png)

2. **[Azure Repos Git]** を選択し、関連するリポジトリを選択します。

    ![Git とリポジトリ](./media/contoso-migration-refactor-web-app-sql-managed-instance/pipeline2.png)

3. **[テンプレートの選択]** で、ビルド用の ASP.NET テンプレートを選択します。

     ![ASP.NET テンプレート](./media/contoso-migration-refactor-web-app-sql-managed-instance/pipeline3.png)

4. ビルドには **ContosoSmartHotelRefactor-ASP.NET-CI** という名前が使用されます。 **[保存してキューに登録]** を選択します。

     ![保存してキューに登録](./media/contoso-migration-refactor-web-app-sql-managed-instance/pipeline4.png)

5. これで最初のビルドが開始されます。 プロセスを監視するビルド番号を選択します。 完了後、プロセスのフィードバックを表示し、 **[成果物]** を選択してビルドの結果を確認できます。

    ![確認](./media/contoso-migration-refactor-web-app-sql-managed-instance/pipeline5.png)

6. **Drop** フォルダーにはビルドの結果が格納されています。

    - 2 つの zip ファイルは、アプリを格納するパッケージです。
    - これらのファイルは、Azure App Service にデプロイするためにリリース パイプライン内で使用されます。

     ![アーティファクト](./media/contoso-migration-refactor-web-app-sql-managed-instance/pipeline6.png)

7. **[リリース]**  >  **[+New pipeline]\(+新しいパイプライン\)** の順に選択します。

    ![新しいパイプライン](./media/contoso-migration-refactor-web-app-sql-managed-instance/pipeline7.png)

8. Azure App Service 用のデプロイ テンプレートを選択します。

    ![Azure App Service のデプロイ テンプレート](./media/contoso-migration-refactor-web-app-sql-managed-instance/pipeline8.png)

9. リリース パイプラインに **ContosoSmartHotel360Refactor** という名前を付けて、WCF Web アプリの名前 (SHWCF-EUS2) を**ステージ**として指定します。

    ![環境](./media/contoso-migration-refactor-web-app-sql-managed-instance/pipeline9.png)

10. ステージ下で、 **[1 個のジョブ、1 個のタスク]** を選択して WCF サービスのデプロイを構成します。

    ![WCF のデプロイ](./media/contoso-migration-refactor-web-app-sql-managed-instance/pipeline10.png)

11. サブスクリプションが選択および承認されていることを確認し、 **[App Service の名前]** を選択します。

     ![App Service の名前を選択する](./media/contoso-migration-refactor-web-app-sql-managed-instance/pipeline11.png)

12. パイプライン > **[成果物]** で、 **[+Add an artifact]\(成果物の追加\)** を選択し、**ContosoSmarthotel360Refactor** パイプラインを使用してビルドするように選択します。

     ![Build](./media/contoso-migration-refactor-web-app-sql-managed-instance/pipeline12.png)

13. 成果物にある稲妻アイコンを選択して、継続的配置トリガーを有効にします。

     ![稲妻アイコン](./media/contoso-migration-refactor-web-app-sql-managed-instance/pipeline13.png)

14. 継続的配置トリガーを **[有効]** に設定する必要があります。

    ![有効な継続的配置](./media/contoso-migration-refactor-web-app-sql-managed-instance/pipeline14.png)

15. ここで、ステージ 1 のジョブ (I タスク) に戻り **[Deploy Azure App Service]\(Azure App Service のデプロイ\)** を選択します。

    ![Azure App Service のデプロイ](./media/contoso-migration-refactor-web-app-sql-managed-instance/pipeline15.png)

16. **[Select a file or folder]\(ファイルまたはフォルダーの選択\)** で、ビルド中に作成していた **SmartHotel.Registration.Wcf.zip** ファイルを探して、 **[保存]** を選択します。

    ![WCF の保存](./media/contoso-migration-refactor-web-app-sql-managed-instance/pipeline16.png)

17. **[パイプライン]**  >  **[ステージ]** と選択し、 **[+追加]** を選択して **SHWEB EUS2** 用の環境を追加します。 別の Azure App Service のデプロイを選択します。

    ![環境の追加](./media/contoso-migration-refactor-web-app-sql-managed-instance/pipeline17.png)

18. Web アプリ (**SmartHotel.Registration.Web.zip**) ファイルを発行するためにプロセスを繰り返して Web アプリを修正します。

    ![Web アプリの発行](./media/contoso-migration-refactor-web-app-sql-managed-instance/pipeline18.png)

19. Web アプリを保存すると、リリース パイプラインが次のように表示されます。

     ![リリース パイプラインの概要](./media/contoso-migration-refactor-web-app-sql-managed-instance/pipeline19.png)

20. **[ビルド]** に戻り、 **[トリガー]**  >  **[継続的インテグレーションを有効にする]** の順に選択します。 これでパイプラインが有効になり、変更がコードに対してコミットされると、フル ビルドとリリースが発生します。

    ![継続的インテグレーションの有効化](./media/contoso-migration-refactor-web-app-sql-managed-instance/pipeline20.png)

21. **[保存してキューに登録]** を選択し、フル パイプラインを実行します。 新しいビルドがトリガーされ、続いてアプリの最初のリリースが Azure App Service に対して作成されます。

    ![パイプラインの保存](./media/contoso-migration-refactor-web-app-sql-managed-instance/pipeline21.png)

22. Contoso 管理者は、Azure DevOps からビルドとリリース パイプラインのプロセスに従うことができます。 ビルドが完了すると、リリースが開始されます。

    ![アプリのビルドとリリース](./media/contoso-migration-refactor-web-app-sql-managed-instance/pipeline22.png)

23. パイプラインが完了すると、両方のサイトがデプロイされ、アプリが稼働します。

    ![リリースの完了](./media/contoso-migration-refactor-web-app-sql-managed-instance/pipeline23.png)

この時点で、アプリは正常に Azure に移行されています。

## <a name="clean-up-after-migration"></a>移行後にクリーンアップする

移行後、Contoso は、以下のクリーンアップ手順を完了する必要があります。

- vCenter のインベントリからオンプレミスの VM を削除します。
- ローカルのバックアップ ジョブからから VM を削除します。
- SmartHotel360 アプリの新しい場所を示すように社内ドキュメントを更新します。 Azure SQL Managed Instance データベースでデータベースは実行中と表示され、2 つの Web アプリでフロント エンドは実行中と表示されます。
- 使用停止されている VM と対話しているリソースがないか確認します。また、関連する設定やドキュメントがあれば、更新して新しい構成を反映します。

## <a name="review-the-deployment"></a>デプロイを再調査する

リソースを Azure に移行したら、新しいインフラストラクチャを完全に操作可能にして、セキュリティ保護する必要があります。

### <a name="security"></a>Security

- Contoso は新しい **SmartHotel-Registration** データベースがセキュリティで保護されていることを確認する必要があります。 [詳細については、こちらを参照してください](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview)。
- 特に、証明書で SSL を使用するように Web アプリを更新する必要があります。

### <a name="backups"></a>バックアップ

- Contoso は、Azure SQL Managed Instance データベースのバックアップ要件を確認する必要があります。 [詳細については、こちらを参照してください](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups)。
- また、Contoso は SQL Database のバックアップと復元の管理について確認する必要があります。 自動バックアップについては、[こちら](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups)をご覧ください。
- Contoso は、データベースのリージョン内フェールオーバーを提供するようにフェールオーバー グループを実装することを検討する必要があります。 [詳細については、こちらを参照してください](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview)。
- Contoso では、復元性のために、主要な米国東部 2 リージョンと米国中部リージョンに Web アプリをデプロイすることを検討する必要があります。 リージョンの障害が発生したときに確実にフェールオーバーするように Traffic Manager を構成できます。

### <a name="licensing-and-cost-optimization"></a>ライセンスとコストの最適化

- すべてのリソースをデプロイした後、Contoso は[インフラストラクチャ計画](./contoso-migration-infrastructure.md#set-up-tagging)に基づいて Azure タグを割り当てる必要があります。
- すべてのライセンスは、Contoso が使用している PaaS サービスのコストに組み込まれています。 これは EA から差し引かれます。
- Contoso は [Azure Cost Management](https://azure.microsoft.com/services/cost-management) を使用して、IT リーダーが定めた予算内に確実に収まるようにします。

## <a name="conclusion"></a>まとめ

この記事の中で、Contoso は、アプリのフロントエンド VM を 2 つの Azure App Service Web アプリに移行することで、Azure にある SmartHotel360 アプリをリファクターしました。 アプリ データベースを Azure SQL Managed Instance に移行しました。
