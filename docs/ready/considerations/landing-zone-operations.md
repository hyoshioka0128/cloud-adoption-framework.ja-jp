---
title: ランディング ゾーンの運用の改善
description: ランディング ゾーンの運用の改善
author: BrianBlanchard
ms.author: brblanch
ms.date: 04/04/2020
ms.topic: overview
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.openlocfilehash: 43325baa63035779a7a7482faaf828f2ecc5614c
ms.sourcegitcommit: 60d8b863d431b5d7c005f2f14488620b6c4c49be
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2020
ms.locfileid: "83223084"
---
# <a name="improve-landing-zone-operations"></a>ランディング ゾーンの運用の改善

ランディング ゾーンの運用では、運用管理の初期基盤が提供されます。 運用が拡大するにつれて、これらの改善により、オペレーショナル エクセレンス、信頼性、およびパフォーマンスの要件を満たすためにランディング ゾーンがリファクタリングされます。

## <a name="landing-zone-operations-best-practices"></a>ランディング ゾーンの運用に関するベスト プラクティス

次のリンクは、ランディング ゾーンの運用を改善するためのベスト プラクティスを提供します。

- [Azure サーバー管理](../../manage/azure-server-management/index.md):運用管理に必要なクラウドネイティブ ツールとサービスを組み込むためのオンボーディング ガイドです。
- [ハイブリッド監視](../../manage/monitor/index.md):多くのお客様は、System Center Operations Manager にかなりの投資を既に行ってきました。 これらのお客様の場合、ハイブリッド監視のこのガイドによって、クラウドネイティブのレポート作成ツールを Operations Manager のツールと比較したり、照らし合わせたりできます。 この比較により、運用管理にどのツールを使用するかをより簡単に決定できるようになります。
- [管理操作の一元化](../../manage/centralize-operations.md): Azure Lighthouse を使用して、複数の Azure テナントの運用管理を一元化します。
- [運用適合性レビューの確立](../../manage/operational-fitness-review.md): 運用適合性について環境を再確認します。
- ワークロード固有の運用に関するベスト プラクティス:
  - [回復性のチェックリスト](https://docs.microsoft.com/azure/architecture/checklist/resiliency-per-service?toc=/azure/cloud-adoption-framework/toc.json&bc=/azure/cloud-adoption-framework/_bread/toc.json)
  - [障害モードの分析](https://docs.microsoft.com/azure/architecture/resiliency/failure-mode-analysis?toc=/azure/cloud-adoption-framework/toc.json&bc=/azure/cloud-adoption-framework/_bread/toc.json)
  - [リージョン全体でのサービスの中断から回復する](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region?toc=/azure/cloud-adoption-framework/toc.json&bc=/azure/cloud-adoption-framework/_bread/toc.json)
  - [データの破損または偶発的な削除から復旧する](https://docs.microsoft.com/azure/architecture/framework/resiliency/data-management?toc=/azure/cloud-adoption-framework/toc.json&bc=/azure/cloud-adoption-framework/_bread/toc.json)

## <a name="four-steps-to-improve-operations-beyond-a-single-landing-zone"></a>1 つのランディング ゾーンを超えて運用を改善するための 4 つのステップ

[管理手法](../../manage/index.md)では、運用管理能力を構築するための全体的なガイダンスを提供します。[管理手法](../../manage/index.md)に関するページを参照してください。 この手法の基本構造と、その手法の次の手順を使用して、ランディング ゾーンの運用とすべてのランディング ゾーンでの運用を改善します。

<!-- cSpell:ignore caf -->

![管理手法](../../_images/manage/caf-manage.png)

1. [管理ベースラインの確立](../../manage/azure-server-management/index.md):管理ベースラインは、運用管理の基礎となります。 初期の運用を改善するためには、この最初のステップの下にあるガイダンスがどのランディング ゾーンにも当てはまります。
2. [ビジネス コミットメントの定義](../../manage/considerations/business-alignment.md):ランディング ゾーン内の各ワークロードの重要度と影響を理解することで、すべてのランディング ゾーンの継続的な管理の向上のための "完了の定義" が確立されます。 このプロセスでは、各ワークロードの信頼性、パフォーマンス、および運用の要件も特定されます。
3. [管理ベースラインの展開](../../manage/best-practices.md):この一連のベスト プラクティスは、初期ベースラインを超えるランディング ゾーンの運用を改善するために適用できます。
4. [高度な運用と設計の原則](../../manage/design-principles.md):より詳しい要件を満たすために、特定のワークロード、プラットフォーム、または完全なランディング ゾーンの設計と運用を確認します。

## <a name="test-driven-development-cycle"></a>テスト駆動開発サイクル

セキュリティの強化を始める前に、"完了の定義" とすべての "受け入れ基準" を理解しておくことが重要です。 詳細については、[ランディング ゾーンのテスト駆動開発](./test-driven-development.md)および [Azure でのテスト駆動開発](./azure-test-driven-development.md)に関する記事を参照してください。

![クラウド ランディング ゾーンのテスト駆動開発プロセス](../../_images/ready/test-driven-development-process.png)

## <a name="next-steps"></a>次のステップ

大規模な導入をサポートするための[ランディング ゾーンのガバナンスの改善](./landing-zone-governance.md)方法を理解します。

> [!div class="nextstepaction"]
> [ランディング ゾーンのガバナンスの改善](./landing-zone-governance.md)
