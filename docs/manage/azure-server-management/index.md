---
title: Azure サーバー管理サービスの概要
description: お使いの環境でサーバー管理サービスをデプロイするために、Azure のクラウド導入フレームワークのこのセクションで提供される規範的な計画について説明します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 05/10/2019
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: operate
ms.openlocfilehash: ae03aa1186aab3503f8d5397e5d397f0fa086b26
ms.sourcegitcommit: 7d3fc1e407cd18c4fc7c4964a77885907a9b85c0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2020
ms.locfileid: "80426543"
---
# <a name="overview-of-azure-server-management-services"></a>Azure サーバー管理サービスの概要

Azure サーバー管理サービスを使用すると、大規模なサーバー管理を一貫性のある方法で行えます。 これらのサービスは、Linux オペレーティング システムと Windows オペレーティング システムの両方に対応しています。 本稼働環境、開発環境、テスト環境で利用できます。 サーバー管理サービスは、Azure IaaS 仮想マシン、物理サーバー、オンプレミスまたはその他のホスティング環境でホストされている仮想マシンをサポートできます。

Azure サーバー管理サービス スイートに含まれるサービスを次の図に示します。![Azure 運用モデルの図](./media/operations-diagram.png)

Microsoft クラウド導入フレームワークのこのセクションからは、お使いの環境でサーバー管理サービスをデプロイするための実用的で規範的な計画が提供されます。 この計画により、これらのサービスをすぐに使用開始できるようにし、環境の規模に関係なく、管理ステージを段階的に進めることができます。

わかりやすくするため、このガイダンスを 3 つのステージに分類しました。

![Azure サーバー管理スイート導入の 3 つのステージ](./media/operations-stages.png)

<!-- markdownlint-disable MD026 -->

## <a name="why-use-azure-server-management-services"></a>Azure サーバー管理サービスを使用する理由

Azure サーバー管理サービスには次の利点があります。

- **Azure ネイティブ:** サーバー管理サービスは Azure Resource Manager に組み込まれており、ネイティブに統合されています。 これらのサービスは、新しい機能を提供するために継続的に改善されます。
- **Windows と Linux:** Windows コンピューターと Linux コンピューターでの管理操作は同じであり、一貫性があります。
- **ハイブリッド:** サーバー管理サービスは、Azure IaaS 仮想マシンの他に、オンプレミスまたはその他のホスティング環境でホストされている物理サーバーと仮想サーバーに対応しています。
- **セキュリティ:** Microsoft では、あらゆる形式のセキュリティに相当のリソースを充てています。 この投資は Azure インフラストラクチャを保護するだけでなく、結果として得られるテクノロジや専門知識を拡大し、場所に関係なく、お客様のリソースを保護します。

## <a name="next-steps"></a>次のステップ

Azure サーバー管理スイートの導入に関わる[ツール、サービス、計画作成](./prerequisites.md)を理解してください。

> [!div class="nextstepaction"]
> [前提条件のツールと計画作成](./prerequisites.md)
