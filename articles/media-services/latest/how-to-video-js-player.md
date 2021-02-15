---
title: Usar o Video.js Player com os serviços de mídia do Azure
description: Este artigo explica como usar o objeto de vídeo HTML e JavaScript com os serviços de mídia do Azure
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
ms.openlocfilehash: a0b357705de04ed4c2be3223f9dd07f61f75d970
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954573"
---
# <a name="how-to-use-the-videojs-player-with-azure-media-services"></a>Como usar o Video.js Player com os serviços de mídia do Azure

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="overview"></a>Visão geral

Video.js é um player de vídeo da Web criado para um mundo HTML5. Ele desempenha Formatos de mídia adaptável (como DASH e HLS) em um navegador, sem usar plugins ou flash. Em vez disso, Video.js usa as extensões Open Web de padrões de mídia e extensões de mídia criptografadas. Além disso, ele dá suporte à reprodução de vídeo em desktops e dispositivos móveis.

Sua documentação oficial pode ser encontrada em [https://docs.videojs.com/](https://docs.videojs.com/) .

## <a name="sample-code"></a>Código de exemplo
O código de exemplo deste artigo está disponível em [Azure-Samples/Media-Services-3rdparty-Player-Samples](https://github.com/Azure-Samples/media-services-3rdparty-player-samples).

## <a name="implement-the-player"></a>Implementar o Player

1. Crie um `index.html` arquivo no qual você hospedará o Player. Adicione as seguintes linhas de código (você pode substituir as versões para mais recente, se aplicável):

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

2. Adicione um `index.js` arquivo com o seguinte código:

    ```javascript
    var videoJS = videojs("video");
    videoJS.src({
      src: "manifestUrl",
      type: "protocolType",
    });
    ```

3. Substitua `manifestUrl` pela URL HLS ou Dash do localizador de streaming do seu ativo que pode ser encontrado na página do localizador de streaming no portal do Azure.
    ![URLs do localizador de streaming](media/how-to-shaka-player/streaming-urls.png)

4. Substitua `protocolType` pelas seguintes opções:

    - "application/x-mpegURL" para protocolos HLS
    - "aplicativo/Dash + XML" para protocolos DASH

### <a name="set-up-captions"></a>Configurar legendas

Execute o `addRemoteTextTrack` método e substitua:

- `subtitleKind` com o `"captions"` , `"subtitles"` , `"descriptions"` ou `"metadata"`  
- `caption` com o caminho do arquivo. VTT (o arquivo VTT precisa estar no mesmo host para evitar o erro CORS)
- `subtitleLang` com o código BCP 47 para Language, por exemplo, `"eng"` para inglês ou `"es"` espanhol
- `subtitleLabel` com o nome de exibição desejado da legenda

```javascript
videojs.players.video.addRemoteTextTrack({
  kind: subtitleKind,
  src: caption,
  srclang: subtitleLang,
  label: subtitleLabel
});
```

### <a name="set-up-token-authentication"></a>Configurar a autenticação de token

O token deve ser definido no campo autorização do cabeçalho da solicitação. Para evitar problemas com o CORS, esse token deve ser definido somente nessas solicitações com `'keydeliver'` em sua URL. As linhas de código a seguir devem fazer o trabalho:

```javascript
setupTokenForDecrypt (options) {
  if (options.uri.includes('keydeliver')) {
    options.headers = options.headers || {}
    options.headers.Authorization = 'Bearer=' + this.getInputToken()
  }

  return options
}
```

Em seguida, a função acima deve ser anexada ao `videojs.Hls.xhr.beforeRequest` evento.

```javascript
videojs.Hls.xhr.beforeRequest = setupTokenForDecrypt;
```

### <a name="set-up-aes-128-encryption"></a>Configurar a criptografia AES-128

O Video.js dá suporte à criptografia AES-128 sem nenhuma configuração adicional. 

> [!NOTE] 
> Atualmente, há um [problema](https://github.com/videojs/video.js/issues/6717) com a criptografia e o conteúdo de CMAF HLS/Dash, que não são reproduzidos.

### <a name="set-up-drm-protection"></a>Configurar a proteção de DRM

Para oferecer suporte à proteção DRM, você deve adicionar a extensão oficial [videojs-contrib-eme](https://github.com/videojs/videojs-contrib-eme) . Uma versão da CDN também funciona.

1. No `index.js` arquivo detalhado acima, você deve inicializar a extensão eme adicionando `videoJS.eme();` *antes* de adicionar a origem do vídeo:

   ```javascript
    videoJS.eme();
   ```

2. Agora você pode definir as URLs dos serviços DRM e as URLs das licenças correspondentes da seguinte maneira:

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

#### <a name="acquiring-the-license-url"></a>Adquirindo a URL da licença

Para adquirir a URL de licença, você pode:

- Consulte a configuração do provedor de DRM
- ou, se você estiver usando o exemplo, consulte o `output.json` documento gerado quando executou o script *setup-vod.ps1* PowerShell para VODs ou o script *start-live.ps1* para fluxos ao vivo. Você também encontrará as crianças dentro desse arquivo.

#### <a name="using-tokenized-drm"></a>Usando DRM com token

Para dar suporte à proteção de DRM com token, você precisa adicionar a seguinte linha à `src` Propriedade do Player:

```javascript
videoJS.src({
src: ...,
emeHeaders: {'Authorization': "Bearer=" + "YOUR TOKEN"},
keySystems: {...
```

## <a name="next-steps"></a>Próximas etapas

- [Usar o Player de Mídia do Azure](../azure-media-player/azure-media-player-overview.md)  
- [Início Rápido: Criptografar conteúdo](encrypt-content-quickstart.md)
