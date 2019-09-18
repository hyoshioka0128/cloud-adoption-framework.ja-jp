---
title: クラウド ガバナンス設計ガイドと企業ポリシーの合致
titleSuffix: Microsoft Cloud Adoption Framework for Azure
description: クラウド ガバナンス設計ガイドと企業ポリシーの合致
author: BrianBlanchard
ms.author: brblanch
ms.date: 01/04/2019
ms.topic: guide
ms.service: cloud-adoption-framework
ms.subservice: govern
ms.openlocfilehash: c7be14efe6723a32808ba9bd2ba01f48292305df
ms.sourcegitcommit: 443c28f3afeedfbfe8b9980875a54afdbebd83a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/16/2019
ms.locfileid: "71031318"
---
<!---
I've established policies. How to help developers adopt these policies?
Draft an architecture design guide.

[Aspirational statement] If you're using Azure, you can use one of ours as a starting point. The choose one of the following 6 as a starting point and mold it to fit your policies.
--->

# <a name="align-your-cloud-governance-design-guide-with-corporate-policy"></a>クラウド ガバナンス設計ガイドと企業ポリシーの合致

[特定されたリスク](./business-risk.md)に基づいて[クラウド ポリシーを定義](./policy-definition.md)したら、IT スタッフと開発者が参照する、これらのポリシーに合致した実践的なガイダンスを作成する必要があります。 クラウド ガバナンス設計ガイドの素案を作成することで、[5 つのガバナンス規範](../governance-disciplines.md)それぞれに対して作成されるポリシー ステートメントに基づいた構造上、技術上、プロセス上の選択肢を指定することができます。

クラウド ガバナンス設計ガイドでは、実際のポリシー要件に最適なクラウド デプロイのコア インフラストラクチャ コンポーネントそれぞれについて、アーキテクチャの選択肢と設計パターンを確立する必要があります。 これらと共に、こうした設計上の各決定事項をサポートするテクノロジ、ツール、およびプロセスの概要説明を含める必要があります。

リスク分析とポリシー ステートメントは、ある程度クラウド プラットフォームに依存しない場合もありますが、設計ガイドでは、IT および開発チームがクラウドベースのワークロードの作成と開発時に使用できるプラットフォーム固有の実装の詳細を提供する必要があります。 設計上の決定を下し、ガイダンスを提供するときには、選択したプラットフォームのアーキテクチャ、ツール、および、機能に焦点を合わせます。

クラウド設計ガイドでは、各インフラストラクチャ コンポーネントに関連する技術的詳細の一部を考慮に入れる必要がありますが、ガイドは広範囲にわたる技術ドキュメントや仕様書となるものではありません。 ガイドでは、ポリシー ステートメントを取り上げ、スタッフが容易に理解して参照できる形式で、設計の決定内容が明確に書かれていることを確認します。

<!-- markdownlint-enable MD033 -->

## <a name="using-the-actionable-governance-guides"></a>実践的なガバナンス ガイドの使用

クラウド導入のために Azure プラットフォームを使用する予定がある場合は、クラウド導入フレームワークで、クラウド導入フレームワーク ガバナンス モデルの増分型アプローチについて説明している[実践的なガバナンス ガイド](../guides/index.md)を提供しています。 ストーリー型のこれらのガイドでは、ビジネス リスク、許容の要件、ポリシー ステートメントを含む一般的な導入シナリオが取り上げられていています。これらが、ガバナンスにおける "実用最小限の製品" (MVP) を作り出すことになります。 これらのガイドは、実世界で顧客が経験する Azure でのクラウド導入プロセスの全体を表しています。

どのクラウド導入にも固有の目標、優先度、課題がありますが、これらのサンプルは、実際のポリシーをガイダンスに移し替えるための適切なテンプレートとなるはずです。 開始点として、実際の状況に最も近いシナリオを選択し、ポリシーの具体的なニーズに合うように変更します。

## <a name="next-steps"></a>次の手順

設計ガイダンスを用意したので、ポリシー コンプライアンスを確保するためのポリシー準拠プロセスを確立します。

> [!div class="nextstepaction"]
> [ポリシー順守プロセスを確立する](./processes.md)
