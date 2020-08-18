---
title: Azure Synapse Analytics の高可用性
description: Azure Synapse Analytics の機能を使用して、高可用性とディザスター リカバリーの要件に対処します。
author: v-hanki
ms.author: brblanch
ms.date: 07/14/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: migrate
ms.openlocfilehash: 93b7a66a1c30ec4975a2cef092b54f56023eed0a
ms.sourcegitcommit: 163e703d9cbf90b26d96087c836a9cbd94fc7e35
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2020
ms.locfileid: "87963283"
---
# <a name="high-availability-for-azure-synapse-analytics"></a>Azure Synapse Analytics の高可用性

Microsoft Azure などの最新のクラウドベースのインフラストラクチャがもたらす主な利点の 1 つは、高可用性 (HA) とディザスター リカバリー (DR) の機能が組み込まれており、実装とカスタマイズが簡素化されることです。 多くの場合、これらの機能は、オンプレミス環境内の同等の機能よりも低コストです。 これらの組み込みの機能を使用することは、既存のデータ ウェアハウス内のバックアップと復旧のメカニズムを移行する必要がないことも意味します。

以降のセクションでは、高可用性とディザスター リカバリーの要件に対処する標準の Azure Synapse Analytics 機能について説明します。

## <a name="high-availability"></a>高可用性

Azure Synapse Analytics では、データベース スナップショットを使用してウェアハウスの高可用性を提供します。 データ ウェアハウス スナップショットでは、データ ウェアハウスの以前の状態を復旧またはコピーするために利用できる復元ポイントが作成されます。 Azure Synapse Analytics は分散システムであるため、データ ウェアハウス スナップショットは Azure Storage に配置されている数多くのファイルで構成されます。 スナップショットでは、データ ウェアハウスに格納されたデータの増分の変更がキャプチャされます。

Azure Synapse Analytics では、スナップショットが終日自動的に取得され、7 日間利用できる復元ポイントが作成されます。 この保持期間は変更できません。 Azure Synapse Analytics では、8 時間の回復ポイントの目標 (RPO) がサポートされます。 プライマリ リージョンのデータ ウェアハウスを、過去 7 日間に作成されたいずれかのスナップショットから復元することができます。

このサービスでは、ユーザー定義の復元ポイントもサポートされています。 手動でスナップショットをトリガーすると、大規模な変更の前後にデータ ウェアハウスの復元ポイントを作成できます。 この機能により、復元ポイントの論理的な一貫性が確保されます。 論理的な一貫性により、短時間で回復するためのワークロードの中断やユーザー エラーに対する追加のデータ保護が提供されます。

## <a name="disaster-recovery"></a>障害復旧

前述のスナップショットに加えて、Azure Synapse Analytics によって、1 日に 1 回、ペアになっているデータセンターに対して標準の geo バックアップも実行されます。 geo 復元の RPO は 24 時間です。 geo バックアップは、Azure Synapse Analytics がサポートされているその他の任意のリージョン内のサーバーに復元することができます。 geo バックアップにより、プライマリ リージョン内の復元ポイントを利用できない場合でもデータ ウェアハウスを確実に復元できます。
