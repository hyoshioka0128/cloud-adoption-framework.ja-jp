---
title: コスト モデルをデジタル資産に対応付ける
titleSuffix: Microsoft Cloud Adoption Framework for Azure
description: コスト モデルをデジタル資産に対応付けて、クラウドのコストを予測します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 12/10/2018
ms.topic: guide
ms.service: cloud-adoption-framework
ms.subservice: plan
ms.custom: governance
ms.openlocfilehash: c1fb4224c7a8865785b795ad8f28bfa04779ce89
ms.sourcegitcommit: a26c27ed72ac89198231ec4b11917a20d03bd222
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/06/2019
ms.locfileid: "70837817"
---
# <a name="align-cost-models-with-the-digital-estate-to-forecast-cloud-costs"></a>コスト モデルをデジタル資産に対応付けて、クラウドのコストを予測します

デジタル資産を合理化した後、選択したクラウド プロバイダーを使用して、デジタル資産を、相当するコスト モデルに対応付けることができます。 コスト モデルについて説明することは、特定のクラウド プロバイダーに限定しないと困難です。 この記事の具体的な例を提供するには、Azure は、想定したクラウド プロバイダーです。

Azure の料金計算ツールを使用すると、透明性と正確性を確保してクラウド支出を管理できるため、Azure や他のクラウドを最大限に活用できます。 クラウドのコストを監視、割り当て、最適化するためのツールが提供されるため、お客様は自信を持って将来の投資を推進することができます。

- [Azure Migrate](/azure/migrate/migrate-overview):Azure Migrate は、おそらく、コスト モデルを対応付けるうえで最もコスト効率に優れたアプローチです。 このツールでは、デジタル資産の[インベントリ](inventory.md)、[限定的な合理化](rationalize.md)、コスト計算に 1 つのツールで対応できます。

- [総保有コスト (TCO) 計算ツール](https://azure.com/tco):Azure クラウド プラットフォームを使用して、オンプレミス インフラストラクチャの総保有コストを削減します。 Azure の TCO 計算ツールを使用して、アプリケーション ワークロードを Azure に移行することで実現できるコスト節約額を推定します。 お使いのオンプレミス環境の簡単な説明を指定するだけで、即座にレポートが作成されます。

- [Azure 料金計算ツール](https://azure.microsoft.com/pricing):料金計算ツールを使用して、予想される毎月の請求額を見積もります。 課金ポータルを使用して、いつでも実際のアカウントの使用状況と課金を追跡できます。 設定した金額を超えたときに通知を希望する場合は、自動電子メール課金アラートを設定します。

- [Azure Cost Management](https://azure.microsoft.com/services/cost-management):Microsoft の子会社である Cloudyn からライセンス供与された Azure Cost Management は、Azure や他のクラウド リソースの使用と管理を効率的に実行するために役立つマルチクラウド コスト管理ソリューションです。 アプリケーション プログラム インターフェイス (API) を使用して、Azure、アマゾン ウェブ サービス、および Google Cloud Platform からクラウドの利用状況と課金データを収集します。 このデータを用いて、クラウド プラットフォーム全体でのリソースの使用量やコストを、一元化されたビューで完全に把握できます。 クラウドの使用量とクラウドの傾向を継続的に監視します。 予算を基準にして実際のクラウド支出を追跡し、予算オーバーを回避します。 通常とは異なる支出や、非効率的な使用を検知します。 履歴データを利用すれば、クラウドの使用状況や支出に対する予測の正確性が高まります。
