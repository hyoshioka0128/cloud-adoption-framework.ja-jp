---
title: Moodle 移行のリソース
description: Moodle 移行のリソースについて説明します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 11/06/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: plan
ms.openlocfilehash: a4b57c31e1b8d3fd489498eb36cf36f259c8e560
ms.sourcegitcommit: a7eb2f6c4465527cca2d479edbfc9d93d1e44bf1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2020
ms.locfileid: "94714911"
---
# <a name="moodle-migration-resources"></a>Moodle 移行のリソース

Azure Resource Manager テンプレートを使用すると、Azure 内に次のリソースが作成されます。

- **ネットワーク テンプレート:** ネットワーク テンプレートによって、仮想ネットワーク、ネットワーク セキュリティ グループ、ネットワーク インターフェイス、サブネット、パブリック IP アドレス、Azure Load Balancer/Application Gateway、Redis キャッシュなどが作成されます。

また、ネットワーク テンプレートは、名前、apiVersion、場所、DNS サーバー名という文字列を含む仮想ネットワークを作成します。 `AddressSpace` には、サブネットで使用できる IP アドレスの範囲が含まれています。

- **仮想ネットワーク:** Azure Virtual Network は、クラウド内のユーザー独自のネットワークを表したものです。 これは、特にサブスクリプション専用に Azure クラウドを論理的に分離したものです。 仮想ネットワークを作成すると、ご使用のサービスとその内部の仮想マシンは、クラウド内で直接かつ安全に通信することができます。 詳細については、 [Azure Virtual Network](/azure/virtual-network/virtual-networks-overview)に関するページを参照してください。

- **ネットワーク セキュリティ グループ:** ネットワーク セキュリティ グループ (NSG) は、Azure Virtual Network に接続されているリソースへのネットワーク トラフィックを許可または拒否するセキュリティ規則の一覧を含むネットワーク フィルター (ファイアウォール) です。 詳細については、「[ネットワーク セキュリティ グループ](/azure/virtual-network/network-security-groups-overview)」を参照してください。

- **ネットワーク インターフェイス:** ネットワーク インターフェイスは、Azure Virtual Machine がインターネット、Azure、およびオンプレミスのリソースと通信できるようにします。 詳細については、[ネットワーク インターフェイス](/azure/virtual-network/virtual-network-network-interface)に関するページを参照してください。

- **サブネット:** サブネット (サブネットワーク) は、大規模なネットワーク内の小規模なネットワークです。 既定では、サブネット内の IP は、仮想ネットワーク内の他のすべての IP と通信できます。 詳細については、[サブネット](/azure/virtual-network/virtual-network-manage-subnet)に関するページを参照してください。

- **パブリック IP:** パブリック IP アドレスは、Azure リソースをインターネットに伝達するために使用されます。 このアドレスは、Azure リソース専用です。 詳細については、[パブリック IP](/azure/virtual-network/public-ip-addresses#:~:text=Public%20IP%20addresses%20enable%20Azure,IP%20assigned%20can%20communicate%20outbound) に関するページを参照してください。

- **Azure Load Balancer:** サーバー ファーム内の複数のサーバー間でのネットワークまたはアプリケーションのトラフィックの効率的な分散。 オンラインのサーバーにのみ要求を送信することによって、高可用性と信頼性を確保します。 詳細については、[Azure Load Balancer](/azure/virtual-machines/windows/tutorial-load-balancer#:~:text=An%20Azure%20load%20balancer%20is,traffic%20to%20an%20operational%20VM) に関するページを参照してください。

4 つの定義済みテンプレートのいずれでも Azure Load Balancer がデプロイされます。 完全に構成可能なデプロイでは、ユーザーは Load Balancer ではなく Azure Application Gateway を選択できます。

- **Azure Application Gateway:** Web アプリケーションへのトラフィックを管理できるようにする Web トラフィックの Azure Load Balancer です。 Application Gateway では、URI パスやホスト ヘッダーのような、HTTP 要求の追加属性に基づいてルーティングを決定できます。 詳細については、[Azure Application Gateway](/azure/application-gateway/overview) に関するページを参照してください。

- **Redis キャッシュ:** Azure Cache for Redis は、オープンソース ソフトウェア Redis を基にしたインメモリ データ ストアを提供します。 Redis は、バックエンド データを大量に格納するアプリケーションのパフォーマンスとスケーラビリティを高めます。 サーバーのメモリ内にアクセス頻度の高いデータを維持し、このデータを高速に読み書きできるようにすることで、大量のアプリケーション要求を処理することができます。 詳細については、[Redis キャッシュ](/azure/azure-cache-for-redis/cache-overview)に関するページを参照してください。

- **ストレージ テンプレート:** ストレージ アカウント テンプレートでは、種類が FileStorage で Premium のローカル冗長ストレージ (LRS) レプリケーション (1 テラバイト (TB)) を使用してストレージ アカウントを作成します。 定義済みのテンプレートに従って、Azure Files を使用するストレージ アカウントではファイル共有が作成されます。

Azure ストレージ アカウントには、BLOB、ファイル、キュー、テーブル、およびディスクが含まれており、これらはすべて Azure Storage データ オブジェクトです。 このストレージ アカウントでは、世界中のどこからでも HTTP または HTTPS 経由でアクセスできる Azure Storage データ用の一意の名前空間が提供されます。 Azure ストレージ アカウントの種類は、汎用 v1、汎用 v2、BlockBlobStorage、FileStorage、および Blob ストレージです。 レプリケーションの種類は、LRS とゾーン冗長または geo 冗長ストレージです。 パフォーマンスの種類は Standard と Premium で、個々のストレージ アカウントは、他の Azure サービスと同様に最大 500 TB のデータを格納できます。

次のストレージ アカウントの種類には、Azure Resource Manager テンプレートのサポートが含まれています。

- NFS:ネットワーク ファイル システム (NFS) を使用すると、リモート ホストはネットワーク経由でファイル システムをマウントし、ローカルにマウントされている場合と同じようにこれらのファイル システムとやり取りできます。 これにより、システム管理者は、ネットワーク上の集中管理されたサーバーにリソースを統合することができます。 詳細については、[NFS](/windows-server/storage/nfs/nfs-overview) に関するページを参照してください。

- GluserFS:複数のペタバイト単位のデータを格納するために、構成要素方式でスケールアウトできるオープンソースの分散ファイル システム。 詳細については、[Gluster FS](/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs) に関するページを参照してください。

- Azure Files:パフォーマンスと互換性のためにオンプレミスでのキャッシュも可能な、セキュリティで保護された SMB ベースのフル マネージド型のクラウド ファイル共有を提供する唯一のパブリック クラウド ファイル ストレージ。 詳細については、[Azure Files](/azure/storage/files/storage-files-introduction) に関するページを参照してください。 NFS と glusterFS の場合、レプリケーションは Standard LRS であり、ストレージの種類は汎用 v1 です。 Azure Files の場合、レプリケーションは Premium ローカル冗長ストレージ (LRS) であり、種類は FileStorage です。

これらのストレージ メカニズムは選択されたデプロイによって異なります。 NFS と glusterFS ではコンテナーが作成され、Azure Files ではファイル共有が作成されます。 最小および小規模から中規模の場合、テンプレートで NFS がサポートされ、大規模および最大の場合、テンプレートで Azure Files がサポートされます。 コンテナーとファイル共有にアクセスするには、ポータルに移動し、リソース グループ内のストレージ アカウントを選択します。

![ストレージ アカウントです。](images/storage-account.png)

ストレージ アカウントの詳細については、[ストレージ アカウント](/azure/storage/common/storage-account-overview)に関するページを参照してください。

- **データベース テンプレート:** データベース テンプレートでは [Azure Database for MySQL サーバー](/azure/mysql/)が作成されます。 Azure Database for MySQL サーバーは簡単にセットアップ、管理、およびスケーリングできます。 これにより、定期的な更新、バックアップ、セキュリティなど、インフラストラクチャとデータベース サーバーの管理とメンテナンスが自動化されます。 バージョン 5.6、5.7、8.0 など、MySQL の最新のコミュニティ エディションを使用してビルドします。 作成されたデータベース サーバーにアクセスするには、デプロイ時に提供された **リソース グループ** に移動し、**Azure Database for MySQL サーバー** に移動します。 データベース サーバーには、サーバー名、サーバー管理者のログイン名、MySQL のバージョン、およびパフォーマンス構成が含まれます。

- **仮想マシン テンプレート:** このテンプレートでは、仮想マシンがコントローラー仮想マシンとして区別されます。 コントローラー仮想マシンのオペレーティング システムは Ubuntu 18.04 です。

- **仮想マシン拡張機能:** 仮想マシン拡張機能は、[Azure Virtual Machines](/azure/virtual-machines/extensions/overview) でのデプロイ後の構成と自動タスクを提供する複数の小さなアプリケーションです。 仮想マシン拡張機能では、コントローラー仮想マシンに Moodle をインストールしてログ ファイルをキャプチャするシェル スクリプト ファイルが実行されます。 ログファイル `stderr` と `stdout` は `/var/lib/waagent/custom-script/download/0/` に作成され、ユーザーはそれらをルート ユーザーとして表示できます。

- **スケール セット テンプレート:** このテンプレートでは[仮想マシン スケール セット](/azure/virtual-machine-scale-sets/overview)が作成されます。 仮想マシン スケール セットを使用すると、自動スケールの仮想マシンのセットをデプロイおよび管理できます。 スケール セット内の仮想マシンの数を手動で拡張したり、CPU などのリソースの使用率、メモリの需要、またはネットワーク トラフィックに基づいて自動的にスケールする規則を定義したりすることができます。 仮想マシン インスタンスの自動スケールは [CPU 使用率](/visualstudio/profiling/average-cpu-utilization)に依存します。 インスタンスをスケールアップすると、仮想マシンがデプロイされ、シェル スクリプトが実行されて Moodle の前提条件および設定用 Cron ジョブをインストールします。 仮想マシン インスタンスにはプライベート IP があります。 プライベート IP を使用してスケール セット上の仮想マシンに接続するには、「[仮想ネットワーク ゲートウェイを作成してプライベート IP 経由で接続する方法](./vpn-gateway.md)」の手順に従ってください。

## <a name="next-steps"></a>次のステップ

Moodle の移行プロセスの詳細については、「[仮想ネットワーク ゲートウェイを作成してプライベート IP 経由で接続する方法](./vpn-gateway.md)」を続行してください。
