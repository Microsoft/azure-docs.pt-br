---
title: Visão geral do Player de Mídia do Azure
description: Saiba mais sobre o Player de Mídia do Azure.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: 9b99e24e47da69aa0f081e056230f4b0c1aa6bb1
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93321732"
---
# <a name="azure-media-player-overview"></a>Visão geral do Player de Mídia do Azure #

O Player de Mídia do Azure é um player de vídeo da Web que reproduz conteúdo de mídia dos [Serviços de Mídia do Microsoft Azure](https://azure.microsoft.com/services/media-services/) em uma ampla variedade de navegadores e dispositivos. O Player de Mídia do Azure usa padrões do setor, como HTML5, MSE (Media Source Extensions) e EME (Encrypted Media Extensions) para fornecer uma experiência avançada de streaming adaptável.  Quando esses padrões não estão disponíveis em um dispositivo ou em um navegador, o Player de Mídia do Azure usa Flash e Silverlight como tecnologias de fallback. Independentemente da tecnologia de reprodução usada, os desenvolvedores têm uma interface JavaScript unificada para acessar as APIs, permitindo que o conteúdo fornecido pelos Serviços de Mídia do Azure seja reproduzido em uma ampla gama de dispositivos e navegadores sem nenhum esforço extra.

Os Serviços de Mídia do Microsoft Azure permitem que o conteúdo seja fornecido com os formatos de streaming DASH, HLS e Smooth Streaming para reprodução de conteúdo. O Player de Mídia do Azure leva em conta esses vários formatos e executa automaticamente o melhor link com base nos recursos da plataforma/navegador. Os Serviços de Mídia do Microsoft Azure também proporcionam criptografia dinâmica de ativos com criptografia comum (PlayReady ou Widevine) ou criptografia de envelope AES de 128 bits. O Player de Mídia do Azure permite a descriptografia do conteúdo criptografado com PlayReady e AES de 128 bits, quando configurado adequadamente.  Para entender como configurar o player, confira a seção [Conteúdo Protegido](azure-media-player-protected-content.md).

Para solicitar novos recursos ou fornecer ideias ou comentários, envie sua mensagem para o [UserVoice do Player de Mídia do Azure](https://aka.ms/ampuservoice). Se tiver problemas ou perguntas específicas ou encontrar bugs, entre em contato conosco em ampinfo@microsoft.com.

> [!NOTE]
> Observe que Player de Mídia do Azure só dá suporte a fluxos de mídia dos Serviços de Mídia do Azure.

## <a name="license"></a>Licença ##

O Player de Mídia do Azure é licenciado e está sujeito aos termos descritos nos Termos de licença para software Microsoft para o Player de Mídia do Azure. Confira o [arquivo de licença](/legal/azure-media-player/azure-media-player-license) para ver os termos completos. Para obter mais informações, confira a [Política de Privacidade](https://www.microsoft.com/en-us/privacystatement/default.aspx).

Copyright 2015 Microsoft Corporation.

## <a name="next-steps"></a>Próximas etapas ##

- [Início rápido do Player de Mídia do Azure](azure-media-player-quickstart.md)
