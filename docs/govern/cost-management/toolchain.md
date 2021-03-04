---
title: Azure でのコスト管理ツール
description: コスト管理規範をサポートするポリシーとプロセスを成熟させるのに、Azure ネイティブ ツールがどのように役立つかをご紹介します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 09/17/2019
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: govern
ms.custom: internal
ms.openlocfilehash: ac9ece45ffc0a0fb1f0ef65385dd7f4372d6e73e
ms.sourcegitcommit: b8f8b7631aabaab28e9705934bf67dad15e3a179
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101790835"
---
# <a name="cost-management-tools-in-azure"></a>Azure でのコスト管理ツール

[Cost Management 規範](./index.md)は、[クラウド ガバナンスの 5 つの規範](../governance-disciplines.md)のうちの 1 つです。 この規範では、クラウド支出計画の策定、クラウド予算の割り当て、クラウド予算の監視と適用、コストに関する異常の検出、実際の支出が整合していないときのクラウド ガバナンス計画の調整に焦点が当てられています。

この規範をサポートするポリシーとプロセスを成熟させるのに役立つ Azure ネイティブ ツールの一覧を次に示します。

| ツール | [Azure Portal](https://azure.microsoft.com/features/azure-portal/) | [Azure Cost Management + Billing](/azure/cost-management-billing/cost-management-billing-overview) | [Power BI Desktop コネクタ](/power-bi/connect-data/desktop-connect-azure-cost-management) | [Azure Policy](/azure/governance/policy/overview) |
|---------|---------|---------|---------|---------|
| 予算管理     | いいえ         | はい         | いいえ         | はい         |
| 1 つのリソースでの支出の監視    | はい         | はい         | はい         | いいえ         |
| 複数のリソースでの支出の監視    | いいえ         | はい        | はい         | いいえ         |
| 1 つのリソースでの支出の管理     | はい、手動でサイズ設定         | はい         | いいえ         | はい         |
| 複数のリソースでの支出の適用    | いいえ         | はい         | いいえ         | はい         |
| リソースへの会計メタデータの適用    | いいえ         | いいえ         | いいえ         | はい         |
| 傾向の監視と検出     | はい          | はい        | はい         | いいえ         |
| 異常な支出の検出     | いいえ         | はい        | はい         | いいえ        |
| 逸脱のソーシャライズ     | いいえ        | はい        | はい        | いいえ        |
