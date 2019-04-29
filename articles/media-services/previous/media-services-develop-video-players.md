---
title: Desenvolver aplicativos de player de vídeo
description: O tópico fornece links para as Estruturas do Player e plug-ins que você pode usar para desenvolver seus próprios aplicativos de cliente que podem consumir a mídia de streaming dos Serviços de Mídia.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 55e419fc-4c39-4902-9c62-f41cfcd86c6c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: b8d4ff3e833dcbe92802845796e3b826735b68ce
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61465636"
---
# <a name="develop-video-player-applications"></a>Desenvolver aplicativos de player de vídeo
## <a name="overview"></a>Visão geral
Os Serviços de Mídia do Azure fornecem as ferramentas necessárias para criar aplicativos de player do cliente sofisticados e dinâmicos para a maioria das plataformas, incluindo: dispositivos iOS, dispositivos Android, Windows, Windows Phone, Xbox e decodificadores de sinais. Este tópico também fornece links para os SDKs e as Estruturas do Player que você pode usar para desenvolver seus próprios aplicativos de cliente que podem consumir a mídia de streaming dos Serviços de Mídia do Azure.

>[!NOTE]
>Quando sua conta AMS é criada, um ponto de extremidade de streaming **padrão** é adicionado à sua conta em estado **Parado**. Para iniciar seu conteúdo de streaming e tirar proveito do empacotamento dinâmico e da criptografia dinâmica, o ponto de extremidade de streaming do qual você deseja transmitir o conteúdo deve estar em estado **Executando**. 
 
## <a name="azure-media-player"></a>Player de Mídia do Azure
[Player de Mídia do Azure](https://aka.ms/ampinfo) é um player de vídeo da Web criado para reproduzir conteúdo de mídia dos Serviços de Mídia do Microsoft Azure em uma grande variedade de navegadores e dispositivos. O Player de Mídia do Azure usa padrões do setor, como HTML5, MSE (Media Source Extensions) e EME (Encrypted Media Extensions) para fornecer uma experiência avançada de streaming adaptável. Quando esses padrões não estão disponíveis em um dispositivo ou em um navegador, o Player de Mídia do Azure usa Flash e Silverlight como tecnologias de fallback. Independentemente da tecnologia de reprodução usada, os desenvolvedores terão uma interface unificada do JavaScript para acessar APIs. Isso permite que o conteúdo fornecido pelos Serviços de Mídia do Azure seja executado em vários dispositivos e navegadores sem esforço adicional.

Os Serviços de Mídia do Microsoft Azure permitem que o conteúdo seja fornecido com os formatos de streaming DASH, Smooth Streaming e HLS para reprodução de conteúdo. O Player de Mídia do Azure leva em conta esses vários formatos e executa automaticamente o melhor link com base nos recursos da plataforma/navegador. Os Serviços de Mídia do Microsoft Azure também permitem a criptografia dinâmica de ativos com criptografia PlayReady ou criptografia de envelope de 128 bits AES. O Player de Mídia do Azure permite a descriptografia do conteúdo criptografado com PlayReady e AES de 128 bits, quando configurado adequadamente. 

Para mais informações:

* [Player de Mídia do Azure](https://aka.ms/ampinfo)
* [Documentação do Player de Mídia do Azure](https://aka.ms/ampdocs) 
* [Blog de Introdução do Player de Mídia do Azure](https://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/)
* [Inscreva-se para se manter atualizado com a versão mais recente do Player de Mídia do Azure](https://aka.ms/ampsignup)
* [Adicionar novas solicitações de recurso, ideias, comentários](https://aka.ms/ampuservoice) 

## <a name="other-tools-for-creating-player-applications"></a>Outras ferramentas para criar aplicativos de player
Você também pode usar qualquer um dos seguintes SDKs:

* [SDK do cliente de Smooth Streaming](https://www.iis.net/downloads/microsoft/smooth-streaming) 
* [Aplicativo de Smooth Streaming da Windows Store](media-services-build-smooth-streaming-apps.md)
* [Plataforma de Mídia da Microsoft: Estrutura de Player](https://playerframework.codeplex.com/) 
* [Documentação da estrutura de player HTML5](https://playerframework.codeplex.com/wikipage?title=HTML5%20Player&referringTitle=Documentation) 
* [Plug-in Microsoft Smooth Streaming para OSMF](https://www.microsoft.com/download/details.aspx?id=36057) 
* [Licenciamento do kit de portabilidade de cliente do Microsoft® Smooth Streaming](https://aka.ms/sspk) 
* [Desenvolvimento de aplicativos de vídeo do XBOX](https://xbox.create.msdn.com/) 

## <a name="advertising"></a>Publicidade
Os Serviços de Mídia do Azure oferecem suporte à inserção de anúncios por meio da Plataforma de Mídia do Windows: Player Frameworks. As estruturas de player com suporte a anúncios estão disponíveis para dispositivos com Windows 8, Silverlight, Windows Phone 8 e iOS. Cada estrutura de player contém código de exemplo que mostra como implementar um aplicativo de player. Há três tipos diferentes de anúncios que você pode inserir em sua mídia:

Lineares – anúncios em tela cheia que pausam o vídeo principal

Não lineares – anúncios de sobreposição que são exibidos quando o vídeo principal está sendo reproduzido, geralmente um logotipo ou outra imagem estática colocada no player.

Complementares – anúncios que são exibidos fora do player

Os anúncios podem ser inseridos em qualquer ponto na linha do tempo do vídeo principal. Você deve indicar ao player quando reproduzir o anúncio e quais anúncios devem ser reproduzidos. Isso é feito usando um conjunto de arquivos padrão baseados em XML: Video Ad Service Template (VAST), Digital Video Multiple Ad Playlist (VMAP), Media Abstract Sequencing Template (MAST) e Digital Video Player Ad Interface Definition (VPAID). Os arquivos VAST especificam quais anúncios devem ser exibidos. Os arquivos VMAP especificam quando reproduzir diversos anúncios e contêm XML VAST. Os arquivos MAST são outra maneira de sequenciar anúncios que também podem conter XML VAST. Os arquivos VPAID definem uma interface entre o player de vídeo e o anúncio ou o servidor de anúncios. Para saber mais, confira [Inserindo anúncios](https://msdn.microsoft.com/library/dn387398.aspx).

Para obter informações sobre o suporte à legendagem oculta e a anúncios em vídeos de Streaming ao vivo, confira [Padrões de legendagem oculta e inserção de anúncios compatíveis](https://msdn.microsoft.com/library/c49e0b4d-357e-4cca-95e5-2288924d1ff3#caption_ad).

## <a name="media-services-learning-paths"></a>Roteiros de aprendizagem dos Serviços de Mídia
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Veja também
[Inserindo um vídeo de streaming adaptável MPEG-DASH em um aplicativo HTML5 com DASH.js](media-services-embed-mpeg-dash-in-html5.md)

[Repositório do dash.js do GitHub](https://github.com/Dash-Industry-Forum/dash.js)

