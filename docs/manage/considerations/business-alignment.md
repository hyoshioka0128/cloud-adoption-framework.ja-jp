---
title: クラウド管理でのビジネス アラインメント
description: Azure 向けのクラウド導入フレームワークを使用して、より適切にクラウド運用を管理し、より緊密なビジネス アラインメントを構築する方法を学習します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 10/17/2019
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: operate
ms.custom: internal
ms.openlocfilehash: 0d8eda2b8f65a305cbd23ba02a80466ced5e657e
ms.sourcegitcommit: 9e4bc0e233a24642853f5e8acbeb9746b2444024
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102113624"
---
# <a name="create-business-alignment-in-cloud-management"></a>クラウド管理でのビジネス アラインメントの構築

オンプレミス環境では、IT 資産 (アプリケーション、仮想マシン、VM ホスト、ディスク、サーバー、デバイス、およびデータ ソース) は、ワークロードの運用をサポートするために IT 部門によって管理されます。 IT の用語では、ワークロードとは、特定のビジネス運用をサポートする IT 資産のコレクションのことです。 ビジネス運用をサポートするために、IT 管理は、こうした資産の中断を最小限に抑えるように設計されたプロセスを用意します。 組織がクラウドに移行すると、管理と運用が少し変化し、より緊密なビジネス アラインメントを構築する機会が生まれます。

## <a name="business-vernacular"></a>ビジネス用語

ビジネス アラインメントを構築する最初のステップは、用語のアラインメントを確保することです。 IT 管理は、ほとんどのエンジニアリングの専門家と同様に、専門用語や高度な技術用語のコレクションを蓄えています。 こうした用語は、ビジネスの利害関係者の混乱を招き、管理サービスをビジネス価値にマップすることを困難にする可能性があります。

幸いなことに、クラウド導入戦略とクラウド導入計画を策定するプロセスは、このような用語を再マッピングする理想的な機会になります。 また、このプロセスにより、ビジネスと連携して、運用管理へのコミットメントを再考する絶好の機会も生まれます。 次の一連の記事では、ビジネスの利害関係者との会話を改善できる 3 つの特定の用語を含むこの新しいアプローチについて紹介します。

- **[重要性](./criticality.md):** ワークロードをビジネス プロセスにマップします。 投資に重点を置くために重要度を順位付けします。
- **[影響](./impact.md):** 潜在的な停止の影響を理解すると、クラウド管理の投資収益率を評価するために役立ちます。
- **[コミットメント](./commitment.md):** ビジネスとの契約を作成および文書化することによって、真のパートナーシップを構築します。

> [!NOTE]
> これらの用語の基礎となるのは、SLA、RTO、RPO などの従来の IT 用語です。 特定のビジネス用語と IT 用語のマッピングの詳細については、「[クラウド管理におけるビジネス コミットメント](./commitment.md)」をご覧ください。

## <a name="operations-management-workbook"></a>運用管理ブック

用語のアラインメントに関するこの会話が基になった決定内容を記録する助けになるように、GitHub サイトで[運用管理ブック](https://raw.githubusercontent.com/Microsoft/CloudAdoptionFramework/master/manage/opsmanagementworkbook.xlsx)が提供されています。 このブックでは、SLA またはコストの計算は行われません。 そのような手段を把握し、損失回避の取り組みに対する結果を予測するためにのみ利用できます。

または、ソリューションが既にクラウドにデプロイされている場合は、Azure でこれらの同じワークロードと関連する資産を直接タグ付けできます。

## <a name="next-steps"></a>次のステップ

[ワークロードの重要度](./criticality.md)を定義して、ビジネス アラインメントの構築を開始します。

> [!div class="nextstepaction"]
> [ワークロードの重要度を定義する](./criticality.md)
