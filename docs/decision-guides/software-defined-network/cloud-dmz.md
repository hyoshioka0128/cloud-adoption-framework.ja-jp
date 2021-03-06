---
title: ソフトウェア定義ネットワーク:クラウド DMZ
description: VPN を使用してオンプレミスとクラウドベースのネットワーク間の制限付きアクセスを可能にするクラウド DMZ ネットワーク アーキテクチャについて説明します。
author: rotycenh
ms.author: abuck
ms.date: 02/11/2019
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: decision-guide
ms.custom: governance
ms.openlocfilehash: 00eefa3b865adf4230a77ed2062bef0a7cf0e854
ms.sourcegitcommit: 9a84c2dfa4c3859fd7d5b1e06bbb8549ff6967fa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/21/2020
ms.locfileid: "83753590"
---
# <a name="software-defined-networking-cloud-dmz"></a>ソフトウェア定義ネットワーク:クラウド DMZ

クラウド DMZ ネットワーク アーキテクチャでは、仮想プライベート ネットワーク (VPN) を使用してネットワークに接続することで、オンプレミス ネットワークとクラウド ベース ネットワークとの間のアクセスを制限することができます。 DMZ モデルは一般にネットワークへの外部アクセスをセキュリティ保護するときに使用されますが、ここで説明するクラウド DMZ アーキテクチャの目的は、特にクラウドベースのリソースからオンプレミス ネットワークへのアクセス (またはその逆) をセキュリティ保護することです。

![セキュリティ保護されたハイブリッド ネットワーク アーキテクチャ](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/images/dmz-private.png)

このアーキテクチャは、組織がクラウド ベースのワークロードとオンプレミスのワークロードの統合を開始したいが、完全に成熟したクラウド セキュリティ ポリシーを持たない、または 2 つの環境の間でセキュリティで保護された専用の WAN 接続を取得していないシナリオをサポートするように設計されています。 その結果、オンプレミス サービスをセキュリティで確実に保護するために、クラウド ネットワークを非武装地帯のように扱う必要があります。

DMZ は、ファイアウォールやパケット検査などのセキュリティ機能を実装するために、ネットワーク仮想アプライアンス (NVA) をデプロイします。 オンプレミスとクラウド ベースのアプリケーションまたはサービスとの間を通過するトラフィックは、監査できるように DMZ を通過する必要があります。 VPN 接続および DMZ ネットワークによって許可されるトラフィックを決定する規則は、IT セキュリティ チームが厳密に管理します。

## <a name="cloud-dmz-assumptions"></a>クラウド DMZ の前提条件

クラウド DMZ のデプロイには、以下の前提が含まれています。

- セキュリティ チームが、オンプレミスとクラウド ベースのセキュリティ要件とポリシーを完全には調整していない。
- クラウドベースのワークロードで、オンプレミスまたはサードパーティのネットワークでホストされているサービスの限定されたサブセットにアクセスする必要がある。または、オンプレミス環境内のユーザーまたはアプリケーションによるクラウド ホスト リソースへのアクセスを制限する必要がある。
- オンプレミスのネットワークとクラウド プロバイダーの間に VPN 接続を実装することが、企業ポリシー、規制要件、または技術的な互換性の問題によって妨げられていない。
- ワークロードが、サブスクリプションのリソース制限をバイパスするために複数のサブスクリプションを必要としないか、複数のサブスクリプションを必要とするが、複数のサブスクリプションに分散するリソースによって使用される接続と共有サービスの中央管理を必要としない。

クラウド DMZ 仮想ネットワーク アーキテクチャの実装を考える際、クラウド導入チームは、次の問題を考慮する必要があります。

- オンプレミス ネットワークをクラウド ネットワークに接続すると、セキュリティ要件がより複雑になります。 クラウド ネットワークとオンプレミス環境の間の接続がセキュリティ保護されていても、クラウド リソースを確実に保護する必要があります。 クラウドベースのワークロードにアクセスするために作成されるすべてのパブリック IP を、[パブリック接続 DMZ](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-dmz?toc=/azure/cloud-adoption-framework/toc.json&bc=/azure/cloud-adoption-framework/_bread/toc.json) または [Azure Firewall](https://docs.microsoft.com/azure/firewall/overview) を使用して、適切にセキュリティ保護する必要があります。
- クラウドDMZ アーキテクチャは、接続をセキュリティでさらに保護し、オンプレミスとクラウド ネットワークの間でセキュリティ ポリシーを調整しながら、本格的なハイブリッド ネットワーク アーキテクチャを幅広く採用できるようにするための足がかりとして広く使用されています。 クラウド DMZ アプローチで対応できる、セキュリティ、ID、接続の特定のニーズがある分離デプロイに適用することもできます。

## <a name="learn-more"></a>詳細情報

クラウド DMZ の実装の詳細については、以下を参照してください。

- [Azure とオンプレミス データ センター間に DMZ を実装する](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-dmz)。 この記事では、安全なハイブリッド ネットワーク アーキテクチャを Azure に実装する方法について説明します。
