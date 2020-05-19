---
title: Azure 内での監視とレポート
description: Azure 向けのクラウド導入フレームワークを使用して、Azure の管理環境に用いる監視、レポート、アラートを設定する方法について学習します。
author: timleyden
ms.author: tileyden
ms.date: 04/09/2019
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.custom: fasttrack-edit, AQC, setup
ms.localizationpriority: high
ms.openlocfilehash: 0be49567a459915dbe4b8e8db90feae9b47a1e5c
ms.sourcegitcommit: 60d8b863d431b5d7c005f2f14488620b6c4c49be
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2020
ms.locfileid: "83223186"
---
<!-- cSpell:ignore timleyden tileyden -->

# <a name="monitoring-and-reporting-in-azure"></a>Azure 内での監視とレポート

Azure が提供する多数のサービスを組み合わせると、アプリケーションやそれらを支える Azure リソースからテレメトリを収集、分析し、それに基づいて対処するための包括的なソリューションが提供されます。 さらに、これらのサービスを拡張して、オンプレミスの重要なリソースを監視して、ハイブリッド監視環境を構築することもできます。

# <a name="azure-monitor"></a>[Azure Monitor](#tab/AzureMonitor)

Azure Monitor では、Azure 内のすべての監視データと診断データに対して 1 つの統合されたハブが提供されます。 これを使用してご利用のリソース全体を可視化することができます。 Azure Monitor を使用すれば、問題を見つけて修正し、パフォーマンスを最適化することができます。 顧客の行動を把握することもできます。

- **メトリックの監視と視覚化。** メトリックは、システムの正常性を把握するのに役立つ Azure リソースから取得できる数値です。 ダッシュボードのグラフをカスタマイズし、レポート用のブックを使用します。

- **ログのクエリと分析。** ログには、アクティビティ ログと Azure からの診断ログが含まれます。 クラウドまたはオンプレミスのリソースについては、他の監視と管理のソリューションから追加のログを収集します。 Log Analytics によって、このすべてのデータが集約される中央のリポジトリが提供されます。 そこから、問題のトラブルシューティングやデータの視覚化に役立つクエリを実行できます。

- **アラートとアクションの設定。** アラートでは、重大な状態が事前に通知されます。 メトリック、ログ、またはサービスの正常性の問題からのトリガーに基づいて是正措置を実行できます。 さまざまな通知とアクションをセットアップし、IT サービス マネジメント ツールにデータを送信できます。

::: zone target="docs"

 以下の監視を開始します。

- [アプリケーション](https://docs.microsoft.com/azure/application-insights/app-insights-overview)
- [Containers](https://docs.microsoft.com/azure/monitoring/monitoring-container-overview)
- [仮想マシン](https://docs.microsoft.com/azure/monitoring/monitoring-service-map)
- [ネットワーク](https://docs.microsoft.com/azure/networking/network-monitoring-overview)

その他のリソースを監視するには、Azure Marketplace で他のソリューションを探します。

Azure Monitor を調査するには、[Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/overview) に移動します。

## <a name="learn-more"></a>詳細情報

詳しくは、「[Azure Monitor のドキュメント](https://docs.microsoft.com/azure/monitoring-and-diagnostics)」をご覧ください。

::: zone-end

::: zone target="chromeless"

<!-- markdownlint-disable DOCSMD001 -->

## <a name="action"></a>アクション

::: form action="OpenBlade[#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/overview]" submitText="Explore Azure Monitor" :::

::: zone-end

# <a name="azure-service-health"></a>[Azure Service Health](#tab/AzureServiceHealth)

Azure Service Health では、ご利用中の Azure サービスとリージョンの正常性について、個々のお客様に応じたビューを提供します。 アクティブな懸案事項に関する情報が Service Health に投稿されるため、これによりリソースへの影響を理解できます。 通常の更新情報では、問題が解決されたときに随時情報が提供されます。

Service Health に計画メンテナンス イベントも公開しているため、リソースの可用性に影響を与える可能性のある変更も把握できます。 サービスの問題、計画メンテナンス、またはその他の変更がお使いの Azure サービスやリージョンに影響を及ぼす可能性がある場合に通知されるように Service Health アラートを設定してください。

Azure Service Health には以下が含まれます。

- **[Azure Status]\(Azure の状態):** Azure サービスの正常性のグローバルなビュー。
- **[サービス正常性]:** Azure サービスの正常性の個人用に設定されたビュー。
- **[リソース正常性]:** 個々のリソースの正常性のより深いビュー。

::: zone target="chromeless"

<!-- markdownlint-disable MD024 -->

## <a name="action"></a>アクション

サービス正常性アラートを設定するには:

1. **Service Health** に移動します。
2. **[正常性アラート]** を選択します。
3. サービス正常性アラートを作成します。

::: form action="OpenBlade[#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/healthalerts]" submitText="Go to Service Health" :::

::: zone-end

::: zone target="docs"

サービス正常性アラートを設定するには、[Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/healthalerts) に移動します。

## <a name="learn-more"></a>詳細情報

詳細については、[Azure Service Health のドキュメント](https://docs.microsoft.com/azure/service-health)を参照してください。

::: zone-end

# <a name="azure-advisor"></a>[Azure Advisor](#tab/AzureAdvisor)

Azure Advisor は、Azure デプロイのベスト プラクティスをフォローして実装するのに役立つ個人用に設定された無料のクラウド コンサルタントです。 リソースの構成と使用状況テレメトリが分析され、環境を最適化に役立つソリューションが推奨されます。 推奨事項は、次のカテゴリに分割されています。

- **高可用性:** ビジネスに不可欠なアプリケーションの継続性を向上させることができます。 推奨事項には、可用性セットへの仮想マシンの追加や geo 冗長エンドポイントの追加などがあります。
- **セキュリティ:** セキュリティ侵害に至る可能性がある脅威と脆弱性を検出します。 推奨事項には、ディスク暗号化の適用やネットワーク セキュリティ グループの有効化などがあります。
- **パフォーマンス:** アプリケーションの速度を向上させます。 推奨事項には、インデックスの作成またはトラフィック マネージャーの設定の再構成による SQL クエリのパフォーマンスの向上などが含まれます。
- **コスト:** Azure の全体的な支出を最適化し、削減します。 推奨事項には、使用頻度の低い仮想マシンのサイズ変更またはシャットダウンや、総保有コストを削減するための Azure Reservations への切り替えなどが含まれます。
- **オペレーショナル エクセレンス:** プロセスとワークフローの効率性と管理性を向上させます。 推奨事項には、Azure Policy ルールの設定と適用、無効なログ アラート ルールの修復、Azure Service Health アラートの構成などが含まれます。

Advisor での推奨事項は、デプロイするリソースと Azure 内で実行するアクションに基づいています。 Advisor で、最新の推奨事項を定期的に確認できます。

::: zone target="chromeless"

## <a name="action"></a>アクション

::: form action="OpenBlade[#blade/Microsoft_Azure_Expert/AdvisorBlade]" submitText="Explore Azure Advisor" :::

::: zone-end

::: zone target="docs"

Azure Advisor を調査するには、[Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Expert/AdvisorBlade) に移動します。

## <a name="learn-more"></a>詳細情報

詳しくは、「[Azure Advisor のドキュメント](https://docs.microsoft.com/azure/advisor)」をご覧ください。

::: zone-end

# <a name="azure-security-center"></a>[Azure Security Center](#tab/AzureSecurityCenter)

Azure Security Center も、監視戦略における重要な役割を果たします。 コンピューター、ネットワーク、ストレージ、データ サービス、およびアプリケーションのセキュリティを監視するために役立てることができます。 Security Center では、Azure リソースを対象とするアクティブな脅威の識別に役立つ機械学習と行動分析を使用して、高度な脅威検出が提供されます。 マルウェアやその他の不審なコードをブロックし、ブルート フォース攻撃やその他のネットワーク攻撃にさらされる領域を縮小する脅威の防止も提供されます。

Security Center では、脅威が識別された場合に、攻撃に対処するために必要な手順を含むセキュリティ アラートがトリガーされます。 また、検出された脅威についての情報を含んだレポートも提供されます。

Azure Security Center は Free と Standard という 2 つのレベルで提供されています。 セキュリティに関する推奨事項などの機能は無料でご利用いただけます。 Standard レベルでは、ハイブリッド クラウド ワークロード全体での高度な脅威の検出と防止などの追加の保護が提供されます。

::: zone target="chromeless"

## <a name="action"></a>アクション

**最初の 30 日間は無料でご利用いただける Standard レベルをお試しください。**

サブスクリプションのリソースのセキュリティ ポリシーを有効にし、設定した後は、 **[防止]** セクションでリソースのセキュリティ状態や問題を参照できます。 これらの問題の一覧は、 **[Recommendations]\(推奨事項\)** タイルでも確認できます。

::: form action="OpenBlade[#blade/Microsoft_Azure_Security/SecurityMenuBlade/SecurityMenuBlade/0]" submitText="Explore Azure Security Center" :::

::: zone-end

::: zone target="docs"

Azure Security Center について調べるには、[Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Security/SecurityMenuBlade/SecurityMenuBlade/0) に移動します。

## <a name="learn-more"></a>詳細情報

詳しくは、「[Azure Security Center のドキュメント](https://docs.microsoft.com/azure/security-center)」をご覧ください。

::: zone-end
