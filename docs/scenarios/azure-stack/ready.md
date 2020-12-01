---
title: Azure Stack Hub 移行のためのクラウド環境を準備する
description: Azure Stack Hub 移行のためのクラウド環境を準備します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 05/19/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.openlocfilehash: 6799879a957ed75c8c5557aa0787ee6f3fcb4472
ms.sourcegitcommit: 57b757759b676a22f13311640b8856557df36581
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/20/2020
ms.locfileid: "94994549"
---
# <a name="ready-your-cloud-environment-for-azure-stack-hub-migration"></a>Azure Stack Hub 移行のためのクラウド環境を準備する

この記事では、[クラウド戦略への Azure Stack の組み入れ](./index.md)を決定し、[Azure Stack Hub 移行用の計画](./plan.md)を作成していることを前提としています。

最初に、対応する必要があるインフラストラクチャの依存関係を評価します。

- ID
- 接続
- セキュリティ
- 暗号化

## <a name="hybrid-environment-configuration"></a>ハイブリッド環境の構成

あるハイブリッド環境で、IT ポートフォリオの一部を Azure パブリック クラウドに置き、その他を Azure Stack Hub プライベート クラウドに置きます。 このような環境を開発するには、まず、パブリック クラウドで基本的な要素をいくつか構成する必要があります。 パブリック クラウドでランディング ゾーンの確立を開始するには、[クラウド導入に合わせて環境を準備する](../../ready/index.md)方法に関するページを参照してください。

**ランディング ゾーンとクラウド プラットフォーム接続**:そのプロセスの中で、現在のデータセンターと Azure 間に安定したネットワーク接続を確保します。 ネットワーク接続が確立されたら、Azure への接続の待機時間、帯域幅、信頼性をテストします。

**ガバナンスと操作**:両方のクラウドに移行する場合には、環境に影響を与えるいくつかの初期の意思決定を行う必要があります。 ベスト プラクティスを適用することで、パブリック クラウド内で実行されるクラウド ネイティブの操作とガバナンス ツールの上に構築します。 このアプローチにより、データセンター内での高価なシステムの実行や Azure Stack Hub デプロイ上の容量の消費で生じるコストが削減されます。 いずれかの形式のクラウドに移行するとき、運用、ガバナンス、変更管理のために、ベスト プラクティスに従うか、既存のシステムの使用を継続する必要があります。

## <a name="private-cloud-environment"></a>プライベート クラウド環境

Azure のプライベート クラウド バージョンである Azure Stack Hub デプロイのみを使用する場合、同じ意思決定ポイントを考慮する必要があります。

**オンプレミスのガバナンスと操作**:ベスト プラクティスでは、Azure のパブリック クラウド バージョン内にあるクラウド ネイティブの操作とガバナンス ツールを使用することが引き続き提示されます。 このベスト プラクティスを早期に評価し、それが自分のシナリオに当てはまるかどうかを判断することが重要です。

**ランディング ゾーンとクラウド プラットフォーム接続**:ワークロード移行が Azure Stack Hub デプロイに存在する場合、エンド ユーザーと Azure Stack アプライアンス間のネットワーク ルートの待機時間、帯域幅、信頼性を文書化してテストすることが重要になります。

## <a name="next-steps"></a>次のステップ

クラウド導入の取り組みの特定の要素に関するガイダンスについては、以下を参照してください。

- [Azure Stack Hub のワークロードを評価する](./migrate-assess.md)
- [Azure Stack Hub にワークロードをデプロイする](./migrate-deploy.md)
- [Azure Stack Hub のガバナンス](./govern.md)
- [Azure Stack Hub の管理](./manage.md)
