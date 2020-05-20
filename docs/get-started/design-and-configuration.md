---
title: 作業を開始しましょう。環境の設計と構成の阻害要素を取り除く
description: クラウド環境の設計と構成を開始します。
author: JanetCThomas
ms.author: janet
ms.date: 04/04/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: overview
ms.openlocfilehash: 007a2219a65147e1682de5d1e0a4ae29251d2153
ms.sourcegitcommit: 5d6a7610e556f7b8ca69960ba76a3adfa9203ded
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/14/2020
ms.locfileid: "83400006"
---
# <a name="get-started-design-and-configuration"></a>作業を開始しましょう。設計と構成

環境の設計と構成は、移行またはイノベーションに重点を置く導入作業にとって最も一般的な阻害要素です。 長期的な導入計画をサポートする設計を迅速に実装することが困難な場合があります。 この記事では、一般的な阻害要素を克服し、導入作業を促進するためのアプローチと一連の手順を説明します。

![設計と構成の開始](../_images/get-started/environment-map.png)

効果的な環境設計と構成の作成に必要な技術的作業は場合により複雑ですが、スコープを管理することで、クラウド プラットフォーム チームの成功率を高めることができます。 最大の課題は、複数の利害関係者間の調整です。一部の関係者は、導入作業を停止または遅らせる権限を持っています。 以下の手順で、短期的な目標を迅速に達成し、長期的な成功を確立する方法を概説します。

## <a name="step-1-document-the-business-strategy"></a>手順 1:ビジネス戦略を文書化する

移行の一般的な阻害要素を回避するには、明確で簡潔なビジネス戦略を確実に文書化します。 動機、ビジネス成果予想、業務上の正当な理由について利害関係者の調整を行うことが、導入および環境構成全体において重要です。

明確で簡潔なビジネス戦略は、クラウド プラットフォーム チームが、環境構成を決定する際に何が重要で何を優先すべきかを把握するのに役立ちます。 特に、イノベーションの速度とコントロールの順守のどちらかを選択しなければならない場合に、チームが意思決定を行うのに役立ちます。

**成果物:**

- [戦略と計画のテンプレート](https://archcenter.blob.core.windows.net/cdn/fusion/readiness/Microsoft-Cloud-Adoption-Framework-Strategy-and-Plan-Template.docx)を使用して、動機、ビジネス成果目標、業務上の正当な理由の概要を記録します。

**成果物の完成をサポートするためのガイダンス:**

- [動機](../strategy/motivations.md):戦略的調整の第一歩は、移行作業を推進する動機について合意を得ることです。 ビジネスと IT におけるさまざまな利害関係者の動機と共通テーマを把握し、分類することから始めます。
- [ビジネス成果](../strategy/business-outcomes/index.md):動機の調整が完了すると、ビジネス成果目標を取得できるようになります。 これにより、変革全体の測定に使用できる明確なメトリックが得られます。
- [クラウド移行のビジネス ケースの作成](../strategy/cloud-migration-business-case.md):これは、業務上の正当な理由に役立つ数式やツールを明確にして、移行のビジネス ケースを作成するための出発点として適しています。

<!-- markdownlint-disable MD033 -->
<br>

| 説明責任チーム | 実行責任チームとサポート チーム | 情報チーム |
| --- | --- | --- |
| <li> クラウド戦略チーム | <li> クラウド導入チーム <li> クラウドのセンター オブ エクセレンスまたは中央 IT | <li> クラウド プラットフォーム チーム |

## <a name="step-2-assess-the-digital-estate"></a>手順 2:デジタル資産を評価する

検出して評価することにより、より深いレベルの技術的調整が実現し、戦略における実行可能な計画を作成するのに役立ちます。 この手順では、環境の現在の状態に関するデータ、そのデータの定量分析、優先順位の最も高いワークロードの詳細な定性的評価を使用して、ビジネス ケースを検証します。

デジタル資産評価を出力することで、最終状態の環境と導入計画のサポート要件をクラウド プラットフォーム チームに明確に示すことができます。

**成果物:**

- 既存インベントリの生データ。
- 業務上の正当な根拠を練り上げるための既存インベントリの定量分析。
- 最初の 10 個のワークロードの定性分析。
- [戦略と計画のテンプレート](https://archcenter.blob.core.windows.net/cdn/fusion/readiness/Microsoft-Cloud-Adoption-Framework-Strategy-and-Plan-Template.docx)内の業務上の正当な理由を更新。

**成果物の完成をサポートするためのガイダンス:**

- [既存システムのインベントリ](../digital-estate/inventory.md):プログラムを用いたデータドリブン アプローチで現在の状態を把握することが最初の手順です。 すべての評価アクティビティを可能にするデータを検索して収集します。
- [増分型の合理化](../digital-estate/rationalize.md#incremental-rationalization):評価作業を効率化して、すべての資産の定性分析に重点を置きます (ビジネス ケースをサポートする場合もあります)。 次に、移行する最初の 10 個のワークロードの詳細な定性分析を追加します。

<!-- markdownlint-disable MD033 -->
<br>

| 説明責任チーム | 実行責任チームとサポート チーム | 情報チーム |
| --- | --- | --- |
| <li> クラウド導入チーム | <li> クラウド戦略チーム | <li> クラウド プラットフォーム チーム |

## <a name="step-3-create-a-cloud-adoption-plan"></a>手順 3:クラウド導入計画を作成する

クラウド導入計画テンプレートでは、プロジェクト バックログを作成するための迅速なアプローチが提供されます。 このバックログを変更して、評価結果、合理化、スキル、パートナー契約を反映させることができます。

短期的なクラウド導入計画とバックログを確認することで、クラウド プラットフォーム チームは、今後数か月間の環境のニーズを把握できます。 これにより、最初のいくつかのランディング ゾーンの "完了の定義" を強化できます。

**成果物:**

- バックログ テンプレートをデプロイします。
- テンプレートを更新して、移行する最初の 10 個のワークロードを反映させます。
- メンバーとベロシティを更新して、リリースのタイミングを見積もります。
- タイムラインのリスク:
  - Azure DevOps を使い慣れていないと、このデプロイ プロセスが遅くなる可能性があります。
  - 各ワークロードで使用できる複雑さとデータも、タイムラインに影響を与える可能性があります。

**成果物の完成をサポートするためのガイダンス:**

- [クラウド導入計画のテンプレート](../plan/template.md):基本テンプレートをデプロイします。
- [ワークロードの調整](../plan/workloads.md):バックログにワークロードを定義します。
- [作業の調整](../plan/assets.md):バックログの資産とワークロードを調整して、優先度の高いワークロードの作業を明確に定義します。
- [メンバーと時間の調整](../plan/iteration-paths.md):移行するワークロードのイテレーション、ベロシティ (メンバーの時間)、リリースを定めます。

<!-- markdownlint-disable MD033 -->
<br>

| 説明責任チーム | 実行責任チームとサポート チーム | 情報チーム |
| --- | --- | --- |
| <li> クラウド導入チーム | <li> クラウド戦略チーム <li> クラウド プラットフォーム チーム | <li> クラウド プラットフォーム チーム |

## <a name="step-4-deploy-the-first-landing-zone"></a>手順 4:最初のランディング ゾーンをデプロイする

最初に、クラウド導入チームは、ワークロードの初期段階のウェーブの要件をサポートできるランディング ゾーンを必要とします。 時間の経過と共に、より複雑なワークロードに対応するようにランディング ゾーンが拡張されます。 ここでは、クラウド プラットフォーム チームとクラウド導入チームが初期学習できるように最初のランディング ゾーンから開始します。

**成果物:**

- 初期のリスクの低い移行用に最初のランディング ゾーンをデプロイします。
- クラウドのセンター オブ エクセレンス (CCoE) チームまたは中央 IT を使用してリファクタリングする計画を作成します。
- タイムラインのリスク:
  - 最初の 10 個のワークロードのガバナンス、運用、およびセキュリティの要件によって、このプロセスは大幅に遅くなることがあります。 最初のランディング ゾーンと後続のランディング ゾーンの実際のリファクタリングはかなり長い時間がかかりますが、移行作業と並行して行う必要があります。

**成果物の完成をサポートするためのガイダンス:**

- [ランディング ゾーンの選択](../ready/landing-zone/first-landing-zone.md):この記事を使用して、短期間の導入計画に基づいてランディング ゾーンをデプロイするための適切なアプローチを見つけます。 次に、その標準化されたコード ベースをデプロイします。
- [ランディング ゾーンを拡張する](../ready/considerations/index.md):短期的な導入計画のサポートが必要な場合を除き、この段階ではまだ、ガバナンス、セキュリティ、または運用に関する長期的な制約に対応しようとしないでください。

<!-- markdownlint-disable MD033 -->
<br>

| 説明責任チーム | 実行責任チームとサポート チーム |
| --- | --- |
| <li> クラウド プラットフォーム チーム | <li> クラウド導入チーム <li> クラウドのセンター オブ エクセレンスまたは中央 IT |

## <a name="step-5-deploy-an-initial-governance-foundation"></a>手順 5:初期のガバナンス基盤をデプロイする

ガバナンスは、移行作業を長期的に成功させるための鍵となる要素です。 移行のスピードとビジネスへの影響が重要です。 しかし、ガバナンスのないスピードは危険になる可能性があります。 組織は、導入パターンとガバナンスおよびコンプライアンスのニーズに合わせて、ガバナンスに関する意思決定を行う必要があります。

これらの決定が行われると、クラウド プラットフォーム チームの並行作業にフィードバックされます。

**成果物:**

- 初期のガバナンス基盤をデプロイします。
- 今後の改善を計画するために、ガバナンス ベンチマークを完成させます。
- タイムライン リスク: ポリシーの改善とガバナンスの実装は、1 つの規範につき 1 から 4 週間かかる場合があります。

**成果物の完成をサポートするためのガイダンス:**

- [ガバナンスのアプローチ](../govern/index.md):この方法は、企業のポリシーとプロセスについて検討するためのプロセスの概要を示します。 次に、クラウド エンタープライズ導入作業においてガバナンスを実現するために必要な規範を構築します。
- [ガバナンス ベンチマーク ツール](../govern/benchmark.md):現在の状態のギャップを見つけて、将来に向けた計画を立てることができるようにします。
- [ガバナンスを始めるための基礎](../govern/guides/complex/prescriptive-guidance.md): ガバナンス MVP の作成に必要な ID ベースラインの規範、セキュリティ ベースラインの規範、およびデプロイ高速化の規範を理解します。これらは、すべての導入の基盤となります。

<!-- markdownlint-disable MD033 -->
<br>

| 説明責任チーム | 実行責任チームとサポート チーム | 助言を行うチーム |
| --- | --- | --- |
| <li> クラウド ガバナンス チーム | <li> クラウド戦略チーム <li> クラウドのセンター オブ エクセレンスまたは中央 IT | <li> クラウド プラットフォーム チーム |

## <a name="step-6-implement-an-operations-baseline"></a>手順 6:運用ベースラインを実装する

運用管理は、移行を成功させるためのもう 1 つの要件です。 現行の運用を理解しないままクラウドに移行することは、リスクの高い決定です。 移行と並行して、長期的な運用に向けた計画を開始する必要があります。

これらの計画が作成されると、クラウド プラットフォーム チームの並行作業にフィードバックされます。

**成果物:**

- 管理ベースラインをデプロイします。
- 運用管理ブックを完成させます。
- Azure アーキテクチャ レビューの評価が必要なワークロードを特定します。
- タイムラインのリスク:
  - ブックを確認する: アプリケーション所有者ごとに 1 時間です。
  - Azure アーキテクチャ レビューの評価を完了する: アプリケーションごとに 1 時間です。

**成果物の完成をサポートするためのガイダンス:**

- [管理ベースライン](../manage/index.md)
- [ビジネス コミットメントの定義](../manage/considerations/business-alignment.md)
- [管理ベースラインの展開](../manage/best-practices.md)
- [高度な運用で特定の機能を利用する](../manage/design-principles.md)

<!-- markdownlint-disable MD033 -->
<br>

| 説明責任チーム | 実行責任チームとサポート チーム | 助言を行うチーム |
| --- | --- | --- |
| <li> クラウド運用チーム | <li> クラウド戦略チーム <li> クラウドのセンター オブ エクセレンスまたは中央 IT | <li> クラウド プラットフォーム チーム |

## <a name="step-7-expand-the-landing-zone"></a>手順 7:ランディング ゾーンの展開

クラウド導入チームが最初の数回の移行を開始すると、クラウド プラットフォーム チームは、クラウド ガバナンス チームとクラウド運用チームのサポートを得て、最終状態の環境構成に向けた構築を開始できます。 クラウド導入計画のペースに応じて、これを反復的なリリースとして行い、導入計画の要件より前に機能を追加する必要があります。

**成果物:**

- ランディング ゾーンのリファクタリングにテスト駆動開発アプローチを採用します。
- ランディング ゾーンのガバナンスを改善します。
- ランディング ゾーンの運用を拡大します。
- ランディング ゾーンのセキュリティを実装します。

**成果物の完成をサポートするためのガイダンス:**

- [ランディング ゾーンのリファクタリング](../ready/landing-zone/refactor.md)
- [ランディング ゾーン用のテスト駆動開発](../ready/considerations/test-driven-development.md)
- [ランディング ゾーンのガバナンスの改善](../ready/considerations/landing-zone-governance.md)
- [ランディング ゾーンの運用の拡大](../ready/considerations/landing-zone-operations.md)
- [ランディング ゾーンのセキュリティの向上](../ready/considerations/landing-zone-security.md)

<!-- markdownlint-disable MD033 -->
<br>

| 説明責任チーム | 実行責任チームとサポート チーム |
| --- | --- |
| <li> クラウド プラットフォーム チーム | <li> クラウド導入チーム <li> クラウドのセンター オブ エクセレンスまたは中央 IT |

## <a name="value-statement"></a>バリュー ステートメント

このガイドで説明した手順を使用すると、チームは、適切に構成されたエンタープライズ対応のクラウド環境への移行を加速させることができます。

## <a name="next-steps"></a>次のステップ

最初の作業を基にして、将来のイテレーションでは次のステップを検討してください。

- [環境 (技術) の準備のラーニング パス](../ready/suggested-skills.md)
- [移行環境の計画チェックリスト](../migrate/migration-considerations/prerequisites/planning-checklist.md)