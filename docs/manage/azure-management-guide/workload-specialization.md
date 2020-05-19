---
title: クラウド管理のための特殊化されたワークロード
description: Azure 向けのクラウド導入フレームワークを使用して、クラウド管理のための特殊化されたワークロード運用について学習します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 10/17/2019
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: operate
ms.custom: fasttrack-edit, AQC
ms.localizationpriority: high
ms.openlocfilehash: cd6f89162b2975313f0eebcd4e3e1385787f8ee7
ms.sourcegitcommit: 60d8b863d431b5d7c005f2f14488620b6c4c49be
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2020
ms.locfileid: "83217440"
---
# <a name="workload-specialization-for-cloud-management"></a>クラウド管理のためのワークロードの特殊化

ワークロードの特殊化は、[プラットフォームの特殊化](./platform-specialization.md)の記事に記載されている概念に基づいています。

![クラウド管理ベースラインを越えるもの](../../_images/manage/beyond-the-baseline.png)

- **ワークロードの運用:** ワークロードの運用ごとの最大の投資と、最高レベルの回復性。 ビジネス価値を促進するワークロードの約 20% にワークロードの運用を推奨します。 通常、この特殊化は、高い重要度またはミッション クリティカルなワークロード用に予約されています。
- **プラットフォームの運用:** 運用の投資は多数のワークロードに分散されています。 回復性の向上は、定義されたプラットフォームを利用するすべてのワークロードに影響します。 重要度が最も高いプラットフォームの約 20% に対してプラットフォームの運用を推奨します。 通常、この特殊化は、中程度から高い重要度のワークロード用に予約されています。
- **改善された管理ベースライン:** 相対的に最小限の運用の投資。 この特殊化では、追加のクラウドネイティブの運用ツールとプロセスを使用して、ビジネス コミットメントがわずかに改善されます。

## <a name="high-level-process"></a>プロセスの概要

ワークロードの特殊化は、反復的なアプローチで次の 4 つのプロセスの規範的な実行で構成されます。 各プロセスの詳細については、[プラットフォームの特殊化](./platform-specialization.md)に関する記事を参照してください。

- **システム設計を改善する:** 中断を効果的に最小限に抑えるために、特定のワークロードの設計を改善します。
- **修復を自動化する:** 一部の改善は費用効果が高くありません。 このような場合、修復を自動化し、中断の影響を抑える方が合理的です。
- **ソリューションをスケーリングする:** システム設計と自動修復を改善したら、サービス カタログを使用してそれらの変更を環境全体にスケーリングできます。
- **継続的な改善:** さまざまな監視ツールを使用して、段階的な改善点を見つけることができます。 これらの改善点は、システム設計、自動化、スケーリングの次のパスで対処できます。

## <a name="cultural-change"></a>文化の変化

多くの場合、ワークロードの特殊化によって、管理ベースライン、強化されたベースライン、プラットフォーム操作の提供に焦点を当てた従来の IT ビルド プロセスに文化の変化がもたらされます。 このような種類のオファリングは、環境全体にスケーリングできます。 ワークロードの特殊化は、プラットフォームの特殊化と実行方法が似ています。 ただし、一般的なプラットフォームとは異なり、個々のワークロードに必要な特殊化は、多くの場合、スケーリングされません。

ワークロードの特殊化が必要な場合、運用管理は一般に中央 IT の観点を越えて進化します。 クラウド導入フレームワークで提案されているアプローチは、クラウド管理機能の分散です。

このモデルでは、監視、デプロイ、DevOps、およびその他の技術革新に焦点を当てた機能などの運用タスクは、アプリケーション開発またはビジネスユニット組織に移行します。 クラウド プラットフォーム チームとコア クラウド監視チームは、引き続き環境全体の管理ベースラインを提供します。

これらの一元化されたチームは、ワークロードの専門チームにワークロードの操作を指導および指示します。 しかし、毎日の運用の責任は、IT の外部で管理されるクラウド管理チームにあります。 この種類の分散コントロールは、クラウドのセンター オブ エクセレンスの成熟度を示す主要指標の 1 つです。

## <a name="beyond-platform-specialization-application-insights"></a>プラットフォームの特殊化を越えるもの: Application Insights

明確なワークロード操作を行うには、特定のワークロードの詳細が必要です。 継続的な改善フェーズの間、Application Insights をクラウド管理ツールチェーンに追加する必要があります。

| 要件                          | ツール                 | 目的                                                                                |
| ------------------------------------ | -------------------- | -------------------------------------------------------------------------------------- |
| アプリケーションの監視               | Application Insights | アプリの監視と診断                                                    |
| パフォーマンス、可用性、使用状況 | Application Insights | アプリ ダッシュボード、複合マップ、使用状況、トレースを使用した高度なアプリケーション監視 |

### <a name="deploy-application-insights"></a>Application Insights のデプロイ

1. Azure portal 内で、 **[Application Insights]** に移動します。
1. **[+ 追加]** を選択して、ライブ Web アプリケーションを監視するための Application Insights リソースを作成します。
1. 画面の指示に従います。

監視用にアプリケーションを構成する方法については、[Azure Monitor Application Insights ハブ](https://docs.microsoft.com/azure/azure-monitor/azure-monitor-app-hub)のページを参照してください。

::: zone target="chromeless"

::: form action="OpenBlade[#create/Microsoft.AppInsights]" submitText="Create Application Insight resources" :::

::: zone-end

### <a name="monitor-performance-availability-and-usage"></a>パフォーマンス、可用性、使用状況を監視する

1. Azure portal で **Application Insights** を検索します。
1. 一覧から Application Insights リソースのいずれかを選択します。

Application Insights には、パフォーマンス、可用性、使用状況、および依存関係を監視するためのさまざまなオプションが含まれています。 こうしたアプリケーション データの各ビューを使用すると、継続的な改善のフィードバック ループが明確になります。

::: zone target="chromeless"

<!-- markdownlint-disable DOCSMD001 -->

::: form action="OpenBlade[#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Insights%2FComponents]" submitText="Monitor applications" :::

<!-- markdownlint-enable DOCSMD001 -->

::: zone-end
