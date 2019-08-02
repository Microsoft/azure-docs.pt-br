---
title: Como exibir o uso da API de Mapas do Azure | Microsoft Docs
description: Saiba como exibir as métricas para suas chamadas de API do Azure Mapas no portal.
author: walsehgal
ms.author: v-musehg
ms.date: 08/06/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 716f9fbc02905a8981771c2fd9dd2be0baeb8196
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67460185"
---
# <a name="view-azure-maps-api-usage"></a>Exibir o uso de API de mapas do Azure

Este artigo mostra como exibir as métricas de uso da API para sua conta do Azure Mapas no [portal](https://portal.azure.com). As métricas são mostradas em um formato gráfico conveniente ao longo de uma duração de tempo personalizáveis.

## <a name="view-metric-snapshot"></a>Exibir instantâneo de métrica

Você pode ver algumas métricas comuns sobre a página de **visão geral** de sua conta de mapas. Atualmente, ele mostra *Total de Solicitações*, *Total de Erros*, e *Disponibilidade* em um período de tempo selecionável.

![Visão geral das métricas do Azure Mapas](media/how-to-view-api-usage/portal-overview.png)

Continue para a próxima seção se você precisar personalizar esses gráficos para sua análise específica.

## <a name="view-detailed-metrics"></a>Exibir métricas detalhadas

1. Entre em sua assinatura do Azure no [portal](https://portal.azure.com).

2. Clique o **Todos os recursos** no lado esquerdo do item de menu e navegue até a sua *conta do Azure Mapas*.

3. Quando sua conta de Mapas for aberta, clique no menu **Métricas** à esquerda.

4. Sobre o painel **Métricas** painel, escolha entre um dos seguintes:

   1. **Disponibilidade** - que mostra a *Média* da disponibilidade da API durante um período de tempo.
   2. **Uso** -que mostra como o uso *contagem* para sua conta.

      ![Métricas do Azure Mapas](media/how-to-view-api-usage/portal-metrics.png)

5. Em seguida, você pode selecionar *Intervalo de tempo* clicando em **Últimas 24 horas (Automático)** . Por padrão, o intervalo de tempo é definido como 24 horas. Depois de clicar, você verá todos os intervalos de tempo selecionáveis. Você pode selecionar *Granularidade de tempo* e optar por mostrar a hora como *local* ou *GMT* no mesmo menu suspenso. Clique em **Aplicar**.

    ![Intervalo de tempo das métricas do Azure Mapas](media/how-to-view-api-usage/time-range.png)

6. Depois de adicionar sua métrica, você pode então **Adicionar filtro** entre as propriedades relevantes dessa métrica e, em seguida, selecione o valor da propriedade que você deseja ver o gráfico.

    ![Filtro de Métricas do Azure Mapas](media/how-to-view-api-usage/filter.png)

7. Você também pode **Aplicar a divisão** para sua métrica com base em sua propriedade de métrica selecionada. Isso permite que o gráfico seja dividido em vários gráficos, cada um para cada valor da propriedade. Na figura a seguir, a cor de cada gráfico corresponde ao valor da propriedade mostrado na parte inferior do gráfico.

    ![Divisão das Métricas do Azure Mapas](media/how-to-view-api-usage/splitting.png)

8. Você também pode observar várias métricas no mesmo gráfico, simplesmente clicando no botão **Adicionar métrica** na parte superior.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as APIs do Azure Maps para as quais você deseja acompanhar o uso:
> [!div class="nextstepaction"] 
> [Instruções SDK Web do Azure mapas](how-to-use-map-control.md)

> [!div class="nextstepaction"] 
> [Instruções do SDK do Android do Azure mapas](how-to-use-android-map-control-library.md)

> [!div class="nextstepaction"]
> [Documentação API REST do Azure Mapas](https://docs.microsoft.com/rest/api/maps)
