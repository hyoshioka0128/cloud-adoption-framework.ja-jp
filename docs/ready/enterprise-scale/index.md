---
title: クラウド導入フレームワークのエンタープライズ規模ランディング ゾーンから開始する
description: Azure 向けの Microsoft クラウド導入フレームワークを使用して、エンタープライズ規模のランディング ゾーンから開始します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 06/15/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.openlocfilehash: 34c8ca965ff323595b7ac0f38814168b646b3ed3
ms.sourcegitcommit: d31a9043d1ae9283ed126bf118ca26d1d18d6948
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/10/2020
ms.locfileid: "88040815"
---
# <a name="start-with-cloud-adoption-framework-enterprise-scale-landing-zones"></a>クラウド導入フレームワークのエンタープライズ規模ランディング ゾーンから開始する

エンタープライズ規模のアーキテクチャは、ご利用の Azure 環境の戦略的設計パスとターゲットの技術状態を表します。 これは Azure プラットフォームと共に進化し続け、Azure の導入を計画するために組織が行う必要があるさまざまな設計上の決定によって定義されます。

すべての企業が同じ方法で Azure を導入するわけではないため、Azure のクラウド導入フレームワークにおけるエンタープライズ規模のランディング ゾーン アーキテクチャはお客様によって異なります。 エンタープライズ規模のアーキテクチャの技術的な考慮事項と設計上の推奨事項により、組織のシナリオに基づいて異なるトレードオフが生じる可能性があります。 いくつかのバリエーションが想定されていますが、中心となる推奨事項に従うと、結果として得られるターゲット アーキテクチャにより、組織にとって維持可能なスケールへのパスが実現します。

## <a name="prescriptive-guidance"></a>規範的なガイダンス

エンタープライズ規模のアーキテクチャは、Azure のベスト プラクティスと組み合わせた規範的なガイダンスを提供します。 組織の Azure 環境の重要なデザイン領域全体にわたる設計原則に従っています。

## <a name="qualifiers-should-i-start-with-enterprise-scale"></a>適合条件:エンタープライズ規模で開始する必要があるか

エンタープライズ規模のアーキテクチャは、設計によってモジュール化されています。 アプリケーションが移行されるか、新しく開発されて Azure にデプロイされるかに関係なく、アプリケーション ポートフォリオをサポートする基本ランディング ゾーンから始めることができます。 このアーキテクチャは、スケール ポイントに関係なく、ビジネス要件に応じてスケーリングできます。

## <a name="start-with-a-cloud-adoption-framework-enterprise-scale-landing-zone"></a>クラウド導入フレームワークのエンタープライズ規模ランディング ゾーンから開始する

エンタープライズ規模でランディング ゾーンを構築するアプローチには、クラウド チームをサポートするために次の 3 つの資産のセットが含まれています。

- [デザインのガイドライン](./design-guidelines.md):Azure のクラウド導入フレームワークにおけるエンタープライズ規模のランディング ゾーンの設計を推進する重要な意思決定についてガイドします。
- [アーキテクチャ](./architecture.md):設計領域とベスト プラクティスを示す概念参照アーキテクチャ。
- [実装](./implementation.md):導入を促進するアーキテクチャの Azure Resource Manager テンプレート。

<!-- TODO: Reinstate once template.md is ready.
- [Template](./template.md): A documentation template to quickly capture decisions and any deviation from the suggested architecture or implementation.
-->

## <a name="community"></a>コミュニティ

<!-- docsTest:ignore "Cloud Solutions Unit" -->

このガイドは、主に Microsoft のアーキテクト、および幅広いクラウド ソリューション ユニットのテクニカル コミュニティによって開発されています。 このコミュニティでは、このガイドを積極的に展開して、エンタープライズ規模の導入作業で学んだ教訓を共有しています。

このガイドでは、標準の準備方法と同じ設計原則を採用しています。 これらの原則を拡張して、ガバナンスやセキュリティなどの項目を計画プロセスの早い段階で統合します。 標準プロセスを拡張する必要があるのは、導入作業に大規模なエンタープライズの変更が必要な場合に、いくつかの自然な仮定を行う必要があるためです。

## <a name="next-steps"></a>次のステップ

[クラウド導入フレームワークのエンタープライズ規模ランディング ゾーンを実装する](./implementation.md)
