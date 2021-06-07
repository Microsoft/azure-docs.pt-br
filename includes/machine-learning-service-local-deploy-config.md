---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: 0eeb82245a53c93af75fc3ce3f37cb588295e5b7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102508088"
---
As entradas no documento `deploymentconfig.json` são mapeadas para os parâmetros de [LocalWebservice.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.local.localwebservicedeploymentconfiguration). A seguinte tabela descreve o mapeamento entre as entidades no documento JSON e os parâmetros do método:

| Entidade JSON | Parâmetro de método | Descrição |
| ----- | ----- | ----- |
| `computeType` | NA | O destino de computação. Para destinos locais, o valor precisa ser `local`. |
| `port` | `port` | A porta local na qual expor o ponto de extremidade HTTP do serviço. |

O seguinte JSON é um exemplo de configuração de implantação a ser usada com a CLI:

```json
{
    "computeType": "local",
    "port": 32267
}
```