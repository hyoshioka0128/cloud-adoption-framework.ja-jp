---
title: Azure リソースの効果的な整理
description: Azure リソースを効果的に整理してリソース管理を簡素化するためのベスト プラクティスについて説明します。
author: laraaleite
ms.author: brblanch
ms.date: 04/09/2019
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.localizationpriority: high
ms.custom: think-tank, fasttrack-edit, AQC, setup
ms.openlocfilehash: 95ca26aaa53625578fec9b4b4d169551d9c0f0d6
ms.sourcegitcommit: a0ddde4afcc7d8c21559e79d406dc439ee4f38d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/21/2020
ms.locfileid: "97713659"
---
<!-- cSpell:ignore profx fsubscriptions fresource -->

# <a name="organize-your-azure-resources-effectively"></a>Azure リソースの効果的な整理

クラウドベースのリソースを整理することは、ワークロードに関連するコストをセキュリティ保護、管理、および追跡するために不可欠です。 ご利用のリソースを整理するには、管理グループ階層を定義し、十分に考慮された名前付け規則に従って、リソースのタグ付けを適用してください。

<!-- markdownlint-disable MD024 -->

## <a name="azure-management-groups-and-hierarchy"></a>[Azure の管理グループと階層](#tab/AzureManagementGroupsAndHierarchy)

Azure には、管理グループ、サブスクリプション、リソース グループ、およびリソースという 4 つのレベルの管理スコープが用意されています。 次の図に、これらのレベルの関係を示します。

   ![管理階層レベルの関係を示す図](./media/organize-resources/scope-levels.png) _図 1: 4 つの管理スコープ レベルが相互にどのように関連しているか。_

- **管理グループ:** これらのグループは、複数のサブスクリプションのアクセス、ポリシー、コンプライアンスを管理するのに役立つコンテナーです。 管理グループ内のすべてのサブスクリプションは、管理グループに適用された条件を自動的に継承します。
- **サブスクリプション:** サブスクリプションでは、ユーザー アカウントと、それらのユーザー アカウントによって作成されたリソースが論理的に関連付けられます。 各サブスクリプションには、作成して使用できるリソース量に対する制限やクォータがあります。 組織では、ユーザー、チーム、またはプロジェクトによって作成されるリソースとコストを管理するためにサブスクリプションを使用できます。
- **リソース グループ:** リソース グループとは、Web アプリ、データベース、ストレージ アカウントなどの Azure リソースのデプロイと管理に使用する論理コンテナーです。
- **リソース:** リソースは、仮想マシン、ストレージ、SQL データベースなど、ユーザーが作成するサービスのインスタンスです。

### <a name="scope-of-management-settings"></a>管理の設定のスコープ

どの管理レベルでも、ポリシーや Azure ロールベースのアクセス制御などの管理設定を適用できます。 選択するレベルで、設定の適用範囲が決まります。 上位レベルの設定が下位レベルに継承されます。 たとえば、サブスクリプションにポリシーを適用すると、そのポリシーはそのサブスクリプション内のすべてのリソース グループとリソースにも適用されます。

通常、より高位のレベルで重要な設定を適用し、より低位のレベルでプロジェクト固有の要件を適用することをお勧めします。 たとえば、組織のすべてのリソースを確実に特定のリージョンに配置したい場合などがあります。 これを行うには、許可された場所を指定するポリシーをサブスクリプションに適用します。 組織内の他のユーザーが新しいリソース グループとリソースを追加すると、許可された場所が自動的に適用されます。 ポリシーについて詳しくは、このガイドのガバナンス、セキュリティ、およびコンプライアンスのセクションをご覧ください。

サブスクリプションの数が少なければ、その個別管理は比較的簡単です。 使用するサブスクリプションの数が増えた場合は、サブスクリプションとリソースの管理を簡素化するために管理グループ階層の作成を検討してください。 詳細については、[Azure サブスクリプションの整理と管理](../azure-best-practices/organize-subscriptions.md)に関するページを参照してください。

コンプライアンス戦略を計画する際には、組織内でセキュリティとコンプライアンス、IT 管理、エンタープライズ アーキテクチャ、ネットワーク、財務、および調達の役割を持つ人と協力してください。

::: zone target="docs"

### <a name="create-a-management-level"></a>管理レベルの作成

管理グループ、追加のサブスクリプション、またはリソース グループを作成することができます。

#### <a name="create-a-management-group"></a>管理グループの作成

管理グループの作成により、複数のサブスクリプションのアクセス、ポリシー、コンプライアンスを管理できます。

1. [[管理グループ]](https://portal.azure.com/#blade/Microsoft_Azure_ManagementGroups/HierarchyBlade) に移動します。
2. **[管理グループの追加]** を選択します。

#### <a name="create-a-subscription"></a>サブスクリプションの作成

サブスクリプションを使用すると、コストと、ユーザー、チーム、またはプロジェクトによって作成されるリソースを管理できます。

1. [[サブスクリプション]](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) に移動します。
2. **[追加]** を選択します。

> [!NOTE]
> サブスクリプションはプログラムで作成することもできます。 詳細については、[Azure サブスクリプションをプログラムで作成する方法](/azure/cost-management-billing/manage/programmatically-create-subscription-preview?tabs=azure-powershell)に関するページを参照してください。

#### <a name="create-a-resource-group"></a>リソース グループを作成する

リソース グループを作成して、Web アプリ、データベースなどのリソースや、同じライフサイクル、アクセス許可、ポリシーを共有するストレージ アカウントなどのリソースを保持します。

1. [リソース グループ](https://portal.azure.com/#create/Microsoft.ResourceGroup)に移動します。
1. **[追加]** を選択します。
1. 下にリソース グループを作成した **サブスクリプション** を選択します。
1. **[リソース グループ]** の名前を入力します。
1. リソース グループの場所の **リージョン** を選択します。

### <a name="learn-more"></a>詳細情報

詳細については、次を参照してください。

- [Azure の基礎](../considerations/fundamental-concepts.md)
- [初期サブスクリプションを作成する](../azure-best-practices/initial-subscriptions.md)
- [追加の Azure サブスクリプションを作成して、Azure 環境をスケーリングする](../azure-best-practices/scale-subscriptions.md)
- [Azure サブスクリプションを整理および管理する](../azure-best-practices/organize-subscriptions.md)
- [Azure 管理グループでリソースを整理する](/azure/azure-resource-manager/management-groups-overview)
- [Azure でのリソース アクセス管理について](../../govern/resource-consistency/resource-access-management.md)
- [サブスクリプション サービスの制限](/azure/azure-resource-manager/management/azure-subscription-service-limits)

::: zone-end

::: zone target="chromeless"

### <a name="actions"></a>Actions

**管理グループの作成:**

管理グループの作成により、複数のサブスクリプションのアクセス、ポリシー、コンプライアンスを管理できます。

1. **[管理グループ]** に移動します。
1. **[管理グループの追加]** を選択します。

::: form action="OpenBlade[#blade/Microsoft_Azure_ManagementGroups/HierarchyBlade]" submitText="Go to Management groups" :::

**追加のサブスクリプションの作成:**

サブスクリプションを使用すると、コストと、ユーザー、チーム、またはプロジェクトによって作成されるリソースを管理できます。

1. **[サブスクリプション]** に移動します。
1. **[追加]** を選択します。

::: form action="OpenBlade[#blade/Microsoft_Azure_Billing/SubscriptionsBlade]" submitText="Go to Subscriptions" :::

**リソース グループの作成:**

リソース グループを作成して、Web アプリ、データベースなどのリソースや、同じライフサイクル、アクセス許可、ポリシーを共有するストレージ アカウントなどのリソースを保持します。

1. **リソース グループ** に移動します。
1. **[追加]** を選択します。
1. 下にリソース グループを作成した **サブスクリプション** を選択します。
1. **[リソース グループ]** の名前を入力します。
1. リソース グループの場所の **リージョン** を選択します。

::: form action="Create[#create/Microsoft.ResourceGroup]" submitText="Create a resource group" :::

::: zone-end

## <a name="naming-standards"></a>[命名規則](#tab/NamingStandards)

適切な命名規則を使用することで、Azure portal、請求書、自動化スクリプト内でリソースを識別しやすくなります。 名前付け戦略には、リソース名のコンポーネントとして、ビジネスと運用の詳細を含める必要があります。

この戦略のビジネスに関連した面では、チームを識別するために必要な組織情報がリソース名に含まれるよう徹底する必要があります。 リソース コストを担当するビジネス オーナーと共にリソースを使用します。

運用の面では、IT チームが必要とする情報が名前に含まれるよう徹底する必要があります。 ワークロード、アプリケーション、環境、重要度など、リソースの管理に役立つ情報を識別する情報を使用します。

リソースの種類によって、[名前付け規則と制限事項](/azure/azure-resource-manager/management/resource-name-rules)が異なります。 特に企業のクラウド導入作業をサポートすることを目的とする詳細と推奨事項については、クラウド導入フレームワークの[名前付けとタグ付けに関するガイダンス](../azure-best-practices/naming-and-tagging.md)を参照してください。

次の表には、例としてのいくつかの Azure リソースの種類に対する名前付けパターンが含まれています。

::: zone target="docs"

>[!TIP]
> 特殊文字 (`-` または `_`) は、名前の先頭または末尾には使用しないでください。 こうした文字があると、ほとんどの検証規則でエラーが発生します。

::: zone-end

| Entity | Scope | 長さ | 大文字小文字の区別 | 有効な文字 | 提案されるパターン | 例 |
| --- | --- | --- | --- | --- | --- | --- |
| Resource group | サブスクリプション | 1-90 | 大文字と小文字は区別されない | 英数字、アンダースコア、かっこ、ハイフン、ピリオド (末尾を除く)、および Unicode 文字 | `<service short name>-<environment>-rg` | `profx-prod-rg` |
| 可用性セット | Resource group | 1 ～ 80 | 大文字と小文字は区別されない | 英数字、アンダースコア、ハイフン | `<service-short-name>-<context>-as` | `profx-SQL-as` |
| タグ | 関連付けられたエンティティ | 512 (名前)、256 (値) | 大文字と小文字は区別されない | 英数字 | `"Key" : "value"` | `"Department" : "Central IT"` |

## <a name="resource-tags"></a>[リソース タグ](#tab/ResourceTags)

タグは、リソースとリソース グループをすばやく特定するのに役立ちます。 Azure リソースにタグを適用すると、カテゴリ別に論理的に整理できます。 各タグは名前と値で構成されます。 たとえば、運用環境のすべてのリソースには名前 "環境" と値 "運用" を適用できます。 タグには、リソースの関連するワークロードまたはアプリケーション、運用要件、および所有者情報に関するコンテキストを含める必要があります。

タグを適用すると、そのタグの名前と値が付けられた、サブスクリプション内のすべてのリソースを取得できます。 課金または管理用にリソースを整理するときに、タグを使用して、さまざまなリソース グループから関連するリソースを取得できます。

その他の多くの要素にもタグを使用できます。 一般的な用途は次のとおりです。

- **メタデータとドキュメント:** 管理者は、`ProjectOwner` などのタグを適用して、操作しているリソースに関する詳細を簡単に確認できます。
- **自動化:** `ShutdownTime` や `DeprovisionDate` のようなタグ値に基づいてアクションを実行できるスクリプトを定期的に実行することが考えられます。
- **コストの最適化**: コストを担当するチームとリソースにリソースを割り当てることができます。 Azure Cost Management + Billing では、コスト センター タグをフィルターとして適用して、チームまたは部門の使用量に基づいて料金を報告できます。

各リソースまたはリソース グループには、最大で 50 個のタグ名とタグ値のペアを付けることができます。 この制限は、リソース グループまたはリソースに直接適用されたタグにのみ適用されます。

タグ付けの推奨事項と例の詳細については、クラウド導入フレームワークの[推奨される名前付けおよびタグ付け規則](../azure-best-practices/naming-and-tagging.md)を参照してください。

::: zone target="docs"

### <a name="apply-a-resource-tag"></a>リソース タグの適用

リソース グループにタグを適用するには:

1. [リソース グループ](https://portal.azure.com/#blade/HubsExtension/BrowseResourceGroups)に移動します。
1. リソース グループを選択します。
1. **[タグの割り当て]** を選択します。
1. 新しい名前と値を入力するか、ドロップダウン リストを使用して既存の名前と値を選択します。

### <a name="learn-more"></a>詳細情報

詳しくは、「[タグを使用した Azure リソースの整理](/azure/azure-resource-manager/management/tag-resources)」をご覧ください。

::: zone-end

::: zone target="chromeless"

### <a name="action"></a>アクション

**リソース タグの適用:**

リソース グループにタグを適用するには:

1. **リソース グループ** に移動します。
1. リソース グループを選択します。
1. **[タグ]** を選択します。
1. 新しい名前と値を入力するか、既存の名前と値を選択します。

::: form action="OpenBlade[#blade/HubsExtension/Resources/resourceType/Microsoft.Resources%2FSubscriptions%2FResourceGroups]" submitText="Go to resource groups" :::

::: zone-end
