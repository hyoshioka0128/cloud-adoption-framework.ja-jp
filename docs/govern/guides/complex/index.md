---
title: 複雑な企業向けのガバナンス ガイド
description: ある架空の複雑な企業が、ガバナンス成熟度のさまざまな段階を通じて、ベスト プラクティスに基づいて実用最小限の製品 (MVP) を定義していきます。
author: BrianBlanchard
ms.author: brblanch
ms.date: 10/17/2019
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: govern
ms.custom: internal
ms.openlocfilehash: 16a45aa4eceb2f16cb9e8e82ba9e4d7a99976ca9
ms.sourcegitcommit: b8f8b7631aabaab28e9705934bf67dad15e3a179
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101789492"
---
# <a name="governance-guide-for-complex-enterprises"></a>複雑な企業向けのガバナンス ガイド

## <a name="overview-of-best-practices"></a>ベスト プラクティスの概要

このガバナンス ガイドは、ある架空の会社のガバナンスが成熟するさまざまな段階での経験に沿ったものです。 実際のお客様の体験に基づいています。 推奨されるベスト プラクティスは、架空の会社の制約とニーズに基づいています。

すぐに始められるように、この概要では、ベスト プラクティスに基づくガバナンスのための実用最小限の製品 (MVP) が定義されています。 また、新しいビジネスや技術的リスクが登場したときにベスト プラクティスをさらに追加するいくつかのガバナンス改善へのリンクも提供されています。

> [!WARNING]
> この MVP は、一連の想定に基づくベースラインの起点です。 この最小限の一連のベスト プラクティスも、独自のビジネス リスクとリスク許容範囲によってもたらされる企業のポリシーに基づいています。 これらの想定がご自身にも当てはまるかどうかを確認するには、この記事に続く[長文の物語](./narrative.md)をお読みください。

### <a name="governance-best-practices"></a>ガバナンスのベスト プラクティス

これらのベスト プラクティスは、組織が複数の Azure サブスクリプションに対して迅速かつ一貫してガバナンス ガードレールを追加するための基盤として機能します。

### <a name="resource-organization"></a>リソースの編成

次の図では、リソースを編成するためのガバナンス MVP 階層を示します。

![リソース編成の図](../../../_images/govern/resource-organization.png)

すべてのアプリケーションを管理グループ、サブスクリプション、リソース グループ階層の適切な領域にデプロイする必要があります。 デプロイ計画の間に、クラウド ガバナンス チームは、クラウド導入チームを支援するために必要なノードを階層に作成します。

1. 最初に地理的な場所、次に環境の種類 (運用環境や非運用環境など) が反映された詳細な階層を持つ各部署の管理グループを定義します。

1. 個別の部署または地域の一意の組み合わせごとに、運用のサブスクリプションと非運用のサブスクリプションを作成します。 複数のサブスクリプションを作成するには、慎重な検討が必要です。 詳細については、「[サブスクリプション決定ガイド](../../../decision-guides/subscriptions/index.md)」を参照してください。

1. このグループ階層の各レベルで、[一貫性のある用語体系](../../../ready/azure-best-practices/naming-and-tagging.md)を適用します。

1. リソース グループは、そのコンテンツのライフサイクルを考慮した形でデプロイする必要があります。 同時に開発され、まとめて管理され、同時に廃止されるリソースは、同じリソース グループに属します。 リソース グループの使用に関するベスト プラクティスの詳細については、「[リソースの整合性の意思決定ガイド](../../../decision-guides/resource-consistency/index.md)」を参照してください。

1. [リージョンの選択](../../../migrate/azure-best-practices/multiple-regions.md)は非常に重要であり、ネットワーク、監視、監査をフェールオーバー/フェールバック用に配置できるように考慮し、[必要な SKU を優先リージョンで使用できる](https://azure.microsoft.com/global-infrastructure/services/)ことを確認する必要があります。

![大企業のリソース編成の図](../../../_images/govern/large-enterprise-resource-organization.png)

これらのパターンには、階層を必要以上に複雑にしないで成長させる余地があります。

[!INCLUDE [governance-of-resources](../../../../includes/governance-of-resources.md)]

<!-- TODO: See comments for suggestion to possibly add here -->

## <a name="incremental-governance-improvements"></a>段階的なガバナンスの改善

この MVP をデプロイした後は、追加のガバナンス レイヤーを環境にすばやく組み込むことができます。 特定のビジネス ニーズに合わせて MVP を改善する方法をいくつか次に示します。

- [保護されたデータのセキュリティ ベースライン](./security-baseline-improvement.md)
- [ミッション クリティカルなアプリケーションのリソース構成](./resource-consistency-improvement.md)
- [コスト管理のコントロール](./cost-management-improvement.md)
- [マルチクラウドの段階的な改善のためのコントロール](./multicloud-improvement.md)

## <a name="what-does-this-guidance-provide"></a>このガイダンスで提供される内容

MVP では、企業ポリシーを迅速に適用するために、[デプロイ高速化規範](../../deployment-acceleration/index.md)のプラクティスとツールが確立されます。 具体的には、MVP では、Azure Blueprints、Azure Policy、Azure 管理グループを使用して、この架空の会社の物語で定義されているいくつかの基本的な企業ポリシーが適用されます。 それらの企業ポリシーが Azure Resource Manager テンプレートと Azure ポリシーを使用して適用されて、ID とセキュリティのための小さいベースラインが確立されます。

![増分ガバナンス MVP の例を示す図。](../../../_images/govern/governance-mvp.png)

## <a name="incremental-improvements-to-governance-practices"></a>ガバナンス プラクティスの段階的な改善

このガバナンス MVP は、時間をかけたガバナンス プラクティスの段階的な改善に使用されます。 導入が進むと、ビジネス上のリスクが増大します。 それらのリスクを管理するため、クラウド導入フレームワーク ガバナンス モデル内のさまざまな規範が変化します。 このシリーズの以降の記事では、架空の企業に影響を与える企業ポリシーの変化について説明します。 これらの変化は、4 つの分野で発生します。

- ID ベースライン規範: 物語で移行の依存関係が変化するとき。
- Cost Management 規範: 導入がスケーリングするとき。
- セキュリティ ベースライン規範: 保護されたデータがデプロイされるとき。
- リソースの整合性規範: IT 運用でミッション クリティカルなワークロードのサポートが始まるとき。

![ガバナンス プラクティスの段階的な改善の例を示す図。](../../../_images/govern/governance-improvement-large.png)

## <a name="next-steps"></a>次のステップ

ガバナンス MVP および今後予想されるガバナンスの変化について確認できたので、コンテキストについてさらに理解するのに役立つ物語を読んでください。

> [!div class="nextstepaction"]
> [理解に役立つ物語を読む](./narrative.md)
