---
title: オンプレミス アプリケーションを Azure App Service Web アプリと SQL マネージド インスタンスにリファクターする
description: Contoso が、オンプレミスのアプリケーションを Azure App Service Web アプリと SQL マネージド インスタンスに移行することで再ホストする方法について説明します。
author: givenscj
ms.author: abuck
ms.date: 07/01/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: migrate
ms.custom: think-tank
ms.openlocfilehash: c8f7aa18c786c4e65e1dbd0c00b90a6110ad8846
ms.sourcegitcommit: c167c45b66cc7324b60c88b8b7aac439f956b65d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102208321"
---
<!-- cSpell:ignore contoso's contosohost vcenter contosodc smarthotel SQLMI SHWCF SHWEB -->

# <a name="refactor-an-on-premises-application-to-an-azure-app-service-web-app-and-a-sql-managed-instance"></a>オンプレミス アプリケーションを Azure App Service Web アプリと SQL マネージド インスタンスにリファクターする

この記事では、Contoso という架空の会社が、Azure への移行の一環として、VMware 仮想マシン (VM) 上で実行される 2 階層の Windows .NET アプリケーションをリファクターする方法を説明します。 Contoso チームは、アプリケーションのフロントエンド VM を、Azure App Service の Web アプリに移行します。 また、Contoso がアプリケーション データベースを Azure SQL マネージド インスタンスに移行する方法についても説明します。

この例で使用する SmartHotel360 アプリケーションは、オープンソース ソフトウェアとして提供されています。 独自のテスト目的に沿って使用する場合は、[GitHub](https://github.com/Microsoft/SmartHotel360) からダウンロードできます。

## <a name="business-drivers"></a>ビジネス ドライバー

Contoso の IT リーダーシップ チームは、ビジネス パートナーと密接に連絡を取り合い、彼らがこの移行で何を達成しようとしているのかを理解しました。

- **ビジネスの成長への対応。** Contoso は成長を続けており、オンプレミスのシステムとインフラストラクチャに負荷がかかっています。
- **効率化。** Contoso では、不要な手順を排除し、開発者とユーザーのプロセスを効率化する必要があります。 ビジネス部門は IT に対して、時間やコストを無駄にせず、迅速に作業を行ってもらう必要があります。これは、例えば、顧客の要求に素早く対応するためです。
- **機敏性の向上。** Contoso IT は、ビジネス部門の要求に対して、対応力を向上させる必要があります。 また、グローバル経済での成功を実現するために、市場の変化に遅れることなく対応できる必要があります。 対応の遅さが障害となったり、ビジネスを阻害したりすることがあってはなりません。
- **スケール。** ビジネスが順調に成長していく中で、Contoso IT は、同じペースで拡張できるシステムを提供する必要があります。
- **コストを削減する。** Contoso はライセンス コストを最小限に抑えたいと考えています。

## <a name="migration-goals"></a>移行の目標

最良の移行方法を見極めやすくするために、Contoso クラウド チームは、次の目標を設定しました。

| 必要条件 | 詳細 |
| --- | --- |
| **Application** | このアプリケーションは、オンプレミスにおける現状と同様、Azure でもクリティカルなものとなります。 <br><br> 現在の VMWare と同等のパフォーマンス機能が必要です。 <br><br> チームは、アプリケーションに投資することは望んでいません。 今のところ、管理者はアプリケーションをクラウドに安全に移行するだけです。 <br><br> チームは、アプリケーションが現在実行されている Windows Server 2008 R2 のサポートを終了したいと考えています。 <br><br> このチームはまた、SQL Server 2008 R2 から最新の PaaS (サービスとしてのプラットフォーム) データベースに移行し、管理の必要性を最小限に抑えたいと考えています。 <br><br> Contoso では、可能であれば、SQL Server ライセンスとソフトウェア アシュアランスへの投資を活かしたいと考えています。 <br><br> さらに、Contoso は Web 層の単一障害点を軽減したいと考えています。 |
| **制限事項** | このアプリケーションは、同じ VM 上で実行されている ASP.NET アプリケーションと Windows Communication Foundation (WCF) サービスから成ります。 Azure App Service を使用して 2 つの Web アプリにこれらのコンポーネントを分散したいと考えています。 |
| **Azure** | Contoso は、アプリケーションを Azure に移行したいとは考えていますが、それを VM で実行することは望んでいません。 Contoso では、Azure PaaS サービスを Web 層とデータ層の両方に利用したいと考えています。 |
| **DevOps** | Contoso は、ビルドとリリース パイプラインに Azure DevOps を使用した DevOps モデルに移行したいと考えています。 |

## <a name="solution-design"></a>ソリューション設計

Contoso は、その目標と要件を設定した後、デプロイ ソリューションを設計して確認します。 また、移行に使用する Azure サービスなど、移行プロセスを明確化します。

### <a name="current-application"></a>現在のアプリケーション

- SmartHotel360 オンプレミス アプリケーションは 2 つの VM (`WEBVM` と `SQLVM`) に階層化されています。
- VM は、VMware ESXi ホスト contosohost1.contoso.com バージョン 6.5 上にあります。
- VMware 環境は、VM 上で実行されている vCenter Server 6.5 (`vcenter.contoso.com`) によって管理されています。
- Contoso にオンプレミスのデータセンター (contoso-datacenter) があり、そこにオンプレミスのドメイン コントローラー (contosodc1) が含まれています。
- Contoso データセンター内のオンプレミス VM は、移行が行われた後に使用停止にされます。

### <a name="proposed-solution"></a>提案されるソリューション

- アプリケーションの Web 層について、Contoso は Azure App Service を使用することに決めました。 この PaaS サービスを使用すると、わずかな構成変更だけでアプリケーションをデプロイできます。 Contoso は Visual Studio を使用して変更を加え、Web サイト用と WCF サービス用の 2 つの Web アプリをデプロイします。
- DevOps パイプラインの要件を満たすために、Contoso は Git リポジトリと共に Azure DevOps のソース コード管理を使用します。 コードをビルドして Azure App Service にデプロイするには、自動ビルドとリリースが使用されます。

### <a name="database-considerations"></a>データベースの考慮事項

Contoso はソリューション設計プロセスの一環として、Azure SQL Database と SQL Managed Instance の機能を比較しました。 同社では、次の考慮事項に基づき、SQL Managed Instance を使用することが決まりました。

- SQL Managed Instance では、最新のオンプレミス SQL Server バージョンとのほぼ 100% の互換性を提供することが目的とされています。 SQL Server をオンプレミスまたは IaaS (サービスとしてのインフラストラクチャ) VM で実行しており、最小限の設計変更でアプリケーションをフル マネージド サービスに移行することを望んでいるお客様には、SQL Managed Instance が推奨されます。
- Contoso は、多数のアプリケーションをオンプレミスから IaaS VM に移行することを計画しています。 それらの VM の多くは、独立系ソフトウェア ベンダーから提供されています。 SQL Managed Instance を使用すれば、これらのアプリケーションのデータベース互換性を確保できると Contoso は認識しています。 サポートされない可能性がある SQL Database ではなく、SQL Managed Instance を使用するつもりです。
- Contoso は、完全に自動化された Azure Database Migration Service を使用して、SQL Managed Instance へのリフトアンドシフト移行を簡単に実行できます。 このサービスを導入すると、Contoso は将来のデータベース移行にそれを再利用できます。
- SQL Managed Instance では、SmartHotel360 アプリケーションの重要なコンポーネントである SQL Server エージェントがサポートされています。 Contoso ではこの互換性が必要です。これがないと、アプリケーションで必要なメンテナンス プランを再設計する必要があります。
- ソフトウェア アシュアランスにより、Contoso は、SQL Server 用の Azure ハイブリッド特典を使用して、既存のライセンスと引き換えに、SQL マネージド インスタンスを割引料金で利用することができます。 SQL Managed Instance を使用することにより、最大 30% のコスト削減が可能となります。
- SQL マネージド インスタンスは仮想ネットワークに完全に含まれるため、Contoso のデータに対して高い分離性とセキュリティが提供されます。 Contoso は、パブリック インターネットから分離された環境を維持しながら、パブリック クラウドのメリットを得ることができます。
- SQL Managed Instance では、Always Encrypted、動的データ マスキング、行レベルのセキュリティ、脅威検出など、多くのセキュリティ機能がサポートされています。

### <a name="solution-review"></a>ソリューションのレビュー

Contoso は、次の表のように長所と短所の一覧をまとめて、提案されたデザインを評価します。

| 考慮事項 | 詳細 |
| --- | --- |
| **長所** | Azure に移行するために SmartHotel360 アプリケーション コードを変更する必要はありません。 <br><br> Contoso では、SQL Server と Windows Server の両方に Azure ハイブリッド特典を使用して、ソフトウェア アシュアランスへの投資を活かすことができます。 <br><br> 移行後は、Windows Server 2008 R2 をサポートする必要がなくなります。 詳細については、「[Microsoft ライフサイクル ポリシー](/lifecycle/)」を参照してください。 <br><br> Contoso では、複数のインスタンスを保持するアプリケーションの Web 層を構成できるので、Web 層は単一障害点ではなくなります。 <br><br> データベースは古い SQL Server 2008 R2 に依存しなくなります。 <br><br> SQL Managed Instance では、Contoso の技術面の要件と目標がサポートされています。 <br><br> SQL マネージド インスタンスでは、SQL Server 2008 R2 から移行するときに、現在のデプロイとの 100% の互換性が提供されます。 <br><br> Contoso は、SQL Server と Windows Server の Azure ハイブリッド特典を使用して、ソフトウェア アシュアランスへの投資を活かすことができます。 <br><br> 将来の移行で、Azure Database Migration Service を再利用できます。 <br><br> SQL マネージド インスタンスには、Contoso が構成する必要のないフォールト トレランスが組み込まれています。 そのため、データ層がフェールオーバーの単一ポイントではなくなります。 |
| **短所** | Azure App Service でサポートされるアプリケーションのデプロイは、各 Web アプリにつき 1 つだけです。 これは、2 つの Web アプリをプロビジョニングする必要がある (Web サイト用に 1 つと WCF サービス用に 1 つ) ことを意味します。 <br><br> データ層について、Contoso がオペレーティング システムまたはデータベース サーバーをカスタマイズしたい場合、または SQL Server と共にサードパーティ製アプリケーションを実行したい場合、SQL Managed Instance は最適なソリューションではない可能性があります。 IaaS VM で SQL Server を実行すると、このような柔軟性が提供されます。 |

## <a name="proposed-architecture"></a>提案されたアーキテクチャ

![提案されたアーキテクチャの図。](./media/contoso-migration-refactor-web-app-sql-managed-instance/architecture.png)

### <a name="migration-process"></a>移行プロセス

1. Contoso は、Azure SQL マネージド インスタンスをプロビジョニングし、Azure Database Migration Service を使用して SmartHotel360 データベースをそれに移行します。
1. Contoso では Web アプリをプロビジョニングして構成し、それらに SmartHotel360 アプリケーションをデプロイします。

    ![移行プロセスの図。](./media/contoso-migration-refactor-web-app-sql-managed-instance/migration-process.png)

### <a name="azure-services"></a>Azure サービス

| サービス | 説明 | コスト |
| --- | --- | --- |
| [Azure App Service Migration Assistant](/learn/paths/migrate-dotnet-apps-azure/) | コードをほとんど変更することなく、オンプレミスからクラウドに .NET Web アプリをシームレスに移行するための、無料のシンプルなパスです。 | このツールは無料でダウンロードできます。 |
| [Azure Database Migration Service](/azure/dms/dms-overview) | Azure Database Migration Service を使用すると、複数のデータベース ソースから Azure データ プラットフォームに、ダウンタイムを最小限に抑えながらシームレスに移行できます。 | [サポートされているリージョン](/azure/dms/dms-overview#regional-availability)と [Azure Database Migration Service の価格](https://azure.microsoft.com/pricing/details/database-migration/)に関する情報をご覧ください。 |
| [Azure SQL Managed Instance](/azure/azure-sql/managed-instance/sql-managed-instance-paas-overview) | SQL Managed Instance は、Azure 内のフル マネージド SQL Server インスタンスを表すマネージド データベース サービスです。 最新バージョンの SQL Server データベース エンジンと同じコードを使用し、最新の機能、パフォーマンスの向上、およびセキュリティ更新プログラムが適用されています。 | Azure で実行されている SQL マネージド インスタンスを使用すると、容量に基づく料金がかかります。 詳細については、[SQL Managed Instance の価格](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/)に関するページを参照してください。 |
| [Azure App Service](/azure/app-service/overview) | フル マネージド プラットフォームを使用した強力なクラウド アプリケーションを作成できます。 | 価格は、サイズ、場所、使用時間に基づきます。 [詳細については、こちらを参照してください](https://azure.microsoft.com/pricing/details/app-service/windows/)。 |
| [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines) | アプリケーション開発のための継続的インテグレーションと継続的デプロイ (CI/CD) パイプラインが用意されています。 パイプラインは、アプリケーション コードを管理するための Git リポジトリ、パッケージやその他のビルド成果物を生成するためのビルド システム、開発、テスト、および運用環境で変更をデプロイするためのリリース管理システムから始まります。 |

## <a name="prerequisites"></a>前提条件

このシナリオを実行するには、Contoso は次の前提条件を満たす必要があります。

| 必要条件 | 詳細 |
| --- | --- |
| **Azure サブスクリプション** | Contoso は、この記事シリーズの前の記事でサブスクリプションを作成しました。 Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/free/)を作成してください。 <br><br> 無料アカウントを作成する場合、サブスクリプションの管理者としてすべてのアクションを実行できます。 <br><br> 既存のサブスクリプションを使用しており、管理者でない場合は、管理者に依頼して所有者アクセス許可または共同作成者アクセス許可を割り当ててもらう必要があります。 |
| **Azure インフラストラクチャ** | Contoso は、[移行のための Azure インフラストラクチャ](./contoso-migration-infrastructure.md)についての記事で説明されているように、Azure インフラストラクチャを設定します。 |

## <a name="scenario-steps"></a>シナリオのステップ

Contoso が移行を実行する方法を次に示します。

> [!div class="checklist"]
>
> - **ステップ 1:Web アプリの評価と移行**。 Contoso では、[Azure App Service Migration Assistant](https://azure.microsoft.com/migration/web-applications/) ツールを使用して、移行前互換性チェックを実行し、Web アプリを Azure App Service に移行します。
> - **手順 2:SQL マネージド インスタンスを設定する**。 Contoso では、オンプレミス SQL Server データベースの移行先となる既存のマネージド インスタンスが必要です。
> - **ステップ 3:Azure Database Migration Service を使用して移行する**。 Contoso は Azure Database Migration Service を使用してアプリケーション データベースを移行します。
> - **手順 4:Azure DevOps を設定する**。 Contoso は新しい Azure DevOps プロジェクトを作成し、Git リポジトリをインポートします。
> - **手順 5:接続文字列を構成する**。 Contoso は Web 層 Web アプリ、WCF サービス Web アプリ、SQL マネージド インスタンスが通信できるように接続文字列を構成します。
> - **手順 6:ビルドとリリース パイプラインを Azure DevOps で設定する**。 最後の手順として、Contoso はアプリケーションを作成するためのビルドとリリース パイプラインを Azure DevOps で設定します。 その後、それらのパイプラインを 2 つの個別の Web アプリにデプロイします。

## <a name="step-1-assess-and-migrate-the-web-apps"></a>手順 1:Web アプリの評価と移行

Contoso 管理者は、[Azure App Service Migration Assistant](https://azure.microsoft.com/migration/web-applications/) ツールを使用して、Web アプリを評価し、移行します。 このプロセスでは、[Microsoft ラーニング パス](/learn/paths/migrate-dotnet-apps-azure/)をガイドとして使用します。 簡単に言えば、管理者は次の操作を実行します。

- Azure [App Service 移行評価](https://azure.microsoft.com/services/app-service/migration-assistant/)ツールを使用して、Web アプリ間の依存関係を評価し、オンプレミスの Web アプリと Azure App Service でサポートされているものの間に非互換性がないかどうかを判断します。

- Azure App Service Migration Assistant をダウンロードし、Azure アカウントにサインインします。

- サブスクリプション、リソース グループ、Web サイトのドメイン名を選択します。

## <a name="step-2-set-up-a-sql-managed-instance"></a>手順 2:SQL マネージド インスタンスを設定する

Azure SQL マネージド インスタンスを設定するため、Contoso は次の要件を満たすサブネットを必要としています。

- サブネットは専用でなければなりません。 また、サブネットは空で他のクラウド サービスを含んでいない必要があります。 サブネットはゲートウェイ サブネットであってはなりません。
- マネージド インスタンスを作成した後、Contoso は、サブネットにリソースを追加することはできません。
- サブネットにネットワーク セキュリティ グループを関連付けることはできません。
- サブネットにはユーザー定義のルート テーブルが必要です。 割り当てられる唯一のルートが `0.0.0.0/0` の次ホップ インターネットである必要があります。
- 仮想ネットワーク用に省略可能なカスタム DNS が指定されている場合、Azure 内の再帰的なリゾルバーの仮想 IP アドレス `168.63.129.16` をリストに追加する必要があります。 [Azure SQL マネージド インスタンスのカスタム DNS を構成する](/azure/azure-sql/managed-instance/custom-dns-configure)方法を確認してください。
- サブネットにサービス エンドポイント (ストレージまたは SQL) を関連付けることはできません。 仮想ネットワークではサービス エンドポイントを無効にする必要があります。
- サブネットには 16 個以上の IP アドレスが必要です。 [マネージド インスタンス サブネットのサイズを指定する](/azure/azure-sql/managed-instance/vnet-existing-add-subnet)方法を確認してください。
- Contoso のハイブリッド環境では、カスタム DNS 設定が必要です。 Contoso は、自社の 1 つ以上の Azure DNS サーバーを使用するように DNS 設定を構成します。 [DNS のカスタマイズの詳細については、こちらを参照してください](/azure/azure-sql/managed-instance/custom-dns-configure)。

### <a name="set-up-a-virtual-network-for-the-managed-instance"></a>マネージド インスタンス用の仮想ネットワークを設定する

Contoso の管理者は仮想ネットワークを次のように設定します。

1. プライマリ リージョン (`East US 2`) に新しい仮想ネットワーク (`VNET-SQLMI-EU2`) を作成します。 これにより、仮想ネットワークが `ContosoNetworkingRG` リソース グループに追加されます。
1. `10.235.0.0/24` のアドレス空間を割り当てます。 その範囲がエンタープライズ内の他のどのネットワークとも重複しないことを確認します。
1. ネットワークに以下の 2 つのサブネットを追加します。
    - `SQLMI-DS-EUS2` (`10.235.0.0/25`).
    - `SQLMI-SAW-EUS2` (`10.235.0.128/29`). このサブネットは、マネージド インスタンスにディレクトリを接続するために使用されます。

      ![マネージド インスタンスの **[仮想ネットワークの作成]** ペインのスクリーンショット。](./media/contoso-migration-rehost-vm-sql-managed-instance/mi-vnet.png)

1. 仮想ネットワークとサブネットがデプロイされた後、ネットワークを以下のようにピアリングします。

    - `VNET-SQLMI-EUS2` を `VNET-HUB-EUS2` (`East US 2` 用のハブの仮想ネットワーク) とピアリングします。

    - `VNET-SQLMI-EUS2` を `VNET-PROD-EUS2` (運用ネットワーク) とピアリングします。

      ![ピアリングされたネットワークのスクリーンショット。](./media/contoso-migration-rehost-vm-sql-managed-instance/mi-peering.png)

1. Contoso は、カスタム DNS 設定を行います。 DNS 設定は、最初は Contoso の Azure ドメイン コント ローラーを指しています。 Azure DNS はセカンダリです。 Contoso Azure ドメイン コントローラーは、以下のように配置されます。

    - `East US 2` リージョンの運用ネットワーク (`VNET-PROD-EUS2`) の `PROD-DC-EUS2` サブネットにあります。

    - `CONTOSODC3` アドレス: `10.245.42.4`

    - `CONTOSODC4` アドレス: `10.245.42.5`

    - Azure DNS リゾルバー: `168.63.129.16`

    ![ネットワーク DNS サーバー リストのスクリーンショット。](./media/contoso-migration-rehost-vm-sql-managed-instance/mi-dns.png)

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

Contoso の管理者は、次のようにしてルーティングを設定します。

1. ユーザー定義のルート テーブルを `ContosoNetworkingRG` リソース グループに作成します。

    ![**[ルート テーブルの作成]** ペインのスクリーンショット。](./media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table.png)

1. SQL Managed Instance の要件に準拠するため、管理者は、ルート テーブル (`MIRouteTable`) のデプロイ後、アドレス プレフィックスが `0.0.0.0/0` のルートを追加します。 **[次ホップの種類]** オプションは **[インターネット]** に設定します。

    ![アドレス プレフィックスを追加するための **[ルートの追加]** ペインのスクリーンショット。](./media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table-prefix.png)

1. ルート テーブルを `SQLMI-DB-EUS2` サブネット (`VNET-SQLMI-EUS2` ネットワーク内) に関連付けます。

    ![サブネットにテーブルをルーティングするための **[サブネットの関連付け]** ペインのスクリーンショット。](./media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table-subnet.png)

**さらにサポートが必要な場合**

[マネージド インスタンス用のルートを設定する方法については、こちらを参照してください](/azure/azure-sql/managed-instance/instance-create-quickstart)。

### <a name="create-a-managed-instance"></a>マネージド インスタンスを作成する

これで、Contoso の管理者は、次のようにして SQL マネージド インスタンスをプロビジョニングできます。

1. マネージド インスタンスにより、ビジネス アプリケーションにサービスが提供されるため、管理者は、会社のプライマリ リージョン (米国東部 2) にマネージド インスタンスをデプロイします。 マネージド インスタンスを `ContosoRG` リソース グループに追加します。
1. 価格レベルを選択し、インスタンスのコンピューティングとストレージのサイズを設定します。 詳細については、[SQL Managed Instance の価格](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/)に関するページを参照してください。

    ![**[SQL Managed Instance]** ペインのスクリーンショット。](./media/contoso-migration-rehost-vm-sql-managed-instance/mi-create.png)

    マネージド インスタンスをデプロイすると、`ContosoRG` リソース グループ内に 2 つの新しいリソースが表示されます。

    - SQL マネージド インスタンス。

    - 仮想クラスター (複数のマネージド インスタンスが存在する場合)。

      ![ContosoRG リソース グループの新しいリソースのスクリーンショット。](./media/contoso-migration-rehost-vm-sql-managed-instance/mi-resources.png)

**さらにサポートが必要な場合**

[マネージド インスタンスをプロビジョニングする方法については、こちらを参照してください](/azure/azure-sql/managed-instance/instance-create-quickstart)。

## <a name="step-3-migrate-via-azure-database-migration-service"></a>手順 3:Azure Database Migration Service を使用して移行する

Contoso の管理者は、[ステップバイステップの移行チュートリアル](/azure/dms/tutorial-sql-server-to-azure-sql)の手順に従って、Azure Database Migration Service を使用してマネージド インスタンスを移行します。 オンライン、オフライン、およびハイブリッド (プレビュー) の移行を実行できます。

Contoso の管理者が行う作業の概要は次のとおりです。

- 仮想ネットワークに接続されている Premium SKU を使用して、Azure Database Migration Service インスタンスを作成します。
- Database Migration Service から、仮想ネットワーク経由で確実にリモート SQL Server にアクセスできるようにします。 これには、仮想ネットワーク レベル、ネットワーク VPN、および SQL Server をホストするマシンで、 Azure から SQL サーバーにすべての受信ポートが許可されていることの確認が伴います。
- Azure Database Migration Service を構成します。
  - 移行プロジェクトを作成します。
  - ソース (オンプレミス データベース) を追加します。
  - ターゲットを選択します。
  - 移行するデータベースを選択します。
  - 詳細設定を構成します。
  - レプリケーション を開始します。
  - すべてのエラーを解決します。
  - 最終的なカットオーバーを実行します。

## <a name="step-4-set-up-azure-devops"></a>手順 4:Azure DevOps を設定する

Contoso は、アプリケーションのために DevOps インフラストラクチャとパイプラインを構築する必要があります。 これを行うために、Contoso 管理者は新しい DevOps プロジェクトを作成し、コードをインポートしてから、ビルドとリリース パイプラインを設定します。

1. Contoso Azure DevOps アカウントで、新しいプロジェクト (`ContosoSmartHotelRefactor`) を作成し、バージョン コントロールに **[Git]** を選択します。

    ![プロジェクトの新規作成ペインのスクリーンショット。](./media/contoso-migration-refactor-web-app-sql-managed-instance/vsts1.png)

1. アプリケーション コードを現在保持している Git リポジトリをインポートします。 ダウンロードは、[パブリック GitHub リポジトリ](https://github.com/Microsoft/SmartHotel360-Registration)から行います。

    ![ソースの種類とクローン URL を指定するための **[Git リポジトリをインポートする]** ペインのスクリーンショット。](./media/contoso-migration-refactor-web-app-sql-managed-instance/vsts2.png)

1. Visual Studio をリポジトリに接続し、チーム エクスプローラーを使用して開発用マシンにコードを複製します。

    ![**[プロジェクトに接続]** ペインのスクリーンショット。](./media/contoso-migration-refactor-web-app-sql-managed-instance/devops1.png)

1. アプリケーションのソリューション ファイルを開きます。 このファイルには、Web アプリと WCF サービスのプロジェクトが別々に存在します。

    ![Web アプリと WCF サービスのプロジェクトを一覧表示するソリューション エクスプローラーのスクリーンショット。](./media/contoso-migration-refactor-web-app-sql-managed-instance/vsts4.png)

## <a name="step-5-configure-connection-strings"></a>手順 5:接続文字列の構成

Contoso の管理者は、Web アプリとデータベースが互いに通信できるようにします。 そのために、コードと Web アプリで接続文字列を構成します。

1. WCF サービス (`SHWCF-EUS2`) の Web アプリで、 **[設定]**  >  **[アプリケーションの設定]** の順に選択し、`DefaultConnection` という名前の新しい接続文字列を追加します。
1. 接続文字列は SmartHotel-Registration データベースからプルし、正しい資格情報を使用して更新します。

    ![接続文字列の設定ペインのスクリーンショット。](./media/contoso-migration-refactor-web-app-sql-managed-instance/string1.png)

1. Visual Studio で、管理者がソリューション ファイルから `SmartHotel.Registration.wcf` プロジェクトを開きます。 このプロジェクトの `web.config` ファイルの `connectionStrings` セクションを接続文字列で更新します。

     ![`SmartHotel.Registration.wcf` プロジェクトの `web.config` ファイルの `connectionStrings` セクションのスクリーンショット。](./media/contoso-migration-refactor-web-app-sql-managed-instance/string2.png)

1. `SmartHotel.Registration.Web` の `web.config` ファイルの `client` セクションを、WCF サービスの新しい場所を指すように変更します。 これは、サービス エンドポイントをホストする WCF Web アプリの URL です。

    ![`SmartHotel.Registration.wcf` プロジェクトの `web.config` ファイルの client セクションのスクリーンショット。](./media/contoso-migration-refactor-web-app-sql-managed-instance/strings3.png)

1. コードの変更は以上で完了です。管理者は、Visual Studio のチーム エクスプローラーを使用して、変更のコミットと同期を行います。

## <a name="step-6-set-up-build-and-release-pipelines-in-azure-devops"></a>手順 6:ビルドとリリース パイプラインを Azure DevOps で設定する

次に、Contoso 管理者は、Azure DevOps を構成して、ビルドおよびリリース プロセスを実行します。

1. Azure DevOps 内で、 **[Build and release]\(ビルドとリリース\)**  >  **[新しいパイプライン]** の順に選択します。

    ![Azure DevOps の **[新しいパイプライン]** リンクのスクリーンショット。](./media/contoso-migration-refactor-web-app-sql-managed-instance/pipeline1.png)

1. **[Azure Repos Git]** を選択し、関連するリポジトリを選択します。

    ![**[Azure Repos Git]** ボタンと選択されたリポジトリのスクリーンショット。](./media/contoso-migration-refactor-web-app-sql-managed-instance/pipeline2.png)

1. **[テンプレートの選択]** で、ビルド用の ASP.NET テンプレートを選択します。

     ![ASP.NET テンプレートを選択するための **[テンプレートの選択]** ペインのスクリーンショット。](./media/contoso-migration-refactor-web-app-sql-managed-instance/pipeline3.png)

1. ビルドの名前に `ContosoSmartHotelRefactor-ASP.NET-CI` を使用し、 **[保存してキューに登録]** を選択して、最初のビルドを開始します。

     ![ビルドの **[保存してキューに登録]** ボタンのスクリーンショット。](./media/contoso-migration-refactor-web-app-sql-managed-instance/pipeline4.png)

1. プロセスを監視するビルド番号を選択します。 完了後、管理者はプロセスのフィードバックを表示し、 **[成果物]** を選択してビルドの結果を確認できます。

    ![[ビルド] ページとビルド結果を確認するための [成果物] リンクのスクリーンショット。](./media/contoso-migration-refactor-web-app-sql-managed-instance/pipeline5.png)

    **[成果物エクスプローラー]** ペインが開き、**drop** フォルダーにビルド結果が表示されます。

    - 2 つの .zip ファイルは、アプリケーションが格納されているパッケージです。
    - これらの .zip ファイルは、Azure App Service にデプロイするためにリリース パイプライン内で使用されます。

     ![**[成果物エクスプローラー]** ペインのスクリーンショット。](./media/contoso-migration-refactor-web-app-sql-managed-instance/pipeline6.png)

1. **[リリース]**  >  **[+ New pipeline]\(+ 新しいパイプライン\)** の順に選択します。

    ![**[新しいパイプライン]** リンクのスクリーンショット。](./media/contoso-migration-refactor-web-app-sql-managed-instance/pipeline7.png)

1. Azure App Service 用のデプロイ テンプレートを選択します。

    ![Azure App Service のデプロイ テンプレートのスクリーンショット。](./media/contoso-migration-refactor-web-app-sql-managed-instance/pipeline8.png)

1. リリース パイプラインに `ContosoSmartHotel360Refactor` という名前を付け、 **[ステージ名]** ボックスに、WCF Web アプリの名前として「`SHWCF-EUS2`」を指定します。

    ![WCF Web アプリのステージ名のスクリーンショット。](./media/contoso-migration-refactor-web-app-sql-managed-instance/pipeline9.png)

1. ステージ下で、 **[1 個のジョブ、1 個のタスク]** を選択して WCF サービスのデプロイを構成します。

    ![**[1 個のジョブ、1 個のタスク]** オプションのスクリーンショット。](./media/contoso-migration-refactor-web-app-sql-managed-instance/pipeline10.png)

1. サブスクリプションが選択および承認されていることを確認し、 **[App Service の名前]** を選択します。

     ![App Service の名前を選択する画面のスクリーンショット。](./media/contoso-migration-refactor-web-app-sql-managed-instance/pipeline11.png)

1. パイプラインで **[成果物]** 、 **[+ 成果物の追加]** の順に選択し、ソースの種類に **[ビルド]** を選択して、`ContosoSmarthotel360Refactor` パイプラインを使用してビルドします。

     ![**[成果物の追加]** ペインの [ビルド] ボタンのスクリーンショット。](./media/contoso-migration-refactor-web-app-sql-managed-instance/pipeline12.png)

1. 継続的デプロイ トリガーを有効にするために、管理者が成果物の稲妻アイコンを選択します。

     ![成果物の稲妻アイコンのスクリーンショット。](./media/contoso-migration-refactor-web-app-sql-managed-instance/pipeline13.png)

1. 継続的デプロイ トリガーを **[有効]** に設定します。

    ![継続的デプロイ トリガーを [有効] に設定した画面のスクリーンショット。](./media/contoso-migration-refactor-web-app-sql-managed-instance/pipeline14.png)

1. 管理者は、ステージ **[1 個のジョブ、1 個のタスク]** に戻り、 **[Deploy Azure App Service]\(Azure App Service のデプロイ\)** を選択します。

    ![**[Deploy Azure App Service]\(Azure App Service のデプロイ\)** を選択するオプションのスクリーンショット。](./media/contoso-migration-refactor-web-app-sql-managed-instance/pipeline15.png)

1. **[Select a file or folder]\(ファイルまたはフォルダーの選択\)** で **drop** フォルダーを展開し、ビルド中に作成された `SmartHotel.Registration.Wcf.zip` ファイルを選択して、 **[保存]** を選択します。

    ![WCF ファイルを選択するための **[Select a file or folder]\(ファイルまたはフォルダーの選択\)** ペインのスクリーンショット。](./media/contoso-migration-refactor-web-app-sql-managed-instance/pipeline16.png)

1. **[パイプライン]**  >  **[ステージ]** の順に選択し、 **[+ 追加]** を選択して、`SHWEB-EUS2` 用の環境を追加します。 別の Azure App Service のデプロイを選択します。

    ![環境を追加するための **[1 個のジョブ、1 個のタスク]** リンクのスクリーンショット。](./media/contoso-migration-refactor-web-app-sql-managed-instance/pipeline17.png)

1. このプロセスを繰り返して Web アプリの `SmartHotel.Registration.Web.zip` ファイルを適切な Web アプリに発行し、 **[保存]** を選択します。

    ![Web ファイルを選択するための **[Select a file or folder]\(ファイルまたはフォルダーの選択\)** ペインのスクリーンショット。](./media/contoso-migration-refactor-web-app-sql-managed-instance/pipeline18.png)

    次のリリース パイプラインが表示されます。

     ![リリース パイプラインの概要のスクリーンショット。](./media/contoso-migration-refactor-web-app-sql-managed-instance/pipeline19.png)

1. **[ビルド]** に戻り、 **[トリガー]** を選択して、 **[継続的インテグレーションを有効にする]** チェック ボックスをオンにします。 この操作でパイプラインが有効になり、変更がコードに対してコミットされると、フル ビルドとリリースが行われます。

    ![**[継続的インテグレーションを有効にする]** チェック ボックスを強調表示した画面のスクリーンショット。](./media/contoso-migration-refactor-web-app-sql-managed-instance/pipeline20.png)

1. **[保存してキューに登録]** を選択し、フル パイプラインを実行します。 新しいビルドがトリガーされ、続いてアプリケーションの最初のリリースが Azure App Service に対して作成されます。

    ![**[保存してキューに登録]** ボタンのスクリーンショット。](./media/contoso-migration-refactor-web-app-sql-managed-instance/pipeline21.png)

1. Contoso 管理者は、Azure DevOps からビルドとリリース パイプラインのプロセスに従うことができます。 ビルドの完了後、リリースが開始されます。

    ![アプリのビルドとリリースのスクリーンショット。](./media/contoso-migration-refactor-web-app-sql-managed-instance/pipeline22.png)

1. パイプラインが完了すると、両方のサイトがデプロイされ、アプリケーションがオンラインで稼働します。

    ![アプリケーションが稼動していることを示す画面のスクリーンショット。](./media/contoso-migration-refactor-web-app-sql-managed-instance/pipeline23.png)

    アプリケーションが Azure に正常に移行されました。

## <a name="clean-up-after-the-migration"></a>移行後にクリーンアップする

移行後、Contoso チームは、以下のクリーンアップ手順を実行します。

- vCenter のインベントリからオンプレミスの VM を削除します。
- ローカルのバックアップ ジョブから VM を削除します。
- SmartHotel360 アプリケーションの新しい場所を示すように社内ドキュメントを更新します。 データベースは SQL マネージド インスタンスで、またフロントエンドは 2 つの Web アプリで実行されていることが、このドキュメントには示されます。
- 使用停止されている VM と対話しているリソースがないかを確認します。また、関連する設定やドキュメントがあれば、更新して新しい構成を反映します。

## <a name="review-the-deployment"></a>デプロイを再調査する

リソースを Azure に移行したら、新しいインフラストラクチャを完全に運用可能な状態にすると共に、そのセキュリティの保護に努める必要があります。

### <a name="security"></a>セキュリティ

- Contoso は新しい `SmartHotel-Registration` データベースのセキュリティ保護に努めます。 [詳細については、こちらを参照してください](/azure/azure-sql/database/security-overview)。
- 特に、Contoso は証明書で SSL を使用するように Web アプリを更新します。

### <a name="backups"></a>バックアップ

- Contoso チームは、Azure SQL Managed Instance のデータベースのバックアップ要件を確認します。 [詳細については、こちらを参照してください](/azure/azure-sql/database/automated-backups-overview)。
- また、SQL Database のバックアップと復元の管理について確認します。 [自動バックアップ](/azure/azure-sql/database/automated-backups-overview)の詳細をご覧ください。
- データベースのリージョン内フェールオーバーを提供するようにフェールオーバー グループの実装を検討します。 [詳細については、こちらを参照してください](/azure/azure-sql/database/auto-failover-group-overview)。
- 回復性を高めるために、メイン リージョン (`East US 2`) とセカンダリ リージョン (`Central US`) に Web アプリをデプロイすることを検討します。 チームはリージョンの障害が発生しても確実にフェールオーバーされるように Traffic Manager を構成することができます。

### <a name="licensing-and-cost-optimization"></a>ライセンスとコストの最適化

- すべてのリソースをデプロイした後、Contoso は[インフラストラクチャ計画](./contoso-migration-infrastructure.md#set-up-tagging)に基づいて Azure タグを割り当てます。
- すべてのライセンスは、Contoso が使用している PaaS サービスのコストに組み込まれています。 このコストは Enterprise Agreement から差し引かれます。
- Contoso は [Azure Cost Management + Billing](/azure/cost-management-billing/cost-management-billing-overview) を使用して、IT リーダーが定めた予算内に確実に収まるようにします。

## <a name="conclusion"></a>まとめ

この記事の中で、Contoso は、アプリケーションのフロントエンド VM を 2 つの Azure App Service Web アプリに移行して、Azure に SmartHotel360 アプリケーションをリファクターしました。 アプリケーション データベースが Azure SQL マネージド インスタンスに移行されました。
