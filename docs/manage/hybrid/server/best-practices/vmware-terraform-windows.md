---
title: Terraform プランを使用して VMware Windows 仮想マシンをデプロイし、Azure Arc に接続する
description: Terraform プランを使用して VMware Windows 仮想マシンをデプロイし、Azure Arc に接続します。
author: likamrat
ms.author: brblanch
ms.date: 01/29/2021
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: operate
ms.custom: think-tank, e2e-hybrid
ms.openlocfilehash: 7bc86b6bc5bbba91794b27397c0a2582ca3cb999
ms.sourcegitcommit: 51565dc4d3a1858bd62f708f2e4c082fbd4c6fe4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/20/2021
ms.locfileid: "107747332"
---
# <a name="use-a-terraform-plan-to-deploy-a-vmware-windows-virtual-machine-and-connect-it-to-azure-arc"></a>Terraform プランを使用して VMware Windows 仮想マシンをデプロイし、Azure Arc に接続する

この記事では、指定された [Terraform](https://www.terraform.io/) プランを使用して Windows Server、VMware vSphere 仮想マシンをデプロイし、これを Azure Arc 対応サーバー リソースとして接続するためのガイダンスを提供します。

## <a name="prerequisites"></a>前提条件

1. Azure Arc Jumpstart リポジトリを複製します。

    ```console
    git clone https://github.com/microsoft/azure_arc.git
    ```

2. [バージョン 2.7 以降の Azure CLI をインストールするか、更新します](/cli/azure/install-azure-cli)。 現在インストールされているバージョンを確認するには、次のコマンドを使用してください。

    ```console
    az --version
    ```

3. [0.12 以降の Terraform をインストールします](https://learn.hashicorp.com/tutorials/terraform/install-cli)

4. vSphere Web クライアントのテンプレートから仮想マシンを[デプロイするためのアクセス許可](https://docs.vmware.com/en/VMware-vSphere/7.0/com.vmware.vsphere.vm_admin.doc/GUID-4D0F8E63-2961-4B71-B365-BBFA24673FDB.html)を持つ VMware vCenter Server ユーザー。

5. Azure サービス プリンシパルを作成します。

    VMware vSphere 仮想マシンを Azure Arc に接続するには、共同作成者ロールが割り当てられた Azure サービス プリンシパルが必要です。 これを作成するには、自分の Azure アカウントにサインインして、次のコマンドを実行します。 このコマンドは [Azure Cloud Shell](https://shell.azure.com/) で実行することもできます。

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

### <a name="prepare-a-windows-server-vmware-vsphere-vm-template"></a>Windows Server VMware vSphere VM テンプレートを準備する

このガイダンスを使用して Windows Server VM をデプロイしてそれを Azure Arc に接続する前に、VMware vSphere テンプレートが必要です。 [こうしたテンプレートは、VMware vSphere 6.5 以降を使用して簡単に作成](./vmware-windows-template.md)できます。

**Terraform プランは、WinRM プロトコルを使用して必要な Azure Arc スクリプトをコピーして実行する、`remote-exec` プロビジョナーを使用します。VM への WinRM 接続を許可するには、テンプレートに変換する前に [`allow_winrm`](https://github.com/microsoft/azure_arc/blob/main/azure_arc_servers_jumpstart/vmware/winsrv/terraform/scripts/allow_winrm.ps1) PowerShell スクリプトを VM 上で実行します。**

> [!NOTE]
> Windows Server VM テンプレートを既にお持ちの場合でも、このガイドを参照用に使用する必要があります。

## <a name="deployment"></a>デプロイ

Terraform プランを実行する前に、プランで使用される環境変数を設定する必要があります。 これらの変数は、先ほど作成した Azure サービス プリンシパル、Azure サブスクリプションとテナント、VMware vSphere の資格情報に基づいています。

1. `az account list` コマンドを使用して、Azure サブスクリプション ID とテナント ID を取得します。

2. Terraform プランでは、Microsoft Azure と VMware vSphere の両方にリソースが作成されます。 次に、仮想マシンでスクリプトが実行され、Azure Arc エージェントと必要なすべてのアーティファクトがインストールされます。 このスクリプトには、VMware vSphere および Azure 環境に関する特定の情報が必要です。 [`scripts/vars.sh`](https://github.com/microsoft/azure_arc/blob/main/azure_arc_servers_jumpstart/vmware/winsrv/terraform/scripts/vars.sh) を編集し、各変数を適切な値で更新します。

    - `TF_VAR_subscription_id` = お使いの Azure サブスクリプション ID
    - `TF_VAR_client_id` = お使いの Azure サービス プリンシパル名
    - `TF_VAR_client_secret` = お使いの Azure サービス プリンシパル パスワード
    - `TF_VAR_tenant_id` = お使いの Azure テナント ID
    - `TF_VAR_resourceGroup` = Azure リソース グループ名
    - `TF_VAR_location` = Azure リージョン
    - `TF_VAR_vsphere_user` = vCenter 管理者ユーザー名
    - `TF_VAR_vsphere_password` = vCenter 管理者パスワード
    - `TF_VAR_vsphere_server` = vCenter サーバー FQDN/IP
    - `TF_VAR_admin_user` = OS 管理者ユーザー名
    - `TF_VAR_admin_password` = OS 管理者パスワード

3. CLI から、クローンされたリポジトリの `azure_arc_servers_jumpstart/vmware/winsrv/terraform` ディレクトリに移動します。

4. 下に示すように、source コマンドを使って [`scripts/vars.sh`](https://github.com/microsoft/azure_arc/blob/main/azure_arc_servers_jumpstart/vmware/winsrv/terraform/scripts/vars.sh) を実行することにより、編集した環境変数をエクスポートします。 Terraform ではプランを適切に実行するために、これらを設定する必要があります。 このスクリプトは、Terraform のデプロイの一部として、仮想マシンでも自動的にリモートで実行されることに注意してください。

    ```console
    source ./scripts/vars.sh
    ```

5. エクスポートした `TF_VAR` 環境変数に加えて、[`terraform.tfvars`](https://github.com/microsoft/azure_arc/blob/main/azure_arc_servers_jumpstart/vmware/winsrv/terraform/terraform.tfvars) の Terraform 変数を VMware vSphere 環境に合わせて編集します。

    !["TF-VAR" 環境変数のスクリーンショット](./media/vmware-terraform-windows/windows-variables.png)

6. Terraform AzureRM、local、および vSphere プロバイダーをダウンロードする `terraform init` コマンドを実行します。

    ![`terraform init` コマンドのスクリーンショット。](./media/vmware-terraform-windows/terraform-init.png)

7. `terraform apply --auto-approve` コマンドを実行して、プランが終了するまで待ちます。 Terraform のデプロイが完了すると、新しい Windows Server VM が稼働し、新しく作成された Azure リソース グループ内に Azure Arc サーバー リソースとして投影されます。

    ![`terraform apply` の完了を示すスクリーンショット。](./media/vmware-terraform-windows/terraform-apply.png)

    ![新しい VMware vSphere Windows Server 仮想マシンを示すスクリーンショット。](./media/vmware-terraform-windows/new-vm.png)

    ![Azure リソース グループ内の Azure Arc 対応サーバーを示すスクリーンショット。](./media/vmware-terraform-windows/server-1.png)

    ![Azure リソース グループ内の Azure Arc 対応サーバーを示す別のスクリーンショット。](./media/vmware-terraform-windows/server-2.png)

## <a name="delete-the-deployment"></a>デプロイの削除

- 最も簡単な方法は、Azure portal 経由で Azure Arc リソースを削除することです。リソースを選択して削除するだけです。 さらに、VMware vSphere VM を削除します。

    ![削除される Azure Arc 対応サーバーのスクリーンショット。](./media/vmware-terraform-windows/delete-server.png)

- インスタンスを手動で削除する場合は、Terraform プランによって作成された `install_arc_agent.ps1` も削除する必要があります。

- 環境全体を破棄する場合は、以下のように `terraform destroy --auto-approve` コマンドを使用します。

    ![`terraform destroy` コマンドのスクリーンショット。](./media/vmware-terraform-windows/terraform-destroy.png)
