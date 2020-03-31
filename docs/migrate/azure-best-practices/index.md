---
title: Azure への移行のベスト プラクティス
description: Azure のクラウド導入フレームワークを使用して、クラウド移行のベスト プラクティスに合わせるために必要なツールを実装する方法について学習します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: migrate
ms.openlocfilehash: 22f2718775b194196f65672f0cbc1712d0a3a4ac
ms.sourcegitcommit: afe10f97fc0e0402a881fdfa55dadebd3aca75ab
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2020
ms.locfileid: "80433543"
---
# <a name="best-practices-for-cloud-migration"></a>クラウド移行のベスト プラクティス

クラウド導入フレームワークの [Azure 移行ガイド](../azure-migration-guide/index.md)は、Azure への移行に関心がある場合の開始点としてお勧めです。 このガイドでは、仮想マシンをクラウドに移行するための一連のツールおよび基本的なアプローチについて説明します。 クラウド導入フレームワークのこのセクションでは、基本的なクラウドネイティブ ツール以外の多くのベスト プラクティスについて説明します。

## <a name="cloud-migration-best-practice-checklist"></a>クラウド移行のベスト プラクティスのチェックリスト

以下のチェックリストは、移行の範囲が [Azure 移行ガイド](../azure-migration-guide/index.md)の範囲を超える可能性のある、一般的に複雑な領域をまとめています。

### <a name="business-driven-scope-expansion"></a>ビジネスによる範囲の拡大

- **[グローバル市場をサポートする](./multiple-regions.md):** 業務は、異なるデータ主権要件がある複数の地域で行われています。 その要件を満たすには、移行段階の前提条件の確認と資産の分散に考慮事項を追加する必要があります。

### <a name="technology-driven-scope-expansion"></a>テクノロジによる範囲の拡大

- **[VMware の移行](./vmware-host.md):** VMware ホストを移行すると、移行プロセス全体を高速化できます。 移行された各 VMware ホストでは、リフトアンドシフト アプローチを使用して、複数のワークロードをクラウドに移動できます。 移行後は、これらの VM とワークロードを VMware に維持したり、最新のクラウド機能に移行したりすることができます。
- **[SQL Server の移行](./sql-migration.md):** SQL Server を移行すると、移行プロセス全体を高速化できます。 移行された各 SQL Server では、複数のデータベースとサービスを移動できるため、複数のワークロードを加速させる可能性があります。
- **[複数のデータセンター](./multiple-datacenters.md):** 複数のデータセンターの移行は、かなり複雑になります。 評価、移行、最適化、管理の各プロセスでは、より複雑な環境用に準備するために、追加の考慮事項について説明します。
- **[データ要件がネットワーク容量を超える](./network-capacity-exceeded.md):** 企業がクラウドへの移行を選択する理由は、多くの場合、既存のデータセンターの容量、速度、または安定性に満足できなくなったためです。 残念ながら、こうした同じ制約によって移行プロセスが複雑になり、評価および移行プロセス段階で追加の計画が必要になります。
- **[ガバナンスまたはコンプライアンスの戦略](./governance-or-compliance.md):** ガバナンスとコンプライアンスが移行の成功に不可欠な場合は、IT ガバナンス チームとクラウド導入チームの間で追加の調整が必要です。

これらの複雑さのいずれかがシナリオに含まれる場合、クラウド導入フレームワークのこのセクションでは、移行プロセスの範囲を適切に調整するために必要なガイダンスの種類を提供します。

これらの各シナリオには、クラウド導入フレームワークのこのセクションのさまざまな記事で対応しています。

## <a name="next-steps"></a>次のステップ

具体的なニーズや範囲変更に対応するには、左側の目次を参照してください。 また、一覧の最初の範囲の拡大である「[グローバル市場をサポートする](./multiple-regions.md)」は、このようなシナリオを検討する際の出発点としてお勧めです。

> [!div class="nextstepaction"]
> [グローバル市場をサポートする](./multiple-regions.md)
