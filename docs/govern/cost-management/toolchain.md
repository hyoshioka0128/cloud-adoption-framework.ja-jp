---
title: Azure でのコスト管理ツール
description: Azure でのコスト管理ツール
author: BrianBlanchard
ms.author: brblanch
ms.date: 09/17/2019
ms.topic: guide
ms.service: cloud-adoption-framework
ms.subservice: govern
ms.custom: governance
ms.openlocfilehash: e35530fbf3a858c000cb78c0c076d7d56d5fbd86
ms.sourcegitcommit: 2362fb3154a91aa421224ffdb2cc632d982b129b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2020
ms.locfileid: "76806409"
---
# <a name="cost-management-tools-in-azure"></a>Azure でのコスト管理ツール

[コスト管理](./index.md)は、[5 つあるクラウド ガバナンス規範](../governance-disciplines.md)のうちの 1 つです。 この規範では、クラウド支出計画の策定、クラウド予算の割り当て、クラウド予算の監視と適用、コストに関する異常の検出、実際の支出が整合していないときのクラウド ガバナンス計画の調整に焦点が当てられています。

このガバナンス規範をサポートするポリシーとプロセスを成熟させるのに役立つ Azure ネイティブ ツールの一覧を次に示します。

| ツール | [Azure Portal](https://azure.microsoft.com/features/azure-portal)  | [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/overview-cost-mgt)  | [Azure EA コンテンツ パック](https://docs.microsoft.com/power-bi/service-connect-to-azure-enterprise)  | [Azure Policy](https://docs.microsoft.com/azure/governance/policy/overview) |
|---------|---------|---------|---------|---------|
|Enterprise Agreement が必要     | いいえ         | いいえ         | はい         | いいえ         |
|予算管理     | いいえ         | はい         | いいえ         | はい         |
|1 つのリソースでの支出の監視    | はい         | はい         | はい         | いいえ         |
|複数のリソースでの支出の監視    | いいえ         | はい        | はい         | いいえ         |
|1 つのリソースでの支出の管理     | はい - 手動でサイズ設定         | はい         | いいえ         | はい         |
|複数のリソースでの支出の適用    | いいえ         | はい         | いいえ         | はい         |
|リソースへの会計メタデータの適用    | いいえ         | いいえ         | いいえ         | はい         |
|傾向の監視と検出     | はい          | はい        | はい         | いいえ         |
|異常な支出の検出     | いいえ         | はい        | はい         | いいえ        |
|逸脱のソーシャライズ     | いいえ        | はい        | はい        | いいえ        |
