---
title: Azure 管理ガイド:開始する前に
titleSuffix: Microsoft Cloud Adoption Framework for Azure
description: Azure での作業を管理する方法について段階的に学習します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 10/17/2019
ms.topic: article
ms.service: cloud-adoption-framework
ms.subservice: operate
ms.custom: fasttrack-edit, AQC
ms.localizationpriority: high
ms.openlocfilehash: c71dea28532c2bd90359e650a3462dddd059a2c5
ms.sourcegitcommit: 35c162d2d09ec1c4a57d3d57a5db1d56ee883806
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/17/2019
ms.locfileid: "72556498"
---
# <a name="before-you-start"></a>開始する前に

::: zone target="docs"

> [!NOTE]
> クラウド導入フレームワークでイノベーションを導くとき、このガイドが開始点となります。 これは Azure クイック スタート センターでも利用できます。 Azure クイック スタート センターへのリンクについては、この記事の後半にあるヒントを参照してください。

::: zone-end

Azure 管理ガイドは、Azure をご利用のお客様が管理のベースラインを作成し、Azure 全体でリソースの一貫性を確立するのを支援するように設計されています。 このガイドでは、Azure のあらゆる運用環境で、特に、機密データを扱う環境で必要な基本ツールを紹介します。 詳細、ベスト プラクティス、クラウド環境の準備に関連する考慮事項については、[クラウド導入フレームワークの準備セクション](../index.md)に関するページを参照してください。

## <a name="scope-of-this-guide"></a>このガイドの範囲

このガイドでは、管理ベースラインのツールを確立する方法について説明します。 ベースラインを拡張し、ベースラインを超えた回復性を構築する方法も説明します。

> [!div class="checklist"]
>
> - **インベントリと可視性:** 複数のクラウドをまたいで資産の目録を作成します。 各資産の実行状態が見えるようにします。
> - **運用のコンプライアンス:** 各状態が適切に構成され、条文に制御された環境で実行されるよう、コントロールとプロセスを確立します。
> - **保護と復旧:** 管理対象のすべての資産が保護され、ベースライン管理ツールで復旧できるようにします。
> - **拡張ベースライン オプション:** ビジネス ニーズを満たす可能性があるベースラインの一般的追加物を評価します。
> - **プラットフォームの運用:** 十分に定義されたサービス カタログと集中管理型のプラットフォームで管理ベースラインを拡張します。
> - **ワークロードの運用:** 管理ベースラインを拡張し、ミッション クリティカルなワークロードに集中的に取り組みます。

## <a name="management-baseline"></a>管理ベースライン

管理ベースラインは、環境内のあらゆる資産に適用するツールとプロセスの最小セットです。 いくつかの追加オプションを管理ベースラインに含めることができます。 後続の記事では数回にわたり、クラウド管理の機能についてさらに詳しく説明します。利用できるオプション全部ではなく、必要最小限のオプションを集中的に取り上げます。

::: zone target="docs"

> [!TIP]
> 対話形式で学習したい場合は、Azure portal でこのガイドを参照してください。 Azure portal で [Azure クイック スタート センター](https://portal.azure.com/?feature.quickstart=true#blade/Microsoft_Azure_Resources/QuickstartCenterBlade)にアクセスし、 **[Azure 管理ガイド]** を選択します。 ステップバイステップの手順に従います。

次のステップ:[インベントリと可視性](./inventory.md)

::: zone-end

::: zone target="chromeless"

このガイドの対話型の手順に従うと、紹介されている機能を実際に試してみることができます。 中断した場所に戻るには、ナビゲーション用の階層リンクを使用してください。

::: zone-end
