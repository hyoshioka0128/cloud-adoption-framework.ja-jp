---
title: 複雑な企業向けのガバナンス ガイド
titleSuffix: Microsoft Cloud Adoption Framework for Azure
description: 複雑な企業向けのガバナンス ガイド
author: BrianBlanchard
ms.author: brblanch
ms.date: 09/05/2019
ms.topic: guide
ms.service: cloud-adoption-framework
ms.subservice: govern
ms.custom: governance
ms.openlocfilehash: 63b66858c023ff85e1ff6f8adc811540f3034e2d
ms.sourcegitcommit: 443c28f3afeedfbfe8b9980875a54afdbebd83a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/16/2019
ms.locfileid: "71025795"
---
# <a name="governance-guide-for-complex-enterprises"></a>複雑な企業向けのガバナンス ガイド

## <a name="overview-of-best-practices"></a>ベスト プラクティスの概要

このガバナンス ガイドは、ある架空の会社のガバナンスが成熟するさまざまな段階での経験に沿ったものです。 実際のお客様の体験に基づいています。 推奨されるベスト プラクティスは、架空の会社の制約とニーズに基づいています。

すぐに始められるように、この概要では、ベスト プラクティスに基づくガバナンスのための実用最小限の製品 (MVP) が定義されています。 また、新しいビジネスや技術的リスクが登場したときにベスト プラクティスをさらに追加するいくつかのガバナンス改善へのリンクも提供されています。

> [!WARNING]
> この MVP は、一連の想定に基づくベースラインの起点です。 この最小限の一連のベスト プラクティスも、独自のビジネス リスクとリスク許容範囲によってもたらされる企業のポリシーに基づいています。 これらの想定が自分にも当てはまるかどうかを確認するには、この記事の後にある[長い物語](./narrative.md)を読んでください。

### <a name="governance-best-practices"></a>ガバナンスのベスト プラクティス

これらのベスト プラクティスは、組織が複数の Azure サブスクリプションに対して迅速かつ一貫してガバナンス ガードレールを追加するための基盤として機能します。

### <a name="resource-organization"></a>リソースの編成

次の図では、リソースを編成するためのガバナンス MVP 階層を示します。

![リソース編成の図](../../../_images/govern/resource-organization.png)

すべてのアプリケーションを管理グループ、サブスクリプション、リソース グループ階層の適切な領域にデプロイする必要があります。 デプロイ計画の間に、クラウド ガバナンス チームは、クラウド導入チームを支援するために必要なノードを階層に作成します。

1. 地理的な場所そして環境の種類 (運用や非運用など) が反映された詳細な階層を持つ各部署の管理グループを定義します。
1. 部署、地理的な場所、環境、および "アプリケーション分類" の一意の組み合わせごとにサブスクリプションを作成します。
1. 各アプリケーションに個別のリソース グループを作成します。
1. このグループ階層の各レベルで、[一貫性のある用語体系](../../../ready/considerations/naming-and-tagging.md)を適用します。

![大企業のリソース編成の図](../../../_images/govern/large-enterprise-resource-organization.png)

これらのパターンには、階層を必要以上に複雑にしないで成長に対応する余地があります。

[!INCLUDE [governance-of-resources](../../../../includes/caf-governance-of-resources.md)]

<!-- See comments for suggestion to possibly add here -->

## <a name="incremental-governance-improvements"></a>段階的なガバナンスの改善

この MVP をデプロイした後は、追加のガバナンス レイヤーを環境にすばやく組み込むことができます。 特定のビジネス ニーズに合わせて MVP を改善する方法をいくつか次に示します。

- [保護されたデータのセキュリティ ベースライン](./security-baseline-improvement.md)
- [ミッション クリティカルなアプリケーションのリソース構成](./resource-consistency-improvement.md)
- [コスト管理の制御](./cost-management-improvement.md)
- [マルチクラウドの段階的な改善のためのコントロール](./multicloud-improvement.md)

<!-- markdownlint-disable MD026 -->

## <a name="what-does-this-guidance-provide"></a>このガイダンスで提供される内容

MVP では、企業ポリシーを迅速に適用するために、[デプロイ高速化](../../deployment-acceleration/index.md)規範のプラクティスとツールが確立されます。 具体的には、MVP では、Azure Blueprints、Azure Policy、Azure 管理グループを使用して、この架空の会社の物語で定義されているいくつかの基本的な企業ポリシーが適用されます。 それらの企業ポリシーが Azure Resource Manager テンプレートと Azure ポリシーを使用して適用されて、ID とセキュリティのための小さいベースラインが確立されます。

![増分ガバナンス MVP の例](../../../_images/govern/governance-mvp.png)

## <a name="incremental-improvements-to-governance-practices"></a>ガバナンス プラクティスの段階的な改善

このガバナンス MVP は、時間をかけたガバナンス プラクティスの段階的な改善に使用されます。 導入が進むと、ビジネス上のリスクが増大します。 それらのリスクを管理するため、クラウド導入フレームワーク ガバナンス モデル内のさまざまな規範が変化します。 このシリーズの以降の記事では、架空の企業に影響を与える企業ポリシーの変化について説明します。 これらの変化は、4 つの分野で発生します。

- ID ベースライン: 物語で移行の依存関係が変化するとき。
- コスト管理: 導入が拡大するとき。
- セキュリティ ベースライン: 保護されたデータがデプロイされるとき。
- リソースの整合性: IT 運用でミッション クリティカルなワークロードのサポートが始まるとき。

![増分ガバナンス MVP の例](../../../_images/govern/governance-improvement-large.png)

## <a name="next-steps"></a>次の手順

ガバナンス MVP および今後予想されるガバナンスの変化について確認できたので、コンテキストについてさらに理解するのに役立つ物語を読んでください。

> [!div class="nextstepaction"]
> [理解に役立つ物語を読む](./narrative.md)
