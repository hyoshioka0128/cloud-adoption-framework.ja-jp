---
title: Azure コンテナーと Azure SQL Database にアプリをリアーキテクトする
description: Contoso が Azure Windows コンテナーと Azure SQL Database でアプリを再構築する方法について説明します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 10/11/2018
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: migrate
services: site-recovery
ms.openlocfilehash: d41c63d9876a5ead14acee9b39542ab256144920
ms.sourcegitcommit: 72a280cd7aebc743a7d3634c051f7ae46e4fc9ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2020
ms.locfileid: "78223121"
---
<!-- cSpell:ignore reqs contosohost contosodc contosoacreus contososmarthotel smarthotel vcenter WEBVM SQLVM -->

# <a name="rearchitect-an-on-premises-app-to-an-azure-container-and-azure-sql-database"></a>Azure コンテナーと Azure SQL Database でオンプレミス アプリを再構築する

この記事では、Contoso という架空の会社が、Azure への移行の一環として、VMware VM 上で実行される 2 層の Windows .NET アプリをリアーキテクトする方法を示します。 Contoso は、アプリ フロントエンド VM を Azure Windows コンテナーに、アプリ データベースを Azure SQL データベースに移行します。

この例で使用される SmartHotel360 アプリは、オープン ソースとして提供されています。 独自のテスト目的に沿って使用する場合は、[GitHub](https://github.com/Microsoft/SmartHotel360) からダウンロードできます。

## <a name="business-drivers"></a>ビジネス ドライバー

Contoso の IT リーダーシップ チームは、ビジネス パートナーと密接に連絡を取り合い、彼らがこの移行で何を達成しようとしているのかを理解しました。

- **ビジネスの成長への対応。** Contoso は成長を続けています。そのため、同社のオンプレミスのシステムとインフラストラクチャに負荷がかかっています。
- **効率化。** Contoso では、不要な手順を取り除き、開発者とユーザーのプロセスを効率化する必要があります。 ビジネス部門は IT に対して、時間やコストを無駄にせず、迅速に作業を行ってもらう必要があります。これは、例えば、顧客の要求に素早く対応するためです。
- **機敏性の向上。** Contoso IT は、ビジネス部門の要求に対して、対応力を向上させる必要があります。 また、グローバル経済で成功を収めるために、市場の変化に対して、より迅速な対応ができる必要があります。 ビジネスの妨げになったり、ビジネスの機会を壊すようなことがあってはなりません。
- **スケール。** ビジネスが順調に成長している中で、Contoso IT 部門は、同じペースで拡張できるシステムを提供する必要があります。
- **コストを削減する。** Contoso はライセンス コストを最小限に抑えたいと考えています。

## <a name="migration-goals"></a>移行の目標

Contoso クラウド チームは、この移行の目標を設定しました。 これらの目標を使用して、最良の移行方法を決定しました。

<!-- markdownlint-disable MD033 -->

**目標** | **詳細**
--- | ---
**アプリの要件** | このアプリは、Azure でも現在と同じくクリティカルです。<br/><br/> 現在の VMWare と同等のパフォーマンス機能が必要です。<br/><br/> Contoso は、アプリが現在ホストされている Windows Server 2008 R2 のサポートを終了することを望んでおり、アプリに投資したいと考えています。<br/><br/> Contoso は、SQL Server 2008 R2 から最新のマネージド データベース プラットフォームに移行して、管理の必要性を最小限に抑えたいと考えています。<br/><br/> Contoso では、可能であれば、SQL Server ライセンスとソフトウェア アシュアランスへの投資を活かしたいと考えています。<br/><br/> Contoso は、必要に応じてアプリの Web 階層をスケーリングしたいと考えています。
**制限事項** | このアプリは、同じ VM 上で実行されている ASP.NET アプリと WCF サービスで構成されています。 Contoso は、Azure App Service を使用して 2 つの Web アプリに分割したいと考えています。
**Azure の要件** | Contoso は、アプリを Azure に移行し、そのアプリをコンテナーで実行してアプリの寿命を延ばしたいと考えています。 Azure でのアプリの実装を完全に最初から始めたくはありません。
**DevOps** | Contoso は、コード ビルドとリリース パイプラインに Azure DevOps Services を使用して DevOps モデルに移行したいと考えています。

<!-- markdownlint-enable MD033 -->

## <a name="solution-design"></a>ソリューション設計

Contoso は目標と要件を決定した後、デプロイ ソリューションを設計およびレビューし、移行に使用する Azure サービスを含め、移行プロセスを決めます。

### <a name="current-app"></a>現在のアプリ

- SmartHotel360 オンプレミス アプリは 2 つの VM (WEBVM と SQLVM) に階層化されています。
- VM は、VMware ESXi ホスト **contosohost1.contoso.com** (バージョン 6.5) 上にあります。
- VMware 環境は、VM 上で実行中の vCenter Server 6.5 (**vcenter.contoso.com**) によって管理されています。
- Contoso にはオンプレミスのデータセンター (contoso-datacenter) があり、そこにオンプレミスのドメイン コントローラー (**contosodc1**) が含まれています。
- Contoso データセンター内のオンプレミス VM は、移行が行われた後に使用停止にされます。

### <a name="proposed-architecture"></a>提案されたアーキテクチャ

- このアプリのデータベース層について、Contoso は[こちらの記事](https://docs.microsoft.com/azure/sql-database/sql-database-features)を使用して Azure SQL Database と SQL Server を比較しました。 そして、いくつかの理由で Azure SQL Database を使用することにしました。
  - Azure SQL Database は、リレーショナル データベース マネージド サービスです。 複数のサービス レベルで予測可能なパフォーマンスを実現します。管理作業はほぼゼロです。 利点としては、ダウンタイムのない動的スケーラビリティ、組み込みのインテリジェントな最適化、グローバルなスケーラビリティと可用性などがあります。
  - Contoso は、軽量な Data Migration Assistant (DMA) を使用して、オンプレミス データベースを評価し、Azure SQL に移行することができます。
  - Contoso はソフトウェア アシュアランスに基づき、SQL Database では SQL Server 用の Azure ハイブリッド特典を利用して、既存のライセンスを割引料金のライセンスに交換することができます。 この方法なら最大 30% の節約が可能です。
  - SQL Database には、常に暗号化された動的データ マスキング、行レベルのセキュリティ/脅威検出など、いくつかのセキュリティ機能があります。
- アプリの Web 層については、Contoso は Azure DevOps Services を使用して Windows コンテナーに変換することを決定しました。
  - Contoso は Azure Service Fabric を使用してアプリをデプロイし、Azure Container Registry (ACR) から Windows コンテナー イメージをプルします。
  - センチメント分析を含むようにアプリを拡張するプロトタイプは、Cosmos DB に接続された Service Fabric の別のサービスとして実装されます。 ツイートから情報が読み取られ、アプリに表示されます。
- DevOps パイプラインを実装するために、Contoso はソース コード管理 (SCM) に Git リポジトリと共に Azure DevOps を使用します。 自動的なビルドとリリースを使用してコードを作成し、Azure Container Registry と Azure Service Fabric にデプロイします。

    ![シナリオのアーキテクチャ](./media/contoso-migration-rearchitect-container-sql/architecture.png)

### <a name="solution-review"></a>ソリューションのレビュー

Contoso は、長所と短所の一覧をまとめて、提案されたデザインを評価します。

<!-- markdownlint-disable MD033 -->

**考慮事項** | **詳細**
--- | ---
**長所** | SmartHotel360 アプリ コードは、Azure Service Fabric に移行するために変更する必要があります。 ただし、変更には Service Fabric SDK ツールを使用して、作業を最小限に抑えられます。<br/><br/> Contoso は Service Fabric に移行することで、マイクロサービスを開発し、元のコード ベースに悪影響を及ぼすことなく、徐々に簡単にマイクロサービスをアプリケーションに追加することができます。<br/><br/> Windows コンテナーには、一般的にコンテナーと同じ利点があります。 機敏性、移植性、制御性が向上します。<br/><br/> Contoso では、SQL Server と Windows Server の両方に Azure ハイブリッド特典を使用して、ソフトウェア アシュアランスへの投資を利用できます。<br/><br/> 移行後は、Windows Server 2008 R2 をサポートする必要がなくなります。 [詳細については、こちらを参照してください](https://support.microsoft.com/lifecycle)。<br/><br/> Contoso は複数のインスタンスがあるアプリの Web 層を構成することができたので、単一障害点ではなくなりました。<br/><br/> 古い SQL Server 2008 R2 に依存しなくなります。<br/><br/> SQL Database は、Contoso の技術面の要件をサポートしています。 Contoso 管理者は Data Migration Assistant を使用してオンプレミス データベースを評価し、互換性があることがわかりました。<br/><br/> SQL Database には、Contoso が設定する必要がないフォールト トレランス機能が組み込まれています。 そのため、データ層がフェールオーバーの単一ポイントではなくなります。
**短所** | コンテナーは他の移行オプションより複雑です。 Contoso にとって、コンテナーの学習曲線が問題になる可能性があります。 この曲線にもかかわらず、Contoso は多くの価値を提供する新しいレベルの複雑さを導入します。<br/><br/> Azure、コンテナー、アプリのマイクロサービスを理解し、サポートできるように、Contoso の運用チームを立ち上げる必要があります。<br/><br/> Contoso は Azure Database Migration Service ではなく Data Migration Assistant を使用してデータベースを移行する場合、大規模なデータベースを移行するためのインフラストラクチャを用意する予定がありません。

<!-- markdownlint-enable MD033 -->

### <a name="migration-process"></a>移行プロセス

1. Contoso は Windows 用 Azure Service Fabric クラスターをプロビジョニングしています。

1. Azure SQL インスタンスをプロビジョニングし、SmartHotel360 データベースをそこに移行します。

1. Contoso は Service Fabric SDK ツールを使用して、Web 層の VM を Docker コンテナーに変換します。

1. Service Fabric クラスターと ACR を接続し、Azure Service Fabric を使用してアプリをデプロイします。

    ![移行プロセス](./media/contoso-migration-rearchitect-container-sql/migration-process.png)

### <a name="azure-services"></a>Azure サービス

**サービス** | **説明** | **コスト**
--- | --- | ---
[Data Migration Assistant (DMA)](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | Azure のデータベース機能に影響を及ぼす可能性がある互換性の問題を評価して検出します。 DMA は SQL のソースとターゲット間で機能パリティを評価し、パフォーマンスと信頼性の向上箇所を推奨します。 | これは無料でダウンロードできるツールです。
[Azure SQL Database](https://azure.microsoft.com/services/sql-database) | インテリジェントなフル マネージド リレーショナル クラウド データベース サービスを提供します。 | 機能、スループット、サイズに基づくコスト。 [詳細については、こちらを参照してください](https://azure.microsoft.com/pricing/details/sql-database/managed)。
[Azure Container Registry](https://azure.microsoft.com/services/container-registry) | あらゆる種類のコンテナー デプロイのイメージを保存します。 | コストは機能、ストレージ、使用期間に基づいて発生します。 [詳細については、こちらを参照してください](https://azure.microsoft.com/pricing/details/container-registry)。
[Azure Service Fabric](https://azure.microsoft.com/services/service-fabric) | 常時接続可能でスケーラブルな分散アプリを構築し、運用します | コンピューティング ノードのサイズ、場所、期間に基づくコスト。 [詳細については、こちらを参照してください](https://azure.microsoft.com/pricing/details/service-fabric)。
[Azure DevOps](https://docs.microsoft.com/azure/azure-portal/tutorial-azureportal-devops) | 継続的インテグレーションと継続的デプロイ (CI/CD) パイプラインをアプリの開発に提供します。 パイプラインは、アプリ コードを管理する Git リポジトリで始まり、パッケージおよびその他のビルド成果物を生成するためのビルド システム、開発、テスト、および運用環境での変更を配置するリリース管理システムへと続きます。

## <a name="prerequisites"></a>前提条件

このシナリオを実行するために Contoso に必要なものを以下に示します。

<!-- markdownlint-disable MD033 -->

**必要条件** | **詳細**
--- | ---
**Azure サブスクリプション** | Contoso は、この記事シリーズの前の記事でサブスクリプションを作成しました。 Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/pricing/free-trial)を作成してください。<br/><br/> 無料アカウントを作成する場合、サブスクリプションの管理者としてすべてのアクションを実行できます。<br/><br/> 既存のサブスクリプションを使用しており、管理者でない場合は、管理者に依頼して所有者アクセス許可または共同作成者アクセス許可を割り当ててもらう必要があります。
**Azure インフラストラクチャ** | [Contoso で以前に Azure インフラストラクチャを設定した方法](./contoso-migration-infrastructure.md)を確認してください。
**開発者の前提条件** | Contoso は、開発者用ワークステーションに次のツールをインストールする必要があります。<br/><br/> - [Visual Studio 2017 Community エディション: バージョン 15.5](https://www.visualstudio.com)<br/><br/> - .NET ワークロードが有効。<br/><br/> - [Git](https://git-scm.com)<br/><br/> - [Service Fabric SDK v 3.0 以降](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started)<br/><br/> - [Windows コンテナーを使用するように設定された Docker CE (Windows 10) または Docker EE (Windows Server)](https://docs.docker.com/docker-for-windows/install)。

<!-- markdownlint-enable MD033 -->

## <a name="scenario-steps"></a>シナリオのステップ

Contoso が移行を実行する方法を次に示します。

> [!div class="checklist"]
>
> - **ステップ 1:Azure に SQL データベース インスタンスをプロビジョニングします。** Contoso は、Azure で SQL インスタンスをプロビジョニングします。 フロントエンド Web VM が Azure コンテナーに移行された後、アプリ Web フロントエンドを含むコンテナー インスタンスは、このデータベースを示すようになります。
> - **手順 2:Azure Container Registry (ACR) を作成する。** Contoso は、Docker コンテナー イメージのエンタープライズ コンテナー レジストリをプロビジョニングします。
> - **ステップ 3:Azure Service Fabric をプロビジョニングする。** Service Fabric クラスターをプロビジョニングします。
> - **手順 4:Service Fabric 証明書を管理する。** Contoso は Azure DevOps Services がクラスターにアクセスするための証明書を設定します。
> - **手順 5:DMA を使用してデータベースを移行する。** Data Migration Assistant を使用してアプリのデータベースを移行します。
> - **手順 6:Azure DevOps Services を設定する。** Contoso は Azure DevOps Services で新しいプロジェクトを設定し、コードを Git リポジトリにインポートします。
> - **手順 7:アプリを変換する。** Contoso は Azure DevOps と SDK ツールを使用してアプリをコンテナーに変換します。
> - **手順 8: ビルドとリリースを設定する。** Contoso は、アプリを作成して ACR および Service Fabric クラスターに発行するためのビルド パイプラインとリリース パイプラインを設定します。
> - **手順 9:アプリを拡張する。** アプリの公開後、Contoso は Azure の機能を利用するためにアプリを拡張し、パイプラインを使用してそれを Azure に再発行します。

## <a name="step-1-provision-an-azure-sql-database"></a>手順 1:Azure SQL Database をプロビジョニングする

Contoso 管理者は、Azure SQL データベースをプロビジョニングします。

1. そこで Azure に **SQL データベース**を作成することを選択します。

    ![SQL をプロビジョニングする](./media/contoso-migration-rearchitect-container-sql/provision-sql1.png)

1. オンプレミス VM 上で実行されているデータベースと一致するようにデータベース名を指定します (**SmartHotel.Registration**)。 ContosoRG リソース グループにデータベースを配置します。 これは、Azure の運用リソースに使用するリソース グループです。

    ![SQL をプロビジョニングする](./media/contoso-migration-rearchitect-container-sql/provision-sql2.png)

1. プライマリ リージョンに新しい SQL Server インスタンス (**sql-smarthotel-eus2**) を設定します。

    ![SQL をプロビジョニングする](./media/contoso-migration-rearchitect-container-sql/provision-sql3.png)

1. サーバーとデータベースのニーズに合わせて価格レベルを設定します。 既に SQL Server のライセンスを持っているため、Azure ハイブリッド特典で費用を節約することにします。

1. サイズ設定については、仮想コアベースの購入を使用し、予想される要件の制限を設定します。

    ![SQL をプロビジョニングする](./media/contoso-migration-rearchitect-container-sql/provision-sql4.png)

1. 次に、データベース インスタンスを作成します。

    ![SQL をプロビジョニングする](./media/contoso-migration-rearchitect-container-sql/provision-sql5.png)

1. インスタンスが作成された後に、データベースを開き、Data Migration Assistant を移行に使用するときに必要な詳細情報を書き留めます。

    ![SQL をプロビジョニングする](./media/contoso-migration-rearchitect-container-sql/provision-sql6.png)

**さらにサポートが必要な場合**

- SQL Database のプロビジョニングの[手順を参照](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal)します。
- 仮想コア リソースの制限の詳細については[こちら](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-elastic-pools)を参照してください。

## <a name="step-2-create-an-acr-and-provision-an-azure-container"></a>手順 2:ACR を作成し、Azure コンテナーをプロビジョニングする

Azure コンテナーは、Web VM からエクスポートされたファイルを使用して作成されます。 コンテナーは Azure Container Registry (ACR) に配置されています。

1. Contoso 管理者は Azure portal で Container Registry を作成します。

     ![Container Registry](./media/contoso-migration-rearchitect-container-sql/container-registry1.png)

1. レジストリの名前 (**contosoacreus2**) を指定し、プライマリ リージョンのインフラストラクチャ リソースに使用するリソース グループ内に配置します。 管理者ユーザーにアクセスを許可し、Premium SKU として設定して、geo レプリケーションを使用できるようにします。

    ![Container Registry](./media/contoso-migration-rearchitect-container-sql/container-registry2.png)

## <a name="step-3-provision-azure-service-fabric"></a>手順 3:Azure Service Fabric をプロビジョニングする

SmartHotel360 コンテナーは、Azure Service Fabric クラスターで実行されます。 Contoso 管理者は、次のように Service Fabric クラスターを作成します。

1. Azure Marketplace から Service Fabric リソースを作成します。

     ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric1.png)

1. **Basic** では、クラスターの一意の DS 名と、オンプレミス VM にアクセスするための資格情報を指定します。 プライマリ リージョンの米国東部 2 の運用リソース グループ (**ContosoRG**) にリソースを配置します。

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric2.png)

1. **[ノード タイプの構成]** に、ノード タイプ名、持続性の設定、VM サイズ、およびアプリケーションのエンドポイントを入力します。

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric3.png)

1. **[キー コンテナーの作成]** で、証明書を格納する新しいキー コンテナーをインフラストラクチャ リソース グループに作成します。

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric4.png)

1. **[アクセス ポリシー]** で、仮想マシンにアクセスしてキー コンテナーを展開できるようにします。

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric5.png)

1. 証明書の名前を指定します。

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric6.png)

1. 概要ページで、証明書のダウンロードに使用されるリンクをコピーします。 Service Fabric クラスターに接続するには、このリンクが必要です。

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric7.png)

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric8.png)

1. 検証に合格したら、クラスターをプロビジョニングします。

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric9.png)

1. [証明書のインポート] ウィザードで、ダウンロードした証明書を開発用マシンにインポートします。 証明書は、クラスターに対する認証に使用されます。

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric10.png)

1. クラスターがプロビジョニングされたら、Service Fabric Cluster Explorer に接続します。

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric11.png)

1. 正しい証明書を選択する必要があります。

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric12.png)

1. Service Fabric Explorer が読み込まれ、Contoso 管理者がクラスターを管理できるようになります。

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric13.png)

## <a name="step-4-manage-service-fabric-certificates"></a>手順 4:Service Fabric 証明書を管理する

Contoso は、クラスターへの Azure DevOps Services アクセスを許可するクラスター証明書が必要です。 Contoso 管理者がこれを設定します。

1. Azure portal を開き、Key Vault を参照します。

1. 証明書を開き、プロビジョニング プロセス中に作成された証明書の拇印をコピーします。

    ![拇印をコピーする](./media/contoso-migration-rearchitect-container-sql/cert1.png)

1. 後で参照できるようにテキスト ファイルにコピーします。

1. これでクライアント証明書が追加され、その証明書がクラスター上の管理用クライアント証明書になります。 これにより、Azure DevOps Services は、リリース パイプラインでアプリをデプロイするためにクラスターに接続できます。 これを実行するために、ポータルで Key Vault を開き、 **[証明書]**  >  **[生成/インポート]** の順に選択します。

    ![クライアント証明書を生成する](./media/contoso-migration-rearchitect-container-sql/cert2.png)

1. 証明書の名前を入力し、**サブジェクト**に X.509 の識別名を指定します。

     ![証明書の名前](./media/contoso-migration-rearchitect-container-sql/cert3.png)

1. 証明書が作成されると、PFX 形式でローカルにダウンロードされます。

     ![証明書をダウンロードする](./media/contoso-migration-rearchitect-container-sql/cert4.png)

1. これで Key Vault の証明書一覧に戻るので、作成されたクライアント証明書の拇印をコピーします。 これをテキスト ファイルに保存します。

     ![クライアント証明書の拇印](./media/contoso-migration-rearchitect-container-sql/cert5.png)

1. Azure DevOps Services デプロイの場合、証明書の Base64 値を決定する必要があります。 これは、PowerShell を使用してローカルの開発者用ワークステーションで行います。 後で使用するために、出力をテキスト ファイルに貼り付けます。

    ```powershell
    [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes("C:\path\to\certificate.pfx"))
    ```

     ![Base64 値](./media/contoso-migration-rearchitect-container-sql/cert6.png)

1. 最後に、新しい証明書を Service Fabric クラスターに追加します。 これを行うには、ポータルでクラスターを開き、 **[セキュリティ]** を選択します。

     ![クライアント証明書を追加する](./media/contoso-migration-rearchitect-container-sql/cert7.png)

1. **[追加]**  >  **[管理クライアント]** の順に選択し、新しいクライアント証明書の拇印に貼り付けます。 次に、 **[追加]** を選択します。 この処理には最大 15 分かかることがあります。

     ![クライアント証明書を追加する](./media/contoso-migration-rearchitect-container-sql/cert8.png)

## <a name="step-5-migrate-the-database-with-dma"></a>手順 5:DMA を使用してデータベースを移行する

Contoso 管理者は DMA を使用して SmartHotel360 データベースを移行できるようになりました。

### <a name="install-dma"></a>DMA をインストールする

1. [Microsoft ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=53595) からオンプレミスの SQL Server VM (**SQLVM**) にツールをダウンロードします。

1. VM 上でセットアップ (DownloadMigrationAssistant.msi) を実行します。

1. **[完了]** ページで、ウィザードを終了する前に **[Microsoft Data Migration Assistant の起動]** を選択します。

### <a name="configure-the-firewall"></a>ファイアウォールの構成

Contoso 管理者は、Azure SQL Database に接続するために、アクセスを許可するようにファイアウォール ルールを設定します。

1. データベースの **[ファイアウォールと仮想ネットワーク]** プロパティで、Azure サービスへのアクセスを許可し、オンプレミス SQL Server VM のクライアント IP アドレスのルールを追加します。

1. サーバーレベルのファイアウォール ルールが作成されます。

    ![ファイアウォール](./media/contoso-migration-rearchitect-container-sql/sql-firewall.png)

**さらにサポートが必要な場合**

Azure SQL Database のファイアウォール ルールの作成と管理の詳細については、[こちら](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)を参照してください。

### <a name="migrate"></a>移行

Contoso 管理者はデータベースを移動できるようになりました。

1. DMA で新しいプロジェクト (**SmartHotelDB**) を作成し、 **[移行]** を選択します。

1. ソース サーバーの種類として **SQL Server**、ターゲットとして **Azure SQL Database** を選択します。

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-1.png)

1. 移行の詳細では、ソース サーバーとして **SQLVM** を追加し、**SmartHotel.Registration** データベースを追加します。

     ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-2.png)

1. 認証に関連しているようなエラーが表示されます。 ただし、調査の結果、問題はデータベース名のピリオド (.) であることがわかりました。 この問題を解決するため、回避策として、**SmartHotel-Registration** という名前を使用して新しい SQL Database をプロビジョニングすることに決めました。 再度 DMA を実行すると、**SmartHotel-Registration** を選択してウィザードを続行できます。

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-3.png)

1. **[オブジェクトの選択]** で、データベース テーブルを選択し、SQL スクリプトを生成します。

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-4.png)

1. DMA によってスクリプトが作成された後、 **[スキーマの配置]** を選択します。

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-5.png)

1. DMA で配置が成功したことを確認します。

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-6.png)

1. 次は移行の開始です。

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-7.png)

1. 移行が完了すると、Contoso はデータベースが Azure SQL インスタンス上で実行されていることを確認できます。

     ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-8.png)

1. Azure portal で余計な SQL データベース **SmartHotel.Registration** を削除します。

     ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-9.png)

## <a name="step-6-set-up-azure-devops-services"></a>手順 6:Azure DevOps Services を設定する

Contoso は、アプリケーションのために DevOps インフラストラクチャとパイプラインを構築する必要があります。 これを行うために、Contoso 管理者は新しい Azure DevOps プロジェクトを作成し、コードをインポートしてから、パイプラインを構築してリリースします。

1. Contoso Azure DevOps アカウントで、新しいプロジェクト (**ContosoSmartHotelRearchitect**) を作成し、バージョン コントロールに **[Git]** を選択します。
![新しいプロジェクト](./media/contoso-migration-rearchitect-container-sql/vsts1.png)

1. アプリ コードを現在保持している Git リポジトリをインポートします。 [パブリック リポジトリ](https://github.com/Microsoft/SmartHotel360-internal-booking-apps)内にあるので、ダウンロードすることができます。

    ![アプリ コードをダウンロードする](./media/contoso-migration-rearchitect-container-sql/vsts2.png)

1. コードをインポートしたら、Visual Studio をリポジトリに接続し、チーム エクスプローラーを使用してコードを複製します。

1. リポジトリが開発者のコンピューターに複製された後に、アプリ用のソリューション ファイルを開きます。 Web アプリと wcf サービスは、ファイル内にそれぞれ別のプロジェクトを持っています。

    ![ソリューション ファイル](./media/contoso-migration-rearchitect-container-sql/vsts4.png)

## <a name="step-7-convert-the-app-to-a-container"></a>手順 7:アプリをコンテナーに変換する

オンプレミス アプリは従来の 3 階層のアプリです。

- これには WebForms と、SQL Server に接続する WCF サービスが含まれています。
- Entity Framework を使用して SQL Database のデータと統合し、WCF サービスを介して公開します。
- WebForms アプリケーションは、WCF サービスと対話します。

Contoso 管理者は、以下のように Visual Studio と SDK Tools を使用してアプリをコンテナーに変換します。

1. Visual Studio を使用して、ローカル リポジトリの **SmartHotel360-internal-booking-apps\src\Registration** ディレクトリにあるオープン ソリューション ファイル (SmartHotel.Registration.sln) を確認します。 2 つのアプリが表示されます。 Web フロントエンドの SmartHotel.Registration.Web と WCF サービス アプリの SmartHotel.Registration.WCF です。

    ![コンテナー](./media/contoso-migration-rearchitect-container-sql/container2.png)

1. Web アプリを右クリックし、 **[追加]**  >  **[Container Orchestrator Support]\(コンテナー オーケストレーター サポート\)** の順に選択します。

1. **[Add Container Orchestra Support]\(コンテナー オーケストレーター サポートの追加\)** で **[Service Fabric]** を選択します。

    ![コンテナー](./media/contoso-migration-rearchitect-container-sql/container3.png)

1. SmartHotel.Registration.WCF アプリについて同じプロセスを繰り返します。

1. 次にソリューションがどのように変化したかを確認します。

    - 新しいアプリは **SmartHotel.RegistrationApplication/** です
    - ここには次の 2 つのサービスが含まれています。**SmartHotel.Registration.WCF** と **SmartHotel.Registration.Web** です。

    ![コンテナー](./media/contoso-migration-rearchitect-container-sql/container4.png)

1. Visual Studio によって Docker ファイルが作成され、必要なイメージが開発者用マシンのローカルにプルダウンされました。

    ![コンテナー](./media/contoso-migration-rearchitect-container-sql/container5.png)

1. Visual Studio によってマニフェスト ファイル (**ServiceManifest.xml**) が作成され、開かれます。 このファイルは、コンテナーが Azure にデプロイされるときにコンテナーを構成する方法を Service Fabric に指示します。

    ![コンテナー](./media/contoso-migration-rearchitect-container-sql/container6.png)

1. もう 1 つのマニフェスト ファイル (**ApplicationManifest.xml) には、コンテナーの構成アプリケーションが含まれています。

    ![コンテナー](./media/contoso-migration-rearchitect-container-sql/container7.png)

1. **ApplicationParameters/Cloud.xml** ファイルを開き、Azure SQL データベースにアプリを接続するように接続文字列を更新します。 接続文字列は Azure portal のデータベースで確認できます。

    ![接続文字列](./media/contoso-migration-rearchitect-container-sql/container8.png)

1. 更新されたコードをコミットし、Azure DevOps Services にプッシュします。

    ![Commit](./media/contoso-migration-rearchitect-container-sql/container9.png)

## <a name="step-8-build-and-release-pipelines-in-azure-devops-services"></a>手順 8:Azure DevOps Services でパイプラインをビルドおよびリリースする

次に、Contoso 管理者は、DevOps が実行するアクションに対するビルドおよびリリース プロセスを実行するように Azure DevOps Services を構成します。

1. Azure DevOps Services では、 **[Build and release]\(ビルドとリリース\)**  >  **[新しいパイプライン]** の順に選択します。

    ![新しいパイプライン](./media/contoso-migration-rearchitect-container-sql/pipeline1.png)

1. **[Azure DevOps Services Git]** と 関連するリポジトリを選択します。

    ![Git とリポジトリ](./media/contoso-migration-rearchitect-container-sql/pipeline2.png)

1. **[テンプレートの選択]** で Docker がサポートされているファブリックを選択します。

     ![ファブリックと Docker](./media/contoso-migration-rearchitect-container-sql/pipeline3.png)

1. アクション タグ イメージを変更して**イメージをビルド**し、プロビジョニングされた ACR を使用するようにタスクを構成します。

     ![レジストリ](./media/contoso-migration-rearchitect-container-sql/pipeline4.png)

1. **[Push images]\(イメージのプッシュ\)** タスクでは、ACR にプッシュされるイメージを構成し、最新のタグを含むように選択します。

1. **[トリガー]** で継続的インテグレーションを有効にして、マスター ブランチを追加します。

    ![トリガー](./media/contoso-migration-rearchitect-container-sql/pipeline5.png)

1. **[Save and Queue]\(保存とキュー\)** を選択してビルドを開始します。

1. ビルドが成功したら、リリース パイプラインに移動します。 Azure DevOps Services では、 **[リリース]**  >  **[新しいパイプライン]** の順に選択します。

    ![リリース パイプライン](./media/contoso-migration-rearchitect-container-sql/pipeline6.png)

1. **Azure Service Fabric デプロイ** テンプレートを選択し、ステージに名前 (**SmartHotelSF**) を付けます。

    ![環境](./media/contoso-migration-rearchitect-container-sql/pipeline7.png)

1. パイプライン名を指定します (**ContosoSmartHotel360Rearchitect**)。 ステージには **[1 job, 1 task]\(1 ジョブ、1 タスク\)** を選択して Service Fabric デプロイを構成します。

    ![フェーズとタスク](./media/contoso-migration-rearchitect-container-sql/pipeline8.png)

1. 次に、 **[新規]** を選択して新しいクラスター接続を追加します。

    ![新しい接続](./media/contoso-migration-rearchitect-container-sql/pipeline9.png)

1. **[Add Service Fabric service connection]\(Service Fabric サービス接続の追加\)** で、接続を構成し、アプリをデプロイするために Azure DevOps Services に使用される認証設定を構成します。 クラスター エンドポイントは、Azure portal で確認できます。Contoso はプレフィックスに **tcp://** を追加しています。

1. 収集した証明書情報は、 **[Server Certificate Thumbprint]\(サーバー証明書の拇印\)** および **[クライアント証明書]** に入力されます。

    ![Certificate](./media/contoso-migration-rearchitect-container-sql/pipeline10.png)

1. パイプライン、 **[Add an artifact]\(アーティファクトの追加\)** の順に選択します。

     ![アーティファクト](./media/contoso-migration-rearchitect-container-sql/pipeline11.png)

1. 最新バージョンを使用して、プロジェクトとビルド パイプラインを選択します。

     ![Build](./media/contoso-migration-rearchitect-container-sql/pipeline12.png)

1. アーティファクト上の稲妻にチェックマークが付いていることを確認します。

     ![アーティファクトの状態](./media/contoso-migration-rearchitect-container-sql/pipeline13.png)

1. さらに、継続的配置トリガーが有効になっていることを確認します。
   ![有効な継続的配置](./media/contoso-migration-rearchitect-container-sql/pipeline14.png)

1. **[保存]**  >  **[Create a release]\(リリースの作成\)** の順に選択します。

    ![Release](./media/contoso-migration-rearchitect-container-sql/pipeline15.png)

1. デプロイが完了すると、SmartHotel360 で Service Fabric が実行されます。

    ![発行](./media/contoso-migration-rearchitect-container-sql/publish4.png)

1. アプリに接続するために、Service Fabric ノードの前面にある Azure Load Balancer のパブリック IP アドレスにトラフィックを誘導します。

    ![発行](./media/contoso-migration-rearchitect-container-sql/publish5.png)

## <a name="step-9-extend-the-app-and-republish"></a>手順 9:アプリを拡張して再発行する

SmartHotel360 アプリとデータベースが Azure で実行された後、Contoso はアプリを拡張したいと考えています。

- Contoso の開発者は、Service Fabric クラスター上で動作する新しい .NET Core アプリケーションのプロトタイプを作成しています。
- アプリは、Cosmos DB からセンチメント データをプルするために使用されます。
- このデータは、Serverless Azure Function と Azure Cognitive Services Text Analysis API を使用して処理されるツイートの形式になります。

### <a name="provision-azure-cosmos-db"></a>Azure Cosmos DB をプロビジョニングする

最初の手順として、Contoso 管理者は Azure Cosmos データベースをプロビジョニングします。

1. Azure Marketplace から Azure Cosmos DB リソースを作成します。

    ![Extend](./media/contoso-migration-rearchitect-container-sql/extend1.png)

1. データベース名 (**contososmarthotel**) を指定し、SQL API を選択し、プライマリ リージョンの米国東部 2 の運用リソース グループに配置します。

    ![Extend](./media/contoso-migration-rearchitect-container-sql/extend2.png)

1. **[作業の開始]** で **[データ エクスプローラー]** を選択し、新しいコレクションを追加します。

1. **[コレクションの追加]** で ID を指定し、ストレージの容量とスループットを設定します。

    ![Extend](./media/contoso-migration-rearchitect-container-sql/extend3.png)

1. ポータルで新しいデータベースを開き、 **[コレクション]**  >  **[ドキュメント]** の順に選択し、 **[新しいドキュメント]** を選択します。

1. 次の JSON コードをドキュメント ウィンドウに貼り付けます。 これは単一のツイート形式のサンプル データです。

    ```json
    {
            "id": "2ed5e734-8034-bf3a-ac85-705b7713d911",
            "tweetId": 927750234331580911,
            "tweetUrl": "https://twitter.com/status/927750237331580911",
            "userName": "CoreySandersWA",
            "userAlias": "@CoreySandersWA",
            "userPictureUrl": "",
            "text": "This is a tweet about #SmartHotel360",
            "language": "en",
            "sentiment": 0.5,
            "retweet_count": 1,
            "followers": 500,
            "hashtags": [
                ""
            ]
    }
    ```

    ![Extend](./media/contoso-migration-rearchitect-container-sql/extend4.png)

1. Cosmos DB エンドポイントと認証キーを確認します。 これらはアプリでコレクションに接続するために使用されます。 データベースで、 **[キー]** を選択し、URI と主キーをメモ帳にコピーします。

    ![Extend](./media/contoso-migration-rearchitect-container-sql/extend5.png)

### <a name="update-the-sentiment-app"></a>センチメント アプリを更新する

Cosmos DB がプロビジョニングされたら、Contoso 管理者はそれに接続するようにアプリを構成できます。

1. Visual Studio のソリューション エクスプローラーで ApplicationModern\ApplicationParameters\cloud.xml ファイルを開きます。

    ![センチメント アプリ](./media/contoso-migration-rearchitect-container-sql/sentiment1.png)

1. 次の 2 つのパラメーターを入力します。

   ```xml
   <Parameter Name="SentimentIntegration.CosmosDBEndpoint" Value="[URI]" />
   ```

   ```xml
   <Parameter Name="SentimentIntegration.CosmosDBAuthKey" Value="[Key]" />
   ```

    ![センチメント アプリ](./media/contoso-migration-rearchitect-container-sql/sentiment2.png)

### <a name="republish-the-app"></a>アプリを再発行する

アプリを拡張した後、Contoso 管理者はパイプラインを使用して Azure に再発行します。

1. コードをコミットして Azure DevOps Services にプッシュします。 これでビルドとリリース パイプラインが開始されます。

1. ビルドとデプロイが完了すると、SmartHotel360 で Service Fabric が実行されます。 Service Fabric 管理コンソールに 3 つのサービスが表示されるようになりました。

    ![再発行](./media/contoso-migration-rearchitect-container-sql/republish3.png)

1. これで、サービスをクリックして、SentimentIntegration アプリが起動していることを確認できるようになりました。

    ![再発行](./media/contoso-migration-rearchitect-container-sql/republish4.png)

## <a name="clean-up-after-migration"></a>移行後にクリーンアップする

移行後、Contoso は、以下のクリーンアップ手順を完了する必要があります。

- vCenter のインベントリからオンプレミスの VM を削除します。
- ローカルのバックアップ ジョブからから VM を削除します。
- SmartHotel360 アプリの新しい場所を示すように社内ドキュメントを更新します。 Azure SQL データベースでデータベースは実行中と表示され、Service Fabric でフロント エンドは実行中と表示されます。
- 使用停止されている VM と対話しているリソースがないか確認します。また、関連する設定やドキュメントがあれば、更新して新しい構成を反映します。

## <a name="review-the-deployment"></a>デプロイを再調査する

リソースを Azure に移行したら、新しいインフラストラクチャを完全に操作可能にして、セキュリティ保護する必要があります。

### <a name="security"></a>Security

- Contoso 管理者は新しい **SmartHotel-Registration** データベースがセキュリティで保護されていることを確認する必要があります。 [詳細については、こちらを参照してください](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview)。
- 特に、証明書で SSL を使用するようにコンテナーを更新する必要があります。
- Key Vault を使用して、Service Fabric アプリのシークレットを保護することを検討する必要があります。 [詳細については、こちらを参照してください](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management)。

### <a name="backups"></a>バックアップ

- Contoso は、Azure SQL Database のバックアップ要件を確認する必要があります。 [詳細については、こちらを参照してください](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups)。
- Contoso 管理者は、データベースのリージョンのフェールオーバーを用意するようにフェールオーバー グループを実装することを検討する必要があります。 [詳細については、こちらを参照してください](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview)。
- ACR Premium SKU の geo レプリケーションを利用できます。 [詳細については、こちらを参照してください](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication)。
- Contoso は、Web App for Containers がリリースされた時点で、メインの米国東部 2 リージョンと米国中部リージョンに Web アプリをデプロイすることを検討する必要があります。 Contoso 管理者は、リージョンの障害が発生した場合に確実にフェールオーバーするように Traffic Manager を構成できます。
- Cosmos DB は自動的にバックアップされます。 Contoso は、このプロセスの詳細情報を[こちらで確認](https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore)します。

### <a name="licensing-and-cost-optimization"></a>ライセンスとコストの最適化

- すべてのリソースをデプロイした後、Contoso は[インフラストラクチャ計画](./contoso-migration-infrastructure.md#set-up-tagging)に基づいて Azure タグを割り当てる必要があります。
- すべてのライセンスは、Contoso が使用している PaaS サービスのコストに組み込まれています。 これは EA から差し引かれます。
- Contoso は、Microsoft の子会社である Cloudyn からライセンスが供与される Azure Cost Management を有効にします。 Azure やその他のクラウド リソースの利用や管理に役立つ、マルチクラウド対応のコスト管理ソリューションです。 Azure Cost Management の詳細については、[こちら](https://docs.microsoft.com/azure/cost-management/overview)を参照してください。

## <a name="conclusion"></a>まとめ

この記事で、Contoso は、アプリのフロントエンド VM を Service Fabric に移行することで、Azure の SmartHotel360 アプリをリファクターしました。 アプリ データベースを Azure SQL データベースに移行しました。
