---
title: Obter dados de uso da Máquina Virtual do Azure usando a API REST | Microsoft Docs
description: Use as APIs REST do Azure para coletar métricas de utilização para uma Máquina Virtual.
services: virtual-machines
author: rloutlaw
ms.reviewer: routlaw
manager: gwallace
ms.service: load-balancer
ms.custom: REST
ms.topic: article
ms.date: 06/13/2018
ms.author: routlaw
ms.openlocfilehash: 53e93d20f4a8c4877374d8242521ca97d481e15d
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67667447"
---
# <a name="get-virtual-machine-usage-metrics-using-the-rest-api"></a>Obter métricas de uso da Máquina Virtual usando a API REST

Este exemplo mostra como recuperar o uso da CPU para uma [Máquina Virtual do Linux](https://docs.microsoft.com/azure/virtual-machines/linux/monitor) usando a [API REST do Azure](/rest/api/azure/).

A documentação de referência completa e os exemplos adicionais da API REST estão disponíveis na [referência de REST do Azure Monitor](/rest/api/monitor). 

## <a name="build-the-request"></a>Criar a solicitação

Usar a seguinte solicitação GET para coletar a [Métrica de CPU de porcentagem](/azure/monitoring-and-diagnostics/monitoring-supported-metrics#microsoftcomputevirtualmachines) de uma Máquina Virtual

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmname}/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=Percentage%20CPU&timespan=2018-06-05T03:00:00Z/2018-06-07T03:00:00Z
```

### <a name="request-headers"></a>Cabeçalhos da solicitação

Os cabeçalhos a seguir são necessários: 

|Cabeçalho da solicitação|DESCRIÇÃO|  
|--------------------|-----------------|  
|*Content-Type:*|Obrigatório. Defina como `application/json`.|  
|*Authorization:*|Obrigatório. Defina como um `Bearer` [token de acesso](/rest/api/azure/#authorization-code-grant-interactive-clients) válido. |  

### <a name="uri-parameters"></a>Parâmetros de URI

| Nome | DESCRIÇÃO |
| :--- | :---------- |
| subscriptionId | A ID de assinatura que identifica uma assinatura do Azure. Se você tiver várias assinaturas, consulte [Trabalhando com várias assinaturas](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest). |
| resourceGroupName | O nome do grupo de recursos do Azure associado ao recurso. É possível obter esse valor na API do Azure Resource Manager, na CLI ou no portal. |
| vmname | O nome da Máquina Virtual do Azure. |
| metricnames | Lista separada por vírgulas de [métricas válidas do Load Balancer](/azure/load-balancer/load-balancer-standard-diagnostics). |
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
