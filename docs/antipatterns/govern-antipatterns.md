---
title: ガバナンスのアンチパターン
description: クラウドを導入する場合には、企業の責任、クラウド プロバイダーの責任、クラウドのガバナンスとセキュリティの標準について理解してください。
author: sarahwendel
ms.author: brblanch
ms.date: 02/18/2021
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: govern
ms.custom: think-tank
ms.openlocfilehash: cafd35c3353e9cb54bd8d57d938e96c3044cb9f0
ms.sourcegitcommit: 9e4bc0e233a24642853f5e8acbeb9746b2444024
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102117516"
---
# <a name="govern-antipatterns"></a>ガバナンスのアンチパターン

多くの場合、お客様はクラウド導入のガバナンス フェーズでアンチパターンを経験します。 時間をかけて共同責任を理解することで、このようなアンチパターンを回避できます。セキュリティ戦略を、自力で作成するのではなく、既存のフレームワーク上に構築する場合と同様です。

## <a name="antipattern-misunderstand-shared-responsibilities"></a>アンチパターン: 共同責任を誤解している

クラウドを導入する場合、さまざまなサービス モデルに関して、お客様の責任がどこで終わり、クラウド プロバイダーの責任がどこから始まるかが常に明確であるとは限りません。 サービス モデルを使用する作業項目に関するプロセスと手法を構築するには、クラウドのスキルと知識が必要です。

### <a name="example-assume-the-cloud-provider-manages-updates"></a>例: クラウド プロバイダーが更新プログラムを管理している場合

会社の人事 (HR) 部門のメンバーは、サービスとしてのインフラストラクチャ (IaaS) を使用してクラウドに多くの Windows サーバーを設定しました。 彼らは、クラウド プロバイダーが更新プログラムを管理すると想定しています。通常、オンサイトの IT 担当者が更新プログラムのインストールを処理するからです。 HR 部門では、Azure の既定ではオペレーティング システムの更新プログラムの展開とインストールが行われないことをわかっていないため、更新プログラムを構成していません。 その結果、サーバーは非準拠になり、セキュリティ上のリスクが生じます。

### <a name="preferred-outcome-create-a-readiness-plan"></a>推奨される結果: 準備計画を作成する

クラウドにおける[共同責任](/azure/security/fundamentals/shared-responsibility)について理解します。 [準備計画](../plan/adapt-roles-skills-processes.md)を構築します。 準備計画によって、専門知識を学び、深めるための継続的な機運を生み出すことができます。

## <a name="antipattern-assume-out-of-the-box-solutions-provide-security"></a>アンチパターン: すぐに使用できるソリューションによりセキュリティが提供されると想定する

多くの場合、企業は、セキュリティをクラウドに備わっているものであると認識しています。 この想定は通常は正しいものですが、ほとんどの環境は、コンプライアンス フレームワークの要件にも準拠する必要があります。これはセキュリティ要件とは異なる場合があります。 Azure には基本的なセキュリティが備わっています。 また、[Azure Security Center](/azure/security-center/) を使用する [Azure portal](https://portal.azure.com) には、セキュリティの向上に役立つ機能があります。 ただし、サブスクリプションを作成するときに、コンプライアンスとセキュリティの標準を適用しても、すぐに使用できるようにはなりません。

### <a name="example-neglect-cloud-security"></a>例: クラウド セキュリティを軽視する

ある企業では、クラウドで新しいアプリケーションを開発しています。 多くの PaaS (サービスとしてのプラットフォーム) サービスと、デバッグ目的でいくつかの IaaS コンポーネントに基づいてアーキテクチャを選択しています。 アプリケーションを運用環境にリリースした後、ジャンプ サーバーの 1 つが侵害され、不明な IP アドレスにデータを抽出されていたことに気付きました。 ジャンプ サーバーのパブリック IP アドレスと推測しやすいパスワードが問題であることがわかりました。 クラウド セキュリティに重点を置いていれば、この状況を回避できたはずです。

### <a name="preferred-outcome-define-a-cloud-security-strategy"></a>推奨される結果: クラウド セキュリティ戦略を定義する

適切なクラウド [セキュリティ戦略](../strategy/define-security-strategy.md)を定義します。 詳細については、「[CISO のクラウド準備状況に関するガイド](../govern/policy-compliance/cloud-security-readiness.md)」を参照してください。また、最高情報セキュリティ責任者 (CISO) にこのガイドを紹介してください。 セキュリティ プラットフォームのリソース、プライバシーと制御、コンプライアンス、透明性などのトピックについて説明されています。

セキュリティで保護されたクラウド ワークロードについては、[Azure セキュリティ ベンチマーク](/azure/security/benchmarks/introduction)に関するページを参照してください。 [Center for Internet Security (CIS) Controls Version 7.1](https://learn.cisecurity.org/cis-controls-download) と [National Institute of Standards and Technology (NIST) SP800-53 Framework](https://www.nist.gov/privacy-framework/nist-sp-800-53) に基づいて構築されており、ほとんどのセキュリティ リスクと対策に対応しています。

Azure Security Center を使用して、リスクを特定し、ベスト プラクティスを適応させ、企業のセキュリティ体制を改善してください。

[Azure Policy](/azure/governance/policy/overview) と [Azure Blueprints](/azure/governance/blueprints/overview) を使用して、企業固有の自動化されたコンプライアンスとセキュリティ要件を実装またはサポートしてください。

## <a name="antipattern-use-a-custom-compliance-or-governance-framework"></a>アンチパターン: 独自のコンプライアンスまたはガバナンス フレームワークを使用する

業界標準に基づいていないカスタムのコンプライアンスおよびガバナンス フレームワークを導入すると、クラウドの導入時間が大幅に増加する場合があります。これは、カスタムのフレームワークをクラウド設定に変換することが困難になるおそれがあるためです。 このシナリオの場合、カスタムの対策と要件を実装可能なセキュリティ制御に変換するために必要な作業量が増えるおそれがあります。 ほとんどの企業は、同様の一連のセキュリティおよびコンプライアンスの要件に準拠する必要があります。 その結果、ほとんどのカスタムのコンプライアンスおよびセキュリティ フレームワークは、現在のコンプライアンス フレームワークとわずかに異なることになります。 追加のセキュリティ要件がある企業は、新しいフレームワークの構築を検討できます。

### <a name="example-use-a-custom-security-framework"></a>例: カスタムのセキュリティ フレームワークを使用する

企業の CISO は、IT セキュリティ担当の従業員に、クラウド セキュリティの戦略とフレームワークを考案するタスクを割り当てます。 IT セキュリティ部門では、業界標準に基づいて構築するのではなく、現在のオンプレミス セキュリティ ポリシーに基づいて構築された新しいフレームワークを作成します。 このクラウド セキュリティ ポリシーを完了した後、AppOps チームと DevOps チームはクラウド セキュリティ ポリシーを実装するのが困難になります。

Azure には、この企業独自のフレームワークとは異なる、より包括的なセキュリティとコンプライアンスの構造が用意されています。 CISO チームは、Azure の制御が自社のコンプライアンスおよびセキュリティ規則と互換性がないと考えています。 そのフレームワークが標準化された制御に基づいていたとしたら、このような結論には至らなかったでしょう。

### <a name="preferred-outcome-rely-on-existing-frameworks"></a>推奨される結果: 既存のフレームワークに依存する

カスタムの企業コンプライアンス フレームワークを確立または導入する前に、CIS Controls バージョン 7.1 や NIST SP800-53 などの既存のフレームワークを使用するか、それに基づいて構築してください。 既存のフレームワークを使用することで、クラウド セキュリティ設定への移行がより簡単になり、測定しやすくなります。 その他のフレームワークの実装については、[Azure Blueprints のサンプル ページ](/azure/governance/blueprints/samples)を参照してください。 一般的なコンプライアンス フレームワーク用のブループリントも Azure に使用できます。

## <a name="next-steps"></a>次のステップ

- [既存の役割、スキル、プロセスをクラウド向けに調整する](../plan/adapt-roles-skills-processes.md)
- [セキュリティ戦略を定義する](../strategy/define-security-strategy.md)
