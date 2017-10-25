---
title: "Adicione um conjunto de dados de referência ao ambiente de Análise de Séries Temporais do Azure | Microsoft Docs"
description: "Neste tutorial, você deve adicionar o conjunto de dados de referência ao seu ambiente de Análise de Séries Temporais"
keywords: 
services: time-series-insights
documentationcenter: 
author: venkatgct
manager: almineev
editor: cgronlun
ms.assetid: 
ms.service: tsi
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/29/2017
ms.author: venkatja
ms.openlocfilehash: 574f3c5b3a889733f47d729c795ec39009f2ef4a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-reference-data-set-for-your-time-series-insights-environment-using-the-ibiza-portal"></a>Criar um conjunto de dados de referência para o seu ambiente de Análise de Séries Temporais usando o portal do Ibiza

Um conjunto de dados de referência é uma coleção de itens que são aumentados com os eventos da fonte de evento. O mecanismo de entrada da Análise de Séries Temporais une um evento da fonte de evento a um item em seu conjunto de dados de referência. Esse evento aumentado é disponibilizado para consulta. Essa junção baseia-se nas chaves definidas no conjunto de dados de referência.

## <a name="steps-to-add-a-reference-data-set-to-your-environment"></a>Etapas para adicionar um conjunto de dados de referência ao seu ambiente

1. Entre no [portal do Ibiza](https://portal.azure.com).
2. Clique em "Todos os recursos" no menu à esquerda do portal do Ibiza.
3. Selecione o seu ambiente de Análise de Séries Temporais.

    ![Criar o conjunto de dados de referência Análises de Séries Temporais](media/add-reference-data-set/getstarted-create-reference-data-set-1.png)

4. Selecione "Conjuntos de dados de referência" e clique em "+ Adicionar".

    ![Criar o conjunto de dados de referência Análises de Séries Temporais - detalhes](media/add-reference-data-set/getstarted-create-reference-data-set-2.png)

5. Especifique o nome do conjunto de dados de referência.
6. Especifique o nome da chave e seu tipo. Esse nome e o tipo são usados para selecionar a propriedade correta do evento na fonte de evento. Por exemplo, se você fornecer o nome da chave como "DeviceId" e digitar como "String", em seguida, o mecanismo de entrada da Análise de Séries Temporais procurará uma propriedade com o nome "DeviceId" do tipo "String" no evento de entrada. Você pode fornecer mais de uma chave para unir ao evento. A correspondência de nome de propriedade diferencia maiúsculas de minúsculas.

     ![Criar o conjunto de dados de referência Análises de Séries Temporais - detalhes](media/add-reference-data-set/getstarted-create-reference-data-set-3.png)

7. Clique em “Criar”.

## <a name="next-steps"></a>Próximas etapas

* [Gerenciar dados de referência](time-series-insights-manage-reference-data-csharp.md) programaticamente.
* Para obter a referência completa de API, confira o documento [API de Dados de Referência](/rest/api/time-series-insights/time-series-insights-reference-reference-data-api).