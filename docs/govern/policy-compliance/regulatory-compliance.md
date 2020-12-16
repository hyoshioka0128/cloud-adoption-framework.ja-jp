---
title: 規制コンプライアンスの概要
description: クラウド ガバナンスに影響を与える可能性がある、さまざまな業界や地域におけるコンプライアンス規制について説明します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 09/17/2019
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: govern
ms.custom: internal
ms.openlocfilehash: ca690c8137a8059febbd8f444f7397ad061c650a
ms.sourcegitcommit: b6f2b4f8db6c3b1157299ece1f044cff56895919
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2020
ms.locfileid: "97019657"
---
# <a name="introduction-to-regulatory-compliance"></a>規制コンプライアンスの概要

これは規制コンプライアンスについての入門記事であり、したがってコンプライアンス戦略の実装については触れられていません。 [Azure のコンプライアンス認証](/microsoft-365/compliance/offering-home)の詳細については、[Microsoft Trust Center](https://www.microsoft.com/trust-center) をご覧ください。 さらに、Azure の特定のお客様は、[Microsoft Service Trust Portal](https://servicetrust.microsoft.com) からすべてのダウンロード可能なドキュメントを入手できます。

規制コンプライアンスとは、地域の行政機関によって施行される法律、または自主的に採択された業界標準で要求されている規則に企業が従うことを保証するための規範とプロセスのことです。 IT 規制コンプライアンスの場合、ユーザーやプロセスが企業システムを監視し、これらの準拠法、規制、標準によって確立されたポリシーや手順への違反を検出して防ぎます。 これはさらに、幅広い監視と強制のプロセスに適用されます。 業界や地域によっては、長くて複雑なプロセスになることがあります。

多国籍組織 (特に、ヘルスケアや金融サービスなどの規制の厳しい業界) では、コンプライアンスが困難です。 標準と規制は数多く存在し、頻繁に変更されることもあるため、企業が変わり続ける国際電子データ処理法に対応するのは困難です。

セキュリティ管理と同様、組織はクラウドでの規制コンプライアンスに関する責任の分担を理解する必要があります。 クラウド プロバイダーは、そのプラットフォームとサービスを準拠させることに努めています。 また、組織も提供するアプリケーション、それらのアプリケーションが依存するインフラストラクチャ、サード パーティによって提供されるサービスが準拠の認定を受けていることを確認する必要があります。

以下では、さまざまな業界や地域でのコンプライアンスに関する規制について説明します。

<!-- docutune:casing PHI "Health Information Portability and Accountability Act" -->

## <a name="hipaa"></a>HIPAA

保護医療情報 (PHI) を処理する医療アプリケーションは、Health Information Portability and Accountability Act (HIPAA) に含まれるプライバシー規則とセキュリティ規則の両方の対象となっています。 少なくとも、HIPAA では、受け取るか作成された PHI が保護されていることを保証する書面を、医療機関がクラウド プロバイダーから受け取ることが要求されている場合があります。

<!-- docutune:ignore Discover -->
<!-- cSpell:ignore Visa Mastercard -->

## <a name="pci"></a>PCI

Payment Card Industry Data Security Standard (PCI DSS) は、Visa、MasterCard、American Express、Discover、JCB などの主要なカード支払いシステムからのブランド付きクレジット カードを処理する組織に向けた、機密情報セキュリティ標準です。 PCI 標準はカード ブランドによって要求されており、Payment Card Industry Security Standards Council によって管理されています。 この標準は、カード所有者データに関する管理を強化してクレジット カード不正を減らすために作成されました。 コンプライアンスの検証は、大量のトランザクションを処理する組織のために準拠報告 (ROC) を作成する外部の認定セキュリティ評価機関 (QSA) や企業固有の内部セキュリティ評価部門 (ISA) によって、または企業用の自己評価アンケート (SAQ) によって毎年実行されます。

## <a name="personal-data"></a>個人データ

個人データは、コンシューマー、従業員、パートナー、またはその他の個人や法人を識別するために使用できる情報です。 多くの新たな法律 (特にプライバシーや個人データに関するもの) では、企業自体に対してコンプライアンスに準拠し、侵害が発生した場合は報告することが義務付けられています。

## <a name="gdpr"></a>GDPR

この分野で最も重要な開発の 1 つは、欧州連合内の個人のデータ保護を強化するように設計された一般データ保護規則 (GDPR) です。 GDPR では、個人に関するデータ ("名前、自宅の住所、写真、メール アドレス、銀行の詳細、ソーシャル ネットワーキング Web サイトへの投稿、医療情報、コンピューターの IP アドレス" など) を、EU 内のサーバーの保持し、EU 外に転送してはならないことが、要求されています。 また、企業に対し、データ侵害があった場合は個人に通知することと、データ保護担当者 (DPO) を置くことが、要求されています。 他の国でも、同様の規制が制定済みまたは制定中です。

## <a name="compliant-foundation-in-azure"></a>Azure での準拠の基盤

規制の対象となる業界や世界中の市場にわたり、お客様各自の遵守義務を満たす助けとなるように、Azure では、その幅の広さ (認証の総数) と深さ (評価範囲に含まれる顧客向けサービスの数) において、業界最大のコンプライアンス ポートフォリオを保持しています。 Azure のコンプライアンス認証は、以下の 4 つのセグメントにグループ化されています。

- グローバル
- 米国政府
- 業界
- 地域

Azure のコンプライアンス認証は、独立したサードパーティの監査企業による正式な認証、証明、検証、承認、および評価の他に、Microsoft による契約の修正、自己評価、顧客向けのガイダンス ドキュメントを含むさまざまな種類の保証に基づいています。 このドキュメントでの各認証の説明では、評価の対象になる顧客向けのサービス、および顧客による独自のコンプライアンスへの順守を支援するダウンロード可能なリソースへのリンクを示す、最新の範囲のステートメントが提供されています。

Microsoft Trust Center では、[Azure のコンプライアンス認証の詳細](https://www.microsoft.com/trust-center/compliance/compliance-overview)をご確認いただけます。 さらに、Azure の特定のお客様は [Service Trust Portal](https://servicetrust.microsoft.com) から、以下に示す区分のすべてのダウンロード可能なドキュメントを入手できます。

- **監査レポート:** FedRAMP、GRC 評価、ISO、PCI DSS、および SOC レポートのセクションが含まれます。
- **データ保護リソース:** コンプライアンス ガイド、FAQ とホワイト ペーパー、および侵入テストとセキュリティ評価のセクションが含まれます。

## <a name="next-steps"></a>次のステップ

クラウド セキュリティの準備の詳細を確認します。

> [!div class="nextstepaction"]
> [クラウド セキュリティの準備](./cloud-security-readiness.md)
