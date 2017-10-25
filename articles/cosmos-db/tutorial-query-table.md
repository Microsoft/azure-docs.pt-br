---
title: Como consultar dados de tabela no Azure Cosmos DB? | Microsoft Docs
description: Saiba como consultar dados de tabela no Azure Cosmos DB
services: cosmos-db
documentationcenter: 
author: kanshiG
manager: jhubbard
editor: 
tags: 
ms.assetid: 14bcb94e-583c-46f7-9ea8-db010eb2ab43
ms.service: cosmos-db
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 05/10/2017
ms.author: govindk
ms.openlocfilehash: e59cfa85c6bf584e44bdc6e88cc19d67df390041
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cosmos-db-how-to-query-table-data-by-using-the-table-api-preview"></a>Azure Cosmos DB: Como consultar os dados da tabela utilizando a API de Tabela (versão prévia)?

A [API de Tabela](table-introduction.md) (visualização) do Azure Cosmos DB oferece suporte a consultas de OData e [LINQ](https://docs.microsoft.com/rest/api/storageservices/fileservices/writing-linq-queries-against-the-table-service) em dados de chave/valor (tabela).  

Este artigo aborda as seguintes tarefas: 

> [!div class="checklist"]
> * Consultar dados com a API de Tabela

As consultas neste artigo usam a seguinte tabela de exemplo `People`:

| PartitionKey | RowKey | Email | PhoneNumber |
| --- | --- | --- | --- |
| Harp | Walter | Walter@contoso.com| 425-555-0101 |
| Smith | Ben | Ben@contoso.com| 425-555-0102 |
| Smith | Jeff | Jeff@contoso.com| 425-555-0104 | 

Como o Azure Cosmos DB é compatível com as APIs de Armazenamento de Tabelas do Azure, consulte [Consultar tabelas e entidades] (https://docs.microsoft.com/rest/api/storageservices/fileservices/querying-tables-and-entities) para obter detalhes sobre como consultar utilizando a API de Tabela. 

Para obter mais informações sobre os recursos premium que o Azure Cosmos DB oferece, consulte [Azure Cosmos DB: API de Tabela](table-introduction.md) e [Desenvolver com a API de Tabela em .NET](tutorial-develop-table-dotnet.md). 

## <a name="prerequisites"></a>Pré-requisitos

Para essas consultas funcionarem, você deve ter uma conta do Azure Cosmos DB e ter dados de entidade no contêiner. Não tenho nenhum deles? Complete o [Guia de início rápido de cinco minutos](https://aka.ms/acdbtnetqs) ou o [tutorial de desenvolvedor](https://aka.ms/acdbtabletut) para criar uma conta e preencher seu banco de dados.

## <a name="query-on-partitionkey-and-rowkey"></a>Consultar em PartitionKey e RowKey
Como as propriedades PartitionKey e RowKey formam a chave primária de uma entidade, é possível utilizar a seguinte sintaxe especial para identificar a entidade: 

**Consulta**

```
https://<mytableendpoint>/People(PartitionKey='Harp',RowKey='Walter')  
```
**Resultados**

| PartitionKey | RowKey | Email | PhoneNumber |
| --- | --- | --- | --- |
| Harp | Walter | Walter@contoso.com| 425-555-0104 |

Como alternativa, você pode especificar essas propriedades como parte da opção `$filter`, conforme mostra a seção a seguir. Observe que os nomes de propriedade de chave e valores constantes diferenciam maiúsculas de minúsculas. As propriedades PartitionKey e RowKey são do tipo Cadeia de caracteres. 

## <a name="query-by-using-an-odata-filter"></a>Consultar utilizando um filtro OData
Ao construir uma cadeia de caracteres de filtro, lembre-se destas regras: 

* Use os operadores lógicos definidos pela Especificação do Protocolo OData para comparar uma propriedade a um valor. Observe que você não pode comparar uma propriedade com um valor dinâmico. Um lado da expressão deve ser uma constante. 
* O nome da propriedade, o operador e um valor constante devem ser separados por espaços codificados por URL. Um espaço é codificado por URL como `%20`. 
* Todas as partes da cadeia de caracteres de filtro diferenciam maiúsculas de minúsculas. 
* O valor da constante deve ser do mesmo tipo de dados como a propriedade para que o filtro retorne resultados válidos. Para obter informações sobre tipos de propriedades com suporte, consulte [Noções básicas sobre o modelo de dados do serviço Tabela](https://docs.microsoft.com/rest/api/storageservices/understanding-the-table-service-data-model). 

Veja um exemplo de consulta que mostra como filtrar por PartitionKey e as propriedades de Email usando um OData `$filter`.

**Consulta**

```
https://<mytableapi-endpoint>/People()?$filter=PartitionKey%20eq%20'Smith'%20and%20Email%20eq%20'Ben@contoso.com'
```

Para obter mais informações sobre como construir expressões de filtro para vários tipos de dados, consulte [Consultar tabelas e entidades](https://docs.microsoft.com/rest/api/storageservices/querying-tables-and-entities).

**Resultados**

| PartitionKey | RowKey | Email | PhoneNumber |
| --- | --- | --- | --- |
| Ben |Smith | Ben@contoso.com| 425-555-0102 |

## <a name="query-by-using-linq"></a>Consultar utilizando LINQ 
Você também pode consultar utilizando o LINQ, o que resulta em expressões de consulta Odata correspondentes. Veja um exemplo de como criar consultas usando o SDK do .NET.:

```csharp
CloudTableClient tableClient = account.CreateCloudTableClient();
CloudTable table = tableClient.GetTableReference("people");

TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>()
    .Where(
        TableQuery.CombineFilters(
            TableQuery.GenerateFilterCondition(PartitionKey, QueryComparisons.Equal, "Smith"),
            TableOperators.And,
            TableQuery.GenerateFilterCondition(Email, QueryComparisons.Equal,"Ben@contoso.com")
    ));

await table.ExecuteQuerySegmentedAsync<CustomerEntity>(query, null);
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você fez o seguinte:

> [!div class="checklist"]
> * Aprendeu a consultar utilizando a API de Tabela (versão prévia) 

Agora você pode prosseguir para o próximo tutorial e aprender a distribuir seus dados globalmente.

> [!div class="nextstepaction"]
> [Distribuir os dados globalmente](tutorial-global-distribution-documentdb.md)
