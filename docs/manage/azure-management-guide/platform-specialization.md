---
title: クラウド管理のための特殊化されたプラットフォーム
description: Azure 向けのクラウド導入フレームワークを使用して、自動修復やシステム設計の改善を含む、プラットフォーム固有のクラウド管理操作を改善します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 10/17/2019
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: operate
ms.custom: fasttrack-edit, AQC
ms.localizationpriority: high
ms.openlocfilehash: 6d4b2e0677b896bf61e4c57b49dda548a6719b69
ms.sourcegitcommit: d88c1cc3597a83ab075606d040ad659ac4b33324
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/15/2020
ms.locfileid: "84785482"
---
# <a name="platform-specialization-for-cloud-management"></a>クラウド管理のためのプラットフォームの特殊化

強化された管理ベースラインと同様に、プラットフォームの特殊化は標準の管理ベースラインを越える拡張機能です。 次の画像およびリストでは、管理ベースラインを拡張する方法を示しています。 この記事では、プラットフォームの特殊化のオプションについて説明します。

![クラウド管理ベースラインを越えるもの](../../_images/manage/beyond-the-baseline.png)

- **ワークロードの運用:** ワークロードの運用ごとの最大の投資と、最高レベルの回復性。 ビジネス価値を促進するワークロードの約 20% にワークロードの運用を推奨します。 通常、この特殊化は、高い重要度またはミッション クリティカルなワークロード用に予約されています。
- **プラットフォームの運用:** 運用の投資は多数のワークロードに分散されています。 回復性の向上は、定義されたプラットフォームを利用するすべてのワークロードに影響します。 重要度が最も高いプラットフォームの約 20% に対してプラットフォームの運用を推奨します。 通常、この特殊化は、中程度から高い重要度のワークロード用に予約されています。
- **改善された管理ベースライン:** 相対的に最小限の運用の投資。 この特殊化では、追加のクラウドネイティブの運用ツールとプロセスを使用して、ビジネス コミットメントがわずかに改善されます。

ワークロードとプラットフォームの両方の運用で、設計とアーキテクチャの原則を変更する必要があります。 これらの変更には時間がかかる可能性があり、運用経費が増加する場合があります。 このような投資を必要とするワークロードの数を削減するために、管理ベースラインを拡張することにより、ビジネス コミットメントを十分に高められる可能性があります。

次の表では、改善された顧客の管理ベースラインに共通する共通のプロセス、ツール、および考えられる影響の概要を示します。

| Process | ツール | 目的  | 推奨される管理レベル |
|---|---|---|---|
| システム設計を改善する | Microsoft Azure Well-Architected Framework | プラットフォームのアーキテクチャ設計の改善による操作の向上 | 該当なし |
| 自動修復 | Azure Automation | プラットフォーム固有の自動化による高度なプラットフォーム データへの対応 | プラットフォームの運用 |
| サービス カタログ | マネージド アプリケーション センター | 組織の標準を満たす承認済みソリューションのセルフサービス カタログの提供 | プラットフォームの運用 |
| コンテナーのパフォーマンス | コンテナーに対する Azure Monitor | コンテナーの監視と診断 | プラットフォームの運用 |
| サービスとしてのプラットフォーム (PaaS) データ パフォーマンス | Azure の SQL 分析 | PaaS データベースの監視と診断 | プラットフォームの運用 |
| サービスとしてのインフラストラクチャ (IaaS) データ パフォーマンス | SQL Server の正常性チェック | IaaS データベースの監視と診断 | プラットフォームの運用 |

## <a name="high-level-process"></a>プロセスの概要

プラットフォームの特殊化は、反復的なアプローチで次の 4 つのプロセスの規範的な実行で構成されます。 各プロセスの詳細については、この記事の以降のセクションで説明します。

- **システム設計を改善する:** 中断を効率的に最小化するために、共通システムまたはプラットフォームの設計を改善します。
- **修復を自動化する:** 一部の改善は費用効果が高くありません。 このような場合、修復を自動化し、中断の影響を抑える方が合理的です。
- **ソリューションをスケーリングする:** システムの設計と自動修復が改善されると、それらの変更をサービス カタログを介して環境全体に拡張できます。
- **継続的な改善:** さまざまな監視ツールを使用して、増分的な改善を検出できます。 これらの改善点は、システム設計、自動化、スケーリングの次のパスで対処できます。

::: zone target="docs"

## <a name="improve-system-design"></a>システム設計を改善する

::: zone-end
::: zone target="chromeless"

## <a name="improve-system-design"></a>[システム設計を改善する](#tab/SystemsDesign)

::: zone-end

システム設計の改善は、どの共通プラットフォームでも運用を改善する上で最も効果的なアプローチです。 システム設計の改善により、安定性を高め、ビジネスの中断を減らすことができます。 個々のシステムの設計は、Azure 向けのクラウド導入フレームワーク全体で使用される環境ビューの範囲外です。

このフレームワークを補完するものとして、[Microsoft Azure Well-Architected Framework](https://docs.microsoft.com/azure/architecture/framework) では、プラットフォームまたは特定のワークロードの品質向上のための基本原則が提供されます。フレームワークの焦点は、アーキテクチャ エクセレンスの 5 つの要素の向上に当てられています。

- **コストの最適化**: もたらされる価値が最大になるようにコストを管理します。
- **オペレーショナル エクセレンス:** 運用環境でのシステムの動作を維持するオペレーショナル プロセスに従います。
- **パフォーマンス効率:** 負荷の変化に合わせてシステムをスケーリングします。
- **信頼性:** 障害から回復して動作を続行するようにシステムを設計します。
- **セキュリティ:** 脅威からアプリケーションとデータを保護します。

技術的負債とアーキテクチャの欠陥は、ほとんどのビジネス中断の原因となります。 既存のデプロイの場合、システム設計の改善は、既存の技術的負債の返済と見なすことができます。 新規のデプロイでは、これらの改善点を技術的負債の回避と見なすことができます。

次の **[自動修復]** タブでは、対処できない、または対処するべきではない技術的負債を修復する方法について説明します。

システム設計の改善については、[Microsoft Azure Well-Architected Framework](https://docs.microsoft.com/azure/architecture/framework) の詳細を確認してください。

システムの設計が向上したら、この記事に戻り、お使いの環境全体で改良を加え、スケーリングする新しい機会を見つけてください。

::: zone target="docs"

## <a name="automated-remediation"></a>自動修復

::: zone-end
::: zone target="chromeless"

## <a name="automated-remediation"></a>[自動修復](#tab/AutomatedRemediation)

::: zone-end

一部の技術的負債には対処できません。 解決策は、修正にコストがかかりすぎる場合や、計画されているにもかかわらず、プロジェクトの期間が長い場合があります。 ビジネスの中断はビジネスに大きな影響を与えない可能性があります。 また、ビジネスの優先順位が回復性への投資ではなく迅速な復旧の場合もあります。

技術的負債の解決が望ましいアプローチではない場合、通常は、自動修復が次の手順になります。 自動修復では、Azure Automation と Azure Monitor を使用して傾向を検出し、自動修復を提供することが最も一般的なアプローチです。

自動修復に関するガイダンスについては、[Azure Automation とアラート](https://docs.microsoft.com/azure/automation/automation-create-alert-triggered-runbook)に関する記事をご覧ください。

::: zone target="docs"

## <a name="scale-the-solution-with-a-service-catalog"></a>サービス カタログを使用したソリューションのスケーリング

::: zone-end
::: zone target="chromeless"

## <a name="scale-the-solution-with-a-service-catalog"></a>[サービス カタログを使用したソリューションのスケーリング](#tab/ServiceCatalog)

::: zone-end

管理の行き届いたサービス カタログは、プラットフォームの特殊化とプラットフォームの運用の基礎です。 カタログの使用は、システムの設計の改善と修復が環境全体でどのようにスケーリングされるかを示しています。

クラウド プラットフォーム チームおよびクラウド自動化チームは、どの環境でも最も一般的なプラットフォームに繰り返し可能なソリューションを作成するために配置されます。 ただし、これらのソリューションが一貫した方法で使用されていない場合、クラウド管理でベースライン オファリングを超えるものはほとんど提供されない可能性があります。

最適化されたプラットフォームを最大限に導入し、メンテナンスのオーバーヘッドを最小限に抑えるには、そのプラットフォームを Azure サービス カタログに追加する必要があります。 カタログ内の各アプリケーションは、サービス カタログを介した内部使用のため、または外部の消費者向けのマーケットプレース オファリングとしてデプロイすることができます。

サービス カタログに発行する手順については、[サービス カタログの発行](https://docs.microsoft.com/azure/managed-applications/publish-service-catalog-app)に関する一連の記事を参照してください。

### <a name="deploy-applications-from-the-service-catalog"></a>サービス カタログからアプリケーションをデプロイする

1. Azure portal 内で、**マネージド アプリケーション センター (プレビュー)** に移動します。
2. **[参照]** ペインで、 **[サービス カタログ アプリケーション]** を選択します。
3. **[+ 追加]** を選択して、会社のサービス カタログからアプリケーション定義を選択します。

サービスを提供しているすべてのマネージド アプリケーションが表示されます。

::: zone target="chromeless"

<!-- markdownlint-disable DOCSMD001 -->

::: form action="OpenBlade[#blade/Microsoft_Azure_Appliance/ManagedAppsHubBlade/browseServiceCatalog]" submitText="Go to Virtual Machines" :::

<!-- markdownlint-enable DOCSMD001 -->

::: zone-end

### <a name="manage-service-catalog-applications"></a>サービス カタログ アプリケーションを管理する

1. Azure portal 内で、**マネージド アプリケーション センター (プレビュー)** に移動します。
1. **[サービス]** ペインで、 **[サービス カタログ アプリケーション]** を選択します。

サービスを提供しているすべてのマネージド アプリケーションが表示されます。

::: zone target="chromeless"

::: form action="OpenBlade[#blade/Microsoft_Azure_Appliance/ManagedAppsHubBlade/serviceServiceCatalogApps]" submitText="Go to Virtual Machines" :::

::: zone-end

::: zone target="docs"

## <a name="continuous-improvement"></a>継続的な改善

::: zone-end
::: zone target="chromeless"

## <a name="continuous-improvement"></a>[継続的な改善](#tab/ContinuousImprovement)

::: zone-end

プラットフォームの特化とプラットフォームの運用は共に、導入、プラットフォーム、自動化、および管理の各チーム間で作成される強力なフィードバック ループに依存します。 各チームは、データ内のこのフィードバック ループを基にすることで、より賢明な意思決定を行うことができます。 プラットフォームの運用で長期的なビジネス コミットメントを達成するには、一元化されたプラットフォーム固有の分析情報を使用することが重要です。

コンテナーと SQL Server は、最も一般的な 2 つの一元管理プラットフォームです。 以下の記事は、これらのプラットフォームでの継続的な改善データ収集の開始に役立ちます。

- [コンテナーのパフォーマンス](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-overview)
- [PaaS データベースのパフォーマンス](https://docs.microsoft.com/azure/azure-monitor/insights/azure-sql)
- [IaaS データベースのパフォーマンス](https://docs.microsoft.com/azure/azure-monitor/insights/sql-assessment)
