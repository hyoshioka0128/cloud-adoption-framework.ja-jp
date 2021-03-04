---
title: ID ベースライン規範の概要
description: クラウド ガバナンス戦略の一環として ID ベースライン規範を策定する方法を理解します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 09/17/2019
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: govern
ms.custom: internal
ms.openlocfilehash: 3e0f1ad5cf9c0fc402b456ad2a781302bc554c9a
ms.sourcegitcommit: b8f8b7631aabaab28e9705934bf67dad15e3a179
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101790750"
---
# <a name="identity-baseline-discipline-overview"></a>ID ベースライン規範の概要

ID ベースラインは、[クラウド導入フレームワーク ガバナンス モデル](../index.md)の[クラウド ガバナンスの 5 つの規範](../governance-disciplines.md)のうちの 1 つです。 ID はクラウドにおける主要なセキュリティ境界とますます考えられるようになっており、従来のネットワーク セキュリティから焦点が移っています。 ID サービスでは IT 環境内でアクセス制御と組織をサポートする主要なメカニズムが提供され、ID ベースラインの規範はクラウド導入作業全体に認証と承認の要件を一貫して適用することで[セキュリティ ベースラインの規範](../security-baseline/index.md)を補完します。

> [!NOTE]
> ID ベースライン規範は、組織による ID サービスの管理とセキュリティ保護を可能にする既存の IT チーム、プロセス、および手順にとって代わるものではありません。 この規範の主な目的は、ID 関連の潜在的なビジネス リスクを識別し、ID 管理インフラストラクチャの実装、維持、運用を担当する IT スタッフにリスク軽減のためのガイダンスを提供することです。 ガバナンス ポリシーおよびプロセスの策定時には、行っている計画と確認のプロセスに、関係する IT チームを関与させるようにしてください。

クラウド導入フレームワークのこのセクションでは、クラウド ガバナンス戦略の一環として ID ベースライン規範を策定する方法について、概要を説明します。 このガイドの主な対象読者は、お客様の組織のクラウド アーキテクトおよびお客様のクラウド ガバナンス チームのその他のメンバーです。 この規範に基づく決定、ポリシー、およびプロセスには、組織の ID 管理ソリューションの実装と管理を担当する IT チームの該当メンバーが関与して、議論する必要があります。

社内に ID とセキュリティの専門家がいない場合、この規範の一環として外部コンサルタントを雇用することを検討してください。 また、この規範に関連する懸案事項を相談するために、[Microsoft コンサルティング サービス](https://www.microsoft.com/industry/services/consulting)、[Microsoft FastTrack](https://azure.microsoft.com/programs/azure-fasttrack/) クラウド導入サービス、または外部のその他のクラウド導入パートナーを雇用することを検討してください。

## <a name="policy-statements"></a>ポリシー ステートメント

実践的なポリシー ステートメントと、その結果のアーキテクチャの要件は、ID ベースラインの規範の基盤となります。 [サンプル ポリシー ステートメント](./policy-statements.md)を開始点として使用して、ID ベースラインのポリシーを定義します。

> [!CAUTION]
> このサンプル ポリシーは、よくあるお客様の体験をもとにしています。 特定のクラウド ガバナンスの要件にこれらのポリシーが合うように調整するには、次の手順を行い、お客様独自のビジネス ニーズに合うポリシー ステートメントを作成してください。

## <a name="develop-governance-policy-statements"></a>ガバナンス ポリシー ステートメントの策定

次の手順では、ID ベースライン性規範を開発する際の例と考えられるオプションを示します。 各手順は、ID に関連するリスクを管理するために必要なポリシーとプロセスを確立するためのクラウド ガバナンス チーム内での話し合いや、組織の影響を受けるビジネス チームや IT チームとの討議の中で、出発点として使用します。

| <span title="アイコン">&nbsp;</span> | <span title="説明">&nbsp;</span> |
|--|--|
| <br> ![テンプレート アイコン](../../_images/govern/process-template.png)   | <br> [ID ベースライン規範テンプレート](./template.md):ID ベースライン規範を文書化するためのテンプレートをダウンロードします。 |
| <br> ![リスク アイコン](../../_images/govern/process-risks.png)         | <br> [ビジネス リスク](./business-risks.md):一般的に ID ベースラインの規範に関係する動機およびリスクを理解できます。 |
| <br> ![メトリック アイコン](../../_images/govern/process-metrics.png)     | <br> [インジケーターとメトリック](./metrics-tolerance.md):ID ベースラインの規範に投資する最適なタイミングであるかどうかを判断するためのインジケーターです。 |
| <br> ![準拠アイコン](../../_images/govern/process-enforce.png)   | <br> [ポリシー準拠プロセス](./compliance-processes.md):ID ベースラインの規範がポリシーに準拠するようにするための推奨の手順です。 |
| <br> ![成熟度アイコン](../../_images/govern/process-maturity.png)   | <br> [成熟度](./discipline-improvement.md):クラウドの導入の段階とクラウド管理の成熟度を調整します。 |
| <br> ![ツールチェーン アイコン](../../_images/govern/process-toolchain.png) | <br> [ツールチェーン](./toolchain.md):ID ベースラインの規範をサポートするために実装できる Azure サービスです。 |

## <a name="next-steps"></a>次のステップ

特定の環境で[ビジネス上のリスク](./business-risks.md)を評価する方法の概要です。

> [!div class="nextstepaction"]
> [ビジネス リスクを理解する](./business-risks.md)
