---
title: Azure ハイブリッドおよびマルチクラウド製品の概要
description: ハイブリッドおよびマルチクラウドのソリューションの実現に役立つ Azure 製品を紹介します
author: BrianBlanchard
ms.author: brblanch
ms.date: 01/11/2021
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: overview
ms.custom: e2e-hybrid
ms.openlocfilehash: 026170a7d3a70abed6d396eae395bc4c3cd525e4
ms.sourcegitcommit: b8f8b7631aabaab28e9705934bf67dad15e3a179
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101800673"
---
# <a name="introduction-to-hybrid-and-multicloud-products-on-azure"></a>Azure でのハイブリッドおよびマルチクラウド製品の概要

マルチクラウドとマルチエッジの効果的なハイブリッド アプローチに依存することが、かつてないほど重要になっています。 Azure は当初から計画的にハイブリッド化されており、お客様のハイブリッド ニーズへの対応に重点を置いていました。またここ数年は、Azure 製品間のハイブリッド統合を中心に取り組んでいます。

お客様による複数のクラウドの導入がいっそう高度化するにつれて、お客様のオンプレミス、マルチクラウド、エッジ、および統合運用の要件をサポートするように、多くの Azure 製品でそのような観点を拡大してきました。 ハイブリッド統合とは、お客様が統一されたガバナンスと管理によって、アプリケーションとデータベースを一貫して構築およびデプロイし、シームレスに運用し、異種環境間にわたって統合されたクラウド セキュリティを提供できることを意味します。

この記事では、ハイブリッドおよびマルチクラウド機能を備えた Azure 製品をすべて紹介するわけではありませんが、使用するクラウド ポートフォリオ全体でこの機能を発揮するいくつかのコア製品について紹介します。

Azure ハイブリッドおよびマルチクラウド製品で実現できることの詳細については、[Azure ハイブリッドとマルチクラウドのハブ](/hybrid/)に関する記事をご覧ください。

![下に示す Azure ハイブリッドおよびマルチクラウド製品の概要](../../_images/hybrid/hybrid-hero-slide.png)

この一連の記事は、関連するプロセス (初期のビジネス戦略からワークロードの最適化、運用管理サイクルに至るまで) にこれらのツールを統合するのに役立ちます。

## <a name="manage-hybrid-and-multicloud-environments-with-unified-operations-tools"></a>統合運用ツールを使用してハイブリッドおよびマルチクラウドの環境を管理する

- [Azure Arc](/azure/azure-arc/?toc=/azure/cloud-adoption-framework/toc.json&bc=/azure/cloud-adoption-framework/_bread/toc.json): このクラウドベースのサービスにより、Azure Resource Manager ベースの管理モデルは、仮想マシン (VM)、Kubernetes クラスター、コンテナー化データベースなどの Azure 以外のリソースに拡張されます。
- [Azure Arc 対応サーバー](/azure/azure-arc/servers/overview?toc=/azure/cloud-adoption-framework/toc.json&bc=/azure/cloud-adoption-framework/_bread/toc.json): このハイブリッド サービスを使用すると、Azure の外部 (企業ネットワークや他のクラウド プロバイダー) でホストされている Windows や Linux のマシンを管理できます。 これは、ネイティブの Azure VM を管理する方法に似ています。
- [Azure Arc 対応 Kubernetes](/azure/azure-arc/kubernetes/overview?toc=/azure/cloud-adoption-framework/toc.json&bc=/azure/cloud-adoption-framework/_bread/toc.json): このハイブリッド サービスを使用すると、Azure の内部または外部の Kubernetes クラスターのデプロイと管理を効率化できます。
- [Azure Arc 対応 SQL Server](/sql/sql-server/azure-arc/overview?toc=/azure/cloud-adoption-framework/toc.json&bc=/azure/cloud-adoption-framework/_bread/toc.json): Azure Arc 対応サーバーのこの部分により、Azure サービスは、Azure の外部にあるお客様のデータセンター、エッジ、またはマルチクラウド環境でホストされている SQL Server インスタンスに拡張されます。
- [Azure Arc 対応データ サービス](/azure/azure-arc/data/overview?toc=/azure/cloud-adoption-framework/toc.json&bc=/azure/cloud-adoption-framework/_bread/toc.json): このハイブリッド サービスにより、Kubernetes と任意のインフラストラクチャを使用して、Azure データ サービスをオンプレミス、エッジ、パブリック クラウドで実行できるようになります。
- [Azure Arc 対応 SQL Managed Instance](/azure/azure-arc/data/managed-instance-overview?toc=/azure/cloud-adoption-framework/toc.json&bc=/azure/cloud-adoption-framework/_bread/toc.json): この Azure SQL Database データ サービスは、Azure Arc 対応データ サービスをホストする任意のインフラストラクチャ上に作成できます。

## <a name="deploy-hybrid-and-multicloud-solutions"></a>ハイブリッドおよびマルチクラウド ソリューションをデプロイする

- [Azure Stack HCI (20h2)](/azure-stack/hci/overview?toc=/azure/cloud-adoption-framework/toc.json&bc=/azure/cloud-adoption-framework/_bread/toc.json): これはハイブリッドのオンプレミス環境で仮想化された Windows および Linux オペレーティング システム (OS) のワークロードとそのストレージをホストする、ハイパーコンバージド インフラストラクチャ (HCI) クラスター ソリューションです。 クラスターは、2 個から 16 個の物理ノードで構成されます。
- [Azure Kubernetes Service on Azure Stack HCI](/azure-stack/aks-hci/overview?toc=/azure/cloud-adoption-framework/toc.json&bc=/azure/cloud-adoption-framework/_bread/toc.json): これは AKS の実装であり、Azure Stack HCI でのコンテナー化されたアプリケーションの大規模な実行を自動化します。
- [Azure Kubernetes Service](/azure/aks/intro-kubernetes?toc=/azure/cloud-adoption-framework/toc.json&bc=/azure/cloud-adoption-framework/_bread/toc.json): このサービスを使用すると、マネージド Kubernetes クラスターを Azure に簡単にデプロイできます。
- [Azure IoT Edge](/azure/iot-edge/?toc=/azure/cloud-adoption-framework/toc.json&bc=/azure/cloud-adoption-framework/_bread/toc.json) によって、クラウドベースのソリューションがローカル環境のエッジにデプロイされ、これらのデバイスおよび生成される IoT データを管理するための完全なサポートが Azure から提供されます。

## <a name="connect-your-hybrid-and-multicloud-environments"></a>ハイブリッドおよびマルチクラウド環境に接続する

- [仮想 WAN](/azure/virtual-wan/?toc=/azure/cloud-adoption-framework/toc.json&bc=/azure/cloud-adoption-framework/_bread/toc.json) により、セキュリティで保護されたグローバル分岐ソリューションに接続します。
- [ExpressRoute](/azure/expressroute/?toc=/azure/cloud-adoption-framework/toc.json&bc=/azure/cloud-adoption-framework/_bread/toc.json) により、Microsoft クラウド サービスへの高速なプライベート接続が確立されます
- [VPN ゲートウェイ](/azure/vpn-gateway/vpn-gateway-about-vpngateways?toc=/azure/cloud-adoption-framework/toc.json&bc=/azure/cloud-adoption-framework/_bread/toc.json)により暗号化されたトラフィックが Azure に送信されます
- [Azure Firewall](/azure/firewall/overview?toc=/azure/cloud-adoption-framework/toc.json&bc=/azure/cloud-adoption-framework/_bread/toc.json) は、組み込みの高可用性とクラウドの無制限のスケーラビリティを備えた、完全にステートフルなサービスとしてのファイアウォールです。
