---
title: Microsoft クラウド導入フレームワークの最新情報
description: Azure 向けの Microsoft クラウド導入フレームワークの最新情報について取り上げます。
author: JanetCThomas
ms.author: janet
ms.date: 03/01/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: overview
ms.custom: internal
ms.openlocfilehash: 961128094a2c3e2b36a3d5ff932d8410addcfad4
ms.sourcegitcommit: 51565dc4d3a1858bd62f708f2e4c082fbd4c6fe4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/20/2021
ms.locfileid: "107746923"
---
<!-- docutune:casing "Getting Started module" "internal Microsoft teams" OneMigrate -->

# <a name="whats-new-in-the-microsoft-cloud-adoption-framework-for-azure"></a>Azure 向けの Microsoft クラウド導入フレームワークの最新情報

クラウド導入フレームワークに加えられた最近の変更の一覧を次に示します。

このフレームワークは、お客様、パートナー、および Microsoft の社内チームと協力して構築されています。 新しいコンテンツと更新されたコンテンツは、使用可能になった時点でリリースされます。 これらのリリースを使用して、Microsoft と共にガイダンスをテストし、検証し、改良することができます。 Microsoft と連携してクラウド導入フレームワークを構築することをお勧めします。

## <a name="march-2021"></a>2021 年 3 月

このリリースはフレームワーク最大の更新プログラムであり、フレームワーク全体にまたがる一連の新しいガイダンスが多数追加されています。

### <a name="adoption-journeys"></a>導入体験

このリリースで最も注目すべき点は、導入体験が追加されたことです。これにより、より深いフレームワークの上にある短く消費可能なオーバーレイやレンズが得られ、より迅速にエンゲージメントを進めることができます。 この短いガイドでは、特定のテクノロジ プラットフォームの導入に向けて、クラウド導入フレームワーク、 [Microsoft Azure Well-Architected Framework](/azure/architecture/framework/)、 [Azure アーキテクチャ センター](/azure/architecture)、 [Microsoft Learn](/learn)、およびその他の [Microsoft ドキュメント](/) のガイダンスを適用する方法を説明します。 次の表に、それぞれの新しい体験の概要ページへのリンクを示します。

| Journey | 説明 |
|--|--|
| [ハイブリッド &nbsp; と &nbsp; マルチクラウド](../scenarios/hybrid/index.md) | ハイブリッド、マルチクラウド、統合された操作をクラウドの導入過程に統合するためのライフサイクル ガイド。 |
| [最新のコンテナー](../scenarios/aks/index.md) | コンテナーの改革によって、迅速なイノベーションとワークロードの移植性が実現します。 最新のコンテナーをクラウド導入体験に統合する方法を確認します。 |
| [SAP on Azure](../scenarios/sap/index.md) | このユーザー体験は、OneMigrate (移行シナリオ) コミットメントの一環として、SAP 移行プロセスとその他のメインストリーム移行プロセスの間のギャップを橋渡しとなり、SAP on Azure の大規模な導入を実現します。 |

### <a name="cloud-economics"></a>クラウドの経済性

Microsoft の[クラウド経済プログラム](https://azure.microsoft.com/overview/cloud-economics/)を統合することによって、[戦略手法](../strategy/index.md)を最新の状態に更新するための最初の手順として、フィードバックや教訓を構築します。

ビジネス成果の各カテゴリの概要を更新し、クラウドの経済性に関するケース スタディへの参照を追加し、組織において関連するビジネス目標を実現する方法を示しています。 具体的なケース スタディで更新された概要は以下のとおりです。

- [財務上の結果](../strategy/business-outcomes/fiscal-outcomes.md)
- [機敏性成果](../strategy/business-outcomes/agility-outcomes.md)
- [グローバルなリーチの成果](../strategy/business-outcomes/reach-outcomes.md)
- [顧客エンゲージメントの成果](../strategy/business-outcomes/engagement-outcomes.md)
- [パフォーマンスの成果](../strategy/business-outcomes/performance-outcomes.md)
- [持続可能性の目標](../strategy/business-outcomes/sustainability.md)

### <a name="enterprise-scale-updates"></a>エンタープライズ規模の更新プログラム

[ネットワークトポロジと接続](../ready/azure-best-practices/define-an-azure-network-topology.md)の重要なデザイン領域には、ネットワーク設計の個々のコンポーネントの合理化を簡略化する新しい記事が含まれています。 これらの設計の側面には、Oracle クラウド インフラストラクチャなどの [マルチクラウド プロバイダーへの接続](../ready/azure-best-practices/connectivity-to-other-providers.md) に関するガイダンスが含まれるようになりました。 また、新しいエンタープライズ規模の Terraform モジュールもリリースされており、Microsoft が Azure ランディング ゾーンの構成に対するオープンソース アプローチへの継続的な投資を示すことができます。 最後に、クラウドのガバナンス要件を満たすために、企業が Azure で[管理グループを最適化し、サブスクリプションを整理する](../ready/enterprise-scale/management-group-and-subscription-organization)方法についてのガイダンスを更新しました。

### <a name="anti-patterns"></a>アンチ パターン

企業はクラウド導入の取り組みにおいて重要な手順を見逃しがちです。 新しい [クラウド導入のアンチパターン](../antipatterns/antipatterns-to-avoid.md) ガイダンスでは、お客様の一般的な問題点、その問題の原因となったスキップされる手順、回復の最速パスを取り上げています。 アンチパターンは各方法に沿って配布されますが、フレームワークの作業の開始セクションには上位 10 個のリストがあります。

## <a name="january-2021"></a>2021 年 1 月

導入とイノベーションを促進するため、GitHub の使用に関する新しい情報を追加し、機械学習のベスト プラクティスを更新しました。 最適なランディング ゾーンを選択するのに役立つ新しい記事と動画を公開しました。

| [アーティクル] | 説明 |
|--|--|
| [GitHub&nbsp; による&nbsp;クラウド&nbsp;導入&nbsp;の促進](../scenarios/github-velocity/index.md) | この記事では、クラウドの導入を促進するために、オープンソース アセット、共同開発環境、自動化およびセキュリティ機能を持つ GitHub を活用する利点について説明します。 |
| [機械学習のベストプラクティス](../innovate/best-practices/machine-learning.md) | Machine Learning のベスト プラクティスのガイダンスを更新して拡張しました。 ベスト プラクティスには次のものが含まれます。 <br><br> <li> [機械学習運用のアプローチ方法](../innovate/best-practices/how-to-approach-mlops.md)および [MLOps プロセス](../innovate/best-practices/mlops-process.md) <li> [機械学習のセキュリティ](../innovate/best-practices/ml-security.md) <li> [機械学習推論とデプロイ](../innovate/best-practices/ml-deployment-inference.md) <li> [モデルに合ったコンピューティング インスタンスの決定](../innovate/best-practices/dev-train-comp-instances-for-ml.md) <li> [機械学習ワークスペースの設定](../innovate/best-practices/set-up-ml-workspaces.md) <li> [責任ある信頼された AI](../innovate/best-practices/trusted-ai.md) |
| [ランディング&nbsp; &nbsp;ゾーンの&nbsp;オプションを&nbsp;選択する](../ready/landing-zone/choose-landing-zone-option.md) | Microsoft では、ランディング ゾーンを実装する 2 つのオプションを提供しています。*小規模から始めて拡張する* と *エンタープライズ規模* です。 この新しい記事を使用して、両方のオプションを確認し、組織に適したアプローチを選択します。 |

## <a name="december-2020"></a>2020 年 12 月

名前付けとタグ付けの戦略に関するガイダンスを修正し、Moodle の移行シナリオに関するガイダンスを追加しました。

| [アーティクル] | 説明 |
|--|--|
| [Azure リソースの名前付けとタグ付けの戦略を作成する&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;](../ready/azure-best-practices/naming-and-tagging.md) | 名前付けとタグ付けの戦略の定義に関するガイダンスを改善しました。 概要に加えて、このガイダンスをこれらのトピックを扱う複数の記事に分けました。 <br><br> <li> [名前付け規則を定義する](../ready/azure-best-practices/resource-naming.md) <li> [さまざまな Azure リソースの種類に推奨される省略形](../ready/azure-best-practices/resource-abbreviations.md) <li> [タグ付けの戦略を定義する](../ready/azure-best-practices/resource-tagging.md) |
| [Moodle デプロイを Azure に移行する](../scenarios/lamp/moodle/migration-overview.md) | Moodle のオープンソースのラーニング管理システムのデプロイをオンプレミス環境から Azure に移行する方法について説明します。 デプロイに Azure portal または Azure CLI を使用するための手順が用意されています。 |

## <a name="october-2020"></a>2020 年 10 月

今月の更新には、クラウド導入フレームワーク全体の段階的な改善と Web アセットのサポートが含まれます。

Microsoft は、クラウド導入フレームワークの適用を促進する Microsoft Learn モジュールを構築することに注力し、最大限の投資をしてきました。 今月、以下に示すモジュールをリリースしました。 作業の開始モジュールでは、業界に合わせた最初のガイダンスが提供されることにご注意ください。ここでは、小売の顧客 (Tailwind Traders) が、次のすべての主要な手法モジュールについて説明します。

| モジュール | 説明 |
|--|--|
| [概要モジュール](/learn/modules/microsoft-cloud-adoption-framework-for-azure/) | フレームワークに関するエントリレベルの概要。 |
| [作業の開始モジュール](/learn/modules/cloud-adoption-framework-getting-started/) | 作業の開始の概要では、適切な手法の適用を促進し、特定の阻害要因を克服する方法について説明します。 |
| [Azure ランディング ゾーン](/learn/modules/cloud-adoption-framework-ready/) | クラウド環境を構築する前に、運用要件を把握し、作業を開始する最適な Azure ランディング ゾーン製品を選択します。 |
| [エンタープライズ規模のアーキテクチャを作成する](/learn/paths/enterprise-scale-architecture/) | 一連のエンタープライズ規模の設計原則、参照アーキテクチャ、参照実装に従って、規模に応じたランディング ゾーンを作成します。 4 つのモジュールで、成功のための単一のラーニング パスを作成する方法を学びます。 |

また、ビジネスの成果を広げることにより、さまざまな一般的なビジネスの動機と、COVID 後の市場で発展し続けるアプローチを共有できるようになりました。

| [アーティクル] | 説明 |
|--|--|
| [持続可能性の成果の例](../strategy/business-outcomes/sustainability.md) | クラウド コンピューティングが、二酸化炭素排出量の削減、資源の効率的な使用、環境フットプリントの削減にどのように役立つかについて説明します。 |
| [目標と成果指標 (OKR) を使用してビジネスの成果を測定する](../strategy/business-outcomes/okr.md) | OKR を使用してビジネスの成果を測定する方法について説明します。 |
| [AppDynamics を使用してビジネス成果を測定する](../digital-estate/app-dynamics.md) | アプリケーションのパフォーマンスおよびユーザー エクスペリエンスを把握することは、ビジネスの成果を測定するための鍵です。 AppDynamics が、ほとんどのユース ケースに対してビジネスの分析情報を提供する方法をご確認ください。 |
| [コスト管理の更新:スポット VM](../govern/cost-management/best-practices.md#best-practice-reduce-nonproduction-costs) | 非運用環境でスポット VM を使用することは、これらの既存の環境でのコストをさらに削減するために急速に普及してきている手法です。 「私は既に作業環境があります。 エンタープライズ規模の設計原則を適用するにはどうすればよいですか。」 エンタープライズ規模への移行に関する新しい記事が役立ちます。 |

| [アーティクル] | 説明 |
|--|--|
| [既存の Azure 環境をエンタープライズ規模に移行する](../ready/enterprise-scale/transition.md) | この記事は、組織が、既存の Azure 環境に基づいてエンタープライズ規模への移行を適切に進めることができるように支援します。 |
| [クラウド導入フレームワークのエンタープライズ規模ランディング ゾーン アーキテクチャ](../ready/enterprise-scale/architecture.md) | この記事は、ハブ アンド スポーク ネットワーク トポロジに基づいた、エンタープライズ規模のランディング ゾーン アーキテクチャの概要図を含むように更新されました。また、エンタープライズ規模のランディング ゾーン アーキテクチャの重要な設計領域について説明し、相互参照を行うように更新されます。 |

## <a name="august-25-2020"></a>2020 年 8 月 25 日

このリリースでは、ランディング ゾーンの実装に関する定義と意思決定の基準が改善されています。

### <a name="operating-model"></a>運用モデル

ランディング ゾーンの設計と実装で最も重要な考慮事項の 1 つは、運用モデルです。 クラウドでどのように運用する必要があるかが、実装するアーキテクチャと制御に直接影響します。 次の記事は、ターゲットの運用モデルを、クラウドで一般的ないくつかのモデルと整合させるために役立ちます。 次に、これらを最適な実装にマップして開始します。

| [アーティクル] | 説明 |
|--|--|
| [一般的な運用モデルを比較する](../operating-model/compare.md) | この記事は、運用モデルを比較して一連の作業を選択するための主要なガイドです。 |
| [クラウド運用モデルを理解する](../operating-model/index.md) | 運用モデルに関するインポートの決定を行うための入門書。 |
| [CAF を使用して運用モデルを定義する](../operating-model/define.md) | クラウド導入フレームワークは、環境を構築し、選択した運用モデル内にクラウドを導入するための段階的なガイドです。 この記事は、さまざまな手法によって運用モデルの開発がどのようにサポートされるかを理解するための枠組みをもたらします。 |
| [使用条件](../operating-model/terms.md) | 運用モデルについて担当者と議論するときに出てくる可能性のある用語。 これらの用語は、アーキテクトやテクニカル スペシャリストがよく使用するものではありませんが、こうした会話では重要です。 |

### <a name="azure-landing-zones-additional-implementation-options"></a>Azure ランディング ゾーン: 追加の実装オプション

Azure ランディング ゾーンの背後にある概念と実装オプションは、主要な Microsoft パートナーと共に構築されました。 このリリースでは、これらの パートナーがクラウド導入を促進するために使用する既存の知的所有権 (IP) が認められています。

| [アーティクル] | 説明 |
|--|--|
| [パートナー ランディング ゾーン](../ready/landing-zone/partner-landing-zone.md) | パートナーの Azure ランディング ゾーンのオファーを確認し、比較します。 |
| [実装オプション](../ready/landing-zone/implementation-options.md) | 既存の Azure ランディング ゾーンの実装オプションにパートナー ランディング ゾーンのオプションを追加するよう更新されました。 |
| [エンタープライズ規模の参照実装](../ready/enterprise-scale/implementation.md) | エンタープライズ規模の参照実装にハブスポーク参照実装が追加されました。 |

> [!NOTE]
> 新しいパートナー ランディング ゾーンの記事には、パートナーがランディング ゾーンを定義または実装する方法が明記されていません。 代わりに、複雑な会話に構造を持たせるように設計されているので、パートナー オファーへの理解を深めることができます。 この一覧の質問と最小評価基準を使用して、可能性のあるパートナーのオファーを比較することもできます。 また、これは Azure ランディング ゾーンの実装オプションの価値をより明確に伝えるために、一部のパートナーにも使用されています。

## <a name="july-17-2020"></a>2020 年 7 月 17 日

このリリースでは、クラウドの導入を実行に移しやすくするために、新しいシナリオが多数追加されています。

**移行シナリオ:**

新しい[移行シナリオ概要ページ](../scenarios/index.md)では、移行手法を基に、Azure の #OneMigrate ソリューションがいかに実現されているかを紹介しています。 ファーストおよびサード パーティの複数のシナリオを Azure に移行するためのアプローチが紹介されています。 これには、次に示す新しい移行シナリオが含まれます。

| [アーティクル] | 説明 |
|--|--|
| [Windows Virtual Desktop](../scenarios/wvd/index.md) | このシナリオでは、生産性の向上を実現し、エンドユーザーのエクスペリエンスをサポートするためのさまざまなワークロードの移行を迅速化します。 |
| [Azure Stack](../scenarios/azure-stack/index.md) | Azure Stack Hub を使用してデータセンターに Azure をデプロイする方法について説明します。 |

**クラウド導入フレームワークにおける分析:**

Microsoft クラウド導入フレームワークに分析ソリューションが追加されました。 これらの新しいトピックでは、クラウドの導入過程で分析ソリューションを有効にするためのベスト プラクティスを紹介しています。

| [アーティクル] | 説明 |
|--|--|
| [Teradata、Netezza、Exadata の分析ソリューション](../migrate/azure-best-practices/analytics/analytics-solutions-overview.md) | Teradata、Netezza、Exadata を含むオンプレミスのレガシ環境から最新の分析ソリューションへの移行について説明します。 |
| [Azure Synapse の高可用性](../migrate/azure-best-practices/analytics/azure-synapse.md) | 最新のクラウドベース インフラストラクチャがもたらす主な利点の 1 つである組み込みの高可用性とディザスター リカバリーについて説明します。 |
| [スキーマ移行のデータ定義言語 (DDL)](../migrate/azure-best-practices/analytics/schema-migration-ddl.md) | データベース オブジェクトについて説明すると共に、既存のデータを移行するにあたっての関連するプロセスについて説明します。 |

**クラウド導入フレームワークにおける AI:**

Microsoft クラウド導入フレームワークに AI ソリューションとベスト プラクティスが統合されました。 顧客ニーズの予測に関するイノベーションの促進、ビジネス プロセスの自動化、情報の発見、顧客との関係を深める新たな方法の模索、クラウド導入の取り組みにおけるエクスペリエンスの向上に、これらの AI ソリューションを活かすことができます。

| [アーティクル] | 説明 |
|--|--|
| [責任ある AI](../strategy/responsible-ai.md) | AI ソリューションを導入する際に考慮すべき AI の原則について説明すると共に、責任ある AI の戦略をいかに確立するかについて説明します。 |
| [Azure イノベーション ガイド: AI によるイノベーション](../innovate/innovation-guide/predict.md) | AI によってイノベーションを起こし、導入ニーズに応じて最良のソリューションを見つける方法について説明します。 |
| [クラウド導入フレームワークにおける AI](../innovate/ai/index.md) | ツール、プログラム、コンテンツ (ベスト プラクティス、構成テンプレート、アーキテクチャ ガイダンス) を含む規範的なフレームワークをレビューして、大規模な AI とクラウド ネイティブ プラクティスの導入を簡略化します。 |
| [Azure Machine Learning を使用した MLOps](../manage/mlops-machine-learning.md) | 機械学習の運用 (MLOps) のベスト プラクティスについて説明します。 |
| [AI によるイノベーション](../innovate/best-practices/predict.md) | デジタル発明の促進につながる AI ソリューション (機械学習、AI アプリケーションとエージェント、ナレッジ マイニング) とベスト プラクティスについて説明します。 |

## <a name="june-15-2020"></a>2020 年 6 月 15 日

クラウド環境を適切に構成することは、多くの場合、クラウドの導入における最初の最も一般的な技術的障害です。 このリリースでは、クラウド環境のデプロイを迅速化するガイダンスに大きな重点が置かれています。 この一般的な障害を克服するために、クラウド導入フレームワークには **Azure ランディング ゾーン** が導入されています。

| [アーティクル] | 説明 |
|--|--|
| [Azure ランディング ゾーン](../ready/landing-zone/index.md) | Azure ランディング ゾーンでは、クラウド導入計画とクラウド運用モデルと整合した環境の作成を高速化するための、設計領域と実装オプションの共通セットが作成されます。 この新しい記事では、Azure ランディング ゾーンがより明確に定義されています。 |
| [Azure ランディング ゾーン: 設計領域](../ready/landing-zone/design-areas.md) | すべての Azure ランディング ゾーンでは、8 つの設計領域の共通セットが共有されています。 Azure ランディング ゾーンをデプロイする前に、お客様はこれらの各設計を検討し、重要な決定を行う必要があります。 |
| [Azure ランディング ゾーン: 実装オプション](../ready/landing-zone/implementation-options.md) | クラウド導入計画とクラウド運用モデルに応じて、Azure ランディングゾーンの最適な実装オプションを選択します。 |

既存の CAF ブループリント定義と CAF Terraform モジュールは、Azure ランディング ゾーンの実装の出発点となります。 しかしながら、エンタープライズ規模のクラウド導入計画の需要に対応できる、より高度な実装オプションを必要とされるお客様もいます。 このリリースでは、そのようなニーズに応えるため、Azure ランディング ゾーンの実装オプションに **CAF エンタープライズ規模** が追加されています。 以下では、CAF エンタープライズ規模のアーキテクチャとリファレンス実装の概要を理解するための記事をいくつか示します。

| [アーティクル] | 説明 |
|--|--|
| [エンタープライズ規模の概要](../ready/enterprise-scale/index.md) | エンタープライズ規模の概要です |
| [CAF のエンタープライズ規模のランディング ゾーンを実装する](../ready/enterprise-scale/implementation.md) | 短時間で実装するためのオプションと GitHub の例です |
| [エンタープライズ規模のアーキテクチャ](../ready/enterprise-scale/architecture.md) | エンタープライズ規模のアーキテクチャについて説明します |
| [エンタープライズ規模の設計の原理](../ready/enterprise-scale/design-principles.md) | このアプローチがクラウド運用モデルに適合するかどうかを評価するための実装時の意思決定に役立つアーキテクチャの設計の原理について説明します |
| [エンタープライズ規模の設計のガイドライン](../ready/enterprise-scale/design-guidelines.md) | Azure ランディング ゾーンの共通設計領域を実現するためにエンタープライズ規模のガイドラインを評価します |
| [実装ガイドライン](../ready/enterprise-scale/implementation-guidelines.md) | デプロイの前に、エンタープライズ規模の実装に必要なアクティビティを確認します |

パートナーは、クラウド導入を成功させるための重要な要素です。 クラウド導入フレームワークのガイダンスの全体を通して、パートナーが果たす重要な役割と、お客様がより適切にパートナーと関わる方法を示すリファレンスが追加されています。 検証済みの CAF パートナーの一覧については、[CAF 対応のパートナー プラン](https://aka.ms/adopt/partneroffers)、[Azure Expert マネージド サービス プロバイダー (MSP)](https://www.microsoft.com/azure/partners/azureexpertmsp?filters=all)、[上級スペシャリスト パートナー](https://www.microsoft.com/azure/partners/advspec)に関するページをご覧ください。

## <a name="may-15-2020"></a>2020 年 5 月 15 日

フィードバックに基づいて、クラウド導入フレームワークの使用を開始するための新しいコンテンツを作成しました。 新しいファースト ステップ ガイドは、実行する内容に基づいてフレームワークを移動するのに役立ちます。 また、クラウド導入の成功をサポートするガイダンス、ツール、学習モジュールとプログラムを簡単に見つけることができるように、新しいランディング ページも作成しました。

| [アーティクル] | 説明 |
|--|--|
| [Azure 向けのクラウド導入フレームワーク](../index.yml) | クラウド導入の成功をサポートするガイダンス、ツール、学習モジュールとプログラムを簡単に見つけることができるように、クラウド導入フレームワークのランディング ページのデザインが変更されました。 |
| [クラウド導入フレームワークの概要](./index.md) | クラウドの導入目標に合ったファースト ステップ ガイドを選択します。 これらの一般的なシナリオでは、Azure 向けの Microsoft クラウド導入フレームワークを使用したロードマップを提供します。 |
| [基本的なアラインメントに関する決定事項を理解し、文書化する](./cloud-concepts.md) | クラウドの導入に関与するすべてのチームが理解する必要がある最初の決定事項について学習します。 |
| [ポートフォリオ階層を理解して整理する](../reference/fundamental-concepts/hosting-hierarchy.md) | ポートフォリオ階層で、ワークロードとサポート サービスがどのようにまとめられているかについて学習します。 |
| [Azure 製品によるポートフォリオ階層のサポート方法](../reference/fundamental-concepts/hierarchy-azure-tools.md) | ポートフォリオ階層をサポートする Azure ツールとソリューションについて学習します。 |
| [組織の配置を管理する](../organize/index.md) | クラウドの効果的な運用モデルとして、適切に配置された組織構造を確立します。 |

## <a name="april-14-2020"></a>2020 年 4 月 14 日

すべてのクラウド導入ツールとテンプレートを 1 か所にまとめて、簡単に見つけられるようにしました。

| [アーティクル] | 説明 |
|--|--|
| [ツールとテンプレート](../reference/tools-templates.md) | クラウドの導入を促進するのに役立つツール、テンプレート、評価を見つけてください。 |

## <a name="april-4-2020"></a>2020 年 4 月 4 日

移行手法と準備手法の改良を引き続き繰り返して、Microsoft のお客様、パートナー、および社内のプログラムからのフィードバックをより正確に反映しました。

**移行手法の更新:**

| [アーティクル] | 説明 |
|--|--|
| [移行手法](../migrate/index.md) | これらの変更により、移行作業のフェーズ (ワークロードの評価、ワークロードのデプロイ、およびワークロードのリリース) が効率化されます。 これらの変更により、移行バックログに関する詳細情報も削除されます。 これらの詳細情報を削除し、計画、準備、および導入の手法を参照することで、代わりに、さまざまな異なるクラウド導入プログラムに柔軟性をもたらし、手法との整合性を高めます。 |

**準備手法の更新:**

| [アーティクル] | 説明 |
|--|--|
| [ランディング ゾーンのリファクター](../ready/landing-zone/refactor.md) | **新しい記事**: この記事では、準備手法を利用して、初期テンプレートから始め、デシジョン ツリーとリファクタリングを使用してランディング ゾーンを展開し、エンタープライズ対応の将来の状態に移行するという理論について説明します。 |
| [ランディング ゾーンを展開する](../ready/considerations/index.md) | **新しい記事**: リファクタリングに関する記事の並列での反復処理のセクションを基に、さまざまな種類のランディング ゾーンの展開によって、共有の原則がサポート対象のプラットフォームに埋め込まれるしくみを示します。 この概要の元の内容は、目次の「[基本的なランディング ゾーンに関する考慮事項](../ready/considerations/basic-considerations.md)」ノードに移動されました。 |
| [ランディング ゾーン用のテスト駆動開発 (TDD)](../ready/considerations/test-driven-development.md) | **新しい記事**: リファクタリング アプローチは、ランディング ゾーンの開発とリファクタリングをガイドするためにテスト駆動開発サイクルを導入することによって、大幅に改善されています。 |
| [Azure でのランディング ゾーンの TDD](../ready/considerations/azure-test-driven-development.md) | **新しい記事**: Azure ガバナンス ツールは、TDD サイクルや赤と緑のテスト用の豊富なプラットフォームを提供します。 |
| [ランディング ゾーンのセキュリティの向上](../ready/considerations/landing-zone-security.md) | **新しい記事**: このセクションのベスト プラクティスの概要。再び TDD サイクルに関連付けられています。 |
| [ランディング ゾーンの運用の改善](../ready/considerations/landing-zone-operations.md) | **新しい記事**: 運用、信頼性、およびパフォーマンスを向上させるためのモジュール方式のアプローチへの移行を伴う、管理手法のベスト プラクティスの一覧。 |
| [ランディング ゾーンのガバナンスの改善](../ready/considerations/landing-zone-governance.md) | **新しい記事**: ガバナンス、コスト管理、およびスケールを向上させるためのモジュール方式のアプローチへの移行を伴う、ガバナンス手法に関連するベスト プラクティスの一覧。 |
| [エンタープライズ規模から始める](../ready/enterprise-scale/index.md) | **新しい記事**: お客様が CAF のエンタープライズ規模のランディング ゾーンのテンプレートから開始したときの、プロセスの違いを示すアプローチを説明します。 この記事は、お客様がこの決定をサポートする資格条件を理解するのに役立ちます。 |

## <a name="march-27-2020"></a>2020 年 3 月 27 日

Azure の導入時に作成する必要がある初期サブスクリプションについてのガイダンスを追加しました。

**サブスクリプション ガイダンスの更新:**

| [アーティクル] | 説明 |
|--|--|
| [Azure の初期サブスクリプションを作成する](../ready/azure-best-practices/initial-subscriptions.md) | **新しい記事**: 運用および非運用の初期サブスクリプションを作成し、サンドボックス サブスクリプションおよび共有サービスを含むサブスクリプションを作成するかどうかを決定します。 |
| [追加のサブスクリプションを作成して、Azure 環境をスケーリングする](../ready/azure-best-practices/scale-subscriptions.md) | 追加のサブスクリプションを作成する理由、サブスクリプション間でのリソースの移動、および新しいサブスクリプションを作成するためのヒントについて説明します。 |
| [複数の Azure サブスクリプションの整理と管理](../ready/azure-best-practices/organize-subscriptions.md) | 管理グループ階層を作成して、Azure サブスクリプションを整理および管理できるようにします。 |

## <a name="march-20-2020"></a>2020 年 3 月 20 日

スケーリングと最適化の前に、Kubernetes でアプリケーションの概念実証から運用環境へのデプロイを成功させるために、ペルソナ別に分類されたツール、プログラム、およびコンテンツを含む規範的なガイダンスを追加しました。

**Kubernetes:**

| [アーティクル] | 説明 |
|--|--|
| [アプリケーションの開発とデプロイ](../innovate/kubernetes/application-development.md) | **新しい記事**: アプリケーション開発の計画、CI/CD パイプラインの構成、Kubernetes 用のサイト信頼性エンジニアリングの実装に関するチェックリスト、リソース、およびベスト プラクティスが提供されています。 |
| [クラスターの設計と操作](../innovate/kubernetes/cluster-design-operations.md) | **新しい記事**: Kubernetes 用のクラスター構成、ネットワーク設計、将来も対応できるスケーラビリティ、ビジネス継続性、およびディザスター リカバリーに関するチェックリスト、リソース、およびベスト プラクティスが提供されています。 |
| [クラスターとアプリケーションのセキュリティ](../innovate/kubernetes/cluster-application-security.md) | **新しい記事**: Kubernetes のセキュリティ計画、運用、およびスケーリングに関するチェックリスト、リソース、およびベスト プラクティスが提供されています。 |

## <a name="march-2-2020"></a>2020 年 3 月 2 日

<!-- docutune:casing "Strategy, Plan, Ready, and Migrate" -->

戦略、計画、準備、および移行が含まれるクラウド導入フレームワークの複数のセクションを貫く移行アプローチの継続性に関するフィードバックに応えて、次の更新が行われています。 これらの更新は、移行の取り組みを進める際に、計画と導入をどのように改善するかを理解しやすくすることを目的として行われています。

**戦略手法の更新:**

| [アーティクル] | 説明 |
|--|--|
| [ポートフォリオのバランスを取る](../strategy/balance-the-portfolio.md): | 戦略メソドロジの中で先に読まれるように、この記事を前のほうに移動しました。 これにより、ライフサイクルのより早い段階で、この思考プロセスを認識できます。 |
| [&nbsp;競合の優先順位のバランス&nbsp;](../strategy/balance-competing-priorities.md) | **新しい記事**: 戦略を伝えるために役立つメソドロジ間の優先順位のバランスを概説しています。 |

**計画手法の更新:**

| [アーティクル] | 説明 |
|--|--|
| [&nbsp;評価のベスト プラクティス&nbsp;](../plan/contoso-migration-assessment.md) | この記事を、計画手法の新しい「ベスト プラクティス」セクションに移動しました。 これにより、ライフサイクルのより早い段階で、ローカル環境の評価というプラクティスを認識できます。 |

**準備手法の更新:**

| [アーティクル] | 説明 |
|--|--|
| [&nbsp;&nbsp;ランディング ゾーンとは&nbsp;&nbsp;](../ready/landing-zone/index.md) | **新しい記事**: ランディング ゾーンという用語を定義しています。 |
| 最初のランディング ゾーン | **新しい記事**: 各種ランディング ゾーンの比較について、詳細に説明しています。 |
| [CAF 移行ランディング ゾーン](../ready/landing-zone/migrate-landing-zone.md) | 最初のランディング ゾーンの選択から、ブループリントの定義を分けました。 |
| [CAF Terraform モジュール](../ready/landing-zone/terraform-landing-zone.md) | ランディング ゾーンの説明の中で Terraform について特に説明するために、準備手法の新しい「ランディング ゾーン」セクションに移動しました。 |

**移行手法の更新:**

| [アーティクル] | 説明 |
|--|--|
| [概要](../migrate/azure-migration-guide/index.md) | 説明をより明確にし、手順を減らすことによってガイドを更新しました。 |
| [評価](../migrate/azure-migration-guide/assess.md) | このレベルの評価が、計画手法で言及されている段階的評価アプローチでどのような働きをするかを示すために、「前提条件の吟味」セクションを追加しました。 |
| [評価プロセス中の分類](../migrate/migration-considerations/assess/classify.md) | **新しい記事**: すべての資産とワークロードを移行前に分類しておくことの重要性を概説しています。 |
| [移行](../migrate/azure-migration-guide/migrate.md) | ティア 1 カンファレンスでのフィードバックに応えて、サードパーティ ツール オプションに UnifyCloud への参照を追加しました。 |
| [&nbsp;&nbsp;テスト、最適化、および昇格&nbsp;](../migrate/azure-migration-guide/optimize-and-transform.md) | この記事のタイトルを、プロセス改善に関する他の提案に合わせて調整しました。 |
| [評価の概要](../migrate/migration-considerations/assess/index.md) | 特定のワークロードとその関連資産の技術的な適合性を評価することがこのフェーズにおける評価のねらいであることを示すように更新しました。 |
| [計画チェックリスト](../migrate/migration-considerations/prerequisites/planning-checklist.md) | 移行後のワークロードの管理を徹底するために、移行作業に向けた計画段階で運用の整合を取っておくことの重要性を明確にするように更新しました。 |

<!-- docutune:ignoreNextStep -->
