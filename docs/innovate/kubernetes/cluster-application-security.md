---
title: クラスターとアプリケーションのセキュリティ
description: クラスターとアプリケーションのセキュリティのためのクラウド導入フレームワークでの Kubernetes のセキュリティの重要項目について説明します。
author: sabbour
keywords: アプリケーションのセキュリティ, セキュリティの重要項目, Kubernetes のセキュリティ
ms.author: brblanch
ms.date: 04/06/2021
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: innovate
ms.custom: think-tank, seo-caf-innovate
ms.openlocfilehash: 071a25ea6895de2c8b52d6f20a87a9f74fb859d6
ms.sourcegitcommit: 37b9560a02ae301d05da338446a5d47fa3bc1908
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2021
ms.locfileid: "107371421"
---
<!-- cSpell:ignore kured -->

# <a name="cluster-and-application-security"></a>クラスターとアプリケーションのセキュリティ

Kubernetes のセキュリティの基本について理解し、クラスターのセキュアな設定とアプリケーションのセキュリティに関するガイダンスを確認します。 Kubernetes クラスターには分散型で動的な性質があるため、コンテナーのライフサイクル全体において Kubernetes のセキュリティは重要です。 アプリケーションのセキュリティの強度は、アプリケーションのセキュリティを構成するサービスのチェーンにおいて最も弱いリンク程度でしかありません。

## <a name="plan-train-and-proof"></a>計画、トレーニング、および証明

開始時に、下のセキュリティの重要項目のチェックリストと Kubernetes セキュリティ リソースを使用すると、クラスターの操作とアプリケーションのセキュリティについて計画するのに役立ちます。  このセクションを終了すると、こちらの質問に答えることができます。

> [!div class="checklist"]
>
> - Kubernetes クラスターのセキュリティと脅威モデルを確認しましたか?
> - クラスターで Kubernetes のロールベースのアクセス制御が有効になっていますか?

**セキュリティ チェックリスト:**

- **セキュリティの基本に関するホワイトペーパーを入念に確認する。** セキュリティで保護された Kubernetes 環境の主な目標は、実行されるアプリケーションを保護すること、セキュリティの問題を迅速に特定して対処できること、および今後同様の問題を回避することです。 詳細については、「[Kubernetes を保護するための決定版ガイド (ホワイト ペーパー)](https://clouddamcdnprodep.azureedge.net/gdc/gdc8LXmoZ/original)」を参照してください。

- **クラスター ノードのセキュリティ強化の設定を確認する。** セキュリティが強化されたホスト OS を使用すると、攻撃の対象範囲が減り、コンテナーを安全にデプロイできます。 詳細については、[AKS 仮想マシン ホストでのセキュリティ強化に関するページ](/azure/aks/security-hardened-vm-host-image)を参照してください。

- **クラスター Kubernetes のロールベースのアクセス制御 (Kubernetes RBAC) をセットアップする。** この制御メカニズムを使用して、ユーザーまたはユーザー グループに対して、リソースの作成または変更、実行中のアプリケーション ワークロードのログの表示などの操作を行うアクセス許可を割り当てることができます。

  詳細については、「
  - [Kubernetes のロールベースのアクセス制御 (Kubernetes RBAC) を理解する (ビデオ)](https://www.youtube.com/watch?list=PLLasX02E8BPCrIhFrc_ZiINhbRkYMKdPT&v=G3R24JSlGjY&index=12) <br>
  - [Azure AD と Azure Kubernetes Service を統合する](/azure/aks/azure-ad-integration-cli) <br>
  - [クラスター構成ファイルへのアクセスの制限](/azure/aks/control-kubeconfig-access)

## <a name="deploy-to-production-and-apply-kubernetes-security-best-practices"></a>運用環境へのデプロイと Kubernetes セキュリティのベスト プラクティスの適用

運用環境向けにアプリケーションを準備するときには、ベスト プラクティスの最小セットを実装します。 この段階で、こちらのチェックリストを使用します。  このセクションを終了すると、こちらの質問に答えることができます。

> [!div class="checklist"]
>
> - イングレス、エグレス、およびポッド間通信のためのネットワーク セキュリティ規則を設定しましたか?
> - クラスターは、ノードのセキュリティ更新プログラムを自動的に適用するように設定されていますか?
> - クラスターとコンテナーのサービスに対してセキュリティ スキャン ソリューションを実行していますか?

**セキュリティ チェックリスト:**

- **グループ メンバーシップを使用してクラスターへのアクセスを制御する。** ユーザーの ID またはグループ メンバーシップに基づいてクラスター リソースへのアクセスを制限するように Kubernetes のロールベースのアクセス制御 (Kubernetes RBAC) を構成します。 詳細については、[Kubernetes RBAC と Azure AD ID を使用してクラスター リソースへのアクセスを制御することに関するページ](/azure/aks/azure-ad-rbac)を参照してください。

- **シークレット管理ポリシーを作成する。** Kubernetes でシークレット管理を使用して、パスワードや証明書などの機密情報を安全にデプロイし、管理します。 詳細については、「[Kubernetes でのシークレット管理について (ビデオ)](https://www.youtube.com/watch?list=PLLasX02E8BPCrIhFrc_ZiINhbRkYMKdPT&v=KmhM33j5WYk&index=10)」を参照してください。

- **ネットワーク ポリシーを使用して、ポッド間のネットワーク トラフィックをセキュリティで保護する。** 最低限の特権の原則を適用して、クラスター内のポッド間のネットワーク トラフィック フローを制御します。 詳細については、[ネットワーク ポリシーを使用してポッド内のトラフィックをセキュリティで保護することに関するページ](/azure/aks/use-network-policies)を参照してください。

- **許可された IP を使用して API サーバーへのアクセスを制限する。** API サーバーへのアクセスを、限られた一連の IP アドレス範囲に制限することで、クラスターのセキュリティを向上させ、攻撃対象を最小限にします。 詳細については、[API サーバーへのアクセスをセキュリティで保護することに関するページ](/azure/aks/api-server-authorized-ip-ranges)をご覧ください。

- **クラスターのエグレス トラフィックを制限する。** クラスターのエグレス トラフィックを制限する場合に、どのポートとアドレスを許可するか確認します。 Azure Firewall またはサードパーティ製ファイアウォール アプライアンスを使用して、エグレス トラフィックをセキュリティで保護し、これらの必要なポートとアドレスを定義することができます。 詳細については、「[AKS でクラスター ノードに対するエグレス トラフィックを制御する](/azure/aks/limit-egress-traffic)」を参照してください。

- **Web アプリケーション ファイアウォール (WAF) を使用してトラフィックをセキュリティで保護する。** Kubernetes クラスターのイングレス コントローラーとして Azure Application Gateway を使用します。 詳細については、[Azure Application Gateway をイングレス コントローラーとして構成することに関するページ](/azure/application-gateway/ingress-controller-overview)を参照してください。

- **ワーカー ノードにセキュリティとカーネルの更新プログラムを適用する。** AKS ノードの更新エクスペリエンスを理解します。 クラスターを保護するために、AKS のノードにセキュリティ更新プログラムが自動的に適用されます。 これらの更新プログラムには、OS のセキュリティ修正プログラムやカーネルの更新プログラムが含まれています。 これらの更新プログラムの中には、プロセスを完了するためにノードの再起動が必要なものがあります。 詳細については、[更新プログラムを適用するために kured を使用してノードを自動的に再起動することに関するページ](/azure/aks/node-updates-kured)を参照してください。

- **コンテナーとクラスターのスキャン ソリューションを構成する。** Azure Container Registry にプッシュされたコンテナーをスキャンし、クラスター ノード、クラウド トラフィック、およびセキュリティ制御についてより詳しく把握します。

  詳細については、次を参照してください。
  - [Azure Container Registry と Security Center の統合](/azure/security-center/defender-for-container-registries-introduction) <br>
  - [Azure Kubernetes Service と Security Center の統合](/azure/security-center/defender-for-kubernetes-introduction)

## <a name="optimize-and-scale"></a>最適化とスケーリング

アプリケーションの運用開始後、ワークフローの最適化と、アプリケーションとチームでのスケーリングの準備はどのように行うことができますか? 最適化とスケーリングのチェックリストを使用して準備してください。  このセクションを終了すると、こちらの質問に答えることができます。

> [!div class="checklist"]
>
> - ガバナンスとクラスターのポリシーを大規模に適用できますか?

**セキュリティ チェックリスト:**

- **クラスター ガバナンス ポリシーを適用する。** 一貫した一元的な方法で、クラスターで大規模な実施と保護を適用します。 詳細については、[Azure Policy を使用したデプロイの制御に関するページ](/azure/governance/policy/concepts/policy-for-kubernetes)を参照してください。

- **クラスター証明書を定期的にローテーションする。** Kubernetes では、そのコンポーネントの多くで認証に証明書が使用されます。 セキュリティやポリシー上の理由から、これらの証明書を定期的にローテーションすることが必要な場合があります。 詳細については、「[Azure Kubernetes Service (AKS) での証明書のローテーション](/azure/aks/certificate-rotation)」を参照してください。