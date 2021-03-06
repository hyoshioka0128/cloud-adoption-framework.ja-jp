---
title: クラウド移行機能について理解する
description: クラウド移行機能について理解する
author: JanetCThomas
ms.author: janet
ms.service: cloud-adoption-framework
ms.subservice: organize
ms.topic: conceptual
ms.date: 05/15/2020
ms.openlocfilehash: a5b3caa04e3f87c30236425aff770e82e0a37b4c
ms.sourcegitcommit: 9a84c2dfa4c3859fd7d5b1e06bbb8549ff6967fa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/21/2020
ms.locfileid: "83755581"
---
# <a name="cloud-migration-functions"></a>クラウド移行機能

クラウド移行チームは、技術実装チームやプロジェクト チームの現代版です。 ただし、クラウドの性質上、より流動的なチーム構成が必要になる場合があります。 クラウドの移行に集中的に取り組んでいる移行チームもあれば、クラウド テクノロジを利用したイノベーションに専念している移行チームもあります。 データセンターの完全な移行など、大規模な導入作業を完了するために必要な幅広い技術に関する専門知識を含む場合もあれば、より厳密な技術的焦点を持ち、特定の目標を達成するためにプロジェクト間を移動する場合もあります。たとえば、SQL VM を SQL PaaS インスタンスに変換するのを支援するデータ プラットフォーム スペシャリストのチームです。

クラウド移行チームの種類や数に関係なく、通常、このようなチームは IT、ビジネス分析、または実装パートナーに専門知識を提供します。

## <a name="prerequisites"></a>前提条件

- [Azure アカウントの作成](https://docs.microsoft.com/learn/modules/create-an-azure-account):Azure を使用する最初の手順では、アカウントを作成します。
- [Azure ポータル](https://docs.microsoft.com/learn/modules/tour-azure-portal):Azure portal の機能とサービスのツアーを実行し、ポータルをカスタマイズします。
- [Azure 入門](https://docs.microsoft.com/learn/modules/welcome-to-azure):Azure を使ってみましょう。 クラウドに最初の仮想マシンを作成して構成します。
- [Azure の基礎](https://docs.microsoft.com/learn/paths/azure-for-the-data-engineer):クラウドの概念を学習し、利点を理解し、基本的な戦略を比較対照して、Azure で利用できる幅広いサービスを確認します。
- [移行手法](../migrate/index.md)を確認します。

## <a name="minimum-scope"></a>最小スコープ

クラウド導入のあらゆる取り組みの中核となるのがクラウド移行チームです。 このチームは、導入を可能にする技術的な変更を推進します。 このチームには、導入作業の目標に応じて、技術的なタスクやビジネス タスクに幅広く対応する多様なチーム メンバーが集められます。

少なくとも、チームのスコープには次のものが含まれます。

- [デジタル資産の合理化](../digital-estate/index.md)
- [優先順位付けされた移行バックログ](../migrate/migration-considerations/assess/release-iteration-backlog.md)の確認、検証、および昇格
- 学習機会としての[最初のワークロード](../digital-estate/rationalize.md#select-the-first-workload)の実行

## <a name="deliverable"></a>成果物

クラウド移行チームからの主な成果物は、使用可能なテクノロジ、ツール、自動化ソリューションを使用して、ガバナンス要件とビジネス成果に合わせた、導入計画に記載されている技術ソリューションのタイムリーで高品質な実装です。

### <a name="ongoing-monthly-tasks"></a>継続的な月単位のタスク

- [変更管理プロセス](../migrate/migration-considerations/prerequisites/technical-complexity.md)を監督する
- [リリース バックログとスプリント バックログ](../migrate/migration-considerations/assess/release-iteration-backlog.md)を管理する
- ガバナンス要件と共に導入ランディング ゾーンを構築、管理する
- [スプリント バックログ](../migrate/migration-considerations/assess/release-iteration-backlog.md)に記載されている技術的なタスクを完了する。

### <a name="team-cadence"></a>チームの調整

クラウド導入機能を提供するチームは、フルタイムで取り組みに専念することをお勧めします。

これらのチームが自己組織的なミーティングを毎日持つことが理想です。 毎日のミーティングの目標は、バックログを迅速に更新すること、および完了した内容、今日行うべき作業、追加の外部サポートを必要とするブロックされた内容を伝えることです。

リリース スケジュールとイテレーション期間は会社ごとに固有です。 ただし、イテレーションごとに 1 週間から 4 週間の範囲が平均期間であるようです。 イテレーションやリリースの頻度に関係なく、各リリースの終わりにチームがすべてのサポート チームとミーティングを持ち、リリースの結果を伝え、今後の取り組みを再優先順位付けすることをお勧めします。 また、[クラウド センター オブ エクセレンス](../organize/cloud-center-of-excellence.md)や [クラウド ガバナンス チーム](./cloud-governance.md)とともに、各スプリントの最後にチームとして集まり、共通の取り組みとサポートの必要性に常に対応できるようにすることも重要です。

クラウドの導入に関連する技術的なタスクの中には、繰り返し発生するものもあります。 従業員の満足度に関する問題を回避し、関連するスキルを維持するためには、チーム メンバーが 3 か月から 6 か月ごとに交代する必要があります。 [クラウド センター オブ エクセレンス](../organize/cloud-center-of-excellence.md)や[クラウド ガバナンス チーム](./cloud-governance.md)のメンバーを定期的に交代することにより、従業員を常に新鮮な気持ちに保ち、新しいイノベーションを活用することできる絶好の機会を提供できます。

## <a name="baseline-capability"></a>ベースライン機能

必要なビジネス成果に応じて、完全なクラウド導入機能を提供するために必要なスキルには、以下が含まれます。

- インフラストラクチャの実装者
- DevOps のエンジニア
- アプリケーション開発者
- データ サイエンティスト
- データまたはアプリケーション プラットフォームのスペシャ リスト

最適なコラボレーションと効率を確保するために、クラウド導入チームは平均 6 人のチームで編成することをお勧めします。 これらのチームは、技術的な実行の観点から自己組織化する必要があります。 アジャイルやスクラムといった反復モデルの豊富な経験に基づくプロジェクト管理のノウハウを含めることも強くお勧めします。 このチームは、フラットな構造で管理される場合に最も効果的です。

## <a name="out-of-scope"></a>対象範囲外

既存の IT スタッフによる追加のサポートが必要になる場合があります。 IT は、イノベーションやビジネスの機敏性のためのクラウド ブローカーやパートナーになることにより、クラウドの導入に対する価値のある貢献者になることができます。

- [中央 IT の役割](../organize/central-it.md)

## <a name="whats-next"></a>参照トピック

導入は多くの利点をもたらしますが、管理されていない導入によって予期しない結果が生じる場合があります。 [クラウド ガバナンス チーム](./cloud-governance.md)と連携し、ビジネスとテクノロジのリスクを軽減しながら、導入とベスト プラクティスを迅速に行います。

これら 2 つのチームは、クラウド導入の取り組みに関してバランスを取ることができますが、持続可能でない可能性があるため、MVP と見なされます。 ["*実行責任、説明責任、助言、通知*" (RACI) チャート](../organize/raci-alignment.md)で示されるように、各チームが多くの役割を担います。

「[組織のアンチパターン: サイロと封土](../organize/fiefdoms-silos.md)」で詳細を確認してください。
