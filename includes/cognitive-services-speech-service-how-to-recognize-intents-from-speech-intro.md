---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 09/24/2018
ms.author: wolfma
ms.openlocfilehash: 80bf9247bbb07fa61b7153e321b1991b82a9d616
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60805501"
---
<!-- N.B. no header, language-agnostic -->

O [SDK de Fala](~/articles/cognitive-services/speech-service/speech-sdk.md) dos Serviços Cognitivos da Microsoft proporciona uma maneira de reconhecer **intenções de fala** e é compatível com o [LUIS (Serviço Inteligente de Reconhecimento Vocal)](https://www.luis.ai/home) dos Serviços Cognitivos.

1. Criar uma configuração de fala usando uma chave de assinatura do LUIS e uma [região](~/articles/cognitive-services/speech-service/regions.md#intent-recognition) como parâmetros. A chave de assinatura LUIS é chamada de **chave do ponto de extremidade** na documentação do serviço. Não é possível usar a chave de criação do LUIS. (Veja a observação mais adiante nesta seção.)

1. Crie um reconhecedor de intenção com base na configuração de fala. Se você quiser o reconhecimento com base em uma fonte diferente de seu microfone padrão (por exemplo, o fluxo de áudio ou o arquivo de áudio), forneça uma configuração de áudio.

1. Obter o modelo de reconhecimento vocal baseado em sua **AppId**. Adicione as intenções de que você precisar.

1. Associa eventos para operação assíncrona, se você quiser. Depois, o reconhecedor chama seus manipuladores de eventos quando há resultados intermediários e finais (incluindo intenções). Se você não associar os eventos, o aplicativo receberá somente um resultado de transcrição final.

1. Iniciar o reconhecimento de intenção. Para reconhecimento a ser realizado uma única vez, como reconhecimento de comando ou consulta, use o método `RecognizeOnceAsync()`. Esse método retorna a primeira expressão reconhecida. Para o reconhecimento de execução longa, use o método `StartContinuousRecognitionAsync()`. Vincule os eventos para resultados de reconhecimento assíncronos.

Consulte os seguintes snippets de código para cenários de reconhecimento de intenção que usam o SDK de Fala. Substitua os valores no exemplo por sua chave de assinatura (chave de ponto de extremidade) do LUIS, a [região da assinatura](~/articles/cognitive-services/speech-service/regions.md#intent-recognition) e a **AppId** do modelo de intenção.

> [!NOTE]
> Em contraste com outros serviços compatíveis com o SDK de Fala, o reconhecimento de intenção requer uma chave de assinatura específica (ponto de extremidade do LUIS). Para obter informações sobre a tecnologia de reconhecimento de intenção, consulte o [site do LUIS](https://www.luis.ai). Para obter informações sobre como adquirir a **chave do ponto de extremidade**, consulte [Criar uma chave de ponto de extremidade do LUIS](https://docs.microsoft.com/azure/cognitive-services/LUIS/luis-how-to-azure-subscription).
