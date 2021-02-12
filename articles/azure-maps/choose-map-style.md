---
title: Alterar o estilo do Controle de Mapeamento Web do Azure Maps
description: Saiba como alterar o estilo e as opções de um mapa. Veja como adicionar um controle de seletor de estilo a um mapa no Azure Maps para que os usuários possam alternar entre diferentes estilos.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/27/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: 556e265cc0d1aae33823185ec98d23f191ed1694
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/18/2020
ms.locfileid: "97680020"
---
# <a name="change-the-style-of-the-map"></a>Alterar o estilo do mapa

O controle de mapa dá suporte a várias [Opções de estilo](/javascript/api/azure-maps-control/atlas.styleoptions) de mapa e estilos de mapa de [base](supported-map-styles.md)diferentes. Todos os estilos podem ser definidos quando o controle de mapa está sendo inicializado. Ou, você pode definir estilos usando a função do controle de mapa `setStyle` . Este artigo mostra como usar essas opções de estilo para personalizar a aparência do mapa. Além disso, você aprenderá a implementar o controle seletor de estilo em seu mapa. O controle de seletor de estilo permite que o usuário alterne entre diferentes estilos de base.

## <a name="set-map-style-options"></a>Definir opções de estilo de mapa

As opções de estilo podem ser definidas durante a inicialização do controle da Web. Ou, você pode atualizar as opções de estilo chamando a função do controle de mapa `setStyle` . Para ver todas as opções de estilo disponíveis, consulte [Opções de estilo](/javascript/api/azure-maps-control/atlas.styleoptions).

```javascript
//Set the style options when creating the map.
var map = new atlas.Map('map', {
    renderWorldCopies: false,
    showBuildingModels: false,
    showLogo: true,
    showFeedbackLink: true,
    style: 'road'

    //Additional map options.
};

//Update the style options at anytime using `setStyle` function.
map.setStyle({
    renderWorldCopies: true,
    showBuildingModels: true,
    showLogo: false,
    showFeedbackLink: false
});
```

A ferramenta a seguir mostra como as opções de estilo diferentes alteram a forma como o mapa é renderizado. Para ver os prédios 3D, amplie o próximo de uma grande cidade.

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Opções de estilo de mapa" src="https://codepen.io/azuremaps/embed/eYNMjPb?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte as <a href='https://codepen.io/azuremaps/pen/eYNMjPb'>Opções de estilo de mapa</a> de caneta pelo Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="set-a-base-map-style"></a>Definir um estilo de mapa base

Você também pode inicializar o controle de mapa com um dos [estilos de mapa base](supported-map-styles.md) que estão disponíveis no SDK da Web. Em seguida, você pode usar a `setStyle` função para atualizar o estilo base com um estilo de mapa diferente.

### <a name="set-a-base-map-style-on-initialization"></a>Definir um estilo de mapa base na inicialização

Os estilos de base do controle de mapa podem ser definidos durante a inicialização. No código a seguir, a `style` opção do controle de mapa é definida como o [ `grayscale_dark` estilo de mapa base](supported-map-styles.md#grayscale_dark).  

```javascript
var map = new atlas.Map('map', {
    style: 'grayscale_dark',

    //Additional map options
);
```

<br/>

<iframe height='500' scrolling='no' title='Configurando o estilo no carregamento do mapa' src='//codepen.io/azuremaps/embed/WKOQRq/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/WKOQRq/'>Configurar estilo no carregamento do mapa</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="update-the-base-map-style"></a>Atualizar o estilo do mapa base

O estilo de mapa base pode ser atualizado usando a `setStyle` função e definindo a `style` opção como alterar para um estilo de mapa base diferente ou adicionar opções de estilo adicionais.

```javascript
map.setStyle({ style: 'satellite' });
```

No código a seguir, depois que uma instância de mapa é carregada, o estilo de mapa é atualizado de `grayscale_dark` para `satellite` usando a função [SetStyle](/javascript/api/azure-maps-control/atlas.map#setstyle-styleoptions-) .

<br/>

<iframe height='500' scrolling='no' title='Atualizar o estilo' src='//codepen.io/azuremaps/embed/yqXYzY/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/yqXYzY/'>Atualizar o estilo</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-the-style-picker-control"></a>Adicionar o controle seletor de estilo

O controle de seletor de estilo fornece um botão fácil de usar com o painel flutuante que pode ser usado pelo usuário final para alternar entre os estilos de base.

O seletor de estilo tem duas opções de layout diferentes: `icon` e `list` . Além disso, o seletor de estilo permite que você escolha duas opções de controle de seletor de estilo diferentes `style` : `light` e `dark` . Neste exemplo, o seletor de estilo usa o `icon` layout e exibe uma lista de seleção de estilos de mapa base na forma de ícones. O seletor de controle de estilo inclui o seguinte conjunto base de estilos: `["road", "grayscale_light", "grayscale_dark", "night", "road_shaded_relief"]` . Para obter mais informações sobre opções de controle de seletor de estilo, consulte [Opções de controle de estilo](/javascript/api/azure-maps-control/atlas.stylecontroloptions).

A imagem abaixo mostra o controle seletor de estilo exibido no `icon` layout.

:::image type="content" source="./media/choose-map-style/style-picker-icon-layout.png" alt-text="Layout do ícone do seletor de estilo":::

A imagem abaixo mostra o controle seletor de estilo exibido no `list` layout.

:::image type="content" source="./media/choose-map-style/style-picker-list-layout.png" alt-text="Layout da lista do seletor de estilo":::

> [!IMPORTANT]
> Por padrão, o controle seletor de estilo lista todos os estilos disponíveis no tipo de preço S0 dos mapas do Azure. Se você quiser reduzir o número de estilos nessa lista, passe uma matriz dos estilos que você deseja que apareça na lista na `mapStyle` opção do seletor de estilo. Se você estiver usando S1 e quiser mostrar todos os estilos disponíveis, defina a `mapStyles` opção do seletor de estilo como `"all"` .

O código a seguir mostra como substituir a lista de `mapStyles` estilos base padrão. Neste exemplo, estamos definindo a `mapStyles` opção para listar os estilos base que desejamos exibir pelo controle seletor de estilo.

<br/>

<iframe height='500' scrolling='no' title='Adicionar o selecionador de estilos' src='//codepen.io/azuremaps/embed/OwgyvG/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/OwgyvG/'>Atualizar o selecionador de estilos</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as classes e os métodos usados neste artigo:

> [!div class="nextstepaction"]
> [Map](/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [Estilo da](/javascript/api/azure-maps-control/atlas.styleoptions)

> [!div class="nextstepaction"]
> [StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol)

> [!div class="nextstepaction"]
> [StyleControlOptions](/javascript/api/azure-maps-control/atlas.stylecontroloptions)

Consulte os artigos a seguir para obter mais exemplos de código para adicionar aos seus mapas:

> [!div class="nextstepaction"]
> [Adicionar controles de mapa](map-add-controls.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de símbolo](map-add-pin.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de bolhas](map-add-bubble-layer.md)
