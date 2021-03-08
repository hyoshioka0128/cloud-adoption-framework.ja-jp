---
title: IP アドレス指定を計画する
description: Azure 内の IP アドレス指定に関する主要な設計上の考慮事項と推奨事項について説明します。
author: JefferyMitchell
ms.author: brblanch
ms.date: 02/18/2021
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.custom: think-tank
ms.openlocfilehash: 8051775c1010bdb5ea4ae79883414bc802607375
ms.sourcegitcommit: b8f8b7631aabaab28e9705934bf67dad15e3a179
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101800098"
---
<!-- docutune:ignore "Azure VPN Gateway" -->

# <a name="plan-for-ip-addressing"></a>IP アドレス指定を計画する

組織では、オンプレミスの場所と Azure リージョンの間で IP アドレス空間が重複しないように、Azure での IP アドレスを計画することが重要です。

**設計上の考慮事項:**

- オンプレミスと Azure リージョンの間で IP アドレス空間が重複すると、競合の大きな問題が生じます。

- 仮想ネットワークを作成した後で、アドレス空間を追加できます。 このプロセスでは、ピアリングを削除して再作成する必要があるため、既に仮想ネットワークが仮想ネットワーク ピアリングを使用して別の仮想ネットワークに接続されている場合、停止が必要になります。

- Azure では、各サブネット内に 5 つの IP アドレスが予約されています。 仮想ネットワークとそれに含まれるサブネットのサイズを決定するときは、それを考慮してください。

- 一部の Azure サービスでは、[専用のサブネット](/azure/virtual-network/virtual-network-for-azure-services#services-that-can-be-deployed-into-a-virtual-network)が必要になります。 このようなサービスとしては、Azure Firewall や Azure VPN Gateway などがあります。

- サブネットを特定のサービスにデリゲートして、サブネット内にサービスのインスタンスを作成することができます。

**設計上の推奨事項:**

- Azure リージョンとオンプレミスの場所の間で、重複しない IP アドレス空間を事前に計画します。

- プライベート インターネットに対するアドレス割り当てから IP アドレスを使用します (RFC 1918)。

- 使用できるプライベート IP アドレスに制限がある環境では (RFC 1918)、IPv6 の使用を検討します。

- IP アドレス空間が無駄にならないよう、不必要に大きな仮想ネットワーク (例: `/16`) を作成しないでください。

- 前もって必要なアドレス空間を計画することなく仮想ネットワークを作成することはやめてください。 アドレス空間を追加すると、仮想ネットワーク ピアリングを介して仮想ネットワークに接続した後で、停止が発生します。

- 仮想ネットワークにはパブリック IP アドレスを使用しないでください。パブリック IP アドレスがお客様の組織に属していない場合は特にそうです。
