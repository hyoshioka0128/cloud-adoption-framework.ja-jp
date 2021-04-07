---
title: Azure リソースの種類に推奨される省略形
description: リソースと資産に名前を付けるときに、さまざまな Azure リソースの種類に使用する推奨される省略形を確認します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 12/01/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.custom: internal, readiness, fasttrack-edit
ms.openlocfilehash: 1b69b833bb6ce6dfb06e597714ee08823b899734
ms.sourcegitcommit: 2aeb418ad97f3bdb7ce1e5ef76c0f3cbea9a57e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2021
ms.locfileid: "106503303"
---
# <a name="recommended-abbreviations-for-azure-resource-types"></a>Azure リソースの種類に推奨される省略形

Azure ワークロードは、通常、複数のリソースおよびサービスで構成されます。 Azure リソースの種類を表す名前付けコンポーネントをリソース名に含めると、アプリケーションまたはサービス コンポーネントを視覚的に認識しやすくなります。

この一覧には、名前付け規則に含める、さまざまな Azure リソースの種類に推奨される省略形が示されています。 これらの省略形は、リソース名のプレフィックスとしてよく使用されるため、リソース名のハイフンを許可しないリソースの種類を除き、各省略形は次のようにハイフン (`-`) が後に続きます。 組織のニーズにより適している場合、名前付け規則で、リソースの種類の省略形が名前の別の場所に配置されることがあります。

<!-- cSpell:ignoreRegExp `[a-z]+-?` -->

## <a name="general"></a>全般

| 資産の種類 | 省略形 |
|--|--|
| 管理グループ | `mg-` |
| Resource group | `rg-` |
| ポリシー定義 | `policy-` |
| API 管理サービス インスタンス | `apim-` |
| マネージド ID | `id-` |

## <a name="networking"></a>ネットワーク

| 資産の種類 | 省略形 |
|--|--|
| 仮想ネットワーク | `vnet-` |
| Subnet | `snet-` |
| 仮想ネットワーク ピアリング | `peer-` |
| ネットワーク インターフェイス (NIC) | `nic-` |
| パブリック IP アドレス | `pip-` |
| ロード バランサー (内部) | `lbi-` |
| ロード バランサー (外部) | `lbe-` |
| ネットワーク セキュリティ グループ (NSG) | `nsg-` |
| アプリケーション セキュリティ グループ (ASG) | `asg-` |
| ローカル ネットワーク ゲートウェイ | `lgw-` |
| 仮想ネットワーク ゲートウェイ | `vgw-` |
| VPN 接続 | `cn-` |
| ExpressRoute 回線 | `erc-` |
| Application gateway | `agw-` |
| ルート テーブル | `route-` |
| ユーザー定義ルート (UDR) | `udr-` |
| Traffic Manager プロファイル | `traf-` |
| フロント ドア | `fd-` |
| CDN プロファイル | `cdnp-` |
| CDN エンドポイント | `cdne-` |
| Web アプリケーション ファイアウォール (WAF) ポリシー | `waf` |

## <a name="compute-and-web"></a>コンピューティングと Web

| 資産の種類 | 省略形 |
|--|--|
| 仮想マシン | `vm` |
| 仮想マシン スケール セット | `vmss-` |
| 可用性セット | `avail-` |
| マネージド ディスク (OS) | `osdisk` |
| マネージド ディスク (データ) | `disk` |
| VM ストレージ アカウント | `stvm` |
| Azure Arc 対応サーバー | `arcs-` |
| Azure Arc 対応 Kubernetes クラスター | `arck` |
| コンテナー レジストリ | `cr` |
| コンテナー インスタンス | `ci-` |
| AKS クラスター | `aks-` |
| Service Fabric クラスター | `sf-` |
| App Service 環境 | `ase-` |
| App Service プラン | `plan-` |
| Web アプリ | `app-` |
| 静的 Web アプリ | `stapp` |
| 関数アプリ | `func-` |
| クラウド サービス | `cld-` |
| Notification Hubs | `ntf-` |
| Notification Hubs 名前空間 | `ntfns-` |

## <a name="databases"></a>データベース

| 資産の種類 | 省略形 |
|--|--|
| Azure SQL Database サーバー | `sql-` |
| Azure SQL データベース | `sqldb-` |
| Azure Cosmos DB データベース | `cosmos-` |
| Azure Cache for Redis インスタンス | `redis-` |
| MySQL データベース | `mysql-` |
| PostgreSQL データベース | `psql-` |
| Azure SQL Data Warehouse | `sqldw-` |
| Azure Synapse Analytics | `syn-` |
| SQL Server Stretch Database | `sqlstrdb-` |
| SQL Managed Instance | `sqlmi-` |

## <a name="storage"></a>ストレージ

| 資産の種類 | 省略形 |
|--|--|
| ストレージ アカウント | `st` |
| Azure StorSimple | `ssimp` |
| Azure Container Registry | `acr` |

## <a name="ai-and-machine-learning"></a>AI および機械学習

| 資産の種類 | 省略形 |
|--|--|
| Azure Cognitive Search | `srch-` |
| Azure Cognitive Services | `cog-` |
| Azure Machine Learning ワークスペース | `mlw-` |

## <a name="analytics-and-iot"></a>Analytics と IoT

| 資産の種類 | 省略形 |
|--|--|
| Azure Analysis Services サーバー | `as` |
| Azure Databricks ワークスペース | `dbw-` |
| Azure Stream Analytics | `asa-` |
| Azure Data Explorer クラスター | `dec` |
| Azure Data Factory | `adf-` |
| Data Lake Store アカウント | `dls` |
| Data Lake Analytics アカウント | `dla` |
| Event Hubs 名前空間 | `evhns-` |
| イベント ハブ | `evh-` |
| Event Grid ドメイン | `evgd-` |
| Event Grid トピック | `evgt-` |
| HDInsight - Hadoop クラスター | `hadoop-` |
| HDInsight - HBase クラスター | `hbase-` |
| HDInsight - Kafka クラスター | `kafka-` |
| HDInsight - Spark クラスター | `spark-` |
| HDInsight - Storm クラスター | `storm-` |
| HDInsight - ML Services クラスター | `mls-` |
| IoT ハブ | `iot-` |
| Power BI Embedded | `pbi-` |
| Time Series Insights 環境 | `tsi-` |

## <a name="developer-tools"></a>開発者用ツール

| 資産の種類 | 省略形 |
|---|---|
| App Configuration ストア | `appcs-` |
| Azure Static Web Apps | `stap-` |

## <a name="integration"></a>統合

| 資産の種類 | 省略形 |
|--|--|
| 統合アカウント | `ia-` |
| ロジック アプリ | `logic-` |
| Service Bus | `sb-` |
| Service Bus キュー | `sbq-` |
| Service Bus トピック | `sbt-` |

## <a name="management-and-governance"></a>管理とガバナンス

| 資産の種類 | 省略形 |
|--|--|
| Automation アカウント | `aa-` |
| Azure Monitor アクション グループ | `ag-` |
| Azure Purview インスタンス | `pview-` |
| ブループリント | `bp-` |
| ブループリント割り当て | `bpa-` |
| Key Vault | `kv-` |
| Log Analytics ワークスペース | `log-` |
| Application Insights | `appi-` |

## <a name="migration"></a>移行

| 資産の種類 | 省略形 |
|--|--|
| Azure Migrate プロジェクト | `migr-` |
| Database Migration Service インスタンス | `dms-` |
| Recovery Services コンテナー | `rsv-` |

## <a name="next-steps"></a>次のステップ

Azure のリソースと資産のタグ付けに関する推奨事項を確認します。

> [!div class="nextstepaction"]
> [タグ付けの戦略を定義する](./resource-tagging.md)
