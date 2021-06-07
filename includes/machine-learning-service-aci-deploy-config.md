---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: 4983c1e1e7f235fa7a5b748a0ce5b1c79176c849
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102510656"
---
As entradas no documento `deploymentconfig.json` são mapeadas para os parâmetros de [AciWebservice.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.aci.aciservicedeploymentconfiguration). A seguinte tabela descreve o mapeamento entre as entidades no documento JSON e os parâmetros do método:

| Entidade JSON | Parâmetro de método | Descrição |
| ----- | ----- | ----- |
| `computeType` | NA | O destino de computação. Para o ACI, o valor precisa ser `ACI`. |
| `containerResourceRequirements` | NA | Contêiner para as entidades de CPU e de memória. |
| &emsp;&emsp;`cpu` | `cpu_cores` | O número de núcleos de CPU a serem alocados. Padrões, `0.1` |
| &emsp;&emsp;`memoryInGB` | `memory_gb` | A quantidade de memória (em GB) a ser alocada para esse serviço Web. Padrão, `0.5` |
| `location` | `location` | A região do Azure na qual implantar esse serviço Web. Se não for especificado, o local do workspace será usado. Mais detalhes sobre as regiões disponíveis podem ser encontrados aqui: [Regiões do ACI](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=container-instances) |
| `authEnabled` | `auth_enabled` | Se a autenticação deve ser habilitada para esse serviço Web. Usa False como padrão |
| `sslEnabled` | `ssl_enabled` | Se o SSL deve ser habilitado para esse serviço Web. Usa False como padrão. |
| `appInsightsEnabled` | `enable_app_insights` | Se o AppInsights deve ser habilitado para esse serviço Web. Usa False como padrão |
| `sslCertificate` | `ssl_cert_pem_file` | O arquivo de certificado necessário se o SSL estiver habilitado |
| `sslKey` | `ssl_key_pem_file` | O arquivo de chave necessário se o SSL estiver habilitado |
| `cname` | `ssl_cname` | O CNAME a ser usado se o SSL estiver habilitado |
| `dnsNameLabel` | `dns_name_label` | O rótulo de nome DNS do ponto de extremidade de pontuação. Se ele não for especificado, um rótulo de nome DNS exclusivo será gerado para o ponto de extremidade de pontuação. |

O seguinte JSON é um exemplo de configuração de implantação a ser usada com a CLI:

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