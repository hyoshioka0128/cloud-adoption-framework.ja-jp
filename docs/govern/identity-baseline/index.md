---
title: ID ベースライン規範の概要
description: クラウド ガバナンスに関連する ID ベースラインの説明
author: BrianBlanchard
ms.author: brblanch
ms.date: 09/17/2019
ms.topic: landing-page
ms.service: cloud-adoption-framework
ms.subservice: govern
ms.custom: governance
layout: LandingPage
ms.openlocfilehash: 1ac3041abc6e4721366f8270ce37d6bf6885b140
ms.sourcegitcommit: 2362fb3154a91aa421224ffdb2cc632d982b129b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2020
ms.locfileid: "76806188"
---
# <a name="identity-baseline-discipline-overview"></a>ID ベースライン規範の概要

ID ベースラインは、[クラウド導入フレームワーク ガバナンス モデル](../index.md)の[クラウド ガバナンスの 5 つの規範](../governance-disciplines.md)のうちの 1 つです。 ID はクラウドにおける主要なセキュリティ境界とますます考えられるようになっており、従来のネットワーク セキュリティから焦点が移っています。 ID サービスでは IT 環境内でアクセス制御と組織をサポートする主要なメカニズムが提供され、ID ベースラインの規範はクラウド導入作業全体に認証と承認の要件を一貫して適用することで[セキュリティ ベースラインの規範](../security-baseline/index.md)を補完します。

> [!NOTE]
> ID ベースラインのガバナンスは、組織による ID サービスの管理とセキュリティ保護を可能にする既存の IT チーム、プロセス、および手順にとって代わるものではありません。 この規範の主な目的は、ID 関連の潜在的なビジネス リスクを識別し、ID 管理インフラストラクチャの実装、維持、運用を担当する IT スタッフにリスク軽減のためのガイダンスを提供することです。 ガバナンス ポリシーおよびプロセスの策定時には、行っている計画と確認のプロセスに、関係する IT チームを関与させるようにしてください。

クラウド導入フレームワークのこのセクションでは、クラウド ガバナンス戦略の一環として ID ベースライン規範を策定する方法について、概要を説明します。 このガイドの主な対象読者は、お客様の組織のクラウド アーキテクトおよびお客様のクラウド ガバナンス チームのその他のメンバーです。 ただし、この規範に基づく決定、ポリシー、およびプロセスには、組織の ID 管理ソリューションの実装と管理を担当する IT チームの該当メンバーが関与して、議論する必要があります。

社内に ID ベースラインとセキュリティの専門家がいない場合、この規範の一環として外部コンサルタントを雇用することを検討してください。 また、この規範に関連する懸案事項を相談するために、[Microsoft コンサルティング サービス](https://www.microsoft.com/enterprise/services)、[Microsoft FastTrack](https://azure.microsoft.com/programs/azure-fasttrack) クラウド導入サービス、または外部のその他のクラウド導入パートナーを雇用することを検討してください。

## <a name="policy-statements"></a>ポリシー ステートメント

実践的なポリシー ステートメントと、その結果のアーキテクチャの要件は、ID ベースラインの規範の基盤となります。 ポリシー ステートメントのサンプルを参照するには、[ID ベースライン ポリシー ステートメント](./policy-statements.md)に関する記事を参照してください。 これらのサンプルは、お客様の組織のガバナンス ポリシーの第一歩となります。

> [!CAUTION]
> このサンプル ポリシーは、よくあるお客様の体験をもとにしています。 特定のクラウド ガバナンスの要件にこれらのポリシーが合うように調整するには、次の手順を行い、お客様独自のビジネス ニーズに合うポリシー ステートメントを作成してください。

## <a name="develop-governance-policy-statements"></a>ガバナンス ポリシー ステートメントの策定

次の 6 つの手順は、ID ベースラインのガバナンスを開発する場合の、例と可能性のあるオプションです。 各手順は、ID に関連するリスクを管理するために必要なポリシーとプロセスを確立するためのクラウド ガバナンス チーム内での話し合いや、組織の影響を受けるビジネス チームや IT チームとの討議の中で、出発点として使用します。

<!-- markdownlint-disable MD033 -->

<ul class="panelContent cardsE">
<li style="display: flex; flex-direction: column;">
    <a href="./template.md">
        <div class="cardSize">
            <div class="cardPadding" >
                <div class="card" >
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../../_images/govern/process-template.png" class="x-hidden-focus"/>
                        </div>
                    </div>
                    <div class="cardText" style="padding-left:0px;">
                        <h3>ID ベースライン テンプレート</h3>
                        <p class="x-hidden-focus">ID ベースラインの規範を文書化するテンプレートをダウンロードします</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li><li style="display: flex; flex-direction: column;">
    <a href="./business-risks.md">
        <div class="cardSize">
            <div class="cardPadding" >
                <div class="card" >
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../../_images/govern/process-risks.png" class="x-hidden-focus"/>
                        </div>
                    </div>
                    <div class="cardText" style="padding-left:0px;">
                        <h3>ビジネス リスク</h3>
                        <p class="x-hidden-focus">一般的に ID ベースラインの規範に関係する動機およびリスクを理解できます。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<li style="display: flex; flex-direction: column;">
    <a href="./metrics-tolerance.md">
        <div class="cardSize">
            <div class="cardPadding" >
                <div class="card" >
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../../_images/govern/process-metrics.png" class="x-hidden-focus"/>
                        </div>
                    </div>
                    <div class="cardText" style="padding-left:0px;">
                        <h3>インジケーターとメトリック</h3>
                        <p class="x-hidden-focus">ID ベースラインの規範に投資する最適なタイミングであるかどうかを知るインジケーターです。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<li style="display: flex; flex-direction: column;">
    <a href="./compliance-processes.md">
        <div class="cardSize">
            <div class="cardPadding" >
                <div class="card" >
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../../_images/govern/process-enforce.png" class="x-hidden-focus"/>
                        </div>
                    </div>
                    <div class="cardText" style="padding-left:0px;">
                        <h3>ポリシー準拠プロセス</h3>
                        <p class="x-hidden-focus">ID ベースラインの規範がポリシーに準拠するようにするための推奨の手順です。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<li style="display: flex; flex-direction: column;">
    <a href="./discipline-improvement.md">
        <div class="cardSize">
            <div class="cardPadding" >
                <div class="card" >
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../../_images/govern/process-maturity.png" class="x-hidden-focus"/>
                        </div>
                    </div>
                    <div class="cardText" style="padding-left:0px;">
                        <h3>成熟度</h3>
                        <p class="x-hidden-focus">クラウドの導入の段階とクラウド管理の成熟度を調整します。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<li style="display: flex; flex-direction: column;">
    <a href="./toolchain.md">
        <div class="cardSize">
            <div class="cardPadding" >
                <div class="card" >
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../../_images/govern/process-toolchain.png" class="x-hidden-focus"/>
                        </div>
                    </div>
                    <div class="cardText" style="padding-left:0px;">
                        <h3>ツールチェーン</h3>
                        <p class="x-hidden-focus">ID ベースラインの規範をサポートするために実装できる Azure サービスです。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
</ul>

<!-- markdownlint-enable MD033 -->

## <a name="next-steps"></a>次のステップ

特定の環境で[ビジネス上のリスク](./business-risks.md)を評価する方法の概要です。

> [!div class="nextstepaction"]
> [ビジネス リスクを理解する](./business-risks.md)
