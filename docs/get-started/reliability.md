---
title: 作業を開始しましょう。適切な管理によって信頼性を向上させる
description: ガバナンス コントロールと管理ベースラインを使用した信頼性向上の基礎について説明します。
author: JanetCThomas
ms.author: janet
ms.date: 04/04/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: overview
ms.openlocfilehash: 8e17c53a2a54a212fa312b62b2ff527497583919
ms.sourcegitcommit: 60d8b863d431b5d7c005f2f14488620b6c4c49be
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2020
ms.locfileid: "83230471"
---
<!-- docsTest:ignore Microsoft-Cloud-Adoption-Framework-Strategy-and-Plan-Template -->

# <a name="get-started-improve-reliability-with-the-right-controls"></a>作業を開始しましょう。適切な管理によって信頼性を向上させる

どうすれば適切な管理策を適用して信頼性を向上させることができるのでしょうか。 このガイドに従うと、構成、リソース編成、セキュリティ ベースライン、リソース保護の不整合に関連した中断を最小限に抑えることができます。 このガイドの各手順を通じて、運用チームは IT ポートフォリオ全体で信頼性とコストのバランスを取ることができ、また、ガバナンス チームは一貫してそのバランスを確実に適用することができます。 信頼性は、他の役割や機能にも依存します。 この記事では、関係する各チーム間で一致した認識を形成できるよう、そうしたさまざまな関連機能を明らかにしていきます。

企業信頼性において、運用管理とガバナンスは対等なパートナーです。 信頼性のベースラインは、運用プラクティスに関してなされた判断によって決まります。 環境全体のガバナンスに使用されるアプローチを通じて、すべてのリソースの一貫性が確保されます。 このガイドの手順 1. と手順 2. は、両チームの最初の一歩を手助けするものです。 順番に列挙されていますが、次の手順を同時に進めてもかまいません。 後続の手順では、全社的なソリューションの信頼性向上に向けて共有された取り組みに企業全体が着手できるよう支援します。

![企業信頼性の概要](../_images/get-started/reliability-map.png)

## <a name="step-1-establish-operations-management-requirements"></a>手順 1:運用管理の要件を規定する

すべてのワークロードが等しく作成されているわけではありません。 どの環境にも、ビジネスに常時直接の影響を及ぼすワークロードは存在します。 また、ビジネス全体への影響は相対的に小さくなりますが、関連するビジネス プロセスやワークロードも存在します。 この手順では、IT ポートフォリオ全体をサポートするための初期要件をクラウド運用チームが特定し、導入します。

**成果物:**

- すべての運用ワークロードで必要となる標準的な運用を定義するための管理ベースラインを導入します。
- クラウド戦略チームと協議してビジネス コミットメントを取り決め、高度な運用と回復性の要件に向けた計画を策定します。
- 大半のワークロードに追加の運用が必要である場合は、管理ベースラインを拡張します。
- より重要度の高いワークロードをサポートするランディング ゾーンとリソースに高度な運用要件を適用します。
- [運用管理ブック](https://raw.githubusercontent.com/microsoft/CloudAdoptionFramework/master/manage/opsmanagementworkbook.xlsx)で、IT ポートフォリオ全体における運用上の判断を文書化します。

**成果物の完遂をサポートするうえでのガイダンス:**

- **[管理ベースライン](../manage/considerations/discipline.md):**

  - [インベントリと可視性](../manage/considerations/inventory.md): [データの収集](../manage/monitor/data-collection.md)、[アラートの構成](../manage/monitor/index.md)、実際の運用モデルに最適な[監視プラットフォーム](../manage/monitor/index.md)の導入を、[クラウドネイティブのツール](../manage/azure-management-guide/inventory.md)で効率よく行うことができます。
  - [運用のコンプライアンス](../manage/considerations/operational-compliance.md):機能停止の大部分は、リソースの構成に対する変更や不適切なメンテナンス プラクティスから生じる傾向があります。 [Azure サーバー管理ガイド](../manage/azure-server-management/index.md)に従ってクラウド ネイティブのツールを導入し、リソースの構成に対する変更やパッチを管理してください。
  - [保護と回復](../manage/considerations/protect.md): どのプラットフォームでも機能停止は必ず起きるものです。 [バックアップと回復のソリューション](../manage/azure-management-guide/protect-recover.md)で備え、中断が発生しても中断時間が最小限で済むようにしてください。

- **[高度な運用](../manage/design-principles.md):** [ビジネス アラインメント](../manage/considerations/business-alignment.md)に関する対話の基礎として管理ベースラインを使用し、[重要度](../manage/considerations/criticality.md)、[事業影響度](../manage/considerations/impact.md)、[運用コミットメント](../manage/considerations/commitment.md)を明確にします。 ビジネス アラインメントを通じて、[ベースラインの改善](../manage/azure-management-guide/enhanced-baseline.md)、特定の[テクノロジ プラットフォーム](../manage/azure-management-guide/workload-specialization.md)の管理、[ワークロード固有の運用](../manage/azure-management-guide/platform-specialization.md)への要求を定量化して検証することができます。

- **アーキテクチャ レビューのガイド:** 運用上の要件を満たすために、ワークロード レベルでのアーキテクチャの変更が必要になる場合があります。 [Azure Architecture Framework](https://docs.microsoft.com/azure/architecture/framework/cost/tradeoffs) と [Azure Architecture Review](https://docs.microsoft.com/assessments?id=azure-architecture-review) は、特定のワークロードの技術所有者との対話を導くのに役立ちます。

<!-- markdownlint-disable MD033 -->
<br>

| 説明責任チーム | 実行責任チームとサポート チーム |
| --- | --- |
| <li> クラウド運用チーム | <li> クラウド戦略チーム <li> クラウド導入チーム <li> クラウド ガバナンス チーム <li> クラウドのセンター オブ エクセレンスまたは中央 IT |

## <a name="step-2-consistently-apply-the-management-baseline"></a>手順 2:管理ベースラインを一貫性をもって適用する

企業信頼性には、管理ベースラインの一貫した適用が必要となります。 その一貫性は、対象となるすべてのリソースについて、適切な企業ポリシーと IT プロセス、自動化ツールで管理ベースラインの導入を統制することによって得られます。

**成果物:**

- 対象となるすべてのシステムに管理ベースラインが適切に適用されていることを確認します。
- [リソースの整合性の規範テンプレート](../govern/resource-consistency/template.md)で、リソースの整合性のポリシー、プロセス、設計ガイダンスを文書化します。

**成果物の完遂をサポートするうえでのガイダンス:**

- すべてのワークロードとリソースを[適切な名前付けおよびタグ付け規則](../ready/azure-best-practices/naming-and-tagging.md)に準拠させ、"重要度" のタグに特に重点を置いて [Azure Policy を使用してタグ付け規則を適用する](https://docs.microsoft.com/azure/governance/policy/tutorials/govern-tags)ようにします。
- クラウド ガバナンスに不慣れな場合は、ガバナンスの方法論を使用して、[ガバナンスのポリシー、プロセス、規範](../govern/index.md)を確立してください。
- コスト管理の規範に不慣れな場合は、[コスト管理の改善に関する記事](../govern/guides/complex/cost-management-improvement.md)に従って検討してください。特に、[導入](../govern/guides/complex/cost-management-improvement.md#incremental-improvement-of-the-best-practices)に関するセクションを重点的にお読みください。

> [!NOTE]
> **信頼性の実現に向けた他のチームとの連携に着手する手順:** クラウド導入ライフサイクル全体におけるさまざまな判断が信頼性に直接影響します。 以降の手順では、IT ポートフォリオ全体で一貫した信頼性を実現するために必要な連携と支援について概説します。

<!-- markdownlint-disable MD033 -->
<br>

| 説明責任チーム | 実行責任チームとサポート チーム |
| --- | --- |
| <li> クラウド ガバナンス チーム | <li> クラウド戦略チーム <li> クラウド運用チーム <li> クラウドのセンター オブ エクセレンスまたは中央 IT |

## <a name="step-3-define-your-strategy"></a>手順 3:戦略を定義する

**成果物:**

- [戦略と計画のテンプレート](https://archcenter.blob.core.windows.net/cdn/fusion/readiness/Microsoft-Cloud-Adoption-Framework-Strategy-and-Plan-Template.docx)で、動機、成果、業務上の正当な理由を記録します。
- クラウド導入の戦略的な方向性に沿った運用上のサポートが管理ベースラインから確実に得られるようにします。

**成果物の完遂をサポートするうえでのガイダンス:**

- [動機を把握する](../strategy/motivations.md): 重要なビジネス イベントや一部の移行の動機は、コスト重視となる傾向があり、その後のすべての取り組みでコスト管理の重要性が大きくなります。 一方、イノベーションまたは移行による成長に関連した未来志向の動機もあり、その場合には、より営業収益に重点が置かれます。 動機を把握することで、コスト管理にどの程度の優先度を置くべきかが理解しやすくなります。
- [ビジネス成果](../strategy/business-outcomes/index.md):いくつかの財務上の成果は、極度にコスト重視となる傾向があります。 目的の成果を財務上のメトリックに対応付ける場合、早い段階でガバナンスのコスト管理の規範に投資する必要があります。
- [業務上の正当な理由](../strategy/cloud-migration-business-case.md): 業務上の正当な理由は、クラウド導入の財務計画全体の概要を示します。 これは、最初の予算作成に役立つ情報源となる可能性があります。

戦略的な決定は、信頼性に直接影響を及ぼし、その影響は導入ライフサイクル全体と長期的な運用に波及します。 戦略を明確にすることで、信頼性への取り組みが向上します。

<!-- markdownlint-disable MD033 -->
<br>

| 説明責任チーム | 実行責任チームとサポート チーム |
| --- | --- |
| <li> クラウド戦略チーム | <li> クラウド ガバナンス チーム <li> クラウド運用チーム <li> クラウドのセンター オブ エクセレンスまたは中央 IT |

## <a name="step-4-develop-a-cloud-adoption-plan"></a>手順 4:クラウド導入計画を作成する

デジタル資産 (既存の IT ポートフォリオの分析) は、業務上の正当な理由を検証するうえでの助けとなるほか、IT ポートフォリオの全体像を正確に把握することにも貢献します。 導入計画により、導入時のアクティビティのタイムラインが明確になります。 計画とデジタル資産分析の整合性を確保することで、将来の運用管理における依存関係を考慮する手段が得られます。 また、計画に対する理解は、クラウド運用チームを開発サイクルに招き入れ、ワークロードの運用に必要な、管理ベースラインに対するあらゆる変更を評価、計画することにもつながります。

**成果物:**

- 目的の戦略を実現するために必要な変更を反映するように、[戦略と計画のテンプレート](https://archcenter.blob.core.windows.net/cdn/fusion/readiness/Microsoft-Cloud-Adoption-Framework-Strategy-and-Plan-Template.docx)を更新します。 記録する変更としては、次のようなものがあります。
  - 既存のデジタル資産の評価。
  - 必要な変更とそれに伴う作業を反映するクラウド導入計画。
  - 計画に基づいて実現する必要がある組織の変更。
  - 既存のチームが必要な作業を成功させるために必要となるスキルに取り組むための計画。
- ガバナンス チームと連携して、コスト モデルと予測モデルを対応付けます (定量分析による支出の最適化に向けた取り組みを含む)。

**成果物の完遂をサポートするうえでのガイダンス:**

- [インベントリの収集](../digital-estate/inventory.md)。 導入前に、デジタル資産を分析するためのデータ ソースを確立します。
- [ベスト プラクティス - Azure Migrate](../plan/contoso-migration-assessment.md)。 Azure Migrate を使用してインベントリを収集します。
- [増分型の合理化](../digital-estate/rationalize.md#incremental-rationalization)。 漸進的な合理化と定量分析では、予算を作成するためにクラウドの候補を特定することができます。
- [コスト モデルと予測モデルの対応付け](../digital-estate/calculate.md)。 Azure Cost Management を使用して、[予算を作成](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-acm-create-budgets?toc=/azure/cloud-adoption-framework/toc.json&bc=/azure/cloud-adoption-framework/_bread/toc.json)することにより、コスト モデルと予測モデルを対応付けます。
- [クラウド導入計画の作成](../plan/plan-intro.md#build-your-cloud-adoption-plan)。 実用的なワークロード、資産、タイムラインの詳細を含む計画を作成します。

<!-- markdownlint-disable MD033 -->
<br>

| 説明責任チーム | 実行責任チームとサポート チーム |
| --- | --- |
| <li> クラウド戦略チーム | <li> クラウド導入チーム <li> クラウド ガバナンス チーム <li> クラウド運用チーム <li> クラウドのセンター オブ エクセレンスまたは中央 IT |

## <a name="step-5-implement-landing-zone-best-practices"></a>手順 5:ランディング ゾーンのベスト プラクティスを導入する

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

## <a name="step-6-complete-waves-of-adoption-effort-and-change"></a>手順 6:導入作業と変更のウェーブを完了する

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
| <li> クラウド導入チーム | <li> クラウド戦略チーム <li> クラウド運用チーム <li> クラウド ガバナンス チーム <li> クラウドのセンター オブ エクセレンスまたは中央 IT |

## <a name="value-statement"></a>バリュー ステートメント

以上の手順によって、企業全体およびホストされているすべてのリソースで信頼性を確保するために必要な適切な管理策とプロセスを導入することができます。
