---
title: デバイスとの対話のためのアンビエント エクスペリエンス
description: デバイスとの対話のための Azure ツールと、顧客の自然な環境と動作を強化するアンビエント エクスペリエンスについて説明します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 10/17/2019
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: innovate
ms.custom: internal, seo-caf-innovate
keywords: アンビエント ユーザー エクスペリエンス, アンビエント エクスペリエンス, デバイスとの対話
ms.openlocfilehash: d6e91786fcc7be69707965a4b967c5b118cc6078
ms.sourcegitcommit: 51565dc4d3a1858bd62f708f2e4c082fbd4c6fe4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/20/2021
ms.locfileid: "107747944"
---
# <a name="ambient-experience-tools-to-interact-with-devices-in-azure"></a>Azure でデバイスと対話するためのアンビエント エクスペリエンス ツール

[デバイスとの対話](../considerations/devices.md)に関する記事で説明したように、顧客との対話に使用されるデバイスは、顧客のニーズを満たして導入を促進するために必要なアンビエント ユーザー エクスペリエンスの量によって異なります。 顧客のニーズを促進するトリガーからそのニーズを満たすソリューションの機能に至るまでの速度は、反復使用の決定要因です。 アンビエント エクスペリエンスによってソリューションをお客さまの身の回りの環境に埋め込むことで、応答時間を短縮し、顧客にとってより良いエクスペリエンスを創り出すことができます。

![デバイスと対話するためのクラウド導入フレームワーク アプローチを示す図。](../../_images/innovate/ambient-experiences.png)

## <a name="alignment-to-the-methodology"></a>方法論に足並みを揃える

この種類のデジタル発明は、次のレベルのアンビエント エクスペリエンスを通して提供できます。 このページの左側の目次に、デジタル発明を加速するための技術的なガイダンスが一覧表示されています。 デバイスとの対話に関するこれらの記事は、方法論に合わせて、アンビエント ユーザー エクスペリエンスのレベルごとにグループ化されています。

- **モバイル エクスペリエンス** モバイル アプリは、通常、顧客を取り巻く環境の一部です。 一部のシナリオでは、モバイル デバイスによって、ソリューションをアンビエントにするための十分な対話性が提供される可能性があります。
- **複合現実:** 顧客の自然な環境を、複合現実を通して変えなければならないことがあります。 このような複合現実の中で顧客とかかわることで、アンビエント ユーザー エクスペリエンスという形態を提供できます。
- **統合現実:** 現実の周辺環境にさらに近づける統合現実ソリューションでは、顧客の物理的なデバイスを使用することに注力して、ソリューションを自然な行動に統合します。
- **Adjusted Reality (調整された現実):** 上記のソリューションで予測分析を利用して、顧客の自然な環境の中で顧客と対話する場合、そのソリューションでは、最高の形態のアンビエント エクスペリエンスが創出されます。

## <a name="toolchain"></a>ツールチェーン

Azure では、アンビエント ユーザー エクスペリエンス ソリューションの各レベル間でのデジタル発明を加速するために、一般に次のツールを使用します。 これらのツールは、これらのエクスペリエンスにツールを合わせる際の複雑さを軽減するために必要なエクスペリエンスの量に基づいてグループ化されています。

| カテゴリ | ツール |
|---|---|
| モバイル エクスペリエンス | <li> Azure App Service <li> Power Apps <li> Power Automate <li> Intune |
| 複合現実 | <li> Unity <li> Azure Spatial Anchors <li> HoloLens |
| Integrated Reality | <li> Azure IoT Hub <li> Azure Sphere <li> Azure Kinect DK |
| 調整された現実 | <li> IoT クラウドからデバイスへ <li> Azure Digital Twins と HoloLens |

## <a name="get-started"></a>はじめに

このページの左側の目次に、さまざまな記事がまとめられています。 これらの記事は、このツールチェーン内の各ツールの使用を開始するのに役立ちます。

> [!NOTE]
> 一部のリンクは、このフレームワークの範囲を超えるために、クラウド導入フレームワークの範囲外になるものもあります。
