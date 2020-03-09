---
title: クラウド ガバナンスの 5 つの規範
description: Azure 向けクラウド導入フレームワークを使用して、Cost Management、デプロイ高速化、ID ベースライン、リソースの整合性、およびセキュリティ ベースラインについて学習します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 09/17/2019
ms.topic: landing-page
ms.service: cloud-adoption-framework
ms.subservice: govern
ms.custom: governance
layout: LandingPage
ms.openlocfilehash: d50364a621e57b95e26f5686f4d470984530e161
ms.sourcegitcommit: af45c1c027d7246d1a6e4ec248406fb9a8752fb5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/27/2020
ms.locfileid: "77707681"
---
# <a name="the-five-disciplines-of-cloud-governance"></a>クラウド ガバナンスの 5 つの規範

<!-- markdownlint-disable MD033 -->

<ul class="panelContent cardsI">
    <li style="display: flex; flex-direction: column;">
        <div class="cardSize">
            <div class="cardPadding" style="padding-bottom:10px;">
                <div class="card" style="padding-bottom:10px;">
                    <div class="cardText" style="padding-left:0px;">
ビジネス プロセスまたは技術プラットフォームの変更にはリスクが伴います。 クラウド カストディアンと呼ばれることもあるメンバーで構成されるクラウド ガバナンス チームの任務は、リスクを軽減し、導入やイノベーションの作業をできるだけ中断させないようにすることです。<br/><br/>クラウド導入フレームワーク ガバナンス モデルでは、<a href="./corporate-policy.md">企業ポリシーの策定</a>と<a href="#disciplines-of-cloud-governance">クラウド ガバナンスの 5 つの規範</a>に重点を置くことによって、(選択したクラウド プラットフォームに関係なく) これらの決定を導きます。 <a href="./guides/index.md">アクションにつながる設計ガイド</a>は、Azure サービスを使用してこのモデルを実証します。 以下では、クラウド導入フレームワーク ガバナンス モデルの規範について説明します。
                    </div>
                </div>
            </div>
        </div>
    </li>
    <li style="display: flex; flex-direction: column;">
        <a href="../_images/operational-transformation-govern-highres.png" style="display: flex; flex-direction: column; flex: 1 0 auto;">
            <div class="cardSize">
                <div class="cardPadding" style="padding-bottom:10px;">
                    <div class="card" style="padding-bottom:10px;">
                        <div class="cardText" style="padding-left:0px;">
    <img src="../_images/operational-transformation-govern-highres.png" alt="Diagram of the Cloud Adoption Framework governance model: Corporate policy and governance disciplines">
    <br>
    <i>図 1 - 企業ポリシーとクラウド ガバナンスの 5 つの規範のダイアグラム。</i>
                        </div>
                    </div>
                </div>
            </div>
        </a>
    </li>
</ul>

<!-- markdownlint-enable MD033 -->

## <a name="disciplines-of-cloud-governance"></a>クラウド ガバナンスの規範

どのようなクラウド プラットフォームにも、ポリシーを周知したりツールチェーンを調整したりするのに役立つ共通のガバナンス規範があります。 これらの規範は、クラウド プラットフォームの枠を超えた企業ポリシーの自動化と実施の適切なレベルに関する意思決定の指針です。

<!-- markdownlint-disable MD033 -->

<ul class="panelContent cardsA">
<li style="display: flex; flex-direction: column;">
    <a href="./cost-management/index.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/govern/cost-management.png" class="x-hidden-focus"/>
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>Cost Management</h3>
                        <p>コストはクラウド ユーザーの最大の関心事です。 すべてのクラウド プラットフォームに対応した、コスト管理のためのポリシーを策定します。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<li style="display: flex; flex-direction: column;">
    <a href="./security-baseline/index.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/govern/security-baseline.png" class="x-hidden-focus"/>
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>セキュリティ ベースライン</h3>
                        <p>セキュリティは複雑なトピックであり、会社ごとに独特です。 セキュリティ要件が確立されると、クラウド ガバナンスのポリシーと実施により、それらの要件がネットワーク、データ、資産の構成全体に適用されます。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<li style="display: flex; flex-direction: column;">
    <a href="./identity-baseline/index.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/govern/identity-baseline.png" class="x-hidden-focus"/>
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>ID ベースライン</h3>
                        <p>ID 要件の適用が一貫性を欠いた場合、侵害のリスクが高まるおそれがあります。 ID ベースラインの規範では、クラウド導入作業全体で ID が一貫して適用されることを保証することに焦点が当てられています。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<li style="display: flex; flex-direction: column;">
    <a href="./resource-consistency/index.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/govern/resource-consistency.png" class="x-hidden-focus"/>
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>リソースの整合性</h3>
                        <p>クラウドの運用はリソース構成の一貫性に依存します。 ガバナンス ツールを使用して、一貫した方法でリソースを構成し、オンボーディング、ドリフト、検出可能性、および復旧に関連したリスクを管理できます。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<li style="display: flex; flex-direction: column;">
    <a href="./deployment-acceleration/index.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/govern/deployment-acceleration.png" class="x-hidden-focus"/>
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>デプロイ高速化</h3>
                        <p>デプロイと構成のアプローチにおける集中化、標準化、一貫性により、ガバナンスのプラクティスが改善されます。 クラウド ベースのガバナンス ツールによって提供されれば、デプロイ アクティビティの迅速化に寄与するクラウドの要因となります。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
</ul>

<!-- markdownlint-enable MD033 -->
