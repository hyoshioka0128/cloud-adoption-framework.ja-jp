---
title: Azure Stack Hub にワークロードをデプロイする
description: Azure Stack Hub を使用して、データセンターにワークロードをデプロイする方法を説明します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 05/19/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: migrate
ms.custom: internal
ms.openlocfilehash: 8d25844c2560fd6463bdb77e1b6d5c5116c33001
ms.sourcegitcommit: b6f2b4f8db6c3b1157299ece1f044cff56895919
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2020
ms.locfileid: "97025760"
---
# <a name="deploy-workloads-to-azure-stack-hub"></a>Azure Stack Hub にワークロードをデプロイする

Azure Stack を使用すると、組織は独自の Azure インスタンスをそのデータセンターで実行できます。 組織が Azure Stack をクラウド戦略に含めるのは、パブリック クラウドではうまくいかない状況への対応に役立つからです。 Azure Stack を使用する最も一般的な理由は次の 3 つです。

- パブリック クラウドへのネットワーク接続が貧弱である。
- 規制上または契約上の要件。
- インターネットに公開できないバックエンド システム。

## <a name="infrastructure-as-a-service-deployment"></a>サービスとしてのインフラストラクチャのデプロイ

サービスとしてのインフラストラクチャ (IaaS) をデプロイする理由が何であれ、Azure Stack Hub へのデプロイは、他の IaaS のデプロイと似ています。 IaaS は仮想マシン (VM) にすぎないと考える人が多くいますが、IaaS はそれだけではありません。 Azure または Azure Stack に VM をデプロイする場合、マシンには、ドメイン ネーム システム、パブリック IP、ファイアウォール規則 (ネットワーク セキュリティ グループとも呼ばれます) を含むソフトウェアによるネットワークと、それ以外の多くの機能が含まれることになります。 VM をデプロイすると、Azure Blob Storage を使用して、VM のためのディスクがソフトウェアによるストレージ上に作成されます。

VM の Azure Stack へのデプロイに関する詳細なガイダンスについては、[Azure Stack のコンピューティングの概要](/azure-stack/user/azure-stack-compute-overview?view=azs-2002)に関する記事を参照してください。

## <a name="platform-as-a-service-deployment"></a>サービスとしてのプラットフォームのデプロイ

クラウドでは、すべてのサービスとしてのプラットフォーム (PaaS) リソースは、VM などの何らかの形態のインフラストラクチャ サービス上で実行されます。 とはいえ、Azure サービスはこれらのバックエンド リソースを難読化するので、お客様がそれらを管理する必要はありません。 これらのインフラストラクチャ リソースの難読化や調整は、Azure Resource Manager によって管理されます。 Azure Resource Manager テンプレートを使用して Azure にデプロイする際に、Resource Manager の一つの側面に気づいたかもしれません。 これらのテンプレートにより、呼び出したいリソース プロバイダーやリソースの構成方法が Azure に指定されます。

クラウドをデータセンターで実行する場合は、Stack Hub の管理者は難読化の層について少し知っておく必要があります。 お客様のユーザーまたは開発者が PaaS リソースを使用できるようにする前に、Azure Stack Hub の管理者はマーケットプレースからリソース プロバイダーをインストールする必要があります。 これらのリソース プロバイダーを使用すると、Azure Stack Hub のインスタンスが、Azure のリソース プロバイダーの機能をお客様のスタック インスタンスで複製できるようになります。 Azure Stack Hub のリソース プロバイダーのデプロイについての詳細は、[Azure Stack IaaS のブログ シリーズ](https://azure.microsoft.com/blog/azure-stack-iaas-part-one/)を参照してください。

## <a name="deploy-workloads"></a>ワークロードのデプロイ

Azure Stack Hub の管理者がスタック インスタンスを適切に構成したら、他のほとんどの Azure 移行の作業と同じ方法で移行を継続できます。 Azure Stack を使用すると、チームは次のいずれかの種類の移行を実行できます。

- [Ethereum ブロックチェーン ネットワーク](/azure-stack/user/azure-stack-ethereum?view=azs-2002)
- [AKS エンジン](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-2002)
- [Azure Cognitive Services](/azure-stack/user/azure-stack-solution-template-cognitive-services?view=azs-2002)
- [C# ASP.NET Web アプリ](/azure-stack/user/azure-stack-dev-start-howto-vm-dotnet?view=azs-2002)
- [Linux VM](/azure-stack/user/azure-stack-dev-start-howto-deploy-linux?view=azs-2002)
- [Java Web アプリ](/azure-stack/user/azure-stack-dev-start-howto-vm-java?view=azs-2002)

## <a name="additional-considerations-during-migration"></a>移行に関するその他の考慮事項

以下の記事は、チームが移行とモダン化を行う際に役立ちます。

- 従量課金、VM 可用性セット、VM スケール セット、ネットワーク アダプター、VM とディスクの追加やサイズ変更の機能などの[スケーラビリティと可用性](https://azure.microsoft.com/blog/azure-stack-iaas-part-six/)のサービス
- VM イメージをアップロードし、ダウンロードし、キャプチャし、デプロイするための機能を含む、[ストレージ容量](https://azure.microsoft.com/blog/azure-stack-iaas-part-3/)
- [Azure Stack クイックスタート テンプレート](https://github.com/Azure/AzureStack-QuickStart-Templates)の GitHub リポジトリ
- [Azure クイックスタート テンプレート](https://github.com/Azure/Azure-QuickStart-Templates)の GitHub リポジトリ

## <a name="next-steps"></a>次のステップ

クラウド導入の取り組みの特定の要素に関するガイダンスについては、以下を参照してください。

- [Azure Stack Hub のガバナンス](./govern.md)
- [Azure Stack Hub の管理](./manage.md)
