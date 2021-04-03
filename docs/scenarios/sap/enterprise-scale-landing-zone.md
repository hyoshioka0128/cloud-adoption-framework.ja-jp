---
title: SAP on Azure 構築セットのエンタープライズ規模のサポート
description: プラットフォーム ワークロード ランディング ゾーンを構築できるエンタープライズ規模の構築セットについて説明します。
author: JefferyMitchell
ms.author: brblanch
ms.date: 02/15/2021
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.custom: think-tank, e2e-sap
ms.openlocfilehash: 6cc81ddf55c9986f2c39ecdbc5ef8fc8dab3356a
ms.sourcegitcommit: 5716a8165934bd69d02d9d3641785039196aee3a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "105734622"
---
# <a name="enterprise-scale-support-for-sap-on-azure-construction-set"></a>SAP on Azure 構築セットのエンタープライズ規模のサポート

エンタープライズ規模の構築セットは、クラウド導入フレームワークのエンタープライズ規模のランディング ゾーン内で、プラットフォーム ワークロード ランディング ゾーンの効果的な構築と運用化を可能にする、特定のアーキテクチャのアプローチと参照の実装を提供します。 エンタープライズ規模のランディング ゾーンを正常に実装したら、SAP on Azure 構築セットを使用できます。 SAP on Azure 構築セットをデプロイする前に、エンタープライズ規模の [概要](../../ready/enterprise-scale/index.md) と [実装に関するガイダンス](../../ready/enterprise-scale/implementation.md) を確認してください。

## <a name="adopting-the-sap-on-azure-construction-set"></a>SAP on Azure 構築セットの導入

すべてのお客様が同じ方法で SAP on Azure を導入するわけではありません。 Azure エンタープライズ規模の SAP on Azure 構築セット アーキテクチャ向けのクラウド導入フレームワークは、お客様によって異なります。 SAP on Azure 構成セットの技術的な考慮事項と設計上の推奨事項により、組織のシナリオに基づいて異なるトレードオフが生じる可能性があります。 中心となる推奨事項に従うと、結果として得られるアーキテクチャにより、組織にとって維持可能なスケールへのパスが実現します。 SAP on Azure の構築セットは、設計によってモジュール化されています。 環境変数をカスタマイズできます。 ランディング ゾーンに対するアプローチには、クラウド チームをサポートするために次の 3 つの資産のセットが含まれています。

- [設計ガイドライン](#design-guidelines)
- [アーキテクチャ](#architecture)
- [実装](#implementation)

## <a name="design-guidelines"></a>設計ガイドライン

Azure のクラウド導入フレームワークにおけるエンタープライズ規模のランディング ゾーンの設計を推進する重要な意思決定を行わなければなりません。 次の 6 つの重要なデザイン領域を考えてみましょう。

- [ID 管理とアクセス管理](./eslz-identity-and-access-management.md)
- [ネットワーク トポロジと接続](./eslz-network-topology-and-connectivity.md)
- [管理と監視](./eslz-management-and-monitoring.md)
- [事業継続とディザスター リカバリー](./eslz-business-continuity-and-disaster-recovery.md)
- [セキュリティ、ガバナンス、コンプライアンス](./eslz-security-governance-and-compliance.md)
- [プラットフォームの自動化と DevOps](./eslz-platform-automation-and-devops.md)

## <a name="architecture"></a>アーキテクチャ

次の図は、デザイン領域を示す概念参照アーキテクチャを示しています。

![SAP 構築セットの概念参照アーキテクチャを示す図。](./media/overview-architecture.png)

## <a name="implementation"></a>実装

**SAP on Azure デプロイ自動化フレームワーク** は、柔軟なワークフローと組み合わせたプロセスの集まりです。 [SAP デプロイ自動化フレームワーク リポジトリ](https://github.com/Azure/sap-hana)には、SAP ランドスケープを Azure クラウドに自動的にデプロイするコードが含まれています。

テンプレートは 2 つのカテゴリに分けられます。

- Terraform モジュールは、インフラストラクチャ コンポーネントを Azure にデプロイします。
  - VM
  - ネットワーク
  - ストレージ
- Ansible プレイブックは、さまざまなロールを実行します。
  - VM の設定とデプロイ
  - SAP HANA のインストール
  - その他の必要なアプリケーションをインストールする

Terraform モジュールによって構築されたインフラストラクチャに、Ansible プレイブック コンポーネントをデプロイしてインストールします。

![SAP 参照実装の概要を示す図](./media/overview-automation.png)

## <a name="next-steps"></a>次の手順

SAP on Azure の構築セットアーキテクチャの包括的な考慮事項と推奨事項を作成するために不可欠な SAP on Azure 構築セットの設計領域を確認します。

- [ID 管理とアクセス管理](./eslz-identity-and-access-management.md)
