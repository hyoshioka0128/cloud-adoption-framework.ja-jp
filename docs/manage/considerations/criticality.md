---
title: ビジネスの重要度 - クラウドの管理と運用
titleSuffix: Microsoft Cloud Adoption Framework for Azure
description: ビジネスの重要度 - クラウドの管理と運用
author: BrianBlanchard
ms.author: brblanch
ms.date: 10/17/2019
ms.topic: article
ms.service: cloud-adoption-framework
ms.subservice: operate
ms.openlocfilehash: 290fcf7093f128c1415bbf960d65074d12490ae1
ms.sourcegitcommit: f3371811a36e12533ecbc3aa936e2a68e0cee25f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/21/2019
ms.locfileid: "72683645"
---
# <a name="business-criticality-in-cloud-management"></a>クラウド管理におけるビジネスの重要度

すべてのビジネスにおいて、あまりに重要なために失敗できないワークロードがいくつか存在します。 このようなワークロードで停止やパフォーマンスの低下が発生すると、企業全体で収益と収益性への悪影響が感じられる可能性があります。 このようなワークロードは、ミッション クリティカルであると見なされます。

その対極には、数か月も使用されることのないワークロードもあります。 このようなワークロードのパフォーマンス低下や停止は望ましくありませんが、その影響は切り離されており限定的です。

IT ポートフォリオ内の各ワークロードの重要度を理解することは、クラウド管理への相互的なコミットメントへの最初の一歩となります。
下の図では、従うべき重要度スケールと、企業によって行われる標準的なコミットメントとの間の一般的な関連を概説しています。

![重要度と管理レベルの関係](../../_images/manage/cloud-criticality-alignment.png)

## <a name="criticality-scale"></a>重要度スケール

ビジネスの重要度の調整作業の最初の手順は、重要度スケールの作成です。 次に示すサンプルのスケールは、参考として使用することも、独自のスケールを作成するためのテンプレートとして使用することもできます。

|重要度  |ビジネス ビュー  |
|---------|---------|
|ミッション クリティカル|企業のミッションに影響を及ぼし、企業の損益計算書に顕著な影響を与える可能性があります。|
|単位クリティカル|特定の部署のミッションとその部署の損益計算書に影響を及ぼします。|
|高|ミッションの妨げにはなりませんが、重要度の高いプロセスに影響を及ぼします。 停止した場合は、測定可能な損失を定量化できます。|
|Medium|プロセスへの影響が考えられます。 損失は少ないか見当もつきませんが、ブランドの毀損やアップストリームの損失が発生する可能性があります。|
|低|ビジネス プロセスへの影響は計り知れません。 ブランドの毀損やアップストリームの損失の可能性はありません。 1 つのチームに局地的な影響が及ぶ可能性があります。|
|サポートされていません|このワークロードに関連付けられたビジネス所有者、チーム、またはプロセスは、ワークロードの継続的な管理への投資を正当化できません。|

業界、業種、または特定のビジネス プロセスに固有の重要度の分類を企業が追加で含めることは一般的です。 追加の分類の例を次に示します。

- **コンプライアンスクリティカル:** 規制の厳しい業界では、コンプライアンス要件を維持するための取り組みの一環として、一部のワークロードが重要になる場合があります。
- **セキュリティクリティカル:** 一部のワークロードはミッション クリティカルでない場合がありますが、停止によってデータが失われたり、保護された情報への意図しないアクセスが発生したりする可能性があります。
- **安全性クリティカル:** 停止中に従業員や顧客の生命または物理的な安全が危険にさらされる場合は、ワークロードを安全性クリティカルとして分類することをお勧めします。

## <a name="importance-of-accurate-criticality"></a>正確な重要度の重要性

このプロセスの後半では、クラウド管理チームがこの分類を使用して、調整された重要度レベルを満たすために必要な作業量を決定します。 オンプレミス環境では、運用管理は、多くの場合、中央で購入され、必要な企業負担のように扱われるため、追加の事業費はほとんどまたはまったくかかりません。 クラウドでは、運用管理 (すべてのクラウドと同様) は、月単位の運用コストとして資産ごとに購入されます。

クラウドでの運用管理には明確かつ直接的なコストがあるため、コストと必要な重要度のスケールを適切に調整することが重要です。

## <a name="select-a-default-criticality"></a>既定の重要度の選択

ポートフォリオ内のすべてのワークロードの最初のレビューには時間がかかる場合があります。 この作業によってより広範なクラウド戦略が妨げられないようにするには、すべてのワークロードに適用する既定の重要度について IT と企業が同意することをお勧めします。

上記の重要度スケールに基づいて、"中" 重要度を既定として使用することをお勧めします。 そうすることで、クラウド戦略チームは、より高いレベルの重要度を必要とするワークロードをすばやく特定できます。

## <a name="using-the-template"></a>テンプレートの使用

次の手順は、[運用管理ブック](https://raw.githubusercontent.com/microsoft/CloudAdoptionFramework/master/manage/opsmanagementworkbook.xlsx)を使用してクラウド管理を計画している読者に当てはまります。

1. 重要度スケールは、このブックのスケール タブで記録できます。
2. "Example" または "Clean Template" の各ワークロードは、"Criticality" 列の既定の重要度を反映するように更新する必要があります。
3. 既定の重要度からの逸脱を反映するには、企業が適切な値を入力する必要があります。

## <a name="next-steps"></a>次の手順

重要度を定義したら、[ビジネスへの影響を計算して記録します](./impact.md)。

> [!div class="nextstepaction"]
> [ビジネスへの影響を計算して記録する](./impact.md)