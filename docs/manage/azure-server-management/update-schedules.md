---
title: 更新スケジュールの作成
description: Azure portal または新しい PowerShell コマンドレット モジュールで更新スケジュールを管理する方法を、Azure 向けのクラウド導入フレームワークを使用して学習します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 05/10/2019
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: operate
ms.openlocfilehash: c418233559d93b59a468caa24fe43984184898ec
ms.sourcegitcommit: 011525720bd9e2d9bcf03a76f371c4fc68092c45
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88574943"
---
# <a name="create-update-schedules"></a>更新スケジュールの作成

Azure portal または新しい PowerShell コマンドレット モジュールを使用して、更新スケジュールを管理できます。

Azure portal を使用して更新スケジュールを作成するには、「[更新プログラムのデプロイをスケジュールする](/azure/automation/automation-tutorial-update-management#schedule-an-update-deployment)」をご覧ください。

Azure PowerShell を使用した更新管理の構成が `Az.Automation` モジュールで新たにサポートされました。 モジュールの[バージョン 1.7.0](https://www.powershellgallery.com/packages/Az/1.7.0) では、[New-AzAutomationUpdateManagementAzureQuery](/powershell/module/az.automation/new-azautomationupdatemanagementazurequery?view=azps-1.7.0) コマンドレットのサポートが追加されました。 このコマンドレットを実行すると、タグ、場所、保存された検索を使用して、柔軟なグループのマシンの更新スケジュールを構成できます。

## <a name="example-script"></a>サンプル スクリプト

このセクションのサンプル スクリプトは、更新スケジュールを適用できるマシンの動的グループを作成するためのタグ付けとクエリの使用方法を示しています。 実行されるアクションは次のとおりです。 その具体的なアクションの実装を、独自のスクリプトを作成する際の参考としてください。

- 毎週土曜日の午前 8:00 に実行する Azure Automation 更新スケジュールを作成します。
- 次の条件に一致するマシンのクエリを作成します。
  - `westus`、`eastus`、`eastus2` のいずれかの Azure の場所にデプロイされている。
  - `Owner` タグが適用され、値が `JaneSmith` に設定されている。
  - `Production` タグが適用され、値が `true` に設定されている。
- クエリ対象のマシンに更新スケジュールを適用し、2 時間の更新ウィンドウを設定します。

サンプル スクリプトを実行する前に、[Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-2.1.0) コマンドレットを使用してサインインする必要があります。 スクリプトを開始するときに、次の情報を指定します。

- ターゲット サブスクリプション ID
- ターゲット リソース グループ
- Log Analytics ワークスペース名
- Azure Automation アカウント名

```powershell

    <#
        .SYNOPSIS
            This script orchestrates the deployment of the solutions and the agents.
        .Parameter SubscriptionName
        .Parameter WorkspaceName
        .Parameter AutomationAccountName
        .Parameter ResourceGroupName

    #>

    param (
        [Parameter(Mandatory=$true)]
        [string] $SubscriptionId,

        [Parameter(Mandatory=$true)]
        [string] $ResourceGroupName,

        [Parameter(Mandatory=$true)]
        [string] $WorkspaceName,

        [Parameter(Mandatory=$true)]
        [string] $AutomationAccountName,

        [Parameter(Mandatory=$false)]
        [string] $scheduleName = "SaturdayCriticalSecurity"
    )

    Import-Module Az.Automation

    $startTime = ([DateTime]::Now).AddMinutes(10)
    $schedule = New-AzAutomationSchedule -ResourceGroupName $ResourceGroupName `
        -AutomationAccountName $AutomationAccountName `
        -StartTime $startTime `
        -Name $scheduleName `
        -Description "Saturday patches" `
        -DaysOfWeek Saturday `
        -WeekInterval 1 `
        -ForUpdateConfiguration

    # Using AzAutomationUpdateManagementAzureQuery to create dynamic groups.

    $queryScope = @("/subscriptions/$SubscriptionID/resourceGroups/")

    $query1Location =@("westus", "eastus", "eastus2")
    $query1FilterOperator = "Any"
    $ownerTag = @{ "Owner"= @("JaneSmith") }
    $ownerTag.Add("Production", "true")

    $DGQuery = New-AzAutomationUpdateManagementAzureQuery -ResourceGroupName $ResourceGroupName `
        -AutomationAccountName $AutomationAccountName `
        -Scope $queryScope `
        -Tag $ownerTag

    $AzureQueries = @($DGQuery)

    $UpdateConfig = New-AzAutomationSoftwareUpdateConfiguration -ResourceGroupName $ResourceGroupName `
        -AutomationAccountName $AutomationAccountName `
        -Schedule $schedule `
        -Windows `
        -Duration (New-TimeSpan -Hours 2) `
        -AzureQuery $AzureQueries `
        -IncludedUpdateClassification Security,Critical
```

## <a name="next-steps"></a>次のステップ

サーバーの管理に役立つ [Azure の共通ポリシー](./common-policies.md)を実装する方法の例をご覧ください。

> [!div class="nextstepaction"]
> [Azure の共通ポリシー](./common-policies.md)
