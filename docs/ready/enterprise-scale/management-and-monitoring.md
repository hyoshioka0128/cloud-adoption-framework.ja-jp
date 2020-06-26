---
title: 管理と監視
description: 管理と監視。
author: BrianBlanchard
ms.author: brblanch
ms.date: 06/15/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.openlocfilehash: 46f98753dda7590daa725c5bd4c0c43a53190871
ms.sourcegitcommit: 9b183014c7a6faffac0a1b48fdd321d9bbe640be
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/19/2020
ms.locfileid: "85077118"
---
# <a name="management-and-monitoring"></a>管理と監視

## <a name="planning-platform-management-and-monitoring"></a>プラットフォームの管理と監視の計画

このセクションでは、プラットフォーム レベルで一元的な管理と監視を行い、Microsoft Azure のエンタープライズ資産を運用的に保守する方法について説明します。 具体的には、中央のチームが大規模な Azure プラットフォーム内で運用の可視性を維持するための主要な推奨事項を提示します。

![管理と監視](./media/management-and-monitoring.png)

"_図 1:プラットフォームの管理と監視。_

<!-- cSpell:ignore syslogs SIEM -->

**設計上の考慮事項:**

- Azure Monitor Log Analytics ワークスペースを管理境界として使用します。

- メトリックとログについて、ホットとコールドの両方のテレメトリ パスをそれぞれ網羅する、アプリケーション中心のプラットフォーム監視。

  - オペレーティング システムのメトリック (パフォーマンス カウンターとカスタム メトリック)

  - オペレーティング システム ログ (インターネット インフォメーション サービス (IIS)、Windows のイベント トレーシング、および syslog)

  - リソースの正常性イベント

- セキュリティ監査のログ記録と、お客様の Azure 資産全体にわたる水平方向のセキュリティ レンズの実現:

  - オンプレミスのセキュリティ情報や、ServiceNow や ArcSight などのイベント管理 (SIEM) システムとの潜在的な統合

  - Azure アクティビティ ログ

  - Azure AD の監査レポート

  - Azure の診断サービス、ログ、およびメトリック、Azure Key Vault 監査イベント、ネットワーク セキュリティ グループ (NSG) フロー ログ、およびイベント ログ

  - Azure Monitor、Network Watcher、Security Center、Azure Sentinel

- Azure のデータ保持のしきい値とアーカイブ要件:

  - Azure Monitor ログの既定の保持期間は 30 日であり、最大 2 年です

  - Azure Active Directory レポート (Premium) の既定の保持期間は 30 日です

  - Azure 診断サービスの既定の保持期間は 90 日です

- 運用上の要件:

  - Azure Monitor ログやサードパーティ ツールなどのネイティブ ツールを備えた運用ダッシュボード

  - 一元化されたロールを使用した特権アクティビティの制御

  - Azure サービスにアクセスするための [Azure リソースのマネージド ID](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

  - リソースの編集と削除を保護するリソースのロック

**設計上の推奨事項:**

- ロールベースのアクセス制御 (RBAC) とデータ主権の要件により個別のワークスペースが必要な場合を除き、1 つの[モニター ログ ワークスペース](https://docs.microsoft.com/azure/azure-monitor/platform/design-logs-deployment)を使用してプラットフォームを一元管理します。 一元的なログ記録は、運用管理チームに必要な可視性に不可欠です。 ログ記録の一元化によって、変更管理、サービス正常性、構成、および IT 運用の他のほとんどの側面に関するレポートが容易になります。 一元化されたワークスペース モデルに集約することで、管理作業が軽減され、可観測性のギャップが生じる可能性が低くなります。

エンタープライズ規模のアーキテクチャのコンテキストでは、一元化されたログ記録は主にプラットフォームの操作に関係しています。 これは、VM ベースのアプリケーションのログ記録に同じワークスペースを使用することを妨げるものではありません。 リソース中心のアクセス制御モードで構成されたワークスペースでは、アプリ チームが自身のリソースのログにのみアクセスできるように、詳細な RBAC が適用されます。 このモデルでは、既存のプラットフォーム インフラストラクチャを使用することで、管理のオーバーヘッドを軽減できるというメリットがアプリ チームにあります。 非コンピューティング リソース (Web アプリや Azure Cosmos DB データベースなど) の場合、アプリケーション チームでは独自の Log Analytics ワークスペースを使用し、そこにルーティングされる診断とメトリックを構成できます。

<!-- docsTest:ignore WORM -->

- ログの保持期間の要件が 2 年を超える場合は、ログを Azure Storage にエクスポートします。 WORM (書き込み 1 回、読み取り多数) ポリシーが設定された不変ストレージを使用して、ユーザーが指定した間隔でデータを消去不可および変更不可にします。

- アクセス制御とコンプライアンス レポートには Azure Policy を使用します。 これにより、組織全体の設定を適用して、一貫したポリシーの準拠と迅速な違反検出を確実に行うことができます。 詳細については、「[Azure Policy の効果について](https://docs.microsoft.com/azure/governance/policy/concepts/effects)」を参照してください。

- Azure Policy を使用して、ゲスト仮想マシン (VM) の構成内のドリフトを監視します。 ポリシーを使用して[ゲスト構成](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration)監査機能を有効にすると、ほとんど苦労することなく、アプリ チームのワークロードで、すぐに機能を使用できるようになります。

- Windows VM と Linux VM の両方の長期的なパッチ適用メカニズムとして、[Azure Automation の更新管理](https://docs.microsoft.com/azure/automation/automation-update-management)を使用します。 ポリシーを使用して更新管理構成を適用すると、すべての VM がパッチ管理処理に確実に含まれ、アプリケーション チームに VM のパッチ デプロイを管理する機能が提供され、すべての VM にわたる可視性と実施機能が中央の IT チームに提供されます。

- Azure Network Watcher を使用して、[Network Watcher NSG フロー ログ v2](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) を使用してトラフィック フローの予防的な監視を行います。 [Traffic Analytics](https://docs.microsoft.com/azure/network-watcher/traffic-analytics) を使用すると、NSG のフロー ログを分析し、仮想ネットワーク内の IP トラフィックに関する詳細な分析情報を収集し、効果的な管理と監視のための重要な情報を得ることができます。 Traffic Analytics を使用すると、通信量の多いホストやアプリのプロトコル、会話が多いホスト ペア、許可またはブロックされたトラフィック、受信または送信トラフィック、開いているインターネット ポート、ブロックの多いルール、Azure データ センター、仮想ネットワーク、サブネット、または承認されていないネットワークごとのトラフィック分布などの情報を得ることができます。

- 重要な共有サービスが誤って削除されないように、リソース ロックを使用します。

- Azure AD RBAC の割り当てを補うために、[拒否ポリシー](https://docs.microsoft.com/azure/governance/policy/concepts/effects#deny)を使用します。 拒否ポリシーを使用すると、要求がリソース プロバイダーに送信されないようにすることで、定義された標準に一致しないリソースのデプロイと構成を防ぐことができます。 拒否ポリシーと RBAC の割り当てを組み合わせることで、適切なガードレールが配置され、**誰が**リソースをデプロイおよび構成できるかと、**どの**リソースをデプロイおよび構成できるかが適用されます。

- プラットフォーム監視ソリューション全体の一部として、[サービス](https://docs.microsoft.com/azure/service-health/service-health-overview)と[リソース](https://docs.microsoft.com/azure/service-health/resource-health-overview)の正常性イベントを含めます。 プラットフォームの観点からサービスとリソースの正常性を追跡することは、Azure のリソース管理の重要なコンポーネントです。

- 生のログ エントリをオンプレミスの監視システムに送信しないでください。 代わりに、"*Azure で生まれたデータは Azure に保持する*" という原則を採用します。 オンプレミスの SIEM 統合が必要な場合は、ログではなく[重要なアラートを送信](https://docs.microsoft.com/azure/security-center/continuous-export)します。

## <a name="planning-for-app-management-and-monitoring"></a>アプリの管理と監視の計画

前のセクションをさらに詳しく説明するために、このセクションでは、お客様のアプリのワークロードのフェデレーション管理と監視について検討し、アプリケーション チームでこれらのワークロードを運用で保守する方法について説明します。

**設計上の考慮事項:**

- アプリケーションの監視には、専用の Log Analytics ワークスペースを使用できます。

- 仮想マシンにデプロイされるアプリケーションの場合、ログは、プラットフォームの観点から専用の Log Analytics ワークスペースに一元的に保存するようにします。また、アプリケーション チームは、アプリケーションおよび仮想マシン上にある RBAC の対象となるログにアクセスできます。

- サービスとしてのインフラストラクチャ (IaaS) およびサービスとしてのプラットフォーム (PaaS) リソースの両方に関するアプリのパフォーマンスと正常性の監視

- すべてのアプリ コンポーネントでのデータ集計

- [正常性モデルと運用化](https://docs.microsoft.com/azure/cloud-adoption-framework/manage/monitor/cloud-models-monitor-overview):

  - ワークロードとそのサブシステムの正常性を測定する方法
  - 正常性を表す信号モデル
  - 複数のアプリ コンポーネントにまたがるエラーに対応する方法

**設計上の推奨事項:**

- 一元化された Azure Monitor Log Analytics ワークスペースを使用して、IaaS および PaaS アプリのリソースからログとメトリックを収集し、[RBAC を使用してログ アクセスを制御](https://docs.microsoft.com/azure/azure-monitor/platform/design-logs-deployment#access-control-overview)します。

- 時間の影響を受ける分析には、[Azure Monitor メトリック](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics)を使用します。 Azure Monitor のメトリックは、タイムスタンプ付きデータを分析するために最適化された時系列データベースに格納されます。 これにより、アラートに適したメトリックになり、問題をすばやく検出できます。 それらによって、システムがどのように実行されているかを知ることができますが、通常は、問題の根本原因を識別するためにログと組み合わせる必要があります。

- 分析情報とレポートのために [Azure Monitor ログ](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs)を使用します。 ログにはさまざまな種類のデータが含まれ、さまざまなプロパティのセットを持つレコードに編成されており、パフォーマンス データ、イベント、トレースなど、さまざまなソースからの複雑なデータを分析するために役立ちます。

- 必要に応じて、ランディング ゾーン内の共有ストレージ アカウントを Azure 診断拡張機能のログ ストレージに使用します。

- 操作のアラートの生成には、[Azure Monitor のアラート](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)を使用します。 Azure Monitor アラートを使用すると、メトリックとログのアラートを統合し、高度な管理と修復の目的でアクションやスマート グループなどの機能を使用することができます。
