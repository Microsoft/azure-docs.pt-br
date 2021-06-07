---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/04/2020
ms.author: glenga
ms.openlocfilehash: 91f72117fdbcdbeda1d906a9760243e66404920c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105104279"
---
| Recurso |[Plano de Consumo](../articles/azure-functions/consumption-plan.md)|[Plano Premium](../articles/azure-functions/functions-premium-plan.md)|[Plano dedicado](../articles/azure-functions/dedicated-plan.md)|[ASE](../articles/app-service/environment/intro.md)| [Kubernetes](../articles/aks/quotas-skus-regions.md) |
| --- | --- | --- | --- | --- | --- |
|[Duração padrão do tempo limite](../articles/azure-functions/functions-scale.md#timeout) (min) |5 | 30 |30<sup>1</sup> | 30 | 30 |
|[Duração máxima do tempo limite](../articles/azure-functions/functions-scale.md#timeout) (min) |10 | não associado<sup>7</sup> | não associado<sup>2</sup> | não associado | não associado |
| Máximo de conexões de saída (por instância) | 600 ativos (1.200 no total) | não associado | não associado | não associado | não associado |
| Tamanho máximo da solicitação (MB)<sup>3</sup> | 100 | 100 | 100 | 100 | Depende do cluster |
| Comprimento máximo da cadeia de caracteres de consulta<sup>3</sup> | 4096 | 4096 | 4096 | 4096 | Depende do cluster |
| Comprimento máximo da URL de solicitação<sup>3</sup> | 8192 | 8192 | 8192 | 8192 | Depende do cluster |
|[ACU](../articles/virtual-machines/acu.md) por instância | 100 | 210-840 | 100-840 | 210-250<sup>8</sup> | [Preços do AKS](https://azure.microsoft.com/pricing/details/container-service/) |
| Memória máxima (GB por instância) | 1.5 | 3,5-14 | 1,75-14 | 3,5-14 | Todos os nós são compatíveis |
| Contagem de instâncias máx. | 200 | 100<sup>9</sup> | varia de acordo com a SKU<sup>10</sup> | 100<sup>10</sup> | Depende do cluster |   
| Aplicativos de funções por plano |100 |100 |não associado<sup>4</sup> | não associado | não associado |
| [Planos do Serviço de Aplicativo](../articles/app-service/overview-hosting-plans.md) | 100 por [região](https://azure.microsoft.com/global-infrastructure/regions/) |100 por grupo de recursos |100 por grupo de recursos | - | - |
| Armazenamento<sup>5</sup> |5 TB |250 GB |50-1.000 GB | 1 TB | n/a |
| Domínios personalizados por aplicativo</a> |500<sup>6</sup> |500 |500 | 500 | n/a |
| domínio personalizado [Suporte a SSL](../articles/app-service/configure-ssl-bindings.md) |conexão SSL SNI não associada incluída | conexões SSL SNI e 1 IP SSL não associadas incluídas |conexões SSL SNI e 1 IP SSL não associadas incluídas | conexões SSL SNI e 1 IP SSL não associadas incluídas | n/a |

<sup>1</sup> Por padrão, o tempo limite do runtime do Functions 1.x em um plano do Serviço de Aplicativo não é associado.  
<sup>2</sup> Requer que o plano do Serviço de Aplicativo seja definido como [Always On](../articles/azure-functions/dedicated-plan.md#always-on). Pagamento com [taxas](https://azure.microsoft.com/pricing/details/app-service/) padrão.  
<sup>3</sup> Esses limites são [definidos no host](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/web.config).  
<sup>4</sup> O número real de aplicativos de funções que você pode hospedar dependerá da atividade dos aplicativos, do tamanho das instâncias do computador e da utilização de recursos correspondente.  
<sup>5</sup> O limite de armazenamento é o tamanho total do conteúdo no armazenamento temporário em todos os aplicativos no mesmo plano do Serviço de Aplicativo. O plano de consumo usa os Arquivos do Azure para obter um armazenamento temporário.  
<sup>6</sup> Quando seu aplicativo de funções estiver hospedado em um [Plano de consumo](../articles/azure-functions/consumption-plan.md), somente a opção CNAME será compatível. Para obter aplicativos de funções em um [plano Premium](../articles/azure-functions/functions-premium-plan.md) ou [plano do Serviço de Aplicativo](../articles/azure-functions/dedicated-plan.md), você poderá mapear um domínio personalizado usando um CNAME ou registro A.  
<sup>7</sup> Garantido por até 60 minutos.  
<sup>8</sup> As funções de trabalho são funções que hospedam aplicativos de clientes. As funções de trabalho estão disponíveis em três tamanhos fixos: Um vCPU/3,5 GB de RAM, dois vCPUs/7 GB de RAM e quatro vCPUs/14 GB de RAM.   
<sup>9</sup> No momento, a execução no Linux em um plano Premium tem um limite de 20 instâncias.  
<sup>10</sup> Confira [Limites do Serviço de Aplicativo](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits) para saber detalhes.