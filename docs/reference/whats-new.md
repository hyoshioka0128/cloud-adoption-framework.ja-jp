---
title: 新機能
description: Azure 向けの Microsoft クラウド導入フレームワークの最新情報について取り上げます。
author: BrianBlanchard
ms.author: brblanch
ms.date: 03/02/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: reference
ms.openlocfilehash: a3e316d49acaaee00d5ecd10efac9aa15c2dbd49
ms.sourcegitcommit: ea63be7fa94a75335223bd84d065ad3ea1d54fdb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "80353674"
---
# <a name="whats-new-in-the-microsoft-cloud-adoption-framework"></a>Microsoft クラウド導入フレームワークの最新情報

## <a name="fulfilling-the-vision"></a>ビジョンの実現

フレームワークの一般提供 (GA) が開始されました。 ただし、お客様、パートナー様、および社内チームのコラボレーションにより、このフレームワークの構築に今もなお積極的に取り組んでいます。 パートナーシップの促進のため、コンテンツは利用可能になった時点で公開されます。 こうした公開リリースにより、ガイダンスをテストして検証し、段階的に改善していくことができます。

重要な変更点については、以下のリリース ノートにまとめています。

## <a name="migration-update-03022020"></a>移行の更新: 2020 年 3 月 2 日

このリリースは、移行アプローチの継続性に関するフィードバックに対し、戦略、計画、準備、移行を含むさまざまなメソドロジで応えたものです。 このリリースは、お客様が移行への取り組みを進める中で、計画と導入のたゆみない改善を読者にわかりやすく示すことを目指しています。 このリリースの基本理念を満たすために、次の変更が行われました。

### <a name="new-articles"></a>新しい記事

- [競合する優先順位のバランスを取る](../strategy/balance-competing-priorities.md): お客様の戦略を伝えるための各メソドロジで生じる優先順位のバランスについて概説しています。
- [評価プロセス中の分類](../migrate/migration-considerations/assess/classify.md): すべての資産とワークロードを移行前に分類しておくことの重要性を概説しています。

### <a name="restructure-landing-zone-process"></a>ランディング ゾーン プロセスの再構築

最初のランディング ゾーンは、準備ガイドから抜粋され、それ自体がセクションとしての役割を果たします。 このアプローチによって、ランディング ゾーンとそのオプションの概念を拡張することができます。 それに伴い、新しい記事もいくつか作成されています。

- [ランディング ゾーンとは](../ready/landing-zone/index.md): "ランディング ゾーン" という用語の定義について取り上げます。
- [最初のランディング ゾーン](../ready/landing-zone/first-landing-zone.md): 各種ランディング ゾーンの比較について詳しく取り上げます。
- [移行ランディング ゾーン](../ready/landing-zone/migrate-landing-zone.md): ランディング ゾーンの選択肢を増やせるよう、以前のランディング ゾーン ブループリント記事を移動して、最初のランディング ゾーンの選択から CAF ブループリントの定義を分離しました。
- [Terraform ランディング ゾーン](../ready/landing-zone/terraform-landing-zone.md)に関する記事: ランディング ゾーンのトピックの中で Terraform を最優先で扱うために、準備メソドロジの「拡大された範囲」セクションを準備メソドロジの新しい「ランディング ゾーン」セクションに移動しました。
- ランディング ゾーンに関する基本的な考慮事項を目次の「基本的なランディング ゾーンに関する考慮事項」にまとめました。
- 移行メソドロジのセキュリティのベスト プラクティスを、「ランディング ゾーンのセキュリティの向上 (機密データ)」という新しいランディング ゾーン セクションに移動しました。このガイダンスが、ライフサイクルのより早い段階で読者の目に触れるようにすることがその目的です。

### <a name="refinements-to-the-azure-migration-guide"></a>Azure 移行ガイドの強化点

ユーザー トラフィック パターンを見ると、このセクションのコンテンツは、導入担当者とアーキテクトによって、その最初の移行時に用いられる可能性が高いと考えられます。 こうした利用者を的確に支援するために、最初の移行時に使用されるツールを読者に案内するという当初の意図に合わせて移行ガイドの目的を調整しました。

- [概要](../migrate/azure-migration-guide/index.md):ガイドの手順数を減らすと共に、説明をよりわかりやすくしました。
- [評価](../migrate/azure-migration-guide/assess.md): 特定のワークロードとその関連資産の技術的な適合性を評価することがこのフェーズにおける評価のねらいであることを、わかりやすく説明しています。 このレベルの評価が、計画メソドロジで最初に言及されている段階的評価アプローチでどのような働きをするかを示すために、「前提条件の吟味」セクションを追加しました。
- [移行](../migrate/azure-migration-guide/migrate.md):ティア 1 カンファレンスでのフィードバックに応えるために、サードパーティ ツール オプションに UnifyCloud のリファレンスが追加されました。
- [テスト、最適化および昇格](../migrate/azure-migration-guide/optimize-and-transform.md): この記事のタイトルを、プロセス改善に関する他の提案に合わせて調整しました。

### <a name="refinements-to-migration-process-improvements"></a>移行プロセス改善に対する調整

- [評価の概要](../migrate/migration-considerations/assess/index.md): 特定のワークロードとその関連資産の技術的な適合性を評価することがこのフェーズにおける評価のねらいであることを、わかりやすく説明しています。
- [計画チェックリスト](../migrate/migration-considerations/prerequisites/planning-checklist.md): 移行後のワークロードの管理を徹底するために、移行作業に向けた計画段階で運用の整合を取っておくことの重要性を明らかにします。

### <a name="placement-of-related-articles"></a>関連する記事の配置

以下の各記事は移動されました。 トラフィックは別の場所にリダイレクトされます。

- [評価のベスト プラクティス](../plan/contoso-migration-assessment.md)に関する記事: 移行メソドロジの「ベスト プラクティス」セクションは、計画メソドロジの新しい「ベスト プラクティス」セクションに移動されました。 この移動によって、ローカル環境の評価というプラクティスが、ライフサイクルのより早い段階で読者の目に触れることになります。
- [ポートフォリオのバランス](../strategy/balance-the-portfolio.md)に関する記事: 移行メソドロジの「拡大された範囲」セクションは、戦略メソドロジに移動されました。 この移動によって、この思考プロセスが、ライフサイクルのより早い段階で読者の目に触れることになります。

### <a name="alignment-of-the-changes"></a>変更に合わせた調整

- [準備の概要](../ready/index.md): 改善されたプロセスに合わせて、「準備の概要」の 4 つの手順を更新しました。
- [移行の概要](../migrate/index.md): 改善されたプロセスに合わせて、「移行の概要」の手順を更新しました。
- [移行手法のグラフィック](../migrate/index.md): 変更点を反映するために手法の画像を更新しました。
- [概要のグラフィック](../index.md): 変更点を反映するために概要のグラフィックを更新しました。
