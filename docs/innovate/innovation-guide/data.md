---
title: Azure イノベーション ガイド:データの民主化
titleSuffix: Microsoft Cloud Adoption Framework for Azure
description: Azure を利用したデータの民主化について説明します。
author: absheik
ms.author: absheik
ms.date: 10/17/2019
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: innovate
ms.custom: fasttrack-new, AQC
ms.localizationpriority: high
ms.openlocfilehash: 65c1ecd1d722286fb495af3069862131629c35d1
ms.sourcegitcommit: 0d14d89b9004a65a322724342cb5086ad2c77467
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2019
ms.locfileid: "72777083"
---
::: zone target="docs"

# <a name="azure-innovation-guide-democratize-data"></a>Azure イノベーション ガイド:データの民主化

::: zone-end

::: zone target="chromeless"

# <a name="democratize-data"></a>データの民主化

::: zone-end

データ民主化の最初の一歩として、データを見つけやすくすることが挙げられます。 データ共有のカタログ化と管理は、企業が既存の情報資産から最大の価値を得るために役立ちます。 データ カタログがあれば、データを管理するユーザーにとってデータ ソースを見つけやすくなり、わかりやすくなります。 Azure Data Catalog は企業内の管理を可能にし、Azure Data Share は企業外の管理と共有を可能にします。

Azure Time Series Insights や Stream Analytics など、データを処理する Azure サービスも、イノベーション ニーズのためにお客様やパートナーが活用し、成果を上げている機能です。

# <a name="catalogtabcatalog"></a>[カタログ](#tab/Catalog)

## <a name="azure-data-catalog"></a>Azure Data Catalog

Azure Data Catalog は、データの利用者がデータの検索に苦労する問題に対処するものであり、かつ、データの生産者が情報資産を維持することを可能にするものです。 IT とビジネス間の溝をなくし、すべての人が分析情報を共有して貢献できるようにします。 必要な場所でデータを活用し、自分で選んだツールで接続します。 登録されたデータ資産を検索できるユーザーを制御します。 オープンな REST API を利用し、既存のツールやプロセスに統合されます。

> [!div class="checklist"]
>
> - Register
> - 検索と注釈
> - 接続と管理

::: zone target="docs"

**[[Azure Data Catalog]](https://docs.microsoft.com/azure/data-catalog) に移動します。**

::: zone-end

::: zone target="chromeless"

### <a name="action"></a>Action

サポートされる Azure Data Catalog は組織あたり 1 つだけです。 自分の組織にカタログが既に作成されている場合、カタログを追加することはできません。

組織の Azure Data Catalog を作成するには:

1. **[Azure Data Catalog]** に移動します。
2. **[作成]** ボタンをクリックします。

<!-- markdownlint-disable DOCSMD001 -->

::: form action="OpenBlade[#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.DataCatalog%2Fcatalogs]" submitText="Go to Azure Data Catalog" :::

<!-- markdownlint-enable DOCSMD001 -->

::: zone-end

# <a name="sharetabshare"></a>[共有](#tab/Share)

## <a name="azure-data-share"></a>Azure Data Share

イノベーションにおいて、データを公開することと、共有するデータと共有する相手を制御することが重要な推進要素となります。 企業はデータの民主化に努めるとき、データの法外なボリューム、ペース、ライフサイクルにたやすく圧倒されるかもしれません。 プロバイダーは Azure Data Share を利用し、データ共有の使用条件を指定することでデータの処理方法を自由に操ることができます。 データ コンシューマーは、データを受け取る前に、これらの条件に同意する必要があります。 データ プロバイダーは、データ コンシューマーが更新を受け取る頻度を指定できます。 データ プロバイダーは、新しい更新へのアクセスをいつでも取り消すことができます。

> [!div class="checklist"]
>
> - データ共有を作成する。
> - データ共有にデータセットを追加する。
> - データ共有の同期スケジュールを有効にする。
> - データ共有に受信者を追加する。

::: zone target="docs"
**[[Azure Data Share]](https://docs.microsoft.com/azure/data-share) に移動します。**

::: zone-end

::: zone target="chromeless"

<!-- markdownlint-disable MD024 -->

### <a name="action"></a>Action

Azure Data Share を作成するには:

1. **[Azure Data Share]** に移動します。
2. **[Create Data Share]\(データ共有の作成\)** をクリックします。

<!-- markdownlint-disable DOCSMD001 -->

::: form action="OpenBlade[#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.DataShare%2Faccounts]" submitText="Go to Azure Data Share" :::

<!-- markdownlint-enable DOCSMD001 -->

::: zone-end

[Azure Open Datasets](https://docs.microsoft.com/azure/open-datasets/overview-what-are-open-datasets) を使用し、モデルに[休日](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays)、[天候](https://azure.microsoft.com/services/open-datasets/catalog/noaa-global-forecast-system)、[空間画像](https://azure.microsoft.com/services/open-datasets/catalog/hls)データを組み込み、分析を強化します。

[ビジネス プロセスの民主化](https://docs.microsoft.com/business-applications-release-notes/october18/microsoft-flow/democratize-business-processes)と[市民開発者に力を与える](https://docs.microsoft.com/business-applications-release-notes/october18/microsoft-flow/empower-citizen-developers)ことが次の手順です。リンクにそれぞれの情報があります。

# <a name="insightstabinsights"></a>[分析情報](#tab/Insights)

## <a name="azure-time-series-insights"></a>Azure Time Series Insights

データ イノベーションの能力は無限です。データ ストリームや多層化ストレージから IoT 規模の時系列データやモデルをほぼリアルタイムで検索し、未加工の利用統計情報を関連付け、アセットに基づく分析情報を引き出します。 継続的に他のデータ ソリューションと円滑に統合したり、根本原因を分析したり、異常を検出したり、Time Series Insights プラットフォームで特注のアプリケーション オプションを追加したりできます。

> [!div class="checklist"]
>
> - Time Series Insights 環境を計画します。
> - エクスプローラーでデータを視覚化します。
> - データ リテンション期間を理解します。
> - カスタム ビューを開発し、共有します。

::: zone target="docs"

**[[Azure Time Series Insights]](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-overview) に移動します。**

::: zone-end

::: zone target="chromeless"

<!-- markdownlint-disable DOCSMD001 -->

### <a name="action"></a>Action

Azure Time Series Insights 環境を作成するには:

1. **[Azure Time Series Insights]** に移動します。
2. **[Time Series Insights 環境の作成]** をクリックします。
3. この環境をイベント ソース (IoT Hub または Event Hub) に指定します。

::: form action="OpenBlade[#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.TimeSeriesInsights%2Fenvironments]" submitText="Go to Azure Time Series Insights" :::

<!-- markdownlint-enable DOCSMD001 -->

::: zone-end
