---
title: オンプレミスの VMware インフラストラクチャを Azure に移行する
description: Contoso という架空の会社がオンプレミスの VMware VM を Azure に移行する方法について説明します。
author: deltadan
ms.author: abuck
ms.date: 07/01/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: migrate
ms.custom: think-tank
ms.openlocfilehash: 66b5a9971af9e39bb9333496f4eff09f5975a3d1
ms.sourcegitcommit: b8f8b7631aabaab28e9705934bf67dad15e3a179
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101787010"
---
<!-- docutune:casing "HCX Bulk Migration" "HCX Cold Migration" -->

# <a name="move-on-premises-vmware-infrastructure-to-azure"></a>オンプレミスの VMware インフラストラクチャを Azure に移行する

Contoso という架空の会社がその VMware 仮想マシン (VM) をオンプレミスのデータセンターから Azure に移行するとき、同社のチームには 2 つの選択肢があります。 この記事では、Contoso が移行方法として選んだ Azure VMware Solution に焦点を絞って説明します。

| 移行オプション | 結果 |
| --- | --- |
| [Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) | <li>オンプレミスの VM を[評価](/azure/migrate/tutorial-assess-vmware-azure-vm)し、[移行](/azure/migrate/tutorial-migrate-vmware)する。 <li>Azure IaaS (サービスとしてのインフラストラクチャ) を使用してワークロードを実行する。 <li>[Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)を使用して VM を管理する。 |
| [Azure VMware Solution](https://azure.microsoft.com/services/azure-vmware/) | <li>VMware Hybrid Cloud Extension (HCX) または vMotion を使用してオンプレミスの VM を移動する。 <li>Azure ベアメタル ハードウェアでネイティブの VMware ワークロードを実行する。 <li>vSphere を使用して VM を管理する。 |

この記事では、Contoso が Azure VMware Solution を使用し、プライベート クラウドを Azure に作成することでワークロードを移行します。VMware vCenter など、VMware でサポートされるツールにネイティブでアクセスすることができます。 Azure VMware Solution は、VMware によってサポートされる、Microsoft のファースト パーティ サービスであることがわかっているので、Contoso は安心してそれを使用することができます。

## <a name="business-drivers"></a>ビジネス ドライバー

Contoso IT チームは、ビジネス パートナーと緊密に連携して、Azure への VMware の移行に関するビジネス ドライバーを定義します。 これらのドライバーには、次のようなものがあります。

- **データセンターの廃止またはシャットダウン:** 既存のデータセンターを統合または廃止する場合、VMware ベースのワークロードをシームレスに移動します。
- **ディザスター リカバリーと事業継続:** Azure にデプロイされた VMware スタックを、オンプレミスのデータセンター インフラストラクチャ用のプライマリまたはセカンダリ オンデマンド ディザスター リカバリー サイトとして使用します。
- **アプリケーションの最新化:** Azure エコシステムを利用して Contoso のアプリケーションを最新化するため、VMware ベースの環境を再構築する必要がありません。
- **DevOps の実装:** Azure DevOps ツール チェーンを VMware 環境に統合し、それぞれのペースでアプリケーションを最新化できます。
- **運用の継続性の確保:** ハイパーバイザー変換とアプリケーションのリファクタリングを回避しながら、vSphere ベースのアプリケーションを再デプロイします。 Windows および SQL Server を実行するレガシ アプリケーションのサポートを拡張します。

## <a name="goals-for-migrating-vmware-on-premises-to-vmware-in-the-cloud"></a>オンプレミスの VMware からクラウド内の VMware への移行の目標

Contoso は、そのビジネス ドライバーを念頭に、この移行についていくつかの目標を設定しました。

- チームが使い慣れた VMware ツールで既存の環境の管理を継続しながら、ネイティブの Azure サービスでアプリケーションを最新化する。
- Contoso の VMware ベースのワークロードをデータセンターから Azure にシームレスに移行し、VMware 環境を Azure と統合する。
- 移行後、Azure 内のアプリケーションは、現在の VMware における場合と同じパフォーマンスを発揮できる必要があります。 このアプリケーションは、オンプレミスにあるときと同様に、クラウド内でも非常に重要であり続ける。

これらの目標が、Azure VMware Solution を使用するという Contoso の決定と、それが最善の移行方法であることの裏付けとなります。

## <a name="benefits-of-running-vmware-workloads-in-azure"></a>Azure で VMware ワークロードを実行する利点

Azure VMware Solution を使用することにより、Contoso は、VMware 環境と Azure との垣根を越え、共通のオペレーティング フレームワークを使用して、アプリケーションをシームレスに実行、管理し、セキュリティで保護できるようになります。

Contoso は、VMware への既存の投資、スキル、ツール (VMware vSphere、vSAN、vCenter など) を活かすことができます。 同時に、Contoso は Azure のスケール、パフォーマンス、イノベーションを得られます。 さらに、次のことが可能となります。

- VMware インフラストラクチャを数分でクラウドに設定できます。
- アプリケーションをそれぞれのペースで最新化できます。
- 専用の分離された高パフォーマンスのインフラストラクチャと、Azure 固有の製品およびサービスで、VMware アプリケーションを強化できます。
- アプリケーションをリファクタリングせずに、オンプレミスの VM を Azure に移動または拡張できます。
- グローバルな Azure インフラストラクチャで、VMware ワークロードのスケーリング、自動化、迅速なプロビジョニングを実現できます。
- Microsoft から提供され、VMware による検証済みの、Azure インフラストラクチャ上で実行されるソリューションの利点を活かすことができます。

## <a name="the-solutions-design"></a>ソリューションの設計

Contoso はその目標と要件を設定した後、デプロイ ソリューションを設計、確認し、移行プロセスを明らかにします。

### <a name="current-architecture"></a>現在のアーキテクチャ

Contoso の現在のアーキテクチャの特徴:

- VM は、[vSphere](https://www.vmware.com/products/vsphere.html) によって管理されるオンプレミスのデータセンターにデプロイされています。
- ワークロードは、[vCenter](https://www.vmware.com/products/vcenter-server.html)、[vSAN](https://www.vmware.com/products/vsan.html)、[NSX](https://www.vmware.com/products/nsx.html) で管理される VMware ESXi ホスト クラスターにデプロイされています。

### <a name="proposed-architecture"></a>提案されたアーキテクチャ

提案されたアーキテクチャを受けて、Contoso は以下を行います。

- [Azure VMware Solution のプライベート クラウド](/azure/azure-vmware/concepts-private-clouds-clusters)を Azure の米国西部リージョンにデプロイする。
- Global Reach を有効にした [ExpressRoute](/azure/azure-vmware/concepts-networking) と仮想ネットワークを使用して、米国西部で実行されている Azure VMware Solution にオンプレミスのデータセンターを接続する。
- [VMware Hybrid Cloud Extension (HCX)](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html) を使用して、VM を専用の Azure VMware Solution に移行する。

![提案されたアーキテクチャの図。](./media/contoso-migration-vmware-to-azure/on-premises-stretched-network-expressroute.png)

## <a name="solution-review"></a>ソリューションのレビュー

Contoso は、次の表のように長所と短所の一覧をまとめて、提案されたデザインを評価します。

| 考慮事項 | 詳細 |
| --- | --- |
| **長所** | <li>ハイ パフォーマンスのベアメタル VMware インフラストラクチャ。 <li>インフラストラクチャは、完全に Contoso 専用となり、他のお客様のインフラストラクチャから物理的に分離されます。 <li>Contoso は VMware を使ったリホストを使用することになるので、特別な構成や移行の複雑さはありません。 <li>Contoso は、[Azure ハイブリッド特典](https://azure.microsoft.com/pricing/hybrid-benefit/)と、レガシ Windows および SQL プラットフォーム用の[拡張セキュリティ更新プログラム](https://www.microsoft.com/cloud-platform/windows-server-2008)を使用して、ソフトウェア アシュランスへの投資を活用できます。 <li>Contoso は、Azure でアプリケーション VM の完全な制御を維持できます。 <br><br> |
| **短所** | <li>Contoso は今後も、Azure App Service や Azure SQL Database などのマネージド サービスにアプリケーションを移行するのではなく、VMware VM としてアプリケーションをサポートしていく必要があります。 <li>Azure VMware Solution の設定と価格は、Azure IaaS 内の個々の VM 単位ではなく大きなノード単位であり、少なくとも 3 ノードが必要です。 現在使用されているオンプレミス環境によって、他の Azure サービスが備えるオンデマンドな特性が制限されるため、Contoso はその容量ニーズを計画する必要があります。 |

> [!NOTE]
> 価格の詳細については、「[Azure VMware Solution の価格](https://azure.microsoft.com/pricing/details/azure-vmware/)」を参照してください。

## <a name="migration-process"></a>移行プロセス

Contoso は、VMware HCX ツールを使用して、その VM を Azure VMware Solution に移動することになります。 その VM は、Azure VMware Solution のプライベート クラウド内で実行されます。 [VMware HCX の移行方法](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-8A31731C-AA28-4714-9C23-D9E924DBB666.html)には、一括移行やコールド移行があります。 VMware vMotion や Replication Assisted vMotion (RAV) は、ライブ マイグレーションによって実行されるワークロード用に予約された方法です。

プロセスを遂行するために、Contoso チームは以下を行います。

- Azure と ExpressRoute におけるネットワークを計画します。
- Azure portal を使用して、Azure VMware Solution のプライベート クラウドを作成します。
- ExpressRoute 回線を含むネットワークを構成します。
- オンプレミスの vSphere 環境を Azure VMware Solution のプライベート クラウドに接続する HCX コンポーネントを構成します。
- VM をレプリケートし、VMware HCX を使用してそれらを Azure に移動します。

 ![移行プロセスの図。](./media/contoso-migration-vmware-to-azure/on-premises-vmware-hcx-azure.png)

## <a name="scenarios-steps"></a>シナリオの手順

> [!div class="checklist"]
>
> - **ステップ 1:ネットワークの計画**
> - **手順 2:Azure VMware Solution のプライベート クラウドを作成する**
> - **ステップ 3:ネットワークを構成する**
> - **手順 4:HCX を使用して VM を移行する**

### <a name="step-1-network-planning"></a>手順 1:ネットワークの計画

Contoso は、Azure Virtual Network、およびオンプレミスと Azure の間の接続を含むネットワークを計画する必要があります。 Azure VMware Solution のプライベート クラウドへの接続と共に、オンプレミスの環境と Azure ベースの環境の間の高速接続を用意する必要があります。

この接続は Azure ExpressRoute を通じて提供されます。また、サービスを有効にするために、特定のネットワーク アドレス範囲とファイアウォール ポートが必要になります。 高帯域幅かつ低遅延のこの接続によって、Contoso は、Azure VMware Solution のプライベート クラウド環境から、Azure サブスクリプションで実行されているサービスにアクセスすることができます。

Contoso は、その[仮想ネットワーク](/azure/virtual-network/virtual-network-vnet-plan-design-arm)用に、重複していないアドレス空間を含む IP アドレス スキームを計画する必要があります。 同社は、[ExpressRoute ゲートウェイ](/azure/expressroute/expressroute-about-virtual-network-gateways)のゲートウェイ サブネットを含める必要があります。

Azure VMware Solution のプライベート クラウドは、もう 1 つの Azure ExpressRoute 接続を使用して Azure で Contoso の仮想ネットワークに接続されます。 ExpressRoute Global Reach を有効にすることで、Azure VMware Solution のプライベート クラウド上で実行される VM に対し、オンプレミスの VM から[直接接続](/azure/azure-vmware/concepts-networking#on-premises-interconnectivity)することができます。 Global Reach を有効にするには、ExpressRoute Premium SKU が必要です。

![ExpressRoute Global Reach と Azure VMware Solution の図。](./media/contoso-migration-vmware-to-azure/adjacency-overview-drawing-double.png)

Azure VMware Solution のプライベート クラウドでは、サブネット用に少なくとも `/22` の CIDR ネットワーク アドレス ブロックが必要です。 オンプレミス環境と仮想ネットワークに接続するには、これが重複していないネットワーク アドレス ブロックである必要があります。

>[!NOTE]
> Azure VMware Solution のネットワーク計画については、[Azure VMware Solution のネットワークのチェックリスト](/azure/azure-vmware/tutorial-network-checklist)に関するページを参照してください。

### <a name="step-2-create-an-azure-vmware-solution-private-cloud"></a>手順 2:Azure VMware Solution のプライベート クラウドを作成する

Contoso はネットワークと IP アドレスの計画を完了すると、今度は、Azure の米国西部リージョンに Azure VMware Solution サービスをセットアップする作業に専念します。 Contoso は、Azure VMware Solution を使用して Azure に vSphere クラスターをデプロイすることができます。

Azure VMware Solution のプライベート クラウドは、ESXi ホスト、vCenter、vSAN、NSX をサポートする分離された VMware ソフトウェア定義データセンターです。 このスタックは、Azure リージョン内の分離された専用のベアメタル ハードウェア ノードで実行されます。 Azure VMware Solution プライベート クラウドの初期デプロイ時の最小ホスト数は 3 つです。 追加のホストは、クラスターあたり最大 16 個まで一度に 1 つずつ追加できます。

詳細については、[Azure VMware Solution プレビューのプライベート クラウドとクラスターの概念](/azure/azure-vmware/concepts-private-clouds-clusters)に関するページを参照してください。

Azure VMware Solution のプライベート クラウドは、Azure VMware Solution ポータルから管理します。 Contoso は自社の管理ドメインに自社の vCenter Server を所有しています。

Azure VMware Solution のプライベート クラウドを作成する方法については、[Azure VMware Solution のプライベート クラウドを Azure にデプロイする](/azure/azure-vmware/tutorial-create-private-cloud)方法に関するページを参照してください。

1. Contoso チームは、まず次のコマンドを実行して、Azure VMware Solution プロバイダーを Azure に登録します。

    ```bash
    az provider register -n Microsoft.AVS --subscription <your subscription ID>
    ```

1. チームは計画したネットワーク情報を入力することによって、Azure portal で Azure VMware Solution のプライベート クラウドを作成します。 次に、 **[確認と作成]** を選択します。 この手順には約 2 時間かかります。

    ![Azure VMware Solution のプライベート クラウドを作成するための Azure portal ペインのスクリーンショット。](./media/contoso-migration-vmware-to-azure/create-private-cloud.png)

1. チームは Azure VMware Solution のプライベート クラウドのデプロイが完了したことを確認するために、リソース グループに移動して、プライベート クラウドのリソースを選択します。 状態が "**成功**" として表示されます。

    ![デプロイが成功したことを示す Contoso Azure VMware Solution のプライベート クラウド ページのスクリーンショット。](./media/contoso-migration-vmware-to-azure/validate-deployment.png)

### <a name="step-3-configure-networking"></a>手順 3:ネットワークを構成する

Azure VMware Solution のプライベート クラウドには、仮想ネットワークが必要です。 Azure VMware Solution のプレビュー期間中はオンプレミスの vCenter がサポートされないため、Contoso のオンプレミス環境と統合するための手順が別途必要になります。 チームは、ExpressRoute 回線と仮想ネットワーク ゲートウェイを設定することにより、その仮想ネットワークを Azure VMware Solution のプライベート クラウドに接続します。

詳細については、「[Azure で VMware プライベート クラウド用のネットワークを構成する](/azure/azure-vmware/tutorial-configure-networking)」を参照してください。

1. Contoso チームはまず、ゲートウェイ サブネットを含む仮想ネットワークを作成します。

    > [!IMPORTANT]
    > プライベート クラウドを作成したときに使用したアドレス空間と重複 "*していない*" アドレス空間を使用する必要があります。

1. ExpressRoute VPN ゲートウェイを作成します。適切な SKU を選択し、 **[確認と作成]** を選択する必要があります。

    ![**[仮想ネットワーク ゲートウェイの作成]** ペインのスクリーンショット。](./media/contoso-migration-vmware-to-azure/create-virtual-network-gateway.png)

1. ExpressRoute を仮想ネットワークに接続するために、認証キーを取得します。 このキーは、Azure portal から、Azure VMware Solution のプライベート クラウド リソースの [接続] 画面にアクセスして確認できます。

    ![Contoso Azure VMware Solution のプライベート クラウドの [接続] ペインにある [ExpressRoute] タブのスクリーンショット。](./media/contoso-migration-vmware-to-azure/request-auth-key.png)

1. Azure VMware Solution のプライベート クラウドを Contoso の仮想ネットワークに接続する VPN ゲートウェイに ExpressRoute を接続します。 これは、Azure に接続を作成することによって行います。

    ![ExpressRoute を仮想ネットワークに接続するための **[接続の追加]** ペインのスクリーンショット。](./media/contoso-migration-vmware-to-azure/add-connection.png)

[Azure VMware Solution のプライベート クラウドにアクセスする方法](/azure/azure-vmware/tutorial-access-private-cloud)に関するページを参照してください。

### <a name="step-4-migrate-by-using-vmware-hcx"></a>手順 4:VMware HCX を使用して移行する

HCX を使用して VMware VM を Azure に移行するには、Contoso チームは次の手順の概要に従う必要があります。

- VMware HCX をインストールして構成します。
- HCX を使用して Azure への移行を実行します。

詳細については、「[Azure VMware Solution 用の HCX をインストールする](/azure/azure-vmware/tutorial-deploy-vmware-hcx)」を参照してください。

#### <a name="install-and-configure-vmware-hcx-for-the-public-cloud"></a>パブリック クラウド用の VMware HCX をインストールして構成する

[VMware HCX](https://cloud.vmware.com/vmware-hcx) は、Azure VMware Solution の既定のインストールに含まれる VMware 製品の一部です。 既定でインストールされているのは HCX Advanced ですが、追加の機能が必要な場合は HCX Enterprise にアップグレードすることができます。

Azure VMware Solution では、HCX の Cloud Manager コンポーネントが自動化されます。 オンプレミス側とお客様の vCenter ドメインで構成する必要があるコネクタ HCX アプライアンスへのダウンロード リンクとカスタマー アクティブ化キーが提供されます。 これらの要素はさらに、お客様が移行やレイヤー 2 ストレッチなどのサービスを利用できるよう、Azure VMware Solution クラウド アプライアンスとペアリングされます。

- Contoso チームは、VMware から提供される OVF パッケージを使用して HCX をデプロイします。

   ![**[OVF テンプレートのデプロイ]** ウィンドウのスクリーンショット。](./media/contoso-migration-vmware-to-azure/configure-template.png)

   Azure VMware Solution のプライベート クラウド用の HCX をインストールして構成する方法については、「[Azure VMware Solution 用の HCX をインストールする](/azure/azure-vmware/tutorial-deploy-vmware-hcx)」を参照してください。

- チームは HCX を構成する際に、移行やその他のオプション (ディザスター リカバリーなど) を有効にしました。

   ![HCX を構成するための **[Create Service Mesh]\(サービス メッシュの作成\)** ウィンドウのスクリーンショット。](./media/contoso-migration-vmware-to-azure/hcx-manager.png)

   詳細については、「[HCX パブリック クラウド用の HCX インストールのワークフロー](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-FDE5473E-6B71-4A71-85B6-8C9BA2B73686.html)」を参照してください。

#### <a name="migrate-vms-to-azure-by-using-hcx"></a>HCX を使用して VM を Azure に移行する

VMware クラウドと HCX を使用して、オンプレミスのデータセンター (移行元) と Azure VMware Solution のプライベート クラウド (移行先) の両方を構成したら、Contoso は VM の移行を開始できます。 チームは、複数の移行テクノロジを使用して、VMware HCX 対応のデータセンター間で VM を移動できます。

- Contoso の HCX アプリケーションがオンラインになり、状態が緑色になります。 これで、チームが HCX を使用して Azure VMware Solution VM を移行、保護する準備ができました。

  ![VMware vSphere Web クライアント ページのスクリーンショット。](./media/contoso-migration-vmware-to-azure/appliance-status.png)

#### <a name="vmware-hcx-bulk-migration"></a>VMware HCX 一括移行

この移行方法では、VMware vSphere のレプリケーション プロトコルを使用して、複数の VM を同時に移行先サイトに移動します。 利点は次のとおりです。

- この方法は、複数の VM を同時に移動するように設計されています。
- あらかじめ定義されたスケジュールに従って完了するように移行を設定できます。
- フェールオーバーが開始されるまでは、VM は移行元のサイトで実行されます。 サービスの中断は再起動と同等です。

#### <a name="vmware-hcx-vmotion-live-migration"></a>VMware HCX vMotion ライブ移行

この移行方法では、VMware vMotion プロトコルを使用して単一の VM をリモート サイトに移動します。 利点は次のとおりです。

- この方法は、一度に 1 つの VM を移動するように設計されています。
- VM の状態が移行されるときにサービスの中断は発生しません。

#### <a name="vmware-hcx-cold-migration"></a>VMware HCX コールド移行

この移行方法では、VMware の近距離通信プロトコルが使用されます。 移行元 VM の電源が切断されたときは、この方法が自動的に選択されます。

#### <a name="vmware-hcx-replication-assisted-vmotion"></a>VMware HCX レプリケーション支援 vMotion

VMware HCX RAV は、VMware HCX 一括移行の利点 (並列操作、回復性、スケジューリング) と VMware HCX vMotion 移行の利点 (VM の状態を移行するときのダウンタイムがゼロなど) を兼ね備えています。

## <a name="additional-resources"></a>その他のリソース

その他 VMware のテクニカル ドキュメントについては、以下を参照してください。

- [VMware HCX のドキュメント](https://docs.vmware.com/en/VMware-HCX/index.html)
- [VMware HCX を使用して仮想マシンを移行する](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g)
