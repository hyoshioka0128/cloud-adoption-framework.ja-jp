---
title: ランディング ゾーンを拡張する
description: Azure のクラウド導入フレームワークを使用して、ランディング ゾーンを拡張します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 05/15/2020
ms.topic: overview
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.openlocfilehash: a5bda49e04da5febf7bc2564f250ca4686c172cc
ms.sourcegitcommit: 011525720bd9e2d9bcf03a76f371c4fc68092c45
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88575198"
---
# <a name="expand-your-landing-zone"></a>ランディング ゾーンを拡張する

準備手法のこのセクションは、[ランディング ゾーンのリファクタリング](../landing-zone/refactor.md)の原則に基づいて作成されています。 この記事で説明されているように、コードとしてのインフラストラクチャに対するリファクタリング アプローチにより、リスクを最小限に抑えつつ、ビジネスの成功を阻害する要素が排除されます。 この一連の記事では、最初のランディング ゾーンをデプロイ済みで、次はそのランディング ゾーンをエンタープライズ要件を満たすよう拡張することを目指していることを前提としています。

## <a name="shared-architecture-principles"></a>共有アーキテクチャの原則

ランディングゾーンを拡張すると、次の原則をランディング ゾーンに埋め込み、さらに幅広くクラウド環境全体に埋め込むためのコードファースト アプローチを提供します。

![共有アーキテクチャの原則](../../_images/ready/shared-principles.png)
_図 1: 共有アーキテクチャの原則。_

これらの同じアーキテクチャ原則は、[Azure Advisor](/azure/advisor/advisor-overview)、[Microsoft Azure Well-Architected Framework](/azure/architecture/framework)、および [Azure アーキテクチャ センター](/azure/architecture)のソリューションで共有されます。

## <a name="applying-these-principles-to-your-landing-zone-improvements"></a>これらの原則をランディング ゾーンの改善点に適用する

クラウド導入フレームワークの手法との連携を強化するために、上記の原則は、実用的なランディング ゾーンの改善点別にグループ化されています。

- 基本的な考慮事項: ホスティング、基礎、その他の基本要素を改良するためにランディング ゾーンをリファクターします。
- 運用の拡張: 運用管理構成を追加して、**パフォーマンス、信頼性、オペレーショナル エクセレンス**を改善します。
- ガバナンスの拡張: ガバナンス構成を追加して、**コスト、信頼性、セキュリティ**、整合性を改善します。
- セキュリティの拡張: 機密データや重要なシステムの保護を強化するために**セキュリティ**構成を追加します。

> [!WARNING]
> (24 か月以内に) **クラウドに 1,000 を超える資産 (アプリ、インフラストラクチャ、データ資産) をホストする**中間目標を設定している導入チームは、自チームのクラウド導入体験において早期の段階でこれらの拡張について検討してください。 他のすべての導入パターンについては、ランディング ゾーンの拡張が並列イテレーションになる場合があり、早期のビジネスの成功につながります。

## <a name="next-steps"></a>次のステップ

最初のランディング ゾーンをリファクタリングする前に、[ランディング ゾーン用のテスト駆動開発](./test-driven-development.md)を理解しておくことが重要です。

> [!div class="nextstepaction"]
> [ランディング ゾーン用のテスト駆動開発](./test-driven-development.md)
