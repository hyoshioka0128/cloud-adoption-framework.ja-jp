---
title: Ansible を使用してアマゾン ウェブ サービス Amazon Elastic Compute Cloud インスタンスの Azure Arc へのオンボードをスケーリングする
description: Ansible を使用してアマゾン ウェブ サービス Amazon Elastic Compute Cloud インスタンスの Azure Arc へのオンボードをスケーリングする
author: likamrat
ms.author: brblanch
ms.date: 01/29/2021
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: operate
ms.custom: think-tank, e2e-hybrid
ms.openlocfilehash: 58b8355659779ef4258dd9d2935e8b09d3d9559b
ms.sourcegitcommit: b8f8b7631aabaab28e9705934bf67dad15e3a179
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101800789"
---
# <a name="use-ansible-to-scale-onboarding-amazon-web-services-amazon-elastic-compute-cloud-instances-to-azure-arc"></a>Ansible を使用してアマゾン ウェブ サービス Amazon Elastic Compute Cloud インスタンスの Azure Arc へのオンボードをスケーリングする

この記事では、[Ansible](https://www.ansible.com/) を使用してアマゾン ウェブ サービス (AWS) Amazon Elastic Compute Cloud (Amazon EC2) インスタンスの Azure Arc へのオンボードをスケーリングする方法について説明します。

このガイドは、Ansible に関する基礎知識があることを前提としています。 EC2 サーバー インベントリの動的読み込みに [`amazon.aws.aws-ec2`](https://docs.ansible.com/ansible/latest/collections/amazon/aws/aws_ec2_inventory.html) プラグインを使用する、基本的な Ansible プレイブックと構成が用意されています。

このガイドは、既存の Ansible テスト環境がまだない場合でも使用できます。また、4 つの Windows Server 2019 サーバーと 4 つの Ubuntu サーバーのほか、単純な構成を持つ基本的な CentOS 7 Ansible 制御サーバーで構成されるサンプルの AWS EC2 サーバー インベントリを作成する Terraform プランが含まれています。

> [!WARNING]
> 提供されている Ansible サンプル ブックでは、WinRM をパスワード認証および HTTP と共に使用して、Windows ベースのサーバーを構成します。 これは運用環境では推奨されません。 運用環境の Windows ホストで Ansible を使用することを計画している場合は、証明書と共に [HTTPS 経由の WinRM](/troubleshoot/windows-client/system-management-components/configure-winrm-for-https) を使用する必要があります。

## <a name="prerequisites"></a>前提条件

1. Azure Arc Jumpstart リポジトリを複製します。

    ```console
    git clone https://github.com/microsoft/azure_arc.git
    ```

2. [バージョン 2.7 以降の Azure CLI をインストールするか、更新します。](/cli/azure/install-azure-cli) 現在インストールされているバージョンを確認するには、次のコマンドを使用してください。

   ```console
   az --version
   ```

3. [SSH キーを生成します](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/) (または既存の SSH キーを使用します)

4. [無料の AWS アカウントを作成します](https://aws.amazon.com/premiumsupport/knowledge-center/create-and-activate-aws-account/)

5. [V0.13 以降の Terraform をインストールします](https://learn.hashicorp.com/tutorials/terraform/install-cli)

   - Azure サービス プリンシパルを作成します。

      AWS 仮想マシンを Azure Arc に接続するには、共同作成者ロールが割り当てられた Azure サービス プリンシパルが必要です。 これを作成するには、自分の Azure アカウントにサインインして、次のコマンドを実行します。 このコマンドは、[Azure Cloud Shell](https://shell.azure.com/) 内で実行することもできます。

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

2. サインインした後、左上にある **[サービス]** ドロップダウンを選択します。 **[セキュリティ、ID、およびコンプライアンス]** で、 **[IAM]** を選択して [[Identity and Access Management] ページ](https://console.aws.amazon.com/iam/home)にアクセスします

    ![AWS クラウド コンソールのスクリーンショット。](./media/aws-scale-ansible/ansible-aws-console.png)

    ![AWS クラウド コンソールでの [Identity and Access Management] のスクリーンショット。](./media/aws-scale-ansible/ansible-iam.png)

3. 左側のメニューから **[ユーザー]** を選択し、 **[ユーザーを追加]** を選択して新しい IAM ユーザーを作成します。

    ![AWS クラウド コンソールでの新しいユーザーを示すスクリーンショット。](./media/aws-scale-ansible/ansible-new-user-1.png)

4. **[ユーザーを追加]** ページで、ユーザーに **Terraform** という名前を指定し、 **[プログラムによるアクセス]** チェックボックスをオンにして、 **[次のステップ]** を選択します

    ![AWS クラウド コンソールでの新しいユーザーの作成を示す 2 番目のスクリーンショット。](./media/aws-scale-ansible/ansible-new-user-2.png)

5. 次の **[アクセス許可の設定]** ページで、 **[既存のポリシーを直接アタッチ]** を選択し、スクリーンショットに示されているように **[AmazonEC2FullAccess]** の横のチェックボックスをオンにして、 **[次のステップ]** を選択します。

    ![AWS クラウド コンソールでの新しいユーザーの作成を示す 3 番目のスクリーンショット。](./media/aws-scale-ansible/ansible-new-user-3.png)

6. **[タグ]** ページで、`azure-arc-demo` というキーを持つタグを割り当て、 **[次のステップ]** を選択して **[確認]** ページに進みます。

    ![AWS クラウド コンソールのタグのスクリーンショット。](./media/aws-scale-ansible/ansible-tags.png)

7. すべてが正しいことを確認し、 **[ユーザーの作成]** を選択します。

    ![AWS クラウド コンソールでの新しいユーザーを示す 4 番目のスクリーンショット。](./media/aws-scale-ansible/ansible-new-user-4.png)

8. ユーザーが作成されると、ユーザーのアクセス キー ID とシークレット アクセス キーが表示されます。 これらの値をコピーしてから **[閉じる]** を選択します。 次のページでは、これがどのように表示されるかの例を確認できます。 これらのキーを取得すると、これらを Terraform で使用して AWS リソースを作成できるようになります。

    ![AWS クラウド コンソールでユーザーが正常に作成されたことを示すスクリーンショット。](./media/aws-scale-ansible/ansible-new-user-5.png)

## <a name="option-1-create-a-sample-aws-server-inventory-and-ansible-control-server-using-terraform-and-onboard-the-servers-to-azure-arc"></a>オプション 1: Terraform を使用してサンプルの AWS サーバー インベントリと Ansible 制御サーバーを作成し、そのサーバーを Azure Arc にオンボードする

> [!NOTE]
> AWS サーバー インベントリと Ansible サーバーが既にある場合は、オプション 2 に進みます。

### <a name="configure-terraform"></a>Terraform を構成する

Terraform プランを実行する前に、プランで使用される環境変数をエクスポートする必要があります。 これらの変数は、Azure サブスクリプションとテナント、Azure サービス プリンシパル、および先ほど作成した AWS IAM ユーザーとキーに基づいています。

1. `az account list` コマンドを使用して、Azure サブスクリプション ID とテナント ID を取得します。

2. Terraform プランでは、Microsoft Azure と AWS の両方にリソースが作成されます。 次に、AWS EC2 仮想マシン上でスクリプトが実行され、Ansible および必要なすべてのアーティファクトがインストールされます。 この Terraform プランには、環境変数を使用してアクセスする AWS と Azure の環境に関する特定の情報が必要です。 [`scripts/vars.sh`](https://github.com/microsoft/azure_arc/blob/main/azure_arc_servers_jumpstart/aws/scaled_deployment/ansible/terraform/scripts/vars.sh) を編集し、各変数を適切な値で更新します。

   - `TF-VAR-subscription-id` = お使いの Azure サブスクリプション ID
   - `TF-VAR-client-id` = お使いの Azure サービス プリンシパル アプリケーション ID
   - `TF-VAR-client-secret` = お使いの Azure サービス プリンシパル パスワード
   - `TF-VAR-tenant-id` = Azure テナント ID
   - `AWS-ACCESS-KEY-ID` = AWS アクセス キー
   - `AWS-SECRET-ACCESS-KEY` = AWS シークレット キー

3. シェルから、複製されたリポジトリの `azure_arc_servers_jumpstart/aws/scaled_deployment/ansible/terraform` ディレクトリに移動します。

4. 下に示すように、ソース コマンドを使用して [`scripts/vars.sh`](https://github.com/microsoft/azure_arc/blob/main/azure_arc_servers_jumpstart/aws/scaled_deployment/ansible/terraform/scripts/vars.sh) を実行することにより、編集した環境変数をエクスポートします。 Terraform ではプランを適切に実行するために、これらを設定する必要があります。

    ```console
    source ./scripts/vars.sh
    ```

5. SSH キーが `~/.ssh` で使用可能であり、かつ `id-rsa.pub` および `id-rsa` という名前であることを確認します。 上記の SSH keygen ガイドに従ってキーを作成した場合は、既に正しくセットアップされているはずです。 そうでない場合は、別のパスのキーを使用するように [`aws-infrastructure.tf`](https://github.com/microsoft/azure_arc/blob/main/azure_arc_servers_jumpstart/aws/scaled_deployment/ansible/terraform/aws_infra.tf) を変更する必要があります。

6. 必要な Terraform プロバイダーをダウンロードする `terraform init` コマンドを実行します。

    ![`terraform init` コマンドのスクリーンショット。](./media/aws-scale-ansible/terraform-init.png)

### <a name="deploy-server-infrastructure"></a>サーバー インフラストラクチャをデプロイする

1. `azure_arc_servers_jumpstart/aws/scaled_deployment/ansible/terraform` ディレクトリから `terraform apply --auto-approve` を実行し、プランが完了するまで待ちます。 正常に完了すると、4 つの Windows Server 2019 サーバー、4 つの Ubuntu サーバー、および 1 つの CentOS 7 Ansible 制御サーバーが使用できるようになります。

2. AWS コンソールを開き、作成したサーバーが表示されていることを確認します。

    ![EC2 インスタンスを表示している AWS コンソールのスクリーンショット。](./media/aws-scale-ansible/ec2-instances.png)

### <a name="run-the-ansible-playbook-to-onboard-the-aws-ec2-instances-as-azure-arc-enabled-servers"></a>Ansible プレイブックを実行して AWS EC2 インスタンスを Azure Arc 対応サーバーとしてオンボードする

1. Terraform プランが完了すると、`ansible-ip` という名前の出力変数に Ansible 制御サーバーのパブリック IP が表示されます。 `ssh centos@xx.xx.xx.xx` を実行して Ansible サーバーに SSH で接続します。ここで、`xx.xx.xx.xx` は Ansible サーバーの IP アドレスに置き換えます。

    ![Ansible を使用してリモート サーバーに接続する SSH キーのスクリーンショット。](./media/aws-scale-ansible/ansible-ssh.png)

2. `cd ansible` を実行してディレクトリを `ansible` ディレクトリに変更します。 このフォルダーには、サーバーを Azure Arc にオンボードするために使用するサンプルの Ansible 構成とプレイブックが含まれています。

    ![ファイルを一覧表示するシェル スクリプトのスクリーンショット。](./media/aws-scale-ansible/ansible-cfg.png)

3. `aw-ec2` Ansible プラグインでは、AWS サーバー インベントリを動的に読み取るために AWS の資格情報が必要です。 これらは環境変数としてエクスポートします。 次のコマンドを実行します。このとき、`AWS-ACCESS-KEY-ID` と `AWS-SECRET-ACCESS-KEY` の値を、前に作成した AWS の資格情報に置き換えます。

    ```console
    export AWS-ACCESS-KEY-ID="XXXXXXXXXXXXXXXXX"
    export AWS-SECRET-ACCESS-KEY="XXXXXXXXXXXXXXX"
    ```

4. [`group-vars/all.yml`](https://github.com/microsoft/azure_arc/blob/main/azure_arc_servers_jumpstart/aws/scaled_deployment/ansible/terraform/ansible_config/group_vars/all.yml) ファイル内の Azure テナント ID とサブスクリプション ID のプレースホルダーの値を、ご使用の環境に適した値に置き換えます。

    ![YAML ファイル内の変数のスクリーンショット。](./media/aws-scale-ansible/yml-variables.png)

5. 次のコマンドを実行して Ansible プレイブックを実行します。このとき、Azure サービス プリンシパル ID とサービス プリンシパル シークレットを置き換えます。

    ```console
    ansible-playbook arc-agent.yml -i ansible-plugins/inventory-uswest2-aws-ec2.yml --extra-vars '{"service-principal-id": "XXXXXXX-XXXXX-XXXXXXX", "service-principal-secret": "XXXXXXXXXXXXXXXXXXXXXXXX"}'
    ```

    プレイブックが正常に実行されると、下のスクリーンショットのような出力が表示されます。

    ![実行中の Ansible プレイブックのスクリーンショット。](./media/aws-scale-ansible/ansible-playbook.png)

6. Azure portal を開き、arc-aws-demo のリソース グループに移動します。 Azure Arc 対応サーバーが一覧表示されます。

    ![Azure Arc 対応サーバーがオンボードされている Azure portal のスクリーンショット。](./media/aws-scale-ansible/onboarding-servers.png)

### <a name="clean-up-environment-by-deleting-resources"></a>リソースを削除して環境をクリーンアップする

このデモの一部として作成したすべてのリソースを削除するには、次のように `terraform destroy --auto-approve` コマンドを使用します。

![`terraform destroy` コマンドのスクリーンショット。](./media/aws-scale-ansible/terraform-destroy.png)

## <a name="option-2-onboarding-an-existing-aws-server-inventory-to-azure-arc-using-your-own-ansible-control-server"></a>オプション 2: 独自の Ansible 制御サーバーを使用して既存の AWS サーバー インベントリを Azure Arc にオンボードする

> [!NOTE]
> 既存の AWS サーバー インベントリと Ansible サーバーがない場合は、オプション 1 に戻ります。

### <a name="review-provided-ansible-configuration-and-playbook"></a>指定されている Ansible 構成とプレイブックを確認する

1. `ansible-config` ディレクトリに移動し、指定された構成を確認します。 指定されている構成には、基本的な `ansible.cfg` ファイルが含まれています。 このファイルを使用すると、AWS IAM ロールを使用してサーバー インベントリを動的に読み込む [`amazon.aws.aws-ec2`](https://docs.ansible.com/ansible/latest/collections/amazon/aws/aws-ec2-inventory.html) Ansible プラグインを有効にすることができます。 使用する IAM ロールに、オンボードするインベントリにアクセスするための十分な特権があることを確認します。

    ![`ansible.cfg` ファイルの詳細を示すスクリーンショット。](./media/aws-scale-ansible/ansible-cfg-details.png)

2. ファイル [`inventory-uswest2-aws-ec2.yml`](https://github.com/microsoft/azure_arc/blob/main/azure_arc_servers_jumpstart/aws/scaled_deployment/ansible/terraform/ansible_config/ansible_plugins/inventory_uswest2_aws_ec2.yml) によって、`aws-ec2` プラグインが `uswest2` リージョンからインベントリをプルし、適用されたタグによって資産をグループ化するように構成されます。 リージョンの変更や、グループまたはフィルターの調整など、サーバー インベントリのオンボードをサポートするように、このファイルを必要に応じて調整します。

   `./ansible-config/group-vars` 内のファイルは、さまざまな Ansible ホスト グループをオンボードするために使用する資格情報を提供するように調整する必要があります。

3. 指定された構成を、環境をサポートするように調整したら、次のコマンドを実行して Ansible プレイブックを実行します。このとき、Azure サービス プリンシパル ID とサービス プリンシパル シークレットを置き換えます。

    ```console
    ansible-playbook arc-agent.yml -i ansible-plugins/inventory-uswest2-aws-ec2.yml --extra-vars '{"service-principal-id": "XXXXXXX-XXXXX-XXXXXXX", "service-principal-secret": "XXXXXXXXXXXXXXXXXXXXXXXX"}'
    ```

    前と同じように、プレイブックが正常に実行されると、次のスクリーンショットのような出力が表示されます。

      ![実行中の Ansible プレイブックのスクリーンショット。](./media/aws-scale-ansible/ansible-playbook.png)

    前と同じように、Azure portal を開き、arc-aws-demo のリソース グループに移動します。 Azure Arc 対応サーバーが一覧表示されます。

    ![Azure Arc 対応サーバーが表示されている Azure portal のスクリーンショット。](./media/aws-scale-ansible/onboarding-servers.png)
