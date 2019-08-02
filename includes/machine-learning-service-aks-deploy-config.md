---
author: larryfr
ms.service: machine-learning
ms.topic: include
ms.date: 07/19/2019
ms.author: larryfr
ms.openlocfilehash: 31d20f4824b034230bc941858e8ecb20cc00b6b2
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68348529"
---
As entradas no `deploymentconfig.json` documento são mapeadas para os parâmetros de [AksWebservice. deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py). A tabela a seguir descreve o mapeamento entre as entidades no documento JSON e os parâmetros para o método:

| Entidade JSON | Parâmetro do método | Descrição |
| ----- | ----- | ----- |
| `computeType` | N/A | O destino de computação. Para AKS, o valor deve ser `aks`. |
| `autoScaler` | N/A | Contém elementos de configuração para dimensionamento automático. Consulte a tabela de dimensionamento de escalabilidade. |
| &emsp;&emsp;`autoscaleEnabled` | `autoscale_enabled` | Se é para habilitar o dimensionamento automático para o serviço Web. Se `numReplicas` ,;caso = contrário ,`False`. `0` `True` |
| &emsp;&emsp;`minReplicas` | `autoscale_min_replicas` | O número mínimo de contêineres a ser usado ao dimensionar automaticamente este serviço Web. Padrão, `1`. |
| &emsp;&emsp;`maxReplicas` | `autoscale_max_replicas` | O número máximo de contêineres a serem usados ao dimensionar automaticamente este serviço Web. Padrão, `10`. |
| &emsp;&emsp;`refreshPeriodInSeconds` | `autoscale_refresh_seconds` | Com que frequência o dimensionador de autoescala tenta dimensionar esse serviço Web. Padrão, `1`. |
| &emsp;&emsp;`targetUtilization` | `autoscale_target_utilization` | A utilização de destino (em porcentagem de 100) que o dimensionador deve tentar manter para esse serviço Web. Padrão, `70`. |
| `dataCollection` | N/A | Contém elementos de configuração para a coleta de dados. |
| &emsp;&emsp;`storageEnabled` | `collect_model_data` | Se a coleta de dados do modelo deve ser habilitada para o serviço Web. Padrão, `False`. |
| `authEnabled` | `auth_enabled` | Se a autenticação deve ser habilitada para o serviço Web. Padrão, `True`. |
| `containerResourceRequirements` | N/A | Contêiner para as entidades de CPU e memória. |
| &emsp;&emsp;`cpu` | `cpu_cores` | O número de núcleos de CPU a serem alocados para este serviço Web. Padrões`0.1` |
| &emsp;&emsp;`memoryInGB` | `memory_gb` | A quantidade de memória (em GB) a ser alocada para este serviço Web. Os`0.5` |
| `appInsightsEnabled` | `enable_app_insights` | Se é para habilitar o log de Application Insights para o serviço Web. Padrão, `False`. |
| `scoringTimeoutMs` | `scoring_timeout_ms` | Um tempo limite para impor chamadas de Pontuação para o serviço Web. Padrão, `60000`. |
| `maxConcurrentRequestsPerContainer` | `replica_max_concurrent_requests` | O máximo de solicitações simultâneas por nó para este serviço Web. Padrão, `1`. |
| `maxQueueWaitMs` | `max_request_wait_time` | O tempo máximo que uma solicitação permanecerá na fila três (em milissegundos) antes de um erro 503 ser retornado. Padrão, `500`. |
| `numReplicas` | `num_replicas` | O número de contêineres a serem alocados para este serviço Web. Nenhum valor padrão. Se esse parâmetro não for definido, o dimensionador será habilitado por padrão. |
| `keys` | N/A | Contém elementos de configuração para chaves. |
| &emsp;&emsp;`primaryKey` | `primary_key` | Uma chave de autenticação primária a ser usada para este serviço Web |
| &emsp;&emsp;`secondaryKey` | `secondary_key` | Uma chave de autenticação secundária a ser usada para este serviço Web |
| `gpuCores` | `gpu_cores` | O número de núcleos de GPU a serem alocados para este WebService. O padrão é UTF-1. Dá suporte apenas a valores de números inteiros. |
| `livenessProbeRequirements` | N/A | Contém elementos de configuração para requisitos de investigação de vida. |
| &emsp;&emsp;`periodSeconds` | `period_seconds` | Com que frequência (em segundos) executar a investigação de tempo de vida. O padrão é 10 segundos. O valor mínimo é 1. |
| &emsp;&emsp;`initialDelaySeconds` | `initial_delay_seconds` | Número de segundos depois que o contêiner é iniciado antes que as investigações de tempo sejam iniciadas. O padrão é 310 |
| &emsp;&emsp;`timeoutSeconds` | `timeout_seconds` | Número de segundos após o qual a investigação de vida atinge o tempo limite. O padrão é 2 segundos. O valor mínimo é 1 |
| &emsp;&emsp;`successThreshold` | `success_threshold` | O mínimo de sucessos consecutivos para que a investigação de tempo de vida seja considerada com êxito após ter falhado. O valor padrão é 1. O valor mínimo é 1. |
| &emsp;&emsp;`failureThreshold` | `failure_threshold` | Quando um pod é iniciado e a investigação de tempo falha, o kubernetes tentará limite vezes antes de desistir. O padrão é 3. O valor mínimo é 1. |
| `namespace` | `namespace` | O namespace kubernetes no qual o WebService é implantado. Até 63 letras minúsculas alfanuméricas (' a-z ', ' 0 '-' 9 ') e hífen ('-') caracteres. O primeiro e o último caracteres não podem ser hifens. |

O JSON a seguir é um exemplo de configuração de implantação para uso com a CLI:

```json
{
    "computeType": "aks",
    "autoScaler":
    {
        "autoscaleEnabled": true,
        "minReplicas": 1,
        "maxReplicas": 3,
        "refreshPeriodInSeconds": 1,
        "targetUtilization": 70
    },
    "dataCollection":
    {
        "storageEnabled": true
    },
    "authEnabled": true,
    "containerResourceRequirements":
    {
        "cpu": 0.5,
        "memoryInGB": 1.0
    }
}
```
