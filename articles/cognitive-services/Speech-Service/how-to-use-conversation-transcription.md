---
title: Início rápido de transcrição de conversa em tempo real – serviço de fala
titleSuffix: Azure Cognitive Services
description: Saiba como usar a transcrição de conversa em tempo real com o SDK de fala. A transcrição de conversa permite transcrever reuniões e outras conversas com a capacidade de adicionar, remover e identificar vários participantes transmitindo o áudio para o serviço de fala.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: trbye
zone_pivot_groups: acs-js-csharp
ms.openlocfilehash: ae3d7b329c55ce0be101cee73e1fc7674a2d75aa
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2020
ms.locfileid: "95026532"
---
# <a name="get-started-with-real-time-conversation-transcription"></a>Introdução à transcrição de conversa em tempo real

A API **ConversationTranscriber** do SDK de fala permite transcrever reuniões e outras conversas com a capacidade de adicionar, remover e identificar vários participantes transmitindo o áudio para o serviço de fala usando o `PullStream` ou o `PushStream` . Primeiro, você cria assinaturas de voz para cada participante usando a API REST e, em seguida, usa as assinaturas de voz com o SDK para transcrever conversas. Consulte a [visão geral](conversation-transcription.md) da transcrição de conversa para obter mais informações.

## <a name="limitations"></a>Limitações

* Disponível somente nas seguintes regiões de assinatura: `centralus` , `eastasia` , `eastus` , `westeurope`
* Requer uma matriz de vários microfones circulares de 7 Mic com um fluxo de referência de reprodução. A matriz de microfone deve atender à [nossa especificação](./speech-devices-sdk-microphone.md).
* O [SDK dos dispositivos de fala](speech-devices-sdk.md) fornece dispositivos adequados e um aplicativo de exemplo que demonstra a transcrição da conversa.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você tem uma conta do Azure e uma assinatura do Serviço de Fala. Se você não tiver uma conta e uma assinatura, [experimente o serviço de Fala gratuitamente](overview.md#try-the-speech-service-for-free).

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript Basics include](includes/how-to/conversation-transcription/real-time-javascript.md)]
::: zone-end

::: zone pivot="programming-language-csharp"
[!INCLUDE [C# Basics include](includes/how-to/conversation-transcription/real-time-csharp.md)]
::: zone-end

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> Transcrição de conversa [assíncrona](how-to-async-conversation-transcription.md) 
>  Código de exemplo do [dispositivo ROOBO](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/blob/master/Samples/Java/Android/Speech%20Devices%20SDK%20Starter%20App/example/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/sdsdkstarterapp/ConversationTranscription.java) 
>  [Código de exemplo do kit de desenvolvimento do Azure Kinect](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/blob/master/Samples/Java/Windows_Linux/SampleDemo/src/com/microsoft/cognitiveservices/speech/samples/Cts.java)