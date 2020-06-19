---
title: クラウド監視のためのスキルの準備
description: クラウド監視のためのスキルの準備
author: BrianBlanchard
ms.author: magoedte
ms.date: 05/19/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: migrate
ms.openlocfilehash: 6672d86f215360f1024b233f62a48eb390b52a52
ms.sourcegitcommit: d88c1cc3597a83ab075606d040ad659ac4b33324
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/15/2020
ms.locfileid: "84785176"
---
<!-- cSpell:ignore kusto ITIL -->

# <a name="skills-readiness-for-cloud-monitoring"></a>クラウド監視のためのスキルの準備

移行の行程の計画フェーズでの目標は、実装をガイドするために必要な計画を作成することです。 また、これらのワークロードを運用環境に移行またはリリースする前に (後ではない)、これらのワークロードを操作する方法を計画に含める必要があります。 ビジネス上の利害関係者は有益かつ中断しないサービスを期待しています。 IT スタッフ メンバーは、統合された Azure サービスを自信を持って使用し、Azure とハイブリッド環境のリソースを効率的に監視するために、新しいスキルを習得して順応する必要があるということを認識しています。

次のラーニング パスを使用して、必要なスキルの開発を加速することができます。 これらは、基礎の学習から始めるように構成されており、インフラストラクチャ、アプリケーション、データ分析の 3 つの主要分野に分かれています。  

## <a name="fundamentals"></a>基礎

- [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/management/overview) の概要では、Azure リソースの管理とデプロイの基本的な概念について説明しています。 企業全体の監視エクスペリエンスを管理する IT スタッフは、管理スコープ、ロールベースのアクセス制御 (RBAC)、 Azure Resource Manager テンプレートの使用、および Azure CLI と Azure PowerShell を使用したリソースの管理について理解する必要があります。

- [Azure Policy](https://docs.microsoft.com/azure/governance/policy/overview) の概要では、Azure Policy を使用してポリシーを作成、割り当て、管理する方法について説明しています。 Azure Policy では、Azure Monitor エージェントのデプロイと構成、Azure Monitor for VMs と Azure Security Center を使用した監視の有効化、診断設定のデプロイ、ゲスト構成設定の監査などを行うことができます。

- [Azure コマンド ライン インターフェイス](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest) (CLI) の概要。これは、Azure リソースを管理するためのクロスプラットフォーム コマンド ライン エクスペリエンスとなっています。 また、[Azure PowerShell](https://docs.microsoft.com/powershell/azure/?view=azps-3.6.1) の概要についても確認してください。 LinkedIn は、[Azure の管理ツールを学習する](https://www.linkedin.com/learning/learning-azure-management-tools)初級レベルのコースの一部として、Azure CLI と PowerShell プログラミング言語を対象としたセッションを提供しています。

  - [Azure CLI を使用する](https://www.linkedin.com/learning/learning-azure-management-tools/use-the-azure-cli)
  - [Azure PowerShell の概要](https://www.linkedin.com/learning/learning-azure-management-tools/understand-azure-powershell)

- ポリシー、ロールベースのアクセス制御、およびその他の Azure サービスを使用してリソースをセキュリティで保護する方法については、「[Azure にリソースのセキュリティを実装する](https://docs.microsoft.com/learn/paths/implement-resource-mgmt-security)」を参照してください。

- 「[Monitoring Microsoft Azure Resources and Workloads (Microsoft Azure のリソースとワークロードの監視)](https://app.pluralsight.com/library/courses/microsoft-azure-resources-workloads-monitoring-update/table-of-contents)」では、Microsoft Azure 監視ツールを使用して、Azure ネットワーク リソースとオンプレミスのリソースを監視する方法について学習できます。

## <a name="infrastructure-monitoring"></a>インフラストラクチャ監視

- 「[Design a Monitoring Strategy for Infrastructure in Microsoft Azure (Microsoft Azure でのインフラストラクチャの監視戦略の設計)](https://www.pluralsight.com/courses/microsoft-azure-monitoring-strategy-infrastructure-design-update)」では、Azure の監視機能およびソリューションの基礎知識を習得できます。

- 「[How to monitor your Kubernetes clusters (Kubernetes クラスターを監視する方法)](https://www.youtube.com/watch?time_continue=3&v=RjsNmapggPU&feature=emb_logo)」では、コンテナー用 Azure Monitor を使用して Kubernetes クラスターを監視する方法について、中級レベルの詳細情報を提供します。

- Azure Monitor を使用して、[Azure Storage および HDInsight](https://www.pluralsight.com/courses/microsoft-azure-data-storage-monitoring) からのデータを監視する方法を学びます。

- 「[Microsoft Azure Database Monitoring Playbook (Microsoft Azure のデータベース監視プレイブック)](https://www.pluralsight.com/courses/microsoft-azure-database-playbook-monitoring)」では、Azure SQL Database、Azure SQL Data Warehouse、Azure Cosmos DB で分析情報を得るために使用できる主な監視機能と実施可能な手順について説明します。

- 「[Monitoring Microsoft Azure Hybrid Cloud Networks (Microsoft Azure ハイブリッド クラウド ネットワークの監視)](https://www.pluralsight.com/courses/microsoft-azure-hybrid-cloud-networks-monitoring)」は、Azure 監視ツールを使用して、ハイブリッド クラウド実装の Azure 仮想ネットワークと仮想プライベート ネットワーク接続を視覚化、管理、最適化する方法を学習できる上級レベルのコースです。

- [Azure Arc for servers](https://docs.microsoft.com/azure/azure-arc/servers/overview) に関する記事では、Azure の外部でホストされている Windows および Linux マシンをネイティブの Azure 仮想マシンと同じように管理できる方法について説明しています。

## <a name="application-monitoring"></a>アプリケーションの監視

- [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) を使用して、アプリケーションとサービスの可用性とパフォーマンスを 1 か所で確認する方法についてご覧ください。 Pluralsight には、役に立つ次のコースが用意されています。

  - 「[Microsoft Azure DevOps エンジニア:Optimize Feedback Mechanisms (Microsoft Azure DevOps エンジニア: フィードバック メカニズムの最適化)](https://www.pluralsight.com/courses/microsoft-azure-optimize-feedback-mechanisms)」は、Application Insights を含む Azure Monitor を使用して、Web アプリケーションの監視と最適化を準備する際に役立ちます。

  - [Microsoft Azure Developer: Monitoring Performance (Microsoft Azure 開発者: パフォーマンスの監視)](https://app.pluralsight.com/library/courses/microsoft-azure-performance-monitoring)。 Azure Monitor Application Insights を使用して、Azure で実行されているアプリケーション コンポーネントのエンド ツー エンドの監視を実現する場合は、このコースから始めます。
  
  - 「[Microsoft Azure のデータベース監視に関するプレイブック](https://www.pluralsight.com/courses/microsoft-azure-database-playbook-monitoring)」では、Azure SQL Database、Azure SQL Data Warehouse、および Azure Cosmos DB の監視を実装して使用する方法について学習できます。

  - 「[Azure Monitor Application Insights を使用したアプリケーションのインストルメント化](https://app.pluralsight.com/library/courses/microsoft-azure-application-insights-web-application-instrument)」では、Application Insights SDK を使用して、Angular と Node.js コンポーネントを使用するアプリからテレメトリとイベントを収集する方法について詳しく説明します。

  - 「[Application Debugging and Profiling (アプリケーションのデバッグとプロファイリング)](https://www.pluralsight.com/courses/devintersection-azureai-session-31)」は、Azure Monitor Application Insights のスナップショット デバッガーとプロファイラーによって提供されるデータの使用と解釈について、Microsoft カンファレンスのセッションから記録したものです。

## <a name="data-analysis"></a>データ分析

- [Azure Monitor でログ クエリ](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries)を作成する方法をご確認ください。 Kusto クエリ言語は、Azure Monitor ログ クエリを作成するための主要リソースです。Azure から収集されたデータとハイブリッド リソース アプリケーション (ライブ アプリケーションなど) の依存関係の間で、ログ データを探索して分析するためにログ クエリを作成します。

- 「[Kusto Query Language (KQL) from Scratch (ゼロから始める Kusto クエリ言語 (KQL))](https://www.pluralsight.com/courses/kusto-query-language-kql-from-scratch)」は、Azure Monitor ログでのログ分析の幅広いユースケースと手法を示す詳細な例を含む包括的なコースです。

## <a name="deeper-skills-exploration"></a>より詳細なスキル調査

スキルを伸ばすためのこれらの初期のオプション以外に、さまざまな学習オプションを利用できます。

### <a name="typical-mappings-of-cloud-it-roles"></a>クラウド IT の役割の典型的なマッピング

Microsoft とパートナーは、あらゆるお客様を対象に、Azure サービスに関連したスキルの向上のためのさまざまなオプションを提供しています。

- [Microsoft IT Pro Career Center](https://www.microsoft.com/itpro):クラウドのキャリア パスをマッピングするのに役立つ無料のオンライン リソースとして機能します。 業界の専門家がクラウド ロールについて行う提案、および提案を実現するためのスキルについて学習してください。 関係を保つために最も必要なスキルを磨くように、学習カリキュラムをご自分のペースで進められます。

Azure に関する知識を [Microsoft Azure 認定のトレーニングや試験]( https://www.microsoft.com/learning/certification-overview.aspx)によって公認のものにすることができます。

## <a name="azure-devops-and-project-management"></a>Azure DevOps とプロジェクトの管理

ハイブリッド クラウド環境では、定義されていない役割、責任、およびアクティビティによって IT が中断されます。 組織は、アジャイルや DevOps の手法を含む最新のサービス管理手法に移行して、合理化された効率的な方法で、現在のビジネスにおける変革と最適化のニーズをより適切に満たす必要があります。

クラウド監視プラットフォームへの移行の一環として、企業内の監視の管理を担当する IT チームは、アジャイルのトレーニングと参加を DevOps アクティビティに含める必要があります。 これには、ビジネス ニーズに合わせて繰り返し調整された最小限の実行可能な監視ソリューションを提供するために、DevOps の _Dev_ に従い、要件を把握して、それを整理されたアジャイルの要件に発展させることも含まれます。 反復的な監視ソリューション パッケージとその他の関連する対象を管理するソース管理では、Azure DevOps Server プロジェクトと GitHub Enterprise Server リポジトリを関連付けます。 これにより、GitHub のコミットと、作業項目に対するプル要求の間でリンクが提供されます。 Azure Boards を使用して作業を計画および追跡する一方で、継続的な監視の統合とデプロイのサポートにより、開発のために GitHub Enterprise を使用できます。

詳細については、以下を参照してください。

- [Azure DevOps を使用して作業を開始する](https://docs.microsoft.com/learn/modules/get-started-with-devops)

- [DevOps 白帯道場の基礎について理解する](https://docs.microsoft.com/learn/paths/devops-dojo-white-belt-foundation)

- [DevOps のプラクティスを進化させる](https://docs.microsoft.com/learn/paths/evolve-your-devops-practices)

- [Azure DevOps を使用してデプロイを自動化する](https://docs.microsoft.com/learn/paths/automate-deployments-azure-devops)

## <a name="other-considerations"></a>その他の考慮事項

多くの場合、顧客は、IT によって提供されるサービスについて、予想されるビジネス上の (および IT 組織に対する) 成果を管理、維持、および提供しようと努力します。 監視は、インフラストラクチャとビジネスを管理するための中核と見なされており、サービスとカスタマー エクスペリエンスの品質の測定に重点が置かれます。 これらの目標を達成するために、ITSM を DevOps と組み合わせて使用して基礎を作ります。これにより、監視チームは、監視サービスを管理、提供、およびサポートする方法を成熟させることができます。 ITSM フレームワークを導入することで、監視チームは、組織の戦略目標とニーズに合わせて、プロバイダーとして機能でき、信頼されたビジネス イネーブラーとして認識されるようになります。

以下を確認して、最も人気のある ITSM フレームワークである [ITIL v4 とクラウド コンピューティングのホワイトペーパー](https://www.axelos.com/case-studies-and-white-papers/itil-4-and-the-cloud)に加えられた更新を理解してください。これは、既存の ITIL ガイダンスを DevOps、アジャイル、および Lean のベスト プラクティスと結合することに重点を置いています。 また、プロセスに依存しないフレームワークを使用して IT を変換する方法について別のブループリントを提供する、[IT4IT 参照アーキテクチャ](https://www.opengroup.org/it4it)についても検討してください。

## <a name="learn-more"></a>詳細情報

追加のラーニング パスについては、[Learn のカタログ](https://docs.microsoft.com/learn/browse)を参照してください。 [ロール] フィルターを使用して、ラーニング パスを自分のロールに合わせてください。
