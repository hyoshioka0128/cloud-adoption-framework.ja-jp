---
title: Azure で移行ランディング ゾーンをデプロイする
description: Azure で移行ランディング ゾーンをデプロイする方法について説明します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 02/25/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.openlocfilehash: c85aec77b5d2e264ba6bc06fcce135125f82573e
ms.sourcegitcommit: 011525720bd9e2d9bcf03a76f371c4fc68092c45
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88574365"
---
# <a name="deploy-a-migration-landing-zone-in-azure"></a>Azure で移行ランディング ゾーンをデプロイする

"移行ランディング ゾーン" とは、オンプレミス環境から Azure に移行されるワークロードをホストする目的でプロビジョニングされ、準備された環境です。

## <a name="deploy-the-blueprint"></a>ブループリントをデプロイする

クラウド導入フレームワークで CAF 移行ランディング ゾーン ブループリントを使用する前に、次の設計原則、前提条件、決定事項、実装ガイダンスを確認してください。 このガイダンスが目的のクラウド導入計画と整合している場合は、デプロイ手順を使用して、[CAF 移行ランディング ゾーン ブループリント](/azure/governance/blueprints/samples/caf-migrate-landing-zone)をデプロイできます。

> [!div class="nextstepaction"]
> [ブループリント サンプルをデプロイする](/azure/governance/blueprints/samples/caf-migrate-landing-zone/deploy)

## <a name="design-principles"></a>設計原則

この実装オプションでは、すべての Azure ランディング ゾーンによって共有される共通の設計領域に対する厳格なアプローチが提供されます。 他の技術的な詳細については、以下の想定と決定事項を参照してください。

### <a name="deployment-options"></a>デプロイ オプション

この実装オプションでは、移行を開始するための実用最小限の製品 (MVP) がデプロイされます。 移行が進むにつれて、お客様はモジュール化されたリファクタリングベースのアプローチに従って、運用モデルを並行して成熟させることができます。[ガバナンス手法](../../govern/index.md)と[管理手法](../../manage/index.md)を使用し、初期の移行作業と並行してこれらの複雑なトピックに対処します。

この MVP アプローチによってデプロイされる具体的なリソースについては、[後の決定に関するセクション](#decisions)で説明します。

### <a name="enterprise-enrollment"></a>エンタープライズ登録

この実装オプションには、エンタープライズ登録での固有の位置はありません。 このアプローチは、Microsoft または Microsoft パートナーとの契約上の合意に関係なく、お客様に適用されるように設計されています。 この実装オプションをデプロイする前に、お客様は対象のサブスクリプションを作成しておく必要があります。

### <a name="identity"></a>ID

この実装オプションでは、[ID 管理のベスト プラクティス](/azure/security/fundamentals/identity-management-best-practices?bc=%2fazure%2fcloud-adoption-framework%2f_bread%2ftoc.json&toc=%2fazure%2fcloud-adoption-framework%2ftoc.json)に従って、ターゲット サブスクリプションが既に Azure Active Directory インスタンスに関連付けられている必要があります

### <a name="network-topology-and-connectivity"></a>ネットワーク トポロジと接続

この実装オプションでは、ゲートウェイ、ファイアウォール、ジャンプボックス、ランディング ゾーンのサブネットを使用して仮想ネットワークが作成されます。 次のステップの繰り返しとして、チームは[ネットワーク関連の意思決定ガイド](../considerations/networking-options.md)に従い、[ネットワーク セキュリティのベスト プラクティス](/azure/security/fundamentals/network-best-practices?bc=%2fazure%2fcloud-adoption-framework%2f_bread%2ftoc.json&toc=%2fazure%2fcloud-adoption-framework%2ftoc.json)に沿って、ゲートウェイ サブネットと他のネットワークとの間に適切な形式の接続を実装します。

### <a name="resource-organization"></a>リソースの編成

この実装オプションでは、特定のリソース グループによって定義されたワークロードに合わせてリソースが編成される、単一のランディング ゾーンが作成されます。 リソース編成に対するこのミニマリスト アプローチを選択すると、チームのクラウド運用モデルがより明確に定義されるまで、リソース編成の技術的な決定が延期されます。

このアプローチは、クラウド導入作業が[サブスクリプションの制限](/azure/azure-resource-manager/management/azure-subscription-service-limits)を超えることがないという前提に基づいています。 また、このオプションは、このランディング ゾーン内でのアーキテクチャの複雑さとセキュリティ要件が限定的であることも前提となっていいます。

クラウド導入計画の過程でこれが変化する場合は、[ガバナンス手法](../../govern/index.md)のガイダンスを使用して、リソース組織をリファクタリングすることが必要になる場合があります。

### <a name="governance-disciplines"></a>ガバナンスの規範

この実装オプションでは、どのようなガバナンス ツールも実装されません。 ポリシーの自動化が定義されていない場合は、いかなるミッション クリティカルなワークロードや機密データに対しても、このランディング ゾーンを使用しないでください。 このランディング ゾーンは、これらの初期段階の移行作業と並行して、全体的な運用モデルの学習、繰り返し、開発を開始するための限定的な運用デプロイに使用されることが想定されています。

ガバナンスの規範の並列開発の時間を短縮するには、[ガバナンス手法](../../govern/index.md)を確認し、CAF 移行ランディング ゾーン ブループリントに加えて、[CAF 基盤ブループリント](./foundation-blueprint.md)をデプロイすることを検討します。

> [!WARNING]
> ガバナンスの規範が成熟すると、リファクタリングが必要になる場合があります。 リファクタリングが必要になる場合があります。 具体的には、後でリソースを[新しいサブスクリプションまたはリソース グループに移動する](/azure/azure-resource-manager/management/move-resource-group-and-subscription?bc=%2fazure%2fcloud-adoption-framework%2f_bread%2ftoc.json&toc=%2fazure%2fcloud-adoption-framework%2ftoc.json)ことが必要になる場合があります。

### <a name="operations-baseline"></a>運用ベースライン

この実装オプションでは、どのような運用も実装されません。 運用ベースラインが定義されていない場合は、いかなるミッション クリティカルなワークロードや機密データに対しても、このランディング ゾーンを使用しないでください。 このランディング ゾーンは、これらの初期段階の移行作業と並行して、全体的な運用モデルの学習、繰り返し、開発を開始するための限定的な運用デプロイに使用されることが想定されています。

運用ベースラインの並列開発の時間を短縮するには、[ガバナンス手法](../../manage/index.md)を確認し、[Azure サーバー管理ガイド](../../manage/azure-server-management/index.md)をデプロイすることを検討します。

> [!WARNING]
> 運用ベースラインが開発されると、リファクタリングが必要になる場合があります。 具体的には、後でリソースを[新しいサブスクリプションまたはリソース グループに移動する](/azure/azure-resource-manager/management/move-resource-group-and-subscription?bc=%2fazure%2fcloud-adoption-framework%2f_bread%2ftoc.json&toc=%2fazure%2fcloud-adoption-framework%2ftoc.json)ことが必要になる場合があります。

### <a name="business-continuity-and-disaster-recovery-bcdr"></a>ビジネス継続性とディザスター リカバリー (BCDR)

この実装オプションでは、どのような BCDR ソリューションも実装されません。 保護と復旧のためのソリューションは、運用ベースラインの開発によって対処することが想定されています。

## <a name="assumptions"></a>前提条件

この初期ランディング ゾーンには、次の前提条件または制約が含まれています。 これらの前提条件がご自分の環境の制約と一致する場合、このブループリントを使用して最初のランディング ゾーンを作成することができます。 このブループリントは、固有の制約を満たすランディング ゾーン ブループリントを作成するために拡張することもできます。

- **サブスクリプションの制限**: この導入の取り組みでは、[サブスクリプションの制限](/azure/azure-resource-manager/management/azure-subscription-service-limits)を超えることは予想されていません。
- **コンプライアンス:** このランディング ゾーンではサード パーティのコンプライアンス要件は必要ありません。
- **アーキテクチャの複雑さ:** アーキテクチャの複雑さによって、追加の運用サブスクリプションが常に必要になるわけではありません。
- **共有サービス:** Azure の既存の共有サービスでは、このサブスクリプションをハブ アンド スポーク アーキテクチャのスポークのように扱うことを要求することはありません。
- **制限された運用環境スコープ:** このランディング ゾーンは、運用環境のワークロードをホストする可能性があります。 これは機密データやミッション クリティカルなワークロードに適した環境ではありません。

これらの前提条件が現在の導入ニーズに合致する場合は、ランディング ゾーンを作成するための開始点としてこのブループリントを使用することができます。

## <a name="decisions"></a>決定事項

ランディング ゾーン ブループリントでは、次の決定事項が示されます。

| コンポーネント                    | 決定事項                                                                                         | 他の方法                                                                                                                                                                                                                                                                |
|------------------------------|---------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 移行ツール              | Azure Site Recovery がデプロイされ、Azure Migrate プロジェクトが作成されます。                | [移行ツール決定ガイド](../../decision-guides/migrate-decision-guide/index.md)                                                                                                                                                                                               |
| ログ記録と監視       | Operational Insights ワークスペースと診断ストレージ アカウントがプロビジョニングされます。                |                                                                                                                                                                                                                                                                                       |
| ネットワーク                      | ゲートウェイ、ファイアウォール、ジャンプボックス、ランディング ゾーンのサブネットを持つ仮想ネットワークが作成されます。  | [ネットワーク関連の意思決定](../considerations/networking-options.md)                                                                                                                                                                                                                       |
| ID                     | サブスクリプションは既に Azure Active Directory インスタンスに関連付けられていると想定されます。 | [ID 管理のベスト プラクティス](/azure/security/fundamentals/identity-management-best-practices?bc=%2fazure%2fcloud-adoption-framework%2f_bread%2ftoc.json&toc=%2fazure%2fcloud-adoption-framework%2ftoc.json) |
| ポリシー                       | このブループリントでは現在、Azure ポリシーを適用しないことを想定しています。                        |                                                                                                                                                                                                                                                                                       |
| サブスクリプション デザイン          | 該当なし - 単一の運用サブスクリプション用に設計されています。                                              | [初期サブスクリプションを作成する](../azure-best-practices/initial-subscriptions.md)                                                                                                                                                                                                      |
| リソース グループ              | 該当なし - 単一の運用サブスクリプション用に設計されています。                                              | [サブスクリプションのスケーリング](../azure-best-practices/scale-subscriptions.md)                                                                                                                                                                                                                 |
| 管理グループ            | 該当なし - 単一の運用サブスクリプション用に設計されています。                                              | [サブスクリプションを整理および管理する](../azure-best-practices/organize-subscriptions.md)                                                                                                                                                                                                |
| Data                         | 該当なし                                                                                               | [Azure で正しい SQL Server のオプションを選択する](/azure/sql-database/sql-database-paas-vs-sql-server-iaas)および [Azure データ ストア ガイダンス](/azure/architecture/guide/technology-choices/data-store-overview)                       |
| ストレージ                      | 該当なし                                                                                               | [Azure Storage ガイダンス](../considerations/storage-options.md)                                                                                                                                                                                                                        |
| 名前付けとタグ付けの標準 | 該当なし                                                                                               | [名前付けとタグ付けのベスト プラクティス](../azure-best-practices/naming-and-tagging.md)                                                                                                                                                                                                    |
| コスト管理              | 該当なし                                                                                               | [コストの追跡](../azure-best-practices/track-costs.md)                                                                                                                                                                                                                              |
| Compute                      | 該当なし                                                                                               | [コンピューティング オプション](../considerations/compute-options.md)                                                                                                                                                                                                                               |

## <a name="customize-or-deploy-a-landing-zone"></a>ランディング ゾーンをカスタマイズまたはデプロイする

デプロイまたはカスタマイズの詳細については、Azure Blueprint サンプルで、CAF 移行ランディング ゾーン ブループリントの参照サンプルを参照し、ダウンロードしてください。

> [!div class="nextstepaction"]
> [ブループリント サンプルをデプロイする](/azure/governance/blueprints/samples/caf-migrate-landing-zone/deploy)

このブループリントに対して行うカスタマイズまたは結果として得られるランディング ゾーンのガイダンスについては、「[ランディング ゾーンに関する考慮事項](../considerations/index.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

最初のランディング ゾーンをデプロイしたら、ランディング ゾーンを拡張することができます。

> [!div class="nextstepaction"]
> [ランディング ゾーンを展開する](../considerations/index.md)
