---
title: Windows Virtual Desktop の計画
description: Azure 向けのクラウド導入フレームワークを使用して、複雑さを軽減し、移行プロセスを標準化する仮想デスクトップ移行のベスト プラクティスについて説明します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 07/01/2010
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: migrate
ms.openlocfilehash: 5e9727db3772e2c04417677925a02f1e8c3d841e
ms.sourcegitcommit: 9163a60a28ffce78ceb5dc8dc4fa1b83d7f56e6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2020
ms.locfileid: "86452732"
---
# <a name="windows-virtual-desktop-planning"></a>Windows Virtual Desktop の計画

Windows Virtual Desktop とデプロイ シナリオは、他の移行作業と同じ移行手法に従っているため、移行ファクトリまたは既存の移行チームは、技術面以外の要件をほとんど変更せずに、プロセスを導入できます。

![クラウド導入フレームワーク移行モデル](../../_images/migrate/methodology.png)

## <a name="plan-your-migration"></a>移行を計画する

他の移行と同様に、チームはワークロードを評価し、ワークロードをデプロイしてから、それらのワークロードをエンド ユーザーにリリースします。 ただし、Windows Virtual Desktop には、ワークロードの評価時に Azure ランディング ゾーンの確認を必要とする特定の要件が含まれています。 また、最初のデプロイの前に概念実証が必要になります。

計画の作成については、Azure DevOps の既存の移行バックログに関する[クラウド導入計画 DevOps テンプレート](../../plan/template.md)をご覧ください。 このテンプレートを使用して、アクティビティの詳細な計画を作成できます。

## <a name="next-step-review-your-environment-or-azure-landing-zones"></a>次のステップ: 環境または Azure ランディング ゾーンを確認する

次の記事の一覧では、クラウド導入過程の特定の時点におけるガイダンスについて説明します。

- [環境または Azure ランディング ゾーンを確認する](./ready.md)
- [Windows Virtual Desktop の概念実証を作成する](./proof-of-concept.md)
- [Windows Virtual Desktop の移行またはデプロイを評価する](./migrate-assess.md)
- [Windows Virtual Desktop インスタンスをデプロイまたは移行する](./migrate-deploy.md)
- [Windows Virtual Desktop のデプロイを運用環境にリリースする](./migrate-release.md)
