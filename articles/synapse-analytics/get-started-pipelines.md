---
title: 'Tutorial: Introdução à integração a pipelines'
description: Neste tutorial, você aprenderá a integrar pipelines e atividades usando o Synapse Studio.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: pipeline
ms.topic: tutorial
ms.date: 12/31/2020
ms.openlocfilehash: 2ea7c3c440fcf95e4512464333efe8461788bceb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98219395"
---
# <a name="integrate-with-pipelines"></a>Fazer a integração a pipelines

Neste tutorial, você aprenderá a integrar pipelines e atividades usando o Synapse Studio. 

## <a name="overview"></a>Visão geral

Você pode integrar uma ampla variedade de tarefas no Azure Synapse.

1. No Synapse Studio, acesse o hub **Integrar**.
1. Selecione **+**  > **Pipeline** para criar um pipeline. Clique no novo objeto de pipeline para abrir o Designer de Pipeline.
1. Em **Atividades**, expanda a pasta **Synapse** e arraste um objeto **Notebook** para o designer.
1. Selecione a guia **Configurações** das propriedades da atividade do Notebook. Use a lista suspensa para escolher qualquer notebook do workspace atual do Azure Synapse. 
1. No pipeline, selecione **Adicionar gatilho** > **Novo/editar**.
1. Em **Escolher gatilho**, selecione **Novo** e defina **Recorrência** como "a cada 1 hora".
1. Selecione **OK**. 
1. Selecione **Publicar Tudo**. 


## <a name="monitor-pipeline"></a>Monitorar o pipeline

1. Depois que o pipeline for publicado, para fazê-lo ser executado imediatamente sem esperar pela próxima hora, selecione **Adicionar gatilho** > **Disparar agora**.
1. No Synapse Studio, acesse o hub **Monitor** e **Execuções de pipeline** para monitorar o andamento da execução do pipeline.



## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Visualizar os dados com o Power BI](get-started-visualize-power-bi.md)
