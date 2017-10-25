---
title: "Adicione uma origem de evento para um ambiente de Análise de Séries Temporais do Azure | Microsoft Docs"
description: "Neste tutorial, você conecta-se a uma origem de evento para o seu ambiente de Análise de Séries Temporais"
keywords: 
services: time-series-insights
documentationcenter: 
author: op-ravi
manager: santoshb
editor: cgronlun
ms.assetid: 
ms.service: tsi
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/21/2017
ms.author: omravi
ms.openlocfilehash: c247a7abd3d1682f8ccd1940e506ef54f9409263
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-event-source-for-your-time-series-insights-environment-using-the-ibiza-portal"></a>Criar uma origem de eventos para o seu ambiente de Análise de Séries Temporais usando o portal do Ibiza

Uma Origem de Evento da Análise de Séries Temporais é derivada de um agente de evento, como o Hubs de Eventos do Azure. A Análise de Séries Temporais se conecta diretamente às Origens de Evento, ingerindo o fluxo de dados sem exigir que os usuários escrevam uma única linha de código. Atualmente, a Análise de Séries Temporais dá suporte a Hubs de Eventos do Azure e a Hubs IoT do Azure. No futuro, mais fontes de eventos serão adicionadas.

## <a name="steps-to-add-an-event-source-to-your-environment"></a>Etapas para adicionar uma origem de evento ao seu ambiente

1.  Entre no [portal do Ibiza](https://portal.azure.com).
2.  Clique em "Todos os recursos" no menu à esquerda do portal do Ibiza.
3.  Selecione o seu ambiente de Análise de Séries Temporais.

  ![Criar origem de evento da Análise de Séries Temporais](media/add-event-source/getstarted-create-event-source-1.png)

4.  Selecione "Origens de Evento", clique em "+ Adicionar".

  ![Criar origem de evento da Análise de Séries Temporais - detalhes](media/add-event-source/getstarted-create-event-source-2.png)

5.  Especifique o nome da origem do evento. Esse nome é associado a todos os eventos dessa origem de evento e está disponível no momento da consulta.
6.  Selecione um hub de eventos da lista de recursos do Hub de Eventos na assinatura atual. Caso contrário, escolha a opção de importação "Fornecer configurações do Hub de Eventos manualmente" para especificar um hub de eventos em outra assinatura. Os eventos devem ser publicados no formato JSON.
7.  Selecione a política que possui permissão de leitura no hub de eventos.
8.  Especifique o grupo de consumidores do hub de eventos.

  > [!IMPORTANT]
  > Verifique se esse grupo de consumidores não é usado por qualquer outro serviço (como um trabalho do Stream Analytics ou outro ambiente de Análise de Séries Temporais). Se o grupo de consumidores é usado por outros serviços, a operação de leitura é prejudicada para esse ambiente e outros serviços. Se você estiver usando "$Default" como o grupo de consumidores, isso pode levar à potencial reutilização por outros leitores.

9.  Clique em “Criar”.

Após a criação da origem de evento, a Análise de Séries Temporais iniciará automaticamente o fluxo de dados no seu ambiente.

## <a name="next-steps"></a>Próximas etapas

* [Enviar eventos](time-series-insights-send-events.md) para a origem do evento
* Exibir seu ambiente no [Portal de Análise de Séries Temporais](https://insights.timeseries.azure.com)
