---
title: Azure イノベーション ガイド:カスタマー フィードバックの準備
titleSuffix: Microsoft Cloud Adoption Framework for Azure
description: カスタマー フィードバックの準備
author: BrianBlanchard
ms.author: brblanch
ms.date: 10/17/2019
ms.topic: article
ms.service: cloud-adoption-framework
ms.subservice: innovate
ms.custom: fasttrack-edit, AQC
ms.localizationpriority: high
ms.openlocfilehash: ec17c66fa92abc292a19a8e74c215111d8638a05
ms.sourcegitcommit: 910efd3e686bd6b9bf93951d84253b43d4cc82b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2019
ms.locfileid: "72769364"
---
::: zone target="docs"

# <a name="azure-innovation-guide-prepare-for-customer-feedback"></a>Azure イノベーション ガイド:カスタマー フィードバックの準備

::: zone-end

::: zone target="chromeless"

# <a name="prepare-for-customer-feedback"></a>カスタマー フィードバックの準備

::: zone-end

ユーザーによる採用、エンゲージメント、リテンション期間がイノベーション成功の鍵です。 なぜですか?

革新的な新しいソリューションを作るということは、ユーザーが欲するものを、あるいはあればいいなと考えているものをユーザーに与えることではありません。 仮説を公式化し、テストしたり、改善したりできるようにすることです。 そのテストには、次の 2 つの形式があります。定量的 (テストからのフィードバック)。開発側が求めるアクションを意味します。 定性的 (顧客からのフィードバック)。メトリックが顧客にとって何を意味するのかを伝えます。 この 2 つを合わせることで、次の仮説とソリューションの次の改善に関する情報が与えられます。 このようなフィードバック ループは、この方法論の中心にある [Build-Measure-Learn プロセス](../considerations/adoption.md)の基礎となります。

フィードバック ループを統合する前に、ソリューションの共有リポジトリが必須要件となります。 集中型リポジトリでは、プロジェクトに関して寄せられるあらゆるフィードバックを記録し、それに基づいて行動できます。  [GitHub](https://github.com/) はオープン ソース ソフトウェアのホームです。 商用として開発されたアプリケーションのソース コード リポジトリをホストする目的で最も一般的に使用されるプラットフォームの 1 つでもあります。 GitHub リポジトリの作り方に関する[こちら](https://docs.microsoft.com/azure/devops/pipelines/repos/github?view=azure-devops&tabs=yaml)の記事は、自分のリポジトリを始めるときに役立ちます。

Azure の次の各ツールは、GitHub でホストされているプロジェクトと統合されます (あるいは互換性があります)。

## <a name="quantitative-feedback-for-web-appstabquantitative-apps"></a>[Web アプリの定量的フィードバック](#tab/Quantitative-Apps)

Application Insights は、アプリケーションの使用に関する定量的フィードバックをほぼリアルタイムで提供できる監視ツールです。 このフィードバックは、現在の仮説をテストして有効性を検証し、バッグログにある次の機能やユーザー ストーリーを形作るために役立ちます。

### <a name="action"></a>Action

アプリケーションの定量的データを表示するには:

1. **[App Insights]** に移動します。
2. アプリケーションが一覧に表示されない場合、 **[追加 +]** リンクをクリックし、画面の指示に従って App Insights の構成を開始します。
3. 目的のアプリが一覧にある場合、それを選択します。
4. **[概要]** ウィンドウには、アプリケーションに関するいくつかの統計情報があります。 **[アプリケーション ダッシュボード]** リンクを使用すると、カスタム ダッシュボードを作成し、仮説に対する関連性の高いデータを表示できます。

::: zone target="chromeless"

<!-- markdownlint-disable DOCSMD001 -->

::: form action="OpenBlade[#blade/HubsExtension/BrowseResourceBlade/resourceType/microsoft.insights%2Fcomponents]" submitText="Go to App Insights" :::

<!-- markdownlint-enable DOCSMD001 -->

::: zone-end

::: zone target="docs"

アプリに関するデータを表示するには、[Azure portal](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/microsoft.insights%2Fcomponents) にアクセスします。

::: zone-end

### <a name="learn-more"></a>詳細情報

- [Azure Monitor を設定する](https://docs.microsoft.com/azure/azure-monitor/learn/quick-monitor-portal)
- [Azure Monitor App Insights の使用を開始する](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-users)
- [利用統計情報ダッシュボードを作成する](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-app-dashboards)

## <a name="quantitative-feedback-for-apistabquantitative-apis"></a>[API の定量的フィードバック](#tab/Quantitative-APIs)

Connected Economy (つながる経済) が企業のイノベーションのあり方を変えています。  市場や業界の混乱がこれまでになく加速しています。 混乱の形態はさまざまであり、企業は**イノベーターのジレンマ**に取り組まなければなりません。それはつまり、進行中の経済活動でつまずくことなく、変化のペースをいかにして設定するかという問題です。

社外的に企業は API を利用し、顧客やそのパートナーとのやり取り方法を変えています。 社内的には事業の個々のパーツをシームレスにつなぐ API を使用しています。 API 経済は、ソーシャル、モバイル、分析、クラウドという 4 つの構成要素で動作します。 短期間かつ費用効果の高い方法でアプリやサービスをつなぎ、価値提案を拡張します。

<!-- markdownlint-disable MD024 -->

### <a name="action"></a>Action

API で定量的データを記録するには:

1. **[API Management サービス]** に移動します。
2. 一覧から目的の API を選択します。
3. **[監視]** セクションから **[診断設定]** を選択します。

API の定量的データを表示するには:

1. **[API Management サービス]** に移動します。
2. 一覧から目的の API を選択します。
3. **[監視]** セクションから **[アプリケーション]** を選択します。

::: zone target="chromeless"

<!-- markdownlint-disable DOCSMD001 -->

::: form action="OpenBlade[#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ApiManagement%2Fservice]" submitText="Go to API Management Service" :::

<!-- markdownlint-enable DOCSMD001 -->

::: zone-end

::: zone target="docs"

API Management サービスを開くには、[Azure portal](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ApiManagement%2Fservice) にアクセスします。

::: zone-end

### <a name="learn-more"></a>詳細情報

Azure Monitor を利用して API に関するフィードバックを得るとき、[こちら](https://docs.microsoft.com/azure/api-management/api-management-howto-use-azure-monitor)の記事が役立ちます。

## <a name="qualitative-feedbacktabqualitative"></a>[定性的フィードバック](#tab/Qualitative)

バックログ (ボード) は、フィードバックがユーザー ストーリーとして記録される場所です。 関連する作業が実行可能なタスクとして追跡記録される場所でもあります。 Azure Boards は GitHub に直接統合できて、フィードバック/作業管理とソース コードの間でシームレスな操作を可能にします。

::: zone target="docs"

### <a name="action"></a>Action

Azure Board と Azure Pipelines には、GitHub と Azure とは別のポータルが必要です。
いずれのツールを始める場合も、[Azure DevOps](https://dev.azure.com/) に移動します。

::: zone-end

::: zone target="chromeless"

<!-- markdownlint-disable DOCSMD001 -->

### <a name="action"></a>Action

DevOps プロジェクトを作成するには:

1. **Azure DevOps プロジェクト**にアクセスします。
2. **[DevOps プロジェクトの作成]** をクリックします。
3. **[Runtime, Framework and Service]\(ランタイム、フレームワーク、サービス\)** を選択します。

::: form action="OpenBlade[#blade/HubsExtension/BrowseResource/resourceType/microsoft.visualstudio%2Faccount%2Fproject]" submitText="Go to Azure DevOps Project" :::

<!-- markdownlint-enable DOCSMD001 -->

::: zone-end

### <a name="learn-more"></a>詳細情報

Azure Boards と GitHub を併用してフィードバックを集中管理する際、次のリンクが役立ちます。

- [Azure Boards の概要](https://docs.microsoft.com/azure/devops/boards/boards/kanban-quickstart?view=azure-devops)
- [Azure Boards と GitHub](https://docs.microsoft.com/azure/devops/boards/boards/kanban-quickstart?view=azure-devops)

## <a name="close-the-loop-with-pipelinestabpipelines"></a>[パイプラインでループを閉じる](#tab/pipelines)

フィードバックに基づいて行動することで、お客様が要望する機能ができあがるとは限りません。 しかしながら、あらゆるデータ ポイントから何らかの変化が生まれるはずです。 たとえば、物事に対する考え方が変わるかもしれません。 あるいは、要望とはまったく違う技術的変更が行われるかもしれません。 いずれにせよ、Azure Pipelines のようなデプロイのパイプラインとツールを利用することで、そのような変更を簡単に公開し、お客様と頻繁に共有できます。

Azure でホストされているアプリケーションに関連するアクティブなデプロイを表示するには:

### <a name="action"></a>Action

パイプラインにある現在のデプロイを表示するには:

1. **[App Service]** に移動します。
2. 一覧から目的のアプリケーションを選択します。
3. [App Service] ウィンドウの **[デプロイ]** セクションから **[デプロイ センター]** を選択します。

::: zone target="chromeless"

<!-- markdownlint-disable DOCSMD001 -->

::: form action="OpenBlade[#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Web%2Fsites]" submitText="Go to App Service" :::

<!-- markdownlint-enable DOCSMD001 -->

::: zone-end

::: zone target="docs"

App Service でアプリケーションを表示するには、[Azure portal](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Web%2Fsites) に移動します。

::: zone-end

### <a name="learn-more"></a>詳細情報

デプロイ パイプラインの構築を始める際に役立つ追加リンク:

- [最初のパイプラインを作成する](https://docs.microsoft.com/azure/devops/pipelines/create-first-pipeline?view=azure-devops&tabs=tfs-2018-2)
- [GitHub リリース タスク](https://docs.microsoft.com/azure/devops/pipelines/tasks/utility/github-release?view=azure-devops)
