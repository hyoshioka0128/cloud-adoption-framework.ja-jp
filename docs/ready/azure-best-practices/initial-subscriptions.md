---
title: Azure の初期サブスクリプションを作成する
description: 初期サブスクリプションを作成して、Azure の導入を開始します。
author: alexbuckgit
ms.author: abuck
ms.date: 05/20/2019
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.custom: internal
ms.openlocfilehash: 4d78f100772e1779f8e8385ca413b979996403aa
ms.sourcegitcommit: b8f8b7631aabaab28e9705934bf67dad15e3a179
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101789679"
---
# <a name="create-your-initial-azure-subscriptions"></a>Azure の初期サブスクリプションを作成する

サブスクリプションの初期セットを作成して、Azure の導入を開始します。 初期要件に基づいて、どのサブスクリプションから始める必要があるかについて説明します。

## <a name="your-first-two-subscriptions"></a>最初の 2 つのサブスクリプション

最初に、次の 2 つのサブスクリプションを作成します。

- 運用環境のワークロードを格納するための Azure サブスクリプションを 1 つ作成します。
- より低価格の [Azure Dev/Test オファー](https://azure.microsoft.com/pricing/dev-test)を使用して、非運用環境として機能する 2 つ目のサブスクリプションを作成します。

!["運用" と "非運用" のラベルが付いたボックスの横にキーが表示された初期サブスクリプション モデル](../../_images/ready/initial-subscription-model.png)
_図 1:"運用" と "非運用" のラベルが付いたボックスの横にキーが表示された初期サブスクリプション モデル_

この方法には多くの利点があります。

- 運用と非運用環境に別々のサブスクリプションを使用すると、リソースの管理をよりシンプルで安全にする境界が作成されます。
- 非運用ワークロード向けに、Azure Dev/Test サブスクリプション オファリングが提供されています。 これらのオファリングでは、Azure のサービスとソフトウェア ライセンスを割引料金で利用できます。
- 運用環境と非運用環境では、Azure ポリシーのセットが異なることが多くなります。 サブスクリプションを分けることで、サブスクリプション レベルで独自のポリシー を簡単に適用できます。
- 非運用サブスクリプションで、特定の種類の Azure リソースをテスト目的で許可できます。 それらのリソース プロバイダーは、運用環境で使用可能にしなくても、非運用サブスクリプションで有効にすることができます。
- Azure Dev/Test サブスクリプションは、分離されたサンドボックス環境として使用できます。 これらのサンドボックスを使用すると、管理者と開発者は、Azure リソースのセット全体の構築と破棄を短時間で行うことができます。 この分離は、データ保護やセキュリティ上の問題に対応する場合にも役立ちます。
- 定義する許容可能なコストのしきい値は、運用環境と非運用環境で異なる可能性があります。

## <a name="sandbox-subscriptions"></a>サンドボックス サブスクリプション

イノベーションに関する目標がクラウド導入戦略の一部となっている場合は、1 つ以上のサンドボックス サブスクリプションを作成することを検討してください。 セキュリティ ポリシーを適用して、これらのテスト サブスクリプションを運用環境および非運用環境から分離できます。 ユーザーは、これらの分離された環境で Azure の機能を簡単に試すことができます。 Azure Dev/Test オファーを使用して、これらのサブスクリプションを作成します。

!["運用"、"非運用"、"サンドボックス" のラベルが付いたボックスの横にキーが表示された初期サブスクリプション モデル](../../_images/ready/initial-subscription-model-with-sandboxes.png)
_図 2:サンドボックス サブスクリプション付きサブスクリプション モデル_

## <a name="shared-services-subscription"></a>共有サービス サブスクリプション

**24 か月以内にクラウドで 1,000 を超える VM またはコンピューティング インスタンス** をホストすることを計画している場合は、共有サービスをホストするために別の Azure サブスクリプションを作成してください。 これにより、最終状態のエンタープライズ アーキテクチャをサポートできる状態になります。

!["運用" と "共有サービス" のラベルが付いたボックスの横にキーが表示された初期サブスクリプション モデル](../../_images/ready/initial-subscription-model-with-shared-services.png)
_図 3:共有サービス付きサブスクリプション モデル_

## <a name="next-steps"></a>次のステップ

要件を満たすために[追加の Azure サブスクリプションを作成する](./scale-subscriptions.md)理由を確認します。

> [!div class="nextstepaction"]
> [追加のサブスクリプションを作成して、Azure 環境をスケーリングする](./scale-subscriptions.md)
