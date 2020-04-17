---
title: Azure イノベーション:デバイスを使用した対話
description: 接続された知覚機能を備えたエッジ デバイスを介して、イマーシブで効果的なビジネス ソリューションを構築するためのフレームワークを Azure でどのように提供しているかについて説明します。
author: umarmohamedusman
ms.author: umarm
ms.date: 10/10/2019
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: innovate
ms.custom: fasttrack-new, AQC
ms.localizationpriority: high
ms.openlocfilehash: b3bb752e823c9be61d61da8d2ea88a9cd5591709
ms.sourcegitcommit: 7d3fc1e407cd18c4fc7c4964a77885907a9b85c0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2020
ms.locfileid: "80427180"
---
<!-- cSpell:ignore umarmohamedusman umarm Moovit -->

::: zone target="docs"

# <a name="azure-innovation-guide-interact-through-devices"></a>Azure イノベーション ガイド:デバイス経由の操作

::: zone-end

::: zone target="chromeless"

# <a name="interact-through-devices"></a>デバイス経由の操作

::: zone-end

断続的に接続される、知覚機能を備えたエッジ デバイスを介してイノベーションを促進することができます。 数百万台のこのようなデバイスを調整し、無限のデータを取得して処理し、増え続けるマルチセンス、マルチデバイスのエクスペリエンスを活用します。 ネットワークのエッジにあるデバイス向けに、Azure では、イマーシブで効果的なビジネス ソリューションを構築するためのフレームワークを提供しています。 Azure を人工知能 (AI) テクノロジと組み合わせることで実現されるユビキタスなコンピューティングにより、思い描くことができるあらゆる種類のインテリジェントなアプリケーションやシステムを構築できます。

Azure のお客様は、ユーザー、データ、またはその両方に近いデータを収集して分析する、拡大し続ける一連の接続されたシステムやデバイスを採用しています。 ユーザーには、応答性に優れたコンテキスト対応のアプリによって、リアルタイムの分析情報とエクスペリエンスが提供されます。 ワークロードの一部をエッジに移動することで、これらのデバイスはクラウドにメッセージを送信する時間を短縮し、空間イベントにより迅速に反応できるようになります。

> [!div class="checklist"]
>
> - 産業用資産
> - HoloLens 2
> - Azure Sphere
> - Kinect DK
> - ドローン
> - Azure SQL Database Edge
> - IoT プラグ アンド プレイ

<!-- markdownlint-disable MD025 -->

## <a name="global-scale-iot-service"></a>[グローバルな規模の IoT サービス](#tab/IoTHub)

<!-- markdownlint-enable MD025 -->

数十億台規模の IoT デバイスとの双方向通信を実行するソリューションを設計します。 既定の device-to-cloud 利用統計情報を使用して、デバイスの状態を把握し、構成のみで他の Azure サービスへのメッセージ ルートを定義できます。 cloud-to-device メッセージを利用することにより、接続されたデバイスにコマンドや通知を信頼性の高い方法で送信できます。また、受信確認メッセージを使用してメッセージの配信を追跡できます。 また、断続的な接続に対応するため、必要に応じてデバイス メッセージを自動で再送信します。

次のような機能が用意されています。

- **セキュリティが強化された通信チャネル**で IoT デバイスとの間でデータを送受信する。
- **ビルトイン デバイス管理**とプロビジョニングで大規模な IoT デバイスに接続して管理する。
- **Event Grid との完全な統合**とサーバーレス コンピューティングによって、IoT アプリケーション開発を簡略化する。
- **Azure IoT Edge との互換性**を活かして、ハイブリッド IoT アプリケーションを構築する。

::: zone target="docs"

**[IoT Hub](https://docs.microsoft.com/azure/iot-hub) に移動する**

**[デバイス プロビジョニング サービス](https://docs.microsoft.com/azure/iot-dps)に移動する**

::: zone-end

::: zone target="chromeless"

### <a name="action"></a>アクション

IoT ハブを作成するには:

1. **IoT Hub** に移動します。
2. **[Create IoT hub]\(IoT ハブの作成\)** を選択します。

<!-- markdownlint-disable DOCSMD001 -->

::: form action="OpenBlade[#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Devices%2FIotHubs]" submitText="Go to IoT Hub" :::

<!-- markdownlint-enable DOCSMD001 -->

IoT Hub Device Provisioning Service は、ゼロタッチでの Just-In-Time プロビジョニングを可能にする、IoT Hub のヘルパー サービスです。

<!-- markdownlint-disable MD024 -->

### <a name="action"></a>アクション

IoT Hub Device Provisioning Service を作成するには:

1. **IoT Hub Device Provisioning Service** に移動します。
2. **[Create Device Provisioning Services]\(Device Provisioning Service の作成\)** を選択します。

<!-- markdownlint-disable DOCSMD001 -->

::: form action="OpenBlade[#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Devices%2FProvisioningServices]" submitText="Go to Device Provisioning Services" :::

<!-- markdownlint-enable DOCSMD001 -->

::: zone-end

<!-- markdownlint-disable MD025 -->

## <a name="azure-digital-twins"></a>[Azure Digital Twins](#tab/DigitalTwins)

現実世界とデジタル世界の垣根を越えてストリーミング データをリンクする、再利用可能でスケーラビリティが高く、空間認識性を備えたエクスペリエンスを構築します。 物理環境の包括的なモデルを使用して、顧客エンゲージメントを強化します。 人、場所、デバイスの間の関係と相互作用をモデル化する空間インテリジェンス グラフを生成します。 雑多なセンサーのデータではなく、物理空間のデータを照会できます。

**Azure Digital Twins のオブジェクト モデル:** スマート ビルディングの地域、会場、フロア、オフィス、ゾーン、会議室、フォーカス ルームやエネルギー グリッドのさまざまな発電所、変電所、エネルギー資源、顧客を表すオントロジーは、Digital Twins のオブジェクト モデルやオントロジーを使用してモデル化できます。

**空間インテリジェンス グラフ:** Digital Twins のオブジェクト モデルで定義されている空間、デバイス、人物の階層グラフで、継承、フィルター、走査、スケーラビリティ、拡張性がサポートされています。 お客様の空間グラフは、Azure でホストされている REST API のコレクションを使用して管理および操作できます。

::: zone target="docs"

**[Azure Digital Twins](https://docs.microsoft.com/azure/digital-twins/about-digital-twins) に移動する**

::: zone-end

::: zone target="chromeless"

### <a name="action"></a>アクション

Azure Digital Twins を作成するには:

1. 左側のウィンドウで、 **[リソースの作成]** を選びます。
2. **digital twins** を検索し、 **[Digital Twins]** を選択します。
3. **[作成]** を選択してデプロイ プロセスを開始します。
4. 既存の Digital Twins を確認するには、このボタンを選択します:

<!-- markdownlint-disable DOCSMD001 -->

::: form action="OpenBlade[#blade/HubsExtension/BrowseResource/resourceType/Microsoft.IoTSpaces%2FGraph]" submitText="Go to Digital Twins" :::

<!-- markdownlint-enable DOCSMD001 -->

::: zone-end

<!-- markdownlint-disable MD025 -->

## <a name="location-intelligence"></a>[位置情報インテリジェンス](#tab/AzureMaps)

Azure Maps サービスでは、近傍、トラフィック、ルート指定などの従来の位置情報機能に加え、**TomTom** や **Moovit** などの世界水準のモビリティ テクノロジ パートナーが提供するリアルタイムの位置情報インテリジェンスを使用してソリューションを作成できるようになりました。 地理空間サービスにより、高度な位置情報およびモビリティ機能がお客様のアプリケーションに容易に統合されます。

**データ サービス (プレビュー):** 空間演算や画像合成で使用する地理空間データをアップロードして格納することで、待ち時間を短縮し、生産性を向上させるほか、お客様のアプリケーション内で新しいシナリオを有効にすることもできます。

**空間演算:** ジオフェンシング、最接近点、大圏距離、バッファーなどの一般的な地理空間数学計算のライブラリを使用して、位置情報のインテリジェンスを強化します。

**位置情報:** IP アドレスから国を調べることができます。 ユーザーの位置情報に基づいてコンテンツとサービスをカスタマイズし、顧客の地理的分布に基づく分析情報を取得します。

::: zone target="docs"

**[Azure Maps](https://docs.microsoft.com/azure/azure-maps) に移動する**

::: zone-end

::: zone target="chromeless"

### <a name="action"></a>アクション

位置情報インテリジェンスを使用するには:

1. **Azure Maps アカウント**に移動します。
2. **[Create Azure Maps accounts]\(Azure Maps アカウントの作成\)** を選択します。

<!-- markdownlint-disable DOCSMD001 -->

::: form action="OpenBlade[#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Maps%2FAccounts]" submitText="Go to Azure Maps Account" :::

<!-- markdownlint-enable DOCSMD001 -->

::: zone-end

## <a name="spatial-experiences"></a>[空間エクスペリエンス](#tab/spatial)

Azure Spatial Anchors によって、開発者は、複合現実プラットフォームで作業を行って、空間の認識、正確な関心ポイントの指定、およびサポートされているデバイスからのこれらの関心ポイントの再呼び出しを実行できます。

**現実世界にコンテキストを追加する:** デジタル コンテンツを配置し、物理的な目的地に結び付けることで、ユーザーが必要な場所で必要なときにデータの理解を深めることができるようにします。

**デバイス間でホログラムを共有する:** 任意のデバイスでチームや顧客に 3D を提供することで、意思決定と成果の実現を加速します。 Spatial Anchors によって、同じ空間にいるユーザーはマルチユーザー対応複合現実アプリケーションに簡単にアクセスできます。

**魅力的なエクスペリエンス:** 相互の関係を作成することによって空間アンカーを接続し、ユーザーがタスクを完了するために対話する必要がある 2 つ以上の目的地が含まれるユーザー エクスペリエンスを提供します。 お使いのアプリでは、ユーザーが仮想アーティファクトを実際の環境に配置できるようになります。 労働環境では、ユーザーは、サポートされているデバイス カメラを機械に向けることで、コンテキスト情報を受け取ることができます。

Azure Spatial Anchors は、サポート対象のデバイス プラットフォーム用のマネージド サービスとクライアント SDK で構成されています。

::: zone target="docs"

**[Azure Spatial Anchors](https://azure.microsoft.com/services/spatial-anchors) に移動する**

::: zone-end

::: zone target="chromeless"

### <a name="action"></a>アクション

空間エクスペリエンスを使用するには:

1. **Spatial Anchors アカウント**に移動します。
2. **[Create spatial anchors accounts]\(Spatial Anchors アカウントの作成\)** を選択します。

<!-- markdownlint-disable DOCSMD001 -->

::: form action="OpenBlade[#blade/HubsExtension/BrowseResource/resourceType/Microsoft.MixedReality%2FSpatialAnchorsAccounts]" submitText="Go to Spatial Anchors Accounts" :::

<!-- markdownlint-enable DOCSMD001 -->

::: zone-end

## <a name="azure-remote-rendering"></a>[Azure Remote Rendering](#tab/RemoteRender)

クラウド内に高品質の対話型 3D コンテンツをレンダリングし、リアルタイムでデバイスにストリーム配信します。 レンダリング ワークロードは、メディアおよびエンターテイメント業界で特殊効果 (VFX) のためによく使用されています。 また、広告、小売、石油とガス、製造など、他にもレンダリングが使用されている業界は多数あります。

レンダリングのプロセスでは、コンピューティング処理の負荷が大きくなります。 生成するフレームや画像が多くになる場合があり、各画像のレンダリングには何時間もかかることがあります。 このため、レンダリングはバッチ処理に最適なワークロードであり、Azure および Azure Batch を使用すると多数のレンダーを並列実行できます。

::: zone target="docs"

**[Azure Remote Rendering](https://azure.microsoft.com/services/remote-rendering) に移動する**

**[Azure を使用したレンダリング](https://docs.microsoft.com/azure/batch/batch-rendering-service)に移動する**

::: zone-end

::: zone target="chromeless"

### <a name="action"></a>アクション

Azure Remote Rendering を使用するには:

1. **Batch アカウント**に移動します。
2. **[Create batch accounts]\(Batch アカウントの作成\)** を選択します。

<!-- markdownlint-disable DOCSMD001 -->

::: form action="OpenBlade[#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Batch%2FBatchAccounts]" submitText="Go to Azure Batch" :::

<!-- markdownlint-enable DOCSMD001 -->

::: zone-end
