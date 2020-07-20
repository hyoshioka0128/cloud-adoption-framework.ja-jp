---
title: データセンターで Azure を実行するための戦略的オプション
description: Azure Stack Hub を使用して、データセンターに Azure をデプロイします。
author: BrianBlanchard
ms.author: brblanch
ms.date: 05/19/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: strategy
ms.openlocfilehash: 5b0f1e25f542a2e13403707ea5604ecc29159d66
ms.sourcegitcommit: 9163a60a28ffce78ceb5dc8dc4fa1b83d7f56e6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2020
ms.locfileid: "86451523"
---
# <a name="strategic-option-to-run-azure-in-your-datacenter"></a>データセンターで Azure を実行するための戦略的オプション

Microsoft では、クラウド優先のアプローチを採用しています。 アプリケーションとデータを 1 つ以上のハイパースケール クラウドに移動することが優先されます。これには、グローバルな Azure オプションや、ロケール固有のソブリン クラウド (Azure Germany や Azure Government など) が含まれます。 Azure Stack Hub は、ソブリン クラウドの別のインスタンスとして機能し、データセンター内の顧客によって操作されるか、クラウド サービス プロバイダーを通じて使用されます。 ただし、Azure Stack Hub はハイパースケールのクラウドではなく、Microsoft は、Azure Stack Hub のサービス レベル アグリーメント (SLA) を公開することもサポートすることもありません。

## <a name="understand-your-cloud-journey"></a>クラウド導入作業について理解する

すべての組織では、歴史、ビジネスの特徴、文化、そして最も重要な出発点に基づいて、そのクラウドへの取り組みは固有のものとなります。 クラウドへの移行には、さまざまなオプション、特徴、機能が用意されています。また、既存のガバナンス、運用を改善したり、新しく実装したり、さらにはクラウド アーキテクチャを活用するようにアプリケーションを設計し直したりすることもできます。

IT およびビジネス戦略の一環としてクラウドを使用することによる明らかなメリットが特定される可能性があります。 しかし、お客様の取り組みによっては、"_少なくとも今は_" クラウドをデータセンター内に維持する同じように強い動機が特定される場合もあります。 このような 2 つの競合する推進力が存在する場合、あえて選択する必要はありません。 Azure Stack Hub の中核となるのは、[サービスとしてのインフラストラクチャ (IaaS)](https://azure.microsoft.com/blog/azure-stack-iaas-part-one) で あり、独自のデータセンターで Azure サービスのサブセットを実行できるようにするサービスとしてのプラットフォーム (PaaS) サービスも備えています。

## <a name="azure-stack-hub-in-your-strategy"></a>戦略における Azure Stack Hub

Azure Stack Hub 移行では、物理サーバーまたは既存の仮想化プラットフォームで実行される既存のアプリケーションを移行するための別の方法が提供されます。 これらのワークロードを Azure Stack Hub の IaaS 環境に移行することで、チームは、スムーズな運用、セルフサービス デプロイ、標準化されたハードウェア構成、Azure 整合性の恩恵を受けることができます。 Azure Stack Hub を使用して最新化やイノベーションのサポートを行うことにより、チームはクラウドを最大限に活用できるようにアプリケーションとワークロードを準備できます。

Azure と Azure Stack Hub でのクラウド導入に関する一貫したプラクティスに従うことにより、同じガバナンスと運用のモデルを、パブリック クラウドまたは独自のデータセンターの資産に適用できます。 Azure Stack Hub では、Azure と同じ Azure Resource Manager モデルを使用して、ソリューションを単一のウィンドウで表示できるようにします。

## <a name="understand-the-differences"></a>違いを理解する

Azure と Azure Stack Hub にはいくつかの違いがあります。 いくつかは非常に明確ですが、その他の違いは実装サイクルの後半まであきらかになりません。 注意すべきいくつかの違いを次に示します。

- Azure では、ほぼ無制限の容量が提供されます。 Azure Stack Hub は、データセンターの物理ハードウェア上に構築されるため、容量の制限につながります。
- Azure と Azure Stack Hub では、API のバージョンと認証メカニズムが多少異なる場合があります。
- Azure Stack Hub では、クラウドを "_誰が_" 操作するかが異なります。これはワークロード操作のレベルに影響を与えます。
- エンド カスタマーがサービスの PaaS または SaaS のどちらを呼び出すかが決定されるため、Azure Stack Hub サービスのどの部分を Azure Stack Hub オペレーターが実行するかを考慮する必要があります。

その他の違いについては、クラウドの導入ライフサイクルのさまざまなポイントで、他の Azure Stack Hub の記事で紹介します。

## <a name="next-step-integrate-this-strategy-into-your-cloud-adoption-journey"></a>次のステップ: この戦略をクラウド導入の取り組みに統合する

次の記事の一覧では、クラウド導入過程の特定の時点におけるガイダンスについて説明します。

- [Azure Stack Hub 移行の計画](./plan.md)
- [環境の準備](./ready.md)
- [Azure Stack Hub のワークロードを評価する](./migrate-assess.md)
- [Azure Stack Hub にワークロードをデプロイする](./migrate-deploy.md)
- [Azure Stack Hub のガバナンス](./govern.md)
- [Azure Stack Hub の管理](./manage.md)
