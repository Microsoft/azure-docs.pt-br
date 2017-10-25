---
title: 'Azure Cosmos DB: Desenvolver com a API no .NET | Microsoft Docs'
description: Aprenda a desenvolver com a API DocumentDB do Azure Cosmos DB usando o .NET
services: cosmos-db
documentationcenter: 
author: dennyglee
manager: jhubbard
editor: 
ms.assetid: cc8df0be-672b-493e-95a4-26dd52632261
ms.service: cosmos-db
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 05/10/2017
ms.author: denlee
ms.custom: mvc
ms.openlocfilehash: b1419e5aad9446b9d96450cfad79b200cda9a518
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cosmos-db-develop-with-the-graph-api-in-net"></a>Azure Cosmos DB: Desenvolver com a API do Graph no .NET
O Azure Cosmos DB é o serviço de banco de dados multimodelo distribuído globalmente da Microsoft. É possível criar e consultar rapidamente documentos, chave/valor e bancos de dados do gráfico. Todos se beneficiam de recursos de escala horizontal e distribuição global no núcleo do Azure Cosmos DB. 

Este tutorial demonstra como criar uma conta do Azure Cosmos DB usando o portal do Azure e como criar um banco de dados do gráfico e o contêiner. O aplicativo, em seguida, cria uma rede social simple com quatro pessoas que usam a [API do Graph](graph-sdk-dotnet.md) (visualização), em seguida, percorre e consulta o gráfico usando Gremlin.

Este tutorial cobre as seguintes tarefas:

> [!div class="checklist"]
> * Criar uma conta do Azure Cosmos DB 
> * Criar um banco de dados do gráfico e um contêiner
> * Serializar os vértices e bordas aos objetos .NET
> * Adicionar vértices e bordas
> * Consultar o gráfico usando Gremlin

## <a name="graphs-in-azure-cosmos-db"></a>Gráficos no Azure Cosmos DB
Você pode usar o Azure Cosmos DB para criar, atualizar e consultar gráficos usando a biblioteca [Microsoft.Azure.Graphs](graph-sdk-dotnet.md). A biblioteca Microsoft.Azure.Graph fornece um método de extensão único `CreateGremlinQuery<T>` sobre a classe `DocumentClient` para executar consultas de Gremlin.

Gremlin é uma linguagem de programação funcional que oferece suporte a operações de gravação (DML) e operações de consulta e passagem. Abordamos alguns exemplos neste artigo para introdução ao uso do Gremlin. Veja [consultas Gremlin](gremlin-support.md) para obter uma explicação detalhada dos recursos Gremlin disponíveis no Azure Cosmos DB. 

## <a name="prerequisites"></a>Pré-requisitos
Certifique-se que você tem o seguinte:

* Uma conta ativa do Azure. Se não tiver uma, você poderá se inscrever em uma [conta gratuita](https://azure.microsoft.com/free/). 
    * Como alternativa, você pode usar o [Emulador do DocumentDB do Azure](local-emulator.md) para este tutorial.
* [Visual Studio](http://www.visualstudio.com/).

## <a name="create-database-account"></a>Criar uma conta de banco de dados

Vamos começar criando uma conta do Azure Cosmos DB no portal do Azure.  

> [!TIP]
> * Já tem uma conta do Azure Cosmos DB? Nesse caso, pule para [Configurar sua solução do Visual Studio](#SetupVS)
> * Você tinha uma conta do Azure DocumentDB? Se sua conta agora é uma conta do Azure Cosmos DB, você pode pular para [Configurar sua solução do Visual Studio](#SetupVS).  
> * Se estiver usando o Emulador do Azure Cosmos DB, execute as etapas em [Emulador do Azure Cosmos DB](local-emulator.md) para configurar o emulador e pule para [Configurar sua solução do Visual Studio](#SetupVS). 
>
> 

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a id="SetupVS"></a>Configurar sua solução do Visual Studio
1. Abra o **Visual Studio** no seu computador.
2. No menu **Arquivo**, selecione **Novo** e depois **Projeto**.
3. Na caixa de diálogo **Novo Projeto**, selecione **Modelos** / **Visual C#** / **Aplicativo de Console (.NET Framework)**, nomeie o projeto e clique em **OK**.
4. No **Gerenciador de Soluções**, clique com o botão direito do mouse no seu novo aplicativo de console, que está em sua solução do Visual Studio e clique em **gerenciar pacotes NuGet...**
5. Na guia **NuGet**, clique em **Procurar**e digite **Microsoft.Azure.Graphs** na caixa de pesquisa e marque **Incluir versões de pré-lançamento**.
6. Nos resultados, encontre **Microsoft.Azure.Graphs** e clique em **Instalar**.
   
   Se receber uma mensagem sobre a análise das alterações para a solução, clique em **OK**. Se receber uma mensagem sobre a aceitação da licença, clique em **Aceito**.
   
    A biblioteca `Microsoft.Azure.Graphs` fornece um método de extensão único `CreateGremlinQuery<T>` para executar operações de Gremlin. Gremlin é uma linguagem de programação funcional que oferece suporte a operações de gravação (DML) e operações de consulta e passagem. Abordamos alguns exemplos neste artigo para introdução ao uso do Gremlin. [Consultas Gremlin](gremlin-support.md) possui uma explicação detalhada dos recursos Gremlin no Azure Cosmos DB.

## <a id="add-references"></a>Conecte seu aplicativo

Adicionar essas duas constantes e sua variável de *cliente* no seu aplicativo. 

```csharp
string endpoint = ConfigurationManager.AppSettings["Endpoint"]; 
string authKey = ConfigurationManager.AppSettings["AuthKey"]; 
``` 
Em seguida, volte ao [portal do Azure](https://portal.azure.com) para recuperar a URL do ponto de extremidade e a chave primária. A URL do ponto de extremidade e a chave primária são necessárias para que seu aplicativo reconheça onde deve se conectar e para que o Azure Cosmos DB confie na conexão do seu aplicativo. 

No portal do Azure, navegue até a sua conta do Azure Cosmos DB, clique em **Chaves** e, em seguida, clique em **Chaves de leitura/gravação**. 

Copie o URI do portal e cole-o sobre `Endpoint` na propriedade do ponto de extremidade acima. Em seguida, copie a CHAVE PRIMÁRIA do portal e cole-a na propriedade `AuthKey` acima. 

![Captura de tela do portal do Azure usada pelo tutorial para criar um aplicativo C#. Mostra para uma conta do Azure Cosmos DB o botão CHAVES realçado na barra de navegação do Azure Cosmos DB e os valores de URI e CHAVE PRIMÁRIA realçados na folha Chaves][chaves] 
 
## <a id="instantiate"></a>Criar uma instância do DocumentClient 
Em seguida, crie uma nova instância do **DocumentClient**.  

```csharp 
DocumentClient client = new DocumentClient(new Uri(endpoint), authKey); 
``` 

## <a id="create-database"></a>Criar um banco de dados 

Agora, crie um [banco de dados](documentdb-resources.md#databases) do Azure Cosmos DB usando o método [CreateDatabaseAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdatabaseasync.aspx) ou o método [CreateDatabaseIfNotExistsAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdatabaseifnotexistsasync.aspx) da classe **DocumentClient** a partir do [SDK .NET do DocumentDB](documentdb-sdk-dotnet.md).  

```csharp 
Database database = await client.CreateDatabaseIfNotExistsAsync(new Database { Id = "graphdb" }); 
``` 
 
## <a name="create-a-graph"></a>Criar um gráfico 

Em seguida, crie um contêiner de gráfico usando o usando o método [CreateDocumentCollectionAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdocumentcollectionasync.aspx) ou o método [CreateDocumentCollectionIfNotExistsAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentcollectionifnotexistsasync.aspx) da classe **DocumentClient**. Uma coleção é um contêiner de entidades de gráfico. 

```csharp 
DocumentCollection graph = await client.CreateDocumentCollectionIfNotExistsAsync( 
    UriFactory.CreateDatabaseUri("graphdb"), 
    new DocumentCollection { Id = "graphcollz" }, 
    new RequestOptions { OfferThroughput = 1000 }); 
``` 

## <a id="serializing"></a>Serializar os vértices e bordas aos objetos .NET
O Azure Cosmos DB usa o [formato de arame GraphSON](gremlin-support.md), que define um esquema JSON de vértices, bordas e propriedades. O SDK do .NET do Azure DB Cosmos inclui JSON.NET como uma dependência, e isso nos permite serializar/desserializar GraphSON em objetos .NET com os quais podemos trabalhar no código.

Por exemplo, vamos trabalhar com uma rede social simples com quatro pessoas. Veremos como criar vértices `Person`, adicionar relacionamentos `Knows` entre eles, em seguida, consultar e percorrer o gráfico para localizar relacionamentos "amigo de amigo". 

O namespace `Microsoft.Azure.Graphs.Elements` fornece as classes `Vertex`, `Edge`, `Property` e `VertexProperty` para desserializar respostas GraphSON para objetos do .NET bem definidos.

## <a name="run-gremlin-using-creategremlinquery"></a>Executar Gremlin usando CreateGremlinQuery
Gremlin, assim como o SQL, oferece suporte a operações de consulta, gravação e leitura. Por exemplo, o trecho a seguir mostra como criar vértices, bordas, executar algumas consultas de exemplo usando `CreateGremlinQuery<T>` e iterar assincronamente esses resultados usando `ExecuteNextAsync` e `HasMoreResults.

```cs
Dictionary<string, string> gremlinQueries = new Dictionary<string, string>
{
    { "Cleanup",        "g.V().drop()" },
    { "AddVertex 1",    "g.addV('person').property('id', 'thomas').property('firstName', 'Thomas').property('age', 44)" },
    { "AddVertex 2",    "g.addV('person').property('id', 'mary').property('firstName', 'Mary').property('lastName', 'Andersen').property('age', 39)" },
    { "AddVertex 3",    "g.addV('person').property('id', 'ben').property('firstName', 'Ben').property('lastName', 'Miller')" },
    { "AddVertex 4",    "g.addV('person').property('id', 'robin').property('firstName', 'Robin').property('lastName', 'Wakefield')" },
    { "AddEdge 1",      "g.V('thomas').addE('knows').to(g.V('mary'))" },
    { "AddEdge 2",      "g.V('thomas').addE('knows').to(g.V('ben'))" },
    { "AddEdge 3",      "g.V('ben').addE('knows').to(g.V('robin'))" },
    { "UpdateVertex",   "g.V('thomas').property('age', 44)" },
    { "CountVertices",  "g.V().count()" },
    { "Filter Range",   "g.V().hasLabel('person').has('age', gt(40))" },
    { "Project",        "g.V().hasLabel('person').values('firstName')" },
    { "Sort",           "g.V().hasLabel('person').order().by('firstName', decr)" },
    { "Traverse",       "g.V('thomas').outE('knows').inV().hasLabel('person')" },
    { "Traverse 2x",    "g.V('thomas').outE('knows').inV().hasLabel('person').outE('knows').inV().hasLabel('person')" },
    { "Loop",           "g.V('thomas').repeat(out()).until(has('id', 'robin')).path()" },
    { "DropEdge",       "g.V('thomas').outE('knows').where(inV().has('id', 'mary')).drop()" },
    { "CountEdges",     "g.E().count()" },
    { "DropVertex",     "g.V('thomas').drop()" },
};

foreach (KeyValuePair<string, string> gremlinQuery in gremlinQueries)
{
    Console.WriteLine($"Running {gremlinQuery.Key}: {gremlinQuery.Value}");

    // The CreateGremlinQuery method extensions allow you to execute Gremlin queries and iterate
    // results asychronously
    IDocumentQuery<dynamic> query = client.CreateGremlinQuery<dynamic>(graph, gremlinQuery.Value);
    while (query.HasMoreResults)
    {
        foreach (dynamic result in await query.ExecuteNextAsync())
        {
            Console.WriteLine($"\t {JsonConvert.SerializeObject(result)}");
        }
    }

    Console.WriteLine();
}
```

## <a name="add-vertices-and-edges"></a>Adicionar vértices e bordas

Vamos examinar as instruções Gremlin mostradas na seção anterior em mais detalhes. Primeiro, temos alguns vértices usando o método `addV` do Gremlin. Por exemplo, o trecho a seguir cria um vértice "Thomas Andersen" do tipo "Pessoa", com propriedades de nome, sobrenome e idade.

```cs
// Create a vertex
IDocumentQuery<Vertex> createVertexQuery = client.CreateGremlinQuery<Vertex>(
    graphCollection, 
    "g.addV('person').property('firstName', 'Thomas')");

while (createVertexQuery.HasMoreResults)
{
    Vertex thomas = (await create.ExecuteNextAsync<Vertex>()).First();
}
```

Em seguida, podemos criar algumas arestas entre esses vértices usando o método `addE` do Gremlin. 

```cs
// Add a "knows" edge
IDocumentQuery<Edge> createEdgeQuery = client.CreateGremlinQuery<Edge>(
    graphCollection, 
    "g.V('thomas').addE('knows').to(g.V('mary'))");

while (create.HasMoreResults)
{
    Edge thomasKnowsMaryEdge = (await create.ExecuteNextAsync<Edge>()).First();
}
```

Podemos atualizar um vértice existente usando a etapa `properties` no Gremlin. Podemos ignorar a chamada para executar a consulta por meio de `HasMoreResults` e `ExecuteNextAsync` para o restante dos exemplos.

```cs
// Update a vertex
client.CreateGremlinQuery<Vertex>(
    graphCollection, 
    "g.V('thomas').property('age', 45)");
```

Você pode remover bordas e vértices usando a etapa `drop` do Gremlin. Aqui está um trecho que mostra como excluir um vértice e uma borda. Observe que descartar um vértice executa uma exclusão em cascata das bordas associadas.

```cs
// Drop an edge
client.CreateGremlinQuery(graphCollection, "g.E('thomasKnowsRobin').drop()");

// Drop a vertex
client.CreateGremlinQuery(graphCollection, "g.V('robin').drop()");
```

## <a name="query-the-graph"></a>Consultar o gráfico

Você também pode executar consultas e passagens usando Gremlin. Por exemplo, o trecho a seguir mostra como contar o número de vértices no gráfico:

```cs
// Run a query to count vertices
IDocumentQuery<int> countQuery = client.CreateGremlinQuery<int>(graphCollection, "g.V().count()");
```
Execute filtros usando as etapas `has` e `hasLabel` do Gremlin e combine-as usando `and`, `or` e `not` para compilar filtros mais complexos:

```cs
// Run a query with filter
IDocumentQuery<Vertex> personsByAge = client.CreateGremlinQuery<Vertex>(
  graphCollection, 
  "g.V().hasLabel('person').has('age', gt(40))");
```

Você pode projetar certas propriedades nos resultados da consulta usando a etapa `values`:

```cs
// Run a query with projection
IDocumentQuery<string> firstNames = client.CreateGremlinQuery<string>(
  graphCollection, 
  $"g.V().hasLabel('person').values('firstName')");
```

Até agora, só vimos operadores de consulta que funcionam em qualquer banco de dados. Os gráficos são rápidos e eficientes para operações de passagem quando você precisa navegar até os vértices e bordas relacionadas. Vamos encontrar todos os amigos de Thomas. Fazemos isso usando a etapa `outE` do Gremlin para localizar todos as bordas externas de Thomas, depois passamos para os vértices internos dessas bordas usando a etapa `inV` do Gremlin:

```cs
// Run a traversal (find friends of Thomas)
IDocumentQuery<Vertex> friendsOfThomas = client.CreateGremlinQuery<Vertex>(
  graphCollection,
  "g.V('thomas').outE('knows').inV().hasLabel('person')");
```

A próxima consulta executa dois saltos para localizar todos os "amigos dos amigos" de Thomas, chamando `outE` e `inV` duas vezes. 

```cs
// Run a traversal (find friends of friends of Thomas)
IDocumentQuery<Vertex> friendsOfFriendsOfThomas = client.CreateGremlinQuery<Vertex>(
  graphCollection,
  "g.V('thomas').outE('knows').inV().hasLabel('person').outE('knows').inV().hasLabel('person')");
```

Você pode criar consultas mais complexas e implementar uma lógica avançada de passagem de gráfico usando o Gremlin, incluindo a combinação de expressões de filtro, executando o loop usando a etapa `loop` e implementando a navegação condicional usando a etapa `choose`. Saiba mais sobre o que você pode fazer com o [Suporte do Gremlin](gremlin-support.md)!

Isso é tudo, este tutorial do Azure Cosmos DB está concluído! 

## <a name="clean-up-resources"></a>Limpar recursos

Se você não continuar usando este aplicativo, siga as seguintes etapas para excluir todos os recursos criados neste tutorial no portal do Azure.  

1. No menu à esquerda no portal do Azure, clique em **Grupos de recursos** e depois clique no nome do recurso criado. 
2. Em sua página de grupo de recursos, clique em **Excluir**, digite o nome do recurso para excluir na caixa de texto e depois clique em **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você fez o seguinte:

> [!div class="checklist"]
> * Criou uma conta do Azure Cosmos DB 
> * Criou um banco de dados do gráfico e um contêiner
> * Serializou vértices e bordas para os objetos .NET
> * Adicionou vértices e bordas
> * Consultou o gráfico usando Gremlin

Agora, você pode criar consultas mais complexas e implementar uma lógica de passagem de gráfico avançada usando o Gremlin. 

> [!div class="nextstepaction"]
> [Consultar usando o Gremlin](tutorial-query-graph.md)
