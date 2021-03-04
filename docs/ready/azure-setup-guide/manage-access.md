---
title: Azure ロールベースのアクセス制御を使用して Azure 環境へのアクセスを管理する
description: Azure ロールベースのアクセス制御を使用して Azure 環境に対するアクセス制御を設定する方法について説明します。
author: LijuKodicheraJayadevan
ms.author: brblanch
ms.date: 04/09/2019
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.localizationpriority: high
ms.custom: think-tank, fasttrack-edit, AQC, setup
ms.openlocfilehash: a89ae92dc9fd682b2a694610b394792361cf6fc0
ms.sourcegitcommit: 9e4bc0e233a24642853f5e8acbeb9746b2444024
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102115511"
---
# <a name="manage-access-to-your-azure-environment-with-azure-role-based-access-control"></a>Azure ロールベースのアクセス制御を使用して Azure 環境へのアクセスを管理する

Azure のリソースとサブスクリプションにアクセスできるユーザーを管理することは、Azure のガバナンス戦略の重要な部分を占めており、グループに基づくアクセス権と特権の割り当ては適切なプラクティスです。 個々のユーザーではなくグループを処理すれば、アクセス ポリシーの保守が簡素化され、チーム間に一貫したアクセス管理が提供され、構成エラーが削減されます。 Azure ロールベースのアクセス制御 (Azure RBAC) は、Azure でアクセスを管理するための主要な方法です。

Azure RBAC は、Azure 内のリソースの詳細なアクセス管理を実現します。 これは、Azure のリソースにアクセスできるユーザー、そのユーザーがそれらのリソースに対して実行できること、そのユーザーがアクセスできるスコープを管理するのに役立ちます。

アクセス制御戦略を計画する場合は、ユーザーの作業を実行するのに必要な最低限の特権をユーザーに付与します。 次の図は、Azure RBAC を割り当てる際の推奨パターンです。

![Azure のロールを示す図](./media/manage-access/role-examples.png)
*図 1:Azure のロール。*

アクセスと制御の方法を計画する場合は、組織内でセキュリティとコンプライアンス、IT 管理、およびエンタープライズ アーキテクトの役割を持つ人と協力することをお勧めします。

クラウド導入フレームワークには、クラウド導入への取り組みで [Azure ロールベースのアクセス制御](../considerations/roles.md)を使用することに関する追加のガイダンスがあります。

::: zone target="chromeless"

## <a name="actions"></a>Actions

**リソース グループへのアクセスを許可する:**

ユーザーにリソース グループへのアクセス権を付与するには:

1. **リソース グループ** に移動します。
1. リソース グループを選択します。
1. **[アクセス制御 (IAM)]** を選択します。
1. **[+ 追加]**  >  **[ロール割り当ての追加]** の順に選択します。
1. ロールを選択し、ユーザー、グループ、またはサービス プリンシパルへのアクセスを割り当てます。

::: form action="OpenBlade[#blade/HubsExtension/Resources/resourceType/Microsoft.Resources%2FSubscriptions%2FResourceGroups]" submitText="Go to resource groups" ::: form-end

**サブスクリプションへのアクセスを許可する:**

ユーザーにサブスクリプションへのアクセス権を付与するには:

1. **[サブスクリプション]** に移動します。
1. サブスクリプションを選択します。
1. **[アクセス制御 (IAM)]** を選択します。
1. **[+ 追加]**  >  **[ロール割り当ての追加]** の順に選択します。
1. ロールを選択し、ユーザー、グループ、またはサービス プリンシパルへのアクセスを割り当てます。

::: form action="OpenBlade[#blade/Microsoft_Azure_Billing/SubscriptionsBlade]" submitText="Go to subscriptions" ::: form-end

::: zone-end

::: zone target="docs"

## <a name="grant-resource-group-access"></a>リソース グループへのアクセスを許可する

ユーザーにリソース グループへのアクセス権を付与するには:

1. [リソース グループ](https://portal.azure.com/#blade/HubsExtension/BrowseResourceGroups)に移動します。
1. リソース グループを選択します。
1. **[アクセス制御 (IAM)]** を選択します。
1. **[+ 追加]**  >  **[ロール割り当ての追加]** の順に選択します。
1. ロールを選択し、ユーザー、グループ、またはサービス プリンシパルへのアクセスを割り当てます。

## <a name="grant-subscription-access"></a>サブスクリプションへのアクセスを許可する

ユーザーにサブスクリプションへのアクセス権を付与するには:

1. [[サブスクリプション]](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) に移動します。
1. サブスクリプションを選択します。
1. **[アクセス制御 (IAM)]** を選択します。
1. **[+ 追加]**  >  **[ロール割り当ての追加]** の順に選択します。
1. ロールを選択し、ユーザー、グループ、またはサービス プリンシパルへのアクセスを割り当てます。

## <a name="learn-more"></a>詳細情報

詳細については、次を参照してください。

- [Azure ロールベースのアクセス制御 (Azure RBAC) とは](/azure/role-based-access-control/overview)
- [クラウド導入フレームワーク:Azure ロールベースのアクセス制御の使用](../considerations/roles.md)

::: zone-end
