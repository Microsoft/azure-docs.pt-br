---
title: Gerenciar gêmeos digitais
titleSuffix: Azure Digital Twins
description: Consulte como recuperar, atualizar e excluir gêmeos e relações individuais.
author: baanders
ms.author: baanders
ms.date: 10/21/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 666e77a06bd2934622400cc2f11830d6ebc34ddb
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "104954642"
---
# <a name="manage-digital-twins"></a>Gerenciar gêmeos digitais

As entidades em seu ambiente são representadas por [gêmeos digital](concepts-twins-graph.md). Gerenciar seu gêmeos digital pode incluir criação, modificação e remoção. Para executar essas operações, você pode usar as [**APIs do DigitalTwins**](/rest/api/digital-twins/dataplane/twins), o [SDK do .net (C#)](/dotnet/api/overview/azure/digitaltwins/client)ou a [CLI do gêmeos digital do Azure](how-to-use-cli.md).

Este artigo se concentra no gerenciamento de gêmeos digitais; para trabalhar com relações e o [gráfico de bispersão](concepts-twins-graph.md) como um todo, confira [*como gerenciar o grafo de entrelaçamento com relações*](how-to-manage-graph.md).

> [!TIP]
> Todas as funções do SDK são fornecidas em versões síncronas e assíncronas.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

## <a name="ways-to-manage-twins"></a>Maneiras de gerenciar gêmeos

[!INCLUDE [digital-twins-ways-to-manage.md](../../includes/digital-twins-ways-to-manage.md)]

## <a name="create-a-digital-twin"></a>Criar um teledigital

Para criar um entrelaçamento, use o `CreateOrReplaceDigitalTwinAsync()` método no cliente de serviço da seguinte maneira:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="CreateTwinCall":::

Para criar um teledigital, você precisa fornecer:
* A ID desejada para o teledigital
* O [modelo](concepts-models.md) que você deseja usar

Opcionalmente, você pode fornecer valores iniciais para todas as propriedades da digital. As propriedades são tratadas como opcionais e podem ser definidas posteriormente, mas **não aparecerão como parte de um vertical até que tenham sido definidas.**

>[!NOTE]
>Embora as propriedades de biquery **não precisem** ser inicializadas, todos os [componentes](concepts-models.md#elements-of-a-model) em FileUp precisam ser definidos quando o conjunto de entrelaçamento é criado. Eles podem ser objetos vazios, mas os próprios componentes devem existir.

O modelo e todos os valores de propriedade inicial são fornecidos por meio do `initData` parâmetro, que é uma cadeia de caracteres JSON que contém os dados relevantes. Para obter mais informações sobre como estruturar esse objeto, vá para a próxima seção.

> [!TIP]
> Depois de criar ou atualizar um vertical, pode haver uma latência de até 10 segundos antes que as alterações sejam refletidas nas [consultas](how-to-query-graph.md). A `GetDigitalTwin` API (descrita [mais adiante neste artigo](#get-data-for-a-digital-twin)) não experimenta esse atraso, portanto, se você precisar de uma resposta instantânea, use a chamada à API em vez de consultar para ver seu gêmeos recém-criado. 

### <a name="initialize-model-and-properties"></a>Inicializar modelo e propriedades

Você pode inicializar as propriedades de um entrelaçamento no momento em que o entrelaçamento é criado. 

A API de criação de entrelaçamento aceita um objeto que é serializado em uma descrição JSON válida das propriedades de entrelaçamento. Consulte [*Concepts: digital gêmeos e o gráfico de bispersão*](concepts-twins-graph.md) para obter uma descrição do formato JSON para um "r". 

Primeiro, você pode criar um objeto de dados para representar os dados de propriedade e de propriedades. Você pode criar um objeto de parâmetro manualmente ou usando uma classe auxiliar fornecida. Aqui está um exemplo de cada um.

#### <a name="create-twins-using-manually-created-data"></a>Criar gêmeos usando dados criados manualmente

Sem o uso de classes auxiliares personalizadas, você pode representar as propriedades de uma folha em um `Dictionary<string, object>` , em que `string` é o nome da propriedade e `object` é um objeto que representa a propriedade e seu valor.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="CreateTwin_noHelper":::

#### <a name="create-twins-with-the-helper-class"></a>Criar gêmeos com a classe auxiliar

A classe auxiliar do `BasicDigitalTwin` permite que você armazene campos de propriedade em um objeto "" ". Talvez você ainda queira criar a lista de propriedades usando um `Dictionary<string, object>` , que pode então ser adicionado ao objeto de entrelaçamento `CustomProperties` diretamente.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="CreateTwin_withHelper":::

>[!NOTE]
> `BasicDigitalTwin` os objetos vêm com um `Id` campo. Você pode deixar esse campo vazio, mas se você adicionar um valor de ID, ele precisará corresponder ao parâmetro de ID passado para a `CreateOrReplaceDigitalTwinAsync()` chamada. Por exemplo:
>
>```csharp
>twin.Id = "myRoomId";
>```

## <a name="get-data-for-a-digital-twin"></a>Obter dados de uma teledigital

Você pode acessar os detalhes de qualquer tipo de informações digitais chamando o `GetDigitalTwin()` método como este:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="GetTwinCall":::

Essa chamada retorna dados de texto cruzado como um tipo de objeto fortemente tipado, como `BasicDigitalTwin` . `BasicDigitalTwin` é uma classe auxiliar de serialização incluída no SDK, que retornará os metadados e as propriedades principais de entrelaçamento no formulário previamente analisado. Aqui está um exemplo de como usar isso para exibir os detalhes de entrelaçamento:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="GetTwin" highlight="2":::

Somente as propriedades que foram definidas pelo menos uma vez são retornadas quando você recupera um conjunto de entrelaçar com o `GetDigitalTwin()` método.

>[!TIP]
>O `displayName` para um papel de cópia é parte de seus metadados de modelo, portanto, ele não será exibido ao obter dados para a instância de a. Para ver esse valor, você pode [recuperá-lo a partir do modelo](how-to-manage-model.md#retrieve-models).

Para recuperar vários gêmeos usando uma única chamada à API, consulte os exemplos de API de consulta em [*instruções: consultar o gráfico de entrelaçamento*](how-to-query-graph.md).

Considere o modelo a seguir (escrito na [DTDL (digital gêmeos Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl/tree/master/DTDL)) que define uma *lua*:

:::code language="json" source="~/digital-twins-docs-samples/models/Moon.json":::

O resultado da chamada `object result = await client.GetDigitalTwinAsync("my-moon");` em um tipo de *lua* de texto pode ser semelhante ao seguinte:

```json
{
  "$dtId": "myMoon-001",
  "$etag": "W/\"e59ce8f5-03c0-4356-aea9-249ecbdc07f9\"",
  "radius": 1737.1,
  "mass": 0.0734,
  "$metadata": {
    "$model": "dtmi:example:Moon;1",
    "radius": {
      "desiredValue": 1737.1,
      "desiredVersion": 5,
      "ackVersion": 4,
      "ackCode": 200,
      "ackDescription": "OK"
    },
    "mass": {
      "desiredValue": 0.0734,
      "desiredVersion": 8,
      "ackVersion": 8,
      "ackCode": 200,
      "ackDescription": "OK"
    }
  }
}
```

As propriedades definidas de 10 digitais são retornadas como propriedades de nível superior no digital. Metadados ou informações do sistema que não fazem parte da definição DTDL são retornados com um `$` prefixo. As propriedades de metadados incluem os seguintes valores:
* `$dtId`: A ID da cópia digital nesta instância de gêmeos digital do Azure
* `$etag`: Um campo HTTP padrão atribuído pelo servidor Web. Isso é atualizado para um novo valor sempre que a atualização de dados é atualizada, o que pode ser útil para determinar se o dado do entrelaçamento foi atualizado no servidor desde uma verificação anterior. Você pode usar `If-Match` para executar atualizações e exclusões que só são concluídas se a ETag da entidade corresponder à ETag fornecida. Para obter mais informações sobre essas operações, consulte a documentação para [DigitalTwins Update](/rest/api/digital-twins/dataplane/twins/digitaltwins_update) e [DigitalTwins Delete](/rest/api/digital-twins/dataplane/twins/digitaltwins_delete).
* `$metadata`: Um conjunto de outras propriedades, incluindo:
  - O DTMI do modelo do teledigital.
  - Status de sincronização para cada propriedade gravável. Isso é mais útil para dispositivos, em que é possível que o serviço e o dispositivo tenham status divergente (por exemplo, quando um dispositivo estiver offline). Atualmente, essa propriedade só se aplica a dispositivos físicos conectados ao Hub IoT. Com os dados na seção de metadados, é possível entender o status completo de uma propriedade, bem como os últimos carimbos de data/hora modificados. Para obter mais informações sobre o status de sincronização, consulte [este tutorial do Hub IOT](../iot-hub/tutorial-device-twins.md) sobre como sincronizar o estado do dispositivo.
  - Metadados específicos do serviço, como do Hub IoT ou do gêmeos digital do Azure. 

Você pode ler mais sobre as classes auxiliares de `BasicDigitalTwin` serialização [*, como em How-to: Use the Azure digital gêmeos APIs and SDKs*](how-to-use-apis-sdks.md).

## <a name="view-all-digital-twins"></a>Exibir todos os gêmeos digitais

Para exibir todas as gêmeos digitais em sua instância, use uma [consulta](how-to-query-graph.md). Você pode executar uma consulta com as [APIs de consulta](/rest/api/digital-twins/dataplane/query) ou os [comandos da CLI](how-to-use-cli.md).

Aqui está o corpo da consulta básica que retornará uma lista de todos os gêmeos digitais na instância:

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="GetAllTwins":::

## <a name="update-a-digital-twin"></a>Atualizar um gêmeo digital

Para atualizar as propriedades de uma troca digital, você escreve as informações que deseja substituir no formato de [patch JSON](http://jsonpatch.com/) . Dessa forma, você pode substituir várias propriedades de uma só vez. Em seguida, você passa o documento de patch JSON para um `UpdateDigitalTwin()` método:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="UpdateTwinCall":::

Uma chamada de patch pode atualizar tantas propriedades em um único número de atualizações que você deseja (até mesmo todas elas). Se precisar atualizar as propriedades em vários gêmeos, você precisará de uma chamada de atualização separada para cada um.

> [!TIP]
> Depois de criar ou atualizar um vertical, pode haver uma latência de até 10 segundos antes que as alterações sejam refletidas nas [consultas](how-to-query-graph.md). A `GetDigitalTwin` API (descrita [anteriormente neste artigo](#get-data-for-a-digital-twin)) não enfrenta esse atraso, portanto, use a chamada à API em vez de consultar para ver seu gêmeos atualizado recentemente se precisar de uma resposta instantânea. 

Aqui está um exemplo de código de patch JSON. Este documento substitui os valores de propriedade de *massa* e de *RADIUS* do teledigital em que é aplicado.

:::code language="json" source="~/digital-twins-docs-samples/models/patch.json":::

Você pode criar patches usando o [JsonPatchDocument](/dotnet/api/azure.jsonpatchdocument)do SDK do .net do Azure. Veja um exemplo.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="UpdateTwin":::

### <a name="update-properties-in-digital-twin-components"></a>Atualizar propriedades em componentes de atualização digitais

Lembre-se de que um modelo pode conter componentes, permitindo que ele seja composto por outros modelos. 

Para aplicar as propriedades de patch em um dos componentes digitais do r, você pode usar a sintaxe de caminho no patch JSON:

:::code language="json" source="~/digital-twins-docs-samples/models/patch-component.json":::

### <a name="update-a-digital-twins-model"></a>Atualizar um modelo de r. digital

A `UpdateDigitalTwin()` função também pode ser usada para migrar uma migração digital para um modelo diferente. 

Por exemplo, considere o seguinte documento de patch JSON que substitui o campo de metadados do digital bificado `$model` :

:::code language="json" source="~/digital-twins-docs-samples/models/patch-model-1.json":::

Esta operação só terá sucesso se o tipo de atualização digital estiver sendo modificado pelo patch estiver em conformidade com o novo modelo. 

Considere o seguinte exemplo:
1. Imagine um "up digital" com um modelo de *foo_old*. *foo_old* define uma propriedade necessária *em massa*.
2. O novo modelo *foo_new* define uma propriedade em massa e adiciona uma nova *temperatura* de propriedade necessária.
3. Após o patch, o FileUp digital deve ter uma propriedade Mass e de temperatura. 

O patch para essa situação precisa atualizar o modelo e a propriedade de temperatura de entrelaçamento, desta forma:

:::code language="json" source="~/digital-twins-docs-samples/models/patch-model-2.json":::

### <a name="handle-conflicting-update-calls"></a>Tratar chamadas de atualização conflitantes

O Azure digital gêmeos garante que todas as solicitações de entrada sejam processadas uma após a outra. Isso significa que, mesmo que várias funções tentem atualizar a mesma propriedade em um entrelaçamento ao mesmo tempo, não há **necessidade** de escrever código de bloqueio explícito para lidar com o conflito.

Esse comportamento está em uma base cada vez mais. 

Como exemplo, imagine um cenário no qual essas três chamadas chegam ao mesmo tempo: 
*   Gravar A propriedade A em *Twin1*
*   Gravar a propriedade B em *Twin1*
*   Gravar A propriedade A em *Twin2*

As duas chamadas que modificam *Twin1* são executadas uma após a outra e as mensagens de alteração são geradas para cada alteração. A chamada para Modify *Twin2* pode ser executada simultaneamente sem conflito, assim que ela chega.

## <a name="delete-a-digital-twin"></a>Excluir uma teledigital

Você pode excluir gêmeos usando o `DeleteDigitalTwin()` método. No entanto, você só pode excluir um "n" quando ele não tiver mais relações. Portanto, exclua as relações de entrada e saída do "s" primeiro.

Aqui está um exemplo do código para excluir gêmeos e suas relações. A `DeleteDigitalTwin` chamada do SDK é realçada para esclarecer onde ela cai no contexto de exemplo mais amplo.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="DeleteTwin" highlight="7":::

### <a name="delete-all-digital-twins"></a>Excluir todos os gêmeos digitais

Para obter um exemplo de como excluir todos os gêmeos de uma vez, baixe o aplicativo de exemplo usado no [*tutorial: explorar os conceitos básicos com um aplicativo cliente de exemplo*](tutorial-command-line-app.md). O arquivo *CommandLoop. cs* faz isso em uma `CommandDeleteAllTwins()` função.

## <a name="runnable-digital-twin-code-sample"></a>Exemplo de código de teledigital executável

Você pode usar o exemplo de código executável abaixo para criar um entrelaçar, atualizar seus detalhes e excluir o. 

### <a name="set-up-the-runnable-sample"></a>Configurar o exemplo executável

O trecho de código usa o [Room.jsna](https://github.com/Azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Room.json) definição do modelo do [*tutorial: explorar o gêmeos digital do Azure com um aplicativo cliente de exemplo*](tutorial-command-line-app.md). Você pode usar esse link para ir diretamente para o arquivo ou baixá-lo como parte do projeto de exemplo completo de ponta a ponta [aqui](/samples/azure-samples/digital-twins-samples/digital-twins-samples/).

Antes de executar o exemplo, faça o seguinte:
1. Baixe o arquivo de modelo, coloque-o em seu projeto e substitua o `<path-to>` espaço reservado no código abaixo para dizer ao seu programa onde encontrá-lo.
2. Substitua o espaço reservado `<your-instance-hostname>` pelo nome de host da instância do gêmeos digital do Azure.
3. Adicione duas dependências ao seu projeto que serão necessárias para trabalhar com o gêmeos digital do Azure. A primeira é o pacote do [SDK dos Gêmeos Digitais do Azure para .NET](/dotnet/api/overview/azure/digitaltwins/client), e a segunda fornece ferramentas para ajudar com a autenticação no Azure.

      ```cmd/sh
      dotnet add package Azure.DigitalTwins.Core
      dotnet add package Azure.Identity
      ```

Você também precisará configurar as credenciais locais se desejar executar o exemplo diretamente. A próxima seção descreve isso.
[!INCLUDE [Azure Digital Twins: local credentials prereq (outer)](../../includes/digital-twins-local-credentials-outer.md)]

### <a name="run-the-sample"></a>Execute o exemplo

Depois de concluir as etapas acima, você poderá executar diretamente o código de exemplo a seguir.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs":::

Aqui está a saída do console do programa acima: 

:::image type="content" source="./media/how-to-manage-twin/console-output-manage-twins.png" alt-text="Saída do console mostrando que o &quot;atualizar&quot; é criado, atualizado e excluído" lightbox="./media/how-to-manage-twin/console-output-manage-twins.png":::

## <a name="next-steps"></a>Próximas etapas

Consulte como criar e gerenciar relações entre seu gêmeos digital:
* [*Como: gerenciar o grafo de entrelaçamento com relações*](how-to-manage-graph.md)