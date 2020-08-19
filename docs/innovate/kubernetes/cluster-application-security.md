---
title: クラスターとアプリケーションのセキュリティ
description: クラスターとアプリケーションのセキュリティのために、クラウド導入フレームワークでの Kubernetes について説明します。
author: sabbour
ms.author: asabbour
ms.date: 03/20/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: innovate
ms.openlocfilehash: f2dc11ac5145dea0fbf65aea65e74e5f2b8fe69c
ms.sourcegitcommit: 011525720bd9e2d9bcf03a76f371c4fc68092c45
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88572257"
---
<!-- cSpell:ignore asabbour sabbour kured -->

# <a name="cluster-and-application-security"></a>クラスターとアプリケーションのセキュリティ

Kubernetes のセキュリティの基本について理解し、クラスターのセキュアな設定とアプリケーションのセキュリティに関するガイダンスを確認します。

## <a name="plan-train-and-proof"></a>計画、トレーニング、および証明

開始時に、以下のチェックリストとリソースを使用するとクラスターの運用とセキュリティを計画するのに役立ちます。 次の質問に答えてください。

> [!div class="checklist"]
>
> - Kubernetes クラスターのセキュリティと脅威モデルを確認しましたか?
> - クラスターでロールベースのアクセス制御が有効になっていますか?

<!-- markdownlint-disable MD033 -->

> [!div class="tdCol2BreakAll"]
>
> | チェック リスト  | リソース |
> |------------------------------------------------------------------|-----------------------------------------------------------------|
> | **セキュリティの基本に関するホワイトペーパーを入念に確認する。** セキュリティで保護された Kubernetes 環境の主な目標は、実行されるアプリケーションを保護すること、セキュリティの問題を迅速に特定して対処できること、および今後同様の問題を回避することです。 | [Kubernetes を保護するための決定版ガイド (ホワイトペーパー)](https://clouddamcdnprodep.azureedge.net/gdc/gdc8LXmoZ/original)     |
> | **クラスター ノードのセキュリティ強化の設定を確認する。** セキュリティが強化されたホスト OS を使用すると、攻撃の対象範囲が減り、コンテナーを安全にデプロイできます。 | [AKS 仮想マシン ホストでのセキュリティ強化](/azure/aks/security-hardened-vm-host-image)     |
> | **クラスターのロールベースのアクセス制御 (RBAC) を設定する。** この制御メカニズムを使用して、ユーザーまたはユーザー グループに対して、リソースの作成または変更、実行中のアプリケーション ワークロードのログの表示などの操作を行うアクセス許可を割り当てることができます。 | [Kubernetes でのロールベースのアクセス制御 (RBAC) について (ビデオ)](https://www.youtube.com/watch?v=G3R24JSlGjY&list=PLLasX02E8BPCrIhFrc_ZiINhbRkYMKdPT&index=12) <br> [Azure AD と Azure Kubernetes Service を統合する](/azure/aks/azure-ad-integration) <br> [クラスター構成ファイルへのアクセスの制限](/azure/aks/control-kubeconfig-access)   |

## <a name="deploy-to-production-and-apply-best-practices"></a>運用環境へのデプロイとベスト プラクティスの適用

運用環境向けにアプリケーションを準備するときには、ベスト プラクティスの最小セットを実装する必要があります。 この段階で、以下のチェックリストを使用します。 次の質問に答えてください。

> [!div class="checklist"]
>
> - イングレス、エグレス、およびポッド間通信のためのネットワーク セキュリティ規則を構成しましたか?
> - クラスターは、ノードのセキュリティ更新プログラムを自動的に適用するように構成されていますか?
> - クラスターとコンテナーのワークロードに対してセキュリティ スキャン ソリューションを実行していますか?

<!-- markdownlint-disable MD033 -->

> [!div class="tdCol2BreakAll"]
>
> | チェック リスト  | リソース |
> |------------------------------------------------------------------|-----------------------------------------------------------------|
> | **グループ メンバーシップを使用してクラスターへのアクセスを制御する。** ユーザーの ID またはグループ メンバーシップに基づいて、クラスター リソースへのアクセスを制限するように Kubernetes のロールベースのアクセス制御 (RBAC) を構成します。 | [RBAC と Azure AD グループを使用してクラスターへのアクセスを制御する](/azure/aks/azure-ad-rbac)    |
> | **シークレット管理ポリシーを作成する。** Kubernetes でシークレット管理を使用して、パスワードや証明書などの機密情報を安全にデプロイし、管理します。 | [Kubernetes でのシークレット管理について (ビデオ)](https://www.youtube.com/watch?v=KmhM33j5WYk&list=PLLasX02E8BPCrIhFrc_ZiINhbRkYMKdPT&index=10) |
> | **ネットワーク ポリシーを使用して、ポッド間のネットワーク トラフィックをセキュリティで保護する。** 最低限の特権の原則を適用して、クラスター内のポッド間のネットワーク トラフィック フローを制御します。 | [ネットワーク ポリシーを使用したポッド間のトラフィックのセキュリティ保護](/azure/aks/use-network-policies) |
> | **許可された IP を使用して API サーバーへのアクセスを制限する。** API サーバーへのアクセスを、限られた一連の IP アドレス範囲に制限することで、クラスターのセキュリティを向上させ、攻撃対象を最小限にします。 | [API サーバーへのアクセスをセキュリティで保護する](/azure/aks/api-server-authorized-ip-ranges) |
> | **クラスターのエグレス トラフィックを制限する。** クラスターのエグレス トラフィックを制限する場合に、どのポートとアドレスを許可するか確認します。 Azure Firewall またはサードパーティ製ファイアウォール アプライアンスを使用して、エグレス トラフィックをセキュリティで保護し、これらの必要なポートとアドレスを定義することができます。 | [AKS でクラスター ノードに対するエグレス トラフィックを制御する](/azure/aks/limit-egress-traffic) |
> | **Web アプリケーション ファイアウォール (WAF) を使用してトラフィックをセキュリティで保護する。** Kubernetes クラスターのイングレス コントローラーとして Azure Application Gateway を使用します。  | [Azure Application Gateway をイングレス コントローラーとして構成する](/azure/application-gateway/ingress-controller-overview)    |
> | **ワーカー ノードにセキュリティとカーネルの更新プログラムを適用する。** AKS ノードの更新エクスペリエンスを理解します。 クラスターを保護するために、AKS のノードにセキュリティ更新プログラムが自動的に適用されます。 これらの更新プログラムには、OS のセキュリティ修正プログラムやカーネルの更新プログラムが含まれています。 これらの更新プログラムの中には、プロセスを完了するためにノードの再起動が必要なものがあります。 | [更新プログラムを適用するために Kured を使用してノードを自動的に再起動する](/azure/aks/node-updates-kured) |
> | **コンテナーとクラスターのスキャン ソリューションを構成する。** Azure Container Registry にプッシュされたコンテナーをスキャンし、クラスター ノード、クラウド トラフィック、およびセキュリティ制御についてより詳しく把握します。 | [Azure Container Registry と Security Center の統合](/azure/security-center/azure-container-registry-integration) <br> [Azure Kubernetes Service と Security Center の統合](/azure/security-center/azure-kubernetes-service-integration)  |

## <a name="optimize-and-scale"></a>最適化とスケーリング

アプリケーションの運用開始後、ワークフローの最適化と、アプリケーションとチームでのスケーリングの準備はどのように行うことができますか? 最適化とスケーリングのチェックリストを使用して準備してください。 次の質問に答えてください。

> [!div class="checklist"]
>
> - ガバナンスとクラスターのポリシーを大規模に適用できますか?

<!-- markdownlint-disable MD033 -->

> [!div class="tdCol2BreakAll"]
>
> | チェック リスト  | リソース |
> |------------------------------------------------------------------|-----------------------------------------------------------------|
> | **クラスター ガバナンス ポリシーを適用する。** 一貫した一元的な方法で、クラスターで大規模な実施と保護を適用します。 | [Azure Policy を使用したデプロイの制御](/azure/governance/policy/concepts/rego-for-aks)    |
> | **クラスター証明書を定期的にローテーションする。** Kubernetes では、そのコンポーネントの多くで認証に証明書が使用されます。 セキュリティやポリシー上の理由から、これらの証明書を定期的にローテーションすることが必要な場合があります。 | [Azure Kubernetes Service (AKS) での証明書のローテーション](/azure/aks/certificate-rotation)    |
