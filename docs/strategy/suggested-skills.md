---
title: 移行の行程の計画フェーズ中のスキル準備パス
description: Azure 向けのクラウド導入フレームワークを使用して、移行の計画フェーズにおけるスキル準備パスについて学習します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 05/19/2019
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: strategy
ms.custom: internal
ms.openlocfilehash: fafe587ad0a264af89d355644382853f54525c97
ms.sourcegitcommit: 042fb295ef5623d45066ce38a389dd8d636cbc20
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/14/2021
ms.locfileid: "100492648"
---
# <a name="skills-readiness-path-during-the-planning-phase-of-a-migration-journey"></a>移行の行程の計画フェーズ中のスキル準備パス

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

- [増分型の合理化](../digital-estate/rationalize.md):遅延バインディングの技術的な決定を適切に調整する合理化に対するアジャイルなアプローチです。
- [合理化の 5 R](../digital-estate/5-rs-of-rationalization.md):合理化のさまざまなオプションについて理解します。

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
- [Azure RBAC によるリソースのセキュリティ保護](/learn/modules/secure-azure-resources-with-rbac)。 Azure RBAC を使用してリソースをセキュリティで保護します。
- [データ ストレージ オプション](/learn/modules/intro-to-data-in-azure)。 Azure データ ストレージの利点について学習します。

## <a name="organizational-skills"></a>組織のスキル

クラウド導入の取り組みの動機と目的のビジネス成果に応じて、リーダーはさまざまな機能を促進するために新しい組織構造または仮想チームを確立することが必要になる場合もあります。 これらの記事は、望ましい結果を得られるようにこれらのチームを編成するために必要なスキルの開発に役立ちます。

- [初期の組織配置](../organize/index.md)。 特定の目標を達成しやすくするための組織の配置とさまざまなチーム構造の概要です。
- [サイロおよび封土の分析](../organize/fiefdoms-silos.md)。 2 つの一般的な組織のアンチパターンと、生産性の高いコラボレーションにチームを導く方法を理解します。

## <a name="deeper-skills-exploration"></a>より詳細なスキル調査

スキルを伸ばすために、これらの初期のオプション以外にさまざまな学習オプションを利用できます。

### <a name="typical-mappings-of-cloud-it-roles"></a>クラウド IT の役割の典型的なマッピング

Microsoft とパートナーは、あらゆるお客様を対象に、Azure サービスに関連したスキルを開発するためのさまざまなオプションを提供しています。

- [ロールとスキルの関連付け](/azure/cloud-adoption-framework/plan/suggested-skills):クラウドのキャリア パスを関連付けるためのリソース。 クラウドでの役割と推奨されるスキルについて説明しています。 ご自分のペースで学習カリキュラムを進めて、価値ある存在であり続けるために最も必要なスキルを磨くことができます。

- Azure に関する公式の知識認定を受けるため、[Azure 認定資格のトレーニングや試験](https://www.microsoft.com/learning/certification-overview.aspx)をご覧ください。

### <a name="microsoft-learn"></a>Microsoft Learn

Microsoft Learn は学習に対する新しいアプローチです。 クラウド導入に伴う新たな責任に対する準備は容易にできるものではありません。 Microsoft Learn では、目標を早く達成するのに役立つ、実践的な学習に対するより価値あるアプローチを提供します。 ポイントとレベルを獲得し、さらなる達成を目指しましょう。

クラウド導入フレームワークの戦略手法に合わせて調整されたラーニング パスの例を次に示します。

[Microsoft Azure のビジネス価値を学習する](/learn/paths/learn-business-value-of-azure):この学習エクスペリエンスでは、デジタル変革とクラウドの機能をビジネスの変革に利用する方法を紹介することから始めます。 信頼できるクラウド プラットフォーム上で、Microsoft Azure Cloud Services によって組織を強化する方法について説明します。 最後に、まとめとして、組織でこのエクスペリエンスを実現する方法を説明します。

## <a name="learn-more"></a>詳細情報

追加のラーニング パスについては、[Microsoft Learn のカタログ](/learn/browse)を参照してください。 **[ロール]** フィルターを使用して、ラーニング パスを自分のロールに合わせてください。
