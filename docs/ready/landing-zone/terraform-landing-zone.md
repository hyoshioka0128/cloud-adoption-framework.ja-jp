---
title: Terraform を使用してランディング ゾーンを構築する
description: Terraform を使用してランディング ゾーンを構築する方法を説明します。
author: arnaudlh
ms.author: arnaul
ms.date: 02/25/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.openlocfilehash: 69d1aaf7ff2a5bd3d71cb03fd1149019792c71f1
ms.sourcegitcommit: 5d6a7610e556f7b8ca69960ba76a3adfa9203ded
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/14/2020
ms.locfileid: "83401280"
---
<!-- cSpell:ignore arnaudlh arnaul Arnaud vCPUs eastasia southeastasia lalogs tfvars -->

# <a name="use-terraform-to-build-your-landing-zones"></a>Terraform を使用してランディング ゾーンを構築する

Azure には、ランディング ゾーンをデプロイするためのネイティブ サービスが用意されています。 他のサードパーティ製ツールも、この作業に役立ちます。 顧客やパートナーがランディング ゾーンのデプロイによく使用するツールの 1 つは、HashiCorp の Terraform です。 このセクションでは、サンプルのランディング ゾーンを使用して、Azure サブスクリプションの基本的なガバナンス、アカウンティング、およびセキュリティの機能をデプロイする方法について説明します。

## <a name="purpose-of-the-landing-zone"></a>ランディング ゾーンの目的

Terraform のクラウド導入フレームワークのファンデーション ランディング ゾーンには、ログ記録、アカウンティング、およびセキュリティを適用するための機能があります。 このランディング ゾーンは、Terraform モジュールと呼ばれる標準コンポーネントを使用して、環境にデプロイされるリソース間の一貫性が維持されます。

## <a name="use-standard-modules"></a>標準モジュールを使用する

コンポーネントの再利用は、コードとしてのインフラストラクチャの基本原則です。 モジュールは、環境内および環境間でのリソースのデプロイにおける標準と一貫性の定義に役立ちます。 この最初のランディング ゾーンをデプロイするために使用されるモジュールは、公式の [Terraform レジストリ](https://registry.terraform.io/modules/aztfmod)で入手できます。

## <a name="architecture-diagram"></a>アーキテクチャの図

最初のランディング ゾーンでは、サブスクリプションに次のコンポーネントがデプロイされます。

![Terraform を使用した基本ランディング ゾーン](../../_images/ready/foundations-terraform-landing-zone.png)

## <a name="capabilities"></a>機能

デプロイされるコンポーネントとその目的は次のとおりです。

<!-- markdownlint-disable MD033 -->

| コンポーネント | 担当 |
|---|---|
| リソース グループ | ファンデーションに必要なコア リソース グループ |
| アクティビティ ログ | サブスクリプションのすべてのアクティビティの監査とアーカイブ: <li> ストレージ アカウント <li> Azure Event Hubs |
| 診断ログ | すべての操作ログは、特定の日数だけ保持されます。 <li> ストレージ アカウント <li> Event Hubs |
| Log Analytics | すべての操作ログを格納します。 詳細なアプリケーションのベスト プラクティスのレビューのための一般的なソリューションのデプロイ: <li> NetworkMonitoring <li> ADAssessment <li> ADReplication <li> AgentHealthAssessment <li> DnsAnalytics <li> KeyVaultAnalytics |
| Azure Security Center | セキュリティ検疫メトリックおよびメールと電話番号に送信されるアラート |

<!-- markdownlint-enable MD033 -->

## <a name="use-this-blueprint"></a>このブループリントを使用する

クラウド導入フレームワークのファンデーション ランディング ゾーンを使用する前に、以下の前提条件、決定事項、実装ガイダンスを確認してください。

## <a name="assumptions"></a>前提条件

この初期のランディング ゾーンを定義するときに、以下の前提条件または制約が考慮されました。 これらの前提条件がご自分の環境の制約と一致する場合、このブループリントを使用して最初のランディング ゾーンを作成することができます。 このブループリントは、固有の制約を満たすランディング ゾーン ブループリントを作成するために拡張することもできます。

- **サブスクリプションの制限**: この導入作業では、[サブスクリプションの制限](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits)を超えることはないと考えられます。 2 つの一般的な指標は、25,000 個の VM または 10,000 個の vCPU を超過することです。
- **コンプライアンス:** このランディング ゾーンでは、サードパーティのコンプライアンス要件は必要ありません。
- **アーキテクチャの複雑さ:** アーキテクチャの複雑さによって、追加の運用サブスクリプションが常に必要になるわけではありません。
- **共有サービス:** このサブスクリプションをハブ アンド スポーク アーキテクチャのスポークのように扱うことを要求する既存の共有サービスは Azure にありません。

これらの前提条件が現在の環境と一致する場合、このブループリントは、ランディング ゾーンの作成を行うための適切な開始点となる可能性があります。

## <a name="design-decisions"></a>設計上の決定

Terraform ランディング ゾーンでは、次の決定事項が示されます。

| コンポーネント              | 決定事項                                                                                                                                                                                                                                                                | 他の方法                                                                                                                                                                                                                                          |
|------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ログ記録と監視 | Azure Monitor Log Analytics ワークスペースが使用されます。 診断ストレージ アカウントとイベント ハブがプロビジョニングされます。                                                                                                                                                        |                                                                                                                                                                                                                                                                 |
| ネットワーク                | N/A - ネットワークは別のランディング ゾーンで実装されます。                                                                                                                                                                                                                    | [ネットワーク関連の意思決定](../considerations/networking-options.md)                                                                                                                                                                                                 |
| ID               | サブスクリプションは既に Azure Active Directory インスタンスに関連付けられていると想定されます。                                                                                                                                                                        | [ID 管理のベスト プラクティス](https://docs.microsoft.com/azure/security/fundamentals/identity-management-best-practices)                                                                                                                               |
| ポリシー                 | このランディング ゾーンでは現在、Azure ポリシーは適用されないものと想定されています。                                                                                                                                                                                            |                                                                                                                                                                                                                                                                 |
| サブスクリプション デザイン    | 該当なし - 単一の運用サブスクリプション用に設計されています。                                                                                                                                                                                                                     | [初期サブスクリプションを作成する](../azure-best-practices/initial-subscriptions.md)                                                                                                                                                                                  |
| リソース グループ        | 該当なし - 単一の運用サブスクリプション用に設計されています。                                                                                                                                                                                                                     | [サブスクリプションのスケーリング](../azure-best-practices/scale-subscriptions.md)                                                                                                                                                                                           |
| 管理グループ      | 該当なし - 単一の運用サブスクリプション用に設計されています。                                                                                                                                                                                                                     | [サブスクリプションを整理する](../azure-best-practices/organize-subscriptions.md)                                                                                                                                                                                     |
| Data                   | 該当なし                                                                                                                                                                                                                                                                      | 「[Azure SQL で適切なデプロイ オプションを選択する](https://docs.microsoft.com/azure/sql-database/sql-database-paas-vs-sql-server-iaas)」と [Azure データ ストア ガイダンス](https://docs.microsoft.com/azure/architecture/guide/technology-choices/data-store-overview) |
| ストレージ                | 該当なし                                                                                                                                                                                                                                                                      | [Azure Storage ガイダンス](../considerations/storage-options.md)                                                                                                                                                                                                  |
| 命名規則       | 環境が作成されるとき、一意のプレフィックスも作成されます。 グローバルな一意名 (ストレージ アカウントなど) を必要とするリソースでは、このプレフィックスが使用されます。 カスタム名には、ランダムなサフィックスが付けられます。 次の表で説明するように、タグの使用法は必須です。 | [名前付けとタグ付けのベスト プラクティス](../azure-best-practices/naming-and-tagging.md)                                                                                                                                                                              |
| コスト管理        | 該当なし                                                                                                                                                                                                                                                                      | [コストの追跡](../azure-best-practices/track-costs.md)                                                                                                                                                                                                        |
| Compute                | 該当なし                                                                                                                                                                                                                                                                      | [コンピューティング オプション](../considerations/compute-options.md)                                                                                                                                                                                                         |

### <a name="tagging-standards"></a>タグ付けの標準

すべてのリソースとリソース グループに、次に示す最小タグ セットが存在している必要があります。

| タグ名          | 説明                                                                                        | Key             | 値の例                                    |
|-------------------|----------------------------------------------------------------------------------------------------|-----------------|--------------------------------------------------|
| 事業単位     | リソースが属しているサブスクリプションまたはワークロードを所有する会社の最上位の部門。 | BusinessUnit    | FINANCE、MARKETING、{Product Name}、CORP、SHARED |
| コスト センター       | このリソースに関連付けられたアカウンティング コスト センター。                                              | CostCenter      | Number                                           |
| ディザスター リカバリー | アプリケーション、ワークロード、またはサービスのビジネス上の重要度。                                     | DR              | DR-ENABLED、NON-DR-ENABLED                       |
| 環境       | アプリケーション、ワークロード、またはサービスのデプロイ環境。                                   | Env             | Prod、Dev、QA、Stage、Test、Training             |
| 所有者名        | このアプリケーション、ワークロード、またはサービスの所有者。                                                    | 所有者           | email                                            |
| デプロイの種類   | リソースの管理方法を定義します。                                                    | deploymentType  | Manual、Terraform                                |
| Version           | デプロイされるブループリントのバージョン。                                                                 | version         | v0.1                                             |
| アプリケーション名  | リソースに関連付けられているアプリケーション、サービス、またはワークロードの名前。             | ApplicationName | "アプリ名"                                       |

<!-- cSpell:ignore caf -->

## <a name="customize-and-deploy-your-first-landing-zone"></a>最初のランディング ゾーンをカスタマイズしてデプロイする

[Terraform ファンデーション ランディング ゾーンを複製する](https://github.com/azure/caf-terraform-landingzones)ことができます。 Terraform の変数を変更することで、ランディング ゾーンを簡単に開始します。 この例では **blueprint_foundations.sandbox.auto.tfvars** を使用するため、このファイルの値が Terraform によって自動的に設定されます。

さまざまな変数セクションを見てみましょう。

この最初のオブジェクトでは、`-hub-core-sec` および `-hub-operations` という名前で、実行時にプレフィックスが追加される 2 つのリソース グループを `southeastasia` リージョンに作成します。

```hcl
resource_groups_hub = {
    HUB-CORE-SEC    = {
        name = "-hub-core-sec"
        location = "southeastasia"
    }
    HUB-OPERATIONS  = {
        name = "-hub-operations"
        location = "southeastasia"
    }
}
```

次に、ファンデーションを設定できるリージョンを指定します。 ここでは、すべてのリソースのデプロイに `southeastasia` を使用します。

```hcl
location_map = {
    region1   = "southeastasia"
    region2   = "eastasia"
}
```

次に、操作ログと Azure サブスクリプション ログの保有期間を指定します。 このデータは、別のストレージ アカウントとイベント ハブに格納され、それらの名前は一意である必要があるためランダムに生成されます。

```hcl
azure_activity_logs_retention = 365
azure_diagnostics_logs_retention = 60
```

tags_hub では、作成されるすべてのリソースに適用されるタグの最小セットを指定します。

```hcl
tags_hub = {
    environment     = "DEV"
    owner           = "Arnaud"
    deploymentType  = "Terraform"
    costCenter      = "65182"
    BusinessUnit    = "SHARED"
    DR              = "NON-DR-ENABLED"
}
```

次に、デプロイの分析に使用されるログ分析名とソリューションのセットを指定します。 ここでは、ネットワーク監視、Active Directory Assessment と Replication、DNS Analytics、および Key Vault Analytics を保持しました。

```hcl

analytics_workspace_name = "lalogs"

solution_plan_map = {
    NetworkMonitoring = {
        "publisher" = "Microsoft"
        "product"   = "OMSGallery/NetworkMonitoring"
    },
    ADAssessment = {
        "publisher" = "Microsoft"
        "product"   = "OMSGallery/ADAssessment"
    },
    ADReplication = {
        "publisher" = "Microsoft"
        "product"   = "OMSGallery/ADReplication"
    },
    AgentHealthAssessment = {
        "publisher" = "Microsoft"
        "product"   = "OMSGallery/AgentHealthAssessment"
    },
    DnsAnalytics = {
        "publisher" = "Microsoft"
        "product"   = "OMSGallery/DnsAnalytics"
    },
    KeyVaultAnalytics = {
        "publisher" = "Microsoft"
        "product"   = "OMSGallery/KeyVaultAnalytics"
    }
}

```

次に、Azure Security Center のアラート パラメーターを構成しました。

```hcl
# Azure Security Center Configuration
security_center = {
    contact_email   = "joe@contoso.com"
    contact_phone   = "+6500000000"
}
```

## <a name="get-started"></a>はじめに

構成を確認した後、Terraform 環境をデプロイする場合と同様に、構成をデプロイできます。 rover を使用することをお勧めします。これは、Windows、Linux、または macOS からデプロイできる Docker コンテナーです。 [ランディング ゾーン](https://github.com/azure/caf-terraform-landingzones)の使用を開始できます。

## <a name="next-steps"></a>次のステップ

ファンデーション ランディング ゾーンは、複雑な環境の基礎が分解して構築されます。 このエディションでは、次の方法で拡張できるシンプルな機能のセットが提供されています。

- ブループリントに他のモジュールを追加します。
- 上位に追加のランディング ゾーンを重ねます。

ランディング ゾーンを重ねることは、システムを分離し、使用している各コンポーネントのバージョンを管理し、コード デプロイとしてのインフラストラクチャの迅速なイノベーションと安定を実現するための良い方法です。

今後の参照アーキテクチャでは、ハブとスポークのトポロジに関するこの概念を説明します。

> [!div class="nextstepaction"]
> [基盤の Terraform ランディング ゾーン サンプルを確認する](https://github.com/azure/caf-terraform-landingzones)
