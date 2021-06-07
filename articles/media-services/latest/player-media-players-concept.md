---
title: Visão geral de players de mídia para Serviços de Mídia
description: Quais players de mídia posso usar com os Serviços de Mídia do Azure? O Player de Mídia do Azure, o Shaka e o Video.js, por enquanto.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 3/08/2021
ms.openlocfilehash: 43d0a8ee82a84a57be7c8ded9e7f5afc172bd9d6
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106281128"
---
# <a name="media-players-for-media-services"></a>Players de mídia para Serviços de Mídia

É possível usar vários players de mídia com os Serviços de Mídia.

## <a name="azure-media-player"></a>Player de Mídia do Azure

O Player de Mídia do Azure é um player de vídeo compatível com uma ampla variedade de navegadores e dispositivos. O Player de Mídia do Azure usa padrões do setor, como HTML5, MSE (Media Source Extensions) e EME (Encrypted Media Extensions) para fornecer uma experiência avançada de streaming adaptável. Quando esses padrões não estiverem disponíveis em um dispositivo ou navegador, o Player de Mídia do Azure usará o Flash e o Silverlight como tecnologias de fallback. Os desenvolvedores terão uma interface unificada do JavaScript para acessar APIs, não importa a tecnologia de reprodução usada. O conteúdo fornecido pelos Serviços de Mídia do Azure poderá ser executado em diversos dispositivos e navegadores sem nenhum esforço adicional.

Confira a [documentação do Player de Mídia do Azure](../azure-media-player/azure-media-player-overview.md).

## <a name="shaka"></a>Shaka

O Shaka Player é uma biblioteca JavaScript de software livre para mídias adaptáveis. Ele executa formatos de mídias adaptáveis (como DASH e HLS) em um navegador, sem usar plug-ins nem o Flash. Em vez disso, o Shaka Player usa Extensões de Fontes de Mídia e Extensões de Mídia Criptografada de padrões abertos da Web.

Confira [Como usar o Shaka Player com os Serviços de Mídia do Azure](player-shaka-player-how-to.md).

## <a name="videojs"></a>Video.js

O Video.js é um player de vídeo que executa formatos de mídia adaptáveis (como DASH e HLS) em um navegador. O Video.js usa Extensões de Fontes de Mídia e Extensões de Mídia Criptografada de padrões abertos da Web. Ele é compatível com a reprodução de vídeo em desktops e dispositivos móveis. A documentação oficial do Video.js pode ser encontrada em https://docs.videojs.com/.

Confira [Como usar o Video.js Player com os Serviços de Mídia do Azure](player-how-to-video-js-player.md).


## <a name="next-steps"></a>Próximas etapas ##

- [Início rápido do Player de Mídia do Azure](../azure-media-player/azure-media-player-quickstart.md)