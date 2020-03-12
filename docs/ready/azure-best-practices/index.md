---
title: Azure 対応性のベスト プラクティス
description: チームが Azure 環境を確立し、準備するためのベスト プラクティスと追加のガイダンスを提供する方法について説明します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 05/10/2019
ms.topic: guide
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.openlocfilehash: 8d44d3981824c9599151391cd3b7e3550ac31cd6
ms.sourcegitcommit: 959cb0f63e4fe2d01fec2b820b8237e98599d14f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2020
ms.locfileid: "79093571"
---
# <a name="best-practices-for-azure-readiness"></a>Azure 対応性のベスト プラクティス

クラウド準備の大部分は、クラウド導入の取り組みを開始して、クラウドに資産とワークロードを移すために移行先の環境を準備するために必要な技術スキルを職員に授けることで占められます。 チームが Azure 環境への準備を行うためのベスト プラクティスと追加のガイダンスについて説明します。

## <a name="azure-fundamentals"></a>Azure 基礎

Azure 環境でアセットを整理およびデプロイします。

- [Azure の基本的な概念](../considerations/fundamental-concepts.md)。 Azure の主要な概念と用語、およびこれらの概念の相互関連性について説明します。
- [推奨される名前付けおよびタグ付け規則](../azure-best-practices/naming-and-tagging.md)。 リソースに名前を付けてタグ付けするための詳細な推奨事項を確認します。 これらの推奨事項では、エンタープライズ クラウドの導入作業がサポートされます。
- [複数の Azure サブスクリプションによるスケーリング](../azure-best-practices/scaling-subscriptions.md)。 複数の Azure サブスクリプションによる拡張戦略を理解できます。
- [Azure 管理グループでリソースを整理する](https://docs.microsoft.com/azure/governance/management-groups/?toc=https://docs.microsoft.com/azure/cloud-adoption-framework/toc.json&bc=https://docs.microsoft.com/azure/cloud-adoption-framework/_bread/toc.json) Azure 管理グループで複数のサブスクリプションにまたがり、リソース、ロール、ポリシー、デプロイを管理する方法について説明します。
- [一貫性のあるハイブリッド クラウドの作成](../considerations/hybrid-consistency.md)。 オンプレミス管理のさまざまな利便性を維持しながら、クラウド イノベーションの利点を提供するハイブリッド クラウド ソリューションを作成します。

## <a name="networking"></a>ネットワーク

ワークロードをサポートするようにクラウド ネットワーク インフラストラクチャを準備します。

- [ネットワーク関連の意思決定](../considerations/networking-options.md)。 組織のワークロード、ガバナンス、接続要件を支援するネットワーク関連のサービス、ツール、アーキテクチャを選択します。
- [仮想ネットワークの計画](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm?toc=https://docs.microsoft.com/azure/cloud-adoption-framework/toc.json&bc=https://docs.microsoft.com/azure/cloud-adoption-framework/_bread/toc.json)。 分離、接続、場所の要件に基づいて仮想ネットワークを計画します。
- [ネットワーク セキュリティのベスト プラクティス](https://docs.microsoft.com/azure/security/azure-security-network-security-best-practices?toc=https://docs.microsoft.com/azure/cloud-adoption-framework/toc.json&bc=https://docs.microsoft.com/azure/cloud-adoption-framework/_bread/toc.json)。 Azure の組み込み機能を利用し、ネットワーク セキュリティの問題に対処するときのベスト プラクティスについて説明します。
- [境界ネットワーク](./perimeter-networks.md)。 クラウド ネットワークと物理的なオンプレミスのデータセンターのネットワークの間で安全な接続が可能になり、また、インターネットの双方向接続も可能になります。
- [ハブ アンド スポーク ネットワーク トポロジ](./hub-spoke-network-topology.md)。 複雑なワークロードの一般的な通信やセキュリティの要件を効率的に管理し、潜在的な Azure サブスクリプションの制限に対処します。

## <a name="identity-and-access-control"></a>ID とアクセスの制御

ワークロードのセキュリティと管理の効率性を改善するために、ID とアクセスの制御のインフラストラクチャを設計します。

- [Azure の ID 管理とアクセス制御セキュリティのベスト プラクティス](https://docs.microsoft.com/azure/security/azure-security-identity-management-best-practices?toc=https://docs.microsoft.com/azure/cloud-adoption-framework/toc.json&bc=https://docs.microsoft.com/azure/cloud-adoption-framework/_bread/toc.json)。 Azure の組み込み機能を利用した ID 管理とアクセス制御に関するベスト プラクティスについて説明します。
- [ロールベースのアクセス制御のベスト プラクティス](../considerations/roles.md)。 ユーザー ロールごとに整理されているリソースのグループベース アクセスを詳細に管理できます。
- [Azure Active Directory でのハイブリッドおよびクラウド デプロイ用の特権アクセスをセキュリティで保護する](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-admin-roles-secure?toc=https://docs.microsoft.com/azure/cloud-adoption-framework/toc.json&bc=https://docs.microsoft.com/azure/cloud-adoption-framework/_bread/toc.json)。 クラウドとオンプレミスの環境全体で組織の管理者アクセスと特権アカウントをセキュリティで保護します。

## <a name="storage"></a>ストレージ

- [Azure Storage ガイダンス](../considerations/storage-options.md)。 使用シナリオをサポートする最適な Azure Storage ソリューションを選択します。
- [Azure Storage のセキュリティ ガイド](https://docs.microsoft.com/azure/storage/blobs/security-recommendations?toc=https://docs.microsoft.com/azure/cloud-adoption-framework/toc.json&bc=https://docs.microsoft.com/azure/cloud-adoption-framework/_bread/toc.json)。 Azure Storage でのセキュリティ機能について説明します。

## <a name="databases"></a>データベース

- [Azure で適切な SQL Server オプションを選択する](https://docs.microsoft.com/azure/sql-database/sql-database-paas-vs-sql-server-iaas?toc=https://docs.microsoft.com/azure/cloud-adoption-framework/toc.json&bc=https://docs.microsoft.com/azure/cloud-adoption-framework/_bread/toc.json)。 SQL Server ワークロードを最も効果的に支える PaaS または IaaS ソリューションを選択します。
- [データベース セキュリティのベスト プラクティス](https://docs.microsoft.com/azure/security/azure-database-security-best-practices?toc=https://docs.microsoft.com/azure/cloud-adoption-framework/toc.json&bc=https://docs.microsoft.com/azure/cloud-adoption-framework/_bread/toc.json)。 Azure プラットフォーム上のデータベース セキュリティのベスト プラクティスについて説明します。
- [適切なデータ ストアの選択](https://docs.microsoft.com/azure/architecture/guide/technology-choices/data-store-overview)。 要件を満たす適切なデータストアを選択します。 SQL および NoSQL データベースから、何百もの実装を選択できます。 データ ストアは、多くの場合、データの構造とサポートする操作の種類によって分類されます。 この記事では、一般的ないくつかのストレージ モデルについて説明します。

## <a name="cost-management"></a>コスト管理

- [事業単位、環境、プロジェクトをまたいでコストを追跡する](./track-costs.md)。 正しいコスト追跡メカニズムを構築するためのベスト プラクティスについて説明します。
- [Azure Cost Management でクラウド投資を最適化する方法](https://docs.microsoft.com/azure/cost-management-billing/costs/cost-mgt-best-practices?toc=https://docs.microsoft.com/azure/cloud-adoption-framework/toc.json&bc=https://docs.microsoft.com/azure/cloud-adoption-framework/_bread/toc.json)。 コスト管理戦略を実装し、コスト関連の問題に対処するためのツールについて学習します。
- [予算の作成と管理](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-acm-create-budgets?toc=https://docs.microsoft.com/azure/cloud-adoption-framework/toc.json&bc=https://docs.microsoft.com/azure/cloud-adoption-framework/_bread/toc.json)。 Azure Cost Management を使用して予算を作成して管理する方法を説明します。
- [コスト データのエクスポート](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data?toc=https://docs.microsoft.com/azure/cloud-adoption-framework/toc.json&bc=https://docs.microsoft.com/azure/cloud-adoption-framework/_bread/toc.json)。 Azure Cost Management を使用してコスト データをエクスポートする方法について説明します。
- [推奨に基づくコストの最適化](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-acm-opt-recommendations?toc=https://docs.microsoft.com/azure/cloud-adoption-framework/toc.json&bc=https://docs.microsoft.com/azure/cloud-adoption-framework/_bread/toc.json)。 Azure Cost Management と Azure Advisor を使用して、十分活用されていないリソースを特定し、コストを減らす方法について説明します。
- [コスト アラートを使用し、使用状況と支出を監視する](https://docs.microsoft.com/azure/cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending?toc=https://docs.microsoft.com/azure/cloud-adoption-framework/toc.json&bc=https://docs.microsoft.com/azure/cloud-adoption-framework/_bread/toc.json)。 Cost Management アラートを使用し、Azure の使用状況と支出を監視する方法について説明します。
