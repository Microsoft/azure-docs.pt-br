---
title: Listar coleções restauráveis em Azure Cosmos DB API do MongoDB-API REST
description: Mostrar o feed de eventos de todas as mutações feitas em todas as coleções do Azure Cosmos DB MongoDB em um banco de dados específico. Isso ajuda no cenário em que o contêiner foi acidentalmente excluído.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: c19de134f40c58a687dcf6bac6ac156e46cef7da
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99537536"
---
# <a name="list-restorable-collections-in-azure-cosmos-db-api-for-mongodb-using-rest-api"></a>Listar coleções restauráveis na API Azure Cosmos DB para MongoDB usando a API REST

> [!IMPORTANT]
> O recurso de restauração pontual (modo de backup contínuo) para Azure Cosmos DB está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Mostre o feed de eventos de todas as mutações feitas em todas as Azure Cosmos DB API para coleções do MongoDB em um banco de dados específico. Isso ajuda no cenário em que o contêiner foi acidentalmente excluído. Esta API requer `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` permissão

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableMongodbCollections?api-version=2020-06-01-preview
```

Com parâmetros opcionais:

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableMongodbCollections?api-version=2020-06-01-preview&amp;restorableMongodbDatabaseRid={restorableMongodbDatabaseRid}
```

## <a name="uri-parameters"></a>Parâmetros do URI

| Nome | Em | Obrigatório | Type | Descrição |
| --- | --- | --- | --- | --- |
| **instanceId** | caminho | True |string| O GUID de instanceId de uma conta de banco de dados restaurável. |
| **local** | caminho | True | string| Azure Cosmos DB região, com espaços entre palavras e cada palavra em maiúsculas. |
| **subscriptionId** | caminho | True | string| A ID da assinatura de destino. |
| **versão da API** | Consulta | True | string | A versão da API a ser usada para esta operação. |
| **restorableMongodbDatabaseRid** | Consulta | |string| A ID do recurso da API do Azure Cosmos DB para o banco de dados MongoDB. |

## <a name="responses"></a>Respostas

| Nome | Tipo | Descrição |
| --- | --- | --- |
| 200 OK | [RestorableMongodbCollectionsListResult](#restorablemongodbcollectionslistresult)| A operação foi concluída com sucesso. |
| Outros códigos de status | [DefaultErrorResponse](#defaulterrorresponse)| Resposta de erro que descreve por que a operação falhou.|

## <a name="examples"></a>Exemplos

### <a name="cosmosdbrestorablemongodbcollectionlist"></a>CosmosDBRestorableMongodbCollectionList

**Exemplo de solicitação**

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/WestUS/restorableDatabaseAccounts/98a570f2-63db-4117-91f0-366327b7b353/restorableMongodbCollections?api-version=2020-06-01-preview&amp;restorableMongodbDatabaseRid=PD5DALigDgw=
```

**Resposta de exemplo**

Código de status: 200

```json
{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/98a570f2-63db-4117-91f0-366327b7b353/restorableMongodbCollections/79609a98-3394-41f8-911f-cfab0c075c86",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableMongodbCollections",
      "name": "79609a98-3394-41f8-911f-cfab0c075c86",
      "properties": {
        "resource": {
          "_rid": "zAyAPQAAAA==",
          "eventTimestamp": "2020-10-13T04:56:42Z",
          "ownerId": "Collection1",
          "ownerResourceId": "V18LoLrv-qA=",
          "operationType": "Create"
        }
      }
    }
  ]
}
```

## <a name="definitions"></a>Definições

|Definição  | Descrição|
| --- | --- |
| [DefaultErrorResponse](#defaulterrorresponse) | Uma resposta de erro do serviço. |
| [ErrorResponse](#errorresponse) | Resposta de erro. |
| [OperationType](#operationtype) | Enum para indicar o tipo de operação do evento. |
| [Recurso](#resource) | O recurso de uma API de Azure Cosmos DB para o evento de coleta do MongoDB |
| [RestorableMongodbCollectionGetResult](#restorablemongodbcollectiongetresult) | Uma API Azure Cosmos DB para o evento de coleta do MongoDB |
| [RestorableMongodbCollectionProperties](#restorablemongodbcollectionproperties) | As propriedades de uma API de Azure Cosmos DB para o evento de coleta do MongoDB |
| [RestorableMongodbCollectionsListResult](#restorablemongodbcollectionslistresult) | A resposta de operação de lista, que contém os eventos de coleta e suas propriedades. |

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
| Criar |string|evento de criação de coleção|
| Excluir |string|evento de exclusão de coleção|
| Substitua |string|evento de modificação da coleção|

### <a name="resource"></a><a id="resource"></a>Recurso

O recurso de um evento de coleta Azure Cosmos DB MongoDB

| **Nome** | **Tipo** | **Descrição** |
| --- | --- | --- |
| _rid |string| Uma propriedade gerada pelo sistema. Um identificador exclusivo. |
| eventTimestamp |string| A hora em que o evento de coleta ocorreu. |
| operationType |[OperationType](#operationtype)|  O tipo de operação deste evento de coleta. |
| ownerId |string| O nome da coleção do MongoDB.|
| ownerResourceId |string| A ID de recurso da coleção do MongoDB. |

### <a name="restorablemongodbcollectiongetresult"></a><a id="restorablemongodbcollectiongetresult"></a>RestorableMongodbCollectionGetResult

Um evento de coleta Azure Cosmos DB MongoDB

| **Nome** | **Tipo** | **Descrição** |
| --- | --- | --- |
| ID |string| O identificador de recurso exclusivo do recurso de Azure Resource Manager. |
| name |string| O nome do recurso do Resource Manager. |
| properties |[RestorableMongodbCollectionProperties](#restorablemongodbcollectionproperties)| As propriedades de um evento de coleção. |
| tipo |string| O tipo de recurso do Azure. |

### <a name="restorablemongodbcollectionproperties"></a><a id="restorablemongodbcollectionproperties"></a>RestorableMongodbCollectionProperties

As propriedades de um evento de coleta Azure Cosmos DB MongoDB

| **Nome** | **Tipo** | **Descrição** |
| --- | --- | --- |
| recurso | [Recurso](#resource)| O recurso de uma API de Azure Cosmos DB para o evento de coleta do MongoDB |

### <a name="restorablemongodbcollectionslistresult"></a><a id="restorablemongodbcollectionslistresult"></a>RestorableMongodbCollectionsListResult

A resposta de operação de lista, que contém os eventos de coleta e suas propriedades.

| **Nome** | **Tipo** | **Descrição** |
| --- | --- | --- |
| valor |[RestorableMongodbCollectionGetResult](#restorablemongodbcollectiongetresult)[]| Lista de Azure Cosmos DB API para eventos de coleta do MongoDB e suas propriedades. |

## <a name="next-steps"></a>Próximas etapas

* [Listar bancos de dados restauráveis](restorable-mongodb-databases-list.md)  na API Azure Cosmos DB para MongoDB usando a API REST.
* [Modelo de recurso](continuous-backup-restore-resource-model.md) do modo de backup contínuo.