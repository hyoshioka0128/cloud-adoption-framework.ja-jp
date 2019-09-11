---
title: Azure でのコスト管理ツール
titleSuffix: Microsoft Cloud Adoption Framework for Azure
description: Azure でのコスト管理ツール
author: BrianBlanchard
ms.author: brblanch
ms.date: 02/11/2019
ms.topic: guide
ms.service: cloud-adoption-framework
ms.subservice: govern
ms.custom: governance
ms.openlocfilehash: 32c05e04c224f88008ded6e9fe2c69bb6095d346
ms.sourcegitcommit: a26c27ed72ac89198231ec4b11917a20d03bd222
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/06/2019
ms.locfileid: "70837429"
---
# <a name="cost-management-tools-in-azure"></a>Azure でのコスト管理ツール

[コスト管理](./index.md)は、[5 つあるクラウド ガバナンス規範](../governance-disciplines.md)のうちの 1 つです。 この規範では、クラウド支出計画の策定、クラウド予算の割り当て、クラウド予算の監視と適用、コストに関する異常の検出、実際の支出が整合していないときのクラウド ガバナンス計画の調整に焦点が当てられています。

このガバナンス規範をサポートするポリシーとプロセスを成熟させるのに役立つ Azure ネイティブ ツールの一覧を次に示します。

| ツール | [Azure Portal](https://azure.microsoft.com/features/azure-portal)  | [Azure Cost Management](/azure/cost-management/overview-cost-mgt)  | [Azure EA コンテンツ パック](/power-bi/service-connect-to-azure-enterprise)  | [Azure Policy](/azure/governance/policy/overview) |
|---------|---------|---------|---------|---------|
|Enterprise Agreement が必要     | いいえ         | いいえ         | はい         | いいえ         |
|予算管理     | いいえ         | はい         | いいえ         | はい         |
|1 つのリソースでの支出の監視    | はい         | はい         | はい         | いいえ         |
|複数のリソースでの支出の監視    | いいえ         | 可能        | はい         | いいえ         |
|1 つのリソースでの支出の管理     | はい - 手動でサイズ設定         | はい         | いいえ         | はい         |
|複数のリソースでの支出の適用    | いいえ         | はい         | いいえ         | はい         |
|リソースへの会計メタデータの適用    | いいえ         | いいえ         | いいえ         | はい         |
|傾向の監視と検出     | はい - 制限あり         | はい        | はい         | いいえ         |
|異常な支出の検出     | いいえ         | 可能        | はい         | いいえ        |
|逸脱のソーシャライズ     | いいえ        | 可能        | はい        | いいえ        |
