---
title: Azure Policy の一般的な例
description: Azure 向けのクラウド導入フレームワークを使用し、PowerShell コマンドレットでポリシーを作成することにより、ガバナンス ポリシー要件に対するコンプライアンスを確保します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 05/10/2019
ms.topic: article
ms.service: cloud-adoption-framework
ms.subservice: operate
ms.openlocfilehash: cdaf9eaba2c7f8df6430dc3ef359e146515437fb
ms.sourcegitcommit: ea63be7fa94a75335223bd84d065ad3ea1d54fdb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "80356404"
---
# <a name="common-azure-policy-examples"></a>Azure Policy の一般的な例

[Azure Policy](https://docs.microsoft.com/azure/governance/policy/overview) は、お使いのクラウド リソースにガバナンスを適用するのに役立ちます。 このサービスは、ガバナンス ポリシー要件への会社全体のコンプライアンスを確実にするためのガードレールを作成するのに役立ちます。 ポリシーを作成するには、Azure portal または PowerShell コマンドレットを使用します。 この記事では、PowerShell コマンドレットの例を紹介します。

> [!NOTE]
> Azure Policy を使用した場合、強制ポリシー (**deployIfNotExists**) が自動的に既存の VM にデプロイされることはありません。 VM のコンプライアンスを維持するには、修復が必要です。 詳細については、「[Azure Policy を使って準拠していないリソースを修復する](https://docs.microsoft.com/azure/governance/policy/how-to/remediate-resources)」を参照してください。

## <a name="common-policy-examples"></a>一般的なポリシーの例

以降のセクションでは、いくつかのよく使用されるポリシーについて説明します。

### <a name="restrict-resource-regions"></a>リソースのリージョンの制限

規制とポリシーのコンプライアンスは、多くの場合、リソースがデプロイされている物理的な場所の制御に依存します。 組み込みポリシーを使用すると、ユーザーは、特定の許可された Azure リージョン内のみにリソースを作成できます。

ポータルでこのポリシーを見つけるには、ポリシー定義ページで、[場所] で検索します。 または、次のコマンドレットを実行してポリシーを見つけます。

```powershell
Get-AzPolicyDefinition | Where-Object { ($_.Properties.policyType -eq "BuiltIn") -and ($_.Properties.displayName -like "*location*") }
```

次のスクリプトでは、ポリシーの割り当て方法を示しています。 ポリシーを割り当てるサブスクリプションを指すように `$SubscriptionID` を変更します。 スクリプトを実行する前に、[Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.1.0) コマンドレットを使用してサインインします。

```powershell
#Specify the value for $SubscriptionID.
$SubscriptionID = <subscription ID>
$scope = "/subscriptions/$SubscriptionID"

#Replace the -Name GUID with the policy GUID you want to assign.
$AllowedLocationPolicy = Get-AzPolicyDefinition -Name "e56962a6-4747-49cd-b67b-bf8b01975c4c"

#Replace the locations with the ones you want to specify.
$policyParam = '{"listOfAllowedLocations":{"value":["eastus","westus"]}}'
New-AzPolicyAssignment -Name "Allowed Location" -DisplayName "Allowed locations for resource creation" -Scope $scope -PolicyDefinition $AllowedLocationPolicy -Location eastus -PolicyParameter $policyParam
```

このスクリプトを使用して、この記事で説明している他のポリシーを適用することもできます。 `$AllowedLocationPolicy` が設定されている行の GUID を、適用するポリシーの GUID に置き換えるだけです。

### <a name="block-certain-resource-types"></a>特定のリソースの種類のブロック

コストの管理に使用される別の一般的な組み込みポリシーを使用して、特定のリソースの種類をブロックすることもできます。

ポータルでこのポリシーを見つけるには、ポリシー定義ページで、[使用できるリソースの種類] で検索します。 または、次のコマンドレットを実行してポリシーを見つけます。

```powershell
Get-AzPolicyDefinition | Where-Object { ($_.Properties.policyType -eq "BuiltIn") -and ($_.Properties.displayName -like "*allowed resource types") }
```

使用するポリシーを識別した後、「[リソースのリージョンの制限](#restrict-resource-regions)」セクションにある PowerShell サンプルを変更して、このポリシーを割り当てることができます。

### <a name="restrict-vm-size"></a>VM サイズの制限

Azure では、さまざまなワークロードをサポートするための幅広い VM サイズが提供されています。 予算を管理するために、ご自身のサブスクリプション内で VM サイズのサブセットのみを許可するポリシーを作成できます。

### <a name="deploy-antimalware"></a>マルウェア対策のデプロイ

このポリシーを使用すると、マルウェア対策で保護されていない VM に、既定の構成を使用した Microsoft *IaaSAntimalware* 拡張機能をデプロイすることができます。

ポリシーの GUID は `2835b622-407b-4114-9198-6f7064cbe0dc` です。

次のスクリプトでは、ポリシーの割り当て方法を示しています。 スクリプトを使用するには、ポリシーを割り当てるサブスクリプションを指すように `$SubscriptionID` を変更します。 スクリプトを実行する前に、[Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.1.0) コマンドレットを使用してサインインします。

```powershell
#Specify the value for $SubscriptionID.
$SubscriptionID = <subscription ID>
$scope = "/subscriptions/$SubscriptionID"

$AntimalwarePolicy = Get-AzPolicyDefinition -Name "2835b622-407b-4114-9198-6f7064cbe0dc"

#Replace location "eastus" with the value that you want to use.
New-AzPolicyAssignment -Name "Deploy Antimalware" -DisplayName "Deploy default Microsoft IaaSAntimalware extension for Windows Server" -Scope $scope -PolicyDefinition $AntimalwarePolicy -Location eastus –AssignIdentity

```

## <a name="next-steps"></a>次のステップ

使用可能な他のサーバー管理ツールやサービスについて学習します。

> [!div class="nextstepaction"]
> [Azure サーバー管理ツールおよびサービス](./tools-services.md)
