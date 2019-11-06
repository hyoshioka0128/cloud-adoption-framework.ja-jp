---
title: 初期のクラウド ガバナンス基盤の確立
titleSuffix: Microsoft Cloud Adoption Framework for Azure
description: 初期のクラウド ガバナンス基盤を確立して、クラウド ガバナンスを開始します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 09/05/2019
ms.topic: landing-page
ms.service: cloud-adoption-framework
ms.subservice: govern
ms.custom: governance
layout: LandingPage
ms.openlocfilehash: aae20779a4009692ebf52602341d81939dee01ea
ms.sourcegitcommit: 57390e3a6f7cd7a507ddd1906e866455fa998d84
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/31/2019
ms.locfileid: "73239532"
---
# <a name="establish-an-initial-cloud-governance-foundation"></a>初期のクラウド ガバナンス基盤の確立

クラウド ガバナンスの確立は、広範な反復作業です。 特にクラウド導入の初期フェーズでは、スピードとコントロールのバランスを効果的に取ることは困難です。 クラウド導入フレームワーク内のガバナンスのガイダンスは、導入へのアジャイルなアプローチを通じてそのバランスを実現するのに役立ちます。

この記事では、初期のガバナンス基盤を確立するための 2 つのオプションを示します。 どちらのオプションでも、導入計画が実装され、要件がより明確に定義されるので、確実にガバナンス制約をスケーリングおよび展開できます。 既定では、初期基盤により分離とコントロールの位置が想定されます。 また、リソースのガバナンスよりもリソース編成に重点が置かれます。 この軽量の開始点は、ガバナンスに対する_実用最小限の製品 (MVP)_ と呼ばれます。 MVP の目的は、ガバナンスの初期位置の確立に対する障壁を削減して、具体的な各種リスクに対処するソリューションの迅速な成熟化を可能にすることです。

## <a name="already-using-the-cloud-adoption-framework"></a>クラウド導入フレームワークを既に使用

クラウドの導入フレームワークに従ってきた場合は、ガバナンス MVP が既にデプロイされている可能性があります。 ガイダンスは、運用モデルの核となる要素です。 クラウド導入ライフサイクルのすべてのフェーズに存在します。 そのため、[クラウド導入フレームワーク](../index.md)によって、[クラウド導入計画](../plan/index.md)の実装に関連するアクティビティにガバナンスを挿入するガイダンスが提供されます。 このガバナンス統合の一例は、[準備](../ready/index.md)のガイダンスに見られる 1 つまたは複数のランディング ゾーンをデプロイするためのブループリントの使用です。 別の例は、[サブスクリプションのスケールアウト](../ready/azure-best-practices/scaling-subscriptions.md)に関するガイダンスです。 これらの推奨事項のいずれかに従ってきた場合、以下の MVP セクションは、既存のデプロイの決定の単なるレビューです。 クイック レビューの後、[初期のガバナンス ソリューションの成熟化とベスト プラクティスのコントロールの適用](./foundation-improvements.md)に進みます。

## <a name="establish-an-initial-governance-foundation"></a>初期のガバナンス基盤の確立

新規または既存のデプロイに健全なガバナンス基盤を適用するための初期のガバナンス基盤 (ガバナンス MVP とも呼ばれます) の 2 つの異なる例を次に示します。 開始するビジネス ニーズに最も合致する MVP を選択します。

<!-- markdownlint-disable MD033 -->

<ul class="panelContent cardsZ">
<li style="display: flex; flex-direction: column;">
    <a href="./guides/standard/index.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardText">
                        <h3>標準的なガバナンス ガイド</h3>
                        <p>推奨される 2 つのサブスクリプション モデルに基づく大半の組織向けのガイドです。複数のリージョンでのデプロイ向けですが、パブリック クラウドおよびソブリンまたは政府機関クラウドは対象ではありません。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<li style="display: flex; flex-direction: column;">
    <a href="./guides/complex/index.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardText">
                        <h3>複雑な企業向けのガバナンス ガイド</h3>
                        <p>複数の独立した IT 事業部門で管理されている企業やパブリック クラウドおよびソブリンまたは政府機関クラウドにまたがる企業向けのガイドです。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
</ul>
<!-- markdownlint-enable MD033 -->

## <a name="next-steps"></a>次の手順

ガバナンス基盤が整ったら、ソリューションの改善と具体的なリスクからの保護に適した推奨事項を適用します。

> [!div class="nextstepaction"]
> [初期のガバナンス基盤の改善](./foundation-improvements.md)
