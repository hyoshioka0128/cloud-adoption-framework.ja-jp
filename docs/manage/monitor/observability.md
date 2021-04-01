---
title: クラウド監視の可観測性
description: 可観測性がどのように監視の成熟度を促進し、クラウド内で実行されているご自身のサービスの動作を理解するうえで役立つかを把握します。
author: mgoedtel
ms.author: magoedte
ms.date: 03/22/2021
ms.topic: guide
ms.service: cloud-adoption-framework
ms.subservice: operate
ms.openlocfilehash: d6f877cd0d5fd0fef46550e186ab4eebf512a5d3
ms.sourcegitcommit: a4b5340bf75c70d9f7d9f5617dfca1dcc0eebada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104893628"
---
# <a name="cloud-monitoring-guide-observability"></a>クラウド監視ガイド: 可観測性

この記事の目的は、"*可観測性*" が監視ソリューションごとに Azure ランディング ゾーン (つまり、実用最小限のすべての製品) 内に確実に確立されるようにして、組織が一貫した監視戦略をより迅速に実装できるようにすることです。

プランナーとして、サービスの監視計画を策定し、監視目標をいくつか追加します。 必要な目標の 1 つが、監視コンシューマー向けに計画しているソリューションに対する安心感または信頼感を、できるだけ早くそのコンシューマーに持ってもらうことです。 その後、レポートやカスタム ダッシュボードの作成など、他の目標に進むことができます。

これを "*導入*" と呼んでいます。

この記事の目標は、[監視戦略](../../strategy/monitoring-strategy.md)の推奨事項を採用し、監視計画を作成して、組織の IT 運用戦略を最新化して進化させることです。 2 番目の目標は、観察し、継続的に反復しながら運用の成熟度を高め、これらのサービスの監視方法を改善することです。

> [!NOTE]
> この記事で、**監視ソリューション** とは、クラウド内でサービスを監視する運用ユニットのことです。  一方、**監視対象** とは、監視されているサービスです。 ソリューションには、ツール、監視データ、アラート、応答の種類、回復アクション、視覚化の種類、ロールベースのアクセスなど、監視に関するあらゆる側面が含まれます。

## <a name="why-observability-is-important"></a>可観測性が重要な理由

簡単に言うと、可観測性は、"*最初*"、監視コンシューマーに、サービスの "*通常*" の操作として見なされるもの、または認識されるものを把握するよう促します。 つまり、あなたはできるだけ早く "*全体の可視性*"、つまり主要な監視原則を求めます。 最初の可観測性が完了したら、その初期レベルの可視性に基づいて、アクションにつながるアラートの開発、有用なダッシュボードの作成、および AIOps ソリューションの評価を行います。 これにより、基になるメトリックとログ監視データに慣れるための時間を確保することができます。

>[!NOTE]
>これは、過去にチームがビルド、テスト、およびデプロイの前に、まず紙の上ですべての監視要件を定義する作業を行っていたときのものとは逆のアプローチです。

計画 (つまり、目標) のターゲットが、Key Vault などの Azure リソースの種類でも、リソース グループ全体でも、さらには Microsoft 365 Exchange Online でも、まずは "*可観測性を確立*" する必要があります。  

このアプローチにより、計画も簡略化されます。 どのような場合でも、全体的な可視性とは、次の 3 次元、つまり 3 つの側面にわたって十分な可視性を実現し、維持することを意味します。

* 詳細な監視
* 広範な監視
* 正常性モデル全体の監視

![3 面の立方体の例](../../strategy/media/monitoring-strategy/three-sided-cube.png)

観察は IT だけの問題ではなく、その目的は、エンド ユーザーが使用できるようにすること、そしてビジネスの期待に応えることです。 理解していないことや、知らないものを監視することはできません。その結果、ビジネスに対して約束したレベルのサービス可用性を提供できなくなります。 クラウド コンピューティングが登場する前に Microsoft が強調していたのは、アプリケーションの設計および開発時の障害モードの分析です。 障害モードの分析は、コード内でいつどのように論理エラーやその他の重大なエラーが発生する可能性があるかを、開発者が検討するうえで役立ちました。 そして、それが発生した場合は、その状況を有意義な方法で公開し、監視ツールが検出して、対処できるようにするだけでなく、アプリケーションの動作をより良く理解し、データ主導の意思決定を行う際に役立つ情報を、開発者、オペレーター、またはシステム エンジニアに提供できるようにします。 現在、[クラウド導入フレームワーク](/architecture/resiliency/failure-mode-analysis)では、アーキテクチャおよび設計フェーズの一環としてそのプロセスに従い、ご自身の設計に Azure サービスの復旧を組み込むことが推奨されています。

要するに、これが早い段階で実用最小限の製品内に必要な可観測性です。

## <a name="monitoring-systems-and-observability"></a>監視システムと可観測性

インフラストラクチャとアプリケーションの監視は、常に複雑です。クラウド コンピューティングが導入されても、その複雑さは変わりません。
ビジネス変革はテクノロジを適用することで最新の戦略を遂行し、将来の戦略形成を支援します。 クラウドは、監視の複雑な性質にさらに影響を与えています。

これは、次のように示されます。

* ビジネス (デジタル) 変革の取り組みが、クラウド テクノロジのハイパーエクスプロイトに移行している。

* オンプレミスで管理する切り離されたツールに対して、監視機能は Azure リソースおよびリソース グループに埋め込まれるようになった。

* クラウドネイティブの監視アーキテクチャが SIEM に似ている (Azure Monitor など)。 拡張性に優れ、ログドリブンで、かつ桁違いに柔軟になった。

アーキテクトにとって診断は、コスト効率に優れたクラウドネイティブ監視構造の活用の核を成すもので、IT 部門がサービスを、さまざまなクラウド モデルの枠を越えて総合的に管理できるようにします。

アーキテクトは、オペレーターと同様、IT インフラストラクチャ コンポーネントやアプリケーションが生成する診断情報を理解する必要があります。 価値あるインテリジェンスに対する、多変量、動的、時系列、イベントフル、ステートフル、およびテレメトリのログ ストリームの融合を作り出すには、次のことが重要です。

* 開発者またはシステム エンジニアの知識と経験。監視対象を深く理解している必要があります。  

* 実際のサポートやトラブルシューティングの経験。データを使って問題を見つける、原因を特定するなど。

* 過去のインシデントを確認して、後で自動化 (自動修復) が可能なテクノロジ以外の理由を見つける。

* ソフトウェアまたはハードウェアのベンダーによるドキュメント、ソフトウェア、トレーニング、またはコンサルティング形式のガイダンス。

Microsoft とそのパートナーは、System Center Operations Manager に慣れている方を対象に *管理パック* をご用意しています。  管理パックはテクノロジ固有です。たとえば、SQL 管理パックをインポートすると、Operations Manager によって、SQL Server をホストしているサーバーが自動的に検出され、そのサーバーを対象として監視が開始されます。 ここでは、可観測性が、多かれ少なかれ、Microsoft および数十の業界の製品エンジニアによって事前定義されています。 Operations Manager を使用している場合、東西南北の依存関係を気にする必要がないため、SQL の正常性の観察が、ネットワーク、仮想化、アプリケーションを含む、より大きな IT サービスの一環として行われます。 Operations Manager は、見慣れた 4 部構成の "*正常性モデル*" に基づく共通のスキーマにより、オンプレミスのインフラストラクチャ向けに設計されています。 インフラストラクチャ サービス アーキテクチャは、クラウド サービスに比べて、コンポーネントやアーキテクチャのパターン内で **修正** される傾向があります。

クラウド内では、必要となるサービスの種類が非常に柔軟です。 それが時間の経過と共にどのように変化するかが監視に含まれ、それは動的でグローバル、かつ回復性がある可能性があります。  クラウド アーキテクトは、オンプレミスの固定概念にとらわれていません。 Operations Manager の参加が可能ですが、やはりその強みは、従来のオンプレミスのインフラストラクチャとアプリケーションです。 これに対し、Azure Monitor のアーキテクチャは、3 つのクラウドモデルすべてをサポートしており、柔軟性がはるかに優れています。 Azure Monitor で可観測性の目標を達成するために、リソース、そのリソースを地理的に配置する場所、コンポーネントの連携の視覚化方法などを、より自由に決定することができます。

Azure Monitor を使用すると、[分析情報](/azure/azure-monitor/monitor-reference)に含まれる既存のブックを利用できます。これにより、Operations Manager の管理パックと同様の機能が提供されます。 それ以外の場合、既知の障害や、潜在的な障害を示す症状を監視および検出する方法を把握するには、Azure サービスごとに Azure のドキュメントを確認する必要があります。

## <a name="monitoring-disciplines"></a>監視の規範

次の図は観察規範を示しています (左側)。このセクションの後半では、Microsoft Azure がここでどのように中心的な役割を果たしているかを説明します。

![監視の規範](./media/observability/monitoring-and-control-disciplines.png)

## <a name="observability-and-response"></a>可観測性と応答

**可観測性** とは、"*監視コンシューマーが定義済みサービスを十分なレベルで制御するのを、監視ソリューションが支援していることを示す定性的な指標です*"。 監視することで、サービス コンシューマーに対して、適切な範囲の監視の "*機能*" と "*観点*" を提供します。

**応答** とは、観測された監視データ (検出されたイベント、テレメトリ パターン、相関情報など) の "*重要性*" を迅速に確認する組織の能力であり、否定的なイベントが発生した場合は、すばやく復元または修復できます。 また、肯定的なイベントや情報イベントは、サービス契約の維持、信頼性の向上、サポート コストの削減に役立ちます。 システムまたはサービスではイベントが動的に発生します。そこで、監視ソリューションが、アラートを提供したり、ループの自動化を制御したりするほか、外部 IT サービス マネジメント (ITSM) システム内でインシデント、問題、または変更を発生させます。 イベントの多くは自動修復できないこと、または自動修復するべきではないことに注意してください。

**ユーティリティ** は、この意味で、可観測性 (および応答) の中心が運用上の使用、つまり監視ソリューションの "*ユーティリティ*" ということです。 Azure Monitor は Microsoft のサービス リソースの観点を提供し、オンプレミスの監視システムと同様の機能を提供します。 監視ソリューションには、ロールベースのアクセスを備えた、ダッシュボード、ブックなどの視覚化が必要です。

**責任**: サービス コンシューマーとサービス プロバイダーの両方が、ハード データに基づいて学習し、改善していく必要性を共有しています。 そのため、クラウド プロバイダーの責任と顧客/コンシューマーの責任を理解する必要があります。 Azure リソースごとに、ログまたはメトリックに基づいて観点を取得します。このデータは、リソース固有のダッシュボードまたはカスタム視覚化内に、ご自身の要件に基づいて表すことができ、組織内の必要なペルソナと共有できます。

> [!NOTE]
> [Wikipedia の可観測性の概要](https://en.wikipedia.org/wiki/Observability)では、可観測性はシステムの特性であり、システム制御理論に由来すると説明されています。 これは、システムのもう 1 つの特性である "[制御性](https://en.wikipedia.org/wiki/Controllability)" と共に使用して、システムの内部状態を、システムの "*外部*" 出力からどの程度適切に推測できるかを示す指標であると言えます。 また、"*動的システム*"、つまり状態オブザーバーも、システムの正常性状態を経時的に測定または推定します。 ただし、最新のサービス管理の観点については、[Microsoft Operations Framework (MOF)](https://docs.microsoft.com/previous-versions/tn-archive/cc506049(v=technet.10)?redirectedfrom=MSDN) に関するページの **サービスの監視と制御**、および Information Technology Infrastructure Library (ITIL) v3 の **監視と制御のループ** に関するトピックをご覧ください。

可観測性に関する詳しい説明に入る前に、使用する監視関連の用語をいくつか取り上げて説明する必要があります。

- **資産:** ファイル共有、ハードウェア、およびソフトウェア資産のコンテンツなどのデジタル資産。"*ターゲット*" とも呼ばれます。

- **フォーカス:** 目標を達成するためのスコープ: 狭い、広い、単一のコンポーネント、コンポーネント クラス、コンポーネントのグループ化、サービス。

- **アスペクト:** ユーザーの観点、ビジネス、サービス所有者などの観点。

- **カバレッジ:** 可視性と目標を示すインジケーター。関連するすべての資産が監視スコープに含まれていることを確認します。

- **管理の容易性:** デジタル資産の制御、自己修復が可能な範囲。変更リスクと、診断または自動修復を行うアクション グループに関連します。

- **データ ソース:** 監視データの主な場所。Azure ストレージ アカウント、Azure Active Directory、カスタム ソースなど

- **頻度:** 継続的な監視と不定期の監視。

## <a name="the-art-of-being-observant"></a>観察技術

可観測性は、監視対象と監視方法に依存します。 Azure 内には複数のソースがあり、物事の動作について、それぞれが異なる観点を持っています。 言うまでもなく、このデータのさまざまな側面を分析するのに役立つツールが Azure には複数用意されています。 Azure Monitor とその機能は、主に、Azure サービスと Azure 以外のリソースの正常性とパフォーマンスを観察する際に使用します。 Azure 内で Microsoft が提供するサービス カタログは広範で、その主な焦点は仮想マシンではありません。 Azure における "*サービス プロバイダー*" の観点は、さまざまなプラットフォームのログを使用して提供されます。

* Azure によって報告されたサービス インシデントと計画メンテナンスに関するサービスの正常性。
* Azure アクティビティ ログは、サブスクリプションにデプロイされているリソースすべてのサブスクリプション レベルのイベントを報告。
* Azure Resource Health は、リソースの現在と過去の正常性を報告。
* ご自身の Azure のデプロイを最適化するためのベスト プラクティスに基づいて推奨ソリューションを受け取る Azure Advisor。

メトリックとログに基づくその他の観点はすべて、Azure Monitor のさまざまな機能を通じて提供されます。 また、Azure リソースによっては、ポータルでそのリソースのプラットフォーム メトリックを直接表示することもできます。

可観測性という用語が意図しているのは、Azure Monitor によって、Azureサービスの正常性、パフォーマンス、およびその他の側面の全体的な可視性を詳細かつ広範に確認できる、という期待感を抱かせることです。 ただし、サービスとコンポーネントの監視以外の、他のアクティビティやフォーカスもあります。 観察は、特定のペルソナだけが、自身の役割や職務で求められたとき、またはプロセスをサポートするために、必要に応じて実行するものと考えるべきではありません。 これは天文学と似ています。 観測は一度行えば終わりというものではありません。宇宙空間の物体を、たまに、設定された期間にわたって、または継続的に監視したいこともあります。 中には、ブラック ホールのように、観測しにくい物体もあります。 必要な監視の種類はコンシューマーによって異なります。 たとえば、アマチュア天文学者はたまにしか観測を行いませんが、天体物理学者は電波放射の長期測定と継続的な監視を重視しています。

適切な計画を立てれば、オンプレミスで作成すると数週間以上かかる中規模から複雑なサービスが、Azure 内で数時間で作成できます。 そうした場合、リージョンの枠を越えて 10 あまりの Azure リソース (サービス コンポーネント) の組み合わせを準備することになります。 これらのリソースは、Azure Resource Manager を使用して管理します。また、Azure 内でリソース グループの監視機能を使えば、サービスの状況を把握できます。 運用環境に移行したサービスは動的かつ柔軟です。 消費の拡大とスケールアウトが始まり、容量は需要に応じて変化します。 イベントが発生すると、サービス コンシューマーが監視データを観察し、傾向を確認して、その重要性を確立することができます。

サービスは、時間の経過と共に、予測できないさまざまな方法で変化することがあります。 そのため、この動作を動的と呼びます。 クラウド サービスのマネージャーは、時間の経過と共にサービスを観察する際、次の点を考慮する必要があります。

* リソースは複数の場所 (地理的な場所を含む) を越えて移動する
* リソースは追加、削除、または変更される
* 使用量が異なる

監視サービス プロバイダーとしてのジョブは、1) サービス、2)利害関係者、3) 主要コンシューマー、という優先順位で価値を提供する監視ソリューションを支援することです。

ご自身の価値ストリームは、さらに早い段階で、次のようにサービスの可観測性を考慮する必要があります。

* 十分に詳細かつ広範に監視します。
* 正常性モデル (可用性、容量/パフォーマンス、セキュリティ、適合性) 全体を対象とします。
* エンドユーザー エクスペリエンスを含め、ユーティリティ (サービスの動作) についても監視します。

監視戦略の一環として監視計画を立てるときは、監視および観察の対象として最小限実行できるもの、または最も重要なものから始めることをお勧めします。 これは、リソースまたはシステムに応じて、Microsoft、その他の信頼できる情報源、社内開発者やシステム エンジニアからの推奨事項に基づいて行うことができます。 ご自身の Azure リソース グループとお使いの Azure Active Directory テナント内のリソースについて、"*消費量*"、"*パフォーマンス*"、"*セキュリティ*"、および "*可用性*" の初期の可視性を確立してください。 観察を通して、データを解釈する方法、およびサービスの監視方法を微調整し最適化するうえで重要なことを学習します。 価値は漸進的に達成されます。コンシューマーが監視チーム (場合によってはサービス プロバイダー) と連携して価値創造に取り組んでいるという点で、ここでは価値の共創が行われています。

可観測性は、最小限の実行可能な監視プランから徐々に進化し、現在、ツールとプロセスを統合するための作業が行われています。 データ (つまり、メトリック、ログ、およびトランザクション) に慣れてくると、そのリソースまたはアプリケーションからの症状や問題の動作および兆候を理解できます。 データに慣れることによって、Azure Monitor およびデータ操作に対する信頼を構築します。 可観測性を通して自信がつき、原因がわかり、以下に役立つ回答を見つけることができます。

* 必要な監視コンポーネントすべてを確実に対象とすることで、監視の盲点を減らします。
* 今後の問題特定に役立つリソースとサービスの監視を強化します。 予測可能で信頼性の高い問題または症状に焦点を当てます。
* これが予想されていなかったかどうかと、インフラストラクチャやアプリケーションなどを改善するために何ができるかを評価します。
* それがインフラストラクチャまたはアプリケーションであることを除外し、特定のブラウザー、ブラウザーのバージョン、またはクライアントのオペレーティング システムが潜在的な問題であることに絞り込みます。
* アプリケーションやインフラストラクチャなどのパフォーマンスまたは可用性の問題を最小限に抑えるために使用できる回避策を特定、文書化、または実装します。
* アプリケーションの動的かつ複雑な性質に適応します。 異常な動作パターンを絞り込むために通常の動作パターンを継続的に学習します。
* 収集、集計、および警告されるデータの種類を調整して、分析、警告、または視覚化しないデータのストレージ コストを最小限に抑えます。

まず、オンプレミスの世界のように、インフラストラクチャやアプリケーションについて心配する必要がなくなり、ワークロードの管理/運用担当者のニーズに対応するために、データを監視する、監視データを入手する、といった操作を自由に行えるようになりました。

アプリケーションやインフラストラクチャ サービスと同様、さまざまな関係者や役割が、監視機能、データ、およびレポートにアクセスする必要があります。 まずは、ワークロードのサポートを担当する主要なシステム エンジニア、運用またはサービス プロバイダーから始め、他の関係者へとアクセスを拡張してください。  

## <a name="fixed-versus-dynamical-approach"></a>固定アプローチと動的アプローチ

データセンター内でのオンプレミスのサービス監視には、従来、System Center Operations Manager などの製品が使用されます。 Operations Manager のアプローチは、エージェントとオペレーティング システムによって、インフラストラクチャとサーバーにしっかりと根ざしています。 アプリケーションの監視は、コンテナ化されているかどうかに関係なく、スタックの上部にあり、スタック下部のサーバーのインフラストラクチャ監視に依存します。 垂直方向に、ソリューションの対象が下から上へと示され、ネットワークから始まり、一番上がユーザー エクスペリエンスの監視になります。

IaaS 監視の長い歴史は、"*可観測性を事前に定義できる*"こと、また、必要に応じて "*事前に設計できる*" ことを示しています。 IaaS ベースのサービスにより、製品エンジニアは Operations Manager 管理パックを作成でき、基本的に、サポートされているほとんどのユース ケースに合わせて監視ソリューションをカスタマイズすることができます。

そのため、監視ソリューションによってインフラストラクチャ サービスを制御する必要があり、多くの場合、顧客は固定アプローチの方を求めています。

クラウド内での固定アプローチは、リソースの配置と組み合わせが空間的にも時間的にもほぼ無限であることを考えると不可能です。 各サービスを一意にすることができます。 Azure 内のサービスの可観測性は、サービスの柔軟な性質 (つまり、動的、グローバル、回復性、ユーザー中心など) に応じて、または、アーキテクチャ テンプレート セットに従って構築する必要がありますが、これはまだ存在しない可能性があります。

このため、可観測性は、イベントの重要性 (アラート、ダイジェスト、ブックなど) を確立する "*前に*"、すべてのコンポーネントがどのように連携しているかを総合的に把握するうえで最も重要なものになります。

繰り返しになりますが、クラウド サービス監視は、はるかに柔軟で、変化のスピードが速く、より動的です。

## <a name="monitoring-plan"></a>監視計画

監視計画を作成して、目標、目的、要件、およびその他の重要な詳細事項について説明します。 次に、組織内の直接の利害関係者全員の合意を得るように働きかけます。 監視計画によって、1 つ以上の監視ソリューションを開発および運用する方法を説明する必要があります。 プロジェクトの戦略および計画フェーズ中、早い段階で監視計画の開発を開始してください。

計画の作成中、最新の監視の 5 つの規範 (観察、測定、応答、学習、改善) に留意することが重要です。

以下は推奨されるアウトラインであり、個別のサービス計画に対する、あるいは Azure のリソースの種類や Microsoft 365 サービスなどのクラウド サービス機能を標準化する際の主な検討事項と見なされています。 この計画の本質は、ソリューションを提供するサービス プロバイダーと、価値を運用する、または価値を引き出すコンシューマーの間の可視性の境界線を定義にすることです。  

時期 (および方法) のスケジュールを含めることも、作業管理アプローチ (DevOps とウォーターフォールなど) には重要です。

### <a name="business-perspective"></a>ビジネスの観点

包括的な監視計画で考慮する必要があるのは、ビジネスが監視によって、および監視から何を求めているかであり、これにはユーザーを中心とした考え方が必要です。 計画を定義するときは、ビジネス要件を文書化して共有することが重要です。次に提案するのは、計画のこの部分のスコープです。

* 利害関係者とコンシューマー
* ビジネス価値ストリームとプロセス
* エンド ユーザーの観点とユーティリティ
* 測定とレポートの要件
* 特定されたリスクとコンプライアンス制御フレームワーク
* アクセスと制御の要件
* ビジネスに対するリスク

### <a name="service-perspective"></a>サービスの観点

包括的な監視計画で考慮する必要があるのは、サービス所有者が監視によって、および監視から求めているものです。 計画を定義するときは、その要件を文書化して共有することが重要です。次に提案するのは、計画のこの部分のスコープです。

* 利害関係者とコンシューマー
* ロールとアカウンタビリティ
* サービスの定義
* アクセスと制御の要件
* アーキテクチャに関する考慮事項?
* コントラクトを支えるサプライヤーとパートナー
* サービス契約 (SLA、OLA)
* サービス保証の対象範囲
* 測定とレポートの要件
* リスク

### <a name="technology-perspective"></a>テクノロジの観点

計画のこのセクションは、ビジネスおよびサービスの観点からの情報を使用して、監視ソリューションを表します。

* ユーザー ストーリーとシナリオ
* 技術ターゲット (ネットワークなど)
* コンポーネントの依存関係マッピング
* タイプ (クラウド ネイティブ、ハイブリッド、オンプレミスなど)
* 観察
* レスポンシブ
* Measurement
* チューニングと最適化

### <a name="key-considerations"></a>重要な考慮事項

関連するすべてのコンシューマー、利害関係者、および経営レベルに確実に伝達および通知するための計画をまとめます。

- **運用ステージ:** 監視ソリューションは、サービスが開始されたときに価値に対する準備ができている必要があります。したがって、計画にラボまたは実稼働前構成を含めて (つまり、このサポート専用の別のサブスクリプションで)、ご自身の前提条件を使って実験したり、その前提をテストしたりできます。 クラウド内の運用テナントは、通常、ガバナンス全体に基づいて安全です。

- **戦略:** 計画を監視および IT 戦略にマップし直して、監視の目標をミッションまたはビジネスに戻すこともできます。 これは、ワークロードがネイティブ クラウドかどうかに依存します

- **ターゲット:** 計画内で、検討中の対象資産またはサービスの説明と分析を行い、必要に応じて、サービスの依存関係を含めるように、監視対象のすべてのコンポーネントをマップします。 対象範囲のギャップを特定し、サービス部分を所有しているユーザーを判断します。

- **ソリューション:** 監視ソリューションについては、コンシューマー、利害関係者、サプライヤー、パートナー、アクセス、およびインストルメンテーションを特定します。 また、アスペクト、フォーカス、応答、およびレポートとダッシュボード (可用性、セキュリティ、ユーザー エクスペリエンスなど) を監視します

### <a name="lean-and-agile-considerations"></a>効率性および俊敏性に関する考慮事項

- **実用最小限の製品:** 最初に開始する必要がある実用最小限の製品を計画によって定義し、その後、監視ソリューションを継続的に進化させて、価値を最大化することができます。 たとえば、他のログベースのブックを構築するための今後のタスクを後で定義して、Azure ダッシュボードにピン留めしたり、Azure portal への利害関係者アクセスを拡張したりできます。

- **今の場所から開始して迅速に価値を得る:** 簡単で価値のある、かつすぐに利用可能な SaaS を使って、迅速かつ徹底的に実験を行い、活用します。

- **ガードレールを知る:** クラウドは新しく不確実なものなので、リスク (機密監視データの漏洩など) を増やさないようにエキスパートの専門家の指導を受けてください。

- **Azure 上の Microsoft 365:** 優れたプランはすべて、Microsoft 365 を備えた Azure テナントを主要プレーヤーと見なします。 Microsoft 365 は Azure AD に依存し、Azure Monitor は、Microsoft 365 とエンドポイント管理の統合を提供します。

- **可観測性を優先:** アラートがコストになっているため、アラートの前に全体的な可視性に焦点を当てます。

- **ログのアーキテクチャ:** エミッタ、テレメトリ、シグナル、AI、Microsoft インテリジェント セキュリティ グラフなどの事前パッケージ化ソリューション - より SIEM に近いアプローチ、Azure Monitor ログなどのログ ソリューションにさまざまなログを集める方法に焦点を当てます。

- **データの移動:** より多くの監視が、エミッタからダッシュボードまでと、インターネット上、およびクラウドのリージョン、簡易ルート、VPN 経由でデータがどこに流れているかに焦点を当てています。

- **アクティビティの監視:** サービス所有者やセキュリティのために、監査ログ、サインイン ログ、およびアクティビティ ログの詳細な分析を容易に行えるようになりました。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [適切なデータの収集](./data-collection.md)