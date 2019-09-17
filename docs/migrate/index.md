---
title: クラウド移行
titleSuffix: Microsoft Cloud Adoption Framework for Azure
description: クラウド導入フレームワークにおけるクラウド移行
author: BrianBlanchard
ms.author: brblanch
ms.date: 04/04/2019
ms.topic: landing-page
ms.service: cloud-adoption-framework
ms.subservice: migrate
layout: LandingPage
ms.openlocfilehash: 982451c9043f775c2e211d9b166ca2d094479ca9
ms.sourcegitcommit: a26c27ed72ac89198231ec4b11917a20d03bd222
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/06/2019
ms.locfileid: "70816399"
---
# <a name="cloud-migration-in-the-cloud-adoption-framework"></a>クラウド導入フレームワークにおけるクラウド移行

大企業規模の[クラウド導入計画](../plan/index.md)には、新しいビジネス ロジックの開発に大きな投資を約束できないワークロードが含まれます。 そのようなワークロードは、リフト アンド シフト、シフト アンド オプティマイズ、モダナイズなど、さまざまな手法でクラウドに移すことができます。 そのいずれも移行と見なされます。 次の演習は、そのようなワークロードを評価、移行、最適化、セキュリティ保護、管理するための反復プロセスの構築に役立ちます。

## <a name="getting-started"></a>使用の開始

クラウド導入ライフサイクルのこのフェーズの準備として、フレームワークからは、次の 5 つの演習が提案されます。

<!-- markdownlint-disable MD033 -->
<ul class="panelContent cardsF">
    <li style="display: flex; flex-direction: column;">
        <a href="./azure-migration-guide/prerequisites.md?tabs=Checklist">
            <div class="cardSize">
                <div class="cardPadding" style="padding-bottom:10px;">
                    <div class="card" style="padding-bottom:10px;">
                        <div class="cardImageOuter">
                            <div class="cardImage">
                                <img alt="" src="../_images/icons/1.png" data-linktype="external">
                            </div>
                        </div>
                        <div class="cardText" style="padding-left:0px;">
                            <h3>移行の前提条件</h3>
ランディング ゾーンがデプロイされており、Azure に移行する最初のいくつかのワークロードをホストする準備ができていることを検証します。 クラウド導入戦略とクラウド導入計画が作成されていない場合、両方の作業が進行中であることを検証します。
                        </div>
                    </div>
                </div>
            </div>
        </a>
    </li>
    <li style="display: flex; flex-direction: column;">
        <a href="./azure-migration-guide/index.md">
            <div class="cardSize">
                <div class="cardPadding" style="padding-bottom:10px;">
                    <div class="card" style="padding-bottom:10px;">
                        <div class="cardImageOuter">
                            <div class="cardImage">
                                <img alt="" src="../_images/icons/2.png" data-linktype="external">
                            </div>
                        </div>
                        <div class="cardText" style="padding-left:0px;">
                            <h3>最初のワークロードを移行する</h3>
Azure 移行ガイドを活用し、最初のワークロードを段階的に移行します。 これは、導入の規模拡大に必要なツールや手法に慣れるのに役立ちます。
                        </div>
                    </div>
                </div>
            </div>
        </a>
    </li>
    <li style="display: flex; flex-direction: column;">
        <a href="./expanded-scope/index.md">
            <div class="cardSize">
                <div class="cardPadding" style="padding-bottom:10px;">
                    <div class="card" style="padding-bottom:10px;">
                        <div class="cardImageOuter">
                            <div class="cardImage">
                                <img alt="" src="../_images/icons/3.png" data-linktype="external">
                            </div>
                        </div>
                        <div class="cardText" style="padding-left:0px;">
                            <h3>拡大移行シナリオ</h3>
範囲拡大チェックリストを活用し、将来の状態のアーキテクチャ、移行プロセス、ランディング ゾーン構成、移行ツール決定に合わせた変更が必要になるシナリオを見つけます。
                        </div>
                    </div>
                </div>
            </div>
        </a>
    </li>
    <li style="display: flex; flex-direction: column;">
        <a href="./azure-best-practices/index.md">
            <div class="cardSize">
                <div class="cardPadding" style="padding-bottom:10px;">
                    <div class="card" style="padding-bottom:10px;">
                        <div class="cardImageOuter">
                            <div class="cardImage">
                                <img alt="" src="../_images/icons/4.png" data-linktype="external">
                            </div>
                        </div>
                        <div class="cardText" style="padding-left:0px;">
                            <h3>ベスト プラクティス</h3>
拡大範囲またはワークロード/アーキテクチャ固有の移行手法が正しく実装されるよう、ベスト プラクティス セクションに対して変更を検証します。
                        </div>
                    </div>
                </div>
            </div>
        </a>
    </li>
    <li style="display: flex; flex-direction: column;">
        <a href="./migration-considerations/index.md">
            <div class="cardSize">
                <div class="cardPadding" style="padding-bottom:10px;">
                    <div class="card" style="padding-bottom:10px;">
                        <div class="cardImageOuter">
                            <div class="cardImage">
                                <img alt="" src="../_images/icons/5.png" data-linktype="external">
                            </div>
                        </div>
                        <div class="cardText" style="padding-left:0px;">
                            <h3>プロセス改善</h3>
移行はプロセス負荷の高い行為です。 移行作業の増加に応じて、移行に関する考慮事項セクションを使用し、プロセスのさまざまな側面を評価し、成熟させてください。
                        </div>
                    </div>
                </div>
            </div>
        </a>
    </li>
</ul>
<!-- markdownlint-enable MD033 -->

## <a name="iterative-migration-process"></a>反復移行プロセス

クラウド移行は基本的に、評価、移行、最適化、セキュリティ保護と管理という 4 つの単純なフェーズから構成されます。 CAF のこのセクションでは、プロセスの各フェーズを最大限に活用し、クラウド導入計画に合わせて各フェーズを調整する方法について説明します。 次の図は、反復的手法で各フェーズを図示しています。

![クラウド導入フレームワーク移行モデル](../_images/operational-transformation-migrate.png)

## <a name="creating-a-balanced-cloud-portfolio"></a>バランスの取れたクラウド ポートフォリオを作成する

バランスの取れたテクノロジ ポートフォリオには、さまざまな状態の資産が混在しています。 一部のアプリケーションは提供終了が予定されており、提供されるサポートは最小限です。 他のアプリケーションまたは資産は保守状態でサポートされていますが、それらのソリューションの機能は安定しています。 新しいビジネス プロセスでは、市況の変化により、機能強化や最新化が継続的に促進される可能性があります。 新たな収益源となる機会が生じると、新しいアプリケーションまたは資産が環境に導入されます。 資産のライフサイクルの各段階で、投資が収益と利益に与える影響は変わります。 ライフサイクル段階が終わりに近づくほど、新しい機能や最新化の取り組みによって投資から大きな利益が得られる可能性は低くなります。

クラウドにはさまざまな導入メカニズムが提供されており、それぞれに同程度の投資と収益があります。 クラウドネイティブ アプリケーションを構築することで、運用コストを大幅に削減できます。 クラウドネイティブ アプリケーションがリリースされると、新しい機能やソリューションの開発をより短時間で繰り返すことができるようになります。 アプリケーションを最新化すると、オンプレミス開発モデルに関連する従来の制約が取り除かれ、同様の恩恵を受けられます。 残念ながら、これら 2 つのアプローチには労力がかかり、ソフトウェア開発チームの規模、スキル、経験に依存します。 多くの場合、労働者は適切に配置されません。アプリケーションを最新化できるスキルと才能を備えた人員であれば、新しいアプリケーションを構築することになります。 労働制約のある市場では、大規模な最新化プロジェクトは従業員の満足度と才能の問題に悩まされる可能性があります。 バランスの取れたポートフォリオでは、オンプレミスのままであれば機能が大幅に強化されるアプリケーション用に、このアプローチを確保しておく必要があります。

## <a name="envision-an-end-state"></a>最終状態の構想

効果的な工程には目的地が必要です。 最初の一歩を踏み出す前に、終了状態の大まかなビジョンを確立してください。 このインフォ グラフィックは、デジタル資産を定義する既存のアプリケーション、データ、およびインフラストラクチャから構成される出発点の概要を示しています。 移行プロセス段階で、各資産は右側のいずれかのオプションを介して移行されます。

## <a name="migration-implementation"></a>移行の実装

これらの記事では、既存の資産の大部分を Azure に移行するという同じ目標を持つ 2 つの工程の概要を説明します。 ただし、ビジネスの成果と現状は、そこに到達するために必要なプロセスに大きく影響します。 このような微妙な差異は、同様の最終状態に到達する 2 つの根本的に異なるアプローチになります。

![クラウド導入フレームワーク移行モデル](../_images/operational-transformation-migrate.png)

最終状態への移行段階で、段階的な実行をガイドするために、このモデルでは移行を 2 つの焦点となる領域に分けます。

**移行の準備:** 主に現状と望ましい結果に基づいて、大まかな移行バックログを作成します。

- **ビジネス成果:** この移行を推進する主な事業目標。
- **デジタル資産の見積り:** 移行するワークロードの数と条件の概算。
- **ロールと責任:** チームの構造、責任の分離、およびアクセス要件の明確な定義。
- **変更管理の要件:** 変更を確認および承認するために必要なリズム、プロセス、およびドキュメント。

このような初期入力が移行バックログを形作ります。 移行バックログの出力は、クラウドに移行するアプリケーションの優先順位一覧です。 この一覧がクラウド移行プロセスの実行を形作ります。 これには、時間の経過と共に、変更を管理するために必要なドキュメントの多くも追加されます。

**移行プロセス:** 各クラウド移行アクティビティは、移行バックログに関連するため、次のいずれかのプロセスに含まれます。

- **評価:** 既存の資産を評価し、その資産の移行計画を立てます。
- **移行:** クラウド内の資産の機能をレプリケートします。
- **最適化:** クラウド資産のパフォーマンス、コスト、アクセス、および運用能力のバランスを取ります。
- **セキュリティ保護と管理:** クラウド資産が継続的な運用に備えていることを確認します。

移行バックログの開発段階で収集される情報によって、各イテレーション中と機能の各リリースでクラウド移行プロセス内に必要な作業の複雑さとレベルが決まります。

## <a name="transition-to-the-end-state"></a>最終状態への移行

目標は、スムーズで部分的に自動化されたクラウドへの移行です。 移行プロセスでは、クラウド ベンダーが提供するツールを使用して、クラウド内の資産を迅速にレプリケートしてステージングします。 検証が完了すると、簡単なネットワークの変更でユーザーはクラウド ソリューションに再ルーティングされます。 多くのユース ケースで、この目標を達成するテクノロジはほとんど利用できます。 Azure には 10,000 個の VM をレプリケートできる速度を示す事例があります。

それでも段階的な移行アプローチが必要です。 ほとんどの環境では、移行対象の VM が多数ある場合、移行を成功させるには小さな作業単位に分割する必要があります。 一定期間に移行できる VM の数を制限する要因は数多くあります。 送信ネットワーク速度は、いくつかある技術的制限の 1 つです。ほとんどの制限は、変更を検証し、それに適応するビジネスの能力に課されます。

クラウド導入フレームワークの段階的な移行アプローチは、技術的およびカルチャ的な制限を反映し、ドキュメント化する段階的な計画を構築するために役立ちます。 このモデルの目的は、IT とビジネス両方のオーバーヘッドを最小限に抑えながら、移行速度を最大化することです。 以下に、移行バックログに基づく段階的な移行の実行例を 2 つ示します。

<!-- markdownlint-disable MD033 -->

<ul class="panelContent cardsZ">
<li style="display: flex; flex-direction: column;">
    <a href="./azure-migration-guide/index.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardText">
                        <h3>Azure 移行ガイド</h3>
                        <p><b>物語の概要:</b>このお客様は、1,000 台未満の VM を移行しています。 サポートされている 10 個未満のアプリケーションは、IT 組織以外のアプリケーション所有者によって所有されています。 その他のアプリケーション、VM、および関連データは、クラウド導入チームのメンバーによって所有およびサポートされています。 クラウド導入チームのメンバーは、既存のデータセンター内の運用環境に対して管理アクセス権を持っています。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<li style="display: flex; flex-direction: column;">
    <a href="./expanded-scope/index.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardText">
                        <h3>複雑なシナリオのガイド</h3>
                        <p><b>物語の概要:</b>このお客様の移行は、ビジネス、カルチャ、およびテクノロジ全体にわたって複雑です。 このガイドには、複数の特定の複雑さの課題とそれらの課題を克服する方法が含まれています。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
</ul>

<!-- markdownlint-enable MD033 -->

これら 2 つの工程は、クラウド移行に投資しているお客様の 2 つの極端な経験を表します。 ほとんどの企業では、上記の 2 つのシナリオの組み合わせが反映されます。 工程を確認した後、クラウド導入フレームワークの移行モデルを使用して、移行の会話を開始し、実際のニーズをよりよく満たすようにベースラインの体験を修正します。

## <a name="next-steps"></a>次の手順

次のどちらかの体験を選択してください。

> [!div class="nextstepaction"]
> [Azure 移行ガイド](./azure-migration-guide/index.md)
>
> [拡大された範囲ガイド](./expanded-scope/index.md)
