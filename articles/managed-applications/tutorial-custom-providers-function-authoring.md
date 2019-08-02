---
title: Como criar um ponto de extremidade RESTful para provedores personalizados
description: Este tutorial apresentará como criar um ponto de extremidade RESTful para provedores personalizados. Ele entra em detalhes sobre como lidar com solicitações e respostas para os métodos HTTP RESTful compatíveis.
author: jjbfour
ms.service: managed-applications
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 176e3b02cbda7577e306d86363cfe5b41335fb6e
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67799175"
---
# <a name="authoring-a-restful-endpoint-for-custom-providers"></a>Como criar um ponto de extremidade RESTful para provedores personalizados

Provedores personalizados permitem que você personalize os fluxos de trabalho no Azure. Um provedor personalizado é um contrato entre o Azure e um `endpoint`. Este tutorial apresenta o processo de criar um `endpoint` RESTful de provedor personalizado. Se você não estiver familiarizado com os Provedores Personalizados do Azure, confira a [visão geral sobre provedores de recursos personalizados](./custom-providers-overview.md).

Este tutorial é dividido nas seguintes etapas:

- Como trabalhar com ações personalizadas e recursos personalizados
- Como particionar recursos personalizados no armazenamento
- Dar suporte a métodos RESTful de provedor personalizado
- Integrar operações RESTful

Este tutorial criará os seguintes tutoriais:

- [Configurar o Azure Functions para provedores personalizados do Azure](./tutorial-custom-providers-function-setup.md)

> [!NOTE]
> Este tutorial se baseia no tutorial anterior. Algumas das etapas no tutorial funcionarão apenas se a Função do Azure tiver sido configurada para funcionar com provedores personalizados.

## <a name="working-with-custom-actions-and-custom-resources"></a>Como trabalhar com ações personalizadas e recursos personalizados

Neste tutorial, vamos atualizar a função para que ela funcione como um ponto de extremidade RESTful para nosso provedor personalizado. No Azure, recursos e ações são modelados conforme a especificação RESTful básica: PUT – cria um novo recurso, GET (instância) – recupera um recurso existente, DELETE – remove um recurso existente, POST – dispara uma ação e GET (coleção) – lista todos os recursos existentes. Para este tutorial, vamos usar Tabelas do Azure como nosso armazenamento, porém, qualquer serviço de armazenamento ou banco de dados pode funcionar.

## <a name="how-to-partition-custom-resources-in-storage"></a>Como particionar recursos personalizados no armazenamento

Como estamos criando um serviço RESTful, é necessário armazenar os recursos criados no armazenamento. Para o armazenamento de Tabela do Azure, é necessário gerar chaves de partição e de linha para nossos dados. Para provedores personalizados, os dados devem ser particionados para o provedor personalizado. Quando uma solicitação de entrada for enviada ao provedor personalizado, o provedor personalizado adicionará o cabeçalho `x-ms-customproviders-requestpath` à solicitação de saída para o `endpoint`.

cabeçalho `x-ms-customproviders-requestpath` de exemplo para um recurso personalizado:

```
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{myResourceType}/{myResourceName}
```

Com base no cabeçalho `x-ms-customproviders-requestpath` de exemplo acima, podemos criar a partitionKey e a rowKey para nosso armazenamento da seguinte maneira:

Parâmetro | Modelo | DESCRIÇÃO
---|---
partitionKey | '{subscriptionId}:{resourceGroupName}:{resourceProviderName}' | A partitionKey é como os dados são particionados. Na maioria dos casos, os dados devem ser particionados pela instância do provedor personalizado.
rowKey | '{myResourceType}:{myResourceName}' | A rowKey é o identificador individual dos dados. Na maioria das vezes, é o nome do recurso.

Além disso, também precisamos criar uma nova classe para modelar nosso recurso personalizado. Neste tutorial, adicionaremos a classe `CustomResource` à nossa função, que é uma classe genérica que aceita qualquer dado inserido:

```csharp
// Custom Resource Table Entity
public class CustomResource : TableEntity
{
    public string Data { get; set; }
}
```

Isso cria uma classe básica baseada em `TableEntity`, que é usada para armazenar dados. A classe `CustomResource` herda duas propriedades de `TableEntity`: partitionKey e rowKey.

## <a name="support-custom-provider-restful-methods"></a>Dar suporte a métodos RESTful de provedor personalizado

> [!NOTE]
> Se você não estiver copiando o código diretamente do tutorial, o conteúdo da resposta deverá ser um JSON válido e definir o cabeçalho `Content-Type` como `application/json`.

Agora que temos o particionamento de dados configurado, vamos fazer o scaffold dos métodos básicos de CRUD e gatilho para recursos personalizados e ações personalizadas. Uma vez que os provedores personalizados atuam como um proxy, a solicitação e a resposta devem ser modeladas e tratadas pelo `endpoint`RESTful. Siga os snippets abaixo para lidar com as operações RESTful básicas:

### <a name="trigger-custom-action"></a>Disparar ação personalizada

Para provedores personalizados, uma ação personalizada é disparada por meio de solicitações `POST`. Uma ação personalizada também pode aceitar um corpo da solicitação que contém um conjunto de parâmetros de entrada. A ação então deve retornar uma resposta sinalizando o resultado da ação, bem como se ela obteve êxito ou falhou. Neste tutorial, adicionaremos o método `TriggerCustomAction` à nossa função:

```csharp
/// <summary>
/// Triggers a custom action with some side effect.
/// </summary>
/// <param name="requestMessage">The http request message.</param>
/// <returns>The http response result of the custom action.</returns>
public static async Task<HttpResponseMessage> TriggerCustomAction(HttpRequestMessage requestMessage)
{
    var myCustomActionRequest = await requestMessage.Content.ReadAsStringAsync();

    var actionResponse = requestMessage.CreateResponse(HttpStatusCode.OK);
    actionResponse.Content = myCustomActionRequest != string.Empty ? 
        new StringContent(JObject.Parse(myCustomActionRequest).ToString(), System.Text.Encoding.UTF8, "application/json") :
        null;
    return actionResponse;
}
```

O método `TriggerCustomAction` aceita uma solicitação de entrada e simplesmente ecoa de volta a resposta com um código de status de sucesso. 

### <a name="create-custom-resource"></a>Criar um recurso personalizado

Para provedores personalizados, um recurso personalizado é criado por meio de solicitações `PUT`. O provedor personalizado aceitará um corpo da solicitação JSON, que contém um conjunto de propriedades para o recurso personalizado. No Azure, os recursos seguem um modelo RESTful. A mesma URL de solicitação usada para criar um recurso também deve poder recuperar e excluir o recurso. Neste tutorial, adicionaremos o método `CreateCustomResource` para criar novos recursos:

```csharp
/// <summary>
/// Creates a custom resource and saves it to table storage.
/// </summary>
/// <param name="requestMessage">The http request message.</param>
/// <param name="tableStorage">The Azure Storage Account table.</param>
/// <param name="azureResourceId">The parsed Azure resource Id.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider id.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The http response containing the created custom resource.</returns>
public static async Task<HttpResponseMessage> CreateCustomResource(HttpRequestMessage requestMessage, CloudTable tableStorage, ResourceId azureResourceId, string partitionKey, string rowKey)
{
    // Adds the Azure top-level properties.
    var myCustomResource = JObject.Parse(await requestMessage.Content.ReadAsStringAsync());
    myCustomResource["name"] = azureResourceId.Name;
    myCustomResource["type"] = azureResourceId.FullResourceType;
    myCustomResource["id"] = azureResourceId.Id;

    // Save the resource into storage.
    var insertOperation = TableOperation.InsertOrReplace(
        new CustomResource
        {
            PartitionKey = partitionKey,
            RowKey = rowKey,
            Data = myCustomResource.ToString(),
        });
    await tableStorage.ExecuteAsync(insertOperation);

    var createResponse = requestMessage.CreateResponse(HttpStatusCode.OK);
    createResponse.Content = new StringContent(myCustomResource.ToString(), System.Text.Encoding.UTF8, "application/json");
    return createResponse;
}
```

O método `CreateCustomResource` atualiza a solicitação de entrada para incluir os campos específicos do Azure: `id`, `name` e `type`. Essas são propriedades de alto nível usadas por serviços em todo o Azure. Elas permitirão que o provedor personalizado integre-se a outros serviços, como o Azure Policy, os Modelos do Azure Resource Manager e os Logs de Atividades do Azure.

Propriedade | Amostra | DESCRIÇÃO
---|---|---
Nome | '{myCustomResourceName}' | O nome do recurso personalizado.
Tipo | 'Microsoft.CustomProviders/resourceProviders/{resourceTypeName}' | O namespace do tipo de recurso.
ID | '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>{resourceTypeName}/{myCustomResourceName}' | A ID do recurso.

Além de adicionar as propriedades, também podemos salvar o documento no Armazenamento de Tabelas do Azure. 

### <a name="retrieve-custom-resource"></a>Recuperar o recurso personalizado

Para provedores personalizados, um recurso personalizado é recuperado por meio de solicitações `GET`. O provedor personalizado *não* aceitará um corpo da solicitação JSON. No caso de solicitações `GET`, o **ponto de extremidade** deve usar o cabeçalho `x-ms-customproviders-requestpath` para retornar o recurso já criado. Neste tutorial, adicionaremos o método `RetrieveCustomResource` para recuperar os recursos existentes:

```csharp
/// <summary>
/// Retrieves a custom resource.
/// </summary>
/// <param name="requestMessage">The http request message.</param>
/// <param name="tableStorage">The Azure Storage Account table.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider id.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The http response containing the existing custom resource.</returns>
public static async Task<HttpResponseMessage> RetrieveCustomResource(HttpRequestMessage requestMessage, CloudTable tableStorage, string partitionKey, string rowKey)
{
    // Attempt to retrieve the Existing Stored Value
    var tableQuery = TableOperation.Retrieve<CustomResource>(partitionKey, rowKey);
    var existingCustomResource = (CustomResource)(await tableStorage.ExecuteAsync(tableQuery)).Result;

    var retrieveResponse = requestMessage.CreateResponse(
        existingCustomResource != null ? HttpStatusCode.OK : HttpStatusCode.NotFound);

    retrieveResponse.Content = existingCustomResource != null ?
            new StringContent(existingCustomResource.Data, System.Text.Encoding.UTF8, "application/json"):
            null;
    return retrieveResponse;
}
```

No Azure, os recursos devem seguir um modelo RESTful. A URL da solicitação que criou o recurso também deverá retornar o recurso se uma solicitação `GET` for executada.

### <a name="remove-custom-resource"></a>Remover o recurso personalizado

Para provedores personalizados, um recurso personalizado é removido por meio de solicitações `DELETE`. O provedor personalizado *não* aceitará um corpo da solicitação JSON. No caso de solicitações `DELETE`, o **ponto de extremidade** deve usar o cabeçalho `x-ms-customproviders-requestpath` para excluir o recurso já criado. Neste tutorial, adicionaremos o método `RemoveCustomResource` para remover os recursos existentes:

```csharp
/// <summary>
/// Removes an existing custom resource.
/// </summary>
/// <param name="requestMessage">The http request message.</param>
/// <param name="tableStorage">The Azure Storage Account table.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider id.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The http response containing the result of the delete.</returns>
public static async Task<HttpResponseMessage> RemoveCustomResource(HttpRequestMessage requestMessage, CloudTable tableStorage, string partitionKey, string rowKey)
{
    // Attempt to retrieve the Existing Stored Value
    var tableQuery = TableOperation.Retrieve<CustomResource>(partitionKey, rowKey);
    var existingCustomResource = (CustomResource)(await tableStorage.ExecuteAsync(tableQuery)).Result;

    if (existingCustomResource != null) {
        var deleteOperation = TableOperation.Delete(existingCustomResource);
        await tableStorage.ExecuteAsync(deleteOperation);
    }

    return requestMessage.CreateResponse(
        existingCustomResource != null ? HttpStatusCode.OK : HttpStatusCode.NoContent);
}
```

No Azure, os recursos devem seguir um modelo RESTful. A URL da solicitação que criou o recurso também deverá excluir o recurso se uma solicitação `DELETE` for executada.

### <a name="list-all-custom-resources"></a>Listar todos os recursos personalizados

Para provedores personalizados, uma lista de recursos personalizados existentes pode ser enumerada por meio de solicitações `GET` de coleção. O provedor personalizado *não* aceitará um corpo da solicitação JSON. No caso de solicitações `GET` de coleção, o `endpoint` deve usar o cabeçalho `x-ms-customproviders-requestpath` para enumerar os recursos já criados. Neste tutorial, adicionaremos o método `EnumerateAllCustomResources` para enumerar os recursos existentes.

```csharp
/// <summary>
/// Enumerates all the stored custom resources for a given type.
/// </summary>
/// <param name="requestMessage">The http request message.</param>
/// <param name="tableStorage">The Azure Storage Account table.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider id.</param>
/// <param name="resourceType">The resource type of the enumeration.</param>
/// <returns>The http response containing a list of resources stored under 'value'.</returns>
public static async Task<HttpResponseMessage> EnumerateAllCustomResources(HttpRequestMessage requestMessage, CloudTable tableStorage, string partitionKey, string resourceType)
{
    // Generate upper bound of the query.
    var rowKeyUpperBound = new StringBuilder(resourceType);
    rowKeyUpperBound[rowKeyUpperBound.Length - 1]++;

    // Create the enumeration query.
    var enumerationQuery = new TableQuery<CustomResource>().Where(
        TableQuery.CombineFilters(
            TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, partitionKey),
            TableOperators.And,
            TableQuery.CombineFilters(
                TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, resourceType),
                TableOperators.And,
                TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, rowKeyUpperBound.ToString()))));
    
    var customResources = (await tableStorage.ExecuteQuerySegmentedAsync(enumerationQuery, null))
        .ToList().Select(customResource => JToken.Parse(customResource.Data));

    var enumerationResponse = requestMessage.CreateResponse(HttpStatusCode.OK);
    enumerationResponse.Content = new StringContent(new JObject(new JProperty("value", customResources)).ToString(), System.Text.Encoding.UTF8, "application/json");
    return enumerationResponse;
}
```

> [!NOTE]
> A chave de linha maior e menor que é a sintaxe de Tabela do Azure para executar uma consulta "startswith" para cadeias de caracteres. 

Para listar todos os recursos existentes, podemos gerar uma consulta de Tabela do Azure que garanta que os recursos existam sob nossa partição de provedor personalizado. A consulta então verifica se a chave de linha começa com o mesmo `{myResourceType}`.

## <a name="integrate-restful-operations"></a>Integrar operações RESTful

Depois que todos os métodos RESTful são adicionados à função, podemos atualizar o método `Run` principal para chamar as funções para processar diferentes solicitações REST:

```csharp
/// <summary>
/// Entry point for the Azure Function webhook and acts as the service behind a custom provider.
/// </summary>
/// <param name="requestMessage">The http request message.</param>
/// <param name="log">The logger.</param>
/// <param name="tableStorage">The Azure Storage Account table.</param>
/// <returns>The http response for the custom Azure API.</returns>
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger log, CloudTable tableStorage)
{
    // Get the unique Azure request path from request headers.
    var requestPath = req.Headers.GetValues("x-ms-customproviders-requestpath").FirstOrDefault();

    if (requestPath == null)
    {
        var missingHeaderResponse = req.CreateResponse(HttpStatusCode.BadRequest);
        missingHeaderResponse.Content = new StringContent(
            new JObject(new JProperty("error", "missing 'x-ms-customproviders-requestpath' header")).ToString(),
            System.Text.Encoding.UTF8, 
            "application/json");
    }

    log.LogInformation($"The Custom Provider Function received a request '{req.Method}' for resource '{requestPath}'.");

    // Determines if it is a collection level call or action.
    var isResourceRequest = requestPath.Split('/').Length % 2 == 1;
    var azureResourceId = isResourceRequest ? 
        ResourceId.FromString(requestPath) :
        ResourceId.FromString($"{requestPath}/");

    // Create the Partition Key and Row Key
    var partitionKey = $"{azureResourceId.SubscriptionId}:{azureResourceId.ResourceGroupName}:{azureResourceId.Parent.Name}";
    var rowKey = $"{azureResourceId.FullResourceType.Replace('/', ':')}:{azureResourceId.Name}";

    switch (req.Method)
    {
        // Action request for an custom action.
        case HttpMethod m when m == HttpMethod.Post && !isResourceRequest:
            return await TriggerCustomAction(
                requestMessage: req);

        // Enumerate request for all custom resources.
        case HttpMethod m when m == HttpMethod.Get && !isResourceRequest:
            return await EnumerateAllCustomResources(
                requestMessage: req,
                tableStorage: tableStorage,
                partitionKey: partitionKey,
                resourceType: rowKey);

        // Retrieve request for a custom resource.
        case HttpMethod m when m == HttpMethod.Get && isResourceRequest:
            return await RetrieveCustomResource(
                requestMessage: req,
                tableStorage: tableStorage,
                partitionKey: partitionKey,
                rowKey: rowKey);

        // Create request for a custom resource.
        case HttpMethod m when m == HttpMethod.Put && isResourceRequest:
            return await CreateCustomResource(
                requestMessage: req,
                tableStorage: tableStorage,
                azureResourceId: azureResourceId,
                partitionKey: partitionKey,
                rowKey: rowKey);

        // Remove request for a custom resource.
        case HttpMethod m when m == HttpMethod.Delete && isResourceRequest:
            return await RemoveCustomResource(
                requestMessage: req,
                tableStorage: tableStorage,
                partitionKey: partitionKey,
                rowKey: rowKey);

        // Invalid request received.
        default:
            return req.CreateResponse(HttpStatusCode.BadRequest);
    }
}
``` 

O método `Run` atualizado agora incluirá a associação de entrada `tableStorage` que foi adicionada ao armazenamento de Tabela do Azure. A primeira parte do método agora lerá o cabeçalho `x-ms-customproviders-requestpath` e usará a biblioteca `Microsoft.Azure.Management.ResourceManager.Fluent` para analisar o valor como uma ID de recurso. O cabeçalho `x-ms-customproviders-requestpath` é enviado pelo provedor personalizado e designa o caminho da solicitação de entrada. Usando a ID de recurso analisada, agora podemos gerar a partitionKey e a rowKey para os dados para pesquisar ou armazenar recursos personalizados.

Além de adicionar os métodos e as classes, precisamos atualizar os métodos using para a função. Adicione o seguinte à parte superior do arquivo:

```csharp
#r "Newtonsoft.Json"
#r "Microsoft.WindowsAzure.Storage"
#r "../bin/Microsoft.Azure.Management.ResourceManager.Fluent.dll"

using System;
using System.Net;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Configuration;
using System.Text;
using System.Threading;
using System.Globalization;
using System.Collections.Generic;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.WindowsAzure.Storage.Table;
using Microsoft.Azure.Management.ResourceManager.Fluent.Core;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
```

Caso você tenha se perdido em qualquer ponto deste tutorial, o exemplo de código completo pode ser encontrado na [referência de ponto de extremidade RESTful C# do provedor personalizado](./reference-custom-providers-csharp-endpoint.md). Depois que a função tiver sido concluída, salve a URL da função que pode ser usada para disparar a função, pois ela será usada em tutoriais posteriores.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, criamos um ponto de extremidade RESTful para trabalhar com o `endpoint` do Provedor Personalizado do Azure. Vá para o próximo artigo para saber como criar um provedor personalizado.

- [Tutorial: Como criar um provedor personalizado](./tutorial-custom-providers-create.md)
