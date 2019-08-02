---
title: Entidades de correspondência de texto extact-LUIS
titleSuffix: Azure Cognitive Services
description: Saiba como adicionar uma entidade de lista para ajudar LUIS a rotular variações de uma palavra ou frase.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: da6e8e794520a737a96ba45f94459fb8c1a5ff18
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68637839"
---
# <a name="use-a-list-entity-to-increase-entity-detection"></a>Usar uma entidade de lista para aumentar a detecção de entidade 
Este tutorial demonstra o uso de uma [lista entidade](luis-concept-entity-types.md) para aumentar a detecção de entidade. Entidades de lista não precisam ser rotuladas já que elas são uma correspondência exata de termos.  

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma entidade de listas 
> * Adicionar valores normalizados e sinônimos
> * Validar a identificação aprimorada da entidade

## <a name="prerequisites"></a>Pré-requisitos

> [!div class="checklist"]
> * [Node.js](https://nodejs.org) mais recente
> * [Aplicativo LUIS de HomeAutomation](luis-get-started-create-app.md). Se você não tiver o aplicativo Home Automation criado, crie um novo aplicativo, e adicione o domínio pré-compilado **HomeAutomation**. Treine e publique o aplicativo. 
> * [AuthoringKey](luis-concept-keys.md#authoring-key), [EndpointKey](luis-concept-keys.md#endpoint-key) (se estiver consultando várias vezes), ID do aplicativo, ID de versão e [região](luis-reference-regions.md) para o aplicativo LUIS.

> [!Tip]
> Se você ainda não tiver uma assinatura, poderá se inscrever para uma [conta gratuita](https://azure.microsoft.com/free/).

A íntegra do código neste tutorial está disponível no [repositório do GitHub de Exemplos do Azure](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/tutorial-list-entity). 

## <a name="use-homeautomation-app"></a>Usar o aplicativo HomeAutomation
O aplicativo HomeAutomation oferece controle de dispositivos como luzes, sistemas de entretenimento e controles de ambiente como aquecimento e refrigeração. Esses sistemas possuem vários nomes diferentes que podem incluir nomes de fabricantes, apelidos, acrônimos e gírias. 

Um sistema que possui muitos nomes em diferentes culturas e regiões é o termostato. Um termostato pode controlar os sistemas de aquecimento e refrigeração para uma casa ou edifício.

Idealmente as expressões a seguir resolverão para a entidade pré-compilada **HomeAutomation.Device**:

|#|enunciado|entidade identificada|para seu app&#39;s|
|--|--|--|--|
|1|ligue o ac|HomeAutomation.Device - "ac"|0.8748562|
|2|ativar o aquecimento|HomeAutomation.Device - "aquecimento"|0.784990132|
|3|mais frio|||

As duas primeiras expressões são mapeadas para diferentes dispositivos. A terceira expressão, "mais frio", não está mapeada para um dispositivo, mas em vez disso, solicita um resultado. LUIS não sabe que o termo "mais frio" significa que o termostato é o dispositivo solicitado. Idealmente, LUIS deve resolver todas essas expressões no mesmo dispositivo. 

## <a name="use-a-list-entity"></a>Usar uma entidade de listas
A entidade HomeAutomation.Device é ótima para um pequeno número de dispositivos ou com algumas variações dos nomes. Para um edifício comercial ou campus, os nomes de dispositivo ultrapassam a utilidade da entidade HomeAutomation.Device. 

Uma **entidade de listas** é uma boa opção para esse cenário porque o conjunto de termos para um dispositivo em um edifício ou campus é um conjunto conhecido, mesmo se ele é um conjunto grande. Usando uma entidade de lista, LUIS pode receber qualquer valor possível no conjunto para o termostato e resolvê-lo apenas para o dispositivo "termostato". 

Este tutorial vai criar uma lista de entidades com o termostato. Os nomes alternativos para um termostato neste tutorial são: 

|nomes alternativos para termostato|
|--|
| ac |
| a/c|
| a-c|
|aquecedor|
|calor|
|mais quente|
|frio|
|mais frio|

Se LUIS precisa determinar uma nova alternativa muitas vezes, uma [lista de frases](luis-concept-feature.md#how-to-use-phrase-lists) é a melhor resposta.

## <a name="create-a-list-entity"></a>Criar uma entidade de listas
Crie um arquivo de Node. js e copie o seguinte código nele. Altere os valores authoringKey, appId, versionId e região.

   [!code-javascript[Create DevicesList List Entity](~/samples-luis/documentation-samples/tutorial-list-entity/add-entity-list.js "Create DevicesList List Entity")]

Use o seguinte comando para instalar as dependências NPM e executar o código para criar a entidade de lista:

```console
npm install && node add-entity-list.js
```

O resultado da execução é a ID da entidade de listas:

```console
026e92b3-4834-484f-8608-6114a83b03a6
```

## <a name="train-the-model"></a>Treinar o modelo
Treinar LUIS para que a nova lista afete os resultados da consulta. O treinamento é um processo de treinamento de duas partes, verificando o status se o treinamento foi feito. Um aplicativo com muitos modelos pode levar alguns minutos para treinar. O seguinte código treina o aplicativo e aguarda até que o treinamento seja bem-sucedido. O código usa uma estratégia de repetição e espera para evitar o erro 429 "excesso de solicitações". 

Crie um arquivo de Node. js e copie o seguinte código nele. Altere os valores authoringKey, appId, versionId e região.

   [!code-javascript[Train LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/train.js "Train LUIS")]

Use o comando a seguir para executar o código para treinar o aplicativo:

```console
node train.js
```

A saída da execução é o status de cada iteração do treinamento dos modelos LUIS. A seguinte execução necessitou apenas de uma verificação de treinamento:

```console
1 trained = true
[ { modelId: '2c549f95-867a-4189-9c35-44b95c78b70f',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } },
  { modelId: '5530e900-571d-40ec-9c78-63e66b50c7d4',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } },
  { modelId: '519faa39-ae1a-4d98-965c-abff6f743fe6',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } },
  { modelId: '9671a485-36a9-46d5-aacd-b16d05115415',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } },
  { modelId: '9ef7d891-54ab-48bf-8112-c34dcd75d5e2',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } },
  { modelId: '8e16a660-8781-4abf-bf3d-f296ebe1bf2d',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } } ]

```
## <a name="publish-the-model"></a>Publicar o modelo
Publique para que a entidade de lista esteja disponível no ponto de extremidade.

Crie um arquivo de Node. js e copie o seguinte código nele. Altere os valores endpointKey, appId e região. Você pode usar o authoringKey se você não planeja chamar esse arquivo além do seu limite de cota.

   [!code-javascript[Publish LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/publish.js "Publish LUIS")]

Use o comando a seguir para executar o código para consultar o aplicativo:

```console
node publish.js
```

A saída a seguir inclui a url de ponto de extremidade de todas as consultas. Os resultados JSON reais incluiriam a appID real. 

```json
{ 
  versionId: null,
  isStaging: false,
  endpointUrl: 'https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>',
  region: null,
  assignedEndpointKey: null,
  endpointRegion: 'westus',
  publishedDateTime: '2018-01-29T22:17:38Z' }
}
```

## <a name="query-the-app"></a>Consulte o aplicativo 
Consulte o aplicativo no ponto de extremidade para comprovar que a entidade de lista ajuda LUIS a determinar o tipo de dispositivo.

Crie um arquivo de Node. js e copie o seguinte código nele. Altere os valores endpointKey, appId e região. Você pode usar o authoringKey se você não planeja chamar esse arquivo além do seu limite de cota.

   [!code-javascript[Query LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/query.js "Query LUIS")]

Use o comando a seguir para executar o código e consultar o aplicativo:

```console
node train.js
```

O resultado são os resultados da consulta. Como o código adicionou o par nome/valor **detalhado** para a cadeia de caracteres de consulta, a saída inclui todas as intenções e suas classificações:

```json
{
  "query": "turn up the heat",
  "topScoringIntent": {
    "intent": "HomeAutomation.TurnOn",
    "score": 0.139018849
  },
  "intents": [
    {
      "intent": "HomeAutomation.TurnOn",
      "score": 0.139018849
    },
    {
      "intent": "None",
      "score": 0.120624863
    },
    {
      "intent": "HomeAutomation.TurnOff",
      "score": 0.06746891
    }
  ],
  "entities": [
    {
      "entity": "heat",
      "type": "HomeAutomation.Device",
      "startIndex": 12,
      "endIndex": 15,
      "score": 0.784990132
    },
    {
      "entity": "heat",
      "type": "DevicesList",
      "startIndex": 12,
      "endIndex": 15,
      "resolution": {
        "values": [
          "Thermostat"
        ]
      }
    }
  ]
}
```

O dispositivo específico de **termostato** é identificado com uma consulta orientada por resultado de "ativar o aquecimento". Como a entidade HomeAutomation.Device original ainda está no aplicativo, você pode ver os seus resultados também. 

Tente as outras duas expressões para ver o que eles também retornaram como termostato. 

|#|enunciado|entidade|type|value|
|--|--|--|--|--|
|1|ligue o ac| ac | DevicesList | Termostato|
|2|ativar o aquecimento|calor| DevicesList |Termostato|
|3|mais frio|mais frio|DevicesList|Termostato|

## <a name="next-steps"></a>Próximas etapas

Você pode criar outra entidade de lista para expandir os locais de dispositivo para salas, andares ou edifícios. 
