---
title: Private Link と DNS の大規模な統合
description: Private Link と DNS の大規模な統合
author: JefferyMitchell
ms.author: brblanch
ms.date: 02/18/2021
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.custom: think-tank
ms.openlocfilehash: 2d7afeca3547f5880e743b82c221f1ab58fdab11
ms.sourcegitcommit: b8f8b7631aabaab28e9705934bf67dad15e3a179
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101800926"
---
# <a name="private-link-and-dns-integration-at-scale"></a>Private Link と DNS の大規模な統合

> この記事では、ハブ アンド スポーク ネットワーク アーキテクチャで、PaaS サービス用の Azure Private Link を Azure プライベート DNS ゾーンと統合する方法について説明します。

## <a name="introduction"></a>はじめに

多くのお客様は、ハブ アンド スポーク ネットワーク アーキテクチャを使用して、Azure でネットワーク インフラストラクチャを構築しています。そこでは、

- ネットワーク共有サービス (ネットワーク仮想アプライアンス、ExpressRoute/VPN ゲートウェイ、DNS サーバーなど) が **ハブ** 仮想ネットワーク (VNet) にデプロイされます
- **スポーク** Vnet では、VNet ピアリング経由でこれらの共有サービスが使用されます。

ハブ アンド スポーク ネットワーク アーキテクチャでは、通常、アプリケーション所有者に Azure サブスクリプションが提供され、これには、*ハブ* VNet に接続された VNet (*スポーク*) が含まれます。 このアーキテクチャでは、仮想マシンをデプロイし、ExpressRoute または VPN 経由で他の VNet またはオンプレミス ネットワークへのプライベート接続を確立することができます。

インターネット送信接続は、Azure Firewall などの中央のネットワーク仮想アプライアンス (NVA) を介して提供されます。

多くのアプリケーション チームは、Azure IaaS および PaaS リソースの組み合わせを使用してソリューションを構築しています。 一部の Azure PaaS サービス (SQL Managed Instance など) は、お客様の VNet にデプロイできます。 その結果、トラフィックは Azure ネットワークでプライベートのままになり、オンプレミスから完全にルーティング可能になります。

ただし、一部の Azure PaaS サービス (Azure Storage や Azure Cosmos DB など) は、お客様の VNet にデプロイできず、パブリックエンド ポイント経由でアクセスできます。 場合によっては、企業トラフィックで、パブリック エンドポイント経由の企業リソース (SQL データベースなど) のデプロイやアクセスが許可されていないために、これによって顧客のセキュリティ ポリシーとの競合が発生することがあります。

[Azure Private Link][link-1] では、プライベート エンドポイント経由の [Azure サービスの一覧][link-2]へのアクセスが許可されていますが、それには、それらのプライベート エンドポイント レコードが、対応する[プライベート DNS ゾーン][link-3]に登録されている必要があります。

この記事では、プライベート エンドポイント経由でのみアクセスできる Azure PaaS サービスをサブスクリプションにデプロイする方法について説明します。

さらにこの記事では、アプリケーション チームが Azure プライベート DNS 経由で、サービスが自動的にプライベート DNS ゾーンに統合されるようにする方法についても説明します。 DNS でレコードを手動で作成したり削除したりする必要がなくなります。

## <a name="private-link-and-dns-integration-in-hub-and-spoke-network-architectures"></a>ハブ アンド スポーク ネットワーク アーキテクチャでの Private Link と DNS の統合

プライベート DNS ゾーンは通常、ハブ VNet がデプロイされている同じ Azure サブスクリプションで、一元的にホストされます。 この中央ホスティング プラクティスは、[クロスプレミス DNS 名前解決][link-4]や、Active Directory などの中央の DNS 解決に対するその他のニーズによって推進されます。 ほとんどの場合、これらのゾーンで DNS レコードを管理するアクセス許可はネットワークまたは ID 管理者のみに与えられています。

アプリケーション チームは、独自のサブスクリプションに Azure リソースを作成するアクセス許可を持っています。 チームには、プライベート DNS ゾーンの DNS レコードの管理を含む、中央ネットワーク接続サブスクリプションでのアクセス許可が与えられていません。 このアクセス制限は、プライベート エンドポイントによる Azure PaaS サービスのデプロイ時に、[必要な DNS レコードを作成][link-4]できないことを意味します。

次の図は、中央 DNS 解決によるエンタープライズ環境の一般的なアーキテクチャの概要を示しており、そこでは Private Link リソースの名前解決が、Azure プライベート DNS 経由で行われます。

![image-1][image-1]

前の図では、次の点に注目することが重要です。

- オンプレミスの DNS サーバーには、プライベート エンドポイントの[パブリック DNS ゾーン フォワーダー][link-3]ごとに、条件付きフォワーダーが構成されており、ハブ VNet でホストされている DNS フォワーダー (`10.100.2.4` と `10.100.2.5`) を指しています。
- すべての Azure Vnet には、DNS フォワーダー (`10.100.2.4` と `10.100.2.5`) がプライマリおよびセカンダリ DNS サーバーとして構成されています。

アプリケーション チームがサブスクリプションに必要な Azure PaaS リソースを自由に作成できるようにするために、次の 2 つの条件が満たされている必要があります。

- 中央ネットワークまたは中央プラットフォーム チームは、アプリケーション チームだけがプライベート エンドポイント経由で Azure PaaS サービスをデプロイし、アクセスできるようにする必要があります。
- 中央ネットワークまたは中央プラットフォーム チームは、プライベート エンドポイントが作成されるたびに、対応するレコードが、作成されたサービスと一致する中央管理プライベート DNS ゾーンに自動的に作成されるようにする必要があります。
  - DNS レコードは、プライベート エンドポイントのライフサイクルに従い、プライベート エンドポイントが削除されたら、DNS レコードが自動的に削除される必要があります。

以下のセクションでは、アプリケーション チームが [Azure Policy][link-10] を使用して、これらの条件を有効にできる方法について説明します。 下の例では、アプリケーション チームがデプロイする必要のある Azure サービスとして Azure Storage を使用しますが、Private Link を[サポートする][link-2]ほとんどの Azure サービスに同じ原則を適用できます。

## <a name="configuration-required-by-platform-team"></a>プラットフォーム チームが必要とする構成

### <a name="create-private-dns-zones"></a>プライベート DNS ゾーンを作成する

[ドキュメント][link-4]に従って、サポートされている Private Link サービスの中央接続サブスクリプションにプライベート DNS ゾーンを作成します。

この例では、**BLOB によるストレージ アカウント** を例として使用するため、接続サブスクリプションに `privatelink.blob.core.windows.net` プライベート DNS ゾーンを作成すると言い換えられます。

![image-2][image-2]

### <a name="policy-definitions"></a>ポリシーの定義

プライベート DNS ゾーンに加えて、[カスタム Azure Policy 定義のセットを作成][link-5]して、プライベート エンドポイントの使用を強制し、作成した DNS ゾーンの DNS レコードの作成を自動化する必要もあります。

1. PaaS サービス ポリシーのパブリック エンドポイントを **拒否** する

   このポリシーにより、ユーザーはパブリック エンドポイントで Azure PaaS サービスを作成できなくなり、リソースの作成時にプライベート エンドポイントが選択されていない場合は、エラー メッセージが表示されます。

   ![image-3][image-3]

   ![image-4][image-4]

   ![image-5][image-5]

   正確なポリシー規則は、PaaS サービス間で異なる場合があります。 Azure Storage アカウントについては、パブリック ネットワークからの要求を許可するかどうかを定義する **networkAcls.defaultAction** プロパティに注目します。 この例では、プロパティ **networkAcls.defaultAction** が `Deny` でない場合、**Microsoft.Storage/storageAccounts** リソースの種類の作成を拒否する条件を設定します。 このポリシー定義を次に示します。

   ```json
   {
     "mode": "All",
     "policyRule": {
       "if": {
         "allOf": [
           {
             "field": "type",
             "equals": "Microsoft.Storage/storageAccounts"
           },
           {
             "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
             "notequals": "Deny"
           }
         ]
       },
       "then": {
         "effect": "Deny"
       }
     }
   }
   ```

2. `privatelink` プレフィックス ポリシーでプライベート DNS ゾーンの作成を **拒否** する

   プラットフォーム チームによって管理されるサブスクリプションでホストされている、条件付きフォワーダーとプライベート DNS ゾーンを含む中央管理 DNS アーキテクチャを使用しているため、アプリケーション チーム所有者が独自の Private Link プライベート DNS ゾーンを作成し、サービスをサブスクリプションにリンクさせないようにする必要があります。

   これを実現するには、アプリケーション チームがプライベート エンドポイントを作成するときに、Azure portal の使用時に、`Integrate with private DNS zone` オプションを `No` に設定する必要があることを確認する必要があります。

   ![image-6][image-6]

   `Yes` が選択されていると、Azure Policy によってプライベート エンドポイントの作成が妨げられます。 この例のポリシー定義では、ゾーンに `privatelink` プレフィックスがある場合、**Microsoft. Network/privateDnsZones** リソースの種類の作成を拒否します。 このポリシー定義について以下に説明します。

   ```json
   {
     "description": "This policy restricts creation of private DNS zones with the `privatelink` prefix",
     "displayName": "Deny-PrivateDNSZone-PrivateLink",
     "mode": "All",
     "parameters": null,
     "policyRule": {
       "if": {
         "allOf": [
           {
             "field": "type",
             "equals": "Microsoft.Network/privateDnsZones"
           },
           {
             "field": "name",
             "like": "privatelink*"
           }
         ]
       },
       "then": {
         "effect": "Deny"
       }
     }
   }
   ```

3. 中央のプライベート DNS ゾーンに必要な DNS レコードを自動的に作成する **DeployIfNotExists** ポリシー

   このポリシーは、プライベート エンドポイント リソースがサービス固有の `groupId` で作成された場合にトリガーされます。 `groupId` は、このプライベート エンドポイントの接続先となるリモート リソース (サービス) から取得したグループの ID です。 次に、プライベート エンドポイント内の [`privateDNSZoneGroup`][link-6] のデプロイをトリガーします。これは、プライベート エンドポイントをプライベート DNS ゾーンに関連付けるために使用されます。 この例では、Azure Storage BLOB の `groupId` は `blob` です (他の Azure サービスの `groupId` は、[この][link-4]記事の「**Subresource** 列」を参照してください)。 ポリシーによって、作成されたプライベート エンドポイントに `groupId` が検出されると、プライベート エンドポイント内に [`privateDNSZoneGroup`][link-6] がデプロイされ、パラメーターとして指定されたプライベート DNS ゾーン リソース ID にリンクされます。 この例では、プライベート DNS ゾーン リソース ID は次のようになります。

   `/subscriptions/<subscription-id>/resourceGroups/<resourceGroupName>/providers/Microsoft.Network/privateDnsZones/privatelink.blob.core.windows.net`

   このポリシー定義を次に示します。

   ```json
   {
     "mode": "Indexed",
     "policyRule": {
       "if": {
         "allOf": [
           {
             "field": "type",
             "equals": "Microsoft.Network/privateEndpoints"
           },
           {
             "count": {
               "field": "Microsoft.Network/privateEndpoints/privateLinkServiceConnections[*].groupIds[*]",
               "where": {
                 "field": "Microsoft.Network/privateEndpoints/privateLinkServiceConnections[*].groupIds[*]",
                 "equals": "blob"
               }
             },
             "greaterOrEquals": 1
           }
         ]
       },
       "then": {
         "effect": "deployIfNotExists",
         "details": {
           "type": "Microsoft.Network/privateEndpoints/privateDnsZoneGroups",
           "roleDefinitionIds": [
             "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
           ],
           "existenceCondition": {
             "allOf": [
               {
                 "field": "Microsoft.Network/privateEndpoints/privateDnsZoneGroups/privateDnsZoneConfigs[*].privateDnsZoneId",
                 "equals": "[parameters('privateDnsZoneId')]"
               }
             ]
           },
           "deployment": {
             "properties": {
               "mode": "incremental",
               "template": {
                 "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
                 "contentVersion": "1.0.0.0",
                 "parameters": {
                   "privateDnsZoneId": {
                     "type": "string"
                   },
                   "privateEndpointName": {
                     "type": "string"
                   },
                   "location": {
                     "type": "string"
                   }
                 },
                 "resources": [
                   {
                     "name": "[concat(parameters('privateEndpointName'), '/deployedByPolicy')]",
                     "type": "Microsoft.Network/privateEndpoints/privateDnsZoneGroups",
                     "apiVersion": "2020-03-01",
                     "location": "[parameters('location')]",
                     "properties": {
                       "privateDnsZoneConfigs": [
                         {
                           "name": "storageBlob-privateDnsZone",
                           "properties": {
                             "privateDnsZoneId": "[parameters('privateDnsZoneId')]"
                           }
                         }
                       ]
                     }
                   }
                 ]
               },
               "parameters": {
                 "privateDnsZoneId": {
                   "value": "[parameters('privateDnsZoneId')]"
                 },
                 "privateEndpointName": {
                   "value": "[field('name')]"
                 },
                 "location": {
                   "value": "[field('location')]"
                 }
               }
             }
           }
         }
       }
     },
     "parameters": {
       "privateDnsZoneId": {
         "type": "String",
         "metadata": {
           "displayName": "privateDnsZoneId",
           "strongType": "Microsoft.Network/privateDnsZones"
         }
       }
     }
   }
   ```

### <a name="policy-assignments"></a>ポリシーの割り当て

ポリシー定義がデプロイされたら、管理グループ階層内の目的のスコープで[ポリシーを割り当て][link-7]ます。 アプリケーション チームが、プライベート エンドポイント アクセスによって排他的に PaaS サービスをデプロイするために使用する Azure サブスクリプションを、ポリシー割り当てのターゲットにするようにします。

> [!IMPORTANT]
> プライベート DNS ゾーンにプライベート エンドポイント DNS レコードを作成して管理する責任がある、[`DeployIfNotExists` ポリシー割り当てによって作成されるマネージド ID][link-9] に対して、プライベート DNS ゾーンがホストされているサブスクリプションまたはリソース グループの[プライベート DNS ゾーン共同作成者ロール][link-8]ロールを割り当ててください。 これは、プライベート エンドポイントがアプリケーション所有者の Azure サブスクリプションにあり、プライベート DNS ゾーンが別のサブスクリプション (中央の接続サブスクリプションなど) にあるためです。

プラットフォーム チームがこの構成を完了すると、アプリケーション チームの Azure サブスクリプションで、プライベート エンドポイント アクセスによって排他的に Azure PaaS サービスを作成する準備ができ、プライベート エンドポイントの DNS レコードが、対応するプライベート DNS ゾーンに自動的に登録 (およびプライベート エンドポイントが削除されたら削除) されます。

## <a name="application-owner-experience"></a>アプリケーション所有者のエクスペリエンス

プラットフォーム チームが、前のセクションで説明したプラットフォーム インフラストラクチャ コンポーネント (プライベート DNS ゾーンとポリシー) をデプロイしたら、アプリケーション所有者が Azure PaaS サービスを Azure サブスクリプションにデプロイしようとする際に、アプリケーション所有者には次の Azure portal エクスペリエンスが提供されます。これは、サブスクリプションが Azure ポリシーによって管理されているため、Azure portal 経由または PowerShell や CLI などの他のクライアント経由でアクティビティを実行する場合も同じです。

1. Azure Portal を使用してストレージ アカウントを作成します。 [基本] タブで、名前と必要な設定を指定し、 **[次へ]** をクリックします。

   ![image-7][image-7]

2. [ネットワーク] セクションで、 **[プライベート エンドポイント]** が選択されていることを確認します。 **[プライベート エンドポイント]** 以外のオプションが選択されている場合、パブリックエンド ポイントが有効にされていると、ポリシーによってこのサービスの作成が妨げられるため、Azure portal では、デプロイ ウィザードの **[確認と作成]** セクションでのストレージ アカウントの作成が許可されません。

   ![image-8][image-8]

3. この画面でプライベート エンドポイントを作成することも、ストレージ アカウントが作成された後に行うこともできます。 この演習では、ストレージ アカウントの作成後にプライベート エンドポイントを作成します。 **[確認と作成]** をクリックし、ストレージ アカウントの作成を完了します。

4. ストレージ アカウントが作成されたら、Azure portal からプライベート エンドポイントを作成します

   ![image-9][image-9]

5. **[リソース]** セクションで、前の手順で作成したストレージ アカウントを見つけ、ターゲット サブリソースに **[BLOB]** を選択して、 **[次へ]** を選択します。

   ![image-10][image-10]

6. **[構成]** セクションで、VNet とサブネットを選択したら、 **[プライベート DNS ゾーンと統合する]** が **[いいえ]** に設定されていることを確認します。 それ以外の場合、Azure Policy により `privatelink` プレフィックスが付いたプライベート DNS ゾーンの作成が許可されないため、Azure portal でプライベート エンドポイントの作成が妨げられます。

   ![image-11][image-11]

7. **[確認と作成]** を選択し、 **[作成]** を選択して、プライベート エンドポイントをデプロイします。

8. 数分後、`DeployIfNotExists` ポリシーがトリガーされ、それ以降の `dnsZoneGroup` デプロイでは、中央管理された DNS ゾーンに、プライベート エンドポイントの必要な DNS レコードが追加されます。

9. プライベート エンドポイントが作成されたら、それを選択し、その FQDN とプライベート IP を確認します。

   ![image-12][image-12]

10. プライベート エンドポイントが作成されたリソース グループのアクティビティ ログを確認するか、プライベート エンドポイント自体のアクティビティ ログを確認することができます。 数分後に `DeployIfNotExist` ポリシー アクションが実行され、それによりプライベートエンド ポイントに DNS ゾーン グループが構成されることに気付きます。

    ![image-13][image-13]

11. 中央ネットワーク チームが `privatelink.blob.core.windows.net` プライベート DNS ゾーンに移動すると、作成したプライベートエンド ポイントに対して DNS レコードが作成されていること、および名前と IP アドレスの両方がプライベート エンドポイント内の値と一致していることを確認できます。

    ![image-14][image-14]

この時点で、DNS レコードがプライベート DNS ゾーンに自動的に記録されているため、アプリケーション チームは、ハブ アンド スポーク ネットワーク環境の任意の VNet から、およびオンプレミスから、プライベート エンドポイント経由でストレージアカウントを使用できます。

アプリケーション所有者がプライベート エンドポイントを削除すると、プライベート DNS ゾーンの対応するレコードが自動的に削除されます。

[link-1]: /azure/private-link/private-link-overview
[link-2]: /azure/private-link/private-link-overview#availability
[link-3]: /azure/private-link/private-endpoint-dns#azure-services-dns-zone-configuration
[link-4]: /azure/private-link/private-endpoint-dns
[link-5]: /azure/governance/policy/tutorials/create-custom-policy-definition
[link-6]: /azure/templates/microsoft.network/2020-05-01/privateendpoints/privatednszonegroups
[link-7]: /azure/governance/policy/assign-policy-portal
[link-8]: /azure/dns/dns-protect-private-zones-recordsets
[link-9]: /azure/governance/policy/how-to/remediate-resources
[link-10]: /azure/governance/policy/overview
[image-1]: ./media/private-link-example-central-dns.png
[image-2]: ./media/private-link-storage-with-blob.jpg
[image-3]: ./media/private-link-storage-policy-step-1.jpg
[image-4]: ./media/private-link-storage-policy-step-2.jpg
[image-5]: ./media/private-link-storage-policy-step-3.jpg
[image-6]: ./media/private-link-private-dns-set-no.jpg
[image-7]: ./media/private-link-create-storage.jpg
[image-8]: ./media/private-link-network-private.jpg
[image-9]: ./media/private-link-create-private.jpg
[image-10]: ./media/private-link-target-blob.jpg
[image-11]: ./media/private-link-integrate-with-private.jpg
[image-12]: ./media/private-link-fqdn-private-ip.jpg
[image-13]: ./media/private-link-activity-log.jpg
[image-14]: ./media/private-link-check-private-dns.jpg
