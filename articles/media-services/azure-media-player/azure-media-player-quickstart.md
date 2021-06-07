---
title: Início rápido do Player de Mídia do Azure
description: Conheça as etapas básicas para configurar o Player de Mídia do Azure.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: quickstart
ms.date: 04/05/2021
ms.openlocfilehash: a6fd603318a25e15d1d4dcc1e3eaf75f96fc5ade
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448620"
---
# <a name="azure-media-player-quickstart"></a>Início rápido do Player de Mídia do Azure
O Player de Mídia do Azure é fácil de configurar. São necessários apenas alguns minutos para obter a reprodução básica de conteúdo de mídia de sua conta dos Serviços de Mídia do Azure. Esta seção mostra as etapas básicas sem entrar em detalhes. As seções a seguir oferecem informações específicas sobre como instalar e configurar o Player de Mídia do Azure.  Basta adicionar as seguintes inclusões ao `<head>` de seu documento:

```html
    <link href="//amp.azure.net/libs/amp/latest/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
    <script src= "//amp.azure.net/libs/amp/latest/azuremediaplayer.min.js"></script>
```

> [!IMPORTANT]
> Você **NÃO** deve usar a versão `latest` na produção, pois ela está sujeita a alterações sob demanda. Substitua `latest` por uma versão do Player de Mídia do Azure, por exemplo, substitua `latest` por `1.0.0`. As versões do Player de Mídia do Azure podem ser consultadas [aqui](https://amp.azure.net/libs/amp/latest/docs/changelog.html).

## <a name="use-the-video-element"></a>Usar o elemento de vídeo

Em seguida, basta usar o elemento `<video>` como você faria normalmente, mas com um atributo `data-setup` adicional contendo as opções que deseja usar. Essas opções podem incluir qualquer opção dos Serviços de Mídia do Azure em um objeto JSON válido.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin" autoplay controls width="640" height="400" poster="poster.jpg" data-setup='{"nativeControlsForTouch": false}'>
        <source src="http://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest" type="application/vnd.ms-sstr+xml" />
        <p class="amp-no-js">
            To view this video please enable JavaScript, and consider upgrading to a web browser that supports HTML5 video
        </p>
    </video>
```

Se você não quiser usar a instalação automática, poderá omitir o atributo `data-setup` e inicializar um elemento de vídeo manualmente.

```javascript
    var myPlayer = amp('vid1', { /* Options */
            "nativeControlsForTouch": false,
            autoplay: false,
            controls: true,
            width: "640",
            height: "400",
            poster: ""
        }, function() {
              console.log('Good to go!');
               // add an event listener
              this.addEventListener('ended', function() {
                console.log('Finished!');
            });
          }
    );
    myPlayer.src([{
        src: "http://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest",
        type: "application/vnd.ms-sstr+xml"
    }]);
```

## <a name="next-steps"></a>Próximas etapas ##

- [Configuração completa do Player de Mídia do Azure](./azure-media-player-full-setup.md)
