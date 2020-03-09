---
title: 移行に重点を置いたコスト管理のメカニズム
description: Azure リソースの予算と支払い方法を設定し、請求書の内容を確認する方法について説明します。
author: bandersmsft
ms.author: banders
ms.date: 08/08/2019
ms.topic: guide
ms.service: cloud-adoption-framework
ms.subservice: migrate
ms.custom: fasttrack-edit, AQC
ms.localizationpriority: high
ms.openlocfilehash: 82c801abee32e0399eb124440868bc455034a018
ms.sourcegitcommit: 72a280cd7aebc743a7d3634c051f7ae46e4fc9ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2020
ms.locfileid: "78222660"
---
# <a name="migration-focused-cost-control-mechanisms"></a>移行に重点を置いたコスト管理のメカニズム

クラウドでは、テクノロジ チームでの役割には関係なく、作業方法にいくつかのシフトが導入されます。 コストは、このシフトの格好の例です。 以前には、IT 資産 (インフラストラクチャ、アプリ、およびデータ) のコストに関心があったのは財務および IT リーダーシップだけでした。 クラウドは IT のすべてのメンバーの能力を高めて、エンド ユーザーをより適切にサポートする意思決定を行い、それに基づいて行動できるようにします。 ただし、その能力には、これらの意思決定を行うときにコストを意識する必要があるという責任が付属します。

この記事では、Azure への移行前、移行中、および移行後に、コストに関する賢い意思決定を行うのに役立つツールを導入します。

この記事のツールには、次のものがあります。

> - Azure Migrate
> - Azure 料金計算ツール
> - Azure TCO 計算ツール
> - Azure Cost Management
> - Azure Advisor

この記事で説明されているプロセスにはまた、IT マネージャー、財務、または基幹業務アプリケーションの所有者とのパートナーシップが必要になる場合もあります。

<!-- markdownlint-disable MD024 MD025 -->

# <a name="estimate-vm-costs-prior-to-migration"></a>[移行の前に VM のコストを見積もる](#tab/EstimateVMCosts)

資産 (インフラストラクチャ、アプリ、またはデータ) を移行する前に、これらの資産に関する観察されたパフォーマンス条件に基づいてコストを見積もり、サイズ設定を改良する機会が存在します。 コストの見積もりには、次の 2 つの目的があります。つまり、コスト管理が可能になり、現在の予算が必要なパフォーマンス要件を満たすことを確認するためのチェックポイントが提供されます。

## <a name="cost-calculators"></a>コスト計算ツール

手動のコスト計算の場合は、移行されるワークロードのアーキテクチャに基づいて迅速なコスト見積もりを提供できる 2 つの便利な計算ツールが存在します。

- Azure [料金計算ツール](https://azure.microsoft.com/pricing/calculator)は、手動で入力された Azure 製品に基づいてコスト見積もりを提供します。
- 決定のために、将来のクラウド コストと現在のオンプレミス コストの比較が必要になる場合があります。 [総保有コスト (TCO) 計算ツール](https://azure.microsoft.com/pricing/tco/calculator)は、このような比較を提供できます。

これらの手動のコスト計算ツールを独自に使用すると、潜在的な支出や削減を予測できます。 これらを Azure Migrate のコスト予測ツールと組み合わせて使用すると、代替アーキテクチャまたはパフォーマンス制約に合うようにコスト予測を調整することもできます。

## <a name="azure-migrate-calculations"></a>Azure Migrate の計算

**前提条件:** このタブの残りでは、読者が移行される資産 (インフラストラクチャ、アプリ、およびデータ) のコレクションを既に Azure Migrate に入力していることを前提にしています。 評価に関する前の記事では、初期データの収集に関する指示が提供されています。 データが入力されたら、次のいくつかの手順に従って、収集されたデータに基づいて毎月のコストを見積もります。

Azure Migrate は、コレクターやサービス マップによってキャプチャされたデータに基づいて、**毎月のコスト見積もり**を計算します。 次の手順では、コスト見積もりを読み込みます。

1. ポータルで [Azure Migrate Assessment]\(Azure Migrate の評価) に移動します。
1. プロジェクトの **[概要]** ページで、 **[+ 評価の作成]** を選択します。
1. **[すべて表示]** を選択して、評価のプロパティを確認します。
1. グループを作成し、グループ名を指定します。
1. グループに追加するマシンを選びます。
1. **[評価の作成]** を選択して、グループと評価を作成します。
1. 評価が作成されたら、それを [概要] > [ダッシュボード] で表示します。
1. ポータル ナビゲーションの [評価の詳細] セクションで、 **[コスト詳細]** を選択します。

結果の見積もり (下の図を参照) によって、コンピューティングとストレージの毎月のコストが識別されます。これは多くの場合、クラウド コストの最大の部分を表しています。

![コスト詳細ビュー](./media/manage-costs/compute-storage-monthly-cost-estimate.png)
*図 1 - Azure Migrate での評価の [コスト詳細] ビューの画像。*

## <a name="additional-resources"></a>その他のリソース

- [Azure Migrate を使用した評価の設定と確認](https://docs.microsoft.com/azure/migrate/tutorial-assess-vmware#set-up-an-assessment)
- 多数の資産 (インフラストラクチャ、アプリ、およびデータ) にわたるコスト管理に関するより包括的な計画については、[クラウド導入フレームワーク ガバナンス モデル](../../govern/guides/index.md)に関するページを参照してください。 特に、[Cost Management の規範](../../govern/cost-management/index.md)と[複雑な企業向けのガバナンス ガイドの Cost Management の改善](../../govern/guides/complex/cost-management-improvement.md)に関するページを参照してください。

# <a name="estimate-and-optimize-vm-costs-during-and-after-migration"></a>[移行中および移行後に VM のコストを見積もり、最適化する](#tab/EstimateOptimize)

移行の前のコストの見積もりによって、コスト予測のための確固としたターゲットが提供されます。 また、移行される各資産 (インフラストラクチャ、アプリ、およびデータ) のパフォーマンスやコスト ニーズを検討する機会も提供されます。 ただし、それはまだ見積もりにすぎません。 資産が移行され、負荷がかかる状態になったら、実際の負荷または合成された負荷に基づいて、より正確なコスト計算を作成できます。

## <a name="azure-advisor-cost-recommendations"></a>Azure Advisor のコストに関する推奨事項

資産 (インフラストラクチャ、アプリ、およびデータ) を Azure に移行してから 24 時間以内に、Azure Advisor は、資産に関するフィードバックを提供するために各資産のパフォーマンスの監視を開始します。 収集されたフィードバックの 1 つの項目は、コストと使用率のバランスに関連しています。

次の手順では、現在のサブスクリプション内の資産 (インフラストラクチャ、アプリ、およびデータ) のコストに関する推奨事項を提供します。

1. ポータルで **[Azure Advisor]** に移動します。 それを行うには、Azure Portal の左側のナビゲーション ウィンドウで **[Advisor]** を選択します。 左側のウィンドウに Advisor が表示されない場合は、 **[すべてのサービス]** を選択します。 サービス メニュー ウィンドウで、 **[Monitoring and Management] (監視と管理)** の **[Advisor]** を選択します。
2. Advisor ダッシュボードには、選択したすべてのサブスクリプションの推奨事項の概要が表示されます。 サブスクリプション フィルター ドロップダウンを使用して、推奨事項を表示するサブスクリプションを選択できます。
3. コストに関する推奨事項を表示するには、[コスト] タブを選択します。

## <a name="azure-cost-management"></a>Azure Cost Management

Azure Cost Management は、消費習慣のより総合的なビュー (コストの詳細ビューや一定期間にわたる消費傾向を含む) を提供できます。 大規模な移行や複雑な移行の場合は、このビューにより、コスト管理に関する広範囲にわたる意思決定を行うために必要な分析情報が提供される可能性があります。

前提条件:このタブの残りでは、読者が Azure セットアップ ガイドの完了中に Azure Cost Management の設定を完了していることを前提にしています。 Azure Cost Management の構成の詳細については、この [Azure セットアップ ガイド](../../ready/azure-setup-guide/manage-costs.md)を参照してください。 データが入力されたら、次のいくつかの手順に従って、収集されたデータに基づいて毎月のコストを見積もります。

次の手順では、サブスクリプションに対する Azure Cost Management のコスト分析データを読み込みます。

1. ポータルで **[コストの管理と請求]** に移動します。 左側のウィンドウに [コストの管理と請求] が表示されない場合は、 **[すべてのサービス]** を選択します。 サービス メニュー ウィンドウで、 **[Monitoring and Management]\(監視と管理\)** の **[コストの管理と請求]** を選択します。
2. [コストの管理と請求] で、左側のナビゲーション ウィンドウの **[コスト管理]** を選択して、クラウド コストの分析と最適化を開始します。
3. [コスト管理] で、 **[コスト分析]** を選択します。
    a. コスト分析で別のスコープに切り替えるには、 **[スコープ]** ピルを使用します。

この分析を使用すると、総コスト、予算 (使用可能な場合)、および累積コストをレビューできます。 各計算は、サービス、リソース、および期間ごとに表示できます。 最も重要なこととして、コストをタグごとに分析できます。 資産 (インフラストラクチャ、アプリ、およびデータ) の適切な名前付けおよびタグ付けは、すべての健全なガバナンスやコスト管理プロセスの基本的な開始点です。 適切なタグにより、コストの管理が改善されると共に、パフォーマンスやコストの最適化のより明確な効果が得られます。

## <a name="additional-resources"></a>その他のリソース

- 多数の資産 (インフラストラクチャ、アプリ、およびデータ) にわたるコスト管理に関するより包括的な計画については、[クラウド導入フレームワーク ガバナンス モデル](../../govern/guides/index.md)に関するページを参照してください。 特に、[Cost Management の規範](../../govern/cost-management/index.md)と[複雑な企業向けのガバナンス ガイドの段階的な Cost Management の改善](../../govern/guides/complex/cost-management-improvement.md)に関するページを参照してください。
- Azure Advisor の詳細については、[Azure Advisor を使用したサービス コストの削減](https://docs.microsoft.com/azure/advisor/advisor-cost-recommendations)に関するページを参照してください。
- Azure Cost Management の詳細については、「[スコープを理解して使用する](https://docs.microsoft.com/azure/cost-management/understand-work-scopes)」および「[コスト分析を使用してコストを調査および分析する](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis)」を参照してください。

# <a name="tips-and-tricks-to-optimize-costs"></a>[コストを最適化するためのヒントとコツ](#tab/TipsTricks)

この記事で説明されているツールに加えて、全体的なクラウド コストを急速に削減するために役立ついくつかのヒントとコツがあります。 知っておく必要があるいくつかの高レベルのヒントを次に示します。

## <a name="avoid-unnecessary-spending"></a>不必要な消費を避ける

理論的には、既存のデータセンター内のほとんどの資産 (インフラストラクチャ、アプリ、およびデータ) をクラウドに移行できます。 ただし、そうすべきというわけではありません。 各ワークロードの評価中に、そのワークロードを移行する必要があることを検証します。 [増分型の合理化](../../digital-estate/rationalize.md)に関するクラウド導入フレームワークの記事は、どの資産を移行すべきかを決定するのに役立ちます。

## <a name="reduce-waste"></a>無駄を減らす

Azure でインフラストラクチャをデプロイしたら、それが使用されていることを確認することが重要です。 すぐに節約を始める最も簡単な方法は、使用されていないリソースを確認して削除することです。

## <a name="reduce-overprovisioning"></a>オーバープロビジョニングを減らす

見積もりへの最適なアプローチを使用した場合でも、オーバープロビジョニングされ、十分に活用されていない資産 (インフラストラクチャ、アプリ、およびデータ) が存在する可能性があります。 前の 2 つのタブにあるツールを使用したこれらの資産のレビューにより、パフォーマンス要件をより適切に満たし、コストを削減するために資産のサイズ設定を削減するための潜在的な手段が識別されます。

## <a name="take-advantage-of-available-discounts"></a>使用可能な割引を利用する

Microsoft アカウント担当者と話をして、どのようにしたら現在の割引オプションを利用できるかを理解します。 コストを削減するためによく使用される割引のいくつかの例を次に示します。

## <a name="azure-reservations"></a>Azure の予約

[Azure の予約](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)を使用すると、仮想マシンまたは SQL Database のコンピューティング容量の 1 年または 3 年分を前払いできます。 前払いでは、使用するリソースに関する割引を受けることができます。 Azure の予約では、仮想マシンまたは SQL Database のコンピューティング コストを大幅に削減できます (1 年または 3 年のどちらかの前払い契約の従量課金制の料金で最大 72 %)。 予約は課金割引を提供するもので、仮想マシンまたは SQL Database の実行時の状態には影響しません。

## <a name="use-azure-hybrid-benefit"></a>Azure ハイブリッド特典を利用する

オンプレミス デプロイで既に Windows Server または SQL Server のライセンスを持っている場合は、[Azure ハイブリッド特典](https://azure.microsoft.com/pricing/hybrid-benefit)プログラムを使用して Azure で節約できます。 Windows Server を利用した場合、各ライセンスに OS のコストが含まれ (最大 2 台の仮想マシンが対象)、お客様が支払うのは基本的なコンピューティング コストのみとなります。 既存の SQL Server ライセンスを使用すれば、仮想コアベースの SQL Database オプションで最大 55% の節約となります。 オプションには、Azure 仮想マシン内の SQL Server と、SQL Server Integration Services が含まれます。

## <a name="low-priority-vms-with-batch"></a>Batch を使用した優先順位の低い VM

優先順位の低いバックグラウンド プロセスの場合、Batch は、バックグラウンド サービス VM を管理し、コストを削減するための手段を提供します。 ただし、この割引オプションを選択する前に、[Batch を使用した優先順位の低い VM](https://docs.microsoft.com/azure/batch/batch-low-pri-vms) のパフォーマンスへの影響を理解することが重要です。

## <a name="additional-resources"></a>その他のリソース

多数の資産 (インフラストラクチャ、アプリ、およびデータ) にわたるコスト管理に関するより包括的な計画については、[クラウド導入フレームワーク ガバナンス モデル](../../govern/guides/index.md)に関するページを参照してください。 特に、[Cost Management の規範](../../govern/cost-management/index.md)と[複雑な企業向けのガバナンス ガイドの段階的な Cost Management の改善](../../govern/guides/complex/cost-management-improvement.md)に関するページを参照してください。
