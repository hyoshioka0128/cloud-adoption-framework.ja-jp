---
title: 基本アラートを設定する
titleSuffix: Microsoft Cloud Adoption Framework for Azure
description: 基本アラートを設定する
author: BrianBlanchard
ms.author: brblanch
ms.date: 05/10/2019
ms.topic: article
ms.service: cloud-adoption-framework
ms.subservice: operate
ms.openlocfilehash: 5d7469f8613b38ffdaefb41410409fba0c9109fd
ms.sourcegitcommit: bf9be7f2fe4851d83cdf3e083c7c25bd7e144c20
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73565294"
---
# <a name="set-up-basic-alerts"></a>基本アラートを設定する

問題が発生したときに通知を受け取ることは、リソース管理の重要な部分です。 アラートにより、メトリック、ログ、サービスの正常性の問題からのトリガーに基づいて、クリティカルな状態が事前に通知されます。 Azure サーバー管理サービスのオンボードの一環として、IT チームによる問題把握に役立つアラートと通知を設定できます。

## <a name="azure-monitor-alerts"></a>Azure Monitor アラート

Azure Monitor には[アラート](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)機能があり、問題が発生したときにメールやメッセージで通知を受け取ることができます。 これらの機能は、サーバーやその他のリソースによって生成されるログやメトリックなどの共通データ監視プラットフォームに基づいています。 Azure Monitor の共通のツール セットを使用すると、複数のリソースから結合されたデータを分析し、それを使用してアラートをトリガーできます。 こうしたトリガーには次のものが含まれます。

- メトリックの値。
- ログの検索クエリ。
- アクティビティ ログのイベント。
- 基になっている Azure プラットフォームの正常性。
- Web サイトの可用性のテスト。

このサービスによって収集される監視データの提供元の詳しい説明については、[Azure Monitor データ ソースの一覧](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources)に関するページを参照してください。

Azure portal を使用して手動でアラートを作成および管理する方法の詳細については、[Azure Monitor のドキュメント](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric)を参照してください。

## <a name="automated-deployment-of-recommended-alerts"></a>推奨アラートの自動デプロイ

このガイドでは、基本的なインフラストラクチャを監視する 15 種類のアラートを作成することをお勧めします。 デプロイ スクリプトは、[Azure Alert Toolkit の GitHub リポジトリ](https://github.com/Microsoft/manageability-toolkits)にあります。

このパッケージでは、次のアラートが作成されます。

- ディスク領域不足
- 使用可能なメモリが少ない
- CPU 使用率が高い
- 予期しないシャットダウン
- 破損したファイル システム
- 一般的なハードウェア障害

このパッケージでは、例として HP サーバー ハードウェアが使用されています。 関連付けられている構成ファイルの設定を、お使いの OEM ハードウェアを反映するように変更します。 構成ファイルに、パフォーマンス カウンターをさらに追加することもできます。 パッケージをデプロイするには、New-CoreAlerts.ps1 ファイルを実行します。

## <a name="next-steps"></a>次の手順

継続中の運用をサポートする運用とセキュリティのメカニズムについて学習します。

> [!div class="nextstepaction"]
> [継続中の管理とセキュリティ](./ongoing-management-overview.md)
