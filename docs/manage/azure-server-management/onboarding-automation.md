---
title: オンボーディングの自動化
description: Azure サーバー管理サービスのデプロイを自動化して効率を高めることを検討している方は、オンボーディング サンプル ファイルをご活用ください。
author: BrianBlanchard
ms.author: brblanch
ms.date: 05/10/2019
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: operate
ms.openlocfilehash: b3c32b6d6afa9b640dd379230865728fbd53c925
ms.sourcegitcommit: af521583b98153f7157895b7ba9de71183d437b0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/31/2020
ms.locfileid: "89176482"
---
# <a name="automate-onboarding"></a>オンボーディングの自動化

Azure サーバー管理サービスのデプロイの効率を向上させるには、このガイドの前のセクションで説明されているようにデプロイを自動化することを検討してください。 以降のセクションで提供されているスクリプトとサンプル テンプレートは、オンボード プロセスの独自の自動化を開発するための出発点です。

このガイダンスには、[CloudAdoptionFramework](https://aka.ms/caf/manage/automation-samples) という、サンプル コードのサポートされている GitHub リポジトリがあります。 リポジトリでは、Azure サーバー管理サービスのデプロイの自動化に役立つスクリプト例と Azure Resource Manager テンプレートが提供されます。

サンプル ファイルでは、Azure PowerShell コマンドレットを使用して次のタスクを自動化する方法が示されています。

- [Log Analytics ワークスペース](/azure/azure-monitor/platform/manage-access)を作成します。 (または、要件を満たしている場合は既存のワークスペースを使用します。 詳細については、[ワークスペースの計画](./prerequisites.md#log-analytics-workspace-and-automation-account-planning)に関するページを参照してください。

- Automation アカウントを作成するか、要件を満たしている既存のアカウントを使用します。 詳細については、「[ワークスペースの計画](./prerequisites.md#log-analytics-workspace-and-automation-account-planning)」を参照してください。

- Automation アカウントと Log Analytics ワークスペースをリンクします。 Azure portal を使用してオンボードしている場合、このステップは必要ありません。

- ワークスペースに対して Update Management および Change Tracking と Inventory を有効にします。

- Azure Policy を使用して Azure VM をオンボードします。 ポリシーによって Log Analytics エージェントと Microsoft Dependency Agent が Azure VM にインストールされます。

- [Azure Policy](https://docs.microsoft.com/azure/backup/backup-azure-auto-enable-backup) を使用して、VM に対する Azure Backup を自動的に有効にします。

- Log Analytics エージェントをオンプレミス サーバーにインストールすることで、それらのサーバーをオンボードします。

このサンプルでは、次の表で説明するファイルが使用されています。 これらをカスタマイズして、独自のデプロイ シナリオをサポートできます。

| ファイル名 | 説明 |
|-----------|-------------|
| `New-AMSDeployment.ps1` | オンボードを自動化するメインの調整スクリプト。 リソース グループ、場所、ワークスペース、Automation アカウントがまだ存在しない場合は、それらが作成されます。 この PowerShell スクリプトには、既存のサブスクリプションが必要です。 |
| `Workspace-AutomationAccount.json` | ワークスペースと Automation アカウント リソースをデプロイする Resource Manager テンプレート。 |
| `WorkspaceSolutions.json` | Log Analytics ワークスペース内で目的のソリューションを有効にする Resource Manager テンプレート。 |
| `ScopeConfig.json` | Change Tracking ソリューションを備えたオンプレミスのサーバーにオプトイン モデルを使用する Resource Manager テンプレート。 オプトイン モデルの使用はオプションです。 |
| `Enable-VMInsightsPerfCounters.ps1` | サーバーに対して VM Insights を有効にし、パフォーマンス カウンターを構成する PowerShell スクリプト。 |
| `ChangeTracking-FileList.json` | Change Tracking によって監視されるファイルの一覧を定義する Resource Manager テンプレート。 |

次のコマンドを使用して `New-AMSDeployment.ps1` を実行します。

```powershell
.\New-AMSDeployment.ps1 -SubscriptionName '{Subscription Name}' -WorkspaceName '{Workspace Name}' -WorkspaceLocation '{Azure Location}' -AutomationAccountName {Account Name} -AutomationAccountLocation {Account Location}
```

## <a name="next-steps"></a>次のステップ

キー管理イベントと問題をチームに通知する基本的なアラートを設定する方法を確認します。

> [!div class="nextstepaction"]
> [基本アラートを設定する](./setup-alerts.md)
