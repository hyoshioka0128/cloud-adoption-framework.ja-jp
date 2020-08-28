---
title: 標準的な企業のガバナンス ガイド
description: ある架空の標準的な企業が、ガバナンス成熟度のさまざまな段階を通じて、ベスト プラクティスに基づいて実用最小限の製品 (MVP) を定義していきます。
author: BrianBlanchard
ms.author: brblanch
ms.date: 09/05/2019
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: govern
ms.custom: governance
ms.openlocfilehash: f5b066eed95a2fbe3ac17cb0b2e00cbdf5263a04
ms.sourcegitcommit: 07d56209d56ee199dd148dbac59671cbb57880c0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88880613"
---
# <a name="standard-enterprise-governance-guide"></a>標準的な企業のガバナンス ガイド

## <a name="overview-of-best-practices"></a>ベスト プラクティスの概要

このガバナンス ガイドは、ある架空の会社のガバナンスが成熟するさまざまな段階での経験に沿ったものです。 実際のお客様の体験に基づいています。 このベスト プラクティスは、架空の会社の制約とニーズに基づいています。

すぐに始められるように、この概要では、ベスト プラクティスに基づくガバナンスのための実用最小限の製品 (MVP) が定義されています。 また、新しいビジネスや技術的リスクが登場したときにベスト プラクティスをさらに追加するいくつかのガバナンス改善へのリンクも提供されています。

> [!WARNING]
> この MVP は、一連の想定に基づくベースラインの起点です。 この最小限の一連のベスト プラクティスも、独自のビジネス リスクとリスク許容範囲によってもたらされる企業のポリシーに基づいています。 これらの想定がご自身にも当てはまるかどうかを確認するには、この記事に続く[長文の物語](./narrative.md)をお読みください。

### <a name="governance-best-practices"></a>ガバナンスのベスト プラクティス

これらのベスト プラクティスは、お使いのサブスクリプションに対して組織が迅速かつ一貫してガバナンス ガードレールを追加するための基盤として機能します。

### <a name="resource-organization"></a>リソースの編成

次の図では、リソースを編成するためのガバナンス MVP 階層を示します。

![リソース編成の図](../../../_images/govern/resource-organization.png)

すべてのアプリケーションを管理グループ、サブスクリプション、リソース グループ階層の適切な領域にデプロイする必要があります。 デプロイ計画の間に、クラウド ガバナンス チームは、クラウド導入チームを支援するために必要なノードを階層に作成します。

1. 環境の種類 (運用、開発、テストなど) ごとに 1 つの管理グループ。
2. 2 つのサブスクリプション (運用環境ワークロード用に 1 つと非運用環境ワークロード用に 1 つ)。
3. このグループ階層の各レベルで、[一貫性のある用語体系](../../../ready/azure-best-practices/naming-and-tagging.md)を適用する必要があります。
4. リソース グループは、コンテンツのライフサイクルを考慮した方法でデプロイする必要があります。つまり、一緒に開発されたものはすべて、一緒に管理し、一緒に廃止します。 リソース グループのベスト プラクティスの詳細については、[こちらを参照](../../../decision-guides/resource-consistency/index.md)してください。
5. [リージョンの選択](../../../migrate/azure-best-practices/multiple-regions.md)は非常に重要であり、ネットワーク、監視、監査をフェールオーバー/フェールバック用に配置できるように考慮し、[必要な SKU を優先リージョンで使用できる](https://azure.microsoft.com/global-infrastructure/services)ことを確認する必要があります。

このパターンの使用例を次に示します。

![中規模の会社のリソース編成の例](../../../_images/govern/mid-market-resource-organization.png)

これらのパターンには、階層を必要以上に複雑にしないで成長に対応する余地があります。

[!INCLUDE [governance-of-resources](../../../../includes/governance-of-resources.md)]

## <a name="iterative-governance-improvements"></a>反復的なガバナンスの改善

この MVP をデプロイした後は、追加のガバナンス レイヤーを環境にすばやく組み込むことができます。 特定のビジネス ニーズに合わせて MVP を改善する方法をいくつか次に示します。

- [保護されたデータのセキュリティ ベースライン](./security-baseline-improvement.md)
- [ミッション クリティカルなアプリケーションのリソース構成](./resource-consistency-improvement.md)
- [コスト管理の制御](./cost-management-improvement.md)
- [マルチクラウドの進化の制御](./multicloud-improvement.md)

## <a name="what-does-this-guidance-provide"></a>このガイダンスで提供される内容

MVP では、企業ポリシーを迅速に適用するために、[デプロイ高速化規範](../../deployment-acceleration/index.md)のプラクティスとツールが確立されます。 具体的には、MVP では、Azure Blueprints、Azure Policy、Azure 管理グループを使用して、この架空の会社の物語で定義されているいくつかの基本的な企業ポリシーが適用されます。 それらの企業ポリシーが Resource Manager テンプレートと Azure ポリシーを使用して適用されて、ID とセキュリティのための小さいベースラインが確立されます。

![増分ガバナンス MVP の例](../../../_images/govern/governance-mvp.png)

## <a name="incremental-improvement-of-governance-practices"></a>ガバナンス プラクティスの段階的な改善

このガバナンス MVP は、時間をかけたガバナンス プラクティスの改善に使用されます。 導入が進むと、ビジネス上のリスクが増大します。 それらのリスクを管理するため、クラウド導入フレームワーク ガバナンス モデル内のさまざまな規範が変化します。 このシリーズの以降の記事では、架空の企業に影響を与える企業ポリシーの段階的な改善について説明します。 これらの改善は、3 つの規範で発生します。

- コスト管理: 導入が拡大するとき。
- セキュリティ ベースライン: 保護されたデータがデプロイされるとき。
- リソースの整合性: IT 運用でミッション クリティカルなワークロードのサポートが始まるとき。

![増分ガバナンス MVP の例](../../../_images/govern/governance-improvement.png)

## <a name="next-steps"></a>次のステップ

ガバナンス MVP について理解し、この後のガバナンスの改善についてわかったら、コンテキストについてさらに理解するのに役立つ物語を読んでください。

> [!div class="nextstepaction"]
> [理解に役立つ物語を読む](./narrative.md)
