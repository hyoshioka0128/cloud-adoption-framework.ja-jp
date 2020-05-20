---
title: Azure 環境へのアクセスを管理する
description: ロールベースのアクセス制御 (RBAC) を使用して Azure 環境に対するアクセス制御を設定する方法について説明します。
author: LijuKodicheraJayadevan
ms.author: kfollis
ms.date: 04/09/2019
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.custom: fasttrack-edit, AQC, setup
ms.localizationpriority: high
ms.openlocfilehash: 94f51b92b50dabff8551e30e476d2738c3f2fef7
ms.sourcegitcommit: 7660521b631ea092fb805df9c9d28ad3024287ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2020
ms.locfileid: "83621649"
---
<!-- cSpell:ignore LijuKodicheraJayadevan -->

# <a name="manage-access-to-your-azure-environment-with-role-based-access-control"></a>ロールベースのアクセス制御を使用して Azure 環境へのアクセスを管理する

Azure のリソースとサブスクリプションにアクセスできるユーザーを管理することは、Azure のガバナンス戦略の重要な部分を占めており、グループに基づくアクセス権と特権の割り当ては適切なプラクティスです。 個々のユーザーではなくグループを処理すれば、アクセス ポリシーの保守が簡素化され、チーム間に一貫したアクセス管理が提供され、構成エラーが削減されます。 Azure のロールベースのアクセス制御 (RBAC) は、Azure でアクセスを管理するための主要な方法です。

RBAC は、Azure 内のリソースの詳細なアクセス管理を実現します。 これは、Azure のリソースにアクセスできるユーザー、そのユーザーがそれらのリソースに対して実行できること、そのユーザーがアクセスできるスコープを管理するのに役立ちます。

アクセス制御戦略を計画する場合は、ユーザーの作業を実行するのに必要な最低限の特権をユーザーに付与します。 次の図は、RBAC 割り当てパターン例です。

![RBAC のロールを示す図](./media/manage-access/role-examples.png)

アクセスと制御の方法を計画する場合は、組織内でセキュリティとコンプライアンス、IT 管理、およびエンタープライズ アーキテクトの役割を持つ人と協力することをお勧めします。

クラウド導入フレームワークには、クラウド導入への取り組みの一環として、[ロールベースのアクセス制御の使用](../considerations/roles.md)方法に関する追加のガイダンスがあります。

::: zone target="chromeless"

## <a name="actions"></a>Actions

**リソース グループへのアクセスを許可する:**

ユーザーにリソース グループへのアクセス権を付与するには:

1. **リソース グループ**に移動します。
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

- [ロールベースのアクセス制御 (RBAC) とは何か](https://docs.microsoft.com/azure/role-based-access-control/overview)
- [クラウド導入フレームワーク:ロールベースのアクセス制御の使用](../considerations/roles.md)

::: zone-end
