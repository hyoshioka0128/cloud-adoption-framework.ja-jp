---
title: 推奨される Azure ロールベースのアクセス制御
description: チーム内の職務を分離し、Azure ロールベースのアクセス制御を付与して、ユーザーとグループがその職務を遂行できるようにする方法について説明します。
author: alexbuckgit
manager: BrianBlanchard
ms.author: brblanch
ms.date: 11/28/2018
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.custom: internal, virtual-network
ms.openlocfilehash: bfb25396e19b11258401dd68065282cc3e78566d
ms.sourcegitcommit: a0ddde4afcc7d8c21559e79d406dc439ee4f38d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/21/2020
ms.locfileid: "97713371"
---
# <a name="azure-role-based-access-control"></a>Azure ロールベースのアクセス制御

グループベースのアクセス権および特権を使用することをお勧めします。 個々のユーザーではなくグループを処理すれば、アクセス ポリシーの保守が簡素化され、チーム間に一貫したアクセス管理が提供され、構成エラーが削減されます。 適切なグループにユーザーを割り当てたり、グループからユーザーを削除することで、特定のユーザーの権限を最新に保つことができます。 [Azure ロールベースのアクセス制御 (Azure RBAC)](/azure/role-based-access-control/overview) では、ユーザー ロールごとに整理されているリソースのアクセスを詳細に管理できます。

ID およびセキュリティ戦略の一部として推奨される Azure RBAC の方法の概要については、[Azure の ID 管理とアクセス制御セキュリティのベスト プラクティス](/azure/security/fundamentals/identity-management-best-practices#use-role-based-access-control)に関するページを参照してください。

## <a name="overview-of-azure-role-based-access-control"></a>Azure ロールベースのアクセス制御の概要

[Azure ロールベースのアクセス制御](/azure/role-based-access-control/overview)を使用すると、チーム内の職務を分離し、特定の Azure Active Directory (Azure AD) ユーザー、グループ、サービス プリンシパル、またはマネージド ID が自分の業務を実行するために必要なアクセス権のみを付与することができます。 すべてのユーザーに Azure サブスクリプションまたはリソースへの無制限のアクセス権を付与するのではなく、リソースのセットごとにアクセス権を制限することができます。

[Azure ロール定義](/azure/role-based-access-control/role-definitions)には、そのロールに割り当てられているユーザーまたはグループに対して許可されている、または許可されていない操作が一覧表示されています。 ロールの[スコープ](/azure/role-based-access-control/overview#scope)は、これらの定義されたアクセス許可がどのリソースに適用されるかを指定します。 スコープは、複数のレベル (管理グループ、サブスクリプション、リソース グループ、またはリソース) で指定できます。 スコープは親子関係で構造化されています。

![Azure RBAC のスコープ階層](../../_images/azure-best-practices/rbac-scope.png)

ユーザーとグループを特定のロールに割り当て、ロールをスコープに割り当てる詳細な手順については、「[Azure portal を使用して Azure ロールの割り当てを追加または削除する](/azure/role-based-access-control/role-assignments-portal)」を参照してください。

アクセス制御戦略を計画するときは、ユーザーが作業を実行するために必要な権限のみを付与する最小特権のアクセス モデルを使用します。 次の図は、このアプローチを使用して Azure RBAC を使用するための推奨パターンを示しています。

![Azure RBAC を使用するための推奨パターン](../../_images/azure-best-practices/rbac-least-privilege.png)

> [!NOTE]
> 定義するアクセス許可が具体的または詳細なものになるほど、アクセス制御が複雑になり、管理が困難になる可能性が高くなります。 この傾向は、クラウド資産の規模が大きくなるにつれて顕著になります。 リソース固有のアクセス許可は使わないようにします。 代わりに、エンタープライズ全体のアクセス制御には[管理グループを使用し](/azure/governance/management-groups)、サブスクリプション内のアクセス制御には[リソース グループ](/azure/azure-resource-manager/management/overview#resource-groups)を使用します。 また、ユーザー固有のアクセス許可は使わないようにします。 代わりに、[Azure AD 内のグループ](/azure/active-directory/fundamentals/active-directory-manage-groups)にアクセス権を割り当てます。

## <a name="use-azure-built-in-roles"></a>Azure 組み込みロールを使用する

Azure では多くの組み込みのロール定義が用意されており、アクセス権を提供する次の 3 つのコア ロールがあります。

- [所有者ロール](/azure/role-based-access-control/built-in-roles#owner)は、リソースへのアクセスを含め、すべてを管理できます。
- [共同作業者ロール](/azure/role-based-access-control/built-in-roles#contributor)は、リソースへのアクセスを除くすべてを管理できます。
- [閲覧者ロール](/azure/role-based-access-control/built-in-roles#reader)は、すべてを表示できますが、変更することはできません。

これらのコア アクセス レベルから開始して、組み込みロールを追加することによって、特定のリソースの種類または Azure 機能にアクセスするためのより詳細なコントロールが提供されます。 たとえば、仮想マシンへのアクセスは、次の組み込みロールを使用して管理できます。

- [仮想マシン管理者ログイン ロール](/azure/role-based-access-control/built-in-roles#virtual-machine-administrator-login)は、ポータルで仮想マシンを表示し、`administrator` としてサインインできます。
- [仮想マシン共同作業者ロール](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)は仮想マシンを管理できますが、それらにアクセスしたり、接続先の仮想ネットワークまたはストレージ アカウントにアクセスしたりすることはできません。
- [仮想マシン ユーザー ログイン ロール](/azure/role-based-access-control/built-in-roles#virtual-machine-user-login)は、ポータルで仮想マシンを表示し、通常ユーザーとしてサインインできます。

組み込みロールを使用して特定の機能へのアクセスを管理する別の例については、「[事業単位、環境、プロジェクトをまたいでコストを追跡する](../azure-best-practices/track-costs.md#provide-the-right-level-of-cost-access)」の、コスト追跡機能へのアクセスの制御に関する説明を参照してください。

使用可能な組み込みロールの完全な一覧については、「[Azure 組み込みロール](/azure/role-based-access-control/built-in-roles)」をご覧ください。

## <a name="use-custom-roles"></a>カスタム ロールを使用する

Azure に組み込まれたロールはさまざまなアクセス制御シナリオをサポートしますが、組織やチームのすべてのニーズを満たせるわけではありません。 たとえば、仮想マシンと Azure SQL Database リソースの管理を担当する単一のユーザー グループがある場合、必要なアクセス制御の管理を最適化するためにカスタム ロールを作成することができます。

Azure RBAC のドキュメントには、[ロール定義の機能](/azure/role-based-access-control/role-definitions)の詳細と併せて、[カスタム ロールの作成](/azure/role-based-access-control/custom-roles)に関する手順が記載されています。

## <a name="separation-of-responsibilities-and-roles-for-large-organizations"></a>大規模組織における責任とロールの分離

Azure RBAC を使用すると、組織は大規模なクラウド資産内のさまざまな管理タスクにさまざまなチームを割り当てることができます。 これにより、中央の IT チームがコア アクセスとセキュリティ機能を制御しながら、ソフトウェア開発者や他のチームが、特定のワークロードやリソースのグループを大きく制御できるようにすることが可能です。

ほとんどのクラウド環境は、複数のロールを使用し、これらのロール間での責任の分離を強調するアクセス制御戦略からも恩恵を受けることができます。 このアプローチでは、リソースまたはインフラストラクチャを大幅に変更するには、完了するまでに複数のロールが関与する必要があり、複数のユーザーが変更を確認して承認する必要があります。 この責任の分離により、他のチーム メンバーが知らないうちに、1人の人物が機密データにアクセスしたり、脆弱性を導入したりする能力が制限されます。

次の表は、IT の責任を別々のカスタム ロールに分割するための一般的なパターンを示しています。

| グループ | 一般的なロール名 | 役割 |
| --- | --- | --- |
| セキュリティ運用 | SecOps | 全般的なセキュリティ監視を行います。 <br> 保存時の暗号化などのセキュリティ ポリシーを確立して適用します。 <br><br> 暗号化キーを管理します。 <br><br> ファイアウォール規則を管理します。 |
| ネットワーク運用担当者 | NetOps | ルートやピアリングなど、仮想ネットワーク内のネットワーク構成および運用を管理します。 |
| システム運用担当者 | SysOps | コンピューティングおよびストレージ インフラストラクチャのオプションを指定し、デプロイ済みのリソースを保守します。 |
| 開発、テスト、および運用担当者 | DevOps | ワークロード機能とアプリケーションを構築してデプロイします。 <br><br> サービスレベル アグリーメントやその他の品質基準を満たすように機能およびアプリケーションを運用します。 |

これらの標準ロール内のアクションおよびアクセス許可の内訳は、これらのロールがさまざまなレベルでさまざまなユーザーによって実行されたとしても、多くの場合はアプリケーション、サブスクリプション、またはすべてのクラウド資産について同じです。 したがって、環境内のさまざまなスコープに適用するための共通の Azure ロール定義のセットを作成できます。 その後、ユーザーおよびグループには共通のロールを割り当てることができますが、それらはユーザーおよびグループが管理する責任があるリソース、リソース グループ、サブスクリプション、または管理グループのスコープのみが対象となります。

たとえば、複数のサブスクリプションを含む[ハブ アンド スポーク ネットワーク トポロジ](../azure-best-practices/hub-spoke-network-topology.md)では、ハブとすべてのワークロード スポークに対して共通のロール定義のセットを使用することができます。 ハブ サブスクリプションの NetOps ロールは、すべてのワークロードで使用される共有サービスのためのネットワークの保守を担当している、組織の中央 IT チームのメンバーに割り当てることができます。 ワークロード スポーク サブスクリプションの NetOps ロールを、その特定のワークロード チームのメンバーに割り当てることで、ワークロード要件を最も良くサポートするように、そのサブスクリプション内でネットワークを構成することができます。 同じロール定義が両方に使用されますが、スコープベースの割り当てにより、ユーザーは自分の業務を実行するために必要なアクセス権のみを持つようになります。
