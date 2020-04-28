---
title: CISO のクラウド セキュリティの準備に関するガイド
description: クラウド変換と増分ガバナンスのために最高情報セキュリティ オフィス (CISO) を準備する方法について説明します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 09/17/2019
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: govern
ms.openlocfilehash: 84bb47e05086ba0921a189d96d20c76a1ab5b057
ms.sourcegitcommit: 825f9ae5b6cdd2fa6cb18c14a9733ba9106194f2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/21/2020
ms.locfileid: "81646822"
---
<!-- cSpell:ignore CISO -->

# <a name="ciso-cloud-readiness-guide"></a>CISO のクラウド準備状況に関するガイド

クラウド導入フレームワークなどの Microsoftによるガイダンスは、このドキュメントのサポート対象である何千社もの企業に特有のセキュリティ制約を、特定したり左右したりするように位置付けられていません。 クラウドに移行する場合、最高情報セキュリティ責任者や最高情報セキュリティ オフィス (CISO) の役割は、クラウド テクノロジに取って代わられるものではありません。 まったく逆に、CISO と CISO オフィスは、よりしっかりと根付いた、統合された存在となります。 このガイドは、読者が CISO のプロセスに精通しており、クラウド変革を実現するためにそれらのプロセスを最新化しようとしていることを前提としています。

クラウドの導入により、従来の IT 環境ではあまり検討されなかったサービスが実現します。 セルフサービスのデプロイや自動化されたデプロイは、一般に、従来は運用環境のデプロイには結び付けられていなかったアプリケーション開発チームやその他の IT チームによって実行されます。 組織によっては、事業の構成単位が同様に、セルフ サービスの機能を持つことになります。 これは、オンプレミス環境では必要でなかった、新しいセキュリティ要件が生じるきっかけとなる可能性があります。 セキュリティの一元化はより困難であり、セキュリティは多くの場合、事業部門と IT 部門の間で責任を共有することになります。 この記事は、そうしたアプローチに向けた CISO の準備と、増分ガバナンスへの取り組みの助けになります。

<!-- markdownlint-disable MD026 -->

## <a name="how-can-a-ciso-prepare-for-the-cloud"></a>CISO がクラウドの準備をする方法

ほとんどのポリシーと同様に、組織内のセキュリティとガバナンスのポリシーは自然に拡大する傾向があります。 セキュリティ インシデントの発生時には、ユーザーへの通知を行い、繰り返しが起きる可能性を減らすためのポリシーがまとめられます。 自然ではあっても、このアプローチでは、ポリシーの肥大化と技術的な依存関係が生じます。 クラウド変革の体験では、ポリシーを最新化してリセットする独自の機会を作ります。 どの変革体験を準備しているときでも、CISO は[ポリシー レビュー](./cloud-policy-review.md)で主要な利害関係者としての役割を果たすことで、即時の測定可能な価値を作り出すことができます。

そのようなレビューにおける CISO の役割は、既存のポリシーやコンプライアンスの制約と、クラウド プロバイダーの改善されたセキュリティ体制の間で、あぶなげないバランスを生み出すことです。 この進行状況はさまざまな形式で測定される場合があり、多くの場合、クラウド プロバイダーに安全に移管できるセキュリティ ポリシーの数で測定されます。

**セキュリティ リスクの移転:** サービスは、サービスとしてのインフラストラクチャ (IaaS) ホスティング モデルに移行されるため、ハードウェアのプロビジョニングに関して企業が引き受ける直接的なリスクは減少します。 リスクは解消されたのではなく、クラウド ベンダーに移転されました。 ハードウェアのプロビジョニングに対するクラウド ベンダーのアプローチによって、セキュリティで保護された繰り返し可能なプロセスで、同じレベルのリスク軽減が提供されるのであれば、ハードウェア プロビジョニングの実行リスクは企業 IT の担当領域から取り除かれ、クラウド プロバイダーに移転されます。 これにより、企業の IT が管理する責任がある全体的なセキュリティ リスクが軽減されますが、リスク自体は引き続き追跡し、定期的に確認する必要があります。

ソリューションがさらに "スタックを上に" 移動して、サービスとしてのプラットフォーム (PaaS) やサービスとしてのソフトウェア (SaaS) モデルが組み込まれると、追加のリスクの回避や移転が可能になります。 リスクが安全にクラウド プロバイダーに移されると、セキュリティ ポリシーやその他のコンプライアンス ポリシーを実行、監視、および適用するコストも安全に削減できます。

**成長の思考様式:** 変更はビジネスの実装者にとっても技術の実装者にとっても恐れられることがあります。 CISO が組織における成長の思考様式を先導している場合、そうした自然なおそれは、安全性とポリシー コンプライアンスへの関心の高まりで置き換えられることがわかっています。 成長の思考様式で、変革体験の 1 つである[ポリシー レビュー](./cloud-policy-review.md)や単純な実装レビューに取り掛かると、チームはすばやく動くことができますが、適正で管理しやすいリスク プロファイルを犠牲にすることはありません。

## <a name="resources-for-the-chief-information-security-officer"></a>最高情報セキュリティ責任者のためのリソース

成長の思考様式で[ポリシー レビュー](./cloud-policy-review.md)に取り掛かるためには、クラウドに関する知識が必須です。 以下のリソースは、CISO が Microsoft の Azure プラットフォームのセキュリティ体制をより深く理解するのに役立ちます。

**セキュリティ プラットフォームのリソース:**

- [セキュリティ開発ライフサイクル、内部監査](https://www.microsoft.com/sdl)
- [必須のセキュリティ トレーニング、バックグラウンド チェック](https://downloads.cloudsecurityalliance.org/star/self-assessment/StandardResponsetoRequestforInformationWindowsAzureSecurityPrivacy.docx)
- [侵入テスト、不正侵入検出、DDoS、監査、ログ記録](https://www.microsoft.com/security/business/operations)
- [最新のデータ センター](https://www.microsoft.com/cloud-platform/global-datacenters)、物理的なセキュリティ、[セキュリティで保護されたネットワーク](https://docs.microsoft.com/azure/security/security-network-overview)
- [クラウドにおける Microsoft Azure のセキュリティ対応に関する資料 (PDF)](https://aka.ms/securityresponsepaper)

**プライバシーと管理:**

- [データの常時管理](https://www.microsoft.com/trust-center/privacy/data-management)
- [データ保管場所の管理](https://www.microsoft.com/trust-center/privacy/data-location)
- [条件に応じたアクセス権の付与](https://www.microsoft.com/trust-center/privacy/data-access)
- [法執行機関への対応](https://www.microsoft.com/trust-center/privacy)
- [厳格なプライバシー基準](https://www.microsoft.com/trust-center/privacy)

**コンプライアンス:**

- [Microsoft セキュリティ センター](https://www.microsoft.com/trust-center)
- [共通管理ハブ](https://www.microsoft.com/trust-center/compliance/compliance-overview)
- [クラウド サービス デュー デリジェンス チェック リスト](https://www.microsoft.com/trust-center/compliance/due-diligence-checklist)
- [地域と国のコンプライアンス](https://www.microsoft.com/trust-center/compliance/regional-country-compliance)

**透明性:**

- [Microsoft が Azure サービスで顧客データの安全性を確保する方法](https://www.microsoft.com/trust-center)
- [Microsoft が Azure サービスでデータの保管場所を管理する方法](https://azuredatacentermap.azurewebsites.net)
- [データにアクセスできるユーザーとその条件](https://www.microsoft.com/trust-center/privacy/data-access)
- [Azure サービスの証明書確認、Transparency Hub](https://www.microsoft.com/trust-center/compliance/compliance-overview)

## <a name="next-steps"></a>次のステップ

どのガバナンス戦略においても、行動を起こす最初の手順は[ポリシー レビュー](./cloud-policy-review.md)です。 [ポリシーとコンプライアンス](./index.md)は、ポリシー レビュー時に役立つガイドになる可能性があります。

> [!div class="nextstepaction"]
> [ポリシー レビューを準備する](./cloud-policy-review.md)
