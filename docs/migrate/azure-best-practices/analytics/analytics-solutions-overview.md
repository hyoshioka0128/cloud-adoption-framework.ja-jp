---
title: Analytics のソリューション
description: Azure のクラウド導入フレームワークを使用して、Teradata、Netezza、Exadata を使用した分析ソリューションについて学習します。
author: v-hanki
ms.author: brblanch
ms.date: 07/14/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: migrate
ms.openlocfilehash: 5d043cd95f8d32ffeaca6b510d04927dc51ce3e3
ms.sourcegitcommit: 9163a60a28ffce78ceb5dc8dc4fa1b83d7f56e6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2020
ms.locfileid: "86452655"
---
<!-- cSpell:ignore Netezza Teradata Exadata Giga GigaOM MPP -->

# <a name="analytics-solutions"></a>Analytics のソリューション

現在の市場オファリングでは、増大する組織のニーズに対応できません。 Teradata、Netezza、Oracle Exadata などのレガシ オンプレミス環境は、コストが高く、イノベーションに時間がかかり、柔軟性がありません。 オンプレミス システムの既存のユーザーは、新しい環境 (たとえば、クラウド、IaaS、PaaS) によって提供されるイノベーションを活用し、インフラストラクチャのメンテナンスやプラットフォーム開発などのタスクをクラウド プロバイダーに委任することを検討しています。 Microsoft Azure は、グローバルに使用できる、高度なセキュリティで保護されたスケーラブルなクラウド環境であり、サポート ツールと機能のエコシステム内に Azure Synapse Analytics が組み込まれています。

![Teradata の移行の設計とパフォーマンス](../../../_images/analytics/analytics-solutions-overview.png)

Azure Synapse Analytics では、超並列処理 (MPP) や自動メモリ内キャッシュなどの手法を使用して、最高のリレーショナル データベース パフォーマンスを実現しています。 このアプローチの結果は、独立したベンチマークで確認することができます。たとえば、[GigaOM](https://gigaom.com) によって最近実施されたでは、Azure Synapse Analytics が他の一般的なクラウド データ ウェアハウス サービスと比較されています。 この環境に既に移行している組織では、次のような多くの利点が確認されています。

- パフォーマンスと費用対効果の向上。
- 機敏性の向上と価値実現までの時間の短縮。
- より高速なサーバーのデプロイとアプリケーション開発。
- 実際の使用量に対してのみ課金される柔軟なスケーラビリティ。
- セキュリティとコンプライアンスの強化。
- ストレージとディザスター リカバリーのコスト削減。
- 全体的な TCO の削減とコスト管理 (運用コスト) の改善。

このような利点を最大限に活用するには、既存 (または新規) のデータとアプリケーションを Azure Synapse Analytics プラットフォームに移行する必要があります。 多くの組織では、このアプローチに、Teradata、Netezza、Exadata などのレガシ オンプレミス プラットフォームから既存のデータ ウェアハウスを移行することを含めています。 組織は、費用対効果が高く、短時間でイノベーションを達成し、スケーラブルで真に柔軟性のある分析オファリングを使用して、データ資産を最新化する必要があります。 Teradata、Netezza、Exadata に関する移行のベスト プラクティスについては、次のセクションを参照してください。

## <a name="next-steps"></a>次のステップ

- [Teradata の分析ソリューション](./analytics-solutions-teradata.md)
- [Netezza の分析ソリューション](./analytics-solutions-netezza.md)
- [Exadata の分析ソリューション](./analytics-solutions-exadata.md)
