---
title: オンプレミス アプリケーションを Azure VM および Azure SQL Managed Instance に移行してリホストする
description: Contoso が、Azure SQL Managed Instance を使用して、オンプレミス アプリを Azure VM にリホストする方法について説明します。
author: givenscj
ms.author: abuck
ms.date: 07/01/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: migrate
ms.custom: think-tank
ms.openlocfilehash: 813e3a7dd9c0ee90bf86c89ab72123160952b6fc
ms.sourcegitcommit: 9d76f709e39ff5180404eacd2bd98eb502e006e0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/17/2021
ms.locfileid: "100631595"
---
<!-- cSpell:ignore WEBVM SQLVM OSTICKETWEB OSTICKETMYSQL contosohost vcenter contosodc NSGs agentless SQLMI iisreset -->

# <a name="rehost-an-on-premises-application-by-migrating-to-azure-vms-and-azure-sql-managed-instance"></a>オンプレミス アプリケーションを Azure VM および Azure SQL Managed Instance に移行してリホストする

この記事では、Contoso という架空の会社が、Azure Migrate を使用して、VMware 仮想マシン (VM) 上で実行されている 2 層の Windows .NET フロントエンド アプリケーションを Azure VM に移行する方法について説明します。 また、Contoso がアプリケーション データベースを Azure SQL Managed Instance に移行する方法についても説明します。

この例で使用される SmartHotel360 アプリケーションは、オープン ソースとして提供されています。 独自のテスト目的に使用する場合は、[GitHub](https://github.com/Microsoft/SmartHotel360) からダウンロードしてください。

## <a name="business-drivers"></a>ビジネス ドライバー

Contoso の IT リーダーシップ チームは、自社のビジネス パートナーと緊密に連携して、ビジネス部門がこの移行で何を達成しようとしているのかを理解しました。 目的は次のとおりです。

- **ビジネスの成長への対応。** Contoso は成長しています。 その結果、会社のオンプレミスのシステムとインフラストラクチャにかかる圧力が増加しています。
- **効率化。** Contoso は不要な手順を取り除き、開発者とユーザーのプロセスを効率化する必要があります。 ビジネス部門は、会社が顧客の要求により迅速に対応するために、IT 部門に対して、時間やコストを無駄にせず、迅速に作業を行うことを求めています。
- **機敏性の向上。** Contoso IT は、ビジネス部門の要求に対して、対応力を向上させる必要があります。 会社がグローバル経済で成功を収めるために、市場で起きる変化に遅れることなく対応する必要があります。 Contoso の IT 部門がビジネスの妨げになったり、ビジネスの妨げになったりするようなことがあってはなりません。
- **スケール。** 会社のビジネスが順調に成長している中で、Contoso IT 部門は、同じペースで拡張できるシステムを提供する必要があります。

## <a name="migration-goals"></a>移行の目標

Contoso クラウド チームは、この移行の目標を確認しました。 会社では、移行の目標を使用して、最適な移行方法を決定します。

- 移行しても、Azure のアプリケーションは、Contoso のオンプレミス VMware 環境で現在提供されているアプリケーションと同じパフォーマンスを発揮できる必要があります。 クラウドに移行するのは、そのアプリケーションのパフォーマンスがそれほど重要でないからではありません。
- Contoso はアプリケーションへの投資を望んでいません。 このアプリケーションはビジネスに欠くことのできない重要なものですが、Contoso は現在の形式のままクラウドに移行したいと考えています。
- アプリケーション移行後のデータベース管理のタスクを最小限に抑える必要があります。
- Contoso は、このアプリケーションに Azure SQL Database を使用したくないと考えています。 Contoso は別のデータベースを探しています。

## <a name="solution-design"></a>ソリューション設計

Contoso は、会社の目標と要件を明確にした後、デプロイ ソリューションを設計してレビューし、移行プロセスを確認します。 また、移行に使用する Azure サービスも確認します。

### <a name="current-architecture"></a>現在のアーキテクチャ

- Contoso にはメイン データセンターが 1 つあります (`contoso-datacenter`)。 このデータセンターは、米国東部のニューヨーク市にあります。
- Contoso は、その他にも米国内全体で 3 つの地方支店があります。
- メイン データセンターは、光ファイバーによるメトロ イーサネット接続 (500 Mbps) でインターネットに接続されています。
- 各支店は、ビジネス クラスの接続を使用して、インターネットにローカルで接続されています。メイン データセンターには、IPsec VPN トンネルで接続されています。 このセットアップにより、Contoso のネットワーク全体を永続的に接続でき、インターネット接続が最適化されます。
- メイン データセンターは、VMware によって完全に仮想化されています。 Contoso には、vCenter Server 6.5 で管理されている ESXi 6.5 仮想化ホストが 2 つあります。
- Contoso は ID 管理に Active Directory を使用しています。 Contoso は内部ネットワーク上で DNS サーバーを使用しています。
- Contoso には、オンプレミスのドメイン コントローラーがあります (`contosodc1`)。
- ドメイン コントローラーは、VMware VM 上で実行されています。 支店にあるドメイン コントローラーは、物理サーバー上で実行されています。
- SmartHotel360 アプリケーションは、2 つの VM (`WEBVM` と `SQLVM`) に階層化されており、VMware ESXi バージョン 6.5 ホスト (`contosohost1.contoso.com`) 上に配置されています。
- VMware 環境は、VM 上で実行中の vCenter Server 6.5 (`vcenter.contoso.com`) によって管理されています。

![Contoso の現在のアーキテクチャの図。](./media/contoso-migration-rehost-vm-sql-managed-instance/contoso-architecture.png)

### <a name="proposed-architecture"></a>提案されたアーキテクチャ

このシナリオでは、Contoso は次のように 2 層のオンプレミス旅行アプリケーションを移行したいと考えています。

- アプリケーション データベース (`SmartHotelDB`) を SQL Managed Instance に移行します。
- フロントエンド `WEBVM` を Azure VM に移行します。
- Contoso データセンター内のオンプレミス VM は、移行の終了後に使用停止にされます。

![シナリオ アーキテクチャの図。](./media/contoso-migration-rehost-vm-sql-managed-instance/architecture.png)

### <a name="database-considerations"></a>データベースの考慮事項

Contoso はソリューション設計プロセスの一環として、Azure SQL Database と SQL Managed Instance の機能を比較しました。 次の点を考慮した結果、SQL Managed Instance を使用することにしました。

- SQL Managed Instance では、最新のオンプレミス SQL Server バージョンとのほぼ 100% の互換性を提供することが目的とされています。 SQL Server をオンプレミスまたは IaaS (サービスとしてのインフラストラクチャ) VM で実行しており、最小限の設計変更でアプリケーションをフル マネージド サービスに移行することを望んでいるお客様には、SQL Managed Instance が推奨されます。
- Contoso は、多数のアプリケーションをオンプレミスから IaaS に移行することを計画しています。 これらのアプリケーションの多くは、ISV が提供するものです。 Contoso は、SQL Managed Instance を使用すれば、サポートされていない可能性がある SQL Database を使用することなく、これらのアプリケーションのデータベース互換性を確保する助けになることを認識しています。
- Contoso は、完全に自動化された Azure Database Migration Service を使用して、SQL Managed Instance へのリフトアンドシフト移行を実行できます。 このサービスを導入すると、Contoso は将来のデータベース移行にそれを再利用できます。
- SQL Managed Instance では、SmartHotel360 アプリケーションの重要なコンポーネントである SQL Server エージェントがサポートされています。 Contoso ではこの互換性が必要です。 互換性がない場合、アプリケーションで必要なメンテナンス プランを再設計する必要があります。
- ソフトウェア アシュアランスにより、Contoso は SQL Server 向け Azure ハイブリッド特典を使用して、既存のライセンスと引き換えに、SQL Managed Instance を割引料金で利用できます。 これにより、SQL Managed Instance のコストを最大 30% 節約できます。
- SQL Managed Instance は仮想ネットワークに完全に含まれるため、Contoso のデータに対して高い分離性とセキュリティが提供されます。 Contoso は、パブリック インターネットから分離された環境を維持しながら、パブリック クラウドのメリットを得ることができます。
- SQL Managed Instance では、多くのセキュリティ機能がサポートされています。 Always Encrypted、動的データ マスキング、行レベルのセキュリティ、脅威検出などの機能があります。

### <a name="solution-review"></a>ソリューションのレビュー

Contoso は、長所と短所の一覧をまとめて、提案されたデザインを評価します。

| 考慮事項 | 詳細 |
| --- | --- |
| **長所** | `WEBVM` は変更なしで Azure に移されるので、移行が簡単になります。 <br><br> SQL Managed Instance では、Contoso の技術面の要件と目標がサポートされています。 <br><br> SQL Managed Instance では、SQL Server 2008 R2 から移行するときに、Contoso の現在のデプロイとの完全な互換性が提供されます。 <br><br> Contoso はソフトウェア アシュアランスへの投資を活用し、SQL Server および Windows Server 向け Azure ハイブリッド特典を使用できます。 <br><br> Contoso は、将来の移行で Azure Database Migration Service を再利用できます。 <br><br> SQL Managed Instance には、Contoso が構成する必要のないフォールト トレランスが組み込まれています。 この機能により、データ層が単一障害点ではなくなります。 |
| **短所** | `WEBVM` では、Windows Server 2008 R2 が実行されています。 このオペレーティング システムは Azure でサポートされていますが、サポート対象のプラットフォームではなくなりました。 詳細については、[Microsoft SQL Server 製品のサポート ポリシー](/troubleshoot/sql/general/support-policy-hardware-virtualization-product)に関するページをご覧ください。 <br><br> `WEBVM` だけがサービスを提供しているので、Web 層はまだ単一のフェールオーバー ポイントです。 <br><br> Contoso は、Azure App Service などのマネージド サービスに移行するのではなく、アプリケーション Web 層を VM として引き続きサポートする必要があります。 <br><br> データ層については、Contoso がオペレーティング システムまたはデータベース サーバーをカスタマイズする場合や、SQL Server と共にサードパーティ アプリケーションを実行する場合、SQL Managed Instance は最適なソリューションではない可能性があります。 IaaS VM で SQL Server を実行すると、このような柔軟性が提供されます。 |

### <a name="migration-process"></a>移行プロセス

Contoso は、次の手順を行って、SmartHotel360 アプリケーションの Web およびデータ層を Azure に移行します。

1. Contoso は既に Azure インフラストラクチャを整備済みであるため、このシナリオで使用する少数の特定 Azure コンポーネントを追加するだけでかまいません。
1. データ層を移行するときは、Azure Database Migration Service を使用します。 このサービスは、Contoso データセンターと Azure との間のサイト間 VPN 接続を経由して、オンプレミス SQL Server VM に接続します。 次にこのサービスは、データベースを移行します。
1. Web 層は、Azure Migrate によるリフトアンドシフト移行を使用して移行されます。 そのプロセスでは、オンプレミス VMware 環境の準備を整え、レプリケーションを設定して有効にしたうえで、VM を Azure にフェールオーバーすることによって移行する必要があります。

     ![移行アーキテクチャの図。](./media/contoso-migration-rehost-vm-sql-managed-instance/migration-architecture.png)

### <a name="azure-services"></a>Azure サービス

| サービス | 説明 | コスト |
| --- | --- | --- |
| [Azure Database Migration Service](/azure/dms/dms-overview) | Azure Database Migration Service を使用すると、複数のデータベース ソースから Azure データ プラットフォームに、ダウンタイムを最小限に抑えながらシームレスに移行できます。 | [サポートされているリージョン](/azure/dms/dms-overview#regional-availability)と [Azure Database Migration Service の価格](https://azure.microsoft.com/pricing/details/database-migration/)に関する情報をご覧ください。 |
| [Azure SQL Managed Instance](/azure/azure-sql/managed-instance/sql-managed-instance-paas-overview) | SQL Managed Instance は、Azure クラウド内のフル マネージド SQL Server インスタンスを表すマネージド データベース サービスです。 最新バージョンの SQL Server データベース エンジンと同じコードを使用し、最新の機能、パフォーマンスの向上、セキュリティ更新プログラムが適用されています。 | Azure で実行されている SQL Managed Instance を使用すると、容量に基づく料金が発生します。 詳細については、[SQL Managed Instance の価格](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/)に関するページを参照してください。 |
| [Azure Migrate](/azure/migrate/migrate-services-overview) | Contoso は、Azure Migrate を使用して VMware VM を評価します。 Azure Migrate は、マシンの移行適合性を評価します。 そのうえで、Azure で実行するための、サイズとコストの見積もりを提供します。 | Azure Migrate は、追加料金なしで利用できます。 評価と移行に使用することにしたツール (ファーストパーティまたは独立系ソフトウェア ベンダー) によっては料金が発生する場合があります。 [Azure Migrate の価格](https://azure.microsoft.com/pricing/details/azure-migrate/)について、詳しくはこちらを参照してください。 |

## <a name="prerequisites"></a>前提条件

Contoso と他のユーザーは、このシナリオの次の前提条件を満たす必要があります。

| 必要条件 | 詳細 |
| --- | --- |
| **Azure サブスクリプション** | このシリーズの最初の記事の中で、Contoso は既にサブスクリプションを作成しました。 Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/free/)を作成してください。 <br><br> 無料アカウントを作成する場合、サブスクリプションの管理者としてすべてのアクションを実行できます。 <br><br> 既存のサブスクリプションを使用する場合に、自分がそのサブスクリプションの管理者ではないようであれば、管理者と協力して、必要なリソース グループおよびリソースに対する所有者または共同作成者のアクセス許可を自分に割り当てます。 |
| **Azure インフラストラクチャ** | Contoso は、[移行のための Azure インフラストラクチャ](./contoso-migration-infrastructure.md)に関する記事で説明されているように、Azure インフラストラクチャを設定します。 |
| **オンプレミスのサーバー** | オンプレミス vCenter Server では、バージョン 5.5、6.0、または 6.5 を実行している必要があります。 <br><br> ESXi ホストでは、バージョン 5.5、6.0、または 6.5 を実行している必要があります。 <br><br> ESXi ホスト上で 1 つ以上の VMware VM が実行されている必要があります。 |
| **オンプレミスの VM** | Azure での実行が保証されている [Linux マシンを確認します](/azure/virtual-machines/linux/endorsed-distros)。 |
| **Database Migration Service** | Azure Database Migration Service には、[互換性のあるオンプレミスの VPN デバイス](/azure/vpn-gateway/vpn-gateway-about-vpn-devices)が必要です。 <br><br> オンプレミスの VPN デバイスを構成できる必要があります。 外部に接続するパブリック IPv4 アドレスが必要です。 このアドレスを NAT デバイスの内側に割り当てることはできません。 <br><br> オンプレミスの SQL Server データベースにアクセスできることを確認します。 <br><br> Windows ファイアウォールは、ソース データベース エンジンにアクセスできる必要があります。 [データベース エンジン アクセス用の Windows Firewall を構成する](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)方法を確認してください。 <br><br> データベース マシンの前面にファイアウォールがある場合は、SMB ポート 445 経由でのデータベースおよびファイルへのアクセスを許可する規則を追加します。 <br><br> ソース SQL Server インスタンスへの接続に使用される資格情報と、SQL Managed Instance をターゲットとする資格情報は、sysadmin サーバー ロールのメンバーである必要があります。 <br><br> Azure Database Migration Service がソース データベースをバックアップするために使用できるネットワーク共有が、オンプレミスのデータベースに存在する必要があります。 <br><br> ソース SQL Server インスタンスを実行しているサービス アカウントに、ネットワーク共有に対する書き込みアクセス許可があることを確認します。 <br><br> ネットワーク共有に対するフル コントロールのアクセス許可を持つ、Windows ユーザーとパスワードをメモしておきます。 Azure Database Migration Service では、これらのユーザーの資格情報を偽装して、Azure Storage コンテナーにバックアップ ファイルがアップロードされます。 <br><br> SQL Server Express のインストール プロセスでは、TCP/IP プロトコルが既定で **無効化** されます。 これが有効になっていることを確認します。 |

## <a name="scenario-steps"></a>シナリオのステップ

Contoso が予定しているデプロイ設定方法は次のとおりです。

> [!div class="checklist"]
>
> - **ステップ 1:SQL Managed Instance を準備する。** Contoso では、オンプレミス SQL Server データベースの移行先となる既存のマネージド インスタンスが必要です。
> - **手順 2: Azure Database Migration Service を準備する。** データベース移行プロバイダーを登録し、インスタンスを作成した後に、Database Migration Service プロジェクトを作成する必要があります。 Contoso はまた、Database Migration Service インスタンス用に Shared Access Signature (SAS) の Uniform Resource Identifier (URI) も設定する必要があります。 SAS URI によって Contoso のストレージ アカウント内のリソースへの委任アクセスが提供されるので、Contoso はストレージ オブジェクトへの制限付きアクセス許可を付与できます。 SQL Server バックアップ ファイルのアップロード先であるストレージ アカウント コンテナーに Azure Database Migration Service がアクセスできるように、SAS URI を設定します。
> - **ステップ 3:Azure Migrate: Server Migration ツール用の Azure を準備する。** Contoso は、Azure Migrate プロジェクトにサーバー移行ツールを追加します。
> - **手順 4:Azure Migrate: Server Migration 用のオンプレミス VMware を準備するServer Migration.** Contoso は、VM 検出用のアカウントを準備し、移行後に Azure VM に接続するための準備をします。
> - **手順 5:オンプレミスの VM をレプリケートする。** Contoso はレプリケーションを設定し、Azure Storage への VM のレプリケーションを開始します。
> - **手順 6: Azure Database Migration Service を介してデータベースを移行する。** Contoso は、データベースを移行します。
> - **手順 7:Azure Migrate: Server Migration で VM を移行するServer Migration.** Contoso は、テスト移行を実行してすべてが機能していることを確認した後、完全移行を実行して VM を Azure に移動します。

## <a name="step-1-prepare-a-sql-managed-instance"></a>手順 1:SQL Managed Instance を準備する

SQL Managed Instance を設定するには、次の要件を満たすサブネットが必要です。

- サブネットは専用でなければなりません。 空である必要があります。 他のクラウド サービスを含めることはできません。 サブネットはゲートウェイ サブネットであってはなりません。
- マネージド インスタンスの作成後、Contoso はサブネットにリソースを追加しないようにする必要があります。
- サブネットにネットワーク セキュリティ グループを関連付けることはできません。
- サブネットにはユーザー定義のルート テーブルが必要です。 割り当てられる唯一のルートが `0.0.0.0/0` の次ホップ インターネットである必要があります。
- 仮想ネットワーク用に省略可能なカスタム DNS が指定されている場合、Azure 内の再帰的なリゾルバーの仮想 IP アドレス `168.63.129.16` をリストに追加する必要があります。 [SQL Managed Instance のカスタム DNS を構成する](/azure/azure-sql/managed-instance/custom-dns-configure)方法をご覧ください。
- サブネットにサービス エンドポイント (ストレージまたは SQL) を関連付けることはできません。 仮想ネットワークではサービス エンドポイントを無効にする必要があります。
- サブネットには 16 個以上の IP アドレスが必要です。 [マネージド インスタンス サブネットのサイズを指定する](/azure/azure-sql/managed-instance/vnet-existing-add-subnet)方法を確認してください。
- Contoso のハイブリッド環境では、カスタム DNS 設定が必要です。 Contoso は、自社の 1 つ以上の Azure DNS サーバーを使用するように DNS 設定を構成します。 [DNS のカスタマイズの詳細については、こちらを参照してください](/azure/azure-sql/managed-instance/custom-dns-configure)。

### <a name="set-up-a-virtual-network-for-the-managed-instance"></a>マネージド インスタンス用の仮想ネットワークを設定する

仮想ネットワークを設定するには、Contoso の管理者が次の手順を実行します。

1. プライマリ リージョン (`East US 2`) に新しい仮想ネットワーク (`VNET-SQLMI-EU2`) を作成します。 これにより、仮想ネットワークが `ContosoNetworkingRG` リソース グループに追加されます。
1. `10.235.0.0/24` のアドレス空間を割り当てます。 その範囲がエンタープライズ内の他のどのネットワークとも重複しないことを確認します。
1. 次の 2 つのサブネットをネットワークに追加します。
    - `SQLMI-DS-EUS2` (`10.235.0.0/25`).
    - `SQLMI-SAW-EUS2` (`10.235.0.128/29`). このサブネットは、マネージド インスタンスにディレクトリを接続するために使用されます。

      ![SQL Managed Instance の [仮想ネットワークの作成] ペインを示すスクリーンショット。](./media/contoso-migration-rehost-vm-sql-managed-instance/mi-vnet.png)

1. 仮想ネットワークとサブネットがデプロイされた後、ネットワークを以下のようにピアリングします。

    - `VNET-SQLMI-EUS2` を `VNET-HUB-EUS2` (`East US 2` でのハブの仮想ネットワーク) とピアリングします。
    - `VNET-SQLMI-EUS2` を `VNET-PROD-EUS2` (運用ネットワーク) とピアリングします。

      ![ネットワーク ピアリングを示すスクリーンショット。](./media/contoso-migration-rehost-vm-sql-managed-instance/mi-peering.png)

1. カスタム DNS の設定を行います。 DNS は、最初は Contoso の Azure ドメイン コント ローラーを指しています。 Azure DNS はセカンダリです。 Contoso Azure ドメイン コントローラーは、以下のように配置されます。

    - `East US 2` 運用ネットワーク (`VNET-PROD-EUS2`) 内の `PROD-DC-EUS2` サブネットに配置されています。
    - `CONTOSODC3` アドレス: `10.245.42.4`。
    - `CONTOSODC4` アドレス: `10.245.42.5`。
    - Azure DNS リゾルバー: `168.63.129.16`。

      ![ネットワークの DNS サーバーを示すスクリーンショット。](./media/contoso-migration-rehost-vm-sql-managed-instance/mi-dns.png)

**さらにサポートが必要な場合**

- [SQL Managed Instance の概要については、こちらをお読みください](/azure/azure-sql/managed-instance/sql-managed-instance-paas-overview)。
- [SQL マネージド インスタンス用の仮想ネットワークを作成する](/azure/azure-sql/managed-instance/vnet-existing-add-subnet)方法をご覧ください。
- [ピアリングを設定する方法については、こちらを参照してください](/azure/virtual-network/virtual-network-manage-peering)。
- [Azure Active Directory DNS 設定を更新する方法については、こちらを参照してください](/azure/active-directory-domain-services/tutorial-create-instance)。

### <a name="set-up-routing"></a>ルーティングを設定する

マネージド インスタンスは、プライベート仮想ネットワーク内に配置されます。 Contoso は、Azure 管理サービスと通信するために、仮想ネットワークのルート テーブルを必要としています。 仮想ネットワークが、仮想ネットワークを管理するサービスと通信できない場合、仮想ネットワークへのアクセスができなくなります。

Contoso は以下の点を考慮します。

- ルート テーブルには、マネージド インスタンスから送信されるパケットを仮想ネットワーク内でルーティングする方法を指定した一連のルール (ルート) を含めます。
- ルート テーブルは、マネージド インスタンスがデプロイされているサブネットに関連付けられています。 サブネットから離れる各パケットは、関連付けられたルート テーブルに基づいて処理されます。
- 1 つのサブネットは、1 つのルート テーブルにのみ関連付けることができます。
- Microsoft Azure では、ルート テーブルの作成に追加料金はかかりません。

 ルーティングを設定するには、Contoso の管理者は次の手順を実行します。

1. `ContosoNetworkingRG` リソース グループに、ユーザー定義のルート テーブルを作成します。

    ![ルート テーブルを示すスクリーンショット。](./media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table.png)

1. SQL Managed Instance の要件に準拠するため、ルート テーブル (`MIRouteTable`) をデプロイした後、アドレス プレフィックスが `0.0.0.0/0` のルートを追加します。 **[次ホップの種類]** オプションは **[インターネット]** に設定します。

    ![ルート テーブルのプレフィックスを示すスクリーンショット。](./media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table-prefix.png)

1. ルート テーブルを (`VNET-SQLMI-EUS2` ネットワークの) `SQLMI-DB-EUS2` サブネットに関連付けます。

    ![ルート テーブルのサブネットを示すスクリーンショット。](./media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table-subnet.png)

**さらにサポートが必要な場合**

[マネージド インスタンス用のルートを設定する方法については、こちらを参照してください](/azure/azure-sql/managed-instance/instance-create-quickstart)。

### <a name="create-a-managed-instance"></a>マネージド インスタンスを作成する

これで、Contoso の管理者は、SQL Managed Instance をプロビジョニングできます。

1. マネージド インスタンスにより、ビジネス アプリケーションにサービスが提供されるため、会社のプライマリ リージョン (`East US 2`) にマネージド インスタンスをデプロイします。 マネージド インスタンスを `ContosoRG` リソース グループに追加します。
1. 価格レベルを選択し、インスタンスのコンピューティングとストレージのサイズを設定します。 詳細については、[SQL Managed Instance の価格](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/)に関するページを参照してください。

    ![[SQL Managed Instance] ペインを示すスクリーンショット。](./media/contoso-migration-rehost-vm-sql-managed-instance/mi-create.png)

1. マネージド インスタンスをデプロイすると、`ContosoRG` リソース グループ内に 2 つの新しいリソースが表示されます。

    - 新しい SQL マネージド インスタンス。
    - Contoso に複数のマネージド インスタンスがある場合の仮想クラスター。

      ![2 つの新しいリソースを示すスクリーンショット。](./media/contoso-migration-rehost-vm-sql-managed-instance/mi-resources.png)

**さらにサポートが必要な場合**

[マネージド インスタンスをプロビジョニングする方法については、こちらを参照してください](/azure/azure-sql/managed-instance/instance-create-quickstart)。

## <a name="step-2-prepare-azure-database-migration-service"></a>手順 2: Azure Database Migration Service を準備する

Azure Database Migration Service を準備するために、Contoso の管理者はいくつかの作業を行う必要があります。

- Azure 内で Database Migration Service プロバイダーを登録します。
- Azure Storage にアクセスするためのアクセス許可を Database Migration Service に付与して、データベースの移行に使用するバックアップ ファイルをアップロードできるようにします。 Azure Storage にアクセスできるようにするために、Azure Blob Storage コンテナーを作成します。 Blob Storage コンテナーの SAS URI を生成します。
- Azure Database Migration Service プロジェクトを作成します。

次の手順を実行します。

1. サブスクリプションにデータベース移行プロバイダーを登録します。 ![Database Migration Service の登録を示すスクリーンショット。](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-subscription.png)

1. Azure Blob Storage コンテナーを作成します。 Contoso は、SAS URI を生成し、Azure Database Migration Service がそれにアクセスできるようにします。

    ![SAS URI の生成を示すスクリーンショット。](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-sas.png)

1. Azure Database Migration Service インスタンスを作成する。

    ![インスタンスの作成を示すスクリーンショット。](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-instance.png)

1. Database Migration Service インスタンスを `VNET-PROD-DC-EUS2` 仮想ネットワークの `PROD-DC-EUS2` サブネットに配置します。
    - ここにそのインスタンスを配置する理由は、VPN ゲートウェイ経由でオンプレミスの SQL Server VM にアクセスできる仮想ネットワーク内に、このサービスが存在する必要があるためです。
    - `VNET-PROD-EUS2` は `VNET-HUB-EUS2` とピアリングされていて、リモート ゲートウェイを使用することが許可されています。 **[リモート ゲートウェイを使用する]** オプションにより、インスタンスが必要に応じて確実に通信ができるようになります。

        ![ネットワークの構成を示すスクリーンショット。](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-network.png)

**さらにサポートが必要な場合**

- [Azure Database Migration Service を設定する方法については、こちらを参照してください](/azure/dms/quickstart-create-data-migration-service-portal)。
- [SAS を作成して使用する方法については、こちらを参照してください](/azure/storage/common/storage-sas-overview)。

## <a name="step-3-prepare-azure-for-the-azure-migrate-server-migration-tool"></a>手順 3:Azure Migrate: Server Migration ツール用の Azure を準備するServer Migration ツール

Contoso が VM を Azure に移行するには、以下の Azure コンポーネントが必要です。

- 移行中に作成される Azure VM が配置される仮想ネットワーク。
- Azure Migrate: Server Migration ツール (プロビジョニング済みのもの)。

Contoso の管理者は、これらのコンポーネントを次のように設定します。

1. ネットワークをセットアップします。 Contoso は、[Azure インフラストラクチャのデプロイ](./contoso-migration-infrastructure.md)を行ったときに、Azure Migrate: Server Migration に使用できるネットワークを既にセットアップしています。

    - SmartHotel360 アプリケーションは運用アプリケーションであり、VM はプライマリ リージョン (`East US 2`) の Azure 運用ネットワーク (`VNET-PROD-EUS2`) に移行されます。
    - 両方の VM は、運用リソースのために使用される `ContosoRG` リソース グループに配置されます。
    - アプリケーションのフロントエンド VM (`WEBVM`) は、運用ネットワークのフロントエンド サブネット (`PROD-FE-EUS2`) に移行されます。
    - アプリケーション データベース VM (`SQLVM`) は、運用ネットワークのデータベース サブネット (`PROD-DB-EUS2`) に移行されます。

## <a name="step-4-prepare-on-premises-vmware-for-azure-migrate-server-migration"></a>手順 4:Azure Migrate: Server Migration 用のオンプレミス VMware を準備するServer Migration

Contoso が VM を Azure に移行するには、以下の Azure コンポーネントが必要です。

- 移行中に作成される Azure VM が配置される仮想ネットワーク。
- プロビジョニングおよび構成された Azure Migrate アプライアンス。

Contoso の管理者は、次の手順に従ってこれらのコンポーネントを設定します。

1. ネットワークをセットアップします。 Contoso は、[Azure インフラストラクチャのデプロイ](./contoso-migration-infrastructure.md)を行ったときに、Azure Migrate: Server Migration に使用できるネットワークを既にセットアップしています。

    - SmartHotel360 アプリケーションは運用アプリケーションであり、VM はプライマリ リージョン (`East US 2`) の Azure 運用ネットワーク (`VNET-PROD-EUS2`) に移行されます。
    - どちらの VM も、運用リソースに使用される `ContosoRG` リソース グループに配置されます。
    - アプリケーション フロントエンド VM (`WEBVM`) は、運用ネットワークのフロントエンド サブネット (`PROD-FE-EUS2`) に移行されます。
    - アプリケーション データベース VM (`SQLVM`) は、運用ネットワークのデータベース サブネット (`PROD-DB-EUS2`) に移行されます。

1. Azure Migrate アプライアンスをプロビジョニングする

    1. Azure Migrate から、OVA イメージをダウンロードして VMware にインポートします。

        ![OVA ファイルのダウンロードを示すスクリーンショット。](./media/contoso-migration-rehost-vm/migration-download-ova.png)

    1. インポートしたイメージを起動し、次の手順に従ってツールを構成します。

       1. 前提条件を設定します。

          ![前提条件の設定を示すスクリーンショット。](./media/contoso-migration-rehost-vm/migration-setup-prerequisites.png)

       1. ツールを Azure サブスクリプションにポイントします。

          ![サブスクリプションの選択を示すスクリーンショット](./media/contoso-migration-rehost-vm/migration-register-azure.png)

       1. VMware vCenter の資格情報を設定します。

          ![VMware vCenter の資格情報の設定を示すスクリーンショット。](./media/contoso-migration-rehost-vm/migration-vcenter-server.png)

       1. 検出用の Linux または Windows ベースの資格情報をすべて追加します。

          ![Linux と Windows の資格情報の設定を示すスクリーンショット。](./media/contoso-migration-rehost-vm/migration-credentials.png)

1. 構成後、ツールによってすべての仮想マシンが列挙されるまでに少し時間がかかります。 このプロセスが完了すると、Azure の Azure Migrate ツールに VM が設定されていることがわかります。

**さらにサポートが必要な場合**

[Azure Migrate アプライアンス](/azure/migrate/migrate-services-overview#azure-migrate-server-migration-tool)を設定する方法をご覧ください。

### <a name="prepare-on-premises-vms"></a>オンプレミスの VM を準備する

Contoso は移行後、Azure VM に接続し、Azure で VM を管理できるようにしたいと考えています。 Contoso の管理者は、移行前に次の手順を実行する必要があります。

1. インターネットでアクセスする場合:

    - 移行前に、オンプレミス VM で RDP または SSH を有効にします。
    - **パブリック** プロファイルに TCP 規則と UDP 規則が追加されていることを確認します。
    - オペレーティング システムのファイアウォールで RDP または SSH が許可されていることを確認します。

1. サイト間 VPN でアクセスする場合:

    - 移行前に、オンプレミス VM で RDP または SSH を有効にします。
    - オペレーティング システムのファイアウォールで RDP または SSH が許可されていることを確認します。
    - Windows の場合、オンプレミス VM 上のオペレーティング システムの SAN ポリシーを **OnlineAll** に設定します。

1. Azure エージェントをインストールします。

    - [Azure Linux エージェント](/azure/virtual-machines/extensions/agent-linux)
    - [Azure Windows エージェント](/azure/virtual-machines/extensions/agent-windows)

1. その他の考慮事項

   - Windows の場合、移行をトリガーするときに、VM 上に保留中の Windows 更新プログラムがあってはいけません。 ある場合は、更新が完了するまで、VM にサインインすることはできません。
   - 移行後、**ブート診断** を調べて、VM のスクリーンショットを確認できます。 これが機能しない場合は、VM が実行中であることを確認し、こちらの[トラブルシューティングのヒント](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)を参照してください。

**さらにサポートが必要な場合**

[移行に向けて VM を準備する](/azure/migrate/prepare-for-migration)方法をご覧ください。

## <a name="step-5-replicate-the-on-premises-vms"></a>手順 5:オンプレミスの VM をレプリケートする

Contoso の管理者は、Azure への移行を実行する前に、レプリケーションを設定して有効にする必要があります。

検出が完了したら、Azure への VMware VM のレプリケーションを開始できます。

1. Azure Migrate プロジェクトで、 **[サーバー]**  >  **[Azure Migrate: Server Migration]** 内)。 次に、 **[レプリケート]** を選択します。

    ![[レプリケート] オプションを示すスクリーンショット。](./media/contoso-migration-rehost-vm/select-replicate.png)

1. **[レプリケート]**  >  **[ソースの設定]**  >  **[マシンは仮想化されていますか?]** で、 **[はい (VMware vSphere の場合)]** を選択します。

1. **[オンプレミスのアプライアンス]** で、設定済みの Azure Migrate アプライアンスの名前を選択し、 **[OK]** を選択します。

    ![[ソースの設定] タブを示すスクリーンショット。](./media/contoso-migration-rehost-vm/source-settings.png)

1. **[仮想マシン]** で、レプリケートするマシンを選択します。
    - VM の評価を実行した場合は、評価結果から VM のサイズ設定とディスクの種類 (Premium または Standard) の推奨事項を適用できます。 **[Azure Migrate Assessment から移行設定をインポートしますか?]** で、 **[はい]** オプションを選択します。
    - 評価を実行しなかった場合、または評価の設定を使用しない場合は、 **[いいえ]** オプションを選択します。
    - 評価の使用を選択した場合は、VM グループと評価名を選択します。

    ![評価の選択を示すスクリーンショット。](./media/contoso-migration-rehost-vm/select-assessment.png)

1. **[仮想マシン]** で、必要に応じて VM を検索し、移行する各 VM を確認します。 次に、 **[次のステップ: ターゲット設定]** をクリックします。

1. **[ターゲット設定]** で、サブスクリプションと、移行先となるターゲット リージョンを選択します。 移行後に Azure VM が属するリソース グループも指定します。 **[仮想ネットワーク]** で、移行後に Azure VM の参加先となる Azure 仮想ネットワークまたはサブネットを選択します。

1. **[Azure ハイブリッド特典]** で、次の操作を行います。

    - Azure ハイブリッド特典を適用しない場合は、 **[いいえ]** を選択します。 次に、 **[次へ]** を選択します。
    - アクティブなソフトウェア アシュアランスまたは Windows Server サブスクリプションの対象となっている Windows Server マシンがあり、移行するマシンに特典を適用する場合は、 **[はい]** を選択します。 次に、 **[次へ]** を選択します。

1. **[コンピューティング]** で、VM の名前、サイズ、OS ディスクの種類、可用性セットを確認します。 VM は [Azure の要件](/azure/migrate/migrate-support-matrix-vmware#vmware-requirements)に準拠している必要があります。

    - **VM サイズ:** 評価の推奨事項を使用している場合は、[VM サイズ] ドロップダウン リストに推奨サイズが表示されます。 それ以外の場合は、Azure Migrate によって、Azure サブスクリプション内の最も近いサイズが選択されます。 または、 **[Azure VM サイズ]** でサイズを手動で選択することもできます。
    - **OS ディスク:** VM の OS (ブート) ディスクを指定します。 OS ディスクは、オペレーティング システムのブートローダーとインストーラーがあるディスクです。
    - **可用性セット:** 移行後に VM を Azure 可用性セットに配置する必要がある場合は、セットを指定します。 このセットは、移行用に指定されたターゲット リソース グループに含まれている必要があります。

1. **[ディスク]** で、VM ディスクを Azure にレプリケートするかどうかを指定します。 Azure でのディスクの種類 (Standard SSD または HDD、あるいは Premium マネージド ディスク) を選択し、 **[次へ]** を選択します。
    - レプリケーションからディスクを除外できます。
    - ディスクを除外すると、移行後に Azure VM 上に存在しなくなります。

1. **[レプリケーションの確認と開始]** で、設定を確認します。 次に、 **[レプリケート]** を選択して、サーバーの初期レプリケーションを開始します。

> [!NOTE]
> レプリケーションを開始する前であれば、 **[管理]**  >  **[マシンのレプリケート]** でレプリケーションの設定をいつでも更新できます。 レプリケーションの開始後は、設定を変更することができません。

## <a name="step-6-migrate-the-database-via-azure-database-migration-service"></a>手順 6:Azure Database Migration Service を使用してデータベースを移行する

Contoso の管理者は、Database Migration Service プロジェクトを作成し、データベースを移行する必要があります。

### <a name="create-an-azure-database-migration-service-project"></a>Azure Database Migration Service プロジェクトを作成する

1. 管理者は、Database Migration Service プロジェクトを作成します。 ソース サーバーの種類として **[SQL Server]** 、ターゲットとして **[Azure SQL Managed Instance]** を選択します。

     ![[新しい移行プロジェクト] ペインを示すスクリーンショット。](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-project.png)

1. 移行ウィザードが開きます。

### <a name="migrate-the-database"></a>データベースを移行する

1. 移行ウィザードで、オンプレミス データベースが配置されるソース VM を指定します。 データベースにアクセスするための資格情報を入力します。

    ![[ソースの詳細] ペインを示すスクリーンショット。](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-wizard-source.png)

1. 移行するデータベース (`SmartHotel.Registration`) を選択します。

    ![[ソース データベースの選択] ペインを示すスクリーンショット。](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-wizard-source-db.png)

1. ターゲットについては、Azure 内のマネージド インスタンスの名前とアクセス資格情報を入力します。

    ![[ターゲットの詳細] ペインを示すスクリーンショット。](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-target-details.png)

1. **[新しいアクティビティ]**  >  **[移行の実行]** で、移行を実行する際の設定を指定します。
    - ソースおよびターゲットの資格情報。
    - 移行する対象のデータベース。
    - オンプレミス VM 上で作成したネットワーク共有。 Azure Database Migration Service は、この共有にソースのバックアップを取得します。
        - ソースの SQL Server インスタンスを実行するサービス アカウントには、この共有に対する書き込み権限が必要です。
        - 共有の FQDN パスを使用する必要があります。
    - ストレージ アカウント コンテナーへのアクセス権を Azure Database Migration Service に付与する SAS URI。このサービスによってそこへ移行用バックアップ ファイルがアップロードされます。

        ![[移行の設定の構成] 画面を示すスクリーンショット。](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-migration-settings.png)

1. 移行の設定を保存し、移行を実行します。
1. **[Overview]\(概要\)** で、移行のステータスを監視します。

    ![状態を示すスクリーンショット。](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-monitor1.png)

1. 移行が完了したら、マネージド インスタンス上にターゲット データベースが存在することを確認します。

    ![データベース移行の確認を示すスクリーンショット。](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-monitor2.png)

## <a name="step-7-migrate-the-vms-with-azure-migrate-server-migration"></a>手順 7:Azure Migrate: Server Migration で VM を移行するServer Migration

Contoso の管理者はクイック テスト移行を実行し、VM が正常に動作していることを確認します。 その後、VM を移行します。

### <a name="run-a-test-migration"></a>テスト移行を実行する

1. **[移行の目標]**  >  **[サーバー]**  >  **[Azure Migrate: Server Migration]** で、 **[移行されたサーバーをテストします]** を選択します。

     ![[移行されたサーバーをテストします] 項目を示すスクリーンショット。](./media/contoso-migration-rehost-linux-vm/test-migrated-servers.png)

1. テストする VM を選択したまま (または右クリックし)、 **[テスト移行]** を選択します。

    ![[テスト移行] 項目を示すスクリーンショット。](./media/contoso-migration-rehost-linux-vm/test-migrate.png)

1. **[テスト移行]** で、移行後に Azure VM が配置される Azure 仮想ネットワークを選択します。 非運用環境の仮想ネットワークを使用することをお勧めします。
1. **テスト移行** ジョブが開始されます。 ポータルの通知でジョブを監視します。
1. 移行が完了したら、Azure portal の **[仮想マシン]** で、移行された Azure VM を確認します。 マシン名には、サフィックス **-Test** が含まれています。
1. テストが完了したら、 **[マシンのレプリケート]** で Azure VM を選択したまま (または右クリックし)、 **[テスト移行をクリーンアップ]** を選択します。

    ![[テスト移行をクリーンアップ] 項目を示すスクリーンショット。](./media/contoso-migration-rehost-linux-vm/clean-up.png)

### <a name="migrate-the-vm"></a>VM の移行

次に、Contoso の管理者は完全移行を実行して移動を完了します。

1. Azure Migrate プロジェクトで、 **[サーバー]**  >  **[Azure Migrate: Server Migration]** の順に移動し、 **[サーバーをレプリケートしています]** を選択します。

    ![[サーバーをレプリケートしています] 項目を示すスクリーンショット。](./media/contoso-migration-rehost-linux-vm/replicating-servers.png)

1. **[マシンのレプリケート]** で、VM を選択したまま (または右クリックし)、 **[移行]** を選択します。
1. **[移行]**  >  **[仮想マシンをシャットダウンし、データ損失のない計画された移行を実行しますか]** で、 **[はい]**  >  **[OK]** の順に選択します。
    - 既定では、Azure Migrate によってオンプレミス VM がシャットダウンされ、前回のレプリケーションが発生した後に行われた VM の変更を同期するためにオンデマンド レプリケーションが実行されます。 このアクションにより、データ損失が発生しなくなります。
    - VM をシャットダウンしない場合は、 **[いいえ]** を選択します。
1. VM に対して移行ジョブが開始されます。 Azure の通知でジョブを追跡します。
1. ジョブが完了したら、 **[仮想マシン]** ページで VM を表示して管理できます。
1. 最後に、Contoso ドメイン コント ローラーのいずれかで、`WEBVM` の DNS レコードを更新します。

### <a name="update-the-connection-string"></a>接続文字列を更新する

移行プロセスの最後の手順として、Contoso の管理者は、Contoso の SQL Managed Instance で実行されている移行されたデータベースを指すように、アプリケーションの接続文字列を更新します。

1. Azure portal で **[設定]**  >  **[接続文字列]** の順に選択して、接続文字列を探します。

    ![[接続文字列] オプションを示すスクリーンショット。](./media/contoso-migration-rehost-vm-sql-managed-instance/failover4.png)

1. SQL Managed Instance のユーザー名とパスワードで文字列を更新します。
1. 文字列が構成されたら、アプリケーションの `web.config` ファイルに含まれる現在の接続文字列を置き換えます。
1. ファイルを更新して保存したら、コマンド プロンプト ウィンドウで `iisreset /restart` を実行して、`WEBVM` 上で IIS を再起動します。
1. IIS の再起動後、SQL Managed Instance 上で実行されているデータベースがアプリケーションで使用されます。
1. この時点で、オンプレミスの SQLVM マシンをシャットダウンできます。 移行が完了しました。

**さらにサポートが必要な場合**

- [テスト フェールオーバーを実行する方法については、こちらを参照してください](/azure/site-recovery/tutorial-dr-drill-azure)。
- [復旧計画を作成する方法については、こちらを参照してください](/azure/site-recovery/site-recovery-create-recovery-plans)。
- [Azure にフェールオーバーする方法については、こちらを参照してください](/azure/site-recovery/site-recovery-failover)。

## <a name="clean-up-after-migration"></a>移行後にクリーンアップする

移行が完了すると、SmartHotel360 アプリケーションが Azure VM 上で実行され、SmartHotel360 データベースが Azure SQL Managed Instance で使用可能になります。

次に、Contoso は、以下のクリーンアップ タスクを実行する必要があります。

- `WEBVM` マシンを vCenter Server インベントリから削除します。
- `SQLVM` マシンを vCenter Server インベントリから削除します。
- ローカル バックアップ ジョブから `WEBVM` と `SQLVM` を削除します。
- `WEBVM` の新しい場所と IP アドレスを示すように内部ドキュメントを更新します。
- `SQLVM` を内部ドキュメントから削除します。 代わりに、Contoso は、`SQLVM` が削除され、VM インベントリに残っていないことを示すようにドキュメントを変更できます。
- 使用停止になった VM とやりとりするリソースがあれば確認します。 すべての関連する設定またはドキュメントを更新して新しい構成を反映します。

## <a name="review-the-deployment"></a>デプロイを再調査する

リソースを Azure に移行したら、新しいインフラストラクチャを完全に操作可能にして、セキュリティで保護する必要があります。

### <a name="security"></a>Security

Contoso セキュリティ チームは、Azure VM と SQL Managed Instance を調査し、その実装でセキュリティ上の問題がないかどうかを確認します。

- チームは、VM のアクセスを制御するために使用されるネットワーク セキュリティ グループを確認します。 ネットワーク セキュリティ グループは、アプリケーションに対して許可されたトラフィックだけが通過できるようにするのに役立ちます。
- Contoso のセキュリティ チームは、ディスク上のデータ保護のために、Azure Disk Encryption と Azure Key Vault の使用も検討します。
- チームは、マネージド インスタンス上での脅威の検出を有効にします。 脅威が検出された場合は、セキュリティ チームとサービス デスク システムにアラートを送信して、チケットを作成します。 [SQL Managed Instance を対象にした脅威の検出の詳細については、こちらを参照してください](/azure/azure-sql/managed-instance/threat-detection-configure)。

     ![SQL Managed Instance のセキュリティ: 脅威検出画面を示すスクリーンショット。](./media/contoso-migration-rehost-vm-sql-managed-instance/mi-security.png)

VM のセキュリティに関する作業の詳細については、「[Azure における IaaS ワークロードのセキュリティに関するベスト プラクティス](/azure/security/fundamentals/iaas)」を参照してください。

### <a name="business-continuity-and-disaster-recovery"></a>事業継続とディザスター リカバリー

事業継続とディザスター リカバリーのために、Contoso は次のアクションを実施します。

- **データの安全性を確保する。** Contoso は、Azure Backup サービスを使用して VM 上のデータをバックアップします。 詳細については、「[Azure VM バックアップの概要](/azure/backup/backup-azure-vms-introduction)」をご覧ください。
- **アプリケーションの稼働を維持する。** Contoso は、Site Recovery を使用して、Azure 内のアプリケーション VM をセカンダリ リージョンにレプリケートします。 詳細については、[Azure VM のセカンダリ Azure リージョンへのディザスター リカバリーの設定](/azure/site-recovery/azure-to-azure-quickstart)に関する記事をご覧ください。
- **詳細情報。** Contoso は、[データベースのバックアップ](/azure/azure-sql/database/automated-backups-overview)など、SQL Managed Instance の管理の詳細を確認します。

### <a name="licensing-and-cost-optimization"></a>ライセンスとコストの最適化

- Contoso は、WEBVM の既存のライセンスを所有しています。 Azure ハイブリッド特典の価格を利用するために、Contoso は既存の Azure VM を変換します。
- Contoso は [Azure Cost Management および Billing](/azure/cost-management-billing/cost-management-billing-overview) を使用して、IT リーダーが定めた予算内に確実に収まるようにします。

## <a name="conclusion"></a>まとめ

この記事では、Contoso が Azure Migrate を使用して、アプリケーション フロントエンド VM を Azure に移行することによって、SmartHotel360 アプリケーションを Azure にリホストしました。 Contoso は、Azure Database Migration Service を使用して、オンプレミス データベースを SQL Managed Instance に移行しました。
