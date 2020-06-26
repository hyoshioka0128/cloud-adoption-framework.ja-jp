---
title: Azure でエンタープライズ規模のランディング ゾーンを実装する
description: エンタープライズ規模のアーキテクチャを実装するためのオプションを確認します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 06/15/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.openlocfilehash: b804810e23213dc6ebedbe94aa9ea7c1af91cc8e
ms.sourcegitcommit: 9b183014c7a6faffac0a1b48fdd321d9bbe640be
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/19/2020
ms.locfileid: "85076877"
---
# <a name="implement-enterprise-scale-landing-zones-in-azure"></a>Azure でエンタープライズ規模のランディング ゾーンを実装する

ビジネス要件のため、最初からガバナンス、セキュリティ、運用が完全に統合されている高度なランディング ゾーンの初期実装が必要な場合は、このページで示すエンタープライズ規模の例のオプションを使用することをお勧めします。 このアプローチでは、Microsoft Azure portal またはコードとしてのインフラストラクチャを使用して、環境を設定および構成できます。 また、組織の準備が整ったら、ポータルとコードとしてのインフラストラクチャ (推奨) の間で切り替えることもできます。 Microsoft Azure の他のコードとしてのインフラストラクチャ アプローチと同様に、Azure Resource Manager テンプレートと GitHub のスキルが必要になります。

## <a name="example-implementation"></a>実装例

次の表では、モジュール形式の実装の例を示します。

| デプロイ例  | 説明  | GitHub のリポジトリ | Deploy to Azure (Azure へのデプロイ) |
|---------|---------|---------|---------|---------|---------|---------|---------|
| エンタープライズ規模の基盤 | これは、エンタープライズ規模の導入に対して推奨される基盤です。 | [GitHub の例](https://github.com/Azure/Enterprise-Scale/blob/main/docs/reference/wingtip/README.md) | [例を Azure にデプロイする](https://ms.portal.azure.com/?feature.customportal=false#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzOps%2Fmain%2Ftemplate%2Fux-foundation.json) |
| エンタープライズ規模の Virtual WAN | Virtual WAN ネットワーク モジュールをエンタープライズ規模の基盤に追加します。 | [GitHub の例](https://github.com/Azure/Enterprise-Scale/blob/main/docs/reference/contoso/Readme.md) | [例を Azure にデプロイする](https://ms.portal.azure.com/?feature.customportal=false#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzOps%2Fmain%2Ftemplate%2Fux-vwan.json) |
| エンタープライズ規模のハブ アンド スポーク | ハブ アンド スポークのネットワーク モジュールをエンタープライズ規模の基盤に追加します。 | [GitHub の例](https://github.com/Azure/Enterprise-Scale/blob/main/docs/reference/adventureworks/README.md) | <!-- [Deploy example to Azure](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fkrnese%2FAzureDeploy%2Fmaint%2FARM%2Fdeployments%2Fe2e.json) --> 近日対応予定 |

## <a name="next-steps"></a>次のステップ

上記の例では、エンタープライズ規模のアプローチの継続的な学習をサポートする簡単なデプロイ オプションが提供されています。 これらの例をエンタープライズ規模の運用バージョンで使用する前に、まず[エンタープライズ規模のアーキテクチャ](./architecture.md)を確認してください。
