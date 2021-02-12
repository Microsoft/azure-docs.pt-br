---
title: Como monitorar Apache Spark aplicativos no Synapse Studio
description: Saiba como monitorar seus aplicativos Apache Spark usando o Synapse Studio.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 11/30/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 5f9733866e85d79bdb85b8a24d1878e1169c2479
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97586033"
---
# <a name="how-to-use-synapse-studio-to-monitor-your-apache-spark-applications"></a>Como usar o Synapse Studio para monitorar seus aplicativos Apache Spark

Com o Azure Synapse Analytics, você pode usar o Spark para executar blocos de anotações, trabalhos e outros tipos de aplicativos em seus pools do Spark em seu espaço de trabalho.

Este artigo explica como monitorar seus aplicativos Apache Spark, permitindo que você fique atento ao status, aos problemas e ao progresso mais recentes.

## <a name="access-apache-spark-applications-list"></a>Lista de aplicativos de Apache Spark de acesso

Para ver a lista de aplicativos Apache Spark em seu espaço de trabalho, primeiro [abra o Synapse Studio](https://web.azuresynapse.net/) e selecione seu espaço de trabalho.

![Fazer logon no espaço de trabalho](./media/common/login-workspace.png)

Depois de abrir seu espaço de trabalho, selecione a seção **Monitor** à esquerda.

![Selecionar Hub de monitor](./media/common/left-nav.png)

Selecione **Apache Spark aplicativos** para exibir a lista de aplicativos Apache Spark.

 ![Selecionar aplicativos Spark](./media/how-to-monitor-spark-applications/monitor-hub-nav-spark-applications.png)

## <a name="filter-your-apache-spark-applications"></a>Filtrar seus aplicativos Apache Spark

Você pode filtrar a lista de aplicativos Apache Spark para aqueles nos quais está interessado. Os filtros na parte superior da tela permitem especificar um campo no qual você deseja filtrar.

Por exemplo, você pode filtrar a exibição para ver apenas os Apache Spark aplicativos que contêm o nome "vendas":

![Filtro de exemplo](./media/how-to-monitor-spark-applications/filter-example.png)

## <a name="view-details-about-a-specific-apache-spark-application"></a>Exibir detalhes sobre um aplicativo Apache Spark específico

Para exibir os detalhes sobre um de seus aplicativos Apache Spark, selecione o aplicativo Apache Spark e exiba os detalhes. Se o aplicativo Apache Spark ainda estiver em execução, você poderá monitorar o progresso. [Leia mais](apache-spark-applications.md).

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o monitoramento de execuções de pipeline, consulte o artigo [Monitor pipeline executa o Synapse Studio](how-to-monitor-pipeline-runs.md) . 

Para obter mais informações sobre como depurar Apache Spark aplicativo, consulte o artigo [monitorar Apache Spark aplicativos no Synapse Studio](apache-spark-applications.md) .