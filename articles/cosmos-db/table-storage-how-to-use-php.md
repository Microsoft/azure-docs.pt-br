---
title: Usar o serviço Tabela de Armazenamento do Azure ou API de Tabela do Azure Cosmos DB do PHP
description: Armazene dados estruturados na nuvem usando o Armazenamento de Tabelas do Azure ou a API de Tabela do Azure Cosmos DB do PHP.
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: php
ms.topic: sample
ms.date: 07/23/2020
ms.openlocfilehash: 9d059c899e4a64d4d2c1b880b2a1d0f89258f33b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93079624"
---
# <a name="how-to-use-azure-storage-table-service-or-the-azure-cosmos-db-table-api-from-php"></a>Como usar o serviço de Tabela de Armazenamento do Azure ou a API da tabela do Azure Cosmos DB do PHP
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

Este artigo mostra como criar tabelas, armazenar dados e realizar operações CRUD nos dados. Escolha o serviço Tabela do Azure ou a API de Tabela do Azure Cosmos DB. Os exemplos são escritos em PHP e usam a [Biblioteca do Cliente de PHP da Tabela Armazenamento do Azure][download]. Os cenários abrangidos incluem **criar e excluir uma tabela** e **inserir, excluir e consultar entidades em uma tabela**. Para obter mais informações sobre o serviço Tabela do Azure, consulte a seção [Próximas etapas](#next-steps) .

## <a name="create-an-azure-service-account"></a>Criar uma conta de serviço do Azure

[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

**Crie uma conta de armazenamento do Azure**

[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

**Criar uma conta da API de Tabela do Azure Cosmos DB**

[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="create-a-php-application"></a>Criar um aplicativo PHP

O único requisito para criar um aplicativo PHP para acessar o serviço Tabela de Armazenamento ou a API de tabela do Azure Cosmos DB é fazer referência a classes na tabela de armazenamento do azure SDK para PHP de dentro de seu código. Você pode usar as ferramentas de desenvolvimento para criar seu aplicativo, incluindo o bloco de notas.

Neste guia, você usa o serviço Tabela de Armazenamento ou os recursos do Azure Cosmos DB que podem ser chamados de dentro de um aplicativo PHP localmente ou no código em execução dentro de um site, função de trabalho ou função web do Azure.

## <a name="get-the-client-library"></a>Obter a biblioteca de cliente

1. Crie um arquivo chamado composer.json na raiz do seu projeto e adicione o seguinte código a ele:
   ```json
   {
   "require": {
    "microsoft/azure-storage-table": "*"
   }
   }
   ```
2. Baixe [composer.phar](https://getcomposer.org/composer.phar) na sua raiz. 
3. Abra um prompt de comando e execute o seguinte comando na raiz do projeto:
   ```
   php composer.phar install
   ```
   Como alternativa, acesse a [biblioteca de clientes PHP da Tabela de Armazenamento do Azure ](https://github.com/Azure/azure-storage-php/tree/master/azure-storage-table) no GitHub para clonar o código-fonte.

## <a name="add-required-references"></a>Adicionar as referências necessárias

Para usar o serviço de Tabela de Armazenamento ou APIs do Azure Cosmos DB, você deve:

* Fazer referência ao arquivo de carregador automático usando a instrução [require_once][require_once], e
* Fazer referência a qualquer classe que você usa.

O exemplo a seguir mostra como incluir o arquivo de carregador automático e fazer referência à classe **TableRestProxy**.

```php
require_once 'vendor/autoload.php';
use MicrosoftAzure\Storage\Table\TableRestProxy;
```

Nos exemplos abaixo, a instrução `require_once` é mostrada sempre, mas somente as classes necessárias para executar o exemplo são referenciadas.

## <a name="add-your-connection-string"></a>Adicionar sua cadeia de conexão

Você pode se conectar à conta de armazenamento do Azure ou à conta da API de Tabela do Azure Cosmos DB. Obtenha a cadeia de conexão com base no tipo de conta que você está usando.

### <a name="add-a-storage-table-service-connection"></a>Adicionar uma conexão de serviço de Tabela de Armazenamento

Para criar uma instância de um cliente de serviço Tabela de Armazenamento, você deve primeiramente ter uma cadeia de conexão válida. O formato da cadeia de conexão do serviço Tabela de Armazenamento é:

```php
$connectionString = "DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]"
```

### <a name="add-a-storage-emulator-connection"></a>Adicionar uma conexão do Emulador de Armazenamento

Para acessar o emulador de Armazenamento:

```php
UseDevelopmentStorage = true
```

### <a name="add-an-azure-cosmos-db-connection"></a>Adicionar uma conexão do Azure Cosmos DB

Para criar uma instância de um cliente de Tabela do Azure Cosmos DB, você deve primeiramente ter uma cadeia de conexão válida. O formato para a cadeia de conexão do Azure Cosmos DB é:

```php
$connectionString = "DefaultEndpointsProtocol=[https];AccountName=[myaccount];AccountKey=[myaccountkey];TableEndpoint=[https://myendpoint/]";
```

Para criar um cliente do serviço de tabela do Azure ou o cliente do Azure Cosmos DB, você precisa usar a classe **TableRestProxy**. Você pode:

* Passar a cadeia de conexão diretamente para ele ou
* Use o **CloudConfigurationManager (CCM)** para verificar várias fontes externas da cadeia de conexão:
  * Por padrão, ele é fornecido com suporte para uma fonte externa – variáveis de ambiente.
  * Você pode adicionar novas origens estendendo a classe `ConnectionStringSource`.

Para os exemplos descritos aqui, a cadeia de conexão é passada diretamente.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;

$tableClient = TableRestProxy::createTableService($connectionString);
```

## <a name="create-a-table"></a>Criar uma tabela

O objeto **TableRestProxy** permite que você crie uma tabela com o método **createTable**. Ao criar uma tabela, você pode definir o tempo limite do serviço Tabela. (Para obter mais informações sobre o tempo limite do serviço Tabela, confira [Como configurar tempos limite para operações do serviço Tabela][table-service-timeouts].)

```php
require_once 'vendor\autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create Table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

try    {
    // Create table.
    $tableClient->createTable("mytable");
}
catch(ServiceException $e){
    $code = $e->getCode();
    $error_message = $e->getMessage();
    // Handle exception based on error codes and messages.
    // Error codes and messages can be found here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
}
```

Para obter informações sobre restrições em nomes de tabela, confira [Noções básicas sobre o modelo de dados do serviço Tabela][table-data-model].

## <a name="add-an-entity-to-a-table"></a>Adicionar uma entidade a uma tabela

Para adicionar uma entidade a uma tabela, crie um novo objeto **Entidade** e passá-lo para **TableRestProxy->insertEntity**. Observe que ao criar uma entidade, você deve especificar um `PartitionKey` e `RowKey`. Estes são os identificadores exclusivos para uma entidade e são os valores que podem ser consultados muito mais rápido que as outras propriedades da entidade. O sistema usa `PartitionKey` para distribuir automaticamente as entidades das tabelas por vários nós de Armazenamento. As entidades com a mesma `PartitionKey` são armazenadas no mesmo nó. (As operações em várias entidades armazenadas no mesmo nó são executadas melhor do que em entidades armazenadas em nós diferentes.) `RowKey` é a ID exclusiva de uma entidade dentro de uma partição.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

$entity = new Entity();
$entity->setPartitionKey("tasksSeattle");
$entity->setRowKey("1");
$entity->addProperty("Description", null, "Take out the trash.");
$entity->addProperty("DueDate",
                        EdmType::DATETIME,
                        new DateTime("2012-11-05T08:15:00-08:00"));
$entity->addProperty("Location", EdmType::STRING, "Home");

try{
    $tableClient->insertEntity("mytable", $entity);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
}
```

Para obter informações sobre tipos e propriedades de Tabela, confira [Noções básicas sobre o modelo de dados do serviço Tabela][table-data-model].

A classe **TableRestProxy** oferece dois métodos alternativos para inserir entidades: **insertOrMergeEntity** e **insertOrReplaceEntity**. Para usar esses métodos, crie uma nova **Entidade** e passe-a como um parâmetro para qualquer método. Cada método vai inserir a entidade se ela não existir. Se a entidade já existir, **insertOrMergeEntity** atualiza os valores de propriedade se as propriedades já existirem e adiciona novas propriedades se elas não existirem, enquanto **insertOrReplaceEntity** substitui completamente uma entidade existente. O exemplo a seguir mostra como usar o **insertOrMergeEntity**. Se a entidade com `PartitionKey` "tasksSeattle" e `RowKey` "1" ainda não existir, ela será inserida. No entanto, se ela tiver sido inserida anteriormente (conforme mostrado no exemplo acima), a propriedade `DueDate` será atualizada e a propriedade `Status` será adicionada. As propriedades `Description` e `Location` também são atualizadas, mas com valores que efetivamente as deixam inalteradas. Se essas duas últimas propriedades não foram adicionadas conforme mostrado no exemplo, mas existiam na entidade de destino, seus valores existentes permaneceriam inalterados.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

//Create new entity.
$entity = new Entity();

// PartitionKey and RowKey are required.
$entity->setPartitionKey("tasksSeattle");
$entity->setRowKey("1");

// If entity exists, existing properties are updated with new values and
// new properties are added. Missing properties are unchanged.
$entity->addProperty("Description", null, "Take out the trash.");
$entity->addProperty("DueDate", EdmType::DATETIME, new DateTime()); // Modified the DueDate field.
$entity->addProperty("Location", EdmType::STRING, "Home");
$entity->addProperty("Status", EdmType::STRING, "Complete"); // Added Status field.

try    {
    // Calling insertOrReplaceEntity, instead of insertOrMergeEntity as shown,
    // would simply replace the entity with PartitionKey "tasksSeattle" and RowKey "1".
    $tableClient->insertOrMergeEntity("mytable", $entity);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="retrieve-a-single-entity"></a>Recuperar uma única entidade

O método **TableRestProxy->getEntity** permite que você recupere uma única entidade consultando seu `PartitionKey` e `RowKey`. No exemplo abaixo, a chave da partição `tasksSeattle` e a chave de linha `1` são passadas para o método **getEntity**.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

try    {
    $result = $tableClient->getEntity("mytable", "tasksSeattle", 1);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

$entity = $result->getEntity();

echo $entity->getPartitionKey().":".$entity->getRowKey();
```

## <a name="retrieve-all-entities-in-a-partition"></a>Recuperar todas as entidades em uma partição

As consultas de entidade são construídas com filtros (para obter mais informações, confira [Consultando tabelas e entidades][filters]). Para recuperar todas as entidades na partição, use o filtro "PartitionKey eq *nome_da_partição*". O exemplo a seguir mostra como recuperar todas as entidades na partição `tasksSeattle` passando um filtro para o método **queryEntities** .

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

$filter = "PartitionKey eq 'tasksSeattle'";

try    {
    $result = $tableClient->queryEntities("mytable", $filter);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

$entities = $result->getEntities();

foreach($entities as $entity){
    echo $entity->getPartitionKey().":".$entity->getRowKey()."<br />";
}
```

## <a name="retrieve-a-subset-of-entities-in-a-partition"></a>Recuperar um subconjunto de entidades em uma partição

O mesmo padrão usado no exemplo anterior pode ser usado para recuperar qualquer subconjunto de entidades em uma partição. O subconjunto de entidades recuperado é determinado pelo filtro usado (para obter mais informações, confira [Consultando tabelas e entidades][filters]). O exemplo a seguir mostra como usar um filtro para recuperar todas as entidades com um `Location` específico e um `DueDate` menor que uma data especificada.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

$filter = "Location eq 'Office' and DueDate lt '2012-11-5'";

try    {
    $result = $tableClient->queryEntities("mytable", $filter);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

$entities = $result->getEntities();

foreach($entities as $entity){
    echo $entity->getPartitionKey().":".$entity->getRowKey()."<br />";
}
```

## <a name="retrieve-a-subset-of-entity-properties"></a>Recuperar um subconjunto de propriedades da entidade

Uma consulta pode recuperar um subconjunto de propriedades da entidade. Essa técnica, chamada *projeção*, reduz a largura de banda e pode melhorar o desempenho da consulta, principalmente para grandes entidades. Para especificar uma propriedade a ser recuperada, passe o nome da propriedade para o método **Consulta->addSelectField**. Você pode chamar esse método várias vezes para adicionar mais propriedades. Depois da execução de **TableRestProxy->queryEntities**, as entidades retornadas somente terão as propriedades selecionadas. (Se você desejar retornar um subconjunto de entidades de tabela, use um filtro conforme as consultas acima.)

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Table\Models\QueryEntitiesOptions;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

$options = new QueryEntitiesOptions();
$options->addSelectField("Description");

try    {
    $result = $tableClient->queryEntities("mytable", $options);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

// All entities in the table are returned, regardless of whether
// they have the Description field.
// To limit the results returned, use a filter.
$entities = $result->getEntities();

foreach($entities as $entity){
    $description = $entity->getProperty("Description")->getValue();
    echo $description."<br />";
}
```

## <a name="update-an-entity"></a>Atualizar uma entidade

Você pode atualizar uma entidade existente usando os métodos **Entidade->setProperty** e **Entidade->addProperty** na entidade e, em seguida, chamando **TableRestProxy->updateEntity**. O exemplo a seguir recupera uma entidade, modifica uma propriedade, remove outra propriedade e adiciona uma nova propriedade. Observe que você pode remover uma propriedade definindo seu valor como **null**.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

$result = $tableClient->getEntity("mytable", "tasksSeattle", 1);

$entity = $result->getEntity();
$entity->setPropertyValue("DueDate", new DateTime()); //Modified DueDate.
$entity->setPropertyValue("Location", null); //Removed Location.
$entity->addProperty("Status", EdmType::STRING, "In progress"); //Added Status.

try    {
    $tableClient->updateEntity("mytable", $entity);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="delete-an-entity"></a>Excluir uma entidade

Para excluir uma entidade, passe o nome da tabela e a `PartitionKey` e `RowKey` da entidade para o método **TableRestProxy->deleteEntity**.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

try    {
    // Delete entity.
    $tableClient->deleteEntity("mytable", "tasksSeattle", "2");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

Para verificações de simultaneidade, você pode definir o Etag para uma entidade a ser excluída, usando o método **DeleteEntityOptions->setEtag** e passando o objeto **DeleteEntityOptions** para **deleteEntity** como um quarto parâmetro.

## <a name="batch-table-operations"></a>Operações de tabela em lote

O método **TableRestProxy->lote** permite que você execute várias operações em uma única solicitação. O padrão aqui envolve a adição de operações para o objeto **BatchRequest** e, em seguida, passa o objeto **BatchRequest** para o método **TableRestProxy->lote**. Para adicionar uma operação a um objeto **BatchRequest** , você pode chamar várias vezes para qualquer um dos seguintes métodos:

* **addInsertEntity** (adiciona uma operação insertEntity)
* **addUpdateEntity** (adiciona uma operação updateEntity)
* **addMergeEntity** (adiciona uma operação mergeEntity)
* **addInsertOrReplaceEntity** (adiciona uma operação insertOrReplaceEntity)
* **addInsertOrMergeEntity** (adiciona uma operação insertOrMergeEntity)
* **addDeleteEntity** (adiciona uma operação deleteEntity)

O exemplo a seguir mostra como executar as operações **insertEntity** e **deleteEntity** em uma única solicitação. 

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;
use MicrosoftAzure\Storage\Table\Models\BatchOperations;

// Configure a connection string for Storage Table service.
$connectionString = "DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]"

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

// Create list of batch operation.
$operations = new BatchOperations();

$entity1 = new Entity();
$entity1->setPartitionKey("tasksSeattle");
$entity1->setRowKey("2");
$entity1->addProperty("Description", null, "Clean roof gutters.");
$entity1->addProperty("DueDate",
                        EdmType::DATETIME,
                        new DateTime("2012-11-05T08:15:00-08:00"));
$entity1->addProperty("Location", EdmType::STRING, "Home");

// Add operation to list of batch operations.
$operations->addInsertEntity("mytable", $entity1);

// Add operation to list of batch operations.
$operations->addDeleteEntity("mytable", "tasksSeattle", "1");

try    {
    $tableClient->batch($operations);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

Para obter mais informações sobre operações de Tabela de envio em lote, confira [Executando transações do grupo de entidade][entity-group-transactions].

## <a name="delete-a-table"></a>Excluir uma tabela

Finalmente, para excluir uma tabela, passe o nome da tabela para o método **TableRestProxy->deleteTable**.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

try    {
    // Delete table.
    $tableClient->deleteTable("mytable");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu os conceitos básicos do serviço Tabela do Azure e Azure Cosmos DB, siga estes links para obter mais informações.

* [O Gerenciador de Armazenamento do Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) é um aplicativo autônomo e gratuito da Microsoft que possibilita o trabalho visual com os dados do Armazenamento do Azure no Windows, MacOS e Linux.

* [Central de Desenvolvimento do PHP](https://azure.microsoft.com/develop/php/).

[download]: https://packagist.org/packages/microsoft/azure-storage-table
[require_once]: https://php.net/require_once
[table-service-timeouts]: /rest/api/storageservices/setting-timeouts-for-table-service-operations

[table-data-model]: /rest/api/storageservices/Understanding-the-Table-Service-Data-Model
[filters]: /rest/api/storageservices/Querying-Tables-and-Entities
[entity-group-transactions]: /rest/api/storageservices/Performing-Entity-Group-Transactions
