---
title: Azure で移行ランディング ゾーンをデプロイする
titleSuffix: Microsoft Cloud Adoption Framework for Azure
description: Azure で移行ランディング ゾーンをデプロイする方法について説明します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 09/27/2019
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.custom: fasttrack-edit
ms.openlocfilehash: 329274859f50aa83ebb90e79597fa1ffe0973ab8
ms.sourcegitcommit: 1dccf1aed8e98aa0f58c4f86d90c65f5fa5ac84d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2019
ms.locfileid: "71811102"
---
# <a name="deploy-a-migration-landing-zone"></a>移行ランディング ゾーンをデプロイする

"*移行ランディング ゾーン*" とは、オンプレミス環境から Azure に移行されるワークロードをホストする目的でプロビジョニングされ、準備された環境を表すために使用される用語です。 移行ランディング ゾーンは、Azure 対応性ガイドの最終成果物です。 この記事では、このガイドで説明する対応性に関するすべてのテーマをまとめて、決定された内容を最初の移行ランディング ゾーンのデプロイに適用します。

以下の各セクションで、移行中の使用に適した環境を設定するために一般的に使用されるランディング ゾーンの概要を示します。 この記事で説明している環境、つまりランディング ゾーンは、Azure ブループリントにも取り込まれています。 クラウド導入フレームワークの移行ランディング ゾーンのブループリントを使用すると、定義済みの環境をシングル クリックでデプロイできます。

## <a name="purpose-of-the-blueprint"></a>ブループリントの目的

クラウド導入フレームワークの移行ランディング ゾーンのブループリントは、ランディング ゾーンを作成します。 このランディング ゾーンは意図的に制限されています。 これは、コードとしてのインフラストラクチャについて学ぶ余地がある一貫した出発点を作成することを目的としています。 移行作業によっては、要件を満たすためにこのランディング ゾーンで十分な場合があります。 一方で、固有の制約を満たすためにブループリントを一部変更しなければならない可能性も少なくありません。

## <a name="blueprint-alignment"></a>ブループリントの配置

次の図は、クラウド導入フレームワークの移行ランディング ゾーンのブループリントを、アーキテクチャの複雑さとコンプライアンスの要件との関係から示しています。

![ブループリントの配置](../../_images/ready/blueprint-overview.png)

- 文字 A は、このブループリントの範囲を示す曲線の内側にあります。 この範囲が意味する内容は、このブループリントは、対応しているアーキテクチャの複雑性は限定的であるが、比較的中間のコンプライアンス要件に基づいて構築されているということです。
- 高度な複雑さと厳格なコンプライアンス要件を持つお客様は、パートナーの拡張ブループリント、または[標準に基づくブループリント サンプル](https://docs.microsoft.com/azure/governance/blueprints/samples)の 1 つを利用することで、より適切に対応できると考えられます。
- ほとんどのお客様は、対極にあるこれら 2 つのケースの間のどこかに該当します。 文字 B は、「[ランディング ゾーンに関する考慮事項](../considerations/index.md)」の記事に記載されているプロセスを表します。 このスペース内のお客様は、それらの記事にある意思決定ガイドを使用して、クラウド導入フレームワークの移行ランディング ゾーンのブループリントに追加するノードを特定できます。 この方法では、ニーズに合わせてブルー プリントをカスタマイズできます。

## <a name="use-this-blueprint"></a>このブループリントを使用する

クラウド導入フレームワークの移行ランディング ゾーンのブループリントを使用する前に、以下の前提条件、決定事項、実装ガイダンスを確認してください。

## <a name="assumptions"></a>前提条件

この初期のランディング ゾーンを定義するときに、以下の前提条件または制約が使用されました。 これらの前提条件がご自分の環境の制約と一致する場合、このブループリントを使用して最初のランディング ゾーンを作成することができます。 このブループリントは、固有の制約を満たすランディング ゾーン ブループリントを作成するために拡張することもできます。

- **サブスクリプションの制限**: この導入の取り組みでは、[サブスクリプションの制限](https://docs.microsoft.com/azure/azure-subscription-service-limits)を超えることは予想されていません。 2 つの一般的な指標は、25,000 個の VM または 10,000 個の vCPU を超過することです。
- **コンプライアンス:** このランディング ゾーンではサード パーティのコンプライアンス要件は必要ありません。
- **アーキテクチャの複雑さ:** アーキテクチャの複雑さによって、追加の運用サブスクリプションが常に必要になるわけではありません。
- **共有サービス:** このサブスクリプションをハブアンドスポーク アーキテクチャのスポークのように扱うことを要求する既存の共有サービスは Azure にありません。

これらの前提条件が現在の環境に適合すると思われる場合、このブループリントは、ランディング ゾーンの作成を行うための適切な開始点となる可能性があります。

## <a name="decisions"></a>決定事項

ランディング ゾーン ブループリントでは、次の決定事項が示されます。

| コンポーネント | 決定事項 | 他の方法 |
|---------|---------|---------|
|移行ツール|Azure Site Recovery がデプロイされ、Azure Migrate プロジェクトが作成されます。|[移行ツール決定ガイド](../../decision-guides/migrate-decision-guide/index.md)|
|ログ記録と監視|Operational Insights ワークスペースと診断ストレージ アカウントがプロビジョニングされます。|         |
|ネットワーク|ゲートウェイ、ファイアウォール、ジャンプボックス、ランディング ゾーンのサブネットを持つ仮想ネットワークが作成されます。|[ネットワーク関連の意思決定](../considerations/network-decisions.md)|
|ID|サブスクリプションは既に Azure Active Directory インスタンスに関連付けられていると想定されます。|[ID 管理のベスト プラクティス](https://docs.microsoft.com/azure/security/azure-security-identity-management-best-practices?toc=https://docs.microsoft.com/azure/cloud-adoption-framework/toc.json&bc=https://docs.microsoft.com/azure/cloud-adoption-framework/bread/toc.json)         |
|ポリシー|このブループリントでは現在、Azure ポリシーを適用しないことを想定しています。|         |
|サブスクリプション デザイン|該当なし - 単一の運用サブスクリプション用に設計されています。|[サブスクリプションのスケーリング](../considerations/scaling-subscriptions.md)|
|管理グループ|該当なし - 単一の運用サブスクリプション用に設計されています。|[サブスクリプションのスケーリング](../considerations/scaling-subscriptions.md)         |
|リソース グループ|該当なし - 単一の運用サブスクリプション用に設計されています。|[サブスクリプションのスケーリング](../considerations/scaling-subscriptions.md)         |
|Data|該当なし|「[Azure SQL で適切なデプロイ オプションを選択する](https://docs.microsoft.com/azure/sql-database/sql-database-paas-vs-sql-server-iaas?toc=https://docs.microsoft.com/azure/architecture/toc.json&bc=https://docs.microsoft.com/azure/architecture/bread/toc.json)」と [Azure データ ストア ガイダンス](https://docs.microsoft.com/azure/architecture/guide/technology-choices/data-store-overview) |
|Storage|該当なし|[Azure Storage ガイダンス](../considerations/storage-guidance.md)         |
|名前付けとタグ付けの標準|該当なし|[名前付けとタグ付けのベスト プラクティス](../considerations/naming-and-tagging.md)         |
|コスト管理|該当なし|[コストの追跡](../azure-best-practices/track-costs.md)|
|Compute|該当なし|[コンピューティング オプション](../considerations/compute-decisions.md)|

## <a name="customize-or-deploy-a-landing-zone-from-this-blueprint"></a>このブループリントからランディング ゾーンをカスタマイズまたはデプロイする

デプロイまたはカスタマイズの詳細については、「[Azure Blueprints サンプル](https://docs.microsoft.com/azure/governance/blueprints/samples)」で、クラウド導入フレームワークの移行ランディング ゾーンのブループリントの参照サンプルを参照し、ダウンロードしてください。

ブループリントのサンプルは、ポータル内でも使用できます。 ブループリントを作成する方法の詳細については、[Azure Blueprints](./govern-org-compliance.md?tabs=azureblueprints#create-a-blueprint) に関するページを参照してください。

このブループリントや結果のランディング ゾーンに対するカスタマイズに関するガイダンスについては、「[ランディング ゾーンに関する考慮事項](../considerations/index.md)」の記事を参照してください。

## <a name="next-steps"></a>次の手順

移行ランディング ゾーンがデプロイされたら、Azure へのワークロードの移行を開始することができます。
最初のワークロードを移行するために必要なツールとプロセスのガイダンスについては、「[Azure 移行ガイド](../../migrate/azure-migration-guide/index.md)」を参照してください。

> [!div class="nextstepaction"]
> [Azure 移行ガイドを使用して最初のワークロードを移行する](../../migrate/azure-migration-guide/index.md)
