---
title: Azure イノベーション:フィードバックの準備
description: Azure のツールを使用して、GitHub でホストされている Web アプリと API に関する定量的で定性的なフィードバックを収集する方法について説明します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 10/17/2019
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: innovate
ms.custom: fasttrack-edit, AQC
ms.localizationpriority: high
ms.openlocfilehash: db526c15a4e33f9531a404c8c257d9c6b5b90261
ms.sourcegitcommit: 60d8b863d431b5d7c005f2f14488620b6c4c49be
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2020
ms.locfileid: "83222319"
---
::: zone target="docs"

# <a name="azure-innovation-guide-prepare-for-customer-feedback"></a>Azure イノベーション ガイド:カスタマー フィードバックの準備

::: zone-end

::: zone target="chromeless"

# <a name="prepare-for-customer-feedback"></a>カスタマー フィードバックの準備

::: zone-end

ユーザーによる採用、エンゲージメント、リテンション期間がイノベーションの成功の鍵となります。 なぜですか?

革新的な新しいソリューションの構築は、ユーザーが求めているものや欲しいと思っているものをユーザーに提供することではありません。 テストして改善できる仮説を立てることです。 そのテストには、次の 2 つの形式があります。

- **定量的 (テストからのフィードバック):** このフィードバックは、開発側が求めるアクションを評価します。
- **定性的 (顧客からのフィードバック):** このフィードバックは、メトリックが意味することを顧客の声で伝えます。

フィードバック ループを統合する前に、ソリューションの共有リポジトリを用意する必要があります。 一元化されたリポジトリにより、プロジェクトについて寄せられるすべてのフィードバックを記録し、それらのフィードバックに基づいて行動できるようになります。 [GitHub](https://github.com) はオープン ソース ソフトウェアのホームです。 商用として開発されたアプリのソース コード リポジトリをホストするために、最も一般的に使用されているプラットフォームの 1 つでもあります。 GitHub リポジトリの作成に関する[こちら](https://docs.microsoft.com/azure/devops/pipelines/repos/github?view=azure-devops&tabs=yaml)の記事は、自分のリポジトリを開始する際に役立ちます。

Azure の次の各ツールは、GitHub でホストされているプロジェクトと統合されます (または互換性があります)。

## <a name="quantitative-feedback-for-web-apps"></a>[Web アプリの定量的フィードバック](#tab/Quantitative-Apps)

Application Insights は、アプリケーションの使用状況に関するほぼリアルタイムの定量的フィードバックを提供する監視ツールです。 このフィードバックは、現在の仮説をテストして検証し、バックログにある次の機能やユーザー ストーリーを形成する際に役立ちます。

### <a name="action"></a>アクション

アプリケーションの定量的データを表示するには:

1. **Application Insights** に移動します。
   - 目的のアプリケーションが一覧に表示されない場合は、 **[追加]** を選択し、プロンプトに従って App Insights の構成を開始します。
   - 目的のアプリが一覧にある場合はそれを選択します。
1. **[概要]** ウィンドウには、アプリケーションに関するいくつかの統計情報があります。 仮説との関連性の高いデータを表示するカスタム ダッシュボードを作成するには、 **[アプリケーション ダッシュボード]** を選択します。

::: zone target="chromeless"

<!-- markdownlint-disable DOCSMD001 -->

::: form action="OpenBlade[#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Insights%2FComponents]" submitText="Go to Application Insights" :::

<!-- markdownlint-enable DOCSMD001 -->

::: zone-end

::: zone target="docs"

アプリに関するデータを表示するには、[Azure portal](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Insights%2FComponents) に移動します。

::: zone-end

### <a name="learn-more"></a>詳細情報

- [Azure Monitor を設定する](https://docs.microsoft.com/azure/azure-monitor/learn/quick-monitor-portal)
- [Azure Monitor Application Insights の概要](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-users)
- [利用統計情報ダッシュボードを作成する](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-app-dashboards)

## <a name="quantitative-feedback-for-apis"></a>[API の定量的フィードバック](#tab/Quantitative-APIs)

Connected Economy (つながる経済) が企業のイノベーションのあり方を変えつつあります。 市場や業界の混乱がこれまでになく加速しています。 混乱の形態はさまざまです。企業は、進行中のビジネス活動でつまずくことなく、変革をどのように先導するかという "_イノベーターのジレンマ_" に取り組まなければなりません。

社外的には、企業は API を使用して顧客やパートナーとやり取りする方法を変えています。 社内的には、API を使用して、ビジネスの個々の部分をシームレスにつないでいます。 API 経済は、ソーシャル、モバイル、分析、クラウドという 4 つの構成要素で機能します。 短期間かつ費用効果の高い方法でアプリやサービスをつなぎ、価値提案を拡張します。

<!-- markdownlint-disable MD024 -->

### <a name="action"></a>アクション

API で定量的データを記録するには:

1. **[API Management サービス]** に移動します。
2. 一覧から目的の API を選択します。
3. **[監視]** セクションの **[診断設定]** を選択します。

API の定量的データを表示するには:

1. **[API Management サービス]** に移動します。
2. 一覧から目的の API を選択します。
3. **[監視]** セクションの **[アプリケーション]** を選択します。

::: zone target="chromeless"

<!-- markdownlint-disable DOCSMD001 -->

::: form action="OpenBlade[#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ApiManagement%2FService]" submitText="Go to API Management services" :::

<!-- markdownlint-enable DOCSMD001 -->

::: zone-end

::: zone target="docs"

API Management サービスを開くには、[Azure portal](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ApiManagement%2FService) に移動します。

::: zone-end

### <a name="learn-more"></a>詳細情報

- [Azure Monitor を使用して API に関するフィードバックを取得する](https://docs.microsoft.com/azure/api-management/api-management-howto-use-azure-monitor)

## <a name="qualitative-feedback"></a>[定性的フィードバック](#tab/Qualitative)

バックログ (ボード) は、フィードバックがユーザー ストーリーとして記録される場所です。 関連する作業が実行可能なタスクとして追跡される場所でもあります。 Azure Boards は GitHub に直接統合できるので、フィードバック/作業管理とソース コードの間でシームレスなエクスペリエンスが実現されます。

::: zone target="docs"

### <a name="action"></a>アクション

Azure Board と Azure Pipelines には、GitHub および Azure とは別のポータルが必要です。 [Azure DevOps Services](https://azure.microsoft.com/services/devops) を使用して開始してください。

::: zone-end

::: zone target="chromeless"

<!-- markdownlint-disable DOCSMD001 -->

### <a name="action"></a>アクション

DevOps プロジェクトを作成するには:

1. **Azure DevOps Projects** に移動します。
2. **[DevOps プロジェクトの作成]** を選択します。
3. **[Runtime, Framework and Service]\(ランタイム、フレームワーク、サービス\)** を選択します。

::: form action="OpenBlade[#blade/HubsExtension/BrowseResource/resourceType/Microsoft.VisualStudio%2FAccount%2FProject]" submitText="Go to Azure DevOps Projects" :::

<!-- markdownlint-enable DOCSMD001 -->

::: zone-end

### <a name="learn-more"></a>詳細情報

次の記事は、Azure Boards と GitHub を併用してフィードバックを一元化し、管理する際に役立ちます。

- [Azure Boards の概要](https://docs.microsoft.com/azure/devops/boards/get-started/?view=azure-devops)
- [Azure Boards と GitHub](https://docs.microsoft.com/azure/devops/boards/github?view=azure-devops)

## <a name="close-the-loop-with-pipelines"></a>[パイプラインでループを閉じる](#tab/pipelines)

フィードバックに基づく行動は、顧客から要求された機能の追加を必ずしも意味するわけではありません。 しかし、あらゆるデータ ポイントから何らかの変化が生じます。 その変化は、物事に対する考え方の変化である場合もあれば、 要求されたものとはまったく異なる技術的な変更である場合もあります。 いずれにせよ、Azure Pipelines のようなデプロイ パイプラインおよびツールを使用することで、そうした変更を速やかに公開することが可能となり、顧客と頻繁に共有できるようになります。

### <a name="action"></a>アクション

パイプラインにある現在のデプロイを表示するには:

1. **[App Service]** に移動します。
2. 一覧から目的のアプリケーションを選択します。
3. [App Services] ペインの **[デプロイ]** セクションで、 **[デプロイ センター]** を選択します。

::: zone target="chromeless"

<!-- markdownlint-disable DOCSMD001 -->

::: form action="OpenBlade[#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Web%2FSites]" submitText="Go to App Services" :::

<!-- markdownlint-enable DOCSMD001 -->

::: zone-end

::: zone target="docs"

App Service でアプリケーションを表示するには、[Azure portal](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Web%2FSites) に移動します。

::: zone-end

### <a name="learn-more"></a>詳細情報

デプロイ パイプラインの構築を始める際には、次の記事をご覧ください。

- [最初のパイプラインを作成する](https://docs.microsoft.com/azure/devops/pipelines/create-first-pipeline?view=azure-devops&tabs=tfs-2018-2)
- [GitHub リリース タスク](https://docs.microsoft.com/azure/devops/pipelines/tasks/utility/github-release?view=azure-devops)
