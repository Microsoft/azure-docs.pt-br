---
title: Regiões-serviço de fala
titleSuffix: Azure Cognitive Services
description: Uma lista de regiões e pontos de extremidade disponíveis para o serviço de fala, incluindo conversão de fala em texto, texto em fala e tradução de fala.
services: cognitive-services
author: mahilleb-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: panosper
ms.custom: seodec18,references_regions
ms.openlocfilehash: 646d29e72b91cd6afcde8e70ad8fd8715442b88e
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98786784"
---
# <a name="speech-service-supported-regions"></a>Regiões com suporte do serviço de fala

O Serviço de Fala permite que seu aplicativo converta áudio em texto, realize a tradução de fala e converta texto em Fala. O serviço está disponível em várias regiões com pontos de extremidade exclusivos para o SDK de Fala e APIs de REST.

O portal de fala para executar configurações personalizadas para sua experiência de fala para todas as regiões está disponível aqui: https://speech.microsoft.com

Tenha em mente os seguintes pontos ao considerar as regiões:

* Se seu aplicativo usar um [SDK de fala](speech-sdk.md), você fornecerá o identificador de região, como `westus` , ao criar uma configuração de fala.
* Se o aplicativo usar uma das [APIs REST](./overview.md#reference-docs) do serviço de Fala, a região fará parte do URI do ponto de extremidade usado ao fazer solicitações.
* As chaves criadas para uma região são válidas somente nessa região. A tentativa de usá-las com outras regiões resultará em erros de autenticação.

## <a name="speech-sdk"></a>SDK de fala

No [SDK de Fala](speech-sdk.md), regiões são especificadas como uma cadeia de caracteres (por exemplo, como um parâmetro para `SpeechConfig.FromSubscription` no SDK de Fala para C#).

### <a name="speech-to-text-text-to-speech-and-translation"></a>Conversão de fala em texto, texto em fala e tradução

O portal de personalização de fala está disponível aqui: https://speech.microsoft.com

O serviço de fala está disponível nessas regiões para **reconhecimento de fala**, conversão de **texto em fala** e **tradução**:

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

Se você usar o [SDK de fala](speech-sdk.md), as regiões serão especificadas pelo **identificador de região** (por exemplo, como um parâmetro para `SpeechConfig.FromSubscription` ). Verifique se a região corresponde à região da sua assinatura.

Se você planeja treinar um modelo personalizado com dados de áudio, use uma das [regiões com hardware dedicado](custom-speech-overview.md#set-up-your-azure-account) para treinamento mais rápido. Você pode usar a [API REST](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/CopyModelToSubscription) para copiar o modelo totalmente treinado para outra região posteriormente.

### <a name="intent-recognition"></a>Reconhecimento de intenção

As regiões disponíveis para **reconhecimento de intenção** por meio do SDK de Fala são os seguintes:

| Região global | Região           | Identificador de região |
| ------------- | ---------------- | -------------------- |
| Ásia          | Leste da Ásia        | `eastasia`           |
| Ásia          | Sudeste Asiático   | `southeastasia`      |
| Austrália     | Leste da Austrália   | `australiaeast`      |
| Europa        | Norte da Europa     | `northeurope`        |
| Europa        | Europa Ocidental      | `westeurope`         |
| América do Norte | Leste dos EUA          | `eastus`             |
| América do Norte | Leste dos EUA 2        | `eastus2`            |
| América do Norte | Centro-Sul dos Estados Unidos | `southcentralus`     |
| América do Norte | Centro-Oeste dos EUA  | `westcentralus`      |
| América do Norte | Oeste dos EUA          | `westus`             |
| América do Norte | Oeste dos EUA 2        | `westus2`            |
| América do Sul | Sul do Brasil     | `brazilsouth`        |

Este é um subconjunto das regiões de publicação compatíveis com o [LUIS (Serviço Inteligente de Reconhecimento Vocal)](../luis/luis-reference-regions.md).

### <a name="voice-assistants"></a>Assistentes de voz

O [SDK de fala](speech-sdk.md) dá suporte a recursos do **Assistente de voz** por meio de [fala de linha direta](./direct-line-speech.md) nessas regiões:

| Região global | Região           | Identificador de região    |
| ------------- | ---------------- | -------------------- |
| América do Norte | Oeste dos EUA          | `westus`             |
| América do Norte | Oeste dos EUA 2        | `westus2`            |
| América do Norte | Leste dos EUA          | `eastus`             |
| América do Norte | Leste dos EUA 2        | `eastus2`            |
| América do Norte | Centro-Oeste dos EUA  | `westcentralus`      |
| América do Norte | Centro-Sul dos Estados Unidos | `southcentralus`     |
| Europa        | Europa Ocidental      | `westeurope`         |
| Europa        | Norte da Europa     | `northeurope`        |
| Ásia          | Leste da Ásia        | `eastasia`           |
| Ásia          | Sudeste Asiático   | `southeastasia`      |
| Índia         | Índia Central    | `centralindia`       |

### <a name="speaker-recognition"></a>Reconhecimento do Locutor

Atualmente, o Reconhecimento do Locutor está disponível apenas na `westus` região.

## <a name="rest-apis"></a>APIs REST

O serviço de fala também expõe pontos de extremidade REST para solicitações de Fala em texto e texto em Fala.

### <a name="speech-to-text"></a>Conversão de fala em texto

Para obter a documentação de referência de fala para texto, consulte [API REST de fala em texto](rest-speech-to-text.md).

O ponto de extremidade para a API REST tem este formato:

```
https://<REGION_IDENTIFIER>.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1
```

Substituir `<REGION_IDENTIFIER>` pelo identificador correspondente à região da sua assinatura desta tabela:

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

> [!NOTE]
> O parâmetro de linguagem deve ser anexado à URL para evitar o recebimento de um erro HTTP 4xx. Por exemplo, o idioma definido para inglês dos EUA usando o ponto de extremidade West US é: `https://westus.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US`.

### <a name="text-to-speech"></a>Conversão de texto em fala

Para obter a documentação de referência de conversão de texto em fala, consulte [API REST de conversão de texto em fala](rest-text-to-speech.md).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]