---
title: Windows Virtual Desktop を Azure にデプロイする
description: Azure 向けクラウド導入フレームワークを使用し、複雑さを軽減し、移行プロセスを標準化するベストプラクティスを使用して Windows Virtual Desktop をデプロイします。
author: BrianBlanchard
ms.author: brblanch
ms.date: 07/17/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: migrate
ms.custom: internal
ms.openlocfilehash: 8546db1193fa9746675e3fabcfde042671e2eb70
ms.sourcegitcommit: b6f2b4f8db6c3b1157299ece1f044cff56895919
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2020
ms.locfileid: "97025488"
---
<!-- cSpell:ignore NTFS Logix -->

# <a name="windows-virtual-desktop-deployment-or-migration"></a>Windows Virtual Desktop のデプロイまたは移行

この記事のガイダンスは、既に [Windows Virtual Desktop の計画を作成](./plan.md)し、[デスクトップのデプロイ要件を評価](./migrate-assess.md)し、[概念実証を完了](./proof-of-concept.md)しており、Windows Virtual Desktop インスタンスを移行またはデプロイする準備ができていることを前提としています。

## <a name="initial-scope"></a>初期のスコープ

Windows Virtual Desktop インスタンスのデプロイは、[概念実証](./proof-of-concept.md)プロセスと同様のプロセスに従います。 この初期のスコープをベースラインとして使用して、評価の出力に必要なさまざまなスコープ変更を説明します。

- Azure Marketplace の Windows&nbsp;10 ギャラリー イメージとスその手順のステップ 1 でのサイズ設定を使用して、プールされたデスクトップの[ホスト プールを作成](/azure/virtual-desktop/create-host-pools-azure-marketplace)します。
- 既に移行されているワークロードについて [RemoteApp アプリケーション グループを作成](/azure/virtual-desktop/manage-app-groups#create-a-remoteapp-group)します。
- ユーザー プロファイルを格納するために [FSLogix プロファイル コンテナーを作成](/azure/virtual-desktop/create-host-pools-user-profile)します。

デプロイおよび移行は、ペルソナの移行、アプリケーションの移行、ユーザー プロファイルの移行で構成されます。 ワークロード評価の結果によっては、これらの移行タスクのそれぞれに変更が生じる可能性があります。 この記事は、評価のフィードバックに基づいてスコープがどのように変化するかを判断するのに役立ちます。

## <a name="iterative-methodology"></a>繰り返しの方法論

各ペルソナで、前に説明した初期のスコープの繰り返しが必要になる可能性があり、結果として複数のホスト プールが必要となります。 Windows Virtual Desktop の評価に応じて、採用チームはペルソナの数またはペルソナごとのユーザー数に基づいて、繰り返しを定義する必要があります。 プロセスをペルソナ主導の繰り返しに分割すると、変化の速さによるビジネスへの影響を低減でき、チームはそれぞれのペルソナのプールに対する適切なテストやオンボードに集中できるようになります。

## <a name="scope-considerations"></a>スコープに関する考慮事項

次に示す一連の考慮事項のそれぞれを、移行またはデプロイされる各ペルソナ グループの設計ドキュメントに含める必要があります。 スコープに関する考慮事項を前に説明した[初期のスコープ](#initial-scope)に織り込んだら、デプロイまたは移行を開始できます。

### <a name="azure-landing-zone-considerations"></a>Azure ランディング ゾーンに関する考慮事項

ペルソナ グループをデプロイする前に、デプロイされる各ペルソナをサポートするのに必要なランディング ゾーンを、Azure リージョンに作成する必要があります。 割り当てられたそれぞれのランディング ゾーンは、[ランディング ゾーンのレビュー要件](./ready.md)に照らして評価する必要があります。

割り当てられた Azure ランディング ゾーンが要件を満たさない場合、環境に対して行う修正をスコープに追加する必要があります。

### <a name="application-and-desktop-considerations"></a>アプリケーションとデスクトップに関する考慮事項

一部のペルソナは、Windows&nbsp;10 Enterprise マルチセッションと互換性のない従来のソリューションに依存している可能性があります。 そのような場合、一部のペルソナでは専用のデスクトップが必要になる場合があります。 この依存関係は、デプロイとテストを行うまでわからないことがあります。

プロセスの後の方で発覚した場合、レガシ アプリケーションのモダナイゼーションまたは移行に対して将来的な繰り返しを割り当てる必要があります。 これにより、デスクトップ エクスペリエンスの長期的なコストが削減されます。 このような将来的な繰り返しは、モダン化にかかる価格の全体的な影響と専用デスクトップに関連する余分なコストを比較することにより、優先度付けして実施する必要があります。 パイプラインの中断を回避し、ビジネスの成果を実現するには、この優先順位付けが現在の繰り返しに影響を与えないようにする必要があります。

目標とするエンドユーザー エクスペリエンスをサポートするために、一部のアプリケーションでは修復、モダナイゼーション、Azure への移行が必要になることがあります。 これらの変更はおそらく、リリース後に必要になるでしょう。 また、デスクトップの待機時間がビジネス機能に影響を与える場合は、アプリケーションの変更により、一部のペルソナの移行を妨げる依存関係が生じることがあります。

### <a name="user-profile-considerations"></a>ユーザー プロファイルに関する考慮事項

[初期のスコープ](#initial-scope)では、[VM ベースの FSLogix ユーザー プロファイル コンテナー](/azure/virtual-desktop/create-host-pools-user-profile)の使用が想定されています。

[ユーザー プロファイルをホストするために Azure NetApp Files](/azure/virtual-desktop/create-fslogix-profile-container) を使用できます。 それを行うには、次を含むいくつかの追加のステップがスコープで必要になります。

- **NetApp インスタンスごと:** NetApp ファイル、ボリューム、Active Directory 接続を構成する。
- **ホストまたはペルソナごと:** セッション ホスト仮想マシンでの FSLogix の構成。
- **ユーザーごと:** ホスト セッションへのユーザーの割り当て。

[Azure Files を使用して、ユーザー プロファイルをホストする](/azure/virtual-desktop/create-file-share)こともできます。 それを行うには、次を含むいくつかの追加のステップがスコープで必要になります。

- **Azure Files インスタンスごと:** ストレージ アカウント、ディスクの種類、Active Directory 接続 ([Active Directory Domain Services (AD DS) もサポートされています](/azure/virtual-desktop/create-profile-container-adds)) を構成し、Active Directory ユーザー グループにロールベースのアクセス制御アクセスを割り当て、ニュー テクノロジ ファイル システムのアクセス許可を適用し、ストレージ アカウントのアクセスキーを取得する。
- **ホストまたはペルソナごと:** セッション ホスト仮想マシンでの FSLogix の構成。
- **ユーザーごと:** ホスト セッションへのユーザーの割り当て。

一部のペルソナまたはユーザーのユーザー プロファイルでも、データの移行作業が必要になることがあります。その場合、ローカルの Active Directory または個々のユーザー デスクトップ内でユーザー プロファイルが修正できるようになるまで、特定のペルソナの移行が遅延する可能性があります。 この遅延は、Windows Virtual Desktop のシナリオ以外では、スコープに大きな影響を与える可能性があります。 修正が完了したら、初期のスコープと上記のアプローチを再開することができます。

## <a name="deploy-or-migrate-windows-virtual-desktop"></a>Windows Virtual Desktop をデプロイまたは移行する

すべての考慮事項を Windows Virtual Desktop の移行またはデプロイの運用スコープに織り込んだら、プロセスを開始できます。 繰り返しを行う際に、採用チームはホスト プール、アプリケーション、ユーザー プロファイルをデプロイできます。 このフェーズが完了したら、デプロイ後の作業である[テストとユーザーのオンボード](./migrate-release.md)を開始できます。

## <a name="next-steps"></a>次のステップ

[Windows Virtual Desktop のデプロイを運用環境にリリースする](./migrate-release.md)
