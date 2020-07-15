---
title: 貢献ガイド
description: 貢献ガイド。
author: alexbuckgit
ms.author: abuck
ms.date: 06/15/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.openlocfilehash: cc9676ec0e35cbb75b1884524d8c23b87c174460
ms.sourcegitcommit: 84d7bfd11329eb4c151c4c32be5bab6c91f376ed
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2020
ms.locfileid: "86235111"
---
# <a name="contribution-guide"></a>貢献ガイド

## <a name="enterprise-scale-committee"></a>エンタープライズ規模委員会

エンタープライズ規模委員会とそのメンバーは、エンタープライズ規模のリポジトリとその言語、設計、および Contoso の実装の主要な世話役です。

現在の委員会メンバー:

<!-- docsTest:disable -->
<!-- cSpell:disable -->

- Uday Pandya
- Callum Coffin
- Kristian Nese
- Victor Arzate
- Johan Dahlbom
- Lyon Till
- Niels Buit

<!-- docsTest:enable --->
<!-- cSpell:enable -->

## <a name="committee-member-responsibilities"></a>委員会メンバーの責任

委員会メンバーは、新しい機能または設計変更を提案する Requests for Comment (RFC) を確認し、承認する責任があります。

初期のエンタープライズ規模委員会は、Microsoft 社員で構成されています。 時間の経過と共に、コミュニティが成長し、新しいコミュニティ メンバーが委員会メンバーに加わることが予想されます。 メンバーシップは、貢献度と専門知識のレベルによって異なります。 プロジェクトに有意義な方法で貢献した個人は、それに応じて評価されます。

委員会メンバーは、いつでも強力なコミュニティ メンバーを指名して、委員会に参加させることができます。 指名は、その個人に資格が与えられる理由と今後の貢献方法が詳しく記載された RFC の形式で提出するようにします。 新しい委員会メンバーの承認には、RFC について話し合った後、全会一致の投票が必要です。

## <a name="contribution-scope-for-enterprise-scale-architecture-guidelines"></a>エンタープライズ規模アーキテクチャ ガイドラインの貢献範囲

このリポジトリへの貢献の範囲は、プラットフォームが進化し、新しいサービスと機能がお客様と共に運用環境で検証されるため、アーキテクチャの全体的なコンテキストで設計ガイドラインが更新されることになります。 プレースホルダー テンプレートを使用して、ドキュメントの更新のためにプル要求 (PR) を送信します。

## <a name="contribution-scope-for-contoso-reference-implementation"></a>Contoso のリファレンス実装の貢献範囲

実装ガイドとリファレンス実装は、新しいサービス、リソース、リソースのプロパティ、API バージョンの出現に応じて更新する必要があります。 コードの貢献は、主に Microsoft Azure Policy の定義と Contoso 実装の割り当てに焦点を当てます。

<!-- cSpell:ignore azops apiversion northeurope -->

## <a name="how-to-submit-a-pr-to-the-upstream-repo"></a>上流のリポジトリに PR を送信する方法

1. 次のコマンドを実行して、上流またはマスターに基づいて新しいブランチを作成します。

    ```shell
    git checkout -b feature upstream/master
    ```

2. PR に含める作業ブランチのファイルを確認します。

    ```shell
    #substitute file name as appropriate. below example
    git checkout feature: .\.github\workflows\azops-push.yml
    ```

3. Git ブランチを origin にプッシュします。

    ```shell
    git push origin -u
    ```

4. 上流からリモート `master` ブランチへの PR を作成します。

## <a name="writing-azure-resource-manager-templates-for-contoso-implementation"></a>Contoso 実装用の Azure Resource Manager テンプレートの作成

Resource Manager テンプレートとパラメーター ファイルを作成する際に、適切な方法や不適切な方法はありません。 Resource Manager は言語であり、誰もが書き方が異なります。 開発者グループ間で記述方法が同様のテンプレートおよびパラメーター ファイルはほとんどありません。 テンプレートとパラメーター ファイルの内容を解釈して、コードを管理し、構成から分離する明確なスタイル定義はありません。 パラメーターおよびオブジェクトをパラメーターとして (スキーマを使用せずに) いつ使用するかのガイダンスも解釈の対象となり、すべてに適合する 1 つの作成スタイルはありません。

複数の開発者が協力して大規模な開発と単体テストを簡素化できるように、テンプレートをパラメーター ファイルから完全に切り離して、特定のスタイルのテンプレートを採用しました。 この最小限の "1 つのテンプレートですべてを規定する" アプローチでは、すべてのリソース プロパティをパラメーター ファイル内の複合オブジェクトとして外部化し、プラットフォームで既に公開されているリソース スキーマに基づいて、厳密なスキーマ検証をファイルに適用できます。 このアプローチでは、テンプレートとパラメーターを明確に区別します。 パラメーター ファイルは、基本的に、`Get-AzResource` または `az resource show` を呼び出すときのリソースの RESTful 表現です。

- Template.json

```json
"resources": [{
        "condition": "[bool(equals(variables('resourceType'),'Microsoft.Authorization/policyDefinitions'))]",
        "type": "Microsoft.Authorization/policyDefinitions",
        "name": "[variables('policyDefinitions').name]",
        "apiVersion": "[variables('apiversion')[variables('resourceType')]]",
        "location": "northeurope",
        "properties": "[variables('policyDefinitions').Properties]"
    }],
```

バグ修正が最新の API バージョンに確実に組み込まれるように、[汎用マルチリソース テンプレート](https://github.com/uday31in/AzOps/blob/main/template/template.json)を使用できます。

- Template.parameters.json

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "input": {
            "value": {
                <copy-paste-value-of-powershell/cli-output-here>
            }
        }
    }
}
```

`Get-AzResource` 関数を呼び出して既存のリソースに `resourceId` を与えることにより、リソース定義を取得します。

```powershell
# Replace the resourceId in the command below before executing it

Get-AzResource -ResourceId '/providers/Microsoft.Management/managementGroups/contoso/providers/Microsoft.Authorization/policyDefinitions/DINE-Diagnostics-ActivityLog' | ConvertTo-Json -depth 100
```

<!-- docsTest:ignore "Policy definition" -->

設計上の判断を下すときは、次の長所と短所を考慮する必要があります。

- 長所:

  - Resource Manager テンプレートを作成する必要はなくなります。 最後の Resource Manager テンプレートは作成済みです。
  - リソースは、Azure portal、Azure CLI、PowerShell、またはサードパーティ製ツールを介して作成および更新された方法に関係なく、ライフサイクル全体にわたって一貫してエクスポートされます。
  - Git に格納されている構成と現在の構成の間のドリフトを検出する方が簡単です。基本的に 2 つの JSON ドキュメントを比較します。
  - クライアント側とサーバー側の両方で、単純なリソース間の暗黙的な依存関係を管理することができます。 Azure にはリソース間の循環依存関係があまりありません。また、既に公開されているリソース スキーマに基づいて暗黙的な依存関係を解決することができます。 たとえば、仮想マシンはカーネルレベルの仮想スイッチに依存している可能性がありますが、その逆はありません (たとえば、ポリシー定義 -> ポリシー割り当て -> ロールの割り当て -> 修復または仮想ネットワーク -> ExpressRoute または Key Vault -> Azure SQL)。

- 短所:

  パラメーター ファイルの複合オブジェクトを作成するときに、情報が失われる可能性があります。 この 1 回限りのアクティビティは、現在のリソースの基本定義を取得するか、最初にポータルを介してリソースを作成することで軽減できます。 Azure-partner-customer-usage-attribution を使用してテンプレートのデプロイを追跡することはできません。 これはエンタープライズ スケールの範囲外です。

リソースのデプロイに使用される現在の Resource Manager テンプレートに問題はありません。また、それらを書き直す必要はありません。 パイプラインによってそれらは引き続きデプロイされ、構成のドリフトが検出されます。 プラットフォームでは展開テンプレートのエクスポートによってこのアクションを完了することができないため、これらを調整することはできません。 これらのパラメーターがあるため、PR 内で送信されるすべてのテンプレートは、エクスポートするものがデプロイするものであるという原則に準拠する必要があります。

PR を送信する前に次のセクションをお読みください。ただし、Azure Key Vault や Azure Monitor ログなどのリソースをポリシー内にラップせずにデプロイするために、テンプレートとパラメーター ファイルを含む PR を送信しないでください。

## <a name="contributing-to-policy-definitions-policy-assignments-and-role-definitions-and-assignments-for-contoso-implementation"></a>ポリシー定義、ポリシー割り当て、および Contoso 実装のロールの定義と割り当てへの貢献

前述のセクションで説明した標準にパラメーターが準拠し、リソースの準備ができたら、リソースを管理グループ スコープでデプロイするか、サブスクリプション スコープ (接続または管理サブスクリプション) でデプロイするかを検討します。 パイプラインを使用すると、4 つのスコープのいずれかでテンプレートをデプロイできますが、ランディング ゾーン テンプレートの一部としてリソース グループ レベルでデプロイされることはありません。 最小バーは、ポリシー定義内にラップされたサブスクリプションレベルの展開テンプレートです。

- 次の処理が必要です。
  - ランディング ゾーン内にデプロイするリソースがある場合は、それらを `DeployIfNotExists` ポリシー内にラップします。この割り当ては、管理グループのスコープで行う必要があります。
  - ランディング ゾーン内のリソースのデプロイ数が 1 つだけの場合 (たとえば、ランディング ゾーン内の仮想ネットワークまたは新しい Azure リージョンの仮想ハブ)、理想的には、サブスクリプション スコープをターゲットとする存在スコープがポリシーに必要です。
  - 理想的には、すべてのポリシー定義を、エンドツーエンド テンプレートで定義されたルートで作成するようにします。

- リソース (キー コンテナーなど) をデプロイするためのテンプレートとパラメーター ファイルを含む PR を送信したり、エンドツーエンドのランディング ゾーンで記述されている以外の独自の管理グループ階層を作成したりしないでください。

例:

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "input": {
      "value": {
        "Name": "Tailspin",
        "DisplayName": "Tailspin",
        "ParentId": "/providers/Microsoft.Management/managementGroups/3fc1081d-6105-4e19-b60c-1ec1252cf560",
        "Children": [
          {
            "Id": "/providers/Microsoft.Management/managementGroups/Tailspin-bu1",
            "Name": "Tailspin-bu1",
            "DisplayName": "Tailspin-bu1",
            "properties": {
              "policyAssignments" :[
              ],
              "roleAssignments": [
              ]
            }
          }
        ],
        "properties": {
          "policyDefinitions": [
                <copy-paste of Json representation of the resource>
          ],
          "policyAssignments" :[
          ],
          "roleDefinitions": [
          ],
          "roleAssignments": [
          ]
        }
      }
    }
  }
}
```

## <a name="contributing-new-azure-policy-definitions-for-contoso-implementation"></a>Contoso 実装のための新しい Azure Policy 定義の貢献

エンタープライズ規模アーキテクチャに準拠するポリシー定義に貢献するには、次のツールと推奨事項を使用します。

[Visual Studio 用 Azure Policy 拡張機能](https://docs.microsoft.com/azure/governance/policy/how-to/extension-for-vscode)

この拡張機能を使用して、ポリシー エイリアスを検索し、リソースとポリシーを確認します。

## <a name="explore-available-resource-properties-with-corresponding-policy-aliases"></a>対応するポリシー エイリアスを使用して使用可能なリソース プロパティを探索する

Azure PowerShell の場合:

```powershell
# List all available providers
Get-AzPolicyAlias -ListAvailable

# Get aliases for a specific resource provider
(Get-AzPolicyAlias -NamespaceMatch 'Microsoft.Network').Aliases.Name
```

Azure CLI の場合

```bash
# List all available providers

az provider list --query [*].namespace

# Get aliases for a specific resource provider

az provider show --namespace Microsoft.Network --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
```

## <a name="contributing-new-azure-policy-assignments"></a>新しい Azure Policy 割り当ての貢献

すべてのポリシーを割り当てるときは、次の点を考慮する必要があります。

- 割り当ての意図を明確にします。 2 つのサブスクリプション (接続性と管理) または管理グループのどちらに属していますか。
- サブスクリプション内のリソースの配分はどのようなものですか。
- 使用されているリージョンは何ですか。サブスクリプションごとに複数のリージョンが許可または使用されていますか。
- ポリシーが割り当てられている場所に影響を与える可能性がある、または与えない可能性があるリソースの種類は何ですか。
- 同じまたは同様の目的を果たす複数のポリシーについて、それらをポリシー イニシアチブにバンドルできますか。
- 有効なポリシーの根拠は何ですか。 代わりに、監査ポリシーを強制に変換する必要はありますか。
- `DeployIfNotExists` ポリシーの場合、使用されるロールベースのアクセス制御定義の最小特権の原則に従っていますか。

<!-- cSpell:ignore don'ts -->

## <a name="code-of-conduct"></a>倫理規定

私たちは、熱心なコミュニティと共に、強力で生産的なコラボレーションを構築するために取り組んでいます。 私たちは、皆さんの意見を大切にしています。 私たちは、すべきこととやってはいけないことの原則とガイドラインに取り組んでいます。
