---
title: ランディング ゾーンの実装オプション
description: 要件に最も適したランディング ゾーンの実装オプションを決定します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 07/07/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.custom: internal
ms.openlocfilehash: 2959fb60d0e7b7b8731fa54227c8dea5dd36087e
ms.sourcegitcommit: 36e85ac734b184de3f29884b744ea74c81ccc72b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/13/2021
ms.locfileid: "103439510"
---
# <a name="landing-zone-implementation-options"></a>ランディング ゾーンの実装オプション

[Azure ランディング ゾーン](./index.md)では、ワークロード用に適切に管理された環境がクラウド導入チームに提供されます。 これらの機能を利用するには、[ランディング ゾーンの設計領域](./design-areas.md)に関するガイダンスに従ってください。

次の実装オプションはそれぞれ、機能以外の要件をサポートするための、特定の運用モデル依存関係のセットに対して設計されています。 各実装オプションには、個別の自動化アプローチが含まれています。 使用可能な場合、参照アーキテクチャと参照実装が含まれているので、クラウドの導入を促進することができます。 各オプションは異なる運用モデルにマップされますが、同じ設計領域が共有されます。 違いは、実装の選択方法です。

## <a name="platform-development-velocity"></a>プラットフォーム開発の速度

最適な実装オプションを選択するときは、推奨される設計領域に加えて、プラットフォーム開発速度 (プラットフォーム チームが必要なスキルを開発できる速さ) が重要な要素となります。 2 つの主なモードを検討します。

**エンタープライズ規模から始める:** ビジネス要件によって、最初からガバナンス、セキュリティ、運用が完全に統合されているランディング ゾーンの高度な初期実装が必要とされる場合は、このモードを使用します。 このアプローチでは、Azure portal またはコードとしてのインフラストラクチャを使用して、環境を設定および構成できます。 また、組織で準備が整ったら、Azure portal とコードとしてのインフラストラクチャ (推奨) との間で切り替えることもできます。 推奨される​コードとしてのインフラストラクチャ アプローチを使用するには、Azure Resource Manager テンプレートと GitHub のスキルが必要になります。

**小規模から始めて拡張する:** これらのスキルを身に付けて、クラウドをよく理解してから意思決定を行うことがより重要な場合は、このモードを使用します。 このアプローチのランディング ゾーンでは、クラウド導入を開始するために必要な基本的なランディング ゾーンの考慮事項の実装にのみ焦点が当てられます。 導入の拡大に合わせて、ガバナンス手法と管理手法のモジュールが、初期ランディング ゾーンの上に構築されます。 Azure ランディング ゾーンの設計原則では、時間の経過と共にリファクタリングが必要になる特定のデザイン領域が示されています。

## <a name="implementation-options"></a>実装オプション

ランディング ゾーンの実装オプションと決定に影響する変数の一部を、次の表に示します。

| 実装オプション | 説明 | デプロイの速度 | より深い設計原則 | デプロイの手順 |
|---|---|---|---|---|
| [CAF 移行ランディング ゾーンのブループリント](./migrate-landing-zone.md) | 危険度の低い資産を移行するための基本的な基盤をデプロイします。 | 小規模から始める | [設計原則](./migrate-landing-zone.md#design-principles) | [デプロイする](./migrate-landing-zone.md) |
| [CAF 基盤ブループリント](./foundation-blueprint.md) | ガバナンス戦略の開発を始めるために必要な最小限のツールが追加されます。 | 小規模から始める | [設計原則](./foundation-blueprint.md#design-principles) | [デプロイする](./foundation-blueprint.md) |
| [CAF エンタープライズ規模のランディング ゾーン (Virtual WAN を使用したハイブリッド接続)](../enterprise-scale/index.md) | エンタープライズ対応のプラットフォーム基盤と、完全な IT ポートフォリオをサポートするために必要なすべての共有サービス (ハイブリッド接続 (Virtual WAN) を含む) がデプロイされます。 | エンタープライズ規模 | [設計原則](../enterprise-scale/design-principles.md) | [デプロイする](https://github.com/Azure/Enterprise-Scale/blob/main/docs/reference/contoso/Readme.md) |
| [CAF エンタープライズ規模のランディング ゾーン (ハブおよびスポークを使用したハイブリッド接続)](../enterprise-scale/index.md) | エンタープライズ対応のプラットフォーム基盤と、完全な IT ポートフォリオをサポートするために必要なすべての共有サービス (ハイブリッド接続 (ハブおよびスポーク) を含む) がデプロイされます。 | エンタープライズ規模 | [設計原則](../enterprise-scale/design-principles.md) | [デプロイする](https://github.com/Azure/Enterprise-Scale/blob/main/docs/reference/adventureworks/README.md) |
| [CAF エンタープライズ規模のランディング ゾーン (foundation)](../enterprise-scale/index.md) | エンタープライズ対応のプラットフォーム基盤と、完全な IT ポートフォリオをサポートするために必要なすべての共有サービスがデプロイされます。接続は、必要に応じて後で追加できます。 | エンタープライズ規模 | [設計原則](../enterprise-scale/design-principles.md) | [デプロイする](https://github.com/Azure/Enterprise-Scale/blob/main/docs/reference/wingtip/README.md) |
| [CAF エンタープライズ規模のランディングゾーン (小規模および中規模企業)](../enterprise-scale/index.md) | この参照実装は、大規模な IT チームを持たず、きめ細かい管理委任モデルを必要としない組織向けです。 | エンタープライズ規模 | [設計原則](../enterprise-scale/design-principles.md) | [デプロイする](https://github.com/Azure/Enterprise-Scale/blob/main/docs/reference/treyresearch/README.md) |
| [クラウド導入フレームワークのエンタープライズ規模用の terraform モジュール](../enterprise-scale/terraform-module-caf-enterprise-scale.md) | Terraform を使用して、エンタープライズ対応のプラットフォーム基盤をデプロイします。 Terraform を使用してプラットフォームを管理するときに、推奨されるリソース階層とガバナンスモデルの配信を高速化する必要がある場合に、このオプションを使用します。 このモジュールは、Terraform レジストリで正式に検証されます。 共有サービス、ネットワーク接続、およびアプリケーショ ンワークロードは、デプロイに統合することも、個別に管理することもできます。 | エンタープライズ規模 | [設計原則](../enterprise-scale/design-principles.md) | [デプロイする](https://registry.terraform.io/modules/Azure/caf-enterprise-scale/azurerm/latest) |
| [CAF Terraform モジュール](./terraform-landing-zone.md) | マルチクラウド運用モデルに対するサードパーティのパス。 このパスでは、Azure ファーストの運用モデルを制限できます。 | 小規模から始める | [設計原則](./terraform-landing-zone.md#design-decisions) | [デプロイする](./terraform-landing-zone.md#customize-and-deploy-your-first-landing-zone) |
| [パートナー ランディング ゾーン](./partner-landing-zone.md) | クラウド導入フレームワークの準備手法に合ったオファリングを提供するパートナーは、独自のカスタマイズされた実装オプションを提供できます。 | 変数 | [設計原則](./partner-landing-zone.md) | [パートナーを見つける](https://www.microsoft.com/azure/partners/adopt?filters=ready) |

次の表では、より技術的な意志決定プロセスをガイドするために、若干異なる視点からこれらの実装オプションをいくつか示します。

| 実装オプション | ハブ | スポーク | デプロイ テクノロジ | デプロイの手順 |
|---|---|---|---|---|
| [クラウド導入フレームワークのエンタープライズ規模のランディング ゾーン](../enterprise-scale/index.md) | Included | Included | Azure Resource Manager テンプレート、Azure portal、Azure Policy、GitHub | [デプロイする](../enterprise-scale/implementation-guidelines.md) |
| [CAF 移行ランディング ゾーンのブループリント](./migrate-landing-zone.md) | リファクタリングが必要 | Included | Azure Resource Manager テンプレート、Azure portal、Azure Blueprints | [デプロイする](./migrate-landing-zone.md) |
| [CAF Terraform モジュール](./terraform-landing-zone.md) | 仮想データセンター モジュールに含まれます | Included | Terraform | [デプロイする](./terraform-landing-zone.md#customize-and-deploy-your-first-landing-zone) |
| [クラウド導入フレームワークのエンタープライズ規模用の terraform モジュール](../enterprise-scale/terraform-module-caf-enterprise-scale.md)  | 除外 | 除外 | Terraform | [デプロイする](https://registry.terraform.io/modules/Azure/caf-enterprise-scale/azurerm/latest) |

## <a name="next-steps"></a>次のステップ

先に進むには、上記の表に示されている実装オプションのいずれかを選択します。 各オプションには、デプロイの手順へのリンクと、実装をガイドする特定の設計原則が含まれています。
