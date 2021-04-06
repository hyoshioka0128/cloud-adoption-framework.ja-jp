---
title: データを民主化するためのイノベーション ツール
description: より広範で、よりコストのかかるデジタル発明に展開する前に仮説を迅速にテストするのに役立つ Azure Data Catalog とその他のデータ収集サービスについて説明します。
author: BrianBlanchard
keywords: データを民主化する, データの民主化, データ収集, イノベーション ツール
ms.author: brblanch
ms.date: 10/17/2019
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: innovate
ms.custom:
- internal
- seo-caf-innovate
ms.openlocfilehash: 79188228341fa131ed2c52aeb87232d19ee45c78
ms.sourcegitcommit: bf73a3f16001cd7c5ef74d9f6de4292f9bfd62e4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/25/2021
ms.locfileid: "105099623"
---
# <a name="innovation-tools-to-democratize-data-in-azure"></a>Azure でデータを民主化するためのイノベーション ツール

[データの民主化](../considerations/data.md)に関する概念の記事で説明したように、多くのデータ収集イノベーションは、技術的な投資をほとんどしないでも実現することができます。 多くの主要なイノベーションでは、生データ以外に必要なものはほとんどありません。 データの民主化とは、顧客を関与させるために必要な最小限のリソースに投資することです。 顧客は、そのデータを使用して、自分たちが既に持っている知識を活用します。

データの民主化から始めると、より幅広く、より費用のかかるデジタル発明に展開する前に、仮説を簡単にテストできます。 より多くの仮説が改良され、発明が大規模に導入されるにつれて、以下のプロセスがイノベーションの運用サポートの準備に役立つようになります。

![データを民主化するためのクラウド導入フレームワーク アプローチを示す図。](../../_images/innovate/democratize-data.png)

## <a name="alignment-to-the-methodology"></a>方法論に足並みを揃える

この種類のデジタル発明は、上の図にも示されているように、以下のプロセスの各フェーズで加速できます。 デジタル発明を加速するための技術的なガイダンスは、このページの左側の目次に一覧表示されています。 これらの記事は、ガイダンスを全体的な方法論に合わせるために、フェーズによってグループ化されています。

- **収集したデータの共有:** データを民主化するための最初のステップは、データを公開して共有することです。
- **データの管理:** 共有する前に、機密データがセキュリティ保護、追跡、および管理されていることを確認します。
- **データの一元管理:** 場合によっては、データの民主化、共有、ガバナンスのための一元化されたプラットフォームを提供する必要があります。
- **データの収集:** 移行、統合、インジェスト、および仮想化では、それぞれ、一元化、管理、および共有のために既存のデータを収集することができます。

すべてのイテレーションで、クラウド導入チームは、アーキテクチャよりも顧客のニーズに重点を置くために必要なレベルだけ、スタックの詳細を把握する必要があります。 顧客のニーズに応じて技術的スパイクを遅らせることで、仮説の検証が加速されます。

すべてのガイダンスは、上記の 4 つのプロセスに対応しています。 ガイダンスは、顧客への最大の影響から最大の技術的影響まで多岐に及びます。 各プロセスに、Azure を使用して[顧客の共感を構築する](../considerations/build.md)能力を高められる可能性があるさまざまな方法に関するガイダンスがあります。

## <a name="toolchain"></a>ツールチェーン

Azure では、上記のフェーズでデジタル発明を加速するために、一般的に次のイノベーション ツールが使用されます。

- [Power BI](/power-bi/)
- [Azure Data Catalog](/azure/data-catalog/)
- [Azure Synapse Analytics](/azure/synapse-analytics/)
- [Azure Cosmos DB](/azure/cosmos-db/)
- [Azure Database for PostgreSQL](/azure/postgresql/)
- [Azure Database for MySQL](/azure/mysql/)
- [Azure Database for MariaDB](/azure/mariadb/)
- [Azure Database for PostgreSQL ハイパースケール](/azure/postgresql/concepts-hyperscale-nodes)
- [Azure Data Lake Storage](/azure/storage/blobs/data-lake-storage-introduction)
- [Azure Database Migration Service](/azure/dms/)
- [Azure SQL Database (Azure SQL Managed Instance あり、またはなし)](/azure/azure-sql/)
- [Azure Data Factory](/azure/data-factory/)
- [Azure Stream Analytics](/azure/stream-analytics/)
- [SQL Server Integration Services](/sql/integration-services/sql-server-integration-services)
- [Azure Stack](/azure-stack/)
- [SQL Server Stretch Database](/sql/sql-server/stretch-database/stretch-database)
- [Azure StorSimple](/azure/storsimple/)
- [Azure Files](/azure/storage/files/)
- [Azure File Sync](/azure/storage/files/storage-sync-files-planning)
- [PolyBase](/sql/relational-databases/polybase/polybase-guide)

発明が大規模な導入に近づくにつれて、各ソリューションの側面に改良と技術的な成熟が必要です。 そのような場合は、これらのサービスのより多くが必要になる可能性があります。 仮説のテスト プロセスに関連する Azure ツールのガイダンスについては、このページの左側の目次を使用してください。

## <a name="get-started"></a>はじめに

このページの左側の目次に、さまざまな記事がまとめられています。 これらの記事は、このツールチェーン内の各ツールの使用を開始するのに役立ちます。

> [!NOTE]
> 一部のリンクは、このフレームワークの範囲を超えるために、クラウド導入フレームワークの範囲外になるものもあります。
