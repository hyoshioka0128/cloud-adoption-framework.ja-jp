---
title: Azure サーバー管理サービスの前提条件となる計画
titleSuffix: Microsoft Cloud Adoption Framework for Azure
description: Azure サーバー管理サービスの前提条件となるツールおよび計画
author: BrianBlanchard
ms.author: brblanch
ms.date: 05/10/2019
ms.topic: article
ms.service: cloud-adoption-framework
ms.subservice: operate
ms.openlocfilehash: 20a4168f5a7650b20357de2ec2628a0edb093993
ms.sourcegitcommit: 6f287276650e731163047f543d23581d8fb6e204
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/07/2019
ms.locfileid: "73751643"
---
# <a name="phase-1-prerequisite-planning-for-azure-server-management-services"></a>フェーズ 1:Azure サーバー管理サービスの前提条件となる計画

このフェーズでは、Azure サーバー管理スイートのサービスについて理解し、これらの管理ソリューションを実装するために必要なリソースをデプロイする方法を計画します。

## <a name="understand-the-tools-and-services"></a>ツールおよびサービスを理解する

以下の詳細な概要については、「[Azure サーバー管理ツールおよびサービス](./tools-services.md)」を参照してください。

- 進行中の Azure 運用に関係する管理領域。
- これらの領域でのサポートに役立つ Azure のサービスとツール。

これらのサービスの複数を組み合わせて使用して、管理要件を満たすことができます。 これらのツールはこのガイダンス全体でしばしば参照されます。

以降の各セクションでは、これらのツールとサービスを使用するために必要な計画と準備について説明します。

## <a name="log-analytics-workspace-and-automation-account-planning"></a>Log Analytics ワークスペースと Automation アカウントの計画

Azure 管理サービスをオンボードするために使用する多くのサービスでは、Log Analytics ワークスペースと、リンクされた Azure Automation アカウントが必要です。

[Log Analytics ワークスペース](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)は、Azure Monitor ログ データの格納用の一意の環境です。 各ワークスペースには、独自のデータ リポジトリと構成があります。 データ ソースとソリューションは、特定のワークスペースにデータを格納するように構成されます。 Azure 監視ソリューションでは、すべてのサーバーをワークスペースに接続することで、それらのログ データを格納してアクセスできるようにする必要があります。

管理サービスの一部では [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) アカウントが必要です。 このアカウントと Azure Automation の機能を使用して、Azure サービスと他のパブリック システムを統合して、サーバー管理プロセスをデプロイ、構成、および管理することができます。

以下の Azure サーバー管理サービスでは、リンクされた Log Analytics ワークスペースと Automation アカウントが必要です。

- [Azure Update Management](https://docs.microsoft.com/azure/automation/automation-update-management)
- [変更履歴とインベントリ](https://docs.microsoft.com/azure/automation/change-tracking)
- [Hybrid Runbook Worker](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker)
- [Desired State Configuration](https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-overview)

このガイダンスの 2 番目のフェーズは、サービスおよび自動化スクリプトのデプロイに焦点を合わせて説明します。 Log Analytics ワークスペースと Automation アカウントを作成する方法が紹介されています。 このガイダンスでは、Azure Policy を使用して、新しい仮想マシンが必ず正しいワークスペースに接続されるようにする方法も紹介しています。

このガイダンスの例では、サーバーがクラウドにまだデプロイされていないデプロイを想定しています。 ワークスペースの計画に関連する原則および考慮事項の詳細については、[Azure Monitor でログ データとワークスペースを管理する](https://docs.microsoft.com/azure/azure-monitor/platform/manage-access)についての記事を参照してください。

## <a name="planning-considerations"></a>計画に関する考慮事項

管理サービスのオンボードに必要なワークスペースとアカウントを準備するときは、次の問題を考慮します。

- **Azure の地域と規制遵守:** Azure リージョンは "*地域*" に分かれています。 [Azure の地域](https://azure.microsoft.com/global-infrastructure/geographies)では、データの保存場所、主権、コンプライアンス、回復性に関する要件が地域的な境界内で確実に遵守されます。 ワークロードがデータ主権またはその他のコンプライアンス要件の対象となる場合、ワークスペースと Automation アカウントは、それらがサポートするワークロード リソースと同じ Azure の地域内のリージョンにデプロイされる必要があります。
- **ワークスペースの数:** 原則として、Azure の地域ごとに必要な最も少ない数のワークスペースを作成します。 コンピューティング リソースやストレージ リソースが配置される Azure の地域ごとに少なくとも 1 つのワークスペースを作成することをお勧めします。 この初期の配置により、今後データを異なる地域に移行する場合に規制の問題を回避しやすくなります。
- **データ保持とキャッピング:** ワークスペースや Automation アカウントを作成するとき、データ保持ポリシーまたはデータ キャッピングの要件も考慮に入れることが必要な場合もあります。 ワークスペースを計画するときこれらの原則およびその他の考慮事項の詳細については、[Azure Monitor でログ データとワークスペースを管理する](https://docs.microsoft.com/azure/azure-monitor/platform/manage-access)についての記事を参照してください。
- **リージョン マッピング:** Log Analytics ワークスペースと Azure Automation アカウントのリンクは特定の Azure リージョン間でのみサポートされます。 たとえば、Log Analytics ワークスペースが *EastUS* リージョンにホストされている場合、リンクされる Automation アカウントは、管理サービスと一緒に使用するには *EastUS2* リージョンに作成する必要があります。 他のリージョンに作成した Automation アカウントは、*EastUS* 内のワークスペースにリンクできません。 デプロイ リージョンの選択は、Azure の地域要件に大きく影響する可能性があります。 ワークスペースと Automation アカウントをホストするリージョンを決定する際は、[リージョン マッピング テーブル](https://docs.microsoft.com/azure/automation/how-to/region-mappings)を参照してください。
- **ワークスペース マルチホーム:** Azure Log Analytics エージェントは一部のシナリオでマルチホームをサポートしますが、この構成で実行すると、エージェントはいくつかの制限事項や課題に直面します。 特定のシナリオに対してマイクロソフトが推奨する場合を除き、Log Analytics エージェント上にマルチホームを構成することはお勧めしません。

## <a name="resource-placement-examples"></a>リソースの配置例

Log Analytics ワークスペースと Automation アカウントの配置先となるサブスクリプションの選択には、複数の異なるモデルがあります。 つまり、ワークスペースと Automation アカウントは、Update Management サービスと Change Tracking および Inventory サービスの実装を担当するチームが所有するサブスクリプションに配置します。

ワークスペースと Automation アカウントを配置するいくつかの方法の例を次に示します。

### <a name="placement-by-geography"></a>地理別の配置

中小規模の環境には、単一のサブスクリプションと、複数の Azure の地域にまたがる数百のリソースがあります。 このような環境では、各地域に 1 つの Log Analytics ワークスペースと 1 つの Azure Automation アカウントを作成します。

各リソース グループには、ワークスペースと Azure Automation アカウントを 1 つのペアとして作成できます。 次に、対応する地域のペアを仮想マシンに配置します。

あるいは、データ コンプライアンス ポリシーでリソースを特定のリージョンに置くことが規定されていない場合、1 つのペアを作成してすべての仮想マシンを管理できます。 また、より細かいロールベースのアクセス制御 (RBAC) を行うために、ワークスペースと Automation アカウントのペアを別々のリソース グループに配置することも推奨されます。

次の図に示した例では、それぞれ異なる地域に配置される 2 つのリソース グループを持つ 1 つのサブスクリプションを使用します。

![小規模から中規模の環境のワークスペース モデル](./media/workspace-model-small.png)

### <a name="placement-in-a-management-subscription"></a>管理サブスクリプション内への配置

大規模な環境は複数のサブスクリプションにまたがり、監視とコンプライアンスを所有する中央の IT 部門があります。 このような環境では、ワークスペースと Automation アカウントのペアを IT 管理サブスクリプションに作成します。 このモデルでは、ある地域内の仮想マシン リソースは、そのデータを IT 管理サブスクリプション内の対応する地域ワークスペース内に格納します。 オートメーション タスクを実行する必要はあっても、リンクされたワークスペースおよび Automation アカウントが不要なアプリケーション チームは、独自のアプリケーション サブスクリプション内に別の Automation アカウントを作成することができます。

![大規模な環境のワークスペース モデル](./media/workspace-model-large.png)

### <a name="decentralized-placement"></a>分散型の配置

大規模な環境の代替モデルでは、アプリケーション開発チームは、修正プログラムの適用と管理を行うことができます。 この場合、ワークスペースと Automation アカウントのペアを、他のリソースと一緒にアプリケーション チーム サブスクリプションに配置します。

  ![分散型環境のワークスペース アカウント モデル](./media/workspace-model-decentralized.png)

## <a name="create-a-workspace-and-automation-account"></a>ワークスペースと Automation アカウントを作成する

ワークスペースとアカウントのペアを配置し、整理する最適な方法を選択したら、オンボード プロセスを開始する前に、これらのリソースを作成したことを確認します。 このガイダンスで後述するオートメーションの例では、ワークスペースと Automation アカウントのペアが自動的に作成されます。 ただし、既存のワークスペースと Automation アカウントのペアがない場合、Azure portal を使用してオンボードする場合はペアを作成する必要があります。

Azure portal を使用して Log Analytics ワークスペースを作成するには、「[ワークスペースを作成する](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace#create-a-workspace)」を参照してください。 次に、「[Azure Automation アカウントを作成する](https://docs.microsoft.com/azure/automation/automation-quickstart-create-account)」の手順に従って、各ワークスペースに対応する Automation アカウントを作成します。

> [!NOTE]
> Azure portal を使用して Automation アカウントを作成すると、ポータルの既定では、Azure Resource Manager とクラシック デプロイ モデル リソースの両方の実行アカウントの作成が試行されます。 環境内にクラシック仮想マシンが備わっておらず、サブスクリプションの共同管理者でない場合、ポータルでは Resource Manager の実行アカウントが作成されますが、クラシック実行アカウントをデプロイするときにエラーが生成されます。 クラシック リソースをサポートする予定がない場合は、このエラーを無視することができます。
>
> [PowerShell](https://docs.microsoft.com/azure/automation/manage-runas-account#create-run-as-account-using-powershell) を使用して実行アカウントを作成することもできます。

## <a name="next-steps"></a>次の手順

Azure サーバー管理サービスに対して[サーバーをオンボードする](./onboarding-overview.md)方法を参照してください。

> [!div class="nextstepaction"]
> [Azure サーバー管理サービスへのオンボード](./onboarding-overview.md)
