---
title: 予測して影響を与えるためのイノベーション ツール
description: Azure 向けクラウド導入フレームワークを使用して、パターンを検出し、予測を統合し、顧客の行動に影響を与えるのに役立つ分析ツールを見つけます。
author: BrianBlanchard
ms.author: brblanch
ms.date: 10/17/2019
ms.topic: guide
ms.service: cloud-adoption-framework
ms.subservice: innovate
ms.openlocfilehash: a1201c3e1bf05f7fc704b1a12744d8f031607169
ms.sourcegitcommit: ea63be7fa94a75335223bd84d065ad3ea1d54fdb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "80356821"
---
# <a name="tools-to-predict-and-influence-data-in-azure"></a>Azure のデータを予測して影響を与えるためのツール

[予測と影響](../considerations/predict.md)に関する概念の記事で説明したように、コンピューターと AI は、パターンの観察において人間よりもはるかに優れています。 クラウドベースの分析ツールを使用すると、パターンを簡単に検出して顧客のニーズに適用することができます。 これらのツールを使用すると、最善の成果を予測できます。 これらの予測を顧客のエクスペリエンスに統合することで、対話を通して顧客の行動パターンに影響を与えることができます。

![予測して影響を与えるためのクラウド導入フレームワーク アプローチ](../../_images/innovate/predict-and-influence.png)

## <a name="alignment-to-the-methodology"></a>方法論に足並みを揃える

この種類のデジタル発明は、以下のプロセスの各フェーズで加速できます。 フェーズは、前の図に示されている方法論に沿っています。 デジタル発明を加速するための技術的なガイダンスは、このページの左側の目次に一覧表示されています。 これらの記事は、方法論に合わせて、同じフェーズにグループ化されています。

前の図では、データと分析は、[データの民主化](./data.md)に関する記事に記載されているベスト プラクティスに沿っています。 主題の専門家は、再現できる可能性がある分析情報を発見したら、次の 3 つの手順を使用して、これらの分析情報を成熟させることができます。

- **パターン:** 予測モデルを作成するためのパターンを検出して定義します。
- **予測:** 顧客のデータにパターンを適用して、モデルと内在するパターンに基づいて成果を予測します。
- **対話:** アプリケーションまたはデータ ソース内で予測を使用して、顧客との対話を推進します。

## <a name="toolchain"></a>ツールチェーン

Azure では、上記の各フェーズでデジタル発明を加速するために、一般的に次のツールが使用されます。

- [Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning)
- [Azure HDInsight](https://docs.microsoft.com/azure/hdinsight)
- [Hadoop R ScaleR](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-r-scaler-sparkr)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)

予測の影響の各フェーズで各ツールがどのように役立つかについては、このページの左側の目次のガイダンスに反映されています。

## <a name="get-started"></a>はじめに

このページの左側の目次に、さまざまな記事がまとめられています。 これらの記事は、このツールチェーン内の各ツールの使用を開始するのに役立ちます。

> [!NOTE]
> 一部のリンクは、このフレームワークの範囲を超えるために、クラウド導入フレームワークの範囲外になるものもあります。
