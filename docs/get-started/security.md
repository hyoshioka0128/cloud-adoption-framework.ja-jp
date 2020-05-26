---
title: 作業を開始しましょう。企業環境をセキュリティで保護する
description: クラウド導入の取り組みおよび運用時の重要なポイントでのセキュリティ統合の概要。
author: JanetCThomas
ms.author: janet
ms.date: 04/04/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: overview
ms.openlocfilehash: 100d5c327fb6e87f0a355f5305ff83ebb972b020
ms.sourcegitcommit: 5d6a7610e556f7b8ca69960ba76a3adfa9203ded
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/14/2020
ms.locfileid: "83400757"
---
<!-- cSpell:ignore CISO passwordless -->

# <a name="get-started-implement-security-across-the-enterprise-environment"></a>作業を開始しましょう。企業環境全体へのセキュリティの実装

セキュリティを実装することで、企業では、内部と外部の両方の悪意のある意図しない行為による運用への潜在的な影響に対する保護に特に重点を置いて、機密性、整合性、可用性を保証することができます。 このロードマップでは、クラウドでの重要なセキュリティ プラクティスを迅速に確立し、セキュリティをクラウド導入プロセスに統合することによって、サイバーセキュリティ攻撃によるビジネス リスクを軽減または回避する主な手順の概要を示します。

このガイドの手順は、クラウド環境とランディング ゾーンのセキュリティ保証をサポートするすべてのロールを対象としています。 これには、直近のリスク軽減の優先順位、最新のセキュリティ戦略の構築、アプローチの運用化、その戦略の実行に関するガイダンスの両方が含まれます。

この入門ガイドには、クラウド導入フレームワーク全体のさまざまな要素が含まれています。

![企業セキュリティの概要](../_images/get-started/security-map.png)

このガイドの手順に従うと、クラウドの導入における障害を回避し、不要な業務または運用の中断を減らすためのプロセスの重要なポイントでのセキュリティの統合に役立ちます。

Microsoft では、(このドキュメント全体で参照されている) Microsoft Azure に関するこのセキュリティ ガイダンスの実装を促進するのに役立つ機能とリソースを構築しました。 これらのリソースは、セキュリティの確立、監視、適用に役立つように設計されており、頻繁に更新および確認されます。

この図は、セキュリティ ガイダンスとプラットフォーム ツールを使用して、Azure でのクラウド資産のセキュリティの可視性と制御を確立するための Microsoft が推奨する総合的なアプローチを示しています。

![セキュリティの図](../_images/security/security-diagram.png)

これらの手順を使用して、クラウド資産をセキュリティで保護し、クラウドを使ってセキュリティ運用を最新化するための戦略を計画および実行します。

## <a name="step-1-establish-essential-security-practices"></a>手順 1:重要なセキュリティ プラクティスを確立する

クラウドにおけるセキュリティは、確実なプラクティスから始まります。 既にクラウドで運用している場合でも、将来の導入を計画している場合でも、重要なセキュリティ プラクティスを迅速に確立することが重要です。

明示的な規制に対するコンプライアンスの要件に加え、次の手順をお勧めします。これらは、ほとんどの組織がクラウドへの移行の際に直面するセキュリティ上の最大の課題に対処するためのものです。

**成果物とサポートに関するガイダンス:**

- **技術:** 最上位のリスクを軽減し、資産の可視性と制御を向上させるには、管理者向けのパスワードレス認証または多要素認証を有効にするか、クラウド リソースの脅威の防止を有効にします。
  - [管理者向けのパスワードレス認証または多要素認証](https://docs.microsoft.com/azure/architecture/framework/security/critical-impact-accounts#passwordless-or-multi-factor-authentication-for-admins)
  - [セキュリティ運用](https://docs.microsoft.com/azure/architecture/framework/security/security-operations)と [Azure Security Center での脅威の防止](https://docs.microsoft.com/azure/security-center/threat-protection)
- **プロセス:** セキュリティのロールと責任を割り当て、インシデント対応プロセスを確立することで、セキュリティに関する迅速な決定と継続的な改善を可能にします。
  - [責任の明確な線引き](https://docs.microsoft.com/azure/architecture/framework/security/governance#clear-lines-of-responsibility)、[環境を管理するための特権の割り当て](https://docs.microsoft.com/azure/architecture/framework/security/governance#assign-privileges-for-managing-the-environment)、セキュア スコアの運用化 <!-- TODO: Improve this and add link to AAF article -->
  - セキュリティ ロールと責任 <!-- TODO: add link to bookmark -->
  - [インシデント対応のリファレンス ガイド](https://aka.ms/irrg)
- **ユーザー:** クラウド環境への移行時にデプロイおよび運用を適切に行うために必要な教育、ツール、アクセス権をセキュリティ チームに提供します。
  - クラウドとクラウド セキュリティの進化の**概念について、すべてのユーザーを対象に教育する**:
    - [脅威の環境、ロール、デジタル戦略の進化](https://docs.microsoft.com/security/compass/microsoft-security-compass-introduction#evolution-of-threat-environment-roles--digital-strategies-2004)
    - [セキュリティ、戦略、ツール、脅威のトランスフォーメーション](https://docs.microsoft.com/security/compass/microsoft-security-compass-introduction#transformation-of-security-strategies-tools--threats-1513)
  - 使用されるプラットフォームのクラウド セキュリティ機能に関する技術的な詳細について、**技術スタッフをトレーニング**します。 Microsoft では、広範な [Azure のセキュリティに関するドキュメント](https://docs.microsoft.com/azure/security)を提供しています。
- **長期的なアーキテクチャに関する決定:** 適切な意思決定を行って、長期的な基盤を確立します。 これらを後で変更するのは非常に難しく、コストがかかります。
  - [企業のセグメント化戦略を構築し、技術アーキテクチャをそれに合わせる (ネットワークのセグメント化や ID のセグメント化など)](https://docs.microsoft.com/azure/architecture/framework/security/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)
  - [単一のエンタープライズ ディレクトリ](https://docs.microsoft.com/azure/architecture/framework/security/identity#single-enterprise-directory)
  - [サービスの認証戦略](https://docs.microsoft.com/azure/architecture/framework/security/applications-services#prefer-identity-authentication-over-keys)
  - [アクセス許可の割り当て戦略](https://docs.microsoft.com/azure/architecture/framework/security/critical-impact-accounts#avoid-granular-and-custom-permissions)

<!-- markdownlint-disable MD033 -->
<br>

| 説明責任チーム | 実行責任チームとサポート チーム |
| --- | --- |
| <li> クラウド セキュリティ チーム <br><br><br> | <li> クラウド戦略チーム <li> クラウド導入チーム <li> クラウドのセンター オブ エクセレンスまたは中央 IT |

この最初の手順では、ガバナンス チームは、環境間で監視、管理、適用できるセキュリティ ベースラインの作成の調整を開始する必要もあります。 これを構築するための追加のガイダンスについては、以下の手順 4 で説明します。

> [!NOTE]
> 各組織では独自の最低限の基準を定義する必要があります。これは、リスク体制とその後のリスクに対する許容範囲は、業界、文化、その他の要因によって大きく異なる可能性があるためです。 たとえば、ある銀行では、テスト システムに対する軽微な攻撃であっても、評判を損なう可能性のあるものは許容されない場合があります。3 か月から 6 か月でデジタル トランスフォーメーションを加速できる場合、同じリスクを喜んで受け入れる企業もあります。

## <a name="step-2-modernize-security-strategy"></a>手順 2:セキュリティ戦略を最新化する

クラウドでの効果的なセキュリティには、現在の脅威環境と、企業資産をホストしているクラウド プラットフォームの性質を反映する、確実な戦略が必要です。 明確な戦略により、すべてのチームの取り組みが改善され、ビジネス上のリスクを軽減する安全で持続可能なエンタープライズ クラウド環境が提供されます。 セキュリティ戦略では、定義されたビジネス成果を実現し、許容できるレベルまでリスクを軽減し、従業員の生産性を高める必要があります。

クラウド セキュリティ戦略では、この導入に向けて、テクノロジ、プロセス、およびメンバーの準備に従事するすべてのチームに明確なガイダンスが提供されます。 この戦略では、クラウドのアーキテクチャおよび技術的機能を伝え、セキュリティ アーキテクチャおよび機能について説明し、チームのトレーニングと教育に影響を与える必要があります。

**成果物:**

この戦略手順により、組織内の多くの利害関係者 (組織のリーダーシップ チームの役員を含む可能性がある) にドキュメントを簡単に伝達できるようになるはずです。

このため、説明と更新を容易にするためにプレゼンテーションに戦略を取り込むことをお勧めします。 これは、文化と優先順位に応じて、同様にドキュメントでサポートできます。

- **戦略のプレゼンテーション:** 単一の戦略プレゼンテーションを行うことも、リーダーシップ対象ユーザーのための概要バージョンを作成することもできます。
  - **完全なプレゼンテーション:** メイン プレゼンテーションまたはオプションの参照スライドに、セキュリティ戦略の要素の完全なセットが含まれている必要があります。
  - **概要:** リスク選好度、最優先事項、または受け入れられるリスクなど、ロールに関連する重要な要素のみを含む、上級管理者および取締役会役員と共に使用するバージョン。
- [戦略と計画のテンプレート](https://archcenter.blob.core.windows.net/cdn/fusion/readiness/Microsoft-Cloud-Adoption-Framework-Strategy-and-Plan-Template.docx)で、動機、成果、業務上の正当な理由を記録することもできます。

**セキュリティ戦略を構築するためのベスト プラクティス:**

成功したプログラムでは、これらの要素がセキュリティ戦略プロセスに組み込まれています。

- **ビジネス戦略に厳密に合わせる:** セキュリティの憲章はビジネス価値を保護するものであるため、すべてのセキュリティの取り組みをその目的に合わせ、内部抗争を最小限に抑えることが重要です。
  - **共通の理解を形成する:** ビジネス、IT、セキュリティの要件が対象となります。
  - **早い段階でセキュリティをクラウド導入に組み込む:** これは、回避可能なリスクから土壇場の危機を回避するためのものです。
  - **アジャイル アプローチ:** これは、最小限のセキュリティ要件を直ちに確立し、経時的にセキュリティ保証を継続的に向上させるためものです。
  - **セキュリティ文化の変更:** 意図的なプロアクティブ リーダーシップ アクションによるものです。

詳細については、「[トランスフォーメーション、マインドセット、期待](../strategy/define-security-strategy.md#transformations-mindsets-and-expectations)」を参照してください。

- **セキュリティ戦略を最新化する:** セキュリティ戦略には、最新のテクノロジ環境、現在の脅威の状況、セキュリティ コミュニティ リソースのあらゆる側面に関する考慮事項が含まれている必要があります。
  - クラウドの**共有責任モデルに適応する**。
  - **すべてのクラウドの種類とマルチクラウドを含める。**
  - 不要で有害な摩擦を避けるために、**ネイティブ クラウド コントロールを優先する**。
  - **セキュリティ コミュニティ統合:** これは、攻撃者の進化のペースに合わせるためのものです。

**追加コンテキストの関連リソース:**

- [脅威の環境、ロール、デジタル戦略の進化](https://docs.microsoft.com/security/compass/microsoft-security-compass-introduction#evolution-of-threat-environment-roles--digital-strategies-2004)
- [セキュリティ、戦略、ツール、脅威のトランスフォーメーション](https://docs.microsoft.com/security/compass/microsoft-security-compass-introduction#transformation-of-security-strategies-tools--threats-1513)
- クラウド導入フレームワーク戦略に関する考慮事項:
  - [セキュリティ戦略を最新化する](../strategy/define-security-strategy.md#modernize-your-security-strategy)
  - [サイバーセキュリティの回復力](../strategy/define-security-strategy.md#cybersecurity-resilience)
  - [クラウドでのセキュリティの関係と責任の変化](../strategy/define-security-strategy.md#how-the-cloud-is-changing-security)

<!-- markdownlint-disable MD033 -->
<br>

| 説明責任チーム | 実行責任チームとサポート チーム |
| --- | --- |
| <li> セキュリティ リーダーシップ チーム (情報セキュリティ最高責任者 (CISO) または同等のもの) | <li> クラウド戦略チーム <li> クラウド セキュリティ チーム <li> クラウド導入チーム <li> クラウドのセンター オブ エクセレンスまたは中央 IT |

**戦略の承認:** 組織内のビジネス ラインのビジネス成果またはビジネス上のリスクについて、説明責任を持つ役員およびビジネス リーダー (これには、組織に応じて、取締役会が含まれる場合があります)。

## <a name="step-3-develop-a-security-plan"></a>手順 3:セキュリティ プランを作成する

プランの策定では、成果、マイルストーン、タイムライン、タスク所有者を定義することで、セキュリティ戦略を実行に移します。 また、このプランでは、チームのロールと責任について概説します。

セキュリティ プランとクラウド導入プランを、単独で策定することはできません。 早い段階でクラウド セキュリティ チームをプランの策定サイクルに招き、セキュリティ問題の検出が遅すぎることによって作業が停止したり、リスクが増大したりするのを防ぐことが重要です。 クラウド プランの策定プロセスに完全に統合することによって得られる、デジタル資産と既存の IT ポートフォリオについて深く理解し、認識したうえでセキュリティ プランを策定することをお勧めします。

**成果物:**

- **セキュリティ プラン:** クラウドの主なプランの策定ドキュメントに含まれている必要があります。また、[戦略と計画テンプレート](https://archcenter.blob.core.windows.net/cdn/fusion/readiness/Microsoft-Cloud-Adoption-Framework-Strategy-and-Plan-Template.docx)、詳細なスライド デッキ、プロジェクト ファイル、あるいは組織の規模、文化、標準的なプラクティスに応じて、これらの形式の組み合わせを使用するドキュメントである場合があります。

- セキュリティ プランには、これらの要素がすべて含まれている必要があります。

  - **組織機能のプラン:** チームは、クラウドへの移行によって現在のセキュリティ ロールと責任がどのように変化するかを認識しています。
    - **セキュリティ スキルのプラン**。これは、テクノロジ、ロール、責任の重要な変更点を参照する際にチーム メンバーをサポートするためのものです。
  - **技術的なセキュリティ アーキテクチャと機能のロードマップ:** これは、技術チームをガイドするためのものです。
  Microsoft では、次のようなアーキテクチャとロードマップを構築する際に役立つ、リファレンス アーキテクチャとテクノロジ機能を提供しています。
    - [Azure コンポーネントとリファレンス モデル](https://docs.microsoft.com/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)。これは、Azure セキュリティ ロールのプランの策定と設計を促進するためのものです。
      ![Azure 管理モデル](../_images/security/azure-administration-model.png)
      ![Azure RBAC モデル](../_images/security/azure-rbac-model.png)
    - [Microsoft サイバーセキュリティ リファレンス アーキテクチャ](https://aka.ms/mcra)。これは、オンプレミスおよびクラウド リソースにまたがるハイブリッド エンタープライズ用の全体的なサイバーセキュリティ アーキテクチャを構築するためのものです。
    - [セキュリティ オペレーション センター (SOC) リファレンス アーキテクチャ](https://docs.microsoft.com/security/compass/security-operations-videos-and-decks#part-1-introduction---soc-learnings-strategies-and-technical-integration-2430)。これは、セキュリティの検出、対応、回復を最新化するためのものです。
    - [ゼロ トラスト ユーザー アクセス リファレンス アーキテクチャ](https://docs.microsoft.com/security/ciso-workshop/ciso-workshop-module-3#part-5-zero-trust-user-access-reference-architecture-842)。これは、クラウドの生成用のアクセス制御アーキテクチャを最新化するためのものです。
    - [Azure Security Center](https://docs.microsoft.com/azure/security-center/) と [Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/)。これは、クラウド資産をセキュリティで保護するためものです。
  - **セキュリティの意識と教育のプラン:** すべてのチームには重要なセキュリティに関する基礎知識もあります。
  - **資産感度のマーキング:** これは、(ビジネス利害関係者、セキュリティ チーム、その他の関係者が共同で構築した) ビジネスへの影響に合わせた分類を使用して、機密性の高い資産を指定するためのものです。

- **ハイブリッド環境を想定する:** これには、サービスとしてのソフトウェア (SaaS) アプリケーション、オンプレミスの環境、サービスとしての複数のクラウド インフラストラクチャおよびサービス プロバイダーとしてのプラットフォーム (該当する場合) が含まれます。
- **セキュリティの変更でクラウド プランを更新する:** セキュリティ プランによってトリガーされた変更を反映するために、メイン クラウド導入プランの他のセクションを更新します。

**セキュリティ プランの策定に関するベスト プラクティス:** プランを策定する際に次のようなアプローチが採用されている場合、セキュリティ プランが成功する可能性が高くなります。

- **アジャイル セキュリティの導入:** 最初に最小限のセキュリティ要件を確立し、重要ではないすべての項目を、次の手順の優先順位付けされた一覧に移動します。
クラウドと脅威環境の変化が速すぎて、この種のプランを有効に活用できないため、これを従来の 3 年から 5 年の詳細なプランにすべきではありません。 実際のプランでは、最初の手順と終了状態を策定することに重点を置く必要があります。
  - **即効性のある成果:** すぐに成果を得て、大きな影響を与える長期的なイニシアチブを開始するための近い将来の詳細なプラン。 これは、組織の文化、標準的なプラクティス、その他の要因によっては、3 か月から 12 か月になることがあります。
  - 目的の最終状態の**明確なビジョン**。これは、各チームの (達成するまでに数年かかる場合がある) プランの策定プロセスをガイドするためのものです。
- **広範なプランの共有:** これは、利害関係者の意識を高め、フィードバックや同意を増やすためのものです。
- **戦略成果に合わせる:** 確実に、プランがセキュリティ戦略に記載されている戦略成果に合っており、その成果が得られるようにします
- **所有権、説明責任、期限:** 確実に、各タスクの所有者が識別され、特定の期間内にそのタスクを完了することに取り組むようにします。
- **セキュリティの人間的側面とのつながりを持つ:** これは、この期間にユーザーをトランスフォーメーションに関与させ、新たな期待を持たせるためのものです。
  - **チーム メンバーのための積極的なトランスフォーメーションに関するサポート:** 以下について、明確に伝達して指導します。
    - 習得する必要があるスキル
    - それらを習得する必要がある理由 (およびその利点)
    - この知識を得る (および習得に役立つリソースを提供する) 方法。
  [戦略と計画テンプレート](https://archcenter.blob.core.windows.net/cdn/fusion/readiness/Microsoft-Cloud-Adoption-Framework-Strategy-and-Plan-Template.docx)を使用して、これを文書化することができます。また、チーム メンバーの教育に役立つ、[オンラインの Microsoft セキュリティ トレーニング](https://docs.microsoft.com/security/compass/microsoft-security-compass-introduction)を利用することができます。
  - **セキュリティ意識を促す:** これは、純粋にユーザーを組織の安全の維持に関与させるのに役立ちます。
- **Microsoft ラーニングとガイダンスを確認する:** Microsoft では、組織がクラウドへの転換を計画し、記録されたトレーニング、ドキュメント、セキュリティのベスト プラクティスと推奨される標準を含む最新のセキュリティ戦略を策定するのに役立つ、分析情報とパースペクティブを公開しています。
  プランとアーキテクチャの策定に役立つ技術的なガイダンスについては、[Microsoft のセキュリティ ドキュメント](https://docs.microsoft.com/security)を参照してください。

<!-- markdownlint-disable MD033 -->
<br>

| 説明責任チーム | 実行責任チームとサポート チーム |
| --- | --- |
| <li> クラウド セキュリティ チーム | <li> クラウド戦略チーム <li> クラウド ガバナンス チーム <li> 組織内のすべてのリスク チーム <li> クラウドのセンター オブ エクセレンスまたは中央 IT |

**セキュリティ プランの承認:** セキュリティ リーダーシップ チーム (CISO または同等のもの)。

## <a name="step-4-secure-new-workloads"></a>手順 4:新しいワークロードをセキュリティで保護する

後で環境にセキュリティを組み込むよりも、セキュリティで保護された状態で始める方がはるかに簡単です。 確実にセキュリティで保護された環境にワークロードが移行され、開発とテストが行われるように、セキュリティで保護された構成で始めることを強くお勧めします。

[ランディング ゾーン](https://docs.microsoft.com/azure/cloud-adoption-framework/ready/landing-zone)の実装中は、多くの決定がセキュリティおよびリスク プロファイルに影響する可能性があります。 クラウド セキュリティ チームでは、ランディング ゾーンの構成を確認し、確実に組織のセキュリティ ベースラインのセキュリティ標準と要件が満たされるようにする必要があります。

**成果物:**

- 新しいランディング ゾーンが組織のコンプライアンスとセキュリティの要件を確実に満たすようにします。

**成果物の完成をサポートするためのガイダンス:**

- **既存の要件とクラウドに関する推奨事項を調和させる:** 推奨されるガイダンスから始め、これを独自のセキュリティ要件に適合させます。 既存のオンプレミス ポリシーと標準を適用しようとしたときに問題が発生しました。多くの場合、これらは古いテクノロジやセキュリティ手法を示しているためです。 Microsoft では、セキュリティ ベースラインを構築するのに役立つガイダンスを公開しています。
  - [戦略とアーキテクチャに関する Azure のセキュリティ標準](https://docs.microsoft.com/security/compass/compass): 環境のセキュリティ体制を形成するための戦略とアーキテクチャに関する推奨事項。
  - [Azure セキュリティ ベンチマーク](https://docs.microsoft.com/azure/security/benchmarks/introduction): Azure 環境をセキュリティで保護するための特定の構成に関する推奨事項。
  - [Azure セキュリティ ベースラインのトレーニング](https://docs.microsoft.com/learn/modules/create-security-baselines)。
- **ガードレールを提供する:** これらには、自動化されたポリシーの監査と適用が必要です。 これらの新しい環境では、チームは、ワークロードの継続的インテグレーションと継続的配置 (CI/CD) だけでなく、開発時のセキュリティの問題を最小限に抑えるために、組織のセキュリティ ベースラインの監査と適用の両方に努める必要があります。
Microsoft では、これを可能にするために Azure でいくつかのネイティブ機能を提供しています。
  - [セキュリティ スコア](https://docs.microsoft.com/azure/security-center/secure-score-security-controls): 組織内のセキュリティの取り組みとプロジェクトを追跡できるようにする、Azure セキュリティ体制のスコア付けされた評価。
  - [Azure Blueprints](https://docs.microsoft.com/azure/governance/blueprints/overview):クラウド アーキテクトと中央 IT グループでは、組織の標準、パターン、および要件を実装し、遵守する一連の反復可能な Azure リソースを定義できます。
  - [Azure Policy](https://docs.microsoft.com/azure/governance/policy/):これらの他のサービスで使用される可視性と制御機能の基盤。 Azure Policy は [Azure Resource Manager (ARM)](https://docs.microsoft.com/azure/azure-resource-manager) に統合されています。これにより、変更を監査し、作成前、作成中、または作成後に Azure 内の任意のリソースに対してポリシーを適用することができます。
- [ランディング ゾーンの運用を改善する](../ready/considerations/landing-zone-security.md): 特定のランディング ゾーン内のセキュリティを強化するためのベスト プラクティスを使用します。

<!-- markdownlint-disable MD033 -->
<br>

| 説明責任チーム | 実行責任チームとサポート チーム |
| --- | --- |
| <li> クラウド セキュリティ チーム | <li> クラウド導入チーム <li> クラウド プラットフォーム チーム <li> クラウド戦略チーム <li> クラウド ガバナンス チーム <li> クラウドのセンター オブ エクセレンスまたは中央 IT |

## <a name="step-5-secure-existing-cloud-workloads"></a>手順 5:既存のクラウド ワークロードをセキュリティで保護する

多くの組織では、セキュリティのベスト プラクティスが適用されていないエンタープライズ クラウド環境に既に資産をデプロイしているため、ビジネス上のリスクが増大しています。

確実に新しいアプリケーションとランディング ゾーンがセキュリティのベスト プラクティスに従うようにした後に、既存の環境が同じ標準に達するようにすることに重点を置く必要があります。

**成果物:**

- 既存のすべてのクラウド環境とランディング ゾーンが、組織のコンプライアンスとセキュリティの要件を確実に満たすようにします。
- セキュリティ ベースライン ポリシーを使用して、運用環境のデプロイの運用準備をテストします。
- セキュリティ ベースラインの設計ガイダンスとセキュリティ要件への準拠を確認します。

**成果物の完成をサポートするためのガイダンス:**

- 最適な状態として、[手順 4](#step-4-secure-new-workloads) で構築したものと同じセキュリティ ベースラインを使用します。 場合によっては、一部のポリシー設定を適用するのではなく、監査のみを行うために調整する必要があります。
- 運用およびセキュリティ リスクのバランスを取ります。 これらの環境では、重要なビジネス プロセスを有効にする運用システムをホストする可能性があるため、運用ダウンタイムのリスクを回避するために、セキュリティの強化を段階的に行う必要がある場合があります。
- ビジネスの重要度で、セキュリティ リスクの検出と修復に優先順位を付けます。これは、侵害された場合にビジネスに大きな影響を与えるワークロードと、リスクにさらされる可能性が高いワークロードから始めます。

詳細については、[ビジネス クリティカルなアプリケーションの特定と分類](https://docs.microsoft.com/azure/architecture/framework/security/applications-services?toc=/security/compass/toc.json&bc=/security/compass/breadcrumb/toc.json#identify-and-classify-business-critical-applications)に関するページを参照してください。

<!-- markdownlint-disable MD033 -->
<br>

| 説明責任チーム | 実行責任チームとサポート チーム |
| --- | --- |
| <li> クラウド導入チーム | <li> クラウド導入チーム <li> クラウド戦略チーム <li> クラウド セキュリティ チーム <li> クラウド ガバナンス チーム <li> クラウドのセンター オブ エクセレンスまたは中央 IT |

## <a name="step-6-govern-to-manage-and-improve-security-posture"></a>手順 6:セキュリティ体制の管理と改善のために統制する

すべての最新の規範と同様に、セキュリティは継続的な改善に集中する必要がある反復的なプロセスです。 組織で経時的に集中を維持しないと、セキュリティ体制が崩壊する可能性があります。

セキュリティ要件の一貫した適用は、確実なガバナンス規範と自動化されたソリューションに基づきます。 クラウド セキュリティ チームによってセキュリティ ベースラインが定義されたら、これらの要件を監査し、確実にすべてのクラウド環境に一貫して適用される (および必要に応じて、強制される) ようにする必要があります。

**成果物:**

- 確実に組織のセキュリティ ベースラインが関連するすべてのシステムに適用され、[セキュリティ スコア](https://docs.microsoft.com/azure/security-center/secure-score-security-controls)または同様のメカニズムを使用して異常が監査されるようにします。
- [セキュリティ ベースライン規範テンプレート](../govern/security-baseline/template.md)のセキュリティ ベースライン ポリシー、プロセス、設計ガイダンスを文書化します。

**成果物の完成をサポートするためのガイダンス:**

- 一貫性を確保するためにユーザーおよびプロセス制御によって補完される、ベースラインを監視する技術的要素として、[手順 4](#step-4-secure-new-workloads) で構築したのと同じセキュリティ ベースラインと監査メカニズムを使用します。
- 確実に、すべてのワークロードとリソースが適切な[名前付けおよびタグ付け規則](../ready/azure-best-practices/naming-and-tagging.md)に従うようにし、"データの機密性" のタグに特に重点を置いて [Azure Policy を使用してタグ付け規則を適用する](https://docs.microsoft.com/azure/governance/policy/tutorials/govern-tags)ようにします。
- クラウド ガバナンスに慣れていない場合は、ガバナンス方法論を使用して、[ガバナンス ポリシー、プロセス、および規範](../govern/index.md)を確立します。

<!-- markdownlint-disable MD033 -->
<br>

| 説明責任チーム | 実行責任チームとサポート チーム |
| --- | --- |
| <li> クラウド ガバナンス チーム | <li> クラウド戦略チーム <li> クラウド セキュリティ チーム <li> クラウドのセンター オブ エクセレンスまたは中央 IT |

## <a name="next-steps"></a>次のステップ

これらの手順では、企業全体のセキュリティ リスクを一貫して管理するために必要な適切な戦略、制御、プロセス、スキル、文化を導入する方法を説明します。
続けてクラウド セキュリティの運用モードに入る場合は、この入門ガイダンスの構築のこれらの次の手順を検討してください。

- セキュリティ担当者がサイバーセキュリティ戦略、アーキテクチャ、優先順位付けされたロードマップを策定して改善するのに役立つ技術的なガイダンスを提供する、[Microsoft のセキュリティ ドキュメント](https://docs.microsoft.com/security)を確認します。
- [Azure サービスの組み込みのセキュリティ制御](https://docs.microsoft.com/azure/security/fundamentals/security-controls)について、セキュリティ情報を確認します。
- 「[Azure で利用できるセキュリティ サービスとテクノロジ](https://docs.microsoft.com/azure/security/azure-security-services-technologies)」で、Azure セキュリティ ツールとサービスを確認します。
- [Microsoft Trust Center](https://www.microsoft.com/trustcenter/guidance/risk-assessment) を確認します。ここには、広範なガイダンス、レポート、および規制遵守プロセスの一環としてリスク評価を行うのに役立つ関連ドキュメントが含まれているためです。
- セキュリティ要件を容易に満たすために使用できるサードパーティ ツールを確認します。 詳細については、「[Azure Security Center でのセキュリティ ソリューションの統合](https://docs.microsoft.com/azure/security-center/security-center-partner-integration)」を参照してください。
