---
title: Windows Virtual Desktop インスタンスの Azure ランディング ゾーン
description: Azure 向けのクラウド導入フレームワークを使用して、複雑さを軽減し、移行プロセスを標準化する仮想デスクトップ移行のベスト プラクティスについて説明します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 07/01/2010
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: migrate
ms.openlocfilehash: 21ae5207c7d4df5ef29c8a7f99000cbbf670f684
ms.sourcegitcommit: 9163a60a28ffce78ceb5dc8dc4fa1b83d7f56e6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2020
ms.locfileid: "86452731"
---
# <a name="windows-virtual-desktop-azure-landing-zone-review"></a>Windows Virtual Desktop の Azure ランディング ゾーンの確認

WVD に移行する前に、チームは、デスクトップとその他のサポートされるワークロードをホストできる Azure ランディング ゾーンが必要です。 次のチェックリストは、互換性についてランディング ゾーンを評価するのに役立ちます。 このフレームワークの[準備手法](../../ready/index.md)のガイダンスは、互換性のある Azure ランディング ゾーンが提供されていない場合に構築するのに役立ちます。

## <a name="evaluate-compatibility"></a>互換性の評価

- **リソースの編成の計画:** ランディング ゾーンには、使用されるサブスクリプション (複数の場合もある) への参照、リソース グループの使用に関するガイダンス、リソースのデプロイ時に使用されるタグ付けおよび名前付け規則を含める必要があります。
- **Azure AD:** エンド ユーザー認証には、Azure Active Directory または Azure AD のテナントを指定する必要があります。
- **ネットワーク:** 移行する前に、必要なネットワーク構成をランディング ゾーンで確立する必要があります。
- **VPN または ExpressRoute:** また、仮想デスクトップをサポートするすべてのランディング ゾーンでは、エンド ユーザーがランディング ゾーンとホストされた資産に接続するためのネットワーク接続が必要になります。 仮想デスクトップに構成された既存のエンドポイントのセットがある場合、それらのオンプレミスのデバイス経由で VPN 接続または ExpressRoute 接続を介してエンド ユーザーをルーティングすることもできます。 まだ存在しない場合、[準備手法](../../ready/index.md)のネットワーク接続オプションを構成する方法に関するガイダンスをご確認ください。
- **ガバナンス、ユーザー、ID:** 仮想デスクトップからのアクセスを管理するための要件と、ユーザーとその ID を管理するための要件は、Azure ポリシーとして構成し、一貫した実施のためにランディング ゾーンに適用する必要があります。
- **セキュリティ:** セキュリティ チームは、ランディング ゾーン構成を確認し、各ランディング ゾーンの用途を承認しました。これには、外部接続のためのランディング ゾーン、ミッション クリティカルなアプリケーションや機密データのためのランディング ゾーンなどがあります。
- **Windows Virtual Desktop (WVD):** WVD PaaS サービスは有効になっています。 <!-- TODO: Add link to enable the service. -->

上記の特別な要件を満たすことができる[準備手法](../../ready/index.md) のベスト プラクティスを使用して開発されたすべてのランディング ゾーンは、この移行のランディング ゾーンの候補と見なされます。

Windows Virtual Desktop の設計方法については、[こちらで要件](https://docs.microsoft.com/azure/virtual-desktop/overview#requirements)をご確認ください。

## <a name="next-step-complete-a-windows-virtual-desktop-proof-of-concept"></a>次のステップ: Windows Virtual Desktop の概念実証を作成する

- [Windows Virtual Desktop の概念実証を作成する](./proof-of-concept.md)
- [Windows Virtual Desktop の移行またはデプロイを評価する](./migrate-assess.md)
- [Windows Virtual Desktop インスタンスをデプロイまたは移行する](./migrate-deploy.md)
- [Windows Virtual Desktop のデプロイを運用環境にリリースする](./migrate-release.md)
