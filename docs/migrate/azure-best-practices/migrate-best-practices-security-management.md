---
title: Azure に移行されたワークロードのセキュリティ保護と管理のためのベスト プラクティス
description: Azure 向けのクラウド導入フレームワークを使用し、移行されたワークロードの運用、管理、およびセキュリティ保護のベスト プラクティスについて説明します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 07/01/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: migrate
ms.openlocfilehash: 35f078aeb04c6fda78470e33e27f332f67bdaeee
ms.sourcegitcommit: 57b757759b676a22f13311640b8856557df36581
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/20/2020
ms.locfileid: "94997473"
---
<!-- docutune:casing "Update Management" -->
<!-- cSpell:ignore FIPS SIEM majeure NSGs -->

# <a name="best-practices-to-secure-and-manage-workloads-migrated-to-azure"></a>Azure に移行されたワークロードのセキュリティ保護と管理のためのベスト プラクティス

移行を計画して設計するときは、移行自体について考えるだけでなく、移行後の Azure でのセキュリティと管理モデルを検討する必要があります。 この記事では、移行後に Azure のデプロイをセキュリティで保護するための計画とベスト プラクティスについて説明します。 また、最適なレベルでデプロイが実行され続けるようにするための継続的なタスクについても説明します。

> [!IMPORTANT]
> この記事で説明するベスト プラクティスと考え方は、Azure プラットフォームと、記事の執筆時点で利用できるサービスの機能に基づいています。 特徴や機能は時間の経過と共に変化します。

## <a name="secure-migrated-workloads"></a>移行されるワークロードをセキュリティで保護する

移行後の最も重要なタスクは、移行されたワークロードを内部および外部の脅威からセキュリティで保護することです。 それを実現するには、以下のベスト プラクティスが役に立ちます。

- Azure Security Center によって提供される監視、評価、およびレコメンデーションを使用する方法について説明します。
- Azure でのデータの暗号化に関するベスト プラクティスを理解します。
- マルウェアや悪意のある攻撃から VM を保護します。
- 移行された Web アプリで機密情報のセキュリティ保護を維持します。
- 移行後に Azure サブスクリプションとリソースにアクセスできるユーザーを確認します。
- Azure の監査ログとセキュリティ ログを定期的に確認します。
- Azure で提供されている高度なセキュリティ機能を理解して評価します。

これらのベスト プラクティスについては、以降のセクションで詳しく説明します。

## <a name="best-practice-follow-azure-security-center-recommendations"></a>ベスト プラクティス:Azure Security Center の推奨事項に従う

Azure テナント管理者は、攻撃からワークロードを保護するセキュリティ機能を有効にする必要があります。 Azure Security Center では、統合されたセキュリティ管理が提供されます。 Security Center からは、ワークロード全体へのセキュリティ ポリシーの適用、脅威にさらされる機会の制限、攻撃の検出とその対応を行うことができます。 Security Center では、Azure テナント全体のリソースと構成が分析され、次のようにセキュリティに関する推奨事項が示されます。

- **一元化されたポリシー管理:** すべてのハイブリッド クラウド ワークロードのセキュリティ ポリシーを一元的に管理することで、会社や規制のセキュリティ要件に確実に準拠できます。
- **継続的なセキュリティ評価:** マシン、ネットワーク、ストレージとデータ サービス、アプリケーションのセキュリティに対する姿勢を監視して、潜在的なセキュリティの問題を検出します。
- **実行可能な推奨事項:** 優先順位が付けられた実行可能なセキュリティの推奨事項を使って、攻撃者が悪用する前にセキュリティの脆弱性を修復します。
- **優先順位が付けられたアラートとインシデント:** 優先順位が付けられたセキュリティ アラートとインシデントにより、最も重大な脅威にまず重点を置きます。

Azure Security Center には、評価と推奨事項に加え、特定のリソースに対して有効にできるセキュリティ機能が用意されています。

- **Just-In-Time (JIT) アクセス。** Azure VM の管理ポートに対する制御されたアクセスである Just-In-Time を使用して、ネットワークの攻撃対象領域を減らします。
  - VM の RDP ポート 3389 をインターネットに対して開いた場合、VM は悪意のあるアクターによるアクティビティに継続的にさらされます。 Azure の IP アドレスはよく知られており、ハッカーは開いているポート 3389 での攻撃を常に探っています。
  - Just-In-Time では、ネットワーク セキュリティ グループ (NSG) と、特定のポートが開いている時間の長さを制限する受信規則が使用されます。
  - Just-In-Time アクセスを有効にすると、Security Center によって、ユーザーが VM に対してロールベースのアクセス制御 (RBAC) の書き込みアクセス許可を持っていることが確認されます。 さらに、ユーザーが VM に接続する方法に関する規則を指定できます。 アクセス許可に問題がない場合、アクセス要求は承認され、Security Center によって、選択したポートへの受信トラフィックを指定した時間だけ許可するように NSG が構成されます。 その時間が経過すると、NSG は以前の状態に戻ります。
- **適応型アプリケーション制御。** 動的な許可リストを使用して VM で実行されるアプリケーションを制御することにより、ソフトウェアやマルウェアを VM から遠ざけます。
  - 適応型アプリケーション制御を使用すると、アプリケーションを承認し、悪意のあるユーザーや管理者によって未承認または審査中のソフトウェア アプリケーションが VM にインストールされるのを防ぐことができます。
    - 悪意のあるアプリケーションを実行する試みをブロックまたは警告し、不要なまたは悪意のあるアプリケーションを回避し、組織のアプリケーション セキュリティ ポリシーに確実に準拠することができます。
- **ファイルの整合性の監視。** VM で実行されているファイルの整合性を確認します。
  - VM の問題が発生するソフトウェアをインストールする必要はありません。 システム ファイルを変更しても、VM の障害やパフォーマンスの低下が発生する場合があります。 ファイルの整合性の監視では、システム ファイルおよびレジストリ設定の変更の有無が調査され、何らかの更新が行われた場合は通知されます。
  - Security Center では、監視する必要のあるファイルが推奨されます。

**詳細情報:**

- [Azure Security Center](/azure/security-center/security-center-intro) の詳細について学習します。
- [Just-In-Time VM アクセス](/azure/security-center/security-center-just-in-time)の詳細を確認する。
- [適応型アプリケーション制御の適用](/azure/security-center/security-center-adaptive-application)について学習する。
- ファイルの整合性の監視を[始める](/azure/security-center/security-center-file-integrity-monitoring)。

## <a name="best-practice-encrypt-data"></a>ベスト プラクティス:データを暗号化する

暗号化は、Azure のセキュリティ プラクティスの重要な部分です。 すべてのレベルで暗号化を有効にすることは、承認されていないユーザーが転送中や保存時の機密データにアクセスするのを防ぐのに役立ちます。

### <a name="encryption-for-infrastructure-as-a-service-iaas"></a>サービスとしてのインフラストラクチャ (IaaS) の暗号化

- **仮想マシン:** VM の場合は、Azure Disk Encryption を使用して、Windows および Linux の IaaS VM ディスクを暗号化できます。
  - Azure Disk Encryption では、BitLocker (Windows の場合) および DM-Crypt (Linux の場合) を使用して、オペレーティング システムとデータ ディスクのボリューム暗号化を行います。
  - Azure によって作成された暗号化キーを使用するか、独自の暗号化キーを提供し、Azure Key Vault で保護することができます。
  - Azure Disk Encryption では、保存時 (ディスク上) および VM の起動中に IaaS VM データがセキュリティで保護されます。
    - Azure Security Center では、暗号化されていない VM があると警告を受け取ります。
- **ストレージ:** Azure Storage に保存されている格納データを保護します。
  - Azure ストレージ アカウントの格納データは、Microsoft が生成する、FIPS 140-2 準拠の AES キーを使用して暗号化できます。また、ユーザー独自のキーを使用することもできます。
  - Azure Storage 暗号化は、新規と既存のすべてのストレージ アカウントに対して有効になっており、無効にすることはできません。

### <a name="encryption-for-platform-as-a-service-paas"></a>サービスとしてのプラットフォーム (PaaS) の暗号化

ユーザーが自分の VM とインフラストラクチャを管理する IaaS とは異なり、PaaS モデルでは、プラットフォームとインフラストラクチャがプロバイダーによって管理されます。 ユーザーはコア アプリケーションのロジックと機能に専念できます。 PaaS サービスの種類は非常に多いので、セキュリティに関する評価はサービスごとに個別に行います。 例として、Azure SQL Database の暗号化を有効にする方法を見てみましょう。

- **Always Encrypted:** SQL Server Management Studio で Always Encrypted ウィザードを使用して、保存データを保護します。
  - Always Encrypted キーを作成して、個々の列のデータを暗号化します。
  - Always Encrypted キーは、データベースのメタデータに暗号化して格納するか、または Azure Key Vault などの信頼できるキー ストアに格納することができます。
  - ほとんどの場合、この機能を使用するには、アプリケーションを変更する必要があります。
- **透過的なデータ暗号化 (TDE):** 保存時のデータベース、関連付けられたバックアップ、トランザクション ログ ファイルをリアルタイムで暗号化および暗号化解除することにより、Azure SQL Database を保護します。
  - TDE を使用すると、アプリケーション層を変更することなく暗号化アクティビティを実行できます。
  - TDE では、Microsoft によって提供される暗号化キーを使用するか、独自のキーを持ち込むことができます。

**詳細情報:**

- [仮想マシンおよび仮想マシン スケール セット向けの Azure Disk Encryption](/azure/security/fundamentals/azure-disk-encryption-vms-vmss) について学習する。
- [Windows VM 用の Azure Disk Encryption ](/azure/virtual-machines/windows/disk-encryption-overview)を有効にする。
- [保存データに対する Azure Storage 暗号化](/azure/storage/common/storage-service-encryption)について学習する。
- [Always Encrypted の概要](/azure/sql-database/sql-database-always-encrypted-azure-key-vault)を読む。
- [SQL Database および Azure Synapse の Transparent Data Encryption](/azure/sql-database/transparent-data-encryption-azure-sql) に関する記事を読む。
- [カスタマー マネージド キーを使用した Azure SQL Database Transparent Data Encryption](/azure/sql-database/transparent-data-encryption-byok-azure-sql) について学習する。

## <a name="best-practice-protect-vms-with-antimalware"></a>ベスト プラクティス:マルウェア対策で VM を保護する

特に、Azure に移行された古い VM には、適切なレベルのマルウェア対策がインストールされていない可能性があります。 Azure では、ウイルス、スパイウェア、その他のマルウェアからの VM の保護に役立つ無料のエンドポイント ソリューションが提供されています。

- Azure Cloud Services と仮想マシン向けの Microsoft Antimalware では、既知の悪意のあるソフトウェアや望ましくないソフトウェアが自身をインストールしようとしたときにアラートが生成されます。
- これは、ユーザーの介入なしにバックグラウンドで実行される単一のエージェント ソリューションです。
- Azure Security Center では、エンドポイントの保護が実行されていない VM を簡単に識別し、必要に応じて Microsoft マルウェア対策をインストールできます。

  ![VM 向けのマルウェア対策のスクリーンショット。](./media/migrate-best-practices-security-management/antimalware.png)
  "_図 1: VM 向けのマルウェア対策。_ "

**詳細情報:**

- [Azure 向け Microsoft Antimalware](/azure/security/fundamentals/antimalware) を確認する。

## <a name="best-practice-secure-web-apps"></a>ベスト プラクティス:Web アプリをセキュリティで保護する

移行された Web アプリではいくつかの問題が発生します。

- ほとんどの従来の Web アプリケーションでは、構成ファイル内に機密情報が存在する傾向があります。 このような情報を含むファイルでは、アプリケーションがバックアップされるとき、またはアプリケーション コードがソース管理にチェックインされたりソース管理からチェックアウトされたりするときに、セキュリティの問題が発生することがあります。
- VM 内に存在する Web アプリを移行する場合は、たいてい、オンプレミス ネットワークやファイアウォールで保護された環境から、インターネットに接続された環境にそのマシンを移動します。 オンプレミスの保護リソースと同じ処理を行うソリューションが設定されていることを確認します。

Azure では、次のソリューションを提供しています。

- **Azure Key Vault:** 現在、Web アプリの開発者は、これらのファイルから機密情報がリークされない手段を講じています。 情報をセキュリティで保護する 1 つの方法は、情報をファイルから抽出し、Azure Key Vault に格納することです。
  - Key Vault を使用して、アプリケーション シークレットのストレージを集中化し、その配布を制御できます。 また、アプリケーション ファイルにセキュリティ情報を格納する必要がなくなります。
  - アプリケーションでは、URI を使用してコンテナー内の情報に安全にアクセスできます。カスタム コードは必要ありません。
  - Azure Key Vault を使用すると、Azure のセキュリティ制御を使用してアクセスをロックダウンし、キーの更新をシームレスに実装できます。 Microsoft がユーザーのデータを参照したり抽出したりすることはありません。
- **Power Apps 向けの App Service Environment:** 移行するアプリで追加の保護が必要な場合は、App Service Environment と Web アプリケーション ファイアウォールを追加してアプリケーション リソースを保護することを検討してください。
  - App Service Environment により、Windows や Linux の Web アプリ、Docker コンテナー、モバイル アプリ、関数アプリなどのアプリケーションを実行するための完全に分離された専用の環境が提供されます。
  - これは、非常に大規模なアプリケーション、分離とセキュリティ保護されたネットワーク アクセスを必要とするアプリケーション、またはメモリ使用率が高いアプリケーションにとって有用です。
- **Web アプリケーション ファイアウォール:** これは Azure Application Gateway の機能であり、Web アプリに対する一元的な保護を提供します。
  - Web アプリを保護するためにバックエンドのコードを変更する必要はありません。
  - Application Gateway の後方にある複数の Web アプリが同時に保護されます。
  - Azure Monitor を使用して、Web アプリケーション ファイアウォールを監視できます。 Web アプリケーション ファイアウォールは Azure Security Center に統合されています。

  ![Azure Key Vault とセキュリティで保護された Web アプリの図。](./media/migrate-best-practices-security-management/web-apps.png)
  "_図 2: Azure Key Vault。_ "

**詳細情報:**

- [Azure Key Vault の概要](/azure/key-vault/general/overview)を読む。
- [Web アプリケーション ファイアウォール](/azure/application-gateway/waf-overview)について学習する。
- [App Service Environment の概要](/azure/app-service/environment/intro)を読む。
- [Key Vault からシークレットを読み取るように Web アプリを構成する](/azure/key-vault/tutorial-web-application-keyvault)方法を学習する。

## <a name="best-practice-review-subscriptions-and-resource-permissions"></a>ベスト プラクティス:サブスクリプションとリソースのアクセス許可を確認する

ワークロードを移行して Azure で実行すると、ワークロードのアクセス権を持つスタッフはあちこち移動するようになります。 セキュリティ チームは、定期的に Azure テナントとリソース グループへのアクセスを確認する必要があります。 Azure には ID 管理とアクセス制御のセキュリティのためのオファリングがあり、その中には Azure リソースにアクセスするためのアクセス許可を承認するロールベースのアクセス制御 (RBAC) も含まれます。

- RBAC では、セキュリティ プリンシパルに対してアクセス許可が割り当てられます。 セキュリティ プリンシパルは、ユーザー、グループ (ユーザーのセット)、サービス プリンシパル (アプリケーションとサービスによって使用される ID)、マネージド ID (Azure によって自動的に管理される Azure Active Directory の ID) を表します。
- RBAC を使用すると、所有者、共同作成者、閲覧者などのロールと、そのロールで実行できる操作を定義するロール定義 (アクセス許可のコレクション) を、セキュリティ プリンシパルに割り当てることができます。
- また、RBAC ではロールの境界を設定するスコープを設定できます。 スコープは、管理グループ、サブスクリプション、リソース グループ、リソースなどのさまざまなレベルで設定できます
- Azure へのアクセス権を持つ管理者が許可されたリソースにのみアクセスできることを確認します。 Azure で定義済みのロールの細かさが十分でない場合は、カスタム ロールを作成し、アクセス許可を分離して制限できます。

Azure へのアクセス権を持つ管理者が許可されたリソースにのみアクセスできることを確認します。 Azure で定義済みのロールの細かさが十分でない場合は、カスタム ロールを作成し、アクセス許可を分離して制限できます。

  ![アクセスの制御のスクリーンショット。](./media/migrate-best-practices-security-management/subscription.png)
  "_図 3:アクセスの制御。_ "

**詳細情報:**

- [Azure のロールベースのアクセス制御 (RBAC)](/azure/role-based-access-control/overview) について学習する。
- [RBAC と Azure portal](/azure/role-based-access-control/role-assignments-portal) を使用したアクセスの管理について学習する。
- [カスタム ロール](/azure/role-based-access-control/custom-roles)について学習する。

## <a name="best-practice-review-audit-and-security-logs"></a>ベスト プラクティス:監査とセキュリティ ログを確認する

Azure Active Directory (Azure AD) では、Azure Monitor に表示されるアクティビティ ログが提供されています。 ログでは、Azure テナントで実行された操作、操作が発生した日時、操作を実行したユーザーがキャプチャされます。

- 監査ログでは、テナントでのタスクの履歴が示されます。 サインイン アクティビティ ログでは、タスクを実行したユーザーが示されます。
- セキュリティ レポートに対するアクセスは、Azure AD のライセンスによって異なります。 Free および Basic ライセンスを使用している場合は、危険なユーザーとサインインの一覧が表示されます。Premium ライセンスを使用している場合は、基になるイベント情報が表示されます。
- アクティビティ ログは、長期的な保持とデータ分析のためにさまざまなエンドポイントにルーティングできます。
- ログの確認を慣習的に行うようにするか、またはセキュリティ情報およびイベント管理 (SIEM) ツールを統合して異常が自動的に確認されるようにします。 Premium ライセンスを使用していない場合は、ユーザーが自分で、または SIEM システムを使用して、さまざまな分析を行う必要があります。 分析には、危険なサインインやイベント、およびその他のユーザー攻撃パターンを探すことが含まれます。

  ![Azure AD のユーザーとグループのスクリーンショット。](./media/migrate-best-practices-security-management/azure-ad.png)
  "_図 4: Azure AD のユーザーとグループ。_ "

**詳細情報:**

- [Azure Monitor の Azure AD アクティビティ ログ](/azure/active-directory/reports-monitoring/concept-activity-logs-azure-monitor)について学習する。
- [Azure AD ポータルでアクティビティ レポートを監査する](/azure/active-directory/reports-monitoring/concept-audit-logs)方法を学習する。

## <a name="best-practice-evaluate-other-security-features"></a>ベスト プラクティス:他のセキュリティ機能を評価する

Azure では、高度なセキュリティ オプションを提供するセキュリティ機能が他にも用意されています。 次のベスト プラクティスの一部を実行するには、アドオン ライセンスと Premium オプションが必要です。

- **Azure AD 管理単位 (AU) を実装する。** サポート スタッフへの管理責務の委任は、基本的な Azure アクセス制御だけでは複雑な場合があります。 Azure AD 内のすべてのグループを管理できるアクセス権をサポート スタッフに与えるのは、組織のセキュリティにとって理想的な方法ではない可能性があります。 AU を使用すると、オンプレミスの組織単位 (OU) と同様の方法で、コンテナーに Azure リソースを分離することができます。 AU を使用するには、AU の管理者に Premium Azure AD ライセンスが必要です。 詳細については、[Azure Active Directory での管理単位の管理](/azure/active-directory/users-groups-roles/directory-administrative-units)に関するページをご覧ください。
- **多要素認証を使用する。** Premium Azure AD ライセンスがある場合は、管理者アカウントに対して多要素認証を有効にして適用できます。 フィッシングは、アカウントの資格情報を侵害する最も一般的な方法です。 悪意のあるアクターが管理者アカウントの資格情報を手に入れると、すべてのリソース グループの削除など、影響範囲の広いアクションを阻止することはできません。 多要素認証は、電子メール、認証アプリ、携帯電話のテキスト メッセージなどのさまざまな方法で確立できます。 管理者は、最も影響が少ないオプションを選択できます。 多要素認証では、脅威分析と条件付きアクセス ポリシーと統合して、多要素認証チャレンジの応答をランダムに要求します。 詳しくは、[セキュリティ ガイダンス](/azure/active-directory/authentication/multi-factor-authentication-security-best-practices)と[多要素認証の設定方法](/azure/active-directory/authentication/multi-factor-authentication-security-best-practices)に関する記事をご覧ください。
- **条件付きアクセスを実装する。** ほとんどの中小規模の組織では、多くの場合、Azure 管理者とサポート チームは地理的に 1 つの場所に配置されます。 この場合、ほとんどのサインインは同じ地域から行われます。 これらの場所の IP アドレスが非常に静的な場合、これらの地域以外から管理者のサインインが見えてはならないのは当然のことです。 離れた場所にいる不正なアクターが管理者の資格情報を侵害した場合でも、条件付きアクセスと多要素認証の組み合わせのようなセキュリティ機能を実装することで、離れた場所からのサインインを防ぐことができます。 これにより、ランダムな IP アドレスで偽装された場所からのサインインを防止することもできます。 [条件付きアクセス](/azure/active-directory/conditional-access/overview)についての詳細を学習し、Azure AD での条件付きアクセスの[ベスト プラクティスを確認](/azure/active-directory/conditional-access/best-practices)してください。
- **エンタープライズ アプリケーションのアクセス許可を確認する。** 時間が経つと、管理者は組織に与える影響を知らずに Microsoft やサード パーティのリンクを選択するようになります。 リンクをクリックすると、Azure アプリにアクセス許可を割り当てる同意画面が表示される場合があります。 これにより、Azure AD データを読み取るためのアクセスや、Azure サブスクリプション全体を管理するためのフル アクセスが許可される場合があります。 管理者やユーザーに Azure リソースへのアクセスが許可されるアプリケーションを定期的に確認する必要があります。 このようなアプリケーションには、必要なアクセス許可のみを確保してください。 さらに、四半期または半年ごとに、アプリケーション ページへのリンクを含むメールをユーザーに送り、組織データへのアクセスが許可されているアプリケーションの存在を認識させることができます。 詳細については、「[アプリケーション リストに予期しないアプリケーションがある](/azure/active-directory/manage-apps/application-types)」と、Azure AD でアプリケーションの割り当てを[制御する方法](/azure/active-directory/manage-apps/remove-user-or-group-access-portal)に関するページを参照してください。

## <a name="managed-migrated-workloads"></a>移行されるワークロードを管理する

以降のセクションでは、次のような Azure の管理に関してお勧めするいくつかのベスト プラクティスについて説明します。

- スマートな名前付け、偶発的な削除の防止、リソースのアクセス許可の管理、効果的なリソースのタグ付けなど、Azure のリソース グループとリソースに関するベスト プラクティスです。
- デプロイ環境の構築と管理のためのブループリントの使用に関する概要を説明します。
- 移行後のデプロイを作成するときに参考にするサンプルの Azure アーキテクチャを確認します。
- 複数のサブスクリプションがある場合、それらを管理グループにまとめて、それらのグループにガバナンス設定を適用できます。
- Azure リソースにコンプライアンス ポリシーを適用します。
- 障害が発生したときにデータの安全、環境の回復性、リソースの稼働状態を維持するための、事業継続とディザスター リカバリー (BCDR) 戦略を作成します。
- 回復性と高可用性のために VM を可用性グループにグループ化します。 VM のディスクとストレージの管理を容易にするため、マネージド ディスクを使用します。
- Azure リソースの診断ログを有効にし、プロアクティブなトラブルシューティングのためのアラートとプレイブックを作成し、デプロイの正常性と状態の統一ビューのために Azure ダッシュボードを使用します。
- Azure のサポート プランとその実装方法を理解し、VM を最新の状態に維持するためのベスト プラクティスを把握して、プロセスを変更管理に組み込みます。

## <a name="best-practice-name-resource-groups"></a>ベスト プラクティス:リソース グループに名前を付ける

管理者とサポート チームのメンバーが簡単に認識できるように、リソース グループには必ずわかりやすい名前を付けます。 これにより、生産性と効率を大幅に向上させることができます。

Azure AD Connect を使用してオンプレミスの Active Directory を Azure AD に同期している場合は、オンプレミスのセキュリティ グループの名前を Azure でのリソース グループの名前と一致させることを検討してください。

  ![リソース グループの名前付けのスクリーンショット。](./media/migrate-best-practices-security-management/naming.png)
  _図 5:リソース グループの名前付け。_ "

**詳細情報:**

- [推奨される名前付け規則](../../ready/azure-best-practices/naming-and-tagging.md)について学習する。

## <a name="best-practice-implement-delete-locks-for-resource-groups"></a>ベスト プラクティス:リソース グループの削除ロックを実装する

最も困るのは、誤って削除したためにリソース グループが消えてしまうことです。 このようなことが起きないように、削除ロックを実装することをお勧めします。

  ![削除ロックのスクリーンショット。](./media/migrate-best-practices-security-management/locks.png)
  _図 6:削除ロック。_ "

**詳細情報:**

- [予期しない変更を防ぐためのリソースのロック](/azure/azure-resource-manager/resource-group-lock-resources)について学習する。

## <a name="best-practice-understand-resource-access-permissions"></a>ベスト プラクティス:リソースのアクセス許可を理解する

サブスクリプションの所有者は、サブスクリプション内に存在するすべてのリソース グループとリソースにアクセスできます。

- この重要な割り当てにユーザーを追加するときは慎重に行います。 この種のアクセス許可の予期しない影響を理解することは、環境の安全と安定を維持するために重要なことです。
- リソースを適切なリソース グループに必ず配置します。
  - ライフサイクルが似ているリソースをまとめます。 リソース グループ全体を削除する必要があるときに、リソースを移動する必要がないようにするのが理想的です。
  - 機能またはワークロードをサポートするリソースは、管理を簡略化するために一緒に配置する必要があります。

**詳細情報:**

- [サブスクリプションとリソース グループの編成](https://azure.microsoft.com/blog/organizing-subscriptions-and-resource-groups-within-the-enterprise)について学習する。

## <a name="best-practice-tag-resources-effectively"></a>ベスト プラクティス:リソースに効果的なタグを付ける

多くの場合、リソースに関連したリソース グループ名を使用するだけでは、内部課金やサブスクリプション内での管理などのメカニズムを効果的に実装するためのメタデータとして十分ではありません。

- ベスト プラクティスとして、Azure タグを使用して、クエリやレポートの基盤として使用できる有用なメタデータを追加することをお勧めします。
- タグを使用すると、定義したプロパティでリソースを論理的に整理する手段が提供されます。 タグは、リソース グループに、または直接リソースに適用できます。
- リソース グループまたは個々のリソースにタグを適用することができます。 リソース グループのタグは、グループ内のリソースによって継承されません。
- PowerShell または Azure Automation を使用してタグ付けを自動化することも、グループやリソースに個別にタグを付けることもできます。
- 要求および変更管理システムを導入している場合は、要求の情報を使用して会社固有のリソース タグを簡単に設定できます。

  ![タグ付けのスクリーンショット。](./media/migrate-best-practices-security-management/tagging.png)
  "_図 7: タグ付け。_ "

**詳細情報:**

- [タグ付けとタグの制限事項](/azure/azure-resource-manager/management/tag-resources)について学習する。
- [タグ付けを設定し、リソース グループからリソースにタグを適用するための PowerShell と CLI の例](/azure/azure-resource-manager/management/tag-resources#powershell)を確認する。
- Azure のタグ付けのベスト プラクティスを[読む](https://www.azurefieldnotes.com/2016/07/18/azure-resource-tagging-best-practices)。

## <a name="best-practice-implement-blueprints"></a>ベスト プラクティス:ブループリントを実装する

エンジニアやアーキテクトがブループリントを使用してプロジェクトの設計パラメーターの概略を示すのと同じように、クラウド アーキテクトや中央の IT グループは Azure Blueprints サービスを使用して、反復可能な一連の Azure リソースを定義できます。 これは、組織の標準、パターン、および要件を実装して遵守するうえで役立ちます。 開発チームは、Azure Blueprints を使用して、組織のコンプライアンス要件を満たす新しい環境を迅速に構築および作成できます。 これらの新しい環境にはネットワークなどの一連の組み込みコンポーネントが備わっているため、開発とデリバリーが迅速化されます。

- ブループリントを使用して、リソース グループのデプロイ、Azure Resource Manager テンプレート、およびポリシーとロールの割り当てを調整します。
- ブループリントは、グローバルに分散されたサービスである Azure Cosmos DB に格納されます。 Blueprint オブジェクトは複数の Azure リージョンにレプリケートされます。 ブループリントによってどのリージョンにリソースがデプロイされても、レプリケーションにより、ブループリントに対するアクセスの低遅延、高可用性、一貫性が実現されます。

**詳細情報:**

- ブループリントについて[読む](/azure/governance/blueprints/overview)。
- [医療分野で AI を促進するためのブループリントの例](https://azure.microsoft.com/blog/customizing-azure-blueprints-to-accelerate-ai-in-healthcare)を確認する。

## <a name="best-practice-review-azure-reference-architectures"></a>ベスト プラクティス:Azure の参照アーキテクチャを確認する

Azure においてセキュリティ保護されたスケーラブルで管理しやすいワークロードを構築することは、容易でない場合があります。 絶え間ない変更のため、最適な環境になるようにさまざまな機能を最新の状態に維持するのは難しいことがあります。 参考になる参照を用意すると、ワークロードを設計および移行するときに役立つ可能性があります。 Azure および Azure パートナーは、さまざまな種類の環境用に複数のサンプル参照アーキテクチャを構築してきました。 これらのサンプルは、参考にして基にできるアイデアを提供するように設計されています。

参照アーキテクチャはシナリオ別に用意されています。 それには、ベスト プラクティスと、管理、可用性、スケーラビリティ、セキュリティに関するアドバイスが含まれます。 App Service Environment を使用することで、Windows や Linux の Web アプリ、Docker コンテナー、モバイル アプリ、関数などのアプリケーションを実行するための完全に分離された専用の環境が提供されます。 App Service では、セキュリティ、負荷分散、自動スケーリング、自動管理などの Azure の機能が、アプリケーションに追加されます。 Azure DevOps と GitHub からの継続的デプロイ、パッケージ管理、ステージング環境、カスタム ドメイン、SSL 証明書など、DevOps の機能を利用することもできます。 App Service は、分離とセキュリティ保護されたネットワーク アクセスを必要とするアプリケーションや、大量のメモリおよびスケーリングする必要があるその他のリソースを使用するアプリケーションに役立ちます。

**詳細情報:**

- [Azure リファレンス アーキテクチャ](/azure/architecture/reference-architectures)について学習する。
- [Azure のシナリオ例](/azure/architecture/example-scenario)を確認する。

## <a name="best-practice-manage-resources-with-azure-management-groups"></a>ベスト プラクティス:Azure 管理グループを使用してリソースを管理する

組織に複数のサブスクリプションがある場合は、それらのアクセス、ポリシー、コンプライアンスを管理する必要があります。 Azure 管理グループの範囲は、サブスクリプションを上回ります。 いくつかのヒントを次に示します。

- 管理グループと呼ばれるコンテナーにサブスクリプションを整理して、ガバナンス条件をそれらに適用します。
- 管理グループ内のすべてのサブスクリプションが、管理グループの条件を自動的に継承します。
- 管理グループを使用すると、サブスクリプションの種類に関係なく、大きな規模でエンタープライズ レベルの管理を行うことができます。
- たとえば、VM を作成できるリージョンを制限する管理グループ ポリシーを適用できます。 その後、このポリシーは、その管理グループの下にあるすべての管理グループ、サブスクリプション、およびリソースに適用されます。
- 管理グループとサブスクリプションの柔軟な構造を作成し、リソースを階層に整理して、統一されたポリシーとアクセス管理を適用できます。

次の図は、管理グループを使用して管理のための階層を作成する例を示しています。

  ![管理グループの図。](./media/migrate-best-practices-security-management/management-groups.png)
  _図 8:管理グループ。_ "

**詳細情報:**

- [管理グループへのリソースの整理](/azure/governance/management-groups)の詳細を確認する。

## <a name="best-practice-deploy-azure-policy"></a>ベスト プラクティス:Azure Policy をデプロイする

Azure Policy は、ポリシーの作成、割り当て、および管理に使用するサービスです。 ポリシーにより、リソースにさまざまなルールと効果が適用されるため、それらのリソースは会社の標準とサービス レベル アグリーメントに準拠した状態が維持されます。

Azure Policy ではリソースが評価されて、ポリシーに準拠していないリソースがスキャンされます。 たとえば、VM に対して特定の SKU サイズのみを許可するポリシーを自身の環境に作成できます。 この設定は、リソースを作成および更新するときと既存のリソースをスキャンするときに Azure Policy によって評価されます。 Azure で提供されている組み込みポリシーを割り当てることも、独自に作成することもできます。

  ![Azure Policy のスクリーンショット。](./media/migrate-best-practices-security-management/policy.png)
  _図 9:Azure Policy。_ "

**詳細情報:**

- [Azure Policy の概要](/azure/governance/policy/overview)を読む。
- [コンプライアンスを適用するためのポリシーの作成と管理](/azure/governance/policy/tutorials/create-and-manage)について学習する。

## <a name="best-practice-implement-a-bcdr-strategy"></a>ベスト プラクティス:BCDR 戦略を実装する

事業継続とディザスター リカバリー (BCDR) の計画は、Azure への移行計画プロセスの一環として完了する必要がある重要な作業です。 法律的には、台風や地震などの大きな力が発生した場合に、責務が免除される "_不可抗力_" 条項を契約に含めることができます。 しかし、災害が発生したときは、サービスを継続的に実行し、必要に応じて復旧を行うことを可能な範囲で保証する義務もあります。 これを行う能力は、会社の未来を左右する可能性があります。

一般に、BCDR 戦略では以下のことを検討する必要があります。

- **データのバックアップ:** 障害が発生した場合に簡単に復旧できるよう、データを安全に保管する方法。
- **ディザスター リカバリー:** 障害が発生した場合にアプリケーションの回復性と可用性を維持する方法。

### <a name="set-up-bcdr"></a>BCDR をセットアップする

Azure に移行する際は、Azure のプラットフォームには回復性の機能がいくつか組み込まれていますが、それらの機能を利用するためには Azure のデプロイを設計する必要があることを理解しておいてください。

- BCDR ソリューションは会社の目標によってさまざまであり、Azure のデプロイ戦略の影響を受けます。 サービスとしてのインフラストラクチャ (IaaS) とサービスとしてのプラットフォーム (PaaS) のデプロイには、BCDR に関して異なる課題があります。
- BCDR ソリューションを導入したら、定期的にテストして、戦略が変わらずに実行可能であることを確認する必要があります。

### <a name="back-up-an-iaas-deployment"></a>IaaS のデプロイをバックアップする

ほとんどの場合、オンプレミスのワークロードは移行後には使用されなくなるので、データのバックアップに関するオンプレミスの戦略を拡張または置換する必要があります。 データセンター全体を Azure に移行する場合は、Azure のテクノロジまたはサード パーティ製の統合ソリューションを使用することで、完全バックアップ ソリューションを設計して実装する必要があります。

Azure IaaS VM で実行されるワークロードについては、以下のバックアップ ソリューションを検討します。

- **Azure Backup:** Azure のWindows と Linux の VM に、アプリケーション整合性バックアップを提供します。
- **ストレージのスナップショット:** BLOB ストレージのスナップショットを作成します。

#### <a name="azure-backup"></a>Azure Backup

Azure Backup では、Azure Storage に格納されているデータの復旧ポイントが作成されます。 Azure Backup では、Azure VM のディスクと Azure Files (プレビュー) をバックアップできます。 Azure Files では、サーバー メッセージ ブロック (SMB) 経由でアクセスできる、クラウド内のファイル共有が提供されます。

Azure Backup を使用すると、次の方法で VM をバックアップすることができます。

- **VM の設定からの直接バックアップ。** Azure portal の VM オプションから直接、Azure Backup で VM をバックアップできます。 1 日に 1 回 VM をバックアップし、必要に応じて VM ディスクを復元できます。 Azure Backup では、アプリケーション対応のデータ スナップショットが取得されます。VM にエージェントはインストールされません。
- **Recovery Services コンテナーでの直接バックアップ。** Azure Backup Recovery Services コンテナーをデプロイすることにより、IaaS VM をバックアップできます。 これにより、1 か所でバックアップを追跡して管理でき、バックアップと復元のきめ細かいオプションが提供されます。 バックアップは、ファイルおよびフォルダー レベルで 1 日に 3 回まで実行できます。 これはアプリケーション対応ではなく、Linux はサポートされていません。 この方法を使用して、バックアップ対象の各 VM に Microsoft Azure Recovery Services (MARS) エージェントをインストールします。
- **Azure Backup Server に対して VM を保護する。** Azure Backup Server は、Azure Backup で無料提供されます。 VM は、ローカルの Azure Backup Server ストレージにバックアップされます。 その後、Azure Backup Server をコンテナー内の Azure にバックアップします。 バックアップはアプリケーション対応であり、バックアップの頻度と保持に関して完全な細分性を備えています。 バックアップはアプリケーション レベルで実行できます。たとえば、SQL Server や SharePoint でバックアップできます。

セキュリティ上の理由により、Azure Backup では AES-256 を使用して送信中のデータが暗号化されます。 これは HTTPS 経由で Azure に送信されます。 Azure に保存されているバックアップ データは、[Azure Storage 暗号化](/azure/storage/common/storage-service-encryption)を使用して暗号化されます。

![Azure Backup のスクリーンショット。](./media/migrate-best-practices-security-management/iaas-backup.png)
"_図 10: Azure Backup。_ "

**詳細情報:**

- [Azure Backup](/azure/backup/backup-overview)の詳細を確認する。
- [Azure VM のバックアップ インフラストラクチャ](/azure/backup/backup-azure-vms-introduction)を計画する。

#### <a name="storage-snapshots"></a>ストレージのスナップショット

Azure VM は、Azure Storage にページ BLOB として格納されます。 スナップショットでは、特定の時点における BLOB の状態がキャプチャされています。 Azure VM ディスクの別のバックアップ方法として、ストレージ BLOB のスナップショットを取得し、別のストレージ アカウントにコピーできます。

BLOB 全体をコピーするか、または増分スナップショット コピーを使用して差分変更のみをコピーし、ストレージ スペースを削減できます。 追加の予防措置として、BLOB ストレージ アカウントの論理的な削除を有効にできます。 この機能が有効になっている場合、削除された BLOB は削除対象としてマークされますが、すぐには消去されません。 猶予期間中は BLOB を復元できます。

**詳細情報:**

- [Azure Blob Storage](/azure/storage/blobs/storage-blobs-introduction) について学習する。
- [BLOB スナップショットを作成する](/azure/storage/blobs/storage-blob-snapshots)方法を学習する。
- BLOB ストレージ バックアップの[サンプル シナリオを確認する](https://azure.microsoft.com/blog/microsoft-azure-block-blob-storage-backup)。
- [BLOB の論理的な削除](/azure/storage/blobs/storage-blob-soft-delete)について読む。
- [Azure Storage でのディザスター リカバリーと強制フェールオーバー (プレビュー)](/azure/storage/common/storage-disaster-recovery-guidance)

#### <a name="third-party-backup"></a>サード パーティのバックアップ

さらに、サード パーティのソリューションを使用して、Azure VM とストレージ コンテナーをローカル ストレージまたは他のクラウド プロバイダーにバックアップすることができます。 詳細については、[Azure Marketplace のバックアップ ソリューション](https://azuremarketplace.microsoft.com/marketplace/apps?search=backup&page=1)に関するページをご覧ください。

### <a name="set-up-disaster-recovery-for-iaas-applications"></a>IaaS アプリケーションのディザスター リカバリーを設定する

BCDR の計画では、データの保護だけでなく、障害の発生時にアプリケーションとワークロードの可用性を維持する方法を検討する必要があります。 Azure IaaS VM および Azure Storage で実行されるワークロードについては、次のセクションのソリューションを検討してください。

#### <a name="azure-site-recovery"></a>Azure Site Recovery

Azure Site Recovery は、障害が発生したときに Azure VM をオンラインにして VM アプリケーションを使用できるようにするための主要な Azure サービスです。

Site Recovery によって、プライマリ Azure リージョンからセカンダリ Azure リージョンに VM がレプリケートされます。 災害が発生した場合は、プライマリ リージョンから VM をフェールオーバーし、セカンダリ リージョンで通常どおり引き続き VM にアクセスします。 操作が正常に戻ったら、プライマリ リージョンに VM をフェールバックできます。

  ![Azure Site Recovery の図。](./media/migrate-best-practices-security-management/site-recovery.png)
  _図 11:Site Recovery。_ "

**詳細情報:**

- [Azure VM のディザスター リカバリー シナリオ](/azure/virtual-machines/virtual-machines-disaster-recovery-guidance)を確認する。
- [移行後に Azure VM のディザスター リカバリーを設定する](/azure/site-recovery/azure-to-azure-replicate-after-migration)方法を学習する。

## <a name="best-practice-use-managed-disks-and-availability-sets"></a>ベスト プラクティス:マネージド ディスクと可用性セットを使用する

Azure では、VM を論理的にグループ化し、セット内の VM を他のリソースから分離するために、可用性セットが使用されます。 可用性セット内の VM は、別々のサブシステムを持つ複数の障害ドメインに分散されて、ローカル障害から保護されます。 また、それらの VM は複数の更新ドメインにも分散され、セット内のすべての VM が同時に再起動されることが防止されます。

Azure マネージド ディスクによって VM ディスクに関連付けられているストレージ アカウントを管理することで、Azure Virtual Machines のディスク管理が簡素化されます。

- 可能な限り、マネージド ディスクを使用してください。 使用するストレージの種類と必要なディスクのサイズを指定するだけで、ディスクの作成と管理は Azure によって行われます。
- 既存のディスクをマネージド ディスクに変換できます。
- 高い回復性と可用性のためには、可用性セットに VM を作成する必要があります。 計画済み、または計画外の停止が発生したときは、可用性セットによって、セット内の少なくとも 1 つの VM が使用可能であることが保証されます。

  ![マネージド ディスクの図。](./media/migrate-best-practices-security-management/managed-disks.png)
  "_図 12: マネージド ディスク。_ "

**詳細情報:**

- [Managed Disks の概要](/azure/virtual-machines/windows/managed-disks-overview)を読む。
- [マネージドへのディスクの変換](/azure/virtual-machines/windows/convert-unmanaged-to-managed-disks)について学習する。
- [Azure で Windows VM の可用性を管理する](/azure/virtual-machines/windows/manage-availability)方法を学習する。

## <a name="best-practice-monitor-resource-usage-and-performance"></a>ベスト プラクティス:リソースの使用状況とパフォーマンスを監視する

Azure の優れたスケーリング機能が目的で、Azure にワークロードを移動することがあります。 しかし、ワークロードを移動するだけで、何も入力しなくても、自動的にスケーリングが実装されるわけではありません。 次に 2 つの例を示します。

- マーケティング組織が、トラフィックを 300% 増加させる新しいテレビ広告を配信した場合、サイトの可用性に問題が発生する可能性があります。 新たに移行されたワークロードが割り当てられている制限に達し、クラッシュする恐れがあります。
- 移行されたワークロードで分散型サービス拒否 (DDoS) 攻撃が発生している場合は、スケーリングしないことをお勧めします。 攻撃元がリソースに到達するのを防ぐ必要があります。

これら 2 つのケースの解決策は異なりますが、どちらの場合も、使用状況とパフォーマンスを監視して、何が起きているかについて分析情報を得る必要があります。

- Azure Monitor を使用すると、これらのメトリックを明らかにし、アラート、自動スケーリング、Event Hubs、Logic Apps による応答を提供できます。
- サード パーティ製の SIEM アプリケーションを統合して、Azure ログで監査とパフォーマンスのイベントを監視することもできます。

  ![Azure Monitor のスクリーンショット。](./media/migrate-best-practices-security-management/monitor.png)
  "_図 13: Azure Monitor。_ "

**詳細情報:**

- [Azure Monitor](/azure/azure-monitor/overview) について学習する。
- 監視と診断に関する[ベスト プラクティスを知る](/azure/architecture/best-practices/monitoring)。
- [自動スケーリング](/azure/architecture/best-practices/auto-scaling)について学習する。
- [Azure のデータを SIEM ツールにルーティングする](/azure/security-center/security-center-export-data-to-siem)方法を学習する。

## <a name="best-practice-enable-diagnostic-logging"></a>ベスト プラクティス:診断ログの有効化

Azure リソースでは、かなり多くのログ メトリックとテレメトリ データが生成されます。 既定では、ほとんどのリソースの種類で診断ログは有効になっていません。 リソース全体で診断ログを有効にすることにより、ログ データのクエリを実行し、アラートとそれに基づくプレイブックを作成できます。

診断ログを有効にするとき、各リソースには特定のカテゴリのセットがあります。 1 つまたは複数のログ カテゴリと、ログ データの場所を選択します。 ログは、ストレージ アカウント、イベント ハブ、または Azure Monitor ログに送信できます。

![診断ログのスクリーンショット。](./media/migrate-best-practices-security-management/diagnostics.png)
"_図 14: 診断ログ。_ "

**詳細情報:**

- [ログ データの収集と使用](/azure/azure-monitor/platform/platform-logs-overview)について学習する。
- [診断ログ](/azure/azure-monitor/platform/diagnostic-logs-schema)でサポートされているものを確認する。

## <a name="best-practice-set-up-alerts-and-playbooks"></a>ベスト プラクティス:アラートとプレイブックを設定する

Azure リソースの診断ログを有効にすると、ログ データを使用したカスタム アラートの作成を始めることができます。

- アラートでは、監視データで条件が検出されると事前に通知されます。 その後は、システムのユーザーがそれに気付く前に問題に対処することができます。 メトリックの値、ログ検索クエリ、アクティビティ ログ イベント、プラットフォームの正常性、Web サイトの可用性についてアラートを生成できます。
- アラートがトリガーされたら、ロジック アプリのプレイブックを実行できます。 プレイブックを使用すると、特定のアラートへの応答を自動化して調整できます。 プレイブックは、Azure Logic Apps に基づいています。 ロジック アプリ テンプレートを使用してプレイブックを作成するか、独自のプレイブックを作成することができます。
- 簡単な例としては、ネットワーク セキュリティ グループに対してポート スキャンが発生するとトリガーされるアラートを作成できます。 実行してスキャン元の IP アドレスをロックするプレイブックを設定することができます。
- もう 1 つの例は、メモリ リークが発生しているアプリケーションです。 メモリの使用量が特定のポイントに達したら、プレイブックでプロセスをリサイクルできます。

  ![アラートのスクリーンショット。](./media/migrate-best-practices-security-management/alerts.png)
  "_図 15: アラート。_ "

**詳細情報:**

- [アラート](/azure/azure-monitor/platform/alerts-overview)について学習する。
- [Security Center のアラートに応答するセキュリティ プレイブック](/azure/security-center/security-center-playbooks)について学習する。

## <a name="best-practice-use-the-azure-dashboard"></a>ベスト プラクティス:Azure ダッシュボードを使用する

Azure portal は Web ベースの統合コンソールで、簡単な Web アプリから複雑なクラウド アプリケーションまですべてを構築、管理、監視することができます。 これには、カスタマイズ可能なダッシュボードとユーザー補助オプションが用意されています。

- ダッシュボードを複数作成し、ご自分の Azure サブスクリプションにアクセスできる他のユーザーと共有することができます。
- この共有モデルでは、チームは Azure 環境の内部を見ることができ、プロアクティブにクラウド内のシステムを管理できます。

  ![Azure ダッシュボードのスクリーンショット。](./media/migrate-best-practices-security-management/dashboard.png)
  "_図 16: Azure ダッシュボード。_ "

**詳細情報:**

- [ダッシュボードを作成する](/azure/azure-portal/azure-portal-dashboards)方法を学習する。
- [ダッシュ ボードの構造](/azure/azure-portal/azure-portal-dashboards-structure)について学習する。

## <a name="best-practice-understand-support-plans"></a>ベスト プラクティス:サポート プランを理解する

いつかは、社内のサポート担当者または Microsoft のサポート担当者との共同作業が必要になります。 ディザスター リカバリーなどのシナリオでのサポートに関するポリシーと手順のセットを用意しておくことが不可欠です。 さらに、管理者やサポート担当者は、これらのポリシーの実装に関するトレーニングを受ける必要があります。

- Azure サービスの問題によってワークロードに影響が発生した場合に備えて、管理者は、最も適切かつ効率的な方法で Microsoft にサポート チケットを送信する方法を知っている必要があります。
- Azure に対して提供されているさまざまなサポート プランを理解しておきます。 その範囲は、開発者インスタンス専用の応答時間から、応答時間が 15 分未満の Premier サポートにまで及びます。

  ![サポート プランのスクリーンショット。](./media/migrate-best-practices-security-management/support.png)
  "_図 17: サポート プラン。_ "

**詳細情報:**

- [Azure サポート プランの概要](https://azure.microsoft.com/support/options)を読む。
- [サービス レベル アグリーメント (SLA)](https://azure.microsoft.com/support/legal/sla) について学習する。

## <a name="best-practice-manage-updates"></a>ベスト プラクティス:更新プログラムの管理

Azure VM をオペレーティング システムとソフトウェアの最新の更新プログラムで常に更新しておくことは、大変手間のかかる作業です。 すべての VM を把握し、それらに必要な更新プログラムを明らかにして、それらの更新プログラムを自動的にプッシュする機能は、非常に価値があります。

- Azure Automation の Update Management を使用して、オペレーティング システムの更新プログラムを管理できます。 これは、Azure、オンプレミス、および他のクラウド プロバイダーにデプロイされている Windows コンピューターと Linux コンピューターを実行するマシンに適用されます。
- Update Management を使用すると、すべてのエージェント コンピューターで利用可能な更新プログラムの状態をすばやく評価し、更新プログラムのインストールを管理できます。
- VM の Update Management は、Azure Automation アカウントから直接有効にすることができます。 Azure portal の VM のページから 1 つの VM を更新することもできます。
- さらに、Azure VM を System Center Configuration Manager に登録できます。 そうすると、Configuration Manager のワークロードを Azure に移行して、単一の Web インターフェイスからレポートの作成やソフトウェアの更新を行うことができます。

  ![VM の更新の図。](./media/migrate-best-practices-security-management/updates.png)
  "_図 18: 更新。_ "

**詳細情報:**

- [Azure の Update Management](/azure/automation/update-management/overview) について学習する。
- [Configuration Manager と Update Management を統合する](/azure/automation/oms-solution-updatemgmt-sccmintegration)方法を学習する。
- Azure での Configuration Manager について[よく寄せられる質問](/sccm/core/understand/configuration-manager-on-azure)。

## <a name="implement-a-change-management-process"></a>変更管理プロセスを実装する

他の運用システムと同様に、何らかの種類の変更によって環境に影響を与える可能性があります。 運用システムを変更するには要求を送信する必要がある変更管理プロセスは、移行後の環境において有用な追加機能です。

- 変更管理のベスト プラクティス フレームワークを構築して、管理者やサポート担当者の認識を高めることができます。
- Azure Automation を使用して、移行されるワークフローの構成管理と変更追跡を支援できます。
- 変更管理プロセスを適用するときは、監査ログを使用して、Azure の変更ログを既存の変更要求にリンクできます。 その後、対応する変更要求なしで行われた変更がある場合は、プロセスで発生した問題を調べることができます。

Azure では、Azure Automation の変更追跡ソリューションを使用できます。

- このソリューションでは、Windows および Linux のソフトウェアとファイル、Windows のレジストリ キー、Windows サービス、および Linux デーモンに対する変更が追跡されます。
- 監視対象サーバーに対する変更は、処理するために Azure Monitor に送信されます。
- 受信したデータにロジックが適用され、クラウド サービスによってそのデータが記録されます。
- [変更の追跡] ダッシュボードでは、サーバー インフラストラクチャで行われた変更を簡単に確認できます。

  ![変更管理のスクリーンショット。](./media/migrate-best-practices-security-management/change.png)
  "_図 19: 変更管理。_ "

**詳細情報:**

- [Change Tracking](/azure/automation/automation-change-tracking) について学習する。
- [Azure Automation の機能](/azure/automation/automation-intro)について学習する。

## <a name="next-steps"></a>次のステップ

他のベスト プラクティスを確認します。

- 移行後のネットワークの[ベスト プラクティス](./migrate-best-practices-networking.md)。
- 移行後のコスト管理の[ベスト プラクティス](./migrate-best-practices-costs.md)。
