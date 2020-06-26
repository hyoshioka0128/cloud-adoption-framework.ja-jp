---
title: Azure に CAF 基盤ブループリントをデプロイする
description: Azure に CAF 基盤ブループリントをデプロイする方法を説明します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 09/27/2019
ms.topic: guide
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.openlocfilehash: 672feabf0bb15d47ffdedbe85279bb7ff9228172
ms.sourcegitcommit: 9b183014c7a6faffac0a1b48fdd321d9bbe640be
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/19/2020
ms.locfileid: "85075905"
---
<!-- docsTest:ignore "CAF Foundation blueprint" -->

# <a name="deploy-a-caf-foundation-blueprint"></a>CAF 基盤ブループリントをデプロイする

CAF 基盤ブループリントでは、ランディング ゾーンはデプロイされません。 代わりに、ガバナンス規範の開発を始めるためのガバナンス MVP (実用最小限の製品) の確立に必要なツールがデプロイされます。 このブループリントは、既存のランディング ゾーンに追加するように設計されており、1 回の操作で CAF 移行ランディング ゾーンのブループリントに適用できます。

## <a name="deploy-the-blueprint"></a>ブループリントをデプロイする

クラウド導入フレームワークで CAF 基盤ブループリントを使用する前に、次の設計原則、前提条件、決定事項、実装ガイダンスを確認してください。 このガイダンスが目的のクラウド導入計画と整合している場合は、[デプロイ手順][deploy-sample]を使用して [CAF 基盤ブループリント](https://docs.microsoft.com/azure/governance/blueprints/samples/caf-foundation)をデプロイできます。

> [!div class="nextstepaction"]
> [ブループリント サンプルをデプロイする][deploy-sample]

## <a name="design-principles"></a>設計原則

この実装オプションでは、すべての Azure ランディング ゾーンによって共有される共通の設計領域に対する厳格なアプローチが提供されます。 他の技術的な詳細については、以下の想定と決定事項を参照してください。

### <a name="deployment-options"></a>デプロイ オプション

この実装オプションでは、ガバナンス規範の基盤として機能する "_実用最小限の製品 (MVP)_ " がデプロイされます。 チームは、モジュール化されたリファクタリングベースのアプローチに従い、[ガバナンス手法](../../govern/index.md)を使用してガバナンス規範を成熟させます。

### <a name="enterprise-enrollment"></a>エンタープライズ登録

この実装オプションには、エンタープライズ登録での固有の位置はありません。 このアプローチは、Microsoft または Microsoft パートナーとの契約上の合意に関係なく、お客様に適用されるように設計されています。 この実装オプションをデプロイする前に、お客様は対象のサブスクリプションを作成しておく必要があります。

### <a name="identity"></a>ID

この実装オプションでは、[ID 管理のベスト プラクティス](https://docs.microsoft.com/azure/security/fundamentals/identity-management-best-practices?toc=/azure/cloud-adoption-framework/toc.json&bc=/azure/cloud-adoption-framework/_bread/toc.json)に従って、ターゲット サブスクリプションが既に Azure Active Directory インスタンスに関連付けられている必要があります。

### <a name="network-topology-and-connectivity"></a>ネットワーク トポロジと接続

この実装オプションでは、[ネットワーク セキュリティのベスト プラクティス](https://docs.microsoft.com/azure/security/fundamentals/network-best-practices?toc=/azure/cloud-adoption-framework/toc.json&bc=/azure/cloud-adoption-framework/_bread/toc.json)に従って定義されたネットワーク トポロジが、ランディング ゾーンに既にあるものと想定されています。

### <a name="resource-organization"></a>リソースの編成

この実装オプションでは、Azure Policy でタグを適用することによりリソース組織の要素を追加する方法が示されます。 具体的には、Azure Policy を使用して `CostCenter` タグがリソースに追加されます。

ガバナンス チームは、タグ付けによって対処されるリソース組織の要素と、サブスクリプションの設計によって対処する必要がある要素を、比較対照する必要があります。 これらの基本的な決定により、クラウド導入計画の進行状況がリソース組織に通知されます。

この比較を導入サイクルの早い段階で支援するには、次の記事を検討する必要があります。

- [Azure の初期サブスクリプション](../azure-best-practices/initial-subscriptions.md): 導入規模のこの段階では、運用モデルに必要なサブスクリプションは 2、3、4 つのいずれですか。
- [サブスクリプションのスケーリング](../azure-best-practices/scale-subscriptions.md): 導入の規模に応じて、サブスクリプションのスケーリングを促進するためにどのような基準が使用されますか。
- [サブスクリプションの整理](../azure-best-practices/organize-subscriptions.md): スケーリングしたサブスクリプションをどのように整理しますか。
- [タグ付けの標準](../azure-best-practices/naming-and-tagging.md#metadata-tags): サブスクリプションの設計を強化するために、タグで一貫してキャプチャする必要があるその他の基準はありますか。

チームがクラウド導入を進めるときにこの比較を役立てるには、[ガバナンス ガイドの規範的なガイダンス](../../govern/guides/complex/prescriptive-guidance.md#application-of-governance-defined-patterns)に関する記事のガバナンス パターンに関するセクションを参照してください。 規範的なガイダンスのこのセクションでは、具体的な例と運用モデルに基づく一連のパターンが示されます。 そのガイダンスには、考慮する必要がある他のパターンへのリンクも含まれています。

### <a name="governance-disciplines"></a>ガバナンスの規範

この実装では、ガバナンス手法の Cost Management 規範における成熟度に対する 1 つのアプローチが示されています。 具体的には、Azure Policy を使用して特定の SKU の許可リストを作成する方法が示されています。 ランディング ゾーンにデプロイできるリソースの種類とサイズを制限することで、過剰な出費のリスクが軽減されます。

他のガバナンス規範の並列開発を高速化するには、[ガバナンス手法](../../govern/index.md)を確認します。 ガバナンスの Cost Management 規範の成熟を続けるには、[Cost Management 規範のガイダンス](../../govern/guides/complex/cost-management-improvement.md#incremental-improvement-of-the-best-practices)に関するページを参照してください。

> [!WARNING]
> ガバナンスの規範が成熟すると、リファクタリングが必要になる場合があります。 リファクタリングが必要になる場合があります。 具体的には、後でリソースを[新しいサブスクリプションまたはリソース グループに移動する](https://docs.microsoft.com/azure/azure-resource-manager/management/move-resource-group-and-subscription?toc=/azure/cloud-adoption-framework/toc.json&bc=/azure/cloud-adoption-framework/_bread/toc.json)ことが必要になる場合があります。

### <a name="operations-baseline"></a>運用ベースライン

この実装オプションでは、運用ベースラインのどのような側面も実装されません。 運用ベースラインが定義されていない場合は、いかなるミッション クリティカルなワークロードや機密データに対しても、このランディング ゾーンを使用しないでください。 このランディング ゾーンは、これらの初期段階の移行作業と並行して、全体的な運用モデルの学習、繰り返し、開発を開始するための限定的な運用デプロイに使用されることが想定されています。

運用ベースラインの並列開発の時間を短縮するには、[ガバナンス手法](../../manage/index.md)を確認し、[Azure サーバー管理ガイド](../../manage/azure-server-management/index.md)をデプロイすることを検討します。

> [!WARNING]
> 運用ベースラインが開発されると、リファクタリングが必要になる場合があります。 具体的には、後でリソースを[新しいサブスクリプションまたはリソース グループに移動する](https://docs.microsoft.com/azure/azure-resource-manager/management/move-resource-group-and-subscription?toc=/azure/cloud-adoption-framework/toc.json&bc=/azure/cloud-adoption-framework/_bread/toc.json)ことが必要になる場合があります。

### <a name="business-continuity-and-disaster-recovery-bcdr"></a>ビジネス継続性とディザスター リカバリー (BCDR)

この実装オプションでは、どのような BCDR ソリューションも実装されません。 保護と復旧のためのソリューションは、運用ベースラインの開発によって対処することが想定されています。

## <a name="assumptions"></a>前提条件

この初期ブループリントでは、チームは、初期クラウド移行作業と並行して、ガバナンス機能を成熟させる作業に取り組んでいることが想定されています。 これらの前提条件が実際の制約と一致する場合、そのブループリントを使用して、ガバナンス成熟度向上プロセスを始めることができます。

- **コンプライアンス:** このランディング ゾーンではサード パーティのコンプライアンス要件は必要ありません。
- **制限された運用環境スコープ:** このランディング ゾーンは、運用環境のワークロードをホストする可能性があります。 これは機密データやミッション クリティカルなワークロードに適した環境ではありません。

これらの前提条件が現在の導入ニーズに合致する場合は、ランディング ゾーンを作成するための開始点としてこのブループリントを使用することができます。

## <a name="customize-or-deploy-this-blueprint"></a>このブループリントをカスタマイズまたはデプロイする

デプロイまたはカスタマイズの詳細については、[Azure ブループリント サンプル][deploy-sample]のページで、CAF 基盤ブループリントの参照サンプルを参照し、ダウンロードしてください。

> [!div class="nextstepaction"]
> [ブループリント サンプルをデプロイする][deploy-sample]

## <a name="next-steps"></a>次のステップ

最初のランディング ゾーンをデプロイしたら、[ランディング ゾーンを拡張する](../considerations/index.md)ことができます。

> [!div class="nextstepaction"]
> [ランディング ゾーンを展開する](../considerations/index.md)

<!-- links -->

[Deploy-sample]: https://docs.microsoft.com/azure/governance/blueprints/samples/caf-foundation/deploy
