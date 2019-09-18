---
title: Azure サーバー管理サービスへのオンボード
titleSuffix: Microsoft Cloud Adoption Framework for Azure
description: Azure サーバー管理サービスへのオンボード
author: BrianBlanchard
ms.author: brblanch
ms.date: 05/10/2019
ms.topic: article
ms.service: cloud-adoption-framework
ms.subservice: operate
ms.openlocfilehash: 330e297b4fb63eaa376e8b6dac0ccf77c2a16ef4
ms.sourcegitcommit: 443c28f3afeedfbfe8b9980875a54afdbebd83a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/16/2019
ms.locfileid: "71031609"
---
# <a name="phase-2-onboarding-azure-server-management-services"></a>フェーズ 2:Azure サーバー管理サービスへのオンボード

Azure 管理サービスに関連する[ツール](./tools-services.md)と[計画](./prerequisites.md)に慣れたら、それらのサービスを Azure リソースと共に使用するためのオンボードの手順を含む 2 番目のフェーズに進むことができます。 このオンボード プロセスを環境内で広く採用する前に、それを評価することから開始します。

> [!NOTE]
> このガイドの以降のセクションで説明する自動化アプローチでは、クラウドにサーバーがまだデプロイされていないデプロイを対象とします。 これには、必要なすべてのリソースとポリシーを作成するためのサブスクリプションに対する所有者の役割が必要です。 Log Analytics ワークスペースと Automation アカウント リソースが既に作成されている場合は、オートメーションのサンプル スクリプトを起動するときに、これらのリソースを適切なパラメーター内で渡すことをお勧めします。

## <a name="onboarding-processes"></a>オンボード プロセス

ガイドのこのセクションでは、Azure 仮想マシンとオンプレミス サーバーの両方を対象に、次のオンボーディング プロセスについて説明します。

- **ポータルを使用して評価のために単一の VM 上の管理サービスを有効にします**。 このプロセスを使用して、Azure サーバー管理サービスを理解します。
- **ポータルを使用してサブスクリプション用の管理サービスを構成します**。 このプロセスは、プロビジョニングされた新しい VM によって管理サービスが自動的に使用されるように Azure 環境を構成するのに役立ちます。 スクリプトやコマンド ラインより Azure portal のエクスペリエンスを好む場合は、このアプローチを使用します。
- **Azure Automation を使用してサブスクリプション用の管理サービスを構成します**。 このプロセスは完全に自動化されます。 必要なのはサブスクリプションの作成だけで、新しくプロビジョニングされた VM に対して管理サービスを使用する環境はスクリプトによって構成されます。 このアプローチは、PowerShell スクリプトと Azure Resource Manager テンプレートに精通しているか、それらの使い方を学習したい場合に使用します。

これらのアプローチの手順はそれぞれ異なります。

> [!NOTE]
> Azure portal を使用している場合の一連のオンボード手順は、自動化されたオンボードの手順とは異なります。ポータルの方がシンプルなオンボード エクスペリエンスが提供されるためです。

次の図は、管理サービスに対して推奨されるデプロイ モデルを示しています。 

![推奨されるデプロイ モデルの図](./media/recommended-deployment.png)

> [!NOTE]
> 上の図に示すように、Log Analytics エージェントには、オンプレミス サーバーの "*自動登録*" と "*オプトイン*" の両方の構成があります。 "*自動登録*" は、Log Analytics エージェントがサーバーにインストールされ、ワークスペースに接続するように構成されている場合に、そのワークスペース上で有効になっているソリューションがサーバーに自動的に適用されることを意味します。 "*オプトイン*" は、エージェントがインストールされ、ワークスペースに接続されている場合でも、ワークスペース内のサーバーのスコープ構成に追加されていない限りソリューションは適用されないことを意味します。

## <a name="next-steps"></a>次の手順

オンボード プロセスを評価するためにポータルを使用して単一の VM をオンボードする方法を確認します。

> [!div class="nextstepaction"]
> [評価のための単一の Azure VM のオンボード](./onboard-single-vm.md)
