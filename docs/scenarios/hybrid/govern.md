---
title: ハイブリッドおよびマルチクラウド ワークロードのポートフォリオのガバナンス
description: ガバナンス機能をハイブリッド、マルチクラウド、エッジのデプロイに拡張する
author: brianblanchard
ms.author: brblanch
ms.date: 02/01/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: govern
ms.custom: e2e-hybrid
ms.openlocfilehash: cd132ec894f26cd9ca3407a138b215b7217d2cf6
ms.sourcegitcommit: b8f8b7631aabaab28e9705934bf67dad15e3a179
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101801278"
---
# <a name="govern-your-portfolio-of-hybrid-and-multicloud-workloads"></a>ハイブリッドおよびマルチクラウド ワークロードのポートフォリオのガバナンス

クラウドの出現により、IT ガバナンスは根本的に代わりました。 現在、集中的な手動によるレビューや変更制御プロセスは、自動化されたガードレールやコンプライアンス ツールに置き換えることができます。 クラウド導入およびワークロードの担当チームは、コンプライアンスとガバナンスの要件が検出され、多くの場合自動化されているとわかっているため、自信を持ってイノベーションを行うことができます。 この新たな自由を実現する鍵は、クラウドが持つコードとしてのインフラストラクチャの基盤にあります。 すべての資産は、他のコード ベースと同様にテストとガバナンスが可能な、定義されたコード ブロックが基になっています。

ハイブリッド、マルチクラウド、およびエッジ戦略により、クラウド ガバナンスのメリットをクラウド以外にも拡張できるようになりました。 [Azure Arc](/azure/azure-arc/overview) を [Azure Policy](/azure/governance/policy/overview)、[Azure Blueprints](/azure/governance/blueprints/overview)、およびその他のガバナンス ツールと組み合わせることによって、多くのガバナンス ガードレールをすべてのクラウド リソース (プライベートまたはパブリック クラウドを問わず) に拡張できるようになります。 [統合運用](./unified-operations.md)は、Azure のネイティブ ツールを使用してガバナンス コントロールを拡張するためのベスト プラクティスとなる概念です。

## <a name="deploy-an-unified-operations-mvp-for-governance"></a>ガバナンスのための統合されたオペレーション MVP をデプロイする

ガバナンスを適切に定義するには、リソースの一貫性を実現するための確実な手法を採用することから始まります。 リソース、リソース グループ、サブスクリプション、[管理グループを整理することで、ガバナンスがしやすく](/azure/governance/management-groups/overview)なります。 以下のいくつかの簡単な手順を行い、クラウドのガバナンス プラクティスを拡大します。

- ハイブリッド、マルチクラウド、エッジのすべての資産に `hosting platform` のタグを追加します
- AWS や GCP などからのリソースにタグを付けます。
- リソースに対してクエリを実行し、それらがホストされている場所を確認します。

まずは、[ハイブリッドおよびマルチクラウド リソースのインベントリとタグ付けを行い](../../manage/hybrid/server/best-practices/arc-inventory-tagging.md)ます。

タグ付けの規則を確立し、いくつかの資産をオンボードしたら、Azure Policy などの使い慣れたガバナンス ツールを使用してリソースの管理を開始できます。 ハイブリッドおよびマルチクラウド リソースにポリシーを割り当てる方法については、[Azure Policy を使用した Azure Arc 対応サーバーの管理](../../manage/hybrid/server/best-practices/arc-policies-mma.md)に関するページに記載されている推奨プラクティスを参照してください。

## <a name="governance-disciplines"></a>ガバナンスの規範

統合運用と Azure Arc の基本を理解すれば、Azure 環境以外の場所でホストされているデプロイにもクラウド ガバナンスの規範を拡張することができます。

セキュリティ ベースラインは、お客様が統合運用シナリオでガバナンスの規範を拡張できる最も一般的な方法です。 次のベスト プラクティスは、環境を問わず、セキュリティ ベースラインを維持するのに役立ちます。

- [Azure Security Center を使用してクラウド上のセキュリティ データを取得および検出する](/azure/security-center/quickstart-onboard-machines)
- [Azure Sentinel を使用してセキュリティ脅威を調査および対応する](/azure/sentinel/tutorial-investigate-cases)
- [AWS アカウントを Azure Defender に接続する](/azure/security-center/quickstart-onboard-aws)
- [GCP アカウントを Azure Defender に接続する](/azure/security-center/quickstart-onboard-gcp)

## <a name="next-step-manage-hybrid-and-multicloud-environments"></a>次のステップ: ハイブリッドおよびマルチクラウド環境の管理

次の記事の一覧は、クラウド導入のシナリオで成功するために役立つ、クラウド導入の取り組みにおける特定のポイントごとのガイダンスをまとめたものです。

- [ハイブリッドおよびマルチクラウド環境の管理](./manage.md)
