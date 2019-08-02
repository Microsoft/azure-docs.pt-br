---
title: Execução de consulta SQL no Azure Cosmos DB
description: Saiba mais sobre a execução da consulta SQL no Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: tisande
ms.openlocfilehash: e4e26b658bd29e4589be40e4d29935059836c909
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342799"
---
# <a name="azure-cosmos-db-sql-query-execution"></a>Execução de consulta SQL do Cosmos DB do Azure

Qualquer linguagem capacitada para fazer solicitações HTTP/HTTPS pode chamar a API REST do Cosmos DB. O cosmos DB também oferece bibliotecas de programação para linguagens de programação .NET, Node. js, JavaScript e Python. A API REST e bibliotecas dão suporte a consultas por meio do SQL, e o SDK do .NET também suporta [consulta LINQ](sql-query-linq-to-sql.md).

Os exemplos a seguir mostram como criar uma consulta e enviá-la a uma conta de banco de dados do Cosmos DB.

## <a id="REST-API"></a>API REST

O Cosmos DB oferece um modelo de programação RESTful por HTTP. O modelo de recurso consiste em um conjunto de recursos em uma conta de banco de dados, que provisiona uma assinatura do Azure. A conta de banco de dados consiste em um conjunto de *bancos de dados*, cada um deles pode conter vários *contêineres*, que por sua vez contêm *itens*, UDFs e outros tipos de recursos. Cada recurso do Cosmos DB é acessível usando um URI lógico e estável. Um conjunto de recursos é chamado um *feed*. 

O modelo de interação básico com esses recursos é por meio dos verbos HTTP `GET`, `PUT`, `POST`, e `DELETE`, com suas interpretações padrão. Use `POST` para criar um novo recurso, executar um procedimento armazenado ou emitir uma consulta do Cosmos DB. As consultas sempre são operações somente leitura, sem efeitos colaterais.

Os exemplos a seguir mostram um `POST` para uma consulta de API do SQL em relação a itens de exemplo. A consulta tem um filtro simples no JSON `name` propriedade. O `x-ms-documentdb-isquery` e Content-Type: `application/query+json` cabeçalhos indicam que a operação é uma consulta. Substitua `mysqlapicosmosdb.documents.azure.com:443` com o URI para sua conta do Cosmos DB.

```json
    POST https://mysqlapicosmosdb.documents.azure.com:443/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {
        "query": "SELECT * FROM Families f WHERE f.id = @familyId",
        "parameters": [
            {"name": "@familyId", "value": "AndersenFamily"}
        ]
    }
```

Os resultados são:

```json
    HTTP/1.1 200 Ok
    x-ms-activity-id: 8b4678fa-a947-47d3-8dd3-549a40da6eed
    x-ms-item-count: 1
    x-ms-request-charge: 0.32

    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "id":"AndersenFamily",
             "lastName":"Andersen",
             "parents":[  
                {  
                   "firstName":"Thomas"
                },
                {  
                   "firstName":"Mary Kay"
                }
             ],
             "children":[  
                {  
                   "firstName":"Henriette Thaulow",
                   "gender":"female",
                   "grade":5,
                   "pets":[  
                      {  
                         "givenName":"Fluffy"
                      }
                   ]
                }
             ],
             "address":{  
                "state":"WA",
                "county":"King",
                "city":"Seattle"
             },
             "_rid":"u1NXANcKogEcAAAAAAAAAA==",
             "_ts":1407691744,
             "_self":"dbs\/u1NXAA==\/colls\/u1NXANcKogE=\/docs\/u1NXANcKogEcAAAAAAAAAA==\/",
             "_etag":"00002b00-0000-0000-0000-53e7abe00000",
             "_attachments":"_attachments\/"
          }
       ],
       "count":1
    }
```

A consulta em seguida, mais complexa retorna vários resultados de uma junção:

```json
    POST https://https://mysqlapicosmosdb.documents.azure.com:443/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {
        "query": "SELECT
                     f.id AS familyName,
                     c.givenName AS childGivenName,
                     c.firstName AS childFirstName,
                     p.givenName AS petName
                  FROM Families f
                  JOIN c IN f.children
                  JOIN p in c.pets",
        "parameters": []
    }
```

Os resultados são: 

```json
    HTTP/1.1 200 Ok
    x-ms-activity-id: 568f34e3-5695-44d3-9b7d-62f8b83e509d
    x-ms-item-count: 1
    x-ms-request-charge: 7.84

    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "familyName":"AndersenFamily",
             "childFirstName":"Henriette Thaulow",
             "petName":"Fluffy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Goofy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Shadow"
          }
       ],
       "count":3
    }
```

Se os resultados de uma consulta pode não caberem em uma única página, a API REST retorna um token de continuação por meio de `x-ms-continuation-token` cabeçalho de resposta. Os clientes podem paginar os resultados incluindo o cabeçalho nos resultados subsequentes. Você também pode controlar o número de resultados por página por meio de `x-ms-max-item-count` cabeçalho do número.

Se uma consulta tem uma função de agregação como contagem, a página de consulta pode retornar um valor parcialmente agregado em apenas uma página de resultados. Os clientes devem executar uma segunda agregação nesses resultados para produzir os resultados finais. Por exemplo, soma das contagens retornadas nas páginas individuais para retornar a contagem total.

Para gerenciar a política de consistência de dados para consultas, use o `x-ms-consistency-level` cabeçalho como em todas as solicitações da API REST. Consistência de sessão também requer ecoar a versão mais recente `x-ms-session-token` cabeçalho de cookie na solicitação de consulta. A política de indexação do contêiner consultado também pode influenciar a consistência dos resultados da consulta. Com as configurações de política para contêineres de indexação padrão, o índice sempre é atualizado com o conteúdo do item e os resultados da consulta correspondem à consistência escolhida para os dados. Para obter mais informações, consulte [níveis de consistência do BD Cosmos do Azure] [níveis de consistência].

Se a política de indexação configurada no contêiner não oferecer suporte a consulta especificada, o servidor do Azure Cosmos DB retornará 400 "solicitação incorreta". Retorna essa mensagem de erro para consultas com caminhos excluídos explicitamente da indexação. Você pode especificar o `x-ms-documentdb-query-enable-scan` cabeçalho para permitir que a consulta executar uma verificação quando um índice não está disponível.

Você pode obter métricas detalhadas na execução da consulta, definindo o `x-ms-documentdb-populatequerymetrics` cabeçalho para `true`. Para obter mais informações, consulte [Métricas de consulta SQL para o Azure Cosmos DB](sql-api-query-metrics.md).

## <a name="c-net-sdk"></a>C# (.NET SDK)

O SDK .NET suporta consultas LINQ e SQL. O exemplo a seguir mostra como executar a consulta de filtro anterior com o .NET:

```csharp
    foreach (var family in client.CreateDocumentQuery(containerLink,
        "SELECT * FROM Families f WHERE f.id = \"AndersenFamily\""))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    SqlQuerySpec query = new SqlQuerySpec("SELECT * FROM Families f WHERE f.id = @familyId");
    query.Parameters = new SqlParameterCollection();
    query.Parameters.Add(new SqlParameter("@familyId", "AndersenFamily"));

    foreach (var family in client.CreateDocumentQuery(containerLink, query))
    {
        Console.WriteLine("\tRead {0} from parameterized SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery(containerLink)
        where f.Id == "AndersenFamily"
        select f))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in client.CreateDocumentQuery(containerLink)
        .Where(f => f.Id == "AndersenFamily")
        .Select(f => f))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }
```

O exemplo a seguir compara duas propriedades quanto à igualdade dentro de cada item e usa projeções anônimas.

```csharp
    foreach (var family in client.CreateDocumentQuery(containerLink,
        @"SELECT {""Name"": f.id, ""City"":f.address.city} AS Family
        FROM Families f
        WHERE f.address.city = f.address.state"))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery<Family>(containerLink)
        where f.address.city == f.address.state
        select new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in
        client.CreateDocumentQuery<Family>(containerLink)
        .Where(f => f.address.city == f.address.state)
        .Select(f => new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }
```

O exemplo seguinte mostra junções, expressadas por meio de LINQ `SelectMany`.

```csharp
    foreach (var pet in client.CreateDocumentQuery(containerLink,
          @"SELECT p
            FROM Families f
                 JOIN c IN f.children
                 JOIN p in c.pets
            WHERE p.givenName = ""Shadow"""))
    {
        Console.WriteLine("\tRead {0} from SQL", pet);
    }

    // Equivalent in Lambda expressions:
    foreach (var pet in
        client.CreateDocumentQuery<Family>(containerLink)
        .SelectMany(f => f.children)
        .SelectMany(c => c.pets)
        .Where(p => p.givenName == "Shadow"))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", pet);
    }
```

O cliente .NET itera automaticamente em todas as páginas de resultados da consulta no `foreach` bloqueia, conforme mostrado no exemplo anterior. As opções de consulta introduzidas na [API REST](#REST-API) seção também estão disponíveis no SDK do .NET, usando o `FeedOptions` e `FeedResponse` classes no `CreateDocumentQuery` método. Você pode controlar o número de páginas usando o `MaxItemCount` configuração.

Você também pode controlar explicitamente paginação criando `IDocumentQueryable` usando o `IQueryable` objeto, em seguida, lendo os `ResponseContinuationToken` valores e transmiti-los de volta como `RequestContinuationToken` em `FeedOptions`. Você pode definir `EnableScanInQuery` para permitir verificações quando a consulta não é compatível com a política de indexação configurada. Para contêineres particionados, você pode usar `PartitionKey` para executar a consulta em relação a uma única partição, embora o Azure Cosmos DB pode extraí-la do texto da consulta. Você pode usar `EnableCrossPartitionQuery` para executar consultas em várias partições.

Para obter mais exemplos de .NET com consultas, consulte o [amostras do .NET do Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-dotnet) no GitHub.

## <a id="JavaScript-server-side-API"></a>API do lado servidor do JavaScript

O Azure Cosmos DB fornece um modelo de programação para [executar o JavaScript com base no aplicativo](stored-procedures-triggers-udfs.md) lógica diretamente em contêineres, usando procedimentos armazenados e gatilhos. A lógica de JavaScript registrada no nível do contêiner pode então emitir operações de banco de dados nos itens do contêiner determinado, encapsuladas em transações ACID ambientes.

O exemplo a seguir mostra como usar `queryDocuments` no servidor do JavaScript API para realizar consultas de dentro de procedimentos e gatilhos armazenados:

```javascript
    function findName(givenName, familyName) {
        var context = getContext();
        var containerManager = context.getCollection();
        var containerLink = containerManager.getSelfLink()

        // create a new item.
        containerManager.createDocument(containerLink,
            { givenName: givenName, familyName: familyName },
            function (err, documentCreated) {
                if (err) throw new Error(err.message);

                // filter items by familyName
                var filterQuery = "SELECT * from root r WHERE r.familyName = 'Wakefield'";
                containerManager.queryDocuments(containerLink,
                    filterQuery,
                    function (err, matchingDocuments) {
                        if (err) throw new Error(err.message);
    context.getResponse().setBody(matchingDocuments.length);

                        // Replace the familyName for all items that satisfied the query.
                        for (var i = 0; i < matchingDocuments.length; i++) {
                            matchingDocuments[i].familyName = "Robin Wakefield";
                            // we don't need to execute a callback because they are in parallel
                            containerManager.replaceDocument(matchingDocuments[i]._self,
                                matchingDocuments[i]);
                        }
                    })
            });
    }
```

## <a name="next-steps"></a>Próximas etapas

- [Introdução ao Azure Cosmos DB](introduction.md)
- [Amostras do .NET no Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Níveis de consistência do Azure Cosmos DB](consistency-levels.md)
