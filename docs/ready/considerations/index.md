---
title: ランディング ゾーンを拡張する
description: Azure のクラウド導入フレームワークを使用して、ランディング ゾーンを拡張する方法を学習する
author: BrianBlanchard
ms.author: brblanch
ms.date: 04/04/2020
ms.topic: overview
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.openlocfilehash: cbfbadebd635891d680da29091ca572611712e09
ms.sourcegitcommit: 7d3fc1e407cd18c4fc7c4964a77885907a9b85c0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2020
ms.locfileid: "81120798"
---
# <a name="expand-your-landing-zone"></a>ランディング ゾーンを拡張する

準備手法のこのセクションは、[ランディング ゾーンのリファクタリング](../landing-zone/refactor.md)の原則に基づいて作成されています。 この記事で説明されているように、コードとしてのインフラストラクチャに対するリファクタリング アプローチにより、リスクを最小限に抑えつつ、ビジネスの成功を阻害する要素が排除されます。 この一連の記事では、最初のランディング ゾーンをデプロイ済みで、次はそのランディング ゾーンをエンタープライズ要件を満たすよう拡張することを目指していることを前提としています。

## <a name="shared-architecture-principles"></a>共有アーキテクチャの原則

ランディングゾーンを拡張すると、次の原則をランディング ゾーンに埋め込み、さらに幅広くクラウド環境全体に埋め込むためのコードファースト アプローチを提供します。

![共有アーキテクチャの原則](../../_images/ready/shared-principles.png)

これらの同じアーキテクチャ原則は、[Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-overview)、[Azure Architecture Framework](https://docs.microsoft.com/azure/architecture/framework)、および [Azure アーキテクチャ センター](https://docs.microsoft.com/azure/architecture)のソリューションで共有されます。

## <a name="applying-these-principles-to-your-landing-zone-improvements"></a>これらの原則をランディング ゾーンの改善点に適用する

クラウド導入フレームワークの手法との連携を強化するために、上記の原則は、実用的なランディング ゾーンの改善点別にグループ化されています。

- 基本的な考慮事項:ホスティング、基礎、その他の基本要素を改良するためにランディング ゾーンをリファクターします。
- 運用の拡張:運用管理構成を追加して、**パフォーマンス、信頼性、オペレーショナル エクセレンス**を改善します。
- ガバナンスの拡張:ガバナンス構成を追加して、**コスト、信頼性、セキュリティ**、整合性を改善します。
- セキュリティの拡張:機密データや重要なシステムの保護を強化するために**セキュリティ**構成を追加します。

> [!WARNING]
> (24 か月以内に) **クラウドに 1,000 以上の資産 (アプリ、インフラストラクチャ、データ資産) をホストする**中間目標を設定している導入チームは、自チームのクラウド導入体験において早期の段階でこれらの拡張について検討してください。 他のすべての導入パターンについては、ランディング ゾーンの拡張が並列イテレーションになる場合があり、早期のビジネスの成功につながります。

## <a name="next-steps"></a>次のステップ

最初のランディング ゾーンをリファクタリングする前に、[ランディング ゾーン用のテスト駆動開発](./test-driven-development.md)を理解しておくことが重要です。

> [!div class="nextstepaction"]
> [ランディング ゾーン用のテスト駆動開発](./test-driven-development.md)
