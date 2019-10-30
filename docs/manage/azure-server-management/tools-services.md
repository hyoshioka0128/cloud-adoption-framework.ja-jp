---
title: Azure サーバー管理ツールおよびサービス
titleSuffix: Microsoft Cloud Adoption Framework for Azure
description: Azure サーバー管理ツールおよびサービス
author: BrianBlanchard
ms.author: brblanch
ms.date: 05/10/2019
ms.topic: article
ms.service: cloud-adoption-framework
ms.subservice: operate
ms.openlocfilehash: e365cca30b5fe98e0737beb3005a13544844dc41
ms.sourcegitcommit: 15898374495761bfb76cee719e0f9189856884e6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/24/2019
ms.locfileid: "72888880"
---
# <a name="azure-server-management-tools-and-services"></a>Azure サーバー管理ツールおよびサービス

このセクションの[概要](./index.md)で説明したように、Azure サーバー管理サービス スイートは次の領域に対応します。

- [移行](#migrate)
- [セキュリティ保護](#secure)
- [保護](#protect)
- [監視](#monitor)
- [構成](#configure)
- [ガバナンス](#govern)

次のセクションでは、これらの管理領域について簡単に説明し、それらに対応する主な Azure サービスに関する詳細なコンテンツへのリンクを提供します。

## <a name="migrate"></a>移行

移行サービスは、Azure へのワークロードの移行を支援します。 最適なガイダンスを提供するために、Azure Migrate サービスではまず、オンプレミス サーバーのパフォーマンスが測定され、移行の適合性が評価されます。 Azure Migrate による評価が完了したら、[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) や [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview) を使用してオンプレミスのマシンを Azure に移行できます。

## <a name="secure"></a>セキュリティ保護

[Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) は、包括的なセキュリティ管理アプリケーションです。 Security Center にオンボードすると、環境のセキュリティや規制遵守の状態をすぐに評価できます。 Azure Security Center にサーバーをオンボードする手順は、[サブスクリプションに対する Azure 管理サービスの構成](./onboard-at-scale.md#azure-security-center)に関するページを参照してください。

## <a name="protect"></a>保護

データを保護するには、バックアップ、高可用性、暗号化、承認、関連する運用上の問題に関して計画を立てる必要があります。 これらのトピックはオンラインで広く説明されているため、ここでは、事業継続とディザスター リカバリー (BCDR) 計画の構築に焦点を当てます。 この種の計画を実装してデプロイする方法を詳しく説明するドキュメントについて言及しています。

データ保護戦略を構築するときには、まずワークロード アプリケーションを複数の層に分割することを検討する必要があります。通常、各層に必要な保護計画がそれぞれに異なるためです。 回復性に優れたアプリケーションの設計に関する詳細については、[回復性に優れた Azure 用アプリケーションの設計](https://docs.microsoft.com/azure/architecture/resiliency)に関するページを参照してください。

最も基本的なデータ保護はバックアップです。 サーバーの損失が発生した場合の復旧プロセスを迅速化するには、データだけでなくサーバー構成もバックアップする必要があります。 バックアップは、偶発的なデータ削除やランサムウェア攻撃に対応するための効果的なメカニズムです。 [Azure Backup](https://docs.microsoft.com/azure/backup) では、Windows または Linux を実行している Azure サーバーおよびオンプレミス サーバー上のデータを保護できます。 このサービスの機能の詳細と攻略ガイドは、[Azure Backup のドキュメント](https://docs.microsoft.com/azure/backup/backup-overview)をご覧ください。

バックアップによる復旧には時間がかかります。 業界の標準では通常、1 日です。 ハードウェア障害やデータ センター停止が発生したときのビジネス継続性がワークロードに必要である場合は、データ レプリケーションの使用を検討します。 [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) は VM の継続的なレプリケーションを可能にします。これはデータ損失を最小限に抑えるソリューションです。 また、Site Recovery はさまざまなレプリケーション シナリオにも対応します (たとえば 2 つの Azure リージョン間、オンプレミス サーバー間、オンプレミスと Azure 間の Azure VM のレプリケーションなど)。 詳細については、[Azure Site Recovery のレプリケーションに関する詳しいマトリックス](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview#what-can-i-replicate)に関するページをご覧ください。

ファイル サーバー データについては、[Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning) サービスも検討の対象となります。このサービスでは、オンプレミス ファイル サーバーの柔軟性、パフォーマンス、互換性を維持しながら、Azure Files で組織のファイル共有を一元化できます。 このサービスを使用するには、Azure File Sync をデプロイする手順に従ってください。

## <a name="monitor"></a>監視

[Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) では、アプリケーション、コンテナー、仮想マシンなど、さまざまなリソースを表示できます。 また、複数のソースからデータを収集します。

- Azure Monitor for VMs ([分析情報](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-overview)) では、仮想マシンの正常性、パフォーマンスの傾向、依存関係の詳細を確認できます。 このサービスは、Azure 仮想マシン、仮想マシン スケール セット、オンプレミス環境のマシンのオペレーティング システムの正常性を監視します。
- ログ分析 ([ログ](https://docs.microsoft.com/azure/azure-monitor/platform/data-collection#logs)) は Azure Monitor の機能です。 Azure 管理全体の中心的役割を果たします。 ログ分析やその他多数の Azure サービスのデータ ストアとして機能します。 高機能なクエリ言語を提供しているほか、アプリケーションやリソースの操作に関する分析情報が得られる分析エンジンを搭載しています。
- [Azure アクティビティ ログ](https://docs.microsoft.com/azure/azure-monitor/platform/activity-logs-overview)も Azure Monitor の機能です。 ここから、Azure で発生するサブスクリプション レベルのイベントに関する分析情報を得ることができます。

## <a name="configure"></a>構成

このカテゴリには複数のサービスが含まれます。 運用タスクの自動化、サーバー構成の管理、更新プログラムのコンプライアンスの評価、更新のスケジュール、サーバーに対する変更の検出などに役立ちます。 これらのサービスは、継続的な運用をサポートするための重要な要素です。

- [更新管理](https://docs.microsoft.com/azure/automation/automation-update-management#view-update-assessments)では、Azure の外部で実行されているオペレーティング システム インスタンスへのデプロイを含め、環境全体で修正プログラムのデプロイを自動化できます。 Windows と Linux の両方のオペレーティング システムに対応し、OS の主な脆弱性と、修正プログラムの適用漏れに起因する不適合を追跡します。
- [Change Tracking および Inventory](https://docs.microsoft.com/azure/automation/change-tracking) では、環境内で実行されているソフトウェアに関する分析情報を得られるほか、発生した変更を明らかにすることができます。
- [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) では、Python と PowerShell のスクリプトまたは Runbook を実行して環境全体でタスクを自動化できます。 [Hybrid Runbook Worker](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) を併用すれば、Runbook をオンプレミスのリソースにも実行できます。
- [Azure Automation State Configuration](https://docs.microsoft.com/azure/automation/automation-dsc-overview) では、PowerShell Desired State Configuration (DSC) 構成を Azure から直接プッシュできます。 その結果、DSC ではゲスト内オペレーティング システムとワークロードの構成を監視して維持できます。

## <a name="govern"></a>ガバナンス

クラウドへの移行により、新たな管理上の課題が生まれ、運用管理の負担という発想をガバナンスの監視へと変えていくことが求められます。 Azure 向けクラウド導入フレームワークは[ガバナンス](../../govern/index.md)から始まります。 クラウドへの移行方法、移行過程、移行に関与する人について説明します。

一般に、標準的な組織と複雑な企業ではガバナンス設計が異なります。 標準的な組織を対象としたガバナンスのベスト プラクティスの詳細については、[標準的なガバナンス ガイド](../../govern/guides/standard/index.md)に関する記事をご覧ください。 複雑な企業を対象としたガバナンスのベスト プラクティスの詳細については、「[複雑な企業向けのガバナンス ガイド](../../govern/guides/complex/index.md)」をご覧ください。

## <a name="billing-information"></a>課金情報

Azure 管理サービスの価格の詳細については、次のページを参照してください。

- [Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery)

- [Azure Backup](https://azure.microsoft.com/pricing/details/backup)

- [Azure Monitor](https://azure.microsoft.com/pricing/details/monitor)

- [Azure Security Center](https://azure.microsoft.com/pricing/details/security-center)

- [Azure Update Management サービス](https://azure.microsoft.com/pricing/details/automation)

- [Azure Change Tracking および Inventory サービス](https://azure.microsoft.com/pricing/details/automation)

- [Desired State Configuration](https://azure.microsoft.com/pricing/details/automation)

- [Azure Automation サービス](https://azure.microsoft.com/pricing/details/automation)

- [Azure Policy](https://azure.microsoft.com/pricing/details/azure-policy)

- [Azure File Sync サービス](https://azure.microsoft.com/pricing/details/storage/blobs)

> [!NOTE]
> Azure Update Management ソリューションは無料ですが、データ インジェストに関連する多少の費用が発生します。 大体の目安として、1 か月あたりの最初の 5 GB のデータ インジェストが無料です。 一般に、各マシンは 1 か月あたり約 25 MB を使うことがわかっています。 この場合、無料の対象となるマシンは 1 か月に約 200 台となります。 サーバーを追加するたびに、追加したサーバーの数と 1 か月あたりの使用量 25 MB を掛けます。 その値と、必要なストレージ総量のストレージ コストを掛けます。 [ストレージ コストについてはこちらをご覧ください](https://azure.microsoft.com/pricing/details/storage)。 サーバーを追加するたびに、その分だけコストが影響を受けます。
