---
title: Adicionar uma camada de gráfico de barras ao Azure Maps Power BI Visual | Mapas do Microsoft Azure
description: Neste artigo, você aprenderá a usar a camada de gráfico de barras no Visual do Microsoft Azure Maps para Power BI.
author: rbrundritt
ms.author: richbrun
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 136676b46df6e32f98dca99fccba19d4aa369dbe
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92896269"
---
# <a name="add-a-bar-chart-layer"></a>Adicionar uma camada de gráfico de barras

A **camada do gráfico de barras** é útil para levar dados para a próxima dimensão, permitindo a visualização de dados de localização como barras 3D ou cilindros no mapa. Semelhante à camada de bolha, o gráfico de barras mais tarde pode facilmente Visualizar duas métricas ao mesmo tempo usando a altura de cor e relativa. Para que as barras tenham altura, uma medida precisa ser adicionada ao Bucket de **tamanho** do painel **campos** . Se uma medida não for fornecida, as barras sem altura serão mostradas como quadrados simples ou círculos, dependendo da opção de **forma de barra** .

> [!div class="mx-imgBorder"]
> ![Um mapa que exibe dados do ponto usando a camada do gráfico de barras](media/power-bi-visual/bar-chart-layer-styled.png)

Os usuários podem inclinar e girar o mapa para exibir seus dados de diferentes perspectivas. O mapa pode ser inclinado ou potimbre usando um dos métodos a seguir.

-   Ative a opção **controles de navegação** nas **configurações de mapa** do painel **Formatar** . Isso adicionará um botão para inclinar o mapa.
-   Pressione o botão direito do mouse para baixo e arraste o mouse para cima ou para baixo.
-   Com a tela de toque, toque no mapa com dois dedos e arraste-os para cima ou para baixo juntos.
-   Com o mapa focalizado, mantenha a tecla **Shift** pressionada e pressione as teclas de seta para **cima** ou **para baixo** .

O mapa pode ser girado usando um dos métodos a seguir.

-   Ative a opção **controles de navegação** nas **configurações de mapa** do painel **Formatar** . Isso adicionará um botão para girar o mapa.
-   Pressione o botão direito do mouse para baixo e arraste o mouse para a esquerda ou para a direita.
-   Com a tela touch, toque no mapa com dois dedos e gire.
-   Com o mapa focalizado, mantenha a tecla **Shift** pressionada e pressione as teclas de **seta para** a **esquerda** ou para a direita.

A seguir estão todas as configurações no painel **Formatar** que estão disponíveis na seção **camada do gráfico de barras** .

| Configuração              | Descrição      |
|----------------------|------------------|
| Forma de barra            | A forma da barra 3D.<br/><br/>&nbsp;&nbsp;&nbsp;&nbsp;• Box – barras renderizadas como caixas retangulares.<br/>&nbsp;&nbsp;&nbsp;&nbsp;• Cilindro – barras renderizadas como cilindros. |
| Altura               | A altura de cada barra. Se um campo for passado para o Bucket de **tamanho** do painel **campos** , as barras serão dimensionadas em relação a esse valor de altura. |
| Dimensionar altura no zoom | Especifica se a altura das barras deve ser dimensionada em relação ao nível de zoom. |
| Largura                | A largura de cada barra.  |
| Dimensionar largura no zoom  | Especifica se a largura das barras deve ser dimensionada em relação ao nível de zoom.  |
| Cor de preenchimento           | Cor de cada barra. Essa opção fica oculta quando um campo é passado para o Bucket de **legenda** do painel **campos** e uma seção **cores de dados** separada aparece no painel **formato** . |
| Transparência         | Transparência de cada barra. |
| Zoom mínimo             | Os blocos de nível de zoom mínimo estão disponíveis. |
| Zoom máximo             | Os blocos de nível de zoom máximo estão disponíveis. |
| Posição da camada       | Especifica a posição da camada em relação a outras camadas do mapa. |

> [!NOTE]
> Se as barras tiverem um valor de largura pequena e a opção **largura da escala em zoom** estiver desabilitada, ela poderá desaparecer quando for ampliada muito, pois a largura renderizada será menor do que um pixel em tamanho. No entanto, quando a opção **dimensionar largura em zoom** está habilitada, cálculos adicionais são executados quando o nível de zoom é alterado, o que pode afetar o desempenho de grandes conjuntos de dados.

## <a name="next-steps"></a>Próximas etapas

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