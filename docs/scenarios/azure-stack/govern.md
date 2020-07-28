---
title: データセンターで実行されている Azure のガバナンス
description: データセンター内の Azure Stack Hub 上で実行されている Azure のガバナンス方法について説明します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 05/19/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: govern
ms.openlocfilehash: c9f8a2f161571ced20f3a9317469cee14cc35fb1
ms.sourcegitcommit: 9163a60a28ffce78ceb5dc8dc4fa1b83d7f56e6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2020
ms.locfileid: "86452646"
---
# <a name="govern-azure-running-in-your-datacenter"></a>データセンターで実行されている Azure のガバナンス

パブリックおよびプライベートのクラウド プラットフォームにまたがるハイブリッド ソリューションのガバナンスは複雑な作業です。 Azure Stack Hub はデータセンターで実行されている Azure のユーザー独自のプライベート インスタンスであるため、そのような複雑さはもともと削減されています。

とはいえ、クラウド導入フレームワークの[ガバナンスの方法](../../govern/index.md)で概説されているビジネス プロセス、規範、およびベスト プラクティスの多くは、Azure Stack Hub を使用したハイブリッド ガバナンスにも適用できます。 Azure のパブリック クラウド バージョンで使用されるクラウドネイティブ ツールの多くは、Azure Stack Hub でも使用できます。

## <a name="azure-stack-hub-governance-considerations"></a>Azure Stack Hub のガバナンスに関する考慮事項

次の一連のブログで、Azure Stack Hub にクラウド ガバナンスの概念を実装する方法について説明しています。

- [組織向けサービス](https://azure.microsoft.com/blog/azure-stack-iaas-part-seven/): リソース グループ、ロールベースのアクセス制御 (RBAC)、変更の監査、ロック、タグなど。
- [セキュリティ サービス](https://azure.microsoft.com/blog/azure-stack-iaas-part-four/): 既定のファイアウォール、制限、VM の更新と修正プログラムの管理、マルウェアの状態など。
- [DevOps オプション](https://azure.microsoft.com/blog/azure-stack-iaas-part-seven-2/): コードとしてのインフラストラクチャ (IaC)、PowerShell およびコマンドライン インターフェイス (CLI) を使用したポータル、Azure Application Insights、Azure DevOps と Jenkins の統合など。

## <a name="governance-toolchain-for-azure-stack-hub"></a>Azure Stack Hub のガバナンス ツールチェーン

クラウドネイティブ ガバナンス ツールを Azure Stack Hub 環境に適用するためのガイダンスについては、以下のリンクを参照してください。

- [Azure Resource Manager テンプレートと Desired State Configuration](https://docs.microsoft.com/azure-stack/user/azure-stack-arm-templates?view=azs-2002)
- [PowerShell](https://docs.microsoft.com/azure-stack/user/azure-stack-powershell-overview?view=azs-2002)
- [Azure Policy](https://docs.microsoft.com/azure-stack/user/azure-stack-policy-module?view=azs-2002)
- [ロールベースのアクセス制御 (RBAC)](https://docs.microsoft.com/azure-stack/user/azure-stack-manage-permissions?view=azs-2002)

## <a name="next-step-integrate-this-strategy-into-your-cloud-adoption-journey"></a>次のステップ: この戦略をクラウド導入の取り組みに統合する

以下の記事では、クラウド導入過程の特定の時点におけるガイダンスについて説明します。

- [Azure Stack Hub の管理](./manage.md)
