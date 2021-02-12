---
title: Usar o Firewall do Azure para proteger implantações do AKS (Serviço de Kubernetes do Azure)
description: Saiba como usar o Firewall do Azure para proteger implantações do AKS (serviço kubernetes do Azure)
author: vhorne
ms.service: firewall
services: firewall
ms.topic: how-to
ms.date: 01/11/2021
ms.author: victorh
ms.openlocfilehash: 51af9ff4972f5edef02426a6e81e8582123c9a7a
ms.sourcegitcommit: 48e5379c373f8bd98bc6de439482248cd07ae883
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98107847"
---
# <a name="use-azure-firewall-to-protect-azure-kubernetes-service-aks-deployments"></a>Usar o Firewall do Azure para proteger implantações do AKS (Serviço de Kubernetes do Azure)

O AKS (serviço kubernetes do Azure) oferece um cluster kubernetes gerenciado no Azure. Ele reduz a complexidade e a sobrecarga operacional do gerenciamento de kubernetes descarregando grande parte dessa responsabilidade no Azure. O AKS lida com tarefas críticas, como monitoramento de integridade e manutenção para você e fornece um cluster de nível empresarial e seguro com governança facilitada.

O kubernetes orquestra clusters de máquinas virtuais e agenda os contêineres para serem executados nessas máquinas virtuais com base nos recursos de computação disponíveis e nos requisitos de recursos de cada contêiner. Os contêineres são agrupados em pods, a unidade operacional básica para kubernetes e esses pods são dimensionados para o estado desejado.

Para fins operacionais e de gerenciamento, os nós em um cluster do AKS precisam acessar portas e nomes de domínio totalmente qualificados (FQDNs) específicos. Essas ações podem ser comunicadas com o servidor de API ou para baixar e depois instalar os componentes principais do cluster e atualizações de segurança de nó do Kubernetes. O Firewall do Azure pode ajudá-lo a bloquear seu ambiente e filtrar o tráfego de saída.

Siga as diretrizes neste artigo para fornecer proteção adicional para o cluster kubernetes do Azure usando o Firewall do Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Um cluster do Azure kubernetes implantado com o aplicativo em execução.

   Para obter mais informações, consulte [tutorial: implantar um cluster do AKS (serviço kubernetes do Azure)](../aks/tutorial-kubernetes-deploy-cluster.md) e [tutorial: executar aplicativos no AKs (serviço kubernetes do Azure)](../aks/tutorial-kubernetes-deploy-application.md).


## <a name="securing-aks"></a>Protegendo AKS

O Firewall do Azure fornece uma marca AKS FQDN para simplificar a configuração. Use as etapas a seguir para permitir o tráfego de saída da plataforma AKS:

- Quando você usa o Firewall do Azure para restringir o tráfego de saída e criar uma rota definida pelo usuário (UDR) para direcionar todo o tráfego de saída, certifique-se de criar uma regra DNAT apropriada no firewall para permitir o tráfego de entrada corretamente. 

   Usar o Firewall do Azure com um UDR interrompe a configuração de entrada devido ao roteamento assimétrico. O problema ocorre se a sub-rede AKS tem uma rota padrão que vai para o endereço IP privado do firewall, mas você está usando um balanceador de carga público. Por exemplo, serviço de entrada ou kubernetes do tipo *Balancer*.

   Nesse caso, o tráfego de entrada do balanceador de carga é recebido por meio de seu endereço IP público, mas o caminho de retorno passa pelo endereço IP privado do firewall. Como o firewall tem monitoramento de estado, ele descarta o pacote de retorno porque o firewall não está ciente de uma sessão estabelecida. Para saber como integrar o Firewall do Azure com o balanceador de carga de serviço ou de entrada, confira [Integrar o Firewall do Azure com o Azure Standard Load Balancer](integrate-lb.md).
- Crie uma coleção de regras de aplicativo e adicione uma regra para habilitar a marca de FQDN *AzureKubernetesService* . O intervalo de endereços IP de origem é a rede virtual do pool de hosts, o protocolo é https e o destino é AzureKubernetesService.
- As seguintes regras de rede/portas de saída são necessárias para um cluster do AKS:

   - Porta TCP 443
   - TCP [*IPAddrOfYourAPIServer*]: 443 será necessário se você tiver um aplicativo que precisa se comunicar com o servidor de API. Essa alteração pode ser definida depois que o cluster for criado.
   - A porta TCP 9000 e a porta UDP 1194 para que o Pod frontal do túnel se comunique com a extremidade do túnel no servidor de API.

      Para ser mais específico, consulte os endereços na tabela a seguir:

   | Ponto de extremidade de destino                                                             | Protocolo | Porta    | Use  |
   |----------------------------------------------------------------------------------|----------|---------|------|
   | **`*:1194`** <br/> *Or* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:1194`** <br/> *Or* <br/> [CIDRs regionais](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:1194`** <br/> *Or* <br/> **`APIServerIP:1194`** `(only known after cluster creation)`  | UDP           | 1194      | Para uma comunicação segura em túnel entre os nós e o plano de controle. |
   | **`*:9000`** <br/> *Or* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:9000`** <br/> *Or* <br/> [CIDRs regionais](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:9000`** <br/> *Or* <br/> **`APIServerIP:9000`** `(only known after cluster creation)`  | TCP           | 9000      | Para uma comunicação segura em túnel entre os nós e o plano de controle. |

   - A porta UDP 123 para sincronização de tempo do protocolo de tempo de rede (NTP) (nós do Linux).
   - A porta UDP 53 para DNS também será necessária se você tiver pods que acessam diretamente o servidor de API.

   Para obter mais informações, consulte [controlar o tráfego de saída para nós de cluster no serviço de kubernetes do Azure (AKs)](../aks/limit-egress-traffic.md).
- Configure as marcas de serviço de armazenamento e AzureMonitor. Azure Monitor recebe dados do log Analytics.

   Você também pode permitir a URL do seu espaço de trabalho individualmente: `<worksapceguid>.ods.opinsights.azure.com` , e `<worksapceguid>.oms.opinsights.azure.com` . Você pode resolver isso de uma das seguintes maneiras:

    - Permitir acesso HTTPS de sua sub-rede do pool de hosts para `*. ods.opinsights.azure.com` , e `*.oms. opinsights.azure.com` . Esses FQDNs curinga habilitam o acesso necessário, mas são menos restritivos.
    - Use a seguinte consulta do log Analytics para listar os FQDNs exatos necessários e, em seguida, permiti-los explicitamente em suas regras de aplicativo de firewall:
   ```
   AzureDiagnostics 
   | where Category == "AzureFirewallApplicationRule" 
   | search "Allow" 
   | search "*. ods.opinsights.azure.com" or "*.oms. opinsights.azure.com"
   | parse msg_s with Protocol " request from " SourceIP ":" SourcePort:int " to " FQDN ":" * 
   | project TimeGenerated,Protocol,FQDN 
   ```


## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o serviço kubernetes do Azure, consulte [kubernetes Core Concepts for Azure kubernetes Service (AKs)](../aks/concepts-clusters-workloads.md).
