---
title: Azure サーバー管理サービスの前提条件となる計画
titleSuffix: Microsoft Cloud Adoption Framework for Azure
description: Azure サーバー管理サービスの前提条件となるツールおよび計画。
author: BrianBlanchard
ms.author: brblanch
ms.date: 05/10/2019
ms.topic: article
ms.service: cloud-adoption-framework
ms.subservice: operate
ms.openlocfilehash: ac4ece6c5daec788d116e67c79429572722fc618
ms.sourcegitcommit: 35c162d2d09ec1c4a57d3d57a5db1d56ee883806
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/17/2019
ms.locfileid: "72548232"
---
# <a name="phase-1-prerequisite-planning-for-azure-server-management-services"></a>フェーズ 1:Azure サーバー管理サービスの前提条件となる計画

このフェーズでは、Azure サーバー管理スイートのサービスについて理解し、これらの管理ソリューションを実装するために必要なリソースをデプロイする方法を計画します。

## <a name="understand-the-tools-and-services"></a>ツールおよびサービスを理解する

継続中の Azure 操作に含まれる管理領域の詳細な概要と、これらの領域でのユーザーのサポートに役立つ Azure のサービスおよびツールについては、「[Azure サーバー管理ツールおよびサービス](./tools-services.md)」を参照してください。 管理要件を満たすには、これらのサービスのいくつかを併用することが必要になります。 これらのツールはこのガイダンス全体でしばしば参照されます。

以降の各セクションでは、これらのツールとサービスを使用するために必要な計画と準備について説明します。

## <a name="log-analytics-workspace-and-automation-account-planning"></a>Log Analytics ワークスペースと Automation アカウントの計画

Azure 管理サービスをオンボードするために使用する多くのサービスでは、Log Analytics ワークスペースと、リンクされた Azure Automation アカウントが必要です。

[Log Analytics ワークスペース](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)は、Azure Monitor ログ データの格納用の一意の環境です。 各ワークスペースには、独自のデータ リポジトリと構成があります。 データ ソースとソリューションは、特定のワークスペースにデータを格納するように構成されます。 Azure 監視ソリューションでは、すべてのサーバーをワークスペースに接続することで、それらのログ データを格納してアクセスできるようにする必要があります。

管理サービスの一部では [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) アカウントが必要です。 このアカウントと Azure Automation の機能を使用して、Azure サービスと他のパブリック システムを統合して、サーバー管理プロセスをデプロイ、構成、および管理することができます。

以下の Azure サーバー管理サービスでは、リンクされた Log Analytics ワークスペースと Automation アカウントが機能する必要があります。

- [Azure Update Management](https://docs.microsoft.com/azure/automation/automation-update-management)
- [変更履歴とインベントリ](https://docs.microsoft.com/azure/automation/change-tracking)
- [Hybrid Runbook Worker](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker)
- [Desired State Configuration](https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-overview)

このガイダンスの 2 番目のフェーズは、サービスおよび自動化スクリプトのデプロイに焦点を合わせて説明します。 Log Analytics ワークスペースと Automation アカウントを作成する方法が紹介されています。 このガイダンスでは、Azure Policy を使用して、新しい VM が必ず正しいワークスペースに接続されるようにする方法も紹介しています。

このガイダンスで扱われている例では、サーバーがクラウドにまだデプロイされていないデプロイを想定しています。 ワークスペースの計画に関連する原則および考慮事項の詳細については、[Azure Monitor でログ データとワークスペースを管理する](https://docs.microsoft.com/azure/azure-monitor/platform/manage-access)についての記事を参照してください。

## <a name="planning-considerations"></a>計画に関する考慮事項

管理サービスをオンボードするために作成するワークスペースとアカウントを準備するときは、以下で説明する問題を参照してください。

- **Azure の地域と規制遵守**。 Azure リージョンは "*地域*" に分かれています。 [Azure の地域](https://azure.microsoft.com/global-infrastructure/geographies)では、データの保存場所、主権、コンプライアンス、回復性に関する要件が地域的な境界内で確実に遵守されます。 ワークロードがデータ主権またはその他のコンプライアンス要件の対象となる場合、ワークスペースと Automation アカウントは、それらがサポートするワークロード リソースと同じ Azure の地域内のリージョンにデプロイされる必要があります。
- **ワークスペースの数**。 原則として、Azure の地域ごとに必要な最も少ない数のワークスペースを作成します。 コンピューティング リソースやストレージ リソースが配置される Azure の地域ごとに少なくとも 1 つのワークスペースを作成することをお勧めします。 この初期の配置により、今後データを異なる地域に移行する場合に規制の問題を回避しやすくなります。
- **データ保持とキャッピング**。 ワークスペースや Automation アカウントを作成するとき、データ保持ポリシーまたはデータ キャッピングの要件も考慮に入れることが必要な場合もあります。 ワークスペースを計画するときこれらの原則およびその他の考慮事項の詳細については、[Azure Monitor でログ データとワークスペースを管理する](https://docs.microsoft.com/azure/azure-monitor/platform/manage-access)についての記事を参照してください。
- **リージョン マッピング**。 Log Analytics ワークスペースと Azure Automation アカウントのリンクは特定の Azure リージョン間でのみサポートされます。 たとえば、Log Analytics ワークスペースが *EastUS* リージョンにホストされている場合、リンクされる Automation アカウントは、管理サービスと一緒に使用するには *EastUS2* リージョンに作成する必要があります。 他のリージョンに作成した Automation アカウントは、*EastUS* 内のワークスペースにリンクできません。 デプロイ リージョンの選択は、Azure の地域要件に大きく影響する可能性があります。 ワークスペースと Automation アカウントをホストするリージョンを決定する際は、[リージョン マッピング テーブル](https://docs.microsoft.com/azure/automation/how-to/region-mappings)を参照してください。
- **ワークスペース マルチホーム**。 Log Analytics エージェントは一部のシナリオでマルチホームをサポートしますが、この構成で実行すると、エージェントはいくつかの制限事項および問題に直面します。 ご使用のシナリオでマルチホームを使用することを Microsoft が推奨しない限り、Log Analytics エージェントでマルチホームを構成することは推奨されません。

## <a name="resource-placement-examples"></a>リソースの配置例

Log Analytics ワークスペースと Automation アカウントの配置先となるサブスクリプションの選択には、複数の異なるモデルがあります。 つまり、ワークスペースと Automation アカウントは、Update Management、Change Tracking、およびインベントリ サービスの実装を担当するチームが所有するサブスクリプションに配置する必要があります。

以下の例は、ワークスペースおよび Automation アカウントをデプロイできるいくつかの方法を示したものです。

### <a name="placement-by-geography"></a>地理別の配置

サブスクリプションが 1 つで、複数の Azure 地域にまたがる数百個のリソースを持つ小規模から中規模の環境の場合、各地域に 1 つの Log Analytics ワークスペースと 1 つの Azure Automation アカウントを作成します。

リソース グループごとにワークスペースと Azure Automation アカウントのペアを 1 つ作成し、そのペアを仮想マシンに対応する地域にデプロイすることができます。 あるいは、データ コンプライアンス ポリシーでリソースを特定のリージョンに置くことが規定されていない場合、1 つのペアを作成してすべての仮想マシンを管理できます。 また、より細かいロールベースのアクセス制御 (RBAC) を行うために、ワークスペースと Automation アカウントのペアを別々のリソース グループに配置することも推奨されます。

次の図に示した例では、それぞれ異なる地域に配置される2 つのリソース グループを持つ 1 つのサブスクリプションを使用します。

![小規模から中規模の環境のワークスペース モデル](./media/workspace-model-small.png)

### <a name="placement-in-a-management-subscription"></a>管理サブスクリプション内への配置

複数のサブスクリプションにまたがり、監視とコンプライアンスを担当する中央の IT 部門を持つ大規模な環境の場合、ワークスペースと Automation アカウントのペアは、IT 管理サブスクリプション内に作成します。 このモデルでは、ある地域内の仮想マシン リソースは、そのデータを IT 管理サブスクリプション内の対応する地域ワークスペース内に格納します。 自動化タスクを実行する必要があるが、リンクされたワークスペースおよび Automation アカウントが不要なアプリケーション チームは、独自のアプリケーション サブスクリプション内に別の Automation アカウントを作成することができます。

![大規模な環境のワークスペース モデル](./media/workspace-model-large.png)

### <a name="decentralized-placement"></a>分散型の配置

大規模な環境の別のモデルにおいて、修正プログラムの適用および管理の責任がアプリケーション開発チームにある場合もあります。 この場合、ワークスペースと Automation アカウントのペアを、他のリソースと一緒にアプリケーション チーム サブスクリプションに配置する必要があります。

  ![分散型環境のワークスペース アカウント モデル](./media/workspace-model-decentralized.png)

## <a name="create-a-workspace-and-automation-account"></a>ワークスペースと Automation アカウントを作成する

ワークスペースとアカウントのペアを配置して編成する最善の方法が決まったら、オンボード プロセスを開始する前にこれらのリソースが作成されていることを確認する必要があります。 このガイダンスの後の方に含まれる自動化の例では、ワークスペースと Automation アカウントのペアが自動的に作成されます。 ただし、既存のワークスペースと Automation アカウントのペアがない場合、ポータルを使用してオンボードする場合はペアを作成する必要があります。

Azure portal で Log Analytics ワークスペースを作成するには、「[ワークスペースを作成する](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace#create-a-workspace)」を参照してください。 次に、「[Azure Automation アカウントを作成する](https://docs.microsoft.com/azure/automation/automation-quickstart-create-account)」の手順に従って、各ワークスペースに対応する Automation アカウントを作成します。

> [!NOTE]
> Azure portal から Automation アカウントを作成する場合、既定の動作では、Azure Resource Manager とクラシック デプロイ モデルの両方のリソースについて実行アカウントを作成しようとします。 環境内にクラシック VM を持たず、サブスクリプションの共同管理者でない場合、ポータルでは Resource Manager の実行アカウントが作成されますが、クラシック実行アカウントをデプロイするときにエラーが生成されます。 クラシック リソースをサポートする予定がない場合は、このエラーを無視することができます。
>
> [PowerShell](https://docs.microsoft.com/azure/automation/manage-runas-account#create-run-as-account-using-powershell) を使用して実行アカウントを作成することもできます。

## <a name="next-steps"></a>次の手順

Azure 管理サービスに対して[サーバーをオンボードする](./onboarding-overview.md)方法をご覧ください。

> [!div class="nextstepaction"]
> [Azure サーバー管理サービスへのオンボード](./onboarding-overview.md)
