---
title: 責任ある AI
description: これを実現するための原則、ガイドライン、ツールなど、Microsoft が倫理的 AI の開発をどのように捉えているかについて説明します。
author: v-hanki
ms.author: janet
ms.date: 06/25/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: strategy
ms.openlocfilehash: 048a19ac39b9488ad8ea68c269637d5052f00eee
ms.sourcegitcommit: d31a9043d1ae9283ed126bf118ca26d1d18d6948
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/10/2020
ms.locfileid: "88040900"
---
<!-- docsTest:ignore InterpretML FairLearn -->

# <a name="responsible-ai"></a>責任ある AI

Microsoft は、人を第一に考える倫理原則に基づいて、AI の発展に取り組んでいます。 Microsoft は、この取り組みにおいてお客様と協力したいと考えています。

## <a name="responsible-ai-principles"></a>責任ある AI の原則

AI ソリューションを実装する際、ソリューションで次の AI の原則を考慮することをお勧めします。

- 公平性:AI システムでは、すべてのユーザーを公平に扱う必要があります。
- 信頼性と安全性:AI システムを確実かつ安全に実行する必要があります。
- プライバシーとセキュリティ:AI システムは安全で、プライバシーを尊重する必要があります。
- 包括性:AI システムはすべての人を支援し、ユーザーを関与させる必要があります。
- 透明性: AI システムは理解可能である必要があります。
- 説明責任:すべてのユーザーが AI システムについて責任を持ちます。

## <a name="establish-a-responsible-ai-strategy"></a>責任ある AI 戦略を確立する

組織の価値観に基づき、独自の責任ある AI 戦略と原則を策定する方法について説明します。

- [AI ビジネス スクールの概要](https://www.microsoft.com/ai/ai-business-school?SilentAuth=1#primaryR7)

## <a name="guidelines-to-develop-ai-responsibly"></a>責任を持って AI を開発するためのガイドライン

ソフトウェア開発ライフサイクルを通して潜在的な問題を予測して対処できるように設計されたこれらのガイドラインを使用して、責任ある AI を実践します。

- [人間と AI の相互作用のガイドライン](https://aka.ms/aiguidelines)
- [会話型 AI のガイドライン](https://www.microsoft.com/research/publication/responsible-bots/)
- [包括的設計のガイドライン](https://www.microsoft.com/design/inclusive/)
- [AI の公平性のチェックリスト](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE4t6dA)
- [データセットのデータシートのテンプレート](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE4t8QB)
- [AI セキュリティ エンジニアリングのガイダンス](https://blogs.microsoft.com/on-the-issues/2019/12/06/ai-machine-learning-security/)

## <a name="tools-for-responsible-ai"></a>責任ある AI のツール

開発者やデータ サイエンティストが AI システムを理解、保護、および制御するのに役立つツールが用意されています。 これらのツールは、Azure Machine Learning、オープンソース プロジェクト、調査など、さまざまなソースから入手できます。

- **理解**:AI システムは、さまざまな理由で予期しない動作が発生する可能性があります。 ソフトウェア ツールは、AI システムの動作を理解し、ニーズに合わせて調整するのに役立ちます。 この種のツールの例には、InterpretML や Fairlearn などがあります。
- **保護**:AI システムはデータに依存しています。 ソフトウェア ツールは、プライバシーを守り、機密性を保証することで、データを保護するのに役立ちます。 この種のツールの例には、機械学習用の機密コンピューティング、WhiteNoise の差分プライバシー、SEAL の準同型暗号、Presidio などがあります。
- **制御**:責任ある AI は、開発サイクルを通じてガバナンスと制御が必要です。 Azure Machine Learning を使用すると、追跡、系統、および制御を向上させるために監査証跡を有効にして、規制要件を満たすことができます。 例には、監査証跡や追跡可能性などがあります。

## <a name="next-steps"></a>次の手順

責任あるソリューションの開発をサポートするためのその他のリソースについては、以下を参照してください。

- [責任ある AI の概要](https://www.microsoft.com/ai/responsible-ai?activetab=pivot1:primaryr6)
- [責任ある AI リソース](https://www.microsoft.com/ai/responsible-ai-resources)
- [責任あるボット:対話型 AI に関する 10 の開発者用ガイドライン](https://www.microsoft.com/research/publication/responsible-bots/)
