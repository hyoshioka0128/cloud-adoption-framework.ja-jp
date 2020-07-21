---
title: オンプレミス アプリケーションを Azure VM および Azure SQL Managed Instance に移行してリホストする
description: Contoso がオンプレミス アプリを Azure VM で Azure SQL Managed Instance を使用してリホストする方法を説明します。
author: givenscj
ms.author: abuck
ms.date: 07/01/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: migrate
services: azure-migrate
ms.openlocfilehash: f0b1bb88a1cabe094b4cd8e76e17feec11876ed5
ms.sourcegitcommit: 84d7bfd11329eb4c151c4c32be5bab6c91f376ed
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2020
ms.locfileid: "86234601"
---
<!-- cSpell:ignore givenscj WEBVM SQLVM OSTICKETWEB OSTICKETMYSQL contosohost vcenter contosodc NSGs agentless SQLMI iisreset -->

# <a name="rehost-an-on-premises-application-by-migrating-to-azure-vms-and-azure-sql-managed-instance"></a>オンプレミス アプリケーションを Azure VM および Azure SQL Managed Instance に移行してリホストする

この記事では、Contoso という架空の会社が、Azure Migrate サービスを使用して、VMware VM 上で実行される 2 階層の Windows .NET フロントエンド アプリケーションを Azure VM に移行する方法を説明します。 また、Contoso がアプリケーション データベースを Azure SQL Managed Instance に移行する方法についても説明します。

この例で使用される SmartHotel360 アプリケーションは、オープン ソースとして提供されています。 独自のテスト目的に沿って使用する場合は、[GitHub](https://github.com/Microsoft/SmartHotel360) からダウンロードできます。

## <a name="business-drivers"></a>ビジネス ドライバー

Contoso の IT リーダーシップ チームは、自社のビジネス パートナーと密接に連絡を取り合い、ビジネス部門がこの移行で何を達成しようとしているのかを理解しました。

- **ビジネスの成長への対応。** Contoso は成長しています。 その結果、会社のオンプレミスのシステムとインフラストラクチャにかかる圧力が増加しています。
- **効率化。** Contoso は不要な手順を取り除き、開発者とユーザーのプロセスを効率化する必要があります。 顧客の要求にすばやく対応できるように、ビジネス部門は IT 部門に対して、時間やコストを無駄にせず、迅速に作業を行うことを求めています。
- **機敏性の向上。** Contoso IT は、ビジネス部門の要求に対して、対応力を向上させる必要があります。 会社がグローバル経済で成功を収めるために、市場で起きる変化に遅れることなく迅速に対応する必要があります。 Contoso の IT 部門がビジネスの妨げになったり、ビジネスの妨げになったりするようなことがあってはなりません。
- **スケール。** 会社のビジネスが順調に成長している中で、Contoso IT 部門は、同じペースで拡張できるシステムを提供する必要があります。

## <a name="migration-goals"></a>移行の目標

Contoso クラウド チームは、この移行の目標を確認しました。 会社では、移行の目標を使用して、最適な移行方法を決定します。

- 移行しても、Azure のアプリケーションは、Contoso のオンプレミス VMware 環境で現在提供されているアプリケーションと同じパフォーマンスを発揮できる必要があります。 クラウドに移行するのは、そのアプリケーションのパフォーマンスがそれほど重要でないからではありません。
- Contoso はアプリケーションへの投資を望んでいません。 このアプリケーションはビジネスに欠くことのできない重要なものですが、Contoso は現在の形式のままクラウドに移行したいと考えています。
- アプリケーション移行後のデータベース管理のタスクを最小限に抑える必要があります。
- Contoso は、このアプリケーションに Azure SQL Database を使用したくないと考えています。 Contoso は別のデータベースを探しています。

## <a name="solution-design"></a>ソリューション設計

Contoso は目標と要件を決定した後、デプロイ ソリューションを設計およびレビューし、移行に使用する Azure サービスを含め、移行プロセスを決めます。

### <a name="current-architecture"></a>現在のアーキテクチャ

- Contoso にはメイン データセンターが 1 つあります (`contoso-datacenter`)。 そのデータセンターは、米国東部のニューヨーク市に位置しています。
- Contoso は、その他にも米国内全体で 3 つの地方支店があります。
- メイン データセンターは、光ファイバーによるメトロ イーサネット接続 (500 Mbps) を通じて、インターネットに接続されています。
- 各支店は、ビジネス クラスの接続を使用して、インターネットにローカルで接続されています。メイン データセンターには、IPsec VPN トンネルで接続されています。 このセットアップにより、Contoso のネットワーク全体を永続的に接続でき、インターネット接続が最適化されます。
- メイン データセンターは、VMware によって完全に仮想化されています。 Contoso には、vCenter Server 6.5 で管理されている ESXi 6.5 仮想化ホストが 2 つあります。
- Contoso は ID 管理に Active Directory を使用しています。 Contoso は内部ネットワーク上で DNS サーバーを使用しています。
- Contoso には、オンプレミスのドメイン コントローラーがあります (`contosodc1`)。
- ドメイン コントローラーは、VMware VM 上で実行されています。 支店にあるドメイン コントローラーは、物理サーバー上で実行されています。
- SmartHotel360 アプリケーションは、2 つの VM (`WEBVM` と `SQLVM`) に階層化されており、VMware ESXi バージョン 6.5 ホスト (`contosohost1.contoso.com`) 上に配置されています。
- VMware 環境は、VM 上で実行中の vCenter Server 6.5 (`vcenter.contoso.com`) によって管理されています。

![現在の Contoso アーキテクチャ](./media/contoso-migration-rehost-vm-sql-managed-instance/contoso-architecture.png)

### <a name="proposed-architecture"></a>提案されたアーキテクチャ

このシナリオでは、Contoso は次のように 2 層のオンプレミス旅行アプリケーションを移行したいと考えています。

- Azure SQL Managed Instance にアプリケーションのデータベース (`SmartHotelDB`) を移行します。
- フロントエンド `WEBVM` を Azure VM に移行します。
- Contoso データセンター内のオンプレミス VM は、移行の終了後に使用停止にされます。

![シナリオのアーキテクチャ](./media/contoso-migration-rehost-vm-sql-managed-instance/architecture.png)

### <a name="database-considerations"></a>データベースの考慮事項

Contoso はソリューション設計プロセスの一環として、Azure SQL Database と SQL Managed Instance の機能を比較しました。 SQL Managed Instance を使用することを決定するにあたり、次の考慮事項が役に立ちました。

- SQL Managed Instance では、最新のオンプレミス SQL Server バージョンとのほぼ 100% の互換性を提供することが目的とされています。 Microsoft では、SQL Server をオンプレミスまたは IaaS VM で実行していて、最小限の設計変更で完全に管理されたサービスにアプリケーションを移行することを望んでいるお客様には、SQL Managed Instance を推奨しています。
- Contoso は、多数のアプリケーションをオンプレミスから IaaS に移行することを計画しています。 それらの多くは、ISV から提供されたものです。 Contoso は、SQL Managed Instance を使用すれば、サポートされていない可能性がある SQL Database を使用することなく、これらのアプリケーションのデータベース互換性を確保する助けになることを認識しています。
- Contoso は、完全に自動化された Azure Database Migration Service を使用して、SQL Managed Instance へのリフト アンド シフト移行を実行できます。 このサービスを導入すると、Contoso は将来のデータベース移行にそれを再利用できます。
- SQL Managed Instance では、SmartHotel360 アプリケーションの重要なコンポーネントである SQL Server エージェントがサポートされています。 Contoso ではこの互換性が必要です。互換性がないと、アプリケーションで必要なメンテナンス プランを再設計する必要があります。
- ソフトウェア アシュアランスを利用すると、Contoso は、SQL Server の Azure ハイブリッド特典を使用して、SQL Managed Instance で既存のライセンスを割引料金に換えることができます。 これによって、Contoso は SQL Managed Instance を最大 30% 節約することができます。
- SQL Managed Instance は仮想ネットワークに完全に含まれるため、Contoso のデータに対して高い分離性とセキュリティが提供されます。 Contoso は、パブリック インターネットから分離された環境を維持しながら、パブリック クラウドのメリットを得ることができます。
- SQL Managed Instance では、Always Encrypted、動的データ マスキング、行レベルのセキュリティ、脅威検出など、多くのセキュリティ機能がサポートされています。

### <a name="solution-review"></a>ソリューションのレビュー

Contoso は、長所と短所の一覧をまとめて、提案されたデザインを評価します。

| 考慮事項 | 詳細 |
| --- | --- |
| **長所** | `WEBVM` は変更することなく Azure に移されるため、移行が簡単で済みます。 <br><br> SQL Managed Instance では、Contoso の技術面の要件と目標がサポートされています。 <br><br> SQL Managed Instance では、SQL Server 2008 R2 から移行するときに、現在の展開との 100% の互換性が提供されます。 <br><br> ソフトウェア アシュアランスと、SQL Server および Windows Server の Azure ハイブリッド特典を使用して、投資を活用できます。 <br><br> 将来の移行で、Azure Database Migration Service を再利用できます。 <br><br> SQL Managed Instance には、Contoso が構成する必要のないフォールト トレランスが組み込まれています。 そのため、データ層がフェールオーバーの単一ポイントではなくなります。 |
| **短所** | `WEBVM` では、Windows Server 2008 R2 が実行されています。 このオペレーティング システムは Azure でサポートされていますが、サポートされるプラットフォームではなくなりました。 [詳細については、こちらを参照してください](https://support.microsoft.com/help/956893)。 <br><br> `WEBVM` だけがサービスを提供しているので、Web 層はまだ単一のフェールオーバー ポイントです。 <br><br> Contoso は、Azure App Service などのマネージド サービスに移行するのではなく、アプリケーション Web 層を VM として引き続きサポートする必要があります。 <br><br> データ層について、Contoso がオペレーティング システムまたはデータベース サーバーをカスタマイズしたい場合、または SQL Server と共にサードパーティ製アプリケーションを実行したい場合、SQL Managed Instance は最適なソリューションではない可能性があります。 IaaS VM で SQL Server を実行すると、このような柔軟性が提供されます。 |

### <a name="migration-process"></a>移行プロセス

Contoso は、次の手順を行って、SmartHotel360 アプリケーションの Web およびデータ層を Azure に移行します。

1. Contoso は既に Azure インフラストラクチャを整備済みであるため、このシナリオで使用する少数の特定 Azure コンポーネントを追加するだけでかまいません。
2. データ層を移行するときは、Azure Database Migration Service を使用します。 このサービスは、Contoso データセンターと Azure との間のサイト間 VPN 接続を経由して、オンプレミス SQL Server VM に接続します。 次にこのサービスは、データベースを移行します。
3. Web 層は、Azure Migrate によるリフト アンド シフト移行を使用して移行します。 そのプロセスでは、オンプレミス VMware 環境の準備を整え、レプリケーションを設定して有効にしたうえで、VM を Azure にフェールオーバーすることによって移行する必要があります。

     ![移行のアーキテクチャ](./media/contoso-migration-rehost-vm-sql-managed-instance/migration-architecture.png)

### <a name="azure-services"></a>Azure サービス

| サービス | 説明 | コスト |
| --- | --- | --- |
| [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview) | Azure Database Migration Service を使用すると、複数のデータベース ソースから Azure データ プラットフォームに、ダウンタイムを最小限に抑えながらシームレスに移行できます。 | [サポートされているリージョン](https://docs.microsoft.com/azure/dms/dms-overview#regional-availability)と | [Azure Database Migration Service の価格](https://azure.microsoft.com/pricing/details/database-migration) |
| [Azure SQL Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) | SQL Managed Instance は、Azure クラウド内のフル マネージド SQL Server インスタンスを表すマネージド データベース サービスです。 最新バージョンの SQL Server データベース エンジンと同じコードを使用し、最新の機能、パフォーマンスの向上、およびセキュリティ更新プログラムが適用されています。 | Azure で実行されている SQL Managed Instance を使用すると、容量に基づく料金がかかります。 詳細については、[SQL Managed Instance の価格](https://azure.microsoft.com/pricing/details/sql-database/managed)に関するページを参照してください。 |
| [Azure Migrate](https://docs.microsoft.com/azure/migrate/migrate-services-overview) | Contoso は、Azure Migrate サービスを使用して VMware VM を評価します。 Azure Migrate は、マシンの移行適合性を評価します。 そのうえで、Azure で実行するための、サイズとコストの見積もりを提供します。 | Azure Migrate は、追加料金なしで利用できます。 ただし、評価と移行に使用することにするツール (ファースト パーティまたは ISV) によっては料金が発生する場合があります。 [Azure Migrate の価格](https://azure.microsoft.com/pricing/details/azure-migrate)について、詳しくはこちらを参照してください。 |

## <a name="prerequisites"></a>前提条件

Contoso と他のユーザーは、次に示すこのシナリオの前提条件を満たす必要があります。

| 必要条件 | 詳細 |
| --- | --- |
| **Azure サブスクリプション** | このシリーズの最初の記事で評価を行ったときに、サブスクリプションは既に作成しているはずです。 Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/free)を作成してください。 <br><br> 無料アカウントを作成する場合、サブスクリプションの管理者としてすべてのアクションを実行できます。 <br><br> 既存のサブスクリプションを使用していて、そのサブスクリプションの管理者でない場合は、管理者に依頼して、必要なリソース グループおよびリソースに対する所有者または共同作成者のアクセス許可を割り当ててもらう必要があります。 |
| **Azure インフラストラクチャ** | Contoso は、[移行のための Azure インフラストラクチャ](./contoso-migration-infrastructure.md)についての記事で説明されているように、Azure インフラストラクチャを設定します。 |
| **オンプレミスのサーバー** | オンプレミス vCenter Server では、バージョン 5.5、6.0、または 6.5 を実行している必要があります。 <br><br> バージョン 5.5、6.0、または 6.5 を実行している ESXi ホスト。 <br><br> ESXi ホスト上で実行している 1 つ以上の VMware VM。 |
| **オンプレミスの VM** | Azure での実行が保証されている [Linux マシンを確認します](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)。 |
| **Database Migration Service** | Azure Database Migration Service には、[互換性のあるオンプレミスの VPN デバイス](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices)が必要です。 <br><br> オンプレミスの VPN デバイスを構成できる必要があります。 外部に接続するパブリック IPv4 アドレスが必要です。 このアドレスを NAT デバイスの内側に割り当てることはできません。 <br><br> オンプレミスの SQL Server データベースにアクセスできることを確認します。 <br><br> Windows ファイアウォールは、ソース データベース エンジンにアクセスできる必要があります。 [データベース エンジン アクセス用の Windows Firewall を構成する](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)方法を確認してください。 <br><br> データベース マシンの前面にファイアウォールがある場合は、SMB ポート 445 経由でのデータベースおよびファイルへのアクセスを許可する規則を追加します。 <br><br> ソース SQL Server インスタンスへの接続に使用される資格情報と、SQL Managed Instance をターゲットとする資格情報は、sysadmin サーバー ロールのメンバーである必要があります。 <br><br> Azure Database Migration Service がソース データベースをバックアップするために使用できるネットワーク共有が、オンプレミスのデータベースに存在する必要があります。 <br><br> ソース SQL Server インスタンスを実行しているサービス アカウントに、ネットワーク共有に対する書き込みアクセス許可があることを確認します。 <br><br> ネットワーク共有に対するフル コントロールのアクセス許可を持つ、Windows ユーザーとパスワードをメモしておきます。 Azure Database Migration Service では、これらのユーザーの資格情報を偽装して、Azure Storage コンテナーにバックアップ ファイルがアップロードされます。 <br><br> SQL Server Express のインストール プロセスでは、TCP/IP プロトコルが既定で**無効化**されます。 これが有効になっていることを確認します。 |

## <a name="scenario-steps"></a>シナリオのステップ

Contoso が予定しているデプロイ設定方法は次のとおりです。

> [!div class="checklist"]
>
> - **ステップ 1: SQL Managed Instance を準備する。** Contoso では、オンプレミス SQL Server データベースの移行先となる既存のマネージド インスタンスが必要です。
> - **手順 2: Azure Database Migration Service を準備する。** Contoso は、データベース移行プロバイダーを登録し、インスタンスを作成してから、Database Migration Service プロジェクトを作成する必要があります。 Contoso はまた、Database Migration Service インスタンス用に Shared Access Signature (SAS) の Uniform Resource Identifier (URI) も設定する必要があります。 SAS URI によって Contoso のストレージ アカウント内のリソースへの委任アクセスが可能になり、Contoso はストレージ オブジェクトへの制限付きアクセス許可を付与できます。 Contoso は、SQL Server バックアップ ファイルのアップロード先であるストレージ アカウント コンテナーに Azure Database Migration Service がアクセスできるように、SAS URI を設定します。
> - **ステップ 3:Azure Migrate: Server Migration 用の Azure を準備するServer Migration.** Azure Migrate プロジェクトにサーバー移行ツールを追加します。
> - **手順 4:Azure Migrate: Server Migration 用のオンプレミス VMware を準備する。** VM 検出用のアカウントを準備し、移行後に Azure VM に接続するための準備をします。
> - **手順 5:VM をレプリケートする。** レプリケーションを設定し、Azure Storage への VM のレプリケーションを開始します。
> - **手順 6: Azure Database Migration Service を介してデータベースを移行する。** Contoso は、データベースを移行します。
> - **手順 7:Azure Migrate: Server Migration で VM を移行する。** テスト移行を実行してすべてが機能していることを確認してから、完全移行を実行して VM を Azure に移動します。

## <a name="step-1-prepare-a-sql-managed-instance"></a>手順 1: SQL Managed Instance を準備する

Azure SQL Managed Instance を設定するため、Contoso は次の要件を満たすサブネットを必要としています。

- サブネットは専用でなければなりません。 また、サブネットは空で他のクラウド サービスを含んでいない必要があります。 サブネットはゲートウェイ サブネットであってはなりません。
- マネージド インスタンスを作成した後、Contoso は、サブネットにリソースを追加することはできません。
- サブネットにネットワーク セキュリティ グループを関連付けることはできません。
- サブネットにはユーザー定義のルート テーブルが必要です。 割り当てられる唯一のルートが `0.0.0.0/0` の次ホップ インターネットである必要があります。
- 仮想ネットワーク用に省略可能なカスタム DNS が指定されている場合、Azure 内の再帰的なリゾルバーの仮想 IP アドレス `168.63.129.16` をリストに追加する必要があります。 [Azure SQL Managed Instance のカスタム DNS を構成する](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-custom-dns)方法を確認してください。
- サブネットにサービス エンドポイント (ストレージまたは SQL) を関連付けることはできません。 仮想ネットワークではサービス エンドポイントを無効にする必要があります。
- サブネットには 16 個以上の IP アドレスが必要です。 [マネージド インスタンス サブネットのサイズを指定する](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-vnet-configuration)方法を確認してください。
- Contoso のハイブリッド環境では、カスタム DNS 設定が必要です。 Contoso は、自社の 1 つ以上の Azure DNS サーバーを使用するように DNS 設定を構成します。 [DNS のカスタマイズの詳細については、こちらを参照してください](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-custom-dns)。

### <a name="set-up-a-virtual-network-for-the-managed-instance"></a>マネージド インスタンス用の仮想ネットワークを設定する

Contoso の管理者は仮想ネットワークを次のように設定します。

1. プライマリ リージョン (`East US 2`) に新しい仮想ネットワーク (`VNET-SQLMI-EU2`) を作成します。 これにより、仮想ネットワークが `ContosoNetworkingRG` リソース グループに追加されます。
2. `10.235.0.0/24` のアドレス空間を割り当てます。 その範囲がエンタープライズ内の他のどのネットワークとも重複しないことを確認します。
3. ネットワークに以下の 2 つのサブネットを追加します。
    - `SQLMI-DS-EUS2` (`10.235.0.0/25`)。
    - `SQLMI-SAW-EUS2` (`10.235.0.128/29`)。 このサブネットは、マネージド インスタンスにディレクトリを接続するために使用されます。

      ![SQL Managed Instance: Create virtual network](./media/contoso-migration-rehost-vm-sql-managed-instance/mi-vnet.png)

4. 仮想ネットワークとサブネットがデプロイされた後、ネットワークを以下のようにピアリングします。

    - `VNET-SQLMI-EUS2` を `VNET-HUB-EUS2` (`East US 2` でのハブの仮想ネットワーク) とピアリングします。
    - `VNET-SQLMI-EUS2` を `VNET-PROD-EUS2` (運用ネットワーク) とピアリングします。

      ![ネットワーク ピアリング](./media/contoso-migration-rehost-vm-sql-managed-instance/mi-peering.png)

5. Contoso は、カスタム DNS 設定を行います。 DNS は、最初は Contoso の Azure ドメイン コント ローラーを指しています。 Azure DNS はセカンダリです。 Contoso Azure ドメイン コントローラーは、以下のように配置されます。

    - `East US 2` 運用ネットワーク (`VNET-PROD-EUS2`) 内の `PROD-DC-EUS2` サブネットに配置されています。
    - `CONTOSODC3` アドレス: `10.245.42.4`。
    - `CONTOSODC4` アドレス: `10.245.42.5`。
    - Azure DNS リゾルバー: `168.63.129.16`。

      ![ネットワークの DNS サーバー](./media/contoso-migration-rehost-vm-sql-managed-instance/mi-dns.png)

**さらにサポートが必要な場合**

- [SQL Managed Instance の概要については、こちらをお読みください](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance)。
- [SQL Managed Instance 用の仮想ネットワークを作成する方法については、こちらを参照してください](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-vnet-configuration)。
- [ピアリングを設定する方法については、こちらを参照してください](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering)。
- [Azure Active Directory DNS 設定を更新する方法については、こちらを参照してください](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance)。

### <a name="set-up-routing"></a>ルーティングを設定する

マネージド インスタンスは、プライベート仮想ネットワーク内に配置されます。 Contoso は、Azure 管理サービスと通信するために、仮想ネットワークのルート テーブルを必要としています。 仮想ネットワークが、仮想ネットワークを管理するサービスと通信できない場合、仮想ネットワークへのアクセスができなくなります。

Contoso は以下の点を考慮します。

- ルート テーブルには、マネージド インスタンスから送信されるパケットを仮想ネットワーク内でルーティングする方法を指定した一連のルール (ルート) を含めます。
- ルート テーブルは、マネージド インスタンスがデプロイされているサブネットに関連付けられています。 サブネットから離れる各パケットは、関連付けられたルート テーブルに基づいて処理されます。
- 1 つのサブネットは、1 つのルート テーブルにのみ関連付けることができます。
- Microsoft Azure では、ルート テーブルの作成に追加料金はかかりません。

 Contoso の管理者は、次のようにしてルーティングを設定します。

1. ユーザー定義のルート テーブルを `ContosoNetworkingRG` リソース グループに作成します。

    ![ルート テーブル](./media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table.png)

2. SQL Managed Instance の要件に準拠するため、ルート テーブル (`MIRouteTable`) をデプロイした後、アドレス プレフィックスが `0.0.0.0/0` のルートを追加します。 **[次ホップの種類]** オプションは **[インターネット]** に設定します。

    ![ルート テーブルのプレフィックス](./media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table-prefix.png)

3. ルート テーブルを `SQLMI-DB-EUS2` サブネット (`VNET-SQLMI-EUS2` ネットワーク内) に関連付けます。

    ![ルート テーブルのサブネット](./media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table-subnet.png)

**さらにサポートが必要な場合**

[マネージド インスタンス用のルートを設定する方法については、こちらを参照してください](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started)。

### <a name="create-a-managed-instance"></a>マネージド インスタンスを作成する

これで、Contoso の管理者は、SQL Managed Instance をプロビジョニングできます。

1. マネージド インスタンスにより、ビジネス アプリケーションにサービスが提供されるため、会社のプライマリ リージョン (`East US 2`) にマネージド インスタンスをデプロイします。 マネージド インスタンスを `ContosoRG` リソース グループに追加します。
2. 価格レベルを選択し、インスタンスのコンピューティングとストレージのサイズを設定します。 詳細については、[SQL Managed Instance の価格](https://azure.microsoft.com/pricing/details/sql-database/managed)に関するページを参照してください。

    ![マネージド インスタンス](./media/contoso-migration-rehost-vm-sql-managed-instance/mi-create.png)

3. マネージド インスタンスをデプロイすると、`ContosoRG` リソース グループ内に 2 つの新しいリソースが表示されます。

    - 新しい SQL Managed Instance。
    - Contoso に複数のマネージド インスタンスがある場合の仮想クラスター。

      ![マネージド インスタンス](./media/contoso-migration-rehost-vm-sql-managed-instance/mi-resources.png)

**さらにサポートが必要な場合**

[マネージド インスタンスをプロビジョニングする方法については、こちらを参照してください](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started)。

## <a name="step-2-prepare-azure-database-migration-service"></a>手順 2: Azure Database Migration Service を準備する

Azure Database Migration Service を準備するために、Contoso の管理者は、次のいくつかの作業を行う必要があります。

- Azure 内で Database Migration Service プロバイダーを登録します。
- Azure Storage にアクセスするためのアクセス許可を Database Migration Service に付与して、データベースの移行に使用するバックアップ ファイルをアップロードできるようにします。 Azure Storage へのアクセス権を付与するために、Azure Blob Storage コンテナーを作成します。 Blob Storage コンテナー用の SAS URI を生成します。
- Azure Database Migration Service プロジェクトを作成します。

その後、次の手順を行います。

1. サブスクリプションにデータベース移行プロバイダーを登録します。 ![データベース移行サービス: [登録]](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-subscription.png)

2. Blob Storage コンテナーを作成します。 Contoso は、SAS URI を生成し、Azure Database Migration Service がそれにアクセスできるようにします。

    ![データベース移行サービス: SAS URI を生成する](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-sas.png)

3. Azure Database Migration Service インスタンスを作成します。

    ![データベース移行サービス: インスタンスを作成する](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-instance.png)

4. Database Migration Service インスタンスを `VNET-PROD-DC-EUS2` 仮想ネットワークの `PROD-DC-EUS2` サブネットに配置します。
    - ここにそのインスタンスを配置する理由は、VPN ゲートウェイ経由でオンプレミスの SQL Server VM にアクセスできる仮想ネットワーク内に、このサービスが存在する必要があるためです。
    - `VNET-PROD-EUS2` は `VNET-HUB-EUS2` とピアリングされていて、リモート ゲートウェイを使用することが許可されています。 **[リモート ゲートウェイを使用する]** オプションにより、インスタンスが必要に応じて確実に通信ができるようになります。

        ![データベース移行サービス: ネットワークを構成する](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-network.png)

**さらにサポートが必要な場合**

- [Azure Database Migration Service を設定する方法については、こちらを参照してください](https://docs.microsoft.com/azure/dms/quickstart-create-data-migration-service-portal)。
- [SAS を作成して使用する方法については、こちらを参照してください](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2)。

## <a name="step-3-prepare-azure-for-the-azure-migrate-server-migration-tool"></a>手順 3:Azure Migrate: Server Migration ツール用の Azure を準備するServer Migration ツール

Contoso が VM を Azure に移行するには、以下の Azure コンポーネントが必要です。

- 移行中に作成される Azure VM が配置される VNet。
- Azure Migrate: Server Migration ツール (プロビジョニング済みのもの)。

これらを次のように設定します。

1. **ネットワークを設定する:** Contoso は、[Azure インフラストラクチャのデプロイ](./contoso-migration-infrastructure.md)を行ったときに、Azure Migrate: Server Migration に使用できるネットワークを既にセットアップしています

    - SmartHotel360 アプリケーションは運用アプリケーションであり、VM はプライマリ リージョン (`East US 2`) の Azure 運用ネットワーク (`VNET-PROD-EUS2`) に移行されます。
    - 両方の VM は、運用リソースのために使用される `ContosoRG` リソース グループに配置されます。
    - アプリケーションのフロントエンド VM (`WEBVM`) は、運用ネットワークのフロントエンド サブネット (`PROD-FE-EUS2`) に移行されます。
    - アプリケーション データベース VM (`SQLVM`) は、運用ネットワークのデータベース サブネット (`PROD-DB-EUS2`) に移行されます。

## <a name="step-4-prepare-on-premises-vmware-for-azure-migrate-server-migration"></a>手順 4:Azure Migrate: Server Migration 用のオンプレミス VMware を準備するServer Migration

Contoso が VM を Azure に移行するには、以下の Azure コンポーネントが必要です。

- 移行中に作成される Azure VM が配置される VNet。
- プロビジョニングおよび構成された Azure Migrate アプライアンス。

これらを次のように設定します。

1. ネットワークを設定します - Contoso は、[Azure インフラストラクチャのデプロイ](./contoso-migration-infrastructure.md)を行ったときに、Azure Migrate: Server Migration に使用できるネットワークを既にセットアップしています

    - SmartHotel360 アプリケーションは運用アプリケーションであり、VM はプライマリ リージョン (`East US 2`) の Azure 運用ネットワーク (`VNET-PROD-EUS2`) に移行されます。
    - 両方の VM は、運用リソースのために使用される `ContosoRG` リソース グループに配置されます。
    - アプリケーションのフロントエンド VM (`WEBVM`) は、運用ネットワーク内のフロントエンド サブネット (`PROD-FE-EUS2`) に移行されます。
    - アプリケーション データベース VM (`SQLVM`) は、運用ネットワーク内のデータベース サブネット (`PROD-DB-EUS2`) に移行されます。

2. Azure Migrate アプライアンスをプロビジョニングする

    - Azure Migrate から、OVA イメージをダウンロードして VMware にインポートします。

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

[Azure Migrate アプリケーションの設定については、こちらを参照してください](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-server-migration-tool)。

### <a name="prepare-on-premises-vms"></a>オンプレミスの VM を準備する

Contoso は移行後、Azure VM に接続し、Azure で VM を管理できるようにしたいと考えています。 そのため、Contoso の管理者は、移行前に以下の操作を行います。

1. インターネットでアクセスする場合:

    - 移行前に、オンプレミス VM で RDP または SSH を有効にします。
    - **パブリック** プロファイルに TCP 規則と UDP 規則が追加されていることを確認します。
    - オペレーティング システムのファイアウォールで RDP または SSH が許可されていることを確認します。

2. サイト間 VPN 経由でアクセスするには、次のようにします。

    - 移行前に、オンプレミス VM で RDP または SSH を有効にします。
    - オペレーティング システムのファイアウォールで RDP または SSH が許可されていることを確認します。
    - Windows の場合、オンプレミス VM 上のオペレーティング システムの SAN ポリシーを **OnlineAll** に設定します。

3. Azure エージェントをインストールします。

    - [Azure Linux エージェント](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux)
    - [Azure Windows エージェント](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows)

4. その他

   - Windows の場合、移行をトリガーするときに、VM 上に保留中の Windows 更新プログラムがあってはいけません。 ある場合は、更新が完了するまで、VM にログインすることはできません。
   - 移行後、**ブート診断**を調べて、VM のスクリーンショットを確認できます。 これが機能しない場合は、VM が実行中であることを確認し、こちらの[トラブルシューティングのヒント](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)を参照してください。

**さらにサポートが必要な場合**

- [移行用の VM の準備](https://docs.microsoft.com/azure/migrate/prepare-for-migration)についてご確認ください。

## <a name="step-5-replicate-the-on-premises-vms"></a>手順 5:オンプレミスの VM をレプリケートする

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

## <a name="step-6-migrate-the-database-via-azure-database-migration-service"></a>手順 6:Azure Database Migration Service を使用してデータベースを移行する

Contoso の管理者は、Database Migration Service プロジェクトを作成し、その後にデータベースを移行する必要があります。

### <a name="create-an-azure-database-migration-service-project"></a>Azure Database Migration Service プロジェクトを作成する

1. 管理者は、Database Migration Service プロジェクトを作成します。 ソース サーバーの種類として **[SQL Server]** 、ターゲットとして **[Azure SQL Managed Instance]** を選択します。

     ![データベース移行サービス: 新しい移行プロジェクト](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-project.png)

2. 移行ウィザードが開きます。

### <a name="migrate-the-database"></a>データベースを移行する

1. 移行ウィザードで、オンプレミス データベースが配置されるソース VM を指定します。 データベースにアクセスするための資格情報を入力します。

    ![データベース移行サービス: ソースの詳細](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-wizard-source.png)

2. 移行するデータベースを選択します (`SmartHotel.Registration`)。

    ![データベース移行サービス: ソース データベースを選択する](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-wizard-source-db.png)

3. ターゲットについては、Azure 内のマネージド インスタンスの名前とアクセス資格情報を入力します。

    ![データベース移行サービス: ターゲットの詳細](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-target-details.png)

4. **[新しいアクティビティ]**  >  **[移行の実行]** で、移行を実行する際の設定を指定します。
    - ソースおよびターゲットの資格情報。
    - 移行する対象のデータベース。
    - オンプレミス VM 上で作成したネットワーク共有。 Azure Database Migration Service は、この共有にソースのバックアップを取得します。
        - ソースの SQL Server インスタンスを実行するサービス アカウントには、この共有に対する書き込み権限が必要です。
        - 共有の FQDN パスを使用する必要があります。
    - ストレージ アカウント コンテナーへのアクセス権を Azure Database Migration Service に付与する SAS URI。このサービスによってそこへ移行用バックアップ ファイルがアップロードされます。

        ![データベース移行サービス: 移行の設定の構成](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-migration-settings.png)

5. 移行の設定を保存し、移行を実行します。
6. **[Overview]\(概要\)** で、移行のステータスを監視します。

    ![データベース移行サービス: モニター](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-monitor1.png)

7. 移行が完了したら、マネージド インスタンス上にターゲット データベースが存在することを確認します。

    ![データベース移行サービス: データベースの移行を確認する](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-monitor2.png)

## <a name="step-7-migrate-the-vms-with-azure-migrate-server-migration"></a>手順 7:Azure Migrate: Server Migration で VM を移行するServer Migration

Contoso の管理者は、簡単なテスト移行を実行して VM が正常に動作していることを確認してから、VM を移行します。

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
6. 最後に、Contoso ドメイン コント ローラーのいずれかで、`WEBVM` の DNS レコードを更新します。

### <a name="update-the-connection-string"></a>接続文字列を更新する

移行プロセスの最終ステップとして、Contoso の管理者はアプリケーションの接続文字列を更新して、Contoso の SQL Managed Instance で実行されている移行されたデータベースを指すようにします。

1. Azure portal で **[設定]**  >  **[接続文字列]** の順に選択して、接続文字列を探します。

    ![Connection strings](./media/contoso-migration-rehost-vm-sql-managed-instance/failover4.png)

2. SQL Managed Instance のユーザー名とパスワードで文字列を更新します。
3. 文字列が構成されたら、アプリケーションの `web.config` ファイルに含まれる現在の接続文字列を置き換えます。
4. ファイルを更新して保存したら、コマンド プロンプト ウィンドウで `iisreset /restart` を実行して、`WEBVM` 上で IIS を再起動します。
5. IIS が再起動したら、SQL Managed Instance 上で実行されているデータベースがアプリケーションで使用されます。
6. この時点で、オンプレミスの SQLVM マシンをシャットダウンできます。 移行が完了しました。

**さらにサポートが必要な場合**

- [テスト フェールオーバーを実行する方法については、こちらを参照してください](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure)。
- [復旧計画を作成する方法については、こちらを参照してください](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans)。
- [Azure にフェールオーバーする方法については、こちらを参照してください](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover)。

## <a name="clean-up-after-migration"></a>移行後にクリーンアップする

移行が完了すると、SmartHotel360 アプリケーションが Azure VM 上で実行され、SmartHotel360 データベースが Azure SQL Managed Instance 上で使用可能になります。

ここで、以下のクリーンアップ タスクを行う必要があります。

- `WEBVM` マシンを vCenter Server インベントリから削除します。
- `SQLVM` マシンを vCenter Server インベントリから削除します。
- ローカル バックアップ ジョブから `WEBVM` と `SQLVM` を削除します。
- `WEBVM` の新しい場所と IP アドレスを示すように内部ドキュメントを更新します。
- `SQLVM` を内部ドキュメントから削除します。 代わりに、Contoso は、`SQLVM` が削除され、VM インベントリに残っていないことを示すようにドキュメントを変更できます。
- 使用停止になった VM とやりとりするリソースがあれば確認します。 すべての関連する設定またはドキュメントを更新して新しい構成を反映します。

## <a name="review-the-deployment"></a>デプロイを再調査する

リソースを Azure に移行したら、新しいインフラストラクチャを完全に操作可能にして、セキュリティで保護する必要があります。

### <a name="security"></a>セキュリティ

Contoso セキュリティ チームは、Azure VM と SQL Managed Instance を調査し、その実装に関して、セキュリティ上の問題がないかを確認します。

- チームは、VM のアクセスを制御するために使用されるネットワーク セキュリティ グループを確認します。 ネットワーク セキュリティ グループは、アプリケーションに対して許可されたトラフィックのみが確実に通過できるようにするのに役立ちます。
- Contoso のセキュリティ チームは、ディスク上のデータ保護のために、Azure Disk Encryption と Azure Key Vault の使用も検討します。
- チームは、マネージド インスタンス上での脅威の検出を有効にします。 脅威が検出された場合は、セキュリティ チームとサービス デスク システムにアラートを送信して、チケットを作成します。 [SQL Managed Instance を対象にした脅威の検出の詳細については、こちらを参照してください](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-threat-detection)。

     ![SQL Managed Instance のセキュリティ: 脅威の検出](./media/contoso-migration-rehost-vm-sql-managed-instance/mi-security.png)

VM のセキュリティに関する作業の詳細については、「[Azure における IaaS ワークロードのセキュリティに関するベスト プラクティス](https://docs.microsoft.com/azure/security/fundamentals/iaas)」を参照してください。

### <a name="bcdr"></a>BCDR

事業継続とディザスター リカバリー (BCDR) のために、Contoso は次のアクションを実施します。

- データの安全性を確保する: Contoso は、Azure Backup サービスを使用して VM 上のデータをバックアップします。 詳細については、「[Azure VM バックアップの概要](https://docs.microsoft.com/azure/backup/backup-azure-vms-introduction)」を参照してください。
- アプリケーションの稼働を維持する: Contoso は、Site Recovery を使用して、Azure 内のアプリケーション VM をセカンダリ リージョンにレプリケートします。 [詳細については、こちらを参照してください](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-quickstart)。
- Contoso は、[データベースのバックアップ](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups)など、SQL Managed Instance の管理についてさらに学習します。

### <a name="licensing-and-cost-optimization"></a>ライセンスとコストの最適化

- Contoso は、WEBVM の既存のライセンスを所有しています。 Azure ハイブリッド特典の価格を利用するために、Contoso は既存の Azure VM を変換します。
- Contoso は [Azure Cost Management と Billing](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview) を使用して、IT リーダーが定めた予算内に確実に収まるようにします。

## <a name="conclusion"></a>まとめ

この記事では、Contoso は Azure Migrate サービスを使用して、アプリケーションのフロントエンド VM を Azure に移行することで、Azure 内で SmartHotel360 アプリケーションをリホストします。 Contoso は、Azure Database Migration Service を使用して、オンプレミス データベースを Azure SQL Managed Instance に移行します。
