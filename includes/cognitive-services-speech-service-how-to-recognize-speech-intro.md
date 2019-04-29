---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
origin.date: 09/24/2018
ms.date: 04/01/2019
ms.author: v-biyu
ms.openlocfilehash: 31482cc7850c574b952c454021af729da324ba15
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60769933"
---
<!-- N.B. no header, no intents here, language-agnostic -->

O [SDK de Fala](~/articles/cognitive-services/speech-service/speech-sdk.md) dos Serviços Cognitivos fornece a maneira mais simples de usar a **Conversão de Fala em Texto** em seu aplicativo com a funcionalidade completa.

1. Crie uma configuração de fala e forneça uma chave de assinatura do serviço de Fala (ou um token de autorização) e uma [região](~/articles/cognitive-services/speech-service/regions.md) como parâmetros. Altere a configuração conforme necessário. Por exemplo, fornecer um ponto de extremidade personalizado para especificar um ponto de extremidade de serviço não padrão ou selecione o idioma de entrada ou o formato de saída.

1. Crie um reconhecedor de fala da configuração de fala. Se você quiser o reconhecimento com base em uma fonte diferente de seu microfone padrão (por exemplo, o fluxo de áudio ou o arquivo de áudio), forneça uma configuração de áudio.

1. Associa eventos para operação assíncrona, se você quiser. Depois, o reconhecedor chama seus manipuladores de eventos quando houver resultados intermediários e finais. Caso contrário, o aplicativo receberá somente um resultado de transcrição final.

1. Iniciar reconhecimento. Para reconhecimento a ser realizado uma única vez, como reconhecimento de comando ou consulta, use o método `RecognizeOnceAsync()` (ou linguagem equivalente). Esse método retorna a primeira expressão reconhecida. Para o reconhecimento de execução longa, como transcrição, use o método `StartContinuousRecognitionAsync()` (ou linguagem equivalente). Vincule os eventos para resultados de reconhecimento assíncronos.

Consulte os seguintes snippets de código para cenários de reconhecimento de fala que usam o SDK de Fala.

[!INCLUDE [Get a subscription key](cognitive-services-speech-service-get-subscription-key.md)]
