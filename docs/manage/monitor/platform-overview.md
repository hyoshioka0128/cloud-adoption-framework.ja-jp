---
title: クラウド監視ガイド – 監視プラットフォームの概要
titleSuffix: Microsoft Cloud Adoption Framework for Azure
description: Microsoft Azure で Azure Monitor または System Center Operations Manager を使用するタイミングを選択します
author: mgoedtel
ms.author: magoedte
ms.date: 07/31/2019
ms.topic: guide
ms.service: cloud-adoption-framework
ms.subservice: operate
services: azure-monitor
ms.openlocfilehash: 33d9647a0804859a611d45e130c753cab89a6ef6
ms.sourcegitcommit: 443c28f3afeedfbfe8b9980875a54afdbebd83a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/16/2019
ms.locfileid: "71031168"
---
# <a name="cloud-monitoring-guide-overview-of-our-monitoring-platforms"></a>クラウド監視ガイド: 監視プラットフォームの概要

Microsoft では、2 つの製品からさまざまな監視機能を提供しています。オンプレミス向けに設計された後クラウドに拡張された System Center Operations Manager と、クラウド向けに設計されたがオンプレミス システムも監視できる Azure Monitor です。 これら 2 つのオファリングでは、アラート、サービス稼働時間の追跡、アプリケーションとインフラストラクチャの正常性の監視、診断、分析などのコア監視サービスが提供されます。

多くの組織は、異種環境を管理するため、DevOps の俊敏性とクラウドのイノベーションに向けた最新のプラクティスを採用しています。 それでも、これらのワークロードを監視する方法に関して適切で信頼できる意思決定を下すことができるか、懸念も抱いています。

この記事では、Microsoft の監視プラットフォームの概要を説明し、両方がコア監視機能を提供するしくみを理解できるようにします。

## <a name="story-of-system-center-operations-manager"></a>System Center Operations Manager のストーリー

弊社は 2000 年に Microsoft Operations Manager (MOM) 2000 で運用管理の分野に参入しました。 2007 年には、System Center Operations Manager という名前の、その製品の再設計バージョンを導入しました。 これは、Windows サーバーの単純な監視にとどまらず、異種プラットフォーム、ネットワーク デバイス、その他のアプリケーションやサービスの依存関係を含むサービスおよびアプリケーションの、堅牢なエンドツーエンドの監視に注力したものでした。 これは、業界において IBM Tivoli や HP Operations Manager と同じクラスに属する、確立されたエンタープライズ レベルのオンプレミス環境向け監視プラットフォームでした。 それが、Azure、アマゾン ウェブ サービス (AWS)、およびその他のクラウド プロバイダーで実行されているコンピューティングとプラットフォームのリソースの監視をサポートするように拡大してきました。

## <a name="story-of-azure-monitor"></a>Azure Monitor のストーリー

2010 年に Azure がリリースされた時点では、クラウド サービスの監視は Azure Diagnostics エージェントによって提供されていました。これは、Azure リソースから診断データを収集する方法を提供するものでした。 この機能は、エンタープライズクラスの監視プラットフォームではなく、一般的な監視ツールと見なされていました。  

Application Insights は、クラウド、モバイル、IoT デバイスが急増する業界の変化と DevOps プラクティスの導入に合わせて移行するために導入されました。 Operations Manager でのアプリケーション パフォーマンス監視から Azure のサービスに拡大し、さまざまな言語で記述された Web アプリケーションの監視機能を豊富に提供します。 2015 年、Visual Studio 向けの Application Insights のプレビューが発表され、これは後に、単に Application Insights と呼ばれるようになりました。 このサービスでは、アプリケーションのパフォーマンス、要求と例外、トレースの詳細が収集されます。

2015 年には、Azure Operational Insights が一般公開されました。 それにより、Azure、オンプレミス、またはその他のクラウド環境にあるコンピューターのデータを収集および検索し、System Center Operations Manager に接続する Log Analytics 分析サービスを提供しました。 セキュリティ監査、正常性の評価、アラート管理などのシナリオ用に、クエリおよび分析ロジック、視覚化、データ収集ルールのコレクションが含まれているインテリジェンス パックが提供され、あらかじめパッケージ化されたさまざまな管理および監視の構成が用意されました。  その後、Azure Operational Insights は Log Analytics と呼ばれるようになりました。  

2016 年、Azure Monitor のプレビューが Ignite で発表されました。 それにより、フレームワークの使用を開始した任意の Azure サービスからプラットフォームのメトリック、リソース診断ログ、サブスクリプションレベルのアクティビティ ログ イベントを収集するための共通フレームワークが提供されました。 以前は、各 Azure サービスに独自の監視方法がありました。

2018 年の Microsoft Ignite カンファレンスでは、元は独立した機能を持って開発された複数の異なるサービスを含むように Azure Monitor ブランドを拡張したことを発表しました。

- Azure プラットフォームのリソースのみを対象に、プラットフォームのメトリック、リソース診断ログ、およびアクティビティ ログを収集する、オリジナルの **Azure Monitor** 機能。
- アプリケーション監視のための **Application Insights**。
- ログ データの収集と分析の主要な場所である **Log Analytics**。
- 前に説明した他の各サービスのアラート メカニズムを統合した新しい**統合アラート サービス**。  
- **Azure Network Watcher** では、Azure 仮想ネットワーク内のリソースの監視、診断、メトリックの表示ができます。

## <a name="the-story-of-operations-management-suite-oms"></a>Operations Management Suite (OMS) のストーリー

2015 年から 2018 年 4 月まで、Operations Management Suite (OMS) は、ライセンス目的で以下の Azure 管理サービスをバンドルしたものでした。

- Application Insights
- Azure Automation
- Azure Backup
- Operational Insights (後に Log Analytics にブランド変更)
- Site Recovery

OMS に含まれていたサービスの機能は、OMS が廃止されても変更されず、Azure Monitor の下で再編されました。

## <a name="infrastructure-requirements"></a>インフラストラクチャの要件

### <a name="operations-manager"></a>Operations Manager

Operations Manager には、管理グループをサポートするための重要なインフラストラクチャとメンテナンスが必要であり、それは機能の基本単位です。 管理グループは少なくとも、1 台以上の管理サーバー、運用およびレポート作成用のデータ ウェアハウス データベースをホストする SQL Server、エージェントで構成されます。 管理グループの設計の複雑さは、監視するワークロードの範囲、ワークロードをサポートするデバイスまたはコンピューターの数など、多数の要因によって決まります。 エンタープライズ監視プラットフォームの一般的な特徴として、高可用性とサイトの回復性が必要な場合に、インフラストラクチャの要件が大きく上がり、必要なメンテナンスも大幅に増加する可能性があります。

![Operations Manager 管理グループの図](./media/monitoring-management-guidance-cloud-and-on-premises/operations-manager-management-group-optimized.svg)

### <a name="azure-monitor"></a>Azure Monitor

Azure Monitor は、それをサポートするすべてのインフラストラクチャが Azure で実行され、Microsoft によって管理される SaaS サービスです。 監視、分析、診断を大規模に実行するように設計されており、すべての国のクラウドで利用可能です。 Azure Monitor をサポートするために必要なインフラストラクチャのコア部分 (コレクター、メトリックとログ ストア、分析) は、Microsoft によってメンテナンスされます。  

![Azure Monitor の図](./media/monitoring-management-guidance-cloud-and-on-premises/azure-monitor-greyed-optimized.svg)

## <a name="data-collection"></a>データ収集

### <a name="operations-manager"></a>Operations Manager

#### <a name="agents"></a>エージェント

Operations Manager では、[Windows コンピューター](https://docs.microsoft.com//system-center/scom/plan-planning-agent-deployment?view=sc-om-1807#windows-agent)にインストールされたエージェントからのみ、データが直接収集されます。 Operations Manager SDK からのデータを受け入れることができますが、これは通常、監視データの収集ではなく、カスタム アプリケーションによって製品を拡張するパートナーのために使用されます。 [Linux コンピューター](https://docs.microsoft.com/system-center/scom/plan-planning-agent-deployment?view=sc-om-1807#linuxunix-agent)やネットワーク デバイスなどの他のソースからのデータ収集は、これらの他のデバイスにリモートからアクセスする Windows エージェントで実行される特別なモジュールを使用して行うことができます。

![Operations Manager エージェントの図](./media/monitoring-management-guidance-cloud-and-on-premises/data-collection-opsman-agents-optimized.svg)

Operations Manager エージェントでは、ローカル コンピューター上のイベント ログ、カスタム ログ、パフォーマンス カウンターなどの複数のデータ ソースからの収集が可能です。 スクリプトを実行することもできます。スクリプトにより、ローカル コンピューターから、または外部ソースからデータを収集できます。 他の方法では収集できないデータや、他の方法では監視できないさまざまなリモート デバイスからデータを収集するために、カスタム スクリプトを作成できます。

#### <a name="management-packs"></a>管理パック

Operations Manager は、ワークフロー (ルール、モニター、およびオブジェクト検出) を使用してすべての監視を実行します。 これらのパッケージは一緒に[管理パック](https://docs.microsoft.com/system-center/scom/manage-overview-management-pack?view=sc-om-2019)にまとめられて、エージェントに配置されます。 管理パックは、事前定義されたルールとモニターを含むさまざまな製品やサービスで使用できます。 独自のアプリケーションやカスタム シナリオ用に独自の管理パックを作成することもできます。

#### <a name="monitoring-configuration"></a>監視構成

管理パックには、何百ものルール、モニター、およびオブジェクト検出ルールを含めることができます。 エージェントは、これらすべての監視設定を、(検出ルールによって決定される) 該当するすべての管理パックから実行します。 各監視設定の各インスタンスは独立して実行され、収集したデータに対して直ちに作用します。 この方法で、Operations Manager は、監視対象リソースのほぼリアルタイムのアラートを実現し、現在の正常性状態を取得することができています。

たとえば、モニターによってパフォーマンス カウンターが数分ごとにサンプリングされます。 そのカウンターがしきい値を超えると、すぐにそのターゲット オブジェクトの正常性状態が設定され、それによって直ちに管理グループでアラートがトリガーされます。 スケジュールされたルールを使用して、特定のイベントが作成されるのを監視し、そのイベントがローカル イベント ログ内に作成されたらすぐにアラートを発生させることができます。

これらの監視設定は互いに分離されており、個々のデータ ソースから機能するため、Operations Manager には複数のソース間でデータを関連付けることについての課題があります。 また、収集された後のデータに対応することは困難です。 Operations Manager データベースにアクセスするワークフローを実行することはできますが、このシナリオは一般的ではなく、通常は限られた数の特殊目的のワークフローに使用されます。

![Operations Manager 管理グループの図](./media/monitoring-management-guidance-cloud-and-on-premises/operations-manager-management-group-optimized.svg)

### <a name="azure-monitor"></a>Azure Monitor

#### <a name="data-sources"></a>データ ソース

Azure Monitor では、Azure のインフラストラクチャとプラットフォームのリソース、Windows コンピューターと Linux コンピューター上のエージェント、Azure ストレージ内に収集された監視データなど、さまざまなソースからデータが収集されます。 任意の REST クライアントで API を使用して Azure Monitor にログ データを書き込むことができ、お使いの Web アプリケーション用にカスタム メトリックを定義できます。 一部のメトリックデータは、その用途に応じて異なる場所にルーティングできます。 たとえば、データを "可能な限り迅速な" アラートに使用したり、他のログ データと共に検索する長期的な傾向分析に使用したりすることができます。

#### <a name="monitoring-solutions-and-insights"></a>監視ソリューションと分析情報

監視ソリューションは、Azure Monitor のログ プラットフォームを利用して、特定のアプリケーションまたはサービスの監視が提供します。 通常はエージェントまたは Azure サービスからのデータ収集が定義され、そのデータを分析するためのログ クエリとビューが提供されます。 通常、アラート ルールは提供されません。つまり、収集されたデータに基づくご自身の独自のアラート基準を定義する必要があります。

分析情報には、Azure Monitor for Containers や Azure Monitor for VMs のように、Azure Monitor のログとメトリックのプラットフォームが利用され、Azure portal で、アプリケーションまたはサービス用にカスタマイズされた監視エクスペリエンスが提供されます。 収集されたデータのカスタマイズされた分析に加えて、正常性の監視とアラートの条件が提供されることもあります。

#### <a name="monitoring-configuration"></a>監視構成

Azure Monitor では、データ収集は、そのデータに対して実行されるアクションから分離されています。これにより、クラウド環境で分散マイクロサービスがサポートされます。 複数のソースからのデータが共通のデータ プラットフォームに統合され、その収集されたデータに基づいて、分析、視覚化、アラートの各機能が提供されます。

Azure Monitor によって収集されたすべてのデータは、ログまたはメトリックとして保存され、Monitor のさまざまな機能はどちらかに依存します。 メトリックには、ほぼリアルタイムでのアラートと問題の迅速な検出に最適な、時系列の数値が含まれています。 ログにはテキストまたは数値データが含まれており、ログは、複雑な分析を実行する場合に特に便利な強力なクエリ言語によってサポートされています。

Monitor ではデータ収集とそのデータに対するアクションが分離されているため、多くの場合に、ほぼリアルタイムのアラートを提供できない可能性があります。 ログ データのアラートを実行するために、アラートで定義された定期的なスケジュールでクエリが実行されます。 この動作により、Azure Monitor では監視対象のすべてのソースからのデータを簡単に関連付けることが可能となっており、さまざまな方法で対話的にデータを分析できます。 これは、根本原因分析を行い、問題が起きる可能性がある他の箇所を特定するために特に役立ちます。

## <a name="health-monitoring"></a>正常性の監視

### <a name="operations-manager"></a>Operations Manager

Operations Manager の管理パックには、監視対象アプリケーションのコンポーネントとそれらの関係を記述するサービス モデルが含まれています。 エージェント上のデータとスクリプトに基づいて、各コンポーネントの現在の正常性状態がモニターによって識別されます。 正常性状態はロールアップされるため、監視対象のコンピューターとアプリケーションの正常性状態の概要がすぐにわかります。

### <a name="azure-monitor"></a>Azure Monitor

Azure Monitor では、サービス コンポーネントの現在の正常性状態を示すモニターのサービス モデルを実装するための、ユーザーが定義可能な方法は提供されていません。 監視ソリューションは Azure Monitor の標準機能に基づいているため、状態レベルの監視は提供されません。 Azure Monitor の以下の機能が役に立つ場合があります。

- **Application Insights** は、Web アプリケーションの複合マップを構築し、各アプリケーション コンポーネントまたは依存関係についての正常性状態を提供します。 これにはアラートの状態と、アプリケーションのより詳細な診断へのドリルダウンが含まれます。

- **Azure Monitor for VMs** では、ゲスト Azure VM で Windows および Linux の仮想マシンを監視しているときに、Operations Manager と同様の正常性監視エクスペリエンスが提供されます。 可用性とパフォーマンスの観点から、主なオペレーティング システム コンポーネントの正常性が評価され、現在の正常性状態が判断されます。 ゲスト VM で、リソース使用率、ディスク領域の容量、またはオペレーティング システムのコア機能に関連する問題が継続的に発生していると判断されると、この状態について注意を促すためのアラートが生成されます。

- **Azure Monitor for Containers** では、Azure Kubernetes Services または Azure Container Instances のパフォーマンスと正常性が監視されます。 この機能により、Kubernetes で使用可能なコントローラー、ノード、およびコンテナーから、メモリやプロセッサのメトリックが Metrics API 経由で収集されます。 コンテナー ログ、コンテナーに関するインベントリ データ、それらのイメージも収集されます。 収集されたパフォーマンスデータに基づく事前定義された正常性基準は、リソースのボトルネックや容量の問題があるかどうかを特定するのに役立ちます。 全体的なパフォーマンスや、特定の種類の Kubernetes オブジェクト (ポッド、ノード、コントローラー、またはコンテナー) のパフォーマンスを理解することもできます。

## <a name="analyzing-data"></a>データの分析

### <a name="operations-manager"></a>Operations Manager

Operations Manager には、データを収集後に分析するための基本的な 4 つの方法が用意されています。

- **ヘルス エクスプローラー**を使用すると、どのモニターが正常性状態の問題を特定しているかを調べ、そのモニターに関する知識とそれに関連するアクションの考えられる原因を確認できます。

- **ビュー**は、収集されたデータを事前定義どおりに視覚化したものです (パフォーマンス データのグラフや、監視対象コンポーネントとそれらの現在の正常性状態の一覧など)。 ダイアグラム ビューは、アプリケーションのサービス モデルを視覚的に表したものです。

- **レポート**を使用すると、Operations Manager データ ウェアハウスに格納されている履歴データを集計することができます。 ビューとレポートの基になるデータをカスタマイズできます。 ただし、収集されたデータの複雑な分析や対話型の分析を可能にする機能はありません。

- **Operations Manager コマンドシェル** (Windows PowerShell を一連の追加コマンドレットで拡張したもの) では、収集されたデータのクエリと視覚化を行えます。 これには、PowerShell でネイティブに行うか、Operations Manager の HTML ベースの Web コンソールを使用して行う、グラフを始めとする視覚化が含まれます。

### <a name="azure-monitor"></a>Azure Monitor

Azure Monitor には強力な分析エンジンが用意されており、ログ データを対話的に操作したり、これらを他の監視データと組み合わせて、傾向分析やその他のデータ分析を行うことができます。 ビューとダッシュボードを使用すると、Azure portal からさまざまな方法でクエリ データを視覚化し、Power BI にインポートすることができます。 監視ソリューションには、クエリおよび収集したデータを表示するビューが含まれます。 Application Insights、Azure Monitor for VMs、Azure Monitor for Containers などの分析情報には、対話型の監視シナリオをサポートするためにカスタマイズされた視覚化が含まれます。

## <a name="alerting"></a>アラート

### <a name="operations-manager"></a>Operations Manager

Operations Manager では、パフォーマンスのしきい値に達したとき、および監視対象コンポーネントの正常性状態が変化したときに、事前定義されたイベントに応じてアラートが作成されます。 これにはアラートの完全な管理が含まれており、アラートの解決方法を設定したり、アラートを別のオペレーターまたはシステム エンジニアに割り当てたりすることができます。 どのアラートでプロアクティブな通知を送信するかを指定する通知ルールをご自分で設定できます。

管理パックには、監視対象アプリケーション内でのさまざまな重大な状況に対するさまざまな定義済みのアラート ルールが含まれています。 ご自身の環境の特定の要件に合わせて、これらのルールを調整するか、カスタム ルールを作成することができます。

### <a name="azure-monitor"></a>Azure Monitor

Azure Monitor では、しきい値を超過したメトリックや、スケジュールされたクエリの結果に基づくアラートを作成できます。 メトリックに基づくアラートでは、ほぼリアルタイムの結果を達成できます。一方、スケジュールされたクエリでは、データ インジェストとインデックス付けの速度に応じて、応答時間がより長くなります。 Azure Monitor のログ クエリ アラートを使用すると、特定のエージェントに限定されるのではなく、複数のワークスペースに格納されているすべてのデータにわたってデータ分析を行うことができます。 これらのアラートには、クロスワークスペース クエリを使用することで、特定の Application Insights アプリからのデータも含められます。

監視ソリューションにアラート ルールが含まれている場合がありますが、通常は独自の要件に基づいてユーザーがアラート ルールを作成します。

## <a name="workflows"></a>Workflows

### <a name="operations-manager"></a>Operations Manager

Operations Manager の管理パックには、何百もの特有のワークフローが含まれており、収集するデータとそのデータに対して実行するアクションの両方が定められています。 たとえば、あるルールでは、数分ごとにパフォーマンス カウンターがサンプリングされ、分析のために結果が保存されます。 モニターで同じパフォーマンス カウンターをサンプリングすると、その値をしきい値と比較して、監視対象オブジェクトの正常性状態を判断することができます。 別のルールでスクリプトを実行し、エージェント コンピューター上のデータを収集および分析して、特定の値が返された場合にアラートを発生させることもできます。

Operations Manager のワークフローは相互に独立しているため、複数の監視対象オブジェクトにわたる分析は困難です。 これらの監視シナリオは、収集後のデータに基づいている必要があります。それは可能ですが困難な場合があり、一般的ではありません。

### <a name="azure-monitor"></a>Azure Monitor

Azure Monitor では、データ収集は、そのデータから実施されるアクションと分析から分離されています。 エージェントやその他のデータ ソースは、ログ データを Log Analytics ワークスペースに書き込み、メトリック データをメトリック データベースに書き込みます。そのデータの分析はまったく行われず、その利用方法は認識されません。 Monitor は、格納されたデータに基づいてアラートやその他のアクションを実行します。これにより、すべてのソースのデータにわたって分析を実行できるようになっています。

## <a name="extending-base-platform"></a>基本プラットフォームの拡張

### <a name="operations-manager"></a>Operations Manager

Operations Manager では、すべての監視ロジックは管理パックに実装されています。管理パックは、独自に作成するか、Microsoft またはパートナーから入手します。 管理パックをインストールすると、さまざまなエージェント上のアプリケーションまたはサービスのコンポーネントが自動的に検出され、適切なルールとモニターが展開されます。 管理パックには、インフラストラクチャ サービスまたはアプリケーションをサポートする完全な監視を提供するため、正常性の定義、アラート ルール、パフォーマンスとイベントの収集ルール、ビューが含まれます。

Operations Manager SDK を使用すると、Operations Manager をサードパーティ製の監視プラットフォームや ITSM ソフトウェアと統合できます。 SDK は、ネットワーク デバイスの監視をサポートするため、および Squared Up HTML5 ダッシュボードや Microsoft Office Visio との統合などのカスタム プレゼンテーション エクスペリエンスを提供するためにも、一部のパートナー管理パックによって使用されます。

### <a name="azure-monitor"></a>Azure Monitor

Azure Monitor では、構成をほとんどしないかまったくしなくても、Azure リソースからメトリックとログが収集されます。 監視ソリューションによって、アプリケーションまたはサービスを監視するためのロジックが追加されますが、Monitor の標準のログ クエリおよびビュー内で引き続き使用できます。 Application Insights や Azure Monitor for VMs などの情報分析ツールでは、データの収集と処理に Monitor プラットフォームが使用されますが、データを視覚化して分析するための追加のツールも用意されています。 ログ クエリやアラートなどの Monitor のコア機能を使用して、情報分析ツールによって収集されたデータを他のデータと組み合わせることができます。

Monitor では、Azure または外部リソースから監視データや管理データを収集する方法がいくつかサポートされています。 その後は、メトリックまたはログのストアからデータを抽出して ITSM や監視ツールに転送したり、Azure Monitor REST API を使用して管理タスクを実行したりすることができます。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [クラウド デプロイ モデルの監視](./cloud-models-monitor-overview.md)