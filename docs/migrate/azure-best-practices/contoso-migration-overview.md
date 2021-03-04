---
title: Azure へのアプリケーション移行例の概要
description: クラウド導入フレームワークの移行手法の一環として含まれている、アプリケーション移行例の概要を説明します。
author: deltadan
ms.author: abuck
ms.date: 07/01/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: migrate
ms.custom: think-tank
ms.openlocfilehash: fe416b568b2133d9ff1cd27c5694c98f2925cfce
ms.sourcegitcommit: b8f8b7631aabaab28e9705934bf67dad15e3a179
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101789968"
---
# <a name="overview-of-application-migration-examples-for-azure"></a>Azure へのアプリケーション移行例の概要

Azure のクラウド導入フレームワークのこのセクションでは、一般的な移行シナリオの例をいくつか紹介し、オンプレミス インフラストラクチャを [Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/) に移行する方法を示します。

## <a name="introduction"></a>はじめに

Azure には、クラウド サービスの包括的なセットが用意されています。 開発者や IT プロフェッショナルは、これらのサービスを使用して、データセンターのグローバルなネットワークを通じて、さまざまなツールやフレームワークでアプリケーションを構築、デプロイ、管理できます。 企業がデジタル シフトに関連する課題に直面しているときに、Azure プラットフォームは次の方法を理解するのに役立ちます。

- リソースと操作を最適化する。
- お客様や従業員と連携する。
- 製品を変換する。

クラウドを使用することで、迅速さと柔軟性、コスト削減、パフォーマンス、信頼性といったメリットを得ることができます。 しかし、多くの組織では、引き続きオンプレミスのデータセンターを稼働させる必要があります。 クラウドの採用の障壁に対処するために、Azure では、オンプレミスのデータセンターと Azure パブリック クラウドの橋渡しをするハイブリッド クラウド戦略を提供しています。 たとえば、Azure Backup などの Azure クラウド リソースを使用してオンプレミスのリソースを保護したり、Azure Analytics を使用してオンプレミスのワークロードの分析情報を取得したりできます。

ハイブリッド クラウド戦略の一環として、Azure にはオンプレミスのアプリケーションやワークロードをクラウドに移行するための多数のソリューションが用意されています。 簡単な手順を使用して、オンプレミス リソースを包括的に評価し、これらを Azure プラットフォームで実行する方法を把握することができます。 次に、入手した詳しい評価に基づき、自信を持ってリソースを Azure に移行することができます。 Azure でリソースを稼働させたら、リソースを最適化して、アクセス、柔軟性、セキュリティ、および信頼性を保持したり向上させたりすることができます。

## <a name="migration-patterns"></a>移行パターン

クラウドへの移行戦略は、リホスト、リファクター、リアーキテクト、リビルドという 4 つの大きなパターンに分類できます。 採用する戦略は、ビジネスの主導者や移行の目標によって変わります。 複数のパターンを採用する場合もあります。 たとえば、重要度の低いアプリケーションはリホストし、より複雑でビジネスに不可欠なアプリケーションはリアーキテクトすることができます。 これらのパターンを確認しましょう。

| Pattern | 定義 | 使用する場合 |
| --- | --- | --- |
| **リホスト** | このオプションは、リフトアンドシフト移行とも呼ばれ、コードを変更する必要がありません。 これを使用すると、既存のアプリケーションを Azure にすばやく移行できます。 各アプリケーションはそのままの状態で移行されるので、コード変更に関連するリスクやコストを伴わずにクラウドのメリットを享受できます。 | アプリケーションをクラウドに迅速に移行する必要がある場合。 <br><br> アプリケーションを変更せずに移行する場合。 <br><br> 移行後に [Azure IaaS (サービスとしてのインフラストラクチャ)](https://azure.microsoft.com/overview/what-is-iaas/) のスケーラビリティを活用できるようにアプリケーションが設計されている場合。 <br><br> ビジネスにとって重要なアプリケーションではあるが、アプリケーションの機能をすぐに変更する必要がない場合。 |
| **リファクター** | 多くの場合、"再パッケージ化" と呼ばれます。リファクタリングでは、[Azure PaaS (サービスとしてのプラットフォーム)](https://azure.microsoft.com/overview/what-is-paas/) に接続してクラウド サービスを使用できるように、アプリケーションに最小限の変更を加える必要があります。 <br><br> たとえば、既存のアプリケーションを Azure App Service または Azure Kubernetes Service (AKS) に移行できます。 <br><br> または、リレーショナル データベースおよび非リレーショナル データベースを、Azure SQL Managed Instance、Azure Database for MySQL、Azure Database for PostgreSQL、Azure Cosmos DB などのオプションにリファクターすることもできます。 | Azure で動作するようにアプリケーションを簡単に再パッケージ化できる場合。 <br><br> Azure で提供される革新的な DevOps プラクティスを適用する場合、またはワークロードにコンテナー戦略を使用する DevOps を検討している場合。 <br><br> リファクタリングでは、既存のコード ベースの移植性と使用可能な開発スキルを考慮する必要があります。 |
| **リアーキテクト** | 移行のためのリアーキテクトでは、アプリケーション アーキテクチャをクラウドのスケーラビリティに最適化するために、アプリケーションの機能とコード ベースの変更および拡張に重点を置きます。 <br><br> たとえば、モノリシック アプリケーションを、相互に連携し、簡単にスケーリングできるマイクロサービスのグループに分割できます。 <br><br> また、リレーショナル データベースと非リレーショナル データベースを、フル マネージドのデータベース ソリューション (SQL Managed Instance、Azure Database for MySQL、Azure Database for PostgreSQL、Azure Cosmos DB など) にリアーキテクトすることもできます。 | 新機能を組み込んだり、クラウド プラットフォーム上で効果的に機能させたりするために、アプリケーションの大幅な改訂が必要な場合。 <br><br> 既存のアプリケーションへの投資を活かしたい場合、スケーラビリティ要件を満たしたい場合、革新的な DevOps プラクティスを適用したい場合、仮想マシンの使用を最小限に抑えたい場合。 |
| **リビルド** | リビルドでは、Azure クラウド テクノロジを使用してアプリケーションを一から再構築することで進化させることができます。 <br><br> たとえば、Azure Functions、AI、SQL Managed Instance、Azure Cosmos DB などの[クラウドネイティブ](https://azure.microsoft.com/overview/cloudnative/) テクノロジを使用して、新しいアプリケーションを構築できます。 | 既存のアプリケーションの機能と有効期間が限られており、開発を迅速に行いたい場合。 <br><br> ビジネスのイノベーションの加速 (Azure が提供する DevOps のプラクティスを含む)、クラウドネイティブ テクノロジを活用した新しいアプリケーションの構築、AI、ブロックチェーン、IoT の進化の活用に対する準備が整っている場合。 |

## <a name="migration-example-articles"></a>移行例に関する記事

このセクションでは、いくつかの一般的な移行シナリオの例を紹介します。 各例では、背景情報や詳細なデプロイ シナリオについて説明すると共に、移行のインフラストラクチャを設定して移行のためのオンプレミス リソースの適合性を評価する方法を示しています。 このセクションには、今後も記事が追加される予定です。

![移行および最新化プロジェクトのカテゴリの図。](./media/migration-patterns.png)
"*図 1: 移行および最新化プロジェクトの一般的なカテゴリ*

このシリーズでは、移行戦略を決定付ける、わずかに異なるビジネス目標によって推進される、各移行シナリオに焦点を合わせています。 デプロイ シナリオごとに、次の情報を示します。

- ビジネスの推進要因と目標。
- 提案されたアーキテクチャ。
- 移行の実行手順。
- 移行完了後の、クリーンアップと次の手順に関する推奨事項。

### <a name="assessment"></a>評価

| [アーティクル] | 詳細 |
| --- | --- |
| [Azure への移行の対象となるオンプレミスのリソースの評価](../../plan/contoso-migration-assessment.md) | 計画方法のベスト プラクティスに関するこの記事では、VMware で実行されているオンプレミス アプリケーションの評価を実行する方法について説明します。 この記事では、架空の組織が Azure Migrate を使用してアプリケーション VM を評価し、Data Migration Assistant を使用してアプリケーションの SQL Server データベースを評価します。 |

### <a name="infrastructure"></a>インフラストラクチャ

| [アーティクル] | 詳細 |
| --- | --- |
| [Azure インフラストラクチャをデプロイする](./contoso-migration-infrastructure.md) | この記事では、組織が移行に向けてオンプレミス インフラストラクチャと Azure インフラストラクチャを準備する方法を示します。 この記事で構築したインフラストラクチャの例は、このセクション内で紹介されている他のサンプルの中で参照されます。 |

### <a name="windows-server-workloads"></a>Windows Server ワークロード

| [アーティクル] | 詳細 |
| --- | --- |
| [アプリケーションを Azure VM にリホストする](./contoso-migration-rehost-vm.md) | この記事では、Azure Migrate を使用して、オンプレミスのアプリケーション VM を Azure VM に移行する例を示します。 |

### <a name="sql-server-workloads"></a>SQL Server ワークロード

| [アーティクル] | 詳細 |
| --- | --- |
| [SQL Server データベースを Azure に移行する](./contoso-migration-sql-server-db-to-azure.md) | この記事では、架空の会社である Contoso が、オンプレミスの各種 SQL Server データベースの Azure への移行をどのように評価、計画し、実行したかについて説明します。 |
| [アプリケーションを Azure VM と SQL Managed Instance にリホストする](./contoso-migration-rehost-vm-sql-managed-instance.md) | この記事では、オンプレミス アプリケーションの Azure へのリフトアンドシフト移行の例を示します。 このプロセスでは、Azure Migrate を使用してアプリケーションのフロントエンド VM を移行し、[Azure Database Migration Service](/azure/dms/dms-overview) を使用してアプリケーション データベースを SQL Managed Instance に移行します。 |
| [SQL Server Always On 可用性グループを使用してアプリケーションを Azure VM にリホストする](./contoso-migration-rehost-vm-sql-ag.md) | この例では、Azure でホストされている SQL Server VM を使用して、アプリケーションとデータを移行する方法を示します。 Azure Migrate を使用してアプリケーション VM を移行し、Database Migration Service を使用して、アプリケーション データベースを、Always On 可用性グループによって保護されている SQL Server クラスターに移行します。 |

### <a name="linux-and-open-source-databases"></a>Linux およびオープンソース データベース

| [アーティクル] | 詳細 |
| --- | --- |
| [オープンソース データベースを Azure に移行する](./contoso-migration-oss-db-to-azure.md) | この記事では、架空の会社である Contoso が、さまざまなオンプレミス オープンソース データベースの Azure への移行をどのように評価、計画し、実行したかについて説明します。 |
| [MySQL を Azure に移行する](./contoso-migration-mysql-to-azure.md) | この記事では、Contoso という架空の会社が、オンプレミスの MySQL オープンソース データベース プラットフォームの Azure への移行をどのように計画し、実行したかについて説明します。 |
| [PostgreSQL を Azure に移行する](./contoso-migration-postgresql-to-azure.md) | この記事では、Contoso という架空の会社が、オンプレミスの PostgreSQL オープンソース データベース プラットフォームの Azure への移行をどのように計画し、実行したかについて説明します。 |
| [MariaDB を Azure に移行する](./contoso-migration-mariadb-to-azure.md) | この記事では、Contoso という架空の会社が、オンプレミスの MariaDB オープンソース データベース プラットフォームの Azure への移行をどのように計画し、実行したかについて説明します。 |
| [Linux アプリケーションを Azure VM と Azure Database for MySQL にリホストする](./contoso-migration-rehost-linux-vm-mysql.md) | この記事では、Azure Migrate を使用して、Linux でホストされているアプリケーションを Azure VM に移行する例を示します。 アプリケーション データベースは、[Database Migration Service](/azure/dms/dms-overview) を使用して Azure Database for MySQL に移行されます。 |
| [Linux アプリケーションを Azure VM にリホストする](./contoso-migration-rehost-linux-vm.md) | この例では、Azure Migrate を使用して、Linux ベースのアプリケーションを Azure VM にリフトアンドシフト移行する方法を示します。 |

### <a name="devtest-workloads"></a>開発/テスト ワークロード

| [アーティクル] | 詳細 |
| --- | --- |
| [開発/テスト環境を Azure IaaS に移行する](./contoso-migration-devtest-to-iaas.md) | この記事では、Contoso が Azure VM への移行によって、VMware VM で実行されている 2 つのアプリケーションの Dev/Test 環境をどのようにリホストするかについて説明します。 |
| [Azure DevTest Labs に移行する](./contoso-migration-devtest-to-labs.md) | この記事では、Contoso が DevTest Labs を使用して、Dev/Test ワークロードを Azure にどのように移行するかについて説明します。 |

### <a name="aspnet-and-php-web-apps"></a>ASP.NET と PHP Web アプリ

| [アーティクル] | 詳細 |
| --- | --- |
| [App Service と SQL Database を使用して Windows アプリケーションをリファクターする](./contoso-migration-refactor-web-app-sql.md) | この例では、オンプレミスの Windows ベースのアプリケーションを Azure Web アプリに移行し、[Database Migration Service](/azure/dms/dms-overview) を使用して、アプリケーション データベースを Azure SQL Database サーバー インスタンスに移行する方法を示します。 |
| [App Service と SQL Managed Instance を使用して Windows アプリケーションをリファクターする](./contoso-migration-refactor-web-app-sql-managed-instance.md) | この例では、オンプレミスの Windows ベースのアプリケーションを Azure Web アプリに移行し、[Database Migration Service](/azure/dms/dms-overview) を使用して、アプリケーション データベースを SQL Managed Instance に移行する方法を示します。 |
| [App Service、Azure Traffic Manager、および Azure Database for MySQL を使用して複数のリージョンに Linux アプリケーションをリファクターする](./contoso-migration-refactor-linux-app-service-mysql.md) | この例では、Traffic Manager を使用してオンプレミスの Linux ベースのアプリケーションを複数の Azure リージョンの Azure Web アプリに移行し、継続的デリバリーを実現できるように GitHub と統合する方法について説明します。 アプリケーション データベースは Azure Database for MySQL インスタンスに移行されます。 |
| [アプリケーションを Azure にリビルドする](./contoso-migration-rebuild.md) | この記事では、Azure の一連の機能と管理サービスを使用して、オンプレミスのアプリケーションをリビルドする例を示します。 これらの機能とサービスには、App Service、AKS、Azure Functions、Azure Cognitive Services、Azure Cosmos DB が含まれます。 |
| [Azure DevOps Services に Team Foundation Server をリファクターする](./contoso-migration-tfs-vsts.md) | この記事では、オンプレミスの Team Foundation Server のデプロイを Azure 内の Azure DevOps Services に移行する例を示します。 |

### <a name="sap"></a>SAP

| [アーティクル] | 詳細 |
| --- | --- |
| [SAP 移行ガイド](https://azure.microsoft.com/resources/sap-on-azure-implementation-guide/) | オンプレミスの SAP ワークロードをクラウドに移行するための実用的なガイダンスを提供します。 |
| [SAP アプリケーションを Azure に移行する](https://azure.microsoft.com/resources/migrating-sap-applications-to-azure/) | クラウドへの SAP の移行に関するホワイト ペーパーとロードマップ。 |
| [SAP on Azure の移行方法](https://azure.microsoft.com/resources/migration-methodologies-for-sap-on-azure/) | SAP アプリケーションを Azure に移行するためのさまざまな移行オプションの概要。 |

### <a name="specialized-workloads"></a>特殊化されたワークロード

| [アーティクル] | 詳細 |
| --- | --- |
| [オンプレミスの VMware インフラストラクチャを Azure に移行する](./contoso-migration-vmware-to-azure.md) | この記事では、Azure VMware Solution を使用して、オンプレミスの VMware VM を Azure に移動する例を示します。 |
| [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) | NetApp を利用したエンタープライズ ファイル ストレージ。 Linux および Windows ファイル ワークロードを Azure で実行します。 |
| [Oracle on Azure](https://azure.microsoft.com/solutions/oracle/) | Azure と Oracle Cloud Infrastructure で Oracle データベースおよびエンタープライズ アプリケーションを実行します。 |
| [Azure における Cray](https://azure.microsoft.com/solutions/high-performance-computing/cray/) | Azure での Cray によるハイ パフォーマンス コンピューティング。 仮想ネットワーク上の専用スーパーコンピューター。 |

### <a name="vdi"></a>VDI

| [アーティクル] | 詳細 |
| --- | --- |
| [オンプレミスのリモート デスクトップ サービスを Azure の Windows Virtual Desktop に移動する](./contoso-migration-rds-to-wvd.md) | この記事では、オンプレミスのリモート デスクトップ サービスを Azure の Windows Virtual Desktop に移行する方法を説明します。 |

### <a name="migration-scaling"></a>移行のスケーリング

| [アーティクル] | 詳細 |
| --- | --- |
| [Azure への移行のスケーリング](./contoso-migration-scale.md) | この記事では、架空の組織が Azure への完全移行に向けてスケーリングの準備を行う方法を示します。 |

### <a name="demo-applications"></a>デモ アプリケーション

このセクションで紹介している記事の例では、2 つのデモ アプリケーションを使用します。SmartHotel360 と osTicket です。

**SmartHotel360**: このテスト アプリケーションは、Azure を操作するときに使用できるように、Microsoft によって開発されました。 これはオープン ソース ライセンスの下で提供されており、[GitHub](https://github.com/Microsoft/SmartHotel360) からダウンロードできます。 これは、SQL Server データベースに接続されている ASP.NET アプリケーションです。 上記の記事で説明するシナリオでは、このアプリケーションの現在のバージョンが、Windows Server 2008 R2 および SQL Server 2008 R2 を実行している 2 つの VMware VM にデプロイされます。 これらのアプリケーション VM はオンプレミスでホストされ、vCenter Server によって管理されています。

**osTicket**: このオープンソースのサービス デスク向け発券アプリケーションは、Linux 上で実行されます。 [GitHub](https://github.com/osTicket/osTicket) からダウンロードできます。 上記の記事で説明するシナリオでは、このアプリケーションの現在のバージョンが、Apache 2、PHP 7.0、および MySQL 5.7 を使用して、Ubuntu 16.04 LTS を実行している 2 つの VMware VM にオンプレミスでデプロイされます。
