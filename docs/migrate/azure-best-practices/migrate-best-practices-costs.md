---
title: Azure に移行するワークロードのコストとサイズ設定のベスト プラクティス
description: Azure 向けのクラウド導入フレームワークを使用して、Azure に移行するワークロードのコストとサイズ設定のベスト プラクティスについて学習します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 07/01/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: migrate
ms.custom: internal
ms.openlocfilehash: c75f3437d5c53f31daf2389061c6874c0d66706a
ms.sourcegitcommit: b8f8b7631aabaab28e9705934bf67dad15e3a179
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101785939"
---
<!-- docutune:casing ARO -->

# <a name="best-practices-to-cost-and-size-workloads-migrated-to-azure"></a>Azure に移行するワークロードのコストとサイズ設定のベスト プラクティス

移行の計画と設計の際には、コストに注目することで、より確実に Azure への移行で長期的な成功を収めることができます。 移行プロジェクト時は、すべてのチーム (財務チーム、管理チーム、アプリケーション開発チーム) が関連するコストを理解しておくことが重要です。

- 移行の前に、月単位、四半期単位、および年単位の予算目標のベースラインを確立して、移行にかかる金額を見積もり、移行を確実に成功に導くことが重要です。
- 移行後は、コストを最適化し、継続的にワークロードを監視して、将来の使用パターンを計画する必要があります。 移行されたリソースは、ある種類のワークロードとして開始されても、使用状況、コスト、および変化するビジネス要件に基づいて、時間の経過と共に別の種類に変化することがあります。

この記事では、移行の前後で、コストとサイズを準備および管理するためのベスト プラクティスについて説明します。

> [!IMPORTANT]
> この記事で説明するベスト プラクティスと見解は、Azure プラットフォームと、記事の執筆時点で利用できるサービスの機能に基づいています。 特徴や機能は時間の経過と共に変化します。 お客様のデプロイにすべての推奨事項が当てはまるとは限らないため、お客様にとって適切なものを選択してください。

## <a name="before-migration"></a>移行前

ワークロードをクラウドに移動する前に、それらを Azure で実行する毎月のコストを見積もります。 クラウドのコストを先を見越して管理すれば、運営費の予算に準拠する助けになります。 予算が限られている場合は、移行前にこれを考慮に入れます。 コストを削減するため、適切な場合は、ワークロードを Azure のサーバーレス テクノロジに変えることを検討します。

このセクションに示すベスト プラクティスを使用すると、次の点で役立ちます。

- コストを見積もる。
- 仮想マシン (VM) とストレージのサイズを適切に設定する。
- Azure ハイブリッド特典を利用する。
- Azure Reserved Virtual Machine Instances を使用する。
- すべてのサブスクリプションのクラウド支出を見積もる。

## <a name="best-practice-estimate-monthly-workload-costs"></a>ベスト プラクティス:毎月のワークロード コストを見積る

移行されるワークロードの月額料金を予測するために、利用できるツールがいくつかあります。

<!-- TODO: Change "input costs" -->

- **Azure 料金計算ツール:** VM やストレージなど、見積もり対象の製品を選択します。 次に、計算ツールにコストを入力し、見積もりを作成します。

  ![Azure 料金計算ツール メニューのスクリーンショット。](./media/migrate-best-practices-costs/pricing.png)
    "*図 1:Azure 料金計算ツール。* "

- **Azure Migrate:** コストを見積もるには、Azure でワークロードを実行するために必要なすべてのリソースを確認し、それらを計上します。 このデータを取得するには、サーバー、VM、データベース、ストレージを含む資産のインベントリを作成します。 この情報は、Azure Migrate を使用して収集できます。

  - Azure Migrate は、オンプレミス環境で検出と評価を行ってインベントリを提供します。
  - Azure Migrate では、全体像を把握できるように、VM 間の依存関係をマップして表示できます。
  - Azure Migrate の評価には、推定コストが含まれています。
    - **コンピューティング コスト:** Azure Migrate は、評価の作成時には推奨される Azure VM のサイズを採用し、Azure Billing API シリーズを使用して VM の推定月間コストを計算します。 この見積りでは、オペレーティング システム、ソフトウェア アシュアランス、Azure Reserved Virtual Machine Instances、VM のアップタイム、場所、および通貨の設定が考慮されます。 評価に含まれるすべての VM にわたるコストが集計され、月間コンピューティング コストの合計が計算されます。
    - **ストレージ コスト:** Azure Migrate は、評価に含まれるすべての VM のストレージ コストを集計して、月間ストレージ コストの合計を計算します。 特定のマシンの月間ストレージ コストは、そのマシンに接続されているすべてのディスクの月間コストを集計することで計算できます。

    ![Azure Migrate のスクリーンショット。](./media/migrate-best-practices-costs/assess.png)
    "*図 2: Azure Migrate の評価。* "

**詳細情報:**

- [Azure 料金計算ツール](https://azure.microsoft.com/pricing/calculator/)を使用します。
- [Azure Migrate の概要](/azure/migrate/migrate-services-overview)を確認します。
- [Azure Migrate の評価](/azure/migrate/concepts-assessment-calculation)についてのページを読みます。
- [Azure Database Migration Service](/azure/dms/dms-overview) の詳細について確認します。

## <a name="best-practice-right-size-vms"></a>ベスト プラクティス:適切なサイズの VM

ワークロードをサポートする Azure VM をデプロイするときは、さまざまなオプションを選択できます。 各種類の VM には特定の機能があり、さまざまな組み合わせの CPU、メモリ、およびディスクが用意されています。 VM は、次の表に示すようにグループ化されます。

| Type | 詳細 | 使用法 |
| --- | --- | --- |
| **汎用目的** | CPU とメモリのバランスがとれています。 | テストと開発、中小規模のデータベース、およびトラフィック量が中小程度の Web サーバーに適しています。 |
| **コンピューティング最適化** | メモリに対する CPU の比が大きくなっています。 | トラフィック量が中程度の Web サーバー、ネットワーク アプライアンス、バッチ処理、およびアプリケーション サーバーに適しています。 |
| **メモリ最適化** | 高いメモリ対 CPU。 | リレーショナル データベース、中から大規模のキャッシュ、およびインメモリ分析に適しています。 |
| **ストレージの最適化** | 高いディスク スループットと I/O。 | ビッグ データ、SQL データベース、および NoSQL データベースに適しています。 |
| **GPU の最適化** | 特殊な VM。 1 つまたは複数の GPU。 | 大量のグラフィックスおよびビデオの編集。 |
| **高性能** | 最も高速で最も強力な CPU。 オプションの、高スループットのネットワーク インターフェイス (RDMA) を備えた VM。 | クリティカルな高性能アプリケーション。 |

- これらの VM 間の価格の違いと、長期的な予算への影響を理解しておくことが重要です。
- 種類ごとに、内部にいくつかの VM シリーズがあります。
- さらに、あるシリーズ内の VM を選択すると、そのシリーズ内の VM のみをスケールアップまたはスケールダウンできます。 たとえば、`DS2_v2` インスタンスは `DS4_v2` にスケールアップできますが、`F2s_v2` インスタンスなどの異なるシリーズのインスタンスには変更できません。

**詳細情報:**

- [VM の種類とサイズ設定](/azure/virtual-machines/sizes)、およびサイズの種類へのマップに関する詳細を確認します。
- [VM インスタンスのサイズ](/azure/cloud-services/cloud-services-sizes-specs)を計画します。
- [架空の Contoso 社の評価サンプル](/azure/cloud-adoption-framework/migrate/)を確認します。

## <a name="best-practice-select-the-right-storage"></a>ベスト プラクティス:適切なストレージを選択する

オンプレミス ストレージ (SAN または NAS) とそれらをサポートするネットワークのチューニングと維持管理は、コストが高く時間がかかる場合があります。 ファイル (ストレージ) のデータは、一般に、運用と管理の負担を軽減するため、クラウドに移行されます。 Microsoft では、Azure にデータを移動するためのオプションをいくつか提供しており、それらのオプションについて決定する必要があります。 データにとって適切なストレージの種類を選択すれば、組織は毎月数千ドルを節約できます。 いくつかの考慮事項があります。

- あまりアクセスされないデータやビジネス クリティカルでないデータは、最も高価なストレージに配置する必要がありません。
- 逆に、重要なビジネス クリティカル データは、より高いレベルのストレージ オプションに配置する必要があります。
- 移行計画時には、最適なストレージにマップするために、データのインベントリを作成し、重要度で分類します。 予算とコストに加えて、パフォーマンスを考慮に入れます。 コストは必ずしも主要な要因ではありません。 最小コストのオプションを選択すると、ワークロードがパフォーマンスと可用性のリスクにさらされる可能性があります。

### <a name="storage-data-types"></a>ストレージのデータの種類

Azure では、ストレージ データのさまざまな種類が提供されています。

| データ型 | 詳細 | 使用法 |
| --- | --- | --- |
| **BLOB** | テキスト データやバイナリ データなどの大量の非構造化オブジェクトを格納するために最適化されています。 <br><br> | HTTP/HTTPS 経由でどこからでもデータにアクセスします。 <br><br> ストリーミングやランダム アクセスのシナリオに使用します。 たとえば、イメージやドキュメントをブラウザーに直接提供する、ビデオやオーディオをストリーム配信する、バックアップやディザスター リカバリーのデータを格納するなどです。 |
| **[ファイル]** | マネージド ファイル共有は SMB 3.0 経由でアクセスされます。 | オンプレミスのファイル共有を移行する場合に、ファイル データへの複数アクセス/接続を提供するために使用します。 |
| **ディスク** | ページ BLOB に基づきます。 <br><br> ディスクの種類: Standard (HDD または SSD) または Premium (SSD)。 <br><br> ディスク管理: アンマネージド (ユーザーがディスクの設定とストレージを管理する) またはマネージド (ユーザーがディスクの種類を選択し、Azure 側でディスクを管理する)。 | VM には Premium ディスクを使用します。 簡単な管理とスケーリングのためにはマネージド ディスクを使用します。 |
| **キュー** | 認証された呼び出し (HTTP または HTTPS) を介してアクセスされる大量のメッセージを格納および取得します。 | 非同期メッセージ キューを使用してアプリケーションのコンポーネントを接続します。 |
| **テーブル** | テーブルを格納します。 | このデータ型は Azure Cosmos DB Table API の一部です。 |

### <a name="access-tiers"></a>アクセス層

Azure Storage には、ブロック BLOB データにアクセスするためのさまざまなオプションが用意されています。 レベルに合った適切なアクセス層を選択することで、最も費用対効果の高い方法でブロック BLOB データが格納されるようにする助けとなります。

| アクセス層 | 詳細 | 使用法 |
| --- | --- | --- |
| **ホット** | ストレージ コストはクールより高いです。 アクセス料金はクールよりも低いです。 <br><br> これが既定のレベルです。 | 頻繁にアクセスされる、アクティブに使用中のデータに使用します。 |
| **クール** | ストレージ コストはホットより低いです。 アクセス料金はホットよりも高いです。 <br><br> 少なくとも 30 日間格納します。 | 短期間、格納します。 データは使用できますが、アクセス頻度は低いものです。 |
| **Archive** | 個々 のブロック BLOB に使用されます。 <br><br> 最も費用対効果の高いストレージ オプション。 データ アクセスは、ホットとコールドよりも高価です。 | 取得の待ち時間としての数時間を許容でき、少なくとも 180 日間は同じ層に留まるデータに使用します。 |

### <a name="storage-account-types"></a>ストレージ アカウントの種類

Azure では、さまざまな種類のストレージ アカウントとパフォーマンス レベルが提供されています。

| アカウントの種類 | 詳細 | 使用法 |
| --- | --- | --- |
| **汎用 v2 Standard** | BLOB (ブロック、ページ、追加)、ファイル、ディスク、キュー、およびテーブルをサポートしています。 <br><br> ホット、クール、およびアーカイブのアクセス層をサポートしています。 ゾーン冗長ストレージ (ZRS) がサポートされています。 | ほとんどのシナリオの、ほとんどの種類のデータに使用します。 Standard ストレージ アカウントは、HDD または SSD ベースとして指定できます。 |
| **汎用 v2 Premium** | Blob Storage データ (ページ BLOB) をサポートしています。 ホット、クール、およびアーカイブのアクセス層をサポートしています。 ZRS がサポートされています。 <br><br> SSD に格納されます。 | すべての VM のために使用することをお勧めします。 |
| **汎用 v1** | アクセスの階層化はサポートされません。 ZRS はサポートされません。 | アプリケーションに Azure クラシック デプロイ モデルが必要な場合に使用します。 |
| **BLOB** | 非構造化オブジェクトを格納するための特殊なストレージ アカウント。 ブロック BLOB と 追加 BLOB のみを提供します (ファイル、キュー、テーブル、ディスクのストレージ サービスはありません)。 同じ持続性、可用性、スケーラビリティ、および General Purpose v2 と同じパフォーマンスを提供します。 | これらのアカウントにページ BLOB を格納することはできないため、VHD ファイルは格納できません。 アクセス層はホットまたはクールに設定できます。 |

### <a name="storage-redundancy-options"></a>ストレージ冗長オプション

ストレージ アカウントでは、回復力と高可用性のためにさまざまな種類の冗長性を使用できます。

| Type | 詳細 | 使用法 |
| --- | --- | --- |
| **ローカル冗長ストレージ (LRS)** | 1 つのストレージ単位内で、別個の障害ドメインと更新ドメインにレプリケートすることで、ローカルの障害から保護します。 1 つのデータ センター内に複数のデータコピーを保管します。 年間 99.999999999% (9 が 11 個) 以上のオブジェクトの持続性を提供します。 | アプリケーションが格納するデータは簡単に再構築できるものかどうかを考慮に入れます。 |
| **ゾーン冗長ストレージ (ZRS)** | 1 つのリージョン内の 3 つのストレージ クラスター間でレプリケートすることで、データ センター停止の保護に冗長性を持たせます。 各ストレージ クラスターは、物理的に分離されており、独自の可用性ゾーン内に置かれています。 複数のデータセンターまたはリージョンにわたってデータのコピーを複数保持することで、年間 99.9999999999% (9 が 12 個) 以上のオブジェクトの持続性を提供します。 | 一貫性、持続性、および高可用性が必要かどうかを考慮します。 複数のゾーンが永続な影響を受けた場合、リージョンの災害からは保護されないことがあります。 |
| **geo 冗長ストレージ (GRS)** | プライマリ リージョンから数百マイル離れたセカンダリ リージョンにデータをレプリケートすることで、リージョン全体の障害から保護します。 年間 99.99999999999999% (9 が 16 個) 以上のオブジェクトの持続性を提供します。 | Microsoft がセカンダリ リージョンへのフェールオーバーを開始しない限り、レプリカは使用できません。 フェールオーバーが発生した場合、読み取りと書き込みのアクセスは可能です。 |
| **読み取りアクセス geo 冗長ストレージ (RA-GRS)** | GRS に似ています。 年間 99.99999999999999% (9 が 16 個) 以上のオブジェクトの持続性を提供します。 | GRS で使用される 2 番目のリージョンからの読み取りアクセスを許可することで、99.99% の読み取り可用性を提供します。 |

**詳細情報:**

- [Azure Storage の料金](https://azure.microsoft.com/pricing/details/storage/)を確認します。
- [Azure Import/Export](/azure/import-export/storage-import-export-service) の詳細を確認します。
- [BLOB、ファイル、ディスク ストレージのデータの種類](/azure/storage/common/storage-introduction)を比較します。
- [アクセス レベル](/azure/storage/blobs/storage-blob-storage-tiers)の詳細を確認します。
- [さまざまな種類のストレージ アカウント](/azure/storage/common/storage-account-overview)を確認します。
- LRS、ZRS、GRS、読み取りアクセス GRS など、[Azure Storage の冗長性](/azure/storage/common/storage-redundancy)について説明します。
- [Azure Files](/azure/storage/files/storage-files-introduction) の詳細を確認します。

## <a name="best-practice-take-advantage-of-azure-hybrid-benefit"></a>ベスト プラクティス:Azure ハイブリッド特典を活用する

オンプレミスの Microsoft ソフトウェアを Azure と統合するポートフォリオにより、競争力とコスト面でのメリットが得られます。 オペレーティング システム、またはソフトウェア アシュアランス (SA) を通したその他のソフトウェア ライセンスを現在所有している場合は、Azure ハイブリッド特典を使用して、それらのライセンスをクラウドに持ち込むことができます。

**詳細情報:**

- Azure ハイブリッド特典の節約額計算ツールを[見てみます](https://azure.microsoft.com/pricing/hybrid-benefit/)。
- [Windows Server 向けの Azure ハイブリッド特典](https://azure.microsoft.com/pricing/hybrid-benefit/)の詳細を確認します。
- [SQL Server Azure VM の料金ガイダンス](/azure/azure-sql/virtual-machines/windows/pricing-guidance)を確認します。

## <a name="best-practice-use-reserved-vm-instances"></a>ベスト プラクティス:予約 VM インスタンスを使用する

ほとんどのクラウド プラットフォームでは、従量課金制の支払いモデルが使用されています。 ワークロードがどのように変動するかは必ずしもわからないので、このモデルには不利な点があります。 ワークロードの明確な意図を指定すれば、インフラストラクチャの計画に貢献することになります。

Azure Reserved Virtual Machine Instances を使用している場合は、VM インスタンスに対して 1 年分または 3 年分の料金を前払いします。

- 前払いにより、使用するリソースに関して割引が提供されます。
- VM、SQL Database のコンピューティング、Azure Cosmos DB、またはその他のリソース コストを、従量課金制の料金と比較して大幅に削減できます。
- 予約は課金割引を提供するもので、リソースの実行時の状態には影響しません。
- 予約インスタンスはキャンセルできます。

![従量課金制と Azure ハイブリッド特典や予約インスタンスを比較したスクリーンショット。](./media/migrate-best-practices-costs/reserve.png)
"*図 3: Azure Reserved VM Instances。* "

**詳細情報:**

- [Azure Reservations](/azure/cost-management-billing/reservations/save-compute-costs-reservations) の詳細を確認します。
- [Azure Reserved VM Instances のよくある質問](https://azure.microsoft.com/pricing/reserved-vm-instances/#faq)ページをご覧ください。
- 「[Azure VM 上の SQL Server の料金ガイダンス](/azure/azure-sql/virtual-machines/windows/pricing-guidance)」を参照してください。

## <a name="best-practice-aggregate-cloud-spending-across-subscriptions"></a>ベスト プラクティス:すべてのサブスクリプションのクラウド支出を集計する

多くの場合、最終的には複数の Azure サブスクリプションを利用することになります。 たとえば、開発と運用の境界を分けるために追加のサブスクリプションが必要な場合があります。また、クライアントごとに別個のサブスクリプションを必要とするプラットフォームがある場合もあります。 すべてのサブスクリプションにわたってデータのレポート作成を 1 つのプラットフォームに集約する機能があるのは、有用な特徴です。

これを行うために、Azure Cost Management + Billing の API を使用できます。 その後、Azure SQL Database などの 1 つのソースにデータを集約したら、Power BI などのツールを使用して、集約されたデータを明示できます。 集約されたサブスクリプション レポートや、きめ細かなレポートを作成できます。 たとえば、コスト管理に関する事前対応のための分析情報が必要なユーザーのために、部門、リソース グループ、またはその他の情報に基づいて、コストに関する特定のビューを作成できます。 彼らに Azure の課金データへのフル アクセスを提供する必要はありません。

**詳細情報:**

- [Azure Consumption API の概要](/azure/cost-management-billing/manage/consumption-api-overview)を確認します。
- [Power BI Desktop で Azure Consumption Insights に接続する](/power-bi/connect-data/desktop-connect-azure-consumption-insights)方法を確認します。
- [Azure ロールベースのアクセス制御 (Azure RBAC) を使用して Azure の課金情報へのアクセスを管理する](/azure/cost-management-billing/manage/manage-billing-access)方法を確認します。

## <a name="after-migration"></a>移行後

ワークロードの移行を成功させてから、数週間にわたって消費データを収集すると、リソースのコストについて明確な認識が得られます。 データを分析するときに、Azure のリソース グループとリソースについての予算ベースラインの生成を開始できます。 その後、クラウドの予算が費やされようとしている場所を理解したら、コストをさらに削減する方法を分析できます。

## <a name="best-practice-use-azure-cost-management--billing"></a>ベスト プラクティス:Azure Cost Management + Billing を使用する

Microsoft では、支出を追跡する助けとなる Azure Cost Management + Billing を提供しています。 このサービスは以下のことを行います。

- Azure の支出の監視と管理を行い、リソースの使用を最適化する助けになります。
- サブスクリプション全体と、そのすべてのリソースが見直されて、推奨事項が示されます。
- 外部ツールやレポート用の財務システムと統合するための完全な API が提供されています。
- リソースの使用状況を追跡し、1 つの統一されたビューでクラウドのコストを管理するうえで役立ちます。
- 情報に基づいた決定を下しやすいように、運用と財務に関する豊富な分析情報が提供されます。

Azure Cost Management + Billing では、次のことができます。

- 財務上の説明責任のために予算を作成します。
  - 特定の期間 (月単位、四半期単位、年単位) と範囲 (サブスクリプションまたはリソース グループ) について、使用またはサブスクライブしているサービスを計算に入れることができます。 たとえば、月、四半期、または年の期間の Azure サブスクリプション予算を作成できます。
    - 作成した予算は、コストの分析に表示されます。 現在の支出と予算を照らし合わせることは、コストや支出を分析する際に重要です。
  - 予算のしきい値に達したときに、電子メール通知を受信することを選択できます。
  - コスト管理データは、分析のために Azure Storage にエクスポートできます。

  ![Cost Management の予算のスクリーンショット。](./media/migrate-best-practices-costs/budget.png)
  "*図 4: Azure Cost Management + Billing の予算。*

- コスト分析を実行し、組織のコストを探って分析すれば、コストがどのように生じるかを理解し、支出の傾向を特定する助けになります。
  - コスト分析は、Enterprise Agreement ユーザーが利用できます。
  - 部門、アカウント、サブスクリプション、リソース グループを含む多様な範囲別に、コスト分析データを表示できます。
  - 現在の月の総コストと、毎日の累積コストを表示するコスト分析を取得できます。

  ![Azure Cost Management の分析のスクリーンショット](./media/migrate-best-practices-costs/analysis.png)
  "*図 5: Azure Cost Management + Billing の分析。*

- 最適化と効率向上が可能な方法を示す Advisor の推奨事項を取得します。

**詳細情報:**

- [Azure Cost Management + Billing の概要](/azure/cost-management-billing/cost-management-billing-overview)を確認します。
- [Azure Cost Management + Billing でクラウド投資を最適化する](/azure/cost-management-billing/costs/cost-mgt-best-practices)方法を確認します。
- [Azure Cost Management + Billing のレポート](/azure/cost-management-billing/cloudyn/use-reports)を確認します。
- [推奨事項に従ってコストを最適化する](/azure/cost-management-billing/costs/tutorial-acm-opt-recommendations)ことに関するチュートリアルを表示します。
- [Azure Consumption API](/rest/api/consumption/budgets) について確認します。

## <a name="best-practice-monitor-resource-utilization"></a>ベスト プラクティス:リソースの使用率を監視する

Azure では、リソースが消費されたときに使用した分だけ支払いを行います。そうでないときに支払いはしません。 VM の場合、VM が割り当てられているときに課金が発生し、VM の割り当てが解除された後は課金されません。 これを念頭に置いて使用中の VM を監視し、VM のサイズ設定を確認します。

ベースラインを特定するために、継続的に VM のワークロードを評価します。 たとえば、月曜日から金曜日の午前 8 時から午後 6 時まではワークロードの用頻度が高く、それ以外の時間帯はほとんど使用されない場合は、ピーク時間帯以外は VM をダウングレードできます。 これは、VM のサイズを変更すること、または仮想マシン スケール セットを使用して VM の上下の自動スケーリングを行うことを意味する場合もあります。 一部の企業では、いつ使用できる必要があり、いつ必要がないかを指定するカレンダーから VM を「一時停止」させています。

Azure Cost Management + Billing、Azure Monitor、Azure Advisor などの Microsoft ツールを使用して VM の使用を監視できます。 サードパーティ製のツールも使用できます。

> [!NOTE]
> VM の監視に加えて、Azure ExpressRoute や仮想ネットワーク ゲートウェイなどの他のネットワーク リソースで、過小使用や過多使用が起きてないかを監視する必要があります。

**詳細情報:**

- [Azure Monitor](/azure/azure-monitor/overview) と [Azure Advisor](/azure/advisor/advisor-overview) の概要を確認します。
- [Azure Advisor のコストに関する推奨事項](/azure/advisor/advisor-cost-recommendations)を確認します。
- [推奨事項に従ってコストを最適化](/azure/cost-management-billing/costs/tutorial-acm-opt-recommendations)し、[予期しない料金を防ぐ](/azure/cost-management-billing/cost-management-billing-overview)方法を学びます。
- [Azure Resource Optimization (ARO) ツールキット](https://github.com/azure/azure-quickstart-templates/tree/master/azure-resource-optimization-toolkit)について学びます。

## <a name="best-practice-implement-resource-group-budgets"></a>ベスト プラクティス:リソース グループの予算を実施する

リソース グループを使用してコスト境界を表すと便利な場合がよくあります。 リソース グループの予算は、リソース グループに関連するコストを追跡するのに役立ちます。 予算に達したか予算を超えたときにアラートをトリガーし、さまざまなプレイブックを実行できます。

**詳細情報:**

- [Azure Budgets でコストを管理する](/azure/cost-management-billing/manage/cost-management-budget-scenario)方法を学びます。
- [Azure の予算の作成と管理](/azure/cost-management-billing/costs/tutorial-acm-create-budgets)に関するチュートリアルを確認します。

## <a name="best-practice-optimize-azure-monitor-retention"></a>ベスト プラクティス:Azure Monitor の保持期間を最適化する

Azure にリソースを移動し、それらの診断ログを有効にすると、大量のログ データが生成されます。 通常、このログ データは、Log Analytics ワークスペースにマップされているストレージ アカウントに送信されます。 Azure Monitor のデータ保持を最適化するためのいくつかのヒントを次に示します。

- ログ データの保持期間が長いほど、より多くのデータを持つことになります。
- すべてのログ データが一様なわけではなく、一部のリソースは他よりも多くのログ データを生成します。
- 規制とコンプライアンスのため、リソースによっては保持期間の長いログ データもあれば、短いデータもあります。
- ログのストレージ コストを最適化することと、必要なログ データを保管することのバランスに注意する必要があります。
- 移行の完了直後にログ記録を評価して設定を行うことをお勧めします。そうすることで、重要でないログの保持にコストがかからなくなります。

**詳細情報:**

- [使用量と推定コストの監視](/azure/azure-monitor/usage-estimated-costs)について学びます。

## <a name="best-practice-optimize-storage"></a>ベスト プラクティス:ストレージを最適化する

移行の前にベスト プラクティスに従ってストレージを選択した場合は、おそらくいくつかの利点を得ることになります。 ただし、さらに最適化できるその他のストレージ コストが存在する可能性があります。 BLOB やファイルは時間の経過と共に古くなります。 データがもはや使用されない可能性もありますが、規制の要件のため、データを一定期間保管する必要がある可能性があります。 そのため、元の移行に使用したパフォーマンスが高いストレージにデータを格納する必要がない場合があります。

古いデータを識別して、より低コストのストレージ領域に移動することが、月単位のストレージ予算とコスト削減に大きな影響を及ぼすことがあります。 Azure では、こうした古いデータを識別して格納する場合に役立つ方法を多数提供しています。

- 汎用 v2 ストレージのアクセス層を活用して、重要度の低いデータをホット アクセス層からクールやアーカイブのアクセス層に移動します。
- StorSimple を使用すると、カスタマイズしたポリシーに基づく古いデータを移動しやすくなります。

**詳細情報:**

- [アクセス レベル](/azure/storage/blobs/storage-blob-storage-tiers)の詳細を確認します。
- [StorSimple の概要](/azure/azure-monitor/overview)を確認します。
- [StorSimple の価格](https://azure.microsoft.com/pricing/details/storsimple/)を確認します。

## <a name="best-practice-automate-vm-optimization"></a>ベスト プラクティス:VM の最適化を自動化する

クラウドで VM を実行する場合の最終目標は、VM が使用する CPU、メモリ、およびディスクを最大利用することです。 最適化されていない VM を検出した場合や、VM が使用されていない期間が頻繁にある場合は、それらの VM を、シャットダウンするか、仮想マシン スケール セットを使用してスケールダウンすることをお勧めします。

VM は、Azure Automation、仮想マシン スケール セット、自動シャット ダウン、スクリプト型のソリューション、サード パーティ製のソリューションを使用して最適化できます。

**詳細情報:**

- [垂直方向の自動スケーリング](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-vertical-scale-reprovision)について学びます。
- [VM の自動開始をスケジュールします](https://azure.microsoft.com/updates/azure-devtest-labs-schedule-vm-auto-start/)。
- [Azure Automation で業務時間外の VM を開始または停止する](/azure/automation/automation-solution-vm-management)方法を確認します。
- [Azure Advisor](/azure/advisor/advisor-overview) と [Azure Resource Optimization (ARO) ツールキット](https://github.com/azure/azure-quickstart-templates/tree/master/azure-resource-optimization-toolkit)に関する詳細情報を確認します。

## <a name="best-practices-use-azure-logic-apps-and-runbooks-with-budgets-api"></a>ベスト プラクティス:Budgets API と共に Logic Apps と Runbook を使用する

Azure では、テナント課金情報にアクセスできる REST API が提供されています。 Budgets API を使用して、外部システムとワークフローを統合できます。これらは、作成したメトリックによって API データからトリガーされます。 使用状況やリソースに関するデータを、お使いのデータ分析ツールで取得できます。 Budgets API は、Azure Logic Apps や Runbook と統合できます。

Azure Resource Usage API と Azure Resource RateCard API は、コストを正確に予測して管理するうえで役立ちます。 これらの API はリソース プロバイダーとして実装されていて、Azure Resource Manager が公開している API に含まれています。

**詳細情報:**

- [Azure Budgets API](/rest/api/consumption/budgets) を確認します。
- [Azure Billing API シリーズ](/azure/cost-management-billing/manage/consumption-api-overview#usage-details-api) を使用して使用状況の分析情報を取得します。

## <a name="best-practice-implement-serverless-technologies"></a>ベスト プラクティス:サーバーレス テクノロジを実装する

VM ワークロードは、多くの場合、ダウンタイムを回避するために "現状のまま" で移行されます。 VM は、多くの場合、間欠的なタスク、実行期間が短いタスク、または長時間かかるタスクをホストしていることがあります。 たとえば、Windows タスク スケジューラや PowerShell スクリプトなどの、スケジュールされたタスクを実行する VM です。 これらのタスクが実行中でないときには、それにもかかわらず VM とディスク ストレージのコストが費やされています。

移行後に、これらの種類のタスクを徹底して見直したら、それを Azure Functions や Azure Batch のジョブなどのサーバーレス テクノロジに移行することを検討します。 これらのソリューションを使用することでコストを削減することができ、VM の管理と保守を行う必要がなくなります。

**詳細情報:**

- [Azure Functions](https://azure.microsoft.com/services/functions/) について学びます。
- [Azure Batch](https://azure.microsoft.com/services/batch/) について学びます。

## <a name="next-steps"></a>次のステップ

他のベスト プラクティスを確認します。

- 移行後の[セキュリティと管理のベスト プラクティス](./migrate-best-practices-security-management.md)。
- 移行後の[ネットワークのベスト プラクティス](./migrate-best-practices-networking.md)。
