---
title: ハイブリッドおよびマルチクラウド シナリオの概要
description: ハイブリッドおよびマルチクラウド シナリオについて説明します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 02/01/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: overview
ms.custom: e2e-hybrid
ms.openlocfilehash: 7a14b0e3384667d9ea3e2545b223be0df6f229bc
ms.sourcegitcommit: b8f8b7631aabaab28e9705934bf67dad15e3a179
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101801201"
---
# <a name="introduction-to-the-hybrid-and-multicloud-scenario"></a>ハイブリッドおよびマルチクラウド シナリオの概要

お客様が採用するクラウド導入形態の規模と洗練度が増すごとに、クラウド導入への道は複雑になって行きます。 この記事シリーズは、ハイブリッドおよびマルチクラウドの導入シナリオに向けた準備を行う際に重要になる、さまざまな技術的および非技術的な考慮事項をまとめたものです。

このシナリオでは、次の 2 つの目的を達成することに焦点を当てます。

- [ハイブリッドおよびマルチクラウドの導入](./index.md): ベンダー ロックインを最小限に抑えたクラウド プラットフォーム間の移動を容易にする、移植性のあるソリューションの構築、デプロイ、移行を行います。
- [統合運用](./unified-operations.md): 一般的な運用プロセスを共有するガバナンスと管理プロセスを用いて集中管理することにより、ソリューションがデプロイされている場所にかかわらず、各クラウドのソリューションをサポートする運用を合理化します。

## <a name="components-of-the-scenario"></a>シナリオの構成要素

このシナリオは、クラウド導入ライフサイクル全体を通して、エンドツーエンドの顧客体験をガイドするように設計されています。

![ハイブリッド マルチクラウド アプローチを示すグラフィック](../../_images/hybrid/hybrid-multicloud-approach.png)

この完全な取り組みを提供するには、いくつかの重要な要素 (ガイダンス セット) が必要になります。

| <span title="アイコン">&nbsp;</span> | <span title="説明">&nbsp;</span> |
|--|--|
| <br> :::image type="icon" source="../../_images/hybrid/cloud-journey.png"::: | <br> [Azure 向けの Microsoft Cloud 導入フレームワーク:](../../get-started/index.md) これらの記事では、各 CAF 手法の最小限の考慮事項と実装について説明します。 これらの記事を使用して、ポートフォリオ全体へのハイブリッドおよびマルチクラウド ワークロードの導入に向けて、意思決定者、中央 IT、およびクラウドのセンター オブ エクセレンスの準備を整えましょう。 |
| <br> :::image type="icon" source="../../_images/hybrid/hybrid-well-architected.png"::: | <br> [Microsoft Azure Well-Architected フレームワーク](/azure/architecture/framework/): これらの記事では、ハイブリッドおよびマルチクラウド環境にワークロードをデプロイする必要が出たときに、各ワークロード所有者が留意すべき考慮事項について説明します。 |
| <br> :::image type="icon" source="../../_images/hybrid/hybrid-architectures.png"::: | <br> [参照用アーキテクチャ](/azure/architecture/browse/): これらのリファレンス ソリューションは、多くの一般的なハイブリッドおよびマルチクラウドのシナリオが発生した際に、デプロイを促進するのに役立ちます。 |
| <br> :::image type="icon" source="../../_images/hybrid/hybrid-best-practices.png"::: | <br> ベスト プラクティス: 300 以上のレベルがあるこれらの記事は、中央 IT チームが Azure Arc、ARM テンプレート、およびその他の関連する Azure 製品を使用して、統合された運用ソリューションに資産をオンボードするのに役立ちます。 |
| <br> :::image type="icon" source="../../_images/hybrid/hybrid-product-docs.png"::: | <br> おすすめの Azure 製品: Azure を用いたハイブリッドおよびマルチクラウド戦略をサポートする製品について学習します。 |
| <br> :::image type="icon" source="../../_images/hybrid/hybrid-skills.png"::: | <br> [Microsoft Learn モジュール:](/learn/azure/) ハイブリッドおよびマルチクラウド ソリューションを実装、管理、およびサポートするために必要なハンズオン スキルを習得します。 |

## <a name="common-customer-challenges-and-supporting-guidance"></a>お客様が直面する一般的な課題とそのサポートに関するガイダンス

**ハイブリッドおよびマルチクラウドの集中運用に備える:** 右側に記載されている目次の[クラウド導入フレームワーク](../../get-started/index.md)に関する 7 本の記事を参照します。 ハイブリッドおよびマルチクラウド環境におけるワークロードのポートフォリオ全体をサポートするために必要なプロセスとアプローチを確立します。

**既存のハイブリッドおよびマルチクラウド ポートフォリオ全体の資産を監視する:** 統合運用、ガバナンス、管理に関する記事に焦点を当て、既存の運用プロセスに統合運用を組み込みます。 準備に関する記事を参考に、使用しているすべてのクラウド環境にそれらの改善を展開します。

**個々のワークロードに変更を波及させる (中央 IT):** ハイブリッドおよびマルチクラウドの制御が改善するにつれて、中央 IT チームは、個々のワークロードの背後にあるアーキテクチャに関する知識が求められる要件に対応せざるを得なくなります。 その場合は、ワークロードの所有者がハイブリッドおよびマルチクラウドの運用を改善するワークロードの潜在的な改善点について理解する上で役立つ [Azure Well-Architected フレームワーク](/azure/architecture/framework/)のガイダンスを参照しましょう。

**個々のワークロードを最適化する (ワークロード チーム):** ワークロードの所有者は、ハイブリッドおよびマルチクラウド戦略をワークロードに統合する最適な方法を理解するため、まずは [Microsoft Azure Well-Architected レビュー](/assessments/?id=azure-architecture-review&mode=pre-assessment)に関するガイダンスを参照することをお勧めします。 チームが集中 IT または CCoE チームからの支援も受けている場合、このガイダンスは、ワークロード開発を加速させる、(一般的に中央 IT チームが担当する) ベスト プラクティスとアーキテクチャについての分析情報を提供します。

**個々の資産をオンボードするプロセス:** ベスト プラクティスのセクションを使用して、すべてのハイブリッド資産をオンボードするための一連のプロセスを実行します。

**具体的な Azure 製品を実装する:** おすすめの製品セクションに記載されているさまざまな Azure 製品を使用して、ハイブリッドおよびマルチクラウドの機能を高速化し、改善します。
