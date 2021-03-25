---
title: クラウド導入計画のアンチパターン
description: 最新化よりも置き換えプロジェクトを選択した、間違った運用またはサービス モデルを使用したクラウド導入計画のアンチパターンは避けてください。
author: mahia127
ms.author: brblanch
ms.date: 02/19/2021
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: plan
ms.custom: think-tank
ms.openlocfilehash: 6d3063f8ba61ab1d25e9402177e11a01b1d3d25b
ms.sourcegitcommit: 9e4bc0e233a24642853f5e8acbeb9746b2444024
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102117502"
---
# <a name="cloud-adoption-plan-antipatterns"></a>クラウド導入計画のアンチパターン

クラウドの導入計画において、次のようなさまざまな理由で、お客様はアンチパターンを経験することになります。

- 運用モデルの整合性が取れていないために、市場投入に時間がかかったり、誤解が生じたり、IT 部署に対してますます圧力がかかる場合がある。
- PaaS (サービスとしてのプラットフォーム) でコストを削減できると考えた企業が、間違ったサービス モデルを選択する。
- 組織のアーキテクチャ変更によって、大規模な置き換えプロジェクトを行う必要が生じる。 これらのプロジェクトの管理は、しばしば複雑でコストがかかります。

## <a name="antipattern-choose-the-wrong-cloud-operating-model"></a>アンチパターン: 間違ったクラウド運用モデルを選択する

企業のクラウド運用モデルは、その戦略上の優先順位とポートフォリオの範囲によって決まります。 モデルにより、アカウンタビリティ、ランディン グゾーン、および何に重点を置いているかは異なります。 モデルが会社の目標に合わない場合は、次のような問題が発生するおそれがあります。

- 市場投入までの時間の増大
- 誤解が生じる
- IT 部署に対する圧力の増加

### <a name="example-assign-too-much-responsibility-to-a-small-team"></a>例: 小規模なチームに過剰な責任を割り当てる

ある企業で、IT 部署にクラウド内で実行されることの全責任を負わせる運用モデルを導入しました。 そのクラウドの責任は 3 人のチームで負います。 この仕組みでは、次のために導入に時間がかかります。

- チームは、ビジネス、運用、およびセキュリティへの影響を完全に理解した後でしか、メジャーを承認しない。
- それらの問題が、そのチームの専門分野ではない。

対象の分野の専門家がクラウド サービスを使用したいので、部署からの圧力が強まります。 部署は会社のクレジット カードを使用して自分たちの環境を作成するため、おそらくシャドウ IT が出現します。

### <a name="preferred-outcome-compare-models-and-build-a-readiness-plan"></a>望ましい結果: モデルを比較し、準備計画を作成する

戦略上の優先順位、ポートフォリオの範囲、要件、および制約を確認します。 現在お使いのクラウド運用モデルと、[最も一般的な 4 つのクラウド運用パターンを比較](../operating-model/compare.md)して、運用モデルの選択肢を検証します。 自分の組織に適したクラウド運用モデルを 1 つ以上特定します。 そしてモデルを決定します。 役割は運用モデルによって変わるため、クラウドに移行する前に、[スキルの準備計画を構築](../plan/adapt-roles-skills-processes.md)します。

## <a name="antipattern-choose-the-wrong-service-model"></a>アンチパターン: 間違ったサービス モデルを選択する

PaaS ソリューション方が IaaS (サービスとしてのインフラストラクチャ) ソリューションよりも低コストであると企業が想定する場合があります。 このような想定により、間違ったサービス モデルが選択されてしまうことがあります。 このような間違いは、クラウドに移行する主な理由がコスト節約である、コスト重視の企業でよく見られます。 これらの企業では、PaaS を採用する場合、特に特定の責任をクラウド プロバイダーに移行する場合に、プロセスも変更する必要があることを忘れてしまっています。 PaaS に切り替えると、調整作業、エンジニアリング手法、およびデリバリー パイプラインが根本的に変わります。 予期しないコストが増え、その結果遅延が発生するおそれがあります。

### <a name="example-choose-paas-over-iaas"></a>例: IaaS より PaaS を選択する

パブリッシャーが、データセンターをクラウドに移行するプログラムを開始します。 経営陣は、現在のアプリケーション アーキテクチャとツールをすべて一度に最新化したいと考えています。 その理由は、以下のとおりです。

- コスト効率の最大化。
- より最新のアプリケーション ポートフォリオの開発。

彼らの導入戦略では、IaaS でなく PaaS が選択されました。 クラウド導入の取り組みの 1 年後の彼らの導入率は低いものでした。 PaaS を最大限に活用には、多くのプロセス、手法、ツールを変更する必要がありました。 経営陣には、PaaS に関連する通常の影響と効果は見られません。 同時に、データセンターのコストは変わらないにもかかわらず、IT はこれまでになく遅くなります。

### <a name="preferred-outcome-minimize-disruption-to-your-business"></a>望ましい結果: 自分のビジネスに対する中断を最小限に抑える

クラウドを最初に採用するプロジェクトでは、IaaS を使用して調整作業を減らします。 最初からではなく後でクラウドに移行した方が、採用した新しいプロセスと手法の管理がしやすくなります。 特にデータセンターを移管するシナリオでは、最初に IaaS を採用します。 同時に、クラウド スキル イニシアチブも開始します。

ワークロードが既にクラウドにあるようになってから、後で徐々に最新化して PaaS を採用します。 得られた経験により、より速く PaaS を導入できます。 最新化に必要な新しいスキルとプロセスは、少数しか学ぶ必要がありません。 また、自分のビジネス プロセスを大幅に中断する必要もありません。

[クラウドの合理化](../digital-estate/5-rs-of-rationalization.md)に従ってデジタル資産を評価します。 この記事では、最も一般的な移行および最新化の手順である 5 つの R について説明します。

- 再ホスト
- リファクター
- 再設計
- [再構築]
- Replace

## <a name="antipattern-replace-architecture"></a>アンチパターン: アーキテクチャを置き換える

PaaS と SaaS (サービスとしてのソフトウェア) に基づくアプリケーションは、比較的簡単に管理できます。 これらには、通常管理者はほとんど労力を必要としません。 その結果、多くの企業は、古い複雑なアーキテクチャを SaaS とクラウドネイティブの概念に置き換える再設計を行います。 このようなアーキテクチャの変更は、通常、大規模な置き換えプロジェクトにつながります。 これらのプロジェクトの管理および遂行は、複雑でコストがかかります。 プロセスと運用モデルを変更した場合は、他にも大きなリスクがあります。

### <a name="example-choose-replacement-over-modernization"></a>例: 最新化でなく置き換えを選択する

ある企業が、大規模な SAP 環境を所有しています。 その IT 部署は、パフォーマンスと安定性に関する問題がいくつか発生しているこの状況を置き換えたいと考えています。 IT で置き換えプロジェクトが開始されてから、環境全体を置き換えるためのデューディリジェンス リストの数は日々増えています。

### <a name="preferred-outcome-rationalize-your-digital-estate"></a>望ましい結果: 自分のデジタル資産を合理化する

大規模または複雑なアプリケーション環境を置き換える前に、代わりに最新化して、自分の環境を段階的に改善することを検討します。 お使いのアプリケーション環境に比較的小さな変更を加えた場合でも、パフォーマンスと信頼性に大きな影響がある場合があります。 たとえば、ホスト プラットフォームを Azure に変更すると、安定性と即効性のある結果を得られます。 推定した置き換えコストのほんの一部で、パフォーマンスと信頼性を得られます。

革新戦略を決定する場合は、最新化のさまざまな選択肢を検討します。 これらのオプションを、概念実証 (PoC) で評価します。

自分の会社の[デジタル資産](../digital-estate/index.md)を把握します。 自分の資産を最もうまく最新化および移行できるのは、[合理化の 5 つの R](../digital-estate/5-rs-of-rationalization.md) のどれであるか判断します。

- 再ホスト
- リファクター
- 再設計
- [再構築]
- Replace

## <a name="next-steps"></a>次のステップ

- [一般的なクラウド運用モデルを比較する](../operating-model/compare.md)
- [スキルの準備計画の構築](../plan/adapt-roles-skills-processes.md)
- [クラウドの合理化](../digital-estate/5-rs-of-rationalization.md)
- [デジタル資産とは](../digital-estate/index.md)