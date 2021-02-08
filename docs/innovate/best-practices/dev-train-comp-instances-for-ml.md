---
title: 機械学習モデルの開発、トレーニング、コンピューティング インスタンスを決定する
description: 機械学習モデルの開発、トレーニング、コンピューティング インスタンスを決定するときは、使用しているアルゴリズム、データ型、データ サイズ、および分散トレーニングを実行するかどうかを考慮してください。
author: SudhandKumar
ms.author: kumarsud
ms.date: 01/20/2021
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: innovate
ms.custom: think-tank
ms.openlocfilehash: 8addb89d7cbfde12bf41dde07c8bb1124efe8c50
ms.sourcegitcommit: 9cd2b48fbfee229edc778f8c5deaf2dc39dfe2d6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/01/2021
ms.locfileid: "99230716"
---
# <a name="determine-development-training-and-compute-instances-for-your-machine-learning-model"></a>機械学習モデルの開発、トレーニング、コンピューティング インスタンスを決定する

機械学習モデルの開発、トレーニング、コンピューティング インスタンスを決定するときは、使用しているアルゴリズム、データ型、データ サイズ、および分散トレーニングを実行するかどうかを考慮してください。

## <a name="development-and-training-for-your-machine-learning-model"></a>機械学習モデルの開発とトレーニング

機械学習モデルの開発とトレーニングについて理解するには、次の図を参照してください。

[![開発とトレーニングの図。](./media/dev-and-training.png)](./media/dev-and-training.png#lightbox)

## <a name="compute-instances-for-your-machine-learning-model"></a>機械学習モデルのコンピューティング インスタンス

次の図は、機械学習モデルで推論を実行する際に使用するコンピューティング インスタンスを選択するのに役立ちます。

[![推論を示す図。](./media/inference.png)](./media/inference.png#lightbox)

## <a name="next-steps"></a>次のステップ

- Azure Machine Learning ワークスペースで[コンピューティング インスタンスを作成および管理](/azure/machine-learning/how-to-create-manage-compute-instance)して、プロセスの詳細を理解します。

- 開発ライフサイクルにおいて、これらの環境がモデルをトレーニングして展開する上でどのように役立つかについては、[Azure Machine Learning のコンピューティング ターゲット](/azure/machine-learning/concept-compute-target)に関するページを参照してください。
