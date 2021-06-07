---
title: Gravando plug-ins para Player de Mídia do Azure
description: Saiba como escrever um plug-in com Player de Mídia do Azure com JavaScript
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 04/20/2020
ms.custom: devx-track-js
ms.openlocfilehash: c1a64bff8b81735d9c4c9a14d2c1e12bd0bfe57e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91296154"
---
# <a name="writing-plugins-for-azure-media-player"></a>Gravando plug-ins para Player de Mídia do Azure #

Um plug-in é escrito por JavaScript para estender ou aprimorar o Player. Você pode gravar plug-ins que alteram a aparência de Player de Mídia do Azure, sua funcionalidade ou até mesmo ter uma interface de ti com outros serviços. Você pode fazer isso em duas etapas simples:

## <a name="step-1"></a>Etapa 1 ##

Escreva seu JavaScript em uma função como esta:

```javascript

    (function () {
        amp.plugin('yourPluginName', function (options) {
        var myPlayer = this;
           myPlayer.addEventListener(amp.eventName.ready, function () {
        console.log("player is ready!");
            });
        });
    }).call(this);
```

Você pode escrever seu código diretamente em sua página HTML dentro de `<script>` marcas ou em um arquivo JavaScript externo. Se você fizer o último, certifique-se de incluir o arquivo JavaScript na `<head>` sua página HTML *após* o script de amp.

Exemplo:

```javascript
    <!--*****START OF Azure Media Player Scripts*****-->
    <script src="//amp.azure.net/libs/amp/latest/azuremediaplayer.min.js"></script>
    <link href="//amp.azure.net/libs/amp/latest/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
    <!--*****END OF Azure Media Player Scripts*****-->
    <!--Add Plugins-->
    <script src="yourPluginName.js"></script>
```

## <a name="step-2"></a>Etapa 2 ##
Inicialize o plug-in com JavaScript de uma das duas maneiras:

Método 1:

```javascript
    var myOptions = {
        autoplay: true,
        controls: true,
        width: "640",
        height: "400",
        poster: "",
        plugins: {
            yourPluginName: {
                [your plugin options]: [example options]
           }
        }
    };     
    var myPlayer = amp([videotag id], myOptions);
```

Método 2:

```javascript
    var video = amp([videotag id]);
    video.yourPluginName({[your plugins option]: [example option]});
```

As opções de plug-in não são necessárias, incluindo-as apenas permite que os desenvolvedores que usam seu plug-in configurem seu comportamento sem precisar alterar o código-fonte.

Para inspiração e mais exemplos de como criar um plug-in, dê uma olhada em nossa [Galeria](azure-media-player-plugin-gallery.md)

>[!NOTE]
> O código do plug-in altera dinamicamente os itens no DOM durante o tempo de vida da experiência do jogador do visualizador, mas nunca faz alterações permanentes no código-fonte do jogador. É aí que um entendimento das ferramentas de desenvolvedor de seu navegador é útil. Por exemplo, se você quiser alterar a aparência de um elemento no Player, poderá encontrar seu elemento HTML por seu nome de classe e, em seguida, adicionar ou alterar atributos a partir daí. Aqui está um ótimo recurso na [alteração de atributos HTML.](http://www.w3schools.com/js/js_htmldom_html.asp)

### <a name="integrated-plugins"></a>Plug-ins integrados ###

 Atualmente, há dois plug-ins inclusas em AMP: The [time-Tip](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/timetip/example.html) e [teclas](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/hotkeys/example.html)de mouse. Esses plug-ins foram originalmente desenvolvidos para serem plug-ins modulares para o Player, mas agora estão incluídos no código-fonte do Player.

### <a name="plugin-gallery"></a>Galeria de plug-ins ###

A [Galeria de plug-](https://aka.ms/ampplugins) ins tem vários plugins que a Comunidade já contribuiu para recursos como marcadores de linha de tempo, zoom, análise e muito mais. A página fornece acesso aos plug-ins e instruções sobre como configurá-lo, bem como uma demonstração que mostra o plug-in em ação. Se você criar um plug-in interessante que você acha que deve ser incluído em nossa galeria, fique à vontade para enviá-lo para que possamos fazer check-out.

## <a name="next-steps"></a>Próximas etapas ##

- [Início rápido do Player de Mídia do Azure](azure-media-player-quickstart.md)
