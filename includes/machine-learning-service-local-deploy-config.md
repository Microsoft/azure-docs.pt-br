---
author: larryfr
ms.service: machine-learning
ms.topic: include
ms.date: 07/19/2019
ms.author: larryfr
ms.openlocfilehash: e8c0b35f2ac6d3468a5e38a90cc7f4de09d3682b
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68348557"
---
As entradas no `deploymentconfig.json` documento são mapeadas para os parâmetros de [LocalWebservice. deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservicedeploymentconfiguration?view=azure-ml-py). A tabela a seguir descreve o mapeamento entre as entidades no documento JSON e os parâmetros para o método:

| Entidade JSON | Parâmetro do método | Descrição |
| ----- | ----- | ----- |
| `computeType` | N/A | O destino de computação. Para local, o valor deve ser `local`. |
| `port` | `port` | A porta local na qual expor o ponto de extremidade HTTP do serviço. |

O JSON a seguir é um exemplo de configuração de implantação para uso com a CLI:

```json
{
    "computeType": "local",
    "port": 32267
}
```
