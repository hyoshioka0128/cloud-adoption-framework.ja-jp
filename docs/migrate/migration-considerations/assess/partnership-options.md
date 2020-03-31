---
title: パートナーシップとサポート オプションを理解する
description: Azure 向けのクラウド導入フレームワークを使用し、移行コストをサポートするためのパートナーシップのオプションとアプローチについて説明します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 04/04/2019
ms.topic: guide
ms.service: cloud-adoption-framework
ms.subservice: migrate
ms.openlocfilehash: 0d7207ce5073d1e3e67e2164774ee0da0eddc784
ms.sourcegitcommit: ea63be7fa94a75335223bd84d065ad3ea1d54fdb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "80355317"
---
<!-- cSpell:ignore CSPs MSPs -->

# <a name="understand-partnership-options"></a>パートナーシップのオプションを理解する

移行の間に、クラウド導入チームは実際にクラウドへのワークロードの移行を行います。 [デジタル資産](../../../digital-estate/index.md)を定義したりコア クラウド インフラストラクチャを構築したりするときの共同作業タスクや問題解決タスクとは異なり、移行では一連のタスクを繰り返し実行する傾向があります。 反復的側面が過ぎると、テストやチューニングのような選択したクラウド プロバイダーについて深い知識を必要とする作業があります。 このプロセスの反復的な性質は、パートナーによる作業が最適な場合があり、フルタイム スタッフの負担が軽減されます。 さらに、パートナーであれば、反復的プロセスの実行で異常が発生したときでも、技術的な専門知識を適合させることができる可能性があります。

パートナーは、1 つのクラウド ベンダーまたは少数のクラウド ベンダーと密接に連携している傾向があります。 パートナーシップのオプションをわかりやすく説明するため、この記事の残りの部分では、Microsoft Azure が選択されたクラウド プロバイダーであると仮定します。

計画、構築、または移行の間に、通常、会社には 4 つの実行パートナーシップ オプションがあります。

- **ガイド付きセルフサービス。** 既存の技術チームが、Microsoft から支援を受けて、移行を実行します。
- **FastTrack for Azure。** Microsoft FastTrack for Azure プログラムを使用して、移行の期間を短縮します。
- **ソリューション パートナー。** Azure ソリューション パートナーまたはクラウド ソリューション パートナー (CSP) と連携して、移行を促進します。
- **サポート付きセルフサービス。** Microsoft からのサポートを受けて、既存の技術スタッフが実行します。

## <a name="guided-self-service"></a>ガイド付きセルフサービス

組織が自分で Azure への移行を行うことを計画している場合、Microsoft は移行過程の全体を通していつでも支援できます。 Azure への迅速な移行のため、Microsoft とパートナーは、仮想マシン、アプリケーション、データベースの移行のリスクが減って速度が速くなるよう、アーキテクチャ、ガイド、ツール、サービスの広範なセットを開発しています。 これらのツールとサービスは、幅広い種類のオペレーティング システム、プログラミング言語、フレームワーク、およびデータベースに対応しています。

- **評価と移行のツール。** Azure では、既存のインフラストラクチャの評価など、クラウド変換のさまざまなフェーズで使われる広範なツールが提供されています。 詳しくは、後の移行に関する章の評価に関するセクションを参照してください。
- **[Microsoft クラウド導入フレームワーク](../../index.md)。** このフレームワークでは、クラウドの導入と移行に対して構造化されたアプローチが提供されています。 Microsoft がサポートしている多くのお客様のエンゲージメントで最善のプラクティスに基づいており、アーキテクチャや設計から実装までの一連の手順として編成されています。 手順ごとに、アプリケーション アーキテクチャの設計に役立つ補助的なガイダンスがあります。
- **[クラウド設計パターン](https://docs.microsoft.com/azure/architecture/patterns)。** Azure では、信頼性が高くスケーラブルで安全なワークロードをクラウドで構築するための便利なクラウド設計パターンがいくつか提供されています。 パターンごとに、そのパターンで対処する問題、パターンの適用に関する考慮事項、Azure に基づいた例が説明されています。 ほとんどのパターンには、Azure でのパターンの実装方法を示すコード サンプルまたはスニペットが含まれています。 ただし、それらは、ホストが Azure か他のクラウド プラットフォームかにかかわらず、分散システムに関連しています。
- **[クラウドの基礎](https://docs.microsoft.com/azure/architecture/guide)。** 基礎は、主要な概念の実装に対する基本的な方法を教えるために役立ちます。 このガイドは、単一の Azure サービスの範囲を超えてソリューションについて考えている技術者に役立ちます。
- **[シナリオ例](https://docs.microsoft.com/azure/architecture/example-scenario)。** 実際のお客様からの参照情報が提供されており、過去のお客様が特定のビジネス目標を達成するために従ったツール、アプローチ、プロセスの概要がわかります。
- **[参照アーキテクチャ](https://docs.microsoft.com/azure/architecture/reference-architectures)。** 参照アーキテクチャは、関連するアーキテクチャをまとめてシナリオごとに配置されています。 各アーキテクチャの説明には、最善のプラクティスが、スケーラビリティ、可用性、管理性、およびセキュリティに関する考慮事項と共に含まれます。 また、ほとんどにはデプロイ可能なソリューションも含まれています。

## <a name="fasttrack-for-azure"></a>FastTrack for Azure

[FastTrack for Azure](https://azure.microsoft.com/roadmap/fasttrack-for-azure) では、Azure エンジニアから直接支援を受け、パートナーと協力して作業できるため、お客様は、Azure ソリューションを迅速に、自信を持って構築できます。 FastTrack では、お客様の実体験に基づくベスト プラクティスとツールが用意されており、Azure ソリューションのセットアップ、構成、開発から運用まで、お客様に対して次のようなガイドが提供されます。

- データセンターの移行
- Azure での Windows Server
- Azure での Linux
- SAP on Azure
- ビジネス継続性とディザスター リカバリー (BCDR)
- ハイ パフォーマンス コンピューティング*
- クラウドネイティブ アプリ
- DevOps
- アプリの最新化
- クラウド規模の分析**
- インテリジェントなアプリ
- インテリジェントなエージェント**
- Azure へのデータの最新化
- セキュリティと管理
- グローバルに分散されたデータ
- IoT***

\* 米国、カナダ、英国、および西ヨーロッパでの限定プレビュー

** 英国および西ヨーロッパでの限定プレビュー

*** 2019 年 H2 に利用可能

一般的な FastTrack for Azure のエンゲージメントでは、Azure ソリューションの計画と開発を成功させるためのビジネス ビジョンの定義を Microsoft がお手伝いします。 チームは、アーキテクチャのニーズを評価し、Azure ソリューションの構築、デプロイ、管理に役立つガイダンス、設計の原則、ツール、リソースを提供します。 チームは、要求があればデプロイ サービスに適したスキルを持つパートナーを割り当て、定期的にデプロイが順調であることを確認して、障害の排除を支援します。

FastTrack for Azure の一般的な連携の主な段階は次のとおりです。

- **検出** 主要な利害関係者を明らかにし、解決すべき問題の目標や展望を理解し、アーキテクチャ ニーズを評価します。
- **ソリューションの実現。** アプリケーション構築の設計原則を把握し、アプリケーションとソリューションのアーキテクチャをレビューし、概念実証 (PoC) 作業を運用段階に引き上げるためのガイダンスやツールを入手します。
- **継続的なパートナーシップ。** Azure のエンジニアとプログラム マネージャーは、ときどきデプロイが順調であることを確認し、障害の排除を支援します。

## <a name="microsoft-services-offerings-aligned-to-cloud-adoption-framework-approaches"></a>クラウド導入フレームワーク アプローチに合わせて配置された Microsoft Services オファリング

![Microsoft Services のクラウド導入フレームワーク アプローチ](../../../_images/migrate/mcs-program-approach.jpg)

**評価:** Microsoft Services では、アーキテクチャ ワーク ショップ、Azure のリアルタイム情報、セキュリティと ID の脅威モデル、さまざまなツールで構成される[統合された、データおよびツール主導アプローチ](https://download.microsoft.com/download/C/7/C/C7CEA89D-7BDB-4E08-B998-737C13107361/Secure_Cloud_Insights_Datasheet_EN_US.pdf)を使用して、既存の Azure 環境に対する課題、リスク、推奨事項、問題に関する分析情報と、[最新化の概要ロードマップ](https://download.microsoft.com/download/F/7/2/F72FAD7E-8BBD-4E04-8C7B-9AC4FE04A150/Cloud_Adoption_Discovery_and_Roadmap_Datasheet.pdf)などの重要な結果を提供します。

**導入:** Microsoft Services の [Azure Cloud Foundation](https://download.microsoft.com/download/D/8/7/D872DFD0-1C46-4145-95E4-B5EAB2958B96/Hybrid_Cloud_Foundation_Datasheet_EN_US.pdf) により、要件を最も適切な参照アーキテクチャにマップすることによって核となる Azure の設計、パターン、ガバナンス アーキテクチャを確立し、ワークロードに必要なインフラストラクチャ、管理、セキュリティ、ID を計画、設計、デプロイします。

**移行/最適化:** Microsoft Services の [Cloud Modernization Solution](https://download.microsoft.com/download/3/7/3/373F90E3-8568-44F3-B096-CD9C1CD28AB7/Cloud_Modernization_Datasheet_EN_US.pdf) では、アプリケーションとインフラストラクチャを Azure に移動し、合理化された移行によってクラウドにデプロイした後で最適化および最新化を行う、包括的なアプローチが提供されます。

**イノベーション:** Microsoft Services の[クラウドのセンター オブ エクセレンス (CCoE) ソリューション](https://download.microsoft.com/download/F/8/B/F8BBE4BD-E5F8-4DFB-82F7-C0A4E17051BB/Cloud_Center_of_Excellence_Datasheet_EN_US.pdf)では、DevOps コーチング エンゲージメントが提供され、DevOps の原則と規範的なクラウド ネイティブのサービス管理とセキュリティ コントロールの組み合わせにより、安全で予測可能かつ柔軟なサービス配信および運用管理機能で、ビジネス革新が推進され、アジリティが向上し、価値実現の時間が短縮されます。

## <a name="azure-support"></a>Azure Support

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)してください。 サポート リクエストに高度な技術的ガイダンスが必要な場合は、[Azure サポート プラン](https://azure.microsoft.com/support/plans)でニーズに最適なプランを探してください。

## <a name="azure-solutions-partner"></a>Azure ソリューション パートナー

Microsoft 認定ソリューション プロバイダーは、世界中で最新の Microsoft のテクノロジ ベースのカスタマー ソリューションを提供することを専門としています。 経験豊富なパートナーのサポートでクラウドでのビジネスを最適化します。

既製の Azure ソリューションまたはカスタム Azure ソリューションを提供するパートナー、また、それらのソリューションをデプロイし、管理するうえでの支援を提供するパートナーからサポートを得られます。

- **[クラウド ソリューション パートナーを見つける](https://www.microsoft.com/solution-providers/home)。** 認定 CSP は、クラウド導入のビジネス目標を評価し、ビジネス ニーズを満たして、ビジネスの機敏性と効率性の向上に役立つ最適なクラウド ソリューションを特定して、クラウドを最大限に活用する支援ができます。
- **[マネージド サービス パートナーを見つける](https://www.microsoft.com/solution-providers/search?cacheId=16a3b49b-fef2-449d-bdf0-628008114cca)。** Azure マネージド サービス パートナー (MSP) は、クラウド体験のあらゆる側面をガイドして、Azure へのビジネス転換を支援します。 移行および運用管理のコンサルティングから、クラウド MSP はクラウド導入に伴うあらゆる利点をお客様に紹介します。 また、一般的なサポート、プロビジョニング、課金エクスペリエンスのワンストップ ショップとしての役割も持ちます。すべてが柔軟な従量課金制ビジネス モデルで提供されます。

## <a name="next-steps"></a>次のステップ

パートナーとサポート戦略を選択した後は、[リリースとイテレーションのバックログ](./release-iteration-backlog.md)を更新し、計画的な作業と割り当てを反映できます。

> [!div class="nextstepaction"]
> [リリースとイテレーションのバックログを使用して変更を管理する](./release-iteration-backlog.md)
