---
title: Moodle の移行タスク、アーキテクチャ、テンプレート
description: Moodle の移行に関連するタスクとアーキテクチャ、およびテンプレートについて説明します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 11/06/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: plan
ms.openlocfilehash: f3e5d88e894289e615ac0573d6c640832b8ed028
ms.sourcegitcommit: a7eb2f6c4465527cca2d479edbfc9d93d1e44bf1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2020
ms.locfileid: "94714934"
---
# <a name="moodle-migration-tasks-architecture-and-template"></a>Moodle の移行タスク、アーキテクチャ、テンプレート

## <a name="moodle-migration-task-outline"></a>Moodle の移行タスクの概要

Moodle の移行には、次のタスクが含まれます。

- Azure Resource Manager テンプレートを使用して Azure インフラストラクチャをデプロイします。
- AzCopy をダウンロードしてインストールします。
- バックアップ アーカイブを Azure Resource Manager デプロイからコントローラー仮想マシン インスタンスにコピーします。
- Moodle アプリケーションと構成の移行。
- Moodle コントローラー インスタンスとワーカー ノードを設定します。
- PHP と Web サーバーの構成。

## <a name="deploy-azure-infrastructure-with-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートを使用して Azure インフラストラクチャをデプロイする

- Azure Resource Manager テンプレートを使用して Azure にインフラストラクチャをデプロイする場合、いくつかのオプションを使用できます。 次の図は、インフラストラクチャ リソースの概要を示しています。

![Azure インフラストラクチャ リソース。](images/architecture.png)

完全に構成可能なデプロイでは、さらに柔軟にデプロイを選択できます。 定義済みのデプロイ サイズでは、4 つの定義済み Moodle サイズのいずれかが使用されます。 定義済みの 4 つのテンプレート オプションは、最小、小規模から中規模、大規模、最大で、これらは [Moodle GitHub リポジトリ](https://github.com/Azure/Moodle)で入手できます。

- [最小](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FMoodle%2Fmaster%2Fazuredeploy-minimal.json):このデプロイでは、NFS、MySQL、および小規模な自動スケール Web フロントエンド仮想マシン sku (1 つの仮想コア) を使用します。これにより、デプロイ時間が短縮され (30 分未満)、現時点では Azure 無料試用版サブスクリプションに対応する 2 つの仮想マシンのみが必要になります。

- [小規模から中規模](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FMoodle%2Fmaster%2Fazuredeploy-small2mid-noha.json):最大 1,000 の同時ユーザーをサポートします。 このデプロイでは、エラスティック検索や Redis キャッシュなどの他のオプションを使用せずに、NFS (高可用性なし) と MySQL (8 つの仮想コア) を使用します。

- [大規模 (高可用性)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FMoodle%2Fmaster%2Fazuredeploy-large-ha.json):2,000 を超える同時ユーザーをサポートします。 このデプロイでは、エラスティック検索などの他のオプションを使用せずに、Azure Files、MySQL (16 の仮想コア)、Redis キャッシュを使用します。

- [Maximum](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FMoodle%2Fmaster%2Fazuredeploy-maximal.json): この最大のデプロイでは、Azure Files、最大の SKU を備えた MySQL、Redis キャッシュ、エラスティック検索 (3 つの仮想マシン)、大規模ストレージ サイズ (データ ディスクとデータベースの両方) を使用します。

**[Launch] \(起動\)** を選択して、定義済みのテンプレートをデプロイします。 これにより Azure portal に移動します。ここで、 **[サブスクリプション]** 、 **[リソース グループ]** 、[ **[SSH キー]**](https://docs.github.com/en/free-pro-team@latest/github/authenticating-to-github/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent)、 **[リージョン]** などの必須フィールドに入力する必要があります。

![カスタム デプロイ:カスタム テンプレートからのデプロイ。](images/custom-deployment.png)

前述の定義済みテンプレートでは、既定のバージョンがデプロイされます。

```bash
Ubuntu: 18.04-LTS
PHP: 7.4
Moodle: 3.8
```

オンプレミスの PHP と Moodle のバージョンが古い場合は、次の手順でバージョンを更新します。

- **[カスタム デプロイ]** ページで **[テンプレートの編集]** を選択します。

![テンプレートの編集:Azure Resource Manager テンプレートの編集。](images/edit-template.png)

- **Resources** セクションで、Moodle および PHP のバージョンを **Parameters** ブロックに追加します。

    ```json
    "phpVersion":       { "value": "7.2" },
    "moodleVersion":    { "value": "MOODLE_38_STABLE"}
    ```

- Moodle 3.9 の場合、値は `MOODLE_39_STABLE` になります。

- **[保存]** を選択して変更を保存します。

## <a name="next-steps"></a>次のステップ

Moodle 移行プロセスの詳細については、「[Moodle 移行のリソース](./migration-resources.md)」に進みます。
