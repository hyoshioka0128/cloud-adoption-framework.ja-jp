---
title: 組織の配置を管理する
titleSuffix: Microsoft Cloud Adoption Framework for Azure
description: 組織の配置を管理するためのアプローチの概要を示します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 09/10/2019
ms.topic: landing-page
ms.service: cloud-adoption-framework
ms.subservice: organize
ms.custom: organize
layout: LandingPage
ms.openlocfilehash: b1fc986983b709491d0b06f4624c60deed7e9219
ms.sourcegitcommit: d19e026d119fbe221a78b10225230da8b9666fe1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/26/2019
ms.locfileid: "71026180"
---
# <a name="managing-organizational-alignment"></a>組織の配置の管理

クラウド導入は、人員が適切に組織化されていなければ達成できません。 クラウド導入の成功は、適切なスキルを持つ人員が、明確に定義された業務目標に沿って、適切に管理された環境おいて、適切な種類の作業を遂行することによって実現します。 効果的なクラウド運用モデルを実現するには、人員が適切に配置された組織構造を確立することが重要です。 この記事では、適切な組織構造を確立して維持するためのアプローチを 4 つのステップで概説します。

## <a name="organization-alignment-exercises"></a>組織の配置の演習

次の演習は、クラウド導入を支援するランディング ゾーンの作成プロセスのガイダンスとして役立ちます。

<!-- markdownlint-disable MD033 -->

<ul class="panelContent cardsF">
    <li style="display: flex; flex-direction: column;">
        <a href="#structure-type">
            <div class="cardSize">
                <div class="cardPadding" style="padding-bottom:10px;">
                    <div class="card" style="padding-bottom:10px;">
                        <div class="cardImageOuter">
                            <div class="cardImage">
                                <img alt="" src="../_images/icons/1.png" data-linktype="external">
                            </div>
                        </div>
                        <div class="cardText" style="padding-left:0px;">
                            <h3>構造の種類</h3>
実際の運用モデルに最も適した組織構造の種類を定義します。
                        </div>
                    </div>
                </div>
            </div>
        </a>
    </li>
    <li style="display: flex; flex-direction: column;">
        <a href="#understand-required-cloud-capabilities">
            <div class="cardSize">
                <div class="cardPadding" style="padding-bottom:10px;">
                    <div class="card" style="padding-bottom:10px;">
                        <div class="cardImageOuter">
                            <div class="cardImage">
                                <img alt="" src="../_images/icons/2.png" data-linktype="external">
                            </div>
                        </div>
                        <div class="cardText" style="padding-left:0px;">
                            <h3>クラウド機能</h3>
クラウドの導入と運用に必要なクラウド機能を理解します。
                        </div>
                    </div>
                </div>
            </div>
        </a>
    </li>
    <li style="display: flex; flex-direction: column;">
        <a href="./organization-structures.md">
            <div class="cardSize">
                <div class="cardPadding" style="padding-bottom:10px;">
                    <div class="card" style="padding-bottom:10px;">
                        <div class="cardImageOuter">
                            <div class="cardImage">
                                <img alt="" src="../_images/icons/3.png" data-linktype="external">
                            </div>
                        </div>
                        <div class="cardText" style="padding-left:0px;">
                            <h3>チームの確立</h3>
さまざまなクラウド機能を提供するチームを定義します。 参照用に、いくつかのベスト プラクティスの選択肢が一覧表示されています。
                        </div>
                    </div>
                </div>
            </div>
        </a>
    </li>
    <li style="display: flex; flex-direction: column;">
        <a href="./raci-alignment.md">
            <div class="cardSize">
                <div class="cardPadding" style="padding-bottom:10px;">
                    <div class="card" style="padding-bottom:10px;">
                        <div class="cardImageOuter">
                            <div class="cardImage">
                                <img alt="" src="../_images/icons/4.png" data-linktype="external">
                            </div>
                        </div>
                        <div class="cardText" style="padding-left:0px;">
                            <h3>RACI マトリックス</h3>
明確に定義された役割は、どのような運用モデルにおいても重要な側面です。 提供されている RACI マトリックスを活用して、クラウド運用モデルのさまざまな機能について、実行責任、説明責任、協業、および報告の役割を各チームにマップします。
                        </div>
                    </div>
                </div>
            </div>
        </a>
    </li>
</ul>

<!-- markdownlint-enable MD033 -->

## <a name="structure-type"></a>構造の種類

以下の組織構造は、必ずしも組織図にマップする必要はありません。 通常、組織図には、命令と統制の管理構造が反映されています。 対照的に、以下の組織構造は、役割と責任の整合を取るように設計されています。 アジャイルなマトリックス組織では、これらの構造は仮想チーム (または、v チーム) として表現するのが最も適切と言えます。 これらの組織構造を組織図で表すことができないことを示唆する制限はありませんが、これは、効果的な運用モデルを作成するために必ずしも必要というわけではありません。

組織の配置を管理するための最初のステップは、以下の組織構造をどのように実現するかを決定することです。

- **組織図の配置:** 管理階層、マネージャーの責任、およびスタッフの配置は、組織の構造に合わせて調整されます。
- **仮想チーム (v チーム):** 管理構造と組織図は変更されません。 代わりに、仮想チームが作成され、必要な機能が割り当てられます。
- **混合モデル:** 多くの場合、変革の目標を達成するには、組織図と v チームの配置を組み合わせて使用する必要があります。

## <a name="understand-required-cloud-capabilities"></a>必要なクラウド機能を理解する

クラウドの導入と長期的な運用モデルを成功させるために必要なクラウドの機能の一覧を以下に示します。 さまざまなクラウド機能について理解したら、それらをスタッフ配置と成熟度に基づいて組織構造に整合させることができます。

- [クラウド導入](./cloud-adoption.md):技術的なソリューションを提供します。
- [クラウド戦略](./cloud-strategy.md):技術的な変更をビジネス ニーズに合わせます。
- [クラウド運用](./cloud-operations.md):採用されたソリューションをサポートおよび運用します。
- [クラウドのセンター オブ エクセレンス](./cloud-center-of-excellence.md):導入の品質、速度、回復性を向上させます。
- [クラウド ガバナンス](./cloud-governance.md):リスクの管理
- [クラウド プラットフォーム](./cloud-platform.md):プラットフォームを運用し、成熟させます。
- [クラウド自動化](./cloud-automation.md):導入とイノベーションを促進します。

## <a name="maturing-organizational-structures"></a>組織構造の成熟化

上記の各機能は、明示的に、または定義されたチーム構造に従って、ある程度まで、クラウド導入のあらゆる作業で提供されます。
導入のニーズが拡大すると、それに伴ってバランスと構造を生み出すニーズも高まります。 そうしたニーズを満たすために、企業は多くの場合、組織構造を成熟させるプロセスに従います。

![組織の成熟サイクル](../_images/ready/org-ready-maturity.png)

[組織構造の成熟度の決定](./organization-structures.md)に関する記事では、成熟度の各レベルについてさらに詳しく説明しています。

## <a name="aligning-raci-charts"></a>RACI チャートの配置

成熟度のレベルごとに、さまざまなクラウド機能の説明責任が新しいチームにシフトします。 このように説明責任がシフトすることで、変化への障壁を取り除き、自動化することにより、移行とイノベーションのサイクルを短縮できます。 割り当てを適切に配置するために、[RACI の配置](./raci-alignment.md)に関する記事では、参照されている各組織構造の RACI チャートを共有しています。

## <a name="next-steps"></a>次の手順

組織構造の決定を経時的に追跡するために、[RACI スプレッドシート テンプレート](https://archcenter.blob.core.windows.net/cdn/fusion/management/raci-template.xlsx)をダウンロードして変更します。

> [!div class="nextstepaction"]
> [RACI スプレッドシート テンプレートをダウンロードする](https://archcenter.blob.core.windows.net/cdn/fusion/management/raci-template.xlsx)
