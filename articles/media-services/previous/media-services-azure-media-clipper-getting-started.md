---
title: Introdução ao Azure Media Clipper | Microsoft Docs
description: Introdução ao Azure Media Clipper, uma ferramenta para criação de clipes de vídeo com ativos dos Serviços de Mídia do Azure
services: media-services
keywords: clipe;subclipe;codificação;mídia
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 03/14/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 51848b9ba4d18b3ac7d652cfbd97cab6b85f2ee8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61466265"
---
# <a name="create-clips-with-azure-media-clipper"></a>Criar clipes com o Azure Media Clipper
Esta seção mostra as etapas básicas de como começar a usar o Azure Media Clipper. As próximas seções fornecem informações específicas sobre como configurar o Azure Media Clipper.

- Primeiro, adicione os links a seguir ao Player de Mídia do Azure e ao Azure Media Clipper ao cabeçalho do documento. Recomendamos especificar explicitamente uma versão do Clipper e do Player de Mídia do Azure nas URLs. Não use a última versão desses recursos em produção, pois eles estão sujeitos a alterações sob demanda.

```javascript
<!--Azure Media Player 2.1.4 or later is a prerequisite-->
<link href="//amp.azure.net/libs/amp/2.1.4/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
<script src="//amp.azure.net/libs/amp/2.1.4/azuremediaplayer.min.js"></script>
<!--Azure Media Clipper script and stylesheet-->
<link href="//amp.azure.net/libs/amc/0.1.0/azuremediaclipper.css" rel="stylesheet">
<script src="//amp.azure.net/libs/amc/0.1.0/azuremediaclipper.min.js"></script>
```

- Em seguida, adicione as classes a seguir ao elemento div no qual você gostaria de criar uma instância do Clipper.

```javascript
<div id="root" class="azure-subclipper" />
```

Opcionalmente, para habilitar o tema escuro, adicione a classe de capa escura:

```javascript
<div id="root" class="azure-subclipper dark-skin" />
```

- Em seguida, crie uma instância do Clipper com a seguinte chamada à API:

```javascript
var subclipper = new subclipper({
    selector: '#root',
    logLevel: 'info',
    restVersion: '2.0',
    minimumMarkerGap: 6,
    submitSubclipCallback: onSubmitSubclip,
    singleBitrateMp4Profile: {
            Codecs: [{
                    // Video Codec with single H264Layers
                }, {
                    // Audio Codec
                }]
    },
    multiBitrateMp4Profile: {
            Codecs: [{
                    // Video Codec with multiple H264Layers
                }, {
                    // Audio Codec
                }]
    },
    keymap: {
      // See below for more info
    },
   // Enable the Video Assets panel in the widget to automatically load assets (input contract)
    assetsPanelLoaderCallback: onLoadAssets,
    height: 600,
    subclippingMode: 'all',
    filterAssetsTypes: true,
    speedLevels: [
        { name: '4x', value: 4.0 },
        { name: '3x', value: 3.0 },
        { name: '2x', value: 2.0 },
        { name: 'normal', value: 1.0 },
        { name: '0.75x', value: 0.75 },
        { name: '0.5x', value: 0.5 },
    ],
    resetOnJobDone: false,
    autoplayVideo: true,
    language: 'en'    
});
```

Os parâmetros para a chamada do método de inicialização são:
- `selector` {REQUIRED, string}: seletor de CSS do elemento HTML correspondente no qual o widget deve ser renderizado.
- `restVersion` {REQUIRED, string}: a versão da API REST de destino dos Serviços de Mídia do Azure. A versão da REST define o formato da saída gerada pelo widget. Atualmente, há suporte apenas para 2.0.
- `submitSubclipCallback` {REQUIRED, promise} A função de retorno de chamada invocada ao clicar no botão “Enviar” do widget. A função de retorno de chamada deve esperar a saída gerada pelo widget (uma configuração do trabalho de renderização ou uma definição de filtro). Para obter mais informações, consulte Enviar retorno de chamada de subclipe.
- `logLevel` {OPTIONAL, {'info', 'warn', 'error'}}: o nível de log a ser exibido no console do navegador. Valor padrão: 'error'
- `minimumMarkerGap` {OPTIONAL, int}: o tamanho mínimo de um subclipe (em segundos). Observação: o valor deve ser maior ou igual a 6, que também é o padrão.
- `singleBitrateMp4Profile` {OPTIONAL, JSON object} o perfil de mp4 de taxa de bits única a ser usado para a configuração do trabalho de renderização gerada pelo widget. Se isso não for fornecido, ele usará o [perfil de MP4 de taxa de bits única padrão](https://docs.microsoft.com/azure/media-services/media-services-mes-preset-h264-single-bitrate-1080p).
- `multiBitrateMp4Profile` {OPTIONAL, JSON object} o perfil de mp4 de múltiplas taxas de bits a ser usado para a configuração do trabalho de renderização gerada pelo widget. Se isso não for fornecido, ele usará o [perfil de MP4 de múltiplas taxas de bits padrão](https://docs.microsoft.com/azure/media-services/media-services-mes-preset-h264-multiple-bitrate-1080p).
- `keymap` {OPTIONAL, json object} Permite personalizar os atalhos de teclado do widget. Para obter mais informações, consulte [Atalhos de teclado personalizáveis](media-services-azure-media-clipper-keyboard-shortcuts.md).
- `assetsPanelLoaderCallback` {OPTIONAL, promise} A função de retorno de chamada invocada para carregar (de forma assíncrona) uma nova página de ativos no painel de ativos sempre que o usuário rolar para baixo até a parte inferior do painel. Para obter mais informações, consulte Retorno de chamada de carregador do painel de ativos.
- `height` {OPTIONAL, number} A altura total do widget (a altura mínima é de 600 px sem o painel de ativos e 850 px com o painel de ativos).
- `subclippingMode` (OPTIONAL, {'all', 'render', 'filter'}): os modos de subclipping permitidos. O valor padrão é 'all'.
- `filterAssetsTypes` (OPTIONAL, bool): filterAssetsTypes permite mostrar/ocultar a lista suspensa de filtros do painel de ativos. O valor padrão é true.
- `speedLevels` (OPTIONAL, array): speedLevels permite configurar diferentes níveis de velocidade para o player de vídeo. Consulte a [documentação do Player de Mídia do Azure](https://amp.azure.net/libs/amp/latest/docs/#amp.player.playbackspeedoptions) para obter mais informações.
- `resetOnJobDone` (OPTIONAL, bool): resetOnJobDone permite que o Clipper redefina o subclipper para um estado inicial quando um trabalho é enviado com êxito.
- `autoplayVideo` (OPTIONAL, bool): autoplayVideo permite que o Clipper reproduza o vídeo automaticamente após o carregamento. O valor padrão é true.
- `language` {OPTIONAL, string}: language define o idioma do widget. Se isso não for especificado, o widget tentará localizar as mensagens de acordo com o idioma do navegador. Se nenhum idioma for detectado no navegador, o widget usará o inglês como padrão. Para obter mais informações, consulte a seção [Configurar localização](media-services-azure-media-clipper-localization.md).
- `languages` {OPTIONAL, JSON}: o parâmetro languages substitui o dicionário padrão de idiomas por um dicionário personalizado definido pelo usuário. Para obter mais informações, consulte a seção [Configurar localização](media-services-azure-media-clipper-localization.md).
- `extraLanguages` (OPTIONAL, JSON): o parâmetro extraLanaguages adiciona novos idiomas ao dicionário padrão. Para obter mais informações, consulte a seção [Configurar localização](media-services-azure-media-clipper-localization.md).

## <a name="typescript-definition"></a>Definição de TypeScript
Um arquivo de definição de [TypeScript](https://www.typescriptlang.org/) para o Clipper pode ser encontrado [aqui](https://amp.azure.net/libs/amc/latest/azuremediaclipper.d.ts).

## <a name="azure-media-clipper-api"></a>API do Azure Media Clipper
Esta seção documenta a superfície de API fornecida pelo Clipper.

- `ready(handler)`: oferece uma maneira de executar o JavaScript assim que o Clipper estiver totalmente carregado e pronto para ser usado.
- `load(assets)`: carrega uma lista de ativos na linha do tempo de widgets (não deve ser usado juntamente com assetsPanelLoaderCallback). Consulte este [artigo](media-services-azure-media-clipper-load-assets.md) para obter detalhes sobre como carregar ativos no Clipper.
- `setLogLevel(level)`: define o nível de log a ser exibido no console do navegador. Os valores possíveis são `info`, `warn` e `error`.
- `setHeight(height)`: define a altura total do widget em pixels (a altura mínima é de 600 px sem o painel de ativos e 850 px com o painel de ativos).
- `version`: obtém a versão do widget.

## <a name="next-steps"></a>Próximas etapas
Consulte as próximas etapas para configurar o Azure Media Clipper:
- [Carregando ativos no Azure Media Clipper](media-services-azure-media-clipper-load-assets.md)
- [Configurando atalhos de teclado personalizados](media-services-azure-media-clipper-keyboard-shortcuts.md)
- [Enviando trabalhos de recorte do Clipper](media-services-azure-media-clipper-submit-job.md)
- [Configurar localização](media-services-azure-media-clipper-localization.md)