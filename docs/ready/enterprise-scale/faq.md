---
title: よく寄せられる質問
description: よく寄せられる質問。
author: BrianBlanchard
ms.author: brblanch
ms.date: 06/15/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.openlocfilehash: befa3a167d38dc49120255b7ba7f5a992be8d888
ms.sourcegitcommit: 9b183014c7a6faffac0a1b48fdd321d9bbe640be
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/19/2020
ms.locfileid: "85076916"
---
# <a name="faq"></a>よく寄せられる質問

このページでは、エンタープライズ規模の設計と Contoso の実装についてよく寄せられる質問を示します。

## <a name="enterprise-scale-design"></a>エンタープライズ規模の設計

### <a name="where-a-landing-zone-maps-in-azure-in-the-context-of-enterprise-scale"></a>エンタープライズ規模のコンテキストでランディング ゾーンが Azure 内でマッピングされる場所

エンタープライズ規模の観点から見ると、サブスクリプションは Azure のランディング ゾーンです。

## <a name="contoso-implementation"></a>Contoso の実装

### <a name="why-enterprise-scale-azure-resource-manager-templates-require-permissions-at-the-tenant-root--scope"></a>エンタープライズ規模の Azure Resource Manager テンプレートでテナントのルート '/' スコープでのアクセス許可が必要な理由

管理グループの作成は、テナントレベルの `put` アプリケーション プログラミング インターフェイスです。 サンプル テンプレートを使用するための前提条件は、ルート スコープでアクセス許可を付与することです。

### <a name="why-sync-a-fork-with-the-upstream-repo"></a>フォークを上流のリポジトリと同期する理由

これにより、バグとパッチを取得する頻度を制御できます。 これは中間のソリューションであり、パイプライン コードベースを GitHub Actions としてパッケージ化しているため、この手順は今後必要ありません。
