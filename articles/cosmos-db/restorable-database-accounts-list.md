---
title: Listar contas de banco de dados restauráveis usando Azure Cosmos DB API REST
description: Lista todas as contas de banco de dados restauráveis Azure Cosmos DB disponíveis em uma assinatura.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: 71aafc756e5291e148c3b162f8946544b6e3c2d0
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99537570"
---
# <a name="list-restorable-database-accounts-using-azure-cosmos-db-rest-api"></a>Listar contas de banco de dados restauráveis usando Azure Cosmos DB API REST

> [!IMPORTANT]
> O recurso de restauração pontual (modo de backup contínuo) para Azure Cosmos DB está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Lista todas as contas de banco de dados restauráveis Azure Cosmos DB disponíveis na assinatura. Esta chamada requer `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/read` permissão.

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/restorableDatabaseAccounts?api-version=2020-06-01-preview
```

## <a name="uri-parameters"></a>Parâmetros do URI

| Nome | Em | Obrigatório | Type | Descrição |
| --- | --- | --- | --- | --- |
| **subscriptionId** | caminho | True | string| A ID da assinatura de destino. |
| **versão da API** | Consulta | True | string | A versão da API a ser usada para esta operação. |

## <a name="responses"></a>Respostas

| Nome | Tipo | Descrição |
| --- | --- | --- |
| 200 OK | [RestorableDatabaseAccountsListResult](#restorabledatabaseaccountslistresult)| A operação foi concluída com sucesso. |
| Outros códigos de status | [DefaultErrorResponse](#defaulterrorresponse)| Resposta de erro que descreve por que a operação falhou. |

## <a name="examples"></a>Exemplos

### <a name="cosmosdbdatabaseaccountlist"></a>CosmosDBDatabaseAccountList

**Exemplo de solicitação**

```http
GET https://management.azure.com/subscriptions/subid/providers/Microsoft.DocumentDB/restorableDatabaseAccounts?api-version=2020-06-01-preview
```

**Resposta de exemplo**

Código de status: 200

```json
{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d",
      "name": "d9b26648-2f53-4541-b3d8-3044f4f9810d",
      "location": "West US",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts",
      "properties": {
        "accountName": "ddb1",
        "creationTime": "2020-04-11T21:56:15Z",
        "deletionTime": "2020-06-12T22:05:09Z",
        "apiType": "Sql",
        "restorableLocations": [
          {
            "locationName": "South Central US",
            "regionalDatabaseAccountInstanceId": "d7a01f78-606f-45c6-9dac-0df32f433bb5",
            "creationTime": "2020-10-30T21:13:10Z",
            "deletionTime": "2020-10-30T21:13:35Z"
          },
          {
            "locationName": "West US",
            "regionalDatabaseAccountInstanceId": "fdb43d84-1572-4697-b6e7-2bcda0c51b2c",
            "creationTime": "2020-10-30T21:13:10Z"
          }
        ]
      }
    },
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/4f9e6ace-ac7a-446c-98bc-194c502a06b4",
      "name": "4f9e6ace-ac7a-446c-98bc-194c502a06b4",
      "location": "East US",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts",
      "properties": {
        "accountName": "ddb2",
        "creationTime": "2020-05-01T08:05:18Z",
        "apiType": "Sql",
        "restorableLocations": [
          {
            "locationName": "South Central US",
            "regionalDatabaseAccountInstanceId": "d7a01f78-606f-45c6-9dac-0df32f433bb5",
            "creationTime": "2020-10-30T21:13:10Z",
            "deletionTime": "2020-10-30T21:13:35Z"
          },
          {
            "locationName": "West US",
            "regionalDatabaseAccountInstanceId": "fdb43d84-1572-4697-b6e7-2bcda0c51b2c",
            "creationTime": "2020-10-30T21:13:10Z"
          }
        ]
      }
    }
  ]
}
```

## <a name="definitions"></a>Definições

|Definição  | Descrição|
| --- | --- |
| [ApiType](#apitype) | Enum para indicar o tipo de API da conta de banco de dados restaurável. |
| [DefaultErrorResponse](#defaulterrorresponse) | Uma resposta de erro do serviço. |
| [ErrorResponse](#errorresponse) | Resposta de erro. |
| [RestorableDatabaseAccountGetResult](#restorabledatabaseaccountgetresult) | Uma conta de banco de dados restaurável Azure Cosmos DB. |
| [RestorableDatabaseAccountsListResult](#restorabledatabaseaccountslistresult) | A resposta de operação de lista, que contém as contas de banco de dados restauráveis e suas propriedades. |
| [RestorableLocationResource](#restorablelocationresource) | Propriedades da conta restaurável regional. |

### <a name="apitype"></a><a id="apitype"></a>ApiType

Enum para indicar o tipo de API da conta de banco de dados restaurável.

| **Nome** | **Tipo** |
| --- | --- |
| Cassandra |string|
| Gremlin |string|
| GremlinV2 |string|
| MongoDB |string|
| Sql |string|
| Tabela |string|

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

### <a name="restorabledatabaseaccountgetresult"></a><a id="restorabledatabaseaccountgetresult"></a>RestorableDatabaseAccountGetResult

Uma conta de banco de dados restaurável Azure Cosmos DB.

| **Nome** | **Tipo** | **Descrição** |
| --- | --- | --- |
| ID |string| O identificador de recurso exclusivo do recurso de Azure Resource Manager. |
| local |string| O local do grupo de recursos ao qual o recurso pertence. |
| name |string| O nome do recurso do Resource Manager. |
| Properties. accountName |string| O nome da conta de banco de dados global |
| Properties. apiType |[ApiType](#apitype)| O tipo de API da conta de banco de dados restaurável. |
| Propriedades. CreationTime |string| A hora de criação da conta de banco de dados restaurável (formato ISO-8601). |
| Propriedades. exdeleble |string| A hora em que a conta de banco de dados restaurável foi excluída (formato ISO-8601). |
| Properties. restorableLocations |[RestorableLocationResource](#restorablelocationresource)[]| Lista de regiões da qual a conta do banco de dados pode ser restaurada. |
| tipo |string| O tipo de recurso do Azure. |

### <a name="restorabledatabaseaccountslistresult"></a><a id="restorabledatabaseaccountslistresult"></a>RestorableDatabaseAccountsListResult

A resposta de operação de lista que contém as contas de banco de dados restauráveis e suas propriedades.

| **Nome** | **Tipo** | **Descrição** |
| --- | --- | --- |
| valor |[RestorableDatabaseAccountGetResult](#restorabledatabaseaccountgetresult)[]| Lista de contas de banco de dados restauráveis e suas propriedades. |

### <a name="restorablelocationresource"></a><a id="restorablelocationresource"></a>RestorableLocationResource

Propriedades da conta restaurável regional.

| **Nome** | **Tipo** | **Descrição** |
| --- | --- | --- |
| creationTime |string| A hora de criação da conta de banco de dados restaurável regional (formato ISO-8601). |
| exclusãotime |string| A hora em que a conta de banco de dados restaurável regional foi excluída (formato ISO-8601). |
| LocalName |string| O local da conta de restauração regional. |
| regionalDatabaseAccountInstanceId |string| A ID da instância da conta restaurável regional. |

## <a name="next-steps"></a>Próximas etapas

* [Contas de banco de dados restauráveis – listar por local.](restorable-database-accounts-list-by-location.md)
* [Modelo de recurso](continuous-backup-restore-resource-model.md) do modo de backup contínuo.