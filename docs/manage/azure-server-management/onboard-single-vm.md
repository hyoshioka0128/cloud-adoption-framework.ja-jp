---
title: VM 上のサーバー管理サービスを有効にする
description: Azure 向けのクラウド導入フレームワークを使用して、単一の VM 上で Azure サーバー管理サービスを有効にする方法を学習します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 05/10/2019
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: operate
ms.openlocfilehash: 65dc5c6830fd5e68c9c8a23c5b796eea18f21c3c
ms.sourcegitcommit: 011525720bd9e2d9bcf03a76f371c4fc68092c45
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88572002"
---
# <a name="enable-server-management-services-on-a-single-vm-for-evaluation"></a>評価のために単一の VM 上のサーバー管理サービスを有効にする

評価のために単一の VM 上のサーバー管理サービスを有効にする方法を説明します。

> [!NOTE]
> VM 上に Azure 管理サービスを実装する前に、必須の [Log Analytics ワークスペースと Azure Automation アカウント](./prerequisites.md#create-a-workspace-and-automation-account)を作成します。

Azure portal で、個々の仮想マシンを Azure サーバー管理サービスにオンボードするだけです。 これらのサービスをオンボードする前に、それらに慣れてしておくことができます。 VM インスタンスを選択すると、[管理ツールとサービス](./tools-services.md)の一覧のすべてのソリューションが、 **[操作]** メニューまたは **[監視]** メニューに表示されます。 ソリューションを選択し、ウィザードの指示に従ってオンボードできます。

![Azure portal 内の仮想マシン設定のスクリーンショット](./media/onboarding-single-vm.png)

## <a name="related-resources"></a>関連リソース

これらのソリューションを個々の VM にオンボードする方法の詳細については、以下を参照してください。

- [Azure 仮想マシンから Update Management、Change Tracking、および Inventory ソリューションをオンボードする](/azure/automation/automation-onboard-solutions-from-vm)
- [Azure Monitoring for VMs をオンボードする](/azure/azure-monitor/insights/vminsights-enable-single-vm)

## <a name="next-steps"></a>次のステップ

Azure Policy を使用して Azure VM を大規模にオンボードする方法を確認します。

> [!div class="nextstepaction"]
> [サブスクリプション用の Azure 管理サービスを構成する](./onboard-at-scale.md)
