---
title: Gerenciar conexões no Azure Functions
description: Saiba como evitar problemas de desempenho no Azure Functions usando clientes de conexão estática.
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 02/25/2018
ms.author: glenga
ms.openlocfilehash: 4e9bd4e9ea467446c2814cdb8956a40b1503b027
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61020478"
---
# <a name="manage-connections-in-azure-functions"></a>Gerenciar conexões no Azure Functions

Funções em um aplicativo de funções compartilham recursos. Entre esses recursos compartilhados são conexões: Conexões HTTP, conexões de banco de dados e conexões com serviços como o armazenamento do Azure. Quando muitas funções estão em execução simultaneamente, é possível ficar sem conexões disponíveis. Este artigo explica como codificar suas funções para evitar o uso de mais conexões do que o necessário.

## <a name="connection-limit"></a>Limite de Conexão

O número de conexões disponíveis é limitado em parte porque um aplicativo de funções é executado em um [ambiente de área restrita](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox). Uma das restrições do que a área restrita impõe no seu código é um [limite o número de conexões (no momento em 600 conexões ativas e o total de conexões de 1.200)](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#numerical-sandbox-limits) por instância. Ao alcançar esse limite, o tempo de execução das funções criará um log com a seguinte mensagem: `Host thresholds exceeded: Connections`.

Esse limite é por instância.  Quando o [controlador de escala adiciona as instâncias do aplicativo de função](functions-scale.md#how-the-consumption-and-premium-plans-work) para tratar mais solicitações, cada instância possui um limite de conexão independente. Isso significa que não há nenhum limite de conexão global, e você pode ter muito mais de 600 conexões ativas em todas as instâncias ativas.

Ao solucionar o problema, certifique-se de que você tiver habilitado o Application Insights para seu aplicativo de funções. O Application Insights permite exibir as métricas para seus aplicativos de funções, como execuções. Para obter mais informações, consulte [exibir a telemetria no Application Insights](functions-monitoring.md#view-telemetry-in-application-insights).  

## <a name="static-clients"></a>Clientes estáticos

Para evitar manter mais conexões que o necessário, reutilize as instâncias do cliente em vez de criar novas com cada invocação de função. É recomendável reutilizando as conexões de cliente para qualquer linguagem que você pode escrever sua função no. Por exemplo, os clientes .NET, como o [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx), [DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
), e os clientes de armazenamento do Azure podem gerenciar conexões se você usar um cliente simples e estático.

Aqui estão algumas diretrizes a seguir quando você estiver usando um cliente específico do serviço em um aplicativo do Azure Functions:

- *Não* criar um novo cliente com cada invocação de função.
- *Fazer* criar um cliente simples e estático que cada invocação de função pode usar.
- *Considere* criando um cliente simples e estático em uma classe auxiliar compartilhado se funções diferentes usam o mesmo serviço.

## <a name="client-code-examples"></a>Exemplos de código do cliente

Esta seção demonstra as melhores práticas para criar e usar clientes de seu código de função.

### <a name="httpclient-example-c"></a>Exemplo do HttpClient (C#)

Aqui está um exemplo de C# função de código que cria um estático [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) instância:

```cs
// Create a single, static HttpClient
private static HttpClient httpClient = new HttpClient();

public static async Task Run(string input)
{
    var response = await httpClient.GetAsync("https://example.com");
    // Rest of function
}
```

Uma pergunta comum sobre [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) no .NET é "Devo descartar meu cliente?" Em geral, você descarta os objetos que implementam `IDisposable` quando você terminar usá-los. Não descartar um cliente estático porque você não é feitas, mas usá-lo quando a função terminar. Você quer que o cliente estático permaneça durante a duração do aplicativo.

### <a name="http-agent-examples-javascript"></a>Exemplos de agente HTTP (JavaScript)

Como ele fornece melhores opções de gerenciamento de conexão, você deve usar a classe[`http.agent`](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_class_http_agent) nativa em vez de métodos não nativos, como o módulo`node-fetch`. Parâmetros de Conexão são configurados por meio de opções no `http.agent` classe. Para opções detalhadas disponíveis com o agente HTTP, consulte [novo agente (\[opções\])](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_new_agent_options).

Global `http.globalAgent` classe usada por `http.request()` tem todos esses valores definidos com seus respectivos padrões. A maneira recomendada de configurar limites de conexão em Funções é definir um número máximo globalmente. O exemplo a seguir define o número máximo de soquetes para o aplicativo de função:

```js
http.globalAgent.maxSockets = 200;
```

 O exemplo a seguir cria uma nova solicitação HTTP com um agente personalizado de HTTP para a solicitação:

```js
var http = require('http');
var httpAgent = new http.Agent();
httpAgent.maxSockets = 200;
options.agent = httpAgent;
http.request(options, onResponseCallback);
```

### <a name="documentclient-code-example-c"></a>Exemplo de código DocumentClient (C#)

O [DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
) conecta uma instância do Azure Cosmos DB. A documentação do Azure Cosmos DB recomenda que você [use um cliente singleton do Azure Cosmos DB pelo tempo de vida do aplicativo](https://docs.microsoft.com/azure/cosmos-db/performance-tips#sdk-usage). O exemplo a seguir mostra um padrão para fazer isso em uma função:

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

### <a name="cosmosclient-code-example-javascript"></a>Exemplo de código CosmosClient (JavaScript)
[CosmosClient](/javascript/api/@azure/cosmos/cosmosclient) se conecta a uma instância do Azure Cosmos DB. A documentação do Azure Cosmos DB recomenda que você [use um cliente singleton do Azure Cosmos DB pelo tempo de vida do aplicativo](../cosmos-db/performance-tips.md#sdk-usage). O exemplo a seguir mostra um padrão para fazer isso em uma função:

```javascript
const cosmos = require('@azure/cosmos');
const endpoint = process.env.COSMOS_API_URL;
const masterKey = process.env.COSMOS_API_KEY;
const { CosmosClient } = cosmos;

const client = new CosmosClient({ endpoint, auth: { masterKey } });
// All function invocations also reference the same database and container.
const container = client.database("MyDatabaseName").container("MyContainerName");

module.exports = async function (context) {
    const { result: itemArray } = await container.items.readAll().toArray();
    context.log(itemArray);
}
```

## <a name="sqlclient-connections"></a>Conexões SqlClient

O código de função pode usar o .NET Framework Data Provider para SQL Server ([SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx)) para estabelecer conexões com um banco de dados relacional do SQL. Isso também é o provedor subjacente para estruturas de dados que se baseiam no ADO.NET, como [Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx). Ao contrário das conexões [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) e [DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
), o ADO.NET implementa o pool de conexões por padrão. Mas, porque você ainda pode ficar sem conexões, você deve otimizar a conexões ao banco de dados. Para obter mais informações, consulte [Pool de Conexões do SQL Server (ADO.NET)](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling).

> [!TIP]
> Algumas estruturas de dados, como o Entity Framework, normalmente obtém cadeias de caracteres de conexão das **ConnectionStrings** seção de um arquivo de configuração. Nesse caso, é necessário adicionar explicitamente as cadeias de conexão de banco de dados SQL à coleção **Cadeias de conexão** das configurações de aplicativo de funções e no [local.settings.json file](functions-run-local.md#local-settings-file) no projeto local. Se você estiver criando uma instância do [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) em seu código de função, você deve armazenar o valor de cadeia de caracteres de conexão na **configurações do aplicativo** com as outras conexões.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre por que recomendamos que os clientes estáticos, consulte [antipadrão de instanciação inadequada](https://docs.microsoft.com/azure/architecture/antipatterns/improper-instantiation/).

Para obter mais dicas de desempenho do Azure Functions, consulte [Melhore o desempenho e a confiabilidade do Azure Functions](functions-best-practices.md).
