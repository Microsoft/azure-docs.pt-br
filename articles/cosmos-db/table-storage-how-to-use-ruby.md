---
title: Como usar a API de Tabela do Azure Cosmos DB e o Armazenamento de Tabelas do Azure com o Ruby
description: Armazene dados estruturados na nuvem usando o Armazenamento de Tabelas do Azure ou a API de Tabelas do Azure Cosmos DB.
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: ruby
ms.topic: sample
ms.date: 07/23/2020
author: sakash279
ms.author: akshanka
ms.reviewer: sngun
ms.openlocfilehash: 2d0c8433fff58854cb77a4e806058eae1937e71b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93101112"
---
# <a name="how-to-use-azure-table-storage-and-the-azure-cosmos-db-table-api-with-ruby"></a>Como usar o Armazenamento de Tabelas do Azure e a API da tabela do Azure Cosmos DB com Ruby
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

Este artigo mostra como criar tabelas, armazenar dados e realizar operações CRUD nos dados. Escolha o serviço Tabela do Azure ou a API de Tabela do Azure Cosmos DB. Os exemplos descritos neste artigo são escritos em Ruby e usam a [Biblioteca de clientes da Tabela do Armazenamento do Microsoft Azure para Ruby](https://github.com/azure/azure-storage-ruby/tree/master/table). Os cenários abordados incluem criar uma tabela, excluir uma tabela, inserir entidades e consultar entidades da tabela.

## <a name="create-an-azure-service-account"></a>Criar uma conta de serviço do Azure

[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

**Crie uma conta de armazenamento do Azure**

[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

**Criar uma conta do BD Cosmos do Azure**

[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="add-access-to-azure-storage-or-azure-cosmos-db"></a>Adicionar acesso ao Armazenamento do Azure ou ao Azure Cosmos DB

Para usar o Armazenamento do Microsoft Azure ou Azure Cosmos DB, você deverá baixar e usar o pacote do Azure do Ruby, que inclui um conjunto de bibliotecas convenientes que se comunicam com os serviços REST da tabela.

### <a name="use-rubygems-to-obtain-the-package"></a>Usar RubyGems para obter o pacote

1. Use uma interface de linha de comando como **PowerShell** (Windows), **Terminal** (Mac) ou **Bash** (Unix).
2. Digite **gem install azure-storage-table** na janela de comando para instalar a gem e as dependências.

### <a name="import-the-package"></a>Importar o pacote

Use seu editor de texto favorito e adicione o seguinte na parte superior do arquivo do Ruby no qual você pretende usar o Armazenamento:

```ruby
require "azure/storage/table"
```

## <a name="add-your-connection-string"></a>Adicionar sua cadeia de conexão

Você pode se conectar à conta de armazenamento do Azure ou à conta da API de Tabela do Azure Cosmos DB. Obtenha a cadeia de conexão com base no tipo de conta que você está usando.

### <a name="add-an-azure-storage-connection"></a>Adicionar uma conexão do Armazenamento do Azure

O módulo do Armazenamento do Microsoft Azure lê as variáveis de ambiente **AZURE_STORAGE_ACCOUNT** e **AZURE_STORAGE_ACCESS_KEY** para obter as informações necessárias para se conectar à sua conta do Armazenamento do Azure. Se essas variáveis de ambiente não estiverem definidas, você deverá especificar as informações da conta antes de usar **Azure::Storage::Table::TableService** com o seguinte código:

```ruby
Azure.config.storage_account_name = "<your Azure Storage account>"
Azure.config.storage_access_key = "<your Azure Storage access key>"
```

Para obter esses valores de uma conta de armazenamento clássico ou do Resource Manager no Portal do Azure:

1. Faça logon no [Portal do Azure](https://portal.azure.com).
2. Navegue até a conta de armazenamento que você deseja usar.
3. Na folha Configurações no lado direito, clique em **Chaves de Acesso**.
4. Na folha Acessar chaves exibida, você verá as teclas de acesso 1 e 2. Você pode usar qualquer uma das duas.
5. Clique no ícone de cópia para copiar a chave para a área de transferência.

### <a name="add-an-azure-cosmos-db-connection"></a>Adicionar uma conexão do Azure Cosmos DB

Para se conectar ao Azure Cosmos DB, copie a cadeia de conexão principal do Portal do Azure e crie um objeto **Client** usando a cadeia de conexão copiada. Você pode passar o objeto **Client** quando criar um objeto **TableService**:

```ruby
common_client = Azure::Storage::Common::Client.create(storage_account_name:'myaccount', storage_access_key:'mykey', storage_table_host:'mycosmosdb_endpoint')
table_client = Azure::Storage::Table::TableService.new(client: common_client)
```

## <a name="create-a-table"></a>Criar uma tabela

O objeto **Azure::Storage::Table::TableService** permite trabalhar com tabelas e entidades. Para criar uma tabela, use o método **create_table()** . O exemplo a seguir cria uma tabela ou imprime o erro, se houver.

```ruby
azure_table_service = Azure::Storage::Table::TableService.new
begin
    azure_table_service.create_table("testtable")
rescue
    puts $!
end
```

## <a name="add-an-entity-to-a-table"></a>Adicionar uma entidade a uma tabela

Para adicionar uma entidade, primeiro crie um objeto hash que defina as propriedades da entidade. É importante lembrar que, para cada entidade, você deve especificar um **PartitionKey** e um **RowKey**. Estes são os identificadores exclusivos das entidades e são os valores que podem ser consultados muito mais rápido que as outras propriedades. O Armazenamento do Azure usa **PartitionKey** para distribuir automaticamente as entidades da tabela entre vários nós de armazenamento. As entidades com o mesmo **PartitionKey** são armazenadas no mesmo nó. O **RowKey** é o ID exclusivo da entidade na partição à qual pertence.

```ruby
entity = { "content" => "test entity",
    :PartitionKey => "test-partition-key", :RowKey => "1" }
azure_table_service.insert_entity("testtable", entity)
```

## <a name="update-an-entity"></a>Atualizar uma entidade

Há vários métodos disponíveis para atualizar uma entidade existente:

* **update_entity():** Atualiza uma entidade existente substituindo-a.
* **merge_entity():** Atualiza uma entidade existente mesclando novos valores de propriedade com a entidade existente.
* **insert_or_merge_entity():** Atualiza uma entidade existente substituindo-a. Se não existir nenhuma entidade, uma nova será inserida:
* **insert_or_replace_entity():** Atualiza uma entidade existente mesclando novos valores de propriedade com a entidade existente. Se nenhuma entidade existir, uma nova será inserida.

O exemplo a seguir demonstra a atualização de uma entidade usando **update_entity()** :

```ruby
entity = { "content" => "test entity with updated content",
    :PartitionKey => "test-partition-key", :RowKey => "1" }
azure_table_service.update_entity("testtable", entity)
```

Com **update_entity()** e **merge_entity()** , se a entidade que estiver sendo atualizada não existir, a operação de atualização falhará. Portanto, se desejar armazenar uma entidade independentemente de sua existência, você deverá usar **insert_or_replace_entity()** ou **insert_or_merge_entity()** .

## <a name="work-with-groups-of-entities"></a>Trabalhar com grupos de entidades

Às vezes, convém enviar várias operações juntas em um lote para garantir o processamento atômico pelo servidor. Para fazer isso, você cria primeiro um objeto **Batch** e depois usa o método **execute_batch()** em **TableService**. O seguinte exemplo demonstra o envio de duas entidades com RowKey 2 e 3 em um lote. Observe que isso funciona apenas em entidades com o mesmo PartitionKey.

```ruby
azure_table_service = Azure::TableService.new
batch = Azure::Storage::Table::Batch.new("testtable",
    "test-partition-key") do
    insert "2", { "content" => "new content 2" }
    insert "3", { "content" => "new content 3" }
end
results = azure_table_service.execute_batch(batch)
```

## <a name="query-for-an-entity"></a>Consultar uma entidade

Para consultar uma entidade em uma tabela, use o método **get_entity()** , transmitindo o nome da tabela, **PartitionKey** e **RowKey**.

```ruby
result = azure_table_service.get_entity("testtable", "test-partition-key",
    "1")
```

## <a name="query-a-set-of-entities"></a>Consultar um conjunto de entidades

Para consultar um conjunto de entidades em uma tabela, crie um objeto de hash de consulta e use o método **query_entities()** . O exemplo a seguir demonstra como obter todas as entidades com o mesmo **PartitionKey**:

```ruby
query = { :filter => "PartitionKey eq 'test-partition-key'" }
result, token = azure_table_service.query_entities("testtable", query)
```

> [!NOTE]
> Se o conjunto de resultados for muito grande para ser retornado por uma única consulta, um token de continuação será retornado para que você possa usar para recuperar páginas subsequentes.


## <a name="query-a-subset-of-entity-properties"></a>consultar um subconjunto de propriedades da entidade

Uma consulta a uma tabela pode recuperar apenas algumas propriedades de uma entidade. Essa técnica, chamada "projeção", reduz a largura de banda e pode melhorar o desempenho da consulta, principalmente para grandes entidades. Use a cláusula select e transmita os nomes das propriedades que você gostaria de trazer para o cliente.

```ruby
query = { :filter => "PartitionKey eq 'test-partition-key'",
    :select => ["content"] }
result, token = azure_table_service.query_entities("testtable", query)
```

## <a name="delete-an-entity"></a>Excluir uma entidade

Para excluir uma entidade, use o método **delete_entity()** . Transmita o nome da tabela que contém a entidade, a PartitionKey e a RowKey da entidade.

```ruby
azure_table_service.delete_entity("testtable", "test-partition-key", "1")
```

## <a name="delete-a-table"></a>Excluir uma tabela

Para excluir uma tabela, use o método **delete_table()** e passe o nome da tabela que você quer excluir.

```ruby
azure_table_service.delete_table("testtable")
```

## <a name="next-steps"></a>Próximas etapas

* [O Gerenciador de Armazenamento do Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) é um aplicativo autônomo e gratuito da Microsoft que possibilita o trabalho visual com os dados do Armazenamento do Azure no Windows, MacOS e Linux.
* [Central de Desenvolvedores do Ruby](https://azure.microsoft.com/develop/ruby/)
* [Biblioteca de cliente de tabela de Armazenamento do Microsoft Azure para Ruby](https://github.com/azure/azure-storage-ruby/tree/master/table)