---
title: Moodle 移行のアーキテクチャとテンプレート
description: Moodle Azure インフラストラクチャのデプロイ用 Azure Resource Manager (ARM) テンプレートの概要と、そのテンプレートをデプロイまたは編集する方法について説明します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 11/30/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: plan
ms.openlocfilehash: e36261c7021f970d4ca94903fe5260a7f787803f
ms.sourcegitcommit: 18f3ee8fcd8838f649cb25de1387b516aa23a5a0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/30/2020
ms.locfileid: "96327852"
---
# <a name="moodle-migration-architecture-and-templates"></a>Moodle 移行のアーキテクチャとテンプレート

Moodle 移行には、次のタスクが含まれます。

1. Azure Resource Manager (ARM) テンプレートを使用して Azure インフラストラクチャをデプロイする。
1. [AzCopy をダウンロードしてインストールする](migration-start.md#download-and-install-azcopy-on-the-controller-vm)。
1. Azure Resource Manager デプロイ内で [Moodle のバックアップ アーカイブをコントローラー仮想マシン インスタンスにコピーする](migration-start.md#copy-the-archive-to-the-controller-vm)。
1. [Moodle アプリケーションと構成を移行する](migration-start.md#import-the-moodle-database-to-azure)。
1. [Moodle コントローラー インスタンスとワーカー ノードを設定する](azure-infra-config.md)。
1. [PHP と Web サーバーを構成する](azure-infra-config.md)。

この記事では、Moodle Azure インフラストラクチャのオプションと、選択した ARM テンプレートを使用して、必要な Azure リソースをデプロイする方法について説明します。

## <a name="azure-infrastructure"></a>Azure インフラストラクチャ

次の図は、Azure Moodle インフラストラクチャ リソースの概要を示しています。

![Azure インフラストラクチャ リソースを示す図。](images/architecture.png)

## <a name="arm-template-options"></a>ARM テンプレート オプション

Moodle リソースを Azure にデプロイするには、完全に構成可能な ARM テンプレートまたは複数の定義済み ARM テンプレートのうち 1 つを使用できます。 完全に構成可能なデプロイを使用すると、最も柔軟にデプロイを選択できます。 完全に構成可能なテンプレートと定義済みテンプレートは、[Moodle GitHub リポジトリ](https://github.com/Azure/Moodle)で見つかります。

定義済みのデプロイ テンプレートによって使用される定義済み Moodle サイズは、最小、小規模から中規模、大規模、最大の 4 つのいずれかです。

- "*最小のデプロイ*" には 2 つの仮想マシン (VM) のみが必要であるため、これは Azure 無料試用版サブスクリプションで機能します。 このデプロイで使用されているのは、ネットワーク ファイル システム (NFS)、MySQL、および、より小規模な自動スケール Web フロントエンド VM SKU (1 つの仮想コア) です。 このテンプレートを使用すると、デプロイ時間が短くなります (30 分未満)。
  
  [![最小 Moodle デプロイ ARM テンプレートを起動するボタン。](images/deploy-to-azure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FMoodle%2Fmaster%2Fazuredeploy-minimal.json)

- *小規模から中規模* は、最大 1000 人の同時ユーザーをサポートします。 このデプロイで使用されているのは NFS (高可用性なし) と MySQL (8 つの仮想コア) です。 このデプロイには、Elasticsearch、Redis キャッシュなどのオプションが含まれていません。
  
  [![小規模から中規模 Moodle デプロイ ARM テンプレートを起動するボタン。](images/deploy-to-azure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FMoodle%2Fmaster%2Fazuredeploy-small2mid-noha.json)

- *大規模 (高可用性) デプロイ* は、2000 人を超える同時ユーザーをサポートします。 このデプロイで使用されているのは、Azure Files、MySQL (16 の仮想コア)、および Redis キャッシュです。Elasticsearch などの他のオプションはありません。
  
  [![大規模 (高可用性) Moodle デプロイ ARM テンプレートを起動するボタン。](images/deploy-to-azure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FMoodle%2Fmaster%2Fazuredeploy-large-ha.json)

- *最大* デプロイで使用されているのは、Azure Files、最大の SKU を備えた MySQL、Redis キャッシュ、3 つの VM 上の Elasticsearch、およびデータ ディスクとデータベースの両方を対象とした大規模ストレージ サイズです。
  
  [![最大 Moodle デプロイ ARM テンプレートを起動するボタン。](images/deploy-to-azure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FMoodle%2Fmaster%2Fazuredeploy-maximal.json)

## <a name="deploy-the-template"></a>テンプレートのデプロイ

定義済み ARM テンプレートのいずれかをデプロイするには:

1. 前のセクションで、必要なデプロイの **[Azure へのデプロイ]** ボタンを選択します。 この操作により、Azure portal に移動します。
   
1. Azure portal 内の **[カスタム デプロイ]** ページで、必須フィールドである **[サブスクリプション]** 、 **[リソース グループ]** 、 **[リージョン]** 、 **[SSH 公開キー]** に入力します。 SSH キーを追加する方法の詳細については、「[新しい SSH キーを生成して ssh-agent に追加する](https://docs.github.com/free-pro-team@latest/github/authenticating-to-github/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent)」を参照してください。
   
   :::image type="content" source="images/custom-deployment.png" alt-text="Moodle デプロイ ARM テンプレートの Azure カスタム デプロイ画面を示すスクリーンショット。" border="false":::
   
1. **[Review + create]\(レビュー + 作成\)** を選択します。

### <a name="edit-the-template"></a>テンプレートの編集

定義済みの ARM テンプレートによって、次の既定のソフトウェア バージョンがデプロイされます。

- Ubuntu:18.04 LTS
- PHP:7.4
- Moodle: 3.8

オンプレミスの PHP と Moodle のバージョンが上記の前の値と異なる場合は、次の手順に従って、テンプレートのバージョンを更新します。

1. Azure portal 内の ARM テンプレートの **[カスタム デプロイ]** ページで、 **[テンプレートの編集]** を選択します。
   
1. テンプレートの **[リソース]** セクションで、 **[パラメーター]** の下に Moodle と PHP のバージョンのパラメーターを追加します。

   ```json
   "phpVersion":       { "value": "7.2" },
   "moodleVersion":    { "value": "MOODLE_38_STABLE"}
   ```
   
   たとえば、Moodle 3.9 の場合、`moodleVersion` 値は `MOODLE_39_STABLE` になります。
   
   :::image type="content" source="images/edit-template.png" alt-text="Moodle デプロイ ARM テンプレートの [テンプレートの編集] ページを示すスクリーンショット。" border="false":::
   
1. **[保存]** を選択します。

## <a name="next-steps"></a>次のステップ

「[Moodle 移行のリソース](migration-resources.md)」に進み、ARM テンプレートが Azure にデプロイするリソースを確認します。
