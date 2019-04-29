---
title: Funcionalidades de estilo de mapa no Azure Mapas | Microsoft Docs
description: Saiba mais sobre as funcionalidades relacionadas ao estilo do Azure Mapas.
author: walsehgal
ms.author: v-musehg
ms.date: 10/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: ffed12b9184c7b6a690c30db9826f031fe6c9f9b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60795892"
---
# <a name="choose-a-map-style-in-azure-maps"></a>Escolha um estilo de mapa no Azure Mapas

O Azure Mapas tem quatro estilos de mapas diferentes para escolher. Para saber mais sobre estilos de mapa, consulte [estilos de mapa com suporte no Azure Mapas](./supported-map-styles.md). Este artigo mostra como usar as funcionalidades relacionadas ao estilo para definir um estilo no carregamento do mapa, definir um novo estilo e usar o controle selecionador de estilos.

## <a name="set-style-on-map-load"></a>Definir estilo no carregamento do mapa

<iframe height='500' scrolling='no' title='Configurando o estilo no carregamento do mapa' src='//codepen.io/azuremaps/embed/WKOQRq/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/WKOQRq/'>Configurar estilo no carregamento do mapa</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

O bloco de código acima define uma chave de assinatura e cria um objeto de Mapa, com o estilo definido como grayscale_dark. Consulte [criar um mapa](./map-create.md) para obter instruções sobre como criar um mapa.

## <a name="update-the-style"></a>Atualizar o estilo

<iframe height='500' scrolling='no' title='Atualizar o estilo' src='//codepen.io/azuremaps/embed/yqXYzY/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/yqXYzY/'>Atualizar o estilo</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

O bloco de código acima define a chave de assinatura e cria um objeto de Mapa sem pré-definir o estilo. Consulte [criar um mapa](./map-create.md) para obter instruções sobre como criar um mapa.

O segundo bloco de código usa o método [setStyle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) do mapa para definir o estilo do mapa para satélite.

## <a name="add-the-style-picker"></a>Adicionar o selecionador de estilos

<iframe height='500' scrolling='no' title='Adicionar o selecionador de estilos' src='//codepen.io/azuremaps/embed/OwgyvG/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/OwgyvG/'>Atualizar o selecionador de estilos</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

O primeiro bloco de código no código acima define a chave de assinatura e cria um objeto de Mapa, o estilo de mapa é pré-configurado para grayscale_dark. Consulte [criar um mapa](./map-create.md) para obter instruções sobre como criar um mapa.

O segundo bloco de código constrói um selecionador de estilos usando o construtor [StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol) atlas.

Um selecionador de estilos permite a seleção de estilo para o mapa. O terceiro bloco de código adiciona o selecionador de estilos ao mapa usando o método [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest). O seletor de estilo está dentro do **ouvinte de eventos** para garantir que seja carregado após o carregamento total do mapa.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as classes e os métodos usados neste artigo:

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Adicione controle aos mapas:

> [!div class="nextstepaction"]
> [Adicionar controles de mapa](./map-add-controls.md)

Adicione um PIN ao mapa:

> [!div class="nextstepaction"]
> [Adicionar um PIN](./map-add-pin.md)
