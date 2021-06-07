---
title: Usar o Armazenamento de Tabelas do Azure e a API de Tabela do Azure Cosmos DB com C++
description: Armazene dados estruturados na nuvem usando o Armazenamento de Tabelas do Azure ou a API de Tabela do Azure Cosmos DB usando C++.
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: cpp
ms.topic: sample
ms.date: 10/07/2019
author: sakash279
ms.author: akshanka
ms.openlocfilehash: 79b58b76954cf15289e85dbf763b7a399897635d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94489872"
---
# <a name="how-to-use-azure-table-storage-and-azure-cosmos-db-table-api-with-c"></a>Como usar o Armazenamento de Tabelas do Azure e a API da tabela do Azure Cosmos DB com C++
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

Este guia mostra como executar cenários comuns usando o serviço de Armazenamento de Tabela do Azure ou API de Tabela do Azure Cosmos DB. Os exemplos são escritos em C++ e usam a [Biblioteca do Cliente de Armazenamento do Azure para C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md). Este artigo aborda os seguintes cenários:

* Criar e excluir uma tabela
* Trabalhar com entidades de tabela

> [!NOTE]
> Este guia tem como alvo a Biblioteca do Cliente de Armazenamento do Azure para C++, versão 1.0.0 e superior. A versão recomendada é a Biblioteca de Clientes de Armazenamento 2.2.0, que está disponível usando [NuGet](https://www.nuget.org/packages/wastorage) ou [GitHub](https://github.com/Azure/azure-storage-cpp/).
>

## <a name="create-accounts"></a>Criar contas

### <a name="create-an-azure-service-account"></a>Criar uma conta de serviço do Azure

[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

### <a name="create-an-azure-storage-account"></a>Criar uma conta de armazenamento do Azure

[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

### <a name="create-an-azure-cosmos-db-table-api-account"></a>Criar uma conta de API de Tabele do Azure Cosmos DB

[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="create-a-c-application"></a>Criar um aplicativo em C++

Neste guia, você usa os recursos de armazenamento em um aplicativo do C++. Para fazer isso, instale a Biblioteca de Clientes do Armazenamento do Azure para C++.

Para instalar a Biblioteca de Clientes do Armazenamento do Azure para C++, use os seguintes métodos:

* **Linux:** Siga as instruções fornecidas na página [LEIAME da Biblioteca de Clientes do Armazenamento do Microsoft Azure para C++: Introdução ao Linux](https://github.com/Azure/azure-storage-cpp#getting-started-on-linux).
* **Windows:** No Windows, use [vcpkg](https://github.com/microsoft/vcpkg) como o gerenciador de dependências. Siga o [início rápido](https://github.com/microsoft/vcpkg#quick-start) para inicializar o vcpkg. Em seguir, use este comando para instalar a biblioteca:

```powershell
.\vcpkg.exe install azure-storage-cpp
```

Você pode encontrar um guia de como criar o código-fonte e exportá-lo para o NuGet no arquivo [LEIAME](https://github.com/Azure/azure-storage-cpp#download--install).

### <a name="configure-access-to-the-table-client-library"></a>Configurar o acesso à biblioteca de cliente de Tabela

Para usar as APIs de armazenamento do Azure para acessar tabelas, adicione as seguintes instruções `include` à parte superior do arquivo do C++:

```cpp
#include <was/storage_account.h>
#include <was/table.h>
```

Um cliente de Armazenamento do Azure ou um cliente do Cosmos DB usa uma cadeia de conexão para armazenar pontos de extremidade e credenciais para acessar serviços de gerenciamento de dados. Quando você executa um aplicativo cliente, você deve fornecer a cadeia de conexão de armazenamento ou a cadeia de conexão do Azure Cosmos DB no formato apropriado.

### <a name="set-up-an-azure-storage-connection-string"></a>Configurar uma cadeia de conexão de Armazenamento do Azure

Este exemplo mostra como declarar um campo estático para armazenar a cadeia de conexão do Armazenamento do Azure:  

```cpp
// Define the Storage connection string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=<your_storage_account>;AccountKey=<your_storage_account_key>"));
```

Use o nome de sua Conta de armazenamento para `<your_storage_account>`. Para <chave_da_conta_de_armazenamento>, use a chave de acesso da Conta de armazenamento listada no [portal do Azure](https://portal.azure.com). Para obter informações sobre Contas de armazenamento e chaves de acesso, confira [Criar uma conta de armazenamento](../storage/common/storage-account-create.md).

### <a name="set-up-an-azure-cosmos-db-connection-string"></a>Configurar uma cadeia de conexão do Azure Cosmos DB

Este exemplo mostra como declarar um campo estático para armazenar a cadeia de conexão do Azure Cosmos DB:

```cpp
// Define the Azure Cosmos DB connection string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=<your_cosmos_db_account>;AccountKey=<your_cosmos_db_account_key>;TableEndpoint=<your_cosmos_db_endpoint>"));
```

Use o nome de sua conta do Azure Cosmos DB para `<your_cosmos_db_account>`. Insira sua chave primária para `<your_cosmos_db_account_key>`. Insira o ponto de extremidade listado no [portal do Azure](https://portal.azure.com) para `<your_cosmos_db_endpoint>`.

Para testar o seu aplicativo no computador local baseado em Windows, você pode usar o Emulador de Armazenamento do Azure instalado com o [SDK do Azure](https://azure.microsoft.com/downloads/). O Emulador de Armazenamento é um utilitário que simula os serviços de Tabela, Fila e Blob do Azure disponíveis no seu computador de desenvolvimento local. O exemplo a seguir mostra como declarar um campo estático para manter a cadeia de conexão em seu emulador de armazenamento local:  

```cpp
// Define the connection string with Azure Storage Emulator.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  
```

Para iniciar o Emulador de Armazenamento do Azure, na sua área de trabalho do Windows, selecione o botão **Iniciar** ou a tecla Windows. Insira e execute o *Emulador de Armazenamento do Microsoft Azure*. Para saber mais, confira [Usar o Emulador de Armazenamento do Azure para desenvolvimento e teste](../storage/common/storage-use-emulator.md).

### <a name="retrieve-your-connection-string"></a>Recuperar sua cadeia de conexão

Você pode usar a classe `cloud_storage_account` para representar as informações de sua conta de armazenamento. Para recuperar as informações de sua conta de armazenamento da cadeia de conexão de armazenamento, use o método `parse`.

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

Em seguida, obtenha uma referência a uma classe `cloud_table_client`. Esta classe permite que você obtenha objetos de referência para tabelas e entidades armazenadas no serviço de armazenamento de tabelas. O código a seguir cria um objeto `cloud_table_client` usando o objeto de conta de armazenamento que você recuperou anteriormente:  

```cpp
// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();
```

## <a name="create-and-add-entities-to-a-table"></a>Criar e adicionar entidades a uma tabela

### <a name="create-a-table"></a>Criar uma tabela

Um objeto `cloud_table_client` permite obter objetos de referência para tabelas e entidades. O código a seguir cria um objeto `cloud_table_client` e o usa para criar uma tabela.

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);  

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Retrieve a reference to a table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Create the table if it doesn't exist.
table.create_if_not_exists();  
```

### <a name="add-an-entity-to-a-table"></a>Adicionar uma entidade a uma tabela

Para adicionar uma entidade a uma tabela, crie um objeto `table_entity` e passe-o para `table_operation::insert_entity`. O código a seguir usa o primeiro nome do cliente como a chave de linha e o último nome como a chave de partição. Juntas, uma chave de partição e uma chave de linha identificam exclusivamente a entidade na tabela. As entidades com a mesma chave de partição podem ser consultadas mais rápido do que aquelas com chaves de partição diferentes. O uso de diversas chaves de partição permite uma maior escalabilidade de operação paralela. Para obter mais informações, veja [Lista de verificação de escalabilidade e desempenho do Armazenamento do Microsoft Azure](../storage/blobs/storage-performance-checklist.md).

O código a seguir cria uma instância de `table_entity` com alguns dados do cliente a serem armazenados. O próximo código chama `table_operation::insert_entity` para criar um objeto `table_operation` a fim de inserir uma entidade em uma tabela e associa a nova entidade de tabela a ele. Por fim, o código chama o método `execute` no objeto `cloud_table`. A nova `table_operation` envia uma solicitação ao serviço Tabela para inserir a nova entidade de cliente na tabela `people`.  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Retrieve a reference to a table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Create the table if it doesn't exist.
table.create_if_not_exists();

// Create a new customer entity.
azure::storage::table_entity customer1(U("Harp"), U("Walter"));

azure::storage::table_entity::properties_type& properties = customer1.properties();
properties.reserve(2);
properties[U("Email")] = azure::storage::entity_property(U("Walter@contoso.com"));

properties[U("Phone")] = azure::storage::entity_property(U("425-555-0101"));

// Create the table operation that inserts the customer entity.
azure::storage::table_operation insert_operation = azure::storage::table_operation::insert_entity(customer1);

// Execute the insert operation.
azure::storage::table_result insert_result = table.execute(insert_operation);
```

### <a name="insert-a-batch-of-entities"></a>Inserir um lote de entidades

É possível inserir um lote de entidades ao serviço Tabela em uma operação de gravação. O código a seguir cria um objeto `table_batch_operation` e adiciona três operações de inserção a ele. Cada operação de inserção é adicionada criando um novo objeto de entidade, definindo seus valores e, em seguida, chamando o método `insert` no objeto `table_batch_operation` para associar a entidade a uma nova operação de inserção. Em seguida, o código chama `cloud_table.execute` para executar a operação.  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Define a batch operation.
azure::storage::table_batch_operation batch_operation;

// Create a customer entity and add it to the table.
azure::storage::table_entity customer1(U("Smith"), U("Jeff"));

azure::storage::table_entity::properties_type& properties1 = customer1.properties();
properties1.reserve(2);
properties1[U("Email")] = azure::storage::entity_property(U("Jeff@contoso.com"));
properties1[U("Phone")] = azure::storage::entity_property(U("425-555-0104"));

// Create another customer entity and add it to the table.
azure::storage::table_entity customer2(U("Smith"), U("Ben"));

azure::storage::table_entity::properties_type& properties2 = customer2.properties();
properties2.reserve(2);
properties2[U("Email")] = azure::storage::entity_property(U("Ben@contoso.com"));
properties2[U("Phone")] = azure::storage::entity_property(U("425-555-0102"));

// Create a third customer entity to add to the table.
azure::storage::table_entity customer3(U("Smith"), U("Denise"));

azure::storage::table_entity::properties_type& properties3 = customer3.properties();
properties3.reserve(2);
properties3[U("Email")] = azure::storage::entity_property(U("Denise@contoso.com"));
properties3[U("Phone")] = azure::storage::entity_property(U("425-555-0103"));

// Add customer entities to the batch insert operation.
batch_operation.insert_or_replace_entity(customer1);
batch_operation.insert_or_replace_entity(customer2);
batch_operation.insert_or_replace_entity(customer3);

// Execute the batch operation.
std::vector<azure::storage::table_result> results = table.execute_batch(batch_operation);
```

Algumas outras observações sobre as operações em lote:

* Você pode executar até 100 operações de `insert`, `delete`, `merge`, `replace`, `insert-or-merge` e `insert-or-replace` em qualquer combinação em um único lote.  
* Uma operação em lote poderá ter uma operação de recuperação se ela for a única operação no lote.  
* Todas as entidades em uma única operação em lote devem ter a mesma chave de partição.  
* Uma operação em lote está limitada a uma carga de dados de 4 MB.  

## <a name="query-and-modify-entities"></a>Consultar e modificar entidades

### <a name="retrieve-all-entities-in-a-partition"></a>Recuperar todas as entidades em uma partição

Para consultar uma tabela para todas as entidades em uma partição, use um objeto `table_query`. O exemplo de código a seguir especifica um filtro para as entidades em que `Smith` é a chave de partição. Esse exemplo imprime os campos de cada entidade nos resultados da consulta no console.  

> [!NOTE]
> Esses métodos não têm suporte atualmente para C++ no Azure Cosmos DB.

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Construct the query operation for all customer entities where PartitionKey="Smith".
azure::storage::table_query query;

query.set_filter_string(azure::storage::table_query::generate_filter_condition(U("PartitionKey"), azure::storage::query_comparison_operator::equal, U("Smith")));

// Execute the query.
azure::storage::table_query_iterator it = table.execute_query(query);

// Print the fields for each customer.
azure::storage::table_query_iterator end_of_results;
for (; it != end_of_results; ++it)
{
    const azure::storage::table_entity::properties_type& properties = it->properties();

    std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key()
        << U(", Property1: ") << properties.at(U("Email")).string_value()
        << U(", Property2: ") << properties.at(U("Phone")).string_value() << std::endl;
}  
```

A consulta neste exemplo retorna todas as entidades que correspondem aos critérios do filtro. Se você tiver tabelas grandes e precisar baixar entidades de tabela com frequência, recomendamos que armazene seus dados nos blobs de armazenamento do Azure.

### <a name="retrieve-a-range-of-entities-in-a-partition"></a>Recuperar um intervalo de entidades em uma partição

Se não desejar consultar todas as entidades em uma partição, você poderá especificar um intervalo. Combine o filtro da chave de partição com um filtro de chave de linha. O exemplo de código a seguir usa dois filtros para obter todas as entidades na partição `Smith` em que a chave de linha (nome) começa com uma letra anterior a `E` do alfabeto e, em seguida, imprime os resultados da consulta.  

> [!NOTE]
> Esses métodos não têm suporte atualmente para C++ no Azure Cosmos DB.

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Create the table query.
azure::storage::table_query query;

query.set_filter_string(azure::storage::table_query::combine_filter_conditions(
    azure::storage::table_query::generate_filter_condition(U("PartitionKey"),
    azure::storage::query_comparison_operator::equal, U("Smith")),
    azure::storage::query_logical_operator::op_and,
    azure::storage::table_query::generate_filter_condition(U("RowKey"), azure::storage::query_comparison_operator::less_than, U("E"))));

// Execute the query.
azure::storage::table_query_iterator it = table.execute_query(query);

// Loop through the results, displaying information about the entity.
azure::storage::table_query_iterator end_of_results;
for (; it != end_of_results; ++it)
{
    const azure::storage::table_entity::properties_type& properties = it->properties();

    std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key()
        << U(", Property1: ") << properties.at(U("Email")).string_value()
        << U(", Property2: ") << properties.at(U("Phone")).string_value() << std::endl;
}  
```

### <a name="retrieve-a-single-entity"></a>Recuperar uma única entidade

Você pode escrever uma consulta para recuperar uma entidade única e específica. O código a seguir usa `table_operation::retrieve_entity` para especificar o cliente `Jeff Smith`. Esse método retorna uma única entidade, em vez de uma coleção, e o valor retornado está em `table_result`. Especificar chaves de partição e de linha em uma consulta é a maneira mais rápida de recuperar uma única entidade de serviço Table.  

```cpp
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Retrieve the entity with partition key of "Smith" and row key of "Jeff".
azure::storage::table_operation retrieve_operation = azure::storage::table_operation::retrieve_entity(U("Smith"), U("Jeff"));
azure::storage::table_result retrieve_result = table.execute(retrieve_operation);

// Output the entity.
azure::storage::table_entity entity = retrieve_result.entity();
const azure::storage::table_entity::properties_type& properties = entity.properties();

std::wcout << U("PartitionKey: ") << entity.partition_key() << U(", RowKey: ") << entity.row_key()
    << U(", Property1: ") << properties.at(U("Email")).string_value()
    << U(", Property2: ") << properties.at(U("Phone")).string_value() << std::endl;
```

### <a name="replace-an-entity"></a>Substituir uma entidade

Para substituir uma entidade, recupere-a do serviço Tabela, modifique o objeto de entidade e salve as alterações novamente no serviço Tabela. O código a seguir altera o número de telefone e o endereço de email de um cliente existente. Em vez de chamar `table_operation::insert_entity`, este código usa `table_operation::replace_entity`. Esta abordagem faz com que a entidade seja totalmente substituída no servidor, a menos que a entidade no servidor tenha sido alterada desde que foi recuperada. Se ela tiver sido alterada, a operação falhará. Esta falha impede que seu aplicativo substitua uma alteração feita entre a recuperação e a atualização por outro componente. O tratamento correto dessa falha é recuperar a entidade novamente, fazer suas alterações, se ainda estiverem válidas, e executar outra operação `table_operation::replace_entity`.  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Replace an entity.
azure::storage::table_entity entity_to_replace(U("Smith"), U("Jeff"));
azure::storage::table_entity::properties_type& properties_to_replace = entity_to_replace.properties();
properties_to_replace.reserve(2);

// Specify a new phone number.
properties_to_replace[U("Phone")] = azure::storage::entity_property(U("425-555-0106"));

// Specify a new email address.
properties_to_replace[U("Email")] = azure::storage::entity_property(U("JeffS@contoso.com"));

// Create an operation to replace the entity.
azure::storage::table_operation replace_operation = azure::storage::table_operation::replace_entity(entity_to_replace);

// Submit the operation to the Table service.
azure::storage::table_result replace_result = table.execute(replace_operation);
```

### <a name="insert-or-replace-an-entity"></a>Inserir ou substituir uma entidade

As operações `table_operation::replace_entity` falharão se a entidade tiver sido alterada depois de ter sido recuperada do servidor. Além disso, você deve recuperar a entidade do servidor primeiro para que `table_operation::replace_entity` seja bem-sucedida. Às vezes, você não sabe se a entidade existe no servidor. Os valores atuais armazenados nele são irrelevantes, porque sua atualização deve substituir todos eles. Para obter esse resultado, use uma operação `table_operation::insert_or_replace_entity`. Esta operação inserirá a entidade se ela não existir. A operação substituirá a entidade se ela existir. No código de exemplo a seguir, a entidade do cliente para `Jeff Smith` ainda é recuperada, mas é salva novamente no servidor usando `table_operation::insert_or_replace_entity`. Todas as atualizações feitas na entidade entre a operação de recuperação e de atualização serão substituídas.  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Insert or replace an entity.
azure::storage::table_entity entity_to_insert_or_replace(U("Smith"), U("Jeff"));
azure::storage::table_entity::properties_type& properties_to_insert_or_replace = entity_to_insert_or_replace.properties();

properties_to_insert_or_replace.reserve(2);

// Specify a phone number.
properties_to_insert_or_replace[U("Phone")] = azure::storage::entity_property(U("425-555-0107"));

// Specify an email address.
properties_to_insert_or_replace[U("Email")] = azure::storage::entity_property(U("Jeffsm@contoso.com"));

// Create an operation to insert or replace the entity.
azure::storage::table_operation insert_or_replace_operation = azure::storage::table_operation::insert_or_replace_entity(entity_to_insert_or_replace);

// Submit the operation to the Table service.
azure::storage::table_result insert_or_replace_result = table.execute(insert_or_replace_operation);
```

### <a name="query-a-subset-of-entity-properties"></a>consultar um subconjunto de propriedades da entidade

Uma consulta a uma tabela pode recuperar apenas algumas propriedades de uma entidade. A consulta no código a seguir usa o método `table_query::set_select_columns` para retornar apenas os endereços de email das entidades na tabela.  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Define the query, and select only the Email property.
azure::storage::table_query query;
std::vector<utility::string_t> columns;

columns.push_back(U("Email"));
query.set_select_columns(columns);

// Execute the query.
azure::storage::table_query_iterator it = table.execute_query(query);

// Display the results.
azure::storage::table_query_iterator end_of_results;
for (; it != end_of_results; ++it)
{
    std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key();

    const azure::storage::table_entity::properties_type& properties = it->properties();
    for (auto prop_it = properties.begin(); prop_it != properties.end(); ++prop_it)
    {
        std::wcout << ", " << prop_it->first << ": " << prop_it->second.str();
    }

    std::wcout << std::endl;
}
```

> [!NOTE]
> Consultar algumas propriedades de uma entidade é uma operação mais eficiente do que recuperar todas as propriedades.
>

## <a name="delete-content"></a>Excluir conteúdo

### <a name="delete-an-entity"></a>Excluir uma entidade

Você poderá excluir uma entidade facilmente depois de recuperá-la. Após recuperar uma entidade, chame `table_operation::delete_entity` com a entidade a ser excluída. Em seguida, chame o método `cloud_table.execute`. O código a seguir recupera e exclui uma entidade com uma chave de partição de `Smith` e uma chave de linha de `Jeff`.

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Create an operation to retrieve the entity with partition key of "Smith" and row key of "Jeff".
azure::storage::table_operation retrieve_operation = azure::storage::table_operation::retrieve_entity(U("Smith"), U("Jeff"));
azure::storage::table_result retrieve_result = table.execute(retrieve_operation);

// Create an operation to delete the entity.
azure::storage::table_operation delete_operation = azure::storage::table_operation::delete_entity(retrieve_result.entity());

// Submit the delete operation to the Table service.
azure::storage::table_result delete_result = table.execute(delete_operation);  
```

### <a name="delete-a-table"></a>Excluir uma tabela

Finalmente, o exemplo de código a seguir exclui uma tabela de uma conta de armazenamento. Uma tabela excluída não estará disponível para ser recriada durante algum tempo após a exclusão.  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Delete the table if it exists
if (table.delete_table_if_exists())
{
    std::cout << "Table deleted!";
}
else
{
    std::cout << "Table didn't exist";
}
```

## <a name="troubleshooting"></a>Solução de problemas

Para o Visual Studio Community Edition, se seu projeto receber erros de build por causa dos arquivos de inclusão *storage_account.h* e *table.h*, remova o comutador do compilador **/permissive-** :

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no seu projeto e selecione **Propriedades**.
1. Na caixa de diálogo **Páginas de propriedade**, expanda **Propriedades de configuração**, expanda **C/C++** e selecione **Linguagem**.
1. Defina o **Modo de conformidade** para **Não**.

## <a name="next-steps"></a>Próximas etapas

[O Gerenciador de Armazenamento do Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) é um aplicativo autônomo e gratuito da Microsoft que possibilita o trabalho visual com os dados do Armazenamento do Azure no Windows, MacOS e Linux.

Siga estes links para saber mais sobre o Armazenamento do Azure e a API de tabela no Azure Cosmos DB:

* [Introdução à Tabela de API](table-introduction.md)
* [Listar recursos do Armazenamento do Azure no C++](../storage/common/storage-c-plus-plus-enumeration.md)
* [Referência da Biblioteca de Cliente de Armazenamento para C++](https://azure.github.io/azure-storage-cpp)
* [Documentação do Armazenamento do Azure](https://azure.microsoft.com/documentation/services/storage/)
