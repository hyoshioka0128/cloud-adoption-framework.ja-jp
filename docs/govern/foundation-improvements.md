---
title: 初期のクラウド ガバナンス基盤の改善
description: Azure 向けクラウド導入フレームワークを使用して、初期のクラウド ガバナンス基盤を段階的に改善する方法を学びます。
author: BrianBlanchard
ms.author: brblanch
ms.date: 09/13/2019
ms.topic: landing-page
ms.service: cloud-adoption-framework
ms.subservice: govern
ms.custom: governance
layout: LandingPage
ms.openlocfilehash: 41535db6452be0b8efea9a91329169326c444ab0
ms.sourcegitcommit: 60d8b863d431b5d7c005f2f14488620b6c4c49be
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2020
ms.locfileid: "83220211"
---
# <a name="improve-your-initial-cloud-governance-foundation"></a>初期のクラウド ガバナンス基盤の改善

この記事では、[初期のクラウド ガバナンス基盤](./initial-foundation.md)を確立していることを前提としています。 クラウド導入計画を実装すると、チームがクラウドの導入に使用する提案されるアプローチから具体的なリスクが浮上します。 リリース計画に関する会話でこれらのリスクが表面化したら、次のグリッドを使用して、導入計画に先立っていくつかのベスト プラクティスをすばやく識別して、リスクが真の脅威になることを防ぎます。

## <a name="maturity-vectors"></a>成熟度のベクター

任意の時点で以下のベスト プラクティスを初期のガバナンス基盤に適用して、次の表で説明されているリスクまたはニーズに対応できます。

> [!IMPORTANT]
> リソース編成は、これらのベスト プラクティスの適用方法に影響を与える可能性があります。 前の手順で実装した初期のクラウド ガバナンス基盤に最もよく合致する推奨事項から開始することが重要です。

| リスク/ニーズ | 標準的な企業 | 複雑な企業 |
|---|---|---|
| クラウド内の機密データ | [規範の改良](./guides/standard/security-baseline-improvement.md) | [規範の改良](./guides/complex/security-baseline-improvement.md) |
| クラウド内のミッション クリティカルなアプリ | [規範の改良](./guides/standard/resource-consistency-improvement.md) | [規範の改良](./guides/complex/resource-consistency-improvement.md) |
| クラウドのコスト管理 | [規範の改良](./guides/standard/cost-management-improvement.md) | [規範の改良](./guides/complex/cost-management-improvement.md) |
| マルチクラウド | [規範の改良](./guides/standard/multicloud-improvement.md) | [規範の改良](./guides/complex/multicloud-improvement.md) |
| 複雑な/レガシ ID 管理 | 該当なし | [規範の改良](./guides/complex/identity-baseline-improvement.md) |
| 複数レイヤーのガバナンス | 該当なし | [規範の改良](./guides/complex/multiple-layers-of-governance.md) |

## <a name="next-steps"></a>次のステップ

ベスト プラクティスの適用に加えて、クラウド導入フレームワークのガバナンス 手法をビジネス上の固有の制約に合わせてカスタマイズすることもできます。 適切な推奨事項に従った後、[企業ポリシーを評価して追加のカスタマイズ要件を把握](./corporate-policy.md)します。

> [!div class="nextstepaction"]
> [企業ポリシーの評価](./corporate-policy.md)
