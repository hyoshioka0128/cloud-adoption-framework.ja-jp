---
title: 手動による Moodle 移行の概要
description: Moodle をオンプレミス環境から Azure に手動で移行するための前提条件と全体的な手順を確認します。
author: UmakanthOS
ms.author: brblanch
ms.date: 11/30/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: plan
ms.custom: think-tank
ms.openlocfilehash: 659c32793a036e1fa7c60a4d16f4d4f5ab771728
ms.sourcegitcommit: 32a958d1dd2d688cb112e9d1be1706bd1e59c505
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/12/2021
ms.locfileid: "98123580"
---
# <a name="overview-of-moodle-manual-migration"></a>手動による Moodle 移行の概要

[Moodle](https://moodle.org/) は、PHP で記述された無料のオープンソースの学習管理システムです。 このガイドでは、Moodle デプロイをオンプレミス環境から Azure に移行する方法について説明します。 このガイドで紹介するのは 2 つのアプローチの手順で、Azure portal または Azure コマンド ライン インターフェイス (Azure CLI) のいずれかが使用されます。

## <a name="prerequisites"></a>前提条件

移行を開始する前に、次の前提条件を満たしておく必要があります。

- オンプレミスのソフトウェアが更新され、次のバージョンに修正プログラムが適用されている。
  - Ubuntu 18.04 LTS
  - Nginx 1.14
  - MySQL 5.6、5.7、または 8.0 データベース サーバー。 このガイドで使用されているのは Azure Database for MySQL です。
  - PHP 7.2、7.3、または 7.4
  - Moodle 3.8 または 3
- お使いの Moodle Web サイトが **メンテナンス モード** に設定されている。
- オンプレミスのインフラストラクチャにアクセスできる (データベース構成を含め、[Moodle のデプロイと構成のバックアップ](migration-pre.md#back-up-on-premises-data)が必要な場合)。
- [Azure CLI](migration-pre.md#install-the-azure-cli) および [AzCopy](migration-pre.md#download-and-install-azcopy) がオンプレミスにインストールされている。
- [Azure サブスクリプション](migration-pre.md#create-a-subscription)および [Azure Blob Storage アカウント](migration-pre.md#create-a-storage-account)が作成されている。

## <a name="moodle-migration-process"></a>Moodle 移行プロセス

Azure Resource Manager (ARM) テンプレートを使用して Moodle を移行すると、Azure にインフラストラクチャが作成されます。その後、Moodle ソフトウェア スタックと関連する依存関係が移行されます。

Azure への Moodle 移行の手順は、次の 3 段階に分かれています。

1. [移行前](migration-pre.md)
1. [アプリケーションの移行](migration-start.md)
1. [移行後](migration-post.md)

## <a name="next-steps"></a>次の手順

「[Moodle 移行のための準備を行う方法](./migration-pre.md)」に進みます。
