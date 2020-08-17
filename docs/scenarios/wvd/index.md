---
title: Windows Virtual Desktop インスタンスを Azure に移行またはデプロイする
description: Windows Virtual Desktop インスタンスを Azure に移行またはデプロイします。
author: BrianBlanchard
ms.author: brblanch
ms.date: 07/01/2010
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: strategy
ms.openlocfilehash: 6463ed2d24446ae588676f45d0a4c349ad0330c0
ms.sourcegitcommit: 949b87bad28d32df84df190160089f01826f3a31
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2020
ms.locfileid: "88196466"
---
# <a name="migrate-or-deploy-windows-virtual-desktop-instances-to-azure"></a>Windows Virtual Desktop インスタンスを Azure に移行またはデプロイする

組織のエンドユーザーのデスクトップをクラウドへ移行することは、クラウド移行の一般的なシナリオです。 これを行うことで、従業員の生産性が向上し、組織のユーザー エクスペリエンスをサポートするためにさまざまなワークロードの移行が促進されます。

## <a name="strategy-and-motivations"></a>戦略と動機

仮想デスクトップの移行は、以下に示すような一般的な目標となる成果によって動機付けされます。

![仮想デスクトップの移行の動機を示す一覧。](../../_images/migrate/wvd/motivations.png)

- 組織において、PC、携帯電話、タブレット、またはブラウザーなど、IT チームが直接管理していない可能性があるものにまで生産性を拡張する必要がある。
- 従業員は、自分のデバイスから会社のデータやアプリケーションにアクセスする必要がある。
- ワークロードがクラウドに移行されると、従業員は、待機時間が短く、より最適化されたエクスペリエンスを実現するために、より多くのサポートが必要になる。
- 組織がリモート ワークをより効果的に拡大できるように、現在のまたは提案された仮想デスクトップのエクスペリエンスのコストを最適化する必要がある。
- IT チームは職場を変革することを望んでいる (これは、多くの場合、従業員のユーザー エクスペリエンスの変革から始まる)。

エンドユーザーのデスクトップをクラウド内に仮想化することで、チームでこれらの各成果を実現できます。

## <a name="approach-windows-virtual-desktop-refactor-and-modernization"></a>アプローチ:Windows Virtual Desktop のリファクターと最新化

この記事シリーズで説明されている方法では、既存の Citrix、VMware、またはリモート デスクトップ サービス ファームは、Windows Virtual Desktop と呼ばれるサービスとしてのプラットフォーム ソリューションによって最新化され、置き換えられます。

このシナリオでは、デスクトップ イメージは Azure に移行されるか、新しいイメージが生成されます。 同様に、ユーザー プロファイルは Azure に移行されるか、新しいプロファイルが作成されます。 ほとんどの場合、クライアント ソリューションは有効ですが、この移行作業ではほとんど変更されません。

![仮想デスクトップの移行シナリオの図。](../../_images/migrate/wvd/scenario-solution.png)

クラウドへの移行が完了すると、仮想デスクトップ ファームの管理のオーバーヘッドとコストがクラウドネイティブ ソリューションに置き換えられます。これにより、チームの仮想デスクトップ エクスペリエンスが管理されます。 チームが考慮する必要があるのは、デスクトップ イメージ、利用可能なアプリケーション、Azure Active Directory、ユーザー プロファイルのサポートだけです。

## <a name="next-steps"></a>次の手順

クラウド導入の取り組みの特定の要素に関するガイダンスについては、以下を参照してください。

- [Windows Virtual Desktop の移行またはデプロイの計画](./plan.md)
- [環境または Azure ランディング ゾーンを確認する](./ready.md)
- [Windows Virtual Desktop の概念実証を完了する](./proof-of-concept.md)
- [Windows Virtual Desktop の移行またはデプロイを評価する](./migrate-assess.md)
- [Windows Virtual Desktop インスタンスをデプロイまたは移行する](./migrate-deploy.md)
- [Windows Virtual Desktop のデプロイを運用環境にリリースする](./migrate-release.md)
