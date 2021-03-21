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
ms.openlocfilehash: bc9912d7226b204e34c6944585ab3b2a73c4ff12
ms.sourcegitcommit: c167c45b66cc7324b60c88b8b7aac439f956b65d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102208525"
---
# <a name="manage-your-portfolio-across-hybrid-and-multicloud-operations"></a>ハイブリッドおよびマルチクラウド運用間でポートフォリオを管理する

ハイブリッドおよびマルチクラウド環境を使用すると、クラウドでの運用方法が自然に変化します。 Azure の Cloud Adoption Framework における[管理手段](../../manage/index.md)では、運用のベースラインを実装し、クラウド導入のライフサイクルを通してそのベースラインを発展させるための道筋を概説しています。 ハイブリッド、マルチクラウド、エッジ デプロイが含まれるよう戦略を拡張するには、適切な運用管理を実装する方法を変える必要があります。 このように変化する要件に対応するための最適な概念が、[統合運用](./unified-operations.md)です。

次のセクションでは、統合運用の概念を採用する方法と、ハイブリッド、マルチクラウド、エッジ運用を効果的に行うためのベスト プラクティスを実装する方法について説明します。

## <a name="extend-your-operations-baseline"></a>運用ベースラインを拡張する

[Azure Arc](/azure/azure-arc/overview) を使用すると、運用ベースラインを拡張するための複雑さとコストを削減することができます。 データセンター、ハイブリッド クラウド、マルチクラウド環境に Azure Arc をデプロイすることで、Azure Resource Manager に備わっている Azure のネイティブ機能を拡張することができます。

オンプレミスと複数のクラウド プロバイダーにまたがる運用ベースラインの使用を開始するため、まずはインベントリとタグ付けの演習を行いましょう。 この演習では、次のいくつかの手順で運用ベースラインの拡張を開始します。

- ハイブリッド、マルチクラウド、エッジのすべての資産に `hosting platform` のタグを追加します。
- AWS や GCP などからのリソースにタグを付けます。
- リソースに対してクエリを実行し、ホストされている場所を見つけます。

まずは、[ハイブリッドおよびマルチクラウド リソースのインベントリとタグ付けを行います](../../manage/hybrid/server/best-practices/arc-inventory-tagging.md)。

<!-- docutune:casing "update management guide" -->

演習を完了すると、ハイブリッドおよびマルチクラウド環境の運用を開始できます。 通常、クラウド全体で操作を拡張するときに最初に行う手順は、"*パッチと更新プログラムの管理の一貫した計画を立てる*" ことです。 クラウド プロバイダー全体のパッチを制御するツールをデプロイするには、[ハイブリッドおよびマルチクラウド更新管理のガイド](../../manage/hybrid/server/best-practices/arc-update-management.md)の手順に従います。

## <a name="enhanced-baseline"></a>拡張ベースライン

継続的に広範な資産とクラウド プロバイダーを取り込んで、操作のベースラインを拡張します。 以下のリストは、拡張した運用ベースラインに追加できる資産の種類の一例です。

- **Azure 資産**: [Linux VM](../../manage/hybrid/server/best-practices/arm-template-linux.md) と [Windows VM](../../manage/hybrid/server/best-practices/arm-template-windows.md)
- **ローカル データセンターの資産**: [Linux VM](../../manage/hybrid/server/best-practices/onboard-server-linux.md) と [Windows VM](../../manage/hybrid/server/best-practices/onboard-server-windows.md)
- **VMware 資産**: [Linux VM](../../manage/hybrid/server/best-practices/vmware-scaled-powercli-linux.md) と [Windows VM](../../manage/hybrid/server/best-practices/vmware-scaled-powercli-windows.md)
- **AWS 資産**: [Linux VM (Terraform を使用)](../../manage/hybrid/server/best-practices/aws-terraform-al2.md) と [AWS Ubuntu (Terraform を使用)](../../manage/hybrid/server/best-practices/aws-terraform-ubuntu.md)
- **GCP 資産**: [Ubuntu VM](../../manage/hybrid/server/best-practices/gcp-terraform-ubuntu.md) と [Windows VM](../../manage/hybrid/server/best-practices/gcp-terraform-windows.md)

## <a name="operations-management-disciplines"></a>運用管理の規範

資産のタグ付けと導入に加えて、ハイブリッド ツールとマルチクラウド ツールを使用して、多くの運用管理の規範を提供することもできます。

成熟した運用管理の規範の 1 つの例として、Microsoft Monitoring Agent を使用したソフトウェアのインストール、ウイルス対策保護、またはその他の構成管理機能の管理があります。 次の記事では、ハイブリッドおよびマルチクラウド環境での Monitoring Agent の構成について説明します。

- [Monitoring Agent を使用した VM の管理](../../manage/hybrid/server/best-practices/arc-vm-extension-mma.md)
- [Monitoring Agent のスケール構成](../../manage/hybrid/server/best-practices/arc-vm-extension-custom-script.md)

## <a name="next-steps"></a>次の手順

統合運用の移行が完了したら、クラウド導入チームはその次のシナリオ固有の移行を開始できます。 移行するプラットフォームが他にもある場合は、次回の統合運用の移行やデプロイを行う際に役立つ、以下の記事を参照してください。

- [統合運用の戦略](./strategy.md)
- [統合運用の計画](./plan.md)
- [環境または Azure ランディング ゾーンを確認する](./ready.md)
- [ハイブリッドおよびマルチクラウドへの移行](./migrate.md)
- [ハイブリッドおよびマルチクラウド環境のガバナンス](./govern.md)
