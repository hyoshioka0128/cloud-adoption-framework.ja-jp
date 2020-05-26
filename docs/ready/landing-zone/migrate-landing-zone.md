---
title: Azure で移行ランディング ゾーンをデプロイする
description: Azure で移行ランディング ゾーンをデプロイする方法について説明します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 02/25/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.openlocfilehash: 601481112f2d8144596951e1a68bd7d0bda0b95a
ms.sourcegitcommit: 7660521b631ea092fb805df9c9d28ad3024287ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2020
ms.locfileid: "83621762"
---
<!-- cSpell:ignore vCPUs jumpbox -->

# <a name="deploy-a-migration-landing-zone"></a>移行ランディング ゾーンをデプロイする

"_移行ランディング ゾーン_" とは、オンプレミス環境から Azure に移行されるワークロードをホストする目的でプロビジョニングされ、準備された環境です。

## <a name="deploy-the-first-landing-zone"></a>最初のランディング ゾーンをデプロイする

クラウド導入フレームワークで移行ランディング ゾーン ブループリントを使用する前に、次の前提条件、決定事項、実装ガイダンスを確認してください。 このガイダンスが目的のクラウド導入計画と整合している場合は、[デプロイ手順][deploy-sample]を使用して[移行ランディング ゾーン ブループリント](https://docs.microsoft.com/azure/governance/blueprints/samples/caf-migrate-landing-zone)をデプロイできます。

> [!div class="nextstepaction"]
> [ブループリント サンプルをデプロイする][deploy-sample]

## <a name="assumptions"></a>前提条件

この初期ランディング ゾーンには、次の前提条件または制約が含まれています。 これらの前提条件がご自分の環境の制約と一致する場合、このブループリントを使用して最初のランディング ゾーンを作成することができます。 このブループリントは、固有の制約を満たすランディング ゾーン ブループリントを作成するために拡張することもできます。

- **サブスクリプションの制限**: この導入の取り組みでは、[サブスクリプションの制限](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits)を超えることは予想されていません。
- **コンプライアンス:** このランディング ゾーンではサード パーティのコンプライアンス要件は必要ありません。
- **アーキテクチャの複雑さ:** アーキテクチャの複雑さによって、追加の運用サブスクリプションが常に必要になるわけではありません。
- **共有サービス:** このサブスクリプションをハブ アンド スポーク アーキテクチャのスポークのように扱うことを要求する既存の共有サービスは Azure にありません。
- **制限された運用環境スコープ:** このランディング ゾーンは、運用環境のワークロードをホストする可能性があります。 これは機密データやミッション クリティカルなワークロードに適した環境ではありません。

これらの前提条件が現在の導入ニーズに合致する場合は、ランディング ゾーンを作成するための開始点としてこのブループリントを使用することができます。

## <a name="decisions"></a>決定事項

ランディング ゾーン ブループリントでは、次の決定事項が示されます。

| コンポーネント                    | 決定事項                                                                                         | 他の方法                                                                                                                                                                                                                                                                |
|------------------------------|---------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 移行ツール              | Azure Site Recovery がデプロイされ、Azure Migrate プロジェクトが作成されます。                | [移行ツール決定ガイド](../../decision-guides/migrate-decision-guide/index.md)                                                                                                                                                                                               |
| ログ記録と監視       | Operational Insights ワークスペースと診断ストレージ アカウントがプロビジョニングされます。                |                                                                                                                                                                                                                                                                                       |
| ネットワーク                      | ゲートウェイ、ファイアウォール、ジャンプボックス、ランディング ゾーンのサブネットを持つ仮想ネットワークが作成されます。  | [ネットワーク関連の意思決定](../considerations/networking-options.md)                                                                                                                                                                                                                       |
| ID                     | サブスクリプションは既に Azure Active Directory インスタンスに関連付けられていると想定されます。 | [ID 管理のベスト プラクティス](https://docs.microsoft.com/azure/security/fundamentals/identity-management-best-practices?toc=/azure/cloud-adoption-framework/toc.json&bc=/azure/cloud-adoption-framework/_bread/toc.json) |
| ポリシー                       | このブループリントでは現在、Azure ポリシーを適用しないことを想定しています。                        |                                                                                                                                                                                                                                                                                       |
| サブスクリプション デザイン          | 該当なし - 単一の運用サブスクリプション用に設計されています。                                              | [初期サブスクリプションを作成する](../azure-best-practices/initial-subscriptions.md)                                                                                                                                                                                                      |
| リソース グループ              | 該当なし - 単一の運用サブスクリプション用に設計されています。                                              | [サブスクリプションのスケーリング](../azure-best-practices/scale-subscriptions.md)                                                                                                                                                                                                                 |
| 管理グループ            | 該当なし - 単一の運用サブスクリプション用に設計されています。                                              | [サブスクリプションを整理および管理する](../azure-best-practices/organize-subscriptions.md)                                                                                                                                                                                                |
| Data                         | 該当なし                                                                                               | 「[Azure SQL で適切なデプロイ オプションを選択する](https://docs.microsoft.com/azure/sql-database/sql-database-paas-vs-sql-server-iaas)」と [Azure データ ストア ガイダンス](https://docs.microsoft.com/azure/architecture/guide/technology-choices/data-store-overview)                       |
| ストレージ                      | 該当なし                                                                                               | [Azure Storage ガイダンス](../considerations/storage-options.md)                                                                                                                                                                                                                        |
| 名前付けとタグ付けの標準 | 該当なし                                                                                               | [名前付けとタグ付けのベスト プラクティス](../azure-best-practices/naming-and-tagging.md)                                                                                                                                                                                                    |
| コスト管理              | 該当なし                                                                                               | [コストの追跡](../azure-best-practices/track-costs.md)                                                                                                                                                                                                                              |
| Compute                      | 該当なし                                                                                               | [コンピューティング オプション](../considerations/compute-options.md)                                                                                                                                                                                                                               |

## <a name="customize-or-deploy-a-landing-zone"></a>ランディング ゾーンをカスタマイズまたはデプロイする

デプロイまたはカスタマイズの詳細については、[Azure Blueprints サンプル][deploy-sample]に関するページで、CAF 移行ランディング ゾーン ブループリントの参照サンプルを参照し、ダウンロードしてください。

> [!div class="nextstepaction"]
> [ブループリント サンプルをデプロイする][deploy-sample]

このブループリントに対して行うカスタマイズまたは結果として得られるランディング ゾーンのガイダンスについては、「[ランディング ゾーンに関する考慮事項](../considerations/index.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

最初のランディング ゾーンをデプロイしたら、[ランディング ゾーンを展開](../considerations/index.md)することができます。

> [!div class="nextstepaction"]
> [ランディング ゾーンを展開する](../considerations/index.md)

<!-- links -->

[deploy-sample]: https://docs.microsoft.com/azure/governance/blueprints/samples/caf-migrate-landing-zone/deploy
