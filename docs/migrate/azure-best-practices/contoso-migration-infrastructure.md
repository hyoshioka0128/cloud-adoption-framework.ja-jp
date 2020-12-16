---
title: 移行インフラストラクチャをデプロイする
description: Contoso が Azure への移行用に Azure インフラストラクチャを設定する方法について説明します。
author: deltadan
ms.author: abuck
ms.date: 07/01/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: migrate
ms.custom: think-tank
ms.openlocfilehash: 801d24aaec9a381e2b60eb8874a1a70cea2054b5
ms.sourcegitcommit: b6f2b4f8db6c3b1157299ece1f044cff56895919
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2020
ms.locfileid: "97015764"
---
<!-- cSpell:ignore untrust CIDR RRAS CONTOSODC SYSVOL ITIL NSGs ASGs -->

# <a name="deploy-a-migration-infrastructure"></a>移行インフラストラクチャをデプロイする

この記事では、架空の会社 Contoso がそのオンプレミス インフラストラクチャを移行のために準備し、移行に備えて Azure インフラストラクチャを設定した後、ハイブリッド環境でビジネスを実行する方法を示します。

この例を参考に貴社独自のインフラストラクチャの移行作業を計画される場合は、記載されているサンプル アーキテクチャが Contoso に固有のものであることに注意してください。 サブスクリプション設計またはネットワーク アーキテクチャに関するインフラストラクチャの重要な決定を行う場合は、組織のビジネス ニーズ、構造、技術的な要件を確認してください。

この記事で説明されている要素がすべて必要かどうかは移行戦略によって異なります。 たとえば、Azure でクラウドネイティブ アプリケーションだけを構築する場合は、それほど複雑なネットワーク構造は必要ありません。

## <a name="overview"></a>概要

Contoso が Azure への移行を実施するには、その前に Azure インフラストラクチャを準備することが重要です。 一般に、次の 6 つの領域を考慮する必要があります。

> [!div class="checklist"]
>
> - **ステップ 1:Azure サブスクリプション。** Azure を購入する方法と、Azure プラットフォームおよびサービスを操作する方法。
> - **手順 2:ハイブリッド ID。** 移行後にオンプレミスおよび Azure のリソースへのアクセスを管理および制御する方法。 ID 管理をクラウドに拡張または移行する方法。
> - **ステップ 3:ディザスター リカバリーと回復性。** 停止や障害が発生した場合のアプリケーションとインフラストラクチャの回復性を確保する方法。
> - **手順 4:ネットワーク。** ネットワーク インフラストラクチャを設計する方法、およびオンプレミスのデータ センターと Azure の間の接続を確立する方法。
> - **手順 5:セキュリティ。** ハイブリッド デプロイをセキュリティで保護する方法。
> - **手順 6:ガバナンス。** セキュリティとガバナンスの要件を満たしたデプロイを維持する方法。

## <a name="before-you-start"></a>開始する前に

インフラストラクチャの確認を始める前に、Azure の関連する機能に関する背景情報を参照することを検討してください。

- Azure へのアクセスを購入する場合、従量課金制サブスクリプション、Microsoft Enterprise Agreement (EA)、Microsoft リセラーまたはクラウド ソリューション プロバイダー (CSP) プログラムの Microsoft パートナーから購入するオープン ライセンスなど、複数のオプションが用意されています。 [購入オプション](https://azure.microsoft.com/pricing/purchase-options/)について学習し、[Azure サブスクリプションの編成](https://azure.microsoft.com/blog/organizing-subscriptions-and-resource-groups-within-the-enterprise/)方法をお読みください。
- Azure の [ID およびアクセス管理 (IAM)](https://www.microsoft.com/security/business/identity) の概要をご覧ください。 [Azure Active Directory (Azure AD)、およびオンプレミス Active Directory のクラウドへの拡張](/azure/active-directory/fundamentals/active-directory-whatis)について確認します。
- Azure は、ハイブリッド接続のオプションを含む堅牢なネットワーク インフラストラクチャを提供します。 [ネットワークとネットワーク アクセス制御](/azure/security/fundamentals/network-overview)の概要についてはこちらをご覧ください。
- 「[Azure のセキュリティの概要](/azure/security/fundamentals/overview)」を読み、[Azure のガバナンス](/azure/governance/)の計画を作成する方法を確認します。

## <a name="on-premises-architecture"></a>オンプレミス アーキテクチャ

以下の図は、Contoso の現在のオンプレミス インフラストラクチャを示しています。

![Contoso のアーキテクチャの図。](./media/contoso-migration-infrastructure/contoso-architecture.png)

"_図 1:Contoso のオンプレミス アーキテクチャ_

- Contoso は、米国東部のニューヨーク市に 1 つのメイン データセンターを配置しています。
- Contoso は、米国の地方に 3 つの支店を配置しています。
- メイン データセンターは、光ファイバーによるメトロ イーサネット接続 (500 Mbps) でインターネットに接続されています。
- 各支店は、ビジネス クラスの接続を使用して、インターネットにローカルで接続されています。メイン データセンターには、IPSec VPN トンネルで接続されています。 このアプローチにより、ネットワーク全体を永続的に接続することが可能となり、インターネット接続が最適化されます。
- メイン データセンターは、VMware によって完全に仮想化されています。 Contoso には、vCenter Server 6.5 によって管理された 2 つの ESXi 6.5 仮想化ホストがあります。
- Contoso では、ID 管理に Active Directory を使用し、内部ネットワーク上でドメイン ネーム システム (DNS) サーバーを使用しています。
- データセンター内のドメイン コントローラーは、VMware 仮想マシン (VM) 上で実行されています。 支店にあるドメイン コントローラーは、物理サーバー上で実行されています。

## <a name="step-1-buy-and-subscribe-to-azure"></a>手順 1:Azure を購入して登録する

Contoso は、Azure の購入方法、サブスクリプションの管理方法、およびサービスとリソースのライセンスを取得する方法を明らかにする必要があります。

### <a name="buy-azure"></a>Azure を購入する

Contoso は、[マイクロソフト エンタープライズ契約](https://azure.microsoft.com/overview/sales-number/)に登録しようとしています。 この契約には、Azure に対する前払いによる年額コミットメントが必然的に伴います。 Contoso には柔軟な課金オプションや価格の最適化などのメリットがあります。

これらについて詳しく説明します。

- Contoso は Azure への年間の支出を推定しました。 契約に署名する時点で、Contoso は最初の 1 年間の料金を払いました。
- Contoso は 1 年が過ぎる前にすべてのコミットメントを使用する必要があります。そうしないと、金銭的価値を失うことになります。
- なんらかの理由で Contoso がそのコミットメントを超えて使うと、Microsoft は差額を請求します。
- コミットメントを超えて発生したコストはすべて、Contoso のコントラクトに記載されているものと同じレートになります。 超過に対するペナルティはありません。

### <a name="manage-subscriptions"></a>サブスクリプションの管理

Azure の料金を支払った後、Contoso は Azure サブスクリプションを管理する方法を決める必要があります。 Contoso は EA を利用しているので、作成できる Azure サブスクリプションの数に制限はありません。 Azure Enterprise Agreement 加入契約では、企業が Azure サービスを構成して使用する方法と、中心となるガバナンス構造が定義されています。

最初の手順として、Contoso は、加入契約のための "_エンタープライズ スキャフォールディング_" と呼ばれる構造を定義しました。 Contoso は、[Azure エンタープライズ スキャフォールディングのガイダンス](/azure/cloud-adoption-framework/reference/azure-scaffold)を使用して、スキャフォールディングの理解と設計に役立てました。

ここでは、Contoso は機能的アプローチを使用してサブスクリプションを管理することを決定しました。

- 会社内では単一の IT 部門を利用し、そこで Azure の予算を管理します。 これがサブスクリプションを持つ唯一のグループになります。
- Contoso は将来的にこのモデルを拡張し、会社の他のグループがエンタープライズ階層に部門として参加できるようにする意向です。
- IT 部門内には、`Production` と `Development` の 2 つのサブスクリプションが構成されています。
- 将来 Contoso で、サブスクリプションを追加する必要がある場合は、それらのサブスクリプションについてもアクセス、ポリシー、コンプライアンスを管理する必要があります。 Contoso は、サブスクリプションの上に追加の階層として [Azure 管理グループ](/azure/governance/management-groups/overview)を導入することで、これを行うことができます。

![エンタープライズ階層の図。](./media/contoso-migration-infrastructure/enterprise-structure.png)

"_図 2:エンタープライズ階層_

### <a name="examine-licensing"></a>ライセンスを調べる

サブスクリプションの構成が済むと、Contoso は Microsoft のライセンスを確認できます。 ライセンス戦略は、Contoso が Azure に移行しようとしているリソースと、VM およびサービスが選択され、Azure にデプロイされる方法によって異なります。

#### <a name="azure-hybrid-benefit"></a>Azure ハイブリッド特典

Azure に VM をデプロイできるように、標準イメージにはソフトウェアが使用された分単位で Contoso に請求されるライセンスが含まれます。 ただし、Contoso は Microsoft の長年の顧客であり、EA およびソフトウェア アシュアランス付きのオープン ライセンスを保持しています。

Azure ハイブリッド特典では、コスト効果の高い移行方法が提供されます。 ソフトウェア アシュアランスでカバーされた Windows Server Datacenter および Standard Edition のライセンスを変換または再利用することによって、Contoso は Azure VM と SQL Server のワークロードを節約できます。 これにより、Contoso は、VM と SQL Server に対して支払う基本コンピューティング料金を低減できます。 詳細については、「[Azure ハイブリッド特典](https://azure.microsoft.com/pricing/hybrid-benefit/)」を参照してください。

#### <a name="license-mobility"></a>ライセンス モビリティ

ソフトウェア アシュアランスによるライセンス モビリティを利用すると、Contoso のような Microsoft ボリューム ライセンスのお客様は、Azure でのアクティブなソフトウェア アシュアランスによって、対象となるサーバー アプリケーションを柔軟にデプロイできます。 これにより、新しいライセンスを購入する必要がなくなります。 関連するモビリティ料金なしで、既存のライセンスを Azure に簡単に展開できます。 詳細については、「 [Azure でのソフトウェア アシュアランスによるライセンス モビリティ](https://azure.microsoft.com/pricing/license-mobility/)」を参照してください。

#### <a name="reserved-instances-for-predictable-workloads"></a>予測可能なワークロードのための予約インスタンス

予測可能なワークロードは、SAP ERP システムのような基幹業務アプリケーションなど、実行されている VM で常に使用できる必要があります。 予測不可能なワークロードとは、需要が多いときに有効になり、需要が少ないと無効になる VM のような、変わりやすいワークロードです。

![Azure Reserved Virtual Machine Instances の図。](./media/contoso-migration-infrastructure/reserved-instance.png)

"_図 3:Azure Reserved Virtual Machine Instances_

長時間維持する必要がある特定の VM インスタンスに対して予約インスタンスを使用する見返りに、Contoso は割引と優先容量の両方を得ることができます。 [Azure Reserved Virtual Machine Instances](https://azure.microsoft.com/pricing/reserved-vm-instances/) を Azure ハイブリッド特典と組み合わせて使用することで、Contoso は通常の従量課金制料金を最大 82% 節約できます (2018 年 4 月現在)。

## <a name="step-2-manage-hybrid-identity"></a>手順 2:ハイブリッドの ID を管理する

ID およびアクセス管理によってユーザーが Azure リソースにアクセスできるようにして、アクセスを制御することは、Azure インフラストラクチャをまとめ上げるための重要なステップです。

Contoso では、Azure に新しい別個のシステムを構築するのではなく、オンプレミスの Active Directory をクラウドに拡張することに決めています。 Contoso は Microsoft 365 をまだ使用していないため、Azure AD インスタンスをプロビジョニングする必要があります。 Contoso が Microsoft 365 を使用していれば、既存の Azure AD テナントおよびディレクトリが既にあるので、これをプライマリ Azure AD インスタンスとして使用できます。

[Microsoft 365 ID モデルと Azure Active Directory](/microsoft-365/enterprise/about-microsoft-365-identity?view=o365-worldwide) の詳細を確認します。 [Azure サブスクリプションを Azure Active Directory テナントに関連付けるか、追加する](/azure/active-directory/fundamentals/active-directory-how-subscriptions-associated-directory)方法も確認してください。

### <a name="create-an-azure-ad-directory"></a>Azure AD Directory を作成する

Contoso は、Azure サブスクリプションに含まれる Azure AD Free エディションを使っています。 Contoso の管理者は、Azure AD ディレクトリを作成します。

1. [Azure portal](https://portal.azure.com) で、 **[リソースの作成]**  >  **[Identity]**  >  **[Azure Active Directory]** の順に選択します。

1. **[ディレクトリの作成]** で、ディレクトリの名前、初期ドメイン名、ディレクトリを作成するリージョンを指定します。

   ![Azure AD ディレクトリを作成するための選択項目のスクリーンショット。](./media/contoso-migration-infrastructure/azure-ad-create.png)

   "_図 4:Azure AD Directory の作成_

> [!NOTE]
> 作成したディレクトリの初期ドメイン名は、`domain-name.onmicrosoft.com` という形式になります。 この名前を変更または削除することはできません。 代わりに、管理者は、登録されたドメイン名を Azure AD に追加する必要があります。

### <a name="add-the-domain-name"></a>ドメイン名を追加する

標準のドメイン名を使用するには、Contoso の管理者がそれをカスタム ドメイン名として Azure AD に追加する必要があります。 この方法を使うと、管理者は使い慣れたユーザー名を割り当てることができます。 たとえば、ユーザーは `billg@contosomigration.onmicrosoft.com` ではなく、メール アドレス `billg@contoso.com` でサインインできます。

カスタム ドメイン名を設定するには、管理者がディレクトリにカスタム ドメイン名を追加し、DNS エントリを追加して、Azure AD で名前を確認します。

1. **[カスタム ドメイン名]**  >  **[カスタム ドメインの追加]** で、ドメインを追加します。
2. Azure で DNS エントリを使うには、ドメイン レジストラーに DNS エントリを登録する必要があります。

    - **[カスタム ドメイン名]** の一覧で、名前の DNS 情報を記録します。 MX レコードを使用しています。
    - ネーム サーバーにアクセスする必要があります。 `contoso.com` ドメインにログインし、メモしておいた詳細情報を使用して、Azure AD によって提供された DNS エントリの新しい MX レコードを作成します。

3. DNS レコードが伝播されたら、 **[検証]** を選択して、ドメインの詳細でカスタム ドメイン名を確認します。

    ![Azure Active Directory DNS の選択画面を示すスクリーンショット。](./media/contoso-migration-infrastructure/azure-ad-dns.png)

    _図 5:ドメイン名の確認_

### <a name="set-up-on-premises-and-azure-groups-and-users"></a>オンプレミスと Azure でグループとユーザー設定する

Azure AD ディレクトリが確立されたら、Contoso の管理者は、Azure AD に同期されるオンプレミスの Active Directory グループに従業員を追加する必要があります。 Azure でのリソース グループの名前と一致するように、オンプレミスのグループ名を設定する必要があります。 これにより、同期を行うために一致するものを識別しやすくします。

#### <a name="create-resource-groups-in-azure"></a>Azure でリソース グループを作成する

Azure リソース グループは Azure リソースをひとまとめにしたものです。 リソース グループ ID を使うことで、Azure はグループ内のリソースに対して操作を実行できます。

1 つの Azure サブスクリプションに複数のリソース グループを含めることができます。 リソース グループは単一のサブスクリプションに存在します。 さらに、1 つのリソース グループに複数のリソースを含めることができます。 リソースは単一のリソース グループに属します。

Contoso の管理者は、次の表に示すように Azure リソース グループを設定します。

| Resource group | 詳細 |
| --- | --- |
| `ContosoCobRG` | このグループには、ビジネスの継続性に関連するすべてのリソースが含まれています。 これには、Contoso が Azure Site Recovery サービスおよび Azure Backup サービス用に使用するコンテナーが含まれます。 <br><br> また、Azure Migrate や Azure Database Migration Service など、移行に使用されるリソースも含まれます。 |
| `ContosoDevRG` | このグループには、Dev/Test リソースが含まれています。 |
| `ContosoFailoverRG` | このグループは、フェールオーバーされたリソースのランディング ゾーンとして機能します。 |
| `ContosoNetworkingRG` | このグループには、すべてのネットワーク リソースが含まれます。 |
| `ContosoRG` | このグループには、運用アプリケーションおよびデータベースに関連するリソースが含まれています。 |

リソース グループは次のようにして作成します。

1. Azure portal の **[リソース グループ]** で、グループを追加します。
2. グループごとに、名前、グループが属するサブスクリプション、およびリージョンを指定します。
3. リソース グループが **[リソース グループ]** の一覧に表示されます。

   ![リソース グループの一覧を示すスクリーンショット](./media/contoso-migration-infrastructure/resource-groups.png)

   _図 6:リソース グループ_

##### <a name="scale-resource-groups"></a>リソース グループをスケーリングする

今後、Contoso では、ニーズに基づいて他のリソース グループを追加することになります。 たとえば、アプリケーションまたはサービスごとにリソース グループを定義することで、それぞれを個別に管理し、セキュリティで保護できます。

#### <a name="create-matching-security-groups-on-premises"></a>一致するセキュリティ グループをオンプレミスに作成する

Contoso の管理者は、オンプレミスの Active Directory インスタンスで、Azure リソース グループの名前と一致する名前を持つセキュリティ グループを設定します。

![オンプレミスの Active Directory セキュリティ グループを示すスクリーンショット。](./media/contoso-migration-infrastructure/on-premises-ad.png)

_図 7:オンプレミスの Active Directory セキュリティ グループ_

管理のため、Contoso は他のすべてのグループに追加されるグループを作成します。 このグループには、Azure 内のすべてのリソース グループに対する権限があります。 限られた数のグローバル管理者がこのグループに追加されます。

### <a name="synchronize-active-directory"></a>Active Directory を同期する

Contoso は、オンプレミスとクラウドのリソースへのアクセスに共通の ID を提供することを望んでいます。 そのためには、オンプレミスの Active Directory インスタンスと Azure AD を統合します。 このモデルでは、ユーザーと組織は単一の ID を利用して、オンプレミスのアプリケーションと、Microsoft 365 などのクラウド サービスやインターネット上の何千もの他のサイトにアクセスできます。 管理者は、Active Directory 内のグループを使用して、Azure で[ロールベースのアクセス制御 (RBAC)](/azure/role-based-access-control/role-assignments-portal) を実装できます。

統合を容易にするため、Contoso は [Azure AD Connect ツール](/azure/active-directory/hybrid/whatis-hybrid-identity)を使います。 このツールをドメイン コントローラーにインストールして構成すると、オンプレミスの Active Directory ID が Azure AD に同期されます。

### <a name="download-the-tool"></a>ツールをダウンロードする

1. Contoso の管理者は、Azure portal で **[Azure Active Directory]**  >  **[Azure AD Connect]** の順に移動し、同期に使用しているサーバーに最新バージョンのツールをダウンロードします。

    ![ダウンロードされた Azure AD Connect へのリンクを示すスクリーンショット。](./media/contoso-migration-infrastructure/download-ad-connect.png)

    _図 8:Azure AD Connect のダウンロード_

2. **[簡単設定]** を使用して、`AzureADConnect.msi` のインストールを開始します。 これは最も一般的なインストールであり、認証にパスワード ハッシュ同期を使用する単一フォレスト トポロジで使用できます。

    ![Azure AD Connect ウィザードを示すスクリーンショット。](./media/contoso-migration-infrastructure/ad-connect-wiz1.png)

    _図 9:Azure AD Connect ウィザード_

3. **[Azure AD に接続]** で、Azure AD に接続するための資格情報 (`admin@contoso.com` または `admin@contoso.onmicrosoft.com` の形式) を指定します。

    ![Azure AD Connect ウィザードの [Azure AD に接続] ページを示すスクリーンショット。](./media/contoso-migration-infrastructure/ad-connect-wiz2.png)

    _図 10:Azure AD Connect ウィザード:Azure AD に接続_

4. **[AD DS に接続]** で、オンプレミスのディレクトリの資格情報 (`CONTOSO\admin` または `contoso.com\admin` の形式) を指定します。

    ![Azure AD Connect ウィザードの [AD DS に接続] ページを示すスクリーンショット。](./media/contoso-migration-infrastructure/ad-connect-wiz3.png)

    _図 11:Azure AD Connect ウィザード:AD DS に接続_"

5. **[構成の準備完了]** で、 **[構成が完了したら、同期プロセスを開始してください]** を選択して直ちに同期を開始します。 その後インストールします。

    次のことを考慮してください。

    - Contoso は Azure に直接接続します。 オンプレミスの Active Directory インスタンスがプロキシの内側にある場合は、「[Azure AD 接続性のトラブルシューティング](/azure/active-directory/hybrid/tshoot-connect-connectivity)」を参照してください。

    - 最初の同期の後、オンプレミスの Active Directory オブジェクトは Azure AD ディレクトリ内に表示されます。

      ![Azure Active Directory に表示されたオンプレミスの Active Directory オブジェクトを示すスクリーンショット。](./media/contoso-migration-infrastructure/on-premises-ad-groups.png)

      _図 12:Azure AD に表示されたオンプレミスの Active Directory オブジェクト_

    - Contoso の IT チームは各グループで表わされ、ロールに基づいています。

      ![グループ メンバーシップを示すスクリーンショット。](./media/contoso-migration-infrastructure/on-premises-ad-group-members.png)

      _図 13:グループ メンバーシップ_

### <a name="set-up-rbac"></a>RBAC を設定する

Azure [RBAC](/azure/role-based-access-control/role-assignments-portal) を使用すると、Azure のきめ細かいアクセス管理が可能になります。 RBAC を使用することで、タスクの実行に必要な範囲のアクセス権だけをユーザーに付与することができます。 適切な RBAC ロールをユーザー、グループ、およびアプリケーションにスコープ レベルで割り当てます。 ロール割り当てのスコープには、サブスクリプション、リソース グループ、または単独のリソースを指定できます。

Contoso の管理者は、オンプレミスから同期した Active Directory グループにロールを割り当てます。

1. `ControlCobRG` リソース グループで、 **[アクセス制御 (IAM)]**  >  **[ロールの割り当ての追加]** を選択します。
2. **[ロールの割り当ての追加]**  >  **[ロール]**  >  **[共同作成者]** で、一覧から `ContosoCobRG` セキュリティ グループを選択します。 グループが **[選択したメンバー]** の一覧に表示されます。
3. 他のリソース グループ (`ContosoAzureAdmins` を除く) に対して、同じアクセス許可を使用してこれを繰り返します。つまり、そのリソース グループに対応するセキュリティ グループに **共同作成者** のアクセス許可を追加します。
4. `ContosoAzureAdmins` セキュリティ グループには、**所有者** ロールを割り当てます。

    ![オンプレミスの Azure Active Directory グループを示すスクリーンショット。](./media/contoso-migration-infrastructure/on-premises-ad-groups.png)

    _図 14:セキュリティ グループへのロールの割り当て_

## <a name="step-3-design-for-resiliency"></a>手順 3:回復性の設計

### <a name="set-up-regions"></a>リージョンを設定する

Azure リソースはリージョン内に展開されます。 リージョンは地域に分かれています。 データ所在地、主権、コンプライアンス、回復性に関する要件が地域的な境界内で確実に遵守されます。

リージョンは一連のデータセンターで構成されます。 これらのデータセンターは、待機時間が定義された境界内に展開され、待機時間の短い専用のリージョン ネットワークを介して接続されます。

回復性のため、各 Azure リージョンは異なるリージョンとペアにされます。 [Azure リージョン](https://azure.microsoft.com/global-infrastructure/geographies/)について参照し、[リージョンが組み合わされる方法](/azure/best-practices-availability-paired-regions)を理解してください。

次の理由から、Contoso は、プライマリ リージョンとして `East US 2` (バージニア州)、セカンダリ リージョンとして `Central US` (アイオワ州) を使用することにしました。

- Contoso のデータセンターはニューヨークにあり、Contoso では最も近いデータセンターに対する待機時間を考慮しました。
- `East US 2` には、Contoso が必要とするすべてのサービスと製品があります。 すべての Azure リージョンで同じ製品とサービスを利用できるわけではありません。 詳細については、「[リージョン別の Azure 製品](https://azure.microsoft.com/global-infrastructure/services/)」を参照してください。
- `Central US` は、`East US 2` の Azure ペア リージョンです。

ハイブリッド環境を考慮して、Contoso はリージョンの設計に回復性とディザスター リカバリーを組み込む方法を検討する必要があります。 最もシンプルな戦略は、回復性を確保するために障害ドメインやリージョン ペアなどの Azure プラットフォームの機能を利用する単一リージョンのデプロイです。 最も複雑な戦略は、クラウド サービスとデータベースが 2 つのリージョンにデプロイされてユーザーに提供される完全なアクティブ/アクティブ モデルです。

Contoso は中間の方法を採用することにしました。 アプリケーションとリソースはプライマリ リージョンにデプロイされ、セカンダリ リージョンには、インフラストラクチャの完全なコピーが保持されます。 この戦略では、アプリケーションの完全な障害やリージョンの障害が発生した場合に、このコピーが完全バックアップとして機能します。

### <a name="set-up-availability"></a>可用性を設定する

#### <a name="availability-sets"></a>可用性セット

可用性セットを使用すると、データセンター内のローカル ハードウェアやネットワークの障害からアプリケーションとデータを保護できます。 可用性セットは、Azure VM をデータセンター内の複数の物理ハードウェアにわたって分散させます。

障害ドメインは、データセンター内の一般的な電源とネットワーク スイッチを持つ基になるハードウェアを表します。 可用性セット内の VM は、1 つのハードウェアまたはネットワーク障害によって発生する停止を最小限に抑えるために、複数の障害ドメインにわたって分散されます。

更新ドメインは、同時にメンテナンスを実行したり、再起動したりできる基になるハードウェアを表します。 可用性セットはまた、常に少なくとも 1 つのインスタンスが実行されている状態を確保するために、複数の更新ドメインにわたって VM を分散させます。

Contoso は、VM ワークロードに高可用性が必要な場合は常に可用性セットを実装します。 詳細については、「[Azure での Windows 仮想マシンの可用性の管理](/azure/virtual-machines/manage-availability)」をご覧ください。

#### <a name="availability-zones"></a>可用性ゾーン

Availability Zones を使用すると、リージョン内のデータセンター全体に影響する障害からアプリケーションとデータを保護できます。

各可用性ゾーンは、Azure リージョン内の一意の物理的な場所を表します。 それぞれのゾーンは、独立した電源、冷却手段、ネットワークを備えた 1 つまたは複数のデータセンターで構成されています。

有効になっているリージョンにはいずれも、最低 3 つのゾーンが別個に存在しています。 ゾーンは 1 つのリージョン内で物理的に分離されているため、データセンターで障害が発生した場合でも、アプリケーションとデータを保護できます。

Contoso は、アプリケーションのスケーラビリティ、可用性、回復性を高める必要がある場合は常に、Availability Zones を使用します。 詳細については、「[Azure のリージョンと Availability Zones](/azure/availability-zones/az-overview)」をご覧ください。

### <a name="configure-backup"></a>バックアップの構成

#### <a name="azure-backup"></a>Azure Backup

Azure Backup を使用すると、Azure VM ディスクをバックアップおよび復元できます。

Azure Backup では、Azure Storage に格納されている VM ディスク イメージの自動バックアップが可能です。 バックアップはアプリケーション整合性を備えているため、バックアップされたデータにトランザクション整合性があり、かつ復元後にアプリケーションが起動されることが保証されます。

Azure Backup はローカル冗長ストレージ (LRS) をサポートしており、ローカルのハードウェア障害が発生した場合は、データセンター内のバックアップ データの複数のコピーをレプリケートします。 Azure Backup は geo 冗長ストレージ (GRS) もサポートしており、リージョンの障害が発生した場合は、ペアになっているセカンダリ リージョンにバックアップ データをレプリケートします。

Azure Backup では、AES-256 を使用して転送中のデータを暗号化します。 バックアップされた保存データは、[Azure Storage 暗号化](/azure/storage/common/storage-service-encryption)を使用して暗号化されます。

Contoso は、ワークロード データがバックアップされ、中断が発生した場合に速やかに復元できるように、すべての運用 VM で Azure Backup を GRS と共に使用します。 詳細については、「[Azure VM バックアップの概要](/azure/backup/backup-azure-vms-introduction)」をご覧ください。

### <a name="set-up-disaster-recovery"></a>ディザスター リカバリーを設定する

#### <a name="azure-site-recovery"></a>Azure Site Recovery

Azure Site Recovery を使用すると、リージョン障害時にもビジネス アプリケーションおよびワークロードの実行状態が維持されるため、ビジネス継続性を確保できます。

Azure Site Recovery は、Azure VM をプライマリ リージョンからセカンダリ リージョンに継続的にレプリケートして、機能的なコピーを両方の場所に確保します。 プライマリ リージョンに障害が発生した場合、アプリケーションやサービスは、セカンダリ リージョンにレプリケートされた VM インスタンスを使用するようフェールオーバーされます。 これによって、中断のリスクが最小限に抑えられます。 通常の運用に戻ったら、アプリケーションまたはサービスはプライマリ リージョン内の VM にフェールバックできます。

Contoso は、ミッション クリティカルなワークロードで使用されるすべての運用 VM に [Azure Site Recovery](/azure/site-recovery/site-recovery-overview) を実装することで、プライマリ リージョンでの障害時の中断を最小限に抑えることができるようにします。

## <a name="step-4-design-a-network-infrastructure"></a>手順 4:ネットワーク インフラストラクチャを設計する

リージョンの設計が決まれば、Contoso は次にネットワーク戦略を検討できます。 オンプレミスのデータセンターと Azure が相互に接続して通信する方法、および Azure のネットワーク インフラストラクチャを設計する方法を検討する必要があります。 特に、Contoso では次のことが必要です。

- **ハイブリッド ネットワーク接続を計画する。** オンプレミスと Azure の間でネットワークを接続する方法を明らかにします。
- **Azure ネットワーク インフラストラクチャを設計する。** リージョンにネットワークを展開する方法を決定します。 また、同じリージョン内および異なるリージョン間でのネットワークの通信方法も決定します。
- **Azure ネットワークを設計および設定する。** Azure ネットワークとサブネットを設定し、それらに置くものを決定します。

### <a name="plan-hybrid-network-connectivity"></a>ハイブリッド ネットワーク接続を計画する

Contoso は、Azure とオンプレミスのデータセンターの間の[ハイブリッド ネットワークに関して複数のアーキテクチャ](/azure/architecture/reference-architectures/hybrid-networking/)を検討しました。 詳しくは、「[オンプレミス ネットワークを Azure に接続するためのソリューションを選択する](/azure/architecture/reference-architectures/hybrid-networking/)」をご覧ください。

なお、Contoso のオンプレミス ネットワークのインフラストラクチャは、現在、ニューヨークのデータセンターと、米国の東半分にある複数のローカル支局で構成されています。 すべての場所には、インターネットへのビジネス クラスの接続があります。 各ブランチは、インターネット経由の IPSec VPN トンネルを介してデータセンターに接続されています。

![Contoso ネットワークの図。](./media/contoso-migration-infrastructure/contoso-networking.png)

_図 15:Contoso ネットワーク_

Contoso が決定したハイブリッド接続の実装方法は次のとおりです。

1. ニューヨークにある Contoso データセンターと、`East US 2` および `Central US` の 2 つの Azure リージョンの間に、新しいサイト間 VPN 接続を設定します。
2. Azure 内の仮想ネットワーク宛のブランチ オフィスのトラフィックは、Contoso のメイン データセンターを介してルーティングされます。
3. Azure のデプロイをスケールアップするときは、データセンターと Azure リージョンの間に Azure ExpressRoute 接続を確立します。 その際、Contoso は、VPN のサイト間接続をフェールオーバーのためだけに保持します。
    - VPN と ExpressRoute ハイブリッド ソリューションの選択の詳細については、[こちら](/azure/architecture/reference-architectures/hybrid-networking/)をご覧ください。
    - [ExpressRoute の場所とサポート](/azure/expressroute/expressroute-locations-providers)を確認してください。

**VPN のみ:**

![Contoso の VPN を示すスクリーンショット。](./media/contoso-migration-infrastructure/hybrid-vpn.png)

_図 16:Contoso VPN_

**VPN および ExpressRoute:**

![Contoso の VPN と ExpressRoute を示すスクリーンショット。](./media/contoso-migration-infrastructure/hybrid-vpn-expressroute.png)

_図 17:Contoso VPN と ExpressRoute_

### <a name="design-the-azure-network-infrastructure"></a>Azure のネットワーク インフラストラクチャを設計する

Contoso のネットワーク構成では、ハイブリッド デプロイをセキュリティで保護し、スケーラブルにする必要があります。 Contoso はこれに対して長期的なアプローチを採用し、回復性を備えたエンタープライズ対応の仮想ネットワークを設計しています。 詳細については、「[仮想ネットワークを計画する](/azure/virtual-network/virtual-network-vnet-plan-design-arm)」をご覧ください。

2 つのリージョンを接続するために、Contoso はハブ間ネットワーク モデルを実装します。 各リージョン内では、ハブ アンド スポーク モデルを使用します。 ネットワークとハブを接続するために、Contoso は Azure ネットワーク ピアリングを使用します。

#### <a name="network-peering"></a>ネットワーク ピアリング

Azure の[仮想ネットワーク ピアリング](/azure/virtual-network/virtual-network-peering-overview)では、仮想ネットワークとハブを接続します。 グローバル ピアリングを使うと、異なるリージョンの仮想ネットワークまたはハブ間の接続が可能になります。 ローカル ピアリングでは、同じリージョン内の仮想ネットワークを接続します。

仮想ネットワーク ピアリングには、次のようないくつかの利点があります。

- ピアリングされた仮想ネットワーク間のネットワーク トラフィックはプライベートである。
- 仮想ネットワーク間のトラフィックは、Microsoft のバックボーン ネットワーク上で保持されます。 仮想ネットワーク間の通信では、パブリック インターネット、ゲートウェイ、暗号化が必要ありません。
- ピアリングでは、異なる仮想ネットワーク内のリソースの間に、低遅延かつ広帯域幅の接続が既定で提供されます。

#### <a name="hub-to-hub-model-across-regions"></a>複数のリージョンにわたるハブ間モデル

Contoso は各リージョンにハブを展開します。 ハブは、オンプレミス ネットワークへの接続の中心点として機能する Azure の仮想ネットワークです。 ハブの仮想ネットワークは、Azure リージョンの境界をまたいで仮想ネットワークを接続するグローバル仮想ネットワーク ピアリングにより、相互に接続されます。 各リージョンのハブは、他のリージョンにあるパートナー ハブにピアリングされます。 ハブは、そのリージョン内のすべてのネットワークにピアリングされ、すべてのネットワーク リソースに接続できます。

![グローバル ピアリングの図。](./media/contoso-migration-infrastructure/global-peering.png)

_図 18:グローバル ピアリング_

#### <a name="hub-and-spoke-model-within-a-region"></a>リージョン内のハブ アンド スポーク モデル

各リージョン内で、Contoso はリージョン ハブからのスポーク ネットワークとして、さまざまな目的の仮想ネットワークをデプロイします。 リージョン内の仮想ネットワークは、ピアリングを使用してハブに接続すると共に、相互に接続します。

#### <a name="design-the-hub-network"></a>ハブ ネットワークを設計する

Contoso は、ハブ アンド スポーク モデル内で、オンプレミスのデータセンターとインターネットからのトラフィックをルーティングする方法について考える必要があります。 Contoso が、`East US 2` ハブと `Central US` ハブのルーティングの処理をどのように決定したかを次に示します。

- Contoso では、VPN を使用して、インターネットからも、企業ネットワークからも Azure へのトラフィックを許可するネットワークを設計しようとしています。
- そのネットワーク アーキテクチャには、信頼されていないフロントエンド境界ゾーンと信頼されているバックエンド ゾーンの 2 つの境界があります。
- ファイアウォールは、ゾーンごとにネットワーク アダプターを備え、信頼されているゾーンへのアクセスを制御します。
- インターネットから:
  - インターネット トラフィックは、境界ネットワーク上の負荷分散されたパブリック IP アドレスにヒットします。
  - このトラフィックは、ファイアウォール経由でルーティングされ、ファイアウォール規則に従います。
  - ネットワーク アクセス制御が実装されると、トラフィックは信頼されているゾーン内の適切な場所に転送されます。
  - 仮想ネットワークからのアウトバウンド トラフィックは、ユーザー定義ルートを使用してインターネットにルーティングされます。 トラフィックはファイアウォールを通過するように強制され、Contoso のポリシーに従って検査されます。
- Contoso データセンターから:
  - サイト間 VPN または ExpressRoute 経由の受信トラフィックは、Azure VPN ゲートウェイのパブリック IP アドレスに到達します。
  - トラフィックは、ファイアウォール経由でルーティングされ、ファイアウォール規則に従います。
  - ファイアウォール ルールを適用すると、トラフィックは信頼されている内部ゾーン サブネット上の内部ロード バランサー (標準 SKU) に転送されます。
  - 信頼されているサブネットからオンプレミスのデータセンターへの VPN 経由のアウトバウンド トラフィックは、ファイアウォールを通してルーティングされます。 規則は、トラフィックがサイト間 VPN 接続を通過する前に適用されます。

### <a name="design-and-set-up-azure-networks"></a>Azure ネットワークを設計および設定する

ネットワークとルーティング トポロジが決まると、Contoso は Azure ネットワークとサブネットを設定できるようになります。

<!-- docutune:casing "class-A" "class-B" -->

- Contoso は、クラス A のプライベート ネットワークを Azure に実装します (`10.0.0.0/8`)。 これが有効なのはオンプレミスであるためです。現在は、クラス B のプライベート アドレス空間 (`172.160.0.0/16`) が割り当てられています。 アドレス範囲の重複がないことは確実です。
- Contoso は、プライマリ リージョンとセカンダリ リージョンの両方に仮想ネットワークをデプロイします。
- Contoso は、プレフィックス `VNET` と、リージョンの省略形 `EUS2` または `CUS` を含む名前付け規則を使用します。 この標準を使用して、ハブ ネットワークに `VNET-HUB-EUS2` (`East US 2` リージョン) および `VNET-HUB-CUS` (`Central US` リージョン) という名前が付けられます。

#### <a name="virtual-networks-in-east-us-2"></a>`East US 2` の仮想ネットワーク

`East US 2` は、Contoso がリソースとサービスのデプロイに使用するプライマリ リージョンです。 Contoso が、そのリージョンでネットワークをどのように設計するのかを次に示します。

- **ハブ:** `East US 2` のハブ仮想ネットワークは、オンプレミスのデータセンターへの Contoso のプライマリ接続と見なされます。

- **仮想ネットワーク:** `East US 2` のスポーク仮想ネットワークは、必要に応じてワークロードを分離するために使用できます。 ハブ仮想ネットワークに加えて、Contoso は 2 つのスポーク仮想ネットワークを `East US 2` で使用します。

  - `VNET-DEV-EUS2`. この仮想ネットワークは、開発プロジェクト用の完全に機能するネットワークを Dev/Test チームに提供します。 運用パイロット領域として動作し、運用インフラストラクチャを利用して機能します。

  - `VNET-PROD-EUS2`. Azure IaaS 運用コンポーネントは、このネットワークに配置されます。

  各仮想ネットワークは、重複のない独自の一意のアドレス空間を使用します。 Contoso はネットワーク アドレス変換 (NAT) が必要ないようにルーティングを構成する予定です。

- **サブネット:** 各アプリケーション層の各ネットワークにサブネットがあります。 運用ネットワークの各サブネットには、開発用仮想ネットワークに対応するサブネットがあります。 運用ネットワークには、ドメイン コントローラー用のサブネットがあります。

次の表は、`East US 2` 内の仮想ネットワークをまとめたものです。

| 仮想ネットワーク | Range | ピア |
| --- | --- | --- |
| `VNET-HUB-EUS2` | `10.240.0.0/20` | `VNET-HUB-CUS2`, `VNET-DEV-EUS2`, `VNET-PROD-EUS2` |
| `VNET-DEV-EUS2` | `10.245.16.0/20` | `VNET-HUB-EUS2` |
| `VNET-PROD-EUS2` | `10.245.32.0/20` | `VNET-HUB-EUS2`, `VNET-PROD-CUS` |

![プライマリ リージョン内のハブ アンド スポーク モデルの図。](./media/contoso-migration-infrastructure/primary-hub-peer.png)

_図 19:ハブ アンド スポーク モデル_

#### <a name="subnets-in-the-east-us-2-hub-network-vnet-hub-eus2"></a>`East US 2 Hub` ネットワーク (`VNET-HUB-EUS2`) のサブネット

| サブネット/ゾーン | CIDR | 使用可能な IP アドレス |
| --- | --- | --- |
| `IB-UntrustZone` | `10.240.0.0/24`  | 251 |
| `IB-TrustZone`   | `10.240.1.0/24`  | 251 |
| `OB-UntrustZone` | `10.240.2.0/24`  | 251 |
| `OB-TrustZone`   | `10.240.3.0/24`  | 251 |
| `GatewaySubnet`  | `10.240.10.0/24` | 251 |

#### <a name="subnets-in-the-east-us-2-development-network-vnet-dev-eus2"></a>`East US 2` の開発用ネットワーク (`VNET-DEV-EUS2`) のサブネット

開発用仮想ネットワークは、運用パイロット領域として開発チームが使用します。 3 つのサブネットがあります。

| Subnet | CIDR | アドレス | サブネット内 |
| --- | --- | --- | --- |
| `DEV-FE-EUS2` | `10.245.16.0/22` | 1019 | フロントエンドまたは Web 層の VM |
| `DEV-APP-EUS2` | `10.245.20.0/22` | 1019 | アプリケーション層の VM |
| `DEV-DB-EUS2` | `10.245.24.0/23` | 507 | データベース VM |

#### <a name="subnets-in-the-east-us-2-production-network-vnet-prod-eus2"></a>`East US 2` の運用ネットワーク (`VNET-PROD-EUS2`) のサブネット

Azure IaaS コンポーネントは、運用ネットワークに配置されています。 各アプリケーション層には独自のサブネットがあります。 サブネットは開発用ネットワーク内のサブネットと一致し、ドメイン コントローラー用のサブネットが追加されています。

| Subnet | CIDR | アドレス | サブネット内 |
| --- | --- | --- | --- |
| `PROD-FE-EUS2` | `10.245.32.0/22` | 1019 | フロントエンドまたは Web 層の VM |
| `PROD-APP-EUS2` | `10.245.36.0/22` | 1019 | アプリケーション層の VM |
| `PROD-DB-EUS2` | `10.245.40.0/23` | 507 | データベース VM |
| `PROD-DC-EUS2` | `10.245.42.0/24` | 251 | ドメイン コントローラー VM |

![ハブ ネットワーク アーキテクチャの図。](./media/contoso-migration-infrastructure/azure-networks-eus2.png)

_図 20:ハブ ネットワーク アーキテクチャ_

#### <a name="virtual-networks-in-central-us-secondary-region"></a>`Central US` (セカンダリ リージョン) の仮想ネットワーク

`Central US` は Contoso のセカンダリ リージョンです。 そこでの Contoso によるネットワークの設計方法を次に示します。

- **ハブ:** `Central US` のハブ仮想ネットワークは、オンプレミスのデータセンターへのセカンダリ接続ポイントと見なされます。 `Central US` のスポーク仮想ネットワークは、必要に応じてワークロードを分離するために使用でき、他のスポークとは別に管理されます。

- **仮想ネットワーク:** `Central US` には、次の 2 つの仮想ネットワークが配置されます。
  - `VNET-PROD-CUS`:これは運用ネットワークであり、セカンダリ ハブと考えることができます。
  - `VNET-ASR-CUS`:この仮想ネットワークは、オンプレミスからのフェールオーバー後に VM が作成される場所、またはプライマリ リージョンからセカンダリ リージョンにフェールオーバーされる Azure VM のための場所として使用されます。 このネットワークは運用ネットワークに似ていますが、ドメイン コントローラーはありません。

  リージョン内の各仮想ネットワークは、重複のない独自のアドレス空間を使用します。 Contoso では NAT なしでルーティングを構成する予定です。

- **サブネット:** サブネットは、`East US 2` のものと同様に設計されます。

次の表は、`Central US` 内の仮想ネットワークをまとめたものです。

| 仮想ネットワーク | Range | ピア |
| --- | --- | --- |
| `VNET-HUB-CUS` | `10.250.0.0/20` | `VNET-HUB-EUS2`, `VNET-ASR-CUS`, `VNET-PROD-CUS` |
| `VNET-ASR-CUS` | `10.255.16.0/20` | `VNET-HUB-CUS`, `VNET-PROD-CUS` |
| `VNET-PROD-CUS` | `10.255.32.0/20` | `VNET-HUB-CUS`, `VNET-ASR-CUS`, `VNET-PROD-EUS2` |

![ペア リージョン内のハブ アンド スポーク モデルの図。](./media/contoso-migration-infrastructure/paired-hub-peer.png)

_図 21:ペア リージョン内のハブ アンド スポーク モデル_

#### <a name="subnets-in-the-central-us-hub-network-vnet-hub-cus"></a>`Central US` ハブ ネットワーク (`VNET-HUB-CUS`) のサブネット

| Subnet | CIDR | 使用可能な IP アドレス |
| --- | --- | --- |
| `IB-UntrustZone` | `10.250.0.0/24` | 251 |
| `IB-TrustZone` | `10.250.1.0/24` | 251 |
| `OB-UntrustZone` | `10.250.2.0/24` | 251 |
| `OB-TrustZone` | `10.250.3.0/24` | 251 |
| `GatewaySubnet` | `10.250.10.0/24` | 251 |

#### <a name="subnets-in-the-central-us-production-network-vnet-prod-cus"></a>`Central US` の運用ネットワーク (`VNET-PROD-CUS`) のサブネット

プライマリ リージョン (`East US 2`) の運用ネットワークと並行して、セカンダリ リージョン (`Central US`) に運用ネットワークがあります。

| Subnet | CIDR | アドレス | サブネット内 |
| --- | --- | --- | --- |
| `PROD-FE-CUS` | `10.255.32.0/22` | 1019 | フロントエンドまたは Web 層の VM |
| `PROD-APP-CUS` | `10.255.36.0/22` | 1019 | アプリケーション層の VM |
| `PROD-DB-CUS` | `10.255.40.0/23` | 507 | データベース VM |
| `PROD-DC-CUS` | `10.255.42.0/24` | 251 | ドメイン コントローラー VM |

#### <a name="subnets-in-the-central-us-failoverrecovery-network-vnet-asr-cus"></a>`Central US` のフェールオーバー (復旧) ネットワーク (`VNET-ASR-CUS`) 内のサブネット

`VNET-ASR-CUS` ネットワークは、リージョン間のフェールオーバーに使用されます。 リージョン間の Azure VM のレプリケートとフェールオーバーには、Site Recovery が使われます。 また、オンプレミスに残されており、ディザスター リカバリーのために Azure にフェールオーバーする保護されたワークロードのための Azure ネットワークに対する Contoso データセンターとしても機能します。

`VNET-ASR-CUS` は `East US 2` の運用仮想ネットワークと同じ基本サブネットですが、ドメイン コントローラー サブネットは必要ありません。

| Subnet | CIDR | アドレス | サブネット内 |
| --- | --- | --- | --- |
| `ASR-FE-CUS` | `10.255.16.0/22` | 1019 | フロントエンドまたは Web 層の VM |
| `ASR-APP-CUS` | `10.255.20.0/22` | 1019 | アプリケーション層の VM |
| `ASR-DB-CUS` | `10.255.24.0/23` | 507 | データベース VM |

![ハブ ネットワーク アーキテクチャの図。](./media/contoso-migration-infrastructure/azure-networks-cus.png)

_図 22:ハブ ネットワーク アーキテクチャ_

#### <a name="configure-peered-connections"></a>ピアリングされた接続を構成する

各リージョンのハブは、他のリージョンのハブと、そのハブ リージョン内のすべての仮想ネットワークにピアリングされます。 この構成により、ハブは通信することが可能となり、リージョン内のすべての仮想ネットワークを認識できます。 ピアリングでは、両側接続が作成されることに注意してください。 1 つ目の仮想ネットワークの開始側ピアからの接続と、2 つ目の仮想ネットワークの開始側ピアからの接続です。

ハイブリッド デプロイでは、ピア間を通過するトラフィックは、オンプレミスのデータセンターと Azure の間の VPN 接続から表示できる必要があります。 そのためには、ピアリングされた接続で特定の設定を使用する必要があります。 スポーク仮想ネットワークからハブを経由したオンプレミスのデータセンターへのすべての接続について、Contoso はトラフィックの転送と VPN ゲートウェイの横断を許可する必要があります。

##### <a name="domain-controller"></a>ドメイン コントローラー

`VNET-PROD-EUS2` ネットワーク内のドメイン コントローラーでは、`EUS2` ハブ/運用ネットワーク間のトラフィック フローと、オンプレミスへの VPN 接続経由のトラフィック フローの両方が必要です。 これを行うために、Contoso の管理者は以下を許可する必要があります。

1. ピアリングされた接続で、 **[転送されたトラフィックを許可する]** と **[ゲートウェイ転送を許可する]** を設定します。 この例では、これは `VNET-HUB-EUS2` から `VNET-PROD-EUS2` への接続になります。

    ![トラフィック転送とゲートウェイ転送を許可するチェック ボックスがオンになった画面のスクリーンショット。](./media/contoso-migration-infrastructure/peering1.png)

    _図 23:ピアリングされた接続_

2. ピアリングのもう一方の側 (`VNET-PROD-EUS2` から `VNET-HUB-EUS2` への接続) では、 **[転送されたトラフィックを許可する]** と **[リモート ゲートウェイを使用する]** を設定します。

    ![トラフィック転送とリモート ゲートウェイの使用を許可するチェック ボックスがオンになった画面のスクリーンショット。](./media/contoso-migration-infrastructure/peering2.png)

    _図 24:ピアリングされた接続_

3. オンプレミスでは、ローカル トラフィックを、VPN トンネルを経由して仮想ネットワークにルーティングする静的ルートを設定します。 Contoso から Azure への VPN トンネルを提供するゲートウェイで構成を行います。 静的ルートには、ルーティングとリモート アクセス サービス (RRAS) を使用します。

    ![静的ルートの選択画面を示すスクリーンショット。](./media/contoso-migration-infrastructure/peering3.png)

    _図 25:ピアリングされた接続_

##### <a name="production-networks"></a>運用ネットワーク

スポークされたピア ネットワークは、ハブを介して別のリージョンのスポークされたピア ネットワークを認識することはできません。 両方のリージョンの Contoso の運用ネットワークが相互に認識するには、Contoso の管理者は `VNET-PROD-EUS2` と `VENT-PROD-CUS` のピアリングされた直接接続を作成する必要があります。

![ピアリングされた直接接続の作成を表す図。](./media/contoso-migration-infrastructure/peering4.png)

_図 26: ピアリングされた直接接続の作成_

### <a name="set-up-dns"></a>DNS を設定する

仮想ネットワークにリソースを展開するときは、ドメイン名の解決に 2 つの選択肢があります。 Azure によって提供される名前解決を使用することも、解決用に DNS サーバーを提供することもできます。 どちらの名前解決方法を使用するかは、リソースが互いに通信するために必要な方法によって決まります。 Azure DNS サービスについての[詳細をご覧ください](/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#azure-provided-name-resolution)。

Contoso の管理者は、Azure DNS サービスはハイブリッド環境に適した選択ではないと判断しました。 代わりに、オンプレミスの DNS サーバーを使用します。 これらについて詳しく説明します。

- これはハイブリッド ネットワークであるため、オンプレミスと Azure のすべての VM は、正常に機能するために名前を解決できる必要があります。 つまり、すべての仮想ネットワークにカスタム DNS の設定を適用する必要があります。
- Contoso は現在、Contoso データセンターとブランチ オフィスにドメイン コントローラー (DC) をデプロイしています。 プライマリ DNS サーバーは、`contosodc1` (`172.16.0.10`) と `contosodc2` (`172.16.0.1`) です。
- 仮想ネットワークがデプロイされると、オンプレミスのドメイン コントローラーは、ネットワーク内の DNS サーバーとして構成されます。
- 仮想ネットワーク用に省略可能なカスタム DNS が指定されている場合、Azure 内の再帰的なリゾルバーの仮想 IP アドレス `168.63.129.16` をリストに追加する必要があります。 そのために、Contoso は各仮想ネットワークで DNS サーバーの設定を構成します。 たとえば、`VNET-HUB-EUS2` ネットワークのカスタム DNS 設定は次のようになります。

    ![カスタム DNS の構成を示すスクリーンショット。](./media/contoso-migration-infrastructure/custom-dns.png)

    _図 27: カスタム DNS_

オンプレミスのドメイン コントローラーに加え、Contoso は Azure ネットワークをサポートするために、4 つのドメイン コントローラー (リージョンごとに 2 つ) を実装します。

| リージョン | DC | 仮想ネットワーク | Subnet | IP アドレス |
| --- | --- | --- | --- | --- |
| `East US 2` | `contosodc3` | `VNET-PROD-EUS2` | `PROD-DC-EUS2` | `10.245.42.4` |
| `East US 2` | `contosodc4` | `VNET-PROD-EUS2` | `PROD-DC-EUS2` | `10.245.42.5` |
| `Central US` | `contosodc5` | `VNET-PROD-CUS` | `PROD-DC-CUS` | `10.255.42.4` |
| `Central US` | `contosodc6` | `VNET-PROD-CUS` | `PROD-DC-CUS` | `10.255.42.4` |

オンプレミスのドメイン コントローラーを展開した後、Contoso は、DNS サーバーの一覧に新しいドメイン コントローラーが含まれるように、両方のリージョンのネットワークで DNS の設定を更新する必要があります。

#### <a name="set-up-domain-controllers-in-azure"></a>Azure でドメイン コントローラーを設定する

ネットワークの設定を更新した後、Contoso の管理者は Azure でドメイン コントローラーを構築できます。

1. Azure portal で、新しい Windows Server VM を適切な仮想ネットワークにデプロイします。
2. 各場所に VM の[可用性セットを作成](/azure/virtual-machines/windows/tutorial-availability-sets)します。 可用性セットは、Azure ファブリックによって Azure リージョン内の異なるインフラストラクチャに VM が分離されることを保証します。 また、Contoso が利用する Azure 内の VM は、可用性セットによって、99.95% のサービス レベル アグリーメント (SLA) の対象となります。

    ![可用性セットの作成画面を示すスクリーンショット。](./media/contoso-migration-infrastructure/availability-group.png)

    _図 28: 可用性セット_

3. VM を展開した後、Contoso は VM に対してネットワーク インターフェイスを開きます。 プライベート IP アドレスを静的に設定し、有効なアドレスを指定します。

    ![VM ネットワーク インターフェイス接続を示すスクリーンショット。](./media/contoso-migration-infrastructure/vm-nic.png)

    _図 29: VM の NIC_

4. 新しいデータ ディスクを VM に接続します。 このディスクには、Active Directory データベースと SYSVOL 共有が含まれています。

   ディスクのサイズにより、サポートされる IOPS の値が決まります。 時間が経過して環境が拡大すると、ディスク サイズを増やすことが必要になる場合があります。

   > [!NOTE]
   > ホスト キャッシュのためにディスクを読み取り/書き込みに設定しないでください。 Active Directory データベースはこれをサポートしません。

   ![Active Directory ディスクを示すスクリーンショット。](./media/contoso-migration-infrastructure/ad-disk.png)

   _図 30: Active Directory ディスク_

5. ディスクを追加したら、リモート デスクトップ サービスを介して VM に接続し、サーバー マネージャーを開きます。

6. **[ファイル サービスと記憶域サービス]** で、新しいボリューム ウィザードを実行します。 ローカル VM でドライブに F: 以降の文字が割り当てられていることを確認します。

    ![新しいボリューム ウィザードを示すスクリーンショット。](./media/contoso-migration-infrastructure/volume-wizard.png)

    _図 31: 新しいボリューム ウィザード_

7. Contoso はサーバー マネージャーで **Active Directory Domain Services** ロールを追加します。 次に、ドメイン コントローラーとして VM を構成します。

    ![サーバー ロールの選択画面のスクリーンショット。](./media/contoso-migration-infrastructure/server-role.png)

    _図 32: サーバー ロールの追加_

8. VM を DC として構成し、再起動した後、DNS マネージャーを開き、Azure DNS リゾルバーをフォワーダーとして構成します。 これにより、DC は Azure DNS で解決できない DNS クエリを転送できます。

    ![DNS リゾルバをフォワーダーとして構成する画面のスクリーンショット。](./media/contoso-migration-infrastructure/dns-forwarder.png)

    _図 33: Azure DNS リゾルバーの構成_

9. 仮想ネットワーク リージョンの適切なドメイン コントローラーで、各仮想ネットワークのカスタム DNS 設定を更新します。 オンプレミスの DC を一覧に含めます。

### <a name="set-up-active-directory"></a>Active Directory のセットアップ

Active Directory はネットワークに関する重要なサービスであり、正しく構成する必要があります。 Contoso の管理者は、Contoso データセンターと `East US 2` および `Central US` リージョン用に Active Directory サイトを構築します。

1. データセンター サイト (`contoso-datacenter`) と共に、2 つの新しいサイト (`AZURE-EUS2`と `AZURE-CUS`) を作成します。
2. サイトを作成した後は、仮想ネットワークおよびデータセンターと一致するように、サイト内にサブネットを作成します。

    ![データセンターのサブネットの作成画面を示すスクリーンショット。](./media/contoso-migration-infrastructure/dc-subnets.png)

    _図 34: データセンターのサブネット_

3. 次に、2 つのサイト リンクを作成してすべてを接続します。 ドメイン コントローラーをそれぞれの場所に移動する必要があります。

    ![データセンターのリンクの作成画面を示すスクリーンショット。](./media/contoso-migration-infrastructure/dc-links.png)

    _図 35: データセンターのリンク_

4. Active Directory レプリケーション トポロジが完成していることを確認します。

    ![データセンターのレプリケーション トポロジを示すスクリーンショット。](./media/contoso-migration-infrastructure/ad-resolution.png)

    _図 36: データセンターのレプリケーション_

すべてが完了すると、ドメイン コントローラーとサイトの一覧が、オンプレミスの Active Directory 管理センターに表示されます。

![Active Directory 管理センターを示すスクリーンショット。](./media/contoso-migration-infrastructure/ad-center.png)

_図 37: Active Directory 管理センター_

## <a name="step-5-plan-for-governance"></a>手順 5:ガバナンスを計画する

Azure は、サービスと Azure プラットフォームに対してさまざまなガバナンス コントロールを提供します。 詳しくは、[Azure のガバナンスのオプション](/azure/governance/)に関するページをご覧ください。

Contoso は ID とアクセスの制御を構成しているので、ガバナンスとセキュリティの一部の面は既に導入しています。 大まかに言うと、次の 3 つの領域を考慮する必要があります。

- **ポリシー:** Azure Policy は、リソースに対してルールと効果を適用および強制します。これによって、リソースは企業の要件と SLA に準拠するようになります。
- **ロック:** Azure では、サブスクリプション、リソース グループ、その他のリソースをロックして、アクセス許可を持つユーザーだけがそれらを変更できるようにすることが可能です。
- **タグ:** タグを使って、リソースを制御、監査、および管理できます。 タグは、リソースにメタデータを関連付けて、リソースまたは所有者に関する情報を提供します。

### <a name="set-up-policies"></a>ポリシーを設定する

Azure Policy サービスは、ポリシー定義に準拠していないリソースをスキャンすることによって、リソースを評価します。 たとえば、特定の種類の VM だけを許可するポリシーや、リソースに特定のタグを付けることを要求するポリシーを使用できます。

ポリシーはポリシー定義を指定し、ポリシー割り当てはポリシーが適用される範囲を指定します。 範囲は、管理グループからリソース グループまでです。 [ポリシーを作成して管理する](/azure/governance/policy/tutorials/create-and-manage)方法をご確認ください。

Contoso は、2 つのポリシーを開始したいと考えています。 これには、リソースを `East US 2` リージョンと `Central US` リージョンにのみ確実にデプロイできるようにするポリシーが必要です。 また、承認された SKU だけに VM SKU を制限するポリシーも必要となります。 目的は、高価な VM SKU が使われないようにすることです。

#### <a name="limit-resources-to-regions"></a>リージョンにリソースを制限する

Contoso は、組み込みポリシー定義の **許可されている場所** を使って、リソースのリージョンを制限します。

1. Azure Portal で、 **[すべてのサービス]** を選択し、 **[ポリシー]** を検索します。
2. **[割り当て]**  >  **[ポリシーの割り当て]** を選択します。
3. ポリシーの一覧で、 **[許可されている場所]** を選びます。
4. **[スコープ]** を Azure サブスクリプションの名前に設定し、許可リストで 2 つのリージョンを選択します。

    ![ポリシーによって定義された許可されている場所を示すスクリーンショット。](./media/contoso-migration-infrastructure/policy-region.png)

    _図 38: ポリシーによって定義された許可されている場所_

5. 既定では、このポリシーは **[拒否]** に設定されています。 つまり、`East US 2` または `Central US` リージョンにないサブスクリプションでデプロイが開始された場合、デプロイは失敗します。 Contoso サブスクリプションのユーザーが `West US` でデプロイを設定しようとした場合、次のようになります。

    ![失敗したポリシーからのエラーを示すスクリーンショット。](./media/contoso-migration-infrastructure/policy-failed.png)

    _図 39: 失敗したポリシー_

#### <a name="allow-specific-vm-skus"></a>特定の VM SKU を許可する

Contoso は、組み込みポリシー定義の `Allow virtual machine SKUs` を使用して、サブスクリプションに作成できる VM の種類を制限します。

![SKU の選択を示すスクリーンショット。](./media/contoso-migration-infrastructure/policy-sku.png)

_図 40: ポリシー SKU_

#### <a name="check-policy-compliance"></a>ポリシーのコンプライアンスを確認する

ポリシーはすぐに有効になり、Contoso はリソースのコンプライアンス状況を確認できます。 Azure Portal で、 **[コンプライアンス]** リンクを選択します。 コンプライアンス ダッシュボードが表示されます。 ドリルダウンして詳細を表示することができます。

![コンプライアンス ダッシュボードを示すスクリーンショット。](./media/contoso-migration-infrastructure/policy-compliance.png)

_図 41: ポリシーへの準拠_

### <a name="set-up-locks"></a>ロックを設定する

Contoso では長い間、システムの管理に ITIL フレームワークを使用してきました。 このフレームワークの最も重要な側面の 1 つは変更制御であり、Contoso では、Azure の展開には必ず、変更制御が実装されるようにしたいと考えています。

Contoso は、[リソースをロック](/azure/azure-resource-manager/management/lock-resources)します。 すべての運用コンポーネントまたはフェールオーバー コンポーネントは、読み取り専用ロックを保持するリソース グループに含まれている必要があります。 つまり、運用項目を変更または削除するには、許可されているユーザーがロックを解除する必要があります。 非運用リソース グループは、`CanNotDelete` ロックを保持します。 つまり、許可されたユーザーはリソースの読み取りまたは変更をできますが、削除することはできません。

### <a name="set-up-tagging"></a>タグ付けの設定

追加されるリソースを追跡するため、Contoso にとっては適切な部門、顧客、環境にリソースを関連付けることの重要性が今後増していきます。 リソースと所有者に関する情報を提供するだけでなく、タグを使用すると、リソースを集計してグループ化し、そのデータをチャージバックのために使用できます。

Contoso は、役割や部署など、ビジネスにとって意味がある方法で Azure の資産を視覚化する必要があります。 タグを共有するために、リソースが同じリソース グループ内に格納されている必要はないことに注意してください。 Contoso は、だれもが同じタグを使用できるように、タグ分類を作成します。

| タグ名 | 値 |
| --- | --- |
| `CostCenter` | 12345:SAP の有効なコスト センターである必要があります。 |
| `BusinessUnit` | (SAP の) 部署の名前です。 `CostCenter` と一致します。 |
| `ApplicationTeam` | アプリケーションのサポートを所有しているチームのメール別名です。 |
| `CatalogName` | リソースでサポートされるサービス カタログに基づく、アプリケーションまたは `SharedServices` の名前です。 |
| `ServiceManager` | リソースの ITIL Service Manager のメール別名です。 |
| `COBPriority` | BCDR に対してビジネスによって設定された優先度です。 値は 1 から 5 です。 |
| `ENV` | 指定できる値は、`DEV`、`STG`、`PROD` です。開発、ステージング、運用を表します。 |

次に例を示します。

![Azure タグを示すスクリーンショット。](./media/contoso-migration-infrastructure/azure-tag.png)

_図 42: Azure タグ_

タグの作成後、Contoso は再び新しいポリシー定義と割り当てを作成して、組織全体で必要なタグの使用を強制します。

## <a name="step-6-consider-security"></a>手順 6:セキュリティを検討する

セキュリティはクラウドにおいて非常に重要であり、Azure ではさまざまなセキュリティ関連のツールと機能が提供されています。 これらを使用して、セキュリティで保護された Azure プラットフォーム上にセキュリティで保護されたソリューションを作成できます。 Azure のセキュリティの詳細については、「[信頼できるクラウド](https://azure.microsoft.com/overview/trusted-cloud/)」を参照してください。

Contoso はいくつかの主要な側面を考慮する必要があります。

- [Azure Security Center](/azure/security-center/security-center-introduction) は、ハイブリッド クラウド ワークロード全体にわたって、統合セキュリティ管理と Azure Advanced Threat Protection を提供します。 これを使用して、ワークロード全体へのセキュリティ ポリシーの適用、脅威にさらされる状態の軽減、攻撃の検出とその対応を行うことができます。
- [ネットワーク セキュリティ グループ (NSG)](/azure/virtual-network/network-security-groups-overview) は、Azure の仮想ネットワークに接続されているリソースへのネットワーク トラフィックを許可または拒否するセキュリティ規則の一覧に基づいて、ネットワーク トラフィックをフィルター処理します。
- [Azure Disk Encryption](/azure/security/fundamentals/encryption-atrest) は、Windows および Linux IaaS VM ディスクの暗号化を支援する機能です。

### <a name="work-with-the-azure-security-center"></a>Azure Security Center を使用する

Contoso は、新しいハイブリッド クラウド、具体的には Azure ワークロードのセキュリティ態勢をすばやく確認できる方法を探しています。 結果として、Contoso は Azure Security Center を実装して次の機能を利用することにしました。

- 一元化されたポリシー管理
- 継続的な評価
- 実行可能な推奨事項

#### <a name="centralize-policy-management"></a>一元化されたポリシー管理

Contoso では、一元化されたポリシー管理を使用して環境全体のセキュリティ ポリシーを一元的に管理することで、セキュリティ要件に確実に準拠する予定です。 すべての Azure リソースに適用されるポリシーを簡単かつ迅速に実装できます。

![セキュリティ ポリシーの選択画面を示すスクリーンショット。](./media/contoso-migration-infrastructure/security-policy.png)

_図 43: セキュリティ ポリシー_

#### <a name="assess-security"></a>セキュリティを評価する

Contoso は、マシン、ネットワーク、ストレージ、データ、アプリケーションのセキュリティを監視して潜在的なセキュリティの問題を検出する、継続的なセキュリティ評価を利用します。

Security Center は、コンピューティング、インフラストラクチャ、データに関する Contoso のリソースのセキュリティ状態を分析します。 また、Azure のアプリおよびサービスのセキュリティ状態も分析されます。 Contoso の運用チームは、継続的な評価を使用して、セキュリティ更新プログラムが不足しているシステムや公開されているネットワーク ポートなど、潜在的なセキュリティの問題を検出できます。

Contoso は、すべての VM が確実に保護されるようにしたいと考えています。 Security Center はこれを支援します。 VM の正常性を検証して、セキュリティの脆弱性が悪用される前にそれを修正するための、優先順位付けされた実行可能な推奨事項を提示します。

![仮想マシンの監視画面のスクリーンショット。](./media/contoso-migration-infrastructure/monitoring.png)

_図 44: 監視_

### <a name="work-with-nsgs"></a>NSG を使用する

ネットワーク セキュリティ グループを使って、仮想ネットワーク内のリソースへのネットワーク トラフィックを制限することができます。

ネットワーク セキュリティ グループには、ソースまたはターゲット IP アドレス、ポート、およびプロトコルを基に、受信/送信ネットワーク トラフィックを許可または拒否するセキュリティ規則の一覧が含まれています。 サブネットに規則を適用すると、サブネット内のすべてのリソースにセキュリティ規則が適用されます。 これには、ネットワーク インターフェイスに加えて、サブネットに展開されている Azure サービスのインスタンスが含まれます。

アプリケーション セキュリティ グループ (ASG) を使用すると、アプリケーションの構造の自然な拡張として、ネットワーク セキュリティを構成できます。 さらに、それらのグループに基づいて、VM をグループ化し、ネットワーク セキュリティ ポリシーを定義することができます。

Contoso は、明示的な IP アドレスを手動でメンテナンスしなくても、ASG を使用することで、大きなセキュリティ ポリシーを再利用することができます。 明示的な IP アドレスと複数の規則セットに伴う複雑さには、プラットフォームが対処してくれるので、組織はビジネス ロジックに専念することができます。 Contoso は、セキュリティ規則の送信元および宛先として ASG を指定できます。 セキュリティ ポリシーを定義したら、Contoso は VM を作成し、VM の NIC をグループに割り当てることができます。

Contoso は、NSG と ASG の両方を実装します。 Contoso は NSG の管理について気にかけています。 また、NSG の過剰使用や運用スタッフの作業の複雑化についても心配しています。 Contoso が行うことは次のとおりです。

- ハブ ネットワークのゲートウェイ サブネットを除き、すべてのサブネットを出入りするすべてのトラフィック (南北) が NSG ルールの対象となります。
- すべてのファイアウォールまたはドメイン コントローラーは、サブネットの NSG と NIC の NSG の両方によって保護されます。
- すべての運用アプリケーションには、ASG を適用します。

Contoso は、このセキュリティ構成がアプリケーションからどのように見えるかをモデル化しています。

![Contoso セキュリティ モデルの図。](./media/contoso-migration-infrastructure/asg.png)

_図 45: セキュリティ モデル_

ASG に関連付けられた NSG は最小限の特権で構成されており、許可されたパケットのみが、ネットワークの 1 つの部分から宛先にフローできます。

| アクション | 名前 | source | 移行先 | Port |
| --- | --- | --- | --- | --- |
| `Allow` | `AllowInternetToFE` | `VNET-HUB-EUS1`/`IB-TrustZone` | `APP1-FE` | 80、443 |
| `Allow` | `AllowWebToApp` | `APP1-FE` | `APP1-APP` | 80、443 |
| `Allow` | `AllowAppToDB` | `APP1-APP` | `APP1-DB` | 1433 |
| `Deny`  | `DenyAllInbound` | Any | Any | Any |

### <a name="encrypt-data"></a>データを暗号化する

サブスクリプションでディスク暗号化キーとシークレットを制御および管理しやすくするために、Azure Disk Encryption は Azure Key Vault と統合されています。 VM ディスク上のすべてのデータが、Azure Storage での保存時に確実に暗号化されます。

Contoso は、特定の VM で暗号化を必要とすることを決定しました。 Contoso は、顧客データ、機密データ、個人データを含む VM に暗号化を適用します。

## <a name="conclusion"></a>まとめ

この記事では、Contoso は、Azure インフラストラクチャと Azure サブスクリプションのポリシー、ハイブリッド ID、ディザスター リカバリー、ネットワーク、ガバナンス、セキュリティに対するインフラストラクチャ ポリシーを設定しました。

クラウドの移行には、ここで実行したすべての手順が必要となるわけではありません。 この例では、Contoso は、すべての種類の移行を処理でき、その一方で安全性、回復性、拡張性を備えたネットワーク インフラストラクチャを計画しました。

## <a name="next-steps"></a>次のステップ

Azure インフラストラクチャを設定したら、Contoso は、クラウドへのワークロードの移行をいつでも開始することができます。 このサンプル インフラストラクチャを移行ターゲットとして使用したシナリオの選択については、[移行のパターンと例の概要](./contoso-migration-overview.md#windows-server-workloads)に関するセクションを参照してください。
