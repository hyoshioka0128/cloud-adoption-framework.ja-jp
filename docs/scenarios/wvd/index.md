---
title: Windows Virtual Desktop インスタンスを Azure に移行またはデプロイする
description: Windows Virtual Desktop インスタンスを Azure に移行またはデプロイします。
author: BrianBlanchard
ms.author: brblanch
ms.date: 07/01/2010
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: strategy
ms.openlocfilehash: 9c28afd9aa3bed856f66fb96e13a07b196f7dfdd
ms.sourcegitcommit: 9163a60a28ffce78ceb5dc8dc4fa1b83d7f56e6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2020
ms.locfileid: "86451484"
---
# <a name="migrate-or-deploy-windows-virtual-desktop-instances-to-azure"></a>Windows Virtual Desktop インスタンスを Azure に移行またはデプロイする

エンドユーザーのデスクトップをクラウドへ移行することは、クラウド移行の一般的なシナリオです。 このシナリオでは、生産性の向上を実現し、エンドユーザーのエクスペリエンスをサポートするためのさまざまなワークロードの移行を迅速化します。

## <a name="strategy-and-motivations"></a>戦略と動機

仮想デスクトップの移行は、次の絵と一覧で示すような一般的な目標となる成果によって動機付けされます。

![仮想デスクトップの移行の動機](../../_images/migrate/wvd/motivations.png)

- PC、携帯電話、タブレット、またはブラウザーなど、IT 部門が直接管理していない可能性があるものにまで生産性を拡張したいという明確な希望がある。
- エンドユーザーは、自分のデバイスから会社のデータとアプリケーションにアクセスする必要がある。
- ワークロードがクラウドに移行されると、エンドユーザーは、待機時間が短く、パフォーマンスに優れたエクスペリエンスを実現するために、より多くのサポートが必要になる。
- リモート ワークのコストを効果的にスケーリングできるように、現在または提案されている仮想デスクトップ エクスペリエンスのコストを最適化する必要がある。
- IT チームは職場を変革することを望んでおり、多くの場合、エンドユーザー エクスペリエンスの変革から始まる。

エンドユーザーのデスクトップをクラウド内に仮想化することで、これらの動機それぞれを実現できます。

## <a name="approach-wvd-refactor-and-modernization"></a>アプローチ:WVD のリファクターと最新化

この記事で説明されている方法では、既存の Citrix、VMware、またはリモート デスクトップ サービス (RDS) ファームは、Windows Virtual Desktop (WVD) と呼ばれるサービスとしてのプラットフォーム (PaaS) ソリューションを使用するように最新化されます。

このアプローチでは、仮想デスクトップの管理が最新のものとなり、Windows Virtual Desktop と呼ばれる Azure のサービスとしてのプラットフォーム プランで置き換えられています。 デスクトップ イメージは、Azure に移行されるかまたは新しいイメージが生成されます。 ユーザー プロファイルも Azure に移行されるか、新しいプロファイルが作成されます。 ほとんどの場合、クライアント ソリューションは有効ですが、この移行作業ではほとんど変更されません。

![仮想デスクトップの移行シナリオのソリューション図](../../_images/migrate/wvd/scenario-solution.png)

完了すると、仮想デスクトップ ファームの管理のオーバーヘッドとコストがクラウドネイティブ ソリューションに置き換えられます。これにより、お客様の仮想デスクトップ エクスペリエンスが管理されます。 チームが考慮する必要があるのは、デスクトップ イメージ、利用可能なアプリケーション、Azure Active Directory、ユーザー プロファイルのサポートだけです。

## <a name="next-step-integrate-this-strategy-into-your-cloud-adoption-journey"></a>次のステップ: この戦略をクラウド導入の取り組みに統合する

次の記事の一覧では、クラウド導入過程の特定の時点におけるガイダンスについて説明します。

- [Windows Virtual Desktop の移行またはデプロイの計画](./plan.md)
- [環境または Azure ランディング ゾーンを確認する](./ready.md)
- [Windows Virtual Desktop の概念実証を完了する](./proof-of-concept.md)
- [Windows Virtual Desktop の移行またはデプロイを評価する](./migrate-assess.md)
- [Windows Virtual Desktop インスタンスをデプロイまたは移行する](./migrate-deploy.md)
- [Windows Virtual Desktop のデプロイを運用環境にリリースする](./migrate-release.md)
