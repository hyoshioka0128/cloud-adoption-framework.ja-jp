---
title: クラウドの操作と管理のアンチパターン
description: IT を導入したり、最新式にしたりしても、仕事が速くなるとも、ビジネス上の成果が向上するとも限りません。
author: lpassig
ms.author: brblanch
ms.date: 02/19/2021
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: operate
ms.custom: think-tank
ms.openlocfilehash: dd52a06b5626abdb0d70cfaa7328a62dedb8441d
ms.sourcegitcommit: 4d68f2e496b065aa5cd915c4e999def51c8901ba
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104674327"
---
# <a name="cloud-operation-and-management-antipatterns"></a>クラウドの操作と管理のアンチパターン

クラウド導入の運用段階や管理段階でお客様がアンチパターンを体験してしまうことが多々あります。 多くの場合、新しいツール チェーンもしくは最新式にしたツール チェーンを慎重に導入することで、こうしたアンチパターンを回避できます。

## <a name="antipattern-focus-on-tooling-not-business-outcomes"></a>アンチパターン: ビジネス上の成果ではなく、ツールに焦点を当てる

IT ツールを最新式にすると、退屈な仕事を社員から解放し、作業環境が改善されることがあります。 ビジネス上の成果を改善するかどうか特定できるよう、新しい IT ツールを測定することが重要です。 ツール チェーンが新しくても、ツール チェーンを最新式にしても、仕事が速くなるとも、ビジネス上の成果が向上するとも限りません。

### <a name="example-introduce-a-platform-that-doesnt-improve-performance"></a>例: プラットフォームを導入してもパフォーマンスが向上しない

改善された新しい継続的インテグレーションと継続的デリバリー (CI/CD) プラットフォームを、ある会社が導入します。 このツールはデリバリーおよび展開パイプラインの定義を簡単にするため、機能を短時間で展開できます。 IT 部署は、パイプラインの構成を高速化するプラットフォームを出荷することに熱心です。 ある部署がこのツールを使用したところ、古いプラットフォームと比べたとき、市場に出すまでの時間がそれほど短縮されないことが判明します。 最終的な承認およびリリース プロセスには変化がなく、改善もされません。

### <a name="preferred-outcome-measure-success-with-business-outcomes"></a>推奨される結果: ビジネス上の成果で成功を測定する

テクノロジと事業目標の足並みを揃えるため、望ましい成果を、両方の領域の指導者に共同で定義してもらいます。 こうした成果と目標が SMART (具体的、測定可能、達成可能、合理的、時間制約あり、の略) であるようにします。 成果と目標が、確実にテクノロジとビジネスに影響を与えるようにします。 [ビジネス内で適切なコミットメントを判断する](../manage/considerations/commitment.md)場合に、Microsoft Cloud Adoption Framework for Azure が役立ちます。

単純なテクノロジだけの成果 (展開の高速化やパイプラインの構成など) で成功を測らないようにします。 そうではなく、テクノロジとビジネスの成果を利用してください。 このタスクでサポートが必要であれば、「[開発者のベロシティ](https://azure.microsoft.com/overview/developer-velocity/)」を参照してください。

## <a name="next-step"></a>次の手順

- [クラウド管理におけるビジネス コミットメント](../manage/considerations/commitment.md)