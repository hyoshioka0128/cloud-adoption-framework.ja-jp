---
title: '複雑な企業向けのガバナンス ガイド: ガバナンス戦略の背景にある初期の企業ポリシー'
titleSuffix: Microsoft Cloud Adoption Framework for Azure
description: '複雑な企業向けのガバナンス ガイド: ガバナンス戦略の背景にある初期の企業ポリシー'
author: BrianBlanchard
ms.author: brblanch
ms.date: 09/05/2019
ms.topic: guide
ms.service: cloud-adoption-framework
ms.subservice: govern
ms.custom: governance
ms.openlocfilehash: 56629e6f313614965ee1baddaa08e4264b4bef53
ms.sourcegitcommit: 35c162d2d09ec1c4a57d3d57a5db1d56ee883806
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/17/2019
ms.locfileid: "72547669"
---
# <a name="governance-guide-for-complex-enterprises-initial-corporate-policy-behind-the-governance-strategy"></a>複雑な企業向けのガバナンス ガイド: ガバナンス戦略の背景にある初期の企業ポリシー

次の企業ポリシーは、このガイドの出発点である初期のガバナンス ポジションを定義しています。 この記事では、初期段階のリスク、初期ポリシー ステートメント、ポリシー ステートメントを適用するための初期プロセスを定義します。

> [!NOTE]
>企業ポリシーは技術文書ではありませんが、多くの技術的判断の根拠になります。 [概要](./index.md)で説明するガバナンス MVP は、最終的にこのポリシーから派生します。 ガバナンス MVP を実装する前に、組織はその目標とビジネス リスクに基づいて企業ポリシーを策定する必要があります。

## <a name="cloud-governance-team"></a>クラウド ガバナンス チーム

CIO は最近、個人データとミッションクリティカル ポリシーの歴史を理解し、それらのポリシーを変更した場合の影響をレビューするために、IT ガバナンス チームとの会議を開きました。 IT と企業にとってのクラウドの可能性全般についても議論しました。

会議後、IT ガバナンス チームの 2 人のメンバーが、クラウド計画の取り組みを調査およびサポートするための許可を求めました。 ガバナンスの必要性とシャドウ IT を制限する機会を認識していた IT ガバナンス責任者はこのアイデアを支持しました。 このような経緯で、クラウド ガバナンス チームが誕生しました。 このチームはこれから数か月間、クラウドの検証過程で経験する多くの失敗について、ガバナンスの観点からクリーンアップを継承していきます。 これにより_クラウド管理人_の呼び名を得ることになります。 今後のイテレーションの中で、このガイドにおけるチームの役割は刻々と変わっていきます。

[!INCLUDE [business-risk](../../../../includes/business-risks.md)]

## <a name="tolerance-indicators"></a>許容度指標

現在、リスク許容度は高く、クラウド ガバナンスへの投資意欲は低い状態です。 このように、許容度指標は、時間とエネルギーの投資を誘発する早期警告システムとして機能します。 以下の指標が観測された場合、ガバナンス戦略を前進させることが賢明です。

- **コスト管理:** クラウドへのデプロイ規模が 1,000 資産を超えている、または毎月の支出が 10,000 米ドルを超えている。
- **ID ベースライン:** レガシまたはサード パーティの多要素認証要件を持つアプリケーションが含まれている。
- **セキュリティ ベースライン:** 定義したクラウド導入計画に保護対象データが含まれている。
- **リソースの整合性:** 定義したクラウド導入計画にミッションクリティカルなアプリケーションが含まれている。

[!INCLUDE [policy-statements](../../../../includes/policy-statements.md)]

## <a name="next-steps"></a>次の手順

この企業ポリシーにより、クラウド ガバナンス チームでは、導入のための基盤になるガバナンス MVP を実装する準備が整います。 次の手順はこの MVP の実装です。

> [!div class="nextstepaction"]
> [ベスト プラクティスの説明](./prescriptive-guidance.md)
