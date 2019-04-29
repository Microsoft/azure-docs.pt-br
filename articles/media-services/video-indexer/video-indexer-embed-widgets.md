---
title: Inserir widgets do Video Indexer em seus aplicativos
titlesuffix: Azure Media Services
description: Saiba como inserir widgets do Video Indexer em seus aplicativos.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: fe3466dcccf6381f26c823ce3deb2126c9534548
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60560380"
---
# <a name="embed-video-indexer-widgets-into-your-applications"></a>Inserir widgets do Video Indexer em seus aplicativos

Este artigo mostra como inserir widgets do Video Indexer em seus aplicativos. O Video Indexer é compatível com a inserção de dois tipos de widgets em seu aplicativo: **Insights Cognitivos** e **Player**. 

A partir da versão 2, a URL base do widget inclui a região da conta. Por exemplo, uma conta da região Oeste dos EUA gera: `https://wus2.videoindexer.ai/embed/insights/...`.

## <a name="widget-types"></a>Tipos de widget

### <a name="cognitive-insights-widget"></a>Widget de Insights Cognitivos

Um widget **Insights cognitivos** inclui todos os insights visuais que foram extraídos do processo de indexação do seu vídeo. O widget de insights é compatível com os seguintes parâmetros de URL opcionais:

|NOME|Definição|DESCRIÇÃO|
|---|---|---|
|widgets|Cadeias de caracteres separadas por vírgula|Permite controlar os insight que você deseja renderizar. <br/>Exemplo: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,search` renderizará apenas insights de interface do usuário de pessoas e marcas<br/>Opções disponíveis: people, keywords, annotations, brands, sentiments, transcript e search.<br/>não tem suporte por meio de URL na versão = 2<br/><br/>**Observação:** Não há suporte para o parâmetro de URL de widgets na versão 2. |

### <a name="player-widget"></a>Widget do player

Um widget **Player** permite transmitir o vídeo usando a taxa de bits adaptável. O widget de player é compatível com os seguintes parâmetros de URL opcionais:

|NOME|Definição|DESCRIÇÃO|
|---|---|---|
|t|Segundos a partir do início|Faz o player começar a reproduzir do ponto de tempo determinado.<br/>Exemplo: t=60|
|captions|Código de idioma|Busca a legenda no idioma determinado durante o carregamento do widget a estar disponível no menu de legendas.<br/>Exemplo: captions=en-US|
|showCaptions|Um valor booliano|Faz o player ser carregado com as legendas já habilitadas.<br/>Exemplo: showCaptions=true|
|Tipo||Ativa uma capa de player de áudio (parte do vídeo é removida).<br/>Exemplo: type=audio|
|autoplay|Um valor booliano|Indica se o player deve começar a reproduzir o vídeo quando carregado (o padrão é true).<br/>Exemplo: autoplay=false|
|Linguagem|Código de idioma|Controla o idioma do player (o padrão é en-US)<br/>Exemplo: language=de-DE|

## <a name="embedding-public-content"></a>Inserindo conteúdo público

1. Navegue até o site do [Video Indexer](https://www.videoindexer.ai/) e entre.
2. Clique no vídeo com o qual você deseja trabalhar.
3. Clique no botão "inserir" que aparece embaixo do vídeo.

    ![Widget](./media/video-indexer-embed-widgets/video-indexer-widget01.png)

    Depois de clicar no botão, um modal de inserção será exibido na tela na qual será possível escolher qual widget você deseja inserir no seu aplicativo.
    Selecionar um widget (**Player** ou **Insights cognitivos**) gera o código de inserção para você colar no seu aplicativo.
 
4. Escolha o tipo de widget desejado (**Insights cognitivos** ou **Player**).
5. Copie o código de inserção e adicione ao seu aplicativo. 

    ![Widget](./media/video-indexer-embed-widgets/video-indexer-widget02.png)

## <a name="embedding-private-content"></a>Inserindo conteúdo privado

É possível obter códigos de inserção de pop-ups de inserção (conforme mostrado na seção anterior) para vídeos **públicos** apenas. 

Se desejar inserir um vídeo **privado**, será necessário passar um token de acesso no atributo **src** do **iframe**:

`https://www.videoindexer.ai/embed/[insights | player]/<accountId>/<videoId>/?accessToken=<accessToken>`
    
Use a API [**Obter Widget de Insights**](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget) para obter o conteúdo do widget de Insights Cognitivos ou use [**Obter token de acesso do vídeo**](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Video-Access-Token?) e adicione-o como um parâmetro de consulta à URL, conforme mostrado acima. Especifique essa URL como o valor **src** do **iframe**.

Se desejar fornecer recursos de edição de insights (como temos em nosso aplicativo Web) no seu widget inserido, será necessário passar um token de acesso com permissões de edição. Use [**Obter widget de insights**](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget) ou [**Obter token de acesso do vídeo**](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Video-Access-Token?) com **&allowEdit=true**. 

## <a name="widgets-interaction"></a>Interação de widgets

O widget **Insights cognitivos** pode interagir com um vídeo em seu aplicativo. Esta seção mostra como conseguir essa interação.

![Widget](./media/video-indexer-embed-widgets/video-indexer-widget03.png)

### <a name="cross-origin-communications"></a>Comunicações entre origens

Para obter os widgets do Video Indexer para se comunicar com outros componentes, o serviço do Video Indexer faz o seguinte:

- Usa o método HTML5 de comunicação entre origens **postMessage** e 
- Valida a mensagem entre a origem do VideoIndexer.ai. 

Se você optar por implementar seu próprio código de player e fazer a integração com os widgets **Insights cognitivos**, será sua responsabilidade validar a origem da mensagem proveniente de VideoIndexer.ai.

### <a name="embed-both-types-of-widgets-in-your-application--blog-recommended"></a>Inserir os dois tipos de widgets em seu aplicativo/blog (recomendado) 

Esta seção mostra como obter a interação entre dois widgets do Video Indexer; assim, quando um usuário clicar no controle de insight em seu aplicativo, o player pulará para o momento relevante.

`<script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script>`

1. Copie o código de inserção do widget **Player**.
2. Copie o código de inserção dos **Insights cognitivos**.
3. Adicione [**Arquivo mediador**](https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js) para lidar com a comunicação entre os dois widgets:

`<script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script>`

Agora, quando um usuário clica no controle de insight em seu aplicativo, o player pula para o momento relevante.

Para obter mais informações, consulte [esta demonstração](https://codepen.io/videoindexer/pen/NzJeOb).

### <a name="embed-the-cognitive-insights-widget-and-use-azure-media-player-to-play-the-content"></a>Inserir o widget de Insights cognitivos e usar o Player de Mídia do Azure para reproduzir o conteúdo

Esta seção mostra como obter a interação entre um widget de **Insights cognitivos** e uma instância do Player de Mídia do Azure que usa o [plug-in do AMP](https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js).
 
1. Adicione um plug-in do Video Indexer para o player do AMP.<br/> `<script src="https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js"></script>`
2. Crie uma instância do Player de Mídia do Azure com o plug-in do Video Indexer.

        // Init Source
        function initSource() {
            var tracks = [{
            kind: 'captions',
            // Here is how to load vtt from VI, you can replace it with your vtt url.
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

        // Init your AMP instance
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
            // Activate the plugin
            this.videobreakdown({
            videoId: "c4c1ad4c9a",
            syncTranscript: true,
            syncLanguage: true
            });

            // Set the source dynamically
            initSource.call(this);
        });

3. Copie o código de inserção dos **Insights cognitivos**.

Agora você ser possível comunicar-se com seu Player de Mídia do Azure.

Para obter mais informações, consulte [esta demonstração](https://codepen.io/videoindexer/pen/rYONrO).

### <a name="embed-video-indexer-cognitive-insights-widget-and-use-your-own-player-could-be-any-player"></a>Inserir o widget dos Insights cognitivos do Video Indexer e usar seu próprio player (pode ser qualquer player)

Se você usar seu próprio player, será necessário cuidar da manipulação do seu player para obter a comunicação. 

1. Insira seu player de vídeo.

    Por exemplo, um player HTML5 padrão

        <video id="vid1" width="640" height="360" controls autoplay preload>
           <source src="//breakdown.blob.core.windows.net/public/Microsoft%20HoloLens-%20RoboRaid.mp4" type="video/mp4" /> 
           Your browser does not support the video tag.
        </video>    

2. Insira o widget de Insights cognitivos.
3. Implemente a comunicação para seu player escutando o evento de "mensagem". Por exemplo: 

        <script>
    
            (function(){
            // Reference your player instance
            var playerInstance = document.getElementById('vid1');
        
            function jumpTo(evt) {
              var origin = evt.origin || evt.originalEvent.origin;
        
              // Validate that event comes from the videobreakdown domain.
              if ((origin === "https://www.videobreakdown.com") && evt.data.time !== undefined){
                
                // Here you need to call your player "jumpTo" implementation
                playerInstance.currentTime = evt.data.time;
               
                // Confirm arrival to us
                if ('postMessage' in window) {
                  evt.source.postMessage({confirm: true, time: evt.data.time}, origin);
                }
              }
            }
        
            // Listen to message event
            window.addEventListener("message", jumpTo, false);
          
            }())    
        
        </script>

Para obter mais informações, consulte [esta demonstração](https://codepen.io/videoindexer/pen/YEyPLd).

## <a name="adding-subtitles"></a>Adicionando legendas

Se você inserir insights do Video Indexer com seu próprio player do AMP, será possível usar o método **GetVttUrl** para obter legendas ocultas (subtítulos). Também é possível chamar um método JavaScript do plug-in do AMP do Video Indexer **getSubtitlesUrl** (conforme mostrado anteriormente). 

## <a name="customizing-embeddable-widgets"></a>Personalizando widgets que permitem inserção

### <a name="cognitive-insights-widget"></a>Widget de insights cognitivos

É possível escolher os tipos de insights desejados especificando-os como um valor para o seguinte parâmetro de URL adicionado ao código de inserção obtido (da API ou do aplicativo Web): `&widgets=<list of wanted widgets>`.

Os valores possíveis são: people, keywords, sentiments, transcript e search.

Por exemplo, se você quer inserir um widget que contém apenas insights de pessoas e de pesquisa, a URL de inserção do iframe deve ter esta aparência:

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,search`

O título da janela do iframe também pode ser personalizado fornecendo `&title=<YourTitle>` à URL do iframe. (Ele personalizará o valor \<title> do HTML).
    
Por exemplo, se você quiser dar à sua janela do iframe o título "MeusInsights", a URL terá esta aparência:

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?title=MyInsights`

Observe que essa opção será relevante apenas em casos em que for necessário abrir os insights em uma nova janela.

### <a name="player-widget"></a>Widget do player

Se inserir o player do Video Indexer, você poderá escolher seu tamanho especificando o tamanho do iframe.

Por exemplo: 

`<iframe width="640" height="360" src="https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/" frameborder="0" allowfullscreen />`

Por padrão, o player do Video Indexer terá legendas ocultas geradas automaticamente com base na transcrição do vídeo que foi extraído do vídeo com o idioma de origem quando ele foi carregado.

Se você quiser fazer a inserção com um idioma diferente, adicione `&captions=< Language | ”all” | “false” >` à URL do player de inserção ou coloque "all" como o valor, caso queira ter todas as legendas de idiomas disponíveis.
Se quiser que as legendas sejam exibidas por padrão, passe `&showCaptions=true`.

A URL de inserção terá esta aparência: 

`https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/?captions=italian`

Se desejar desabilitar as legendas, você poderá passar "false" como valor para o parâmetro de legendas.

Reprodução automática – por padrão, o player começará a reproduzir o vídeo. é possível optar por não reproduzir passando &autoplay=false para a URL de inserção acima.

## <a name="next-steps"></a>Próximas etapas

Para obter informações sobre como exibir e editar insights do Video Indexer, consulte [este](video-indexer-view-edit.md) artigo.

Além disso, confira [Video indexer CodePen](https://codepen.io/videoindexer/pen/eGxebZ).
