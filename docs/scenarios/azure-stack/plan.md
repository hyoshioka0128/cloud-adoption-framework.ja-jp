---
title: Azure Stack Hub 移行の計画
description: Azure Stack Hub 移行の計画
author: BrianBlanchard
ms.author: brblanch
ms.date: 05/19/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: plan
ms.openlocfilehash: fd756ec02305c2bdc9a5c6d106f6b5b4c170c60c
ms.sourcegitcommit: 9163a60a28ffce78ceb5dc8dc4fa1b83d7f56e6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2020
ms.locfileid: "86452751"
---
# <a name="plan-for-azure-stack-hub-migration"></a>Azure Stack Hub 移行の計画

この記事では、[クラウド戦略に Azure Stack を組み入れる](./index.md)方法について確認し、その記事の例に沿って進めていることを前提としています。

移行作業に直接移る前に、Azure および Azure Stack Hub に関する期待値を適切に設定することが重要です。 そうすることで、後からプロジェクトでの落とし穴や障壁を回避できます。 実装を成功させるために重要なのは、Azure を使用する局面と Azure Stack Hub を使用する局面についてよく理解していることです。

## <a name="digital-estate-alignment"></a>デジタル資産の配置

このためには、[デジタル資産の合理化](../../digital-estate/index.md)を完了するときに、いくつかの簡単な質問に回答することから始めます。

- 規制要件、データ主権、またはコンプライアンスのニーズなど、アプリケーションとデータをオンプレミスで維持する動機は何ですか。
- どの特定の規制またはコンプライアンスの要件が、データセンター内で維持するための意思決定に影響を及ぼしていますか。
- データのプライバシーは、データ移行にどう影響しますか。
- 移行は、最新化の取り組みとして定義されていますか。
- もしそうなら、移行後に必要な次のステップと目標を定義済みですか。
- SLA、RPO、RTO、および可用性の要件はどのようなものですか。

一部のワークロードでは、この情報によって、そのワークロードに対する Azure と Azure Stack Hub の価値に関する議論が促されます。

## <a name="assessment-best-practices"></a>評価のベスト プラクティス

[Azure Migrate によるデジタル資産の評価](../../plan/contoso-migration-assessment.md)のベスト プラクティスによって、お客様のデジタル資産におけるワークロードおよび資産の評価と配置をさらに推し進めることができます。 該当のベスト プラクティスでは、IT ポートフォリオ全体への分析情報が提供されます。 また、移行を進めるために容量、スケール、および構成に関する技術要件を特定するうえでも役立ちます。

適切な評価データを使用することで、Azure でのパブリックまたはプライベート クラウド プラットフォームという選択肢を評価する際に、チームはより賢明な選択を行い、優先度を明確に設定できます。

## <a name="planning-best-practices"></a>計画のベスト プラクティス

以下の資料は、Azure および Azure Stack Hub 間の違いについて理解するうえで役立ちます。

- [Azure Stack の概要とロードマップ](https://azure.microsoft.com/resources/videos/ignite-2018-azure-stack-overview-and-roadmap/)
- [Azure Stack の容量計画](https://docs.microsoft.com/azure/azure-stack/capacity-planning)
- [Azure Stack Hub データセンターの統合のチュートリアル](https://docs.microsoft.com/azure-stack/operator/azure-stack-customer-journey)
- [Azure Stack VM の機能](https://docs.microsoft.com/azure-stack/user/azure-stack-vm-considerations?view=azs-1910)

各ワークロードの最適なプラットフォームについて理解したら、その意思決定を[クラウド導入計画](../../plan/template.md)に組み入れて、パブリックおよびプライベート クラウドの移行を対応すべき 1 つの作業として管理できます。

## <a name="next-step-prepare-your-environment-for-azure-stack-hub-migrations"></a>次のステップ: Azure Stack Hub 移行のために環境を準備する

次の記事の一覧では、クラウド導入過程の特定の時点におけるガイダンスについて説明します。 次のステップとして、最初の[環境の準備](./ready.md)に関する記事に進んでください。

- [環境の準備](./ready.md)
- [Azure Stack Hub のワークロードを評価する](./migrate-assess.md)
- [Azure Stack Hub にワークロードをデプロイする](./migrate-deploy.md)
- [Azure Stack Hub のガバナンス](./govern.md)
- [Azure Stack Hub の管理](./manage.md)
