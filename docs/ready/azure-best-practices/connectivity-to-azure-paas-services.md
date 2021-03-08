---
title: Azure PaaS サービスへの接続
description: サービス テクノロジとしての Azure プラットフォームへの接続に関する主要な設計上の考慮事項と推奨事項について説明します。
author: JefferyMitchell
ms.author: brblanch
ms.date: 02/18/2021
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.custom: think-tank
ms.openlocfilehash: f2bf97ead19f75a93d6350e8e8f1d4a1c3223094
ms.sourcegitcommit: b8f8b7631aabaab28e9705934bf67dad15e3a179
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101801129"
---
# <a name="connectivity-to-azure-paas-services"></a>Azure PaaS サービスへの接続

これまでの接続セクションを基にして、このセクションでは、Azure PaaS サービスを使用するための推奨される接続方法について説明します。

**設計上の考慮事項:**

- Azure PaaS サービスは通常、パブリック エンドポイントを介してアクセスされます。 ただし、Azure プラットフォームには、そのようなエンドポイントをセキュリティで保護する機能や、完全にプライベートにする機能が用意されています。

  - 仮想ネットワーク インジェクションでは、サポートされるサービスに対する専用のプライベート デプロイが提供されます。 管理プレーンのトラフィックは、依然としてパブリック IP アドレスを通して流れます。

  - [Private Link](/azure/private-link/private-endpoint-overview#private-link-resource) では、プライベート IP アドレスを使用して、Azure PaaS インスタンス、または Azure Load Balancer Standard レベルの内側にあるカスタム サービスへの専用アクセスが提供されます。

  - 仮想ネットワーク サービス エンドポイントでは、選択されたサブネットから選択された PaaS サービスへのサービス レベルのアクセスが提供されます。

- エンタープライズは多くの場合、PaaS サービスに対するパブリック エンドポイントに関する問題があり、適切に軽減する必要があります。

- [サポートされているサービス](/azure/private-link/private-link-overview#availability)の場合、Private Link により、サービス エンドポイントに関するデータの窃盗の問題は解決されます。 代わりの方法として、NVA による送信フィルターを使用して、データの窃盗を緩和するための手順を提供できます。

**設計上の推奨事項:**

- サポートされている Azure サービスに対する仮想ネットワーク インジェクションを使用して、仮想ネットワーク内からそれらを使用できるようにします。

- 仮想ネットワークに挿入された Azure PaaS サービスでは、パブリック IP アドレスを使用して管理プレーンの操作がまだ実行されます。 UDR と NSG を使用して、この通信が仮想ネットワーク内にロックダウンされるようにします。

- [使用可能な場合](/azure/private-link/private-link-overview#availability)は、Private Link を共有 Azure PaaS サービスに対して使用します。 Private Link は、一部のサービスについては一般提供されており、多くのサービスではパブリック プレビュー段階です。

- ExpressRoute プライベート ピアリングを介して、オンプレミスから Azure PaaS サービスにアクセスします。 専用の Azure サービスに対する仮想ネットワーク インジェクション、または使用可能な共有 Azure サービスに対する Azure Private Link を使用します。 仮想ネットワーク インジェクションまたは Private Link を使用できない場合にオンプレミスから Azure PaaS サービスにアクセスするには、Microsoft ピアリングで ExpressRoute を使用します。 この方法により、パブリック インターネット経由での推移を回避できます。

- 仮想ネットワーク内から Azure PaaS サービスへのアクセスをセキュリティで保護するには、仮想ネットワーク サービス エンドポイントを使用しますが、Private Link を使用できず、データ流出の問題が存在しない場合に限ります。 サービス エンドポイントでのデータ窃盗に関する問題に対処するには、NVA のフィルター処理を使用するか、Azure Storage に対する仮想ネットワーク サービス エンドポイント ポリシーを使用します。

- すべてのサブネットにおいて既定で仮想ネットワーク サービス エンドポイントを有効にしないでください。

- NVA フィルター処理を使用している場合を除き、データ窃盗の問題が存在する場合は、仮想ネットワーク サービス エンドポイントを使用しないでください。

- Azure から Azure リソースへの通信を可能にするために強制トンネリングを実装することは推奨されません。
