---
title: エンタープライズ規模のランディング ゾーンの使用を開始する
description: エンタープライズ規模のランディング ゾーンの使用を開始する
author: BrianBlanchard
ms.author: brblanch
ms.date: 06/15/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.openlocfilehash: d2218a84cda97c42a98327a9902009a47f8839b5
ms.sourcegitcommit: d88c1cc3597a83ab075606d040ad659ac4b33324
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/15/2020
ms.locfileid: "84792202"
---
# <a name="start-with-enterprise-scale-landing-zones"></a>エンタープライズ規模のランディング ゾーンの使用を開始する

エンタープライズ規模のアーキテクチャは、お客様の Azure 環境の戦略的設計パスとターゲットの技術状態を表します。 これは Azure プラットフォームと共に進化し続け、最終的には、Azure の取り組みを定義するために組織が行う必要があるさまざまな設計上の決定によって定義されます。

すべての企業が同じ方法で Azure を採用するわけではないため、エンタープライズ規模のアーキテクチャは顧客によって異なる可能性があります。 最終的には、エンタープライズ規模のアーキテクチャの技術的な考慮事項と設計上の推奨事項により、顧客のシナリオに基づいて異なるトレードオフが生じる可能性があります。 いくつかのバリエーションが想定されていますが、中心となる推奨事項に従うと、結果として得られるターゲット アーキテクチャによって、顧客にとって維持可能なスケールへのパスが実現します。

## <a name="prescriptive-guidance"></a>規範的なガイダンス

エンタープライズ規模のアーキテクチャでは、Azure のベスト プラクティスと組み合わせた規範的なガイダンスが提供されます。また、お客様の Azure 環境にとって重要なデザイン領域全体にわたる設計原則に従っています。

## <a name="qualifiers-should-i-start-with-enterprise-scale"></a>適合条件:エンタープライズ規模で開始する必要があるか

エンタープライズ規模のアーキテクチャは、設計によってモジュール化されており、アプリケーションが移行されるか、新しく開発されて Azure にデプロイされるかに関係なく、お客様はアプリケーション ポートフォリオをサポートする基本ランディング ゾーンから始めることができます。 アーキテクチャは、スケール ポイントに関係なく、お客様のビジネス要件に応じて拡張できます。

## <a name="start-with-an-enterprise-scale-landing-zone"></a>エンタープライズ規模のランディング ゾーンの使用を開始する

ランディング ゾーンを構築するためのエンタープライズ規模のアプローチには、クラウド チームをサポートするために、次の 4 つの資産のセットが含まれています。

- [デザインのガイドライン](./design-guidelines.md):CAF エンタープライズ規模のランディング ゾーンの設計を推進する重要な意思決定についてガイドします。
- [アーキテクチャ](./architecture.md):設計領域とベスト プラクティスを示す概念参照アーキテクチャ。
- [実装](./implementation.md):導入を促進するアーキテクチャの Azure Resource Manager テンプレート。

<!-- TODO: Reinstate once template.md is ready. 
- [Template](./template.md): A documentation template to quickly capture decisions and any deviation from the suggested architecture or implementation.
-->

## <a name="community"></a>コミュニティ

<!-- docsTest:ignore "Cloud Solutions Unit" -->

このガイドは、主に Microsoft のアーキテクト、および幅広いクラウド ソリューション ユニットのテクニカル コミュニティによって開発されています。 このコミュニティでは、このガイドを積極的に展開して、エンタープライズ規模の導入作業で学んだ教訓を共有しています。

このガイドでは、標準の Ready 方法と同じ設計原則を採用していますが、これらの原則に基づいて発展し、計画プロセスの早い段階で、ガバナンスやセキュリティなどのトピックを統合するようになっています。 標準プロセスを拡張する必要があるのは、導入作業に大規模なエンタープライズの変更が必要な場合に、いくつかの自然な仮定を行う必要があるためです。

## <a name="next-steps"></a>次のステップ

[CAF エンタープライズ規模のランディング ゾーンを実装する](./implementation.md)

> [!div class="nextstepaction"]
> [CAF エンタープライズ規模のランディング ゾーンを実装する](./implementation.md)
