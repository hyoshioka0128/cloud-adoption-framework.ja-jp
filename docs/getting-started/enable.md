---
title: クラウド導入の取り組みの途中でお客様の成功を実現する
titleSuffix: Microsoft Cloud Adoption Framework for Azure
description: クラウド導入の取り組み全体を通じてお客様の成功を実現する
author: BrianBlanchard
ms.author: brblanch
ms.date: 04/04/2019
ms.topic: landing-page
ms.service: cloud-adoption-framework
ms.subservice: overview
layout: LandingPage
ms.openlocfilehash: f24227cbbb06630810f7f6b48e7acdf842c171b3
ms.sourcegitcommit: 7df593a67a2e77b5f61c815814af9f0c36ea5ebd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/09/2020
ms.locfileid: "75781591"
---
# <a name="enable-success-during-a-cloud-adoption-journey"></a>クラウド導入の取り組みの途中で成功を実現する

クラウド導入フレームワークは、さまざまなクラウド導入作業のガイドとなる無料のセルフサービス ツールです。 このフレームワークは、Microsoft Azure によって実現可能なビジネスの目標を顧客が無事に達成できるよう支援します。 ただし、このコンテンツでは、読者が直面しているビジネス、カルチャ、または技術面の課題は広範であり、クラウドにとらわれない視点が求められる場合があることも認識しています。 したがって、このガイダンスの各セクションは Azure ファーストのアプローチで始まり、ビジネスや技術面の多くの意思決定にも応用できる、クラウドにとらわれない理論に従っています。

このフレームワーク全体で、可能化が中心的なテーマです。 次のチェックリストは、IT とビジネスの両方で導入の取り組みが成功したと見なされるようにするための、基本的なクラウド導入原則を項目化したものです。

- **プランニング:** 明確な[ビジネスの成果](../strategy/business-outcomes/index.md)、明確に定義された[デジタル資産計画](../digital-estate/index.md)、よく理解された[導入バックログ](../migrate/migration-considerations/prerequisites/migration-backlog-review.md)を確立する。
- **準備完了:** [スキルと学習計画](../ready/technical-skills.md)によってスタッフの準備を確実にする。
- **運用:** 導入中および導入後のアクティビティをガイドする管理可能な運用モデルを定義します。
  - **[整理](../organize/index.md):** ユーザーとチームを集めて、適切なクラウドの運用と導入に関する知識を伝達します。
  - **ガバナンス:** 適正な[ガバナンス規範](../govern/index.md)を整備し、コスト管理、リスク軽減、コンプライアンス、セキュリティの各基準をすべてのクラウド導入にわたって一貫して適用します。
  - **管理:** ビジネス プロセスの中断を最小限に抑え、IT ポートフォリオの安定性を確保するための、IT ポートフォリオの継続的な[運用管理](../manage/index.md)。
  - **サポート:** 適正な[パートナーシップとサポートのオプション](../migrate/migration-considerations/assess/partnership-options.md)を整備します。

もう 1 つの主要テーマは、クラウド導入を成功させるための重要な品質属性であるセキュリティです。 セキュリティはこのフレームワーク全体に統合されており、クラウド ワークロードに対する機密性、整合性、可用性の保証を維持するための統合されたガイダンスとなります。 

## <a name="additional-tools"></a>その他のツール

クラウド導入フレームワークに加えて、Microsoft では、成功を実現できる追加のトピックを扱っています。 この記事では、クラウド導入フレームワークの範囲を超えて、成功を大幅に向上させられるいくつかの一般的なツールについて説明します。 クラウド ガバナンス、回復性があるアーキテクチャ、技術的なスキル、および DevOps アプローチの確立はそれぞれ、あらゆるクラウド導入作業の成功にとって重要です。 クラウド導入作業中にいつでも確認できるリソースとして、このページをブックマークしてください。

<!-- markdownlint-disable MD033 -->

<ul class="panelContent cardsH">
<li style="display: flex; flex-direction: column;">
    <a href="../govern/guides/index.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage bgdAccent1">
                            <img alt="Cloud Adoption Framework governance model" src="../_images/operational-transformation-govern-highres.png" data-linktype="external" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>クラウド ガバナンス</h3>
                        <p>ビジネス リスクを理解し、それらのリスクを適切なポリシーとプロセスにマッピングします。 クラウド ガバナンス ツールと、クラウド ガバナンスの 5 つの原則を使用することで、リスクが最小限に抑えられ、成功の可能性が高まります。 クラウド ガバナンスはコストの管理、一貫性の維持、セキュリティの向上、デプロイの迅速化に役立ちます。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<li style="display: flex; flex-direction: column;">
    <a href="https://docs.microsoft.com/azure/architecture/framework/resiliency/overview" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage bgdAccent1">
                            <img alt="Resilient architecture" src="https://docs.microsoft.com/azure/architecture/resiliency/images/redundancy.svg" data-linktype="external" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>信頼性の高いアーキテクチャ (回復性)</h3>
                        <p>クラウド内で信頼性の高いアプリケーションを構築することは、従来のアプリケーション開発とは異なります。 従来は、スケールアップのためによりハイエンドのハードウェアを購入する場合がありましたが、クラウド環境では、スケールアップではなくスケールアウトを行います。 目標は、障害がまったく発生しないように努力することではなく、障害が発生した単一コンポーネントの影響を最小限に抑えることです。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<li style="display: flex; flex-direction: column;">
    <a href="../ready/technical-skills.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage bgdAccent1">
                            <img alt="Build Technical Skills" src="https://docs.microsoft.com/media/learn/Product/Learn/learningpath_graphic.svg" data-linktype="external" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>技術スキルの構築</h3>
                        <p>クラウド導入の成功を支援する最良のツールは、よく訓練されたチームです。 集中的なラーニング パスの助けを借りて、既存のビジネスおよび技術チーム メンバーのスキルを向上させます。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<li style="display: flex; flex-direction: column;">
    <a href="https://docs.microsoft.com/azure/devops/learn/" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage bgdAccent1">
                            <img alt="Learn DevOps" src="https://docs.microsoft.com/azure/devops/learn/_img/learn-devops.svg" data-linktype="external" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>DevOps アプローチ</h3>
                        <p>Microsoft の歴史的な変革は、カルチャに対する成長マインドセットのアプローチと、技術の実践に対する DevOps のアプローチにしっかり根付いています。 クラウド導入フレームワークでは、フレームワーク全体に両方を組み込んでいます。 DevOps の導入を迅速化するには、DevOps 学習コンテンツを確認してください</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<li style="display: flex; flex-direction: column;">
    <a href="https://docs.microsoft.com/azure/architecture/" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage bgdAccent1">
                            <img alt="Azure Architecture Center" src="https://docs.microsoft.com/azure/architecture/example-scenario/data/media/architecture-data-warehouse.png" data-linktype="external" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>Azure アーキテクチャ センター</h3>
                        <p>Azure 上で実行するソリューションのアーキテクチャ設計に役立つアーキテクチャ ソリューション、参照アーキテクチャ、シナリオの例、ベスト プラクティス、およびクラウド デザイン パターン。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<li style="display: flex; flex-direction: column;">
    <a href="https://azure.microsoft.com/pricing/calculator/" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage bgdAccent1">
                            <img alt="Azure Pricing Calculator" src="../_images/calculator-preview.png" data-linktype="external" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>Azure 料金計算ツール</h3>
                        <p>選択したソリューションを構築または移行するために必要な各種 Azure コンポーネントのコストを計算します。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
</ul>

<!-- markdownlint-enable MD033 -->

## <a name="next-steps"></a>次のステップ

クラウド導入フレームワークの最も有効な側面を理解していれば、[移行](./migrate.md)または[イノベーション](./innovate.md)の取り組みで成功を収める確率が大幅に上昇します。

> [!div class="nextstepaction"]
> [移行](./migrate.md)
>
> [イノベーション](./innovate.md)
