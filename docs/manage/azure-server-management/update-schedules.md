---
title: 更新スケジュールの作成
titleSuffix: Microsoft Cloud Adoption Framework for Azure
description: 更新スケジュールの作成
author: BrianBlanchard
ms.author: brblanch
ms.date: 05/10/2019
ms.topic: article
ms.service: cloud-adoption-framework
ms.subservice: operate
ms.openlocfilehash: 9e6e078859bb580794477328099b66d14009bdca
ms.sourcegitcommit: d19e026d119fbe221a78b10225230da8b9666fe1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2019
ms.locfileid: "71221394"
---
# <a name="create-update-schedules"></a>更新スケジュールの作成

Azure portal または新しい PowerShell コマンドレット モジュールを使用して、更新スケジュールを管理できます。

Azure portal を使用して更新スケジュールを作成するには、「[更新プログラムのデプロイをスケジュールする](https://docs.microsoft.com/azure/automation/automation-tutorial-update-management#schedule-an-update-deployment)」をご覧ください。

Azure PowerShell を使用した更新管理の構成が Az.Automation モジュールで新たにサポートされました。 [バージョン 1.7.0](https://www.powershellgallery.com/packages/Az/1.7.0) のモジュールでは、[New-AzAutomationUpdateManagementAzureQuery](/powershell/module/az.automation/new-azautomationupdatemanagementazurequery?view=azps-1.7.0) コマンドレットのサポートが追加されているので、タグ、場所、保存された検索条件を使用して、対象となるマシンを柔軟にグループ化し、更新スケジュールを構成できます。

## <a name="example-script"></a>サンプル スクリプト

以下のサンプル スクリプトは、更新スケジュールを適用できるマシンの動的グループを作成するためのタグ付けとクエリの使用方法を示しています。 実行されるアクションは次のとおりです。 その具体的なアクションの実装を、独自のスクリプトを作成する際の参考としてください。

- 毎週土曜日の午前 8 時に実行する Azure Automation 更新スケジュールを作成します。
- 次の条件に一致するマシンのクエリを作成します。
  - `westus`、`eastus`、`eastus2` のいずれかの Azure の場所にデプロイされている
  - `Owner` タグが適用され、値が `JaneSmith` に設定されている
  - `Production` タグが適用され、値が `true` に設定されている
- クエリ対象のマシンに更新スケジュールを適用し、2 時間の更新ウィンドウを設定します。

サンプル スクリプトを実行する前に、[Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.1.0) コマンドレットを使用してサインインする必要があります。 スクリプトの起動時に、次の情報を指定する必要があります。

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
        [string]$SubscriptionId,

        [Parameter(Mandatory=$true)]
        [string]$ResourceGroupName,

        [Parameter(Mandatory=$true)]
        [string]$WorkspaceName,

        [Parameter(Mandatory=$true)]
        [string]$AutomationAccountName,

        [Parameter(Mandatory=$false)]
        [string]$scheduleName = "SaturdayCritialSecurity"
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
    $ownerTag = @{"Owner"= @("JaneSmith")}
    $ownerTag.add("Production", "true")

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

## Next steps

See examples of how to implement [common policies in Azure](./common-policies.md) that can help manage your servers.

> [!div class="nextstepaction"]
> [Common policies in Azure](./common-policies.md)
