---
title: オンボードとアラート構成の自動化
titleSuffix: Microsoft Cloud Adoption Framework for Azure
description: オンボードとアラート構成の自動化
author: BrianBlanchard
ms.author: brblanch
ms.date: 05/10/2019
ms.topic: article
ms.service: cloud-adoption-framework
ms.subservice: operate
ms.openlocfilehash: 86997d8c433c23d6ecbe5d3ac124623d318099c0
ms.sourcegitcommit: a26c27ed72ac89198231ec4b11917a20d03bd222
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/06/2019
ms.locfileid: "70838965"
---
# <a name="automate-onboarding"></a>オンボーディングの自動化

Azure サーバー管理サービスのデプロイの効率を向上させるには、このガイダンスの前のセクションで説明されている推奨事項を使用して管理サービスのデプロイを自動化することを検討してください。 以降のセクションで提供されているスクリプトとサンプル テンプレートは、オンボード プロセスの独自の自動化を開発するための出発点です。

## <a name="onboarding-by-using-automation"></a>Automation を使用したオンボード

このガイダンスには、[CloudAdoptionFramework](https://aka.ms/CAF/manage/automation-samples) という、サンプル コードのサポートされている GitHub リポジトリがあります。ここでは、Azure サーバー管理サービスのデプロイの自動化に役立つスクリプト例と Azure Resource Manager テンプレートが提供されています。

これらのサンプル ファイルでは、Azure PowerShell コマンドレットを使用して次のタスクを自動化する方法が示されています。

1. [Log Analytics ワークスペース](/azure/azure-monitor/platform/manage-access)を作成します (または、要件を満たしている場合は既存のワークスペースを使用します&mdash;[ワークスペース計画](./prerequisites.md#log-analytics-workspace-and-automation-account-planning)に関するページを参照)。

2. Automation アカウントを作成します (または、要件を満たしている場合は既存のアカウントを使用します。[ワークスペース計画](./prerequisites.md#log-analytics-workspace-and-automation-account-planning)に関するページを参照してください)。

3. Automation アカウントと Log Analytics ワークスペースをリンクします (ポータルを通じてオンボードする場合は不要です)。

4. ワークスペースに対して Update Management および Change Tracking と Inventory を有効にします。

5. Azure Policy を使用して Azure VM をオンボードします (ポリシーによって Log Analytics Agent と Dependency Agent が Azure VM にインストールされます)。

6. Log Analytics エージェントをオンプレミス サーバーにインストールすることで、それらのサーバーをオンボードします。

次の表で説明されているファイルはこのサンプル内で使用されています。また、これらをカスタマイズして、独自のデプロイ シナリオをサポートすることができます。

| ファイル名 | 説明 |
|-----------|-------------|
| New-AMSDeployment.ps1 | オンボードを自動化するメインの調整スクリプト。 この PowerShell スクリプトには既存のサブスクリプションが必要ですが、リソース グループ、場所、ワークスペース、および Automation アカウントが存在しない場合は作成されます。 |
| Workspace-AutomationAccount.json | ワークスペースと Automation アカウント リソースをデプロイする Resource Manager テンプレート。 |
| WorkspaceSolutions.json | Log Analytics ワークスペースで目的のソリューションを有効にする Resource Manager テンプレート。 |
| ScopeConfig.json | Change Tracking ソリューションを備えたオンプレミスのサーバーにオプトイン モデルを使用する Resource Manager テンプレート。 オプトイン モデルの使用はオプションです。 |
| Enable-VMInsightsPerfCounters.ps1 | サーバーに対して VMInsight を有効にし、パフォーマンス カウンターを構成する PowerShell スクリプト。 |
| ChangeTracking-Filelist.json | Change Tracking によって監視されるファイルの一覧を定義する Resource Manager テンプレート。 |

New-AMSDeployment.ps1 は、次のコマンドを使用して実行できます。

```powershell
.\New-AMSDeployment.ps1 -SubscriptionName '{Subscription Name}' -WorkspaceName '{Workspace Name}' -WorkspaceLocation '{Azure Location}' -AutomationAccountName {Account Name} -AutomationAccountLocation {Account Location}
```

## <a name="next-steps"></a>次の手順

キー管理イベントと問題をチームに通知する基本的なアラートを設定する方法を確認します。

> [!div class="nextstepaction"]
> [基本アラートを設定する](./setup-alerts.md)
