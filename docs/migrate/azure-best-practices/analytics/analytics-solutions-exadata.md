---
title: Oracle データ ウェアハウス用の Azure Synapse Analytics の移行
description: Azure 用のクラウド導入フレームワークを使用して、Azure Synapse Analytics に Oracle データ ウェアハウス スキーマを移行する方法について説明します。
author: v-hanki
ms.author: brblanch
ms.date: 07/14/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: migrate
ms.openlocfilehash: 0c89967ac5cb815ad771e6a9ee70cad2f97056db
ms.sourcegitcommit: 011525720bd9e2d9bcf03a76f371c4fc68092c45
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88570829"
---
<!-- cSpell:ignore Exadata SSMA -->

# <a name="azure-synapse-analytics-solutions-and-migration-for-an-oracle-data-warehouse"></a>Azure Synapse Analytics ソリューションと Oracle データ ウェアハウスの移行

Oracle データウェア ハウス スキーマは、いくつかの点で Azure Synapse Analytics とは異なります。 相違点として、Azure Synapse でサポートされていないデータベース、データ型、およびさまざまな Oracle データベース オブジェクトの種類などがあります。

他のデータベース管理システムと同様に、Oracle データ ウェアハウスを Azure Synapse に移行すると、Oracle には複数の個別のデータベースが存在し、Azure Synapse には 1 つのデータベースしか存在しないことがわかります。 Oracle データ ウェアハウスのステージング データベース、運用データベース、データ マート データベースのテーブルとビューを Azure Synapse に移行するために、新しい名前付け規則 (Oracle スキーマとテーブル名の連結など) を使用することが必要な場合があります。

Azure Synapse では、いくつかの Oracle データベース オブジェクトがサポートされていません。 Azure Synapse でサポートされていないデータベース オブジェクトとしては、Oracle ビットマップ インデックス、関数ベースのインデックス、ドメイン インデックス、Oracle のクラスター化テーブル、行レベルのトリガー、ユーザー定義のデータ型、PL/SQL ストアド プロシージャなどがあります。 これらのオブジェクトを特定するには、さまざまな Oracle システム カタログのテーブルとビューに対してクエリを実行します。 場合によっては、回避策を使用できます。 たとえば、Azure Synapse でパーティション分割または他のインデックスの種類を使用して、サポートされていない Oracle のインデックスの種類を回避することができます。 Oracle のクラスター化されたテーブルではなく、具体化されたビューを使用できる場合があります。また、SQL Server Migration Assistant (SSMA) for Oracle などの移行ツールを使用して、少なくとも一部の PL/SQL を変換することができます。

Oracle データ ウェアハウス スキーマを移行する場合は、列上のデータ型の違いも考慮する必要があります。 Azure Synapse のデータ型にマッピングされないデータ型を持つ Oracle データ ウェアハウスおよびデータ マート スキーマ内の列を見つけるには、Oracle カタログに対してクエリを実行します。 これらのインスタンスのいくつかについては、回避策を使用できます。

ご利用のスキーマのパフォーマンスを移行後も維持または向上させるには、Oracle インデックス作成など、現在使用しているパフォーマンス メカニズムを検討してください。 たとえば、Oracle クエリで頻繁に使用されるビットマップ インデックスは、Azure Synapse に移行されたスキーマ内で非クラスター化インデックスを作成することが有益であることを示している可能性があります。

Azure Synapse でお勧めする方法には、データ分布を使用して、同じ処理ノードに結合するデータを同一場所に配置することが含まれます。 Azure Synapse でお勧めするもう 1 つの方法は、結合する列のデータ型を確実に同じものとすることです。 同一の結合列を使用すれば、照合のためにデータを変換する必要性が少なくなるため、結合処理が最適化されます。 Azure Synapse では、多くの場合、すべての Oracle インデックスを移行する必要はありません。他の機能によってハイ パフォーマンスが実現されるからです。 代わりに、並列クエリ処理およびインメモリ データに加えて、I/O を削減する結果セット キャッシュおよびデータ分散オプションを利用できます。

SSMA for Oracle は、Oracle データ ウェアハウスまたはデータ マートを Azure Synapse に移行するために役立ちます。 SSMA は、既存の Oracle 環境からのテーブル、ビュー、データの移行のプロセスを自動化するように設計されています。 他の機能の中でも、SSMA ではターゲットの Azure Synapse テーブル用のインデックスの種類とデータ分散が推奨され、移行時にデータ型のマッピングが適用されます。 SSMA は、非常に大量のデータに対して最も効率的な方法ではありませんが、小規模なテーブルには適しています。
