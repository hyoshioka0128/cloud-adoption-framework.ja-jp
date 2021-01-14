---
title: GitHub によるクラウド導入の促進
description: 企業は、GitHub でのオープンソース コミュニティとのつながりを利用することで、Azure サービスを正常に導入した組織の何千ものクラウド ソリューションの例を見つけることができます。これらは、反復、強化され、すぐにデプロイ可能なものです。
author: nkpatterson
ms.author: janet
ms.date: 1/11/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: strategy
ms.custom: think-tank
ms.openlocfilehash: 054be31046adab8df422ceef97a61fbd0878127b
ms.sourcegitcommit: 32a958d1dd2d688cb112e9d1be1706bd1e59c505
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/12/2021
ms.locfileid: "98124345"
---
# <a name="how-github-accelerates-cloud-adoption"></a>GitHub によるクラウド導入の促進

## <a name="overview"></a>概要

イノベーションは、今日の厳しい競争の世界における新しい通貨のようなものです。 ライドシェア、ストリーミング コンテンツ、自動運転車、その他のサービスによって、人々の生活のリズムが根本的に変わってきた一方で、市場が根底から覆され、厳しい競争の世界の中心は物理的な資産からデジタル体験へと移り変わっています。

このような優れたデジタル体験が牽引する破壊的な動きによって、老舗の企業は、イノベーションが可能で、より迅速に顧客に価値を提供できる企業からの厳しい競争に直面しています。 競争力を持ち、崩壊を防ぐためには、イノベーションの文化を構築し、最適なツールとクラウド サービスを使用する必要があります。

GitHub には、企業が次のことを行うのに役立つさまざまな機能が用意されています。

- Azure のサービスと機能の活用。
- 現在の手法の最新化。
- この文化的改革により、アジャイル性とイノベーション性を高める。

企業は、GitHub でのオープンソース コミュニティとのつながりを利用することで、Azure サービスを正常に導入した組織の何千ものクラウド ソリューションの例を見つけることができます。これらは、反復、強化され、すぐにデプロイ可能なものです。 これらのソリューションを簡単に流用して繰り返することで、ビジネス ニーズに合わせて調整できます。

GitHub を使用すると、チーム内で簡単に共有できるようになり、次のアプリケーションやワークロードの最新化とデプロイを迅速に行えるようになります。 企業では、オープンソース コミュニティからの共有と再利用、コラボレーションとコミュニケーションなどのベスト プラクティスを借用して組織内で適用することにより、イノベーションの基本理念である InnerSource に目を向けることができます。

オープンソース パッケージの保護から日々作成される知的財産に至るまで、ソフトウェア サプライ チェーン全体をセキュリティで保護することは、すべての企業にとっての主要な優先事項になります。 これには、ライフサイクル全体にわたって統合および自動化できる高度なセキュリティ テクノロジが必要です。GitHub Advanced Security や GitHub Actions などのネイティブ GitHub 機能では、この種の柔軟性が提供されます。

## <a name="take-advantage-of-open-source-assets"></a>オープンソース アセットを活用する

極めて効率的な組織は、最新のソフトウェア開発にとってオープンソース ソフトウェア (OSS) はオプションではなく必須であると認識しています。 信頼している開発者コミュニティと連携し、セキュリティで保護されたプラットフォームを使用して、OSS に戦略的に投資しています。 その結果、これらの組織は、リスクを最小限に抑えながら、迅速にイノベーションを推し進め、競合他社をしのぎ、コストを削減しています。

OSS は、アプリケーションに組み込まれているパッケージ、ライブラリ、スクリプト、および依存関係として解釈される場合がありますが、明確に定義された Azure アーキテクチャのオープンソース アセットには、コードとしてのインフラストラクチャ (IaC)、ドキュメント、ガイダンス、およびブループリントという多数の形式があります。 これらのブループリントは、Microsoft、パートナー、ベンダー、顧客、および個人によって OSS コミュニティに提供されたものであり、GitHub ですぐに入手できます。 これらは、簡単に変更して再利用し、特定の Azure 環境にデプロイできます。

### <a name="infrastructure-as-code"></a>コードとしてのインフラストラクチャ

コードとしてのインフラストラクチャ (IaC) は、記述型モデルでのインフラストラクチャ (ネットワーク、仮想マシン、ロード バランサー、接続トポロジ) の管理であり、DevOps チームがソース コードに使用するものと同じバージョン管理を使用します。 同じソース コードからは同じバイナリが生成されるという原則と同様に、IaC モデルが適用されるたびに同じ環境が生成されます。 IaC は [継続的デリバリー (CD)](/azure/devops/learn/what-is-continuous-delivery) で使用される主要な DevOps 手法です。

IaC は、リリース パイプラインでの環境ドリフトの問題を解決するために発達しました。 これを用いないと、チームは個々のデプロイ環境の設定を維持する必要があり、環境間の不整合によってデプロイ中に問題が発生します。 結果として、すべての環境が、自動的には再現できない独自の構成であるスノーフレーク状態になります。 スノーフレークがあると、インフラストラクチャの管理とメンテナンスに手動のプロセスが含まれるため、エラーが発生し、追跡が困難になる可能性があります。IaC を使用したインフラストラクチャのデプロイは反復可能であり、構成ドリフトや依存関係の不足によって発生するランタイムの問題を回避できます。

IaC を使用すると、チームは環境の記述に変更を加え、構成モデルのバージョン管理を行いうことになります。これは通常は、適切に文書化されている JSON などのコード形式です。詳細については、「[Azure Resource Manager テンプレート](/azure/azure-resource-manager/templates/overview)」をご覧ください。 開発者は、アプリケーションのソースコードと同じ GitHub リポジトリで IaC コードをホストし、[GitHub Actions](https://github.com/features/actions) によって提供される IaC に対して同じ継続的インテグレーション (CI)/CD 手法を採用することで、ワークフローを単純化できます。

さまざまな Azure スコープでカスタム Resource Manager テンプレートをデプロイする方法の例については、[AzOps](https://github.com/Azure/azops) GitHub Action をご覧ください。 Resource Manager テンプレートまたは IaC を初めて使用する場合は、GitHub で [azure-quickstart-templates リポジトリ](https://github.com/Azure/azure-quickstart-templates)を参照し、デプロイするテンプレートを見つけて、 **[Azure へのデプロイ]** ボタンを選択してその動作をテストすることもできます。

![**[Azure へのデプロイ]** ボタンのスクリーンショット。](./media/deploy-to-azure.png)

### <a name="cloud-pattern-components-and-best-practices"></a>クラウド パターンのコンポーネントとベスト プラクティス

次のアーキテクチャ図は、GitHub DevSecOps 環境内の GitHub および Azure コンポーネントで実行されるセキュリティ チェックを強調表示しています。

![GitHub DevSecOps 環境内の GitHub および Azure コンポーネントで実行されるセキュリティ チェックを強調表示しているアーキテクチャ図。](./media/github-security-checks.png)

- [GitHub](https://docs.github.com/en/github) には、オープンソースと内部ソースのプロジェクトのコラボレーションに使用できるコードホスティング プラットフォームが用意されています。

- [Codespaces](https://docs.github.com/en/github/developing-online-with-codespaces/about-codespaces) はオンライン開発環境です。 GitHub によってホストされ、Microsoft Visual Studio Code によって強化されているこのツールは、クラウドでの包括的な開発ソリューションになります。

- [GitHub セキュリティ](https://github.com/features/security)は、さまざまな方法で脅威を解消するように機能します。 エージェントとサービスによって、リポジトリと依存パッケージの脆弱性が特定されます。 さらに、それらによって、依存関係が最新のセキュアなバージョンにアップグレードされます。

- [GitHub Actions](https://docs.github.com/en/actions/getting-started-with-github-actions/about-github-actions) は、リポジトリに CI/CD 機能を直接提供するカスタム ワークフローです。 ランナーと呼ばれるコンピューターで、これらの CI/CD ジョブがホストされます。

- [Azure Active Directory](/azure/active-directory/fundamentals/active-directory-whatis) は、Azure および、Microsoft 365 や GitHub などのその他のクラウド アプリへのアクセスを制御するマルチテナント クラウドベースの ID サービスです。

- [Azure App Service](https://azure.microsoft.com/services/app-service/) には、Web アプリの構築、デプロイ、およびスケーリングを行うためのフレームワークが用意されています。 このプラットフォームでは、組み込みのインフラストラクチャ メンテナンス、セキュリティ パッチ、およびスケーリングが提供されます。

- [Azure Policy](/azure/governance/policy/overview) は、チームがクラウド リソースにルールを適用できるポリシー定義によって、IT の問題を管理し防止するのに役立ちます。 たとえば、プロジェクトで、認識されていない SKU がある仮想マシンをデプロイしようとしている場合は、Azure Policy によって、問題についてのアラートが送信され、デプロイが中止されます。

- [Azure Security Center](/azure/security-center/security-center-intro) は、ハイブリッド クラウド ワークロード全体で統合されたセキュリティ管理と高度な脅威保護を実現します。

- [Azure Monitor](/azure/azure-monitor/overview) によって、パフォーマンス メトリックやアクティビティ ログその他のアプリ テレメトリが収集され、分析されます。 このサービスで、通常と異なる状態が特定されると、アプリと担当者に通知されます。

## <a name="innersource"></a>InnerSource

### <a name="innersource-overview"></a>InnerSource の概要

多くの企業では、*InnerSource* という用語を使用して、コードでエンジニアリング チームがどのように連携するかを説明しています。 InnerSource は、Kubernetes や Visual Studio Code などの大規模なオープンソース プロジェクトのベスト プラクティスを使用して、エンジニアが独自のソフトウェアを構築する開発手法です。

オープンソースの大規模なプロジェクトでは、数千人規模の共同作成者の間での連携とチームワークが必要です。 最も成功したものは、未来と日々のユーザーのニーズ (速度、信頼性、機能) を見据えたビジョンによって牽引されています。 これらが運用されている規模から、いくつかの教訓を学ぶことができ、企業が InnerSource を使用してより優れたソフトウェアを迅速に構築するのに役立ちます。

GitHub のプル要求と問題により、コラボレーションとコード レビューが開発プロセスに組み込まれています。 内部およびアウトソーシングのチームは、作業の共有、変更についての話し合い、フィードバックの受け取りをすべて 1 か所で行うことができます。 これにより、組織は専門知識を内部的に共有し、他のプロジェクト用に開発されたフィールド テスト済みソリューションを一から作り直さなくて済みます。

### <a name="the-anatomy-of-an-innersource-project"></a>InnerSource プロジェクトの構造

個人、チーム、リソースを適切に組み合わせることで、プロジェクトの成功を実現します。 多くのオープンソース プロジェクトは、同様の組織構造に従うことで、組織が機能横断型チームを設定して InnerSource プロジェクトを管理しやすくしています。 一般的なオープンソース プロジェクトには、次の種類のメンバーが存在します。

- **保守担当者:** これらの共同作成者は、ビジョンを推進し、プロジェクトの組織的な側面を管理する責任を負います。 コードの元の所有者や作成者ではない可能性があります。

- **共同作成者:** プロジェクトに何らかの貢献をしているユーザーです。

- **コミュニティ メンバー:** プロジェクトを使用するユーザーです。 プロジェクトの方向性に関して積極的に会話していたり、意見を表明したりしている場合があります。

大規模なプロジェクトでは、ツール、トリアージ、コミュニティ モデレーションなどのさまざまなタスクに焦点を絞った小委員会や作業グループが存在する場合もあります。 InnerSource プロジェクトは、同様の構造に従うことが考えられます。 多くのエンジニアリング組織は、開発者をアプリケーション エンジニアリング、プラットフォーム エンジニアリング、Web 開発などのチームに分類します。 このように組織を構築すると、適格なメンバーが除外されたブラインド スポットが発生する可能性があります。 組織全体の複数のチームによって支えられた主要な意思決定グループを編成することで、問題を迅速に解決するために必要な専門知識を再結集することができます。

企業内では、共同作成者は会社全体の開発者であり、管理者はプロジェクトのリーダーであり、主要な意思決定者です。

- **保守担当者**:開発者、プロダクト マネージャー、およびその他の主要な意思決定者は、プロジェクトのビジョンを推進し、日々の投稿を管理する責任を担います。

- **共同作成者**:ソフトウェアの推進を支援する、開発者、データ サイエンティスト、プロダクト マネージャー、マーケティング担当者、およびその他の企業内の役割。 共同作成者はプロジェクト チームのメンバーではない可能性がありますが、コードの投稿、バグ修正の提出などにより、ソフトウェアの作成を支援します。

詳細については、「[InnerSource の概要](https://resources.github.com/whitepapers/introduction-to-innersource/)」ホワイトペーパーをご覧ください。

## <a name="automation"></a>Automation

GitHub Actions は、ユーザーが GitHub リポジトリでカスタム ワークフローを直接作成できるように支援します。 ユーザーは、CI/CD を含む任意のジョブを実行するためのアクションを検出、作成、および共有して、完全にカスタマイズされたワークフローでアクションを組み合わせることができます。 また、さまざまなプログラミング言語で記述されたプロジェクトをビルドおよびテストする CI ワークフローを作成することもできます。 いくつかの例をこちらに示します。

- [JavaScript と TypeScript 用の GitHub Actions](https://help.github.com/en/actions/language-and-framework-guides/github-actions-for-javascript-and-typescript)
- [Python 用の GitHub Actions](https://help.github.com/en/actions/language-and-framework-guides/github-actions-for-python)
- [Java 用の GitHub Actions](https://help.github.com/en/actions/language-and-framework-guides/github-actions-for-java)
- [Docker 用の GitHub Actions](https://help.github.com/en/actions/language-and-framework-guides/github-actions-for-docker)

GitHub Actions を使用すると、IaC の概念と CI/CD の手法を組み合わせて、エンドツーエンドのデプロイ ライフサイクル全体を自動化できます。これには、反復可能な方法でのターゲット環境のプロビジョニングや更新、アプリケーション自体のパッケージ化とデプロイなどが含まれます。

### <a name="example"></a>例

[Azure 向けの GitHub Actions](https://github.com/Azure/actions) は、Azure App Service、Azure Kubernetes Service、Azure Functions などの Azure サービスを対象とするデプロイ プロセスを自動化する方法を簡略化するために構築されています。 [Azure スターター アクション ワークフロー リポジトリ](https://github.com/Azure/actions-workflow-samples)では、任意の言語、任意のエコシステムの Web アプリをビルドして Azure にデプロイするためのエンド ツー エンドのワークフローが提供されています。 [GitHub Marketplace](https://github.com/marketplace?query=Azure&type=actions) にアクセスして、利用可能なすべての Actions を確認してください。

## <a name="security"></a>Security

### <a name="githubs-shift-left-security-features"></a>GitHub のシフト レフト セキュリティ機能

開発の最初のステップから、DevSecOps はセキュリティのベストプラクティスを順守しています。 DevSecOps では、シフトレフト戦略を使用して、セキュリティ フォーカスがリダイレクトされます。 最後の監査に目を向けるのではなく、最初の開発にシフトします。 堅牢なコードを作成するだけでなく、このフェイル ファスト アプローチを使用すると、修正が容易な早期に問題を解決するのに役立ちます。

多くのセキュリティ機能を備えている GitHub は、DevSecOps ワークフローのすべての部分をサポートするツールを提供しています。

- 組み込みのセキュリティ拡張機能を備えたブラウザーベースの IDE
- セキュリティ アドバイザリを継続的に監視し、脆弱で古い依存関係を置き換えるエージェント
- ソースコードの脆弱性をスキャンする検索機能
- 開発、テスト、およびデプロイのすべての手順を自動化するアクションベースのワークフロー
- セキュリティの脅威をプライベートで議論して解決してから、情報を公開する方法を提供するスペース
- Azure の監視および評価機能と組み合わせることで、これらの機能はセキュアなクラウド ソリューションを構築するための優れたサービスを提供します。

### <a name="example"></a>例

GitHub DevSecOps インストールは、多くのセキュリティ シナリオに対応しています。 次のケースの可能性があります。

- セキュリティ機能を提供する事前構成済みの環境を利用しようとする開発者
- すぐに使える最新の優先順位付けされたセキュリティ レポートに加えて、影響を受けるコードや推奨される修正についての詳細情報に依存する管理者
- コードに秘密が公開されている場合、システムに自動的に新しい侵害されていないセキュリティ デバイスを取得させる必要がある効率化された組織
- より新しいか、またはセキュリティが強化されたバージョンの外部パッケージが利用可能になったときの自動アップグレードの利点が得られる可能性がある開発チーム

参考資料:

- [GitHub の DevSecOps-Azure ソリューションのアイデア | Microsoft Docs](/azure/architecture/solution-ideas/articles/devsecops-in-github)
- [Azure DevOps パイプライン内で GitHub Advanced Security を使用して GitHub リポジトリをスキャンするコード](https://github.blog/2020-10-27-code-scanning-a-github-repository-using-github-advanced-security-within-an-azure-devops-pipeline/)
- [ソフトウェア サプライ チェーンへの DevSecOps の適用](https://github.blog/2020-12-03-applying-devsecops-to-your-software-supply-chain/)

## <a name="next-steps"></a>次の手順

- 実装チーム (通常は開発マネージャーと、管理者として定義されている数名の開発者) を選択し、GitHub をデプロイします。
- GitHub の高度な使用方法のための一般的な Git ワークフローと高度な Git ワークフローについて説明します。

下のリンクを使用すると、GitHub Docs に移動します。

- [GitHub @ Microsoft Learning](/learn/browse/?products=github)
- [GitHub の学習ラボ](https://lab.github.com/)
- [GitHub Docs](https://docs.github.com/en)
- [GitHub DevSecOps の使用を開始するためのヒント](https://resources.github.com/whitepapers/Architects-guide-to-DevOps/)
