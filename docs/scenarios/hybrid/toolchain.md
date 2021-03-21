---
title: Azure ハイブリッドおよびマルチクラウド製品の概要
description: ハイブリッドおよびマルチクラウドのソリューションの実現に役立つ Azure 製品を紹介します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 01/11/2021
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: overview
ms.custom: e2e-hybrid
ms.openlocfilehash: 8d552d0e9e109cbb2d9e98da27d29fc7fe6bdf74
ms.sourcegitcommit: c167c45b66cc7324b60c88b8b7aac439f956b65d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102208899"
---
# <a name="introduction-to-hybrid-and-multicloud-products-on-azure"></a>Azure でのハイブリッドおよびマルチクラウド製品の概要

マルチクラウドとマルチエッジの効果的なハイブリッド アプローチに依存することが、かつてないほど重要になっています。 Azure は、Azure 製品間のハイブリッド統合により、お客様のハイブリッド ニーズに対応することに重点を置いています。

お客様は、複数クラウドの導入過程でより高度な知識を得てきました。 一部の Azure 製品では、お客様のオンプレミス、マルチクラウド、エッジ、および統合運用の要件をサポートするために、対象範囲が広がっています。 ハイブリッド統合とは、お客様が統一されたガバナンスと管理によって、アプリケーションとデータベースを一貫して構築およびデプロイし、シームレスに運用し、異種環境間にわたって統合されたクラウド セキュリティを提供できることを意味します。

この記事では、クラウドのポートフォリオ全体でこの機能を提供できるハイブリッド機能とマルチクラウド機能を備えた Azure 製品をいくつか紹介します。

Azure ハイブリッドおよびマルチクラウド製品で実現できることの詳細については、[Azure ハイブリッドとマルチクラウドのハブ](/hybrid/)に関する記事を参照してください。

![この記事に記載されている Azure ハイブリッドおよびマルチクラウド製品の概要を示す図。](../../_images/hybrid/hybrid-hero-slide.png)

この一連の記事は、関連するプロセス (初期のビジネス戦略からワークロードの最適化、運用管理サイクルに至るまで) にこれらのツールを統合するのに役立ちます。

## <a name="manage-hybrid-and-multicloud-environments-with-unified-operations-tools"></a>統合運用ツールを使用してハイブリッドおよびマルチクラウドの環境を管理する

- [Azure Arc](/azure/azure-arc/?toc=/azure/cloud-adoption-framework/toc.json&bc=/azure/cloud-adoption-framework/_bread/toc.json) はクラウドベースのサービスです。これを使用して、Azure Resource Manager ベースの管理モデルを、仮想マシン、Kubernetes クラスター、コンテナー化データベースなどの Azure 以外のリソースに拡張することができます。
- [Azure Arc 対応サーバー](/azure/azure-arc/servers/overview?toc=/azure/cloud-adoption-framework/toc.json&bc=/azure/cloud-adoption-framework/_bread/toc.json)はハイブリッド サービスです。これを使用して、Azure の外部 (企業ネットワークや他のクラウド プロバイダー) でホストされている Windows や Linux のマシンを管理することができます。 この機能は、ネイティブの Azure VM を管理する方法に似ています。
- [Azure Arc 対応 Kubernetes](/azure/azure-arc/kubernetes/overview?toc=/azure/cloud-adoption-framework/toc.json&bc=/azure/cloud-adoption-framework/_bread/toc.json) はハイブリッド サービスです。これを使用して、Azure の内部または外部の Kubernetes クラスターのデプロイと管理を効率化することができます。
- [Azure Arc 対応 SQL Server](/sql/sql-server/azure-arc/overview?toc=/azure/cloud-adoption-framework/toc.json&bc=/azure/cloud-adoption-framework/_bread/toc.json) は、Azure Arc 対応サーバーの一部です。これを使用して、Azure の外部にあるお客様のデータセンター、エッジ、またはマルチクラウド環境でホストされている SQL Server インスタンスに Azure サービスを拡張することができます。
- [Azure Arc 対応データ サービス](/azure/azure-arc/data/overview?toc=/azure/cloud-adoption-framework/toc.json&bc=/azure/cloud-adoption-framework/_bread/toc.json)は、ハイブリッド サービスです。これにより、Kubernetes と任意のインフラストラクチャを使用して、Azure データ サービスをオンプレミス、エッジ、パブリック クラウドで実行することができます。
- [Azure Arc 対応 SQL Managed Instance](/azure/azure-arc/data/managed-instance-overview?toc=/azure/cloud-adoption-framework/toc.json&bc=/azure/cloud-adoption-framework/_bread/toc.json) は、Azure SQL Database データ サービスです。これは、Azure Arc 対応データ サービスをホストする任意のインフラストラクチャ上に作成することができます。

## <a name="deploy-hybrid-and-multicloud-solutions"></a>ハイブリッドおよびマルチクラウド ソリューションをデプロイする

- [Azure Stack HCI (20h2)](/azure-stack/hci/overview?toc=/azure/cloud-adoption-framework/toc.json&bc=/azure/cloud-adoption-framework/_bread/toc.json) はハイパーコンバージド インフラストラクチャ (HCI) クラスター ソリューションです。これにより、ハイブリッドのオンプレミス環境で仮想化された Windows および Linux オペレーティング システムのワークロードとそのストレージをホストすることができます。 クラスターは、2 個から 16 個の物理ノードで構成されます。
- [Azure Kubernetes Service (AKS) on Azure Stack HCI](/azure-stack/aks-hci/overview?toc=/azure/cloud-adoption-framework/toc.json&bc=/azure/cloud-adoption-framework/_bread/toc.json) は AKS の実装です。これにより、Azure Stack HCI でのコンテナー化されたアプリケーションの大規模な実行が自動化されます。
- [Azure Kubernetes Service](/azure/aks/intro-kubernetes?toc=/azure/cloud-adoption-framework/toc.json&bc=/azure/cloud-adoption-framework/_bread/toc.json) を使用すると、マネージド Kubernetes クラスターを Azure 内に簡単にデプロイできます。
- [Azure IoT Edge](/azure/iot-edge/?toc=/azure/cloud-adoption-framework/toc.json&bc=/azure/cloud-adoption-framework/_bread/toc.json) によって、クラウドベースのソリューションがローカル環境のエッジにデプロイされ、これらのデバイスおよび生成される IoT データを管理するための完全なサポートが Azure から提供されます。

## <a name="connect-your-hybrid-and-multicloud-environments"></a>ハイブリッドおよびマルチクラウド環境に接続する

- [Azure Virtual WAN](/azure/virtual-wan/?toc=/azure/cloud-adoption-framework/toc.json&bc=/azure/cloud-adoption-framework/_bread/toc.json) により、セキュリティで保護されたグローバル分岐ソリューションに接続します。
- [Azure ExpressRoute](/azure/expressroute/?toc=/azure/cloud-adoption-framework/toc.json&bc=/azure/cloud-adoption-framework/_bread/toc.json) により、Microsoft クラウド サービスへの高速なプライベート接続が確立されます。
- [Azure VPN Gateway](/azure/vpn-gateway/vpn-gateway-about-vpngateways?toc=/azure/cloud-adoption-framework/toc.json&bc=/azure/cloud-adoption-framework/_bread/toc.json) から Azure には、暗号化されたトラフィックが送信されます。
- [Azure Firewall](/azure/firewall/overview?toc=/azure/cloud-adoption-framework/toc.json&bc=/azure/cloud-adoption-framework/_bread/toc.json) は、組み込みの高可用性とクラウドの無制限のスケーラビリティを備えた、完全にステートフルなサービスとしてのファイアウォールです。
