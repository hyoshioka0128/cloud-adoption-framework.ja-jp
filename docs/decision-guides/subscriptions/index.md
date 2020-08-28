---
title: サブスクリプション決定ガイド
description: Azure 資産を整理するためのサブスクリプションの設計戦略と管理グループ階層について説明します。
author: alexbuckgit
ms.author: abuck
ms.date: 10/17/2019
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: decision-guide
ms.custom: governance
ms.openlocfilehash: 4312329fa5747bfaa2ad12de308f156a6aa74e99
ms.sourcegitcommit: 07d56209d56ee199dd148dbac59671cbb57880c0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88881531"
---
# <a name="subscription-decision-guide"></a>サブスクリプション決定ガイド

有効なサブスクリプション設計によって、組織はクラウド導入時に Azure の資産を整理および管理するための構造を確立できます。 このガイドでは、追加のサブスクリプションを作成するタイミングを決定し、ビジネスの優先事項をサポートするために管理グループ階層を拡張する方法について説明します。

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションを作成して、それをアカウントに関連付け、仮想マシンやデータベースなどのリソースをサブスクリプションにデプロイすることで、Azure の導入が開始されます。 これらの概念の概要については、「[Azure fundamental concepts](../../ready/considerations/fundamental-concepts.md)」 (Azure の基本概念) を参照してください。

- [初期サブスクリプションを作成する。](../../ready/azure-best-practices/initial-subscriptions.md)
- [追加のサブスクリプションを作成](../../ready/azure-best-practices/scale-subscriptions.md)して、Azure 環境をスケーリングする。
- Azure 管理グループを使用して[サブスクリプションを整理および管理](../../ready/azure-best-practices/organize-subscriptions.md)する。

## <a name="model-your-organization"></a>組織をモデル化する

すべての組織が異なるため、Azure 管理グループは柔軟になるように設計されています。 組織の階層を反映するためにクラウド資産をモデル化すると、階層のより高いレベルでポリシーを定義して適用することができます。また、継承に依存して、これらのポリシーが階層のより低い管理グループに自動的に適用されることを確認します。 サブスクリプションはさまざまな管理グループ間で移動できますが、予測される組織のニーズを反映する初期管理グループの階層を設計するのに役立ちます。

サブスクリプションの設計を最終的に決定する前に、[リソースの整合性](../resource-consistency/index.md)に関する考慮事項が設計の選択に与える可能性がある影響についても検討してください。

> [!NOTE]
> Azure Enterprise Agreement (EA) では、支払い目的で別の組織階層を定義できます。 この階層は、リソースに適切なポリシーとアクセス コントロールを簡単に適用するために、継承モデルの提供に重点を置く、管理グループの階層とは異なります。

## <a name="subscription-design-strategies"></a>サブスクリプションの設計戦略

ビジネスの優先事項に対処するために、次のサブスクリプションの設計戦略を検討してください。

### <a name="workload-separation-strategy"></a>ワークロード分離戦略

組織が新しいワークロードをクラウドに追加するときに、サブスクリプションのさまざまな所有権または職責の基本的な分離は、運用と非運用の管理グループの両方で複数のサブスクリプションになる可能性があります。 このアプローチでワークロードの分離が提供されますが、サブスクリプションのサブセット全体でポリシーを自動的に適用するために、継承モデルを大きく活用することはありません。

![ワークロード分離戦略](../../_images/ready/management-group-hierarchy-v2.png)

### <a name="application-category-strategy"></a>アプリケーション カテゴリ戦略

組織のクラウドの占有領域が増加すると、追加のサブスクリプションが、一般に、ビジネスの重要度、コンプライアンスの要件、アクセス制御、またはデータ保護のニーズが根本的に異なるアプリケーションをサポートするために作成されます。 初期の運用と非運用のサブスクリプションから構築すると、これらのアプリケーション カテゴリをサポートするサブスクリプションは、該当する場合は、運用と非運用のいずれかの管理グループの下で整理されます。 通常、これらのサブスクリプションは、中央の IT チームの運用スタッフによって所有および管理されます。

![アプリケーション カテゴリ戦略](../../_images\decision-guides\decision-guide-subscriptions-hierarchy.png)

組織ごとにアプリケーションの分類が異なり、多くの場合、特定のアプリケーションやサービスに基づいて、またはアプリケーションのアーキタイプの系統に沿って、サブスクリプションが分けられます。 この分類は、多くの場合、サブスクリプションのリソース制限の大部分を消費する可能性があるワークロードをサポートするように、またはミッション クリティカルなワークロードを分けて、これらの制限の下にある他のワークロードと競合しないように設計されています。 個別サブスクリプションを正当化する可能性のあるワークロードには、次のようなものがあります。

- ミッションクリティカルなワークロード。
- 社内の売却済商品の原価 (COGS) の一部であるアプリケーション。 たとえば、ある会社により製造されるすべてのウィジェットには、テレメトリを送信する Azure IoT モジュールが含まれています。 そのため、COGS の一部として、会計またはガバナンスの目的で専用のサブスクリプションが必要になる場合があります。
- HIPAA、FedRAMP など、規制の要件の対象となるアプリケーション。

### <a name="functional-strategy"></a>機能戦略

機能戦略では、管理グループの階層を使用し、財務、営業、または IT サポートなどの機能ラインに従ってサブスクリプションとアカウントが整理されます。

### <a name="business-unit-strategy"></a>事業単位戦略

この事業単位戦略では、管理グループの階層を使用し、損益カテゴリ、部署、部門、利益センター、または同様のビジネス構造に基づいて、サブスクリプションとアカウントがグループ化されます。

### <a name="geographic-strategy"></a>地理的戦略

国際的な組織では、この地理的戦略により、管理グループの階層を使用し、地理的な地域に基づいてサブスクリプションとアカウントをグループ化します。

## <a name="mix-subscription-strategies"></a>サブスクリプション戦略を組みわせる

管理グループの階層は、最大レベル 6 の深さになることがあります。 これにより、組織のニーズを満たすように、これらのいくつかの戦略を組み合わせた階層を作成するための柔軟性が提供されます。 たとえば、以下の図では、事業単位戦略を地理的戦略と組み合わせる組織階層を示しています。

![混合サブスクリプション戦略](../../_images\decision-guides\decision-guide-subscriptions-hierarchy-mixed.png)

## <a name="related-resources"></a>関連リソース

- [Azure でのリソース アクセス管理](../../govern/resource-consistency/resource-access-management.md)
- [大企業での複数レイヤーのガバナンス](../../govern/guides/complex/multiple-layers-of-governance.md)
- [複数の地理的リージョン](../../migrate/azure-best-practices/multiple-regions.md)

## <a name="next-steps"></a>次のステップ

サブスクリプションの設計は、クラウド導入プロセス時のアーキテクチャの決定で必要なコア インストラクチャ コンポーネントの 1 つにすぎません。 アーキテクチャの決定ガイドの概要を参照して、他の種類のインフラストラクチャの設計に関する決定を行うときに使用されるその他の戦略を確認してください。

> [!div class="nextstepaction"]
> [アーキテクチャの意思決定ガイド](../index.md)
