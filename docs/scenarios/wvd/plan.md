---
title: Windows Virtual Desktop の計画
description: Azure 向けクラウド導入フレームワークを使用し、Windows Virtual Desktop 移行のベスト プラクティスについて説明します。複雑さを軽減し、移行プロセスを標準化できます。
author: BrianBlanchard
ms.author: brblanch
ms.date: 07/01/2010
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: migrate
ms.openlocfilehash: f2c609be969dc39a61a00ccccab6b897110a731f
ms.sourcegitcommit: 12fa4597633ca8e04efbae7d0bd7526d3581618e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88662398"
---
# <a name="windows-virtual-desktop-planning"></a>Windows Virtual Desktop の計画

Windows Virtual Desktop とデプロイ シナリオでは、他の移行作業と同じ移行手法に従います。 一貫性のあるこの手法により、移行ファクトリや既存の移行チームは、非技術要件をほとんど変更せずにこのプロセスを採用できます。

![クラウド導入フレームワークの移行手法。](../../_images/migrate/methodology.png)

## <a name="plan-your-migration"></a>移行を計画する

他の移行と同様に、チームはワークロードを評価し、デプロイし、エンド ユーザーにリリースします。 ただし、Windows Virtual Desktop には、ワークロードの評価時に Azure ランディング ゾーンの確認を必要とする特定の要件が含まれています。 このプロセスでは、最初のデプロイの前に概念実証も必要になります。

計画を作成するには、Azure DevOps の既存の移行バックログに関する[クラウド導入計画 DevOps テンプレート](../../plan/template.md)をご覧ください。 このテンプレートを使用し、アクティビティの詳細な計画を作成します。

## <a name="next-steps"></a>次のステップ

クラウド導入の取り組みの特定の要素に関するガイダンスについては、以下を参照してください。

- [環境または Azure ランディング ゾーンを確認する](./ready.md)
- [Windows Virtual Desktop の概念実証を作成する](./proof-of-concept.md)
- [Windows Virtual Desktop の移行またはデプロイを評価する](./migrate-assess.md)
- [Windows Virtual Desktop インスタンスをデプロイまたは移行する](./migrate-deploy.md)
- [Windows Virtual Desktop のデプロイを運用環境にリリースする](./migrate-release.md)
