---
title: クラウド ガバナンスの方法論
titleSuffix: Microsoft Cloud Adoption Framework for Azure
description: クラウド導入フレームワークでクラウド ガバナンスを推進する手法の基本的な理解を確立します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 07/04/2019
ms.topic: article
ms.service: cloud-adoption-framework
ms.subservice: organize
ms.custom: organize
ms.openlocfilehash: cc4567495d60f76be760d532dc16a66274834396
ms.sourcegitcommit: 443c28f3afeedfbfe8b9980875a54afdbebd83a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/16/2019
ms.locfileid: "71031321"
---
# <a name="cloud-governance-methodology"></a>クラウド ガバナンスの方法論

クラウドの導入はプロセスであってターゲットではありません。 その過程では、明確なマイルストーンと具体的なビジネス上の利点があります。 ただし、クラウド導入の最終的な状態は、企業が体験を始める時点では不明です。 クラウド ガバナンスでは、企業が体験を通して安全な位置を維持できるガードレールが作成されます。

クラウド導入フレームワークには、実際の顧客の体験に基づく、架空の企業の経験を説明しているガバナンス ガイドが用意されています。 各ガイドは、顧客によるクラウド導入のガバナンスの面に沿って進みます。

## <a name="envision-an-end-state"></a>最終状態の構想

目的地のない旅は単なる放浪です。 最初の一歩を踏み出す前に、終了状態の大まかなビジョンを確立することが重要です。 次のインフォグラフィックでは、終了状態の枠組みを提供します。 それは作業の開始点ではなく、可能性のある目標を示します。

![クラウド導入フレームワークのガバナンス モデルのインフォグラフィック](../_images/operational-transformation-govern-highres.png)

クラウド導入フレームワークのガバナンス モデルでは、体験の過程での重要な主要領域が識別されます。 各領域は、企業がクラウド サービスを導入することで対処する必要のある異なる種類のリスクに関係しています。 このフレームワーク内では、ガバナンス ガイドによってクラウド ガバナンス チームに必要なアクションが示されます。 その過程で、クラウド導入フレームワークのガバナンス モデルの各原則がさらに詳しく説明されます。 だいたい次のような内容です。

**企業ポリシー:** 企業のポリシーはクラウド ガバナンスを主導します。 ガバナンス ガイドでは、会社のポリシーの特定の側面に焦点が当てられます。

- **ビジネス リスク:** 企業のリスクを識別して理解します。
- **ポリシーとコンプライアンス:** コンプライアンス要件をサポートするポリシー ステートメントにリスクを変換します。
- **プロセス:** 示されたポリシーに準拠していることを確認します。

**クラウド ガバナンスの 5 つの規範:** これらの規範によって企業のポリシーがサポートされます。 各規範は、潜在的な落とし穴から企業を保護します。

- Cost Management
- セキュリティ ベースライン
- リソースの整合性
- ID ベースライン
- デプロイ高速化

基本的に、企業のポリシーは潜在的な問題を検出する早期警告システムとして機能します。 規範は、企業がリスクを管理してガードレールを作成するのに役立ちます。

## <a name="grow-to-the-end-state"></a>最終状態に成長する

ガバナンス要件はクラウド導入の体験を通して変化するため、ガバナンスに対する異なるアプローチが必要です。 企業は、"*最初の一歩を踏み出す前に*" 小さいチームがすべてのハイウェイのガードレールとロードマップを作成するまで待つことはできません。 より迅速かつ円滑なビジネスの結果が期待されます。 IT ガバナンスも迅速に移動し、クラウド導入の過程でビジネスの需要とペースを合わせて関連性を保ち、"シャドウ IT" を避ける必要があります。

"**増分ガバナンス**" アプローチがこれらの特性を支援します。 増分ガバナンスでは、企業のポリシー、プロセス、およびツールの小さなセットに依存して、導入とガバナンスの基盤が確立されます。 その基盤は、**実用最小限の製品 (MVP)** と呼ばれます。 MVP により、管理チームは、導入のライフサイクル全体の実装にガバナンスをすばやく組み込むことができます。 MVP は、クラウド導入プロセスのどの時点でも確立できます。 ただし、可能な限り早い段階で MVP を導入することをお勧めします。

リスクの変化に迅速に対応する機能により、クラウド ガバナンス チームは新しい方法で作業に取り組むことができます。 クラウド ガバナンス チームは、偵察者としてクラウド戦略チームに参加することができ、クラウド導入チームより先に行動し、進むべき道を示し、導入計画に関連するリスクを管理するためのガードレールをすばやく確立します。 これらの Just-In-Time ガバナンス レイヤーは**ガバナンスの繰り返し**として知られます。 このアプローチでは、ガバナンス戦略はクラウド導入チームより一歩早く成長します。

次の図では、単純なガバナンス MVP とガバナンスの 3 つの繰り返しを示します。 繰り返しの過程で、新しいリスクを修復するために追加の企業ポリシーが定義されます。 デプロイ高速化の規範が、各デプロイのこれらの変更に適用されます。

![段階的なガバナンス改善の例](../_images/govern/incremental-governance-example.png)

> [!NOTE]
> ガバナンスは、セキュリティ、ネットワーク、ID、財務、DevOps、運用などの主要な機能に代わるものではありません。 その過程では、各機能のメンバーとの対話およびメンバーへの依存関係があります。 決定とアクションが速く行われるように、これらのメンバーをクラウド ガバナンス チームに含める必要があります。

## <a name="next-steps"></a>次の手順

クラウド導入フレームワークの[ガバナンス ベンチマーク ツール](https://cafbaseline.com)を使用して、変革の過程を評価し、フレームワーク内で定義されている 6 つの重要な領域における組織内のギャップを特定できます。

> [!div class="nextstepaction"]
> [変革の過程の評価](./benchmark.md)