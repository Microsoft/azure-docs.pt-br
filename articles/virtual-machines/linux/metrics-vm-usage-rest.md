---
title: Obter dados de uso da máquina virtual do Azure usando a API REST
description: Use as APIs REST do Azure para coletar métricas de utilização para uma Máquina Virtual.
author: rloutlaw
ms.service: virtual-machines
ms.subservice: monitoring
ms.custom: REST
ms.topic: how-to
ms.date: 06/13/2018
ms.author: routlaw
ms.openlocfilehash: a7237bfc82a932b774b4b6ef293c242a84fd75af
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100591216"
---
# <a name="get-virtual-machine-usage-metrics-using-the-rest-api"></a>Obter métricas de uso da Máquina Virtual usando a API REST

Este exemplo mostra como recuperar o uso da CPU para uma Máquina Virtual do Linux usando a [API REST do Azure](/rest/api/azure/).

A documentação de referência completa e os exemplos adicionais da API REST estão disponíveis na [referência de REST do Azure Monitor](/rest/api/monitor). 

## <a name="build-the-request"></a>Criar a solicitação

Usar a seguinte solicitação GET para coletar a [Métrica de CPU de porcentagem](../../azure-monitor/essentials/metrics-supported.md#microsoftcomputevirtualmachines) de uma Máquina Virtual

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmname}/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=Percentage%20CPU&timespan=2018-06-05T03:00:00Z/2018-06-07T03:00:00Z
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
| resourceGroupName | O nome do grupo de recursos do Azure associado ao recurso. É possível obter esse valor na API do Azure Resource Manager, na CLI ou no portal. |
| vmname | O nome da Máquina Virtual do Azure. |
| metricnames | Lista separada por vírgulas de [métricas válidas do Load Balancer](../../load-balancer/load-balancer-standard-diagnostics.md). |
| api-version | A versão da API a ser usada para a solicitação.<br /><br /> Este documento abrange a versão da API `2018-01-01`, incluída na URL acima.  |
| TimeSpan | Cadeia de caracteres com o seguinte formato `startDateTime_ISO/endDateTime_ISO` que define o intervalo de tempo das métricas retornadas. Este parâmetro opcional está configurado para retornar dados de um dia no exemplo. |
| &nbsp; | &nbsp; |

### <a name="request-body"></a>Corpo da solicitação

Nenhum corpo de solicitação é necessário para esta operação.

## <a name="handle-the-response"></a>Tratar da resposta

O código de status 200 é retornado quando a lista de valores da métrica é retornada com êxito. Uma lista completa de códigos de erro está disponível na [documentação de referência](/rest/api/monitor/metrics/list#errorresponse).

## <a name="example-response"></a>Exemplo de resposta 

```json
{
    "cost": 0,
    "timespan": "2018-06-08T23:48:10Z/2018-06-09T00:48:10Z",
    "interval": "PT1M",
    "value": [
        {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmname}/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=Percentage%20CPU",
            "type": "Microsoft.Insights/metrics",
            "name": {
                "value": "Percentage CPU",
                "localizedValue": "Percentage CPU"
            },
            "unit": "Percent",
            "timeseries": [
                {
                    "metadatavalues": [],
                    "data": [
                        {
                            "timeStamp": "2018-06-08T23:48:00Z",
                            "average": 0.44
                        },
                        {
                            "timeStamp": "2018-06-08T23:49:00Z",
                            "average": 0.31
                        },
                        {
                            "timeStamp": "2018-06-08T23:50:00Z",
                            "average": 0.29
                        },
                        {
                            "timeStamp": "2018-06-08T23:51:00Z",
                            "average": 0.29
                        },
                        {
                            "timeStamp": "2018-06-08T23:52:00Z",
                            "average": 0.285
                        } ]
                } ]
        } ]
}
```
