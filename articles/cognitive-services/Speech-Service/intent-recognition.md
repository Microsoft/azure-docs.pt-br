---
title: Visão geral do reconhecimento de intenção-serviço de fala
titleSuffix: Azure Cognitive Services
description: O reconhecimento de intenção permite que você reconheça os objetivos dos usuários predefinidos. Este artigo é uma visão geral dos benefícios e funcionalidades do serviço de reconhecimento de intenção.
services: cognitive-services
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: v-demjoh
keywords: reconhecimento de intenção
ms.openlocfilehash: 215b25e440b8cad76e0656e47d32b184edf4ac66
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2020
ms.locfileid: "95018694"
---
# <a name="what-is-intent-recognition"></a>O que é o reconhecimento de intenção?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Nesta visão geral, você aprenderá sobre os benefícios e capacidades do reconhecimento de intenção. O SDK de Fala dos Serviços Cognitivos integra-se ao LUIS (Reconhecimento vocal) para fornecer reconhecimento de intenção. Uma intenção é algo que o usuário deseja fazer: agendar um voo, conferir o clima ou fazer uma chamada.
Usando o reconhecimento de intenção, seus aplicativos, ferramentas e dispositivos podem determinar o que o usuário deseja iniciar ou fazer com base nas opções definidas no LUIS.

## <a name="luis-key-required"></a>Chave LUIS necessária

* O LUIS integra-se ao serviço de Fala para reconhecer intenções de fala. Você não precisa de uma assinatura do serviço de Fala, apenas do LUIS.
* O reconhecimento da intenção de fala é integrado ao SDK. Você pode usar uma chave LUIS com o serviço de fala.
* O reconhecimento de intenção por meio do SDK de fala é [oferecido em um subconjunto de regiões com suporte do Luis](./regions.md#intent-recognition).

## <a name="get-started"></a>Introdução

Consulte o guia de [início rápido](quickstarts/intent-recognition.md) para começar a usar o reconhecimento de intenção.

## <a name="sample-code"></a>Código de exemplo

Código de exemplo para reconhecimento de intenção:

* [Início Rápido: usar aplicativo de automação Inicial predefinido](../luis/luis-get-started-create-app.md)
* [Reconhecer as intenções da fala usando o SDK de Fala para C#](./how-to-recognize-intents-from-speech-csharp.md)
* [Reconhecimento de intenção e outros serviços de fala usando o Unity em C #](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/unity/speechrecognizer)
* [Reconhecer tentativas usando o SDK de fala para Python](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/python/console)
* [Reconhecimento de intenção e outros serviços de fala usando o SDK de fala para C++ no Windows](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/cpp/windows/console)
* [Reconhecimento de intenção e outros serviços de fala usando o SDK de fala para Java no Windows ou Linux](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/java/jre/console)
* [Reconhecimento de intenção e outros serviços de fala usando o SDK de fala para JavaScript em um navegador da Web](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/js/browser)

## <a name="reference-docs"></a>Documentos de Referência

* [SDK da fala](./speech-sdk.md)

## <a name="next-steps"></a>Próximas etapas

* Concluir o [início rápido](quickstarts/intent-recognition.md) do reconhecimento de intenção
* [Obter gratuitamente uma chave de assinatura dos Serviços de Fala](overview.md#try-the-speech-service-for-free)
* [Obter o SDK de Fala](speech-sdk.md)