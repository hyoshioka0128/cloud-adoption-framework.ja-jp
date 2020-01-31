---
title: 管理操作の一元化
description: 管理操作の一元化に関するガイダンス
author: JnHs
ms.author: jenhayes
ms.date: 09/27/2019
ms.topic: guide
ms.service: cloud-adoption-framework
ms.subservice: operate
ms.openlocfilehash: a02e5804bd2ea18bd385634cbab57b21c8427bba
ms.sourcegitcommit: 2362fb3154a91aa421224ffdb2cc632d982b129b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2020
ms.locfileid: "76807888"
---
# <a name="centralize-management-operations"></a>管理操作の一元化

ほとんどの組織では、すべてのユーザーに単一の Azure Active Directory (Azure AD) テナントを使用すると、管理操作が簡素化され、メンテナンス コストが削減されます。 これは、すべての管理タスクを、そのテナント内の指名されたユーザー、ユーザー グループ、またはサービス プリンシパルによって実行できるからです。 

可能な場合は、組織で 1 つの Azure AD テナントのみを使用することをお勧めします。 ただし、次のような理由により、組織で複数の Azure AD テナントを管理することが必要な場合もあります。

- 完全に独立した子会社である。
- 複数の地域で個別に事業を展開している。
- 特定の法的要件またはコンプライアンス要件が該当する。
- 他の組織の買収 (場合によっては、長期的なテナント統合戦略が定義されるまでの一時的な状況) がある。

マルチテナント アーキテクチャが必要な場合は、[Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/overview) を使用して、管理操作を一元化し、合理化することができます。 複数のテナントからのサブスクリプションを [Azure 委任リソース管理](https://docs.microsoft.com/azure/lighthouse/concepts/azure-delegated-resource-management)のためにオンボードできます。 このオプションにより、管理側テナント内の指定されたユーザーが、一元化されたスケーラブルな方法で[テナント間管理機能](https://docs.microsoft.com/azure/lighthouse/concepts/cross-tenant-management-experience)を実行できます。

たとえば、組織に 1 つのテナント、"*テナント A*" があるとします。その後、組織は*テナント B* と*テナント C* の 2 つの追加テナントを取得します。これらを個別のテナントとして維持する必要があるビジネス上の理由があります。

組織では、すべてのテナントで同じポリシー定義、バックアップ方法、およびセキュリティ プロセスを使用する必要があります。 テナント A 内でこれらのタスクの実行を担当するユーザー (ユーザー グループとサービス プリンシパルを含む) が既に存在するため、テナント A 内の同じユーザーがこれらのタスクを実行できるように、テナント B とテナント C 内のすべてのサブスクリプションをオンボードできます。 テナント A は、テナント B とテナント C の管理テナントになります。

![テナント A のユーザーによるテナント B とテナント C のリソースの管理](../_images/manage/enterprise-azure-lighthouse.jpg)

詳しくは、「[エンタープライズ シナリオにおける Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/concepts/enterprise)」をご覧ください。
