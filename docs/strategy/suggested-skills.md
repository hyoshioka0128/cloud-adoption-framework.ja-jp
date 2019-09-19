---
title: スキル準備パス
titleSuffix: Microsoft Cloud Adoption Framework for Azure
description: スキル準備パスの概要
author: BrianBlanchard
ms.author: brblanch
ms.date: 05/19/2019
ms.topic: article
ms.service: cloud-adoption-framework
ms.subservice: strategy
ms.openlocfilehash: f9dc2a7276a8845ad19ce4196822b730e7a6502d
ms.sourcegitcommit: 443c28f3afeedfbfe8b9980875a54afdbebd83a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/16/2019
ms.locfileid: "71032156"
---
# <a name="skills-readiness-path-during-the-plan-phase-of-a-migration-journey"></a>移行の行程の計画フェーズ中のスキル準備パス

移行の行程の計画フェーズにおける目標は、移行の実装をガイドするために必要な計画を作成することです。 このフェーズには、次のように、重要なスキルがいくつか必要です。

- 展望を確立する。
- 業務上の正当な理由をまとめる。
- デジタル資産を合理化する。
- 移行バックログ (技術的計画) を作成する。

以降のセクションでは、これらのスキルのそれぞれを開発するためのラーニング パスを示します。

## <a name="establish-the-vision"></a>展望の確立

あらゆるクラウド導入作業の成功は、ビジネスの展望によって定義されます。 技術チームが動機や目的の成果を理解していないと、彼らがみずからビジネスの成功に向けた取り組みをガイドするのは困難です。 技術チーム向けにビジネスの展望を文書化して明確に伝える方法については、次の記事を参照してください。

- [導入の動機](./motivations.md)。 技術的な取り組みの背景にある理由を文書化し、明確に伝えます。
- [ビジネス成果](./business-outcomes/index.md)。 ビジネス上の変更に関して技術チームに期待されることを明確に伝えます。
- [学習メトリック](./learning-metrics.md)。 長期的なビジネス成果に向けた進捗状況を示すことができる短期的なメトリックを確立します。

## <a name="build-the-business-justification"></a>業務上の正当な理由の構築

クラウドを導入するための投資を正当化するには、組織の会計実務のより詳細な分析と理解が必要になる場合があります。 業務上の正当な理由に関する記事は、これらのスキルの開発に役立ちます。

- [クラウド移行のビジネス ケース](./cloud-migration-business-case.md)。 クラウド移行のためのビジネス ケースを確立します。

## <a name="rationalize-the-digital-estate"></a>デジタル資産の合理化

目的のビジネス ケースを現在および将来のデジタル資産インベントリと合わせることで、ビジネス ケースを精緻化できます。 これらの記事では、デジタル資産の合理化の実現について説明します。

- [増分型の合理化](../digital-estate/rationalize.md)。 遅延バインディングの技術的な決定を適切に調整する合理化に対するアジャイルなアプローチです。
- [合理化の R](../digital-estate/5-rs-of-rationalization.md)。 合理化のさまざまなオプションについて理解します。

## <a name="create-a-migration-backlog-technical-plan"></a>移行のバックログ (技術的計画) の作成

ビジネス ケースと合理化されたデジタル資産を実践的な移行計画に変換して、目的のビジネス成果を実現するために必要な技術的アクティビティをガイドします。

## <a name="business-planning-skills"></a>ビジネス計画のスキル

準備フェーズでは、技術スタッフは、クラウドに移行されたワークロードをホスト、運用、管理できる移行ランディング ゾーンを作成します。 これらのラーニング パスは、必要なスキルの開発に役立ちます。

- [Azure アカウントの作成](/learn/modules/create-an-azure-account)。 Azure を使用する最初の手順では、アカウントを作成します。 お使いのアカウントは、自身がプロビジョニングした Azure サービスを保持し、ID、課金、ユーザー設定などの個人設定を処理します。
- [Azure Portal](/learn/modules/tour-azure-portal) Azure portal の機能とサービスのツアーを実行し、ポータルをカスタマイズします。
- [Azure 入門](/learn/modules/welcome-to-azure)。 Azure では最初に、クラウドの最初の仮想マシンを作成し構成します。
- [Azure のセキュリティの概要](/learn/modules/intro-to-security-in-azure)。 クラウドで作業する場合のインフラストラクチャとデータの保護に関する基本的な概念について学習します。 自分にどのような責任があり、Azure が代わりに何を処理するかについて理解します。
- [Azure でのリソースの管理](/learn/paths/manage-resources-in-azure)。 Azure コマンド ラインと Web ポータルを使用してクラウド ベースのリソースを作成、管理、制御する方法について説明します。
- [VM の作成](/learn/modules/create-windows-virtual-machine-in-azure)。 Azure portal を使用して仮想マシンを作成します。
- [Azure のネットワーク](/learn/modules/intro-to-azure-networking)。 Azure のネットワークの基本と、Azure のネットワークが回復性の向上と待ち時間の短縮にどのように役立つかについて学習します。
- [Azure のコンピューティング オプション](/learn/modules/intro-to-azure-compute)。 Azure のコンピューティング サービスについて学習します。
- [RBAC によるリソースのセキュリティ保護](/learn/modules/secure-azure-resources-with-rbac)。 RBAC を使用してリソースをセキュリティで保護します。
- [データ ストレージ オプション](/learn/modules/intro-to-data-in-azure/index)。 Azure データ ストレージの利点について学習します。

## <a name="organizational-skills"></a>組織のスキル

クラウド導入の取り組みの動機と目的のビジネス成果に応じて、リーダーはさまざまな機能を促進するために新しい組織構造または仮想チーム (v チーム) を確立することが必要になる場合もあります。 これらの記事は、望ましい結果を得られるようにこれらのチームを編成するために必要なスキルの開発に役立ちます。

- [初期の組織配置](../organize/index.md)。 特定の目標を達成しやすくするための組織の配置とさまざまなチーム構造の概要です。
- [サイロおよび封土の分析](../organize/fiefdoms-silos.md)。 2 つの一般的な組織のアンチパターンと、生産性の高いコラボレーションにチームを導く方法を理解します。

## <a name="deeper-skills-exploration"></a>より詳細なスキル調査

スキルを伸ばすためのこれらの初期のオプション以外に、さまざまな学習オプションを利用できます。

### <a name="typical-mappings-of-cloud-it-roles"></a>クラウド IT の役割の典型的なマッピング

Microsoft とパートナーは、あらゆるお客様を対象に、Azure サービスに関連したスキルの向上に役立つさまざまなオプションを提供しています。

- [Microsoft Virtual Academy](https://mva.microsoft.com/product-training/microsoft-azure)。 Azure の構築を支援した人々によるトレーニングが提供されています。 基本的な概要から詳細な技術トレーニングまで、これらのコースは、IT 実装者が Azure をビジネスに利用する方法を学習するのに役立ちます。
- [Microsoft IT Pro Cloud Essentials](https://www.microsoft.com/azureessentials)。 クラウド サービス、教育、サポートなどの特典がある無料の年間サブスクリプションが提供されます。 IT Pro Cloud Essentials では、知識を増やしてキャリア アップへの道を切り開くために最も重要な分野で、実際の体験、的を絞った教育機会、および専門家によるサポートを IT 実装担当者に提供します。
- [Microsoft IT Pro Center](https://www.microsoft.com/itpro)。 クラウドのキャリア パスをマッピングするのに役立つ無料のオンライン リソースとして機能します。 業界の専門家がクラウド ロールについて行う提案、および提案を実現するためのスキルについて学習してください。 関係を保つために最も必要なスキルを磨くように、学習カリキュラムをご自分のペースで進められます。

Azure に関する知識を [Microsoft Azure 認定のトレーニングや試験](https://www.microsoft.com/learning/azure-certification.aspx)によって公認のものにすることをお勧めします。

## <a name="microsoft-learn"></a>Microsoft Learn

Microsoft Learn は学習に対する新しいアプローチです。 クラウド導入に伴う新たな責任に対する準備は容易にできるものではありません。 Microsoft Learn では、目標を早く達成するのに役立つ、実践的な学習に対するより価値あるアプローチを提供します。 ポイントとレベルを獲得し、さらなる達成を目指しましょう。

Microsoft Learn のロール固有のラーニング パスのいくつかの例を以下に示します。

[ビジネス ユーザー](/learn/browse/?roles=business-user)は、クラウドベースのテクノロジのプランニング、テスト、および導入に参加することを求められたときに、短期間で多くのことを習得できる可能性があります。 クラウド モデルおよびツールの導入に重点を置いた Microsoft Learn のモジュールは、これらのユーザーがクラウドベースのサービスを通じてビジネスを管理できるよう支援します。

[ソリューション アーキテクト](/learn/browse/?roles=solution-architect)は、何百ものモジュールとラーニング パスと対話できます。 利用できるトピックの範囲は、コア インストラクチャ サービスから高度なデータ変換に及びます。

[管理者](/learn/browse/?roles=administrator)は、Azure の基礎やコンテナーの構成、さらにはクラウドでの高度な管理に重点を置いたモジュールを利用できます。

[開発者](/learn/browse/?roles=developer&term=infrastructure)は、クラウドへの資産の移行において、重要な役割を果たします。 開発者は、アーキテクチャ、ガバナンス、最新化アクティビティ中に役立つ方法について学習できます。

追加のラーニング パスについては、[Learn のカタログ](/learn/browse)を参照してください。 [ロール] フィルターを使用して、ラーニング パスを自分のロールに合わせてください。