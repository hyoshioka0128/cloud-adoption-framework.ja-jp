---
title: Azure Stack Hub にワークロードをデプロイする
description: Azure Stack Hub を使用して、データセンターにワークロードをデプロイする方法を説明します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 05/19/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: migrate
ms.openlocfilehash: 2e29dcf36d0da76d72187fb895f6108e7a9dd2b5
ms.sourcegitcommit: 9163a60a28ffce78ceb5dc8dc4fa1b83d7f56e6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2020
ms.locfileid: "86452747"
---
# <a name="deploy-workloads-to-azure-stack-hub"></a>Azure Stack Hub にワークロードをデプロイする

Azure Stack を使用すると、お客様は Azure の自分のインスタンスを、自分のデータセンターで実行できるようになります。 組織が Azure Stack をクラウド戦略の一部として選択するのは、パブリック クラウドではうまくいかない状況への対応に役立つからです。 Azure Stack を使用する、最も一般的な 3 つの理由は、パブリック クラウドへの貧弱なネットワーク接続、規制上または契約上の要件、インターネットに公開できないバックエンド システムです。

## <a name="infrastructure-as-a-service-iaas-deployment"></a>サービスとしてのインフラストラクチャ (IaaS) のデプロイ

デプロイの理由が何であれ、Azure Stack Hub へのデプロイは、他の IaaS のデプロイと似ています。 IaaS を単に仮想マシンだと考える人が多くいますが、IaaS にはもっと多くのことが関係しています。 Azure または Azure Stack に VM をデプロイする場合、マシンには、DNS、パブリック IP、ファイアウォール規則 (ネットワーク セキュリティ グループとも呼ばれます) を含むソフトウェアによるネットワークと、それ以外の多くの機能が含まれることになります。 VM をデプロイすると、Azure Blob Storage を使用して、VM のためのディスクがソフトウェアによるストレージ上に作成されます。

VM の Azure Stack へのデプロイに関する詳細なガイダンスについては、[Azure Stack のコンピューティングの概要](https://docs.microsoft.com/azure-stack/user/azure-stack-compute-overview?view=azs-2002)に関する記事を参照してください。

## <a name="platform-as-a-service-paas-deployment"></a>サービスとしてのプラットフォーム (PaaS) のデプロイ

クラウドでは、すべての PaaS リソースは、仮想マシン (VM) などの何らかの形態のインフラストラクチャ サービス上で実行されます。 とはいえ、Azure サービスはこれらのバックエンド リソースを難読化するので、お客様がそれらを管理する必要はありません。 これらのインフラストラクチャ リソースの難読化や調整は、Azure Resource Manager によって管理されます。 Azure Resource Manager テンプレートを使用して Azure にデプロイする際に、Resource Manager の一つの側面に気づいたかもしれません。 これらのテンプレートにより、呼び出したいリソース プロバイダーやリソースの構成方法が Azure に指定されます。

クラウドをデータセンターで実行する場合は、Stack Hub の管理者は難読化の層について少し知っておく必要があります。 お客様のユーザーまたは開発者が PaaS リソースを使用できるようにする前に、Azure Stack Hub の管理者はマーケットプレースからリソース プロバイダーをインストールする必要があります。 これらのリソース プロバイダーを使用すると、Azure Stack Hub のインスタンスが、Azure のリソース プロバイダーの機能をお客様のスタック インスタンスで複製できるようになります。 Azure Stack Hub のリソース プロバイダーのデプロイについての詳細は、[Azure Stack IaaS のブログ シリーズ](https://azure.microsoft.com/blog/azure-stack-iaas-part-one/)を参照してください。

## <a name="deploy-workloads"></a>ワークロードのデプロイ

Azure Stack Hub の管理者がスタック インスタンスを適切に構成したら、他のほとんどの Azure 移行の作業と同じ方法で移行を継続できます。 お客様が Azure Stack で一般的に行う、いくつかの特定の種類の移行に関するリンクを以下に示します。

- [Ethereum ブロックチェーン ネットワーク](https://docs.microsoft.com/azure-stack/user/azure-stack-ethereum?view=azs-2002)
- [AKS エンジン](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-2002)
- [Azure Cognitive Services](https://docs.microsoft.com/azure-stack/user/azure-stack-solution-template-cognitive-services?view=azs-2002)
- [C# ASP.NET Web アプリ](https://docs.microsoft.com/azure-stack/user/azure-stack-dev-start-howto-vm-dotnet?view=azs-2002)
- [Linux VM](https://docs.microsoft.com/azure-stack/user/azure-stack-dev-start-howto-deploy-linux?view=azs-2002)
- [Java Web アプリ](https://docs.microsoft.com/azure-stack/user/azure-stack-dev-start-howto-vm-java?view=azs-2002)

## <a name="additional-considerations-during-migration"></a>移行に関するその他の考慮事項

以下の記事は、チームが移行とモダン化を行う際に役立ちます。

- 従量課金、VM 可用性セット、仮想マシン スケール セット、ネットワーク アダプター、VM およびディスクの追加とサイズ変更の機能などの[スケーラビリティと可用性](https://azure.microsoft.com/blog/azure-stack-iaas-part-six/)のサービス。
- アップロードとダウンロード、および VM イメージのキャプチャとデプロイを行うための機能を含む、[ストレージ容量](https://azure.microsoft.com/blog/azure-stack-iaas-part-3/)。
- [Azure Stack クイックスタート テンプレート](https://github.com/Azure/AzureStack-QuickStart-Templates)の GitHub リポジトリ。
- [Azure クイックスタート テンプレート](https://github.com/Azure/Azure-QuickStart-Templates)の GitHub リポジトリ。

## <a name="next-step-integrate-this-strategy-into-your-cloud-adoption-journey"></a>次のステップ: この戦略をクラウド導入の取り組みに統合する

次の記事の一覧では、クラウド導入過程の特定の時点におけるガイダンスについて説明します。

- [Azure Stack Hub のガバナンス](./govern.md)
- [Azure Stack Hub の管理](./manage.md)
