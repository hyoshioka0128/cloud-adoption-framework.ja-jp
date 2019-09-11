---
title: クラウド監視ガイド
titleSuffix: Microsoft Cloud Adoption Framework for Azure
description: Azure Monitor と System Center Operations Manager の概要
author: MGoedtel
ms.author: magoedte
ms.date: 07/31/2019
ms.topic: guide
ms.service: cloud-adoption-framework
ms.subservice: operate
services: azure-monitor
ms.openlocfilehash: 21c855c31bca641eafe416ddee79370935c8b288
ms.sourcegitcommit: a26c27ed72ac89198231ec4b11917a20d03bd222
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/06/2019
ms.locfileid: "70818388"
---
# <a name="cloud-monitoring-guide-introduction"></a>クラウド監視ガイド:はじめに

クラウドは、企業によるテクノロジ リソースの調達と利用の方法が根本的に変化していることの表れです。 以前は、インフラストラクチャからソフトウェアまで、あらゆるレベルのテクノロジの所有権と責任が企業にあることが前提になっていました。 今では、クラウドによって、企業は必要に応じてリソースをプロビジョニングして使用できる可能性があります。

設計の選択という点から言えば、クラウドはほぼ無制限の柔軟性をもたらしますが、企業が求めているのは、一貫性のある実証済みのクラウド テクノロジ導入手法です。 クラウド導入の目標もタイムラインも企業ごとに異なるため、1 つのアプローチですべての導入に対応することはほぼ不可能です。

![クラウド導入戦略の図](./media/monitoring-management-guidance-cloud-and-on-premises/introduction-cloud-adoption.png)

このデジタル変革は、お使いのインフラストラクチャ、ワークロード、およびアプリケーションを最新化する機会でもあります。 ビジネスの戦略と目標によっては、オンプレミスからクラウドでの完全な運用への移行の一環として、ハイブリッド クラウド モデルを導入する可能性が高くなります。 この変革の過程で、IT チームには、クラウドからすぐに得られる利点を導入し具現化することが求められます。 また、IT チームは Azure に移行するアプリケーションやサービスを効果的に監視しつつ、効果的な IT 運用/DevOps を維持できる方法を見つけ出すことも求められます。

利害関係者は、クラウドベースのサービスとしてのソフトウェア (SaaS) 監視と管理ツールの活用を求めています。 彼らは、エンドツーエンドの可視性を確保し、コストを削減し、インフラストラクチャや従来のソフトウェアベース IT 運用ツールのメンテナンスへの対応を減らすために、サービスやソリューションによって何が提供されるかを理解する必要があります。

しかし、IT 部門としては、 サービス運用プロセスをサポートするために多大な投資を行ってきたツールを活用して両方のクラウド モデルの監視を行い、最終的には SaaS ベースのサービスに移行したいと思われるでしょう。 この方法が選ばれる理由は、切り替えのために計画に時間がかかったり、リソースや資金がかかったりすることだけではありません。 どの製品または Azure サービスが移行を実現するのに適しているのか、あるいは適用できるのかについて当惑してしまうためでもあります。

このガイドの目的は、エンタープライズ IT マネージャー、ビジネス意思決定者、アプリケーション アーキテクト、およびアプリケーション開発者が次のことを理解できるように、詳細なリファレンスを提供することにあります。

* Azure 監視プラットフォーム、およびその機能の概要と比較
* ハイブリッド、プライベート、および Azure ネイティブのワークロードの監視に最適なソリューション
* インフラストラクチャとアプリケーションの両方に対してエンドツーエンドで推奨される監視アプローチ。 これには、Azure に移行するこれらの一般的なワークロードのためのデプロイ可能なソリューションも含まれます。

このガイドは、個々の Azure サービスやソリューションを使用または構成するためのハウツー ガイドではありませんが、該当する場合や利用可能な場合には、それらのソースが参照されます。 このガイドを読むことで、推奨されるプラクティスとパターンに従ってワークロードを正常に処理する方法を理解できます。

Azure Monitor と System Center Operations Manager に精通していない場合に、それらを比類のないものにしている要素と、それらの比較について理解を深めてから詳細に進みたい場合は、[監視プラットフォームの概要](./platform-overview.md)に関するページを参照してください。

## <a name="audience"></a>対象ユーザー

このガイドは主に、エンタープライズ管理者、IT 運用、IT セキュリティとコンプライアンス、アプリケーション アーキテクト、ワークロード開発所有者、およびワークロード操作所有者を対象としています。

## <a name="how-this-guide-is-structured"></a>本書の構成

この記事はシリーズの一部です。 次の記事は、次の順で、まとめて読まれるように作られています。

* はじめに (この記事)
* [クラウド デプロイ モデルの監視戦略](./cloud-models-monitor-overview.md)
* [適切なデータの収集](./data-collection.md)
* [アラート](./alert.md)

## <a name="products-and-services"></a>製品およびサービス

Azure、お客様の企業ネットワーク、その他のクラウド プロバイダーでホストされているさまざまなリソースを監視、管理するためのソフトウェアとサービスを選択できます。 次に例を示します。

* System Center Operations Manager
* Azure Monitor (現在、Log Analytics と Application Insights が含まれています)
* Azure Policy と Azure Blueprints
* Azure Automation
* Azure Logic Apps
* Azure Event Hubs

このガイドの最初のバージョンでは、現在の監視プラットフォームである Azure Monitor と System Center Operations Manager について説明し、各クラウド デプロイ モデルを監視するための推奨される戦略の概要を示します。 また、データ収集とアラートをはじめとする、監視に関する推奨事項の最初のセットも含まれています。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [クラウド デプロイ モデルの監視戦略](./cloud-models-monitor-overview.md)
