---
title: リソースの整合性の規範の概要
description: クラウド ガバナンス戦略の一環としてリソースの整合性規範を策定する方法について説明します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 09/17/2019
ms.topic: landing-page
ms.service: cloud-adoption-framework
ms.subservice: govern
ms.custom: governance
layout: LandingPage
ms.openlocfilehash: 3bfe85e2305f1c844ddf92a14cf298a0a1c30edb
ms.sourcegitcommit: 60d8b863d431b5d7c005f2f14488620b6c4c49be
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2020
ms.locfileid: "83218171"
---
# <a name="resource-consistency-discipline-overview"></a>リソースの整合性の規範の概要

リソースの整合性は、[クラウド導入フレームワーク ガバナンス モデル](../index.md)内の[クラウド ガバナンスの 5 つの規範](../governance-disciplines.md)のうちの 1 つです。 これは、環境、アプリケーションまたはワークロードの運用管理に関係するポリシーを規定する方法に関する規範です。 IT 運用チームは、多くの場合、アプリケーション、ワークロード、および資産のパフォーマンスの監視を行います。 また、スケーリングの要求、パフォーマンスのサービス レベル アグリーメント (SLA) 違反の修正、および自動修復によるパフォーマンス SLA 違反の事前回避の対応作業も通常行っています。 クラウド ガバナンスの 5 つの規範のうちのリソースの整合性とは、IT の運用中に検出でき、復旧のソリューションに含まれ、繰り返し可能な操作手順に組み込むことができる方法で、リソースが常に構成されていることを保証する規範です。

> [!NOTE]
> リソースの整合性規範は、クラウドベースのリソースを効率的に管理しているお客様の組織の既存 IT チーム、プロセス、および手順にとって代わるものではありません。 この規範は、クラウド上のご利用のリソースを管理する責任がある IT スタッフが、ビジネス上の潜在的なリスクを特定し、リスクを軽減するときの指針となることを主な目的としています。 ガバナンス ポリシーおよびプロセスの策定時には、行っている計画と確認のプロセスに、関係する IT チームを関与させるようにしてください。

クラウド導入フレームワーク のこのセクションでは、お客様のクラウド ガバナンス戦略の一環として、リソースの整合性の規範を開発する方法を説明します。 このガイドの主な対象読者は、お客様の組織のクラウド アーキテクトおよびお客様のクラウド ガバナンス チームのその他のメンバーです。 ただし、この規範を使用した決定、ポリシーおよびプロセスには、お客様の組織のリソースの整合性ソリューションを導入および管理する責任のある IT チームの該当メンバーが関与して、議論する必要があります。

お客様の社内にリソースの整合性の専門家がいない場合、この規範の一環として外部コンサルタントを雇用することを検討してください。 また、ご利用のクラウドベースの資産を最善の方法で整理、トラッキングおよび最適化する相談のために、[Microsoft コンサルティング サービス](https://www.microsoft.com/industry/services/consulting)、[Microsoft FastTrack](https://azure.microsoft.com/programs/azure-fasttrack) クラウド導入サービス、または外部のその他のクラウド導入のエキスパートを雇用することを検討してください。

## <a name="policy-statements"></a>ポリシー ステートメント

実践的なポリシー ステートメントと、その結果のアーキテクチャの要件は、リソースの整合性の規範の基盤となります。 ポリシー ステートメントのサンプルを参照するには、[リソース整合性ポリシー ステートメント](./policy-statements.md)に関する記事を参照してください。 これらのサンプルは、お客様の組織のガバナンス ポリシーの第一歩となります。

> [!CAUTION]
> このサンプル ポリシーは、よくあるお客様の体験をもとにしています。 特定のクラウド ガバナンスの要件にこれらのポリシーが合うように調整するには、次の手順を行い、お客様独自のビジネス ニーズに合うポリシー ステートメントを作成してください。

## <a name="develop-governance-policy-statements"></a>ガバナンス ポリシー ステートメントの策定

次の 6 つの手順では、リソースの整合性規範を開発する際の例と可能性のあるオプションを示します。 各手順は、リソースの整合性規範のリスクを管理するのに必要なポリシーやプロセスを確立するためのクラウド ガバナンス チーム内での話し合いや、組織の影響を受けるビジネスおよび IT チームとの討議の中で、出発点として使用します。

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
                        <h3>リソースの整合性規範テンプレート</h3>
                        <p class="x-hidden-focus">リソースの整合性規範を文書化するためのテンプレートをダウンロードできます。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<li style="display: flex; flex-direction: column;">
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
                        <p class="x-hidden-focus">リソースの整合性の規範に通常関係する、動機およびリスクを理解できます。</p>
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
                        <p class="x-hidden-focus">リソースの整合性の規範に投資する最適なタイミングであるかどうかを知るインジケーターです。</p>
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
                        <p class="x-hidden-focus">リソースの整合性の規範がポリシーに準拠するようにするための推奨の手順です。</p>
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
                        <p class="x-hidden-focus">リソースの整合性の規範をサポートするために実装できる Azure サービスです。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
</ul>

## <a name="next-steps"></a>次のステップ

特定の環境で[ビジネス上のリスク](./business-risks.md)を評価する方法の概要です。

> [!div class="nextstepaction"]
> [ビジネス リスクを理解する](./business-risks.md)
