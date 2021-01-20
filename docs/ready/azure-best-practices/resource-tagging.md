---
title: タグ付けの戦略を定義する
description: Azure のリソースと資産のタグ付けに関する推奨事項を確認します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 12/01/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.custom: internal, readiness, fasttrack-edit
ms.openlocfilehash: 2f889349e9ae296a416bfb4956dfd4810121576c
ms.sourcegitcommit: 003c04cf21c184be272102c9476f27dd675357bf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/19/2021
ms.locfileid: "98571925"
---
# <a name="define-your-tagging-strategy"></a>タグ付けの戦略を定義する

クラウド リソースにメタデータ タグを適用すると、リソース名に含めることができなかったそれらの資産に関する情報を含めることができます。 その情報を使用して、リソースに対してより高度なフィルター処理およびレポート作成を実行できます。 これらのタグには、リソースの関連するワークロードまたはアプリケーション、運用要件、および所有者情報に関するコンテキストを含めることができます。 IT チームやビジネス チームはこの情報を使用して、リソースを見つけたり、リソースの使用状況や請求に関するレポートを生成したりできます。

どのタグをリソースに適用するか、どのタグが必須か任意かは、組織によって異なります。 次の一覧は、リソースに関する重要なコンテキストと情報を取得する一般的なタグの例です。 この一覧を出発点として使用し、独自のタグ付け規則を指定してください。

## <a name="minimum-suggested-tags"></a>最小限の推奨されるタグ

次のタグは、後続のすべてのクラウド導入フレームワークの手法において実装とプロセスをガイドします。 これらの手法のベスト プラクティスの多くは、次のタグに基づくクラウドの操作とガバナンスの自動化を示しています。

| タグ名 | 説明 | キーとサンプル値 |
|--|--|--|
| **ワークロード名** | リソースがサポートするワークロードの名前。 | _WorkloadName_ <br><br> <li> `ControlCharts` |
| **データ分類** | このリソースによってホストされるデータの機密性。 | _DataClassification_ <br><br> <li> `Non-business` <li> `Public` <li> `General` <li> `Confidential` <li> `Highly confidential` |
| **ビジネス上の重要度** | リソースまたはサポートされているワークロードのビジネスへの影響。 | _重要度_ <br><br> <li> `Low` <li> `Medium` <li> `High` <li> `Business unit-critical` <li> `Mission-critical` |
| **事業単位** | リソースが属しているサブスクリプションまたはワークロードを所有する会社の最上位の部門。 小規模な組織では、このタグは 1 つの会社の、または共有された最上位の組織要素を表す可能性があります。 | _BusinessUnit_ <br><br> <li> `Finance` <li> `Marketing` <li> `Product XYZ` <li> `Corp` <li> `Shared` |
| **操作の確約** | このワークロードまたはリソースに提供される操作のサポートのレベル。 | _OpsCommitment_ <br><br> <li> `Baseline only` <li> `Enhanced baseline` <li> `Platform operations` <li> `Workload operations` |
| **運用チーム** | 日常の運用に責任を負うチーム。 | _OpsTeam_ <br><br> <li> `Central IT` <li> `Cloud operations` <li> `ControlCharts team` <li> `MSP-{Managed Service Provider name}` |

## <a name="additional-common-tagging-examples"></a>その他の一般的なタグ付けの例

Azure リソースの使用状況の可視性が向上させるために Azure で一般的に使用されている複数のタグを次に示します。

| タグ名 | 説明 | キーとサンプル値 |
|--|--|--|
| **アプリケーション名** | ワークロードが複数のアプリケーションまたはサービス間で細分化されている場合に、細分性を追加します。 | _ApplicationName_ <br><br> <li> `IssueTrackingSystem` |
| **承認者名** | このリソースに関連したコストの承認を担当する人物。 | _Approver_ <br><br> <li> `chris@contoso.com` |
| **必要な予算/承認された予算** | このアプリケーション、サービス、またはワークロードに割り当てられた金額。 | _BudgetAmount_ <br><br> <li> `$200,000` |
| **コスト センター** | このリソースに関連付けられたアカウンティング コスト センター。 | _CostCenter_ <br><br> <li> `55332` |
| **ディザスター リカバリー** | アプリケーション、ワークロード、またはサービスのビジネス上の重要度。 | _DR_ <br><br> <li> `Mission-critical` <li> `Critical` <li> `Essential` |
| **プロジェクトの終了日** | アプリケーション、ワークロード、またはサービスの使用停止がスケジュールされている日付。 | _EndDate_ <br><br> <li> `2023-10-15` |
| **環境** | アプリケーション、ワークロード、またはサービスのデプロイ環境。 | _Env_ <br><br> <li> `Prod` <li> `Dev` <li> `QA` <li> `Stage` <li> `Test` |
| **所有者名** | このアプリケーション、ワークロード、またはサービスの所有者。 | _所有者_ <br><br> <li> `jane@contoso.com` |
| **要求者名** | このアプリケーションの作成を要求したユーザー。 | _要求者_ <br><br> <li> `john@contoso.com` |
| **サービス クラス** | アプリケーション、ワークロード、またはサービスのサービス レベル アグリーメント レベル。 | _ServiceClass_ <br><br> <li> `Dev` <li> `Bronze` <li> `Silver` <li> `Gold` |
| **プロジェクトの開始日** | アプリケーション、ワークロード、またはサービスが最初にデプロイされた日付。 | _StartDate_ <br><br> <li> `2020-10-15` |

## <a name="take-action"></a>アクションの実行

「[リソースの名前付けとタグ付けの意思決定ガイド](../../decision-guides/resource-tagging/index.md)」を確認します。

## <a name="next-steps"></a>次のステップ

Azure のサブスクリプション間でリソース グループと資産を移動する方法を確認します。

> [!div class="nextstepaction"]
> [サブスクリプション間でリソース グループと資産を移動する](/azure/azure-resource-manager/management/move-resource-group-and-subscription)
