---
title: 既存の Linux サーバーを Azure Arc に接続する
description: 既存の Linux サーバーを Azure Arc に接続します。
author: likamrat
ms.author: brblanch
ms.date: 01/29/2021
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: operate
ms.custom: think-tank, e2e-hybrid
ms.openlocfilehash: 6608133beaafa0deccbd9ea54968575009d8a903
ms.sourcegitcommit: 51565dc4d3a1858bd62f708f2e4c082fbd4c6fe4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/20/2021
ms.locfileid: "107746890"
---
# <a name="connect-an-existing-linux-server-to-azure-arc"></a>既存の Linux サーバーを Azure Arc に接続する

この記事では、簡単なシェル スクリプトを使用して Linux サーバーを Azure Arc に接続するためのガイダンスを提供します。

## <a name="prerequisites"></a>前提条件

1. [Azure CLI バージョン 2.7 以降をインストールまたは更新します](/cli/azure/install-azure-cli)。 現在インストールされているバージョンを確認するには、次のコマンドを使用します。

    ```console
    az --version
    ```

2. Azure サービス プリンシパルを作成します。

    サーバーを Azure Arc に接続するには、共同作成者ロールが割り当てられた Azure サービス プリンシパルが必要です。 これを作成するには、自分の Azure アカウントにサインインして、次のコマンドを実行します。 このコマンドは [Azure Cloud Shell](https://shell.azure.com/) で実行することもできます。

    ```console
    az login
    az ad sp create-for-rbac -n "<Unique SP Name>" --role contributor
    ```

    次に例を示します。

    ```console
    az ad sp create-for-rbac -n "http://AzureArcServers" --role contributor
    ```

    出力は次のようになります。

    ```json
    {
      "appId": "XXXXXXXXXXXXXXXXXXXXXXXXXXXX",
      "displayName": "AzureArcServers",
      "name": "http://AzureArcServers",
      "password": "XXXXXXXXXXXXXXXXXXXXXXXXXXXX",
      "tenant": "XXXXXXXXXXXXXXXXXXXXXXXXXXXX"
    }
    ```

    > [!NOTE]
    > サービス プリンシパルのスコープを特定の [Azure サブスクリプションとリソース グループ](/cli/azure/ad/sp)に設定することを強くお勧めします。

3. サーバーの新しい Azure リソース グループを作成します。

    ![リソース グループが空の Azure portal のスクリーンショット。](./media/onboard-server/linux-resource-group.png)

4. [`az_connect_linux`](https://github.com/microsoft/azure_arc/blob/main/azure_arc_servers_jumpstart/scripts/az_connect_linux.sh) シェル スクリプトをダウンロードします。

5. 環境変数をお使いの環境に合わせて変更します。

    ![変更する環境変数のスクリーンショット。](./media/onboard-server/linux-variables.png)

6. 任意のツールを使用してスクリプトを指定のサーバーにコピーします (または、スクリプトをコピーしてサーバー内の新しいファイルに貼り付けます)。 次の例は、`scp` を使用してスクリプトを macOS からサーバーにコピーする方法を示しています。

    ![`scp` スクリプトのスクリーンショット。](./media/onboard-server/linux-scp.png)

## <a name="deployment"></a>デプロイ

`. ./az_connect_linux.sh` コマンドを使用してスクリプトを実行します。

> [!NOTE]
> ドットが付加されている理由は、スクリプトに *export* 関数があり、残りのコマンドと同じシェル セッションで変数をエクスポートする必要があるからです。

正常に完了すると、Linux サーバーがリソース グループ内の新しい Azure Arc リソースとして接続されます。

![実行中の `az_connect_linux.sh` Linux スクリプトのスクリーンショット。](./media/onboard-server/az-connect-linux.png)

![Azure portal の Azure Arc 対応リソースのスクリーンショット。](./media/onboard-server/linux-resource.png)

![Azure portal の Azure Arc 対応リソースの詳細のスクリーンショット。](./media/onboard-server/linux-resource-detail.png)

## <a name="delete-the-deployment"></a>デプロイの削除

サーバーを削除するには、サーバーを選択して Azure portal から削除します。

![Azure portal のリソースを削除するオプションのスクリーンショット。](./media/onboard-server/linux-delete-resource.png)

デプロイ全体を削除するには、Azure リソース グループを Azure portal から削除します。

![Azure portal を介してリソース グループを削除するオプションのスクリーンショット。](./media/onboard-server/linux-delete-resource-group.png)
