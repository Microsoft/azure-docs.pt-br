---
title: Recuperar métricas com a API REST
titleSuffix: Azure Load Balancer
description: Neste artigo, comece a usar as APIs REST do Azure para coletar métricas de integridade e uso para Azure Load Balancer.
services: sql-database
author: asudbring
manager: KumudD
ms.service: load-balancer
ms.custom: REST, seodec18
ms.topic: how-to
ms.date: 11/19/2019
ms.author: allensu
ms.openlocfilehash: 391be596d890e05e6a8fdaf35d2cade371e468d6
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102213175"
---
# <a name="get-load-balancer-usage-metrics-using-the-rest-api"></a>Obter Load Balancer métricas de uso usando a API REST

Coletar o número de bytes processados por um [Standard Load Balancer](./load-balancer-overview.md) para um intervalo de tempo usando a [API REST do Azure](/rest/api/azure/).

A documentação de referência completa e os exemplos adicionais da API REST estão disponíveis na [referência de REST do Azure Monitor](/rest/api/monitor). 

## <a name="build-the-request"></a>Criar a solicitação

Use a seguinte solicitação GET para coletar a [métrica ByteCount](./load-balancer-standard-diagnostics.md#multi-dimensional-metrics) de um Standard Load Balancer. 

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/loadBalancers/{loadBalancerName}/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=ByteCount&timespan=2018-06-05T03:00:00Z/2018-06-07T03:00:00Z
```

### <a name="request-headers"></a>Cabeçalhos de solicitação

Os cabeçalhos a seguir são necessários: 

|Cabeçalho da solicitação|Descrição|  
|--------------------|-----------------|  
|*Tipo de Conteúdo:*|Obrigatórios. Defina como `application/json`.|  
|*Autorização:*|Obrigatórios. Defina como um [token de acesso](/rest/api/azure/#authorization-code-grant-interactive-clients) `Bearer` válido. |  

### <a name="uri-parameters"></a>Parâmetros do URI

| Name | Descrição |
| :--- | :---------- |
| subscriptionId | A ID de assinatura que identifica uma assinatura do Azure. Se você tiver várias assinaturas, consulte [trabalhando com várias assinaturas](/cli/azure/manage-azure-subscriptions-azure-cli). |
| resourceGroupName | O nome do grupo de recursos que contém o recurso. Você pode obter esse valor por meio da API do Azure Resource Manager, da CLI ou do portal. |
| loadBalancerName | O nome do Azure Load Balancer. |
| nomes de métrica | Lista separada por vírgulas de [métricas válidas do Load Balancer](./load-balancer-standard-diagnostics.md). |
| api-version | A versão da API a ser usada para a solicitação.<br /><br /> Este documento abrange a versão da API `2018-01-01`, incluída na URL acima.  |
| TimeSpan | O período de tempo da consulta. É uma cadeia de caracteres com o formato a seguir `startDateTime_ISO/endDateTime_ISO` . Este parâmetro opcional está configurado para retornar dados de um dia no exemplo. |
| &nbsp; | &nbsp; |

### <a name="request-body"></a>Corpo da solicitação

Nenhum corpo de solicitação é necessário para esta operação.

## <a name="handle-the-response"></a>Tratar da resposta

O código de status 200 é retornado quando a lista de valores da métrica é retornada com êxito. Uma lista completa de códigos de erro está disponível na [documentação de referência](/rest/api/monitor/metrics/list#errorresponse).

## <a name="example-response"></a>Exemplo de resposta 

```json
{
    "cost": 0,
    "timespan": "2018-06-05T03:00:00Z/2018-06-07T03:00:00Z",
    "interval": "PT1M",
    "value": [
        {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/loadBalancers/{loadBalancerName}/providers/Microsoft.Insights/metrics/ByteCount",
            "type": "Microsoft.Insights/metrics",
            "name": {
                "value": "ByteCount",
                "localizedValue": "Byte Count"
            },
            "unit": "Count",
            "timeseries": [
                {
                    "metadatavalues": [],
                    "data": [
                        {
                            "timeStamp": "2018-06-06T17:24:00Z",
                            "total": 1067921034.0
                        },
                        {
                            "timeStamp": "2018-06-06T17:25:00Z",
                            "total": 0.0
                        },
                        {
                            "timeStamp": "2018-06-06T17:26:00Z",
                            "total": 3781344.0
                        },
                    ]
                }
            ]
        }
    ],
    "namespace": "Microsoft.Network/loadBalancers",
    "resourceregion": "eastus"
}
```