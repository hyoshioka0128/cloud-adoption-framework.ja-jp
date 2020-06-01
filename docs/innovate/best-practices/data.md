---
title: データを民主化するためのイノベーション ツール
description: より広範で、よりコストのかかるデジタル発明に展開する前に、仮説を迅速にテストするのに役立つ Azure Data Catalog とその他のサービスについて説明します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 10/17/2019
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: innovate
ms.openlocfilehash: 9e54205060604e24974d853e666962130b756ada
ms.sourcegitcommit: 070e6a60f05519705828fcc9c5770c3f9f986de5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/24/2020
ms.locfileid: "83814921"
---
# <a name="tools-to-democratize-data-in-azure"></a>Azure でデータを民主化するためのツール

[データの民主化](../considerations/data.md)に関する概念の記事で説明したように、多くのイノベーションは技術的な投資がほとんどなくても実現することができます。 多くの主要なイノベーションでは、生データ以外に必要なものはほとんどありません。 データの民主化とは、データを使用して既存の知識を活用する顧客との関係を築くために必要なリソースだけに投資を行うことです。

データから始めると、より幅広く、より費用のかかるデジタル発明に展開する前に、仮説を簡単にテストできます。 より多くの仮説が改良され、発明が大規模に導入されるにつれて、以下のプロセスがイノベーションの運用サポートの準備に役立つようになります。

![データを民主化するためのクラウド導入フレームワークのアプローチ](../../_images/innovate/democratize-data.png)

## <a name="alignment-to-the-methodology"></a>方法論に足並みを揃える

この種類のデジタル発明は、上の図にも示されているように、以下のプロセスの各フェーズで加速できます。 デジタル発明を加速するための技術的なガイダンスは、このページの左側の目次に一覧表示されています。 これらの記事は、ガイダンスを全体的な方法論に合わせるために、フェーズによってグループ化されています。

- **データの共有:** データを民主化するための最初のステップは、データを公開して共有することです。
- **データの管理:** 共有する前に、機密データがセキュリティ保護、追跡、および管理されていることを確認します。
- **データの一元管理:** 場合によっては、データの共有とガバナンスのための一元化されたプラットフォームを提供する必要があります。
- **データの収集:** 移行、統合、インジェスト、および仮想化では、それぞれ、一元化、管理、および共有のために既存のデータを収集することができます。

すべてのイテレーションで、クラウド導入チームは、アーキテクチャよりも顧客のニーズに重点を置くために必要なレベルだけ、スタックの詳細を把握する必要があります。 顧客のニーズに応じて技術的スパイクを遅らせることで、仮説の検証が加速されます。

すべてのガイダンスは、上記の 4 つのプロセスに対応しています。 ガイダンスは、顧客への最大の影響から最大の技術的影響まで多岐に及びます。 各プロセスに、Azure を使用して[顧客の共感を構築する](../considerations/build.md)能力を高められる可能性があるさまざまな方法に関するガイダンスがあります。

## <a name="toolchain"></a>ツールチェーン

Azure では、上記のフェーズでデジタル発明を加速するために、一般的に次のツールが使用されます。

- [Power BI](https://docs.microsoft.com/power-bi)
- [Azure Data Catalog](https://docs.microsoft.com/azure/data-catalog)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db)
- [Azure Database for PostgreSQL](https://docs.microsoft.com/azure/postgresql)
- [Azure Database for MySQL](https://docs.microsoft.com/azure/mysql)
- [Azure Database for MariaDB](https://docs.microsoft.com/azure/mariadb)
- [Azure Database for PostgreSQL Hyperscale](https://docs.microsoft.com/azure/postgresql/concepts-hyperscale-nodes)
- [Azure Data Lake](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction)
- [Azure Database Migration Service](https://docs.microsoft.com/azure/dms)
- [Azure SQL Database (Azure SQL Managed Instance あり、またはなし)](https://docs.microsoft.com/azure/sql-database)
- [Azure Data Factory](https://docs.microsoft.com/azure/data-factory)
- [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics)
- [SQL Server Integration Services](https://docs.microsoft.com/sql/integration-services)
- [Azure Stack](https://docs.microsoft.com/azure-stack)
- [SQL Server Stretch Database](https://docs.microsoft.com/sql/sql-server/stretch-database)
- [Microsoft Azure StorSimple](https://docs.microsoft.com/azure/storsimple)
- [Azure Files](https://docs.microsoft.com/azure/storage/files)
- [Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning)
- [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase)

発明が大規模な導入に近づくにつれて、各ソリューションの側面に改良と技術的な成熟が必要です。 そのような場合は、これらのサービスのより多くが必要になる可能性があります。 仮説のテスト プロセスに関連する Azure ツールのガイダンスについては、このページの左側の目次を使用してください。

## <a name="get-started"></a>はじめに

このページの左側の目次に、さまざまな記事がまとめられています。 これらの記事は、このツールチェーン内の各ツールの使用を開始するのに役立ちます。

> [!NOTE]
> 一部のリンクは、このフレームワークの範囲を超えるために、クラウド導入フレームワークの範囲外になるものもあります。
