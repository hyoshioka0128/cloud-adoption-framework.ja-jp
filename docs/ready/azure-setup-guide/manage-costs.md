---
title: Azure リソースのコストと課金を管理する
description: Azure 向けのクラウド導入フレームワークを使用して請求書の内容を理解し、Azure リソースの予算と支払を設定する方法について学習します。
author: dchimes
ms.author: kfollis
ms.date: 04/09/2019
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.custom: fasttrack-edit, AQC, setup
ms.localizationpriority: high
ms.openlocfilehash: e7d04e91c8ec5a97d302d47ed5c52e7d58e6aae5
ms.sourcegitcommit: 84d7bfd11329eb4c151c4c32be5bab6c91f376ed
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2020
ms.locfileid: "86233139"
---
<!-- cSpell:ignore dchimes -->

# <a name="manage-costs-and-billing-for-your-azure-resources"></a>Azure リソースのコストと課金を管理する

コスト管理とは、ビジネスに関連するコストを効率的に計画して制御するプロセスです。 コストの管理タスクは、一般に、財務、管理、およびアプリのチームが実行します。 Azure Cost Management および Billing を利用すると、コストを考慮して計画を立てることができます。 また、効果的にコストを分析し、クラウド支出を最適化する措置を取ることにも役立ちます。

クラウド コスト管理プロセスを組織全体に統合することの詳細については、クラウド導入フレームワークの、[事業単位、環境、プロジェクトをまたいでコストを追跡する](../azure-best-practices/track-costs.md)方法に関する記事を参照してください。

## <a name="manage-your-costs-with-azure-cost-management-and-billing"></a>Azure Cost Management および Billing でコストを管理する

Azure Cost Management および Billing には、コストの予測と管理に役立ついくつかの方法が用意されています。

- **[クラウド コストの分析]** は、コストの調査と分析に役立ちます。 アカウントの集計されたコストを表示したり、時間の経過に沿って累積コストを表示することができます。
- **[予算による監視]** では、予算を作成し、それを超えそうになったときに警告するアラートを構成することができます。
- **[推奨事項による最適化]** では、アイドル リソースや使用率が低いリソースを識別できるため、無駄を減らすための措置を取ることができます。
- **[請求書と支払いの管理]** ではクラウド投資を視覚化できます。

::: zone target="docs"

### <a name="predict-and-manage-costs"></a>コストの予測と管理

1. [[コストの管理と請求]](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/Overview) に移動します。
1. **[コスト管理]** を選択します。
1. クラウド コストの分析と最適化に役立つ機能を見つけます。

### <a name="manage-invoices-and-payment-methods"></a>請求書と支払い方法の管理

1. [[コストの管理と請求]](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/Overview) に移動します。
1. 左ウィンドウの**課金**のセクションから、 **[請求書]** または **[支払い方法]** を選択します。

## <a name="billing-and-subscription-support"></a>課金とサブスクリプションのサポート

Azure のお客様は、課金とサブスクリプションのサポートに毎日 24 時間アクセスできます。 Azure の利用状況を把握するためにサポートが必要な場合は、サポート リクエストを作成します。

### <a name="create-a-support-request"></a>サポート要求の作成

新しいサポート リクエストを送信するには:

1. [[ヘルプとサポート]](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) に移動します。
1. **[新しいサポート リクエスト]** を選択します。

### <a name="view-a-support-request"></a>サポート リクエストの表示

サポート リクエストとその状態を表示するには:

1. [[ヘルプとサポート]](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) に移動します。
1. **[すべてのサポート リクエスト]** を選択します。

## <a name="learn-more"></a>詳細情報

詳細については、次を参照してください。

- [Azure の課金とコスト管理に関するドキュメント](https://docs.microsoft.com/azure/billing)
- [クラウド導入フレームワーク:事業単位、環境、プロジェクトをまたいでコストを追跡する](../azure-best-practices/track-costs.md)
- [クラウド導入フレームワーク:Cost Management の規範](../../govern/cost-management/index.md)

::: zone-end

::: zone target="chromeless"

## <a name="actions"></a>Actions

**コストの予測と管理**

1. **[コストの管理と請求]** に移動します。
1. **[コスト管理]** を選択します。

**請求書と支払い方法の管理**

1. **[コストの管理と請求]** に移動します。
1. 左ウィンドウの**課金**のセクションから、 **[請求書]** または **[支払い方法]** を選択します。

::: form action="OpenBlade[#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/Overview]" submitText="Go to Cost Management + Billing" ::: form-end

**課金とサブスクリプションのサポート**

Azure のお客様は、課金とサブスクリプションのサポートに毎日 24 時間アクセスできます。 Azure の利用状況を把握するためにサポートが必要な場合は、サポート リクエストを作成します。

**サポート リクエストの作成**

新しいサポート リクエストを送信するには:

1. **[ヘルプとサポート]** に移動します。
2. **[新しいサポート リクエスト]** を選択します。

**サポート リクエストの表示**サポート リクエストとその状態を表示するには:

1. **[ヘルプとサポート]** に移動します。
2. **[すべてのサポート リクエスト]** を選択します。

::: form action="OpenBlade[#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview]" submitText="Go to Help + Support" ::: form-end

::: zone-end
