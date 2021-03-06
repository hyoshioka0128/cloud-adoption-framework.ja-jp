---
title: ソフトウェア定義ネットワーク:PaaS のみ
description: クラウド内のソフトウェア定義ネットワークにおける PaaS のみのアーキテクチャ モデルの利点と制限事項について説明します。
author: rotycenh
ms.author: abuck
ms.date: 02/11/2019
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: decision-guide
ms.custom: governance
ms.openlocfilehash: e5351d14c6200056e4c5b43f622f655a23e79668
ms.sourcegitcommit: 9a84c2dfa4c3859fd7d5b1e06bbb8549ff6967fa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/21/2020
ms.locfileid: "83753579"
---
# <a name="software-defined-networking-paas-only"></a>ソフトウェア定義ネットワーク:PaaS のみ

サービスとしてのプラットフォーム (PaaS) リソースを実装すると、デプロイ プロセスによって、負荷分散、ポートのブロック、他の PaaS サービスへの接続など、そのネットワークに対する少数の制御を持つ仮の基礎ネットワークが自動的に作成されます。

Azure では、いくつかの PaaS リソースの種類を[仮想ネットワークにデプロイ](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)するか、[仮想ネットワークに接続](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)して、このようなリソースを既存の仮想ネットワーク インフラストラクチャと統合できます。 [App Service Environment](https://docs.microsoft.com/azure/app-service/environment/intro)、[Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/intro-kubernetes)、[Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview) など、その他のサービスは、仮想ネットワーク内にデプロイする必要があります。 多くの場合、PaaS リソースによって提供される既定のネイティブ ネットワーク機能だけに依存する PaaS のみのネットワーク アーキテクチャで、ワークロードの接続性とトラフィック管理の要件を十分に満たすことができます。

PaaS のみのネットワーク アーキテクチャを検討している場合は、必要な前提条件が実際の要件と一致していることを必ず確認してください。

## <a name="paas-only-assumptions"></a>PaaS のみの前提条件

PaaS のみのネットワーク アーキテクチャのデプロイは、以下を前提としています。

- デプロイされるアプリケーションはスタンドアロン アプリケーションであるか、仮想ネットワークを必要としない他の PaaS リソースのみに依存しています。
- お客様の IT 運用チームは、スタンドアロンの PaaS アプリケーションの管理、構成、デプロイをサポートするためにツール、トレーニング、プロセスを更新できます。
- PaaS アプリケーションは、IaaS リソースを含む、より広範なクラウド移行作業には含まれません。

このような前提条件は、PaaS のみのネットワークのデプロイに合わせた最低限の資格条件です。 この方法は単一アプリケーションのデプロイの要件と一致する可能性がありますが、各クラウド導入チームは、次のような長期的な問題を検討する必要があります。

- このデプロイは、PaaS 以外の他のリソースへのアクセスを必要とする範囲または規模に拡大する予定ですか。
- 現在のソリューション以外に他の PaaS のデプロイは計画されていますか。
- 組織は将来の他のクラウド移行の計画を立てていますか。

このような問題に対する答えは、チームが PaaS のみの選択肢を選ぶことを妨げるものではありませんが、最終的な決定を下す前に検討する必要があります。
