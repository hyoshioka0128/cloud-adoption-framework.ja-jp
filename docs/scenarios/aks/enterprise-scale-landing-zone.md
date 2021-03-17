---
title: AKS のエンタープライズ規模のシナリオ用の構築セット
description: Azure Kubernetes Service (AKS) の構築セットを使用して、AKS をサポートするエンタープライズ規模のランディング ゾーンを作成します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 03/01/2021
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.openlocfilehash: ddeeb4518141224cf0f210c208cf80a718f4f3f7
ms.sourcegitcommit: 3dd5cb5e84df8049ecbd484061e6df16a1914bb5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/09/2021
ms.locfileid: "102515347"
---
# <a name="construction-set-for-aks-enterprise-scale-scenario"></a>AKS のエンタープライズ規模のシナリオ用の構築セット

エンタープライズ規模の AKS の構築セットは、Azure Kubernetes Service (AKS) ランディング ゾーンの戦略的設計パスとターゲットの技術状態を表します。 AKS の構築セットは、スケーラブルな Azure Kubernetes Service (AKS) クラスターのランディング ゾーン サブスクリプションを準備するためのアーキテクチャのアプローチと参照の実装を提供します。 構築セットのランディング ゾーンの実装は、クラウド導入フレームワークのアーキテクチャとベスト プラクティスに準拠しています。

お客様は、さまざまな方法で AKS を採用しています。 構築セットは "*お客様*" の方法に合ったアーキテクチャを構築できるように適応させることができるため、組織を持続可能なスケーリングを行うための道へと導くことができます。

## <a name="to-begin-implement-an-enterprise-scale-landing-zone"></a>まず、エンタープライズ規模のランディング ゾーンを実装する

AKS の構築セットでは、エンタープライズ規模のランディング ゾーンが正常に実装されていることを前提としています。 この前提条件の詳細については、次の記事を参照してください。

- [クラウド導入フレームワークのエンタープライズ規模ランディング ゾーンから開始する](../../ready/enterprise-scale/index.md)
- [クラウド導入フレームワークのエンタープライズ規模のランディング ゾーンを Azure に実装する](../../ready/enterprise-scale/implementation.md)

## <a name="what-the-aks-construction-set-provides"></a>AKS の構築セットは以下を提供します

構築セットでランディング ゾーンを構築するアプローチには、プロジェクトをサポートするための以下の資産が含まれています。

- モジュール式のアプローチ。環境変数をカスタマイズできるようになります
- 重要な意思決定の評価に役立つ設計ガイドライン
- ランディング ゾーンのアーキテクチャ
- 以下を含む実装:
  - 独自の AKS デプロイの環境を作成できるデプロイ可能なリファレンス
  - デプロイされた環境をテストするための Microsoft が承認した AKS の参照実装

## <a name="design-guidelines"></a>設計ガイドライン

以下の記事では、ランディング ゾーンを作成するためのガイドラインを提供しています。

- [Azure Kubernetes Service (AKS) のエンタープライズ規模のシナリオ](./eslz-identity-and-access-management.md)
- [AKS のエンタープライズ規模のシナリオにおけるネットワーク トポロジと接続性](./eslz-network-topology-and-connectivity.md)
- [AKS のエンタープライズ規模のシナリオの管理と監視](./eslz-management-and-monitoring.md)
- [AKS のエンタープライズ規模のシナリオにおける事業継続とディザスター リカバリー](./eslz-business-continuity-and-disaster-recovery.md)
- [AKS のエンタープライズ規模のシナリオにおけるセキュリティ、ガバナンス、およびコンプライアンス](./eslz-security-governance-and-compliance.md)
- [AKS のエンタープライズ規模のシナリオにおけるプラットフォームの自動化と DevOps](./eslz-platform-automation-and-devops.md)

## <a name="example-conceptual-reference-architecture"></a>概念参照アーキテクチャの例

次の概念参照アーキテクチャは、設計領域とベスト プラクティスを示す例です。

![責任領域](./media/aks-enterprise-scale-landing-zone.png)

## <a name="obtain-the-aks-construction-set"></a>AKS の構築セットを取得する

AKS の構築セットは、[こちらの GitHub リポジトリ](https://github.com/Azure/caf-terraform-landingzones-starter/tree/starter/enterprise_scale/construction_sets/aks/online/aks_secure_baseline)で入手できる Terraform テンプレートのオープンソース コレクションです。

テンプレートには 2 種類あります。

- 仮想マシン、ネットワーク、ストレージなどのインフラストラクチャ コンポーネントを Azure にデプロイする Terraform モジュール。
- 既に展開されているインフラストラクチャに仮想マシンを構成したり、AKS HANA や必要なアプリケーションをインストールしたりするための異なるロールを実行する Ansible プレイブック。

## <a name="next-steps"></a>次のステップ

- AKS の構築セットの重要な設計領域を確認して、AKS の構築セットのアーキテクチャについての完全な考慮事項と推奨事項を作成します。 詳細については、[Azure Kubernetes Service (AKS) のエンタープライズ規模のシナリオ](./eslz-identity-and-access-management.md)に関するページを参照してください。
