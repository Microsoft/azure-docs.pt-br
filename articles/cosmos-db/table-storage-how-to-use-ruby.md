---
title: Como usar o Armazenamento de Tabelas do Azure por meio do Ruby | Microsoft Docs
description: "Armazene dados estruturados na nuvem usando o Armazenamento de Tabelas do Azure, um repositório de dados NoSQL."
services: cosmos-db
documentationcenter: ruby
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 047cd9ff-17d3-4c15-9284-1b5cc61a3224
ms.service: cosmos-db
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 12/08/2016
ms.author: mimig
ms.openlocfilehash: 372bc89f75ad4730f0defbf9d6f9f041ae5ce1bf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-azure-table-storage-from-ruby"></a>Como usar o Armazenamento de Tabela do Azure por meio do Ruby
[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-cosmos-db-langsoon-tip-include](../../includes/storage-table-cosmos-db-langsoon-tip-include.md)]

## <a name="overview"></a>Visão geral
Este guia mostra como executar cenários comuns usando o serviço Tabela do Azure. Os exemplos de código são gravados com a API do Ruby. Os cenários abrangidos incluem **criar e excluir uma tabela, inserindo e consultando entidades em uma tabela**.

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-ruby-application"></a>Criar um aplicativo Ruby
Para obter instruções sobre como criar um aplicativo Ruby, confira [Aplicativo Web Ruby on Rails em uma VM do Azure](../virtual-machines/linux/classic/virtual-machines-linux-classic-ruby-rails-web-app.md).

## <a name="configure-your-application-to-access-storage"></a>Configurar seu aplicativo para acessar o Armazenamento
Para usar o Armazenamento do Azure, você precisará baixar e usar o pacote do Azure para o Ruby, que inclui um conjunto de bibliotecas de conveniência que se comunicam com os serviços REST do Armazenamento.

### <a name="use-rubygems-to-obtain-the-package"></a>Usar RubyGems para obter o pacote
1. Use uma interface de linha de comando como **PowerShell** (Windows), **Terminal** (Mac) ou **Bash** (Unix).
2. Digite **gem install azure** na janela de comandos para instalar a gema e as dependências.

### <a name="import-the-package"></a>Importar o pacote
Use seu editor de texto favorito e adicione o seguinte na parte superior do arquivo do Ruby no qual você pretende usar o Armazenamento:

```ruby
require "azure"
```

## <a name="set-up-an-azure-storage-connection"></a>Configurar uma conexão do Armazenamento do Azure
O módulo do Azure lerá as variáveis de ambiente **AZURE\_STORAGE\_ACCOUNT** e **AZURE\_STORAGE\_ACCESS\_KEY** para obter as informações necessárias para se conectar à sua conta de Armazenamento do Azure. Se essas variáveis de ambiente não estiverem definidas, você deverá especificar as informações da conta antes de usar **Azure::TableService** com o seguinte código:

```ruby
Azure.config.storage_account_name = "<your azure storage account>"
Azure.config.storage_access_key = "<your azure storage access key>"
```

Para obter esses valores de uma conta de armazenamento clássico ou do Resource Manager no Portal do Azure:

1. Faça logon no [Portal do Azure](https://portal.azure.com).
2. Navegue até a conta de armazenamento que você deseja usar.
3. Na folha Configurações no lado direito, clique em **Chaves de Acesso**.
4. Na folha Acessar chaves exibida, você verá as teclas de acesso 1 e 2. Você pode usar qualquer uma das duas.
5. Clique no ícone de cópia para copiar a chave para a área de transferência.

## <a name="create-a-table"></a>Criar uma tabela
O objeto **Azure::TableService** permite trabalhar com tabelas e entidades. Para criar uma tabela, use o método **create\_table()**. O exemplo a seguir cria uma tabela ou imprime o erro, se houver.

```ruby
azure_table_service = Azure::TableService.new
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

* **update\_entity():** atualiza uma entidade existente substituindo-a.
* **merge\_entity():** atualiza uma entidade existente mesclando novos valores de propriedade à entidade existente.
* **insert\_or\_merge\_entity():** atualiza uma entidade existente substituindo-a. Se não existir nenhuma entidade, uma nova será inserida:
* **insert\_or\_replace\_entity():** atualiza uma entidade existente mesclando novos valores de propriedade à entidade existente. Se nenhuma entidade existir, uma nova será inserida.

O exemplo a seguir demonstra a atualização de uma entidade usando **update\_entity()**:

```ruby
entity = { "content" => "test entity with updated content",
    :PartitionKey => "test-partition-key", :RowKey => "1" }
azure_table_service.update_entity("testtable", entity)
```

Com **update\_entity()** e **merge\_entity()**, se a entidade que estiver sendo atualizada não existir, a operação de atualização falhará. Portanto, se desejar armazenar uma entidade independentemente de sua existência, você deverá usar **insert\_or\_replace\_entity()** ou **insert\_or\_merge\_entity()**.

## <a name="work-with-groups-of-entities"></a>Trabalhar com grupos de entidades
Às vezes, convém enviar várias operações juntas em um lote para garantir o processamento atômico pelo servidor. Para fazer isso, você cria primeiro um objeto **Batch** e depois usa o método **execute\_batch()** em **TableService**. O seguinte exemplo demonstra o envio de duas entidades com RowKey 2 e 3 em um lote. Observe que isso funciona apenas em entidades com o mesmo PartitionKey.

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
Para consultar uma entidade em uma tabela, use o método **get\_entity()**, transmitindo o nome da tabela, **PartitionKey** e **RowKey**.

```ruby
result = azure_table_service.get_entity("testtable", "test-partition-key",
    "1")
```

## <a name="query-a-set-of-entities"></a>Consultar um conjunto de entidades
Para consultar um conjunto de entidades em uma tabela, crie um objeto de hash de consulta e use o método **query\_entities()**. O exemplo a seguir demonstra como obter todas as entidades com o mesmo **PartitionKey**:

```ruby
query = { :filter => "PartitionKey eq 'test-partition-key'" }
result, token = azure_table_service.query_entities("testtable", query)
```

> [!NOTE]
> Se o conjunto de resultados for muito grande para ser retornado por uma única consulta, um token de continuação será retornado para que você possa usar para recuperar páginas subsequentes.
>
>

## <a name="query-a-subset-of-entity-properties"></a>consultar um subconjunto de propriedades da entidade
Uma consulta a uma tabela pode recuperar apenas algumas propriedades de uma entidade. Essa técnica, chamada "projeção", reduz a largura de banda e pode melhorar o desempenho da consulta, principalmente em grandes entidades. Use a cláusula select e transmita os nomes das propriedades que você gostaria de trazer para o cliente.

```ruby
query = { :filter => "PartitionKey eq 'test-partition-key'",
    :select => ["content"] }
result, token = azure_table_service.query_entities("testtable", query)
```

## <a name="delete-an-entity"></a>Excluir uma entidade
Para excluir uma entidade, use o método **delete\_entity()**. Você precisa transmitir o nome da tabela que contém a entidade, a PartitionKey e a RowKey da entidade.

```ruby
azure_table_service.delete_entity("testtable", "test-partition-key", "1")
```

## <a name="delete-a-table"></a>Excluir uma tabela
Para excluir uma tabela, use o método **delete\_table()** e passe o nome da tabela que você quer excluir.

```ruby
azure_table_service.delete_table("testtable")
```

## <a name="next-steps"></a>Próximas etapas

* [O Gerenciador de Armazenamento do Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) é um aplicativo autônomo e gratuito da Microsoft que possibilita o trabalho visual com os dados do Armazenamento do Azure no Windows, MacOS e Linux.
* [SDK do Azure para repositório Ruby](http://github.com/WindowsAzure/azure-sdk-for-ruby) no GitHub

