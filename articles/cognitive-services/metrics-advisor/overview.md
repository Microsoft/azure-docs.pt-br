---
title: O que é o serviço Assistente de Métricas?
titleSuffix: Azure Cognitive Services
description: O que é o Assistente de Métricas?
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: overview
ms.date: 09/14/2020
ms.author: mbullwin
ms.openlocfilehash: dfdd7286013bbb6462fb8e5b1bdf52e6ed738029
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106384673"
---
# <a name="what-is-metrics-advisor-preview"></a>O que é o Assistente de Métricas (versão prévia)? 

O Assistente de Métricas é parte integrante dos Serviços Cognitivos do Azure que usa a IA para realizar o monitoramento de dados e a detecção de anomalias em dados de série temporal. O serviço automatiza o processo de aplicação de modelos aos dados e fornece um conjunto de APIs de workspace baseado na Web para ingestão de dados, detecção de anomalias e diagnósticos, sem que você precise ter conhecimentos sobre machine learning. Os desenvolvedores podem criar aplicativos AIOps, manutenção predicativa e aplicativos de monitor de negócios com base no serviço. Use o Assistente de Métricas para:

* Analisar dados multidimensionais de várias fontes de dados
* Identificar e correlacionar anomalias
* Configurar e ajustar o modelo de detecção de anomalias usado nos dados
* Diagnosticar anomalias e ajudar com a análise de causa raiz

:::image type="content" source="media/metrics-advisor-overview.png" alt-text="Visão geral do Assistente de Métricas":::

Esta documentação contém os seguintes tipos de artigos:
* Os [inícios rápidos](./Quickstarts/web-portal.md) são instruções passo a passo que permitem fazer chamadas para o serviço e obter resultados em um período curto. 
* Os [guias de instruções](./how-tos/onboard-your-data.md) contêm instruções para usar o serviço de maneiras mais específicas ou personalizadas.
* Os [artigos conceituais](glossary.md) fornecem explicações detalhadas sobre a funcionalidade e os recursos do serviço.

## <a name="connect-to-a-variety-of-data-sources"></a>Conectar-se a varias fontes de dados

O Assistente de Métricas pode se conectar a muitos armazenamentos de dados e [ingerir métricas multidimensionais](how-tos/onboard-your-data.md) deles, incluindo: SQL Server, Armazenamento de Blobs do Azure, MongoDB e muito mais.

## <a name="easy-to-use-and-customizable-anomaly-detection"></a>Detecção de anomalias fácil de usar e personalizável

* O Assistente de Métricas seleciona automaticamente o melhor modelo para os dados, sem que você precise ter conhecimentos sobre o aprendizado de máquina.
* Monitore automaticamente todas as séries temporais dentro das [métricas multidimensionais](glossary.md#multi-dimensional-metric).
* Use o [ajuste de parâmetros](how-tos/configure-metrics.md) e os [comentários interativos](how-tos/anomaly-feedback.md) para personalizar o modelo aplicado em seus dados e os resultados futuros da detecção de anomalias.

## <a name="real-time-alerts-through-multiple-channels"></a>Alertas em tempo real por meio de vários canais

Sempre que anomalias são detectadas, o Assistente de Métricas pode [enviar alertas em tempo real](how-tos/alerts.md) por meio de vários canais usando ganchos, como: ganchos de email, webhooks e ganchos do Azure DevOps. Regras de alerta flexíveis permitem que você personalize quais alertas são enviados e o destino deles.

## <a name="smart-diagnostic-insights-by-analyzing-anomalies"></a>Insights de diagnóstico inteligente analisando anomalias

Analise as anomalias detectadas em métricas multidimensionais e gere [insights de diagnóstico inteligente](how-tos/diagnose-incident.md), incluindo a causa raiz mais provável, árvores de diagnósticos, drilling de métricas e muito mais. Ao configurar o [Grafo de métricas](how-tos/metrics-graph.md), a análise de métricas cruzadas pode ser habilitada para ajudar você a visualizar os incidentes.


## <a name="typical-workflow"></a>Fluxo de trabalho típico

O fluxo de trabalho é simples: após a integração dos dados, você pode ajustar a detecção de anomalias e criar configurações que se adequem ao cenário desejado.

1. [Crie um recurso do Azure](https://go.microsoft.com/fwlink/?linkid=2142156) para o Assistente de Métrica. 
2. Crie o primeiro monitoramento usando o portal da Web.
    1. Integrar os dados
    2. Ajustar a detecção de anomalias
    3. Assinar alertas
    4. Exibir insights de diagnóstico
3. Use a API REST para personalizar a instância.

## <a name="next-steps"></a>Próximas etapas

* Explore um início rápido: [monitore a primeira métrica na Web](quickstarts/web-portal.md).
* Explore um início rápido: [use as APIs REST para personalizar a solução](./quickstarts/rest-api-and-client-library.md).
