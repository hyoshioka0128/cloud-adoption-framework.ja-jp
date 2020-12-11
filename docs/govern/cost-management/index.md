---
title: Cost Management の規範の概要
description: クラウド ガバナンス戦略の一環として Cost Management の規範を策定する方法について説明します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 09/17/2019
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: govern
ms.custom: internal
ms.openlocfilehash: 7bf50f9cea70eca3a4f6f3eefe56567fe463b0b0
ms.sourcegitcommit: b6f2b4f8db6c3b1157299ece1f044cff56895919
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2020
ms.locfileid: "97021952"
---
# <a name="cost-management-discipline-overview"></a>Cost Management の規範の概要

Cost Management 規範は、[クラウド導入フレームワーク ガバナンス モデル](../index.md)の[クラウド ガバナンスの 5 つの規範](../governance-disciplines.md)のうちの 1 つです。 多くのお客様にとって、クラウド テクノロジを採用する際には、コストの管理が大きな問題になります。 パフォーマンスに対する要求、導入ペース、およびクラウド サービス コストのバランスをとることが困難な場合があります。 これは、クラウド テクノロジを実装する大規模なビジネス変革の際に特に該当します。 このセクションでは、クラウド ガバナンス戦略の一環として Cost Management 規範を策定する方法について概要を説明します。

> [!NOTE]
> Cost Management 規範は、IT 関連コストに関する組織の財務管理に関連する既存のビジネス チーム、会計実務、および手順に代わるものではありません。 この規範の主な目的は、IT 支出に関連したクラウド関連の潜在的なリスクを識別し、クラウド リソースのデプロイとその管理を担当するビジネス チームと IT チームにリスク軽減のガイダンスを提供することです。

このガイドの主な対象読者は、お客様の組織のクラウド アーキテクトおよびお客様のクラウド ガバナンス チームのその他のメンバーです。 この規範を使用した決定、ポリシー、プロセスには、ビジネス チームと IT チームの該当メンバー (特にクラウド ベースのワークロードの所有、管理、支払いを担当するリーダー) が関与して、議論する必要があります。

## <a name="policy-statements"></a>ポリシー ステートメント

実践的なポリシー ステートメントと、その結果のアーキテクチャの要件は、Cost Management の規範の基盤となります。 [サンプル ポリシー ステートメント](./policy-statements.md)を開始点として使用して、Cost Management のポリシーを定義します。

> [!CAUTION]
> このサンプル ポリシーは、よくあるお客様の体験をもとにしています。 特定のクラウド ガバナンスの要件にこれらのポリシーが合うように調整するには、次の手順を行い、お客様独自のビジネス ニーズに合うポリシー ステートメントを作成してください。

## <a name="develop-governance-policy-statements"></a>ガバナンス ポリシー ステートメントの策定

次の手順を使用して、環境内でコストを管理するためのガバナンス ポリシーを定義できます。

| <span title="アイコン">&nbsp;</span> | <span title="説明">&nbsp;</span> |
|--|--|
| <br> ![テンプレート アイコン](../../_images/govern/process-template.png) | <br> [Cost Management 規範テンプレート](./template.md):Cost Management 規範を文書化するテンプレートをダウンロードします。 |
| <br> ![リスク アイコン](../../_images/govern/process-risks.png) | <br> [ビジネス リスク](./business-risks.md):一般的に Cost Management の規範に関係する動機およびリスクを理解できます。 |
| <br> ![メトリック アイコン](../../_images/govern/process-metrics.png) | <br> [インジケーターとメトリック](./metrics-tolerance.md):Cost Management の規範に投資する最適なタイミングであるかどうかを判断するためのインジケーターです。 |
| <br> ![準拠アイコン](../../_images/govern/process-enforce.png) | <br> [ポリシー準拠プロセス](./compliance-processes.md):Cost Management の規範でのポリシー準拠を確保するための推奨の手順です。 |
| <br> ![成熟度アイコン](../../_images/govern/process-maturity.png) | <br> [成熟度](./discipline-improvement.md):クラウドの導入の段階とクラウド管理の成熟度を調整します。 |
| <br> ![ツールチェーン アイコン](../../_images/govern/process-toolchain.png) | <br> [ツールチェーン](./toolchain.md):Cost Management の規範をサポートするために実装できる Azure サービスです。 |

## <a name="next-steps"></a>次のステップ

特定の環境でビジネス上のリスクを評価する方法の概要を学びます。

> [!div class="nextstepaction"]
> [ビジネス リスクを理解する](./business-risks.md)
