---
title: Azure 向け Windows Virtual Desktop の評価
description: Azure 向けクラウド導入フレームワークを使用し、移行プロセスまたはデプロイ プロセスを加速するベストプラクティスを使用して Windows Virtual Desktop の移行シナリオを評価します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 07/17/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: migrate
ms.openlocfilehash: f489c9230a26c149c7d44b9eb0ba0631a757c0ef
ms.sourcegitcommit: 57b757759b676a22f13311640b8856557df36581
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/20/2020
ms.locfileid: "94997014"
---
# <a name="windows-virtual-desktop-assessment"></a>Windows Virtual Desktop の評価

Windows Virtual Desktop の[概念実証](./proof-of-concept.md)によって、Contoso のクラウド導入チーム向けのベースラインの実装として初期のスコープが提供されています。 しかし、その概念実証の出力は、おそらく彼らの運用環境のニーズを満たしてはいないでしょう。

Windows Virtual Desktop 評価演習は、データドリブンのプロセスによって仮定をテストすることに重点を置いています。 評価データは、チームが一連の重要な質問に答え、自分たちの仮定の正しさまたは誤りを実証し、必要に応じてスコープを改善すると、チームの Windows Virtual Desktop のシナリオをサポートするのに役立ちます。 この仮定検証のアプローチを使用すると、チームは、エンドユーザー デスクトップの Windows Virtual Desktop への移行またはデプロイを加速することができます。

## <a name="assess-windows-virtual-desktop-deployments"></a>Windows Virtual Desktop のデプロイの評価

Windows Virtual Desktop 評価ごとに、ユーザーのペルソナ、仮想マシン (VM) の整合ホスト プール、エンドユーザーのアプリケーションとデータ、ユーザー プロファイル (データ) の組み合わせを評価します。 評価を行う場合には、データを使用してこのセクションの質問に答えることがチームの目的となります。 これらの答えによって、Windows Virtual Desktop 移行のデプロイとリリースの実際のスコープが決まります。

これらの質問への答えは、データから始まります。 計画の方法論、具体的には[ベスト プラクティス](../../plan/index.md)と[デジタル資産の評価](../../digital-estate/index.md)では、移行計画を作成するために既にデータが収集および分析されている必要があります。 とはいえ、この特定のワークロードの評価における質問では、追加のデータが必要になります。 Windows Virtual Desktop のデプロイ計画を作成するために、デスクトップ、ユーザー、各ユーザーが使用するワークロードに関するデータが必要になります。

データ収集ツールとして [Movere](/azure/migrate/migrate-services-overview#movere) を使用する場合は、他の移行シナリオと同じように、[Azure Migrate](/azure/migrate) のデータを使用して、ペルソナの作成とこれらの質問に答えるのに必要なデータを入手することができるでしょう。

このセクションのすべての質問に答えるのに必要なデータがない場合は、お持ちのデータを補うための別の検出プロセスが、他のサードパーティ ソフトウェア ベンダーから提供されている可能性があります。 ベンダーの [Lakeside](/azure/migrate/migrate-services-overview#isv-integration) も仮想デスク トップ インフラストラクチャ移行を目標とするセクション内で Azure Migrate と統合されています。 このベンダーは、ペルソナ、ホスト プール、アプリケーション、ユーザー プロファイルを含む Windows Virtual Desktop デプロイ計画を立てる場合に役立ちます。

### <a name="user-personas"></a>ユーザーのペルソナ

この移行シナリオに含まれるすべてのユーザーをサポートするには、どれくらいの数の別個のペルソナが必要になるでしょうか? 以下の条件に基づいてユーザーをバケット化するとペルソナを定義することができます。

- **個人用プール:** 特定のユーザーのグループは、プールではなく専用のデスクトップを必要としますか? たとえば、セキュリティ、コンプライアンス、ハイ パフォーマンス、ノイジーネイバーといった要件が理由で、一部のユーザーはプール戦略に含まれていない専用のデスクトップで実行することになります。 この情報を入力するには、[Windows Virtual Desktop のホスト プールのデプロイ中に、ホスト プールの種類として個人用を指定します](/azure/virtual-desktop/create-host-pools-azure-marketplace#begin-the-host-pool-setup-process)。
- **密度:** 特定のユーザーのグループは、密度の低いデスクトップ エクスペリエンスを必要としますか? たとえば、密度が高くなると、仮想中央処理ユニット (vCPU) あたり 6 ユーザーというライト ユーザーの想定ではなく、vCPU あたり 2 ユーザーにすることが必要な場合があります。 密度情報は、[ Windows Virtual Desktop ホスト プール デプロイのプール設定](/azure/virtual-desktop/create-host-pools-azure-marketplace#begin-the-host-pool-setup-process)に入力します。
- **パフォーマンス:** 特定のユーザーのグループは、ハイ パフォーマンスのデスクトップ エクスペリエンスを必要としますか? たとえば、一部のユーザーは、想定されている vCPU あたり 4&nbsp;ギガバイト (GB) の RAM を超える vCPU あたりのメモリを必要としています。 VM のサイズは、[Windows Virtual Desktop のホスト プールのデプロイでの仮想マシンの詳細](/azure/virtual-desktop/create-host-pools-azure-marketplace#virtual-machine-details)で入力します。
- **グラフィック処理 (GPU):** 特定のユーザーのグループには、より高いグラフィック性能の要件がありますか? たとえば、この [GPU VM の構成ガイド](/azure/virtual-desktop/configure-vm-gpu)で説明されているように、一部のユーザーは Azure で GPU ベースの VM を必要とします。
- **Azure リージョン:** 特定のグループの OS ユーザーは、さまざまな地理的リージョンから操作しますか? たとえば、お客様がホスト プールを構成する前に、各リージョンのユーザーは[見積りツール](https://azure.microsoft.com/services/virtual-desktop/assessment/#estimation-tool)を使用して Azure までの待機時間をテストする必要があります。 テスト ユーザーは、待機時間が最小の Azure リージョンと、上位 3 つの Azure リージョンの待機時間 (ミリ秒) を共有する必要があります。
- **ビジネス機能:** 特定のユーザーのグループ化で、部署、請求コード、ビジネス機能ごとにバケット化できますか? このようなグループ化は、運用の後の段階で会社のコストを調整するのに役立ちます。
- **ユーザー数:** それぞれのペルソナには、どれくらいのユーザーがいますか?
- **最大セッション数:** 地域と運用の時間に基づいて、最大負荷時に想定される各ペルソナの同時ユーザー数はどれくらいですか?

前述の各質問に基づいて、ビジネス機能、コスト センター、地理的リージョン、技術要件ごとにユーザーのペルソナの描写を始めることができます。 次の表は、回答を記録して、評価を完成させたり設計ドキュメントを作成したりするのに役立ちます。

| 条件  | ペルソナ グループ&nbsp; 1  | ペルソナ グループ&nbsp; 2  | ペルソナ グループ&nbsp; 3  |
|---------|---------|---------|---------|
| プール  | プール | プール | 専用 (セキュリティ上の懸念) |
| 密度 | ライト (6&nbsp; ユーザー/vCPU) | ヘビー (2&nbsp; ユーザー/vCPU) | 専用 (1&nbsp; ユーザー/vCPU) |
| パフォーマンス | 低 | 高メモリ消費量 | 低 |
| GPU | 該当なし | 必須 | なし |
| Azure リージョン | 北米 | 西ヨーロッパ | 北米 |
| ユーザー数 | 1,000 | 50 | 20 |
| セッション数 | 200 | 50 | 10 |

各ペルソナ、またはビジネス機能と技術要件が異なる各ユーザー グループには、特定のホストプール構成が必要になります。

エンドユーザーの評価により、プールの種類、密度、サイズ、CPU/GPU、ランディング ゾーン リージョンなどの必要なデータを得ることができます。

ホスト プールの構成の評価によって、そのデータがデプロイ計画にマッピングされるようになりました。 技術要件、ビジネス要件、コストの調整は、ホスト プールの適切な数と構成を決定するのに役立ちます。

[米国東部](https://azure.com/e/448606254c9a44f88798892bb8e0ef3c)、[西ヨーロッパ](https://azure.com/e/61a376d5f5a641e8ac31d1884ade9e55)、[東南アジア](https://azure.com/e/7cf555068922461587d0aa99a476f926)のリージョンでの価格の例をご覧ください。

### <a name="application-groups"></a>アプリケーション グループ

Movere と Lakeside の両方による現在のオンプレミス環境のスキャンにより、エンドユーザーのデスクトップで実行されているアプリケーションに関するデータが提供されます。 そのデータを使用すると、各ペルソナで必要になるすべてのアプリケーションの一覧を作成できます。 必要なアプリケーションそれぞれについて、以下の質問の答えにより、デプロイの繰り返しを計画できます。

- ペルソナがこのデスクトップを使用するために、インストールする必要のあるアプリケーションはありますか? ペルソナが、完全に Web ベースのソフトウェアをサービス アプリケーションとして使用するのでない限り、各ペルソナに対して[カスタムのマスター VHD イメージを構成](/azure/virtual-desktop/set-up-customize-master-image)して、そのマスター イメージに必要なアプリケーションをインストールすることが必要になるでしょう。
- このペルソナは、Microsoft 365 アプリケーションを必要としますか? そうであるなら、[カスタマイズされたマスター VHD イメージに Microsoft 365 を追加する](/azure/virtual-desktop/install-office-on-wvd-master-image)必要があります。
- このアプリケーションは、Windows&nbsp;10 Enterprise マルチセッションと互換性がありますか? アプリケーションに互換性が無い場合、カスタムの VHD イメージを実行するために[個人用プール](/azure/virtual-desktop/configure-host-pool-personal-desktop-assignment-type)が必要になることがあります。 アプリケーションと Windows Virtual Desktop の互換性の問題については、[Desktop Application Assure](/fasttrack/win-10-app-assure-assistance-offered) サービスを参照してください。
- ミッションクリティカルなアプリケーションは、Windows Virtual Desktop インスタンスと任意のバックエンド システムとの間の待機時間から影響を受ける可能性がありますか? その場合、アプリケーションをサポートしているそのバックエンド システムを Azure に移行することの検討をお勧めします。

これらの質問の答えによっては、デスクトップの移行またはデプロイに先立って、デスクトップ イメージまたはサポートしているアプリケーション コンポーネントに対する修復を計画に含める必要があるかもしれません。

## <a name="next-steps"></a>次のステップ

クラウド導入の取り組みの特定の要素に関するガイダンスについては、以下を参照してください。

- [Windows Virtual Desktop インスタンスをデプロイまたは移行する](./migrate-deploy.md)
- [Windows Virtual Desktop のデプロイを運用環境にリリースする](./migrate-release.md)
