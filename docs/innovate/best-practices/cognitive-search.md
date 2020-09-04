---
title: Azure Cognitive Search とは
description: Azure Cognitive Search (旧称 Azure Search) を使用すると、インデックス作成中に AI プロセスを適用できます。
author: v-hanki
ms.author: janet
ms.date: 07/14/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: innovate
ms.openlocfilehash: a5221713e9e6125aa134e958b2baca2e99dcadec
ms.sourcegitcommit: 07d56209d56ee199dd148dbac59671cbb57880c0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88884540"
---
<!-- cSpell:ignore Lucene -->

<!-- docsTest:casing "JFK Files" -->
<!-- docsTest:ignore "Azure Search" -->

# <a name="what-is-azure-cognitive-search"></a>Azure Cognitive Search とは

Azure Cognitive Search (旧称 Azure Search) は、Web アプリケーション、モバイル アプリケーション、エンタープライズ アプリケーションのプライベートな異種コンテンツに対する豊富な検索機能を追加するための API とツールを開発者に提供するマネージド クラウド ソリューションです。 コードまたはツールでデータ インジェスト (インデックス作成) を呼び出し、インデックスを作成して読み込みます。 必要に応じて、インデックス作成中に AI プロセスを適用するコグニティブ スキルを追加できます。 これにより、検索やその他のシナリオに役立つ新しい情報と構造を追加できます。

サービスの他の面では、アプリケーション コードを使用して、クエリ要求を発行し、応答を処理します。 検索エクスペリエンスは、Azure Cognitive Search の機能を使用してクライアント内で定義します。クエリは、自分で作成および所有し、サービスに保存する永続化されたインデックスに対して実行します。

![Azure Cognitive Search を示す図。](../../_images/ai-cognitive-search.png)

機能は、情報の検索に固有の複雑さを感じさせないシンプルな [REST API](/rest/api/searchservice/) または [.NET SDK](/azure/search/search-howto-dotnet-sdk) を使って公開されます。 API だけでなく、Azure Portal では、管理とコンテンツ管理のサポートおよびプロトタイプの作成とインデックスのクエリのためのツールも提供されます。 サービスはクラウドで実行されるため、インフラストラクチャと可用性は Microsoft によって管理されます。

## <a name="when-to-use-azure-cognitive-search"></a>Azure Cognitive Search を使用する場合

Azure Cognitive Search は、次のアプリケーション シナリオに適しています。

- 異種のコンテンツ タイプのプライベートな単一の検索可能なインデックスへの統合。 クエリは、常に自分で作成してドキュメントと共に読み込むインデックスに対して実行されます。 インデックスは、常に Azure Cognitive Search インスタンスのクラウドに配置されます。 任意のソースまたはプラットフォームの JSON ドキュメントのストリームからインデックスを作成できます。 または、Azure をソースとするコンテンツの場合、インデクサーを使用してデータをインデックスにプルできます。 インデックスの定義と管理および所有権が、Azure Cognitive Search を使用する主な理由です。
- 生のコンテンツは、Azure Blob Storage や Azure Cosmos DB などの Azure データ ソース上の Microsoft Office コンテンツ タイプなど、区分されていない大きなテキスト、画像ファイル、またはアプリケーション ファイルです。 インデックス作成中にコグニティブ スキルを適用して、構造を追加したり、画像ファイルとアプリケーション ファイルから意味を抽出したりすることができます。
- 検索に関連する機能の簡単な実装。 Azure Cognitive Search API を使用すると、クエリの構築、ファセット ナビゲーション、フィルター (地理空間検索を含む)、シノニム マッピング、先行入力クエリ、および関連性チューニングを簡素化できます。 組み込みの機能を使用して、商用 Web 検索エンジンと同様の検索体験に対するエンドユーザーの期待に応えることができます。
- 非構造化テキストのインデックス作成、または画像ファイルからのテキストと情報の抽出。 Azure Cognitive Search の [AI エンリッチメント](/azure/search/cognitive-search-concept-intro)機能により、AI 処理がインデックス作成パイプラインに追加されます。 一般的なユース ケースには、スキャンされたドキュメントに対する OCR、大きなドキュメントに対するエンティティ認識とキー フレーズ抽出、言語検出とテキスト翻訳、感情分析などがあります。
- Azure Cognitive Search のカスタムおよび言語アナライザーを使用して満たされる言語要件。 英語以外のコンテンツがある場合、Azure Cognitive Search では、Lucene アナライザーと Microsoft の自然言語プロセッサの両方がサポートされます。 また、特定の生コンテンツ (分音記号のフィルター処理など) の特殊な処理を実現するようにアナライザーを構成することもできます。

## <a name="use-azure-cognitive-search"></a>Azure Cognitive Search の使用

### <a name="step-1-provision-the-service"></a>手順 1: サービスをプロビジョニングする

[Azure portal](https://portal.azure.com/) または [Azure Resource Manager REST API](/rest/api/searchmanagement/) を使用して、Azure Cognitive Search インスタンスをプロビジョニングできます。 他のサブスクライバーと共有する無料サービス、または自分のサービスのみで使用するリソース専用の有料レベルを選択できます。 有料レベルでは、2 つの面でサービスを拡張できます。

- 大量のクエリ負荷を処理できるように、レプリカを追加して容量を拡張する。
- より多くのドキュメントに対応できるように、パーティションを追加して記憶域を拡大する。

ドキュメント ストレージとクエリのスループットを個別に処理することによって、運用環境の要件に基づいてリソースの割り当てを調整できます。

### <a name="step-2-create-an-index"></a>手順 2:インデックスを作成する

検索可能なコンテンツをアップロードする前に、まず Azure Cognitive Search インデックスを定義する必要があります。 インデックスは、データを保持し、検索クエリを受け付けることができるデータベース テーブルに似ています。 検索するドキュメントの構造を反映するように、マップするインデックス スキーマを定義します。これは、データベースのフィールドに似ています。

スキーマは Azure portal で作成するか、[.NET SDK](/azure/search/search-howto-dotnet-sdk) または [REST API](/rest/api/searchservice/) を使用してプログラムによって作成できます。

### <a name="step-3-load-data"></a>手順 3:データの読み込み

インデックスを定義したら、コンテンツをアップロードする準備が完了します。 プッシュ モデルまたはプル モデルを使用できます。

プル モデルは、外部データ ソースからデータを取得します。 これは、データへの接続、読み取り、シリアル化など、データ インジェストのさまざまな側面を効率化および自動化するインデクサーを通じてサポートされます。 [インデクサー](/rest/api/searchservice/Indexer-operations)は、Azure Cosmos DB、Azure SQL Database、Azure Blob Storage、および Azure Virtual Machines インスタンスでホストされている SQL Server で使用できます。 インデクサーは、オンデマンドのデータ更新にもスケジュールされたデータ更新にも構成できます。

プッシュ モデルは SDK または REST API によって提供され、更新したドキュメントをインデックスに送信するために使用されます。 JSON 形式を使用することにより、事実上すべてのデータセットからデータをプッシュできます。 データの読み込み方法の詳細については、[ドキュメントの追加、更新、削除](/rest/api/searchservice/addupdate-or-delete-documents)に関するページまたは[.NET SDK の使用方法](/azure/search/search-howto-dotnet-sdk)に関するページを参照してください。

### <a name="step-4-search"></a>手順 4:検索

インデックスを入力したら、[REST API](/rest/api/searchservice/search-documents) または [.NET SDK](/dotnet/api/microsoft.azure.search.idocumentsoperations?view=azure-dotnet) で簡単な HTTP 要求を使用して、サービス エンドポイントに[検索クエリを発行](/azure/search/search-query-overview)できます。 [最初の検索アプリケーションの作成](/azure/search/tutorial-csharp-create-first-app)の手順に従って、ユーザー入力を収集して結果を処理する Web ページをビルドして拡張します。 [対話型 REST 向けの Postman](/azure/search/search-get-started-postman) 呼び出しや Azure portal の組み込みの[検索エクスプローラー](/azure/search/search-explorer)を使用して、既存のインデックスに対するクエリを実行することもできます。

## <a name="next-steps"></a>次の手順

- [Azure Cognitive Search](/azure/search/) の詳細を確認する。
- [AI アーキテクチャ](/azure/architecture/browse/)の詳細を確認する。
- [JFK Files のアーキテクチャとソリューションの例](/azure/architecture/solution-ideas/articles/cognitive-search-with-skillsets)に関する記事で、ナレッジ マイニング ソリューションの例を参照する。
