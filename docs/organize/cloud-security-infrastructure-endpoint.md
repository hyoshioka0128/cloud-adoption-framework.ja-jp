---
title: クラウド インフラストラクチャとエンドポイント セキュリティの機能
description: クラウド インフラストラクチャとエンドポイント セキュリティの機能について説明します。
author: JanetCThomas
ms.author: janet
ms.date: 05/15/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: organize
ms.custom: internal
ms.openlocfilehash: 437576b84ee6c96b0d6b3be74c55359f80a3d7ef
ms.sourcegitcommit: b6f2b4f8db6c3b1157299ece1f044cff56895919
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2020
ms.locfileid: "97024672"
---
# <a name="function-of-cloud-infrastructure-and-endpoint-security"></a>クラウド インフラストラクチャとエンドポイント セキュリティの機能

インフラストラクチャとエンドポイントのセキュリティに従事しているクラウド セキュリティ チームは、エンタープライズ アプリケーションとユーザーが使用するインフラストラクチャおよびネットワーク コンポーネントに対するセキュリティ保護、検出、および応答制御を提供します。

## <a name="modernization"></a>最新化

ソフトウェア定義データセンターやその他のクラウド テクノロジは、次のようなインフラストラクチャとエンドポイントのセキュリティに関する従来の課題を解決するのに役立ちます。

- **インベントリと構成エラーの検出** は、すべてがすぐに可視化されるため、クラウドでホストされているアセットについての信頼性がはるかに高くなります (物理データセンターとの比較)。
- **脆弱性管理** は、全体的なセキュリティ体制管理の重要な部分に発展しています。
- **コンテナー テクノロジの追加** は、組織がこのテクノロジを幅広く採用すると、インフラストラクチャ チームとネットワーク チームによって管理およびセキュリティ保護されます。 例については、「[Security Center のコンテナーのセキュリティ](/azure/security-center/container-security)」をご覧ください。
- **セキュリティ エージェントの統合** とツールの簡素化により、セキュリティ エージェントとツールの保守とパフォーマンスのオーバーヘッドが削減されます。
- **アプリケーションの許可リスト登録** と内部ネットワークのフィルター処理を使用した、クラウド ホスト サーバー (機械学習によって生成されたルール セットを使用) の構成と展開は、非常に簡単になっています。 Azure の例については、[適応型アプリケーション制御](/azure/security-center/security-center-adaptive-application)および[アダプティブ ネットワークのセキュリティ強化](/azure/security-center/security-center-adaptive-network-hardening)に関する記事をご覧ください。
- インフラストラクチャとセキュリティを構成するための **自動テンプレート** が、クラウドのソフトウェア定義データセンターによりはるかに簡単になります。 Azure での例は [Azure Blueprints](/azure/governance/blueprints/overview) です
- **Just-In-Time (JIT) と Just enough access (JEA)** により、サーバーとエンドポイントに対する特権アクセスに対して最小限の特権の原則を実際に適用できます。
- **ユーザー エクスペリエンス** は、ユーザーがエンドポイント デバイスを選択または購入できるようになるにつれて重要になります。
- **統合エンドポイント管理** を使用すると、モバイル デバイスや従来の PC を含むすべてのエンドポイント デバイスのセキュリティ体制を管理できるだけでなく、ゼロ信頼アクセス制御ソリューションに対して重要なデバイス整合性シグナルを提供できます。
- **ネットワーク セキュリティ アーキテクチャ** とコントロールは、クラウド アプリケーション アーキテクチャへの移行によって部分的になくなりつつありますが、基本的なセキュリティ対策を維持しています。 詳細については、「[ネットワーク セキュリティと封じ込め](/azure/architecture/framework/security/network-security-containment)」を参照してください。

## <a name="team-composition-and-key-relationships"></a>チームの構成と重要な関係

クラウド インフラストラクチャとエンドポイント セキュリティは、一般に、次の役割とやり取りします。

- IT アーキテクチャと運用
- セキュリティのアーキテクチャ
- セキュリティ オペレーション センター (SOC)
- コンプライアンス チーム
- 監査チーム

## <a name="next-steps"></a>次のステップ

[脅威インテリジェンス](./cloud-security-threat-intelligence.md)の機能を確認します。
