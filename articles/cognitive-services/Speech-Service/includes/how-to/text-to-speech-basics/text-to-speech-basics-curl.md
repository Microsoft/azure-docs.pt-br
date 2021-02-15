---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 10/09/2020
ms.author: v-jawe
ms.openlocfilehash: 8a877e1773431053c5ad7344209076cb868a0ee3
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94424948"
---
Neste início rápido, você aprenderá converter texto em fala usando o serviço de Fala e o cURL.

Para obter uma visão de alto nível dos conceitos de Conversão de Texto em Fala, confira o artigo de [visão geral](../../../text-to-speech.md).

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você tem uma conta do Azure e uma assinatura do Serviço de Fala. Se você não tiver uma conta e uma assinatura, [experimente o serviço de Fala gratuitamente](../../../overview.md#try-the-speech-service-for-free).

## <a name="convert-text-to-speech"></a>Converter texto em fala

No prompt de comando, execute o comando a seguir. Será necessário inserir os valores a seguir no comando.
- Sua chave de assinatura do serviço de Fala.
- Sua região do serviço de Fala.

Talvez também seja interessante alterar os valores a seguir.
- O valor do cabeçalho `X-Microsoft-OutputFormat`, que controla o formato de saída de áudio. Você pode encontrar uma lista de formatos de saída de áudio com suporte na [referência da API REST de conversão de texto em fala](../../../rest-text-to-speech.md#audio-outputs).
- A voz de saída. Para obter uma lista de vozes disponíveis para seu ponto de extremidade de Fala, confira a próxima seção.
- O arquivo de saída. Neste exemplo, direcionamos a resposta do servidor para um arquivo chamado `output.wav`.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/text-to-speech.sh":::

## <a name="list-available-voices-for-your-speech-endpoint"></a>Listar as vozes disponíveis para seu ponto de extremidade de Fala

Para listar as vozes disponíveis para seu ponto de extremidade de Fala, execute o comando a seguir.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/get-voices.sh" id="request":::

Você deverá receber uma resposta semelhante à mostrada a seguir.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/get-voices.sh" id="response":::