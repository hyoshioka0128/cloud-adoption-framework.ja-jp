---
title: 手動による Moodle 移行の概要
description: 手動による Moodle 移行の概要。
author: BrianBlanchard
ms.author: brblanch
ms.date: 11/06/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: plan
ms.openlocfilehash: 729304a79e26ef5461b2186a14b73d48cd11b45b
ms.sourcegitcommit: a7eb2f6c4465527cca2d479edbfc9d93d1e44bf1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2020
ms.locfileid: "94714407"
---
# <a name="overview-of-a-manual-moodle-migration"></a>手動による Moodle 移行の概要

このドキュメントでは、Moodle アプリケーションをオンプレミス環境から Azure に移行する方法について説明します。 各ステップに対して以下の 2 つの方法が用意されています。

- Azure portal を使用できるようにする方法。
- Azure CLI を使用しながら、コマンド ラインで同じタスクを実行できるようにする方法。

## <a name="prerequisites"></a>前提条件

オンプレミスでデプロイされたソフトウェア スタックのバージョンが、このガイドでサポートされているバージョンよりも古い場合は、オンプレミスのバージョンを更新するか、パッチを適用して、このガイドに記載されているバージョンになることが想定されています。

- Moodle のデプロイと構成 (データベース構成を含む) のバックアップを作成するには、オンプレミスのインフラストラクチャへのアクセス権が必要です。
- 移行の前に、Azure サブスクリプションと Azure BLOB ストレージ アカウントを作成する必要があります。
- Azure CLI と AzCopy を開いて、プロセス全体で使用します。
- Moodle Web サイトを **メンテナンス モード** に設定します。

この移行ガイドでは、次のソフトウェア バージョンをサポートしています。

- Ubuntu 18.04 LTS
- Nginx 1.14
- MySQL 5.6、5.7、または 8.0 データベース サーバー。 このガイドでは、Azure Database for MySQL を使用します。
- PHP 7.2、7.3、または 7.4
- Moodle 3.8 および 3

## <a name="when-to-use-azure-resource-manager-template-for-moodle-migrations"></a>Moodle 移行のために Azure Resource Manager テンプレートを使用する状況

Azure Resource Manager テンプレートを使用して Moodle を移行すると、Azure にインフラストラクチャが作成されます。 インフラストラクチャが作成されると、Moodle ソフトウェア スタックと関連する依存関係が移行されます。

## <a name="moodle-migration-tasks"></a>Moodle の移行タスク

Moodle アプリケーションを Azure に移行する手順は、次の 3 つのタスクに分類されます。

1. 移行前
1. アプリケーションを移行する実際の手順
1. 移行後

## <a name="next-steps"></a>次のステップ

Moodle 移行プロセスの準備情報について、[Moodle 移行の準備方法](./migration-pre.md)へと進みます。
