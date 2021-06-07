---
title: Sintaxe e recursos compatíveis da API do Azure Cosmos DB para MongoDB (versão 3.2)
description: Saiba mais sobre a sintaxe e os recursos compatíveis da API do Azure Cosmos DB para MongoDB (versão 3.2).
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: overview
ms.date: 10/16/2019
author: sivethe
ms.author: sivethe
ms.openlocfilehash: ce6b01a99c29020b0ede5a860da9d3d6f37e9f06
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104605742"
---
# <a name="azure-cosmos-dbs-api-for-mongodb-32-version-supported-features-and-syntax"></a>API do Azure Cosmos DB para MongoDB (versão 3.2): sintaxe e recursos compatíveis
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

O Azure Cosmos DB é o serviço de banco de dados multimodelo distribuído globalmente da Microsoft. Você pode se comunicar com a API do Azure Cosmos DB para MongoDB usando qualquer um dos [drivers](https://docs.mongodb.org/ecosystem/drivers) de cliente de software livre do MongoDB. A API do Azure Cosmos DB para MongoDB permite o uso de drivers de cliente existentes ao aderir ao [protocolo de transmissão](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol) do MongoDB.

Usando a API do Azure Cosmos DB para MongoDB, você pode aproveitar os benefícios do MongoDB com os quais está acostumado, com todas as funcionalidades empresariais que o Cosmos DB oferece: [distribuição global](distribute-data-globally.md), [fragmentação automática](partitioning-overview.md), garantias de disponibilidade e latência, indexação automática de cada campo, criptografia em repouso, backups e muito mais.

> [!NOTE]
> A versão 3.2 da API do Cosmos DB para MongoDB não tem planos atuais para EOL (fim da vida útil). O aviso mínimo para um futuro EOL é de três anos.

## <a name="protocol-support"></a>Suporte de protocolo

Todas as novas contas da API do Azure Cosmos DB para MongoDB são compatíveis com o servidor MongoDB versão **3.6**. Este artigo aborda o MongoDB versão 3.2. Os operadores com suporte e qualquer limitação ou exceções estão listadas abaixo. Qualquer driver de cliente que entenda esses protocolos poderá conectar-se à API do Azure Cosmos DB para MongoDB. 

A API do Azure Cosmos DB para MongoDB também oferece uma experiência de atualização direta para contas qualificadas. Saiba mais sobre o [guia de atualização de versão MongoDB](mongodb-version-upgrade.md).

## <a name="query-language-support"></a>Suporte de linguagem de consulta

A API do Azure Cosmos DB para MongoDB fornece um suporte abrangente para construções de linguagem de consulta do MongoDB. Abaixo, você encontrará uma lista detalhada de operações, operadores, estágios, comandos e opções atualmente compatíveis.

## <a name="database-commands"></a>Comandos de banco de dados

A API do Azure Cosmos DB para MongoDB é compatível com os seguintes comandos de banco de dados:

> [!NOTE]
> Este artigo lista apenas os comandos de servidor com suporte e exclui as funções de wrapper do lado do cliente. Funções de wrapper do lado do cliente, como `deleteMany()` e `updateMany()`, utilizam internamente os comandos de servidor `delete()` e `update()`. As funções que utilizam comandos de servidor com suporte são compatíveis com a API do Azure Cosmos DB para MongoDB.

### <a name="query-and-write-operation-commands"></a>Comandos de operação de consulta e gravação

- excluir
- localizar
- findAndModify
- getLastError
- getMore
- insert
- atualizar

### <a name="authentication-commands"></a>Comandos de autenticação

- logout
- authenticate
- getnonce

### <a name="administration-commands"></a>Comandos de administração

- dropDatabase
- listCollections
- drop
- create
- filemd5
- createIndexes
- listIndexes
- dropIndexes
- ConnectionStatus
- reIndex

### <a name="diagnostics-commands"></a>Comandos de diagnóstico

- buildInfo
- collStats
- dbStats
- hostInfo
- listDatabases
- whatsmyuri

<a name="aggregation-pipeline"></a>

## <a name="aggregation-pipelinea"></a>Pipeline de agregação</a>

O Cosmos DB dá suporte ao pipeline de agregação para o MongoDB 3.2 na versão prévia pública. Consulte o [blog do Azure](https://azure.microsoft.com/blog/azure-cosmosdb-extends-support-for-mongodb-aggregation-pipeline-unique-indexes-and-more/) para obter instruções sobre como integrar a visualização pública.

### <a name="aggregation-commands"></a>Comandos de agregação

- aggregate
- count
- distinct

### <a name="aggregation-stages"></a>Estágios de agregação

- $project
- $match
- $limit
- $skip
- $unwind
- $group
- $sample
- $sort
- $lookup
- $out
- $count
- $addFields

### <a name="aggregation-expressions"></a>Expressões de agregação

#### <a name="boolean-expressions"></a>Expressões boolianas

- $and
- $or
- $not

#### <a name="set-expressions"></a>Expressões de definição

- $setEquals
- $setIntersection
- $setUnion
- $setDifference
- $setIsSubset
- $anyElementTrue
- $allElementsTrue

#### <a name="comparison-expressions"></a>Expressões de comparação

- $cmp
- $eq
- $gt
- $gte
- $lt
- $lte
- $ne

#### <a name="arithmetic-expressions"></a>Expressões aritméticas

- $abs
- $add
- $ceil
- $divide
- $exp
- $floor
- $ln
- $log
- $log10
- $mod
- $multiply
- $pow
- $sqrt
- $subtract
- $trunc

#### <a name="string-expressions"></a>Expressões de cadeia de caracteres

- $concat
- $indexOfBytes
- $indexOfCP
- $split
- $strLenBytes
- $strLenCP
- $strcasecmp
- $substr
- $substrBytes
- $substrCP
- $toLower
- $toUpper

#### <a name="array-expressions"></a>Expressões de matriz

- $arrayElemAt
- $concatArrays
- $filter
- $indexOfArray
- $isArray
- $range
- $reverseArray
- $size
- $slice
- $in

#### <a name="date-expressions"></a>Expressões de data

- $dayOfYear
- $dayOfMonth
- $dayOfWeek
- $year
- $month
- $week
- $hour
- $minute
- $segundo
- $millisecond
- $isoDayOfWeek
- $isoWeek

#### <a name="conditional-expressions"></a>Expressões condicionais

- $cond
- $ifNull

## <a name="aggregation-accumulators"></a>Acumuladores de agregação

- $sum
- $avg
- $first
- $last
- $max
- $min
- $push
- $addToSet

## <a name="operators"></a>Operadores

A seguir estão os operadores com suporte e seus exemplos correspondentes de uso. Considere este documento de exemplo usado nas consultas a seguir:

```json
{
  "Volcano Name": "Rainier",
  "Country": "United States",
  "Region": "US-Washington",
  "Location": {
    "type": "Point",
    "coordinates": [
      -121.758,
      46.87
    ]
  },
  "Elevation": 4392,
  "Type": "Stratovolcano",
  "Status": "Dendrochronology",
  "Last Known Eruption": "Last known eruption from 1800-1899, inclusive"
}
```

| Operador | Exemplo |
| --- | --- |
| $eq | `{ "Volcano Name": { $eq: "Rainier" } }` |
| $gt | `{ "Elevation": { $gt: 4000 } }` | 
| $gte | `{ "Elevation": { $gte: 4392 } }` | 
| $lt | `{ "Elevation": { $lt: 5000 } }` | 
| $lte | `{ "Elevation": { $lte: 5000 } }` | 
| $ne | `{ "Elevation": { $ne: 1 } }` | 
| $in | `{ "Volcano Name": { $in: ["St. Helens", "Rainier", "Glacier Peak"] } }` |
| $nin | `{ "Volcano Name": { $nin: ["Lassen Peak", "Hood", "Baker"] } }` |
| $or | `{ $or: [ { Elevation: { $lt: 4000 } }, { "Volcano Name": "Rainier" } ] }` | 
| $and | `{ $and: [ { Elevation: { $gt: 4000 } }, { "Volcano Name": "Rainier" } ] }` |
| $not | `{ "Elevation": { $not: { $gt: 5000 } } }`| 
| $nor | `{ $nor: [ { "Elevation": { $lt: 4000 } }, { "Volcano Name": "Baker" } ] }` |
| $exists | `{ "Status": { $exists: true } }`|
| $type | `{ "Status": { $type: "string" } }`| 
| $mod | `{ "Elevation": { $mod: [ 4, 0 ] } }` | 
| $regex | `{ "Volcano Name": { $regex: "^Rain"} }`| 

### <a name="notes"></a>Observações

Em consultas de $regex, as expressões ancoradas à esquerda permitem pesquisa de índice. No entanto, usar o modiciador 'i' (não sensível a maiúsculas e minúsculas) e o modificador 'm' (várias linhas) faz com que a coleção verifique todas as expressões.
Quando houver a necessidade de se incluir '$' ou '|', é melhor criar duas (ou mais) consultas regex.
Por exemplo, dada a seguinte consulta original: ```find({x:{$regex: /^abc$/})```, ela deve ser modificada da seguinte maneira: ```find({x:{$regex: /^abc/, x:{$regex:/^abc$/}})```.
A primeira parte usará o índice para restringir a pesquisa a esses documentos começando com ^abc e a segunda parte corresponderá às entradas exatas.
O operador de barra '|' atua como uma função "or" – a consulta ```find({x:{$regex: /^abc|^def/})``` faz a correspondência dos documentos em que o campo 'x' tem valores que começam com "abc" ou "def". Para utilizar o índice, é recomendável dividir a consulta em duas consultas diferentes unidas pelo operador $or: ```find( {$or : [{x: $regex: /^abc/}, {$regex: /^def/}] })```.

### <a name="update-operators"></a>Operadores de atualização

#### <a name="field-update-operators"></a>Operadores de atualização de campo

- $inc
- $mul
- $rename
- $setOnInsert
- $set
- $unset
- $min
- $max
- $currentDate

#### <a name="array-update-operators"></a>Operadores de atualização de matriz

- $addToSet
- $pop
- $pullAll
- $pull (Observação: não há suporte para $pull com condição)
- $pushAll
- $push
- $each
- $slice
- $sort
- $position

#### <a name="bitwise-update-operator"></a>Operador de atualização bit a bit

- $bit

### <a name="geospatial-operators"></a>Operadores geoespaciais

Operador | Exemplo | Com suporte |
--- | --- | --- |
$geoWithin | ```{ "Location.coordinates": { $geoWithin: { $centerSphere: [ [ -121, 46 ], 5 ] } } }``` | Sim |
$geoIntersects |  ```{ "Location.coordinates": { $geoIntersects: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Sim |
$near | ```{ "Location.coordinates": { $near: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Sim |
$nearSphere | ```{ "Location.coordinates": { $nearSphere : [ -121, 46  ], $maxDistance: 0.50 } }``` | Sim |
$geometry | ```{ "Location.coordinates": { $geoWithin: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Sim |
$minDistance | ```{ "Location.coordinates": { $nearSphere : { $geometry: {type: "Point", coordinates: [ -121, 46 ]}, $minDistance: 1000, $maxDistance: 1000000 } } }``` | Sim |
$maxDistance | ```{ "Location.coordinates": { $nearSphere : [ -121, 46  ], $maxDistance: 0.50 } }``` | Sim |
$center | ```{ "Location.coordinates": { $geoWithin: { $center: [ [-121, 46], 1 ] } } }``` | Sim |
$centerSphere | ```{ "Location.coordinates": { $geoWithin: { $centerSphere: [ [ -121, 46 ], 5 ] } } }``` | Sim |
$box | ```{ "Location.coordinates": { $geoWithin: { $box:  [ [ 0, 0 ], [ -122, 47 ] ] } } }``` | Sim |
$polygon | ```{ "Location.coordinates": { $near: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Sim |

## <a name="sort-operations"></a>Classificar operações

Ao usar a operação `findOneAndUpdate`, há suporte para operações de classificação em um único campo, mas não há para operações em vários campos.

## <a name="other-operators"></a>Outros operadores

Operador | Exemplo | Observações
--- | --- | --- |
$all | ```{ "Location.coordinates": { $all: [-121.758, 46.87] } }``` |
$elemMatch | ```{ "Location.coordinates": { $elemMatch: {  $lt: 0 } } }``` |
$size | ```{ "Location.coordinates": { $size: 2 } }``` |
$comment |  ```{ "Location.coordinates": { $elemMatch: {  $lt: 0 } }, $comment: "Negative values"}``` |
$text |  | Sem suporte. Nesse caso, use $regex.

## <a name="unsupported-operators"></a>Operadores sem suporte

Os operadores ```$where``` e ```$eval``` não têm suporte pelo Azure Cosmos DB.

### <a name="methods"></a>Métodos

Os seguintes métodos são suportados:

#### <a name="cursor-methods"></a>Métodos de cursor

Método | Exemplo | Observações
--- | --- | --- |
cursor.Sort() | ```cursor.sort({ "Elevation": -1 })``` | Documentos sem chave de classificação não são retornados

## <a name="unique-indexes"></a>Índices exclusivos

O Cosmos DB indexa cada campo em documentos que são gravados no banco de dados por padrão. Os índices exclusivos garantem que um campo específico não tenha valores duplicados em todos os documentos em uma coleção, de forma semelhante a como a exclusividade é preservada na chave `_id` padrão. Crie índices personalizados no Cosmos DB usando o comando createIndex, incluindo a restrição 'unique'.

Há índices exclusivos disponíveis para todas as contas do Cosmos usando a API do Azure Cosmos DB para MongoDB.

## <a name="time-to-live-ttl"></a>Vida útil (TTL)

O Cosmos DB é compatível com uma TTL (vida útil) com base no carimbo de data/hora do documento. A TTL pode ser habilitada para coleções acessando o [portal do Azure](https://portal.azure.com).

## <a name="user-and-role-management"></a>Gerenciamento de usuários e funções

O Cosmos DB ainda não dá suporte a usuários e funções. No entanto, o Cosmos DB dá suporte ao Azure RBAC (controle de acesso baseado em função do Azure) e as senhas/chaves de leitura/gravação e somente leitura que podem ser obtidas por meio do [portal do Azure](https://portal.azure.com) (página Cadeia de conexão).

## <a name="replication"></a>Replicação

O Cosmos DB é compatível com a replicação automática nativa em camadas mais baixas. Essa lógica é estendida para atingir também réplica global e baixa. O Cosmos DB não dá suporte a comandos de replicação manual.

## <a name="write-concern"></a>Problema de Gravação

Alguns aplicativos dependem de uma [Preocupação de gravação](https://docs.mongodb.com/manual/reference/write-concern/) que especifica o número de respostas necessárias durante uma operação de gravação. Devido à forma como o Cosmos DB lida com a replicação em segundo plano, todas as gravações são automaticamente Quorum, por padrão. Toda preocupação de gravação especificada pelo código do cliente é ignorada. Saiba mais em [Como usar níveis de consistência para maximizar a disponibilidade e o desempenho](consistency-levels.md).

## <a name="sharding"></a>Fragmentação

O Azure Cosmos DB oferece suporte à fragmentação automática, do lado do servidor. Ele gerencia a criação, o posicionamento e o balanceamento de fragmentos automaticamente. O Azure Cosmos DB não dá suporte a comandos de fragmentação manual, o que significa que você não precisa invocar comandos como shardCollection, addShard, balancerStart, moveChunk etc. Você só precisa especificar a chave de fragmentação ao criar os contêineres ou consultar os dados.

## <a name="next-steps"></a>Próximas etapas

- Saiba como [usar o Studio 3T](mongodb-mongochef.md) com a API do Azure Cosmos DB para MongoDB.
- Saiba como [usar o Robo 3T](mongodb-robomongo.md) com a API do Azure Cosmos DB para MongoDB.
- Explore [exemplos](mongodb-samples.md) do MongoDB com a API do Azure Cosmos DB para MongoDB.
