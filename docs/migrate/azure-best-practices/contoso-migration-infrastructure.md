---
title: 移行インフラストラクチャをデプロイする
description: Contoso が Azure への移行用に Azure インフラストラクチャを設定する方法について説明します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 10/1/2018
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: migrate
services: azure-migrate
ms.openlocfilehash: 4d8a7b53722de4b356753626d0cc695fa1a77596
ms.sourcegitcommit: 2362fb3154a91aa421224ffdb2cc632d982b129b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2020
ms.locfileid: "76807514"
---
# <a name="deploy-a-migration-infrastructure"></a>移行インフラストラクチャをデプロイする

この記事では、架空の会社 Contoso がそのオンプレミス インフラストラクチャを移行のために準備し、移行に備えて Azure インフラストラクチャを設定した後、ハイブリッド環境でビジネスを実行する方法を示します。 この例を独自のインフラストラクチャ移行作業の計画に役立てるために使用する場合は、次の点に注意してください。

- 示されているサンプル アーキテクチャは Contoso に固有のものです。 サブスクリプション設計またはネットワーク アーキテクチャに関するインフラストラクチャの重要な決定を行う場合は、自分の組織のビジネス ニーズ、構造、および技術的な要件を確認してください。
- この記事で説明されている要素がすべて必要かどうかは移行戦略によって異なります。 たとえば、Azure でクラウド ネイティブ アプリのみを作成する場合は、あまり複雑ではないネットワーク構造が必要になることがあります。

## <a name="overview"></a>概要

Contoso が Azure への移行を実施するには、その前に Azure インフラストラクチャを準備することが重要です。 一般に、Contoso が考慮する必要のある広範囲の領域には次の 6 つがあります。

> [!div class="checklist"]
>
> - **ステップ 1:Azure サブスクリプション。** Contoso が Azure を購入する方法、および Azure プラットフォームやサービスと対話する方法。
> - **手順 2:ハイブリッド ID。** 移行後にオンプレミスおよび Azure のリソースへのアクセスを管理および制御する方法。 Contoso が ID 管理をクラウドに拡張または移行する方法。
> - **ステップ 3:ディザスター リカバリーと回復性。** 停止や障害が発生した場合のアプリとインフラストラクチャの回復性を Contoso が保証する方法。
> - **手順 4:ネットワーク。** Contoso がネットワーク インフラストラクチャを設計する方法、およびオンプレミスのデータ センターと Azure 間の接続を確立する方法。
> - **手順 5:セキュリティ。** ハイブリッド/Azure デプロイをセキュリティで保護する方法。
> - **手順 6:ガバナンス。** Contoso での展開がセキュリティおよびガバナンスの要件を満たし続ける方法。

## <a name="before-you-start"></a>開始する前に

インフラストラクチャについて始める前に、この記事で説明する Azure の機能に関する背景情報を読みたいかもしれません。

- Azure へのアクセスを購入するには、従量課金制、Enterprise Agreement (EA)、Microsoft リセラーやクラウド ソリューション プロバイダー (CSP) と呼ばれる Microsoft パートナーからのオープン ライセンスなどのいくつかのオプションを使用できます。 [購入オプション](https://azure.microsoft.com/pricing/purchase-options)について学習し、[Azure サブスクリプションの編成](https://azure.microsoft.com/blog/organizing-subscriptions-and-resource-groups-within-the-enterprise)方法をお読みください。
- Azure の [ID 管理とアクセス管理](https://www.microsoft.com/trustcenter/security/identity)の概要をご覧ください。 特に、[Azure AD およびオンプレミスの Active Directory のクラウドへの拡張](https://docs.microsoft.com/azure/active-directory/identity-fundamentals)について学習してください。 [ハイブリッド環境での ID とアクセス管理 (IAM)](https://azure.microsoft.com/resources/hybrid-cloud-identity) について役に立つ電子書籍をダウンロードできます。
- Azure は、ハイブリッド接続のオプションを含む堅牢なネットワーク インフラストラクチャを提供します。 [ネットワークとネットワーク アクセス制御](https://docs.microsoft.com/azure/security/security-network-overview)の概要についてはこちらをご覧ください。
- [Azure セキュリティ](https://docs.microsoft.com/azure/security/azure-security)の概要を理解し、[ガバナンス](https://docs.microsoft.com/azure/security/governance-in-azure)計画の作成についてお読みください。

## <a name="on-premises-architecture"></a>オンプレミス アーキテクチャ

以下の図は、現在の Contoso オンプレミス アーキテクチャを示しています。

 ![Contoso アーキテクチャ](./media/contoso-migration-infrastructure/contoso-architecture.png)

- Contoso は、米国東部のニューヨーク市に 1 つのメイン データセンターを配置しています。
- Contoso は、米国の地方に 3 つの支店を配置しています。
- メイン データセンターは、光ファイバーによるイーサネット接続 (500 mbps) を通じて、インターネットに接続されています。
- 各支店は、ビジネス クラスの接続を使用して、インターネットにローカルで接続されています。メイン データセンターには、IPSec VPN トンネルで接続されています。 これにより、ネットワーク全体を永続的に接続でき、インターネット接続が最適化されます。
- メイン データセンターは、VMware によって完全に仮想化されています。 Contoso には ESXi 6.5 仮想化ホストが 2 つあり、vCenter Server 6.5 で管理されています。
- Contoso は、ID 管理に Active Directory を使用しており、内部ネットワーク上で DNS サーバーを使用しています。
- データセンター内のドメイン コントローラーは、VMware VM 上で実行されています。 支店にあるドメイン コントローラーは、物理サーバー上で実行されています。

## <a name="step-1-buy-and-subscribe-to-azure"></a>手順 1:Azure を購入して登録する

Contoso は、Azure の購入方法、サブスクリプションの設計方法、およびサービスとリソースのライセンスを取得する方法を明らかにする必要があります。

### <a name="buy-azure"></a>Azure を購入する

Contoso は [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement) を利用します。 その場合、Azure に年額コミットメントをあらかじめ支払い、Contoso には柔軟な課金オプションや料金の最適化などの大きなメリットがあります。

- Contoso は Azure への年間の支出を推定しました。 契約に署名する時点で、Contoso は最初の 1 年間の料金を払いました。
- Contoso は 1 年が過ぎる前にすべてのコミットメントを使用する必要があります。そうしないと、金銭的価値を失うことになります。
- 何らかの理由で Contoso がコミットメントを超えて使うと、Microsoft は差額を請求します。
- コミットメントを超えて発生したコストはすべて、Contoso のコントラクトに記載されているものと同じレートになります。 超過に対するペナルティはありません。

### <a name="manage-subscriptions"></a>サブスクリプションの管理

Azure の料金を支払った後、Contoso は Azure サブスクリプションを管理する方法を決める必要があります。 Contoso は EA を利用しているので、設定できる Azure サブスクリプションの数に制限はありません。

- Azure エンタープライズ加入契約では、企業が Azure サービスを構成して使用する方法、および中心となるガバナンス構造が定義されています。
- 最初の手順として、Contoso は、エンタープライズ加入契約のためのエンタープライズ スキャフォールディングと呼ばれる構造を定義しました。 Contoso では、スキャフォールディングを理解して設計するために、[こちらの記事](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-subscription-governance)を読みました。
- ここでは、Contoso は機能的アプローチを使用してサブスクリプションを管理することを決定しました。
  - 会社内では、Azure の予算を制御する 1 つの IT 部門を使用します。 これがサブスクリプションを持つ唯一のグループになります。
  - Contoso は将来的にこのモデルを拡張し、会社の他のグループもエンタープライズ登録に部門として参加できるようにする意向です。
  - IT 部門内には、製造と開発の 2 つのサブスクリプションが構成されています。
  - 将来、サブスクリプションを追加する必要がある場合は、それらのサブスクリプションのアクセス、ポリシー、コンプライアンスを管理する必要があります。 Contoso では、サブスクリプションの上に追加の階層として [Azure 管理グループ](https://docs.microsoft.com/azure/azure-resource-manager/management-groups-overview)を導入することで、この管理作業を行うことができます。

  ![エンタープライズの構造](./media/contoso-migration-infrastructure/enterprise-structure.png)

### <a name="examine-licensing"></a>ライセンスを調べる

サブスクリプションの構成が済むと、Contoso は Microsoft のライセンスを確認できます。 ライセンス戦略は、Contoso が Azure に移行しようとしているリソースや、Azure VM およびサービスが選択されデプロイされる方法によって異なります。

#### <a name="azure-hybrid-benefit"></a>Azure ハイブリッド特典

Azure に VM を展開するとき、標準イメージにはソフトウェアが使用された分単位で Contoso に請求されるライセンスが含まれます。 ただし、Contoso は Microsoft の顧客であった期間が長く、EA およびソフトウェア アシュアランス (SA) 付きのオープン ライセンスを持っています。

Azure ハイブリッド特典ではコスト効果の高い方法が Contoso の移行に対して提供され、Contoso はソフトウェア アシュアランスでカバーされた Windows Server Datacenter および Standard Edition のライセンスを変換または再利用をすることにより、Azure VM と SQL Server のワークロードを節約できます。 これにより、Contoso が VM と SQL Server に対して支払う基本コンピューティング料金が低くなります。 [詳細については、こちらを参照してください](https://azure.microsoft.com/pricing/hybrid-benefit)。

#### <a name="license-mobility"></a>ライセンス モビリティ

SA によるライセンス モビリティを利用すると、Contoso のような Microsoft ボリューム ライセンスのお客様は、Azure でのアクティブな SA によって、ライセンス モビリティに対応するサーバー アプリを柔軟に展開できます。 これにより、新しいライセンスを購入する必要がなくなります。 関連するモビリティ料金なしで、既存のライセンスを Azure に簡単に展開できます。 [詳細については、こちらを参照してください](https://azure.microsoft.com/pricing/license-mobility)。

#### <a name="reserve-instances-for-predictable-workloads"></a>予測可能なワークロードのためにインスタンスを予約する

予測可能なワークロードとは、実行されている VM で常に使用できるようにする必要があるワークロードのことです。 たとえば、SAP ERP システムのような基幹業務アプリなどです。 これに対して、予測不可能なワークロードは、要求が多ければ有効になり少なければ無効になる VM などの、変わりやすいワークロードです。

![予約インスタンス](./media/contoso-migration-infrastructure/reserved-instance.png)

長時間維持する必要があることがわかっている特定の VM インスタンスに対して予約インスタンスを使用する見返りに、Contoso は割引と優先容量の両方を得ることができます。 [Azure 予約インスタンス](https://azure.microsoft.com/pricing/reserved-vm-instances)を Azure ハイブリッド特典と共に利用することで、Contoso は正規の従量課金制料金を 82% 節約できます (2018 年 4 月現在)。

## <a name="step-2-manage-hybrid-identity"></a>手順 2:ハイブリッドの ID を管理する

ID およびアクセス管理 (IAM) によってユーザーが Azure リソースにアクセスできるようにして、アクセスを制御することは、Azure インフラストラクチャをまとめ上げるための重要なステップです。

- Contoso では、Azure に新しい別個のシステムを構築するのではなく、オンプレミスの Active Directory をクラウドに拡張することに決めています。
- これを行うために、Azure ベースの Active Directory を作成します。
- Contoso は Office 365 を利用していないので、新しい Azure AD をプロビジョニングする必要があります。
- Office 365 では、ユーザー管理用に Azure AD が使われています。 Contoso が Office 365 を使用していたとすれば、既に Azure AD テナントが存在するため、それをプライマリ ディレクトリとして使用できます。
- Office 365 の Azure AD に関して[詳しくはこちらをご覧ください](https://support.office.com/article/understanding-office-365-identity-and-azure-active-directory-06a189e7-5ec6-4af2-94bf-a22ea225a7a9)。また、既存の Azure AD テナントに[サブスクリプションを追加する方法](https://docs.microsoft.com/azure/active-directory/active-directory-how-subscriptions-associated-directory)を学習してください。

### <a name="create-an-azure-ad"></a>Azure AD を作成する

Contoso は、Azure サブスクリプションに含まれる Azure AD Free エディションを使っています。 Contoso 管理者は、次のようにディレクトリを設定します。

1. [Azure Portal](https://portal.azure.com) で、 **[リソースの作成]**  >  **[ID]**  >  **[Azure Active Directory]** の順に移動します。
2. **[ディレクトリの作成]** で、ディレクトリの名前、初期ドメイン名、および Azure AD ディレクトリが作成されるリージョンを指定します。

    ![Azure AD を作成する](./media/contoso-migration-infrastructure/azure-ad-create.png)

    > [!NOTE]
    > 作成したディレクトリは、**domainname.onmicrosoft.com** という形式の初期ドメイン名になります。 この名前を変更または削除することはできません。 代わりに、登録されたドメイン名を Azure AD に追加する必要があります。

### <a name="add-the-domain-name"></a>ドメイン名を追加する

標準のドメイン名を使用するには、Contoso の管理者がカスタム ドメイン名として Azure AD に追加する必要があります。 この方法を使うと、管理者は使い慣れたユーザー名を割り当てることができます。 たとえば、ユーザーはメール アドレス billg@contoso.com でログインでき、billg@contosomigration.microsoft.com を使う必要はありません。

カスタム ドメイン名を設定するには、ディレクトリにカスタム ドメイン名を追加し、DNS エントリを追加してから、Azure AD で名前を確認します。

1. **[カスタム ドメイン名]**  >  **[カスタム ドメインの追加]** で、ドメインを追加します。
2. Azure で DNS エントリを使うには、ドメイン レジストラーに DNS エントリを登録する必要があります。

    - **[カスタム ドメイン名]** の一覧で、名前の DNS 情報を記録します。 MX エントリを使用しています。
    - これを行うには、ネーム サーバーにアクセスする必要があります。 Contoso.com ドメインにログインし、記録した詳細情報を使って、Azure AD によって提供された DNS エントリの新しい MX レコードを作成します。

3. DNS レコードが伝播されたら、ドメインの詳細名で、 **[検証]** を選択してカスタム ドメイン名を確認します。

     ![Azure AD DNS](./media/contoso-migration-infrastructure/azure-ad-dns.png)

### <a name="set-up-on-premises-and-azure-groups-and-users"></a>オンプレミスと Azure でグループとユーザー設定する

Azure AD が稼働状態になったので、Contoso 管理者は、Azure Active Directory に同期されるオンプレミスの Active Directory グループに従業員を追加する必要があります。 Azure でのリソース グループの名前と一致するように、オンプレミスのグループ名を設定する必要があります。 これにより、同期を行うために一致するものを識別しやすくします。

#### <a name="create-resource-groups-in-azure"></a>Azure でリソース グループを作成する

Azure リソース グループは Azure リソースをひとまとめにしたものです。 リソース グループ ID を使うことで、Azure はグループ内のリソースに対して操作を実行できます。

- 1 つの Azure サブスクリプションで複数のリソース グループを使用できますが、特定のリソース グループは 1 つのサブスクリプション内にのみ存在できます。
- さらに、1 つのリソース グループが複数のリソースを含むことができますが、1 つのリソースは単一のリソースグループにのみ属すことができます。

Contoso の管理者は、次の表に示すように Azure リソース グループを設定します。

<!-- markdownlint-disable MD033 -->

**リソース グループ** | **詳細**
--- | ---
**ContosoCobRG** | このグループには、ビジネスの継続性 (COB) に関連するすべてのリソースが含まれています。 これには、Contoso が Azure Site Recovery サービスおよび Azure Backup サービス用に使用するコンテナーが含まれます。<br/><br/> また、Azure Migrate や Azure Database Migration Service などの、移行に使用されるリソースも含まれます。
**ContosoDevRG** | このグループには、開発とテストのリソースが含まれます。
**ContosoFailoverRG** | このグループには、フェールオーバーされたリソースが格納されます。
**ContosoNetworkingRG** | このグループには、すべてのネットワーク リソースが含まれます。
**ContosoRG** | このグループには、運用アプリおよびデータベースに関連するリソースが含まれます。

<!-- markdownlint-disable MD026 -->

リソース グループは次のようにして作成します。

1. Azure portal の **[リソース グループ]** で、グループを追加します。
2. グループごとに、名前、グループが属するサブスクリプション、およびリージョンを指定します。
3. リソース グループが **[リソース グループ]** の一覧に表示されます。

    ![リソース グループ](./media/contoso-migration-infrastructure/resource-groups.png)

##### <a name="scale-resource-groups"></a>リソース グループをスケーリングする

今後、Contoso では、ニーズに基づいて他のリソース グループを追加することになります。 たとえば、アプリまたはサービスごとのリソース グループを定義して、それらを独立に管理したり、セキュリティ保護したりできるようにします。

#### <a name="create-matching-security-groups-on-premises"></a>一致するセキュリティ グループをオンプレミスに作成する

1. Contoso の管理者は、オンプレミスの Active Directory で、Azure リソース グループの名前と一致する名前を持つセキュリティ グループを設定します。

    ![オンプレミスの Active Directory セキュリティ グループ](./media/contoso-migration-infrastructure/on-prem-ad.png)

2. 管理のため、Contoso は他のすべてのグループに追加されるグループを作成します。 このグループには、Azure 内のすべてのリソース グループに対する権限があります。 限られた数の全体管理者がこのグループに追加されます。

### <a name="synchronize-active-directory"></a>Active Directory を同期する

Contoso は、オンプレミスとクラウドのリソースへのアクセスに共通の ID を提供することを望んでいます。 そのためには、Contoso はオンプレミスの Active Directory と Azure AD を統合します。 このモデルでは次のようになります。

- ユーザーと組織は、単一の ID を利用して、オンプレミスのアプリケーションと、Office 365 などのクラウド サービスまたは何千ものインターネット上の他のサイトにアクセスできます。
- 管理者は、Active Directory 内のグループを使用して、Azure で[ロールベースのアクセス制御 (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) を実装できます。

統合を容易にするため、Contoso は [Azure AD Connect ツール](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect)を使います。 ドメイン コントローラーにツールをインストールして構成すると、そのツールは、ローカルなオンプレミスの Active Directory ID を Azure AD に同期します。

### <a name="download-the-tool"></a>ツールをダウンロードする

1. Contoso の管理者は、Azure Portal で **[Azure Active Directory]**  >  **[Azure AD Connect]** の順に移動し、同期に使っているサーバーに最新バージョンのツールをダウンロードします。

    ![Azure AD Connect のダウンロード](./media/contoso-migration-infrastructure/download-ad-connect.png)

2. **[簡単設定を使う]** で **AzureADConnect.msi** のインストール開始します。 これは最も一般的なインストールで、認証用にパスワード ハッシュ同期を使う単一フォレスト トポロジで使用できます。

    ![Azure AD Connect ウィザード](./media/contoso-migration-infrastructure/ad-connect-wiz1.png)

3. **[Azure AD に接続]** で、Azure AD に接続するための資格情報を admin@contoso.com または admin@contoso.onmicrosoft.com の形式で指定します。

    ![Azure AD Connect ウィザード](./media/contoso-migration-infrastructure/ad-connect-wiz2.png)

4. **[AD DS に接続]** で、オンプレミスの Active Directory の資格情報を CONTOSO\admin or contoso.com\admin の形式で指定します。

     ![Azure AD Connect ウィザード](./media/contoso-migration-infrastructure/ad-connect-wiz3.png)

5. **[構成の準備完了]** で、 **[構成が完了したら、同期プロセスを開始してください]** を選択して直ちに同期を開始します。 その後インストールします。

以下の点に注意してください。

- Contoso は Azure に直接接続します。 オンプレミスの Active Directory がプロキシの背後に存在する場合は、この[記事](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-connectivity)を読んでください。

- 最初の同期の後、オンプレミスの Active Directory オブジェクトは Azure AD ディレクトリ内に表示されます。

    ![Azure 内のオンプレミスの Active Directory](./media/contoso-migration-infrastructure/on-prem-ad-groups.png)

- Contoso の IT チームは、ロールに基づいて各グループで表わされます。

    ![Azure 内のオンプレミスの Active Directory のメンバー](./media/contoso-migration-infrastructure/on-prem-ad-group-members.png)

### <a name="set-up-rbac"></a>RBAC を設定する

Azure の[ロールベースのアクセス制御 (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) では、Azure のきめ細かなアクセス管理が可能になります。 RBAC を使用すると、タスクの実行に必要な範囲のアクセス権だけをユーザーに付与することができます。 適切な RBAC ロールをユーザー、グループ、およびアプリケーションにスコープ レベルで割り当てます。 ロール割り当てのスコープには、サブスクリプション、リソース グループ、または単独のリソースを指定できます。

Contoso 管理者はここで、オンプレミスから同期した Active Directory グループにロールを割り当てます。

1. **ControlCobRG** リソース グループで、 **[アクセス制御 (IAM)]**  >  **[ロールの割り当ての追加]** を選択します。
2. **[ロールの割り当ての追加]**  >  **[ロール]** で **[共同作成者]** を選択し、一覧から **ContosoCobRG** グループを選択します。 グループが **[選択したメンバー]** の一覧に表示されます。
3. これを、他のリソース グループ (**ContosoAzureAdmins** を除く) について同じアクセス許可で繰り返します。つまり、そのリソース グループに一致するアカウントに共同作成者のアクセス許可を追加します。
4. **ContosoAzureAdmins** グループについては、**所有者**ロールを割り当てます。

    ![Azure 内のオンプレミスの Active Directory のメンバー](./media/contoso-migration-infrastructure/on-prem-ad-groups.png)

## <a name="step-3-design-for-resiliency"></a>手順 3:回復性の設計

### <a name="set-up-regions"></a>リージョンを設定する

Azure リソースはリージョン内に展開されます。

- リージョンは地理的に編成されており、データの保存場所、主権、コンプライアンス、回復性に関する要件は地理的な境界内で遵守されます。
- リージョンは一連のデータセンターで構成されます。 これらのデータセンターは、待機時間が定義された境界内に展開され、待機時間の短い専用のリージョン ネットワークを介して接続されます。
- 回復性のため、各 Azure リージョンは異なるリージョンとペアにされます。
- [Azure リージョン](https://azure.microsoft.com/global-infrastructure/regions)について参照し、[リージョンが組み合わされる方法](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)を理解してください。

Contoso はプライマリ リージョンとして米国東部 2 (バージニア州) を選択し、セカンダリ リージョンとして米国中部 (アイオワ州) を使うことにしました。 これには、いくつかの理由があります。

- Contoso のデータセンターはニューヨークにあり、Contoso では最も近いデータセンターに対する待機時間を考慮しました。
- 米国東部 2 リージョンには、Contoso が使う必要のあるすべてのサービスと製品を備えています。 すべての Azure リージョンで同じ製品とサービスを利用できるわけではありません。 「[リージョン別の Azure 製品](https://azure.microsoft.com/global-infrastructure/services)」をご覧ください。
- 米国中部は、米国東部 2 に対してペアにされる Azure リージョンです。

ハイブリッド環境を考慮して、Contoso はリージョンの設計に回復性とディザスター リカバリーを組み込む方法を検討する必要があります。 回復性を障害ドメインやリージョンのペアリングなどの Azure プラットフォームの機能に依存する単一リージョンの展開から、クラウド サービスとデータベースが 2 つのリージョンに展開されてユーザーに提供される完全なアクティブ/アクティブ モデルまで、広範な戦略がサポートされています。

Contoso は中間の方法を採用することにしました。 Contoso は、アプリとリソースをプライマリ リージョンに展開する一方、完全なインフラストラクチャをセカンダリ リージョンに保持し、アプリの完全な障害やリージョンの障害が発生した場合の完全なバックアップとして機能するようにします。

### <a name="set-up-availability"></a>可用性を設定する

**可用性セット:**

可用性セットは、データセンター内のローカル ハードウェアやネットワークの障害からアプリとデータを保護するのに役立ちます。

- 可用性セットは、Azure VM をデータセンター内のさまざまな物理ハードウェアにわたって分散させます。
- 障害ドメインは、データセンター内の一般的な電源とネットワーク スイッチを持つ基になるハードウェアを表します。 可用性セット内の VM は、1 つのハードウェアまたはネットワーク障害によって発生する停止を最小限に抑えるために、さまざまな障害ドメインにわたって分散されます。
- 更新ドメインは、同時にメンテナンスを実行したり、再起動したりできる基になるハードウェアを表します。 可用性セットはまた、常に少なくとも 1 つのインスタンスが実行されている状態を確保するために、複数の更新ドメインにわたって VM を分散させます。

Contoso は、VM ワークロードに高可用性が必要な場合は常に可用性セットを実装します。 [詳細については、こちらを参照してください](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability)。

**可用性ゾーン:**

可用性ゾーンは、リージョン内のデータセンター全体に影響する障害からアプリとデータを保護するのに役立ちます。

- 各可用性ゾーンは、Azure リージョン内の一意の物理的な場所を表します。
- それぞれのゾーンは、独立した電源、冷却手段、ネットワークを備えた 1 つまたは複数のデータセンターで構成されています。
- 有効になっているリージョンにはいずれも、最低 3 つのゾーンが別個に存在しています。
- ゾーンは 1 つのリージョン内で物理的に分離されているため、データセンターで障害が発生した場合でも、アプリケーションとデータを保護できます。

Contoso は、アプリにスケーラビリティ、高可用性、および回復性が求められる場合に、可用性ゾーンを展開します。 [詳細については、こちらを参照してください](https://docs.microsoft.com/azure/availability-zones/az-overview)。

### <a name="set-up-backup"></a>バックアップを設定する

**Azure Backup:**

Azure Backup を使用すると、Azure VM ディスクをバックアップおよび復元できます。

- Azure Backup では、Azure ストレージに格納されている VM ディスク イメージの自動バックアップが可能です。
- バックアップはアプリケーション整合性を備えているため、バックアップされたデータにトランザクション整合性があり、かつ復元後にアプリケーションが起動されることが保証されます。
- Azure Backup はローカル冗長ストレージ (LRS) をサポートしており、ローカル ハードウェアの障害が発生した場合は、データセンター内のバックアップ データの複数のコピーをレプリケートします。
- リージョン障害が発生した場合、Azure Backup は geo 冗長ストレージ (GRS) もサポートしているため、バックアップ データをセカンダリ ペア リージョンにレプリケートします。
- Azure Backup は、転送中のデータを AES 256 を使用して暗号化します。 バックアップされた保存データは、[Storage Service Encryption (SSE)](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) を使用して暗号化されます。

Contoso はすべての運用 VM に Azure Backup を GRS と共に使用することにより、ワークロード データがバックアップされ、障害またはその他の中断が発生した場合はすばやく復元されるようにします。 [詳細については、こちらを参照してください](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup)。

### <a name="set-up-disaster-recovery"></a>ディザスター リカバリーを設定する

**Azure Site Recovery:**

Azure Site Recovery は、リージョン障害中でもビジネス アプリやワークロードの実行状態を維持することにより、ビジネス継続性を確保するのに役立ちます。

- Azure Site Recovery は Azure VM をプライマリ リージョンからセカンダリ リージョンに継続的にレプリケートして、機能的なコピーを両方の場所に確保します。
- プライマリ リージョンに障害が発生した場合、アプリケーションまたはサービスはセカンダリ リージョンでレプリケートされた VM インスタンスの使用にフェールオーバーするため、潜在的な中断が最小限に抑えられます。
- 通常の運用に戻ったら、アプリケーションまたはサービスはプライマリ リージョン内の VM にフェールバックできます。

Contoso は、ミッション クリティカルなワークロードで使用されるすべての運用 VM に Azure Site Recovery を実装することにより、プライマリ リージョン内の障害時の中断が最小限に抑えられるようにします。 [詳細情報](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview)

## <a name="step-4-design-a-network-infrastructure"></a>手順 4:ネットワーク インフラストラクチャを設計する

リージョンの設計が決まれば、Contoso は次にネットワーク戦略を検討できます。 オンプレミスのデータセンターと Azure が相互に接続して通信する方法、および Azure のネットワーク インフラストラクチャを設計する方法を検討する必要があります。 特に、Contoso では次のことが必要です。

- **ハイブリッド ネットワーク接続を計画する。** オンプレミスと Azure の間でネットワークを接続する方法を明らかにします。
- **Azure ネットワーク インフラストラクチャを設計する。** リージョンにネットワークを展開する方法を決定します。 また、同じリージョン内および異なるリージョン間でのネットワークの通信方法も決定します。
- **Azure ネットワークを設計および設定する。** Azure ネットワークとサブネットを設定し、それらに置くものを決定します。

### <a name="plan-hybrid-network-connectivity"></a>ハイブリッド ネットワーク接続を計画する

Contoso は、Azure とオンプレミスのデータセンターの間のハイブリッド ネットワークに関して、[複数のアーキテクチャ](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking)を検討しました。 詳しくは、「[オンプレミス ネットワークを Azure に接続するためのソリューションを選択する](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/considerations)」をご覧ください。

なお、Contoso のオンプレミス ネットワークのインフラストラクチャは、現在、ニューヨークのデータセンターと、米国東部の複数のローカル支局で構成されています。 すべての場所には、インターネットへのビジネス クラスの接続があります。 各ブランチは、インターネット経由の IPSec VPN トンネルを介して、データセンターに接続されています。

![Contoso のネットワーク](./media/contoso-migration-infrastructure/contoso-networking.png)

Contoso が決定したハイブリッド接続の実装方法は次のとおりです。

1. ニューヨークにある Contoso のデータセンターと、米国東部 2 および米国中部の 2 つの Azure リージョンの間に、新しいサイト間 VPN 接続を設定します。
2. Azure 仮想ネットワーク宛のブランチ オフィスのトラフィックは、Contoso のメイン データセンターを介してルーティングされます。
3. Azure の展開をスケールアップするときは、データセンターと Azure リージョンの間に ExpressRoute 接続を確立します。 その際、Contoso は、VPN のサイト間接続をフェールオーバーのためだけに保持します。
    - VPN と ExpressRoute ハイブリッド ソリューションの選択に関して[詳しくはこちらをご覧ください](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/considerations)。
    - [ExpressRoute の場所とサポート](https://docs.microsoft.com/azure/expressroute/expressroute-locations-providers)を確認してください。

**VPN のみ:**

![Contoso VPN](./media/contoso-migration-infrastructure/hybrid-vpn.png)

**VPN および ExpressRoute:**

![Contoso VPN/ExpressRoute](./media/contoso-migration-infrastructure/hybrid-vpn-expressroute.png)

### <a name="design-the-azure-network-infrastructure"></a>Azure のネットワーク インフラストラクチャを設計する

ハイブリッド展開が安全性と拡張性を備えるような方法でネットワークを配置することが重要です。 そのため、Contoso は長期的なアプローチを採用し、仮想ネットワーク (Vnet) が回復性を持ちエンタープライズに対応するように設計しています。 Vnet の計画に関して[詳しくはこちらをご覧ください](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm)。

2 つのリージョンを接続するため、Contoso はハブ間ネットワーク モデルを実装することにしました。

- 各リージョン内で、Contoso はハブ アンド スポーク モデルを使用します。
- ネットワークとハブを接続するために、Contoso は Azure ネットワーク ピアリングを使用します。

#### <a name="network-peering"></a>ネットワーク ピアリング

Azure では、VNet とハブを接続するためにネットワーク ピアリングが提供されています。 グローバル ピアリングを使うと、異なるリージョンの VNet とハブの間を接続できます。 ローカル ピアリングは同じリージョン内の VNet を接続します。 VNet ピアリングには、次のようないくつかの利点があります。

- ピアリングされた VNet 間のネットワーク トラフィックはプライベートになります。
- VNet 間のトラフィックは、Microsoft のバックボーン ネットワーク上で保持されます。 VNet 間の通信では、パブリック インターネット、ゲートウェイ、暗号化が必要ありません。
- ピアリングでは、異なる VNet 内のリソースの間に、待機時間が短く、広帯域幅の接続が既定で提供されます。

ネットワーク ピアリングに関して[詳しくはこちらをご覧ください](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)。

#### <a name="hub-to-hub-across-regions"></a>異なるリージョンのハブとハブ

Contoso は各リージョンにハブを展開します。 ハブは、オンプレミス ネットワークへの主要な接続ポイントとして機能する Azure の仮想ネットワーク (VNet) です。 ハブの VNet は、グローバル VNet ピアリングを使用して相互接続します。 グローバル VNet ピアリングは、異なる Azure リージョンにある VNet の間を接続します。

- 各リージョンのハブは、他のリージョンにあるパートナー ハブにピアリングされます。
- ハブは、そのリージョン内のすべてのネットワークにピアリングされ、すべてのネットワーク リソースに接続できます。

    ![グローバル ピアリング](./media/contoso-migration-infrastructure/global-peering.png)

#### <a name="hub-and-spoke-model-within-a-region"></a>リージョン内のハブ アンド スポーク モデル

Contoso は、各リージョン内にリージョン ハブからのスポーク ネットワークとして、さまざまな目的の VNet を展開します。 リージョン内の VNet はピアリングを使って、ハブに接続すると共に、相互に接続します。

#### <a name="design-the-hub-network"></a>ハブ ネットワークを設計する

Contoso は、選択したハブ アンド スポーク モデルの内部において、オンプレミスのデータセンターとインターネットからのトラフィックをルーティングする方法について考える必要があります。 Contoso が米国東部 2 ハブと米国中部ハブのルーティングの処理方法をどのように決定したかを次に示します。

- Contoso は、"reverse c" という名前のネットワークを設計しています。これはパケットが受信ネットワークから送信ネットワークに辿るパスであるため、このような名前になっています。
- そのネットワーク アーキテクチャには、信頼されていないフロントエンド境界ゾーンと信頼されているバックエンド ゾーンの 2 つの境界があります。
- ファイアウォールは、ゾーンごとにネットワーク アダプターを備え、信頼されているゾーンへのアクセスを制御します。
- インターネットから:
  - インターネット トラフィックは、境界ネットワーク上の負荷分散されたパブリック IP アドレスにヒットします。
  - このトラフィックは、ファイアウォール経由でルーティングされ、ファイアウォール規則に従います。
  - ネットワーク アクセス制御が実装されると、トラフィックは信頼されているゾーン内の適切な場所に転送されます。
  - VNet からの送信トラフィックは、ユーザー定義ルートを使用してインターネットにルーティングされます。 トラフィックは、強制的にファイアウォールを通過させられ、Contoso のポリシーで検査されます。
- Contoso データセンターから:
  - VPN サイト間接続 (または ExpressRoute) 経由の受信トラフィックは、Azure VPN ゲートウェイのパブリック IP アドレスにヒットします。
  - トラフィックは、ファイアウォール経由でルーティングされ、ファイアウォール規則に従います。
  - ファイアウォール ルールを適用すると、トラフィックは信頼されている内部ゾーン サブネット上の内部ロード バランサー (標準SKU) に転送されます。
  - 信頼されているサブネットからオンプレミスのデータセンターへの VPN 経由の送信トラフィックは、ファイアウォールを通してルーティングされ、VPN サイト間接続を経由する前に規則を適用されます。

### <a name="design-and-set-up-azure-networks"></a>Azure ネットワークを設計および設定する

ネットワークとルーティング トポロジが決まると、Contoso は Azure ネットワークとサブネットを設定できるようになります。

- Contoso は、クラス A のプライベート ネットワークを Azure に実装します (0.0.0.0 から 127.255.255.255)。 現在はオンプレミスでクラス B のプライベート アドレス空間 172.160.0/16 を使用していて、Contoso ではアドレス範囲の間にオーバーラップがないため、これが動作します。
- Contoso は、プライマリ リージョンとセカンダリ リージョンに VNet を展開します。
- Contoso はプレフィックス **VNET** とリージョンの省略形 **EUS2** または **CUS** を含む名前付け規則を使用します。 この標準を使用すると、ハブ ネットワークの名前は **VNET-HUB-EUS2** (米国東部 2) および **VNET-HUB-CUS** (米国中部) になります。
- Contoso は [IPAM ソリューション](https://docs.microsoft.com/windows-server/networking/technologies/ipam/ipam-top)を使用していないので、NAT なしでネットワーク ルーティングを計画する必要があります。

#### <a name="virtual-networks-in-east-us-2"></a>米国東部 2 での仮想ネットワーク

米国東部 2 は、Contoso がリソースとサービスの展開に使用するプライマリ リージョンです。 そこでの Contoso によるネットワークの設計方法を次に示します。

- **ハブ:** 米国東部 2 のハブ VNet は、オンプレミスのデータセンターに対する主要な接続の中心点です。
- **VNet:** 米国東部 2 のスポーク VNet は、必要に応じてワークロードの分離に使用できます。 ハブ VNet に加えて、Contoso は 2 つのスポーク VNet を米国東部 2 で使用します。
  - **VNET-DEV-EUS2**: この VNet は、開発およびテスト チームに、開発プロジェクト用の完全に機能するネットワークを提供します。 運用パイロット領域として動作し、運用インフラストラクチャを利用して機能します。
    - **VNET-PROD-EUS2**。 Azure IaaS 運用コンポーネントは、このネットワークに配置されます。
  - 各 VNet は専用の一意アドレス空間を持ち、オーバーラップはありません。 Contoso は NAT が必要ないようにルーティングを構成する予定です。
- **サブネット:**
  - 各アプリ階層の各ネットワークにサブネットがあります
  - 運用ネットワークの各サブネットに対応するサブネットが開発用 VNet 内にあります。
  - さらに、運用ネットワークには、ドメイン コントローラー用のサブネットがあります。

米国東部 2 の VNet は、次の表に要約されます。

**VNet** | **Range** | **ピア**
--- | --- | ---
**VNET-HUB-EUS2** | 10.240.0.0/20 | VNET-HUB-CUS2、VNET-DEV-EUS2、VNET-PROD-EUS2
**VNET-DEV-EUS2** | 10.245.16.0/20 | VNET-HUB-EUS2
**VNET-PROD-EUS2** | 10.245.32.0/20 | VNET-HUB-EUS2、VNET-PROD-CUS

![プライマリ リージョン内のハブ アンド スポーク モデル](./media/contoso-migration-infrastructure/primary-hub-peer.png)

#### <a name="subnets-in-the-east-us-2-hub-network-vnet-hub-eus2"></a>東部米国 2 のハブ ネットワーク (VNET-HUB-EUS2) 内のサブネット

**サブネット/ゾーン** | **CIDR** | **使用可能な IP アドレス
--- | --- | ---
**IB-UntrustZone** | 10.240.0.0/24 | 251
**IB-TrustZone** | 10.240.1.0/24 | 251
**OB-UntrustZone** | 10.240.2.0/24 | 251
**OB-TrustZone** | 10.240.3.0/24 | 251
**GatewaySubnets** | 10.240.10.0/24 | 251

#### <a name="subnets-in-the-east-us-2-dev-network-vnet-dev-eus2"></a>東部米国 2 の開発用ネットワーク (VNET-DEV-EUS2) 内のサブネット

開発用 VNet は、運用パイロット領域として開発チームによって使われます。 3 つのサブネットがあります。

**サブネット** | **CIDR** | **アドレス** | **サブネット内**
--- | --- | --- | ---
**DEV-FE-EUS2** | 10.245.16.0/22 | 1019 | フロントエンド/Web 層の VM
**DEV-APP-EUS2** | 10.245.20.0/22 | 1019 | アプリ階層の VM
**DEV-DB-EUS2** | 10.245.24.0/23 | 507 | データベース VM

#### <a name="subnets-in-the-east-us-2-production-network-vnet-prod-eus2"></a>東部米国 2 の運用ネットワーク (VNET-PROD-EUS2) 内のサブネット

Azure IaaS コンポーネントは、運用ネットワーク内に配置されます。 各アプリ階層には専用のサブネットがあります。 サブネットは開発用ネットワーク内のサブネットと一致し、ドメイン コントローラー用のサブネットが追加されています。

**サブネット** | **CIDR** | **アドレス** | **サブネット内**
--- | --- | --- | ---
**PROD-FE-EUS2** | 10.245.32.0/22 | 1019 | フロントエンド/Web 層の VM
**PROD-APP-EUS2** | 10.245.36.0/22 | 1019 | アプリ階層の VM
**PROD-DB-EUS2** | 10.245.40.0/23 | 507 | データベース VM
**PROD-DC-EUS2** | 10.245.42.0/24 | 251 | ドメイン コントローラー VM

![ハブ ネットワークのアーキテクチャ](./media/contoso-migration-infrastructure/azure-networks-eus2.png)

#### <a name="virtual-networks-in-central-us-secondary-region"></a>米国中部 (セカンダリ リージョン) 内の仮想ネットワーク

米国中部は Contoso のセカンダリ リージョンです。 そこでの Contoso によるネットワークの設計方法を次に示します。

- **ハブ:** 米国東部 2 のハブ VNet はオンプレミスのデータセンターに対する接続の中心点であり、米国東部 2 のスポーク VNet は必要に応じてワークロードの分離に使用でき、他のスポークとは別に管理されます。
- **VNet:** Contoso は米国中部に 2 つの VNet を保持します。
  - VNET-PROD-CUS: この VNet は、VNET-PROD_EUS2 に似た運用ネットワークです。
  - VNET-ASR-CUS: この VNet は、オンプレミスからのフェールオーバー後に VM が作成される場所、またはプライマリ リージョンからセカンダリ リージョンにフェールオーバーされる Azure VM のための場所として使用されます。 このネットワークは運用ネットワークに似ていますが、ドメイン コントローラーは存在しません。
  - リージョン内の各 VNet は専用のアドレス空間を持ち、オーバーラップはありません。 Contoso では NAT なしでルーティングを構成する予定です。
- **サブネット:** サブネットは、米国東部 2 のサブネットと同様の方法で設計されます。 例外は、Contoso ではドメイン コントローラー用のサブネットが必要ないことです。

米国中部の VNet をまとめると次の表のようになります。

**VNet** | **Range** | **ピア**
--- | --- | ---
**VNET-HUB-CUS** | 10.250.0.0/20 | VNET-HUB-EUS2、VNET-ASR-CUS、VNET-PROD-CUS
**VNET-ASR-CUS** | 10.255.16.0/20 | VNET-HUB-CUS、VNET-PROD-CUS
**VNET-PROD-CUS** | 10.255.32.0/20 | VNET-HUB-CUS、VNET-ASR-CUS、VNET-PROD-EUS2

![ペア リージョン内のハブ アンド スポーク モデル](./media/contoso-migration-infrastructure/paired-hub-peer.png)

#### <a name="subnets-in-the-central-us-hub-network-vnet-hub-cus"></a>米国中部のハブ ネットワーク (VNET-HUB-CUS) 内のサブネット

**サブネット** | **CIDR** | **使用可能な IP アドレス**
--- | --- | ---
**IB-UntrustZone** | 10.250.0.0/24 | 251
**IB-TrustZone** | 10.250.1.0/24 | 251
**OB-UntrustZone** | 10.250.2.0/24 | 251
**OB-TrustZone** | 10.250.3.0/24 | 251
**GatewaySubnet** | 10.250.2.0/24 | 251

#### <a name="subnets-in-the-central-us-production-network-vnet-prod-cus"></a>米国中部の運用ネットワーク (VNET-PROD-CUS) 内のサブネット

米国東部 2 のプライマリ リージョン内の運用ネットワークと並列に、米国中部のセカンダリ リージョン内に運用ネットワークが存在します。

**サブネット** | **CIDR** | **アドレス** | **サブネット内**
--- | --- | --- | ---
**PROD-FE-CUS** | 10.255.32.0/22 | 1019 | フロントエンド/Web 層の VM
**PROD-APP-CUS** | 10.255.36.0/22 | 1019 | アプリ階層の VM
**PROD-DB-CUS** | 10.255.40.0/23 | 507 | データベース VM
**PROD-DC-CUS** | 10.255.42.0/24 | 251 | ドメイン コントローラー VM

#### <a name="subnets-in-the-central-us-failoverrecovery-network-in-central-us-vnet-asr-cus"></a>米国中部のフェールオーバー/復旧ネットワーク (VNET-ASR-CUS) 内のサブネット

VNET-ASR-CUS ネットワークは、リージョン間のフェールオーバーのために使われます。 リージョン間の Azure VM のレプリケートとフェールオーバーには、Site Recovery が使われます。 また、オンプレミスに残っているがディザスター リカバリーのために Azure にフェールオーバーする保護されたワークロードのための Azure ネットワークに対する Contoso データセンターとしても機能します。

VNET-ASR-CUS は米国東部 2 の運用 VNet と同じ基本サブネットですが、ドメイン コントローラー サブネットの必要はありません。

**サブネット** | **CIDR** | **アドレス** | **サブネット内**
--- | --- | --- | ---
**ASR-FE-CUS** | 10.255.16.0/22 | 1019 | フロントエンド/Web 層の VM
**ASR-APP-CUS** | 10.255.20.0/22 | 1019 | アプリ階層の VM
**ASR-DB-CUS** | 10.255.24.0/23 | 507 | データベース VM

![ハブ ネットワークのアーキテクチャ](./media/contoso-migration-infrastructure/azure-networks-cus.png)

#### <a name="configure-peered-connections"></a>ピアリングされた接続を構成する

各リージョンのハブは、他のリージョンのハブと、そのハブ リージョン内のすべての VNet にピアリングされます。 これにより、ハブは通信することができ、リージョン内のすべての VNet を認識できます。 以下の点に注意してください。

- ピアリングは双方向の接続を作成します。 1 つ目は 1 番目の VNet のピアから開始し、2 つ目は 2 番目の VNet のピアから開始します。
- ハイブリッド デプロイでは、ピア間を通過するトラフィックは、オンプレミスのデータセンターと Azure の間の VPN 接続から表示できる必要があります。 そのためには、ピアリングされた接続で特定の設定を行う必要があります。

スポーク VNet からハブを経由してオンプレミスのデータセンターへのすべての接続について、Contoso はトラフィックの転送と VPN ゲートウェイの横断を許可する必要があります。

##### <a name="domain-controller"></a>ドメイン コントローラー

VNET-PROD-EUS2 ネットワーク内のドメイン コントローラーでは、EUS2 ハブ/運用ネットワークとオンプレミスへの VPN 接続の両方にトラフィックを流す必要があります。 これを行うために、Contoso の管理者は次のような許可を設定する必要があります。

1. ピアリングされた接続で、 **[転送されたトラフィックを許可する]** と **[ゲートウェイ転送を許可する]** を設定します。 この例では、これは VNET-HUB-EUS2 から VNET-PROD-EUS2 への接続です。

    ![ピアリング](./media/contoso-migration-infrastructure/peering1.png)

2. ピアリングの他の側 (VNET-PROD-EUS2 から VNET-HUB-EUS2 への接続) では、 **[転送されたトラフィックを許可する]** と **[リモート ゲートウェイを使用する]** を設定します。

    ![ピアリング](./media/contoso-migration-infrastructure/peering2.png)

3. オンプレミスでは、ローカル トラフィックを VNet への VPN トンネルにルーティングする静的ルートを設定します。 Contoso から Azure への VPN トンネルを提供するゲートウェイで構成を行います。 このために RRAS を使用します。

    ![ピアリング](./media/contoso-migration-infrastructure/peering3.png)

##### <a name="production-networks"></a>運用ネットワーク

スポークされたピア ネットワークは、ハブを介して別のリージョンのスポークされたピア ネットワークを認識することはできません。

両方のリージョンの Contoso の運用ネットワークが相互を認識するには、Contoso の管理者は VNET-PROD-EUS2 と VENT-PROD-CUS にピアリングされた直接接続を作成する必要があります。

![ピアリング](./media/contoso-migration-infrastructure/peering4.png)

### <a name="set-up-dns"></a>DNS を設定する

仮想ネットワークにリソースを展開するときは、ドメイン名の解決に 2 つの選択肢があります。 Azure によって提供される名前解決を使うことも、解決用に DNS サーバーを提供することもできます。 どちらの名前解決方法を使用するかは、リソースが互いに通信するために必要な方法によって決まります。 Azure DNS サービスについての[詳細をご覧ください](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#azure-provided-name-resolution)。

Contoso の管理者は、Azure DNS サービスはハイブリッド環境に適した選択ではないと判断しました。 代わりに、オンプレミスの DNS サーバーを使用します。

- これはハイブリッド ネットワークなので、オンプレミスと Azure のすべての VM は、正常に機能するために名前を解決できる必要があります。 つまり、すべての VNet にカスタム DNS の設定を適用する必要があります。
- Contoso は現在、Contoso データセンターとブランチ オフィスに DC を展開しています。 プライマリ DNS サーバーは CONTOSODC1(172.16.0.10) と CONTOSODC2(172.16.0.1) です。
- VNet が展開されると、オンプレミスのドメイン コントローラーは、ネットワーク内の DNS サーバーとして使用されるように設定されます。
- これを構成するには、カスタム DNS を VNet で使用するときに、Azure の再帰的なリゾルバーの IP アドレス (168.63.129.16 など) を DNS リストに追加する必要があります。 そのために、Contoso は各 VNet で DNS サーバーの設定を構成します。 たとえば、VNET-HUB-EUS2 ネットワークのカスタム DNS の設定は次のようになります。

    ![[カスタム DNS]](./media/contoso-migration-infrastructure/custom-dns.png)

オンプレミスのドメイン コントローラーだけでなく、Contoso は Azure ネットワークをサポートするために、各リージョンに 2 つずつ、4 つのドメイン コントローラーをさらに実装する予定です。 Contoso の Azure での展開は次のようになります。

**[リージョン]** | **DC** | **VNet** | **サブネット** | **IP アドレス (IP address)**
--- | --- | --- | --- | ---
EUS2 | CONTOSODC3 | VNET-PROD-EUS2 | PROD-DC-EUS2 | 10.245.42.4
EUS2 | CONTOSODC4 | VNET-PROD-EUS2 | PROD-DC-EUS2 | 10.245.42.5
CUS | CONTOSODC5 | VNET-PROD-CUS | PROD-DC-CUS | 10.255.42.4
CUS | CONTOSODC6 | VNET-PROD-CUS | PROD-DC-CUS | 10.255.42.4

オンプレミスのドメイン コントローラーを展開した後、Contoso は、DNS サーバーの一覧に新しいドメイン コントローラーが含まれるように、両方のリージョンのネットワークで DNS の設定を更新する必要があります。

#### <a name="set-up-domain-controllers-in-azure"></a>Azure でドメイン コントローラーを設定する

ネットワークの設定を更新した後、Contoso の管理者は Azure でドメイン コントローラーを構築できます。

1. Azure portal で、新しい Windows Server VM を適切な VNet に展開します。
2. VM 用の可用性セットを各場所に作成します。 可用性セットは次のことを行います。

    - Azure ファブリックが Azure リージョン内の異なるインフラストラクチャに VM を分離することを保証します。
    - Contoso が Azure の VM で 99.95% SLA の対象になるようにします。 [詳細については、こちらを参照してください](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets)。

    ![可用性グループ](./media/contoso-migration-infrastructure/availability-group.png)

3. VM を展開した後、Contoso は VM に対してネットワーク インターフェイスを開きます。 ここでは、プライベート IP アドレスを静的に設定し、有効なアドレスを指定します。

    ![VM NIC](./media/contoso-migration-infrastructure/vm-nic.png)

4. 次に、新しいデータ ディスクを VM に接続します。 このディスクには、Active Directory データベースと sysvol 共有が含まれています。
    - ディスクのサイズにより、サポートされる IOPS の値が決まります。
    - 時間が経過して環境が拡大すると、ディスク サイズを増やすことが必要になる場合があります。
    - ホスト キャッシュのためにドライブを読み取り/書き込みに設定しないでください。 Active Directory データベースはこれをサポートしません。

     ![Active Directory ディスク](./media/contoso-migration-infrastructure/ad-disk.png)

5. ディスクを追加した後、リモート デスクトップ経由で VM に接続し、サーバー マネージャーを開きます。

6. その後 **[ファイル サービスと記憶域サービス]** で新しいボリューム ウィザードを実行し、ローカル VM でドライブ文字が F: 以降になっていることを確認します。

     ![新しいボリューム ウィザード](./media/contoso-migration-infrastructure/volume-wizard.png)

7. Contoso はサーバー マネージャーで **Active Directory Domain Services** ロールを追加します。 次に、ドメイン コントローラーとして VM を構成します。

      ![サーバー ロール](./media/contoso-migration-infrastructure/server-role.png)

8. VM を DC として構成して再起動した後、DNS マネージャーを開き、Azure DNS リゾルバーをフォワーダーとして構成します。 これにより、DC は Azure DNS で解決できない DNS クエリを転送できます。

    ![DNS フォワーダー](./media/contoso-migration-infrastructure/dns-forwarder.png)

9. 次に、VNet リージョンの適切なドメイン コントローラーで、各 VNet のカスタム DNS 設定を更新します。 オンプレミスの DC を一覧に含めます。

### <a name="set-up-active-directory"></a>Active Directory のセットアップ

Active Directory はネットワークで重要なサービスであり、正しく構成する必要があります。 Contoso 管理者は、Contoso データセンターと EUS2 および CUS リージョン用に Active Directory サイトを構築します。

1. 2 つの新しいサイト (AZURE-EUS2、AZURE-CUS) およびデータセンター サイト (ContosoDatacenter) を作成します。
2. サイトを作成した後は、VNet およびデータセンターと一致するように、サイト内にサブネットを作成します。

    ![DC サブネット](./media/contoso-migration-infrastructure/dc-subnets.png)

3. 次に、2 つのサイト リンクを作成してすべてを接続します。 ドメイン コントローラーをそれぞれの場所に移動する必要があります。

    ![DC リンク](./media/contoso-migration-infrastructure/dc-links.png)

4. すべての構成が済むと、Active Directory レプリケーション トポロジが完成します。

    ![DC レプリケーション](./media/contoso-migration-infrastructure/ad-resolution.png)

5. すべてが完了すると、ドメイン コントローラーとサイトの一覧が、オンプレミスの Active Directory 管理センターに表示されます。

    ![[Active Directory 管理センター]](./media/contoso-migration-infrastructure/ad-center.png)

## <a name="step-5-plan-for-governance"></a>手順 5:ガバナンスを計画する

Azure は、サービスと Azure プラットフォームに対してさまざまなガバナンス コントロールを提供します。 詳しくは、[Azure のガバナンスのオプション](https://docs.microsoft.com/azure/security/governance-in-azure)に関するページをご覧ください。

Contoso は ID とアクセスの制御を構成しているので、ガバナンスとセキュリティの一部の面は既に導入しています。 大まかに言うと、考慮する必要がある 3 つの領域があります。

- **ポリシー:** Azure Policy は、リソースに対してルールと効果を適用および実施して、それらのリソースが企業の要件および SLA への準拠を維持するようにします。
- **ロック:** Azure では、ユーザーはサブスクリプション、リソース グループ、その他のリソースをロックし、権限を持つユーザーだけがそれらを変更できるようにすることができます。
- **タグ:** タグを使って、リソースを制御、監査、および管理できます。 タグは、リソースにメタデータを関連付けて、リソースまたは所有者に関する情報を提供します。

### <a name="set-up-policies"></a>ポリシーを設定する

Azure Policy サービスは、リソースを評価して、適用されているポリシー定義に準拠していないリソースをスキャンします。 たとえば、特定の種類の VM だけを許可するポリシーや、リソースが特定のタグを持つポリシーが設定されていることがあります。

ポリシーはポリシー定義を指定し、ポリシー割り当てはポリシーが適用される範囲を指定します。 範囲は、管理グループからリソース グループまでです。 ポリシーの作成と管理の[詳細をご覧ください](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)。

Contoso は、いくつかのポリシーを開始する予定です。

- これには、リソースを EUS2 リージョンと CUS リージョンにのみ確実にデプロイできるようにするポリシーが必要です。
- 承認された SKU だけに VM SKU を制限します。 目的は、高価な VM SKU が使われないようにすることです。

#### <a name="limit-resources-to-regions"></a>リージョンにリソースを制限する

Contoso は、組み込みポリシー定義の**許可されている場所**を使って、リソースのリージョンを制限します。

1. Azure Portal で、 **[すべてのサービス]** を選択し、 **[ポリシー]** を検索します。
2. **[割り当て]**  >  **[ポリシーの割り当て]** を選択します。
3. ポリシーの一覧で、 **[許可されている場所]** を選びます。
4. **[スコープ]** を Azure サブスクリプションの名前に設定し、許可リストで 2 つのリージョンを選びます。

    ![ポリシーで許可されたリージョン](./media/contoso-migration-infrastructure/policy-region.png)

5. 既定ではポリシーは **[拒否]** に設定されており、EUS2 または CUS ではないサブスクリプションで展開が開始された場合、展開は失敗します。 Contoso サブスクリプションの誰かが米国西部で展開を設定しようとすると、次のようになります。

    ![ポリシー エラー](./media/contoso-migration-infrastructure/policy-failed.png)

#### <a name="allow-specific-vm-skus"></a>特定の VM SKU を許可する

Contoso は、組み込みポリシー定義の**許可されている仮想マシン SKU** を使って、サブスクリプションにおいて作成できる VM の種類を制限します。

![ポリシー SKU](./media/contoso-migration-infrastructure/policy-sku.png)

#### <a name="check-policy-compliance"></a>ポリシーのコンプライアンスを確認する

ポリシーはすぐに有効になり、Contoso はリソースのコンプライアンス状況を確認できます。

1. Azure Portal で、 **[コンプライアンス]** リンクを選択します。
2. コンプライアンス ダッシュボードが表示されます。 詳細にさらにドリルダウンすることができます。

    ![ポリシーのコンプライアンス](./media/contoso-migration-infrastructure/policy-compliance.png)

### <a name="set-up-locks"></a>ロックを設定する

Contoso では長い間、システムの管理に ITIL フレームワークを使用してきました。 このフレームワークの最も重要な側面の 1 つは変更制御であり、Contoso では、Azure の展開には必ず、変更制御が実装されるようにしたいと考えています。

Contoso は、次のようにロックを実装しようとしています。

- すべての運用コンポーネントまたはフェールオーバー コンポーネントは、ReadOnly ロックを保持するリソース グループに含まれている必要があります。 つまり、運用項目を変更または削除するには、ロックを削除する必要があります。
- 非運用リソース グループは、CanNotDelete ロックを保持します。 つまり、許可されたユーザーはリソースの読み取りまたは変更をできますが、削除することはできません。

ロックに関して[詳しくはこちらをご覧ください](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources)。

### <a name="set-up-tagging"></a>タグ付けの設定

追加されるリソースを追跡するため、Contoso にとっては適切な部門、顧客、環境にリソースを関連付けることの重要性が今後増していきます。

リソースと所有者に関する情報を提供するだけでなく、タグを使うと、リソースを集計してグループ化し、そのデータをチャージバックのために使うことができます。

Contoso は、ビジネスにとって有意義な方法で Azure の資産を視覚化する必要があります。 たとえばロール別や部門別です。 タグを共有するために、リソースが同じリソース グループ内に格納されている必要はないことに注意してください。 Contoso は誰もが同じタグを使用できるように、簡単なタグ分類を作成します。

**タグ名** | **Value**
--- | ---
CostCenter | 12345:SAP の有効なコスト センターである必要があります。
BusinessUnit | (SAP の) 部署の名前です。 CostCenter と一致します。
ApplicationTeam | アプリのサポートを所有しているチームのメール別名です。
CatalogName | リソースがサポートするサービス カタログに従った、アプリまたは ShareService の名前です。
ServiceManager | リソースの ITIL Service Manager のメール別名です。
COBPriority | BCDR に対してビジネスによって設定された優先度です。 値は 1 から 5 です。
ENV | 指定できる値は DEV、STG、PROD です。 開発、ステージング、運用を表します。

次に例を示します。

 ![Azure タグ](./media/contoso-migration-infrastructure/azure-tag.png)

タグを作成した後、Contoso は戻って新しいポリシー定義および割り当てを作成し、必要なタグを組織全体で強制的に使用するようにします。

## <a name="step-6-consider-security"></a>手順 6:セキュリティを検討する

セキュリティはクラウドにおいて非常に重要であり、Azure ではさまざまなセキュリティ関連のツールと機能が提供されています。 これらは、セキュリティで保護された Azure プラットフォーム上にセキュリティで保護されたソリューションを作成するのに役立ちます。 Azure のセキュリティについて詳しくは、「[信頼されるクラウドとしての自信](https://azure.microsoft.com/overview/trusted-cloud)」をご覧ください。

Contoso はいくつかの主要な側面を考慮する必要があります。

- **Azure Security Center:** Azure Security Center は、ハイブリッド クラウド ワークロード全体で統合されたセキュリティ管理と高度な脅威保護を実現します。 Security Center を使用して、ワークロード全体へのセキュリティ ポリシーの適用、脅威にさらされる状態の軽減、攻撃の検出とその対応を行うことができます。 [詳細については、こちらを参照してください](https://docs.microsoft.com/azure/security-center/security-center-intro)。
- **ネットワーク セキュリティ グループ (NSG):** NSG は、一連のセキュリティ規則が含まれているフィルター (ファイアウォール) です。規則が適用されると、Azure VNet に接続されたリソースへのネットワーク トラフィックが許可または拒否されます。 [詳細については、こちらを参照してください](https://docs.microsoft.com/azure/virtual-network/security-overview)。
- **データ暗号化:** Azure Disk Encryption は、Windows と Linux の IaaS 仮想マシン ディスクを暗号化するのに役立つ機能です。 [詳細については、こちらを参照してください](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest)。

### <a name="work-with-the-azure-security-center"></a>Azure Security Center を使用する

Contoso は、新しいハイブリッド クラウドと特に Azure ワークロードのセキュリティ体制をすばやく確認できる方法を探しています。 結果として、Contoso は Azure Security Center を実装して次の機能を利用することにしました。

- 一元化されたポリシー管理
- 継続的な評価
- 実行可能な推奨事項

#### <a name="centralize-policy-management"></a>一元化されたポリシー管理

Contoso では、一元化されたポリシー管理を使用して環境全体のセキュリティ ポリシーを一元的に管理することで、セキュリティ要件に確実に準拠する予定です。 簡単かつ迅速にポリシーを実装して、すべての Azure リソースに適用できます。

![セキュリティ ポリシー](./media/contoso-migration-infrastructure/security-policy.png)

#### <a name="assess-and-action"></a>評価して対処する

Contoso は、コンピューター、ネットワーク、ストレージ、データ、およびアプリケーションのセキュリティを監視する継続的なセキュリティ評価を利用して、潜在的なセキュリティの問題を検出します。

- Security Center は、Contoso のコンピューティング リソース、インフラストラクチャ リソース、データ リソース、および Azure のアプリとサービスのセキュリティ状態を分析します。
- Contoso の運用チームは、継続的な評価を使用して、セキュリティ更新プログラムが不足しているシステムや公開されているネットワーク ポートなど、潜在的なセキュリティの問題を検出できます。
- 特に Contoso では、確実にすべての VM が保護されるようにしたいと考えています。 Security Center はこれを支援し、VM の正常性を検証して、セキュリティの脆弱性が悪用される前にそれを修正するための、優先順位付けされた実行可能な推薦事項を提示します。

![監視](./media/contoso-migration-infrastructure/monitoring.png)

### <a name="work-with-nsgs"></a>NSG を使用する

ネットワーク セキュリティ グループを使って、仮想ネットワーク内のリソースへのネットワーク トラフィックを制限することができます。

- ネットワーク セキュリティ グループには、ソースまたはターゲット IP アドレス、ポート、およびプロトコルを基に、受信/送信ネットワーク トラフィックを許可または拒否するセキュリティ規則の一覧が含まれています。
- サブネットに規則を適用すると、サブネット内のすべてのリソースにセキュリティ規則が適用されます。 これには、ネットワーク インターフェイスに加えて、サブネットに展開されている Azure サービスのインスタンスが含まれます。
- アプリケーション セキュリティ グループ (ASG) を使用すると、ネットワーク セキュリティをアプリの構造の自然な拡張として構成でき、VM をグループ化して、それらのグループに基づくネットワーク セキュリティ ポリシーを定義できます。
  - アプリ セキュリティ グループを使うと、明示的な IP アドレスを手動でメンテナンスせずに、スケールでセキュリティ ポリシーを再利用することができます。 プラットフォームが明示的な IP アドレスと複数の規則セットの複雑さを処理するので、ユーザーはビジネス ロジックに専念することができます。
  - Contoso はセキュリティ規則のソースおよびターゲットとして、アプリケーション セキュリティ グループを指定できます。 セキュリティ ポリシーを定義した後は、Contoso はVM を作成し、VM NIC をグループに割り当てることができます。

Contoso は、NSG と ASG の両方を実装します。 Contoso は NSG の管理について気にかけています。 また NSG の使用超過や運用スタッフの作業の複雑化についても心配しています。 Contoso が行うことは次のとおりです。

- すべてのサブネットを出入りするすべてのトラフィックは (南北)、ハブ ネットワークの GatewaySubnets を除き、NSG ルールの対象となります。
- すべてのファイアウォールまたはドメイン コントローラーは、サブネットの NSG と NIC の NSG の両方によって保護されます。
- すべての運用アプリケーションには、ASG を適用します。

Contoso は、これがアプリケーションからどのように見えるかをモデル化しています。

![Security](./media/contoso-migration-infrastructure/asg.png)

ASG に関連付けられた NSG は最小限の特権で構成されており、許可されたパケットのみが、ネットワークの 1 つの部分から宛先にフローできます。

**操作** | **Name** | **ソース** | **移行先** | **[ポート]**
--- | --- | --- | --- | ---
Allow | AllowInternetToFE | VNET-HUB-EUS1/IB-TrustZone | APP1-FE 80、443
Allow | AllowWebToApp | APP1-FE | APP1-APP | 80、443
Allow | AllowAppToDB | APP1-APP | APP1-DB | 1433
拒否 | DenyAllInbound | Any | Any | Any

### <a name="encrypt-data"></a>データを暗号化する

Key Vault サブスクリプションでディスク暗号化キーやシークレットを制御および管理しやすくするために、Azure Disk Encryption は Azure Key Vault と統合されています。 VM 上のすべてのデータが、Azure Storage での保存時に暗号化されることが保証されます。

- Contoso は、特定の VM で暗号化を必要とすることを決定しました。
- Contoso は顧客データ、機密データ、PPI データを含む VM に暗号化を適用します。

## <a name="conclusion"></a>まとめ

この記事では、Contoso は、Azure インフラストラクチャと Azure サブスクリプションのポリシー、ハイブリッド ID、ディザスター リカバリー、ネットワーク、ガバナンス、セキュリティに対するインフラストラクチャ ポリシーを設定しました。

Contoso がここで行ったすてべてのステップが、クラウドへの移行に必要なわけではありません。 Contoso の場合は、すべての種類の移行に使用でき、安全性、回復性、拡張性を備えたネットワーク インフラストラクチャを計画することを望んでいました。

このインフラストラクチャの導入が済むと、Contoso は移行を実施する準備が整います。

## <a name="next-steps"></a>次のステップ

Azure インフラストラクチャを設定したら、Contoso は、クラウドへのワークロードの移行を開始する準備ができました。 このサンプル インフラストラクチャを移行ターゲットとして使用したシナリオの選択については、[移行のパターンと例の概要](./contoso-migration-overview.md#windows-server-workloads)に関するセクションを参照してください。
