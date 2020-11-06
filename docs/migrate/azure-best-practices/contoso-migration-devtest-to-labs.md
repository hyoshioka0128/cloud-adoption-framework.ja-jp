---
title: Dev/Test 環境を Azure DevTest Labs に移行する
description: Contoso が、Azure DevTest Labs を使用して、オンプレミスの Dev/Test 環境を Azure に移行する方法について説明します。
author: deltadan
ms.author: abuck
ms.date: 07/1/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: migrate
ms.openlocfilehash: 68941187e45d393ac21e00027a7b54998625597a
ms.sourcegitcommit: fbfd66dab002b549d3e9cbf1b7efa0099d0b7700
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2020
ms.locfileid: "93283367"
---
# <a name="migrate-a-devtest-environment-to-azure-devtest-labs"></a>Dev/Test 環境を Azure DevTest Labs に移行する

この記事では、Contoso という架空の会社が、Dev/Test 環境を Azure DevTest Labs に移行する方法について説明します。

## <a name="migration-options"></a>移行オプション

Contoso には、Dev/Test 環境を Azure に移行するときに使用できるオプションがいくつかあります。

| 移行オプション | 結果 |
| --- | --- |
| [Azure Migrate](/azure/migrate/migrate-services-overview) | オンプレミスの VM を[評価](/azure/migrate/tutorial-assess-vmware-azure-vm)し、[移行](/azure/migrate/tutorial-migrate-vmware)する。 <br><br> Azure IaaS (サービスとしてのインフラストラクチャ) を使用して、Dev/Test サーバーを実行する。 <br><br> [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) を使用して VM を管理する。 |
| [DevTest Labs](/azure/devtest-labs/devtest-lab-overview) | 開発およびテスト環境を迅速にプロビジョニングします。 <br><br> クォータとポリシーを使用して無駄を最小限に抑える。 <br><br> 自動シャットダウンを設定してコストを最小限に抑える。 <br><br> Windows および Linux 環境を構築する。 |

> [!NOTE]
> この記事では、DevTest Labs を使用して、オンプレミスの Dev/Test 環境を Azure に移行する方法について説明します。 Contoso が、Azure Migrate を使用して Dev/Test を Azure IaaS に移行した方法については、[こちら](./contoso-migration-devtest-to-iaas.md)をご覧ください。

## <a name="business-drivers"></a>ビジネス ドライバー

開発リーダーシップ チームは、この移行で達成したいことを簡単にまとめました。

- 開発者が DevOps ツールとセルフサービス環境にアクセスできるようにする。
- 継続的インテグレーション/継続的デリバリー (CI/CD) パイプライン用の DevOps ツールと、AI、機械学習、サーバーレスなどの Dev/Test 用クラウドネイティブ ツールにアクセスできるようにする。
- Dev/Test 環境でのガバナンスとコンプライアンスを確保する。
- すべての Dev/Test 環境をデータセンターから移行することでコストを節約し、ソフトウェアを開発するためのハードウェアを今後は購入しない。

> [!NOTE]
> Contoso は、[開発テスト用の従量課金制プランのサブスクリプション オファー](https://azure.microsoft.com/offers/ms-azr-0023p/)を自社の環境で使用します。 チームのアクティブな各 Visual Studio サブスクライバーは、Azure Virtual Machines のサブスクリプションに含まれている Microsoft ソフトウェアを、追加料金なしで Dev/Test 用に使用できます。 Contoso は、実行する VM の Linux 料金のみを支払います。 それには、通常であればさらに高い料金がかかる SQL Server、SharePoint Server、その他のソフトウェアの VM が含まれます。

<!-- -->

> [!NOTE]
> Enterprise Agreement を締結している Azure のお客様は、[Azure Dev/Test サブスクリプション プラン](https://azure.microsoft.com/offers/ms-azr-0148p/)からもメリットが得られます。 詳細については、[EA ポータルを使用した EA Dev/Test サブスクリプションの有効化と作成](https://channel9.msdn.com/blogs/ea.azure.com/enabling-and-creating-ea-devtest-subscriptions-through-the-ea-portal)に関するビデオを参照してください。

## <a name="migration-goals"></a>移行の目標

Contoso 開発チームは、この移行の目標を明確にしました。 これらの目標を使用して、最良の移行方法を決定します。

- 開発およびテスト環境を迅速にプロビジョニングします。 開発者がソフトウェアを作成またはテストするために必要なインフラストラクチャを、数か月ではなく数分で構築できるようにします。
- 移行後、Azure の Dev/Test 環境は、オンプレミスの現在のシステムよりも機能が強化されている必要があります。
- IT 部門によってプロビジョニングされる VM から、セルフサービス プロビジョニングを使用する DevOps に運用モデルを移行します。
- Contoso は、オンプレミスの Dev/Test 環境から迅速に移行したいと考えています。
- すべての開発者が、Dev/Test 環境にリモートで安全に接続できるようにします。

## <a name="solution-design"></a>ソリューション設計

Contoso は、目標と要件を明確にした後、デプロイ ソリューションを設計してレビューします。 このソリューションには、Dev/Test 用に使用する Azure サービスが含まれています。

### <a name="current-architecture"></a>現在のアーキテクチャ

- Contoso のアプリケーション用の Dev/Test VM は、オンプレミスのデータセンターの VMware で実行されています。
- これらの VM は、コードが運用 VM に昇格される前の開発およびテストに使用されます。
- 開発者は各自のワークステーションを管理していますが、ホーム オフィスからリモートで接続するための新しいソリューションを必要としています。

### <a name="proposed-architecture"></a>提案されたアーキテクチャ

- Contoso は、[Azure Dev/Test サブスクリプション](https://azure.microsoft.com/offers/ms-azr-0023p/)を使用して、Azure リソースのコストを削減します。 このサブスクリプションは、Microsoft ソフトウェアのライセンス料金が発生しない VM など、大幅な節約を実現します。
- Contoso は、環境の管理に DevTest Labs を使用します。 クラウドでの開発とテストのための新しいツールへの移行をサポートするために、新しい VM は DevTest Labs で作成されます。
- Contoso データセンター内のオンプレミスの Dev/Test VM は、移行の完了後に使用停止になります。
- 開発者とテスト担当者は、ワークステーションの Windows Virtual Desktop にアクセスできるようになります。

![シナリオ アーキテクチャの図。](./media/contoso-migration-devtest-to-labs/architecture.png)

" _図 1:シナリオのアーキテクチャ_

### <a name="database-considerations"></a>データベースの考慮事項

進行中の開発をサポートするために、Contoso は VM 上で実行されているデータベースを引き続き使用することにしました。 ただし、現在の VM は、DevTest Labs で実行される新しい VM に置き換えられます。 今後、Contoso は [Azure SQL Database](/azure/azure-sql/database/sql-database-paas-overview) や [Azure Database for MySQL](/azure/mysql/overview) などのサービスとしてのプラットフォーム (PaaS) サービスの使用を推進します。

現在の VMware データベース VM は使用停止になり、DevTest Labs の Azure VM に置き換えられます。 既存のデータベースは、簡単なバックアップと復元によって移行されます。 Azure Dev/Test サブスクリプション プランを使用すると、Windows Server インスタンスや SQL Server インスタンスのライセンス料金が発生しないため、コンピューティング コストを最小限に抑えることができます。

### <a name="solution-review"></a>ソリューションのレビュー

Contoso は、長所と短所の一覧をまとめて、提案された設計を評価します。

| 考慮事項 | 詳細 |
| --- | --- |
| **長所** | 現在のすべての開発 VM (アプリケーションおよびデータベース) は、DevTest Labs で実行されている新しい VM に置き換えられます。 これにより、専用のクラウド開発環境の機能を利用できるようになります。 <br><br> Contoso は、Azure Dev/Test サブスクリプションへの投資を利用してライセンス料金を節約できます。 <br><br> Contoso は、Azure でアプリケーション VM の完全な制御を維持します。 <br><br> 開発者にサブスクリプションに対する権限が与えられるため、開発者は IT 部門が要求に対応するのを待たずに新しいリソースを作成できるようになります。 |
| **短所** | この移行では、開発のみがクラウドに移行されます。 開発者が引き続き VM を使用しているため、開発に PaaS サービスは使用されません。 その結果、Contoso には、セキュリティ更新プログラムなど、VM の操作のサポートを開始する必要が生じます。 IT 部門は過去に VM を管理していましたが、Contoso ではこの新しい運用タスクのソリューションを見つける必要があります。 <br><br> Contoso は新しいアプリケーションおよびデータベース VM を構築して、プロセスを自動化する必要があります これは、クラウドでの VM 構築と、DevTest Labs によって提供されるツールを利用できることを意味します。 したがって、この一覧では短所として挙げられていますが、結果はプラスになります。 |

### <a name="migration-process"></a>移行プロセス

Contoso は、DevTest Labs を使用して、開発アプリケーションおよびデータベース VM を新しい Azure VM に移行します。

- Contoso には、開発用仮想ネットワークを含め、[Azure インフラストラクチャ](./contoso-migration-infrastructure.md)が既に整っています。
- すべての準備ができたら、Contoso は DevTest Labs をプロビジョニングして構成します。
- Contoso は、開発用仮想ネットワークを構成し、リソース グループを割り当て、ポリシーを設定します。
- Contoso は、開発者がリモートの場所で使用する Windows Virtual Desktop インスタンスを作成します。
- Contoso は、開発用の DevTest Labs 内で VM を作成し、データベースを移行します。

![移行プロセスを示す図。](./media/contoso-migration-devtest-to-labs/migration-process-devtest-labs.png)

" _図 2:移行プロセス_

## <a name="prerequisites"></a>前提条件

このシナリオを実行するために Contoso に必要なものを以下に示します。

| 必要条件 | 詳細 |
| --- | --- |
| **Azure Dev/Test サブスクリプション** | Contoso は、コストが最大 80% 削減される [Azure Dev/Test サブスクリプション](https://azure.microsoft.com/offers/ms-azr-0023p/)を作成します。 <br><br> Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/free/)を作成してください。 <br><br> 無料アカウントを作成する場合、サブスクリプションの管理者としてすべてのアクションを実行できます。 <br><br> 既存のサブスクリプションを使用する場合、自分が管理者ではないなら、管理者と協力して自分に所有者または共同作成者のアクセス許可を割り当てます。 <br><br> より詳細なアクセス許可が必要な場合は、[Azure のロールベースのアクセス制御を使用した Site Recovery のアクセス管理](/azure/site-recovery/site-recovery-role-based-linked-access-control)に関するページを参照してください。 |
| **Azure インフラストラクチャ** | Contoso が [Azure インフラストラクチャを設定する方法](./contoso-migration-infrastructure.md)を確認します。 |

## <a name="scenario-steps"></a>シナリオのステップ

Contoso の管理者が移行を実行する方法を次に示します。

> [!div class="checklist"]
>
> - 手順 1:新しい Azure Dev/Test サブスクリプションをプロビジョニングし、DevTest Labs インスタンスを作成します。
> - 手順 2:開発用仮想ネットワークを構成し、リソース グループを割り当て、ポリシーを設定します。
> - 手順 3:開発者がリモートの場所から使用する Windows 10 Enterprise マルチセッション仮想デスクトップを作成します。
> - 手順 4:開発用の DevTest Labs 内で数式と VM を作成し、データベースを移行します。

## <a name="step-1-provision-a-new-azure-devtest-subscription-and-create-a-devtest-labs-instance"></a>手順 1:新しい Azure Dev/Test サブスクリプションをプロビジョニングし、DevTest Labs インスタンスを作成する

Contoso の管理者は、まず、Azure Dev/Test プランを使用して新しいサブスクリプションをプロビジョニングし、DevTest Labs インスタンスを作成する必要があります。

これらを次のように設定します。

管理者はリンクをたどって [Azure Dev/Test サブスクリプション プラン](https://azure.microsoft.com/offers/ms-azr-0023p/)にアクセスし、新しいサブスクリプションをプロビジョニングします。これにより、システムでコストを最大 80% 削減できます。 このプランでは、管理者は Dev/Test のために Windows 10 イメージを Azure 上で実行できます。 リモート開発者の管理エクスペリエンスを簡素化する、[Windows Virtual Desktop](/azure/virtual-desktop/overview) にアクセスできるようになります。

![開発テスト用の従量課金制プランのオファーのスクリーンショット ([有効化する] ボタンが表示されている)。](./media/contoso-migration-devtest-to-labs/devtest-subscription.png)

" _図 3:Azure Dev/Test サブスクリプション プラン_

新しいサブスクリプションのプロビジョニングが完了したら、Contoso の管理者は、Azure portal を使用して新しい DevTest Labs インスタンスを作成します。 この新しいラボは、`ContosoDevRG` リソース グループに作成されます。

![ポータル上の DevTest Labs の [作成] ボタンのスクリーンショット。](./media/contoso-migration-devtest-to-labs/new-lab.png)

" _図 4:新しい DevTest Labs インスタンスの作成_

## <a name="step-2-configure-the-development-virtual-network-assign-a-resource-group-and-set-policies"></a>手順 2:開発用仮想ネットワークを構成し、リソース グループを割り当て、ポリシーを設定する

DevTest Labs インスタンスの作成が完了したら、Contoso は次の構成を実行します。

1. 仮想ネットワークを構成します。

   1. ポータルで DevTest Labs インスタンスを開き、 **[構成とポリシー]** を選択します。

      ![ContosoDevTestLabs の [設定] の [構成とポリシー] のスクリーンショット。](./media/contoso-migration-devtest-to-labs/configure-lab.png)

      _図 5:DevTest Labs インスタンス: 構成とポリシー_

   2. Contoso は、 **[仮想ネットワーク]**  >  **[+ 追加]** の順に選択し、 `vnet-dev-eus2` を選択して **[保存]** を選択します。 これにより、開発用仮想ネットワークを VM のデプロイに使用できるようになります。 仮想ネットワークは、DevTest Labs インスタンスのデプロイ時にも作成されています。

      ![仮想ネットワークを追加するための選択画面のスクリーンショット。](./media/contoso-migration-devtest-to-labs/vnets.png)

      _図 6:仮想ネットワーク_

2. リソース グループを割り当てます。

    - Contoso は、リソースが `ContosoDevRG` リソース グループに確実にデプロイされるように、ラボ設定でこれを構成します。 また、開発者に **[共同作成者]** ロールを割り当てます。

      ![リソース グループを割り当てる選択画面のスクリーンショット。](./media/contoso-migration-devtest-to-labs/assign-resource-group.png)

      _図 7:リソース グループの割り当て_

    > [!NOTE]
    > 共同作成者ロールは、他のユーザーにアクセス権を付与する権限以外のすべての権限を持つ管理者レベルのロールです。 詳細については、[Azure のロールベースのアクセス制御](/azure/role-based-access-control/overview)に関するページを参照してください。

3. ラボ ポリシーを設定します。

    1. Contoso は、開発者がチーム ポリシーの範囲内で DevTest Labs を使用できるようにする必要があります。 Contoso は、これらのポリシーを使用して DevTest Labs を構成します。

    1. Contoso は、ローカル時刻 (7:00:00 PM) と正しいタイム ゾーンを指定して、自動シャットダウンを有効にします。

       ![自動シャットダウン設定の選択画面のスクリーンショット。](./media/contoso-migration-devtest-to-labs/autoshutdown.png)

       _図 8:自動シャットダウン_

    1. Contoso は、開発者がオンラインになって作業を開始するときに VM が実行されているようにするため、自動開始を有効にします。 それらは、ローカル タイム ゾーンと開発者が作業する曜日に合わせて構成されます。

       ![自動開始設定の選択画面のスクリーンショット。](./media/contoso-migration-devtest-to-labs/autostart.png)

       _図 9:自動開始_

    1. Contoso は、許可される VM サイズを構成して、大規模でコストが高い VM を起動できないようにします。

       ![許可される VM サイズを構成するための選択画面のスクリーンショット。](./media/contoso-migration-devtest-to-labs/vm-sizes.png)

       _図 10:許可される VM サイズ_

    1. Contoso は、サポートのメッセージを構成します。

       ![サポートのメッセージを構成するための選択画面のスクリーンショット。](./media/contoso-migration-devtest-to-labs/support.png)

       _図 11:サポートのメッセージ_

## <a name="step-3-create-windows-10-enterprise-multi-session-virtual-desktops-for-developers-to-use-from-remote-locations"></a>手順 3:開発者がリモートの場所から使用する Windows 10 Enterprise マルチセッション仮想デスクトップを作成する

Contoso は、リモート開発者向けに Windows Virtual Desktop ベースを作成する必要があります。

1. Contoso は、 **[すべての仮想マシン]**  >  **[+ 追加]** の順に選択し、VM の Windows 10 Enterprise マルチセッション ベースを選択します。

   ![Windows 10 ベースの選択画面のスクリーンショット](./media/contoso-migration-devtest-to-labs/windows-10-vm-base.png)

   _図 12:Windows 10 Enterprise マルチセッション ベース_

1. Contoso は、VM のサイズに加えて、インストールする成果物も構成します。 この場合、開発者は、Visual Studio Code、Git、Chocolatey などの一般的な開発ツールにアクセスできます。

   ![選択された成果物を示すスクリーンショット。](./media/contoso-migration-devtest-to-labs/artifacts.png)

   _図 13:アーティファクト_

1. Contoso は、VM 構成が正確かどうかを確認します。

   ![ベースから仮想マシンを作成するための選択画面のスクリーンショット。](./media/contoso-migration-devtest-to-labs/vm-from-base.png)

   _図 14:ベースからの仮想マシンの作成_

1. VM が作成されると、Contoso のリモート開発者はこの開発ワークステーションに接続して作業に使用できるようになります。 選択した成果物がインストールされているので、開発者は自分のワークステーションを構成する時間を節約できます。

   ![RemoteDevs 仮想マシンの情報を示すスクリーンショット。](./media/contoso-migration-devtest-to-labs/remote-vm.png)

   _図 15:リモート開発者 VM_

## <a name="step-4-create-formulas-and-vms-within-devtest-labs-for-development-and-migrate-databases"></a>手順 4:開発用の DevTest Labs 内で数式と VM を作成し、データベースを移行する

DevTest Labs が構成され、リモート開発者のワークステーションが稼働状態になったら、Contoso は開発用の VM の構築に注力します。 まず、次の手順を実行します。

1. Contoso は、アプリケーションとデータベース VM の数式 (再利用可能なベース) を作成し、その数式を使用してアプリケーションとデータベース VM をプロビジョニングします。

   Contoso は、 **[数式]**  >  **[+ 追加]** の順に選択し、 **Windows Server 2012 R2 Datacenter** ベースを選択します。

   ![Windows 2012 R2 ベースの選択画面のスクリーンショット。](./media/contoso-migration-devtest-to-labs/windows-2012-base.png)

   _図 16:Windows 2012 R2 ベース_

1. Contoso は、VM のサイズに加えて、インストールする成果物も構成します。 この場合、開発者は、Visual Studio Code、Git、Chocolatey などの一般的な開発ツールにアクセスできます。

   ![Windows 2012 R2 ベースの構成で選択されている VM サイズと成果物を示すスクリーンショット。](./media/contoso-migration-devtest-to-labs/windows-2012-base-configuration.png)

   _図 17:Windows 2012 R2 ベースの構成_

1. データベース VM の数式を作成するために、Contoso は同じ基本手順を実行します。 今回は、ベースとして SQL Server 2012 イメージを選択します。

   ![SQL Server 2012 R2 ベースの選択画面のスクリーンショット。](./media/contoso-migration-devtest-to-labs/sql-2012-base.png)

   _図 18:SQL Server 2012 イメージ_ "

1. Contoso は、サイズと成果物を指定して数式を構成します。 成果物には、このデータベース開発 VM の数式に必要な SQL Server Management Studio が含まれています。

   ![SQL 2012 R2 ベースの構成を示すスクリーンショット。](./media/contoso-migration-devtest-to-labs/sql-2012-base-configuration.png)

   _図 19:SQL 2020 R2 ベースの構成_

   [Azure DevTest Labs での数式の使用](/azure/devtest-labs/devtest-lab-manage-formulas)に関する詳細を確認してください。

1. これで、開発者がアプリケーションとデータベースに使用する Windows ベースの数式が作成されました。

   ![構成されたデータベース VM を示すスクリーンショット。](./media/contoso-migration-devtest-to-labs/contoso-formulas.png)

   _図 20:Windows ベースの数式_

次に、これらの数式を使用して、アプリケーションとデータベース VM をプロビジョニングします。

1. 数式が作成されたら、Contoso は次に、 **[すべての仮想マシン]** を選択し、現在のアプリケーション開発 VM の構成に対応する数式 **Windows2012AppDevVmBase** を選択します。

   ![アプリ VM をベースとして選択する画面のスクリーンショット。](./media/contoso-migration-devtest-to-labs/app-vm.png)

   _図 21:アプリケーション開発 VM_

1. Contoso は、このアプリケーション VM に必要なサイズと成果物を指定して VM を構成します。

   ![アプリ VM のサイズと成果物の選択画面のスクリーンショット。](./media/contoso-migration-devtest-to-labs/app-vm-configuration.png)

   _図 22:VM のサイズとアーティファクトの構成_

1. Contoso は、現在のデータベース開発 VM の構成に対応する数式 **SQLDbDevVmBase** を使用して、データベース VM をプロビジョニングします。

   ![データベース VM のプロビジョニングを示すスクリーンショット。](./media/contoso-migration-devtest-to-labs/database-vm.png)

   _図 23:データベース VM_

1. Contoso は、必要なサイズと成果物を指定して VM を構成します。

   ![データベース VM のサイズと成果物の選択画面のスクリーンショット。](./media/contoso-migration-devtest-to-labs/database-vm-config.png)

   _図 24:VM のデータベース構成_

1. リモート開発者のワークステーションと最初の VM の作成が完了すると、Contoso の開発者が Azure でコードの記述を開始する準備が整います。

   ![Contoso の仮想マシンを示すスクリーンショット。](./media/contoso-migration-devtest-to-labs/contoso-vms.png)

   _図 25:Contoso VM_

1. Contoso は、バックアップから、または何らかの種類のコード生成プロセスを使用して VM 上にスキーマを構築することによって、開発データベースを復元できるようになりました。 成果物を使用して SQL Server Management Studio が既にインストールされているので、これらはツールをインストールする必要のない単純なタスクです。

## <a name="clean-up-after-migration"></a>移行後にクリーンアップする

Contoso は、引き続きこれらの手順に従い、DevTest Labs を使用して VM を Azure に移行します。 各移行が完了すると、すべての開発 VM が DevTest Labs で実行されるようになります。

次に、Contoso は、以下のクリーンアップ手順を完了する必要があります。

- vCenter インベントリから VM を削除します。
- ローカル バックアップ ジョブからすべての VM を削除します。
- VM の新しい場所と IP アドレスを示すように内部ドキュメントを更新します。
- VM と対話しているリソースを確認し、すべての関連する設定またはドキュメントを新しい構成を反映するように更新します。

### <a name="security"></a>セキュリティ

Contoso セキュリティ チームは、Azure VM を調査して、セキュリティの問題を特定します。 アクセスを制御するために、VM のネットワーク セキュリティ グループ (NSG) を見直します。 NSG は、許可されたトラフィックだけがアプリケーションに到達できるようにするために使用されます。 チームは、Azure Disk Encryption と Azure Key Vault を使用して、ディスク上のデータをセキュリティで保護することも検討します。 詳細については、「[Azure における IaaS ワークロードのセキュリティに関するベスト プラクティス](/azure/security/fundamentals/iaas)」を参照してください。

### <a name="licensing-and-cost-optimization"></a>ライセンスとコストの最適化

- Contoso は、すべての Azure 開発リソースがこの Dev/Test サブスクリプションを使用して作成されるようにすることで、コストを 80% 削減できます。
- 確実にコストを抑え、オーバープロビジョニングが誤って発生しないようにするため、すべての DevTest Labs インスタンスと VM のポリシーに照らして予算が確認されます。
- Contoso は、[Azure Cost Management + Billing](/azure/cost-management-billing/cost-management-billing-overview) を有効にして、Azure リソースの監視と管理を支援します。

## <a name="conclusion"></a>まとめ

この記事では、Contoso が開発環境を DevTest Labs に移行しました。 また、リモート開発者と契約開発者向けのプラットフォームとして、Windows Virtual Desktop を実装しました。

**さらにサポートが必要な場合**

まず、お使いのサブスクリプションで [DevTest Labs インスタンスを作成](/azure/devtest-labs/devtest-lab-create-lab)し、[開発者向けの DevTest Labs](/azure/devtest-labs/devtest-lab-developer-lab) を使用する方法をご確認ください。
