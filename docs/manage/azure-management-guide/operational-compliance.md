---
title: Azure における運用のコンプライアンス
titleSuffix: Microsoft Cloud Adoption Framework for Azure
description: 運用のコンプライアンスを高めることによってビジネスの安定性を確保します
author: BrianBlanchard
ms.author: brblanch
ms.date: 10/17/2019
ms.topic: article
ms.service: cloud-adoption-framework
ms.subservice: operate
ms.custom: fasttrack-edit, AQC
ms.localizationpriority: high
ms.openlocfilehash: 7073df6b697da49429d4086d9f8f3f113583e52d
ms.sourcegitcommit: 35c162d2d09ec1c4a57d3d57a5db1d56ee883806
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/17/2019
ms.locfileid: "72557093"
---
# <a name="operational-compliance-in-azure"></a>Azure における運用のコンプライアンス

運用のコンプライアンスは、すべてのクラウド管理ベースラインにおける 2 つ目の規範です。

![クラウド管理ベースライン](../../_images/manage/management-baseline.png)

運用のコンプライアンスを強化することで、構成のずれに関連した機能停止や、システムのパッチが適切に適用されていないことに関連した脆弱性の可能性を小さくすることができます。

すべてのエンタープライズグレード環境について、管理ベースラインの推奨最小値の概要を次の表に示します。

|Process  |ツール  |目的  |
|---------|---------|---------|
|更新プログラムの管理|更新管理|更新プログラムの管理とスケジューリング|
|ポリシーの適用|Azure Policy|環境とゲストのコンプライアンスを確保するためのポリシーの適用|
|環境の 構成|Azure Blueprint|コア サービスの自動化されたコンプライアンス|

::: zone target="docs"

## <a name="update-management"></a>更新管理

::: zone-end
::: zone target="chromeless"

## <a name="update-managementtabupdatemanagement"></a>[更新管理](#tab/UpdateManagement)

::: zone-end

Update Management で管理されるコンピューターでは、評価と更新プログラムのデプロイに次の構成を使用します。

- Windows または Linux 用の Microsoft Monitoring Agent (MMA)
- PowerShell Desired State Configuration (DSC) (Linux の場合)
- Automation Hybrid Runbook Worker
- Microsoft Update または Windows Server Update Services (WSUS) (Windows コンピューターの場合)

詳細については、[Update Management ソリューション](https://docs.microsoft.com/azure/automation/automation-update-management)に関するページを参照してください。

> [!WARNING]
> Update Management を使用するには、あらかじめ VM またはサブスクリプション全体を Log Analytics および Azure Automation にオンボードしておく必要があります。
> オンボーディングには、更新の管理を始める前に行うべき 2 つのアプローチがあります。
>
> - [単一の VM](https://docs.microsoft.com/azure/cloud-adoption-framework/manage/azure-server-management/onboard-single-vm)
> - [サブスクリプション全体](https://docs.microsoft.com/azure/cloud-adoption-framework/manage/azure-server-management/onboard-at-scale)

### <a name="manage-updates"></a>更新プログラムの管理

リソース グループにポリシーを適用するには:

1. [[Azure Automation]](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Automation%2FAutomationAccounts) に移動します。
2. 一覧から **Automation アカウント**のいずれかを選択します。
3. ポータルのナビゲーションから **[構成管理]** セクションを探します。
4. インベントリ、変更管理、State Configuration をそれぞれ使用して、マネージド VM の状態と運用のコンプライアンスを制御することができます。

::: zone target="chromeless"

<!-- markdownlint-disable DOCSMD001 -->

::: form action="OpenBlade[#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Automation%2FAutomationAccounts]" submitText="Assign Policy" :::

<!-- markdownlint-enable DOCSMD001 -->

::: zone-end

::: zone target="docs"

## <a name="azure-policy"></a>Azure Policy

::: zone-end
::: zone target="chromeless"

## <a name="azure-policytabazurepolicy"></a>[Azure Policy](#tab/AzurePolicy)

::: zone-end

Azure Policy は、ガバナンス プロセス全体で使用されます。 ただし、クラウド管理プロセス内でも大いに役立ちます。 Azure のリソースを監査し、修復することに加えて、Azure Policy は、マシン内の設定を監査することができます。 検証は、クライアントとゲスト構成拡張機能によって実行されます。 クライアントを介した拡張機能によって、次のような設定が検証されます。

- オペレーティング システムの構成
- アプリケーションの構成または存在
- 環境設定

現時点での Azure Policy ゲスト構成では、マシン内の設定の監査のみが行われます。 構成は適用されません。

::: zone target="chromeless"

### <a name="action"></a>Action

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

- [Azure Policy](https://docs.microsoft.com/azure/azure-policy)
- [Azure Policy - ゲスト構成](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration)
- [クラウド導入フレームワーク:ポリシー適用の意思決定ガイド](../../decision-guides/policy-enforcement/index.md)

## <a name="azure-blueprints"></a>Azure Blueprint

::: zone-end
::: zone target="chromeless"

## <a name="azure-blueprintstabazureblueprints"></a>[Azure Blueprint](#tab/AzureBlueprints)

::: zone-end

Azure Blueprints によってクラウド アーキテクトや中央の情報技術グループは、組織の標準、パターン、要件を実装および順守した反復可能な一連の Azure リソースを定義できます。 Azure Blueprints を使用すると、開発チームは新しい環境を迅速に構築して立ち上げることができます。新しい環境は組織のコンプライアンスに従って構築され、ネットワークなどの一連の組み込みコンポーネントを含んでいるという確信が得られるため、開発とデリバリーにかかる時間を短縮できます。

ブループリントは、さまざまなリソース テンプレートやその他のアーティファクトのデプロイを宣言によって調整する手法です。

- ロールの割り当て。
- ポリシーの割り当て。
- Azure Resource Manager のテンプレート。
- リソース グループ。

クラウド ガバナンス チームがまだブループリントを適用していない場合は、ブループリントを適用することによって、環境における運用のコンプライアンスを徹底することができます。

### <a name="create-a-blueprint"></a>ブループリントを作成する

ブループリントを作成するには:

::: zone target="chromeless"

1. **[Blueprints - Getting started]\(ブループリント - 作業の開始\)** に移動します。
1. **[ブループリントの作成]** セクションで、 **[作成]** を選択します。
1. ブループリントの一覧をフィルター処理して、適切なブループリントを選択します。
1. **ブループリントの名前**を入力し、適切な**定義の場所**を選択します。
1. ページの下部の **[次へ:アーティファクト >>]** をクリックし、ブループリントに含まれるアーティファクトを確認します。
1. **[下書きの保存]** をクリックします。

::: form action="OpenBlade[#blade/Microsoft_Azure_Policy/BlueprintsMenuBlade/GetStarted]" submitText="Create a blueprint" :::

::: zone-end

::: zone target="docs"

1. [[Blueprints - Getting started]\(ブループリント - 作業の開始\)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/BlueprintsMenuBlade/GetStarted) に移動します。
1. **[ブループリントの作成]** セクションで、 **[作成]** を選択します。
1. ブループリントの一覧をフィルター処理して、適切なブループリントを選択します。
1. **ブループリントの名前**を入力し、適切な**定義の場所**を選択します。
1. ページの下部の **[次へ:アーティファクト >>]** をクリックし、ブループリントに含まれるアーティファクトを確認します。
1. **[下書きの保存]** をクリックします。

::: zone-end

### <a name="publish-a-blueprint"></a>ブループリントを発行する

ブループリント アーティファクトをサブスクリプションに対して発行するには、次のようにします。

::: zone target="chromeless"

1. **[ブループリント - ブループリントの定義]** に移動します。
1. 前の手順で作成したブループリントを選択します。
1. ブループリントの定義を確認し、 **[ブループリントを発行する]** を選択します。
1. **[バージョン]** ("1.0" など) と **[変更に関するメモ]** を入力し、 **[発行]** を選択します。

::: form action="OpenBlade[#blade/Microsoft_Azure_Policy/BlueprintsMenuBlade/Blueprints]" submitText="Blueprint definitions" :::

::: zone-end

::: zone target="docs"

1. [[ブループリント - ブループリントの定義]](https://portal.azure.com/#blade/Microsoft_Azure_Policy/BlueprintsMenuBlade/Blueprints) に移動します。
1. 前の手順で作成したブループリントを選択します。
1. ブループリントの定義を確認し、 **[ブループリントを発行する]** を選択します。
1. **[バージョン]** ("1.0" など) と **[変更に関するメモ]** を入力し、 **[発行]** を選択します。

### <a name="learn-more"></a>詳細情報

詳細については、次を参照してください。

- [Azure Blueprint](https://docs.microsoft.com/azure/governance/blueprints)
- [クラウド導入フレームワーク:リソースの整合性の意思決定ガイド](../../decision-guides/resource-consistency/index.md)
- [標準ベースのブループリント サンプル](https://docs.microsoft.com/azure/governance/blueprints/samples/index#standards-based-blueprint-samples)

::: zone-end
