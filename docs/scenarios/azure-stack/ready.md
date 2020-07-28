---
title: Azure Stack Hub 移行のためのクラウド環境を準備する
description: Azure Stack Hub 移行のためのクラウド環境を準備する
author: BrianBlanchard
ms.author: brblanch
ms.date: 05/19/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.openlocfilehash: a83c14475cc4d0c46e6c69e4061ea115e5b9b62b
ms.sourcegitcommit: 9163a60a28ffce78ceb5dc8dc4fa1b83d7f56e6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2020
ms.locfileid: "86452746"
---
# <a name="ready-your-cloud-environment-for-azure-stack-hub-migration"></a>Azure Stack Hub 移行のためのクラウド環境を準備する

この記事では、[クラウド戦略への Azure Stack の組み入れ](./index.md)を決定し、[Azure Stack Hub 移行用の計画](./plan.md)を作成していることを前提としています。

最初に、対応する必要があるインフラストラクチャの依存関係を評価します。

- ID
- 接続
- セキュリティ
- 暗号化

## <a name="hybrid-environment-configuration"></a>ハイブリッド環境の構成

Azure のパブリック クラウド内の IT ポートフォリオの一部と、Azure Stack Hub プライベート クラウド内の他のポートフォリオの一部を利用して、ハイブリッド環境を開発している場合は、まず、パブリック クラウド内のいくつかの基本的な項目を構成することを考えます。 [準備の方法](../../ready/index.md)に関するガイダンスを確認して、パブリック クラウド内にランディング ゾーンを確立できます。

**ランディング ゾーンとクラウド プラットフォーム接続:** そのプロセスの中で、現在のデータセンターと Azure 間に安定したネットワーク接続があることを確認します。 ネットワーク接続が確立されたら、Azure への接続の待機時間、帯域幅、および信頼性をテストします。

**ガバナンスと操作:** 両方のクラウドに移行する場合には、環境に影響を与えるいくつかの初期の意思決定を行う必要があります。 ベスト プラクティスは、パブリック クラウド内で実行されるクラウド ネイティブの操作とガバナンス ツール上に構築されます。 このアプローチによって、データセンター内で高価なシステムを実行するコストや、Azure Stack Hub 上で容量を消費する負荷が軽減されます。 いずれかの形式の Azure クラウドに移行する場合は、運用、ガバナンス、および変更管理のために、ベスト プラクティスに従うか既存のシステムの使用を継続するかを決断する必要があります。

## <a name="private-cloud-environment"></a>プライベート クラウド環境

Azure のプライベート クラウド バージョン Azure Stack Hub のみを使用することを選んだ場合も、同じ意思決定のポイントを考慮する必要があります。

**オンプレミスのガバナンスと操作:** ベスト プラクティスでは、Azure のパブリック クラウド バージョン内にあるクラウド ネイティブの操作とガバナンス ツールを使用することが引き続き提示されます。 それらのベスト プラクティスを早期に評価し、ベスト プラクティスがご自身のシナリオに当てはまるかどうかを判断することが重要です。

**ランディング ゾーンとクラウド プラットフォーム接続:** ワークロード移行が Azure Stack Hub にデプロイされる場合、エンド ユーザーと Azure Stack アプライアンス間のネットワーク ルートの待機時間、帯域幅、および信頼性を文書化してテストすることが重要になります。

## <a name="next-step-assess-workloads-before-migration"></a>次のステップ: 移行前にワークロードを評価する

次の記事では、クラウド導入過程の特定の時点におけるガイダンスを提供しています。 ワークロードの評価に関する最初の記事では、各ワークロードを移行する準備が確実にできるように、計画プロセス中の評価を超えて詳しく説明しています。

- [Azure Stack Hub のワークロードを評価する](./migrate-assess.md)
- [Azure Stack Hub にワークロードをデプロイする](./migrate-deploy.md)
- [Azure Stack Hub のガバナンス](./govern.md)
- [Azure Stack Hub の管理](./manage.md)
