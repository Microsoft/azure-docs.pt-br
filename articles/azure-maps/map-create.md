---
title: Criar um mapa com mapas do Azure | Mapas do Microsoft Azure
description: Descubra como adicionar mapas a páginas da Web usando o SDK da Web do Azure Maps. Saiba mais sobre as opções de animação, estilo, câmera, serviços e interações do usuário.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 833b6413cc5dfde1129075a286e5fe93a06e159f
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92890914"
---
# <a name="create-a-map"></a>Criar um mapa

Este artigo mostra maneiras de criar um mapa e animar um mapa.  

## <a name="loading-a-map"></a>Carregando um mapa

Para carregar um mapa, crie uma nova instância da [classe Map](/javascript/api/azure-maps-control/atlas.map). Ao inicializar o mapa, passe uma ID do elemento DIV para renderizar o mapa e passar um conjunto de opções a serem usadas ao carregar o mapa. Se as informações de autenticação padrão não forem especificadas no `atlas` namespace, essas informações precisarão ser especificadas nas opções de mapa ao carregar o mapa. O mapa carrega vários recursos de forma assíncrona para o desempenho. Assim, depois de criar a instância de mapa, anexe `ready` um `load` evento ou ao mapa e, em seguida, adicione qualquer código adicional que interaja com o mapa para o manipulador de eventos. O `ready` evento é acionado assim que o mapa tem recursos suficientes carregados para interagir com programaticamente. O `load` evento é acionado depois que o modo de exibição de mapa inicial termina completamente de ser carregado. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Carregamento de mapa básico" src="//codepen.io/azuremaps/embed/rXdBXx/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Consulte o <a href='https://codepen.io/azuremaps/pen/rXdBXx/'>mapa básico</a> da caneta pelo mapas do Azure ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Você pode carregar vários mapas na mesma página. Vários mapas na mesma página podem usar a mesma configuração e configurações de idioma e autenticação diferentes.

## <a name="show-a-single-copy-of-the-world"></a>Mostrar uma única cópia do mundo

Quando o mapa for ampliado em uma tela larga, várias cópias do mundo aparecerão horizontalmente. Essa opção é excelente para alguns cenários, mas para outros aplicativos, é desejável ver uma única cópia do mundo. Esse comportamento é implementado definindo a opção Maps `renderWorldCopies` como `false` .

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="renderWorldCopies = false" src="//codepen.io/azuremaps/embed/eqMYpZ/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Confira a caneta <a href='https://codepen.io/azuremaps/pen/eqMYpZ/'>renderWorldCopies = false</a> pelo Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) em <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="map-options"></a>Opções de mapa

Ao criar um mapa, há vários tipos diferentes de opções que podem ser passados para personalizar como as funções de mapa são listadas abaixo.

- [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions) e [CameraBoundOptions](/javascript/api/azure-maps-control/atlas.cameraboundsoptions) são usados para especificar a área que o mapa deve exibir.
- As [serviceoptions](/javascript/api/azure-maps-control/atlas.serviceoptions) são usadas para especificar como o mapa deve interagir com os serviços que alimentam o mapa.
- As [styleoptions](/javascript/api/azure-maps-control/atlas.styleoptions) são usadas para especificar que o mapa deve ser estilizado e renderizado.
- [UserInteraction](/javascript/api/azure-maps-control/atlas.userinteractionoptions) é usado para especificar como o mapa deve alcançar quando o usuário está interagindo com o mapa. 

Essas opções também podem ser atualizadas depois que o mapa tiver sido carregado usando as `setCamera` `setServiceOptions` funções,, `setStyle` e `setUserInteraction` . 

## <a name="controlling-the-map-camera"></a>Controlando a câmera do mapa

Há duas maneiras de definir a área exibida do mapa usando a câmera de um mapa. Você pode definir as opções de câmera ao carregar o mapa. Ou, você pode chamar a `setCamera` opção a qualquer momento depois que o mapa for carregado para atualizar programaticamente a exibição do mapa.  

<a id="setCameraOptions"></a>

### <a name="set-the-camera"></a>Definir a câmera

A câmera do mapa controla o que é exibido no visor da tela do mapa. As opções de câmera podem ser passadas para as opções de mapa ao serem inicializadas ou passadas para a `setCamera` função Maps.

```javascript
//Set the camera options when creating the map.
var map = new atlas.Map('map', {
    center: [-122.33, 47.6],
    zoom: 12

    //Additional map options.
};

//Update the map camera at anytime using setCamera function.
map.setCamera({
    center: [-110, 45],
    zoom: 5 
});
```

No código a seguir, um [objeto MAP](/javascript/api/azure-maps-control/atlas.map) é criado e as opções Center e zoom são definidas. As propriedades do mapa, como nível de centro e zoom, fazem parte das [câmerasoptions](/javascript/api/azure-maps-control/atlas.cameraoptions).

<br/>

<iframe height='500' scrolling='no' title='Criar um mapa por meio de CameraOptions' src='//codepen.io/azuremaps/embed/qxKBMN/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja a Caneta <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>Crie um mapa via `CameraOptions` </a> pelos Serviços Baseados em Localização do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="setCameraBoundsOptions"></a>

### <a name="set-the-camera-bounds"></a>Defina os limites de câmera

Uma caixa delimitadora pode ser usada para atualizar a câmera do mapa. Se a caixa delimitadora foi calculada a partir de dados de ponto, geralmente é útil especificar também um valor de preenchimento de pixel nas opções da câmera para considerar o tamanho do ícone. Isso ajudará a garantir que os pontos não caiam na borda do visor do mapa.

```javascript
map.setCamera({
    bounds: [-122.4, 47.6, -122.3, 47.7],
    padding: 10
});
```

No código a seguir, um [objeto de mapa](/javascript/api/azure-maps-control/atlas.map) é construído por meio de `new atlas.Map()` . As propriedades do mapa, como `CameraBoundsOptions`, podem ser definidas por meio da função [ setCamera ](/javascript/api/azure-maps-control/atlas.map) da classe Map. As propriedades de limites e preenchimento são definidas usando `setCamera`.

<br/>

<iframe height='500' scrolling='no' title='Criar um mapa por meio de CameraBoundsOptions' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja a Caneta <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>Crie um mapa via `CameraBoundsOptions` </a> pelos Mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="animate-map-view"></a>Animar a exibição do mapa

Ao definir as opções de câmera do mapa, [as opções de animação](/javascript/api/azure-maps-control/atlas.animationoptions) também podem ser definidas. Essas opções especificam o tipo de animação e a duração que deve ser tomada para mover a câmera.

```javascript
map.setCamera({
    center: [-122.33, 47.6],
    zoom: 12,
    duration: 1000,
    type: 'fly'  
});
```

No código a seguir, o primeiro bloco de código cria um mapa e define os estilos de mapa de inserção e zoom. No segundo bloco de código, um manipulador de eventos de clique é criado para o botão de animação. Quando esse botão é clicado, a `setCamera` função é chamada com alguns valores aleatórios para as [câmerasoptions](/javascript/api/azure-maps-control/atlas.cameraoptions) e [animationoptions](/javascript/api/azure-maps-control/atlas.animationoptions).

<br/>

<iframe height='500' scrolling='no' title='Animar a exibição do mapa' src='//codepen.io/azuremaps/embed/WayvbO/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/WayvbO/'>animar a exibição do mapa</a> por mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="request-transforms"></a>Transformações de solicitação

Às vezes, é útil ser capaz de modificar as solicitações HTTP feitas pelo controle de mapa. Por exemplo:

- Adicione cabeçalhos adicionais às solicitações de bloco. Isso geralmente é feito para serviços protegidos por senha.
- Modifique as URLs para executar solicitações por meio de um serviço de proxy.

As [Opções de serviço](/javascript/api/azure-maps-control/atlas.serviceoptions) do mapa têm um `transformRequest` que pode ser usado para modificar todas as solicitações feitas pelo mapa antes que elas sejam feitas. A `transformRequest` opção é uma função que usa dois parâmetros; uma URL de cadeia de caracteres e uma cadeia de caracteres de tipo de recurso que indica para que a solicitação é usada. Essa função deve retornar um resultado de [RequestParameters](/javascript/api/azure-maps-control/atlas.requestparameters) .

```JavaScript
transformRequest: (url: string, resourceType: string) => RequestParameters
```

O exemplo a seguir mostra como usar isso para modificar todas as solicitações para o tamanho `https://example.com` adicionando um nome de usuário e uma senha como cabeçalhos à solicitação.

```JavaScript
var map = new atlas.Map('myMap', {
    transformRequest: function (url, resourceType) {
        //Check to see if the request is to the specified endpoint.
        if (url.indexOf('https://examples.com') > -1) {
            //Add custom headers to the request.
            return {
                url: url,
                header: {
                    username: 'myUsername',
                    password: 'myPassword'
                }
            };
        }

        //Return the URL unchanged by default.
        return { url: url };
    },

    authOptions: {
        authType: 'subscriptionKey',
        subscriptionKey: '<Your Azure Maps Key>'
    }
});
```

## <a name="try-out-the-code"></a>Experimentar o código

Examine os exemplos de código. Você pode editar o código JavaScript dentro da **guia js** e ver as alterações na exibição do mapa na **guia resultado** . Você também pode clicar em **Editar em CodePen** , no canto superior direito, e modificar o código em CodePen.

<a id="relatedReference"></a>

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [Map](/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [Câmara de câmera](/javascript/api/azure-maps-control/atlas.cameraoptions)

> [!div class="nextstepaction"]
> [Animaçãooptions](/javascript/api/azure-maps-control/atlas.animationoptions)

Consulte exemplos de código para adicionar funcionalidade ao seu aplicativo:

> [!div class="nextstepaction"]
> [Alterar estilo do mapa](choose-map-style.md)

> [!div class="nextstepaction"]
> [Adicionar controles ao mapa](map-add-controls.md)

> [!div class="nextstepaction"]
> [Exemplos de código](/samples/browse/?products=azure-maps)