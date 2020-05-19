---
title: ストレージ オプションを確認する
description: Azure 向けのクラウド導入フレームワークを使用して、Azure ワークロードのストレージ オプションを確認する方法について学習します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 05/15/2019
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.openlocfilehash: af6604db1e0bd50ea93373dfdffa67d74b413f68
ms.sourcegitcommit: 60d8b863d431b5d7c005f2f14488620b6c4c49be
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2020
ms.locfileid: "83216318"
---
<!-- cSpell:ignore HDFS databox Avere HANA ACLs Isilon DFSR Cloudera -->

# <a name="review-your-storage-options"></a>ストレージ オプションを確認する

ストレージ機能は、クラウドでホストされたワークロードやサービスをサポートするために重要です。 クラウド導入対応性の準備の一部として、この記事を確認することにより、ストレージ ニーズに対する計画やその対処に役立ててください。

## <a name="select-storage-tools-and-services-to-support-your-workloads"></a>ワークロードをサポートするストレージ ツールおよびサービスを選択する

[Azure Storage](https://docs.microsoft.com/azure/storage) は、クラウド ストレージを提供するための Azure プラットフォームのマネージド サービスです。 Azure Storage は、いくつかのコア サービスとサポートしている機能で構成されています。 Azure のストレージはセキュリティで保護され、高可用性、持続性、拡張性、さらに冗長性を備えています。 ここで説明されているシナリオや考慮事項を確認することにより、組織のワークロード、ガバナンス、およびデータ ストレージの要件に適した関連する Azure サービスと適切なアーキテクチャを選択してください。

<!-- For each application or service you'll deploy to your landing zone environment, use the following decision tree as a starting point to help you determine your storage resources requirements:

![Azure storage decision tree](../../_images/ready/storage-decision-tree.png)
-->

### <a name="key-questions"></a>主な質問

Azure Storage のデシジョン ツリーに基づいた意思決定に役立てるために、ワークロードに関する次の質問に答えてください。

- **ワークロードには、サービスとしてのインフラストラクチャ (IaaS) 仮想マシンのデプロイをサポートするためのディスク ストレージが必要ですか?** [Azure Disk Storage](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) は、IaaS 仮想マシンに仮想ディスク機能を提供します。
- **ワークロードの一部として、ダウンロード可能なイメージ、ドキュメント、またはその他のメディアを提供する必要がありますか?** [Azure Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) は、[静的ファイルをホストする](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website)機能を提供します。その後、これらのファイルはインターネット経由でダウンロードできます。 BLOB ストレージでホストされた資産をパブリックにすることも、あるいは Azure Active Directory (Azure AD)、共有キー、または共有アクセス署名によって[資産を承認されたユーザーに制限する](https://docs.microsoft.com/azure/storage/common/storage-auth)こともできます。
- **仮想マシン ログ、アプリケーション ログ、および分析データを格納するための場所が必要ですか?** Azure Blob Storage を使用して [Azure Monitor ログ データを格納](https://docs.microsoft.com/azure/storage/common/storage-analytics)できます。
- **バックアップ、ディザスター リカバリー、またはワークロード関連データのアーカイブのための場所を提供する必要がありますか?** Azure Disk Storage は、Azure Blob Storage を使用して、[バックアップおよびディザスター リカバリー機能](https://docs.microsoft.com/azure/virtual-machines/windows/backup-and-disaster-recovery-for-azure-iaas-disks)を提供します。 また、オンプレミスまたは IaaS VM でホストされた [SQL Server データ](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service?view=sql-server-2017)などの他のリソースをバックアップするための場所として BLOB ストレージを使用することもできます。
- **ビッグ データ分析ワークロードをサポートする必要はありますか。** [Azure Data Lake Storage Gen 2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction) は Azure Blob Storage 上に構築されています。 Data Lake Storage Gen 2 は、大企業のデータ レイク機能をサポートできます。 また、数ペタバイトの情報の格納を処理しながら、数百ギガビットのスループットを維持することもできます。
- **クラウド ネイティブなファイル共有を提供する必要がありますか?** Azure には、クラウドでホストされたファイル共有を提供する Azure NetApp Files と Azure Files という 2 つのプライマリ サービスがあります。 [Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction) は、SAP などの一般的なエンタープライズ ワークロードに最適な高パフォーマンスの NFS 共有を提供します。 [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) は、SMB 3.0 および HTTPS 経由でアクセスできるファイル共有を提供します。
- **オンプレミスのハイ パフォーマンス コンピューティング (HPC) ワークロード用にハイブリッド クラウド ストレージをサポートする必要はありますか。** [Avere vFXT for Azure](https://docs.microsoft.com/azure/avere-vfxt/avere-vfxt-overview) は、クラウド ベースのストレージを使用して、オンプレミスのストレージ機能を拡張するために使用できるハイブリッド キャッシュ ソリューションです。 Avere vFXT for Azure は、1,000 ～ 40,000 個の CPU コアのコンピューティング ファームを含む、読み取り負荷の高い HPC ワークロード向けに最適化されています。 Avere vFXT for Azure は、オンプレミスのハードウェア ネットワーク接続ストレージ (NAS)、Azure Blob Storage、またはその両方と統合できます。
- **オンプレミスのデータのクラウドへの大規模なアーカイブや同期を実行する必要がありますか?** [Azure Data Box](https://docs.microsoft.com/azure/databox-family) 製品は、オンプレミス環境からクラウドへの大量のデータの移動に役立つように設計されています。 [Azure Data Box Gateway](https://docs.microsoft.com/azure/databox-online/data-box-gateway-overview) は、オンプレミスに存在する仮想デバイスです。 Data Box Gateway は、クラウドへの大規模なデータ移行を管理するために役立ちます。 クラウドに移動する前にデータを分析、変換、またはフィルター処理する必要がある場合は、オンプレミス環境にデプロイされた AI 対応の物理エッジ コンピューティング デバイスである [Azure Data Box Edge](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview) を使用できます。 Data Box Edge は、データの処理と Azure への安全な転送を高速化します。
- **クラウド ストレージを使用するように既存のオンプレミスのファイル共有を拡張したいと考えていますか?** [Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide) では、オンプレミスの Windows Server コンピューターでホストされたファイル共有の拡張機能として Azure Files サービスを使用できます。 この同期サービスは、Windows Server を Azure ファイル共有の高速キャッシュに変換します。 これにより、共有にアクセスするオンプレミスのコンピューターは Windows Server で使用できる任意のプロトコルを使用できます。

## <a name="common-storage-scenarios"></a>一般的なストレージ シナリオ

Azure には、さまざまなストレージ機能のための複数の製品やサービスが用意されています。 この記事で前に示されているストレージ要件のデシジョン ツリーに加えて、次の表では、一連の可能性のあるストレージ シナリオと、そのシナリオの要件に対処するための推奨される Azure サービスについて説明します。

### <a name="block-storage-scenarios"></a>ブロック ストレージのシナリオ

<!-- markdownlint-disable MD033 -->

| **シナリオ** | **推奨される Azure サービス** | **推奨されるサービスに関する考慮事項** |
|---|---|---|
| LOB アプリケーションを実行している直接接続ストレージを含むベア メタル サーバーまたは VM (Hyper-V または VMware) があります。 | [Azure Disk Storage (Premium SSD)](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#premium-ssd) | 運用サービスの場合、Premium SSD オプションは、高い IOPS とスループットと共に一貫した低待機時間を提供します。 |
| Web およびモバイル アプリをホストするサーバーがあります。 | [Azure Disk Storage (Standard SSD)](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-ssd) | 運用環境の CPU に依存した Web およびアプリ サーバーには (Premium SSD より低コストの) Standard SSD の IOPS とスループットで十分である可能性があります。 |
| エンタープライズ SAN またはオールフラッシュ アレイ (AFA) があります。 | [Azure Disk Storage (Premium または Ultra SSD)](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types)  <br><br> [Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction) | Ultra SSD は NVMe ベースであり、高い IOPS と帯域幅と共にミリ秒未満の待機時間を提供します。 Ultra SSD は、最大 64 TiB まで拡張できます。 Premium SSD か Ultra SSD かの選択は、ピーク待機時間、IOPS、およびスケーラビリティの要件によって異なります。 |
| 高可用性 (HA) クラスター化サーバー (SQL Server FCI や Windows Server フェールオーバー クラスタリングなど) があります。 | [Azure Files (Premium)](https://docs.microsoft.com/azure/storage/files/storage-files-planning#storage-tiers) <br><br> [Azure Disk Storage (Premium または Ultra SSD)](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types) | クラスター化されたワークロードには、フェールオーバーまたは HA のために同じ基になる共有ストレージをマウントする複数のノードが必要です。 Premium ファイル共有は、SMB 経由でマウントできる共有ストレージを提供します。 また、[パートナー ソリューション](https://azuremarketplace.microsoft.com/marketplace/apps/sios_datakeeper.sios-datakeeper-8?tab=Overview)を使用して、Premium SSD または Ultra SSD 上に共有ブロック ストレージを構成することもできます。 |
| リレーショナル データベースまたはデータ ウェアハウスのワークロード (SQL Server や Oracle など) があります。 | [Azure Disk Storage (Premium または Ultra SSD)](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types) | Premium SSD か Ultra SSD かの選択は、ピーク待機時間、IOPS、およびスケーラビリティの要件によって異なります。 Ultra SSD では、[スケーラビリティのために記憶域プールを構成する必要がなくなる](https://azure.microsoft.com/blog/mission-critical-performance-with-ultra-ssd-for-sql-server-on-azure-vm)ため、複雑さも軽減されます。 |
| NoSQL クラスター (Cassandra や MongoDB など) があります。 | [Azure Disk Storage (Premium SSD)](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#premium-ssd) | Azure Disk Storage の Premium SSD オファリングは、高い IOPS とスループットと共に一貫した低待機時間を提供します。 |
| 永続ボリュームを使用してコンテナーを実行しています。 | [Azure Files (Standard または Premium)](https://docs.microsoft.com/azure/storage/files/storage-files-planning) <br><br> [Azure Disk Storage (Standard、Premium、または Ultra SSD)](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types) | ファイル (RWX) およびブロック (RWO) ボリュームのドライバー オプションは、Azure Kubernetes Service (AKS) とカスタム Kubernetes の両方のデプロイに使用できます。 永続ボリュームは、Azure Disk Storage ディスクまたはマネージド Azure Files 共有のどちらかにマップできます。 永続ボリュームに対するワークロードの要件に基づいて、Premium または Standard オプションを選択します。 |
| データ レイクがあります (HDFS データ用の Hadoop クラスターなど)。 | [Azure Data Lake Storage Gen 2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction) <br><br> [Azure Disk Storage (Standard または Premium SSD)](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types) | Azure Blob Storage の Data Lake Storage Gen 2 機能は、サーバー側の HDFS 互換性とペタバイト規模の並列分析を提供します。 また、HA と信頼性も提供されます。 Cloudera などのソフトウェアは、必要に応じて、マスター/ワーカー ノード上で Premium または Standard SSD を使用できます。 |
| SAP または SAP HANA の展開があります。 | [Azure Disk Storage (Premium または Ultra SSD)](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types) | Ultra SSD は、階層 1 の SAP ワークロードにミリ秒未満の待機時間を提供するように最適化されています。 Ultra SSD は現在、プレビュー段階です。 Premium SSD は M シリーズと共に、一般提供 (GA) オプションを提供します。 |
| プライマリ サーバーから同期する厳密な RPO/RTO を持つディザスター リカバリー サイトがあります。 | [Azure ページ BLOB](https://docs.microsoft.com/azure/storage/blobs/storage-blob-pageblob-overview) | Azure ページ BLOB は、フェールオーバーが発生するまでコンピューティング VM を必要とすることなく、Azure への低コストのレプリケーションを可能にするために、レプリケーション ソフトウェアによって使用されます。 詳細については、[Azure Disk Storage のドキュメント](https://docs.microsoft.com/azure/virtual-machines/windows/backup-and-disaster-recovery-for-azure-iaas-disks)を参照してください。 **注:** ページ BLOB は最大 8TB をサポートします。 |

### <a name="file-and-object-storage-scenarios"></a>ファイルとオブジェクトのストレージ シナリオ

| **シナリオ** | **推奨される Azure サービス** | **推奨されるサービスに関する考慮事項** |
|---|---|---|
| Windows ファイル サーバーを使用しています。 | [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-planning) <br><br> [Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning) | Azure File Sync を使用すると、めったに使用されないデータをクラウド ベースの Azure ファイル共有に格納しながら、最もよく使用されるファイルをオンプレミスでキャッシュして高速なローカル アクセスを実現できます。 マルチサイト同期を使用して、ファイルを複数のサーバー間の同期を維持することもできます。 ワークロードをクラウドのみのデプロイに移行する予定がある場合は、Azure Files で十分である可能性があります。 |
| エンタープライズ NAS (NetApp Files や Dell-EMC Isilon など) があります。 | [Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction) <br><br> [Azure Files (Premium)](https://docs.microsoft.com/azure/storage/files/storage-files-planning#storage-tiers) | NetApp のオンプレミス デプロイがある場合は、Azure NetApp Files を使用してデプロイを Azure に移行することを検討してください。 Windows Server や Linux サーバーを使用しているか、それらのサーバーに移行しようとしているか、またはファイル共有の基本的な機能が必要な場合は、Azure Files を使用することを検討してください。 継続的なオンプレミス アクセスの場合は、クラウド階層化メカニズムを使用して、Azure File Sync で Azure ファイル共有をオンプレミスのファイル共有と同期させます。 |
| ファイル共有 (SMB または NFS) があります。 | [Azure Files (Standard または Premium)](https://docs.microsoft.com/azure/storage/files/storage-files-planning) <br><br> [Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction) | Azure Files の Premium 層か Standard 層かの選択は、IOPS、スループット、および待機時間の一貫性へのニーズによって異なります。 NetApp のオンプレミス デプロイがある場合は、Azure NetApp Files を使用することを検討してください。 アクセス制御リスト (ACL) とタイムスタンプをクラウドに移行する必要がある場合は、Azure File Sync が、これらのすべての設定を便利な移行パスとして Azure ファイル共有に移動することができます。 |
| 数ペタバイトのデータのためのオンプレミスのオブジェクト ストレージ システム (Dell-EMC ECS など) があります。 | [Azure BLOB Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) |  Azure Blob Storage には、ワークロードのパフォーマンスとコストのニーズに適合するように、Premium、ホット、クール、アーカイブの各層が用意されています。 |
| DFSR デプロイ、またはブランチ オフィスを処理するための別の方法があります。 | [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-planning) <br><br> [Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning) | Azure File Sync はマルチサイト同期を提供し、クラウド内の複数のサーバーとネイティブ Azure ファイル共有間でファイルの同期を維持します。 クラウド階層化を使用して、オンプレミスの固定のストレージ フットプリントに移動します。 クラウド階層化は、サーバーを関連するファイルのキャッシュに変換しながら、Azure ファイル共有内のコールド データをスケーリングします。 |
| バックアップとディザスター リカバリー、または長期的なデータ保持のために、テープ ライブラリ (オンプレミスまたはオフサイト) を所有しています。 | [Azure Blob Storage (クールまたはアーカイブ層)](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers) | Azure Blob Storage のアーカイブ層は、可能性のある最も低いコストになりますが、オフライン データをクール、ホット、または Premium 層のストレージにコピーしてアクセス可能になるまでに数時間必要になることがあります。 クール層は、低コストでの瞬時のアクセスを提供します。 |
| バックアップを受け取るように構成されたファイルまたはオブジェクト ストレージがあります。 | [Azure Blob Storage (クールまたはアーカイブ層)](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers) <br/>[Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning) | 最もコストが低いストレージでの長期の保持のためにデータをバックアップするには、データを Azure Blob Storage に移動し、クールおよびアーカイブ層を使用します。 サーバー (オンプレミスまたは Azure VM 上に存在) 上のファイル データの高速ディザスター リカバリーを有効にするには、Azure File Sync を使用して、共有を個々の Azure ファイル共有と同期します。Azure ファイル共有スナップショットを使用すると、以前のバージョンを復元し、それを元の接続されたサーバーと同期したり、Azure ファイル共有でネイティブにアクセスしたりできます。 |
| ディザスター リカバリー サイトへのデータ レプリケーションを実行しています。 | [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-planning) <br><br> [Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning) | Azure File Sync では、ディザスター リカバリー サーバーが必要なくなり、ファイルがネイティブな Azure SMB 共有に格納されます。 高速ディザスター リカバリーでは、障害が発生したオンプレミス サーバー上のすべてのデータが迅速に再構築されます。 複数のサーバーの場所の同期状態を維持したり、クラウドを使った階層化により関連するデータのみをオンプレミスに保存したりすることができます。 |
| 切断されたシナリオでデータ転送を管理します。 | [Azure Data Box Edge または Azure Data Box Gateway](https://docs.microsoft.com/azure/databox-online) | Data Box Edge または Data Box Gateway を使用すると、接続が切断されたシナリオでデータをコピーできます。 ゲートウェイはオフラインである場合、コピーされたすべてのファイルをキャッシュに保存し、後で接続されたときにそれをアップロードします。 |
| クラウドへの継続的なデータ パイプラインを管理しています。 | [Azure Data Box Edge または Azure Data Box Gateway](https://docs.microsoft.com/azure/databox-online) | 単に、常にデータを生成しているシステムにそのデータを直接 Storage Gateway にコピーさせることによって、そのシステムからクラウドにデータを移動します。 後でそのデータにアクセスする必要がある場合は、そのまま配置した場所にあります。 |
| 同時に受信するデータの量が急増しています。 | [Azure Data Box Edge または Azure Data Box Gateway](https://docs.microsoft.com/azure/databox-online) | 自律走行車が車庫への駐車に戻ったときや、遺伝子シーケンス処理マシンで分析が完了したときのように、同時に受信する大量のデータを管理します。 そのすべてのデータを高速なローカルの速度で Data Box Gateway にコピーした後、ネットワークで許可されたら、ゲートウェイがそれをアップロードできるようにします。 |

### <a name="plan-based-on-data-workloads"></a>データ ワークロードに基づいて計画する

| **シナリオ** | **推奨される Azure サービス** | **推奨されるサービスに関する考慮事項** |
|---|---|---|
| 非構造化データを保持する必要がある新しいクラウド ネイティブ アプリケーションを開発したいと考えています。 | [Azure BLOB Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) | |
| オンプレミスの NetApp インスタンスから Azure にデータを移行する必要があります。 | [Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction) | |
| オンプレミスの Windows ファイル サーバー インスタンスから Azure にデータを移行する必要があります。 | [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-planning) | |
| ファイル データをクラウドに移動する必要がありますが、引き続き主にオンプレミスからデータにアクセスします。 | [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-planning) <br><br> [Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning) | |
| クラウドで計算が実行されている間オンプレミスに存在するデータ資産を含む NFS/SMB の読み取り負荷の高いファイル ベースのワークロードである "バースト コンピューティング" をサポートする必要があります。 | [Avere vFXT for Azure](https://docs.microsoft.com/azure/avere-vfxt/avere-vfxt-overview) | IaaS スケールアウト NFS/SMB ファイル キャッシュ |
| ローカル ディスクまたは iSCSI を使用するオンプレミスのアプリケーションを移動する必要があります。 | [Azure Disk Storage](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) | |
| 永続ボリュームを持つコンテナー ベースのアプリケーションを移行する必要があります。 | [Azure Disk Storage](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) <br><br> [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-planning) | |
| Windows Server や NetApp ではないファイル共有をクラウドに移動する必要があります。 | [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-planning) <br><br> [Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction) | Protocol Support Regional Availability Performance Requirements Snapshot と Clone Capabilities Price Sensitivity |
| 数テラバイトから数ペタバイトのデータをオンプレミスから Azure に転送する必要があります。 | [Azure Data Box Edge](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview) | |
| Azure に転送する前にデータを処理する必要があります。 | [Azure Data Box Edge](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview) | |
| ローカル キャッシュを使用して、自動化された方法で継続的なデータ インジェストをサポートする必要があります。 | [Azure Data Box Gateway](https://docs.microsoft.com/azure/databox-online/data-box-gateway-overview) | |

## <a name="learn-more-about-azure-storage-services"></a>Azure Storage サービスの詳細

要件に最も適合する Azure ツールを特定したら、次の表でリンクされている詳細なドキュメントを使用して、これらのサービスに精通してください。

| **サービス** | **説明** |
|---|---|
| [Azure BLOB Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) | Azure Blob Storage は、Microsoft のクラウド用オブジェクト ストレージ ソリューションです。 Blob Storage は、テキスト データやバイナリ データなどの大量の非構造化データを格納するために最適化されています。 非構造化データは、特定のデータ モデルまたは定義に従っていないデータ (テキスト データやバイナリ データなど) です。 <br><br> Blob Storage は、次の用途に適しています。 <li> 画像またはドキュメントをブラウザーに直接配信する。 <li> 分散アクセス用にファイルを格納する。 <li> ビデオおよびオーディオをストリーミング配信する。 <li> ログ ファイルに書き込む。 <li> バックアップと復元、ディザスター リカバリー、アーカイブのためのデータを格納する。 <li> オンプレミス サービスまたは Azure ホステッド サービスで分析するデータを格納する。 |
| [Azure Data Lake Storage Gen 2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction) | Blob Storage では、Microsoft のクラウド向けのエンタープライズ ビッグ データ分析ソリューションである Azure Data Lake Storage Gen2 がサポートされています。 Azure Data Lake Storage Gen2 では、階層型のファイル システムに加え、低コスト、階層型ストレージ、高可用性、強力な一貫性、ディザスター リカバリー機能を含む Blob Storage の利点が提供されます。 |
| [Azure Disk Storage](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) | Azure Disk Storage は、Azure 仮想マシンを機能強化するための永続的で、かつ高パフォーマンスのブロック ストレージを提供します。 Azure ディスクは持続性が高く、セキュリティで保護されており、[Premium または Ultra SSD](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types) を使用する VM のための業界で唯一の単一インスタンス SLA を提供します。 Azure ディスクは、Azure 仮想マシンの障害ドメインにマップされる可用性セットと可用性ゾーンで高可用性を提供します。 さらに、Azure ディスクは Azure の最上位リソースとして管理されます。 既定では、ロールベースのアクセス制御 (RBAC)、ポリシー、タグ付けなどの Azure Resource Manager 機能が提供されます。 |
| [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-planning) | Azure Files では、フル マネージドのネイティブな SMB ファイル共有が提供され、VM を実行する必要がありません。 Azure Files 共有をネットワーク ドライブとして任意の Azure VM またはオンプレミス マシンにマウントできます。 |
| [Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning) | Azure File Sync を使用すると、オンプレミスのファイル サーバーの柔軟性、パフォーマンス、互換性を維持したまま、Azure Files 内の組織のファイル共有を一元化できます。 Azure File Sync により、ご利用の Windows Server が Azure ファイル共有の高速キャッシュに変わります。 |
| [Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction) | Azure NetApp Files サービスは、エンタープライズ クラスでハイパフォーマンスの従量制課金ファイル ストレージ サービスです。 Azure NetApp Files は、既定で高い可用性を備え、あらゆる種類のワークロードをサポートします。 サービス レベルとパフォーマンス レベルを選び、サービスを通じてスナップショットを設定することができます。 |
| [Azure Data Box Edge](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview) | Azure Data Box Edge は、Azure との間でデータを移動するオンプレミスのネットワーク デバイスです。 Data Box Edge は、アップロード中にデータを前処理するための AI 対応のエッジ コンピューティング機能を備えています。 Data Box Gateway は、仮想バージョンのデバイスですが、同じデータ転送機能を備えています。 |
| [Azure Data Box Gateway](https://docs.microsoft.com/azure/databox-online/data-box-gateway-overview) | Azure Data Box Gateway は、Azure にシームレスにデータを送信できるストレージ ソリューションです。 Data Box Gateway は、仮想化環境またはハイパーバイザーにプロビジョニングされた仮想マシンに基づく仮想デバイスです。 この仮想デバイスはオンプレミスに存在し、ユーザーは NFS および SMB プロトコルを使用してそこにデータを書き込みます。 このデバイスはその後、Azure ブロック BLOB、Azure ページ BLOB、または Azure Files にデータを転送します。 |
| [Avere vFXT for Azure](https://docs.microsoft.com/azure/avere-vfxt/avere-vfxt-overview) | Avere vFXT for Azure は、データ集約型のハイ パフォーマンス コンピューティング (HPC) タスク向けのファイルシステム キャッシュ ソリューションです。 これを使用すると、クラウド コンピューティングのスケーラビリティを活かして、データがお客様のオンプレミス ハードウェアに格納されている場合であっても、必要なときに必要な場所でデータにアクセスできます。 |

## <a name="data-redundancy-and-availability"></a>データの冗長性と可用性

Azure Storage には、ローカル冗長ストレージ (LRS)、ゾーン冗長ストレージ (ZRS)、geo 冗長ストレージ (GRS)、読み取りアクセス geo 冗長ストレージ (RA-GRS) など、顧客のニーズに基づいて持続性と高可用性を確保するために役立つさまざまな冗長性オプションがあります。

これらの機能や、ユース ケースに最適な冗長性オプションを決定する方法の詳細については、「[Azure Storage の冗長性](https://docs.microsoft.com/azure/storage/common/storage-redundancy)」を参照してください。 また、ストレージ サービスのサービス レベル アグリーメント (SLA) は、財務的な裏付けのある保証を提供します。 詳細については、「[Managed Disks の SLA](https://azure.microsoft.com/support/legal/sla/managed-disks/v1_0)」、「[Virtual Machines の SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8)」、および「[ストレージ アカウントの SLA](https://azure.microsoft.com/support/legal/sla/storage/v1_4)」を参照してください。

Azure ディスクの適切なソリューションの計画については、[Azure Disk Storage のバックアップとディザスター リカバリー](https://docs.microsoft.com/azure/virtual-machines/windows/backup-and-disaster-recovery-for-azure-iaas-disks)に関するページを参照してください。

## <a name="security"></a>Security

クラウド内のデータを保護するために、Azure Storage は、データのセキュリティと保存中および転送中のデータの暗号化に関するいくつかのベスト プラクティスを提供しています。 次のようにすることができます。

- RBAC と Azure AD を使用して、ストレージ アカウントをセキュリティで保護します。
- アプリケーションと Azure の間で送信されるデータを、クライアント側暗号化、HTTPS、または SMB 3.0 を使って、セキュリティで保護できます。
- Storage Service Encryption を使用して Azure Storage に書き込まれたら自動的に暗号化されるようにデータを設定します。
- Shared Access Signature を使って、Azure Storage 内のデータ オブジェクトに対する委任されたアクセス権を付与できます。
- 分析を使用して、だれかが Azure のストレージにアクセスするときに使用している認証方法を追跡します。

これらのセキュリティ機能は、Azure Blob Storage (ブロックとページ) および Azure Files に適用されます。 ストレージのセキュリティに関する詳細なガイダンスは、「[Azure Storage セキュリティ ガイド](https://docs.microsoft.com/azure/storage/blobs/security-recommendations)」で入手してください。

[Storage Service Encryption](https://docs.microsoft.com/azure/storage/storage-service-encryption) は、保存時の暗号化を提供し、データを保護して組織のセキュリティとコンプライアンス コミットメントを満たします。 Storage Service Encryption は、すべての Azure リージョン内のすべてのマネージド ディスク、スナップショット、およびイメージに対して既定で有効になっています。 2017 年 6 月 10 日から、新しいマネージド ディスク、スナップショット、イメージ、および既存のマネージド ディスクに書き込まれた新しいデータはすべて、Microsoft によって管理されるキーを使用して保存時に自動的に暗号化されます。 詳細については、[マネージド ディスクの FAQ](https://docs.microsoft.com/azure/virtual-machines/windows/faq-for-disks#managed-disks-and-storage-service-encryption) に関するページを参照してください。

Azure Disk Encryption を使用すると、[Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault) に格納されているキーを使用して、OS として IaaS VM に接続されたマネージド ディスクとデータ ディスクを保存時および転送中に暗号化できます。 Windows の場合、ドライブは業界標準の [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) 暗号化テクノロジを使用して暗号化されます。 Linux の場合、ディスクは [dm-crypt](https://wikipedia.org/wiki/Dm-crypt) サブシステムを使用して暗号化されます。 暗号化プロセスは Azure Key Vault と統合されているので、ディスクの暗号化キーを制御および管理できます。 詳細については、「[Windows および Linux IaaS VM の Azure Disk Encryption](https://docs.microsoft.com/azure/security/fundamentals/azure-disk-encryption-vms-vmss)」をご覧ください。

## <a name="regional-availability"></a>リージョン別の提供状況

Azure を使用すると、顧客やパートナーが "_どのような場所にいても_"、そこに到達するために必要な規模でサービスを提供できます。 [マネージド ディスク](https://azure.microsoft.com/global-infrastructure/services/?products=managed-disks)と [Azure Storage](https://azure.microsoft.com/global-infrastructure/services/?products=storage) のリージョン別の提供状況のページには、これらのサービスが使用可能なリージョンが表示されています。 あるサービスのリージョン別の提供状況を事前に確認しておくと、ワークロードや顧客のニーズに関する適切な意思決定を行うために役立ちます。

マネージド ディスクは、Premium SSD と Standard SSD のオファリングが存在するすべての Azure リージョンで使用できます。 Ultra SSD は現在パブリック プレビュー段階ですが、1 つの可用性ゾーン (米国東部 2 リージョン) でのみ提供されます。 Ultra SSD を必要とするミッション クリティカルな最上層のワークロードを計画する場合は、リージョン別の提供状況を確認してください。

ホットおよびクール BLOB ストレージ、Data Lake Storage Gen2、および Azure Files ストレージは、すべての Azure リージョンで使用できます。 アーカイブ BLOB ストレージ、Premium ファイル共有、および Premium ブロック BLOB ストレージは、特定のリージョンに制限されています。 リージョンのページを参照して、リージョン別の提供状況の最新の状態を確認することをお勧めします。

Azure のグローバル インフラストラクチャの詳細については、[Azure リージョンのページ](https://azure.microsoft.com/global-infrastructure/regions)を参照してください。 また、各 Azure リージョンで使用可能な製品に関する具体的な詳細について、「[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/?products=storage)」のページを参照することもできます。

## <a name="data-residency-and-compliance-requirements"></a>データの保存場所とコンプライアンスの要件

多くの場合は、データ ストレージに関連した法律上および契約上の要件がワークロードに適用されます。 これらの要件は、組織の場所、データ ストアをホストしている物理的な資産の管轄区域、および該当する事業部門によって異なることがあります。 考慮すべきデータに関する義務の要素には、データ分類、データの場所、および共同責任モデルに基づくデータ保護に関するそれぞれの責任が含まれます。 これらの要件の理解に役立つ情報として、ホワイト ペーパー「[Azure を使用して基準に準拠しているデータの保存場所とセキュリティを実現する](https://azure.microsoft.com/resources/achieving-compliant-data-residency-and-security-with-azure)」を参照してください。

コンプライアンスの取り組みの一環として、データベース リソースが物理的に配置されている場所の制御が必要になる場合があります。 Azure リージョンは地域と呼ばれるグループに編成されています。 [Azure の地域](https://azure.microsoft.com/global-infrastructure/geographies)では、データの保存場所、主権、コンプライアンス、回復性に関する要件が地域的および政治的な境界内で確実に遵守されます。 ワークロードがデータ主権やその他のコンプライアンス要件の影響を受ける場合は、要件に準拠している Azure 地域のリージョンにストレージ リソースをデプロイする必要があります。
