---
title: Azure への移行のベスト プラクティス
description: Azure への移行のベスト プラクティスの概要
author: BrianBlanchard
ms.author: brblanch
ms.date: 04/04/2019
ms.topic: guide
ms.service: cloud-adoption-framework
ms.subservice: migrate
ms.openlocfilehash: 5cc4a0cfdfe605fdd374055e782db2f0ce47c13f
ms.sourcegitcommit: 2362fb3154a91aa421224ffdb2cc632d982b129b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2020
ms.locfileid: "76807293"
---
# <a name="azure-migration-best-practices"></a>Azure への移行のベスト プラクティス

Azure 内には、移行に役立つ複数のツールが用意されています。 クラウド導入フレームワークのこのセクションでは、移行のベスト プラクティスに従って、これらのツールを実装する方法について説明します。 これらのベスト プラクティスは、下図に示すクラウド導入フレームワーク移行モデル内のプロセスの 1 つに合わせて調整されています。

左側の目次にあるいずれかのプロセスを展開すると、そのプロセスの間に通常必要なベスト プラクティスが表示されます。

![クラウド導入フレームワーク移行モデル](../../_images/operational-transformation-migrate.png)

> [!NOTE]
> デジタル資産の計画および資産の査定は、次に示す 2 つの異なるレベルの移行計画および査定です。
>
> - **デジタル資産の計画:** 計画時、デジタル資産を計画または合理化し、全体的な移行バックログを作成します。 ただし、この計画は、ワークロードを移行する前に検証する必要がある、いくつかの前提および詳細情報に基づきます。
> - **資産の査定:** ワークロードを移行する前にワークロードの個々の資産を査定して、クラウドの互換性を評価し、アーキテクチャとサイズの制約を理解します。 このプロセスでは、最初の前提を検証し、個々の資産の移行に必要な詳細情報を用意します。
