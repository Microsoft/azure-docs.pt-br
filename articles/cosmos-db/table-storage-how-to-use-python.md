---
title: Introdução ao Armazenamento de Tabelas do Azure e à API de Tabela do Azure Cosmos DB usando Python
description: Armazene dados estruturados na nuvem usando o Armazenamento de Tabelas do Azure ou a API de Tabelas do Azure Cosmos DB.
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: python
ms.topic: sample
ms.date: 04/05/2018
author: wmengmsft
ms.author: wmeng
ms.reviewer: sngun
ms.openlocfilehash: 0f0acc721fd8888953d80976234b431943985ebf
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68356274"
---
# <a name="get-started-with-azure-table-storage-and-the-azure-cosmos-db-table-api-using-python"></a>Introdução ao Armazenamento de Tabelas do Azure e à API de Tabela do Azure Cosmos DB usando Python

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

O Armazenamento de Tabelas do Azure e o Azure Cosmos DB são serviços que armazenam dados NoSQL estruturados na nuvem, fornecendo um repositório chave/atributo com um design sem esquema. Como o armazenamento de Tabelas e o Azure Cosmos DB não possuem um esquema, é fácil adaptar seus dados à medida que as necessidades de seu aplicativo evoluem. O acesso aos dados do Armazenamento de Tabelas e da API de Tabelas é rápido e econômico para muitos tipos de aplicativos e normalmente tem um custo mais baixo que o SQL tradicional para volumes de dados semelhantes.

Você pode usar o armazenamento de Tabelas ou o Azure Cosmos DB para armazenar conjuntos de dados flexíveis, como dados de usuário para aplicativos web, catálogos de endereços, informações sobre dispositivos ou outros tipos de metadados exigidos pelo serviço. Você pode armazenar qualquer número de entidades em uma tabela e uma conta de armazenamento pode conter um número ilimitado de tabelas, até o limite de capacidade da conta de armazenamento.

### <a name="about-this-sample"></a>Sobre este exemplo
Este exemplo mostra como usar o [SDK de Tabela do Azure Cosmos DB para Python](https://pypi.python.org/pypi/azure-cosmosdb-table/) em cenários comuns do Armazenamento de Tabelas do Azure. O nome do SDK indica que ele é destinado ao uso com o Azure Cosmos DB, mas ele funciona com o Azure Cosmos DB e o Armazenamento de Tabelas do Azure; cada serviço tem apenas um ponto de extremidade exclusivo. Esses cenários são explorados usando exemplos em Python que ilustram como:
* Criar e excluir tabelas
* Inserir e consultar entidades
* Modificar entidades

Enquanto estiver trabalhando nos cenários deste exemplo, talvez você queira consultar a [Referência da API do SDK do Azure Cosmos DB para Python](https://docs.microsoft.com/python/api/overview/azure/cosmosdb?view=azure-python).

## <a name="prerequisites"></a>Pré-requisitos

Você precisará do seguinte para concluir este exemplo com sucesso:

- [Python](https://www.python.org/downloads/) 2.7, 3.3, 3.4, 3.5 ou 3.6
- [SDK de Tabela do Azure Cosmos DB para Python](https://pypi.python.org/pypi/azure-cosmosdb-table/). Esse SDK se conecta ao Armazenamento de Tabelas do Azure e à API de Tabela do Azure Cosmos DB.
- [Conta de armazenamento do Azure](../storage/common/storage-quickstart-create-account.md) ou [Conta do Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/)

## <a name="create-an-azure-service-account"></a>Criar uma conta de serviço do Azure
[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

### <a name="create-an-azure-storage-account"></a>Criar uma conta de armazenamento do Azure
[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

### <a name="create-an-azure-cosmos-db-table-api-account"></a>Criar uma conta de API de Tabele do Azure Cosmos DB
[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="install-the-azure-cosmos-db-table-sdk-for-python"></a>Instalar o SDK de Tabela do Azure Cosmos DB para Python

Depois de criar uma conta de armazenamento, a próxima etapa é instalar o [SDK de Tabela do Microsoft Azure Cosmos DB para Python](https://pypi.python.org/pypi/azure-cosmosdb-table/). Para obter detalhes sobre como instalar o SDK, consulte o arquivo [README.rst](https://github.com/Azure/azure-cosmosdb-python/blob/master/azure-cosmosdb-table/README.rst) no repositório do SDK de Tabela do Cosmos DB para Python no GitHub.

## <a name="import-the-tableservice-and-entity-classes"></a>Importar as classes Entity e TableService

Para trabalhar com entidades no serviço Tabela do Azure em Python, use as classes [TableService][py_TableService] e [Entity][py_Entity]. Adicione este código próximo à parte superior seu arquivo Python para importar ambos:

```python
from azure.cosmosdb.table.tableservice import TableService
from azure.cosmosdb.table.models import Entity
```

## <a name="connect-to-azure-table-service"></a>Conectar-se ao serviço Tabela do Azure

Para se conectar ao serviço Tabela de Armazenamento do Azure, crie um objeto [TableService][py_TableService] e passe sua chave de conta e o nome da conta de armazenamento. Substitua `myaccount` e `mykey` pelo nome e pela chave da sua conta.

```python
table_service = TableService(account_name='myaccount', account_key='mykey')
```

## <a name="connect-to-azure-cosmos-db"></a>Conectar-se ao Azure Cosmos DB

Para se conectar ao Azure Cosmos DB, copie a cadeia de conexão principal do portal do Azure e crie um objeto [TableService][py_TableService] usando a cadeia de conexão copiada:

```python
table_service = TableService(connection_string='DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;TableEndpoint=myendpoint;)
```

## <a name="create-a-table"></a>Criar uma tabela

Chame [create_table][py_create_table] para criar a tabela.

```python
table_service.create_table('tasktable')
```

## <a name="add-an-entity-to-a-table"></a>Adicionar uma entidade a uma tabela

Para adicionar uma entidade, primeiro crie um objeto que representa a entidade e, em seguida, passe o objeto para o [método TableService.insert_entity][py_TableService]. O objeto de entidade pode ser um dicionário ou um objeto do tipo [Entity][py_Entity] e define os valores e os nomes de propriedade da entidade. Cada entidade deve incluir as propriedades [PartitionKey e RowKey](#partitionkey-and-rowkey) necessárias, além de quaisquer outras propriedades que você definir para a entidade.

Este exemplo cria um objeto de dicionário que representa uma entidade e o passa para o método [insert_entity][py_insert_entity] a fim de adicionar o objeto à tabela:

```python
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '001',
        'description': 'Take out the trash', 'priority': 200}
table_service.insert_entity('tasktable', task)
```

Este exemplo cria um objeto [Entity][py_Entity] e, em seguida, passa esse objeto para o método [insert_entity][py_insert_entity] para adicioná-lo à tabela:

```python
task = Entity()
task.PartitionKey = 'tasksSeattle'
task.RowKey = '002'
task.description = 'Wash the car'
task.priority = 100
table_service.insert_entity('tasktable', task)
```

### <a name="partitionkey-and-rowkey"></a>PartitionKey e RowKey

Você deve especificar uma propriedade **PartitionKey** e uma **RowKey** para cada entidade. Esses são os identificadores exclusivos das entidades, uma vez que juntos eles formam a chave primária de uma entidade. Você pode consultar usando esses valores muito mais rápido do que pode consultar quaisquer outras propriedades de entidade porque somente essas propriedades são indexadas.

O serviço Tabela usa **PartitionKey** para distribuir de forma inteligente as entidades de tabela entre os nós de armazenamento. As entidades com o mesmo **PartitionKey** são armazenadas no mesmo nó. O **RowKey** é a ID exclusiva da entidade na partição à qual pertence.

## <a name="update-an-entity"></a>Atualizar uma entidade

Para atualizar todos os valores de propriedade de uma entidade, chame o método [update_entity][py_update_entity]. Este exemplo mostra como substituir uma entidade existente pela versão atualizada:

```python
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '001',
        'description': 'Take out the garbage', 'priority': 250}
table_service.update_entity('tasktable', task)
```

Se a entidade que está sendo atualizada ainda não existir, a operação de atualização falhará. Se você quiser armazenar uma entidade quer ela exista ou não, use [insert_or_replace_entity][py_insert_or_replace_entity]. No exemplo a seguir, a primeira chamada substituirá a entidade existente. A segunda chamada vai inserir uma nova entidade, contanto que não exista na tabela nenhuma entidade com o PartitionKey e o RowKey especificados.

```python
# Replace the entity created earlier
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '001',
        'description': 'Take out the garbage again', 'priority': 250}
table_service.insert_or_replace_entity('tasktable', task)

# Insert a new entity
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '003',
        'description': 'Buy detergent', 'priority': 300}
table_service.insert_or_replace_entity('tasktable', task)
```

> [!TIP]
> O método [update_entity][py_update_entity] substitui todas as propriedades e valores de uma entidade existente, que você também pode usar para remover propriedades de uma entidade existente. Você pode usar o método [merge_entity][py_merge_entity] para atualizar uma entidade existente com valores de propriedade novos ou modificados, sem substituir a entidade por completo.

## <a name="modify-multiple-entities"></a>Modificar várias entidades

Para garantir o processamento atômico de uma solicitação pelo serviço Tabela, você pode enviar várias operações juntas em um lote. Primeiro, use a classe [TableBatch][py_TableBatch] para adicionar várias operações a um único lote. Em seguida, chame [TableService][py_TableService].[commit_batch][py_commit_batch] para enviar as operações em uma operação atômica. Todas as entidades a serem modificadas em lote devem estar na mesma partição.

Esse exemplo adiciona duas entidades juntas em um lote:

```python
from azure.cosmosdb.table.tablebatch import TableBatch
batch = TableBatch()
task004 = {'PartitionKey': 'tasksSeattle', 'RowKey': '004',
           'description': 'Go grocery shopping', 'priority': 400}
task005 = {'PartitionKey': 'tasksSeattle', 'RowKey': '005',
           'description': 'Clean the bathroom', 'priority': 100}
batch.insert_entity(task004)
batch.insert_entity(task005)
table_service.commit_batch('tasktable', batch)
```

Os lotes também podem ser usados com a sintaxe do gerenciador de contexto:

```python
task006 = {'PartitionKey': 'tasksSeattle', 'RowKey': '006',
           'description': 'Go grocery shopping', 'priority': 400}
task007 = {'PartitionKey': 'tasksSeattle', 'RowKey': '007',
           'description': 'Clean the bathroom', 'priority': 100}

with table_service.batch('tasktable') as batch:
    batch.insert_entity(task006)
    batch.insert_entity(task007)
```

## <a name="query-for-an-entity"></a>Consultar uma entidade

Para consultar uma entidade em uma tabela, passe seu PartitionKey e seu RowKey para o método [TableService][py_TableService].[get_entity][py_get_entity].

```python
task = table_service.get_entity('tasktable', 'tasksSeattle', '001')
print(task.description)
print(task.priority)
```

## <a name="query-a-set-of-entities"></a>Consultar um conjunto de entidades

Você pode consultar um conjunto de entidades fornecendo uma cadeia de caracteres de filtro com o parâmetro **filtro**. Este exemplo localiza todas as tarefas em Seattle aplicando um filtro em PartitionKey:

```python
tasks = table_service.query_entities(
    'tasktable', filter="PartitionKey eq 'tasksSeattle'")
for task in tasks:
    print(task.description)
    print(task.priority)
```

## <a name="query-a-subset-of-entity-properties"></a>consultar um subconjunto de propriedades da entidade

Você também pode restringir quais propriedades são retornadas para cada entidade em uma consulta. Essa técnica, chamada *projeção*, reduz a largura de banda e pode melhorar o desempenho da consulta, principalmente para entidades ou conjuntos de resultados grandes. Use o parâmetro **select** e transmita os nomes das propriedades que você deseja que sejam retornadas para o cliente.

A consulta no código a seguir retorna apenas as descrições das entidades na tabela.

> [!NOTE]
> O snippet a seguir funciona apenas no Armazenamento do Azure. Não há suporte para ele no emulador de armazenamento.

```python
tasks = table_service.query_entities(
    'tasktable', filter="PartitionKey eq 'tasksSeattle'", select='description')
for task in tasks:
    print(task.description)
```

## <a name="delete-an-entity"></a>Excluir uma entidade

Exclua uma entidade passando seu **PartitionKey** e seu **RowKey** para o método [delete_entity][py_delete_entity].

```python
table_service.delete_entity('tasktable', 'tasksSeattle', '001')
```

## <a name="delete-a-table"></a>Excluir uma tabela

Se você não precisar mais de uma tabela ou qualquer uma das entidades dentro dela, chame o método [delete_table][py_delete_table] para excluir permanentemente a tabela do Armazenamento do Microsoft Azure.

```python
table_service.delete_table('tasktable')
```

## <a name="next-steps"></a>Próximas etapas

* [Perguntas Frequentes – Desenvolver com a API de Tabela](https://docs.microsoft.com/azure/cosmos-db/faq)
* [Referência da API do SDK do Azure Cosmos DB para Python](https://docs.microsoft.com/python/api/overview/azure/cosmosdb?view=azure-python)
* [Centro de desenvolvedores do Python](https://azure.microsoft.com/develop/python/)
* [Gerenciador de Armazenamento do Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md): Um aplicativo gratuito multiplataforma para trabalhar visualmente com os dados do Armazenamento do Azure no Windows, no macOS e no Linux.
* [Trabalhando com Python no Visual Studio (Windows)](https://docs.microsoft.com/visualstudio/python/overview-of-python-tools-for-visual-studio)


[py_commit_batch]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_create_table]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_delete_entity]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_get_entity]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_insert_entity]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_insert_or_replace_entity]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_Entity]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.models.entity?view=azure-python
[py_merge_entity]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_update_entity]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_delete_table]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_TableService]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_TableBatch]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
