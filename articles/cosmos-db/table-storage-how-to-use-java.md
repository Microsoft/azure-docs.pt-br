---
title: Como usar o Armazenamento de Tabelas do Java | Microsoft Docs
description: "Armazene dados estruturados na nuvem usando o Armazenamento de Tabelas do Azure, um repositório de dados NoSQL."
services: cosmos-db
documentationcenter: java
author: mimig1
manager: jhubbard
editor: tysonn
ms.assetid: 45145189-e67f-4ca6-b15d-43af7bfd3f97
ms.service: cosmos-db
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 12/08/2016
ms.author: mimig
ms.openlocfilehash: 7f92b1e14a514e9eda39f7ca94f63fc761dfdf41
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-table-storage-from-java"></a>Como usar o Armazenamento de Tabela do Java
[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-cosmos-db-langsoon-tip-include](../../includes/storage-table-cosmos-db-langsoon-tip-include.md)]

## <a name="overview"></a>Visão geral
Este guia mostra como executar cenários comuns usando o serviço de Armazenamento de Tabela do Azure. As amostras são escritas em Java e usam o [SDK de Armazenamento do Azure para Java][Azure Storage SDK for Java]. Os cenários abrangidos incluem **criar**, **listar** e **excluir** tabelas, além de **inserir**, **consultar**, **modificar** e **excluir** entidades em uma tabela. Para obter mais informações sobre tabelas, consulte a seção [Próximas etapas](#Next-Steps) .

Observação: um SDK está disponível para os desenvolvedores que usam o Armazenamento do Azure em dispositivos Android. Para obter mais informações, consulte [Azure Storage SDK for Android][Azure Storage SDK for Android] (SDK de Armazenamento do Azure para Android).

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-java-application"></a>Criar um aplicativo Java
Neste guia, você usará os recursos de armazenamento que podem ser executados em um aplicativo Java localmente ou no código em execução em uma função web ou de trabalho do Azure.

Para isso, você precisará instalar o JDK (Java Development Kit) e criar uma conta de armazenamento do Azure na sua assinatura do Azure. Depois disso, você precisará verificar se o sistema de desenvolvimento atende aos requisitos mínimos e às dependências listadas no repositório [Microsoft Azure Storage SDK for Java][Azure Storage SDK for Java] (SDK de Armazenamento do Microsoft Azure para Java) no GitHub. Se o seu sistema atender a esses requisitos, você poderá seguir as instruções para baixar e instalar as Bibliotecas de Armazenamento do Azure para Java em seu sistema por meio desse repositório. Depois de concluir essas tarefas, você poderá criar um aplicativo Java que usa os exemplos neste artigo.

## <a name="configure-your-application-to-access-table-storage"></a>Configurar seu aplicativo para acessar o armazenamento de tabelas
Adicione as seguintes instruções de importação na parte superior do arquivo Java em que deseja usar as APIs de armazenamento do Microsoft Azure para acessar tabelas:

```java
// Include the following imports to use table APIs
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.table.*;
import com.microsoft.azure.storage.table.TableQuery.*;
```

## <a name="set-up-an-azure-storage-connection-string"></a>Configurar uma cadeia de conexão de armazenamento do Azure
Um cliente de armazenamento do Azure usa uma cadeia de conexão para armazenar pontos de extremidade e credenciais para acessar serviços de gerenciamento de dados. Ao executar um aplicativo cliente, é necessário fornecer a cadeia de conexão de armazenamento no formato a seguir, usando o nome de sua conta de armazenamento e a tecla de Acesso primário da conta de armazenamento listada no [portal do Azure](https://portal.azure.com) para os valores *AccountName* e *AccountKey*. Este exemplo mostra como você pode declarar um campo estático para armazenar a cadeia de conexão:

```java
// Define the connection-string with your values.
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key";
```

Em um aplicativo que esteja sendo executado em uma função no Microsoft Azure, essa cadeia pode ser armazenada no arquivo de configuração do serviço, *ServiceConfiguration.cscfg*, podendo ser acessada com uma chamada para o método **RoleEnvironment.getConfigurationSettings** . Segue um exemplo de como obter a cadeia de conexão de um elemento **Setting** denominado *StorageConnectionString* no arquivo de configuração de serviço:

```java
// Retrieve storage account from connection-string.
String storageConnectionString =
    RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");
```

Os exemplos abaixo pressupõem que você usou um desses dois métodos para obter a cadeia de conexão do armazenamento.

## <a name="how-to-create-a-table"></a>Como: criar uma tabela
Um objeto **CloudTableClient** permite obter os objetos de referência para tabelas e entidades. O código a seguir cria um objeto **CloudTableClient** e o usa para criar um novo objeto **CloudTable**, o qual representa uma tabela nomeada "pessoas". (Observação: existem outras maneiras de criar objetos **CloudStorageAccount**. Para saber mais, veja **CloudStorageAccount** na [Referência de SDK do cliente de armazenamento do Azure]).

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create the table if it doesn't exist.
    String tableName = "people";
    CloudTable cloudTable = tableClient.getTableReference(tableName);
    cloudTable.createIfNotExists();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-list-the-tables"></a>Como: listar as tabelas
Para obter uma lista de tabelas, chame o método **CloudTableClient.listTables()** para recuperar uma lista iterável de nomes de tabela.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Loop through the collection of table names.
    for (String table : tableClient.listTables())
    {
        // Output each table name.
        System.out.println(table);
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-add-an-entity-to-a-table"></a>Como: adicionar uma entidade a uma tabela
As entidades mapeiam para os objetos do Java que usam uma classe personalizada, implementando **TableEntity**. Para sua conveniência, a classe **TableServiceEntity** implementa **TableEntity** e usa reflexão para mapear as propriedades para os métodos getter e setter nomeados para as propriedades. Para adicionar uma entidade a uma tabela, primeiro crie uma classe que defina as propriedades da sua entidade. O código a seguir define uma classe de entidade que usa o nome do cliente como a chave de linha e o sobrenome como a chave de partição. Juntas, uma chave de partição e uma chave de linha identificam exclusivamente a entidade na tabela. As entidades com a mesma chave de partição podem ser consultadas mais rápido do que aquelas com chaves de partição diferentes.

```java
public class CustomerEntity extends TableServiceEntity {
    public CustomerEntity(String lastName, String firstName) {
        this.partitionKey = lastName;
        this.rowKey = firstName;
    }

    public CustomerEntity() { }

    String email;
    String phoneNumber;

    public String getEmail() {
        return this.email;
    }

    public void setEmail(String email) {
        this.email = email;
    }

    public String getPhoneNumber() {
        return this.phoneNumber;
    }

    public void setPhoneNumber(String phoneNumber) {
        this.phoneNumber = phoneNumber;
    }
}
```

As operações de tabela que envolvem entidades exigem umobjeto **TableOperation** . Este objeto define a operação a ser realizada em uma entidade, que pode ser executada com um objeto **CloudTable** . O código a seguir cria uma nova instância da classe **CustomerEntity** com alguns dados do cliente que serão armazenados. O próximo código chama **TableOperation.insertOrReplace** para criar um objeto **TableOperation** para inserir uma entidade em uma tabela e associa a nova **CustomerEntity** a ele. Por fim, o código chama o método **execute** no objeto **CloudTable**, especificando a tabela “pessoas" e a nova **TableOperation**, que envia uma solicitação para o serviço de armazenamento para inserir a nova entidade de cliente na tabela “pessoas” ou substituir a entidade se ela já existir.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Create a new customer entity.
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.setEmail("Walter@contoso.com");
    customer1.setPhoneNumber("425-555-0101");

    // Create an operation to add the new customer to the people table.
    TableOperation insertCustomer1 = TableOperation.insertOrReplace(customer1);

    // Submit the operation to the table service.
    cloudTable.execute(insertCustomer1);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-insert-a-batch-of-entities"></a>Como: inserir um lote de entidades
Você pode inserir um lote de entidades ao serviço Tabela em uma operação de gravação. O código a seguir cria um objeto **TableBatchOperation** e adiciona três operações de inserção a ele. Cada operação de inserção é adicionada criando um novo objeto de entidade, definindo seus valores e, em seguida, chamando o método **insert** no objeto **TableBatchOperation** para associar a entidade a uma nova operação de inserção. Em seguida, o código chama **execute** no objeto **CloudTable**, especificando a tabela “pessoas" e o objeto **TableBatchOperation**, que envia o lote de operações da tabela para o serviço de armazenamento em uma única solicitação.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Define a batch operation.
    TableBatchOperation batchOperation = new TableBatchOperation();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Create a customer entity to add to the table.
    CustomerEntity customer = new CustomerEntity("Smith", "Jeff");
    customer.setEmail("Jeff@contoso.com");
    customer.setPhoneNumber("425-555-0104");
    batchOperation.insertOrReplace(customer);

    // Create another customer entity to add to the table.
    CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
    customer2.setEmail("Ben@contoso.com");
    customer2.setPhoneNumber("425-555-0102");
    batchOperation.insertOrReplace(customer2);

    // Create a third customer entity to add to the table.
    CustomerEntity customer3 = new CustomerEntity("Smith", "Denise");
    customer3.setEmail("Denise@contoso.com");
    customer3.setPhoneNumber("425-555-0103");
    batchOperation.insertOrReplace(customer3);

    // Execute the batch of operations on the "people" table.
    cloudTable.execute(batchOperation);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

Algumas outras observações sobre as operações em lote:

* Você pode executar até 100 operações de inserção, exclusão, mesclagem, substituição, inserção ou mesclagem e inserção ou substituição em qualquer combinação em um único lote.
* Uma operação em lote pode ter uma operação de recuperação se ela for a única operação no lote.
* Todas as entidades em uma única operação em lote devem ter a mesma chave de partição.
* Uma operação em lote está limitada a uma carga de dados de 4 MB.

## <a name="how-to-retrieve-all-entities-in-a-partition"></a>Como recuperar todas as entidades em uma partição
Para consultar uma tabela de todas as entidades em uma partição, use **TableQuery**. Chame **TableQuery.from** para criar uma consulta em uma determinada tabela que retorna um tipo de resultado especificado. O código a seguir especifica um filtro para entidades onde 'Smith' é a chave da partição. **TableQuery.generateFilterCondition** é um método auxiliar para criar filtros para consultas. Chame **where** na referência retornada pelo método **TableQuery.from** para aplicar o filtro à consulta. Quando a chamada é executada com uma chamada para **execute** no objeto **CloudTable**, ela retorna um **Iterator** com o tipo de resultado **CustomerEntity** especificado. Com isso, você pode usar o **Iterator** retornado em cada loop para consumir os resultados. Esse código imprime os campos de cada entidade nos resultados da consulta no console.

```java
try
{
    // Define constants for filters.
    final String PARTITION_KEY = "PartitionKey";
    final String ROW_KEY = "RowKey";
    final String TIMESTAMP = "Timestamp";

    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Create a filter condition where the partition key is "Smith".
    String partitionFilter = TableQuery.generateFilterCondition(
        PARTITION_KEY,
        QueryComparisons.EQUAL,
        "Smith");

    // Specify a partition query, using "Smith" as the partition key filter.
    TableQuery<CustomerEntity> partitionQuery =
        TableQuery.from(CustomerEntity.class)
        .where(partitionFilter);

    // Loop through the results, displaying information about the entity.
    for (CustomerEntity entity : cloudTable.execute(partitionQuery)) {
        System.out.println(entity.getPartitionKey() +
            " " + entity.getRowKey() +
            "\t" + entity.getEmail() +
            "\t" + entity.getPhoneNumber());
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-retrieve-a-range-of-entities-in-a-partition"></a>Como: recuperar um intervalo de entidades em uma partição
Se não desejar consultar todas as entidades em uma partição, você poderá especificar um intervalo, usando as operações de comparação em um filtro. O código a seguir combina dois filtros para obter todas as entidades na partição "Smith", onde a chave de linha (nome) começa com uma letra até a letra 'E' do alfabeto. Em seguida, eleimprime os resultados da consulta. Se você usar as entidades adicionadas à tabela na seção de inserção do lote deste guia, apenas duas entidades serão retornadas desta vez (Ben e Denise Smith); Jeff Smith não será incluído.

```java
try
{
    // Define constants for filters.
    final String PARTITION_KEY = "PartitionKey";
    final String ROW_KEY = "RowKey";
    final String TIMESTAMP = "Timestamp";

    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Create a filter condition where the partition key is "Smith".
    String partitionFilter = TableQuery.generateFilterCondition(
        PARTITION_KEY,
        QueryComparisons.EQUAL,
        "Smith");

    // Create a filter condition where the row key is less than the letter "E".
    String rowFilter = TableQuery.generateFilterCondition(
        ROW_KEY,
        QueryComparisons.LESS_THAN,
        "E");

    // Combine the two conditions into a filter expression.
    String combinedFilter = TableQuery.combineFilters(partitionFilter,
        Operators.AND, rowFilter);

    // Specify a range query, using "Smith" as the partition key,
    // with the row key being up to the letter "E".
    TableQuery<CustomerEntity> rangeQuery =
        TableQuery.from(CustomerEntity.class)
        .where(combinedFilter);

    // Loop through the results, displaying information about the entity
    for (CustomerEntity entity : cloudTable.execute(rangeQuery)) {
        System.out.println(entity.getPartitionKey() +
            " " + entity.getRowKey() +
            "\t" + entity.getEmail() +
            "\t" + entity.getPhoneNumber());
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-retrieve-a-single-entity"></a>Como: recuperar uma única entidade
Você pode escrever uma consulta para recuperar uma entidade única e específica. O código a seguir chama **TableOperation.retrieve** com uma chave de partição e parâmetros da chave de linha para especificar o cliente "Jeff Smith", em vez de criar uma **TableQuery** e usar filtros para executar a mesma tarefa. Quando executada, a operação de recuperação retorna apenas uma entidade, em vez de uma coleção. O método **getResultAsType** converte o resultado para o tipo de destino da atribuição, um objeto **CustomerEntity**. Se esse tipo não for compatível com o tipo especificado na consulta, uma exceção será lançada. Um valor nulo será retornado se nenhuma entidade tiver uma correspondência exata de chave de partição e chave de linha. Especificar chaves de partição e de linha em uma consulta é a maneira mais rápida de recuperar uma única entidade de serviço Table.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Retrieve the entity with partition key of "Smith" and row key of "Jeff"
    TableOperation retrieveSmithJeff =
        TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

    // Submit the operation to the table service and get the specific entity.
    CustomerEntity specificEntity =
        cloudTable.execute(retrieveSmithJeff).getResultAsType();

    // Output the entity.
    if (specificEntity != null)
    {
        System.out.println(specificEntity.getPartitionKey() +
            " " + specificEntity.getRowKey() +
            "\t" + specificEntity.getEmail() +
            "\t" + specificEntity.getPhoneNumber());
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-modify-an-entity"></a>Como modificar uma entidade
Para modificar uma entidade, recupere-a do serviço Tabela, altere o objeto de entidade e, em seguida, salve as alterações novamente no serviço Tabela com uma operação de substituição ou mesclagem. O código a seguir altera o número de telefone de um cliente existente. Em vez de chamar **TableOperation.insert**como fizemos para inserir, esse código chama **TableOperation.replace**. O método **CloudTableClient.execute** chamará o serviço Tabela e a entidade será atualizada, a menos que outro aplicativo a tenha alterado desde o momento em que ela foi recuperada por esse aplicativo. Quando isso acontece, uma exceção é lançada e a entidade deve ser recuperada, modificada e salva novamente. Esse padrão de repetição de simultaneidade otimista é comum em um sistema de armazenamento distribuído.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Retrieve the entity with partition key of "Smith" and row key of "Jeff".
    TableOperation retrieveSmithJeff =
        TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

    // Submit the operation to the table service and get the specific entity.
    CustomerEntity specificEntity =
        cloudTable.execute(retrieveSmithJeff).getResultAsType();

    // Specify a new phone number.
    specificEntity.setPhoneNumber("425-555-0105");

    // Create an operation to replace the entity.
    TableOperation replaceEntity = TableOperation.replace(specificEntity);

    // Submit the operation to the table service.
    cloudTable.execute(replaceEntity);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-query-a-subset-of-entity-properties"></a>Como: consultar um subconjunto de propriedades de entidade
Uma consulta a uma tabela pode recuperar apenas algumas propriedades de uma entidade. Essa técnica, chamada projeção, reduz a largura de banda e pode melhorar o desempenho da consulta, principalmente para grandes entidades. A consulta no código a seguir usa o método **select** para retornar apenas os endereços de e-mail das entidades na tabela. Os resultados são projetados em um coleção de **String** com a ajuda de um **EntityResolver**, que faz a conversão de tipo nas entidades retornadas do servidor. Saiba mais sobre projeção em [Microsoft Azure Tables: Introducing Upsert and Query Projection][Azure Tables: Introducing Upsert and Query Projection] (Tabelas do Microsoft Azure: Apresentando a projeção de upsert e de consulta). Observe que a projeção não é compatível com o emulador de armazenamento local, portanto, esse código é executado somente ao usar uma conta no serviço tabela.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Define a projection query that retrieves only the Email property
    TableQuery<CustomerEntity> projectionQuery =
        TableQuery.from(CustomerEntity.class)
        .select(new String[] {"Email"});

    // Define a Entity resolver to project the entity to the Email value.
    EntityResolver<String> emailResolver = new EntityResolver<String>() {
        @Override
        public String resolve(String PartitionKey, String RowKey, Date timeStamp, HashMap<String, EntityProperty> properties, String etag) {
            return properties.get("Email").getValueAsString();
        }
    };

    // Loop through the results, displaying the Email values.
    for (String projectedString :
        cloudTable.execute(projectionQuery, emailResolver)) {
            System.out.println(projectedString);
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-insert-or-replace-an-entity"></a>Como: inserir ou substituir uma entidade
Em geral, você deseja adicionar uma entidade a uma tabela sem saber se ela já existe na tabela. Uma operação de inserção ou substituição permite que você faça uma única solicitação para inserir a entidade, se ela não existir, ou para substituir a entidade existente, se ela existir. Com base nos exemplos anteriores, o código a seguir insere ou substitui a entidade para "Walter Harp". Depois de criar uma nova entidade, esse código chamará o método **TableOperation.insertOrReplace** . Em seguida, esse código chamará **execute** no objeto **CloudTable** com a tabela e a operação de inserção ou substituição de tabela como os parâmetros. Para atualizar somente parte de uma entidade, o método **TableOperation.insertOrMerge** pode ser usado em vez disso. Observe que inserir ou substituir não tem suporte com o emulador de armazenamento local, portanto, esse código é executado somente ao usar uma conta no serviço tabela. Saiba mais sobre inserção ou substituição e inserção ou mesclagem em [Microsoft Azure Tables: Introducing Upsert and Query Projection][Azure Tables: Introducing Upsert and Query Projection] (Tabelas do Microsoft Azure: Apresentando a projeção de upsert e de consulta).

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Create a new customer entity.
    CustomerEntity customer5 = new CustomerEntity("Harp", "Walter");
    customer5.setEmail("Walter@contoso.com");
    customer5.setPhoneNumber("425-555-0106");

    // Create an operation to add the new customer to the people table.
    TableOperation insertCustomer5 = TableOperation.insertOrReplace(customer5);

    // Submit the operation to the table service.
    cloudTable.execute(insertCustomer5);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-delete-an-entity"></a>Como excluir uma entidade
Você poderá excluir uma entidade facilmente depois que recuperá-la. Depois que a entidade for recuperada, chame **TableOperation.delete** com a entidade a excluir. Em seguida, chame **execute** no objeto **CloudTable**. O código a seguir recupera e exclui uma entidade de cliente.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Create an operation to retrieve the entity with partition key of "Smith" and row key of "Jeff".
    TableOperation retrieveSmithJeff = TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

    // Retrieve the entity with partition key of "Smith" and row key of "Jeff".
    CustomerEntity entitySmithJeff =
        cloudTable.execute(retrieveSmithJeff).getResultAsType();

    // Create an operation to delete the entity.
    TableOperation deleteSmithJeff = TableOperation.delete(entitySmithJeff);

    // Submit the delete operation to the table service.
    cloudTable.execute(deleteSmithJeff);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-delete-a-table"></a>Como excluir uma tabela
Finalmente, o código a seguir excluirá uma tabela de uma conta de armazenamento. Uma tabela que tenha sido excluída não estará disponível para ser recriada por um período de tempo após a exclusão, geralmente menos de 40 segundos.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Delete the table and all its data if it exists.
    CloudTable cloudTable = tableClient.getTableReference("people");
    cloudTable.deleteIfExists();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```
[!INCLUDE [storage-check-out-samples-java](../../includes/storage-check-out-samples-java.md)]

## <a name="next-steps"></a>Próximas etapas

* [O Gerenciador de Armazenamento do Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) é um aplicativo autônomo e gratuito da Microsoft que possibilita o trabalho visual com os dados do Armazenamento do Azure no Windows, MacOS e Linux.
* [Microsoft Azure Storage SDK for Java][Azure Storage SDK for Java] (SDK de Armazenamento do Microsoft Azure para Java)
* [Referência de SDK do cliente de armazenamento do Azure][Referência de SDK do cliente de armazenamento do Azure]
* [Azure Storage REST API Reference][Azure Storage REST API] (Referência de API REST do Armazenamento do Azure)
* [Microsoft Azure Storage Team Blog][Azure Storage Team Blog] (Blog da equipe de Armazenamento do Microsoft Azure)

Para saber mais, visite [Azure para desenvolvedores Java](/java/azure).

[Azure SDK for Java]: http://go.microsoft.com/fwlink/?LinkID=525671
[Azure Storage SDK for Java]: https://github.com/azure/azure-storage-java
[Azure Storage SDK for Android]: https://github.com/azure/azure-storage-android
[Referência de SDK do cliente de armazenamento do Azure]: http://dl.windowsazure.com/storage/javadoc/
[Azure Storage REST API]: https://msdn.microsoft.com/library/azure/dd179355.aspx
[Azure Storage Team Blog]: http://blogs.msdn.com/b/windowsazurestorage/
[Azure Tables: Introducing Upsert and Query Projection]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx
