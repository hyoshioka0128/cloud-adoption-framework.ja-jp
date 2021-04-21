---
title: クラウド導入フレームワークのエンタープライズ規模用の terraform モジュール
description: クラウド導入フレームワークのエンタープライズ規模用の terraform モジュールの使用法を確認してください。
author: krowlandson
ms.author: brblanch
ms.date: 02/03/2021
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.custom: think-tank
ms.openlocfilehash: 230d4258762e05ee9320f32c9165b176119040a8
ms.sourcegitcommit: 51565dc4d3a1858bd62f708f2e4c082fbd4c6fe4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/20/2021
ms.locfileid: "107746516"
---
# <a name="terraform-module-for-cloud-adoption-framework-enterprise-scale"></a>クラウド導入フレームワークのエンタープライズ規模用の terraform モジュール

Azure には、ランディング ゾーンをデプロイするためのネイティブ サービスが用意されています。 他のサードパーティ製ツールも、この作業に役立ちます。 顧客やパートナーがランディング ゾーンのデプロイによく使用するツールの 1 つは、HashiCorp の Terraform です。

このセクションでは、 [クラウド導入フレームワークのエンタープライズ規模の公式 terraform モジュール](https://registry.terraform.io/modules/Azure/caf-enterprise-scale/azurerm/latest) を使用して、Terraform を使用して大規模な Azure ランディング ゾーンを管理するためのユーザー体験を加速させる方法を示します。

## <a name="purpose-of-enterprise-scale-landing-zones"></a>エンタープライズ規模のランディング ゾーンの目的

[クラウド導入フレームワークの Terraform モジュール](https://registry.terraform.io/modules/Azure/caf-enterprise-scale/azurerm/latest)は、エンタープライズ規模の[参照アーキテクチャ](./architecture.md#high-level-architecture)から terraform を使用して[管理グループとサブスクリプションの組織](./management-group-and-subscription-organization.md)階層を展開するための簡略化されたアプローチを提供し、次のことを可能にします。

1. *管理グループ* を使用する *サブスクリプション* 組織の管理対象リソース階層。
1. *Azure Policy* と *Access Control (IAM)* を使用したスケーラブルなセキュリティとガバナンスモデル。割り当てることができるカスタム定義の豊富なライブラリが用意されています。
1. *管理グループ* の継承によって、*サブスクリプション* 間でセキュリティとガバナンスの設定が適用されます。

これらの機能を 1 つの Terraform モジュールにパッケージ化することにより、大規模な運用時に、Azure プラットフォーム全体で一貫性のある構築と適用が容易になります。

## <a name="use-standard-modules"></a>標準モジュールを使用する

コンポーネントの再利用は、コードとしてのインフラストラクチャの基本原則です。 モジュールは、環境内および環境間でのリソースのデプロイにおける標準と一貫性の定義に役立ちます。 このモジュールは、公式の [Terraform レジストリ](https://registry.terraform.io/modules/Azure) に発行され、HashiCorp によって検証されます。

## <a name="architecture-diagram"></a>アーキテクチャの図

[クラウド導入フレームワークの Terraform モジュール](https://registry.terraform.io/modules/Azure/caf-enterprise-scale/azurerm/latest)では、エンタープライズ規模の[参照アーキテクチャ](./architecture.md#high-level-architecture)から次のコンポーネントを展開します。

![クラウド導入フレームワークの Terraform モジュールによってデプロイされたリソースの概要 ](media/terraform-caf-enterprise-scale-overview.png)
 *図 1: クラウド導入フレームワークのエンタープライズ規模の Terraform モジュールによってデプロイされたリソースの概要*。

## <a name="capabilities"></a>機能

デプロイされるコンポーネントとその目的は次のとおりです。

| コンポーネント | 担当 |
|---|---|
| 管理グループ | コア管理グループは、エンタープライズ規模の参照アーキテクチャのリソース階層の基礎を提供します。 <li> デモ ランディング ゾーン (SAP、corp、および online) のオプションの管理グループ <li> カスタム ランディング ゾーンのオプションの管理グループ (独自に定義) |
| Azure Policy | プラットフォーム全体にセキュリティとガバナンスを提供する Azure Policy: <li> 組み込みポリシーでカバーされていない一般的なガバナンス パターンを対象とするカスタム ポリシー定義とポリシー セット定義 <li> 管理グループ階層内の任意のスコープでポリシーを作成して割り当て、継承によってコンプライアンスが確実に適用されるようにする <li> 独自のカスタム定義で展開して、特定のセキュリティとガバナンスの要件を満たす </li> |
| アクセス制御 (IAM) 設定 | RBAC ポリシーに準拠するように、リソース階層でロールを作成して割り当てます。 <li> 最小限の特権の原則を保証するカスタムロール定義を作成する <li> 正しいスコープでロールの割り当てを作成し、プラットフォームとアプリケーションのチームが適切なアクセス許可を持っていることを確認する |
| サブスクリプション | サブスクリプションは管理グループに割り当てられます。 <li> ポリシーと RBAC の継承を有効にする <li> プラットフォームのセキュリティとガバナンスの要件への準拠を保証する |

## <a name="customize-and-deploy"></a>カスタマイズとデプロイ

このモジュールの概要を簡単にするために、 [Terraform レジストリ](https://registry.terraform.io/modules/Azure)に発行されています。 これにより、再利用可能なモジュールとしてレジストリから直接使用できるようになり、リリースされる更新プログラムの恩恵を受けることができます。

このモジュールの唯一の依存関係は次のとおりです。

- Terraform (*推奨バージョン0.13.2 以降*)
- AzureRM プロバイダー (*推奨バージョン 2.34.0 以降*)

> [!IMPORTANT]
> 一部の Terraform と AzureRM プロバイダーのバージョンの組み合わせには既知の問題があります。 これらのうちのいくつかは、修復された新しいバグによるものですが、一時的なエラーもあります。通常は、デプロイを再実行することによって解決できます。 通常、アップグレードする前に、特定のバージョンに固定し、テストを十分に行うことをお勧めします。 新しいバージョンのモジュールがリリースされると、プロジェクトチームは、最新の Terraform および AzureRM プロバイダーのバージョンとの互換性を確保するために、モジュールを再配置することを計画します。

### <a name="simple-example"></a>簡単な例

ルート モジュール`main.tf` の単純な開始構成を次に示します。

> [!TIP]
> モジュールには必須の変数が 1 つ `root_parent_id`だけあり ますが、`root_id`を設定することもお勧め します。 値を変更すると、 `root_id` ダウンストリームの依存関係を含む、モジュールによって管理されるすべてのリソースの完全な再展開が開始されます。

```hcl

# We strongly recommend using the required_providers block to set the
# Azure Provider source and version being used.

terraform {
  required_providers {
    azurerm = {
      source  = "hashicorp/azurerm"
      version = ">= 2.46.1"
    }
  }
}

provider "azurerm" {
  features {}
}

# You can use the azurerm_client_config data resource to dynamically
# extract the current Tenant ID from your connection settings.

data "azurerm_client_config" "current" {
}

# Call the caf-enterprise-scale module directly from the Terraform Registry
# pinning to the latest version

module "enterprise_scale" {
  source  = "Azure/caf-enterprise-scale/azurerm"
  version = "0.1.0"

  root_parent_id = data.azurerm_client_config.current.tenant_id
  root_id        = "demo"

}
```

このコード例では、エンタープライズ規模の参照アーキテクチャから、推奨される最小の [管理グループとサブスクリプションの組織](./management-group-and-subscription-organization.md) をデプロイします。 これを実行すると、デプロイのカスタマイズが開始されます。

> [!TIP]
> Terraform を初めて使用する場合は、HashiCorp の [学習](https://learn.hashicorp.com/tutorials/terraform/install-cli?in=terraform/azure-get-started)、terraform のインストールと使用に関するチュートリアル、および Azure でのプロバイダーの構成方法と [AzureRM プロバイダーガイド](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs#authenticating-to-azure) に関するページを参照してください。

## <a name="next-steps"></a>次の手順

クラウド導入フレームワークの Terraform モジュールは、エンタープライズ規模のランディング ゾーンを構築するための高速なパスを提供します。 また、各ランディング ゾーンの構成を管理するための簡略化されたアプローチを維持したまま、デプロイを拡張してカスタマイズするための柔軟性も備えています。

詳細については、Terraform レジストリのモジュールを参照してください。また、デプロイをカスタマイズする方法を説明する例やチュートリアルをさらに紹介する [GitHub wiki](https://github.com/Azure/terraform-azurerm-caf-enterprise-scale/wiki) をご覧ください。

> [!div class="nextstepaction"]
> [Terraform レジストリでモジュールを確認する](https://registry.terraform.io/modules/Azure/caf-enterprise-scale/azurerm/latest)
