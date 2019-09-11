---
title: 初期のクラウド ガバナンス基盤の改善
titleSuffix: Microsoft Cloud Adoption Framework for Azure
description: 初期のクラウド ガバナンス基盤を段階的に改善する方法を説明します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 01/03/2019
ms.topic: landing-page
ms.service: cloud-adoption-framework
ms.subservice: govern
ms.custom: governance
layout: LandingPage
ms.openlocfilehash: e019e99cd6af2047e040ae02c1899ce21c222545
ms.sourcegitcommit: 5846ed4d0bf1b6440f5e87bc34ef31ec8b40b338
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/11/2019
ms.locfileid: "70906065"
---
# <a name="improve-your-initial-cloud-governance-foundation"></a>初期のクラウド ガバナンス基盤の改善

この記事では、[初期のクラウド ガバナンス基盤](./getting-started.md)を確立していることを前提としています。 クラウド導入計画を実装すると、チームがクラウドの導入に使用する提案されるアプローチから具体的なリスクが浮上します。 リリース計画に関する会話でこれらのリスクが表面化したら、次のグリッドを使用して、導入計画に先立っていくつかの推奨プラクティスをすばやく識別して、リスクが真の脅威になることを防ぎます。

## <a name="maturity-vectors"></a>成熟度のベクター

任意の時点で以下の規範的なガイダンスを初期のガバナンス基盤に適用して、次の表で説明されているリスクまたはニーズに対応できます。

> [!IMPORTANT]
> リソース編成は、この規範的なガイダンスの適用方法に影響を与える可能性があります。 前の手順で実装した初期のクラウド ガバナンス基盤に最もよく合致する推奨事項から開始することが重要です。

|リスク/ニーズ | 中小企業 | 大企業 |
|---|---|---|
|クラウド内の機密データ|[規範的なガイダンス](./journeys/standard-enterprise/security-baseline-evolution.md)|[規範的なガイダンス](./journeys/complex-enterprise/security-baseline-evolution.md)|
|クラウド内のミッション クリティカルなアプリ|[規範的なガイダンス](./journeys/standard-enterprise/resource-consistency-evolution.md)|[規範的なガイダンス](./journeys/complex-enterprise/resource-consistency-evolution.md)|
|クラウドのコスト管理|[規範的なガイダンス](./journeys/standard-enterprise/cost-management-evolution.md)|[規範的なガイダンス](./journeys/complex-enterprise/cost-management-evolution.md)|
|マルチクラウド|[規範的なガイダンス](./journeys/standard-enterprise/multicloud-evolution.md)|[規範的なガイダンス](./journeys/complex-enterprise/multicloud-evolution.md)|
|複雑な/レガシ ID 管理|         |[規範的なガイダンス](./journeys/complex-enterprise/identity-baseline-evolution.md)|
|複数レイヤーのガバナンス|         |[規範的なガイダンス](./journeys/complex-enterprise/multiple-layers-of-governance.md)|

## <a name="next-steps"></a>次の手順

規範的なガイダンスの適用に加えて、クラウド導入フレームワーク内のガバナンス方法論は、ビジネス上の固有の制約に合わせてカスタマイズ可能です。 適切な推奨事項に従った後、[企業ポリシーを評価して追加のカスタマイズ要件を把握](./corporate-policy.md)します。

> [!div class="nextstepaction"]
> [企業ポリシーの評価](./corporate-policy.md)
