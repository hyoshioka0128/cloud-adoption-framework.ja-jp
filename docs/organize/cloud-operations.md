---
title: クラウド運用機能を理解する
description: クラウド運用機能の構成を理解し、チームを適切に配置します。
author: JanetCThomas
ms.author: janet
ms.service: cloud-adoption-framework
ms.subservice: organize
ms.topic: conceptual
ms.date: 05/15/2020
ms.openlocfilehash: a8ff734e8f29d3cfcc848fc99270aae16b73c8c5
ms.sourcegitcommit: 9a84c2dfa4c3859fd7d5b1e06bbb8549ff6967fa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/21/2020
ms.locfileid: "83755572"
---
# <a name="cloud-operations-functions"></a>クラウド運用機能

運用チームは、従来の IT 運用および資産に関連する問題の監視と修復に注力します。 クラウドでは、資本コストと運用アクティビティの多くがクラウド プロバイダーに移行され、IT 運用を改善して重要な付加価値を提供する機会が得られます。

クラウド運用機能を実現するために必要なスキルは、以下によって提供される可能性があります。

- IT 運用
- IT 運用ベンダーへの外部委託
- クラウド サービス プロバイダー
- クラウドマネージド サービス プロバイダー
- アプリケーション固有の運用チーム
- ビジネス アプリケーションの運用チーム
- DevOps チーム

## <a name="preparation"></a>準備

- [Azure でのリソースの管理](https://docs.microsoft.com/learn/paths/manage-resources-in-azure):Azure CLI と Web ポータルを介してクラウドベースのリソースを作成、管理、制御する方法について学習します。
- [Azure のネットワーク サービス](https://docs.microsoft.com/learn/modules/intro-to-azure-networking): Azure のネットワークの基本と、回復性を向上させて待ち時間を短縮する方法を学習します。

次の項目を確認してください。

- [ビジネス成果](../strategy/business-outcomes/index.md)
- [財務モデル](../strategy/financial-models.md)
- [クラウド導入の動機](../strategy/motivations.md)
- [ビジネス リスク](../govern/policy-compliance/risk-tolerance.md)
- [デジタル資産の合理化](../digital-estate/index.md)

## <a name="minimum-scope"></a>最小スコープ

クラウド運用チームのメンバーの職務には、合意した運用予算内で最大のワークロード パフォーマンスと最小限のビジネス中断を実現することが含まれています。

- ワークロードの重要度、中断やパフォーマンス低下の影響を判断します。
- ビジネスの承認済みのコストとパフォーマンスのコミットメントを確立します。
- クラウド ワークロードを監視および操作する。

## <a name="deliverables"></a>成果物

- 資産とワークロードのインベントリの管理
- ワークロードのパフォーマンスの監視
- 運用のコンプライアンスの維持
- ワークロードおよび関連付けられている資産の保護
- パフォーマンスの低下やビジネス中断が発生した場合に資産を回復する
- コア プラットフォームの機能を成熟させる
- ワークロード パフォーマンスの継続的な改善
- ビジネスへのコミットメントに合わせてワークロードの予算および設計要件を改善する

### <a name="meeting-cadence"></a>ミーティングの頻度

フィードバックの提供と運用要件の準備を行うために、リリース計画とクラウド センター オブ エクセレンス計画の立案にクラウド運用チームを関与させる必要があります。

## <a name="out-of-scope"></a>対象範囲外

低レベルの技術資産に対する現状の運用を維持することに注力する従来の IT 運用は、クラウド運用チームのスコープではありません。 ストレージ、CPU、メモリ、ネットワーク機器、サーバー、仮想マシン ホストなどでは、ピーク時の運用を維持するために、メンテナンス、監視、修復、および問題の修正を続行する必要があります。 クラウド内では、これらの設備投資と運用アクティビティの多くがクラウド プロバイダーに移転されます。

## <a name="next-steps"></a>次のステップ

導入と運用がスケーリングされるときに、既存の IT 要件を拡張するガバナンスのベスト プラクティスを定義および自動化することが重要です。 クラウド センター オブ エクセレンスの結成は、クラウド導入、クラウド運用、クラウド ガバナンスの取り組みをスケーリングするための重要なステップです。

各項目の詳細情報

- [クラウドのセンター オブ エクセレンス](../organize/cloud-center-of-excellence.md)の機能。
- [組織のアンチパターン: サイロと封土](../organize/fiefdoms-silos.md)

"実行責任、説明責任、助言、通知" (RACI) の各パーティを明確にするクロスチーム マトリックスを作成することによりチーム間での責任を調整する方法について説明します。 [RACI スプレッドシート テンプレート](https://archcenter.blob.core.windows.net/cdn/fusion/management/raci-template.xlsx)をダウンロードして編集してください。
