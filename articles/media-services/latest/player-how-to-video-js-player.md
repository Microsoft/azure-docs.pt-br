---
title: Usar o player Video.js com os Serviços de Mídia do Azure
description: Este artigo explica como usar o objeto de vídeo HTML e JavaScript com os Serviços de Mídia do Azure
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
ms.openlocfilehash: 0ac00ecbbe5e0a72bccf6effe5e82fc7d973c91e
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106281129"
---
# <a name="how-to-use-the-videojs-player-with-azure-media-services"></a>Como usar o player Video.js com os Serviços de Mídia do Azure

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="overview"></a>Visão geral

O Video.js é um player de vídeo da Web criado para um mundo HTML5. Ele executa formatos de mídias adaptáveis (como DASH e HLS) em um navegador, sem usar plug-ins nem o Flash. Em vez disso, ele usa Extensões de Fontes de Mídia e Extensões de Mídia Criptografada de padrões abertos da Web. Além disso, ele é compatível com a reprodução de vídeo em desktops e dispositivos móveis.

A documentação oficial do Video.js pode ser encontrada em [https://docs.videojs.com/](https://docs.videojs.com/).

## <a name="sample-code"></a>Código de exemplo
O código de exemplo deste artigo está disponível em [Azure-Samples/media-services-3rdparty-player-samples](https://github.com/Azure-Samples/media-services-3rdparty-player-samples).

## <a name="implement-the-player"></a>Implementar o player

1. Crie um arquivo `index.html` no qual você hospedará o player. Adicione as seguintes linhas de código (você pode substituir as versões pelas mais recentes, se aplicável):

    ```html
    <html>
      <head>
        <link href="https://vjs.zencdn.net/7.8.2/video-js.css" rel="stylesheet" />
      </head>
      <body>
        <video id="video" class="video-js vjs-default-skin vjs-16-9" controls data-setup="{}">
        </video>

        <script src="https://vjs.zencdn.net/7.8.2/video.js"></script>
        <script src="https://cdn.jsdelivr.net/npm/videojs-contrib-eme@3.7.0/dist/videojs-contrib-eme.min.js"></script>
        <script type="module" src="index.js"></script>
      </body>
    <html>
    ```

2. Adicione um arquivo `index.js` com o seguinte código:

    ```javascript
    var videoJS = videojs("video");
    videoJS.src({
      src: "manifestUrl",
      type: "protocolType",
    });
    ```

3. Substitua `manifestUrl` pela URL HLS ou DASH do localizador de streaming de seu ativo, que pode ser encontrado na página do localizador de streaming no portal do Azure.
    ![ID do localizador de streaming](media/player-shaka-player-how-to/streaming-urls.png)

4. Substitua `protocolType` pelas seguintes opções:

    - "application/x-mpegURL" para protocolos HLS
    - "application/dash+xml" para protocolos DASH

### <a name="set-up-captions"></a>Configurar legendas

Execute o método `addRemoteTextTrack` e substitua:

- `subtitleKind` por `"captions"`, `"subtitles"`,`"descriptions"` ou `"metadata"`  
- `caption` pelo caminho do arquivo .vtt (o arquivo vtt precisa estar no mesmo host para evitar o erro de CORS)
- `subtitleLang` pelo código BCP 47 do idioma, por exemplo, `"eng"` para inglês ou `"es"` para espanhol
- `subtitleLabel` pelo nome de exibição desejado da legenda

```javascript
videojs.players.video.addRemoteTextTrack({
  kind: subtitleKind,
  src: caption,
  srclang: subtitleLang,
  label: subtitleLabel
});
```

### <a name="set-up-token-authentication"></a>Configurar a autenticação de token

O token deve ser definido no campo de autorização do cabeçalho da solicitação. Para evitar problemas com o CORS, esse token deve ser definido somente nas solicitações que têm `'keydeliver'` na URL. As seguintes linhas de código devem fazer esse trabalho:

```javascript
setupTokenForDecrypt (options) {
  if (options.uri.includes('keydeliver')) {
    options.headers = options.headers || {}
    options.headers.Authorization = 'Bearer=' + this.getInputToken()
  }

  return options
}
```

Em seguida, a função acima deve ser anexada ao evento `videojs.Hls.xhr.beforeRequest`.

```javascript
videojs.Hls.xhr.beforeRequest = setupTokenForDecrypt;
```

### <a name="set-up-aes-128-encryption"></a>Configurar a criptografia AES-128

O Video.js dá suporte à criptografia AES-128 sem nenhuma configuração adicional. 

> [!NOTE] 
> Atualmente, há um [problema](https://github.com/videojs/video.js/issues/6717) com a criptografia e o conteúdo CMAF de HLS/DASH, que não podem ser reproduzidos.

### <a name="set-up-drm-protection"></a>Configurar a proteção de DRM

Para dar suporte à proteção de DRM, você deve adicionar a extensão oficial [videojs-contrib-eme](https://github.com/videojs/videojs-contrib-eme). Uma versão da CDN também funciona.

1. No arquivo `index.js` detalhado acima, você deve inicializar a extensão EME adicionando `videoJS.eme();` *antes* de adicionar a origem do vídeo:

   ```javascript
    videoJS.eme();
   ```

2. Agora, você pode definir as URLs dos serviços de DRM e as URLs das licenças correspondentes da seguinte maneira:

   ```javascript
   videoJS.src({
       keySystems: {
           "com.microsoft.playready": "YOUR PLAYREADY LICENSE URL",
           "com.widevine.alpha": "YOUR WIDEVINE LICENSE URL",
           "com.apple.fps.1_0": {
            certificateUri: "YOUR FAIRPLAY CERTIFICATE URL",
            licenseUri: "YOUR FAIRPLAY LICENSE URL"
           }
         }
       })

   ```

#### <a name="acquiring-the-license-url"></a>Obtendo a URL da licença

Para obter a URL de licença, você pode:

- Consultar a configuração do provedor de DRM
- ou, se estiver usando o exemplo, consultar o documento `output.json` gerado quando você executou o script *setup-vod.ps1* do PowerShell para VODs ou o script *start-live.ps1* para transmissões ao vivo. Você também encontrará o KIDs dentro desse arquivo.

#### <a name="using-tokenized-drm"></a>Usando o DRM com token

Para dar suporte à proteção de DRM com token, você precisa adicionar a seguinte linha à propriedade `src` do player:

```javascript
videoJS.src({
src: ...,
emeHeaders: {'Authorization': "Bearer=" + "YOUR TOKEN"},
keySystems: {...
```

## <a name="next-steps"></a>Próximas etapas

- [Usar o Player de Mídia do Azure](../azure-media-player/azure-media-player-overview.md)  
- [Início Rápido: Criptografar conteúdo](drm-encrypt-content-how-to.md)
