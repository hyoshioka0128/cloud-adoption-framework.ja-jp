---
title: Azure のランディング ゾーン用のテスト駆動開発
description: Azure のランディング ゾーン用のテスト駆動開発。
author: BrianBlanchard
ms.author: brblanch
ms.date: 05/15/2020
ms.topic: overview
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.openlocfilehash: 8d44734998d3a59391d9fcceb8f39704b845bafc
ms.sourcegitcommit: 71a4f33546443d8c875265ac8fbaf3ab24ae8ab4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2020
ms.locfileid: "86479791"
---
# <a name="test-driven-development-for-landing-zones-in-azure"></a>Azure のランディング ゾーン用のテスト駆動開発

[ランディング ゾーン用のテスト駆動開発 (TDD)](./test-driven-development.md) に関する前の記事で説明したように、TDD サイクルは、クラウド導入計画を実現するために必要な特定の機能の受け入れ基準を検証するテストから始まります。 ランディング ゾーンの展開またはリファクタリングをテストして、受け入れ基準が満たされていることを検証できます。 この記事では、テスト駆動開発サイクルを自動化するための、Azure のクラウドネイティブのツールチェーンの概要を示します。

## <a name="azure-tools-to-support-landing-zone-tdd-cycles"></a>ランディング ゾーンの TDD サイクルをサポートするための Azure ツール

![Azure のテスト駆動開発ツール](../../_images/ready/azure-tdd-tools.png)
_図 1: Azure のテスト駆動開発ツール。_

Azure ネイティブのガバナンス製品とサービスのツールチェーンは、ランディング ゾーンを作成するために、テスト駆動開発に簡単に統合できます。 これらの各ツールは特定の目的に対応しているため、TDD サイクルに合わせてランディング ゾーンを簡単に開発、テスト、デプロイできます。

## <a name="microsoft-provided-test-and-deployment-templates-to-accelerate-tdd"></a>Microsoft が提供する、TDD を促進するためのテストとデプロイのテンプレート

次の例は、ガバナンスのために Microsoft によって提供されています。 それぞれを、ランディング ゾーンのテスト駆動開発サイクルでテストまたは一連のテストとして使用できます。 以降のセクションで、各ツールについて詳しく説明します。

- Azure Blueprints では、さまざまな[ブループリントのサンプル](https://docs.microsoft.com/azure/governance/blueprints/samples)が提供されます。これには、テストのポリシーとデプロイ用のテンプレートが含まれます。 これらのブループリントのサンプルにより、TDD サイクルにおける開発、デプロイ、およびテストの作業を加速させることができます。
- Azure Policy には[組み込みのポリシー イニシアティブ](https://docs.microsoft.com/azure/governance/policy/samples/built-in-initiatives)も含まれています。これを使用すると、ランディング ゾーンの完了の完全な定義をテストして適用することができます。 Azure Policy には、完了の定義内の個々の受け入れ基準を満たすことができる[組み込みのポリシー定義](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies)が含まれています。
- Azure Graph には高度な[クエリのサンプル](https://docs.microsoft.com/azure/governance/resource-graph/samples/advanced)が含まれていて、高度なテスト シナリオ用にランディング ゾーン内にワークロードをデプロイする方法を理解するために使用できます。
- [Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates)には、ランディング ゾーンとワークロードのデプロイの迅速化に役立つソース コードのテンプレートが用意されています。

上記の例は、TDD サイクルを高速化するためのツールとして使用できます。 これらは、次のセクションのガバナンス ツールで実行されます。これにより、クラウド プラットフォーム チームが独自のソース コードとテストを作成できます。

## <a name="azure-governance-tools-that-can-accelerate-tdd-cycles"></a>TDD サイクルを高速化できる Azure ガバナンス ツール

[Azure Policy](https://docs.microsoft.com/azure/governance/policy):デプロイまたはデプロイの試行がガバナンス ポリシーから逸脱したときに、Azure Policy によって自動的に検出、保護、解決が可能です。 ただし、Azure Policy には、完了の定義で受け入れ基準をテストするための主要なメカニズムも用意されています。 TDD サイクルでは、単一の受け入れ基準をテストするためのポリシー定義を作成できます。 同様に、すべての受け入れ基準は、サブスクリプション全体に割り当てられたポリシー イニシアティブに追加できます。 このアプローチは、ランディング ゾーンを変更する前に赤テストのメカニズムを提供します。 ランディング ゾーンは、完了の定義を満たすと、今後のリリースでテストが失敗する可能性のあるコードの変更を回避するために、テスト条件を適用するために使用できます。

[Azure Blueprints](https://docs.microsoft.com/azure/governance/blueprints):Azure Blueprint は、ポリシーとその他のデプロイ ツールを、複数のランディング ゾーンに割り当てることができる反復可能なパッケージにグループ化します。 ブループリントでは、複数の導入作業で共通の完了の定義を共有する場合に有用であることがわかり、時間の経過に伴って更新できます。 また、ランディング ゾーンの以降の展開とリファクタリングの作業の際のデプロイにも役立ちます。

[Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview):Resource Graph には、ランディング ゾーン内にデプロイされた資産に関する情報に基づいて、データ ドリブン テストを作成するためのクエリ言語が用意されています。 このツールでは、後で導入計画の中で、ワークロード資産と基になるクラウド環境との間の相互作用に基づいて、複雑なテストを定義することもできます。

[Azure Resource Manager テンプレート](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview): これらのテンプレートは、Azure にデプロイされているすべての環境の主要なソース コードを提供します。 Terraform などのいくつかのサードパーティ製のツールでは、独自の ARM テンプレートが生成され、Azure Resource Manager に送信されます。

[Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/management/overview):Resource Manager は、ビルド関数とデプロイ機能に一貫したプラットフォームを提供します。 このプラットフォームでは、ソース コード定義に基づいてランディング ゾーンをデプロイできます。

## <a name="next-steps"></a>次のステップ

最初のランディング ゾーンのリファクタリングを開始するため、[基本的なランディング ゾーンに関する考慮事項](./basic-considerations.md)を評価します。

> [!div class="nextstepaction"]
> [基本的なランディング ゾーンに関する考慮事項](./basic-considerations.md)
