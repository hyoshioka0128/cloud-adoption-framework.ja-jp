---
title: トラフィックの検査を計画する
description: Azure 仮想ネットワーク内のトラフィックをミラーリングまたはタップすることに関する主要な設計上の考慮事項と推奨事項について確認します。
author: JefferyMitchell
ms.author: brblanch
ms.date: 02/18/2021
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.custom: think-tank
ms.openlocfilehash: dba15dab8463e7708c7f9bf0e45080417886393e
ms.sourcegitcommit: b8f8b7631aabaab28e9705934bf67dad15e3a179
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101801370"
---
# <a name="plan-for-traffic-inspection"></a>トラフィックの検査を計画する

多くの業界では、組織は、詳細な検査と分析のために、Azure のトラフィックがネットワーク パケット コレクターにミラー化されることを望みます。 この要件では、通常、受信と送信のインターネット トラフィックに焦点が当てられています。 このセクションでは、Azure Virtual Network 内のトラフィックをミラーリングまたはタップするときの主な考慮事項と推奨される方法について説明します。

**設計上の考慮事項:**

<!-- docutune:ignore TAP -->

- [Azure 仮想ネットワーク ターミナル アクセス ポイント (TAP)](/azure/virtual-network/virtual-network-tap-overview) はプレビュー段階です。 可用性の詳細については、`azurevnettap@microsoft.com` にお問い合わせください。

- Azure Network Watcher でのパケット キャプチャは一般提供されていますが、キャプチャの最大期間は 5 時間に制限されています。

**設計上の推奨事項:**

Azure 仮想ネットワーク TAP の代わりの手段としては、次のオプションを評価してください。

- キャプチャ ウィンドウは制限されていますが、Network Watcher パケットを使用してキャプチャします。

- 最新バージョンの NSG フロー ログで、必要な詳細レベルが提供されるかどうかを評価します。

- ディープ パケット インスペクションが必要なシナリオには、パートナー ソリューションを使用します。

- トラフィックをミラー化するためのカスタム ソリューションを開発しないでください。 このアプローチは小規模なシナリオでは許容されるかもしれませんが、複雑さとサポートの問題が発生する可能性があるため、大規模に使用することは推奨されません。
