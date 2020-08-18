---
title: クラウド導入フレームワークのエンタープライズ規模の設計ガイドライン
description: Azure 用の Microsoft クラウド導入フレームワークにおけるエンタープライズ規模の設計のガイドラインについて説明します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 06/15/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.openlocfilehash: c3a093d92777f41783cd47478f2a72dc3fa0c2ad
ms.sourcegitcommit: d31a9043d1ae9283ed126bf118ca26d1d18d6948
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/10/2020
ms.locfileid: "88040883"
---
# <a name="cloud-adoption-framework-enterprise-scale-design-guidelines"></a>クラウド導入フレームワークのエンタープライズ規模の設計ガイドライン

この記事とそれに続く記事シリーズでは、エンタープライズ規模のアーキテクチャで、[Azure ランディング ゾーンの各設計領域](../landing-zone/design-areas.md)にこだわりの配置を確保する方法について概要を説明します。 このシリーズでは、段階的な設計ガイドラインのセットを提供します。これに従うことで、エンタープライズ規模のソリューションに組み込まれている設計原則を実装することができます。

エンタープライズ規模のアーキテクチャの中核には、相互に関連し、依存し合う設計上の判断を伴う基本的な設計トピックで構成される、重要な設計パスがあります。 このリポジトリでは、このようなアーキテクチャ上で重要な技術領域全体に設計ガイダンスを提供し、エンタープライズ規模のアーキテクチャを定義するために下す必要がある重要な設計上の判断を支援します。 検討対象の各領域について、提供されている考慮事項と推奨事項を確認し、それらを使用して各領域内の設計を構築および推進します。

たとえば、資産に必要なサブスクリプションの数を確認したいとします。 [サブスクリプションの組織とガバナンス](./management-group-and-subscription-organization.md#subscription-organization-and-governance)を確認し、概説されている推奨事項を使用してサブスクリプションの決定を行うことができます。

## <a name="critical-design-areas"></a>重要な設計領域

次の 8 つの重要な設計領域は、実際の要件を Azure の構造と機能に置き換えるうえで役立ちます。 これらの設計領域により、オンプレミスとクラウドの設計インフラストラクチャ間の不一致に対処できるようになります (通常、この不一致によって、エンタープライズ規模の定義と Azure 導入の間に不一致や摩擦が生じます)。

このような重要な領域内で行われた判断の影響は、エンタープライズ規模のアーキテクチャ全体に影響し、他の判断にも影響します。 後になって関連する領域でトレードオフを生み出す可能性のある、包括的な意思決定の結果について理解を深めるために、これら 8 つの領域を十分に理解してください。

- [Enterprise Agreement (EA) 加入契約と Azure Active Directory テナント](./enterprise-enrollment-and-azure-ad-tenants.md)
- [ID 管理とアクセス管理](./identity-and-access-management.md)
- [管理グループとサブスクリプションの組織](./management-group-and-subscription-organization.md)
- [ネットワーク トポロジと接続](./network-topology-and-connectivity.md)
- [管理と監視](./management-and-monitoring.md)
- [事業継続とディザスター リカバリー](./business-continuity-and-disaster-recovery.md)
- [セキュリティ、ガバナンス、およびコンプライアンス](./security-governance-and-compliance.md)
- [プラットフォームの自動化と DevOps](./platform-automation-and-devops.md)
