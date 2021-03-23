---
title: クラウド導入フレームワークのエンタープライズ規模のランディング ゾーンを Azure に実装する
description: Azure 向けのクラウド導入フレームワークにおけるエンタープライズ規模のアーキテクチャの実装オプションについて説明します。
author: JefferyMitchell
ms.author: brblanch
ms.date: 06/15/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.custom: think-tank
ms.openlocfilehash: 2d33b50eb9eaa46202091660f67762a946eb9db5
ms.sourcegitcommit: 36e85ac734b184de3f29884b744ea74c81ccc72b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/13/2021
ms.locfileid: "103439714"
---
# <a name="implement-cloud-adoption-framework-enterprise-scale-landing-zones-in-azure"></a>クラウド導入フレームワークのエンタープライズ規模のランディング ゾーンを Azure に実装する

ビジネス要件のため、最初からガバナンス、セキュリティ、運用のコントロール プレーンが完全に統合された高度なランディング ゾーンの初期実装が必要な場合は、ここで示すエンタープライズ規模の例のオプションを使用してください。 このアプローチでは、Azure portal かコードとしてのインフラストラクチャを使用して、Azure 環境を設定および構成できます。 また、組織の準備が整ったら、ポータルとコードとしてのインフラストラクチャ (推奨) の間で切り替えることもできます。

## <a name="reference-implementation"></a>リファレンス実装

次の表に、推奨されるエンタープライズ規模のアーキテクチャに基づく参照の実装例を示します。

| デプロイ例 | 説明 | GitHub のリポジトリ | Deploy to Azure (Azure へのデプロイ) |
|---------|---------|---------|---------|---------|---------|---------|---------|
| エンタープライズ規模の基盤 | これは、エンタープライズ規模の導入に対して推奨される基盤です。 | [GitHub の例][GitHub-WingTip] | [![DTA-Button-WingTip]][DTA-WingTip] |
| エンタープライズ規模のハブ アンド スポーク | ハブ アンド スポークのネットワーク モジュールをエンタープライズ規模の基盤に追加します。 | [GitHub の例][GitHub-AdventureWorks] | [![DTA-Button-AdventureWorks]][DTA-AdventureWorks] |
| エンタープライズ規模の Virtual WAN | Virtual WAN ネットワーク モジュールをエンタープライズ規模の基盤に追加します。 | [GitHub の例][GitHub-Contoso] | [![DTA-Button-Contoso]][DTA-Contoso] |

## <a name="next-steps"></a>次のステップ

上記の例では、エンタープライズ規模のアプローチの継続的な学習をサポートする簡単なデプロイ オプションが提供されています。 これらの例をエンタープライズ規模の運用バージョンで使用する前に、エンタープライズ規模のアーキテクチャを確認してください。

> [!div class="nextstepaction"]
> [エンタープライズ規模のアーキテクチャをレビューする](./architecture.md)

<!-- The following section is used to store references to external images and links to reduce maintenance overhead and enable tooltips -->

[//]: # (*******************************)
[//]: # (以下の外部画像を参照)
[//]: # (*******************************)

[DTA-Button-wingtip]: https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true "レファレンス実装 (Foundation) を Azure にデプロイします。"
[DTA-Button-adventureworks]: https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true "は、adventureworks リファレンス実装 (ハブとスポークとのハイブリッド接続) を Azure にデプロイします。"
[DTA-Button-adventureworks]: https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true "は、adventureworks リファレンス実装 (ハブとスポークとのハイブリッド接続) を Azure にデプロイします。"

[//]: # (**************************)
[//]: # (下の外部リンクラベル)
[//]: # (**************************)

[GitHub-WingTip]: https://github.com/Azure/Enterprise-Scale/blob/main/docs/reference/wingtip/README.md
[GitHub-AdventureWorks]: https://github.com/Azure/Enterprise-Scale/blob/main/docs/reference/adventureworks/README.md
[GitHub-Contoso]: https://github.com/Azure/Enterprise-Scale/blob/main/docs/reference/contoso/Readme.md

[DTA-WingTip]: https://ms.portal.azure.com/?feature.customportal=false#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FEnterprise-Scale%2Fmain%2Fdocs%2Freference%2Fwingtip%2FarmTemplates%2Fes-foundation.json/createUIDefinitionUri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FEnterprise-Scale%2Fmain%2Fdocs%2Freference%2Fwingtip%2FarmTemplates%2Fportal-es-foundation.json
[DTA-AdventureWorks]: https://ms.portal.azure.com/?feature.customportal=false#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FEnterprise-Scale%2Fmain%2Fdocs%2Freference%2Fadventureworks%2FarmTemplates%2Fes-hubspoke.json/createUIDefinitionUri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FEnterprise-Scale%2Fmain%2Fdocs%2Freference%2Fadventureworks%2FarmTemplates%2Fportal-es-hubspoke.json

[DTA-Contoso]: https://ms.portal.azure.com/?feature.customportal=false#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FEnterprise-Scale%2Fmain%2Fdocs%2Freference%2Fcontoso%2FarmTemplates%2Fes-vwan.json/createUIDefinitionUri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FEnterprise-Scale%2Fmain%2Fdocs%2Freference%2Fcontoso%2FarmTemplates%2Fportal-es-vwan.json
