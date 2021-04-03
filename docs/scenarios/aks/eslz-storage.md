---
title: AKS のエンタープライズ規模のストレージ
description: AKS のエンタープライズ規模のストレージに関するガイドライン
author: gbowerman
ms.author: brblanch
ms.date: 03/01/2021
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: migrate
ms.custom: think-tank, e2e-aks
ms.openlocfilehash: b73b4f6af1cb05504bd466f2fc48c0e0617ccc15
ms.sourcegitcommit: 5716a8165934bd69d02d9d3641785039196aee3a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "105734435"
---
# <a name="aks-enterprise-scale-storage"></a>AKS のエンタープライズ規模のストレージ

組織またはエンタープライズは、その特定の要件を満たすために、適切な AKS プラットフォーム レベルの機能を設計する必要があります。 具体的には、これらのアプリケーション ワークロードには異なるストレージ要件がある場合があります。 これらのアプリケーションのストレージのニーズを選択する場合は、パフォーマンス、永続性、アクセシビリティ、コストなど、複数の考慮事項があります。 これらのニーズに合わせてさまざまなストレージの概念が Kubernetes によって提供されます。特に、AKS では、アプリケーションごとに適切なサービスを選択する必要があります。

## <a name="design-considerations"></a>設計上の考慮事項

次の要因について検討します。

- アプリケーションでステートフル ワークロードが必要かどうか。
- アプリケーションでシークレットを管理する必要があるかどうか。
- アプリケーションでストレージのスループットを保証する必要があるかどうか、およびこの特性がストレージ層 (Standard、Premium、Ultra)、ストレージ サービス (Azure ディスク、Azure Files、Azure NetApp Files、またはサードパーティのサービス)、およびワーカーのサイズにどのように関連しているか。
- クラスターで実行されている複数の書き込み読み取りアプリケーション。
- マルチテナント機能とノード プールの分離。
- 組織の要件を満たすストレージ クラスの作成。

## <a name="design-recommendations"></a>設計の推奨事項

設計の実証済みプラクティスを次に示します。

- ステートフル ワークロードを検討している場合は、動的ストレージの Azure ディスクまたは Azure Files を準備します。
- マルチポッドで同じストレージに書き込み/読み取りを行う必要がある場合は、Azure Files または Azure NetApp Files のようなサードパーティのサービスをサポートします。
- ほとんどの場合、Premium SSD ストレージをお勧めします。
- 異なるストレージとパフォーマンス特性を必要とするさまざまなワークロードには、ノード プールを使用します。
- ストレージ クラスを作成します。
