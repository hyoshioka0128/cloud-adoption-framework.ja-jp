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
ms.openlocfilehash: 2affa2383f958ec432f163b68b216a8ce394283c
ms.sourcegitcommit: c167c45b66cc7324b60c88b8b7aac439f956b65d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102208508"
---
# <a name="govern-your-portfolio-of-hybrid-and-multicloud-workloads"></a>ハイブリッドおよびマルチクラウド ワークロードのポートフォリオのガバナンス

クラウドの出現により、IT ガバナンスは根本的に代わりました。 現在、集中的な手動によるレビューや変更制御プロセスは、自動化されたガードレールやコンプライアンス ツールに置き換えることができます。 クラウド導入およびワークロードの担当チームは、コンプライアンスとガバナンスの要件が検出され、多くの場合自動化されているとわかっているため、自信を持ってイノベーションを行うことができます。 この新たな自由を実現する鍵は、クラウドが持つコードとしてのインフラストラクチャの基盤にあります。 すべての資産は、他のコード ベースと同様にテストとガバナンスが可能な、定義されたコード ブロックが基になっています。

ハイブリッド、マルチクラウド、およびエッジ戦略により、クラウド ガバナンスのメリットをクラウド以外にも拡張できるようになりました。 [Azure Arc](/azure/azure-arc/overview) は [Azure Policy](/azure/governance/policy/overview)、[Azure Blueprints](/azure/governance/blueprints/overview)、その他のガバナンス ツールと結合できます。 結合により、ガバナンス ガードレールの多くが事実上あらゆるクラウド リソース、プライベートまたはパブリック クラウドまで同様に拡大されます。 [統合運用](./unified-operations.md)は、Azure のネイティブ ツールを使用してガバナンス コントロールを拡張するための最良の概念です。

## <a name="deploy-a-unified-operations-mvp-for-governance"></a>ガバナンスのための統合されたオペレーション MVP をデプロイする

ガバナンスを適切に定義するには、リソースの一貫性を実現するための確実な手法を採用することから始まります。 リソース、リソース グループ、サブスクリプション、[管理グループを整理することで、ガバナンスがしやすく](/azure/governance/management-groups/overview)なります。 以下の簡単な手順でクラウドのガバナンス プラクティスを拡大します。

- ハイブリッド、マルチクラウド、エッジのすべての資産に `hosting platform` のタグを追加します。
- AWS や GCP などからのリソースにタグを付けます。
- リソースに対してクエリを実行し、リソースがホストされている場所を確認します。

まずは、[ハイブリッドおよびマルチクラウド リソースのインベントリとタグ付けを行います](../../manage/hybrid/server/best-practices/arc-inventory-tagging.md)。

タグ付けの規則を確立し、いくつかの資産を取り込んだら、Azure Policy などの使い慣れたガバナンス ツールを使用してリソースの管理を開始できます。 ハイブリッドおよびマルチクラウド リソースにポリシーを割り当てる方法については、[Azure Policy を使用した Azure Arc 対応サーバーの管理](../../manage/hybrid/server/best-practices/arc-policies-mma.md)に関するページに記載されている推奨プラクティスを参照してください。

## <a name="governance-disciplines"></a>ガバナンスの規範

統合運用と Azure Arc の基本を理解すれば、Azure 環境以外の場所でホストされているデプロイにクラウド ガバナンスの規範を拡張できます。

セキュリティ ベースラインは、統合運用シナリオでガバナンスの規範を拡張できる最も一般的な方法の 1 つです。 次のベスト プラクティスは、環境を問わず、セキュリティ ベースラインを維持するのに役立ちます。

- [Azure Security Center を使用してクラウド上のセキュリティ データを取得および検出する](/azure/security-center/quickstart-onboard-machines)
- [Azure Sentinel を使用してセキュリティ脅威を調査および対応する](/azure/sentinel/tutorial-investigate-cases)
- [AWS アカウントを Azure Defender に接続する](/azure/security-center/quickstart-onboard-aws)
- [GCP アカウントを Azure Defender に接続する](/azure/security-center/quickstart-onboard-gcp)

## <a name="next-steps"></a>次の手順

クラウドの導入に関する詳細なガイダンスについては、次の記事を参照してください。

- [ハイブリッドおよびマルチクラウド環境を管理する](./manage.md)
