---
title: Como monitorar solicitações SQL no Synapse Studio
description: Saiba como monitorar suas solicitações SQL usando o Synapse Studio.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 11/30/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 7296fec91decaf1bd80829f9f3a743a518fbb7a7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96465986"
---
# <a name="use-synapse-studio-to-monitor-your-sql-requests"></a>Use o Synapse Studio para monitorar suas solicitações SQL

Com o Synapse Studio, você pode executar scripts SQL nos pools do SQL em seu espaço de trabalho.

Este artigo explica como monitorar suas solicitações SQL, permitindo que você fique atento ao status das solicitações em execução e descubra detalhes de solicitações históricas.

## <a name="access-sql-requests-list"></a>Acessar lista de solicitações SQL

Para ver a lista de solicitações SQL em seu espaço de trabalho, primeiro [abra o Synapse Studio](https://web.azuresynapse.net/) e selecione seu espaço de trabalho.

![Fazer logon no espaço de trabalho](./media/common/login-workspace.png)

Depois de abrir seu espaço de trabalho, selecione a seção **Monitor** à esquerda.

![Selecionar Hub de monitor](./media/common/left-nav.png)

Selecione **solicitações SQL** para exibir a lista de solicitações SQL.

 ![Selecionar solicitações SQL](./media/how-to-monitor-sql-requests/monitor-hub-nav-sql-requests.png)

## <a name="select-a-sql-pool"></a>Selecionar um pool do SQL

Por padrão, o histórico de solicitações do SQL para o pool de SQL sem servidor interno é mostrado nessa exibição. Você pode selecionar um de seus pools de SQL dedicados para ver o histórico de solicitações do SQL desse pool.

![Selecionar pool SQL](./media/how-to-monitor-sql-requests/select-pool.png)

## <a name="filter-your-sql-requests"></a>Filtrar suas solicitações SQL

Você pode filtrar a lista de solicitações SQL para aquelas que lhe interessam. Os filtros na parte superior da tela permitem especificar um campo no qual você deseja filtrar.

Por exemplo, você pode filtrar a exibição para ver apenas as solicitações do SQL enviadas por `maria@contoso.com` :

![Filtro de exemplo](./media/how-to-monitor-sql-requests/filter-example.png)

## <a name="view-details-about-a-specific-sql-request"></a>Exibir detalhes sobre uma solicitação SQL específica

Para exibir os detalhes sobre uma de suas solicitações SQL, abra a solicitação SQL para navegar até o modo de exibição detalhes. Para solicitações complexas em execução em pools SQL dedicados, você pode monitorar o progresso.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o monitoramento de execuções de pipeline, consulte o artigo [monitorar execuções de pipeline no Synapse Studio](how-to-monitor-pipeline-runs.md) . 

Para obter mais informações sobre como monitorar Apache Spark aplicativos, consulte o artigo [monitorar Apache Spark aplicativos no Synapse Studio](how-to-monitor-spark-applications.md) .