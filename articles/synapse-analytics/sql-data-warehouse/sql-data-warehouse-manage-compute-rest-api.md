---
title: Pausar, retomar, dimensionar com APIs REST para o pool SQL dedicado (anteriormente conhecido como SQL DW)
description: Gerencie a potência de computação para o pool de SQL dedicado (anteriormente conhecido como SQL DW) no Azure Synapse Analytics por meio de APIs REST.
services: synapse-analytics
author: antvgski
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/29/2019
ms.author: anvang
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: c04f61aaef5f5072ce0fb39ff111ba07ee151700
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100375894"
---
# <a name="rest-apis-for-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>APIs REST para o pool do SQL dedicado (anteriormente conhecido como SQL DW) no Azure Synapse Analytics

APIs REST para gerenciar a computação para o pool de SQL dedicado (anteriormente conhecido como SQL DW) no Azure Synapse Analytics.

## <a name="scale-compute"></a>Computação de escala

Para alterar as unidades do data warehouse, use a API REST [Criar ou Atualizar Banco de Dados](/rest/api/sql/databases/createorupdate). O exemplo a seguir configura as unidades do data warehouse para DW1000 para o banco de dados MySQLDW, que está hospedado no servidor MyServer. O servidor está em um grupo de recursos do Azure chamado ResourceGroup1.

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2020-08-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    location: "West Central US",
    "sku": {
    "name": "DW200c"
    }
}
```

## <a name="pause-compute"></a>Pausar computação

Para pausar um banco de dados, use a API REST [Pausar o banco de dados](/rest/api/sql/databases/pause). O exemplo a seguir pausa um banco de dados chamado Database02 hospedado em um servidor chamado Server01. O servidor está em um grupo de recursos do Azure chamado ResourceGroup1.

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/pause?api-version=2020-08-01-preview HTTP/1.1
```

## <a name="resume-compute"></a>Retomar a computação

Para iniciar um banco de dados, use a API REST [Retornar o banco de dados](/rest/api/sql/databases/resume). O exemplo a seguir inicia um banco de dados chamado Database02 hospedado em um servidor chamado Server01. O servidor está em um grupo de recursos do Azure chamado ResourceGroup1.

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/resume?api-version=2020-08-01-preview HTTP/1.1
```

## <a name="check-database-state"></a>Verificar estado do banco de dados

> [!NOTE]
> Atualmente, verificar o estado do banco de dados pode retornar ONLINE enquanto o banco de dados estiver concluindo o fluxo de trabalho online, resultando em erros de conexão. Talvez seja necessário adicionar um atraso de 2 a 3 minutos no código do aplicativo se você estiver usando essa chamada à API para disparar tentativas de conexão.

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Sql/servers/{serverName}/databases/{databaseName}?api-version=2020-08-01-preview
```

## <a name="get-maintenance-schedule"></a>Obter o agendamento de manutenção

Verifique o agendamento de manutenção que foi definido para um pool SQL dedicado (anteriormente conhecido como SQL DW).

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/maintenanceWindows/current?maintenanceWindowName=current&api-version=2017-10-01-preview HTTP/1.1

```

## <a name="set-maintenance-schedule"></a>Definir o agendamento de manutenção

Para definir e atualizar um agendamento de manutenção em um pool SQL dedicado existente (anteriormente conhecido como SQL DW).

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/maintenanceWindows/current?maintenanceWindowName=current&api-version=2017-10-01-preview HTTP/1.1

{
    "properties": {
        "timeRanges": [
                {
                                "dayOfWeek": "Saturday",
                                "startTime": "00:00",
                                "duration": "08:00",
                },
                {
                                "dayOfWeek": "Wednesday",
                                "startTime": "00:00",
                                "duration": "08:00",
                }
                ]
    }
}

```

## <a name="next-steps"></a>Próximas etapas

Para saber mais, veja [Gerenciar computação](sql-data-warehouse-manage-compute-overview.md).
