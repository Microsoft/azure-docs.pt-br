---
title: "Diagnóstico inteligente de alterações no desempenho do aplicativo Web no Azure Application Insights | Microsoft Docs"
description: "Diagnóstico automático de picos ou etapas em telemetria de desempenho do seu aplicativo Web."
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/16/2017
ms.author: cfreeman
ms.openlocfilehash: 5e53bc714d89bf6204681349e7890e0b8fbc7046
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="diagnose-sudden-changes-in-your-app-telemetry"></a>Diagnosticar alterações repentinas na telemetria do aplicativo

*Esse recurso está em visualização.*

Faça o diagnóstico de alterações repentinas no uso ou no desempenho do seu aplicativo Web com um único clique! O recurso Smart Diagnostics está disponível sempre que você cria um gráfico no [Analytics](app-insights-analytics.md) em [Application Insights](app-insights-overview.md). Sempre que houver uma alteração incomum de tendência dos seus resultados, como um pico ou um DIP, o Diagnóstico Inteligente identificará um padrão de dimensões e valores relacionados que poderão explicar a alteração. Isso ajuda a diagnosticar rapidamente o problema. 

Nesse exemplo, o Diagnóstico Inteligente identificou um padrão de valores de propriedade relacionado à alteração e destaca a diferença entre resultados com e sem esse padrão:

![resultado de diagnóstico analítico de exemplo](./media/app-insights-analytics-diagnostics/analytics-result.png)
 

## <a name="diagnose-data-changes"></a>Diagnosticar alterações de dados

1.  Execute uma consulta no Analytics e renderize-o como um gráfico de tempo. 
2.  Clique em qualquer ponto de pico destacado, se houver.
 
    ![ponto de pico](./media/app-insights-analytics-diagnostics/peak.png)

    O diagnóstico leva alguns segundos para descobrir um padrão.

3. A guia Resultados do Diagnóstico mostra um padrão que pode explicar a descontinuidade dos dados.

    ![result](./media/app-insights-analytics-diagnostics/result.png)
 
    O texto mostra os valores de dimensão que parecem estar relacionados com a mudança. Nesse exemplo, isso está associado a uma solicitação específica e uma versão específica do navegador.

    Observe também os dois componentes do gráfico, com o filtro verdadeiro e falso. O componente falso mostra uma tendência inalterada. Em outras palavras, não há alteração nos resultados da telemetria se excluímos a combinação de dimensões problemática identificada pelo Diagnóstico. Em contrapartida, os resultados dentro dessa combinação apresenta uma alteração dramática dentro da área de investigação destacada. Isso mostra que o Diagnóstico encontrou uma combinação de propriedades que explica a alteração.

4.  Se o padrão for complexo, será necessário passar o mouse sobre **Mostrar tudo** para visualizar as dimensões.

    ![mostrar tudo](./media/app-insights-analytics-diagnostics/show-all.png)
 
5.  Caso o Diagnóstico não encontre nenhum padrão significativo a ser notificado, a página "nenhum resultado" será apresentada. Nesse momento, você pode alterar sua consulta. Por exemplo, você pode restringir o intervalo de tempo e o agrupamento na consulta do Analytics para uma análise mais detalhada e resultados potencialmente melhores.

Tendo o conhecimento de que uma página específica do seu site tem um problema em um navegador específico, você pode ir diretamente para a página do problema e investigar as alterações recentes.

## <a name="try-the-demo"></a>Experimente a demonstração

[Clique aqui para ver uma demonstração](https://analytics.applicationinsights.io/demo?q=H4sIAAAAAAAAA3VSTY%2FTQAy991dYPXWlLf0QIO2KIiGWA3duiMPsxEnMzhe2p6WIH48nVUsuGylRNPOe3%2FOzN5vFZgPfRhL4VZHPIGM%2BCdgHdESgpMjOKx0RnsgNKYuSF%2BjRaWUE7xKMGIoBgTpMSv2Z0jBxOWc1QBWEPjM4EMUCP2uc0A3x8E5HKMi%2BEQNC7oHRbIgKdJWdUk5vmr9PvdkArildit%2Fcrk0lBDjnyhBzk%2FKVxdTy0QhNY6RhDPYqdlCy9XMV96NjBZc68IH8y6Tzuf01iZxeIZ%2FI5DqMOYmaQQRXNUdz6qGb5WOdSKEXnOozHtEFK%2Bh0qnq5YQzGF9DcoinoqbcigkO0NOZRNGOZaaBkMuat5xznFOtULKhG%2BdrGlVDhy%2B8SMlsETV8dD6gTd0YrbsBrFq6U1v%2Filv4C%2FsJpRJuwUrQTZ0P7eIDOHLeD1X67e7%2Fe7dbbB9htH%2Ffbu4vQDfvhFez%2B8a1h%2F1f3VSy%2BJ4Ol1oN8X4qN0qMZWv44HJanzKFLeJIltKcRpcbomP7gbHNkdV2Xe1uqO3g%2BwzOl1c3PvbmMlC7KjKlry2GX0w4s%2FgFoo5%2BhBAMAAA%3D%3D&timespan=PT24H) no dados de exemplo.

## <a name="how-it-works"></a>Como ele funciona

O Diagnóstico Inteligente usa um algoritmo de aprendizado de máquina não supervisionado avançado com base na operação [DiffPatterns](app-insights-analytics-reference.md). Ele procura padrões candidatos que podem explicar a alteração de dados. Ele analisa o impacto de cada candidato na métrica e mostra o padrão que melhor se correlaciona com a alteração.

## <a name="no-diagnostic-points"></a>Não há pontos de diagnóstico?

O Diagnóstico Inteligente funciona somente quando os seguintes critérios forem atendidos:

 * A configuração do Diagnóstico Inteligente está ativada. Procure o ícone de Configurações no Analytics.
 * A opção de Diagnóstico Inteligente nas configurações do Analytics está selecionada. 
 * Eixo de tempo: o eixo X do gráfico deve ser do tipo `datetime`.
 * Gráfico de linha ou área: o diagnóstico funciona somente com esses tipos de gráfico. Use `| render timechart` ou `| render areachart` no final da sua consulta; ou selecione Gráfico de Área ou Linha no seletor suspenso.
 * Descontinuidade: deve haver uma descontinuidade significativa nos dados.
 * Pontos suficientes para analisar.
 * Não mais que uma cláusula de resumo na consulta.
 * Nenhuma cláusula de projeto que contenha uma definição de nome antes da cláusula de resumo.

 
 ## <a name="related-articles"></a>Artigos relacionados

 * [Tutorial do Analytics](app-insights-analytics-tour.md)
 * [Detecção inteligente](app-insights-proactive-diagnostics.md) alerta automaticamente para problemas de desempenho.