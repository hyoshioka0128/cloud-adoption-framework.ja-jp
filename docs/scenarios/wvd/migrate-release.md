---
title: Windows Virtual Desktop のデプロイ後とリリースのプロセス
description: Azure 向けクラウド導入フレームワークを使用する、Windows Virtual Desktop の移行のベスト プラクティスについて説明します。これにより、複雑さを軽減し、移行プロセスを標準化することができます。
author: BrianBlanchard
ms.author: brblanch
ms.date: 07/01/2010
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: migrate
ms.openlocfilehash: 4c5c4140bb5735706f8cefbb4f37cdc391601c75
ms.sourcegitcommit: 9163a60a28ffce78ceb5dc8dc4fa1b83d7f56e6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2020
ms.locfileid: "86452728"
---
# <a name="windows-virtual-desktop-post-deployment"></a>Windows Virtual Desktop のデプロイ後

Windows Virtual Desktop インスタンスを移行またはデプロイするためのリリース プロセスは比較的簡単です。 このプロセスは、[Windows Virtual Desktop の概念実証](./proof-of-concept.md)で使用されているものと同じです。

- ユーザーのサンプリングについて、アプリケーション グループとデプロイされたデスクトップのパフォーマンスと待機時間をテストします。
- エンド ユーザーをオンボードして、次のものを介した接続方法について説明します。
  - [Windows デスクトップ クライアント](https://docs.microsoft.com/azure/virtual-desktop/connect-windows-7-and-10)
  - [Web クライアント](https://docs.microsoft.com/azure/virtual-desktop/connect-web)
  - [Android クライアント](https://docs.microsoft.com/azure/virtual-desktop/connect-android)
  - [macOS クライアント](https://docs.microsoft.com/azure/virtual-desktop/connect-macos)
  - [iOS クライアント](https://docs.microsoft.com/azure/virtual-desktop/connect-ios)

## <a name="post-deployment"></a>配置後

リリースが完了したら、[Windows Virtual Desktop をより適切に操作するため、ログと診断](https://docs.microsoft.com/azure/virtual-desktop/diagnostics-log-analytics#push-diagnostics-data-to-your-workspace)を追加するのが一般的です。 また、一般的に、運用チームはプールされたホストとデスクトップ VM を [Azure サーバー管理のベスト プラクティス](../../manage/azure-server-management/index.md)にオンボードして、レポート、修正プログラム、および BCDR の構成を管理します。

この移行シナリオの対象外ではありますが、以後移行を繰り返していく中で、追加のワークロードを Azure に移行する必要性がこのリリース プロセスによって明らかになる場合があります。 Office 365 または Azure AD を構成していない場合、チームはデスクトップ シナリオのリリース時にこれらのサービスにオンボードできます。 ハイブリッド運用モデルでは、運用チームは、Intune、System Center、または他の構成管理ツールを統合して、運用、コンプライアンス、セキュリティを向上させることもできます。

## <a name="next-step-your-next-migration-iteration"></a>次のステップ: 次の移行の繰り返し

Windows Virtual Desktop の移行が完了したら、クラウド導入チームはその次のシナリオ固有の移行を開始できます。 移行すべきデスクトップがまだある場合は、次の Windows Virtual Desktop の移行またはデプロイのガイドとして、この記事のシリーズをもう一度利用できます。

- [Windows Virtual Desktop の移行またはデプロイの計画](./plan.md)
- [環境または Azure ランディング ゾーンを確認する](./ready.md)
- [Windows Virtual Desktop の概念実証を作成する](./proof-of-concept.md)
- [Windows Virtual Desktop の移行またはデプロイを評価する](./migrate-assess.md)
- [Windows Virtual Desktop インスタンスをデプロイまたは移行する](./migrate-deploy.md)
- [Windows Virtual Desktop のデプロイを運用環境にリリースする](./migrate-release.md)
