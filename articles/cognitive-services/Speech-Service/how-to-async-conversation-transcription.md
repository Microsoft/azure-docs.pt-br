---
title: Transcrição de conversa assíncrona – serviço de fala
titleSuffix: Azure Cognitive Services
description: Saiba como usar a transcrição de conversa assíncrona usando o serviço de fala. Disponível somente para Java e C#.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: amishu
ms.custom: devx-track-csharp
zone_pivot_groups: programming-languages-set-twenty-one
ms.openlocfilehash: 4b2dfa8d474f10d6b4ca1c46ac2b575e8d8407ff
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88934627"
---
# <a name="asynchronous-conversation-transcription"></a>Transcrição de conversas assíncrona

Neste artigo, a transcrição de conversa assíncrona é demonstrada usando a API **RemoteConversationTranscriptionClient** . Se você tiver configurado a transcrição de conversa para fazer a transcrição assíncrona e tiver um `conversationId` , poderá obter a transcrição associada a ela `conversationId` usando a API **RemoteConversationTranscriptionClient** .

## <a name="asynchronous-vs-real-time--asynchronous"></a>Assíncrono versus em tempo real + assíncrono

Com a transcrição assíncrona, você transmite o áudio da conversa, mas não precisa de uma transcrição retornada em tempo real. Em vez disso, depois que o áudio for enviado, use o `conversationId` de `Conversation` para consultar o status da transcrição assíncrona. Quando a transcrição assíncrona estiver pronta, você obterá um `RemoteConversationTranscriptionResult` .

Com mais assíncrono em tempo real, você obtém a transcrição em tempo real, mas também obtém a transcrição consultando com o `conversationId` (semelhante ao cenário assíncrono).

São necessárias duas etapas para realizar a transcrição assíncrona. A primeira etapa é carregar o áudio, escolhendo somente assíncrono ou em tempo real, além de assíncrono. A segunda etapa é obter os resultados da transcrição.

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-to/remote-conversation/csharp/examples.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-to/remote-conversation/java/examples.md)]
::: zone-end


## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Explorar nossos exemplos no GitHub](https://aka.ms/csspeech/samples)
