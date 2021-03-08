---
title: 最新のコンテナー ソリューションを管理する
description: ガバナンス プラクティスを最新のコンテナー インスタンスに拡張する
author: BrianBlanchard
ms.author: brblanch
ms.date: 03/01/2021
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: govern
ms.openlocfilehash: a764ac7671eeeea7430b20f48cff223fba554845
ms.sourcegitcommit: b8f8b7631aabaab28e9705934bf67dad15e3a179
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101801281"
---
<!-- docutune:ignore "public container registry" -->

# <a name="govern-modern-container-solutions"></a>最新のコンテナー ソリューションを管理する

[クラウド導入フレームワークの手法](../../govern/index.md)を使って、クラウド ポートフォリオのガバナンスを体系的かつ段階的に改善することができます。 この記事では、Azure または他のパブリック/プライベート クラウドにデプロイされた Kubernetes クラスターにガバナンスのアプローチを拡張する方法について説明します。

## <a name="initial-governance-foundation"></a>ガバナンスを始めるための基礎

ガバナンスでは、最初に[ガバナンス MVP と呼ばれることが多い初期のガバナンス基盤](../../govern/initial-foundation.md)を使用します。 この基盤により、クラウド環境でガバナンスを実現するために必要な、基本的な Azure 製品がデプロイされます。

初期のガバナンス基盤では、ガバナンスの次の側面に重点が置かれています。

- 基本的なハイブリッド ネットワークと接続。
- ID とアクセス制御のための Azure のロールベースのアクセス制御 (RBAC)。
- リソースを一貫して識別するための名前付けとタグ付けの標準。
- リソース グループ、サブスクリプション、および管理グループを使用したリソースの編成。
- ガバナンス ポリシーを適用するための Azure Policy と Azure Blueprints。

初期のガバナンス基盤のこのような機能を使用して、最新のコンテナー ソリューション インスタンスを管理できます。 ただし、初めに、[Azure Policy をコンテナーに](/azure/governance/policy/concepts/policy-for-kubernetes?bc=/azure/cloud-adoption-framework/_bread/toc.json&toc=/azure/cloud-adoption-framework/toc.json)適用するためのコンポーネントを初期の基盤にいくつか追加する必要があります。 構成が完了したら、Azure Policy と初期のガバナンス基盤を使用して、次の種類のコンテナーを管理できます。

- [Azure Kubernetes Service (AKS)](/azure/aks/intro-kubernetes?bc=/azure/cloud-adoption-framework/_bread/toc.json&toc=/azure/cloud-adoption-framework/toc.json)
- [Azure Arc 対応 Kubernetes](/azure/azure-arc/kubernetes/overview?bc=/azure/cloud-adoption-framework/_bread/toc.json&toc=/azure/cloud-adoption-framework/toc.json)
- [AKS エンジン](https://github.com/Azure/aks-engine/blob/master/docs/README.md)

## <a name="expand-on-governance-disciplines"></a>ガバナンス規範での拡張

初期のガバナンス基盤をガバナンスのさまざまな規範で拡張して、すべての Kubernetes インスタンスで一貫性のある安定したデプロイ アプローチを実現できます。

Kubernetes クラスターのガバナンスは、5 つの異なる観点から見ることができます。

### <a name="azure-resource-governance"></a>Azure リソースのガバナンス

1 つめは Azure リソースの観点です。 すべてのクラスターが組織の要件に準拠していることを確認します。 この中には、ネットワーク トポロジ、プライベート クラスター、SRE チームの Azure RBAC のロール、診断設定、利用可能なリージョン、ノード プールに関する考慮事項、Azure Container Registry のガバナンス、Azure Load Balancer のオプション、AKS のアドオン、診断設定などの概念が含まれます。 このガバナンスによって、組織内のクラスターの "外観" と "トポロジ" の一貫性が確保されます。 これは、クラスター デプロイ後のブートストラップにも拡張する必要があります。たとえば、インストールする必要があるセキュリティ エージェントやその構成方法などです。

<!-- docutune:casing Bicep CVEs -->

Snowflake のクラスターは、中央の能力では管理が困難です。 ポリシーが一様に適用され、異常なクラスターが使用されず検出可能になるように、クラスター間の不整合を最小限に抑えます。 これには、ARM、Bicep、Terraform といった、クラスターのデプロイに使用されるテクノロジが含まれる場合もあります。

管理グループ/サブスクリプション レベルで適用される Azure Policy では、これらの考慮事項の大部分に対応することができますが、すべてではありません。

### <a name="kubernetes-workload-governance"></a>Kubernetes ワークロードのガバナンス

Kubernetes はそれ自体がプラットフォームです。このため、2 つめはクラスター内での処理のガバナンスです。 これには、名前空間のガイダンス、ネットワーク ポリシー、Kubernetes RBAC、制限、クォータなどが含まれます。 これは、ワークロードに適用されるガバナンスであり、クラスターにはそれほど適用されません。 すべてのワークロードは一意です。これは、それぞれによって解決されるビジネス上の問題が異なること、およびこれらがさまざまな方法とテクノロジを使用して実装されることによります。 "1 つのサイズであらゆるものに対応可能な" ガバナンス プラクティスは多くないかもしれませんが、OCI 成果物の作成/消費、サプライ チェーンの要件、パブリック コンテナー レジストリの使用、イメージの検疫プロセス、デプロイ パイプラインなどに関するガバナンスを検討する必要があります。

可能な場合は、一般的なツールとパターンについても標準化することを検討します。 Helm、サービス メッシュ、イングレス コントローラー、GitOps のオペレーター、永続ボリュームなどのテクノロジに関する推奨事項を提示します。 これには、ポッドのマネージド ID と Key Vault のソーシング シークレットの使用に関するガバナンスも含まれます。

テレメトリへのアクセスに関する強い期待に応え、ワークロードの所有者が自社製品の品質向上に必要なメトリックとデータに適切にアクセスできるようにします。また、クラスター オペレーターがシステム テレメトリにアクセスしてサービス内容を改善できるようにします。 多くの場合、データを両者の間で相互に関連付け、必要に応じて適切にアクセスできるように、ガバナンス ポリシーを設定する必要があります。

クラスター レベルで適用される AKS の Azure Policy では、これらの一部に対応することができますが、すべてではありません。

### <a name="cluster-operator-roles-devops-sre"></a>クラスター オペレーターのロール (DevOps、SRE)

3 つめは、クラスター オペレーターのロールに関するガバナンスです。 SRE チームはクラスターとどのように対話するのでしょうか? チームとワークロード チームにはどのような関係性があるでしょうか? これらは同じでしょうか? クラスター オペレーターには、クラスター トリアージ アクティビティのプレイブックが明確に定義されている必要があります。たとえば、クラスターへのアクセス方法、クラスターにどこからアクセスするか、クラスターに対してどのようなアクセス許可を持っているか、それらのアクセス許可がいつ割り当てられたか、などです。 ガバナンス ドキュメント、ポリシー、トレーニング資料などで、ワークロード オペレーターとクラスター オペレーターがこの点についてどのように異なるかを確認します。 組織によっては、これらが同じである可能性もあります。

### <a name="cluster-per-workload-or-many-workloads-per-cluster"></a>ワークロードにつき 1 つのクラスター、クラスターにつき多数のワークロード

4 つめはマルチテナントに関するガバナンスです。 つまり、本質的にはすべてが同じワークロード チームによって所有されているアプリケーションの "グループのようなもの" がクラスターに含まれ、関連するワークロード コンポーネントが "*単一のセット*" で示されている場合があります。 また、複数の異なるワークロードとワークロード所有者が含まれている事実上のマルチテナントとしてクラスターを設計している場合があります。これは実行され、組織内の管理サービス内容として管理されます。 それぞれのガバナンス戦略は顕著に異なります。このため、選択した戦略が適用されるように管理する必要があります。 両方のモデルをサポートする必要がある場合は、どの種類のクラスターにどのポリシーを適用するのかについて、ガバナンス計画で明確に定義します。

これは、スタッフの配置、予算、イノベーションなどに大きな影響を与えるため、[戦略フェーズ](./strategy.md)中に選択する必要があります。

### <a name="stay-current-efforts"></a>現在の取り組みを維持

5 つめは、ノード イメージの鮮度 (修正プログラムの適用) や Kubernetes のバージョン管理などの操作です。 ノード イメージのアップグレード、適用された修正プログラムの追跡、Kubernetes および AKS CVE の修復プランの追跡とまとめは、誰が担当するのでしょうか? ワークロード チームは、クラスターのアップグレード時にソリューションが機能するかどうかの検証に関与する "*必要があります*"。クラスターが最新ではない場合は、Azure サポートから除外されます。 AKS では、"最新の状態に維持" の取り組みについて強力なガバナンスを適切に実施することが "*重要*" ですが、Azure の他のほとんどのプラットフォームではなおさらです。 このため、クラスターを最新の状態に保つには、アプリケーション チームと緊密に連携し、十分な時間 (少なくとも月単位) をかけてワークロードを検証する必要があります。 Kubernetes に依存しているすべてのチームが、このプラットフォームに関わっている間、この継続的な取り組みの要件とコストを理解していることを確認します。

### <a name="security-baseline"></a>セキュリティ ベースライン

AKS クラスターのセキュリティのために、次のベスト プラクティスをセキュリティ ベースラインに追加することができます。

- [ポッドのセキュリティ保護](/azure/aks/use-pod-security-on-azure-policy?bc=/azure/cloud-adoption-framework/_bread/toc.json&toc=/azure/cloud-adoption-framework/toc.json)
- [ポッド間のトラフィックのセキュリティ保護](/azure/aks/use-network-policies?bc=/azure/cloud-adoption-framework/_bread/toc.json&toc=/azure/cloud-adoption-framework/toc.json)
- プライベート クラスターを使用していない場合は、[AKS API への許可された IP アクセス](/azure/aks/api-server-authorized-ip-ranges?bc=/azure/cloud-adoption-framework/_bread/toc.json&toc=/azure/cloud-adoption-framework/toc.json)。

### <a name="identity"></a>ID

以下のような多くのベスト プラクティスを ID ベースラインに適用して、Kubernetes クラスター全体で ID とアクセス管理の一貫性を維持できます。

- [Azure AD との統合](/azure/aks/managed-aad?bc=/azure/cloud-adoption-framework/_bread/toc.json&toc=/azure/cloud-adoption-framework/toc.json)
- [RBAC と Azure AD の統合](/azure/aks/azure-ad-rbac?bc=/azure/cloud-adoption-framework/_bread/toc.json&toc=/azure/cloud-adoption-framework/toc.json)
- [Kubernetes のマネージド ID](/azure/aks/use-managed-identity?bc=/azure/cloud-adoption-framework/_bread/toc.json&toc=/azure/cloud-adoption-framework/toc.json)
- [Azure AD ポッドID を使用した、他の Azure リソースへのアクセス](/azure/aks/use-azure-ad-pod-identity?bc=/azure/cloud-adoption-framework/_bread/toc.json&toc=/azure/cloud-adoption-framework/toc.json)

## <a name="next-step-manage-modern-container-solutions"></a>次の手順: 最新のコンテナー ソリューションの管理

次の記事では、クラウド導入シナリオを成功させるのに役立つ、クラウド導入作業のガイダンスが特定のポイントごとに示されています。

- [最新のコンテナー ソリューションの管理](./manage.md)
