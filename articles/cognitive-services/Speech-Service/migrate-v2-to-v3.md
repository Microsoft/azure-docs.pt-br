---
title: Migrar da API REST v2 para v3-serviço de fala
titleSuffix: Azure Cognitive Services
description: Este documento ajuda os desenvolvedores a migrar código de v2 para V3 do na API REST de fala em texto dos serviços de fala.
services: cognitive-services
author: bexxx
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: rbeckers
ms.custom: devx-track-csharp
ms.openlocfilehash: 9c8016b566db8be1b7f5c5ddb8d92123d6673db5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98569837"
---
# <a name="migrate-code-from-v20-to-v30-of-the-rest-api"></a>Migrar o código de v 2.0 para v 3.0 da API REST

Em comparação com a v2, a versão V3 da API REST dos serviços de fala para a fala em texto é mais confiável, mais fácil de usar e mais consistente com as APIs para serviços semelhantes. A maioria das equipes pode migrar de v2 para v3 em um ou dois dias.

## <a name="forward-compatibility"></a>Compatibilidade com encaminhamento

Todas as entidades de v2 também podem ser encontradas na API v3 sob a mesma identidade. Onde o esquema de um resultado foi alterado (por exemplo, transcrições), o resultado de um GET na versão V3 da API usa o esquema v3. O resultado de um GET na versão V2 da API usa o mesmo esquema v2. As entidades recém-criadas no v3 **não** estão   disponíveis em respostas de APIs v2. 

## <a name="migration-steps"></a>Etapas da migração

Esta é uma lista resumida de itens que você precisa estar atento ao se preparar para a migração. Os detalhes são encontrados nos links individuais. Dependendo do uso atual da API, nem todas as etapas listadas aqui podem ser aplicáveis. Apenas algumas alterações exigem alterações não triviais no código de chamada. A maioria das alterações requer apenas uma alteração nos nomes dos itens. 

Alterações gerais: 

1. [Alterar o nome do host](#host-name-changes)

1. [Renomeie a ID da propriedade como Self no código do cliente](#identity-of-an-entity) 

1. [Alterar o código para iterar em coleções de entidades](#working-with-collections-of-entities)

1. [Renomeie o nome da propriedade para displayName no seu código de cliente](#name-of-an-entity)

1. [Ajustar a recuperação dos metadados das entidades referenciadas](#accessing-referenced-entities)

1. Se você usar a transcrição do lote: 

    * [Ajustar o código para criar transcrições em lotes](#creating-transcriptions) 

    * [Adaptar o código ao novo esquema de resultados de transcrição](#format-of-v3-transcription-results)

    * [Ajustar o código para o modo como os resultados são recuperados](#getting-the-content-of-entities-and-the-results)

1. Se você usar APIs de treinamento/teste de modelo personalizado: 

    * [Aplicar modificações ao treinamento de modelo personalizado](#customizing-models)

    * [Alterar a forma como os modelos de base e personalizados são recuperados](#retrieving-base-and-custom-models)

    * [Renomear o segmento de caminho accuracytests para avaliações em seu código de cliente](#accuracy-tests)

1. Se você usar as APIs de pontos de extremidade:

    * [Alterar como os logs de ponto de extremidade são recuperados](#retrieving-endpoint-logs)

1. Outras alterações secundárias: 

    * [Passe todas as propriedades personalizadas como CustomProperties em vez de propriedades em suas solicitações POST](#using-custom-properties)

    * [Ler o local do cabeçalho de resposta em vez do local de operação](#response-headers)

## <a name="breaking-changes"></a>Alterações de quebra

### <a name="host-name-changes"></a>Alterações de nome de host

Os nomes de host do ponto de extremidade foram alterados de `{region}.cris.ai` para `{region}.api.cognitive.microsoft.com` . Os caminhos para os novos pontos de extremidade não contêm mais `api/` porque ele faz parte do nome do host. O [documento do Swagger](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0) lista caminhos e regiões válidos.
>[!IMPORTANT]
>Altere o nome do host de `{region}.cris.ai` para `{region}.api.cognitive.microsoft.com` onde região é a região da sua assinatura de fala. Também remova `api/` de qualquer caminho no código do cliente.

### <a name="identity-of-an-entity"></a>Identidade de uma entidade

A propriedade `id` agora é `self` . Na v2, um usuário de API tinha que saber como nossos caminhos na API estão sendo criados. Isso era não extensível e exigia um trabalho desnecessário do usuário. A propriedade `id` (UUID) é substituída por `self` (cadeia de caracteres), que é o local da entidade (URL). O valor ainda é exclusivo entre todas as suas entidades. Se `id` é armazenado como uma cadeia de caracteres em seu código, uma renomeação é suficiente para dar suporte ao novo esquema. Agora você pode usar o `self` conteúdo como a URL para as `GET` `PATCH` chamadas REST,, e `DELETE` para sua entidade.

Se a entidade tiver funcionalidade adicional disponível por meio de outros caminhos, elas serão listadas em `links` . O exemplo a seguir de transcrição mostra um método separado para `GET` o conteúdo da transcrição:
>[!IMPORTANT]
>Renomeie a propriedade `id` como `self` no código do cliente. Altere o tipo de `uuid` para `string` se necessário. 

**transcrição de v2:**

```json
{
    "id": "9891c965-bb32-4880-b14b-6d44efb158f3",
    "createdDateTime": "2019-01-07T11:34:12Z",
    "lastActionDateTime": "2019-01-07T11:36:07Z",
    "status": "Succeeded",
    "locale": "en-US",
    "name": "Transcription using locale en-US"
}
```

**transcrição V3:**

```json
{
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3",
    "createdDateTime": "2019-01-07T11:34:12Z",
    "lastActionDateTime": "2019-01-07T11:36:07Z",
    "status": "Succeeded",
    "locale": "en-US", 
    "displayName": "Transcription using locale en-US",
    "links": {
      "files": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files"
    }
}
```

Dependendo da implementação do código, talvez não seja suficiente renomear a propriedade. É recomendável usar os `self` valores retornados e `links` como as URLs de destino de suas chamadas REST, em vez de gerar caminhos em seu cliente. Usando as URLs retornadas, você pode ter certeza de que as alterações futuras em caminhos não quebrarão o código do cliente.

### <a name="working-with-collections-of-entities"></a>Trabalhando com coleções de entidades

Anteriormente, a API v2 retornou todas as entidades disponíveis em um resultado. Para permitir um controle mais refinado sobre o tamanho de resposta esperado na v3, todos os resultados da coleção são paginados. Você tem controle sobre a contagem de entidades retornadas e o deslocamento inicial da página. Esse comportamento facilita a previsão do tempo de execução do processador de resposta.

A forma básica da resposta é a mesma para todas as coleções:

```json
{
    "values": [
        {     
        }
    ],
    "@nextLink": "https://{region}.api.cognitive.microsoft.com/speechtotext/v3.0/{collection}?skip=100&top=100"
}
```

A `values` propriedade contém um subconjunto das entidades de coleção disponíveis. A contagem e o deslocamento podem ser controlados usando os `skip` `top` parâmetros de consulta e. Quando `@nextLink` não está `null` , há mais dados disponíveis e o próximo lote de dados pode ser recuperado fazendo um get on `$.@nextLink` .

Essa alteração requer chamar o `GET` para a coleção em um loop até que todos os elementos tenham sido retornados.

>[!IMPORTANT]
>Quando a resposta de um GET para `speechtotext/v3.0/{collection}` contém um valor em `$.@nextLink` , continue a emitir `GETs` `$.@nextLink` até que `$.@nextLink` não esteja definida para recuperar todos os elementos dessa coleção.

### <a name="creating-transcriptions"></a>Criando transcrições

Uma descrição detalhada sobre como criar lotes de transcrições pode ser encontrada em instruções de [transcrição do lote](./batch-transcription.md).

A API de transcrição v3 permite que você defina explicitamente opções de transcrição específicas. Todas as propriedades de configuração (opcional) agora podem ser definidas na `properties` propriedade.
A versão v3 também dá suporte a vários arquivos de entrada, portanto, ele requer uma lista de URLs em vez de uma única URL como a v2. O nome da propriedade v2 `recordingsUrl` agora está `contentUrls` em v3. A funcionalidade de análise de sentimentos em transcrições foi removida na v3. Consulte [análise de texto](https://azure.microsoft.com/en-us/services/cognitive-services/text-analytics/) do serviço cognitiva da Microsoft para obter opções de análise de sentimentos.

A nova propriedade `timeToLive` em `properties` pode ajudar a remover as entidades concluídas existentes. `timeToLive`Especifica uma duração após a qual uma entidade concluída será excluída automaticamente. Defina-o como um valor alto (por exemplo `PT12H` ) quando as entidades são continuamente rastreadas, consumidas e excluídas e, portanto, geralmente processadas antes de 12 horas passaram.

**corpo da solicitação de POSTAgem de transcrição v2:**

```json
{
  "locale": "en-US",
  "name": "Transcription using locale en-US",
  "recordingsUrl": "https://contoso.com/mystoragelocation",
  "properties": {
    "AddDiarization": "False",
    "AddWordLevelTimestamps": "False",
    "PunctuationMode": "DictatedAndAutomatic",
    "ProfanityFilterMode": "Masked"
  }
}
```

**corpo da solicitação de POSTAgem de transcrição V3:**

```json
{
  "locale": "en-US",
  "displayName": "Transcription using locale en-US",
  "contentUrls": [
    "https://contoso.com/mystoragelocation",
    "https://contoso.com/myotherstoragelocation"
  ],
  "properties": {
    "diarizationEnabled": false,
    "wordLevelTimestampsEnabled": false,
    "punctuationMode": "DictatedAndAutomatic",
    "profanityFilterMode": "Masked"
  }
}
```
>[!IMPORTANT]
>Renomeie a propriedade `recordingsUrl` para `contentUrls` e passe uma matriz de URLs em vez de uma única URL. Passe as configurações para `diarizationEnabled` ou `wordLevelTimestampsEnabled` como `bool` em vez de `string` .

### <a name="format-of-v3-transcription-results"></a>Formato dos resultados da transcrição v3

O esquema dos resultados da transcrição mudou um pouco para alinhar com as transcrições criadas por pontos de extremidade em tempo real. Encontre uma descrição detalhada do novo formato no [como fazer a transcrição do lote](./batch-transcription.md). O esquema do resultado é publicado em nosso [repositório de exemplo do GitHub](https://aka.ms/csspeech/samples) em `samples/batch/transcriptionresult_v3.schema.json` .

Os nomes de propriedade agora são camel-case e os valores para `channel` e `speaker` agora usam tipos inteiros. O formato de durações agora usa a estrutura descrita em ISO 8601, que corresponde à formatação de duração usada em outras APIs do Azure.

Exemplo de um resultado de transcrição v3. As diferenças são descritas nos comentários.

```json
{
  "source": "...",                      // (new in v3) was AudioFileName / AudioFileUrl
  "timestamp": "2020-06-16T09:30:21Z",  // (new in v3)
  "durationInTicks": 41200000,          // (new in v3) was AudioLengthInSeconds
  "duration": "PT4.12S",                // (new in v3)
  "combinedRecognizedPhrases": [        // (new in v3) was CombinedResults
    {
      "channel": 0,                     // (new in v3) was ChannelNumber
      "lexical": "hello world",
      "itn": "hello world",
      "maskedITN": "hello world",
      "display": "Hello world."
    }
  ],
  "recognizedPhrases": [                // (new in v3) was SegmentResults
    {
      "recognitionStatus": "Success",   // 
      "channel": 0,                     // (new in v3) was ChannelNumber
      "offset": "PT0.07S",              // (new in v3) new format, was OffsetInSeconds
      "duration": "PT1.59S",            // (new in v3) new format, was DurationInSeconds
      "offsetInTicks": 700000.0,        // (new in v3) was Offset
      "durationInTicks": 15900000.0,    // (new in v3) was Duration

      // possible transcriptions of the current phrase with confidences
      "nBest": [
        {
          "confidence": 0.898652852,phrase
          "speaker": 1,
          "lexical": "hello world",
          "itn": "hello world",
          "maskedITN": "hello world",
          "display": "Hello world.",

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
>[!IMPORTANT]
>Desserializar o resultado da transcrição no novo tipo, conforme mostrado acima. Em vez de um único arquivo por canal de áudio, diferencie os canais verificando o valor da propriedade de `channel` para cada elemento em `recognizedPhrases` . Agora há um único arquivo de resultado para cada arquivo de entrada.


### <a name="getting-the-content-of-entities-and-the-results"></a>Obtendo o conteúdo das entidades e os resultados

Na v2, os links para os arquivos de entrada ou resultado foram embutidos com o restante dos metadados de entidade. Como uma melhoria na v3, há uma separação clara entre os metadados de entidade (que são retornados por um GET on `$.self` ) e os detalhes e as credenciais para acessar os arquivos de resultado. Essa separação ajuda a proteger os dados do cliente e permite um controle fino sobre a duração da validade das credenciais.

No v3, `links` inclua uma subpropriedade chamada `files` no caso de a entidade exponha dados (DataSets, transcrições, pontos de extremidade ou avaliações). Um GET on retornará `$.links.files` uma lista de arquivos e uma URL de SAS para acessar o conteúdo de cada arquivo. Para controlar a duração da validade das URLs de SAS, o parâmetro de consulta `sasValidityInSeconds` pode ser usado para especificar o tempo de vida.

**transcrição de v2:**

```json
{
  "id": "9891c965-bb32-4880-b14b-6d44efb158f3",
  "status": "Succeeded",
  "reportFileUrl": "https://contoso.com/report.txt?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=6c044930-3926-4be4-be76-f728327c53b5",
  "resultsUrls": {
    "channel_0": "https://contoso.com/audiofile1.wav?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=6c044930-3926-4be4-be76-f72832e6600c",
    "channel_1": "https://contoso.com/audiofile2.wav?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=3e0163f1-0029-4d4a-988d-3fba7d7c53b5"
  }
}
```

**transcrição V3:**

```json
{
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3",
    "links": {
      "files": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files"
    } 
}
```

**Um GET on `$.links.files` resultaria em:**

```json
{
  "values": [
    {
      "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files/f23e54f5-ed74-4c31-9730-2f1a3ef83ce8",
      "name": "Name",
      "kind": "Transcription",
      "properties": {
        "size": 200
      },
      "createdDateTime": "2020-01-13T08:00:00Z",
      "links": {
        "contentUrl": "https://customspeech-usw.blob.core.windows.net/artifacts/mywavefile1.wav.json?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=e05d8d56-9675-448b-820c-4318ae64c8d5"
      }
    },
    {
      "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files/28bc946b-c251-4a86-84f6-ea0f0a2373ef",
      "name": "Name",
      "kind": "TranscriptionReport",
      "properties": {
        "size": 200
      },
      "createdDateTime": "2020-01-13T08:00:00Z",
      "links": {
        "contentUrl": "https://customspeech-usw.blob.core.windows.net/artifacts/report.json?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=e05d8d56-9675-448b-820c-4318ae64c8d5"
      }
    }
  ],
  "@nextLink": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files?skip=2&top=2"
}
```

A `kind` propriedade indica o formato do conteúdo do arquivo. Para transcrições, os arquivos de tipo `TranscriptionReport` são o resumo do trabalho e os arquivos do tipo `Transcription` são o resultado do próprio trabalho.

>[!IMPORTANT]
>Para obter os resultados das operações, use um `GET` on `/speechtotext/v3.0/{collection}/{id}/files` , elas não estão mais contidas nas respostas de `GET` em `/speechtotext/v3.0/{collection}/{id}` ou `/speechtotext/v3.0/{collection}` .

### <a name="customizing-models"></a>Personalizando modelos

Antes da v3, houve uma distinção entre um _modelo acústico_ e um _modelo de linguagem_ quando um modelo estava sendo treinado. Essa distinção resultou na necessidade de especificar vários modelos ao criar pontos de extremidade ou transcrições. Para simplificar esse processo para um chamador, removemos as diferenças e tornamos tudo a depender do conteúdo dos conjuntos de valores que estão sendo usados para o treinamento do modelo. Com essa alteração, a criação do modelo agora dá suporte a conjuntos de dados mistos, bem como dados de idioma e acústicos. Os pontos de extremidade e as transcrições agora exigem apenas um modelo.

Com essa alteração, a necessidade de um `kind` na `POST` operação foi removida e a `datasets[]` matriz agora pode conter vários conjuntos de os mesmos ou tipos mistos.

Para melhorar os resultados de um modelo treinado, os dados acústicos são automaticamente usados internamente durante o treinamento de idioma. Em geral, os modelos criados por meio da API v3 fornecem resultados mais precisos do que os modelos criados com a API v2.

>[!IMPORTANT]
>Para personalizar a parte do modelo acústico e de linguagem, passe todo o idioma necessário e os conjuntos de valores acústicos na `datasets[]` postagem para `/speechtotext/v3.0/models` . Isso criará um modelo único com ambas as partes personalizadas.

### <a name="retrieving-base-and-custom-models"></a>Recuperando modelos básicos e personalizados

Para simplificar a obtenção dos modelos disponíveis, a V3 separou as coleções de "modelos básicos" dos "modelos personalizados" de Propriedade do cliente. As duas rotas agora são `GET /speechtotext/v3.0/models/base` e `GET /speechtotext/v3.0/models/` .

Na v2, todos os modelos foram retornados juntos em uma única resposta.

>[!IMPORTANT]
>Para obter uma lista de modelos base fornecidos para personalização, use `GET` em `/speechtotext/v3.0/models/base` . Você pode encontrar seus próprios modelos personalizados com um `GET` ativado `/speechtotext/v3.0/models` .

### <a name="name-of-an-entity"></a>Nome de uma entidade

A `name` Propriedade agora é `displayName` . Isso é consistente com outras APIs do Azure para não indicar Propriedades de identidade. O valor dessa propriedade não deve ser exclusivo e pode ser alterado após a criação da entidade com uma `PATCH` operação.

**transcrição de v2:**

```json
{
    "name": "Transcription using locale en-US"
}
```

**transcrição V3:**

```json
{
    "displayName": "Transcription using locale en-US"
}
```

>[!IMPORTANT]
>Renomeie a propriedade `name` como `displayName` no código do cliente.

### <a name="accessing-referenced-entities"></a>Acessando entidades referenciadas

Na v2, as entidades referenciadas eram sempre embutidas, por exemplo, os modelos usados de um ponto de extremidade. O aninhamento de entidades resultou em grandes respostas e os consumidores raramente consumiram o conteúdo aninhado. Para reduzir o tamanho da resposta e melhorar o desempenho, as entidades referenciadas não são mais embutidas na resposta. Em vez disso, uma referência à outra entidade é exibida e pode ser usada diretamente para um subseqüente `GET` (também é uma URL), seguindo o mesmo padrão que o `self` link.

**transcrição de v2:**

```json
{
  "id": "9891c965-bb32-4880-b14b-6d44efb158f3",
  "models": [
    {
      "id": "827712a5-f942-4997-91c3-7c6cde35600b",
      "modelKind": "Language",
      "lastActionDateTime": "2019-01-07T11:36:07Z",
      "status": "Running",
      "createdDateTime": "2019-01-07T11:34:12Z",
      "locale": "en-US",
      "name": "Acoustic model",
      "description": "Example for an acoustic model",
      "datasets": [
        {
          "id": "702d913a-8ba6-4f66-ad5c-897400b081fb",
          "dataImportKind": "Language",
          "lastActionDateTime": "2019-01-07T11:36:07Z",
          "status": "Succeeded",
          "createdDateTime": "2019-01-07T11:34:12Z",
          "locale": "en-US",
          "name": "Language dataset",
        }
      ]
    },
  ]
}
```

**transcrição V3:**

```json
{
  "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3",
  "model": {
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/models/021a72d0-54c4-43d3-8254-27336ead9037"
  }
}
```

Se você precisar consumir os detalhes de um modelo referenciado, conforme mostrado no exemplo acima, basta emitir um GET on `$.model.self` .

>[!IMPORTANT]
>Para recuperar os metadados das entidades referenciadas, emita um GET on `$.{referencedEntity}.self` , por exemplo, para recuperar o modelo de uma transcrição, faça um `GET` `$.model.self` .


### <a name="retrieving-endpoint-logs"></a>Recuperando logs de ponto de extremidade

Versão v2 dos resultados do ponto de extremidade de log com suporte do serviço. Para recuperar os resultados de um ponto de extremidade com v2, você criaria uma "exportação de dados", que representava um instantâneo dos resultados definidos por um intervalo de tempo. O processo de exportação de lotes de dados era inflexível. A API v3 fornece acesso a cada arquivo individual e permite a iteração por meio deles.

**Um ponto de extremidade v3 em execução com êxito:**

```json
{
  "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/afa0669c-a01e-4693-ae3a-93baf40f26d6",
  "links": {
    "logs": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/afa0669c-a01e-4693-ae3a-93baf40f26d6/files/logs" 
  }
}
```

**Resposta de GET `$.links.logs` :**

```json
{
  "values": [
    {
      "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/6d72ad7e-f286-4a6f-b81b-a0532ca6bcaa/files/logs/2019-09-20_080000_3b5f4628-e225-439d-bd27-8804f9eed13f.wav",
      "name": "2019-09-20_080000_3b5f4628-e225-439d-bd27-8804f9eed13f.wav",
      "kind": "Audio",
      "properties": {
        "size": 12345
      },
      "createdDateTime": "2020-01-13T08:00:00Z",
      "links": {
        "contentUrl": "https://customspeech-usw.blob.core.windows.net/artifacts/2019-09-20_080000_3b5f4628-e225-439d-bd27-8804f9eed13f.wav?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=e05d8d56-9675-448b-820c-4318ae64c8d5"
      }
    }    
  ],
  "@nextLink": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/afa0669c-a01e-4693-ae3a-93baf40f26d6/files/logs?top=2&SkipToken=2!188!MDAwMDk1ITZhMjhiMDllLTg0MDYtNDViMi1hMGRkLWFlNzRlOGRhZWJkNi8yMDIwLTA0LTAxLzEyNDY0M182MzI5NGRkMi1mZGYzLTRhZmEtOTA0NC1mODU5ZTcxOWJiYzYud2F2ITAwMDAyOCE5OTk5LTEyLTMxVDIzOjU5OjU5Ljk5OTk5OTlaIQ--"
}
```

A paginação para logs de ponto de extremidade funciona de forma semelhante a todas as outras coleções, exceto que nenhum deslocamento pode ser especificado. Devido à grande quantidade de dados disponíveis, a paginação é determinada pelo servidor.

No v3, cada log de ponto de extremidade pode ser excluído individualmente por meio da emissão de uma `DELETE` operação no `self` de um arquivo ou usando o `DELETE` em `$.links.logs` . Para especificar uma data de término, o parâmetro de consulta `endDate` pode ser adicionado à solicitação.

>[!IMPORTANT]
>Em vez de criar exportações de log em `/api/speechtotext/v2.0/endpoints/{id}/data` uso `/v3.0/endpoints/{id}/files/logs/` para acessar arquivos de log individualmente. 

### <a name="using-custom-properties"></a>Usando propriedades personalizadas

Para separar Propriedades personalizadas das propriedades de configuração opcionais, todas as propriedades nomeadas explicitamente agora estão localizadas na `properties` propriedade e todas as propriedades definidas pelos chamadores agora estão localizadas na `customProperties` propriedade.

**entidade de transcrição v2:**

```json
{
  "properties": {
    "customerDefinedKey": "value",
    "diarizationEnabled": "False",
    "wordLevelTimestampsEnabled": "False"
  }
}
```

**entidade de transcrição V3:**

```json
{
  "properties": {
    "diarizationEnabled": false,
    "wordLevelTimestampsEnabled": false 
  },
  "customProperties": {
    "customerDefinedKey": "value"
  }
}
```

Essa alteração também permite que você use tipos corretos em todas as propriedades nomeadas explicitamente em `properties` (por exemplo, booliano, em vez de cadeia de caracteres).

>[!IMPORTANT]
>Passe todas as propriedades personalizadas como `customProperties` em vez de `properties` em suas `POST` solicitações.

### <a name="response-headers"></a>Cabeçalhos de resposta

V3 não retorna mais o `Operation-Location` cabeçalho além do `Location` cabeçalho em `POST` solicitações. O valor de ambos os cabeçalhos em v2 era o mesmo. Agora, somente `Location` é retornado.

Como a nova versão de API agora é gerenciada pelo gerenciamento de API do Azure (APIM), os cabeçalhos relacionados à limitação `X-RateLimit-Limit` , `X-RateLimit-Remaining` e `X-RateLimit-Reset` não estão contidos nos cabeçalhos de resposta.

>[!IMPORTANT]
>Leia o local do cabeçalho de resposta `Location` em vez de `Operation-Location` . No caso de um código de resposta 429, leia o `Retry-After` valor do cabeçalho em vez de `X-RateLimit-Limit` , `X-RateLimit-Remaining` ou `X-RateLimit-Reset` .


### <a name="accuracy-tests"></a>Testes de precisão

Os testes de precisão foram renomeados para avaliações porque o novo nome descreve melhor o que eles representam. Os novos caminhos são: `https://{region}.api.cognitive.microsoft.com/speechtotext/v3.0/evaluations` .

>[!IMPORTANT]
>Renomeie o segmento de caminho `accuracytests` para `evaluations` no seu código de cliente.


## <a name="next-steps"></a>Próximas etapas

Examine todos os recursos dessas APIs REST comumente usadas fornecidas pelos serviços de fala:

* [API REST de conversão de fala em texto](rest-speech-to-text.md)
* [Documento do Swagger](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0) para V3 da API REST
* Para obter um exemplo de código para executar transcrições em lote, exiba o [repositório de exemplo do GitHub](https://aka.ms/csspeech/samples) no `samples/batch` subdiretório.
