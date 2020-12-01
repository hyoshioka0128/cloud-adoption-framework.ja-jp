---
title: Azure における運用のコンプライアンス
description: 運用のコンプライアンスを通じて、機能停止や脆弱性の可能性を小さくすることにより、ビジネスの安定性を確保する方法を説明します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 10/17/2019
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: operate
ms.custom: fasttrack-edit, AQC
ms.localizationpriority: high
ms.openlocfilehash: 2ea6f481fbbd8cea4f6ebc1c9c930a34eb840c04
ms.sourcegitcommit: 412b945b3492ff3667c74627524dad354f3a9b85
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/18/2020
ms.locfileid: "94880706"
---
<!-- docutune:casing "Update Management" "Guest Configuration" "Blueprints: Getting started" "Blueprints: Blueprint definitions" MMA -->
<!-- cSpell:ignore WSUS -->

# <a name="operational-compliance-in-azure"></a>Azure における運用のコンプライアンス

"_運用のコンプライアンス_" は、すべてのクラウド管理ベースラインにおける 2 つ目の規範です。

![クラウド管理ベースライン](../../_images/manage/management-baseline.png)

運用のコンプライアンスを強化することで、構成のずれに関連した機能停止や、システムのパッチが不適切に適用されたことに関連した脆弱性の可能性を小さくすることができます。

すべてのエンタープライズグレード環境について、管理ベースラインの推奨最小値の概要を次の表に示します。

| Process | ツール | 目的 |
|---|---|---|
| 更新プログラムの管理 | Azure Automation の Update Management | 更新プログラムの管理とスケジューリング |
| ポリシーの適用 | Azure Policy | 環境とゲストのコンプライアンスを確保するためのポリシーの適用 |
| 環境の構成 | Azure Blueprint | コア サービスの自動化されたコンプライアンス |
| リソース構成 | 必要な状態の構成 | ゲスト OS と環境のいくつかの側面の自動構成 |

::: zone target="docs"

## <a name="update-management"></a>更新管理

::: zone-end
::: zone target="chromeless"

## <a name="update-management"></a>[更新管理](#tab/UpdateManagement)

::: zone-end

Azure Automation の Update Management ソリューションによって管理されるコンピューターでは、次の構成を使用して評価と更新プログラムのデプロイが実行されます。

- Windows または Linux 用の Microsoft Monitoring Agent (MMA)。
- PowerShell Desired State Configuration (DSC) (Linux の場合)。
- Azure Automation の Hybrid Runbook Worker。
- Microsoft Update または Windows Server Update Services (WSUS) (Windows コンピューターの場合)。

詳細については、[Azure Automation の Update Management ソリューション](/azure/automation/update-management/overview)に関する記事をご覧ください。

> [!WARNING]
> Update Management を使用するには、あらかじめ仮想マシンまたはサブスクリプション全体を Log Analytics および Azure Automation にオンボードしておく必要があります。
>
> オンボードには、次の 2 つの方法があります。
>
> - [単一の VM](../../manage/azure-server-management/onboard-single-vm.md)
> - [サブスクリプション全体](../../manage/azure-server-management/onboard-at-scale.md)
>
> Update Management に進む前に、いずれかに従う必要があります。

### <a name="manage-updates"></a>更新プログラムの管理

リソース グループにポリシーを適用するには:

1. [[Azure Automation]](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Automation%2FAutomationAccounts) に移動します。
1. **[Automation アカウント]** を選択し、一覧表示されているアカウントの 1 つを選択します。
1. **[構成管理]** に移動します。
1. **[インベントリ]** 、 **[変更管理]** 、 **[State Configuration]** を使用して、マネージド VM の状態と運用のコンプライアンスを制御することができます。

::: zone target="chromeless"

<!-- markdownlint-disable DOCSMD001 -->

::: form action="OpenBlade[#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Automation%2FAutomationAccounts]" submitText="Assign Policy" :::

<!-- markdownlint-enable DOCSMD001 -->

::: zone-end

::: zone target="docs"

## <a name="azure-policy"></a>Azure Policy

::: zone-end
::: zone target="chromeless"

## <a name="azure-policy"></a>[Azure Policy](#tab/AzurePolicy)

::: zone-end

Azure Policy は、ガバナンス プロセス全体で使用されます。 クラウド管理プロセス内でも大いに役立ちます。 Azure Policy では、Azure リソースを監査および修復でき、マシン内の設定を監査することもできます。 検証は、クライアントとゲスト構成拡張機能によって実行されます。 クライアントを介した拡張機能によって、次のような設定が検証されます。

- オペレーティング システムの構成。
- アプリケーションの構成または存在。
- 環境設定。

Azure Policy ゲスト構成では、現在、マシン内の設定の監査のみが行われます。 構成は適用されません。

::: zone target="chromeless"

### <a name="action"></a>アクション

組み込みのポリシーを管理グループ、サブスクリプション、またはリソース グループに割り当てます。

::: form action="OpenBlade[#blade/Microsoft_Azure_Policy/PolicyMenuBlade/GettingStarted]" submitText="Assign Policy" :::

::: zone-end

::: zone target="docs"

### <a name="apply-a-policy"></a>ポリシーを適用する

リソース グループにポリシーを適用するには:

1. [Azure Policy](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/GettingStarted) に移動します。
1. **[ポリシーを割り当てる]** を選択します。

### <a name="learn-more"></a>詳細情報

詳細については、次を参照してください。

- [Azure Policy](/azure/azure-policy)
- [Azure Policy: ゲスト構成](/azure/governance/policy/concepts/guest-configuration)
- [クラウド導入フレームワーク:ポリシー適用の意思決定ガイド](../../decision-guides/policy-enforcement/index.md)

## <a name="azure-blueprints"></a>Azure Blueprint

::: zone-end
::: zone target="chromeless"

## <a name="azure-blueprints"></a>[Azure Blueprint](#tab/AzureBlueprints)

::: zone-end

Azure Blueprints を使用すると、クラウド アーキテクトや中央の情報テクノロジ グループは、反復可能な一連の Azure リソースを定義できます。 これらのリソースによって、組織の標準、パターン、および要件が実装され、遵守されます。

Azure Blueprints を使用すると、開発チームは新しい環境を迅速に構築して立ち上げることができます。 チームは、組織のコンプライアンスに従って構築しているという確信を得ることができます。 そのためには、ネットワークなどの一連の組み込みコンポーネントを使用して、開発とデリバリーを高速化します。

ブループリントは、さまざまなリソース テンプレートや以下のようなその他のアーティファクトのデプロイを宣言によって調整する手法です。

- ロールの割り当て。
- ポリシーの割り当て。
- Azure Resource Manager のテンプレート。
- リソース グループ。

クラウド ガバナンス チームがまだこの適用を行っていない場合は、ブループリントを適用することによって、環境における運用のコンプライアンスを徹底することができます。

### <a name="create-a-blueprint"></a>ブループリントを作成する

ブループリントを作成するには:

::: zone target="chromeless"

1. **[Blueprints:Getting started] (ブループリント: 作業の開始)** に移動します。
1. **[ブループリントの作成]** ペインで、 **[作成]** を選択します。
1. ブループリントの一覧をフィルター処理して、適切なブループリントを選択します。
1. **ブループリントの名前** ボックスに、ブループリントの名前を入力します。
1. **[定義の場所]** を選択し、適切な場所を選択します。
1. **[次へ :アーティファクト]** を選択し、ブループリントに含まれるアーティファクトを確認します。
1. **[下書きの保存]** を選択します。

::: form action="OpenBlade[#blade/Microsoft_Azure_Policy/BlueprintsMenuBlade/GetStarted]" submitText="Create a blueprint" :::

::: zone-end

::: zone target="docs"

1. [[Blueprints:Getting started] (ブループリント: 作業の開始)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/BlueprintsMenuBlade/GetStarted) に移動します。
1. **[ブループリントの作成]** ペインで、 **[作成]** を選択します。
1. ブループリントの一覧をフィルター処理して、適切なブループリントを選択します。
1. **ブループリントの名前** ボックスに、ブループリントの名前を入力します。
1. **[定義の場所]** を選択し、適切な場所を選択します。
1. **[次へ :アーティファクト]** を選択し、ブループリントに含まれるアーティファクトを確認します。
1. **[下書きの保存]** を選択します。

::: zone-end

### <a name="publish-a-blueprint"></a>ブループリントを発行する

ブループリント アーティファクトをサブスクリプションに対して発行するには、次のようにします。

::: zone target="chromeless"

1. **[ブループリント - ブループリントの定義]** に移動します。
1. 前の手順で作成したブループリントを選択します。
1. ブループリントの定義を確認し、 **[ブループリントを発行する]** を選択します。
1. **[Version]\(バージョン\)** ボックスに「1.0」のようなバージョンを入力します。
1. **[変更に関するメモ]** ボックスに、メモを入力します。
1. **[発行]** を選択します。

::: form action="OpenBlade[#blade/Microsoft_Azure_Policy/BlueprintsMenuBlade/Blueprints]" submitText="Blueprint definitions" :::

::: zone-end

::: zone target="docs"

1. Azure portal で [[ブループリント: ブループリントの定義]](https://portal.azure.com/#blade/Microsoft_Azure_Policy/BlueprintsMenuBlade/Blueprints) に移動します。
1. 前の手順で作成したブループリントを選択します。
1. ブループリントの定義を確認し、 **[ブループリントを発行する]** を選択します。
1. **[Version]\(バージョン\)** ボックスに「1.0」のようなバージョンを入力します。
1. **[変更に関するメモ]** ボックスに、メモを入力します。
1. **[発行]** を選択します。

<!-- markdownlint-disable MD024 -->

### <a name="learn-more"></a>詳細情報

詳細については、次を参照してください。

- [Azure Blueprint](/azure/governance/blueprints)
- [クラウド導入フレームワーク:リソースの整合性の意思決定ガイド](../../decision-guides/resource-consistency/index.md)
- [標準ベースのブループリント サンプル](/azure/governance/blueprints/samples/index#standards-based-blueprint-samples)

::: zone-end
