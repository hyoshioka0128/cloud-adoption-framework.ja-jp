---
title: オンプレミス アプリを Azure VM および Azure SQL Database Managed Instance に移行して再ホストする
description: Contoso がオンプレミス アプリを Azure VM で Azure SQL Database Managed Instance を使用してリホストする方法を説明します。
author: givenscj
ms.author: abuck
ms.date: 04/02/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: migrate
services: azure-migrate
ms.openlocfilehash: fd859de8d7388a0cbd7c55255e005d98e6e87418
ms.sourcegitcommit: bd9872320b71245d4e9a359823be685e0f4047c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/26/2020
ms.locfileid: "83861584"
---
<!-- cSpell:ignore givenscj WEBVM SQLVM OSTICKETWEB OSTICKETMYSQL contosohost vcenter contosodc NSGs agentless SQLMI iisreset -->

# <a name="rehost-an-on-premises-app-on-an-azure-vm-and-sql-database-managed-instance"></a>オンプレミス アプリを Azure VM および SQL Database Managed Instance にリホストする

この記事では、Contoso という架空の会社が、Azure Migrate サービスを使用して、VMware VM 上で実行される 2 階層の Windows .NET フロントエンド アプリを Azure VM に移行する方法を説明します。 また、Contoso がアプリ データベースを Azure SQL Database Managed Instance に移行する方法も説明します。

この例で使用される SmartHotel360 アプリは、オープン ソースとして提供されています。 独自のテスト目的に沿って使用する場合は、[GitHub](https://github.com/Microsoft/SmartHotel360) からダウンロードできます。

## <a name="business-drivers"></a>ビジネス ドライバー

Contoso の IT リーダーシップ チームは、自社のビジネス パートナーと密接に連絡を取り合い、ビジネス部門がこの移行で何を達成しようとしているのかを理解しました。

- **ビジネスの成長への対応。** Contoso は成長しています。 その結果、会社のオンプレミスのシステムとインフラストラクチャにかかる圧力が増加しています。
- **効率化。** Contoso は不要な手順を取り除き、開発者とユーザーのプロセスを効率化する必要があります。 顧客の要求にすばやく対応できるように、ビジネス部門は IT 部門に対して、時間やコストを無駄にせず、迅速に作業を行うことを求めています。
- **機敏性の向上。** Contoso IT は、ビジネス部門の要求に対して、対応力を向上させる必要があります。 会社がグローバル経済で成功を収めるために、市場で起きる変化に遅れることなく迅速に対応する必要があります。 Contoso の IT 部門がビジネスの妨げになったり、ビジネスの妨げになったりするようなことがあってはなりません。
- **スケール。** 会社のビジネスが順調に成長している中で、Contoso IT 部門は、同じペースで拡張できるシステムを提供する必要があります。

## <a name="migration-goals"></a>移行の目標

Contoso クラウド チームは、この移行の目標を確認しました。 会社では、移行の目標を使用して、最適な移行方法を決定します。

- 移行しても、Azure のアプリは、Contoso のオンプレミス VMware 環境で現在提供されているのと同じパフォーマンスを発揮できる必要があります。 クラウドに移行したからといって、アプリのパフォーマンスの重要性が低下するわけではありません。
- Contoso はアプリへの投資を望んでいません。 このアプリはビジネスに欠くことのできない重要なものですが、Contoso は現在の形式のままクラウドに移行したいと考えています。
- アプリ移行後のデータベース管理のタスクを最小限に抑える必要があります。
- Contoso は、このアプリに Azure SQL Database を使用したくないと考えています。 Contoso は別のデータベースを探しています。

## <a name="solution-design"></a>ソリューション設計

Contoso は目標と要件を決定した後、デプロイ ソリューションを設計およびレビューし、移行に使用する Azure サービスを含め、移行プロセスを決めます。

### <a name="current-architecture"></a>現在のアーキテクチャ

- Contoso にはメイン データセンターが 1 つあります (**contoso-datacenter**)。 そのデータセンターは、米国東部のニューヨーク市に位置しています。
- Contoso は、その他にも米国内全体で 3 つの地方支店があります。
- メイン データセンターは、光ファイバーによるメトロ イーサネット接続 (500 MBps) を通じて、インターネットに接続されています。
- 各支店は、ビジネス クラスの接続を使用して、インターネットにローカルで接続されています。メイン データセンターには、IPsec VPN トンネルで接続されています。 このセットアップにより、Contoso のネットワーク全体を永続的に接続でき、インターネット接続が最適化されます。
- メイン データセンターは、VMware によって完全に仮想化されています。 Contoso には、vCenter Server 6.5 で管理されている ESXi 6.5 仮想化ホストが 2 つあります。
- Contoso は ID 管理に Active Directory を使用しています。 Contoso は内部ネットワーク上で DNS サーバーを使用しています。
- Contoso には、オンプレミスのドメイン コントローラーがあります (**contosodc1**)。
- ドメイン コントローラーは、VMware VM 上で実行されています。 支店にあるドメイン コントローラーは、物理サーバー上で実行されています。
- SmartHotel360 アプリは、2 つの VM (**WEBVM** と **SQLVM**) に階層化されており、VMware ESXi バージョン 6.5 ホスト (**contosohost1.contoso.com**) 上に配置されています。
- VMware 環境は、VM 上で実行中の vCenter Server 6.5 (**vcenter.contoso.com**) によって管理されています。

![現在の Contoso アーキテクチャ](./media/contoso-migration-rehost-vm-sql-managed-instance/contoso-architecture.png)

### <a name="proposed-architecture"></a>提案されたアーキテクチャ

このシナリオでは、Contoso は次のように 2 層のオンプレミス旅行アプリを移行したいと考えています。

- Azure SQL Database Managed Instance にアプリのデータベース (**SmartHotelDB**) を移行します。
- フロントエンド **WebVM** を Azure VM に移行します。
- Contoso データセンター内のオンプレミス VM は、移行の終了後に使用停止にされます。

![シナリオのアーキテクチャ](./media/contoso-migration-rehost-vm-sql-managed-instance/architecture.png)

### <a name="database-considerations"></a>データベースの考慮事項

Contoso はソリューション設計プロセスの一環として、Azure SQL Database と SQL Server Managed Instance の機能を比較しました。 Managed Instance を使用することを決定するにあたり、次の考慮事項が役に立ちました。

- Managed Instance では、最新のオンプレミス SQL Server バージョンとのほぼ 100% の互換性を提供することが目的とされています。 Microsoft では、SQL Server をオンプレミスまたは IaaS VM で実行していて、最小限の設計変更で完全に管理されたサービスにアプリを移行することを望んでいるお客様には、Managed Instance を推奨しています。
- Contoso は、多数のアプリをオンプレミスから IaaS に移行することを計画しています。 それらの多くは、ISV から提供されたものです。 Contoso は、Managed Instance を使用すれば、サポートされていない可能性がある SQL Database を使用することなく、これらのアプリのデータベース互換性を確保する助けになることを認識しています。
- Contoso は、完全に自動化された Azure Database Migration Service を使用して、Managed Instance へのリフト アンド シフト移行を実行できます。 このサービスを導入すると、Contoso は将来のデータベース移行にそれを再利用できます。
- SQL Managed Instance では、SmartHotel360 アプリの重要なコンポーネントである SQL Server エージェントがサポートされています。 Contoso ではこの互換性が必要です。互換性がないと、アプリで必要なメンテナンス プランを再設計する必要があります。
- ソフトウェア アシュアランスに基づき、Contoso は、SQL Database Managed Instance で SQL Server 用の Azure ハイブリッド特典を利用して、既存のライセンスを割引料金のライセンスに交換することができます。 これによって、Contoso は Managed Instance を最大 30% 節約することができます。
- SQL Managed Instance は仮想ネットワークに完全に含まれるため、Contoso のデータに対して高い分離性とセキュリティが提供されます。 Contoso は、パブリック インターネットから分離された環境を維持しながら、パブリック クラウドのメリットを得ることができます。
- Managed Instance では、Always Encrypted、動的データ マスキング、行レベルのセキュリティ、脅威検出など、多くのセキュリティ機能がサポートされています。

### <a name="solution-review"></a>ソリューションのレビュー

Contoso は、長所と短所の一覧をまとめて、提案されたデザインを評価します。

<!-- markdownlint-disable MD033 -->

| **考慮事項** | **詳細** |
| --- | --- |
| **長所** | WEBVM は変更することなく Azure に移されるため、移行が簡単で済みます。 <br><br> SQL Managed Instance では、Contoso の技術面の要件と目標がサポートされています。 <br><br> Managed Instance では、SQL Server 2008 R2 から移行するときに、現在の展開との 100% の互換性が提供されます。 <br><br> ソフトウェア アシュアランスと、SQL Server および Windows Server の Azure ハイブリッド特典を使用して、投資を活用できます。 <br><br> 将来の移行では、Azure Database Migration Service を再利用できます。 <br><br> SQL Managed Instance には、Contoso が構成する必要のないフォールト トレランスが組み込まれています。 そのため、データ層がフェールオーバーの単一ポイントではなくなります。 |
| **短所** | WEBVM では、Windows Server 2008 R2 が実行されています。 このオペレーティング システムは Azure でサポートされていますが、サポートされるプラットフォームではなくなりました。 [詳細については、こちらを参照してください](https://support.microsoft.com/help/956893)。 <br><br> WEBVM だけがサービスを提供しているので、Web 層はまだ単一のフェールオーバー ポイントです。 <br><br> Contoso は、Azure App Service といったマネージド サービスにアプリを移行するのではなく、VM としてアプリ Web 層を引き続きサポートする必要があります。 <br><br> データ層については、Contoso がオペレーティング システムやデータベース サーバーをカスタマイズしたい場合、または SQL Server と共にサードパーティ製アプリを実行したい場合は、Managed Instance は最適なソリューションではない可能性があります。 IaaS VM で SQL Server を実行すると、このような柔軟性が提供されます。 |

<!-- markdownlint-enable MD033 -->

### <a name="migration-process"></a>移行プロセス

Contoso は、次の手順を実行して、SmartHotel360 アプリの Web 層とデータ層を Azure に移行します。

1. Contoso は既に Azure インフラストラクチャを整備済みであるため、このシナリオで使用する少数の特定 Azure コンポーネントを追加するだけでかまいません。
2. データ層を移行するときは、Azure Database Migration Service を使用します。 このサービスは、Contoso データセンターと Azure との間のサイト間 VPN 接続を経由して、オンプレミス SQL Server VM に接続します。 次にこのサービスは、データベースを移行します。
3. Web 層は、Azure Migrate によるリフト アンド シフト移行を使用して移行します。 そのプロセスでは、オンプレミス VMware 環境の準備を整え、レプリケーションを設定して有効にしたうえで、VM を Azure にフェールオーバーすることによって移行する必要があります。

     ![移行のアーキテクチャ](./media/contoso-migration-rehost-vm-sql-managed-instance/migration-architecture.png)

### <a name="azure-services"></a>Azure サービス

| サービス | 説明 | コスト |
| --- | --- | --- |
| [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview) | Azure Database Migration Service を使用すると、複数のデータベース ソースから Azure データ プラットフォームに、ダウンタイムを最小限に抑えながらシームレスに移行できます。 | [サポートされているリージョン](https://docs.microsoft.com/azure/dms/dms-overview#regional-availability)と | [Database Migration Service の価格](https://azure.microsoft.com/pricing/details/database-migration)についてご確認ください。 |
| [Azure SQL Database マネージド インスタンス](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) | Managed Instance は、Azure クラウド内の完全に管理された SQL Server インスタンスを表すマネージド データベース サービスです。 最新バージョンの SQL Server データベース エンジンと同じコードを使用し、最新の機能、パフォーマンスの向上、およびセキュリティ更新プログラムが適用されています。 | Azure で実行されている SQL Database Managed Instance を使用すると、容量に基づく料金がかかります。 [Managed Instance の価格の詳細については、こちらを参照してください](https://azure.microsoft.com/pricing/details/sql-database/managed)。 |
| [Azure Migrate](https://docs.microsoft.com/azure/migrate/migrate-services-overview) | Contoso は、Azure Migrate サービスを使用して | VMware VM を評価します。 Azure Migrate は、マシンの移行適合性を評価します | 。 そのうえで、Azure で実行するための、サイズとコストの見積もりを提供します | Azure。 | 2018 年 5 月の時点で Azure Migrate は無料サービスです。 |

## <a name="prerequisites"></a>前提条件

Contoso と他のユーザーは、次に示すこのシナリオの前提条件を満たす必要があります。

<!-- markdownlint-disable MD033 -->

| 必要条件 | 詳細 |
| --- | --- |
| **Azure サブスクリプション** | このシリーズの最初の記事で評価を行ったときに、サブスクリプションは既に作成しているはずです。 Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/pricing/free-trial)を作成してください。 <br><br> 無料アカウントを作成する場合、サブスクリプションの管理者としてすべてのアクションを実行できます。 <br><br> 既存のサブスクリプションを使用していて、そのサブスクリプションの管理者でない場合は、管理者に依頼して、必要なリソース グループおよびリソースに対する所有者または共同作成者のアクセス許可を割り当ててもらう必要があります。 |
| **Azure インフラストラクチャ** | Contoso は、[移行のための Azure インフラストラクチャ](./contoso-migration-infrastructure.md)についての記事で説明されているように、Azure インフラストラクチャを設定します。 |
| **オンプレミスのサーバー** | オンプレミス vCenter Server は、バージョン 5.5、6.0、または 6.5 を実行している必要があります。 <br><br> バージョン 5.5、6.0、または 6.5 を実行している ESXi ホスト。 <br><br> ESXi ホスト上で実行している 1 つ以上の VMware VM。 |
| **オンプレミスの VM** | Azure での実行が保証されている [Linux マシンを確認します](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)。 |
| **Database Migration Service** | Azure Database Migration Service には、[互換性のあるオンプレミスの VPN デバイス](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices)が必要です。 <br><br> オンプレミスの VPN デバイスを構成できる必要があります。 外部に接続するパブリック IPv4 アドレスが必要です。 このアドレスを NAT デバイスの内側に割り当てることはできません。 <br><br> オンプレミスの SQL Server データベースにアクセスできることを確認します。 <br><br> Windows ファイアウォールは、ソース データベース エンジンにアクセスできる必要があります。 [データベース エンジン アクセス用の Windows Firewall を構成する方法については、こちらを参照してください](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)。 <br><br> データベース マシンの前面にファイアウォールがある場合は、SMB ポート 445 経由でのデータベースおよびファイルへのアクセスを許可する規則を追加します。 <br><br> ソース SQL Server インスタンスおよびターゲット Managed Instance への接続に使用する資格情報は、sysadmin サーバー ロールのメンバーである必要があります。 <br><br> Azure Database Migration Service がソース データベースをバックアップするために使用できるネットワーク共有が、オンプレミスのデータベースに存在する必要があります。 <br><br> ソース SQL Server インスタンスを実行しているサービス アカウントに、ネットワーク共有に対する書き込みアクセス許可があることを確認します。 <br><br> ネットワーク共有に対するフル コントロールのアクセス許可を持つ、Windows ユーザーとパスワードをメモしておきます。 Azure Database Migration Service は、これらのユーザーの資格情報を偽装して、Azure Storage コンテナーにバックアップ ファイルをアップロードします。 <br><br> SQL Server Express のインストール プロセスでは、TCP/IP プロトコルが既定で**無効化**されます。 これが有効になっていることを確認します。 |

<!-- markdownlint-enable MD033 -->

## <a name="scenario-steps"></a>シナリオのステップ

Contoso が予定しているデプロイ設定方法は次のとおりです。

> [!div class="checklist"]
>
> - **ステップ 1:SQL Database Managed Instance を準備する。** Contoso では、オンプレミス SQL Server データベースの移行先となる既存のマネージド インスタンスが必要です。
> - **手順 2:Azure Database Migration Service を準備する。** データベース移行プロバイダーを登録し、インスタンスを作成した後に、Azure Database Migration Service プロジェクトを作成する必要があります。 また、Azure Database Migration Service 用に Shared Access Signature (SAS) の Uniform Resource Identifier (URI) も設定する必要があります。 SAS URI によって Contoso のストレージ アカウント内のリソースへの委任アクセスが可能になり、Contoso はストレージ オブジェクトへの制限付きアクセス許可を付与できます。 SQL Server バックアップ ファイルのアップロード先であるストレージ アカウント コンテナーに Azure Database Migration Service がアクセスできるように、SAS URI を設定します。
> - **ステップ 3:Azure Migrate: Server Migration 用の Azure を準備する。** Azure Migrate プロジェクトに Server Migration ツールを追加します。
> - **手順 4:Azure Migrate: Server Migration 用のオンプレミス VMware を準備する。** VM 検出用のアカウントを準備し、移行後に Azure VM に接続するための準備をします。
> - **手順 5:VM をレプリケートする。** レプリケーションを設定し、Azure Storage への VM のレプリケーションを開始します。
> - **手順 6:Azure Database Migration Service を使用してデータベースを移行する。** Contoso は、データベースを移行します。
> - **手順 7:Azure Migrate: Server Migration で VM を移行する。** テスト移行を実行してすべてが機能していることを確認してから、完全移行を実行して VM を Azure に移動します。

## <a name="step-1-prepare-a-sql-database-managed-instance"></a>手順 1:SQL Database Managed Instance を準備する

Azure SQL Database Managed Instance を設定するため、Contoso には次の要件を満たしているサブネットが必要です。

- サブネットは専用でなければなりません。 また、サブネットは空で他のクラウド サービスを含んでいない必要があります。 サブネットはゲートウェイ サブネットであってはなりません。
- Managed Instance を作成した後は、サブネットにリソースを追加することはできません。
- サブネットにネットワーク セキュリティ グループを関連付けることはできません。
- サブネットにはユーザー定義のルート テーブルが必要です。 割り当てられている唯一のルートが 0.0.0.0/0 の次ホップ インターネットである必要があります。
- 仮想ネットワーク用に省略可能なカスタム DNS が指定されている場合、Azure 内の再帰的なリゾルバーの仮想 IP アドレス `168.63.129.16` をリストに追加する必要があります。 [Azure SQL Database Managed Instance のカスタム DNS を構成する](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-custom-dns)方法についてご確認ください。
- サブネットにサービス エンドポイント (ストレージまたは SQL) を関連付けることはできません。 仮想ネットワークではサービス エンドポイントを無効にする必要があります。
- サブネットには 16 個以上の IP アドレスが必要です。 [Managed Instance サブネットのサイズを指定する方法については、こちらを参照してください](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-vnet-configuration)。
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

- [SQL Database Managed Instance の概要](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance)を確認してください。
- [SQL Database Managed Instance 用の仮想ネットワークを作成する方法については、こちらを参照してください](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-vnet-configuration)。
- [ピアリングを設定する方法については、こちらを参照してください](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering)。
- [Azure Active Directory DNS 設定を更新する方法については、こちらを参照してください](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance)。

### <a name="set-up-routing"></a>ルーティングを設定する

Managed Instance は、プライベート仮想ネットワーク内に配置されます。 仮想ネットワークが Azure Management Service と通信するためのルート テーブルが必要です。 仮想ネットワークが、仮想ネットワークを管理するサービスと通信できない場合、仮想ネットワークへのアクセスができなくなります。

Contoso は以下の点を考慮します。

- ルート テーブルには、Managed Instance から送信されたパケットを仮想ネットワーク内でルーティングする方法を指定した一連のルール (ルート) が含まれています。
- ルート テーブルは、Managed Instance がデプロイされているサブネットに関連付けられています。 サブネットから離れる各パケットは、関連付けられたルート テーブルに基づいて処理されます。
- 1 つのサブネットは、1 つのルート テーブルにのみ関連付けることができます。
- Microsoft Azure では、ルート テーブルの作成に追加料金はかかりません。

 Contoso の管理者は、次のようにしてルーティングを設定します。

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

## <a name="step-2-prepare-the-azure-database-migration-service"></a>手順 2:Azure Database Migration Service を準備する

Azure Database Migration Service を準備するために、Contoso の管理者は、いくつかの作業を行う必要があります。

- Azure 内で Azure Database Migration Service プロバイダーを登録します。
- Azure Database Migration Service に Azure Storage へのアクセス権を付与し、データベースの移行に使用するバックアップ ファイルをアップロードできるようにします。 Azure Storage へのアクセス権を付与するために、Azure Blob Storage コンテナーを作成します。 Blob Storage コンテナー用の SAS URI を生成します。
- Azure Database Migration Service プロジェクトを作成します。

その後、次の手順を行います。

1. サブスクリプションにデータベース移行プロバイダーを登録します。
    ![Database Migration Service - 登録](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-subscription.png)

2. Blob Storage コンテナーを作成します。 SAS URI を生成し、Azure Database Migration Service がその URI にアクセスできるようにします。

    ![Database Migration Service - SAS URI の生成](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-sas.png)

3. Azure Database Migration Service インスタンスを作成します。

    ![Database Migration Service - インスタンスの作成](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-instance.png)

4. Azure Database Migration Service インスタンスを **VNET-PROD-DC-EUS2** 仮想ネットワークの **PROD-DC-EUS2** サブネット内に配置します。
    - Azure Database Migration Service をそこに配置する理由は、このサービスが、VPN ゲートウェイ経由でオンプレミスの SQL Server VM にアクセスできる仮想ネットワーク内にあることが必要なためです。
    - **VNET-PROD-EUS2** は **VNET-HUB-EUS2** とピアリングされ、リモート ゲートウェイの使用を許可されます。 **[リモート ゲートウェイを使用する]** オプションにより、Azure Database Migration Service は必要に応じて通信ができるようになります。

        ![Database Migration Service - ネットワークの構成](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-network.png)

**さらにサポートが必要な場合**

- [Azure Database Migration Service を設定する方法については、こちらを参照してください](https://docs.microsoft.com/azure/dms/quickstart-create-data-migration-service-portal)。
- [SAS を作成して使用する方法については、こちらを参照してください](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2)。

## <a name="step-3-prepare-azure-for-the-azure-migrate-server-migration-tool"></a>手順 3:Azure Migrate: Server Migration ツール用の Azure を準備するServer Migration ツール

Contoso が VM を Azure に移行するには、以下の Azure コンポーネントが必要です。

- 移行中に作成される Azure VM が配置される VNet。
- Azure Migrate: Server Migration ツール (プロビジョニング済みのもの)。

これらを次のように設定します。

1. **ネットワークを設定する:** Contoso は、[Azure インフラストラクチャのデプロイ](./contoso-migration-infrastructure.md)を行ったときに、Azure Migrate: Server Migration に使用できるネットワークを既にセットアップしています

    - SmartHotel360 アプリは運用アプリであり、VM はプライマリ リージョンである米国東部 2 の Azure 運用ネットワーク (VNET-PROD-EUS2) に移行されます。
    - 両方の VM は、運用リソースのために使用される ContosoRG リソース グループに配置されます。
    - アプリのフロントエンド VM (WEBVM) は、運用ネットワーク内のフロントエンド サブネット (PROD-FE-EUS2) に移行されます。
    - アプリのデータベース VM (SQLVM) は、運用ネットワーク内のデータベース サブネット (PROD-DB-EUS2) に移行されます。

## <a name="step-4-prepare-on-premises-vmware-for-azure-migrate-server-migration"></a>手順 4:Azure Migrate: Server Migration 用のオンプレミス VMware を準備するServer Migration

Contoso が VM を Azure に移行するには、以下の Azure コンポーネントが必要です。

- 移行中に作成される Azure VM が配置される VNet。
- Azure Migrate: Server Migration ツール (OVA) (プロビジョニングおよび構成済みのもの)。

これらを次のように設定します。

1. ネットワークを設定します - Contoso は、[Azure インフラストラクチャのデプロイ](./contoso-migration-infrastructure.md)を行ったときに、Azure Migrate: Server Migration に使用できるネットワークを既にセットアップしています

    - SmartHotel360 アプリは運用アプリであり、VM はプライマリ リージョンである米国東部 2 の Azure 運用ネットワーク (VNET-PROD-EUS2) に移行されます。
    - 両方の VM は、運用リソースのために使用される ContosoRG リソース グループに配置されます。
    - アプリのフロントエンド VM (WEBVM) は、運用ネットワーク内のフロントエンド サブネット (PROD-FE-EUS2) に移行されます。
    - アプリのデータベース VM (SQLVM) は、運用ネットワーク内のデータベース サブネット (PROD-DB-EUS2) に移行されます。

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

      - 検出用の Linux または Windows ベースの資格情報をすべて追加します。

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

2. サイト間 VPN でアクセスする場合:

    - 移行前に、オンプレミス VM で RDP または SSH を有効にします。
    - オペレーティング システムのファイアウォールで RDP または SSH が許可されていることを確認します。
    - Windows の場合、オンプレミス VM 上のオペレーティング システムの SAN ポリシーを **OnlineAll** に設定します。

3. Azure エージェントをインストールします。

    - [Azure Linux エージェント](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux)
    - [Azure Windows エージェント](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows)

4. その他

   - Windows の場合、移行をトリガーするときに、VM 上に保留中の Windows 更新プログラムがあってはいけません。 ある場合は、更新が完了するまで、VM にログインすることはできません。
   - 移行後、**ブート診断**を調べて、VM のスクリーンショットを確認できます。 これが機能しない場合は、VM が実行中であることを確認し、こちらの[トラブルシューティングのヒント](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)を参照してください。

5. 他に支援が必要でしょうか。

   - [移行用の VM の準備](https://docs.microsoft.com/azure/migrate/contoso-migration-rehost-vm#prepare-vms-for-migration)についてご確認ください。

## <a name="step-5-replicate-the-on-premises-vms"></a>手順 5:オンプレミスの VM をレプリケートする

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

7. **[Azure ハイブリッド特典]** で、次のように選択します。

    - Azure ハイブリッド特典を適用しない場合は、 **[いいえ]** を選択します。 続けて、 **[次へ]** をクリックします。
    - アクティブなソフトウェア アシュアランスまたは Windows Server のサブスクリプションの対象となっている Windows Server マシンがあり、移行中のマシンにその特典を適用する場合は、 **[はい]** を選択します。 続けて、 **[次へ]** をクリックします。

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

## <a name="step-6-migrate-the-database-using-the-azure-database-migration-service"></a>手順 6:Azure Database Migration Service を使用してデータベースを移行する

Contoso の管理者は、Azure Database Migration Service プロジェクトを作成し、その後にデータベースを移行する必要があります。

### <a name="create-an-azure-database-migration-service-project"></a>Azure Database Migration Service プロジェクトを作成する

1. Azure Database Migration Service プロジェクトを作成します。 ソース サーバーのタイプとして **[SQL Server]** 、ターゲットとして **[Azure SQL Database Managed Instance]** を選択します。

     ![Database Migration Service - 新しい移行プロジェクト](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-project.png)

2. 移行ウィザードが開きます。

### <a name="migrate-the-database"></a>データベースを移行する

1. 移行ウィザードで、オンプレミス データベースが配置されるソース VM を指定します。 データベースにアクセスするための資格情報を入力します。

    ![Database Migration Service - ソースの詳細](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-wizard-source.png)

2. 移行するデータベース (**SmartHotel.Registration**) を選択します。

    ![Database Migration Service - ソース データベースの選択](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-wizard-source-db.png)

3. ターゲットとして、Azure の Managed Instance の名前とアクセス資格情報を入力します。

    ![Database Migration Service - ターゲットの詳細](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-target-details.png)

4. **[新しいアクティビティ]**  >  **[移行の実行]** で、移行を実行する際の設定を指定します。
    - ソースおよびターゲットの資格情報。
    - 移行する対象のデータベース。
    - オンプレミス VM 上で作成したネットワーク共有。 Azure Database Migration Service は、この共有にソースのバックアップを取得します。
        - ソースの SQL Server インスタンスを実行するサービス アカウントには、この共有に対する書き込み権限が必要です。
        - 共有の FQDN パスを使用する必要があります。
    - ストレージ アカウント コンテナーへのアクセス権を Azure Database Migration Service に付与する SAS URI。このコンテナーに、サービスによって移行用バックアップ ファイルがアップロードされます。

        ![Database Migration Service - 移行の設定の構成](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-migration-settings.png)

5. 移行の設定を保存し、移行を実行します。
6. **[Overview]\(概要\)** で、移行のステータスを監視します。

    ![Database Migration Service - 監視](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-monitor1.png)

7. 移行が完了したら、Managed Instance 上にターゲット データベースが存在することを確認します。

    ![Database Migration Service - データベース移行の確認](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-monitor2.png)

## <a name="step-7-migrate-the-vms-with-azure-migrate-server-migration"></a>手順 7:Azure Migrate: Server Migration で VM を移行するServer Migration

Contoso の管理者は、簡単なテスト移行を実行して VM が正常に動作していることを確認してから、VM を移行します。

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

### <a name="migrate-the-vms"></a>VM を移行する

Contoso の管理者は、ここで、移動を完了する完全移行を実行します。

1. Azure Migrate プロジェクトの **[サーバー]**  >  **[Azure Migrate: Server Migration]** で、 **[サーバーをレプリケートしています]** をクリックします。

    ![サーバーをレプリケートしています](./media/contoso-migration-rehost-linux-vm/replicating-servers.png)

2. **[マシンのレプリケート]** で VM を右クリックし、 **[移行]** を選択します。
3. **[移行]**  >  **[仮想マシンをシャットダウンし、データ損失のない計画された移行を実行しますか]** で、 **[はい]**  >  **[OK]** の順に選択します。
    - 既定では、Azure Migrate によってオンプレミスの VM がシャットダウンされ、前回のレプリケーションが発生した後に発生したすべての VM の変更を同期するためにオンデマンド レプリケーションが実行されます。 こうすることで、データ損失がなくなります。
    - VM をシャットダウンしたくない場合は、 **[いいえ]** を選択します。
4. VM に対して移行ジョブが開始されます。 Azure 通知でジョブを追跡します。
5. ジョブが完了したら、 **[仮想マシン]** ページで VM を表示して管理できます。
6. 最後に、Contoso ドメイン コント ローラーのいずれかで、**WEBVM** の DNS レコードを更新します。

### <a name="update-the-connection-string"></a>接続文字列を更新する

移行プロセスの最終ステップとして、アプリケーションの接続文字列を更新して、Contoso の Managed Instance で実行されている移行されたデータベースを指すようにします。

1. Azure portal で **[設定]**  >  **[接続文字列]** を選択して、接続文字列を探します。

    ![Connection strings](./media/contoso-migration-rehost-vm-sql-managed-instance/failover4.png)

2. SQL Database Managed Instance のユーザー名とパスワードを使用して文字列を更新します。
3. 文字列を構成した後は、アプリケーションの web.config ファイルに含まれる現在の接続文字列を置き換えます。
4. ファイルを更新して保存した後、コマンド プロンプト ウィンドウで `iisreset /restart` を実行して、WEBVM 上で IIS を再起動します。
5. IIS が再起動した後は、SQL Database Managed Instance 上で実行されているデータベースがアプリで使用されます。
6. この時点で、オンプレミスの SQLVM マシンをシャットダウンできます。 移行が完了しました。

**さらにサポートが必要な場合**

- [テスト フェールオーバーを実行する方法については、こちらを参照してください](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure)。
- [復旧計画を作成する方法については、こちらを参照してください](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans)。
- [Azure にフェールオーバーする方法については、こちらを参照してください](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover)。

## <a name="clean-up-after-migration"></a>移行後にクリーンアップする

移行が完了しました。SmartHotel360 アプリは Azure VM 上で実行されており、SmartHotel360 データベースは Azure SQL Database Managed Instance 上で使用可能です。

ここで、以下のクリーンアップ タスクを行う必要があります。

- WEBVM マシンを vCenter Server インベントリから削除します。
- SQLVM マシンを vCenter Server インベントリから削除します。
- WEBVM と SQLVM をローカル バックアップ ジョブから削除します。
- WEBVM の新しい場所と IP アドレスを示すように内部ドキュメントを更新します。
- SQLVM を内部ドキュメントから削除します。 代わりに、SQLVM が削除され、VM インベントリに残っていないことを示すようにドキュメントを変更できます。
- 使用停止になった VM とやりとりするリソースがあれば確認します。 すべての関連する設定またはドキュメントを更新して新しい構成を反映します。

## <a name="review-the-deployment"></a>デプロイを再調査する

リソースを Azure に移行したら、新しいインフラストラクチャを完全に操作可能にして、セキュリティで保護する必要があります。

### <a name="security"></a>Security

Contoso セキュリティ チームは、Azure VM と SQL Database Managed Instance を調査し、その実装に関して、セキュリティ上の問題がないかを確認します。

- チームは、VM のアクセスを制御するために使用されるネットワーク セキュリティ グループを確認します。 ネットワーク セキュリティ グループにより、アプリに対して許可されたトラフィックのみが通過できるようにすることができます。
- Contoso のセキュリティ チームは、ディスク上のデータ保護のために、Azure Disk Encryption と Azure Key Vault の使用も検討します。
- チームは、Managed Instance 上での脅威の検出を有効にします。 脅威が検出された場合は、セキュリティ チームとサービス デスク システムにアラートを送信して、チケットを作成します。 [Managed Instance を対象にした脅威の検出の詳細については、こちらを参照してください](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-threat-detection)。

     ![Managed Instance のセキュリティ - 脅威の検出](./media/contoso-migration-rehost-vm-sql-managed-instance/mi-security.png)

VM のセキュリティに関する作業の詳細については、「[Azure における IaaS ワークロードのセキュリティに関するベスト プラクティス](https://docs.microsoft.com/azure/security/fundamentals/iaas)」を参照してください。

### <a name="bcdr"></a>BCDR

事業継続とディザスター リカバリー (BCDR) のために、Contoso は次のアクションを実施します。

- データの安全性を確保する: Contoso は、Azure Backup サービスを使用して VM 上のデータをバックアップします。 [詳細については、こちらを参照してください](https://docs.microsoft.com/azure/backup/backup-overview)。
- アプリの稼働状態を維持する: Contoso は、Site Recovery を使用して、Azure 内のアプリの VM をセカンダリ リージョンにレプリケートします。 [詳細については、こちらを参照してください](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-quickstart)。
- Contoso は、[データベースのバックアップ](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups)など、SQL Managed Instance の管理についてさらに学習します。

### <a name="licensing-and-cost-optimization"></a>ライセンスとコストの最適化

- Contoso は、WEBVM の既存のライセンスを所有しています。 Azure ハイブリッド特典の価格を利用するために、Contoso は既存の Azure VM を変換します。
- Contoso は [Azure Cost Management](https://azure.microsoft.com/services/cost-management) を使用して、IT リーダーが定めた予算内に確実に収まるようにします。

## <a name="conclusion"></a>まとめ

この記事では、Contoso が Azure Migrate サービスを使用して、アプリのフロントエンド VM を Azure に移行することで、Azure 内で SmartHotel360 アプリを再ホストしました。 オンプレミス データベースの Azure SQL Database Managed Instance への移行には、Azure Database Migration Service を使用しました。
