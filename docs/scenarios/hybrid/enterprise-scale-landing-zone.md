---
title: ハイブリッドとマルチクラウドに対するエンタープライズ規模のサポート
description: エンタープライズ規模で、ハイブリッドまたはマルチクラウド アーキテクチャの導入を加速する方法について説明します。
author: DominicAllen
ms.author: doalle
ms.date: 03/01/2021
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.openlocfilehash: 698fa8ed9569755df0349bbd8c7c77021d295000
ms.sourcegitcommit: 9e4bc0e233a24642853f5e8acbeb9746b2444024
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102116361"
---
# <a name="enterprise-scale-support-for-hybrid-and-multicloud"></a>ハイブリッドとマルチクラウドに対するエンタープライズ規模のサポート
  
エンタープライズ規模のランディング ゾーンは、ミッション クリティカルなテクノロジ プラットフォームと、サポートされているすべてのワークロード用にお使いのランディング ゾーンを準備するための、特定のアーキテクチャ アプローチ、参照アーキテクチャ、およびリファレンス実装を提供します。

エンタープライズ規模の環境は、ハイブリッドとマルチクラウドを念頭に置いて構築されています。 ハイブリッドとマルチクラウドをサポートするには、参照アーキテクチャに対する 3 つの簡単な追加が必要です。

- **ハイブリッド:** ネットワークのハイブリッド接続を可能にします
- **マルチクラウド:** ネットワークのマルチクラウド接続を可能にします
- **統合運用:** ガバナンスの拡大と業務運営の支援に Azure Arc を追加できます

## <a name="why-hybrid"></a>なぜハイブリッドなのか

組織が最新のクラウド サービスとその利点を選ぶにつれ、必然的に従来のオンプレミスのインフラストラクチャが並行して実行される期間が生じます。

さらに、クラウド サービスが評価されたり、ビジネス要件で決定されることにより、サービスを複数のパブリック クラウド サービスで実行することを組織が選択する場合があります。

分散型の異種資産では、業務運営への影響を軽減するために、統合管理とガバナンスを簡素化する必要があります。

クラウド導入フレームワーク ガイダンスの一環として導入されるランディング ゾーンの概念を使用すると、ハイブリッド アーキテクチャの構築のパターンを確立し、接続性、ガバナンス、監視の標準を導入できます。

これは、移行プロジェクトの後に、インフラストラクチャとサービスを簡素化して統合する戦略的意図がある場合に便利です。管理プロセスとツールに標準が設定されるということは、Azure に移行後にワークロードを変更する必要がないことを意味するためです。

## <a name="prerequisite"></a>前提条件

この記事では、エンタープライズ規模のランディング ゾーンが正常に実装されていることを前提としています。 この前提条件の詳細については、エンタープライズ規模の[概要](../../ready/enterprise-scale/index.md)と[実装ガイダンス](../../ready/enterprise-scale/implementation.md)に関するページをご確認ください。

## <a name="design-guidelines"></a>設計ガイドライン

Azure のクラウド導入フレームワークにおけるエンタープライズ規模のランディング ゾーンの設計を推進する重要な意思決定についてガイドします。 自分のエンタープライズ規模のランディング ゾーンまたはその他の Azure ランディング ゾーンの実装を確認および変更する場合に使用する重要な設計領域は次の 6 つです。

- [ID 管理とアクセス管理](../../ready/enterprise-scale/identity-and-access-management.md)
- [ネットワーク トポロジと接続](../../ready/enterprise-scale/network-topology-and-connectivity.md)
- [管理と監視](../../ready/enterprise-scale/management-and-monitoring.md)
- [事業継続とディザスター リカバリー](../../ready/enterprise-scale/business-continuity-and-disaster-recovery.md)
- [セキュリティ、ガバナンス、およびコンプライアンス](../../ready/enterprise-scale/security-governance-and-compliance.md)
- [プラットフォームの自動化と DevOps](../../ready/enterprise-scale/platform-automation-and-devops.md)

## <a name="implementation-additions"></a>実装の追加

ハイブリッドおよびマルチクラウドのニーズに対応するためにエンタープライズ規模を拡大するには、お使いの実装の一部として次を検討してください。

## <a name="landing-zone"></a>ランディング ゾーン

ランディング ゾーンとは、組織がクラウド環境を迅速に構築し、クラウド テクノロジの導入を加速するための一連の参照アーキテクチャです。

クラウド導入フレームワークには、Azure に外部環境と接続するアーキテクチャを開発する場合の特定の指針があります。 [外部の場所からのイングレスおよびエグレス用に、ハブやスポークなどのパターンを使用して、ランディング ゾーンを作成することです。](../../ready/enterprise-scale/implementation.md)

たとえば、[こちらのガイダンスに従い](../../ready/azure-best-practices/connectivity-to-other-cloud-providers.md)、他のクラウド環境との接続専用のランディング ゾーン アーキテクチャを構築します。

## <a name="network"></a>ネットワーク

Azure と外部環境を統合する場合の重要な考慮事項に、ネットワーク トポロジがあります。円滑な導入を確保するために、すべての計画の演習や設計作業に織り込む必要があります。
次に具体的なトピックについての、特定の実用的なガイダンスを示します。

- [Azure への接続性](../../ready/azure-best-practices/connectivity-to-azure.md)
- [IP アドレス指定](../../ready/azure-best-practices/plan-for-ip-addressing.md)
- [ハブ アンド スポーク ネットワーク トポロジ](../../ready/azure-best-practices/hub-spoke-network-topology.md)
- [PrivateLink と DNS の統合](../../ready/azure-best-practices/private-link-and-dns-integration-at-scale.md)
- [ランディング ゾーンのネットワークのセグメント化](../../ready/azure-best-practices/plan-for-landing-zone-network-segmentation.md)

## <a name="identity"></a>ID

組織が、既存のオンプレミス データセンターと平行してクラウド環境を構築する場合、ID の領域について検討し、確実に問題が発生しないようにする必要があります。
アプリケーションがオンプレミスとクラウド間に分散されるので、ユーザーのアクセス管理が課題となります。
Azure には、ユーザーの操作性を簡略化する、オンプレミス環境とクラウド環境間での ID 管理テクノロジがあります。

ハイブリッドで ID を使用するには、組織で Azure Active Directory の次の 3 つのアプローチを検討する必要があります。

- [パスワード ハッシュの同期 (PHS)](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-phs)
- [パススルー認証 (PTA)](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta)
- [フェデレーション (AD FS)](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-fed)

ハイブリッド環境での ID モデルの計画の詳細については、[こちらをご確認](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-hybrid-identity)ください。

## <a name="governance"></a>ガバナンス

インフラストラクチャとアプリケーションが複数の場所で拡大されると、ガバナンスの標準の維持がさらに複雑になる場合があります。
ハイブリッドを計画する場合、ワークロードのスケールアウトに伴い、適したパターンを確定するために、ガバナンス ツールとプロセスも一元化する必要があります。

[クラウド導入フレームワークのエンタープライズ規模のランディング ゾーン アーキテクチャ](../../ready/enterprise-scale/architecture.md)には、リソースを論理的にグループ化するために管理グループを構造化して使用できる、[Azure Policy](https://docs.microsoft.com/azure/governance/policy/overview) と[ロールベースのアクセス制御 (RBAC)](../../ready/azure-setup-guide/manage-access.md) のデプロイを標準化するパターンが含まれます。
これらのパターンは、Azure Arc などのテクノロジを使用して拡張できます。詳細については、以下で詳しく説明します。

## <a name="management"></a>管理

ハイブリッド環境のガバナンスで検討が必要なことがあるのと同様に、大規模な分散ワークロードの管理にも、デプロイの規模の拡大に応じて問題が確実に発生しないよう計画が必要です。
[Log Analytics](/azure/azure-monitor/log-query/log-analytics-overview)、[Application Insights](/azure/azure-monitor/app/app-insights-overview)、[Azure Monitor](https://azure.microsoft.com/services/monitor/#features)、および [Azure Security Center](/azure/security-center/) などのテクノロジを使用して、[テレメトリを集約し、'単一枠ビュー' で作業できると](../../manage/azure-management-guide/inventory.md?tabs=AzureServiceHealth%2CLog-Analytics%2CAzure-Monitor%2CConfigure-solutions)は、インフラストラクチャおよびアプリケーションのチームが統合されたビューから例外で管理し、識別した問題を修正できるようにします。  

また、ガバナンス手法と同様に、上述の管理テクノロジは、特定のユース ケースにおけるオンプレミスや他のクラウド プラットフォームなど、他の環境にも対象を広げることができます。

## <a name="azure-arc"></a>Azure Arc

この記事で説明しているとおり、Azure には組織用に、インフラストラクチャとアプリケーションを大規模に監視および管理する管理ツールが多数用意されています。
ハイブリッド ランディング ゾーンを実装する場合、Azure 外で実行されるインフラストラクチャとアプリケーションの制御には、これらの Azure ツールを使用する必要があります。

これは、可能な限り簡単に大規模な監視および管理ができるよう、ハイブリッド資産全体を 1 つの管理プレーンと 1 つのビューで管理できるようにします。

[Azure Arc](/azure/azure-arc/) は、矛盾のない、マルチクラウドおよびオンプレミス管理プラットフォームを提供することにより、ガバナンスおよび管理を容易にします。
Azure Arc では、お使いの既存のリソースを [Azure Resource Manager](/azure/azure-resource-manager/management/overview#:~:text=Azure%20Resource%20Manager%20is%20the%20deployment%20and%20management,Manager%20templates%20(ARM%20templates),%20see%20the%20template) に投影することでお使いの環境全体を単一枠ビューで管理できるようにします。

あたかも Azure 内で実行されているかのように、仮想マシンや Kubernetes クラスター、データベースを管理できるようになります。 それらがどこにあっても、使い慣れた Azure のサービスや管理機能を使用することが可能です。 Azure Arc なら、従来の ITOps を引き続き使用しながら、DevOps プラクティスを導入し、自分の環境で新しいクラウド ネイティブ パターンをサポートすることができます。
