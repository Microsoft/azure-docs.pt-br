---
title: Player de Mídia do Azure métodos de API
description: A API Player de Mídia do Azure permite que você interaja com o vídeo por meio de JavaScript, independentemente de o navegador estar executando o vídeo por meio de vídeo em HTML5, Flash, Silverlight ou qualquer outra tecnologia de reprodução com suporte.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: d1ad0cbe30ce20e61a31f0534a47fe031f6f257b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "81727262"
---
# <a name="api"></a>API #

A API Player de Mídia do Azure permite que você interaja com o vídeo por meio de JavaScript, independentemente de o navegador estar executando o vídeo por meio de vídeo em HTML5, Flash, Silverlight ou qualquer outra tecnologia de reprodução com suporte.

## <a name="referencing-the-player"></a>Referenciando o Player ##

Para usar as funções de API, você precisa acessar o objeto Player. Felizmente, é fácil obter. Você só precisa ter certeza de que sua marca de vídeo tem uma ID. O código de inserção de exemplo tem uma ID de `vid1` . Se você tiver vários vídeos em uma página, verifique se cada marca de vídeo tem uma ID exclusiva.

`var myPlayer = amp('vid1');`

> [!NOTE]
> Se o Player ainda não tiver sido inicializado por meio do atributo de configuração de dados ou outro método, isso também inicializará o Player.

## <a name="wait-until-the-player-is-ready"></a>Aguarde até que o Player esteja pronto ##

O tempo que leva Player de Mídia do Azure para configurar o vídeo e a API variará dependendo da tecnologia de reprodução que está sendo usada. Em geral, o HTML5 será muito mais rápido para carregar do que o flash ou o Silverlight. Por esse motivo, a função ' Ready ' do Player deve ser usada para disparar qualquer código que exija a API do Player.

```javacript
    amp("vid_1").ready(function(){
      var myPlayer = this;

      // EXAMPLE: Start playing the video.
      myPlayer.play();
    });
```

OU

```javacript
    var myPlayer = amp("vid_1", myOptions, function(){
        //this is the ready function and will only execute after the player is loaded
    });
```

## <a name="api-methods"></a>Métodos de API ##

Agora que você tem acesso a um player pronto, é possível controlar o vídeo, obter valores ou responder a eventos de vídeo. Os nomes de função Player de Mídia do Azure API tentam seguir a [API de mídia do HTML5](http://www.whatwg.org/specs/web-apps/current-work/multipage/the-video-element.html). A principal diferença é que as funções getter/setter são usadas para propriedades de vídeo.

```javacript
    // setting a property on a bare HTML5 video element
    myVideoElement.currentTime = 120;

    // setting a property with Azure Media Player
    myPlayer.currentTime(120);
```

## <a name="registering-for-events"></a>Registrando para eventos ##
Os eventos devem ser registrados diretamente após inicializar o Player pela primeira vez para garantir que todos os eventos sejam relatados adequadamente ao aplicativo e devem ser feitos fora do evento Ready.

```javacript
    var myPlayer = amp("vid_1", myOptions, function(){
        //this is the ready function and will only execute after the player is loaded
    });
    myPlayer.addEventListener(amp.eventName.error, _ampEventHandler);
    //add other event listeners
```

## <a name="next-steps"></a>Próximas etapas ##

<!---Some context for the following links goes here--->
- [Início rápido do Player de Mídia do Azure](azure-media-player-quickstart.md)