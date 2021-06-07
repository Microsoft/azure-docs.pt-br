---
title: Manipular eventos de mapa | Microsoft Azure Mapas
description: Saiba quais eventos são acionados quando os usuários interagem com mapas. Exiba uma lista de todos os eventos de mapa com suporte. Consulte como usar o SDK da Web do Azure Maps para manipular eventos.
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/10/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 8121ceb68bdea9332316a9508bf6f4731e05b0ba
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92890778"
---
# <a name="interact-with-the-map"></a>Interagir com o mapa

Este artigo mostra como usar a [classe de evento de mapa](/javascript/api/azure-maps-control/atlas.map#events). A propriedade realça os eventos no mapa e em camadas diferentes do mapa. Você também pode realçar eventos quando interage com um marcador HTML.

## <a name="interact-with-the-map"></a>Interagir com o mapa

Percorra o mapa abaixo e visualize os eventos de mouse correspondentes realçados à direita. Você pode clicar na **guia JS** para visualizar e editar o código JavaScript. Você também pode clicar no botão **Editar no CodePen** para modificar o código no CodePen.

<br/>

<iframe height='600' scrolling='no' title='Interagir com o mapa – eventos do mouse' src='//codepen.io/azuremaps/embed/bLZEWd/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja a Caneta <a href='https://codepen.io/azuremaps/pen/bLZEWd/'>Interagir com os eventos do mapa do mouse</a> pelos Mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'> CodePen</a>.
</iframe>

## <a name="interact-with-map-layers"></a>Interagir com as camadas do mapa

O código a seguir realça o evento acionado enquanto você interage com a camada de símbolo. A camada de polígono, bolha, linha e símbolo dão suporte ao mesmo conjunto de eventos. As camadas de mapa de calor e bloco não dão suporte a nenhum desses eventos.

<br/>

<iframe height='600' scrolling='no' title='Interação com o mapa – eventos de camada' src='//codepen.io/azuremaps/embed/bQRRPE/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/bQRRPE/'>Interação com o mapa – eventos de camada</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="interact-with-html-marker"></a>Interagir com o marcador HTML

O código a seguir adiciona eventos de mapa JavaScript a um marcador HTML. Ele também destaca o nome dos eventos que são acionados conforme você interage com o marcador HTML.

<br/>

<iframe height='500' scrolling='no' title='Interação com o mapa – eventos de marcador HTML' src='//codepen.io/azuremaps/embed/VVzKJY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/VVzKJY/'>Interação com o mapa – eventos de marcador HTML</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

A tabela a seguir lista todos os eventos de classe de mapa compatíveis.

| Evento               | Descrição |
|---------------------|-------------|
| `boxzoomend`        | Acionado quando uma interação de "zoom da caixa de diálogo" termina.|
| `boxzoomstart`      | Acionado quando uma interação de "zoom da caixa de diálogo" começa.|
| `click`             | Acionado quando um dispositivo apontador é pressionado e liberado no mesmo ponto no mapa.|
| `close`             | Acionado quando o popup é fechado manualmente ou programaticamente.|
| `contextmenu`       | Acionado quando o botão direito do mouse é clicado.|
| `data`              | Acionado quando os dados do mapa são carregados ou alterados. |
| `dataadded`         | Acionado quando formas são adicionadas à `DataSource`.|
| `dataremoved`       | Acionado quando formas são removidas da `DataSource`.|
| `datasourceupdated` | Acionado quando o objeto `DataSource` é atualizado.|
| `dblclick`          | Acionado quando um dispositivo apontador é clicado duas vezes no mesmo ponto no mapa.|
| `drag`              | Acionado repetidamente durante uma interação "arrastar para panorâmica" no mapa, pop-up ou marcador HTML.|
| `dragend`           | Acionado quando uma interação "arrastar para panorâmica" termina no mapa, pop-up ou marcador HTML.|
| `dragstart`         | Acionado quando uma interação "arrastar para panorâmica" começa no mapa, pop-up ou marcador HTML.|
| `error`             | Acionado quando um erro ocorre.|
| `idle`              | <p>Acionado após o último quadro renderizado antes do mapa entrar em um estado "ocioso":<ul><li>Nenhuma transição de câmera está em andamento.</li><li>Todos os blocos atualmente solicitados foram carregados.</li><li>Todas as animações de esmaecimento/transição foram concluídas.</li></ul></p>|
| `keydown`           | Acionado quando uma tecla é pressionada.|
| `keypress`          | Acionado quando uma tecla que produz um caractere digitável (uma tecla ANSI) é pressionada.|
| `keyup`             | Acionado quando uma tecla é solta.|
| `layeradded`        | Acionado quando uma camada é adicionada ao mapa.|
| `layerremoved`      | Acionado quando uma camada é removida do mapa.|
| `load`              | Acionado imediatamente depois que todos os recursos necessários tiverem sido baixados e a primeira renderização visualmente completa do mapa tiver ocorrido.|
| `mousedown`         | Acionado quando um dispositivo apontador é pressionado no mapa ou sobre um elemento.|
| `mouseenter`        | Acionado quando um dispositivo apontador é movido inicialmente sobre o mapa ou sobre um elemento. |
| `mouseleave`        | Acionado quando um dispositivo apontador é movido para fora do mapa ou de um elemento. |
| `mousemove`         | Acionado quando um dispositivo apontador é movido no mapa ou em um elemento.|
| `mouseout`          | Acionado quando um dispositivo apontador deixa a tela do mapa ou deixa um elemento.|
| `mouseover`         | Acionado quando um dispositivo apontador é movido sobre o mapa ou sobre um elemento.|
| `mouseup`           | Acionado quando um dispositivo apontador é liberado no mapa ou na parte superior de um elemento.|
| `move`              | Acionado repetidamente durante uma transição animada de uma exibição para outra, como resultado de métodos ou de interação do usuário.|
| `moveend`           | Acionado logo após o mapa concluir uma transição de uma exibição para outra, como resultado de métodos ou de interação do usuário.|
| `movestart`         | Acionado imediatamente antes de o mapa começar uma transição de uma exibição para outra, como resultado de métodos ou de interação do usuário.|
| `open`              | Acionado quando o popup é aberto manualmente ou programaticamente.|
| `pitch`             | Acionado sempre que a inclinação do mapa (declive) muda como resultado de métodos ou de interação do usuário.|
| `pitchend`          | Acionado imediatamente após a inclinação do mapa (declive) terminar de ser alterada como resultado de métodos ou de interação do usuário.|
| `pitchstart`        | Acionado sempre que a inclinação do mapa (declive) inicia uma alteração como resultado de métodos ou de interação do usuário.|
| `ready`             | Acionado quando os recursos de mapa mínimos necessários são carregados antes que o mapa esteja pronto para interação programática.|
| `render`            | <p>Acionado sempre que o mapa é desenhado na tela, como resultado de:<ul><li>Uma alteração na posição, no zoom, na inclinação ou no rumo do mapa.</li><li>Uma alteração no estilo do mapa.</li><li>Uma alteração em uma fonte `DataSource`.</li><li>O carregamento de um bloco de vetor, arquivo GeoJSON, glifo ou sprite.</li></ul></p>|
| `resize`            | Acionado imediatamente após o mapa ter sido redimensionado.|
| `rotate`            | Acionado repetidamente durante uma interação "arrastar para girar".|
| `rotateend`         | Acionado quando uma interação "arrastar para girar" termina.|
| `rotatestart`       | Acionado quando uma interação "arrastar para girar" é iniciada.|
| `shapechanged`      | Acionado quando uma propriedade de objeto de conformação é alterada.|
| `sourcedata`        | Acionado quando uma das origens do mapa é carregada ou alterada, incluindo quando um bloco pertencente a uma origem é carregado ou alterado. |
| `sourceadded`       | Acionado quando uma `DataSource` ou `VectorTileSource` é adicionada ao mapa.|
| `sourceremoved`     | Acionado quando uma `DataSource` ou `VectorTileSource` é removida do mapa.|
| `styledata`         | Acionado quando o estilo do mapa é carregado ou alterado.|
| `styleimagemissing` | Acionado quando uma camada tenta carregar, de um sprite de imagens, uma imagem que não existe |
| `tokenacquired`     | Acionado quando um token de acesso de Azure Active Directory é obtido.|
| `touchcancel`       | Acionado quando `touchcancel` ocorre um evento dentro do mapa.|
| `touchend`          | Acionado quando `touchend` ocorre um evento dentro do mapa.|
| `touchmove`         | Acionado quando `touchmove` ocorre um evento dentro do mapa.|
| `touchstart`        | Acionado quando `touchstart` ocorre um evento dentro do mapa.|
| `wheel`             | Acionado quando um evento de roda do mouse ocorre dentro do mapa.|
| `zoom`              | Acionado repetidamente durante uma transição animada de um nível de zoom para outro, como resultado de métodos ou de interação do usuário.|
| `zoomend`           | Acionado logo após o mapa concluir uma transição de um nível de zoom para outro, como resultado de métodos ou de interação do usuário.|
| `zoomstart`         | Acionado imediatamente antes de o mapa começar uma transição de um nível de zoom para outro, como resultado de métodos ou de interação do usuário.|


## <a name="next-steps"></a>Próximas etapas

Consulte os seguintes artigos para obter exemplos de código completo:

> [!div class="nextstepaction"]
> [Como usar o módulo de Serviços do Azure Mapas](./how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Exemplos de código](/samples/browse/?products=azure-maps)