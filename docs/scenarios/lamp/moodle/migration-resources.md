---
title: Moodle 移行のリソース
description: Azure 仮想ネットワーク、ネットワーク セキュリティ グループ、サブネットなど、Azure での Moodle 移行によって作成されるリソースについて説明します。
author: UmakanthOS
ms.author: brblanch
ms.date: 11/30/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: plan
ms.custom: think-tank
ms.openlocfilehash: 677b8c4a582688599a868ae5af9b9f43d8b0cdca
ms.sourcegitcommit: 54f01dd0eafa23c532e54c821954ba682357f686
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/13/2021
ms.locfileid: "98175136"
---
# <a name="moodle-migration-resources"></a>Moodle 移行のリソース

Azure Resource Manager (ARM) テンプレートを使用して Moodle を移行すると、デプロイによって Azure 内にリソースが作成されます。 このデプロイ プロセスの一環として、子テンプレートを使用して追加のデプロイが自動的に実行されます。 以下のセクションでは、これらのデプロイと、そのデプロイによって作成されるリソースについて説明します。

## <a name="network-template"></a>ネットワーク テンプレート

ネットワーク テンプレートのデプロイによって、次のようなリソースが作成されます。

- [Azure Virtual Network](/azure/virtual-network/virtual-networks-overview): クラウド内のユーザー独自のネットワークの表現。 仮想ネットワークは、ご使用のサブスクリプション専用の Azure クラウドを論理的に分離したものです。 仮想ネットワークを作成すると、ご使用のサービスとその内部の仮想マシンは、クラウド内で直接かつ安全に通信することができます。 ネットワーク テンプレートによって作成される仮想ネットワークには、仮想ネットワーク名、API バージョン、場所、DNS サーバー名、アドレス空間が含まれます。 アドレス空間には、サブネットが使用できる IP アドレスの範囲が含まれています。

- [ネットワーク セキュリティ グループ (NSG)](/azure/virtual-network/network-security-groups-overview):セキュリティ規則の一覧を含むネットワーク フィルター (ファイアウォール)。 これらの規則によって、仮想ネットワークに接続されているリソースへのネットワーク トラフィックが許可または拒否されます。

- [ネットワーク インターフェイス](/azure/virtual-network/virtual-network-network-interface): Azure 仮想マシンがインターネット、Azure、およびオンプレミスのリソースと通信するときに使用できるインターフェイス。

- [サブネット](/azure/virtual-network/virtual-network-manage-subnet):大規模なネットワーク内の小規模なネットワーク。 サブネットはサブネットワークとも呼ばれます。 既定では、サブネット内の IP アドレスは、仮想ネットワーク内の他のすべての IP アドレスと通信できます。

- [[パブリック IP アドレス]](/azure/virtual-network/public-ip-addresses#:~:text=public%20ip%20addresses%20enable%20azure,IP%20assigned%20can%20communicate%20outbound): Azure リソースがインターネット通信に使用する IP アドレス。 このアドレスは、Azure リソース専用です。

- [Azure Load Balancer](/azure/virtual-machines/windows/tutorial-load-balancer#:~:text=an%20azure%20load%20balancer%20is,traffic%20to%20an%20operational%20vm):サーバー ファーム内の複数のサーバーにわたり、ネットワークまたはアプリケーションのトラフィックを効率的に分散するロード バランサー。 Load Balancer は、オンラインのサーバーにのみ要求を送信することで、高可用性と信頼性を確保します。

- [Azure Application Gateway](/azure/application-gateway/overview):Load Balancer の代替手段。 4 つすべての定義済み ARM テンプレートによって Load Balancer がデプロイされます。 ARM テンプレートの代わりに完全に構成可能なデプロイを使用すると、Load Balancer ではなく Application Gateway を選択できます。 Application Gateway は、Web アプリケーションへのトラフィック管理に使用できる Web トラフィック ロード バランサーです。 Application Gateway によるルーティングの決定は、URI パス、ホスト ヘッダーのような HTTP 要求の追加属性に基づいて行うことができます。

- [Azure Cache for Redis](/azure/azure-cache-for-redis/cache-overview):オープンソース ソフトウェア Redis に基づくインメモリ データ ストア。 Redis は、バックエンド データを大量に格納するアプリケーションのパフォーマンスとスケーラビリティを高めます。 大量のアプリケーション要求を処理するために、サーバーのメモリ内にアクセス頻度の高いデータが維持されます。 このデータは、高速で読み書きすることができます。

## <a name="storage-template"></a>ストレージ テンプレート

ストレージ アカウント テンプレートのデプロイにより、種類が FileStorage の Azure ストレージ アカウントが作成されます。 アカウントには、Premium パフォーマンス、ローカル冗長ストレージ (LRS) レプリケーション、および 1 テラバイト (TB) のストレージが含まれます。 定義済みテンプレートが構成されるため、Azure Files を使用するストレージ アカウントによってファイル共有が作成されます。

[Azure ストレージ アカウント](/azure/storage/common/storage-account-overview)には、BLOB、ファイル、キュー、テーブル、ディスクなどの Azure Storage データ オブジェクトが含まれています。 このストレージ アカウントでは、世界中のどこからでも HTTP または HTTPS 経由でアクセスできる Azure Storage データ用の一意の名前空間が提供されます。 使用可能な Azure ストレージ アカウントの種類は、汎用 v1、汎用 v2、BlockBlobStorage、FileStorage、および Blob ストレージです。 レプリケーションの種類は、geo 冗長ストレージまたは LRS、およびゾーン冗長または geo 冗長ストレージです。 パフォーマンスの種類は Standard と Premium で、個々のストレージ アカウントは、他の Azure サービスと同様に最大 500 TB のデータを格納できます。

ARM テンプレートは、次のストレージ アカウントの種類をサポートしています。

- [Network File System (NFS)](/windows-server/storage/nfs/nfs-overview):リモート ホストがネットワーク経由でファイル システムをマウントするときに使用できるアカウントの種類。 リモート ホストは、ローカルにマウントされている場合と同じように、これらのファイル システムとやり取りできます。 この設計により、システム管理者は、ネットワーク上の集中管理されているサーバーにリソースを統合することができます。

- [GlusterFS](/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs): 複数のペタバイト単位のデータを格納するために、構成要素方式でスケールアウトできるオープンソースの分散ファイル システム。

- [Azure ファイル](/azure/storage/files/storage-files-introduction):パフォーマンスと互換性のためにオンプレミスでのキャッシュも可能な、セキュリティで保護された SMB ベースのフル マネージド型のクラウド ファイル共有を提供する唯一のパブリック クラウド ファイル ストレージ。 NFS および GlusterFS の場合、レプリケーションは Standard LRS で、ストレージの種類は汎用 v1 です。 Azure Files の場合、レプリケーションは Premium LRS で、種類は FileStorage です。

これらのストレージ メカニズムは、選択するデプロイによって異なります。 NFS と GlusterFS によりコンテナーが作成され、Azure Files によりファイル共有が作成されます。 最小および小規模から中規模の場合、テンプレートにより NFS がサポートされます。 大規模および最大規模の場合は、テンプレートにより Azure Files がサポートされます。 コンテナーとファイル共有にアクセスするには、Azure portal に移動し、リソース グループ内のストレージ アカウントを選択します。

![Azure portal のスクリーンショット。 ストレージ アカウントのページが表示され、コンテナーとファイル共有にアクセスするためのボタンを使用できます。](./images/storage-account.png)

## <a name="database-template"></a> データベース テンプレート

データベース テンプレートのデプロイにより、[Azure Database for MySQL](/azure/mysql/) サーバーが作成されます。 Azure Database for MySQL は簡単にセットアップ、管理、およびスケーリングできます。 これにより、定期的な更新、バックアップ、セキュリティなど、インフラストラクチャとデータベース サーバーの管理とメンテナンスが自動化されます。 Azure Database for MySQL は、バージョン 5.6、5.7、8.0 など、MySQL の最新のコミュニティ エディションを使用してビルドされます。 テンプレートによって作成されたデータベース サーバーにアクセスするには、Azure portal に移動し、デプロイ プロセスによって提供されたリソース グループを開きます。 次に、**Azure Database for MySQL サーバー** に移動します。 データベース サーバーには、テンプレートによってサーバー名、サーバー管理者のログイン名、MySQL のバージョン、およびパフォーマンス構成が設定されます。

## <a name="virtual-machine-template"></a>仮想マシン テンプレート

仮想マシン テンプレートのデプロイによって、仮想マシンがコントローラー仮想マシンとして指定されます。 コントローラー仮想マシンのオペレーティング システムは Ubuntu 18.04 です。

仮想マシン拡張機能は、[Azure Virtual Machines](/azure/virtual-machines/extensions/overview) でのデプロイ後の構成と自動タスクを提供する複数の小さなアプリケーションです。 仮想マシン拡張機能を使用すると、シェル スクリプトが実行されます。このシェル スクリプトによって、コントローラー仮想マシンに Moodle がインストールされ、ログ ファイルがキャプチャされます。 これにより `stderr` ログ ファイルと `stdout` ログ ファイルが `/var/lib/waagent/custom-script/download/0/` フォルダーに作成されます。 これらのファイルは、ルート ユーザーとして表示できます。

## <a name="scale-set-template"></a>スケール セット テンプレート

スケール セット テンプレートのデプロイによって、[仮想マシン スケール セット](/azure/virtual-machine-scale-sets/overview)が作成されます。 仮想マシン スケール セットを使用すると、自動スケールの仮想マシンのセットをデプロイおよび管理できます。 スケール セット内の仮想マシンの数を手動で拡張したり、[CPU](/visualstudio/profiling/average-cpu-utilization) などのリソースの使用率、メモリの需要、またはネットワーク トラフィックに基づいて自動的にスケールする規則を定義したりすることができます。 インスタンスがスケールアップすると、これにより仮想マシンがデプロイされます。 その後、シェル スクリプトが実行され、このシェル スクリプトによって、Moodle の前提条件がインストールされ、cron ジョブが設定されます。 スケール セットの仮想マシンには、プライベート IP アドレスがあります。 スケールセット内の仮想マシン インスタンスの表示に関する詳細と、これらのインスタンスにアクセスする方法については、[仮想マシン スケール セットのドキュメント](/azure/virtual-machine-scale-sets/tutorial-create-and-manage-cli#view-the-vm-instances-in-a-scale-set)を参照してください。

## <a name="next-steps"></a>次の手順

Moodle 移行プロセスの次の手順として、[Moodle 手動移行手順](migration-start.md)を続けます。
