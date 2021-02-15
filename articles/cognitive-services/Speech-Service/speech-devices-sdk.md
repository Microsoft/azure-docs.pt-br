---
title: SDK de dispositivos de fala-serviço de fala
titleSuffix: Azure Cognitive Services
description: Introdução ao SDK de Dispositivos de Fala. O serviço de fala funciona com uma ampla variedade de dispositivos e fontes de áudio. O SDK de Dispositivos de Fala é uma biblioteca pré-ajustada emparelhada com kits de desenvolvimento de matriz de microfone criados para um fim específico.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: erhopf
ms.openlocfilehash: 0309329b37436022dc88f8951853c4bb09a2c080
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2020
ms.locfileid: "95015266"
---
# <a name="what-is-the-speech-devices-sdk"></a>O que é o SDK dos dispositivos de fala?

O [serviço de fala](overview.md) funciona com uma ampla variedade de dispositivos e fontes de áudio. Agora, você pode levar os aplicativos de fala para o próximo nível com hardware e software correspondentes. O SDK de Dispositivos de Fala é uma biblioteca pré-ajustada emparelhada com kits de desenvolvimento de matriz de microfone criados para um fim específico.

O SDK de dispositivos de fala pode ajudá-lo:

- Teste rapidamente novos cenários de voz.
- Mais fácil integre o serviço de fala baseado em nuvem em seu dispositivo.
- Crie uma experiência de usuário excepcional para seus clientes.

O SDK de dispositivos de fala consome os [Speech SDK](speech-sdk.md). Usando nossos algoritmos avançados de processamento de áudio com a matriz de microfone do dispositivo para enviar o áudio para o [serviço de fala](overview.md). Ele fornece [reconhecimento de fala](speech-to-text.md) de campo muito preciso por meio de supressão de ruído, cancelamento de eco, beamforming e dereverberation.

Você também pode usar o SDK de dispositivos de fala para criar dispositivos de ambiente que tenham sua própria [palavra-chave personalizada](./custom-keyword-basics.md). Uma palavra-chave personalizada fornece uma indicação que inicia uma interação do usuário que é exclusiva de sua marca.

O SDK dos dispositivos de fala permite uma variedade de cenários habilitados para voz, como [assistentes de voz](./voice-assistants.md), sistemas de pedidos de unidade, [transcrição de conversa](./conversation-transcription.md)e palestrantes inteligentes. Você pode responder aos usuários com texto, falar com eles com uma [voz padrão](./how-to-custom-voice-create-voice.md) ou personalizada, apresentar resultados de pesquisa, [traduzir](speech-translation.md) para outros idiomas e muito mais. Estamos ansiosos para ver suas criações.

## <a name="get-the-speech-devices-sdk"></a>Obter SDK dos dispositivos de fala

### <a name="android"></a>Android

O SDK dos dispositivos de fala para Android dá suporte ao [Roobo v1](speech-devices-sdk-roobo-v1.md) e aos dispositivos equivalentes para que eles baixem a versão mais recente do [SDK de dispositivos de fala do Android](https://aka.ms/sdsdk-download-android).


Se você tiver um dispositivo Android diferente, como um telefone ou móvel, comece com o [SDK de fala do Android](speech-sdk.md)


### <a name="windows"></a>Windows

Para o Windows, o aplicativo de exemplo é fornecido como um aplicativo Java de plataforma cruzada. Baixe a versão mais recente do [SDK de dispositivos de fala do JRE](https://aka.ms/sdsdk-download-JRE).
O aplicativo é compilado com o pacote do SDK de Fala e o Java IDE do Eclipse (v4) no Windows de 64 bits. Ele é executado em um JRE (Java Runtime Environment) 8 de 64 bits.

### <a name="linux"></a>Linux

Para o Linux, o aplicativo de exemplo é fornecido como um aplicativo Java de plataforma cruzada. Baixe a versão mais recente do [SDK de dispositivos de fala do JRE](https://aka.ms/sdsdk-download-JRE).
O aplicativo é criado com o pacote do SDK de fala e o eclipse Java IDE (v4) no Linux de 64 bits (Ubuntu 16, 4, Ubuntu 18, 4, Debian 9, RHEL 8, CentOS 8). Ele é executado em um JRE (Java Runtime Environment) 8 de 64 bits.

Binários adicionais são fornecidos para dar suporte a dispositivos futuros, [Roobo v2 DDK](https://aka.ms/sdsdk-download-roobov2), [Urbetter DDK](https://aka.ms/sdsdk-download-urbetter), [GGEC palestrante](https://aka.ms/sdsdk-download-speaker), [Linux ARM32](https://aka.ms/sdsdk-download-linux-arm32)e [Linux ARM64](https://aka.ms/sdsdk-download-linux-arm64).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Escolha seu dispositivo de fala](get-speech-devices-sdk.md)
> [!div class="nextstepaction"]
> [Obter gratuitamente uma chave de assinatura dos Serviços de Fala](overview.md#try-the-speech-service-for-free)