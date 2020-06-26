---
title: 設計ガイドライン
description: 設計ガイドライン。
author: BrianBlanchard
ms.author: brblanch
ms.date: 06/15/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.openlocfilehash: c1f23841cbde493b2c8279a1870ebbe7b061c721
ms.sourcegitcommit: 9b183014c7a6faffac0a1b48fdd321d9bbe640be
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/19/2020
ms.locfileid: "85076943"
---
# <a name="design-guidelines"></a>設計ガイドライン

この記事とそれに続く記事シリーズでは、エンタープライズ規模のアーキテクチャで、Microsoft [Azure ランディング ゾーンの各設計領域](../landing-zone/design-areas.md)にこだわりの配置を確保する方法について概要を説明します。 この記事シリーズでは、一連の設計ガイドラインを段階的に作成します。これに従うと、エンタープライズ規模のソリューションに組み込まれた設計原則を実装することができます。

エンタープライズ規模のアーキテクチャの中核には、相互に関連し、依存し合う設計上の判断を伴う基本的な設計トピックで構成される、重要な設計パスがあります。 このリポジトリでは、このようなアーキテクチャ上で重要な技術領域全体に設計ガイダンスを提供し、エンタープライズ規模のアーキテクチャを定義するために下す必要がある重要な設計上の判断を支援します。 読者は、検討している各領域について、提供された考慮事項と推奨事項を確認し、それらを使用して各領域内の設計を構築および推進する必要があります。

たとえば、お客様から、資産に必要なサブスクリプション数を尋ねられることがよくあります。 この場合、読者は、[サブスクリプションの組織とガバナンス](./management-group-and-subscription-organization.md#subscription-organization-and-governance)を確認し、概要に記載されている推奨事項を使用してサブスクリプションの判断を推進するようにします。

## <a name="critical-design-areas"></a>重要な設計領域

次の 8 つの重要な設計領域は、お客様の要件を Microsoft Azure の構造と機能に変換し、オンプレミスとクラウドの設計インフラストラクチャ間の不一致 (これにより、通常、エンタープライズ規模の定義と Azure の導入の間に不一致と摩擦が生じます) に対処するために役立ちます。

このような重要な領域内で行われた判断の影響は、エンタープライズ規模のアーキテクチャ全体に影響し、他の判断にも影響します。 読者は、関連する領域内でトレードオフを引き起こす可能性のある包括的な判断の結果をより理解できるように、以下の 8 つの領域を理解しておく必要があります。

1. [エンタープライズ登録と Azure Active Directory テナント](./enterprise-enrollment-and-azure-ad-tenants.md)
2. [ID 管理とアクセス管理](./identity-and-access-management.md)
3. [管理グループとサブスクリプションの組織](./management-group-and-subscription-organization.md)
4. [ネットワーク トポロジと接続](./network-topology-and-connectivity.md)
5. [管理と監視](./management-and-monitoring.md)
6. [事業継続とディザスター リカバリー](./business-continuity-and-disaster-recovery.md)
7. [セキュリティ、ガバナンス、およびコンプライアンス](./security-governance-and-compliance.md)
8. [プラットフォームの自動化と DevOps](./platform-automation-and-devops.md)
