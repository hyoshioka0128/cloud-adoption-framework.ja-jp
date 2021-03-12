---
title: 既存の Windows Server インスタンスを Azure Arc に接続する
description: 既存の Windows Server インスタンスを Azure Arc に接続します。
author: likamrat
ms.author: brblanch
ms.date: 01/29/2021
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: operate
ms.custom: think-tank, e2e-hybrid
ms.openlocfilehash: 38b5d79919fa8e4f75af0e9f485e5fec32149aeb
ms.sourcegitcommit: b8f8b7631aabaab28e9705934bf67dad15e3a179
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101800118"
---
# <a name="connect-an-existing-windows-server-instance-to-azure-arc"></a>既存の Windows Server インスタンスを Azure Arc に接続する

この記事では、簡単な PowerShell スクリプトを使用して Windows マシンを Azure Arc に接続するためのガイダンスを提供します。

## <a name="prerequisites"></a>前提条件

1. [Azure CLI バージョン 2.7 以降をインストールまたは更新します](/cli/azure/install-azure-cli)。 現在インストールされているバージョンを確認するには、次のコマンドを使用します。

    ```console
    az --version
    ```

2. Azure サービス プリンシパルを作成します。

    サーバーを Azure Arc に接続するには、共同作成者ロールが割り当てられた Azure サービス プリンシパルが必要です。 作成するには、自分の Azure アカウントにサインインして、次のコマンドを実行します。 このコマンドは、[Azure Cloud Shell](https://shell.azure.com/) 内で実行することもできます。

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
    > サービス プリンシパルのスコープを、特定の [Azure サブスクリプションとリソース グループ](/cli/azure/ad/sp)に限定することを強くお勧めします。

3. マシンの新しい Azure リソース グループを作成します。

    ![Azure portal 内の空のリソース グループのスクリーンショット。](./media/onboard-server/windows-resource-group.png)

4. [`az-connect-win`](https://github.com/microsoft/azure_arc/blob/main/azure_arc_servers_jumpstart/scripts/az_connect_win.ps1) PowerShell スクリプトをダウンロードします。

5. 実際の環境に合わせて環境変数を変更し、指定されたマシンにスクリプトをコピーします。

    ![変更する環境変数のスクリーンショット。](./media/onboard-server/windows-variables.png)

## <a name="deployment"></a>デプロイ

指定されたマシンで **管理者として** PowerShell ISE を開き、スクリプトを実行します。 スクリプトのエージェントのインストール パスには `$env:ProgramFiles` が使用されているので、**PowerShell ISE (x86) は使用しない** ようにしてください。

!['azcmagent connect' コマンドのスクリーンショット。](./media/onboard-server/azcmagent.png)

!['az-connect' Windows スクリプトのスクリーンショット。](./media/onboard-server/az-connect-windows-2.png)

完了すると、リソース グループ内の新しい Azure Arc リソースとして Windows Server インスタンスが接続されます。

![実行中の 'az_connect' Windows スクリプトのスクリーンショット。](./media/onboard-server/az-connect-windows.png)

![Azure portal の Azure Arc 対応リソースのスクリーンショット。](./media/onboard-server/windows-resource.png)

![Azure portal の Azure Arc 対応リソースの詳細のスクリーンショット。](./media/onboard-server/windows-resource-detail.png)

## <a name="delete-the-deployment"></a>デプロイの削除

サーバーを削除するには、サーバーを選択して Azure portal から削除します。

![Azure portal のリソースを削除するオプションのスクリーンショット。](./media/onboard-server/windows-delete-resource.png)

デプロイ全体を削除するには、Azure リソース グループを Azure portal から削除します。

![Azure portal を介してリソース グループを削除するオプションのスクリーンショット。](./media/onboard-server/windows-delete-resource-group.png)
