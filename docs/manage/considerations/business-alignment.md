---
title: ビジネス アラインメント - クラウドの管理と運用
titleSuffix: Microsoft Cloud Adoption Framework for Azure
description: ビジネス アラインメント - クラウドの管理と運用
author: BrianBlanchard
ms.author: brblanch
ms.date: 10/17/2019
ms.topic: article
ms.service: cloud-adoption-framework
ms.subservice: operate
ms.openlocfilehash: 9c6884d57b9238f58921b14ec3ddbbe3623506af
ms.sourcegitcommit: 35c162d2d09ec1c4a57d3d57a5db1d56ee883806
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/17/2019
ms.locfileid: "72558589"
---
# <a name="create-business-alignment-in-cloud-management"></a>クラウド管理でのビジネス アラインメントの構築

オンプレミス環境では、IT 資産 (アプリケーション、仮想マシン、VM ホスト、ディスク、サーバー、デバイス、およびデータ ソース) は、ワークロードの運用をサポートするために IT 部門によって管理されます。 IT の用語では、ワークロードとは、特定のビジネス運用をサポートする IT 資産のコレクションのことです。 オンプレミス環境では、IT 管理は、サポート ビジネス運用の中断を最小限に抑えるために、これらの IT 資産の中断を最小限に抑えるプロセス設計を提供します。 クラウドに移行する場合は、管理と運用が少し変化し、より緊密なビジネス アラインメントを開発する機会が生まれます。

## <a name="business-vernacular"></a>ビジネス用語

ビジネス アラインメントを作成する最初のステップは、用語のアラインメントです。 ほとんどのエンジニアリング業と同様に、IT 管理には、多数の専門用語や高度な技術用語が伴います。 これらの用語は、ビジネスの利害関係者の混乱を招き、管理サービスをビジネス価値にマップすることを困難にする可能性があります。

さいわいなことに、クラウド導入戦略とクラウド導入計画を開発するプロセスによって、用語を再マッピングするのに理想的な状況が作り出されます。 また、ビジネスと連携して、運用管理へのコミットメントを再考する絶好の機会も生み出されます。 次の一連の記事では、ビジネスの利害関係者との会話を改善できる 3 つの特定の用語を含むこの新しいアプローチについて紹介します。

- **[重要度](./criticality.md)** : ワークロードをビジネス プロセスにマップします。 投資に重点を置くために重要度を順位付けします。
- **[影響](./impact.md)** : 潜在的な停止の影響を理解して、クラウド管理の投資収益率を評価するのに役立てます。
- **[コミットメント](./commitment.md)** : **ビジネスとの**契約を作成および文書化することによって、真のパートナーシップを開発します。

> [!NOTE]
> これらの各用語の下には、SLA、RTO、RPO などの従来の IT 用語があります。 ビジネス用語と IT 用語のマッピングについては、コミットメントに関する記事に詳細に説明されています。

## <a name="ops-management-planning-workbook"></a>運用管理計画のブック

この会話の結果としての決定を記録するのに役立つ[運用管理ブック](https://raw.githubusercontent.com/microsoft/CloudAdoptionFramework/master/manage/opsmanagementworkbook.xlsx)を GitHub リポジトリで入手できます。 このブックでは、SLA またはコストの計算は行われません。 このブックは、これらのメジャーをキャプチャし、損失回避の取り組みに対するリターンを予測するためのガイドとしてのみ機能します。

または、ソリューションが既にクラウドにデプロイされている場合は、Azure でこれらの同じワークロードと関連する資産を直接タグ付けできます。

## <a name="next-steps"></a>次の手順

[ワークロードの重要度](./criticality.md)を定義して、ビジネス アラインメントの構築を開始します。

> [!div class="nextstepaction"]
> [ワークロードの重要度を定義する](./criticality.md)
