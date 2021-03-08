---
title: エンタープライズ規模の Azure Kubernetes Service (AKS) セキュリティ ガバナンスおよびコンプライアンス
description: クラウド セキュリティ コントロールのライフサイクルと、AKS セキュリティ コントロール、Azure Policy、AKS コスト管理を設定する方法について説明します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 03/01/2021
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.openlocfilehash: 6f7b00443846d2219828784c077b00516211752e
ms.sourcegitcommit: b8f8b7631aabaab28e9705934bf67dad15e3a179
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101801289"
---
<!-- docutune:casing "Contoso Financials" -->

# <a name="aks-enterprise-scale-platform-security-governance-and-compliance"></a>AKS のエンタープライズ規模のプラットフォーム セキュリティ ガバナンスおよびコンプライアンス

この記事では、ソリューションを実装する前に考慮すべき Azure Kubernetes Service (AKS) セキュリティ ガバナンスの側面について説明します。

実装は顧客によって異なるため、これらのほとんどの内容はテクノロジに依存しません。 記事では、Azure といくつかのオープンソース ソフトウェア (OSS) を使用してソリューションを実装する方法に重点を置いています。

## <a name="cloud-security-control-lifecycle"></a>クラウド セキュリティ コントロールのライフサイクル

クラウド ネイティブなセキュリティおよびガバナンスでは、クラウドが場合によっては動的であるのに対して、ほとんどの企業は比較的安定した環境に慣れています。 従来の環境では、ID、ネットワーク、マシンや仮想マシン (VM) の OS 管理などの従来の IT 機能と整合している検出および防止コントロールが、構造化された組織モデル内に含まれています。 従来のセキュリティおよびコンプライアンス チームが直面する全体的な構造やインフラストラクチャの変更、またはセキュリティとリスクの目標を達成するために必要なコントロールや監視の変更は比較的まれです。

クラウドと DevOps モデルの導入により、セキュリティは変更に適応して、ビジネスや発展の妨げにならないようにする必要があります。 セキュリティの適応には、適切なロールと責任の作成などのユーザー プロセスと、スキャン、テスト、コードとしてのインフラストラクチャなどのオートメーションおよびテクニカル ツールが含まれます。 これらのツールは、引き続きセキュリティ要件を満たしながら、イノベーションと高速な配信時間を可能にする DevOps パイプラインの一部です。

まずは現在行っているのと同じことから始めたくなりますが、重要なポイントは、現在オンプレミスで行っているのとまったく同じことをクラウドで行おうとすると失敗するということです。 クラウド導入は、スキルを習得して更新したり、組織のセキュリティ体制を向上させたりする機会です。 セキュリティを運用プロセスへの展開の一部として効率化することによって、セキュリティを阻害要因ではなく実現要因として位置付けることができます。 10 ～ 20 年前のツールやプロセスを使用する場合は、10 ～ 20 年前のセキュリティの課題、問題、不満点を継承または導入することになります。 クラウド導入は、やり直す機会です。

やり直しは、クラウドの理解から始まります。

- 使用可能なクラウド サービス、コントロール、可視性はどのようなものですか?
- クラウド サービスおよびコントロールをどのように操作しますか?
- 使用可能なセキュリティ ツールおよびガバナンス ツールはどのようなものですか?
- 最新の DevOps モデルで基幹業務をより俊敏にできるように、セキュリティおよびガバナンス ツールをプロセスやシステムでどのように使用できますか?
- セキュリティおよびコンプライアンス要件を確実に満たすにはどうしたらよいですか?

多くの場合は、セキュリティ コントロールの確立と、それらのコントロール自体の管理および操作の両方で、プロセスの観点からライフサイクル フレームワークを開始することが最も簡単です。 セキュリティ コントロールを確立するために、多くの顧客は、NIST 800-53、ISO、CIS ベンチマーク、HIPAA/HITRUST などの一般的なフレームワークおよび標準を使用します。 これらの標準は、包括的なフレームワークの確立に役立つと共に、セキュリティ コントロールおよびプロセスを確立、文書化、監査するために役立つガイダンスを提供します。

セキュリティ コントロールを管理および操作するために、クラウドと DevOps を含むセキュリティ開発ライフサイクル プロセスなどのさまざまなモデルを調べることができます。 これらのアプローチおよびモデルでは、セキュリティ コントロールを組織内のその他のすべての資産と同様に扱い、ライフサイクル管理が含まれます。 次の図は、組織のセキュリティ コントロール ライフサイクル管理の例を示しています。

:::image type="content" source="./media/security-control-lifecycle.png" alt-text="Azure セキュリティ コントロール ライフサイクル管理の設定を示す図。" border="false":::

### <a name="azure-security-control-architecture"></a>Azure セキュリティ コントロールのアーキテクチャ

すべての Azure セキュリティ コントロールは Azure Resource Manager API から始まり、これに基づいて構築されています。 次の図は、Azure Policy エンジンが、あらゆるソースからのポリシーを適用するためにどのように役立つかを示しています。 つまり、組織は複数の場所でポリシーを定義する必要がないため、この機能は重要です。

:::image type="content" source="./media/enterprise-control-plane-architecture.png" alt-text="Azure セキュリティ コントロールのアーキテクチャを示す図。" border="false":::

### <a name="security-control-governance"></a>セキュリティ コントロールのガバナンス

セキュリティ コントロール フレームワークの主な利点の 1 つは、ガバナンスとコントロールの監査が組み込まれていることです。 Azure Policy に加えて、Azure セキュリティ コントロールのガバナンスには、[Azure Security Center](/azure/security-center/security-center-introduction) が、その[セキュリティ スコア](/azure/security-center/secure-score-security-controls)およびコンプライアンス ダッシュボード機能と共に含まれています。 コンプライアンスは、根本的に、ガバナンスの可観測性に関するすべての機能です。 次の図は、サンプルのコンプライアンス ダッシュボードを示しています。

:::image type="content" source="./media/enterprise-control-plane-governance.png" alt-text="Azure セキュリティ コントロールのガバナンスを示す図。" border="false":::

## <a name="azure-security-controls-setup"></a>Azure セキュリティ コントロールの設定

このセクションでは、包括的な Azure 固有のセキュリティ コントロール フレームワークの設定について説明します。 このセクションでは、組織のセキュリティ コントロールが既に確立されていることを前提にしています。 これらのセキュリティ コントロールの定義は、この記事の範囲外です。

以降のセクションでは、架空の Contoso Financials のシナリオでのセキュリティ要件を満たすための次のコントロールの実装について説明します。

- 監査レポートの目的ですべてのクラウド API 要求をログに記録する。
- API サーバーへのアクセスをセキュリティで保護するために認可された IP 範囲を有効にする。
- 許可されている場所を使用して AKS クラスターを特定のリージョンでのみ作成する。

これらのコントロールは、完全な Azure デプロイに含まれているセキュリティ コントロールと Azure ポリシーのサブセットにすぎません。 そのプロセスを理解したら、それを繰り返して、組織に必要なその他のすべてのセキュリティ コントロールを実装できます。

### <a name="log-all-cloud-api-requests-for-audit-reporting-purposes"></a>監査レポートの目的ですべてのクラウド API 要求をログに記録する

[Azure アクティビティ ログ](/azure/azure-monitor/essentials/platform-logs-overview)では、監査ログを含むすべての Azure Resource Manager 操作をキャプチャします。 その課題は、アクティビティ ログの保有ライフサイクルが限られていることです。 監査レポートのログを保持するには、アクティビティ ログから、Azure Monitor ログなどのより永続的な保存場所にデータをエクスポートする必要があります。

Azure サブスクリプションでのアクティビティ ログの収集を設定する方法に関するチュートリアルについては、「[Log Analytics ワークスペースに送信する](/azure/azure-monitor/essentials/activity-log#send-to-log-analytics-workspace)」を参照してください。

次のスクリーンショットは、Azure Log Analytics ワークスペースでの Azure アクティビティ ログのキャプチャを示しています。

:::image type="content" source="./media/activity-log-capture.png" alt-text="アクティビティ ログのキャプチャを示すスクリーンショット。" border="false":::

### <a name="enable-azure-security-monitoring-to-check-for-authorized-ip-ranges"></a>認可された IP 範囲をチェックするために Azure セキュリティ監視を有効にする

何かを作成する前には、クラウド プロバイダーのセキュリティ監視のベスト プラクティスや推奨事項について確認してください。 クラウド プロバイダーによって組織に必要なすべてのセキュリティ コントロールが実装されるわけではありませんが、重要なのは、一から作り直すことを避けるために提供されているコントロールを使用し、カスタム コントロールは必要な場合にのみ作成することです。

Azure では、Azure Security Center を有効にしてセキュリティ監視の推奨事項を確認できます。 Azure Security Center を使用すると、認可された IP 範囲が AKS クラスターで有効になっているかどうかを判定することもできます。

Azure サブスクリプションで Azure Security Center Standard を有効にするには、「[クイック スタート: Azure Security Center を設定する](/azure/security-center/security-center-get-started)」を参照してください。

次のスクリーンショットは、Azure Security Center Standard の監視を示しています。

:::image type="content" source="./media/security-center-standard.png" alt-text="Azure Security Center Standard の監視を示すスクリーンショット。":::

### <a name="enforce-an-azure-policy-to-create-aks-clusters-only-in-certain-regions"></a>AKS クラスターを特定のリージョンでのみ作成するための Azure Policy を適用する

クラウド プロバイダーのポリシーの適用についても確認してください。 Azure では、エンタープライズ コントロール プレーンの重要な部分である Azure Policy を評価します。 Azure Policy ですべてを行うことはできませんが、重要なのは、一から作り直すことを避けるために提供されているものを使用し、カスタム コントロールは必要な場合にのみ作成することです。

Azure Policy を使用すると、AKS クラスターなどの Azure リソースを作成するための許可されている場所を実装できます。 詳細情報と手順については、「[チュートリアル: コンプライアンスを強制するポリシーの作成と管理](/azure/governance/policy/tutorials/create-and-manage)」を参照してください。

次のスクリーンショットは、Azure Policy を使用して、許可されるリージョンの場所を実装する手順を示しています。

1. Azure Policy で、組み込みの **[許可されている場所]** ポリシーを選択します。

   :::image type="content" source="./media/policy-definitions.png" alt-text="組み込みの Azure ポリシーを示すスクリーンショット。":::

1. ポリシーの定義を表示し、ポリシーを割り当てます。

   :::image type="content" source="./media/assign-policy.png" alt-text="[許可されている場所] ポリシーの定義を示すスクリーンショット。":::

1. ポリシーを構成します。

   :::image type="content" source="./media/configure-policy.png" alt-text="[許可されている場所] の構成画面を示すスクリーンショット。":::

1. ポリシー パラメーターを構成します。

   :::image type="content" source="./media/configure-parameters.png" alt-text="[許可されている場所] のパラメーター画面を示すスクリーンショット。":::

1. ポリシーの割り当てを表示します。

   :::image type="content" source="./media/policy-assignments.png" alt-text="Azure Policy の割り当てを示すスクリーンショット。":::

リソースの作成を特定のリージョンに制限する機能は、多くの使用可能な Azure ポリシーのうちの 1 つにすぎません。

- Azure Policy で実行できる内容のサンプルについては、「[Azure Policy のサンプル](/azure/governance/policy/samples/)」を参照してください。
- Azure Security Center 用の Azure Policy の詳細については、「[Azure Security Center 用の Azure Policy 組み込み定義](/azure/security-center/policy-reference)」を参照してください。

## <a name="cost-governance"></a>コスト ガバナンス

コスト ガバナンスは、コストを管理するためのポリシーを実装する継続的なプロセスです。 Kubernetes のコンテキストでは、組織がコストを管理および最適化できるいくつかの方法が存在します。 これには、リソースの使用状況や消費を管理および制御し、基になるインフラストラクチャを予防的に監視および最適化するためのネイティブな Kubernetes ツールが含まれます。

このセクションでは、[Kubecost](https://kubecost.com/) を使用して AKS クラスターのコストを管理する方法を示します。 コスト割り当てのスコープをデプロイ、サービス、ラベル、ポッド、または名前空間に指定できるため、クラスター ユーザーのチャージバックまたは表示での柔軟性が提供されます。

### <a name="install-kubecost"></a>Kubecost をインストールする

Kubecost には、いくつかのインストール オプションがあります。 詳細については、[Kubecost のインストール](https://docs.kubecost.com/install)に関するページを参照してください。

Kubecost を直接インストールするには、次のコマンドを使用します。

```bash

# Create the Kubecost namespace

kubectl create namespace kubecost

# Install Kubecost into the AKS cluster

kubectl apply -f https://raw.githubusercontent.com/kubecost/cost-analyzer-helm-chart/master/kubecost.yaml --namespace kubecost
```

Helm 2 を使用して Kubecost をインストールするには、次のコマンドを使用します。

```bash
helm repo add kubecost https://kubecost.github.io/cost-analyzer/
helm install kubecost/cost-analyzer --namespace kubecost --name kubecost --set kubecostToken="YWxnaWJib25AbWljcm9zb2Z0LmNvbQ==xm343yadf98"
```

Helm 3 を使用して Kubecost をインストールするには、次のコマンドを使用します。

```bash
kubectl create namespace kubecost
helm repo add kubecost https://kubecost.github.io/cost-analyzer/
helm install kubecost kubecost/cost-analyzer --namespace kubecost --set kubecostToken="YWxnaWJib25AbWljcm9zb2Z0LmNvbQ==xm343yadf98"
```

数分経ったら、Kubecost が起動して実行中であることを確認します。

```bash
kubectl get pods -n kubecost

# Connect to the Kubecost dashboard UI

kubectl port-forward -n kubecost svc/kubecost-cost-analyzer 9090:9090
```

ここでブラウザーを開き、`http://127.0.0.1:9090` を指定して Kubecost UI を開くことができます。 Kubecost UI で、コスト割り当て情報を表示するクラスターを選択します。

### <a name="navigate-kubecost"></a>Kubecost 内を移動する

Kubecost では、リソースが次のカテゴリに分類されます。

- 月単位のクラスター コスト
- 名前空間のコスト
- デプロイ リソースのコスト
- コスト効率

次のダッシュボードのような概要を表示するクラスターを選択します。

:::image type="content" source="./media/kubecost-dashboard.png" alt-text="Kubecost のダッシュボードを示すスクリーンショット。":::

リソースの名前空間のコストを詳しく調査するには、左側にある **[割り当て]** を選択します。 **[割り当て]** には、CPU、メモリ、永続ボリューム、ネットワークのコストが表示されます。 Kubecost では Azure の価格からデータを取得しますが、リソースのカスタム コストを設定することもできます。

:::image type="content" source="./media/kubecost-allocation.png" alt-text="Kubecost の [割り当て] 画面を示すスクリーンショット。":::

使用率が低いリソースのコスト削減を詳しく調査するには、左側にある **[削減額]** を選択します。 **[削減額]** には、使用率が低いノードおよびポッドや破棄されたリソースに関する情報が表示され、クラスター内のオーバープロビジョニングされているリソース要求が識別されます。 次のスクリーンショットは、 **[削減額]** の概要の例を示しています。

:::image type="content" source="./media/kubecost-savings.png" alt-text="Kubecost の [削減額] 画面を示すスクリーンショット。":::

少し時間を取って、Kubecost によって提供されるさまざまなビューや機能の間を移動してください。

## <a name="design-considerations"></a>設計上の考慮事項

AKS には、Azure Active Directory、Azure Storage、Azure Virtual Network などの他の Azure サービスへの複数のインターフェイスがあります。これには、計画段階で特別な注意が必要です。 AKS ではまた、他のインフラストラクチャ ランドスケープの場合と同じセキュリティ、ガバナンス、コンプライアンス メカニズムおよびコントロールを適用することを考慮する必要がある追加の複雑さも発生します。

AKS セキュリティ ガバナンスおよびコンプライアンスに関するその他の設計上の考慮事項のいくつかを次に示します。

- クラスターのコントロール プレーンがインターネット経由でアクセス可能 (既定) か、またはプライベート クラスターとして特定の仮想ネットワーク内でのみアクセス可能かを判定します。

- 組み込みの [AppArmor](/azure/aks/operator-best-practices-cluster-security#app-armor) Linux セキュリティ モジュールを使用して、コンテナーが実行できるアクション (読み取り、書き込み、実行など)、またはファイル システムのマウントなどのシステム機能を制限することを評価します。

- プロセス レベルで[セキュア コンピューティング (seccomp)](/azure/aks/operator-best-practices-cluster-security#secure-computing) を使用して、コンテナーが実行できるプロセス呼び出しを制限することを評価します。

- 脅威の検出のために [Azure Defender for Kubernetes](/azure/security-center/defender-for-kubernetes-introduction) を使用することを検討します。

## <a name="design-recommendations"></a>設計の推奨事項

- Azure ロールベースのアクセス制御を使用して [Kubernetes クラスター構成](/azure/aks/control-kubeconfig-access)ファイルへのアクセスを制限します。

- [リソースへのポッド アクセスをセキュリティで保護します](/azure/aks/developer-best-practices-pod-security#secure-pod-access-to-resources)。 最小限のアクセス許可を付与し、ルートまたは特権エスカレーションの使用を避けます。

- [ポッドマネージド ID](/azure/aks/operator-best-practices-identity#use-pod-managed-identities) と[シークレット ストア CSI ドライバーの Azure Key Vault プロバイダー](https://github.com/Azure/secrets-store-csi-driver-provider-azure)を使用して、シークレット、証明書、接続文字列を保護します。

- 定期的に (90 日ごとなど) [証明書をローテーションする](/azure/aks/certificate-rotation)ようにしてください。

- 可能な場合は [AKS ノード イメージのアップグレード](/azure/aks/node-image-upgrade)を使用して AKS クラスターのノード イメージを更新するか、または [kured](/azure/aks/node-updates-kured) を使用して更新プログラム適用後のノードの再起動を自動化します。

- [Azure Security Center](/azure/security-center/security-center-introduction) で AKS の推奨事項を表示します。

- [Kubernetes 用の Azure Policy アドオン](/azure/aks/use-pod-security-on-azure-policy)を使用して、構成を監視および適用します。
