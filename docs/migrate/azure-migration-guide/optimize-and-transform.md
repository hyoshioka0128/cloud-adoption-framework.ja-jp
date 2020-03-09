---
title: 最適化およびレベル上げ
description: Azure 移行ガイドのこの部分では、ソリューションの設計の確認、サービスの適切なサイズ設定、コストの分析など、最適化の領域について説明します。
author: matticusau
ms.author: mlavery
ms.date: 02/25/2020
ms.topic: guide
ms.service: cloud-adoption-framework
ms.subservice: migrate
ms.custom: fasttrack-new, AQC
ms.localizationpriority: high
ms.openlocfilehash: bcd49a2168db862c3e1a0d948e4948abccbfe7c7
ms.sourcegitcommit: 72a280cd7aebc743a7d3634c051f7ae46e4fc9ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2020
ms.locfileid: "78222494"
---
<!-- cSpell:ignore Fservers Fdatabases -->

<!-- markdownlint-disable MD025 DOCSMD001 -->

# <a name="test-optimize-and-promote"></a>テスト、最適化および昇格

サービスを Azure に移行したら、次の段階では、ソリューションの最適化できる可能性がある領域の確認などを行います。 この作業には、ソリューションの設計の確認、サービスの適切なサイズ設定、コストの分析が含まれる可能性があります。

この段階は、環境を最適化し、可能な環境の変換を実行する機会でもあります。 たとえば、"再ホスト" 移行を実行した後、サービスが Azure 上で実行されている場合は、ソリューション構成や消費されるサービスを再検討し、可能であれば何らかの "リファクタリング" を実行してソリューションの機能を最新化して向上することができます。

この記事の残りの部分では、移行されたワークロードを最適化するためのツールについて説明します。 パフォーマンスとコストのバランスを取ると、ワークロードを運用環境にレベル上げする準備が整います。 プロモーション オプションのガイダンスについては、「[最適化およびレベル上げ](../migration-considerations/optimize/index.md)」でプロセス改善に関する記事を参照してください。

# <a name="right-size-assets"></a>[資産を適切なサイズにする](#tab/optimize)

消費ベースのコスト モデルを提供するすべての Azure サービスは、Azure portal、CLI、または PowerShell を使用してサイズを変更できます。 サービスのサイズを正しく設定するための最初の手順は、その使用状況のメトリックを確認することです。 Azure Monitor サービスを使用すると、これらのメトリックにアクセスできます。 必要に応じて、分析しているサービスのメトリックの収集を構成し、ワークロード パターンに基づいて意味のあるデータを収集できるように適切な時間を確保します。

1. **[監視]** に移動します。
1. **[メトリック]** を選択し、分析するサービスのメトリックを表示するようにグラフを構成します。

::: zone target="chromeless"

::: form action="OpenBlade[#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/metrics]" submitText="Go to Monitor" :::

::: zone-end

サイズを変更できる一般的なサービスをいくつか次に示します。

## <a name="resize-a-virtual-machine"></a>仮想マシンのサイズを変更する

Azure Migrate では、移行前評価フェーズの一環として適切なサイズの分析を実行します。このツールを使用して移行された仮想マシンは、お客様の移行前の要件に基づいて既にサイズが調整されている可能性があります。

ただし、他の方法を使用して作成または移行された仮想マシンの場合、または移行後の仮想マシンの要件を調整する必要がある場合は、必要に応じて仮想マシンのサイズをさらに調整します。

1. **[仮想マシン]** に移動します。
1. 一覧から目的の仮想マシンを選択します。
1. 一覧から **[サイズ]** と目的の新しいサイズを選択します。 必要に応じて、フィルターを調整して目的のサイズを見つけます。
1. **[サイズ変更]** を選択します。

運用環境の仮想マシンのサイズを変更すると、サービスが中断する可能性があります。 運用環境に昇格する前に、VM に正しいサイズ設定を適用するようにします。

::: zone target="chromeless"

::: form action="OpenBlade[#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Compute%2FVirtualMachines]" submitText="Go to Virtual Machines" :::

::: zone-end

::: zone target="docs"

- [Azure リソースに対する予約を管理する](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)
- [Windows VM のサイズを変更する](https://docs.microsoft.com/azure/virtual-machines/windows/resize-vm)
- [Azure CLI を使用して Linux 仮想マシンのサイズを変更する](https://docs.microsoft.com/azure/virtual-machines/linux/change-vm-size)

パートナーはパートナー センターを使用して使用状況を確認できます。

- [最大予約使用状況のための Microsoft Azure VM のサイズ設定](https://docs.microsoft.com/partner-center/azure-usage)

::: zone-end

## <a name="resize-a-storage-account"></a>ストレージ アカウントのサイズを変更する

1. **[ストレージ アカウント]** に移動します。
1. 目的のストレージ アカウントを選択します。
1. 実際の要件に合わせて **[構成]** を選択し、ストレージ アカウントのプロパティを調整します。
1. **[保存]** を選択します。

::: zone target="chromeless"

::: form action="OpenBlade[#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2FStorageAccounts]" submitText="Go to Storage Accounts" :::

::: zone-end

## <a name="resize-a-sql-database"></a>SQL データベースのサイズを変更する

1. **[SQL データベース]** または **[SQL サーバー]** に移動してサーバーを選択します。
1. 目的のデータベースを選択します。
1. **[構成]** と目的の新しいサービス レベル サイズを選択します。
1. **[適用]** を選択します。

::: zone target="chromeless"

::: form action="OpenBlade[#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Sql%2Fservers%2Fdatabases]" submitText="Go to SQL Databases" :::

::: zone-end

# <a name="cost-management"></a>[Cost Management](#tab/ManageCost)

継続的なコスト分析とレビューを実行することが重要です。 この作業は、コストとワークロードのバランスを取るために必要な場合に、リソースのサイズを変更する機会になります。

Azure Cost Management は Azure Advisor と連携して、コストの最適化に関する推奨事項を提供します。 Azure Advisor を使用すると、活動休止状態のリソースや十分に活用されていないリソースを特定することで効率性を最適化し、改善することができます。

1. **[コストの管理と請求]** を選択します。
1. **[アドバイザーの推奨事項]** 、 **[コスト]** タブの順に選択します。
1. **[影響]** と **[年間の潜在的な削減額]** を使用して、潜在的なベネフィットを確認します。

::: zone target="chromeless"

::: form action="OpenBlade[#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/Overview]" submitText="Go to Cost Management + Billing" :::

::: zone-end

**Advisor** を使用し、 **[コスト]** タブを選択して、潜在的なコスト削減の推奨事項を特定することもできます。

> [!TIP]
> 継続的な可用性を必要としないサービスの場合、必要に応じてサービスを開始、停止、または一時停止するソリューションを実装すると、コストの管理に役立ちます (たとえば、Azure Virtual Machines、Azure SQL Data Warehouse など)。
>

::: zone target="chromeless"

::: form action="OpenBlade[#blade/Microsoft_Azure_Expert/AdvisorBlade]" submitText="Go to Azure Advisor" :::

::: zone-end

::: zone target="docs"

- [チュートリアル:推奨事項に従ってコストを最適化する](https://docs.microsoft.com/azure/cost-management/tutorial-acm-opt-recommendations)
- [Azure の課金とコスト管理で予想外の料金を防ぐ](https://docs.microsoft.com/azure/billing/billing-getting-started)
- [コスト分析を使用してコストを調査および分析する](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis)

::: zone-end
