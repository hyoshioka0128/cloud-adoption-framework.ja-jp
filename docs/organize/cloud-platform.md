---
title: クラウド プラットフォームの機能について
description: ソース、スコープ、成果物を含むクラウド プラットフォームの機能について説明します。
author: JanetCThomas
ms.author: janet
ms.service: cloud-adoption-framework
ms.subservice: organize
ms.topic: conceptual
ms.date: 05/15/2020
ms.openlocfilehash: b6853bc98a2211496fba3bade2f04012e29ae78f
ms.sourcegitcommit: 57b757759b676a22f13311640b8856557df36581
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/20/2020
ms.locfileid: "94995603"
---
# <a name="cloud-platform-functions"></a>クラウド プラットフォームの機能

クラウドにより、多数の技術的な変更に加え、技術的なソリューションを効率化する機会ももたらされます。 ただし、一般的な IT の原則とビジネス ニーズは変わりません。 機密性の高いビジネス データは引き続き保護する必要があります。 お使いの IT プラットフォームがローカル エリア ネットワークに依存している場合は、クラウド上にネットワーク定義が必要になる可能性が高くなります。 アプリケーションやデータにアクセスする必要があるユーザーは、自分の現在の ID で関連するクラウド リソースにアクセスしたいと考えます。

クラウドによって新しいスキルを習得する機会が与えられる一方で、現在のアーキテクトは自身の経験と分野の専門知識を直接応用できるようにする必要があります。 クラウド プラットフォームの機能は、通常、クラウド プラットフォームについて学習することに焦点を絞った、選ばれたアーキテクト グループによって提供されます。 これらのアーキテクトはその後、他のユーザーの意思決定を支援し、クラウド環境にコントロールが適切に適用されるようにします。

完全なクラウド プラットフォームの機能を提供するために必要なスキルは、以下によって提供できます。

- エンタープライズ アーキテクチャ
- IT 運用
- IT ガバナンス
- IT インフラストラクチャ
- ネットワーク
- ID
- 仮想化
- 事業継続とディザスター リカバリー
- IT 部門のアプリケーション所有者

## <a name="preparation"></a>準備

- [クラウド アーキテクチャの基礎](https://www.pluralsight.com/courses/cloud-architecture-foundations):適切な基本的ソリューションの設計を支援する Pluralsight コース。
- [Microsoft Azure アーキテクチャ](https://www.pluralsight.com/courses/cloud-architecture-foundations): アーキテクトに Azure アーキテクチャの基礎を教える Pluralsight コース。
- [Azure のネットワーク サービス](/learn/modules/intro-to-azure-networking): Azure のネットワークの基本と、回復性を向上させて待ち時間を短縮する方法を学習します。

**以下のことを確認してください。**

- [ビジネス成果](../strategy/business-outcomes/index.md)
- [財務モデル](../strategy/financial-models.md)
- [クラウド導入の動機](../strategy/motivations.md)
- [ビジネス リスク](../govern/policy-compliance/risk-tolerance.md)
- [デジタル資産の合理化](../digital-estate/index.md)

## <a name="minimum-scope"></a>最小スコープ

クラウド プラットフォームの役目は、クラウド プラットフォームまたはランディング ゾーンの作成とサポートが中心となります。

通常、次のタスクは定期的に実行されます。

- [優先度付けされた移行バックログ](../migrate/migration-considerations/assess/release-iteration-backlog.md)に対して導入計画と進行状況を監視する。
- 移行バックログのサポートに必要なプラットフォームの変更を特定し、優先度順位を付ける。

**ミーティングの頻度:**

通常、クラウド プラットフォームの専門知識は作業チームから提供されます。 参加者は、日々のスケジュールの大部分をクラウド プラットフォームの作業に充てることになります。 協力の範囲はミーティングやフィードバック サイクルに限定されません。

## <a name="deliverables"></a>成果物

- ソリューションをサポートするクラウド プラットフォームを構築して管理する。
- プラットフォーム アーキテクチャを定義して実装する。
- クラウド プラットフォームを運用および管理する。
- プラットフォームを継続的に改善する。
- クラウド プラットフォームの最新の技術革新についていく。
- ビジネス価値の創造を支援する新しいクラウド機能を導入する。
- セルフサービス ソリューションを推奨する。
- ソリューションが既存のガバナンスおよびコンプライアンスの要件を満たしていることを確認する。
- プラットフォーム アーキテクチャのデプロイを作成して検証する。
- 新しいプラットフォーム要件の提供元のリリース計画を確認する。

## <a name="next-steps"></a>次のステップ

クラウド プラットフォームがより適切に定義されるようになるにつれて、[クラウド自動化機能](./cloud-automation.md)を調整することで導入を推進できます。 これは、ビジネス リスクや技術的なリスクを軽減しつつベスト プラクティスを確立することにも役立ちます。

"実行責任、説明責任、助言、通知" (RACI) の各パーティを明確にするクロスチーム マトリックスを作成することによりチーム間での責任を調整する方法について説明します。 [RACI テンプレート](https://raw.githubusercontent.com/microsoft/CloudAdoptionFramework/master/organize/raci-template.xlsx)をダウンロードして変更してください。
