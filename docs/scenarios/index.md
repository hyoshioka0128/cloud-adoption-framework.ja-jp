---
title: Azure 導入の 1 つの移行アプローチ
description: Azure Migrate の 1 つの移行アプローチに従って、IT ポートフォリオ全体を移行し、最新化します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 10/10/2019
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: migrate
ms.openlocfilehash: f8135b59ed583a2c790b1f78a6b1f940fe4a0acd
ms.sourcegitcommit: 9163a60a28ffce78ceb5dc8dc4fa1b83d7f56e6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2020
ms.locfileid: "86451489"
---
<!-- docsTest:ignore "One Migration" -->
<!-- cSpell:ignore HANA -->

# <a name="one-migration-approach-to-migrating-the-it-portfolio"></a>IT ポートフォリオを移行するための 1 つの移行アプローチ

Azure と Azure Migrate は、どちらも Microsoft テクノロジをホストすることでよく知られています。 しかし、Windows と SQL Server を超えた移行をサポートする Azure の機能については、あまりご存知でないかもしれません。 Migrate のメソドロジで取り込まれた移行シナリオでは、Microsoft とサードパーティの両方のテクノロジを移行するための一貫したガイドラインとプロセスの同じセットを示します。

## <a name="migration-scenarios"></a>移行シナリオ

以下のイメージと一覧は、移行と最新化のための同じ反復的な方法を採用するいくつかの移行シナリオをまとめたものです。

![クラウド導入フレームワーク移行モデル](../_images/migrate/one-migrate.png)

### <a name="links-to-migration-scenarios"></a>拡大移行シナリオへのリンク

| | | | |
|---------|---------|---------|---------|
| **仮想マシン** | [仮想マシン](../migrate/azure-best-practices/contoso-migration-rehost-vm.md) | [Linux サーバー](../migrate/azure-best-practices/contoso-migration-rehost-linux-vm.md) | [仮想デスクトップ](./wvd/index.md) |
| **アプリケーション** | [ASP.NET](../migrate/azure-best-practices/contoso-migration-refactor-web-app-sql.md) | [Java](https://docs.microsoft.com/azure/java/migration-overview?toc=/azure/cloud-adoption-framework/toc.json&bc=/azure/cloud-adoption-framework/_bread/toc.json) | [PHP](../migrate/azure-best-practices/contoso-migration-refactor-linux-app-service-mysql.md) |
| **データ** | [SQL Server](../migrate/azure-best-practices/contoso-migration-rehost-vm-sql-managed-instance.md) | [オープン ソース データベース](../migrate/azure-best-practices/sql-migration.md) | Analytics |
| **ハイブリッド** | [Azure Stack](./azure-stack/index.md) | [VMware](../migrate/azure-best-practices/vmware-host.md) | |
| **その他のシナリオ** | [ワークロードをセキュリティで保護する](../migrate/azure-best-practices/migrate-best-practices-security-management.md) | [メインフレーム](../infrastructure/mainframe-migration/index.md) | NetApp と SAP HANA |

## <a name="migrate-methodology"></a>移行手法

移行の各シナリオでは、同じ一貫したプロセスによって、既存のワークロードのクラウドへの移行作業が進められます。

![クラウド導入フレームワーク移行モデル](../_images/migrate/methodology.png)

複数のワークロードのリリースをガイドするための移行を段階的に構築します。 計画および準備の手法を通じてクラウド導入計画と Azure ランディング ゾーンを確立すると、移行の段階に構造を追加するのに役立ちます。

各イテレーションでは、移行の方法に従って、ワークロードの評価、ワークロードのデプロイ、およびワークロードのリリースを行います。 特定のシナリオに合わせてこれらのプロセスを変更するには、上記の移行シナリオのいずれかをクリックします。

## <a name="next-steps"></a>次のステップ

特定のシナリオの移行でない場合は、まず [4 つの手順の CAF 移行プロセス](../migrate/index.md)に従ってください。
