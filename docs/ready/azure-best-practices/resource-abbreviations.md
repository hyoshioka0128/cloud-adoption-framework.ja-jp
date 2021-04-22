---
title: Azure リソースの種類に推奨される省略形
description: リソースと資産に名前を付けるときに、さまざまな Azure リソースの種類に使用する推奨される省略形を確認します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 4/14/2021
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.custom: internal, readiness, fasttrack-edit
ms.openlocfilehash: f5fc551fb8916b31772ff2e1039bb763414aa814
ms.sourcegitcommit: 734712402f0adf890a61bf9177d153ebace96745
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2021
ms.locfileid: "107528908"
---
# <a name="recommended-abbreviations-for-azure-resource-types"></a>Azure リソースの種類に推奨される省略形

Azure ワークロードは、通常、複数のリソースおよびサービスで構成されます。 Azure リソースの種類を表す名前付けコンポーネントをリソース名に含めると、アプリケーションまたはサービス コンポーネントを視覚的に認識しやすくなります。

この一覧には、名前付け規則に含める、さまざまな Azure リソースの種類に推奨される省略形が示されています。 これらの省略形は、リソース名のプレフィックスとしてよく使用されるため、リソース名のハイフンを許可しないリソースの種類を除き、各省略形は次のようにハイフン (`-`) が後に続きます。 組織のニーズにより適している場合、名前付け規則で、リソースの種類の省略形が名前の別の場所に配置されることがあります。

<!-- cSpell:ignoreRegExp `[a-z]+-?` -->

## <a name="general"></a>全般

| 資産の種類 | リソース プロバイダー名前空間とエンティティ | 省略形 |
|--|--|--|
| API 管理サービス インスタンス | `Microsoft.ApiManagement/service` | `apim-` |
| マネージド ID | `Microsoft.ManagedIdentity/userAssignedIdentities` | `id-` |
| 管理グループ | `Microsoft.Management/managementGroups` | `mg-` |
| ポリシー定義 | `Microsoft.Authorization/policyDefinitions` | `policy-` |
| リソース グループ | `Microsoft.Resources/resourceGroups` | `rg-` |

## <a name="networking"></a>ネットワーク

| 資産の種類 | リソース プロバイダー名前空間とエンティティ | 省略形 |
|--|--|--|
| Application gateway | `Microsoft.Network/applicationGateways` | `agw-` |
| アプリケーション セキュリティ グループ (ASG) | `Microsoft.Network/applicationSecurityGroups` | `asg-` |
| Bastion | `Microsoft.Network/bastionHosts` | `bas-` |
| CDN プロファイル | `Microsoft.Cdn/profiles` | `cdnp-` |
| CDN エンドポイント | `Microsoft.Cdn/profiles/endpoints` | `cdne-` |
| 接続 | `Microsoft.Network/connections`| `con-` |
| DNS | `Microsoft.Network/dnsZones` | `dnsz-` |
| [DNS ゾーン] | `Microsoft.Network/privateDnsZones` | `pdnsz-` |
| ファイアウォール | `Microsoft.Network/azureFirewalls` | `afw-` |
| ExpressRoute 回線 | `Microsoft.Network/expressRouteCircuits` | `erc-` |
| Front Door インスタンス | `Microsoft.Network/frontDoors` | `fd-` |
| Front Door ファイアウォール ポリシー | `Microsoft.Network/frontdoorWebApplicationFirewallPolicies` | `fdfp-`|
| ロード バランサー (内部) | `Microsoft.Network/loadBalancers` | `lbi-`|
| ロード バランサー (外部) | `Microsoft.Network/loadBalancers` | `lbe-`|
| ロード バランサー規則 | `Microsoft.Network/loadBalancers/inboundNatRules` | `rule-`|
| ローカル ネットワーク ゲートウェイ | `Microsoft.Network/localNetworkGateways` | `lgw-` |
| ネットワーク インターフェイス (NIC) | `Microsoft.Network/networkInterfaces` | `nic-`|
| ネットワーク セキュリティ グループ (NSG) | `Microsoft.Network/networkSecurityGroups` | `nsg-` |
| ネットワーク セキュリティ グループ (NSG) のセキュリティ規則 | `Microsoft.Network/networkSecurityGroups/securityRules` | `nsgsr-` |
| Network Watcher | `Microsoft.Network/networkWatchers` | `nw-` |
| Private Link | `"Microsoft.Network/privateLinkServices` | `pl-` |
| パブリック IP アドレス | `Microsoft.Network/publicIPAddresses` | `pip-`|
| パブリック IP アドレス プレフィックス | `Microsoft.Network/publicIPPrefixes` | `ippre-`|
| ルート フィルター | `Microsoft.Network/routeFilters` | `rf-` |
| ルート テーブル | `Microsoft.Network/routeTables` | `rt-` |
| サービス エンドポイント | `Microsoft.serviceEndPointPolicies` | `se-` |
| Traffic Manager プロファイル | `Microsoft.Network/trafficManagerProfiles` | `traf-` |
| ユーザー定義ルート (UDR) | `Microsoft.Network/routeTables/routes` | `udr-` |
| 仮想ネットワーク | `Microsoft.Network/virtualNetworks` | `vnet-`|
| 仮想ネットワーク ピアリング | `Microsoft.Network/virtualNetworks/virtualNetworkPeerings` | `peer-`|
| 仮想ネットワーク サブネット | `Microsoft.Network/virtualNetworks/subnets` | `snet-`|
| Virtual WAN | `Microsoft.Network/virtualWans` | `vwan-`|
| VPN Gateway| `Microsoft.Network/vpnGateways` | `vpng-`|
| VPN 接続 | `Microsoft.Network/vpnGateways/vpnConnections` | `cn-` |
| VPN サイト | `Microsoft.Network/vpnGateways/vpnSites` | `st-` |
| 仮想ネットワーク ゲートウェイ | `Microsoft.Network/virtualNetworkGateways` | `vgw-` |
| Web アプリケーション ファイアウォール (WAF) ポリシー | `Microsoft.Network/firewallPolicies` | `waf` |
| Web アプリケーション ファイアウォール (WAF) ポリシー規則グループ | `Microsoft.Network/firewallPolicies/ruleGroups` | `wafrg` |

## <a name="compute-and-web"></a>コンピューティングと Web

| 資産の種類 | リソース プロバイダー名前空間とエンティティ | 省略形 |
|--|--|--|
| App Service 環境 | `Microsoft.Web/sites` | `ase-` |
| App Service プラン | `Microsoft.Web/serverFarms` | `plan-` |
| 可用性セット | `Microsoft.Compute/availabilitySets` | `avail-` |
| Azure Arc 対応サーバー | `Microsoft.HybridCompute/machines` | `arcs-` |
| Azure Arc 対応 Kubernetes クラスター | `Microsoft.Kubernetes/connectedClusters` | `arck` |
| クラウド サービス | `Microsoft.Compute/cloudServices` | `cld-` |
| ディスク暗号化セット | `Microsoft.Compute/diskEncryptionSets` | `des` |
| 関数アプリ | `Microsoft.Web/sites` | `func-` |
| [ギャラリー] | `Microsoft.Compute/galleries` | `gal` |
| マネージド ディスク (OS) | `Microsoft.Compute/disks` | `osdisk` |
| マネージド ディスク (データ) | `Microsoft.Compute/disks` | `disk` |
| Notification Hubs | `Microsoft.NotificationHubs/namespaces/notificationHubs` | `ntf-` |
| Notification Hubs 名前空間 | `Microsoft.NotificationHubs/namespaces` | `ntfns-` |
| スナップショット | `Microsoft.Compute/snapshots` | `snap-` |
| 静的 Web アプリ | `Microsoft.Web/sites` | `stapp` |
| 仮想マシン | `Microsoft.Compute/virtualMachines` | `vm` |
| 仮想マシン スケール セット | `Microsoft.Compute/virtualMachineScaleSets` | `vmss-` |
| VM ストレージ アカウント | `Microsoft.Storage/storageAccounts` | `stvm` |
| Web アプリ | `Microsoft.Web/sites` | `app-` |

## <a name="containers"></a>Containers

| 資産の種類 | リソース プロバイダー名前空間とエンティティ | 省略形 |
|--|--|--|
| AKS クラスター | `Microsoft.ContainerService/managedClusters` | `aks-` |
| コンテナー レジストリ | `Microsoft.ContainerRegistry/registries` | `cr` |
| コンテナー インスタンス | `Microsoft.ContainerInstance/containerGroups` | `ci` |
| Service Fabric クラスター | `Microsoft.ServiceFabric/clusters` | `sf-` |

## <a name="databases"></a>データベース

| 資産の種類 | リソース プロバイダー名前空間とエンティティ | 省略形 |
|--|--|--|
| Azure Cosmos DB データベース | `Microsoft.DocumentDB/databaseAccounts/sqlDatabases` | `cosmos-` |
| Azure Cache for Redis インスタンス | `Microsoft.Cache/Redis` | `redis-` |
| Azure SQL Database サーバー | `Microsoft.Sql/servers` | `sql-` |
| Azure SQL データベース | `Microsoft.Sql/servers/databases` | `sqldb-` |
| Azure Synapse Analytics | `Microsoft.Synapse/workspaces` | `syn-` |
| Azure Synapse Analytics ワークスペース | `Microsoft.Synapse/workspaces` | `syn-` |
| Azure Synapse Analytics の SQL 専用プール | `Microsoft.Synapse/workspaces/sqlPools` | `syndw-` |
| Azure Synapse Analytics の Spark プール | `Microsoft.Synapse/workspaces/sqlPools` | `synspark-` |
| MySQL データベース | `Microsoft.DBforMySQL/servers` | `mysql-` |
| PostgreSQL データベース | `Microsoft.DBforPostgreSQL/servers` | `psql-` |
| SQL Server Stretch Database | `Microsoft.Sql/servers/databases` | `sqlstrdb-` |
| SQL Managed Instance | `Microsoft.Sql/managedInstances` | `sqlmi-` |

## <a name="storage"></a>ストレージ

| 資産の種類 | リソース プロバイダー名前空間とエンティティ | 省略形 |
|--|--|--|
| ストレージ アカウント | `Microsoft.Storage/storageAccounts` | `st` |
| Azure StorSimple | `Microsoft.StorSimple/managers` | `ssimp` |

## <a name="ai-and-machine-learning"></a>AI および機械学習

| 資産の種類 | リソース プロバイダー名前空間とエンティティ | 省略形 |
|--|--|--|
| Azure Cognitive Search | `Microsoft.Search/searchServices` | `srch-` |
| Azure Cognitive Services | `Microsoft.CognitiveServices/accounts` | `cog-` |
| Azure Machine Learning ワークスペース | `Microsoft.MachineLearningServices/workspaces` | `mlw-` |

## <a name="analytics-and-iot"></a>Analytics と IoT

| 資産の種類 | リソース プロバイダー名前空間とエンティティ | 省略形 |
|--|--|--|
| Azure Analysis Services サーバー | `Microsoft.AnalysisServices/servers` | `as` |
| Azure Databricks ワークスペース | `Microsoft.Databricks/workspaces` | `dbw-` |
| Azure Stream Analytics | `Microsoft.StreamAnalytics/cluster` | `asa-` |
| Azure Data Explorer クラスター | `Microsoft.Kusto/clusters` | `dec` |
| Azure Data Explorer のクラスター データベース | `Microsoft.Kusto/clusters/databases` | `dedb` |
| Azure Data Factory | `Microsoft.DataFactory/factories` | `adf-` |
| Data Lake Store アカウント | `Microsoft.DataLakeStore/accounts` | `dls` |
| Data Lake Analytics アカウント | `Microsoft.DataLakeAnalytics/accounts` | `dla` |
| Event Hubs 名前空間 | `Microsoft.EventHub/namespaces` | `evhns-` |
| イベント ハブ | `Microsoft.EventHub namespaces/eventHubs` | `evh-` |
| Event Grid ドメイン | `Microsoft.EventGrid/domains` | `evgd-` |
| Event Grid トピック | `Microsoft.EventGrid/domains/topics` | `evgt-` |
| HDInsight - Hadoop クラスター | `Microsoft.HDInsight/clusters` | `hadoop-` |
| HDInsight - HBase クラスター | `Microsoft.HDInsight/clusters` | `hbase-` |
| HDInsight - Kafka クラスター | `Microsoft.HDInsight/clusters` | `kafka-` |
| HDInsight - Spark クラスター | `Microsoft.HDInsight/clusters` | `spark-` |
| HDInsight - Storm クラスター | `Microsoft.HDInsight/clusters` | `storm-` |
| HDInsight - ML Services クラスター | `Microsoft.HDInsight/clusters` | `mls-` |
| IoT ハブ | `Microsoft.Devices/IotHubs` | `iot-` |
| プロビジョニング サービス | `Microsoft.Devices/provisioningServices` | `provs-` |
| プロビジョニング サービス証明書 | `Microsoft.Devices/provisioningServices/certificates` | `pcert-` |
| Power BI Embedded | `Microsoft.PowerBIDedicated/capacities` | `pbi-` |
| Time Series Insights 環境 | `Microsoft.TimeSeriesInsights/environments` | `tsi-` |

## <a name="developer-tools"></a>開発者用ツール

| 資産の種類 | リソース プロバイダー名前空間とエンティティ | 省略形 |
|--|--|--|
| App Configuration ストア | `Microsoft.AppConfiguration/configurationStores` | `appcs-` |
| Azure Static Web Apps | `Microsoft.Web/sites` | `stap-` |
| SignalR | `Microsoft.SignalRService/SignalR` | `sigr` |

## <a name="integration"></a>統合

| 資産の種類 | リソース プロバイダー名前空間とエンティティ | 省略形 |
|--|--|--|
| 統合アカウント | `Microsoft.Logic/integrationAccounts` | `ia-` |
| ロジック アプリ | `Microsoft.Logic/workflows` | `logic-` |
| Service Bus | `Microsoft.ServiceBus/namespaces` | `sb-` |
| Service Bus キュー | `Microsoft.ServiceBus/namespaces/queues` | `sbq-` |
| Service Bus トピック | `Microsoft.ServiceBus/namespaces/topics` | `sbt-` |

## <a name="management-and-governance"></a>管理とガバナンス

| 資産の種類 | リソース プロバイダー名前空間とエンティティ | 省略形 |
|--|--|--|
| Automation アカウント | `Microsoft.Automation/automationAccounts` | `aa-` |
| Application Insights | `Microsoft.Insights/components` | `appi-` |
| Azure Monitor アクション グループ | `Microsoft.Insights/actionGroups` | `ag-` |
| Azure Purview インスタンス | `Microsoft.Purview/accounts` | `pview-` |
| ブループリント | `Microsoft.Blueprint/blueprints` | `bp-` |
| ブループリント割り当て | `Microsoft.Blueprint/blueprints/artifacts` | `bpa-` |
| Key Vault | `Microsoft.KeyVault/vaults` | `kv-` |
| Log Analytics ワークスペース | `Microsoft.OperationalInsights/workspaces` | `log-` |

## <a name="migration"></a>移行

| 資産の種類 | リソース プロバイダー名前空間とエンティティ | 省略形 |
|--|--|--|
| Azure Migrate プロジェクト | `Microsoft.Migrate/assessmentProjects` | `migr-` |
| Database Migration Service インスタンス | `Microsoft.DataMigration/services` | `dms-` |
| Recovery Services コンテナー | `Microsoft.RecoveryServices/vaults` | `rsv-` |

## <a name="deprecated-product-names"></a>非推奨の製品名

| 資産の種類 | リソース プロバイダー名前空間とエンティティ | 省略形 |
|--|--|--|
| Azure SQL Data Warehouse | `Microsoft.Sql/servers` | `sqldw-` |

## <a name="next-steps"></a>次のステップ

Azure のリソースと資産のタグ付けに関する推奨事項を確認します。

> [!div class="nextstepaction"]
> [タグ付けの戦略を定義する](./resource-tagging.md)
