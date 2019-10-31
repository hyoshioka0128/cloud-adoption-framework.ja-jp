---
title: Azure での保護と復旧
titleSuffix: Microsoft Cloud Adoption Framework for Azure
description: 復旧時間を短縮してビジネスの安定性を確保する
author: BrianBlanchard
ms.author: brblanch
ms.date: 10/17/2019
ms.topic: article
ms.service: cloud-adoption-framework
ms.subservice: operate
ms.custom: fasttrack-edit, AQC
ms.localizationpriority: high
ms.openlocfilehash: a79164435772f571849d0a836f43b53ce3bca087
ms.sourcegitcommit: 35c162d2d09ec1c4a57d3d57a5db1d56ee883806
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/17/2019
ms.locfileid: "72557008"
---
# <a name="protect-and-recover-in-azure"></a>Azure での保護と復旧

保護と復旧は、すべてのクラウド管理ベースラインにおいて 3 つ目の、そして最後の分野です。

![クラウド管理ベースライン](../../_images/manage/management-baseline.png)

"運用のコンプライアンス" に関する前の記事では、ビジネスの中断の可能性を減らすことが目的です。 "保護と復旧" に関するこの記事は、防ぐことができない停止の期間と影響を軽減することを目的としています。

すべてのエンタープライズグレード環境について、管理ベースラインの推奨最小値の概要を次の表に示します。

|Process  |ツール  |目的  |
|---------|---------|---------|
|データの保護|Azure Backup|クラウド内のデータと VM をバックアップする|
|環境を保護する|Azure Security Center|

::: zone target="docs"

## <a name="azure-backup"></a>Azure Backup

::: zone-end
::: zone target="chromeless"

## <a name="azure-backuptabupdbackupatemanagement"></a>[Azure Backup](#tab/UpdbackupateManagement)

::: zone-end

Azure Backup は、Microsoft Cloud のデータのバックアップ (または保護) と復旧に使用できる、Azure ベースのサービスです。 Azure Backup は、既存のオンプレミスまたはオフサイトのバックアップ ソリューションを、信頼性の高い、セキュリティで保護された、コスト競争力のあるクラウド ベースのソリューションに置き換えます。 また、1 つの一貫したソリューションを通じてオンプレミスの資産を保護および復旧するためにも使用できます。

### <a name="enable-backup-for-an-azure-vm"></a>Azure VM のバックアップを有効にする

1. Azure Portal で、 **[仮想マシン]** を選択し、レプリケートする VM を選択します。
1. **[操作]** で、 **[バックアップ]** を選択します。
1. Recovery Services コンテナーを作成するか、または既存のコンテナーを選択します。
1. **[Create (or edit) a new policy]\(新しいポリシーを作成 (または編集) する)** を選択します。
1. スケジュールと保持期間を構成します。
1. **[OK]** を選択します。
1. **[バックアップの有効化]** を選択します。

::: zone target="chromeless"

::: form action="OpenBlade[#blade/HubsExtension/Resources/resourceType/Microsoft.Compute%2FVirtualMachines]" submitText="Go to Virtual Machines" :::

::: zone-end

::: zone target="docs"

[概要](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup)

## <a name="azure-site-recovery"></a>Azure Site Recovery

::: zone-end
::: zone target="chromeless"

## <a name="azure-site-recoverytabsiterecovery"></a>[Azure Site Recovery](#tab/siterecovery)

::: zone-end

Azure Site Recovery は、ディザスター リカバリー戦略の重要なコンポーネントです。

Azure Site Recovery サービスを使用すると、プライマリ Azure リージョンでホストされた仮想マシンとワークロードを、セカンダリ リージョンでホストされたコピーにレプリケートできます。 プライマリ リージョンで障害が発生した場合は、セカンダリ リージョンで実行されているコピーにフェールオーバーし、そこから引き続きアプリケーションやサービスにアクセスできます。 この復旧に対するプロアクティブなアプローチにより、復旧時間が大幅に短縮されます。 復旧環境が不要になった場合は、運用環境のトラフィックを元の環境に戻すことができます。

### <a name="replicate-an-azure-vm-to-another-region-with-site-recovery-service"></a>Site Recovery サービスを使用して Azure VM を別のリージョンにレプリケートする

次の手順では、Site Recovery サービスを使用して Azure VM を別のリージョンに (Azure 間で) レプリケートするプロセスの概要を説明しています。

>
> [!TIP]
> シナリオによっては、正確な手順が少し異なることがあります。
>

### <a name="enable-replication-for-the-azure-vm"></a>Azure VM のレプリケーションの有効化

1. Azure Portal で、 **[仮想マシン]** を選択し、レプリケートする VM を選択します。
1. **[操作]** で、 **[ディザスター リカバリー]** を選択します。
1. **[ディザスター リカバリーの構成]**  >  **[ターゲット リージョン]** で、レプリケート先のターゲット リージョンを選択します。
1. このクイック スタートでは、その他は既定の設定をそのまま使用します。
1. **[レプリケーションを有効にする]** を選択します。これで、VM のレプリケーションを有効にするジョブが開始されます。

::: zone target="chromeless"

::: form action="OpenBlade[#blade/HubsExtension/Resources/resourceType/Microsoft.Compute%2FVirtualMachines]" submitText="Go to Virtual Machines" :::

::: zone-end

### <a name="verify-settings"></a>設定の確認

レプリケーション ジョブが完了したら、レプリケーションの状態を確認し、レプリケーションの正常性を検証した後、デプロイをテストできます。

1. VM のメニューで、 **[ディザスター リカバリー]** を選択します。
2. レプリケーションの正常性、作成された復旧ポイント、およびマップ上のソース リージョンとターゲット リージョンを検証します。

::: zone target="chromeless"

::: form action="OpenBlade[#blade/HubsExtension/Resources/resourceType/Microsoft.Compute%2FVirtualMachines]" submitText="Go to Virtual Machines" :::

::: zone-end

### <a name="learn-more"></a>詳細情報

- [Azure Site Recovery の概要](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview)
- [Azure VM を別のリージョンにレプリケートする](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-quickstart)
