---
title: クラウド イノベーション:Azure 上のデバイスと対話するためのツール
titleSuffix: Microsoft Cloud Adoption Framework for Azure
description: Azure 上のデバイスと対話するためのツール
author: BrianBlanchard
ms.author: brblanch
ms.date: 10/17/2019
ms.topic: guide
ms.service: cloud-adoption-framework
ms.subservice: innovate
ms.openlocfilehash: a8dcdf770e31a1b0fb380ac0fe85bb2fe9c8ed0b
ms.sourcegitcommit: f3371811a36e12533ecbc3aa936e2a68e0cee25f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/21/2019
ms.locfileid: "72683391"
---
# <a name="tools-to-interact-with-devices-in-azure"></a>Azure 上のデバイスと対話するためのツール

[デバイスとの対話](../considerations/devices.md)に関する理論の記事で説明したように、顧客との対話に使用されるデバイスは、顧客のニーズを満たして導入を促進するために必要なアンビエント エクスペリエンスのレベルによって異なります。 ニーズを促進するトリガーから顧客のニーズを満たすソリューションの機能に至るまでの速度は、反復使用の決定要因です。 アンビエント エクスペリエンスによってソリューションをお客さまの身の回りの環境に埋め込むことで、応答時間を短縮し、顧客にとってより良いエクスペリエンスを創り出すことができます。

![デバイスと対話するためのクラウド導入フレームワーク アプローチ](../../_images/innovate/ambient-experiences.png)

## <a name="alignment-to-the-methodology"></a>方法論に足並みを揃える

この種類のデジタル発明は、上記の図に示されている方法論に沿って、次のレベルのアンビエント エクスペリエンスを通して提供できます。 デジタル発明を加速するための技術的なガイダンスは、左側の目次に一覧表示されています。 これらの記事は、方法論に合わせて、同じレベルのアンビエント エクスペリエンスにグループ化されています。

- **モバイル エクスペリエンス**モバイル アプリは、通常、顧客を取り巻く環境の一部です。 一部のシナリオでは、モバイル デバイスによって、ソリューションをアンビエントにするための十分なレベルの対話性が提供される可能性があります。
- **複合現実:** 顧客の自然な環境を、複合現実を通して変えなければならないことがあります。 このような複合現実の中で顧客とかかわることで、アンビエント エクスペリエンスという形態を提供できます。
- **統合現実:** 現実の周辺環境にさらに近づける統合現実ソリューションでは、顧客の物理的な現実の中に存在するデバイスを使用することに注力して、ソリューションを自然な行動に統合します。
- **調整現実:** 上記のアンビエント ソリューションで予測分析を利用して、自然な環境の中で顧客と対話する場合、そのソリューションでは、最高の形態のアンビエント エクスペリエンスが創出されます。

## <a name="toolchain"></a>ツールチェーン

Azure では、上記のアンビエント ソリューションの各レベル間でのデジタル発明を加速するために、一般的に次のツールが活用されます。 これらのツールは、エクスペリエンスで必要なツールを揃える際の複雑さを軽減するために、エクスペリエンスのレベルに基づいてグループ化されています。

- モバイル エクスペリエンス:App Service、PowerApps、Microsoft Flow、Intune
- 複合現実:Unity、Azure Spatial Anchors、HoloLens
- 統合現実:Azure IoT Hub、Azure Sphere、Kinect DK
- 調整現実:IoT cloud-to-device、Digital Twins + HoloLens

## <a name="get-started"></a>作業開始

左側の目次に、このツールチェーンの各ツールの使用を開始するためのさまざまな記事がまとめられています。

> [!NOTE]
> 一部のリンクは、クラウド導入フレームワークの範囲を越えた情報を提供するために、このフレームワークから離れる場合があります。
