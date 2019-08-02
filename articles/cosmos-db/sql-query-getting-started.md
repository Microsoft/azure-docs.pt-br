---
title: Introdução às consultas SQL no Azure Cosmos DB
description: Introdução a consultas SQL
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: tisande
ms.openlocfilehash: 87b275806c06443e37e9e92c35a38b4cde378322
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342921"
---
# <a name="getting-started-with-sql-queries"></a>Introdução às consultas SQL

As contas do Azure Cosmos DB SQL API suportam a itens de consultas usando a linguagem SQL (Structured Query) como uma linguagem de consulta JSON. As metas de design da linguagem de consulta do Azure Cosmos DB são:

* Dá suporte ao SQL, uma das linguagens de consulta mais conhecidas e populares, em vez de inventar uma nova linguagem de consulta. SQL fornece um modelo de programação formal para consultas avançadas em itens JSON.  

* Use o modelo de programação do JavaScript como a base para a linguagem de consulta. Sistema de tipos, avaliação de expressão e invocação de função do JavaScript são as raízes de API do SQL. Essas raízes fornecem um modelo de programação natural para recursos como projeções relacionais, navegação hierárquica entre itens JSON, autojunções, consultas espaciais e invocação de funções definidas pelo usuário (UDFs) gravadas inteiramente em JavaScript.

## <a name="upload-sample-data"></a>Carregar dados de exemplo

Em sua conta do Cosmos DB SQL API, crie um contêiner chamado `Families`. Crie dois itens simples de JSON no contêiner. Você pode executar a maioria dos exemplos de consulta nos documentos de consulta do Azure Cosmos DB usando esse conjunto de dados.

### <a name="create-json-items"></a>Criar itens de JSON

O código a seguir cria dois itens JSON simples sobre famílias. Os itens JSON simples para as famílias Andersen e Barros incluem os pais, filhos e seus animais de estimação, endereço e informações de registro. O primeiro item tem cadeias de caracteres, números, boolianos, matrizes e propriedades aninhadas.


```json
{
  "id": "AndersenFamily",
  "lastName": "Andersen",
  "parents": [
     { "firstName": "Thomas" },
     { "firstName": "Mary Kay"}
  ],
  "children": [
     {
         "firstName": "Henriette Thaulow",
         "gender": "female",
         "grade": 5,
         "pets": [{ "givenName": "Fluffy" }]
     }
  ],
  "address": { "state": "WA", "county": "King", "city": "Seattle" },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

O segundo item usa `givenName` e `familyName` em vez de `firstName` e `lastName`.

```json
{
  "id": "WakefieldFamily",
  "parents": [
      { "familyName": "Wakefield", "givenName": "Robin" },
      { "familyName": "Miller", "givenName": "Ben" }
  ],
  "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [
            { "givenName": "Goofy" },
            { "givenName": "Shadow" }
        ]
      },
      { 
        "familyName": "Miller",
         "givenName": "Lisa",
         "gender": "female",
         "grade": 8 }
  ],
  "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
  "creationDate": 1431620462,
  "isRegistered": false
}
```

### <a name="query-the-json-items"></a>Consultar os itens JSON

Tente algumas consultas nos dados JSON para entender alguns dos principais aspectos da linguagem de consulta SQL do Azure Cosmos DB.

A consulta a seguir retorna os itens onde a `id` correspondências de campo `AndersenFamily`. Uma vez que ele é um `SELECT *` consulta, a saída da consulta é o item JSON completo. Para obter mais informações sobre a sintaxe SELECT, consulte [instrução SELECT](sql-query-select.md). 

```sql
    SELECT *
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Os resultados da consulta são: 

```json
    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "Seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]
```

A consulta a seguir reformata a saída JSON em uma forma diferente. A consulta em um novo JSON `Family` objeto com dois campos selecionados, `Name` e `City`, quando a cidade do endereço é o mesmo que o estado. Nesse caso, corresponde a "NY, NY".

```sql
    SELECT {"Name":f.id, "City":f.address.city} AS Family
    FROM Families f
    WHERE f.address.city = f.address.state
```

Os resultados da consulta são:

```json
    [{
        "Family": {
            "Name": "WakefieldFamily",
            "City": "NY"
        }
    }]
```

A consulta a seguir retorna todos os nomes dos filhos na família cuja `id` corresponde a `WakefieldFamily`, ordenada por cidade.

```sql
    SELECT c.givenName
    FROM Families f
    JOIN c IN f.children
    WHERE f.id = 'WakefieldFamily'
    ORDER BY f.address.city ASC
```

Os resultados são:

```json
    [
      { "givenName": "Jesse" },
      { "givenName": "Lisa"}
    ]
```

## <a name="remarks"></a>Comentários

Os exemplos anteriores mostram vários aspectos da linguagem de consulta do Cosmos DB:  

* Uma vez que a API do SQL trabalha com valores JSON, ele lida com entidades em forma de árvore em vez de linhas e colunas. Você pode consultar os nós de árvore em qualquer profundidade arbitrária, como `Node1.Node2.Node3…..Nodem`, de forma semelhante à referência de duas partes de `<table>.<column>` no ANSI SQL.

* Como funciona a linguagem de consulta com o repositório de dados, o sistema de tipo deve ser vinculado dinamicamente. A mesma expressão pode obter tipos diferentes em itens diferentes. O resultado de uma consulta é um valor JSON válido, mas não é garantido para ser de um esquema fixo.  

* O Azure Cosmos DB dá suporte somente a itens JSON estritos. O sistema de tipos e as expressões são restritas para lidar somente com tipos JSON. Para obter mais informações, consulte o [especificação JSON](https://www.json.org/).  

* Um contêiner do Cosmos DB é uma coleção de itens JSON sem esquema. As relações de dentro e entre itens de contêiner são capturadas implicitamente pela contenção, não pela chave primária e as relações de chave estrangeiras. Esse recurso é importante para as junções de intra-item, discutidas posteriormente neste artigo.

## <a name="next-steps"></a>Próximas etapas

- [Introdução ao Azure Cosmos DB](introduction.md)
- [Amostras do .NET no Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Cláusula SELECT](sql-query-select.md)
