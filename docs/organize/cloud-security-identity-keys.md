---
title: クラウドでの ID とキーの管理の機能
description: クラウドでの ID とキーの管理の機能について説明します。
author: JanetCThomas
ms.author: janet
ms.service: cloud-adoption-framework
ms.subservice: organize
ms.topic: conceptual
ms.date: 05/15/2020
ms.openlocfilehash: 7942cc63341b3f15f770d6f23dca3e56bc606b65
ms.sourcegitcommit: 011525720bd9e2d9bcf03a76f371c4fc68092c45
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88574739"
---
# <a name="function-of-identity-and-key-management-in-the-cloud"></a>クラウドでの ID とキーの管理の機能

ID 管理を担当するセキュリティ チームの主な目的は、ユーザー、サービス、デバイス、アプリケーションの認証と承認を行うことです。 キーと証明書の管理では、暗号化操作のためのキー マテリアルに対するセキュリティで保護された配布とアクセスが提供されます (多くの場合、ID 管理と同様の結果がサポートされます)。

## <a name="modernization"></a>最新化

データ ID とキー管理の近代化は、次のように形成されています。

- ID 管理とキーおよび認証管理の分野は、どちらもセキュリティで保護された通信を実現するための認証と承認を保証するものであるため、密接に関連するようになっています。
- ID コントロールは、クラウド アプリケーションの主要なセキュリティ境界として新たに登場しています
- クラウド サービスのキー ベースの認証は、キーを格納し、キーへのアクセスを安全に提供するのが困難であるため、ID 管理に置き換えられています。
- 単一の ID、シングル サインオン (SSO)、ネイティブ アプリケーションの統合など、オンプレミスの ID アーキテクチャから得られた肯定的な教訓を引き継ぐことの決定的な重要性。
- オンプレミスのアーキテクチャを必要以上に複雑にし、サポートを困難にし、攻撃を容易にすることが多かった、オンプレミスのアーキテクチャの一般的なミスを避けることの決定的な重要性。 これには以下が含まれます。
  - 無秩序に拡大するグループと組織単位 (OU)。
  - 無秩序に拡大するサードパーティのディレクトリと ID 管理システムのセット。
  - アプリケーション ID 戦略の明確な標準化と所有権の欠如。
- 資格情報の盗難攻撃には大きな影響があり、脅威が軽減される高い可能性があります。
- サービス アカウントとアプリケーション アカウントは、上位の課題になりますが、解決が容易になります。 ID チームは、[Azure AD マネージド ID](/azure/active-directory/managed-identities-azure-resources/overview) のような、この問題の解決を開始するクラウド機能を積極的に採用する必要があります。

## <a name="team-composition-and-key-relationships"></a>チームの構成と重要な関係

ID およびキー管理チームは、次の役割と強力な関係を構築する必要があります。

- IT アーキテクチャと運用
- セキュリティ アーキテクチャと運用
- 開発チーム
- データ セキュリティ チーム
- プライバシー チーム
- 法律チーム
- コンプライアンスおよびリスク管理チーム

## <a name="next-steps"></a>次のステップ

[インフラストラクチャとエンドポイント セキュリティ](./cloud-security-infrastructure-endpoint.md)の機能を確認します
