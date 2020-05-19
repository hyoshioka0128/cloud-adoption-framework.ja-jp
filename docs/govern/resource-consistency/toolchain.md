---
title: Azure のリソース整合性ツール
description: Azure ネイティブ ツールが、リソースの整合性の規範をサポートするポリシーとプロセスを成熟させるのにどのように役立つかについて説明します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 09/17/2019
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: govern
ms.custom: governance
ms.openlocfilehash: a64cbd53cdd4c524b370681ebedf1f8282ac2a93
ms.sourcegitcommit: 60d8b863d431b5d7c005f2f14488620b6c4c49be
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2020
ms.locfileid: "83217950"
---
# <a name="resource-consistency-tools-in-azure"></a>Azure のリソース整合性ツール

[リソースの整合性](./index.md)は、[5 つのクラウド ガバナンス規範](../governance-disciplines.md)のうちの 1 つです。 これは、環境、アプリケーションまたはワークロードの運用管理に関係するポリシーを規定する方法に関する規範です。 クラウド ガバナンスの 5 つの規範のうち、"リソースの整合性" の規範にアプリケーション、ワークロード、および資産のパフォーマンスの監視が必要です。 これには、スケール要求の充足、パフォーマンスの SLA 違反の修復、および自動修復によるパフォーマンス SLA 違反の事前回避を行うために必要なタスクも含まれています。

この規範をサポートするポリシーとプロセスを成熟させるのに役立つ Azure ツールの一覧を次に示します。

| ツール | [Azure Portal](https://azure.microsoft.com/features/azure-portal)  | [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/management/overview)  | [Azure Blueprint](https://docs.microsoft.com/azure/governance/blueprints/overview) | [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) | [Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) | [Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview) | [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) |
|---------|---------|---------|---------|---------|---------|---------|---------|
| リソースのデプロイ                             | はい | はい | はい | はい | いいえ  | いいえ | いいえ |
| リソースの管理                             | はい | はい | はい | はい | いいえ  | いいえ | いいえ |
| テンプレートを使用してリソースをデプロイする             | いいえ  | はい | いいえ  | はい | いいえ  | いいえ | いいえ |
| 調整された環境のデプロイ          | いいえ  | いいえ  | はい | いいえ  | いいえ  | いいえ | いいえ |
| リソース グループを定義する                       | はい | はい | はい | いいえ  | いいえ  | いいえ | いいえ |
| ワークロードとアカウントの所有者を管理する           | はい | はい | はい | いいえ  | いいえ  | いいえ | いいえ |
| リソースへの条件付きアクセスを管理する       | はい | はい | はい | いいえ  | いいえ  | いいえ | いいえ |
| RBAC ユーザーを構成する                         | はい | いいえ  | いいえ  | いいえ  | はい | いいえ | いいえ |
| ロールとアクセス許可をリソースに割り当てる | はい | はい | はい | いいえ  | はい | いいえ | いいえ |
| リソース間の依存関係を定義する        | いいえ  | はい | はい | いいえ  | いいえ  | いいえ | いいえ |
| アクセス制御を適用する                         | はい | はい | はい | いいえ  | はい | いいえ | いいえ |
| 可用性とスケーラビリティを評価する          | いいえ  | いいえ  | いいえ  | はい | いいえ  | いいえ | いいえ |
| タグをリソースに適用する                      | はい | はい | はい | いいえ  | いいえ  | いいえ | いいえ |
| Azure Policy の規則を割り当てる                    | はい | はい | はい | いいえ  | いいえ  | いいえ | いいえ |
| 自動修復を適用する                  | いいえ  | いいえ  | いいえ  | はい | いいえ  | いいえ | いいえ |
| 請求の管理                               | はい | いいえ  | いいえ  | いいえ  | いいえ  | いいえ | いいえ |
| ディザスター リカバリーのためにリソースを計画する         | はい | はい | はい | いいえ  | いいえ  | はい | はい |
| 障害発生時または SLA 違反時にデータを復旧する     | いいえ | いいえ  | いいえ  | いいえ  | いいえ  | はい | はい |
| 障害発生時または SLA 違反時にアプリケーションとデータを復旧する     | いいえ | いいえ  | いいえ  | いいえ  | いいえ  | はい | はい |

リソースの整合性に関するこれらのツールおよび機能と共に、デプロイされたリソースのパフォーマンスと正常性の問題を監視する必要があります。 [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) は Azure の既定の監視およびレポート ソリューションです。 Azure Monitor には、ご利用のクラウド リソースを監視するための機能が用意されています。 次の一覧に、一般的な監視要件に対応する機能を示します。

| ツール | [Azure Portal](https://azure.microsoft.com/features/azure-portal) | [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) | [Log Analytics](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview) | [Azure 監視 REST API](https://docs.microsoft.com/rest/api/monitor) |
|----------------------------------------------------|--------------|----------------------|---------------|------------------------|
| 仮想マシンの利用統計情報をログに記録する                 | いいえ           | いいえ                   | はい           | いいえ                     |
| 仮想ネットワークの利用統計情報をログに記録する              | いいえ           | いいえ                   | はい           | いいえ                     |
| PaaS サービスの利用統計情報をログに記録する                   | いいえ           | いいえ                   | はい           | いいえ                     |
| アプリケーションの利用統計情報をログに記録する                     | いいえ           | はい                  | いいえ            | いいえ                     |
| レポートとアラートを構成する                       | はい          | いいえ                   | いいえ            | はい                    |
| 定期的なレポートまたはカスタム分析をスケジュールする        | いいえ           | いいえ                   | いいえ            | いいえ                     |
| ログとパフォーマンス データを視覚化および分析する     | はい          | いいえ                   | いいえ            | いいえ                     |
| オンプレミスまたはサード パーティ製の監視ソリューションと統合する     | いいえ           | いいえ                   | いいえ            | はい                    |

デプロイを計画するときには、ログ データの格納場所と、クラウド ベースの[レポート サービスと監視サービス](../../decision-guides/logging-and-reporting/index.md)を既存のプロセスとツールに統合する方法を検討する必要があります。

> [!NOTE]
> 組織では、ワークロードとリソースの監視にサード パーティ製の DevOps ツールも使用します。 詳細については、「[DevOps ツールの統合](https://azure.microsoft.com/products/devops-tool-integrations)」を参照してください。

## <a name="next-steps"></a>次のステップ

Azure で[ポリシー定義](https://docs.microsoft.com/azure/governance/policy)の作成、割り当て、管理を行う方法について説明します。
