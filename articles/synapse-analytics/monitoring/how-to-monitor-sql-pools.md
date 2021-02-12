---
title: Como monitorar pools do SQL no Synapse Studio
description: Saiba como monitorar seus pools SQL usando o Synapse Studio.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 11/30/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 63b63526333435387c3ff5b5c9d5599ec851c1a8
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96465989"
---
# <a name="use-synapse-studio-to-monitor-your-sql-pools"></a>Use o Synapse Studio para monitorar seus pools SQL

Com o Synapse Studio, você pode executar scripts SQL nos pools do SQL em seu espaço de trabalho.

Este artigo explica como monitorar seus pools SQL, permitindo que você fique atento ao status e à atividade de seus pools.

## <a name="access-sql-pools-list"></a>Acessar lista de pools SQL

Para ver a lista de pools SQL em seu espaço de trabalho, primeiro [abra o Synapse Studio](https://web.azuresynapse.net/) e selecione seu espaço de trabalho.

![Fazer logon no espaço de trabalho](./media/common/login-workspace.png)

Depois de abrir seu espaço de trabalho, selecione a seção **Monitor** à esquerda.

![Selecionar Hub de monitor](./media/common/left-nav.png)

Selecione **pools SQL** para exibir a lista de pools SQL.

 ![Selecionar pools SQL](./media/how-to-monitor-sql-pools/monitor-hub-nav-sql-pools.png)

## <a name="filter-your-sql-pools"></a>Filtrar seus pools de SQL

Você pode filtrar a lista de pools SQL para aqueles que lhe interessam. Os filtros na parte superior da tela permitem especificar um campo no qual você deseja filtrar.

Por exemplo, você pode filtrar a exibição para ver apenas os pools do SQL que contêm o nome "salesrecords":

![Filtro de exemplo](./media/how-to-monitor-sql-pools/filter-example.png)

## <a name="view-details-about-a-specific-sql-pool"></a>Exibir detalhes sobre um pool de SQL específico

Para exibir os detalhes sobre um de seus pools SQL, selecione o pool SQL para exibir os detalhes.

![Detalhes do pool do SQL](./media/how-to-monitor-sql-pools/sql-pool-details.png)

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o monitoramento de execuções de pipeline, consulte o artigo [monitorar execuções de pipeline no Synapse Studio](how-to-monitor-pipeline-runs.md) . 

Para obter mais informações sobre como monitorar solicitações SQL, consulte o artigo [monitorar solicitações do SQL no Synapse Studio](how-to-monitor-sql-requests.md) .