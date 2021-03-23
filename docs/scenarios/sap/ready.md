---
title: SAP エンタープライズ規模の移行のために環境または Azure ランディング ゾーンを確認する
description: SAP エンタープライズ規模の移行のために Azure 環境または Azure ランディング ゾーンを準備します。
author: JefferyMitchell
ms.author: brblanch
ms.date: 03/01/2021
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.custom: think-tank
ms.openlocfilehash: 5f45966fa3f37920c6cb594fabe68eba31aa62c4
ms.sourcegitcommit: 36e85ac734b184de3f29884b744ea74c81ccc72b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/13/2021
ms.locfileid: "103443173"
---
# <a name="review-your-environment-or-azure-landing-zone-for-an-sap-enterprise-scale-migration"></a>SAP エンタープライズ規模の移行のために環境または Azure ランディング ゾーンを確認する

SAP プラットフォームは、通常、ビジネス機能、機密データ、および業務に不可欠なワークロードをサポートしています。 SAP プラットフォームとワークロードをクラウドにデプロイする前に、ランディング ゾーンは、セキュリティで保護されたミッションクリティカルなアプリケーションに対するガバナンス、セキュリティ、運用の要件を満たしている必要があります。

中心となる運用モデルを採用していて、*小さく初めて拡張していく* アプローチでランディング ゾーンを構築している場合は、運用 SAP プラットフォームを Azure にデプロイする前に、クラウド導入フレームワークの [`Govern`](../../govern/index.md) フェーズと [`Manage`](../../govern/index.md) フェーズにいる必要があります。 導入計画の SAP プラットフォームを利用することで、クラウド環境により多くの操作、コンプライアンス、およびセキュリティの要件が存在する可能性があることが示されます。 ただし、クラウドの取り組みを開始したばかりの場合は、ランディング ゾーンに対するエンタープライズ規模のアプローチから始めることを検討してください。 どちらの方法で開始するかについては、「[Azure のランディング ゾーン](../../ready/landing-zone/index.md)」を参照してください。

## <a name="how-to-evaluate-an-sap-azure-landing-zone"></a>SAP Azure ランディング ゾーンを評価する方法

SAP 製品をホストする Azure ランディング ゾーンは、次の条件をすべて満たしている必要があります。

- これらには、セキュリティ、ガバナンス、および継続的な操作のためのプロセスとツールが定義されています。

- これらは、必要な監視、運用のコンプライアンス、および回復の要件を確保するために、許容される操作管理ベースラインを提供します。

- オンプレミス データセンターと Azure 環境の間で十分なデータ転送を可能にするために、ネットワーク接続が合理化され、SAP プラットフォームとすべてのワークロード資産が計画されています。

- SAP プラットフォームはミッションクリティカルであるため、Azure と既存のデータセンター間のネットワーク接続の冗長性を考慮する必要があります。

- 管理グループ階層は、Azure での SAP 環境の長期的なガバナンスにとって重要です。 個別の制御を運用環境と実稼働環境に簡単に適用できるようにするには、少なくとも 2 つの SAP 管理グループが必要です。

- サブスクリプションの整理は、コア SAP プラットフォームとクラウド内のすべての依存ワークロードの環境セグメンテーションのもう 1 つの重要な側面です。 依存するワークロードから SAP プラットフォームを分離するには、コア プラットフォームを専用サブスクリプションに分離することを検討してください。

クラウド プラットフォーム チームによって提供されるランディング ゾーンが基本的な要件に対応していない場合は、クラウドの導入を開始する前に、チームと協力して環境を定着させてください。

## <a name="how-to-accelerate-an-sap-azure-landing-zone"></a>SAP Azure ランディング ゾーンを高速化する方法

エンタープライズ規模の環境の開発は、複雑で時間がかかることがあります。 SAP [エンタープライズ スケールの構築セット](./enterprise-scale-landing-zone.md) を確認して、SAP 製品の Azure ランディングゾーンのデプロイを加速させ、SAP プラットフォームをサポートできる環境を設計および実装する方法を理解します。

## <a name="next-step-migrate-an-sap-platform-to-azure"></a>次のステップ: SAP プラットフォームを Azure に移行する

次の記事では、Azure での SAP の導入を成功させるために役立つクラウド導入の過程における特定の時点に関するガイダンスを提供します。

- [SAP プラットフォームを Azure に移行する](./migrate.md)
- [SAP によるイノベーション](./innovate.md)
- [SAP の管理](./manage.md)
