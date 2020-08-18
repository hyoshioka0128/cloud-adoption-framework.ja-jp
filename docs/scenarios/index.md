---
title: Azure 導入の 1 つの移行アプローチ
description: Azure Migrate の 1 つの移行アプローチに従って、IT ポートフォリオ全体を移行し、最新化します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 10/10/2019
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: migrate
ms.openlocfilehash: 088ee6b4582ef83255bcda6a5006dac3571e830a
ms.sourcegitcommit: 917188fa930cadddb03f9e9bbcdd7b630e4ee33e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/17/2020
ms.locfileid: "88284590"
---
<!-- docsTest:ignore "One Migration" -->
<!-- cSpell:ignore HANA -->

# <a name="the-one-migration-approach-to-migrating-the-it-portfolio"></a>IT ポートフォリオを移行するための 1 つの移行アプローチ

Azure と Azure Migrate は、どちらも Microsoft テクノロジをホストすることでよく知られています。 しかし、Windows と SQL Server を超えた移行をサポートする Azure の機能については、あまりご存知でないかもしれません。 Migrate の手法で取り込まれた "*1 つの移行*" シナリオでは、Microsoft とサードパーティの両方のテクノロジを移行するための一貫したガイドラインとプロセスの同じセットを示します。

## <a name="migration-scenarios"></a>移行シナリオ

以下の図と表は、移行と最新化のための同じ反復的な Migrate の手法を採用するいくつかのシナリオをまとめたものです。

![クラウド導入フレームワーク移行モデルの図。](../_images/migrate/one-migrate.png)

| | | | |
|---------|---------|---------|---------|
| **仮想マシン** | [仮想マシン](../migrate/azure-best-practices/contoso-migration-rehost-vm.md) | [Linux サーバー](../migrate/azure-best-practices/contoso-migration-rehost-linux-vm.md) | [仮想デスクトップ](./wvd/index.md) |
| **アプリケーション** | [ASP.NET](../migrate/azure-best-practices/contoso-migration-refactor-web-app-sql.md) | [Java](/azure/java/migration-overview?bc=%2fazure%2fcloud-adoption-framework%2f_bread%2ftoc.json&toc=%2fazure%2fcloud-adoption-framework%2ftoc.json) | [PHP](../migrate/azure-best-practices/contoso-migration-refactor-linux-app-service-mysql.md) |
| **データ** | [SQL Server](../migrate/azure-best-practices/contoso-migration-rehost-vm-sql-managed-instance.md) | [オープン ソース データベース](../migrate/azure-best-practices/sql-migration.md) | Analytics |
| **ハイブリッド** | [Azure Stack](./azure-stack/index.md) | [VMware](../migrate/azure-best-practices/vmware-host.md) | |
| **その他のシナリオ** | [ワークロードをセキュリティで保護する](../migrate/azure-best-practices/migrate-best-practices-security-management.md) | [メインフレーム](../infrastructure/mainframe-migration/index.md) | NetApp と SAP HANA |

## <a name="migration-methodology"></a>移行手法

上記の各移行シナリオでは、次に示すように、既存のワークロードをクラウドに移行するときに、同じ基本的なプロセスに従って作業を進めます。

![クラウド導入フレームワーク移行モデルの図。](../_images/migrate/methodology.png)

各シナリオで、複数のワークロードのリリースをガイドするための移行を段階的に構築します。 計画および準備の手法を通じてクラウド導入計画と Azure ランディング ゾーンを確立すると、移行の段階に構造を追加するのに役立ちます。

各イテレーションでは、移行手法に従って、ワークロードの評価、デプロイ、およびリリースを行います。 特定のシナリオに合わせてこれらのプロセスを変更するには、上記の表に示された移行シナリオのいずれかを選択します。

## <a name="next-steps"></a>次の手順

特定のシナリオの移行でない場合は、まず [4 つの手順のクラウド導入フレームワーク移行プロセス](../migrate/index.md)に従ってください。