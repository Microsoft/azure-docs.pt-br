---
title: Gêmeos digitais e o grafo de gêmeos
titleSuffix: Azure Digital Twins
description: Entenda o conceito de uma telegrafia digital e como suas relações fazem um gráfico.
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 00058f75a2c4378371c427ff9ebabe7e2336b06a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99576541"
---
# <a name="understand-digital-twins-and-their-twin-graph"></a>Entenda o gêmeos digital e seu gráfico de entrelaçamento

Em uma solução de gêmeos digital do Azure, as entidades em seu ambiente são representadas por **gêmeos digital**. Uma cópia digital é uma instância de um dos seus [modelos](concepts-models.md)definidos pelo personalizado. Ele pode ser conectado a outras gêmeos digitais por meio de **relações** para formar um **grafo de entrelaçamento**: este grafo de Altova é a representação de todo o seu ambiente.

> [!TIP]
> "Azure digital gêmeos" refere-se a esse serviço do Azure como um todo. "Cópia digital (ões)" ou apenas ""/s "" refere-se a nós de cópia individual na sua instância do serviço.

## <a name="digital-twins"></a>Gêmeos Digitais

Antes de criar uma cópia digital em sua instância do gêmeos digital do Azure, você precisa ter um *modelo* carregado para o serviço. Um modelo descreve o conjunto de propriedades, mensagens de telemetria e relações que uma mensagem de grupo pode ter em particular, entre outras coisas. Para os tipos de informações que são definidas em um modelo, consulte [*conceitos: modelos personalizados*](concepts-models.md).

Depois de criar e carregar um modelo, seu aplicativo cliente pode criar uma instância do tipo; Este é um teledigital. Por exemplo, depois de criar um modelo de *andar*, você pode criar um ou vários gêmeos digitais que usam esse tipo (como um tipo de *chão* de texto chamado *GroundFloor*, outro chamado *Floor2*, etc.).

[!INCLUDE [digital-twins-versus-device-twins](../../includes/digital-twins-versus-device-twins.md)]

## <a name="relationships-a-graph-of-digital-twins"></a>Relações: um grafo de gêmeos digital

Gêmeos são conectados a um grafo de entrelaçamento por suas relações. As relações que um papel de entrelaçar pode ter são definidas como parte de seu modelo.  

Por exemplo, a *base* do modelo pode definir uma relação *Contains que tem* como alvo gêmeos do tipo *Room*. Com essa definição, o gêmeos digital do Azure permitirá que você crie *conterá* relações de qualquer *andar* de cima para qualquer *sala* (incluindo gêmeos que são subtipos de *espaço* ). 

O resultado desse processo é um conjunto de nós (o gêmeos digital) conectado por meio de bordas (suas relações) em um grafo.

[!INCLUDE [visualizing with Azure Digital Twins explorer](../../includes/digital-twins-visualization.md)]

## <a name="create-with-the-apis"></a>Criar com as APIs

Esta seção mostra o que parece criar gêmeos digital e relações de um aplicativo cliente. Ele contém exemplos de código .NET que utilizam as [APIs DigitalTwins](/rest/api/digital-twins/dataplane/twins), para fornecer contexto adicional sobre o que acontece dentro de cada um desses conceitos.

### <a name="create-digital-twins"></a>Criar gêmeos digitais

Abaixo está um trecho de código de cliente que usa as [APIs DigitalTwins](/rest/api/digital-twins/dataplane/twins) para criar uma instância de um tipo de *espaço*.

Você pode inicializar as propriedades de um conjunto de entrelaçamento quando ele é criado ou defini-los mais tarde. Para criar um entrelaçar com propriedades inicializadas, crie um documento JSON que forneça os valores de inicialização necessários.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="CreateTwin_noHelper":::

Você também pode usar uma classe auxiliar chamada `BasicDigitalTwin` para armazenar campos de propriedade em um objeto "" "" e mais diretamente, como uma alternativa ao uso de um dicionário. Para obter mais informações sobre a classe auxiliar e exemplos de seu uso, consulte a seção [*criar uma*](how-to-manage-twin.md#create-a-digital-twin) altas digitais de *como: gerenciar digital gêmeos*.

>[!NOTE]
>Embora as propriedades de entrelaçamento sejam tratadas como opcionais e, portanto, não precisem ser inicializadas, todos os [componentes](concepts-models.md#elements-of-a-model) em FileUp precisam ser definidos quando o conjunto **de entrelaçar** é criado. Eles podem ser objetos vazios, mas os próprios componentes devem existir.

### <a name="create-relationships"></a>Criar relações

Aqui está um código de cliente de exemplo que usa as [APIs DigitalTwins](/rest/api/digital-twins/dataplane/twins) para criar uma relação de um "digital" (a "origem") para outra ("destino").

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_other.cs" id="CreateRelationship_short":::

## <a name="json-representations-of-graph-elements"></a>Representações JSON de elementos de grafo

Dados de relações digitais e dados de relação são armazenados no formato JSON. Isso significa que, quando você [consulta o grafo de entrelaçamento](how-to-query-graph.md) em sua instância do gêmeos digital do Azure, o resultado será uma representação JSON do gêmeos digital e das relações que você criou.

### <a name="digital-twin-json-format"></a>Formato JSON digital de entrelaçamento

Quando representado como um objeto JSON, uma pesquisa digital exibirá os seguintes campos:

| Nome do campo | Descrição |
| --- | --- |
| `$dtId` | Uma cadeia de caracteres fornecida pelo usuário que representa a ID do digital de texto |
| `$etag` | Campo HTTP padrão atribuído pelo servidor Web |
| `$conformance` | Um enum que contém o status de conformidade desta teledigital *(compatível*, *não* compatível, *desconhecido*) |
| `{propertyName}` | O valor de uma propriedade em JSON ( `string` , tipo de número ou objeto) |
| `$relationships` | A URL do caminho para a coleção de relações. Esse campo estará ausente se o digital up não tiver nenhuma borda de relação de saída. |
| `$metadata.$model` | Adicional A ID da interface do modelo que caracteriza este teledigital |
| `$metadata.{propertyName}.desiredValue` | [Somente para propriedades graváveis] O valor desejado da propriedade especificada |
| `$metadata.{propertyName}.desiredVersion` | [Somente para propriedades graváveis] A versão do valor desejado |
| `$metadata.{propertyName}.ackVersion` | A versão confirmada pelo aplicativo de dispositivo que está implementando o teledigital |
| `$metadata.{propertyName}.ackCode` | [Somente para propriedades graváveis] O `ack` código retornado pelo aplicativo de dispositivo que implementa o teledigital |
| `$metadata.{propertyName}.ackDescription` | [Somente para propriedades graváveis] A `ack` Descrição retornada pelo aplicativo de dispositivo que implementa o teledigital |
| `{componentName}` | Um objeto JSON que contém os valores de propriedade e metadados do componente, semelhantes aos do objeto raiz. Esse objeto existe mesmo que o componente não tenha nenhuma propriedade. |
| `{componentName}.{propertyName}` | O valor da Propriedade do componente em JSON ( `string` , tipo de número ou objeto) |
| `{componentName}.$metadata` | As informações de metadados para o componente, semelhante ao nível raiz `$metadata` |

Aqui está um exemplo de um filetreme digital formatado como um objeto JSON:

```json
{
  "$dtId": "Cafe",
  "$etag": "W/\"e59ce8f5-03c0-4356-aea9-249ecbdc07f9\"",
  "Temperature": 72,
  "Location": {
    "x": 101,
    "y": 33
  },
  "component": {
    "TableOccupancy": 1,
    "$metadata": {
      "TableOccupancy": {
        "desiredValue": 1,
        "desiredVersion": 3,
        "ackVersion": 2,
        "ackCode": 200,
        "ackDescription": "OK"
      }
    }
  },
  "$metadata": {
    "$model": "dtmi:com:contoso:Room;1",
    "Temperature": {
      "desiredValue": 72,
      "desiredVersion": 5,
      "ackVersion": 4,
      "ackCode": 200,
      "ackDescription": "OK"
    },
    "Location": {
      "desiredValue": {
        "x": 101,
        "y": 33,
      },
      "desiredVersion": 8,
      "ackVersion": 8,
      "ackCode": 200,
      "ackDescription": "OK"
    }
  }
}
```

### <a name="relationship-json-format"></a>Formato JSON de relação

Quando representado como um objeto JSON, uma relação de uma pesquisa digital exibirá os seguintes campos:

| Nome do campo | Descrição |
| --- | --- |
| `$relationshipId` | Uma cadeia de caracteres fornecida pelo usuário que representa a ID dessa relação. Essa cadeia de caracteres é exclusiva no contexto da fonte de texto digital de origem, o que também significa que `sourceId`  +  `relationshipId` é exclusivo no contexto da instância do gêmeos digital do Azure. |
| `$etag` | Campo HTTP padrão atribuído pelo servidor Web |
| `$sourceId` | A ID da fonte de los digitais |
| `$targetId` | A ID do teledigital de destino |
| `$relationshipName` | O nome da relação |
| `{propertyName}` | Adicional O valor de uma propriedade dessa relação, em JSON ( `string` , tipo de número ou objeto) |

Aqui está um exemplo de uma relação formatada como um objeto JSON:

```json
{
  "$relationshipId": "relationship-01",
  "$etag": "W/\"506e8391-2b21-4ac9-bca3-53e6620f6a90\"",
  "$sourceId": "GroundFloor",
  "$targetId": "Cafe",
  "$relationshipName": "contains",
  "startDate": "2020-02-04"
}
```

## <a name="next-steps"></a>Próximas etapas

Consulte como gerenciar elementos de grafo com as APIs de teledigital do Azure:
* [*Como: gerenciar gêmeos digitais*](how-to-manage-twin.md)
* [*Como: gerenciar o grafo de entrelaçamento com relações*](how-to-manage-graph.md)

Ou então, saiba como consultar o grafo gêmeos do Azure digital para obter informações:
* [*Conceitos: linguagem de consulta*](concepts-query-language.md)