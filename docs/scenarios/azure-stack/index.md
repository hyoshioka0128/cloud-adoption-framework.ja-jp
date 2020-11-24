---
title: データセンターで Azure を実行するための戦略的オプション - Azure Stack
description: Azure Stack Hub を使用して、データセンターに Azure をデプロイします。
author: BrianBlanchard
ms.author: brblanch
ms.date: 05/19/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: strategy
ms.openlocfilehash: 3158a14c5fd71af6b342242f774b9e525f93d87c
ms.sourcegitcommit: 57b757759b676a22f13311640b8856557df36581
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/20/2020
ms.locfileid: "94997031"
---
# <a name="azure-stack-a-strategic-option-for-running-azure-in-your-datacenter"></a>Azure Stack:データセンターで Azure を実行するための戦略的オプション

Microsoft は、アプリケーションとデータ ストレージに対してクラウド優先のアプローチを採用しています。 アプリケーションとデータを 1 つ以上のハイパースケール クラウドに移動することが優先されます。これには、グローバルな Azure オプションや、ロケール固有のソブリン クラウド (Azure Germany や Azure Government など) が含まれます。

Azure Stack Hub は、ソブリン クラウドの別のインスタンスとして機能し、データセンター内の顧客によって操作される場合もあれば、クラウド サービス プロバイダーを通じて使用される場合もあります。 ただし、Azure Stack Hub はハイパースケールのクラウドではなく、Microsoft は、Azure Stack Hub のサービス レベル アグリーメントを公開することもサポートすることもありません。

## <a name="understand-your-cloud-journey"></a>クラウド導入作業について理解する

それぞれの組織では、歴史、ビジネスの特徴、文化、そして最も重要な出発点に基づいて、クラウドへの取り組みは固有のものとなります。 その過程では、多くのオプション、フィーチャー、および機能が提供されます。 また、既存のガバナンスと運用を改善し、新しいものを実装し、クラウド アーキテクチャを利用できるようにアプリケーションを再設計する機会も得られます。

組織の取り組みによって、IT およびビジネス戦略の一環としてクラウドを使用することによる明らかなメリットが特定される可能性があります。 しかし、お客様の取り組みによっては、少なくとも今はクラウドを既存のデータセンター内に維持する同じように強い動機が特定される場合もあります。 このような 2 つの競合する推進力が存在する場合、あえて選択する必要はありません。 Azure Stack Hub の中核となるのは、[サービスとしてのインフラストラクチャ (IaaS)](https://azure.microsoft.com/blog/azure-stack-iaas-part-one) です。 独自のデータセンターで Azure サービスのサブセットを実行できるようにするサービスとしてのプラットフォーム (PaaS) サービスも備えています。

## <a name="azure-stack-hub-in-your-strategy"></a>戦略における Azure Stack Hub

Azure Stack Hub では、物理サーバーまたは既存の仮想化プラットフォームで実行される既存のアプリケーションを移行するための別の方法が提供されます。 これらのワークロードを Azure Stack Hub デプロイの IaaS 環境に移行することで、チームは、スムーズな運用、セルフサービス デプロイ、標準化されたハードウェア構成、Azure 整合性の恩恵を受けることができます。 Azure Stack Hub を使用して最新化やイノベーションのサポートを行うことにより、チームはクラウドを最大限に活用できるようにアプリケーションとワークロードを準備できます。

Azure と Azure Stack Hub でのクラウド導入に関する一貫したプラクティスに従うことにより、同じガバナンスと運用のモデルを、パブリック クラウドまたは独自のデータセンターの資産に適用できます。 Azure Stack Hub では、Azure と同じ Azure Resource Manager モデルを使用して、ソリューションを単一のウィンドウで表示できるようにします。

## <a name="compare-azure-with-azure-stack-hub"></a>Azure と Azure Stack Hub の比較

Azure と Azure Stack Hub にはいくつかの違いがあります。 いくつかは非常に明確ですが、その他は実装サイクルの後半まであきらかになりません。 次の相違点に注意してください。

- Azure では、ほぼ無制限の容量が提供されます。 Azure Stack Hub は、データセンターの物理ハードウェア上に構築されるため、容量の制限につながります。
- Azure と Azure Stack Hub では、API のバージョンと認証メカニズムが多少異なる場合があります。
- Azure Stack Hub では、クラウドを "_誰が_" 操作するかが異なります。これはワークロード操作のレベルに影響を与えます。
- 顧客がサービスの PaaS またはソフトウェアとしてのサービス (SaaS) のどちらを呼び出すかが決定されるため、Azure Stack Hub サービスのどの部分を Azure Stack Hub オペレーターが実行するかを検討する必要があります。

その他の違いについては、クラウドの導入ライフサイクルのさまざまなポイントで、他の Azure Stack Hub の記事で紹介します。

## <a name="next-steps"></a>次のステップ

クラウド導入の取り組みの特定の要素に関するガイダンスについては、以下を参照してください。

- [Azure Stack Hub 移行の計画](./plan.md)
- [環境の準備](./ready.md)
- [Azure Stack Hub のワークロードを評価する](./migrate-assess.md)
- [Azure Stack Hub にワークロードをデプロイする](./migrate-deploy.md)
- [Azure Stack Hub のガバナンス](./govern.md)
- [Azure Stack Hub の管理](./manage.md)
