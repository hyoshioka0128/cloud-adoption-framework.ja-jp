---
title: 実装ガイドライン
description: 実装ガイドライン。
author: BrianBlanchard
ms.author: brblanch
ms.date: 06/15/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.openlocfilehash: 392e1e9625f631a76cd7584d4cc31485bcea8f0a
ms.sourcegitcommit: 7c16b2857b00520bec3c4f6e9844ceac33970846
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2020
ms.locfileid: "85766788"
---
<!-- cSpell:ignore interdomain VMSS -->

# <a name="implementation-guidelines"></a>実装ガイドライン

このセクションでは、エンタープライズ規模のプラットフォーム ネイティブ リファレンス実装を開始する方法と、設計目標の概要について説明します。

エンタープライズ規模のアーキテクチャを実装するためには、次の 3 つのカテゴリのアクティビティを実行する必要があります。

1. **エンタープライズ規模のアーキテクチャに該当するもの:** 初期構成を確立するために Azure と Azure AD の管理者が実行する必要があるアクティビティが含まれます。 これらのアクティビティは本質的にシーケンシャルであり、主に 1 回限りのアクティビティです。

2. **新しいリージョンを有効にする ( _[ファイル] -> [新規] -> [リージョン]_ ):** エンタープライズ規模のプラットフォームを新しい Azure リージョンに拡張する必要がある場合に常に必要な一連のアクティビティ。

3. **新しいランディング ゾーンをデプロイする ( _[ファイル] -> [新規] -> [Landing Zone]\(ランディング ゾーン\)_ ):** これらは、新しいランディング ゾーンのインスタンスを作成するために必要な定期的なアクティビティです。

大規模に運用するには、これらのアクティビティがコードとしてのインフラストラクチャ (IaC) の原則に従う必要があり、デプロイ パイプラインを使用して自動化される必要があります。

## <a name="what-must-be-true-for-an-enterprise-scale-landing-zone"></a>エンタープライズ規模のランディング ゾーンに該当すること

### <a name="enterprise-agreement-ea-enrollment-and-azure-ad-tenants"></a>Enterprise Agreement (EA) の登録と Azure AD テナント

1. EA 管理者と通知アカウントを設定します。

2. 部署 (事業領域/地域ベース/組織) を作成します。

3. 部署の下に EA アカウントを作成します。

4. オンプレミスから ID を同期する場合は、Azure AD テナントごとに Azure AD Connect を設定します。

5. Azure リソースへのゼロ スタンディング アクセスと、Azure AD PIM 経由の Just-In-Time アクセスを確立します。

### <a name="management-group-and-subscription"></a>管理グループとサブスクリプション

1. こちらの[記事](./management-group-and-subscription-organization.md#define-a-management-group-hierarchy)の推奨事項に従って、管理グループ階層を作成します。

2. サブスクリプション プロビジョニング基準と、サブスクリプション所有者の責任を定義します。

3. プラットフォーム管理、グローバル ネットワーク、接続と ID のリソース (AD ドメイン コントローラーなど) 用に、管理、接続、および ID の各サブスクリプションを作成します。

4. プラットフォームの CI/CD パイプラインで使用するために IaC とサービス プリンシパルをホストするように Git リポジトリを設定します。

5. Azure AD PIM を使用してサブスクリプションおよび管理グループ スコープのカスタム ロール定義を作成し、エンタイトルメントを管理します。

6. ランディング ゾーンに対して、以下の表の Azure Policy 割り当てを作成します。

| 名前                  | 説明                                                                                   |
|-----------------------|-----------------------------------------------------------------------------------------------|
| [Deny-PublicEndpoints](https://github.com/Azure/Enterprise-Scale/tree/main/azopsreference/3fc1081d-6105-4e19-b60c-1ec1252cf560/contoso/.AzState/Microsoft.Authorization_policySetDefinitions-Deny-PublicEndpoints.parameters.json)  | すべてのランディング ゾーンでパブリック エンドポイントを使用したサービスの作成を拒否します。                    |
| [Deploy-VM-Backup](https://github.com/Azure/Enterprise-Scale/tree/main/azopsreference/3fc1081d-6105-4e19-b60c-1ec1252cf560/contoso/.AzState/Microsoft.Authorization_policyDefinitions-Deploy-VM-Backup.parameters.json)      | バックアップが構成され、ランディング ゾーン内のすべての VM に配置されるようにします。                |
| [Deploy-VNet](https://github.com/Azure/Enterprise-Scale/tree/main/azopsreference/3fc1081d-6105-4e19-b60c-1ec1252cf560/contoso/.AzState/Microsoft.Authorization_policyDefinitions-Deploy-vNet.parameters.json)           | すべてのランディング ゾーンに VNet がデプロイされ、リージョンの仮想ハブにピアリングされるようにします。 |

### <a name="global-networking-and-connectivity"></a>グローバル ネットワークと接続性

1. 仮想ハブと VNet がデプロイされる Azure リージョンごとに適切な VNet CIDR 範囲を割り当てます

2. Azure Policy を介してネットワーク リソースを作成する場合は、以下の表に記載されているポリシーを接続サブスクリプションに割り当てます。 こうすることで、指定されたパラメーターに基づいて以下の一覧のリソースが作成されることが Azure Policy により保証されます。
   - Azure Virtual WAN Standard を作成します。
   - リージョンごとに Azure Virtual WAN 仮想ハブを作成します。 仮想ハブごとに少なくとも 1 つのゲートウェイ (ExpressRoute または VPN) がデプロイされていることを確認します。
   - 各仮想ハブ内に Azure Firewall をデプロイすることにより、仮想ハブをセキュリティで保護します。
   - 必要な Azure Firewall ポリシーを作成し、セキュリティで保護された仮想ハブに割り当てます。
   - セキュリティで保護された仮想ハブに接続されているすべての VNet が Azure Firewall で保護されていることを確認します。

3. Azure プライベート DNS ゾーンをデプロイして構成します。

4. Azure プライベート ピアリングを使用して ExpressRoute 回線をプロビジョニングします。 「[ExpressRoute 回線のピアリングの作成と変更を行う](https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager#private)」の手順に従います。

5. ExpressRoute 回線を介してオンプレミスの HQ および DC を Azure Virtual WAN 仮想ハブに接続します。

6. NSG を使用して、仮想ハブ間の VNet トラフィックを保護します。

7. (省略可能:)ExpressRoute プライベート ピアリングで暗号化を設定します。 「[ExpressRoute の暗号化: Virtual WAN 向けの ExpressRoute 経由の IPsec](https://docs.microsoft.com/en-us/azure/virtual-wan/vpn-over-expressroute)」の手順に従います。

8. (省略可能:)VPN 経由でブランチを仮想ハブに接続します。 「[Azure Virtual WAN を使用してサイト間接続を作成する](https://docs.microsoft.com/azure/virtual-wan/virtual-wan-site-to-site-portal)」の手順に従います。

9. (省略可能:)ExpressRoute を介して Azure に複数のオンプレミスの場所が接続されている場合に、オンプレミス HQ および DC に接続するように ExpressRoute Global Reach を構成します。 「[ExpressRoute Global Reach を構成する](https://docs.microsoft.com/azure/expressroute/expressroute-howto-set-global-reach)」の手順に従います。

次の一覧は、エンタープライズ規模のデプロイ用のネットワーク リソースを実装するときに使用される Azure Policy 割り当てを示しています。

| 名前                     | 説明                                                                            |
|--------------------------|----------------------------------------------------------------------------------------|
| [Deploy-FirewallPolicy](https://github.com/Azure/Enterprise-Scale/tree/main/azopsreference/3fc1081d-6105-4e19-b60c-1ec1252cf560/contoso/.AzState/Microsoft.Authorization_policyDefinitions-Deploy-FirewallPolicy.parameters.json)  | ファイアウォール ポリシーを作成します。 |
| [Deploy-VHub](https://github.com/Azure/Enterprise-Scale/tree/main/azopsreference/3fc1081d-6105-4e19-b60c-1ec1252cf560/contoso/.AzState/Microsoft.Authorization_policyDefinitions-Deploy-vHUB.parameters.json)        | このポリシーは、仮想ハブ、Azure Firewall、ゲートウェイ (VPN/ExpressRoute) をデプロイし、接続されている VNet の Azure Firewall への既定のルートを構成します。 |
| [Deploy-VWAN](https://github.com/Azure/Enterprise-Scale/tree/main/azopsreference/3fc1081d-6105-4e19-b60c-1ec1252cf560/contoso/.AzState/Microsoft.Authorization_policyDefinitions-Deploy-vWAN.parameters.json)            | 仮想 WAN をデプロイします                             |

### <a name="security-governance-and-compliance"></a>セキュリティ、ガバナンス、およびコンプライアンス

1. [サービス有効化フレームワーク](./security-governance-and-compliance.md#service-enablement-framework)を定義および適用して、Azure サービスがエンタープライズのセキュリティとガバナンスの要件を満たすようにします。

2. カスタム Azure RBAC ロールの定義を作成します。

3. 特権 ID 管理を容易にするために、Azure AD PIM を有効にし、Azure リソースを検出します。

4. Azure AD PIM を使用して、リソースの Azure コントロール プレーン管理用の Azure AD のみのグループを作成します。

5. Azure サービスがエンタープライズ要件に確実に準拠するように、以下の最初の表に記載されているポリシーを適用します。

6. 名前付け規則を定義し、Azure Policy を介して適用します。

7. すべてのスコープでポリシー マトリックスを作成します (たとえば、Azure Policy を介してすべての Azure サービスの監視を有効にします)。

会社全体のコンプライアンス状態を適用するには、次のポリシーを使用する必要があります。

| 名前                       | 説明                                                        |
|----------------------------|--------------------------------------------------------------------|
| [Allowed-ResourceLocation](https://github.com/Azure/Enterprise-Scale/tree/main/azopsreference/3fc1081d-6105-4e19-b60c-1ec1252cf560/contoso/.AzState/Microsoft.Authorization_policyDefinitions-Allowed-ResourceLocation.parameters.json)   | リソースをデプロイできる許可されたリージョンを指定します       |
| [Allowed-RGLocation](https://github.com/Azure/Enterprise-Scale/tree/main/azopsreference/3fc1081d-6105-4e19-b60c-1ec1252cf560/contoso/.AzState/Microsoft.Authorization_policyDefinitions-Allowed-RGLocation.parameters.json)         | リソース グループをデプロイできる許可されたリージョンを指定します |
| [Denied-Resources](https://github.com/Azure/Enterprise-Scale/tree/main/azopsreference/3fc1081d-6105-4e19-b60c-1ec1252cf560/contoso/.AzState/Microsoft.Authorization_policyDefinitions-Denied-Resources.parameters.json)           | 会社に対して拒否されているリソース                           |
| [Deny-AppGW-Without-WAF](https://github.com/Azure/Enterprise-Scale/tree/main/azopsreference/3fc1081d-6105-4e19-b60c-1ec1252cf560/contoso/.AzState/Microsoft.Authorization_policyDefinitions-Deny-AppGW-Without-WAF.parameters.json)     | WAF を有効にした Application Gateway のデプロイを許可します              |
| [Deny-IP-Forwarding](https://github.com/Azure/Enterprise-Scale/tree/main/azopsreference/3fc1081d-6105-4e19-b60c-1ec1252cf560/contoso/.AzState/Microsoft.Authorization_policyDefinitions-Deny-IP-Forwarding.parameters.json)         | IP 転送を拒否します                                                 |
| [Deny-RDP-From-Internet](https://github.com/Azure/Enterprise-Scale/tree/main/azopsreference/3fc1081d-6105-4e19-b60c-1ec1252cf560/contoso/.AzState/Microsoft.Authorization_policyDefinitions-Deny-RDP-From-Internet.parameters.json)     | インターネットからの RDP 接続を拒否します                                 |
| [Deny-Subnet-Without-Nsg](https://github.com/Azure/Enterprise-Scale/tree/main/azopsreference/3fc1081d-6105-4e19-b60c-1ec1252cf560/contoso/.AzState/Microsoft.Authorization_policyDefinitions-Deny-Subnet-Without-Nsg.parameters.json)    | NSG を使用しないサブネットの作成を拒否します                                |
| [Deploy-ASC-CE](https://github.com/Azure/Enterprise-Scale/tree/main/azopsreference/3fc1081d-6105-4e19-b60c-1ec1252cf560/contoso/.AzState/Microsoft.Authorization_policyDefinitions-Deploy-ASC-CE.parameters.json)              | ワークスペースに ASC 連続エクスポートをデプロイします                          |
| [Deploy-ASC-Monitoring](https://github.com/Azure/Enterprise-Scale/tree/main/azopsreference/3fc1081d-6105-4e19-b60c-1ec1252cf560/contoso/.AzState/Microsoft.Authorization_policyDefinitions-Deploy-ASC-Monitoring.parameters.json)      | Azure Security Center で監視を有効にします                         |
| [Deploy-ASC-Standard](https://github.com/Azure/Enterprise-Scale/tree/main/azopsreference/3fc1081d-6105-4e19-b60c-1ec1252cf560/contoso/.AzState/Microsoft.Authorization_policyDefinitions-Deploy-ASC-Standard.parameters.json)        | サブスクリプションで Security Center Standard が確実に有効であるようにします。  |
| [Deploy-Diag-ActivityLog](https://github.com/Azure/Enterprise-Scale/tree/main/azopsreference/3fc1081d-6105-4e19-b60c-1ec1252cf560/contoso/.AzState/Microsoft.Authorization_policyDefinitions-Deploy-Diag-ActivityLog.parameters.json)    | 診断アクティビティ ログを有効にし、LA に転送します             |
| [Deploy-Diag-LogAnalytics](https://github.com/Azure/Enterprise-Scale/tree/main/azopsreference/3fc1081d-6105-4e19-b60c-1ec1252cf560/contoso/.AzState/Microsoft.Authorization_policyDefinitions-Deploy-Diag-LogAnalytics.parameters.json)   |                                                                    |
| [Deploy-VM-Monitoring](https://github.com/Azure/Enterprise-Scale/tree/main/azopsreference/3fc1081d-6105-4e19-b60c-1ec1252cf560/contoso/.AzState/Microsoft.Authorization_policyDefinitions-Deploy-VM-Monitoring.parameters.json)       | VM の監視が確実に有効であるようにします  

### <a name="platform-indentity"></a>プラットフォーム ID

1. Azure Policy 経由で ID リソースを作成する場合は、以下の表に記載されているポリシーを ID サブスクリプションに割り当てます。 こうすることで、指定されたパラメーターに基づいて以下の一覧のリソースが作成されることが Azure Policy により保証されます。

2. AD ドメイン コントローラーをデプロイします。

次の一覧は、エンタープライズ規模のデプロイ用の ID リソースを実装するときに使用できるポリシーを示しています。

| 名前                         | 説明                                                               |
|------------------------------|---------------------------------------------------------------------------|
| [DataProtectionSecurityCenter](https://github.com/Azure/Enterprise-Scale/tree/main/azopsreference/3fc1081d-6105-4e19-b60c-1ec1252cf560/contoso/.AzState/Microsoft.Authorization_policyDefinitions-DataProtectionSecurityCenter.parameters.json) | Azure Security Center によって自動的に作成された ASC DataProtection         |
| [Deploy-VNet-Identity](https://github.com/Azure/Enterprise-Scale/tree/main/azopsreference/3fc1081d-6105-4e19-b60c-1ec1252cf560/contoso/.AzState/Microsoft.Authorization_policyDefinitions-Deploy-vNet.parameters.json)         | たとえば DC をホストする ID サブスクリプションに VNet をデプロイします。     |

### <a name="platform-management-and-monitoring"></a>プラットフォームの管理と監視

1. 組織およびリソース中心のビューのためのポリシー コンプライアンスとセキュリティ ダッシュボードを作成します。

2. プラットフォーム シークレット (サービス プリンシパルと Automation アカウント) とキー ロールオーバーのワークフローを作成します。

3. Log Analytics 内のログの長期的なアーカイブと保持を設定します。

4. プラットフォーム シークレットを格納する Azure Key Vault を設定します。

5. Azure Policy を介してプラットフォーム管理リソースを作成する場合は、以下の表に記載されているポリシーを管理サブスクリプションに割り当てます。 こうすることで、指定されたパラメーターに基づいて以下の一覧のリソースが作成されることが Azure Policy により保証されます。

| 名前                   | 説明                                                                            |
|------------------------|----------------------------------------------------------------------------------------|
| [Deploy-LA-Config](https://github.com/Azure/Enterprise-Scale/tree/main/azopsreference/3fc1081d-6105-4e19-b60c-1ec1252cf560/contoso/.AzState/Microsoft.Authorization_policyDefinitions-Deploy-LA-Config.parameters.json)       | Log Analytics ワークスペースの構成                                           |
| [Deploy-Log-Analytics](https://github.com/Azure/Enterprise-Scale/tree/main/azopsreference/3fc1081d-6105-4e19-b60c-1ec1252cf560/contoso/.AzState/Microsoft.Authorization_policyDefinitions-Deploy-Log-Analytics.parameters.json)   | Log Analytics ワークスペースをデプロイします |

## <a name="file--new--region"></a>**[ファイル] -> [新規] -> [リージョン]**

1. Azure Policy を介してネットワーク リソースを作成する場合は、以下の表に記載されているポリシーを接続サブスクリプションに割り当てます。 こうすることで、指定されたパラメーターに基づいて以下の一覧のリソースが作成されることが Azure Policy により保証されます。
   - 接続サブスクリプション内で、既存の Virtual WAN 内に新しい仮想ハブを作成します。
   - Azure Firewall を仮想ハブ内にデプロイして、仮想ハブをセキュリティで保護し、既存または新規のファイアウォール ポリシーを Azure Firewall にリンクします。
   - セキュリティで保護された仮想ハブに接続されているすべての VNet が Azure Firewall で保護されていることを確認します。

2. ExpressRoute または VPN を使用して、仮想ハブをオンプレミスのネットワークに接続します。

3. NSG を使用して、仮想ハブ間の VNet トラフィックを保護します。

4. (省略可能:)ExpressRoute プライベート ピアリングで暗号化を設定します。

| 名前                     | 説明                                                                            |
|--------------------------|----------------------------------------------------------------------------------------|
| [Deploy-VHub](https://github.com/Azure/Enterprise-Scale/tree/main/azopsreference/3fc1081d-6105-4e19-b60c-1ec1252cf560/contoso/.AzState/Microsoft.Authorization_policyDefinitions-Deploy-vHUB.parameters.json)        | このポリシーは、仮想ハブ、Azure Firewall、ゲートウェイ (VPN/ExpressRoute) をデプロイし、接続されている VNet の Azure Firewall への既定のルートを構成します。 |

## <a name="file---new---landing-zone-for-applications-and-workloads"></a>アプリケーションおよびワークロード用の [ファイル] -> [新規] -> [Landing zone]\(ランディング ゾーン\)

1. サブスクリプションを作成し、`Landing Zones` 管理グループのスコープの下に移動します。

2. サブスクリプションに対して Azure AD グループ (所有者、閲覧者、共同作成者など) を作成します。

3. 確立された Azure AD グループの Azure AD PIM のエンタイトルメントを作成します。
