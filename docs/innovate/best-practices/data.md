---
title: クラウド イノベーション:Azure でデータを民主化するためのツール
titleSuffix: Microsoft Cloud Adoption Framework for Azure
description: Azure でデータを民主化するためのツール
author: BrianBlanchard
ms.author: brblanch
ms.date: 10/17/2019
ms.topic: guide
ms.service: cloud-adoption-framework
ms.subservice: innovate
ms.openlocfilehash: b9c868efb6f35472552778b583b638da59069edd
ms.sourcegitcommit: f3371811a36e12533ecbc3aa936e2a68e0cee25f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/21/2019
ms.locfileid: "72683403"
---
# <a name="tools-to-democratize-data-in-azure"></a>Azure でデータを民主化するためのツール

[データの民主化](../considerations/data.md)に関する理論の記事で説明したように、多くのイノベーションは技術的な投資を最小限に抑えて実現することができます。 生データをほとんど必要としない主要なイノベーションの例は多数あります。 データの民主化とは、顧客がデータを使用して既存の知識を活用するために必要なだけの投資を行うことです。 データから始めると、より幅広く、より費用のかかるデジタル発明に展開する前に、仮説を簡単にテストできます。 より多くの仮説が改良されて、発明が大規模に導入されるにつれて、以下のプロセスのそれぞれが、イノベーションの運用サポートの準備に役立つようになります。

![データを民主化するためのクラウド導入フレームワークのアプローチ](../../_images/innovate/democratize-data.png)

## <a name="alignment-to-the-methodology"></a>方法論に足並みを揃える

この種類のデジタル発明は、上の図にも示されているように、以下のプロセスの各段階で加速できます。 デジタル発明を加速するための技術ガイダンスについては、左側の目次をご覧ください。 これらの記事は同じフェーズにグループ化されており、ガイダンスは全体的な手法に沿っています。

- **データの共有:** データを民主化するための最初のステップは、データを公開して共有することです。
- **データの管理:** 共有する前に、機密データがセキュリティ保護、追跡、および管理されていることを確認します。
- **データの一元管理:** 場合によっては、データの共有とガバナンスのための一元化されたプラットフォームを提供する必要があります。
- **データの収集:** 移行、統合、インジェスト、および仮想化では、それぞれ、一元化、管理、および共有のために既存のデータを収集することができます。

クラウド導入チームは、すべてのイテレーションでアーキテクチャよりも顧客のニーズに重点を置くために必要なレベルだけ、スタックの詳細を把握することをお勧めします。 顧客のニーズに応じて技術的スパイクを遅らせることで、仮説の検証が加速されます。 そのため、すべてのガイダンスは、顧客への最大の影響から最大の技術的影響までの、上記の 4 つのプロセスに対応しています。 それぞれに、Azure で[顧客の共感を構築する](../considerations/build.md)機能を高めるために役立つさまざまな方法に関するガイダンスがあります。

## <a name="toolchain"></a>ツールチェーン

Azure では、上記の各フェーズでデジタル発明を加速するために、一般的に次のツールを活用します。Power BI、Azure Data Catalog、Azure SQL Data Warehouse、Cosmos DB、PostgreSQL、MySQL、MariaDB、PostgreSQL Hyperscale、Azure Data Lake、Azure Data Migration Service、Azure SQL Database (マネージド インスタンスあり、またはなし)、Azure Data Factory、Azure Stream Analytics、SQL Server Integration Services、Azure Stack、Azure SQL stretch データベース、StorSimple、Azure Files、File Sync、Polybase。

発明が大規模な導入に近づくにつれて、各ソリューションの側面に改良と技術的な成熟が必要になっていきます。 そのような場合は、これらのサービスの多くが必要になる可能性があります。 ここでは、左側の目次を使用して、仮説のテストに必要なプロセスに関連する Azure ツールのガイダンスを見つけてください。

## <a name="get-started"></a>作業開始

左側の目次は、このツールチェーンの各ツールの使用を開始するためのさまざまな記事の概要を示しています。

> [!NOTE]
> 一部のリンクは、このフレームワークの範囲を超えるために、クラウド導入フレームワークの範囲外になるものもあります。
