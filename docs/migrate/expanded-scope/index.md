---
title: クラウド移行の拡大された範囲のチェックリスト
description: クラウド移行の拡大された範囲のチェックリスト
author: BrianBlanchard
ms.author: brblanch
ms.date: 02/25/2020
ms.topic: guide
ms.service: cloud-adoption-framework
ms.subservice: migrate
ms.openlocfilehash: 730da49f910c34bf2bd94b8766cb292520201e50
ms.sourcegitcommit: 58ea417a7df3318e3d1a76d3807cc4e7e3976f52
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2020
ms.locfileid: "78892635"
---
# <a name="expanded-scope-for-cloud-migration"></a>クラウド移行の拡大された範囲

クラウド導入フレームワークの [Azure 移行ガイド](../azure-migration-guide/index.md)は、Azure への再ホスト移行に関心がある方にお勧めの出発点であり、"リフトとシフト" 移行とも呼ばれます。 このガイドでは、仮想マシンをクラウドに移行するための一連の前提条件、ツール、およびアプローチについて説明します。

このガイドはこの種の移行に慣れるために効果的な基準ですが、いくつかの前提条件があります。 これらの前提条件では、クラウド導入フレームワークの多くの読者にガイドを合わせるために、移行へのアプローチを簡略化しています。 クラウド導入フレームワークのこのセクションでは、これらの前提条件に該当しない場合の取り組みのガイドに役立つ、拡大された範囲の移行シナリオについて説明します。

## <a name="cloud-migration-expanded-scope-checklist"></a>クラウド移行の拡大された範囲のチェックリスト

以下のチェックリストは、移行の範囲が [Azure 移行ガイド](../azure-migration-guide/index.md)の範囲を超える可能性のある、一般的に複雑な領域をまとめています。

### <a name="business-driven-scope-expansion"></a>ビジネスによる範囲の拡大

- **[グローバル市場をサポートする](../azure-best-practices/multiple-regions.md):** 業務は、異なるデータ主権要件がある複数の地域で行われています。 その要件を満たすには、移行段階の前提条件の確認と資産の分散に考慮事項を追加する必要があります。

### <a name="technology-driven-scope-expansion"></a>テクノロジによる範囲の拡大

- **[VMware の移行](../azure-best-practices/vmware-host.md):** VMware ホストを移行すると、移行プロセス全体を高速化できます。 移行された各 VMware ホストでは、リフトアンドシフト アプローチを使用して、複数のワークロードをクラウドに移動できます。 移行後は、これらの VM とワークロードを VMware に維持したり、最新のクラウド機能に移行したりすることができます。
- **[SQL Server の移行](../azure-best-practices/sql-migration.md):** SQL Server を移行すると、移行プロセス全体を高速化できます。 移行された各 SQL Server では、複数のデータベースとサービスを移動できるため、複数のワークロードを加速させる可能性があります。
- **[複数のデータセンター](../azure-best-practices/multiple-datacenters.md):** 複数のデータセンターの移行は、かなり複雑になります。 評価、移行、最適化、管理の各プロセスでは、より複雑な環境用に準備するために、追加の考慮事項について説明します。
- **[データ要件がネットワーク容量を超える](../azure-best-practices/network-capacity-exceeded.md):** 企業がクラウドへの移行を選択する理由は、多くの場合、既存のデータセンターの容量、速度、または安定性に満足できなくなったためです。 残念ながら、こうした同じ制約によって移行プロセスが複雑になり、評価および移行プロセス段階で追加の計画が必要になります。
- **[ガバナンスまたはコンプライアンスの戦略](../azure-best-practices/governance-or-compliance.md):** ガバナンスとコンプライアンスが移行の成功に不可欠な場合は、IT ガバナンス チームとクラウド導入チームの間で追加の調整が必要です。

これらの複雑さのいずれかがシナリオに含まれる場合、クラウド導入フレームワークのこのセクションでは、移行プロセスの範囲を適切に調整するために必要なガイダンスの種類を提供します。

これらの各シナリオには、クラウド導入フレームワークのこのセクションのさまざまな記事で対応しています。

## <a name="next-steps"></a>次のステップ

具体的なニーズや範囲変更に対応するには、左側の目次を参照してください。 また、一覧の最初の範囲の拡大である「[グローバル市場をサポートする](../azure-best-practices/multiple-regions.md)」は、このようなシナリオを検討する際の出発点としてお勧めです。

> [!div class="nextstepaction"]
> [グローバル市場をサポートする](../azure-best-practices/multiple-regions.md)
