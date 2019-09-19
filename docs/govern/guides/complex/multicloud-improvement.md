---
title: '複雑な企業向けのガバナンス ガイド: マルチクラウドの改善'
titleSuffix: Microsoft Cloud Adoption Framework for Azure
description: 大企業ガイド:マルチクラウドの改善
author: BrianBlanchard
ms.author: brblanch
ms.date: 02/11/2019
ms.topic: guide
ms.service: cloud-adoption-framework
ms.subservice: govern
ms.custom: governance
ms.openlocfilehash: 6f015fcc7a0cb4000502d90ff971341fd6d26ca5
ms.sourcegitcommit: 443c28f3afeedfbfe8b9980875a54afdbebd83a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/16/2019
ms.locfileid: "71031677"
---
# <a name="large-enterprise-guide-multicloud-improvement"></a>大企業ガイド:マルチクラウドの改善

## <a name="advancing-the-narrative"></a>物語を進める

Microsoft では、お客様が特定の目的で複数のクラウドを採用していることを認識しています。 このガイドの架空の会社も例外ではありません。 Azure の導入体験と並行して、ビジネスの成功は、小規模ながらも補完的なビジネスの取得へとつながりました。 そのビジネスは、別のクラウド プロバイダーですべての IT 操作を実行しています。

この記事では、新しい組織を統合するときに物事がどのように変化するかについて説明します。 この物語では、この会社はこのガバナンス ガイドで示されているガバナンスの各イテレーションを完了しているものとします。

### <a name="changes-in-the-current-state"></a>現在の状態の変化

このストーリーの前の段階では、会社は、クラウドの支出が会社の通常運用経費の一部になるときに、コスト管理とコスト監視の実装を開始しました。

その後、以下に示すように、ガバナンスに影響を与えるいくつかの変化がありました。

- ID は、Active Directory のオンプレミス インスタンスによって管理されている。 Azure Active Directory へのレプリケーションによりハイブリッド ID が促進された。
- IT の操作やクラウドの操作の多くは、Azure Monitor および関連する自動化機能によって管理されている。
- ディザスター リカバリーとビジネス継続性 (DRBC) は Azure Vault インスタンスによって制御されている。
- Azure Security Center を使用して、セキュリティ違反や攻撃を監視している。
- Azure Security Center と Azure Monitor を使用して、クラウドのガバナンスを監視している。
- Azure Blueprints、Azure Policy、および管理グループを使用して、ポリシーのコンプライアンスを自動化している。

### <a name="incrementally-improve-the-future-state"></a>将来の状態を段階的に改善する

目標は、取得した会社を、可能な限り既存事業に統合することです。

## <a name="changes-in-tangible-risks"></a>具体的なリスクの変化

**ビジネスの取得コスト:** 新しいビジネスの取得は、約 5 年間で黒字化する見込みです。 利益が出るまでに時間がかるため、取締役会は、できるだけ取得コストを管理することを希望しています。 コスト管理と技術的統合が互いに競合するリスクがあります。

このビジネス リスクは、いくつかの技術的リスクへと拡大する可能性があります。

- クラウドの移行によって追加の取得コストが生まれるリスクがある。
- また、新しい環境が適切に管理されていなかったり、ポリシー違反が発生したりするリスクもある。

## <a name="incremental-improvement-of-the-policy-statements"></a>ポリシー ステートメントの段階的な改善

ポリシーに対する次の変更は、新しいリスクを修正して導入をガイドするのに役立ちます。

- セカンダリ クラウド内のすべての資産は、既存の運用管理ツールとセキュリティ監視ツールを使用して監視する必要がある。
- すべての組織単位は、既存の ID プロバイダーに統合する必要がある。
- プライマリ ID プロバイダーが、セカンダリ クラウド内の資産への認証を管理する。

## <a name="incremental-improvement-of-the-best-practices"></a>ベスト プラクティスの段階的な改善

記事のこのセクションでは、ガバナンス MVP の設計を改善して、新しい Azure ポリシーと Azure Cost Management の実装を含めます。 これら 2 つの設計変更を組み合わせることで、会社の新しいポリシー ステートメントを実現します。

1. ネットワークを接続する。 ガバナンスによってサポートされるネットワーキングと IT セキュリティによって実行されます。
    1. MPLS または専用回線のプロバイダーから新しいクラウドに接続を追加することにより、ネットワークが統合されます。 ルーティング テーブルとファイアウォールの構成を追加することにより、環境の間のアクセスとトラフィックを管理します。
1. ID プロバイダーを統合する。 セカンダリ クラウドでホストされているワークロードに応じて、ID プロバイダーの統合に対してさまざまな選択肢があります。 以下に例を示します。
    1. OAuth 2 を使用して認証するアプリケーションの場合、セカンダリ クラウド内の Active Directory のユーザーは、単純に既存の Azure AD テナントにレプリケートできます。
    1. 一方、2 つのオンプレミス ID プロバイダー間のフェデレーションにより、新しい Active Directory ドメインのユーザーを Azure にレプリケートすることができます。
1. Azure Site Recovery に資産を追加する。
    1. Azure Site Recovery は、最初からハイブリッド クラウドおよびマルチクラウドのツールとして構築されました。
    1. セカンダリ クラウド内の仮想マシンは、オンプレミスの資産の保護に使用されているのと同じ Azure Site Recovery プロセスによって保護できる場合があります。
1. Azure Cost Management に資産を追加します。
    1. Azure Cost Management は、最初からマルチクラウド ツールとして構築されました。
    1. セカンダリ クラウド内の仮想マシンは、一部のクラウド プロバイダーの Azure Cost Management と互換性がある場合があります。 追加コストが適用される場合があります。
1. Azure Monitor に資産を追加する。
    1. Azure Monitor は、最初からハイブリッド クラウド ツールとして構築されました。
    1. セカンダリ クラウド内の仮想マシンは、Azure Monitor エージェントと互換性があり、運用監視のために Azure Monitor に含めることができる場合があります。
1. ガバナンス実施ツール。
    1. ガバナンス実施はクラウド固有です。
    1. ガバナンス ガイドで設定した会社のポリシーはクラウド固有ではありません。 実装はクラウドごとに異なる可能性がありますが、ポリシー ステートメントはセカンダリ プロバイダーに適用できます。

マルチクラウドの導入が増えるにつれて、上記のガバナンスの設計も成熟していきます。

## <a name="next-steps"></a>次の手順

多くの大企業で、クラウド ガバナンスの 5 つの規範が導入の妨げになる可能性があります。 次の記事では、ガバナンスをチーム スポーツにして、クラウドでの長期的成功の確保に役立てるいくつかの追加的な考えをご紹介します。

> [!div class="nextstepaction"]
> [複数レイヤーのガバナンス](./multiple-layers-of-governance.md)