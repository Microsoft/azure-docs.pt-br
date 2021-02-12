---
title: Gerenciar a indexação na API do Azure Cosmos DB para MongoDB
description: Este artigo apresenta uma visão geral de Azure Cosmos DB recursos de indexação usando a API do Azure Cosmos DB para MongoDB
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: how-to
ms.date: 01/08/2020
author: timsander1
ms.author: tisande
ms.custom: devx-track-js
ms.openlocfilehash: 34caca47746814046a894494ec43d9b5c977389a
ms.sourcegitcommit: 31cfd3782a448068c0ff1105abe06035ee7b672a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/10/2021
ms.locfileid: "98060054"
---
# <a name="manage-indexing-in-azure-cosmos-dbs-api-for-mongodb"></a>Gerenciar a indexação na API do Azure Cosmos DB para MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

A API do Azure Cosmos DB para MongoDB aproveita os principais recursos de gerenciamento de índice do Azure Cosmos DB. Este artigo se concentra em como adicionar índices usando a API do Azure Cosmos DB para MongoDB. Você também pode ler uma [visão geral da indexação em Azure Cosmos DB](index-overview.md) relevante em todas as APIs.

## <a name="indexing-for-mongodb-server-version-36"></a>Indexação para o servidor MongoDB versão 3,6

A API do Azure Cosmos DB para o servidor MongoDB versão 3,6 indexa automaticamente o `_id` campo, que não pode ser removido. Ele impõe automaticamente a exclusividade do `_id` campo por chave de fragmentação. Na API do Azure Cosmos DB para MongoDB, a fragmentação e a indexação são conceitos separados. Você não precisa indexar sua chave de fragmento. No entanto, assim como com qualquer outra propriedade em seu documento, se essa propriedade for um filtro comum em suas consultas, é recomendável indexar a chave de fragmentação.

Para indexar campos adicionais, você aplica os comandos de gerenciamento de índice do MongoDB. Como no MongoDB, a API do Azure Cosmos DB para MongoDB indexa automaticamente o `_id` campo somente. Essa política de indexação padrão difere da API de SQL do Azure Cosmos DB, que indexa todos os campos por padrão.

Para aplicar uma classificação a uma consulta, você deve criar um índice nos campos usados na operação de classificação.

### <a name="editing-indexing-policy"></a>Editando política de indexação

É recomendável editar sua política de indexação no Data Explorer dentro do portal do Azure.
. Você pode adicionar índices de campo único e curinga do editor de políticas de indexação no Data Explorer:

:::image type="content" source="./media/mongodb-indexing/indexing-policy-editor.png" alt-text="Editor de políticas de indexação":::

> [!NOTE]
> Você não pode criar índices compostos usando o editor de política de indexação no Data Explorer.

## <a name="index-types"></a>Tipos de índice

### <a name="single-field"></a>Campo único

Você pode criar índices em qualquer campo único. A ordem de classificação do índice de campo único não importa. O comando a seguir cria um índice no campo `name` :

`db.coll.createIndex({name:1})`

Você pode criar o mesmo índice de campo único no `name` portal do Azure:

:::image type="content" source="./media/mongodb-indexing/add-index.png" alt-text="Adicionar índice de nome no editor de políticas de indexação":::

Uma consulta usa vários índices de campo único, quando disponíveis. Você pode criar até 500 índices de campo único por contêiner.

### <a name="compound-indexes-mongodb-server-version-36"></a>Índices compostos (servidor MongoDB versão 3,6)

A API do Azure Cosmos DB para MongoDB dá suporte a índices compostos para contas que usam o protocolo de transmissão da versão 3,6. Você pode incluir até oito campos em um índice composto. Ao contrário do MongoDB, você deve criar um índice composto somente se sua consulta precisar classificar com eficiência em vários campos ao mesmo tempo. Para consultas com vários filtros que não precisam ser classificados, crie vários índices de campo único em vez de um único índice composto. 

> [!NOTE]
> Você não pode criar índices compostos em propriedades aninhadas ou matrizes.

O comando a seguir cria um índice composto nos campos `name` e `age` :

`db.coll.createIndex({name:1,age:1})`

Você pode usar índices compostos para classificar com eficiência em vários campos ao mesmo tempo, conforme mostrado no exemplo a seguir:

`db.coll.find().sort({name:1,age:1})`

Você também pode usar o índice composto anterior para classificar com eficiência uma consulta com a ordem de classificação oposta em todos os campos. Veja um exemplo:

`db.coll.find().sort({name:-1,age:-1})`

No entanto, a sequência dos caminhos no índice composto deve corresponder exatamente à consulta. Veja um exemplo de uma consulta que exigiria um índice composto adicional:

`db.coll.find().sort({age:1,name:1})`

> [!NOTE]
> Os índices compostos são usados apenas em consultas que classificam os resultados. Para consultas que têm vários filtros que não precisam ser classificados, crie índices de campo único multipe.

### <a name="multikey-indexes"></a>Índices de multikey

Azure Cosmos DB cria índices multikey para indexar o conteúdo armazenado em matrizes. Se você indexar um campo com um valor de matriz, Azure Cosmos DB indexa automaticamente todos os elementos na matriz.

### <a name="geospatial-indexes"></a>Índices geoespaciais

Muitos operadores geoespaciais se beneficiarão dos índices geoespaciais. Atualmente, a API do Azure Cosmos DB para MongoDB dá suporte a `2dsphere` índices. A API ainda não oferece suporte a `2d` índices.

Aqui está um exemplo de criação de um índice geoespacial no `location` campo:

`db.coll.createIndex({ location : "2dsphere" })`

### <a name="text-indexes"></a>Índices de texto

Atualmente, a API do Azure Cosmos DB para MongoDB não dá suporte a índices de texto. Para consultas de pesquisa de texto em cadeias de caracteres, você deve usar a integração de [pesquisa cognitiva do Azure](../search/search-howto-index-cosmosdb.md) com Azure Cosmos DB. 

## <a name="wildcard-indexes"></a>Índices curinga

Você pode usar índices curinga para dar suporte a consultas em campos desconhecidos. Vamos imaginar que você tenha uma coleção que contém dados sobre as famílias.

Aqui está parte de um documento de exemplo nessa coleção:

```json
  "children": [
     {
         "firstName": "Henriette Thaulow",
         "grade": "5"
     }
  ]
```

Aqui está outro exemplo, desta vez com um conjunto ligeiramente diferente de propriedades em `children` :

```json
  "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "pets": [
            { "givenName": "Goofy" },
            { "givenName": "Shadow" }
      },
      {
        "familyName": "Merriam",
        "givenName": "John",
      }
  ]
```

Nesta coleção, os documentos podem ter muitas propriedades possíveis diferentes. Se você quisesse indexar todos os dados na `children` matriz, você tem duas opções: criar índices separados para cada propriedade individual ou criar um índice curinga para toda a `children` matriz.

### <a name="create-a-wildcard-index"></a>Criar um índice curinga

O comando a seguir cria um índice curinga em qualquer propriedade em `children` :

`db.coll.createIndex({"children.$**" : 1})`

**Ao contrário do MongoDB, os índices curinga podem dar suporte a vários campos em predicados de consulta**. Não haverá diferença no desempenho da consulta se você usar um único índice curinga em vez de criar um índice separado para cada propriedade.

Você pode criar os seguintes tipos de índice usando a sintaxe curinga:

- Campo único
- Geoespacial

### <a name="indexing-all-properties"></a>Indexando todas as propriedades

Veja como você pode criar um índice curinga em todos os campos:

`db.coll.createIndex( { "$**" : 1 } )`

Você também pode criar índices curinga usando o Data Explorer no portal do Azure:

:::image type="content" source="./media/mongodb-indexing/add-wildcard-index.png" alt-text="Adicionar índice curinga no editor de política de indexação":::

> [!NOTE]
> Se você estiver apenas iniciando o desenvolvimento, é **altamente** recomendável começar com um índice curinga em todos os campos. Isso pode simplificar o desenvolvimento e facilitar a otimização das consultas.

Documentos com muitos campos podem ter uma cobrança de alta unidade de solicitação (RU) para gravações e atualizações. Portanto, se você tiver uma carga de trabalho de gravação intensa, deverá optar por caminhos de índice individualmente em vez de usar índices curinga.

### <a name="limitations"></a>Limitações

Os índices curinga não oferecem suporte a nenhum dos seguintes tipos de índice ou propriedades:

- Composto
- TTL
- Exclusivo

**Ao contrário do MongoDB**, na API do Azure Cosmos DB para MongoDB, você **não pode** usar índices curinga para:

- Criar um índice curinga que inclui vários campos específicos

`db.coll.createIndex(
    { "$**" : 1 },
    { "wildcardProjection " :
        {
           "children.givenName" : 1,
           "children.grade" : 1
        }
    }
)`

- Criar um índice curinga que exclui vários campos específicos

`db.coll.createIndex(
    { "$**" : 1 },
    { "wildcardProjection" :
        {
           "children.givenName" : 0,
           "children.grade" : 0
        }
    }
)`

Como alternativa, você pode criar vários índices curinga.

## <a name="index-properties"></a>Propriedades de índice

As operações a seguir são comuns para as contas que servem o protocolo de conexão versão 3,6 e as contas que atendem a versões anteriores. Você pode saber mais sobre [índices com suporte e propriedades indexadas](mongodb-feature-support-36.md#indexes-and-index-properties).

### <a name="unique-indexes"></a>Índices exclusivos

[Índices exclusivos](unique-keys.md) são úteis para impor que dois ou mais documentos não contenham o mesmo valor para campos indexados.

> [!IMPORTANT]
> Índices exclusivos podem ser criados somente quando a coleção está vazia (não contém nenhum documento).

O comando a seguir cria um índice exclusivo no campo `student_id` :

```shell
globaldb:PRIMARY> db.coll.createIndex( { "student_id" : 1 }, {unique:true} )
{
        "_t" : "CreateIndexesResponse",
        "ok" : 1,
        "createdCollectionAutomatically" : false,
        "numIndexesBefore" : 1,
        "numIndexesAfter" : 4
}
```

Para coleções fragmentadas, você deve fornecer a chave de fragmento (partição) para criar um índice exclusivo. Em outras palavras, todos os índices exclusivos em uma coleção fragmentada são índices compostos em que um dos campos é a chave de partição.

Os comandos a seguir criam uma coleção fragmentada ```coll``` (a chave de fragmento é ```university``` ) com um índice exclusivo nos campos `student_id` e `university` :

```shell
globaldb:PRIMARY> db.runCommand({shardCollection: db.coll._fullName, key: { university: "hashed"}});
{
        "_t" : "ShardCollectionResponse",
        "ok" : 1,
        "collectionsharded" : "test.coll"
}
globaldb:PRIMARY> db.coll.createIndex( { "university" : 1, "student_id" : 1 }, {unique:true});
{
        "_t" : "CreateIndexesResponse",
        "ok" : 1,
        "createdCollectionAutomatically" : false,
        "numIndexesBefore" : 3,
        "numIndexesAfter" : 4
}
```

No exemplo anterior, omitir a ```"university":1``` cláusula retorna um erro com a seguinte mensagem:

```"cannot create unique index over {student_id : 1.0} with shard key pattern { university : 1.0 }"```

### <a name="ttl-indexes"></a>Índices TTL

Para habilitar a expiração do documento em uma determinada coleção, você precisa criar um [índice TTL (vida útil)](../cosmos-db/time-to-live.md). Um índice de TTL é um índice no `_ts` campo com um `expireAfterSeconds` valor.

Exemplo:

```JavaScript
globaldb:PRIMARY> db.coll.createIndex({"_ts":1}, {expireAfterSeconds: 10})
```

O comando anterior exclui todos os documentos na ```db.coll``` coleção que não foram modificados nos últimos 10 segundos.

> [!NOTE]
> O campo **_ts** é específico para Azure Cosmos DB e não pode ser acessado de clientes do MongoDB. É uma propriedade reservada (sistema) que contém o carimbo de data/hora da última modificação do documento.

## <a name="track-index-progress"></a>Acompanhar o progresso do índice

A versão 3,6 da API do Azure Cosmos DB para MongoDB dá suporte ao `currentOp()` comando para acompanhar o progresso do índice em uma instância de banco de dados. Esse comando retorna um documento que contém informações sobre operações em andamento em uma instância de banco de dados. Você usa o `currentOp` comando para controlar todas as operações em andamento no MongoDB nativo. Na API do Azure Cosmos DB para MongoDB, esse comando só dá suporte ao acompanhamento da operação de índice.

Aqui estão alguns exemplos que mostram como usar o `currentOp` comando para acompanhar o progresso do índice:

* Obter o progresso do índice para uma coleção:

   ```shell
   db.currentOp({"command.createIndexes": <collectionName>, "command.$db": <databaseName>})
   ```

* Obter o progresso do índice para todas as coleções em um banco de dados:

  ```shell
  db.currentOp({"command.$db": <databaseName>})
  ```

* Obtenha o progresso do índice para todos os bancos de dados e coleções em uma conta do Azure Cosmos:

  ```shell
  db.currentOp({"command.createIndexes": { $exists : true } })
  ```

### <a name="examples-of-index-progress-output"></a>Exemplos de saída de progresso de índice

Os detalhes do progresso do índice mostram a porcentagem de progresso para a operação de índice atual. Aqui está um exemplo que mostra o formato do documento de saída para diferentes estágios do progresso do índice:

- Uma operação de índice em uma coleção "foo" e no banco de dados de "barra" que é 60 por cento concluído terá o seguinte documento de saída. O `Inprog[0].progress.total` campo mostra 100 como a porcentagem de conclusão de destino.

   ```json
   {
        "inprog" : [
        {
                ………………...
                "command" : {
                        "createIndexes" : foo
                        "indexes" :[ ],
                        "$db" : bar
                },
                "msg" : "Index Build (background) Index Build (background): 60 %",
                "progress" : {
                        "done" : 60,
                        "total" : 100
                },
                …………..…..
        }
        ],
        "ok" : 1
   }
   ```

- Se uma operação de índice acabou de ser iniciada em uma coleção "foo" e no banco de dados "bar", o documento de saída poderá mostrar 0% de progresso até atingir um nível mensurável.

   ```json
   {
        "inprog" : [
        {
                ………………...
                "command" : {
                        "createIndexes" : foo
                        "indexes" :[ ],
                        "$db" : bar
                },
                "msg" : "Index Build (background) Index Build (background): 0 %",
                "progress" : {
                        "done" : 0,
                        "total" : 100
                },
                …………..…..
        }
        ],
       "ok" : 1
   }
   ```

- Quando a operação de índice em andamento for concluída, o documento de saída mostrará operações vazias `inprog` .

   ```json
   {
      "inprog" : [],
      "ok" : 1
   }
   ```

## <a name="background-index-updates"></a>Atualizações de índice em segundo plano

Independentemente do valor especificado para a propriedade de índice de **plano de fundo** , as atualizações de índice sempre são feitas em segundo plano. Como as atualizações de índice consomem as unidades de solicitação (RUs) em uma prioridade mais baixa que outras operações de banco de dados, as alterações de índice não resultarão em nenhum tempo de inatividade para gravações, atualizações ou exclusões.

Não há nenhum impacto na disponibilidade de leitura ao adicionar um novo índice. As consultas só utilizarão novos índices depois que a transformação do índice for concluída. Durante a transformação do índice, o mecanismo de consulta continuará a usar índices existentes, portanto, você observará um desempenho de leitura semelhante durante a transformação de indexação para o que você observou antes de iniciar a alteração de indexação. Ao adicionar novos índices, também não há nenhum risco de resultados de consulta incompletos ou inconsistentes.

Ao remover índices e executar imediatamente as consultas que têm filtros nos índices descartados, os resultados podem ser inconsistentes e incompletos até que a transformação do índice seja concluída. Se você remover índices, o mecanismo de consulta não fornecerá resultados consistentes ou completos quando as consultas filtrarem esses índices recentemente removidos. A maioria dos desenvolvedores não removem índices e, em seguida, tentam consultá-los imediatamente, na prática, essa situação é improvável.

> [!NOTE]
> Você pode [acompanhar o progresso do índice](#track-index-progress).

## <a name="reindex-command"></a>Comando REINDEX

O `reIndex` comando recriará todos os índices em uma coleção. Na maioria dos casos, isso é desnecessário. No entanto, em alguns casos raros, o desempenho da consulta pode melhorar após a execução do `reIndex` comando.

Você pode executar o `reIndex` comando usando a seguinte sintaxe:

`db.runCommand({ reIndex: <collection> })`

Você pode usar a sintaxe abaixo para verificar se você precisa executar o `reIndex` comando:

`db.runCommand({"customAction":"GetCollection",collection:<collection>, showIndexes:true})`

Saída de exemplo:

```
{
        "database" : "myDB",
        "collection" : "myCollection",
        "provisionedThroughput" : 400,
        "indexes" : [
                {
                        "v" : 1,
                        "key" : {
                                "_id" : 1
                        },
                        "name" : "_id_",
                        "ns" : "myDB.myCollection",
                        "requiresReIndex" : true
                },
                {
                        "v" : 1,
                        "key" : {
                                "b.$**" : 1
                        },
                        "name" : "b.$**_1",
                        "ns" : "myDB.myCollection",
                        "requiresReIndex" : true
                }
        ],
        "ok" : 1
}
```

Se `reIndex` for necessário, **requiresReIndex** será true. Se `reIndex` não for necessário, essa propriedade será omitida.

## <a name="migrate-collections-with-indexes"></a>Migrar coleções com índices

No momento, você só pode criar índices exclusivos quando a coleção não contém nenhum documento. Ferramentas populares de migração do MongoDB tente criar os índices exclusivos depois de importar os dados. Para evitar esse problema, você pode criar manualmente as coleções correspondentes e os índices exclusivos em vez de permitir que a ferramenta de migração tente. (Você pode obter esse comportamento para o ```mongorestore``` usando o `--noIndexRestore` sinalizador na linha de comando.)

## <a name="indexing-for-mongodb-version-32"></a>Indexação para o MongoDB versão 3,2

Os recursos de indexação disponíveis e os padrões são diferentes para contas do Azure cosmos que são compatíveis com a versão 3,2 do protocolo de transmissão do MongoDB. Você pode [verificar a versão da sua conta](mongodb-feature-support-36.md#protocol-support) e [atualizar para a versão 3,6](mongodb-version-upgrade.md).

Se você estiver usando a versão 3,2, esta seção descreve as principais diferenças com a versão 3,6.

### <a name="dropping-default-indexes-version-32"></a>Descartando índices padrão (versão 3,2)

Ao contrário da versão 3,6 da API do Azure Cosmos DB para MongoDB, a versão 3,2 indexa cada propriedade por padrão. Você pode usar o seguinte comando para descartar esses índices padrão para uma coleção ( ```coll``` ):

```JavaScript
> db.coll.dropIndexes()
{ "_t" : "DropIndexesResponse", "ok" : 1, "nIndexesWas" : 3 }
```

Depois de descartar os índices padrão, você pode adicionar mais índices como faria na versão 3,6.

### <a name="compound-indexes-version-32"></a>Índices compostos (versão 3,2)

Os índices compostos possuem referências a vários campos de um documento. Se você quiser criar um índice composto, [atualize para a versão 3,6](mongodb-version-upgrade.md).

### <a name="wildcard-indexes-version-32"></a>Índices curinga (versão 3,2)

Se você quiser criar um índice curinga, [atualize para a versão 3,6](mongodb-version-upgrade.md).

## <a name="next-steps"></a>Próximas etapas

* [Indexação no Azure Cosmos DB](../cosmos-db/index-policy.md)
* [Expirar os dados no Azure Cosmos DB automaticamente com a vida útil](../cosmos-db/time-to-live.md)
* Para saber mais sobre a relação entre particionamento e indexação, consulte o artigo como [consultar um contêiner de Cosmos do Azure](how-to-query-container.md) .
