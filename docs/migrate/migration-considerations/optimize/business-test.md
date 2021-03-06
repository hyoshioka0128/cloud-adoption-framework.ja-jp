---
title: 移行時のビジネス テストのガイダンス
description: ビジネス テストを使用して、ソリューションのパフォーマンスが期待と合致し、ビジネス プロセスを妨げないことを確認する方法について説明します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: migrate
ms.openlocfilehash: 15ab13f8d5797921e125c3b397b44213afcde249
ms.sourcegitcommit: 7d3fc1e407cd18c4fc7c4964a77885907a9b85c0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2020
ms.locfileid: "80432373"
---
# <a name="guidance-for-business-testing-uat-during-migration"></a>移行時のビジネス テスト (UAT) のガイダンス

従来 IT 機能と見なされてきた、ビジネス変革時のユーザー受け入れテストは、もっぱら IT によってのみ指揮できます。 しかし、この機能は、多くの場合にビジネス機能として、最も効率的に実行されることがあります。 その場合、IT は、テストを容易にし、テスト プランを開発して、可能な場合にテストを自動化することによって、このビジネス アクティビティをサポートします。 IT は多くの場合に、テストの代行者としての役割を持ちますが、実際のまたはレプリケートされたビジネス プロセスのコンテキストで、新しいソリューションを利用しようとする実際のユーザーの直接の観察に代わるものはありません。

> [!NOTE]
> 利用可能な場合、自動テストは、はるかに効果的かつ効率的なあらゆるシステムのテストの手段です。 しかし、クラウド移行では多くの場合に、レガシ システムまたは少なくとも安定した実稼働システムに最も重点が置かれます。 多くの場合、それらのシステムは、完全で適切に保守された自動テストによって管理されていません。 この記事では、移行時にそのようなテストを使用できないことを前提としています。

自動テストの次は、パワー ユーザーによるプロセスとテクノロジの変更のテストになります。 パワー ユーザーは一般に、テクノロジ ツールや一連のツールとのやり取りを必要とする実際のプロセスを実行するユーザーです。 パワー ユーザーは、商品やサービスを獲得する、e コマース サイトを使用する外部のお客様によって表されることがあります。 また、パワー ユーザーは、お客様にサービスを提供し、お客様の体験を記録するコール センターなどのビジネス プロセスを実行する従業員のグループによって表されることもあります。

ビジネス テストの目的は、パワー ユーザーからの検証を求め、新しいソリューションが期待と合致し、ビジネス プロセスを妨げないことを確認することです。 その目的が満たされない場合、ビジネス テストが、ワークロードが期待を満たしていない理由と程度を定義するのに役立つ可能性があるフィードバック ループとしての役割を持ちます。

## <a name="business-activities-during-business-testing"></a>ビジネス テスト中のビジネス アクティビティ

ビジネス テスト中の最初の繰り返しは、お客様によって直接に手動で行われます。 これは、フィードバック ループの最も純粋ながら最も時間のかかる形式です。

- **パワー ユーザーを特定します。** 企業は一般に、技術的な変更によって最も影響を受けるパワー ユーザーについて十分に把握しています。
- **パワー ユーザーを調整し、準備します。** パワー ユーザーがビジネス目標、目的の結果、ビジネス プロセスへの期待される変更を理解していることを確認します。 ユーザー自身と管理構造でテスト プロセスの準備します。
- **フィードバック ループの解釈に取り組みます。** IT スタッフがパワー ユーザーからのフィードバックのさまざまなポイントの影響を理解する支援をします。
- **プロセスの変更を明確にします。** 変革によって、ビジネス プロセスの変更がトリガーされる可能性がある場合、その変更とすべてのダウン ストリームの影響を伝えます。
- **フィードバックの優先順位を付けます。** IT チームがビジネスに与える影響に基づいてフィードバックの優先順位を付ける支援をします。

時により、IT は項目別のビジネス テスト アクティビティの代行者としての役割を果たすことができるアナリストや製品所有者を採用することがあります。 しかし、ビジネス部門の参加は強く推奨され、望ましいビジネスの成果が生まれる可能性があります。

## <a name="it-activities-during-business-testing"></a>ビジネス テスト中の IT アクティビティ

IT はビジネス テスト結果の受信者の 1 人としての役割を果たします。 ビジネス テスト中に明らかになるフィードバック ループは、最終的に技術的な変更やプロセスの変更を定義する作業項目になります。 IT は、受信者として、円滑化、フィードバックの収集、結果の技術的アクションの管理において支援することを期待されます。 ビジネス テスト中に IT が実行するアクティビティは一般に、次のようになります。

- ビジネス テストの構造とロジスティクスを提供します。
- テスト中の円滑化を支援します。
- フィードバックを記録する手段とプロセスを提供します。
- ビジネス部門がフィードバックの優先順位を付けて、検証する支援をします。
- 技術的変更に対処するための計画を開発します。
- パワー ユーザーによるテストを効率化できる既存の自動テストを特定します。
- 繰り返しのデプロイやテストが必要になる可能性のある変更については、テスト プロセスを研究し、ベンチマークを定義し、自動化を作成して、パワー ユーザーによるテストをさらに効率化します。

## <a name="next-steps"></a>次のステップ

ビジネス テストと組み合わせて[移行された資産の最適化](./optimize.md)によって、コストとワークロードのパフォーマンスを改善できます。

> [!div class="nextstepaction"]
> [クラウド資産をベンチマークおよびサイズ変更する](./optimize.md)
