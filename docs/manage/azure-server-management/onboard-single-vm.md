---
title: 評価のために単一の VM 上の管理サービスを有効にする
titleSuffix: Microsoft Cloud Adoption Framework for Azure
description: 評価のために単一の VM 上の管理サービスを有効にする
author: BrianBlanchard
ms.author: brblanch
ms.date: 05/10/2019
ms.topic: article
ms.service: cloud-adoption-framework
ms.subservice: operate
ms.openlocfilehash: e9d5e17e87d79d8d1fdf7239298a973959103a37
ms.sourcegitcommit: 443c28f3afeedfbfe8b9980875a54afdbebd83a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/16/2019
ms.locfileid: "71031329"
---
# <a name="enable-management-services-on-a-single-vm-for-evaluation"></a>評価のために単一の VM 上の管理サービスを有効にする

評価のために単一の VM 上の管理サービスを有効にする方法を説明します。

> [!NOTE]
> 仮想マシンを Azure 管理サービスにオンボードする前に、必須の [Log Analytics ワークスペースと Azure Automation アカウント](./prerequisites.md#create-a-workspace-and-automation-account)を作成します。

Azure portal では、個々の仮想マシン (VM) を Azure サーバー管理サービスに簡単にオンボードできます。 このポータルでは、VM にオンボードする前に、これらのサービスについて理解を深めることができます。 VM インスタンスを選択した場合、[管理ツールとサービス](./tools-services.md)の一覧内で説明されているすべてのソリューションが、 **[操作]** メニューまたは **[監視]** メニューに表示されます。 各ソリューションを選択し、ウィザードの指示に従ってオンボードできます。

![Azure portal 内の仮想マシン設定のスクリーンショット](./media/onboarding-single-vm.png)

## <a name="related-resources"></a>関連リソース

各ソリューションへの個々の VM のオンボードについて詳しくは、以下をご覧ください。

- [Azure 仮想マシンから Update Management、Change Tracking、および Inventory ソリューションをオンボードする](https://docs.microsoft.com/azure/automation/automation-onboard-solutions-from-vm)
- [VM に対して Azure Monitor をオンボードする](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-single-vm)

## <a name="next-steps"></a>次の手順

Azure Policy を使用して Azure VM を大規模にオンボードする方法を確認します。

> [!div class="nextstepaction"]
> [サブスクリプション用の Azure 管理サービスを構成する](./onboard-at-scale.md)
