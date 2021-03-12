---
title: Terraform プランを使用して Google Cloud Platform Windows インスタンスをデプロイし、Azure Arc に接続する
description: Terraform プランを使用して Google Cloud Platform Windows インスタンスをデプロイし、Azure Arc に接続します。
author: likamrat
ms.author: brblanch
ms.date: 01/29/2021
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: operate
ms.custom: think-tank, e2e-hybrid
ms.openlocfilehash: 33bfa7ef30dabe154c6547f83185160ddd0d3340
ms.sourcegitcommit: b8f8b7631aabaab28e9705934bf67dad15e3a179
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101800746"
---
# <a name="use-a-terraform-plan-to-deploy-a-google-cloud-platform-windows-instance-and-connect-it-to-azure-arc"></a>Terraform プランを使用して Google Cloud Platform Windows インスタンスをデプロイし、Azure Arc に接続する

この記事では、指定された [Terraform](https://www.terraform.io/) プランを使用して Windows Server GCP インスタンスをデプロイし、Azure Arc 対応サーバー リソースとして接続するためのガイダンスを提供します。

## <a name="prerequisites"></a>前提条件

1. Azure Arc Jumpstart リポジトリをクローンします。

    ```console
    git clone https://github.com/microsoft/azure_arc.git
    ```

2. [バージョン 2.7 以降の Azure CLI をインストールするか、更新します](/cli/azure/install-azure-cli)。 現在インストールされているバージョンを確認するには、次のコマンドを使用してください。

    ```console
    az --version
    ```

3. [0.12 以降の Terraform をインストールします](https://learn.hashicorp.com/tutorials/terraform/install-cli)

4. **課金が有効になっている Google Cloud アカウント:** [無料試用版アカウントを作成します](https://cloud.google.com/free)。 Windows Server 仮想マシンを作成するには、アカウントをアップグレードして課金を有効にする必要があります。 メニューから **[Billing]\(お支払い\)** を選択し、右下にある **[Upgrade]\(アップグレード\)** を選択します。

    ![GCP アカウントで課金を有効にする方法を示す最初のスクリーンショット。](./media/gcp-windows/billing-1.png)

    ![GCP アカウントで課金を有効にする方法を示す 2 番目のスクリーンショット。](./media/gcp-windows/billing-2.png)

    ![GCP アカウントで課金を有効にする方法を示す 3 番目のスクリーンショット。](./media/gcp-windows/billing-3.png)

    **免責事項:** 予期しない料金を回避するには、この記事の最後にある「デプロイの削除」セクションに従ってください。

5. Azure サービス プリンシパルを作成します。

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

## <a name="create-a-new-gcp-project"></a>新しい GCP プロジェクトを作成する

1. [Google API コンソール](https://console.cloud.google.com)に移動し、お使いの Google アカウントでサインインします。 ログインしたら、`Azure Arc demo` という名前の[新しいプロジェクトを作成します](https://cloud.google.com/resource-manager/docs/creating-managing-projects)。 作成した後は、プロジェクト ID を必ずコピーしてください。通常、これはプロジェクト名とは異なるためです。

    ![GCP コンソールの **[新しいプロジェクト]** ページの最初のスクリーンショット。](./media/gcp-windows/new-project-1.png)

    ![GCP コンソールの **[新しいプロジェクト]** ページの 2 番目のスクリーンショット。](./media/gcp-windows/new-project-2.png)

2. 新しいプロジェクトが作成され、ページ上部にあるドロップダウンで選択されたら、プロジェクトに対して Compute Engine API のアクセスを有効にする必要があります。 **[+ Enable APIs and Services]\(API とサービスを有効にする\)** をクリックし、"*compute engine*" を検索します。 次に、 **[Enable]\(有効にする\)** を選択して、API アクセスを有効にします。

    ![GCP コンソールの **Compute Engine API** の最初のスクリーンショット。](./media/gcp-windows/comp-eng-api-1.png)

    ![GCP コンソールの **Compute Engine API** の 2 番目のスクリーンショット。](./media/gcp-windows/comp-eng-api-2.png)

3. 次に、Terraform で GCP プロジェクトのリソースを作成および管理するために使用されるサービス アカウント キーを設定します。 [サービス アカウント キーの作成ページ](https://console.cloud.google.com/apis/credentials/serviceaccountkey)にアクセスします。 ドロップダウンから **[新しいサービス アカウント]** を選択し、名前を指定し、プロジェクト、ロールとして所有者、キーの種類として JSON を選択し、 **[作成]** を選択します。 これにより、Terraform がリソースを管理するために必要なすべての資格情報を含む JSON ファイルがダウンロードされます。 ダウンロードした JSON ファイルを `azure_arc_servers_jumpstart/gcp/windows/terraform` ディレクトリにコピーします。

    ![GCP コンソールでのサービス アカウントの作成方法を示すスクリーンショット。](./media/gcp-windows/svc-account.png)

## <a name="deployment"></a>デプロイ

Terraform プランを実行する前に、プランで使用される環境変数を設定し、エクスポートする必要があります。 これらの変数は、先ほど作成した Azure サービス プリンシパル、Azure サブスクリプションとテナント、GCP プロジェクト名に基づいています。

1. `az account list` コマンドを使用して、Azure サブスクリプション ID とテナント ID を取得します。

2. Terraform プランでは、Microsoft Azure と Google Cloud Platform の両方にリソースが作成されます。 次に、GCP 仮想マシンでスクリプトが実行され、Azure Arc エージェントと必要なすべてのアーティファクトがインストールされます。 このスクリプトには、GCP および Azure 環境に関する特定の情報が必要です。 [`scripts/vars.sh`](https://github.com/microsoft/azure_arc/blob/main/azure_arc_servers_jumpstart/gcp/windows/terraform/scripts/vars.sh) を編集し、各変数を適切な値で更新します。

    - `TF-VAR-subscription-id` = お使いの Azure サブスクリプション ID
    - `TF-VAR-client-id` = お使いの Azure サービス プリンシパル アプリケーション ID
    - `TF-VAR-client-secret` = お使いの Azure サービス プリンシパル パスワード
    - `TF-VAR-tenant-id` = お使いの Azure テナント ID
    - `TF-VAR-gcp-project-id` = GCP プロジェクト ID
    - `TF-VAR-gcp-credentials-filename` = GCP 資格情報 JSON ファイル名

3. CLI から、クローンされたリポジトリの `azure_arc_servers_jumpstart/gcp/windows/terraform` ディレクトリに移動します。

4. 以下に示すように、source コマンドを使って [`scripts/vars.sh`](https://github.com/microsoft/azure_arc/blob/main/azure_arc_servers_jumpstart/gcp/windows/terraform/scripts/vars.sh) を実行することにより、編集した環境変数をエクスポートします。 Terraform ではプランを適切に実行するために、これらを設定する必要があります。

    ```console
    source ./scripts/vars.sh
    ```

5. Terraform AzureRM プロバイダーをダウンロードする `terraform init` コマンドを実行します。

    ![`terraform init` コマンドのスクリーンショット。](./media/gcp-windows/terraform-init.png)

6. 次に、`terraform apply --auto-approve` コマンドを実行して、プランが終了するまで待ちます。 Terraform スクリプトが完了すると、GCP Windows Server 2019 VM がデプロイされ、VM に Azure Arc エージェントをダウンロードして、VM を新しい Azure Arc 対応サーバーとして新しい Azure リソース グループ内に接続するスクリプトが開始されます。 エージェントがプロビジョニングを完了するまでに数分かかりますので、休憩しましょう。

    ![`terraform apply` コマンドのスクリーンショット。](./media/gcp-windows/terraform-apply.png)

7. 数分後に Azure portal を開くと、`arc-gcp-demo` リソース グループに移動できるようになっているはずです。 GCP に作成された Windows Server 仮想マシンがリソースとして表示されます。

    ![Azure portal 内の Azure Arc 対応サーバーのスクリーンショット。](./media/gcp-windows/server.png)

## <a name="semi-automated-deployment-optional"></a>半自動デプロイ (省略可能)

Terraform プランは、VM が最初に起動されたときに PowerShell スクリプトを実行して Azure Arc エージェントを自動的にインストールし、VM を管理対象リソースとして Azure に接続します。

![`azcmagent connect` コマンドのスクリーンショット。](./media/gcp-windows/azcmagent-connect.png)

実際の登録プロセスのデモまたは制御を行う場合は、次の手順を行います。

1. `terraform apply` コマンドを実行する前に [`main.tf`](https://github.com/microsoft/azure_arc/blob/main/azure_arc_servers_jumpstart/gcp/windows/terraform/main.tf) を開き、`windows-startup-script-ps1 = local-file.install_arc_agent-ps1.content` 行をコメント アウトしてファイルを保存します。

    ![Azure Arc エージェントの自動オンボードを無効にするためにコメント アウトされた '' を示すスクリーンショット。](./media/gcp-windows/main-tf.png)

2. 上記の指示に従って `terraform apply --auto-approve` を実行します。

3. GCP コンソールを開き、[コンピューティング インスタンス ページ](https://console.cloud.google.com/compute/instances)に移動して、作成した VM を選択します。

    ![GCP コンソールでのサーバーのスクリーンショット。](./media/gcp-windows/gcp-server.png)

    ![GCP 内で Windows サーバーのパスワードをリセットする方法を示すスクリーンショット。](./media/gcp-windows/reset-password.png)

4. **[Set Password]\(パスワードの設定\)** を選択し、ユーザー名を指定して VM のユーザーとパスワードを作成します。

    ![GCP 内で Windows サーバーのユーザー名とパスワードを設定する方法を示すスクリーンショット。](./media/gcp-windows/name-pword.png)

5. GCP コンソールの VM ページで [RDP] ボタンを選択して仮想マシンに RDP 接続し、先ほど作成したユーザー名とパスワードでサインインします。

    ![GCP インスタンスに RDP 接続する方法を示すスクリーンショット。](./media/gcp-windows/gcp-rdp.png)

6. ログインしたら、**管理者として** PowerShell ISE を開きます。 PowerShell ISE の x86 バージョンではなく、x64 バージョンを実行していることを確認してください。 開いたら、 **[File]\(ファイル\) > [New]\(新規作成\)** を選択して、空の `.ps1` ファイルを作成します。 次に、`./scripts/install_arc_agent.ps1` の内容全体を貼り付けます。 [play\(実行\)] ボタンをクリックして、スクリプトを実行します。 完了すると、マシンが正常にオンボードしたことを示す出力が表示されるはずです。

    ![Azure Arc エージェント接続スクリプトとともに Windows Powershell Integrated Scripting Environment を示すスクリーンショット。](./media/gcp-windows/ise-script.png)

## <a name="delete-the-deployment"></a>デプロイの削除

このデモの一部として作成したすべてのリソースを削除するには、次のように `terraform destroy --auto-approve` コマンドを使用します。

  ![`terraform destroy` コマンドのスクリーンショット。](./media/gcp-windows/terraform-destroy.png)

または、[GCP コンソール](https://console.cloud.google.com/compute/instances)から直接 GCP VM を削除することもできます。

  ![GCP コンソールから仮想マシンを削除する方法を示すスクリーンショット。](./media/gcp-windows/delete-vm.png)
