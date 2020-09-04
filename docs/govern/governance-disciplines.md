---
title: クラウド ガバナンスの 5 つの規範
description: Azure 向けクラウド導入フレームワークを使用して、コスト管理、デプロイ高速化、ID ベースライン、リソースの整合性、およびセキュリティ ベースラインの各規範について学習します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 09/17/2019
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: govern
ms.custom: governance
ms.openlocfilehash: 15da317061924f0c631877a7a7a0430d8e336038
ms.sourcegitcommit: 07d56209d56ee199dd148dbac59671cbb57880c0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88881140"
---
# <a name="the-five-disciplines-of-cloud-governance"></a>クラウド ガバナンスの 5 つの規範

<!-- docutune:casing "Disciplines of Cloud Governance" "Cost Management" "Deployment Acceleration" "Identity Baseline" "Resource Consistency" "Security Baseline" -->

|  |  |
|--|--|
| ビジネス プロセスまたは技術プラットフォームの変更にはリスクが伴います。 クラウド カストディアンと呼ばれることもあるメンバーで構成されるクラウド ガバナンス チームの任務は、リスクを軽減し、導入やイノベーションの作業をできるだけ中断させないようにすることです。 <br><br> これらの意思決定の指針には、選択されているクラウド プラットフォームに関係なく、[企業ポリシーの策定](./corporate-policy.md)と[クラウド ガバナンスの 5 つの規範](#disciplines-of-cloud-governance)に重点が置かれた、クラウド導入フレームワーク ガバナンス モデルが使用されます。 [アクションにつながる設計ガイド](./guides/index.md)は、Azure サービスを使用してこのモデルを実証します。 以下では、クラウド導入フレームワーク ガバナンス モデルの規範について説明します。 | <br><br> [![クラウド導入フレームワーク ガバナンス モデルの図: 企業のポリシーとガバナンスの規範](../_images/operational-transformation-govern-thumbnail.png)](../_images/operational-transformation-govern-large.png#lightbox) <br> "*図 1:企業ポリシーとクラウド ガバナンスの 5 つの規範。* |

## <a name="disciplines-of-cloud-governance"></a>クラウド ガバナンスの規範

どのようなクラウド プラットフォームにも、ポリシーを周知したりツールチェーンを調整したりするのに役立つ共通のガバナンス規範があります。 これらの規範は、クラウド プラットフォームの枠を超えた企業ポリシーの自動化と実施の適切なレベルに関する意思決定の指針です。

|  |  |
|--|--|
| <br> ![Cost Management](../_images/govern/cost-management.png) | <br> [コスト管理](./cost-management/index.md):コストはクラウド ユーザーの最大の関心事です。 すべてのクラウド プラットフォームに対応した、コスト管理のためのポリシーを策定します。 |
| <br> ![セキュリティ ベースライン](../_images/govern/security-baseline.png) | <br> [セキュリティ ベースライン](./security-baseline/index.md):セキュリティは複雑なテーマであり、会社ごとに独特です。 セキュリティ要件が確立されると、クラウド ガバナンスのポリシーと実施により、それらの要件がネットワーク、データ、資産の構成全体に適用されます。|
| <br> ![ID ベースライン](../_images/govern/identity-baseline.png) | <br> [ID ベースライン](./identity-baseline/index.md):ID 要件の適用が一貫性を欠いた場合、侵害のリスクが高まるおそれがあります。 ID ベースラインの規範では、クラウド導入作業全体で ID が一貫して適用されることを保証することに焦点が当てられています。 |
| <br> ![リソースの整合性](../_images/govern/resource-consistency.png) | <br> [リソースの整合性](./resource-consistency/index.md):クラウドの運用はリソース構成の一貫性に依存します。 ガバナンス ツールを使用して、一貫した方法でリソースを構成し、オンボーディング、ドリフト、検出可能性、および復旧に関連したリスクを管理できます。 |
| <br> ![デプロイ高速化](../_images/govern/deployment-acceleration.png) | <br> [デプロイ高速化](./deployment-acceleration/index.md):デプロイと構成のアプローチにおける集中化、標準化、一貫性により、ガバナンスのプラクティスが改善されます。 クラウド ベースのガバナンス ツールによって提供されれば、デプロイ アクティビティの迅速化に寄与するクラウドの要因となります。 |
