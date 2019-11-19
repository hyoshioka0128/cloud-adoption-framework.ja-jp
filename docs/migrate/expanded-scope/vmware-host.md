---
title: VMware ホストを使用して移行を高速化する
titleSuffix: Microsoft Cloud Adoption Framework for Azure
description: VMware ホストを使用して移行を高速化する
author: BrianBlanchard
ms.author: brblanch
ms.date: 10/10/2019
ms.topic: guide
ms.service: cloud-adoption-framework
ms.subservice: migrate
ms.openlocfilehash: 724a227407f431e08b5344dfd1280397bfca9b65
ms.sourcegitcommit: bf9be7f2fe4851d83cdf3e083c7c25bd7e144c20
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73566885"
---
# <a name="accelerate-migration-with-vmware-hosts"></a>VMware ホストを使用して移行を高速化する

VMware ホスト全体を移行すると、1 回の移行作業で複数のワークロードと複数の資産を移動できます。 このガイダンスでは、VMware ホストの移行を通じて [Azure 移行ガイド](../azure-migration-guide/index.md)の範囲を拡大します。 この範囲の拡大によって要求される作業のほとんどは、移行作業の前提条件と移行プロセス中に発生します。

## <a name="suggested-prerequisites"></a>推奨される前提条件

最初の VMware ホストを Azure に移行する場合、ID、ネットワーク、および管理の要件を準備するために、複数の前提条件を満たす必要があります。 これらの前提条件が満たされれば、それぞれの追加のホストの移行に要する労力が大幅に減ります。 以下のセクションでは、前提条件の詳細を示しています。

### <a name="secure-your-azure-environment"></a>Azure 環境のセキュリティ保護

Azure 環境で、ロールベースのアクセス制御とネットワーク接続に適したクラウド ソリューションを実装します。 この実装には、[環境のセキュリティ保護のガイド](https://docs.microsoft.com/azure/vmware-cloudsimple/private-cloud-secure?toc=https://docs.microsoft.com/azure/cloud-adoption-framework/toc.json&bc=https://docs.microsoft.com/azure/cloud-adoption-framework/_bread/toc.json)が役立ちます。

### <a name="private-cloud-management"></a>プライベート クラウドの管理

プライベート クラウドの管理を確立するには、2 つの必須タスクと 1 つのオプション タスクがあります。 [プライベート クラウドの特権のエスカレート](https://docs.microsoft.com/azure/vmware-cloudsimple/escalate-privileges?toc=https://docs.microsoft.com/azure/cloud-adoption-framework/toc.json&bc=https://docs.microsoft.com/azure/cloud-adoption-framework/_bread/toc.json)と[ワークロードの DNS および DHCP の設定](https://docs.microsoft.com/azure/vmware-cloudsimple/dns-dhcp-setup?toc=https://docs.microsoft.com/azure/cloud-adoption-framework/toc.json&bc=https://docs.microsoft.com/azure/cloud-adoption-framework/_bread/toc.json)は、どちらも必須のベスト プラクティスです。

[レイヤー 2 のストレッチ ネットワークを使用してワークロードを移行する](https://docs.microsoft.com/azure/vmware-cloudsimple/migration-layer-2-vpn?toc=https://docs.microsoft.com/azure/cloud-adoption-framework/toc.json&bc=https://docs.microsoft.com/azure/cloud-adoption-framework/_bread/toc.json)ことが目的の場合、この 3 番目のベスト プラクティスも必要です。

### <a name="private-cloud-networking"></a>プライベート クラウド ネットワーク

管理要件が確立されたら、次のベスト プラクティスを使用してプライベート クラウド ネットワークを確立できます。

- [プライベート クラウドへの VPN 接続](https://docs.microsoft.com/azure/vmware-cloudsimple/set-up-vpn?toc=https://docs.microsoft.com/azure/cloud-adoption-framework/toc.json&bc=https://docs.microsoft.com/azure/cloud-adoption-framework/_bread/toc.json)
- [ExpressRoute を使用したオンプレミス ネットワーク接続](https://docs.microsoft.com/azure/vmware-cloudsimple/on-premises-connection?toc=https://docs.microsoft.com/azure/cloud-adoption-framework/toc.json&bc=https://docs.microsoft.com/azure/cloud-adoption-framework/_bread/toc.json)
- [ExpressRoute を使用した Azure 仮想ネットワーク接続](https://docs.microsoft.com/azure/vmware-cloudsimple/azure-expressroute-connection?toc=https://docs.microsoft.com/azure/cloud-adoption-framework/toc.json&bc=https://docs.microsoft.com/azure/cloud-adoption-framework/_bread/toc.json)
- [DNS 名前解決の構成](https://docs.microsoft.com/azure/vmware-cloudsimple/on-premises-dns-setup?toc=https://docs.microsoft.com/azure/cloud-adoption-framework/toc.json&bc=https://docs.microsoft.com/azure/cloud-adoption-framework/_bread/toc.json)

### <a name="integration-with-the-cloud-adoption-plan"></a>クラウド導入計画との統合

他の前提条件が満たされたら、各 VMware ホストを[クラウド導入計画](../../plan/template.md)に含める必要があります。 クラウド導入計画内では、移行対象となる各ホストを[個別のワークロード](../../plan/workloads.md)として追加します。 各ワークロード内で、移行する VM を[資産](../../plan/workloads.md)として追加します。 ワークロードと資産を導入計画に一括で追加する方法については、[Excel を使用した作業項目の追加と編集](https://docs.microsoft.com/azure/devops/boards/backlogs/office/bulk-add-modify-work-items-excel?view=azure-devops)に関するページを参照してください。

## <a name="migrate-process-changes"></a>移行プロセスの変更

各イテレーション中に、導入チームは、バックログを使用して、最も優先度の高いワークロードを移行します。 VMware ホストの場合、このプロセスは実際には変わりません。 バックログ上の次のワークロードが VMware ホストの場合、唯一の変更は使用されるツールです。

移行作業では、次のツールを使用できます。

- [ネイティブ VMware ツール](https://docs.microsoft.com/azure/vmware-cloudsimple/migrate-workloads?toc=https://docs.microsoft.com/azure/cloud-adoption-framework/toc.json&bc=https://docs.microsoft.com/azure/cloud-adoption-framework/_bread/toc.json)
- [Azure Data Box](https://docs.microsoft.com/azure/vmware-cloudsimple/migration-using-azure-data-box?toc=https://docs.microsoft.com/azure/cloud-adoption-framework/toc.json&bc=https://docs.microsoft.com/azure/cloud-adoption-framework/_bread/toc.json)

また、次のツールを使用して、ディザスター リカバリー フェールオーバーを通じてワークロードを移行することもできます。

- [ワークロード仮想マシンのバックアップ](https://docs.microsoft.com/azure/vmware-cloudsimple/backup-workloads-veeam?toc=https://docs.microsoft.com/azure/cloud-adoption-framework/toc.json&bc=https://docs.microsoft.com/azure/cloud-adoption-framework/_bread/toc.json)
- [Zerto を使用したディザスター リカバリー サイトとしてのプライベート クラウドの構成](https://docs.microsoft.com/azure/vmware-cloudsimple/disaster-recovery-zerto?toc=https://docs.microsoft.com/azure/cloud-adoption-framework/toc.json&bc=https://docs.microsoft.com/azure/cloud-adoption-framework/_bread/toc.json)
- [VMware SRM を使用したディザスター リカバリー サイトとしてのプライベート クラウドの構成](https://docs.microsoft.com/azure/vmware-cloudsimple/disaster-recovery-site-recovery-manager?toc=https://docs.microsoft.com/azure/cloud-adoption-framework/toc.json&bc=https://docs.microsoft.com/azure/cloud-adoption-framework/_bread/toc.json)

## <a name="next-steps"></a>次の手順

範囲拡大チェックリストに戻り、移行方法が完全に調整されていることを確認します。

> [!div class="nextstepaction"]
> [範囲拡大チェックリスト](./index.md)
