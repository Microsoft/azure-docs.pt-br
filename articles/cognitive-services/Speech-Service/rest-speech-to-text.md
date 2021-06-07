---
title: REST (referência de API de fala em texto) – serviço de fala
titleSuffix: Azure Cognitive Services
description: Saiba como usar a API REST de fala em texto. Neste artigo, você aprenderá sobre opções de autorização, opções de consulta, como estruturar uma solicitação e receber uma resposta.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/08/2021
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: 76b4a749bff94cbf218d3cd3c7d132eef119b7ef
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104606898"
---
# <a name="speech-to-text-rest-api"></a>API REST de conversão de fala em texto

A conversão de fala em texto tem duas APIs REST diferentes. Cada API atende a sua finalidade especial e usa conjuntos diferentes de pontos de extremidade.

As APIs REST de conversão de fala em texto são:
- [A API REST de fala em texto v 3.0](#speech-to-text-rest-api-v30) é usada para a transcrição e a [fala personalizada](custom-speech-overview.md)do [lote](batch-transcription.md) . o v 3.0 é um [sucessor da v 2.0](./migrate-v2-to-v3.md).
- A [API REST de conversão de fala em texto para áudio curto](#speech-to-text-rest-api-for-short-audio) é usada para a transcrição online como uma alternativa ao [SDK de fala](speech-sdk.md). As solicitações que usam essa API podem transmitir apenas até 60 segundos de áudio por solicitação. 

## <a name="speech-to-text-rest-api-v30"></a>API REST de conversão de fala em texto v 3.0

A API REST de fala em texto v 3.0 é usada para a transcrição e a [fala personalizada](custom-speech-overview.md)do [lote](batch-transcription.md) . Se você precisar se comunicar com a transcrição online via REST, use a [API REST de fala em texto para áudio curto](#speech-to-text-rest-api-for-short-audio).

Use a API REST v 3.0 para:
- Copie modelos para outras assinaturas caso você queira que os colegas tenham acesso a um modelo que você criou ou nos casos em que você deseja implantar um modelo em mais de uma região
- Transcrever dados de um contêiner (transcrição em massa) e fornecer várias URLs de arquivo de áudio
- Carregar dados de contas de armazenamento do Azure por meio do uso de um URI de SAS
- Obter logs por ponto de extremidade se os logs tiverem sido solicitados para esse ponto de extremidade
- Solicite o manifesto dos modelos que você criar, para a finalidade de configurar contêineres locais

A API REST v 3.0 inclui recursos como:
- **Notificações-WebHooks**– todos os processos em execução do serviço agora dão suporte a notificações de webhook. A API REST v 3.0 fornece as chamadas para permitir que você registre seus WebHooks onde as notificações são enviadas
- **Atualizando modelos por trás de pontos de extremidade** 
- **Adaptação de modelo com vários conjuntos de dados**— adapte um modelo usando várias combinações de conjunto de dados de dados acústicos, de linguagem e de pronúncia
- **Traga seu próprio armazenamento**– use suas próprias contas de armazenamento para logs, arquivos de transcrição e outros dados

Veja exemplos sobre como usar a API REST v 3.0 com a transcrição do lote [neste artigo](batch-transcription.md).

Se você estiver usando a API REST de fala em texto v 2.0, veja como migrar para o v 3.0 neste [guia](./migrate-v2-to-v3.md).

Consulte a referência completa da API REST de fala em texto v 3.0 [aqui](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0).

## <a name="speech-to-text-rest-api-for-short-audio"></a>API REST de conversão de fala em texto para áudio curto

Como alternativa ao SDK de [fala](speech-sdk.md), o serviço de fala permite que você converta a fala em texto usando uma API REST.
A API REST para áudio curto é muito limitada e só deve ser usada nos casos em que o [SDK de fala](speech-sdk.md) não possa.

Antes de usar a API REST de fala em texto para áudio curto, considere o seguinte:

* As solicitações que usam a API REST para áudio curto e transmissão de áudio diretamente podem conter até 60 segundos de áudio.
* A API REST de conversão de fala em texto para áudio curto retorna apenas os resultados finais. Resultados parciais não são fornecidos.

Se o envio de áudio mais longo for um requisito para seu aplicativo, considere o uso do [SDK de fala](speech-sdk.md) ou da [API REST de fala em texto v 3.0](#speech-to-text-rest-api-v30).

> [!TIP]
> Consulte [Este artigo](sovereign-clouds.md) para os pontos de extremidade do Azure governamental e do Azure China.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

### <a name="regions-and-endpoints"></a>Regiões e endpoints

O ponto de extremidade para a API REST para áudio curto tem este formato:

```
https://<REGION_IDENTIFIER>.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1
```

Substituir `<REGION_IDENTIFIER>` pelo identificador correspondente à região da sua assinatura desta tabela:

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

> [!NOTE]
> O parâmetro de linguagem deve ser anexado à URL para evitar o recebimento de um erro HTTP 4xx. Por exemplo, o idioma definido para inglês dos EUA usando o ponto de extremidade West US é: `https://westus.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US`.

### <a name="query-parameters"></a>Parâmetros de consulta

Esses parâmetros podem ser incluídos na string de consulta da solicitação REST.

| Parâmetro | Descrição | Obrigatório/Opcional |
|-----------|-------------|---------------------|
| `language` | Identifica a linguagem falada que está sendo reconhecida. Consulte [idiomas com suporte](language-support.md#speech-to-text). | Obrigatório |
| `format` | Especifica o formato do resultado. Os valores aceitos são `simple` e `detailed`. Resultados simples incluem `RecognitionStatus`, `DisplayText`, `Offset` e `Duration`. As respostas detalhadas incluem quatro representações diferentes de texto de exibição. A configuração padrão é `simple`. | Opcional |
| `profanity` | Especifica como lidar com palavrões em resultados de reconhecimento. Os valores aceitos são `masked` , que substitui profanação por asteriscos, `removed` , que remove toda a profanação do resultado, ou `raw` , que inclui a profanação no resultado. A configuração padrão é `masked`. | Opcional |
| `cid` | Ao usar o [portal de fala personalizada](./custom-speech-overview.md) para criar modelos personalizados, você pode usar modelos personalizados por meio de sua **ID de ponto de extremidade** encontrada na página **implantação** . Use a **ID do ponto de extremidade** como o argumento para o `cid` parâmetro de cadeia de caracteres de consulta. | Opcional |

### <a name="request-headers"></a>Cabeçalhos da solicitação

Esta tabela lista os cabeçalhos obrigatórios e opcionais para solicitações de conversão de fala em texto.

|parâmetro| Descrição | Obrigatório/Opcional |
|------|-------------|---------------------|
| `Ocp-Apim-Subscription-Key` | Sua chave de assinatura do serviço de Fala. | Esse cabeçalho ou `Authorization` é obrigatório. |
| `Authorization` | Um token de autorização precedido pela palavra `Bearer`. Para obter mais informações, consulte [Autenticação](#authentication). | Esse cabeçalho ou `Ocp-Apim-Subscription-Key` é obrigatório. |
| `Pronunciation-Assessment` | Especifica os parâmetros para mostrar as pontuações de pronúncia nos resultados de reconhecimento, que avaliam a qualidade de pronúncia da entrada de fala, com indicadores de precisão, fluência, integridade, etc. Esse parâmetro é um JSON codificado em base64 contendo vários parâmetros detalhados. Consulte [parâmetros de avaliação de pronúncia](#pronunciation-assessment-parameters) para saber como criar esse cabeçalho. | Opcional |
| `Content-type` | Descreve o formato e o codec dos dados de áudio fornecidos. Os valores aceitos são `audio/wav; codecs=audio/pcm; samplerate=16000` e `audio/ogg; codecs=opus`. | Obrigatório |
| `Transfer-Encoding` | Especifica que os dados de áudio em partes estão sendo enviados, em vez de um único arquivo. Use este cabeçalho somente se agrupar dados de áudio. | Opcional |
| `Expect` | Se usar transferência em partes, envie `Expect: 100-continue`. O serviço de Fala reconhece a solicitação inicial e aguarda os dados adicionais.| Necessário se enviar dados de áudio em partes. |
| `Accept` | Se fornecido, deve ser `application/json`. O serviço de fala fornece resultados em JSON. Algumas estruturas de solicitação fornecem um valor padrão incompatível. É uma boa prática sempre incluir `Accept` . | Opcional, mas recomendado. |

### <a name="audio-formats"></a>Formatos de áudio

O áudio é enviado no corpo da solicitação HTTP `POST`. Ele deve estar em um dos formatos nesta tabela:

| Formatar | Codec | Taxa de bits | Taxa de amostragem  |
|--------|-------|----------|--------------|
| WAV    | PCM   | 256 kbps | 16 kHz, mono |
| OGG    | OPUS  | 256 kpbs | 16 kHz, mono |

>[!NOTE]
>Os formatos acima têm suporte por meio da API REST para áudio curto e WebSocket no serviço de fala. O [SDK de fala](speech-sdk.md) atualmente dá suporte ao formato WAV com o codec PCM, bem como [outros formatos](how-to-use-codec-compressed-audio-input-streams.md).

### <a name="pronunciation-assessment-parameters"></a>Parâmetros de avaliação de pronúncia

Esta tabela lista os parâmetros obrigatórios e opcionais para avaliação de pronúncia.

| Parâmetro | Descrição | Necessário? |
|-----------|-------------|---------------------|
| ReferenceText | O texto em relação ao qual a pronúncia será avaliada. | Obrigatório |
| GradingSystem | O sistema de ponto para a calibragem de pontuação. O `FivePoint` sistema fornece uma pontuação de ponto flutuante de 0-5 e `HundredMark` fornece uma pontuação de ponto flutuante de 0-100. Padrão: `FivePoint`. | Opcional |
| Granularidade | A granularidade da avaliação. Os valores aceitos são `Phoneme` , que mostra a pontuação no nível de texto completo, Word e fonema, `Word` , que mostra a pontuação no texto completo e no nível de palavra, `FullText` , que mostra a pontuação somente no nível de texto completo. A configuração padrão é `Phoneme`. | Opcional |
| Dimensão | Define os critérios de saída. Os valores aceitos são `Basic` , que mostram apenas a pontuação de precisão, `Comprehensive` mostra pontuações em mais dimensões (por exemplo, Pontuação fluência e pontuação de integridade no nível de texto completo, tipo de erro no nível de palavra). Verifique os [parâmetros de resposta](#response-parameters) para ver as definições de diferentes dimensões de Pontuação e tipos de erro do Word. A configuração padrão é `Basic`. | Opcional |
| EnableMiscue | Habilita o cálculo de miscue. Com isso habilitado, as palavras pronunciadas serão comparadas ao texto de referência e serão marcadas com omissão/inserção com base na comparação. Os valores aceitos são `False` e `True`. A configuração padrão é `False`. | Opcional |
| Scenarioid | Um GUID que indica um sistema de ponto personalizado. | Opcional |

Veja abaixo um exemplo de JSON que contém os parâmetros de avaliação de pronúncia:

```json
{
  "ReferenceText": "Good morning.",
  "GradingSystem": "HundredMark",
  "Granularity": "FullText",
  "Dimension": "Comprehensive"
}
```

O código de exemplo a seguir mostra como criar os parâmetros de avaliação de pronúncia no `Pronunciation-Assessment` cabeçalho:

```csharp
var pronAssessmentParamsJson = $"{{\"ReferenceText\":\"Good morning.\",\"GradingSystem\":\"HundredMark\",\"Granularity\":\"FullText\",\"Dimension\":\"Comprehensive\"}}";
var pronAssessmentParamsBytes = Encoding.UTF8.GetBytes(pronAssessmentParamsJson);
var pronAssessmentHeader = Convert.ToBase64String(pronAssessmentParamsBytes);
```

É altamente recomendável o carregamento de streaming (em partes) durante o lançamento dos dados de áudio, o que pode reduzir significativamente a latência. Consulte o [código de exemplo em linguagens de programação diferentes](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/PronunciationAssessment) para saber como habilitar o streaming.

>[!NOTE]
>O recurso de avaliação de pronúncia está disponível apenas no momento no `en-US` idioma.

### <a name="sample-request"></a>Solicitação de exemplo

O exemplo abaixo inclui o nome do host e os cabeçalhos necessários. É importante observar que o serviço também espera dados de áudio, o que não está incluído nesta amostra. Como mencionado anteriormente, o chunking é recomendado, no entanto, não é obrigatório.

```HTTP
POST speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codecs=audio/pcm; samplerate=16000
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.stt.speech.microsoft.com
Transfer-Encoding: chunked
Expect: 100-continue
```

Para habilitar a avaliação de pronúncia, você pode adicionar o cabeçalho abaixo. Consulte [parâmetros de avaliação de pronúncia](#pronunciation-assessment-parameters) para saber como criar esse cabeçalho.

```HTTP
Pronunciation-Assessment: eyJSZWZlcm...
```

### <a name="http-status-codes"></a>Códigos de status HTTP

O código de status HTTP para cada resposta indica sucesso ou erros comuns.

| Código de status HTTP | Descrição | Possível motivo |
|------------------|-------------|-----------------|
| `100` | Continuar | A solicitação inicial foi aceita. Continue enviando o restante dos dados. (Usado com transferência em partes) |
| `200` | OK | A solicitação foi bem-sucedida. O corpo da resposta é um objeto JSON. |
| `400` | Solicitação incorreta | Código de idioma não fornecido, não um idioma com suporte, arquivo de áudio inválido, etc. |
| `401` | Não Autorizado | Chave de assinatura ou token de autorização inválido na região especificada, ou ponto de extremidade inválido. |
| `403` | Proibido | Chave de assinatura ou token de autorização ausente. |

### <a name="chunked-transfer"></a>Transferência em partes

A transferência em partes ( `Transfer-Encoding: chunked` ) pode ajudar a reduzir a latência de reconhecimento. Ele permite que o serviço de fala comece a processar o arquivo de áudio enquanto ele é transmitido. A API REST para áudio curto não fornece resultados parciais ou provisórios.

Este exemplo de código mostra como enviar áudio em blocos. Apenas o primeiro bloco deve conter o cabeçalho do arquivo de áudio. `request` é um `HttpWebRequest` objeto conectado ao ponto de extremidade REST apropriado. `audioFile` é o caminho para um arquivo de áudio em disco.

```csharp
var request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
request.SendChunked = true;
request.Accept = @"application/json;text/xml";
request.Method = "POST";
request.ProtocolVersion = HttpVersion.Version11;
request.Host = host;
request.ContentType = @"audio/wav; codecs=audio/pcm; samplerate=16000";
request.Headers["Ocp-Apim-Subscription-Key"] = "YOUR_SUBSCRIPTION_KEY";
request.AllowWriteStreamBuffering = false;

using (var fs = new FileStream(audioFile, FileMode.Open, FileAccess.Read))
{
    // Open a request stream and write 1024 byte chunks in the stream one at a time.
    byte[] buffer = null;
    int bytesRead = 0;
    using (var requestStream = request.GetRequestStream())
    {
        // Read 1024 raw bytes from the input audio file.
        buffer = new Byte[checked((uint)Math.Min(1024, (int)fs.Length))];
        while ((bytesRead = fs.Read(buffer, 0, buffer.Length)) != 0)
        {
            requestStream.Write(buffer, 0, bytesRead);
        }

        requestStream.Flush();
    }
}
```

### <a name="response-parameters"></a>Parâmetros de resposta

Os resultados são fornecidos como JSON. O `simple` formato inclui esses campos de nível superior.

| Parâmetro | Descrição  |
|-----------|--------------|
|`RecognitionStatus`|Status, como `Success` para reconhecimento bem-sucedido. Consulte a próxima tabela.|
|`DisplayText`|O texto reconhecido após a capitalização, a pontuação, a normalização inversa de texto (conversão de texto falado em formas mais curtas, como 200 para "200" ou "Dr. Smith" para "médico Smith") e mascaramento de profanação. Apresentar somente em caso de êxito.|
|`Offset`|O tempo (em unidades de 100 nanossegundos) no qual a fala reconhecida começa no fluxo de áudio.|
|`Duration`|A duração (em unidades de 100 nanossegundos) da fala reconhecida no fluxo de áudio.|

O `RecognitionStatus` campo pode conter estes valores:

| Status | Descrição |
|--------|-------------|
| `Success` | O reconhecimento foi bem-sucedido e o campo `DisplayText` está presente. |
| `NoMatch` | A fala foi detectada no fluxo de áudio, mas nenhuma palavra do idioma de destino foi combinada. Normalmente, isso significa que o idioma do reconhecimento é um idioma diferente daquele que o usuário está falando. |
| `InitialSilenceTimeout` | O início do fluxo de áudio continha apenas silêncio e o serviço atingiu o tempo limite aguardando pela fala. |
| `BabbleTimeout` | O início do fluxo de áudio continha apenas ruído e o serviço atingiu o tempo limite aguardando pela fala. |
| `Error` | O serviço de reconhecimento encontrou um erro interno e não foi possível continuar. Tente novamente, se possível. |

> [!NOTE]
> Se o áudio consistir apenas em conteúdo ofensivo e o parâmetro de consulta `profanity` estiver definido como `remove`, o serviço não retornará um resultado de fala.

O `detailed` formato inclui formulários adicionais de resultados reconhecidos.
Ao usar o formato `detailed`, `DisplayText` é fornecido como `Display` para cada resultado na lista `NBest`.

O objeto na `NBest` lista pode incluir:

| Parâmetro | Descrição |
|-----------|-------------|
| `Confidence` | A pontuação de confiança da entrada de 0,0 (nenhuma confiança) a 1,0 (confiança total) |
| `Lexical` | O formato lexical do texto reconhecido: as palavras reais reconhecidas. |
| `ITN` | O formato de texto inverso normalizado (“canônico”) do texto reconhecido, com números de telefone, números, abreviações (“doutor Rodrigues” para “dr Rodrigues”) e demais transformações aplicadas. |
| `MaskedITN` | O formato ITN com mascaramento de conteúdo ofensivo aplicado se solicitado. |
| `Display` | O formato de exibição do texto reconhecido, com a pontuação e uso de maiúsculas adicionados. Este parâmetro é o mesmo que `DisplayText` fornecido quando o formato é definido como `simple`. |
| `AccuracyScore` | Precisão da pronúncia da fala. A precisão indica o quão próximo os fonemas correspondem à pronúncia de um orador nativo. A pontuação de precisão de nível de texto completo e de palavra é agregada da Pontuação de precisão de nível de fonema. |
| `FluencyScore` | Fluência da fala determinada. Fluência indica a precisão com que a fala corresponde ao uso silencioso de quebras silenciosas entre palavras do orador nativo. |
| `CompletenessScore` | Integridade da fala, determinada pelo cálculo da proporção de palavras pronunciadas para fazer referência à entrada de texto. |
| `PronScore` | Pontuação geral que indica a qualidade da pronúncia da fala determinada. Isso é agregado de `AccuracyScore` `FluencyScore` e `CompletenessScore` com peso. |
| `ErrorType` | Esse valor indica se uma palavra é omitida, inserida ou pronunciada incorretamente, em comparação com `ReferenceText` . Os valores possíveis são `None` (ou seja, nenhum erro nesta palavra), `Omission` `Insertion` e `Mispronunciation` . |

### <a name="sample-responses"></a>Respostas de exemplo

Uma resposta típica para `simple` reconhecimento:

```json
{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": "1236645672289",
  "Duration": "1236645672289"
}
```

Uma resposta típica para `detailed` reconhecimento:

```json
{
  "RecognitionStatus": "Success",
  "Offset": "1236645672289",
  "Duration": "1236645672289",
  "NBest": [
      {
        "Confidence" : "0.87",
        "Lexical" : "remind me to buy five pencils",
        "ITN" : "remind me to buy 5 pencils",
        "MaskedITN" : "remind me to buy 5 pencils",
        "Display" : "Remind me to buy 5 pencils.",
      }
  ]
}
```

Uma resposta típica para reconhecimento com avaliação de pronúncia:

```json
{
  "RecognitionStatus": "Success",
  "Offset": "400000",
  "Duration": "11000000",
  "NBest": [
      {
        "Confidence" : "0.87",
        "Lexical" : "good morning",
        "ITN" : "good morning",
        "MaskedITN" : "good morning",
        "Display" : "Good morning.",
        "PronScore" : 84.4,
        "AccuracyScore" : 100.0,
        "FluencyScore" : 74.0,
        "CompletenessScore" : 100.0,
        "Words": [
            {
              "Word" : "Good",
              "AccuracyScore" : 100.0,
              "ErrorType" : "None",
              "Offset" : 500000,
              "Duration" : 2700000
            },
            {
              "Word" : "morning",
              "AccuracyScore" : 100.0,
              "ErrorType" : "None",
              "Offset" : 5300000,
              "Duration" : 900000
            }
        ]
      }
  ]
}
```

## <a name="next-steps"></a>Próximas etapas

- [Criar uma conta gratuita do Azure](https://azure.microsoft.com/free/cognitive-services/)
- [Personalizar modelos acústicos](./how-to-custom-speech-train-model.md)
- [Personalizar modelos de linguagem](./how-to-custom-speech-train-model.md)
- [Familiarize-se com a transcrição do lote](batch-transcription.md)

