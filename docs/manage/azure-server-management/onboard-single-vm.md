---
title: 評価のために単一の VM 上のサーバー管理サービスを有効にする
titleSuffix: Microsoft Cloud Adoption Framework for Azure
description: 評価のために単一の VM 上のサーバー管理サービスを有効にする
author: BrianBlanchard
ms.author: brblanch
ms.date: 05/10/2019
ms.topic: article
ms.service: cloud-adoption-framework
ms.subservice: operate
ms.openlocfilehash: 6dbc0dce4e129a785ddd3ae735115a211bf04dee
ms.sourcegitcommit: 3669614902627f0ca61ee64d97621b2cfa585199
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2019
ms.locfileid: "73656541"
---
# <a name="enable-server-management-services-on-a-single-vm-for-evaluation"></a>評価のために単一の VM 上のサーバー管理サービスを有効にする

評価のために単一の VM 上のサーバー管理サービスを有効にする方法を説明します。

> [!NOTE]
> VM 上に Azure 管理サービスを実装する前に、必須の [Log Analytics ワークスペースと Azure Automation アカウント](./prerequisites.md#create-a-workspace-and-automation-account)を作成します。

Azure portal で、個々の仮想マシンを Azure サーバー管理サービスにオンボードするだけです。 これらのサービスをオンボードする前に、それらに慣れてしておくことができます。 VM インスタンスを選択すると、[管理ツールとサービス](./tools-services.md)の一覧のすべてのソリューションが、 **[操作]** メニューまたは **[監視]** メニューに表示されます。 ソリューションを選択し、ウィザードの指示に従ってオンボードできます。

![Azure portal 内の仮想マシン設定のスクリーンショット](./media/onboarding-single-vm.png)

## <a name="related-resources"></a>関連リソース

これらのソリューションを個々の VM にオンボードする方法の詳細については、以下を参照してください。

- [Azure 仮想マシンから Update Management、Change Tracking、および Inventory ソリューションをオンボードする](https://docs.microsoft.com/azure/automation/automation-onboard-solutions-from-vm)
- [Azure Monitoring for VMs をオンボードする](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-single-vm)

## <a name="next-steps"></a>次の手順

Azure Policy を使用して Azure VM を大規模にオンボードする方法を確認します。

> [!div class="nextstepaction"]
> [サブスクリプション用の Azure 管理サービスを構成する](./onboard-at-scale.md)
