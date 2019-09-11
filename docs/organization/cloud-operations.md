---
title: クラウド運用機能
titleSuffix: Microsoft Cloud Adoption Framework for Azure
description: クラウド運用機能の構成について説明します
author: BrianBlanchard
ms.author: brblanch
ms.date: 09/10/2019
ms.topic: article
ms.service: cloud-adoption-framework
ms.subservice: organize
ms.custom: organize
ms.openlocfilehash: c0b5ca5dabb852baf3925917929e07b2a497ca19
ms.sourcegitcommit: 5846ed4d0bf1b6440f5e87bc34ef31ec8b40b338
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/11/2019
ms.locfileid: "70906273"
---
# <a name="cloud-operation-capabilities"></a>クラウド運用機能

ビジネスの変革は、クラウドの導入によって有効になる場合があります。 ただし、利益を実現できるのは、クラウドにデプロイされるワークロードがパフォーマンス目標に沿って動作する場合だけです。 追加のワークロードによってクラウド テクノロジが採用されると、追加の運用キャパシティが必要になります。

従来の IT 運用では、さまざまな低レベルの技術資産に対して現状の運用を維持することに重点を置く必要がありました。 ストレージ、CPU、メモリ、ネットワーク機器、サーバー、仮想マシン ホストのようなものでピーク時の操作を維持するには、継続的なメンテナンスが必要です。 多くの場合、資本予算には、これらの低レベルの資産の年 1 回または定期的な更新に関連する大規模な費用が含まれます。

 運用内の人的資源も、これらの資産に関連する問題の監視、修復、改善に大きな重点を置きます。 クラウド内では、これらの設備投資と運用アクティビティの多くがクラウド プロバイダーに移転されます。 これにより、IT 運用を改善し、大きな付加価値を提供する機会が提供されます。

## <a name="possible-sources-for-this-capability"></a>この機能の提供元

クラウド運用機能を提供するために必要なスキルは、以下によって提供される可能性があります。

- IT 運用
- IT 運用ベンダーへの外部委託
- クラウド サービス プロバイダー
- クラウドマネージド サービス プロバイダー
- アプリケーション固有の運用チーム
- ビジネス アプリケーションの運用チーム
- DevOps チーム

## <a name="key-responsibilities"></a>主な責務

クラウド運用機能を提供する人の職務は、合意された運用予算内で最大のワークロード パフォーマンスと最小限のビジネス中断を実現することです。

### <a name="strategic-tasks"></a>戦略的なタスク

- [ビジネス成果](../business-strategy/business-outcomes/index.md)、[財務モデル](../business-strategy/financial-models.md)、[クラウド導入の動機](../business-strategy/motivations-why-are-we-moving-to-the-cloud.md)、[ビジネス上のリスク](../governance/policy-compliance/risk-tolerance.md)、[デジタル資産の合理化](../digital-estate/overview.md)を確認する。
- ワークロードの重要度、中断やパフォーマンス低下の影響を判断する。
- ビジネスの承認済みコスト/パフォーマンス コミットメントを確立する。
- クラウド ワークロードを監視および操作する。

### <a name="technical-tasks"></a>技術的なタスク

- 資産とワークロードのインベントリを管理する。
- ワークロードをパフォーマンスの監視する。
- 運用のコンプライアンスを維持する。
- ワークロードおよび関連付けられている資産を保護する。
- パフォーマンスの低下やビジネス中断が発生した場合に資産を回復する。
- コア プラットフォームの機能を成熟させる。
- ワークロード パフォーマンスを継続的に改善する。
- ビジネスへのコミットメントに合わせてワークロードの予算および設計要件を改善する。

## <a name="meeting-cadence"></a>ミーティングの頻度

フィードバックを提供し、運用要件に備えるために、これらのクラウド運用機能の実行をリリース計画とクラウドのセンター オブ エクセレンス計画に含める必要があります。

## <a name="next-steps"></a>次の手順

導入と運用がスケーリングされるときに、既存の IT 要件を拡張するガバナンスのベスト プラクティスを定義および自動化することが重要です。 [クラウドのセンター オブ エクセレンス](./cloud-center-excellence.md)は、クラウド導入、クラウド運用、クラウド ガバナンスの取り組みをスケーリングするための重要なステップです。

> [!div class="nextstepaction"]
> [クラウドのセンター オブ エクセレンスの確立](./cloud-center-excellence.md)
