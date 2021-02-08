---
title: 機械学習のセキュリティ
description: 機械学習は、ビジネス固有のセキュリティに関する考慮事項を提示し、企業で機械学習アーキテクチャを設計および評価する際には、いくつかのセキュリティ原則を考慮する必要があります。
author: shinchan75034
ms.author: brblanch
ms.date: 01/20/2021
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: innovate
ms.custom: think-tank
ms.openlocfilehash: a702b08b518919b6f3b1a30bf954e81a5b02aeea
ms.sourcegitcommit: 9cd2b48fbfee229edc778f8c5deaf2dc39dfe2d6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/01/2021
ms.locfileid: "99230680"
---
# <a name="machine-learning-security"></a>機械学習のセキュリティ

機械学習は、ビジネス固有のセキュリティに関する考慮事項を提示し、企業で機械学習アーキテクチャを設計および評価する際には、いくつかのセキュリティ原則を考慮する必要があります。

- **回復力:** 機械学習システムでは、異常な動作を特定し、操作や強制型変換を防ぐ必要があります。

- **裁量:** AI が関与するデータ アクセス シナリオでは、データへのアクセスを最小限に制限する必要があります。

- **悪意のあるデータ:** 機械学習アルゴリズムは、悪意のあるデータから保護する必要があります。

- **透明性とアカウンタビリティ:** AI には、透明性とアカウンタビリティを提供する組み込みのフォレンジック機能が必要です。 これらの機能は、AI による侵入検出の初期形態として機能し、判断が行われた正確な時点と使用されたデータをエンジニアが把握するのに役立ちます。

- **セキュリティで保護された環境:** データ、およびモデルの結果や予測を保護するには、開発、トレーニング、および推論環境へのアクセスをセキュリティで保護する必要があります。

## <a name="deploy-azure-kubernetes-service-to-secure-an-inference-environment"></a>Azure Kubernetes Service をデプロイして推論環境をセキュリティで保護する

運用環境での推論には、Azure Kubernetes Service (AKS) を使用することをお勧めします。 仮想ネットワークでは、次の 2 つのオプションを使用できます。

- パブリック IP アドレスを持つ AKS クラスターを仮想ネットワークにデプロイするか、接続します。
- プライベート AKS クラスターを仮想ネットワークにアタッチします。

既定の AKS クラスターには、パブリック IP アドレスを持つコントロール プレーンがあります。 AKS クラスター用のプライベート IP アドレスが必要な場合は、プライベート AKS クラスターを使用します。 推論を行うには、AKS クラスターと Azure Machine Learning ワークスペースが同じ仮想ネットワーク内にある必要があります。

仮想ネットワーク内の既定の AKS 推論クラスターをセキュリティで保護するには、次の 3 つの IP アドレスを指定します。

- CIDR 表記では、**AKS のアドレス範囲** を指定します。 これは、他のサブネット範囲と重複させることはできません。

- AKS 内の DNS サービスには、**AKS DNS サービス IP アドレス** を割り当てます。 これは、AKS のアドレス範囲内である必要があります。

- Docker ブリッジには、**Docker ブリッジ アドレス** を割り当てます。これにより、スコアリング スクリプトがコンテナーとして実行されます。 このアドレスを、サブネット IP または AKS アドレス範囲内に設定することはできません。

プライベート AKS クラスターで、内部およびプライベート ロード バランサーを使用するように AKS を構成することができます。 このシナリオではプライベート IP のみが許可されており、このタスクには Python SDK または Azure コマンドライン拡張機能を使用できますが、Azure Machine Learning スタジオは使用できません。 プライベート ロード バランサーを使用する場合は、仮想ネットワークが含まれる AKS クラスター リソース グループに、ネットワーク共同作成者ロールを付与する必要があります。

## <a name="next-steps"></a>次のステップ

- IP アドレスの範囲と仮想ネットワークで推論を実行する手順については、[仮想ネットワークを使用して Azure Machine Learning 推論環境をセキュリティで保護する方法](/azure/machine-learning/how-to-secure-inferencing-vnet?tabs=python#secure-vnet-traffic)に関するページを参照してください。

- プライベート ロード バランサーを構成してネットワーク共同作成者ロールを設定する方法については、[ネットワーク共同作成者ロールのセクション](/azure/machine-learning/how-to-secure-inferencing-vnet?tabs=python#network-contributor-role)を参照してください。
