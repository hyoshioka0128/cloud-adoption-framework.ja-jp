---
title: オンプレミス アプリを Azure に再構築する
description: Contoso が Azure App Service、Azure Kubernetes Service、Azure Cosmos DB、Azure Functions、Azure Cognitive Services サービスを使用して Azure にアプリをリビルドする方法について説明します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 10/11/2018
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: migrate
services: site-recovery
ms.openlocfilehash: e09df6f954a410dedde7ecd77fd7b8bd5d429241
ms.sourcegitcommit: bd9872320b71245d4e9a359823be685e0f4047c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/26/2020
ms.locfileid: "83862230"
---
<!-- docsTest:ignore SmartHotel360 SmartHotel360-Backend Pet.Checker vcenter.contoso.com contoso-datacenter git aks ContosoRG PetCheckerFunction -->

<!-- cSpell:ignore givenscj WEBVM SQLVM contosohost vcenter contosodc smarthotel contososmarthotel smarthotelcontoso smarthotelpetchecker petchecker smarthotelakseus smarthotelacreus smarthotelpets kubectl contosodevops visualstudio azuredeploy cloudapp smarthotelsettingsurl appsettings -->

# <a name="rebuild-an-on-premises-app-on-azure"></a>オンプレミス アプリを Azure 上にリビルドする

この記事では、Contoso という架空の会社が、Azure への移行の一環として、VMware VM 上で実行される 2 階層の Windows .NET アプリをリビルドする方法を説明します。 Contoso は、アプリのフロントエンド VM を、Azure App Service Web アプリに移行します。 アプリのバックエンドは、Azure Kubernetes Service (AKS) によって管理されるコンテナーにデプロイされたマイクロサービスを使用してビルドされます。 サイトは、ペットの写真の機能を提供する Azure Functions と対話します。

この例で使用される SmartHotel360 アプリは、オープン ソースとして提供されています。 独自のテスト目的に沿って使用する場合は、[GitHub](https://github.com/Microsoft/SmartHotel360) からダウンロードできます。

## <a name="business-drivers"></a>ビジネス ドライバー

IT リーダーシップ チームは、ビジネス パートナーと密接に連絡を取り合い、彼らがこの移行で何を達成しようとしているのかを理解しました。

- **ビジネスの成長への対応。** Contoso は成長を続けており、Contoso の Web サイトで、差別化されたエクスペリエンスを顧客に提供したいと考えています。
- **機敏である。** Contoso は、グローバル経済で成功を収めるために、より迅速に市場の変化に対応できる必要があります。
- **スケール。** ビジネスが順調に成長していく中で、Contoso IT チームは、同じペースで拡張可能なシステムを提供する必要があります。
- **コストを削減する。** Contoso はライセンス コストを最小限に抑えたいと考えています。

## <a name="migration-goals"></a>移行の目標

Contoso クラウド チームは、この移行のためのアプリの要件を設定しました。 これらの要件を使用して、最良の移行方法を決定しました。

- このアプリは、Azure でも現在と同じくクリティカルです。 優れたパフォーマンスを発揮し、容易にスケールできる必要があります。
- このアプリでは、IaaS コンポーネントを使用しません。 PaaS またはサーバーレス サービスを使用するようにすべてをビルドする必要があります。
- アプリのビルドはクラウド サービスで実行する必要があり、コンテナーはクラウド内のプライベートなエンタープライズ規模のコンテナー レジストリに存在する必要があります。
- アプリで下された決定がホテルで受け入れられる必要があるため、ペットの写真に使用される API サービスは正確かつ実世界で信頼できるものである必要があります。 アクセス権を付与されたペットは、ホテルでの滞在を許可されます。
- DevOps パイプラインの要件を満たすために、Contoso ではソース コード管理に Azure Repos の Git リポジトリを使用します。 コードのビルドと Azure App Service、Azure Functions、および AKS へのデプロイには、自動ビルドとリリースが使用されます。
- バックエンドのマイクロサービスと、フロントエンドの Web サイトに対して別個の CI/CD パイプラインが必要です。
- バックエンド サービスのリリース サイクルはフロントエンド Web アプリのリリース サイクルとは異なります。 この要件を満たすため、2 つの異なるパイプラインが展開されます。
- Contoso では、すべてのフロント エンド Web サイトのデプロイには上長の承認が必要であり、CI/CD パイプラインではこれを提供する必要があります。

## <a name="solution-design"></a>ソリューション設計

Contoso は目標と要件を決定した後、デプロイ ソリューションを設計およびレビューし、移行に使用する Azure サービスを含め、移行プロセスを決めます。

### <a name="current-app"></a>現在のアプリ

- SmartHotel360 オンプレミス アプリは 2 つの VM (WEBVM と SQLVM) に階層化されています。
- VM は、VMware ESXi ホスト **contosohost1.contoso.com** (バージョン 6.5) 上に配置されます。
- VMware 環境は、VM 上で実行中の vCenter Server 6.5 (**vcenter.contoso.com**) によって管理されています。
- Contoso には、オンプレミスのドメイン コントローラー (**contosodc1**) を含むオンプレミスのデータセンター (**contoso-datacenter**) があります。
- Contoso データセンター内のオンプレミス VM は、移行が行われた後に使用停止にされます。

### <a name="proposed-architecture"></a>提案されたアーキテクチャ

- アプリのフロントエンドは、プライマリ Azure リージョンに Azure App Service の Web アプリとしてデプロイされます。
- Azure 関数がペットの写真をアップロードし、サイトはこの機能と対話します。
- ペットの写真の関数には、Azure Cosmos DB と共に Azure Cognitive Services の Computer Vision API が使用されています。
- サイトのバックエンドは、マイクロ サービスを使用してビルドされます。 これらは、AKS で管理されるコンテナーにデプロイされます。
- コンテナーは Azure DevOps を使用してビルドされ、Azure Container Registry にプッシュされます。
- Contoso は当面、Visual Studio を使用して Web アプリと関数コードを手動でデプロイする予定です。
- マイクロサービスは、Kubernetes コマンドライン ツールを呼び出す PowerShell script スクリプトを使用してデプロイされます。

    ![シナリオのアーキテクチャ](./media/contoso-migration-rebuild/architecture.png)

### <a name="solution-review"></a>ソリューションのレビュー

Contoso は、長所と短所の一覧をまとめて、提案されたデザインを評価します。

<!-- markdownlint-disable MD033 -->

**考慮事項** | **詳細**
--- | ---
**長所** | Contoso はエンドツーエンドのデプロイに PaaS とサーバーレス ソリューションを使用することで、管理に要する時間を大幅に削減できます。 <br><br> マイクロサービス ベースのアーキテクチャへの移行により、Contoso は時間の経過と共にソリューションを容易に拡張できるようになります。 <br><br> 新しい機能は、既存のソリューションのコード ベースを妨げずにオンライン状態にすることができます。 <br><br> Web アプリは複数のインスタンスで構成されるため、単一障害点が発生しません。 <br><br> アプリがさまざまなトラフィック量を処理できるように、自動スケールが有効化されます。 <br><br> PaaS サービスへの移行に伴い、Contoso は Windows Server 2008 R2 オペレーティング システム上で実行されている古いソリューションを廃止できます。 <br><br> Azure Cosmos DB にはフォールト トレランスが組み込まれており、Contoso による構成は必要ありません。 これは、データ層が単一のフェールオーバー ポイントでなくなることを意味します。
**短所** | コンテナーは他の移行オプションより複雑です。 Contoso にとって、学習曲線が問題になる可能性があります。 この曲線にもかかわらず、Contoso は価値を提供する新しいレベルの複雑さを導入します。 <br><br> Azure、コンテナー、アプリのマイクロサービスを理解し、サポートする Contoso の運用チームを立ち上げる必要があります。 <br><br> Contoso は、ソリューション全体の DevOps を完全に実装していません。 Contoso は、AKS、Azure Functions、および Azure App Service へのサービスのデプロイで、そのことを考慮する必要があります。

<!-- markdownlint-enable MD033 -->

### <a name="migration-process"></a>移行プロセス

1. Contoso では、Azure Container Registry、AKS、および Azure Cosmos DB をプロビジョニングします。
2. デプロイのインフラストラクチャ (Azure App Service Web アプリ、ストレージ アカウント、関数、および API) をプロビジョニングします。
3. インフラストラクチャの準備が整ったら、Azure DevOps を使用してマイクロサービスのコンテナー イメージをビルドし、コンテナー レジストリにプッシュします。
4. Contoso は、PowerShell スクリプトを使用してこれらのマイクロサービスを AKS にデプロイします。
5. 最後に、Azure 関数と Web アプリをデプロイします。

    ![移行プロセス](./media/contoso-migration-rebuild/migration-process.png)

### <a name="azure-services"></a>Azure サービス

**サービス** | **説明** | **コスト**
--- | --- | ---
[AKS](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | Kubernetes の管理、デプロイ、操作を簡略化します。 完全に管理された Kubernetes コンテナー オーケストレーション サービスを提供します。 | AKS は無料サービスです。 仮想マシンと、関連するストレージとネットワーク リソースの使用した分に対してのみ料金が発生します。 [詳細については、こちらを参照してください](https://azure.microsoft.com/pricing/details/kubernetes-service)。
[Azure Functions](https://azure.microsoft.com/services/functions) | イベント ドリブン型のサーバーレス コンピューティング エクスペリエンスにより、開発を高速化できます。 オンデマンドでスケールできます。 | 使用したリソースに対してのみ料金が発生します。 プランでは、1 秒あたりのリソースの使用量と実行回数に基づいて課金されます。 [詳細については、こちらを参照してください](https://azure.microsoft.com/pricing/details/functions)。
[Azure Container Registry](https://azure.microsoft.com/services/container-registry) | あらゆる種類のコンテナー デプロイのイメージを保存します。 | コストは機能、ストレージ、使用期間に基づいて発生します。 [詳細については、こちらを参照してください](https://azure.microsoft.com/pricing/details/container-registry)。
[Azure App Service](https://azure.microsoft.com/services/app-service/containers) | あらゆるプラットフォームで稼働するエンタープライズグレードの Web アプリ、モバイル アプリ、API アプリをすばやくビルド、デプロイ、およびスケーリングできます。 | App Service プランは、1 秒単位で課金されます。 [詳細については、こちらを参照してください](https://azure.microsoft.com/pricing/details/app-service/windows)。

## <a name="prerequisites"></a>前提条件

このシナリオで Contoso に必要なことを以下に示します。

<!-- markdownlint-disable MD033 -->

| **必要条件** | **詳細** |
| --- | --- |
| Azure サブスクリプション | <li> Contoso は前の記事でサブスクリプションを作成しました。 Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/pricing/free-trial)を作成してください。 <li> 無料アカウントを作成する場合、サブスクリプションの管理者としてすべてのアクションを実行できます。 <li> 既存のサブスクリプションを使用しており、管理者でない場合は、管理者に依頼して所有者アクセス許可または共同作成者アクセス許可を割り当ててもらう必要があります。 |
| Azure インフラストラクチャ | <li> [Contoso で Azure インフラストラクチャを設定する方法](./contoso-migration-infrastructure.md)を確認してください。 |
| 開発者の前提条件 | Contoso は、開発者用ワークステーションに次のツールをインストールする必要があります。 <li>  [Visual Studio 2017 Community エディション: バージョン 15.5](https://visualstudio.microsoft.com) <li> .NET ワークロードが有効。 <li> [Git](https://git-scm.com) <li> [Azure PowerShell](https://azure.microsoft.com/downloads) <li> [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) <li> Windows コンテナーを使用するように設定された [Docker CE (Windows 10) または Docker EE (Windows Server)](https://docs.docker.com/docker-for-windows/install)。 |

<!-- markdownlint-enable MD033 -->

## <a name="scenario-steps"></a>シナリオのステップ

Contoso が移行を実行する方法を次に示します。

> [!div class="checklist"]
>
> - **ステップ 1:AKS と Azure Container Registry をプロビジョニングする。** Contoso は PowerShell を使用して、管理対象の AKS クラスターとコンテナー レジストリをプロビジョニングします。
> - **手順 2:Docker コンテナーをビルドする。** Azure DevOps を使用して Docker コンテナーの継続的インテグレーション (CI) を設定し、コンテナー レジストリにプッシュします。
> - **ステップ 3:バックエンドのマイクロサービスをデプロイする。** バックエンドのマイクロサービスによって使用される、残りのインフラストラクチャをデプロイします。
> - **手順 4:フロントエンドのインフラストラクチャをデプロイする。** フロントエンドのインフラストラクチャ (ペットの写真用の Blob Storage、Azure Cosmos DB、Computer Vision API を含む) をデプロイします。
> - **手順 5:バックエンドを移行する。** マイクロサービスをデプロイして AKS 上で実行し、バックエンドを移行します。
> - **手順 6:フロントエンドを公開する。** SmartHotel360 アプリを、App Service と、ペット サービスによって呼び出される関数アプリに公開します。

## <a name="step-1-provision-back-end-resources"></a>手順 1:バックエンド リソースをプロビジョニングする

Contoso の管理者は、AKS と Azure Container Registry を使用して、マネージド Kubernetes クラスターを作成するデプロイ スクリプトを実行します。

- このセクションの手順では、**SmartHotel360-Backend** リポジトリを使用します。
- **SmartHotel360-Backend** GitHub リポジトリには、デプロイのこの部分のためのソフトウェアがすべて含まれています。

### <a name="ensure-prerequisites"></a>前提条件を確認する

1. Contoso の管理者は、開始する前に、デプロイに使用する開発用マシンにすべての前提条件ソフトウェアがインストールされていることを確認します。
2. Git を使用して、開発用マシンにリポジトリをローカルにクローンします。

    `git clone https://github.com/Microsoft/SmartHotel360-Backend.git`

### <a name="provision-aks-and-azure-container-registry"></a>AKS と Azure Container Registry をプロビジョニングする

Contoso の管理者は次のようにプロビジョニングします。

1. Visual Studio Code を使用してフォルダーを開き、 **/deploy/k8s** ディレクトリに移動します。ここに、スクリプト **gen-aks-env.ps1** が含まれています。

2. AKS と Azure Container Registry を使用して、マネージド Kubernetes クラスターを作成するスクリプトを実行します。

    ![AKS](./media/contoso-migration-rebuild/aks1.png)

3. ファイルを開いた状態で $location パラメーターを **eastus2** に更新し、ファイルを保存します。

    ![AKS](./media/contoso-migration-rebuild/aks2.png)

4. **[表示]**  >  **[統合ターミナル]** を選択して、Visual Studio Code で統合ターミナルを開きます。

    ![AKS](./media/contoso-migration-rebuild/aks3.png)

5. PowerShell 統合ターミナルで、Connect-AzureRmAccount コマンドを使用して Azure にサインインします。 PowerShell の使用を開始する方法の[詳細を確認](https://docs.microsoft.com/powershell/azure/get-started-azureps)してください。

    ![AKS](./media/contoso-migration-rebuild/aks4.png)

6. `az login` コマンドを実行し、Web ブラウザーを使用した認証の手順に従って、Azure CLI の認証を行います。 Azure CLI でのログインの[詳細については、こちら](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)を参照してください。

    ![AKS](./media/contoso-migration-rebuild/aks5.png)

7. 次のコマンドを、リソース グループ名 **ContosoRG**、AKS クラスター名 **smarthotel-aks-eus2**、および新しいレジストリ名を渡して実行します。

    `.\gen-aks-env.ps1  -resourceGroupName ContosoRg -orchestratorName smarthotelakseus2 -registryName smarthotelacreus2`

    ![AKS](./media/contoso-migration-rebuild/aks6.png)

8. Azure によって、AKS クラスターのリソースを含んだ別のリソース グループが作成されます。

    ![AKS](./media/contoso-migration-rebuild/aks7.png)

9. デプロイが完了したら、`kubectl` コマンドライン ツールをインストールします。 このツールは、Azure Cloud Shell では既にインストールされています。

    `az aks install-cli`

10. `kubectl get nodes` コマンドを実行して、クラスターへの接続を確認します。 ノードは、自動的に作成されたリソース グループ内の VM と同じ名前です。

    ![AKS](./media/contoso-migration-rebuild/aks8.png)

11. 次のコマンドを実行して、Kubernetes Dashboard を起動します。

    `az aks browse --resource-group ContosoRG --name smarthotelakseus2`

12. ブラウザー タブが開き、ダッシュボードが表示されます。 これは Azure CLI を使用したトンネル接続です。

    ![AKS](./media/contoso-migration-rebuild/aks9.png)

## <a name="step-2-configure-the-back-end-pipeline"></a>手順 2:バックエンド パイプラインを構成する

### <a name="create-an-azure-devops-project-and-build"></a>Azure DevOps プロジェクトとビルドを作成する

Contoso は Azure DevOps プロジェクトを作成し、CI ビルドを構成してコンテナーを作成し、それをコンテナー レジストリにプッシュします。 このセクションの手順では、[SmartHotel360-Backend](https://github.com/Microsoft/SmartHotel360-Backend) リポジトリを使用します。

1. visualstudio.com から新しい組織 (**contosodevops360.visualstudio.com**) を作成し、それを Git を使用するように構成します。

2. バージョン コントロールに Git、ワークフローにアジャイルを使用して、新しいプロジェクト (**SmartHotelBackend**) を作成します。

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts1.png)

3. [GitHub リポジトリ](https://github.com/Microsoft/SmartHotel360-Backend)をインポートします。

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts2.png)

4. **[パイプライン]** で、 **[ビルド]** を選択し、リポジトリから、Azure Repos Git をソースとして使用する新しいパイプラインを作成します。

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts3.png)

5. 空のジョブでの開始を選択します。

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts4.png)

6. ビルド パイプラインで **[Hosted Linux Preview]\(ホストされている Linux プレビュー\)** を選択します。

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts5.png)

7. **[Phase 1]\(フェーズ 1\)** で、 **[Docker Compose]** タスクを追加します。 このタスクは、Docker Compose をビルドします。

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts6.png)

8. 手順を繰り返し、**Docker Compose** タスクをもう 1 つ追加します。 これにより、コンテナーがコンテナー レジストリにプッシュされます。

     ![Azure DevOps](./media/contoso-migration-rebuild/vsts7.png)

9. 1 番目のタスク (ビルドの実行) を選択し、ビルドに Azure サブスクリプション、認可、およびコンテナー レジストリを構成します。

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts8.png)

10. リポジトリの **src** フォルダーにある **docket-compose.yaml** ファイルのパスを指定します。 サービス イメージのビルドを選択し、最新のタグを含めます。 アクションが **[Build service images]\(サービス イメージのビルド\)** に変わると、Azure DevOps タスクの名前が **[Build services automatically]\(サービスを自動的にビルド\)** に変わります。

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts9.png)

11. Contoso は次に、2 番目の Docker タスク (プッシュの実行) を構成します。 サブスクリプションと **smarthotelacreus2** コンテナー レジストリを選択します。

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts10.png)

12. 前回と同様に、ファイルには docker-compose.yaml ファイルを入力し、 **[Push service images]\(サービス イメージのプッシュ\)** を選択し、最新のタグを含めます。 アクションが **[Push service images]\(サービス イメージのプッシュ\)** に変わると、Azure DevOps タスクの名前が **[Push services automatically]\(サービスを自動的にプッシュ\)** に変わります。

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts11.png)

13. Azure DevOps タスクが構成されたので、Contoso はビルド パイプラインを保存し、ビルド プロセスを開始します。

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts12.png)

14. ビルド ジョブを選択して、進行状況を確認します。

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts13.png)

15. ビルドが完了すると、コンテナー レジストリに、マイクロサービスによって使用されるコンテナーが設定された新しいリポジトリが表示されます。

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts14.png)

### <a name="deploy-the-back-end-infrastructure"></a>バックエンド インフラストラクチャをデプロイする

AKS クラスターを作成し、Docker イメージをビルドしたので、次に Contoso の管理者は、バックエンドのマイクロサービスによって使用される残りのインフラストラクチャをデプロイします。

- このセクションの手順では、[SmartHotel360-Backend](https://github.com/Microsoft/SmartHotel360-Backend) リポジトリを使用します。
- **/deploy/k8s/arm** フォルダーに、すべての項目を作成する 1 つのスクリプトが含まれています。

デプロイは次のように行います。

1. 開発者コマンド プロンプトを開き、Azure サブスクリプションのコマンド `az login` を使用します。

2. deploy.cmd ファイルを使用し、次のコマンドを入力して **ContosoRG** リソース グループと **EUS2** リージョンに Azure リソースをデプロイします。

    `.\deploy.cmd azuredeploy ContosoRG -c eastus2`

    ![バック エンドをデプロイする](./media/contoso-migration-rebuild/backend1.png)

3. Azure ポータルで、後で使用するために、各データベースの接続文字列をキャプチャします。

    ![バック エンドをデプロイする](./media/contoso-migration-rebuild/backend2.png)

### <a name="create-the-back-end-release-pipeline"></a>バックエンド リリース パイプラインを作成する

ここで、Contoso の管理者は以下のことを行います。

- サービスへの受信トラフィックを許可する NGINX イングレス コントローラーをデプロイします。
- AKS クラスターにマイクロサービスを送ります。
- 最初のステップとして、マイクロサービスへの接続文字列を Azure DevOps を使用して更新します。 次に、新しい Azure DevOps リリース パイプラインを構成して、マイクロサービスをデプロイします。
- このセクションの手順では、[SmartHotel360-Backend](https://github.com/Microsoft/SmartHotel360-Backend) リポジトリを使用します。
- 一部の構成設定 (Active Directory B2C など) については、この記事では説明しません。 これらの設定の詳細については、上記のリポジトリを参照してください。

次のパイプラインを作成します。

1. Visual Studio を使用して、前に説明したデータベース接続情報で **/deploy/k8s/config_local.yml** ファイルを更新します。

    ![データベース接続](./media/contoso-migration-rebuild/back-pipe1.png)

2. Azure DevOps を開き、SmartHotel360 プロジェクトの **[リリース]** で **[+新しいパイプライン]** を選択します。

    ![新しいパイプライン](./media/contoso-migration-rebuild/back-pipe2.png)

3. **[空のジョブ]** を選択し、テンプレートを使用せずにパイプラインを開始します。
4. ステージとパイプラインの名前を付けます。

      ![ステージ名](./media/contoso-migration-rebuild/back-pipe4.png)

      ![パイプライン名](./media/contoso-migration-rebuild/back-pipe5.png)

5. 成果物を追加します。

     ![成果物を追加する](./media/contoso-migration-rebuild/back-pipe6.png)

6. ソース タイプとして **[Git]** を選択し、SmartHotel360 アプリのプロジェクト、ソース、およびマスター ブランチを指定します。

    ![成果物の設定](./media/contoso-migration-rebuild/back-pipe7.png)

7. タスクのリンクを選択します。

    ![タスクのリンク](./media/contoso-migration-rebuild/back-pipe8.png)

8. PowerShell スクリプトを Azure 環境で実行できるように、新しい Azure PowerShell タスクを追加します。

    ![Azure での PowerShell](./media/contoso-migration-rebuild/back-pipe9.png)

9. タスクの Azure サブスクリプションを選択し、Git レポジトリから **deploy.ps1** スクリプトを選択します。

    ![スクリプトの実行](./media/contoso-migration-rebuild/back-pipe10.png)

10. スクリプトに引数を追加します。 このスクリプトでは、すべてのクラスター コンテンツ (**イングレス**および**イングレス コントローラー**を除く) が削除され、マイクロサービスがデプロイされます。

    ![[スクリプトの引数]](./media/contoso-migration-rebuild/back-pipe11.png)

11. 指定の Azure PowerShell バージョンを最新に設定し、パイプラインを保存します。

12. **リリース** ページに戻り、新しいリリースを手動で作成します。

    ![新しいリリース](./media/contoso-migration-rebuild/back-pipe12.png)

13. リリースを作成した後、それを選択し、 **[アクション]** の **[デプロイ]** を選択します。

      ![リリースのデプロイ](./media/contoso-migration-rebuild/back-pipe13.png)

14. デプロイが完了したら、Azure Cloud Shell: `kubectl get services` を使用して、以下のコマンドを実行することでサービスの状態を確認します。

## <a name="step-3-provision-front-end-services"></a>手順 3:フロントエンド サービスをプロビジョニングする

Contoso の管理者は、フロントエンド アプリによって使用されるインフラストラクチャをデプロイする必要があります。 次のものを作成します。

- ペットの画像を格納する BLOB ストレージ コンテナー
- ペット情報を含むドキュメントを格納する Azure Cosmos DB データベース
- Web サイト用の Computer Vision API。

このセクションの手順では、[SmartHotel360-Website](https://github.com/microsoft/smartHotel360-website) リポジトリを使用します。

### <a name="create-blob-storage-containers"></a>BLOB ストレージ コンテナーの作成

1. Azure portal で、作成済みのストレージ アカウントを開き、 **[BLOB]** をクリックします。
2. パブリック アクセス レベル をコンテナーに設定した **Pets** という新しいコンテナーを作成します。 ユーザーは、ペットの写真をこのコンテナーにアップロードします。

    ![Storage Blob](./media/contoso-migration-rebuild/blob1.png)

3. **settings** という名前の 2 つめのコンテナーを作成します。 このコンテナーに、フロントエンド アプリのすべての設定が含まれるファイルが配置されます。

    ![Storage Blob](./media/contoso-migration-rebuild/blob2.png)

4. 後で参照できるように、ストレージ アカウントのアクセスの詳細をテキスト ファイルにキャプチャします。

    ![Storage Blob](./media/contoso-migration-rebuild/blob2.png)

### <a name="provision-an-azure-cosmos-db-database"></a>Azure Cosmos DB のデータベースをプロビジョニングする

Contoso の管理者は、ペットの情報に使用する Azure Cosmos DB データベースをプロビジョニングします。

1. Azure Marketplace で、**Azure Cosmos DB** を作成します。

    ![Azure Cosmos DB](./media/contoso-migration-rebuild/cosmos1.png)

2. 名前 (**contososmarthotel**) を指定し、SQL API を選択し、米国東部 2 メイン リージョンの **ContosoRG** 運用リソース グループに配置します。

    ![Azure Cosmos DB](./media/contoso-migration-rebuild/cosmos2.png)

3. 既定の容量およびスループットを指定した新しいコレクションをデータベースに追加します。

    ![Azure Cosmos DB](./media/contoso-migration-rebuild/cosmos3.png)

4. 後で参照できるように、このデータベースの接続情報をメモします。

    ![Azure Cosmos DB](./media/contoso-migration-rebuild/cosmos4.png)

### <a name="provision-computer-vision"></a>Computer Vision をプロビジョニングする

Contoso の管理者は、Computer Vision API をプロビジョニングします。 この API は、ユーザーによってアップロードされた写真を評価するために関数によって呼び出されます。

1. Azure Marketplace で、**Computer Vision** インスタンスを作成します。

     ![Computer Vision](./media/contoso-migration-rebuild/vision1.png)

2. **ContosoRG** 運用リソース グループ、米国東部 2 メイン リージョンで、API (**smarthotelpets**) をプロビジョニングします。

    ![Computer Vision](./media/contoso-migration-rebuild/vision2.png)

3. 後で参照できるように、この API の接続文字列をテキスト ファイルに保存します。

     ![Computer Vision](./media/contoso-migration-rebuild/vision3.png)

### <a name="provision-the-azure-web-app"></a>Azure Web アプリをプロビジョニングする

Contoso の管理者は、Azure portal を使用して Web アプリをプロビジョニングします。

1. ポータルで **[Web アプリ]** を選択します。

    ![Web アプリ](./media/contoso-migration-rebuild/web-app1.png)

2. アプリ名 (**smarthotelcontoso**) を指定し、Windows 上で実行し、運用リソース グループ **ContosoRG** に配置します。 アプリのモニタリング用に新しい Application Insights リソースを作成します。

    ![Web アプリの名前](./media/contoso-migration-rebuild/web-app2.png)

3. 完了したら、アプリのアドレスを参照して、アプリが正常に作成されたことを確認します。

4. ここで、Azure Portal でコードのステージング スロットを作成します。 パイプラインはこのスロットにデプロイされます。 これにより、管理者がリリースを実行するまで、コードは運用環境に移行されません。

    ![Web アプリのステージング スロット](./media/contoso-migration-rebuild/web-app3.png)

### <a name="provision-the-azure-function-app"></a>Azure Function アプリのプロビジョニング

Azure portal で、Contoso の管理者は Function App をプロビジョニングします。

1. **[Function App]** を選択します。

    ![Function App の作成](./media/contoso-migration-rebuild/function-app1.png)

2. アプリ名 (**smarthotelpetchecker**) を指定します。 運用リソース グループ **ContosoRG** にアプリを配置します。 ホスティングの場所を**従量課金プラン**に設定し、アプリを米国東部 2 リージョンに配置します。 監視用の Application Insights インスタンスと共に新しいストレージ アカウントが作成されます。

    ![Function App の設定](./media/contoso-migration-rebuild/function-app2.png)

3. アプリがデプロイされたら、アプリのアドレスを参照して、アプリが正常に作成されたことを確認します。

## <a name="step-4-set-up-the-front-end-pipeline"></a>手順 4:フロントエンド パイプラインを設定する

Contoso 管理者は、フロントエンド サイトに 2 つの異なるプロジェクトを作成します。

1. Azure DevOps で、プロジェクト **SmartHotelFrontend** を作成します。

    ![フロントエンド プロジェクト](./media/contoso-migration-rebuild/function-app1.png)

2. [SmartHotel360 フロントエンド](https://github.com/Microsoft/SmartHotel360-Website) Git リポジトリを新しいプロジェクトにインポートします。

3. 関数アプリ用の別の Azure DevOps プロジェクト (**SmartHotelPetChecke**r) を作成し、[PetChecker](https://github.com/sonahander/SmartHotel360-PetCheckerFunction) Git リポジトリをこのプロジェクトにインポートします。

### <a name="configure-the-web-app"></a>Web アプリを構成する

次に、Contoso の管理者は Contoso リソースを使用するように Web アプリを構成します。

1. Azure DevOps プロジェクトに接続し、リポジトリを開発用マシンにローカルにクローンします。
2. Visual Studio でフォルダーを開いて、リポジトリ内のすべてのファイルを表示します。

    ![リポジトリ ファイル](./media/contoso-migration-rebuild/configure-webapp1.png)

3. 必要に応じて構成の変更を更新します。

    - Web アプリは、起動時に **SettingsUrl** アプリ設定を探します。
    - この変数には、構成ファイルをポイントする URL が含まれている必要があります。
    - 既定では、使用される設定はパブリック エンドポイントです。

4. /config-sample.json/sample.json ファイルを更新します。

    - これは、パブリック エンドポイントを使用する場合の Web 用の構成ファイルです。
    - **urls** セクションと **pets_config** セクションを、AKS API エンドポイント、ストレージ アカウント、Azure Cosmos DB データベースの値を使用して編集します。
    - URL は、Contoso が作成する新しい Web アプリの DNS 名と一致している必要があります。
    - Contoso の場合、これは **smarthotelcontoso.eastus2.cloudapp.azure.com** です。

    ![Json 設定](./media/contoso-migration-rebuild/configure-webapp2.png)

5. ファイルが更新されたら、名前を **smarthotelsettingsurl** に変更し、前に作成した BLOB ストレージにアップロードします。

    ![名前の変更とアップロード](./media/contoso-migration-rebuild/configure-webapp3.png)

6. ファイルを選択肢て URL を取得します。 この URL は、アプリが構成ファイルプル ダウンするときに使用されます。

    ![アプリケーションの URL](./media/contoso-migration-rebuild/configure-webapp4.png)

7. **appsettings.Production.json** ファイルで、**SettingsUrl** を、新しいファイルの URL に更新します。

    ![更新 URL](./media/contoso-migration-rebuild/configure-webapp5.png)

### <a name="deploy-the-website-to-azure-app-service"></a>Azure App Service に Web サイトをデプロイする

これで Contoso の管理者は Web サイトを公開できます。

1. Azure DevOps を開き、 **SmartHotelFrontend** プロジェクトの **[ビルドとリリース]** で **[+新しいパイプライン]** をクリックします。
2. **[Azure DevOps Git]** をソースとして選択します。
3. **[ASP.NET Core]** テンプレートを選択します。
4. パイプラインを確認し、 **[Web プロジェクトの発行]** と **[発行されたプロジェクトの zip 圧縮]** が選択されていることを確認します。

    ![パイプラインの設定](./media/contoso-migration-rebuild/vsts-publish-front2.png)

5. **[トリガー]** で継続的インテグレーションを有効にして、マスター ブランチを追加します。 これにより、マスター ブランチにコミットされた新しいコードをソリューションが持つたびに、ビルド パイプラインが起動します。

    ![継続的インテグレーション](./media/contoso-migration-rebuild/vsts-publish-front3.png)

6. **[保存してキューに登録]** を選択してビルドを開始します。
7. ビルドが完了したら、**Azure App Service のデプロイ**を使用してリリース パイプラインを構成します。
8. ステージ名 **Staging** を指定します。

    ![環境名](./media/contoso-migration-rebuild/vsts-publish-front4.png)

9. 成果物を追加し、構成したビルドを選択します。

     ![成果物を追加する](./media/contoso-migration-rebuild/vsts-publish-front5.png)

10. 成果物の上にある稲妻アイコンを選択して、継続的デプロイを有効にします。

    ![継続的なデプロイ](./media/contoso-migration-rebuild/vsts-publish-front6.png)
11. **[環境]** の **[ステージング]** の下で **[1 job, 1 task]\(1 ジョブ、1 タスク\)** を選択します。
12. サブスクリプションおよびアプリ名を選択した後、 **[Azure App Service のデプロイ]** タスクを開きます。 **ステージング** デプロイ スロットを使用するようにデプロイが構成されます。 これによって、このスロットでレビューと承認のコードが自動的にビルドされます。

     ![スロット](./media/contoso-migration-rebuild/vsts-publish-front7.png)

13. **[パイプライン]** で、新しいステージを追加します。

    ![新しい環境](./media/contoso-migration-rebuild/vsts-publish-front8.png)

14. **[スロットを使用した Azure App Service の配置]** を選択し、環境名を **Prod** とします。
15. **[1 job, 2 task]\(1 ジョブ、2 タスク\)** を選択し、次にサブスクリプション、アプリ サービス名、および**ステージング** スロットを選択します。

    ![環境名](./media/contoso-migration-rebuild/vsts-publish-front10.png)

16. 次に、パイプラインから **[Deploy Azure App Service to Slot]\(Azure App Service をスロットにデプロイ\)** をクリックします。 これは直前のステップからここに配置されています。

    ![パイプラインから削除する](./media/contoso-migration-rebuild/vsts-publish-front11.png)

17. パイプラインを保存します。 パイプラインで、 **[配置後の条件]** を選択します。

    ![配置後](./media/contoso-migration-rebuild/vsts-publish-front12.png)

18. **[展開後承認]** を有効にし、承認者として開発リーダーを追加します。

    ![展開後承認](./media/contoso-migration-rebuild/vsts-publish-front13.png)

19. ビルド パイプラインで、ビルドを手動でキックオフします。 これによって、サイトをステージング スロットにデプロイする新しいリリース パイプラインがトリガーされます。 Contoso については、このスロットの URL は `https://smarthotelcontoso-staging.azurewebsites.net/` です。

20. ビルドが完了すると、リリースがスロットにデプロイされ、Azure DevOps から承認のために開発リーダーにメールが送られます。

21. 開発リーダーは **[承認の表示]** を選択し、Azure DevOps ポータルで要求を承認または拒否できます。

    ![承認メール](./media/contoso-migration-rebuild/vsts-publish-front14.png)

22. リーダーはコメントと承認を行います。 **ステージング**と **prod** スロットの交換が開始され、ビルドが運用環境に移行します。

    ![承認とスワップ](./media/contoso-migration-rebuild/vsts-publish-front15.png)

23. パイプラインがスワップを完了します。

    ![完全なスワップ](./media/contoso-migration-rebuild/vsts-publish-front16.png)

24. チームは **prod** スロットをチェックして、`https://smarthotelcontoso.azurewebsites.net/` で Web アプリが運用環境にあることを確認します。

### <a name="deploy-the-petchecker-function-app"></a>PetChecker 関数アプリをデプロイする

Contoso の管理者はアプリを次のようにデプロイします。

1. Azure DevOps プロジェクトに接続することで、リポジトリを開発用マシンにローカルにクローンします。
2. Visual Studio でフォルダーを開いて、リポジトリ内のすべてのファイルを表示します。
3. **src/PetCheckerFunction/local.settings.json** ファイルを開き、ストレージ、Azure Cosmos DB データベース、および Computer Vision API 用のアプリ設定を追加します。

    ![関数のデプロイ](./media/contoso-migration-rebuild/function5.png)

4. コードをコミットし、Azure DevOps に同期して戻し、変更をプッシュします。
5. 新しいビルド パイプラインを追加し、次にソースに **[Azure DevOps Git]** を選択します。
6. **[ASP.NET Core (.NET Framework)]** テンプレートを選択します。
7. テンプレートの既定値をそのまま使用します。
8. **[トリガー]** で、 **[継続的インテグレーションを有効にする]** を選択し、次に **[保存してキューに登録]** を選択してビルドを開始します。
9. ビルドが完了したら、 **[スロットを使用した Azure App Service の配置]** を追加してリリース パイプラインをビルドします。
10. 環境に **Prod** という名前を付けて、サブスクリプションを選択します。 **[アプリの種類]** を **[関数アプリ]** に設定し、アプリサービス名を **smarthotelpetchecker** とします。

    ![関数アプリ](./media/contoso-migration-rebuild/petchecker2.png)

11. 成果物 **ビルド** を追加します。

    ![アーティファクト](./media/contoso-migration-rebuild/petchecker3.png)

12. **[継続的配置トリガー]** を有効にし、次に **[保存]** を選択します。
13. **[Queue new build]\(新しいビルドをキューに登録\)** を選択し、CI/CD パイプライン全体を実行します。
14. 関数はデプロイされた後、Azure portal に **[実行中]** 状態で表示されます。

    ![関数のデプロイ](./media/contoso-migration-rebuild/function6.png)

15. Pet Checker アプリが期待どおりに動作していることを確認するために、アプリを参照します (`http://smarthotel360public.azurewebsites.net/pets`)。

16. アバターをクリックして、写真をアップロードします。

    ![関数のデプロイ](./media/contoso-migration-rebuild/function7.png)

17. 最初に確認したい写真は、小さい犬の写真です。

    ![関数のデプロイ](./media/contoso-migration-rebuild/function8.png)

18. アプリが受領のメッセージを返します。

    ![関数のデプロイ](./media/contoso-migration-rebuild/function9.png)

## <a name="review-the-deployment"></a>デプロイを再調査する

リソースを Azure に移行したら、新しいインフラストラクチャを完全に操作可能にして、セキュリティ保護する必要があります。

### <a name="security"></a>Security

- Contoso は新しいデータベースが安全であることを確認する必要があります。 [詳細については、こちらを参照してください](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview)。
- アプリは、SSL と証明書を使用するように更新する必要があります。 コンテナー インスタンスは 443 で応答するように再デプロイする必要があります。
- Contoso は Key Vault を使用して、Service Fabric アプリのシークレットを保護することを検討する必要があります。 [詳細については、こちらを参照してください](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management)。

### <a name="backups-and-disaster-recovery"></a>バックアップとディザスター リカバリー

- Contoso は、[Azure SQL Database のバックアップ要件](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups)を確認する必要があります。
- Contoso は、[データベースのリージョン内フェールオーバーを提供するように SQL フェールオーバー グループを実装](https://docs.microsoft.com/azure/sql-database/sql-database-auto-failover-group)することを検討する必要があります。
- Contoso では、[Azure Container Registry Premium SKU の geo レプリケーション](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication)を使用できます。
- Azure Cosmos DB は自動的にバックアップされます。 Contoso は、このプロセスの詳細情報を[こちらで確認](https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore)できます。

### <a name="licensing-and-cost-optimization"></a>ライセンスとコストの最適化

- すべてのリソースをデプロイした後、Contoso は[インフラストラクチャ計画](./contoso-migration-infrastructure.md#set-up-tagging)に基づいて Azure タグを割り当てる必要があります。
- すべてのライセンスは、Contoso が使用している PaaS サービスのコストに組み込まれています。 これは EA から差し引かれます。
- Contoso では、[Azure Cost Management](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview) を有効にして、Azure リソースの監視と管理を支援します。

## <a name="conclusion"></a>まとめ

この記事で、Contoso は SmartHotel360 アプリを Azure にリビルドしました。 オンプレミス アプリのフロントエンド VM は、Azure App Service Web アプリにリビルドされます。 アプリのバックエンドは、Azure Kubernetes Service (AKS) によって管理されるコンテナーにデプロイされたマイクロサービスを使用してビルドされます。 Contoso はアプリの機能を、ペットの写真アプリで強化しました。

## <a name="suggested-skills"></a>推奨されるスキル

Microsoft Learn は学習に対する新しいアプローチです。 クラウド導入に伴う新たな責任に対する準備は容易にできるものではありません。 Microsoft Learn では、目標を早く達成するのに役立つ、実践的な学習に対するより価値あるアプローチを提供します。 ポイントとレベルを獲得し、さらなる達成を目指しましょう。

ここでは、Azure の Contoso SmartHotel360 アプリに合わせてカスタマイズされた Microsoft Learn のラーニング パスの例をいくつか紹介します。

- **[Azure App Service を使用して Azure に Web サイトをデプロイする](https://docs.microsoft.com/learn/paths/deploy-a-website-with-azure-app-service):** Azure 内の Web アプリを使用すると、基盤となるサーバー、ストレージ、ネットワーク アセットを操作しなくても簡単に Web サイトを公開して管理することができます。 代わりに、Web サイトの機能に焦点を当て、堅牢な Azure プラットフォームを使用して、サイトへの安全なアクセスを提供することができます。

- **[Azure Cognitive Vision サービスを使用してイメージを処理して分類する](https://docs.microsoft.com/learn/paths/classify-images-with-vision-services):** Microsoft Cognitive Services では、ご利用のアプリケーションで Computer Vision 機能を有効にできる事前に作成した機能を提供します。 Cognitive Vision サービスを使用して、顔を検出し、イメージをタグ付けして分類し、オブジェクトを特定する方法について説明します。
