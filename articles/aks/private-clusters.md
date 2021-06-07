---
title: Criar um cluster privado do Serviço de Kubernetes do Azure
description: Saiba como criar um cluster privado do AKS (Serviço de Kubernetes do Azure)
services: container-service
ms.topic: article
ms.date: 3/5/2021
ms.openlocfilehash: 21d839df04c868d2c21932f96a6b72a32b0404e5
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104771848"
---
# <a name="create-a-private-azure-kubernetes-service-cluster"></a>Criar um cluster privado do Serviço de Kubernetes do Azure

Em um cluster privado, o plano de controle ou o servidor de API tem endereços IP internos que são definidos no documento [alocação de endereço RFC1918 para a Internet privada](https://tools.ietf.org/html/rfc1918) . Usando um cluster privado, você pode garantir que o tráfego de rede entre o servidor de API e os pools de nós permaneça apenas na rede privada.

O plano de controle ou o servidor de API está em uma assinatura do Azure gerenciada pelo AKS (Serviço de Kubernetes do Azure). O cluster ou o pool de nós de um cliente está na assinatura do cliente. O servidor e o cluster ou o pool de nós podem se comunicar entre si por meio do [serviço de Link Privado do Azure][private-link-service] na rede virtual do servidor de API e um ponto de extremidade privado que é exposto na sub-rede do cluster AKS do cliente.

## <a name="region-availability"></a>Disponibilidade de região

O cluster privado está disponível em regiões públicas, Azure governamental e regiões da 21Vianet do Azure na China em que há [suporte para AKs](https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service).

> [!NOTE]
> Os sites do Azure governamental têm suporte, no entanto US Gov Texas não tem suporte no momento devido ao suporte de link privado ausente.

## <a name="prerequisites"></a>Pré-requisitos

* A CLI do Azure versão 2.2.0 ou posterior
* O serviço de vínculo privado tem suporte somente no Azure Load Balancer padrão. Não há suporte para Azure Load Balancer básico.  
* Para usar um servidor DNS personalizado, adicione o IP do DNS do Azure 168.63.129.16 como o servidor DNS upstream no servidor DNS personalizado.

## <a name="create-a-private-aks-cluster"></a>Criar um cluster de AKS privado

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos ou use um grupo de recursos existente para seu cluster de AKS.

```azurecli-interactive
az group create -l westus -n MyResourceGroup
```

### <a name="default-basic-networking"></a>Rede básica padrão 

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster  
```
Em que `--enable-private-cluster` é um sinalizador obrigatório para um cluster privado. 

### <a name="advanced-networking"></a>Rede avançada  

```azurecli-interactive
az aks create \
    --resource-group <private-cluster-resource-group> \
    --name <private-cluster-name> \
    --load-balancer-sku standard \
    --enable-private-cluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24 
```
Em que `--enable-private-cluster` é um sinalizador obrigatório para um cluster privado. 

> [!NOTE]
> Se o endereço de ponte do Docker (172.17.0.1/16) entrar em conflito com o CIDR da sub-rede, altere o endereço da ponte do Docker adequadamente.

## <a name="configure-private-dns-zone"></a>Configurar zona de DNS privado 

Os parâmetros a seguir podem ser aproveitados para configurar a zona DNS privado.

- "System" é o valor padrão. Se o argumento--Private-DNS-Zone for omitido, AKS criará uma zona de DNS privado no grupo de recursos do nó.
- "None" significa que o AKS não criará uma zona de DNS privado.  Isso exige que você traga seu próprio servidor DNS e configure a resolução DNS para o FQDN privado.  Se você não configurar a resolução DNS, o DNS só poderá ser resolvido dentro dos nós de agente e causará problemas de cluster após a implantação. 
- "CUSTOM_PRIVATE_DNS_ZONE_RESOURCE_ID" exige que você crie uma zona de DNS privado nesse formato para a nuvem global do Azure: `privatelink.<region>.azmk8s.io` . Você precisará da ID do recurso que DNS privado zona avançar.  Além disso, você precisará de uma identidade atribuída pelo usuário ou uma entidade de serviço com pelo menos as `private dns zone contributor` `vnet contributor` funções e.
- o "FQDN-subdomínio" pode ser utilizado com "CUSTOM_PRIVATE_DNS_ZONE_RESOURCE_ID" apenas para fornecer recursos de subdomínio para `privatelink.<region>.azmk8s.io`

### <a name="prerequisites"></a>Pré-requisitos

* A versão de visualização do AKS 0.5.3 ou posterior
* A versão de API 2020-11-01 ou posterior

### <a name="create-a-private-aks-cluster-with-private-dns-zone-preview"></a>Criar um cluster AKS privado com a zona de DNS privado (visualização)

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster --enable-managed-identity --assign-identity <ResourceId> --private-dns-zone [system|none]
```

### <a name="create-a-private-aks-cluster-with-a-custom-private-dns-zone-preview"></a>Criar um cluster AKS privado com uma zona de DNS privado personalizada (visualização)

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster --enable-managed-identity --assign-identity <ResourceId> --private-dns-zone <custom private dns zone ResourceId> --fqdn-subdomain <subdomain-name>
```
## <a name="options-for-connecting-to-the-private-cluster"></a>Opções para se conectar ao cluster privado

O ponto de extremidade do servidor de API não tem nenhum endereço IP público. Para gerenciar o servidor de API, você precisará usar uma VM que tenha acesso à VNet (rede virtual) do Azure do cluster AKS. Há várias opções para estabelecer a conectividade de rede com o cluster privado.

* Crie uma VM na mesma VNet (rede virtual) do Azure que o cluster AKS.
* Use uma VM em uma rede separada e configure o [emparelhamento de rede virtual][virtual-network-peering].  Consulte a seção abaixo para obter mais informações sobre esta opção.
* Adicionar uma conexão de [Rota Expressa ou VPN][express-route-or-VPN].

Criar uma VM na mesma VNET que o cluster AKS é a opção mais fácil.  A rota expressa e as VPNs adicionam custos e exigem complexidade adicional de rede.  O emparelhamento de rede virtual exige que você planeje os intervalos de CIDR de rede para garantir que não haja intervalos sobrepostos.

## <a name="virtual-network-peering"></a>Emparelhamento de rede virtual

Conforme mencionado, o emparelhamento de rede virtual é uma maneira de acessar seu cluster privado. Para usar o emparelhamento de rede virtual, você precisa configurar um vínculo entre a rede virtual e a zona DNS privada.
    
1. Vá para o grupo de recursos de nó no portal do Azure.  
2. Selecione a zona DNS privada.   
3. No painel esquerdo, selecione o link **Rede virtual**.  
4. Crie um novo link para adicionar a rede virtual da VM à zona DNS privada. Leva alguns minutos para que o link da zona DNS fique disponível.  
5. No portal do Azure, navegue até o grupo de recursos que contém a rede virtual do cluster.  
6. No painel à direita, selecione a rede virtual. O nome da rede virtual está no formato *aks-vnet-\** .  
7. No painel esquerdo, selecione **Emparelhamentos**.  
8. Selecione **Adicionar**, adicione a rede virtual da VM e crie o emparelhamento.  
9. Vá para a rede virtual onde você tem a VM, selecione **Emparelhamentos**, selecione a rede virtual AKS e crie o emparelhamento. Se os intervalos de endereços na rede virtual AKS e na rede virtual da VM entrarem em conflito, o emparelhamento falhará. Para saber mais, confira [Emparelhamento de rede virtual][virtual-network-peering].

## <a name="hub-and-spoke-with-custom-dns"></a>Hub e spoke com DNS personalizado

As [arquiteturas de hub e spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) geralmente são usadas para implantar redes no Azure. Em muitas dessas implantações, as configurações de DNS nas VNets spoke são definidas para fazer referência a um encaminhador DNS central para permitir a resolução de DNS local e baseada no Azure. Ao implantar um cluster AKS em um ambiente de rede como esse, há algumas considerações especiais que devem ser levadas em conta.

![Hub de cluster privado e spoke](media/private-clusters/aks-private-hub-spoke.png)

1. Por padrão, quando um cluster privado é provisionado, um ponto de extremidade privado (1) e uma zona DNS privada (2) são criados no grupo de recursos gerenciado por cluster. O cluster usa um registro A na zona privada para resolver o IP do ponto de extremidade privado para comunicação com o servidor de API.

2. A zona DNS privada é vinculada somente à VNet à qual os nós de cluster estão anexados (3). Isso significa que o ponto de extremidade privado só pode ser resolvido por hosts nessa VNet vinculada. Em cenários em que nenhum DNS personalizado está configurado na VNet (padrão), isso funciona sem emitir como hosts Point em 168.63.129.16 para DNS que pode resolver registros na zona DNS privada devido ao link.

3. Em cenários onde a VNet que contém o cluster tem configurações de DNS personalizadas (4), a implantação de cluster falhará, a menos que a zona DNS privada esteja vinculada à VNet que contém os solucionadores de DNS personalizados (5). Esse link pode ser criado manualmente após a criação da zona privada durante o provisionamento do cluster ou via automação na detecção da criação da zona usando mecanismos de implantação baseados em eventos (por exemplo, grade de eventos do Azure e Azure Functions).

> [!NOTE]
> Se você estiver usando [traga sua própria tabela de rotas com kubenet](./configure-kubenet.md#bring-your-own-subnet-and-route-table-with-kubenet) e traga seu próprio DNS com cluster privado, a criação do cluster falhará. Você precisará associar a [RouteTable](./configure-kubenet.md#bring-your-own-subnet-and-route-table-with-kubenet) no grupo de recursos do nó à sub-rede após a criação do cluster falhar, a fim de tornar a criação bem-sucedida.

## <a name="limitations"></a>Limitações 
* Os intervalos autorizados por IP não podem ser aplicados ao ponto de extremidade do servidor de API privada, eles se aplicam somente ao servidor de API pública
* As [limitações do serviço de Link Privado do Azure][private-link-service] se aplicam a clusters privados.
* Não há suporte para agentes hospedados pela Microsoft do Azure DevOps com clusters privados. Considere o uso de [agentes auto-hospedados](/azure/devops/pipelines/agents/agents?tabs=browser). 
* Para clientes que precisam habilitar o Registro de Contêiner do Azure para trabalhar com AKS privado, a rede virtual do registro de contêiner deve ser emparelhada com a rede virtual do cluster do agente.
* Não há suporte para converter clusters AKS existentes em clusters privados
* Excluir ou modificar o ponto de extremidade privado na sub-rede do cliente fará com que o cluster pare de funcionar. 
* Depois que os clientes tiverem atualizado o registro a em seus próprios servidores DNS, esses pods ainda resolverão o FQDN do apiserver para o IP mais antigo após a migração até que eles sejam reiniciados. Os clientes precisam reiniciar hostNetwork pods e pods padrão de DNSPolicy após a migração do plano de controle.
* No caso de manutenção no plano de controle, o [IP do AKS](./limit-egress-traffic.md) pode ser alterado. Nesse caso, você deve atualizar o registro a que aponta para o IP privado do servidor de API no seu servidor DNS personalizado e reiniciar os pods ou as implantações personalizadas usando hostNetwork.

<!-- LINKS - internal -->
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[private-link-service]: ../private-link/private-link-service-overview.md#limitations
[virtual-network-peering]: ../virtual-network/virtual-network-peering-overview.md
[azure-bastion]: ../bastion/tutorial-create-host-portal.md
[express-route-or-vpn]: ../expressroute/expressroute-about-virtual-network-gateways.md
[devops-agents]: /azure/devops/pipelines/agents/agents
[availability-zones]: availability-zones.md
