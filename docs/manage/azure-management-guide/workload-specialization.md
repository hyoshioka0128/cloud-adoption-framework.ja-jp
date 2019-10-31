---
title: Azure のクラウド管理のためのワークロードの特殊化
titleSuffix: Microsoft Cloud Adoption Framework for Azure
description: ワークロード固有のクラウド管理操作を改善する
author: BrianBlanchard
ms.author: brblanch
ms.date: 10/17/2019
ms.topic: article
ms.service: cloud-adoption-framework
ms.subservice: operate
ms.custom: fasttrack-edit, AQC
ms.localizationpriority: high
ms.openlocfilehash: 51bdf23ccb2262202dfa095c5e9b57f727d11d3b
ms.sourcegitcommit: 35c162d2d09ec1c4a57d3d57a5db1d56ee883806
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/17/2019
ms.locfileid: "72556991"
---
# <a name="workload-specialization-for-cloud-management"></a>クラウド管理のためのワークロードの特殊化

ワークロードの特殊化は、[プラットフォームの特殊化](./platform-specialization.md)の記事に記載されている概念に基づいています。

![クラウド管理ベースラインを越えるもの](../../_images/manage/beyond-the-baseline.png)

- **ワークロードの運用:** ワークロード操作の投資あたりの最大数。 最高レベルの回復性。 ビジネス価値を促進するワークロードの +/-20% に推奨されます。 通常は、高い重要度またはミッション クリティカルなワークロード用に予約されています。
- **プラットフォームの運用:** 運用の投資は多数のワークロードに分散されています。 回復性の向上は、定義されたプラットフォームを利用するすべてのワークロードに影響します。 最高の重要度のプラットフォームの +/-20% に推奨されます。 通常は、中程度から高い重要度のワークロード用に予約されています。
- **拡張された管理ベースライン:** 最小限の相対的な操作の投資。 追加のクラウドネイティブの運用ツールとプロセスを使用して、わずかに改善されたビジネス コミットメント。

## <a name="high-level-process"></a>プロセスの概要

ワークロードの特殊化は、反復的なアプローチで次の 4 つのプロセスの規範的な実行で構成されます。 各プロセスの詳細については、[プラットフォームの特殊化](./platform-specialization.md)に関する記事を参照してください。

- **システム設計を改善する:** 中断を効果的に最小限に抑えるために、特定のワークロードの設計を改善します。
- **修復を自動化する:** 一部の改善は費用効果が高くありません。 このような場合、修復を自動化し、中断の影響を軽減する方が適切な場合があります。
- **ソリューションをスケーリングする:** システムの設計と自動修復が改善されると、それらの変更をサービス カタログを介して環境全体に拡張できます。
- **継続的な改善:** さまざまな監視ツールを使用して、システム設計、自動化、スケーリングの次のパスで対処できる段階的な改善点を見つけることができます。

## <a name="cultural-change"></a>文化の変化

ワークロードの特殊化によって、多くの場合、文化の変化がトリガーされます。 従来の IT は、管理ベースライン、強化されたベースライン、プラットフォーム操作の提供に焦点を当てたプロセスを構築します。 このような種類のオファリングは、環境全体に拡張できます。 ワークロードの特殊化は、プラットフォームの特殊化と実行方法が非常に似ています。 ただし、一般的なプラットフォームとは異なり、個々のワークロードに必要な特殊化は、多くの場合、スケーリングされません。

ワークロードの特殊化が必要な場合、運用管理が中央 IT の観点を越えて進化することは一般的です。 クラウド導入フレームワークで提案されているアプローチは、クラウド管理機能の分散です。

このモデルでは、監視、デプロイ、DevOps、およびその他の技術革新に焦点を当てた機能などの運用タスクは、アプリケーション開発またはビジネス ユニット組織に移行します。 クラウド プラットフォームとコア クラウド監視チームは、引き続き環境全体の管理ベースラインを提供します。 これらの一元化されたチームは、ワークロードの専門チームにワークロードの操作を指導および指示します。 しかし、毎日の運用の責任は、IT の外部で管理されるクラウド管理チームにあります。 この種類の分散コントロールは、クラウドのセンター オブ エクセレンスの成熟度を示す主要指標の 1 つです。

## <a name="beyond-platform-specialization---application-insights"></a>プラットフォームの特殊化を越えるもの - Application Insights

明確なワークロード操作を行うには、特定のワークロードの詳細が必要です。 継続的な改善フェーズの間、Application Insights をクラウド管理ツールチェーンに追加する必要があります。

|アプリケーションの監視|Application Insights|アプリの監視と診断| |パフォーマンス、可用性、使用状況|Application Insights|アプリ ダッシュボード、複合マップ、使用状況、トレースを使用した高度なアプリケーション監視|

### <a name="deploy-application-insights"></a>Application Insights のデプロイ

1. Azure portal で **Application Insights** を検索します。
2. **[+ 追加]** をクリックして、ライブ Web アプリケーションを監視するための Application Insights リソースを作成します。
3. 画面の指示に従います

監視用にアプリケーションを構成する方法については、[Azure Monitor Application Insights ハブ](https://docs.microsoft.com/azure/azure-monitor/azure-monitor-app-hub)のページを参照してください。

::: zone target="chromeless"

::: form action="OpenBlade[#create/Microsoft.AppInsights]" submitText="Create Application Insight resources" :::

::: zone-end

### <a name="monitor-performance-availability-and-usage"></a>パフォーマンス、可用性、使用状況を監視する

1. Azure portal で **Application Insights** を検索します。
2. 一覧から Application Insights リソースのいずれかを選択します。

Application Insights のナビゲーションには、パフォーマンス、可用性、使用状況、および依存関係を監視するためのさまざまなオプションが含まれています。 こうしたアプリケーション データの各ビューを使用すると、継続的な改善のフィードバック ループが明確になります。

::: zone target="chromeless"

<!-- markdownlint-disable DOCSMD001 -->

::: form action="OpenBlade[#blade/HubsExtension/BrowseResourceBlade/resourceType/microsoft.insights%2Fcomponents]" submitText="Monitor applications" :::

<!-- markdownlint-enable DOCSMD001 -->

::: zone-end
