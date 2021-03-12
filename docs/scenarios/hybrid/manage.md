---
title: ハイブリッドおよびマルチクラウド運用間でポートフォリオを管理する
description: Azure のエンタープライズ コントロール プレーンを使用して、ハイブリッドおよびマルチクラウドのデプロイ間で運用管理を行うための効果的な制御を実装します。
author: brianblanchard
ms.author: brblanch
ms.date: 02/01/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: operate
ms.custom: e2e-hybrid
ms.openlocfilehash: c7586b799203b556446d7f34311f8a468308e398
ms.sourcegitcommit: b8f8b7631aabaab28e9705934bf67dad15e3a179
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101800100"
---
# <a name="manage-your-portfolio-across-hybrid-and-multicloud-operations"></a>ハイブリッドおよびマルチクラウド運用間でポートフォリオを管理する

ハイブリッドやマルチクラウドを導入することで、クラウドの運用方法を徐々に移行することができます。 Azure の Microsoft Cloud Adoption Framework における[管理手段](../../manage/index.md)では、運用のベースラインを実装し、クラウド導入のライフサイクルを通してそのベースラインを成熟させるための道筋を概説しています。 ハイブリッド、マルチクラウド、エッジ デプロイが含まれるよう戦略を拡張するには、適切な運用管理をどのように実装するのかを変える必要があります。 このように変化する要件に対応するためのベスト プラクティスとなる概念が、[統合運用](./unified-operations.md)です。

次のセクションでは、統合運用の概念を採用する方法と、ハイブリッド、マルチクラウド、エッジ運用を効果的に行うためのベスト プラクティスを実装する方法について説明します。

## <a name="extending-your-operations-baseline"></a>運用ベースラインを拡張する

[Azure Arc](/azure/azure-arc/overview) を使用すると、運用ベースラインを拡張するための複雑さとコストを削減することができます。 データセンター、ハイブリッド クラウド、マルチクラウド環境に Azure Arc をデプロイすることで、Azure Resource Manager に備わっている Azure のネイティブ機能を拡張することができます。

複数のクラウド プロバイダーにまたがる運用ベースラインの使用を開始するため、まずは簡単なインベントリとタグ付けの演習を行いましょう。 この簡単な演習では、次のいくつかの簡単な手順で運用ベースラインの拡張を開始します。

- ハイブリッド、マルチクラウド、エッジのすべての資産に `hosting platform` のタグを追加します。
- AWS や GCP などからのリソースにタグを付けます。
- リソースに対してクエリを実行し、ホストされている場所を見つけます。

まずは、[ハイブリッドおよびマルチクラウド リソースのインベントリとタグ付けを行い](../../manage/hybrid/server/best-practices/arc-inventory-tagging.md)ます。

<!-- docutune:casing "update management guide" -->

基本的なインベントリとタグ付けの演習を完了したら、ハイブリッド環境とマルチクラウド環境の運用を開始できます。 クラウド間で運用を拡張するお客様のほとんどが最初に行うステップが、**パッチと更新管理のための一貫した計画を確立する** ことです。 [ハイブリッドおよびマルチクラウド更新管理ガイド](../../manage/hybrid/server/best-practices/arc-update-management.md)に従って、クラウド プロバイダー全体のパッチを制御するツールをデプロイします。

## <a name="enhanced-baseline"></a>拡張ベースライン

継続的に広範な資産やクラウド プロバイダーをオンボードすることで、運用ベースラインを拡張することができます。 以下のリストは、拡張した運用ベースラインに追加できる資産の種類の一例です。

- Azure 資産のオンボード: [Linux VM](../../manage/hybrid/server/best-practices/arm-template-linux.md) と [Windows VM](../../manage/hybrid/server/best-practices/arm-template-windows.md)
- ローカル データセンターへの資産のオンボード: [Linux VM](../../manage/hybrid/server/best-practices/onboard-server-linux.md) と [Windows VM](../../manage/hybrid/server/best-practices/onboard-server-windows.md)
- VMware 資産のオンボード: [Linux VM](../../manage/hybrid/server/best-practices/vmware-scaled-powercli-linux.md) と [Windows VM](../../manage/hybrid/server/best-practices/vmware-scaled-powercli-windows.md)
- AWS 資産のオンボード: [Linux VM (Terraform を使用)](../../manage/hybrid/server/best-practices/aws-terraform-al2.md) と [AWS Ubuntu (Terraform を使用)](../../manage/hybrid/server/best-practices/aws-terraform-ubuntu.md)
- GCP 資産のオンボード: [Ubuntu VM](../../manage/hybrid/server/best-practices/gcp-terraform-ubuntu.md) と [Windows VM](../../manage/hybrid/server/best-practices/gcp-terraform-windows.md)

## <a name="operations-management-disciplines"></a>運用管理の規範

ハイブリッドおよびマルチクラウド ツールを使用することで、資産のタグ付けとオンボードだけでなく、さまざまな運用管理の規範を提供することができます。

成熟した運用管理の規範の 1 つの例として、Microsoft Monitoring Agent を使用したソフトウェアのインストール、ウイルス対策保護、またはその他の構成管理機能の管理があります。 次の記事では、ハイブリッドおよびマルチクラウド環境での MMA の構成について説明します。

- [MMA で VM を管理する](../../manage/hybrid/server/best-practices/arc-vm-extension-mma.md)
- [MMA のスケール構成](../../manage/hybrid/server/best-practices/arc-vm-extension-custom-script.md)

## <a name="next-step-your-next-migration-iteration"></a>次のステップ: 次の移行の繰り返し

統合運用の移行が完了したら、クラウド導入チームはその次のシナリオ固有の移行を開始できます。 また、移行するプラットフォームが他にもある場合は、次回の統合運用の移行やデプロイを行う際に役立つ、以下の記事を参照してください。

- [統合運用の戦略](./strategy.md)
- [統合運用の計画](./plan.md)
- [環境または Azure ランディング ゾーンを確認する](./ready.md)
- [ハイブリッドおよびマルチクラウド移行](./migrate.md)
- [ハイブリッドおよびマルチクラウド環境のガバナンス](./govern.md)
