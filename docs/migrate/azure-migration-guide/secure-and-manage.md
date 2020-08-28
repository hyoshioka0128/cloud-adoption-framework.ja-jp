---
title: セキュリティ保護と管理
description: Azure に移行した後、環境の管理に使用できるセキュリティと管理の方法について詳しく説明します。
author: matticusau
ms.author: mlavery
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: migrate
ms.custom: fasttrack-new, AQC
ms.localizationpriority: high
ms.openlocfilehash: afde995e27d37114727fafa540f0f1232c49e8b3
ms.sourcegitcommit: 8b5fdb68127c24133429b4288f6bf9004a1d1253
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2020
ms.locfileid: "88848127"
---
<!-- markdownlint-disable MD024 MD025 DOCSMD001 -->

# <a name="secure-and-manage"></a>セキュリティ保護と管理

環境を Azure に移行したら、セキュリティや、環境を管理するために使用される方法を検討することが重要です。 Azure では、ソリューションでこれらのニーズを満たすための多くの機能が提供されます。

## <a name="azure-monitor"></a>[Azure Monitor](#tab/monitor)

Azure Monitor は、クラウドおよびオンプレミス環境の利用統計情報を収集、分析し、それに対応する包括的なソリューションを提供することにより、アプリケーションの可用性とパフォーマンスを最大化します。 このツールは、ご利用のアプリケーションがどのように実行されているかを把握するのに役立ちます。さらに、このツールにより、そのアプリケーションに影響している問題点およびアプリケーションが依存しているリソースを事前に明らかにしておくことができます。

### <a name="use-and-configure-azure-monitor"></a>Azure Monitor を使用および構成する

1. Azure Portal で **[監視]** に移動します。
2. 概要の **[メトリック]** 、 **[ログ]** 、または **[サービス正常性]** を選択します。
3. 関連する分析情報のいずれかを選択します。

::: zone target="chromeless"

::: form action="OpenBlade[#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/overview]" submitText="Go to Azure Monitor" :::

::: zone-end

::: zone target="docs"

### <a name="learn-more"></a>詳細情報

- [Azure Monitor の概要](/azure/azure-monitor/overview)。

::: zone-end

## <a name="azure-service-health"></a>[Azure Service Health](#tab/serviceHealth)

Azure Service Health は、Azure サービスの問題がユーザーに影響を与えた場合の個人用に設定されたガイダンスとサポートを提供します。 問題を通知してくれるだけでなく、その影響の把握にも役立つほか、問題が解決した場合にはその旨の情報が随時配信されます。 また、お使いのリソースの可用性に影響を及ぼすおそれのある、予定されているメンテナンスや変更に備えることもできます。

Azure Service Health には以下が含まれます。

- **[Azure Status]\(Azure の状態):** Azure サービスの正常性のグローバルなビュー。
- **[サービス正常性]:** Azure サービスの正常性の個人用に設定されたビュー。
- **[リソース正常性]:** Azure サービスによってプロビジョニングされた個々のリソースの正常性のより深いビュー。

組み合わされると、これらのエクスペリエンスにより、Azure の正常性の包括的なビューがユーザーに関連する詳細レベルで表示されます。

### <a name="access-service-health"></a>Service Health にアクセスする

1. Azure Portal で **[監視]** に移動します。
2. **[サービスの正常性]** を選択します。

::: zone target="chromeless"

::: form action="OpenBlade[#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues]" submitText="Go to Service Health" :::

::: zone-end

::: zone target="docs"

### <a name="learn-more"></a>詳細情報

詳細については、「[Azure Service Health](/azure/service-health)」を参照してください。

::: zone-end

## <a name="azure-advisor"></a>[Azure Advisor](#tab/advisor)

Azure Advisor は、ベスト プラクティスに従って Azure デプロイメントを最適化できるようにする、個人用に設定されたクラウド コンサルタントです。 Azure Advisor では、リソース構成と使用量テレメトリを分析します。 その後、Azure の全体的な使用量を削減する機会を探すと同時に、リソースのパフォーマンス、セキュリティ、高可用性を向上させることができるソリューションを提案します。

### <a name="access-azure-advisor"></a>Azure Advisor にアクセスする

1. Azure Portal で **[Advisor]** に移動するか、またはリソースを検索します。
2. **[高可用性]** 、 **[セキュリティ]** 、 **[パフォーマンス]** 、 **[コスト]** を選択します。

::: zone target="chromeless"

::: form action="OpenBlade[#blade/Microsoft_Azure_Expert/AdvisorMenuBlade/overview]" submitText="Go to Azure Advisor" :::

::: zone-end

::: zone target="docs"

### <a name="learn-more"></a>詳細情報

[概要](/azure/advisor/advisor-overview)。

::: zone-end

## <a name="azure-security-center"></a>[Azure Security Center](#tab/security)

Azure Security Center は、データセンターのセキュリティ体制を強化すると共に、クラウド内 (&mdash;Azure 内かどうかにかかわらず&mdash;) だけでなくオンプレミスのハイブリッド ワークロードにわたる Azure Advanced Threat Protection を提供する、統合されたインフラストラクチャ セキュリティ管理システムです。

### <a name="access-azure-security-center"></a>Azure Security Center にアクセスする

1. Azure Portal で **[Security Center]** に移動するか、またはリソースを検索します。
2. **[推奨事項]** を選択します。

::: zone target="chromeless"

::: form action="OpenBlade[#blade/Microsoft_Azure_Security/SecurityMenuBlade/0]" submitText="Go to Security Center" :::

::: zone-end

::: zone target="docs"

### <a name="learn-more"></a>詳細情報

[概要](/azure/security-center/security-center-intro)

::: zone-end

## <a name="azure-backup"></a>[Azure Backup](#tab/backup)

Azure Backup は、Microsoft クラウド内のデータをバックアップ (または保護) および復元するために使用できる Azure ベースのサービスです。 Azure Backup は、既存のオンプレミスまたはオフサイトのバックアップ ソリューションを、信頼性の高い、セキュリティで保護された、コスト競争力のあるクラウド ベースのソリューションに置き換えます。

### <a name="enable-backup-for-an-azure-vm"></a>Azure VM のバックアップを有効にする

1. Azure portal で、 **[仮想マシン]** を選択し、レプリケートする VM を選択します。
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

[概要](/azure/backup/backup-overview)

::: zone-end

## <a name="azure-site-recovery"></a>[Azure Site Recovery](#tab/siteRecovery)

Azure Site Recovery は、移行が完了した後のディザスター リカバリー戦略の重要な要素を形成します。

Azure Site Recovery サービスを使用すると、プライマリ Azure リージョンでホストされた仮想マシンとワークロードを、セカンダリ リージョンでホストされたコピーにレプリケートできます。 プライマリ リージョンで障害が発生した場合は、セカンダリ リージョンで実行されているコピーにフェールオーバーし、そこから引き続きアプリケーションやサービスにアクセスできます。 仮想マシンのプライマリ コピーの停止が再び稼働状態になったら、そこにフェールバックできます。

### <a name="replicate-an-azure-vm-to-another-region-with-site-recovery-service"></a>Site Recovery サービスを使用して Azure VM を別のリージョンにレプリケートする

次の手順では、Site Recovery サービスを使用して Azure VM を別のリージョンに (Azure 間で) レプリケートするプロセスの概要を説明しています。

>
> [!TIP]
> シナリオによっては、正確な手順が少し異なることがあります。
>

### <a name="enable-replication-for-the-azure-vm"></a>Azure VM のレプリケーションの有効化

1. Azure portal で、 **[仮想マシン]** を選択し、レプリケートする VM を選択します。
1. **[操作]** で、 **[ディザスター リカバリー]** を選択します。
1. **[ディザスター リカバリーの構成]**  >  **[ターゲット リージョン]** で、レプリケート先のターゲット リージョンを選択します。
1. このクイック スタートでは、その他は既定の設定をそのまま使用します。
1. **[レプリケーションを有効にする]** を選択します。 これにより VM レプリケーションを有効にするジョブが開始されます。

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

::: zone target="docs"

### <a name="learn-more"></a>詳細情報

- [Azure Site Recovery の概要](/azure/site-recovery/site-recovery-overview)
- [Azure VM を別のリージョンにレプリケートする](/azure/site-recovery/azure-to-azure-quickstart)

::: zone-end
