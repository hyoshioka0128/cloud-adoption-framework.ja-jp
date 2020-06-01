---
title: コンピューティング オプションを確認する
description: Azure 向けのクラウド導入フレームワークを使用して、ワークロードのホストに必要なコンピューティング要件を判断する方法を学習します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 05/15/2019
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.openlocfilehash: 2e4aad0e866ee7ffd3fb0eeb3e2688b24aa5dae1
ms.sourcegitcommit: 9a84c2dfa4c3859fd7d5b1e06bbb8549ff6967fa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/21/2020
ms.locfileid: "83756279"
---
# <a name="review-your-compute-options"></a>コンピューティング オプションを確認する

クラウド導入を準備する場合、ワークロードをホストするためのコンピューティング要件を決定することが重要な検討事項です。 Azure コンピューティングの製品とサービスは、さまざまなワークロード コンピューティングのシナリオと機能をサポートしています。 コンピューティング要件に対応するためのランディング ゾーン環境の構成方法は、ワークロードのガバナンス、技術的要件、ビジネス要件によって異なります。

## <a name="identify-compute-services-requirements"></a>コンピューティング サービスの要件を特定する

ランディング ゾーンの評価と準備の一環として、ランディング ゾーンでサポートする必要があるすべてのコンピューティング リソースを特定する必要があります。 このプロセスでは、ワークロードを構成する各アプリケーションとサービスの評価を行い、コンピューティング要件とホスティング要件を決定します。 要件を特定して文書化すると、ランディング ゾーンのポリシーを作成して、ワークロードのニーズに基づいて許可されるリソースの種類を制御できます。

ランディング ゾーン環境にデプロイする各アプリケーションまたはサービスに対しては、次のデシジョン ツリーを出発点として使用すると、コンピューティング サービスの要件を決定するのに役立ちます。

![Azure コンピューティング サービスのデシジョン ツリー](../../_images/ready/compute-decision-tree.png)

> [!NOTE]
> 各アプリケーションまたはサービスのコンピューティング オプションを評価する方法の詳細については、Azure アプリケーション アーキテクチャ ガイドの「[Azure コンピューティング オプションの概要](https://docs.microsoft.com/azure/architecture/guide/technology-choices/compute-decision-tree)」を参照してください。

### <a name="key-questions"></a>主な質問

ワークロードに関する次の質問に答えると、Azure コンピューティング サービスのデシジョン ツリーに基づいて意思決定を行うのに役立ちます。

- **まったく新しいアプリケーションおよびサービスを構築していますか? それとも、既存のオンプレミスのワークロードから移行していますか?** クラウド導入作業の一環として新しいアプリケーションを開発すると、設計段階から最新のクラウド ベースのホスティング テクノロジを最大限に活用できます。
- **既存のワークロードを移行する場合、それらは最新のクラウド テクノロジを利用できますか?** オンプレミスのワークロードを移行するには、次の分析が必要です。既存のアプリケーションとサービスを最新のクラウド テクノロジを利用するように簡単に最適化できますか? または、"_リフト アンド シフト_" 方式の方がワークロードに適していますか?
- **アプリケーションやサービスはコンテナーを利用できますか?** アプリケーションが、コンテナー化されたホスティングに適している場合は、[Azure のコンテナー サービス](https://azure.microsoft.com/product-categories/containers)が提供するリソースの効率性、スケーラビリティ、およびオーケストレーション機能を利用できます。 [Azure マネージド ディスク](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview)と [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) の両方を、コンテナー化されたアプリケーションで永続的ストレージとして使用できます。
- **アプリケーションは Web ベースと API ベースのどちらですか? また、アプリケーションで PHP、ASP.NET、Node.js、あるいは類似のテクノロジを使用しますか?** Web アプリは [Azure App Service](https://docs.microsoft.com/azure/app-service/overview) のマネージド インスタンスにデプロイできるため、ホスティングのために仮想マシンを維持する必要はありません。
- **ワークロードの OS とホスティング環境を完全に制御する必要がありますか?** OS、ディスク、ローカルで実行されているソフトウェア、その他の構成など、ホスティング環境を制御する必要がある場合は、[Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines) を使用してアプリケーションとサービスをホストすることができます。 仮想マシンのサイズとパフォーマンス レベルの選択に加えて、仮想ディスク ストレージに関する決定が、サービスとしてのインフラストラクチャ (IaaS) のワークロードに関連するパフォーマンスと SLA に影響します。 詳細については、[Azure Disk Storage](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) のドキュメントをご覧ください。
- **ワークロードには、ハイパフォーマンス コンピューティング (HPC) 機能が含まれますか?** [Azure Batch](https://docs.microsoft.com/azure/batch/batch-technical-overview) は、プラットフォーム サービスとしてコンピューティング リソースのジョブ スケジューリングと自動スケール機能を備えているので、クラウドで大規模な並列アプリケーションや HPC アプリケーションを簡単に実行できます。
- **アプリケーションでマイクロサービス アーキテクチャが使用されますか?** マイクロサービス ベースのアーキテクチャを使用するアプリケーションでは、いくつかの最適化されたコンピューティング テクノロジを利用できます。 自己完結型のイベント駆動型ワークロードは、[Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) を使用して、インフラストラクチャを必要としないスケーラブルなサーバーレス アプリケーションを構築できます。 マイクロサービスが実行されている環境をより詳細に制御する必要があるアプリケーションでは、[Azure Container Instances](https://docs.microsoft.com/azure/container-instances/container-instances-overview)、[Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/intro-kubernetes)、[Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview) などのコンテナー サービスを使用できます。

> [!NOTE]
> ほとんどの Azure コンピューティング サービスは、Azure Storage と組み合わせて使用されます。 関連するストレージの決定については、「[ストレージ設計に関する決定事項](./storage-options.md)」を参照してください。

## <a name="common-compute-scenarios"></a>コンピューティングに関する一般的なシナリオ

次の表は、いくつかの一般的な使用シナリオと、その処理に推奨されるコンピューティング サービスを示しています。

| **シナリオ** | **コンピューティング サービス** |
| --- | --- |
| 選択した構成を使用して、Linux と Windows の仮想マシンを数秒でプロビジョニングする必要がある。 | [Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines) |
| 自動スケーリングにより数分で数千台の VM を作成し、高可用性を実現する必要がある。 | [仮想マシン スケール セット](https://azure.microsoft.com/services/virtual-machine-scale-sets) |
| Kubernetes のデプロイ、管理、操作を簡略化したい。 | [Azure Kubernetes Service (AKS)](https://azure.microsoft.com/services/kubernetes-service) |
| イベント駆動のサーバーレス アーキテクチャを使用してアプリ開発を加速する必要がある。 | [Azure Functions](https://azure.microsoft.com/services/functions) |
| Windows と Linux でマイクロサービスを開発し、コンテナーを調整する必要がある。 | [Azure Service Fabric](https://azure.microsoft.com/services/service-fabric) |
| フル マネージド プラットフォームを使用して、Web とモバイル向けのクラウド アプリをすばやく作成したい。 | [Azure App Service](https://azure.microsoft.com/services/app-service) |
| 1 つのコマンドを使用して、アプリをコンテナー化し、簡単にコンテナーを実行したい。 | [Azure Container Instances](https://azure.microsoft.com/services/container-instances) |
| 仮想マシンを数十、数百から数千台にスケーリング可能な、クラウド規模のジョブ スケジューリングとコンピューティング管理を可能にする必要がある。 | [Azure Batch](https://azure.microsoft.com/services/batch) |
| 可用性が高くスケーラブルなクラウド アプリケーションと API を作成し、ハードウェアではなくアプリに集中できるようにする必要がある。 | [Azure Cloud Services](https://azure.microsoft.com/services/cloud-services) |

## <a name="regional-availability"></a>リージョン別の提供状況

Azure を利用すれば、_顧客やパートナーがいる場所を問わず_、彼らを獲得するために必要なスケールでサービスを提供できます。 クラウドのデプロイを計画する際の重要な要素として、ワークロード リソースをホストする Azure リージョンの決定があります。

Azure App Service など、いくつかのコンピューティング オプションは、ほとんどの Azure リージョンで一般提供されています。 しかし、一部のコンピューティング サービスは、特定のリージョンでのみサポートされます。 一部の仮想マシンの種類とそれに関連付けられているストレージの種類は、利用可能なリージョンに制限があります。 どのリージョンにコンピューティング リソースをデプロイするかを決定する前に、[リージョン ページ](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=azure-vmware-cloudsimple,cloud-services,batch,container-instances,app-service,service-fabric,functions,kubernetes-service,virtual-machine-scale-sets,virtual-machines)を参照して、リージョン別の提供状況の最新の状態を確認することをお勧めします。

Azure のグローバル インフラストラクチャの詳細については、[Azure リージョンのページ](https://azure.microsoft.com/global-infrastructure/regions)を参照してください。 また、各 Azure リージョンで利用可能なサービス全体の具体的な詳細については、[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=all)に関するページで確認できます。

## <a name="data-residency-and-compliance-requirements"></a>データの保存場所とコンプライアンスの要件

多くの場合、データ ストレージに関連する法律上および契約上の要件がワークロードに適用されます。 これらの要件は、組織の場所、ファイルやデータが格納および処理される管轄区域、および該当する事業部門によって異なる場合があります。 考慮すべきデータに関する義務の要素には、データ分類、データの場所、および共同責任モデルに基づくデータ保護に関するそれぞれの責任が含まれます。 多くのコンピューティング ソリューションは、リンクされたストレージ リソースに依存しています。 この要件は、コンピューティングの決定に影響を与えることもあります。 これらの要件の理解に役立つ情報として、ホワイト ペーパー「[Azure を使用して基準に準拠しているデータの保存場所とセキュリティを実現する](https://azure.microsoft.com/resources/achieving-compliant-data-residency-and-security-with-azure)」をご覧ください。

コンプライアンスの取り組みの一環として、コンピューティング リソースが物理的に配置されている場所の制御が必要になる場合があります。 Azure リージョンは地域と呼ばれるグループに編成されています。 [Azure の地域](https://azure.microsoft.com/global-infrastructure/geographies)では、データの保存場所、主権、コンプライアンス、回復性に関する要件が地域的および政治的な境界内で確実に遵守されます。 ワークロードがデータ主権やその他のコンプライアンス要件の影響を受ける場合は、要件に準拠している Azure 地域のリージョンにストレージ リソースをデプロイする必要があります。

## <a name="establish-controls-for-compute-services"></a>コンピューティング サービスの制御を設定する

ランディング ゾーン環境を準備するときに、各ユーザーがデプロイできるリソースを制限する制御を設定できます。 この制御は、開発者や IT チームがワークロードをサポートするために必要なリソースを引き続きデプロイおよび構成できるようにしながら、コストを管理し、セキュリティ リスクを制限するのに役立ちます。

ランディング ゾーンの要件を特定して文書化したら、[Azure Policy](https://docs.microsoft.com/azure/governance/policy/overview) を使用して、ユーザーに作成を許可するコンピューティング リソースを制御できます。 これらの制御は、[コンピューティング リソースの種類の作成を許可または拒否する](https://docs.microsoft.com/azure/governance/policy/samples/allowed-resource-types)という形をとることができます。 たとえば、ユーザーが Azure App Service または Azure Functions リソースのみを作成するように制限することができます。 ポリシーを使用して、リソースの作成時に、[プロビジョニングできる仮想マシン SKU を制限する](https://docs.microsoft.com/azure/governance/policy/samples/allowed-skus-storage)、または[特定の VM イメージのみ許可する](https://docs.microsoft.com/azure/governance/policy/samples/allowed-custom-images)など、指定可能なオプションを制御することもできます。

ポリシーは、リソース、リソース グループ、サブスクリプション、および管理グループにスコープを設定できます。 これらのポリシーを [Azure Blueprint](https://docs.microsoft.com/azure/governance/blueprints/overview) の定義に含め、クラウド資産全体で繰り返し適用できます。
