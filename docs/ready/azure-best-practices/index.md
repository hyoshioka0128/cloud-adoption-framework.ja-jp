---
title: Azure 対応性ベスト プラクティス
titleSuffix: Microsoft Cloud Adoption Framework for Azure
description: Azure 対応性のベスト プラクティスの概要
author: BrianBlanchard
ms.author: brblanch
ms.date: 05/10/2019
ms.topic: guide
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.openlocfilehash: 67b65affee2a2ac351ab603a52b0b6202d41458f
ms.sourcegitcommit: 35c162d2d09ec1c4a57d3d57a5db1d56ee883806
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/17/2019
ms.locfileid: "72548992"
---
# <a name="best-practices-for-azure-readiness"></a>Azure 対応性のベスト プラクティス

クラウド準備の大部分は、クラウド導入の取り組みを開始して、クラウドに資産とワークロードを移すために移行先の環境を準備するために必要な技術スキルを職員に授けることで占められます。 次のトピックでは、チームが Azure 環境を確立し、準備するためのベスト プラクティスと追加のガイダンスを提供します。

## <a name="azure-fundamentals"></a>Azure 基礎

Azure 環境で資産を整理し、デプロイするとき、次のガイダンスを利用できます。

- [Azure の基本的な概念](../considerations/fundamental-concepts.md)。 Azure で使用される基本的な概念と用語について説明します。 これらの概念の相互関連性についても説明します。
- [推奨される名前付けおよびタグ付け規則](../considerations/naming-and-tagging.md)。 リソースに名前を付けてタグ付けするための詳細な推奨事項を確認します。 これらの推奨事項では、エンタープライズ クラウドの導入作業がサポートされます。
- [複数の Azure サブスクリプションによるスケーリング](../considerations/scaling-subscriptions.md)。 複数の Azure サブスクリプションによる拡張戦略を理解できます。
- [Azure 管理グループでリソースを整理する](https://docs.microsoft.com/azure/governance/management-groups/?toc=https://docs.microsoft.com/azure/cloud-adoption-framework/toc.json&bc=https://docs.microsoft.com/azure/cloud-adoption-framework/_bread/toc.json) Azure 管理グループで複数のサブスクリプションにまたがり、リソース、ロール、ポリシー、デプロイを管理する方法について説明します。
- [一貫性のあるハイブリッド クラウドの作成](../../infrastructure/misc/hybrid-consistency.md)。 オンプレミス管理のさまざまな利便性を維持しながら、クラウド イノベーションの利点を提供するハイブリッド クラウド ソリューションを作成します。

## <a name="networking"></a>ネットワーク

ワークロードを支援する目的でクラウド ネットワーク インフラストラクチャを準備するには、次のガイダンスを使用します。

- [ネットワーク関連の意思決定](../considerations/network-decisions.md)。 組織のワークロード、ガバナンス、接続要件を支援するネットワーク関連のサービス、ツール、アーキテクチャを選択します。
- [仮想ネットワークの計画](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm?toc=https://docs.microsoft.com/azure/cloud-adoption-framework/toc.json&bc=https://docs.microsoft.com/azure/cloud-adoption-framework/_bread/toc.json)。 分離、接続、場所の要件に基づいて仮想ネットワークを計画する方法について説明します。
- [ネットワーク セキュリティのベスト プラクティス](https://docs.microsoft.com/azure/security/azure-security-network-security-best-practices?toc=https://docs.microsoft.com/azure/cloud-adoption-framework/toc.json&bc=https://docs.microsoft.com/azure/cloud-adoption-framework/_bread/toc.json)。 Azure の組み込み機能を利用してネットワーク セキュリティの一般的な問題に対処するときのベスト プラクティスについて説明します。
- [境界ネットワーク](./perimeter-networks.md)。 非武装地帯 (DMZ) とも呼ばれている境界ネットワークを使用すると、クラウド ネットワークと、オンプレミス (または物理的) データセンター ネットワークの間で安全な接続とインターネットの双方向接続が可能になります。
- [ハブアンドスポーク ネットワーク トポロジ](./hub-spoke-network-topology.md)。 ハブ アンド スポークは、複雑なワークロードについて、一般的な通信またはセキュリティ要件をより効率的に管理するためのネットワーク モデルです。 また、Azure サブスクリプションの潜在的な制限にも対応します。

## <a name="identity-and-access-control"></a>ID とアクセスの制御

ワークロードのセキュリティと管理の効率性を改善する目的で ID とアクセス制御インフラストラクチャを設計するとき、次のガイダンスをご利用ください。

- [Azure の ID 管理とアクセス制御セキュリティのベスト プラクティス](https://docs.microsoft.com/azure/security/azure-security-identity-management-best-practices?toc=https://docs.microsoft.com/azure/cloud-adoption-framework/toc.json&bc=https://docs.microsoft.com/azure/cloud-adoption-framework/_bread/toc.json)。 Azure の組み込み機能を利用した ID 管理とアクセス制御に関するベスト プラクティスについて説明します。
- [ロールベースのアクセス制御のベスト プラクティス](./roles.md)。 Azure ロールベースのアクセス制御 (RBAC) では、ユーザー ロールごとに整理されているリソースのグループベース アクセスを詳細に管理できます。
- [Azure Active Directory でのハイブリッドおよびクラウド デプロイ用の特権アクセスをセキュリティで保護する](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-admin-roles-secure?toc=https://docs.microsoft.com/azure/cloud-adoption-framework/toc.json&bc=https://docs.microsoft.com/azure/cloud-adoption-framework/_bread/toc.json)。 Azure Active Directory を使用すると、クラウドとオンプレミスの環境全体で組織の管理者アクセスと管理者アカウントを確実にセキュリティで保護するのに役立ちます。

## <a name="storage"></a>Storage

- [Azure Storage ガイダンス](../considerations/storage-guidance.md)。 使用シナリオをサポートする最適な Azure Storage ソリューションを選択します。
- [Azure Storage のセキュリティ ガイド](https://docs.microsoft.com/azure/storage/common/storage-security-guide?toc=https://docs.microsoft.com/azure/cloud-adoption-framework/toc.json&bc=https://docs.microsoft.com/azure/cloud-adoption-framework/_bread/toc.json)。 Azure Storage でのセキュリティ機能について説明します。

## <a name="databases"></a>データベース

- [Azure で適切な SQL Server オプションを選択する](https://docs.microsoft.com/azure/sql-database/sql-database-paas-vs-sql-server-iaas?toc=https://docs.microsoft.com/azure/cloud-adoption-framework/toc.json&bc=https://docs.microsoft.com/azure/cloud-adoption-framework/_bread/toc.json)。 SQL Server ワークロードを最も効果的に支える PaaS または IaaS ソリューションを選択します。
- [データベース セキュリティのベスト プラクティス](https://docs.microsoft.com/azure/security/azure-database-security-best-practices?toc=https://docs.microsoft.com/azure/cloud-adoption-framework/toc.json&bc=https://docs.microsoft.com/azure/cloud-adoption-framework/_bread/toc.json)。 Azure プラットフォーム上のデータベース セキュリティのベスト プラクティスについて説明します。
- [適切なデータ ストアの選択](https://docs.microsoft.com/azure/architecture/guide/technology-choices/data-store-overview)。 要件に応じた正しいデータ ストアを選択することは、重要な設計上の意思決定です。 SQL および NoSQL データベースの文字通り何百もの実装の中から選択することになります。 データ ストアは、多くの場合、データの構造とサポートする操作の種類によって分類されます。 この記事では、最も一般的ないくつかのストレージ モデルについて説明します。

## <a name="cost-management"></a>コスト管理

- [事業単位、環境、プロジェクトをまたいでコストを追跡する](./track-costs.md)。 正しいコスト追跡メカニズムを構築するためのベスト プラクティスについて説明します。
- [Azure Cost Management でクラウド投資を最適化する方法](https://docs.microsoft.com/azure/cost-management/cost-mgt-best-practices?toc=https://docs.microsoft.com/azure/cloud-adoption-framework/toc.json&bc=https://docs.microsoft.com/azure/cloud-adoption-framework/_bread/toc.json)。 コスト管理戦略を実装し、コスト関連の問題に対処するためのツールについて学習します。
- [予算の作成と管理](https://docs.microsoft.com/azure/cost-management/tutorial-acm-create-budgets?toc=https://docs.microsoft.com/azure/cloud-adoption-framework/toc.json&bc=https://docs.microsoft.com/azure/cloud-adoption-framework/_bread/toc.json)。 Azure Cost Management を使用して予算を作成して管理する方法を説明します。
- [コスト データのエクスポート](https://docs.microsoft.com/azure/cost-management/tutorial-export-acm-data?toc=https://docs.microsoft.com/azure/cloud-adoption-framework/toc.json&bc=https://docs.microsoft.com/azure/cloud-adoption-framework/_bread/toc.json)。 Azure Cost Management でデータをエクスポートし、エクスポートしたデータを管理する方法について説明します。
- [推奨に基づくコストの最適化](https://docs.microsoft.com/azure/cost-management/tutorial-acm-opt-recommendations?toc=https://docs.microsoft.com/azure/cloud-adoption-framework/toc.json&bc=https://docs.microsoft.com/azure/cloud-adoption-framework/_bread/toc.json)。 Azure Cost Management と Azure Advisor を使用して、十分活用されていないリソースを特定し、コストを減らす措置をする方法について説明します。
- [コスト アラートを使用し、使用状況と支出を監視する](https://docs.microsoft.com/azure/cost-management/cost-mgt-alerts-monitor-usage-spending?toc=https://docs.microsoft.com/azure/cloud-adoption-framework/toc.json&bc=https://docs.microsoft.com/azure/cloud-adoption-framework/_bread/toc.json)。 Cost Management アラートを使用し、Azure の使用状況と支出を監視する方法について説明します。
