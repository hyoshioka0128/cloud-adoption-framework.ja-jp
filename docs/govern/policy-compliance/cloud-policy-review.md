---
title: クラウド ポリシーの確認を実施する
titleSuffix: Microsoft Cloud Adoption Framework for Azure
description: クラウド ポリシーの確認を実施する方法を説明します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 02/11/2019
ms.topic: guide
ms.service: cloud-adoption-framework
ms.subservice: govern
ms.custom: governance
ms.openlocfilehash: 167613bd304505bc53128c2864250e5cae80b281
ms.sourcegitcommit: 443c28f3afeedfbfe8b9980875a54afdbebd83a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/16/2019
ms.locfileid: "71031669"
---
<!-- markdownlint-disable MD026 -->

# <a name="conduct-a-cloud-policy-review"></a>クラウド ポリシーの確認を実施する

クラウド ポリシーの確認は、クラウドにおける[ガバナンスの成熟](../index.md)に向けた最初の手順です。 このプロセスの目的は、既存の社内 IT ポリシーを最新化することです。 完了すると、更新されたポリシーによって、クラウドベースのリソースに対して同等のレベルでリスク管理を実行できます。 この記事では、クラウド ポリシーの確認プロセスとその重要性について説明します。

## <a name="why-perform-a-cloud-policy-review"></a>クラウド ポリシーの確認を行う理由

ほとんどの企業は、管理ポリシーと整合するプロセスの実行を通じて IT を管理しています。 小規模な企業では、それらのポリシーは根拠があいまいで、プロセスが大まかに定義されている場合があります。 ビジネスが大企業へと成長するにつれ、ポリシーとプロセスはより明確に文書化され、一貫して実行される傾向があります。

企業において企業 IT ポリシーが成熟するにつれ、過去の技術的な決定への依存が管理ポリシーに組み込まれていく傾向があります。 たとえば、ディザスター リカバリーのプロセスにオフサイトのテープ バックアップを義務付けるポリシーが含まれていることがよくあります。 これを含めることは、1 つのタイプのテクノロジ (テープ バックアップ) への依存が前提となりますが、そのタイプは最適なソリューションではなくなっている場合があります。

クラウドの変化によって、過去のレガシ ポリシーの決定を再検討するための転換点が自然に発生します。 技術的な機能と既定のプロセスは、継承リスクと同様にクラウドで大きく変化します。 前の例を使用すると、テープ バックアップ ポリシーは 1 つの場所にデータを保持することによる単一障害点のリスクに端を発していたため、ビジネスはこのリスクを軽減して、リスク プロファイルを最小限に抑える必要があります。 クラウドのデプロイでは、はるかに低い目標復旧時間 (RTO) で同じリスク軽減を実現するいくつかの選択肢があります。 例:

- クラウド ネイティブ ソリューションでは、Azure SQL Database の geo レプリケーションを有効にできる場合があります。
- ハイブリッド ソリューションでは、Azure Site Recovery を使用して、IaaS ワークロードを複数のデータセンターにレプリケートできる場合があります。

クラウドの変換を実行する際には、クラウドの導入チームが利用できるツール、サービス、およびプロセスの多くをポリシーで管理することがよくあります。 それらのポリシーがレガシ テクノロジに基づいている場合、変更を推進するためのチームの取り組みを妨げる可能性があります。 最悪の場合、回避策を有効にするために、重要なポリシーが移行チームによって完全に無視されます。 どちらも許容できる結果ではありません。

## <a name="the-cloud-policy-review-process"></a>クラウド ポリシーの確認のプロセス

クラウド ポリシーの確認では、既存の IT ガバナンスと IT セキュリティ ポリシーを次の[クラウド ガバナンスの 5 つの規範](../index.md)と整合させます。[Cost Management](../cost-management/index.md)、[セキュリティ ベースライン](../security-baseline/index.md)、 [ID ベースライン](../identity-baseline/index.md)、[リソースの整合性](../resource-consistency/index.md)、[デプロイ高速化](../deployment-acceleration/index.md)。

確認プロセスは、これらの規範ごとに次の手順に従います。

1. 特定の規範に関連する既存のオンプレミス ポリシーを確認し、2 つの主要なデータ ポイント (レガシの依存、識別されたビジネス上のリスク) を探します。
2. 次の簡単な質問を行ってビジネス上の各リスクを評価します。「クラウド モデルにビジネス上のリスクがまだ存在しますか?」
3. リスクが依然として存在する場合は、技術的なソリューションではなく、必要な軽減策を文書化することでポリシーを書き換えます。
4. 更新されたポリシーをクラウド導入チームと共に確認して、必要な軽減策に対する可能なソリューションを理解します。

## <a name="example-of-a-policy-review-for-a-legacy-policy"></a>レガシ ポリシーにおけるポリシーの確認の例

プロセスの例を示すために、前のセクションのテープ バックアップ ポリシーをもう一度使用します。

- 企業ポリシーでは、すべての実稼働システムに対してオフサイト テープ バックアップが義務付けられています。 このポリシーでは、対象となる次の 2 つのデータ ポイントを確認できます。
  - テープ バックアップ ソリューションへのレガシの依存
  - 実稼働機器と同じ物理的な場所にバックアップを保存することに関連して想定されるビジネス上のリスク。
- リスクはまだ存在しますか? はい。 クラウドでも、1 つの施設に依存することでリスクが発生します。 オンプレミスのソリューションに存在していたリスクよりも、このリスクがビジネスに影響を与える可能性は低いですが、リスクは依然として存在します。
- ポリシーを書き換えます。 データ センター全体の障害の場合は、障害から 24 時間以内に別のデータセンターと別の地理的な場所に実稼働システムを復元する手段が必要です。
- クラウド導入のチームと確認します。 実装されるソリューションに応じて、このリソース整合性ポリシーに準拠する複数の手段があります。

## <a name="next-steps"></a>次の手順

クラウド ガバナンス戦略へのデータ分類の追加の詳細を確認します。

> [!div class="nextstepaction"]
> [データ分類](./data-classification.md)