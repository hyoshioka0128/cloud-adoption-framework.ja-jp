---
title: チーム間での責任の調整
titleSuffix: Microsoft Cloud Adoption Framework for Azure
description: チーム間での責任の調整について説明します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 09/10/2019
ms.topic: article
ms.service: cloud-adoption-framework
ms.subservice: organize
ms.custom: organize
ms.openlocfilehash: 40ccd0c17a55a87c84d40abd749bf8e61f891e6c
ms.sourcegitcommit: 35c162d2d09ec1c4a57d3d57a5db1d56ee883806
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/17/2019
ms.locfileid: "72549082"
---
# <a name="align-responsibilities-across-teams"></a>チーム間で責任を調整する

"*実行責任、説明責任、助言、通知*" (RACI) の各パーティを明確にするクロスチーム マトリックスを作成することによりチーム間での責任を調整する方法について説明します。 この記事では、「[チーム構造の確立](./organization-structures.md)」で説明されている組織構造の RACI マトリックスの例を示します。

- [クラウド導入チームのみ](#cloud-adoption-team-only)
- [MVP のベスト プラクティス](#best-practice-minimum-viable-product-mvp)
- [中央 IT](#central-it)
- [戦略的連携](#strategic-alignment)
- [運用での連携](#operational-alignment)
- [クラウドのセンター オブ エクセレンス (CCoE)](#cloud-center-of-excellence-ccoe)

組織構造の決定を経時的に追跡するために、[RACI スプレッドシート テンプレート](https://archcenter.blob.core.windows.net/cdn/fusion/management/raci-template.xlsx)をダウンロードして変更します。

この記事の例では、RACI の次の構成要素について記載しています。

- 1 つの機能に対して "*説明責任*" を負う 1 つのチーム。
- 結果に対して "*責任*" を負う任意のチーム。
- 計画中に "*助言*" を求められる必要のあるチーム。
- 作業が完了したときに "*通知*" を受ける必要のあるチーム。

最初の表を除く各表の最後の行には、最も合致するクラウド機能へのリンクが追加情報として含まれています。

## <a name="cloud-adoption-team-only"></a>クラウド導入チームのみ

|  |ソリューションの配信  |ビジネスの整合  |変更管理  |ソリューションの運用  |ガバナンス |プラットフォームの成熟度  |プラットフォームの運用  |プラットフォームの自動化  |
|---------|---------|---------|---------|---------|---------|---------|---------|---------|
|クラウド導入チーム |説明責任|説明責任|説明責任|説明責任|説明責任|説明責任|説明責任|説明責任|

## <a name="best-practice-minimum-viable-product-mvp"></a>ベスト プラクティス: 実用最小限の製品 (MVP)

|  |ソリューションの配信  |ビジネスの整合  |変更管理  |ソリューションの運用  |ガバナンス |プラットフォームの成熟度  |プラットフォームの運用  |プラットフォームの自動化  |
|---------|---------|---------|---------|---------|---------|---------|---------|---------|
|クラウド導入チーム|説明責任|説明責任|説明責任|説明責任|助言|助言|助言|通知|
|クラウド ガバナンス チーム|助言|通知|通知|通知|説明責任|説明責任|説明責任|説明責任|
||||||||||
|合致するクラウド機能|[クラウド導入](./cloud-adoption.md)|[クラウド戦略](./cloud-strategy.md)|[クラウド戦略](./cloud-strategy.md)|[クラウド運用](./cloud-operations.md)|[CCoE](./cloud-center-of-excellence.md)-[クラウド ガバナンス](./cloud-governance.md)|[CCoE](./cloud-center-of-excellence.md)-[クラウド プラットフォーム](./cloud-platform.md)|[CCoE](./cloud-center-of-excellence.md)-[クラウド プラットフォーム](./cloud-platform.md)|[CCoE](./cloud-center-of-excellence.md)-[クラウド自動化](./cloud-automation.md)|

## <a name="central-it"></a>中央 IT

| |ソリューションの配信  |ビジネスの整合  |変更管理  |ソリューションの運用  |ガバナンス |プラットフォームの成熟度  |プラットフォームの運用  |プラットフォームの自動化  |
|---------|---------|---------|---------|---------|---------|---------|---------|---------|
|クラウド導入チーム  |説明責任|説明責任|責任    |責任|通知   |通知   |通知   |通知   |
|クラウド ガバナンス チーム|助言  |通知   |通知   |通知   |説明責任|助言  |責任|通知   |
|中央 IT           |助言  |通知   |説明責任   |説明責任   |責任  |説明責任|説明責任|説明責任|
||||||||||
|合致するクラウド機能|[クラウド導入](./cloud-adoption.md)|[クラウド戦略](./cloud-strategy.md)|[クラウド戦略](./cloud-strategy.md)|[クラウド運用](./cloud-operations.md)|[クラウド ガバナンス](./cloud-governance.md)|[中央 IT](./central-it.md)|[中央 IT](./central-it.md)|[中央 IT](./central-it.md)|

## <a name="strategic-alignment"></a>戦略的連携

|  |ソリューションの配信  |ビジネスの整合  |変更管理  |ソリューションの運用  |ガバナンス |プラットフォームの成熟度  |プラットフォームの運用  |プラットフォームの自動化  |
|---------|---------|---------|---------|---------|---------|---------|---------|---------|
|クラウド戦略チーム  |助言  |説明責任|説明責任|助言  |助言  |通知   |通知   |通知   |
|クラウド導入チーム  |説明責任|助言  |責任|説明責任|通知   |通知   |通知   |通知   |
|CCoE モデル RACI      |助言  |通知   |通知   |通知   |説明責任|説明責任|説明責任|説明責任|
||||||||||
|合致するクラウド機能|[クラウド導入](./cloud-adoption.md)|[クラウド戦略](./cloud-strategy.md)|[クラウド戦略](./cloud-strategy.md)|[クラウド運用](./cloud-operations.md)|[CCoE](./cloud-center-of-excellence.md)-[クラウド ガバナンス](./cloud-governance.md)|[CCoE](./cloud-center-of-excellence.md)-[クラウド プラットフォーム](./cloud-platform.md)|[CCoE](./cloud-center-of-excellence.md)-[クラウド プラットフォーム](./cloud-platform.md)|[CCoE](./cloud-center-of-excellence.md)-[クラウド自動化](./cloud-automation.md)|

## <a name="operational-alignment"></a>運用での連携

|  |ソリューションの配信  |ビジネスの整合  |変更管理  |ソリューションの運用  |ガバナンス |プラットフォームの成熟度  |プラットフォームの運用  |プラットフォームの自動化  |
|---------|---------|---------|---------|---------|---------|---------|---------|---------|
|クラウド戦略チーム  |助言  |説明責任|説明責任|助言  |助言  |通知   |通知   |通知   |
|クラウド導入チーム  |説明責任|助言  |責任|助言  |通知   |通知   |通知   |通知   |
|クラウド運用チーム|助言  |助言  |責任|説明責任|助言  |通知   |説明責任|助言  |
|CCoE モデル RACI      |助言  |通知   |通知   |通知   |説明責任|説明責任|責任|説明責任|
||||||||||
|合致するクラウド機能|[クラウド導入](./cloud-adoption.md)|[クラウド戦略](./cloud-strategy.md)|[クラウド戦略](./cloud-strategy.md)|[クラウド運用](./cloud-operations.md)|[CCoE](./cloud-center-of-excellence.md)-[クラウド ガバナンス](./cloud-governance.md)|[CCoE](./cloud-center-of-excellence.md)-[クラウド プラットフォーム](./cloud-platform.md)|[CCoE](./cloud-center-of-excellence.md)-[クラウド プラットフォーム](./cloud-platform.md)|[CCoE](./cloud-center-of-excellence.md)-[クラウド自動化](./cloud-automation.md)|

## <a name="cloud-center-of-excellence-ccoe"></a>クラウドのセンター オブ エクセレンス (CCoE)

|  |ソリューションの配信  |ビジネスの整合  |変更管理  |ソリューションの運用  |ガバナンス |プラットフォームの成熟度  |プラットフォームの運用  |プラットフォームの自動化  |
|---------|---------|---------|---------|---------|---------|---------|---------|---------|
|クラウド戦略チーム  |助言  |説明責任|説明責任|助言  |助言  |通知   |通知   |通知   |
|クラウド導入チーム  |説明責任|助言  |責任|助言  |通知   |通知   |通知   |通知   |
|クラウド運用チーム|助言  |助言  |責任|説明責任|助言  |通知   |説明責任|助言  |
|クラウド ガバナンス チーム|助言  |通知   |通知   |助言  |説明責任|助言  |責任|通知   |
|クラウド プラットフォーム チーム  |助言  |通知   |通知   |助言  |助言  |説明責任|責任|責任|
|クラウド自動化チーム|助言  |通知   |通知   |通知   |助言  |責任|責任|説明責任|
||||||||||
|合致するクラウド機能|[クラウド導入](./cloud-adoption.md)|[クラウド戦略](./cloud-strategy.md)|[クラウド戦略](./cloud-strategy.md)|[クラウド運用](./cloud-operations.md)|[CCoE](./cloud-center-of-excellence.md)-[クラウド ガバナンス](./cloud-governance.md)|[CCoE](./cloud-center-of-excellence.md)-[クラウド プラットフォーム](./cloud-platform.md)|[CCoE](./cloud-center-of-excellence.md)-[クラウド プラットフォーム](./cloud-platform.md)|[CCoE](./cloud-center-of-excellence.md)-[クラウド自動化](./cloud-automation.md)|

## <a name="next-steps"></a>次の手順

組織構造に関する決定を時間経過に沿って追跡するため、[RACI スプレッドシート テンプレート](https://archcenter.blob.core.windows.net/cdn/fusion/management/raci-template.xlsx)をダウンロードして変更します。 この記事の RACI マトリックスから、最もよく合致するサンプルをコピーして修正します。

> [!div class="nextstepaction"]
> [RACI スプレッドシート テンプレートをダウンロードする](https://archcenter.blob.core.windows.net/cdn/fusion/management/raci-template.xlsx)
