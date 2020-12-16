---
title: Azure リソースの名前付けおよびタグ付けの戦略を作成する
description: エンタープライズ クラウドの導入作業におけるリソースの名前付けおよびタグ付けの戦略の概要について説明します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 12/01/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.custom: internal, readiness, fasttrack-edit
ms.openlocfilehash: 29b6f4d8528e3f8ecd531f33fce791d07cb9f26c
ms.sourcegitcommit: b6f2b4f8db6c3b1157299ece1f044cff56895919
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2020
ms.locfileid: "97024366"
---
# <a name="develop-your-naming-and-tagging-strategy-for-azure-resources"></a>Azure リソースの名前付けおよびタグ付けの戦略を作成する

ガバナンス、運用管理、およびアカウンティングの各要件をサポートするようにクラウド資産を整理します。 明確に定義された名前付け規則とメタデータのタグ付け規則を使用すると、リソースをすばやく見つけて管理するのに役立ちます。 これらの規則は、チャージバックとショーバックという会計処理を使用して、クラウドの使用コストをビジネス チームに関連付けるためにも役立ちます。

名前付けおよびタグ付けの戦略は、できるだけ早期に定義してください。 次のリンクを使用すると、戦略の定義と実装に役立ちます。

- [名前付け規則を定義する](./resource-naming.md)
- [Azure リソースの種類に推奨される省略形](./resource-abbreviations.md)
- [タグ付けの戦略を定義する](./resource-tagging.md)
- [リソースの名前付けとタグ付けの意思決定ガイド](../../decision-guides/resource-tagging/index.md)

> [!NOTE]
> 企業ごとに独自の組織上および管理上の要件があります。 これらの推奨事項は、クラウド導入チームとの話し合いを始めるのに役立ちます。 議論が進むなかで、以下のテンプレートを使用して、これらの推奨事項を特定のビジネス ニーズに整合させるときに行う名前付けおよびタグ付けに関する決定を文書化してください。
>
> [名前付けおよびタグ付け規則の追跡テンプレート](https://raw.githubusercontent.com/microsoft/CloudAdoptionFramework/master/ready/naming-and-tagging-conventions-tracking-template.xlsx)をダウンロードします。

## <a name="purpose-of-naming-and-tagging"></a>名前付けおよびタグ付けの目的

セキュリティ上の理由から、リソースを正確に表現し、名前を付けることは不可欠です。 セキュリティ インシデントが発生した場合に、影響を受けるシステム、それらのシステムがサポートする機能、およびビジネスへの影響の可能性を迅速に特定することが非常に重要です。 [Azure Security Center](/azure/security-center/security-center-introduction) や [Azure Sentinel](/azure/sentinel) などのセキュリティ サービスは、リソースと、リソース名によって関連付けられているログおよびアラート情報を参照します。

Azure では、[Azure リソースの名前付け規則と制限事項](/azure/azure-resource-manager/management/resource-name-rules)が定義されています。 このガイダンスでは、エンタープライズ クラウドの導入作業を支援するための詳細な推奨事項について説明します。

リソース名の変更が困難な場合があります。 大規模なクラウドのデプロイを開始する前に、包括的な名前付け規則を確立してください。

## <a name="naming-and-tagging-strategy"></a>名前付けおよびタグ付けの戦略

名前付けおよびタグ付け戦略には、リソース名とメタデータ タグのコンポーネントとしてビジネスおよび運用の詳細が含まれます。

- この戦略のビジネス面では、リソースの名前とタグに、チームを識別するために必要な組織情報が確実に含まれるようにします。 リソース コストを担当するビジネス オーナーと共にリソースを使用します。

- 運用の面では、名前とタグに、ワークロード、アプリケーション、環境、重要度、およびリソースの管理に役立つその他の情報を識別するために IT チームが使用する情報が確実に含まれるようにします。

## <a name="next-steps"></a>次のステップ

Azure のリソースおよび資産の名前付け規則の定義に関する考慮事項について理解し、Azure のリソースと資産の名前の例を確認します。

> [!div class="nextstepaction"]
> [Azure のリソースと資産に名前を付ける](./resource-naming.md)
