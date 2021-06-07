---
title: REST (referência de API de conversão de texto em fala) – serviço de fala
titleSuffix: Azure Cognitive Services
description: Saiba como usar a API REST de conversão de texto em fala. Neste artigo, você aprenderá sobre opções de autorização, opções de consulta, como estruturar uma solicitação e receber uma resposta.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/08/2021
ms.author: trbye
ms.custom: references_regions
ms.openlocfilehash: 9da83746cdaf693922b88841cd9c0fac432611c9
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104870833"
---
# <a name="text-to-speech-rest-api"></a>API REST conversão de texto em fala

O serviço de fala permite que você [converta o texto em fala sintetizada](#convert-text-to-speech) e [obtenha uma lista de vozes com suporte](#get-a-list-of-voices) para uma região usando um conjunto de APIs REST. Cada ponto de extremidade disponível é associado a uma região. É necessária uma chave de assinatura para o ponto de extremidade/região que você planeja usar.

A API REST de conversão de texto em fala é compatível com vozes neurais e padrão de conversão de texto em fala, cada uma delas compatível com um idioma e dialeto específicos, identificados pela localidade.

* Para obter uma lista completa de vozes, consulte [suporte para idioma](language-support.md#text-to-speech).
* Para obter informações sobre a disponibilidade regional, consulte [regiões](regions.md#text-to-speech).

> [!IMPORTANT]
> Os custos variam para vozes padrão, personalizadas e neurais. Para saber mais, consulte [Preços](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

Antes de usar essa API, entenda:

* A API REST de conversão de texto em voz requer um cabeçalho de autorização. Isso significa que você precisa concluir uma troca de tokens para acessar o serviço. Para obter mais informações, consulte [Autenticação](#authentication).

> [!TIP]
> Consulte [Este artigo](sovereign-clouds.md) para os pontos de extremidade do Azure governamental e do Azure China.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

## <a name="get-a-list-of-voices"></a>Obter uma lista de vozes

O `voices/list` ponto de extremidade permite obter uma lista completa de vozes para uma região/um ponto de extremidade específico.

### <a name="regions-and-endpoints"></a>Regiões e endpoints

| Região | Ponto de extremidade |
|--------|----------|
| Leste da Austrália | `https://australiaeast.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Sul do Brasil | `https://brazilsouth.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Canadá Central | `https://canadacentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Centro dos EUA | `https://centralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Leste da Ásia | `https://eastasia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Leste dos EUA | `https://eastus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Leste dos EUA 2 | `https://eastus2.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| França Central | `https://francecentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Centro da Índia | `https://centralindia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Japan East | `https://japaneast.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Coreia Central | `https://koreacentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Centro-Norte dos EUA | `https://northcentralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Norte da Europa | `https://northeurope.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Norte da África do Sul | `https://southafricanorth.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Centro-Sul dos Estados Unidos | `https://southcentralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Sudeste Asiático | `https://southeastasia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Sul do Reino Unido | `https://uksouth.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Centro-Oeste dos EUA | `https://westcentralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Europa Ocidental | `https://westeurope.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Oeste dos EUA | `https://westus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Oeste dos EUA 2 | `https://westus2.tts.speech.microsoft.com/cognitiveservices/voices/list` |

> [!TIP]
> As [vozes na versão prévia](language-support.md#neural-voices-in-preview) estão disponíveis apenas nestas três regiões: leste dos eua, Europa Ocidental e sudeste asiático.

### <a name="request-headers"></a>Cabeçalhos da solicitação

Esta tabela lista os cabeçalhos obrigatórios e opcionais para solicitações de conversão de texto em fala.

| parâmetro | Descrição | Obrigatório/Opcional |
|--------|-------------|---------------------|
| `Ocp-Apim-Subscription-Key` | Sua chave de assinatura do serviço de Fala. | Esse cabeçalho ou `Authorization` é obrigatório. |
| `Authorization` | Um token de autorização precedido pela palavra `Bearer`. Para obter mais informações, consulte [Autenticação](#authentication). | Esse cabeçalho ou `Ocp-Apim-Subscription-Key` é obrigatório. |



### <a name="request-body"></a>Corpo da solicitação

Um corpo não é necessário para `GET` solicitações a esse ponto de extremidade.

### <a name="sample-request"></a>Solicitação de exemplo

Essa solicitação requer apenas um cabeçalho de autorização.

```http
GET /cognitiveservices/voices/list HTTP/1.1

Host: westus.tts.speech.microsoft.com
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
```

### <a name="sample-response"></a>Resposta de exemplo

Essa resposta foi truncada para ilustrar a estrutura de uma resposta.

> [!NOTE]
> A disponibilidade de voz varia por região/ponto de extremidade.

```json
[

    {
    "Name": "Microsoft Server Speech Text to Speech Voice (en-US, AriaNeural)",
    "DisplayName": "Aria",
    "LocalName": "Aria",
    "ShortName": "en-US-AriaNeural",
    "Gender": "Female",
    "Locale": "en-US",
    "StyleList": [
      "chat",
      "customerservice",
      "newscast-casual",
      "newscast-formal",
      "cheerful",
      "empathetic"
    ],
    "SampleRateHertz": "24000",
    "VoiceType": "Neural",
    "Status": "GA"
  },

  ...

     {
    "Name": "Microsoft Server Speech Text to Speech Voice (ga-IE, OrlaNeural)",
    "DisplayName": "Orla",
    "LocalName": "Orla",
    "ShortName": "ga-IE-OrlaNeural",
    "Gender": "Female",
    "Locale": "ga-IE",
    "SampleRateHertz": "24000",
    "VoiceType": "Neural",
    "Status": "Preview"
  },

  ...

   {
    "Name": "Microsoft Server Speech Text to Speech Voice (zh-CN, YunxiNeural)",
    "DisplayName": "Yunxi",
    "LocalName": "云希",
    "ShortName": "zh-CN-YunxiNeural",
    "Gender": "Male",
    "Locale": "zh-CN",
    "StyleList": [
      "Calm",
      "Fearful",
      "Cheerful",
      "Disgruntled",
      "Serious",
      "Angry",
      "Sad",
      "Depressed",
      "Embarrassed"
    ],
    "SampleRateHertz": "24000",
    "VoiceType": "Neural",
    "Status": "Preview"
  },

    ...

   {
    "Name": "Microsoft Server Speech Text to Speech Voice (ar-EG, Hoda)",
    "DisplayName": "Hoda",
    "LocalName": "هدى",
    "ShortName": "ar-EG-Hoda",
    "Gender": "Female",
    "Locale": "ar-EG",
    "SampleRateHertz": "16000",
    "VoiceType": "Standard",
    "Status": "GA"
  },

...

]
```

### <a name="http-status-codes"></a>Códigos de status HTTP

O código de status HTTP para cada resposta indica sucesso ou erros comuns.

| Código de status HTTP | Descrição | Possível motivo |
|------------------|-------------|-----------------|
| 200 | OK | A solicitação foi bem-sucedida. |
| 400 | Solicitação incorreta | Um parâmetro obrigatório está ausente, vazio ou nulo. Ou então, o valor passado como um parâmetro obrigatório ou opcional é inválido. Um problema comum é um cabeçalho que é muito longo. |
| 401 | Não Autorizado | A solicitação não foi autorizada. Verifique se a chave de assinatura ou o token são válidos e se estão na região correta. |
| 429 | Número Excessivo de Solicitações | Você excedeu a cota ou a taxa de solicitações permitidas para a sua assinatura. |
| 502 | Gateway incorreto    | Problema de rede ou do servidor. Também pode indicar cabeçalhos inválidos. |


## <a name="convert-text-to-speech"></a>Converter texto em fala

O `v1` ponto de extremidade permite converter conversão de texto em fala usando a [linguagem de marcação de síntese de fala (SSML)](speech-synthesis-markup.md).

### <a name="regions-and-endpoints"></a>Regiões e endpoints

Essas regiões são suportadas para text-to-speech usando a API REST. Certifique-se de selecionar o terminal que corresponde à sua região de assinatura.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]

### <a name="request-headers"></a>Cabeçalhos da solicitação

Esta tabela lista os cabeçalhos obrigatórios e opcionais para solicitações de conversão de texto em fala.

| parâmetro | Descrição | Obrigatório/Opcional |
|--------|-------------|---------------------|
| `Authorization` | Um token de autorização precedido pela palavra `Bearer`. Para obter mais informações, consulte [Autenticação](#authentication). | Obrigatório |
| `Content-Type` | Especifica o tipo de conteúdo para o texto fornecido. Aceita o valor: `application/ssml+xml`. | Obrigatório |
| `X-Microsoft-OutputFormat` | Especifica o formato de saída de áudio. Para obter uma lista completa dos valores aceitos, consulte [saídas de áudio](#audio-outputs). | Obrigatório |
| `User-Agent` | O nome do aplicativo. O valor fornecido deve ter menos de 255 caracteres. | Obrigatório |

### <a name="audio-outputs"></a>Saídas de áudio

Esta é uma lista de formatos de áudio suportados que são enviados em cada solicitação como o cabeçalho `X-Microsoft-OutputFormat`. Cada um incorpora um tipo de taxa de bits e codificação. O serviço de fala dá suporte a saídas de áudio de 24 kHz, 16 kHz e 8 kHz.

```output
raw-16khz-16bit-mono-pcm            raw-8khz-8bit-mono-mulaw
riff-8khz-8bit-mono-alaw            riff-8khz-8bit-mono-mulaw
riff-16khz-16bit-mono-pcm           audio-16khz-128kbitrate-mono-mp3
audio-16khz-64kbitrate-mono-mp3     audio-16khz-32kbitrate-mono-mp3
raw-24khz-16bit-mono-pcm            riff-24khz-16bit-mono-pcm
audio-24khz-160kbitrate-mono-mp3    audio-24khz-96kbitrate-mono-mp3
audio-24khz-48kbitrate-mono-mp3     ogg-24khz-16bit-mono-opus
raw-48khz-16bit-mono-pcm            riff-48khz-16bit-mono-pcm
audio-48khz-96kbitrate-mono-mp3     audio-48khz-192kbitrate-mono-mp3
webm-16khz-16bit-mono-opus          webm-24khz-16bit-mono-opus
```

> [!NOTE]
> Se sua voz selecionada e o formato de saída tiverem diferentes taxas de bits, o áudio é aumentado conforme necessário.
> Ogg-24kHz-16 bits-mono-Opus pode ser decodificado com o [codec Opus](https://opus-codec.org/downloads/)

### <a name="request-body"></a>Corpo da solicitação

O corpo de cada solicitação `POST` é enviada como [SSML (Speech Synthesis Markup Language)](speech-synthesis-markup.md). A SSML permite que você escolha o idioma e a voz da fala sintetizada retornada pelo serviço de conversão de texto em fala. Para obter uma lista completa de vozes compatíveis, confira [suporte para idioma](language-support.md#text-to-speech).

> [!NOTE]
> Se usar uma voz personalizada, o corpo de uma solicitação poderá ser enviado como texto sem formatação (ASCII ou UTF-8).

### <a name="sample-request"></a>Solicitação de exemplo

Esta solicitação HTTP usa SSML para especificar a voz e o idioma. Se o comprimento do corpo for longo e o áudio resultante exceder 10 minutos, ele será truncado para 10 minutos. Em outras palavras, o tamanho do áudio não pode exceder 10 minutos.

```http
POST /cognitiveservices/v1 HTTP/1.1

X-Microsoft-OutputFormat: raw-24khz-16bit-mono-pcm
Content-Type: application/ssml+xml
Host: westus.tts.speech.microsoft.com
Content-Length: 225
Authorization: Bearer [Base64 access_token]

<speak version='1.0' xml:lang='en-US'><voice xml:lang='en-US' xml:gender='Female'
    name='en-US-AriaNeural'>
        Microsoft Speech Service Text-to-Speech API
</voice></speak>
```

### <a name="http-status-codes"></a>Códigos de status HTTP

O código de status HTTP para cada resposta indica sucesso ou erros comuns.

| Código de status HTTP | Descrição | Possível motivo |
|------------------|-------------|-----------------|
| 200 | OK | A solicitação foi bem-sucedida. O corpo da resposta é um arquivo de áudio. |
| 400 | Solicitação incorreta | Um parâmetro obrigatório está ausente, vazio ou nulo. Ou então, o valor passado como um parâmetro obrigatório ou opcional é inválido. Um problema comum é um cabeçalho que é muito longo. |
| 401 | Não Autorizado | A solicitação não foi autorizada. Verifique se a chave de assinatura ou o token são válidos e se estão na região correta. |
| 415 | Tipo de Mídia Sem Suporte | É possível que o incorreto `Content-Type` tenha sido fornecido. `Content-Type` deve ser definido como `application/ssml+xml` . |
| 429 | Número Excessivo de Solicitações | Você excedeu a cota ou a taxa de solicitações permitidas para a sua assinatura. |
| 502 | Gateway incorreto    | Problema de rede ou do servidor. Também pode indicar cabeçalhos inválidos. |

Se o status HTTP for `200 OK`, o corpo da resposta conterá um arquivo de áudio no formato solicitado. Este arquivo pode ser reproduzido enquanto é transferido, salvo em um buffer ou salvo em um arquivo.

## <a name="next-steps"></a>Próximas etapas

- [Criar uma conta gratuita do Azure](https://azure.microsoft.com/free/cognitive-services/)
- [Síntese assíncrona para áudio de forma longa](./long-audio-api.md)
- [Introdução à Voz Personalizada](how-to-custom-voice.md)
