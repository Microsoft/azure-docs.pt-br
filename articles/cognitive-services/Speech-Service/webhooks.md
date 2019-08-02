---
title: WebHooks-serviço de fala
titleSuffix: Azure Cognitive Services
description: Os WebHooks são a chamada HTTP que faz backup ideal para otimizar sua solução ao lidar com processos de longa execução, como importações, adaptação, testes de exatidão ou transcrições de arquivos de longa execução.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: panosper
ms.openlocfilehash: 3d07e540bf88c956f61b5d3b2a98702cad616985
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68558795"
---
# <a name="webhooks-for-speech-services"></a>WebHooks para serviços de fala

WebHooks são como retornos de chamada HTTP que permitem que seu aplicativo aceite dados dos serviços de fala quando ele se tornar disponível. Usando WebHooks, você pode otimizar o uso de nossas APIs REST eliminando a necessidade de sondar continuamente uma resposta. Nas próximas seções, você aprenderá a usar WebHooks com os serviços de fala.

## <a name="supported-operations"></a>Operações com suporte

Os serviços de fala dão suporte a WebHooks para todas as operações de longa execução. Cada uma das operações listadas abaixo pode disparar um retorno de chamada HTTP após a conclusão.

* DataImportCompletion
* ModelAdaptationCompletion
* AccuracyTestCompletion
* TranscriptionCompletion
* EndpointDeploymentCompletion
* EndpointDataCollectionCompletion

Em seguida, vamos criar um webhook.

## <a name="create-a-webhook"></a>Criar um webhook

Vamos criar um webhook para uma transcrição offline. O cenário: um usuário tem um arquivo de áudio de longa execução que gostaria de transcrever de forma assíncrona com a API de transcrição do lote.

WebHooks podem ser criados fazendo uma solicitação post para https://\<Region\>. Cris.ai/API/speechtotext/v2.1/Transcriptions/Hooks.

Os parâmetros de configuração para a solicitação são fornecidos como JSON:

```json
{
  "configuration": {
    "url": "https://your.callback.url/goes/here",
    "secret": "<my_secret>"
  },
  "events": [
    "TranscriptionCompletion"
  ],
  "active": true,
  "name": "TranscriptionCompletionWebHook",
  "description": "This is a Webhook created to trigger an HTTP POST request when my audio file transcription is completed.",
  "properties": {
      "Active" : "True"
  }

}
```
Todas as solicitações POST para a API de transcrição do `name`lote exigem um. Os `description` parâmetros `properties` e são opcionais.

A `Active` propriedade é usada para alternar a chamada de volta para sua URL ativada e desativada sem a necessidade de excluir e recriar o registro do webhook. Se você só precisar chamar de volta uma vez após a conclusão do processo, exclua o webhook e alterne `Active` a propriedade para false.

O tipo `TranscriptionCompletion` de evento é fornecido na matriz de eventos. Ele chamará de volta para o ponto de extremidade quando uma transcrição entrar em um`Succeeded` estado `Failed`de terminal (ou). Ao chamar de volta para a URL registrada, a solicitação conterá um `X-MicrosoftSpeechServices-Event` cabeçalho contendo um dos tipos de evento registrados. Há uma solicitação por tipo de evento registrado.

Há um tipo de evento que você não pode assinar. É o tipo `Ping` de evento. Uma solicitação com esse tipo é enviada para a URL quando terminar de criar um webhook ao usar a URL de ping (veja abaixo).  

Na configuração, a `url` propriedade é necessária. Solicitações POST são enviadas para esta URL. O `secret` é usado para criar um hash SHA256 da carga, com o segredo como uma chave HMAC. O hash é definido como o `X-MicrosoftSpeechServices-Signature` cabeçalho ao chamar de volta para a URL registrada. Esse cabeçalho é codificado em base64.

Este exemplo ilustra como validar uma carga usando C#:

```csharp

private const string EventTypeHeaderName = "X-MicrosoftSpeechServices-Event";
private const string SignatureHeaderName = "X-MicrosoftSpeechServices-Signature";

[HttpPost]
public async Task<IActionResult> PostAsync([FromHeader(Name = EventTypeHeaderName)]WebHookEventType eventTypeHeader, [FromHeader(Name = SignatureHeaderName)]string signature)
{
    string body = string.Empty;
    using (var streamReader = new StreamReader(this.Request.Body))
    {
        body = await streamReader.ReadToEndAsync().ConfigureAwait(false);
        var secretBytes = Encoding.UTF8.GetBytes("my_secret");
        using (var hmacsha256 = new HMACSHA256(secretBytes))
        {
            var contentBytes = Encoding.UTF8.GetBytes(body);
            var contentHash = hmacsha256.ComputeHash(contentBytes);
            var storedHash = Convert.FromBase64String(signature);
            var validated = contentHash.SequenceEqual(storedHash);
        }
    }

    switch (eventTypeHeader)
    {
        case WebHookEventType.Ping:
            // Do your ping event related stuff here (or ignore this event)
            break;
        case WebHookEventType.TranscriptionCompletion:
            // Do your subscription related stuff here.
            break;
        default:
            break;
    }

    return this.Ok();
}

```
Nesse trecho de código, o `secret` é decodificado e validado. Você também observará que o tipo de evento webhook foi alternado. Atualmente, há um evento por transcrição concluída. O código repete cinco vezes para cada evento (com um atraso de um segundo) antes de desistir.

### <a name="other-webhook-operations"></a>Outras operações de webhook

Para obter todos os WebHooks registrados: GET https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks

Para obter um webhook específico: GET https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id

Para remover um webhook específico: DELETE https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id

> [!Note]
> No exemplo acima, a região é ' westus '. Isso deve ser substituído pela região em que você criou o recurso de serviços de fala no portal do Azure.

Corpo https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id/ping do post: vazio

Envia uma solicitação POST para a URL registrada. A solicitação contém um `X-MicrosoftSpeechServices-Event` cabeçalho com um ping de valor. Se o webhook foi registrado com um segredo, ele conterá um `X-MicrosoftSpeechServices-Signature` cabeçalho com um hash SHA256 da carga com o segredo como chave HMAC. O hash é codificado em base64.

Corpo https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id/test do post: vazio

Envia uma solicitação POST para a URL registrada se uma entidade para o tipo de evento assinado (transcrição) estiver presente no sistema e estiver no estado apropriado. A carga será gerada a partir da última entidade que chamaria o gancho da Web. Se nenhuma entidade estiver presente, a POSTAgem responderá com 204. Se uma solicitação de teste puder ser feita, ela responderá com 200. O corpo da solicitação é da mesma forma que na solicitação GET para uma entidade específica à qual o gancho da Web assinou (por exemplo, transcrição de instância). A solicitação terá os `X-MicrosoftSpeechServices-Event` cabeçalhos e `X-MicrosoftSpeechServices-Signature` , conforme descrito anteriormente.

### <a name="run-a-test"></a>Executar um teste

Um teste rápido pode ser feito usando o site https://bin.webhookrelay.com. A partir daí, você pode obter URLs de retorno de chamada para passar como parâmetro para o HTTP POST para criar um webhook descrito anteriormente no documento.

Clique em ' criar Bucket ' e siga as instruções na tela para obter um gancho. Em seguida, use as informações fornecidas nesta página para registrar o gancho no serviço de fala. A carga de uma mensagem de retransmissão – em resposta à conclusão de uma transcrição – tem a seguinte aparência:

```json
{
    "results": [],
    "recordingsUrls": [
        "my recording URL"
    ],
    "models": [
        {
            "modelKind": "AcousticAndLanguage",
            "datasets": [],
            "id": "a09c8c8b-1090-443c-895c-3b1cf442dec4",
            "createdDateTime": "2019-03-26T12:48:46Z",
            "lastActionDateTime": "2019-03-26T14:04:47Z",
            "status": "Succeeded",
            "locale": "en-US",
            "name": "v4.13 Unified",
            "description": "Unified",
            "properties": {
                "Purpose": "OnlineTranscription,BatchTranscription,LanguageAdaptation",
                "ModelClass": "unified-v4"
            }
        }
    ],
    "statusMessage": "None.",
    "id": "d41615e1-a60e-444b-b063-129649810b3a",
    "createdDateTime": "2019-04-16T09:35:51Z",
    "lastActionDateTime": "2019-04-16T09:38:09Z",
    "status": "Succeeded",
    "locale": "en-US",
    "name": "Simple transcription",
    "description": "Simple transcription description",
    "properties": {
        "PunctuationMode": "DictatedAndAutomatic",
        "ProfanityFilterMode": "Masked",
        "AddWordLevelTimestamps": "True",
        "AddSentiment": "True",
        "Duration": "00:00:02"
    }
}
```
A mensagem contém a URL de gravação e os modelos usados para transcrever essa gravação.

## <a name="next-steps"></a>Próximas etapas

* [Obtenha sua assinatura de avaliação de Fala](https://azure.microsoft.com/try/cognitive-services/)
