---
title: Azure DevOps へのクラウド導入計画のデプロイ
description: テンプレートを使用してクラウド導入の取り組みを標準プロセスに適合させ、Azure DevOps にすばやくバックログをデプロイする方法について説明します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 07/01/2019
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: plan
ms.custom: internal
ms.openlocfilehash: 98d10cf0b27381bf24c0283843df0a7cc3907980
ms.sourcegitcommit: b6f2b4f8db6c3b1157299ece1f044cff56895919
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2020
ms.locfileid: "97024451"
---
# <a name="cloud-adoption-plan-and-azure-devops"></a>クラウド導入計画と Azure DevOps

Azure DevOps は、反復的なプロジェクトを管理する Azure のお客様向けの、クラウドベースのツールのセットです。 これにはデプロイ パイプラインと DevOps の他の重要な側面を管理するためのツールも含まれています。

この記事では、テンプレートを使用して Azure DevOps にバックログをすばやくデプロイする方法について説明します。 このテンプレートは、クラウド導入フレームワークのガイダンスに基づき、クラウド導入作業を標準化されたプロセスに合わせます。

## <a name="create-your-cloud-adoption-plan"></a>クラウド導入計画を作成する

クラウド導入計画をデプロイするには、[Azure DevOps Demo Generator](https://aka.ms/adopt/plan/generator) を開きます。 このツールにより、Azure DevOps テナントにこのテンプレートがデプロイされます。 ツールを使用するには、次の手順に従う必要があります。

1. **[選択したテンプレート]** フィールドが **[Cloud Adoption Plan]\(クラウド導入計画\)** に設定されていることを確認します。 そうでない場合は、 **[テンプレートの選択]** を選択して適切なテンプレートを選択します。
2. **[組織の選択]** ドロップダウン リスト ボックスから、対象の Azure DevOps 組織を選択します。
3. 新しいプロジェクトの名前を入力します。 このクラウド導入計画は、対象の Azure DevOps テナントにデプロイされると、この名前が設定されます。
4. **[プロジェクトの作成]** を選択して、戦略と計画のテンプレートに基づき、新しいプロジェクトをお使いのテナント内に作成します。 進行状況バーに、プロジェクトをデプロイするまでの進捗が示されます。
5. デプロイが完了したら、 **[プロジェクトに移動]** を選択して新しいプロジェクトを表示します。

プロジェクトの作成が完了したら、この一連の記事を読み進めて、実際のクラウド導入計画に合わせてこのテンプレートを変更する方法を確認してください。

このツールに関するその他のサポートとガイダンスについては、[Azure DevOps Services Demo Generator](/azure/devops/demo-gen) に関する記事をご覧ください。

## <a name="bulk-edit-the-cloud-adoption-plan"></a>クラウド導入計画を一括編集する

計画プロジェクトがデプロイされたら、Microsoft Excel を使用してそれを変更できます。 Microsoft Excel を使用すると、Azure DevOps ブラウザー エクスペリエンスを使用するよりもはるかに簡単に計画内に新しいワークロードまたは資産を作成できます。

一括編集を行うためにワークステーションを準備するには、[Microsoft Excel を使用した作業項目の一括追加または変更](/azure/devops/boards/backlogs/office/bulk-add-modify-work-items-excel?view=azure-devops)に関する記事をご覧ください。

ユーザーは Microsoft Project を使用して、タスクの追跡、バックログの作成、リソースの割り当てを行うこともできます。 [Microsoft Project を Azure DevOps に接続する](/azure/devops/boards/backlogs/office/create-your-backlog-tasks-using-project?view=tfs-2018)手順を参照してください。

## <a name="use-the-cloud-adoption-plan"></a>クラウド導入計画を使用する

クラウド導入計画では、アクティビティがアクティビティの種類ごとに整理されています。

- **エピック:** "_エピック_" は、クラウド導入ライフサイクルの全体的なフェーズを表します。
- **機能:** 機能は、各フェーズ内での具体的な目標の整理に使用されます。 たとえば、特定のワークロードの移行は 1 つの機能になります。
- **ユーザー ストーリー:** ユーザー ストーリーは、特定の目標に基づくアクティビティの論理的なコレクションに作業をグループ化します。
- **タスク:** タスクは、実際に実行する必要がある作業です。

各レイヤーでは、依存関係に基づいてアクティビティが順序付けされます。 アクティビティは、目下の目標またはタスクを明確にするために、クラウド導入フレームワーク内の記事にリンクされています。

クラウド導入計画の最もわかりやすいビューは、**エピック** バックログ ビューです。 **エピック** バックログ ビューへの変更については、[バックログの表示](/azure/devops/boards/backlogs/define-features-epics?view=azure-devops#view-a-backlog-or-portfolio-backlog)に関する記事を参照してください。 このビューからは簡単に、導入ライフサイクルの現在のフェーズを完了するために必要な作業を計画および管理できます。

> [!NOTE]
> クラウド導入計画の現在の状態は、移行作業に重点を置いています。 ガバナンス、イノベーション、または操作に関連したタスクは、手動で設定する必要があります。

## <a name="align-the-cloud-adoption-plan"></a>クラウド導入計画を調整する

戦略の手法と計画の手法の概要ページは、それぞれ[戦略と計画のテンプレート](https://raw.githubusercontent.com/microsoft/CloudAdoptionFramework/master/plan/cloud-adoption-framework-strategy-and-plan-template.docx)を参照しています。 このテンプレートでは、クラウド導入計画のテンプレートを導入用の特定の計画に合わせて調整するための決定事項およびデータ ポイントが整理されています。 新しいプロジェクトを配置する前に、[戦略の手法](../strategy/index.md)と[計画の手法](../plan/index.md)の演習を完了することを検討してください。

次の記事では、クラウド導入計画の調整がサポートされています。

- [ワークロード](./workloads.md):移行または最新化の対象となる各ワークロードを取り込むために、クラウド移行エピック内の機能を調整します。 これらの機能を追加して変更することで、上位 10 個のワークロードを移行する作業を取り込みます。
- [資産](./assets.md):各資産 (仮想マシン、アプリケーション、またはデータ) は、各ワークロードの下のユーザー ストーリーで表現されます。 これらのユーザー ストーリーを追加し、デジタル資産に合うように変更します。
- [合理化](./review-rationalization.md):各ワークロードが定義されると、そのワークロードに関する初期の想定に疑問が生じる場合があります。 この結果、各資産のタスクが変更されることがあります。
- [リリース計画を作成する](./iteration-paths.md):イテレーション パスは、作業を各種リリースやイテレーションに合わせることでリリース計画を確立します。
- [タイムラインを確立する](./timelines.md):各イテレーションの開始日と終了日を定義すると、プロジェクト全体を管理するタイムラインが作成されます。

これらの 5 つの記事は、実際の導入作業の管理を開始するために必要な調整タスクそれぞれに役立ちます。 次の手順では、調整の演習を開始します。

## <a name="next-steps"></a>次のステップ

[ワークロードの定義および優先順位付け](./workloads.md)で計画プロジェクトの調整を開始します。

> [!div class="nextstepaction"]
> [ワークロードを定義し、優先順位を付ける](./workloads.md)
