---
title: Sobre o SDK de dispositivos de fala-serviço de fala
titleSuffix: Azure Cognitive Services
description: Introdução ao SDK de Dispositivos de Fala. O Serviço de Fala funciona com uma ampla variedade de dispositivos e fontes de áudio. Agora, você pode levar os aplicativos de fala para o próximo nível com hardware e software correspondentes. O SDK de Dispositivos de Fala é uma biblioteca pré-ajustada emparelhada com kits de desenvolvimento de matriz de microfone criados para um fim específico.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: f2819dd2194193ffa8171034fdbe01ac4cb26174
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68558915"
---
# <a name="about-the-speech-devices-sdk"></a>Sobre os dispositivos de fala do SDK

Os [serviços de fala](overview.md) funcionam com uma ampla variedade de dispositivos e fontes de áudio. Agora, você pode levar os aplicativos de fala para o próximo nível com hardware e software correspondentes. O SDK de Dispositivos de Fala é uma biblioteca pré-ajustada emparelhada com kits de desenvolvimento de matriz de microfone criados para um fim específico.

O SDK de dispositivos de fala pode ajudá-lo:

* Teste rapidamente novos cenários de voz.
* Integre mais facilmente os serviços de fala baseados em nuvem em seu dispositivo.
* Crie uma experiência de usuário excepcional para seus clientes.

O SDK de dispositivos de fala consome os [Speech SDK](speech-sdk.md). Ele usa o SDK de fala para enviar o áudio que é processado pelo nosso algoritmo de processamento de áudio avançado da matriz de microfone do dispositivo para os [serviços de fala](overview.md). Usa o áudio multicanal para fornecer um [reconhecimento de fala em campo distante](speech-to-text.md) mais preciso através da supressão de ruído, cancelamento de eco, beamforming e remoção da reverberação.

Você também pode usar o SDK de dispositivos de fala para criar dispositivos de ambiente que tenham sua própria [palavra de ativação personalizada](speech-devices-sdk-create-kws.md) para que a indicação que inicia uma interação do usuário seja exclusiva à sua marca.

O SDK dos dispositivos de fala facilita uma variedade de cenários habilitados para voz, como [assistentes virtuais de primeiro voz personalizados](https://aka.ms/bots/speech/va), sistemas de pedidos de unidades, [transcrição de conversa](conversation-transcription-service.md)e palestrantes inteligentes. Você pode responder aos usuários com texto, falar com eles com uma [voz padrão](how-to-customize-voice-font.md) ou personalizada, apresentar resultados de pesquisa, [traduzir](speech-translation.md) para outros idiomas e muito mais. Estamos ansiosos para ver suas criações.

## <a name="get-the-speech-devices-sdk"></a>Obter SDK dos dispositivos de fala

### <a name="android"></a>Android

Para dispositivos Android, baixe a versão mais recente do [SDK de dispositivos de fala do Android](https://aka.ms/sdsdk-download-android).

### <a name="windows"></a>Windows

Para o Windows, o aplicativo de exemplo é fornecido como um aplicativo Java de plataforma cruzada. Baixe a versão mais recente do [SDK de dispositivos de fala do JRE](https://aka.ms/sdsdk-download-JRE).
O aplicativo é compilado com o pacote do SDK de Fala e o Java IDE do Eclipse (v4) no Windows de 64 bits. Ele é executado em um JRE (Java Runtime Environment) 8 de 64 bits.

### <a name="linux"></a>Linux

Para o Linux, o aplicativo de exemplo é fornecido como um aplicativo Java de plataforma cruzada. Baixe a versão mais recente do [SDK de dispositivos de fala do JRE](https://aka.ms/sdsdk-download-JRE).
O aplicativo é compilado com o pacote do SDK de Fala e o Java IDE do Eclipse (v4) no Linux 64 bits (Ubuntu 16.04, Ubuntu 18.04, Debian 9). Ele é executado em um JRE (Java Runtime Environment) 8 de 64 bits.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Escolha seu dispositivo de fala](get-speech-devices-sdk.md)
>
> [!div class="nextstepaction"]
> [Obter gratuitamente uma chave de assinatura do Serviço de Fala](get-started.md)
