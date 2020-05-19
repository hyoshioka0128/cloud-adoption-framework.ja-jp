---
title: Azure でのコスト管理ツール
description: コスト管理規範をサポートするポリシーとプロセスを成熟させるのに、Azure ネイティブ ツールがどのように役立つかをご紹介します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 09/17/2019
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: govern
ms.custom: governance
ms.openlocfilehash: 6f2e08a85df87f7973f19ebd83b71de0f2003189
ms.sourcegitcommit: 60d8b863d431b5d7c005f2f14488620b6c4c49be
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2020
ms.locfileid: "83220483"
---
# <a name="cost-management-tools-in-azure"></a>Azure でのコスト管理ツール

[コスト管理規範](./index.md)は、[クラウド ガバナンスの 5 つの規範](../governance-disciplines.md)のうちの 1 つです。 この規範では、クラウド支出計画の策定、クラウド予算の割り当て、クラウド予算の監視と適用、コストに関する異常の検出、実際の支出が整合していないときのクラウド ガバナンス計画の調整に焦点が当てられています。

この規範をサポートするポリシーとプロセスを成熟させるのに役立つ Azure ネイティブ ツールの一覧を次に示します。

<!-- TODO: Content packs are deprecated. -->

| ツール | [Azure Portal](https://azure.microsoft.com/features/azure-portal)  | [Azure Cost Management](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview)  | [Azure EA コンテンツ パック](https://docs.microsoft.com/power-bi/service-connect-to-azure-enterprise)  | [Azure Policy](https://docs.microsoft.com/azure/governance/policy/overview) |
|---------|---------|---------|---------|---------|
| Enterprise Agreement が必要     | いいえ         | いいえ         | はい         | いいえ         |
| 予算管理     | いいえ         | はい         | いいえ         | はい         |
| 1 つのリソースでの支出の監視    | はい         | はい         | はい         | いいえ         |
| 複数のリソースでの支出の監視    | いいえ         | はい        | はい         | いいえ         |
| 1 つのリソースでの支出の管理     | はい - 手動でサイズ設定         | はい         | いいえ         | はい         |
| 複数のリソースでの支出の適用    | いいえ         | はい         | いいえ         | はい         |
| リソースへの会計メタデータの適用    | いいえ         | いいえ         | いいえ         | はい         |
| 傾向の監視と検出     | はい          | はい        | はい         | いいえ         |
| 異常な支出の検出     | いいえ         | はい        | はい         | いいえ        |
| 逸脱のソーシャライズ     | いいえ        | はい        | はい        | いいえ        |
