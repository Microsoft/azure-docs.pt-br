---
title: Eventos da ferramenta de desenho | Mapas do Microsoft Azure
description: Neste artigo, você aprenderá a adicionar uma barra de ferramentas de desenho a um mapa usando o SDK da Web do Microsoft Azure Mapas
author: rbrundritt
ms.author: richbrun
ms.date: 12/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 6abe0ed88adbdf8263aa27d340fb2fff156d98e6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "90089336"
---
# <a name="drawing-tool-events"></a>Eventos da ferramenta de desenho

Ao usar ferramentas de desenho em um mapa, é útil responder a alguns eventos enquanto o usuário desenha no mapa. Esta tabela lista todos os eventos compatíveis com a classe `DrawingManager`.

| Evento | Descrição |
|-------|-------------|
| `drawingchanged` | Acionado quando qualquer coordenada em uma forma é adicionada ou alterada. | 
| `drawingchanging` | Acionado quando qualquer coordenada de visualização em uma forma está sendo exibida. Por exemplo, esse evento é acionado várias vezes enquanto uma coordenada é arrastada. | 
| `drawingcomplete` | Acionado quando uma forma termina de ser desenhada ou retirada do modo de edição. |
| `drawingmodechanged` | Acionado quando o modo de desenho é alterado. O novo modo de desenho é passado para o manipulador de eventos. |
| `drawingstarted` | Acionado quando o usuário começa a desenhar uma forma ou coloca uma forma no modo de edição.  |

O código a seguir mostra como funcionam os eventos no módulo de Ferramentas de Desenho. Desenhe formas no mapa e observe os eventos serem acionados.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Eventos de ferramentas de desenho" src="https://codepen.io/azuremaps/embed/dyPMRWo?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Confira os <a href='https://codepen.io/azuremaps/pen/dyPMRWo'>eventos de ferramentas de desenho</a> da Caneta dos Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

## <a name="examples"></a>Exemplos

Vamos ver alguns cenários comuns que usam os eventos de ferramentas de desenho.

### <a name="select-points-in-polygon-area"></a>Selecionar pontos na área de polígono

Este código demonstra como monitorar um evento de maneiras de desenho de um usuário. Neste exemplo, o código monitora formas de polígonos, retângulos e círculos. Em seguida, determina quais pontos de dados no mapa estão dentro da área desenhada. O evento `drawingcomplete` é usado para disparar a lógica de seleção. Na lógica de seleção, o código executa um loop em todos os pontos de dados no mapa. Ele verifica se há uma interseção do ponto e a área da forma desenhada. Este exemplo usa a biblioteca de software livre [Turf.js](https://turfjs.org/) para fazer um cálculo de interseção espacial.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Selecionar dados na área de polígono desenhada" src="https://codepen.io/azuremaps/embed/XWJdeja?height=500&theme-id=default&default-tab=result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Confira <a href='https://codepen.io/azuremaps/pen/XWJdeja'>Selecionar dados na área de polígono desenhada</a> da Caneta dos Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

### <a name="draw-and-search-in-polygon-area"></a>Desenho e pesquisa na área de polígono

Esse código procura pontos de interesse dentro da área de uma forma depois que o usuário termina de desenhar a forma. Modifique e execute o código clicando em 'Editar no CodePen' no canto superior direito do quadro. O evento `drawingcomplete` é usado para disparar a lógica de pesquisa. Se o usuário desenha um retângulo ou um polígono, uma pesquisa dentro da geometria é executada. Se um círculo é desenhado, a posição do raio e do centro é usada para realizar uma pesquisa de ponto de interesse. O evento `drawingmodechanged` é usado para determinar quando o usuário alterna para o modo de desenho e limpa a tela de desenho.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Desenho e pesquisa na área de polígono" src="https://codepen.io/azuremaps/embed/eYmZGNv?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Confira <a href='https://codepen.io/azuremaps/pen/eYmZGNv'>Desenho e pesquisa na área de polígono</a> da Caneta dos Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

### <a name="create-a-measuring-tool"></a>Criar uma ferramenta de medição

O código abaixo mostra como os eventos de desenho podem ser usados para criar uma ferramenta de medição. O `drawingchanging` é usado para monitorar a forma enquanto ela é desenhada. À medida que o usuário move o mouse, as dimensões da forma são calculadas. O evento `drawingcomplete` é usado para fazer um cálculo final na forma depois que ela é desenhada. O evento `drawingmodechanged` é usado para determinar quando o usuário alterna para um modo de desenho. Além disso, o evento `drawingmodechanged` limpa a tela de desenho e as informações de medição antigas.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Ferramenta de medição" src="https://codepen.io/azuremaps/embed/RwNaZXe?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Confira a <a href='https://codepen.io/azuremaps/pen/RwNaZXe'>Ferramenta de medição</a> da Caneta dos Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

## <a name="next-steps"></a>Próximas etapas

Saiba como usar recursos adicionais do módulo de ferramentas de desenho:

> [!div class="nextstepaction"]
> [Obter dados da forma](map-get-shape-data.md)

> [!div class="nextstepaction"]
> [Tipos de interação e atalhos de teclado](drawing-tools-interactions-keyboard-shortcuts.md)

Saiba mais sobre o módulo de Serviços:

> [!div class="nextstepaction"]
> [Módulo de serviços](how-to-use-services-module.md)

Confira mais exemplos de código:

> [!div class="nextstepaction"]
> [Página de exemplo de código](https://aka.ms/AzureMapsSamples)
