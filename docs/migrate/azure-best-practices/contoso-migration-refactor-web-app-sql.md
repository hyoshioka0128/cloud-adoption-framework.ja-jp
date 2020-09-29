---
title: Azure App Service と SQL Database にアプリケーションを移行する
description: Azure 向けのクラウド導入フレームワークを使用し、アプリを Azure App Service と Azure SQL Database に移行することでリファクターする方法について説明します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 07/01/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: migrate
ms.openlocfilehash: 013c21eaf964d09f07122c9b6deb1b1539558541
ms.sourcegitcommit: 4e12d2417f646c72abf9fa7959faebc3abee99d8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2020
ms.locfileid: "90775753"
---
<!-- cSpell:ignore WEBVM SQLVM contosohost vcenter contosodc smarthotel SHWEB SHWCF -->

# <a name="migrate-an-application-to-azure-app-service-and-sql-database"></a>Azure App Service と SQL Database にアプリケーションを移行する

この記事では、Contoso という架空の会社が、Azure への移行の一環として、VMware VM 上で実行される 2 層の Windows .NET アプリケーションをリファクターする方法を示します。 Contoso チームは、アプリケーションのフロントエンド仮想マシン (VM) を Azure App Service Web アプリに、またアプリケーション データベースを Azure SQL Database に移行します。

この例で使用する SmartHotel360 アプリケーションは、オープンソースとして提供されています。 独自のテスト目的に沿って使用する場合は、[GitHub](https://github.com/Microsoft/SmartHotel360) からダウンロードできます。

## <a name="business-drivers"></a>ビジネス ドライバー

Contoso の IT リーダーシップ チームは、ビジネス パートナーと密接に連絡を取り合い、彼らがこの移行で何を達成しようとしているのかを理解しました。

- **ビジネスの成長への対応**。 Contoso は成長を続けており、オンプレミスのシステムとインフラストラクチャに負荷がかかっています。
- **効率化**。 Contoso では、不要な手順を排除し、開発者とユーザーのプロセスを効率化する必要があります。 ビジネス部門は IT に対して、時間やコストを無駄にせず、迅速に作業を行ってもらう必要があります。これは、例えば、顧客の要求に素早く対応するためです。
- **機敏性の強化**。 Contoso IT は、グローバル経済における成功を実現するために、ビジネス部門の要求に対して、対応力を向上させる必要があります。 市場の変化に対して、より迅速に対応できなければなりません。 IT がビジネスの妨げになったり、ビジネスの機会を壊すようなことがあってはなりません。
- **スケール**。 ビジネスが順調に成長していく中で、Contoso IT は、同じペースで拡張できるシステムを提供する必要があります。
- **コストの削減**。 Contoso はライセンス コストを最小限に抑えたいと考えています。

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
- VMware 環境は、VM 上で実行されている vCenter Server 6.5 (vcenter.contoso.com) によって管理されています。
- Contoso にオンプレミスのデータセンター (contoso-datacenter) があり、そこにオンプレミスのドメイン コントローラー (contosodc1) が含まれています。
- Contoso データセンター内のオンプレミス VM は、移行が行われた後に使用停止にされます。

### <a name="proposed-solution"></a>提案されるソリューション

- このアプリケーションのデータベース層について、Contoso は「[機能の比較: Azure SQL Database と Azure SQL Managed Instance](/azure/sql-database/sql-database-features)」を参照して、Azure SQL Database と SQL Server を比較しました。 そして、いくつかの理由で Azure SQL Database を使用することにしました。
  - Azure SQL Database は、マネージド リレーショナル データベース サービスです。 複数のサービス レベルで予測可能なパフォーマンスを実現します。管理作業はほぼゼロです。 利点としては、ダウンタイムのない動的スケーラビリティ、組み込みのインテリジェントな最適化、グローバルなスケーラビリティと可用性などがあります。
  - Contoso では軽量な Data Migration Assistant を利用して、Azure SQL へのオンプレミス データベースの移行を評価することができます。
  - Contoso では Azure Database Migration Service を利用して、Azure SQL にオンプレミス データベースを移行できます。
  - ソフトウェア アシュアランスにより、Contoso は、SQL Server 用の Azure ハイブリッド特典を使用して、既存のライセンスと引き換えに、SQL Database のデータベースを割引料金で利用することができます。 この方法なら最大 30% のコスト削減が可能です。
  - SQL Database では、Always Encrypted、動的データ マスキング、行レベルのセキュリティ、SQL 脅威検出などのセキュリティ機能を提供しています。
- Contoso では、アプリケーションの Web 層については、Azure App Service を使用することに決めました。 この PaaS サービスを使用すると、わずかな構成変更だけでアプリケーションをデプロイできます。 Contoso は Visual Studio を使用して変更を加え、Web サイト用と WCF サービス用の 2 つの Web アプリをデプロイします。
- DevOps パイプラインの要件を満たすために、Contoso は Git リポジトリと共に Azure DevOps のソース コード管理を使用します。 コードをビルドして Azure App Service にデプロイするには、自動ビルドとリリースが使用されます。

### <a name="solution-review"></a>ソリューションのレビュー

Contoso は、次の表のように長所と短所の一覧をまとめて、提案されたデザインを評価します。

| 考慮事項 | 詳細 |
| --- | --- |
| **長所** | Azure に移行するために SmartHotel360 アプリケーション コードを変更する必要はありません。 <br><br> Contoso では、SQL Server と Windows Server の両方に Azure ハイブリッド特典を使用して、ソフトウェア アシュアランスへの投資を活かすことができます。 <br><br> 移行後は、Windows Server 2008 R2 をサポートする必要がなくなります。 詳細については、「[Microsoft ライフサイクル ポリシー](https://aka.ms/lifecycle)」を参照してください。 <br><br> Contoso では、複数のインスタンスを保持するアプリケーションの Web 層を構成できるので、Web 層は単一障害点ではなくなります。 <br><br> データベースは古い SQL Server 2008 R2 に依存しなくなります。 <br><br> SQL Database は技術面の要件をサポートしています。 Contoso で Data Migration Assistant を使用してオンプレミス データベースを評価したところ、互換性があることがわかりました。 <br><br> Azure SQL Database には、Contoso が設定する必要がないフォールト トレランス機能が組み込まれています。 そのため、データ層がフェールオーバーの単一ポイントではなくなります。 <br><br> Contoso が Azure Database Migration Service を使用してデータベースを移行する場合、規模に応じてデータベースを移行するためのインフラストラクチャが用意されます。 |
| **短所** | Azure App Service でサポートされるアプリケーションのデプロイは、各 Web アプリにつき 1 つだけです。 これは、2 つの Web アプリをプロビジョニングする必要がある (Web サイト用に 1 つと WCF サービス用に 1 つ) ことを意味します。 |

## <a name="proposed-architecture"></a>提案されたアーキテクチャ

![提案されたアーキテクチャの図。](./media/contoso-migration-refactor-web-app-sql/architecture.png)

### <a name="migration-process"></a>移行プロセス

1. Contoso は、Azure SQL マネージド インスタンスをプロビジョニングし、Azure Database Migration Service を使用して SmartHotel360 データベースをそれに移行します。
1. Contoso では Web アプリをプロビジョニングして構成し、そこに SmartHotel360 アプリケーションをデプロイします。

    ![移行プロセスの図。](./media/contoso-migration-refactor-web-app-sql/migration-process.png)

### <a name="azure-services"></a>Azure サービス

| サービス | 説明 | コスト |
| --- | --- | --- |
| [Azure App Service Migration Assistant](/learn/paths/migrate-dotnet-apps-azure/) | コードをほとんど変更することなく、オンプレミスからクラウドに .NET Web アプリをシームレスに移行するための、無料のシンプルなパスです。 | このツールは無料でダウンロードできます。 |
| [Data Migration Assistant](/sql/dma/dma-overview?view=ssdt-18vs2017) | Contoso は Data Migration Assistant を使用して、Azure でのデータベースの機能に影響を与える可能性のある互換性の問題を評価し、検出します。 Data Migration Assistant は SQL のソースとターゲット間で機能パリティを評価し、パフォーマンスと信頼性の向上箇所を推奨します。 | このツールは無料でダウンロードできます。 |
| [Azure Database Migration Service](/azure/dms/dms-overview) | Azure Database Migration Service を使用すると、複数のデータベース ソースから Azure データ プラットフォームに、ダウンタイムを最小限に抑えながらシームレスに移行できます。 | [サポートされているリージョン](/azure/dms/dms-overview#regional-availability)に関する情報と、[Database Migration Service の価格](https://azure.microsoft.com/pricing/details/database-migration)に関する情報をご覧ください。 |
| [Azure SQL Database](/azure/azure-sql/database/sql-database-paas-overview) | インテリジェントなフル マネージド リレーショナル クラウド データベース サービス。 | コストは、機能、スループット、サイズに基づきます。 [詳細については、こちらを参照してください](https://azure.microsoft.com/pricing/details/sql-database/managed)。 |
| [Azure App Service](/azure/app-service/overview) | フル マネージド プラットフォームを使用した強力なクラウド アプリケーションを作成できます。 | 価格は、サイズ、場所、使用時間に基づきます。 [詳細については、こちらを参照してください](https://azure.microsoft.com/pricing/details/app-service/windows)。 |
| [Azure DevOps](/azure/azure-portal/tutorial-azureportal-devops) | 継続的インテグレーションと継続的配置 (CI/CD) パイプラインをアプリケーション開発に提供します。 パイプラインは、アプリケーション コードを管理するための Git リポジトリ、パッケージやその他のビルド成果物を生成するためのビルド システム、開発、テスト、および運用環境で変更をデプロイするためのリリース管理システムから始まります。 |

## <a name="prerequisites"></a>前提条件

このシナリオを実行するには、Contoso は次の前提条件を満たす必要があります。

| 必要条件 | 詳細 |
| --- | --- |
| **Azure サブスクリプション** | Contoso は、この記事シリーズの前の記事でサブスクリプションを作成しました。 Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/free)を作成してください。 <br><br> 無料アカウントを作成する場合、サブスクリプションの管理者としてすべてのアクションを実行できます。 <br><br> 既存のサブスクリプションを使用しており、管理者でない場合は、管理者に依頼して所有者アクセス許可または共同作成者アクセス許可を割り当ててもらう必要があります。 |
| **Azure インフラストラクチャ** | Contoso は、[移行のための Azure インフラストラクチャ](./contoso-migration-infrastructure.md)についての記事で説明されているように、Azure インフラストラクチャを設定します。 |

## <a name="scenario-steps"></a>シナリオのステップ

Contoso が移行を実行する方法を次に示します。

> [!div class="checklist"]
>
> - **ステップ 1:Web アプリの評価と移行**。 Contoso では、[Azure App Service Migration Assistant](https://azure.microsoft.com/migration/web-applications/) ツールを使用して、移行前互換性チェックを実行し、Web アプリを Azure App Service に移行します。
> - **手順 2:Azure SQL Database にデータベースをプロビジョニングする**。 Contoso が Azure SQL データベース インスタンスをプロビジョニングします。 アプリケーションの Web サイトが Azure に移行されると、WCF サービス Web アプリはこのインスタンスを指します。
> - **ステップ 3:データベースを評価する**。 Contoso は、移行するデータベースを Data Migration Assistant を使用して評価し、Azure Database Migration Service を使用してそのデータベースを移行します。
> - **手順 4:Azure DevOps を設定する**。 Contoso は新しい Azure DevOps プロジェクトを作成し、Git リポジトリをインポートします。
> - **手順 5:接続文字列を構成する**。 Contoso は Web 層 Web アプリ、WCF サービス Web アプリ、および SQL インスタンスが通信できるように接続文字列を構成します。
> - **手順 6:ビルドとリリース パイプラインを Azure DevOps で設定する**。 最後の手順として、Contoso はアプリケーションを作成するためのビルドとリリース パイプラインを Azure DevOps で設定し、それらを 2 つの個別の Web アプリにデプロイします。

## <a name="step-1-assess-and-migrate-the-web-apps"></a>手順 1:Web アプリの評価と移行

Contoso 管理者は、[Azure App Service Migration Assistant](https://azure.microsoft.com/migration/web-applications/) ツールを使用して、Web アプリを評価し、移行します。 このプロセスでは、[ASP.NET アプリを Azure に移行するラーニング パス](/learn/paths/migrate-dotnet-apps-azure/)をガイドとして使用します。
管理者は、次のアクションを実行します。

- Azure [App Service 移行評価](https://appmigration.microsoft.com/assessment/)ツールを使用して、Web アプリ間の依存関係を評価し、オンプレミスの Web アプリと Azure App Service でサポートされているものの間に非互換性がないかどうかを判断します。

- Azure App Service Migration Assistant をダウンロードし、Azure アカウントにサインインします。

- サブスクリプション、リソース グループ、Web サイトのドメイン名を選択します。

## <a name="step-2-provision-a-database-in-azure-sql-database"></a>手順 2:Azure SQL Database にデータベースをプロビジョニングする

1. Contoso 管理者は、Azure SQL データベース インスタンスの作成を決めます。

    ![SQL Database リンクを示すスクリーンショット。](./media/contoso-migration-refactor-web-app-sql/provision-sql1.png)

1. オンプレミス VM 上で実行されているデータベース (`SmartHotel.Registration`) と一致するようにデータベース名を指定します。 ContosoRG リソース グループにデータベースを配置します。 これは、Azure の運用リソースに使用するリソース グループです。

    ![SQL データベース インスタンスの詳細を示すスクリーンショット。](./media/contoso-migration-refactor-web-app-sql/provision-sql2.png)

1. プライマリ リージョンに新しい SQL Server インスタンス (**sql-smarthotel-eus2**) を設定します。

    ![新しい SQL Server インスタンスのスクリーンショット。](./media/contoso-migration-refactor-web-app-sql/provision-sql3.png)

1. サーバーとデータベースのニーズに合わせて価格レベルを設定します。 既に SQL Server のライセンスを持っているため、Azure ハイブリッド特典で費用を節約することにします。
1. サイズ設定については、仮想コア ベースの購入を使用し、予想される要件の制限を設定します。

    ![仮想コア サイズ設定要件のスクリーンショット。](./media/contoso-migration-refactor-web-app-sql/provision-sql4.png)

1. データベース インスタンスを作成します。

    ![SQL データベース インスタンス作成のスクリーンショット。](./media/contoso-migration-refactor-web-app-sql/provision-sql5.png)

1. データベースを開き、Data Migration Assistant を移行に使用するときに必要な詳細情報を書き留めます。

    ![データベース インスタンスのテキスト ファイルのスクリーンショット。](./media/contoso-migration-refactor-web-app-sql/provision-sql6.png)

**さらにサポートが必要な場合**

- SQL Database のプロビジョニングの[手順を参照](/azure/sql-database/sql-database-get-started-portal)します。
- [仮想コア リソースの制限](/azure/sql-database/sql-database-vcore-resource-limits-elastic-pools)に関する説明を参照します。

## <a name="step-3-assess-the-database"></a>手順 3:データベースを評価する

Contoso の管理者は、Data Migration Assistant を使用してデータベースを評価した後、[ステップバイステップの移行チュートリアル](/azure/dms/tutorial-sql-server-azure-sql-online)を参照し、Azure Database Migration Service を使用してそれを移行します。 オンライン、オフライン、およびハイブリッド (プレビュー) の移行を実行できます。

管理者が行う作業の概要は次のとおりです。

- Data Migration Assistant を使用して、データベースの移行に関する問題を検出し、解決します。
- 仮想ネットワークに接続されている Premium SKU を使用して、Azure Database Migration Service インスタンスを作成します。
- そのインスタンスから仮想ネットワーク経由でリモート SQL Server に確実にアクセスできるようにします。 これには、仮想ネットワーク レベル、ネットワーク VPN、および SQL Server をホストするマシンで、Azure から SQL サーバーにすべての受信ポートが許可されていることの確認が伴います。
- 次のようにしてインスタンスを構成します。
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

1. Contoso Azure DevOps アカウントで、新しいプロジェクト (**ContosoSmartHotelRefactor**) を作成し、バージョン コントロールに **[Git]** を選択します。

    ![Azure DevOps で新しいプロジェクトを作成する画面のスクリーンショット。](./media/contoso-migration-refactor-web-app-sql/vsts1.png)

1. アプリケーション コードを現在保持している Git リポジトリをインポートします。 ダウンロードは、[パブリック GitHub リポジトリ](https://github.com/Microsoft/SmartHotel360-Registration)から行います。

    ![[Git リポジトリをインポートする] ペインのスクリーンショット。](./media/contoso-migration-refactor-web-app-sql/vsts2.png)

1. Visual Studio をリポジトリに接続し、チーム エクスプローラーを使用して開発用マシンにコードを複製します。

    ![[プロジェクトに接続] ペインのスクリーンショット。](./media/contoso-migration-refactor-web-app-sql/devops1.png)

1. アプリケーションのソリューション ファイルを開きます。 このファイルには、Web アプリのプロジェクトと WCF サービスのプロジェクトが別々に存在します。

    ![Web アプリと WCF サービスのプロジェクトを一覧表示するソリューション エクスプローラーのスクリーンショット。](./media/contoso-migration-refactor-web-app-sql/vsts4.png)

## <a name="step-5-configure-connection-strings"></a>手順 5:接続文字列の構成

Contoso の管理者は、Web アプリとデータベースが互いに通信できるようにします。 そのために、コードと Web アプリで接続文字列を構成します。

1. WCF サービス (`SHWCF-EUS2`) の Web アプリで、 **[設定]**  >  **[アプリケーションの設定]** の順に選択し、**DefaultConnection** という名前の新しい接続文字列を追加します。
1. 接続文字列は SmartHotel-Registration データベースからプルし、正しい資格情報を使用して更新します。

    ![接続文字列の設定ペインのスクリーンショット。](./media/contoso-migration-refactor-web-app-sql/string1.png)

1. Visual Studio で、管理者がソリューション ファイルから `SmartHotel.Registration.wcf` プロジェクトを開きます。 このプロジェクトの `web.config` ファイルの `connectionStrings` セクションを接続文字列で更新します。

     ![SmartHotel.Registration.wcf プロジェクトの web.config ファイルの connectionStrings セクションのスクリーンショット。](./media/contoso-migration-refactor-web-app-sql/string2.png)

1. `SmartHotel.Registration.Web` の `web.config` ファイルの `client` セクションを、WCF サービスの新しい場所を指すように変更します。 これは、サービス エンドポイントをホストする WCF Web アプリの URL です。

    ![SmartHotel.Registration.wcf プロジェクトの web.config ファイルの client セクションのスクリーンショット。](./media/contoso-migration-refactor-web-app-sql/strings3.png)

1. コードの変更は以上で完了です。管理者は、Visual Studio のチーム エクスプローラーを使用して、変更のコミットと同期を行います。

## <a name="step-6-set-up-build-and-release-pipelines-in-azure-devops"></a>手順 6:ビルドとリリース パイプラインを Azure DevOps で設定する

次に、Contoso 管理者は、Azure DevOps を構成して、ビルドおよびリリース プロセスを実行します。

1. Azure DevOps 内で、 **[Build and release]\(ビルドとリリース\)**  >  **[新しいパイプライン]** の順に選択します。

    ![Azure DevOps の [新しいパイプライン] リンクのスクリーンショット。](./media/contoso-migration-refactor-web-app-sql/pipeline1.png)

1. **[Azure Repos Git]** を選択し、 **[リポジトリ]** ボックスの一覧から適切なリポジトリを選択します。

    ![[Azure Repos Git] ボタンと選択されたリポジトリのスクリーンショット。](./media/contoso-migration-refactor-web-app-sql/pipeline2.png)

1. **[テンプレートの選択]** で、ビルド用の **ASP.NET** テンプレートを選択します。

     ![ASP.NET テンプレートを選択するための [テンプレートの選択] ペインのスクリーンショット。](./media/contoso-migration-refactor-web-app-sql/pipeline3.png)

1. ビルドの名前に **ContosoSmartHotelRefactor-ASP.NET-CI** を使用し、 **[保存してキューに登録]** を選択して、最初のビルドを開始します。

     ![ビルドの [保存してキューに登録] ボタンのスクリーンショット。](./media/contoso-migration-refactor-web-app-sql/pipeline4.png)

1. プロセスを監視するビルド番号を選択します。 完了後、管理者はプロセスのフィードバックを表示し、 **[成果物]** を選択してビルドの結果を確認できます。

    ![[ビルド] ページとビルド結果を確認するための [成果物] リンクのスクリーンショット。](./media/contoso-migration-refactor-web-app-sql/pipeline5.png)

    **[成果物エクスプローラー]** ペインが開き、**drop** フォルダーにビルド結果が表示されます。

    - 2 つの .zip ファイルは、アプリケーションが格納されているパッケージです。
    - これらの .zip ファイルは、Azure App Service にデプロイするためにリリース パイプライン内で使用されます。

     ![[成果物エクスプローラー] ペインのスクリーンショット。](./media/contoso-migration-refactor-web-app-sql-managed-instance/pipeline6.png)

1. **[リリース]**  >  **[+ 新しいパイプライン]** を選択します。

    ![新しいパイプライン](./media/contoso-migration-refactor-web-app-sql/pipeline7.png)

1. Azure App Service 用のデプロイ テンプレートを選択します。

    ![Azure App Service のデプロイ テンプレートのスクリーンショット。](./media/contoso-migration-refactor-web-app-sql/pipeline8.png)

1. リリース パイプラインに **ContosoSmartHotel360Refactor** という名前を付け、 **[ステージ名]** ボックスに、WCF Web アプリの名前として「**SHWCF-EUS2**」を指定します。

    ![WCF Web アプリのステージ名のスクリーンショット。](./media/contoso-migration-refactor-web-app-sql/pipeline9.png)

1. ステージ下で、 **[1 個のジョブ、1 個のタスク]** を選択して WCF サービスのデプロイを構成します。

    ![[1 個のジョブ、1 個のタスク] オプションのスクリーンショット。](./media/contoso-migration-refactor-web-app-sql-managed-instance/pipeline10.png)

1. サブスクリプションが選択および承認されていることを確認し、 **[App Service の名前]** を選択します。

     ![App Service の名前を選択する画面のスクリーンショット。](./media/contoso-migration-refactor-web-app-sql/pipeline11.png)

1. パイプライン > **[成果物]** で、 **[Add an artifact]\(成果物の追加\)** を選択し、**ContosoSmarthotel360Refactor** パイプラインを使用してビルドするように選択します。

     ![[Add an artifact]\(成果物の追加\) ペインの [ビルド] ボタンのスクリーンショット。](./media/contoso-migration-refactor-web-app-sql/pipeline12.png)

1. 継続的デプロイ トリガーを有効にするために、管理者が成果物の稲妻アイコンを選択します。

     ![成果物の稲妻アイコンのスクリーンショット。](./media/contoso-migration-refactor-web-app-sql/pipeline13.png)

1. 継続的デプロイ トリガーを **[有効]** に設定します。

    ![継続的デプロイ トリガーを [有効] に設定した画面のスクリーンショット。](./media/contoso-migration-refactor-web-app-sql/pipeline14.png)

1. 管理者は、ステージ **[1 個のジョブ、1 個のタスク]** に戻り、 **[Deploy Azure App Service]\(Azure App Service のデプロイ\)** を選択します。

    ![[Deploy Azure App Service]\(Azure App Service のデプロイ\) を選択するオプションのスクリーンショット。](./media/contoso-migration-refactor-web-app-sql/pipeline15.png)

1. **[Select a file or folder]\(ファイルまたはフォルダーの選択\)** で **drop** フォルダーを展開し、ビルド中に作成された *SmartHotel.Registration.Wcf.zip* ファイルを選択して、 **[保存]** を選択します。

    ![WCF ファイルを選択するための [Select a file or folder]\(ファイルまたはフォルダーの選択\) ペインのスクリーンショット。](./media/contoso-migration-refactor-web-app-sql/pipeline16.png)

1. **[パイプライン]**  >  **[ステージ]** の順に選択し、 **[+ 追加]** を選択して、`SHWEB-EUS2` 用の環境を追加します。 別の Azure App Service のデプロイを選択します。

    ![環境を追加するための [1 個のジョブ、1 個のタスク] リンクのスクリーンショット。](./media/contoso-migration-refactor-web-app-sql/pipeline17.png)

1. このプロセスを繰り返して `SmartHotel.Registration.Web.zip` ファイルを適切な Web アプリに発行し、 **[保存]** を選択します。

    ![Web ファイルを選択するための [Select a file or folder]\(ファイルまたはフォルダーの選択\) ペインのスクリーンショット。](./media/contoso-migration-refactor-web-app-sql/pipeline18.png)

    次のリリース パイプラインが表示されます。

     ![リリース パイプラインの概要のスクリーンショット。](./media/contoso-migration-refactor-web-app-sql/pipeline19.png)

1. **[ビルド]** に戻り、 **[トリガー]** を選択して、 **[継続的インテグレーションを有効にする]** チェック ボックスをオンにします。 この操作でパイプラインが有効になり、変更がコードに対してコミットされると、フル ビルドとリリースが行われます。

    ![[継続的インテグレーションを有効にする] チェック ボックスを強調表示した画面のスクリーンショット。](./media/contoso-migration-refactor-web-app-sql/pipeline20.png)

1. **[保存してキューに登録]** を選択し、フル パイプラインを実行します。 新しいビルドがトリガーされ、続いてアプリケーションの最初のリリースが Azure App Service に対して作成されます。

    ![[保存してキューに登録] ボタンのスクリーンショット。](./media/contoso-migration-refactor-web-app-sql/pipeline21.png)

1. Contoso 管理者は、Azure DevOps からビルドとリリース パイプラインのプロセスに従うことができます。 ビルドの完了後、リリースが開始されます。

    ![アプリのビルドとリリースの進行状況を示す画面のスクリーンショット。](./media/contoso-migration-refactor-web-app-sql/pipeline22.png)

1. パイプラインが完了すると、両方のサイトがデプロイされ、アプリケーションがオンラインで稼働します。

    ![アプリケーションが稼動していることを示す画面のスクリーンショット。](./media/contoso-migration-refactor-web-app-sql/pipeline23.png)

    アプリケーションが Azure に正常に移行されました。

## <a name="clean-up-after-migration"></a>移行後にクリーンアップする

移行後、Contoso は、以下のクリーンアップ手順を実行します。

- vCenter のインベントリからオンプレミスの VM を削除します。
- ローカルのバックアップ ジョブから VM を削除します。
- SmartHotel360 アプリケーションの新しい場所を示すように社内ドキュメントを更新します。 データベースは Azure SQL Database で、またフロントエンドは 2 つの Web アプリで実行されていることが、このドキュメントには示されます。
- 使用停止されている VM と対話しているリソースがないかを確認します。また、関連する設定やドキュメントがあれば、更新して新しい構成を反映します。

## <a name="review-the-deployment"></a>デプロイを再調査する

リソースを Azure に移行したら、新しいインフラストラクチャを完全に運用可能な状態にすると共に、そのセキュリティの保護に努める必要があります。

### <a name="security"></a>セキュリティ

- Contoso は新しい `SmartHotel-Registration` データベースのセキュリティ保護に努めます。 [詳細については、こちらを参照してください](/azure/sql-database/sql-database-security-overview)。
- 特に、Contoso は証明書で SSL を使用するように Web アプリを更新します。

### <a name="backups"></a>バックアップ

- Contoso チームは、Azure SQL Database のバックアップ要件を確認します。 [詳細については、こちらを参照してください](/azure/sql-database/sql-database-automated-backups)。
- また、SQL Database のバックアップと復元の管理について確認します。 自動バックアップについては、[こちら](/azure/sql-database/sql-database-automated-backups)をご覧ください。
- データベースのリージョン内フェールオーバーを提供するようにフェールオーバー グループの実装を検討します。 [詳細については、こちらを参照してください](/azure/sql-database/sql-database-geo-replication-overview)。
- 回復性を高めるために、メイン リージョン (`East US 2`) とセカンダリ リージョン (`Central US`) に Web アプリをデプロイすることを検討します。 チームはリージョンの障害が発生しても確実にフェールオーバーされるように Traffic Manager を構成することができます。

### <a name="licensing-and-cost-optimization"></a>ライセンスとコストの最適化

- すべてのリソースをデプロイした後、Contoso は[インフラストラクチャ計画](./contoso-migration-infrastructure.md#set-up-tagging)に基づいて Azure タグを割り当てます。
- すべてのライセンスは、Contoso が使用している PaaS サービスのコストに組み込まれています。 このコストは Enterprise Agreement から差し引かれます。
- Contoso は [Azure Cost Management と Billing](/azure/cost-management-billing/cost-management-billing-overview) を使用して、IT リーダーが定めた予算内に確実に収まるようにします。

## <a name="conclusion"></a>まとめ

この記事の中で、Contoso は、アプリケーションのフロントエンド VM を 2 つの Azure App Service Web アプリに移行することで、Azure にある SmartHotel360 アプリケーションをリファクターしました。 アプリケーション データベースを Azure SQL Database に移行しました。
