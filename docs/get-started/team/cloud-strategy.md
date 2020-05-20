---
title: 作業を開始しましょう。クラウド戦略チームを結成する
description: ビジネス成果と合致するようにクラウド導入の取り組みを進行させ、その成功に向けて備えをするクラウド戦略チームを結成します。
author: JanetCThomas
ms.author: janet
ms.service: cloud-adoption-framework
ms.subservice: overview
ms.topic: conceptual
ms.date: 04/04/2020
ms.openlocfilehash: a885de58423bea1cef9e2e87ef4d09f4cb6e9363
ms.sourcegitcommit: 5d6a7610e556f7b8ca69960ba76a3adfa9203ded
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/14/2020
ms.locfileid: "83399873"
---
# <a name="get-started-build-a-cloud-strategy-team"></a>作業を開始しましょう。クラウド戦略チームを結成する

クラウド導入に向けたあらゆる取り組みは、成功を収めるために、ある程度の戦略を必要とします。 このファースト ステップ ガイドは、専任のチームまたはバーチャル チーム (v チーム) を結成して確かなクラウド戦略を実現するうえで参考となります。 最初に行うべきことは、戦略チームが必要か、それとも既存のチーム メンバーで役割を分担し、クラウド戦略を遂行できるかの判断です。

クラウド戦略チームを結成して、動機とビジネス成果を明らかにすると共に、ビジネスの優先順位とクラウド導入の取り組みとの整合性を検証して、それを維持することになります。 ビジネス成果がビジネス機能に影響を与える場合は、組織全体からビジネス リーダーを招集して戦略チームに含めることをお勧めします。 クラウド戦略チームの目的は、クラウド テクノロジによって実現される具体的なビジネス成果を生み出すことです。 ビジネス成果と合致するようクラウド導入の取り組みを進行させるのは、総じてこのチームになります。 可能な限り、ビジネス成果とクラウド戦略チームの両方を、プロセスの早い段階で定義する必要があります。

![クラウド戦略チームの結成に着手する](../../_images/get-started/strategy-team-map.png)

> [!NOTE]
> この記事では、戦略ファシリテーターについて言及しています。 戦略ファシリテーターは、クラウド戦略チームが作業に着手するときに、チームの意見をすり合わせると共に、共通認識を形成するうえでの説明責任を一時的に担います。 多くの場合、クラウド導入の取り組みの成功について最も詳しく状況を把握している人物が戦略ファシリテーターです。 一般に、プログラム マネージャー、アーキテクト、コンサルタントもこの役割を担います。

## <a name="step-1-determine-if-a-cloud-strategy-team-is-needed"></a>手順 1:クラウド戦略チームが必要かどうかを判断する

クラウド戦略チームは、"クラウド戦略機能" と呼ばれる、クラウドにおいて必要な機能を遂行します。 クラウド戦略チームを結成するためには、専任のビジネス リーダー、利害関係者、プログラム マネージャーから成る所定のグループが定期的な頻度で集い、戦略を遂行してクラウド導入を推進する必要があります。

**成果物:**

- クラウド戦略チームが必要かどうかを判断します。

**成果物の完遂をサポートするうえでのガイダンス:**

通常、クラウド導入がビジネスにとって重要である場合や、継続的に経営幹部からのサポートを必要とする場合、ビジネスへのリスクを伴う場合、このチームは必須です。

<!-- markdownlint-disable MD033 -->
<br>

|                                                     |                                                                                                                            |
|-----------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------|
| **ビジネスにとって重要である:** | <li> クラウド導入が、重役レベルの役職によって把握される取り組みである。 <li> クラウド導入の取り組みが成功を収めることで、市場での立ち位置や顧客維持、収益が向上する。 <li> 導入ポートフォリオ内のプログラムが、戦略的なビジネス成果に直結する。 <li> 導入に向けた取り組みに伴うワークロードのポートフォリオが、戦略的でかつミッション クリティカルであり、複数の部署に影響を及ぼす可能性がある。 |
| **継続的に経営幹部からのサポートを必要とする:** | <li> クラウド導入の取り組みが、組織変動の算段に影響を及ぼす。 <li> クラウド導入の取り組みに伴って、多数のビジネス ユーザーによるトレーニングが別途必要になり、ビジネス機能の中断を招くおそれがある。 <li> 既存のデータセンターにこだわる事情が、既存の IT 運用チームまたはベンダーにある。 <li> 既存の IT チームが取り組みにあまり賛同していない。 |
| **ビジネスへのリスクを伴う:** | <li> 所定の期間内に移行が完了しなかった場合、市場に悪影響が生じたり、ホスティング コストが増えたりする。 <li> 導入対象として予定されているワークロードをデータの漏えいから保護する必要があり、仮にデータが漏えいすれば、ビジネスの成功や顧客のセキュリティに支障が生じる可能性がある。 <li> クラウド化に向けた取り組みの測定に用いられるメトリックがビジネス志向であり、技術的な成功への依存やリスクを形成する。 |

これらが現在の要件に該当する場合、以降の手順に従うことで、スムーズにクラウド戦略チームを結成することができます。

<!-- markdownlint-disable MD026 -->

**説明責任チーム:**

- 戦略ファシリテーターは、クラウド戦略チームの必要性を判断する責任を負います。

## <a name="what-if-i-dont-need-a-cloud-strategy-team"></a>クラウド戦略チームが不要だったらどうするか

クラウド戦略のニーズを満たすために必要な[クラウド戦略機能](../../organize/cloud-strategy.md)を改めて確認しましょう。 組織の戦略的なニーズを満たすうえで、専任のチームや v チームが必ずしもすべての組織に必要というわけではありません。 [RACI テンプレート](https://archcenter.blob.core.windows.net/cdn/fusion/management/raci-template.xlsx)で戦略の主要な説明責任を列挙し、それぞれチーム内のだれが担当するかを確認します。 そのすべての説明責任を 1 人の担当者が負う場合は単に、RACI テンプレート内の "クラウド戦略" をその人物の名前に置き換えてください。

## <a name="step-2-establish-the-cloud-strategy-team"></a>手順 2:クラウド戦略チームを結成する

クラウド戦略チームは、ビジネス リーダーと IT リーダーとの間に立って、定期的にすり合わせを行う役目を果たします。 重要度やリスクの度合いによって、また、戦略チームのニーズを後押しする経営幹部のサポートのレベルによって、チームの参加や構成メンバーは変わる可能性があります。

**成果物:**

- クラウド導入戦略の説明責任と実行責任を積極的に共有する適切な組織または個人を特定します。

**成果物の完遂をサポートするうえでのガイダンス:**

- 専任のチームがあることで恩恵を受け、また戦略の推進に貢献しうる利害関係者を明確にするために、前の手順 1. の根拠を文書化し、共有します。
- だれが適任かについては、[クラウド戦略の機能](../../organize/cloud-strategy.md)に関するページを参照してください。
- この機能の[最小スコープ](../../organize/cloud-strategy.md#minimum-scope)と[成果物](../../organize/cloud-strategy.md#deliverable)を確認して、想定される関係者それぞれの整合性と労力的余地を検証します。
- さまざまな [RACI の構成例](../../organize/raci-alignment.md#strategic-alignment)を確認し、現在のチーム構造に基づいて適切な RACI チャートを作成します。 または、RACI テンプレートに用意されているいずれかのサンプル タブを使用してください。
- その結果を [RACI テンプレート](https://archcenter.blob.core.windows.net/cdn/fusion/management/raci-template.xlsx)の [Org Alignment]\(組織配置\) タブで文書化します。

**説明責任チーム:**

- クラウド戦略チームの結成についての説明責任は、戦略ファシリテーターが負います。

## <a name="step-3-establish-a-cadence"></a>手順 3:頻度を確立する

クラウド導入の取り組みの初期段階で、このチームは、意思の疎通と反復的な戦略レビューを頻繁に行う必要があります。 導入作業が始まるとその頻度は減っていき、バックログ優先順位の状態と検証 (または調整) へと重点が移ることになります。

以下の手順 4.、手順 5.、手順 6. は、4 週間から 6 週間以内に完了する必要があります。 残りの手順は、後続のミーティングで遂行することになります。 チームによって手順 7. が開始されるまでは、通常よりも多いミーティング頻度を維持する必要があります。

**成果物:**

推奨されるミーティングの頻度を確認したうえで、戦略チームのメンバー全員とのミーティングをスケジュールします。

**成果物の完遂をサポートするうえでのガイダンス:**

- 記入したそれぞれのメンバーの認識を一致させるために、推奨される短期的および長期的な[ミーティング頻度](../../organize/cloud-strategy.md#deliverable)を確認します。

**説明責任チーム:**

- クラウド戦略チームのミーティング頻度の確立についての説明責任は、戦略ファシリテーターが負います。

## <a name="step-4-establish-a-motivation-driven-strategy"></a>手順 4:動機主導の戦略を確立する

クラウド導入の取り組みには、移行とイノベーションの両方のアプローチがあります。 技術チームが戦略を設定するとき、戦略は、そのチームが現在有しているスキルや強みによって決まるのが一般的です。 そのような戦略は、技術的には成功を収める可能性が高いものの、ビジネス インパクトが限定的なものになるリスクをはらんでいます。

クラウド戦略チームの最初の目的は、ビジネスの動機に基づいて戦略の概略を設定することです。 これは通常、クラウド戦略チームのメンバー全員との 1 時間のワークショップで完了することができます。 また、さまざまな技術チームおよび対象となる利害関係者と一緒に、さらに 1 時間以上かけて動機を確認することも必要です。

最初のワークショップ時に、チームの各メンバーが動機に優先順位を付け ([動機の把握](../../strategy/motivations.md)に関する記事を参照)、その最優先事項を共有する必要があります。 移行またはイノベーションの方向でテーマが見えてくるまで、戦略ファシリテーターが主導で、少なくとも 1 回、会話の場を設けるようにします。 動機は、両方のカテゴリの上位 3 件のリストにある可能性が高く、明確なパターンがどちらか一方に傾くまでには、チームがそのリストを深く掘り下げる必要があります。

**成果物:**

チーム メンバーの足並みを揃えるうえで役立つ会話が、この作業を通じて引き出されます。 この成果物は、残りの戦略と後続の計画の指針となります。

- [戦略と計画のテンプレート](https://archcenter.blob.core.windows.net/cdn/fusion/readiness/Microsoft-Cloud-Adoption-Framework-Strategy-and-Plan-Template.docx)に動機を記録します。

**成果物の完遂をサポートするうえでのガイダンス:**

- [動機を把握する](../../strategy/motivations.md): 重要なビジネス イベントや一部の移行の動機は、コスト重視となる傾向があり、その後のすべての取り組みでコスト管理の重要性が大きくなります。 一方、イノベーションまたは移行による成長に関連した未来志向の動機もあり、その場合には、より営業収益に重点が置かれます。 動機を把握することで、コスト管理にどの程度の優先度を置くべきかが理解しやすくなります。

<!-- markdownlint-disable MD033 -->
<br>

| 説明責任チーム | 実行責任チームとサポート チーム |
| --- | --- |
| <li> クラウド戦略チーム | 次のチームと一緒に動機をレビューし、戦略の全体的方向を確認します。 <li> クラウド ガバナンス チーム <li> クラウド導入チーム <li> クラウドのセンター オブ エクセレンスまたは中央 IT |

## <a name="step-5-establish-business-outcomes"></a>手順 5:ビジネス成果を確立する

クラウド戦略チームの各メンバーは、ビジネスの成功を測定するための具体的なメトリックと共に、ビジネス成果を少なくとも 1 つ定義するよう求められます。 そのメトリックを、クラウド導入の取り組みの直接の結果として改善できる場合、チーム メンバーは、予想される影響を共有するよう求められます。 クラウド導入の取り組みがメトリックに影響しない場合でも、そのメトリックをビジネスが後押しできるようなら、そのことも文書化する必要があります。

通常、クラウド戦略チームのリーダーの多くは、その核となるメトリックを分解して、クラウド導入の取り組みによって影響を受ける可能性のある成果を特定しなければなりません。 その取り組みによってチーム メンバーの成果が変わらないようなら、プログラムへの関心の度合いを維持することは難しいでしょう。 ファシリテーターは各リーダーと連携して、一線化されたメトリックを策定し、チーム メンバーが戦略チームの一員として適任であるかどうかを再評価する必要があります。

ビジネス成果に反応が現れるまでに時間がかかる場合もあります。 そうした類の変化は、技術の変化に比べて進行が遅いのが一般的です。 透明性を維持するために、戦略チームは、より短期的な学習メトリックを取りまとめる必要があります。 そうしたメトリックには、それぞれのチーム会議でレビューすることによって技術的な目標やビジネス成果に向けた進捗を実証できるような変化 (技術的変化など) が挙げられます。

**成果物:**

- クラウド戦略チームのメンバーごとに、期待される成果を少なくとも 1 つ割り出します。
- 期待される時間の必達目標と期待される成果を一線化するために、メンバーのリストを絞り込みます。
- 継続的に進行状況をレポートできるよう一連の短期的メトリックと中期的メトリックを整理します。

**成果物の完遂をサポートするうえでのガイダンス:**

- [戦略と計画のテンプレート](https://archcenter.blob.core.windows.net/cdn/fusion/readiness/Microsoft-Cloud-Adoption-Framework-Strategy-and-Plan-Template.docx)にビジネス成果を記録します。
- [ビジネス成果](../../strategy/business-outcomes/index.md):いくつかの財務上の成果は、極度にコスト重視となる傾向があります。 目標とする成果が財務メトリックと対応関係にある場合は、極力早い段階で Cost Management のガバナンス規範に投資するのが賢明でしょう。
- [メトリックの学習](../../strategy/learning-metrics.md):ビジネス成果と導入の技術的な取り組みとの間に存在するギャップは、学習メトリックを利用して埋めることができます。

**説明責任チーム:**

ビジネス成果を、その成功を測定するための具体的なメトリックと共に定義する作業については、クラウド戦略チームが説明責任を負います。

## <a name="step-6-make-a-decision-to-proceed-or-cancel-based-on-the-business-justification"></a>手順 6:業務上の正当な理由に基づいて、計画を続行するか中止するかの判断を下す

業務上の正当な理由は、計画、長期的なリターンの予想、総保有コスト (TCO) の予想に寄与します。 この手順でクラウド戦略チームは、計画を進める判断をチームですり合わせるために最低限、どの程度の規模の分析が必要かについて合意する必要があります。 戦略的なすり合わせには、踏み込んだ計画と TCO 分析が必要になる場合もあります。 ほとんどのクラウド戦略チームは、単純なコスト分析で十分に方向性を一致させることができるでしょう。

戦略チームの各メンバーは、サポート チームに期待される具体的な分析をスムーズに伝えるために、業務上の正当な理由に対する一般的な誤解やアプローチを確認する必要があります。 期待される分析が伝われば、戦略チームは、費やす時間とミーティングの頻度を減らすことができます。 ただし、業務上の正当な理由とデジタル資産分析の両方について見解が一致するまでは、チームは引き続き、戦略を遂行するうえでの説明責任を負います。

**成果物:**

- サポート チームと一緒に、業務上の正当な理由を提示する作業を開始します。
- クラウド導入計画を続行するか中止するかの判断について戦略チームの意見が一致するまで、サポート チームと毎月 (または必要に応じて) ミーティングを行います。

**成果物の完遂をサポートするうえでのガイダンス:**

- [業務上の正当な理由](../../strategy/cloud-migration-business-case.md): 業務上の正当な理由は、クラウド導入の財務計画全体の概要を示します。 これは、最初の予算作成に役立つ情報源となる可能性があります。

<!-- markdownlint-disable MD033 -->
<br>

| 説明責任チーム | 実行責任チームとサポート チーム |
| --- | --- |
| <li> クラウド戦略チーム | <li> クラウド ガバナンス チーム <li> クラウド導入チーム <li> クラウドのセンター オブ エクセレンスまたは中央 IT |

## <a name="step-7-support-adoption-through-a-regular-rhythm"></a>手順 7:定期的に導入をサポートする

クラウド戦略チームは、戦略を進める判断で合意したら、作業のペースを落とし、頻度を減らしていくことができます。 この時点でチームの期待も変わっていきます。 取り組みの内容が戦略的な定義から導入作業 (計画、準備、導入) へと移ったら、戦略チームは、優先順位付けと戦略的なサポートに重点を置く必要があります。

**成果物:**

- **優先順位付け:** 既存のデジタル資産が正当化されるときは、最大のビジネス価値を生み出すアクションに技術導入チームが専念できるよう、戦略チームは、移行またはイノベーションが優先されるような機運を高めるべく行動します。
- **リスクを評価する:** クラウド導入が広がるにつれて、新しい導入形態から、今までになかったリスクが顕在化します。 そうした新たなリスクを評価する責任は戦略チームにあります。 新たなリスクを評価し、ビジネスがそのリスクに耐えられるのか、それともリスクを防ぐためのポリシーが必要であるのかの判断が戦略チームには求められます。
- **予算と支出を確認する:** クラウド導入が拡大するにつれ、ポートフォリオに含まれるさまざまなワークロードの予算も増えていきます。 予算に対する実際の支出を毎月、クラウド戦略チームが確認し、解決すべき問題を突き止める必要があります。 予算の変更を早期に把握して対処することが、導入ライフサイクルの後の段階になって想定外の費用が発覚するのを防ぐことにつながります。
- **ビジネス計画:** 導入チームによる移行またはイノベーションに向けた取り組みが完了したら、新しいテクノロジ ソリューションからのリターンを最大化するために、さらなるビジネス計画が必要になります。 エンド ユーザーのトレーニングやビジネス プロセスの変更など、導入後の作業がそれに該当します。
- **経営幹部からのサポート:** クラウド導入には組織の変化が伴います。 それが最も顕著に現れるのは IT 組織です。 変更を把握したり、新しいスキルを身に付けたり、新たなモデルでの最善の運用方法を理解したりするために、さまざまなチームやそのメンバーに、戦略チームから別途サポートを提供しなければならない場合もあります。

**成果物の完遂をサポートするうえでのガイダンス:**

- [増分型の合理化](../../digital-estate/rationalize.md):遅延バインディングの技術的な決定を適切に調整する合理化に対するアジャイルなアプローチです。
- [合理化の 5 R](../../digital-estate/5-rs-of-rationalization.md):合理化のさまざまなオプションについて理解します。


<!-- markdownlint-disable MD033 -->
<br>

| 説明責任チーム | 実行責任チームとサポート チーム |
| --- | --- |
| <li> クラウド戦略チーム | <li> クラウド ガバナンス チーム <li> クラウド導入チーム <li> クラウドのセンター オブ エクセレンスまたは中央 IT |

## <a name="whats-next"></a>参照トピック

戦略と計画は重要です。 ただし、クラウド導入機能がなければ、何も実施できません。 導入作業を開始する前に、この重要な機能の役割について理解してください。

クラウド導入機能を担当する導入チーム (または個人) と連携して、戦略と[クラウド導入機能](../../organize/cloud-adoption.md)との整合性を確保します。

"実行責任、説明責任、助言、通知" (RACI) の各パーティを明確にするクロスチーム マトリックスを作成することによりチーム間での責任を調整する方法について説明します。 [RACI スプレッドシート テンプレート](https://archcenter.blob.core.windows.net/cdn/fusion/management/raci-template.xlsx)をダウンロードして編集してください。