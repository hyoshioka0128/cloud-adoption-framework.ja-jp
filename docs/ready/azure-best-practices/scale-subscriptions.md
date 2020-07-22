---
title: 追加のサブスクリプションを作成して、Azure 環境をスケーリングする
description: Azure のクラウド導入フレームワークを使用して、複数の Azure サブスクリプションを使用して環境をスケーリングするための戦略を策定する方法を学習します。
author: alexbuckgit
ms.author: abuck
ms.date: 05/20/2019
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.openlocfilehash: ec2cac2978047a95ef2199933286ec98e2e6d63f
ms.sourcegitcommit: 71a4f33546443d8c875265ac8fbaf3ab24ae8ab4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2020
ms.locfileid: "86479842"
---
# <a name="create-additional-subscriptions-to-scale-your-azure-environment"></a>追加のサブスクリプションを作成して、Azure 環境をスケーリングする

多くの場合、組織では複数の Azure サブスクリプションを使用して、サブスクリプションごとのリソース制限を回避し、Azure リソースの管理と制御を強化します。 サブスクリプションをスケーリングするための戦略を立てておくことが重要です。

## <a name="review-fundamental-concepts"></a>基本的な概念を確認する

[初期サブスクリプション](./initial-subscriptions.md)を超えて Azure 環境を拡張する場合、アカウント、テナント、ディレクトリ、サブディレクトリなどの Azure の概念を理解しておくことが重要です。 詳細については、「[Azure 基礎の概念](../considerations/fundamental-concepts.md)」を参照してください。

他の考慮事項により、追加のサブスクリプションが必要になる場合があります。 クラウド資産を拡張するときは、次の点に留意してください。

## <a name="technical-considerations"></a>技術的な考慮事項

**サブスクリプションの制限**: サブスクリプションでは、一部のリソースの種類に対して制限が定義されています。 たとえば、あるサブスクリプションでは仮想ネットワークの数に制限があります。 サブスクリプションがこれらの制限に近づいた場合は、別のサブスクリプションを作成し、そこに追加のリソースを置く必要があります。 詳細については、[Azure サブスクリプションとサービスの制限](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#general-limits) に関する記事を参照してください。

**クラシック モデルのリソース:** Azure を長期間使用している場合は、クラシック デプロイ モデルを使用して作成されたリソースが存在する可能性があります。 Azure のポリシー、ロールベースのアクセス制御、リソースのグループ化、およびタグは、クラシック モデルのリソースには適用できません。 これらのリソースは、クラシック モデルのリソースのみを含むサブスクリプションに移動する必要があります。

**コスト:** サブスクリプション間のデータのイングレスとエグレスに対して追加コストが発生することがあります。

## <a name="business-priorities"></a>ビジネスの優先順位

ビジネスの優先順位によって、追加のサブスクリプションを作成する場合があります。 次のような優先順位が考えられます。

- 革新
- 移行
- コスト
- 操作
- Security
- ガバナンス

サブスクリプションのスケーリングに関するその他の考慮事項については、クラウド導入フレームワークの「[サブスクリプション決定ガイド](../../decision-guides/subscriptions/index.md)」を参照してください。

## <a name="moving-resources-between-subscriptions"></a>サブスクリプション間でのリソースの移動

サブスクリプション モデルが大きくなると、一部のリソースを他のサブスクリプションに所属させることを決定する場合があります。 多くの種類のリソースをサブスクリプション間で移動できます。 また、自動デプロイを使用して、別のサブスクリプションでリソースを再作成することもできます。 詳細については、[別のリソース グループまたはサブスクリプションへの Azure リソースの移動](https://docs.microsoft.com/azure/azure-resource-manager/management/move-resource-group-and-subscription)に関する記事をご覧ください。

## <a name="tips-for-creating-new-subscriptions"></a>新しいサブスクリプションを作成するためのヒント

- サブスクリプションの新規作成の担当者を特定します。
- サブスクリプションで既定で使用可能なリソースの種類を決定します。
- すべての標準サブスクリプションの外観を決定します。 RBAC アクセス、ポリシー、タグ、インフラストラクチャ リソースなどの考慮事項があります。
- 可能であれば、サービス プリンシパルを使用して[プログラムによって新しいサブスクリプションを作成します](https://docs.microsoft.com/azure/azure-resource-manager/management/programmatically-create-subscription)。 サブスクリプションを作成するには、[サービス プリンシパルにアクセス許可を付与する](https://docs.microsoft.com/azure/azure-resource-manager/grant-access-to-create-subscription)必要があります。 自動化されたワークフローを通じて新しいサブスクリプションを要求できるセキュリティ グループを定義します。
- エンタープライズ契約 (EA) をご利用のお客様は、組織で非 EA サブスクリプションの作成がブロックされるように Azure サポートにご依頼ください。

## <a name="next-steps"></a>次のステップ

管理グループ階層を作成すると、[サブスクリプションとリソースを整理および管理](./organize-subscriptions.md)できます。

> [!div class="nextstepaction"]
> [サブスクリプションとリソースの整理と管理](./organize-subscriptions.md)
