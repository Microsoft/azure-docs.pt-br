---
title: Noções básicas sobre camadas no Azure Maps Power BI Visual | Mapas do Microsoft Azure
description: Neste artigo, você aprenderá sobre as diferentes camadas disponíveis no Visual do Microsoft Azure Maps para Power BI.
author: rbrundritt
ms.author: richbrun
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: a5c7296a0e7b7f6ea33c1f4a669675efd90f9e9a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "86261590"
---
# <a name="understanding-layers-in-the-azure-maps-power-bi-visual"></a>Noções básicas sobre camadas no Azure Maps Power BI Visual

Há dois tipos de camadas disponíveis no Visual do Azure Maps. O primeiro tipo enfoca a renderização de dados que são passados para o painel **campos** do Visual e consiste nas camadas a seguir, vamos chamar essas camadas de renderização de dados.

:::row:::
    :::column span="":::
        **Camada de bolhas**

        Renderiza pontos como círculos dimensionados no mapa.

        ![Camada de bolha no mapa](media/power-bi-visual/bubble-layer-thumb.png)
    :::column-end:::
    :::column span="":::
        **Camada do gráfico de barras**

        Renderiza pontos como barras 3D no mapa.
        
        ![Camada do gráfico de barras no mapa](media/power-bi-visual/bar-chart-layer-thumb.png)
    :::column-end:::
:::row-end:::

O segundo tipo de camada conecta adição de fontes externas de dados para mapear para fornecer mais contexto e consiste nas camadas a seguir.

:::row:::
    :::column span="":::
        **Camada de referência**

        Sobreponha um arquivo geojson carregado na parte superior do mapa.

        ![Camada de referência no mapa](media/power-bi-visual/reference-layer-thumb.png)
    :::column-end:::
    :::column span="":::
        **Camada de peça**

        Sobreponha uma camada de bloco personalizada na parte superior do mapa.
        
        ![Camada do bloco no mapa](media/power-bi-visual/tile-layer-thumb.png)
    :::column-end:::
    :::column span="":::
        **Camada de tráfego**

        Sobreponha as informações de tráfego em tempo real no mapa.
        
        ![Camada de tráfego no mapa](media/power-bi-visual/traffic-layer-thumb.png)
    :::column-end:::
:::row-end:::

Todas as camadas de renderização de dados, bem como a **camada de peça**, têm opções para os níveis de zoom mínimo e máximo usados para especificar um intervalo de nível de zoom em que essas camadas devem ser exibidas. Isso permite que um tipo de camada de renderização seja usado em um nível de zoom e uma transição para outra camada de renderização em outro nível de zoom.

Essas camadas também têm a opção de ser posicionada em relação a outras camadas no mapa. Quando várias camadas de renderização de dados são usadas, a ordem na qual elas são adicionadas ao mapa determina sua ordem de camada relativa quando eles têm o mesmo valor de **posição de camada** .

## <a name="general-layer-settings"></a>Configurações de camada geral

A seção camada geral do painel **formato** são configurações comuns que se aplicam às camadas que estão conectadas ao conjunto de Power bi DataSet no painel **campos** (camada de bolha, gráfico de barras).

| Configuração     | Descrição   |
|-------------|---------------|
| Transparência não selecionada | A transparência das formas que não estão selecionadas, quando uma ou mais formas são selecionadas.  |
| Mostrar zeros              | Especifica se os pontos que têm um valor de tamanho igual a zero devem ser mostrados no mapa usando o raio mínimo. |
| Mostrar negativos          | Especifica se o valor absoluto dos valores de tamanho negativo deve ser plotado.   |
| Valor mínimo de dados          | O valor mínimo dos dados de entrada para dimensionar. Boa para exceções de recorte.  |
| Valor máximo de dados          | O valor máximo dos dados de entrada a serem dimensionados. Boa para exceções de recorte.  |

## <a name="next-steps"></a>Próximas etapas

Altere o modo como os dados são exibidos no mapa:

> [!div class="nextstepaction"]
> [Adicionar uma camada de bolhas](power-bi-visual-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de gráfico de barras](power-bi-visual-add-bar-chart-layer.md)

Adicione mais contexto ao mapa:

> [!div class="nextstepaction"]
> [Adicionar uma camada de referência](power-bi-visual-add-reference-layer.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de bloco](power-bi-visual-add-tile-layer.md)

> [!div class="nextstepaction"]
> [Mostrar tráfego em tempo real](power-bi-visual-show-real-time-traffic.md)
