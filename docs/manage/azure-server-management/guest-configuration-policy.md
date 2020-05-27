---
title: ゲスト構成ポリシー
description: Azure Policy のゲスト構成拡張機能を使用して Azure VM 内の構成設定を監査する方法を、Azure 向けのクラウド導入フレームワークを使用して学習します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 05/10/2019
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: operate
ms.openlocfilehash: 938934e1a45d8f8abebede6cca8dcd9987cfb7e0
ms.sourcegitcommit: bd9872320b71245d4e9a359823be685e0f4047c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/26/2020
ms.locfileid: "83861465"
---
# <a name="guest-configuration-policy"></a>ゲスト構成ポリシー

Azure Policy の[ゲスト構成](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration)拡張機能を使用して、仮想マシン内の構成設定を監査できます。 ゲスト構成は、現在 Azure VM 上でのみサポートされます。

ゲスト構成ポリシーの一覧を見つけるには、Azure Policy ポータル ページで「ゲスト構成」を検索します。 または、PowerShell ウィンドウで次のコマンドレットを実行して、一覧を見つけます。

```powershell
Get-AzPolicySetDefinition | where-object {$_.Properties.metadata.category -eq "Guest Configuration"}
```

> [!NOTE]
> ゲスト構成機能は、追加のポリシー セットをサポートするために定期的に更新されます。 新しいサポート対象ポリシーを定期的に確認し、役立つかどうかを評価してください。

<!-- TODOBACKLOG: Update these links when available. 

By default, we recommend that you enable the following policies:

- **Preview:** Audit to verify that password-security settings are correct on Linux and Windows machines.
- Audit to verify that certificates are not nearing expiration on Windows VMs.

-->

## <a name="deployment"></a>デプロイ

次の PowerShell スクリプトの例を使用して、これらのポリシーをデプロイします。

- Windows および Linux コンピューターのパスワード セキュリティ設定が正しく設定されていることを確認します。
- Windows VM で証明書の有効期限が近づいていないことを確認します。

 このスクリプトを実行する前に、[Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.1.0) コマンドレットを使用してサインインします。 スクリプトを実行するときに、ポリシーを適用するサブスクリプションの名前を指定する必要があります。

```powershell

    # Assign Guest Configuration policy.

    param (
        [Parameter(Mandatory=$true)]
        [string] $SubscriptionName
    )

    $Subscription = Get-AzSubscription -SubscriptionName $SubscriptionName
    $scope = "/subscriptions/" + $Subscription.Id

    $PasswordPolicy = Get-AzPolicySetDefinition -Name "3fa7cbf5-c0a4-4a59-85a5-cca4d996d5a6"
    $CertExpirePolicy = Get-AzPolicySetDefinition -Name "b6f5e05c-0aaa-4337-8dd4-357c399d12ae"

    New-AzPolicyAssignment -Name "PasswordPolicy" -DisplayName "[Preview]: Audit that password security settings are set correctly inside Linux and Windows machines" -Scope $scope -PolicySetDefinition $PasswordPolicy -AssignIdentity -Location eastus

    New-AzPolicyAssignment -Name "CertExpirePolicy" -DisplayName "[Preview]: Audit that certificates are not expiring on Windows VMs" -Scope $scope -PolicySetDefinition $CertExpirePolicy -AssignIdentity -Location eastus

```

## <a name="next-steps"></a>次のステップ

重要なファイル、サービス、ソフトウェア、レジストリの変更に対して[変更の追跡とアラートを有効にする](./enable-tracking-alerting.md)方法をご確認ください。

> [!div class="nextstepaction"]
> [重要な変更の追跡とアラートを有効にする](./enable-tracking-alerting.md)
