---
title: 最新のコンテナーを計画する
description: シナリオが計画に与える影響について説明する
author: BrianBlanchard
ms.author: brblanch
ms.date: 03/01/2021
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: strategy
ms.openlocfilehash: 5665aece1aeedee7f398e92bb844cab5c2d5ee83
ms.sourcegitcommit: 4d68f2e496b065aa5cd915c4e999def51c8901ba
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104674310"
---
# <a name="plan-for-modern-containers"></a>最新のコンテナーを計画する

[クラウド導入フレームワークの計画方法論](../../plan/index.md)を使用して、クラウド導入計画全体を作成して、クラウドベースのデジタル変革に関わるプログラムとチームをガイドできます。 このガイダンスでは、バックログを作成するためのテンプレートと、チーム全体で必要なスキルを構築するための計画を、クラウドで実行しようとしている内容に基づいて提供します。

標準的な計画方法論では、[デジタル資産の合理化の 5 R](../../digital-estate/5-rs-of-rationalization.md) に焦点を当てます (大まかに言えば、クラウド導入の最も一般的なパス)。より具体的には、これらの計画は、リホスト、再設計、または再構築という最も一般的なシナリオに合わせて調整されます。

## <a name="initial-containers-considerations"></a>コンテナーに関する最初の考慮事項

ワークロードをコンテナーにパッケージ化することが、スケジュールして作業する必要がある 1 番目の作業です。 2 番目は、これらのコンテナーのホスティングを計画します。

### <a name="containers-without-orchestration"></a>オーケストレーションのないコンテナー

ワークロードの中には、自己完結性が高く、Kubernetes のような大規模なプラットフォームに付属する高度な制御やインフラストラクチャの要件の恩恵を必ずしも受けられないものもあります。 ワークロードがコンテナー化されているからといって、Kubernetes にデプロイしなければならないというわけではありません。 Azure には、ポートフォリオ内でワークロードを実行できるように、AKS で必要な管理とインフラストラクチャのレベルを必要としないさまざまなソリューションが用意されています。 次のソリューションではそれぞれ、このアプローチに従って計画を立てます。

- [App Service](/azure/app-service/)
- [Azure Functions](/azure/azure-functions/functions-overview)
- [Azure Container Instances](/azure/container-instances/container-instances-overview)
- [Batch](/azure/batch/batch-technical-overview)

複雑化が予想されず、上記のソリューションの目的と制限に沿って配置されるワークロードについては、より軽量のソリューションをコンテナーに使用することを検討してください。

### <a name="containers-with-orchestration"></a>オーケストレーションのあるコンテナー

これらのワークロードが、フル マネージドの PaaS プラットフォームで実行できずインフラストラクチャレベルの制御で中継する必要がある、コンテナー オーケストレーターにより提供されるような高度なデプロイ機能を使用したい、またはモジュールの複雑化が予想される場合は、Azure Kubernetes Service (AKS) を利用します。 AKS により、両方のコンテナー ホスティングが解決されるだけでなく、広範なアーキテクチャ、SRE、セキュリティ、デプロイ、監視、インフラストラクチャのオプションも提供されます。

プラットフォームの機能セットには、クラスター オペレーター レベルとワークロード レベルの両方でプラットフォームを学習する要件があります。 運用チーム、アーキテクチャ チーム、ワークロード エンジニアリング チームの教育を移行タイムラインに組み込みます。 また、AKS はプラットフォームであるため、ワークロード チームが、このプラットフォーム内の責任の分離と、現在のホスティング配置について確実に理解するようにします。 これはいくつかの点では似ているかもしれませんが、他の点では新しいものになるでしょう。

非常に特殊なワークロードまたは特定の組織要件について、Azure には、コンテナーのオーケストレーション領域に他の主要なプラットフォームが 2 つ用意されています。

- Azure Red Hat OpenShift
- Azure Service Fabric

代替案を検討すべき理由がある場合は、すべてのプラットフォーム オプションの利点とトレードオフを理解するための時間を確保してください。 Azure の既定のソリューションは AKS です。このドキュメントでは、AKS テクノロジを選択していることを前提としています。

## <a name="digital-estate"></a>デジタル資産

デジタル資産を計画するときは、[インベントリ データを収集](../../digital-estate/inventory.md)し、[資産を合理化](../../digital-estate/rationalize.md)することを考えます。 コンテナー導入計画では、VM、データ、アプリケーションなどのすべての資産が、サポートするワークロード別にグループ化されていることが重要です。 グループ化と基本的な合理化が完了したら、これらのワークロードを評価して、パッケージとリホストまたは再構築オプションを決定できます。

デジタル資産の評価では、コンテナーの永続化に基づいてデータの計画を評価する必要もあります。 コンテナーは、永続的または非永続的な状態で実行できます。 エラーが発生した場合、永続状態のコンテナーのデータは保持されます。 非永続的コンテナーのデータは保持されません。 熟練の DevOps チームで一般的な、非永続的構成を選択する場合は、Azure SQL Database のような永続的な環境でワークロード データをホスティングすることを考慮する必要があります。

これらの考慮事項により、コンテナー化されたワークロードのクラウド導入を完了するために必要なアクションが明確になります。

## <a name="modern-container-adoption-plan"></a>最新のコンテナー導入計画

標準的な[クラウド導入計画テンプレート](../../plan/template.md)では、一般的なクラウド導入処理に必要な作業の種類が考慮されています。 しかしながら将来的には、ワークロードをコンテナーにパッケージ化し、コンテナーのプロビジョニングをオーケストレーションするタスクを計画に追加する必要があるでしょう。

## <a name="modern-container-readiness-plan"></a>最新のコンテナー準備計画

クラウド導入スキルアップ計画に加えて、クラウド導入チームは、計画を実行する前に、コンテナーと Kubernetes に関連するスキル開発が必要になる場合があります。

- [Kubernetes の基本について学ぶ](/azure/aks/concepts-clusters-workloads)
- [コンテナーについて学ぶ](https://azure.microsoft.com/product-categories/containers/)
- [Kubernetes のベスト プラクティスについて理解を深める](/azure/aks/best-practices)

ワークロード チームが移行計画を文書化およびドライランするための時間を確実に割り当てます。 既存のアプリケーションまたは外部システム (このワークロードに依存する依存関係とシステムの両方) を変更し、移行作業をサポートするために柔軟性を高める必要がある場合があります。 これは、運用前の環境と運用環境の両方に当てはまります。

## <a name="next-step-review-your-environment-or-azure-landing-zone"></a>次のステップ: 環境または Azure ランディング ゾーンを確認する

次の一連の記事には、クラウド導入作業において、クラウド導入シナリオの成功に役立つ特定のポイントについて説明されています。

- [環境または Azure ランディング ゾーンを確認する](./ready.md)
- [最新のコンテナーへのワークロードの移行](./migrate.md)
- [最新のコンテナー ソリューションを使用したイノベーション](/azure/architecture/reference-architectures/containers/aks-start-here?toc=/azure/cloud-adoption-framework/toc.json&bc=/azure/cloud-adoption-framework/_bread/toc.json)
- [最新のコンテナー ソリューションのガバナンス](./govern.md)
- [最新のコンテナー ソリューションの管理](./manage.md)
- [コンテナーとコンピューティングのデシジョン ツリーの参照](/azure/architecture/guide/technology-choices/compute-decision-tree)