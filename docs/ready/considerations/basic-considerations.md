---
title: Azure ランディング ゾーンに関する考慮事項
description: Azure のクラウド導入フレームワークを使用して、ランディング ゾーンによってクラウド導入環境の基礎構成要素が提供されるしくみについて学びます。
author: BrianBlanchard
ms.author: brblanch
ms.date: 04/09/2020
ms.topic: overview
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.openlocfilehash: 98160a01647907609bfbc1b16d9a4ffe8e23e06c
ms.sourcegitcommit: 7660521b631ea092fb805df9c9d28ad3024287ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2020
ms.locfileid: "83621523"
---
# <a name="landing-zone-considerations"></a>ランディング ゾーンに関する考慮事項

ランディング ゾーンとは、クラウド導入環境の基礎構成要素です。 "_ランディング ゾーン_" という用語は、Azure など、クラウド環境のワークロードをホストする目的でプロビジョニングされ、準備された環境を指します。 完全に機能するランディング ゾーンは、クラウド導入フレームワークにおける準備手順のイテレーションの最終成果物です。

![ランディング ゾーンに関する考慮事項](../../_images/ready/landing-zone-considerations.png)

この画像は、ランディング ゾーン デプロイを実装する際の主な考慮事項を示しています。 考慮事項は、ホスティング、Azure 基礎、ガバナンスという 3 つのカテゴリまたは種類に分割できます。

## <a name="hosting-considerations"></a>ホスティングに関する考慮事項

すべてのランディング ゾーンで、ホスティング オプションに構造が与えられます。 構造はガバナンス コントロールを介して明示的に作成されるか、ランディング ゾーン内でサービスを導入することにより自然に作成されます。 次の記事は、自身のランディング ゾーンを作成するブループリントまたはその他の自動化スクリプトに反映させる意思決定を行う際に役立ちます。

- [コンピューティング関連の意思決定](./compute-options.md): 運用面の複雑さを最小限に抑えるために、コンピューティング関連の選択肢をランディング ゾーンの目的に合わせて調整します。 この意思決定は、Azure Policy イニシアティブやランディング ゾーン ブループリントなど、自動化ツールチェーンを利用して強制できます。
- [ストレージ関連の意思決定](./storage-options.md): ワークロード要件を支援する正しい Azure Storage ソリューションを選択します。
- [ネットワーク関連の意思決定](./networking-options.md): 組織のワークロード、ガバナンス、接続要件を支援するネットワーク関連のサービス、ツール、アーキテクチャを選択します。
- [データベース関連の意思決定](./data-options.md): ワークロード要件に最適なデータベース テクノロジを決定しま

## <a name="azure-fundamentals"></a>Azure 基礎

各ランディング ゾーンは、クラウド環境全体でリソースを整理するための広範なソリューションの一部です。 Azure 基礎は、組織の基本的構成要素です。

- [Azure の基本的な概念](./fundamental-concepts.md): Azure でのリソースの整理のために使用される基本的な概念と用語、およびこれらの概念の相互関連性について説明します。
- [リソースの整合性の意思決定ガイド](../../decision-guides/resource-consistency/index.md): それぞれの基礎を理解できたら、ランディング ゾーンを形成する意思決定に、リソース編成に関連する意思決定ガイドが役立ちます。

## <a name="governance-considerations"></a>ガバナンスに関する考慮事項

クラウド導入フレームワークのガバナンス手順では、全体として環境を制御するためのプロセスを確立します。 しかし、多くのユース ケースでは、ガバナンス関連の意思決定をランディング ゾーン基準で行わなければならない場合があります。 多くのシナリオで、ベースラインが包括的に確立されている場合でも、ガバナンス ベースラインはランディング ゾーン基準で強制されます。 これは、組織でデプロイされる最初のごく少数のランディング ゾーンにも当てはまります。

次の記事は、使用するランディング ゾーンに関するガバナンス上の意思決定を行う際に役立ちます。 各意思決定を自身のガバナンス ベースラインに組み込むことができます。

- **コスト要件。** 組織のクラウド導入に対する動機付けとその環境に関して行われた運用上のコミットメントに基づき、このランディング ゾーンに対して、さまざまなコスト管理構成を場合によっては変更する必要があります。
- **監視関連の意思決定。** ランディング ゾーンの運用要件によっては、さまざまな監視ツールをデプロイできます。 監視関連の意思決定に関する記事は、デプロイするために最適なツールの決定に役立ちます。
- **ロールベースのアクセス制御**。 Azure [ロールベースのアクセス制御 (RBAC)](../considerations/roles.md) では、ユーザー ロールごとに整理されているリソースのグループベース アクセスを詳細に管理できます。
- **ポリシー関連の意思決定**。 [Azure Blueprints サンプル](https://docs.microsoft.com/azure/governance/blueprints/samples)に、事前作成されたコンプライアンス ブループリントが用意されており、各ブループリントには、ポリシー イニシアティブが事前定義されています。 ポリシー関連の意思決定は、自身の要件と制約に基づいて、最良のブループリントまたはポリシー イニシアティブを選択するのに役立ちます。
- **一貫性のあるハイブリッド クラウド[の作成](./hybrid-consistency.md)。** オンプレミス管理のさまざまな利便性を維持しながら、組織にクラウド イノベーションの利点を提供するハイブリッド クラウド ソリューションを作成します。
