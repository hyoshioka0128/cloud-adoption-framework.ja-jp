---
title: レプリケーション オプション
description: Azure 向けのクラウド導入フレームワークを使用して、レプリケーション プロセスを把握すると共に、クラウド移行になぜレプリケーションが必要かを理解します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: migrate
ms.custom: internal
ms.openlocfilehash: 858ae7a6c9ebfa63af0148088bd51de3bd04334a
ms.sourcegitcommit: b8f8b7631aabaab28e9705934bf67dad15e3a179
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101788438"
---
# <a name="replication-options"></a>レプリケーション オプション

すべての移行の前に、プライマリ システムが安全で、問題なく動作を続けることを確認する必要があります。 ダウンタイムがあるとユーザーやお客様の作業が中断され、時間とコストがかかります。 移行は、オンプレミスの仮想マシンをオフにして Azure にコピーするような簡単なことではありません。 移行ツールでは、ライブ システムをダウンタイムなしで Azure にコピーできるように、非同期レプリケーションまたは同期レプリケーションを考慮する必要があります。 中でも、システムはオンプレミスの対応する部分と同期が取れている必要があります。 移行したリソースを Azure の分離されたパーティションでテストし、ワークロードが想定どおりに動作するのを確認することが必要な場合があります。

クラウド導入フレームワーク内のコンテンツでは、資産をクラウドにレプリケートするには Azure Migrate (または Azure Site Recovery) が最適なツールであると想定されています。 ただし、他のオプションも利用できます。 この記事では、意思決定に役立つようにそれらのオプションについて説明します。

## <a name="azure-site-recovery-also-known-as-azure-migrate"></a>Azure Site Recovery (Azure Migrate とも呼ばれます)

[Azure Site Recovery](/azure/site-recovery/site-recovery-overview) では、Azure VM、オンプレミス VM、物理サーバーのディザスター リカバリーの調整と管理が行われます。 Site Recovery を使うと、オンプレミスおよび他のクラウド プロバイダーのマシンの Azure への移行を管理することもできます。 オンプレミスのマシンを Azure に、または Azure VM をセカンダリ リージョンにレプリケートします。 その後、VM をプライマリ サイトからセカンダリにフェールオーバーして、移行プロセスが完了します。 Azure Site Recovery では、さまざまな移行シナリオを実現できます。

- **オンプレミスから Azure への移行。** オンプレミスの VMware VM、Hyper-V VM、物理サーバーを Azure に移行します。 これを行うには、完全なディザスター リカバリーの場合とほぼ同じ手順を実行します。 Azure からオンプレミス サイトにマシンをフェールバックしないだけです。
- **Azure リージョン間の移行。** Azure VM をある Azure リージョンから別のリージョンに移行します。 移行が完了した後、現在は移行先のセカンダリ リージョンにある Azure VM に対してディザスター リカバリーを構成します。
- **他のクラウドから Azure への移行。** 他のクラウド プロバイダーでプロビジョニングされたコンピューティング インスタンスを Azure VM に移行できます。 Site Recovery では、それらのインスタンスは移行目的の物理サーバーとして扱われます。

![Azure Site Recovery](../../../_images/migrate/asr-replication-image.png)
*図 1:資産を Azure または他のクラウドに移動している Azure Site Recovery。*

移行についてオンプレミスおよびクラウドのインフラストラクチャを評価した後は、移行戦略でオンプレミス マシンをレプリケートするときに Azure Site Recovery が役に立ちます。 次の簡単な手順で、オンプレミス VM、物理サーバー、クラウド VM インスタンスの Azure への移行を設定できます。

- 前提条件を確認します。
- Azure リソースを準備します。
- オンプレミス VM またはクラウド インスタンスを移行用に準備します。
- 構成サーバーをデプロイします。
- VM のレプリケーションを有効にします。
- フェールオーバーをテストして、すべてが問題なく動作することを確認します。
- Azure へのワンタイム フェールオーバーを実行します。

## <a name="azure-database-migration-service"></a>Azure Database Migration Service

このサービスは、複数のツールではなく、1 つの包括的なサービスを使用することで、クラウド移行の複雑さを軽減するのに役立ちます。 [Azure Database Migration Service](/azure/dms/dms-overview) は、オンプレミスの SQL Server データベースをクラウドに移行するためのシームレスなエンドツーエンドのソリューションとして設計されています。 それは、複数のデータベース ソースから Azure データ プラットフォームへのシームレスな移行を最小限のダウンタイムで実現できるように設計された、フル マネージドのサービスです。 既存のツールとサービスのいくつかの機能が統合されており、お客様に包括的で高可用性のソリューションが提供されます。

このサービスでは、Data Migration Assistant を使用して評価レポートを生成します。評価レポートには、移行を実行する前に必要な変更について推奨される手順が記載されています。 必要な修正を実行するかどうかは、お客様の判断に委ねられます。 移行プロセスを開始する準備ができたら、Azure Database Migration Service によって、関連するすべての手順が実行されます。 プロセスは Microsoft によって決定されたベスト プラクティスを利用して実行されるので、お客様は安心して移行プロジェクトの完了を待つことができます。

## <a name="next-steps"></a>次のステップ

レプリケーションが完了した後は、[ステージング アクティビティ](./stage.md)を開始できます。

> [!div class="nextstepaction"]
> [移行の間のステージング アクティビティ](./stage.md)
