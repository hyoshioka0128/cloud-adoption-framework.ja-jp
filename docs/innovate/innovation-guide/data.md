---
title: Azure イノベーション ガイド:データの民主化
titleSuffix: Microsoft Cloud Adoption Framework for Azure
description: Azure を使用してデータを民主化する方法について説明します。
author: absheik
ms.author: absheik
ms.date: 10/17/2019
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: innovate
ms.custom: fasttrack-new, AQC
ms.localizationpriority: high
ms.openlocfilehash: 6c1e253d2ff49c331f4f1c153124f2ca06c81c36
ms.sourcegitcommit: bf9be7f2fe4851d83cdf3e083c7c25bd7e144c20
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73565547"
---
::: zone target="docs"

# <a name="azure-innovation-guide-democratize-data"></a>Azure イノベーション ガイド:データの民主化

::: zone-end

::: zone target="chromeless"

# <a name="democratize-data"></a>データの民主化

::: zone-end

データの民主化の最初の一歩として、データを見つけやすくすることが挙げられます。 データ共有のカタログ化と管理は、企業が既存の情報資産から最大の価値を得るために役立ちます。 データ カタログにより、データを管理しているユーザーはデータ ソースの検出と把握が容易になります。 Azure Data Catalog により、企業内部での管理が可能となり、Azure Data Share により、企業外部での管理と共有が可能になります。

Azure Time Series Insights や Stream Analytics など、データを処理する Azure サービスも、イノベーションのニーズに対応するためにお客様やパートナーが使用し、成果を上げている機能です。

# <a name="catalogtabcatalog"></a>[カタログ](#tab/Catalog)

## <a name="azure-data-catalog"></a>Azure Data Catalog

Azure Data Catalog は、データ コンシューマーによるデータ検出の課題に対処し、データ プロデューサーが情報資産を管理することを可能にします。 IT とビジネス間のギャップが解消され、すべてのユーザーが分析情報を共有して貢献できるようになります。 必要な場所にデータを保存し、使用するツールで接続できます。 Azure Data Catalog を使用すると、登録されたデータ資産を検出できるユーザーを制御できます。 オープン REST API を使用して、既存のツールやプロセスに統合できます。

> [!div class="checklist"]
>
> - Register
> - 検索と注釈
> - 接続と管理

::: zone target="docs"

**[Azure Data Catalog のドキュメント](https://docs.microsoft.com/azure/data-catalog)に移動する**

::: zone-end

::: zone target="chromeless"

### <a name="action"></a>Action

Azure Data Catalog は、組織ごとに 1 つだけ使用できます。 組織のデータ カタログが既に作成されている場合、カタログを追加することはできません。

組織の Azure Data Catalog を作成するには、次の手順に従います。

1. **[Azure Data Catalog]** に移動します。
2. **作成** を選択します。

<!-- markdownlint-disable DOCSMD001 -->

::: form action="OpenBlade[#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.DataCatalog%2Fcatalogs]" submitText="Go to Azure Data Catalog" :::

<!-- markdownlint-enable DOCSMD001 -->

::: zone-end

# <a name="sharetabshare"></a>[共有](#tab/Share)

## <a name="azure-data-share"></a>Azure Data Share

データのオープンな共有と、共有するデータおよび共有する相手の制御のバランスを取ることが、イノベーションの重要な推進要素となります。 企業はデータの民主化を試みるときに、データの膨大な量、ペース、ライフサイクルに圧倒されがちです。 Azure Data Share を使用すると、プロバイダーはデータ共有の使用条件を指定してデータを処理する方法を制御できます。 データ コンシューマーは、データを受け取る前に、これらの条件に同意する必要があります。 データ プロバイダーは、データ コンシューマーが更新を受け取る頻度を指定できます。 データ プロバイダーは、新しい更新へのアクセスをいつでも取り消すことができます。

> [!div class="checklist"]
>
> - データ共有を作成する。
> - データ共有にデータセットを追加する。
> - データ共有の同期スケジュールを有効にする。
> - データ共有に受信者を追加する。

::: zone target="docs"
**[Azure Data Share のドキュメント](https://docs.microsoft.com/azure/data-share)に移動する**

::: zone-end

::: zone target="chromeless"

<!-- markdownlint-disable MD024 -->

### <a name="action"></a>Action

データ共有を作成するには、次の手順に従います。

1. **[Azure Data Share]** に移動します。
2. **[Create data share]\(データ共有の作成\)** をクリックします。

<!-- markdownlint-disable DOCSMD001 -->

::: form action="OpenBlade[#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.DataShare%2Faccounts]" submitText="Go to Azure Data Shares" :::

<!-- markdownlint-enable DOCSMD001 -->

::: zone-end

# <a name="insightstabinsights"></a>[分析情報](#tab/Insights)

## <a name="azure-time-series-insights"></a>Azure Time Series Insights

Azure Time Series Insights のデータ イノベーション機能は無限です。 データ ストリームのほぼリアルタイムのデータ探索と、IoT 規模の時系列データに対応する多層ストレージが提供されます。 また、未加工のテレメトリをコンテキスト化し、資産ベースの分析情報を引き出すモデルも提供されます。 Time Series Insights プラットフォーム上で、他のデータ ソリューションとのスムーズかつ継続的な統合を実現し、根本原因分析や異常検出を行うことができます。また、カスタム アプリケーションを構築することもできます。

> [!div class="checklist"]
>
> - Time Series Insights 環境を計画します。
> - エクスプローラーでデータを視覚化します。
> - データ リテンション期間を理解します。
> - カスタム ビューを開発し、共有します。

::: zone target="docs"

**[Azure Time Series Insights の概要](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-overview)に移動する**

::: zone-end

::: zone target="chromeless"

### <a name="action"></a>Action

Azure Time Series Insights 環境を作成するには:

1. **Azure Time Series Insights 環境**に移動します。
2. **[Time Series Insights 環境の作成]** を選択します。
3. この環境でイベント ソース (Azure IoT Hub または Event Hubs) を参照します。

<!-- markdownlint-disable DOCSMD001 -->

::: form action="OpenBlade[#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.TimeSeriesInsights%2Fenvironments]" submitText="Go to Azure Time Series Insights" :::

<!-- markdownlint-enable DOCSMD001 -->

::: zone-end
