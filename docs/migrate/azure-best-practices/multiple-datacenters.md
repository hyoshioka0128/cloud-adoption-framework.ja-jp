---
title: 複数のデータセンター
description: 複数のデータセンター
author: BrianBlanchard
ms.author: brblanch
ms.date: 04/04/2019
ms.topic: guide
ms.service: cloud-adoption-framework
ms.subservice: migrate
ms.openlocfilehash: 9156df0b76f6edf1d249d5d724e0a5d0f4fd8e15
ms.sourcegitcommit: 58ea417a7df3318e3d1a76d3807cc4e7e3976f52
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2020
ms.locfileid: "78898133"
---
# <a name="multiple-datacenters"></a>複数のデータセンター

多くの場合、移行の範囲には、複数のデータ センターの移行が含まれます。 このガイダンスでは、[Azure 移行ガイド](../azure-migration-guide/index.md)の範囲を、複数のデータセンターに対応するように拡大しています。

## <a name="general-scope-expansion"></a>全般的な範囲の拡大

この範囲の拡大によって要求される作業の大半は、移行の前提条件、アクセス、および最適化プロセス中に発生します。

## <a name="suggested-prerequisites"></a>推奨される前提条件

移行を開始する前に、プロジェクト管理ツール内に、移行対象の各データセンターを表すエピックを作成します。 その後、移行を正当化するビジネス上の成果と動機を理解しておくことが重要です。 これらの動機を使用して、エピック (データセンター) の一覧に優先順位を付けることができます。 たとえば、移行の動機がリース契約を更新する前にデータセンターを移行したいという要望の場合は、リース契約の更新日に基づいて各エピックに優先順位を付けます。

各エピック内では、評価と移行の対象であるワークロードを、機能として管理します。 ワークロード内の各資産を、ユーザー ストーリーとして管理します。 各資産の評価、移行、最適化、昇格、セキュリティ保護、および管理を行うために必要な作業を、各資産のタスクとして表現します。

その後、クラウド導入チームによって、資産およびユーザー ストーリーを移行するために必要な一連のタスクで構成されるスプリントまたはイテレーションが構成されます。 次に、1 つまたは複数のワークロードまたは機能で構成されるリリースが運用環境に昇格されます。

## <a name="assess-process-changes"></a>プロセス変更の評価

複数のデータセンターに対応するための範囲の拡大での評価プロセスに対する最大の変更は、ワークロードの正確な記録と優先順位付け、およびデータセンター間の依存関係に関連しています。

### <a name="suggested-action-during-the-assess-process"></a>評価プロセス中に推奨されるアクション

**データセンター間の依存関係の評価:** [Azure Migrate の依存関係視覚化ツール](https://docs.microsoft.com/azure/migrate/concepts-dependency-visualization)は、依存関係の特定を支援できます。 移行前にこのツール セットを使用することは、一般的に適切なベスト プラクティスです。 ただし、グローバルな複雑さを処理する場合は、評価プロセスで実行する必要がある手順になります。 [依存関係のグループ化](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies)による視覚化は、ワークロードをサポートするために必要な資産の IP アドレスとポートを特定することを支援できます。

> [!IMPORTANT]
> 2 つの重要な注意事項:まず、資産の配置と IP アドレス スキーマを理解している該当分野の専門家が、セカンダリ データセンターに存在している資産を識別する必要があります。 次に、視覚化された下流方向の依存関係とクライアントの両方を評価して、双方向の依存関係を理解することが重要です。

## <a name="migrate-process-changes"></a>移行プロセスの変更

複数のデータセンターの移行は、データセンターの統合に似ています。 移行後は、クラウドが複数の資産の唯一のデータセンターになります。 移行プロセス中に発生する可能性がある範囲の拡大は、IP アドレスの検証と調整です。

### <a name="suggested-action-during-the-migrate-process"></a>移行プロセスで推奨されるアクション

クラウドへの移行の成功に大きく影響するアクティビティは次のとおりです。

- **ネットワークの競合の評価:** データセンターを単一のクラウド プロバイダーに統合すると、ネットワーク、DNS、またはその他の競合が発生する可能性があります。 移行中は、競合テストを行って、クラウドでホストされる運用システムでの中断の発生を回避することが重要です。
- **ルーティング テーブルの更新:** 多くの場合、ネットワークまたはデータセンターを統合する際は、ルーティング テーブルを変更する必要があります。

## <a name="optimize-and-promote-process-changes"></a>最適化および昇格プロセスの変更

最適化プロセス中に、追加テストが必要になる可能性があります。

### <a name="suggested-action-during-the-optimize-and-promote-process"></a>最適化および昇格プロセス中に推奨されるアクション

昇格に先立って、この範囲の拡大によるテスト レベルの追加を行うことが重要です。 テストでは、ルーティングまたはその他のネットワークの競合をテストすることが重要です。 さらに、デプロイされたアプリケーションを分離し、テストを再度実行して、すべての依存関係がクラウドに移行されていることを検証することが重要です。 ここでの分離は、デプロイされた環境と運用ネットワークの分離を意味しています。 これを行うことで、オンプレミスでまだ実行されている、見落とされた資産を把握できます。

## <a name="secure-and-manage-process-changes"></a>セキュリティ保護プロセスと管理プロセスの変更

セキュリティ保護プロセスと管理プロセスでは、この範囲の拡大による変更はありません。

## <a name="next-steps"></a>次のステップ

[範囲拡大チェックリスト](./index.md)に戻り、移行方法が完全に調整されていることを確認します。

> [!div class="nextstepaction"]
> [範囲拡大チェックリスト](./index.md)