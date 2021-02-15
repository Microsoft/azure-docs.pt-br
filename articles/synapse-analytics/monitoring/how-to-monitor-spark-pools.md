---
title: Como monitorar pools de Apache Spark no Synapse Studio
description: Saiba como monitorar seus pools de Apache Spark usando o Synapse Studio.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 11/30/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 8d95897e0c2d58b2a3955918be945800eed9ba56
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96465960"
---
# <a name="use-synapse-studio-to-monitor-your-apache-spark-pools"></a>Use o Synapse Studio para monitorar seus pools de Apache Spark

Com o Azure Synapse Analytics, você pode usar o Spark para executar blocos de anotações, trabalhos e outros tipos de aplicativos em pools do Spark em seu espaço de trabalho.

Este artigo explica como monitorar seus pools de Apache Spark, permitindo que você fique atento ao status de seus pools, incluindo quantas vCores estão sendo usadas por diferentes usuários de espaço de trabalho.

## <a name="access-spark-pools-list"></a>Acessar a lista de pools do Spark

Para ver a lista de pools de Apache Spark em seu espaço de trabalho, primeiro [abra o Synapse Studio](https://web.azuresynapse.net/) e selecione seu espaço de trabalho.

![Fazer logon no espaço de trabalho](./media/common/login-workspace.png)

Depois de abrir seu espaço de trabalho, selecione a seção **Monitor** à esquerda.

![Selecionar Hub de monitor](./media/common/left-nav.png)

Selecione **pools de Apache Spark** para exibir a lista de pools de Apache Spark.

 ![Selecionar pools de Apache Spark](./media/how-to-monitor-spark-pools/monitor-hub-nav-spark-pools.png)

## <a name="filter-your-spark-pools"></a>Filtrar os pools do Spark

Você pode filtrar a lista de pools do Spark para aqueles que lhe interessam. Os filtros na parte superior da tela permitem especificar um campo no qual você deseja filtrar.

Por exemplo, você pode filtrar a exibição para ver apenas os pools do Spark que contêm o nome "dataprep":

![Filtro de exemplo](./media/how-to-monitor-spark-pools/filter-example.png)

## <a name="view-details-about-a-specific-spark-pool"></a>Exibir detalhes sobre um pool do Spark específico

Para exibir os detalhes sobre um de seus pools do Spark, selecione o pool do Spark para exibir os detalhes.

![Detalhes do pool de Apache Spark](./media/how-to-monitor-spark-pools/spark-pool-details.png)

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o monitoramento de execuções de pipeline, consulte o artigo [monitorar execuções de pipeline no Synapse Studio](how-to-monitor-pipeline-runs.md) . 

Para obter mais informações sobre como monitorar Apache Spark aplicativos, consulte o artigo [monitorar Apache Spark aplicativos no Synapse Studio](how-to-monitor-spark-applications.md) .
