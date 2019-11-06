---
title: Azure 基礎の概念
titleSuffix: Microsoft Cloud Adoption Framework for Azure
description: Azure で使用されている基本的な概念と用語、概念どうしの関連性について説明します。
author: alexbuckgit
ms.author: abuck
ms.date: 05/20/2019
ms.topic: guide
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.openlocfilehash: 30dd36841171ebc0e239a8e59f94d290cc5782c7
ms.sourcegitcommit: bf9be7f2fe4851d83cdf3e083c7c25bd7e144c20
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73561767"
---
# <a name="azure-fundamental-concepts"></a>Azure 基礎の概念

Azure で使用されている基本的な概念と用語、概念どうしの関連性について説明します。

## <a name="azure-terminology"></a>Azure の用語

Azure クラウドの導入作業を開始する場合は、以下の定義を理解しておくと役に立ちます。

- **リソース:** Azure によって管理されるエンティティ。 Azure 仮想マシン、仮想ネットワーク、ストレージ アカウントなどが例として挙げられます。
- **サブスクリプション:** リソースの論理コンテナー。 各 Azure リソースは、1 つのサブスクリプションだけに関連付けられます。 Azure の導入はサブスクリプションの作成から始まります。
- **Azure アカウント:** Azure サブスクリプションを作成するときに指定するメール アドレスが、サブスクリプションの Azure アカウントです。 メール アカウントに関連付けられている当事者が、サブスクリプションのリソースにかかる毎月の費用に対して責任を負います。 Azure アカウントを作成するときに、連絡先情報と、クレジット カードなどの課金の詳細情報を提供します。 複数のサブスクリプションに対して同じ Azure アカウント (メール アドレス) を使用できます。 各サブスクリプションが関連付けられる Azure アカウントは 1 つに限られます。
- **アカウント管理者:** Azure サブスクリプションの作成に使用されるメール アドレスに関連付けられている当事者。 アカウント管理者は、サブスクリプションのリソースにかかる全費用の支払いに対して責任を負います。
- **Azure Active Directory (Azure AD):** Microsoft のクラウドベースの ID とアクセスの管理サービス。 従業員は、Azure AD を通じてリソースにサインインし、アクセスすることができます。
- **Azure AD テナント:** Azure AD の信頼された専用インスタンス。 Azure AD テナントは、組織が Microsoft クラウド サービスのサブスクリプション (Microsoft Azure、Microsoft Intune、Office 365 など) に最初にサインアップしたときに自動的に作成されます。 1 つの Azure テナントは単一の組織を表します。
- **Azure AD ディレクトリ:** 各 Azure AD テナントには、信頼された専用のディレクトリが 1 つ用意されます。 ディレクトリには、テナントのユーザー、グループ、アプリが含まれています。 ディレクトリは、テナント リソースに対する ID とアクセスの管理機能を実行するために使用されます。 ディレクトリは複数のサブスクリプションに関連付けることができますが、各サブスクリプションが関連付けられるディレクトリは 1 つに限られます。
- **リソース グループ:** サブスクリプション内の関連するリソースをグループ化する論理コンテナー。 各リソースが所属できるリソース グループは 1 つに限られます。
- **管理グループ:** 1 つ以上のサブスクリプションに使用する論理コンテナー。 管理グループ、サブスクリプション、リソース グループ、リソースからなる階層を定義し、アクセス、ポリシー、コンプライアンスを継承によって効率よく管理できます。
- **[リージョン]:** 待ち時間で定義された境界内にデプロイされる Azure データセンターのセット。 データセンターは、リージョンの待ち時間の短い専用ネットワーク経由で接続されます。 ほとんどの Azure リソースは特定の Azure リージョンで実行されます。

## <a name="azure-subscription-purposes"></a>Azure サブスクリプションの目的

Azure サブスクリプションにはいくつかの目的があります。 Azure サブスクリプションとは:

- **法的契約。** 各サブスクリプションは [Azure プラン](https://azure.microsoft.com/support/legal/offer-details) (無料試用版や従量課金制など) に関連付けられます。 各プランには固有の料金プラン、特典、使用条件が設定されています。 Azure プランはサブスクリプションを作成するときに選択します。
- **支払い契約。** サブスクリプションを作成するときに、そのサブスクリプションの支払い情報 (クレジット カード番号など) を指定します。 そのサブスクリプションにデプロイされたリソースにかかる費用が毎月計算され、指定した支払い方法で請求されます。
- **スケールの境界。** サブスクリプションに対してスケール制限が定義されます。 サブスクリプションのリソースは、設定されたスケール制限を超えることはできません。 たとえば、1 つのサブスクリプションで作成できる仮想マシンの数には制限があります。
- **管理上の境界。** サブスクリプションは、管理、セキュリティ、ポリシーの境界として機能します。 Azure では、これらのニーズに対応するその他のメカニズムも提供しています (管理グループ、リソース グループ、ロールベースのアクセス制御など)。

## <a name="azure-subscription-considerations"></a>Azure サブスクリプションに関する考慮事項

Azure サブスクリプションを作成するときに、サブスクリプションについていくつかの重要な選択を行います。

- **サブスクリプションの支払い担当者**: サブスクリプションを作成するときに指定するメール アドレスに関連付けられる当事者は、既定でサブスクリプションのアカウント管理者です。 このメール アドレスに関連付けられている当事者が、サブスクリプションのリソースにかかる全費用の支払いに対して責任を負います。
- **関心のある Azure プラン**: 各サブスクリプションは、特定の [Azure プラン](https://azure.microsoft.com/support/legal/offer-details)に関連付けられます。 自分のニーズに最も合う Azure プランを選択できます。 たとえば、サブスクリプションを使用して非運用ワークロードを実行する場合は、開発テスト用の従量課金制プランまたは Enterprise Dev/Test プランを選択することができます。

> [!NOTE]
> Azure にサインアップするときに、"*Azure アカウントの作成*" というフレーズが表示されることがあります。 Azure アカウントは、Azure サブスクリプションを作成し、サブスクリプションをメール アカウントに関連付けるときに作成します。

## <a name="azure-administrative-roles"></a>Azure 管理ロール

Azure では、サブスクリプション、ID、リソースを管理するための 3 種類のロールが定義されています。

- 従来のサブスクリプション管理者ロール
- Azure ロールベース アクセス制御 (RBAC) ロール
- Azure Active Directory (Azure AD) 管理者ロール

Azure サブスクリプションのアカウント管理者ロールは、Azure サブスクリプションの作成に使用されるメール アカウントに割り当てられます。 アカウント管理者はサブスクリプションの請求先所有者です。 アカウント管理者は、[Azure アカウント センター](https://account.azure.com/Subscriptions)でサブスクリプションの詳細を管理できます。

既定では、サブスクリプションのサービス管理者ロールは、Azure サブスクリプションの作成に使用されるメール アカウントにも割り当てられます。 サービス管理者には、サブスクリプションに関して、RBAC ベースの所有者ロールと同等の権限が与えられます。 サービス管理者には、Azure portal へのフル アクセス権も与えられます。 アカウント管理者は、サービス管理者を別のメール アカウントに変更できます。

Azure サブスクリプションを作成するときに、それを既存の Azure AD テナントに関連付けることができます。 これを行わなかった場合、新しい Azure AD テナントが関連するディレクトリと共に作成されます。 Azure AD ディレクトリのグローバル管理者ロールは、Azure AD サブスクリプションの作成に使用されるメール アカウントに割り当てられます。

メール アカウントは複数の Azure サブスクリプションに関連付けることができます。 アカウント管理者は、サブスクリプションを別のアカウントに譲渡できます。

Azure で定義されているロールの詳細については、「[従来のサブスクリプション管理者ロール、Azure RBAC ロール、および Azure AD 管理者ロール](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)」を参照してください。

## <a name="subscriptions-and-regions"></a>サブスクリプションとリージョン

各 Azure リソースは、1 つのサブスクリプションのみに論理的に関連付けられます。 リソースを作成するとき、そのリソースのデプロイ先となる Azure サブスクリプションを選択します。 リソースは、後で別のサブスクリプションに移動できます。

サブスクリプションは特定の Azure リージョンに関連付けられません。 一方、各 Azure リソースは 1 つのリージョンにだけデプロイされます。 複数のリージョンにあるリソースを同じサブスクリプションに関連付けることができます。

> [!NOTE]
> ほとんどの Azure リソースは特定のリージョンにデプロイされます。 ただし、Azure Policy サービスを使用して設定するポリシーなど、特定の種類のリソースは、グローバル リソースと見なされます。

## <a name="related-resources"></a>関連リソース

この記事で説明した概念の詳細情報は、次のリソースで参照できます。

- [Azure のしくみ](../../getting-started/what-is-azure.md)
- [Azure でのリソース アクセス管理](../../govern/resource-consistency/resource-access-management.md)
- [Azure リソース マネージャーの概要](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)
- [Azure リソースのロールベースのアクセス制御 (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview)
- [Azure Active Directory とは](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)
- [Azure サブスクリプションを Azure Active Directory テナントに関連付けるまたは追加する](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-how-subscriptions-associated-directory)
- [Azure AD Connect のトポロジ](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-topologies)
- [マイクロソフトのクラウド プランのサブスクリプション、ライセンス、アカウント、およびテナント](https://docs.microsoft.com/office365/enterprise/subscriptions-licenses-accounts-and-tenants-for-microsoft-cloud-offerings)

## <a name="next-steps"></a>次の手順

Azure の基本的な概念を理解したところで、[複数の Azure サブスクリプションで拡張する](../azure-best-practices/scaling-subscriptions.md)方法について見ていきましょう。

> [!div class="nextstepaction"]
> [複数の Azure サブスクリプションでの拡張](../azure-best-practices/scaling-subscriptions.md)
