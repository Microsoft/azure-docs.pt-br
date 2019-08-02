---
title: Transcrição de conversa – serviço de fala
titleSuffix: Azure Cognitive Services
description: A transcrição de conversa é um recurso avançado dos serviços de fala que combina o reconhecimento de fala em tempo real, a identificação do orador e o diarization. A transcrição de conversa é perfeita para transcrever reuniões presenciais, com a capacidade de diferenciar os alto-falantes, permite que você saiba quem disse o que e quando, permitindo que os participantes se concentrem na reunião e sigam rapidamente nas próximas etapas. Esse recurso também melhora a acessibilidade. Com a transcrição, você pode envolver ativamente os participantes com deficiências auditivas.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: acbf82335523ee226f4b899180f0f22cb94cca5e
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562883"
---
# <a name="what-is-conversation-transcription"></a>O que é a transcrição de conversa?

A transcrição de conversa é um recurso avançado dos serviços de fala que combina o reconhecimento de fala em tempo real, a identificação do orador e o diarization. A transcrição de conversa é perfeita para transcrever reuniões presenciais, com a capacidade de diferenciar os alto-falantes, permite que você saiba quem disse o que e quando, permitindo que os participantes se concentrem na reunião e sigam rapidamente nas próximas etapas. Esse recurso também melhora a acessibilidade. Com a transcrição, você pode envolver ativamente os participantes com deficiências auditivas.   

A transcrição de conversa fornece reconhecimento preciso com modelos de fala personalizáveis que você pode adaptar para entender o vocabulário específico da indústria e da empresa. Além disso, você pode emparelhar a transcrição de conversa com o SDK dos dispositivos de fala para otimizar a experiência de dispositivos com vários microfones.

>[!NOTE]
> Atualmente, a transcrição de conversa é recomendada para pequenas reuniões. Se você quiser estender a transcrição de conversa para grandes reuniões em escala, entre em contato conosco.

Este diagrama ilustra o hardware, o software e os serviços que funcionam em conjunto com a transcrição de conversa.

![O diagrama importar transcrição de conversa](media/scenarios/conversation-transcription-service.png)

>[!IMPORTANT]
> Uma matriz de sete microfone circular com configuração de geometria específica é necessária. Para obter detalhes de especificação e design, consulte [microfone do SDK do dispositivo de fala da Microsoft](https://aka.ms/cts/microphone). Para saber mais ou comprar um kit de desenvolvimento, confira [obter o SDK do dispositivo de fala da Microsoft](https://aka.ms/cts/getsdk).

## <a name="get-started-with-conversation-transcription"></a>Introdução à transcrição de conversa

Há três etapas que você precisa seguir para começar a usar a transcrição de conversa.

1. Colete exemplos de voz dos usuários.
2. Gerar perfis de usuário usando os exemplos de voz do usuário
3. Usar o SDK de fala para identificar usuários (alto-falantes) e transcrever a fala

## <a name="collect-user-voice-samples"></a>Coletar exemplos de voz do usuário

A primeira etapa é coletar gravações de áudio de cada usuário. A fala do usuário deve ser registrada em um ambiente silencioso sem ruído de fundo. O comprimento recomendado para cada amostra de áudio é entre 30 e dois minutos. Amostras de áudio mais longas resultarão em precisão aprimorada ao identificar os alto-falantes. O áudio deve ser um canal mono com uma taxa de amostra de 16 KHz.

Além das diretrizes mencionadas anteriormente, como o áudio é gravado e armazenado cabe a você – é recomendado um banco de dados seguro. Na próxima seção, vamos examinar como esse áudio é usado para gerar perfis de usuário que são usados com o SDK de fala para reconhecer os alto-falantes.

## <a name="generate-user-profiles"></a>Gerar perfis de usuário

Em seguida, você precisará enviar as gravações de áudio que você coletou para o serviço de geração de assinatura para validar o áudio e gerar perfis de usuário. O [serviço de geração de assinatura](https://aka.ms/cts/signaturegenservice) é um conjunto de APIs REST, que permitem gerar e recuperar perfis de usuário.

Para criar um perfil de usuário, você precisará usar a `GenerateVoiceSignature` API. Os detalhes de especificação e o código de exemplo estão disponíveis:

> [!NOTE]
> A transcrição de conversa está disponível atualmente em "en-US" e "zh-CN" nas seguintes regiões `centralus` : `eastasia`e.

* [Especificação REST](https://aka.ms/cts/signaturegenservice)
* [Como usar a transcrição de conversa](https://aka.ms/cts/howto)

## <a name="transcribe-and-identify-speakers"></a>Transcrever e identificar os alto-falantes

A transcrição de conversa espera fluxos de áudio multicanal e perfis de usuário como entradas para gerar transcrições e identificar alto-falantes. Os dados de perfil de usuário e de áudio são enviados ao serviço de transcrição de conversa usando o SDK de dispositivos de fala. Como mencionado anteriormente, uma matriz de sete microfone circular e o SDK de dispositivos de fala são necessários para usar a transcrição de conversa.

>[!NOTE]
> Para obter detalhes de especificação e design, consulte [microfone do SDK do dispositivo de fala da Microsoft](https://aka.ms/cts/microphone). Para saber mais ou comprar um kit de desenvolvimento, confira [obter o SDK do dispositivo de fala da Microsoft](https://aka.ms/cts/getsdk).

Para saber como usar a transcrição de conversa com o SDK de dispositivos de fala, consulte [como usar a transcrição de conversa](https://aka.ms/cts/howto).


## <a name="quick-start-with-a-sample-app"></a>Início Rápido com um aplicativo de exemplo

O SDK do dispositivo de fala da Microsoft tem um aplicativo de exemplo de início rápido para todos os exemplos relacionados ao dispositivo. A transcrição de conversa é uma delas. Você pode encontrá-lo no [início rápido do SDK do dispositivo de fala](https://aka.ms/sdsdk-quickstart) com o aplicativo de exemplo e seu código-fonte para sua referência.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba mais sobre o SDK de dispositivos de fala](speech-devices-sdk.md)
