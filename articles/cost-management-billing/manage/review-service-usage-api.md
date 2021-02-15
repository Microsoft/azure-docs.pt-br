---
title: Examinar o uso de recursos de serviço do Azure com a API REST
description: Aprenda a usar as APIs REST do Azure para revisar o uso de recursos de serviço do Azure. Crie um relatório de gerenciamento de custos e filtre por tipos de recursos específicos.
author: lleonard-msft
ms.service: cost-management-billing
ms.subservice: enterprise
ms.topic: reference
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 018e61fbc74091fa25211b8a25ce067862a0cc7d
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92132390"
---
# <a name="review-azure-resource-usage-using-the-rest-api"></a>Revisar o uso de recursos de serviço do Azure usando a API REST

As APIs de Gerenciamento de Custos do Azure o ajudam a revisar e gerenciar o consumo de recursos do Azure.

Neste artigo, você aprenderá a criar um relatório diário que irá gerar um documento de valores separados por vírgula com suas informações de uso por hora e, em seguida, como usar filtros para personalizar o relatório para que você possa consultar o uso de máquinas virtuais, bancos de dados e recursos marcados em um grupo de recursos do Azure.

>[!NOTE]
> A API de Gerenciamento de Custos está em versão prévia privada.

## <a name="create-a-basic-cost-management-report"></a>Criar um relatório de gerenciamento de custos básico

Use a operação `reports` na API de Gerenciamento de Custos para definir como o relatório de custo é gerado e onde os relatórios serão publicados.

```http
https://management.azure.com/subscriptions/{subscriptionGuid}/providers/Microsoft.CostManagement/reports/{reportName}?api-version=2018-09-01-preview
Content-Type: application/json   
Authorization: Bearer
```

O parâmetro `{subscriptionGuid}` é obrigatório e deve conter uma ID da assinatura que pode ser lida usando as credenciais fornecidas no token de API. 

O parâmetro `{reportName}` especifica o nome do relatório. Para obter uma lista de nomes de relatório, você pode usar a operação Reports_List para obter uma lista: `/subscriptions/{subscriptionId}/providers/Microsoft.CostManagement/reports`. Veja a saída de exemplo no [GitHub](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/cost-management/resource-manager/Microsoft.CostManagement/preview/2018-08-01-preview/examples/ReportList.json).

Os cabeçalhos a seguir são necessários:

|Cabeçalho da solicitação|Descrição|  
|--------------------|-----------------|  
|*Tipo de Conteúdo:*| Obrigatórios. Defina como `application/json`. |  
|*Autorização:*| Obrigatórios. Defina como um token `Bearer` válido. |

Configure os parâmetros do relatório no corpo da solicitação HTTP. No exemplo a seguir, o relatório é definido para ser gerado todos os dias quando ativo, é um arquivo CSV gravado em um contêiner de blob de Armazenamento do Microsoft Azure e contém informações de custo por hora para todos os recursos no grupo de recursos `westus`.

```json
{
    "properties": {
        "schedule": {
            "status": "Inactive",
            "recurrence": "Daily",
            "recurrencePeriod": {
                "from": "2018-08-21",
                "to": "2019-10-31"
            }
        },
        "deliveryInfo": {
            "destination": {
                "resourceId": "/subscriptions/{subscriptionGuid}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}",
                "container": "MyReportContainer",
                "rootFolderPath": "MyScheduleTest"
            }
        },
        "format": "Csv",
        "definition": {
            "type": "Usage",
            "timeframe": "MonthToDate",
            "dataSet": {
                "granularity": "Hourly",
                "filter": {
                    "dimensions": {
                        "name": "ResourceLocation",
                        "operator": "In",
                        "values": [
                            "westus"
                        ]
                    }
                }
            }
        }
    }
}
```

O

## <a name="filtering-reports"></a>Filtrando relatórios

As seções `filter` e `dimensions` do corpo da solicitação durante a criação de um relatório permitem que você se concentre nos custos para tipos específicos de recursos. O corpo da solicitação anterior mostra como filtrar por todos os recursos em uma região.

### <a name="get-all-compute-usage"></a>Obter todo o uso de computação

Use a dimensão `ResourceType` para relatar os custos de máquina virtual do Azure em sua assinatura em todas as regiões.

```json
"filter": {
    "dimensions": {
        "name": "ResourceType",
        "operator": "In",
        "values": [
                "Microsoft.ClassicCompute/virtualMachines",
                "Microsoft.Compute/virtualMachines"
        ]
    }
}
```

### <a name="get-all-database-usage"></a>Obter todo o uso do banco de dados

Use a dimensão `ResourceType` para relatar os custos de Banco de Dados SQL do Azure em sua assinatura em todas as regiões.

```json
"filter": {
    "dimensions": {
        "name": "ResourceType",
        "operator": "In",
        "values": [
                "Microsoft.Sql/servers"
        ]
    }
}
```

### <a name="report-on-specific-instances"></a>Relatar em instâncias específicas

A `Resource` dimensão permite relatar os custos de recursos específicos.

```json
"filter": {
    "dimensions": {
        "name": "Resource",
        "operator": "In",
        "values": [
            "subscriptions/{subscriptionGuid}/resourceGroups/{resourceGroup}/providers/Microsoft.ClassicCompute/virtualMachines/{ResourceName}"
        ]
    }
}
```

### <a name="changing-timeframes"></a>Alterando períodos de tempo

Configure a definição de `timeframe` como `Custom` para definir um período fora das opções internas de semana e mês até a data.

```json
"timeframe": "Custom",
"timePeriod": {
    "from": "2017-12-31T00:00:00.000Z",
    "to": "2018-12-30T00:00:00.000Z"
}
```

## <a name="next-steps"></a>Próximas etapas
- [Iniciar com a API REST do Azure](/rest/api/azure/)