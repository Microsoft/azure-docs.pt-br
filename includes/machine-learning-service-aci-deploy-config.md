---
author: larryfr
ms.service: machine-learning
ms.topic: include
ms.date: 07/19/2019
ms.author: larryfr
ms.openlocfilehash: fd9527714b87cc35fcfc61f4858cbacee7fa95f5
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68348534"
---
As entradas no `deploymentconfig.json` documento são mapeadas para os parâmetros de [AciWebservice. deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciservicedeploymentconfiguration?view=azure-ml-py). A tabela a seguir descreve o mapeamento entre as entidades no documento JSON e os parâmetros para o método:

| Entidade JSON | Parâmetro do método | Descrição |
| ----- | ----- | ----- |
| `computeType` | N/A | O destino de computação. Para ACI, o valor deve ser `ACI`. |
| `containerResourceRequirements` | N/A | Contêiner para as entidades de CPU e memória. |
| &emsp;&emsp;`cpu` | `cpu_cores` | O número de núcleos de CPU a serem alocados. Padrões`0.1` |
| &emsp;&emsp;`memoryInGB` | `memory_gb` | A quantidade de memória (em GB) a ser alocada para este serviço Web. Os`0.5` |
| `location` | `location` | A região do Azure para a qual implantar este WebService. Se não for especificado, o local do espaço de trabalho será usado. Mais detalhes sobre as regiões disponíveis podem ser encontrados aqui: [Regiões ACI](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=container-instances) |
| `authEnabled` | `auth_enabled` | Se a autenticação deve ser habilitada para este WebService. O padrão é false |
| `sslEnabled` | `ssl_enabled` | Se o SSL deve ser habilitado para este WebService. O padrão é false. |
| `appInsightsEnabled` | `enable_app_insights` | Se deseja habilitar AppInsights para este WebService. O padrão é false |
| `sslCertificate` | `ssl_cert_pem_file` | O arquivo de certificado necessário se o SSL estiver habilitado |
| `sslKey` | `ssl_key_pem_file` | O arquivo de chave necessário se o SSL estiver habilitado |
| `cname` | `ssl_cname` | O CNAME para se o SSL estiver habilitado |
| `dnsNameLabel` | `dns_name_label` | O rótulo de nome DNS para o ponto de extremidade de pontuação. Se não for especificado, um rótulo de nome DNS exclusivo será gerado para o ponto de extremidade de pontuação. |

O JSON a seguir é um exemplo de configuração de implantação para uso com a CLI:

```json
{
    "computeType": "aci",
    "containerResourceRequirements":
    {
        "cpu": 0.5,
        "memoryInGB": 1.0
    },
    "authEnabled": true,
    "sslEnabled": false,
    "appInsightsEnabled": false
}
```