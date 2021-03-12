---
title: Terraform プランを使用して Google Cloud Platform Ubuntu インスタンスをデプロイし、Azure Arc に接続する
description: Terraform プランを使用して Google Cloud Platform Ubuntu インスタンスをデプロイし、Azure Arc に接続します。
author: likamrat
ms.author: brblanch
ms.date: 01/29/2021
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: operate
ms.custom: think-tank, e2e-hybrid
ms.openlocfilehash: ba39a2e2b8bd9e226c8d81599de1920b9878b9e0
ms.sourcegitcommit: b8f8b7631aabaab28e9705934bf67dad15e3a179
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101801405"
---
# <a name="use-a-terraform-plan-to-deploy-a-google-cloud-platform-ubuntu-instance-and-connect-it-to-azure-arc"></a>Terraform プランを使用して Google Cloud Platform Ubuntu インスタンスをデプロイし、Azure Arc に接続する

この記事では、指定された [Terraform](https://www.terraform.io/) プランを使用して Google Cloud Platform (GCP) インスタンスをデプロイし、Azure Arc 対応サーバー リソースとして接続するためのガイダンスを提供します。

## <a name="prerequisites"></a>前提条件

1. Azure Arc Jumpstart リポジトリを複製します。

    ```console
    git clone https://github.com/microsoft/azure_arc.git
    ```

2. [バージョン 2.7 以降の Azure CLI をインストールするか、更新します](/cli/azure/install-azure-cli)。 次のコマンドを使用して、現在インストールされているバージョンを確認します。

    ```console
    az --version
    ```

3. [SSH キーを生成します](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/) (または既存の SSH キーを使用します)

4. [無料の Google Cloud Platform アカウントを作成します](https://cloud.google.com/free)

5. [0.12 以降の Terraform をインストールします](https://learn.hashicorp.com/tutorials/terraform/install-cli)

6. Azure サービス プリンシパルを作成します。

    GCP 仮想マシンを Azure Arc に接続するには、共同作成者ロールが割り当てられた Azure サービス プリンシパルが必要です。 これを作成するには、Azure アカウントにサインインして、次のコマンドを実行します。 このコマンドは、[Azure Cloud Shell](https://shell.azure.com/) 内で実行することもできます。

    ```console
    az login
    az ad sp create-for-rbac -n "<Unique SP Name>" --role contributor
    ```

    次に例を示します。

    ```console
    az ad sp create-for-rbac -n "http://AzureArcGCP" --role contributor
    ```

    出力は次のようになります。

    ```json
    {
      "appId": "XXXXXXXXXXXXXXXXXXXXXXXXXXXX",
      "displayName": "AzureArcGCP",
      "name": "http://AzureArcGCP",
      "password": "XXXXXXXXXXXXXXXXXXXXXXXXXXXX",
      "tenant": "XXXXXXXXXXXXXXXXXXXXXXXXXXXX"
    }
    ```

    > [!NOTE]
    > サービス プリンシパルのスコープを、特定の [Azure サブスクリプションとリソース グループ](/cli/azure/ad/sp)に限定することを強くお勧めします。

<!-- docutune:casing "Compute Engine API" -->

## <a name="create-a-new-gcp-project"></a>新しい GCP プロジェクトを作成する

1. [Google API コンソール](https://console.developers.google.com)に移動し、お使いの Google アカウントでサインインします。 ログインしたら、`Azure Arc demo` という名前の[新しいプロジェクトを作成します](https://cloud.google.com/resource-manager/docs/creating-managing-projects)。 作成した後は、プロジェクト ID を必ずコピーしてください。通常、これはプロジェクト名とは異なるためです。

    ![GCP コンソールの **[新しいプロジェクト]** ページを示す最初のスクリーンショット。](./media/gcp-ubuntu/ubuntu-new-project-1.png)

    ![GCP コンソールの **[新しいプロジェクト]** ページを示す 2 番目のスクリーンショット。](./media/gcp-ubuntu/ubuntu-new-project-2.png)

2. 新しいプロジェクトが作成され、ページ上部にあるドロップダウンで選択されたら、プロジェクトに対して Compute Engine API のアクセスを有効にする必要があります。 **[+ Enable APIs and Services]\(API とサービスを有効にする\)** をクリックし、"*compute engine*" を検索します。 次に、 **[Enable]\(有効にする\)** を選択して、API アクセスを有効にします。

    ![GCP コンソールの **Compute Engine API** を示す最初のスクリーンショット。](./media/gcp-ubuntu/ubuntu-comp-eng-api-1.png)

    ![GCP コンソールの **Compute Engine API** を示す 2 番目のスクリーンショット。](./media/gcp-ubuntu/ubuntu-comp-eng-api-2.png)

3. 次に、Terraform で GCP プロジェクトのリソースを作成および管理するために使用されるサービス アカウント キーを設定します。 [サービス アカウント キーの作成ページ](https://console.cloud.google.com/apis/credentials/serviceaccountkey)にアクセスします。 ドロップダウンから **[新しいサービス アカウント]** を選択し、名前を指定し、プロジェクト、ロールとして所有者、キーの種類として JSON を選択し、 **[作成]** を選択します。 これによって、Terraform でリソースを管理するために必要なすべての資格情報を含む JSON ファイルがダウンロードされます。 ダウンロードした JSON ファイルを `azure_arc_servers_jumpstart/gcp/ubuntu/terraform` ディレクトリにコピーします。

    ![GCP コンソールでのサービス アカウントの作成方法を示すスクリーンショット。](./media/gcp-ubuntu/ubuntu-svc-account.png)

4. 最後に、SSH キーが `~/.ssh` で使用可能であり、かつ `id-rsa.pub` および `id-rsa` という名前であることを確認します。 上記の ssh-keygen ガイドに従ってキーを作成した場合は、既に正しくセットアップされているはずです。 されていない場合は、別のパスのキーを使用するように [`main.tf`](https://github.com/microsoft/azure_arc/blob/main/azure_arc_servers_jumpstart/gcp/ubuntu/terraform/main.tf) を変更する必要があります。

## <a name="deployment"></a>デプロイ

Terraform プランを実行する前に、プランで使用される環境変数をエクスポートする必要があります。 これらの変数は、先ほど作成した Azure サービス プリンシパル、Azure サブスクリプションとテナント、GCP プロジェクト名に基づいています。

1. `az account list` コマンドを使用して、Azure サブスクリプション ID とテナント ID を取得します。

2. Terraform プランでは、Microsoft Azure と Google Cloud Platform の両方にリソースが作成されます。 次に、GCP 仮想マシンでスクリプトが実行されて、Azure Arc エージェントと必要なすべての成果物がインストールされます。 このスクリプトには、GCP および Azure 環境に関する特定の情報が必要です。 [`scripts/vars.sh`](https://github.com/microsoft/azure_arc/blob/main/azure_arc_servers_jumpstart/gcp/ubuntu/terraform/scripts/vars.sh) を編集し、各変数を適切な値で更新します。

    - `TF-VAR-subscription-id` = お使いの Azure サブスクリプション ID
    - `TF-VAR-client-id` = お使いの Azure サービス プリンシパル アプリケーション ID
    - `TF-VAR-client-secret` = お使いの Azure サービス プリンシパル パスワード
    - `TF-VAR-tenant-id` = お使いの Azure テナント ID
    - `TF-VAR-gcp-project-id` = GCP プロジェクト ID
    - `TF-VAR-gcp-credentials-filename` = GCP 資格情報 JSON ファイル名

3. CLI から、クローンされたリポジトリの `azure_arc_servers_jumpstart/gcp/ubuntu/terraform` ディレクトリに移動します。

4. 下に示すように、source コマンドを使って [`scripts/vars.sh`](https://github.com/microsoft/azure_arc/blob/main/azure_arc_servers_jumpstart/gcp/ubuntu/terraform/scripts/vars.sh) を実行することにより、編集した環境変数をエクスポートします。 Terraform ではプランを適切に実行するために、これらを設定する必要があります。 このスクリプトは、Terraform のデプロイの一部として、GCP 仮想マシンでも自動的にリモートで実行されることに注意してください。

    ```console
    source ./scripts/vars.sh
    ```

5. Terraform AzureRM プロバイダーをダウンロードする `terraform init` コマンドを実行します。

    !["terraform init" コマンドのスクリーンショット。](./media/gcp-ubuntu/ubuntu-terraform-init.png)

6. 次に、`terraform apply --auto-approve` コマンドを実行して、プランが終了するまで待ちます。 完了すると、GCP Ubuntu VM がデプロイされ、新しいリソース グループ内の新しい Azure Arc 対応サーバーとして接続されます。

7. Azure portal を開き、`arc-gcp-demo` リソース グループに移動します。 GCP に作成された仮想マシンはリソースとして表示されます。

    ![Azure portal 内の Azure Arc 対応サーバーのスクリーンショット。](./media/gcp-ubuntu/ubuntu-server.png)

## <a name="semi-automated-deployment-optional"></a>半自動デプロイ (省略可能)

お気付きかもしれませんが、実行の最後の段階では、VM を新しい Azure Arc 対応サーバー リソースとして登録します。

!["azcmagent connect" コマンド実行のスクリーンショット。](./media/gcp-ubuntu/ubuntu-azcmagent-connect.png)

実際の登録プロセスのデモまたは制御を行う場合は、次の手順を行います。

1. [`install_arc_agent.sh.tmpl`](https://github.com/microsoft/azure_arc/blob/main/azure_arc_servers_jumpstart/gcp/ubuntu/terraform/scripts/install_arc_agent.sh.tmpl) スクリプト テンプレートで `run connect command` セクションをコメント アウトし、ファイルを保存します。

    ![Azure Arc エージェントの自動オンボードを無効にするためにコメント アウトされた "main.tf" を示すスクリーンショット。](./media/gcp-ubuntu/ubuntu-main-tf.png)

2. `terraform output` を実行して、GCP VM のパブリック IP を取得します。

    ![Terraform の出力のスクリーンショット。](./media/gcp-ubuntu/ubuntu-terraform.png)

3. `ssh arcadmin@xx.xx.xx.xx` を使用して VM を SSH 接続し ます。ここで、`xx.xx.xx.xx` はホスト IP です。

    ![GCP サーバーに接続する SSH キーのスクリーンショット。](./media/gcp-ubuntu/ubuntu-ssh.png)

4. [`vars.sh`](https://github.com/microsoft/azure_arc/blob/main/azure_arc_servers_jumpstart/gcp/ubuntu/terraform/scripts/vars.sh) のすべての環境変数をエクスポートします。

    !["vars.sh" でエクスポートする環境変数のスクリーンショット。](./media/gcp-ubuntu/ubuntu-export-variables.png)

5. 次のコマンドを実行します。

    ```console azcmagent connect --service-principal-ID $tf-VAR-client-ID --service-principal-secret $tf-VAR-client-secret --resource-group "Azure Arc gcp-demo" --tenant-ID $tf-VAR-tenant-ID --location "westus2" --subscription-ID $tf-VAR-subscription-ID
    ```

    !["azcmagent connect" コマンドの正常完了を示すスクリーンショット。](./media/gcp-ubuntu/ubuntu-azcmagent.png)

6. 完了すると、VM は Azure Arc に登録され、Azure portal 経由でリソース グループに表示されます。

## <a name="delete-the-deployment"></a>デプロイの削除

このデモの一部として作成したすべてのリソースを削除するには、下のように `terraform destroy --auto-approve` コマンドを使用します。

!["terraform destroy" コマンドのスクリーンショット。](./media/gcp-ubuntu/ubuntu-terraform-destroy.png)

または、[GCP コンソール](https://console.cloud.google.com/compute/instances)から直接 GCP VM を削除することもできます。

![GCP コンソールから仮想マシンを削除する方法を示すスクリーンショット。](./media/gcp-ubuntu/ubuntu-delete-vm.png)
