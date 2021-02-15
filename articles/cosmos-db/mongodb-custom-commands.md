---
title: Comandos de extensão do MongoDB para gerenciar dados na API do Azure Cosmos DB para MongoDB
description: Este artigo descreve como usar comandos de extensão do MongoDB para gerenciar dados armazenados na API do Azure Cosmos DB para o MongoDB.
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 05/28/2020
ms.author: chrande
ms.custom: devx-track-js
ms.openlocfilehash: 2fd2fa7620e57c58f72dad73c1012a19190e8fbc
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93359639"
---
# <a name="use-mongodb-extension-commands-to-manage-data-stored-in-azure-cosmos-dbs-api-for-mongodb"></a>Usar comandos de extensão do MongoDB para gerenciar dados armazenados na API do Azure Cosmos DB para MongoDB 
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

O documento a seguir contém os comandos de ação personalizada que são específicos para a API do Azure Cosmos DB para MongoDB. Esses comandos podem ser usados para criar e obter recursos de banco de dados específicos para o [modelo de capacidade de Azure Cosmos DB](account-databases-containers-items.md).

Usando a API do Azure Cosmos DB para MongoDB, você pode aproveitar os benefícios Cosmos DB como distribuição global, fragmentação automática, alta disponibilidade, garantias de latência, automático, criptografia em repouso, backups e muito mais, preservando seus investimentos em seu aplicativo MongoDB. Você pode se comunicar com a API do Azure Cosmos DB para MongoDB usando qualquer um dos [drivers de cliente do MongoDB](https://docs.mongodb.org/ecosystem/drivers)de software livre. A API do Azure Cosmos DB para MongoDB permite o uso de drivers de cliente existentes ao aderir ao [protocolo de transmissão do MongoDB](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol).

## <a name="mongodb-protocol-support"></a>Suporte ao protocolo MongoDB

A API do Azure Cosmos DB para MongoDB é compatível com o servidor MongoDB versão 3,2 e 3,6. Consulte [recursos e sintaxe com suporte](mongodb-feature-support.md) para obter mais detalhes. 

Os comandos de extensão a seguir fornecem a capacidade de criar e modificar recursos específicos do Azure Cosmos DB por meio de solicitações de banco de dados:

* [Criar banco de dados](#create-database)
* [Atualizar banco de dados](#update-database)
* [Obter banco de dados](#get-database)
* [Criar coleção](#create-collection)
* [Atualizar coleção](#update-collection)
* [Obter coleção](#get-collection)

## <a name="create-database"></a><a id="create-database"></a> Criar banco de dados

O comando criar extensão de banco de dados cria um novo banco de dados MongoDB. O nome do banco de dados pode ser usado no contexto do banco de dados definido pelo `use database` comando. A tabela a seguir descreve os parâmetros no comando:

|**Campo**|**Tipo** |**Descrição** |
|---------|---------|---------|
| `customAction`   |  `string`  |   Nome do comando personalizado; ele deve ser "CreateDatabase".      |
| `offerThroughput` | `int`  | Taxa de transferência provisionada que você define no banco de dados. Esse parâmetro é opcional. |
| `autoScaleSettings` | `Object` | Necessário para o [modo de dimensionamento automático](provision-throughput-autoscale.md). Este objeto contém as configurações associadas ao modo de capacidade de dimensionamento automático. Você pode configurar o `maxThroughput` valor, que descreve a maior quantidade de unidades de solicitação que a coleção será aumentada dinamicamente. |

### <a name="output"></a>Saída

Se o comando for bem-sucedido, ele retornará a seguinte resposta:

```javascript
{ "ok" : 1 }
```

Consulte a [saída padrão](#default-output) do comando personalizado para os parâmetros na saída.

### <a name="examples"></a>Exemplos

#### <a name="create-a-database"></a>Criar um banco de dados

Para criar um banco de dados chamado `"test"` que usa todos os valores padrão, use o seguinte comando:

```javascript
use test
db.runCommand({customAction: "CreateDatabase"});
```

Esse comando criará um banco de dados sem taxa de transferência no nível do banco de dados. Isso significa que as coleções nesse banco de dados precisarão especificar a quantidade de taxa de transferência que você precisa usar.

#### <a name="create-a-database-with-throughput"></a>Criar um banco de dados com taxa de transferência

Para criar um banco de dados chamado `"test"` e especificar uma taxa de transferência provisionada no [nível de banco de dados](set-throughput.md#set-throughput-on-a-database) de 1000 RUs, use o seguinte comando:

```javascript
use test
db.runCommand({customAction: "CreateDatabase", offerThroughput: 1000 });
```

Isso criará um banco de dados e definirá uma taxa de transferência para ele. Todas as coleções nesse banco de dados compartilharão a taxa de transferência definida, a menos que as coleções sejam criadas com [um nível de taxa de transferência específico](set-throughput.md#set-throughput-on-a-database-and-a-container).

#### <a name="create-a-database-with-autoscale-throughput"></a>Criar um banco de dados com taxa de transferência de autoescala

Para criar um banco de dados chamado `"test"` e especificar uma taxa de transferência máxima de dimensionamento automático de 20.000 ru/s em [nível de banco de dados](set-throughput.md#set-throughput-on-a-database), use o seguinte comando:

```javascript
use test
db.runCommand({customAction: "CreateDatabase", autoScaleSettings: { maxThroughput: 20000 } });
```

## <a name="update-database"></a><a id="update-database"></a> Atualizar banco de dados

O comando Atualizar extensão do banco de dados atualiza as propriedades associadas ao banco de dados especificado. A tabela a seguir descreve os parâmetros no comando:

|**Campo**|**Tipo** |**Descrição** |
|---------|---------|---------|
| `customAction`    |    `string`     |   Nome do comando personalizado. Deve ser "UpdateDatabase".      |
|  `offerThroughput`   |  `int`       |     Nova taxa de transferência provisionada que você deseja definir no banco de dados se o banco de dados usar a [taxa de transferência no nível do banco de dados](set-throughput.md#set-throughput-on-a-database)  |
| `autoScaleSettings` | `Object` | Necessário para o [modo de dimensionamento automático](provision-throughput-autoscale.md). Este objeto contém as configurações associadas ao modo de capacidade de dimensionamento automático. Você pode configurar o `maxThroughput` valor, que descreve a maior quantidade de unidades de solicitação que o banco de dados aumentará dinamicamente. |

Esse comando usa o banco de dados especificado no contexto da sessão. Esse é o banco de dados que você usou no `use <database>` comando. No momento, o nome do banco de dados não pode ser alterado usando esse comando.

### <a name="output"></a>Saída

Se o comando for bem-sucedido, ele retornará a seguinte resposta:

```javascript
{ "ok" : 1 }
```

Consulte a [saída padrão](#default-output) do comando personalizado para os parâmetros na saída.

### <a name="examples"></a>Exemplos

#### <a name="update-the-provisioned-throughput-associated-with-a-database"></a>Atualizar a taxa de transferência provisionada associada a um banco de dados

Para atualizar a taxa de transferência provisionada de um banco de dados com nome `"test"` para 1200 RUs, use o seguinte comando:

```javascript
use test
db.runCommand({customAction: "UpdateDatabase", offerThroughput: 1200 });
```

#### <a name="update-the-autoscale-throughput-associated-with-a-database"></a>Atualizar a taxa de transferência de dimensionamento automático associada a um banco de dados

Para atualizar a taxa de transferência provisionada de um banco de dados com nome `"test"` para 20.000 RUs, ou para transformá-lo em um [nível de taxa de transferência de dimensionamento automático](provision-throughput-autoscale.md), use o seguinte comando:

```javascript
use test
db.runCommand({customAction: "UpdateDatabase", autoScaleSettings: { maxThroughput: 20000 } });
```


## <a name="get-database"></a><a id="get-database"></a> Obter banco de dados

O comando obter extensão do banco de dados retorna o objeto de banco de dados. O nome do banco de dados é usado no contexto do banco de dados no qual o comando é executado.

```javascript
{
  customAction: "GetDatabase"
}
```

A tabela a seguir descreve os parâmetros no comando:


|**Campo**|**Tipo** |**Descrição** |
|---------|---------|---------|
|  `customAction`   |   `string`      |   Nome do comando personalizado. Deve ser "GetDatabase"|
        
### <a name="output"></a>Saída

Se o comando for executado com sucesso, a resposta conterá um documento com os seguintes campos:

|**Campo**|**Tipo** |**Descrição** |
|---------|---------|---------|
|  `ok`   |   `int`     |   Status da resposta. 1 = = êxito. 0 = = falha.      |
| `database`    |    `string`        |   Nome do banco de dados.      |
|   `provisionedThroughput`  |    `int`      |    Taxa de transferência provisionada que é definida no banco de dados se o banco de dados estiver usando a  [taxa de transferência manual no nível do banco de dados](set-throughput.md#set-throughput-on-a-database)     |
| `autoScaleSettings` | `Object` | Esse objeto contém os parâmetros de capacidade associados ao banco de dados se ele estiver usando o [modo de dimensionamento automático](provision-throughput-autoscale.md). O `maxThroughput` valor descreve a quantidade mais alta de unidades de solicitação para as quais o banco de dados será aumentado dinamicamente. |

Se o comando falhar, uma resposta de comando personalizado padrão será retornada. Consulte a [saída padrão](#default-output) do comando personalizado para os parâmetros na saída.

### <a name="examples"></a>Exemplos

#### <a name="get-the-database"></a>Obter o banco de dados

Para obter o objeto de banco de dados para um banco de dados chamado `"test"` , use o seguinte comando:

```javascript
use test
db.runCommand({customAction: "GetDatabase"});
```

Se o banco de dados não tiver nenhuma taxa de transferência associada, a saída será:

```javascript
{ "database" : "test", "ok" : 1 }
```

Se o banco de dados tiver uma [taxa de transferência manual no nível do banco de dados](set-throughput.md#set-throughput-on-a-database) associada a ele, a saída mostraria os `provisionedThroughput` valores:

```javascript
{ "database" : "test", "provisionedThroughput" : 20000, "ok" : 1 }
```

Se o banco de dados tiver uma [taxa de transferência de dimensionamento automático no nível do banco de dados](provision-throughput-autoscale.md) associada a ele, a saída mostrará o `provisionedThroughput` , que descreve os ru/s mínimos para o banco de dados e o `autoScaleSettings` objeto, incluindo o `maxThroughput` , que descreve o máximo de ru/s para o banco de dados.

```javascript
{
        "database" : "test",
        "provisionedThroughput" : 2000,
        "autoScaleSettings" : {
                "maxThroughput" : 20000
        },
        "ok" : 1
}
```

## <a name="create-collection"></a><a id="create-collection"></a> Criar coleção

O comando criar extensão da coleção cria uma nova coleção do MongoDB. O nome do banco de dados é usado no contexto dos bancos de dados definido pelo `use database` comando. O formato do comando é o seguinte:

```javascript
{
  customAction: "CreateCollection",
  collection: "<Collection Name>",
  shardKey: "<Shard key path>",
  offerThroughput: (int), // Amount of throughput allocated to a specific collection

}
```

A tabela a seguir descreve os parâmetros no comando:

| **Campo** | **Tipo** | **Necessário** | **Descrição** |
|---------|---------|---------|---------|
| `customAction` | `string` | Obrigatório | Nome do comando personalizado. Deve ser "Criarcollection".|
| `collection` | `string` | Obrigatório | Nome da coleção. Não são permitidos caracteres especiais ou espaços.|
| `offerThroughput` | `int` | Opcional | Taxa de transferência provisionada a ser definida no banco de dados. Se esse parâmetro não for fornecido, o padrão será o mínimo, 400 RU/s. * Para especificar a taxa de transferência além de 10.000 RU/s, o `shardKey` parâmetro é obrigatório.|
| `shardKey` | `string` | Necessário para coleções com alta taxa de transferência | O caminho para a chave de fragmento da coleção fragmentada. Esse parâmetro será necessário se você definir mais de 10.000 RU/s em `offerThroughput` .  Se for especificado, todos os documentos inseridos exigirão essa chave e valor. |
| `autoScaleSettings` | `Object` | Necessário para o [modo de dimensionamento automático](provision-throughput-autoscale.md) | Este objeto contém as configurações associadas ao modo de capacidade de dimensionamento automático. Você pode configurar o `maxThroughput` valor, que descreve a maior quantidade de unidades de solicitação que a coleção será aumentada dinamicamente. |

### <a name="output"></a>Saída

Retorna uma resposta de comando personalizado padrão. Consulte a [saída padrão](#default-output) do comando personalizado para os parâmetros na saída.

### <a name="examples"></a>Exemplos

#### <a name="create-a-collection-with-the-minimum-configuration"></a>Criar uma coleção com a configuração mínima

Para criar uma nova coleção com `"testCollection"` o nome e os valores padrão, use o seguinte comando: 

```javascript
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection"});
```

Isso resultará em uma nova coleção fixa, não fragmentada, com 400RU/s e um índice no `_id` campo criado automaticamente. Esse tipo de configuração também será aplicado ao criar novas coleções por meio da `insert()` função. Por exemplo: 

```javascript
use test
db.newCollection.insert({});
```

#### <a name="create-a-unsharded-collection"></a>Criar uma coleção não fragmentada

Para criar uma coleção não fragmentada com nome `"testCollection"` e taxa de transferência provisionada de 1000 RUs, use o seguinte comando: 

```javascript
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000});
``` 

Você pode criar uma coleção com até 10.000 RU/s como o `offerThroughput` sem precisar especificar uma chave de fragmentação. Para coleções com maior taxa de transferência, confira a próxima seção.

#### <a name="create-a-sharded-collection"></a>Criar uma coleção fragmentada

Para criar uma coleção fragmentada com nome `"testCollection"` e taxa de transferência provisionada de 11.000 RUs e uma `shardkey` Propriedade "a. b", use o seguinte comando:

```javascript
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 11000, shardKey: "a.b" });
```

Este comando agora requer o `shardKey` parâmetro, pois mais de 10.000 ru/s especificados no `offerThroughput` .

#### <a name="create-an-unsharded-autoscale-collection"></a>Criar uma coleção de autoescala não fragmentada

Para criar uma coleção não fragmentada chamada `'testCollection'` que usa a [capacidade de taxa de transferência de dimensionamento automático](provision-throughput-autoscale.md) definida como 4.000 ru/s, use o seguinte comando:

```javascript
use test
db.runCommand({ 
    customAction: "CreateCollection", collection: "testCollection", 
    autoScaleSettings:{
      maxThroughput: 4000
    } 
});
```

Para o `autoScaleSettings.maxThroughput` valor, você pode especificar um intervalo de 4.000 ru/s a 10.000 ru/s sem uma chave de fragmentação. Para maior taxa de transferência de dimensionamento automático, você precisa especificar o `shardKey` parâmetro.

#### <a name="create-a-sharded-autoscale-collection"></a>Criar uma coleção de dimensionamento automático fragmentada

Para criar uma coleção fragmentada chamada `'testCollection'` com uma chave de fragmento chamada `'a.b'` e que usa a [capacidade de taxa de transferência de dimensionamento automático](provision-throughput-autoscale.md) definida como 20.000 ru/s, use o seguinte comando:

```javascript
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", shardKey: "a.b", autoScaleSettings: { maxThroughput: 20000 }});
```

## <a name="update-collection"></a><a id="update-collection"></a> Atualizar coleção

O comando Atualizar extensão da coleção atualiza as propriedades associadas à coleção especificada.

```javascript
{
  customAction: "UpdateCollection",
  collection: "<Name of the collection that you want to update>",
  offerThroughput: (int) // New throughput that will be set to the collection
}
```

A tabela a seguir descreve os parâmetros no comando:

|**Campo**|**Tipo** |**Descrição** |
|---------|---------|---------|
|  `customAction`   |   `string`      |   Nome do comando personalizado. Deve ser "updatecollection".      |
|  `collection`   |   `string`      |   Nome da coleção.       |
| `offerThroughput` | `int` |   Taxa de transferência provisionada a ser definida na coleção.|
| `autoScaleSettings` | `Object` | Necessário para o [modo de dimensionamento automático](provision-throughput-autoscale.md). Este objeto contém as configurações associadas ao modo de capacidade de dimensionamento automático. O `maxThroughput` valor descreve a maior quantidade de unidades de solicitação que a coleção será aumentada dinamicamente. |

## <a name="output"></a>Saída

Retorna uma resposta de comando personalizado padrão. Consulte a [saída padrão](#default-output) do comando personalizado para os parâmetros na saída.

### <a name="examples"></a>Exemplos

#### <a name="update-the-provisioned-throughput-associated-with-a-collection"></a>Atualizar a taxa de transferência provisionada associada a uma coleção

Para atualizar a taxa de transferência provisionada de uma coleção com `"testCollection"` o nome para 1200 RUs, use o seguinte comando:

```javascript
use test
db.runCommand({customAction: "UpdateCollection", collection: "testCollection", offerThroughput: 1200 });
```

## <a name="get-collection"></a><a id="get-collection"></a> Obter coleção

O comando Get Collection personalizado retorna o objeto da coleção.

```javascript
{
  customAction: "GetCollection",
  collection: "<Name of the collection>"
}
```

A tabela a seguir descreve os parâmetros no comando:


|**Campo**|**Tipo** |**Descrição** |
|---------|---------|---------|
| `customAction`    |   `string`      |   Nome do comando personalizado. Deve ser "GetCollection".      |
| `collection`    |    `string`     |    Nome da coleção.     |

### <a name="output"></a>Saída

Se o comando for executado com sucesso, a resposta conterá um documento com os campos a seguir


|**Campo**|**Tipo** |**Descrição** |
|---------|---------|---------|
|  `ok`   |    `int`     |   Status da resposta. 1 = = êxito. 0 = = falha.      |
| `database`    |    `string`     |   Nome do banco de dados.      |
| `collection`    |    `string`     |    Nome da coleção.     |
|  `shardKeyDefinition`   |   `document`      |  Documento de especificação de índice usado como chave de fragmentação. Esse é um parâmetro de resposta opcional.       |
|  `provisionedThroughput`   |   `int`      |    Taxa de transferência provisionada a ser definida na coleção. Esse é um parâmetro de resposta opcional.     |
| `autoScaleSettings` | `Object` | Esse objeto contém os parâmetros de capacidade associados ao banco de dados se ele estiver usando o [modo de dimensionamento automático](provision-throughput-autoscale.md). O `maxThroughput` valor descreve a maior quantidade de unidades de solicitação que a coleção será aumentada dinamicamente. |

Se o comando falhar, uma resposta de comando personalizado padrão será retornada. Consulte a [saída padrão](#default-output) do comando personalizado para os parâmetros na saída.

### <a name="examples"></a>Exemplos

#### <a name="get-the-collection"></a>Obter a coleção

Para obter o objeto de coleção para uma coleção denominada `"testCollection"` , use o seguinte comando:

```javascript
use test
db.runCommand({customAction: "GetCollection", collection: "testCollection"});
```

Se a coleção tiver uma capacidade de taxa de transferência associada a ela, ela incluirá o `provisionedThroughput` valor e a saída será:

```javascript
{
        "database" : "test",
        "collection" : "testCollection",
        "provisionedThroughput" : 400,
        "ok" : 1
}
```

Se a coleção tiver uma taxa de transferência de dimensionamento automático associada, ela incluirá o `autoScaleSettings` objeto com o `maxThroughput` parâmetro, que define a taxa de transferência máxima que a coleção aumentará dinamicamente. Além disso, ele também incluirá o `provisionedThroughput` valor, que define a taxa de transferência mínima que essa coleção reduzirá se não houver nenhuma solicitação na coleção: 

```javascript
{
        "database" : "test",
        "collection" : "testCollection",
        "provisionedThroughput" : 1000,
        "autoScaleSettings" : {
            "maxThroughput" : 10000
        },
        "ok" : 1
}
```

Se a coleção estiver compartilhando a [taxa de transferência no nível do banco de dados](set-throughput.md#set-throughput-on-a-database), seja no modo de dimensionamento automático ou manual, a saída será:

```javascript
{ "database" : "test", "collection" : "testCollection", "ok" : 1 }
```

```javascript
{
        "database" : "test",
        "provisionedThroughput" : 2000,
        "autoScaleSettings" : {
            "maxThroughput" : 20000
        },
        "ok" : 1
}
```


## <a name="default-output-of-a-custom-command"></a><a id="default-output"></a> Saída padrão de um comando personalizado

Se não for especificado, uma resposta personalizada conterá um documento com os seguintes campos:

|**Campo**|**Tipo** |**Descrição** |
|---------|---------|---------|
|  `ok`   |    `int`     |   Status da resposta. 1 = = êxito. 0 = = falha.      |
| `code`    |   `int`      |   Retornado somente quando o comando falhou (ou seja, ok = = 0). Contém o código de erro do MongoDB. Esse é um parâmetro de resposta opcional.      |
|  `errMsg`   |  `string`      |    Retornado somente quando o comando falhou (ou seja, ok = = 0). Contém uma mensagem de erro amigável. Esse é um parâmetro de resposta opcional.      |

Por exemplo:

```javascript
{ "ok" : 1 }
```

## <a name="next-steps"></a>Próximas etapas

Em seguida, você pode continuar a aprender os seguintes conceitos de Azure Cosmos DB: 

* [Indexação no Azure Cosmos DB](../cosmos-db/index-policy.md)
* [Expirar os dados no Azure Cosmos DB automaticamente com a vida útil](../cosmos-db/time-to-live.md)
