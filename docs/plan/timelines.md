---
title: クラウド導入計画のタイムライン
description: クラウド導入計画に基づいてタイムラインを見積もる方法を、Azure のクラウド導入フレームワークを使用して学習します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 07/01/2019
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: plan
ms.openlocfilehash: e5407f2f78942d22c5fa07b277f600d5440ddc04
ms.sourcegitcommit: 60d8b863d431b5d7c005f2f14488620b6c4c49be
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2020
ms.locfileid: "83214023"
---
# <a name="timelines-in-a-cloud-adoption-plan"></a>クラウド導入計画のタイムライン

このシリーズの前の記事では、ワークロードとタスクが[リリースとイテレーション](./iteration-paths.md)に割り当てられました。 これらの割り当てによって、この記事内のタイムラインの見積もりがフィードされます。

作業分解構造は、シーケンシャルなプロジェクト管理ツール内でよく使用されます。 これらは、依存するタスクが一定期間にわたってどのように完了するかを表します。 このような構造は、タスクが本質的にシーケンシャルである場合にうまく機能します。 クラウド導入で見られるタスク内の相互依存関係によって、このような構造の管理が難しくなります。 このギャップを埋めるために、複雑さを隠すことで、イテレーション パスの割り当てに基づいてタイムラインを見積もることができます。

## <a name="estimate-timelines"></a>タイムラインの見積もり

タイムラインを開発するには、リリースから開始します。 これらのリリース目標により、ビジネスへの影響について目標とする日付が作成されます。 イテレーションは、これらのリリースを特定の期間に合わせることを支援します。

タイムライン内によりきめ細かなマイルストーンが必要な場合は、イテレーションの割り当てを使用してマイルストーンを示します。 この割り当てを行うには、ワークロード関連タスクの最後のインスタンスが最終的なマイルストーンとして機能できることを想定します。 また、最後のタスクにマイルストーンとしてタグを付けることはチームにとって一般的です。

任意の細分性レベルでは、イテレーションの最終日を各マイルストーンの日付として使用します。 これにより、ワークロードの導入の完了が特定の日付に結び付けられます。 この日付は、スプレッドシートまたは Microsoft Project のようなシーケンシャルなプロジェクト管理ツールで追跡できます。

## <a name="delivery-plans-in-azure-devops"></a>Azure DevOps でのデリバリー計画

<!-- docsTest:ignore "Microsoft Delivery Plans" -->

Azure DevOps を使用してクラウド導入計画を管理している場合は、[Microsoft Delivery Plans 拡張機能](https://marketplace.visualstudio.com/items?itemname=ms.vss-plans)の使用を検討してください。 この拡張機能を使用すると、イテレーションとリリースの割り当てに基づくタイムラインをすばやく視覚的に表現できます。
