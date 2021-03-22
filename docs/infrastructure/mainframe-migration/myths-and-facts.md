---
title: メインフレームの移行に関する通説と事実
description: メインフレームに関する通説と事実を区別し、Azure に最も適しているメインフレーム ワークロードを評価する方法について説明します。
author: njray
ms.author: brblanch
ms.date: 12/27/2018
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: migrate
ms.custom: think-tank
ms.openlocfilehash: 5c0a226143f62a08046382d59a58d4b34ca8282c
ms.sourcegitcommit: 9e4bc0e233a24642853f5e8acbeb9746b2444024
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102115035"
---
<!-- cSpell:ignore chargebacks IPLs -->

# <a name="mainframe-myths-and-facts"></a>メインフレームに関する通説と事実

メインフレームはコンピューティングの歴史において際立っており、非常に特殊なワークロードに対して価値があることに変わりはありません。 メインフレームは実績のあるプラットフォームであり、長い歴史を持つ運用手順によって信頼性が高く堅牢な環境が構成されているということに、異論はほとんどありません。 ソフトウェアは使用状況に基づいて実行され、MIPS (百万命令/秒) の単位で測定されて、配賦のために広範な使用状況レポートを利用できます。

メインフレームの信頼性、可用性、処理能力はほとんど神話の域に達しています。 Azure に最も適しているメインフレーム ワークロードを評価するには、最初に事実から通説を区別する必要があります。

## <a name="myth-mainframes-never-go-down-and-have-a-minimum-of-five-9s-of-availability"></a>通説:メインフレームはダウンすることがなく、最低でもファイブ ナインの可用性である

メインフレームのハードウェアとオペレーティング システムは、信頼性が高く安定性しているものと見なされます。 しかし、実際には、メンテナンスと再起動のためのダウンタイムをスケジュールする必要があります (イニシャル プログラム ロード (IPL) と呼ばれます)。 これらのタスクを考慮すると、多くの場合、メインフレーム ソリューションの可用性はツー ナインまたはスリー ナインに近く、これはハイエンドの Intel ベース サーバーと同等です。

また、メインフレームは他のサーバーと同じように災害に対して脆弱であり、この種の障害に対処するために無停電電源 (UPS) システムを必要とします。

## <a name="myth-mainframes-have-limitless-scalability"></a>通説:メインフレームのスケーラビリティは無限である

メインフレームのスケーラビリティは、顧客情報管理システム (CICS) などのシステム ソフトウェアのキャパシティ、およびメインフレームのエンジンとストレージの新しいインスタンスのキャパシティに依存します。 メインフレームを使用する一部の大企業では、パフォーマンスのために CICS をカスタマイズしており、そうしないと、利用可能な最大のメインフレームの機能を超えています。

## <a name="myth-intel-based-servers-are-not-as-powerful-as-mainframes"></a>通説:Intel ベースのサーバーはメインフレームほど強力ではない

新しいコア密度の Intel ベースのシステムのコンピューティング キャパシティはメインフレームに匹敵します。

## <a name="myth-the-cloud-cant-accommodate-mission-critical-applications-for-large-companies-such-as-financial-institutions"></a>通説:クラウドでは、金融機関などの大企業のミッション クリティカルなアプリケーションに対応できない

クラウド ソリューションでは要求が満たされない例がまれにありますが、通常はアプリケーションのアルゴリズムを分散できないためです。 これらの少数の例は例外であって、ルールではありません。

## <a name="summary"></a>まとめ

比較すると、Azure では、メインフレームの同等の機能を格段に低いコストで提供できる、代替プラットフォームが提供されます。 さらに、クラウドのサブスクリプションに基づく使用量によるコスト モデルの総保有コスト (TCO) は、メインフレーム コンピューターよりはるかに低コストです。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [メインフレームから Azure に切り替える](./migration-strategies.md)
