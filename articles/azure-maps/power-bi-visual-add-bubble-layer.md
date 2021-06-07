---
title: Adicionar uma camada de bolha ao Azure Maps Power BI Visual | Mapas do Microsoft Azure
description: Neste artigo, você aprenderá a usar a camada de bolha no Visual do Microsoft Azure Maps para Power BI.
author: rbrundritt
ms.author: richbrun
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: ff0d2c8e90995222af79d5be63ca3e9da24b3c45
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92896218"
---
# <a name="add-a-bubble-layer"></a>Adicionar uma camada de bolha

A **camada de bolha** renderiza os dados de localização como círculos em escala no mapa.

> [!div class="mx-imgBorder"]
> ![Um mapa que exibe dados do ponto usando a camada de bolha](media/power-bi-visual/bubble-layer-with-legend-color.png)

Inicialmente, todas as bolhas têm a mesma cor de preenchimento. Se um campo for passado para o Bucket de **legenda** do painel **campos** , as bolhas serão coloridas com base em sua categorização. O contorno das bolhas é branco como padrão, mas pode ser alterado para uma nova cor ou habilitando a opção de estrutura de tópicos de alto contraste. A opção de **estrutura de tópicos de alto contraste** atribui dinamicamente uma cor de contorno que é uma variante de alto contraste da cor de preenchimento. Isso ajuda a garantir que as bolhas sejam claramente visíveis, independentemente do estilo do mapa. A seguir estão as configurações principais no painel **formato** que estão disponíveis na seção **camada de bolha** .

| Configuração               | Descrição    |
|-----------------------|----------------|
| Tamanho                  | O tamanho de cada bolha. Essa opção fica oculta quando um campo é passado para o Bucket de **tamanho** do painel **campos** . Opções adicionais serão exibidas conforme descrito no tópico escala de [tamanho de bolha](#bubble-size-scaling) mais adiante neste artigo. |
| Cor de preenchimento            | Cor de cada bolha. Essa opção fica oculta quando um campo é passado para o Bucket de **legenda** do painel **campos** e uma seção **cores de dados** separada aparece no painel **formato** . |
| Transparência de preenchimento     | Transparência de cada bolha. |
| Estrutura de tópicos de alto contraste | Faz com que a cor da estrutura de tópicos contraste com a cor de preenchimento para melhor acessibilidade usando uma variante de alto contraste da cor de preenchimento. |
| Cor do contorno         | Cor que descreve a bolha. Essa opção fica oculta quando a opção de **estrutura de tópicos de alto contraste** está habilitada. |
| Transparência da estrutura de tópicos  | Transparência da estrutura de tópicos. |
| Largura da estrutura de tópicos         | Largura da estrutura de tópicos em pixels. |
| Desfoque                  | A quantidade de desfoque aplicada ao contorno. Um valor de 1 desfoca as bolhas de modo que apenas o ponto central não tenha transparência. Um valor de 0 aplica qualquer efeito de desfoque. |
| Alinhamento de densidade       | Especifica a aparência das bolhas quando o mapa é arestado. <br/><br/>&nbsp;&nbsp;&nbsp;&nbsp;• As bolhas de visor aparecem em sua borda no mapa em relação ao visor. (padrão)<br/>&nbsp;&nbsp;&nbsp;&nbsp;• As bolhas de mapa são renderizadas na superfície do mapa. |
| Escala de zoom            | A quantidade de bolhas deve ser dimensionada em relação ao nível de zoom. Uma escala de zoom de um significa que não há dimensionamento. Valores grandes farão as bolhas menores quando forem ampliados e maiores quando forem ampliados. Isso ajuda a reduzir a desordem no mapa quando o zoom é reduzido, mas garante que os pontos se destaquem mais quando são ampliados. Um valor de 1 não aplica nenhum dimensionamento. |
| Zoom mínimo              | Os blocos de nível de zoom mínimo estão disponíveis. |
| Zoom máximo              | Os blocos de nível de zoom máximo estão disponíveis. |
| Posição da camada        | Especifica a posição da camada em relação a outras camadas do mapa. |

## <a name="bubble-size-scaling"></a>Escala de tamanho de bolha

Se um campo for passado para o Bucket de **tamanho** do painel **campos** , as bolhas serão dimensionadas de forma relativa ao valor de medida de cada ponto de dados. A opção **tamanho** na seção **camada de bolha** do painel **Formatar** desaparecerá quando um campo for passado para o Bucket de **tamanho** , pois as bolhas terão seus raios dimensionados entre um valor mínimo e máximo. As opções a seguir serão exibidas na seção **camada de bolha** do painel **Formatar** quando um Bucket de **tamanho** tiver um campo especificado.

| Configuração             | Descrição  |
|---------------------|--------------|
| Tamanho mínimo            | Tamanho mínimo da bolha ao dimensionar os dados.|
| Tamanho máx.            | Tamanho máximo da bolha ao dimensionar os dados.|
| Método de dimensionamento de tamanho | Algoritmo de dimensionamento usado para determinar o tamanho relativo da bolha.<br/><br/>&nbsp;&nbsp;&nbsp;&nbsp;• Intervalo linear de dados de entrada mapeados linearmente para o tamanho mínimo e máximo. (padrão)<br/>&nbsp;&nbsp;&nbsp;&nbsp;• Intervalo de log dos dados de entrada logaritmicamente mapeados para o tamanho mínimo e máximo.<br/>&nbsp;&nbsp;&nbsp;&nbsp;• Cubic-Bezier-especifique os valores X1, y1, X2, Y2 de uma curva de Cubic-Bezier para criar um método de dimensionamento personalizado. |

Quando o **método de dimensionamento de tamanho** for definido como **log**, as opções a seguir serão disponibilizadas.

| Configuração   | Descrição      |
|-----------|------------------|
| Escala de log | A escala logarítmica a ser aplicada ao calcular o tamanho das bolhas. |

Quando o **método de dimensionamento de tamanho** for definido como **Bezier cúbico**, as opções a seguir serão disponibilizadas para personalizar a curva de dimensionamento.

| Configuração | Descrição                           |
|---------|---------------------------------------|
| X1      | O parâmetro X1 de uma curva de Bézier cúbica. |
| S1      | X2 parâmetro de uma curva de Bézier cúbica. |
| X2      | Parâmetro Y1 de uma curva de Bézier cúbica. |
| S2      | Parâmetro Y2 de uma curva Bézier cúbica. |

> [!TIP]
> [https://cubic-bezier.com/](https://cubic-bezier.com/) tem uma ferramenta útil para criar os parâmetros para Cubic-Bezier curvas.

## <a name="next-steps"></a>Próximas etapas

Altere o modo como os dados são exibidos no mapa:

> [!div class="nextstepaction"]
> [Adicionar uma camada de gráfico de barras](power-bi-visual-add-bar-chart-layer.md)

Adicione mais contexto ao mapa:

> [!div class="nextstepaction"]
> [Adicionar uma camada de referência](power-bi-visual-add-reference-layer.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de bloco](power-bi-visual-add-tile-layer.md)

> [!div class="nextstepaction"]
> [Mostrar tráfego em tempo real](power-bi-visual-show-real-time-traffic.md)

Personalize o Visual:

> [!div class="nextstepaction"]
> [Dicas e truques para formatação de cores no Power BI](/power-bi/visuals/service-tips-and-tricks-for-color-formatting)

> [!div class="nextstepaction"]
> [Personalizar títulos, telas de fundo e legendas de visualizações](/power-bi/visuals/power-bi-visualization-customize-title-background-and-legend)