---
title: Inserindo um vídeo de Streaming Adaptável MPEG-DASH em um Aplicativo HTML5 com DASH.js | Microsoft Docs
description: Este tópico demonstra como inserir um Vídeo de Streaming Adaptável MPEG-DASH em um Aplicativo HTML5 com DASH.js.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 5aa0e7b6-f5c3-4cc1-aa33-ed16ea4780c2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: f521fd11a2053cf8cf1ea0f9f91667fe475f0eee
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61464191"
---
# <a name="embedding-an-mpeg-dash-adaptive-streaming-video-in-an-html5-application-with-dashjs"></a>Inserção de um vídeo de streaming adaptável MPEG-DASH em um aplicativo HTML5 com DASH.js  

## <a name="overview"></a>Visão geral
O MPEG-DASH é um padrão ISO para a transmissão adaptativa de conteúdo de vídeo, o que oferece benefícios significativos para os desenvolvedores que desejam oferecer saída de streaming de vídeo adaptável de alta qualidade. Com o MPEG-DASH, o fluxo de vídeo ajusta automaticamente para uma definição inferior quando a rede ficar congestionada. Dessa maneira, é menor a probabilidade de o vídeo ser pausado enquanto o player baixa os próximos segundos a serem reproduzidos (também conhecido como buffer). À medida que o congestionamento de rede é reduzido, o player de vídeo retorna para um fluxo de qualidade mais alta. Essa capacidade de adaptação da largura de banda necessária também resulta em um tempo de início mais rápido do vídeo. Isso significa que os primeiros segundos podem ser reproduzidos em um segmento de qualidade inferior para rápido download e, em seguida, o vídeo passará a ter uma qualidade mais alta assim que conteúdo suficiente tiver sido armazenado em buffer.

O dash.js é um player de vídeo MPEG-DASH de software livre escrito em JavaScript. Seu objetivo é fornecer um player robusto de plataforma cruzada que pode ser reutilizado livremente em aplicativos que exigem reprodução de vídeo. Ele fornece reprodução de MPEG-DASH em qualquer navegador que dê suporte ao MSE (Media Source Extensions) da W3C, que hoje são o Chrome, o Microsoft Edge e o IE11 (outros navegadores demonstraram intenção de dar suporte ao MSE). Para obter mais informações sobre o DASH.js, consulte o repositório do dash.js do GitHub.

## <a name="creating-a-browser-based-streaming-video-player"></a>Criando um player de vídeo de streaming com base no navegador
Para criar uma página da Web simples que exiba um player de vídeo com os controles esperados, como reproduzir, pausar, retroceder etc., você precisará:

1. Criar uma página HTML
2. Adicionar a marca de vídeo
3. Adicionar o player dash.js
4. Inicializar o player
5. Adicionar estilo CSS
6. Exibir os resultados em um navegador que implemente o MSE

A inicialização do player pode ser concluída em algumas linhas de código do JavaScript. Usando o dash.js, é realmente simples inserir vídeo MPEG-DASH em seus aplicativos baseados em navegador.

## <a name="creating-the-html-page"></a>Criando uma página HTML
A primeira etapa é criar uma página padrão HTML contendo o elemento **video**, salvar esse arquivo como basicPlayer.html, como ilustrado no exemplo a seguir:

```html
    <!DOCTYPE html>
    <html>
      <head><title>Adaptive Streaming in HTML5</title></head>
      <body>
        <h1>Adaptive Streaming with HTML5</h1>
        <video id="videoplayer" controls></video>
      </body>
    </html>
```

## <a name="adding-the-dashjs-player"></a>Adicionando o player DASH.js
Para adicionar a implementação de referência do dash. js para o aplicativo, você precisa obter o arquivo dash da versão mais recente do projeto dash. js. O arquivo deve ser salvo na pasta JavaScript do seu aplicativo. Esse arquivo é um arquivo de conveniência que reúne todo o código dash.js necessário em um único arquivo. Ao explorar o repositório do dash.js, você encontrará os arquivos individuais, o código de teste e muito mais, mas se tudo o que você quer é usar o dash.js, o arquivo dash.all.js é do que você precisa.

Para adicionar o player dash.js aos seus aplicativos, adicione uma marca de script à seção de cabeçalho do basicPlayer.html:

```html
    <!-- DASH-AVC/265 reference implementation -->
    < script src="js/dash.all.js"></script>
```

Em seguida, crie uma função para inicializar o player quando a página for carregada. Adicione o seguinte script após a linha na qual você carregou o dash.all.js:

```html
    <script>
    // setup the video element and attach it to the Dash player
    function setupVideo() {
      var url = "http://wams.edgesuite.net/media/MPTExpressionData02/BigBuckBunny_1080p24_IYUV_2ch.ism/manifest(format=mpd-time-csf)";
      var context = new Dash.di.DashContext();
      var player = new MediaPlayer(context);
                      player.startup();
                      player.attachView(document.querySelector("#videoplayer"));
                      player.attachSource(url);
    }
    </script>
```

Primeiramente, essa função cria um DashContext. Ele é usado para configurar o aplicativo para um ambiente de tempo de execução específico. De um ponto de vista técnico, ele define as classes que a estrutura de injeção de dependência deve usar ao construir o aplicativo. Na maioria dos casos, você usa o Dash.di.DashContext.

Em seguida, instancie a classe primária da estrutura dash.js, o MediaPlayer. Essa classe contém os principais métodos necessários, como reproduzir e pausar, gerencia a relação com o elemento de vídeo e também gerencia a interpretação do arquivo MPD (Media Presentation Description) que descreve o vídeo a ser reproduzido.

A função startup() da classe MediaPlayer é chamada para garantir que o player esteja pronto para reproduzir o vídeo. Entre outras coisas, a função garante que todas as classes necessárias (conforme definido pelo contexto) tenham sido carregadas. Assim que o vídeo estiver pronto, você poderá anexar o elemento de vídeo a ele usando a função attachView(). A função de inicialização permite que o MediaPlayer injete o fluxo de vídeo no elemento e também controle a reprodução conforme a necessidade.

Passe a URL do arquivo MPD para o Media Player para que ele saiba qual vídeo deverá ser reproduzido. A função setupVideo() recém-criado precisará ser executada depois que a página tiver sido totalmente carregada. Faça isso usando o evento onload do elemento body. Altere o elemento `<body>` para:

```html
    <body onload="setupVideo()">
```

Por fim, defina o tamanho do elemento de vídeo usando CSS. Em um ambiente de streaming adaptável, isso é importante principalmente porque o tamanho do vídeo que está sendo reproduzido pode mudar conforme a reprodução se adapta às condições em constante mudança da rede. Nesta simples demonstração, basta forçar o elemento de vídeo a ser 80% da janela do navegador disponível adicionando o seguinte CSS à seção de cabeçalho da página:

```html
    <style>
    video {
      width: 80%;
      height: 80%;
    }
    </style>
```

## <a name="playing-a-video"></a>Reproduzindo um vídeo
Para reproduzir um vídeo, aponte o navegador no arquivo basicPlayback.html e clique em Reproduzir no player de vídeo exibido.

## <a name="media-services-learning-paths"></a>Roteiros de aprendizagem dos Serviços de Mídia
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Veja também
[Desenvolver aplicativos de player de vídeo](media-services-develop-video-players.md)

[Repositório do dash.js do GitHub](https://github.com/Dash-Industry-Forum/dash.js) 

