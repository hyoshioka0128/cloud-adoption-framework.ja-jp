---
title: アプリケーション配信の計画
description: セキュリティで保護された方法でのアプリケーションの配信に関する主要な設計上の考慮事項と推奨事項について説明します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 02/18/2021
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.custom: internal
ms.openlocfilehash: b6813c18df70884af187c24d779af50dbd07c221
ms.sourcegitcommit: b8f8b7631aabaab28e9705934bf67dad15e3a179
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101801254"
---
# <a name="plan-for-application-delivery"></a>アプリケーション配信の計画

このセクションでは、内部および外部に公開されるアプリケーションを、安全で拡張性と可用性の高い方法で配信するための主な推奨事項について説明します。

**設計上の考慮事項:**

- Azure Load Balancer (内部およびパブリック) では、リージョン レベルでのアプリケーションの配信に対して高可用性が提供されます。

- Azure Application Gateway を使用すると、リージョン レベルで HTTP/S アプリケーションを安全に配信できます。

- Azure Front Door を使用すると、Azure リージョン間で高可用性の HTTP/S アプリケーションを安全に配信できます。

- Azure Traffic Manager を使用すると、グローバル アプリケーションを配信できます。

**設計上の推奨事項:**

- 内部に公開されるアプリケーションと外部に公開されるアプリケーションの両方について、ランディング ゾーン内でアプリケーションの配信を実行します。

- HTTP/S アプリケーションのセキュリティ保護された配信を行うには、Application Gateway v2 を使用し、WAF の保護とポリシーを有効にします。

- HTTP/S アプリケーションのセキュリティに Application Gateway v2 を使用できない場合は、パートナーの NVA を使用します。

- 受信 HTTP/S 接続に使用される Azure Application Gateway v2 またはパートナーの NVA は、セキュリティ保護対象のアプリケーションと共に、ランディング ゾーンの仮想ネットワーク内にデプロイします。

- ランディング ゾーン内のすべてのパブリック IP アドレスに対し、DDoS Standard 保護プランを使用します。

- 複数の Azure リージョンにまたがるグローバル HTTP/S アプリケーションを配信し、保護するには、Azure Front Door と WAF ポリシーを使用します。

- Front Door と Application Gateway を使用して HTTP/S アプリケーションを保護する場合は、Front Door で WAF ポリシーを使用します。 Application Gateway をロックダウンして、Front Door からのトラフィックのみを受信します。

- HTTP/S 以外のプロトコルにまたがるグローバル アプリケーションを配信するには、Traffic Manager を使用します。
