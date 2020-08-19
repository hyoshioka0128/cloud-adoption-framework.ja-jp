---
title: Azure での保護と復旧
description: 復旧時間を短縮し、業務中断の可能性を小さくすることによって、ビジネスの安定性を確保する方法を説明します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 10/17/2019
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: operate
ms.custom: fasttrack-edit, AQC
ms.localizationpriority: high
ms.openlocfilehash: fdedf440bc947a2877cca6854b4745dd8482e493
ms.sourcegitcommit: 011525720bd9e2d9bcf03a76f371c4fc68092c45
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88567752"
---
<!-- cSpell:ignore siterecovery -->

# <a name="protect-and-recover-in-azure"></a>Azure での保護と復旧

"_保護と復旧_" は、すべてのクラウド管理ベースラインにおいて 3 つ目の、そして最後の分野です。

![クラウド管理ベースライン](../../_images/manage/management-baseline.png)

「[Azure における運用のコンプライアンス](./operational-compliance.md)」では、ビジネスの中断の可能性を減らすことが目的です。 現在の記事は、防ぐことができない停止の期間と影響を軽減することを目的としています。

すべてのエンタープライズグレード環境について、管理ベースラインの推奨最小値の概要を次の表に示します。

| Process                 | ツール                  | 目的                                                                                  |
| ----------------------- | --------------------- | ---------------------------------------------------------------------------------------- |
| データの保護            | Azure Backup          | クラウド内のデータと仮想マシンをバックアップします。                                          |
| 環境を保護する | Azure Security Center | ハイブリッド ワークロード全体でセキュリティを強化し、高度な脅威保護を提供します。 |

::: zone target="docs"

## <a name="azure-backup"></a>Azure Backup

::: zone-end
::: zone target="chromeless"

## <a name="azure-backup"></a>[Azure Backup](#tab/AzureBackup)

::: zone-end

Azure Backup では、ご自身のデータを Microsoft Cloud 内でバックアップ、保護、および復旧できます。 Azure Backup によって、既存のオンプレミスまたはオフサイトのバックアップ ソリューションがクラウドベースのソリューションに置き換えられます。 この新しいソリューションは、信頼性が高く、セキュリティで保護され、コスト競争力を備えています。 Azure Backup では、1 つの一貫したソリューションを通じてオンプレミスの資産を保護および復旧することもできます。

### <a name="enable-backup-for-an-azure-vm"></a>Azure VM のバックアップを有効にする

1. Azure portal で、 **[仮想マシン]** を選択し、レプリケートする VM を選択します。
1. **[操作]** ペインで、 **[バックアップ]** を選択します。
1. Azure Recovery Services コンテナーを作成するか、または既存のコンテナーを選択します。
1. **[Create (or edit) a new policy]\(新しいポリシーを作成 (または編集) する)** を選択します。
1. スケジュールと保持期間を構成します。
1. **[OK]** を選択します。
1. **[バックアップの有効化]** を選択します。

::: zone target="chromeless"

::: form action="OpenBlade[#blade/HubsExtension/Resources/resourceType/Microsoft.Compute%2FVirtualMachines]" submitText="Go to Virtual Machines" :::

::: zone-end

::: zone target="docs"

[概要](/azure/backup/backup-overview)

## <a name="azure-site-recovery"></a>Azure Site Recovery

::: zone-end
::: zone target="chromeless"

## <a name="azure-site-recovery"></a>[Azure Site Recovery](#tab/siterecovery)

::: zone-end

Azure Site Recovery は、ディザスター リカバリー戦略の重要なコンポーネントです。

Site Recovery では、プライマリ Azure リージョン内にホストされた VM とワークロードがレプリケートされます。 これらは、セカンダリ リージョン内にホストされたコピーにレプリケートされます。 プライマリ リージョン内で障害が発生した場合は、セカンダリ リージョン内で実行されているコピーにフェールオーバーします。 その後、そこからアプリケーションとサービスに引き続きアクセスできます。 この復旧に対するプロアクティブなアプローチにより、復旧時間が大幅に短縮されます。 復旧環境が不要になった場合は、運用環境のトラフィックを元の環境に戻すことができます。

### <a name="replicate-an-azure-vm-to-another-region-with-site-recovery"></a>Site Recovery を使用して Azure VM を別のリージョンにレプリケートする

次の手順では、Azure から Azure へのレプリケーション (Azure VM から別のリージョンへのレプリケーション) に Site Recovery を使用するプロセスの概要を説明しています。
>
> [!TIP]
> シナリオによっては、正確な手順が少し異なることがあります。
>

### <a name="enable-replication-for-the-azure-vm"></a>Azure VM のレプリケーションの有効化

1. Azure portal で、 **[仮想マシン]** を選択し、レプリケートする VM を選択します。
1. **[操作]** ペインで、 **[ディザスター リカバリー]** を選択します。
1. **[ディザスター リカバリーの構成]**  >  **[ターゲット リージョン]** を選択し、レプリケート先のターゲット リージョンを選択します。
1. このクイックスタートでは、他のすべてのオプションの既定値をそのまま使用します。
1. **[レプリケーションを有効にする]** を選択します。これで、VM のレプリケーションを有効にするジョブが開始されます。

::: zone target="chromeless"

::: form action="OpenBlade[#blade/HubsExtension/Resources/resourceType/Microsoft.Compute%2FVirtualMachines]" submitText="Go to Virtual Machines" :::

::: zone-end

### <a name="verify-settings"></a>設定の確認

レプリケーション ジョブが完了したら、レプリケーションの状態を確認し、レプリケーションの正常性を検証した後、デプロイをテストできます。

1. VM のメニューで、 **[ディザスター リカバリー]** を選択します。
1. レプリケーションの正常性、作成された復旧ポイント、およびマップ上のソース リージョンとターゲット リージョンを検証します。

::: zone target="chromeless"

::: form action="OpenBlade[#blade/HubsExtension/Resources/resourceType/Microsoft.Compute%2FVirtualMachines]" submitText="Go to Virtual Machines" :::

::: zone-end

### <a name="learn-more"></a>詳細情報

- [Azure Site Recovery の概要](/azure/site-recovery/site-recovery-overview)
- [Azure VM を別のリージョンにレプリケートする](/azure/site-recovery/azure-to-azure-quickstart)
