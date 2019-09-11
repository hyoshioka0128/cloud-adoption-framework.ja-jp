---
title: クラウド監視ガイド – クラウド デプロイ モデルの監視戦略
titleSuffix: Microsoft Cloud Adoption Framework for Azure
description: Microsoft Azure で Azure Monitor または System Center Operations Manager を使用するタイミングを選択します
author: MGoedtel
ms.author: magoedte
ms.date: 07/31/2019
ms.topic: guide
ms.service: cloud-adoption-framework
ms.subservice: operate
services: azure-monitor
ms.openlocfilehash: 1090701c866d5740a57fde270cb33b685a5da2ca
ms.sourcegitcommit: a26c27ed72ac89198231ec4b11917a20d03bd222
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/06/2019
ms.locfileid: "70839393"
---
# <a name="cloud-monitoring-guide-monitoring-strategy-for-cloud-deployment-models"></a>クラウド監視ガイド: クラウド デプロイ モデルの監視戦略

この記事では、次の基準に基づいて、クラウド デプロイ モデルごとに推奨する監視戦略について説明します。

- Operations Manager またはその他のエンタープライズ監視プラットフォームへの継続的なコミットメントが必要です。 これは、IT 運用のプロセス、ナレッジ、専門知識との統合のため、または特定の機能が Azure Monitor でまだ利用できないからです。
- あなたはオンプレミスとパブリック クラウドの両方で、またはクラウド内だけでワークロードを監視する必要があります。
- あなたのクラウド移行戦略には、IT 運用の最新化や、Microsoft のクラウド監視サービスおよびソリューションへの移行が含まれます。
- エアギャップにより、または物理的に隔離され、プライベート クラウドまたは物理ハードウェアでホストされ、かつ監視を必要とする重要なシステムをあなたは備えている場合があります。

完全なサービス指向の監視の観点を提供するために、Microsoft の戦略には、インフラストラクチャ (コンピューティング、ストレージ、サーバーのワークロード)、アプリケーション (エンド ユーザー、例外、クライアント)、ネットワーク リソースを監視するためのサポートが含まれています。

## <a name="azure-cloud-monitoring"></a>Azure クラウドの監視

Azure Monitor は、Azure リソースを監視するための 1 つのソースを提供するプラットフォーム サービスです。 Azure 上に構築されるクラウド ソリューションで、VM ワークロードに基づいたビジネス機能をサポートするもの、または、マイクロサービスやその他のプラットフォーム リソースを利用する複雑なアーキテクチャをサポートするもの向けに設計されています。 Azure Active Directory Domain Services などのテナント サービスから、サブスクリプション レベルのイベント、Azure サービスの正常性まで、スタックのすべてのレイヤーを監視します。 VM、ストレージ、ネットワーク リソースなどのインフラストラクチャ リソース、そして最上位レイヤーのアプリケーションも監視します。 これらの各依存関係を監視し、それぞれが発する可能性がある適切な信号を収集することで、アプリケーションや主要インフラストラクチャを必要に応じて観察できるようになります。

次の表は、スタックの各レイヤーを監視するための推奨される方法をまとめたものです。

<!-- markdownlint-disable MD033 -->

レイヤー | リソース | Scope (スコープ) | 方法
---|---|---|----
Application | Azure VM、Azure App Services、Azure Service Fabric、Azure Functions、および Azure Cloud Services 上の .NET、.NET Core、Java、JavaScript、および Node.js プラットフォームで実行されている Web ベースのアプリケーション | ライブ Web アプリケーションを監視して、パフォーマンスの異常を自動検出し、コードの例外と問題を識別し、操作性に関するテレメトリを収集します。 |  Application Insights
Containers | Azure Kubernetes Service/Azure Container Instances | コンテナーおよびコンテナー インスタンス上で実行されるワークロードの容量、可用性、およびパフォーマンスを監視します。 | コンテナーに対する Azure Monitor
ゲスト オペレーティング システム | Linux および Windows VM オペレーティング システム | 容量、可用性、およびパフォーマンスを監視します。 各 VM 上でホストされている依存関係をマップします。これには、サーバー間のアクティブなネットワーク接続の可視性、受信接続および送信接続の待機時間、任意の TCP 接続アーキテクチャでのポートなどが含まれます。 | VM に対する Azure Monitor
Azure リソース - PaaS | Azure Database サービス (SQL や mySQL など) | Azure Database for SQL のパフォーマンス メトリック。 | SQL データを Azure Monitor ログにストリーミングする診断ログを有効にします。
Azure リソース - IaaS | 1.Azure Storage<br/> 2.Azure Application Gateway<br/> 手順 3.Azure Key Vault<br/> 4.ネットワーク セキュリティ グループ<br/> 5.Azure の Traffic Manager | 1.容量、可用性、およびパフォーマンス。<br/> 2.パフォーマンスと診断ログ (アクティビティ、アクセス、パフォーマンス、およびファイアウォール)。<br/> 手順 3.ご利用の Key Vault がいつ、だれによって、どのような方法でアクセスされたかを監視します。<br/> 4.ルールが適用されたときのイベントと、拒否または許可を行うためにルールが適用された回数を示すルール カウンターを監視します。<br/>5.エンドポイントの状態の可用性を監視します。 | 1.Blob Storage のストレージ メトリック。<br/> 2.診断ログを有効にし、Azure Monitor ログへのストリーミングを構成します。<br/> 手順 3.診断ログを有効にし、Azure Monitor Logsへのストリーミングを構成し、[Azure Key Vault Analytics Solution](https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault) を有効にします。 <br/> 4.ネットワーク セキュリティ グループの診断ログを有効にし、Azure Monitor ログへのストリーミングを構成します。<br/> 5.Traffic Manager エンドポイントの診断ログを有効にし、Azure Monitor ログへのストリーミングを構成します。
ネットワーク| ご利用の仮想マシンと 1 つまたは複数のエンドポイント (別の VM、完全修飾ドメイン名、Uniform Resource Identifier、または IPv4 アドレス) との間の通信です。 | VM とエンドポイントの間で発生する到達可能性、待機時間、およびネットワーク トポロジ変更を監視します。 | Azure Network Watcher
Azure サブスクリプション | Azure サービスの正常性と基本的なリソースの正常性 | <li> サービスまたはリソースに対して実行された管理操作。<br/><li> Azure サービスが低下状態または利用不可状態にあるサービス正常性。<br/><li> Azure サービスの観点から Azure リソースで検出された正常性の問題。<br/><li> 失敗または例外を示す Azure の自動スケーリングを使用して実行された操作。 <br/><li> 許可または拒否操作が発生したことを示す Azure Policy を使用して実行された操作。<br/><li> Azure Security Center によって生成されたアラートのレコード。 |Azure Resource Manager を使用して監視およびアラート通知用のアクティビティ ログで提供されます。
Azure テナント|Azure Active Directory || 診断ログを有効にし、Azure Monitor ログへのストリーミングを構成します。

<!-- markdownlint-enable MD033 -->

## <a name="hybrid-cloud-monitoring"></a>ハイブリッド クラウドの監視

このセクションは現在、このクラウド モデルに対するお客様の関心に応えられるよう、網羅的な一連のレコメンデーションを提供するために執筆が進められており、近日中に公開予定です。  

## <a name="private-cloud-monitoring"></a>プライベート クラウドの監視

System Center Operations Manager を使用して、Azure Stack の総合的な監視を実現できます。 具体的には、仮想マシン上のテナント (リソース レベル) で実行されているワークロード、および、Azure Stack をホストしているインフラストラクチャ (物理サーバーとネットワーク スイッチ) を監視できます。 Azure Stack に含まれている[インフラストラクチャ監視機能](/azure/azure-stack/azure-stack-monitor-health)を組み合わせて総合的な監視を実現することもできます。 これらの機能は、Azure Stack リージョンの正常性とアラート、および Azure Stack の [Azure Monitor サービス](/azure/azure-stack/user/azure-stack-metrics-azure-data)を表示するために役立ちます。後者は、基本レベルのインフラストラクチャ メトリックと、ほとんどのサービスのログを提供します。

既に Operations Manager に投資している場合は、Azure Stack 管理パックを使用して、Azure Stack デプロイの可用性と正常性状態を監視します。 これには、リージョン、リソース プロバイダー、更新プログラム、更新プログラムの実行、スケール ユニット、ユニット ノード、インフラストラクチャ ロール、およびそれらのインスタンス (ハードウェア リソースで構成される論理エンティティ) が含まれます。 この管理パックでは、正常性および更新プログラムのリソース プロバイダーの REST API を使用して Azure Stack との通信が行われます。 物理サーバーとストレージ デバイスを監視するには、OEM ベンダーの管理パックを使用します (たとえば、Lenovo、Hewlett Packard、または Dell から提供されています)。 Operations Manager は、ネットワーク スイッチをネイティブに監視し、SNMP プロトコルを使用して基本的な統計を収集することができます。 Azure 管理パックでは、2 つの基本的な手順に従ってテナント ワークロードを監視できます。 監視するサブスクリプションを構成してから、そのサブスクリプション用のモニターを追加します。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [適切なデータの収集](./data-collection.md)
