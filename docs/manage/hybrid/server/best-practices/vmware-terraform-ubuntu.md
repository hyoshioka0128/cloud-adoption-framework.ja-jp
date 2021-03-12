---
title: Terraform プランを使用して VMware Ubuntu 仮想マシンをデプロイして Azure Arc に接続する
description: Terraform プランを使用して VMware Ubuntu 仮想マシンをデプロイし、これを Azure Arc に接続します。
author: likamrat
ms.author: brblanch
ms.date: 01/29/2021
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: operate
ms.custom: think-tank, e2e-hybrid
ms.openlocfilehash: 99e146fe7d1a7218efc5de212c163ea87739d557
ms.sourcegitcommit: b8f8b7631aabaab28e9705934bf67dad15e3a179
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101801358"
---
# <a name="use-a-terraform-plan-to-deploy-a-vmware-ubuntu-virtual-machine-and-connect-it-to-azure-arc"></a>Terraform プランを使用して VMware Ubuntu 仮想マシンをデプロイして Azure Arc に接続する

この記事では、提供された [Terraform](https://www.terraform.io/) プランを使用して Ubuntu サーバー、VMware vSphere 仮想マシンをデプロイし、これを Azure Arc 対応サーバー リソースとして接続するためのガイダンスを提供します。

## <a name="prerequisites"></a>前提条件

1. Azure Arc Jumpstart リポジトリを複製します。

    ```console
    git clone https://github.com/microsoft/azure_arc.git
    ```

2. [バージョン 2.7 以降の Azure CLI をインストールするか、更新します。](/cli/azure/install-azure-cli) 現在インストールされているバージョンを確認するには、次のコマンドを使用してください。

    ```console
    az --version
    ```

3. [0.12 以降の Terraform をインストールします](https://learn.hashicorp.com/tutorials/terraform/install-cli)

4. VSphere Web クライアントのテンプレートから仮想マシンを[デプロイするためのアクセス許可](https://docs.vmware.com/en/VMware-vSphere/7.0/com.vmware.vsphere.vm_admin.doc/GUID-4D0F8E63-2961-4B71-B365-BBFA24673FDB.html)を持つ VMware vCenter Server ユーザー。

5. Azure サービス プリンシパルを作成します。

    VMware vSphere 仮想マシンを Azure Arc に接続するには、共同作成者ロールが割り当てられた Azure サービス プリンシパルが必要です。 これを作成するには、自分の Azure アカウントにサインインして、次のコマンドを実行します。 このコマンドは、[Azure Cloud Shell](https://shell.azure.com/) 内で実行することもできます。

    ```bash
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

### <a name="preparing-an-ubuntu-server-vmware-vsphere-vm-template"></a>Ubuntu サーバー VMware vSphere VM テンプレートの準備

下のガイドを使用して Ubuntu サーバー VM をデプロイしてそれを Azure Arc に接続する前に、VMware vSphere テンプレートが必要です。 [この記事](./vmware-ubuntu-template.md)では、VMware vSphere 6.5 以降を使用してこのようなテンプレートを簡単に作成する方法について説明します。

> [!NOTE]
> Ubuntu サーバー VM テンプレートを既にお持ちの場合でも、このガイドを参照用に使用することをお勧めします。

## <a name="deployment"></a>デプロイ

Terraform プランを実行する前に、プランで使用される環境変数を設定する必要があります。 これらの変数は、先ほど作成した Azure サービス プリンシパル、Azure サブスクリプションとテナント、VMware vSphere の資格情報に基づいています。

1. `az account list` コマンドを使用して、Azure サブスクリプション ID とテナント ID を取得します。

2. Terraform プランでは、Microsoft Azure と VMware vSphere の両方にリソースが作成されます。 次に、仮想マシンでスクリプトが実行されて、Azure Arc エージェントと必要なすべてのアーティファクトがインストールされます。 このスクリプトには、VMware vSphere および Azure 環境に関する特定の情報が必要です。 [`scripts/vars.sh`](https://github.com/microsoft/azure_arc/blob/main/azure_arc_servers_jumpstart/vmware/ubuntu/terraform/scripts/vars.sh) を編集し、各変数を適切な値で更新します。

    - `TF-VAR-subscription-id` = お使いの Azure サブスクリプション ID
    - `TF-VAR-client-id` = お使いの Azure サービス プリンシパル名
    - `TF-VAR-client-secret` = お使いの Azure サービス プリンシパル パスワード
    - `TF-VAR-tenant-id` = お使いの Azure テナント ID
    - `TF-VAR-resourceGroup` = Azure リソース グループ名
    - `TF-VAR-location` = Azure リージョン
    - `TF-VAR-vsphere-user` = vCenter 管理者ユーザー名
    - `TF-VAR-vsphere-password` = vCenter 管理者パスワード
    - `TF-VAR-vsphere-server` = vCenter サーバー FQDN/IP
    - `TF-VAR-admin-user` = OS 管理者ユーザー名
    - `TF-VAR-admin-password` = OS 管理者パスワード

3. CLI から、複製されたリポジトリの `azure_arc_servers_jumpstart/vmware/ubuntu/terraform` ディレクトリに移動します。

4. 下に示すように、ソース コマンドを使用して [`scripts/vars.sh`](https://github.com/microsoft/azure_arc/blob/main/azure_arc_servers_jumpstart/vmware/ubuntu/terraform/scripts/vars.sh) を実行することにより、編集した環境変数をエクスポートします。 Terraform ではプランを適切に実行するために、これらを設定する必要があります。 このスクリプトは、Terraform のデプロイの一部として、仮想マシンでも自動的にリモートで実行されることに注意してください。

    `source ./scripts/vars.sh`

5. エクスポートした `TF-VAR` 環境変数に加えて、[`terraform.tfvars`](https://github.com/microsoft/azure_arc/blob/main/azure_arc_servers_jumpstart/vmware/ubuntu/terraform/terraform.tfvars) の Terraform 変数を VMware vSphere 環境に合わせて編集します。

    ![TF-VAR 環境変数のスクリーンショット](./media/vmware-terraform-ubuntu/variables.png)

6. Terraform AzureRM、local、および vSphere プロバイダーをダウンロードする `terraform init` コマンドを実行します。

    ![`terraform init` コマンドのスクリーンショット。](./media/vmware-terraform-ubuntu/terraform-init.png)

7. `terraform apply --auto-approve` コマンドを実行して、プランが終了するまで待ちます。

8. Terraform のデプロイが完了すると、新しい Ubuntu サーバー VM が稼働し、新しく作成された Azure リソース グループ内に Azure Arc 対応サーバー リソースとして投影されます。

    ![`terraform apply` の完了を示すスクリーンショット。](./media/vmware-terraform-ubuntu/terraform-apply.png)

    ![新しい VMware vSphere Ubuntu サーバーの仮想マシンを示すスクリーンショット。](./media/vmware-terraform-ubuntu/new-vm.png)

    ![Azure リソース グループ内の Azure Arc 対応サーバーを示すスクリーンショット。](./media/vmware-terraform-ubuntu/server-1.png)

    ![Azure リソース グループ内の Azure Arc 対応サーバーを示す別のスクリーンショット。](./media/vmware-terraform-ubuntu/server-2.png)

## <a name="delete-the-deployment"></a>デプロイの削除

- 最も簡単な方法は、Azure portal を使用して Azure Arc リソースを削除することであり、単にリソースを選択して削除します。 さらに、VMware vSphere VM を削除します。

  ![削除される Azure Arc 対応サーバーのスクリーンショット。](./media/vmware-terraform-ubuntu/delete-server.png)

  インスタンスを手動で削除する場合は、Terraform プランによって作成された *install-azure-arc-agent.sh* も削除する必要があります。

- 環境全体を破棄する場合は、下のように `terraform destroy --auto-approve` コマンドを使用します。

  ![`terraform destroy` コマンドのスクリーンショット。](./media/vmware-terraform-ubuntu/terraform-destroy.png)
