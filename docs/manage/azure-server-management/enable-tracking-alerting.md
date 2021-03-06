---
title: 重要な変更の追跡とアラート
description: Azure Change Tracking と Inventory を使用して、ハイブリッド環境における重要な変更の追跡とアラートを有効にします。
author: BrianBlanchard
ms.author: brblanch
ms.date: 05/10/2019
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: operate
ms.openlocfilehash: 1fa20d37c5cc7813220ff5862743f3179f4aefcd
ms.sourcegitcommit: bd9872320b71245d4e9a359823be685e0f4047c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/26/2020
ms.locfileid: "83861516"
---
<!-- cSpell:ignore HKEY kusto -->

# <a name="enable-tracking-and-alerting-for-critical-changes"></a>重要な変更の追跡とアラートを有効にする

Azure Change Tracking と Inventory では、ハイブリッド環境の構成の状態およびその環境に対する変更に関するアラートが表示されます。 デプロイしたサーバーに影響する可能性がある重要なファイル、サービス、ソフトウェア、レジストリの変更を報告できます。

既定では、Azure Automation インベントリ サービスはファイルやレジストリの設定は監視しません。 このソリューションでは、監視対象として推奨されるレジストリ キーの一覧が提供されます。 この一覧を表示するには、Azure portal でお使いの Automation アカウントに移動し、次に **[インベントリ]**  >  **[設定の編集]** を選択します。

![Azure portal の Azure Automation [インベントリ] ビューのスクリーンショット](./media/change-tracking1.png)

各レジストリ キーの詳細については、「[レジストリ キーの変更追跡](https://docs.microsoft.com/azure/automation/automation-change-tracking#registry-key-change-tracking)」を参照してください。 評価するキーを選択し、それを有効にします。 この設定は、現在のワークスペースで有効になっているすべての VM に適用されます。

サービスを使用して、重要なファイルの変更を追跡することもできます。 たとえば、C:\windows\system32\drivers\etc\hosts ファイルを追跡できます。OS がホスト名を IP アドレスにマップするために使用されるためです。 このファイルを変更すると、接続の問題が発生したり、トラフィックが危険な Web サイトにリダイレクトされたりする可能性があります。

hosts ファイルに対してファイル コンテンツの追跡を有効にするには、「[ファイル コンテンツの追跡の有効化](https://docs.microsoft.com/azure/automation/change-tracking-file-contents#enable-file-content-tracking)」の手順に従ってください。

追跡中のファイルに加えられた変更に対するアラートを追加することもできます。 たとえば、hosts ファイルに加えられた変更に対してアラートを設定するとします。 コマンド バーまたはリンクされた Log Analytics ワークスペースのログ検索で **[Log Analytics]** を選択します。 Log Analytics で、次のクエリを使用して、hosts ファイルに対する変更を検索します。

  ```kusto
  ConfigurationChange | where FieldsChanged contains "FileContentChecksum" and FileSystemPath contains "hosts"
  ```

![Azure portal の Log Analytics クエリ エディターのスクリーンショット](./media/change-tracking2.png)

このクエリでは、パスに "hosts" という単語が含まれているファイルの内容に対する変更を検索します。 パス パラメーターを変更して、特定のファイルを検索することもできます (例: `FileSystemPath ==  "c:\\windows\\system32\\drivers\\etc\\hosts"`)。
  
クエリから結果が返されたら、 **[新しいアラート ルール]** を選択してアラート ルール エディターを開きます。 このエディターは、Azure portal の Azure Monitor から開くこともできます。

アラート ルール エディターで、クエリを確認し、必要に応じてアラートのロジックを変更します。 この場合、環境に含まれるいずれかのマシンで変更が検出された場合にアラートが生成されるようにします。

![Azure portal の Log Analytics アラート ルール エディターのスクリーンショット](./media/change-tracking3.png)

条件ロジックを設定した後に、そのアラートに対応するアクションを実行するアクション グループを割り当てることができます。 この例では、アラートが生成されると、メールが送信され、ITSM チケットが作成されます。 ほかにも多数の有用なアクションを実行できます (Azure 関数、Azure Automation Runbook、Webhook、ロジック アプリのトリガーなど)。

![Azure portal でのアラート ルールの概要のサンプルを示すスクリーンショット](./media/change-tracking4.png)

すべてのパラメーターとロジックを設定したら、そのアラートを環境に適用します。

## <a name="tracking-and-alerting-examples"></a>追跡とアラートの例

このセクションでは、使用する場合がある追跡とアラートのその他の一般的なシナリオを次に示します。

### <a name="driver-file-changed"></a>ドライバー ファイルの変更

次のクエリを使用して、ドライバー ファイルの変更、追加、削除が行われたかどうかを検出します。 それは、重要なシステム ファイルに対する変更を追跡するのに役立ちます。

  ```kusto
  ConfigurationChange | where ConfigChangeType == "Files" and FileSystemPath contains " c:\\windows\\system32\\drivers\\"
  ```

### <a name="specific-service-stopped"></a>特定のサービスの停止

次のクエリを使用して、システムにとって重要なサービスに対する変更を追跡します。

  ```kusto
  ConfigurationChange | where SvcState == "Stopped" and SvcName contains "w3svc"
  ```

### <a name="new-software-installed"></a>新しいソフトウェアのインストール

ソフトウェア構成をロックダウンする必要がある環境では、次のクエリを使用します。

  ```kusto
  ConfigurationChange | where ConfigChangeType == "Software" and ChangeCategory == "Added"
  ```

### <a name="specific-software-version-is-or-isnt-installed-on-a-machine"></a>特定のソフトウェア バージョンがマシンにインストールされているかどうか

次のクエリを使用して、セキュリティを評価します。 このクエリで参照している `ConfigurationData` にはインベントリのログが含まれており、変更ではなく、最後に報告された構成の状態が提供されます。

  ```kusto
  ConfigurationData | where SoftwareName contains "Monitoring Agent" and CurrentVersion != "8.0.11081.0"
  ```

### <a name="known-dll-changed-through-the-registry"></a>レジストリを通じた既知の DLL の変更

既知のレジストリ キーの変更を検出するには、次のクエリを使用します。

  ```kusto
  ConfigurationChange | where RegistryKey == "HKEY_LOCAL_MACHINE\\System\\CurrentControlSet\\Control\\Session Manager\\KnownDlls"
  ```

## <a name="next-steps"></a>次のステップ

Azure Automation で[更新スケジュールを作成](./update-schedules.md)して、サーバーの更新を管理する方法を確認します。

> [!div class="nextstepaction"]
> [更新スケジュールの作成](./update-schedules.md)
