---
title: ツールとテンプレート
description: クラウドの導入を促進するのに役立つ、クラウド導入フレームワークに用意されているツールとテンプレートを見つけてください。
author: JanetCThomas
ms.author: janet
ms.date: 04/14/2020
ms.service: cloud-adoption-framework
ms.subservice: reference
ms.topic: article
ms.openlocfilehash: 3968e453eb89a0f00d7fd23eeec90fb6a947cd05
ms.sourcegitcommit: 9a84c2dfa4c3859fd7d5b1e06bbb8549ff6967fa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/21/2020
ms.locfileid: "83756058"
---
<!-- cSpell:ignore Terraform's -->

# <a name="tools-and-templates"></a>ツールとテンプレート

クラウド導入フレームワークには、技術的変更をすばやく実装するのに役立つツールが含まれています。 これらのツール、テンプレート、評価を使用して、クラウドの導入を促進します。 以下のリソースは、導入の各フェーズで役立つ可能性があります。 一部のツールとテンプレートは、複数のフェーズで使用できます。

## <a name="strategy"></a>戦略

| リソース | 説明 |
|----------|-------------|
| [クラウド導入過程トラッカー](https://docs.microsoft.com/assessments/?mode=pre-assessment&id=cloud-journey-tracker) | ビジネスのニーズに基づいてクラウド導入パスを特定します。 |
| [戦略&nbsp;と&nbsp;計画&nbsp;テンプレート](https://archcenter.blob.core.windows.net/cdn/fusion/readiness/Microsoft-Cloud-Adoption-Framework-Strategy-and-Plan-Template.docx) | クラウド導入の戦略と計画を実行するときの決定事項を文書化します。 |

## <a name="plan"></a>プラン

| リソース | 説明 |
|----------|-------------|
| [クラウド導入過程トラッカー](https://docs.microsoft.com/assessments/?mode=pre-assessment&id=cloud-journey-tracker) | ビジネスのニーズに基づいてクラウド導入パスを特定します。 |
| [戦略&nbsp;と&nbsp;計画&nbsp;テンプレート](https://archcenter.blob.core.windows.net/cdn/fusion/readiness/Microsoft-Cloud-Adoption-Framework-Strategy-and-Plan-Template.docx) | クラウド導入の戦略と計画を実行するときの決定事項を文書化します。 |
| [クラウド導入計画ジェネレーター](../plan/template.md) | クラウド導入計画テンプレートを使用して [Azure Boards](https://docs.microsoft.com/azure/devops/boards/get-started/what-is-azure-boards) にバックログをデプロイすることで、プロセスを標準化します。 |

## <a name="ready"></a>Ready

| リソース | 説明 |
|----------|-------------|
| [適合性チェックリスト](https://raw.githubusercontent.com/Microsoft/CloudAdoptionFramework/master/ready/readiness-checklist.docx) | このチェックリストを使用して、導入のために、最初の移行ランディング ゾーンの準備、ブループリントのカスタマイズ、その拡張などの環境の準備を行います。 |
| [名前付けとタグ付けの追跡テンプレート](https://archcenter.blob.core.windows.net/cdn/fusion/readiness/CAF%20Readiness%20Naming%20and%20Tagging%20tracking%20template.xlsx) | 一貫性を確保し、オンボードする時間を短縮するために、名前付けとタグ付けの基準に関する決定事項を文書化します。 |
| [CAF&nbsp;基盤&nbsp;ブループリント](https://github.com/Microsoft/CloudAdoptionFramework/tree/master/ready/migration-landing-zone-governance) | 最初のガバナンス基盤となる軽量な実装を利用して、Azure でガバナンス ツールの実際の経験を提供します。 |
| [CAF 移行ランディング ゾーンのブループリント](https://github.com/Microsoft/CloudAdoptionFramework/tree/master/ready/migration-landing-zone) | オンプレミス環境から Azure に移行されるワークロードをホストするためのプロビジョニングと準備を行います。 このブループリントの詳細については、「[移行ランディング ゾーンをデプロイする](../ready/landing-zone/migrate-landing-zone.md)」を参照してください。 |
| [Terraform ランディング ゾーンのブループリント](../ready/landing-zone/terraform-landing-zone.md) | Terraform バージョンの CAF ランディング ゾーン ブループリント用のオープンソース コード ベース。 |
| [Terraform レジストリ](https://registry.terraform.io/search?q=aztfmod) | Terraform のレジストリ Web サイトがフィルター処理されて、Terraform ランディング ゾーンを作成するために必要なすべてのクラウド導入フレームワーク モジュールが一覧表示されます。 |

## <a name="govern"></a>ガバナンス

| リソース | 説明 |
|----------|-------------|
| [ガバナンス ベンチマーク評価](https://cafbaseline.com) | 現在の状態と、ビジネス上の優先事項との間のギャップを特定し、それらのギャップに対処するのに役立つ適切なリソースを取得します。 |
| [CAF&nbsp;基盤&nbsp;ブループリント](https://github.com/Microsoft/CloudAdoptionFramework/tree/master/ready/migration-landing-zone-governance) | Azure でガバナンス ツールに関する実際の経験を提供するための、最初のガバナンス基盤となる軽量な実装。 |
| [ガバナンス プロセス テンプレート](https://archcenter.blob.core.windows.net/cdn/fusion/governance/Governance%20Discipline%20Template.docx) | ガバナンスの各規範を適用するために使用される、ガバナンス プロセスの基本セットを定義します。 |
| [コスト管理プロセス テンプレート](https://archcenter.blob.core.windows.net/cdn/fusion/governance/Cost%20Management%20Discipline%20Template.docx) | コスト管理に重点を置いて組織内のクラウド ガバナンスを成熟させることができるポリシー ステートメントと設計ガイダンスを定義します。 |
| [デプロイ高速化プロセス テンプレート](https://archcenter.blob.core.windows.net/cdn/fusion/governance/Deployment%20Acceleration%20Discipline%20Template.docx) | デプロイの高速化に重点を置いて組織内のクラウド ガバナンスを成熟させることができるポリシー ステートメントと設計ガイダンスを定義します。 |
| [ID プロセス テンプレート](https://archcenter.blob.core.windows.net/cdn/fusion/governance/Identity%20Baseline%20Discipline%20Template.docx) | ID の要件に重点を置いて組織内のクラウド ガバナンスを成熟させることができるポリシー ステートメントと設計ガイダンスを定義します。 |
| [リソース整合性プロセス テンプレート](https://archcenter.blob.core.windows.net/cdn/fusion/governance/Resource%20Consistency%20Discipline%20Template.docx) | リソースの整合性に重点を置いて組織内のクラウド ガバナンスを成熟させることができるポリシー ステートメントと設計ガイダンスを定義します。 |
| [セキュリティ ベースライン プロセス テンプレート](https://archcenter.blob.core.windows.net/cdn/fusion/governance/Security%20Baseline%20Discipline%20Template.docx) | セキュリティ ベースラインに重点を置いて組織内のクラウド ガバナンスを成熟させることができるポリシー ステートメントと設計ガイダンスを定義します。 |

## <a name="manage"></a>管理する

| リソース | 説明 |
|----------|-------------|
| [Microsoft Azure Well-Architected Review](https://docs.microsoft.com/assessments/?id=azure-architecture-review) | このオンライン評価は、ワークロード固有の、アーキテクチャと操作に関するオプションの定義に役立ちます。 |
| [ベスト&nbsp;プラクティス&nbsp;ソース&nbsp;コード](https://github.com/Microsoft/CloudAdoptionFramework/tree/master/manage/Automation-Best-Practices) | このデプロイ可能なソース コードは、Azure サーバー管理サービスについてのベスト プラクティスを補完し、その導入を促進するものです。 このソース コードを使用して、運用管理を迅速に有効にし、運用ベースラインを確立します。 |
| [運用管理ブック](https://raw.githubusercontent.com/Microsoft/CloudAdoptionFramework/master/manage/opsmanagementworkbook.xlsx) | クラウドでの運用管理に関する決定事項を文書化し、ビジネス部門との会話を促進することで、SLA、回復性への投資、運用に関連する予算割り当てに関する整合性を確保します。 |

## <a name="organize"></a>整理

| リソース | 説明 |
|----------|-------------|
| [チーム間 RACI ダイアグラム](https://archcenter.blob.core.windows.net/cdn/fusion/management/raci-template.xlsx) | 組織構造の決定を経時的に追跡するために、RACI スプレッドシート テンプレートをダウンロードして変更します。 |
