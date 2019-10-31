---
title: Azure のクラウド管理のためのプラットフォームの特殊化
titleSuffix: Microsoft Cloud Adoption Framework for Azure
description: プラットフォーム固有のクラウド管理操作を改善します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 10/17/2019
ms.topic: article
ms.service: cloud-adoption-framework
ms.subservice: operate
ms.custom: fasttrack-edit, AQC
ms.localizationpriority: high
ms.openlocfilehash: 0386a8c30758cce6c1c3d23bfa73d1f90e919692
ms.sourcegitcommit: 35c162d2d09ec1c4a57d3d57a5db1d56ee883806
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/17/2019
ms.locfileid: "72556974"
---
# <a name="platform-specialization-for-cloud-management"></a>クラウド管理のためのプラットフォームの特殊化

強化された管理ベースラインと同様に、プラットフォームの特殊化は標準の管理ベースラインを越える拡張機能です。 管理ベースラインを拡張する方法を示す図と箇条書きについては、以下を参照してください。 この記事では、プラットフォームの特殊化のオプションについて説明します。

![クラウド管理ベースラインを越えるもの](../../_images/manage/beyond-the-baseline.png)

- **ワークロードの運用:** ワークロード操作の投資あたりの最大数。 最高レベルの回復性。 ビジネス価値を促進するワークロードの +/-20% に推奨されます。 通常は、高い重要度またはミッション クリティカルなワークロード用に予約されています。
- **プラットフォームの運用:** 運用の投資は多数のワークロードに分散されています。 回復性の向上は、定義されたプラットフォームを利用するすべてのワークロードに影響します。 最高の重要度のプラットフォームの +/-20% に推奨されます。 通常は、中程度から高い重要度のワークロード用に予約されています。
- **拡張された管理ベースライン:** 最小限の相対的な操作の投資。 追加のクラウドネイティブの運用ツールとプロセスを使用して、わずかに改善されたビジネス コミットメント。

ワークロードとプラットフォームの両方の操作で、設計とアーキテクチャの原則を変更する必要があります。 これらの変更には時間がかかる可能性があり、運用経費が増加する場合があります。 このような投資を必要とするワークロードの数を減らすために、管理ベースラインを強化すると、ビジネス コミットメントが十分に向上する可能性があります。

顧客の強化された管理ベースラインに共通する一般的なプロセス、ツール、および考えられる影響の概要を次の表に示します。

|Process  |ツール  |目的  |推奨される管理レベル  |
|---------|---------|---------|---------|
|システム設計を改善する|Azure Architecture Framework|プラットフォームのアーキテクチャ設計を改善して操作を向上する|
|自動修復|Azure Automation|プラットフォーム固有の自動化による高度なプラットフォーム データへの対応|プラットフォームの運用|
|サービス カタログ|マネージド アプリケーション センター|組織の標準を満たす承認済みソリューションのセルフサービス カタログを提供する|プラットフォームの運用|
|コンテナーのパフォーマンス|Azure Monitor for Containers|コンテナーの監視と診断|プラットフォームの運用|
|PaaS データのパフォーマンス|Azure の SQL 分析|PaaS DB の監視と診断|プラットフォームの運用|
|IaaS データのパフォーマンス|SQL Server の正常性チェック|IaaS DB の監視と診断|プラットフォームの運用|

## <a name="high-level-process"></a>プロセスの概要

プラットフォームの特殊化は、反復的なアプローチで次の 4 つのプロセスの規範的な実行で構成されます。 各プロセスの詳細については、この記事の以下のセクションで説明します。

- **システム設計を改善する:** 中断を効率的に最小化するために、共通システム (またはプラットフォーム) の設計を改善します。
- **修復を自動化する:** 一部の改善は費用効果が高くありません。 このような場合、修復を自動化し、中断の影響を軽減する方が適切な場合があります。
- **ソリューションをスケーリングする:** システムの設計と自動修復が改善されると、それらの変更をサービス カタログを介して環境全体に拡張できます。
- **継続的な改善:** さまざまな監視ツールを使用して、システム設計、自動化、スケーリングの次のパスで対処できる段階的な改善点を見つけることができます。

::: zone target="docs"

## <a name="improve-system-design"></a>システム設計を改善する

::: zone-end
::: zone target="chromeless"

## <a name="improve-system-designtabsystemsdesign"></a>[システム設計を改善する](#tab/SystemsDesign)

::: zone-end

システム設計の改善は、どの共通プラットフォームでも運用を改善する上で最も効果的なアプローチです。 システム設計の改善により、安定性が向上し、ビジネスの中断が減る可能性があります。 個々のシステムの設計は、クラウド導入フレームワークを介して実行される環境ビューの範囲外です。 このフレームワークを補完するために、Azure Architecture Framework には、特定のシステムの回復性と設計を向上させるためのベスト プラクティスが用意されています。 こうした設計の改善は、プラットフォームのシステム設計や特定のワークロードに適用できます。

Azure Architecture Framework は、システム設計の 5 つの柱全体の改善に焦点を当てています。

- **拡張性:** 負荷の増加に対応するために、共通プラットフォーム資産をスケーリングします。
- **可用性:** アップタイムの可能性を向上させることにより、ビジネスの中断を軽減します。
- **回復性:** 復旧時間を改善させ、中断期間を短縮します。
- **セキュリティ:** 外部の脅威からアプリケーションとデータを保護します。
- **管理:** これらの共通プラットフォーム資産に固有の運用プロセス。

ほとんどのビジネス中断は、何らかの形の技術的負債、またはアーキテクチャの欠陥に相当します。 既存のデプロイでは、システム設計の改善は、既存の技術的負債に対する支払いと見なすことができます。 新しいデプロイでは、システム設計の改善を技術的負債の回避と見なすことができます。 次のタブの "自動修復" では、対処できない、または対処するべきではない技術的負債に対処する方法について説明します。

システム設計を改善する詳細な方法については、[Azure Architecture Framework](https://docs.microsoft.com/azure/architecture/guide/pillars) に関する記事を参照してください。

システムの設計が向上したら、この記事に戻り、お使いの環境全体で改良を加え、スケーリングする新しい機会を見つけてください。

::: zone target="docs"

## <a name="automated-remediation"></a>自動修復

::: zone-end
::: zone target="chromeless"

## <a name="automated-remediationtabautomatedremediation"></a>[自動修復](#tab/AutomatedRemediation)

::: zone-end

一部の技術的負債には対処できません。 解決策のコストが高くなりすぎて修正できない可能性があります。 解決策を計画することもできますが、プロジェクトの期間は長くなります。 ビジネスの中断がビジネスに大きな影響を与えない可能性があります。また、ビジネスの優先事項が、回復性への投資ではなく、迅速に回復することである可能性があります。

技術的負債の解決が望ましい方法ではない場合、通常は、自動修復が必要な次の手順になります。 Azure Automation と Azure Monitor を使用して傾向を検出し、自動修復を提供することが、自動修復の最も一般的なアプローチです。

自動修復に関するガイダンスについては、[Azure Automation とアラートに関するこの記事](https://docs.microsoft.com/azure/automation/automation-create-alert-triggered-runbook)を参照してください。

::: zone target="docs"

## <a name="scale-the-solution-with-a-service-catalog"></a>サービス カタログを使用したソリューションのスケーリング

::: zone-end
::: zone target="chromeless"

## <a name="scale-the-solution-with-a-service-catalogtabservicecatalog"></a>[サービス カタログを使用したソリューションのスケーリング](#tab/ServiceCatalog)

::: zone-end

プラットフォームの特殊化とプラットフォームの運用の基礎は、管理の行き届いたサービス カタログです。 これは、システムの設計の改善と修復が環境全体でどのようにスケーリングされるかを示しています。 クラウド プラットフォーム チームおよびクラウド オートメーション チームは、任意の環境で最も一般的なプラットフォームに対して反復可能なソリューションを作成することに適しています。 ただし、これらのソリューションが常に利用されていない場合は、クラウド管理によってベースライン オファリング以外の機能が提供される可能性があります。

導入を最大化し、最適化されたプラットフォームのメンテナンス オーバーヘッドを最小限に抑えるには、Azure 内のサービス カタログにプラットフォームを追加する必要があります。 カタログ内の各アプリケーションは、サービス カタログを介した内部使用のため、または外部の消費者向けのマーケットプレース オファリングとしてデプロイすることができます。

サービス カタログに発行する手順については、[サービス カタログの発行](https://docs.microsoft.com/azure/managed-applications/publish-service-catalog-app)に関する記事を参照してください。

### <a name="deploy-applications-from-the-service-catalog"></a>サービス カタログからアプリケーションをデプロイする

1. Azure portal で、**マネージド アプリケーション センター (プレビュー)** を検索します。
2. ポータル ナビゲーションの **[参照]** セクションの下にある **[サービス カタログ アプリケーション]** をクリックします。
3. **[+ 追加]** をクリックして、会社のサービス カタログからアプリケーション定義を選択します。

サービスを提供しているすべてのマネージド アプリケーションがここに表示されます。

::: zone target="chromeless"

<!-- markdownlint-disable DOCSMD001 -->

::: form action="OpenBlade[#blade/Microsoft_Azure_Appliance/ManagedAppsHubBlade/browseServiceCatalog]" submitText="Go to Virtual Machines" :::

<!-- markdownlint-enable DOCSMD001 -->

::: zone-end

### <a name="manage-service-catalog-applications"></a>サービス カタログ アプリケーションを管理する

1. Azure portal で、**マネージド アプリケーション センター (プレビュー)** を検索します。
2. ポータル ナビゲーションの **[サービス]** セクションの下にある **[サービス カタログ アプリケーション]** をクリックします。

サービスを提供しているすべてのマネージド アプリケーションがここに表示されます。

::: zone target="chromeless"

::: form action="OpenBlade[#blade/Microsoft_Azure_Appliance/ManagedAppsHubBlade/serviceServiceCatalogApps]" submitText="Go to Virtual Machines" :::

::: zone-end

::: zone target="docs"

## <a name="continuous-improvement"></a>継続的な改善

::: zone-end
::: zone target="chromeless"

## <a name="continuous-improvementtabcontinuousimprovement"></a>[継続的な改善](#tab/ContinuousImprovement)

::: zone-end

プラットフォームの特殊化とプラットフォーム操作はどちらも、導入、プラットフォーム、自動化、および管理チーム間の強力なフィードバック ループに依存しています。 データ内のこうしたフィードバック ループを基礎とすることで、各チームがより賢明な意思決定を行えるようになります。 プラットフォーム操作で長期的なビジネス コミットメントを実現するには、一元化されたプラットフォームに固有の分析情報を利用することが重要です。 コンテナーと SQL Server は、最も一般的に使用されている 2 つのプラットフォームなので、継続的な改善データの収集を開始するために役立つ記事をいくつか次に示します。

[コンテナーのパフォーマンス](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-overview)
[PaaS データベースのパフォーマンス](https://docs.microsoft.com/azure/azure-monitor/insights/azure-sql)
[IaaS データベースのパフォーマンス](https://docs.microsoft.com/azure/azure-monitor/insights/sql-assessment)
