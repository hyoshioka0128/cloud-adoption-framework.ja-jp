---
title: Azure DevOps Services に Team Foundation Server の展開をリファクターする
description: Azure 向けのクラウド導入フレームワークを使用し、オンプレミスの Team Foundation Server (TFS) デプロイを Azure の Azure DevOps Services に移行してリファクターする方法について説明します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 10/11/2018
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: migrate
services: site-recovery
ms.openlocfilehash: a50223154345f513c0c5437991a7e88be1283a4b
ms.sourcegitcommit: 9b183014c7a6faffac0a1b48fdd321d9bbe640be
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/19/2020
ms.locfileid: "85075973"
---
<!-- cSpell:ignore contosodevmigration contosomigration onmicrosoft visualstudio sourceconnectionstring CONTOSOTFS DACPAC SQLDB SQLSERVERNAME INSTANCENAME azuredevopsmigration validateonly -->

# <a name="refactor-a-team-foundation-server-deployment-to-azure-devops-services"></a>Azure DevOps Services に Team Foundation Server の展開をリファクターする

この記事では、架空の会社である Contoso がオンプレミスの Team Foundation Server (TFS) のデプロイを Azure 上の Azure DevOps Services に移行することで、それをリファクターする方法について説明します。 Contoso の開発チームは、過去 5 年間、チームのコラボレーションとソース管理に TFS を使用してきました。 現在は、開発やテストの作業とソース管理に対応するクラウドベースのソリューションに移行したいと考えています。 Azure DevOps モデルに移行し、新しいクラウドネイティブ アプリを開発する際に、Azure DevOps Services が役立ちます。

## <a name="business-drivers"></a>ビジネス ドライバー

IT リーダーシップ チームは、ビジネス パートナーと密接に協力して将来の目標を明らかにしました。 パートナーは、開発ツールやテクノロジにはあまり関心がありませんが、次の点を把握しています。

- **ソフトウェア:** 中心の業務にかかわらず、Contoso を含め、すべての企業がソフトウェア企業になりました。 企業の上層部は、IT 部門がユーザーの新しい業務慣行や顧客のエクスペリエンスを利用して業務を改善する方法に関心を持っています。
- **効率化:** Contoso はプロセスを合理化し、開発者とユーザーにとって不要な手順を削除する必要があります。 こうすることで、顧客の要件により効率的に対応できるようになります。 時間やお金を無駄にすることなく、IT 部門の迅速化が必要です。
- **機敏性:** Contoso の IT 部門は、グローバル経済で成功を収めるために、ビジネス ニーズに対応し、市場よりも迅速に対応する必要があります。 IT 部門がビジネスのブロックする要素にならないようにします。

## <a name="migration-goals"></a>移行の目標

Contoso クラウド チームは、Azure DevOps Services への移行の目標を設定しました。

- チームにはデータをクラウドに移行するツールが必要です。 手作業がほとんど不要になるようにする必要があります。
- 昨年の作業項目データと履歴を移行する必要があります。
- 新しいユーザー名とパスワードは設定したくありません。 現在のシステムの割り当てはすべて維持する必要があります。
- ソース管理については、Team Foundation バージョン管理 (TFVC) から Git に移行したいと考えています。
- Git への切り替えは、ソース コードの最新バージョンのみをインポートする "tip migration" (チップ移行) になります。 これは、コードベースの転換時にすべての作業が中止されるダウンタイム中に行われます。 Contoso は、移行後に最新のマスター ブランチ履歴しか利用できなくなることを理解しています。
- Contoso はこの変更を心配し、完全に移行する前にテストしたいと考えています。 Azure DevOps Services への移行後も TFS へのアクセスを維持したいと考えています。
- 複数のコレクションがあるので、プロセスをより理解できるように、プロジェクトがごく少数のコレクションから始めたいと考えています。
- TFS コレクションが Azure DevOps Services 組織と 1 対 1 の関係であることは理解しているので、複数の URL を持つことになります。 ただし、これはコード ベースとプロジェクトの分離の現在のモデルと一致します。

## <a name="proposed-architecture"></a>提案されたアーキテクチャ

- Contoso は、TFS プロジェクトをクラウドに移行し、プロジェクトやソース管理をオンプレミスでホストしなくなります。
- TFS は、Azure DevOps Services に移行されます。
- 現在、Contoso には `ContosoDev` という名前の TFS コレクションが 1 つあります。これは `contosodevmigration.visualstudio.com` という Azure DevOps Services 組織に移行されます。
- 昨年のプロジェクト、作業項目、バグ、およびイテレーションは、Azure DevOps Services に移行されます。
- Contoso は Azure Active Directory を利用します。これは、移行計画の開始時に [Azure インフラストラクチャをデプロイ](./contoso-migration-infrastructure.md)したときに設定したものです。

![シナリオのアーキテクチャ](./media/contoso-migration-tfs-vsts/architecture.png)

## <a name="migration-process"></a>移行プロセス

Contoso は、次のようにして移行プロセスを完了します。

1. かなり多くの準備が必要です。 最初に、Contoso は TFS の実装をサポートされるレベルまでアップグレードする必要があります。 現在は TFS 2017 Update 3 を実行していますが、データベースの移行を使用するには、最新の更新プログラムが適用された、サポートされている 2018 バージョンを実行する必要があります。
2. アップグレード後、TFS Migration Tool を実行し、コレクションを検証します。
3. 一連の準備ファイルを構築し、テストのために移行のドライ ランを実行します。
4. その後、もう一度移行を実行します。今度は、作業項目、バグ、スプリント、およびコードを含む完全な移行です。
5. 移行後、コードを TFVC から Git に移動します。

![移行プロセス](./media/contoso-migration-tfs-vsts/migration-process.png)

## <a name="prerequisites"></a>前提条件

このシナリオを実行するために Contoso に必要なものを以下に示します。

<!-- markdownlint-disable MD033 -->

| **必要条件** | **詳細** |
| --- | --- |
| **Azure サブスクリプション** | このシリーズの先行する記事の中で、Contoso はサブスクリプションを作成しました。 Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/pricing/free-trial)を作成してください。 <br><br> 無料アカウントを作成する場合、サブスクリプションの管理者としてすべてのアクションを実行できます。 <br><br> 既存のサブスクリプションを使用しており、管理者でない場合は、管理者に依頼して所有者アクセス許可または共同作成者アクセス許可を割り当ててもらう必要があります。 <br><br> さらに詳細なアクセス許可が必要な場合は、[こちらの記事](https://docs.microsoft.com/azure/site-recovery/site-recovery-role-based-linked-access-control)をご覧ください。 |
| **Azure インフラストラクチャ** | Contoso は、[移行のための Azure インフラストラクチャ](./contoso-migration-infrastructure.md)についての記事で説明されているように、Azure インフラストラクチャを設定します。 |
| **オンプレミスの TFS サーバー** | オンプレミスで、TFS 2018 Upgrade 2 を実行するか、このプロセスの一環としてそれにアップグレードする必要があります。 |

## <a name="scenario-steps"></a>シナリオのステップ

Contoso は、次のようにして移行を完了します。

> [!div class="checklist"]
>
> - **ステップ 1:Azure ストレージ アカウントを作成する。** このストレージ アカウントは、移行プロセス中に使用されます。
> - **手順 2:TFS をアップグレードする。** 展開を TFS 2018 Update 2 にアップグレードします。
> - **ステップ 3:コレクションを検証する。** 移行の準備の TFS コレクションを検証します。
> - **手順 4:準備ファイルを構築する。** TFS Migration Tool を使用して、移行ファイルを作成します。

## <a name="step-1-create-a-storage-account"></a>手順 1:ストレージ アカウントの作成

1. Contoso の管理者は Azure portal でストレージ アカウントを作成します (**contosodevmigration**)。
2. フェールオーバーに使用するセカンダリ リージョン (米国中部) でアカウントを設定します。 汎用の標準アカウントとローカル冗長ストレージを使用します。

    ![ストレージ アカウント](./media/contoso-migration-tfs-vsts/storage1.png)

**さらにサポートが必要な場合**

- [Azure Storage の概要](https://docs.microsoft.com/azure/storage/common/storage-introduction)。
- [ストレージ アカウントの作成](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account)。

## <a name="step-2-upgrade-tfs"></a>手順 2:TFS をアップグレードする

Contoso の管理者は、TFS サーバーを TFS 2018 Update 2 にアップグレードします。 開始前の作業:

- [TFS 2018 Update 2](https://visualstudio.microsoft.com/downloads) をダウンロードします。
- [ハードウェア要件](https://docs.microsoft.com/azure/devops/server/requirements)を検証し、[リリース ノート](https://docs.microsoft.com/visualstudio/releasenotes/tfs2018-relnotes)と[アップグレードの注意事項](https://docs.microsoft.com/azure/devops/server/upgrade/get-started#before-you-upgrade-to-tfs-2018)をすべて読みます。

次の手順でアップグレードします。

1. まず (VMware VM 上で実行されている) TFS サーバーをバックアップし、VMware のスナップショットを作成します。

    ![TFS](./media/contoso-migration-tfs-vsts/upgrade1.png)

2. TFS のインストーラーが起動し、インストール先を選択します。 インストーラーはインターネットにアクセスできる必要があります。

    ![TFS](./media/contoso-migration-tfs-vsts/upgrade2.png)

3. インストールが完了すると、サーバー構成ウィザードが起動します。

    ![TFS](./media/contoso-migration-tfs-vsts/upgrade3.png)

4. 検証後、ウィザードはアップグレードを完了します。

     ![TFS](./media/contoso-migration-tfs-vsts/upgrade4.png)

5. プロジェクト、作業項目、およびコードを確認して TFS のインストールを検証します。

     ![TFS](./media/contoso-migration-tfs-vsts/upgrade5.png)

> [!NOTE]
> 一部の TFS アップグレードでは、アップグレードの完了後に機能の構成ウィザードを実行する必要があります。 [詳細については、こちらを参照してください](https://docs.microsoft.com/azure/devops/reference/configure-features-after-upgrade?utm_source=ms&utm_medium=guide&utm_campaign=vstsdataimportguide&view=vsts)。

**さらにサポートが必要な場合**

TFS のアップグレードの詳細については、[こちら](https://docs.microsoft.com/azure/devops/server/upgrade/get-started)を参照してください。

## <a name="step-3-validate-the-tfs-collection"></a>手順 3:TFS コレクションを検証する

Contoso の管理者は、移行前に ContosoDev コレクション データベースに対して TFS Migration Tool を実行して検証します。

1. 管理者は [TFS Migration Tool](https://www.microsoft.com/download/details.aspx?id=54274) をダウンロードして解凍します。 実行している TFS 更新プログラム用のバージョンをダウンロードすることが重要です。 バージョンは管理コンソールで確認できます。

    ![TFS](./media/contoso-migration-tfs-vsts/collection1.png)

2. プロジェクト コレクションの URL を指定して、検証を実行するツールを実行します。

        `TfsMigrator validate /collection:http://contosotfs:8080/tfs/ContosoDev`

3. ツールにエラーが表示されます。

    ![TFS](./media/contoso-migration-tfs-vsts/collection2.png)

4. ログ ファイルは、ツールの場所の直前の `Logs` フォルダーにあります。 主要な検証ごとにログ ファイルが生成されます。 `TfsMigration.log` は主要な情報を保持しています。

    ![TFS](./media/contoso-migration-tfs-vsts/collection3.png)

5. 管理者は、ID に関連するこのエントリを見つけます。

    ![TFS](./media/contoso-migration-tfs-vsts/collection4.png)

6. コマンド ラインで `TfsMigrator validate /help` を実行し、ID を検証するにはコマンドで `/tenantDomainName` が必要と思われることを確認します。

     ![TFS](./media/contoso-migration-tfs-vsts/collection5.png)

7. この値を Azure AD 名 と共に指定して、検証コマンドをもう一度実行します。`TfsMigrator validate /collection:http://contosotfs:8080/tfs/ContosoDev /tenantDomainName:contosomigration.onmicrosoft.com`

    ![TFS](./media/contoso-migration-tfs-vsts/collection7.png)

8. Azure AD のサインイン画面が表示され、全体管理者ユーザーの資格情報を入力します。

     ![TFS](./media/contoso-migration-tfs-vsts/collection8.png)

9. 検証に合格します。これはツールで確認できます。

    ![TFS](./media/contoso-migration-tfs-vsts/collection9.png)

## <a name="step-4-create-the-migration-files"></a>手順 4:移行ファイルを作成する

検証が完了すると、Contoso の管理者は TFS Migration Tool を使用して移行ファイルを構築できます。

1. ツールで準備手順を実行します。

    `TfsMigrator prepare /collection:http://contosotfs:8080/tfs/ContosoDev /tenantDomainName:contosomigration.onmicrosoft.com /accountRegion:cus`

     ![準備](./media/contoso-migration-tfs-vsts/prep1.png)

    準備の手順では、以下のことを行います。
    - コレクションをスキャンしてすべてのユーザー一覧を検索し、識別マップ ログを作成します (**IdentityMapLog.csv**)。
    - Azure Active Directory への接続を準備し、各 ID に一致するものを検索します。
    - Contoso は Azure AD をすでにデプロイし、Azure AD Connect を使用して同期しているため、一致する ID を検出して Active とマークできます。

2. Azure AD のサインイン画面が表示され、全体管理者の資格情報を入力します。

    ![準備](./media/contoso-migration-tfs-vsts/prep2.png)

3. Prepare が完了すると、インポート ファイルが正常に生成されたことがレポートされます。

    ![準備](./media/contoso-migration-tfs-vsts/prep3.png)

4. 管理者は、IdentityMapLog.csv ファイルと import.json ファイルの両方が新しいフォルダーに作成されていることを確認できます。

    ![準備](./media/contoso-migration-tfs-vsts/prep4.png)

5. import.json ファイルにはインポート設定が指定されています。 このファイルには、目的の組織名やストレージ アカウント情報などの情報が含まれています。 ほとんどのフィールドは自動的に設定されます。 一部のフィールドにはユーザー入力が必要です。 Contoso はファイルを開き、作成する Azure DevOps Services 組織名 **contosodevmigration** を追加します。 この名前の場合、Azure DevOps Services の URL は **contosodevmigration.visualstudio.com** になります。

    ![準備](./media/contoso-migration-tfs-vsts/prep5.png)

    > [!NOTE]
    > 組織は移行前に作成する必要があります。移行後に変更することもできます。

6. インポート中に Azure DevOps Services に取り込まれるアカウントを示す ID ログ マップ ファイルを確認します。

    - アクティブな ID とは、インポート後に Azure DevOps Services 内のユーザーになる ID のことです。
    - Azure DevOps Services で、これらの ID にライセンスが付与され、移行後に組織のユーザーとして表示されます。
    - これらの ID は、ファイルの **Expected Import Status** 列で **Active** とマークされています。

    ![準備](./media/contoso-migration-tfs-vsts/prep6.png)

## <a name="step-5-migrate-to-azure-devops-services"></a>手順 5:Azure DevOps Services に移行する

準備が完了したので、Contoso の管理者は移行に集中できるようになりました。 移行を実行すると、バージョン管理には TFVC ではなく Git が使用されるようになります。

移行に備えてコレクションをオフラインにするために、管理者はまず開発チームとダウンタイムのスケジュールを立てます。 移行プロセスの手順は次のとおりです。

1. **コレクションをデタッチする。** コレクションの ID データは、コレクションがアタッチされ、オンラインのときは TFS サーバー構成データベース内にあります。 コレクションが TFS サーバーからデタッチされると、その ID データのコピーが作成され、転送のためにコレクションと共にパッケージ化されます。 インポートの ID 部分を実行するには、このデータが必要です。 インポート中に発生した変更をインポートする方法がないため、インポートが完了するまでコレクションをデタッチしたままにする必要があります。
2. **バックアップを生成する。** 移行プロセスの次の手順は、Azure DevOps Services にインポートできるバックアップを生成することです。 データ層アプリケーション コンポーネント パッケージ (DACPAC) は、データベースの変更を単一のファイルにパッケージ化し、SQL の他のインスタンスに展開することができる SQL Server の機能です。 また、Azure DevOps Services に直接復元することもでき、コレクション データをクラウドに取り込むためのパッケージ化方法として使用されます。 Contoso は SqlPackage.exe ツールを使用して DACPAC を生成します。 このツールは、SQL Server Data Tools に含まれています。
3. **ストレージにアップロードする。** DACPAC が作成されたら、Azure Storage にアップロードします。 アップロードが完了したら、Shared Access Signature (SAS) を取得し、TFS Migration Tool からストレージにアクセスできるようにします。
4. **インポート ファイルに入力する。** Contoso は、DACPAC 設定など、インポート ファイルに足りないフィールドに入力します。 初めに、完全な移行の前にすべてが正常に機能していることを確認するため、_dry-run_ インポートを実行することを指定します。
5. **dry-run インポートを実行します。** dry-run インポートは、コレクションの移行をテストするのに役立ちます。 ドライ ランの保存期間は限られているため、運用環境の移行が実行される前に削除されます。 それらは、設定期間が経過すると、自動的に削除されます。 ドライ ランが削除される時期についての注意は、インポートが完了した後に送信される成功メールに記載されています。 時期を書き留め、それに応じて計画を立てます。
6. **運用環境の移行を完了する。** dry-run による移行が完了したら、Contoso の管理者は **import.json** ファイルを更新し、インポートを再実行することによって、最終的な移行を実行します。

### <a name="detach-the-collection"></a>コレクションをデタッチする

まず Contoso の管理者はローカル SQL Server のバックアップと TFS サーバーの VMware スナップショットを作成してから、デタッチします。

1. TFS 管理コンソールで、デタッチするコレクションを選択します (**ContosoDev**)。

    ![移行](./media/contoso-migration-tfs-vsts/migrate1.png)

2. **[全般]** で、 **[コレクションのデタッチ]** を選択します。

    ![移行](./media/contoso-migration-tfs-vsts/migrate2.png)

3. チーム プロジェクト コレクションのデタッチ ウィザードの **[サービス メッセージ]** で、コレクション内のプロジェクトに接続しようとするユーザー向けのメッセージを入力します。

    ![移行](./media/contoso-migration-tfs-vsts/migrate3.png)

4. **[デタッチの進行状況]** で進行状況を監視し、プロセスが完了したら **[次へ]** を選択します。

    ![移行](./media/contoso-migration-tfs-vsts/migrate4.png)

5. **[準備チェック]** でチェックが完了したら、 **[デタッチ]** を選択します。

    ![移行](./media/contoso-migration-tfs-vsts/migrate5.png)

6. **[閉じる]** を選択して完了します。

    ![移行](./media/contoso-migration-tfs-vsts/migrate6.png)

7. コレクションは TFS 管理コンソールで参照されなくなります。

    ![移行](./media/contoso-migration-tfs-vsts/migrate7.png)

### <a name="generate-a-dacpac"></a>DACPAC を生成する

Contoso は Azure DevOps Services へのインポートのためにバックアップ (DACPAC) を作成します。

- DACPAC の作成には SQL Server Data Tools の SqlPackage.exe が使用されます。 SQL Server Data Tools と共に、120、130、140 などの名前のフォルダーに複数バージョンの SqlPackage.exe がインストールされます。 適切なバージョンを使用して DACPAC を準備することが重要です。

- TFS 2018 のインポートでは、140 以降のフォルダーの SqlPackage.exe を使用する必要があります。 CONTOSOTFS の場合、このファイルは次の場所にあります: `C:\Program Files (x86)\Microsoft Visual Studio\2017\Enterprise\Common7\IDE\Extensions\Microsoft\SQLDB\DAC\140`

Contoso の管理者は次のように DACPAC を生成します。

1. コマンド プロンプトを開き、SQLPackage.exe の場所に移動します。 次のコマンドを入力して DACPAC を生成します。

    `SqlPackage.exe /sourceconnectionstring:"Data Source=SQLSERVERNAME\INSTANCENAME;Initial Catalog=Tfs_ContosoDev;Integrated Security=True" /targetFile:C:\TFSMigrator\Tfs_ContosoDev.dacpac /action:extract /p:ExtractAllTableData=true /p:IgnoreUserLoginMappings=true /p:IgnorePermissions=true /p:Storage=Memory`

    ![バックアップ](./media/contoso-migration-tfs-vsts/backup1.png)

2. コマンドの実行後、次のメッセージが表示されます。

    ![バックアップ](./media/contoso-migration-tfs-vsts/backup2.png)

3. DACPAC ファイルのプロパティを確認します。

    ![バックアップ](./media/contoso-migration-tfs-vsts/backup3.png)

### <a name="update-the-file-to-storage"></a>ストレージに対してファイルを更新する

DACPAC が作成されたら、Contoso は Azure Storage にアップロードします。

1. [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer) をダウンロードしてインストールします。

    ![アップロード](./media/contoso-migration-tfs-vsts/backup5.png)

2. サブスクリプションに接続し、移行のために作成したストレージ アカウントを探します (**contosodevmigration**)。 新しい BLOB コンテナー **azuredevopsmigration** を作成します。

    ![アップロード](./media/contoso-migration-tfs-vsts/backup6.png)

3. アップロード対象の DACPAC ファイルをブロック BLOB として指定します。

    ![アップロード](./media/contoso-migration-tfs-vsts/backup7.png)

4. ファイルがアップロードされたら、ファイル名、 **[SAS の生成]** の順に選択します。 ストレージ アカウントの下の BLOB コンテナーを展開し、インポート ファイルを含むコンテナーを選択し、 **[Shared Access Signature の取得]** を選択します。

    ![アップロード](./media/contoso-migration-tfs-vsts/backup8.png)

5. 既定値をそのまま使用し、 **[作成]** を選択します。 これで、24 時間のアクセスが可能になります。

    ![アップロード](./media/contoso-migration-tfs-vsts/backup9.png)

6. Shared Access Signature の URL をコピーし、TFS Migration Tool で使用できるようにします。

    ![アップロード](./media/contoso-migration-tfs-vsts/backup10.png)

> [!NOTE]
> 許可された期間内に移行が完了する必要があります。完了しない場合、アクセス許可は期限切れになります。
> Azure portal からは SAS キーを生成しないでください。 この方法で生成されたキーは、アカウント スコープであり、インポートには使用できません。

### <a name="fill-in-the-import-settings"></a>インポート設定を入力する

以前、Contoso の管理者はインポート仕様ファイル (import.json) の一部を入力していました。 今回は、残りの設定を追加する必要があります。

import.json ファイルを開き、次のフィールドに入力します。

- **[場所]:** 前述の手順で生成された SAS キーの場所。
- **Dacpac:** ストレージ アカウントにアップロードした DACPAC ファイルに名前を設定します。 拡張子 ".dacpac" を含めます。
- **ImportType:** ここでは DryRun に設定します。

![設定のインポート](./media/contoso-migration-tfs-vsts/import1.png)

### <a name="perform-a-dry-run-migration"></a>dry-run による移行を実行する

Contoso の管理者は、すべてが予期したとおり機能していることを確認するための dry-run の移行を実行することによって、移行を開始します。

1. コマンド プロンプトを開き、TfsMigration の場所 (`C:\TFSMigrator`) に移動します。
2. 最初の手順として、インポート ファイルを検証します。 Contoso は、ファイルの形式が正しいこと、SAS キーが動作していることを確認したいと考えています。

    `TfsMigrator import /importFile:C:\TFSMigrator\import.json /validateonly`

3. この検証で、SAS キーの有効期間を長くする必要があるというエラーが返されます。

    ![ドライ ラン](./media/contoso-migration-tfs-vsts/test1.png)

4. Azure Storage Explorer を使用して有効期間を 7 日間に設定した新しい SAS キーを作成します。

    ![ドライ ラン](./media/contoso-migration-tfs-vsts/test2.png)

5. `import.json` ファイルを更新し、検証をもう一度実行します。 今回は検証が正常に完了します。

    `TfsMigrator import /importFile:C:\TFSMigrator\import.json /validateonly`

    ![ドライ ラン](./media/contoso-migration-tfs-vsts/test3.png)

6. ドライ ランを開始します。

    `TfsMigrator import /importFile:C:\TFSMigrator\import.json`

7. 移行を確認するメッセージが発行されます。 ドライ ラン後にステージング データが保持される時間の長さに注意してください。

    ![ドライ ラン](./media/contoso-migration-tfs-vsts/test4.png)

8. Azure AD の [サインイン] が表示されます。Contoso 管理者のサインインで完了する必要があります。

    ![ドライ ラン](./media/contoso-migration-tfs-vsts/test5.png)

9. メッセージにはインポートに関する情報が表示されます。

    ![ドライ ラン](./media/contoso-migration-tfs-vsts/test6.png)

10. 15 分ほど経過した後、管理者は URL にアクセスし、次の情報を確認します。

     ![ドライ ラン](./media/contoso-migration-tfs-vsts/test7.png)

11. 移行が完了したら、Contoso の開発リーダーは Azure DevOps Services にサインインし、ドライ ランが正常に動作していることを確認します。 認証後、Azure DevOps Services は、組織を確認するいくつかの詳細情報が必要です。

    ![ドライ ラン](./media/contoso-migration-tfs-vsts/test8.png)

12. Azure DevOps Services で、開発リーダーはプロジェクトが Azure DevOps Services に移行されたことを確認できます。 15 日後に組織が削除されるという通知が表示されます。

    ![ドライ ラン](./media/contoso-migration-tfs-vsts/test9.png)

13. 開発リーダーはいずれかのプロジェクトを開き、 **[作業項目]**  >  **[自分に割り当て済み]** を開きます。 作業項目データが ID と共に移行されたことが表示されます。

    ![ドライ ラン](./media/contoso-migration-tfs-vsts/test10.png)

14. 開発リーダーは、他のプロジェクトとコードもチェックして、ソース コードと履歴が移行されたことを確認します。

    ![ドライ ラン](./media/contoso-migration-tfs-vsts/test11.png)

### <a name="run-the-production-migration"></a>運用環境の移行を実行する

ドライ ランが完了したら、Contoso の管理者は運用環境の移行に進みます。 ドライ ランを削除し、インポート設定を更新し、インポートをもう一度実行します。

1. Azure DevOps Services ポータルで、dry-run 用の組織を削除します。
2. **ImportType** を **ProductionRun** に設定するように import.json ファイルを更新します。

    ![Production](./media/contoso-migration-tfs-vsts/full1.png)

3. ドライ ランの場合と同様に、移行を開始します。`TfsMigrator import /importFile:C:\TFSMigrator\import.json`
4. 移行を確認するメッセージが表示され、セキュリティで保護されている場所に、最大 7 日間、ステージング領域としてデータを保持できるという警告が表示されます。

    ![Production](./media/contoso-migration-tfs-vsts/full2.png)

5. Azure AD の [サインイン] で、リーダーは Contoso 管理者のサインインを指定します。

    ![Production](./media/contoso-migration-tfs-vsts/full3.png)

6. メッセージにはインポートに関する情報が表示されます。

    ![Production](./media/contoso-migration-tfs-vsts/full4.png)

7. 15 分ほど経過した後、リーダーは URL にアクセスし、次の情報を確認します。

    ![Production](./media/contoso-migration-tfs-vsts/full5.png)

8. 移行が完了したら、Contoso の開発リーダーは Azure DevOps Services にサインインし、移行が正常に機能していることを確認します。 開発リーダーは、サインイン後に、プロジェクトが移行されたことを確認できます。

    ![Production](./media/contoso-migration-tfs-vsts/full6.png)

9. 開発リーダーはいずれかのプロジェクトを開き、 **[作業項目]**  >  **[自分に割り当て済み]** を開きます。 作業項目データが ID と共に移行されたことが表示されます。

    ![Production](./media/contoso-migration-tfs-vsts/full7.png)

10. 開発リーダーは、他の作業項目データを見て確認します。

    ![Production](./media/contoso-migration-tfs-vsts/full8.png)

11. 開発リーダーは、他のプロジェクトとコードもチェックして、ソース コードと履歴が移行されたことを確認します。

    ![Production](./media/contoso-migration-tfs-vsts/full9.png)

### <a name="move-source-control-from-tfvc-to-git"></a>ソース管理を TFVC から Git に移行する

Contoso は、移行が完了したら、ソース コード管理については TFVC から Git に移行したいと考えています。 同一組織での Git リポジトリとして Azure DevOps Services 組織における現在のソース コードをインポートする必要があります。

1. Azure DevOps Services ポータルで、TFVC リポジトリ ( **$/PolicyConnect**) のいずれかを開いて確認します。

    ![Git](./media/contoso-migration-tfs-vsts/git1.png)

2. **[ソース]** ドロップ ダウン、 **[インポート]** の順に選択します。

    ![Git](./media/contoso-migration-tfs-vsts/git2.png)

3. **[ソースの種類]** では、 **[TFVC]** を選択し、リポジトリへのパスを指定します。 履歴は移行しないことを決めました。

    ![Git](./media/contoso-migration-tfs-vsts/git3.png)

    > [!NOTE]
    > TFVC と Git のバージョン管理情報の保存方法は異なるので、履歴は移行しないことをお勧めします。 これは、Windows や他の製品を一元管理されたバージョン管理から Git に移行したときに Microsoft が採用したアプローチです。

4. インポートの後、管理者はコードを確認します。

    ![Git](./media/contoso-migration-tfs-vsts/git4.png)

5. 2 番目のリポジトリ ( **$/SmartHotelContainer**) について、同じプロセスを繰り返します。

    ![Git](./media/contoso-migration-tfs-vsts/git5.png)

6. ソースを確認した後、開発リーダーたちは Azure DevOps Services への移行が完了したことに同意します。 Azure DevOps Services は、移行に関わるチーム内のすべての開発のソースになりました。

    ![Git](./media/contoso-migration-tfs-vsts/git6.png)

**さらにサポートが必要な場合**

TFVC からのインポートの詳細については、[こちら](https://docs.microsoft.com/azure/devops/repos/git/import-from-TFVC?view=vsts)を参照してください。

## <a name="clean-up-after-migration"></a>移行後にクリーンアップする

移行が完了したら、Contoso は次の操作を行う必要があります。

- 追加のインポート アクティビティについては、[インポート後](https://docs.microsoft.com/azure/devops/articles/migration-post-import?view=vsts)に関する記事を参照してください。
- TFVC リポジトリを削除するか、読み取り専用モードに設定します。 コード ベースは使用しないでください。ただし、その履歴を参照することはできます。

## <a name="post-migration-training"></a>移行後のトレーニング

Contoso は関連するチーム メンバーに Azure DevOps Services と Git のトレーニングを用意する必要があります。
