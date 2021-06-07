---
title: O feed de alterações na API de Azure Cosmos DB para Cassandra
description: Saiba como usar o feed de alterações na API de Azure Cosmos DB para Cassandra para obter as alterações feitas em seus dados.
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 11/25/2019
ms.author: thvankra
ms.openlocfilehash: 10f037dddcce43a1e023982af816660bd325d57f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97605086"
---
# <a name="change-feed-in-the-azure-cosmos-db-api-for-cassandra"></a>O feed de alterações na API de Azure Cosmos DB para Cassandra
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

O suporte do [feed de alterações](change-feed.md) na API Azure Cosmos DB para Cassandra está disponível por meio de predicados de consulta na Cassandra (linguagem de consulta do CQL). Usando essas condições de predicado, você pode consultar a API do feed de alterações. Os aplicativos podem obter as alterações feitas em uma tabela usando a chave primária (também conhecida como chave de partição), conforme necessário em CQL. Em seguida, você pode executar ações adicionais com base nos resultados. As alterações nas linhas da tabela são capturadas na ordem de seu tempo de modificação e na ordem de classificação por chave de partição.

O exemplo a seguir mostra como obter um feed de alterações em todas as linhas em uma API do Cassandra tabela de keyspace usando o .NET. O predicado COSMOS_CHANGEFEED_START_TIME () é usado diretamente em CQL para consultar itens no feed de alterações a partir de uma hora de início especificada (neste caso, DateTime atual). Você pode baixar o exemplo completo, para C# [aqui](/samples/azure-samples/azure-cosmos-db-cassandra-change-feed/cassandra-change-feed/) e para Java [aqui](https://github.com/Azure-Samples/cosmos-changefeed-cassandra-java).

Em cada iteração, a consulta é retomada no último ponto em que as alterações foram lidas, usando o estado de paginação. Podemos ver um fluxo contínuo de novas alterações na tabela no keyspace. Veremos as alterações nas linhas inseridas ou atualizadas. Não há suporte para a observação de operações de exclusão usando o feed de alterações no API do Cassandra no momento.

# <a name="java"></a>[Java](#tab/java)

```java
    Session cassandraSession = utils.getSession();

    try {
        DateTimeFormatter dtf = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");  
        LocalDateTime now = LocalDateTime.now().minusHours(6).minusMinutes(30);  
        String query="SELECT * FROM uprofile.user where COSMOS_CHANGEFEED_START_TIME()='" 
            + dtf.format(now)+ "'";
        
        byte[] token=null; 
        System.out.println(query); 
        while(true)
        {
            SimpleStatement st=new  SimpleStatement(query);
            st.setFetchSize(100);
            if(token!=null)
                st.setPagingStateUnsafe(token);
            
            ResultSet result=cassandraSession.execute(st) ;
            token=result.getExecutionInfo().getPagingState().toBytes();
            
            for(Row row:result)
            {
                System.out.println(row.getString("user_name"));
            }
        }
    } finally {
        utils.close();
        LOGGER.info("Please delete your table after verifying the presence of the data in portal or from CQL");
    }
```

# <a name="c"></a>[C#](#tab/csharp)

```C#
    //set initial start time for pulling the change feed
     DateTime timeBegin = DateTime.UtcNow;

    //initialise variable to store the continuation token
    byte[] pageState = null;
    while (true)
    {
        try
        {

            //Return the latest change for all rows in 'user' table    
            IStatement changeFeedQueryStatement = new SimpleStatement(
            $"SELECT * FROM uprofile.user where COSMOS_CHANGEFEED_START_TIME() = '{timeBegin.ToString("yyyy-MM-ddTHH:mm:ss.fffZ", CultureInfo.InvariantCulture)}'");
            if (pageState != null)
            {
                changeFeedQueryStatement = changeFeedQueryStatement.SetPagingState(pageState);
            }
            Console.WriteLine("getting records from change feed at last page state....");
            RowSet rowSet = session.Execute(changeFeedQueryStatement);

            //store the continuation token here
            pageState = rowSet.PagingState;

            List<Row> rowList = rowSet.ToList();
            if (rowList.Count != 0)
            {
                for (int i = 0; i < rowList.Count; i++)
                {
                    string value = rowList[i].GetValue<string>("user_name");
                    int key = rowList[i].GetValue<int>("user_id");
                    // do something with the data - e.g. compute, forward to another event, function, etc.
                    // here, we just print the user name field
                    Console.WriteLine("user_name: " + value);
                }
            }
            else
            {
                Console.WriteLine("zero documents read");
            }
        }
        catch (Exception e)
        {
            Console.WriteLine("Exception " + e);
        }
    }

```
---

Para obter as alterações em uma única linha por chave primária, você pode adicionar a chave primária na consulta. O exemplo a seguir mostra como controlar as alterações para a linha em que "user_id = 1"

# <a name="c"></a>[C#](#tab/csharp)

```C#
    //Return the latest change for all row in 'user' table where user_id = 1
    IStatement changeFeedQueryStatement = new SimpleStatement(
    $"SELECT * FROM uprofile.user where user_id = 1 AND COSMOS_CHANGEFEED_START_TIME() = '{timeBegin.ToString("yyyy-MM-ddTHH:mm:ss.fffZ", CultureInfo.InvariantCulture)}'");

```

# <a name="java"></a>[Java](#tab/java)

```java
    String query="SELECT * FROM uprofile.user where user_id=1 and COSMOS_CHANGEFEED_START_TIME()='" 
                       + dtf.format(now)+ "'";
    SimpleStatement st=new  SimpleStatement(query);
```
---
## <a name="current-limitations"></a>Limitações atuais

As seguintes limitações são aplicáveis ao usar o feed de alterações com API do Cassandra:

* Atualmente, há suporte para inserções e atualizações. Ainda não há suporte para a operação de exclusão. Como alternativa, você pode adicionar um marcador flexível em linhas que estão sendo excluídas. Por exemplo, adicione um campo na linha chamada "Deleted" e defina-o como "true".
* A última atualização é persistida como na principal API do SQL e as atualizações intermediárias para a entidade não estão disponíveis.


## <a name="error-handling"></a>Tratamento de erros

Há suporte para os seguintes códigos de erro e mensagens ao usar o feed de alterações no API do Cassandra:

* **Código de erro HTTP 429** -quando o feed de alterações é limitado pela taxa, ele retorna uma página vazia.

## <a name="next-steps"></a>Próximas etapas

* [Gerenciar recursos da API do Cassandra do Azure Cosmos DB usando modelos do Azure Resource Manager](./templates-samples-cassandra.md)