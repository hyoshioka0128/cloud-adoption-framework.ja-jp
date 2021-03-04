---
title: コスト モデルをデジタル資産に対応付ける
description: Azure やその他のクラウドを最大限に活用するために、透明性と正確性をもってクラウド支出を予測および管理するのに役立つ Azure の料金計算ツールについて説明します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 12/10/2018
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: plan
ms.custom: internal
ms.openlocfilehash: c99f84e3e116a40b0a6dd436ef529340cb240547
ms.sourcegitcommit: b8f8b7631aabaab28e9705934bf67dad15e3a179
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101791532"
---
# <a name="align-cost-models-with-the-digital-estate-to-forecast-cloud-costs"></a>コスト モデルをデジタル資産に対応付けて、クラウドのコストを予測します

デジタル資産を合理化した後、選択したクラウド プロバイダーを使用して、デジタル資産を、相当するコスト モデルに対応付けることができます。 コスト モデルについて説明することは、特定のクラウド プロバイダーに限定しないと困難です。 この記事の具体的な例を提供するには、Azure は、想定したクラウド プロバイダーです。

Azure の料金計算ツールを使用すると、透明性と正確性を確保してクラウド支出を管理できるため、Azure や他のクラウドを最大限に活用できます。 クラウドのコストを監視、割り当て、最適化するためのツールが提供されるため、お客様は自信を持って将来の投資を推進することができます。

- [Azure Migrate](/azure/migrate/migrate-services-overview):Azure Migrate は、おそらく、コスト モデルを対応付けるうえで最もコスト効率に優れたアプローチです。 このツール 1 つで、[デジタル資産のインベントリ](./inventory.md)、[限定的な合理化](./rationalize.md)、コスト計算に対応できます。

- [総保有コスト (TCO) 計算ツール](https://azure.microsoft.com/pricing/tco/calculator/):Azure クラウド プラットフォームを使用して、オンプレミス インフラストラクチャの総保有コストを削減します。 Azure の TCO 計算ツールを使用して、アプリケーション ワークロードを Azure に移行することで実現できるコスト節約額を推定します。 お使いのオンプレミス環境の簡単な説明を指定するだけで、即座にレポートが作成されます。

- [Azure 料金計算ツール](https://azure.microsoft.com/pricing/calculator/):料金計算ツールを使用して、予想される毎月の請求額を見積もります。 課金ポータルを使用して、いつでも実際のアカウントの使用状況と課金を追跡できます。 設定した金額を超えたときに通知を希望する場合は、自動電子メール課金アラートを設定します。

- [Azure Cost Management および Billing](/azure/cost-management-billing/cost-management-billing-overview):Azure Cost Management + Billing は、Azure や他のクラウド リソースを効果的に使用および管理するのに役立つコスト管理ソリューションです。 アプリケーション プログラム インターフェイス (API) を使用して、Azure、アマゾン ウェブ サービス、および Google Cloud Platform からクラウドの利用状況と課金データを収集します。 このデータを用いて、クラウド プラットフォーム全体でのリソースの使用量やコストを、一元化されたビューで完全に把握できます。 クラウドの使用量とクラウドの傾向を継続的に監視します。 予算を基準にして実際のクラウド支出を追跡し、予算オーバーを回避します。 通常とは異なる支出や、非効率的な使用を検知します。 履歴データを利用すれば、クラウドの使用状況や支出に対する予測の正確性が高まります。
