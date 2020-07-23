---
title: Azure で CAF のエンタープライズ規模のランディング ゾーンを実装する
description: CAF のエンタープライズ規模のアーキテクチャを実装するためのオプションを確認します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 06/15/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.openlocfilehash: 2b17a23a67ae4dde719710fdf2f7d70c42126f99
ms.sourcegitcommit: 4bbd5f6444d033ef1f38dc6f3bad7b914a82f68f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2020
ms.locfileid: "86128320"
---
# <a name="implement-caf-enterprise-scale-landing-zones-in-azure"></a>Azure で CAF のエンタープライズ規模のランディング ゾーンを実装する

ビジネス要件のため、最初からガバナンス、セキュリティ、運用が完全に統合された高度なランディング ゾーンの初期実装が必要な場合は、ここで示すエンタープライズ規模の例のオプションを使用してください。 このアプローチでは、Microsoft Azure portal またはコードとしてのインフラストラクチャを使用して、環境を設定および構成できます。 また、組織の準備が整ったら、ポータルとコードとしてのインフラストラクチャ (推奨) の間で切り替えることもできます。 Microsoft Azure の他のコードとしてのインフラストラクチャ アプローチと同様に、Azure Resource Manager テンプレートと GitHub のスキルが必要になります。

## <a name="example-implementation"></a>実装例

次の表では、モジュール形式の実装の例を示します。

| デプロイ例  | 説明  | GitHub のリポジトリ | Deploy to Azure (Azure へのデプロイ) |
|---------|---------|---------|---------|---------|---------|---------|---------|
| エンタープライズ規模の基盤 | これは、エンタープライズ規模の導入に対して推奨される基盤です。 | [GitHub の例](https://github.com/Azure/Enterprise-Scale/blob/main/docs/reference/wingtip/README.md) | [例を Azure にデプロイする](https://ms.portal.azure.com/?feature.customportal=false#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzOps%2Fmain%2Ftemplate%2Fux-foundation.json) |
| エンタープライズ規模の Virtual WAN | Virtual WAN ネットワーク モジュールをエンタープライズ規模の基盤に追加します。 | [GitHub の例](https://github.com/Azure/Enterprise-Scale/blob/main/docs/reference/contoso/Readme.md) | [例を Azure にデプロイする](https://ms.portal.azure.com/?feature.customportal=false#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzOps%2Fmain%2Ftemplate%2Fux-vwan.json) |
| エンタープライズ規模のハブ アンド スポーク | ハブ アンド スポークのネットワーク モジュールをエンタープライズ規模の基盤に追加します。 | [GitHub の例](https://github.com/Azure/Enterprise-Scale/blob/main/docs/reference/adventureworks/README.md) | <!-- [Deploy example to Azure](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fkrnese%2FAzureDeploy%2Fmaint%2FARM%2Fdeployments%2Fe2e.json) --> 近日対応予定 |

## <a name="next-steps"></a>次のステップ

上記の例では、エンタープライズ規模のアプローチの継続的な学習をサポートする簡単なデプロイ オプションが提供されています。 これらの例をエンタープライズ規模の運用バージョンで使用する前に、まず[エンタープライズ規模のアーキテクチャ](./architecture.md)を確認してください。
