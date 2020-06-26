---
title: 実装ガイドライン
description: 実装ガイドライン。
author: BrianBlanchard
ms.author: brblanch
ms.date: 06/15/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.openlocfilehash: 124fbd45892f7d9e6b410de304d3843a418c9b0d
ms.sourcegitcommit: 9b183014c7a6faffac0a1b48fdd321d9bbe640be
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/19/2020
ms.locfileid: "85076896"
---
<!-- cSpell:ignore interdomain VMSS -->

# <a name="implementation-guidelines"></a>実装ガイドライン

このセクションは、エンタープライズ規模のプラットフォーム ネイティブ リファレンス実装の基本と、設計目標、現在の設計、FAQ、および既知の問題の概要について説明します。

エンタープライズ規模のアーキテクチャを実装するには、2 つのアクティビティ セットが必要です。 1 つ目のセットは、エンタープライズ規模の場合に該当するはずです。 これには、初期構成を確立するために Azure Active Directory 管理者が実行する必要があるアクティビティが含まれます。 これらは本質的にシーケンシャルであり、主に 1 回限りのアクティビティです。 2 つ目のセットには、 **[ファイル]**  >  **[新規]**  >  **[リージョン]** と **[ファイル]**  >  **[新規]**  >  **[Landing zone]\(ランディング ゾーン\)** が含まれています。 これらは、ランディング ゾーンのインスタンス化に必要な定期的なアクティビティであり、Azure AD 内のリソース作成を調整するワークフローを開始するためにユーザー入力が必要です。 大規模に運用するためには、これらのアクティビティが "_コードとしてのインフラストラクチャ (IaC)_ " とデプロイ パイプラインによる自動化の原則に従うことが重要です。

次の表には、空白列が含まれています。 これらの列を使用して、環境に固有の値と注記を取得します。

## <a name="what-must-be-true-for-an-enterprise-scale-landing-zone"></a>エンタープライズ規模のランディング ゾーンに該当すること

### <a name="enterprise-agreement-ea-enrollment-and-azure-ad-tenants"></a>Enterprise Agreement (EA) の登録と Azure AD テナント

| Activities | 必須のパラメーター | エンタープライズ規模の構成例 |
|---|---|---|
| 1.EA 管理者と通知アカウントを設定します。 | | |
| 2.部門、事業領域、地域ベース、組織階層を作成します。 | | |
| 3.EA アカウントを作成し、予算を割り当てます。 | | |
| 4.オンプレミスから ID を同期する場合は、Azure AD テナントごとに Azure AD Connect を設定します。 | | |
| 5.Azure リソースへのゼロ スタンディング アクセスと、Azure AD Privileged Identity Management (PIM) によるジャストインタイム アクセスを確立します。 | | |

### <a name="management-group-and-subscription"></a>管理グループとサブスクリプション

| Activities | 必須のパラメーター | エンタープライズ規模の構成例 |
|---|---|---|
| 1.管理グループ階層を作成します (理想的には 3 から 4 レベル以下)。 | | |
| 2.ユーザーが Azure を試すための最上位レベルのサンドボックス管理グループを作成します。 | | |
| 3.サブスクリプションのプロビジョニング基準とサブスクリプション所有者の責任を公開します (wiki 形式の場合もあります)。 | | |
| 4.プラットフォーム管理とグローバル ネットワークおよび接続リソースの管理および接続サブスクリプションを作成します。 | | |
| 5.プラットフォームの継続的インテグレーションと継続的配置パイプラインで使用する Git リポジトリとサービス プリンシパルを設定します。 | | |
| 6.Azure AD PIM を使用してサブスクリプションおよび管理グループ スコープのカスタム ロール定義を作成し、エンタイトルメントを管理します。 | | |

### <a name="global-networking-and-connectivity"></a>グローバル ネットワークと接続性

<!-- markdownlint-disable MD033 -->
<!-- cSpell:ignore vwan vhub -->
<!-- docsTest:ignore "Standard virtual WAN" -->

| Activities | 必須のパラメーター | エンタープライズ規模の構成例 |
|---|---|---|
| 1.Azure Virtual WAN 仮想ハブと仮想ネットワークがデプロイされる各 Azure リージョンに適切な仮想ネットワークのクラスレス ドメイン間ルーティング (CIDR) 範囲を割り当てます。 | リージョンごとに 1 つの CIDR 範囲 | 北ヨーロッパ: `10.0.0.0/16` <br> 西ヨーロッパ: `10.1.0.0/16` <br> 米国東部: `10.2.0.0/16` |
| 2.接続サブスクリプション内に Standard 仮想 WAN を作成します。 | 仮想 WAN の名前 <br> Azure リージョン | 仮想 WAN の名前: `contoso-vwan` <br> Azure リージョン: `North Europe` |
| 3.各リージョンの仮想ハブを作成します。 仮想ハブごとに少なくとも 1 つのゲートウェイ (ExpressRoute または VPN) がデプロイされていることを確認します。 | 仮想 WAN の名前 <br> 仮想ハブの名前 <br> 仮想ハブのリージョン <br> 仮想ハブのアドレス空間 <br> ExpressRoute ゲートウェイ <br> VPN Gateway | Virtual WAN: `contoso-vwan` <br> 仮想ハブのリージョン: `North Europe` <br> 仮想ハブの名前: `vhub-neu` <br> 仮想ハブのアドレス空間: `10.0.0.0/16` <br> ExpressRoute ゲートウェイ: あり (1 スケール ユニット) <br> VPN ゲートウェイ: なし |
| 4.Azure Firewall Manager を使用して、各仮想ハブ内に Azure Firewall をデプロイすることにより、仮想ハブをセキュリティで保護します。                                                        | 仮想ハブの名前                           | 仮想ハブの名前: `vhub-neu`                       |
| 5.接続のサブスクリプション内に必要なファイアウォール ポリシーを作成し、それらをセキュリティで保護された仮想ハブに割り当てます。                                                 | Azure Firewall ポリシーの名前 <br> ファイアウォール ポリシーのインバンドおよびアウトバウンド規則 | ファイアウォール ポリシーの名前: `contoso-global-fw-policy` <br> `*.microsoft.com` に対してアウトバウンド規則を許可します  |
| 6.Azure Firewall Manager を使用して、セキュリティで保護された仮想ハブに接続されているすべての仮想ネットワークが Azure Firewall によって保護されていることを確認します。                                                | 仮想ハブの名前 <br> インターネット トラフィック - 仮想ネットワークからのトラフィック | 仮想ハブの名前: `vhub-neu` <br> インターネット トラフィック - 仮想ネットワークからのトラフィック - Azure Firewall 経由で送信 |
| 7.グローバル接続サブスクリプション内に Azure プライベート DNS ゾーンをデプロイして構成します。                                                             | プライベート DNS ゾーンの名前               | プライベート DNS ゾーンの名前: `azure.contoso.com` |
| 8.プライベート ピアリングを使用して ExpressRoute 回線をプロビジョニングします。                                                                                                 | [記事の指示に従ってください](https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager#private) | [記事の指示に従ってください](https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager#private) |
| 9.ExpressRoute 回線を介してオンプレミスの HQ および DC を Azure Virtual Hub に接続します。                                                                                | 承認キー <br> 仮想ハブの名前 | 承認キー: `xxxxxxxx` <br> 仮想ハブ: `vhub-neu` |
| 10.(省略可能):ExpressRoute プライベート ピアリングで暗号化を設定します。                                                                                         | [記事の指示に従ってください](https://docs.microsoft.com/azure/virtual-wan/vpn-over-expressroute) | [記事の指示に従ってください](https://docs.microsoft.com/azure/virtual-wan/vpn-over-expressroute) |
| 11.(省略可能):VPN 経由でブランチを仮想ハブに接続します。                                                                                                | [記事の指示に従ってください](https://docs.microsoft.com/azure/virtual-wan/virtual-wan-site-to-site-portal) | [記事の指示に従ってください](https://docs.microsoft.com/azure/virtual-wan/virtual-wan-site-to-site-portal) |
| 12.NSG を使用して、仮想ハブ間の仮想ネットワーク トラフィックを保護します。                                                                                                             | 受信の規則 <br> アウトバウンド規則 | 受信の規則 <br> アウトバウンド規則 |
| 13.ExpressRoute を介して Azure に複数のオンプレミスの場所が接続されている場合に、オンプレミス HQ および DC に接続するように ExpressRoute Global Reach を構成します。 | [記事の指示に従ってください](https://docs.microsoft.com/azure/expressroute/expressroute-howto-set-global-reach) | [記事の指示に従ってください](https://docs.microsoft.com/azure/expressroute/expressroute-howto-set-global-reach) |

<!-- markdownlint-enable MD033 -->

### <a name="security-governance-and-compliance"></a>セキュリティ、ガバナンス、およびコンプライアンス

| Activities | 必須のパラメーター | エンタープライズ規模の構成例 |
|---|---|---|
| 1.サービスのホワイトリスト登録フレームワークを定義および適用して、Azure サービスがエンタープライズのセキュリティおよびガバナンス要件を満たすようにします (付録を参照してください)。 | | |
| 2.カスタムのロールベースのアクセス制御 (RBAC) ロール定義を作成します。 | | |
| 3.PIM を有効にし、Azure リソースを検出して PIM を容易にします。 | | |
| 4.PIM を使用してリソースの Azure コントロール プレーン管理用の Azure AD 専用グループを作成します。 | | |
| 5.Azure サービスがエンタープライズ要件に確実に準拠するように、Azure Policy を適用します。 | | |
| 6.名前付け規則を定義し、Azure Policy を介して適用します。 | | |
| 7.すべてのスコープでポリシー マトリックスを作成します (たとえば、すべての Azure サービスの監視を有効にします)。 | | |
| 8.ネットワーク、セキュリティ、監視に関連する Azure ポリシーを適用します (後述するポリシー一覧の例を参照してください)。 | | |

### <a name="platform-management-and-monitoring"></a>プラットフォームの管理と監視

| Activities | 必須のパラメーター | エンタープライズ規模の構成例 |
|---|---|---|
| 1.組織およびリソース中心のビューのためのポリシー コンプライアンスとセキュリティ ダッシュボードを作成します。 | | |
| 2.プラットフォーム シークレット (サービス プリンシパルと Automation アカウント) とキー ロールオーバーのワークフローを作成します。 | | |
| 3.(省略可能:)組織全体の仮想マシン (VM) ギャラリー イメージを設定します。 | | |
| 4.Azure Monitor ログ内のログの長期的なアーカイブと保持を設定します。 | | |
| 5.プラットフォーム シークレットの格納に使用されるキー コンテナーの事業継続とディザスター リカバリーを設定します。 | | |
| 6.Azure Firewall Manager を使用して、セキュリティで保護された仮想ハブに接続されているすべての仮想ネットワークが Azure Firewall によって保護されていることを確認します。 | | |

管理グループのスコープで一般的なネットワーク、セキュリティ、および監視の制御を実施するための Azure ポリシーの例を次の表に示します。

| カテゴリ   | ポリシー | |------------|--------------------------------------------------------------------------------------------------_| | ネットワーク    | 1. (プレビュー:)Container Registry には仮想ネットワーク サービス エンドポイントを使用する必要があります。                   | |            | 2. カスタム IP セキュリティおよび インターネット キー交換ポリシーをすべての Azure Virtual Network ゲートウェイ接続に適用する必要があります。 | |            | 3. App Service は仮想ネットワーク サービス エンドポイントを使用する必要があります (内部アプリのみ)。                | |            | 4. Azure VPN ゲートウェイで SKU を使用しないでください。                            | |            | 5. Azure Cosmos DB は仮想ネットワーク サービス エンドポイントを使用する必要があります。                                 | |            | 6. 仮想ネットワーク作成時に Network Watcher をデプロイします。                                      | |            | 7. イベント ハブは仮想ネットワーク サービス エンドポイントを使用する必要があります。                                       | |            | 8. ゲートウェイ サブネットをネットワーク セキュリティ グループで構成しないでください。                        | |            | 9. Key Vault は仮想ネットワーク サービス エンドポイントを使用する必要があります。                                       | |            | 10. ネットワーク インターフェイスで IP 転送を無効にする必要があります。                                              | |            | 11. Service Bus は仮想ネットワーク サービス エンドポイントを使用する必要があります。                                    | |            | 12. SQL Server は仮想ネットワーク サービス エンドポイントを使用する必要があります。                                     | |            | 13. ストレージ アカウントは仮想ネットワーク サービス エンドポイントを使用する必要があります。                               | |            | 14. サブネットはネットワーク セキュリティ グループに関連付けられている必要があります。                                   | |            | 15. 保護されていないネットワーク エンドポイントを Azure Security Center で監視します。                          | | セキュリティ   | 1. [プレビュー]:Microsoft Monitoring Agent デプロイの監査 - 一覧にない VM イメージ (OS)。                         | |            | 2. [プレビュー]:VMSS の Microsoft Monitoring Agent デプロイの監査 - 一覧にない VM イメージ (OS)。                 | |            | 3. [プレビュー]:Azure Monitor Log Analytics エージェントのデプロイの監査 - 一覧にない VM イメージ (OS)。                      | |            | 4. [プレビュー]:VMSS での Azure Monitor Log Analytics エージェントのデプロイの監査 - 一覧にない VM イメージ (OS)。              | |            | 5. [プレビュー]:VM の Azure Monitor Log Analytics ワークスペースの監査 - レポートの不一致。                             | |            | 6. [プレビュー]:Linux VM 用の Microsoft Dependency Agent をデプロイします。                                              | |            | 7. [プレビュー]:Windows VM 用の Microsoft Dependency Agent をデプロイします。                                            | |            | 8. [プレビュー]:Linux VM 用の Azure Monitor Log Analytics エージェントをデプロイします。                                           | |            | 9. [プレビュー]:Windows VM 用の Azure Monitor Log Analytics エージェントをデプロイします。                                         | |            | 10. アクティビティ ログを 1 年以上保持する必要があります。                                        | |            | 11. 診断設定を監査します。                                                                     | |            | 12. Azure Monitor ログ プロファイルで、カテゴリ `write`、`delete`、および `action` のログを収集する必要があります。 | |            | 13. Azure Monitor ですべてのリージョンからアクティビティ ログを収集する必要があります。                                  | |            | 14. Azure Monitor ソリューション "Security and Audit" をデプロイする必要があります。                                 | |            | 15. Azure サブスクリプションにはアクティビティ ログのログ プロファイルが必要です。                               | |            | 16. Batch アカウントの診断設定をイベント ハブにデプロイします。                                    | |            | 17. バッチ アカウントの診断設定を Azure Monitor Log Analytics ワークスペースにデプロイします。                      | |            | 18. Data Lake Analytics の診断設定をイベント ハブにデプロイします。                              | |            | 19. Data Lake Analytics の診断設定を Azure Monitor Log Analytics ワークスペースにデプロイします。                | |            | 20. Data Lake Storage gen1 の診断設定をイベント ハブにデプロイします。                           | |            | 21. Data Lake Storage gen1 の診断設定を Azure Monitor Log Analytics ワークスペースにデプロイします。             | |            | 22. イベント ハブの診断設定をイベント ハブにデプロイします。                                        | |            | 23. イベント ハブの診断設定を Azure Monitor Log Analytics ワークスペースにデプロイします。                          | |            | 24. Key Vault の診断設定を Azure Monitor Log Analytics ワークスペースにデプロイします。                          | |            | 25. Logic Apps の診断設定をイベント ハブにデプロイします。                                       | |            | 26. Logic Apps の診断設定を Azure Monitor Log Analytics ワークスペースにデプロイします。                         | |            | 27. ネットワーク セキュリティ グループの診断設定をデプロイします。                                       | |            | 28. 検索サービスの診断設定をイベント ハブにデプロイします。                                  | |            | 29. 検索サービスの診断設定を Azure Monitor Log Analytics ワークスペースにデプロイします。                    | |            | 30. Service Bus の診断設定をイベント ハブにデプロイします。                                      | |            | 31. Service Bus の診断設定を Azure Monitor Log Analytics ワークスペースにデプロイします。                        | |            | 32. ストリーム分析の診断設定をイベント ハブにデプロイします。                                 | |            | 33. ストリーム分析の診断設定を Azure Monitor Log Analytics ワークスペースにデプロイします。                   | |            | 34. Azure Monitor Log Analytics エージェントは、VM スケール セットにインストールする必要があります。                    | |            | 35. Azure Monitor Log Analytics エージェントは、VM にインストールする必要があります。                              | | 監視 | 36. [プレビュー]:Microsoft Dependency Agent のデプロイの監査 - 一覧にない VM イメージ (OS)。                        | |            | 37. [プレビュー]:VMSS の Microsoft Dependency Agent デプロイの監査 - 一覧にない VM イメージ (OS)。                | |            | 38. [プレビュー]:Azure Monitor Log Analytics エージェントのデプロイの監査 - 一覧にない VM イメージ (OS)。                     | |            | 39. [プレビュー]:VMSS での Azure Monitor Log Analytics エージェントのデプロイの監査 - 一覧にない VM イメージ (OS)。             | |            | 40. [プレビュー]:VM の Azure Monitor Log Analytics ワークスペースの監査 - レポートの不一致。                            | |            | 41. [プレビュー]:Linux VM 用の Microsoft Dependency Agent をデプロイします。                                             | |            | 42. [プレビュー]:Windows VM 用の Microsoft Dependency Agent をデプロイします。                                           | |            | 43. [プレビュー]:Linux VM 用の Azure Monitor Log Analytics エージェントをデプロイします。                                          | |            | 44. [プレビュー]:Windows VM 用の Azure Monitor Log Analytics エージェントをデプロイします。                                        | |            | 45. アクティビティ ログを 1 年以上保持する必要があります。                                        | |            | 46. 診断設定を監査します。                                                                     | |            | 47. Azure Monitor ログ プロファイルで、カテゴリ `write`、`delete`、および `action` のログを収集する必要があります。 | |            | 48. Azure Monitor ですべてのリージョンからアクティビティ ログを収集する必要があります。                                  | |            | 49. Azure Monitor ソリューション `security and audit` をデプロイする必要があります。                                 | |            | 50. Azure サブスクリプションにはアクティビティ ログのログ プロファイルが必要です。                               | |            | 51. Batch アカウントの診断設定をイベント ハブにデプロイします。                                    | |            | 52. バッチ アカウントの診断設定を Azure Monitor Log Analytics ワークスペースにデプロイします。                      | |            | 53. Data Lake Analytics の診断設定をイベント ハブにデプロイします。                              | |            | 54. Data Lake Analytics の診断設定を Azure Monitor Log Analytics ワークスペースにデプロイします。                | |            | 55. Data Lake Storage gen1 の診断設定をイベント ハブにデプロイします。                           | |            | 56. Data Lake Storage gen1 の診断設定を Azure Monitor Log Analytics ワークスペースにデプロイします。             | |            | 57. イベント ハブの診断設定をイベント ハブにデプロイします。                                        | |            | 58. イベント ハブの診断設定を Azure Monitor Log Analytics ワークスペースにデプロイします。                          | |            | 59. Key Vault の診断設定を Azure Monitor Log Analytics ワークスペースにデプロイします。                          | |            | 60. Logic Apps の診断設定をイベント ハブにデプロイします。                                       | |            | 61. Logic Apps の診断設定を Azure Monitor Log Analytics ワークスペースにデプロイします。                         | |            | 62. ネットワーク セキュリティ グループの診断設定をデプロイします。                                       | |            | 63. 検索サービスの診断設定をイベント ハブにデプロイします。                                  | |            | 64. 検索サービスの診断設定を Azure Monitor Log Analytics ワークスペースにデプロイします。                    | |            | 65. Service Bus の診断設定をイベント ハブにデプロイします。                                      | |            | 66. Service Bus の診断設定を Azure Monitor Log Analytics ワークスペースにデプロイします。                        | |            | 67. ストリーム分析の診断設定をイベント ハブにデプロイします。                                 | |            | 68. ストリーム分析の診断設定を Azure Monitor Log Analytics ワークスペースにデプロイします。                   | |            | 69. Azure Monitor Log Analytics エージェントは、VM スケール セットにインストールする必要があります。                    | |            | 70. Azure Monitor Log Analytics エージェントは、VM にインストールする必要があります。                              | | Key Vault  | 71. [プレビュー]:許可されている証明書キーの種類を管理します。                                              | |            | 72. [プレビュー]:楕円曲線暗号証明書で許可されている曲線名を管理します。           | |            | 73. [プレビュー]:証明書有効期間のアクション トリガーを管理します。                                       | |            | 74. [プレビュー]:証明書の有効期間を管理します。                                                | |            | 75. [プレビュー]:統合されていない証明機関から発行された証明書を管理します。                                 | |            | 76. [プレビュー]:統合された証明機関から発行された証明書を管理します。                                    | |            | 77. [プレビュー]:有効期限までの期間が指定された日数内である証明書を管理します。      | |            | 78. [プレビュー]:RSA 証明書の最小キー サイズを管理します。                                      | |            | 79. Key Vault の診断設定をイベント ハブにデプロイします。                                        | |            | 80. Key Vault で診断ログを有効にする必要があります。                                               | |            | 81. Key Vault の論理的な削除を有効にします。                                                             |

## <a name="file--gt-new--gt-region-and-file--gt-new--gt-landing-zone"></a>**[ファイル] -&gt; [新規] -&gt; [リージョン]** と **[ファイル] -&gt; [新規] -&gt; [Landing zone]\(ランディング ゾーン\)**

### <a name="file--gt-new--gt-region"></a>[ファイル] -&gt; [新規] -&gt; [リージョン]

1. 接続サブスクリプション内で、既存の Azure Virtual WAN 内に新しい仮想ハブを作成します。

2. Azure Firewall を仮想ハブ内にデプロイして、Azure Firewall Manager を使用して仮想ハブをセキュリティで保護し、既存または新規のファイアウォール ポリシーを Azure Firewall にリンクします。

3. Azure Firewall Manager を使用して、セキュリティで保護された仮想ハブに接続されているすべての仮想ネットワークが Azure Firewall によって保護されていることを確認します。

4. ExpressRoute を使用して、または VPN 経由で仮想ハブをオンプレミスに接続します。

5. (省略可能:)ExpressRoute プライベート ピアリングで暗号化を設定します。

6. ネットワーク セキュリティ グループを使用して、仮想ハブ間の仮想ネットワーク トラフィックを保護します。

### <a name="file--gt-new--gt-landing-zone-for-applications-and-workloads"></a>アプリケーションとワークロードの場合、[ファイル] -&gt; [新規] -&gt; [Landing zone]\(ランディング ゾーン\)

1. サブスクリプションを作成し、要求元をサブスクリプションの所有者として割り当てます。

2. 管理グループ階層内でサブスクリプションを割り当てます。

3. 予算とアラート通知を設定します。

4. セキュリティ連絡先のメール アドレスと電話番号を設定します。

5. サブスクリプション (n) の所有者、閲覧者、共同作成者などの Azure AD グループを作成します。

6. 確立された Azure AD グループの Azure AD PIM のエンタイトルメントを作成します。

7. 仮想ネットワーク接続が必要な場合は、仮想ネットワーク CIDR 範囲をプロビジョニングします。

8. 仮想ネットワークをリージョンの仮想ハブとピアリングします。

9. 必要な共有サービス (ドメイン コントローラーなど) をプロビジョニングします。

10. 組織で定義されたポリシー マトリックスの指定に従って、サブスクリプション スコープに必要なポリシーを割り当てます。

\* サンドボックス サブスクリプションの場合は省略可能。

サブスクリプション スコープで一般的なエンタープライズ コントロールを適用する Azure ポリシーの例を次の表に示します。

| カテゴリ          | ポリシー                                                                                                 |
|-------------------|--------------------------------------------------------------------------------------------------------|
| リソースとタグ | 1.許可される場所                                                                                   |
|                   | 2.リソース グループが許可される場所                                                               |
|                   | 3.許可されるリソースの種類                                                                              |
|                   | 4.カスタム RBAC 規則の使用監査                                                                    |
|                   | 5.カスタム サブスクリプションの所有者ロールが作成されていないこと                                                    |
|                   | 6.許可されないリソースの種類                                                                          |
|                   | 7.リソース グループにタグを追加する                                                                        |
|                   | 8.タグをリソースに追加する                                                                              |
|                   | 9.タグが存在しない場合は、リソース グループからタグを継承する                                                    |
|                   | 10.タグとその値が必要                                                                          |
|                   | 11.タグとその値がリソース グループに必要                                                       |
| Compute           | 12.許可される VM SKU                                                                       |
|                   | 13.ディザスター リカバリーが構成されていない VM を監査する                                        |
|                   | 14. マネージド ディスクを使用していない VM の監査                                                            |
|                   | 15. Windows Server の既定の Microsoft サービスとしてのインフラストラクチャ (IaaS) マルウェア対策拡張機能を展開する                              |
|                   | 16. VM スケール セットの診断ログを有効にする必要がある                                    |
|                   | 17. Azure 向け Microsoft Antimalware は保護定義を自動的に更新するように構成する必要がある |
|                   | 18. Windows 用の Microsoft Antimalware 拡張機能を Windows サーバーに展開する必要があります                          |
|                   | 19. 承認済みの VM 拡張機能のみがインストールされている必要がある                                                    |
|                   | 20. VM スケール セット上の OS イメージの自動修正が必要                                  |
|                   | 21. アタッチされていないディスクを暗号化する必要がある                                                               |
|                   | 22.ネットワーク インターフェイスにはパブリック IP を設定できない                                                      |
|                   | 23.VM は、承認された仮想ネットワークに接続する必要がある                                |
|                   | 24.仮想ネットワークは、指定された仮想ネットワーク ゲートウェイを使用する必要があります                                      |
