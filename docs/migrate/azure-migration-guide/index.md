---
title: Azure 移行ガイドの概要
description: 手順付きのガイダンスを利用して、組織のサービスを Azure に効果的に移行する方法について説明します。
author: matticusau
ms.author: mlavery
ms.date: 02/25/2020
ms.topic: guide
ms.service: cloud-adoption-framework
ms.subservice: migrate
ms.custom: fasttrack-new, AQC
ms.localizationpriority: high
ms.openlocfilehash: 4d2bb420609bb703a1f32b0912eb9c17ccabe4e9
ms.sourcegitcommit: 72a280cd7aebc743a7d3634c051f7ae46e4fc9ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2020
ms.locfileid: "78225515"
---
# <a name="azure-migration-guide-before-you-start"></a>Azure 移行ガイド:開始する前に

[クラウド導入フレームワークの移行のメソドロジ](../index.md)では、1 つのワークロード、またはリリースごとのワークロードの小さなコレクションを移行する反復的なプロセスについて案内します。 次に、ワークロードが運用環境の需要に対応できるようにするため、各イテレーションにおける、評価、移行、最適化と昇格のプロセスが続きます。 そのクラウドに依存しないプロセスによって、任意のクラウド プロバイダーに移行することができます。

このガイドでは、ご利用のオンプレミス環境から **Azure** に移行する場合の、そのプロセスの簡略化されたバージョンを示します。

::: zone target="docs"

> [!TIP]
> 対話形式で学習したい場合は、Azure portal でこのガイドを参照してください。 Azure portal で [Azure クイック スタート センター](https://portal.azure.com/?feature.quickstart=true#blade/Microsoft_Azure_Resources/QuickstartCenterBlade)にアクセスし、 **[Migrate your environment to Azure]** \(環境を Azure に移行する\) を選択して、段階を追った手順に従います。

::: zone-end

## <a name="migration-tools"></a>[移行ツール](#tab/MigrationTools)

このガイドでは、Azure への移行時に最もよく使用されるメソドロジとクラウドネイティブなツールが公開されるため、Azure への初めて移行への推奨されるパスとなります。 これらのツールは、次のページで提供されます。

> [!div class="checklist"]
>
> - **各ワークロードの技術的な適合を評価する。** 移行の技術的な対応性と適合性を検証します。
> - **サービスを移行する。** オンプレミスのリソースを Azure にレプリケートして、実際の移行を実行します。
> - **コストと課金を管理する。** Azure でコストを管理するために必要なツールについて理解します。
> - **最適化して昇格させる。** ワークロードを運用環境に昇格する前に、コストとパフォーマンスのバランスを最適化します。
> - **サポートを受ける。** 移行段階または移行後のアクティビティ段階で、ヘルプとサポートを受けます。

[クラウド導入フレームワークの準備のメソドロジ](../../ready/index.md)のベスト プラクティスに従って、ランディング ゾーンが既にデプロイされていることを前提としています。

## <a name="when-to-use-this-guide"></a>[このガイドを使用する状況](#tab/WhenToUseThisGuide)

このガイドで説明されているツールはさまざまな移行シナリオをサポートしていますが、このガイドでは、_複雑さが最小限_の限られた範囲の作業に焦点を当てます。 この移行ガイドがご自分のプロジェクトに適しているかどうかを判断するには、次の条件がご自身の状況に該当するかどうかを検討してください。

- 初期移行のワークロードはミッション クリティカルではなく、機密データも含まれていない。
- 同種環境を移行する。
- 移行を完了するために調整する必要があるのは、ごく少数の部署のみである。
- 移行全体を自動化する予定はない。
- 少数のサーバーを移行する。
- 移行するコンポーネントの依存関係のマップは定義が簡単である。
- 業界には、この移行に関連する最小限の規制要件がある。

ご自身の状況に、これらの条件のいずれも該当しない場合は、代わりに[拡大された範囲ガイド](../expanded-scope/index.md)を検討してください。 また、拡大された範囲ガイドが必要な移行を実行するには、Microsoft チームまたはパートナーのいずれかにサポートを依頼することをお勧めします。 Microsoft または認定パートナーと契約しているお客様には、このようなシナリオでさらにお勧めします。 サポートの依頼の詳細情報は、このガイドに記載されています。

<!-- markdownlint-enable MD033 -->

::: zone target="docs"

詳細については、次を参照してください。

- [拡大された範囲ガイド](../expanded-scope/index.md)

::: zone-end
