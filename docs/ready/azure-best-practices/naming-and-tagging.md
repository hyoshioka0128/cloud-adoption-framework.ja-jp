---
title: '準備完了: 推奨される名前付けおよびタグ付け規則'
titleSuffix: Microsoft Cloud Adoption Framework for Azure
description: この記事では、エンタープライズ クラウド導入作業のサポートを明確に対象にした、リソースの命名とタグ付けに関する詳細な推奨事項を示します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 04/01/2019
ms.topic: guide
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.custom: readiness
ms.openlocfilehash: 9caeca52ba0ab3a909b0f42ac6f016d44033a4ee
ms.sourcegitcommit: 617c3f12a3657a8a1393fd08d261dd98eb81b65c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/14/2019
ms.locfileid: "74086798"
---
# <a name="ready-recommended-naming-and-tagging-conventions"></a>準備完了: 推奨される名前付けおよびタグ付け規則

運用管理を支援し、アカウンティング要件をサポートする方法でのクラウド ベースの資産の整理は、大規模なクラウド導入作業が直面する一般的な課題です。 明確に定義された名前付け規則とメタデータのタグ付け規則をクラウドでホストされているリソースに適用すると、IT スタッフはリソースをすばやく見つけて管理できます。 名前とタグが明確に定義されていると、ビジネス チームとの間でチャージバックとショーバックのアカウンティング メカニズムを使用してクラウドの使用コストを調整する場合にも役立ちます。

Azure アーキテクチャ センターの [Azure リソースの名前付け規則と制限事項](https://docs.microsoft.com/azure/architecture/best-practices/resource-naming)のガイダンスに、一般的な推奨事項とプラットフォームの制限事項が示されています。 次の説明では、その一般的なガイダンスを、特にエンタープライズ クラウド導入作業のサポートを対象にしたより詳細な推奨事項を使用して拡張しています。

リソース名は変更が困難な場合があります。 大規模なクラウドのデプロイを開始する前に、クラウド導入チームは、包括的な名前付け規則を確立することを優先事項にします。

> [!NOTE]
> 企業ごとに組織上および管理上の要件は異なります。 この記事の推奨事項は、クラウド導入チーム内での議論の出発点として利用できます。
>
> これらの議論が進んだら、次のテンプレートを使用して、これらの推奨事項を特定のビジネス ニーズに整合させるときに行う名前付けとタグ付けに関する決定をキャプチャしてください。
>
> [名前付けおよびタグ付け規則の追跡テンプレート](https://archcenter.blob.core.windows.net/cdn/fusion/readiness/CAF%20Readiness%20Naming%20and%20Tagging%20tracking%20template.xlsx)をダウンロードします。

## <a name="naming-and-tagging-resources"></a>リソースの名前付けとタグ付け

名前付けおよびタグ付け戦略には、リソース名とメタデータ タグのコンポーネントとしてビジネスおよび運用の詳細が含まれます。

- この戦略のビジネス関連の面では、リソースの名前とタグに、チームを識別するために必要な組織情報が確実に含まれるようにします。 リソース コストを担当するビジネス オーナーと共にリソースを使用します。
- 運用の面では、名前とタグに、ワークロード、アプリケーション、環境、重要度、およびリソースの管理に役立つその他の情報を識別するために IT チームが使用する情報が確実に含まれるようにします。

### <a name="resource-naming"></a>リソースの名前付け

効果的な名前付け規則では、重要なリソース情報をリソースの名前の一部として使用してリソース名を組み立てます。 たとえば、[この記事の後の方で](#sample-naming-convention)説明されている推奨される名前付け規則を使用すると、運用 SharePoint ワークロードのパブリック IP リソースは `pip-sharepoint-prod-westus-001` のような名前になります。

その名前から、リソースの種類、それに関連付けられたワークロード、そのデプロイ環境、およびそれをホストしている Azure リージョンをすばやく識別できます。

#### <a name="naming-scope"></a>名前付けスコープ

すべての Azure リソースの種類には、リソース名が一意である必要があるレベルを定義するスコープがあります。 リソースには、そのスコープ内の一意の名前が必要です。

たとえば、仮想ネットワークにはリソース グループ スコープがあります。つまり、指定されたリソース グループには `vnet-prod-westus-001` という名前の 1 つのネットワークだけが存在できます。 他のリソース グループには `vnet-prod-westus-001` という名前の独自の仮想ネットワークが存在できます。 別の例を示すと、サブネットはスコープが仮想ネットワークに設定されています。つまり、仮想ネットワーク内の各サブネットの名前は一意である必要があります。

パブリック エンドポイントを含む PaaS サービスや仮想マシンの DNS ラベルなどの一部のリソースの名前にはグローバル スコープがあります。つまり、それらは Azure プラットフォーム全体にわたって一意である必要があります。

リソース名には長さの制限があります。 名前付け規則を作成するときに、名前に埋め込まれるコンテキストおよびそのスコープと長さのバランスをとることが重要です。 リソースの種類ごとの許可される文字、スコープ、および名前の長さに関連した名前付け規則の詳細については、「[Azure リソースの名前付け規則](/azure/architecture/best-practices/resource-naming)」を参照してください。

#### <a name="recommended-naming-components"></a>推奨される名前付けコンポーネント

名前付け規則を作成する場合は、リソース名に反映させる重要な情報を特定します。 リソースの種類によって関連する情報は異なります。 次の一覧は、リソース名を作成するときに役立つ情報の例を示しています。

リソース名の長さの制限を超えないようにするために、名前付けコンポーネントの長さは短いままにしてください。

| 名前付けコンポーネント | 説明 | 例 |
| --- | --- | --- |
| 事業単位 | リソースが属しているサブスクリプションまたはワークロードを所有する会社の最上位の部門。 小規模な組織では、このコンポーネントは 1 つの会社の最上位の組織要素を表す可能性があります。 | *fin*、*mktg*、*product*、*it*、*corp* |
| サブスクリプションの種類 | リソースが含まれているサブスクリプションの目的に関する概要の説明。 多くの場合は、デプロイ環境の種類または特定のワークロードによって分類されます。 | *prod*、*shared、client* |
| アプリケーションまたはサービス名 | リソースが属しているアプリケーション、ワークロード、またはサービスの名前。 | *navigator*、*emissions*、*sharepoint*、*hadoop* |
| デプロイ環境 | リソースによってサポートされているワークロードの開発ライフサイクルのステージ。 | *prod、dev、qa、stage、test* |
| リージョン | リソースがデプロイされている Azure リージョン。 | *westus、eastus2、westeurope、usgovia* |

#### <a name="recommended-resource-type-prefixes"></a>推奨されるリソースの種類のプレフィックス

各ワークロードは、多数の個々のリソースやサービスで構成できます。 リソースの種類のプレフィックスをリソース名に組み込むと、アプリケーションまたはサービス コンポーネントの視覚的な識別が簡単になります。

次の一覧は、名前付け規則を定義する場合に使用すべき推奨される Azure リソースの種類のプレフィックスを示しています。

| リソースの種類                       | リソース名のプレフィックス |
| ----------------------------------- | -------------------- |
| Resource group                      | rg-                  |
| Azure Virtual Network               | vnet-                |
| 仮想ネットワーク ゲートウェイ             | vnet-gw-             |
| ゲートウェイ接続                  | cn-                  |
| Subnet                              | snet-                |
| ネットワーク セキュリティ グループ              | nsg-                 |
| ルート テーブル                         | route-               |
| Azure Virtual Machines              | vm-                  |
| VM ストレージ アカウント                  | stvm                 |
| パブリック IP                           | pip-                 |
| Azure Load Balancer                 | lb-                  |
| NIC                                 | nic-                 |
| Azure Key Vault                     | kv-                  |
| Azure Kubernetes Service            | aks-                 |
| Azure Service Bus                   | sb-                  |
| Azure Service Bus キュー            | sbq-                 |
| Azure App Service アプリ              | azapp-               |
| Azure Functions アプリ                | azfun-               |
| Azure クラウド サービス                | azcs-                |
| Azure SQL Database                  | sqldb-               |
| Azure Cosmos DB (旧称 Azure DocumentDB) | cosdb-               |
| Azure Cache for Redis               | redis-               |
| Azure Database for MySQL            | mysql-               |
| Azure SQL Data Warehouse            | sqldw-               |
| SQL Server Stretch Database         | sqlstrdb-            |
| Azure Storage                       | stor                 |
| Azure StorSimple                    | ssimp                |
| Azure Search                        | srch-                |
| Azure Cognitive Services            | cs-                  |
| Azure Machine Learning ワークスペース    | aml-                 |
| Azure Data Lake Storage             | dls                  |
| Azure Data Lake Analytics           | dla                  |
| Azure HDInsight - Spark             | hdis-                |
| Azure HDInsight - Hadoop            | hdihd-               |
| Azure HDInsight - Microsoft R Server          | hdir-                |
| Azure HDInsight - HBase             | hdihb-               |
| Power BI Embedded                   | pbiemb               |
| Azure Stream Analytics              | asa-                 |
| Azure Data Factory                  | df-                  |
| Azure Event Hubs                    | evh-                 |
| Azure IoT Hub                       | aih-                 |
| Azure 通知ハブ             | anh-                 |
| Azure Notification Hubs 名前空間   | anhns-               |

### <a name="metadata-tags"></a>メタデータ タグ

クラウド リソースにメタデータ タグを適用すると、リソース名に含めることができなかったそれらの資産に関する情報を含めることができます。 その情報を使用して、リソースに対してより高度なフィルター処理およびレポート作成を実行できます。 これらのタグには、リソースの関連するワークロードまたはアプリケーション、運用要件、および所有者情報に関するコンテキストを含めることができます。 IT チームやビジネス チームはこの情報を使用して、リソースを見つけたり、リソースの使用状況や請求に関するレポートを生成したりできます。

どのタグをリソースに適用するか、どのタグが必須か任意かは、組織によって異なります。 次の一覧は、リソースに関する重要なコンテキストと情報を取得する一般的なタグの例です。 この一覧を出発点として使用し、独自のタグ付け規則を指定してください。

| タグ名                  | 説明                                                                                                                                                                                                    | Key               | 値の例                                   |
|---------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------|-------------------------------------------------|
| アプリケーション名          | リソースが関連付けられているアプリケーション、サービス、またはワークロードの名前。                                                                                                                                 | *ApplicationName* | *{app name}*                                    |
| 承認者名             | このリソースに関連したコストの承認を担当する人物。                                                                                                                                               | *Approver*        | *{email}*                                       |
| 必要な予算/承認された予算  | このアプリケーション、サービス、またはワークロードに割り当てられた金額。                                                                                                                                                    | *BudgetAmount*    | *{\$}*                                          |
| 事業単位             | リソースが属しているサブスクリプションまたはワークロードを所有する会社の最上位の部門。 小規模な組織では、このタグは 1 つの会社の、または共有された最上位の組織要素を表す可能性があります。 | *BusinessUnit*    | *FINANCE、MARKETING、{Product Name}、CORP、SHARED* |
| コスト センター               | このリソースに関連付けられたアカウンティング コスト センター。                                                                                                                                                          | *CostCenter*      | *{number}*                                      |
| 障害復旧         | アプリケーション、ワークロード、またはサービスのビジネス上の重要度。                                                                                                                                                | *DR*              | *Mission-critical、Critical、Essential*         |
| プロジェクトの終了日   | アプリケーション、ワークロード、またはサービスの使用停止がスケジュールされている日付。                                                                                                                                  | *EndDate*         | *{date}*                                        |
| 環境               | アプリケーション、ワークロード、またはサービスのデプロイ環境。                                                                                                                                              | *Env*             | *Prod、Dev、QA、Stage、Test*                    |
| 所有者名                | このアプリケーション、ワークロード、またはサービスの所有者。                                                                                                                                                                | *Owner*           | *{email}*                                       |
| 要求者名            | このアプリケーションの作成を要求したユーザー。                                                                                                                                                          | *Requestor*       | *{email}*                                       |
| サービス クラス             | アプリケーション、ワークロード、またはサービスのサービス レベル アグリーメント レベル。                                                                                                                                       | *ServiceClass*    | *Dev、Bronze、Dev、Gold*                     |
| プロジェクトの開始日 | アプリケーション、ワークロード、またはサービスが最初にデプロイされた日付。                                                                                                                                           | *開始日*       | *{date}*                                        |

## <a name="sample-naming-convention"></a>サンプルの名前付け規則

次のセクションでは、エンタープライズ クラウド デプロイ中にデプロイされた一般的な Azure リソースの種類の名前付けスキームの例を示します。

<!-- markdownlint-disable MD033 -->

### <a name="subscriptions"></a>Subscriptions

| 資産の種類   | Scope (スコープ)                        | 形式                                             | 例                                     |
|--------------|------------------------------|----------------------------------------------------|----------------------------------------------|
| Subscription | アカウント/Enterprise Agreement | \<Business Unit\>-\<Subscription type\>-\<\#\#\#\> | <ul><li>mktg-prod-001 </li><li>corp-shared-001 </li><li>fin-client-001</li></ul> |

### <a name="resource-groups"></a>リソース グループ

| 資産の種類     | Scope (スコープ)        | 形式                                                     | 例                                                                            |
|----------------|--------------|------------------------------------------------------------|-------------------------------------------------------------------------------------|
| Resource group | Subscription | rg-\<App or Service name\>-\<Subscription type\>-\<\#\#\#\> | <ul><li>rg-mktgsharepoint-prod-001 </li><li>rg-acctlookupsvc-share-001 </li><li>rg-ad-dir-services-shared-001</li></ul> |

### <a name="virtual-networking"></a>仮想ネットワーク

| 資産の種類               | Scope (スコープ)           | 形式                                                                | 例                                                                                              |
|--------------------------|-----------------|-----------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------|
| Azure Virtual Network          | Resource group  | vnet-\<Subscription type\>-\<Region\>-\<\#\#\#\>                      | <ul><li>vnet-shared-eastus2-001 </li><li>vnet-prod-westus-001 </li><li>vnet-client-eastus2-001</li></ul>                                  |
| 仮想ネットワークの仮想ゲートウェイ     | 仮想ネットワーク | vnet-gw-v-\<Subscription type\>-\<Region\>-\<\#\#\#\>                 | <ul><li>vnet-gw-v-shared-eastus2-001 </li><li>vnet-gw-v-prod-westus-001 </li><li>vnet-gw-v-client-eastus2-001</li></ul>                   |
| 仮想ネットワークのローカル ゲートウェイ       | 仮想ゲートウェイ | vnet-gw-l-\<Subscription type\>-\<Region\>-\<\#\#\#\>                 | <ul><li>vnet-gw-l-shared-eastus2-001 </li><li>vnet-gw-l-prod-westus-001 </li><li>vnet-gw-l-client-eastus2-001</li></ul>                   |
| サイト間接続 | Resource group  | cn-\<local gateway name\>-to-\<virtual gateway name\>                 | <ul><li>cn-l-gw-shared-eastus2-001-to-v-gw-shared-eastus2-001 </li><li>cn-l-gw-shared-eastus2-001-to-shared-westus-001</li></ul> |
| 仮想ネットワーク接続         | Resource group  | cn-\<subscription1\>\<region1\>-to-\<subscription2\>\<region2\>-      | <ul><li>cn-shared-eastus2-to-shared-westus </li><li>cn-prod-eastus2-to-prod-westus</li></ul>                                     |
| Subnet                   | 仮想ネットワーク | snet-\<subscription\>-\<subregion\>-\<\#\#\#\>                       | <ul><li>snet-shared-eastus2-001 </li><li>snet-prod-westus-001 </li><li>snet-client-eastus2-001</li></ul>                                  |
| ネットワーク セキュリティ グループ   | サブネットまたは NIC   | nsg-\<policy name or appname\>-\<\#\#\#\>                             | <ul><li>nsg-weballow-001 </li><li>nsg-rdpallow-001 </li><li>nsg-sqlallow-001 </li><li>nsg-dnsbloked-001</li></ul>                                  |
| パブリック IP                | Resource group  | pip-\<vm name or app name\>-\<Environment\>-\<subregion\>-\<\#\#\#\> | <ul><li>pip-dc1-shared-eastus2-001 </li><li>pip-hadoop-prod-westus-001</li></ul>                                                 |

### <a name="azure-virtual-machines"></a>Azure Virtual Machines

| 資産の種類         | Scope (スコープ)          | 形式                                                              | 例                                                                             |
|--------------------|----------------|---------------------------------------------------------------------|--------------------------------------------------------------------------------------|
| Azure Virtual Machines    | Resource group | vm\<policy name or appname\>\<\#\#\#\>                              | <ul><li>vmnavigator001 </li><li>vmsharepoint001 </li><li>vmsqlnode001 </li><li>vmhadoop001</li></ul>                              |
| VM ストレージ アカウント | グローバル         | stvm\<performance type\>\<appname or prodname\>\<region\>\<\#\#\#\> | <ul><li>stvmstcoreeastus2001 </li><li>stvmpmcoreeastus2001 </li><li>stvmstplmeastus2001 </li><li>stvmsthadoopeastus2001</li></ul> |
| DNS ラベル          | グローバル         | \<A record of vm\>.[\<region\>.cloudapp.azure.com]                  | <ul><li>dc1.westus.cloudapp.azure.com </li><li>web1.eastus2.cloudapp.azure.com</li></ul>                        |
| Azure Load Balancer      | Resource group | lb-\<app name or role\>\<Environment\>\<\#\#\#\>                    | <ul><li>lb-navigator-prod-001 </li><li>lb-sharepoint-dev-001</li></ul>                                          |
| NIC                | Resource group | nic-\<\#\#\>-\<vmname\>-\<subscription\>\<\#\#\#\>                  | <ul><li>nic-01-dc1-shared-001 </li><li>nic-02-vmhadoop1-prod-001 </li><li>nic-02-vmtest1-client-001</li></ul>            |

### <a name="paas-services"></a>PaaS サービス

| 資産の種類     | Scope (スコープ)  | 形式                                                              | 例                                                                                 |
|----------------|--------|---------------------------------------------------------------------|------------------------------------------------------------------------------------------|
| Azure App Service    | グローバル | azapp-\<App Name\>-\<Environment\>-\<\#\#\#\>.[{azurewebsites.net}] | <ul><li>azapp-navigator-prod-001.azurewebsites.net </li><li>azapp-accountlookup-dev-001.azurewebsites.net</li></ul> |
| Azure Functions アプリ   | グローバル | azfun-\<App Name\>-\<Environment\>-\<\#\#\#\>.[{azurewebsites.net}] | <ul><li>azfun-navigator-prod-001.azurewebsites.net </li><li>azfun-accountlookup-dev-001.azurewebsites.net</li></ul> |
| Azure クラウド サービス | グローバル | azcs-\<App Name\>-\<Environment\>-\<\#\#\#\>.[{cloudapp.net}]       | <ul><li>azcs-navigator-prod-001.azurewebsites.net </li><li>azcs-accountlookup-dev-001.azurewebsites.net</li></ul>   |

### <a name="azure-service-bus"></a>Azure Service Bus

| 資産の種類         | Scope (スコープ)       | 形式                                                     | 例                           |
|--------------------|-------------|------------------------------------------------------------|------------------------------------|
| Azure Service Bus        | グローバル      | sb-\<App Name\>-\<Environment\>.[{servicebus.windows.net}] | <ul><li>sb-navigator-prod </li><li>sb-emissions-dev</li></ul> |
| Azure Service Bus キュー | Service Bus | sbq-\<query descriptor\>                                   | <ul><li>sbq-messagequery</li></ul>                   |

### <a name="databases"></a>データベース

| 資産の種類                          | Scope (スコープ)              | 形式                                | 例                                       |
|-------------------------------------|--------------------|---------------------------------------|------------------------------------------------|
| Azure SQL Database                  | グローバル             | sqldb-\<App Name\>-\<Environment\>    | <ul><li>sqldb-navigator-prod </li><li>sqldb-emissions-dev</li></ul>       |
| Azure Cosmos DB (旧称 DocumentDB) | グローバル             | cosdb-\<App Name\>-\<Environment\>    | <ul><li>cosdb-navigator-prod </li><li>cosdb-emissions-dev</li></ul>       |
| Azure Cache for Redis               | グローバル             | redis-\<App Name\>-\<Environment\>    | <ul><li>redis-navigator-prod </li><li>redis-emissions-dev</li></ul>       |
| Azure Database for MySQL            | グローバル             | mysql-\<App Name\>-\<Environment\>    | <ul><li>mysql-navigator-prod </li><li>mysql-emissions-dev</li></ul>       |
| Azure SQL Data Warehouse                  | グローバル             | sqldw-\<App Name\>-\<Environment\>    | <ul><li>sqldw-navigator-prod </li><li>sqldw-emissions-dev</li></ul>       |
| SQL Server Stretch Database         | Azure SQL Database | sqlstrdb-\<App Name\>-\<Environment\> | <ul><li>sqlstrdb-navigator-prod </li><li>sqlstrdb-emissions-dev</li></ul> |

### <a name="storage"></a>Storage

| 資産の種類                              | Scope (スコープ)  | 形式                                                                        | 例                                   |
|-----------------------------------------|--------|-------------------------------------------------------------------------------|--------------------------------------------|
| Azure Storage アカウント - 一般的な使用     | グローバル | st\<storage name\>\<\#\#\#\>                                                  | <ul><li>stnavigatordata001 </li><li>stemissionsoutput001</li></ul>    |
| Azure Storage アカウント - 診断ログ | グローバル | stdiag\<first 2 letters of subscription name and number\>\<region\>\<\#\#\#\> | <ul><li>stdiagsh001eastus2001 </li><li>stdiagsh001westus001</li></ul> |
| Azure StorSimple                              | グローバル | ssimp\<App Name\>\<Environment\>                                              | <ul><li>ssimpnavigatorprod </li><li>ssimpemissionsdev</li></ul>       |

### <a name="ai--machine-learning"></a>AI + 機械学習

| 資産の種類                       | Scope (スコープ)          | 形式                            | 例                               |
|----------------------------------|----------------|-----------------------------------|----------------------------------------|
| Azure Search                     | グローバル         | srch-\<App Name\>-\<Environment\> | <ul><li>srch-navigator-prod </li><li>srch-emissions-dev</li></ul> |
| Azure Cognitive Services               | Resource group | cs-\<App Name\>-\<Environment\>   | <ul><li>cs-navigator-prod </li><li>cs-emissions-dev</li></ul>     |
| Azure Machine Learning ワークスペース | Resource group | aml-\<App Name\>-\<Environment\>  | <ul><li>aml-navigator-prod </li><li>aml-emissions-dev</li></ul>   |

### <a name="analytics"></a>Analytics

| 資産の種類                | Scope (スコープ)  | 形式                             | 例                                 |
|---------------------------|--------|------------------------------------|------------------------------------------|
| Azure Data Factory        | グローバル | df-\<App Name\>\<Environment\>     | <ul><li>df-navigator-prod </li><li>df-emissions-dev</li></ul>       |
| Azure Data Lake Storage   | グローバル | dls\<App Name\>\<Environment\>     | <ul><li>dlsnavigatorprod </li><li>dlsemissionsdev</li></ul>         |
| Azure Data Lake Analytics | グローバル | dla\<App Name\>\<Environment\>     | <ul><li>dlanavigatorprod </li><li>dlaemissionsdev</li></ul>         |
| Azure HDInsight - Spark         | グローバル | hdis-\<App Name\>-\<Environment\>  | <ul><li>hdis-navigator-prod </li><li>hdis-emissions-dev </li></ul>  |
| Azure HDInsight - Hadoop        | グローバル | hdihd-\<App Name\>-\<Environment\> | <ul><li>hdihd-hadoop-prod </li><li>hdihd-emissions-dev</li></ul>    |
| Azure HDInsight - Microsoft R Server      | グローバル | hdir-\<App Name\>-\<Environment\>  | <ul><li>hdir-navigator-prod </li><li>hdir-emissions-dev</li></ul>   |
| Azure HDInsight - HBase         | グローバル | hdihb-\<App Name\>-\<Environment\> | <ul><li>hdihb-navigator-prod </li><li>hdihb-emissions-dev</li></ul> |
| Power BI Embedded         | グローバル | pbiemb\<App Name\>\<Environment\>  | <ul><li>pbiem-navigator-prod </li><li>pbiem-emissions-dev</li></ul> |

### <a name="internet-of-things-iot"></a>モノのインターネット(IoT)

| 資産の種類                         | Scope (スコープ)          | 形式                             | 例                                 |
|------------------------------------|----------------|------------------------------------|------------------------------------------|
| Azure Stream Analytics on IoT Edge | Resource group | asa-\<App Name\>-\<Environment\>   | <ul><li>asa-navigator-prod </li><li>asa-emissions-dev</li></ul>     |
| Azure IoT Hub                      | グローバル         | aih-\<App Name\>-\<Environment\>   | <ul><li>aih-navigator-prod </li><li>aih-emissions-dev</li></ul>     |
| Azure Event Hubs                          | グローバル         | evh-\<App Name\>-\<Environment\>   | <ul><li>evh-navigator-prod </li><li>evh-emissions-dev</li></ul>     |
| Azure 通知ハブ                   | Resource group | anh-\<App Name\>-\<Environment\>   | <ul><li>evh-navigator-prod </li><li>evh-emissions-dev</li></ul>     |
| Azure Notification Hubs 名前空間         | グローバル         | anhns-\<App Name\>-\<Environment\> | <ul><li>anhns-navigator-prod </li><li>anhns-emissions-dev</li></ul> |
