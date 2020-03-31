---
title: Azure 管理の概要
description: Azure の運用環境を管理するために必要な基本的なツールに関する情報を使用して、Azure のクラウド導入フレームワークについて説明します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 10/17/2019
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: operate
ms.custom: fasttrack-edit, AQC
ms.localizationpriority: high
ms.openlocfilehash: 30ce3f5d074e7b446da6c11efe5aa81e1c25724f
ms.sourcegitcommit: afe10f97fc0e0402a881fdfa55dadebd3aca75ab
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2020
ms.locfileid: "80426834"
---
::: zone target="docs"

# <a name="azure-management-guide-before-you-start"></a>Azure 管理ガイド:開始する前に

::: zone-end

::: zone target="chromeless"

# <a name="before-you-start"></a>開始する前に

::: zone-end

Azure 管理ガイドは、Azure をご利用のお客様が管理ベースラインを作成し、Azure 全体でリソースの一貫性を確立する際に役立ちます。 このガイドでは、Azure のあらゆる運用環境、特に、機密データをホストする環境に必要となる基本的なツールを紹介します。 詳細、ベスト プラクティス、クラウド環境の準備に関連する考慮事項については、クラウド導入フレームワークの[準備セクション](../index.md)に関するページを参照してください。

## <a name="scope-of-this-guide"></a>このガイドの範囲

このガイドでは、管理ベースライン用のツールを確立する方法について説明します。 ベースラインを拡張し、ベースラインを超えた回復性を構築する方法も説明します。

> [!div class="checklist"]
>
> - **インベントリと可視性:** 複数のクラウドをまたいで資産の目録を作成します。 各資産の実行状態が見えるようにします。
> - **運用のコンプライアンス:** 各状態が適切に構成され、条文に制御された環境で実行されるよう、コントロールとプロセスを確立します。
> - **保護と復旧:** 管理対象のすべての資産が保護され、ベースライン管理ツールで復旧できるようにします。
> - **拡張ベースライン オプション:** ビジネス ニーズを満たす可能性があるベースラインへの一般的な追加オプションを評価します。
> - **プラットフォームの運用:** 十分に定義されたサービス カタログと集中管理型のプラットフォームを使用して管理ベースラインを拡張します。
> - **ワークロードの運用:** 管理ベースラインを拡張し、ミッション クリティカルなワークロードに集中的に取り組みます。

## <a name="management-baseline"></a>管理ベースライン

管理ベースラインは、環境内のすべての資産に適用する必要のある最小限のツールとプロセスのセットです。 いくつかの追加オプションを管理ベースラインに含めることができます。 後続の記事では数回にわたり、利用可能なすべてのオプションではなく必要最小限のオプションに重点を置くことで、クラウド管理の機能を促進します。

::: zone target="docs"

次の手順は、[インベントリと可視性](./inventory.md)です。

::: zone-end

::: zone target="chromeless"

このガイドの対話型の手順に従うと、紹介されている機能を実際に試してみることができます。 中断した場所に戻るには、ナビゲーション用の階層リンクを使用してください。

::: zone-end
