---
title: Personalizar UDRs (rotas definidas pelo usuário) no AKS (Serviço de Kubernetes do Azure)
description: Saiba como definir uma rota de saída personalizada no AKS (Serviço de Kubernetes do Azure)
services: container-service
ms.topic: article
ms.date: 06/29/2020
ms.openlocfilehash: 72ba90510afb00ee001c97612e88f452039f53a4
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102182121"
---
# <a name="customize-cluster-egress-with-a-user-defined-route"></a>Personalizar a saída do cluster com uma rota de User-Defined

A saída de um cluster AKS pode ser personalizada para se ajustar a cenários específicos. Por padrão, o AKS provisionará uma Load Balancer SKU padrão a ser configurada e usada para saída. No entanto, a configuração padrão poderá não cumprir os requisitos de todos os cenários se IPs públicos não forem permitidos ou saltos adicionais forem necessários para saída.

Este artigo explica como personalizar uma rota de saída do cluster para dar suporte a cenários de rede personalizados, como aqueles que não permitem IPs públicos e requer que o cluster fique atrás de uma NVA (solução de virtualização de rede).

## <a name="prerequisites"></a>Pré-requisitos
* CLI do Azure versão 2.0.81 ou superior
* Versão da API de `2020-01-01` ou superior


## <a name="limitations"></a>Limitações
* Outboundtype só pode ser definido no momento da criação do cluster e não pode ser atualizado posteriormente.
* A configuração `outboundType` requer clusters do AKS com um `vm-set-type` de `VirtualMachineScaleSets` e `load-balancer-sku` de `Standard`.
* A configuração de `outboundType` para um valor de `UDR` requer uma rota definida pelo usuário com conectividade de saída válida para o cluster.
* A configuração de `outboundType` para um valor de `UDR` implica que o IP de origem de entrada roteado para o balanceador de carga pode **não corresponder** ao endereço de destino de saída do cluster.

## <a name="overview-of-outbound-types-in-aks"></a>Visão geral dos tipos de saída no AKS

Um cluster AKS pode ser personalizado com um `outboundType` tipo exclusivo `loadBalancer` ou `userDefinedRouting` .

> [!IMPORTANT]
> O tipo de saída afeta apenas o tráfego de saída do cluster. Para obter mais informações, consulte [configurando controladores de entrada](ingress-basic.md).

> [!NOTE]
> Você pode usar sua própria [tabela de rotas][byo-route-table] com a rede UDR e kubenet. Certifique-se de que a identidade do cluster (entidade de serviço ou identidade gerenciada) tenha permissões de colaborador para a tabela de rotas personalizada.

### <a name="outbound-type-of-loadbalancer"></a>Tipo de saída do balanceador de carga

Se `loadBalancer` for definido, AKs concluirá a configuração a seguir automaticamente. O balanceador de carga é usado para saída por meio de um IP público atribuído por AKS. Um tipo de saída de `loadBalancer` dá suporte aos serviços Kubernetes do tipo `loadBalancer`, que esperam a saída do balanceador de carga criado pelo provedor de recursos do AKS.

A configuração a seguir é feita pelo AKS.
   * Um endereço IP público é provisionado para a saída do cluster.
   * O endereço IP público é atribuído ao recurso do balanceador de carga.
   * Os pools de back-end para o balanceador de carga são configurados para nós de agente no cluster.

Abaixo está uma topologia de rede implantada em clusters do AKS por padrão, que usam um `outboundType` de `loadBalancer`.

![O diagrama mostra a entrada I e saída I P, em que o p de entrada direciona o tráfego para um balanceador de carga, que direciona o tráfego para e de um cluster interno e outro tráfego para a saída I P, que direciona o tráfego para a Internet, M C R, serviços exigidos pelo Azure e o plano de controle K S.](media/egress-outboundtype/outboundtype-lb.png)

### <a name="outbound-type-of-userdefinedrouting"></a>Tipo de saída de userDefinedRouting

> [!NOTE]
> O uso do tipo de saída é um cenário de rede avançado e requer uma configuração de rede adequada.

Se `userDefinedRouting` for definido, AKs não configurará automaticamente os caminhos de saída. A configuração de egresso deve ser feita por você.

O cluster AKS deve ser implantado em uma rede virtual existente com uma sub-rede que foi configurada anteriormente porque, quando não estiver usando a arquitetura do SLB (Load Balancer padrão), você deve estabelecer uma saída explícita. Dessa forma, essa arquitetura requer o envio explícito do tráfego de saída para um dispositivo como um firewall, gateway, proxy ou para permitir que a NAT (conversão de endereços de rede) seja feita por um IP público atribuído ao balanceador de carga padrão ou dispositivo.

#### <a name="load-balancer-creation-with-userdefinedrouting"></a>Criação do balanceador de carga com userDefinedRouting

Clusters AKs com um tipo de saída de UDR recebem um balanceador de carga padrão (SLB) somente quando o primeiro serviço kubernetes do tipo ' Balancer ' é implantado. O balanceador de carga é configurado com um endereço IP público para solicitações de *entrada* e um pool de back-end para solicitações de *entrada* . As regras de entrada são configuradas pelo provedor de nuvem do Azure, mas **nenhuma regra de saída ou endereço IP público de saída** é configurada como resultado de ter um tipo de saída de UDR. Seu UDR ainda será a única fonte para o tráfego de saída.

Os balanceadores de carga do Azure [não incorrem em um encargo até que uma regra seja colocada](https://azure.microsoft.com/pricing/details/load-balancer/).

## <a name="deploy-a-cluster-with-outbound-type-of-udr-and-azure-firewall"></a>Implantar um cluster com o tipo de saída de UDR e o Firewall do Azure

Para ilustrar o aplicativo de um cluster com o tipo de saída usando uma rota definida pelo usuário, um cluster pode ser configurado em uma rede virtual com um firewall do Azure em sua própria sub-rede. Consulte este exemplo no [exemplo restringir o tráfego de egresso com o Firewall do Azure](limit-egress-traffic.md#restrict-egress-traffic-using-azure-firewall).

> [!IMPORTANT]
> O tipo de saída de UDR exige que haja uma rota para 0.0.0.0/0 e destino do próximo salto de NVA (solução de virtualização de rede) na tabela de rotas.
> A tabela de rotas já tem um padrão 0.0.0.0/0 para a Internet, sem um IP público para SNAT apenas adicionar essa rota não fornecerá a saída. O AKS validará que você não cria uma rota 0.0.0.0/0 apontando para a Internet, mas em vez de NVA ou gateway, etc. Ao usar um tipo de saída de UDR, um endereço IP público do balanceador de carga para **solicitações de entrada** não é criado, a menos que um serviço do tipo *Balancer* seja configurado. Um endereço IP público para **solicitações de saída** nunca será criado por AKs se um tipo de saída de UDR for definido.

## <a name="next-steps"></a>Próximas etapas

Confira [Visão geral do UDR de rede do Azure](../virtual-network/virtual-networks-udr-overview.md).

Confira [Como criar, alterar ou exclui uma tabela de rotas](../virtual-network/manage-route-table.md).

<!-- LINKS - internal -->
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[byo-route-table]: configure-kubenet.md#bring-your-own-subnet-and-route-table-with-kubenet
