---
title: Recuperar métricas com a API REST
titlesuffix: Azure Load Balancer
description: Use as APIs REST do Azure para coletar métricas de integridade e de utilização do Load Balancer em um determinado intervalo de datas e horários.
services: sql-database
author: KumudD
ms.reviewer: routlaw
manager: jeconnoc
ms.service: load-balancer
ms.custom: REST, seodec18
ms.topic: article
ms.date: 06/06/2017
ms.author: KumudD
ms.openlocfilehash: 9f5206ef5348ee8fd7b3fe981a9cfe4afc1367fb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60734534"
---
# <a name="get-load-balancer-utilization-metrics-using-the-rest-api"></a>Obter as métricas de utilização do Load Balancer usando a API REST

Estas instruções mostram como coletar o número de bytes processados por um [Standard Load Balancer](/azure/load-balancer/load-balancer-standard-overview) em um intervalo de tempo usando a [API REST do Azure](/rest/api/azure/).

A documentação de referência completa e os exemplos adicionais da API REST estão disponíveis na [referência de REST do Azure Monitor](/rest/api/monitor). 

## <a name="build-the-request"></a>Criar a solicitação

Use a seguinte solicitação GET para coletar a [métrica ByteCount](/azure/load-balancer/load-balancer-standard-diagnostics#multi-dimensional-metrics) de um Standard Load Balancer. 

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/loadBalancers/{loadBalancerName}/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=ByteCount&timespan=2018-06-05T03:00:00Z/2018-06-07T03:00:00Z
```

### <a name="request-headers"></a>Cabeçalhos da solicitação

Os cabeçalhos a seguir são necessários: 

|Cabeçalho da solicitação|DESCRIÇÃO|  
|--------------------|-----------------|  
|*Tipo de Conteúdo:*|Obrigatório. Defina como `application/json`.|  
|*Autorização:*|Obrigatório. Defina como um `Bearer` [token de acesso](/rest/api/azure/#authorization-code-grant-interactive-clients) válido. |  

### <a name="uri-parameters"></a>Parâmetros de URI

| NOME | DESCRIÇÃO |
| :--- | :---------- |
| subscriptionId | A ID de assinatura que identifica uma assinatura do Azure. Se você tiver várias assinaturas, consulte [Trabalhando com várias assinaturas](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest). |
| resourceGroupName | O nome do grupo de recursos que contém o recurso. Você pode obter esse valor por meio da API do Azure Resource Manager, da CLI ou do portal. |
| loadBalancerName | O nome do Azure Load Balancer. |
| metricnames | Lista separada por vírgulas de [métricas válidas do Load Balancer](/azure/load-balancer/load-balancer-standard-diagnostics). |
| api-version | A versão da API a ser usada para a solicitação.<br /><br /> Este documento abrange a versão da API `2018-01-01`, incluída na URL acima.  |
| TimeSpan | O período de tempo da consulta. É uma cadeia de caracteres com o seguinte formato `startDateTime_ISO/endDateTime_ISO`. Este parâmetro opcional está configurado para retornar dados de um dia no exemplo. |
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
