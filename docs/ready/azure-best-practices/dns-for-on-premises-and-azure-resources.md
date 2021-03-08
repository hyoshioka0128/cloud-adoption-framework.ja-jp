---
title: オンプレミスおよび Azure 用の DNS
description: オンプレミスと Microsoft Azure 用の DNS に関連する主な設計上の考慮事項と推奨事項について確認します。
author: JefferyMitchell
ms.author: brblanch
ms.date: 02/18/2021
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.custom: think-tank
ms.openlocfilehash: 5a3793322e66892d314b6291fd83d75a3bcbe932
ms.sourcegitcommit: b8f8b7631aabaab28e9705934bf67dad15e3a179
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101800930"
---
# <a name="dns-for-on-premises-and-azure-resources"></a>オンプレミスおよび Azure リソース用の DNS

ドメイン ネーム システム (DNS) は、エンタープライズ規模のアーキテクチャ全体における重要な設計トピックです。 DNS への既存の投資を使用することを望む組織があります。 または、クラウドの導入を、内部 DNS インフラストラクチャを最新化し、Azure のネイティブ機能を使用する機会と考える組織もあります。

**設計上の考慮事項:**

- DNS リゾルバーを Azure プライベート DNS と組み合わせて使用し、クロスプレミスの名前解決を実現できます。

- お客様は、オンプレミスと Azure の間で既存の DNS ソリューションの使用を必須とすることもできます。

- 自動登録を使用して仮想ネットワークをリンクできるプライベート DNS ゾーンの最大数は 1 です。

- 自動登録を有効にしないで仮想ネットワークをリンクできるプライベート DNS ゾーンの最大数は 1,000 です。

**設計上の推奨事項:**

- Azure での名前解決だけが必要な環境の場合は、解決のための Azure プライベート DNS を使用します。 名前解決用にデリゲートされたゾーンを作成します (`azure.contoso.com` など)。

- Azure とオンプレミスの間で名前解決が必要な環境では、少なくとも 2 つの仮想マシン (VM) にデプロイされた既存の DNS インフラストラクチャ (Active Directory 統合 DNS など) を使用します。 それらの DNS サーバーを使用するように、仮想ネットワークで DNS の設定を構成します。

- その場合でも、Azure プライベート DNS ゾーンを仮想ネットワークにリンクし、オンプレミスの DNS サーバーを使用することで、オンプレミスの DNS 名 (`onprem.contoso.com` など) への条件付き転送によるハイブリッド リゾルバーとして、DNS サーバーを使用することができます。 Azure プライベート DNS ゾーン (`azure.contoso.com` など) に対する Azure 内のリゾルバー VM への条件付きフォワーダーを使用して、オンプレミスのサーバーを構成できます。

- 独自の DNS (Red Hat OpenShift など) をデプロイする必要がある特殊なワークロードでは、優先 DNS 解決を使用する必要があります。

- 仮想ネットワーク内にデプロイされている仮想マシンの DNS レコードのライフサイクルを自動的に管理するには、Azure DNS に対する自動登録を有効にします。

- Azure プライベート DNS でのクロスプレミスの DNS 解決には、リゾルバーとして仮想マシンを使用します。

- グローバル接続サブスクリプション内に Azure プライベート DNS ゾーンを作成します。 他の Azure プライベート DNS ゾーンを作成する場合があります (たとえば、Azure Private Link 用の `privatelink.database.windows.net` や `privatelink.blob.core.windows.net`)。
