---
title: "Criar um ambiente de Análise de Séries Temporais do Azure | Microsoft Docs"
description: "Neste tutorial, você aprenderá como criar o Ambiente de Séries Temporais, conectá-lo a uma origem de evento e prepará-lo para analisar seus dados de evento em minutos."
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
ms.openlocfilehash: 5a159ed0ba38899b21212518d41ff995895904ef
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-new-time-series-insights-environment-in-the-azure-portal"></a>Criar um novo ambiente de Análise de Séries Temporais no portal do Azure

O ambiente de Análise de Séries Temporais é um recurso do Azure com capacidade de entrada e o armazenamento. Os clientes provisionam os ambientes por meio do portal do Azure com a capacidade necessária.

## <a name="steps-to-create-the-environment"></a>Etapas para criar o ambiente

Execute estas etapas para criar o seu ambiente:

1.  Entre no [Portal do Azure](https://portal.azure.com).
2.  Clique no sinal de adição ("+") no canto superior esquerdo.
3.  Pesquise "Análises de Séries Temporais" na caixa de pesquisa.

  ![Criar o ambiente de Análise de Séries Temporais](media/get-started/getstarted-create-environment1.png)

4.  Selecione "Análise de Séries Temporais", clique em "Criar".

  ![Criar o grupo de recursos de Análise de Séries Temporais](media/get-started/getstarted-create-environment2.png)

5.  Especifique o nome do ambiente. Esse nome representará o ambiente no [gerenciador das séries temporais](https://insights.timeseries.azure.com).
6.  Selecione uma assinatura. Escolha uma que contenha a sua fonte de eventos. A Análise das Séries Temporais podem detectar automaticamente o Hub IoT do Azure e os recursos de Hub de Eventos existentes na mesma assinatura.
7.  Selecione ou crie um grupo de recursos. Um grupo de recursos é uma coleção de recursos do Azure que são usados juntos.
8.  Selecione um local de hospedagem. Para evitar a movimentação de dados entre data centers, escolha o local que contém a origem do evento.
9.  Selecione um tipo de preço.
10. Selecione a capacidade. Você pode alterar a capacidade de um ambiente após a criação.
11. Crie seu ambiente. Você também pode fixar o seu ambiente no painel para acesso fácil sempre que você entrar.

  ![Criar uma marcação para Análise de Séries Temporais no painel](media/get-started/getstarted-create-environment3.png)

## <a name="next-steps"></a>Próximas etapas

* [Definir políticas de acesso de dados](time-series-insights-data-access.md) para acessar o seu ambiente no [Portal de Análise de Séries Temporais](https://insights.timeseries.azure.com)
* [Criar uma origem de evento](time-series-insights-add-event-source.md)
* [Enviar eventos](time-series-insights-send-events.md) para a origem do evento
