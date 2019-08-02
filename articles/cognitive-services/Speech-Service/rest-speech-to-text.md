---
title: REST (referência de API de fala em texto) – serviço de fala
titleSuffix: Azure Cognitive Services
description: Saiba como usar a API REST de fala em texto. Neste artigo, você aprenderá sobre opções de autorização, opções de consulta, como estruturar uma solicitação e receber uma resposta.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 6324c00d9b85a13ef6e69185e3b380b20f761f3b
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68552976"
---
# <a name="speech-to-text-rest-api"></a>API REST de conversão de fala em texto

Como alternativa ao SDK de [fala](speech-sdk.md), os serviços de fala permitem que você converta a fala em texto usando uma API REST. Cada terminal acessível está associado a uma região. Seu aplicativo requer uma chave de assinatura para o endpoint que você planeja usar.

Antes de usar a API REST de fala em texto, entenda:
* As solicitações que usam a API REST podem conter apenas 10 segundos de áudio gravado.
* A API REST de fala para texto só retorna os resultados finais. Resultados parciais não são fornecidos.

Se o envio de áudio mais longo é um requisito para o seu aplicativo, considere usar o [SDK de Fala](speech-sdk.md) ou [transcrição do lote](batch-transcription.md).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

## <a name="regions-and-endpoints"></a>Regiões e endpoints

Essas regiões são suportadas para transcrição de fala para texto usando a API REST. Certifique-se de selecionar o terminal que corresponde à sua região de assinatura.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

## <a name="query-parameters"></a>Parâmetros de consulta

Esses parâmetros podem ser incluídos na string de consulta da solicitação REST.

| Parâmetro | Descrição | Obrigatório/Opcional |
|-----------|-------------|---------------------|
| `language` | Identifica a linguagem falada que está sendo reconhecida. Confira os [Idiomas compatíveis](language-support.md#speech-to-text). | Necessário |
| `format` | Especifica o formato do resultado. Os valores aceitos são `simple` e `detailed`. Resultados simples incluem `RecognitionStatus`, `DisplayText`, `Offset` e `Duration`. As respostas detalhadas incluem vários resultados com valores de confiança e quatro representações diferentes. A configuração padrão é `simple`. | Opcional |
| `profanity` | Especifica como lidar com palavrões em resultados de reconhecimento. Os valores aceitos são `masked`, o que substitui a profanidade por asteriscos, `removed`, que remove todas as profanações do resultado, ou `raw`, o que inclui a profanidade no resultado. A configuração padrão é `masked`. | Opcional |

## <a name="request-headers"></a>Cabeçalhos de solicitação

Esta tabela lista cabeçalhos obrigatórios e opcionais para solicitações de fala para texto.

|Cabeçalho| Descrição | Obrigatório/Opcional |
|------|-------------|---------------------|
| `Ocp-Apim-Subscription-Key` | Sua chave de assinatura dos serviços de fala. | Esse cabeçalho ou `Authorization` é obrigatório. |
| `Authorization` | Um token de autorização precedido pela palavra `Bearer`. Para obter mais informações, consulte [Autenticação](#authentication). | Esse cabeçalho ou `Ocp-Apim-Subscription-Key` é obrigatório. |
| `Content-type` | Descreve o formato e o codec dos dados de áudio fornecidos. Os valores aceitos são `audio/wav; codecs=audio/pcm; samplerate=16000` e `audio/ogg; codecs=opus`. | Necessário |
| `Transfer-Encoding` | Especifica que os dados de áudio em partes estão sendo enviados, em vez de um único arquivo. Use este cabeçalho somente se agrupar dados de áudio. | Opcional |
| `Expect` | Se usar transferência em partes, envie `Expect: 100-continue`. Os serviços de fala reconhecem a solicitação inicial e aguardam dados adicionais.| Necessário se enviar dados de áudio em partes. |
| `Accept` | Se fornecido, deve ser `application/json`. Os serviços de fala fornecem resultados em JSON. Algumas estruturas de solicitação da Web fornecem um valor padrão incompatível se você não especificar uma, portanto, é uma boa prática incluir sempre `Accept`. | Opcional, mas recomendado. |

## <a name="audio-formats"></a>Formatos de áudio

O áudio é enviado no corpo da solicitação HTTP `POST`. Ele deve estar em um dos formatos nesta tabela:

| Formatar | Codec | Bitrate | Taxa de amostragem |
|--------|-------|---------|-------------|
| WAV | PCM | 16-bit | 16 kHz, mono |
| OGG | OPUS | 16-bit | 16 kHz, mono |

>[!NOTE]
>Os formatos acima têm suporte por meio da API REST e do WebSocket nos serviços de fala. O [Speech SDK](speech-sdk.md) atualmente dá suporte a apenas o WAV de formato com o codec PCM.

## <a name="sample-request"></a>Solicitação de exemplo

Esta é uma solicitação HTTP típica. O exemplo abaixo inclui o nome do host e os cabeçalhos necessários. É importante observar que o serviço também espera dados de áudio, o que não está incluído nesta amostra. Como mencionado anteriormente, o chunking é recomendado, no entanto, não é obrigatório.

```HTTP
POST speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codecs=audio/pcm; samplerate=16000
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.stt.speech.microsoft.com
Transfer-Encoding: chunked
Expect: 100-continue
```

## <a name="http-status-codes"></a>Códigos de status HTTP

O código de status HTTP para cada resposta indica sucesso ou erros comuns.

| Código de status HTTP | Descrição | Possível motivo |
|------------------|-------------|-----------------|
| 100 | Continuar | A solicitação inicial foi aceita. Continue enviando o restante dos dados. (Usado com transferência em partes.) |
| 200 | OK | A solicitação foi bem-sucedida. O corpo da resposta é um objeto JSON. |
| 400 | Solicitação incorreta | O código de idioma não foi fornecido ou o idioma não tem suporte. Arquivo de áudio inválido. |
| 401 | Não Autorizado | Chave de assinatura ou token de autorização inválido na região especificada, ou ponto de extremidade inválido. |
| 403 | Proibido | Chave de assinatura ou token de autorização ausente. |

## <a name="chunked-transfer"></a>Transferência em partes

A transferência em partes`Transfer-Encoding: chunked`() pode ajudar a reduzir a latência de reconhecimento porque permite que os serviços de fala comecem a processar o arquivo de áudio enquanto ele está sendo transmitido. A API REST não fornece resultados parciais ou provisórios. Essa opção destina-se somente a melhorar a capacidade de resposta.

Este exemplo de código mostra como enviar áudio em blocos. Apenas o primeiro bloco deve conter o cabeçalho do arquivo de áudio. `request` é um objeto HTTPWebRequest conectado ao ponto de extremidade REST apropriado. `audioFile` é o caminho para um arquivo de áudio em disco.

```csharp

    HttpWebRequest request = null;
    request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
    request.SendChunked = true;
    request.Accept = @"application/json;text/xml";
    request.Method = "POST";
    request.ProtocolVersion = HttpVersion.Version11;
    request.Host = host;
    request.ContentType = @"audio/wav; codecs=audio/pcm; samplerate=16000";
    request.Headers["Ocp-Apim-Subscription-Key"] = args[1];
    request.AllowWriteStreamBuffering = false;

using (fs = new FileStream(audioFile, FileMode.Open, FileAccess.Read))
{
    /*
    * Open a request stream and write 1024 byte chunks in the stream one at a time.
    */
    byte[] buffer = null;
    int bytesRead = 0;
    using (Stream requestStream = request.GetRequestStream())
    {
        /*
        * Read 1024 raw bytes from the input audio file.
        */
        buffer = new Byte[checked((uint)Math.Min(1024, (int)fs.Length))];
        while ((bytesRead = fs.Read(buffer, 0, buffer.Length)) != 0)
        {
            requestStream.Write(buffer, 0, bytesRead);
        }

        // Flush
        requestStream.Flush();
    }
}
```

## <a name="response-parameters"></a>Parâmetros de resposta

Os resultados são fornecidos como JSON. O `simple` formato inclui esses campos de nível superior.

| Parâmetro | Descrição  |
|-----------|--------------|
|`RecognitionStatus`|Status, como `Success` para reconhecimento bem-sucedido. Consulte a próxima tabela.|
|`DisplayText`|O texto reconhecido após o uso de maiúsculas, a pontuação, a normalização de texto inverso (conversão de texto falado em formas mais curtas, como 200 para “duzentos” ou “Dr. Rodrigues” para “doutor Rodrigues”) e o mascaramento de conteúdo ofensivo. Apresentar somente em caso de êxito.|
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

O `detailed` formato inclui os mesmos dados que o `simple` formato, juntamente com `NBest`, uma lista de interpretações alternativas do mesmo resultado de reconhecimento. Esses resultados são classificados da maior probabilidade de menos provável. A primeira entrada é o mesmo que o resultado do reconhecimento principal.  Ao usar o formato `detailed`, `DisplayText` é fornecido como `Display` para cada resultado na lista `NBest`.

Cada objeto no `NBest` lista inclui:

| Parâmetro | Descrição |
|-----------|-------------|
| `Confidence` | A pontuação de confiança da entrada de 0,0 (nenhuma confiança) a 1,0 (confiança total) |
| `Lexical` | O formato lexical do texto reconhecido: as palavras reais reconhecidas. |
| `ITN` | O formato de texto inverso normalizado (“canônico”) do texto reconhecido, com números de telefone, números, abreviações (“doutor Rodrigues” para “dr Rodrigues”) e demais transformações aplicadas. |
| `MaskedITN` | O formato ITN com mascaramento de conteúdo ofensivo aplicado se solicitado. |
| `Display` | O formato de exibição do texto reconhecido, com a pontuação e uso de maiúsculas adicionados. Este parâmetro é o mesmo que `DisplayText` fornecido quando o formato é definido como `simple`. |

## <a name="sample-responses"></a>Respostas de exemplo

Essa é uma resposta típica para o reconhecimento`simple`.

```json
{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": "1236645672289",
  "Duration": "1236645672289"
}
```

Essa é uma resposta típica para o reconhecimento`detailed`.

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
      },
      {
        "Confidence" : "0.54",
        "Lexical" : "rewind me to buy five pencils",
        "ITN" : "rewind me to buy 5 pencils",
        "MaskedITN" : "rewind me to buy 5 pencils",
        "Display" : "Rewind me to buy 5 pencils.",
      }
  ]
}
```

## <a name="next-steps"></a>Próximas etapas

- [Obtenha sua assinatura de avaliação de Fala](https://azure.microsoft.com/try/cognitive-services/)
- [Personalizar modelos acústicos](how-to-customize-acoustic-models.md)
- [Personalizar modelos de linguagem](how-to-customize-language-model.md)
