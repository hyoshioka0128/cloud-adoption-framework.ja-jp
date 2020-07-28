---
title: 責任ある AI
description: 責任ある AI。
author: v-hanki
ms.author: janet
ms.date: 06/25/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: strategy
ms.openlocfilehash: c3fe645b9f87987a1b5b5f8a4161e1fba87a1855
ms.sourcegitcommit: 9163a60a28ffce78ceb5dc8dc4fa1b83d7f56e6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2020
ms.locfileid: "86452729"
---
<!-- docsTest:ignore InterpretML FairLearn -->

# <a name="responsible-ai"></a>責任ある AI

Microsoft は、人を第一に考える倫理原則に基づいて、AI の発展に取り組んでいます。 Microsoft は、この取り組みにおいてお客様と協力したいと考えています。

## <a name="responsible-ai-principles"></a>責任ある AI の原則

AI ソリューションを実装する際、ソリューションで次の AI の原則を考慮することをお勧めします。

- 公平性:AI システムでは、すべてのユーザーを公平に扱う必要があります
- 信頼性と安全性:AI システムを確実かつ安全に実行する必要があります
- プライバシーとセキュリティ:AI システムは安全で、プライバシーを尊重する必要があります
- 包括性:"AI システムはすべての人を支援し、ユーザーを関与させる必要があります
- 透明性: AI システムは理解可能である必要があります
- 説明責任:すべてのユーザーが AI システムについて責任を持ちます

## <a name="establish-a-responsible-ai-strategy"></a>責任ある AI 戦略を確立する

組織の価値観に基づき、独自の責任ある AI 戦略と原則を策定する方法について説明します。

- [AI ビジネス スクールの概要](https://www.microsoft.com/ai/ai-business-school?SilentAuth=1#primaryR7)

## <a name="guidelines-to-develop-ai-responsibly"></a>責任を持って AI を開発するためのガイドライン

責任ある AI を実践する際、以下のガイドラインはソフトウェア開発ライフサイクルを通して潜在的な問題を予測して対処するのに役立ちます。

- [人間と AI の相互作用のガイドライン](https://aka.ms/aiguidelines)
- [会話型 AI のガイドライン](https://www.microsoft.com/research/publication/responsible-bots/)
- [包括的設計のガイドライン](https://www.microsoft.com/design/inclusive/)
- [AI の公平性のチェックリスト](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE4t6dA)
- [データセットのデータシートのテンプレート](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE4t8QB)
- [AI セキュリティ エンジニアリングのガイダンス](https://blogs.microsoft.com/on-the-issues/2019/12/06/ai-machine-learning-security/)

## <a name="tools-for-responsible-ai"></a>責任ある AI のツール

調査、オープン ソース プロジェクト、Azure Machine Learning はすべて、開発者とデータ サイエンティストが AI システムを理解し、保護し、制御できるようにすることを目的として設計されています。

- **理解**:AI システムは、さまざまな理由で予期しない動作が発生する可能性があります。 これらのソフトウェア ツールは、AI システムの動作を理解し、ニーズに合わせて調整するのに役立ちます。 たとえば、InterpretML、Fairlearn などです。
- **保護**:AI システムはデータに依存しています。 これらのソフトウェア ツールは、プライバシーを守り、機密性を保証することで、データを保護するのに役立ちます。 たとえば、ML の Confidential Computing、WhiteNoise の差分プライバシー、SEAL の準同型暗号、Presidio などです。
- **制御**:責任ある AI は、開発サイクルを通じてガバナンスと制御が必要です。 Azure Machine Learning を使用すると、規制要件を満たすために、追跡可能性、系統、制御を向上させる監査証跡を作成できるようになります。 たとえば、監査証跡や追跡可能性などです。

## <a name="next-steps"></a>次の手順

責任あるソリューションの開発をサポートするためのその他のリソースについては、以下を参照してください。

- [責任ある AI の概要](https://www.microsoft.com/ai/responsible-ai?activetab=pivot1:primaryr6)
- [責任ある AI リソース](https://www.microsoft.com/ai/responsible-ai-resources)
- [責任あるボット:対話型 AI に関する 10 の開発者用ガイドライン](https://www.microsoft.com/research/publication/responsible-bots/)
