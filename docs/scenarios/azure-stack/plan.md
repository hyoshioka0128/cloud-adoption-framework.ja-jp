---
title: Azure Stack Hub 移行の計画
description: Azure Stack Hub 移行を計画します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 05/19/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: plan
ms.openlocfilehash: 64e1ae5cbe6f6ed31575614551f1dafc3275f1a0
ms.sourcegitcommit: 011525720bd9e2d9bcf03a76f371c4fc68092c45
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88572665"
---
# <a name="plan-your-azure-stack-hub-migration"></a>Azure Stack Hub 移行の計画

この記事では、[クラウド戦略に Azure Stack を組み入れる](./index.md)方法について確認し、その記事の例に沿って進めていることを前提としています。

組織での移行作業に直接移る前に、Azure および Azure Stack Hub に関する期待値を適切に設定することが重要です。 そうすることで、後になってプロジェクトで落とし穴や障壁が発生するのを防ぐことができます。 実装を成功させるために重要なのは、Azure を使用する局面と Azure Stack Hub を使用する局面についてよく理解していることです。

## <a name="digital-estate-alignment"></a>デジタル資産の配置

組織のデジタル資産の配置は、[デジタル資産の合理化](../../digital-estate/index.md)の完了時にいくつかの簡単な質問に答えるところから始まります。

- 規制要件、データ主権、またはコンプライアンスのニーズなど、アプリケーションとデータをオンプレミスで維持する動機は何ですか。
- 規制またはコンプライアンスに関する特定のどの要件が、データセンター内で維持するという意思決定に影響していますか。
- データのプライバシーは、データの移行にどのように影響しますか。
- 移行は、最新化の取り組みとして定義されていますか。
- もしそうなら、移行後に必要な次のステップと目標を定義済みですか。
- サービス レベル アグリーメント、目標復旧ポイント、目標復旧時間、および可用性の要件は何ですか。

一部のワークロードでは、これらの質問に対して答えることで、そのワークロードでの Azure の価値と Azure Stack Hub の価値の比較についての議論が促されます。

## <a name="assessment-best-practices"></a>評価のベスト プラクティス

[Azure Migrate によるデジタル資産の評価](../../plan/contoso-migration-assessment.md)のベスト プラクティスを適用することによって、デジタル資産におけるワークロードおよび資産の評価と配置をさらに推し進めることができます。 このベスト プラクティスによって、IT ポートフォリオ全体への分析情報がもたらされます。 また、移行を進めるために容量、スケール、および構成に関する技術要件を特定するうえでも役立ちます。

適切な評価データを使用することで、クラウド導入チームは、Azure でパブリックまたはプライベート クラウド プラットフォームというオプションを評価するときに、より賢明な選択とより明確な優先順位付けを行うことができます。

## <a name="planning-best-practices"></a>計画のベスト プラクティス

次のリソースは、Azure と Azure Stack Hub の違いについて理解するうえで役立ちます。

- [Azure Stack の概要とロードマップ](https://azure.microsoft.com/resources/videos/ignite-2018-azure-stack-overview-and-roadmap/)
- [Azure Stack の容量計画](/azure/azure-stack/capacity-planning)
- [Azure Stack Hub データセンターの統合のチュートリアル](/azure-stack/operator/azure-stack-customer-journey)
- [Azure Stack の仮想マシン機能](/azure-stack/user/azure-stack-vm-considerations?view=azs-1910)

各ワークロードの最適なプラットフォームについて理解したら、その意思決定を[クラウド導入計画](../../plan/template.md)に組み入れて、パブリック クラウドとプライベート クラウドの移行を、対応すべき 1 つの作業として管理できます。

## <a name="next-steps"></a>次のステップ

クラウド導入の取り組みの特定の要素に関するガイダンスについては、以下を参照してください。

- [Azure Stack Hub 移行のためのクラウド環境を準備する](./ready.md)
- [Azure Stack Hub のワークロードを評価する](./migrate-assess.md)
- [Azure Stack Hub にワークロードをデプロイする](./migrate-deploy.md)
- [Azure Stack Hub のガバナンス](./govern.md)
- [Azure Stack Hub の管理](./manage.md)
