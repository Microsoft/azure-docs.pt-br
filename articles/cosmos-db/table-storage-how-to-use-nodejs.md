---
title: Como usar o Armazenamento de Tabelas do Azure por meio do Node.js | Microsoft Docs
description: "Armazene dados estruturados na nuvem usando o Armazenamento de Tabelas do Azure, um repositório de dados NoSQL."
services: cosmos-db
documentationcenter: nodejs
author: mimig1
manager: jhubbard
editor: tysonn
ms.assetid: fc2e33d2-c5da-4861-8503-53fdc25750de
ms.service: cosmos-db
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 12/08/2016
ms.author: mimig
ms.openlocfilehash: e8b22dad84e89be6051041e60ca9d72ec4aa3c56
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-azure-table-storage-from-nodejs"></a>Como usar o armazenamento de Tabela do Azure por meio do Node.js
[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-cosmos-db-langsoon-tip-include](../../includes/storage-table-cosmos-db-langsoon-tip-include.md)]

## <a name="overview"></a>Visão geral
Este tópico mostra como executar cenários comuns usando o serviço Tabela do Azure em um aplicativo do Node.js.

Os exemplos de código neste tópico pressupõem que você já tenha um aplicativo do Node.js. Para obter informações sobre como criar um aplicativo do Node.js no Azure, confira um destes tópicos:

* [Criar um aplicativo Web do Node.js no Serviço de Aplicativo do Azure](../app-service/app-service-web-get-started-nodejs.md)
* [Criar e implantar um aplicativo Node.js para um serviço de nuvem do AzureServiço de nuvem do Node.js](../cloud-services/cloud-services-nodejs-develop-deploy-app.md) (usando o Windows PowerShell)

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="configure-your-application-to-access-azure-storage"></a>Configurar seu aplicativo para acessar o Armazenamento de Blob
Para usar o Armazenamento do Azure, você precisa do SDK do Armazenamento do Azure para Node.js, que inclui um conjunto de bibliotecas de conveniência que se comunicam com os serviços REST do armazenamento.

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Usar o NPM (Gerenciador de Pacotes de Nós) para obter o pacote
1. Use uma interface de linha de comando, como **PowerShell** (Windows), **Terminal** (Mac) ou **Bash** (Unix), e navegue até a pasta em que você criou o aplicativo.
2. Digite **npm install azure-storage** na janela de comando. A saída do comando é semelhante ao exemplo a seguir.

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
3. Você pode executar manualmente o comando **ls** para verificar se uma pasta **node\_modules** foi criada. Dentro dessa pasta, você encontrará o pacote **azure-storage** que contém as bibliotecas necessárias para acessar o armazenamento.

### <a name="import-the-package"></a>Importar o pacote
Adicione o código a seguir à parte superior do arquivo **server.js** em seu aplicativo:

```nodejs
var azure = require('azure-storage');
```

## <a name="set-up-an-azure-storage-connection"></a>Configurar uma conexão do Armazenamento do Azure
O módulo do Azure lerá as variáveis de ambiente AZURE\_STORAGE\_ACCOUNT e AZURE\_STORAGE\_ACCESS\_KEY ou AZURE\_STORAGE\_CONNECTION\_STRING para obter as informações necessárias para se conectar à sua conta de armazenamento do Azure. Se essas variáveis de ambiente não estiverem definidas, você deverá especificar as informações da conta ao chamar **TableService**.

## <a name="create-a-table"></a>Criar uma tabela
O código a seguir cria um objeto **TableService** e utiliza-o para criar uma nova tabela. Adicione o seguinte próximo à parte superior do **server.js**.

```nodejs
var tableSvc = azure.createTableService();
```

A chamada para **createTableIfNotExists** criará uma nova tabela com o nome especificado, se ela ainda não existir. O exemplo a seguir criará uma nova tabela denominada 'mytable' se ele ainda não existir:

```nodejs
tableSvc.createTableIfNotExists('mytable', function(error, result, response){
  if(!error){
    // Table exists or created
  }
});
```

O `result.created` será `true` se uma nova tabela for criada e `false` se a tabela já existir. O `response` conterá informações sobre a solicitação.

### <a name="filters"></a>Filtros
É possível aplicar operações de filtragem opcionais às operações executadas usando **TableService**. As operações de filtragem podem incluir registro em log, repetição automática, etc. Os filtros são objetos que implementam um método com a assinatura:

```nodejs
function handle (requestOptions, next)
```

Depois de fazer seu pré-processamento nas opções de solicitação, o método precisará chamar "next", transmitindo um retorno de chamada com a seguinte assinatura:

```nodejs
function (returnObject, finalCallback, next)
```

Nesse retorno de chamada, e depois de processar o returnObject (a resposta da solicitação ao servidor), o retorno de chamada precisará invocar “next”, se ele existir, para continuar processando outros filtros ou simplesmente invocar finalCallback para terminar a invocação de serviço.

Dois filtros que implementam a lógica de repetição estão incluídos no SDK do Azure para Node.js, **ExponentialRetryPolicyFilter** e **LinearRetryPolicyFilter**. O seguinte código cria um objeto **TableService** que usa o **ExponentialRetryPolicyFilter**:

```nodejs
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var tableSvc = azure.createTableService().withFilter(retryOperations);
```

## <a name="add-an-entity-to-a-table"></a>Adicionar uma entidade a uma tabela
Para adicionar uma entidade, primeiro crie um objeto que defina as propriedades da entidade. Todas as entidades devem conter uma **PartitionKey** e **RowKey**, que são identificadores exclusivos da entidade.

* **PartitionKey** – determina a partição em que a entidade está armazenada
* **RowKey** – identifica exclusivamente a entidade dentro da partição

Ambas, **PartitionKey** e **RowKey**, devem ser valores de cadeia de caracteres. Para obter informações, consulte [Noções básicas sobre o modelo de dados do serviço Tabela](http://msdn.microsoft.com/library/azure/dd179338.aspx).

A seguir, um exemplo de definição de uma entidade. Observe que **dueDate** é definido com um tipo de **Edm.DateTime**. A especificação do tipo é opcional, e os tipos serão inferidos se não especificados.

```nodejs
var task = {
  PartitionKey: {'_':'hometasks'},
  RowKey: {'_': '1'},
  description: {'_':'take out the trash'},
  dueDate: {'_':new Date(2015, 6, 20), '$':'Edm.DateTime'}
};
```

> [!NOTE]
> Existe também um campo **Carimbo de Data/Hora** para cada registro, que é definido pelo Azure quando uma entidade é inserida ou atualizada.
>
>

Você também pode usar o **entityGenerator** para criar entidades. O exemplo a seguir cria a mesma entidade tarefa usando o **entityGenerator**.

```nodejs
var entGen = azure.TableUtilities.entityGenerator;
var task = {
  PartitionKey: entGen.String('hometasks'),
  RowKey: entGen.String('1'),
  description: entGen.String('take out the trash'),
  dueDate: entGen.DateTime(new Date(Date.UTC(2015, 6, 20))),
};
```

Para adicionar uma entidade à sua tabela, passe o objeto de entidade para o método **insertEntity** .

```nodejs
tableSvc.insertEntity('mytable',task, function (error, result, response) {
  if(!error){
    // Entity inserted
  }
});
```

Se a operação for bem-sucedida, `result` conterá a [ETag](http://en.wikipedia.org/wiki/HTTP_ETag) do registro inserido e `response` conterá informações sobre a operação.

Resposta de exemplo:

```nodejs
{ '.metadata': { etag: 'W/"datetime\'2015-02-25T01%3A22%3A22.5Z\'"' } }
```

> [!NOTE]
> Por padrão, **insertEntity** não retorna a entidade inserida como parte da informação de `response`. Se você planeja executar outras operações nessa entidade ou se desejar armazenar as informações em cache, pode ser útil retorná-las como parte de `result`. Você pode fazer isso habilitando **echoContent** da seguinte maneira:
>
> `tableSvc.insertEntity('mytable', task, {echoContent: true}, function (error, result, response) {...}`
>
>

## <a name="update-an-entity"></a>Atualizar uma entidade
Há vários métodos disponíveis para atualizar uma entidade existente:

* **replaceEntity** – atualiza uma entidade existente ao substituí-la
* **mergeEntity** – atualiza uma entidade existente mesclando novos valores de propriedade à entidade existente
* **insertOrReplaceEntity** – atualiza uma entidade existente substituindo-a. Se não existir nenhuma entidade, uma nova será inserida
* **insertOrMergeEntity** – atualiza uma entidade existente mesclando novos valores de propriedade à existente. Se não existir nenhuma entidade, uma nova será inserida

O exemplo a seguir demonstra a atualização de uma entidade usando **replaceEntity**:

```nodejs
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

Com **replaceEntity** e **mergeEntity**, se a entidade que estiver sendo atualizada não existir, a operação de atualização falhará. Portanto, se desejar armazenar uma entidade independentemente de sua existência, use **insertOrReplaceEntity** ou **insertOrMergeEntity**.

O `result` para operações de atualização de sucesso conterá **Etag** da entidade atualizada.

## <a name="work-with-groups-of-entities"></a>Trabalhar com grupos de entidades
Às vezes, convém enviar várias operações juntas em um lote para garantir o processamento atômico pelo servidor. Para fazer isso, use a classe **TableBatch** para criar um lote; em seguida, use o método **executeBatch** de **TableService** para executar as operações em lote.

 O exemplo a seguir demonstra o envio de duas entidades em um lote:

```nodejs
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
Operações adicionadas ao lote podem ser inspecionadas ao exibir a propriedade `operations` . Você também pode usar os seguintes métodos para trabalhar com as operações:

* **clear** – limpa todas as operações de um lote.
* **getOperations** – obtém uma operação do lote
* **hasOperations** – retorna true se o lote contiver operações
* **removeOperations** – remove uma operação
* **size** – retorna o número de operações no lote

## <a name="retrieve-an-entity-by-key"></a>Recuperar uma entidade por chave
Para retornar uma entidade específica com base em **PartitionKey** e **RowKey**, use o método **retrieveEntity**.

```nodejs
tableSvc.retrieveEntity('mytable', 'hometasks', '1', function(error, result, response){
  if(!error){
    // result contains the entity
  }
});
```

Quando essa operação for concluída, `result` conterá a entidade.

## <a name="query-a-set-of-entities"></a>Consultar um conjunto de entidades
Para consultar uma tabela, utilize o objeto **TableQuery** para compilar uma expressão de consulta utilizando as seguintes cláusulas:

* **select** – os campos a serem retornados da consulta
* **where** – a cláusula “where”

  * **and** – uma condição where `and`
  * **or** – uma condição where `or`
* **top** – o número de itens a serem buscados

O exemplo a seguir compila uma consulta que retornará os cinco principais itens com uma PartitionKey de “hometasks”.

```nodejs
var query = new azure.TableQuery()
  .top(5)
  .where('PartitionKey eq ?', 'hometasks');
```

Como **select** não é usado, todos os campos serão retornados. Para realizar a consulta em uma tabela, use **queryEntities**. O exemplo a seguir usa essa consulta para retornar entidades de 'mytable'.

```nodejs
tableSvc.queryEntities('mytable',query, null, function(error, result, response) {
  if(!error) {
    // query was successful
  }
});
```

Se for bem-sucedido, `result.entries` conterá uma matriz de entidades que correspondem à consulta. Se a consulta não tiver sido capaz de retornar todas as entidades, `result.continuationToken` não será*null* e poderá ser usado como terceiro parâmetro de **queryEntities** para recuperar mais resultados. Para a consulta inicial, use *null* como o terceiro parâmetro.

### <a name="query-a-subset-of-entity-properties"></a>consultar um subconjunto de propriedades da entidade
Uma consulta a uma tabela pode recuperar apenas alguns campos de uma entidade.
Isso reduz a largura de banda e pode melhorar o desempenho da consulta, principalmente em grandes entidades. Use a cláusula **select** e transmita os nomes dos campos a serem retornados. Por exemplo, a consulta a seguir retornará apenas os campos **description** e **dueDate**.

```nodejs
var query = new azure.TableQuery()
  .select(['description', 'dueDate'])
  .top(5)
  .where('PartitionKey eq ?', 'hometasks');
```

## <a name="delete-an-entity"></a>Excluir uma entidade
Você pode excluir uma entidade usando suas chaves de partição e de linha. Neste exemplo, o objeto **task1** contém os valores **RowKey** e **PartitionKey** da entidade a ser excluída. Depois o objeto é passado para o método **deleteEntity** .

```nodejs
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

```nodejs
tableSvc.deleteTable('mytable', function(error, response){
    if(!error){
        // Table deleted
    }
});
```

Se você não tiver certeza de que a tabela existe, use **deleteTableIfExists**.

## <a name="use-continuation-tokens"></a>Usar tokens de continuação
Quando você estiver consultando tabelas com grandes quantidades de resultados, deverá procurar tokens de continuação. Pode haver grandes quantidades de dados disponíveis para a sua consulta dos quais talvez você não saiba, se não criá-la de modo a reconhecer quando um token de continuação está presente.

O objeto de resultados retornado durante a consulta de entidades define uma propriedade `continuationToken` quando esse token está presente. Você pode usar isso ao realizar uma consulta para continuar a mover-se pela partição e entidades de tabela.

Ao consultar, um parâmetro continuationToken pode ser fornecido entre a instância do objeto de consulta e a função de retorno de chamada:

```nodejs
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

Também há um exemplo de continuação dentro do repositório do Node.js do Armazenamento do Azure no GitHub. Procure `examples/samples/continuationsample.js`.

## <a name="work-with-shared-access-signatures"></a>Trabalhar com assinaturas de acesso compartilhado
SAS (Assinaturas de acesso compartilhado) são uma maneira segura de fornecer acesso granular a tabelas sem fornecer o nome ou as chaves da conta de armazenamento. As SAS são muitas vezes usadas para fornecer acesso limitado aos seus dados, como permitir que um aplicativo móvel consulte registros.

Um aplicativo confiável, como um serviço baseado em nuvem, gera uma SAS usando **generateSharedAccessSignature** de **TableService** e a fornece a um aplicativo não confiável ou semiconfiável, como um aplicativo móvel. A SAS é gerada utilizando uma política que descreve as datas inicial e final durante as quais a SAS é válida, assim como o nível de acesso concedido ao titular da SAS.

O exemplo a seguir gera uma nova política de acesso compartilhado que permitirá que o titular da SAS consulte ('r') a tabela, e expira 100 minutos após o momento em que é criado.

```nodejs
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

Observe que também devem ser fornecidas as informações do host, já que são necessárias quando o titular da SAS tenta acessar a tabela.

O aplicativo cliente usa a SAS com **TableServiceWithSAS** para executar operações na tabela. O exemplo a seguir conecta à tabela e executa uma consulta.

```nodejs
var sharedTableService = azure.createTableServiceWithSas(host, tableSAS);
var query = azure.TableQuery()
  .where('PartitionKey eq ?', 'hometasks');

sharedTableService.queryEntities(query, null, function(error, result, response) {
  if(!error) {
    // result contains the entities
  }
});
```

Como a SAS foi gerada só com acesso de consulta, se for feita uma tentativa de inserir, atualizar ou excluir entidades, será retornado um erro.

### <a name="access-control-lists"></a>Listas de Controle de Acesso
Você também pode usar uma ACL (Lista de Controle de Acesso) para definir a política de acesso para uma SAS. Isso é útil se você quiser permitir que vários clientes acessem a tabela, mas oferecem diferentes políticas de acesso para cada cliente.

Uma ACL é implementada através de um conjunto de políticas de acesso, com uma ID associada a cada política. O seguinte exemplo define duas políticas: uma para “user1” e outra para “user2”:

```nodejs
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

```nodejs
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

Uma vez que a ACL foi definida, você pode criar uma SAS com base na ID de uma política. O exemplo a seguir cria uma nova SAS para 'user2':

```nodejs
tableSAS = tableSvc.generateSharedAccessSignature('hometasks', { Id: 'user2' });
```

## <a name="next-steps"></a>Próximas etapas
Para saber mais, consulte os recursos a seguir.

* [O Gerenciador de Armazenamento do Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) é um aplicativo autônomo e gratuito da Microsoft que possibilita o trabalho visual com os dados do Armazenamento do Azure no Windows, MacOS e Linux.
* [SDK do Armazenamento do Azure para Node](https://github.com/Azure/azure-storage-node) no GitHub.
* [Centro de desenvolvedores do Node. js](/develop/nodejs/)
* [Criar e implantar um aplicativo Node.js em um site do Azure](../app-service/app-service-web-get-started-nodejs.md)