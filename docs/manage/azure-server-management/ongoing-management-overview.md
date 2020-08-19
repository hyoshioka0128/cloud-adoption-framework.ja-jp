---
title: 継続中の管理とセキュリティ
description: Azure 向けのクラウド導入フレームワークを使用して、進行中の操作をサポートする操作とセキュリティ構成に重点を置く方法を学習します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 05/10/2019
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: operate
ms.openlocfilehash: adeda043b1631caf19975163c3433f7aa9bef12a
ms.sourcegitcommit: 011525720bd9e2d9bcf03a76f371c4fc68092c45
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88571883"
---
# <a name="phase-3-ongoing-management-and-security"></a>フェーズ 3: 継続中の管理とセキュリティ

Azure サーバー管理サービスをオンボードした後、進行中の操作をサポートする操作とセキュリティ構成に重点を置く必要があります。 ここではまず、Azure Security Center を確認して環境を保護します。 そのうえで、サーバーのコンプライアンスを維持するポリシーを構成し、一般的なタスクを自動化します。 このセクションは、次のトピックで構成されています。

- **[セキュリティに関する推奨事項に対処する。](#address-security-recommendations)** Azure Security Center から、ご利用の環境のセキュリティを強化するための推奨事項が得られます。 これらの推奨事項を実践すると、セキュリティ スコアにその効果が反映されます。
- **[ゲスト構成ポリシーを有効にします。](./guest-configuration-policy.md)** Azure Policy のゲスト構成機能を使用して、仮想マシンの設定を監査します。 たとえば、いずれかの証明書の期限が切れそうになっているかどうかを確認できます。
- **[重要な変更を追跡し、警告します。](./enable-tracking-alerting.md)** トラブルシューティング時に、考慮すべき最初の質問は、"何が変更されたか" です。 この記事では、変更を追跡し、重要なコンポーネントを予防的に監視するアラートを作成する方法を示します。
- **[更新スケジュールを作成します。](./update-schedules.md)** 更新プログラムのインストールをスケジュールして、すべてのサーバーに最新の更新プログラムが存在するようにします。
- **[Azure Policy の一般的な例。](./common-policies.md)** この記事では、一般的な管理ポリシーの例を示します。

## <a name="address-security-recommendations"></a>セキュリティに関する推奨事項に対処する

Azure Security Center は、環境のセキュリティを管理する一元的な場所です。 全体的な評価と実践目標となる推奨事項が表示されます。

このサービスによって提供される推奨事項を確認し、実践することをお勧めします。 Azure Security Center のその他のメリットについては、「[Azure Security Center の推奨事項に従う](/azure/migrate/migrate-best-practices-security-management#best-practice-follow-azure-security-center-recommendations)」を参照してください。

## <a name="next-steps"></a>次のステップ

[Azure Policy のゲスト構成機能を有効にする](./guest-configuration-policy.md)方法を確認します。

> [!div class="nextstepaction"]
> [ゲスト構成ポリシー](./guest-configuration-policy.md)
