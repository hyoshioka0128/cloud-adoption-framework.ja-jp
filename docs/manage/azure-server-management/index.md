---
title: Azure サーバー管理サービスの概要
titleSuffix: Microsoft Cloud Adoption Framework for Azure
description: Azure サーバー管理サービス入門
author: BrianBlanchard
ms.author: brblanch
ms.date: 05/10/2019
ms.topic: article
ms.service: cloud-adoption-framework
ms.subservice: operate
ms.openlocfilehash: 4d1ada9d47e54f4b0d3828ce93b2d55f3eda8a34
ms.sourcegitcommit: 443c28f3afeedfbfe8b9980875a54afdbebd83a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/16/2019
ms.locfileid: "71026411"
---
# <a name="overview-of-azure-server-management-services"></a>Azure サーバー管理サービスの概要

Azure サーバー管理サービスを利用すれば、お客様は大規模なサーバー管理を一貫性のある方法で実行できます。 こうしたサービスは Linux オペレーティング システムと Windows オペレーティング システムの両方に対応しており、本稼働環境、開発環境、テスト環境で利用できます。 またこれらは、Azure IaaS 仮想マシン、物理サーバー、オンプレミスまたはその他のホスティング環境でホストされている仮想マシンをサポートできます。 

Azure サーバー管理サービス スイートに含まれるサービスを、次の図に示します。 

![Azure 運用モデルの図](./media/operations-diagram.png)

Microsoft クラウド導入フレームワークのこのセクションのガイダンスからは、環境でサーバー管理サービスをデプロイするために実用的で規範的な指示を与える計画が提供されます。 この計画は、このようなサービスを簡単に使用開始できるようにするためにもので、環境の規模に関係なく、管理ステージをインクリメント方式で導きます。

わかりやすくするため、このガイダンスを 3 つのステージに分類しました。

![Azure サーバー管理スイート導入の 3 つのステージ](./media/operations-stages.png)

<!-- markdownlint-disable MD026 -->

## <a name="why-use-azure-management-services"></a>Azure 管理サービスを使用する理由とは?

Azure 管理サービスには次の利点があります。

- **Azure ネイティブ。** 管理サービスは Azure Resource Manager に組み込まれており、ネイティブに統合されています。 これらのサービスは、新しい機能を提供するために継続的に改善されます。
- **Windows と Linux**。 Windows コンピューターと Linux コンピューターの管理操作には一貫性があり同じ操作です。
- **ハイブリッド。** 管理サービスは、Azure IaaS 仮想マシンの他に、オンプレミスまたはその他のホスティング環境でホストされている物理サーバーと仮想サーバーに対応しています。
- **セキュリティ。** Microsoft では、あらゆる形式のセキュリティに相当のリソースを充てています。 この投資は Azure クラウド インフラストラクチャを保護するだけでなく、結果的に与えられるテクノロジや専門知識を拡大し、場所に関係なく、お客様のリソースを保護します。

## <a name="next-steps"></a>次の手順

Azure サーバー管理スイートの導入に関わる[ツール、サービス、計画作成](./prerequisites.md)を理解してください。

> [!div class="nextstepaction"]
> [前提条件のツールと計画作成](./prerequisites.md)
