---
title: SAP on Azure のエンタープライズ規模の管理と監視
description: SAP on Azure の管理と監視のための設計上の考慮事項と推奨事項について説明します。
author: JefferyMitchell
ms.author: brblanch
ms.date: 03/01/2021
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.custom: think-tank
ms.openlocfilehash: e7a70b51d2d66723fbcfb95ca0616bddacdce5ba
ms.sourcegitcommit: 36e85ac734b184de3f29884b744ea74c81ccc72b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/13/2021
ms.locfileid: "103443201"
---
# <a name="enterprise-scale-management-and-monitoring-for-sap-on-azure"></a>SAP on Azure のエンタープライズ規模の管理と監視

この記事では、プラットフォーム レベルでの一元的な管理と監視によって、SAP on Azure のエンタープライズ資産の運用を維持する方法について説明します。 この記事では、SAP オペレーション チームが Azure プラットフォーム内で SAP システムを保守する際の主な推奨事項について説明します。

**設計上の考慮事項:**

SAP on Azure の監視と管理に関する設計上の考慮事項を次に示します。

- プラットフォームとアプリケーション層の監視を確立するための Azure Monitor と Application Insights を備えた一元化された Azure Log Analytics ワークスペースについて考えてみましょう。

- 待機時間の影響を受けやすいアプリケーションについて、仮想マシン (VM) の待機時間の監視を検討してください。

- 個々の仮想マシンではなく、中央の仮想マシンから [Azacsnap](/azure/azure-netapp-files/azacsnap-introduction) をスケジュールすることを検討してください。

**設計上の推奨事項:**

SAP on Azure の監視と管理に関する設計上の推奨事項を次に示します。

- SAP システムとソリューションを監視します。

- SAP ソリューション マネージャーと [Azure Monitor for SAP Solutions](/azure/virtual-machines/workloads/sap/azure-monitor-overview) を使用して、SAP HANA、高可用性 SUSE クラスター、SQL システムを監視します。

- SAP 確認用 VM 拡張機能を実行します。 VM Extension for SAP は、VM の割り当て済みマネージド ID を使用して、VM の監視と構成データにアクセスします。 このチェックでは、SAP アプリケーション内で表示されるすべてのパフォーマンス メトリックが、基になる[ Azure Extension for SAP ](/azure/virtual-machines/workloads/sap/deployment-guide)によって提供されていることを確認します。

- [Azure Backup](/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)サービスを使用して HANA データベースを保護します。 HANA データベースの Azure NetApp Files (ANF) をデプロイする場合は、[Azure アプリケーション整合性スナップショット ツール (AzAcSnap)](/azure/azure-netapp-files/azacsnap-introduction) を使用して、アプリケーション整合性スナップショットを作成します。

- [SAP テレメトリ ツール](https://github.com/microsoft/saptelemetry)ツールを使用して、ビジネス プロセスの分析情報を提供する監視プラットフォームを作成します。

- Network Watcher [接続モニター](/azure/network-watcher/connection-monitor) を使用して、SAP データベースとアプリケーション サーバーの待機時間メトリックを監視したり、 [Azure Monitor](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/collecting-and-displaying-niping-network-latency-measurements/ba-p/1833979)でネットワーク待機時間の測定値を収集して表示したりします。

- [Azure Site Recovery](/azure/site-recovery/monitoring-common-questions)監視を使用して、SAP アプリケーション サーバーのディザスター リカバリー サービスの正常性を維持します。

- [SAP ランドスケープ管理 (LaMa)](https://www.sap.com/products/landscape-management.html)を使用して SAP Basis 操作を最適化および管理します。 SAP システムの再配置、コピー、複製、および更新を行うには、 [Azure 用の SAP LaMa コネクタ](/azure/virtual-machines/workloads/sap/lama-installation) を使用します。

- プロビジョニングされた Azure インフラストラクチャに対する [SAP HANA の品質チェック](https://github.com/Azure/SAP-on-Azure-Scripts-and-Utilities/tree/main/QualityCheck) を実行し、プロビジョニングされた VM が Azure のベスト プラクティスで SAP HANA に準拠していることを確認します。

- 各 Azure サブスクリプションについて、ゾーンのデプロイ前に [Azure 可用性ゾーンの待機時間テスト](https://github.com/Azure/SAP-on-Azure-Scripts-and-Utilities/tree/main/AvZone-Latency-Test) を実行して、SAP on Azure デプロイの待機時間の短いゾーンを選択します。

- [Azure Sentinel](/azure/sentinel/overview)を使用して SAP の脅威保護を実装します。
