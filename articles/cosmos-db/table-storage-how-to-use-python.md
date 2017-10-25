---
title: Como usar o Armazenamento de Tabelas do Azure com Python | Microsoft Docs
description: "Armazene dados estruturados na nuvem usando o Armazenamento de Tabelas do Azure, um repositório de dados NoSQL."
services: cosmos-db
documentationcenter: python
author: mimig1
manager: jhubbard
editor: tysonn
ms.assetid: 7ddb9f3e-4e6d-4103-96e6-f0351d69a17b
ms.service: cosmos-db
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 05/16/2017
ms.author: mimig
ms.openlocfilehash: 0c46f04786ba4b62bd7ca22c5e25643123e6e136
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-table-storage-in-python"></a>Como usar o Armazenamento de Tabelas no Python

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-cosmos-db-langsoon-tip-include](../../includes/storage-table-cosmos-db-langsoon-tip-include.md)]

Este guia mostra como executar cenários comuns do Armazenamento de Tabelas do Azure no Python usando o [SDK do Armazenamento do Microsoft Azure para Python](https://github.com/Azure/azure-storage-python). Os cenários abordados incluem a criação e a exclusão de uma tabela e a inserção e consulta de entidades.

Enquanto estiver trabalhando nos cenários deste tutorial, talvez você queira consultar a [Referência do SDK do Armazenamento do Azure para API do Python](https://azure-storage.readthedocs.io/en/latest/index.html).

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="install-the-azure-storage-sdk-for-python"></a>Instalar o SDK do Armazenamento do Azure para Python

Depois de criar uma conta de armazenamento, a próxima etapa é instalar o [SDK do Armazenamento do Microsoft Azure para Python](https://github.com/Azure/azure-storage-python). Para obter detalhes sobre como instalar o SDK, consulte o arquivo [README.rst](https://github.com/Azure/azure-storage-python/blob/master/README.rst) no repositório do SDK do Armazenamento para Python no GitHub.

## <a name="create-a-table"></a>Criar uma tabela

Para trabalhar com o serviço Tabela do Azure no Python, você deve importar o módulo [TableService][py_TableService]. Como você estará trabalhando com entidades de tabela, também precisará da classe [Entity][py_Entity]. Adicione este código próximo à parte superior seu arquivo Python para importar ambos:

```python
from azure.storage.table import TableService, Entity
```

Crie um objeto [TableService][py_TableService], passando o nome da conta de armazenamento e a chave de conta. Substitua `myaccount` e `mykey` por seu nome de conta e a chave e chame [create_table][py_create_table] para criar a tabela no Armazenamento do Azure.

```python
table_service = TableService(account_name='myaccount', account_key='mykey')

table_service.create_table('tasktable')
```

## <a name="add-an-entity-to-a-table"></a>Adicionar uma entidade a uma tabela

Para adicionar uma entidade, primeiro crie um objeto que representa a entidade, em seguida, passe o objeto para o método [TableService][py_TableService].[insert_entity][py_insert_entity]. O objeto de entidade pode ser um dicionário ou um objeto do tipo [Entity][py_Entity] e define os valores e nomes de propriedade da entidade. Cada entidade deve incluir as propriedades [PartitionKey e RowKey](#partitionkey-and-rowkey) necessárias, além de quaisquer outras propriedades que você definir para a entidade.

Este exemplo cria um objeto de dicionário que representa uma entidade, em seguida, o passa para o método [insert_entity][py_insert_entity] para adicioná-lo à tabela:

```python
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '001', 'description' : 'Take out the trash', 'priority' : 200}
table_service.insert_entity('tasktable', task)
```

Este exemplo cria um objeto [Entity][py_Entity], em seguida, o passa para o método [insert_entity][py_insert_entity] para adicioná-lo à tabela:

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

Para atualizar todos os valores de propriedade da entidade, chame o método [update_entity][py_update_entity]. Este exemplo mostra como substituir uma entidade existente pela versão atualizada:

```python
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '001', 'description' : 'Take out the garbage', 'priority' : 250}
table_service.update_entity('tasktable', task)
```

Se a entidade que está sendo atualizada ainda não existir, a operação de atualização falhará. Se você quiser armazenar uma entidade quer ela exista ou não, use [insert_or_replace_entity][py_insert_or_replace_entity]. No exemplo a seguir, a primeira chamada substituirá a entidade existente. A segunda chamada vai inserir uma nova entidade, contanto que não exista na tabela nenhuma entidade com o PartitionKey e o RowKey especificados.

```python
# Replace the entity created earlier
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '001', 'description' : 'Take out the garbage again', 'priority' : 250}
table_service.insert_or_replace_entity('tasktable', task)

# Insert a new entity
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '003', 'description' : 'Buy detergent', 'priority' : 300}
table_service.insert_or_replace_entity('tasktable', task)
```

> [!TIP]
> O método [update_entity][py_update_entity] substitui todas as propriedades e valores de uma entidade existente, que você também pode usar para remover propriedades de uma entidade existente. Você pode usar o método [merge_entity][py_merge_entity] para atualizar uma entidade existente com valores de propriedade novos ou modificados sem substituir totalmente a entidade.

## <a name="modify-multiple-entities"></a>Modificar várias entidades

Para garantir o processamento atômico de uma solicitação pelo serviço Tabela, você pode enviar várias operações juntas em um lote. Primeiro, use a classe [TableBatch][py_TableBatch] para adicionar várias operações a um único lote. Em seguida, chame [TableService][py_TableService].[commit_batch][py_commit_batch] para enviar as operações em uma operação atômica. Todas as entidades a serem modificadas em lote devem estar na mesma partição.

Esse exemplo adiciona duas entidades juntas em um lote:

```python
from azure.storage.table import TableBatch
batch = TableBatch()
task004 = {'PartitionKey': 'tasksSeattle', 'RowKey': '004', 'description' : 'Go grocery shopping', 'priority' : 400}
task005 = {'PartitionKey': 'tasksSeattle', 'RowKey': '005', 'description' : 'Clean the bathroom', 'priority' : 100}
batch.insert_entity(task004)
batch.insert_entity(task005)
table_service.commit_batch('tasktable', batch)
```

Os lotes também podem ser usados com a sintaxe do gerenciador de contexto:

```python
task006 = {'PartitionKey': 'tasksSeattle', 'RowKey': '006', 'description' : 'Go grocery shopping', 'priority' : 400}
task007 = {'PartitionKey': 'tasksSeattle', 'RowKey': '007', 'description' : 'Clean the bathroom', 'priority' : 100}

with table_service.batch('tasktable') as batch:
    batch.insert_entity(task006)
    batch.insert_entity(task007)
```

## <a name="query-for-an-entity"></a>Consultar uma entidade

Para consultar uma entidade em uma tabela, passe seu PartitionKey e RowKey para o método [TableService][py_TableService].[get_entity][py_get_entity].

```python
task = table_service.get_entity('tasktable', 'tasksSeattle', '001')
print(task.description)
print(task.priority)
```

## <a name="query-a-set-of-entities"></a>Consultar um conjunto de entidades

Você pode consultar um conjunto de entidades fornecendo uma cadeia de caracteres de filtro com o parâmetro **filtro**. Este exemplo localiza todas as tarefas em Seattle aplicando um filtro em PartitionKey:

```python
tasks = table_service.query_entities('tasktable', filter="PartitionKey eq 'tasksSeattle'")
for task in tasks:
    print(task.description)
    print(task.priority)
```

## <a name="query-a-subset-of-entity-properties"></a>consultar um subconjunto de propriedades da entidade

Você também pode restringir quais propriedades são retornadas para cada entidade em uma consulta. Essa técnica, chamada *projeção*, reduz a largura de banda e pode melhorar o desempenho da consulta, principalmente para entidades ou conjuntos de resultados grandes. Use o parâmetro **select** e transmita os nomes das propriedades que você deseja que sejam retornadas para o cliente.

A consulta no código a seguir retorna apenas as descrições das entidades na tabela.

> [!NOTE]
> O trecho a seguir funciona apenas no Armazenamento do Azure. Não há suporte para ele no emulador de armazenamento.

```python
tasks = table_service.query_entities('tasktable', filter="PartitionKey eq 'tasksSeattle'", select='description')
for task in tasks:
    print(task.description)
```

## <a name="delete-an-entity"></a>Excluir uma entidade

Exclua uma entidade passando seu PartitionKey e RowKey para o método [delete_entity][py_delete_entity].

```python
table_service.delete_entity('tasktable', 'tasksSeattle', '001')
```

## <a name="delete-a-table"></a>Excluir uma tabela

Se você não precisar mais de uma tabela ou qualquer uma das entidades dentro dela, chame o método [delete_table][py_delete_table] para excluir permanentemente a tabela do Armazenamento do Azure.

```python
table_service.delete_table('tasktable')
```

## <a name="next-steps"></a>Próximas etapas

* [Referência do SDK do Armazenamento do Azure para API do Python](https://azure-storage.readthedocs.io/en/latest/index.html)
* [SDK do Armazenamento do Azure para Python](https://github.com/Azure/azure-storage-python)
* [Centro de desenvolvedores do Python](https://azure.microsoft.com/develop/python/)
* [Gerenciador de Armazenamento do Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md): um aplicativo gratuito de plataforma cruzada para trabalhar visualmente com os dados do Armazenamento do Azure no Windows, macOS e Linux.

[py_commit_batch]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html#azure.storage.table.tableservice.TableService.commit_batch
[py_create_table]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html#azure.storage.table.tableservice.TableService.create_table
[py_delete_entity]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html#azure.storage.table.tableservice.TableService.delete_entity
[py_delete_table]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html#azure.storage.table.tableservice.TableService.delete_table
[py_Entity]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.models.html#azure.storage.table.models.Entity
[py_get_entity]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html#azure.storage.table.tableservice.TableService.get_entity
[py_insert_entity]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html#azure.storage.table.tableservice.TableService.insert_entity
[py_insert_or_replace_entity]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html#azure.storage.table.tableservice.TableService.insert_or_replace_entity
[py_merge_entity]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html#azure.storage.table.tableservice.TableService.merge_entity
[py_update_entity]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html#azure.storage.table.tableservice.TableService.update_entity
[py_TableService]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html
[py_TableBatch]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tablebatch.html#azure.storage.table.tablebatch.TableBatch
