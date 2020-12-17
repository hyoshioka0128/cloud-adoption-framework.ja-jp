---
title: 標準的な企業のガバナンス:初期の企業ポリシー
description: Azure 向けのクラウド導入フレームワークを使用して、初期の標準的なガバナンスの位置、初期段階のリスク、初期のポリシー ステートメント、および初期の適用プロセスを定義します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 09/05/2019
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: govern
ms.custom: internal
ms.openlocfilehash: 8313b926ff9a65d9210e9d9adaa66f4ae9862aa7
ms.sourcegitcommit: b6f2b4f8db6c3b1157299ece1f044cff56895919
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2020
ms.locfileid: "97020745"
---
# <a name="standard-enterprise-governance-guide-initial-corporate-policy-behind-the-governance-strategy"></a>標準的な企業のガバナンス ガイド:ガバナンス戦略の背景にある初期の企業ポリシー

次の企業ポリシーでは、このガイドの出発点である初期のガバナンス ポジションが定義されています。 この記事では、初期段階のリスク、初期ポリシー ステートメント、ポリシー ステートメントを適用するための初期プロセスを定義します。

> [!NOTE]
> 企業ポリシーは技術文書ではありませんが、多くの技術的判断の根拠になります。 [概要](./index.md)で説明するガバナンス MVP は、最終的にこのポリシーから派生します。 ガバナンス MVP を実装する前に、組織はその目標とビジネス リスクに基づいて企業ポリシーを策定する必要があります。

## <a name="cloud-governance-team"></a>クラウド ガバナンス チーム

この物語のクラウド管理チームは、ガバナンスのニーズを認識している 2 人のシステム管理者で構成されます。 これからの数か月間、2 人は会社のクラウド プレゼンスのガバナンスをクリーンアップするジョブを引き継ぎ、*クラウド管理者* という肩書を与えられるようになります。 今後のイテレーションで、この肩書は変わる可能性があります。

[!INCLUDE [business-risk](../../../../includes/business-risks.md)]

## <a name="tolerance-indicators"></a>許容度指標

現在、リスクの許容度は高く、クラウド ガバナンスへの投資意欲は低い状態です。 このように、許容度指標は、時間とエネルギーの投資を増やす早期警告システムとして機能します。 以下の指標が観測された場合、ガバナンス戦略を反復的に改善する必要があります。

- **コスト管理:** デプロイのスケールが、リソースの数または月額コストの事前に定義された制限を超えています。
- **セキュリティ ベースライン:** 定義したクラウド導入計画に保護対象データが含まれている。
- **リソースの整合性:** 定義したクラウド導入計画にミッションクリティカルなアプリケーションが含まれている。

[!INCLUDE [policy-statements](../../../../includes/policy-statements.md)]

## <a name="next-steps"></a>次のステップ

この企業ポリシーにより、クラウド ガバナンス チームでは、導入のための基盤になるガバナンス MVP を実装する準備が整います。 次の手順はこの MVP の実装です。

> [!div class="nextstepaction"]
> [ベスト プラクティスの説明](./prescriptive-guidance.md)
