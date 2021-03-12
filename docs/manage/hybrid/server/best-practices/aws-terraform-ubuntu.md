---
title: Terraform プランを使用してアマゾン ウェブ サービスの Amazon Elastic Compute Cloud インスタンスをデプロイし、Azure Arc に接続する
description: Terraform プランを使用してアマゾン ウェブ サービスの Amazon Elastic Compute Cloud インスタンスをデプロイし、Azure Arc に接続します。
author: likamrat
ms.author: brblanch
ms.date: 01/29/2021
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: operate
ms.custom: think-tank, e2e-hybrid
ms.openlocfilehash: abf59b66640f9c5ce569e5aed1df5007029bc566
ms.sourcegitcommit: b8f8b7631aabaab28e9705934bf67dad15e3a179
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101800749"
---
# <a name="use-a-terraform-plan-to-deploy-an-amazon-web-services-amazon-elastic-compute-cloud-instance-and-connect-it-to-azure-arc"></a>Terraform プランを使用してアマゾン ウェブ サービスの Amazon Elastic Compute Cloud インスタンスをデプロイし、Azure Arc に接続する

この記事では、指定された [Terraform](https://www.terraform.io/) プランを使用してアマゾン ウェブ サービス (AWS) の Amazon Elastic Compute Cloud (Amazon EC2) インスタンスをデプロイし、それを Azure Arc 対応サーバー リソースとして接続するためのガイダンスを提供します。

## <a name="prerequisites"></a>前提条件

1. Azure Arc Jumpstart リポジトリをクローンします。

    ```console
    git clone https://github.com/microsoft/azure_arc.git
    ```

2. [バージョン 2.7 以降の Azure CLI をインストールするか、更新します](/cli/azure/install-azure-cli)。 現在インストールされているバージョンを確認するには、次のコマンドを使用してください。

    ```console
    az --version
    ```

3. [SSH キーを生成します](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/) (または既存の SSH キーを使用します)

4. [無料の AWS アカウントを作成します](https://aws.amazon.com/premiumsupport/knowledge-center/create-and-activate-aws-account/)

5. [0.12 以降の Terraform をインストールします](https://learn.hashicorp.com/tutorials/terraform/install-cli)

6. Azure サービス プリンシパルを作成します。

    AWS 仮想マシンを Azure Arc に接続するには、共同作成者ロールが割り当てられた Azure サービス プリンシパルが必要です。 これを作成するには、Azure アカウントにサインインして、次のコマンドを実行します。 このコマンドは、[Azure Cloud Shell](https://shell.azure.com/) 内で実行することもできます。

    ```console
    az login
    az ad sp create-for-rbac -n "<Unique SP Name>" --role contributor
    ```

    次に例を示します。

    ```console
    az ad sp create-for-rbac -n "http://AzureArcAWS" --role contributor
    ```

    出力は次のようになります。

    ```json
    {
      "appId": "XXXXXXXXXXXXXXXXXXXXXXXXXXXX",
      "displayName": "AzureArcAWS",
      "name": "http://AzureArcAWS",
      "password": "XXXXXXXXXXXXXXXXXXXXXXXXXXXX",
      "tenant": "XXXXXXXXXXXXXXXXXXXXXXXXXXXX"
    }
    ```

    > [!NOTE]
    > サービス プリンシパルのスコープを、特定の [Azure サブスクリプションとリソース グループ](/cli/azure/ad/sp)に限定することを強くお勧めします。

## <a name="create-an-aws-identity"></a>AWS ID を作成する

Terraform で AWS にリソースを作成するには、適切なアクセス許可を持つ新しい AWS IAM ロールを作成し、それを使用するように Terraform を構成する必要があります。

1. [AWS マネジメント コンソール](https://console.aws.amazon.com/console/home)にサインインします

2. サインインした後、左上にある **[Services]\(サービス\)** ドロップダウンを選択します。 **[Security, Identity, and Compliance]\(セキュリティ、ID、およびコンプライアンス\)** で、 **[IAM]** を選択して、[ID およびアクセス管理のページ](https://console.aws.amazon.com/iam/home)にアクセスします

    ![AWS クラウド コンソールのスクリーンショット。](./media/aws-ubuntu/aws-ubuntu-console.png)

    ![AWS クラウド コンソールでの ID およびアクセス管理のスクリーンショット。](./media/aws-ubuntu/aws-ubuntu-iam.png)

3. 左側のメニューで **[ユーザー]** をクリックし、 **[ユーザーを追加]** を選択して新しい IAM ユーザーを作成します。

    ![AWS クラウド コンソールで新しいユーザーを作成しているスクリーンショット。](./media/aws-ubuntu/aws-ubuntu-new-user-1.png)

4. **[ユーザーを追加]** ページで、ユーザーに `Terraform` という名前を指定し、 **[プログラムによるアクセス]** チェックボックスをオンにして、 **[次のステップ]** を選択します。

    ![AWS クラウド コンソールで新しいユーザーを作成している 2 番目のスクリーンショット。](./media/aws-ubuntu/aws-ubuntu-new-user-2.png)

5. 次の **[アクセス許可の設定]** ページで、 **[既存のポリシーを直接アタッチ]** を選択し、スクリーンショットに示されているように **[AmazonEC2FullAccess]** の横のチェックボックスをオンにして、 **[次のステップ]** を選択します。

    ![AWS クラウド コンソールで新しいユーザーを作成している 3 番目のスクリーンショット。](./media/aws-ubuntu/aws-ubuntu-new-user-3.png)

6. **[タグ]** ページで、`azure-arc-demo` というキーを持つタグを割り当て、 **[次のステップ]** を選択して **[確認]** ページに進みます。

    ![AWS クラウド コンソールのタグのスクリーンショット。](./media/aws-ubuntu/aws-ubuntu-tags.png)

7. すべてが正しいことを確認し、準備ができたら **[ユーザーの作成]** を選択します。

    ![AWS クラウド コンソールでユーザーを作成している 4 番目のスクリーンショット。](./media/aws-ubuntu/aws-ubuntu-new-user-4.png)

8. ユーザーが作成されると、ユーザーのアクセス キー ID とシークレット アクセス キーが表示されます。 これらの値をコピーしてから **[閉じる]** を選択します。 次のページでは、これがどのように表示されるかの例を確認できます。 これらのキーを取得すると、これらを Terraform で使用して AWS リソースを作成できるようになります。

    ![AWS クラウド コンソールでユーザーが正常に作成されたスクリーンショット。](./media/aws-ubuntu/aws-ubuntu-new-user-5.png)

## <a name="configure-terraform"></a>Terraform を構成する

Terraform プランを実行する前に、プランで使用される環境変数をエクスポートする必要があります。 これらの変数は、Azure サブスクリプションとテナント、Azure サービス プリンシパル、および先ほど作成した AWS IAM ユーザーとキーに基づいています。

1. `az account list` コマンドを使用して、Azure サブスクリプション ID とテナント ID を取得します。

2. Terraform プランでは、Microsoft Azure と AWS の両方にリソースが作成されます。 次に、AWS EC2 仮想マシン上でスクリプトが実行され、Azure Arc エージェントと必要なすべてのアーティファクトがインストールされます。 このスクリプトには、AWS および Azure 環境に関する特定の情報が必要です。 [`scripts/vars.sh`](https://github.com/microsoft/azure_arc/blob/main/azure_arc_servers_jumpstart/aws/ubuntu/terraform/scripts/vars.sh) を編集し、各変数を適切な値で更新します。

    - `TF-VAR-subscription-id` = お使いの Azure サブスクリプション ID
    - `TF-VAR-client-id` = お使いの Azure サービス プリンシパル アプリ ID
    - `TF-VAR-client-secret` = お使いの Azure サービス プリンシパル パスワード
    - `TF-VAR-tenant-id` = お使いの Azure テナント ID
    - `AWS-ACCESS-KEY-ID` = AWS アクセス キー
    - `AWS-SECRET-ACCESS-KEY` = AWS シークレット キー

3. Azure CLI から、クローンされたリポジトリの `azure_arc_servers_jumpstart/aws/ubuntu/terraform` ディレクトリに移動します。

4. 以下に示すように、source コマンドを使って [`scripts/vars.sh`](https://github.com/microsoft/azure_arc/blob/main/azure_arc_servers_jumpstart/aws/ubuntu/terraform/scripts/vars.sh) を実行することにより、編集した環境変数をエクスポートします。 Terraform ではプランを適切に実行するために、これらを設定する必要があります。 このスクリプトは、Terraform のデプロイの一部として、AWS 仮想マシンでも自動的にリモートで実行されることに注意してください。

    ```console
    source ./scripts/vars.sh
    ```

5. SSH キーが *~/.ssh* で使用可能であり、かつ `id-rsa.pub` および `id-rsa` という名前であることを確認します。 上記の ssh-keygen ガイドに従ってキーを作成した場合は、既に正しくセットアップされているはずです。 されていない場合は、別のパスのキーを使用するように [`main.tf`](https://github.com/microsoft/azure_arc/blob/main/azure_arc_servers_jumpstart/aws/ubuntu/terraform/main.tf) を変更する必要があります。

6. Terraform AzureRM プロバイダーをダウンロードする `terraform init` コマンドを実行します。

    ![`terraform init` コマンドのスクリーンショット。](./media/aws-ubuntu/aws-ubuntu-terraform-init.png)

## <a name="deployment"></a>デプロイ

1. `terraform apply --auto-approve` コマンドを実行して、プランが終了するまで待ちます。 完了すると、AWS Amazon Linux 2 EC2 インスタンスがデプロイされ、新しいリソース グループ内の新しい Azure Arc 対応サーバーとして接続されます。

2. Azure portal を開き、arc-aws-demo のリソース グループに移動します。 AWS に作成された仮想マシンはリソースとして表示されます。

    ![Azure portal 内の Azure Arc 対応サーバーを示すスクリーンショット。](./media/aws-ubuntu/aws-ubuntu-server.png)

    ![EC2 インスタンスを表示している AWS コンソールのスクリーンショット。](./media/aws-ubuntu/aws-ubuntu-ec2-instances.png)

## <a name="semi-automated-deployment-optional"></a>半自動デプロイ (省略可能)

お気付きかもしれませんが、実行の最後の段階では、VM を新しい Azure Arc 対応サーバー リソースとして登録します。

  ![`azcmagent connect` コマンドのスクリーンショット。](./media/aws-ubuntu/aws-ubuntu-azcmagent-1.png)

実際の登録プロセスのデモまたは制御を行う場合は、次の手順を行います。

1. [`install_arc_agent.sh.tmpl`](https://github.com/microsoft/azure_arc/blob/main/azure_arc_servers_jumpstart/aws/ubuntu/terraform/scripts/install_arc_agent.sh.tmpl) スクリプト テンプレートで `run connect command` セクションをコメント アウトし、ファイルを保存します。

    ![Azure Arc エージェントの自動オンボードを無効にするためにコメント アウトされた '' を示すスクリーンショット。](./media/aws-ubuntu/aws-ubuntu-main-tf.png)

2. `terraform output` を実行して、AWS VM のパブリック IP を取得します。

    ![Terraform の出力のスクリーンショット。](./media/aws-ubuntu/aws-ubuntu-terraform.png)

3. `ssh ubuntu@xx.xx.xx.xx` を使用して VM に SSH 接続します。ここで、`xx.xx.xx.xx` はホストの IP です。

    ![EC2 サーバーに接続する SSH キーのスクリーンショット。](./media/aws-ubuntu/aws-ubuntu-ssh.png)

4. [`vars.sh`](https://github.com/microsoft/azure_arc/blob/main/azure_arc_servers_jumpstart/aws/ubuntu/terraform/scripts/vars.sh) のすべての環境変数をエクスポートします。

    !['' を使用してエクスポートする環境変数のスクリーンショット。](./media/aws-ubuntu/aws-ubuntu-export-variables.png)

5. 次のコマンドを実行します。

    ```console
    azcmagent connect --service-principal-id $TF-VAR-client-id --service-principal-secret $TF-VAR-client-secret --resource-group "arc-aws-demo" --tenant-id $TF-VAR-tenant-id --location "westus2" --subscription-id $TF-VAR-subscription-id
    ```

    ![`azcmagent connect` コマンドの別のスクリーンショット。](./media/aws-ubuntu/aws-ubuntu-azcmagent-2.png)

6. 完了すると、VM は Azure Arc に登録され、Azure portal 経由でリソース グループに表示されます。

## <a name="delete-the-deployment"></a>デプロイの削除

このデモの一部として作成したすべてのリソースを削除するには、次のように `terraform destroy --auto-approve` コマンドを使用します。

  ![`terraform destroy` コマンドのスクリーンショット。](./media/aws-ubuntu/aws-ubuntu-terraform-destroy.png)

または、[AWS コンソール](https://console.aws.amazon.com/ec2/v2/home)から AWS EC2 インスタンスを終了して、インスタンスを直接削除することもできます。 インスタンスが実際に削除されるまでに数分かかることに注意してください。

  ![AWS コンソールでインスタンスを終了する方法のスクリーンショット。](./media/aws-ubuntu/aws-ubuntu-terminate.png)

インスタンスを手動で削除する場合は、Terraform プランによって作成された `*./scripts/install_arc_agent.sh` も削除する必要があります。
