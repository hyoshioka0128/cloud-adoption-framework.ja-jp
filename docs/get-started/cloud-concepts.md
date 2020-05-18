---
title: 作業を開始しましょう。基本的なアラインメントに関する決定事項の文書化
description: クラウド導入の取り組みを推進するために必要な最初の決定事項を理解し、文書化します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 04/04/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: overview
ms.openlocfilehash: 0c59a383f47629dd16cd81bc6865812fcaca4679
ms.sourcegitcommit: 60d8b863d431b5d7c005f2f14488620b6c4c49be
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2020
ms.locfileid: "83230587"
---
<!-- docsTest:ignore CAF%20Readiness%20Naming%20and%20Tagging%20tracking%20template -->

# <a name="get-started-understand-and-document-foundational-alignment-decisions"></a>作業を開始しましょう。基本的なアラインメントに関する決定事項を理解し、文書化する

クラウド導入の取り組みにより、さまざまな体験を通じて、ビジネス、技術、組織に関する多数のメリットを最大限に引き出すことができます。 何を達成しようとしているかにかかわらず、取り組みにクラウドが関与する場合は、関係するすべてのチームが理解する必要がある最初の決定事項がいくつかあります。 このガイドに従って作業する際には、[最初の意思決定テンプレート](https://raw.githubusercontent.com/microsoft/cloudadoptionframework/master/references/initial-decisions-checklist.docx)を使用してそれらの決定事項を記録し、クラウド導入ライフサイクル全体に参加するチーム メンバーを迅速にオンボードすることができます。

> [!NOTE]
> 次のいずれかの手順をクリックすると、クラウド導入フレームワークの目次が表示されます。これは、後でチームが関連するガイダンスを実装する際に使用される基本概念を説明するためのものです。 このチェックリストに頻繁に戻るには、このページにブックマークします。

## <a name="step-1-understand-how-azure-works"></a>手順 1:Azure のしくみを理解する

クラウド導入の取り組みをサポートするクラウド プロバイダーとして Azure を選択した場合は、[Azure のしくみ](./what-is-azure.md)について理解しておくことが重要です。

**関与するチーム、成果物、補助的なガイダンス:**

クラウド導入ライフサイクルに関与するすべての個人に、Azure のしくみについての基礎知識が必要です。

## <a name="step-2-understand-initial-azure-concepts"></a>手順 2:Azure の初期概念を理解する

Azure は、Azure の実装に特に関連する技術戦略について詳細に話し合うために必要な、一連の[基本概念](../ready/considerations/fundamental-concepts.md)に基づいて構築されています。

**関与するチーム、成果物、補助的なガイダンス:**

技術戦略の Azure 実装にかかわるすべての個人は、基本概念の用語と定義を理解している必要があります。

## <a name="step-3-review-the-portfolio"></a>手順 3:ポートフォリオを確認する

選択したクラウド プロバイダーに関係なく、クラウド ホスティングと環境に関するすべての意思決定は、まずワークロードのポートフォリオ全体を把握することから始まります。 クラウド導入フレームワークには、ポートフォリオを理解して評価するためのいくつかのツールが含まれています。

**成果物:**

- [最初の意思決定テンプレート](https://raw.githubusercontent.com/microsoft/cloudadoptionframework/master/references/initial-decisions-checklist.docx)で、ポートフォリオ ドキュメントの場所、状態、責任者を記録します。

**成果物の完成をサポートするためのガイダンス:**

- [基本概念](../ready/considerations/fundamental-concepts.md)は、クラウドの導入を開始する前に、Azure の主要なトピックについて理解するのに役立ちます。
- [運用管理ブック](https://raw.githubusercontent.com/microsoft/cloudadoptionframework/master/manage/opsmanagementworkbook.xlsx)とビジネス アラインメント アプローチは、クラウド運用チームに移行されたワークロードと資産を理解するのに役立ちます。
- [クラウド導入計画](../plan/plan-intro.md)では、クラウドに導入予定のワークロードと資産のバックログが提供されます。
- [デジタル資産分析](../digital-estate/approach.md)は、クラウドに導入予定の既存のワークロードと資産を文書化するためのアプローチです。 Azure では、デジタル資産は [Azure Migrate](https://docs.microsoft.com/azure/migrate/migrate-support-matrix) と呼ばれるツールで最も適切に表現されます。

<!-- markdownlint-disable MD033 -->
<br>

| 説明責任チーム | 実行責任チームとサポート チーム |
| --- | --- |
| <li> クラウド戦略チームは、ポートフォリオを表示するビューを定義する責任を負います。 | <li> 複数のチームが、次のガイダンスを使用してそれらのビューを作成します。 クラウド導入に関与するすべての個人は、プロセスの後半で決定をサポートするために、ポートフォリオ ビューがどこで見つかるかを把握しておく必要があります。 |

## <a name="step-4-define-portfolio-hierarchy-depth-to-align-the-portfolio"></a>手順 4:ポートフォリオを調整するためのポートフォリオ階層の深さを定義する

クラウドでの資産とワークロードのホスティングは、1 つのワークロードとそのサポート資産で構成される単純なものである場合もあります。 その他のお客様の場合、クラウド導入戦略には、何千ものワークロードや、その他多数のサポート資産が含まれることがあります。 ポートフォリオ階層は、レイヤー化された階層レベルごとに共通名の概要を示し、クラウド プロバイダーに関係なく組織の共通言語を作成するのに役立ちます。

**成果物:**

- [最初の意思決定テンプレート](https://raw.githubusercontent.com/microsoft/cloudadoptionframework/master/references/initial-decisions-checklist.docx)に、関連する階層のニーズを記録します。

**成果物の完成をサポートするためのガイダンス:**

- 基本的な用語を揃えるために[ポートフォリオ階層](../reference/fundamental-concepts/hosting-hierarchy.md)のレベルについて理解します。

<!-- markdownlint-disable MD033 -->
<br>

| 説明責任チーム | 実行責任チームとサポート チーム |
| --- | --- |
| <li> クラウド ガバナンス チームは、クラウドでの企業のポリシーを形成するために、ポートフォリオ階層を定義、適用、自動化する責任を負います。 | <li> クラウド導入の技術戦略に関与するすべての個人は、ポートフォリオ階層と、現在使用されている階層のレベルについて理解している必要があります。 |

## <a name="step-5-establish-a-naming-and-tagging-standard-across-the-portfolio"></a>手順 5:ポートフォリオ全体で名前付けとタグ付けの標準を確立する

既存のすべてのワークロードと資産には、名前付けとタグ付けの標準に従って適切な名前を付け、タグを付ける必要があります。 それらの標準を文書化し、すべてのチーム メンバーが参照として利用できるようにする必要があります。 可能であれば、タグ付けの要件を最小限にするために、標準を自動的に適用する必要もあります。

**成果物:**

- [最初の意思決定テンプレート](https://raw.githubusercontent.com/microsoft/cloudadoptionframework/master/references/initial-decisions-checklist.docx)に、名前付けとタグ付けの標準ブックの場所、状態、責任者を記録します。

**成果物の完成をサポートするためのガイダンス:**

- [名前付けとタグ付けの標準](../ready/azure-best-practices/naming-and-tagging.md)を作成します。
- 決定事項を追跡するために、[名前付けおよびタグ付け規則の追跡ブック](https://archcenter.blob.core.windows.net/cdn/fusion/readiness/CAF%20Readiness%20Naming%20and%20Tagging%20tracking%20template.xlsx)に入力します。
- [Azure の既存のタグを確認して更新します](https://docs.microsoft.com/azure/azure-resource-manager/management/tag-resources)。
- [Azure にタグ付けポリシーを適用します](https://docs.microsoft.com/azure/azure-resource-manager/management/tag-policies)。

<!-- markdownlint-disable MD033 -->
<br>

| 説明責任チーム | 実行責任チームとサポート チーム |
| --- | --- |
| <li> クラウド ガバナンス チームは、ポートフォリオ全体の一貫性を確保するために、名前付けとタグ付けの標準を定義、適用、自動化する責任を負います。 | <li> クラウド導入の技術戦略に関与するすべての個人は、クラウドへのデプロイの前に、名前付けとタグ付けの標準について理解しておく必要があります。 |

## <a name="step-6-create-a-resource-organization-design-to-implement-the-portfolio-hierarchy"></a>手順 6:ポートフォリオ階層を実装するためのリソース組織の設計を作成する

ポートフォリオ階層の決定事項との一貫性を確保するには、リソース組織の設計を作成することが重要です。 このような設計によって、クラウド プロバイダーの組織用ツールを、クラウド導入計画のサポートに必要なポートフォリオ階層に合ったものにすることができるでしょう。 この設計は、クラウド環境内の特定の境界にデプロイできる資産を明確にすることで、実装のガイドとなります。

**成果物:**

- [最初の意思決定テンプレート](https://raw.githubusercontent.com/microsoft/cloudadoptionframework/master/references/initial-decisions-checklist.docx)で、Azure 製品をポートフォリオ階層の対応するレベルにマップします。

**成果物の完成をサポートするためのガイダンス:**

- [Azure 製品でポートフォリオ階層がどのようにサポートされるか](../reference/fundamental-concepts/hierarchy-azure-tools.md)を理解します。
- 選択したポートフォリオ階層に合わせるための既存のサブスクリプションを確認します。

サブスクリプション戦略の構築:

- [仕様では 2 つのサブスクリプション](../ready/azure-best-practices/initial-subscriptions.md)から開始します。
  - 共有サービスやサンドボックス サブスクリプションなどの一般的なエンタープライズ ニーズに対応するために、基本サブスクリプションの設計を追加します。
- クラウド導入計画をサポートするために追加のサブスクリプションが必要な場合は、[複数のサブスクリプションを管理](../ready/azure-best-practices/organize-subscriptions.md)します。
- [ポートフォリオ階層に基づいて明確な境界](../reference/fundamental-concepts/hierarchy-azure-tools.md#organizing-the-hierarchy-in-azure)を確立します。
- 組織の戦略に準拠するように、必要に応じて[サブスクリプション間でリソース グループと資産を移動](https://docs.microsoft.com/azure/azure-resource-manager/management/move-resource-group-and-subscription)します。

<!-- markdownlint-disable MD033 -->
<br>

| 説明責任チーム | 実行責任チームとサポート チーム |
| --- | --- |
| <li> クラウド ガバナンス チームは、ポートフォリオ全体にわたってリソース組織の設計を定義、実装、自動化する責任を負います。 | <li> クラウド導入の技術戦略に関与するすべての個人は、クラウドへのデプロイの前に、リソース組織の設計について理解している必要があります。 |

## <a name="step-7-map-capabilities-teams-and-raci-to-fundamental-concepts"></a>手順 7:機能、チーム、RACI を基本概念にマップする

ポートフォリオ階層の複雑さは、さまざまなチームの日常業務のガイドとなるように、組織の構造や手法に情報を提供するのに役立ちます。

**成果物:**

- これらの基本概念に基づいて、組織の足並みを揃えるためのファースト ステップ ガイドを完了します。

<!-- docsTest:ignore Get Align -->

**成果物の完成をサポートするためのガイダンス:**

- これまでの手順をガイドとして使用して、[ポートフォリオ階層のアカウンタビリティ ガイダンス](../reference/fundamental-concepts/hosting-hierarchy.md#hierarchy-accountability-and-guidance)を評価します。これにより、専用の組織または仮想チームで提供する必要のある機能はどれかを判断できます。
- 「[作業を開始しましょう: 組織の足並みを揃える](./org-alignment.md)」を使用して、ポートフォリオ階層のアカウンタビリティ ガイダンスを RACI 図に適用します。

<!-- markdownlint-disable MD033 -->
<br>

| 説明責任チーム | 実行責任チームとサポート チーム |
| --- | --- |
| <li> クラウド戦略チームは、クラウド導入ライフサイクル全体を確実に成功させるために、仮想または専用の組織構造を調整する責任を負います。 | <li> クラウド導入ライフサイクルに関与するすべての個人は、人員の配置とアカウンタビリティのレベルを理解している必要があります。 |

## <a name="whats-next"></a>参照トピック

これらの基本概念を基に、クラウド導入フレームワークのこのセクションにある一連のファースト ステップ ガイドを使用して構築します。

> [!div class="nextstepaction"]
> [他のファースト ステップ ガイドに基本概念を適用する](./index.md)
