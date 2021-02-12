---
title: Listar bancos de dados restauráveis na API Azure Cosmos DB para MongoDB usando a API REST
description: Mostre o feed de eventos de todas as mutações feitas em todos os Azure Cosmos DB bancos de dados MongoDB na conta restaurável. Isso ajuda no cenário em que o banco de dados foi acidentalmente excluído para obter o tempo de exclusão.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: 5ebb0a23822074f61a16bf1d7652dba589399542
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99537503"
---
# <a name="list-restorable-databases-in-azure-cosmos-db-api-for-mongodb-using-rest-api"></a>Listar bancos de dados restauráveis na API Azure Cosmos DB para MongoDB usando a API REST

> [!IMPORTANT]
> O recurso de restauração pontual (modo de backup contínuo) para Azure Cosmos DB está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Mostre o feed de eventos de todas as mutações feitas em todos os Azure Cosmos DB bancos de dados MongoDB na conta restaurável. Isso ajuda no cenário em que o banco de dados foi acidentalmente excluído para obter o tempo de exclusão. Esta API requer `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` permissão

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableMongodbDatabases?api-version=2020-06-01-preview
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
| 200 OK | [RestorableMongodbDatabasesListResult](#restorablemongodbdatabaseslistresult)| A operação foi concluída com sucesso. |
| Outros códigos de status | [DefaultErrorResponse](#defaulterrorresponse)| Resposta de erro que descreve por que a operação falhou.|

## <a name="examples"></a>Exemplos

### <a name="cosmosdbrestorablemongodbdatabaselist"></a>CosmosDBRestorableMongodbDatabaseList

**Exemplo de solicitação**

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/WestUS/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableMongodbDatabases?api-version=2020-06-01-preview
```

**Resposta de exemplo**

Código de status: 200

```json
{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/36f09704-6be3-4f33-aa05-17b73e504c75/restorableMongodbDatabases/59c21367-b98b-4a8e-abb7-b6f46600decc",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableMongodbDatabases",
      "name": "59c21367-b98b-4a8e-abb7-b6f46600decc",
      "properties": {
        "resource": {
          "_rid": "DLB14gAAAA==",
          "eventTimestamp": "2020-09-02T19:45:03Z",
          "ownerId": "Database1",
          "ownerResourceId": "PD5DALigDgw=",
          "operationType": "Create"
        }
      }
    },
    {
      "id": "/subscriptions/2296c272-5d55-40d9-bc05-4d56dc2d7588/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableMongodbDatabases/8456cb17-cdb0-4c6a-8db8-d0ff3f886257",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableMongodbDatabases",
      "name": "8456cb17-cdb0-4c6a-8db8-d0ff3f886257",
      "properties": {
        "resource": {
          "_rid": "ESXNLAAAAA==",
          "eventTimestamp": "2020-09-02T19:53:42Z",
          "ownerId": "Database1",
          "ownerResourceId": "PD5DALigDgw=",
          "operationType": "Delete"
        }
      }
    }
  ]
}
```

## <a name="definitions"></a>Definições

| Definição | Descrição | | --- || --- | | [DefaultErrorResponse](#defaulterrorresponse) | Uma resposta de erro do serviço. | | [ErrorResponse](#errorresponse) | Resposta de erro. | | [OperationType](#operationtype) | Enum para indicar o tipo de operação do evento. | | [Recurso](#resource) | O recurso de um evento de API Azure Cosmos DB para o banco de dados MongoDB | | [RestorableMongodbDatabaseGetResult](#restorablemongodbdatabasegetresult) | Um evento de API Azure Cosmos DB para o banco de dados MongoDB | | [RestorableMongodbDatabaseProperties](#restorablemongodbdatabaseproperties) | As propriedades de um evento de API Azure Cosmos DB para o banco de dados MongoDB | | [RestorableMongodbDatabasesListResult](#restorablemongodbdatabaseslistresult) | A resposta de operação de lista, que contém a API de Azure Cosmos DB para eventos de banco de dados MongoDB e suas propriedades. |

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

### <a name="resource"></a><a id="resource"></a>Recurso

O recurso de um evento de API Azure Cosmos DB para o banco de dados MongoDB

| **Nome** | **Tipo** | **Descrição** |
| --- | --- | --- |
| _rid |string| Uma propriedade gerada pelo sistema. Um identificador exclusivo. |
| eventTimestamp |string| A hora em que o evento de banco de dados ocorreu. |
| operationType |[OperationType](#operationtype)| O tipo de operação deste evento de banco de dados.  |
| ownerId |string| O nome da API de Azure Cosmos DB para o banco de dados MongoDB.|
| ownerResourceId |string| A ID de recurso Azure Cosmos DB API para o banco de dados MongoDB. |

### <a name="restorablemongodbdatabasegetresult"></a><a id="restorablemongodbdatabasegetresult"></a>RestorableMongodbDatabaseGetResult

Um evento de API Azure Cosmos DB para o banco de dados MongoDB

| **Nome** | **Tipo** | **Descrição** |
| --- | --- | --- |
| ID |string| O identificador de recurso exclusivo do recurso de Azure Resource Manager. |
| name |string| O nome do recurso do Resource Manager. |
| properties |[RestorableMongodbDatabaseProperties](#restorablemongodbdatabaseproperties)| As propriedades de um evento de API Azure Cosmos DB para o banco de dados MongoDB. |
| tipo |string| O tipo de recurso do Azure. |

### <a name="restorablemongodbdatabaseproperties"></a><a id="restorablemongodbdatabaseproperties"></a>RestorableMongodbDatabaseProperties

As propriedades de uma API de Azure Cosmos DB para evento de banco de dados MongoDB

| **Nome** | **Tipo** | **Descrição** |
| --- | --- | --- |
| recurso |[Recurso](#resource)| O recurso de um evento de API Azure Cosmos DB para o banco de dados MongoDB |

### <a name="restorablemongodbdatabaseslistresult"></a><a id="restorablemongodbdatabaseslistresult"></a>RestorableMongodbDatabasesListResult

A resposta de operação de lista, que contém os eventos de banco de dados e suas propriedades.

| **Nome** | **Tipo** | **Descrição** |
| --- | --- | --- |
| valor |[RestorableMongodbDatabaseGetResult](#restorablemongodbdatabasegetresult)[]| Lista de eventos de banco de dados e suas propriedades. |

## <a name="next-steps"></a>Próximas etapas

* [Listar recursos restauráveis](restorable-mongodb-resources-list.md)  na API Azure Cosmos DB para MongoDB usando a API REST.
* [Liste coleções restauráveis](restorable-mongodb-collections-list.md)  na API Azure Cosmos DB para MongoDB usando a API REST.
* [Modelo de recurso](continuous-backup-restore-resource-model.md) do modo de backup contínuo.