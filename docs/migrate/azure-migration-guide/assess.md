---
title: デジタル資産を評価する
titleSuffix: Microsoft Cloud Adoption Framework for Azure
description: デジタル資産を評価する
author: matticusau
ms.author: mlavery
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: migrate
ms.custom: fasttrack-new, AQC
ms.localizationpriority: high
ms.openlocfilehash: 78c82a2cb8793f8b5f0b90b1958fb4a83e3da935
ms.sourcegitcommit: d19e026d119fbe221a78b10225230da8b9666fe1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2019
ms.locfileid: "71224389"
---
# <a name="assess-the-digital-estate"></a>デジタル資産を評価する

理想的な移行では、すべての資産 (インフラストラクチャ、アプリ、またはデータ) がクラウド プラットフォームと互換性があり、いつでも移行できる状態にあることでしょう。 実際には、すべてをクラウドに移行する必要があるわけではありません。 さらに、すべての資産がクラウド プラットフォームと互換性があるわけでもありません。 ワークロードをクラウドに移行する前に、ワークロードと各関連資産 (インフラストラクチャ、アプリ、データ) を評価することが重要です。

このセクションのリソースは、ご利用の環境を評価して、移行に対するその適合性と、検討すべき方法を判断するのに役立ちます。

# <a name="toolstabtools"></a>[ツール](#tab/Tools)

以下のツールは、ご利用の環境を評価して、移行の適合性と使用すべき最善の方法を判断するのに役立ちます。 移行作業をサポートする適切なツールの選定に関する有益な情報については、[クラウド導入フレームワークの移行ツール決定ガイド](../../decision-guides/migrate-decision-guide/index.md)を参照してください。

## <a name="azure-migrate"></a>Azure Migrate

Azure Migrate サービスでは、Azure への移行についてオンプレミスのインフラストラクチャ、アプリケーション、データを評価します。 このサービスで、オンプレミスの資産の移行の適合性と、パフォーマンスに基づくサイズを評価して、オンプレミスの資産を Azure で実行するためのコストを見積もることができます。 リフト アンド シフトでの移行を検討している場合、または移行の初期評価段階にある場合に、このサービスは適しています。 評価が完了したら、Azure Migrate を利用して移行を実行できます。

![Azure Migrate の概要](./media/assess/azuremigrate-overview-1.png)

### <a name="create-a-new-server-migration-project"></a>新しいサーバー移行プロジェクトの作成

Azure Migrate を使用してサーバーの移行評価を開始するには、次の手順に従います。

1. **[Azure Migrate]** を選択します。
1. **[概要]** で **[サーバーの評価と移行]** をクリックします。
1. **[ツールの追加]** を選択します。
1. **[サーバーの検出、評価、移行]** で、 **[ツールの追加]** をクリックします。
1. **[移行プロジェクト]** で、自分の Azure サブスクリプションを選択し、リソース グループがない場合は作成します。
1. **[プロジェクトの詳細]** で、プロジェクト名と、プロジェクトを作成したい地域を指定し、 **[次へ]** をクリックします。
1. **[評価ツールの選択]** で、 **[今は評価ツールの追加をスキップします] > [次へ]** の順に選択します。
1. **[移行ツールの選択]** で、次を選択します: **[Azure Migrate: サーバーの移行] > [次へ]** 。
1. **[ツールの確認と追加]** で設定を確認し、 **[ツールの追加]** をクリックします
1. ツールは、追加されると、**Azure Migrate プロジェクト > [サーバー] > [移行ツール]** に表示されます。

::: zone target="chromeless"

::: form action="Blade[#blade/Microsoft_Azure_Migrate/AmhResourceMenuBlade/overview]" submitText="Assess and migrate servers" :::

::: zone-end

::: zone target="docs"

### <a name="read-more"></a>詳細情報

- [Azure Migrate の概要](https://docs.microsoft.com/azure/migrate/migrate-services-overview)
- [物理サーバーまたは仮想化されたサーバーを Azure に移行する](https://docs.microsoft.com/azure/migrate/tutorial-migrate-physical-virtual-machines)
- [Azure portal での Azure Migrate](https://portal.azure.com/#blade/Microsoft_Azure_Migrate/AmhResourceMenuBlade/overview)

::: zone-end

## <a name="service-map"></a>サービス マップ

サービス マップは、Windows および Linux システムのアプリケーション コンポーネントを自動的に検出し、サービス間の通信をマップします。 Service Map を使用すると、サーバーを重要なサービスを提供する相互接続されたシステムとして表示することができます。 Service Map は、TCP 接続アーキテクチャ全体におけるサーバー、プロセス、受信接続と送信接続の待機時間、ポートの間の接続を表示します。エージェントのインストール以外の構成は必要ありません。

Azure Migrate では、Service Map を使用して、環境全体でレポート機能と依存関係が強化されます。 この統合の詳細については、「[依存関係の視覚化](https://docs.microsoft.com/azure/migrate/concepts-dependency-visualization)」を参照してください。 Azure Migration サービスを使用する場合は、Service Map を構成してその利点を得るために余計な手順を行う必要はありません。 以下の説明は、Service Map を他の目的またはプロジェクトに使用したい場合の参考用に提供されています。

### <a name="enable-dependency-visualization-using-service-map"></a>Service Map を使用して依存関係の視覚化を有効にする

依存関係の視覚化を使用するには、分析するオンプレミスの各マシンにエージェントをダウンロードしてインストールする必要があります。

- 各コンピューターに [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows) をインストールする必要があります。
- 各マシンに [Microsoft Dependency Agent](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-hybrid-cloud#install-the-dependency-agent-on-windows) をインストールする必要があります。
- また、インターネットに接続されていないマシンの場合、それらに Log Analytics ゲートウェイをダウンロードしてインストールする必要があります。

<!-- markdownlint-disable MD024 -->

### <a name="read-more"></a>詳細情報

- [Azure での Service Map ソリューションの使用](https://docs.microsoft.com/azure/azure-monitor/insights/service-map)
- [Azure Migrate と Service Map: 依存関係の視覚化](https://docs.microsoft.com/azure/migrate/concepts-dependency-visualization)

# <a name="scenarios-and-stakeholderstabscenarios"></a>[シナリオと利害関係者](#tab/Scenarios)

## <a name="scenarios"></a>シナリオ

このガイドでは、次のシナリオに重点を置いています。

- **レガシ ハードウェア:** サポートの終了または有効期間の終了に近づいているレガシ ハードウェアへの依存関係を削除するために移行しています。
- **容量の増加:** 現在のインフラストラクチャでは提供できない資産 (インフラストラクチャ、アプリ、およびデータ) 用の容量を増やす必要があります。
- **データセンターの最新化:** 確実にビジネスを最新の状態に保ち、競争力を維持するには、ご利用のデータセンターを拡張するか、クラウド テクノロジーを使用してご利用のデータセンターを最新化する必要があります。
- **アプリケーションまたはサービスの最新化:** クラウド ネイティブ機能を利用できるようにご利用のアプリケーションを更新したいと考えています。 リホスト移行戦略が最初の目的であっても、アプリケーションまたはサービスのレビューのため、および潜在的な最新化のための計画を作成する機能は、すべての移行で一般的なプロセスです。

### <a name="organizational-alignment-and-stakeholders"></a>組織の配置および利害関係者

利害関係者の完全な一覧は、移行プロジェクトによって異なります。 利害関係者はプロジェクトの特定の段階でしか識別されないことが多いため、移行計画の開始時に利害関係者がすべてわかっているとは限らないものと想定することをお勧めします。 ただし、移行プロジェクトを開始する前に、組織のクラウド移行作業全体に関心を持つ財務、IT インフラストラクチャ、およびアプリケーション グループから主要なビジネス リーダーを特定することができます。

これらの主要な高レベルの利害関係者を中心に構成された中核的なクラウド戦略チームを設立すれば、クラウドの導入に向けて組織の準備を行い、クラウド移行作業全体を先導することができます。 このチームには、クラウド テクノロジと移行プロセスを理解した上で、移行のビジネス上の正当な理由を明らかにし、移行作業に最適なソリューションの概要を決定する役割があります。 また、特定のアプリケーションおよびビジネスの利害関係者を識別し、それらと連携して、移行を確実に成功させることができるように支援します。

クラウド移行作業のために組織を準備する方法の詳細については、「[initial organization alignment](../../ready/initial-org-alignment.md)」 (初期の組織の調整) にある Cloud Adoption Framework に関する記事を参照してください。

# <a name="timelinestabtimelines"></a>[タイムライン](#tab/Timelines)

一般的な状況として、このガイドで取り上げる移行シナリオは 1 か月から 6 か月で完了できるとお客様は理解しています。

移行のタイムラインを評価する際に考慮すべき要素の一部を次に示します。

- **移行する資産 (インフラストラクチャ、アプリ、データ):** 資産の数と多様性。
- **スタッフの準備状況:** スタッフは新しい環境を管理する準備ができていますか、それともスタッフにはトレーニングが必要ですか?
- **資金調達:** 移行を完了するための適切な承認と予算がありますか?
- **変更管理:** 変更の実装および承認について、お客様のビジネスには特定の要件がありますか?
- **セグメントの規制:** セグメントまたは業界の規制を遵守する必要がありますか?

# <a name="cost-managementtabmanagecost"></a>[コスト管理](#tab/ManageCost)

ご利用の環境を評価する場合は、コスト分析手順を含める絶好の機会が得られます。 評価活動によって収集されたデータを使用して、コストを分析し予測できることが必要になります。 このコスト予測では、1 回限りのコスト (データのイングレスの増加など) に加えて、従量課金サービスのコストも考慮する必要があります。

移行中に、決定および実行アクティビティに影響を与える特定の要因:

- **デジタル資産のサイズ:** ご利用のデジタル資産のサイズを理解することは、移行を実行するために必要な決定事項およびリソースに直接影響します。
- **会計モデル:** 構造化資本費用モデルから流動的運用費用モデルへの移行。

::: zone target="docs"

次のリソースには、関連する情報が提供されています。

- [クラウド コストの見積もり](../migration-considerations/assess/estimate.md)

::: zone-end
