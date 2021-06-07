---
title: Exibir métricas de uso da API do Azure Maps | Mapas do Microsoft Azure
description: Saiba como exibir as métricas de uso da API do Azure Maps, como solicitações totais, total de erros e disponibilidade. Consulte Como filtrar dados e dividir os resultados.
author: anastasia-ms
ms.author: v-stharr
ms.date: 08/06/2018
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: c9b732bd25e7ef8aa084c98d5b059d422f86a4b0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96003507"
---
# <a name="view-azure-maps-api-usage-metrics"></a>Exibir as métricas de uso da API do Azure Mapas

Este artigo mostra como exibir as métricas de uso da API para sua conta do Azure Maps, na [portal do Azure](https://portal.azure.com). As métricas são mostradas em um formato gráfico conveniente ao longo de uma duração de tempo personalizáveis.

## <a name="view-metric-snapshot"></a>Exibir instantâneo de métrica

Você pode ver algumas métricas comuns sobre a página de **visão geral** de sua conta de mapas. Atualmente, ele mostra *Total de Solicitações*, *Total de Erros*, e *Disponibilidade* em um período de tempo selecionável.

![Visão geral das métricas de uso do Azure Maps](media/how-to-view-api-usage/portal-overview.png)

Continue para a próxima seção se você precisar personalizar esses gráficos para sua análise específica.

## <a name="view-detailed-metrics"></a>Exibir métricas detalhadas

1. Entre em sua assinatura do Azure no [portal](https://portal.azure.com).

2. Clique o **Todos os recursos** no lado esquerdo do item de menu e navegue até a sua *conta do Azure Mapas*.

3. Quando sua conta de Mapas for aberta, clique no menu **Métricas** à esquerda.

4. No painel **métricas** , escolha uma das seguintes opções:

   1. **Disponibilidade** - que mostra a *Média* da disponibilidade da API durante um período de tempo.
   2. **Uso** -que mostra como o uso *contagem* para sua conta.

      ![Painel de métricas de uso do Azure Maps](media/how-to-view-api-usage/portal-metrics.png)

5. Em seguida, você pode selecionar *Intervalo de tempo* clicando em **Últimas 24 horas (Automático)**. Por padrão, o intervalo de tempo é definido como 24 horas. Depois de clicar, você verá todos os intervalos de tempo selecionáveis. Você pode selecionar *Granularidade de tempo* e optar por mostrar a hora como *local* ou *GMT* no mesmo menu suspenso. Clique em **Aplicar**.

    ![Intervalo de tempo das métricas do Azure Maps](media/how-to-view-api-usage/time-range.png)

6. Depois de adicionar sua métrica, você pode **Adicionar o filtro** das propriedades relevantes para essa métrica. Em seguida, selecione o valor da propriedade que você deseja ver refletido no grafo.

    ![Filtro de métricas de uso do Azure Maps](media/how-to-view-api-usage/filter.png)

7. Você também pode **Aplicar a divisão** para sua métrica com base em sua propriedade de métrica selecionada. Ele permite que o grafo seja dividido em vários gráficos, para cada valor dessa propriedade. Na figura a seguir, a cor de cada gráfico corresponde ao valor da propriedade mostrado na parte inferior do gráfico.

    ![Divisão de métricas de uso do Azure Maps](media/how-to-view-api-usage/splitting.png)

8. Você também pode observar várias métricas no mesmo gráfico, simplesmente clicando no botão **Adicionar métrica** na parte superior.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as APIs do Azure Maps para as quais você deseja acompanhar o uso:
> [!div class="nextstepaction"] 
> [Instruções do SDK para Web do Azure Maps](how-to-use-map-control.md)

> [!div class="nextstepaction"] 
> [Instruções do Azure Maps SDK do Android](how-to-use-android-map-control-library.md)

> [!div class="nextstepaction"]
> [Documentação API REST do Azure Mapas](/rest/api/maps)