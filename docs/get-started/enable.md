---
title: 健全な運用モデルと組織配置でお客様の成功を実現する
description: 無料のセルフサービス クラウド導入フレームワークとその他のツールを使用して、お客様の成功を実現するクラウド導入の意思決定に役立てることができます。
author: BrianBlanchard
ms.author: brblanch
ms.date: 05/15/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: overview
ms.openlocfilehash: ac6df9dafb43af14f41d94c8dde4416aceccf231
ms.sourcegitcommit: 9163a60a28ffce78ceb5dc8dc4fa1b83d7f56e6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2020
ms.locfileid: "86450545"
---
# <a name="enable-customer-success-with-a-sound-operating-model-and-organizational-alignment"></a>健全な運用モデルと組織配置でお客様の成功を実現する

多くの場合、クラウドの導入作業におけるお客様の成功は、技術的スキルや導入関連のプロジェクトにはほとんど関係ありません。 運用モデルによって、導入を可能にする機会や、場合によってはクラウドの導入が遅くなる可能性のある問題点が生じます。

## <a name="alignment"></a>Alignment

特にイノベーションを推進する場合、ビジネス チームと技術チームの連携はソリューションの成功にとって最も重要です。

- Microsoft では、ビジネスの利害関係者向けに、ビジネス戦略の開発をサポートし、ベスト プラクティスの例を提供する [Microsoft AI ビジネス スクール](https://www.microsoft.com/ai/ai-business-school)を作成しました。
- 技術の利害関係者向けには、新しい AI スキルを身に付けることができる [Microsoft AI ラーニング パス](https://docs.microsoft.com/learn/)が用意されています。

## <a name="blockers"></a>問題点

クラウドの導入が遅くなったり停滞したりする場合は、継続的な成功を実現するために運用モデルを評価することをお勧めします。 ワークロード間またはプロジェクト間で成功に一貫性がない場合は、運用モデルが不適切である可能性があります。 ポリシーの問題点、レガシ プロセス、または不適切な人員によって複数のプロジェクトが停滞している場合も、成功を妨げているのは運用モデルである可能性があります。

## <a name="opportunities"></a>機会

一般的な問題点を超えて、運用モデルの段階的な強化により、ポートフォリオ全体でスケーリングできる主な機会がいくつかあります。 一般的に顧客は、特に運用上の優位性、コストの最適化、セキュリティ、信頼性、パフォーマンス、または人員の管理をスケーリングする必要があります。 これらの内容をポートフォリオ レベルでスケーリングすると、ワークロード中心の特定のチームのベスト プラクティスを、他のすべてのプロジェクトやワークロードに適用できます。

## <a name="get-start-guides-to-enable-teams-through-an-operating-model"></a>運用モデルを使用してチームを有効にするためのファースト ステップ ガイド

次のガイドは、運用モデルの調整を開始し、時間の経過と共に改善を続ける際に役立ちます。

|                                                                                     |                                                                                                                                |
|-------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------|
| [クラウド変換中に優れた機能を提供するにはどうすればよいですか。](./operational-excellence.md)                   | このガイドの手順を使用すると、戦略チームは、優れた運用を確実に継続するために必要な組織の変更管理を実現できます。 |
| [エンタープライズ コストを管理するにはどうすればよいですか。](./manage-costs.md)                                          | エンタープライズ コストの最適化を開始し、環境全体のコストを管理します。                                                                           |
| [エンタープライズ クラウド環境を常にセキュリティで保護するにはどうすればよいですか。](./security.md)             | このファースト ステップ ガイドは、侵害のリスクを最小限に抑え、侵害が発生した場合の回復を促進するために、企業全体に適切なセキュリティ要件が適用されていることを確認するのに役立ちます。                                       |
| [信頼性を向上させるために適切なコントロールを適用するにはどうすればよいですか。](./reliability.md)                   | このファースト ステップ ガイドに従うと、構成、リソース組織、セキュリティ ベースライン、またはリソース保護ポリシーの不整合に関連する中断を最小限に抑えることができます。 |
| [企業全体のパフォーマンスを確保するにはどうすればよいですか。](./performance.md)                               | このファースト ステップ ガイドは、企業全体のパフォーマンスを維持するためのプロセスを確立するのに役立ちます。                               |
| [どのようにして組織を調整しますか。](./org-alignment.md)                               | このファースト ステップ ガイドは、適切に編成された組織の構造を確立するのに役立ちます。                               |

## <a name="shared-architecture-principles"></a>共有アーキテクチャの原則

適切に管理された運用モデルの核となる原則は、一連の一般的なアーキテクチャの原則に基づいています。 この記事シリーズのファースト ステップ ガイドは、これらの原則をクラウド プラットフォームにわたって、ワークロードのポートフォリオ全体でスケーリングするうえで、チームをサポートするのに役立ちます。

![共有アーキテクチャの原則](../_images/shared-principles.png)

これらの原則は、[Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-overview)、[Microsoft Azure Well-Architected Framework](https://docs.microsoft.com/azure/architecture/framework)、および [Azure アーキテクチャ センター](https://docs.microsoft.com/azure/architecture)のソリューションで共有されます。

- [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-overview) では、ソリューション、ワークロード、および完全なポートフォリオにわたる個々の資産の原則が評価されます。
- [Azure アーキテクチャ センター](https://docs.microsoft.com/azure/architecture)では、特定の技術的なソリューションを開発および管理するために、これらの原則が適用されます。
- [Microsoft Azure Well-Architected Framework](https://docs.microsoft.com/azure/architecture/framework) では、アーキテクチャの決定を導くために、ワークロード全体でこれらの原則のバランスをとることができます。
- [クラウド導入フレームワーク](../index.yml)では、確実にポートフォリオ全体で原則がスケーリングされ、適切に管理された環境にわたって導入チームが有効になります。
