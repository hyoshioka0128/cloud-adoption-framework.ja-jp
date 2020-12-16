---
title: 事業単位、環境、プロジェクトをまたいでコストを追跡する
description: Azure 向けのクラウド導入フレームワークを使用して、追跡メカニズムを作成するための意思決定と実装の方法を理解します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 09/05/2019
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.custom: internal
ms.openlocfilehash: 74943f7dae0e71807da5cfcd449f79e1fb1cb055
ms.sourcegitcommit: b6f2b4f8db6c3b1157299ece1f044cff56895919
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2020
ms.locfileid: "97026117"
---
# <a name="track-costs-across-business-units-environments-or-projects"></a>事業単位、環境、プロジェクトをまたいでコストを追跡する

[コスト志向の組織を構築する](../../organize/cost-conscious-organization.md)には、コスト関連データに対する可視性および適切に定義されたアクセス権 (スコープ) が必要です。 このベスト プラクティスに関する記事では、追跡メカニズムを作成するための意思決定と実装の方法について概説します。

![コスト志向のプロセスの概要](../../_images/ready/cost-optimization-process.png)
_図 1: コスト志向のプロセスの概要。_

## <a name="establish-a-well-managed-environment-hierarchy"></a>適切に管理された環境の階層の確立

コスト管理は、ガバナンスやその他の管理コンストラクトのように、適切に管理された環境に依存します。 このような環境 (特に複雑な環境) を確立するには、全資産の分類および編成で一貫性のあるプロセスが必要です。

資産 (リソースとも呼ばれます) には、すべての仮想マシン、データ ソース、およびクラウドにデプロイされたアプリケーションが含まれます。 Azure では、資産を分類および編成するためのさまざまなメカニズムが用意されています。 [Azure サブスクリプションの整理と管理](../azure-best-practices/organize-subscriptions.md)に関するページでは、複数の条件に基づいてリソースを整理し、適切に管理された環境を確立するためのオプションについて詳しく説明しています。 この記事では、クラウド コストの可視性を提供するために Azure の基本的な概念を適用することに焦点を当てています。

### <a name="classification"></a>分類

"_タグ付け_" は、アセットを分類する簡単な方法です。 タグ付けにより、資産にメタデータが関連付けられます。 このメタデータを使用して、さまざまなデータ ポイントに基づいて資産を分類できます。 企業がコスト管理の取り組みの一環としてタグを使って資産を分類する場合、事業単位、部署、請求コード、地域、環境、プロジェクト、ワークロード、"アプリケーション分類" などのタグが必要であることが多いです。 Azure Cost Management + Billing では、これらのタグを使用してコスト データのさまざまなビューを作成できます。

タグ付けは、あらゆるコスト レポートのデータを理解するための主要な方法です。 これは、適切に管理された環境の基本要素です。 また、環境の適切なガバナンスを確立する最初の手順でもあります。

事業単位、環境、およびプロジェクトをまたいでコスト情報を正確に追跡する最初の手順は、タグ付けの標準を定義することです。 2 番目の手順は、タグ付けの標準が確実に適用されていることを確認することです。 次の記事は、[名前付けとタグ付けの標準の作成](../azure-best-practices/naming-and-tagging.md)および[タグ付けの標準を適用するガバナンス MVP の確立](../../govern/guides/complex/index.md)に役立ちます。

### <a name="resource-organization"></a>リソースの編成

資産の編成にはいくつかのアプローチがあります。 このセクションでは、事業単位、地域、および IT 組織に分散されたコスト構造を持つ大企業のニーズに基づいたベスト プラクティスについて概説します。 小規模であまり複雑ではない組織向けの同様のベスト プラクティスについては、「[標準的な企業のガバナンス ガイド](../../govern/guides/standard/index.md)」を参照してください。

大企業の場合は、次のモデルのように、管理グループ、サブスクリプション、およびリソース グループの階層を作成して、必要な職務を遂行できる適切な可視性レベルを各チームに提供できます。 企業で予算超過を防ぐためにコスト管理が必要な場合は、この構造内のサブスクリプションに Azure Blueprints や Azure Policy などのガバナンス ツールを適用して、将来のコスト エラーを迅速に食い止めることができます。

![大企業のリソース編成の図](../../_images/govern/large-enterprise-resource-organization.png)
_図 2: 大企業のリソース編成。_

上の図では、管理グループ階層のルートに各事業単位のノードがあります。 この例の多国籍企業では、地域の事業単位に可視性が必要であるため、階層内の各事業単位の下に地域のノードが作成されています。

各地域内には、コスト、アクセス、およびガバナンスのコントロールを分離するために、運用環境用と非運用環境用の別々のノードがあります。 より効率的な運用とより賢明な投資を可能にするために、企業はサブスクリプションを使用して、運用実績への貢献度に応じて運用環境をさらに分離しています。 最後に、企業はリソース グループを使用して、アプリケーションと呼ばれるデプロイ可能な機能のユニットをキャプチャします。

この図はベスト プラクティスを示していますが、次のオプションは含まれていません。

- 多くの企業は、運用拠点を単一の地政学的リージョンに限定しています。 このアプローチにより、各地域のデータ主権の要件に基づいてガバナンス規範やコスト データを多様化させる必要が減ります。 このような場合、地域ノードは必要ありません。
- 開発、テスト、および品質制御の環境を個別のサブスクリプションにさらに分離したいと考える企業もあります。
- 企業がクラウドのセンター オブ エクセレンス (CCoE) チームを統合すると、各地域ノードで共有されるサービス サブスクリプションにより、資産の重複を削減することができます。
- 導入時の小さな労力で、管理階層をさらに縮小できる可能性があります。 さまざまな環境の階層で、企業 IT の単一のルート ノードの下に単一レベルの下位ノードがあることはめずらしくありません。 これは、適切に管理された環境のベスト プラクティスに反するものではありません。 しかし、コスト管理や他の重要な機能に最小権限アクセス モデルを提供することがより難しくなります。

この記事の残りの部分では、上の図のベスト プラクティスのアプローチを使用することを前提としています。 しかし、次の記事は、お客様の会社に最適なリソース編成にこのアプローチを適用するのに役立ちます。

- [複数のサブスクリプションを使用して、Azure 環境をスケーリングする](../azure-best-practices/scale-subscriptions.md)
- [Azure サブスクリプションを整理および管理する](../azure-best-practices/organize-subscriptions.md)
- [ガバナンス MVP をデプロイして適切に管理された環境の標準を管理する](../../govern/guides/complex/index.md)

## <a name="provide-the-right-level-of-cost-access"></a>適切なレベルのコスト アクセスの提供

コストの管理はチームのアクティビティです。 クラウド導入フレームワークの組織の準備に関するセクションでは、少人数のコア チームを定義し、それらのチームによってサポートされるクラウド導入作業について説明しています。 この記事では、チームの定義について詳しく説明し、各チームにコスト管理データに対する適切なレベルの可視性が提供されるように、それぞれのメンバーに割り当てるスコープとロールを定義します。

"**ロール**" では、さまざまな資産に対してユーザーが実行できることを定義します。 "**スコープ**" では、ユーザーがこれらのことを実行できる資産 (ユーザー、グループ、サービス プリンシパル、またはマネージド ID) を定義します。

一般的なベスト プラクティスとしては、さまざまなロールやスコープにユーザーを割り当てる、最小特権モデルをお勧めします。

### <a name="roles"></a>ロール

<!-- docutune:casing Owner Contributor Reader -->

Azure Cost Management + Billing では、各スコープに対して次の組み込みロールがサポートされています。

- [所有者](/azure/role-based-access-control/built-in-roles#owner):コストを表示し、コストの構成を含めたすべてを管理することができます。
- [共同作成者](/azure/role-based-access-control/built-in-roles#contributor):コストを表示し、コストの構成を含めたすべてを管理できますが、アクセスの制御はできません。
- [閲覧者](/azure/role-based-access-control/built-in-roles#reader):コストのデータと構成を含めたすべてを表示できますが、変更を加えることはできません。
- [Cost Management 共同作成者](/azure/role-based-access-control/built-in-roles#cost-management-contributor): コストを表示し、コストの構成を管理することができます。
- [Cost Management 閲覧者](/azure/role-based-access-control/built-in-roles#cost-management-reader): コストのデータと構成を表示することができます。

一般的なベスト プラクティスとしては、すべてのチームのメンバーに Cost Management 共同作成者のロールを割り当てます。 このロールでは、予算を作成および管理し、コストをより効果的に監視してレポートできるようにエクスポートするためのアクセス権が付与されます。 しかし、[クラウド戦略チーム](../../organize/cloud-strategy.md)のメンバーは、Cost Management 閲覧者のみに設定する必要があります。 Azure Cost Management + Billing ツール内での予算の設定に関与しないためです。

### <a name="scope"></a>Scope

次のスコープとロールの設定により、コスト管理に必要な可視性が作成されます。 このベスト プラクティスは、資産の編成方法に応じて若干の変更が必要になることがあります。

- [クラウド導入チーム](../../organize/cloud-adoption.md)。 最適化に向けた継続的な変更を行う職務には、リソース グループ レベルで Cost Management 共同作成者のアクセス権が必要です。

  - **作業環境**。 クラウド導入チームは、少なくとも、影響を受けるすべてのリソース グループか、開発/テストまたは継続的なデプロイ アクティビティに関連するこれらのグループに対する[共同作成者](/azure/role-based-access-control/built-in-roles#contributor)のアクセス権を既に持っているはずです。 追加のスコープ設定は必要ありません。
  - **運用環境**。 責任の適切な分離が確立されたら、クラウド導入チームが自分たちのプロジェクトに関連するリソース グループに対するアクセス権を持ち続けることはないでしょう。 このチームに対しては、ワークロードの運用インスタンスをサポートするリソース グループに追加のスコープを設定し、チームの決定の運用コストへの影響を可視化することが必要です。 このチームの運用リソース グループに [Cost Management 共同作成者](/azure/role-based-access-control/built-in-roles#cost-management-contributor)スコープを設定すると、チームはコストを監視し、サポートされているワークロードでの使用状況と継続的な投資に基づいて予算を設定することができます。

- [クラウド戦略チーム](../../organize/cloud-strategy.md)。 複数のプロジェクトや事業単位をまたいでコストを追跡する職務には、管理グループ階層のルート レベルで Cost Management 閲覧者のアクセス権が必要です。

  - 管理グループでこのチームに [Cost Management 閲覧者](/azure/role-based-access-control/built-in-roles#cost-management-reader)のアクセス権を割り当てます。 これにより、その管理グループ階層によって管理されるサブスクリプションに関連付けられているすべてのデプロイに対する継続的な可視性が保証されます。

- [クラウド ガバナンス チーム](../../organize/cloud-governance.md)。 導入作業全体でコスト管理、予算調整、レポートを行う職務には、管理グループ階層のルート レベルで Cost Management 共同作成者のアクセス権が必要です。

  - 適切に管理された環境では、クラウド ガバナンス チームが高いレベルのアクセス権を持っている可能性があるため、[Cost Management 共同作成者](/azure/role-based-access-control/built-in-roles#cost-management-contributor)の追加のスコープ割り当ては必要ありません。

<!-- cSpell:ignore automations -->

- [クラウドのセンター オブ エクセレンス](../../organize/cloud-center-of-excellence.md)。 共有サービスに関連するコストを管理する職務には、サブスクリプション レベルで Cost Management 共同作成者のアクセス権が必要です。 さらに、このチームには、CCoE オートメーションがコストに与える影響を理解するために、それらのオートメーションによってデプロイされたアセットを含むリソース グループまたはサブスクリプションに対する Cost Management 共同作成者のアクセス権も必要になることがあります。

  - **共有サービス**。 クラウドのセンター オブ エクセレンスが関与している場合、ベスト プラクティスとして、CCoE によって管理される資産が、ハブ アンド スポーク モデル内の一元的な共有サービス サブスクリプションからサポートされることをお勧めします。 このシナリオでは、CCoE はそのサブスクリプションに対する共同作成者または所有者のアクセス権を持っている可能性があるため、[Cost Management 共同作成者](/azure/role-based-access-control/built-in-roles#cost-management-contributor)の追加のスコープ割り当ては必要ありません。
  - **CCoE オートメーションまたはコントロール**。 CCoE では一般に、コントロールおよび自動化されたデプロイ スクリプトがクラウド導入チームに提供されます。 CCoE には、これらのアクセラレータがコストに与える影響を理解する責任があります。 これを可視化するため、チームには、それらのアクセラレータを実行しているすべてのリソース グループまたはサブスクリプションに対する [Cost Management 共同作成者](/azure/role-based-access-control/built-in-roles#cost-management-contributor)のアクセス権が必要です。

- **クラウド運用チーム**。 運用環境の継続的コストを管理する職務には、運用環境のすべてのサブスクリプションに対する Cost Management 共同作成者のアクセス権が必要です。

  - 一般的に推奨されることとして、運用資産と非運用資産は、運用環境に関連付けられている管理グループ階層のノードによって管理されている別々のサブスクリプションに配置します。 適切に管理された環境では、運用チームのメンバーは運用環境のサブスクリプションに対する所有者または共同作成者のアクセス権を既に持っている可能性があるため、[Cost Management 共同作成者](/azure/role-based-access-control/built-in-roles#cost-management-contributor)ロールは必要ありません。

## <a name="additional-cost-management-resources"></a>コスト管理に関するその他のリソース

Azure Cost Management + Billing は、予算を設定し、Azure または AWS のクラウド コストを可視化するための定評あるツールです。 適切に管理された環境の階層への適切なアクセス権を確立した後、このツールを使用してコストを監視および管理するとき、次の記事が役立つ可能性があります。

### <a name="get-started-with-azure-cost-management--billing"></a>Azure Cost Management + Billing の使用を開始する

Azure Cost Management + Billing の使用を開始するには、[Azure Cost Management + Billing を使用してクラウドへの投資を最適化する方法](/azure/cost-management-billing/costs/cost-mgt-best-practices?toc=/azure/cloud-adoption-framework/toc.json&bc=/azure/cloud-adoption-framework/_bread/toc.json)に関する記事をご覧ください。

### <a name="use-azure-cost-management--billing"></a>Azure Cost Management + Billing を使用する

- [予算を作成して管理する](/azure/cost-management-billing/costs/tutorial-acm-create-budgets)
- [コスト データのエクスポート](/azure/cost-management-billing/costs/tutorial-export-acm-data)
- [推奨に基づくコストの最適化](/azure/cost-management-billing/costs/tutorial-acm-opt-recommendations)
- [コスト アラートを使用し、使用状況と支出を監視する](/azure/cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending)

<!-- docutune: "AWS Cost and Usage" -->

### <a name="use-azure-cost-management--billing-to-govern-aws-costs"></a>Azure Cost Management + Billing を使用して AWS のコストを管理する

- [AWS のコストと使用状況レポートの統合を設定する](/azure/cost-management-billing/costs/aws-integration-set-up-configure)
- [AWS のコストの管理](/azure/cost-management/aws-integration-manage)

### <a name="establish-access-roles-and-scope"></a>アクセス、ロール、スコープを設定する

- [コスト管理のスコープについて](/azure/cost-management/understand-work-scopes)
- [リソース グループのスコープの設定](/azure/role-based-access-control/quickstart-assign-role-user-portal)
