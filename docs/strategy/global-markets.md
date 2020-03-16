---
title: グローバル マーケットの決定の影響
description: グローバル マーケットの決定がクラウドへの変革過程に及ぼす影響を、Azure 向けのクラウド導入フレームワークを使用して理解します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 04/04/2019
ms.topic: guide
ms.service: cloud-adoption-framework
ms.subservice: strategy
ms.openlocfilehash: 267408d4634bbed9c6bb9c8884e2af395f5df4d9
ms.sourcegitcommit: 26caeb6b7f4e14df30bf16727d0b1b3d63b9c0c2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2020
ms.locfileid: "78337828"
---
<!-- markdownlint-disable MD026 -->

# <a name="how-will-global-market-decisions-affect-the-transformation-journey"></a>グローバル マーケットの決定が変革過程に与える影響

クラウドでは、グローバル スケールで実行する新しい機会が開かれます。 企業は、新しいデータセンターに大きな投資を必要とせずに市場に資産を展開できるので、グローバルな運用の障壁が大幅に減ります。 残念ながら、これにより技術および法律の点での複雑さも大幅に増します。

## <a name="data-sovereignty"></a>データの主権

多くの地理的地域ではデータ主権規制が確立されています。 これらの規制では、データを格納できる場所、元の国から持ち出すことのできるデータ、その地域の住民に関して収集できるデータが制限されています。 外国でクラウドベースのソリューションを運用する前に、そのクラウド プロバイダーがデータの主権をどのように処理するかを理解する必要があります。 各地域での Azure のアプローチの詳細については、[こちら](https://azure.microsoft.com/global-infrastructure/geographies)をご覧ください。 Azure でのコンプライアンスの詳細については、Microsoft セキュリティ センターで「[Microsoft におけるプライバシー](https://www.microsoft.com/trustcenter/privacy)」をご覧ください。

この記事の残りの部分では、弁護士が外国での活動を審査および承認したものとします。

## <a name="business-units"></a>事業単位

どの事業単位が外国で活動し、どの国が影響を受けるかを理解しておく必要があります。 この情報は、クラウド プロバイダーへのホスティング、請求、展開のソリューションの設計に使用されます。

## <a name="employee-usage-patterns"></a>従業員の使用パターン

ユーザーと同じ国でホストされていないアプリケーションにグローバル ユーザーがアクセスする方法を理解しておくことが重要です。 グローバル ワイド エリア ネットワーク (WAN) ではユーザーは既存のネットワーク契約に基づいてルーティングされます。 従来のオンプレミスの世界では、いくつかの制約によって WAN の設計は制限されています。 これらの制約のため、クラウド導入前に十分に理解していない場合、不適切なユーザー エクスペリエンスにつながります。

クラウド モデルでは、商品インターネットにより多くの新しいオプションが開かれます。 複数の地域に広がる従業員の通信は、クラウド導入チームが、優れたユーザー エクスペリエンスを作成し、**かつ**ネットワーク コスト削減の可能性がある WAN ソリューションを設計するのに役立ちます。

## <a name="external-user-usage-patterns"></a>外部ユーザーの使用パターン

顧客やパートナーなど、外部のユーザーの使用パターンを理解することも同じように重要です。 従業員の使用パターンと同様、外部ユーザーの使用パターンも、クラウド デプロイのパフォーマンスに悪影響を与える可能性があります。 外国に大規模またはミッション クリティカルなユーザー ベースが存在する場合は、全体的なソリューション設計にグローバルな展開戦略を組み込むことが賢明です。

## <a name="next-steps"></a>次のステップ

グローバル マーケットの決定を行って伝達した後、チームはそれらのメトリックに対する[技術標準の確立](../digital-estate/index.md)を始めることができます。
結果は、[変換バックログまたは移行バックログ](..//migrate/migration-considerations/prerequisites/technical-complexity.md)になります。

> [!div class="nextstepaction"]
> [デジタル資産を評価する](../digital-estate/index.md)
