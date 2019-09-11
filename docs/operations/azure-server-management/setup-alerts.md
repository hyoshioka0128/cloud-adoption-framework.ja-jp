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
ms.openlocfilehash: 5b11a97e78d5fcd1b2a2cc866f5a7062bc6a2977
ms.sourcegitcommit: a26c27ed72ac89198231ec4b11917a20d03bd222
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/06/2019
ms.locfileid: "70839421"
---
# <a name="set-up-basic-alerts"></a>基本アラートを設定する

問題が発生したときに通知を受け取ることは、リソース管理の重要な部分です。 アラートでは、重大な状態が事前に通知されます。 メトリック、ログ、またはサービスの正常性の問題からのトリガーをそのベースにすることができます。 Azure サーバー管理サービスのオンボードの一環として、IT チームによる問題把握に役立つアラートと通知を設定できます。

## <a name="azure-monitor-alerts"></a>Azure Monitor アラート

Azure Monitor には[アラート](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)機能があり、問題が発生したときにメールやメッセージで通知を受け取ることができます。 これらの機能は、サーバーやその他のリソースによって生成されるログやメトリックなどの共通データ監視プラットフォームに基づいています。 このプラットフォームでは、Azure Monitor にある共通のツール一式を使用して、複数のリソースからのデータをまとめて分析し、アラートのトリガーに使用することができます。 こうしたトリガーには次のものが含まれます。

- メトリックの値。
- ログの検索クエリ。
- アクティビティ ログのイベント。
- 基になっている Azure プラットフォームの正常性。
- Web サイトの可用性のテスト。

このサービスによって収集される監視データの提供元の詳しい説明については、[Azure Monitor データ ソースの一覧](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources)に関するページを参照してください。

ポータルを使用して手動でアラートを作成および管理する方法の詳細については、[Azure Monitor のドキュメント](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric)を参照してください。

## <a name="automated-deployment-of-recommended-alerts"></a>推奨アラートの自動デプロイ

このガイドでは、基本的なインフラストラクチャを監視する 15 種類のアラートを有効にすることをお勧めします。 デプロイ スクリプトは、[Azure Alert Toolkit の GitHub リポジトリ](https://github.com/Microsoft/manageability-toolkits)にあります。

このパッケージにより、ディスク領域不足、使用可能なメモリの不足、高 CPU 使用率、予期しないシャットダウン、破損したファイル システム、および一般的なハードウェア障害についてアラートが作成されます。 このパッケージでは、例として HP サーバー ハードウェアが使用されています。 関連付けられている構成ファイルの設定を、お使いの OEM ハードウェアを反映するように変更する必要があります。 構成ファイルに、パフォーマンス カウンターをさらに追加することもできます。 パッケージをデプロイするには、New-CoreAlerts.ps1 ファイルを実行します。

## <a name="next-steps"></a>次の手順

継続中の運用をサポートする運用とセキュリティのメカニズムについて学習します。

> [!div class="nextstepaction"]
> [継続中の管理とセキュリティ](./ongoing-management-overview.md)
