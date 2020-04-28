---
title: ツールとテンプレート
description: クラウドの導入を促進するのに役立つ、クラウド導入フレームワークに用意されているツールとテンプレートを見つけてください。
ms.service: cloud-adoption-framework
ms.subservice: reference
ms.topic: article
author: JanetCThomas
ms.author: janet
ms.date: 04/14/2020
ms.openlocfilehash: e37019a571d5868b98083e9f7fba0198da2b1ff0
ms.sourcegitcommit: 825f9ae5b6cdd2fa6cb18c14a9733ba9106194f2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/21/2020
ms.locfileid: "81646726"
---
<!-- cSpell:ignore CAF Terraform's -->

# <a name="tools-and-templates"></a>ツールとテンプレート

クラウド導入フレームワークには、技術的変更をすばやく実装するのに役立つツールが含まれています。 これらのツール、テンプレート、評価を使用して、クラウドの導入を促進します。 以下のリソースは、導入の各フェーズで役立つ可能性があります。 一部のツールとテンプレートは、複数のフェーズで使用できます。

## <a name="strategy"></a>戦略

| リソース | 説明 |
|----------|-------------|
| [Cloud Journey Tracker](https://docs.microsoft.com/assessments/?mode=pre-assessment&id=cloud-journey-tracker) | ビジネスのニーズに基づいてクラウド導入パスを特定します。 |
| [戦略&nbsp;と&nbsp;計画&nbsp;テンプレート](https://archcenter.blob.core.windows.net/cdn/fusion/readiness/Microsoft-Cloud-Adoption-Framework-Strategy-and-Plan-Template.docx) | クラウド導入の戦略と計画を実行するときの決定事項を文書化します。 |

## <a name="plan"></a>プラン

| リソース | 説明 |
|----------|-------------|
| [Cloud Journey Tracker](https://docs.microsoft.com/assessments/?mode=pre-assessment&id=cloud-journey-tracker) | ビジネスのニーズに基づいてクラウド導入パスを特定します。 |
| [戦略&nbsp;と&nbsp;計画&nbsp;テンプレート](https://archcenter.blob.core.windows.net/cdn/fusion/readiness/Microsoft-Cloud-Adoption-Framework-Strategy-and-Plan-Template.docx) | クラウド導入の戦略と計画を実行するときの決定事項を文書化します。 |
| [クラウド導入計画ジェネレーター](../plan/template.md) | クラウド導入計画テンプレートを使用して [Azure Boards](https://docs.microsoft.com/azure/devops/boards/get-started/what-is-azure-boards) にバックログをデプロイすることで、プロセスを標準化します。 |

## <a name="ready"></a>Ready

| リソース | 説明 |
|----------|-------------|
| [適合性チェックリスト](https://raw.githubusercontent.com/microsoft/CloudAdoptionFramework/master/ready/readiness-checklist.docx) | このチェックリストを使用して、導入のために、最初の移行ランディング ゾーンの準備、ブループリントのカスタマイズ、その拡張などの環境の準備を行います。 |
| [名前付けとタグ付けの追跡テンプレート](https://archcenter.blob.core.windows.net/cdn/fusion/readiness/CAF%20Readiness%20Naming%20and%20Tagging%20tracking%20template.xlsx) | 一貫性を確保し、オンボードする時間を短縮するために、名前付けとタグ付けの基準に関する決定事項を文書化します。 |
| [CAF&nbsp;基盤&nbsp;ブループリント](https://github.com/microsoft/CloudAdoptionFramework/tree/master/ready/migration-landing-zone-governance) | 最初のガバナンス基盤となる軽量な実装を利用して、Azure でガバナンス ツールの実際の経験を提供します。 |
| [CAF 移行ランディング ゾーンのブループリント](https://github.com/microsoft/CloudAdoptionFramework/tree/master/ready/migration-landing-zone) | オンプレミス環境から Azure に移行されるワークロードをホストするためのプロビジョニングと準備を行います。 このブループリントの詳細については、「[移行ランディング ゾーンをデプロイする](https://docs.microsoft.com/azure/architecture/cloud-adoption/ready/azure-readiness-guide/migration-landing-zone)」を参照してください。 |
| [Terraform ランディング ゾーンのブループリント](https://github.com/microsoft/CloudAdoptionFramework/tree/master/ready/terraform-landing-zones/landingzone_caf_foundations) | Terraform バージョンの CAF ブループリント用のオープンソース コード ベース。 |
| [Terraform レジストリ](https://registry.terraform.io/search?q=aztfmod) | Terraform のレジストリ Web サイトがフィルター処理されて、Terraform ランディング ゾーンを作成するために必要なすべてのクラウド導入フレームワーク モジュールが一覧表示されます。 |

## <a name="govern"></a>ガバナンス

| リソース | 説明 |
|----------|-------------|
| [ガバナンス ベンチマーク評価](https://cafbaseline.com) | 現在の状態と、ビジネス上の優先事項との間のギャップを特定し、それらのギャップに対処するのに役立つ適切なリソースを取得します。 |
| [CAF&nbsp;基盤&nbsp;ブループリント](https://github.com/microsoft/CloudAdoptionFramework/tree/master/ready/migration-landing-zone-governance) | Azure でガバナンス ツールに関する実際の経験を提供するための、最初のガバナンス基盤となる軽量な実装。 |
| [ガバナンス プロセス テンプレート](https://archcenter.blob.core.windows.net/cdn/fusion/governance/Governance%20Discipline%20Template.docx) | ガバナンスの各規範を適用するために使用される、ガバナンス プロセスの基本セットを定義します。 |
| [Cost Management プロセス テンプレート](https://archcenter.blob.core.windows.net/cdn/fusion/governance/Cost%20Management%20Discipline%20Template.docx) | コスト管理に重点を置いて組織内のクラウド ガバナンスを成熟させることができるポリシー ステートメントと設計ガイダンスを定義します。 |
| [デプロイ高速化プロセス テンプレート](https://archcenter.blob.core.windows.net/cdn/fusion/governance/Deployment%20Acceleration%20Discipline%20Template.docx) | デプロイの高速化に重点を置いて組織内のクラウド ガバナンスを成熟させることができるポリシー ステートメントと設計ガイダンスを定義します。 |
| [ID プロセス テンプレート](https://archcenter.blob.core.windows.net/cdn/fusion/governance/Identity%20Baseline%20Discipline%20Template.docx) | ID の要件に重点を置いて組織内のクラウド ガバナンスを成熟させることができるポリシー ステートメントと設計ガイダンスを定義します。 |
| [リソース整合性プロセス テンプレート](https://archcenter.blob.core.windows.net/cdn/fusion/governance/Resource%20Consistency%20Discipline%20Template.docx) | リソースの整合性に重点を置いて組織内のクラウド ガバナンスを成熟させることができるポリシー ステートメントと設計ガイダンスを定義します。 |
| [セキュリティ ベースライン プロセス テンプレート](https://archcenter.blob.core.windows.net/cdn/fusion/governance/Security%20Baseline%20Discipline%20Template.docx) | セキュリティ ベースラインに重点を置いて組織内のクラウド ガバナンスを成熟させることができるポリシー ステートメントと設計ガイダンスを定義します。 |

## <a name="manage"></a>管理する

| リソース | 説明 |
|----------|-------------|
| [Azure アーキテクチャ レビュー](https://docs.microsoft.com/assessments/?id=azure-architecture-review) | このオンライン評価は、ワークロード固有の、アーキテクチャと操作に関するオプションの定義に役立ちます。 |
| [ベスト&nbsp;プラクティス&nbsp;ソース&nbsp;コード](https://github.com/microsoft/CloudAdoptionFramework/tree/master/manage/Automation-Best-Practices) | このデプロイ可能なソース コードは、Azure サーバー管理のベスト プラクティスに記載されているガイダンスを補完し、導入を促進するものです。 このソース コードを使用して、運用管理を迅速に有効にし、運用ベースラインを確立します。 |
| [運用管理ブック](https://raw.githubusercontent.com/microsoft/CloudAdoptionFramework/master/manage/opsmanagementworkbook.xlsx) | クラウドでの運用管理に関する決定事項を文書化し、ビジネス部門との会話を促進することで、SLA、回復性への投資、運用に関連する予算割り当てに関する整合性を確保します。 |

## <a name="organize"></a>整理

| リソース | 説明 |
|----------|-------------|
| [チーム間 RACI ダイアグラム](https://archcenter.blob.core.windows.net/cdn/fusion/management/raci-template.xlsx) | 組織構造の決定を経時的に追跡するために、RACI スプレッドシート テンプレートをダウンロードして変更します。 |
