---
title: オンプレミスの VMware インフラストラクチャを Azure に移行する
description: Contoso がオンプレミスの VMware VM を Azure に移行する方法について説明します。
author: deltadan
ms.author: abuck
ms.date: 07/01/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: migrate
services: azure-migrate
ms.openlocfilehash: 12d83670b5d2c18a14229c2b14373da70c6b84d9
ms.sourcegitcommit: bcc73d194c6d00c16ae2e3c7fb2453ac7dbf2526
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2020
ms.locfileid: "86199450"
---
<!-- docsTest:ignore "Bulk Migration" "Cold Migration" -->

# <a name="move-on-premises-vmware-infrastructure-to-azure"></a>オンプレミスの VMware インフラストラクチャを Azure に移行する

Contoso には、VMware 仮想マシン (VM) をオンプレミスのデータセンターから Azure に移行する際に使用できるオプションがいくつかあります。

| 移行オプション | 結果 |
| --- | --- |
| [Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) | オンプレミスの VM を[評価](https://docs.microsoft.com/azure/migrate/tutorial-assess-vmware)し、[移行](https://docs.microsoft.com/azure/migrate/tutorial-migrate-vmware)する。 <br><br> Azure IaaS を使用してワークロードを実行する。 <br><br> [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)を使用して VM を管理する。 |
| [Azure VMware Solution](https://azure.microsoft.com/overview/azure-vmware) | VMware HCX または vMotion を使用してオンプレミスの VM を移動する。 <br><br> Azure ベアメタル ハードウェアでネイティブの VMware ワークロードを実行する。 <br><br> vSphere を使用して VM を管理する。 |

Azure VMware Solution は、VMware vCenter およびワークロードを移行するために VMware でサポートされるその他のツールにネイティブ アクセスできるプライベート クラウドを Azure 内に作成するために使用されます。 Contoso は、Azure VMware Solution が VMware によってサポートされる Microsoft のファースト パーティ サービスであることがわかっているので、自信をもってこれを使用することができます。

> [!NOTE]
> この記事では、Azure VMware Solution (AVS) を使用してオンプレミスの VMware 仮想マシンを Azure に移行する方法を重点的に取り上げます。

## <a name="business-drivers"></a>ビジネス ドライバー

Contoso IT チームは、ビジネス パートナーと緊密に連携して、Azure への VMware の移行に関するビジネス ドライバーを定義します。 これらのドライバーには、次のようなものがあります。

- **データセンターの廃止またはシャットダウン:** 既存のデータセンターを統合または廃止する場合、VMware ベースのワークロードをシームレスに移動します。
- **ディザスター リカバリーと事業継続:** Azure にデプロイされた VMware スタックを、オンプレミスのデータセンター インフラストラクチャ用のプライマリまたはセカンダリ オンデマンド ディザスター リカバリー サイトとして使用します。
- **アプリケーションの最新化:** Azure エコシステムを利用して Contoso のアプリケーションを最新化するため、VMware ベースの環境を再構築する必要がありません。
- **DevOps の実装:** Azure DevOps ツール チェーンを VMware 環境に統合し、独自のペースでアプリケーションを最新化できます。
- **運用の継続性の確保:** ハイパーバイザー変換とアプリケーションのリファクタリングを回避しながら、vSphere ベースのアプリケーションを再デプロイします。 Windows および SQL Server を実行するレガシ アプリケーションのサポートを拡張します。

## <a name="vmware-on-premises-to-vmware-in-the-cloud-goals"></a>オンプレミスの VMware からクラウド内の VMware への移行の目標

Contoso は、ビジネス ドライバーを念頭に置いて、この移行の目標を特定しました。

- チームが使い慣れた VMware ツールで既存の環境の管理を継続しながら、ネイティブの Azure サービスでアプリケーションを最新化する。
- Contoso の VMware ベースのワークロードをデータセンターから Azure にシームレスに移行し、VMware 環境を Azure と統合する。
- 移行後、Azure 内のアプリケーションは、現在の VMware における場合と同じパフォーマンスを発揮できる必要があります。 このアプリケーションは、オンプレミスにあるときと同様に、クラウド内でも非常に重要であり続ける。

これらの目標は、AVS を使用するという決定を裏付け、それが Contoso にとって最善の移行方法であることを立証します。

## <a name="benefits-of-running-vmware-workloads-in-azure"></a>Azure で VMware ワークロードを実行する利点

Azure VMware Solution (AVS) を使用すると、Contoso は、VMware 環境と Azure 間で、共通のオペレーティング フレームワークを使用して、アプリケーションをシームレスに実行および管理し、セキュリティで保護することができるようになりました。

Contoso は、Azure のスケール、パフォーマンス、イノベーションを利用しながら、既存の VMware への投資、スキル、ツール (VMware vSphere、vSAN、vCenter など) を活用します。 その他の利点としては次のものが考えられます。

- クラウドで VMware インフラストラクチャを数分でプロビジョニングし、独自のペースでアプリケーションを最新化できます。
- 専用の分離された高パフォーマンスのインフラストラクチャと、Azure 固有の製品およびサービスで、VMware アプリケーションを強化できます。
- アプリケーションをリファクタリングせずに、オンプレミスの VM を Azure に移動または拡張できます。
- グローバルな Azure インフラストラクチャで、VMware ワークロードのスケーリング、自動化、迅速なプロビジョニングを実現できます。
- Azure VMware Solution は、Microsoft から提供され、VMware によって検証済みで、Azure インフラストラクチャ上で実行されます。

## <a name="solutions-design"></a>ソリューションの設計

Contoso は、目標と要件を特定した後、デプロイ ソリューションを設計してレビューし、移行プロセスを特定します。

### <a name="current-architecture"></a>現在のアーキテクチャ

- VM は、[vSphere](https://www.vmware.com/products/vsphere.html) によって管理されるオンプレミスのデータセンターにデプロイされています。
- ワークロードは、[vCenter](https://www.vmware.com/products/vcenter-server.html)、[vSAN](https://www.vmware.com/products/vsan.html)、[NSX](https://www.vmware.com/products/nsx.html) で管理される VMware ESXi ホスト クラスターにデプロイされています。

### <a name="proposed-architecture"></a>提案されたアーキテクチャ

- [AVS プライベート クラウド](https://docs.microsoft.com/azure/azure-vmware/concepts-private-clouds-clusters) を `West US`Azure リージョンにデプロイする。
- 仮想ネットワークと、Global Reach を有効にした [ExpressRoute](https://docs.microsoft.com/azure/azure-vmware/concepts-networking) を使用して、`West US` で実行されている AVS にオンプレミスのデータセンターを接続する。
- [VMware Hybrid Cloud Extension (HCX)](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html) を使用して、VM を専用の Azure VMware Solution に移行する。

![提案されたアーキテクチャ](./media/contoso-migration-vmware-to-azure/on-premises-stretched-network-expressroute.png)

## <a name="solution-review"></a>ソリューションのレビュー

Contoso は、長所と短所の一覧をまとめて、提案されたデザインを評価します。

| 考慮事項 | 詳細 |
| --- | --- |
| **長所** | ハイ パフォーマンスのベアメタル VMware インフラストラクチャ。 インフラストラクチャは、完全に Contoso 専用となり、他のお客様のインフラストラクチャから物理的に分離されます。 <br><br> Contoso は VMware を使用する再ホストを使用することになるので、特別な構成や移行の複雑さはありません。 <br><br> Contoso は、[Azure ハイブリッド特典](https://azure.microsoft.com/pricing/hybrid-benefit/)と、レガシ Windows および SQL プラットフォーム用の[拡張セキュリティ更新プログラム](https://www.microsoft.com/cloud-platform/windows-server-2008)を使用して、ソフトウェア アシュランスへの投資を活用できます。 <br><br> Contoso は、Azure で引き続きアプリケーションの VM を完全に制御できます。 <br><br> |
| **短所** | Contoso は今後も、Azure App Service や Azure SQL Database などのマネージド サービスにアプリケーションを移行するのではなく、VMware VM としてアプリケーションをサポートしていく必要があります。 <br><br> Azure VMware Solution のプロビジョニングと価格は、Azure IaaS 内の個々の VM 単位ではなく大きなノード単位であり、少なくとも 3 ノードが必要です。 Azure の他のサービスのオンデマンドの性質から利点を得るのではなく、現在オンプレミスで計画されているとおりの容量ニーズを計画する必要があります。 |

> [!NOTE]
> Azure VMware Solution の価格の詳細については、[こちら](https://azure.microsoft.com/pricing/details/azure-vmware/)を参照してください。

## <a name="migration-process"></a>移行プロセス

Contoso は、VMware HCX ツールを使用して VM を AVS に移行します。 VM は、AVS プライベート クラウドで実行されます。  [VMware HCX の移行の種類](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-8A31731C-AA28-4714-9C23-D9E924DBB666.html)としては、一括移行とコールド移行があり、さらに、ワークロードを実行中に、vMotion または Replication Assisted vMotion (RAV) を使用してライブ移行を行うこともできます。

- Contoso は、Azure と ExpressRoute でネットワークを計画します。
- Contoso は、Azure portal を使用して AVS プライベート クラウドを作成します。
- この後、ExpressRoute 回線など、ネットワークが構成されます。
- 次に、Contoso は、オンプレミスの vSphere 環境を AVS プライベート クラウドに接続する HCX コンポーネントを構成します。
- この後、VMware HCX を使用して、VM がレプリケートされ、Azure に移動されます。

 ![移行プロセス](./media/contoso-migration-vmware-to-azure/on-premises-vmware-hcx-azure.png)

## <a name="scenarios-steps"></a>シナリオの手順

1. ネットワークの計画
1. AVS プライベート クラウドを作成する
1. ネットワークを構成する
1. HCX を使用して VM を移行する

### <a name="step-1-network-planning"></a>手順 1:ネットワークの計画

Contoso は、Azure Virtual Network およびオンプレミスと Azure 間の接続を含むネットワークを計画する必要があります。 Contoso は、AVS プライベート クラウドへの接続と共に、オンプレミスの環境と Azure ベースの環境間の高速接続を提供する必要があります。

この接続は Azure ExpressRoute を通じて提供されます。また、サービスを有効にするために、特定のネットワーク アドレス範囲とファイアウォール ポートが必要になります。 この高帯域幅で低待機時間の接続によって、Contoso のユーザーは、AVS プライベート クラウド環境から、Azure サブスクリプションで実行されているサービスにアクセスできます。

Contoso は、[仮想ネットワーク](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm)用に、重複していないアドレス空間を含む IP アドレス スキームを計画する必要があります。 [ExpressRoute ゲートウェイ](https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways)のゲートウェイ サブネットを含める必要があります。

AVS プライベート クラウドは、Azure ExpressRoute 接続を使用して Contoso の Azure Virtual Network に接続されます。 ExpressRoute Global Reach を有効にして、オンプレミスから、AVS プライベート クラウド上で実行される VM への[直接接続](https://docs.microsoft.com/azure/azure-vmware/concepts-networking#on-premises-interconnectivity)を許可します。 Global Reach を有効にするには、ExpressRoute Premium SKU が必要です。

![ExpressRoute Global Reach と AVS](./media/contoso-migration-vmware-to-azure/adjacency-overview-drawing-double.png)

AVS プライベート クラウドでは、サブネット用に少なくとも `/22` の CIDR ネットワーク アドレス ブロックが必要です。 オンプレミス環境と仮想ネットワークに接続するには、これが重複していないネットワーク アドレス ブロックである必要があります。

>[!NOTE]
> AVS のネットワーク計画については、[チュートリアル](https://docs.microsoft.com/azure/azure-vmware/tutorial-network-checklist/)を参照してください。

### <a name="step-2-create-an-avs-private-cloud"></a>手順 2:AVS プライベート クラウドを作成する

ネットワークと IP アドレスの計画が完了したら、Contoso は、次に、West US Azure リージョンでの AVS サービスのプロビジョニングに集中します。 AVS により、Contoso は、Azure 内に vSphere クラスターをデプロイできます。

AVS プライベート クラウドは、分離された VMware ソフトウェア定義データセンターであり、ESXi ホスト、vCenter、vSAN、NSX をサポートします。 スタックは、Azure リージョン内の分離された専用のベアメタル ハードウェア ノードで実行されます。 AVS プライベート クラウドの初期デプロイ時の最小ホスト数は 3 つです。 追加のホストは、クラスターあたり最大 16 個まで一度に 1 つずつ追加できます。

>[!NOTE]
> AVS プライベート クラウドの概念の詳細については、[こちら](https://docs.microsoft.com/azure/azure-vmware/concepts-private-clouds-clusters)を参照してください。

AVS プライベート クラウドは、AVS ポータルを介して管理され、 専用の管理ドメインに専用の vCenter サーバーがあります。

>[!NOTE]
> AVS プライベート クラウドの作成については、[チュートリアル](https://docs.microsoft.com/azure/azure-vmware/tutorial-create-private-cloud)を参照してください。

- Contoso は、まず、次のコマンドを使用して、AVS プロバイダーを Azure に登録します。

```bash
az provider register -n Microsoft.AVS --subscription <your subscription ID>
```

- Contoso は、Azure portal を使用して計画のネットワーク情報を入力し、AVS プライベート クラウドを作成します。

![AVS プライベート クラウドを作成する](./media/contoso-migration-vmware-to-azure/create-private-cloud.png)

> [!NOTE]
> この手順には約 2 時間かかります。

- Contoso は、リソース グループに移動して、プライベート クラウドのリソースを選択することにより、AVS プライベート クラウドのデプロイが完了したことを確認します。 デプロイが完了すると、状態が **[成功]** として表示されます。

![プライベート クラウドのデプロイを検証する](./media/contoso-migration-vmware-to-azure/validate-deployment.png)

### <a name="step-3-configure-networking"></a>手順 3:ネットワークを構成する

Azure VMware Solution (AVS) プライベート クラウドには、仮想ネットワークが必要です。 AVS ではプレビュー期間中はオンプレミスの vCenter がサポートされないため、ご使用のオンプレミス環境と統合するための追加の手順が必要になります。 ExpressRoute 回線と仮想ネットワーク ゲートウェイを設定すると、仮想ネットワークが AVS プライベート クラウドに接続されます。

>[!NOTE]
> AVS のネットワークの構成については、[チュートリアル](https://docs.microsoft.com/azure/azure-vmware/tutorial-configure-networking)を参照してください。

- Contoso は、まず、ゲートウェイ サブネットを含む仮想ネットワークを作成します。

> [!IMPORTANT]
> Contoso は、プライベート クラウドを作成したときに使用したアドレス空間と重複**していない**アドレス空間を使用する必要があります。

- 次に、ExpressRoute VPN ゲートウェイを作成します。適切な SKU を選択する必要があります。

![ExpressRoute VPN Gateway](./media/contoso-migration-vmware-to-azure/create-virtual-network-gateway.png)

- ExpressRoute を仮想ネットワークに接続するには、認証キーが必要です。 これは、Azure portal 内の AVS プライベート クラウドのリソースの [接続] 画面に表示されます。

![ER 認証キー](./media/contoso-migration-vmware-to-azure/request-auth-key.png)

- 最後に、AVS プライベート クラウドを仮想ネットワークに接続する VPN ゲートウェイに ExpressRoute を接続します。 これは、Azure で接続を作成することによって実現されます。

![ER を Vnet に接続する](./media/contoso-migration-vmware-to-azure/add-connection.png)

>[!NOTE]
> プライベート クラウドにアクセスし、vSphere に接続する方法については、この[チュートリアル](https://docs.microsoft.com/azure/azure-vmware/tutorial-access-private-cloud)を参照してください。

### <a name="step-4-migrate-using-vmware-hcx"></a>手順 4:VMware HCX を使用して移行する

HCX を使用して VMware VM を Azure に移行するには、Contoso は次の手順の概要に従う必要があります。

- VMware HCX をインストールして構成します。
- HCX を使用して Azure への移行を実行します。

>[!NOTE]
> AVS 用の HCX をインストールする方法については、この[チュートリアル](https://docs.microsoft.com/azure/azure-vmware/hybrid-cloud-extension-installation)を参照してください。

<!-- docsTest:ignore L2 -->

#### <a name="install-and-configure-vmware-hcx-for-public-cloud"></a>パブリック クラウド用の VMware HCX をインストールして構成する

[VMware HCX](https://cloud.vmware.com/vmware-hcx) は、Azure VMware Solution の既定のインストールに含まれる VMware 製品の一部です。 既定では、HCX Advanced がインストールされますが、追加機能としてより多くの機能を含む HCX Enterprise にアップグレードできます。 AVS では、HCX の Cloud Manager コンポーネントが自動化され、カスタマー アクティブ化キーと、オンプレミス側とお客様の vCenter ドメインで構成する必要があるコネクタ HCX アプライアンスへのダウンロード リンクが提供されます。 これらは、AVS クラウド アプライアンスとペアリングされ、お客様は、必要に応じて、移行や L2 ストレッチなどのサービスの利用を開始できます。

- Contoso は、VMware から提供される OVA を使用して HCX をデプロイします。

![HCX OVA](./media/contoso-migration-vmware-to-azure/configure-template.png)

AVS プライベート クラウド用に HCX を構成する方法については、このガイドを参照してください。

>[!NOTE]
> AVS 用の HCX をインストールする方法については、この[チュートリアル](https://docs.microsoft.com/azure/azure-vmware/hybrid-cloud-extension-installation)を参照してください。

- HCX の構成時に、Contoso は、ディザスター リカバリーなどの他のオプション間での移行を有効にすることを選択しました。

![HCX の構成](./media/contoso-migration-vmware-to-azure/hcx-manager.png)

> 注: HCX パブリック クラウド用の HCX インストールのワークフローの詳細については、[こちら](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-FDE5473E-6B71-4A71-85B6-8C9BA2B73686.html)を参照してください。

#### <a name="perform-migrations-to-azure-using-hcx"></a>HCX を使用して Azure への移行を実行する

VMware クラウドと HCX を使用してオンプレミスのデータセンター (ソース) と AVS プライベート クラウド (移行先) の両方を構成したら、Contoso は VM の移行を開始できます。 複数の移行テクノロジを使用すると、VMware HCX 対応のデータセンター間で仮想マシンを移動できます。

- Contoso の HCX アプリケーションがオンラインになり、状態が緑色になります。 これで、HCX を使用して、AVS VM を移行して保護する準備ができました。

![HCX の状態](./media/contoso-migration-vmware-to-azure/appliance-status.png)

#### <a name="vmware-hcx-bulk-migration"></a>VMware HCX 一括移行

この移行方法では、VMware vSphere レプリケーション プロトコルを使用して、仮想マシンを移行先サイトに移動します。

- 一括移行オプションは、仮想マシンを並行して移動するように設計されています。
- この移行の種類は、事前定義されたスケジュールに従って完了するように設定できます。
- フェールオーバーが開始されるまで、仮想マシンはソース サイトで実行されます。 一括移行によるサービスの中断は、再起動に相当します。

#### <a name="vmware-hcx-vmotion-live-migration"></a>VMware HCX vMotion ライブ移行

この方法では、VMware vMotion プロトコルを使用して仮想マシンをリモート サイトに移動します。

- vMotion オプションは、一度に 1 つの仮想マシンだけを移動するように設計されています。
- 仮想マシンの状態が移動されます。 VMware HCX vMotion 中、サービスの中断は発生しません。

#### <a name="vmware-hcx-cold-migration"></a>VMware HCX コールド移行

この移行方法では、VMware NFC プロトコルが使用されます。 これは、ソースの仮想マシンの電源が切断された時点で自動的に選択されます。

#### <a name="vmware-hcx-replication-assisted-vmotion"></a>VMware HCX レプリケーション支援 vMotion

VMware HCX レプリケーション支援 vMotion (RAV) では、VMware HCX 一括移行の利点 (並列操作、回復性、スケジューリング) と VMware HCX vMotion の利点 (ゼロのダウンタイム、仮想マシンの状態移行) を兼ね備えています。

> [!IMPORTANT]
> VMware のテクニカル ドキュメントの「[VMware HCX documentation」 (VMware HCX のドキュメント)](https://docs.vmware.com/en/VMware-HCX/index.html)」および「[Migrating Virtual Machines with VMware HCX」 (VMware HCX による仮想マシンの移行)](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g)を参照してください。
