---
title: ランディング ゾーンのセキュリティの向上
description: ランディング ゾーンのセキュリティを向上させます。
author: BrianBlanchard
ms.author: brblanch
ms.date: 05/15/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.custom: internal
ms.openlocfilehash: e79921cfe0752b77bf349d9976fdb1c3692d2ae9
ms.sourcegitcommit: b6f2b4f8db6c3b1157299ece1f044cff56895919
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2020
ms.locfileid: "97023703"
---
# <a name="improve-landing-zone-security"></a>ランディング ゾーンのセキュリティの向上

ワークロードまたはそれらがホストされているランディング ゾーンで、機密データや重要なシステムにアクセスする必要がある場合は、データと資産を保護することが重要です。 ランディング ゾーンのセキュリティの向上は、ランディング ゾーンを展開またはリファクタリングして高度なセキュリティ要件に対応させることによる、[ランディング ゾーンに対するテスト駆動開発アプローチ](./test-driven-development.md)を基礎とします。

## <a name="landing-zone-security-best-practices"></a>ランディング ゾーンのセキュリティに関するベスト プラクティス

次のリファレンス アーキテクチャとベスト プラクティスの一覧は、ランディング ゾーンのセキュリティを向上させる方法の例を示しています。

- [Azure Security Center](/azure/security-center/security-center-get-started?toc=/azure/cloud-adoption-framework/toc.json&bc=/azure/cloud-adoption-framework/_bread/toc.json):Security Center にサブスクリプションをオンボードします。
- [Azure Sentinel](/azure/sentinel/quickstart-onboard?toc=/azure/cloud-adoption-framework/toc.json&bc=/azure/cloud-adoption-framework/_bread/toc.json):Azure Sentinel をオンボードすると、**セキュリティ情報イベント管理 (SIEM)** および **セキュリティ オーケストレーション自動応答 (SOAR)** ソリューションが提供されます。
- [ネットワーク境界のセキュリティ](../../reference/networking-vdc.md):ネットワーク境界をデータセンターで保護する方法に似た、ネットワークを開発するためのいくつかの参照パターン。
- [セキュリティ保護されたネットワーク アーキテクチャ](/azure/architecture/reference-architectures/dmz/secure-vnet-dmz?toc=/azure/cloud-adoption-framework/toc.json&bc=/azure/cloud-adoption-framework/_bread/toc.json):境界ネットワークとセキュリティで保護されたネットワーク アーキテクチャを実装するための参照アーキテクチャ。
- [ID 管理とアクセス制御](/azure/security/fundamentals/identity-management-best-practices?toc=/azure/cloud-adoption-framework/toc.json&bc=/azure/cloud-adoption-framework/_bread/toc.json):ID とアクセスを実装して Azure のランディング ゾーンをセキュリティで保護するための一連のベスト プラクティス。
- [ネットワーク セキュリティのプラクティス](/azure/security/fundamentals/network-best-practices?toc=/azure/cloud-adoption-framework/toc.json&bc=/azure/cloud-adoption-framework/_bread/toc.json):ネットワークをセキュリティで保護するための追加のベスト プラクティスについて説明しています。
- [運用可能なセキュリティ](/azure/security/fundamentals/operational-best-practices?toc=/azure/cloud-adoption-framework/toc.json&bc=/azure/cloud-adoption-framework/_bread/toc.json)では、Azure で運用可能なセキュリティを強化するためのベスト プラクティスについて説明しています。
- [セキュリティ ベースライン規範](../../govern/guides/complex/security-baseline-improvement.md#incremental-improvement-of-best-practices):セキュリティ要件を適用するためのガバナンス主導のセキュリティ ベースラインを作成する例。

## <a name="test-driven-development-cycle"></a>テスト駆動開発サイクル

セキュリティの強化を始める前に、"完了の定義" とすべての "受け入れ基準" を理解しておくことが重要です。 詳細については、[ランディング ゾーンのテスト駆動開発](./test-driven-development.md)および [Azure でのテスト駆動開発](./azure-test-driven-development.md)に関する記事をご覧ください。

![クラウド ランディング ゾーンのテスト駆動開発プロセス](../../_images/ready/test-driven-development-process.png)

## <a name="next-steps"></a>次のステップ

重要なアプリケーションをサポートするために[ランディング ゾーンの運用の改善](./landing-zone-operations.md)方法について説明します。

> [!div class="nextstepaction"]
> [ランディング ゾーンの運用の改善](./landing-zone-operations.md)
