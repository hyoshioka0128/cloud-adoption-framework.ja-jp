---
title: ハイブリッドとマルチクラウドに対するエンタープライズ規模のサポート
description: エンタープライズ規模で AKS の導入を促進する方法について説明します
author: BrianBlanchard
ms.author: brblanch
ms.date: 03/01/2021
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.openlocfilehash: 5d3845c27b176d9ca474077e1b7ffa5cc82038e2
ms.sourcegitcommit: b8f8b7631aabaab28e9705934bf67dad15e3a179
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101800906"
---
# <a name="enterprise-scale-support-for-hybrid-and-multicloud"></a>ハイブリッドとマルチクラウドに対するエンタープライズ規模のサポート
  
エンタープライズ規模のランディング ゾーンでは、ミッションクリティカルなテクノロジ プラットフォームと、サポートされているすべてのワークロードに対応するランディング ゾーンを準備するための、特定のアーキテクチャ アプローチ、参照アーキテクチャ、およびリファレンス実装が提供されます。 

エンタープライズ規模の環境は、ハイブリッドとマルチクラウドを念頭に置いて構築されています。 ハイブリッドとマルチクラウドをサポートするには、参照アーキテクチャに対する 3 つの簡単な追加が必要です。

- ハイブリッド: ハイブリッド ネットワーク接続を追加する
- マルチクラウド: マルチクラウド ネットワーク接続を追加する
- 統合運用: Azure Arc を追加してガバナンスと操作のサポートを拡張する 

## <a name="prerequisite"></a>前提条件

この記事では、エンタープライズ規模のランディング ゾーンが正常に実装されていることを前提としています。 この前提条件の詳細については、AKS の構築セットをデプロイする前に、エンタープライズ規模の[概要](https://docs.microsoft.com/azure/cloud-adoption-framework/ready/enterprise-scale/)と[実装ガイダンス](https://docs.microsoft.com/azure/cloud-adoption-framework/ready/enterprise-scale/implementation)を参照してください。

## <a name="design-guidelines"></a>設計ガイドライン

Azure のクラウド導入フレームワークにおけるエンタープライズ規模のランディング ゾーンの設計を推進する重要な意思決定についてガイドします。 エンタープライズ規模のランディング ゾーンまたはその他の Azure ランディング ゾーンの実装を確認および変更するために使用できる、6 つの重要なデザイン領域があります。

- [ID およびアクセス管理](../../ready/enterprise-scale/identity-and-access-management.md)
- [ネットワーク トポロジと接続](../../ready/enterprise-scale/network-topology-and-connectivity.md)
- [管理と監視](../../ready/enterprise-scale/management-and-monitoring.md)
- [事業継続とディザスター リカバリー](../../ready/enterprise-scale/business-continuity-and-disaster-recovery.md)
- [セキュリティ、ガバナンス、およびコンプライアンス](../../ready/enterprise-scale/security-governance-and-compliance.md)
- [プラットフォームの自動化と DevOps](../../ready/enterprise-scale/platform-automation-and-devops.md)

## <a name="implementation-additions"></a>実装の追加

ハイブリッドおよびマルチクラウドのニーズに対応するためにエンタープライズ規模を拡張するには、実装に次の単純な追加を行うことを検討してください。

- ハイブリッド: ネットワーク トポロジと接続のデザイン領域から、関連する設計ガイダンスを適用します。
    - または、VWan またはハブ/スポークの参照実装から開始して、接続性の考慮事項の整理に着手します。 TODO: リンクを追加します
- マルチクラウド: 任意のエンタープライズ規模の実装にマルチクラウド接続を追加します TODO: リンクを追加します
- 統合運用: Azure Arc およびオンボード資産を他のクラウド プラットフォームから追加して、これらの Azure 以外の資産に標準のガバナンスと操作用のツールを適用します。 TODO: 管理するためのリンクを追加します
