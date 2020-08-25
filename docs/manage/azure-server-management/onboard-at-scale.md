---
title: サブスクリプションのサービスを構成する
description: サーバーにサービス エージェントをデプロイし、管理ソリューションを有効にすることで、サブスクリプションの Azure サーバー管理サービスを構成する方法について説明します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 05/10/2019
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: operate
ms.openlocfilehash: 5eeb8bb94ff0de3c1d684bdfd7523657678f9d4b
ms.sourcegitcommit: 011525720bd9e2d9bcf03a76f371c4fc68092c45
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88572070"
---
<!-- cSpell:ignore VMUUID kusto -->

# <a name="configure-azure-server-management-services-at-scale"></a>大規模な Azure サーバー管理サービスを構成する

Azure サーバー管理サービスをサーバーにオンボードするには、次の 2 つのタスクを完了する必要があります。

- サーバーにサービス エージェントをデプロイする。
- 管理ソリューションを有効にする。

この記事では、これらのタスクを完了するために必要な 3 つのプロセスについて説明します。

1. Azure Policy を使用して必須のエージェントを Azure VM にデプロイします。
1. 必須のエージェントをオンプレミスのサーバーにデプロイします。
1. ソリューションを有効にして構成します。

> [!NOTE]
> 仮想マシンを Azure サーバー管理サービスにオンボードする前に、必須の [Log Analytics ワークスペースと Azure Automation アカウント](./prerequisites.md#create-a-workspace-and-automation-account)を作成します。

## <a name="use-azure-policy-to-deploy-extensions-to-azure-vms"></a>Azure Policy を使用して拡張機能を Azure VM にデプロイする

[Azure 管理ツールとサービス](./tools-services.md)に関する記事で説明されているすべての管理ソリューションでは、Log Analytics エージェントが Azure 仮想マシンとオンプレミスのサーバーにインストールする必要があります。 Azure Policy を使用すると Azure VM を大規模にオンボードできます。 エージェントがお使いの Azure VM にインストールされ、適切な Log Analytics ワークスペースに接続されるようにポリシーを割り当てます。

Azure Policy には、Log Analytics エージェントと [Microsoft Dependency Agent](/azure/azure-monitor/insights/vminsights-onboard#the-microsoft-dependency-agent) を含む[組み込みのポリシー イニシアチブ](/azure/governance/policy/concepts/definition-structure#initiatives)があります。これは Azure Monitor for VMs に必要です。

<!-- TODOBACKLOG: Add these when available.
**Preview:** Enable Azure Monitor for virtual machine scale sets.
**Preview:** Enable Azure Monitor for VMs.
 -->

> [!NOTE]
> Azure 監視用のさまざまなエージェントについて詳しくは、「[Azure 監視エージェントの概要](/azure/azure-monitor/platform/agents-overview)」をご覧ください。

### <a name="assign-policies"></a>ポリシーの割り当て

前のセクションで説明したポリシーを割り当てるには:

1. Azure portal で、 **[ポリシー]** 、 **[割り当て]** 、 **[イニシアチブの割り当て]** の順に移動します。

    ![ポータルのポリシー インターフェイスのスクリーン ショット](./media/onboarding-at-scale1.png)

2. **[ポリシーの割り当て]** ページで、省略記号 (…) を選択した後、管理グループまたはサブスクリプションを選択して、 **[スコープ]** を設定します。 任意でリソース グループを選択します。 次に、 **[スコープ]** ページの下部にある **[選択]** を選択します。 スコープによって、ポリシーの割り当て先のリソースまたはリソース グループが決まります。

3. **[ポリシー定義]** の横にある省略記号 **[...]** を選択して、使用可能な定義の一覧を開きます。 イニシアティブ定義をフィルター処理するには、 **[検索]** ボックスに「**Azure Monitor**」と入力します。

    ![ポータルのポリシー インターフェイスのスクリーン ショット](./media/onboarding-at-scale2.png)

4. **[割り当て名]** には選択したポリシー名が自動的に設定されますが、これを変更することはできません。 また、省略可能な説明を追加して、このポリシーの割り当ての詳細情報を提供することもできます。 **[割り当て担当者]** フィールドは、サインイン ユーザーに基づいて自動的に入力されます。 このフィールドは省略可能であり、カスタム値がサポートされています。

5. このポリシーについて、関連付ける Log Analytics エージェントの **[Log Analytics ワークスペース]** を選択します。

    ![ポータルのポリシー インターフェイスのスクリーン ショット](./media/onboarding-at-scale3.png)

6. **[マネージド ID の場所]** チェック ボックスを選択します。 このポリシーの種類が [`DeployIfNotExists`](/azure/governance/policy/concepts/effects#deployifnotexists) である場合、このポリシーをデプロイするにはマネージド ID が必要になります。 ポータルでは、チェック ボックスの選択で指示されたとおりにアカウントが作成されます。

7. **[割り当て]** を選択します。

ウィザードを完了すると、ポリシー割り当てが環境にデプロイされます。 ポリシーが有効になるまで、最大で 30 分かかる可能性があります。 テストするには、30 分後に新しい VM を作成し、その VM 上で Microsoft Monitoring エージェントが既定で有効になっているかどうかを確認します。

## <a name="install-agents-on-on-premises-servers"></a>エージェントをオンプレミスのサーバーにインストールする

> [!NOTE]
> Azure サーバー管理サービスをサーバーにオンボードする前に、必須の [Log Analytics ワークスペースと Azure Automation アカウント](./prerequisites.md#create-a-workspace-and-automation-account)を作成します。

オンプレミスのサーバーの場合、[Log Analytics エージェントと Microsoft Dependency Agent](/azure/azure-monitor/insights/vminsights-enable-hybrid-cloud) を手動でダウンロードしてインストールし、適切なワークスペースに接続するようにそれらを構成する必要があります。 ワークスペース ID とキー情報を指定する必要があります。 この情報を取得するには、Azure portal で Log Analytics ワークスペースに移動し、 **[設定]**  >  **[詳細設定]** を選択します。

![Azure portal 内の Log Analytics ワークスペース詳細設定のスクリーンショット](./media/onboarding-on-premises.png)

## <a name="enable-and-configure-solutions"></a>ソリューションを有効にして構成する

ソリューションを有効にするには、Log Analytics ワークスペースを構成する必要があります。 オンボードされた Azure VM とオンプレミスのサーバーは、接続先の Log Analytics ワークスペースからソリューションを取得します。

### <a name="update-management"></a>更新管理

Update Management、Change Tracking、および Inventory ソリューションには、Log Analytics ワークスペースと Automation アカウントの両方が必要です。 これらのリソースが正しく構成されるように、Automation アカウントを使用してオンボードすることをお勧めします。 詳しくは、「[Update Management、Change Tracking、および Inventory ソリューションの配布準備](/azure/automation/automation-onboard-solutions-from-automation-account)」をご覧ください。

すべてのサーバーに対して Update Management ソリューションを有効にすることをお勧めします。 Azure VM とオンプレミスのサーバーでは Update Management が無料です。 Automation アカウントから Update Management を有効にした場合、ワークスペース内に[スコープ構成](/azure/automation/automation-onboard-solutions-from-automation-account#scope-configuration)が作成されます。 Update Management サービスの対象マシンを含むように、スコープを手動で更新します。

既存のサーバーと今後のサーバーを対象にするには、スコープ構成を削除する必要があります。 これを行うには、Azure portal で Automation アカウントを表示します。 **[Update Management]**  >  **[コンピューターの管理]**  >  **[使用可能なマシンと今後のマシンすべてで有効にします]** を選択します。 この設定によって、ワークスペースに接続されているすべての Azure VM で Update Management を使用できます。

![Azure portal 内の Update Management のスクリーンショット](./media/onboarding-configuration1.png)

### <a name="change-tracking-and-inventory-solutions"></a>Change Tracking と Inventory のソリューション

Change Tracking と Inventory のソリューションをオンボードするには、Update Management の場合と同じ手順に従います。 Automation アカウントからこれらのソリューションをオンボードする方法の詳細については、「[Update Management、Change Tracking、および Inventory ソリューションの配布準備](/azure/automation/automation-onboard-solutions-from-automation-account)」をご覧ください。

Change Tracking ソリューションは、Azure VM では無料で、オンプレミスのサーバーでは 1 か月あたりノードごとに 6 ドルかかります。 このコストには、Change Tracking、Inventory、Desired State Configuration が含まれます。 特定のオンプレミス サーバーのみを登録する場合は、それらのサーバーをオプトインできます。 すべての運用サーバーをオンボードすることをお勧めします。

#### <a name="opt-in-via-the-azure-portal"></a>Azure portal を介したオプトイン

1. Change Tracking と Inventory が有効になっている Automation アカウントに移動します。
2. **[Change Tracking]** を選択します。
3. 右上のウィンドウで **[マシンの管理]** を選択します。
4. **[選択したマシンで有効にします]** を選択します。 次に、マシン名の横にある **[追加]** を選択します。
5. **[有効化]** を選択して、それらのマシンに対してソリューションを有効にします。

![Azure portal 内の Change Tracking のスクリーンショット](./media/onboarding-configuration2.png)

#### <a name="opt-in-by-using-saved-searches"></a>保存された検索を使用したオプトイン

また、スコープ構成を構成してオンプレミスのサーバーをオプトインできます。 スコープ構成では、保存された検索を使用します。

保存された検索を作成または変更するには、次の手順に従います。

1. 前の手順で構成した Automation アカウントにリンクされている Log Analytics ワークスペースに移動します。

1. **[全般]** の下の **[保存された検索]** を選択します。

1. **[フィルター]** ボックスに、「**Change Tracking**」と入力して、保存された検索の一覧をフィルター処理します。 結果内の **[MicrosoftDefaultComputerGroup]** を選択します。

1. Change Tracking にオプトインするコンピューターを含めるためにコンピューター名または VMUUID を入力します。

  ```kusto
  Heartbeat
  | where AzureEnvironment=~"Azure" or Computer in~ ("list of the on-premises server names", "server1")
  | distinct Computer
  ```

  > [!NOTE]
  > このサーバー名は、式に含まれる値と厳密に一致する必要があります。ドメイン名サフィックスは含めないでください。

1. **[保存]** を選択します。 既定では、スコープ構成は保存された検索 **MicrosoftDefaultComputerGroup** にリンクされます。 それは自動的に更新されます。

### <a name="azure-activity-log"></a>[Azure Activity Log (Azure アクティビティ ログ)]

[[Azure アクティビティ ログ]](/azure/azure-monitor/platform/activity-logs-overview) も Azure Monitor の一部です。 ここから、Azure で発生するサブスクリプション レベルのイベントに関する分析情報を得ることができます。

このソリューションを実行するには:

1. Azure portal で **[すべてのサービス]** を開き、次に **[管理 + ガバナンス]**  >  **[ソリューション]** を選択します。
2. **[ソリューション]** ビューで、 **[追加]** を選択します。
3. **[Activity Log Analytics]** を検索して選択します。
4. **［作成］** を選択します

前のセクションで作成した、ソリューションが有効になっているワークスペースの**ワークスペース名**を指定する必要があります。

### <a name="azure-log-analytics-agent-health"></a>Azure Log Analytics の Agent Health

Azure Log Analytics の Agent Health ソリューションでは、Windows および Linux サーバーの正常性、パフォーマンス、可用性に関する報告が行われます。

このソリューションを実行するには:

1. Azure portal で **[すべてのサービス]** を開き、次に **[管理 + ガバナンス]**  >  **[ソリューション]** を選択します。
2. **[ソリューション]** ビューで、 **[追加]** を選択します。
3. **[Azure Log Analytics の Agent Health]** を検索して選択します。
4. **［作成］** を選択します

前のセクションで作成した、ソリューションが有効になっているワークスペースの**ワークスペース名**を指定する必要があります。

作成が完了した後、 **[表示]**  >  **[ソリューション]** を選択すると、ワークスペース リソース インスタンスに "**AgentHealthAssessment**" が表示されます。

### <a name="antimalware-assessment"></a>マルウェア対策評価

Antimalware Assessment ソリューションは、マルウェアに感染しているか感染のリスクが高まっているサーバーの識別に役立ちます。

このソリューションを実行するには:

1. Azure portal で **[すべてのサービス]** を開き、次に **[管理 + ガバナンス]**  >  **[ソリューション]** を選択します。
2. **[ソリューション]** ビューで、 **[追加]** を選択します。
3. **[Antimalware Assessment]** を検索して選択します。
4. **［作成］** を選択します

前のセクションで作成した、ソリューションが有効になっているワークスペースの**ワークスペース名**を指定する必要があります。

作成が完了した後、 **[表示]**  >  **[ソリューション]** を選択すると、ワークスペース リソース インスタンスに "**AntiMalware**" が表示されます。

### <a name="azure-monitor-for-vms"></a>VM に対する Azure Monitor

「[評価のために単一の VM 上の管理サービスを有効にする](./onboard-single-vm.md)」で説明されているように、VM インスタンスのビュー ページを通して [Azure Monitor for VMs](/azure/azure-monitor/insights/vminsights-overview) を有効にすることができます。 この記事で説明されている他のソリューションに対して行ったように **[ソリューション]** ページからソリューションを直接有効にすることはしないでください。 大規模なデプロイの場合、[自動化](./onboarding-automation.md)を使用してワークスペース内で適切なソリューションを有効にする方が簡単な場合があります。

### <a name="azure-security-center"></a>Azure Security Center

すべてのサーバーを少なくとも Azure Security Center の Free レベルにオンボードすることをお勧めします。 このオプションでは、基本的なセキュリティ評価と、環境に関する実行可能なセキュリティの推奨事項が提供されます。 Standard レベルにはさらに多くの利点があります。 詳細については、[Azure Security Center の価格](/azure/security-center/security-center-pricing)に関する記事をご覧ください。

Azure Security Center の Free レベルを有効にするには、次の手順に従います。

1. **Security Center** ポータル ページに移動します。
2. **[ポリシーとコンプライアンス]** の下の **[セキュリティ ポリシー]** を選択します。
3. 右端のウィンドウで、作成した Log Analytics ワークスペース リソースを見つけます。
4. そのワークスペースに対する **[設定の編集]** を選択します。
5. **[価格レベル]** を選択します。
6. **[Free]** オプションを選択します。
7. **[保存]** を選択します。

## <a name="next-steps"></a>次のステップ

サーバーのオンボードとアラートの作成にオートメーションを使用する方法を確認します。

> [!div class="nextstepaction"]
> [オンボードとアラート構成の自動化](./onboarding-automation.md)
