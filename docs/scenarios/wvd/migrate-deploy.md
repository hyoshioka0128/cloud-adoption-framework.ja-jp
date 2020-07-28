---
title: Windows Virtual Desktop を Azure にデプロイする
description: Azure 向けクラウド導入フレームワークを使用する、Windows Virtual Desktop の移行のベスト プラクティスについて説明します。これにより、複雑さを軽減し、移行プロセスを標準化することができます。
author: BrianBlanchard
ms.author: brblanch
ms.date: 07/01/2010
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: migrate
ms.openlocfilehash: 416d3be5b850ece425590a680a59e0b112f9cdad
ms.sourcegitcommit: 9163a60a28ffce78ceb5dc8dc4fa1b83d7f56e6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2020
ms.locfileid: "86452739"
---
<!-- cSpell:ignore NTFS Logix -->

# <a name="windows-virtual-desktop-deployment-or-migration"></a>Windows Virtual Desktop のデプロイまたは移行

この記事のガイダンスは、既に [Windows Virtual Desktop の計画を作成](./plan.md)し、[デスクトップのデプロイ要件を評価](./migrate-assess.md)し、[概念実証を完了](./proof-of-concept.md)しており、Windows Virtual Desktop インスタンスを移行またはデプロイする準備ができていることを前提としています。

## <a name="initial-scope"></a>初期のスコープ

WVD のデプロイは、概念実証で行ったステップと同じようなプロセスで行います。 この初期のスコープをベースラインとして使用して、評価の出力に基づいて必要となる様々なスコープの変更を説明します。

- Azure Marketplace の Windows 10 ギャラリー イメージとステップ 1 でのサイズ設定を使用して、プールされたデスクトップの[ホスト プールを作成](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-azure-marketplace)します。
- 既に移行されているワークロードについて [RemoteApp アプリケーション グループを作成](https://docs.microsoft.com/azure/virtual-desktop/manage-app-groups#create-a-remoteapp-group)します。
- ユーザー プロファイルを格納するために [FSLogix プロファイル コンテナーを作成](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-user-profile)します。

デプロイまたは移行は、ペルソナの移行、アプリケーションの移行、ユーザー プロファイルの移行で構成されます。 ワークロード評価の結果によっては、これらの移行タスクのそれぞれに変更が生じる可能性があります。 この記事は、評価のフィードバックに基づいてスコープがどのように変化するかを判断するのに役立ちます。

## <a name="iterative-methodology"></a>繰り返しの方法論

各ペルソナでは、既に概要を説明した初期のスコープの繰り返しが必要になる可能性があり、結果として複数のホスト プールが必要となります。 Windows Virtual Desktop の評価に基づいて、採用チームはペルソナの数またはペルソナごとのユーザー数 (あるいはその両方) に応じて、繰り返しを定義する必要があります。 プロセスをペルソナ主導の繰り返しに分割することで、変化の速さによるビジネスへの影響を低減でき、チームはそれぞれのペルソナのプールに対する適切なテストとオンボードに集中できるようになります。

## <a name="scope-considerations"></a>スコープに関する考慮事項

次に示す一連の考慮事項のそれぞれを、移行またはデプロイされる各ペルソナ グループの設計ドキュメントに含める必要があります。 スコープに関する考慮事項を上記の[初期スコープ](#initial-scope)に織り込んだら、デプロイまたは移行を開始できます。

### <a name="azure-landing-zone-considerations"></a>Azure ランディング ゾーンに関する考慮事項

デプロイに先立って、デプロイされる各ペルソナをサポートするのに必要なランディング ゾーンを、Azure リージョンに作成する必要があります。 割り当てられたそれぞれのランディング ゾーンは、[ランディング ゾーンのレビュー要件](./ready.md)に照らして評価する必要があります。

割り当てられた Azure ランディング ゾーンが要件を満たさない場合、環境に対して行う修正をスコープに追加する必要があります。

### <a name="application-and-desktop-considerations"></a>アプリケーションとデスクトップに関する考慮事項

一部のペルソナは、Windows 10 マルチセッションと互換性のない従来のソリューションに依存している可能性があります。 そのような場合、一部のペルソナでは専用のデスクトップが必要になるかもしれません。 この依存関係は、デプロイとテストを行うまでわからないことがあります。 プロセスの後のほうで発覚した場合、レガシ アプリケーションのモダン化または移行に対して将来的な繰り返しを割り当てることにより、デスクトップ エクスペリエンスの長期的なコストを削減する必要があります。 このような将来的な繰り返しは、モダン化にかかる価格の全体的な影響と専用デスクトップに関連する余分なコストを比較することにより、優先度付けして実施する必要があります。 これは、パイプラインの中断を回避し、ビジネスの成果を実現するために、現在の繰り返しには影響を与えるべきではありません。

目標とするエンドユーザー エクスペリエンスをサポートするために、一部のアプリケーションでは修復、モダン化、Azure への移行が必要になることがあります。 これらの変更はおそらく、リリース後に必要になるでしょう。 また、デスクトップの待機時間がビジネス機能に影響を与える場合は、アプリケーションの変更により、一部のペルソナの移行を妨げる依存関係が生じることがあります。

### <a name="user-profile-considerations"></a>ユーザー プロファイルに関する考慮事項

[初期のスコープ](#initial-scope)では、[VM ベースの FSLogix ユーザー プロファイル コンテナー](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-user-profile)の使用が想定されています。

[ユーザー プロファイルをホストするために Azure NetApp Files](https://docs.microsoft.com/azure/virtual-desktop/create-fslogix-profile-container) を使用できます。 そうするには、次を含むいくつかの追加のステップがスコープで必要になります。

- **NetApp インスタンスごと:** NetApp ファイル、ボリューム、Azure Directory 接続の構成。
- **ホストまたはペルソナごと:** セッション ホスト仮想マシンでの FSLogix の構成。
- **ユーザーごと:** ホスト セッションへのユーザーの割り当て。

また、[ユーザー プロファイルをホストするために Azure Files](https://docs.microsoft.com/azure/virtual-desktop/create-file-share) を使用することもできます。これには、次を含むいくつかの追加のステップがスコープで必要になります。

- **Azure Files インスタンスごと:** ストレージ アカウント、ディスクの種類、Active Directory 接続 ([Azure AD DS もサポートされています](https://docs.microsoft.com/azure/virtual-desktop/create-profile-container-adds)) の構成、Active Directory ユーザー グループへの RBAC アクセスの割り当てと NTFS アクセス許可の適用、ストレージ アカウントのアクセス キーの取得。
- **ホストまたはペルソナごと:** セッション ホスト仮想マシンでの FSLogix の構成。
- **ユーザーごと:** ホスト セッションへのユーザーの割り当て。

一部のペルソナまたはユーザーのユーザー プロファイルでも、データの移行作業が必要になることがあります。その場合、ローカルの Active Directory または個々のユーザー デスクトップ内でユーザー プロファイルが修正できるようになるまで、特定のペルソナの移行が遅延する可能性があります。 これは、WVD シナリオ外のスコープに大きな影響を与える可能性があります。 修正されたら、初期のスコープまたは上記のアプローチを再開することができます。

## <a name="deploy-or-migrate-windows-virtual-desktop"></a>Windows Virtual Desktop をデプロイまたは移行する

すべての考慮事項を Windows Virtual Desktop の移行またはデプロイの運用スコープに織り込んだら、プロセスを開始できます。 繰り返しを行う際に、採用チームはホスト プール、アプリケーション、ユーザー プロファイルをデプロイできます。 それが完了したら、デプロイ後の作業である[テストとユーザーのオンボード](./migrate-release.md)を行います。

## <a name="next-steps"></a>次のステップ

次のステップでは、Windows Virtual Desktop インスタンスをデプロイまたは移行します。 次の記事の一覧では、クラウド導入過程の特定の時点におけるガイダンスについて説明します。

- [Windows Virtual Desktop のデプロイを運用環境にリリースする](./migrate-release.md)
