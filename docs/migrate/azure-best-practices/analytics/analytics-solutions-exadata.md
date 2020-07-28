---
title: Exadata の分析ソリューション
description: Azure のクラウド導入フレームワークを使用して、Exadata を使用した分析ソリューションについて学習します。
author: v-hanki
ms.author: brblanch
ms.date: 07/14/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: migrate
ms.openlocfilehash: 9f2812dcdc09e0ff0a5403003384faa997b185ca
ms.sourcegitcommit: 9163a60a28ffce78ceb5dc8dc4fa1b83d7f56e6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2020
ms.locfileid: "86452666"
---
<!-- cSpell:ignore Exadata SSMA -->

# <a name="analytics-solutions-for-exadata"></a>Exadata の分析ソリューション

## <a name="migrating-an-oracle-data-warehouse-schema-to-azure-synapse-analytics"></a>Oracle データ ウェアハウス スキーマを Azure Synapse Analytics に移行する

Oracle データ ウェアハウスは、スキーマに関して Azure Synapse Analytics と異なる点がいくつかあります。 これには、Azure Synapse Analytics でサポートされていないデータベース、データ型、さまざまな Oracle データベース オブジェクトの種類などがあります。

他のデータベース管理システムと同様に、Oracle データ ウェアハウスを Azure Synapse に移行すると、Oracle には複数の個別のデータベースが存在し、Azure Synapse には 1 つのデータベースしか存在しないことがわかります。 そのため、必要に応じて、新しい名前付け規則 (Oracle スキーマとテーブル名の連結など) を使用して、Oracle データ ウェアハウスのステージング データベース、運用データベース、およびデータ マート データベースのテーブルとビューを Azure Synapse に移行します。

また、サポートされていない Oracle データベース オブジェクトがいくつかあります。 たとえば、Oracle ビットマップ インデックス、関数ベースのインデックス、ドメイン インデックスはサポートされていません。 Oracle のクラスター化テーブル、行レベルのトリガー、ユーザー定義のデータ型、および PL/SQL ストアド プロシージャについても同様です。 これらのオブジェクトを特定するには、さまざまな Oracle システム カタログのテーブルとビューに対してクエリを実行します。 いくつかのケースで回避策が考えられます。 たとえば、Azure Synapse で他のインデックスの種類またはパーティションを使用して、Oracle でサポートされていないインデックスの種類を回避することができます。 具体化されたビューを Oracle のクラスター化されたテーブルの代わりに使用できる場合があります。また、SQL Server Migration Assistant for Oracle などの移行ツールを使用して、少なくとも一部の PL/SQL を変換することができます。

列にも、スキーマを移行するときに考慮する必要があるデータ型の違いがあります。 Oracle カタログに対してクエリを実行することで、Azure Synapse のデータ型にマッピングされないデータ型を持つ Oracle データ ウェアハウスおよびデータ マート スキーマの列を見つけることができます。 このようなインスタンスの一部には回避策があります。

移行後のスキーマのパフォーマンスの維持または改善に関しては、Oracle のインデックス作成など、現在どのようなパフォーマンス メカニズムが採用されているかを確認してください。 たとえば、Oracle のクエリで頻繁に使用されるビットマップ インデックスは、非クラスター化インデックスを Azure Synapse の移行されたスキーマ内に作成する必要があることを示している場合があります。 Azure Synapse の適切なプラクティスとして、マッチングのためにデータを変換する必要性を減らして結合処理を最適化するために、データ処理を使用して結合するデータを同じ処理ノードに配置すること、結合する列のデータ型を必ず同じにすることがあります。 Azure Synapse を使用すると、多くの場合、すべての Oracle インデックスを移行する必要はありません。これは、他の機能を使用してハイ パフォーマンスを実現しているためです。 代わりに、I/O を削減する並列クエリ処理、インメモリ データ、結果セット キャッシュ、およびデータ分散というすべてのオプションを利用できます。

Oracle データ ウェアハウスまたはデータ マートを Azure Synapse に移行するために役立つツールがいくつかあります。 このようなツールとして、SQL Server Migration Assistant (SSMA) for Oracle があります。これは、既存の Oracle 環境からのテーブル、ビュー、およびデータの移行を自動化するように設計されています。 他の機能の中でも、SSMA ではターゲットの Azure Synapse テーブル用のインデックスの種類とデータ分散が推奨され、移行時にデータ型のマッピングが適用されます。 SSMA は、非常に大量のデータに対して最も効率的な方法ではありませんが、小規模なテーブルには適しています。

## <a name="next-steps"></a>次のステップ

<!-- TODO: Add actionable next step -->
