---
title: 各ワークロードを評価し、計画を調整する
description: Azure 向けのクラウド導入フレームワークを使用して、ご利用の環境の移行に対する適合性と検討すべき方法を評価します。
author: matticusau
ms.author: mlavery
ms.date: 02/25/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: migrate
ms.custom: fasttrack-new, AQC
ms.localizationpriority: high
ms.openlocfilehash: b80acbe78ad8d4ad1cc50346db5a16ba20dbb38a
ms.sourcegitcommit: d88c1cc3597a83ab075606d040ad659ac4b33324
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/15/2020
ms.locfileid: "84787879"
---
# <a name="assess-workloads-and-refine-plans"></a>ワークロードを評価して計画を調整する

このガイドのリソースは、各ワークロードを評価し、各ワークロードの移行に対する適合性についての前提条件を吟味し、移行オプションに関するアーキテクチャ上の決定を確定するのに役立ちます。

<!-- markdownlint-disable MD025 -->

# <a name="tools"></a>[ツール](#tab/Tools)

上記のリンクのガイダンスに従っていない場合は、情報に基づく移行に関する意思決定を行うために、データと評価ツールが必要になる可能性があります。 Azure Migrate は、評価**および** Azure への移行を行うためのネイティブ ツールです。 新しいサーバー移行プロジェクトを作成し、必要なデータを収集するには、次の手順を使用します (まだ行っていない場合)。

## <a name="azure-migrate"></a>Azure Migrate

Azure Migrate では、Azure への移行についてオンプレミスのインフラストラクチャ、アプリケーション、データが評価されます。 このサービスは以下のことを行います。

- オンプレミス資産の移行の適合性を評価します。
- パフォーマンスベースのサイズ設定を実行します。
- オンプレミスの資産を Azure で実行する場合のコスト見積もりを提供します。

リフト アンド シフトのアプローチを検討している場合、または移行の初期評価段階にある場合に、このサービスは適しています。 評価が完了したら、Azure Migrate を使用して移行を実行します。

![Azure Migrate の概要](./media/assess/azure-migrate-overview-1.png)

### <a name="create-a-new-server-migration-project"></a>新しいサーバー移行プロジェクトの作成

以下の手順を使用して、Azure Migrate を使用したサーバーの移行評価を開始します。

1. **[Azure Migrate]** を選択します。
1. **[概要]** で **[サーバーの評価と移行]** を選択します。
1. **[ツールの追加]** を選択します。
1. **[サーバーの検出、評価、移行]** で、 **[ツールの追加]** を選択します。
1. **[移行プロジェクト]** で、自分の Azure サブスクリプションを選択し、リソース グループがない場合は作成します。
1. **[プロジェクトの詳細]** で、プロジェクト名と、プロジェクトを作成したい地域を指定し、 **[次へ]** を選択します。
1. **[評価ツールの選択]** で、 **[今は評価ツールの追加をスキップします] > [次へ]** の順に選択します。
1. **[移行ツールの選択]** で、次を選択します: **[Azure Migrate: サーバーの移行] > [次へ]** 。
1. **[確認 + ツールの追加]** で設定を確認し、 **[Add tools]\(ツールの追加\)** を選択します。
1. ツールは、追加されると、**Azure Migrate プロジェクト > [サーバー] > [移行ツール]** に表示されます。

::: zone target="chromeless"

::: form action="OpenBlade[#blade/Microsoft_Azure_Migrate/AmhResourceMenuBlade/overview]" submitText="Assess and migrate servers" :::

::: zone-end

::: zone target="docs"

### <a name="learn-more"></a>詳細情報

- [Azure Migrate の概要](https://docs.microsoft.com/azure/migrate/migrate-services-overview)
- [物理サーバーまたは仮想化されたサーバーを Azure に移行する](https://docs.microsoft.com/azure/migrate/tutorial-migrate-physical-virtual-machines)
- [Azure portal での Azure Migrate](https://portal.azure.com/#blade/Microsoft_Azure_Migrate/AmhResourceMenuBlade/overview)

::: zone-end

## <a name="service-map"></a>サービス マップ

Service Map は自動的に Windows および Linux のシステム上のアプリケーション コンポーネントを検出し、サービス間の通信をマップします。 Service Map を使用すると、サーバーを重要なサービスを提供する相互接続されたシステムとして表示することができます。 Service Map は、TCP 接続アーキテクチャ全体におけるサーバー、プロセス、受信接続と送信接続の待機時間、ポートの間の接続を表示します。エージェントのインストール以外の構成は必要ありません。

Azure Migrate では、Service Map を使用して、環境全体でレポート機能と依存関係が強化されます。 この統合の詳細については、「[依存関係の視覚化](https://docs.microsoft.com/azure/migrate/concepts-dependency-visualization)」を参照してください。 Azure Migration サービスを使用する場合は、Service Map を構成してそのベネフィットを得るために追加の手順を行う必要はありません。 以下の説明は、Service Map を他の目的またはプロジェクトに使用したい場合の参考用に提供されています。

### <a name="enable-dependency-visualization-using-service-map"></a>Service Map を使用して依存関係の視覚化を有効にする

依存関係の視覚化を使用するには、分析するオンプレミスの各マシンにエージェントをダウンロードしてインストールします。

- 各コンピューターに [Microsoft Monitoring Agent](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows) をインストールする必要があります。
- 各コンピューターに [Microsoft Dependency Agent](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-hybrid-cloud#install-the-dependency-agent-on-windows) をインストールする必要があります。
- インターネットに接続されていないマシンがある場合、それらのマシンに Log Analytics ゲートウェイをダウンロードしてインストールする必要もあります。

<!-- markdownlint-disable MD024 -->

### <a name="learn-more"></a>詳細情報

- [Azure での Service Map ソリューションの使用](https://docs.microsoft.com/azure/azure-monitor/insights/service-map)
- [Azure Migrate と Service Map: 依存関係の視覚化](https://docs.microsoft.com/azure/migrate/concepts-dependency-visualization)

# <a name="challenge-assumptions"></a>[前提条件を吟味する](#tab/Challenge-Assumptions)

理想的な移行では、すべての資産 (インフラストラクチャ、アプリ、またはデータ) がクラウド プラットフォームと互換性があり、いつでも移行または最新化できる状態にあることでしょう。 実際には、すべてのワークロードをクラウドに移行する必要があるわけではありません。 すべての資産がクラウド プラットフォームと互換性があるわけではありません。 ワークロードをクラウドに移行する前に、各ワークロードとすべての依存資産 (インフラストラクチャ、アプリ、データ) を評価します。

[クラウド導入フレームワークのプランの方法論](../../plan/index.md)では、[増分型の合理化](../../digital-estate/rationalize.md#incremental-rationalization)と [10 のパワー](../../digital-estate/rationalize.md#release-planning)のアプローチを利用して移行の評価と計画を行うようにアドバイスされています。 このガイドには、[Azure Migrate を使用してデジタル資産を評価する](../../plan/contoso-migration-assessment.md)ための詳細なベスト プラクティスも含まれています。

上記のリンクでは、前提条件は許容されることが示唆され、移行の計画段階では多くの場合推奨されています。 しかし、ここでは行動を起こしてみましょう。 これらの前提条件は、クラウドに移行する前にワークロードごとに吟味する必要があります。

## <a name="two-steps-of-incremental-rationalization"></a>増分型の合理化の 2 つの手順

[増分型の合理化](../../digital-estate/rationalize.md#incremental-rationalization)を正しく実現するには、均等に重み付けされた 2 つの手順が必要です。 どちらの手順でも、データおよび環境の分析情報が必要です。 ただし、どちらのアプローチでも、移行作業を成功させるために必要な時間と詳細の粒度が尊重されます。

- [10 のパワーのリリース計画](../../digital-estate/rationalize.md#release-planning):最初の合理化とリリース計画では、[合理化の 5 R](../../digital-estate/5-rs-of-rationalization.md) のうち 1 つだけが評価で使用されます。 [クラウド導入戦略に関するドキュメント](https://archcenter.blob.core.windows.net/cdn/fusion/readiness/Microsoft-Cloud-Adoption-Framework-Strategy-and-Plan-Template.docx)で定義されている全体的な動機に最適な合理化オプションに基づいて、見積もりと計画を行います。

- **各ワークロードの詳細な評価:** 10 のパワーのリリース計画に関連する前提条件は、計画を策定するには十分受け入れ可能です。 ただし、移行前に評価されていない場合は、これらの同じ前提条件によって重大な問題が発生する可能性があります。

## <a name="challenge-assumptions-and-update-the-plan"></a>前提条件を吟味して計画を更新する

Azure Migrate または任意の評価ツールで評価データをよく確認します。 このデータにより、互換性の問題、修復要件、サイズの提案、およびその他の考慮事項に関する分析情報が得られます。

移行の前に、そのデータと、製品所有者、開発チーム、管理者などとの発見に関する討議内容を使用して、この特定のワークロードの移行の可能性を評価します。 この発見を使用して、このワークロードに関する核となる前提条件を吟味します。 検出内容によって移行または導入の計画が変更された場合は、それに応じて計画を更新します。

これらの前提を吟味する最初の手順は、[合理化の 5 R すべてのレビュー](../../digital-estate/rationalize.md)です。

    - 想定される合理化アプローチは、このワークロードに適していますか。 これは最適なアプローチですか。
    - [レプリケーションの物理的性質](../migration-considerations/migrate/replicate.md#replication-risks---physics-of-replication)のいずれかが、このワークロードの移行に影響しますか。
    - このワークロードでは、移行前に[修復アクティビティ](../migration-considerations/assess/evaluate.md)が必要ですか。

これらの種類の質問は、前提条件の吟味に役立ち、各ワークロードの最適なパスにつながります。

前提条件を検証するための、詳細な質問のリストと定義されたプロセスについては、[評価プロセスの改善の概要](../migration-considerations/assess/index.md)に関する記事を参照してください。

# <a name="scenarios-and-stakeholders"></a>[シナリオと利害関係者](#tab/Scenarios)

## <a name="scenarios"></a>シナリオ

このガイドでは、次のシナリオについて説明します。

- **レガシ ハードウェア:** サポートの終了または耐用期間の終了に近づいているレガシ ハードウェアへの依存関係を削除するために移行する。
- **容量の増加:** 現在のインフラストラクチャでは提供できない資産 (インフラストラクチャ、アプリ、およびデータ) 用の容量を増やす。
- **データセンターの最新化:** 確実にビジネスを最新の状態に保ち、競争力を維持するために、データセンターを拡張するか、クラウド テクノロジーを使用してデータセンターを最新化する。
- **アプリケーションまたはサービスの最新化:** クラウド ネイティブ機能を利用できるようにアプリケーションを更新する。 当初の目的がリホスト移行戦略であっても、アプリケーションまたはサービスのレビューのため、および潜在的な最新化のための計画を作成する機能は、すべての移行で一般的なプロセスです。

### <a name="organizational-alignment-and-stakeholders"></a>組織の配置および利害関係者

利害関係者の完全な一覧は、移行プロジェクトによって異なります。 利害関係者はプロジェクトの特定の段階でしか識別されないことが多いため、移行計画の開始時に利害関係者がすべてわかっているとは限らないものと想定することが最善です。 ただし、移行プロジェクトを開始する前に、組織のクラウド移行作業全体に関心を持つ財務、IT インフラストラクチャ、およびアプリケーション グループから主要なビジネス リーダーを特定します。

これらの主要な高レベルの利害関係者を中心に構成された中核的なクラウド戦略チームを設立すれば、クラウドの導入に向けて組織の準備を行い、クラウド移行作業全体を先導することができます。 このチームには、クラウド テクノロジと移行プロセスを理解した上で、移行のビジネス上の正当な理由を明らかにし、移行作業に最適なソリューションの概要を決定する役割があります。 また、特定のアプリケーションおよびビジネスの利害関係者を識別し、それらと連携して、移行を確実に成功させることができるように支援します。

クラウド移行作業のために組織を準備する方法の詳細については、クラウド導入フレームワークの「[初期の組織配置](../../plan/initial-org-alignment.md)」に関するガイドを参照してください。

# <a name="timelines"></a>[タイムライン](#tab/Timelines)

一般的に、このガイドで取り上げる移行シナリオは 1 か月から 6 か月で完了できるとお客様は理解します。

移行のタイムラインを評価する際は、以下について考慮します。

- **移行する資産 (インフラストラクチャ、アプリ、データ):** 資産の数と多様性。
- **スタッフの準備状況:** スタッフは新しい環境を管理する準備ができていますか、それともスタッフにはトレーニングが必要ですか?
- **資金調達:** 移行を完了するための適切な承認と予算がありますか?
- **変更管理:** 変更の実装および承認について、お客様のビジネスには特定の要件がありますか?
- **セグメントの規制:** セグメントまたは業界の規制を遵守する必要がありますか?

# <a name="cost-management"></a>[コスト管理](#tab/ManageCost)

環境を評価することにより、コスト分析の手順を含めるよい機会が得られます。 評価活動によって収集されたデータを使用すれば、コストを分析して予測できるはずです。 このコスト予測では、1 回限りのコスト (データのイングレスの増加など) に加えて、従量課金サービスのコストも考慮する必要があります。

移行中に、決定および実行アクティビティに影響を与える特定の要因:

- **デジタル資産のサイズ:** ご利用のデジタル資産のサイズを理解することは、移行を実行するために必要な決定事項およびリソースに直接影響します。
- **会計モデル:** 構造化資本費用モデルから流動的運用費用モデルへの移行。

::: zone target="docs"

次のリソースには、関連する情報が提供されています。

- [クラウド コストの見積り](../migration-considerations/assess/estimate.md)

::: zone-end
