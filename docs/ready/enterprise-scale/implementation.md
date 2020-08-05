---
title: Azure で CAF のエンタープライズ規模のランディング ゾーンを実装する
description: CAF のエンタープライズ規模のアーキテクチャを実装するためのオプションを確認します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 06/15/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.openlocfilehash: 164a52dc11673e454ff7ad63fc7c4ddc4617395d
ms.sourcegitcommit: 9662234674e663bc7d4bc134d303520cb146bd95
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/04/2020
ms.locfileid: "87560442"
---
# <a name="implement-caf-enterprise-scale-landing-zones-in-azure"></a>Azure で CAF のエンタープライズ規模のランディング ゾーンを実装する

ビジネス要件のため、最初からガバナンス、セキュリティ、運用が完全に統合された高度なランディング ゾーンの初期実装が必要な場合は、ここで示すエンタープライズ規模の例のオプションを使用してください。 このアプローチでは、Microsoft Azure portal またはコードとしてのインフラストラクチャを使用して、環境を設定および構成できます。 また、組織の準備が整ったら、ポータルとコードとしてのインフラストラクチャ (推奨) の間で切り替えることもできます。

## <a name="example-implementation"></a>実装例

次の表では、モジュール形式の実装の例を示します。

| デプロイ例  | 説明  | GitHub のリポジトリ | Deploy to Azure (Azure へのデプロイ) |
|---------|---------|---------|---------|---------|---------|---------|---------|
| エンタープライズ規模の基盤 | これは、エンタープライズ規模の導入に対して推奨される基盤です。 | [GitHub の例](https://github.com/Azure/Enterprise-Scale/blob/main/docs/reference/wingtip/README.md) | [例を Azure にデプロイする](https://ms.portal.azure.com/?feature.customportal=false#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FEnterprise-Scale%2Fmain%2Fdocs%2Freference%2Fwingtip%2FarmTemplates%2Fes-foundation.json/createUIDefinitionUri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FEnterprise-Scale%2Fmain%2Fdocs%2Freference%2Fwingtip%2FarmTemplates%2Fportal-es-foundation.json) |
| エンタープライズ規模の Virtual WAN | Virtual WAN ネットワーク モジュールをエンタープライズ規模の基盤に追加します。 | [GitHub の例](https://github.com/Azure/Enterprise-Scale/blob/main/docs/reference/contoso/Readme.md) | [例を Azure にデプロイする](https://ms.portal.azure.com/?feature.customportal=false#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FEnterprise-Scale%2Fmain%2Fdocs%2Freference%2Fcontoso%2FarmTemplates%2Fes-vwan.json/createUIDefinitionUri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FEnterprise-Scale%2Fmain%2Fdocs%2Freference%2Fcontoso%2FarmTemplates%2Fportal-es-vwan.json) |
| エンタープライズ規模のハブ アンド スポーク | ハブ アンド スポークのネットワーク モジュールをエンタープライズ規模の基盤に追加します。 | [GitHub の例](https://github.com/Azure/Enterprise-Scale/blob/main/docs/reference/adventureworks/README.md) | <!-- [Deploy example to Azure](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fkrnese%2FAzureDeploy%2Fmaint%2FARM%2Fdeployments%2Fe2e.json) --> 近日対応予定 |

## <a name="next-steps"></a>次のステップ

上記の例では、エンタープライズ規模のアプローチの継続的な学習をサポートする簡単なデプロイ オプションが提供されています。 これらの例をエンタープライズ規模の運用バージョンで使用する前に、まず[エンタープライズ規模のアーキテクチャ](./architecture.md)を確認してください。
