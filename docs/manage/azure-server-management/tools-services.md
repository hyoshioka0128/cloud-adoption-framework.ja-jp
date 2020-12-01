---
title: Azure サーバー管理サービス
description: Azure 向けのクラウド導入フレームワークを使用して、Azure サーバー管理サービスのスイートがカバーする領域について学習します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 05/10/2019
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: operate
ms.openlocfilehash: ee6f351f35914f326dc1864bd92114ef9edf917e
ms.sourcegitcommit: 412b945b3492ff3667c74627524dad354f3a9b85
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/18/2020
ms.locfileid: "94880145"
---
# <a name="azure-server-management-tools-and-services"></a>Azure サーバー管理ツールおよびサービス

このガイダンスの[概要](./index.md)で説明したように、Azure サーバー管理サービスのスイートは次の分野を対象としています。

- 移行
- セキュリティで保護
- 保護
- モニター
- 構成
- ガバナンス

次のセクションでは、これらの管理領域について簡単に説明し、それらに対応する主な Azure サービスに関する詳細なコンテンツへのリンクを提供します。

## <a name="migrate"></a>移行

移行サービスは、Azure へのワークロードの移行を支援します。 最適なガイダンスを提供するために、Azure Migrate サービスではまず、オンプレミス サーバーのパフォーマンスが測定され、移行の適合性が評価されます。 Azure Migrate による評価が完了したら、[Azure Site Recovery](/azure/site-recovery/site-recovery-overview) や [Azure Database Migration Service](/azure/dms/dms-overview) を使用してオンプレミスのマシンを Azure に移行できます。

## <a name="secure"></a>セキュリティで保護

[Azure Security Center](/azure/security-center/security-center-intro) は、包括的なセキュリティ管理アプリケーションです。 Security Center にオンボードすると、環境のセキュリティや規制遵守の状態をすぐに評価できます。 Azure Security Center にサーバーをオンボードする手順は、[サブスクリプションに対する Azure 管理サービスの構成](./onboard-at-scale.md#azure-security-center)に関するページを参照してください。

## <a name="protect"></a>保護

データを保護するには、バックアップ、高可用性、暗号化、承認、関連する運用上の問題に関して計画を立てる必要があります。 これらのトピックはオンラインで広く説明されているため、ここでは、事業継続とディザスター リカバリー (BCDR) 計画の構築に焦点を当てます。 この種の計画を実装してデプロイする方法を詳しく説明するドキュメントについて言及しています。

データ保護戦略を構築する場合、最初にワークロード アプリケーションを異なる層に分割することを検討します。 通常、各層には独自の保護計画が必要なため、このアプローチは役立ちます。 回復性に優れたアプリケーションの設計に関する詳細については、[回復性に優れた Azure 用アプリケーションの設計](/azure/architecture/resiliency)に関するページを参照してください。

最も基本的なデータ保護はバックアップです。 サーバーが失われた場合の回復プロセスを高速化するには、データだけでなくサーバー構成もバックアップします。 バックアップは、偶発的なデータ削除やランサムウェア攻撃に対応するための効果的なメカニズムです。 [Azure Backup](/azure/backup) では、Windows または Linux を実行している Azure サーバーおよびオンプレミス サーバー上のデータを保護できます。 Azure Backup でできることの詳細と攻略ガイドについては、[Azure Backup サービスの概要](/azure/backup/backup-overview)に関する記事をご覧ください。

ハードウェア障害やデータ センター停止の発生時に、リアルタイムのビジネス継続性がワークロードに必要である場合は、データ レプリケーションの使用を検討します。 [Azure Site Recovery](/azure/site-recovery/site-recovery-overview) は VM の継続的なレプリケーションを可能にします。これはデータ損失を最小限に抑えるソリューションです。 Site Recovery では、レプリケーションなど、いくつかのレプリケーション シナリオもサポートされます。

- 2 つの Azure リージョン間の Azure VM。
- オンプレミスのサーバー間。
- オンプレミスのサーバーと Azure の間。

詳細については、[Azure Site Recovery のレプリケーションに関する詳しいマトリックス](/azure/site-recovery/site-recovery-overview#what-can-i-replicate)に関するページをご覧ください。

ファイルサーバー データについては、[Azure File Sync](/azure/storage/files/storage-sync-files-planning) サービスも検討の対象となります。このサービスでは、オンプレミス ファイル サーバーの柔軟性、パフォーマンス、互換性を維持しながら、Azure Files で組織のファイル共有を一元化できます。 このサービスを使用するには、Azure File Sync をデプロイする手順に従ってください。

## <a name="monitor"></a>モニター

[Azure Monitor](/azure/azure-monitor/overview) では、アプリケーション、コンテナー、仮想マシンなど、さまざまなリソースを表示できます。 また、複数のソースからデータを収集します。

- [Azure Monitor for VMs](/azure/azure-monitor/insights/vminsights-overview) では、VM の正常性、パフォーマンスの傾向、および依存関係の詳細が提供されます。 このサービスは、Azure 仮想マシン、仮想マシン スケール セット、オンプレミス環境のマシンのオペレーティング システムの正常性を監視します。
- [Log Analytics](/azure/azure-monitor/log-query/log-query-overview) は Azure Monitor の機能です。 Azure 管理全体の中心的役割を果たします。 ログ分析やその他多数の Azure サービスのデータ ストアとして機能します。 高機能なクエリ言語を提供しているほか、アプリケーションやリソースの操作に関する分析情報が得られる分析エンジンを搭載しています。
- [Azure アクティビティ ログ](/azure/azure-monitor/platform/activity-logs-overview)も Azure Monitor の機能です。 ここから、Azure で発生するサブスクリプション レベルのイベントに関する分析情報を得ることができます。

## <a name="configure"></a>構成

このカテゴリには複数のサービスが含まれます。 次のような場合に役立ちます。

- 運用タスクを自動化する。
- サーバーの構成を管理する。
- 更新プログラムのコンプライアンスを測定する。
- 更新のスケジュールを設定する。
- サーバーに対する変更を検出する。

これらのサービスは、継続的な運用をサポートするために不可欠です。

- [Update Management](/azure/automation/update-management/overview) では、Azure の外部で実行されているオペレーティング システム インスタンスへのデプロイを含め、環境全体で修正プログラムのデプロイを自動化できます。 Windows と Linux の両方のオペレーティング システムに対応し、OS の主な脆弱性と、修正プログラムの適用漏れに起因する不適合を追跡します。
- [Change Tracking および Inventory](/azure/automation/change-tracking) では、環境内で実行されているソフトウェアに関する分析情報を得られるほか、発生した変更を強調表示することができます。
- [Azure Automation](/azure/automation/automation-intro) では、Python と PowerShell のスクリプトまたは Runbook を実行して環境全体でタスクを自動化できます。 Azure Automation と [Hybrid Runbook Worker](/azure/automation/automation-hybrid-runbook-worker) を併用すると、Runbook をオンプレミスのリソースにも拡張できます。
- [Azure Automation State Configuration](/azure/automation/automation-dsc-overview) では、PowerShell Desired State Configuration (DSC) 構成を Azure から直接プッシュできます。 DSC を使うと、ゲスト オペレーティング システムとワークロードの構成を監視し、保存することもできます。

## <a name="govern"></a>ガバナンス

クラウドを導入して移行すると、新しい管理の課題が生じます。 運用管理の負担から監視とガバナンスに移行する際には、異なる考え方が必要です。 クラウド導入フレームワークは[ガバナンス](../../govern/index.md)から始まります。 このフレームワークでは、クラウドへの移行方法、移行過程、移行に関与する人について説明します。

一般に、標準的な組織と複雑な企業ではガバナンス設計が異なります。 標準的な組織を対象としたガバナンスのベスト プラクティスの詳細については、[標準的なエンタープライズ ガバナンス ガイド](../../govern/guides/standard/index.md)に関する記事を参照してください。 複雑な企業を対象としたガバナンスのベスト プラクティスの詳細については、「[複雑な企業向けのガバナンス ガイド](../../govern/guides/complex/index.md)」を参照してください。

## <a name="billing-information"></a>課金情報

Azure 管理サービスの価格の詳細については、次のページを参照してください。

- [Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery)

- [Azure Backup](https://azure.microsoft.com/pricing/details/backup)

- [Azure Monitor](https://azure.microsoft.com/pricing/details/monitor)

- [Azure Security Center](https://azure.microsoft.com/pricing/details/security-center)

- 次を含む [Azure Automation](https://azure.microsoft.com/pricing/details/automation)。
  - 必要な状態の構成
  - Update Management ソリューション
  - 変更履歴とインベントリ ソリューション

- [Azure Policy](https://azure.microsoft.com/pricing/details/azure-policy)

- [Azure File Sync サービス](https://azure.microsoft.com/pricing/details/storage/blobs)

> [!NOTE]
> Azure Update Management ソリューションは無料ですが、データ インジェストに関連する多少の費用が発生します。 経験則として、1 か月あたりのデータ インジェストの最初の 5 ギガバイト (GB) は無料です。 一般に、各マシンは 1 か月あたり約 25 MB を使うことがわかっています。 この場合、無料の対象となるマシンは 1 か月に約 200 台となります。 その他のサーバーについては、追加サーバーの数に月あたり 25 MB を掛けます。 次に、必要な追加ストレージのストレージ価格を結果に掛けます。 コストについては、[Azure Storage の価格](https://azure.microsoft.com/pricing/details/storage)に関するページをご覧ください。 通常、サーバーを追加するごとに、コストにわずかな影響があります。
