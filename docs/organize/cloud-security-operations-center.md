---
title: クラウド セキュリティ オペレーション センターの機能
description: クラウドのセキュリティに必要な機能を説明します
author: JanetCThomas
ms.author: janet
ms.service: cloud-adoption-framework
ms.subservice: organize
ms.topic: conceptual
ms.date: 04/30/2020
ms.openlocfilehash: 7c3044e4d5e970b9f78bb46bda2aadf11273e556
ms.sourcegitcommit: 60d8b863d431b5d7c005f2f14488620b6c4c49be
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2020
ms.locfileid: "83230227"
---
<!-- cSpell:ignore CISO MTTA MTTR SIEM NIST SOCs CDOC -->

# <a name="functions-of-a-cloud-security-operations-center-soc"></a>クラウド セキュリティ オペレーション センター (SOC) の機能

クラウド セキュリティ オペレーション センター (SOC) の主な目的は、エンタープライズ資産に対するアクティブな攻撃を検出、対応、復旧することです。

SOC が成熟するにつれて、セキュリティ運用は次のようになります。

- ツールによって検出された攻撃にリアクティブに対応します
- 過去のリアクティブな検出から漏れた攻撃を事前に発見します

## <a name="modernization"></a>最新化

脅威の検出と対応については、現在、あらゆるレベルで重要な近代化が行われています。

- **ビジネス リスク管理への昇格:** SOC は、組織のビジネ スリスク管理の重要なコンポーネントになっています。
- **メトリックと目標:** SOC の有効性の追跡は、"検出までの時間" から次の主要な指標に進化しています。
  - 平均応答時間 (MTTA) による*応答性*
  - 平均修復時間 (MTTR) による*修復速度*
- **テクノロジの進化:** SOC テクノロジは、SIEM 内のログの静的分析だけの使用から、特殊なツールや高度な分析手法の使用の追加へと、発展しています。 これにより、SIEM の広範なビューを補完する高品質のアラートと調査エクスペリエンスを提供する、資産に関する深い分析情報が得られます。 どちらの種類のツールでも、悪意のある攻撃者である可能性がある異常なアクションの特定と優先順位付けのために、AI と機械学習、動作分析、および統合脅威インテリジェンス (TI) の使用が増えています。
- **脅威ハンティング:** SOC では、高度な攻撃者を事前に特定し、最前線のアナリストのキューから雑音の多いアラートを除くために、仮説主導の脅威ハンティングが追加されています。
- **インシデント管理:** 法律、コミュニケーション、およびその他のチームでインシデントの非技術的要素を調整するため、規範が形式化されています。
**内部コンテキストの統合:** ユーザー アカウントとデバイスの相対的なリスク スコア、データとアプリケーションの機密性、およびしっかり防御するための主要なセキュリティ分離境界などの SOC アクティビティの優先順位付けのため。

 詳細については、次を参照してください。

- [戦略とアーキテクチャの標準&mdash;セキュリティ操作](https://docs.microsoft.com/security/compass/security-operations-videos-and-decks)
- [CISO ワークショップ モジュール 4b: 脅威防止戦略](https://docs.microsoft.com/security/ciso-workshop/ciso-workshop-module-4b)
- サイバー防御オペレーション センター (CDOC) ブログ シリーズ [パート 1](https://www.microsoft.com/security/blog/2019/02/21/lessons-learned-from-the-microsoft-soc-part-1-organization/)、[パート 2a](https://www.microsoft.com/security/blog/2019/04/23/lessons-learned-microsoft-soc-part-2-organizing-people/)、[パート 2b](https://www.microsoft.com/security/blog/2019/06/06/lessons-learned-from-the-microsoft-soc-part-2b-career-paths-and-readiness/)、[パート 3a](https://www.microsoft.com/security/blog/2019/10/07/ciso-series-lessons-learned-from-the-microsoft-soc-part-3a-choosing-soc-tools/)、[パート 3b](https://www.microsoft.com/security/blog/2019/12/23/ciso-series-lessons-learned-from-the-microsoft-soc-part-3b-a-day-in-the-life)
- [NIST コンピューター セキュリティ インシデント処理ガイド](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)
- [サイバーセキュリティ イベント復旧のための NIST ガイド](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-184.pdf)

## <a name="team-composition-and-key-relationships"></a>チームの構成と重要な関係

クラウド セキュリティ オペレーション センターは、通常、次の種類の役割で構成されています。

- IT 運用 (常に緊密に連絡)
- 脅威インテリジェンス
- セキュリティのアーキテクチャ
- インサイダー リスク プログラム
- 法務と人事
- コミュニケーション チーム
- リスク組織 (存在する場合)
- 業界固有の関連、コミュニティ、およびベンダー (インシデント発生前)

## <a name="next-steps"></a>次のステップ

[セキュリティ アーキテクチャ](./cloud-security-architecture.md)の機能を確認します。
