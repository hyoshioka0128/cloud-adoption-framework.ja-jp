---
title: Windows Virtual Desktop の計画
description: Azure 向けクラウド導入フレームワークを使用し、複雑さを軽減し、移行プロセスを標準化するベストプラクティスを使用して Windows Virtual Desktop の移行を計画します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 07/17/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: migrate
ms.custom: internal
ms.openlocfilehash: dabe9dd4731ba2de263e19c6ebfdc4b994989a21
ms.sourcegitcommit: 86d51757bd34b49ce3b061123a6aaa8c88d3b2cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/06/2021
ms.locfileid: "97909431"
---
# <a name="windows-virtual-desktop-planning"></a>Windows Virtual Desktop の計画

Windows Virtual Desktop とデプロイ シナリオでは、他の移行作業と同じ移行手法に従います。 一貫性のあるこの手法により、移行ファクトリや既存の移行チームは、非技術要件をほとんど変更せずにこのプロセスを採用できます。

![クラウド導入フレームワークの移行手法。](../../_images/migrate/methodology.png)

## <a name="plan-your-migration"></a>移行を計画する

他の移行と同様に、チームはワークロードを評価し、デプロイし、エンド ユーザーにリリースします。 ただし、Windows Virtual Desktop には、ワークロードの評価時に Azure ランディング ゾーンの確認を必要とする特定の要件が含まれています。 このプロセスでは、最初のデプロイの前に概念実証も必要になります。

計画を作成するには、Azure DevOps の既存の移行バックログに関する[クラウド導入計画 DevOps テンプレート](../../plan/template.md)をご覧ください。 このテンプレートを使用し、アクティビティの詳細な計画を作成します。

## <a name="business-justification"></a>業務の妥当性

計画の一環として、Windows Virtual Desktop に移行することによるビジネス上の利点を明瞭に伝えることができる必要があります。 次の一覧には、ビジネス ケースで使用する項目が含まれています。
  
- Windows Virtual Desktop のコントロール プレーンまたは管理プレーンは、顧客にサービスとして提供されます。 コントロール プレーンは、エンド ユーザーのデスクトップへのシームレスなグローバル接続、および IT で必要となる集中管理されたデプロイとオーケストレーションを管理します。 これは PaaS サービスであり、お客様がハードウェアの調達、ハードウェアのデプロイ、パッチの適用、サポートを行う必要はありません。 常に最新に保たれるこのサービスをご使用になるだけです。 コストはかかりません。既に所有しているライセンスを通じて利用できるサービスであるため、この PaaS サービスを使用することでコストを削減できます。 また、移行後には、独自のオンプレミスの仮想デスクトップ管理サービスに対するさまざまな管理、トラブルシューティング、障害対応、パッチの適用などを行う必要がなくなります。 これにより、ビジネスにとってはるかに重要なこと、つまり、より多くの価値をビジネスにもたらすことに集中できるようになります。これは通常、顧客がアプリケーションとデータを使用するときに最適なユーザー エクスペリエンスを得られるようにすることです。
- 初期費用は不要です。 仮想デスクトップをオンプレミスで実行するには、ピーク時の負荷に対応するために必要なすべてのハードウェアについて、プロジェクトの進行中にそのハードウェアが使用されていない場合や、プロジェクトの完了時にそのハードウェアが期間の 100% は使用されていない場合でも、前もって支払うか長期リース契約を締結する必要があります。 Windows Virtual Desktop と Azure は使用量に基づいて課金されるため、お支払いは使用した分だけです。 また、ビジネス要件に応じてスケール アップもスケール ダウンもできるため、ビジネス要件を満たしながらスケール ダウンによってコストを削減することもできます。
- Windows Virtual Desktop と Azure では、新機能の観点から、はるかに速い周期を実現しています。 オンプレミスのハードウェアと市販ソフトウェアでは、寿命が限られ、大規模な新しい機能セットや新機能を頻繁に受け取ることがなく、通常は実装のためのプロジェクトにコストがかかります。 Windows Virtual Desktop と Azure では、常に新機能を受け取ることができ、そのデプロイは Microsoft によって管理されます。 お客様は、常に最新に保たれるこのサービスをご使用になるだけです。 新しいサービスを利用できるようにする場合、新しいサービスをビジネス ユーザーにロールアウトするためのプロジェクトは必要ありません。 これらの新機能により、競争上の優位性が得られる可能性があります。また、少なくとも、技術的負債が重荷になることを防止できます。
- Azure は、大規模なスケールを実現するハイパースケール クラウドです。 オンプレミスのサービスでは、コンピューティング プラットフォームとして Azure と競うことはできません。 これにより、組織の機敏性が大幅に向上します。 世界各地に拡張する新しい機会があり、そこにローカルのデータ センターをお持ちでない場合、Microsoft はこのサービスを Azure リージョンでホストすることができます。Azure リージョンは増え続けているため、お客様側で対応するよりも、Microsoft はインフラストラクチャまたはサービスをエンド ユーザーの近くに配置することができます。
- エンド ユーザーが期待する Windows 10 の優れたユーザー エクスペリエンスをマルチセッションのコストで。 Windows Virtual Desktop を使用すると、Windows Server とリモート デスクトップ サービスのスケールに Windows 10 のユーザー エクスペリエンスを組み合わせることができます。アプリケーションの互換性は損なわれません
- Windows 10 マルチセッションにはクライアント アクセス ライセンスは必要ないため、Windows 10 マルチセッションでの CAL の要件はありません
- Windows Virtual Desktop では、Windows Server (Windows Server 2012 R2、2016、または 2019) をデプロイする場合、Windows Server ライセンスを購入する必要はありません
- すべての Windows Virtual Desktop 仮想マシンは、基本コンピューティング料金で課金されます。 Windows Virtual Desktop は、Windows ライセンスが含まれている既に所有している別のライセンス (M365 E3+) を通じて利用できます
- Windows Virtual Desktop 内のすべての Windows 7 仮想マシンでは、2023 年 1 月 14 日まで、無料の拡張セキュリティ更新プログラムを受け取ります


## <a name="next-steps"></a>次のステップ

クラウド導入の取り組みの特定の要素に関するガイダンスについては、以下を参照してください。

- [環境または Azure ランディング ゾーンを確認する](./ready.md)
- [Windows Virtual Desktop の概念実証を作成する](./proof-of-concept.md)
- [Windows Virtual Desktop の移行またはデプロイを評価する](./migrate-assess.md)
- [Windows Virtual Desktop インスタンスをデプロイまたは移行する](./migrate-deploy.md)
- [Windows Virtual Desktop のデプロイを運用環境にリリースする](./migrate-release.md)
