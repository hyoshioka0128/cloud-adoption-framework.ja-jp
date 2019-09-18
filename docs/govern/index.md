---
title: Azure 向けの Microsoft クラウド導入フレームワークのガバナンス
description: Azure 向けの Microsoft クラウド導入フレームワークのガバナンスについて説明します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 02/11/2019
ms.topic: landing-page
ms.service: cloud-adoption-framework
ms.subservice: govern
ms.custom: governance
layout: LandingPage
ms.openlocfilehash: 899306c60be3750cd6165763a63d1d5b1f5eb0a6
ms.sourcegitcommit: 443c28f3afeedfbfe8b9980875a54afdbebd83a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/16/2019
ms.locfileid: "71026095"
---
# <a name="governance-in-the-microsoft-cloud-adoption-framework-for-azure"></a>Azure 向けの Microsoft クラウド導入フレームワークのガバナンス

クラウドは、ビジネスをサポートするテクノロジの新しいパラダイムを生み出します。 また、これらの新しいパラダイムによって、それらのテクノロジの導入、管理、およびガバナンスの方法も変わります。 無人プロセスで実行される 1 行のコードによって、実質的にデータ センター全体を破壊して再構築できる場合、私たちは従来のアプローチについて見直す必要があります。 これは、特にガバナンスの場合に当てはまります。

## <a name="get-started-with-cloud-governance"></a>クラウド ガバナンスを開始する

クラウド ガバナンスは反復プロセスです。 オンプレミスの IT 環境を管理する既存のポリシーを持つ組織の場合、クラウド ガバナンスによりこれらのポリシーが補完される必要があります。 ただし、オンプレミスとクラウド間の会社のポリシーの統合レベルは、クラウド ガバナンスの成熟度とクラウド内のデジタル資産によって異なります。 クラウド資産が時間の経過とともに変化するにつれて、クラウド ガバナンスのプロセスとポリシーも変化します。 次の演習は、最初のガバナンス基盤の構築を開始するのに役立ちます。

<!-- markdownlint-disable MD033 -->

<ul class="panelContent cardsF">
    <li style="display: flex; flex-direction: column;">
        <a href="./methodology.md">
            <div class="cardSize">
                <div class="cardPadding" style="padding-bottom:10px;">
                    <div class="card" style="padding-bottom:10px;">
                        <div class="cardImageOuter">
                            <div class="cardImage">
                                <img alt="" src="../_images/icons/1.png" data-linktype="external">
                            </div>
                        </div>
                        <div class="cardText" style="padding-left:0px;">
                            <h3>手法</h3>
クラウド導入フレームワークでクラウド ガバナンスを推進する手法の基本的な理解を確立し、最終状態のソリューションについて熟考を開始します。
                        </div>
                    </div>
                </div>
            </div>
        </a>
    </li>
    <li style="display: flex; flex-direction: column;">
        <a href="./benchmark.md">
            <div class="cardSize">
                <div class="cardPadding" style="padding-bottom:10px;">
                    <div class="card" style="padding-bottom:10px;">
                        <div class="cardImageOuter">
                            <div class="cardImage">
                                <img alt="" src="../_images/icons/2.png" data-linktype="external">
                            </div>
                        </div>
                        <div class="cardText" style="padding-left:0px;">
                            <h3>ベンチマーク</h3>
現状の状態と将来の状態を評価して、フレームワークを適用するためのビジョンを確立します。
                        </div>
                    </div>
                </div>
            </div>
        </a>
    </li>
    <li style="display: flex; flex-direction: column;">
        <a href="./initial-foundation.md">
            <div class="cardSize">
                <div class="cardPadding" style="padding-bottom:10px;">
                    <div class="card" style="padding-bottom:10px;">
                        <div class="cardImageOuter">
                            <div class="cardImage">
                                <img alt="" src="../_images/icons/3.png" data-linktype="external">
                            </div>
                        </div>
                        <div class="cardText" style="padding-left:0px;">
                            <h3>ガバナンスを始めるための基礎</h3>
小規模で簡単に実装されるガバナンス ツールのセットを使って、ガバナンス体験を始めます。 この最初のガバナンス基盤は、実用最小限の製品 (MVP) と呼ばれます。
                        </div>
                    </div>
                </div>
            </div>
        </a>
    </li>
    <li style="display: flex; flex-direction: column;">
        <a href="./foundation-improvements.md">
            <div class="cardSize">
                <div class="cardPadding" style="padding-bottom:10px;">
                    <div class="card" style="padding-bottom:10px;">
                        <div class="cardImageOuter">
                            <div class="cardImage">
                                <img alt="" src="../_images/icons/4.png" data-linktype="external">
                            </div>
                        </div>
                        <div class="cardText" style="padding-left:0px;">
                            <h3>初期のガバナンス基盤の改善</h3>
クラウド導入プランの実装全体で、最終状態へと進みながら、ガバナンス コントロールを繰り返し追加して具体的なリスクに対処します。
                        </div>
                    </div>
                </div>
            </div>
        </a>
    </li>
</ul>

<!-- markdownlint-enable MD033 -->

## <a name="objective-of-this-content"></a>このコンテンツの目的

クラウド導入フレームワークのこのセクションのガイダンスには、次の 2 つの目的があります。

- お客様がよく経験する一般的なエクスペリエンスを表す、実践的なガバナンス ガイドの例を示します。 それぞれの例には、ビジネス リスク、リスク軽減に関する会社のポリシー、および技術的ソリューションを実装するための設計ガイダンスが含まれています。 必然的に、その設計ガイダンスは Azure 固有となります。 これらのガイドにある他のすべての内容は、クラウドに依存しないアプローチ、またはマルチクラウド アプローチで適用できます。
- さまざまなビジネス ニーズを満たす、パーソナライズされたガバナンス ソリューションを作成するのに役立ちます。 これらのニーズには、企業のポリシー、プロセス、およびツールの開発に関する詳細なガイダンスを通じた複数のパブリック クラウドのガバナンスが含まれます。

この内容は、クラウド ガバナンス チームが使用することを目的としています。 また、クラウド ガバナンスで強力な基盤を開発する必要があるクラウド設計者も対象としています。

## <a name="intended-audience"></a>対象ユーザー

クラウド導入フレームワークのコンテンツは、企業のビジネス、テクノロジ、および文化に影響を与えます。 クラウド導入フレームワークのこのセクションは主に、IT セキュリティ、IT ガバナンス、財務、事業部門のリーダー、ネットワーク、ID、およびクラウド導入のチームを対象としています。 これらの役割の人々に対するさまざまな依存関係には、このガイダンスを使用したクラウド アーキテクトによる促進的アプローチが必要です。 これらのチームでの円滑化は 1 回限りのアクションとなる可能性があります。 場合によっては、これらの他の役割の人々とのやり取りが継続します。

クラウド アーキテクトは、これらの対象ユーザーをまとめる思想的リーダーおよび進行役の役割を果たします。 このガイドのコレクションの内容は、クラウド アーキテクトが適切な対象ユーザーと適切なコミュニケーションを取り、必要な決定を促すことをサポートするために設計されています。 クラウドによって実現されるビジネスの変革は、ビジネスと IT 全体にわたる意思決定を導くクラウド アーキテクトによって決まります。

**このセクションでのクラウド アーキテクトの特性:** クラウド導入フレームワークの各セクションは、クラウド アーキテクトの役割のさまざまな専門性やバリエーションを表しています。 クラウド導入フレームワークのこのセクションは、技術的リスクの軽減または削減に対する情熱を持つクラウド アーキテクトを対象として設計されています。 一部のクラウド プロバイダーは、これらのスペシャリストを "*クラウド カストディアン*" と呼びます。弊社では、"*クラウド ガーディアン*"、または総称して "*クラウド ガバナンス チーム*" と呼ぶことにします。 実践的な各ガバナンス ガイドの記事では、クラウド ガバナンス チームの構成とロールが時間の経過とともにどのように変化するかを示します。

## <a name="use-this-guide"></a>このガイドの使用法

このガイドに最初から最後まで従う場合、この内容は、クラウドの実装と並行して堅牢なクラウド ガバナンス戦略を開発するのに役立ちます。 このガイダンスでは、そのような戦略の理論と実装を段階を追って説明します。

Azure 実装の理論とクイック アクセスの速習コースについては、[ガバナンス ガイドの概要](./guides/index.md)に関するページから開始してください。 このガイダンスを使用すると、基本的なことから開始して、クラウド導入作業と並行して、ガバナンスのニーズを段階的に改善することができます。

## <a name="next-steps"></a>次の手順

クラウド導入フレームワークでクラウド ガバナンスを推進する手法の基本的な理解を確立します。

> [!div class="nextstepaction"]
> [メソドロジを理解する](./methodology.md)
