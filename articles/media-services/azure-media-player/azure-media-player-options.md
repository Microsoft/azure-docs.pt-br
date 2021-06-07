---
title: Opções de Player de Mídia do Azure
description: O código de inserção de Player de Mídia do Azure é simplesmente uma marca de vídeo HTML5, portanto, para muitas das opções, você pode usar os atributos de marca padrão para definir as opções.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.custom: devx-track-js
ms.openlocfilehash: 392ecff2ab120e713a07d130493a4a8339dae5ab
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92366863"
---
# <a name="options"></a>Opções #

## <a name="setting-options"></a>Opções de configuração ##

O código de inserção de Player de Mídia do Azure é simplesmente uma marca de vídeo HTML5, portanto, para muitas das opções, você pode usar os atributos de marca padrão para definir as opções.

`<video controls autoplay ...>`

Como alternativa, você pode usar o atributo Data-setup para fornecer opções no formato [JSON](http://json.org/example.html) . Isso também é como você definiria opções que não são padrão para a marca de vídeo.

`<video data-setup='{ "controls": true, "autoplay": false }'...>`

Por fim, se você não estiver usando o atributo de configuração de dados para disparar a instalação do Player, poderá passar um objeto com as opções do Player como o segundo argumento na função de configuração do JavaScript.

`amp("vid1", { "controls": true, "autoplay": false });`

> [!NOTE]
> As opções no Construtor são definidas apenas na primeira inicialização antes que a origem seja definida.  Se você quiser modificar as opções no mesmo elemento de Player de Mídia do Azure inicializado, deverá atualizar as opções antes de alterar a origem. Você pode atualizar as opções em JavaScript usando `myPlayer.options({/*updated options*/});` . Observe que apenas as opções alteradas serão afetadas, todas as outras opções definidas anteriormente serão mantidas.

## <a name="individual-options"></a>Opções individuais ##

> [!NOTE]
>Os atributos de marca de vídeo só podem ser verdadeiros ou falsos (boolianos), basta incluir o atributo (sem sinal de igual) para ativá-lo ou excluí-lo para desativá-lo. Por exemplo, para ativar os controles: incorreto, `<video controls="true" ...>` `<video controls ...>` o maior problema que as pessoas executam é tentar definir esses valores como false usando false como o valor (por exemplo, Controls = "false") que realmente faz o oposto e define o valor como true porque o atributo ainda está incluído.

### <a name="controls"></a>controls ###

A opção Controls define se o Player tem ou não controles com os quais o usuário pode interagir. Sem controles, a única maneira de iniciar a reprodução de vídeo é com o atributo de reprodução automática ou por meio da API.

`<video controls ...>` ou `{ "controls": true }`

### <a name="autoplay"></a>autoplay ###

Se a reprodução automática for verdadeira, o vídeo começará a ser reproduzido assim que a página for carregada (sem qualquer interação do usuário).

> [!NOTE]
> Essa opção não tem suporte em dispositivos móveis como Windows Phone, Apple iOS e Android. Os dispositivos móveis bloqueiam a funcionalidade de reprodução automática para evitar o uso de planos de dados mensais do consumidor (geralmente caros). Um usuário tocar/clicar é necessário para iniciar o vídeo nesse caso.

`<video autoplay ...>`or `{ "autoplay": true }`

### <a name="poster"></a>pôster ###
O atributo pôster define a imagem que é exibida antes do início da reprodução do vídeo. Isso geralmente é um quadro do vídeo ou uma tela de título personalizada. Assim que o usuário clica em reproduzir, a imagem desaparece.

`<video poster="myPoster.jpg" ...>` ou `{ "poster": "myPoster.jpg" }`

### <a name="width"></a>width ###

O atributo width define a largura de exibição do vídeo.

`<video width="640" ...>` ou `{ "width": 640 }`

### <a name="height"></a>altura ###

O atributo Height define a altura de exibição do vídeo.

`<video height="480" ...>` ou `{ "height": 480 }`

### <a name="plugins"></a>plug-ins ###

O JSON de plug-ins determina quais plug-ins são carregados com essa instância do AMP permite que você configure as opções que o plug-in pode ter.

   `<video... data-setup='{plugins: { "contentTitle": {"name": "Azure Medi Services Overview"}}}'...>`

Para obter mais informações sobre o desenvolvimento e o uso do plug-in, consulte [Writing plugins](azure-media-player-writing-plugins.md)

### <a name="other-options"></a>outras opções ###

Outras opções podem ser definidas na `<video>` marca usando o `data-setup` parâmetro que usa um JSON.
`<video ... data-setup='{"nativeControlsForTouch": false}'>`

#### <a name="nativecontrolsfortouch"></a>nativeControlsForTouch ####

Isso é definido explicitamente como false. Ao definir como false, ele permitirá que a aparência do Player de Mídia do Azure seja processada da mesma forma pelas plataformas.  Além disso, ao contrário do nome, o toque ainda estará habilitado.

### <a name="fluid"></a>flexível ###

Ao definir essa opção como verdadeiro, o elemento de vídeo assumirá a largura total do contêiner pai e a altura será ajustada para ajustar um vídeo com uma taxa de proporção de 16:9 padrão.

`<video ... data-setup='{"fluid": true}'>`

`fluid` a opção substitui `width` as configurações explícitas e `height` . Essa opção só está disponível na versão Player de Mídia do Azure `2.0.0` e posterior.

### <a name="playbackspeed"></a>playbackSpeed ###

`playbackSpeed` a opção controla o controle playbackSpeed e o conjunto de configurações de velocidade de reprodução disponíveis para o usuário. `playbackSpeed` usa um objeto. Para habilitar o controle de velocidade de reprodução na barra de controle, a propriedade `enabled` do objeto precisa ser definida como true. Um exemplo de como habilitar a velocidade de reprodução na marcação:

`<video ... data-setup='{"playbackSpeed": {"enabled": true}}'>`


Outras propriedades da `playbackSpeed` configuração são fornecidas pelo objeto [PlaybackSpeedOptions](/javascript/api/azuremediaplayer/amp.player.playbackspeedoptions) .

Exemplo de configuração de opções de velocidade de reprodução em JavaScript:

```javascript
    var myPlayer = amp('vid1', {
        fluid: true,
        playbackSpeed: {
            enabled: true,
            initialSpeed: 1.0,
            speedLevels: [
                { name: "x4.0", value: 4.0 },
                { name: "x3.0", value: 3.0 },
                { name: "x2.0", value: 2.0 },
                { name: "x1.75", value: 1.75 },
                { name: "x1.5", value: 1.5 },
                { name: "x1.25", value: 1.25 },
                { name: "normal", value: 1.0 },
                { name: "x0.75", value: 0.75 },
                { name: "x0.5", value: 0.5 },
            ]
        }
    });
```

Essa opção só está disponível no Player de Mídia do Azure versão 2.0.0 e posterior.

### <a name="staledatatimelimitinsec"></a>staleDataTimeLimitInSec ###

A `staleDataTimeLimitInSec` opção é uma otimização que permite que você configure quantos segundos de dados obsoletos você gostaria de manter nos buffers do mediary. Isso está desabilitado por padrão.

### <a name="cea708captionssettings"></a>cea708CaptionsSettings ###

A configuração habilitado como verdadeiro permite que você exiba a legenda de CEA ao vivo em suas transmissões ao vivo e arquivos dinâmicos. O atributo de rótulo não é necessário, se não estiver incluído, o Player retornará para um rótulo padrão.

```javascript
     cea708CaptionsSettings: {
                enabled: true,
                srclang: 'en',
                label: 'Live CC'
            }
```

Essa opção só está disponível no Player de Mídia do Azure versão 2.1.1 e posterior.

## <a name="next-steps"></a>Próximas etapas ##

- [Início rápido do Player de Mídia do Azure](azure-media-player-quickstart.md)
