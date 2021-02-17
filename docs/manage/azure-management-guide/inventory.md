---
title: Azure のインベントリと可視性
description: 運用データを収集するためにインベントリとその実行状態の可視性を提供するツールについて説明します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 10/17/2019
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: operate
ms.localizationpriority: high
ms.custom: internal, fasttrack-edit, AQC
ms.openlocfilehash: 1c5f49ff4ea88826b5e2022e7a5a85ffcb65b71f
ms.sourcegitcommit: 042fb295ef5623d45066ce38a389dd8d636cbc20
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/14/2021
ms.locfileid: "100492342"
---
# <a name="inventory-and-visibility-in-azure"></a>Azure のインベントリと可視性

_インベントリと可視性_ は、クラウド管理ベースラインの 3 つの原則の 1 つ目です。

![クラウド管理ベースライン](../../_images/manage/management-baseline.png)

運用について判断を下す場合に、適切な運用データを収集することが不可欠であるため、この規範が最初の項目になります。 クラウド管理チームは、管理対象とそれらの資産の運用状況を把握する必要があります。 この記事では、インベントリとインベントリの実行状態の可視性の両方を提供するさまざまなツールについて説明します。

すべてのエンタープライズグレード環境について、管理ベースラインの推奨最小値の概要を次の表に示します。

| Process | ツール | 目的 |
|---|---|---|
| Azure サービスの正常性の監視 | [Azure Service Health](/azure/service-health/service-health-overview) | Azure で実行されているサービスの正常性、パフォーマンス、診断 |
| ログの一元化 | [Log Analytics](/azure-monitor/log-query/log-analytics-overview) | すべての可視性のための一元的なログ |
| 監視の一元化 | [Azure Monitor](/azure/azure-monitor/overview) | 運用データと傾向の一元的な監視 |
| 仮想マシンのインベントリと変更の追跡 | [変更履歴とインベントリ](/azure/automation/change-tracking/overview) | ゲスト OS レベルの VM のインベントリと変更の監視 |
| サブスクリプションの監視 | [Azure アクティビティ ログ](/azure/azure-monitor/platform/activity-log) | サブスクリプション レベルの変更の監視 |
| ゲスト OS の監視 | [Azure Monitor for VMs](/azure/azure-monitor/insights/vminsights-overview) | VM の変更とパフォーマンスの監視 |
| ネットワーク監視 | [Azure Network Watcher](/azure/network-watcher/network-watcher-monitoring-overview) | ネットワークの変更とパフォーマンスの監視 |
| DNS の監視 | [DNS Analytics](/azure/azure-monitor/insights/dns-analytics) | DNS のセキュリティ、パフォーマンス、操作 |

::: zone target="docs"

## <a name="azure-service-health"></a>Azure Service Health

::: zone-end
::: zone target="chromeless"

## <a name="azure-service-health"></a>[Azure Service Health](#tab/AzureServiceHealth)

::: zone-end

Azure Service Health では、ご利用中の Azure サービスとリージョンの正常性について、個々のお客様に応じたビューを提供します。 アクティブな問題に関する情報が Azure Service Health に投稿され、リソースへの影響を把握できます。 通常の更新情報では、問題が解決されたときに随時情報が提供されます。

また、Azure Service Health には計画メンテナンス イベントも公開しているため、リソースの可用性に影響を与える可能性のある変更も把握できます。 サービスの問題、計画メンテナンス、またはその他の変更がお使いの Azure サービスやリージョンに影響を及ぼす可能性がある場合に通知されるように Service Health アラートを設定してください。

Azure Service Health には以下が含まれます。

- **[Azure Status]\(Azure の状態):** Azure サービスの正常性のグローバルなビュー。
- **[サービス正常性]:** Azure サービスの正常性の個人用に設定されたビュー。
- **[リソース正常性]:** 個々のリソースの正常性のより深いビュー。

::: zone target="chromeless"

<!-- markdownlint-disable MD024 -->

### <a name="action"></a>アクション

サービス正常性アラートを設定するには:

1. **Service Health** に移動します。
2. **[正常性アラート]** を選択します。
3. Service Health アラートを作成します。

<!-- markdownlint-disable DOCSMD001 -->

::: form action="OpenBlade[#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/healthalerts]" submitText="Go to Service Health" :::

<!-- markdownlint-enable DOCSMD001 -->

::: zone-end

::: zone target="docs"

サービス正常性アラートを設定するには、[Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/healthalerts) に移動します。

### <a name="learn-more"></a>詳細情報

詳細については、「[Azure Service Health](/azure/service-health)」を参照してください。

## <a name="log-analytics"></a>Log Analytics

::: zone-end
::: zone target="chromeless"

## <a name="log-analytics"></a>[Log Analytics](#tab/Log-Analytics)

::: zone-end

[Log Analytics ワークスペース](/azure/azure-monitor/learn/quick-create-workspace)は、Azure Monitor ログ データの格納用の一意の環境です。 各ワークスペースには、独自のデータ リポジトリと構成があります。 データ ソースとソリューションは、特定のワークスペースにデータを格納するように構成されます。 Azure 監視ソリューションでは、すべてのサーバーをワークスペースに接続することで、それらのログ データを格納してアクセスできるようにする必要があります。

::: zone target="chromeless"

### <a name="action"></a>アクション

<!-- markdownlint-disable DOCSMD001 -->

::: form action="OpenBlade[#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.OperationalInsights%2FWorkspaces]" submitText="Explore Azure Monitor" :::

<!-- markdownlint-enable DOCSMD001 -->

::: zone-end

::: zone target="docs"

### <a name="learn-more"></a>詳細情報

詳細については、[Log Analytics ワークスペースの作成に関するドキュメント](/azure/azure-monitor/learn/quick-create-workspace)を参照してください。

## <a name="azure-monitor"></a>Azure Monitor

::: zone-end
::: zone target="chromeless"

## <a name="azure-monitor"></a>[Azure Monitor](#tab/Azure-Monitor)

::: zone-end

Azure Monitor では、Azure 内のすべての監視データと診断データに対して 1 つの統合されたハブが提供され、リソース全体が可視化されます。 Azure Monitor を使用すれば、問題を見つけて修正し、パフォーマンスを最適化することができます。 顧客の行動を把握することもできます。

- **メトリックの監視と視覚化。** メトリックは、Azure リソースから取得できる数値です。 これらはシステムの正常性を理解するのに役立ちます。 ダッシュボードのグラフをカスタマイズし、レポート用のブックを使用します。

- **ログのクエリと分析。** ログには、アクティビティ ログと Azure からの診断ログが含まれます。 クラウドまたはオンプレミスのリソースについては、他の監視と管理のソリューションから追加のログを収集します。 Log Analytics によって、このすべてのデータが集約される中央のリポジトリが提供されます。 そこから、問題のトラブルシューティングやデータの視覚化に役立つクエリを実行できます。

- **アラートとアクションの設定。** アラートでは、重大な状態が通知されます。 メトリック、ログ、またはサービスの正常性の問題からのトリガーに基づいて是正措置を実行できます。 さまざまな通知とアクションをセットアップし、IT サービスマネジメント ツールにデータを送信することもできます。

::: zone target="chromeless"

### <a name="action"></a>アクション

<!-- markdownlint-disable DOCSMD001 -->

::: form action="OpenBlade[#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/overview]" submitText="Explore Azure Monitor" :::

<!-- markdownlint-enable DOCSMD001 -->

::: zone-end

::: zone target="docs"

 以下の監視を開始します。

- [アプリケーション](/azure/application-insights/app-insights-overview)
- [Containers](/azure/monitoring/monitoring-container-overview)
- [仮想マシン](/azure/monitoring/monitoring-service-map)
- [ネットワーク](/azure/networking/network-monitoring-overview)

その他のリソースを監視するには、Azure Marketplace で他のソリューションを探します。

Azure Monitor を調査するには、[Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/overview) に移動します。

### <a name="learn-more"></a>詳細情報

詳しくは、「[Azure Monitor のドキュメント](/azure/monitoring-and-diagnostics)」をご覧ください。

## <a name="onboard-solutions"></a>ソリューションのオンボード

::: zone-end
::: zone target="chromeless"

## <a name="onboard-solutions"></a>[ソリューションのオンボード](#tab/Configure-solutions)

::: zone-end

ソリューションを有効にするには、Log Analytics ワークスペースを構成する必要があります。 オンボードされた Azure VM とオンプレミスのサーバーでは、接続先の Log Analytics ワークスペースからソリューションが取得されます。

オンボードには、次の 2 つの方法があります。

- [単一の VM](../../manage/azure-server-management/onboard-single-vm.md)
- [サブスクリプション全体](../../manage/azure-server-management/onboard-at-scale.md)

各記事では、次のソリューションをオンボードするための一連の手順について説明します。

- Update Management ソリューション
- 変更履歴とインベントリ ソリューション
- Azure activity log
- Azure Log Analytics の Agent Health
- マルウェア対策評価
- VM に対する Azure Monitor
- Azure Security Center

上記の各ステップは、インベントリと可視性を確立するために役立ちます。
