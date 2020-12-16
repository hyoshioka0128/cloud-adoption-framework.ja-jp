---
title: Windows Virtual Desktop インスタンスの Azure ランディング ゾーン
description: Azure 向けクラウド導入フレームワークを使用し、複雑さを軽減し、移行プロセスを標準化するベストプラクティスを使用して仮想デスクトップの移行環境を準備します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 07/17/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: migrate
ms.custom: internal
ms.openlocfilehash: a906c64b4e9bcc73223c1c1f6165d9bd5392c64d
ms.sourcegitcommit: b6f2b4f8db6c3b1157299ece1f044cff56895919
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2020
ms.locfileid: "97025437"
---
# <a name="windows-virtual-desktop-azure-landing-zone-review"></a>Windows Virtual Desktop の Azure ランディング ゾーンの確認

Contoso のクラウド導入チームには、Windows Virtual Desktop への移行の前に、デスクトップとその他のサポート ワークロードをホストできる Azure ランディング ゾーンが必要です。 次のチェックリストは、チームが互換性に関するランディング ゾーンの評価を行う際に役立ちます。 このフレームワークの[準備手法](../../ready/index.md)におけるガイダンスは、互換性のある Azure ランディング ゾーンが提供されていない場合にチームがこれを構築する場合に役立ちます。

## <a name="evaluate-compatibility"></a>互換性の評価

- **リソースの編成の計画:** ランディング ゾーンには、1 つまたは複数の使用されるサブスクリプションへの参照、リソース グループの使用に関するガイダンス、チームでのリソースのデプロイ時に使用されるタグ付けおよび名前付けの基準を含める必要があります。
- **Azure AD:** エンド ユーザー認証には、Azure Active Directory (Azure AD) インスタンスまたは Azure AD テナントを指定する必要があります。
- **ネットワーク:** 移行する前に、必要なネットワーク構成をランディング ゾーンで確立する必要があります。
- **VPN または ExpressRoute:** また、仮想デスクトップをサポートするすべてのランディング ゾーンでは、エンド ユーザーがランディング ゾーンおよびホストされた資産に接続できるよう、ネットワーク接続が必要になります。 既存のエンドポイントのセットが仮想デスクトップに構成されている場合、それらのオンプレミスのデバイスを経由し、VPN 接続または Azure ExpressRoute 接続を介して、エンド ユーザーをルーティングすることができます。 接続がまだ存在しない場合は、[準備手法](../../ready/index.md)におけるネットワーク接続を構成するオプションに関するガイダンスを確認してください。
- **ガバナンス、ユーザー、ID:** 一貫した適用のため、仮想デスクトップからのアクセスを管理するための要件と、ユーザーとその ID を管理するための要件は、Azure ポリシーとして構成し、ランディング ゾーンに適用する必要があります。
- **セキュリティ:** セキュリティ チームは、ランディング ゾーン構成を確認し、各ランディング ゾーンの用途を承認しました。これには、外部接続のためのランディング ゾーン、およびミッション クリティカルなアプリケーションや機密データのためのランディング ゾーンなどが含まれます。
- **Windows Virtual Desktop:** サービスとしての Windows Virtual Desktop プラットフォームが有効になっています。 <!-- TODO: Add link to enable the service. -->

[準備手法](../../ready/index.md)のベスト プラクティスを使用してチームで開発され、上記の特化された要件を満たすことができるランディング ゾーンはすべて、この移行のランディング ゾーンとして認められます。

Windows Virtual Desktop の設計方法については、[Windows Virtual Desktop の要件](/azure/virtual-desktop/overview#requirements)を確認してください。

## <a name="next-steps"></a>次のステップ

クラウド導入の取り組みの特定の要素に関するガイダンスについては、以下を参照してください。

- [Windows Virtual Desktop の概念実証を作成する](./proof-of-concept.md)
- [Windows Virtual Desktop の移行またはデプロイを評価する](./migrate-assess.md)
- [Windows Virtual Desktop インスタンスをデプロイまたは移行する](./migrate-deploy.md)
- [Windows Virtual Desktop のデプロイを運用環境にリリースする](./migrate-release.md)
