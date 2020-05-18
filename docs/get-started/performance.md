---
title: 作業を開始しましょう。ポートフォリオ全体で一貫したパフォーマンスを確保する
description: パフォーマンスの維持、パフォーマンスの期待値の設定、組織全体での整合性確保のためのプロセスを確立することを含む、ポートフォリオ全体にわたるパフォーマンス管理の基礎について説明します。
author: JanetCThomas
ms.author: janet
ms.date: 04/04/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: overview
ms.openlocfilehash: 6052e119cc2bf2ce078bfdc3df6613ff665e503f
ms.sourcegitcommit: 60d8b863d431b5d7c005f2f14488620b6c4c49be
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2020
ms.locfileid: "83230451"
---
<!-- docsTest:ignore Microsoft-Cloud-Adoption-Framework-Strategy-and-Plan-Template -->

# <a name="get-started-ensure-consistent-performance-across-a-portfolio"></a>作業を開始しましょう。ポートフォリオ全体で一貫したパフォーマンスを確保する

ワークロードのポートフォリオ全体で十分なパフォーマンスを確保するにはどうすればよいのでしょうか。 このガイドは、企業全体のパフォーマンスを維持するためのプロセスを確立するのに役立ちます。 ここに記載されている手順を参考に、運用チームは、すべてのワークロードで一貫したパフォーマンスの期待値を設定できます。 パフォーマンスは、他の役割や機能にも依存します。 この記事では、関係する各チーム間で一致した認識を形成できるよう、そうした関連機能を明らかにしていきます。

ポートフォリオ全体で一貫したパフォーマンスを確保するうえで最も一般的なアプローチは、運用管理の一元化です。 運用ベースラインと包括的な改善を決定するのは、運用プラクティスに関する判断です。 このガイドの最初の手順は、運用チームの最初の一歩を手助けするものです。 後続の手順では、ワークロードのポートフォリオ全体でのエンタープライズ パフォーマンスに向けて共有された取り組みに企業全体が着手できるよう支援します。

![エンタープライズ パフォーマンス管理の概要](../_images/get-started/performance-map.png)

## <a name="step-1-establish-operations-management-requirements"></a>手順 1:運用管理の要件を規定する

クラウド導入フレームワークにまとめられている運用管理のベースラインでは、運用の一貫性を確保するための一連の管理策とクラウドネイティブの運用ツールが提供されています。 そのベースラインを自動化ツールで拡張することにより、ポートフォリオ全体で一貫したパフォーマンス要件を満たすために必要なパフォーマンス監視と自動化がもたらされます。

**成果物:**

- パフォーマンスの期待値からの逸脱に関連した自動修復タスクを含むように管理ベースラインを改善します。
- パフォーマンスの要件を満たすためにワークロード固有のデータ パターンやアーキテクチャの変更が必要である場合、ワークロード固有の運用によって、より優れたパフォーマンス管理が可能となります。
- IT ポートフォリオ全体の運用に関する判断を[運用管理ブック](https://raw.githubusercontent.com/microsoft/CloudAdoptionFramework/master/manage/opsmanagementworkbook.xlsx)で文書化します。特に、[Baseline]\(ベースライン\) タブの [Operational Compliance]\(運用のコンプライアンス\) セクションで、パフォーマンスの自動化に関する判断を中心に入力します。

**成果物の完遂をサポートするうえでのガイダンス:**

- [管理ベースラインの改善](../manage/azure-management-guide/enhanced-baseline.md)に関する記事では、Azure Automation などのツールを使用してパフォーマンスに関連した強化を追加する例を取り上げています。 このアプローチには、関連資産のサイズとスケールに対する基本的な変更を通じて一貫したパフォーマンスを維持する効果があります。
- [ワークロード固有の運用](../manage/azure-management-guide/platform-specialization.md)では、Azure アーキテクチャ レビューを使用して、特定のワークロードの自動化に関するガイダンスを提供します。 パフォーマンス管理に対するこのアプローチは、ワークロード固有のデータによって運用上のアクションを推し進める必要がある場合に特に有効です。
- 前述のガイダンスは、ワークロードのポートフォリオをすべてサポートする[管理ベースライン](../manage/considerations/discipline.md)が既に導入されていることを前提としています。

> [!NOTE]
> **パフォーマンスの期待値について組織全体で認識を一致させる作業の開始手順:** クラウド導入ライフサイクル全体におけるさまざまな判断がパフォーマンスに直接影響します。 以降の手順では、IT ポートフォリオ全体でパフォーマンスを上げるために必要な連携と支援について概説します。

<!-- markdownlint-disable MD033 -->
<br>

| 説明責任チーム | 実行責任チームとサポート チーム |
| --- | --- |
| <li> クラウド運用チーム | <li> クラウド戦略チーム <li> クラウド導入チーム <li> クラウド ガバナンス チーム <li> クラウドのセンター オブ エクセレンスまたは中央 IT |

## <a name="step-2-consistent-application-of-the-management-baseline"></a>手順 2:管理ベースラインを一貫性をもって適用する

管理ベースラインが改善されたら、リソースの整合性というクラウド ガバナンスの規範にもそれらの改善が適用されるようにすることが大切です。 そうすることで、管理下のすべての環境に対し、改善されたベースラインが確実に適用されます。

**成果物:**

- 対象となるすべてのシステムに、改善された管理ベースラインが適切に適用されていることを確認します。
- [リソースの整合性の規範テンプレート](../govern/resource-consistency/template.md)で、リソースの整合性のポリシー、プロセス、設計ガイダンスを文書化します。

**成果物の完遂をサポートするうえでのガイダンス:**

- すべてのワークロードとリソースを[適切な名前付けおよびタグ付け規則](../ready/azure-best-practices/naming-and-tagging.md)に準拠させ、"重要度" のタグに特に重点を置いて [Azure Policy を使用してタグ付け規則を適用する](https://docs.microsoft.com/azure/governance/policy/tutorials/govern-tags)ようにします。
- クラウド ガバナンスに不慣れな場合は、ガバナンスの方法論を使用して、[ガバナンスのポリシー、プロセス、規範](../govern/index.md)を確立してください。
- コスト管理の規範に不慣れな場合は、[コスト管理の改善に関する記事](../govern/guides/complex/cost-management-improvement.md)に従って検討してください。特に、[導入](../govern/guides/complex/cost-management-improvement.md#incremental-improvement-of-the-best-practices)に関するセクションを重点的にお読みください。

<!-- markdownlint-disable MD033 -->
<br>

| 説明責任チーム | 実行責任チームとサポート チーム |
| --- | --- |
| <li> クラウド ガバナンス チーム | <li> クラウド戦略チーム <li> クラウド運用チーム <li> クラウドのセンター オブ エクセレンスまたは中央 IT |

## <a name="step-3-define-strategy"></a>手順 3:戦略を定義する

戦略的な決定は、パフォーマンスに直接影響を及ぼし、その影響は導入ライフサイクル全体と長期的な運用に波及します。 戦略を明確にすることで、パフォーマンスへの取り組みがポートフォリオ全体で向上します。 また、戦略が明確であることによって、ワークロードの特殊化と高度な運用がある程度要求されるワークロードを運用チームが把握しやすくなります。

**成果物:**

- [戦略と計画のテンプレート](https://archcenter.blob.core.windows.net/cdn/fusion/readiness/Microsoft-Cloud-Adoption-Framework-Strategy-and-Plan-Template.docx)で、動機、成果、業務上の正当な理由を記録します。
- クラウド導入の戦略的な方向性に沿った運用上のサポートが管理ベースラインから確実に得られるようにします。

**成果物の完遂をサポートするうえでのガイダンス:**

- [動機を把握する](../strategy/motivations.md): 重要なビジネス イベントや一部の移行の動機は、コスト重視となる傾向があり、その後のすべての取り組みでコスト管理の重要性が大きくなります。 一方、イノベーションまたは移行による成長に関連した未来志向の動機もあり、その場合には、より営業収益に重点が置かれます。 動機を把握することで、コスト管理にどの程度の優先度を置くべきかが理解しやすくなります。
- [ビジネス成果](../strategy/business-outcomes/index.md):いくつかの財務上の成果は、極度にコスト重視となる傾向があります。 目的の成果を財務上のメトリックに対応付ける場合、早い段階でガバナンスのコスト管理の規範に投資する必要があります。
- [業務上の正当な理由](../strategy/cloud-migration-business-case.md): 業務上の正当な理由は、クラウド導入の財務計画全体の概要を示します。 これは、最初の予算作成に役立つ情報源となる可能性があります。

<!-- markdownlint-disable MD033 -->
<br>

| 説明責任チーム | 実行責任チームとサポート チーム |
| --- | --- |
| <li> クラウド戦略チーム | <li> クラウド ガバナンス チーム <li> クラウド運用チーム <li> クラウドのセンター オブ エクセレンスまたは中央 IT |

## <a name="step-4-assess-and-plan-for-workload-adoption"></a>手順 4:ワークロードの導入を評価、計画する

デジタル資産 (既存の IT ポートフォリオの分析) は、業務上の正当な理由を検証するうえでの助けとなるほか、IT ポートフォリオの全体像を正確に把握することにも貢献します。 導入計画により、導入時のアクティビティのタイムラインが明確になります。 計画とデジタル資産分析の整合性を確保することで、将来の運用管理における依存関係を考慮する手段が得られます。 また、計画に対する理解は、クラウド運用チームを開発サイクルに招き入れ、ワークロードの運用に必要な、管理ベースラインに対するあらゆる変更を評価、計画することにもつながります。

**成果物:**

- [戦略と計画のテンプレート](https://archcenter.blob.core.windows.net/cdn/fusion/readiness/Microsoft-Cloud-Adoption-Framework-Strategy-and-Plan-Template.docx)を更新して、デジタル資産分析によってもたらされた変更を反映します。
- クラウド運用チームと連携して、短期的および長期的な導入計画における各ワークロードの重要度とビジネスへの影響を明確に定義します。
- クラウド運用チームと連携して、オペレーション レディネスのタイムラインを確立します。

**成果物の完遂をサポートするうえでのガイダンス:**

- [インベントリの収集](../digital-estate/inventory.md): 導入前に、デジタル資産を分析するためのデータ ソースを確立します。
- [ベスト プラクティス - Azure Migrate](../plan/contoso-migration-assessment.md): Azure Migrate を使用してインベントリを収集します。
- [増分型の合理化](../digital-estate/rationalize.md#incremental-rationalization):漸進的な合理化と定量分析では、予算を作成するためにクラウドの候補を特定することができます。
- [コスト モデルと予測モデルの対応付け](../digital-estate/calculate.md): Azure Cost Management を使用して、[予算を作成](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-acm-create-budgets?toc=/azure/cloud-adoption-framework/toc.json&bc=/azure/cloud-adoption-framework/_bread/toc.json)することにより、コスト モデルと予測モデルを対応付けます。
- [クラウド導入計画の作成](../plan/plan-intro.md#build-your-cloud-adoption-plan): 実用的なワークロード、資産、タイムラインの詳細を含む計画を作成します。

<!-- markdownlint-disable MD033 -->
<br>

| 説明責任チーム | 実行責任チームとサポート チーム |
| --- | --- |
| <li> クラウド戦略チーム | <li> クラウド ガバナンス チーム <li> クラウド運用チーム <li> クラウドのセンター オブ エクセレンスまたは中央 IT |

## <a name="step-5-expand-the-landing-zones"></a>手順 5:ランディング ゾーンを拡張する

クラウド導入フレームワークの "準備" 手法では、クラウドでワークロードをホストするランディングゾーンの開発に重点を置いています。 ランディング ゾーンの導入中は、運用に影響を及ぼすさまざまな意思決定が生じます。 ランディング ゾーンを見直して、よりよい運用を実現するために、クラウド運用チームに助言を求めます。 また、ランディング ゾーンの設計に影響を及ぼす可能性のある設計ガイダンスと "リソースの整合性" ポリシーに対する理解を深めるために、クラウド ガバナンス チームにも助言を求めます。

**成果物:**

- 短期的な導入計画で、ワークロードをホストできる 1 つ以上のランディング ゾーンをデプロイします。
- すべてのランディング ゾーンが運用上の判断とリソースの整合性の要件に沿っていることを確認します。

**成果物の完遂をサポートするうえでのガイダンス:**

- [ランディング ゾーンの運用の改善](../ready/considerations/landing-zone-operations.md): 特定のランディング ゾーン内の運用を強化するためのベストプラクティスが紹介されています。

<!-- markdownlint-disable MD033 -->
<br>

| 説明責任チーム | 実行責任チームとサポート チーム |
| --- | --- |
| <li> クラウド導入チーム | <li> クラウド運用チーム <li> クラウド戦略チーム <li> クラウド ガバナンス チーム <li> クラウドのセンター オブ エクセレンスまたは中央 IT |

## <a name="step-6-adoption"></a>手順 6:導入

長期的な運用は、移行とイノベーションの取り組みの段階で行った意思決定の影響を受けることがあります。 導入プロセスの初期段階で一貫した整合性を確保しておくことで、運用リリースに対する障壁をなくし、新しいソリューションを運用管理プラクティスにオンボードするうえで必要な作業量を減らすことができます。

**成果物:**

- リソースの整合性ポリシーを使用して、運用環境のデプロイの運用準備状況をテストします。
- リソース整合性の設計ガイダンスと運用の要件に準拠していることを確認します。
- 高度な運用の要件を[運用管理ブック](https://raw.githubusercontent.com/microsoft/CloudAdoptionFramework/master/manage/opsmanagementworkbook.xlsx)で文書化します。

**成果物の完遂をサポートするうえでのガイダンス:**

- [環境の準備状況チェックリスト](../migrate/migration-considerations/prerequisites/planning-checklist.md)
- [昇格前のチェックリスト](../migrate/migration-considerations/optimize/ready.md)
- [運用リリースのチェックリスト](../migrate/migration-considerations/optimize/promote.md)

<!-- markdownlint-disable MD033 -->
<br>

| 説明責任チーム | 実行責任チームとサポート チーム |
| --- | --- |
| <li> クラウド導入チーム | <li> クラウド戦略チーム <li> クラウド運用チーム <li> クラウド戦略チーム <li> クラウド ガバナンス チーム <li> クラウドのセンター オブ エクセレンスまたは中央 IT |

## <a name="value-statement"></a>バリュー ステートメント

以上の手順によって、企業全体およびホストされているすべてのリソースでパフォーマンスを確保するために必要な適切な管理策とプロセスを導入することができます。
