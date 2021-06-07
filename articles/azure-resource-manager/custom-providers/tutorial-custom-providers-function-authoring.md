---
title: Criar um ponto de extremidade RESTful
description: Este tutorial mostra como criar um ponto de extremidade RESTful para provedores personalizados. Ele fornece detalhes de como manipular solicitações e respostas dos métodos HTTP RESTful compatíveis.
author: jjbfour
ms.topic: tutorial
ms.date: 01/13/2021
ms.author: jobreen
ms.openlocfilehash: 54d0df287865d5d92403bf68227a2d4c5faa8bb4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98200202"
---
# <a name="author-a-restful-endpoint-for-custom-providers"></a>Criar um ponto de extremidade RESTful para provedores personalizados

Um provedor personalizado é um contrato entre o Azure e um ponto de extremidade. Com provedores personalizados, você pode personalizar os fluxos de trabalho no Azure. Este tutorial mostra como criar um ponto de extremidade RESTful para provedores personalizados. Caso você não esteja familiarizado com os Provedores Personalizados do Azure, confira [a visão geral sobre provedores de recursos personalizados](overview.md).

> [!NOTE]
> Este tutorial se baseia no tutorial [Configurar o Azure Functions para os Provedores Personalizados do Azure](./tutorial-custom-providers-function-setup.md). Algumas das etapas deste tutorial funcionarão apenas se o aplicativo de funções do Azure tiver sido configurado para funcionar com provedores personalizados.

## <a name="work-with-custom-actions-and-custom-resources"></a>Trabalhar com ações e recursos personalizados

Neste tutorial, você atualizará o aplicativo de funções, de modo que ele funcione como um ponto de extremidade RESTful para o provedor personalizado. Os recursos e as ações do Azure são modelados conforme a seguinte especificação RESTful básica:

- **PUT**: criar um recurso
- **GET (instância)** : recuperar um recurso existente
- **DELETE**: remover um recurso existente
- **POST**: disparar uma ação
- **GET (coleção)** : listar todos os recursos existentes

 Neste tutorial, você usará o Armazenamento de Tabelas do Azure. Mas qualquer serviço de armazenamento ou banco de dados pode funcionar.

## <a name="partition-custom-resources-in-storage"></a>Particionar os recursos personalizados no armazenamento

Como você está criando um serviço RESTful, precisará armazenar os recursos criados. Para o Armazenamento de Tabelas do Azure, é necessário gerar chaves de partição e de linha para os dados. Para provedores personalizados, os dados devem ser particionados para o provedor personalizado. Quando uma solicitação de entrada é enviada ao provedor personalizado, o provedor personalizado adiciona o cabeçalho `x-ms-customproviders-requestpath` às solicitações de saída para o ponto de extremidade.

O seguinte exemplo mostra um cabeçalho `x-ms-customproviders-requestpath` para um recurso personalizado:

```
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{myResourceType}/{myResourceName}
```

Com base no cabeçalho `x-ms-customproviders-requestpath` do exemplo, você pode criar os parâmetros *partitionKey* and *rowKey* para o armazenamento, conforme mostrado na seguinte tabela:

Parâmetro | Modelo | Descrição
---|---|---
*partitionKey* | `{subscriptionId}:{resourceGroupName}:{resourceProviderName}` | O parâmetro *partitionKey* especifica como os dados são particionados. Geralmente, os dados são particionados pela instância do provedor personalizado.
*rowKey* | `{myResourceType}:{myResourceName}` | O parâmetro *rowKey* especifica o identificador individual dos dados. Geralmente, o identificador é o nome do recurso.

Você também precisa criar uma classe para modelar o recurso personalizado. Neste tutorial, você adicionará a seguinte classe **CustomResource** ao aplicativo de funções:

```csharp
// Custom Resource Table Entity
public class CustomResource : TableEntity
{
    public string Data { get; set; }
}
```
**CustomResource** é uma classe genérica e simples que aceita qualquer dado de entrada. Ela se baseia em **TableEntity**, que é usada para armazenar dados. A classe **CustomResource** herda duas propriedades de **TableEntity**: **partitionKey** e **rowKey**.

## <a name="support-custom-provider-restful-methods"></a>Dar suporte a métodos RESTful de provedor personalizado

> [!NOTE]
> Se você não estiver copiando o código diretamente deste tutorial, o conteúdo da resposta deverá ser um JSON válido que define o cabeçalho `Content-Type` como `application/json`.

Agora que você configurou o particionamento de dados, crie os métodos de gatilho e CRUD básicos para as ações e os recursos personalizados. Como os provedores personalizados funcionam como proxies, o ponto de extremidade RESTful precisa modelar e manipular a solicitação e a resposta. Os snippets de código a seguir mostram como manipular as operações RESTful básicas.

### <a name="trigger-a-custom-action"></a>Disparar uma ação personalizada

Para provedores personalizados, uma ação personalizada é disparada por meio de solicitações POST. Uma ação personalizada também pode aceitar um corpo da solicitação que contém um conjunto de parâmetros de entrada. A ação retorna então uma resposta que sinaliza o resultado da ação e se ela obteve êxito ou falhou.

Adicione o seguinte método **TriggerCustomAction** ao aplicativo de funções:

```csharp
/// <summary>
/// Triggers a custom action with some side effects.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <returns>The HTTP response result of the custom action.</returns>
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

O método **TriggerCustomAction** aceita uma solicitação de entrada e simplesmente ecoa de volta a resposta com um código de status.

### <a name="create-a-custom-resource"></a>Criar um recurso personalizado

Para provedores personalizados, um recurso personalizado é criado por meio de solicitações PUT. O provedor personalizado aceita o corpo de uma solicitação JSON, que contém um conjunto de propriedades para o recurso personalizado. Os recursos do Azure seguem um modelo RESTful. Você pode usar a mesma URL de solicitação para criar, recuperar ou excluir um recurso.

Adicione o seguinte método **CreateCustomResource** para criar recursos:

```csharp
/// <summary>
/// Creates a custom resource and saves it to table storage.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="tableStorage">The Azure Table storage account.</param>
/// <param name="azureResourceId">The parsed Azure resource ID.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider ID.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The HTTP response containing the created custom resource.</returns>
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

O método **CreateCustomResource** atualiza a solicitação de entrada para incluir os campos específicos do Azure **id**, **name** e **type**. Esses campos são propriedades de alto nível usadas pelos serviços no Azure. Eles permitem que o provedor personalizado interopere com outros serviços, como Azure Policy, modelos do Azure Resource Manager e Log de Atividades do Azure.

Propriedade | Exemplo | Descrição
---|---|---
**name** | {myCustomResourceName} | O nome do recurso personalizado
**tipo** | Microsoft.CustomProviders/resourceProviders/{resourceTypeName} | O namespace do tipo de recurso
**id** | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>{resourceTypeName}/{myCustomResourceName} | A ID do recurso

Além de adicionar as propriedades, você também pode salvar o documento JSON no Armazenamento de Tabelas do Azure.

### <a name="retrieve-a-custom-resource"></a>Recuperar um recurso personalizado

Para provedores personalizados, um recurso personalizado é recuperado por meio de solicitações GET. Um provedor personalizado *não* aceita o corpo de uma solicitação JSON. Para solicitações GET, o ponto de extremidade usa o cabeçalho `x-ms-customproviders-requestpath` para retornar o recurso já criado.

Adicione o seguinte método **RetrieveCustomResource** para recuperar os recursos existentes:

```csharp
/// <summary>
/// Retrieves a custom resource.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="tableStorage">The Azure Table storage account.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider ID.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The HTTP response containing the existing custom resource.</returns>
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

No Azure, os recursos seguem um modelo RESTful. A URL da solicitação que cria o recurso também retorna o recurso se uma solicitação GET é executada.

### <a name="remove-a-custom-resource"></a>Remover um recurso personalizado

Para provedores personalizados, um recurso personalizado é removido por meio de solicitações DELETE. Um provedor personalizado *não* aceita o corpo de uma solicitação JSON. Para uma solicitação DELETE, o ponto de extremidade usa o cabeçalho `x-ms-customproviders-requestpath` para excluir o recurso já criado.

Adicione o seguinte método **RemoveCustomResource** para remover os recursos existentes:

```csharp
/// <summary>
/// Removes an existing custom resource.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="tableStorage">The Azure storage account table.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider ID.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The HTTP response containing the result of the deletion.</returns>
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

No Azure, os recursos seguem um modelo RESTful. A URL da solicitação que cria o recurso também exclui o recurso se uma solicitação DELETE é executada.

### <a name="list-all-custom-resources"></a>Listar todos os recursos personalizados

Para provedores personalizados, é possível enumerar uma lista dos recursos personalizados existentes usando a coleção de solicitações GET. Um provedor personalizado *não* aceita o corpo de uma solicitação JSON. Para uma coleção de solicitações GET, o ponto de extremidade usa o cabeçalho `x-ms-customproviders-requestpath` para enumerar os recursos já criados.

Adicione o seguinte método **EnumerateAllCustomResources** para enumerar os recursos existentes:

```csharp
/// <summary>
/// Enumerates all the stored custom resources for a given type.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="tableStorage">The Azure Table storage account.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider ID.</param>
/// <param name="resourceType">The resource type of the enumeration.</param>
/// <returns>The HTTP response containing a list of resources stored under 'value'.</returns>
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
> RowKey QueryComparisons.GreaterThan e QueryComparisons.LessThan é a sintaxe do Armazenamento de Tabelas do Azure para executar uma consulta "startswith" para cadeias de caracteres.

Para listar todos os recursos existentes, gere uma consulta do Armazenamento de Tabelas do Azure que garanta que os recursos existam na partição do provedor personalizado. A consulta então verifica se a chave de linha começa com o mesmo valor `{myResourceType}`.

## <a name="integrate-restful-operations"></a>Integrar operações RESTful

Depois que todos os métodos RESTful forem adicionados ao aplicativo de funções, atualize o método **Run** principal que chama as funções para manipular as diferentes solicitações REST:

```csharp
/// <summary>
/// Entry point for the Azure function app webhook that acts as the service behind a custom provider.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="log">The logger.</param>
/// <param name="tableStorage">The Azure Table storage account.</param>
/// <returns>The HTTP response for the custom Azure API.</returns>
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
        // Action request for a custom action.
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

O método **Run** atualizado agora inclui a associação de entrada *tableStorage* que você adicionou ao Armazenamento de Tabelas do Azure. A primeira parte do método lê o cabeçalho `x-ms-customproviders-requestpath` e usa a biblioteca `Microsoft.Azure.Management.ResourceManager.Fluent` para analisar o valor como uma ID do recurso. O cabeçalho `x-ms-customproviders-requestpath` é enviado pelo provedor personalizado e especifica o caminho da solicitação de entrada.

Usando a ID do recurso analisada, você pode gerar os valores **partitionKey** and **rowKey** para os dados para pesquisar ou armazenar recursos personalizados.

Depois de adicionar os métodos e as classes, você precisará atualizar os métodos **using** do aplicativo de funções. Adicione o seguinte código ao início do arquivo C#:

```csharp
#r "Newtonsoft.Json"
#r "Microsoft.WindowsAzure.Storage"
#r "../bin/Microsoft.Azure.Management.ResourceManager.Fluent"

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

Caso você tenha se perdido em qualquer ponto deste tutorial, encontre o exemplo de código completo na [referência de ponto de extremidade RESTful do C# do provedor personalizado](./reference-custom-providers-csharp-endpoint.md). Depois de concluir o aplicativo de funções, salve a URL dele. Ela pode ser usada para disparar o aplicativo de funções em tutoriais posteriores.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você criou um ponto de extremidade RESTful para funcionar com um ponto de extremidade de Provedor Personalizado do Azure. Para saber como criar um provedor personalizado, acesse o artigo [Tutorial: Como criar um provedor personalizado](./tutorial-custom-providers-create.md).
