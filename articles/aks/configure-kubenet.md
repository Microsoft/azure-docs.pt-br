---
title: Configurar a rede kubenet no Serviço de Kubernetes do Azure (AKS)
description: Saiba como configurar a rede kubenet (básica) no Serviço de Kubernetes do Azure (AKS) para implantar um cluster do AKS em uma rede virtual e sub-rede existentes.
services: container-service
ms.topic: article
ms.date: 06/02/2020
ms.reviewer: nieberts, jomore
ms.openlocfilehash: a32b06163f446fe0df7f1f1581d741d889cf8e9f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104583506"
---
# <a name="use-kubenet-networking-with-your-own-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Use a rede do kubenet com seus próprios intervalos de endereços IP no Serviço de Kubernetes do Azure (AKS)

Por padrão, os clusters do AKS usam o [kubenet][kubenet], e uma rede virtual e uma sub-rede do Azure são criadas para você. Com o *kubenet*, os nós obtêm um endereço IP de uma sub-rede da rede virtual do Azure. Os pods recebem um endereço IP de um espaço de endereço logicamente diferente para a sub-rede da rede virtual do Azure dos nós. A NAT (Conversão de Endereços de Rede) é configurada para que os pods possam alcançar recursos na rede virtual do Azure. O endereço IP de origem do tráfego é configurado via NAT como o endereço IP primário do nó. Essa abordagem reduz muito o número de endereços IP que você precisa reservar no espaço de rede para uso dos pods.

Com a [CNI (Interface de Rede de Contêiner) do Azure][cni-networking], cada pod obtém um endereço IP da sub-rede e pode ser acessado diretamente. Esses endereços IP devem ser exclusivos em todo o seu espaço de rede e devem ser planejados com antecedência. Cada nó tem um parâmetro de configuração para o número máximo de pods aos quais ele dá suporte. O número equivalente de endereços IP por nó é então reservado com antecedência para esse nó. Essa abordagem exige mais planejamento e, muitas vezes, leva à exaustão do endereço IP ou à necessidade de recriar os clusters em uma sub-rede maior conforme as demandas de aplicativo aumentam. Você pode configurar o máximo de pods implantar para um nó em tempo de criação de cluster ou ao criar novos pools de nós. Se você não especificar maxPods ao criar novos pools de nós, você receberá um valor padrão de 110 para kubenet.

Este artigo mostra como usar a rede *kubenet* para criar e usar uma sub-rede da rede virtual de um cluster do AKS. Para saber mais sobre opções de rede e considerações, confira [Conceitos de rede para o Kubernetes e o AKS][aks-network-concepts].

## <a name="prerequisites"></a>Pré-requisitos

* A rede virtual do cluster do AKS deve permitir conectividade com a Internet de saída.
* Não crie mais de um cluster do AKS na mesma sub-rede.
* Os clusters AKs podem não usar `169.254.0.0/16` ,, `172.30.0.0/16` `172.31.0.0/16` ou `192.0.2.0/24` para o intervalo de endereços do serviço kubernetes, intervalo de endereços Pod ou intervalo de endereços de rede virtual do cluster.
* A identidade do cluster usada pelo cluster AKS deve ter pelo menos a função de [colaborador de rede](../role-based-access-control/built-in-roles.md#network-contributor) na sub-rede em sua rede virtual. Você também deve ter as permissões apropriadas, como o proprietário da assinatura, para criar uma identidade de cluster e atribuir permissões a ela. Se você quiser definir uma [função personalizada](../role-based-access-control/custom-roles.md) em vez de usar a função de Colaborador de Rede interna, as seguintes permissões serão necessárias:
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`

> [!WARNING]
> Para usar pools de nós do Windows Server, você deve usar o Azure CNI. O uso de kubenet como o modelo de rede não está disponível para contêineres do Windows Server.

## <a name="before-you-begin"></a>Antes de começar

Você precisa do CLI do Azure versão 2.0.65 ou posterior instalado e configurado. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure][install-azure-cli].

## <a name="overview-of-kubenet-networking-with-your-own-subnet"></a>Visão geral da rede do kubenet com sua própria sub-rede

Em muitos ambientes, você definiu redes virtuais e sub-redes com intervalos de endereços IP alocados. Esses recursos de rede virtual são usados para dar suporte a vários serviços e aplicativos. Para fornecer conectividade de rede, os clusters do AKS podem usar o *kubenet* (rede básica) ou o CNI do Azure (*rede avançada*).

Com o *kubenet*, somente os nós recebem um endereço IP na sub-rede da rede virtual. Os pods não podem se comunicar diretamente entre si. Em vez disso, o roteamento definido pelo usuário (UDR) e o encaminhamento de IP são usados para conectividade entre os pods nos nós. Por padrão, a configuração de encaminhamento de UDRs e IP é criada e mantida pelo serviço AKS, mas você tem a opção de [trazer sua própria tabela de rotas para o gerenciamento de rotas personalizado][byo-subnet-route-table]. Você também pode implantar pods atrás de um serviço que recebe um endereço IP atribuído e o tráfego de balanceamento de carga do aplicativo. O diagrama a seguir mostra como os nós do AKS recebem um endereço IP na sub-rede da rede virtual, mas não nos pods:

![Modelo de rede Kubenet com um cluster do AKS](media/use-kubenet/kubenet-overview.png)

O Azure dá suporte a um máximo de 400 rotas em um UDR, portanto, você não pode ter um cluster do AKS com mais de 400 nós. Não há suporte para [nós virtuais][virtual-nodes] AKs e políticas de rede do Azure com *kubenet*.  Você pode usar [as políticas de rede do Calico][calico-network-policies], pois elas têm suporte com o kubenet.

Com o *CNI do Azure*, cada pod recebe um endereço IP na sub-rede IP e pode se comunicar diretamente com outros pods e serviços. Seus clusters podem ser tão grandes quanto o intervalo de endereços IP especificado. No entanto, o intervalo de endereços IP deve ser planejado com antecedência e todos os endereços IP são consumidos pelos nós AKS com base no número máximo de pods a que eles podem dar suporte. Recursos e cenários de rede avançados, como [nós virtuais][virtual-nodes] ou políticas de rede (Azure ou Calico), têm suporte com o *CNI do Azure*.

### <a name="limitations--considerations-for-kubenet"></a>Limitações & considerações para kubenet

* Um salto adicional é necessário no design de kubenet, o que adiciona uma latência secundária à comunicação Pod.
* As tabelas de rotas e as rotas definidas pelo usuário são necessárias para usar kubenet, o que adiciona complexidade às operações.
* O endereçamento de Pod direto não tem suporte para kubenet devido ao design de kubenet.
* Ao contrário dos clusters do Azure CNI, vários clusters kubenet não podem compartilhar uma sub-rede.
* Os recursos **sem suporte no kubenet** incluem:
   * [Políticas de rede do Azure](use-network-policies.md#create-an-aks-cluster-and-enable-network-policy), mas as políticas de rede Calico têm suporte no kubenet
   * [Pools de nós do Windows](./windows-faq.md)
   * [Complemento de nós virtuais](virtual-nodes.md#network-requirements)

### <a name="ip-address-availability-and-exhaustion"></a>Disponibilidade e esgotamento de endereços IP

Com o *CNI do Azure*, um problema comum é que o intervalo de endereços IP atribuído é muito pequeno para então adicionar nós adicionais ao dimensionar ou atualizar um cluster. A equipe de rede também pode não conseguir emitir um intervalo de endereços IP grande o suficiente para dar suporte às demandas de aplicativos esperadas.

Como um compromisso, você pode criar um cluster do AKS que usa o *kubenet* e se conectar a uma sub-rede de rede virtual existente. Essa abordagem permite que os nós recebam endereços IP definidos, sem a necessidade de reservar um grande número de endereços IP antecipadamente para todos os possíveis pods que podem ser executados no cluster.

Com o *kubenet*, você pode usar um intervalo de endereços IP muito menor e ser capaz de oferecer suporte a grandes clusters e demandas de aplicativos. Por exemplo, mesmo com um intervalo de endereços IP */27* em sua sub-rede, você pode executar um cluster de nó de 20-25 com espaço suficiente para dimensionar ou atualizar. Esse tamanho de cluster daria suporte a até *2.200-2.750* pods (com um máximo padrão de 110 pods por nó). O número máximo de pods por nó que você pode configurar com *kubenet* em AKS é 110.

Os seguintes cálculos básicos comparam a diferença nos modelos de rede:

- **kubenet** – um simples intervalo de endereços IP de */24* pode dar suporte a até *251* nós no cluster (cada sub-rede de rede virtual do Azure reserva os três primeiros endereços IP para operações de gerenciamento)
  - Esta contagem de nós pode dar suporte a até *27.610* pods (com um máximo padrão de 110 pods por nó com *kubenet*)
- **CNI do Azure** – esse mesmo intervalo de sub-rede */24* básica só podia dar suporte a um máximo de *8* nós no cluster
  - Esta contagem de nós pode dar suporte a até *240* pods (com um máximo padrão de 30 pods por nó com o *CNI do Azure*)

> [!NOTE]
> Esses máximos não levam em conta operações de atualização ou dimensionamento. Na prática, não é possível executar o número máximo de nós a que o intervalo de endereços IP de sub-rede dá suporte. É preciso deixar alguns endereços IP disponíveis para uso durante a escala de operações de atualização.

### <a name="virtual-network-peering-and-expressroute-connections"></a>Emparelhamento de rede virtual e conexões do ExpressRoute

Para fornecer conectividade local, as abordagens de rede *kubenet* e *CNI do Azure* podem usar o [emparelhamento de rede virtual do Azure][vnet-peering] ou as [conexões do ExpressRoute][express-route]. Planeje seus intervalos de endereços IP com cuidado para evitar sobreposição e roteamento de tráfego incorreto. Por exemplo, muitas redes locais usam um intervalo de endereços *10.0.0.0/8* que é anunciado pela conexão ExpressRoute. É recomendável criar seus clusters do AKS em sub-redes da rede virtual do Azure fora desse intervalo de endereços, como *172.16.0.0/16*.

### <a name="choose-a-network-model-to-use"></a>Escolher um modelo de rede para usar

A escolha de qual plug-in de rede usar para seu cluster do AKS geralmente é um equilíbrio entre a flexibilidade e as necessidades de configuração avançada. As seguintes considerações ajudam a delinear quando cada modelo de rede pode ser o mais adequado.

Use o *kubenet* quando:

- Você tiver espaço de endereço IP limitado.
- A maior parte da comunicação do pod estiver dentro do cluster.
- Você não precisa de recursos avançados do AKS, como nós virtuais ou política de rede do Azure.  Use [as políticas de rede do Calico][calico-network-policies].

Use o *CNI do Azure* quando:

- Você tem espaço de endereço IP disponível.
- A maior parte da comunicação do pod destina-se a recursos fora do cluster.
- Você não deseja gerenciar rotas definidas pelo usuário para conectividade de Pod.
- Você precisa de recursos avançados do AKS, como nós virtuais ou política de rede do Azure.  Use [as políticas de rede do Calico][calico-network-policies].

Para obter mais informações para ajudá-lo a decidir qual modelo de rede usar, consulte [comparar modelos de rede e seu escopo de suporte][network-comparisons].

## <a name="create-a-virtual-network-and-subnet"></a>Criar a rede virtual e a sub-rede

Para começar a usar a *kubenet* e sua própria sub-rede de rede virtual, primeiro crie um grupo de recursos usando o comando [az group create][az-group-create]. O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Se você não tiver uma rede virtual e uma sub-rede para usar, crie esses recursos de rede usando o comando [az network vnet create][az-network-vnet-create]. No exemplo a seguir, a rede virtual é nomeada *myVnet* com o prefixo de endereço de *192.168.0.0/16*. Uma sub-rede é criada chamada *myAKSSubnet* com o prefixo de endereço *192.168.1.0/24*.

```azurecli-interactive
az network vnet create \
    --resource-group myResourceGroup \
    --name myAKSVnet \
    --address-prefixes 192.168.0.0/16 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 192.168.1.0/24
```

## <a name="create-a-service-principal-and-assign-permissions"></a>Criar uma entidade de serviço e atribuir permissões

Para permitir a interação de um cluster AKS com outros recursos do Azure, usamos uma entidade de serviço do Azure Active Directory. A entidade de serviço precisa ter permissões para gerenciar a rede virtual e a sub-rede que usam os nós do AKS. Para criar uma entidade de serviço, use o comando [az ad sp create-for-rbac][az-ad-sp-create-for-rbac]:

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

A saída de exemplo a seguir mostra a ID do aplicativo e a senha da entidade de serviço. Esses valores são usados em etapas adicionais para atribuir uma função à entidade de serviço e, em seguida, criar o cluster do AKS:

```output
{
  "appId": "476b3636-5eda-4c0e-9751-849e70b5cfad",
  "displayName": "azure-cli-2019-01-09-22-29-24",
  "name": "http://azure-cli-2019-01-09-22-29-24",
  "password": "a1024cd7-af7b-469f-8fd7-b293ecbb174e",
  "tenant": "72f998bf-85f1-41cf-92ab-2e7cd014db46"
}
```

Para atribuir as delegações corretas nas etapas restantes, use os comandos [az network vnet show][az-network-vnet-show] e [az network vnet subnet show][az-network-vnet-subnet-show] para obter as IDs de recursos necessários. Essas IDs de recursos são armazenadas como variáveis e referenciadas nas etapas restantes:

```azurecli-interactive
VNET_ID=$(az network vnet show --resource-group myResourceGroup --name myAKSVnet --query id -o tsv)
SUBNET_ID=$(az network vnet subnet show --resource-group myResourceGroup --vnet-name myAKSVnet --name myAKSSubnet --query id -o tsv)
```

Agora, atribua a entidade de serviço para suas permissões de *colaborador de rede* de cluster AKs na rede virtual usando o comando [AZ role Assignment Create][az-role-assignment-create] . Forneça seu próprio *\<appId>* , conforme mostrado na saída do comando anterior para criar a entidade de serviço:

```azurecli-interactive
az role assignment create --assignee <appId> --scope $VNET_ID --role "Network Contributor"
```

## <a name="create-an-aks-cluster-in-the-virtual-network"></a>Criar um cluster do AKS na rede virtual

Agora você criou uma rede virtual e uma sub-rede, e criou e atribuiu permissões para que uma entidade de serviço use esses recursos de rede. Agora crie um cluster do AKS na sua rede virtual e sub-rede usando o comando [az aks create][az-aks-create]. Defina sua própria entidade de serviço *\<appId>* e *\<password>* , conforme mostrado na saída do comando anterior, para criar a entidade de serviço.

Os seguintes intervalos de endereços IP também são definidos como parte do processo de criação de cluster:

* O *--service-cidr* é usado para designar um endereço IP aos serviços internos no cluster do AKS. Esse intervalo de endereços IP deve ser um espaço de endereço que não esteja em uso em outro lugar em seu ambiente de rede, incluindo qualquer intervalo de rede local se você se conectar ou se planeja se conectar a suas redes virtuais do Azure usando a rota expressa ou uma conexão VPN site a site.

* O endereço *--dns-service-ip* deve ser o endereço *.10* do seu intervalo de endereço IP de serviço.

* *--pod-cidr* deve ser um espaço de endereço grande que não esteja em uso em outro lugar no ambiente de rede. Esse intervalo inclui todos os intervalos de rede local se você se conectar ou se pretende conectar-se a suas redes virtuais do Azure usando a rota expressa ou uma conexão VPN site a site.
    * Esse intervalo de endereços deve ser grande o suficiente para acomodar o número de nós que você espera ampliar. Se precisar de mais endereços para nós adicionais, não é possível alterar esse intervalo de endereços depois que o cluster for implantado.
    * O intervalo de endereços IP de Pod é usado para atribuir um espaço de endereço */24* a cada nó no cluster. No exemplo a seguir, o *--Pod-CIDR* de *10.244.0.0/16* atribui o primeiro nó *10.244.0.0/24*, o segundo nó *10.244.1.0/24* e o terceiro nó *10.244.2.0/24*.
    * À medida que o cluster é dimensionado ou atualizado, a plataforma do Azure continua atribuindo um intervalo de endereços IP do pod a cada novo nó.
    
* O *--Docker-Bridge-address* permite que os nós AKs se comuniquem com a plataforma de gerenciamento subjacente. Esse endereço IP não deve estar dentro do intervalo de endereços IP da rede virtual do cluster e não deve se sobrepor a outros intervalos de endereços em uso na rede.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 3 \
    --network-plugin kubenet \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --pod-cidr 10.244.0.0/16 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal <appId> \
    --client-secret <password>
```

> [!Note]
> Se você quiser habilitar um cluster AKS para incluir uma [política de rede Calico][calico-network-policies] , poderá usar o comando a seguir.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 3 \
    --network-plugin kubenet --network-policy calico \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --pod-cidr 10.244.0.0/16 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal <appId> \
    --client-secret <password>
```

Quando você cria um cluster AKS, um grupo de segurança de rede e uma tabela de rotas são criados automaticamente. Esses recursos de rede são gerenciados pelo plano de controle AKS. O grupo de segurança de rede é associado automaticamente às NICs virtuais em seus nós. A tabela de rotas é associada automaticamente à sub-rede da rede virtual. As regras do grupo de segurança de rede e as tabelas de rotas são atualizadas automaticamente conforme você cria e expõe serviços.

## <a name="bring-your-own-subnet-and-route-table-with-kubenet"></a>Traga sua própria sub-rede e tabela de rotas com kubenet

Com o kubenet, uma tabela de rotas deve existir em suas sub-redes de cluster. O AKS dá suporte ao trazer sua própria sub-rede e tabela de rotas existentes.

Se a sua sub-rede personalizada não contiver uma tabela de rotas, o AKS criará uma para você e adicionará regras a ela em todo o ciclo de vida do cluster. Se sua sub-rede personalizada contiver uma tabela de rotas quando você criar o cluster, o AKS confirmará a tabela de rotas existente durante operações de cluster e adicionará/atualizará as regras de acordo com as operações do provedor de nuvem.

> [!WARNING]
> As regras personalizadas podem ser adicionadas à tabela de rotas personalizada e atualizadas. No entanto, as regras são adicionadas pelo provedor de nuvem kubernetes que não devem ser atualizadas ou removidas. As regras como 0.0.0.0/0 sempre devem existir em uma determinada tabela de rotas e mapear para o destino do seu gateway de Internet, como um NVA ou outro gateway de saída. Tenha cuidado ao atualizar regras que apenas suas regras personalizadas estão sendo modificadas.

Saiba mais sobre como configurar uma [tabela de rotas personalizada][custom-route-table].

A rede Kubenet requer regras de tabela de rotas organizadas para rotear solicitações com êxito. Devido a esse design, as tabelas de rotas devem ser cuidadosamente mantidas para cada cluster que depende dele. Vários clusters não podem compartilhar uma tabela de rotas porque os CIDRs de diferentes clusters podem se sobrepor, o que causa um roteamento inesperado e quebrado. Ao configurar vários clusters na mesma rede virtual ou dedicar uma rede virtual a cada cluster, certifique-se de que as seguintes limitações sejam consideradas.

Limitações:

* As permissões devem ser atribuídas antes da criação do cluster, verifique se você está usando uma entidade de serviço com permissões de gravação para sua sub-rede personalizada e tabela de rotas personalizada.
* Uma tabela de rotas personalizada deve ser associada à sub-rede antes de criar o cluster AKS.
* O recurso de tabela de rotas associado não pode ser atualizado após a criação do cluster. Embora o recurso de tabela de rotas não possa ser atualizado, as regras personalizadas podem ser modificadas na tabela de rotas.
* Cada cluster AKS deve usar uma única tabela de rotas exclusiva para todas as sub-redes associadas ao cluster. Não é possível reutilizar uma tabela de rotas com vários clusters devido à possibilidade de sobreposição de CIDRs e de regras de roteamento conflitantes.

Depois de criar uma tabela de rotas personalizada e associá-la à sua sub-rede em sua rede virtual, você pode criar um novo cluster AKS que usa sua tabela de rotas.
Você precisa usar a ID de sub-rede para onde planeja implantar o cluster AKS. Essa sub-rede também deve ser associada à sua tabela de rotas personalizada.

```azurecli-interactive
# Find your subnet ID
az network vnet subnet list --resource-group
                            --vnet-name
                            [--subscription]
```

```azurecli-interactive
# Create a kubernetes cluster with with a custom subnet preconfigured with a route table
az aks create -g MyResourceGroup -n MyManagedCluster --vnet-subnet-id MySubnetID
```

## <a name="next-steps"></a>Próximas etapas

Com um cluster do AKS implantado em sua sub-rede de rede virtual existente, agora você pode usar o cluster como normal. Comece a [criar novos aplicativos usando o Helm][develop-helm] ou [implante aplicativos existentes usando o Helm][use-helm].

<!-- LINKS - External -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet
[Calico-network-policies]: https://docs.projectcalico.org/v3.9/security/calico-network-policy

<!-- LINKS - Internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-network-concepts]: concepts-network.md
[az-group-create]: /cli/azure/group#az-group-create
[az-network-vnet-create]: /cli/azure/network/vnet#az-network-vnet-create
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-network-vnet-show]: /cli/azure/network/vnet#az-network-vnet-show
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-aks-create]: /cli/azure/aks#az-aks-create
[byo-subnet-route-table]: #bring-your-own-subnet-and-route-table-with-kubenet
[develop-helm]: quickstart-helm.md
[use-helm]: kubernetes-helm.md
[virtual-nodes]: virtual-nodes-cli.md
[vnet-peering]: ../virtual-network/virtual-network-peering-overview.md
[express-route]: ../expressroute/expressroute-introduction.md
[network-comparisons]: concepts-network.md#compare-network-models
[custom-route-table]: ../virtual-network/manage-route-table.md
