---
title: Azure DevOps Services に Team Foundation Server の展開をリファクターする
description: Azure 向けのクラウド導入フレームワークを使用し、オンプレミスの Team Foundation Server デプロイを Azure の Azure DevOps Services に移行してリファクターする方法について説明します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 07/01/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: migrate
ms.custom: internal
ms.openlocfilehash: 4543821d183169ff4932f62118915c71db2fe6fc
ms.sourcegitcommit: 9e4bc0e233a24642853f5e8acbeb9746b2444024
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102113811"
---
<!-- cSpell:ignore contosodev contosodevmigration contosomigration onmicrosoft visualstudio sourceconnectionstring smarthotelcontainer identitymaplog CONTOSOTFS DACPAC SQLDB SQLSERVERNAME INSTANCENAME sqlpackage SSDT azuredevopsmigration validateonly ImportType -->

# <a name="refactor-a-team-foundation-server-deployment-to-azure-devops-services"></a>Azure DevOps Services に Team Foundation Server の展開をリファクターする

この記事では、架空の会社である Contoso がオンプレミスの Visual Studio Team Foundation Server のデプロイを、Azure の Azure DevOps Services に移行することでリファクターする方法について説明します。 Contoso の開発チームは、過去 5 年間、チームのコラボレーションとソース管理に Team Foundation Server を使用してきました。 現在は、クラウドベースのソリューションに移行して、開発やテストの作業とソース管理を行いたいと考えています。 Contoso チームが Azure DevOps モデルに移行し、新しいクラウドネイティブ アプリケーションを開発する際に、Azure DevOps Services が役立ちます。

## <a name="business-drivers"></a>ビジネス ドライバー

Contoso の IT リーダーシップ チームは、ビジネス パートナーと密接に協力して将来の目標を明らかにしました。 パートナーは、開発ツールやテクノロジにあまり関心がありませんが、チームはこれらの点を把握しています。

- **ソフトウェア:** 中心の業務にかかわらず、Contoso を含め、すべての企業がソフトウェア企業になりました。 企業の上層部は、IT 部門がユーザーの新しい業務慣行や顧客の新しいエクスペリエンスを利用して業務を改善する方法に関心を持っています。
- **効率化:** Contoso はそのプロセスを合理化し、開発者とユーザーにとって不要な手順を取り除く必要があります。 そうすることで、顧客の要件により効率的に対応できるようになります。 IT 部門には、時間やお金を無駄にせず、迅速に行動できることが求められています。
- **機敏性:** Contoso の IT 部門は、グローバル経済における成功を実現するために、ビジネス ニーズに対しての対応力を向上させる必要があります。 市場の変化に対して、より迅速に対応できなければなりません。 IT がビジネスの妨げになったり、ビジネスの機会を壊すようなことがあってはなりません。

## <a name="migration-goals"></a>移行の目標

Contoso クラウド チームは、Azure DevOps Services への移行に関して、次の目標を設定しました。

- このチームには、データをクラウドに移行するツールが必要です。 手作業がほとんど不要になるようにする必要があります。
- 昨年の作業項目データと履歴を移行する必要があります。
- 新しいユーザー名とパスワードは設定したくありません。 現在のシステムの割り当てはすべて維持する必要があります。
- ソース管理については、Team Foundation バージョン管理 (TFVC) から Git に移行したいと考えています。
- Git への切り替えは、ソース コードの最新バージョンのみをインポートするチップ移行になります。 この切り替えは、コードベースの転換時にすべての作業が中止されるダウンタイム中に行われます。 チームは、移行後に最新のメイン ブランチ履歴しか利用できなくなることを理解しています。
- チームはこの変更を心配し、完全に移行する前にテストしたいと考えています。 チームは Azure DevOps Services への移行後も Team Foundation Server へのアクセスは維持したい考えです。
- チームは、複数のコレクションがあるので、プロセスを理解しやすいように、プロジェクトがごく少数のコレクションから始めたいと考えています。
- Team Foundation Server コレクションは Azure DevOps Services 組織と 1 対 1 の関係にあるため、URL は複数になることがわかっています。 ただし、これはコード ベースとプロジェクトの分離という現在のモデルと一致します。

## <a name="proposed-architecture"></a>提案されたアーキテクチャ

- Contoso は Team Foundation Server プロジェクトをクラウドに移行します。今後は、プロジェクトやソース管理をオンプレミスではホストしません。
- Team Foundation Server は Azure DevOps Services に移行されます。
- 現在、Contoso には `ContosoDev` という名前の Team Foundation Server コレクションが 1 つあります。これは `contosodevmigration.visualstudio.com` という Azure DevOps Services 組織に移行されます。
- 昨年のプロジェクト、作業項目、バグ、およびイテレーションは、Azure DevOps Services に移行されます。
- Contoso はその Azure Active Directory (Azure AD) インスタンスを利用します。これは、移行計画の開始時に [Azure インフラストラクチャをデプロイ](./contoso-migration-infrastructure.md)したときに設定したものです。

![提案されたアーキテクチャの図。](./media/contoso-migration-tfs-vsts/architecture.png)

## <a name="migration-process"></a>移行プロセス

Contoso は、次のようにして移行プロセスを完了します。

1. かなり多くの準備が必要です。 最初に、Contoso は Team Foundation Server の実装をサポートされるレベルまでアップグレードする必要があります。 Contoso は現在 Team Foundation Server 2017 Update 3 を稼働していますが、データベースの移行を使用するには、最新の更新プログラムが適用された、サポートされている 2018 バージョンを稼働する必要があります。
1. Contoso はアップグレード後、Team Foundation Server 移行ツールを実行し、そのコレクションを検証します。
1. Contoso は一連の準備ファイルを構築し、テストのために移行のドライ ランを実行します。
1. その後、もう一度移行を実行します。今度は、作業項目、バグ、スプリント、およびコードを含む完全な移行です。
1. 移行後、Contoso はコードを TFVC から Git に移動します。

![Contoso の移行プロセスの図。](./media/contoso-migration-tfs-vsts/migration-process.png)

## <a name="prerequisites"></a>前提条件

このシナリオを実行するには、Contoso は次の前提条件を満たす必要があります。

| 必要条件 | 詳細 |
| --- | --- |
| **Azure サブスクリプション** | このシリーズの先行する記事の中で、Contoso はサブスクリプションを作成しました。 Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/free/)を作成してください。 <br><br> 無料アカウントを作成する場合、サブスクリプションの管理者としてすべてのアクションを実行できます。 <br><br> 既存のサブスクリプションを使用しており、管理者でない場合は、管理者に依頼して所有者アクセス許可または共同作成者アクセス許可を割り当ててもらう必要があります。 <br><br> より詳細なアクセス許可が必要な場合は、「[Azure のロールベースのアクセス制御 (Azure RBAC) を使用して Site Recovery のアクセスを管理する](/azure/site-recovery/site-recovery-role-based-linked-access-control)」をご覧ください。 |
| **Azure インフラストラクチャ** | Contoso は、[移行のための Azure インフラストラクチャ](./contoso-migration-infrastructure.md)に関する記事で説明されているように、Azure インフラストラクチャを設定します。 |
| **オンプレミスの Team Foundation Server インスタンス** | オンプレミス インスタンスは、Team Foundation Server 2018 Upgrade 2 を稼働しているか、このプロセスの一環としてそのバージョンにアップグレードする必要があります。 |

## <a name="scenario-steps"></a>シナリオのステップ

Contoso は、次のようにして移行を完了します。

> [!div class="checklist"]
>
> - **ステップ 1:Azure ストレージ アカウントを作成する**。 このストレージ アカウントは、移行プロセス中に使用されます。
> - **手順 2:Team Foundation Server をアップグレードする**。 Contoso が、自社のデプロイを Team Foundation Server 2018 Upgrade 2 にアップグレードします。
> - **ステップ 3:Team Foundation Server コレクションを検証する**。 Contoso は、移行の準備として Team Foundation Server コレクションを検証します。
> - **手順 4:移行ファイルを作成する**。 Contoso は Team Foundation Server 移行ツールを使用して、移行ファイルを作成します。

## <a name="step-1-create-an-azure-storage-account"></a>ステップ 1: Azure Storage アカウントを作成する

1. Contoso の管理者は、Azure portal でストレージ アカウント (`contosodevmigration`) を作成します。
1. フェールオーバーに使用するセカンダリ リージョン (`Central US`) にアカウントを配置します。 汎用の標準アカウントとローカル冗長ストレージを使用します。

    ![**[ストレージ アカウントの作成]** ペインのスクリーンショット。](./media/contoso-migration-tfs-vsts/storage1.png)

**さらにサポートが必要な場合**

- [Azure Storage の概要](/azure/storage/common/storage-introduction)。
- [ストレージ アカウントの作成](/azure/storage/common/storage-account-create)。

<!-- docutune:casing "Server Configuration Wizard" "Configure Features Wizard" "Detach Team Project Collection Wizard" -->

## <a name="step-2-upgrade-team-foundation-server"></a>手順 2:Team Foundation Server をアップグレードする

Contoso の管理者が Team Foundation Server インスタンスを Team Foundation Server 2018 Update 2 にアップグレードします。 始める前に次の作業を行います。

- [Team Foundation Server 2018 Update 2](https://visualstudio.microsoft.com/downloads/) をダウンロードします。
- [ハードウェア要件](/azure/devops/server/requirements)を確認します。
- [リリース ノート](/visualstudio/releasenotes/tfs2018-relnotes)と[アップグレードの注意事項](/azure/devops/server/upgrade/get-started)を読みます。

次の手順でアップグレードします。

1. まず、VMware 仮想マシン (VM) 上で実行されている Team Foundation Server インスタンスを管理者がバックアップし、VMware のスナップショットを作成します。

    ![Team Foundation Server をアップグレードするための **[Getting Started]\(作業の開始\)** ペインのスクリーンショット。](./media/contoso-migration-tfs-vsts/upgrade1.png)

1. Team Foundation Server のインストーラーが起動したら、インストール先を選択します。 インストーラーはインターネットにアクセスできる必要があります。

    ![Visual Studio の Team Foundation Server インストール サイトのスクリーンショット。](./media/contoso-migration-tfs-vsts/upgrade2.png)

1. インストールが完了すると、サーバー構成ウィザードが起動します。

    ![Team Foundation Server 2018 Update 2 構成ウィザードのスクリーンショット。](./media/contoso-migration-tfs-vsts/upgrade3.png)

1. 検証後、サーバー構成ウィザードでアップグレードを完了します。

     ![Team Foundation Server 構成ウィザードの [アップグレードの進行状況] ペインのスクリーンショット。](./media/contoso-migration-tfs-vsts/upgrade4.png)

1. 管理者が、プロジェクト、作業項目、コードを確かめて Team Foundation Server のインストールを確認します。

     ![Team Foundation Server のインストールを確認するための **[製品バックログ]** ペインのスクリーンショット。](./media/contoso-migration-tfs-vsts/upgrade5.png)

> [!NOTE]
> 一部の Team Foundation Server アップグレードでは、アップグレードの完了後に機能の構成ウィザードを実行する必要があります。 [詳細については、こちらを参照してください](/previous-versions/azure/devops/reference/upgrade/configure-features-after-upgrade?view=azure-devops&preserve-view=true&viewFallbackFrom=vsts)。

**さらにサポートが必要な場合**

[Team Foundation Server のアップグレード](/azure/devops/server/upgrade/get-started)に関するページを参照してください。

## <a name="step-3-validate-the-team-foundation-server-collection"></a>手順 3:Team Foundation Server コレクションを検証する

Contoso の管理者は、移行前に `contosodev` コレクション データベースに対して Team Foundation Server 移行ツールを実行して検証します。

1. [Team Foundation Server 移行ツール](https://www.microsoft.com/download/details.aspx?id=54274)をダウンロードして解凍します。 稼働している Team Foundation Server Update に合ったバージョンをダウンロードすることが重要です。 バージョンは管理コンソールで確認できます。

    ![製品バージョンを確認するための Team Foundation Server ペインのスクリーンショット。](./media/contoso-migration-tfs-vsts/collection1.png)

1. 次のコマンドに示したとおり、プロジェクト コレクションの URL を指定して、検証を実行するツールを実行します。

    `TfsMigrator validate /collection:http://contosotfs:8080/tfs/ContosoDev`

    ツールにエラーが表示されます。

    ![Team Foundation Server 移行ツールの検証エラーのスクリーンショット。](./media/contoso-migration-tfs-vsts/collection2.png)

1. ログ ファイルは、ツールの場所の直下の `Logs` フォルダーにあります。 主要な検証ごとにログ ファイルが生成されます。 `TfsMigration.log` は主要な情報を保持しています。

    ![Team Foundation Server のログ ファイルの場所のスクリーンショット。](./media/contoso-migration-tfs-vsts/collection3.png)

1. このエントリが見つかります。これは ID に関連するものです。

    ![ID の検証中に発生したエラーを示すスクリーンショット。](./media/contoso-migration-tfs-vsts/collection4.png)

1. コマンド ラインで `TfsMigrator validate /help` を実行し、ID を検証するにはコマンドで `/tenantDomainName` が必要と思われることを確認します。

     ![ID を検証するために必要なコマンドを示すスクリーンショット。](./media/contoso-migration-tfs-vsts/collection5.png)

1. この値と Azure AD 名を指定して、検証コマンドをもう一度実行します (`TfsMigrator validate /collection:http://contosotfs:8080/tfs/ContosoDev /tenantDomainName:contosomigration.onmicrosoft.com`)。

    ![正しいコマンドを示すコマンド プロンプトのスクリーンショット。](./media/contoso-migration-tfs-vsts/collection7.png)

1. Azure AD サインイン ウィンドウが表示されたら、全体管理者ユーザーの資格情報を入力します。

     ![管理者資格情報が入力された Azure AD サインイン ウィンドウのスクリーンショット。](./media/contoso-migration-tfs-vsts/collection8.png)

1. 検証に合格します。これはツールで確認できます。

    ![検証に合格したことを示すスクリーンショット。](./media/contoso-migration-tfs-vsts/collection9.png)

## <a name="step-4-build-the-migration-files"></a>手順 4:移行ファイルを作成する

検証が完了すると、Contoso の管理者は Team Foundation Server 移行ツールを使用して移行ファイルを作成できます。

1. ツールで準備手順を実行します。

    `TfsMigrator prepare /collection:http://contosotfs:8080/tfs/ContosoDev /tenantDomainName:contosomigration.onmicrosoft.com /accountRegion:cus`

     ![Team Foundation Server 移行ツールの準備コマンドのスクリーンショット。](./media/contoso-migration-tfs-vsts/prep1.png)

    準備の手順では、以下を行います。
    - コレクションをスキャンしてすべてのユーザーの一覧を検索し、ID マップ ログ (`IdentityMapLog.csv`) を作成します。
    - Azure AD への接続を準備し、各 ID に一致するものを検索します。
    - Contoso は Azure AD を既にデプロイし、Azure AD Connect を使用して同期しているため、一致する ID を準備コマンドで検出し、**Active** とマークすることができます。

1. Azure AD のサインイン画面が表示されたら、管理者が全体管理者の資格情報を入力します。

    ![ユーザー テキスト ボックスに管理者資格情報が入力された Azure AD サインイン画面のスクリーンショット。](./media/contoso-migration-tfs-vsts/prep2.png)

1. 準備が完了すると、インポート ファイルが正常に生成されたことがレポートされます。

    ![コレクションの検証に成功したことを示す移行ツールのスクリーンショット。](./media/contoso-migration-tfs-vsts/prep3.png)

1. 管理者は、`IdentityMapLog.csv` ファイルと `import.json` ファイルの両方が新しいフォルダーに作成されていることを確認できます。

    ![準備](./media/contoso-migration-tfs-vsts/prep4.png)

1. `import.json` ファイルにはインポート設定が記録されています。 そこには、目的の組織名やストレージ アカウントの詳細などの情報が含まれています。 ほとんどのフィールドは自動的に設定されます。 一部のフィールドにはユーザー入力が必要です。 管理者はファイルを開き、作成する Azure DevOps Services 組織名 `contosodevmigration` を追加します。 この名前の場合、Contoso の Azure DevOps Services の URL は `contosodevmigration.visualstudio.com` になります。

    ![Azure DevOps Services 組織名を示すスクリーンショット。](./media/contoso-migration-tfs-vsts/prep5.png)

    > [!NOTE]
    > 組織は移行の開始前に作成しておく必要があります。 これは移行の完了後に変更することができます。

1. 管理者が ID ログ マップ ファイルを確認します。そこには、インポート中に Azure DevOps Services に取り込まれるアカウントが示されます。

    - アクティブな ID とは、インポート後に Azure DevOps Services 内のユーザーになる ID のことです。
    - Azure DevOps Services で、これらの ID にライセンスが付与され、移行後に組織のユーザーとして表示されます。
    - これらの ID は、ファイルの **Expected Import Status** 列で **Active** とマークされています。

    ![Azure DevOps Services に取り込まれるアカウントを示す ID ログ マップ ファイルのスクリーンショット。](./media/contoso-migration-tfs-vsts/prep6.png)

## <a name="step-5-migrate-to-azure-devops-services"></a>手順 5:Azure DevOps Services に移行する

準備が完了したら、Contoso の管理者は移行に専念することができます。 移行を実行すると、バージョン管理には TFVC ではなく Git が使用されるようになります。

管理者はまず開発チームと共にダウンタイムのスケジュールを設定します。そうすることで、移行に備えてコレクションをオフラインにするための計画を立てることができます。

移行プロセスの手順は次のとおりです。

1. **コレクションをデタッチする。** コレクションの ID データは、コレクションがアタッチされ、オンラインのときは Team Foundation Server インスタンスの構成データベース内にあります。

    コレクションが Team Foundation Server インスタンスからデタッチされると、その ID データのコピーが作成され、転送のためにコレクションと共にパッケージ化されます。 インポートの ID 部分を実行するには、このデータが必要です。

    インポートが完了するまではコレクションをデタッチしたままにすることをお勧めします。インポート中に発生した変更はインポートできません。

1. **バックアップを生成する。** 次の手順は、Azure DevOps Services にインポートできるバックアップを生成することです。 データ層アプリケーション コンポーネント パッケージ (DACPAC) は、データベースの変更を単一のファイルにパッケージ化し、SQL の他のインスタンスにデプロイすることができる SQL Server の機能です。

    また、バックアップは、Azure DevOps Services に直接復元することもでき、コレクション データをクラウドに取り込むためのパッケージ化方法として使用されます。 Contoso は `sqlpackage.exe` ツールを使用して DACPAC を生成します。 このツールは、SQL Server Data Tools に含まれています。

1. **ストレージにアップロードする。** DACPAC が作成されたら、管理者がそれを Azure Storage にアップロードします。 アップロードが完了したら、Shared Access Signature (SAS) を取得し、Team Foundation Server 移行ツールからストレージにアクセスできるようにします。
1. **インポート ファイルに入力する。** Contoso は、DACPAC 設定など、インポート ファイルに足りないフィールドに入力します。 完全な移行の前にすべてが正常に機能していることを確認するため、管理者は、"*ドライラン*" インポートを実行するよう指定します。
1. **dry-run インポートを実行します。** ドライラン インポートは、コレクションの移行をテストするのに役立ちます。 ドライ ランは保存期間が限られているため、運用環境の移行が実行される前に削除されます。 それらは、設定期間が経過すると、自動的に削除されます。 ドライ ランが削除される時期を Contoso に知らせる注意は、インポートが完了した後に送信される成功メールに記載されています。 チームは注意を確認し、それに応じて計画を立てます。
1. **運用環境の移行を完了する。** ドライランによる移行が完了したら、Contoso の管理者は `import.json` ファイルを更新し、インポートを再実行して、最終的な移行を実行します。

<!-- docutune:casing "Team Foundation Server Administration Console" -->

### <a name="detach-the-collection"></a>コレクションをデタッチする

コレクションをデタッチする前に、Contoso の管理者は、ローカル SQL Server インスタンスのバックアップと Team Foundation Server インスタンスの VMware スナップショットを作成します。

1. 管理者は、Team Foundation Server 管理コンソールで、デタッチするコレクション (**ContosoDev**) を選択します。

    ![Foundation Server 管理コンソールの **[チーム プロジェクト コレクション]** セクションのスクリーンショット。](./media/contoso-migration-tfs-vsts/migrate1.png)

1. **[全般]** タブを選択し、 **[コレクションのデタッチ]** を選択します。

    ![Foundation Server 管理コンソールの **[コレクションのデタッチ]** リンクのスクリーンショット。](./media/contoso-migration-tfs-vsts/migrate2.png)

1. **チーム プロジェクト コレクションのデタッチ** ウィザードの **[サービス メッセージ]** ペインで、コレクション内のプロジェクトに接続しようとするユーザー向けのメッセージを管理者が入力します。

    ![チーム プロジェクト コレクションのデタッチ ウィザードの **[サービス メッセージ]** ペインのスクリーンショット。](./media/contoso-migration-tfs-vsts/migrate3.png)

1. **[デタッチの進行状況]** ペインで進行状況を監視します。 処理が完了したら、 **[次へ]** を選択します。

    ![チーム プロジェクト コレクションのデタッチ ウィザードの **[デタッチの進行状況]** ペインのスクリーンショット。](./media/contoso-migration-tfs-vsts/migrate5.png)

1. **[準備チェック]** ペインでチェックが完了したら、 **[デタッチ]** を選択します。

    ![チーム プロジェクト コレクションのデタッチ ウィザードの **[適合性チェック]** ペインのスクリーンショット。](./media/contoso-migration-tfs-vsts/migrate4.png)

1. コレクションが正常にデタッチされたら、 **[閉じる]** を選択して完了します。

    ![チーム プロジェクト コレクションのデタッチ ウィザードの **[完了]** ペインのスクリーンショット。](./media/contoso-migration-tfs-vsts/migrate6.png)

    コレクションは Team Foundation Server 管理コンソールで参照されなくなります。

    ![コレクションが一覧から消えていることを示す Team Foundation Server 管理コンソールのスクリーンショット。](./media/contoso-migration-tfs-vsts/migrate7.png)

### <a name="generate-a-dacpac"></a>DACPAC を生成する

Azure DevOps Services にインポートするためのバックアップ (DACPAC) を Contoso の管理者が作成します。

- 管理者は DACPAC の作成に SQL Server Data Tools (SSDT) の `sqlpackage.exe` ユーティリティを使用します。 SQL Server Data Tools と一緒に `sqlpackage.exe` の複数のバージョンがインストールされ、`120`、`130`、`140` などの名前が付いたフォルダーに配置されます。 適切なバージョンを使用して DACPAC を準備することが重要です。

- Team Foundation Server 2018 のインポートでは、*140* 以降のフォルダーの `sqlpackage.exe` を使用する必要があります。 `CONTOSOTFS` の場合、このファイルは `C:\Program Files (x86)\Microsoft Visual Studio\2017\Enterprise\Common7\IDE\Extensions\Microsoft\SQLDB\DAC\140` にあります。

Contoso の管理者は次のように DACPAC を生成します。

1. コマンド プロンプトを開き、`sqlpackage.exe` の場所に移動します。 DACPAC を生成するには、次のコマンドを実行します。

    `SqlPackage.exe /sourceconnectionstring:"Data Source=SQLSERVERNAME\INSTANCENAME;Initial Catalog=Tfs_ContosoDev;Integrated Security=True" /targetFile:C:\TFSMigrator\Tfs_ContosoDev.dacpac /action:extract /p:ExtractAllTableData=true /p:IgnoreUserLoginMappings=true /p:IgnorePermissions=true /p:Storage=Memory`

    ![DACPAC を生成するためのコマンドを表示するコマンド プロンプトのスクリーンショット。](./media/contoso-migration-tfs-vsts/backup1.png)

    次のメッセージが表示されます。

    ![データベースが正常に抽出されて DACPAC ファイルに保存されたというメッセージを表示するコマンド プロンプトのスクリーンショット。](./media/contoso-migration-tfs-vsts/backup2.png)

1. DACPAC ファイルのプロパティを確認します。

    ![確認する DACPAC ファイルのプロパティを表示するスクリーンショット。](./media/contoso-migration-tfs-vsts/backup3.png)

### <a name="upload-the-file-to-storage"></a>ファイルをストレージにアップロードする

管理者が、作成した DACPAC ファイルを Azure Storage アカウントにアップロードします。

1. [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) をダウンロードしてインストールします。

    ![**Storage Explorer の無料ダウンロード**のスクリーンショット。](./media/contoso-migration-tfs-vsts/backup5.png)

1. Storage Explorer で、管理者がそのサブスクリプションに接続し、移行用に作成したストレージ アカウント (`contosodevmigration`) を検索して選択します。 新しい BLOB コンテナー `azuredevopsmigration` を作成します。

    ![Storage Explorer の **[BLOB コンテナーの作成]** リンクのスクリーンショット。](./media/contoso-migration-tfs-vsts/backup6.png)

1. DACPAC ファイルのアップロードについて、 **[ファイルのアップロード]** ペインの **[BLOB の種類]** ボックスの一覧から、管理者が **[ブロック BLOB]** を指定します。

    ![Storage Explorer の **[ファイルのアップロード]** ペインのスクリーンショット。](./media/contoso-migration-tfs-vsts/backup7.png)

1. ファイルのアップロード後、ファイル名を選択し、 **[SAS の生成]** を選択します。 ストレージ アカウントの **[BLOB コンテナー]** 一覧を展開し、インポート ファイルを含むコンテナーを選択して、 **[Shared Access Signature の取得]** を選択します。

    ![Storage Explorer の **[Shared Access Signature の取得]** リンクのスクリーンショット。](./media/contoso-migration-tfs-vsts/backup8.png)

1. **[Shared Access Signature]** ペインで既定の設定をそのまま使用し、 **[作成]** を選択します。 これで、24 時間のアクセスが可能になります。

    ![Storage Explorer の **[Shared Access Signature]** ペインのスクリーンショット。](./media/contoso-migration-tfs-vsts/backup9.png)

1. Shared Access Signature の URL をコピーし、Team Foundation Server 移行ツールで使用できるようにします。

    ![Storage Explorer の Shared Access Signature の URL のスクリーンショット。](./media/contoso-migration-tfs-vsts/backup10.png)

> [!NOTE]
> 許可された期間内に移行が完了する必要があります。完了しない場合、アクセス許可は期限切れになります。 Azure portal からは SAS キーを生成 "*しない*" でください。 ポータルから生成されたキーは、アカウント スコープであり、インポートには使用できません。

### <a name="fill-in-the-import-settings"></a>インポート設定を入力する

以前、Contoso の管理者はインポート仕様ファイル (`import.json`) の一部を入力していました。 今回は、残りの設定を追加する必要があります。

`import.json` ファイルを開き、次のフィールドに入力します。

- **[場所]:** 上記で生成された SAS キーの場所を入力します。
- **DACPAC:** 先ほどストレージ アカウントにアップロードした DACPAC ファイルの名前を入力します。拡張子 *.dacpac* を含める必要があります。
- **ImportType:** ここでは「**DryRun**」を入力します。

![フィールドの入力が済んだ `import.json` ファイルのスクリーンショット。](./media/contoso-migration-tfs-vsts/import1.png)

### <a name="perform-a-dry-run-migration"></a>dry-run による移行を実行する

Contoso の管理者はドライ ラン移行を実行し、すべてが想定どおりに動作していることを確認します。

1. コマンド プロンプトを開き、`TfsMigrator` の場所 (`C:\TFSMigrator`) に移動します。
1. ファイルの形式が正しいこと、SAS キーが正常に機能していることを確認する必要があります。 次のコマンドを実行して、インポート ファイルを検証します。

    `TfsMigrator import /importFile:C:\TFSMigrator\import.json /validateonly`

    この検証で、SAS キーの有効期間を長くする必要があるというエラーが返されます。

    ![検証エラーを表示するコマンド プロンプトのスクリーンショット。](./media/contoso-migration-tfs-vsts/test1.png)

1. Azure Storage Explorer を使用して期限切れまでの期間を 7 日間に設定した新しい SAS キーを作成します。

    ![有効期限を表示する Storage Explorer の **[Shared Access Signature]** ペインのスクリーンショット。](./media/contoso-migration-tfs-vsts/test2.png)

1. `import.json` ファイルを更新し、コマンドを再実行します。 今度は、検証が正常に完了します。

    `TfsMigrator import /importFile:C:\TFSMigrator\import.json /validateonly`

    ![**検証が正常に完了した**というメッセージを表示するコマンド プロンプトのスクリーンショット。](./media/contoso-migration-tfs-vsts/test3.png)

1. 次のコマンドを実行してドライ ランを開始します。

    `TfsMigrator import /importFile:C:\TFSMigrator\import.json`

    移行を続行するかどうかの確認を求めるメッセージが表示されます。 ステージング データが維持される期間がドライ ラン後 7 日間であることに注目してください。

    ![移行を続行するかどうかの確認を Contoso に求めるメッセージのスクリーンショット。](./media/contoso-migration-tfs-vsts/test4.png)

1. Azure AD のサインイン ウィンドウが表示されます。 Contoso の管理者が Azure AD に管理者アクセス許可でサインインします。

    ![Visual Studio の Azure AD サインイン ウィンドウのスクリーンショット。](./media/contoso-migration-tfs-vsts/test5.png)

    インポートが正常に開始されたことを確認するメッセージが表示されます。

    ![インポートが正常に開始されたことを示すスクリーンショット。](./media/contoso-migration-tfs-vsts/test6.png)

1. 約 15 分後、管理者が Web サイトにアクセスして次の情報を確認します。

     ![コレクションが復元されていることを示すスクリーンショット。](./media/contoso-migration-tfs-vsts/test7.png)

1. 移行が完了したら、Contoso の開発リーダーは Azure DevOps Services にサインインし、ドライ ランが正常に動作していることを確認します。 認証後、Azure DevOps Services は、組織を確認するいくつかの詳細情報が必要です。

    ![Contoso チームに追加情報を求める Azure DevOps Services ウィンドウのスクリーンショット。](./media/contoso-migration-tfs-vsts/test8.png)

    開発リーダーは、プロジェクトが正常に移行されたことを確認できます。 ドライ ラン アカウントが 15 日後に削除されるという警告がページの上部付近に表示されています。

    ![ドライ ラン アカウントが 15 日後に削除されることを警告するメッセージが表示された Azure DevOps Services インターフェイスのスクリーンショット。](./media/contoso-migration-tfs-vsts/test9.png)

1. 開発リーダーはいずれかのプロジェクトを開き、 **[作業項目]**  >  **[自分に割り当て済み]** を選択します。 作業項目データが ID と共に正常に移行されたことが、このページで確認できます。

    ![移行済みの全プロジェクトを表示する Azure DevOps Services の **[作業項目]** ペインのスクリーンショット。](./media/contoso-migration-tfs-vsts/test10.png)

1. ソース コードと履歴が移行されたことを確認するために、開発リーダーは、他のプロジェクトとコードをチェックします。

    ![すべてのコードと履歴が移行されたことを示す Azure DevOps Services の **[履歴]** ペインのスクリーンショット。](./media/contoso-migration-tfs-vsts/test11.png)

### <a name="run-the-production-migration"></a>運用環境の移行を実行する

ドライ ランが完了したら、Contoso の管理者は運用環境の移行に進みます。 ドライ ランを削除し、インポート設定を更新し、インポートをもう一度実行します。

1. Azure DevOps Services ポータルで、dry-run 用の組織を削除します。
1. **ImportType** を **ProductionRun** に設定するように `import.json` ファイルを更新します。

    ![ImportType フィールドが ProductionRun に設定された Azure DevOps Services ポータルのスクリーンショット。](./media/contoso-migration-tfs-vsts/full1.png)

1. ドライ ランのときと同様に、次のコマンドを実行して移行を開始します。

    `TfsMigrator import /importFile:C:\TFSMigrator\import.json`.

    移行を実行するかどうかの確認を管理者に求めるメッセージが表示されます。 セキュリティで保護されている場所に、最大 7 日間、ステージング領域としてデータを保持できるという警告が表示されます。

    ![移行データの保持期間が最大 7 日間であることを警告する Azure DevOps Services メッセージのスクリーンショット。](./media/contoso-migration-tfs-vsts/full2.png)

1. Azure AD のサインイン ウィンドウで、Contoso 管理者のサインイン情報を指定します。

    ![Visual Studio の Azure AD サインイン画面のスクリーンショット。](./media/contoso-migration-tfs-vsts/full3.png)

    インポートが正常に開始されたというメッセージが表示されます。

    ![インポートが正常に開始されたという Azure DevOps Services メッセージのスクリーンショット。](./media/contoso-migration-tfs-vsts/full4.png)

1. 約 15 分後、管理者が Web サイトにアクセスして次の情報を確認します。

    ![データがクラウドにコピーされていることを示すスクリーンショット。](./media/contoso-migration-tfs-vsts/full5.png)

1. 移行が完了したら、開発リーダーは Azure DevOps Services にサインインして、移行が適切に行われたことを確認します。 開発リーダーは、サインイン後に、プロジェクトが移行されたことを確認できます。

    ![プロジェクトが移行されたことを示すスクリーンショット。](./media/contoso-migration-tfs-vsts/full6.png)

1. 開発リーダーはいずれかのプロジェクトを開き、 **[作業項目]**  >  **[自分に割り当て済み]** を選択します。 作業項目データが ID と共に移行されたことが表示されます。

    ![作業項目データと ID が移行されたことを示すスクリーンショット。](./media/contoso-migration-tfs-vsts/full7.png)

1. 開発リーダーは、他の作業項目データが移行済みであることを確認します。

    ![確認すべきその他の作業項目データを一覧表示する画面のスクリーンショット。](./media/contoso-migration-tfs-vsts/full8.png)

1. ソース コードと履歴が移行されたことを確認するために、開発リーダーは、他のプロジェクトとコードをチェックします。

    ![移行の確認対象としてその他のプロジェクトおよびソース コードを一覧表示する画面のスクリーンショット。](./media/contoso-migration-tfs-vsts/full9.png)

### <a name="move-source-control-from-tfvc-to-git"></a>ソース管理を TFVC から Git に移行する

移行が完了したら、Contoso の管理者は、ソース コード管理を TFVC から Git に移行する必要があります。 管理者は、同一組織での Git リポジトリとして Azure DevOps Services 組織における現在のソース コードをインポートする必要があります。

1. Azure DevOps Services ポータルで、TFVC リポジトリ (`$/PolicyConnect`) のいずれかを開いて確認します。

    ![Azure DevOps Services ポータルの **$/PolicyConnect** リポジトリのスクリーンショット。](./media/contoso-migration-tfs-vsts/git1.png)

1. ソースの **[$/PolicyConnect]** ボックスの一覧から、 **[リポジトリのインポート]** を選択します。

    ![Azure DevOps Services ポータルの **[リポジトリのインポート]** リンクのスクリーンショット。](./media/contoso-migration-tfs-vsts/git2.png)

1. **[Source type]\(ソースの種類\)** ボックスの一覧から **[TFVC]** を選択します。 **[パス]** ボックスにリポジトリのパスを指定し、 **[インポート]** を選択します。 **[履歴の移行]** チェック ボックスはオフのままとします。

    ![**[TFVC からインポートする]** ペインのスクリーンショット。](./media/contoso-migration-tfs-vsts/git3.png)

    > [!NOTE]
    > TFVC と Git とではバージョン管理情報の保存方法が異なるので、リポジトリの履歴は移行 "*しない*" ことをお勧めします。 これは、Windows や他の製品を一元管理されたバージョン管理から Git に移行したときに Microsoft が採用したアプローチです。

1. インポートの完了後、管理者はコードを確認します。

    ![インポートが成功したことを示すスクリーンショット。](./media/contoso-migration-tfs-vsts/git4.png)

1. 2 つ目のリポジトリ (`$/smarthotelcontainer`) について、同じプロセスを繰り返します。

    ![2 つ目のリポジトリの **[TFVC からインポートする]** ペインのスクリーンショット。](./media/contoso-migration-tfs-vsts/git5.png)

1. ソースを確認した後、開発リーダーたちは Azure DevOps Services への移行が完了したことに同意します。 今後は Azure DevOps Services が、この移行にかかわったチームのすべての開発のソースになります。

    ![Azure DevOps Services への移行が完了したことを示すスクリーンショット。](./media/contoso-migration-tfs-vsts/git6.png)

**さらにサポートが必要な場合**

詳細については、「[TFVC から Git にリポジトリをインポートする](/azure/devops/repos/git/import-from-tfvc?view=azure-devops&preserve-view=true&viewFallbackFrom=vsts)」を参照してください。

## <a name="clean-up-after-migration"></a>移行後にクリーンアップする

移行が完了したら、Contoso チームは次のことを行う必要があります。

- 追加のインポート アクティビティについては、[インポート後](/azure/devops/migrate/migration-post-import?view=azure-devops&preserve-view=true&viewFallbackFrom=vsts)に関する記事を参照してください。
- TFVC リポジトリを削除するか、読み取り専用モードに設定します。 コード ベースは使用しないでください。ただし、その履歴を参照することはできます。

## <a name="post-migration-training"></a>移行後のトレーニング

Contoso チームは関連するチーム メンバーに Azure DevOps Services と Git のトレーニングを用意する必要があります。
