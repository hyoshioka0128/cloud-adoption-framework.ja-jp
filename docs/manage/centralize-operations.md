---
title: 管理操作の一元化
titleSuffix: Microsoft Cloud Adoption Framework for Azure
description: 管理操作の一元化に関するガイダンス
author: JnHs
ms.author: jenhayes
ms.date: 09/27/2019
ms.topic: guide
ms.service: cloud-adoption-framework
ms.subservice: operate
ms.openlocfilehash: b8e4e37ba9a771937b11f08f1abae45de2f818ab
ms.sourcegitcommit: 1dccf1aed8e98aa0f58c4f86d90c65f5fa5ac84d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2019
ms.locfileid: "71806260"
---
# <a name="centralize-management-operations"></a>管理操作の一元化

ほとんどの組織では、すべてのユーザーに対して 1 つの Azure Active Directory (Azure AD) テナントを使用すると、管理操作が簡略化され、メンテナンス コストが削減されます。これは、すべての管理タスクは、指定されたユーザー、ユーザーグループ、またはそのテナント内のサービス プリンシパルによって行われるためです。 可能な場合は、組織で 1 つの Azure AD テナントを使用することをお勧めします。

ただし、複数の Azure AD テナントを管理する必要がある場合もあります。 たとえば、次のような理由で複数のテナントが必要になる場合があります。

- 完全に独立した子会社
- 複数の地域での個別の操作
- 法的要件とコンプライアンス要件
- 他の組織の買収 (場合によっては、長期的なテナント統合戦略が定義されるまでの一時的な状況)

マルチテナント アーキテクチャが必要な場合は、[Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/overview) 使用して、管理操作を一元化し、合理化することができます。 [Azure の委任されたリソース管理](https://docs.microsoft.com/azure/lighthouse/concepts/azure-delegated-resource-management)では、複数のテナントのサブスクリプションをオンボードすることができます。これにより、管理しているテナントの特定のユーザーは、[一元的でスケーラブルな方法で](https://docs.microsoft.com/azure/lighthouse/concepts/cross-tenant-management-experience)クロステナント管理機能を実行できます。

たとえば、組織に 1 つのテナント、"*テナント A*" があるとします。その後、組織は *Tenant B* と *Tenant C* の 2 つの追加テナントを取得します。これらを個別のテナントとして維持する必要があるビジネス上の理由があります。

組織では、すべてのテナントで同じポリシー定義、バックアップ方法、およびセキュリティ プロセスを使用する必要があります。 テナント A 内でこれらのタスクの実行を担当するユーザー (ユーザー グループとサービス プリンシパルを含む) が既に存在するため、テナント A 内の同じユーザーがこれらのタスクを実行できるように、テナント B とテナント C 内のすべてのサブスクリプションをオンボードできます。 テナント A は、テナント B とテナント C の管理テナントになります。

![テナント A のユーザーによるテナント B とテナント C のリソースの管理](../_images/manage/enterprise-azure-lighthouse.jpg)

詳しくは、「[エンタープライズ シナリオにおける Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/concepts/enterprise)」をご覧ください。
