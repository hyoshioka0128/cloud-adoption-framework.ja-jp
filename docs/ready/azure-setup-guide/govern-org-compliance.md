---
title: Azure のガバナンス、セキュリティ、コンプライアンス
description: Azure 向けのクラウド導入フレームワークを使用して、Azure 環境のガバナンス、セキュリティ、コンプライアンスを設定する方法について学習します。
author: tvuylsteke
ms.author: kfollis
ms.date: 09/27/2019
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.custom: fasttrack-edit, AQC, setup
ms.localizationpriority: high
ms.openlocfilehash: 3725963273a870cb753f3c939fbf69f5f8dc36b6
ms.sourcegitcommit: 08d6d5bda45814745fc181b0a07bcb8c415bf342
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/14/2020
ms.locfileid: "86373086"
---
<!-- cSpell:ignore tvuylsteke >

<!-- markdownlint-disable MD024 MD025 -->

# <a name="governance-security-and-compliance-in-azure"></a>Azure でのガバナンス、セキュリティ、コンプライアンス

企業のポリシーを確立し、ガバナンス戦略を策定するときに、Azure Policy、Azure Blueprints、Azure Security Center などのツールとサービスを使用して、組織のガバナンスに関する意思決定を実施したり自動化したりすることができます。 ガバナンスの計画を開始する前に、[ガバナンス ベンチマーク ツール](https://cafbaseline.com)を使用して、組織のクラウド ガバナンスへのアプローチにおける可能性のあるギャップを特定します。 ガバナンス プロセスの開発に関する詳細については、[管理方法](../../govern/index.md)に関するページを参照してください。

## <a name="azure-blueprints"></a>[Azure Blueprint](#tab/AzureBlueprints)

Azure Blueprints によってクラウド アーキテクトや中央の情報技術グループは、組織の標準、パターン、要件を実装および順守した反復可能な一連の Azure リソースを定義できます。 Azure Blueprints を使用すると、開発チームは新しい環境を迅速に構築して立ち上げることができます。これらは組織のコンプライアンスに従って構築され、ネットワークなどの一連の組み込みコンポーネントを含んでいるという確信が得られるため、開発とデリバリーをすばやく行うことができます。

ブループリントは、さまざまなリソース テンプレートやその他のアーティファクトのデプロイを宣言によって調整する手法です。

- ロールの割り当て。
- ポリシーの割り当て。
- Azure Resource Manager のテンプレート。
- リソース グループ。

### <a name="create-a-blueprint"></a>ブループリントを作成する

ブループリントを作成するには:

::: zone target="chromeless"

1. **[Blueprints:Getting started] (ブループリント: 作業の開始)** に移動します。
1. **[ブループリントの作成]** セクションで、 **[作成]** を選択します。
1. ブループリントの一覧をフィルター処理して、適切なブループリントを選択します。
1. **ブループリントの名前**を入力し、適切な**定義の場所**を選択します。
1. **[次へ :アーティファクト >>]** を選択し、ブループリントに含まれるアーティファクトを確認します。
1. **[下書きの保存]** を選択します。

::: form action="OpenBlade[#blade/Microsoft_Azure_Policy/BlueprintsMenuBlade/GetStarted]" submitText="Create a blueprint" :::

::: zone-end

::: zone target="docs"

<!-- docsTest:ignore "Next : Artifacts" "Blueprints: Get started" "Blueprints: Blueprint definitions" -->

1. [[Blueprints:はじめに]](https://portal.azure.com/#blade/Microsoft_Azure_Policy/BlueprintsMenuBlade/GetStarted) に移動します。
1. **[ブループリントの作成]** セクションで、 **[作成]** を選択します。
1. ブループリントの一覧をフィルター処理して、適切なブループリントを選択します。
1. **ブループリントの名前**を入力し、適切な**定義の場所**を選択します。
1. **[次へ :アーティファクト >>]** を選択し、ブループリントに含まれるアーティファクトを確認します。
1. **[下書きの保存]** を選択します。

::: zone-end

### <a name="publish-a-blueprint"></a>ブループリントを発行する

ブループリント アーティファクトをサブスクリプションに対して発行するには、次のようにします。

::: zone target="chromeless"

1. **[Blueprints:ブループリントの定義]** に移動します。
1. 前の手順で作成したブループリントを選択します。
1. ブループリントの定義を確認し、 **[ブループリントを発行する]** を選択します。
1. **[バージョン]** (_1.0_ など) と **[変更に関するメモ]** を入力し、 **[発行]** を選択します。

::: form action="OpenBlade[#blade/Microsoft_Azure_Policy/BlueprintsMenuBlade/Blueprints]" submitText="Blueprint definitions" :::

::: zone-end

::: zone target="docs"

1. [[Blueprints:ブループリントの定義]](https://portal.azure.com/#blade/Microsoft_Azure_Policy/BlueprintsMenuBlade/Blueprints) に移動します。
1. 前の手順で作成したブループリントの定義を選択します。
1. ブループリントの定義を確認し、 **[ブループリントを発行する]** を選択します。
1. **[バージョン]** (_1.0_ など) と **[変更に関するメモ]** を入力し、 **[発行]** を選択します。

::: zone-end

::: zone target="docs"

### <a name="learn-more"></a>詳細情報

詳細については、次を参照してください。

- [Azure Blueprint](https://docs.microsoft.com/azure/governance/blueprints)
- [クラウド導入フレームワーク:リソースの整合性の意思決定ガイド](../../decision-guides/resource-consistency/index.md)
- [標準ベースのブループリント サンプル](https://docs.microsoft.com/azure/governance/blueprints/samples/index#standards-based-blueprint-samples)

::: zone-end

## <a name="azure-policy"></a>[Azure Policy](#tab/AzurePolicy)

Azure Policy は、ポリシーの作成、割り当て、および管理に使用するサービスです。 これらのポリシーによってリソースにルールが適用されるため、それらのリソースは会社の標準とサービス レベル アグリーメントに準拠した状態に保たれます。 Azure Policy では、実装するポリシーに準拠していないリソースを識別するためにリソースがスキャンされます。 たとえば、環境内で特定のサイズの仮想マシン (VM) のみを許可するポリシーを作成できます。 このポリシーを実装する場合は、お使いの環境内の既存の VM と新たにデプロイした VM を評価します。 ポリシーの評価では、監視とレポートに使用するためのコンプライアンス イベントが生成されます。

次のことを行う一般的なポリシーを検討してください。

- リソースおよびリソース グループにタグ付けを適用します。
- デプロイされたリソースのリージョンを制限します。
- 特定のリソースのコストの高い SKU を制限します。
- Azure マネージド ディスクなどの重要なオプション機能の使用を監査します。

::: zone target="chromeless"

### <a name="action"></a>アクション

組み込みのポリシーを管理グループ、サブスクリプション、またはリソース グループに割り当てます。

::: form action="OpenBlade[#blade/Microsoft_Azure_Policy/PolicyMenuBlade/GettingStarted]" submitText="Assign Policy" :::

::: zone-end

::: zone target="docs"

### <a name="apply-a-policy"></a>ポリシーを適用する

リソース グループにポリシーを適用するには:

1. [Azure Policy](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/GettingStarted) に移動します。
1. **[ポリシーを割り当てる]** を選択します。

### <a name="learn-more"></a>詳細情報

詳細については、次を参照してください。

- [Azure Policy](https://docs.microsoft.com/azure/governance/policy)
- [クラウド導入フレームワーク:ポリシー適用の意思決定ガイド](../../decision-guides/policy-enforcement/index.md)

::: zone-end

## <a name="azure-security-center"></a>[Azure Security Center](#tab/AzureSecurityCenter)

Azure Security Center は、ガバナンス戦略における重要な役割を果たします。 次の機能により、セキュリティを常に把握できます。

- ワークロードにまたがる統合されたセキュリティ ビューを提供します。
- ファイアウォールやその他のパートナー ソリューションを含むさまざまなソースから、セキュリティ データが収集、検索、分析されます。
- 攻撃を受ける前に問題を修復するための実行可能なセキュリティの推奨事項が提供されます。
- セキュリティ標準への準拠を確実にするために、ハイブリッド クラウド ワークロードにまたがってセキュリティ ポリシーを適用できます。

セキュリティ ポリシーや推奨事項などのセキュリティ機能の多くは、無料でご利用いただけます。 Just-In-Time VM アクセスのようなより高度な機能の一部と、ハイブリッド ワークロードのサポートは、Security Center Standard レベルでご利用いただけます。 Just In Time VM アクセスでは、Azure VM 上の管理ポートに対するアクセスを制御することで、ネットワーク攻撃対象領域を縮小できます。

> [!TIP]
> 各サブスクリプションで、Azure Security Center は既定で有効になっています。 Azure Security Center がエージェントをインストールし、データの収集を開始できるようにするには、仮想マシンからデータ収集を有効にすることをお勧めします。

::: zone target="docs"

Azure Security Center について調べるには、[Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Security/SecurityMenuBlade/SecurityMenuBlade/0) に移動します。

### <a name="learn-more"></a>詳細情報

詳細については、次を参照してください。

- [Azure Security Center](https://docs.microsoft.com/azure/security-center)
- [Just In Time VM アクセス](https://docs.microsoft.com/azure/security-center/security-center-just-in-time#how-does-just-in-time-access-work)
- [Security Center の価格レベル](https://azure.microsoft.com/pricing/details/security-center)
- [クラウド導入フレームワーク:セキュリティ ベースライン規範](../../govern/security-baseline/index.md)

::: zone-end

::: zone target="chromeless"

### <a name="action"></a>アクション

::: form action="OpenBlade[#blade/Microsoft_Azure_Security/SecurityMenuBlade/SecurityMenuBlade/0]" submitText="Explore Azure Security Center" :::

::: zone-end
