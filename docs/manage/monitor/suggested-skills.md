---
title: クラウド監視のためのスキルの準備
description: クラウド監視のためのスキルの準備
author: BrianBlanchard
ms.author: brblanch
ms.date: 08/26/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: migrate
ms.custom: internal
ms.openlocfilehash: dffd1534d461d443a80f20b3f7cd65392dbe9acc
ms.sourcegitcommit: 9d76f709e39ff5180404eacd2bd98eb502e006e0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/17/2021
ms.locfileid: "100631391"
---
<!-- cSpell:ignore kusto ITIL -->

# <a name="skills-readiness-for-cloud-monitoring"></a>クラウド監視のためのスキルの準備

移行の行程を計画するときの目標は、実装をガイドするために必要な計画を作成することです。 また、これらのワークロードを運用環境に移行またはリリースする前に (後ではない)、これらのワークロードを操作する方法を計画に含める必要があります。 ビジネス上の利害関係者は有益かつ中断しないサービスを期待しています。 IT スタッフ メンバーは、統合された Azure サービスを自信を持って使用し、Azure とハイブリッド環境のリソースを効率的に監視するために、新しいスキルを習得して順応する必要があるということを認識しています。

次のラーニング パスを使用して、必要なスキルの開発を加速することができます。 これらは、基礎の学習から始めるように構成されており、インフラストラクチャ、アプリケーション、データ分析の 3 つの主要分野に分かれています。

## <a name="fundamentals"></a>基礎

- [Azure Resource Manager](/azure/azure-resource-manager/management/overview) の概要では、Azure リソースの管理とデプロイの基本的な概念について説明しています。 企業全体の監視エクスペリエンスを管理する IT スタッフは、管理スコープ、Azure ロールベースのアクセス制御 (Azure RBAC)、 Azure Resource Manager テンプレートの使用、および Azure CLI と Azure PowerShell を使用したリソースの管理について理解する必要があります。

- [Azure Policy](/azure/governance/policy/overview) の概要では、Azure Policy を使用してポリシーを作成、割り当て、管理する方法について説明しています。 Azure Policy では、Azure Monitor エージェントのデプロイと構成、Azure Monitor for VMs と Azure Security Center を使用した監視の有効化、診断設定のデプロイ、ゲスト構成設定の監査などを行うことができます。

- [Azure コマンド ライン インターフェイス](/cli/azure/get-started-with-azure-cli) (CLI) の概要。これは、Azure リソースを管理するためのクロスプラットフォーム コマンド ライン エクスペリエンスとなっています。 また、[Azure PowerShell](/powershell/azure/) の概要についても確認してください。 [Azure の管理ツールを学習する](https://www.linkedin.com/learning/learning-azure-management-tools)初級レベルのコースの一部として、Azure CLI および PowerShell プログラミング言語を対象としたセッションが LinkedIn で提供されています。

  - [Azure CLI を使用する](https://www.linkedin.com/learning/learning-azure-management-tools/use-the-azure-cli)
  - [Azure PowerShell の概要](https://www.linkedin.com/learning/learning-azure-management-tools/understand-azure-powershell)

- ポリシー、Azure ロールベースのアクセス制御、その他の Azure サービスを使用してリソースをセキュリティで保護する方法については、「[Azure にリソース管理のセキュリティを実装する](/learn/paths/implement-resource-mgmt-security)」をご覧ください。

- 「[Microsoft Azure リソースとワークロードの監視](https://www.pluralsight.com/courses/microsoft-azure-resources-workloads-monitoring-update)」では、Azure 監視ツールを使用して Azure ネットワーク リソースおよびオンプレミスにあるリソースを監視する方法について学習できます。

- 監視のデプロイの計画と設計については、「[Azure Monitor best practices and recommendations (Azure Monitor のベスト プラクティスと推奨事項)](https://www.youtube.com/watch?v=IWkqqahX_Ck&list=PLLasX02E8BPCDMuesOy2C0_TMFsoZWe_0&index=6)」をご覧ください。

## <a name="infrastructure-monitoring"></a>インフラストラクチャ監視

- 「[Design a Monitoring Strategy for Infrastructure in Microsoft Azure (Microsoft Azure でのインフラストラクチャの監視戦略の設計)](https://www.pluralsight.com/courses/microsoft-azure-monitoring-strategy-infrastructure-design-update)」では、Azure の監視機能およびソリューションの基礎知識を習得できます。

- 「[How to monitor your Kubernetes clusters (Kubernetes クラスターを監視する方法)](https://www.youtube.com/watch?time_continue=3&v=RjsNmapggPU&feature=emb_logo)」では、コンテナー用 Azure Monitor を使用して Kubernetes クラスターを監視する方法について、中級レベルの詳細情報を提供します。

- Azure Monitor を使用して、[Azure Storage および HDInsight](https://www.pluralsight.com/courses/microsoft-azure-data-storage-monitoring) からのデータを監視する方法を学びます。

- 「[Microsoft Azure Database Monitoring Playbook (Microsoft Azure のデータベース監視プレイブック)](https://www.pluralsight.com/courses/microsoft-azure-database-playbook-monitoring)」では、Azure SQL Database、Azure SQL Data Warehouse、Azure Cosmos DB で分析情報を得るために使用できる主な監視機能と実施可能な手順について説明します。

- 「[Monitoring Microsoft Azure Hybrid Cloud Networks (Microsoft Azure ハイブリッド クラウド ネットワークの監視)](https://www.pluralsight.com/courses/microsoft-azure-hybrid-cloud-networks-monitoring)」は、Azure 監視ツールを使用して、ハイブリッド クラウド実装の仮想ネットワークと仮想プライベート ネットワーク接続を視覚化、管理、最適化する方法を学習できる上級レベルのコースです。

- [Azure Arc for servers](/azure/azure-arc/servers/overview) に関する記事では、Azure で実行されている仮想マシンと同様に、Azure の外部でホストされている Windows および Linux マシンを管理する方法について説明しています。

- [VM の監視方法](https://www.youtube.com/watch?v=O7scXPrsM_0&list=PLLasX02E8BPCDMuesOy2C0_TMFsoZWe_0&index=6&t=0s)に関する動画では、Azure Monitor for VMs を使用して、ハイブリッド マシンまたはサーバー、および Azure VM または仮想マシン スケール セットを監視する方法について理解を深めるための中級レベルの詳細情報を提供します。

## <a name="application-monitoring"></a>アプリケーションの監視

- [Azure Monitor](/azure/azure-monitor/overview) を使用して、アプリケーションとサービスの可用性とパフォーマンスを 1 か所で確認する方法についてご覧ください。 Pluralsight には、役に立つ次のコースが用意されています。

  - 「[Microsoft Azure DevOps エンジニア:Optimize Feedback Mechanisms (Microsoft Azure DevOps エンジニア: フィードバック メカニズムの最適化)](https://www.pluralsight.com/courses/microsoft-azure-optimize-feedback-mechanisms)」は、Application Insights を含む Azure Monitor を使用して、Web アプリケーションの監視と最適化を準備する際に役立ちます。

  - 「[Azure Web アプリでのページの読み込み時間をキャプチャして表示する](/learn/modules/capture-page-load-times-application-insights/)」。 Azure Monitor Application Insights を使用して、Azure で実行されているアプリケーション コンポーネントのエンド ツー エンドの監視を実現する場合は、このコースから始めます。

  - 「[Microsoft Azure のデータベース監視に関するプレイブック](https://www.pluralsight.com/courses/microsoft-azure-database-playbook-monitoring)」では、Azure SQL Database、Azure SQL Data Warehouse、および Azure Cosmos DB の監視を実装して使用する方法について学習できます。

  - 「[Instrument applications with Azure Monitor Application Insights (Azure Monitor Application Insights を使用したアプリケーションのインストルメント化)](https://app.pluralsight.com/library/courses/microsoft-azure-application-insights-web-application-instrument)」では、Application Insights SDK を使用して、Angular と Node.js コンポーネントを使用するアプリケーションからテレメトリとイベントを収集する方法について詳しく説明します。

  - 「[Application Debugging and Profiling (アプリケーションのデバッグとプロファイリング)](https://www.pluralsight.com/courses/devintersection-azureai-session-31)」は、Azure Monitor Application Insights のスナップショット デバッガーとプロファイラーによって提供されるデータの使用と解釈について、Microsoft カンファレンスのセッションから記録したものです。

<!-- docutune:ignore "from Scratch" -->

## <a name="data-analysis"></a>データ分析

- [Azure Monitor でログ クエリ](/learn/modules/analyze-infrastructure-with-azure-monitor-logs/)を作成する方法をご確認ください。 Kusto クエリ言語は、Azure Monitor ログ クエリを記述するための主要リソースです。クエリでは、Azure から収集されたデータとハイブリッド リソース アプリケーション (ライブ アプリケーションなど) の依存関係の間で、ログ データを探索して分析します。

- 「[Kusto Query Language (KQL) from Scratch (ゼロから始める Kusto クエリ言語 (KQL))](https://www.pluralsight.com/courses/kusto-query-language-kql-from-scratch)」は、Azure Monitor ログでのログ分析の幅広いユースケースと手法を示す詳細な例を含む包括的なコースです。

## <a name="deeper-skills-exploration"></a>より詳細なスキル調査

スキルを伸ばすために、これらの初期のオプション以外にさまざまな学習オプションを利用できます。

### <a name="typical-mappings-of-cloud-it-roles"></a>クラウド IT の役割の典型的なマッピング

Microsoft とパートナーは、あらゆるお客様を対象に、Azure サービスに関連したスキルを開発するためのさまざまなオプションを提供しています。

- [ロールとスキルの関連付け](/azure/cloud-adoption-framework/plan/suggested-skills):クラウドのキャリア パスを関連付けるためのリソース。 クラウドでの役割と推奨されるスキルについて説明しています。 ご自分のペースで学習カリキュラムを進めて、価値ある存在であり続けるために最も必要なスキルを磨くことができます。

- Azure に関する公式の知識認定を受けるため、[Azure 認定資格のトレーニングや試験](https://www.microsoft.com/learning/certification-overview.aspx)をご覧ください。

## <a name="azure-devops-and-project-management"></a>Azure DevOps とプロジェクトの管理

ハイブリッド クラウド環境では、定義されていない役割、責任、およびアクティビティによって IT が中断されます。 組織は、アジャイルや DevOps の手法を含む、サービスを管理するための最新の手法に移行して、合理化された効率的な方法で、現在のビジネスにおける変革と最適化のニーズをより適切に満たす必要があります。

クラウド監視プラットフォームへの移行の一環として、企業内の監視の管理を担当する IT チームは、アジャイルのトレーニングと参加を DevOps アクティビティに含める必要があります。 これには、ビジネス ニーズに合わせて繰り返し調整された最小限の実行可能な監視ソリューションを提供するために、DevOps の _Dev_ に従い、要件を把握して、それを整理されたアジャイルの要件に発展させることも含まれます。 反復的な監視ソリューション パッケージとその他の関連する対象を管理するソース管理では、Azure DevOps Server プロジェクトと GitHub Enterprise Server リポジトリを関連付けます。 これにより、GitHub のコミットとプル要求からの作業項目へのリンクが提供されます。 Azure Boards を使用して作業を計画および追跡する一方で、継続的な監視の統合とデプロイのサポートにより、開発のために GitHub Enterprise を使用できます。

詳細については、以下を参照してください。

- [Azure DevOps を使用して作業を開始する](/learn/modules/get-started-with-devops)

- [DevOps 白帯道場の基礎について理解する](/learn/paths/devops-dojo-white-belt-foundation)

- [DevOps のプラクティスを進化させる](/learn/paths/evolve-your-devops-practices)

- [Azure DevOps を使用してデプロイを自動化する](/learn/paths/automate-deployments-azure-devops)

## <a name="other-considerations"></a>その他の考慮事項

多くの場合、顧客は、IT によって提供されるサービスについて、予想されるビジネス上の (および IT 組織に対する) 成果を管理、維持、および提供しようと努力します。 監視は、インフラストラクチャとビジネスを管理するための中核と見なされており、サービスとカスタマー エクスペリエンスの品質の測定に重点が置かれます。 これらの目標を達成するために、ITSM を DevOps と組み合わせて使用して基礎を作ります。これにより、監視チームは、監視サービスを管理、提供、およびサポートする方法を成熟させることができます。 ITSM フレームワークを導入することで、監視チームは、組織の戦略目標とニーズに合わせて、プロバイダーとして機能でき、信頼されたビジネス イネーブラーとして認識されるようになります。

<!-- docutune:casing "ITIL 4 and the Cloud" -->

以下を確認して、最もよく使用されている ITSM フレームワークである [ITIL 4 とクラウドに関するホワイト ペーパー](https://www.axelos.com/case-studies-and-white-papers/itil-4-and-the-cloud)に加えられた更新を理解してください。これは、既存の ITIL ガイダンスを、DevOps、アジャイル、Lean の各アプローチのベスト プラクティスと結合することに重点を置いています。 また、プロセスに依存しないフレームワークを使用して IT を変換する方法について別のブループリントを提供する、[IT4IT 参照アーキテクチャ](https://www.opengroup.org/it4it)についても検討してください。

## <a name="learn-more"></a>詳細情報

追加のラーニング パスについては、[Microsoft Learn のカタログ](/learn/browse)を参照してください。 ロール フィルターを使用して、ラーニング パスを自分のロールに合わせてください。
