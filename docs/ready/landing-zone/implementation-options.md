---
title: ランディング ゾーンの実装オプション
description: 要件に最も適したランディング ゾーンの実装オプションを決定します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 07/07/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.openlocfilehash: 6a5aad81f1ea855434160c2bc8aefd313fca7335
ms.sourcegitcommit: 12fa4597633ca8e04efbae7d0bd7526d3581618e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88662202"
---
# <a name="landing-zone-implementation-options"></a>ランディング ゾーンの実装オプション

[Azure ランディング ゾーン](./index.md)では、ワークロード用に適切に管理された環境がクラウド導入チームに提供されます。 これらの機能を利用するには、[ランディング ゾーンの設計領域](./design-areas.md)に関するガイダンスに従ってください。

次の実装オプションはそれぞれ、機能以外の要件をサポートするための、特定の運用モデル依存関係のセットに対して設計されています。 各実装オプションには、個別の自動化アプローチが含まれています。 使用可能な場合、参照アーキテクチャと参照実装が含まれているので、クラウドの導入を促進することができます。 各オプションは異なる運用モデルにマップされますが、同じ設計領域が共有されます。 違いは、実装の選択方法です。

## <a name="platform-development-velocity"></a>プラットフォーム開発の速度

最適なデプロイ オプションを選択するときは、推奨される設計領域に加えて、プラットフォーム開発速度 (プラットフォーム チームが必要なスキルを開発できる速さ) が重要な要素となります。 2 つの主なモードを検討します。

**エンタープライズ規模から始める:** ビジネス要件によって、最初からガバナンス、セキュリティ、運用が完全に統合されているランディング ゾーンの高度な初期実装が必要とされる場合は、このモードを使用します。 このアプローチでは、Azure portal またはコードとしてのインフラストラクチャを使用して、環境を設定および構成できます。 また、組織で準備が整ったら、ポータルとコードとしてのインフラストラクチャ (推奨) との間で切り替えることもできます。 コードとしてのインフラストラクチャのアプローチには、Azure Resource Manager テンプレートと GitHub のスキルが必要です。

**小規模から始めて拡張する:** これらのスキルを身に付けて、クラウドをよく理解してから意思決定を行うことがより重要な場合は、このモードを使用します。 このアプローチのランディング ゾーンでは、クラウド導入を開始するために必要な基本的なランディング ゾーンの考慮事項の実装にのみ焦点が当てられます。 導入の拡大に合わせて、ガバナンス手法と管理手法のモジュールが、初期ランディング ゾーンの上に構築されます。 Azure ランディング ゾーンの設計原則では、時間の経過と共にリファクタリングが必要になる特定のデザイン領域が示されています。

## <a name="implementation-options"></a>実装オプション

ランディング ゾーンの実装オプションと決定に影響する変数の一部を、次の表に示します。

| 実装オプション | 説明 | デプロイの速度 | より深い設計原則 | デプロイの手順 |
|---|---|---|---|---|
| [CAF 移行ランディング ゾーンのブループリント](./migrate-landing-zone.md) | 危険度の低い資産を移行するための基本的な基盤をデプロイします。 | 小規模から始める | [設計原則](./migrate-landing-zone.md#design-principles) | [デプロイする](./migrate-landing-zone.md) |
| [CAF 基盤ブループリント](./foundation-blueprint.md) | ガバナンス戦略の開発を始めるために必要な最小限のツールが追加されます。 | 小規模から始める | [設計原則](./foundation-blueprint.md#design-principles) | [デプロイする](./foundation-blueprint.md) |
| [CAF エンタープライズ規模のランディング ゾーン](../enterprise-scale/index.md) | エンタープライズ対応のプラットフォーム基盤と、完全な IT ポートフォリオをサポートするために必要なすべての共有サービスがデプロイされます。 | エンタープライズ規模 | [設計原則](../enterprise-scale/design-principles.md) | [デプロイする](https://github.com/Azure/Enterprise-Scale/blob/main/docs/reference/contoso/Readme.md) |
| [CAF Terraform モジュール](./terraform-landing-zone.md) | マルチクラウド運用モデルに対するサードパーティのパス。 このパスでは、Azure ファーストの運用モデルを制限できます。 | 小規模から始める | [設計原則](./terraform-landing-zone.md#design-decisions) | [デプロイする](./terraform-landing-zone.md#customize-and-deploy-your-first-landing-zone) |
| [パートナー ランディング ゾーン](./partner-landing-zone.md) | クラウド導入フレームワークの準備手法に合ったオファリングを提供するパートナーは、独自のカスタマイズされた実装オプションを提供できます。 | 変数 | [設計原則](./partner-landing-zone.md) | [パートナーを見つける](https://www.microsoft.com/azure/partners/adopt?filters=ready) |

次の表では、より技術的な意志決定プロセスをガイドするために、若干異なる視点からこれらの実装オプションをいくつか示します。

| 実装オプション | ハブ | スポーク | デプロイ テクノロジ | デプロイの手順 |
|---|---|---|---|---|
| [クラウド導入フレームワークのエンタープライズ規模のランディング ゾーン](../enterprise-scale/index.md) | Included  | Included | Azure Resource Manager テンプレート、Azure portal、Azure Policy、GitHub | [デプロイする](../enterprise-scale/implementation-guidelines.md) |
| [クラウド導入フレームワークの移行ランディング ゾーンのブループリント](./migrate-landing-zone.md) | リファクタリングが必要 | Included | Azure Resource Manager テンプレート、Azure portal、Azure Blueprints | [デプロイする](./migrate-landing-zone.md) |
| [クラウド導入フレームワークの Terraform モジュール](./terraform-landing-zone.md)  | 仮想データセンター モジュールに含まれます | Included | Terraform | [デプロイする](./terraform-landing-zone.md#customize-and-deploy-your-first-landing-zone) |

## <a name="next-steps"></a>次のステップ

先に進むには、上記の表に示されている実装オプションのいずれかを選択します。 各オプションには、デプロイの手順へのリンクと、実装をガイドする特定の設計原則が含まれています。
