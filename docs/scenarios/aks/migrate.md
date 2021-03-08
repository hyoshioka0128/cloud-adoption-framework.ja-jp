---
title: 最新のコンテナーへのワークロードの移行
description: 複数の Web アプリケーションをコンテナー ソリューションに移行することで、クラウド プラットフォームの依存関係を軽減し、インフラストラクチャのフットプリントを削減できる可能性があります
author: BrianBlanchard
ms.author: brblanch
ms.date: 03/01/2021
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: migrate
ms.openlocfilehash: 810a62968a6703e110dfad04a9f6c0256b2109cf
ms.sourcegitcommit: b8f8b7631aabaab28e9705934bf67dad15e3a179
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101800099"
---
# <a name="migrate-workloads-to-modern-containers"></a>最新のコンテナーへのワークロードの移行

ほとんどの最新のコンテナー オプションでは、アプリケーションの再アーキテクチャや再デプロイが必要です。 しかし、Azure Kubernetes Service (AKS) のオーケストレーション機能により、AKS ソリューションを標準の移行プロセスに組み込むことができます。

オンプレミスのデータセンターから Azure の Kubernetes クラスターに既存のワークロードを移行することには明確な増加傾向があります。 このアプローチによって、移行後のインフラストラクチャ フットプリントが削減する可能性があります。 さらに重要なことに、コンテナーへの移行により、ポートフォリオの移植性が高まり、パブリック クラウドとプライベート クラウドの間のワークロードの移動が簡単になります。 この傾向は、組織に Web アプリケーションの大規模なコレクションがある場合に最もよく見られます。

## <a name="one-migrate-approach"></a>1 つの移行アプローチ

[クラウド導入フレームワークの 1 つの移行シナリオ](../index.md)の一環として、Azure Kubernetes Service (AKS) に移行して、クラウド内のコンテナーを高速化できます。 一般に、Azure への移行では、Azure Migrate とパートナー ツールを使用して、ワークロードの評価、ワークロードの移行、およびクラウドへのワークロードのリリースを行います。 この 3 ステップのプロセスを AKS の移行に適用できますが、移行手順に役立つ他のいくつかのツールが必要になる場合があります。

### <a name="assess-workloads"></a>ワークロードの評価

ワークロードとそれらの現在のコンテナ化の状態のインベントリが必要になります。 ワークロードは、コンテナー内での動作中、機能し、パフォーマンスが高いことが検証されるまで移行できません。 アプリケーション所有者と協力して、コンテナー化を実行し、結果を検証して、作業のイメージ構築パイプラインを作成するための時間を割り当てます。 Windows 固有の要件 (例: Gmsa)、ローカル ファイル システムの使用状況、キャッシュ実装の詳細、シングルトン実装などの独自の依存関係、およびデータベースなどの依存関係をメモします。

中央管理チームが組織全体のコンテナー化の作業を指揮することができますが、それを、むしろプロジェクト管理機能と技術要件の収集および監視プロセスと考え、アプリケーション所有者がこのプロセスに大きく関与する必要があります。

### <a name="migrate-containers-and-workloads"></a>コンテナーとワークロードの移行

移行するときは、ターゲットの Kubernetes のバージョンが AKS に対して確実にサポート対象期間内であるようにします。 古いバージョンを使用している場合、サポート対象範囲内でないため、AKS でサポートされるようにバージョンをアップグレードすることが必要になる場合があります。 詳細については、[AKS でサポートされる Kubernetes のバージョン](/azure/aks/supported-kubernetes-versions)に関するページを参照してください。

どのような移行でも、合意できるメンテナンス期間を決定し、すべての利害関係者に対して、移行の進め方を明確にします。 必要に応じて、移行を追跡し、ダッシュボードに表示します。 ダウンタイム移行を交渉できない場合は、ダウンタイムなしの移行に関する追加の計画、コスト、および複雑さを考慮します。 ダウンタイム移行が期待されていない場合に、それが必要であることがわかった場合は、その変更を利害関係者に通知します。 その変更の影響分析を実行し、リスクが文書化され、合意されていることを確認します。

すべての移行 (ダウンタイム移行でも) で、移行をサポートするために柔軟性を追加して既存のアプリケーションを変更する必要がある場合があります。 できるだけ早く、アプリケーション チームがワークロード移行の計画に完全に関与するようにします。 たとえば、移行が完了する前に、追加の DNS、接続文字列、設定切り替え機能を現在のワークロードにデプロイする必要がある場合があります。

現在、Azure へのコンテナーとワークロードのレプリケーションを実行するために、いくつかのオープンソース ツールのいずれかを使用する必要があります。

既存の Kubernetes プラットフォーム (AKS エンジン、ACS、またはその他の Kubernetes 実装) を使用している場合は、移行に役立つオープンソース ツールの使用を検討することができます。 このような場合、Kubernetes で機能するワークロードを既に取得しているため、AKS でのホスト変更は通常はるかに簡単になります。 移行を実行する前に、AKS にすべての機能が存在することを確認します。

- [Velero](https://velero.io)
- [Azure Kube CLI 拡張機能](https://github.com/yaron2/azure-kube-cli)
- [Reshifter](https://github.com/mhausenblas/reshifter)
- [AKS エンジン](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview)から AKS への移行
- [Azure Container Service (acs)](https://azure.microsoft.com/updates/azure-container-service-will-retire-on-january-31-2020/) から AKS への移行
- 既存のリソースを別のリージョンに移動する

移行するときは、ターゲットの Kubernetes のバージョンが AKS に対して確実にサポート対象期間内であるようにします。 古いバージョンを使用している場合は、サポート対象範囲内ではなく、AKS でサポートされるようにバージョンをアップグレードすることが必要になる場合があります。 詳細については、[AKS でサポートされる Kubernetes のバージョン](/azure/aks/supported-kubernetes-versions)に関するページを参照してください。 可能であれば、常に同じバージョンの Kubernetes に移行してください。 つまり、優先度に基づいて、既存のシステムでインプレース アップグレードを実行するか、移行後アップグレードを計画します。

## <a name="next-step-innovate-using-modern-container-solutions"></a>次の手順: 最新のコンテナー ソリューションを使用したイノベーション

以下の記事は、クラウド導入の取り組みにおける特定ポイントでのガイダンスを提供し、クラウド導入シナリオでの成功に役立ちます。

- [最新のコンテナー ソリューションを使用したイノベーション](/azure/architecture/reference-architectures/containers/aks-start-here?toc=/azure/cloud-adoption-framework/toc.json&bc=/azure/cloud-adoption-framework/_bread/toc.json)
- [最新のコンテナー ソリューションの管理](./govern.md)
- [最新のコンテナー ソリューションの管理](./manage.md)
