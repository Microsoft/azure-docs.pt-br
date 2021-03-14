---
title: Como usar o Shaka Player com os serviços de mídia do Azure
description: Este artigo explica como usar o Shaka Player com os serviços de mídia do Azure
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-js
ms.openlocfilehash: 7b4bda5a439b1e0b35dc6d3af99e0273e4987568
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2021
ms.locfileid: "101091354"
---
# <a name="how-to-use-the-shaka-player-with-azure-media-services"></a>Como usar o Shaka Player com os serviços de mídia do Azure

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="overview"></a>Visão geral

O Shaka Player é uma biblioteca JavaScript de código aberto para mídia adaptável. Ele desempenha Formatos de mídia adaptável (como DASH e HLS) em um navegador, sem usar plugins ou flash. Em vez disso, o Shaka Player usa as extensões de mídia criptografadas de mídia de padrões da Web e extensões de Media criptografia.

É recomendável usar [Mux.js](https://github.com/videojs/mux.js/) como, sem ele, o Shaka Player ofereceria suporte ao formato HLS CMAF, mas não HLS TS.

Sua documentação oficial pode ser encontrada na [documentação do Shaka Player](https://shaka-player-demo.appspot.com/docs/api/tutorial-welcome.html).

## <a name="sample-code"></a>Código de exemplo

O código de exemplo deste artigo está disponível em [Azure-Samples/Media-Services-3rdparty-Player-Samples](https://github.com/Azure-Samples/media-services-3rdparty-player-samples).

## <a name="implementing-the-player"></a>Implementando o Player

Siga estas instruções se você precisar implementar sua própria instância do Player:

1. Crie um `index.html` arquivo no qual você hospedará o Player. Adicione as seguintes linhas de código (você pode substituir as versões para mais recente, se aplicável):

    ```html
    <html>
      <head>
        <script src="//cdn.jsdelivr.net/npm/shaka-player@3.0.1/dist/shaka-player.compiled.js"></script>
        <script src="//cdn.jsdelivr.net/npm/mux.js@5.6.3/dist/mux.js"></script>
        <script type="module" src="index.js"></script>
      </head>
      <body>
        <video id="video" controls></video>
      </body>
    </html>
    ```

1. Adicione um arquivo JavaScript com o seguinte código:

    ```javascript
    // myScript.js
    shaka.polyfill.installAll();

    var video = document.getElementById('video');
    var player = new shaka.Player(video);
    window.player = player;

    var manifestUrl = 'https://amsplayeraccount-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/sample-vod.ism/manifest(format=m3u8-aapl)';
    player.load(manifestUrl);
    ```

1. Substitua `manifestUrl` pela URL HLS ou Dash do localizador de streaming do seu ativo, que pode ser encontrado na página do localizador de streaming na portal do Azure.
    ![URLs do localizador de streaming](media/how-to-shaka-player/streaming-urls.png)

1. Execute um servidor (por exemplo `npm http-server` , com) e seu player deve estar trabalhando...

## <a name="set-up-captions"></a>Configurar legendas

### <a name="set-up-vod-captions"></a>Configurar legendas de VOD

Execute as seguintes linhas de código e substitua `captionUrl` pelo diretório. VTT (o arquivo VTT precisa estar no mesmo host para evitar o erro CORS), `lang` com o código de duas letras para o idioma e `type` com `caption` ou `subtitle` :

```javascript
player.configure('streaming.alwaysStreamText', true)
player.load(manifestUrl).then(function(){
        player.addTextTrack(captionUrl, lang, type, 'text/vtt');
        var tracks = player.getTextTracks();
        player.selectTextTrack(tracks[0]);
});
```

### <a name="set-up-live-stream-captions"></a>Configurar legendas de fluxo ao vivo

Habilitar legendas na transmissão ao vivo é configurado adicionando a seguinte linha de código:

```javascript
player.setTextTrackVisibility(true)
```

## <a name="set-up-token-authentication"></a>Configurar a autenticação de token

Execute as seguintes linhas de código e substitua `token` pela cadeia de caracteres do token:

```javascript
player.getNetworkingEngine().registerRequestFilter(function (type, request) {
  if (type === shaka.net.NetworkingEngine.RequestType.LICENSE) {
    request.headers['Authorization'] = 'Bearer ' + token;
  }
});
```

## <a name="set-up-aes-128-encryption"></a>Configurar a criptografia AES-128

Atualmente, o Shaka Player não dá suporte à criptografia AES-128.

Um link para um [problema](https://github.com/google/shaka-player/issues/850) do GitHub para seguir o status desse recurso.

## <a name="set-up-drm-protection"></a>Configurar a proteção de DRM

O Shaka Player usa EME (extensões de mídia criptografada), que requer uma URL segura a ser usada. Portanto, para testar qualquer conteúdo protegido por DRM, é necessário usar HTTPS. Se o site estiver usando HTTPS, o manifesto e todos os segmentos também precisarão usar HTTPS. Isso ocorre devido a requisitos de conteúdo mistos.

A ordem de preferência para o gerenciamento de Shaka das URLs de seus servidores de licença:

1. ClearKey config, usada para depuração, deve substituir todo o resto. (O aplicativo ainda pode especificar um servidor de licença ClearKey.)
2. Os servidores configurados por aplicativo, se houver algum, devem substituir qualquer coisa do manifesto.
3. Os servidores de licença fornecidos pelo manifesto serão usados somente se nada mais for especificado.

Para especificar a URL do servidor de licença para Widevine ou PlayReady, podemos usar o seguinte código:

```javascript
player.configure({
  drm: {
    servers: {
      "com.widevine.alpha": "YOUR WIDEVINE LICENSE URL",
      "com.microsoft.playready": "YOUR PLAYREADY LICENSE URL"
    }
  }
});

```

Todo o conteúdo do FairPlay requer a definição de um certificado do servidor. Ele é definido na configuração do Player:

```javascript
const req = await fetch("YOUR FAIRPLAY CERTIFICATE URL");
const cert = await req.arrayBuffer();
player.configure('drm.advanced.com\\.apple\\.fps\\.1_0.serverCertificate', new Uint8Array(cert));
```

Para obter mais informações, consulte a [documentação de proteção do Shaka Player DRM](https://shaka-player-demo.appspot.com/docs/api/tutorial-drm-config.html).

## <a name="next-steps"></a>Próximas etapas

* [Usar o Player de Mídia do Azure](../azure-media-player/azure-media-player-overview.md)
* [Início Rápido: Criptografar conteúdo](encrypt-content-quickstart.md)
