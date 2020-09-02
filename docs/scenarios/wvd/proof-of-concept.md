---
title: Windows Virtual Desktop の概念実証
description: Azure 向けクラウド導入フレームワークを使用する、Windows Virtual Desktop の移行のベスト プラクティスについて説明します。これにより、複雑さを軽減し、移行プロセスを標準化することができます。
author: BrianBlanchard
ms.author: brblanch
ms.date: 07/01/2010
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: migrate
ms.openlocfilehash: 6edca977153499d6593398303808b24f4e1e1d6b
ms.sourcegitcommit: 07d56209d56ee199dd148dbac59671cbb57880c0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88885305"
---
<!-- cSpell:ignore FSLogix onboards remoteapp macos -->

# <a name="windows-virtual-desktop-proof-of-concept"></a>Windows Virtual Desktop の概念実証

Contoso のクラウド導入チームでは、エンド ユーザーのデスクトップをデプロイする前に、概念実証を実行およびテストして、Azure ランディング ゾーンの構成とエンド ユーザーのネットワーク容量を検証します。

移行プロセスの以下のアプローチは、概念実証の実装の概要を示すために簡略化されています。

1. **評価**: チームは、既定の仮想マシン (VM) のサイズを使用して、ホスト プールをデプロイします。 評価データは、予想される同時ユーザー セッションの数と、それらの同時セッションをサポートするために必要な VM の数をチームが特定するのに役立ちます。
2. **デプロイ**: チームは、Azure Marketplace の Windows 10 ギャラリー イメージとその評価手順 1 のサイズ設定を使用して、プールされたデスクトップの[ホスト プールを作成](/azure/virtual-desktop/create-host-pools-azure-marketplace)します。
3. **デプロイ**: チームは、既に移行されているワークロードについて [RemoteApp アプリケーション グループを作成](/azure/virtual-desktop/manage-app-groups#create-a-remoteapp-group)します。
4. **デプロイ**: チームは、ユーザー プロファイルを格納するために、[FSLogix プロファイル コンテナーを作成](/azure/virtual-desktop/create-host-pools-user-profile)します。
5. **リリース**: チームは、ユーザーのサンプリングについて、アプリケーション グループとデプロイされたデスクトップのパフォーマンスと待機時間をテストします。
6. **リリース**: チームは、エンド ユーザーをオンボードして、[Windows デスクトップ クライアント](/azure/virtual-desktop/connect-windows-7-and-10)、[Web クライアント](/azure/virtual-desktop/connect-web)、[Android クライアント](/azure/virtual-desktop/connect-android)、[macOS クライアント](/azure/virtual-desktop/connect-macos)、または [iOS クライアント](/azure/virtual-desktop/connect-ios)を介した接続方法について説明します。

## <a name="assumptions"></a>前提条件

概念実証アプローチは、運用のニーズをいくつか満たすことはありますが、いくつかの仮定の上で成り立つものです。

Windows Virtual Desktop のすべてのエンタープライズ移行に関して、次の仮定がすべて正しいと証明されるとは限りません。 運用環境デプロイには、Windows Virtual Desktop の評価時に特定された運用上の要件に、より厳密に適合する別のデプロイが必要であると、導入チームは想定する必要があります。 前提条件は次のとおりです。

- エンド ユーザーに、Azure で割り当てられているランディング ゾーンに低待機時間の接続がある。
- すべてのユーザーが、デスクトップの共有プールから作業できる。
- すべてのユーザーが、Azure Marketplace から Windows&nbsp;10 Enterprise マルチセッション イメージを使用できる。
- すべてのユーザー プロファイルが、Azure Files、Azure NetApp Files、FSLogix プロファイル コンテナーの VM ベースのストレージ サービスのいずれかに移行される。
- すべてのユーザーが、[VM のサイズに関する推奨事項に従い](/windows-server/remote/remote-desktop-services/virtual-machine-recs#multi-session-recommendations)、仮想中央処理装置 (vCPU) ごと、および 4&nbsp;GB の RAM ごとにユーザーが 6 人という密度で、一般的なペルソナによって説明できる。
- すべてのワークロードが、Windows&nbsp;10 マルチセッションと互換性がある。
- 運用環境での使用で、仮想デスクトップとアプリケーション グループ間の待機時間を受け入れられる。

概念実証の構成参照に基づいて Windows Virtual Desktop シナリオのコストを計算するために、チームでは、[米国東部](https://azure.com/e/448606254c9a44f88798892bb8e0ef3c)、[西ヨーロッパ](https://azure.com/e/61a376d5f5a641e8ac31d1884ade9e55)、または[東南アジア](https://azure.com/e/7cf555068922461587d0aa99a476f926)の料金計算ツールを使用します。
> [!NOTE]
> これらの例ではすべて、Azure Files をユーザー プロファイルのストレージ サービスとして使用しています。

## <a name="next-steps"></a>次のステップ

クラウド導入の取り組みの特定の要素に関するガイダンスについては、以下を参照してください。

- [Windows Virtual Desktop の移行またはデプロイを評価する](./migrate-assess.md)
- [Windows Virtual Desktop インスタンスをデプロイまたは移行する](./migrate-deploy.md)
- [Windows Virtual Desktop のデプロイを運用環境にリリースする](./migrate-release.md)
