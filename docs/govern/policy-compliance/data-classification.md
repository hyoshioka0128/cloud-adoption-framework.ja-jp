---
title: データ分類とは
titleSuffix: Microsoft Cloud Adoption Framework for Azure
description: データ分類とは
author: BrianBlanchard
ms.author: brblanch
ms.date: 09/17/2019
ms.topic: guide
ms.service: cloud-adoption-framework
ms.subservice: govern
ms.custom: governance
ms.openlocfilehash: 86c57efed1be2760aca607197eb8d28f0151097a
ms.sourcegitcommit: d19e026d119fbe221a78b10225230da8b9666fe1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2019
ms.locfileid: "71223569"
---
<!-- markdownlint-disable MD026 -->

# <a name="what-is-data-classification"></a>データ分類とは

データ分類によって、組織のデータを判定して価値を割り当てることができます。これは、ガバナンスの一般的な開始点です。 データ分類プロセスでは、リスクを識別するために、機密性とビジネスへの影響によってデータを分類します。 データを分類した後、機密情報や重要なデータを盗難や損失から保護する方法で管理できます。

## <a name="understand-data-risks-then-manage-them"></a>データのリスクを理解して管理する

リスクを管理する前に、リスクについて理解しておく必要があります。 データ侵害の責任の場合は、データの分類から理解を始めます。 データ分類は、デジタル資産内のすべての資産にメタ データ特性を関連付けるプロセスです。それにより、その資産に関連付けられているデータの種類を識別します。

Microsoft では、クラウドへの移行やデプロイの潜在的な候補として識別されている資産はすべて、データ分類、ビジネス上の重要度、および請求責任を記録するためのメタデータを文書化しておくことを推奨しています。 分類のこれら 3 つのポイントが、リスクの理解と軽減に大いに役立ちます。

## <a name="microsofts-data-classification"></a>Microsoft のデータ分類

Microsoft が使用する分類の一覧を以下に示します。 業界や既存のセキュリティ要件に応じて、データ分類の標準は、組織内に既に存在する場合があります。 標準が存在しない場合は、このサンプル分類を使用して、ご自身のデジタル資産とリスク プロファイルを深く理解することをお勧めします。

- **ビジネス以外:** Microsoft に属していない私生活のデータ。
- **パブリック:** 自由に利用でき、公開することが承認されているビジネス データ。
- **全般:** 一般の人々を対象としていないビジネス データ。
- **機密:** 過剰に共有された場合に Microsoft に損害を与える可能性のあるビジネス データ。
- **極秘:** 過剰に共有された場合に Microsoft に広範な損害を与える可能性のあるビジネス データ。

## <a name="tagging-data-classification-in-azure"></a>Azure でのデータ分類のタグ付け

メタデータ ストレージで推奨されるアプローチはリソース タグであり、これらのタグを使用して、デプロイされるリソースにデータ分類情報を適用できます。 分類によるクラウド資産のタグ付けは正式なデータ分類プロセスの代わりにはなりませんが、リソースの管理とポリシーの適用を行うための有益なツールを提供します。 [Azure Information Protection](https://docs.microsoft.com/azure/information-protection/what-is-information-protection) は、配置場所 (オンプレミス、Azure など) に関係なく "_データ_" を分類するために役立つ優れたソリューションであり、全体的な分類戦略の一部として考慮する必要があります。

Azure でのリソースのタグ付けの詳細については、[タグを使用した Azure リソースの整理](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)に関する記事をご覧ください。

## <a name="next-steps"></a>次の手順

いずれかの「実践的なガバナンス ガイド」の際にデータ分類を適用します。

> [!div class="nextstepaction"]
> [実践的なガバナンス ガイドを選択する](../guides/index.md)
