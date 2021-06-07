---
title: Como usar a transcrição do lote – serviço de fala
titleSuffix: Azure Cognitive Services
description: A transcrição de lote é ideal se você quer transcrever uma grande quantidade de áudio em armazenamento, como Blobs do Azure. Usando a API REST dedicada, você pode apontar para arquivos de áudio por um URI de SAS (assinatura de acesso compartilhado) e transcrições de recebimento de forma assíncrona.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/23/2020
ms.author: wolfma
ms.custom: devx-track-csharp
ms.openlocfilehash: e48fead4d4364fd84f178388dbfb9158296e687b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98659964"
---
# <a name="how-to-use-batch-transcription"></a>Como usar a transcrição do lote

A transcrição do lote é um conjunto de operações da API REST que permite transcrever uma grande quantidade de áudio no armazenamento. Você pode apontar para arquivos de áudio usando um URI típico ou um URI de [SAS (assinatura de acesso compartilhado)](../../storage/common/storage-sas-overview.md) e receber os resultados de transcrição de forma assíncrona. Com a API v 3.0, você pode transcrever um ou mais arquivos de áudio ou processar um contêiner de armazenamento inteiro.

Você pode usar as APIs REST de transcrição do lote para chamar os seguintes métodos:

|    Operação de transcrição em lote                                             |    Método    |    Chamada à API REST                                   |
|------------------------------------------------------------------------------|--------------|----------------------------------------------------|
|    Cria uma nova transcrição.                                              |    POST      |    speechtotext/v 3.0/transcrições            |
|    Recupera uma lista de transcrições para a assinatura autenticada.    |    GET       |    speechtotext/v 3.0/transcrições            |
|    Obtém uma lista de localidades com suporte para transcrições offline.              |    GET       |    speechtotext/v 3.0/transcrições/localidades    |
|    Atualiza os detalhes mutáveis da transcrição identificada por sua ID.    |    PATCH     |    speechtotext/v 3.0/transcrições/{ID}       |
|    Exclui a tarefa de transcrição especificada.                                 |    Delete (excluir)    |    speechtotext/v 3.0/transcrições/{ID}       |
|    Obtém a transcrição identificada pela ID fornecida.                        |    GET       |    speechtotext/v 3.0/transcrições/{ID}       |
|    Obtém os arquivos de resultado da transcrição identificada pela ID fornecida.    |    GET       |    speechtotext/v 3.0/transcrições/{ID}/arquivos |

Você pode examinar e testar a API detalhada, que está disponível como um [documento do Swagger](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0).

Os trabalhos de transcrição do lote são agendados de acordo com a melhor iniciativa.
Você não pode estimar quando um trabalho mudará para o estado de execução, mas isso deve ocorrer em minutos sob a carga normal do sistema. Uma vez no estado de execução, a transcrição ocorre mais rápido do que a velocidade de reprodução do tempo de execução de áudio.

## <a name="prerequisites"></a>Pré-requisitos

Como acontece com todos os recursos do serviço de fala, você cria uma chave de assinatura no [Portal do Microsoft Azure](https://portal.azure.com) seguindo nosso [Guia de primeiros passos](overview.md#try-the-speech-service-for-free).

>[!NOTE]
> Uma assinatura padrão (S0) para o serviço de fala é necessária para usar a transcrição do lote. Chaves de assinatura gratuita (F0) não funcionarão. Para obter mais informações, consulte [preços e limites](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

Se você planeja personalizar modelos, siga as etapas em [personalização acústica](./how-to-custom-speech-train-model.md) e [personalização de idioma](./how-to-custom-speech-train-model.md). Para usar os modelos criados na transcrição do lote, você precisa do local do modelo. Você pode recuperar o local do modelo ao inspecionar os detalhes do modelo ( `self` Propriedade). Um ponto de extremidade personalizado implantado *não é necessário* para o serviço de transcrição do lote.

>[!NOTE]
> Como parte da API REST, a transcrição do lote tem um conjunto de [cotas e limites](speech-services-quotas-and-limits.md#batch-transcription), que incentivamos a análise. Para aproveitar ao máximo a capacidade de transcrição do lote para transcrever com eficiência um grande número de arquivos de áudio, recomendamos sempre enviar vários arquivos por solicitação ou apontar para um contêiner de armazenamento de BLOBs com os arquivos de áudio para transcrever. O serviço irá transcrever os arquivos, reduzindo simultaneamente o tempo de retorno. O uso de vários arquivos em uma única solicitação é muito simples e direto – consulte a seção de [configuração](#configuration) . 

## <a name="batch-transcription-api"></a>Transcrição de lote API

A API de transcrição do lote dá suporte aos seguintes formatos:

| Formatar | Codec | Bits por amostra | Taxa de amostragem             |
|--------|-------|---------|---------------------------------|
| WAV    | PCM   | 16 bits  | 8 kHz ou 16 kHz, mono ou estéreo |
| MP3    | PCM   | 16 bits  | 8 kHz ou 16 kHz, mono ou estéreo |
| OGG    | OPUS  | 16 bits  | 8 kHz ou 16 kHz, mono ou estéreo |

Para fluxos de áudio estéreo, os canais esquerdo e direito são divididos durante a transcrição. Um arquivo de resultado JSON está sendo criado para cada canal.
Para criar uma transcrição final ordenada, use os carimbos de data/hora gerados por expressão.

### <a name="configuration"></a>Configuração

Os parâmetros de configuração são fornecidos como JSON. 

**Transcrever um ou mais arquivos individuais.** Se você tiver mais de um arquivo para transcrever, é recomendável enviar vários arquivos em uma solicitação. O exemplo a seguir está usando três arquivos:

```json
{
  "contentUrls": [
    "<URL to an audio file 1 to transcribe>",
    "<URL to an audio file 2 to transcribe>",
    "<URL to an audio file 3 to transcribe>"
  ],
  "properties": {
    "wordLevelTimestampsEnabled": true
  },
  "locale": "en-US",
  "displayName": "Transcription of file using default model for en-US"
}
```

**Processando um contêiner de armazenamento inteiro.** As [SAS](../../storage/common/storage-sas-overview.md) do contêiner devem conter `r` (leitura) e `l` as permissões (lista):

```json
{
  "contentContainerUrl": "<SAS URL to the Azure blob container to transcribe>",
  "properties": {
    "wordLevelTimestampsEnabled": true
  },
  "locale": "en-US",
  "displayName": "Transcription of container using default model for en-US"
}
```

**Use um modelo treinado personalizado em uma transcrição do lote.** O exemplo está usando três arquivos:

```json
{
  "contentUrls": [
    "<URL to an audio file 1 to transcribe>",
    "<URL to an audio file 2 to transcribe>",
    "<URL to an audio file 3 to transcribe>"
  ],
  "properties": {
    "wordLevelTimestampsEnabled": true
  },
  "locale": "en-US",
  "model": {
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/models/{id}"
  },
  "displayName": "Transcription of file using default model for en-US"
}
```


### <a name="configuration-properties"></a>Propriedades de configuração

Use essas propriedades opcionais para configurar a transcrição:

:::row:::
   :::column span="1":::
      **Parâmetro**
   :::column-end:::
   :::column span="2":::
      **Descrição**
:::row-end:::
:::row:::
   :::column span="1":::
      `profanityFilterMode`
   :::column-end:::
   :::column span="2":::
      Opcional, o padrão é `Masked` . Especifica como lidar com palavrões em resultados de reconhecimento. Os valores aceitos são `None` desabilitar a filtragem de profanação, `Masked` substituir a profanação por asteriscos, `Removed` remover toda a profanação do resultado ou `Tags` Adicionar marcas de "profanação".
:::row-end:::
:::row:::
   :::column span="1":::
      `punctuationMode`
   :::column-end:::
   :::column span="2":::
      Opcional, o padrão é `DictatedAndAutomatic` . Especifica como manipular a pontuação nos resultados do reconhecimento. Os valores aceitos são `None` para desabilitar `Dictated` a pontuação, para implicar Pontuação explícita (falada), `Automatic` para permitir que o decodificador lide com pontuação ou `DictatedAndAutomatic` use pontuação ditada e automática.
:::row-end:::
:::row:::
   :::column span="1":::
      `wordLevelTimestampsEnabled`
   :::column-end:::
   :::column span="2":::
      Opcional, `false` por padrão. Especifica se os carimbos de data/hora no nível da palavra devem ser adicionados à saída.
:::row-end:::
:::row:::
   :::column span="1":::
      `diarizationEnabled`
   :::column-end:::
   :::column span="2":::
      Opcional, `false` por padrão. Especifica que a análise de diarization deve ser executada na entrada, que se espera que seja um canal mono contendo duas vozes. Observação: requer `wordLevelTimestampsEnabled` que seja definido como `true` .
:::row-end:::
:::row:::
   :::column span="1":::
      `channels`
   :::column-end:::
   :::column span="2":::
      Opcional `0` e `1` transcrita por padrão. Uma matriz de números de canal para processar. Aqui, um subconjunto dos canais disponíveis no arquivo de áudio pode ser especificado para ser processado (por exemplo, `0` somente).
:::row-end:::
:::row:::
   :::column span="1":::
      `timeToLive`
   :::column-end:::
   :::column span="2":::
      Opcional, sem exclusão por padrão. Uma duração para excluir automaticamente as transcrições após a conclusão da transcrição. O `timeToLive` é útil em transcrições de processamento em massa para garantir que eles serão eventualmente excluídos (por exemplo, `PT12H` por 12 horas).
:::row-end:::
:::row:::
   :::column span="1":::
      `destinationContainerUrl`
   :::column-end:::
   :::column span="2":::
      URL opcional com [SAS ad hoc](../../storage/common/storage-sas-overview.md) para um contêiner gravável no Azure. O resultado é armazenado nesse contêiner. **Não** há suporte para SAS com política de acesso armazenada. Quando não especificado, a Microsoft armazena os resultados em um contêiner de armazenamento gerenciado pela Microsoft. Quando a transcrição for excluída chamando a [transcrição de exclusão](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/DeleteTranscription), os dados de resultado também serão excluídos.
:::row-end:::

### <a name="storage"></a>Armazenamento

A transcrição do lote pode ler áudio de um URI da Internet disponível para o público e pode ler as transcrições de áudio ou gravação usando um URI de SAS com o [armazenamento de BLOBs do Azure](../../storage/blobs/storage-blobs-overview.md).

## <a name="batch-transcription-result"></a>Resultado da transcrição do lote

Para cada entrada de áudio, um arquivo de resultado de transcrição é criado.
A operação [obter arquivos de transcrições](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetTranscriptionFiles) retorna uma lista de arquivos de resultado para esta transcrição. Para localizar o arquivo de transcrição de um arquivo de entrada específico, filtre todos os arquivos retornados com `kind`  ==  `Transcription` e `name`  ==  `{originalInputName.suffix}.json` .

Cada arquivo de resultado da transcrição tem este formato:

```json
{
  "source": "...",                      // sas url of a given contentUrl or the path relative to the root of a given container
  "timestamp": "2020-06-16T09:30:21Z",  // creation time of the transcription, ISO 8601 encoded timestamp, combined date and time
  "durationInTicks": 41200000,          // total audio duration in ticks (1 tick is 100 nanoseconds)
  "duration": "PT4.12S",                // total audio duration, ISO 8601 encoded duration
  "combinedRecognizedPhrases": [        // concatenated results for simple access in single string for each channel
    {
      "channel": 0,                     // channel number of the concatenated results
      "lexical": "hello world",
      "itn": "hello world",
      "maskedITN": "hello world",
      "display": "Hello world."
    }
  ],
  "recognizedPhrases": [                // results for each phrase and each channel individually
    {
      "recognitionStatus": "Success",   // recognition state, e.g. "Success", "Failure"          
      "speaker": 1,                     // if `diarizationEnabled` is `true`, this is the identified speaker (1 or 2), otherwise this property is not present
      "channel": 0,                     // channel number of the result
      "offset": "PT0.07S",              // offset in audio of this phrase, ISO 8601 encoded duration 
      "duration": "PT1.59S",            // audio duration of this phrase, ISO 8601 encoded duration
      "offsetInTicks": 700000.0,        // offset in audio of this phrase in ticks (1 tick is 100 nanoseconds)
      "durationInTicks": 15900000.0,    // audio duration of this phrase in ticks (1 tick is 100 nanoseconds)

      // possible transcriptions of the current phrase with confidences
      "nBest": [
        {
          "confidence": 0.898652852,    // confidence value for the recognition of the whole phrase
          "lexical": "hello world",
          "itn": "hello world",
          "maskedITN": "hello world",
          "display": "Hello world.",

          // if wordLevelTimestampsEnabled is `true`, there will be a result for each word of the phrase, otherwise this property is not present
          "words": [
            {
              "word": "hello",
              "offset": "PT0.09S",
              "duration": "PT0.48S",
              "offsetInTicks": 900000.0,
              "durationInTicks": 4800000.0,
              "confidence": 0.987572
            },
            {
              "word": "world",
              "offset": "PT0.59S",
              "duration": "PT0.16S",
              "offsetInTicks": 5900000.0,
              "durationInTicks": 1600000.0,
              "confidence": 0.906032
            }
          ]
        }
      ]
    }
  ]
}
```

O resultado contém os seguintes campos:

:::row:::
   :::column span="1":::
      **Campo**
   :::column-end:::
   :::column span="2":::
      **Conteúdo**
:::row-end:::
:::row:::
   :::column span="1":::
      `lexical`
   :::column-end:::
   :::column span="2":::
      As palavras reais reconhecidas.
:::row-end:::
:::row:::
   :::column span="1":::
      `itn`
   :::column-end:::
   :::column span="2":::
      Forma normalizada de texto inverso do texto reconhecido. Abreviações ("Doctor Smith" para "Dr Smith"), números de telefone e outras transformações são aplicadas.
:::row-end:::
:::row:::
   :::column span="1":::
      `maskedITN`
   :::column-end:::
   :::column span="2":::
      O formulário em com mascaramento de profanação aplicado.
:::row-end:::
:::row:::
   :::column span="1":::
      `display`
   :::column-end:::
   :::column span="2":::
      O formulário de exibição do texto reconhecido. A pontuação adicionada e a capitalização são incluídas.
:::row-end:::

## <a name="speaker-separation-diarization"></a>Separação de alto-falantes (diarization)

Diarization é o processo de separar os alto-falantes em um pedaço de áudio. O pipeline do lote dá suporte a diarization e é capaz de reconhecer dois alto-falantes em gravações de canal mono. O recurso não está disponível em gravações de estéreo.

A saída de transcrição com diarization habilitado contém uma `Speaker` entrada para cada frase transcrita. Se diarization não for usado, a `Speaker` propriedade não estará presente na saída JSON. Para diarization, damos suporte a duas vozes, portanto os alto-falantes são identificados como `1` ou `2` .

Para solicitar diarization, adicione definir a `diarizationEnabled` propriedade como `true` a solicitação HTTP mostrada abaixo.

 ```json
{
  "contentUrls": [
    "<URL to an audio file to transcribe>",
  ],
  "properties": {
    "diarizationEnabled": true,
    "wordLevelTimestampsEnabled": true,
    "punctuationMode": "DictatedAndAutomatic",
    "profanityFilterMode": "Masked"
  },
  "locale": "en-US",
  "displayName": "Transcription of file using default model for en-US"
}
```

Os carimbos de data/hora de nível de palavra devem ser habilitados, já que os parâmetros na solicitação acima indicam.

## <a name="best-practices"></a>Práticas recomendadas

O serviço de transcrição do lote pode lidar com um grande número de transcrições enviadas. Você pode consultar o status de suas transcrições com [obter transcrições](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetTranscriptions).
Chame [excluir transcrição](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/DeleteTranscription) regularmente do serviço depois de recuperar os resultados. Como alternativa, defina a `timeToLive` propriedade para garantir a exclusão eventual dos resultados.

## <a name="sample-code"></a>Código de exemplo

Exemplos completos estão disponíveis no [repositório de exemplo do GitHub](https://aka.ms/csspeech/samples) dentro do `samples/batch` subdiretório.

Atualize o código de exemplo com suas informações de assinatura, região de serviço, URI apontando para o arquivo de áudio para transcrever e local do modelo se você estiver usando um modelo personalizado.

[!code-csharp[Configuration variables for batch transcription](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#transcriptiondefinition)]

O código de exemplo configura o cliente e envia a solicitação de transcrição. Em seguida, ele sonda as informações de status e os detalhes de impressão sobre o progresso da transcrição.

```csharp
// get the status of our transcriptions periodically and log results
int completed = 0, running = 0, notStarted = 0;
while (completed < 1)
{
    completed = 0; running = 0; notStarted = 0;

    // get all transcriptions for the user
    paginatedTranscriptions = null;
    do
    {
        // <transcriptionstatus>
        if (paginatedTranscriptions == null)
        {
            paginatedTranscriptions = await client.GetTranscriptionsAsync().ConfigureAwait(false);
        }
        else
        {
            paginatedTranscriptions = await client.GetTranscriptionsAsync(paginatedTranscriptions.NextLink).ConfigureAwait(false);
        }

        // delete all pre-existing completed transcriptions. If transcriptions are still running or not started, they will not be deleted
        foreach (var transcription in paginatedTranscriptions.Values)
        {
            switch (transcription.Status)
            {
                case "Failed":
                case "Succeeded":
                    // we check to see if it was one of the transcriptions we created from this client.
                    if (!createdTranscriptions.Contains(transcription.Self))
                    {
                        // not created form here, continue
                        continue;
                    }

                    completed++;

                    // if the transcription was successful, check the results
                    if (transcription.Status == "Succeeded")
                    {
                        var paginatedfiles = await client.GetTranscriptionFilesAsync(transcription.Links.Files).ConfigureAwait(false);

                        var resultFile = paginatedfiles.Values.FirstOrDefault(f => f.Kind == ArtifactKind.Transcription);
                        var result = await client.GetTranscriptionResultAsync(new Uri(resultFile.Links.ContentUrl)).ConfigureAwait(false);
                        Console.WriteLine("Transcription succeeded. Results: ");
                        Console.WriteLine(JsonConvert.SerializeObject(result, SpeechJsonContractResolver.WriterSettings));
                    }
                    else
                    {
                        Console.WriteLine("Transcription failed. Status: {0}", transcription.Properties.Error.Message);
                    }

                    break;

                case "Running":
                    running++;
                    break;

                case "NotStarted":
                    notStarted++;
                    break;
            }
        }

        // for each transcription in the list we check the status
        Console.WriteLine(string.Format("Transcriptions status: {0} completed, {1} running, {2} not started yet", completed, running, notStarted));
    }
    while (paginatedTranscriptions.NextLink != null);

    // </transcriptionstatus>
    // check again after 1 minute
    await Task.Delay(TimeSpan.FromMinutes(1)).ConfigureAwait(false);
}
```

Para obter detalhes completos sobre as chamadas anteriores, consulte nosso [documento do Swagger](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0). Para o exemplo completo mostrado aqui, acesse [GitHub](https://aka.ms/csspeech/samples) no `samples/batch` subdiretório.

Este exemplo usa uma configuração assíncrona para postar áudio e receber o status da transcrição.
O `PostTranscriptions` método envia os detalhes do arquivo de áudio e o `GetTranscriptions` método recebe os Estados.
`PostTranscriptions` retorna um identificador e `GetTranscriptions` o usa para criar um identificador para obter o status de transcrição.

Este código de exemplo não especifica um modelo personalizado. O serviço usa o modelo de linha de base para transcrever o arquivo ou arquivos. Para especificar o modelo, você pode passar o mesmo método de referência do modelo para o modelo personalizado.

> [!NOTE]
> Para transcrições de linha de base, você não precisa declarar a ID para o modelo de linha de base.

## <a name="next-steps"></a>Próximas etapas

- [Referência da API de fala para texto v3](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/CopyModelToSubscription)
