---
title: Configuração completa do Player de Mídia do Azure
description: Saiba como configurar o Player de Mídia do Azure.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 04/20/2020
ms.custom: devx-track-js
ms.openlocfilehash: b6b239f44311424db2e80c59e2aba639ae3c0000
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98797461"
---
# <a name="azure-media-player-full-setup"></a>Configuração completa do Player de Mídia do Azure #

O Player de Mídia do Azure é fácil de configurar. Leva apenas alguns minutos para obter a reprodução básica do conteúdo de mídia diretamente da sua conta dos serviços de mídia do Azure. Os [exemplos](https://github.com/Azure-Samples/azure-media-player-samples) também são fornecidos no diretório de exemplos da versão.

## <a name="step-1-include-the-javascript-and-css-files-in-the-head-of-your-page"></a>Etapa 1: incluir os arquivos JavaScript e CSS no cabeçalho da página ##

Com Player de Mídia do Azure, você pode acessar os scripts da versão hospedada da CDN. Geralmente, é recomendável colocar o JavaScript antes da marca do corpo final `<body>` em vez de `<head>` , mas o Azure meia Player inclui um ' HTML5 Shiv ', que precisa estar no cabeçalho das versões mais antigas do IE para respeitar a marca de vídeo como um elemento válido.

> [!NOTE]
> Se você já estiver usando um HTML5 Shiv como o [Modernizr](https://modernizr.com/) , poderá incluir o player de mídia do Azure JavaScript em qualquer lugar. No entanto, verifique se sua versão do Modernizr inclui o Shiv para vídeo.

### <a name="cdn-version"></a>Versão da CDN ###

```html
    <link href="//amp.azure.net/libs/amp/latest/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
    <script src= "//amp.azure.net/libs/amp/latest/azuremediaplayer.min.js"></script>
```

> [!IMPORTANT]
> Você **NÃO** deve usar a versão `latest` na produção, pois ela está sujeita a alterações sob demanda. Substituir `latest` por uma versão do player de mídia do Azure. Por exemplo, substitua `latest` por `2.1.1`. As versões do Player de Mídia do Azure podem ser consultadas [aqui](azure-media-player-changelog.md).

> [!NOTE]
> Desde o `1.2.0` lançamento, não é mais necessário incluir o local para os Techs de fallback (ele irá selecionar automaticamente o local do caminho relativo do arquivo de azuremediaplayer.min.js). Você pode modificar o local das tecnologias de fallback adicionando o script a seguir no `<head>` após os scripts acima.

> [!NOTE]
> Devido à natureza dos plug-ins flash e Silverlight, os arquivos SWF e XAP devem ser hospedados em um domínio sem informações confidenciais ou dados-isso é automaticamente levado para você com a versão hospedada da CDN do Azure.

```javascript
    <script>
      amp.options.flashSS.swf = "//amp.azure.net/libs/amp/latest/techs/StrobeMediaPlayback.2.0.swf"
      amp.options.silverlightSS.xap = "//amp.azure.net/libs/amp/latest/techs/SmoothStreamingPlayer.xap"
    </script>
```

## <a name="step-2-add-an-html5-video-tag-to-your-page"></a>Etapa 2: adicionar uma marca de vídeo HTML5 à sua página ##

Com Player de Mídia do Azure, você pode usar uma marca de vídeo HTML5 para inserir um vídeo. Player de Mídia do Azure lerá a marca e a fará funcionar em todos os navegadores, não apenas aquelas que dão suporte ao vídeo HTML5. Além da marcação básica, Player de Mídia do Azure precisa de algumas partes extras.

1. O `<data-setup>` atributo no `<video>` diz player de mídia do Azure para configurar automaticamente o vídeo quando a página estiver pronta e ler qualquer (no formato JSON) do atributo.
1. O `id` atributo: deve ser usado e exclusivo para cada vídeo na mesma página.
1. O `class` atributo contém duas classes:
    - `azuremediaplayer` aplica estilos necessários para Player de Mídia do Azure funcionalidade de interface do usuário
    - `amp-default-skin` aplica a aparência padrão aos controles HTML5
1. O `<source>` inclui dois atributos necessários
    - `src` o atributo pode incluir um arquivo **. ISM/manifest* dos serviços de mídia do Azure é adicionado, player de mídia do Azure adiciona automaticamente as URLs para traço, Smooth e HLS ao Player
    - `type` o atributo é o tipo MIME necessário do fluxo. O tipo MIME associado a *". ISM/Manifest"* é *"application/vnd. ms-SSTR + XML"*
1. O  `<data-setup>` atributo opcional no `<source>` informa player de mídia do Azure se há alguma política de entrega exclusiva para o fluxo dos serviços de mídia do Azure, incluindo, mas não se limitando a, tipo de criptografia (AES ou PlayReady, Widevine ou Fairplay) e token.

Inclua/exclua atributos, configurações, fontes e faixas exatamente como você faria para o vídeo do HTML5.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin" autoplay controls width="640" height="400" poster="poster.jpg" data-setup='{"techOrder": ["azureHtml5JS", "flashSS", "html5FairPlayHLS","silverlightSS", "html5"], "nativeControlsForTouch": false}'>
        <source src="http://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest" type="application/vnd.ms-sstr+xml" />
        <p class="amp-no-js">
            To view this video please enable JavaScript, and consider upgrading to a web browser that supports HTML5 video
        </p>
    </video>
```

Por padrão, o botão de reprodução grande está localizado no canto superior esquerdo para que ele não cubra as partes interessantes do pôster. Se preferir centralizar o botão de reprodução grande, você pode adicionar um adicional `amp-big-play-centered` `class` ao seu `<video>` elemento.

### <a name="alternative-setup-for-dynamically-loaded-html"></a>Configuração alternativa para HTML carregado dinamicamente ###

Se sua página da Web ou aplicativo carregar a marca de vídeo dinamicamente (AJAX, appendChild, etc.), para que ela não exista quando a página for carregada, você desejará configurar manualmente o Player em vez de depender do atributo de configuração de dados. Para fazer isso, primeiro remova o atributo de configuração de dados da marca para que não haja confusão quando o player for inicializado. Em seguida, execute o JavaScript a seguir depois que o Player de Mídia do Azure JavaScript tiver sido carregado e depois que a marca de vídeo tiver sido carregada no DOM.

```javascript
    var myPlayer = amp('vid1', { /* Options */
            techOrder: ["azureHtml5JS", "flashSS", "html5FairPlayHLS","silverlightSS", "html5"],
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
            }
          }
    );
    myPlayer.src([{
        src: "http://samplescdn.origin.mediaservices.windows.net/e0e820ec-f6a2-4ea2-afe3-1eed4e06ab2c/AzureMediaServices_Overview.ism/manifest",
        type: "application/vnd.ms-sstr+xml"
    }]);
```

O primeiro argumento na `amp` função é a ID da sua marca de vídeo. Substitua-o por seu próprio.

O segundo argumento é um objeto de opções. Ele permite que você defina opções adicionais como você pode com o atributo de configuração de dados.

O terceiro argumento é um retorno de chamada ' Ready '. Depois que Player de Mídia do Azure for inicializado, ele chamará essa função. No retorno de chamada pronto, o objeto ' this ' refere-se à instância do Player.

Em vez de usar uma ID de elemento, você também pode passar uma referência para o próprio elemento.

```javascript

    amp(document.getElementById('example_video_1'), {/*Options*/}, function() {
        //This is functionally the same as the previous example.
    });
    myPlayer.src([{ src: "//example/path/to/myVideo.ism/manifest", type: "application/vnd.ms-sstr+xml"]);
```

## <a name="next-steps"></a>Próximas etapas ##

- [Início rápido do Player de Mídia do Azure](azure-media-player-quickstart.md)