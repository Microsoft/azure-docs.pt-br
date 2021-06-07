---
title: Usar o armazenamento de Tabelas do Azure ou a API de Tabela do Azure Cosmos DB usando o Node.js
description: Armazene dados estruturados na nuvem usando o Armazenamento de Tabelas do Azure ou a API de Tabela do Azure Cosmos DB do Node.js.
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: nodejs
ms.topic: sample
ms.date: 07/23/2020
author: sakash279
ms.author: akshanka
ms.custom: devx-track-js
ms.openlocfilehash: 2d40b70d49b1934c9dd2d911369245b1b2e4f2ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93079692"
---
# <a name="how-to-use-azure-table-storage-or-the-azure-cosmos-db-table-api-from-nodejs"></a>Como usar o Armazenamento de Tabelas do Azure e a API da Tabela do Azure Cosmos DB do Node.js
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

Este artigo mostra como criar tabelas, armazenar dados e realizar operações CRUD nos dados. Escolha o serviço Tabela do Azure ou a API de Tabela do Azure Cosmos DB. As amostras são escritas em Node.js.

## <a name="create-an-azure-service-account"></a>Criar uma conta de serviço do Azure

[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

**Crie uma conta de armazenamento do Azure**

[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

**Criar uma conta da API de Tabela do Azure Cosmos DB**

[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="configure-your-application-to-access-azure-storage-or-the-azure-cosmos-db-table-api"></a>Configurar seu aplicativo para acessar o Armazenamento do Azure ou a API da Tabela do Azure Cosmos DB

Para usar o Armazenamento do Microsoft Azure ou o Azure Cosmos DB, você precisa do SDK de Armazenamento do Microsoft Azure para Node.js, que inclui um conjunto de bibliotecas de conveniência que comunicam-se com os serviços REST de Armazenamento.

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Usar o NPM (Gerenciador de Pacotes de Nós) para obter o pacote

1. Use uma interface de linha de comando, como **PowerShell** (Windows), **Terminal** (Mac) ou **Bash** (Unix), e navegue até a pasta em que você criou o aplicativo.
2. Digite **npm install azure-storage** na janela de comando. A saída do comando é semelhante ao exemplo a seguir.

   ```bash
    azure-storage@0.5.0 node_modules\azure-storage
    +-- extend@1.2.1
    +-- xmlbuilder@0.4.3
    +-- mime@1.2.11
    +-- node-uuid@1.4.3
    +-- validator@3.22.2
    +-- underscore@1.4.4
    +-- readable-stream@1.0.33 (string_decoder@0.10.31, isarray@0.0.1, inherits@2.0.1, core-util-is@1.0.1)
    +-- xml2js@0.2.7 (sax@0.5.2)
    +-- request@2.57.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, oauth-sign@0.8.0, tunnel-agent@0.4.1, isstream@0.1.2, json-stringify-safe@5.0.1, bl@0.9.4, combined-stream@1.0.5, qs@3.1.0, mime-types@2.0.14, form-data@0.2.0, http-signature@0.11.0, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)
   ```

3. Você pode executar manualmente o comando **ls** para verificar se uma pasta **node_modules** foi criada. Dentro dessa pasta, você encontrará o pacote **azure-storage** que contém as bibliotecas necessárias para acessar o armazenamento.

### <a name="import-the-package"></a>Importar o pacote

Adicione o código a seguir à parte superior do arquivo **server.js** em seu aplicativo:

```javascript
var azure = require('azure-storage');
```

## <a name="add-your-connection-string"></a>Adicionar sua cadeia de conexão

Você pode se conectar à conta de armazenamento do Azure ou à conta da API de Tabela do Azure Cosmos DB. Obtenha a cadeia de conexão com base no tipo de conta que você está usando.

### <a name="add-an-azure-storage-connection"></a>Adicionar uma conexão do Armazenamento do Azure

O módulo do Azure lê as variáveis de ambiente AZURE_STORAGE_ACCOUNT e AZURE_STORAGE_ACCESS_KEY ou AZURE_STORAGE_CONNECTION_STRING para obter as informações necessárias para se conectar à sua conta do Armazenamento do Azure. Se essas variáveis de ambiente não estiverem definidas, você deverá especificar as informações da conta ao chamar `TableService`. Por exemplo, o seguinte código cria um objeto `TableService`:

```javascript
var tableSvc = azure.createTableService('myaccount', 'myaccesskey');
```

### <a name="add-an-azure-cosmos-db-connection"></a>Adicionar uma conexão do Azure Cosmos DB

Para adicionar uma conexão do Azure Cosmos DB, crie um objeto `TableService` e especifique o nome da conta, a chave primária e o ponto de extremidade. Copie esses valores em **Configurações** > **Cadeia de Conexão** no Portal do Azure da sua conta do Cosmos DB. Por exemplo:

```javascript
var tableSvc = azure.createTableService('myaccount', 'myprimarykey', 'myendpoint');
```

## <a name="create-a-table"></a>Criar uma tabela

O código a seguir cria um objeto `TableService` e o usa para criar uma tabela.

```javascript
var tableSvc = azure.createTableService();
```

A chamada para `createTableIfNotExists` criará uma tabela com o nome especificado, se ela ainda não existir. O exemplo a seguir criará uma nova tabela denominada 'mytable' se ele ainda não existir:

```javascript
tableSvc.createTableIfNotExists('mytable', function(error, result, response){
  if(!error){
    // Table exists or created
  }
});
```

O `result.created` será `true` se uma nova tabela for criada, e `false` se a tabela já existir. O `response` contém informações sobre a solicitação.

### <a name="apply-filters"></a>Aplicar filtros

Você pode aplicar uma filtragem opcional às operações executadas usando `TableService`. As operações de filtragem podem incluir registro em log, repetição automática etc. Os filtros são objetos que implementam um método com a assinatura:

```javascript
function handle (requestOptions, next)
```

Após fazer seu pré-processamento nas opções de solicitação, o método precisará chamar **next**, passando um retorno de chamada com a assinatura abaixo:

```javascript
function (returnObject, finalCallback, next)
```

Nesse retorno de chamada, e após processar o `returnObject` (a resposta da solicitação ao servidor), o retorno de chamada precisará invocar `next`, se ele existir, para continuar processando outros filtros ou apenas invocar `finalCallback` para encerrar a invocação de serviço.

Dois filtros que implementam a lógica de repetição são incluídos com o Azure SDK para Node.js, `ExponentialRetryPolicyFilter` e `LinearRetryPolicyFilter`. O seguinte código cria um objeto `TableService` que usa o `ExponentialRetryPolicyFilter`:

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var tableSvc = azure.createTableService().withFilter(retryOperations);
```

## <a name="add-an-entity-to-a-table"></a>Adicionar uma entidade a uma tabela

Para adicionar uma entidade, primeiro crie um objeto que defina as propriedades da entidade. Todas as entidades devem conter uma **PartitionKey** e **RowKey**, que são identificadores exclusivos da entidade.

* **PartitionKey** - determina a partição na qual a entidade está armazenada.
* **RowKey** - identifica exclusivamente a entidade dentro da partição.

Ambas, **PartitionKey** e **RowKey**, devem ser valores de cadeia de caracteres. Para obter informações, consulte [Noções básicas sobre o modelo de dados do serviço Tabela](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model).

A seguir, um exemplo de definição de uma entidade. O **dueDate** é definido como um tipo de `Edm.DateTime`. A especificação do tipo é opcional, e os tipos são inferidos, se não forem especificados.

```javascript
var task = {
  PartitionKey: {'_':'hometasks'},
  RowKey: {'_': '1'},
  description: {'_':'take out the trash'},
  dueDate: {'_':new Date(2015, 6, 20), '$':'Edm.DateTime'}
};
```

> [!NOTE]
> Existe também um campo `Timestamp` para cada registro, que é definido pelo Azure quando uma entidade é inserida ou atualizada.

Você também pode usar o `entityGenerator` para criar entidades. O exemplo a seguir cria a mesma entidade de tarefa usando o `entityGenerator`.

```javascript
var entGen = azure.TableUtilities.entityGenerator;
var task = {
  PartitionKey: entGen.String('hometasks'),
  RowKey: entGen.String('1'),
  description: entGen.String('take out the trash'),
  dueDate: entGen.DateTime(new Date(Date.UTC(2015, 6, 20))),
};
```

Para adicionar uma entidade à sua tabela, passe o objeto de entidade para o método `insertEntity`.

```javascript
tableSvc.insertEntity('mytable',task, function (error, result, response) {
  if(!error){
    // Entity inserted
  }
});
```

Se a operação for bem-sucedida, `result` conterá a [ETag](https://en.wikipedia.org/wiki/HTTP_ETag) do registro inserido, e `response` conterá informações sobre a operação.

Resposta de exemplo:

```javascript
{ '.metadata': { etag: 'W/"datetime\'2015-02-25T01%3A22%3A22.5Z\'"' } }
```

> [!NOTE]
> Por padrão, `insertEntity` não retorna a entidade inserida como parte da informação de `response`. Se você planeja executar outras operações nessa entidade ou se quiser armazenar as informações em cache, pode ser útil retorná-las como parte de `result`. Você pode fazer isso habilitando `echoContent` da seguinte maneira:
>
> `tableSvc.insertEntity('mytable', task, {echoContent: true}, function (error, result, response) {...}`

## <a name="update-an-entity"></a>Atualizar uma entidade

Há vários métodos disponíveis para atualizar uma entidade existente:

* `replaceEntity` – Atualiza uma entidade existente substituindo-a.
* `mergeEntity` – Atualiza uma entidade existente mesclando novos valores de propriedade com a entidade existente.
* `insertOrReplaceEntity` – Atualiza uma entidade existente substituindo-a. Se nenhuma entidade existir, uma nova será inserida.
* `insertOrMergeEntity` – Atualiza uma entidade existente mesclando novos valores de propriedade com a existente. Se nenhuma entidade existir, uma nova será inserida.

O seguinte exemplo demonstra a atualização de uma entidade usando `replaceEntity`:

```javascript
tableSvc.replaceEntity('mytable', updatedTask, function(error, result, response){
  if(!error) {
    // Entity updated
  }
});
```

> [!NOTE]
> Por padrão, a atualização de uma entidade não verifica se os dados que estão sendo atualizados foram modificados anteriormente por outro processo. Para suporte a atualizações simultâneas:
>
> 1. Obtenha a ETag do objeto que está sendo atualizado. Isso é retornado como parte de `response` para qualquer operação relacionada à entidade e pode ser recuperado por meio de `response['.metadata'].etag`.
> 2. Ao realizar uma operação de atualização em uma entidade, adicione as informações de ETag obtidas anteriormente para a nova entidade. Por exemplo:
>
>       entity2['.metadata'].etag = currentEtag;
> 3. Realize a operação de atualização. Se a entidade foi modificada desde a recuperação do valor de ETag, como outra instância do seu aplicativo, um `error` será retornado informando que a condição da atualização especificada na solicitação não foi atendida.
>
>

Com `replaceEntity` e `mergeEntity`, se a entidade que está sendo atualizada não existir, a operação de atualização falhará. Portanto, se você quiser armazenar uma entidade independentemente de sua existência, use `insertOrReplaceEntity` ou `insertOrMergeEntity`.

O `result` de operações de atualização bem-sucedidas conterá o **Etag** da entidade atualizada.

## <a name="work-with-groups-of-entities"></a>Trabalhar com grupos de entidades

Às vezes, convém enviar várias operações juntas em um lote para garantir o processamento atômico pelo servidor. Para fazer isso, use a classe `TableBatch` a fim de criar um lote; em seguida, use o método `executeBatch` de `TableService` para executar as operações em lote.

 O exemplo a seguir demonstra o envio de duas entidades em um lote:

```javascript
var task1 = {
  PartitionKey: {'_':'hometasks'},
  RowKey: {'_': '1'},
  description: {'_':'Take out the trash'},
  dueDate: {'_':new Date(2015, 6, 20)}
};
var task2 = {
  PartitionKey: {'_':'hometasks'},
  RowKey: {'_': '2'},
  description: {'_':'Wash the dishes'},
  dueDate: {'_':new Date(2015, 6, 20)}
};

var batch = new azure.TableBatch();

batch.insertEntity(task1, {echoContent: true});
batch.insertEntity(task2, {echoContent: true});

tableSvc.executeBatch('mytable', batch, function (error, result, response) {
  if(!error) {
    // Batch completed
  }
});
```

Para operações em lote bem-sucedidas, `result` conterá informações para cada operação no lote.

### <a name="work-with-batched-operations"></a>Trabalhando com operações em lote

Inspecione as operações adicionadas a um lote exibindo a propriedade `operations`. Você também pode usar os seguintes métodos para trabalhar com as operações:

* **clear** - limpa todas as operações de um lote.
* **getOperations** - obtém uma operação do lote.
* **hasOperations** - retorna true se o lote contiver operações.
* **removeOperations** - remove uma operação.
* **size** - retorna o número de operações no lote.

## <a name="retrieve-an-entity-by-key"></a>Recuperar uma entidade por chave

Para retornar uma entidade específica com base em **PartitionKey** e **RowKey**, use o método **retrieveEntity**.

```javascript
tableSvc.retrieveEntity('mytable', 'hometasks', '1', function(error, result, response){
  if(!error){
    // result contains the entity
  }
});
```

Quando essa operação for concluída, `result` conterá a entidade.

## <a name="query-a-set-of-entities"></a>Consultar um conjunto de entidades

Para consultar uma tabela, utilize o objeto **TableQuery** para compilar uma expressão de consulta utilizando as seguintes cláusulas:

* **select** - os campos a serem retornados da consulta.
* **where** - a cláusula where.

  * **and** - uma condição where `and`.
  * **or** - uma condição where `or`.
* **top** - o número de itens a serem buscados.

O exemplo a seguir compila uma consulta que retorna os cinco principais itens com uma PartitionKey de "hometasks".

```javascript
var query = new azure.TableQuery()
  .top(5)
  .where('PartitionKey eq ?', 'hometasks');
```

Como **select** não é usado, todos os campos retornam. Para realizar a consulta em uma tabela, use **queryEntities**. O exemplo a seguir usa essa consulta para retornar entidades de 'mytable'.

```javascript
tableSvc.queryEntities('mytable',query, null, function(error, result, response) {
  if(!error) {
    // query was successful
  }
});
```

Se for bem-sucedido, `result.entries` conterá uma matriz de entidades que correspondem à consulta. Se a consulta não tiver sido capaz de retornar todas as entidades, `result.continuationToken` não será *null* e poderá ser usado como terceiro parâmetro de **queryEntities** para recuperar mais resultados. Para a consulta inicial, use *null* como o terceiro parâmetro.

### <a name="query-a-subset-of-entity-properties"></a>consultar um subconjunto de propriedades da entidade

Uma consulta a uma tabela pode recuperar apenas alguns campos de uma entidade.
Isso reduz a largura de banda e pode melhorar o desempenho da consulta, principalmente em grandes entidades. Use a cláusula **select** e transmita os nomes dos campos a serem retornados. Por exemplo, a consulta a seguir retorna apenas os campos **description** e **dueDate**.

```javascript
var query = new azure.TableQuery()
  .select(['description', 'dueDate'])
  .top(5)
  .where('PartitionKey eq ?', 'hometasks');
```

## <a name="delete-an-entity"></a>Excluir uma entidade

Você pode excluir uma entidade usando suas chaves de partição e de linha. Neste exemplo, o objeto **task1** contém os valores **RowKey** e **PartitionKey** da entidade a ser excluída. Depois o objeto é passado para o método **deleteEntity** .

```javascript
var task = {
  PartitionKey: {'_':'hometasks'},
  RowKey: {'_': '1'}
};

tableSvc.deleteEntity('mytable', task, function(error, response){
  if(!error) {
    // Entity deleted
  }
});
```

> [!NOTE]
> Considere o uso de ETags ao excluir itens, para garantir que o item não seja modificado por outro processo. Veja [Atualizar uma entidade](#update-an-entity) para obter informações sobre o uso de ETags.
>
>

## <a name="delete-a-table"></a>Excluir uma tabela

O código a seguir exclui uma tabela de uma conta de armazenamento.

```javascript
tableSvc.deleteTable('mytable', function(error, response){
    if(!error){
        // Table deleted
    }
});
```

Se você não tiver certeza de que a tabela existe, use **deleteTableIfExists**.

## <a name="use-continuation-tokens"></a>Usar tokens de continuação

Quando você estiver consultando tabelas com grandes quantidades de resultados, deverá procurar tokens de continuação. Pode haver grandes quantidades de dados disponíveis para a sua consulta dos quais talvez você não saiba, se não criá-la de modo a reconhecer quando um token de continuação está presente.

O objeto **results** retornado durante a consulta de entidades define uma propriedade `continuationToken` quando esse token está presente. Você pode usar isso ao realizar uma consulta para continuar a mover-se pela partição e entidades de tabela.

Ao consultar, é possível fornecer um parâmetro `continuationToken` entre a instância do objeto de consulta e a função de retorno de chamada:

```javascript
var nextContinuationToken = null;
dc.table.queryEntities(tableName,
    query,
    nextContinuationToken,
    function (error, results) {
        if (error) throw error;

        // iterate through results.entries with results

        if (results.continuationToken) {
            nextContinuationToken = results.continuationToken;
        }

    });
```

Se inspecionar o objeto `continuationToken`, você encontrará propriedades como `nextPartitionKey`, `nextRowKey` e `targetLocation`, que podem ser usadas para iterar por todos os resultados.

Você também pode usar `top` juntamente com `continuationToken` para definir o tamanho da página.

## <a name="work-with-shared-access-signatures"></a>Trabalhar com assinaturas de acesso compartilhado

SAS (Assinaturas de acesso compartilhado) são uma maneira segura de fornecer acesso granular a tabelas sem fornecer o nome ou as chaves da conta de Armazenamento. As SAS são muitas vezes usadas para fornecer acesso limitado aos seus dados, como permitir que um aplicativo móvel consulte registros.

Um aplicativo confiável, como um serviço baseado em nuvem, gera uma SAS usando **generateSharedAccessSignature** de **TableService** e a fornece a um aplicativo não confiável ou semiconfiável, como um aplicativo móvel. A SAS é gerada utilizando uma política que descreve as datas inicial e final durante as quais a SAS é válida, assim como o nível de acesso concedido ao titular da SAS.

O exemplo a seguir gera uma nova política de acesso compartilhado que permitirá que o titular da SAS consulte ('r') a tabela, e expira 100 minutos após o momento em que é criado.

```javascript
var startDate = new Date();
var expiryDate = new Date(startDate);
expiryDate.setMinutes(startDate.getMinutes() + 100);
startDate.setMinutes(startDate.getMinutes() - 100);

var sharedAccessPolicy = {
  AccessPolicy: {
    Permissions: azure.TableUtilities.SharedAccessPermissions.QUERY,
    Start: startDate,
    Expiry: expiryDate
  },
};

var tableSAS = tableSvc.generateSharedAccessSignature('mytable', sharedAccessPolicy);
var host = tableSvc.host;
```

Observe que você também deve fornecer as informações do host, já que elas são necessárias quando o titular da SAS tenta acessar a tabela.

O aplicativo cliente usa a SAS com **TableServiceWithSAS** para executar operações na tabela. O exemplo a seguir conecta à tabela e executa uma consulta. Confira o artigo [Conceder acesso limitado a recursos de Armazenamento do Azure usando SAS (assinaturas de acesso compartilhado)](../storage/common/storage-sas-overview.md) sobre o formato de tableSAS.

```javascript
// Note in the following command, host is in the format: `https://<your_storage_account_name>.table.core.windows.net` and the tableSAS is in the format: `sv=2018-03-28&si=saspolicy&tn=mytable&sig=9aCzs76n0E7y5BpEi2GvsSv433BZa22leDOZXX%2BXXIU%3D`;

var sharedTableService = azure.createTableServiceWithSas(host, tableSAS);
var query = azure.TableQuery()
  .where('PartitionKey eq ?', 'hometasks');

sharedTableService.queryEntities(query, null, function(error, result, response) {
  if(!error) {
    // result contains the entities
  }
});
```

Como a SAS foi gerada só com acesso de consulta, se você tentar inserir, atualizar ou excluir entidades, surgirá um erro.

### <a name="access-control-lists"></a>Listas de Controle de Acesso

Você também pode usar uma ACL (Lista de Controle de Acesso) para definir a política de acesso para uma SAS. Isso é útil se você quiser permitir que vários clientes acessem a tabela, mas oferecem diferentes políticas de acesso para cada cliente.

Uma ACL é implementada através de um conjunto de políticas de acesso, com uma ID associada a cada política. O seguinte exemplo define duas políticas: uma para “user1” e outra para “user2”:

```javascript
var sharedAccessPolicy = {
  user1: {
    Permissions: azure.TableUtilities.SharedAccessPermissions.QUERY,
    Start: startDate,
    Expiry: expiryDate
  },
  user2: {
    Permissions: azure.TableUtilities.SharedAccessPermissions.ADD,
    Start: startDate,
    Expiry: expiryDate
  }
};
```

O exemplo a seguir obtém a ACL atual para a tabela **hometasks** e adiciona as novas políticas usando **setTableAcl**. Essa abordagem permite:

```javascript
var extend = require('extend');
tableSvc.getTableAcl('hometasks', function(error, result, response) {
if(!error){
    var newSignedIdentifiers = extend(true, result.signedIdentifiers, sharedAccessPolicy);
    tableSvc.setTableAcl('hometasks', newSignedIdentifiers, function(error, result, response){
      if(!error){
        // ACL set
      }
    });
  }
});
```

Após a definição da ACL, você pode criar uma SAS com base na ID de uma política. O exemplo a seguir cria uma nova SAS para 'user2':

```javascript
tableSAS = tableSvc.generateSharedAccessSignature('hometasks', { Id: 'user2' });
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte os recursos a seguir.

* [O Gerenciador de Armazenamento do Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) é um aplicativo autônomo e gratuito da Microsoft que possibilita o trabalho visual com os dados do Armazenamento do Azure no Windows, MacOS e Linux.
* Repositório [SDK do Armazenamento do Azure para Node.js](https://github.com/Azure/azure-storage-node) no GitHub.
* [Desenvolvedores do Azure para Node.js](/azure/developer/javascript/)
* [Criar um aplicativo Web do Node.js no Azure](../app-service/quickstart-nodejs.md)
* [Criar e implantar um aplicativo Node.js para um serviço de nuvem do AzureServiço de nuvem do Node.js](../cloud-services/cloud-services-nodejs-develop-deploy-app.md) (usando o Windows PowerShell)