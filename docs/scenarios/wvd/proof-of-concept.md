---
title: Windows Virtual Desktop の概念実証
description: Azure 向けのクラウド導入フレームワークを使用して、複雑さを軽減し、移行プロセスを標準化する仮想デスクトップ移行のベスト プラクティスについて説明します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 07/01/2010
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: migrate
ms.openlocfilehash: 00419cc14952f2a4b0faf1f986af50e086c8ffa5
ms.sourcegitcommit: 9163a60a28ffce78ceb5dc8dc4fa1b83d7f56e6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2020
ms.locfileid: "86452738"
---
# <a name="windows-virtual-desktop-proof-of-concept"></a>Windows Virtual Desktop の概念実証

Azure ランディング ゾーンの構成とエンドユーザーのネットワーク容量を検証するには、エンドユーザーのデスクトップをデプロイする前に概念実証を作成してテストします。 移行プロセスの以下のアプローチは、概念実証の実装の概要を示すために簡略化されています。

1. **評価:** ホスト プールは、既定の VM サイズを使用してデプロイする必要があります。 評価データは、予想される同時ユーザー セッションの数と、それらの同時セッションをサポートするために必要な仮想マシンの数を特定するのに役立ちます。
2. **デプロイ:** Azure Marketplace の Windows 10 ギャラリー イメージとステップ 1 でのサイズ設定を使用して、プールされたデスクトップの[ホスト プールを作成](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-azure-marketplace)します。
3. **デプロイ:** 既に移行されているワークロードについて [RemoteApp アプリケーション グループを作成](https://docs.microsoft.com/azure/virtual-desktop/manage-app-groups#create-a-remoteapp-group)します。
4. **デプロイ:** ユーザー プロファイルを格納するために [FSLogix プロファイル コンテナーを作成](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-user-profile)します。
5. **リリース:** ユーザーのサンプリングについて、アプリケーション グループとデプロイされたデスクトップのパフォーマンスと待機時間をテストします。
6. **リリース:** エンド ユーザーをオンボードして、[Windows デスクトップ クライアント](https://docs.microsoft.com/azure/virtual-desktop/connect-windows-7-and-10)、[Web クライアント](https://docs.microsoft.com/azure/virtual-desktop/connect-web)、[Android クライアント](https://docs.microsoft.com/azure/virtual-desktop/connect-android)、[macOS クライアント](https://docs.microsoft.com/azure/virtual-desktop/connect-macos)、[iOS クライアント](https://docs.microsoft.com/azure/virtual-desktop/connect-ios)を介した接続方法について説明します

## <a name="assumptions"></a>前提条件

概念実証アプローチは、運用のニーズをいくつか満たす可能性があります。 ただし、このアプローチはいくつもの仮定に基づいて構築されています。

WVD のすべてのエンタープライズ移行で、以下の仮定がすべて事実であることはほとんどありません。 導入チームは、運用環境デプロイでは、Windows Virtual Desktop の評価時に識別された運用上の要件により厳密に適合する別のデプロイが必要であることを想定する必要があります。

1. エンド ユーザーは、Azure で割り当てられているランディング ゾーンに低待機時間の接続がある。
2. すべてのユーザーが、デスクトップの共有プールから作業できる。
3. すべてのユーザーが、Azure Marketplace から Windows 10 Enterprise マルチセッションイメージを使用できる。
4. すべてのユーザー プロファイルが、Azure Files、Azure NetApp Files、FSLogix プロファイル コンテナーの VM ベースのストレージ サービスのいずれかに移行される。
5. すべてのユーザーが、[VM のサイズに関する推奨事項に従って](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/virtual-machine-recs#multi-session-recommendations)、1 vCPU あたり 6 ユーザーと 4 GB の RAM の密度の一般的な人で説明できる
6. すべてのワークロードが Windows 10 マルチセッションと互換性がある。
7. 運用環境での使用で、仮想デスクトップとアプリケーション グループ間の待機時間を受け入れられる。

概念実証の構成参照に基づいて WVD シナリオのコストを計算するには、[米国東部](https://azure.com/e/448606254c9a44f88798892bb8e0ef3c)、[西ヨーロッパ](https://azure.com/e/61a376d5f5a641e8ac31d1884ade9e55)、[東南アジア](https://azure.com/e/7cf555068922461587d0aa99a476f926)の料金計算ツール。 注 - これらのすべての例で Azure Files を使用しています。これはユーザー プロファイル ストレージのストレージ サービスです。

## <a name="next-step-assess-for-windows-virtual-desktop"></a>次のステップ: Windows Virtual Desktop を評価する

次の記事の一覧では、クラウド導入過程の特定の時点におけるガイダンスについて説明します。

- [Windows Virtual Desktop の移行またはデプロイを評価する](./migrate-assess.md)
- [Windows Virtual Desktop インスタンスをデプロイまたは移行する](./migrate-deploy.md)
- [Windows Virtual Desktop のデプロイを運用環境にリリースする](./migrate-release.md)
