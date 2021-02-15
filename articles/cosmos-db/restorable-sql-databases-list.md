---
title: Listar bancos de dados de API SQL restauráveis no Azure Cosmos DB usando a API REST
description: Mostre o feed de eventos de todas as mutações feitas em todos os Azure Cosmos DB bancos de dados SQL na conta restaurável. Isso ajuda no cenário em que o banco de dados foi acidentalmente excluído para obter o tempo de exclusão.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: 218a39a2bf8e9269e43c4876c1654d94be886997
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99539494"
---
# <a name="list-restorable-sql-api-databases-in-azure-cosmos-db-using-rest-api"></a>Listar bancos de dados de API SQL restauráveis no Azure Cosmos DB usando a API REST

> [!IMPORTANT]
> O recurso de restauração pontual (modo de backup contínuo) para Azure Cosmos DB está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Mostre o feed de eventos de todas as mutações feitas em todos os Azure Cosmos DB bancos de dados SQL na conta restaurável. Isso ajuda no cenário em que o banco de dados foi acidentalmente excluído para obter o tempo de exclusão. Esta API requer `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` permissão

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableSqlDatabases?api-version=2020-06-01-preview
```

## <a name="uri-parameters"></a>Parâmetros do URI

| Nome | Em | Obrigatório | Type | Descrição |
| --- | --- | --- | --- | --- |
| **instanceId** | caminho | True |string| O GUID de instanceId de uma conta de banco de dados restaurável. |
| **local** | caminho | True | string| Azure Cosmos DB região, com espaços entre palavras e cada palavra em maiúsculas. |
| **subscriptionId** | caminho | True | string| A ID da assinatura de destino. |
| **versão da API** | Consulta | True | string | A versão da API a ser usada para esta operação. |

## <a name="responses"></a>Respostas

| Nome | Tipo | Descrição |
| --- | --- | --- |
| 200 OK | [RestorableSqlDatabasesListResult](#restorablesqldatabaseslistresult)| A operação foi concluída com sucesso. |
| Outros códigos de status | [DefaultErrorResponse](#defaulterrorresponse)| Resposta de erro que descreve por que a operação falhou. |

## <a name="examples"></a>Exemplos

### <a name="cosmosdbrestorablesqldatabaselist"></a>CosmosDBRestorableSqlDatabaseList

**Exemplo de solicitação**

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/WestUS/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableSqlDatabases?api-version=2020-06-01-preview
```

**Resposta de exemplo**

Código de status: 200

```json
{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/36f09704-6be3-4f33-aa05-17b73e504c75/restorableSqlDatabases/59c21367-b98b-4a8e-abb7-b6f46600decc",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableSqlDatabases",
      "name": "59c21367-b98b-4a8e-abb7-b6f46600decc",
      "properties": {
        "resource": {
          "_rid": "DLB14gAAAA==",
          "eventTimestamp": "2020-09-02T19:45:03Z",
          "ownerId": "Database1",
          "ownerResourceId": "3fu-hg==",
          "operationType": "Create",
          "database": {
            "id": "Database1",
            "_rid": "3fu-hg==",
            "_self": "dbs/3fu-hg==/",
            "_etag": "\"0000c20a-0000-0700-0000-5f4ff63f0000\"",
            "_colls": "colls/",
            "_users": "users/"
          }
        }
      }
    },
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableSqlDatabases/8456cb17-cdb0-4c6a-8db8-d0ff3f886257",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableSqlDatabases",
      "name": "8456cb17-cdb0-4c6a-8db8-d0ff3f886257",
      "properties": {
        "resource": {
          "_rid": "ESXNLAAAAA==",
          "eventTimestamp": "2020-09-02T19:53:42Z",
          "ownerId": "Database1",
          "ownerResourceId": "3fu-hg==",
          "database": {
            "id": "Database1",
            "_rid": "3fu-hg==",
            "_self": "dbs/3fu-hg==/",
            "_etag": "\"0000c20a-0000-0700-0000-5f4ff63f0000\"",
            "_colls": "colls/",
            "_users": "users/",
            "_ts": 1599075903
          },
          "operationType": "Delete"
        }
      }
    },
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableSqlDatabases/2c07991b-9c7c-4e85-be68-b18c1f2ff326",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableSqlDatabases",
      "name": "2c07991b-9c7c-4e85-be68-b18c1f2ff326",
      "properties": {
        "resource": {
          "_rid": "aXFqUQAAAA==",
          "eventTimestamp": "2020-09-02T19:53:15Z",
          "ownerId": "Database2",
          "ownerResourceId": "0SziSg==",
          "database": {
            "id": "Database2",
            "_rid": "0SziSg==",
            "_self": "dbs/0SziSg==/",
            "_etag": "\"0000ca0a-0000-0700-0000-5f4ff82b0000\"",
            "_colls": "colls/",
            "_users": "users/"
          },
          "operationType": "Create"
        }
      }
    }
  ]
}
```

## <a name="definitions"></a>Definições

| Definição | Descrição | | --- || --- | | [Banco de dados](#database) | Azure Cosmos DB objeto de recurso do banco de dados SQL | | [DefaultErrorResponse](#defaulterrorresponse) | Uma resposta de erro do serviço. | | [ErrorResponse](#errorresponse) | Resposta de erro. | | [OperationType](#operationtype) | Enum para indicar o tipo de operação do evento. | | [Recurso](#resource) | O recurso de um evento de banco de dados SQL Azure Cosmos DB | | [RestorableSqlDatabaseGetResult](#restorablesqldatabasegetresult) | Um evento de banco de dados SQL Azure Cosmos DB | | [RestorableSqlDatabaseProperties](#restorablesqldatabaseproperties) | As propriedades de um evento de banco de dados SQL Azure Cosmos DB | | [RestorableSqlDatabasesListResult](#restorablesqldatabaseslistresult) | A resposta de operação de lista, que contém os eventos do banco de dados SQL e suas propriedades. |

### <a name="database"></a><a id="database"></a>Banco

Azure Cosmos DB objeto de recurso do banco de dados SQL

| **Nome**  |  do **Tipo**  |  de **Descrição** | | --- || --- | ---| | _colls | cadeia de caracteres | Uma propriedade gerada pelo sistema que especificava o caminho endereçável do recurso de coleções. | | _etag | cadeia de caracteres | Uma propriedade gerada pelo sistema que representa a ETag de recurso necessária para o controle de simultaneidade otimista. | | _rid | cadeia de caracteres | Uma propriedade gerada pelo sistema. Um identificador exclusivo. | | _self | cadeia de caracteres | Uma propriedade gerada pelo sistema que especifica o caminho endereçável do recurso de banco de dados. | | _ts | | Uma propriedade gerada pelo sistema que denota o último carimbo de data/hora atualizado do recurso. | | _users | cadeia de caracteres | Uma propriedade gerada pelo sistema que especifica o caminho endereçável do recurso do usuário. | | ID | cadeia de caracteres | Nome do banco de dados SQL do Azure Cosmos DB |

### <a name="defaulterrorresponse"></a><a id="defaulterrorresponse"></a>DefaultErrorResponse

Uma resposta de erro do serviço.

| **Nome** | **Tipo** | **Descrição** |
| --- | --- | --- |
| erro | [ErrorResponse](#errorresponse)| Resposta de erro. |

### <a name="errorresponse"></a><a id="errorresponse"></a>ErrorResponse

Resposta de erro.

| **Nome** | **Tipo** | **Descrição** |
| --- | --- | --- |
| code |string| Código do erro. |
| message |string| Mensagem de erro indicando por que a operação falhou. |

### <a name="operationtype"></a><a id="operationtype"></a>OperationType

Enum para indicar o tipo de operação do evento.

| **Nome** | **Tipo** | **Descrição** |
| --- | --- | --- |
| Criar |string|evento de criação de banco de dados|
| Excluir |string|evento de exclusão de banco de dados|
| Substitua |string|evento de modificação do banco de dados|
| SystemOperation |string|evento de modificação do banco de dados disparado pelo sistema. Este evento não é iniciado pelo usuário|

### <a name="resource"></a><a id="resource"></a>Recurso

O recurso de um evento de banco de dados SQL Azure Cosmos DB

| **Nome** | **Tipo** | **Descrição** |
| --- | --- | --- |
| _rid |string| Uma propriedade gerada pelo sistema. Um identificador exclusivo. |
| Banco de Dados |[Backup de banco de dados](#database)| Azure Cosmos DB objeto de recurso do banco de dados SQL |
| eventTimestamp |string| A hora em que o evento de banco de dados ocorreu. |
| operationType |[OperationType](#operationtype)| O tipo de operação deste evento de banco de dados. |
| ownerId |string| O nome do banco de dados SQL. |
| ownerResourceId |string| A ID de recurso do banco de dados SQL. |

### <a name="restorablesqldatabasegetresult"></a><a id="restorablesqldatabasegetresult"></a>RestorableSqlDatabaseGetResult

Um evento de banco de dados SQL Azure Cosmos DB

| **Nome** | **Tipo** | **Descrição** |
| --- | --- | --- |
| ID |string| O identificador de recurso exclusivo do recurso de Azure Resource Manager. |
| name |string| O nome do recurso de Azure Resource Manager. |
| properties | [RestorableSqlDatabaseProperties](#restorablesqldatabaseproperties)| As propriedades de um evento do banco de dados SQL. |
| tipo |string| O tipo de recurso do Azure. |

### <a name="restorablesqldatabaseproperties"></a><a id="restorablesqldatabaseproperties"></a>RestorableSqlDatabaseProperties

As propriedades de um evento de banco de dados SQL Azure Cosmos DB

| **Nome** | **Tipo** | **Descrição** |
| --- | --- | --- |
| recurso |[Recurso](#resource)| O recurso de um evento de banco de dados SQL Azure Cosmos DB |

### <a name="restorablesqldatabaseslistresult"></a><a id="restorablesqldatabaseslistresult"></a>RestorableSqlDatabasesListResult

A resposta de operação de lista, que contém os eventos do banco de dados SQL e suas propriedades.

| **Nome** | **Tipo** | **Descrição** |
| --- | --- | --- |
| valor |[RestorableSqlDatabaseGetResult](#restorablesqldatabasegetresult)[]| Lista de eventos do banco de dados SQL e suas propriedades. |

## <a name="next-steps"></a>Próximas etapas

* [Listar contêineres restauráveis](restorable-sql-containers-list.md) no Azure Cosmos DB API do SQL usando a API REST.
* [Modelo de recurso](continuous-backup-restore-resource-model.md) do modo de backup contínuo.