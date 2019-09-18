---
title: サブスクリプション用の Azure 管理サービスを構成する
titleSuffix: Microsoft Cloud Adoption Framework for Azure
description: サブスクリプション用の Azure 管理サービスを構成する
author: BrianBlanchard
ms.author: brblanch
ms.date: 05/10/2019
ms.topic: article
ms.service: cloud-adoption-framework
ms.subservice: operate
ms.openlocfilehash: 56a989e975625c9d8f0f3db80dab9043dca3a479
ms.sourcegitcommit: 443c28f3afeedfbfe8b9980875a54afdbebd83a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/16/2019
ms.locfileid: "71032422"
---
# <a name="configure-azure-management-services-at-scale"></a>大規模な Azure 管理サービスを構成する

サーバーへの Azure 管理サービスのオンボードには、2 つのタスクが関係します。サーバーへのサービス エージェントのデプロイと、管理ソリューションの有効化です。 この記事では、これらのタスクを完了できるようにする以下のプロセスについて説明します。

- [Azure Policy を使用して必須のエージェントを Azure VM にデプロイする](#deploy-extensions-to-azure-vms-using-azure-policy)
- [必須のエージェントをオンプレミスのサーバーに展開する](#install-required-agents-on-on-premises-servers)
- [ソリューションを有効にして構成する](#enable-and-configure-solutions)

> [!NOTE]
> 仮想マシンを Azure 管理サービスにオンボードする前に、必須の [Log Analytics ワークスペースと Azure Automation アカウント](./prerequisites.md#create-a-workspace-and-automation-account)を作成します。

## <a name="deploy-extensions-to-azure-vms-using-azure-policy"></a>Azure Policy を使用して拡張機能を Azure VM にデプロイする

[Azure 管理ツールとサービス](./tools-services.md)に関する記事で説明されているすべての管理ソリューションでは、Log Analytics エージェントを Azure 仮想マシン (VM) とオンプレミスのサーバーにインストールする必要があります。 Azure Policy を使用すると Azure VM を大規模にオンボードできます。 エージェントがすべての Azure VM にインストールされ、適切な Log Analytics ワークスペースに接続されるようにポリシーを割り当ててください。

Azure Policy には、Log Analytics エージェントと [Microsoft Dependency Agent](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-onboard#the-microsoft-dependency-agent) の両方を含む[ポリシーのイニシアティブ](https://docs.microsoft.com/azure/governance/policy/index.md#initiative-definition)が組み込まれており、これは Azure Monitor for VMs に必要です。

<!-- TODO: Add these when available.
- [Preview]: Enable Azure Monitor for virtual machine scale sets.
- [Preview]: Enable Azure Monitor for VMs.
 -->

> [!NOTE]
> Azure 監視用のさまざまなエージェントについて詳しくは、「[Azure 監視エージェントの概要](https://docs.microsoft.com/azure/azure-monitor/platform/agents-overview)」をご覧ください。

### <a name="assign-policies"></a>ポリシーの割り当て

前のセクションに記載されているポリシーを割り当てるには:

1. Azure portal で、 **[Azure Policy]**  >  **[割り当て]**  >  **[イニシアティブの割り当て]** に移動します。

    ![ポータルのポリシー インターフェイスのスクリーン ショット](./media/onboarding-at-scale1.png)

2. **[ポリシーの割り当て]** ページで、省略記号 (...) をクリックして **[スコープ]** を選択し、管理グループまたはサブスクリプションを選択します。 任意でリソース グループを選択します。 スコープによって、ポリシーの割り当て先のリソースまたはリソース グループが決まります。 次に、 **[スコープ]** ページの下部にある **[選択]** を選択します。

3. **[ポリシー定義]** の横にある省略記号 (...) を選択して、使用可能な定義の一覧を開きます。 **[検索]** ボックスに「**Azure Monitor**」と入力して、イニシアティブ定義をフィルター処理できます。

    ![ポータルのポリシー インターフェイスのスクリーン ショット](./media/onboarding-at-scale2.png)

4. **[割り当て名]** には選択したポリシー名が自動的に設定されますが、これを変更することはできません。 また、省略可能な説明を追加して、このポリシーの割り当ての詳細情報を提供することもできます。 **[割り当て担当者]** フィールドは、サインイン ユーザーに基づいて自動的に入力されます。 このフィールドは任意で、カスタム値を入力できます。

5. このポリシーについて、関連付ける Log Analytics エージェントの **[Log Analytics ワークスペース]** を選択します。

    ![ポータルのポリシー インターフェイスのスクリーン ショット](./media/onboarding-at-scale3.png)

6. **[マネージド ID の場所]** を確認します。 このポリシーが [DeployIfNotExists](https://docs.microsoft.com/azure/governance/policy/concepts/effects#deployifnotexists) という種類の場合は、このポリシーをデプロイするためにマネージド ID が必要になります。 ポータルでは、チェック ボックスの選択で示されているとおりにアカウントが作成されます。

7. **[割り当て]** を選択します。

ウィザードを完了すると、ポリシー割り当てが環境にデプロイされます。 ポリシーが有効になるまで、最大で 30 分かかる可能性があります。 30 分後に新しい VM を作成した後、その VM 上で Microsoft Monitoring Agent (MMA) エージェントが既定で有効になっているかどうかを確認することで、それをテストできます。

## <a name="install-required-agents-on-on-premises-servers"></a>必須のエージェントをオンプレミスのサーバーにインストールする

> [!NOTE]
> Azure 管理サービスにサーバーをオンボードする前に、必須の [Log Analytics ワークスペースと Azure Automation アカウント](./prerequisites.md#create-a-workspace-and-automation-account)を作成します。

オンプレミスのサーバーの場合、[Log Analytics エージェントと Microsoft Dependency Agent](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-hybrid-cloud) を手動でダウンロードしてインストールし、適切なワークスペースに接続するようにそれらを構成する必要があります。 これを行うには、ワークスペース ID とキー情報を指定します。これらは、Azure portal で Log Analytics ワークスペースに移動し、 **[設定]**  >  **[詳細設定]** を選択すると確認できます。

![Azure portal 内の Log Analytics ワークスペース詳細設定のスクリーンショット](./media/onboarding-on-premises.png)

## <a name="enable-and-configure-solutions"></a>ソリューションを有効にして構成する

ソリューションを有効にするには、Log Analytics ワークスペースを構成する必要があります。 オンボードされた Azure VM とオンプレミスのサーバーは、接続先の Log Analytics ワークスペースからソリューションを取得します。

このセクションでは、以下のソリューションについて説明します。

- [更新管理](#update-management)
- [変更履歴とインベントリ](#change-tracking-and-inventory-solutions)
- [Azure アクティビティ ログ](#azure-activity-log)
- [Azure Log Analytics の Agent Health](#azure-log-analytics-agent-health)
- [マルウェア対策評価](#antimalware-assessment)
- [Azure Monitor for VMs](#azure-monitor-for-vms)
- [Azure Security Center](#azure-security-center)

### <a name="update-management"></a>更新管理

Update Management、Change Tracking、および Inventory ソリューションには、Log Analytics ワークスペースと Automation アカウントの両方が必要です。 これらのリソースが正しく構成されるように、Automation アカウントを使用してオンボードすることをお勧めします。 詳しくは、「[Update Management、Change Tracking、および Inventory ソリューションの配布準備](https://docs.microsoft.com/azure/automation/automation-onboard-solutions-from-automation-account)」をご覧ください。

すべてのサーバーに対して Update Management ソリューションを有効にすることをお勧めします。 Azure VM とオンプレミスのサーバーでは Update Management が無料です。 Automation アカウントから Update Management を有効にした場合、ワークスペース内に[スコープ構成](https://docs.microsoft.com/azure/automation/automation-onboard-solutions-from-automation-account#scope-configuration)が作成されます。 更新サービスの対象のマシンを含むようにスコープを手動で更新する必要があります。

すべての既存のサーバーと今後のサーバーをカバーするには、スコープ構成を削除する必要があります。 これを行うには、Azure portal 内で Automation アカウントを表示し、 **[Update Management]**  >  **[マシンの管理]**  >  **[使用可能なマシンと今後のマシンすべてに対して有効にします]** を選択します。 この設定を有効にすると、ワークスペースに接続されているすべての Azure VM で Update Management を使用できます。

![Azure portal 内の Update Management のスクリーンショット](./media/onboarding-configuration1.png)

### <a name="change-tracking-and-inventory-solutions"></a>Change Tracking と Inventory のソリューション

Change Tracking と Inventory のソリューションをオンボードするには、Update Management の場合と同じ手順に従います。 Automation アカウントからのこれらのソリューションのオンボードについて詳しくは、「[Update Management、Change Tracking、および Inventory ソリューションの配布準備](https://docs.microsoft.com/azure/automation/automation-onboard-solutions-from-automation-account)」をご覧ください。

Change Tracking ソリューションは、Azure VM では無料で、オンプレミスのサーバーでは 1 か月あたりノードごとに 6 ドルかかります。 このコストには、Change Tracking、Inventory、Desired State Configuration が含まれます。 特定のオンプレミス サーバーのみを登録する場合は、それらのサーバーをオプトインできます。 すべての運用サーバーをオンボードすることをお勧めします。

#### <a name="opt-in-via-the-azure-portal"></a>Azure portal を介したオプトイン

1. Change Tracking と Inventory が有効になっている Automation アカウントに移動します。
2. **[Change Tracking]** を選択します。
3. 右上のウィンドウで **[マシンの管理]** を選択します。
4. **[選択したマシンで有効にします]** を選択し、マシン名の横にある **[追加]** をクリックして、有効にするマシンを選択します。
5. **[有効化]** を選択して、それらのマシンに対してソリューションを有効にします。

![Azure portal 内の Change Tracking のスクリーンショット](./media/onboarding-configuration2.png)

#### <a name="opt-in-by-using-saved-searches"></a>保存された検索を使用したオプトイン

また、スコープ構成を構成してオンプレミスのサーバーをオプトインできます。 スコープ構成では、保存された検索を使用します。

保存された検索を作成または変更するには、次の手順を使用します。

1. 上記の手順で構成した Automation アカウントにリンクされている Log Analytics ワークスペースに移動します。

2. **[全般]** の下の **[保存された検索]** を選択します。

3. **[フィルター]** ボックスに、「**Change Tracking**」と入力して、保存された検索の一覧をフィルター処理します。 結果内の **[MicrosoftDefaultComputerGroup]** を選択します。

4. Change Tracking にオプトインするコンピューターを含めるためにコンピューター名または VMUUID を入力します。

    ```kusto
    Heartbeat
    | where AzureEnvironment=~"Azure" or Computer in~ ("list of the on-premises server names", "server1")
    | distinct Computer
    ```

    > [!NOTE]
    > このサーバー名は、式に含まれる値と厳密に一致する必要があります。また、ドメイン名サフィックスは含めないでください。

5. **[保存]** を選択します。

6. 既定では、スコープ構成は保存された検索 **MicrosoftDefaultComputerGroup** にリンクされ、自動的に更新されます。

### <a name="azure-activity-log"></a>[Azure Activity Log (Azure アクティビティ ログ)]

[[Azure アクティビティ ログ]](https://docs.microsoft.com/azure/azure-monitor/platform/activity-logs-overview) も Azure Monitor の一部です。 ここから、Azure で発生するサブスクリプション レベルのイベントに関する分析情報を得ることができます。

このソリューションを追加するには:

1. Azure portal 内で、 **[すべてのサービス]** を開き、 **[管理 + ガバナンス]**  >  **[ソリューション]** を選択します。
2. **[ソリューション]** ビューで、 **[追加]** を選択します。
3. **[Activity Log Analytics]** を検索して選択します。
4. **作成** を選択します。

前のセクションで作成した、ソリューションが有効になっているワークスペースの**ワークスペース名**を指定する必要があります。

### <a name="azure-log-analytics-agent-health"></a>Azure Log Analytics の Agent Health

Azure Log Analytics の Agent Health ソリューションでは、Windows および Linux サーバーの正常性、パフォーマンス、可用性に関する分析情報が得られます。

このソリューションを追加するには:

1. Azure portal 内で、 **[すべてのサービス]** を開き、 **[管理 + ガバナンス]**  >  **[ソリューション]** を選択します。
2. **[ソリューション]** ビューで、 **[追加]** を選択します。
3. **[Azure Log Analytics の Agent Health]** を検索して選択します。
4. **作成** を選択します。

前のセクションで作成した、ソリューションが有効になっているワークスペースの**ワークスペース名**を指定する必要があります。

作成が完了した後、 **[表示]**  >  **[ソリューション]** を選択すると、ワークスペース リソース インスタンスに "**AgentHealthAssessment**" が表示されます。

### <a name="antimalware-assessment"></a>マルウェア対策評価

Antimalware Assessment ソリューションは、マルウェアに感染しているか感染のリスクが高まっているサーバーの識別に役立ちます。

このソリューションを追加するには:

1. Azure portal 内で、 **[すべてのサービス]** を開き、 **[管理 + ガバナンス]**  >  **[ソリューション]** を選択します。
2. **[ソリューション]** ビューで、 **[追加]** を選択します。
3. **[Antimalware Assessment]** を検索して選択します。
4. **作成** を選択します。

前のセクションで作成した、ソリューションが有効になっているワークスペースの**ワークスペース名**を指定する必要があります。

作成が完了した後、 **[表示]**  >  **[ソリューション]** を選択すると、ワークスペース リソース インスタンスに "**AntiMalware**" が表示されます。

### <a name="azure-monitor-for-vms"></a>VM に対する Azure Monitor

前の記事「[評価のために単一の VM 上の管理サービスを有効にする](./onboard-single-vm.md)」で説明されているように、[Azure Monitor for VMs](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-overview) は、VM インスタンスのビュー ページを通じて有効にすることができます。 この記事で説明されている他のソリューションに対して行ったように **[ソリューション]** ページからソリューションを直接有効にすることはしないでください。 大規模なデプロイの場合、[自動化](./onboarding-automation.md)を使用してワークスペース内で適切なソリューションを有効にする方が簡単な場合があります。

### <a name="azure-security-center"></a>Azure Security Center

このガイドでは、すべてのサーバーを既定で Azure Security Center の *Free*  レベルにオンボードすることをお勧めします。 このオプションでは、基本レベルのセキュリティ評価と、環境に対する実行可能なセキュリティの推奨事項が提供されます。 Security Center の *Standard* レベルにアップグレードすると、[Security Center の価格ページ](https://docs.microsoft.com/azure/security-center/security-center-pricing)で詳しく説明されている追加の特典が提供されます。

Azure Security Center の Free レベルを有効にするには、次の手順を使用します。

1. **Security Center** ポータル ページに移動します。
2. **[POLICY & COMPLIANCE]\(ポリシーとコンプライアンス\)** の下の **[セキュリティ ポリシー]** を選択します。
3. 右端のウィンドウで、作成した Log Analytics ワークスペース リソースを見つけます。
4. そのワークスペースに対して **[設定の編集]** を選択します。
5. **[価格レベル]** を選択します。
6. **[Free]** オプションを選択します。
7. **[保存]** を選択します。

## <a name="next-steps"></a>次の手順

サーバーのオンボードとアラートの作成にオートメーションを使用する方法を確認します。

> [!div class="nextstepaction"]
> [オンボードとアラート構成の自動化](./onboarding-automation.md)
