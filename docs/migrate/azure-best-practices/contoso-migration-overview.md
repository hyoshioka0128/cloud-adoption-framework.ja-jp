---
title: Azure へのアプリケーション移行例の概要
titleSuffix: Microsoft Cloud Adoption Framework for Azure
description: Cloud Adoption Framework の移行セクションの一環として含まれている、アプリケーション移行例の概要を示します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 10/11/2018
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: migrate
ms.openlocfilehash: 18b2bc641ba45c83a8ce6c5069857c398801adfd
ms.sourcegitcommit: bf9be7f2fe4851d83cdf3e083c7c25bd7e144c20
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73566442"
---
# <a name="application-migration-patterns-and-examples"></a>アプリケーションの移行パターンと例

Cloud Adoption Framework のこのセクションでは、一般的な移行シナリオの例をいくつか紹介し、オンプレミス インフラストラクチャを [Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure) クラウドに移行するための方法を示します。

## <a name="introduction"></a>はじめに

Azure には、クラウド サービスの包括的なセットが用意されています。 開発者や IT プロフェッショナルは、これらのサービスを利用し、データセンターのグローバルなネットワークを通じて、さまざまなツールやフレームワークをベースにアプリケーションを構築、デプロイ、および管理することができます。 ビジネスはデジタル シフトに関連する課題に直面しているため、Azure クラウドはリソースや操作の最適化、お客様や従業員との連携、および製品の変換の方法を理解できるように支援します。

ただし、速度と柔軟性、最小限のコスト、パフォーマンス、および信頼性の面でのクラウドのすべての利点にもかかわらず、多くの組織ではまだしばらくの間、オンプレミス データセンターを実行する必要があることも認識しています。 クラウドの採用の障壁に対処するために、Azure では、オンプレミスのデータセンターと Azure パブリック クラウドの間に橋を構築するハイブリッド クラウド戦略を提供しています。 たとえば、Azure Backup などの Azure クラウド リソースを使用してオンプレミスのリソースを保護したり、Azure 分析を使用してオンプレミスのワークロードを把握したりすることができます。

ハイブリッド クラウド戦略の一環として、Azure ではオンプレミスのアプリやワークロードをクラウドに移行するためのソリューションを用意しています。 簡単な手順で、オンプレミス リソースを包括的に評価し、Azure クラウドではどのように実行されるかを把握することができます。 次に、入手した詳しい評価に基づき、自信を持ってリソースを Azure に移行することができます。 Azure でリソースを稼働させたら、リソースを最適化して、アクセス、柔軟性、セキュリティ、および信頼性を保持したり向上させたりすることができます。

## <a name="migration-patterns"></a>移行パターン

クラウドへの移行戦略は、リホスト、リファクター、リアーキテクト、リビルドという 4 つの大きなパターンに分類できます。 採用する戦略は、ビジネスの主導者や移行の目標によって変わります。 複数のパターンを採用する場合もあります。 たとえば、単純なアプリやビジネス上重要でないアプリはリホストし、より複雑でビジネスに不可欠なアプリはリアーキテクトするという選択も可能です。 これらのパターンを確認しましょう。

<!-- markdownlint-disable MD033 -->

**パターン** | **定義** | **いつ使用するか**
--- | --- | ---
**リホスト** | 多くの場合、"_リフト アンド シフト_" 移行と呼ばれます。 このオプションでは、コードの変更を必要とせずに、既存のアプリを Azure にすばやく移行できます。 各アプリはそのままの状態で移行されるので、コード変更に伴うリスクとコストなしにクラウドのメリットを享受できます。 | アプリをクラウドにすばやく移動する必要がある場合。<br/><br/> アプリを変更せずに移動したい場合。<br/><br/> 移行後に [Azure IaaS](https://azure.microsoft.com/overview/what-is-iaas) のスケーラビリティを活かせるように、アプリが設計されている場合。<br/><br/> ビジネスにとって重要なアプリであるが、アプリの機能をすぐに変更する必要がない場合。
**リファクター** | 多くの場合、"再パッケージ化" と呼ばれます。リファクターを行うには、[Azure PaaS](https://azure.microsoft.com/overview/what-is-paas) に接続してクラウド サービスを使用できるように、アプリに最小限の変更を加える必要があります。<br/><br/> たとえば、既存のアプリを Azure App Service または Azure Kubernetes Service (AKS) に移行できます。<br/><br/> または、リレーショナル データベースおよび非リレーショナル データベースを、Azure SQL Database Managed Instance、Azure Database for MySQL、Azure Database for PostgreSQL、Azure Cosmos DB などのオプションにリファクターすることもできます。 | Azure で動作するようにアプリを簡単に再パッケージ化できる場合。<br/><br/> Azure が提供する革新的な DevOps プラクティスを適用したい場合、またはワークロード向けのコンテナー戦略を使用した DevOps を検討している場合。<br/><br/> リファクタリングでは、既存のコード ベースの移植性と使用可能な開発スキルを考慮する必要があります。
**リアーキテクト** | 移行のためのリアーキテクトは、アプリのアーキテクチャを最適化してクラウドのスケーラビリティを活かすために、アプリの機能とコード ベースを変更、拡張することを中心とした作業となります。<br/><br/> たとえば、モノリシック アプリケーションを、相互に連携し、簡単にスケーリングできるマイクロサービスのグループに分割できます。<br/><br/> または、リレーショナル データベースと非リレーショナル データベースを、Azure SQL Database Managed Instance、Azure Database for MySQL、Azure Database for PostgreSQL、Azure Cosmos DB など、フル マネージドのデータベース ソリューションにリアーキテクトすることもできます。 | アプリにメジャー リビジョンを施し、新機能を組み込んだり、クラウド プラットフォームで作業を効果的に行ったりする必要がある場合。<br/><br/> 既存のアプリケーションへの投資を活かしたい場合、スケーラビリティ要件を満たしたい場合、革新的な Azure DevOps プラクティスを適用したい場合、仮想マシンの使用を最小限に抑えたい場合。
**リビルド** | リビルドでは、Azure のクラウド テクノロジを利用してアプリを最初からリビルドし、進化させることができます。<br/><br/> たとえば、Azure Functions、Azure AI、Azure SQL Database Managed Instance、Azure Cosmos DB などの[クラウド ネイティブ](https://azure.com/cloudnative) テクノロジを活用して、新しいアプリを構築できます。 | 開発は迅速に行いたいが、既存のアプリの機能と寿命が限られている場合。<br/><br/> ビジネスのイノベーションの加速 (Azure が提供する DevOps のプラクティスを含む)、クラウドネイティブ テクノロジを活用した新しいアプリケーションの構築、AI、ブロックチェーン、IoT の進化の活用に対する準備が整っている場合。

<!-- markdownlint-enable MD033 -->

## <a name="migration-example-articles"></a>移行例に関する記事

このセクションに示した記事では、いくつかの一般的な移行シナリオの例を紹介しています。 これらの各例では、背景情報や詳細なデプロイ シナリオについて説明すると共に、移行のインフラストラクチャを設定して移行のためのオンプレミス リソースの適合性を評価する方法を示しています。 このセクションには、今後も記事が追加される予定です。

![一般的な移行/モダン化プロジェクト](./media/migration-patterns.png)

"*一般的な移行およびモダン化プロジェクトのカテゴリ。* "

シリーズ内の記事は、以下のように要約されます。

- 各移行シナリオは、少しずつ異なるビジネス目標に沿って作成されています。このビジネス目標によって移行戦略が決まります。
- 各デプロイ シナリオでは、ビジネス ドライバーと目標、提案されたアーキテクチャ、移行を実施するための手順、クリーンアップの推奨事項、移行完了後の次の手順についての情報が記載されています。

### <a name="assessment"></a>評価

**記事** | **詳細**
--- | ---
[Azure への移行の対象となるオンプレミスのリソースの評価](./contoso-migration-assessment.md) | この記事では、VMware 上で実行されているオンプレミス アプリの評価を実行する方法を示します。 例では、サンプル組織が Azure Migrate サービスを使用してアプリの VM を、また、Data Migration Assistant を使用してアプリの SQL Server データベースを評価します。

### <a name="infrastructure"></a>インフラストラクチャ

**記事** | **詳細**
--- | ---
[Azure インフラストラクチャをデプロイする](./contoso-migration-infrastructure.md) | この記事では、組織が移行に向けてオンプレミス インフラストラクチャと Azure インフラストラクチャを準備する方法を示します。 この記事で構築したインフラストラクチャの例は、このセクション内で紹介されている他のサンプルの中で参照されます。

### <a name="windows-server-workloads"></a>Windows Server ワークロード

**記事** | **詳細**
--- | ---
[Azure VM でのアプリのリホスト](./contoso-migration-rehost-vm.md) | この記事では、Site Recovery サービスを使用してオンプレミス アプリ VM を Azure VM に移行する例を示します。
[Azure コンテナーと Azure SQL Database 内でアプリをリアーキテクトする](./contoso-migration-rearchitect-container-sql.md) | この記事では、Azure Service Fabric 内で実行される Windows コンテナーとしてアプリの Web 層をリアーキテクトし、さらに、Azure SQL Database を使用してデータベースをリアーキテクトして、アプリを移行する例を示します。

### <a name="linux-workloads"></a>Linux ワークロード

**記事** | **詳細**
--- | ---
[Azure VM および Azure Database for MySQL での Linux アプリのリホスト](./contoso-migration-rehost-linux-vm-mysql.md) | この記事では、Site Recovery を使用して Linux でホストされているアプリを Azure VM に移行する例を示します。 アプリ データベースを Azure Database for MySQL に移行するために MySQL Workbench を使用します。
[Linux アプリの Azure VM へのリホスト](./contoso-migration-rehost-linux-vm.md) | この例では、Site Recovery サービスを使用して、Linux ベース アプリの Azure VM へのリフト アンド シフト移行を完了する方法を示します。

### <a name="sql-server-workloads"></a>SQL Server ワークロード

**記事** | **詳細**
--- | ---
[Azure VM および SQL Database Managed Instance でのアプリのリホスト](./contoso-migration-rehost-vm-sql-managed-instance.md) | この記事では、オンプレミス アプリの Azure へのリフト アンド シフト移行の例を示します。 これには、[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) を使用してアプリのフロントエンド VM を移行し、[Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview) を使用してアプリのデータベースを Azure SQL Database Managed Instance に移行します。
[Azure VM および SQL Server Always On 可用性グループでのアプリのリホスト](./contoso-migration-rehost-vm-sql-ag.md) | この例では、Azure にホストされている SQL Server VM を使用して、アプリとデータを移行する方法を示します。 Site Recovery を使用してアプリの VM を移行し、Azure Database Migration Service を使用してアプリのデータベースを Always On 可用性グループで保護されている SQL Server クラスターに移行します。

### <a name="aspnet-php-and-java-apps"></a>ASP.NET、PHP、および Java アプリ

**記事** | **詳細**
--- | ---
[Azure Web App と Azure SQL Database 内でアプリをリファクターする](./contoso-migration-refactor-web-app-sql.md) | この例では、オンプレミスの Windows ベース アプリを Azure Web アプリに移行し、また、Database Migration Assistant を使用してアプリのデータベースを Azure SQL Server インスタンスに移行する方法を示します。
[Azure App Service、Azure Traffic Manager、および Azure Database for MySQL を使用して複数のリージョンに Linux アプリをリファクターする](./contoso-migration-refactor-linux-app-service-mysql.md) | この例では、Azure Traffic Manager を使用してオンプレミスの Linux ベース アプリを、継続的デリバリーのために GitHub に統合される複数の Azure リージョン上の Azure Web アプリに移行する方法を示します。 アプリのデータベースは Azure Database for MySQL インスタンスに移行されます。
[Azure でのアプリのリビルド](./contoso-migration-rebuild.md) | この記事では、Azure のさまざまな機能とマネージド サービス (Azure App Service、Azure Kubernetes Service (AKS)、Azure Functions、Azure Cognitive Services、Azure Cosmos DB など) を使用してオンプレミス アプリをリビルドする例を示します。
[Azure DevOps Services 上で Team Foundation Server をリファクターする](./contoso-migration-tfs-vsts.md) | この記事では、オンプレミスの Team Foundation Server のデプロイを Azure 内の Azure DevOps Services に移行する例を示します。

### <a name="migration-scaling"></a>移行のスケーリング

**記事** | **詳細**
--- | ---
[Azure への移行のスケーリング](./contoso-migration-scale.md) | この記事では、サンプル組織が Azure への完全な移行に対してスケーリングを行うための準備の方法を示します。

### <a name="demo-apps"></a>デモ アプリ

このセクションで紹介している記事の例では、2 つのデモ アプリを使用します。SmartHotel360 と osTicket です。

- **SmartHotel360:** このアプリは、Azure を操作する際に使用できるテスト アプリとして Microsoft が開発したものです。 オープン ソースとして提供されており、[GitHub](https://github.com/Microsoft/SmartHotel360) からダウンロードできます。 これは、SQL Server データベースに接続されている ASP.NET アプリです。 上記の記事で取り上げられたシナリオでは、このアプリの現在のバージョンは Windows Server 2008 R2 および SQL Server 2008 R2 を実行している 2 つの VMware VM にデプロイされます。 これらのアプリの VM はオンプレミスでホストされ、vCenter Server によって管理されています。
- **osTicket:** Linux 上で稼働する、オープン ソースのサービス デスク向け発券アプリです。 [GitHub](https://github.com/osTicket/osTicket) からダウンロードできます。 上記の記事で取り上げられたシナリオでは、このアプリの現在のバージョンは Apache 2、PHP 7.0、および MySQL 5.7 を使用して、Ubuntu 16.04 LTS を実行している 2 つの VMware VM にオンプレミスでデプロイされます。
