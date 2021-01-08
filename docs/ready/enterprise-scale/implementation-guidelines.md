---
title: エンタープライズ規模の実装ガイドライン
description: Azure 向けの Microsoft Cloud 導入フレームワークのエンタープライズ規模の実装ガイドラインについて説明します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 06/15/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.custom: think-tank
ms.openlocfilehash: 20c539f824699f7533007c29d95f0bb87f551a4e
ms.sourcegitcommit: 6ff3d529461fbf159bb219d3d869613e80a1f3c6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/22/2020
ms.locfileid: "97736856"
---
<!-- cSpell:ignore interdomain VMSS VWAN -->

# <a name="enterprise-scale-implementation-guidelines"></a>エンタープライズ規模の実装ガイドライン

この記事では、エンタープライズ規模のプラットフォーム ネイティブ リファレンス実装を開始する方法と、設計目標の概要について説明します。

エンタープライズ規模のアーキテクチャを実装するには、次のカテゴリのアクティビティの観点から考える必要があります。

<!-- docutune:disable -->

1. **エンタープライズ規模のアーキテクチャに該当するもの:** 初期構成を確立するために Azure および Azure Active Directory (Azure AD) の管理者が実行する必要があるアクティビティが含まれます。 これらのアクティビティは本質的にシーケンシャルであり、主に 1 回限りのアクティビティです。

2. **新しいリージョンを有効にする ([ファイル]、[新規]、[リージョン] の順に選択):** エンタープライズ規模のプラットフォームを新しい Azure リージョンに拡張する必要がある場合に常に必要なアクティビティが含まれます。

3. **新しいランディング ゾーンをデプロイする ([ファイル]、[新規]、[Landing Zone]\(ランディング ゾーン\)):** これらは、新しいランディング ゾーンのインスタンスを作成するために必要な定期的なアクティビティです。

<!-- docutune:enable -->

大規模に運用するには、これらのアクティビティがコードとしてのインフラストラクチャ (IaC) の原則に従っている必要があり、デプロイ パイプラインを使用して自動化される必要があります。

## <a name="what-must-be-true-for-an-enterprise-scale-landing-zone"></a>エンタープライズ規模のランディング ゾーンに該当すること

以降のセクションでは、Azure 向けの Microsoft Cloud 導入フレームワークでこのアクティビティのカテゴリを完了する手順について説明します。

### <a name="enterprise-agreement-enrollment-and-azure-ad-tenants"></a>Enterprise Agreement の登録と Azure AD テナント

1. Enterprise Agreement (EA) 管理者と通知アカウントを設定します。

2. 部署 (事業領域/地域ベース/組織) を作成します。

3. 部署の下に EA アカウントを作成します。

4. オンプレミスから ID を同期する場合は、Azure AD テナントごとに Azure AD Connect を設定します。

5. Azure リソースへのゼロ スタンディング アクセスと、Azure AD Privileged Identity Management (PIM) によるジャストインタイム アクセスを確立します。

### <a name="management-group-and-subscription"></a>管理グループとサブスクリプション

1. [管理グループとサブスクリプションの組織](./management-group-and-subscription-organization.md#define-a-management-group-hierarchy)の推奨事項に従って、管理グループ階層を作成します。

2. サブスクリプション プロビジョニングの基準とサブスクリプションの所有者の責任を定義します。

3. プラットフォーム管理、グローバル ネットワーク、接続と ID のリソース (Active Directory ドメイン コントローラーなど) 用に、管理、接続、および ID の各サブスクリプションを作成します。

4. 継続的インテグレーションと継続的配置を実現するために、プラットフォーム パイプラインで使用する IaC とサービス プリンシパルをホストする Git リポジトリを設定します。

5. Azure AD PIM を使用してサブスクリプションおよび管理グループ スコープのカスタム ロール定義を作成し、エンタイトルメントを管理します。

6. ランディング ゾーンに対して、次の表の Azure Policy 割り当てを作成します。

   | 名前 | 説明 |
   |--|--|
   | [`Deny-PublicEndpoints`](https://github.com/Azure/Enterprise-Scale/tree/main/azopsreference/3fc1081d-6105-4e19-b60c-1ec1252cf560%20(3fc1081d-6105-4e19-b60c-1ec1252cf560)) /.AzState/Microsoft.Authorization_policySetDefinitions-Deny-PublicEndpoints.parameters.json) | すべてのランディング ゾーンでパブリック エンドポイントを使用したサービスの作成を拒否します。 |
   | [`Deploy-VM-Backup`](https://github.com/Azure/Enterprise-Scale/tree/main/azopsreference/3fc1081d-6105-4e19-b60c-1ec1252cf560%20(3fc1081d-6105-4e19-b60c-1ec1252cf560)) /Landing%20Zones%20(landingzones) /.AzState/Microsoft.Authorization_policyAssignments-Deploy-VM-Backup.parameters.json) | バックアップが構成され、ランディング ゾーン内のすべての VM に配置されるようにします。 |
   | [`Deploy-VNet`](https://github.com/Azure/Enterprise-Scale/tree/main/azopsreference/3fc1081d-6105-4e19-b60c-1ec1252cf560%20(3fc1081d-6105-4e19-b60c-1ec1252cf560)) /.AzState/Microsoft.Authorization_policyDefinitions-Deploy-vNet.parameters.json) | すべてのランディング ゾーンに仮想ネットワークがデプロイされ、リージョンの仮想ハブにピアリングされるようにします。 |

#### <a name="sandbox-governance-guidance"></a>サンドボックス ガバナンスのガイダンス

[「管理グループとサブスクリプションの組織」の重要な設計領域](./management-group-and-subscription-organization.md)で詳しく説明したように、サンドボックス管理グループ階層内に配置されたサブスクリプションには、より制限の緩いポリシー手法を取る必要があります。 これらのサブスクリプションは、業務内でユーザーが Azure の製品やサービスを試してそれらによるイノベーションを行うために使用する必要があるため、正式な開発環境に移行する前に、そのアイデアや概念が機能するかどうかを検証することは、ランディング ゾーン階層ではまだ許可されていない可能性があります。

ただし、サンドボックス管理グループ階層内のこれらのサブスクリプションにおいても、適切に使用される (イノベーションのための、新しい Azure サービスと機能の実験と、観念化の検証など) ように、いくつかのガードレールが必要です。

**そのため、次のことをお勧めします。**

1. サンドボックス管理グループのスコープで、次の表の Azure Policy 割り当てを作成します。

  | 名前                  |     説明                                                                                     | 割り当てに関する注釈 |
  |-----------------------|-----------------------------------------------------------------------------------------------|--------------------------------------------------------------|
  | [`Deny-VNET-Peering-Cross-Subscription`](https://github.com/Azure/Enterprise-Scale/tree/main/azopsreference/3fc1081d-6105-4e19-b60c-1ec1252cf560%20(3fc1081d-6105-4e19-b60c-1ec1252cf560)/) /.AzState) | サブスクリプションの外部にある他の VNET への VNET ピアリング接続が作成されないようにします。 | このポリシーがサンドボックス管理グループ階層のスコープ レベルにのみ割り当てられるようにしてください。 |
  | [`Denied-Resources`](https://github.com/Azure/Enterprise-Scale/tree/main/azopsreference/3fc1081d-6105-4e19-b60c-1ec1252cf560%20(3fc1081d-6105-4e19-b60c-1ec1252cf560)) /.AzState/Microsoft.Authorization_policyAssignments-Denied-Resources.parameters.json) | サンドボックス サブスクリプションでの作成が拒否されるリソース。 これにより、ハイブリッド接続リソース (*VPN、ExpressRoute、Virtual WAN* など) が作成されなくなります。 | このポリシーを割り当てるときは、次のリソースを選択して、その作成を拒否します。VPN ゲートウェイ: `microsoft.network/vpngateways`、P2S ゲートウェイ: `microsoft.network/p2svpngateways`、仮想 WAN: `microsoft.network/virtualwans`、仮想 WAN ハブ: `microsoft.network/virtualhubs`、ExpressRoute 回線: `microsoft.network/expressroutecircuits`、ExpressRoute ゲートウェイ: `microsoft.network/expressroutegateways`、ExpressRoute ポート: `microsoft.network/expressrouteports`、ExpressRoute 交差接続: `microsoft.network/expressroutecrossconnections` およびローカル ネットワーク ゲートウェイ: `microsoft.network/localnetworkgateways`。 |
  | [`Deploy-Budget-Sandbox`](https://github.com/Azure/Enterprise-Scale/tree/main/azopsreference/3fc1081d-6105-4e19-b60c-1ec1252cf560%20(3fc1081d-6105-4e19-b60c-1ec1252cf560)/) /.AzState) | 各サンドボックス サブスクリプションに対して、電子メール アラートが有効になっている予算が確実に存在するようにします。 この予算は、各サブスクリプションで `default-sandbox-budget` という名前になります。 | ポリシーの割り当て時に、パラメーターが既定値から変更されていない場合、予算 (`default-sandbox-budget`) は 1000 通貨しきい値の制限付きで作成され、サブスクリプションの所有者と共同作成者 (Azure ロールの割り当てに基づきます) に、予算しきい値の 90% と 100% で電子メール アラートが送信されます。 |

### <a name="global-networking-and-connectivity"></a>グローバル ネットワークと接続性

1. 仮想ハブと仮想ネットワークがデプロイされる Azure リージョンごとに適切な仮想ネットワーク CIDR 範囲を割り当てます。

2. Azure Policy を介してネットワーク リソースを作成する場合は、次の表に記載されているポリシーを接続サブスクリプションに割り当てます。 こうすることで、指定されたパラメーターに基づいて次の一覧のリソースが作成されることが Azure Policy により保証されます。
   - Azure Virtual WAN Standard インスタンスを作成します。
   - リージョンごとに Azure Virtual WAN 仮想ハブを作成します。 仮想ハブごとに少なくとも 1 つのゲートウェイ (Azure ExpressRoute または VPN) を確実にデプロイします。
   - 各仮想ハブ内に Azure Firewall をデプロイすることにより、仮想ハブをセキュリティで保護します。
   - 必要な Azure Firewall ポリシーを作成し、セキュリティで保護された仮想ハブに割り当てます。
   - セキュリティで保護された仮想ハブに接続されているすべての仮想ネットワークが Azure Firewall で保護されていることを確保します。

3. Azure プライベート DNS ゾーンをデプロイして構成します。

4. Azure プライベート ピアリングを使用して ExpressRoute 回線をプロビジョニングします。 「[ExpressRoute 回線のピアリングの作成と変更を行う](/azure/expressroute/expressroute-howto-routing-portal-resource-manager#private)」の手順に従います。

5. ExpressRoute 回線を介してオンプレミスの HQ および DC を Azure Virtual WAN 仮想ハブに接続します。

6. ネットワーク セキュリティ グループ (NSG) を使用して、仮想ハブ間の仮想ネットワーク トラフィックを保護します。

7. (省略可能) ExpressRoute プライベート ピアリングで暗号化を設定します。 「[ExpressRoute 暗号化:Virtual WAN 向けの ExpressRoute 経由の IPsec](/azure/virtual-wan/vpn-over-expressroute)」の手順に従います。

8. (省略可能) VPN 経由でブランチを仮想ハブに接続します。 「[Azure Virtual WAN を使用してサイト間接続を作成する](/azure/virtual-wan/virtual-wan-site-to-site-portal)」の手順に従います。

9. (省略可能) ExpressRoute を介して Azure に複数のオンプレミスの場所が接続されている場合に、オンプレミス HQ および DC に接続するように ExpressRoute Global Reach を構成します。 「[ExpressRoute Global Reach を構成する](/azure/expressroute/expressroute-howto-set-global-reach)」の手順に従います。

次の一覧は、エンタープライズ規模のデプロイ用のネットワーク リソースを実装するときに使用する Azure Policy 割り当てを示しています。

| 名前                     | 説明                                                                            |
|--------------------------|----------------------------------------------------------------------------------------|
| [`Deploy-FirewallPolicy`](https://github.com/Azure/Enterprise-Scale/tree/main/azopsreference/3fc1081d-6105-4e19-b60c-1ec1252cf560%20(3fc1081d-6105-4e19-b60c-1ec1252cf560)) /.AzState/Microsoft.Authorization_policyDefinitions-Deploy-FirewallPolicy.parameters.json) | ファイアウォール ポリシーを作成します。 |
| [`Deploy-VHub`](https://github.com/Azure/Enterprise-Scale/tree/main/azopsreference/3fc1081d-6105-4e19-b60c-1ec1252cf560%20(3fc1081d-6105-4e19-b60c-1ec1252cf560)) /.AzState/Microsoft.Authorization_policyDefinitions-Deploy-vHUB.parameters.json) | このポリシーにより、仮想ハブ、Azure Firewall、および VPN または ExpressRoute ゲートウェイがデプロイされます。 また、Azure Firewall への接続された仮想ネットワークのデフォルト ルートも構成されます。 |
| [`Deploy-VWAN`](https://github.com/Azure/Enterprise-Scale/tree/main/azopsreference/3fc1081d-6105-4e19-b60c-1ec1252cf560%20(3fc1081d-6105-4e19-b60c-1ec1252cf560)) /.AzState/Microsoft.Authorization_policyDefinitions-Deploy-vWAN.parameters.json) | 仮想 WAN をデプロイします。 |

### <a name="security-governance-and-compliance"></a>セキュリティ、ガバナンス、およびコンプライアンス

1. [サービス有効化フレームワーク](./security-governance-and-compliance.md#service-enablement-framework)を定義および適用して、Azure サービスがエンタープライズのセキュリティとガバナンスの要件を満たすようにします。

2. カスタム ロールの定義を作成します。

3. Azure AD PIM を有効にし、Azure リソースを検出して PIM を容易にします。

4. Azure AD PIM を使用して、リソースの Azure コントロール プレーン管理用の Azure AD のみのグループを作成します。

5. Azure サービスがエンタープライズ要件に確実に準拠するように、次の表に記載されているポリシーを適用します。

6. 名前付け規則を定義し、Azure Policy を介して適用します。

7. すべてのスコープでポリシー マトリックスを作成します (たとえば、Azure Policy を介してすべての Azure サービスの監視を有効にします)。

会社全体のコンプライアンス状態を適用するには、次のポリシーを使用する必要があります。

| 名前                       | 説明                                                        |
|----------------------------|--------------------------------------------------------------------|
| [`Allowed-ResourceLocation`](https://github.com/Azure/Enterprise-Scale/tree/main/azopsreference/3fc1081d-6105-4e19-b60c-1ec1252cf560%20(3fc1081d-6105-4e19-b60c-1ec1252cf560)) /.AzState/Microsoft.Authorization_policyAssignments-Allowed-ResourceLocation.parameters.json)   | リソースをデプロイできる許可されたリージョンを指定します |
| [`Allowed-RGLocation`](https://github.com/Azure/Enterprise-Scale/tree/main/azopsreference/3fc1081d-6105-4e19-b60c-1ec1252cf560%20(3fc1081d-6105-4e19-b60c-1ec1252cf560)) /.AzState/Microsoft.Authorization_policyAssignments-Allowed-RGLocation.parameters.json)         | リソース グループをデプロイできる許可されたリージョンを指定します。 |
| [`Denied-Resources`](https://github.com/Azure/Enterprise-Scale/tree/main/azopsreference/3fc1081d-6105-4e19-b60c-1ec1252cf560%20(3fc1081d-6105-4e19-b60c-1ec1252cf560)) /.AzState/Microsoft.Authorization_policyAssignments-Denied-Resources.parameters.json)           | 会社に対して拒否されているリソース。 |
| [`Deny-AppGW-Without-WAF`](https://github.com/Azure/Enterprise-Scale/tree/main/azopsreference/3fc1081d-6105-4e19-b60c-1ec1252cf560%20(3fc1081d-6105-4e19-b60c-1ec1252cf560)) /.AzState/Microsoft.Authorization_policyDefinitions-Deny-AppGW-Without-WAF.parameters.json)     | Azure Web アプリケーション ファイアウォールを有効にしてデプロイされたアプリケーション ゲートウェイを許可します。 |
| [`Deny-IP-Forwarding`](https://github.com/Azure/Enterprise-Scale/tree/main/azopsreference/3fc1081d-6105-4e19-b60c-1ec1252cf560%20(3fc1081d-6105-4e19-b60c-1ec1252cf560)) /.AzState/Microsoft.Authorization_policyAssignments-Deny-IP-Forwarding.parameters.json)         | IP 転送を拒否します。 |
| [`Deny-RDP-From-Internet`](https://github.com/Azure/Enterprise-Scale/tree/main/azopsreference/3fc1081d-6105-4e19-b60c-1ec1252cf560%20(3fc1081d-6105-4e19-b60c-1ec1252cf560)) /.AzState/Microsoft.Authorization_policyAssignments-Deny-RDP-From-Internet.parameters.json)     | インターネットからの RDP 接続を拒否します。 |
| [`Deny-Subnet-Without-Nsg`](https://github.com/Azure/Enterprise-Scale/tree/main/azopsreference/3fc1081d-6105-4e19-b60c-1ec1252cf560%20(3fc1081d-6105-4e19-b60c-1ec1252cf560)) /.AzState/Microsoft.Authorization_policyDefinitions-Deny-Subnet-Without-Nsg.parameters.json)    | NSG を使用しないサブネットの作成を拒否します。 |
| [`Deploy-ASC-CE`](https://github.com/Azure/Enterprise-Scale/tree/main/azopsreference/3fc1081d-6105-4e19-b60c-1ec1252cf560%20(3fc1081d-6105-4e19-b60c-1ec1252cf560)) /.AzState/Microsoft.Authorization_policyDefinitions-Deploy-ASC-CE.parameters.json)              | Log Analytics ワークスペースへの Azure Security Center 連続エクスポートを設定します。 |
| [`Deploy-ASC-Monitoring`](https://github.com/Azure/Enterprise-Scale/tree/main/azopsreference/3fc1081d-6105-4e19-b60c-1ec1252cf560%20(3fc1081d-6105-4e19-b60c-1ec1252cf560)) /.AzState/Microsoft.Authorization_policyAssignments-Deploy-ASC-Monitoring.parameters.json)      | Security Center での監視を有効にします。 |
| [`Deploy-ASC-Standard`](https://github.com/Azure/Enterprise-Scale/tree/main/azopsreference/3fc1081d-6105-4e19-b60c-1ec1252cf560%20(3fc1081d-6105-4e19-b60c-1ec1252cf560)) /.AzState/Microsoft.Authorization_policyDefinitions-Deploy-ASC-Standard.parameters.json)        | サブスクリプションで Security Center Standard が確実に有効であるようにします。 |
| [`Deploy-Diag-ActivityLog`](https://github.com/Azure/Enterprise-Scale/tree/main/azopsreference/3fc1081d-6105-4e19-b60c-1ec1252cf560%20(3fc1081d-6105-4e19-b60c-1ec1252cf560)) /.AzState/Microsoft.Authorization_policyDefinitions-Deploy-Diagnostics-ActivityLog.parameters.json) | 診断アクティビティ ログを有効にし、Log Analytics に転送します。 |
| [`Deploy-Diag-LogAnalytics`](https://github.com/Azure/Enterprise-Scale/tree/main/azopsreference/3fc1081d-6105-4e19-b60c-1ec1252cf560%20(3fc1081d-6105-4e19-b60c-1ec1252cf560)) /.AzState/Microsoft.Authorization_policyDefinitions-Deploy-Log-Analytics.parameters.json) | |
| [`Deploy-VM-Monitoring`](https://github.com/Azure/Enterprise-Scale/tree/main/azopsreference/3fc1081d-6105-4e19-b60c-1ec1252cf560%20(3fc1081d-6105-4e19-b60c-1ec1252cf560)) /.AzState/Microsoft.Authorization_policyDefinitions-Deploy-Diagnostics-VM.parameters.json) | VM の監視が確実に有効であるようにします。 |

### <a name="platform-identity"></a>プラットフォーム ID

1. Azure Policy 経由で ID リソースを作成する場合は、次の表に記載されているポリシーを ID サブスクリプションに割り当てます。 こうすることで、指定されたパラメーターに基づいて次の一覧のリソースが作成されることが Azure Policy により保証されます。

2. Active Directory ドメイン コントローラーをデプロイします。

次の一覧は、エンタープライズ規模のデプロイ用の ID リソースを実装するときに使用できるポリシーを示しています。

| 名前                         | 説明                                                               |
|------------------------------|---------------------------------------------------------------------------|
| [`DataProtectionSecurityCenter`](https://github.com/Azure/Enterprise-Scale/tree/main/azopsreference/3fc1081d-6105-4e19-b60c-1ec1252cf560%20(3fc1081d-6105-4e19-b60c-1ec1252cf560)) /.AzState/) | Security Center によって自動的に作成されたデータ保護。 |
| [`Deploy-VNet-Identity`](https://github.com/Azure/Enterprise-Scale/tree/main/azopsreference/3fc1081d-6105-4e19-b60c-1ec1252cf560%20(3fc1081d-6105-4e19-b60c-1ec1252cf560)) /.AzState/Microsoft.Authorization_policyDefinitions-Deploy-vNet.parameters.json) | ホストへの ID サブスクリプション (DC など) に仮想ネットワークをデプロイします。 |

### <a name="platform-management-and-monitoring"></a>プラットフォームの管理と監視

1. 組織およびリソース中心のビューのためのポリシー コンプライアンスとセキュリティ ダッシュボードを作成します。

2. プラットフォーム シークレット (サービス プリンシパルと Automation アカウント) とキー ロールオーバーのワークフローを作成します。

3. Log Analytics 内のログの長期的なアーカイブと保持を設定します。

4. プラットフォーム シークレットを格納する Azure Key Vault を設定します。

5. Azure Policy を介してプラットフォーム管理リソースを作成する場合は、次の表に記載されているポリシーを管理サブスクリプションに割り当てます。 こうすることで、指定されたパラメーターに基づいて次の一覧のリソースが作成されることが Azure Policy により保証されます。

| 名前                   | 説明                                                                            |
|------------------------|----------------------------------------------------------------------------------------|
| [`Deploy-LA-Config`](https://github.com/Azure/Enterprise-Scale/tree/main/azopsreference/3fc1081d-6105-4e19-b60c-1ec1252cf560%20(3fc1081d-6105-4e19-b60c-1ec1252cf560)) /.AzState/Microsoft.Authorization_policyDefinitions-Deploy-LA-Config.parameters.json) | Log Analytics ワークスペースの構成。 |
| [`Deploy-Log-Analytics`](https://github.com/Azure/Enterprise-Scale/tree/main/azopsreference/3fc1081d-6105-4e19-b60c-1ec1252cf560%20(3fc1081d-6105-4e19-b60c-1ec1252cf560)) /.AzState/Microsoft.Authorization_policyDefinitions-Deploy-Log-Analytics.parameters.json) | Log Analytics ワークスペースをデプロイします。 |

## <a name="file--new--region"></a>[ファイル] > [新規] > [リージョン]

1. Azure Policy を介してネットワーク リソースを作成する場合は、次の表に記載されているポリシーを接続サブスクリプションに割り当てます。 こうすることで、指定されたパラメーターに基づいて次の一覧のリソースが作成されることが Azure Policy により保証されます。

    - 接続サブスクリプションで、既存の Virtual WAN 内に新しい仮想ハブを作成します。
    - Azure Firewall を仮想ハブ内にデプロイして、仮想ハブをセキュリティで保護し、既存または新規のファイアウォール ポリシーを Azure Firewall にリンクします。
    - セキュリティで保護された仮想ハブに接続されているすべての仮想ネットワークが Azure Firewall で保護されていることを確保します。

2. ExpressRoute または VPN を使用して、仮想ハブをオンプレミスのネットワークに接続します。

3. NSG を使用して、仮想ハブ間の仮想ネットワーク トラフィックを保護します。

4. (省略可能) ExpressRoute プライベート ピアリングで暗号化を設定します。

| 名前                     | 説明                                                                            |
|--------------------------|----------------------------------------------------------------------------------------|
| [`Deploy-VHub`](https://github.com/Azure/Enterprise-Scale/tree/main/azopsreference/3fc1081d-6105-4e19-b60c-1ec1252cf560%20(3fc1081d-6105-4e19-b60c-1ec1252cf560)) /.AzState/Microsoft.Authorization_policyDefinitions-Deploy-vHUB.parameters.json) | このポリシーにより、仮想ハブ、Azure Firewall、ゲートウェイ (VPN または ExpressRoute) がデプロイされます。 また、Azure Firewall への接続された仮想ネットワークのデフォルト ルートも構成されます。 |

<!-- docutune:disable -->

## <a name="file--new--landing-zone-for-applications-and-workloads"></a>アプリケーションおよびワークロード用の [ファイル] > [新規] > [Landing zone]\(ランディング ゾーン\)

<!-- docutune:enable -->

1. サブスクリプションを作成し、`Landing Zones` 管理グループのスコープの下に移動します。

2. `Owner`、`Reader`、`Contributor` など、サブスクリプションの Azure AD グループを作成します。

3. 確立された Azure AD グループの Azure AD PIM のエンタイトルメントを作成します。
