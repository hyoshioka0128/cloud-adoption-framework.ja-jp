---
title: 作業を開始しましょう。適切な管理によって信頼性を向上させる
description: ガバナンス コントロールと管理ベースラインを使用した信頼性向上の基礎について説明します。
author: JanetCThomas
ms.author: janet
ms.date: 05/15/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: overview
ms.custom: internal
ms.openlocfilehash: a42115e034e0f737e05feedad44cd5c0c777317f
ms.sourcegitcommit: b6f2b4f8db6c3b1157299ece1f044cff56895919
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2020
ms.locfileid: "97022343"
---
# <a name="get-started-improve-reliability-with-the-right-controls"></a>作業を開始しましょう。適切な管理によって信頼性を向上させる

どうすれば適切な管理策を適用して信頼性を向上させることができるのでしょうか。 この記事は、次のことに関連する中断を最小限に抑えるのに役立ちます。

- 構成の不整合。
- リソースの編成。
- セキュリティ ベースライン。
- リソースの保護。

この記事の手順は、運用チームが IT ポートフォリオ全体の信頼性とコストのバランスを取るのに役立ちます。 また、この記事は、運用チームがバランスが一貫して適用されるよう確保するのにも役立ちます。 信頼性は、他の役割や機能にも依存します。 この記事では、関係するチームの中で一致した認識を形成できるよう、関連機能を明らかにしていきます。

企業信頼性において、運用管理とガバナンスは対等なパートナーです。 信頼性のベースラインは、運用プラクティスに関して下した判断によって決まります。 環境全体のガバナンスに使用されるアプローチを通じて、すべてのリソースの一貫性が確保されます。

この記事の手順 1 と手順 2 は、両チームの最初の一歩を手助けするものです。 順番にリストされていますが、並列に実行することもできます。 後続の手順では、全社的なソリューションの信頼性向上に向けて共有された取り組みに企業全体が着手できるよう支援します。

![企業信頼性の概要](../_images/get-started/reliability-map.png)

## <a name="step-1-establish-operations-management-requirements"></a>手順 1:運用管理の要件を規定する

すべてのワークロードは同じになるように作成されているわけではありません。 どの環境にも、ビジネスに常時直接の影響を及ぼすワークロードは存在します。 また、ビジネス全体への影響は相対的に小さくなりますが、関連するビジネス プロセスやワークロードも存在します。 この手順では、IT ポートフォリオ全体をサポートするための初期要件をクラウド運用チームが特定し、導入します。

**成果物:**

- すべての運用ワークロードで必要となる標準的な運用を定義するための管理ベースラインを導入します。
- クラウド戦略チームと協議してビジネス コミットメントを取り決め、高度な運用と回復性の要件に向けた計画を策定します。
- 大半のワークロードに追加の運用が必要である場合は、管理ベースラインを拡張します。
- 最も重要度の高いワークロードをサポートするランディング ゾーンとリソースに高度な運用要件を適用します。
- [運用管理ブック](https://raw.githubusercontent.com/Microsoft/CloudAdoptionFramework/master/manage/opsmanagementworkbook.xlsx)で、IT ポートフォリオ全体における運用上の判断を文書化します。

**成果物の完遂をサポートするうえでのガイダンス:**

- [管理ベースライン](../manage/considerations/discipline.md):

  - [インベントリと可視性](../manage/considerations/inventory.md): [クラウドネイティブのツール](../manage/azure-management-guide/inventory.md)は、[データの収集](../manage/monitor/data-collection.md)と[アラートの構成](../manage/monitor/index.md)に役立ちます。 また、このツールは、運用モデルに最適な[監視プラットフォーム](../manage/monitor/index.md)を実装するのにも役立ちます。
  - [運用のコンプライアンス](../manage/considerations/operational-compliance.md):機能停止の大部分は、リソースの構成に対する変更や不適切なメンテナンス プラクティスから生じる傾向があります。 [Azure サーバー管理ガイド](../manage/azure-server-management/index.md)に従ってクラウドネイティブのツールを実装し、リソースの構成に対する変更やパッチを管理してください。
  - [保護と回復](../manage/considerations/protect.md): どのプラットフォームでも機能停止は必ず起きるものです。 [バックアップと回復のソリューション](../manage/azure-management-guide/protect-recover.md)で備え、中断が発生しても中断時間が最小限で済むようにしてください。
- [高度な運用](../manage/design-principles.md): [ビジネス アラインメント](../manage/considerations/business-alignment.md)に関する会話の基盤として、管理ベースラインを使用します。 [重要度](../manage/considerations/criticality.md)、[ビジネスへの影響](../manage/considerations/impact.md)、[運用上のコミットメント](../manage/considerations/commitment.md)について明確に説明するのに役立ちます。 ビジネス アラインメントを通じて、[ベースラインの改善](../manage/azure-management-guide/enhanced-baseline.md)、特定の[テクノロジ プラットフォーム](../manage/azure-management-guide/workload-specialization.md)の管理、[ワークロード固有の運用](../manage/azure-management-guide/platform-specialization.md)への要求を定量化して検証することができます。
- **アーキテクチャ レビューのガイド:** 運用上の要件を満たすために、ワークロード レベルでのアーキテクチャの変更が必要になる場合があります。 [Microsoft Azure Well-Architected Framework](/azure/architecture/framework/cost/tradeoffs) と [Microsoft Azure Well-Architected Review](/assessments?id=azure-architecture-review) は、特定のワークロードの技術所有者との対話を導くのに役立ちます。

<br>

| 説明責任チーム | 実行責任チームとサポート チーム |
| --- | --- |
| <li> クラウド運用チーム | <li> クラウド戦略チーム <li> クラウド導入チーム <li> クラウド ガバナンス チーム <li> クラウドのセンター オブ エクセレンスまたは中央 IT チーム |

## <a name="step-2-consistently-apply-the-management-baseline"></a>手順 2:管理ベースラインを一貫性をもって適用する

企業信頼性には、管理ベースラインの一貫した適用が必要となります。 この一貫性は、適切な企業ポリシー、IT プロセス、および自動化ツールによってもたらされます。 これらのリソースが、影響を受けるすべてのリソースの管理ベースラインの実装を制御します。

**成果物:**

- 対象となるすべてのシステムに管理ベースラインが適切に適用されていることを確認します。
- [リソースの整合性の規範テンプレート](../govern/resource-consistency/template.md)で、リソースの整合性のポリシー、プロセス、設計ガイダンスを文書化します。

**成果物の完遂をサポートするうえでのガイダンス:**

- すべてのワークロードとリソースが[適切な名前付けおよびタグ付け規則](../ready/azure-best-practices/naming-and-tagging.md)に従っていることを確認します。 [Azure Policy を使用してタグ付け規則を適用](/azure/governance/policy/tutorials/govern-tags)し、重要度のタグを特に強調します。
- クラウド ガバナンスを初めて使用する方は、ガバナンスの方法論を使用して、[ガバナンスのポリシー、プロセス、規範](../govern/index.md)を確立してください。
- コスト管理規範を初めて使用する方は、[コスト管理の規範の改善](../govern/guides/complex/cost-management-improvement.md)に関する記事に記載されているガイダンスに従ってください。 [実装](../govern/guides/complex/cost-management-improvement.md#incremental-improvement-of-best-practices)のセクションに注目します。

> [!NOTE]
> **信頼性の実現に向けた他のチームとの連携に着手する手順:** クラウド導入ライフサイクル全体におけるさまざまな判断が信頼性に直接影響します。 以降の手順では、IT ポートフォリオ全体で一貫した信頼性を実現するために必要な連携と支援について概説します。

<br>

| 説明責任チーム | 実行責任チームとサポート チーム |
| --- | --- |
| <li> クラウド ガバナンス チーム | <li> クラウド戦略チーム <li> クラウド運用チーム <li> クラウドのセンター オブ エクセレンスまたは中央 IT チーム |

## <a name="step-3-define-your-strategy"></a>手順 3:戦略を定義する

戦略的な判断は、信頼性に直接影響します。 それらは、導入ライフサイクルと長期的な運用に波及します。 戦略を明確にすることで、信頼性への取り組みが向上します。

**成果物:**

- [戦略と計画のテンプレート](https://raw.githubusercontent.com/microsoft/CloudAdoptionFramework/master/plan/cloud-adoption-framework-strategy-and-plan-template.docx)に、動機、成果、業務上の正当な理由を記録します。
- クラウド導入の戦略的な方向性に沿った運用上のサポートが管理ベースラインから確実に得られるようにします。

**成果物の完遂をサポートするうえでのガイダンス:**

- [動機を把握する](../strategy/motivations.md): 重要なビジネス イベントや、移行の動機によっては、コストが重視される傾向があります。 そのような領域においては、後のあらゆる取り組みについてコスト管理の重要性が高くなる場合があります。 一方、イノベーションまたは移行による成長に関連した未来志向の動機もあり、その場合には、より営業収益に重点が置かれます。 動機を理解することは、コスト管理の優先順位を決めるうえで役立ちます。
- [ビジネス成果](../strategy/business-outcomes/index.md):一部の財政上の成果では、コストが極端に重視される傾向があります。 目的の成果を財務上のメトリックに対応付ける場合は、初期段階でコスト管理ガバナンス規範に投資してください。
- [業務上の正当な理由](../strategy/cloud-migration-business-case.md): 業務上の正当な理由は、クラウド導入の財務計画全体の概要を示します。 これは、最初の予算作成に役立つ情報源となる可能性があります。

<br>

| 説明責任チーム | 実行責任チームとサポート チーム |
| --- | --- |
| <li> クラウド戦略チーム | <li> クラウド ガバナンス チーム <li> クラウド運用チーム <li> クラウドのセンター オブ エクセレンスまたは中央 IT チーム |

## <a name="step-4-develop-a-cloud-adoption-plan"></a>手順 4:クラウド導入計画を作成する

デジタル資産 (または既存の IT ポートフォリオの分析) は、業務上の正当な理由を検証するのに役立ちます。 IT ポートフォリオ全体の詳細を見渡せるようになります。 導入計画により、導入時のアクティビティのタイムラインが明確になります。

導入計画とデジタル資産分析を連携すると、今後の運用管理の依存関係を計画できます。 また、導入計画を理解することで、開発サイクルにクラウド運用チームを招待します。 チームは、ワークロードを運用するために必要な管理ベースラインに対する変更を評価し、計画することができます。

**成果物:**

- 目的の戦略を実現するために必要な変更を反映するように、[戦略と計画のテンプレート](https://raw.githubusercontent.com/microsoft/CloudAdoptionFramework/master/plan/cloud-adoption-framework-strategy-and-plan-template.docx)を更新します。 記録される変更としては、次のようなものがあります。

  - 既存のデジタル資産の評価。
  - 必要な変更とそれに伴う作業を反映するクラウド導入計画。
  - 計画を実現するために必要な組織の変更。
  - 既存のチームが必要な作業を成功させるために必要となるスキルに対処するための計画。
- ガバナンス チームと協力して、コスト モデルと予測モデルを調整します。 このプロセスには、定量分析によって支出の最適化を開始する作業が含まれます。

**成果物の完遂をサポートするうえでのガイダンス:**

- [インベントリの収集](../digital-estate/inventory.md): 導入前に、デジタル資産を分析するためのデータ ソースを確立します。
- [ベスト プラクティス:Azure Migrate](../plan/contoso-migration-assessment.md):Azure Migrate を使用してインベントリを収集します。
- [増分型の合理化](../digital-estate/rationalize.md#incremental-rationalization):増分型の合理化の間は、定量分析により予算を作成するためにクラウドの候補を特定することができます。
- [コスト モデルと予測モデルの対応付け](../digital-estate/calculate.md): Azure Cost Management + Billing を使用して、[予算を作成](/azure/cost-management-billing/costs/tutorial-acm-create-budgets?toc=/azure/cloud-adoption-framework/toc.json&bc=/azure/cloud-adoption-framework/_bread/toc.json)することにより、コストと予測モデルを対応付けます。
- [クラウド導入計画の作成](../plan/plan-intro.md#build-your-cloud-adoption-plan): 実用的なワークロード、資産、タイムラインの詳細を含む計画を作成します。

<br>

| 説明責任チーム | 実行責任チームとサポート チーム |
| --- | --- |
| <li> クラウド戦略チーム | <li> クラウド導入チーム <li> クラウド ガバナンス チーム <li> クラウド運用チーム <li> クラウドのセンター オブ エクセレンスまたは中央 IT チーム |

## <a name="step-5-implement-landing-zone-best-practices"></a>手順 5:ランディング ゾーンのベスト プラクティスを導入する

クラウド導入フレームワークの "準備" 手法では、クラウドでワークロードをホストするランディングゾーンの開発に重点を置いています。 ランディング ゾーンの導入中は、運用に影響を及ぼすさまざまな意思決定が生じます。 ランディング ゾーンを見直して、よりよい運用を実現するために、クラウド運用チームに助言を求めます。 また、ランディング ゾーンの設計に影響を及ぼす可能性のあるリソースの整合性ポリシーと設計ガイダンスに対する理解を深めるために、クラウド ガバナンス チームにも助言を求めます。

**成果物:**

- 短期的な導入計画で、ワークロードをホストできる 1 つ以上のランディング ゾーンをデプロイします。
- すべてのランディング ゾーンが運用上の判断とリソースの整合性の要件に沿っていることを確認します。

**成果物の完遂をサポートするうえでのガイダンス:**

- [ランディング ゾーンの運用の改善](../ready/considerations/landing-zone-operations.md): 特定のランディング ゾーン内の運用を強化するためのベストプラクティスが紹介されています。

<br>

| 説明責任チーム | 実行責任チームとサポート チーム |
| --- | --- |
| <li> クラウド導入チーム | <li> クラウド運用チーム <li> クラウド戦略チーム <li> クラウド ガバナンス チーム <li> クラウドのセンター オブ エクセレンスまたは中央 IT チーム |

## <a name="step-6-complete-waves-of-adoption-effort-and-change"></a>手順 6:導入作業と変更のウェーブを完了する

長期的な運用は、移行とイノベーションの取り組みの段階で行った意思決定の影響を受けることがあります。 導入プロセスの早い段階で一致した認識を一貫して維持することで、運用リリースに対する障壁を取り除くことができます。 また、新しいソリューションを運用管理プラクティスに導入するために必要な作業も少なくなります。

**成果物:**

- リソースの整合性ポリシーを使用して、運用環境のデプロイの運用準備状況をテストします。
- リソース整合性の設計ガイダンスと運用の要件に準拠していることを確認します。
- 高度な運用の要件を[運用管理ブック](https://raw.githubusercontent.com/Microsoft/CloudAdoptionFramework/master/manage/opsmanagementworkbook.xlsx)で文書化します。

**成果物の完遂をサポートするうえでのガイダンス:**

- [環境の準備状況チェックリスト](../migrate/migration-considerations/prerequisites/planning-checklist.md)
- [昇格前のチェックリスト](../migrate/migration-considerations/optimize/ready.md)
- [運用リリースのチェックリスト](../migrate/migration-considerations/optimize/promote.md)

<br>

| 説明責任チーム | 実行責任チームとサポート チーム |
| --- | --- |
| <li> クラウド導入チーム | <li> クラウド戦略チーム <li> クラウド運用チーム <li> クラウド ガバナンス チーム <li> クラウドのセンター オブ エクセレンスまたは中央 IT チーム |

## <a name="value-statement"></a>バリュー ステートメント

これらの手順は、企業全体およびホストされているすべてのリソースで信頼性を確保するために必要な、管理策とプロセスを導入するのに役立ちます。
