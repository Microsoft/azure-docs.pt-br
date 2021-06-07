---
title: 'Tutorial: Introdução ao Azure Synapse Analytics – monitorar seu workspace do Synapse'
description: Neste tutorial, você aprenderá a monitorar atividades no workspace do Synapse.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: monitoring
ms.topic: tutorial
ms.date: 12/31/2020
ms.openlocfilehash: 7e8525dbebb42e1f387ee8f0c192efd5e64c9453
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102426033"
---
# <a name="monitor-your-synapse-workspace"></a>Monitorar o workspace do Synapse

Neste tutorial, você aprenderá a monitorar atividades no workspace do Synapse. É possível monitorar as atividades atuais e históricas do SQL, Apache Spark e Pipelines. 

## <a name="introduction-to-the-monitor-hub"></a>Introdução ao Hub de monitoramento

Abra o Synapse Studio e navegue até o hub de **Monitoramento**. Aqui você poderá ver um histórico de todas as atividades que estão ocorrendo no workspace e quais estão ativas no momento. 

* Em **Integração**, você pode monitorar pipelines, gatilhos e runtimes de integração.
* Em **Atividades**, você pode monitorar atividades do Spark e do SQL. 

## <a name="integration"></a>Integração

1. Navegue até **Integração > Execuções de pipeline**. Nessa exibição, você pode ver toda vez que um pipeline é executado em seu workspace. 
1. Localize o pipeline que você executou na etapa anterior e clique no respectivo **Nome de pipeline** para exibir os detalhes.
1. Clique em **Barra de trilha** perto da parte superior do Synapse Studio, clique em **Todas as execuções de pipeline** para retornar à exibição anterior.

## <a name="apache-spark-activities"></a>Atividades do Apache Spark

1. Procure **Atividades > Aplicativos do Apache Spark**. Agora você pode ver todos os aplicativos Spark em execução ou que foram executados em seu workspace.
1. Localize um aplicativo que não esteja mais em execução e clique em **Nome de aplicativo**. Agora você pode ver os detalhes do aplicativo Spark.
1. Se você estiver familiarizado com Apache Spark, poderá encontrar a interface do usuário do servidor de histórico do Apache Spark padrão clicando em **Servidor de histórico do Spark**.

## <a name="sql-activities"></a>Atividades do SQL

1. Procure **Atividades > Solicitações SQL**.
1. Nesta exibição, você pode ver solicitações SQL.
1. Selecione um **Pool** a ser monitorado no filtro **Pool**. Agora você pode ver todas as solicitações SQL que estão em execução ou foram executadas em seu workspace nesse pool.
1. Localize uma solicitação SQL específica e clique no link **Mais** para ver o texto completo da solicitação SQL.

    > [!NOTE] 
    > As solicitações SQL enviadas por meio do Synapse Studio em um pool de SQL dedicado habilitado para workspace (antigo SQL DW) podem ser exibidas no hub do monitor. Para todas as outras atividades de monitoramento, você pode acessar o monitoramento do pool de SQL dedicado do portal do Azure (antigo SQL DW).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Explorar o Centro de Conhecimento](get-started-knowledge-center.md)
