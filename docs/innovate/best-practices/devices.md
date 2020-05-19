---
title: デバイスとの対話のためのイノベーション ツール
description: デバイス経由で操作するための Azure ツールと、顧客の自然な環境と動作を強化するアンビエント エクスペリエンスについて説明します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 10/17/2019
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: innovate
ms.openlocfilehash: 401acfb2a0689fe54d43c29a8ab32a8d6e218670
ms.sourcegitcommit: 60d8b863d431b5d7c005f2f14488620b6c4c49be
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2020
ms.locfileid: "83219769"
---
# <a name="tools-to-interact-with-devices-in-azure"></a>Azure 上のデバイスと対話するためのツール

[デバイスとの対話](../considerations/devices.md)に関する概念の記事で説明したように、顧客との対話に使用されるデバイスは、顧客のニーズを満たして導入を促進するために必要なアンビエント エクスペリエンスの量によって異なります。 顧客のニーズを促進するトリガーからそのニーズを満たすソリューションの機能に至るまでの速度は、反復使用の決定要因です。 アンビエント エクスペリエンスによってソリューションをお客さまの身の回りの環境に埋め込むことで、応答時間を短縮し、顧客にとってより良いエクスペリエンスを創り出すことができます。

![デバイスと対話するためのクラウド導入フレームワーク アプローチ](../../_images/innovate/ambient-experiences.png)

## <a name="alignment-to-the-methodology"></a>方法論に足並みを揃える

この種類のデジタル発明は、次のレベルのアンビエント エクスペリエンスを通して提供できます。 これらのレベルは、前の図に示されている方法論に沿っています。 このページの左側の目次に、デジタル発明を加速するための技術的なガイダンスが一覧表示されています。 これらの記事は、方法論に合わせて、アンビエント エクスペリエンスのレベルごとにグループ化されています。

- **モバイル エクスペリエンス**モバイル アプリは、通常、顧客を取り巻く環境の一部です。 一部のシナリオでは、モバイル デバイスによって、ソリューションをアンビエントにするための十分な対話性が提供される可能性があります。
- **複合現実:** 顧客の自然な環境を、複合現実を通して変えなければならないことがあります。 このような複合現実の中で顧客とかかわることで、アンビエント エクスペリエンスという形態を提供できます。
- **統合現実:** 現実の周辺環境にさらに近づける統合現実ソリューションでは、顧客の物理的なデバイスを使用することに注力して、ソリューションを自然な行動に統合します。
- **Adjusted Reality (調整された現実):** 上記のソリューションで予測分析を利用して、顧客の自然な環境の中で顧客と対話する場合、そのソリューションでは、最高の形態のアンビエント エクスペリエンスが創出されます。

## <a name="toolchain"></a>ツールチェーン

Azure では、上記のアンビエント ソリューションの各レベル間でのデジタル発明を加速するために、一般に次のツールを使用します。 これらのツールは、これらのエクスペリエンスにツールを合わせる際の複雑さを軽減するために必要なエクスペリエンスの量に基づいてグループ化されています。

<!-- markdownlint-disable MD033 -->

| カテゴリ | ツール |
|---|---|
| モバイル エクスペリエンス | <li> Azure App Service <li> PowerApps <li> Microsoft Flow <li> Intune |
| 複合現実 | <li> Unity <li> Azure Spatial Anchors <li> HoloLens |
| Integrated Reality | <li> Azure IoT Hub <li> Azure Sphere <li> Azure Kinect DK |
| 調整された現実 | <li> IoT クラウドからデバイスへ <li> Azure Digital Twins と HoloLens |

<!--markdownlint-enable MD033 -->

## <a name="get-started"></a>はじめに

このページの左側の目次に、さまざまな記事がまとめられています。 これらの記事は、このツールチェーン内の各ツールの使用を開始するのに役立ちます。

> [!NOTE]
> 一部のリンクは、このフレームワークの範囲を超えるために、クラウド導入フレームワークの範囲外になるものもあります。
