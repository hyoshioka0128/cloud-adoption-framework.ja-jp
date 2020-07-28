---
title: Azure 向け Windows Virtual Desktop の評価
description: Azure 向けクラウド導入フレームワークを使用する、Windows Virtual Desktop の移行のベスト プラクティスについて説明します。これにより、複雑さを軽減し、移行プロセスを標準化することができます。
author: BrianBlanchard
ms.author: brblanch
ms.date: 07/01/2010
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: migrate
ms.openlocfilehash: 47bcc7420cdcea5ace4397737e420434da9fd27f
ms.sourcegitcommit: 9163a60a28ffce78ceb5dc8dc4fa1b83d7f56e6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2020
ms.locfileid: "86452734"
---
# <a name="windows-virtual-desktop-assessment"></a>Windows Virtual Desktop の評価

[Windows Virtual Desktop の概念実証](./proof-of-concept.md)では、ベースラインの実装として初期のスコープを得ることができます。 その概念実証の出力は、おそらく運用環境のニーズを満たしてはいないでしょう。 評価プロセスは、データ主導のプロセスによって仮定をテストすることに重点を置いています。 評価データは、一連の重要な質問に答え、仮定の正しさまたは誤りを実証し、必要な場合にはスコープを改善することにより、Windows Virtual Desktop のシナリオをサポートします。 この仮定検証のアプローチを使用することで、エンドユーザー デスクトップの WVD への移行またはデプロイを加速することができます。

## <a name="assess-windows-virtual-desktop-deployments"></a>Windows Virtual Desktop のデプロイの評価

Windows Virtual Desktop 評価ごとに、ユーザーのペルソナ、整合ホスト プール (VM)、エンドユーザーのアプリケーションとデータ、ユーザー プロファイル (データ) の組み合わせを評価します。 評価を行う際には、データを使用してこのセクションの質問に答えることが目的となります。 これらの答えによって、WVD 移行のデプロイとリリースの実際のスコープが決まります。

これらの質問への答えは、データから始まります。 計画の方法論、具体的には[ベスト プラクティス](../../plan/index.md)と[デジタル資産の評価](../../digital-estate/index.md)では、移行計画を作成するにはデータを既に収集し、分析している必要があります。 とはいえ、この特定のワークロードの評価における質問では、追加のデータが必要になります。 具体的には、WVD のデプロイ計画を作成するために、デスクトップ、ユーザー、各ユーザーが使用するワークロードに関するデータが必要になります。

データ収集ツールとして [Movere](https://docs.microsoft.com/azure/migrate/migrate-services-overview#movere) を使用する場合は、他の移行シナリオと同じように、[Azure Migrate](https://docs.microsoft.com/azure/migrate) のデータを使用して、ペルソナの作成とこれらの質問に答えるのに必要なデータを入手することができるでしょう。

このセクションのすべての質問に答えるのに必要なデータがない場合は、他のサードパーティ ソフトウェア ベンダーが提供する別の検出プロセスを使用して、既に持っているデータを補うことができます。 [Lakeside](https://docs.microsoft.com/azure/migrate/migrate-services-overview#isv-integration) も "VDI" 移行を目標とするセクションで Azure Migrate と統合されており、ペルソナ、ホスト プール、アプリケーション、ユーザー プロファイルを含む WVD デプロイ計画を立てる際に役立ちます。

### <a name="user-personas"></a>ユーザーのペルソナ

この移行シナリオに含まれるすべてのユーザーをサポートするには、どれくらいの数の別個のペルソナが必要になるでしょうか? ペルソナの定義は、以下に基づいてユーザーをバケット化することによって行えます。

- **個人用プール:** 特定のユーザーのグループは、プールではなく専用のデスクトップを必要としますか? たとえば、セキュリティ、コンプライアンス、ハイ パフォーマンス、ノイジーネイバーといった要件が理由で、一部のユーザーはプール戦略に含まれるのではなく、専用のデスクトップを実行する必要があります。 これは、[WVD のホスト プールをデプロイする際に、ホスト プールの種類として個人用](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-azure-marketplace#begin-the-host-pool-setup-process)を指定することで設定できます。
- **密度:** 特定のユーザーのグループは、密度の低いデスクトップ エクスペリエンスを必要としますか? 例: ライト ユーザーの場合は vCPU あたり 6 ユーザーを想定しますが、ヘビー ユーザーの場合は vCPU あたり 2 ユーザーにする必要があります。 これは、[WVD のホスト プールのデプロイにおけるプールの設定](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-azure-marketplace#begin-the-host-pool-setup-process)で入力できます。
- **パフォーマンス:** 特定のユーザーのグループは、ハイ パフォーマンスのデスクトップ エクスペリエンスを必要としますか? 例: 一部のユーザーは、想定されている vCPU あたり 4 GB の RAM を超える vCPU あたりのメモリを必要としています。 VM のサイズは、[WVD のホスト プールのデプロイでの仮想マシンの詳細](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-azure-marketplace#virtual-machine-details)で入力できます。
- **グラフィック処理 (GPU):** 特定のユーザーのグループには、より高いグラフィック性能の要件がありますか? たとえば、この [GPU VM の構成ガイド](https://docs.microsoft.com/azure/virtual-desktop/configure-vm-gpu)で説明されているように、一部のユーザーは Azure で GPU ベースの VM を必要とします。
- **Azure リージョン:** 特定のグループの OS ユーザーは、さまざまな地理的リージョンから操作しますか? たとえば、ホスト プールを構成する前に、各リージョンのユーザーは[見積りツール](https://azure.microsoft.com/services/virtual-desktop/assessment/#estimation-tool)を使用して Azure までの待機時間をテストする必要があります。 テスト ユーザーは、待機時間が最小の Azure リージョンと、上位 3 つの Azure リージョンの待機時間 (ミリ秒) を共有する必要があります。
- **ビジネス機能:** 特定のユーザーのグループ化で、部署、請求コード、ビジネス機能ごとにバケット化できますか? このようなグループ化は、運用の後の段階で会社のコストを調整するのに役立ちます。
- **ユーザー数:** それぞれのペルソナには、どれくらいのユーザーがいますか?
- **最大セッション数:** 地域と運用の時間に基づいて、最大負荷時に想定される各ペルソナの同時ユーザー数はどれくらいですか?

上記の各質問に基づいて、**ビジネス機能、コスト センター**、地理的リージョン、技術要件ごとにユーザーのペルソナの描写を始めることができます。 次の表は、回答を記録して、評価を完成させたり設計ドキュメントを作成したりするのに役立ちます。

|  | ペルソナ グループ 1  | ペルソナ グループ 2  | ペルソナ グループ 3  |
|---------|---------|---------|---------|
| プール  | プール | プール | 専用 (セキュリティ上の懸念) |
| 密度 | ライト (6 ユーザー/vCPU) | ヘビー (2 ユーザー/vCPU) | 専用 (1 ユーザー/vCPU) |
| パフォーマンス | 低 | 高メモリ消費量 | 低 |
| GPU | 該当なし | 必須 | なし |
| Azure リージョン | 北米 | 西ヨーロッパ | 北米 |
| ユーザー数 | 1000 | 50 | 20 |
| セッション数 | 200 | 50 | 10 |

技術要件が異なる各ペルソナ (またはビジネス機能が異なるユーザーの各グループ) には、特定のホスト プールの構成が必要になります。

エンドユーザーの評価により、プールの種類、密度、サイズ、CPU/GPU、ランディング ゾーン リージョンなどの、必要なデータを得ることができます。

ホスト プールの構成の評価により、そのデータがデプロイ計画にマッピングされます。 技術要件、ビジネス要件、コストの調整は、ホスト プールの適切な数と構成を決定するのに役立ちます。

[米国東部](https://azure.com/e/448606254c9a44f88798892bb8e0ef3c)、[西ヨーロッパ](https://azure.com/e/61a376d5f5a641e8ac31d1884ade9e55)、[東南アジア](https://azure.com/e/7cf555068922461587d0aa99a476f926)のリージョンでの価格の例をご覧ください。

### <a name="application-groups"></a>アプリケーション グループ

Movere と Lakeside による現在のオンプレミス環境のスキャンでは、エンドユーザーのデスクトップで実行されているアプリケーションに関するデータが提供されます。 そのデータを使用して、各ペルソナで必要になるすべてのアプリケーションの一覧を作成します。 必要なアプリケーションそれぞれについて、以下の質問の答えにより、デプロイの繰り返しを計画できます。

- ペルソナがこのデスクトップを使用するために、インストールする必要のあるアプリケーションはありますか? ペルソナが完全に Web ベースのサービスとしてのソフトウェアのアプリケーションを使用するのでない限り、各ペルソナに対して[カスタムのマスター VHD イメージを構成](https://docs.microsoft.com/azure/virtual-desktop/set-up-customize-master-image)して、そのマスター イメージに必要なアプリケーションをインストールすることが必要になるでしょう。
- このペルソナは、Office 365 アプリケーションを必要としますか? そうであるなら、[カスタマイズされたマスター VHD イメージに Office 365 を追加する](https://docs.microsoft.com/azure/virtual-desktop/install-office-on-wvd-master-image)必要があります。
- このアプリケーションは、Windows 10 マルチセッションと互換性がありますか? アプリケーションに互換性が無い場合、カスタムの VHD イメージを実行するために[個人用プール](https://docs.microsoft.com/azure/virtual-desktop/configure-host-pool-personal-desktop-assignment-type)が必要になることがあります。 Windows Virtual Desktop でのアプリケーションの互換性の問題については、[Desktop Application Assure](https://docs.microsoft.com/fasttrack/win-10-app-assure-assistance-offered) サービスからサポートを得ることもできます。
- ミッションクリティカルなアプリケーションは、仮想デスクトップと任意のバックエンド システムとの間の待機時間から影響を受ける可能性がありますか? そうであるなら、アプリケーションをサポートしているそのバックエンド システムを Azure に移行することを検討できます。

これらの質問の答えによっては、デスクトップの移行またはデプロイに先立って、デスクトップ イメージまたはサポートしているアプリケーション コンポーネントに対する修復を計画に含める必要があるかもしれません。

## <a name="next-step-deploy-or-migrate-windows-virtual-desktop-instances"></a>次のステップ: Windows Virtual Desktop インスタンスをデプロイまたは移行する

次の記事の一覧では、クラウド導入過程の特定の時点におけるガイダンスについて説明します。

- [Windows Virtual Desktop インスタンスをデプロイまたは移行する](./migrate-deploy.md)
- [Windows Virtual Desktop のデプロイを運用環境にリリースする](./migrate-release.md)
