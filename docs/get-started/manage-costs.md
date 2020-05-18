---
title: 作業を開始しましょう。クラウド コストを管理する
description: クラウドの導入に伴うコストの管理について基本的な事項を説明します。
author: JanetCThomas
ms.author: janet
ms.date: 04/04/2020
ms.topic: guide
ms.service: cloud-adoption-framework
ms.subservice: overview
ms.openlocfilehash: 22956507d801163b2ee75f074f48bbd955546c11
ms.sourcegitcommit: 60d8b863d431b5d7c005f2f14488620b6c4c49be
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2020
ms.locfileid: "83230527"
---
<!-- docsTest:ignore Microsoft-Cloud-Adoption-Framework-Strategy-and-Plan-Template -->

# <a name="get-started-manage-cloud-costs"></a>作業を開始しましょう。クラウド コストを管理する

クラウド ガバナンスの中核となる規範の 1 つは、コスト管理です。 コスト管理の規範では、予算の確定、コスト割り当てパターンの監視、IT ポートフォリオ全体でのクラウド支出行動を改善するための制御の実装に重点を置きます。 ただし、エンタープライズ コストの最適化では、コストを最小限に抑え、スケーリング、パフォーマンス、セキュリティ、信頼性に対する要求のバランスを取るために、他にも多くの役割や機能が関係します。 この記事では、関係する各チーム間の調整に役立つように、これらのさまざまなサポート機能の位置づけを入門ガイドとしてまとめています。

## <a name="get-started"></a>はじめに

どんな大規模な企業でも、コストの最適化の基盤となるのはガバナンスです。 次のセクションでは、ガバナンスの一環としてのコストの最適化についてガイダンスの概要を示します。 以下の手順は、各チームが、コストの最適化においてその役割を目的とするアクションを開始するのに役立ちます。 これらの手順を組み合わせることで、組織全体が一致団結して、コストの最適化への取り組みを開始することができます。

![エンタープライズ コストの管理の概要](../_images/get-started/cost-map.png)

## <a name="step-1-enterprise-cost-optimization"></a>手順 1:エンタープライズ コストの最適化

クラウド ガバナンス チームでは、支出とパフォーマンスの監視、リソースのサイズ変更と支出の削減、未使用リソースの安全な終了を組み合わせることで、過剰な支出や予定外の支出を評価し、それに対処するための十分な準備を行います。 エンタープライズ コストの最適化は、環境レベルでのコストに関する懸念に賢明に対処するために必要なツール、プロセス、依存関係についてチームで共通の認識を持つことから始まります。

**成果物:**

- 賢明なコスト管理の変更を企業全体で実装します。
- [コスト管理ポリシー テンプレート](../govern/cost-management/template.md)で、コスト管理のポリシー、プロセス、設計ガイダンスを文書化します。

この手順の成果物は、次のような定期的な作業の結果です。

- クラウド戦略チームとの戦略的連携を確保します (これには、ポートフォリオ全体のワークロード関係者が含まれます)。
- 環境全体でコストを最適化します。
  - 未使用の VM をシャットダウンまたは自動シャットダウンします。
  - 停止した VM を削除または割り当て解除します。
  - リソースのサイズが適切であることを確認します。
  - 実際の支出を予算上の想定範囲内に収めます。
- Azure Architecture Review を使用してアーキテクチャの変更を検証し、ワークロードの技術所有者との対話を促進します。

**成果物の完成をサポートするためのガイダンス:**

- すべてのワークロードとリソースが[適切な名前付け規則とタグ付け規則](../ready/azure-best-practices/naming-and-tagging.md)に従っていることを確認し、"コスト センター" と "技術所有者" のタグに特に重点を置いて、[Azure Policy を使用してタグ付け規則を適用](https://docs.microsoft.com/azure/governance/policy/tutorials/govern-tags)します。
- 企業全体での分析と改善の指針となる[コスト管理のベスト プラクティス](../govern/cost-management/best-practices.md)を定期的に確認して適用します。 この記事の中で最も影響力のあるガバナンス プラクティスの一部を次に示します。

  - [一般コストのベスト プラクティス](../govern/cost-management/best-practices.md)に従って、サイズ変更とコストを削減し、未使用のマシンを停止します。
  - [ハイブリッド使用特典](../govern/cost-management/best-practices.md#best-practice-take-advantage-of-azure-hybrid-benefit)を適用して、ライセンス コストを削減します。
  - [予約インスタンス](../govern/cost-management/best-practices.md#best-practice-use-reserved-vm-instances)を調整して、リソース コストを削減します。
  - [リソース使用率を監視](../govern/cost-management/best-practices.md#best-practice-monitor-resource-utilization)して、リソースのパフォーマンスに対する影響を最小限に抑えます。
  - 非運用環境を管理するポリシーを使用して、[非運用コストを削減](../govern/cost-management/best-practices.md#best-practice-reduce-nonproduction-costs)します。
  - [コストの最適化に関する推奨事項](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-acm-opt-recommendations?toc=/azure/cloud-adoption-framework/toc.json&bc=/azure/cloud-adoption-framework/_bread/toc.json)に従います。
- コスト最適化のための効果的な変更を実装するには、ワークロード レベルでのトレードオフが必要になる場合があります。 [ Azure Architecture Framework](https://docs.microsoft.com/azure/architecture/framework/cost/tradeoffs) と [Azure Architecture Review](https://docs.microsoft.com/assessments/?id=azure-architecture-review) は、特定のワークロードの技術所有者との対話を導くのに役立ちます。
- クラウド ガバナンスに不慣れな場合は、ガバナンスの方法論を使用して、[ガバナンスのポリシー、プロセス、規範](../govern/index.md)を確立してください。
- コスト管理の規範に不慣れな場合は、[コスト管理の改善に関する記事](../govern/guides/complex/cost-management-improvement.md)に従って検討してください。特に、[実装](../govern/guides/complex/cost-management-improvement.md#incremental-improvement-of-the-best-practices)に関するセクションを重点的にお読みください。

<!-- markdownlint-disable MD033 -->
<br>

| 説明責任チーム | 実行責任チームとサポート チーム |
| --- | --- |
| <li> クラウド ガバナンス チーム | <li> クラウド戦略チーム <li> クラウド導入チーム <li> クラウドのセンター オブ エクセレンスまたは中央 IT |

## <a name="steps-to-scale-cost-optimization"></a>コスト最適化の規模を拡大するための手順

ほとんどの企業のガバナンス チームは、大幅なコスト最適化を検出して推進することができます。 データ主導の基本的なリソース サイズ変更は、コストに対してすぐに目に見える影響をもたらす可能性があります。

ただし、「[コスト志向の組織を構築する](../organize/cost-conscious-organization.md)」で説明しているように、企業全体でコスト管理とコストの最適化に重点を置くことにより、はるかに高い価値を実現することができます。 以下の手順では、さまざまなチームがコスト志向の組織を構築するために役立つ方法を示します。

## <a name="step-2-define-strategy"></a>手順 2:戦略を定義する

戦略的な決定は、コスト管理に直接影響を及ぼし、その影響は導入ライフサイクル全体と長期的な運用に波及します。 戦略を明確にすることは、ガバナンス チームが推進するコスト最適化の取り組みを向上させます。

**成果物:**

- [戦略と計画のテンプレート](https://archcenter.blob.core.windows.net/cdn/fusion/readiness/Microsoft-Cloud-Adoption-Framework-Strategy-and-Plan-Template.docx)に、動機、成果、業務上の正当な理由を記録します。 Azure Cost Management を使用して最初の予算を作成します。

**成果物の完成をサポートするためのガイダンス:**

- [動機を理解する](../strategy/motivations.md)。 重要なビジネス イベントや一部の移行の動機は、コスト重視の傾向があり、その後のすべての取り組みに向けたコスト管理の重要性を高めます。 移行によるイノベーションまたは成長に関連した、他の先進的な動機では、営業収益がより重視される可能性があります。 動機を認識することは、コスト管理をどの程度優先する必要があるかを理解するのに役立ちます。
- [ビジネス成果](../strategy/business-outcomes/index.md)。 一部の財政上の成果では、コストが極端に重視される傾向があります。 目的の結果を財務上のメトリックに対応付ける場合、極力早い段階でガバナンスのコスト管理の規範に投資する必要があります。
- [業務上の正当な理由](../strategy/cloud-migration-business-case.md)。 業務上の正当な理由は、クラウド導入の財務計画全体の概要を示します。 これは、最初の予算作成に役立つ情報源です。

<!-- markdownlint-disable MD033 -->
<br>

| 説明責任チーム | 実行責任チームとサポート チーム |
| --- | --- |
| <li> クラウド戦略チーム | <li> クラウド ガバナンス チーム <li> クラウド導入チーム <li> クラウドのセンター オブ エクセレンスまたは中央 IT |

## <a name="step-3-develop-a-cloud-adoption-plan"></a>手順 3:クラウド導入計画を作成する

導入計画により、導入時のアクティビティのタイムラインが明確になります。 計画とデジタル資産分析を調整することで、毎月の支出の増加を予測できます。 また、クラウド ガバナンス チームがプロセスを調整し、支出パターンを特定するのにも役立ちます。

**成果物:**

- [クラウド導入計画](../plan/plan-intro.md#build-your-cloud-adoption-plan)の作成手順の 1 から 6 を完了します。
- クラウド ガバナンス チームと協力して、予算を調整し、現実的な支出予測を作成します。

**成果物の完成をサポートするためのガイダンス:**

- [インベントリの収集](../digital-estate/inventory.md)。 導入前に、デジタル資産を分析するためのデータ ソースを確立します。
- [ベスト プラクティス:Azure Migrate](../plan/contoso-migration-assessment.md)。 Azure Migrate を使用してインベントリを収集します。
- [増分型の合理化](../digital-estate/rationalize.md#incremental-rationalization)。 段階的な合理化では、予算を作成するために定量分析でクラウドの候補を特定します。
- [コスト モデルと予測モデルの調整](../digital-estate/calculate.md)。 Azure Cost Management を使用して、[予算を作成](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-acm-create-budgets?toc=/azure/cloud-adoption-framework/toc.json&bc=/azure/cloud-adoption-framework/_bread/toc.json)することにより、コスト モデルと予測モデルを調整します。
- [クラウド導入計画の作成](../plan/plan-intro.md#build-your-cloud-adoption-plan)。 実用的なワークロード、資産、タイムラインの詳細を含む計画を作成します。 この計画は、一定期間にわたる支出 (またはコスト予測) の基礎となります。 "_一定期間にわたる支出_" は、ガバナンスのコスト管理の規範内で、すべての実施可能な最適化分析のための最初のベースラインになります。

<!-- markdownlint-disable MD033 -->
<br>

| 説明責任チーム | 実行責任チームとサポート チーム |
| --- | --- |
| <li> クラウド導入チーム | <li> クラウド戦略チーム <li> クラウド ガバナンス チーム <li> クラウドのセンター オブ エクセレンスまたは中央 IT |

## <a name="step-4-implement-landing-zone-best-practices"></a>手順 4:ランディング ゾーンのベスト プラクティスを実装する

クラウド導入フレームワークの準備方法では、クラウドでワークロードをホストするランディング ゾーンの開発に重点を置いています。 ランディング ゾーンの実装時には、コストの最適化に関するさまざまな決定事項について検討する必要があります。

**成果物:**

- 短期的な導入計画で、ワークロードをホストできる 1 つ以上のランディング ゾーンをデプロイします。
- すべてのランディング ゾーンが、コスト最適化に関する決定とコスト管理の要件を満たしていることを確認します。

**成果物の完成をサポートするためのガイダンス:**

- [コストの追跡](../ready/azure-best-practices/track-costs.md#provide-the-right-level-of-cost-access)。 適切に管理された環境階層を確立し、適切なレベルのコスト アクセスを提供し、各ランディング ゾーンで追加のコスト管理リソースを使用します。
- [クラウドへの投資の最適化](https://docs.microsoft.com/azure/cost-management-billing/costs/cost-mgt-best-practices?toc=/azure/cloud-adoption-framework/toc.json&bc=/azure/cloud-adoption-framework/_bread/toc.json)。 投資を最適化するためのベスト プラクティスを理解します。
- [予算の作成と管理](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-acm-create-budgets?toc=/azure/cloud-adoption-framework/toc.json&bc=/azure/cloud-adoption-framework/_bread/toc.json)。 予算を作成し管理するためのベスト プラクティスを理解します。
- [推奨事項に従ったコストの最適化](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-acm-opt-recommendations?toc=/azure/cloud-adoption-framework/toc.json&bc=/azure/cloud-adoption-framework/_bread/toc.json)。 コストを最適化する推奨事項を使用するためのベスト プラクティスを理解します。
- [使用状況と支出の監視](https://docs.microsoft.com/azure/cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending?toc=/azure/cloud-adoption-framework/toc.json&bc=/azure/cloud-adoption-framework/_bread/toc.json)。 ランディング ゾーン内の使用状況と支出を監視するためのベストプラクティスを理解します。

<!-- markdownlint-disable MD033 -->
<br>

| 説明責任チーム | 実行責任チームとサポート チーム |
| --- | --- |
| <li> クラウド導入チーム | <li> クラウド戦略チーム <li> クラウド ガバナンス チーム <li> クラウドのセンター オブ エクセレンスまたは中央 IT |

## <a name="step-5-complete-waves-of-migration-effort"></a>手順 5:移行作業のウェーブを完了する

移行は、クラウド導入チームによって実施される反復可能なプロセスです。 このプロセス全般で、ポートフォリオ全体のコストを最適化できる機会が数多くあります。 これらのプロセス内の決定の多くは、移行の各ウェーブまたは反復中に、ワークロードの小さなグループに適用されます。

**成果物:**

- 完全に最適化されたワークロードのコレクションのベンチマーク、テスト、サイズ変更、デプロイを行います。

**成果物の完成をサポートするためのガイダンス:**

- [移行に重点を置いたコスト管理のメカニズム](../migrate/azure-migration-guide/manage-costs.md)では、移行中に役立つクラウドネイティブのコスト最適化制御に関する分析情報を提供します。
- [移行されるワークロードのコストを最適化するためのベスト プラクティス](../migrate/azure-best-practices/migrate-best-practices-costs.md)には、各ワークロードのリリース コストを最大限に最適化するために移行の前後で従う必要がある 14 のベスト プラクティスのチェックリストが含まれています。

長期的な運用コストは、移行プロセスの改善の各領域における一般的なテーマです。 次のプロセス改善の一覧は、移行プロセスの段階別に編成されています。

- [前提条件](../migrate/migration-considerations/prerequisites/index.md): 変更とバックログの管理に関する情報を提供します。これは、クラウド コストの予算と実際のコストの両方に影響します。
- [評価](../migrate/migration-considerations/assess/index.md): 前提条件の検証からパートナー オプションの理解までの 6 つの具体的なプロセスについて説明しています。これらはそれぞれ、クラウド最適化の機会に影響を与えます。
- [移行](../migrate/migration-considerations/migrate/index.md): 資産の修復に関する 1 つのプロセス案が含まれています。これは、最適化されたソリューションを優先して、構成どおりの状態を最適化する機会を提供します。
- [昇格](../migrate/migration-considerations/optimize/index.md): 昇格では、テストから廃止された資産の使用停止まで、移行された資産のテスト、サイズ調整、検証、リリースに重点を置きます。 これは、予測と予算を実際のパフォーマンスと構成に対してテストできる最初の明確なポイントです。

<!-- markdownlint-disable MD033 -->
<br>

| 説明責任チーム | 実行責任チームとサポート チーム |
| --- | --- |
| <li> クラウド導入チーム | <li> クラウド戦略チーム <li> クラウド ガバナンス チーム <li> クラウドのセンター オブ エクセレンスまたは中央 IT |

## <a name="step-6-drive-customer-focused-innovation"></a>手順 6:顧客重視のイノベーションを推進する

新製品のイノベーションと開発では、より詳細なアーキテクチャ レビューが必要です。 クラウド導入フレームワークは、イノベーション プロセスと製品管理の考え方に関する詳細を提供します。 ただし、新しいイノベーションに関するコスト最適化の決定は、このガイダンスの範囲外です。

**成果物:**

- コストと他の重要な設計上の考慮事項のバランスをとるために、新しいイノベーションに関するアーキテクチャの主要な決定を行います。

**成果物の完成をサポートするためのガイダンス:**

- [Azure Architecture Review](https://docs.microsoft.com/assessments/?id=azure-architecture-review) を使用して、アーキテクチャに関する決定のバランスを把握します。
- イノベーションでのコスト最適化に関する詳細なガイダンスについては、「[Azure Architecture Framework](https://docs.microsoft.com/azure/architecture/framework)」を参照してください。

<!-- markdownlint-disable MD033 -->
<br>

| 説明責任チーム | 実行責任チームとサポート チーム |
| --- | --- |
| <li> クラウド導入チーム | <li> クラウド戦略チーム <li> クラウド ガバナンス チーム <li> クラウドのセンター オブ エクセレンスまたは中央 IT |

## <a name="step-7-implement-sound-operations"></a>手順 7:堅固な運用を実装する

堅固な管理ベースラインを確立したら、データを収集し、運用アラートを作成します。これは、コストを最適化する機会の検出に役立ちます。 この作業で重視されるのは、回復性とコストの最適化のバランスです。

**成果物:**

- 各ワークロードの重要性と回復性の分類に合わせて、コストを最適化するための継続的な推奨事項についてエンタープライズ環境を監視します。

**成果物の完成をサポートするためのガイダンス:**

- [ビジネス アライメントを作成](../manage/considerations/business-alignment.md)して、回復性への投資の重要性と意欲を明確にします。

<!-- markdownlint-disable MD033 -->
<br>

| 説明責任チーム | 実行責任チームとサポート チーム |
| --- | --- |
| <li> クラウド運用チーム | <li> クラウド戦略チーム <li> クラウド ガバナンス チーム <li> クラウドのセンター オブ エクセレンスまたは中央 IT |

## <a name="value-statement"></a>バリュー ステートメント

これらの手順に従うことで、[コスト志向の組織を構築する](../organize/cost-conscious-organization.md)ことができます。 所有権を共有し、適切なチームと適切なタイミングで共同作業を行うことにより、コストの最適化をより簡単に実装できます。
