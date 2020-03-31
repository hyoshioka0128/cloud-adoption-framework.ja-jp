---
title: Azure の管理ベースラインの改善
description: Azure 向けのクラウド導入フレームワークを使用して、管理ベースラインに対する一般的な改善について理解します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 10/17/2019
ms.topic: article
ms.service: cloud-adoption-framework
ms.subservice: operate
ms.custom: fasttrack-edit, AQC
ms.localizationpriority: high
ms.openlocfilehash: 223d6bf040987b7266d284d9175b588d08806c9f
ms.sourcegitcommit: ea63be7fa94a75335223bd84d065ad3ea1d54fdb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "80356518"
---
<!-- cSpell:ignore ITSMC -->

# <a name="enhanced-management-baseline-in-azure"></a>Azure の管理ベースラインの改善

最初の 3 つのクラウド管理規範では、管理ベースラインについて説明しています。 このガイドの前の記事では、管理ベースラインと呼ばれるクラウド管理サービスの MVP (実用最小限の製品: Minimum Viable Product) について説明しました。 この記事では、そのベースラインに対する一般的な改善をいくつか概説します。

管理ベースラインの目的は、サポート対象の "*すべて*" のワークロードに対して最小限のレベルのビジネス コミットメントが提供される一貫したオファリングを作成することです。 この共通の繰り返し可能な管理サービスのベースラインにより、チームは、逸脱を最小限に抑えて、高度に最適化された運用管理を実現できます。

ただし、ビジネスに対して標準的なオファリングを超えるコミットメントが必要な場合があります。 次の画像およびリストでは、管理ベースラインを超えることを行う 3 つの方法を示しています。

![クラウド管理ベースラインを越えるもの](../../_images/manage/beyond-the-baseline.png)

- **ワークロードの運用:**
  - ワークロードの運用あたりの最大の投資。
  - 最高レベルの回復性。
  - ビジネス価値を促進するワークロードの約 20% に推奨されます。
  - 通常は、高い重要度またはミッション クリティカルなワークロード用に予約されています。
- **プラットフォームの運用:**
  - 運用の投資は多数のワークロードに分散されています。
  - 回復性の向上は、定義されたプラットフォームを利用するすべてのワークロードに影響します。
  - 重要度が最も高いプラットフォームの約 20% に対して提案されます。
  - 通常は、中から高の重要度のワークロード用に予約されています。
- **改善された管理ベースライン:**
  - 最小限の相対的な操作の投資。
  - 追加のクラウドネイティブの運用ツールとプロセスを使用して、わずかに改善されたビジネス コミットメント。

ワークロードの運用とプラットフォームの運用の両方で、設計とアーキテクチャの原則を変更する必要があります。 これらの変更には時間がかかる可能性があり、運用経費が増加する場合があります。 このような投資を必要とするワークロードの数を削減するために、管理ベースラインを拡張することにより、ビジネス コミットメントを十分に高められる可能性があります。

次の表では、改善された顧客の管理ベースラインに共通するプロセス、ツール、および考えられる影響の概要を示します。

| 規範  | Process  | ツール | 潜在的な影響 | 詳細情報 |
|---|---|---|---|---|
|インベントリと可視性|サービスの変更の追跡|Azure Resource Graph|Azure のサービスに対する変更が視認しやすくなると、負の影響をより迅速に検出したり、より迅速に修復できる場合がある。|[Azure Resource Graph の概要](https://docs.microsoft.com/azure/governance/resource-graph/overview)|
|インベントリと可視性|IT サービスマネジメント (ITSM) の統合|IT Service Management Connector|ITSM に自動接続されることにより、認識が早くなる。|[IT Service Management Connector (ITSMC)](https://docs.microsoft.com/azure/azure-monitor/platform/itsmc-overview)|
|運用のコンプライアンス|操作の自動化|Azure Automation|変更により迅速および正確に対応するために運用のコンプライアンスを自動化する。|次のセクションを参照|
|運用のコンプライアンス|マルチクラウド操作|Azure Automation の Hybrid Runbook Worker|複数のクラウド間での操作を自動化する。|[Hybrid Runbook Worker の概要](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker)|
|運用のコンプライアンス|ゲストの自動化| Desired State Configuration (DSC)|エラーと構成のずれを減らすためのゲスト オペレーティング システムのコードベースの構成。|[DSC の概要](https://docs.microsoft.com/powershell/scripting/dsc/overview/overview)|
|保護と復旧|侵害通知|Azure Security Center|セキュリティ違反復旧トリガーを含むよう保護を改善。|次のセクションを参照|

::: zone target="docs"

## <a name="azure-automation"></a>Azure Automation

::: zone-end
::: zone target="chromeless"

## <a name="azure-automation"></a>[Azure Automation](#tab/AzureAutomation)

::: zone-end

Azure Automation は、制御の自動化を一元的に管理するためのシステムです。 Azure Automation では、環境メトリックに応じ、単純な修復、スケーリング、および最適化プロセスを実行できます。 これらのプロセスにより、手動によるインシデント処理に関連するオーバーヘッドが軽減されます。

最も重要なのは、ビジネス プロセスの中断を大幅に減らす、自動修復をほぼリアルタイムで配信できることです。 最も一般的なビジネスの中断を調査することで、自動化できる可能性がある環境内のアクティビティを識別できます。

### <a name="runbooks"></a>Runbooks

自動修復するコードの基本単位が Runbook です。 Runbook には、インシデントから修復または回復するための手順が含まれています。

Runbook を作成または管理するには:

1. [[Azure Automation]](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Automation%2FAutomationAccounts) に移動します。
1. **[Automation アカウント]** を選択し、一覧表示されているアカウントの 1 つを選択します。
1. **[プロセス オートメーション]** に移動します。
1. 表示されるオプションを使用して、Runbook、スケジュール、およびその他の自動修復機能を作成または管理できます。

::: zone target="chromeless"

<!-- markdownlint-disable DOCSMD001 -->

::: form action="OpenBlade[#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Automation%2FAutomationAccounts]" submitText="Assign Policy" :::

<!-- markdownlint-enable DOCSMD001 -->

::: zone-end
::: zone target="docs"

## <a name="azure-security-center"></a>Azure Security Center

::: zone-end
::: zone target="chromeless"

## <a name="azure-security-center"></a>[Azure Security Center](#tab/AzureSecurityCenter)

::: zone-end

Azure Security Center も、お使いの保護と復旧戦略において重要な役割を果たします。 コンピューター、ネットワーク、ストレージ、データ サービス、およびアプリケーションのセキュリティを監視するために役立てることができます。

Azure Security Center では、機械学習と行動分析を使用して、お使いの Azure リソースに対する現在の脅威を識別する高度な脅威検出を行います。 マルウェアやその他の不審なコードをブロックし、ブルート フォース攻撃やその他のネットワーク攻撃にさらされる領域を縮小する脅威の防止も提供されます。

Azure Security Center では、脅威が識別された場合に、攻撃に対処するために必要な手順を含むセキュリティ アラートがトリガーされます。 また、検出された脅威についての情報を含んだレポートも提供されます。

Azure Security Center は 2 つのレベルで提供されます。Free と Standard です。 セキュリティに関する推奨事項などの機能は Free レベルでご利用いただけます。 Standard レベルでは、ハイブリッド クラウド ワークロード全体での高度な脅威の検出と防止などの追加の保護が提供されます。

::: zone target="chromeless"

### <a name="action"></a>アクション

#### <a name="try-standard-tier-for-free-for-your-first-30-days"></a>最初の 30 日間は無料でご利用いただける Standard レベル

サブスクリプションのリソースに対しセキュリティ ポリシーを有効にし、構成した後は、 **[防止]** ペインでお使いのリソースのセキュリティ状態や問題を参照できます。 これらの問題の一覧は、 **[Recommendations]\(推奨事項\)** タイルでも確認できます。

::: form action="OpenBlade[#blade/Microsoft_Azure_Security/SecurityMenuBlade/SecurityMenuBlade/0]" submitText="Explore Azure Security Center" :::

::: zone-end

::: zone target="docs"

Azure Security Center について調べるには、[Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Security/SecurityMenuBlade/SecurityMenuBlade/0) に移動します。

### <a name="learn-more"></a>詳細情報

詳しくは、「[Azure Security Center のドキュメント](https://docs.microsoft.com/azure/security-center)」をご覧ください。

::: zone-end
