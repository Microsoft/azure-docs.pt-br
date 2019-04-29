---
title: Como criar funções definidas pelo usuário em Gêmeos Digitais do Azure | Microsoft Docs
description: Como criar funções definidas pelo usuário, correspondentes e atribuições de função com os Gêmeos Digitais do Azure.
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: alinast
ms.custom: seodec18
ms.openlocfilehash: 7208f96d99127247b51510e0c43c1733bb327dfb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60921800"
---
# <a name="how-to-create-user-defined-functions-in-azure-digital-twins"></a>Como criar funções definidas pelo usuário em Gêmeos Digitais do Azure

As [funções definidas pelo usuário](./concepts-user-defined-functions.md) permitem que o usuário execute uma lógica personalizada em relação às mensagens de telemetria recebidas e aos metadados de grafo espacial. Os usuários também podem enviar eventos para [pontos de extremidade](./how-to-egress-endpoints.md) pré-definidos.

Este guia apresenta um exemplo que demonstra como detectar e alertar sobre qualquer leitura que exceda uma determinada temperatura dos eventos de temperatura recebidos.

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

## <a name="client-library-reference"></a>Referência da biblioteca de clientes

As funções disponíveis como métodos auxiliares no tempo de execução de funções definidas pelo usuário são listadas no documento de [referência da biblioteca de clientes](./reference-user-defined-functions-client-library.md).

## <a name="create-a-matcher"></a>Criar um correspondente

Correspondentes são objetos de grafo que determinam quais funções definidas pelo usuário são executadas para uma determinada mensagem de telemetria.

- Comparações de condição de correspondente válidas:

  - `Equals`
  - `NotEquals`
  - `Contains`

- Destinos de condição de correspondente válidos:

  - `Sensor`
  - `SensorDevice`
  - `SensorSpace`

O correspondente de exemplo a seguir é avaliado como verdadeiro em qualquer evento de telemetria do sensor `"Temperature"` como valor de tipo de dados. Você pode criar vários correspondentes em uma função definida pelo usuário fazendo uma solicitação HTTP POST autenticada para:

```plaintext
YOUR_MANAGEMENT_API_URL/matchers
```

Com o corpo JSON:

```JSON
{
  "Name": "Temperature Matcher",
  "Conditions": [
    {
      "target": "Sensor",
      "path": "$.dataType",
      "value": "\"Temperature\"",
      "comparison": "Equals"
    }
  ],
  "SpaceId": "YOUR_SPACE_IDENTIFIER"
}
```

| Value | Substitua por |
| --- | --- |
| YOUR_SPACE_IDENTIFIER | Em qual região do servidor de sua instância está hospedada |

## <a name="create-a-user-defined-function"></a>Criar uma função definida pelo usuário

A criação de uma função definida pelo usuário envolve fazer uma solicitação HTTP com várias partes para as APIs de Gerenciamento dos Gêmeos Digitais do Azure.

[!INCLUDE [Digital Twins multipart requests](../../includes/digital-twins-multipart.md)]

Depois que os correspondentes forem criados, carregue o trecho de código de função com a seguinte solicitação HTTP POST de várias partes autenticada:

```plaintext
YOUR_MANAGEMENT_API_URL/userdefinedfunctions
```

Use o seguinte corpo:

```plaintext
--USER_DEFINED_BOUNDARY
Content-Type: application/json; charset=utf-8
Content-Disposition: form-data; name="metadata"

{
  "spaceId": "YOUR_SPACE_IDENTIFIER",
  "name": "User Defined Function",
  "description": "The contents of this udf will be executed when matched against incoming telemetry.",
  "matchers": ["YOUR_MATCHER_IDENTIFIER"]
}
--USER_DEFINED_BOUNDARY
Content-Disposition: form-data; name="contents"; filename="userDefinedFunction.js"
Content-Type: text/javascript

function process(telemetry, executionContext) {
  // Code goes here.
}

--USER_DEFINED_BOUNDARY--
```

| Value | Substitua por |
| --- | --- |
| USER_DEFINED_BOUNDARY | Um nome de limite de conteúdo com diversas partes |
| YOUR_SPACE_IDENTIFIER | O identificador de espaço  |
| YOUR_MATCHER_IDENTIFIER | A ID do correspondente que você quer usar |

1. Verifique se os cabeçalhos incluem: `Content-Type: multipart/form-data; boundary="USER_DEFINED_BOUNDARY"`.
1. Verifique se o corpo tem diversas partes:

   - A primeira parte contém os metadados de função definida pelo usuário necessários.
   - A segunda parte contém a lógica de computação do JavaScript.

1. Na seção **USER_DEFINED_BOUNDARY**, substitua os valores de **spaceId** (`YOUR_SPACE_IDENTIFIER`) e **matchers**(`YOUR_MATCHER_IDENTIFIER`).
1. Verifique se a função definida pelo usuário do JavaScript é fornecida como `Content-Type: text/javascript`.

### <a name="example-functions"></a>Funções de exemplo

Defina a leitura da telemetria do sensor diretamente para o sensor com o tipo de dados **Temperatura**, que é `sensor.DataType`:

```JavaScript
function process(telemetry, executionContext) {

  // Get sensor metadata
  var sensor = getSensorMetadata(telemetry.SensorId);

  // Retrieve the sensor value
  var parseReading = JSON.parse(telemetry.Message);

  // Set the sensor reading as the current value for the sensor.
  setSensorValue(telemetry.SensorId, sensor.DataType, parseReading.SensorValue);
}
```

O parâmetro de **telemetria** expõe os atributos **SensorId** e **Message**, correspondendo a uma mensagem enviada por um sensor. O parâmetro **executionContext** expõe os seguintes atributos:

```csharp
var executionContext = new UdfExecutionContext
{
    EnqueuedTime = request.HubEnqueuedTime,
    ProcessorReceivedTime = request.ProcessorReceivedTime,
    UserDefinedFunctionId = request.UserDefinedFunctionId,
    CorrelationId = correlationId.ToString(),
};
```

No próximo exemplo, registramos uma mensagem se a leitura da telemetria do sensor ultrapassar um limite predefinido. Se as configurações de diagnóstico estiverem habilitadas na instância dos Gêmeos Digitais do Azures, os logs das funções definidas pelo usuário também serão encaminhados:

```JavaScript
function process(telemetry, executionContext) {

  // Retrieve the sensor value
  var parseReading = JSON.parse(telemetry.Message);

  // Example sensor telemetry reading range is greater than 0.5
  if(parseFloat(parseReading.SensorValue) > 0.5) {
    log(`Alert: Sensor with ID: ${telemetry.SensorId} detected an anomaly!`);
  }
}
```

O código a seguir disparará uma notificação se o nível de temperatura ultrapassar a constante predefinida:

```JavaScript
function process(telemetry, executionContext) {

  // Retrieve the sensor value
  var parseReading = JSON.parse(telemetry.Message);

  // Define threshold
  var threshold = 70;

  // Trigger notification 
  if(parseInt(parseReading) > threshold) {
    var alert = {
      message: 'Temperature reading has surpassed threshold',
      sensorId: telemetry.SensorId,
      reading: parseReading
    };

    sendNotification(telemetry.SensorId, "Sensor", JSON.stringify(alert));
  }
}
```

Para obter um exemplo de código de função mais complexo definido pelo usuário, consulte o [Início rápido de ocupação](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availability.js).

## <a name="create-a-role-assignment"></a>Criar uma atribuição de função

Crie uma atribuição de função para que a função definida pelo usuário seja executada. Se não houver nenhuma atribuição de função para a função definida pelo usuário, ela não terá as permissões corretas para interagir com a API de Gerenciamento ou para ter acesso para executar ações nos objetos de grafo. As ações que a função definida pelo usuário pode executar são especificadas por meio do controle de acesso baseado em função nas APIs do Gerenciamento de Gêmeos Digitais do Azure. Por exemplo, funções definidas pelo usuário podem ser limitadas no escopo, especificando determinadas funções ou certos caminhos de controle de acesso. Para obter mais informações, consulte a documentação de [controle de acesso baseado em função](./security-role-based-access-control.md).

1. [Consulte a API do sistema](./security-create-manage-role-assignments.md#all) de todas as funções para obter a ID da função que você deseja atribuir à sua função definida pelo usuário. Faça isso, executando uma solicitação HTTP GET autenticada para:

    ```plaintext
    YOUR_MANAGEMENT_API_URL/system/roles
    ```
   Guarde a ID da função desejada. Ela será passada como o atributo do corpo JSON **roleId** (`YOUR_DESIRED_ROLE_IDENTIFIER`) abaixo.

1. O **objectId** (`YOUR_USER_DEFINED_FUNCTION_ID`) será a ID da função definida pelo usuário criada anteriormente.
1. Localize o valor do **caminho** (`YOUR_ACCESS_CONTROL_PATH`), consultando os espaços com `fullpath`.
1. Copie o valor `spacePaths` retornado. Esse valor será utilizado posteriormente. Faça uma solicitação HTTP GET autenticada para:

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?name=YOUR_SPACE_NAME&includes=fullpath
    ```

    | Value | Substitua por |
    | --- | --- |
    | YOUR_SPACE_NAME | O nome do espaço que você deseja usar |

1. Cole o valor `spacePaths` retornado no **caminho** para criar uma atribuição de função definida pelo usuário, fazendo uma solicitação HTTP POST autenticada para:

    ```plaintext
    YOUR_MANAGEMENT_API_URL/roleassignments
    ```
    Com o corpo JSON:

    ```JSON
    {
      "roleId": "YOUR_DESIRED_ROLE_IDENTIFIER",
      "objectId": "YOUR_USER_DEFINED_FUNCTION_ID",
      "objectIdType": "YOUR_USER_DEFINED_FUNCTION_TYPE_ID",
      "path": "YOUR_ACCESS_CONTROL_PATH"
    }
    ```

    | Value | Substitua por |
    | --- | --- |
    | YOUR_DESIRED_ROLE_IDENTIFIER | O identificador para a função desejada |
    | YOUR_USER_DEFINED_FUNCTION_ID | A ID da função definida pelo usuário que você deseja usar |
    | YOUR_USER_DEFINED_FUNCTION_TYPE_ID | A ID especificando o tipo de função definida pelo usuário |
    | YOUR_ACCESS_CONTROL_PATH | O caminho de controle de acesso |

>[!TIP]
> Leia o artigo [Como criar e gerenciar atribuições de função](./security-create-manage-role-assignments.md) para obter mais informações sobre operações e pontos de extremidade da API de Gerenciamento de funções definidas pelo usuário.

## <a name="send-telemetry-to-be-processed"></a>Enviar telemetria para ser processada

O sensor definido no gráfico de inteligência espacial envia telemetria. Por sua vez, a telemetria dispara a execução da função definida pelo usuário que foi carregada. O processador de dados pega a telemetria. Em seguida, um plano de execução é criado para a invocação da função definida pelo usuário.

1. Recupere os correspondentes para o sensor de onde a leitura foi gerada.
1. Dependendo de quais correspondentes foram avaliados com êxito, recupere as funções definidas pelo usuário associadas.
1. Execute cada função definida pelo usuário.

## <a name="next-steps"></a>Próximas etapas

- Saiba com [criar pontos de extremidade de Gêmeos Digitais do Azure ](./how-to-egress-endpoints.md) para envio de eventos.

- Para obter mais detalhes sobre o roteamento em Gêmeos Digitais do Azure, leia [Roteamento de eventos e mensagens](./concepts-events-routing.md).

- Revise a documentação de [referência da biblioteca de clientes](./reference-user-defined-functions-client-library.md).
