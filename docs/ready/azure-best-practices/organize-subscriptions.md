---
title: 複数の Azure サブスクリプションの整理と管理
description: Azure のクラウド導入フレームワークを使用して、管理グループ階層を作成してサブスクリプションとリソースの管理を簡素化について検討します。
author: alexbuckgit
ms.author: abuck
ms.date: 05/20/2019
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.openlocfilehash: 0e9115387360b46015114d6069fcedb9afb43d58
ms.sourcegitcommit: 568037e0d2996e4644c11eb61f96362a402759ec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/16/2020
ms.locfileid: "84799655"
---
# <a name="organize-and-manage-multiple-azure-subscriptions"></a>複数の Azure サブスクリプションの整理と管理

サブスクリプションの数がわずかであれば、比較的簡単に個別管理できます。 しかし、サブスクリプションの数が多い場合は、管理グループ階層を作成してサブスクリプションとリソースを管理できるようにします。

## <a name="azure-management-groups"></a>Azure 管理グループ

Azure 管理グループを使用すると、組織のサブスクリプションのアクセス、ポリシー、コンプライアンスを効率よく管理できます。 各管理グループは、1 つ以上のサブスクリプションのコンテナーです。

管理グループは 1 つの階層に配置されます。 組織の構造やニーズに合わせるために、この階層は Azure Active Directory (Azure AD) テナントで定義します。 最上位のレベルは_ルート管理グループ_と呼ばれます。 階層には、最大 6 レベルの管理グループを定義できます。 各サブスクリプションは 1 つの管理グループだけに属します。

Azure には、次の 4 つのレベルの管理スコープがあります。

- 管理グループ
- サブスクリプション
- リソース グループ
- リソース

階層内のあるレベルに適用されているアクセス権やポリシーは、その下のレベルに継承されます。 リソース所有者またはサブスクリプション所有者は、継承されたポリシーを変更できません。 この制限によりガバナンスを向上させることができます。

> [!NOTE]
> タグの継承はまだサポートされていませんが、まもなく使用可能になる予定です。

この継承モデルを使用すると、各サブスクリプションが適切なポリシーやセキュリティ コントロールに準拠するように、階層内にサブスクリプションを配置できます。

![Azure リソースを整理するための 4 つのスコープ レベル](../../ready/azure-setup-guide/media/organize-resources/scope-levels.png)

ルート管理グループに割り当てられたアクセス権やポリシーは、ディレクトリ内のすべてのリソースに適用されます。 このスコープで定義する項目は慎重に検討してください。 割り当ては必要なものだけを含めてください。

## <a name="create-your-management-group-hierarchy"></a>管理グループ階層を作成する

管理グループ階層を定義するとき、まず、ルート管理グループを作成します。 次に、ディレクトリ内の既存のすべてのサブスクリプションをルート管理グループに移動します。 新しいサブスクリプションは常にルート管理グループで作成されます。 後で別の管理グループに移動できます。

サブスクリプションを既存の管理グループに移動すると、それより上位の管理グループ階層からポリシーとロールの割り当てが継承されます。 Azure ワークロード用に複数のサブスクリプションを作成したら、他のサブスクリプションと共有される Azure サービスを格納する追加のサブスクリプションを作成できます。

Azure 環境が拡張されることが予想される場合は、運用環境と非運用環境用の管理グループをここで作成し、管理グループ レベルで適切なポリシーとアクセス制御を適用する必要があります。 新しいサブスクリプションが各管理グループに追加されるときには、適切な制御が継承されます。

![管理グループ階層の例](../../_images/ready/management-group-hierarchy-v2.png)

## <a name="example-use-cases"></a>ユース ケースの例

管理グループを使用して、さまざまなワークロードを区別する基本的な例には、次のようなものがります。

- **運用環境と非運用環境のワークロード:** 管理グループを使用して、運用環境と非運用環境のサブスクリプション間でさまざまなロールとポリシーをより簡単に管理します。 たとえば、非運用サブスクリプションでは開発者に共同作成者のアクセス許可を付与し、運用環境では開発者に閲覧者のアクセス許可のみを付与できます。
- **内部サービスと外部サービス:** 企業では、多くの場合、内部サービスと外部の顧客向けサービスで要件、ポリシー、ロールが異なります。

## <a name="related-resources"></a>関連リソース

Azure リソースの整理と管理の詳細については、次のリソースを確認してください。

- [Azure 管理グループでリソースを整理する](https://docs.microsoft.com/azure/governance/management-groups)
- [Azure のすべてのサブスクリプションと管理グループを管理する目的でアクセス権限を昇格させる](https://docs.microsoft.com/azure/role-based-access-control/elevate-access-global-admin)
- [Azure リソースを別のリソース グループやサブスクリプションに移動する](https://docs.microsoft.com/azure/azure-resource-manager/management/move-resource-group-and-subscription)

## <a name="next-steps"></a>次のステップ

Azure リソースをデプロイするときに従う[推奨される名前付けとタグ付けの規則](./naming-and-tagging.md)を確認します。

> [!div class="nextstepaction"]
> [推奨される名前付けおよびタグ付け規則](./naming-and-tagging.md)
