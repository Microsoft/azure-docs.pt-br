---
title: Portais para criar e editar consultas de log no Azure Log Analytics | Microsoft Docs
description: "Este artigo descreve os portais que você pode usar no Azure Log Analytics para criar e editar pesquisas de logs."
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2017
ms.author: magoedte; bwren
ms.openlocfilehash: b205f226d95d94b938a70a834ac0147e76d459ea
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="portals-for-creating-and-editing-log-queries-in-azure-log-analytics"></a>Portais para criar e editar consultas de log no Azure Log Analytics

Você pode usar pesquisas de logs em vários locais pelo Log Analytics para recuperar dados do espaço de trabalho.  No entanto, para realmente criar e editar consultas, além de trabalhar interativamente com os dados retornados, você tem duas opções que são descritas abaixo.  

## <a name="log-search"></a>Pesquisa de log 
A página Pesquisa de Logs pode ser acessada no Portal do Azure.  Ele é adequado para criar consultas básicas que podem ser criadas em uma única linha.  A Pesquisa de Logs pode ser usada sem iniciar um portal externo e você pode usá-la para executar uma variedade de funções com pesquisas de logs, incluindo a criação de regras de alerta, a criação de grupos de computadores e a exportação dos resultados da consulta.  

A Pesquisa de Logs fornece vários recursos para editar a consulta sem ter um conhecimento completo da linguagem de consulta.  É possível obter um resumo desses recursos em [Criar pesquisas de logs no Azure Log Analytics usando a Pesquisa de Logs](log-analytics-log-search-log-search-portal.md).


![Página da Pesquisa de Logs](media/log-analytics-log-search-portals/log-search-portal.png)

## <a name="advanced-analytics-portal"></a>Portal Análise Avançada
O portal Análise Avançada é um portal dedicado que fornece funcionalidade avançada não disponível na Pesquisa de Logs do Portal do Azure.  Os recursos incluem a capacidade de editar uma consulta em várias linhas e de executar código seletivamente, além do IntelliSense sensível ao contexto e da análise inteligente.  O portal Análise Avançada é mais adequado para a criação de consultas complexas, que são salvas como uma pesquisa de logs ou então copiadas e coladas em outros elementos do Log Analytics.  Inicie o portal Análise Avançada de um link na página Pesquisa de Logs.

![Portal Análise Avançada](media/log-analytics-log-search-portals/advanced-analytics-portal.png)


Devido a seus recursos avançados, normalmente você usará o portal Análise Avançada como a principal ferramenta para criar e editar consultas.  Depois de determinar se a consulta funciona conforme o esperado, copie-a e cole-a em outro lugar, como a página Pesquisa de Logs ou o Designer de Exibição.  No entanto, já que o portal Análise Avançada dá suporte a consultas com várias linhas, você precisa levar em consideração o exposto a seguir ao copiar uma consulta desse portal.

- Comentários devem ser removidos da consulta antes que ele seja copiada e colada em outro local.  Você pode comentar uma linha, precedendo-a de duas barras (//).  Quando você cola uma consulta de várias linhas em uma única linha, quebras de linha são removidas.  Se os comentários forem incluídos, todos os caracteres após o primeiro comentário serão considerados parte do comentário.


## <a name="next-steps"></a>Próximas etapas

- Percorra um tutorial sobre como usar a [Pesquisa de Logs](log-analytics-tutorial-viewdata.md) para aprender a criar consultas usando a linguagem de consulta
- Faça check-out no [portal Análises Avançadas](https://go.microsoft.com/fwlink/?linkid=856587) para criar consultas sofisticadas e usar como um ambiente de desenvolvimento para suas pesquisas de logs.

