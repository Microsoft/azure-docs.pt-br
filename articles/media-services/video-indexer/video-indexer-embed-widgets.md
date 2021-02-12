---
title: Inserir Video Indexer widgets em seus aplicativos
titleSuffix: Azure Media Services
description: Saiba como inserir Video Indexer widgets em seus aplicativos.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/25/2021
ms.author: juliako
ms.custom: devx-track-js
ms.openlocfilehash: b13086e11e1181bba91a3255e68e9f8a32e78450
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98797780"
---
# <a name="embed-video-indexer-widgets-in-your-apps"></a>Inserir Video Indexer widgets em seus aplicativos

Este artigo mostra como você pode inserir Video Indexer widgets em seus aplicativos. O Video Indexer é compatível com a inserção de três tipos de widgets nos seus aplicativos: *Insights cognitivos*, *Player* e *Editor*.

A partir da versão 2, a URL base do widget inclui a região da conta especificada. Por exemplo, uma conta da região Oeste dos EUA gera: `https://www.videoindexer.ai/embed/insights/.../?location=westus2`.

## <a name="widget-types"></a>Tipos de widget

### <a name="cognitive-insights-widget"></a>Widget de Insights Cognitivos

Um widget Insights cognitivos inclui todos os insights visuais que foram extraídos do processo de indexação do seu vídeo. O widget cognitiva insights dá suporte aos seguintes parâmetros de URL opcionais:

|Nome|Definição|Descrição|
|---|---|---|
|`widgets` | Cadeias de caracteres separadas por vírgula | Permite que você controle as informações que deseja renderizar.<br/>Exemplo: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,keywords` renderiza apenas pessoas e palavras-chave da interface do usuário.<br/>Opções disponíveis: pessoas, animatedCharacters, palavras-chave, rótulos, sentimentos, emoções, tópicos, quadros chave, transcrição, OCR, alto-falantes, cenas e namedEntities.|
|`controls`|Cadeias de caracteres separadas por vírgula|Permite que você controle os controles que deseja renderizar.<br/>Exemplo: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?controls=search,download` renderiza apenas a opção de pesquisa e o botão de download.<br/>Opções disponíveis: Pesquisar, baixar, predefinições, idioma.|
|`language`|Um código de idioma curto (nome do idioma)|Controla a linguagem de informações.<br/>Exemplo: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?language=es-es` <br/>ou `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?language=spanish`|
|`locale` | Um código de idioma curto | Controla o idioma da interface do usuário. O valor padrão é `en`. <br/>Exemplo: `locale=de`.|
|`tab` | A guia selecionada padrão | Controla a guia **insights** que é renderizada por padrão. <br/>Exemplo: `tab=timeline` renderiza as informações com a guia **linha do tempo** selecionada.|
|`location` ||O `location` parâmetro deve ser incluído nos links inseridos, consulte [como obter o nome da sua região](regions.md). Se sua conta estiver em visualização, `trial` deverá ser usada para o valor de local. `trial` é o valor padrão para o `location` parâmetro.| 

### <a name="player-widget"></a>Widget do player

Você pode usar o widget Player para transmitir vídeo usando a taxa de bits adaptável. O widget Player dá suporte aos seguintes parâmetros de URL opcionais.

|Nome|Definição|Descrição|
|---|---|---|
|`t` | Segundos desde o início | Faz com que o Player comece a ser reproduzido do ponto de tempo especificado.<br/> Exemplo: `t=60`. |
|`captions` | Um código de idioma | Busca a legenda no idioma especificado durante o carregamento do widget para estar disponível no menu **legendas** .<br/> Exemplo: `captions=en-US`. |
|`showCaptions` | Um valor booliano | Faz o player ser carregado com as legendas já habilitadas.<br/> Exemplo: `showCaptions=true`. |
|`type`| | Ativa uma capa de player de áudio (a parte de vídeo é removida).<br/> Exemplo: `type=audio`. |
|`autoplay` | Um valor booliano | Indica se o Player deve começar a reproduzir o vídeo quando carregado. O valor padrão é `true`.<br/> Exemplo: `autoplay=false`. |
|`language`/`locale` | Um código de idioma | Controla o idioma do jogador. O valor padrão é `en-US`.<br/>Exemplo: `language=de-DE`.|
|`location` ||O `location` parâmetro deve ser incluído nos links inseridos, consulte [como obter o nome da sua região](regions.md). Se sua conta estiver em visualização, `trial` deverá ser usada para o valor de local. `trial` é o valor padrão para o `location` parâmetro.| 

### <a name="editor-widget"></a>Widget do editor

Você pode usar o widget editor para criar novos projetos e gerenciar informações de um vídeo. O widget editor dá suporte aos seguintes parâmetros de URL opcionais.

|Nome|Definição|Descrição|
|---|---|---|
|`accessToken`<sup>*</sup> | String | Fornece acesso a vídeos que estão apenas na conta que é usada para inserir o widget.<br> O widget do editor requer o `accessToken` parâmetro. |
|`language` | Um código de idioma | Controla o idioma do jogador. O valor padrão é `en-US`.<br/>Exemplo: `language=de-DE`. |
|`locale` | Um código de idioma curto | Controla o idioma do insights. O valor padrão é `en`.<br/>Exemplo: `language=de`. |
|`location` ||O `location` parâmetro deve ser incluído nos links inseridos, consulte [como obter o nome da sua região](regions.md). Se sua conta estiver em visualização, `trial` deverá ser usada para o valor de local. `trial` é o valor padrão para o `location` parâmetro.| 

<sup>*</sup>O proprietário deve fornecer `accessToken` com cuidado.

## <a name="embedding-videos"></a>Inserindo vídeos

Esta seção aborda a inserção de conteúdo público e privado em aplicativos.

O `location` parâmetro deve ser incluído nos links inseridos, consulte [como obter o nome da sua região](regions.md). Se sua conta estiver em visualização, `trial` deverá ser usada para o valor de local. `trial` é o valor padrão para o `location` parâmetro. Por exemplo: `https://www.videoindexer.ai/accounts/00000000-0000-0000-0000-000000000000/videos/b2b2c74b8e/?location=trial`.

> [!IMPORTANT]
> O compartilhamento de um link para o widget **Player** ou **insights** incluirá o token de acesso e concederá as permissões somente leitura à sua conta.

### <a name="public-content"></a>Conteúdo público

1. Entre no site do [Video indexer](https://www.videoindexer.ai/) .
1. Selecione o vídeo com o qual você deseja trabalhar e pressione **reproduzir**.
1. Selecione o tipo de widget que você deseja (**ideias cognitivas**, **Player** ou **Editor**).
1. Clique em **&lt; / &gt; Inserir**.
5. Copie o código de inserção (aparece em **copiar o código inserido** na caixa de diálogo **compartilhar & inserir** ).
6. Adicione o código ao seu aplicativo.

### <a name="private-content"></a>Conteúdo privado

Para inserir um vídeo privado, você deve passar um token de acesso no `src` atributo do iframe:

`https://www.videoindexer.ai/embed/[insights | player]/<accountId>/<videoId>/?accessToken=<accessToken>`
    
Para obter o conteúdo do widget do insights cognitiva, use um dos seguintes métodos:

- A API do [widget obter insights](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget) .<br/>
- O [token de acesso obter vídeo](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Access-Token?). Adicione-o como um parâmetro de consulta à URL. Especifique essa URL como o `src` valor para o iframe, conforme mostrado anteriormente.

Para fornecer recursos de edição de informações em seu widget inserido, você deve passar um token de acesso que inclui permissões de edição. Use o [widget obter insights](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget) ou o [token de acesso de obtenção de vídeo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Access-Token?) com `&allowEdit=true` .

## <a name="widgets-interaction"></a>Interação de widgets

O widget cognitiva insights pode interagir com um vídeo em seu aplicativo. Esta seção mostra como conseguir essa interação.

![Video Indexer widget do insights cognitiva](./media/video-indexer-embed-widgets/video-indexer-widget03.png)

### <a name="flow-overview"></a>Visão geral do fluxo

Quando você edita as transcrições, ocorre o seguinte fluxo:

1. Você edita a transcrição na linha do tempo.
1. Video Indexer obtém essas atualizações e as salva nas [edições from transcrição](customize-language-model-with-website.md#customize-language-models-by-correcting-transcripts) no modelo de linguagem.
1. As legendas são atualizadas:

    * Se você estiver usando o widget Player Video Indexer, ele será atualizado automaticamente.
    * Se você estiver usando um player externo, receberá um novo usuário de arquivo de legendas a chamada **obter legendas de vídeo** .

### <a name="cross-origin-communications"></a>Comunicações entre origens

Para obter Video Indexer widgets se comuniquem com outros componentes, o serviço Video Indexer:

- Usa o método HTML5 de comunicação entre origens `postMessage` .
- Valida a mensagem entre a origem do VideoIndexer.ai.

Se você implementar seu próprio código do Player e integrá-lo a widgets cognitivas, é sua responsabilidade validar a origem da mensagem que vem de VideoIndexer.ai.

### <a name="embed-widgets-in-your-app-or-blog-recommended"></a>Inserir widgets em seu aplicativo ou blog (recomendado)

Esta seção mostra como obter a interação entre dois widgets Video Indexer para que, quando um usuário selecionar o controle Insight em seu aplicativo, o jogador salte para o momento relevante.

1. Copie o código de inserção do widget Player.
2. Copie o código de inserção dos Insights cognitivos.
3. Adicione [Arquivo mediador](https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js) para lidar com a comunicação entre os dois widgets:<br/> 
`<script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script>`

Agora, quando um usuário seleciona o controle Insight em seu aplicativo, o jogador salta para o momento relevante.

Para obter mais informações, consulte a [demonstração Video indexer-incorporar widgets](https://codepen.io/videoindexer/pen/NzJeOb).

### <a name="embed-the-cognitive-insights-widget-and-use-azure-media-player-to-play-the-content"></a>Inserir o widget de Insights cognitivos e usar o Player de Mídia do Azure para reproduzir o conteúdo

Esta seção mostra como obter a interação entre um widget do insights cognitiva e uma instância de Player de Mídia do Azure usando o [plug-in amp](https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js).

1. Adicionar um plug-in de Video Indexer para o AMP Player:<br/> `<script src="https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js"></script>`
2. Instancie Player de Mídia do Azure com o plug-in de Video Indexer.

    ```javascript
    // Init the source.
    function initSource() {
        var tracks = [{
        kind: 'captions',
        // To load vtt from VI, replace it with your vtt URL.
        src: this.getSubtitlesUrl("c4c1ad4c9a", "English"),
        srclang: 'en',
        label: 'English'
        }];
        myPlayer.src([
        {
            "src": "//amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest",
            "type": "application/vnd.ms-sstr+xml"
        }
        ], tracks);
    }

    // Init your AMP instance.
    var myPlayer = amp('vid1', { /* Options */
        "nativeControlsForTouch": false,
        autoplay: true,
        controls: true,
        width: "640",
        height: "400",
        poster: "",
        plugins: {
        videobreakedown: {}
        }
    }, function () {
        // Activate the plug-in.
        this.videobreakdown({
        videoId: "c4c1ad4c9a",
        syncTranscript: true,
        syncLanguage: true,
        location: "trial" /* location option for paid accounts (default is trial) */
        });

        // Set the source dynamically.
        initSource.call(this);
    });
    ```

3. Copie o código de inserção dos Insights cognitivos.

Agora você pode se comunicar com Player de Mídia do Azure.

Para obter mais informações, consulte a [demonstração do player de mídia do Azure + vi insights](https://codepen.io/videoindexer/pen/rYONrO).

### <a name="embed-the-video-indexer-cognitive-insights-widget-and-use-a-different-video-player"></a>Inserir o widget Video Indexer cognitiva insights e usar um player de vídeo diferente

Se você usar um player de vídeo diferente de Player de Mídia do Azure, será necessário manipular manualmente o player de vídeo para obter a comunicação.

1. Insira seu player de vídeo.

    Por exemplo, um player HTML5 padrão:

    ```html
    <video id="vid1" width="640" height="360" controls autoplay preload>
       <source src="//breakdown.blob.core.windows.net/public/Microsoft%20HoloLens-%20RoboRaid.mp4" type="video/mp4" /> 
       Your browser does not support the video tag.
    </video>
    ```

2. Insira o widget de Insights cognitivos.
3. Implemente a comunicação para seu player escutando o evento de "mensagem". Por exemplo:

    ```javascript
    <script>
    
        (function(){
        // Reference your player instance.
        var playerInstance = document.getElementById('vid1');
        
        function jumpTo(evt) {
          var origin = evt.origin || evt.originalEvent.origin;
        
          // Validate that the event comes from the videobreakdown domain.
          if ((origin === "https://www.videobreakdown.com") && evt.data.time !== undefined){
                
            // Call your player's "jumpTo" implementation.
            playerInstance.currentTime = evt.data.time;
               
            // Confirm the arrival to us.
            if ('postMessage' in window) {
              evt.source.postMessage({confirm: true, time: evt.data.time}, origin);
            }
          }
        }
        
        // Listen to the message event.
        window.addEventListener("message", jumpTo, false);
          
        }())    
        
    </script>
    ```

Para obter mais informações, consulte a [demonstração do player de mídia do Azure + vi insights](https://codepen.io/videoindexer/pen/YEyPLd).

## <a name="adding-subtitles"></a>Adicionando legendas

Se você inserir informações de Video Indexer com seu próprio [player de mídia do Azure](https://aka.ms/azuremediaplayer), você poderá usar o `GetVttUrl` método para obter legendas ocultas (legendas). Você também pode chamar um método JavaScript do plug-in Video Indexer AMP `getSubtitlesUrl` (como mostrado anteriormente).

## <a name="customizing-embeddable-widgets"></a>Personalizando widgets que permitem inserção

### <a name="cognitive-insights-widget"></a>Widget de Insights Cognitivos

Você pode escolher os tipos de informações desejadas. Para fazer isso, especifique-os como um valor para o seguinte parâmetro de URL que é adicionado ao código de inserção que você obtém (da API ou do aplicativo Web): `&widgets=<list of wanted widgets>` .

Os valores possíveis são: `people` , `animatedCharacters` , `keywords` , `labels` , `sentiments` , `emotions` , `topics` , `keyframes` , `transcript` , `ocr` , `speakers` , `scenes` e `namedEntities` .

Por exemplo, se você quiser inserir um widget que contenha apenas pessoas e ideias de palavras-chave, a URL de inserção do iframe terá a seguinte aparência:

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,keywords`

O título da janela do iframe também pode ser personalizado fornecendo `&title=<YourTitle>` à URL do iframe. (Ele personaliza o valor HTML `<title>` ).
   
Por exemplo, se você quiser dar à sua janela do iframe o título "MeusInsights", a URL terá esta aparência:

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?title=MyInsights`

Observe que essa opção será relevante apenas em casos em que for necessário abrir os insights em uma nova janela.

### <a name="player-widget"></a>Widget do player

Se inserir o player do Video Indexer, você poderá escolher seu tamanho especificando o tamanho do iframe.

Por exemplo:

`<iframe width="640" height="360" src="https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/" frameborder="0" allowfullscreen />`

Por padrão, Video Indexer Player gerou automaticamente legendas codificadas com base na transcrição do vídeo. A transcrição é extraída do vídeo com o idioma de origem que foi selecionado quando o vídeo foi carregado.

Se você quiser inserir com um idioma diferente, poderá adicionar `&captions=<Language Code>` à URL do player de inserção. Se desejar que as legendas sejam exibidas por padrão, você poderá passar &as legendas = true.

A URL de inserção terá esta aparência:

`https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/?captions=en-us`

#### <a name="autoplay"></a>Reprodução Automática

Por padrão, o Player começará a reproduzir o vídeo. Você pode optar por não passar `&autoplay=false` para a URL de inserção anterior.

## <a name="code-samples"></a>Exemplos de código

Consulte o repositório de [exemplos de código](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/Embedding%20widgets) que contém exemplos para video indexer API e widgets:

| Arquivo/pasta                       | Descrição                                |
|-----------------------------------|--------------------------------------------|
| `azure-media-player`              | Carregue vídeo do indexador de vídeo em um Player de Mídia do Azure personalizado.                        |
| `azure-media-player-vi-insights`  | Inserir ideias do VI com um Player de Mídia do Azure personalizado.                             |
| `control-vi-embedded-player`      | Insira o VI Player e controle-o de fora.                                    |
| `custom-index-location`           | Inserir ideias do VI de um local externo personalizado (pode ser cliente um blob).     |
| `embed-both-insights`             | Uso básico do VI insights e insights.                            |
| `embed-insights-with-AMP`         | Insira o widget VI insights com um Player de Mídia do Azure personalizado.                      |
| `customize-the-widgets`           | Insira widgets VI com opções personalizadas.                                     |
| `embed-both-widgets`              | Insira o VI Player e as informações e comunique-se entre eles.                      |
| `url-generator`                   | Gera a URL de inserção personalizada de widgets com base nas opções especificadas pelo usuário.             |
| `html5-player`                    | Inserir ideias do VI com um player de vídeo HTML5 padrão.                           |

## <a name="supported-browsers"></a>Navegadores com suporte

Para obter mais informações, consulte [navegadores com suporte](video-indexer-overview.md#supported-browsers).

## <a name="next-steps"></a>Próximas etapas

Para obter informações sobre como exibir e editar insights Video Indexer, consulte [Exibir e editar informações de video indexer](video-indexer-view-edit.md).

Além disso, confira [CodePen do indexador de vídeo](https://codepen.io/videoindexer/pen/eGxebZ).
