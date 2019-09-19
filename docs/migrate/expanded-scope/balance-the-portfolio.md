---
title: ポートフォリオのバランスを取る
titleSuffix: Microsoft Cloud Adoption Framework for Azure
description: ポートフォリオのバランスを取る
author: BrianBlanchard
ms.author: brblanch
ms.date: 04/04/2019
ms.topic: guide
ms.service: cloud-adoption-framework
ms.subservice: migrate
ms.openlocfilehash: 1227b798972ce7e139181c9267a1a1e860390029
ms.sourcegitcommit: a26c27ed72ac89198231ec4b11917a20d03bd222
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/06/2019
ms.locfileid: "70836709"
---
# <a name="balance-the-portfolio"></a>ポートフォリオのバランスを取る

クラウドの導入は、技術的実装として巧妙に見せ掛けたポートフォリオ管理作業です。 ポートフォリオ管理の実行と同様に、ポートフォリオのバランスは成功に不可欠な要素です。 戦略的レベルで、これは、クラウドを最大限に活用するために、移行、イノベーション、実験のバランスを取ることを意味します。 クラウド導入作業が一方向に傾きすぎると、移行作業に複雑さが加わります。 この記事では、ポートフォリオのバランスを実現するためのアプローチをわかりやすく説明します。

## <a name="general-scope-expansion"></a>全般的な範囲の拡大

このトピックは本質的に戦略的です。 そのため、この記事で採用されているアプローチも等しく戦略的です。 戦略をデータドリブンの意思決定に基づかせるため、この記事では、読者が既存の[デジタル資産](../../digital-estate/index.md)を評価している (またはその作業中である) ことを前提としています。 このアプローチの目標は、ワークロードの評価をサポートし、定性的質問とポートフォリオの改善によって、ポートフォリオ間の適切なバランスを確保することです。

### <a name="documenting-business-outcomes"></a>ビジネスの成果の文書化

ポートフォリオのバランスを取る前に、クラウド移行作業を推進するビジネスの成果を文書化し、共有することが重要です。 クラウド移行に関する一般的なビジネスの成果のいくつかの例については、[クラウド移行の概要](../../getting-started/migrate.md)に関するページを参照してください。

次の表は、目的のビジネスの成果を文書化し、共有するために役立つ可能性があります。 ほとんどの企業では同時に複数の成果を追求していることに注意する必要があります。 この演習の重要性は、クラウド移行作業に最も直接的に関連する成果を明らかにすることです。

|結果  |測定基準  |目標  |期間  |この作業の優先順位  |
|---------|---------|---------|---------|---------|
|IT コストの削減     |データセンターの予算         |200 万ドルの削減         |12 か月         |1 番         |
|データセンターの撤退     |データセンターからの撤退         |2 データセンター         |6 か月         |2 番         |
|ビジネスの機敏性の向上     |市場投入までの時間の短縮  |デプロイ時間を 6 か月短縮         |2 年間         |3 番        |
|カスタマー エクスペリエンスの向上     |顧客満足度 (CSAT)         |10% 向上         |12 か月         |4 番         |

> [!IMPORTANT]
> 上の表は、架空の例であり、優先順位の設定に使用しないでください。 多くの場合、この表は、コスト削減をカスタマー エクスペリエンスの上に位置付けることで、アンチパターンと見なすことができます。

上の表では、クラウド戦略チームと、クラウド移行を監督するクラウド導入チームの優先事項が正確に表されています。 短期的な制約のため、このチームは IT コストの削減に重点を置き、データセンターからの撤退を、目的の IT コスト削減を達成するための手段として優先しています。 しかし、この表で競合する優先事項を文書化することによって、クラウド導入チームは、クラウド戦略チームが包括的なポートフォリオ戦略の実装をうまく調整する機会を見極められるように支援できます。

### <a name="move-fast-while-maintaining-balance"></a>バランスを維持しながらすばやく動く

[デジタル資産の漸進的合理化](../../digital-estate/index.md)に関するガイダンスでは、バランスが取れていない位置から合理化を開始するアプローチを推奨しています。 クラウド戦略チームは、リホスト アプローチとの互換性について、すべてのワークロードを評価する必要があります。 そのようなアプローチは、定量的データに基づいた複雑なデジタル資産の速やかな評価が可能になるため推奨されます。 初めにこのような仮定を立てることにより、クラウド導入チームはすばやく取り掛かることができ、ビジネスの成果を得るまでの時間を短縮できます。 しかし、その記事に説明されているように、定性的質問によって、ポートフォリオに必要なバランスが提供されます。 この記事では、約束されたバランスを作成するためのプロセスを説明します。

### <a name="importance-of-sunset-and-retire-decisions"></a>サポート終了と廃止の決断の重要性

上記の[ビジネスの成果の文書化](#documenting-business-outcomes)セクションの表では、1 番の目標である IT コストの削減に役立つ可能性がある重要な成果が上げられていません。 IT コストの削減が、ビジネスの成果の一覧のどこにランク付けされていても、ワークロードの終了と廃止の可能性を検討することが重要です。 一部のシナリオでは、コストの削減は、短期的な投資に値しないワークロードを移行しないことからもたらされる可能性があります。 一部のお客様は、使用率が低いワークロードの廃止によって、20% を超える合計コスト削減のコスト削減を報告しています。

サポート終了や廃止の決断を適切に反映しているポートフォリオのバランスを取るために、クラウド戦略チームとクラウド導入チームが評価および移行プロセス内で各ワークロードについて次の質問をすることをお勧めします。

- ワークロードは過去 6 か月以内にエンドユーザーによって使われましたか?
- エンドユーザーのトラフィックは一定していますか、増加していますか?
- このワークロードは、今後 12 か月間ビジネスで必要とされますか?

これらのいずれかの質問の答えが "いいえ" の場合、ワークロードは廃止の候補となる可能性があります。 廃止の可能性がアプリケーション所有者によって確認された場合、ワークロードを移行しても意味がないと考えられます。 これにより、いくつかの適格性の質問が発生します。

- このワークロードに対して、廃止またはサポート終了の計画を立てることができますか?
- このワークロードは、データセンターからの撤退の前に廃止できますか?

これらの両方の質問の答えが "はい" である場合に、ワークロードを移行_しない_ことを検討することが賢明であるといえます。 このアプローチは、コストを削減し、データセンターから撤退するという目標の達成に役立つことがあります。

いずれかの質問に対する答えが "いいえ" の場合は、ワークロードを廃止できるようになるまで、ホストする計画を立てることが賢明であると考えられます。 この計画には、低コストのデータセンターまたは代替のデータセンターへの資産の移動を含めることができ、それによってもコストの削減と 1 つのデータセンターの撤退の目標が達成できることになります。

## <a name="suggested-prerequisites"></a>推奨される前提条件

ベースライン ガイドに指定された前提条件は、この複雑なトピックに対処する場合でも十分なはずです。 ただし、それらの前提条件の中で、資産インベントリとデジタル資産を強調する必要があります。そのデータが次のアクティビティにつながるためです。

## <a name="assess-process-changes"></a>プロセス変更の評価

ポートフォリオのバランスを取るには、評価プロセス中に追加の定性分析が必要であり、これはシンプルなポートフォリオ合理化の推進に役立ちます。

### <a name="suggested-action-during-the-assess-process"></a>評価プロセス中に推奨されるアクション

上記の[ビジネスの成果の文書化](#documenting-business-outcomes)セクションの表のデータに基づくと、ポートフォリオが移行を重視した実行モデルに傾きすぎるというリスクの可能性があります。 カスタマー エクスペリエンスが最優先事項である場合、イノベーションが頻繁なポートフォリオでその可能性が高くなります。 どちらが正しくどちらが誤りでもありませんが、一方向に傾きすぎると、一般に利益が減少し、不要な複雑さが加わり、クラウド導入作業に関連する実行時間が長くなります。

複雑さを軽減するため、ポートフォリオ合理化の従来のアプローチに、ただし反復モデルで従うことをお勧めします。 次の手順では、このようなアプローチへの定性的モデルを示しています。

- クラウド戦略チームは、移行されるワークロードの優先順位付きのバックログを管理します。
- クラウド戦略チームとクラウド導入チームは、各リリースの完了前にリリース計画会議を主催します。
- リリース計画会議で、チームは優先順位付きのバックログの上位 5 位から 10 位のワークロードに同意します。
- リリース計画会議外で、クラウド導入チームは、アプリケーション所有者と領域の専門家の次の質問を尋ねます。
  - このアプリケーションは、同等のサービスとしてのプラットフォーム (PaaS) に置き換えることができますか?
  - このアプリケーションは、サード パーティ アプリケーションですか?
  - 今後 12 か月間でアプリケーションの継続的な開発に投資する予算が承認されていますか?
  - このアプリケーションの追加の開発によって、カスタマー エクスペリエンスが向上しますか? 競争上の差別化要素を作成しますか? ビジネスの収益の追加を促進しますか?
  - このワークロード内のデータは、BI、機械学習、IoT、または関連テクノロジに関連するダウンストリームのイノベーションに寄与しますか?
  - ワークロードは、Azure App Service などの最新のアプリケーション プラットフォームと互換性がありますか?
- 上記の質問の回答と他の必要な定性分析は、優先順位付きのバックログへの調整に影響することがあります。 これらの調整には次のようなものが含まれます。
  - ワークロードを PaaS ソリューションで置き換えることができる場合、移行バックログから完全に削除できます。 少なくともリホストと置き換えの決断のための追加のデュー デリジェンスがタスクとして追加され、移行バッグログから一時的にそのワークロードの優先順位が低下します。
  - ワークロードが開発の進行中である (または進行させる必要がある) 場合は、リファクタリング/リアーキテクト/再構築モデルに最も適合する可能性があります。 イノベーションと移行では、異なる技術的スキルが必要であるため、リファクタリング/リアーキテクト/再構築アプローチに合わせるアプリケーションは、移行バックログと対照的に、イノベーション バックログによって管理することをお勧めします。
  - ワークロードがダウンストリーム イノベーションの一部である場合、データ プラットフォームをリファクタリングすることは理にかなっている可能性がありますが、アプリケーション レイヤーをリホストの候補のままにします。 ワークロードのデータ プラットフォームのマイナーなリファクタリングは、多くの場合に移行またはイノベーション バックログで対処できます。 この合理化の結果によって、バックログの作業項目が詳細になる可能性がありますが、優先順位の変更はありません。
  - ワークロードが戦略的ではないが、最新のクラウド ベースのアプリケーション ホスティング プラットフォームと互換性がある場合は、アプリケーションに対してマイナーなリファクタリングを実行して、それを最新のアプリとしてデプロイすることが賢明であると考えられます。 これは、クラウド移行の IaaS と OS の全体的なライセンス要件を削減することで、全体的な節約に寄与する可能性があります。
  - ワークロードがサード パーティ アプリケーションで、そのワークロードのデータがダウンストリームのイノベーションで使用するように計画されていない場合は、バックログにリホスト オプションとして残しておくことが最善であると考えられます。

これらの質問は、各ワークロードについて実行される定性分析の範囲にすべきではありませんが、バランスが取れていないポートフォリオの複雑さに対処するために役立つ会話を導くことを意図しています。

## <a name="migrate-process-changes"></a>移行プロセスの変更

移行時に、ポートフォリオのバランスを取るアクティビティによって、移行の速度 (資産が移行されるスピード) にマイナスの影響を与える可能性があります。 次のガイダンスでは、移行作業の中断を回避するために作業を調整する理由と方法について説明しています。

### <a name="suggested-action-during-the-migrate-process"></a>移行プロセス中に推奨されるアクション

ポートフォリオの合理化には、多様な技術的作業が必要です。 クラウド導入チームにとっては、移行作業内のポートフォリオの多様性に対応したくなります。 ビジネス利害関係者は、1 つのクラウド導入チームに移行バックログ全体に対処することを求めます。 これはほとんど賢明なアプローチとはいえず、多くの場合に、非生産的になる可能性があります。

これらの多様な作業を複数のクラウド導入チームで分担することをお勧めします。 2 つのチーム モデルを実行モデルの例として使用して、チーム 1 を移行チームとし、チーム 2 をイノベーション チームとします。 より大規模な作業の場合は、これらのチームをさらに分割して、置換/PaaS作業やマイナー リファクタリングなどの他のアプローチに対処できます。 次に、リホスト、リファクタリング、またはマイナー リファクタリングに必要なスキルとロールについて説明します。

**リホスト:** リホストでは、チーム メンバーがインフラストラクチャに重点を置いた変更を実装する必要があります。 一般に Azure Site Recovery などのツールを使用して、VM や他の資産を Azure に移行します。 この作業は、データセンター管理者や IT 実装者に適しています。 クラウド移行チームは、この作業を大規模に提供するために適切に構成されます。 これは、ほとんどのシナリオで既存の資産を移行するための最も高速なアプローチです。

**リファクタリング:** リファクタリングでは、チーム メンバーがソース コードの変更、アプリケーションのアーキテクチャの変更、または新しいクラウド サービスの導入を行う必要があります。 一般にこの作業では、Visual Studio などの開発ツールと Azure DevOps のようなデプロイ パイプライン ツールを使用して、最新のアプリケーションを Azure に再デプロイします。 この作業は、アプリケーション開発ロールまたは DevOps パイプライン開発ロールに適しています。 クラウド イノベーション チームは、この作業を提供するために最適に構成されます。 このアプローチで、既存の資産をクラウド資産で置き換えるために長時間かかることがありますが、アプリではクラウド ネイティブの機能を利用できます。

**マイナー リファクタリング:** 一部のアプリケーションは、データまたはアプリケーション レベルでのマイナー リファクタリングによって最新化できます。 この作業では、チーム メンバーがクラウドベースのデータ プラットフォームにデータをデプロイするか、またはアプリケーションにマイナーな構成変更を適用する必要があります。 これには、データまたはアプリケーション開発分野の専門家の限定的なサポートが必要になることがあります。 しかし、この作業は、IT 実装者によってサードパーティ アプリをデプロイする場合に行われる作業に似ています。 この作業は、クラウド移行チームまたはクラウド戦略チームに適しています。 この作業は、リホスト移行ほど高速でありませんが、リファクタリング作業を実行するより時間がかかりません。

移行時に、作業を上記の 3 つの方法で分割し、それらの作業を適切な繰り返しで適切なチームが実行することをお勧めします。 ポートフォリオを多様化することをお勧めする一方、作業の焦点をそらさないまま、分離させることもお勧めします。

## <a name="optimize-and-promote-process-changes"></a>最適化および昇格プロセスの変更

移行作業内の最適化および昇格プロセス中に、追加の変更は必要ありません。

## <a name="secure-and-manage-process-changes"></a>セキュリティ保護および管理プロセスの変更

移行作業内のセキュリティ保護および管理プロセス中に、追加の変更は必要ありません。

## <a name="next-steps"></a>次の手順

[拡張スコープ チェックリスト](./index.md)に戻り、移行方法が完全に調整されていることを確認します。

> [!div class="nextstepaction"]
> [拡張スコープ チェックリスト](./index.md)