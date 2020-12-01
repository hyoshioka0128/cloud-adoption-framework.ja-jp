---
title: ランディング ゾーンのガバナンスの改善
description: ランディング ゾーンのガバナンスを改善します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 05/15/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.openlocfilehash: f841854558e297c61645c518d1d42cf415b3124b
ms.sourcegitcommit: 57b757759b676a22f13311640b8856557df36581
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/20/2020
ms.locfileid: "94997252"
---
# <a name="improve-landing-zone-governance"></a>ランディング ゾーンのガバナンスの改善

ランディング ゾーンのガバナンスは、ガバナンス全体の最小単位です。 最初のいくつかのランディング ゾーン内に安定したガバナンスの基礎を確立することで、導入ライフサイクルの後半で必要となるリファクタリングの量を減らすことができます。 ランディング ゾーンのガバナンスを改善することで、コスト管理が統合され、スケーリングを可能にする基本的なツールが確立され、クラウド ガバナンス チームがクラウド ガバナンスの 5 つの原則に簡単に対応できるようになります。

## <a name="landing-zone-governance-best-practices"></a>ランディング ゾーンのガバナンスのベスト プラクティス

- **ランディング ゾーンの初期ガバナンス:** [初期のガバナンス基盤](../../govern/guides/complex/index.md)の確立に関する記事は、最初のいくつかのランディング ゾーンに初期ガバナンス ツールを追加する際に役立ちます。 これらのプラクティスは、導入とガバナンスのスケーリングに役立ち、安定したコスト管理の実装にも役立ちます。 このアプローチは、リソース編成、ポリシー定義、RBAC ロール、ブループリント定義から始まります。
- **[名前付けとタグ付けの標準](../azure-best-practices/naming-and-tagging.md):** 名前付けとタグ付けの一貫性を確保します。これは、安定したガバナンス プラクティスを確立するための基本データです。
- **[ワークロード間にわたってコストを追跡する](../azure-best-practices/track-costs.md):** 最初のランディング ゾーンのコストの追跡を始めます。 複数のワークロードとロール間で一貫性を適用する方法を評価します。
- **[複数のサブスクリプションでスケーリングする](../azure-best-practices/scale-subscriptions.md):** 複数のサブスクリプションが要件になるため、このランディング ゾーンとその他のランディング ゾーンのスケーリング方法を評価します。
- **[サブスクリプションを整理する](../azure-best-practices/organize-subscriptions.md):** 複数のサブスクリプションの整理と管理の方法を理解します。

## <a name="four-steps-to-improve-overall-governance"></a>全体的なガバナンスを向上させるための 4 つのステップ

[管理手法](../../govern/index.md)は、ガバナンスのポリシー、プロセス、および規範を構築するための全体的なガイダンスを提供します。 この手法の基本構造と、その手法の次のステップを使用して、ランディング ゾーンのガバナンスとすべてのランディング ゾーンでのガバナンスを改善します。

1. [手法を理解する](../../govern/methodology.md): 最終状態のガバナンス設計をガイドする基本的な手法を理解します。
2. [ベンチマーク](../../govern/benchmark.md): 現在と将来の両方の状態を評価して、ビジョンを確立し、行動を取ります。
3. [ガバナンスを始めるための基礎](../../govern/initial-foundation.md): すべてのランディング ゾーンの初期基盤を確立するための、小規模で簡単に実装されたガバナンス ツールのセット。
4. [ガバナンス基盤の改善](../../govern/foundation-improvements.md): すべてのランディング ゾーンのガバナンスを強化するために、ガバナンス制御を繰り返し追加します。

## <a name="next-steps"></a>次のステップ

クラウドの導入は、新しいワークロードの各ウェーブまたはリリースで引き続き展開されます。 これらの要件を事前に把握しておくために、クラウド プラットフォーム チームには、定期的に追加のランディング ゾーンのベスト プラクティスを確認することをお勧めします。

> [!div class="nextstepaction"]
> [追加のランディング ゾーンのベスト プラクティスを確認する](../azure-best-practices/index.md)
