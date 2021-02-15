---
title: Gerenciar conexões no Azure Functions
description: Saiba como evitar problemas de desempenho no Azure Functions usando clientes de conexão estática.
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 02/25/2018
ms.openlocfilehash: ec16ce3e7f9793be2a012a029bcca31c9a7ea4cf
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97936695"
---
# <a name="manage-connections-in-azure-functions"></a>Gerenciar conexões no Azure Functions

Funções em um aplicativo de funções compartilham recursos. Entre esses recursos compartilhados estão conexões: conexões HTTP, conexões de banco de dados e conexões com serviços como o armazenamento do Azure. Quando muitas funções estão em execução simultaneamente, é possível ficar sem conexões disponíveis. Este artigo explica como codificar suas funções para evitar o uso de mais conexões do que precisam.

## <a name="connection-limit"></a>Limite de conexão

O número de conexões disponíveis é limitado parcialmente porque um aplicativo de funções é executado em um [ambiente de área restrita](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox). Uma das restrições que a área de proteção impõe em seu código é um limite no número de conexões de saída, que são atualmente 600 conexões ativas (total de 1.200) por instância. Quando você atinge esse limite, o tempo de execução do Functions grava a seguinte mensagem nos logs: `Host thresholds exceeded: Connections` . Para obter mais informações, consulte os [limites de serviço do Functions](functions-scale.md#service-limits).

Esse limite é por instância. Quando o [controlador de escala adiciona instâncias do aplicativo de funções](event-driven-scaling.md) para lidar com mais solicitações, cada instância tem um limite de conexão independente. Isso significa que não há nenhum limite de conexão global e você pode ter muito mais do que 600 conexões ativas em todas as instâncias ativas.

Ao solucionar problemas, certifique-se de ter habilitado Application Insights para seu aplicativo de funções. Application Insights permite exibir métricas para seus aplicativos de funções, como execuções. Para obter mais informações, consulte [Exibir telemetria no Application insights](analyze-telemetry-data.md#view-telemetry-in-application-insights).  

## <a name="static-clients"></a>Clientes estáticos

Para evitar manter mais conexões que o necessário, reutilize as instâncias do cliente em vez de criar novas com cada invocação de função. É recomendável reutilizar conexões de cliente para qualquer idioma em que você possa escrever sua função. Por exemplo, clientes .NET como o [HttpClient](/dotnet/api/system.net.http.httpclient?view=netcore-3.1&preserve-view=true), o [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient)e os clientes de armazenamento do Azure podem gerenciar conexões se você usar um único cliente estático.

Aqui estão algumas diretrizes a serem seguidas quando você estiver usando um cliente específico do serviço em um aplicativo Azure Functions:

- *Não crie um* novo cliente com cada invocação de função.
- *Crie um* único cliente estático que cada invocação de função possa usar.
- *Considere* a criação de um único cliente estático em uma classe auxiliar compartilhada se funções diferentes usarem o mesmo serviço.

## <a name="client-code-examples"></a>Exemplos de código do cliente

Esta seção demonstra as melhores práticas para criar e usar clientes de seu código de função.

### <a name="httpclient-example-c"></a>Exemplo do HttpClient (C#)

Veja um exemplo de código de função C# que cria uma instância [HttpClient](/dotnet/api/system.net.http.httpclient?view=netcore-3.1&preserve-view=true) estática:

```cs
// Create a single, static HttpClient
private static HttpClient httpClient = new HttpClient();

public static async Task Run(string input)
{
    var response = await httpClient.GetAsync("https://example.com");
    // Rest of function
}
```

Uma pergunta comum sobre o [HttpClient](/dotnet/api/system.net.http.httpclient?view=netcore-3.1&preserve-view=true) no .net é "Devo descartar meu cliente?" Em geral, você descartar objetos que implementam `IDisposable` quando você terminar de usá-los. Mas você não descartar um cliente estático porque não está pronto usando-o quando a função termina. Você quer que o cliente estático permaneça durante a duração do aplicativo.

### <a name="http-agent-examples-javascript"></a>Exemplos de agente HTTP (JavaScript)

Como ele fornece melhores opções de gerenciamento de conexão, você deve usar a [`http.agent`](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_class_http_agent) classe nativa em vez de métodos não nativos, como o `node-fetch` módulo. Os parâmetros de conexão são configurados por meio de opções na `http.agent` classe. Para obter opções detalhadas disponíveis com o agente HTTP, consulte [novo agente ( \[ opções \] )](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_new_agent_options).

A `http.globalAgent` classe global usada pelo `http.request()` tem todos esses valores definidos para seus respectivos padrões. A maneira recomendada de configurar limites de conexão em Funções é definir um número máximo globalmente. O exemplo a seguir define o número máximo de soquetes para o aplicativo de função:

```js
http.globalAgent.maxSockets = 200;
```

 O exemplo a seguir cria uma nova solicitação HTTP com um agente HTTP personalizado somente para essa solicitação:

```js
var http = require('http');
var httpAgent = new http.Agent();
httpAgent.maxSockets = 200;
options.agent = httpAgent;
http.request(options, onResponseCallback);
```

### <a name="documentclient-code-example-c"></a>Exemplo de código DocumentClient (C#)

O [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient) conecta uma instância do Azure Cosmos DB. A documentação do Azure Cosmos DB recomenda que você [use um cliente singleton do Azure Cosmos DB pelo tempo de vida do aplicativo](../cosmos-db/performance-tips.md#sdk-usage). O exemplo a seguir mostra um padrão para fazer isso em uma função:

```cs
#r "Microsoft.Azure.Documents.Client"
using Microsoft.Azure.Documents.Client;

private static Lazy<DocumentClient> lazyClient = new Lazy<DocumentClient>(InitializeDocumentClient);
private static DocumentClient documentClient => lazyClient.Value;

private static DocumentClient InitializeDocumentClient()
{
    // Perform any initialization here
    var uri = new Uri("example");
    var authKey = "authKey";
    
    return new DocumentClient(uri, authKey);
}

public static async Task Run(string input)
{
    Uri collectionUri = UriFactory.CreateDocumentCollectionUri("database", "collection");
    object document = new { Data = "example" };
    await documentClient.UpsertDocumentAsync(collectionUri, document);
    
    // Rest of function
}
```
Se você estiver trabalhando com funções v3. x, precisará de uma referência a Microsoft.Azure.DocumentDB. Core. Adicione uma referência no código:

```cs
#r "Microsoft.Azure.DocumentDB.Core"
```
Além disso, crie um arquivo chamado "function. proj" para seu gatilho e adicione o conteúdo abaixo:

```cs

<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <TargetFramework>netcoreapp3.0</TargetFramework>
    </PropertyGroup>
    <ItemGroup>
        <PackageReference Include="Microsoft.Azure.DocumentDB.Core" Version="2.12.0" />
    </ItemGroup>
</Project>

```
### <a name="cosmosclient-code-example-javascript"></a>Exemplo de código CosmosClient (JavaScript)
[CosmosClient](/javascript/api/@azure/cosmos/cosmosclient) conecta-se a uma instância de Azure Cosmos DB. A documentação do Azure Cosmos DB recomenda que você [use um cliente singleton do Azure Cosmos DB pelo tempo de vida do aplicativo](../cosmos-db/performance-tips.md#sdk-usage). O exemplo a seguir mostra um padrão para fazer isso em uma função:

```javascript
const cosmos = require('@azure/cosmos');
const endpoint = process.env.COSMOS_API_URL;
const key = process.env.COSMOS_API_KEY;
const { CosmosClient } = cosmos;

const client = new CosmosClient({ endpoint, key });
// All function invocations also reference the same database and container.
const container = client.database("MyDatabaseName").container("MyContainerName");

module.exports = async function (context) {
    const { resources: itemArray } = await container.items.readAll().fetchAll();
    context.log(itemArray);
}
```

## <a name="sqlclient-connections"></a>Conexões SqlClient

Seu código de função pode usar o .NET Framework Provedor de Dados para SQL Server ([SqlClient](/dotnet/api/system.data.sqlclient)) para fazer conexões com um banco de dados relacional do SQL. Esse também é o provedor subjacente para estruturas de dados que dependem de ADO.NET, como [Entity Framework](/ef/ef6/). Ao contrário das conexões [HttpClient](/dotnet/api/system.net.http.httpclient) e [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient), o ADO.NET implementa o pool de conexões por padrão. Mas como você ainda pode ficar sem conexões, você deve otimizar as conexões com o banco de dados. Para obter mais informações, consulte [Pool de Conexões do SQL Server (ADO.NET)](/dotnet/framework/data/adonet/sql-server-connection-pooling).

> [!TIP]
> Algumas estruturas de dados, como Entity Framework, normalmente recebem cadeias de conexão da seção **connectionStrings** de um arquivo de configuração. Nesse caso, é necessário adicionar explicitamente as cadeias de conexão de banco de dados SQL à coleção **Cadeias de conexão** das configurações de aplicativo de funções e no [local.settings.json file](functions-run-local.md#local-settings-file) no projeto local. Se você estiver criando uma instância de [SqlConnection](/dotnet/api/system.data.sqlclient.sqlconnection) em seu código de função, deverá armazenar o valor da cadeia de conexão nas **configurações do aplicativo** com suas outras conexões.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre por que recomendamos clientes estáticos, consulte [antipadrão de instanciação impróprio](/azure/architecture/antipatterns/improper-instantiation/).

Para obter mais dicas de desempenho do Azure Functions, consulte [Melhore o desempenho e a confiabilidade do Azure Functions](functions-best-practices.md).
