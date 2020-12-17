---
title: Azure にオンプレミスのアプリケーションをリビルドする
description: Contoso が Azure App Service、Azure Kubernetes Service (AKS)、Azure Cosmos DB、Azure Functions、Azure Cognitive Services を使用して Azure にアプリケーションをリビルドする方法について説明します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 7/1/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: migrate
ms.custom: internal
ms.openlocfilehash: 281132c0668f6556520be15540c084376c05e8ae
ms.sourcegitcommit: 17743480a4b1d0516d61f1045380597203ec1de0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/15/2020
ms.locfileid: "97521867"
---
<!-- docutune:casing "Enable .NET" SmartHotel360-Backend Pet.Checker "contoso-datacenter" PetCheckerFunction -->

<!-- cSpell:ignore SQLVM WEBVM contoso contosohost vcenter contosodc smarthotel contososmarthotel smarthotelcontoso smarthotelpetchecker petchecker smarthotelakseus smarthotelacreus smarthotelpets kubectl contosodevops visualstudio azuredeploy cloudapp smarthotelsettingsurl appsettings -->

# <a name="rebuild-an-on-premises-application-in-azure"></a>Azure にオンプレミスのアプリケーションをリビルドする

この記事では、Contoso という架空の会社が、Azure への移行の一環として、VMware 仮想マシン (VM) 上で実行される 2 階層の Windows .NET アプリケーションをリビルドする方法を説明します。 Contoso は、フロントエンド VM を、Azure App Service Web アプリに移行します。 Contoso は、アプリケーションのバックエンドを、Azure Kubernetes Service (AKS) によって管理されるコンテナーにデプロイされたマイクロサービスを使用してビルドします。 サイトは、ペットの写真の機能を提供する Azure Functions と対話します。

この例で使用される SmartHotel360 アプリケーションは、オープンソース ライセンスの下で提供されています。 独自のテスト目的に沿って使用する場合は、[GitHub](https://github.com/Microsoft/SmartHotel360) からダウンロードできます。

## <a name="business-drivers"></a>ビジネス ドライバー

Contoso の IT リーダーシップ チームは、ビジネス パートナーと密接に連絡を取り合い、彼らがこの移行で何を達成しようとしているのかを理解しました。

- **ビジネスの成長への対応。** Contoso は成長を続けており、Contoso の Web サイトで、差別化されたエクスペリエンスを顧客に提供したいと考えています。
- **機敏である。** Contoso は、グローバル経済で成功を収めるために、より迅速に市場の変化に対応できる必要があります。
- **スケール。** ビジネスが順調に成長していく中で、Contoso IT チームは、同じペースで拡張可能なシステムを提供する必要があります。
- **コストを削減する。** Contoso はライセンス コストを最小限に抑えたいと考えています。

## <a name="migration-goals"></a>移行の目標

Contoso クラウド チームは、この移行のためのアプリケーションの要件を設定しました。 これらの要件を使用して、最良の移行方法を決定しました。

- このアプリケーションは、オンプレミスにおける現状と同様、Azure でもクリティカルなものとなります。 優れたパフォーマンスを発揮し、容易にスケールできる必要があります。
- このアプリケーションでは、サービスとしてのインフラストラクチャ (IaaS) コンポーネントを使用しません。 サービスとしてのプラットフォーム (PaaS) またはサーバーレス サービスを使用するようにすべてをビルドする必要があります。
- アプリケーションのビルドはクラウド サービスで実行する必要があり、コンテナーはクラウド内のプライベートなエンタープライズ規模のレジストリに存在する必要があります。
- アプリケーションで下された決定がホテルで受け入れられる必要があるため、ペットの写真に使用される API サービスは正確かつ実世界で信頼できるものである必要があります。 アクセス権を付与されたペットは、ホテルでの滞在を許可されます。
- DevOps パイプラインの要件を満たすために、Contoso ではソース コード管理に Azure Repos の Git リポジトリを使用します。 コードのビルドと Azure App Service、Azure Functions、および AKS へのデプロイには、自動ビルドとリリースが使用されます。
- バックエンドのマイクロサービスと、フロントエンドの Web サイトに対して、別個の継続的インテグレーションと継続的デプロイ (CI/CD) パイプラインが必要です。
- バックエンド サービスとフロントエンド Web アプリとでは、リリース サイクルが異なります。 この要件を満たすため、Contoso は 2 つの異なるパイプラインをデプロイすることになります。
- Contoso では、すべてのフロント エンド Web サイトのデプロイには上長の承認が必要であり、CI/CD パイプラインではこれを提供する必要があります。

## <a name="solution-design"></a>ソリューション設計

Contoso は目標と要件を決定した後、デプロイ ソリューションを設計およびレビューし、移行に使用する Azure サービスを含め、移行プロセスを決めます。

### <a name="current-application"></a>現在のアプリケーション

- SmartHotel360 オンプレミス アプリケーションは 2 つの VM (`WEBVM` と `SQLVM`) に階層化されています。
- これらの VM は、VMware ESXi ホスト `contosohost1.contoso.com` (バージョン 6.5) 上に配置されています。
- VMware 環境は、VM で実行中のvCenter Server 6.5 (`vcenter.contoso.com`) によって管理されています。
- Contoso には、オンプレミスのデータセンター (`contoso-datacenter`) があり、オンプレミスのドメイン コントローラー (`contosodc1`) が含まれています。
- Contoso データセンター内のオンプレミス VM は、移行が行われた後に使用停止にされます。

### <a name="proposed-architecture"></a>提案されたアーキテクチャ

- アプリケーションのフロントエンドは、プライマリ Azure リージョンに Azure App Service の Web アプリとしてデプロイされます。
- Azure 関数がペットの写真をアップロードし、サイトはこの機能と対話します。
- ペットの写真の関数には、Azure Cosmos DB と共に Azure Cognitive Services の Computer Vision API が使用されています。
- サイトのバックエンドは、マイクロ サービスを使用してビルドされます。 これらのマイクロサービスは、AKS で管理されるコンテナーにデプロイされます。
- コンテナーは Azure DevOps を使用してビルドされ、Azure Container Registry にプッシュされます。
- Contoso は当面、Visual Studio を使用して Web アプリと関数コードを手動でデプロイする予定です。
- マイクロサービスは、Kubernetes コマンドライン ツールを呼び出す PowerShell スクリプトを使用してデプロイします。

    ![Azure への移行シナリオのアーキテクチャ図。](./media/contoso-migration-rebuild/architecture.png)

    "_図 1:シナリオのアーキテクチャ_

### <a name="solution-review"></a>ソリューションのレビュー

Contoso は、長所と短所の一覧をまとめて、提案されたデザインを評価します。

| 考慮事項 | 詳細 |
| --- | --- |
| **長所** | Contoso はエンドツーエンドのデプロイに PaaS とサーバーレス ソリューションを使用することで、管理に要する時間を大幅に削減できます。 <br><br> マイクロサービス ベースのアーキテクチャへの移行により、Contoso は時間の経過と共にソリューションを容易に拡張できるようになります。 <br><br> 新しい機能は、既存のソリューションのコード ベースを妨げることなくオンライン状態にすることができます。 <br><br> Web アプリは複数のインスタンスで構成されるため、単一障害点は生まれません。 <br><br> アプリケーションがさまざまなトラフィック量を処理できるように、自動スケールが有効化されます。 <br><br> PaaS サービスへの移行に伴い、Contoso は Windows Server 2008 R2 オペレーティング システム上で実行されている古いソリューションを廃止できます。 <br><br> Azure Cosmos DB にはフォールト トレランスが組み込まれており、Contoso による構成は必要ありません。 これは、データ層が単一のフェールオーバー ポイントでなくなることを意味します。 |
| **短所** | コンテナーは他の移行オプションより複雑です。 Contoso にとって、学習曲線が問題になる可能性があります。 この曲線にもかかわらず、Contoso は価値を提供する新しいレベルの複雑さを導入します。 <br><br> Azure、コンテナー、アプリケーションのマイクロサービスを理解し、サポートする Contoso の運用チームを立ち上げる必要があります。 <br><br> Contoso は、ソリューション全体の DevOps を完全に実装していません。 Contoso は、AKS、Azure Functions、および Azure App Service へのサービスのデプロイで、そのことを考慮する必要があります。 |

### <a name="migration-process"></a>移行プロセス

1. Contoso では、Azure Container Registry、AKS、および Azure Cosmos DB をプロビジョニングします。
2. デプロイ用のインフラストラクチャ (Azure App Service Web アプリ、ストレージ アカウント、関数、API を含む) をプロビジョニングします。
3. インフラストラクチャの準備が整ったら、Azure DevOps を使用してマイクロサービスのコンテナー イメージをビルドし、コンテナー レジストリにプッシュします。
4. Contoso は、PowerShell スクリプトを使用してこれらのマイクロサービスを AKS にデプロイします。
5. 最後に、関数と Web アプリをデプロイします。

    ![移行プロセスの図 (準備からクラウドへのデプロイまで)。](./media/contoso-migration-rebuild/migration-process.png)

    "_図 2:移行プロセス_

### <a name="azure-services"></a>Azure サービス

| サービス | 説明 | コスト |
|---|---|---|
| [AKS](/sql/dma/dma-overview?view=ssdt-18vs2017) | Kubernetes の管理、デプロイ、操作を簡略化します。 完全に管理された Kubernetes コンテナー オーケストレーション サービスを提供します。 | AKS は無料サービスです。 VM と、関連するストレージおよびネットワーク リソースの使用した分に対してのみ料金が発生します。 [詳細については、こちらを参照してください](https://azure.microsoft.com/pricing/details/kubernetes-service)。 |
| [Azure Functions](https://azure.microsoft.com/services/functions) | イベント ドリブン型のサーバーレス コンピューティング エクスペリエンスにより、開発を高速化できます。 オンデマンドでスケールできます。 | 使用したリソースに対してのみ料金が発生します。 プランでは、1 秒あたりのリソースの使用量と実行回数に基づいて課金されます。 [詳細については、こちらを参照してください](https://azure.microsoft.com/pricing/details/functions)。 |
| [Azure Container Registry](https://azure.microsoft.com/services/container-registry) | あらゆる種類のコンテナー デプロイのイメージを保存します。 | コストは機能、ストレージ、使用期間に基づいて発生します。 [詳細については、こちらを参照してください](https://azure.microsoft.com/pricing/details/container-registry)。 |
| [Azure App Service](https://azure.microsoft.com/services/app-service/containers) | あらゆるプラットフォームで実行されるエンタープライズグレードの Web アプリ、モバイル アプリ、API アプリをすばやくビルド、デプロイ、スケーリングできます。 | App Service プランは、1 秒単位で課金されます。 [詳細については、こちらを参照してください](https://azure.microsoft.com/pricing/details/app-service/windows)。 |

## <a name="prerequisites"></a>前提条件

このシナリオで Contoso に必要なことを以下に示します。

| 必要条件 | 詳細 |
| --- | --- |
| Azure サブスクリプション | <li> 前の記事で、Contoso はサブスクリプションを作成しました。 Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/free)を作成してください。 <li> 無料アカウントを作成する場合、サブスクリプションの管理者としてすべてのアクションを実行できます。 <li> 既存のサブスクリプションを使用するものの、管理者でない場合は、管理者に依頼して所有者アクセス許可または共同作成者アクセス許可を自分に割り当ててもらう必要があります。 |
| Azure インフラストラクチャ | <li> [Contoso で Azure インフラストラクチャを設定する方法](./contoso-migration-infrastructure.md)を確認してください。 |
| 開発者の前提条件 | Contoso は、開発者用ワークステーションに次のツールをインストールする必要があります。 <li> [Visual Studio Community 2017 バージョン 15.5](https://visualstudio.microsoft.com) <li> .NET ワークロードが有効 <li> [Git](https://git-scm.com) <li> [Azure PowerShell](https://azure.microsoft.com/downloads) <li> [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) <li> [Docker Community Edition (Windows 10) または Docker Enterprise Edition (Windows Server)](https://docs.docker.com/docker-for-windows/install) が Windows コンテナーを使用するように設定済み |

## <a name="scenario-steps"></a>シナリオのステップ

Contoso が移行を実行する方法を次に示します。

> [!div class="checklist"]
>
> - **ステップ 1:AKS と Azure Container Registry をプロビジョニングする。** Contoso は PowerShell を使用して、マネージド AKS クラスターとコンテナー レジストリをプロビジョニングします。
> - **手順 2:Docker コンテナーをビルドする。** Contoso は、Azure DevOps を使用して Docker コンテナーの継続的インテグレーション (CI) を設定し、それらのコンテナーをコンテナー レジストリにプッシュします。
> - **ステップ 3:バックエンドのマイクロサービスをデプロイする。** Contoso は、バックエンドのマイクロサービスによって使用される、残りのインフラストラクチャをデプロイします。
> - **手順 4:フロントエンドのインフラストラクチャをデプロイする。** Contoso は、フロントエンドのインフラストラクチャ (ペットの写真用の Blob Storage、Azure Cosmos DB、Computer Vision API を含む) をデプロイします。
> - **手順 5:バックエンドを移行する。** Contoso は、マイクロサービスをデプロイして AKS 上で実行し、バックエンドを移行します。
> - **手順 6:フロントエンドを公開する。** Contoso は、SmartHotel360 アプリケーションを、Azure App Service と、ペット サービスによって呼び出される関数アプリに公開します。

## <a name="provision-back-end-resources"></a>バックエンド リソースをプロビジョニングする

Contoso の管理者は、AKS と Azure Container Registry を使用して、マネージド Kubernetes クラスターを作成するデプロイ スクリプトを実行します。 このセクションの手順では、[SmartHotel360-Backend](https://github.com/Microsoft/SmartHotel360-Backend) GitHub リポジトリを使用します。 このリポジトリには、デプロイのこの部分のためのソフトウェアがすべて含まれています。

### <a name="ensure-that-prerequisites-are-met"></a>前提条件が満たされていることを確認する

Contoso の管理者は、開始する前に、デプロイに使用する開発用マシンにすべての前提条件ソフトウェアがインストールされていることを確認します。 Git を使用して、ローカルの開発用マシンにリポジトリをクローンします。

`git clone https://github.com/Microsoft/SmartHotel360-Backend.git`

### <a name="provision-aks-and-azure-container-registry"></a>AKS と Azure Container Registry をプロビジョニングする

Contoso の管理者は、AKS と Azure Container Registry を次のようにプロビジョニングします。

1. Visual Studio Code でフォルダーを開き、スクリプト `gen-aks-env.ps1` が格納されている `/deploy/k8s` ディレクトリに移動します。

2. AKS と Azure Container Registry を使用して、マネージド Kubernetes クラスターを作成するスクリプトを実行します。

    ![Visual Studio Code での AKS スクリプトを示すスクリーンショット。](./media/contoso-migration-rebuild/aks1.png)

    "_図 3:マネージド Kubernetes クラスターの作成。_

3. ファイルを開いた状態で $location パラメーターを `eastus2` に更新し、ファイルを保存します。

    ![eastus2 に更新された AKS $location パラメーターを示すスクリーンショット。](./media/contoso-migration-rebuild/aks2.png)

    "_図 4:ファイルの保存。_

4. **[表示]**  >  **[統合ターミナル]** を選択して、Visual Studio Code で統合ターミナルを開きます。

    !["統合ターミナル" リンクを示すスクリーンショット。](./media/contoso-migration-rebuild/aks3.png)

    _図 5:Visual Studio Code のターミナル。_

5. PowerShell 統合ターミナルで、`Connect-AzureRmAccount` コマンドを使用して Azure にサインインします。 詳細については、[PowerShell の使用開始](/powershell/azure/get-started-azureps)に関するページを参照してください。

    ![PowerShell 統合ターミナルのサインイン ウィンドウのスクリーンショット。](./media/contoso-migration-rebuild/aks4.png)

    _図 6:PowerShell 統合ターミナル。_

6. `az login` コマンドを実行し、Web ブラウザーを使用した認証の手順に従って、Azure CLI の認証を行います。 [Azure CLI でのログイン](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)に関する詳細を確認してください。

    ![Azure CLI の認証ウィンドウを示すスクリーンショット。](./media/contoso-migration-rebuild/aks5.png)

    _図 7:Azure CLI の認証。_

7. 次のコマンドを、リソース グループ名 `ContosoRG`、AKS クラスター名 `smarthotel-aks-eus2`、および新しいレジストリ名を渡して実行します。

    `.\gen-aks-env.ps1  -resourceGroupName ContosoRg -orchestratorName smarthotelakseus2 -registryName smarthotelacreus2`

    ![リソース グループ ペインでの smarthotel コマンドを示すスクリーンショット。](./media/contoso-migration-rebuild/aks6.png)

    _図 8:コマンドの実行。_

8. Azure によって、AKS クラスターのリソースを含む別のリソース グループが作成されます。

    ![リソース グループ作成のスクリーンショット。](./media/contoso-migration-rebuild/aks7.png)

    _図 9:Azure でのリソース グループの作成。_

9. デプロイが完了したら、`kubectl` コマンドライン ツールをインストールします。 このツールは、Azure Cloud Shell では既にインストールされています。

    `az aks install-cli`

10. `kubectl get nodes` コマンドを実行して、クラスターへの接続を確認します。 ノードは、自動的に作成されたリソース グループ内の VM と同じ名前です。

    ![クラスターへの接続の確認を示すスクリーンショット。](./media/contoso-migration-rebuild/aks8.png)

    _図 10:クラスターへの接続の確認。_

11. 次のコマンドを実行して、Kubernetes ダッシュボードを起動します。

    `az aks browse --resource-group ContosoRG --name smarthotelakseus2`

12. ブラウザー タブが開き、ダッシュボードが表示されます。 これは Azure CLI を使用したトンネル接続です。

    ![トンネル接続を示すスクリーンショット。](./media/contoso-migration-rebuild/aks9.png)

    _図 11:トンネル接続。_

## <a name="step-2-configure-the-back-end-pipeline"></a>手順 2:バックエンド パイプラインを構成する

### <a name="create-an-azure-devops-project-and-build"></a>Azure DevOps プロジェクトとビルドを作成する

Contoso は Azure DevOps プロジェクトを作成し、CI ビルドを構成してコンテナーを作成し、それをコンテナー レジストリにプッシュします。 このセクションの手順では、[SmartHotel360-Backend](https://github.com/Microsoft/SmartHotel360-Backend) リポジトリを使用します。

1. `visualstudio.com` から新しい組織 (`contosodevops360.visualstudio.com`) を作成し、Git を使用するようにそれを構成します。

2. バージョン コントロールに **Git**、ワークフローに **アジャイル** を選択して、新しいプロジェクト (`SmartHotelBackend`) を作成します。

    ![Azure DevOps の [新しいプロジェクトの作成] ペインのスクリーンショット。](./media/contoso-migration-rebuild/vsts1.png)

    _図 12:新しいプロジェクトの作成。_

3. [GitHub リポジトリ](https://github.com/Microsoft/SmartHotel360-Backend)をインポートします。

    ![Azure DevOps の [Git リポジトリをインポートする] ペインのスクリーンショット。](./media/contoso-migration-rebuild/vsts2.png)

    _図 13:GitHub リポジトリのインポート。_

4. **[パイプライン]** で、 **[ビルド]** を選択し、リポジトリから、Azure Repos Git をソースとして使用する新しいパイプラインを作成します。

    ![新しいパイプラインを作成するための DevOps ペインのスクリーンショット。](./media/contoso-migration-rebuild/vsts3.png)

    _図 14:新しいパイプラインの作成。_

5. **[空のジョブ]** を選択します。

    ![Azure DevOps の [空のジョブ] リンクのスクリーンショット。](./media/contoso-migration-rebuild/vsts4.png)

    _図 15:空のジョブの開始。_

6. ビルド パイプラインで **[Hosted Linux Preview]\(ホストされている Linux プレビュー\)** を選択します。

    ![Azure DevOps のビルド パイプライン設定画面のスクリーンショット。](./media/contoso-migration-rebuild/vsts5.png)

    _図 16:ビルド パイプラインの設定。_

7. **[Phase 1]\(フェーズ 1\)** で、 **[Docker Compose]** タスクを追加します。 このタスクでは、Docker Compose をビルドします。

    ![Azure DevOps で Docker Compose タスクをビルドする画面のスクリーンショット。](./media/contoso-migration-rebuild/vsts6.png)

    _図 17:Docker Compose のビルド。_

8. 手順を繰り返し、**Docker Compose** タスクをもう 1 つ追加します。 これにより、コンテナーがコンテナー レジストリにプッシュされます。

     ![Azure DevOps でもう 1 つ Docker Compose タスクを追加する画面のスクリーンショット。](./media/contoso-migration-rebuild/vsts7.png)

    _図 18:別の Docker Compose タスクの追加。_

9. 1 番目のタスクを選択し、Azure サブスクリプション、認可、Container Registry を指定してビルドの実行とそのビルドの構成を行います。

    ![Azure DevOps でビルドの作成と構成を行う画面のスクリーンショット。](./media/contoso-migration-rebuild/vsts8.png)

    _図 19:ビルドの作成と構成。_

10. リポジトリの *src* フォルダーにある `docker-compose.yaml` ファイルのパスを指定します。 サービス イメージのビルドを選択し、最新のタグを含めます。 アクションが **[Build service images]\(サービス イメージのビルド\)** に変わると、Azure DevOps タスクの名前が **[Build services automatically]\(サービスを自動的にビルド\)** に変わります。

    ![Azure DevOps における各種タスクビルド指定のスクリーンショット。](./media/contoso-migration-rebuild/vsts9.png)

    _図 20:タスクの詳細。_

11. Contoso は次に、2 番目の Docker タスク (プッシュの実行) を構成します。 サブスクリプションとコンテナー レジストリ (`smarthotelacreus2`) を選択します。

    ![Azure DevOps で 2 つ目の Docker タスクを構成する画面のスクリーンショット。](./media/contoso-migration-rebuild/vsts10.png)

    _図 21:2 番目の Docker タスクの構成。_

12. *docker-compose.yaml* ファイル名を入力し、 **[Push service images]\(サービス イメージのプッシュ\)** を選択し、最新のタグを含めます。 アクションが **[Push service images]\(サービス イメージのプッシュ\)** に変わると、Azure DevOps タスクの名前が **[Push services automatically]\(サービスを自動的にプッシュ\)** に変わります。

    ![Azure DevOps タスクの名前を変更する画面のスクリーンショット。](./media/contoso-migration-rebuild/vsts11.png)

    _図 22:Azure DevOps タスクの名前の変更。_

13. Azure DevOps タスクが構成されたので、Contoso はビルド パイプラインを保存し、ビルド プロセスを開始します。

    ![Azure DevOps でビルド プロセスを開始する画面のスクリーンショット。](./media/contoso-migration-rebuild/vsts12.png)

    _図 23:ビルド プロセスの開始。_

14. ビルド ジョブを選択して、進行状況を確認します。

    ![Azure DevOps でビルドの進行状況を確認する画面のスクリーンショット。](./media/contoso-migration-rebuild/vsts13.png)

    _図 24:進行状況の確認。_

15. ビルドが完了すると、コンテナー レジストリに、マイクロサービスによって使用されるコンテナーが設定された新しいリポジトリが表示されます。

    ![Azure DevOps でビルドの完了後に新しいリポジトリを表示する画面のスクリーンショット。](./media/contoso-migration-rebuild/vsts14.png)

    _図 25:ビルドの終了後に新しいリポジトリを表示。_

### <a name="deploy-the-back-end-infrastructure"></a>バックエンド インフラストラクチャをデプロイする

AKS クラスターを作成し、Docker イメージをビルドしたので、次に Contoso の管理者は、バックエンドのマイクロサービスによって使用される残りのインフラストラクチャをデプロイします。

- このセクションの手順では、[SmartHotel360-Backend](https://github.com/Microsoft/SmartHotel360-Backend) リポジトリを使用します。
- `/deploy/k8s/arm` フォルダーに、すべての項目を作成する 1 つのスクリプトが含まれています。

管理者は、次のようにしてインフラストラクチャをデプロイします。

1. 開発者コマンド プロンプトを開き、Azure サブスクリプションのコマンド `az login` を使用します。

2. `deploy.cmd` ファイルを使用し、次のコマンドを入力して `ContosoRG` リソース グループと `East US 2` リージョンに Azure リソースをデプロイします。

    `.\deploy.cmd azuredeploy ContosoRG -c eastus2`

    ![バックエンドをデプロイする画面のスクリーンショット。](./media/contoso-migration-rebuild/backend1.png)

    _図 26: バックエンドのデプロイ。_

3. Azure portal で、後で使用するために、各データベースの接続文字列をキャプチャします。

    ![各データベースの接続文字列を示すスクリーンショット。](./media/contoso-migration-rebuild/backend2.png)

    _図 27: 各データベースの接続文字列のキャプチャ。_

### <a name="create-the-back-end-release-pipeline"></a>バックエンド リリース パイプラインを作成する

ここで、Contoso の管理者は以下のことを行います。

- サービスへの受信トラフィックを許可する NGINX イングレス コントローラーをデプロイします。
- AKS クラスターにマイクロサービスを送ります。
- 最初のステップとして管理者は、マイクロサービスへの接続文字列を Azure DevOps を使用して更新します。 次に、新しい Azure DevOps リリース パイプラインを構成して、マイクロサービスをデプロイします。
- このセクションの手順では、[SmartHotel360-Backend](https://github.com/Microsoft/SmartHotel360-Backend) リポジトリを使用します。
- 一部の構成設定 (Active Directory B2C など) については、この記事では説明しません。 これらの設定の詳細については、先ほど触れた SmartHotel360-Backend リポジトリを参照してください。

管理者は次のようにしてパイプラインを作成します。

1. Visual Studio で、前に説明したデータベース接続情報で */deploy/k8s/config_local.yml* ファイルを更新します。

    ![Visual Studio の [新しいパイプライン] ボタンを示すスクリーンショット。](./media/contoso-migration-rebuild/back-pipe1.png)

    _図 28: データベース接続。_

2. Azure DevOps を開き、SmartHotel360 プロジェクトの **[リリース]** ペインで **[+ 新しいパイプライン]** を選択します。

    ![Azure DevOps の [新しいパイプライン] ボタンを示すスクリーンショット。](./media/contoso-migration-rebuild/back-pipe2.png)

    _図 29: 新しいパイプラインの作成。_

3. **[空のジョブ]** を選択し、テンプレートを使用せずにパイプラインを開始します。
4. ステージとパイプラインの名前を付けます。

      ![Azure DevOps のステージ名の作成画面を示すスクリーンショット。](./media/contoso-migration-rebuild/back-pipe4.png)

    _図 30: ステージ名。_

      ![Azure DevOps のパイプライン名の作成画面を示すスクリーンショット。](./media/contoso-migration-rebuild/back-pipe5.png)

    _図 31: パイプライン名。_

5. 成果物を追加します。

     ![Azure DevOps でアーティファクトを追加する画面のスクリーンショット。](./media/contoso-migration-rebuild/back-pipe6.png)

    _図 32: 成果物の追加。_

6. ソースの種類として **[Git]** を選択し、SmartHotel360 アプリケーションのプロジェクト、ソース、メイン ブランチを指定します。

    ![[成果物の追加] ペインのスクリーンショット (ソースの種類として Git を選択したところ)。](./media/contoso-migration-rebuild/back-pipe7.png)

    _図 33: [成果物の設定] ペイン。_

7. タスクのリンクを選択します。

    ![Azure DevOps でタスク リンクが強調表示された画面のスクリーンショット。](./media/contoso-migration-rebuild/back-pipe8.png)

    _図 34: タスクのリンク。_

8. PowerShell スクリプトを Azure 環境で実行できるように、新しい Azure PowerShell タスクを追加します。

    ![Azure に新しい PowerShell タスクを追加する画面のスクリーンショット。](./media/contoso-migration-rebuild/back-pipe9.png)

    _図 35: 新しい PowerShell タスクの追加。_

9. タスクの Azure サブスクリプションを選択し、Git レポジトリから `deploy.ps1` スクリプトを選択します。

    ![実行するスクリプトを Git リポジトリから選択する画面のスクリーンショット。](./media/contoso-migration-rebuild/back-pipe10.png)

    _図 36: スクリプトの実行。_

10. スクリプトに引数を追加します。 このスクリプトでは、すべてのクラスター コンテンツ (**イングレス** および **イングレス コントローラー** を除く) が削除され、マイクロサービスがデプロイされます。

    ![スクリプトに追加される引数を示すスクリーンショット。](./media/contoso-migration-rebuild/back-pipe11.png)

    _図 37: スクリプトへの引数の追加。_

11. 指定の Azure PowerShell バージョンを最新バージョンに設定し、パイプラインを保存します。

12. **[新しいリリースを作成する]** ペインに戻って、新しいリリースを手動で作成します。

    ![[新しいリリースを作成する] ペインのスクリーンショット。](./media/contoso-migration-rebuild/back-pipe12.png)

    _図 38: 新しいリリースの手動作成。_

13. リリースの作成後、それを選択し、 **[アクション]** の **[デプロイ]** を選択します。

      ![リリースをデプロイするための [デプロイ] ボタンが強調表示されている画面のスクリーンショット。](./media/contoso-migration-rebuild/back-pipe13.png)

    _図 39: リリースのデプロイ。_

14. デプロイが完了したら、Azure Cloud Shell: `kubectl get services` を使用して、以下のコマンドを実行することでサービスの状態を確認します。

## <a name="step-3-provision-front-end-services"></a>手順 3:フロントエンド サービスをプロビジョニングする

Contoso の管理者は、フロントエンド アプリケーションによって使用されるインフラストラクチャをデプロイする必要があります。 次のものを作成します。

- ペットの画像を格納する Blob Storage コンテナー。
- ペット情報を含むドキュメントを格納する Azure Cosmos DB データベース。
- Web サイト用の Computer Vision API。

このセクションの手順では、[SmartHotel360-Website](https://github.com/microsoft/smartHotel360-website) リポジトリを使用します。

### <a name="create-blob-storage-containers"></a>BLOB ストレージ コンテナーを作成する

1. Azure portal で管理者は、作成済みのストレージ アカウントを開き、 **[BLOB]** をクリックします。
2. パブリック アクセス レベルをコンテナーに設定した新しいコンテナー (`Pets`) を作成します。 ユーザーは、ペットの写真をこのコンテナーにアップロードします。

    ![Azure portal で新しいコンテナーを作成する画面のスクリーンショット。](./media/contoso-migration-rebuild/blob1.png)

    _図 40: 新しいコンテナーの作成。_

3. `settings` という名前の 2 つ目のコンテナーを作成します。 このコンテナーに、フロントエンド アプリのすべての設定が含まれるファイルが配置されます。

    ![Azure portal で 2 つ目の新しいコンテナーを作成する画面のスクリーンショット。](./media/contoso-migration-rebuild/blob2.png)

    _図 41: 2 つ目のコンテナーの作成。_

4. 後で参照できるように、ストレージ アカウントのアクセスの詳細をテキスト ファイルにキャプチャします。

    ![アクセスの詳細をキャプチャするテキスト ファイルのスクリーンショット。](./media/contoso-migration-rebuild/blob2.png)

    _図 42: アクセスの詳細をキャプチャするテキスト ファイル。_

### <a name="provision-an-azure-cosmos-db-database"></a>Azure Cosmos DB のデータベースをプロビジョニングする

Contoso の管理者は、ペットの情報に使用する Azure Cosmos DB データベースをプロビジョニングします。

1. Azure Marketplace で、**Azure Cosmos DB** データベースを作成します。

    ![Azure Marketplace で Azure Cosmos DB データベースを作成する画面のスクリーンショット。](./media/contoso-migration-rebuild/cosmos1.png)

    _図 43: Azure Cosmos DB データベースの作成。_

2. 名前 (`contososmarthotel`) を指定し、SQL API を選択し、メイン リージョン (`East US 2`) の運用リソース グループ `ContosoRG` に配置します。

    ![Azure Cosmos DB データベースの名前とその他の設定のスクリーンショット。](./media/contoso-migration-rebuild/cosmos2.png)

    _図 44: Azure Cosmos DB データベースの名前指定。_

3. 既定の容量およびスループットを指定した新しいコレクションをデータベースに追加します。

    ![Azure Cosmos DB の [コレクションの追加] ペインのスクリーンショット。](./media/contoso-migration-rebuild/cosmos3.png)

    _図 45: データベースへのコレクションの追加。_

4. 後で参照できるように、このデータベースの接続情報をメモします。

    ![Azure Cosmos DB データベースの接続情報のスクリーンショット。](./media/contoso-migration-rebuild/cosmos4.png)

    _図 46: データベースの接続情報。_

### <a name="provision-the-computer-vision-api"></a>Computer Vision API をプロビジョニングする

Contoso の管理者は、Computer Vision API をプロビジョニングします。 この API は、ユーザーによってアップロードされた写真を評価するために関数によって呼び出されます。

1. 管理者が Azure Marketplace で、**Computer Vision** インスタンスを作成します。

     ![Azure Marketplace で新しい Computer Vision インスタンスを作成する画面のスクリーンショット。](./media/contoso-migration-rebuild/vision1.png)

    _図 47: Azure Marketplace での新しいインスタンス。_

2. API (`smarthotelpets`) を運用リソース グループ `ContosoRG` のメイン リージョン (`East US 2`) にプロビジョニングします。

    ![運用リソース グループに API を設定する画面のスクリーンショット。](./media/contoso-migration-rebuild/vision2.png)

    _図 48: 運用リソースグループでの API のプロビジョニング。_

3. 後で参照できるように、この API の接続文字列をテキスト ファイルに保存します。

     ![API の接続設定をテキスト ファイルに保存する画面のスクリーンショット。](./media/contoso-migration-rebuild/vision3.png)

    _図 49: API の接続の設定の保存。_

### <a name="provision-the-azure-web-app"></a>Azure Web アプリをプロビジョニングする

Contoso の管理者は、Azure portal を使用して Web アプリをプロビジョニングします。

1. ポータルで **[Web アプリ]** を選択します。

    ![Azure portal で Web アプリを選択する画面のスクリーンショット。](./media/contoso-migration-rebuild/web-app1.png)

    _図 50: Web アプリの選択。_

2. Web アプリの名前 (`smarthotelcontoso`) を指定し、Windows 上で実行して、運用リソース グループ `ContosoRG` に配置します。 アプリケーションのモニタリング用に新しい Application Insights リソースを作成します。

    ![Web アプリの名前やその他の詳細を指定する画面のスクリーンショット。](./media/contoso-migration-rebuild/web-app2.png)

    _図 51: Web アプリケーションの名前。_

3. 完了したら、管理者はブラウザーでアプリケーションのアドレスにアクセスし、アプリが正常に作成されたかどうかを確認します。

4. Azure portal で、コードのステージング スロットを作成します。 パイプラインはこのスロットにデプロイされます。 このアプローチにより、管理者がリリースを実行するまで、コードは運用環境に移行されません。

    ![Web アプリのステージング スロットを追加する画面のスクリーンショット。](./media/contoso-migration-rebuild/web-app3.png)

    _図 52: Web アプリのステージング スロットを追加する。_

### <a name="provision-the-function-app"></a>関数アプリをプロビジョニングする

Contoso の管理者は、Azure portal で関数アプリをプロビジョニングします。

1. **[Function App]** を選択します。

    ![関数アプリを作成する画面のスクリーンショット。](./media/contoso-migration-rebuild/function-app1.png)

    _図 53: 関数アプリの選択。_

2. 関数アプリ (`smarthotelpetchecker`) の名前を指定します。 運用リソース グループ (`ContosoRG`) に関数アプリを配置します。 ホスティングの場所を **従量課金プラン** に設定し、関数アプリを `East US 2` リージョンに配置します。 監視用の Application Insights インスタンスと共に新しいストレージ アカウントが作成されます。

    ![関数アプリの設定を示すスクリーンショット。](./media/contoso-migration-rebuild/function-app2.png)

    _図 54: 関数アプリの設定。_

3. 関数アプリをデプロイしたら、そのアドレスに管理者がブラウザーでアクセスして、アプリが正常に作成されたことを確認します。

## <a name="step-4-set-up-the-front-end-pipeline"></a>手順 4:フロントエンド パイプラインを設定する

Contoso 管理者は、フロントエンド サイトに 2 つの異なるプロジェクトを作成します。

1. Azure DevOps で、プロジェクト `SmartHotelFrontend` を作成します。

    ![フロントエンド プロジェクトの作成画面のスクリーンショット。](./media/contoso-migration-rebuild/function-app1.png)

    _図 55: フロントエンド プロジェクトの作成。_

2. [SmartHotel360 フロントエンド](https://github.com/Microsoft/SmartHotel360-Website) Git リポジトリを新しいプロジェクトにインポートします。

3. 関数アプリ用の別の Azure DevOps プロジェクト (`SmartHotelPetChecker`) を作成し、[petchecker](https://github.com/sonahander/SmartHotel360-PetCheckerFunction) Git リポジトリをこのプロジェクトにインポートします。

### <a name="configure-the-web-app"></a>Web アプリを構成する

次に、Contoso の管理者は Contoso リソースを使用するように Web アプリを構成します。

1. 管理者が Azure DevOps プロジェクトに接続し、開発用マシンのローカルにリポジトリをクローンします。
2. Visual Studio でフォルダーを開いて、リポジトリ内のすべてのファイルを表示します。

    ![リポジトリ内のすべてのファイルを含んだフォルダーを表示する Visual Studio のスクリーンショット。](./media/contoso-migration-rebuild/configure-webapp1.png)

    _図 56: リポジトリ内のすべてのファイルを表示。_

3. 必要に応じて構成の変更を更新します。

    - Web アプリの起動時に、`SettingsUrl` アプリ設定が検索されます。
    - この変数には、構成ファイルをポイントする URL が含まれている必要があります。
    - 既定では、使用される設定はパブリック エンドポイントです。

4. `/config-sample.json/sample.json` ファイルを更新します。

    - これは、パブリック エンドポイントを使用する場合の Web 用の構成ファイルです。
    - `urls` セクションと `pets_config` セクションを、AKS API エンドポイント、ストレージ アカウント、Azure Cosmos DB データベースの値を使用して編集します。
    - URL は、Contoso が作成する新しい Web アプリの DNS 名と一致している必要があります。
    - Contoso の場合、これは `smarthotelcontoso.eastus2.cloudapp.azure.com` です。

    ![Visual Studio の .json 設定のスクリーンショット。](./media/contoso-migration-rebuild/configure-webapp2.png)

    _図 57: .json 設定。_

5. ファイルの更新後、管理者は、その名前を `smarthotelsettingsurl` に変更して、あらかじめ作成しておいた Blob Storage にアップロードします。

    ![.json ファイルの名前変更とアップロードを行う画面のスクリーンショット。](./media/contoso-migration-rebuild/configure-webapp3.png)

    _図 58: ファイルの名前変更とアップロード。_

6. ファイルを選択肢て URL を取得します。 この URL は、アプリケーションが構成ファイルプル ダウンするときに使用されます。

    ![アプリケーションによって使用されるファイルの URL のスクリーンショット。](./media/contoso-migration-rebuild/configure-webapp4.png)

    _図 59: アプリケーションの URL。_

7. *appsettings.Production.json* ファイルで、`SettingsURL` を、新しいファイルの URL に更新します。

    ![新しいファイルに URL を更新する画面のスクリーンショット。](./media/contoso-migration-rebuild/configure-webapp5.png)

    _図 60: 新しいファイルへの URL の更新。_

### <a name="deploy-the-website-to-azure-app-service"></a>Azure App Service に Web サイトをデプロイする

これで Contoso の管理者は Web サイトを公開できます。

1. Azure DevOps を開き、`SmartHotelFrontend` プロジェクトの **[ビルドとリリース]** で **[+ 新しいパイプライン]** を選択します。
2. **[Azure DevOps Git]** をソースとして選択します。
3. **[ASP.NET Core]** テンプレートを選択します。
4. パイプラインを確認し、 **[Web プロジェクトの発行]** と **[発行されたプロジェクトの zip 圧縮]** が選択されていることを確認します。

    ![Web プロジェクトのパイプライン設定のスクリーンショット。](./media/contoso-migration-rebuild/vsts-publish-front2.png)

    _図 61: パイプラインの設定。_

5. **[トリガー]** で継続的インテグレーションを有効にして、メイン ブランチを追加します。 これにより、メイン ブランチにコミットされた新しいコードをソリューションが持つたびに、ビルド パイプラインが起動します。

    ![継続的インテグレーションを有効にして、メイン ブランチを追加する画面のスクリーンショット。](./media/contoso-migration-rebuild/vsts-publish-front3.png)

    _図 62: 継続的インテグレーションを有効にする。_

6. **[保存してキューに登録]** を選択してビルドを開始します。
7. ビルドが完了したら、**Azure App Service のデプロイ** を使用してリリース パイプラインを構成します。
8. ステージ名 **Staging** を指定します。

    ![環境のステージ名を指定する画面のスクリーンショット。](./media/contoso-migration-rebuild/vsts-publish-front4.png)

    _図 63: 環境の名前指定。_

9. 成果物を追加し、構成したビルドを選択します。

    ![成果物の追加画面のスクリーンショット (ソースの種類として [ビルド] を選択したところ)。](./media/contoso-migration-rebuild/vsts-publish-front5.png)

    _図 64: 成果物の追加。_

10. 成果物の稲妻アイコンを選択し、継続的デプロイを **[有効]** に設定します。

    ![継続的デプロイを有効にする画面のスクリーンショット。](./media/contoso-migration-rebuild/vsts-publish-front6.png)

    _図 65: 継続的なデプロイの有効化。_

11. **[環境]** の **[ステージング]** の下で **[1 job, 1 task]\(1 ジョブ、1 タスク\)** を選択します。

12. サブスクリプションおよび Web アプリ名を選択した後、管理者は **[Azure App Service のデプロイ]** タスクを開きます。 **ステージング** デプロイ スロットを使用するようにデプロイが構成されます。 これによって、このスロットでレビューと承認のコードが自動的にビルドされます。

     ![Web アプリをスロットにデプロイする画面のスクリーンショット。](./media/contoso-migration-rebuild/vsts-publish-front7.png)

    _図 66: スロットへのデプロイ。_

13. **[パイプライン]** で、新しいステージを追加します。

    ![[パイプライン] タブと新しいステージの追加のスクリーンショット。](./media/contoso-migration-rebuild/vsts-publish-front8.png)

    _図 67: 新しいステージの追加。_

14. **[スロットを使用した Azure App Service の配置]** を選択し、環境名を **Prod** とします。

15. **[1 job, 2 tasks]\(1 ジョブ、2 タスク\)** を選択し、次にサブスクリプション、アプリ サービス名、および **ステージング** スロットを選択します。

    ![環境の名前を指定する画面のスクリーンショット。](./media/contoso-migration-rebuild/vsts-publish-front10.png)

    _図 68: 環境の名前指定。_

16. 次に、パイプラインから **[Deploy Azure App Service to Slot]\(Azure App Service をスロットにデプロイ\)** をクリックします。 これは直前のステップからここに配置されています。

    ![パイプラインからスロットを削除する画面のスクリーンショット。](./media/contoso-migration-rebuild/vsts-publish-front11.png)

    _図 69: パイプラインからのスロットの削除。_

17. パイプラインを保存します。 パイプラインで、 **[配置後の条件]** を選択します。

    ![[配置後の条件] ボタンのスクリーンショット。](./media/contoso-migration-rebuild/vsts-publish-front12.png)

    _図 70: 配置後の条件。_

18. **[配置後の承認]** を有効にし、承認者として開発リーダーを追加します。

    ![有効になった [配置後の承認] の承認者一覧のスクリーンショット。](./media/contoso-migration-rebuild/vsts-publish-front13.png)

    _図 71: 承認者の追加。_

19. ビルド パイプラインで、管理者がビルドを手動で開始します。 これによって、サイトをステージング スロットにデプロイする新しいリリース パイプラインがトリガーされます。 Contoso については、このスロットの URL は `https://smarthotelcontoso-staging.azurewebsites.net/` です。

20. ビルドが完了すると、リリースがスロットにデプロイされ、Azure DevOps から承認のために開発リーダーにメールが送られます。

21. 開発リーダーは **[承認の表示]** を選択し、Azure DevOps ポータルで要求を承認または拒否できます。

    ![配置後の承認の [承認または拒否] リンクのスクリーンショット。](./media/contoso-migration-rebuild/vsts-publish-front14.png)

    _図 72: 保留中のリリース承認要求。_

22. 開発リーダーがコメントを入力して承認します。 **ステージング** と **prod** スロットの交換が開始され、ビルドが運用環境に移行します。

    ![配置後の承認とコメントのスクリーンショット。](./media/contoso-migration-rebuild/vsts-publish-front15.png)

    _図 73: 運用環境へのビルドの移行。_

23. パイプラインがスワップを完了します。

    ![ビルドのデプロイの状態を示すスクリーンショット。](./media/contoso-migration-rebuild/vsts-publish-front16.png)

    _図 74: スワップの完了。_

24. チームは **prod** スロットをチェックして、`https://smarthotelcontoso.azurewebsites.net/` で Web アプリが運用環境にあることを確認します。

### <a name="deploy-the-petchecker-function-app"></a>PetChecker 関数アプリをデプロイする

Contoso の管理者は次のようにしてアプリケーションをデプロイします。

1. Azure DevOps プロジェクトに接続することで、リポジトリを開発用マシンにローカルに複製します。
2. Visual Studio でフォルダーを開いて、リポジトリ内のすべてのファイルを表示します。
3. *src/PetCheckerFunction/local.settings.json* ファイルを開き、ストレージ、Azure Cosmos DB データベース、および Computer Vision API 用のアプリ設定を追加します。

    ![.json ファイルに含まれるアプリ設定を Visual Studio で表示した画面のスクリーンショット。](./media/contoso-migration-rebuild/function5.png)

    _図 75: 関数のデプロイ。_

4. コードをコミットし、Azure DevOps に同期して戻し、変更をプッシュします。
5. 新しいビルド パイプラインを追加し、ソースに **[Azure DevOps Git]** を選択します。
6. **[ASP.NET Core (.NET Framework)]** テンプレートを選択します。
7. テンプレートの既定値をそのまま使用します。
8. **[トリガー]** で、 **[継続的インテグレーションを有効にする]** を選択し、 **[保存してキューに登録]** を選択してビルドを開始します。
9. ビルドが完了したら、 **[スロットを使用した Azure App Service の配置]** を追加してリリース パイプラインをビルドします。

10. 環境に **Prod** という名前を付けて、サブスクリプションを選択します。 **[アプリの種類]** を **[関数アプリ]** に設定し、アプリサービス名を `smarthotelpetchecker` とします。

    ![アプリの種類とアプリ サービス名のスクリーンショット。](./media/contoso-migration-rebuild/petchecker2.png)

    _図 76: 関数アプリ。_

11. 成果物 (**ビルド**) を追加します。

    ![成果物の追加画面のスクリーンショット (ソースの種類として [ビルド] を選択したところ)。](./media/contoso-migration-rebuild/petchecker3.png)

    _図 77: 成果物の追加。_

12. **[継続的配置トリガー]** を有効にし、 **[保存]** を選択します。

13. **[Queue new build]\(新しいビルドをキューに登録\)** を選択し、CI/CD パイプライン全体を実行します。

14. 関数はデプロイされた後、Azure portal に **[実行中]** 状態で表示されます。

    !["実行中" 状態で表示された関数アプリのスクリーンショット。](./media/contoso-migration-rebuild/function6.png)

    _図 78: 関数の状態の更新。_

15. `http://smarthotel360public.azurewebsites.net/pets` でこのペット チェッカー アプリケーションを閲覧し、正常に動作していることを確認します。

16. アバターをクリックして、写真をアップロードします。

    ![アバターに画像を割り当てるペインのスクリーンショット。](./media/contoso-migration-rebuild/function7.png)

    _図 79: アバターへの写真の割り当て。_

17. 最初に確認したい写真は、小さい犬の写真です。

    ![犬の写真のスクリーンショット。](./media/contoso-migration-rebuild/function8.png)

    _図 80: 写真の確認。_

18. アプリケーションによって受領のメッセージが返されます。

    ![受領のメッセージのスクリーンショット。](./media/contoso-migration-rebuild/function9.png)

    _図 81: 受領のメッセージ。_

## <a name="review-the-deployment"></a>デプロイを再調査する

リソースを Azure に移行したら、新しいインフラストラクチャを完全に操作可能にして、セキュリティ保護する必要があります。

### <a name="security"></a>Security

- Contoso は新しいデータベースが安全であることを確認する必要があります。 詳細については、「[Azure SQL Database と SQL Managed Instance のセキュリティ機能の概要](/azure/sql-database/sql-database-security-overview)」を参照してください。
- アプリケーションは、SSL と証明書を使用するように更新する必要があります。 コンテナー インスタンスは 443 で応答するように再デプロイする必要があります。
- Contoso は、Service Fabric アプリケーションのシークレットの保護に Azure Key Vault を役立てることを検討する必要があります。 詳細については、「[Service Fabric アプリケーションで暗号化されたシークレットを管理する](/azure/service-fabric/service-fabric-application-secret-management)」を参照してください。

### <a name="backups-and-disaster-recovery"></a>バックアップとディザスター リカバリー

- Contoso は、[Azure SQL Database のバックアップ要件](/azure/sql-database/sql-database-automated-backups)を確認する必要があります。
- Contoso は、[データベースのリージョン内フェールオーバーを提供するように SQL フェールオーバー グループを実装](/azure/sql-database/sql-database-auto-failover-group)することを検討する必要があります。
- Contoso では、[Azure Container Registry Premium SKU の geo レプリケーション](/azure/container-registry/container-registry-geo-replication)を使用できます。
- Azure Cosmos DB は自動的にバックアップされます。 詳細については、「[Azure Cosmos DB でのオンライン バックアップとオンデマンドのデータ復元](/azure/cosmos-db/online-backup-and-restore)」を参照してください。

### <a name="licensing-and-cost-optimization"></a>ライセンスとコストの最適化

- すべてのリソースをデプロイした後、Contoso は[インフラストラクチャ計画](./contoso-migration-infrastructure.md#set-up-tagging)に基づいて Azure タグを割り当てる必要があります。
- すべてのライセンスは、Contoso が使用している PaaS サービスのコストに組み込まれています。 これはマイクロソフト エンタープライズ契約から差し引かれます。
- Contoso は、[Azure Cost Management + Billing](/azure/cost-management-billing/cost-management-billing-overview) を有効にして、Azure リソースの監視と管理を支援します。

## <a name="conclusion"></a>まとめ

この記事で、Contoso は SmartHotel360 アプリケーションを Azure にリビルドしました。 オンプレミス アプリケーションのフロントエンド VM は、Azure App Service Web アプリにリビルドされます。 アプリケーションのバックエンドは、AKS によって管理されるコンテナーにデプロイされたマイクロサービスを使用してビルドされます。 Contoso は機能をペットの写真アプリケーションで強化しました。

## <a name="suggested-skills"></a>推奨されるスキル

Microsoft Learn は学習に対する新しいアプローチです。 クラウド導入に伴う新たな責任に対する準備は容易にできるものではありません。 Microsoft Learn では、目標を早く達成するのに役立つ、実践的な学習に対するより価値あるアプローチを提供します。 Microsoft Learn では、ポイントを獲得し、レベルを積み重ねて、さらなる達成を目指すことができます。

ここでは、Azure の Contoso SmartHotel360 アプリケーションに合わせてカスタマイズされた Microsoft Learn のラーニング パスの例を 2 つ紹介します。

<!-- docsTest:casing "with the Azure Cognitive Vision Services" "computer vision functionality" -->

- **[Azure App Service を使用して Azure に Web サイトをデプロイする](/learn/paths/deploy-a-website-with-azure-app-service):** Azure に Web アプリを作成すれば、基盤となるサーバー、ストレージ、ネットワーク アセットを操作しなくても簡単に Web サイトを公開して管理することができます。 代わりに、Web サイトの機能に焦点を当て、堅牢な Azure プラットフォームを使用して、サイトへの安全なアクセスを提供することができます。

- **[Azure Cognitive Vision サービスを使用してイメージを処理して分類する](/learn/paths/classify-images-with-vision-services):** Microsoft Cognitive Services では、ご利用のアプリケーションで Computer Vision 機能を有効にできる事前に作成した機能を提供します。 Azure Cognitive Vision Services を使用して、顔を検出し、イメージをタグ付けして分類し、オブジェクトを特定する方法について学習します。
