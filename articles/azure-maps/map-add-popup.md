---
title: Adicionar um pop-up a um ponto em um mapa | Mapas do Microsoft Azure
description: Saiba mais sobre pop-ups, modelos pop-up e eventos pop-up no Azure Maps. Veja como adicionar um pop-up a um ponto em um mapa e como reutilizar e personalizar pop-ups.
author: anastasia-ms
ms.author: v-stharr
ms.date: 02/27/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 903574a8c8696f10d9903a685bab8d12b3e6395f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92891029"
---
# <a name="add-a-popup-to-the-map"></a>Adicionar um pop-up ao mapa

Este artigo mostra como adicionar um pop-up para um ponto em um mapa.

## <a name="understand-the-code"></a>Compreender o código

O código a seguir adiciona um recurso de ponto, que tem `name` e `description` Propriedades, ao mapa usando uma camada de símbolo. Uma instância da [classe Popup](/javascript/api/azure-maps-control/atlas.popup) é criada, mas não exibida. Os eventos do mouse são adicionados à camada de símbolo para disparar abertura e fechamento do Popup. Quando o símbolo de marcador é focalizado, a propriedade do Popup `position` é atualizada com a posição do marcador e a `content` opção é atualizada com algum HTML que encapsula as  `name` Propriedades e `description` do recurso de ponto que está sendo focalizado. Em seguida, o popup é exibido no mapa usando sua `open` função.

```javascript
//Define an HTML template for a custom popup content laypout.
var popupTemplate = '<div class="customInfobox"><div class="name">{name}</div>{description}</div>';

//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

dataSource.add(new atlas.data.Feature(new atlas.data.Point([-122.1333, 47.63]), {
  name: 'Microsoft Building 41', 
  description: '15571 NE 31st St, Redmond, WA 98052'
}));

//Create a layer to render point data.
var symbolLayer = new atlas.layer.SymbolLayer(dataSource);

//Add the polygon and line the symbol layer to the map.
map.layers.add(symbolLayer);

//Create a popup but leave it closed so we can update it and display it later.
popup = new atlas.Popup({
  pixelOffset: [0, -18],
  closeButton: false
});

//Add a hover event to the symbol layer.
map.events.add('mouseover', symbolLayer, function (e) {
  //Make sure that the point exists.
  if (e.shapes && e.shapes.length > 0) {
    var content, coordinate;
    var properties = e.shapes[0].getProperties();
    content = popupTemplate.replace(/{name}/g, properties.name).replace(/{description}/g, properties.description);
    coordinate = e.shapes[0].getCoordinates();

    popup.setOptions({
      //Update the content of the popup.
      content: content,

      //Update the popup's position with the symbol's coordinate.
      position: coordinate

    });
    //Open the popup.
    popup.open(map);
  }
});

map.events.add('mouseleave', symbolLayer, function (){
  popup.close();
});
```

Veja abaixo o exemplo de código completo em execução da funcionalidade acima.

<br/>

<iframe height='500' scrolling='no' title='Adicionar um pop até usando Azure Mapas' src='//codepen.io/azuremaps/embed/MPRPvz/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/MPRPvz/'>Adicionar um pop-up usando os Mapas do Azure</a> pelos Mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="reusing-a-popup-with-multiple-points"></a>Reutilizar um pop-up com vários pontos

Há casos em que a melhor abordagem é criar um pop-up e reutilizá-lo. Por exemplo, você pode ter um grande número de pontos e deseja mostrar apenas um pop-up por vez. Ao reutilizar o pop-up, o número de elementos DOM criados pelo aplicativo é muito reduzido, o que pode fornecer um melhor desempenho. O exemplo a seguir cria recursos de 3 pontos. Se você clicar em qualquer um deles, um pop-up será exibido com o conteúdo para esse recurso de ponto.

<br/>

<iframe height='500' scrolling='no' title='Reutilizar pop-up com vários marcadores' src='//codepen.io/azuremaps/embed/rQbjvK/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/rQbjvK/'>Reutilizar pop-up com vários marcadores</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customizing-a-popup"></a>Personalizando um pop-up

Por padrão, o pop-up tem um plano de fundo branco, uma seta de ponteiro na parte inferior e um botão fechar no canto superior direito. O exemplo a seguir altera a cor do plano de fundo para preto usando a `fillColor` opção do pop-up. O botão fechar é removido definindo a `CloseButton` opção como false. O conteúdo HTML do pop-up usa preenchido com 10 pixels das bordas do pop-up. O texto é tornado branco e, portanto, aparece bem no plano de fundo preto.  

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Pop-up personalizado" src="//codepen.io/azuremaps/embed/ymKgdg/?height=500&theme-id=0&default-tab=result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Consulte o <a href='https://codepen.io/azuremaps/pen/ymKgdg/'>Popup personalizado</a> da caneta pelo Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-popup-templates-to-the-map"></a>Adicionar modelos pop-up ao mapa

Os modelos de pop-up facilitam a criação de layouts controlados por dados para pop-ups. As seções a seguir demonstram o uso de vários modelos de pop-up para gerar conteúdo formatado usando as propriedades de recursos.

> [!NOTE]
> Por padrão, todo o conteúdo renderizado usar o modelo pop-up será colocado em área restrita dentro de um iframe como um recurso de segurança. No entanto, há limitações:
>
> - Todos os scripts, formulários, bloqueio de ponteiro e funcionalidade de navegação superior estão desabilitados. Os links podem ser abertos em uma nova guia quando clicado. 
> - Os navegadores mais antigos que não suportam o `srcdoc` parâmetro em IFrames serão limitados a renderizar uma pequena quantidade de conteúdo.
> 
> Se você confiar nos dados que estão sendo carregados nos pop-ups e potencialmente desejar que esses scripts carregados em pop-ups possam acessar seu aplicativo, você pode desabilitar isso definindo a opção de modelos pop-up `sandboxContent` como false. 

### <a name="string-template"></a>Modelo de cadeia de caracteres

O modelo de cadeia de caracteres substitui espaços reservados com valores das propriedades do recurso. As propriedades do recurso não precisam receber um valor do tipo cadeia de caracteres. Por exemplo, `value1` contém um inteiro. Esses valores são passados para a propriedade content do `popupTemplate` . 

A `numberFormat` opção especifica o formato do número a ser exibido. Se o `numberFormat` não for especificado, o código usará o formato de data de modelos pop-up. A `numberFormat` opção formata os números usando a função [Number. toLocaleString](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString) . Para formatar números grandes, considere usar a `numberFormat` opção com funções de [NumberFormat. Format](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/NumberFormat/format). Por exemplo, o trecho de código a seguir usa `maximumFractionDigits` para limitar o número de dígitos de fração a dois.

> [!Note]
> Há apenas uma maneira na qual o modelo de cadeia de caracteres pode renderizar imagens. Primeiro, o modelo de cadeia de caracteres precisa ter uma marca de imagem. O valor que está sendo passado para a marca de imagem deve ser uma URL para uma imagem. Em seguida, o modelo de cadeia de caracteres precisa ter `isImage` definido como true no `HyperLinkFormatOptions` . A `isImage` opção especifica que o hiperlink é para uma imagem e o hiperlink será carregado em uma marca de imagem. Quando o hiperlink for clicado, a imagem será aberta.

```javascript
var templateOptions = {
  content: 'This template uses a string template with placeholders.<br/><br/> - Value 1 = {value1}<br/> - Value 2 = {value2/subValue}<br/> - Array value [2] = {arrayValue/2}',
  numberFormat: {
    maximumFractionDigits: 2
  }
};

var feature = new atlas.data.Feature(new atlas.data.Point([0, 0]), {
    title: 'Template 1 - String template',
    value1: 1.2345678,
    value2: {
        subValue: 'Pizza'
    },
    arrayValue: [3, 4, 5, 6]
});

var popup = new atlas.Popup({
  content: atlas.PopupTemplate.applyTemplate(feature.properties, templateOptions),
  position: feature.geometry.coordinates
});
```

### <a name="propertyinfo-template"></a>Modelo PropertyInfo

O modelo PropertyInfo exibe as propriedades disponíveis do recurso. A `label` opção especifica o texto a ser exibido para o usuário. Se `label` não for especificado, o hiperlink será exibido. E, se o hiperlink for uma imagem, o valor atribuído à marca "Alt" será exibido. `dateFormat`Especifica o formato da data e, se o formato de data não for especificado, a data será renderizada como uma cadeia de caracteres. A `hyperlinkFormat` opção renderiza links clicáveis, da mesma forma, a `email` opção pode ser usada para renderizar endereços de email clicáveis.

Antes que o modelo PropertyInfo exiba as propriedades para o usuário final, ele verifica recursivamente que as propriedades são definidas de fato para esse recurso. Ele também ignora a exibição de propriedades de estilo e título. Por exemplo, ele não exibirá `color` , `size` , `anchor` , `strokeOpacity` e `visibility` . Assim, quando a verificação do caminho da propriedade for concluída no back-end, o modelo PropertyInfo mostrará o conteúdo em um formato de tabela.

```javascript
var templateOptions = {
  content: [
    {
        propertyPath: 'createDate',
        label: 'Created Date'
    },
    {
        propertyPath: 'dateNumber',
        label: 'Formatted date from number',
        dateFormat: {
          weekday: 'long',
          year: 'numeric',
          month: 'long',
          day: 'numeric',
          timeZone: 'UTC',
          timeZoneName: 'short'
        }
    },
    {
        propertyPath: 'url',
        label: 'Code samples',
        hideLabel: true,
        hyperlinkFormat: {
          lable: 'Go to code samples!',
          target: '_blank'
        }
    },
    {
        propertyPath: 'email',
        label: 'Email us',
        hideLabel: true,
        hyperlinkFormat: {
          target: '_blank',
          scheme: 'mailto:'
        }
    }
  ]
};

var feature = new atlas.data.Feature(new atlas.data.Point([0, 0]), {
    title: 'Template 2 - PropertyInfo',
    createDate: new Date(),
    dateNumber: 1569880860542,
    url: 'https://aka.ms/AzureMapsSamples',
    email: 'info@microsoft.com'
}),

var popup = new atlas.Popup({
  content: atlas.PopupTemplate.applyTemplate(feature.properties, templateOptions),
  position: feature.geometry.coordinates
});
```

### <a name="multiple-content-templates"></a>Vários modelos de conteúdo

Um recurso também pode exibir conteúdo usando uma combinação do modelo de cadeia de caracteres e do modelo PropertyInfo. Nesse caso, o modelo de cadeia de caracteres renderiza valores de espaços reservados em um plano de fundo branco.  E o modelo PropertyInfo renderiza uma imagem de largura inteira dentro de uma tabela. As propriedades neste exemplo são semelhantes às propriedades que explicamos nos exemplos anteriores.

```javascript
var templateOptions = {
  content: [
    'This template has two pieces of content; a string template with placeholders and a array of property info which renders a full width image.<br/><br/> - Value 1 = {value1}<br/> - Value 2 = {value2/subValue}<br/> - Array value [2] = {arrayValue/2}',
    [{
      propertyPath: 'imageLink',
      label: 'Image',
      hideImageLabel: true,
      hyperlinkFormat: {
        isImage: true
      }
    }]
  ],
  numberFormat: {
    maximumFractionDigits: 2
  }
};

var feature = new atlas.data.Feature(new atlas.data.Point([0, 0]), {
    title: 'Template 3 - Multiple content template',
    value1: 1.2345678,
    value2: {
    subValue: 'Pizza'
    },
    arrayValue: [3, 4, 5, 6],
    imageLink: 'https://azuremapscodesamples.azurewebsites.net/common/images/Pike_Market.jpg'
});

var popup = new atlas.Popup({
  content: atlas.PopupTemplate.applyTemplate(feature.properties, templateOptions),
  position: feature.geometry.coordinates
});
```

### <a name="points-without-a-defined-template"></a>Pontos sem um modelo definido

Quando o modelo pop-up não está definido como um modelo de cadeia de caracteres, um modelo de PropertyInfo ou uma combinação de ambos, ele usa as configurações padrão. Quando `title` e `description` são as únicas propriedades atribuídas, o modelo pop-up mostra um plano de fundo branco, um botão fechar no canto superior direito. E, em telas pequenas e médias, ele mostra uma seta na parte inferior. As configurações padrão são exibidas dentro de uma tabela para todas as propriedades que não sejam o `title` e o `description` . Mesmo ao fazer fallback para as configurações padrão, o modelo pop-up ainda pode ser manipulado programaticamente. Por exemplo, os usuários podem desativar a detecção de hiperlink e as configurações padrão ainda se aplicam a outras propriedades.

Clique nos pontos no mapa no CodePen. Há um ponto no mapa para cada um dos seguintes modelos pop-up: modelo de cadeia de caracteres, modelo de PropertyInfo e vários modelos de conteúdo. Há também três pontos para mostrar como os modelos são renderizados usando as configurações padrão.

<br/>

<iframe height='500' scrolling='no' title='PopupTemplates' src='//codepen.io/azuremaps/embed/dyovrzL/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a <a href='https://codepen.io/azuremaps/pen/dyovrzL/'>PopupTemplates</a> de caneta por mapas do Azure ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="reuse-popup-template"></a>Reutilizar modelo pop-up

Semelhante a reutilizar o Popup, você pode reutilizar os modelos de pop-up. Essa abordagem é útil quando você deseja mostrar apenas um modelo pop-up por vez, para vários pontos. Ao reutilizar o modelo de pop-up, o número de elementos DOM criados pelo aplicativo é reduzido, o que melhora o desempenho do aplicativo. O exemplo a seguir usa o mesmo modelo de pop-up para três pontos. Se você clicar em qualquer um deles, um pop-up será exibido com o conteúdo para esse recurso de ponto.

<br/>

<iframe height='500' scrolling='no' title='ReusePopupTemplate' src='//codepen.io/azuremaps/embed/WNvjxGw/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a <a href='https://codepen.io/azuremaps/pen/WNvjxGw/'>ReusePopupTemplate</a> de caneta por mapas do Azure ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="popup-events"></a>Eventos pop-up

Os pop-ups podem ser abertos, fechados e arrastados. A classe Popup fornece eventos para ajudar os desenvolvedores a reagir a esses eventos. O exemplo a seguir realça quais eventos são acionados quando o usuário abre, fecha ou arrasta o pop-up. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Eventos pop-up" src="//codepen.io/azuremaps/embed/BXrpvB/?height=500&theme-id=0&default-tab=result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Consulte os <a href='https://codepen.io/azuremaps/pen/BXrpvB/'>eventos de pop-up</a> de caneta pelo Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [Pop-up](/javascript/api/azure-maps-control/atlas.popup)

> [!div class="nextstepaction"]
> [PopupOptions](/javascript/api/azure-maps-control/atlas.popupoptions)

> [!div class="nextstepaction"]
> [Popuptemplate](/javascript/api/azure-maps-control/atlas.popuptemplate)

Consulte os seguintes artigos excelentes para obter exemplos de código completo:

> [!div class="nextstepaction"]
> [Adicionar uma camada de símbolo](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Adicionar um marcador HTML](./map-add-custom-html.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de linha](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de polígono](map-add-shape.md)