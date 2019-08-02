---
author: ggailey777
ms.service: billing
ms.topic: include
ms.date: 05/09/2019
ms.author: glenga
ms.openlocfilehash: f2470f937d2d812bf79cea3c23d89a50717a5a92
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67277100"
---
| Resource | [Plano de consumo](../articles/azure-functions/functions-scale.md#consumption-plan) | [Plano Premium](../articles/azure-functions/functions-scale.md#premium-plan) | [Plano de serviço de aplicativo](../articles/azure-functions/functions-scale.md#app-service-plan)<sup>1</sup> |
| --- | --- | --- | --- |
| Expansão | Controlado por evento | Controlado por evento | [Manual/autoscale](../articles/app-service/web-sites-scale.md) | 
| Máximo de instâncias | 200 | 20 | 10-20 |
|Padrão [duração de tempo limite](../articles/azure-functions/functions-scale.md#timeout) (min) |5 | 30 |30<sup>2</sup> |
|Máx [duração de tempo limite](../articles/azure-functions/functions-scale.md#timeout) (min) |10 | não associado | unbounded<sup>3</sup> |
| Máximo de conexões de saída (por instância) | 600 Active Directory (total de 1200) | não associado | não associado |
| Tamanho máximo de solicitação (MB)<sup>4</sup> | 100 | 100 | 100 |
| Comprimento máximo de cadeia de caracteres de consulta<sup>4</sup> | 4096 | 4096 | 4096 |
| Comprimento máximo de URL de solicitação<sup>4</sup> | 8192 | 8192 | 8192 |
| [ACU](../articles/virtual-machines/windows/acu.md) por instância | 100 | 210-840 | 100-840 |
| Memória máxima (GB por instância) | 1.5 | 3.5-14 | 1.75-14 |
| Aplicativos de funções por um plano |100 |100 |unbounded<sup>5</sup> |
| [Planos do Serviço de Aplicativo](../articles/app-service/overview-hosting-plans.md) | 100 por [região](https://azure.microsoft.com/global-infrastructure/regions/) |100 por grupo de recursos |100 por grupo de recursos |
| Armazenamento<sup>6</sup> |1 GB |250 GB |50 A 1000 GB |
| Domínios personalizados por aplicativo</a> |500<sup>7</sup> |500 |500 |
| domínio personalizado [Suporte a SSL](../articles/app-service/app-service-web-tutorial-custom-ssl.md) |não associado conexão SSL SNI incluída | unbounded SSL SNI e 1 conexões IP SSL incluídas |unbounded SSL SNI e 1 conexões IP SSL incluídas | 

<sup>1</sup> para obter limites específicos para as várias opções de plano de serviço de aplicativo, consulte a [limites de plano do serviço de aplicativo](../articles/azure-subscription-service-limits.md#app-service-limits).  
<sup>2</sup> por padrão, o tempo limite para o tempo de execução 1.x de funções em um plano do serviço de aplicativo é ilimitado.  
<sup>3</sup> requer que o plano de serviço de aplicativo a ser definido como [Always On](../articles/azure-functions/functions-scale.md#always-on). Padrão de pagamento [taxas](https://azure.microsoft.com/pricing/details/app-service/).  
<sup>4</sup> esses limites são [definida no host](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/web.config).  
<sup>5</sup> o número real de aplicativos de funções que você pode hospedar depende da atividade dos aplicativos, o tamanho das instâncias de máquina e a utilização de recursos correspondente.
<sup>6</sup> o limite de armazenamento é o tamanho total do conteúdo no armazenamento temporário em todos os aplicativos no mesmo plano do serviço de aplicativo. Plano de consumo usa arquivos do Azure para armazenamento temporário.  
<sup>7</sup> quando seu aplicativo de funções está hospedado em um [plano de consumo](../articles/azure-functions/functions-scale.md#consumption-plan), há suporte apenas a opção de CNAME. Para aplicativos de funções em um [plano Premium](../articles/azure-functions/functions-scale.md#premium-plan) ou um [plano de serviço de aplicativo](../articles/azure-functions/functions-scale.md#app-service-plan), você pode mapear um domínio personalizado usando um CNAME ou um registro.
