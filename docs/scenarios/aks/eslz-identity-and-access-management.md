---
title: AKS のためのエンタープライズ規模の ID およびアクセス管理
description: このエンタープライズ規模のシナリオで Azure Kubernetes Service のための ID およびアクセス管理をどのように向上させることができるかについて説明します。
author: TomGeske
ms.author: thomasge
ms.date: 03/01/2021
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.openlocfilehash: 12ca9ad27af872e64d4cef1a90a99c04f5c21c87
ms.sourcegitcommit: b8f8b7631aabaab28e9705934bf67dad15e3a179
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101801314"
---
# <a name="identity-and-access-management-for-azure-kubernetes-service-aks-enterprise-scale-scenario"></a>Azure Kubernetes Service (AKS) のための ID およびアクセス管理のエンタープライズ規模のシナリオ

組織または企業は、その要件を満たすための適切なセキュリティ設定を設計する必要があります。 ID およびアクセス管理には、クラスター ID、ワークロード ID、オペレーター アクセスなどの複数の側面があります。

## <a name="design-considerations"></a>設計上の考慮事項

- どのようなクラスター ID を使用するか (マネージド ID またはサービス プリンシパル) を決定します。
- クラスター アクセスを認証する方法 (証明書ベースまたは Azure Active Directory) を決定します。
- マルチテナント クラスターと、Kubernetes でロールベースのアクセス制御 (RBAC) を設定する方法を決定します。
  - 分離のための方法 (名前空間、ネットワーク ポリシー、コンピューティング (ノード プール)、またはクラスター) を決定します。
  - 分離のためのアプリケーション チームごとの Kubernetes RBAC ロールとコンピューティング割り当てについて決定します。
  - アプリケーション チームが自身のクラスターまたはその他のクラスター内の他のワークロードを読み取ることができるかどうかを決定します。
- [AKS ランディング ゾーン](../../ready/enterprise-scale/identity-and-access-management.md)用のカスタム Azure RBAC ロールについて決定します。
  - クラスター全体を管理またはトラブルシューティングするために、サイト信頼性エンジニアリング (SRE) ロールにどのようなアクセス許可が必要かを決定します。
  - SecOps にどのようなアクセス許可が必要かを決定します。
  - ランディング ゾーンの所有者にどのようなアクセス許可が必要かを決定します。
  - クラスターにデプロイするためにアプリケーション チームにどのようなアクセス許可が必要かを決定します。
- ワークロード ID (Azure AD ポッド ID) が必要かどうかを決定します。 これは、Azure Key Vault の統合や Azure Cosmos DB などの Azure サービスに必要なることがあります。

## <a name="design-recommendations"></a>設計の推奨事項

- **クラスター ID**
  - AKS クラスターに独自の[マネージド ID](https://aka.ms/aks/mi) を使用します。
  - クラスター マネージド ID に必要なアクセス許可の管理を簡略化するには、AKS ランディング ゾーン用のカスタム Azure RBAC ロールを定義します。
- **クラスターへのアクセス**
  - 構成およびシークレットへのアクセスを保護するために[特権を制限](/azure/aks/azure-ad-rbac)し、管理者特権の付与を最小限に抑えるには、Azure AD で Kubernetes RBAC を使用します。
  - 認証やオペレーターおよび開発者のアクセスに Azure AD を使用するには、[AKS マネージド Azure AD 統合](https://aka.ms/aks/managed-aad)を使用します。
- Kubernetes で、必要な RBAC ロールとロール バインディングを定義します。
  - サイト信頼性エンジニアリング (SRE)、SecOps、開発者のアクセスのために、Azure AD グループに対して [Kubernetes のロールとロール バインディング](/azure/aks/concepts-identity#kubernetes-role-based-access-control-kubernetes-rbac)を使用します。
  - SRE のフル アクセスは、必要に応じてジャストインタイムに付与する必要があります。 [Azure AD の Privileged Identity Management](/azure/active-directory/privileged-identity-management/pim-configure) と ID およびアクセス管理を[エンタープライズ規模](../../ready/enterprise-scale/identity-and-access-management.md)で使用します。
