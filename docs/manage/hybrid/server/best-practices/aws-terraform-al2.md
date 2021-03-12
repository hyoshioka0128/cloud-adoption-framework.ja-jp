---
title: Terraform プランを使用して Amazon Elastic Compute Cloud に Amazon Linux 2 インスタンスをデプロイし、それを Azure Arc に接続する
description: Terraform プランを使用して Amazon Elastic Compute Cloud に Amazon Linux 2 インスタンスをデプロイし、それを Azure Arc に接続します。
author: likamrat
ms.author: brblanch
ms.date: 01/29/2021
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: operate
ms.custom: think-tank, e2e-hybrid
ms.openlocfilehash: 270b73e5533f2da5808d68a45b700112892b6bb9
ms.sourcegitcommit: b8f8b7631aabaab28e9705934bf67dad15e3a179
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101800985"
---
# <a name="use-a-terraform-plan-to-deploy-an-amazon-linux-2-instance-on-amazon-elastic-compute-cloud-and-connect-it-to-azure-arc"></a>Terraform プランを使用して Amazon Elastic Compute Cloud に Amazon Linux 2 インスタンスをデプロイし、それを Azure Arc に接続する

この記事では、提供された [Terraform](https://www.terraform.io/) プランを使用してアマゾン ウェブ サービス (AWS) の Amazon Elastic Compute Cloud (Amazon EC2) Linux 2 インスタンスをデプロイし、それを Azure Arc 対応サーバー リソースとして接続するためのガイダンスを提供します。

## <a name="prerequisites"></a>前提条件

1. Azure Arc Jumpstart リポジトリをクローンします。

    ```console
    git clone https://github.com/microsoft/azure_arc.git
    ```

2. [Azure CLI をインストールまたは更新します](/cli/azure/install-azure-cli)。 Azure CLI は、バージョン 2.7.0 以降を実行している必要があります。 現在インストールされているバージョンを確認するには、`az --version` を使用します。

3. [SSH キーを生成します](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/) (または既存の SSH キーを使用します)

4. [無料の AWS アカウントを作成します](https://aws.amazon.com/premiumsupport/knowledge-center/create-and-activate-aws-account/)

5. [0.12 以降の Terraform をインストールします](https://learn.hashicorp.com/tutorials/terraform/install-cli)

6. Azure サービス プリンシパルを作成します。

    AWS 仮想マシンを Azure Arc に接続するには、共同作成者ロールが割り当てられた Azure サービス プリンシパルが必要です。 これを作成するには、自分の Azure アカウントにサインインして、次のコマンドを実行します。

    ```console
    az login
    az ad sp create-for-rbac -n "http://AzureArcAWS" --role contributor
    ```

    出力は次のようになります。

    ```json
    {
      "appId": "XXXXXXXXXXXXXXXXXXXXXXXX",
      "displayName": "AzureArcAWS",
      "name": "http://AzureArcAWS",
      "password": "XXXXXXXXXXXXXXXXXXXXXXXX",
      "tenant": "XXXXXXXXXXXXXXXXXXXXXXXX"
    }
    ```

    > [!NOTE]
    > サービス プリンシパルのスコープを、特定の [Azure サブスクリプションとリソース グループ](/cli/azure/ad/sp)に限定することを強くお勧めします。

## <a name="create-an-aws-identity"></a>AWS ID を作成する

Terraform で AWS にリソースを作成するには、適切なアクセス許可を持つ新しい AWS IAM ロールを作成し、それを使用するように Terraform を構成する必要があります。

1. [AWS マネジメント コンソール](https://console.aws.amazon.com/console/home)にサインインします

2. サインインした後、左上にある **[サービス]** ドロップダウンを選択します。 **[セキュリティ、ID、およびコンプライアンス]** で、 **[IAM]** を選択して [[Identity and Access Management] ページ](https://console.aws.amazon.com/iam/home)にアクセスします

    ![AWS クラウド コンソールのスクリーンショット。](./media/aws-terraform-al2/al2-aws-console.png)

    ![AWS クラウド コンソールでの [Identity and Access Management] のスクリーンショット。](./media/aws-terraform-al2/al2-aws-iam.png)

3. 左側のメニューで **[ユーザー]** をクリックし、 **[ユーザーを追加]** を選択して新しい IAM ユーザーを作成します。

    ![AWS クラウド コンソールでの新しいユーザーの作成方法を示すスクリーンショット。](./media/aws-terraform-al2/al2-new-user-1.png)

4. **[ユーザーを追加]** ページで、ユーザーに `Terraform` という名前を指定し、 **[プログラムによるアクセス]** チェックボックスをオンにして、 **[次のステップ]** を選択します

    ![AWS クラウド コンソールでの新しいユーザーの作成方法を示す 2 番目のスクリーンショット。](./media/aws-terraform-al2/al2-new-user-2.png)

5. 次の **[アクセス許可の設定]** ページで、 **[既存のポリシーを直接アタッチ]** を選択し、スクリーンショットに示されているように **[AmazonEC2FullAccess]** の横のチェックボックスをオンにして、 **[次のステップ]** を選択します

    ![AWS クラウド コンソールでの新しいユーザーの作成方法を示す 3 番目のスクリーンショット。](./media/aws-terraform-al2/al2-new-user-3.png)

6. **[タグ]** ページで、`azure-arc-demo` というキーを持つタグを割り当て、 **[次のステップ]** を選択して [確認] ページに進みます。

    ![AWS クラウド コンソールのタグのスクリーンショット。](./media/aws-terraform-al2/al2-tags.png)

7. すべてが正しいことを確認し、 **[ユーザーの作成]** を選択します。

    ![AWS クラウド コンソールでの新しいユーザーの作成方法を示す 4 番目のスクリーンショット。](./media/aws-terraform-al2/al2-new-user-4.png)

8. ユーザーが作成されると、ユーザーのアクセス キー ID とシークレット アクセス キーが表示されます。 これらの値をコピーしてから **[閉じる]** を選択します。 次のページでは、これがどのように表示されるかの例を確認できます。 これらのキーを取得すると、これらを Terraform で使用して AWS リソースを作成できるようになります。

    ![AWS クラウド コンソールでユーザーが正常に作成されたスクリーンショット。](./media/aws-terraform-al2/al2-new-user-5.png)

## <a name="configure-terraform"></a>Terraform を構成する

Terraform プランを実行する前に、プランで使用される環境変数をエクスポートする必要があります。 これらの変数は、Azure サブスクリプションとテナント、Azure サービス プリンシパル、および先ほど作成した AWS IAM ユーザーとキーに基づいています。

1. `az account list` コマンドを使用して、Azure サブスクリプション ID とテナント ID を取得します。

2. Terraform プランでは、Microsoft Azure と AWS の両方にリソースが作成されます。 次に、AWS EC2 仮想マシンでスクリプトを実行されて、Azure Arc エージェントと必要なすべてのアーティファクトがインストールされます。 このスクリプトには、AWS および Azure 環境に関する特定の情報が必要です。 [`scripts/vars.sh`](https://github.com/microsoft/azure_arc/blob/main/azure_arc_servers_jumpstart/aws/AL2/terraform/scripts/vars.sh) を編集し、各変数を適切な値で更新します。

    - `TF-VAR-subscription-id` = お使いの Azure サブスクリプション ID
    - `TF-VAR-client-id` = お使いの Azure サービス プリンシパル アプリケーション ID
    - `TF-VAR-client-secret` = お使いの Azure サービス プリンシパル パスワード
    - `TF-VAR-tenant-id` = お使いの Azure テナント ID
    - `AWS-ACCESS-KEY-ID` = AWS アクセス キー
    - `AWS-SECRET-ACCESS-KEY` = AWS シークレット キー

3. Azure CLI から、複製されたリポジトリの `azure_arc_servers_jumpstart/aws/al2/terraform` ディレクトリに移動します。

4. 次に示すように、ソース コマンドを使用して [`scripts/vars.sh`](https://github.com/microsoft/azure_arc/blob/main/azure_arc_servers_jumpstart/aws/AL2/terraform/scripts/vars.sh) を実行することにより、編集した環境変数をエクスポートします。 Terraform ではプランを適切に実行するために、これらを設定する必要があります。 このスクリプトは、Terraform のデプロイの一部として、AWS 仮想マシンでも自動的にリモートで実行されることに注意してください。

    ```console
    source ./scripts/vars.sh
    ```

5. SSH キーが `~/.ssh` で使用可能であり、かつ `id-rsa.pub` および `id-rsa` という名前であることを確認します。 上記の ssh-keygen ガイドに従ってキーを作成した場合は、既に正しくセットアップされているはずです。 されていない場合は、別のパスのキーを使用するように [`main.tf`](https://github.com/microsoft/azure_arc/blob/main/azure_arc_servers_jumpstart/aws/AL2/terraform/main.tf) を変更する必要があります。

6. Terraform AzureRM プロバイダーをダウンロードする `terraform init` コマンドを実行します。

    ![`terraform init` コマンドのスクリーンショット。](./media/aws-terraform-al2/al2-terraform-init.png)

## <a name="deployment"></a>デプロイ

1. `terraform apply --auto-approve` コマンドを実行して、プランが終了するまで待ちます。 完了すると、AWS Amazon Linux 2 EC2 インスタンスがデプロイされ、新しいリソース グループ内の新しい Azure Arc 対応サーバーとして接続されます。

2. Azure portal を開き、`arc-servers-demo` リソース グループに移動します。 AWS に作成された仮想マシンはリソースとして表示されます。

    ![Azure portal 内の Azure Arc 対応サーバーを示すスクリーンショット。](./media/aws-terraform-al2/al2-server.png)

## <a name="semi-automated-deployment-optional"></a>半自動デプロイ (省略可能)

お気付きかもしれませんが、実行の最後の段階では、VM を新しい Azure Arc 対応サーバー リソースとして登録します。

  ![`azcmagent connect` コマンドのスクリーンショット。](./media/aws-terraform-al2/al2-azcmagent.png)

実際の登録プロセスをデモンストレーションまたはコントロールする場合は、次の手順を行います。

1. [`install_arc_agent.sh.tmpl`](https://github.com/microsoft/azure_arc/blob/main/azure_arc_servers_jumpstart/aws/AL2/terraform/scripts/install_arc_agent.sh.tmpl)スクリプト テンプレートで `run connect command` セクションをコメント アウトし、ファイルを保存します。

    ![コメント アウトされている `azcmagent connect` コマンドのスクリーンショット。](./media/aws-terraform-al2/al2-azcmagent-commented.png)

2. `terraform output` を実行して、AWS VM のパブリック IP を取得します。

    ![Terraform の出力のスクリーンショット。](./media/aws-terraform-al2/al2-terraform-output.png)

3. `ssh ec2-user@xx.xx.xx.xx` を使用して VM に SSH 接続します。ここで、`xx.xx.xx.xx` はホスト IP です。

    ![EC2 サーバーに接続する SSH キーのスクリーンショット。](./media/aws-terraform-al2/al2-ssh.png)

4. [`vars.sh`](https://github.com/microsoft/azure_arc/blob/main/azure_arc_servers_jumpstart/aws/AL2/terraform/scripts/vars.sh) のすべての環境変数をエクスポートします。

    !['' でエクスポートする環境変数のスクリーンショット。](./media/aws-terraform-al2/al2-export-variables.png)

5. 次のコマンドを実行します。

    ```console
    azcmagent connect --service-principal-id $TF-VAR-client-id --service-principal-secret $TF-VAR-client-secret --resource-group "Arc-Servers-Demo" --tenant-id $TF-VAR-tenant-id --location "westus2" --subscription-id $TF-VAR-subscription-id
    ```

    ![`azcmagent connect` コマンドの別のスクリーンショット。](./media/aws-terraform-al2/al2-azcmagent-2.png)

6. 完了すると、VM は Azure Arc に登録され、Azure portal 経由でリソース グループに表示されます。

## <a name="delete-the-deployment"></a>デプロイの削除

このデモの一部として作成したすべてのリソースを削除するには、次のように `terraform destroy --auto-approve` コマンドを使用します。
    ![`terraform destroy` コマンドのスクリーンショット。](./media/aws-terraform-al2/al2-terraform-destroy.png)

または、[AWS コンソール](https://console.aws.amazon.com/ec2/v2/home)から AWS EC2 インスタンスを終了して、インスタンスを直接削除することもできます。
    ![AWS コンソールでインスタンスを終了する方法のスクリーンショット。](./media/aws-terraform-al2/al2-terminate.png)
