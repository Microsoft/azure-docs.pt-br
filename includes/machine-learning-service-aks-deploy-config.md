---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: e17f14d3acf8d74d1715d14fbd914ee536d29931
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102510981"
---
As entradas no documento `deploymentconfig.json` são mapeadas para os parâmetros de [AksWebservice.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration). A seguinte tabela descreve o mapeamento entre as entidades no documento JSON e os parâmetros do método:

| Entidade JSON | Parâmetro de método | Descrição |
| ----- | ----- | ----- |
| `computeType` | NA | O destino de computação. Para o AKS, o valor deve ser `aks`. |
| `autoScaler` | NA | Contém elementos de configuração para dimensionamento automático. Confira a tabela do dimensionador automático. |
| &emsp;&emsp;`autoscaleEnabled` | `autoscale_enabled` | Habilitar o dimensionamento automático para o serviço Web. Se `numReplicas` = `0`, `True`; caso contrário, `False`. |
| &emsp;&emsp;`minReplicas` | `autoscale_min_replicas` | O número mínimo de contêineres a serem usados no dimensionamento automático desse serviço Web. Padrão, `1`. |
| &emsp;&emsp;`maxReplicas` | `autoscale_max_replicas` | O número máximo de contêineres a serem usados no dimensionamento automático desse serviço Web. Padrão, `10`. |
| &emsp;&emsp;`refreshPeriodInSeconds` | `autoscale_refresh_seconds` | A frequência em que o dimensionador automático tenta dimensionar esse serviço Web. Padrão, `1`. |
| &emsp;&emsp;`targetUtilization` | `autoscale_target_utilization` | A utilização de destino (em percentual) que o dimensionador automático deve tentar manter para esse serviço Web. Padrão, `70`. |
| `dataCollection` | NA | Contém elementos de configuração para coleta de dados. |
| &emsp;&emsp;`storageEnabled` | `collect_model_data` | Habilitar a coleta de dados do modelo para o serviço Web. Padrão, `False`. |
| `authEnabled` | `auth_enabled` | Habilitar ou não a autenticação de chave para o serviço Web. `tokenAuthEnabled` e `authEnabled` não podem ser `True`. Padrão, `True`. |
| `tokenAuthEnabled` | `token_auth_enabled` | Habilitar ou não a autenticação de token para o serviço Web. `tokenAuthEnabled` e `authEnabled` não podem ser `True`. Padrão, `False`. |
| `containerResourceRequirements` | NA | Contêiner para as entidades de CPU e de memória. |
| &emsp;&emsp;`cpu` | `cpu_cores` | O número de núcleos de CPU a serem alocados para esse serviço Web. Padrões, `0.1` |
| &emsp;&emsp;`memoryInGB` | `memory_gb` | A quantidade de memória (em GB) a ser alocada para esse serviço Web. Padrão, `0.5` |
| `appInsightsEnabled` | `enable_app_insights` | Habilitar o registro em log do Application Insights para o serviço Web. Padrão, `False`. |
| `scoringTimeoutMs` | `scoring_timeout_ms` | Um tempo limite para impor as chamadas de pontuação ao serviço Web. Padrão, `60000`. |
| `maxConcurrentRequestsPerContainer` | `replica_max_concurrent_requests` | O máximo de solicitações simultâneas por nó para esse serviço Web. Padrão, `1`. |
| `maxQueueWaitMs` | `max_request_wait_time` | O tempo máximo que uma solicitação permanecerá na fila (em milissegundos) antes de um erro 503 ser retornado. Padrão, `500`. |
| `numReplicas` | `num_replicas` | O número de contêineres a serem alocados para esse serviço Web. Sem valor padrão. Se esse parâmetro não for definido, o dimensionador automático será habilitado por padrão. |
| `keys` | NA | Contém elementos de configuração para chaves. |
| &emsp;&emsp;`primaryKey` | `primary_key` | Uma chave de autenticação primária a ser usada para esse serviço Web |
| &emsp;&emsp;`secondaryKey` | `secondary_key` | Uma chave de autenticação secundária a ser usada para esse serviço Web |
| `gpuCores` | `gpu_cores` | O número de núcleos de GPU (réplica por contêiner) a serem alocados para esse serviço Web. O padrão é UTF-1. Dá suporte apenas a valores de números inteiros. |
| `livenessProbeRequirements` | NA | Contém elementos de configuração para requisitos de investigação de atividade. |
| &emsp;&emsp;`periodSeconds` | `period_seconds` | A frequência (em segundos) para realizar a investigação de atividade. O padrão é 10 segundos. O valor mínimo é 1. |
| &emsp;&emsp;`initialDelaySeconds` | `initial_delay_seconds` | Número de segundos depois que o contêiner é iniciado antes que as investigações de atividade sejam iniciadas. O valor padrão é 310 |
| &emsp;&emsp;`timeoutSeconds` | `timeout_seconds` | Número de segundos após os quais a investigação de atividade expira. O valor padrão é 2 segundos. O valor mínimo é 1 |
| &emsp;&emsp;`successThreshold` | `success_threshold` | O mínimo de sucessos consecutivos para que a investigação de atividade seja considerada bem-sucedida após apresentar falha. O valor padrão é 1. O valor mínimo é 1. |
| &emsp;&emsp;`failureThreshold` | `failure_threshold` | Quando um pod for iniciado e a investigação de atividade falhar, o Kubernetes tentará o failureThreshold vezes antes de desistir. O valor padrão é 3. O valor mínimo é 1. |
| `namespace` | `namespace` | O namespace do Kubernetes em que o serviço Web é implantado. Até 63 letras minúsculas alfanuméricas ('a' – 'z', '0' – '9') e hifens ('-'). O primeiro e o último caractere não podem ser hifens. |

O seguinte JSON é um exemplo de configuração de implantação a ser usada com a CLI:

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