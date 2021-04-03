---
title: SAP on Azure 構築セットのためのエンタープライズ規模の ID およびアクセス管理
description: Microsoft Azure における SAP デプロイでの ID およびアクセス管理に関連する設計上の考慮事項と推奨事項について説明します。
author: JefferyMitchell
ms.author: brblanch
ms.date: 02/12/2021
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.custom: think-tank, e2e-sap
ms.openlocfilehash: 7738da364848201cd699f754ecbabc5355b1ec9b
ms.sourcegitcommit: 5716a8165934bd69d02d9d3641785039196aee3a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "105734248"
---
<!-- docutune:casing LMS -->

# <a name="enterprise-scale-identity-and-access-management-for-sap-on-azure-construction-set"></a>SAP on Azure 構築セットのためのエンタープライズ規模の ID およびアクセス管理

この記事では、Microsoft Azure における SAP デプロイでの ID およびアクセス管理に関連する設計上の考慮事項と推奨事項について説明します。

**設計上の考慮事項:**

- Azure リソース管理の境界と、インフラストラクチャ チームと SAP Basis チームの間の SAP Basis 管理の境界を決定します。 SAP の非運用環境で、管理者特権での Azure リソース管理アクセスを SAP Basis チームに提供することを検討してください。 たとえば、 [仮想マシンの共同作成者](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) ロールを付与します。 また、運用環境で部分的な仮想マシン共同作成者などの部分的に管理者アクセス権を付与することもできます。 どちらのオプションでも、職務の分離と運用効率のバランスをとることができます。

- チームで実行するよう求める Azure の管理アクティビティと管理アクティビティをレビューします。 SAP on Azure ランドスケープを検討してください。 組織内での役割の最適な配分を決定します。

SAP on Azure の管理に関連する一般的な Azure 管理アクティビティを次に示します。

| Azure リソース | Azure リソース プロバイダー | Activities |
|---|---|---|
| 仮想マシン | Microsoft.Compute/virtualMachines | 開始、停止、再起動、割り当て解除、デプロイ、再デプロイ、変更、サイズ変更、拡張、可用性セット、近接配置グループ |
| 仮想マシン | Microsoft.Compute/disks | ディスクの読み取りと書き込み |
| ストレージ | Microsoft.Storage | ストレージアカウントの読み取り、変更 (ブート診断など) |
| ストレージ | Microsoft.NetApp | NetApp の容量プールとボリュームの読み取り、変更 |
| ストレージ | Microsoft.NetApp | ANF スナップショット |
| ストレージ | Microsoft.NetApp | ANF リージョン間レプリケーション |
| ネットワーク | Microsoft.Network/networkInterfaces | ネットワーク インターフェイスの読み取り、作成、変更 |
| ネットワーク | Microsoft.Network/loadBalancers | ロードバランサーの読み取り、作成、変更 |
| ネットワーク | Microsoft.Network/networkSecurityGroups | NSG の読み取り |
| ネットワーク | Microsoft.Network/azureFirewalls | ファイアウォールの読み取り |

- SAP Cloud Platform サービスを使用している場合は、プリンシパル伝達を使用して、SAP Cloud Platform アプリケーションからオンプレミスの SAP ランドスケープに ID を転送することを検討してください。 SAP Cloud Connector を使用します。

- ID およびアクセス管理プロセスを確認して再調整する機会を Azure に移行することを検討してください。 SAP ランドスケープのプロセスと、エンタープライズレベルのプロセスをレビューします。
  - SAP 非アクティブ ユーザー ロックアウト ポリシーをレビューします。
  - SAP ユーザーのパスワード ポリシーをレビューし、Azure Active Directory (Azure AD) にアラインします。
  - Leaver、Mover、および Starter (LMS) の手順を確認し、それらを Azure AD に揃えます。 SAP の人材管理 (HCM) を使用している場合は、SAP HCM が LMS プロセスを駆動している可能性があります。

- SAP SaaS アプリケーションでユーザーを設定および削除するには、Azure AD の自動ユーザー プロビジョニング機能を使用することを検討してください。 現在、SAP Analytics Cloud および SAP ID 認証サービスはこのシナリオをサポートしています。

- [Kerberos を使用した NFS クライアント暗号化](/azure/azure-netapp-files/configure-kerberos-encryption)で、Azure NetApp Files と Azure Virtual Machines 間のセキュリティで保護された Network File System (NFS) 通信。 Azure NetApp Files では、Azure AD 接続用に Active Directory Domain Services (AD DS) と Azure Active Directory Domain Services の両方がサポートされています。 [NFS v4.1 での Kerberos のパフォーマンスへの影響](/azure/azure-netapp-files/configure-kerberos-encryption#kerberos_performance)を検討します。

- 保護の品質 (QoP) などの適切な保護レベルを使用して、セキュリティで保護されたネットワーク通信 (SNC) を使用した SAP システム間のリモート関数呼び出し (RFC) 接続をセキュリティで保護します。 SNC の保護によって、パフォーマンスのオーバーヘッドが発生します。 同じ SAP システムのアプリケーション サーバー間での RFC 通信を保護するために、SAP では SNC ではなくネットワーク セキュリティを使用することをお勧めします。 これらの Azure サービスは、SAP システムへの SNC で保護された RFC 接続 (Azure Data Factory、オンプレミス データ ゲートウェイ (Logic Apps、Power BI) など) をサポートしています。

**設計上の推奨事項:**

- エンドユーザーが SAP アプリケーションに接続できるように、Azure AD または Active Directory フェデレーション サービス (AD FS) を使用してシングル サイン オン (SSO) を実装します。
  - [SAML を使用して、SAP NetWeaver](/azure/active-directory/saas-apps/sap-netweaver-tutorial) ベースの web アプリケーション (SAP Fiori、SAP WebGUI など) に SSO を実装します。
  - SAP NetWeaver SSO またはパートナー ソリューションを使用して、SAP GUI に SSO を実装できます。
  - SAML を使用する Azure AD で、SAP Analytics Cloud、SAP Cloud Platform、SAP Cloud Platform Identity Authentication service、SAP C4C などの[ SAP SaaS アプリケーションへの SSO ](/azure/active-directory/saas-apps/sap-customer-cloud-tutorial)を実装します。

- SAP Cloud Platform Identity Authentication サービスを使用している場合は、Azure AD と統合する必要があります。 この統合により、SAP IAS はプロキシ ID プロバイダーとして機能し、ユーザー管理を処理する Azure AD に ID 認証要求を転送できます。

- SAP SuccessFactors を使用している場合は、Azure AD の[自動化されたユーザー プロビジョニング](/azure/active-directory/saas-apps/sap-successfactors-inbound-provisioning-cloud-only-tutorial)機能を使用することをお勧めします。 この統合により、新しい従業員を SAP SuccessFactors に追加するときに、そのユーザーアカウントを Azure AD に自動的に作成できます。 必要に応じて、ユーザー アカウントは Microsoft 365 および Azure AD でサポートされているその他の SaaS アプリケーションで作成できます。 SAP SuccessFactors に電子メール アドレスの書き戻しを使用します。
