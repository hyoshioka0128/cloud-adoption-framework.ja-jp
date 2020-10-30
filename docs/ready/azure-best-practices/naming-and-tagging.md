---
title: 推奨される名前付けおよびタグ付け規則
description: この記事では、エンタープライズ クラウド導入作業のサポートを明確に対象にした、リソースの名前付けとタグ付けに関する詳細な推奨事項について説明します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 03/05/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.custom: readiness, fasttrack-edit
ms.openlocfilehash: 18551e264163ebc8342da9c92c7a438fd1163cf9
ms.sourcegitcommit: c1d6c1c777475f92a3f8be6def84f1779648a55c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/21/2020
ms.locfileid: "92334869"
---
<!-- docutune:disable -->
<!-- cSpell:ignore appcs arck cdnp cdne osdisk westeurope usgovia accountlookup messagequery -->

# <a name="recommended-naming-and-tagging-conventions"></a>推奨される名前付けおよびタグ付け規則

運用管理要件と会計処理要件をサポートするようにクラウド資産を整理します。 明確に定義された名前付け規則とメタデータのタグ付け規則を使用すると、リソースをすばやく見つけて管理するのに役立ちます。 これらの規則は、チャージバックとショーバックという会計処理を使用して、クラウドの使用コストをビジネス チームに関連付けるためにも役立ちます。

リソースの正確な表現と名前付けは、セキュリティ保護のために不可欠です。 セキュリティ インシデントが発生した場合、影響を受けるシステム、それらのシステムのビジネスへの潜在的な影響、およびそれらが使用されている対象を迅速に特定することが、適切なリスク決定を行うために重要です。 [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-introduction) や [Azure Sentinel](https://docs.microsoft.com/azure/sentinel) などのセキュリティ サービスは、リソースと、リソース名によって関連付けられているログおよびアラート情報を参照します。

Azure では、[Azure リソースの名前付け規則と制限事項](/azure/azure-resource-manager/management/resource-name-rules)が定義されています。 このガイダンスでは、エンタープライズ クラウドの導入作業を支援するための詳細な推奨事項について説明します。

リソース名の変更が困難な場合があります。 大規模なクラウドのデプロイを開始する前に、包括的な名前付け規則を確立してください。

> [!NOTE]
> 企業ごとに組織上および管理上の要件は異なります。 これらの推奨事項は、クラウド導入チーム内での議論の出発点を提供します。
>
> これらの議論が進んだら、次のテンプレートを使用して、これらの推奨事項を特定のビジネス ニーズに整合させるときに行う名前付けとタグ付けに関する決定を記録してください。
>
> [名前付けおよびタグ付け規則の追跡テンプレート](https://raw.githubusercontent.com/microsoft/CloudAdoptionFramework/master/ready/naming-and-tagging-conventions-tracking-template.xlsx)をダウンロードします。

## <a name="naming-and-tagging-resources"></a>リソースの名前付けとタグ付け

名前付けおよびタグ付け戦略には、リソース名とメタデータ タグのコンポーネントとしてビジネスおよび運用の詳細が含まれます。

この戦略のビジネス面では、リソースの名前とタグに、チームを識別するために必要な組織情報が確実に含まれるようにします。 リソース コストを担当するビジネス オーナーと共にリソースを使用します。

運用の面では、名前とタグに、ワークロード、アプリケーション、環境、重要度、およびリソースの管理に役立つその他の情報を識別するために IT チームが使用する情報が確実に含まれるようにします。

## <a name="resource-naming"></a>リソースの名前付け

効果的な名前付け規則では、重要なリソース情報をリソースの名前の一部として使用してリソース名を組み立てます。 たとえば、[推奨される名前付け規則](#example-names)を使用すると、運用 SharePoint ワークロードのパブリック IP リソースは `pip-sharepoint-prod-westus-001` のような名前になります。

その名前から、リソースの種類、それに関連付けられたワークロード、そのデプロイ環境、およびそれをホストしている Azure リージョンをすばやく識別できます。

### <a name="naming-scope"></a>名前付けスコープ

すべての Azure リソースの種類には、リソース名が一意である必要があるレベルを定義するスコープがあります。 リソースには、そのスコープ内の一意の名前が必要です。

たとえば、仮想ネットワークにはリソース グループ スコープがあります。つまり、指定されたリソース グループには `vnet-prod-westus-001` という名前の 1 つのネットワークだけが存在できます。 他のリソース グループは `vnet-prod-westus-001` という名前の独自の仮想ネットワークを持つことができます。 サブネットはスコープが仮想ネットワークに設定されます。つまり、仮想ネットワーク内の各サブネットの名前は一意である必要があります。

パブリック エンドポイントを含む PaaS サービスや仮想マシンの DNS ラベルなどの一部のリソースの名前にはグローバル スコープがあります。つまり、それらは Azure プラットフォーム全体にわたって一意である必要があります。

リソース名には長さの制限があります。 名前付け規則を作成するときに、名前に埋め込まれるコンテキストおよびそのスコープと長さのバランスをとることが重要です。 詳細については、「[Azure リソースの名前付け規則と制限事項](/azure/azure-resource-manager/management/resource-name-rules)」を参照してください。

### <a name="recommended-naming-components"></a>推奨される名前付けコンポーネント

名前付け規則を作成する場合は、リソース名に反映させる重要な情報を特定します。 リソースの種類によって関連する情報は異なります。 次の一覧は、リソース名を作成するときに役立つ情報の例を示しています。

リソース名の長さの制限を超えないようにするために、名前付けコンポーネントの長さは短いままにしてください。

| 名前付けコンポーネント            | 説明                                                                                                                                                                                                      | 例                                         |
|-----------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------|
| 事業単位               | リソースが属しているサブスクリプションまたはワークロードを所有する会社の最上位の部門。 小規模な組織では、このコンポーネントは 1 つの会社の最上位の組織要素を表す可能性があります。 | `fin`, `mktg`, `product`, `it`, `corp`           |
| サブスクリプションの種類           | リソースが含まれているサブスクリプションの目的に関する概要の説明。 多くの場合は、デプロイ環境の種類または特定のワークロードによって分類されます。                                                       | `prod`, `shared`, `client`                       |
| アプリケーションまたはサービス名 | リソースが属しているアプリケーション、ワークロード、またはサービスの名前。                                                                                                                                    | `navigator`, `emissions`, `sharepoint`, `hadoop` |
| デプロイ環境      | リソースによってサポートされているワークロードの開発ライフサイクルのステージ。                                                                                                                              | `prod`, `dev`, `qa`, `stage`, `test`             |
| リージョン                      | リソースがデプロイされている Azure リージョン。                                                                                                                                                                 | `westus`, `eastus2`, `westeurope`, `usgovia`     |

### <a name="recommended-resource-type-prefixes"></a>推奨されるリソースの種類のプレフィックス

各ワークロードは、多数の個々のリソースやサービスで構成できます。 リソースの種類のプレフィックスをリソース名に組み込むと、アプリケーションまたはサービス コンポーネントの視覚的な識別が簡単になります。

この一覧では、名前付け規則を定義する場合に使用すべき推奨される Azure リソースの種類のプレフィックスを示します。

<!-- cSpell:ignore apim snet traf vmss stvm arcm ntfns sqldb psql sqldw sqlstrdb ssimp srch hbase appi migr -->

### <a name="general"></a>全般

| 資産の種類                      | 名前のプレフィックス |
|---------------------------------|-------------|
| 管理グループ                | mg-         |
| Resource group                  | rg-         |
| ポリシー定義               | policy-     |
| API 管理サービス インスタンス | apim-       |
| マネージド ID                | id-         |

### <a name="networking"></a>ネットワーク

| 資産の種類                       | 名前のプレフィックス |
|----------------------------------|-------------|
| 仮想ネットワーク                  | vnet-       |
| Subnet                           | snet-       |
| 仮想ネットワーク ピアリング          | peer-       |
| ネットワーク インターフェイス (NIC)          | nic-        |
| パブリック IP アドレス                | pip-        |
| ロード バランサー (内部)         | lbi-        |
| ロード バランサー (外部)         | lbe-        |
| ネットワーク セキュリティ グループ (NSG)     | nsg-        |
| アプリケーション セキュリティ グループ (ASG) | asg-        |
| ローカル ネットワーク ゲートウェイ            | lgw-        |
| 仮想ネットワーク ゲートウェイ          | vgw-        |
| VPN 接続                   | cn-         |
| ExpressRoute 回線             | erc-        |
| Application gateway              | agw-        |
| ルート テーブル                      | route-      |
| ユーザー定義ルート (UDR)         | udr-        |
| Traffic Manager プロファイル          | traf-       |
| フロント ドア                       | fd-         |
| CDN プロファイル                      | cdnp-       |
| CDN エンドポイント                     | cdne-       |

### <a name="compute-and-web"></a>コンピューティングと Web

| 資産の種類 | 名前のプレフィックス |
|--|--|
| 仮想マシン | vm |
| 仮想マシン スケール セット | vmss- |
| 可用性セット | avail- |
| マネージド ディスク (OS) | osdisk |
| マネージド ディスク (データ) | disk |
| VM ストレージ アカウント | stvm |
| Azure Arc 対応サーバー | arcs- |
| Azure Arc 対応 Kubernetes クラスター | arck |
| コンテナー レジストリ | cr |
| コンテナー インスタンス | ci- |
| AKS クラスター | aks- |
| Service Fabric クラスター | sf- |
| App Service 環境 | ase- |
| App Service プラン | plan- |
| Web アプリ | app- |
| 関数アプリ | func- |
| クラウド サービス | cld- |
| Notification Hubs | ntf- |
| Notification Hubs 名前空間 | ntfns- |

### <a name="databases"></a>データベース

| 資産の種類                     | 名前のプレフィックス |
|--------------------------------|-------------|
| Azure SQL Database サーバー      | sql-        |
| Azure SQL データベース             | sqldb-      |
| Azure Cosmos DB データベース       | cosmos-     |
| Azure Cache for Redis インスタンス | redis-      |
| MySQL データベース                 | mysql-      |
| PostgreSQL データベース            | psql-       |
| Azure SQL Data Warehouse       | sqldw-      |
| Azure Synapse Analytics        | syn-        |
| SQL Server Stretch Database    | sqlstrdb-   |

### <a name="storage"></a>ストレージ

| 資産の種類               | 名前のプレフィックス |
|--------------------------|-------------|
| ストレージ アカウント          | st          |
| Azure StorSimple         | ssimp       |
| Azure Container Registry | acr         |

### <a name="ai-and-machine-learning"></a>AI および機械学習

| 資産の種類                       | 名前のプレフィックス |
|----------------------------------|-------------|
| Azure Cognitive Search           | srch-       |
| Azure Cognitive Services         | cog-        |
| Azure Machine Learning ワークスペース | mlw-        |

### <a name="analytics-and-iot"></a>Analytics と IoT

| 資産の種類                       | 名前のプレフィックス | |---------------------------------_|-------------| | Azure Analysis Services サーバー | as          | | Azure Databricks ワークスペース  | dbw-        | | Azure Stream Analytics           | asa-        | | Azure Data Explorer クラスター   | dec         | | Azure Data Factory               | adf-        | | Data Lake Store アカウント       | dls         | | Data Lake Analytics アカウント   | dla         | | イベント ハブ                    | evh-        | | HDInsight - Hadoop クラスター    | hadoop-     | | HDInsight - HBase クラスター     | hbase-      | | HDInsight - Kafka クラスター     | kafka-      | | HDInsight - Spark クラスター     | spark-      | | HDInsight - Storm クラスター     | storm-      | | HDInsight - ML Services クラスター | mls-        | | IoT ハブ                         | iot-        | | Power BI Embedded                | pbi-        | | Time Series Insights 環境        | tsi-        |

### <a name="developer-tools"></a>開発者用ツール

| 資産の種類 | 名前のプレフィックス |
|---|---|
| App Configuration ストア | appcs- |

### <a name="integration"></a>統合

| 資産の種類          | 名前のプレフィックス |
|---------------------|-------------|
| 統合アカウント | ia-         |
| ロジック アプリ          | logic-      |
| Service Bus         | sb-         |
| Service Bus キュー   | sbq-        |
| Service Bus トピック   | sbt-        |

### <a name="management-and-governance"></a>管理とガバナンス

| 資産の種類 | 名前のプレフィックス |
|--|--|
| Automation アカウント | aa- |
| Azure Monitor アクション グループ | ag- |
| ブループリント | bp- |
| ブループリント割り当て | bpa- |
| Key Vault | kv- |
| Log Analytics ワークスペース | log- |
| Application Insights | appi- |
| Recovery Services コンテナー | rsv- |

### <a name="migration"></a>移行

| 資産の種類                          | 名前のプレフィックス |
|-------------------------------------|-------------|
| Azure Migrate プロジェクト               | migr-       |
| Database Migration Service インスタンス | dms-        |
| Recovery Services コンテナー             | rsv-        |

<!-- cSpell:enable -->

## <a name="metadata-tags"></a>メタデータ タグ

クラウド リソースにメタデータ タグを適用すると、リソース名に含めることができなかったそれらの資産に関する情報を含めることができます。 その情報を使用して、リソースに対してより高度なフィルター処理およびレポート作成を実行できます。 これらのタグには、リソースの関連するワークロードまたはアプリケーション、運用要件、および所有者情報に関するコンテキストを含めることができます。 IT チームやビジネス チームはこの情報を使用して、リソースを見つけたり、リソースの使用状況や請求に関するレポートを生成したりできます。

どのタグをリソースに適用するか、どのタグが必須か任意かは、組織によって異なります。 次の一覧は、リソースに関する重要なコンテキストと情報を取得する一般的なタグの例です。 この一覧を出発点として使用し、独自のタグ付け規則を指定してください。

| タグ名                  | 説明                                                                                                                                                                                                          | Key               | 値の例                                              |
|---------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------|------------------------------------------------------------|
| アプリケーション名          | リソースが関連付けられているアプリケーション、サービス、またはワークロードの名前。                                                                                                                                       | _ApplicationName_ | _{アプリケーション名}_                                               |
| 承認者名             | このリソースに関連したコストの承認を担当する人物。                                                                                                                                                     | _Approver_        | _{email}_                                                  |
| 必要な予算/承認された予算  | このアプリケーション、サービス、またはワークロードに割り当てられた金額。                                                                                                                                                          | _BudgetAmount_    | _{\$}_                                                     |
| 事業単位             | リソースが属しているサブスクリプションまたはワークロードを所有する会社の最上位の部門。 小規模な組織では、このタグは 1 つの会社の、または共有された最上位の組織要素を表す可能性があります。 | _BusinessUnit_    | _FINANCE_ 、 _MARKETING_ 、 _{Product Name}_ 、 _CORP_ 、 _SHARED_ |
| コスト センター               | このリソースに関連付けられたアカウンティング コスト センター。                                                                                                                                                                | _CostCenter_      | _{number}_                                                 |
| 障害復旧         | アプリケーション、ワークロード、またはサービスのビジネス上の重要度。                                                                                                                                                       | _DR_              | _Mission-critical_ 、 _Critical_ 、 _Essential_                |
| プロジェクトの終了日   | アプリケーション、ワークロード、またはサービスの使用停止がスケジュールされている日付。                                                                                                                                         | _EndDate_         | _{date}_                                                   |
| 環境               | アプリケーション、ワークロード、またはサービスのデプロイ環境。                                                                                                                                                     | _Env_             | _Prod_ 、 _Dev_ 、 _QA_ 、 _Stage_ 、 _Test_                       |
| 所有者名                | このアプリケーション、ワークロード、またはサービスの所有者。                                                                                                                                                                      | _所有者_           | _{email}_                                                  |
| 要求者名            | このアプリケーションの作成を要求したユーザー。                                                                                                                                                                 | _要求者_       | _{email}_                                                  |
| サービス クラス             | アプリケーション、ワークロード、またはサービスのサービス レベル アグリーメント レベル。                                                                                                                                              | _ServiceClass_    | _Dev_ 、 _Bronze_ 、 _Silver_ 、 _Gold_                          |
| プロジェクトの開始日 | アプリケーション、ワークロード、またはサービスが最初にデプロイされた日付。                                                                                                                                                  | _StartDate_       | _{date}_                                                   |

## <a name="example-names"></a>名前の例

次のセクションでは、エンタープライズ クラウドのデプロイにおける一般的な Azure リソースの種類のいくつかのサンプル名を示します。

<!-- TODO: Use tick marks for names. -->

<!-- cSpell:ignore mktgsharepoint acctlookupsvc vmhadoop vmtest vmsharepoint vmnavigator vmsqlnode stvmstcoreeastus stvmpmcoreeastus stvmstplmeastus stvmsthadoopeastus stnavigatordata stemissionsoutput stdiag stdiagsh ssimpnavigatorprod ssimpemissionsdev dlanavigatorprod dlsnavigatorprod dlaemissionsdev dlsemissionsdev weballow rdpallow sqlallow dnsblocked cloudapp azurewebsites servicebus appcn keda acrnavigatorprod -->

<!-- markdownlint-disable MD024 -->

### <a name="example-names-general"></a>名前の例:全般

| 資産の種類                      | Scope                                 | Format                                                      | 例                                                                                           |
|---------------------------------|---------------------------------------|-------------------------------------------------------------|----------------------------------------------------------------------------------------------------|
| 管理グループ                | 事業単位または環境の種類 | mg-\<Business Unit\>\[-\<Environment type\>\]               | <li> mg-mktg <li> mg-hr <li> mg-corp-prod <li> mg-fin-client                                       |
| サブスクリプション                    | アカウント/ <br> Enterprise Agreement    | \<Business Unit\>-\<Subscription type\>-\<\#\#\#\>          | <li> mktg-prod-001 <li> corp-shared-001 <li> fin-client-001                                        |
| Resource group                  | サブスクリプション                          | rg-\<App or service name\>-\<Subscription type\>-\<\#\#\#\> | <li> rg-mktgsharepoint-prod-001 <li> rg-acctlookupsvc-share-001 <li> rg-ad-dir-services-shared-001 |
| API 管理サービス インスタンス | グローバル                                | apim-\<App or service name\>                                | apim-navigator-prod                                                                                |
| マネージド ID                | Resource group                        | id-\<App or service name\>                                  | id-appcn-keda-prod-eus-001                                                                         |

> [!NOTE]
> 上記を含め、このドキュメント内にある名前の例では、3 桁の埋め込み (\<\#\#\#\>) が示されています。 つまり、  mktg-prod- *001*
>
> 構成管理データベース (CMDB)、IT 資産管理ツール、または従来のアカウンティング ツールで資産が管理されている場合、埋め込みにより、人間が読みやすくなり、並べ替えるのに役立ちます。 デプロイされた資産が IT 資産の大規模なインベントリまたはポートフォリオの一部として一元的に管理されている場合、埋め込みアプローチは、インベントリの名前付けを管理するためにこれらのシステムで使用されるインターフェイスと適合します。
>
> 残念ながら、従来の資産埋め込みアプローチは、非埋め込み数値に基づいて資産を反復処理する場合のある、コードとしてのインフラストラクチャのアプローチでは問題が発生する可能性があります。 このアプローチは、デプロイまたは自動構成管理のタスクにおいて一般的です。 これらのスクリプトでは、埋め込みを定期的に削除し、埋め込まれた数値を実数に変換する必要があります。これにより、スクリプトの開発と実行時間が遅くなります。
>
> どちらのアプローチを実装するかは、個人的な判断です。 この記事での埋め込みの意図は、インベントリの番号付けに対して一貫したアプローチを使用することの重要性を示すことであり、どちらのアプローチが優れているかを示すものではありません。 数値スキーマ (埋め込みあり、またはなし) を決定する前に、CMDB/資産管理ソリューションとコードベースのインベントリ管理のうち、どちらの方が長期的な操作に大きな影響を与えるかを評価します。 その後、運用ニーズに最適な埋め込みオプションを採用します。

### <a name="example-names-networking"></a>名前の例:ネットワーク

| 資産の種類                   | Scope           | Format                                                               | 例                                                                                                                      |
|------------------------------|-----------------|----------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------|
| 仮想ネットワーク              | Resource group  | vnet-\<Subscription type\>-\<Region\>-\<\#\#\#\>                     | <li> vnet-shared-eastus2-001 <li> vnet-prod-westus-001 <li> vnet-client-eastus2-001 |
| Subnet                       | 仮想ネットワーク | snet-\<subscription\>-\<subregion\>-\<\#\#\#\>                       | <li> snet-shared-eastus2-001 <li> snet-prod-westus-001 <li> snet-client-eastus2-001 |
| ネットワーク インターフェイス (NIC)      | Resource group  | nic-\<\#\#\>-\<vm name\>-\<subscription\>\<\#\#\#\>                   | <li> nic-01-dc1-shared-001 <li> nic-02-vmhadoop1-prod-001 <li> nic-02-vmtest1-client-001 |
| パブリック IP アドレス            | Resource group  | pip-\<vm name or app name\>-\<Environment\>-\<subregion\>-\<\#\#\#\> | <li> pip-dc1-shared-eastus2-001 <li> pip-hadoop-prod-westus-001 |
| Load Balancer                | Resource group  | lb-\<app name or role\>\<Environment\>\<\#\#\#\>                     | <li> lb-navigator-prod-001 <li> lb-sharepoint-dev-001 |
| ネットワーク セキュリティ グループ (NSG) | サブネットまたは NIC   | nsg-\<policy name or app name\>-\<\#\#\#\>                           | <li> nsg-weballow-001 <li> nsg-rdpallow-001 <li> nsg-sqlallow-001 <li> nsg-dnsblocked-001 |
| ローカル ネットワーク ゲートウェイ        | 仮想ゲートウェイ | lgw-\<Subscription type\>-\<Region\>-\<\#\#\#\>                      | <li> lgw-shared-eastus2-001 <li> lgw-prod-westus-001 <li> lgw-client-eastus2-001 |
| 仮想ネットワーク ゲートウェイ      | 仮想ネットワーク | vgw-\<Subscription type\>-\<Region\>-\<\#\#\#\>                      | <li> vgw-shared-eastus2-001 <li> vgw-prod-westus-001 <li> vgw-client-eastus2-001 |
| サイト間接続      | Resource group  | cn-\<local gateway name\>-to-\<virtual gateway name\>                | <li> cn-lgw-shared-eastus2-001-to-vgw-shared-eastus2-001 <li> cn-lgw-shared-eastus2-001-to-shared-westus-001 |
| VPN 接続               | Resource group  | cn-\<subscription1\>\<region1\>-to-\<subscription2\>\<region2\>-     | <li> cn-shared-eastus2-to-shared-westus <li> cn-prod-eastus2-to-prod-westus |
| ルート テーブル                  | Resource group  | route-\<route table name\>                                           | <li> route-navigator <li> route-sharepoint |
| DNS ラベル                    | グローバル          | \<A record of vm\>.<region\>.cloudapp.azure.com                   | <li> dc1.westus.cloudapp.azure.com <li> web1.eastus2.cloudapp.azure.com |

### <a name="example-names-compute-and-web"></a>名前の例:コンピューティングと Web

| 資産の種類                  | Scope          | Format                                                              | 例                                                                                                                          |
|-----------------------------|----------------|---------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------|
| 仮想マシン             | Resource group | vm\<policy name or app name\>\<\#\#\#\>                              | <li> vmnavigator001 <li> vmsharepoint001 <li> vmsqlnode001 <li> vmhadoop001 |
| VM ストレージ アカウント          | グローバル         | stvm\<performance type\>\<app name or prod name\>\<region\>\<\#\#\#\> | <li> stvmstcoreeastus2001 <li> stvmpmcoreeastus2001 <li> stvmstplmeastus2001 <li> stvmsthadoopeastus2001 |
| Web アプリ                     | グローバル         | app-\<App Name\>-\<Environment\>-\<\#\#\#\>.[{azurewebsites.net}]   | <li> app-navigator-prod-001.azurewebsites.net <li> app-accountlookup-dev-001.azurewebsites.net |
| 関数アプリ                | グローバル         | func-\<App Name\>-\<Environment\>-\<\#\#\#\>.[{azurewebsites.net}]  | <li> func-navigator-prod-001.azurewebsites.net <li> func-accountlookup-dev-001.azurewebsites.net |
| クラウド サービス               | グローバル         | cld-\<App Name\>-\<Environment\>-\<\#\#\#\>.[{cloudapp.net}]        | <li> cld-navigator-prod-001.azurewebsites.net <li> cld-accountlookup-dev-001.azurewebsites.net |
| 通知ハブ            | Resource group | ntf-\<App Name\>-\<Environment\>                                    | <li> ntf-navigator-prod <li> ntf-emissions-dev |
| Notification Hubs 名前空間 | グローバル         | ntfns-\<App Name\>-\<Environment\>                                  | <li> ntfns-navigator-prod <li> ntfns-emissions-dev |

### <a name="example-names-databases"></a>名前の例:データベース

| 資産の種類                     | Scope              | Format                                 | 例                                                                  |
|--------------------------------|--------------------|----------------------------------------|---------------------------------------------------------------------------|
| Azure SQL Database サーバー      | グローバル             | sql-\<App Name\>-\<Environment\>       | <li> sql-navigator-prod <li> sql-emissions-dev |
| Azure SQL データベース             | Azure SQL データベース | sqldb-\<Database Name>-\<Environment\> | <li> sqldb-users-prod <li> sqldb-users-dev |
| Azure Cosmos DB データベース       | グローバル             | cosmos-\<App Name\>-\<Environment\>    | <li> cosmos-navigator-prod <li> cosmos-emissions-dev |
| Azure Cache for Redis インスタンス | グローバル             | redis-\<App Name\>-\<Environment\>     | <li> redis-navigator-prod <li> redis-emissions-dev |
| MySQL データベース                 | グローバル             | mysql-\<App Name\>-\<Environment\>     | <li> mysql-navigator-prod <li> mysql-emissions-dev |
| PostgreSQL データベース            | グローバル             | psql-\<App Name\>-\<Environment\>      | <li> psql-navigator-prod <li> psql-emissions-dev |
| Azure SQL Data Warehouse       | グローバル             | sqldw-\<App Name\>-\<Environment\>     | <li> sqldw-navigator-prod <li> sqldw-emissions-dev |
| SQL Server Stretch Database    | Azure SQL データベース | sqlstrdb-\<App Name\>-\<Environment\>  | <li> sqlstrdb-navigator-prod <li> sqlstrdb-emissions-dev |

### <a name="example-names-storage"></a>名前の例:ストレージ

| 資産の種類                        | Scope  | Format                                                                        | 例                                                              |
|-----------------------------------|--------|-------------------------------------------------------------------------------|-----------------------------------------------------------------------|
| ストレージ アカウント (全般)     | グローバル | st\<storage name\>\<\#\#\#\>                                                  | <li> stnavigatordata001 <li> stemissionsoutput001 |
| Storage アカウント (診断ログ) | グローバル | stdiag\<first 2 letters of subscription name and number\>\<region\>\<\#\#\#\> | <li> stdiagsh001eastus2001 <li> stdiagsh001westus001 |
| Azure StorSimple                  | グローバル | ssimp\<App Name\>\<Environment\>                                              | <li> ssimpnavigatorprod <li> ssimpemissionsdev |
| Azure Container Registry          | グローバル | acr\<App Name\>\<Environment\>\<\#\#\#\>                                      | <li> acrnavigatorprod001 |

### <a name="example-names-ai-and-machine-learning"></a>名前の例:AI と機械学習

| 資産の種類                       | Scope          | Format                            | 例                                                          |
|----------------------------------|----------------|-----------------------------------|-------------------------------------------------------------------|
| Azure Cognitive Search           | グローバル         | srch-\<App Name\>-\<Environment\> | <li> srch-navigator-prod <li> srch-emissions-dev |
| Azure Cognitive Services         | Resource group | cog-\<App Name\>-\<Environment\>  | <li> cog-navigator-prod <li> cog-emissions-dev |
| Azure Machine Learning ワークスペース | Resource group | mlw-\<App Name\>-\<Environment\>  | <li> mlw-navigator-prod <li> mlw-emissions-dev |

### <a name="example-names-analytics-and-iot"></a>名前の例:Analytics と IoT

| 資産の種類                  | Scope          | Format                              | 例                                                              |
|-----------------------------|----------------|-------------------------------------|-----------------------------------------------------------------------|
| Azure Data Factory          | グローバル         | adf-\<App Name\>\<Environment\>     | <li> adf-navigator-prod <li> adf-emissions-dev |
| Azure Stream Analytics      | Resource group | asa-\<App Name\>-\<Environment\>    | <li> asa-navigator-prod <li> asa-emissions-dev |
| Data Lake Analytics アカウント | グローバル         | dla\<App Name\>\<Environment\>      | <li> dlanavigatorprod <li> dlaemissionsdev |
| Data Lake Storage アカウント   | グローバル         | dls\<App Name\>\<Environment\>      | <li> dlsnavigatorprod <li> dlsemissionsdev |
| イベント ハブ                   | グローバル         | evh-\<App Name\>-\<Environment\>    | <li> evh-navigator-prod <li> evh-emissions-dev |
| HDInsight - HBase クラスター   | グローバル         | hbase-\<App Name\>-\<Environment\>  | <li> hbase-navigator-prod <li> hbase-emissions-dev |
| HDInsight - Hadoop クラスター  | グローバル         | hadoop-\<App Name\>-\<Environment\> | <li> hadoop-navigator-prod <li> hadoop-emissions-dev |
| HDInsight - Spark クラスター   | グローバル         | spark-\<App Name\>-\<Environment\>  | <li> spark-navigator-prod <li> spark-emissions-dev  |
| IoT ハブ                     | グローバル         | iot-\<App Name\>-\<Environment\>    | <li> iot-navigator-prod <li> iot-emissions-dev |
| Power BI Embedded           | グローバル         | pbi-\<App Name\>\<Environment\>     | <li> pbi-navigator-prod <li> pbi-emissions-dev |

### <a name="example-names-integration"></a>名前の例:統合

| 資産の種類        | Scope       | Format                                                     | 例                                                      |
|-------------------|-------------|------------------------------------------------------------|---------------------------------------------------------------|
| Service Bus       | グローバル      | sb-\<App Name\>-\<Environment\>.[{servicebus.windows.net}] | <li> sb-navigator-prod <li> sb-emissions-dev |
| Service Bus キュー | Service Bus | sbq-\<query descriptor\>                                   | <li> sbq-messagequery |
| Service Bus トピック | Service Bus | sbt-\<query descriptor\>                                   | <li> sbt-messagequery |
