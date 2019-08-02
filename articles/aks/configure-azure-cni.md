---
title: Configurar a rede CNI do Azure no AKS (Serviço de Kubernetes do Azure)
description: Saiba como configurar a rede (avançada) CNI do Azure no AKS (Serviço de Kubernetes do Azure), incluindo a implantação de um cluster do AKS em uma rede virtual e uma sub-rede existentes.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 06/03/2019
ms.author: mlearned
ms.openlocfilehash: 1cc2849ffe55fff737993140a1d0f18182820eff
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68498580"
---
# <a name="configure-azure-cni-networking-in-azure-kubernetes-service-aks"></a>Configurar a rede CNI do Azure no AKS (Serviço de Kubernetes do Azure)

Por padrão, os clusters AKS usam [kubenet][kubenet], e uma rede virtual e uma sub-rede são criadas para você. Com o *kubenet*, os nós obtém um endereço IP de uma sub-rede da rede virtual. Em seguida, o NAT (conversão de endereços de rede) é configurado nos nós e os pods recebem um endereço IP "oculto" por trás do IP do nó. Essa abordagem reduz o número de endereços IP que você precisa reservar no espaço de rede para uso dos pods.

Com a [CNI (interface de rede de contêiner do Azure)][cni-networking], cada pod Obtém um endereço IP da sub-rede e pode ser acessado diretamente. Esses endereços IP devem ser exclusivos em todo o seu espaço de rede e devem ser planejados com antecedência. Cada nó tem um parâmetro de configuração para o número máximo de pods aos quais ele dá suporte. O número equivalente de endereços IP por nó é então reservado com antecedência para esse nó. Essa abordagem exige mais planejamento e, muitas vezes, leva à exaustão do endereço IP ou à necessidade de recriar os clusters em uma sub-rede maior conforme as demandas de aplicativo aumentam.

Este artigo mostra como usar a rede *CNI do Azure* para criar e usar uma sub-rede da rede virtual de um cluster do AKS. Para obter mais informações sobre opções e considerações de rede, consulte [conceitos de rede para kubernetes e AKs][aks-network-concepts].

## <a name="prerequisites"></a>Pré-requisitos

* A rede virtual do cluster do AKS deve permitir conectividade com a Internet de saída.
* Não crie mais de um cluster do AKS na mesma sub-rede.
* Os clusters AKs não podem `169.254.0.0/16`usar `172.30.0.0/16` `172.31.0.0/16`,, ou `192.0.2.0/24` para o intervalo de endereços do serviço kubernetes.
* A entidade de serviço usada pelo cluster do AKS deve ter pelo menos permissões de [Colaborador de Rede](../role-based-access-control/built-in-roles.md#network-contributor) na sub-rede na rede virtual. Se você quiser definir uma [função personalizada](../role-based-access-control/custom-roles.md) em vez de usar a função de Colaborador de Rede interna, as seguintes permissões serão necessárias:
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`

## <a name="plan-ip-addressing-for-your-cluster"></a>Planejar o endereçamento IP para o cluster

Os clusters configurados com a rede CNI do Azure exigem planejamento adicional. O tamanho da rede virtual e da sub-rede deve acomodar o número de pods que você planeja executar e o número de nós do cluster.

Endereços IP para os pods e os nós do cluster são atribuídos a partir da sub-rede especificada na rede virtual. Cada nó é configurado com um endereço IP primário. Por padrão, 30 endereços IP adicionais são pré-configurados pelo CNI do Azure e atribuídos a pods programados no nó. Quando você expandir o cluster, cada nó está configurado da mesma forma com endereços IP da sub-rede. Você também pode ver o [máximo de pods por nó](#maximum-pods-per-node).

> [!IMPORTANT]
> O número de endereços IP necessários deve incluir considerações para operações de atualização e dimensionamento. Se você definir o intervalo de endereços IP para dar suporte a apenas um número fixo de nós, você não pode atualizar ou dimensionar o cluster.
>
> - Quando você **atualiza** seu cluster AKS, um novo nó é implantado no cluster. Serviços e cargas de trabalho começam a ser executados no novo nó e um nó mais antigo é removido do cluster. Esse processo de atualização contínua requer um mínimo de um bloco adicional de endereços IP a serem disponibilizados. Sua contagem de nós, em seguida, é `n + 1`.
>   - Essa consideração é particularmente importante quando você usa pools de nós do Windows Server (atualmente em visualização no AKS). Os nós do Windows Server no AKS não aplicam atualizações do Windows automaticamente, em vez disso, você executa uma atualização no pool de nós. Essa atualização implanta novos nós com a imagem mais recente do nó base do servidor do Windows 2019 e patches de segurança. Para obter mais informações sobre como atualizar um pool de nós do Windows Server, consulte [atualizar um pool de nós no AKs][nodepool-upgrade].
>
> - Quando você **dimensiona** um cluster AKS, um novo nó é implantado no cluster. Serviços e cargas de trabalho começam a ser executados no novo nó. Seu intervalo de endereços IP precisa levar em consideração como você pode aumentar o número de nós e pods que seu cluster pode suportar. Um nó adicional para operações de atualização também deve ser incluído. Sua contagem de nós, em seguida, é `n + number-of-additional-scaled-nodes-you-anticipate + 1`.

Se você espera que seus nós executem o número máximo de pods e destrua e implante pods regularmente, você também deve incluir alguns endereços IP adicionais por nó. Esses endereços IP adicionais levam em consideração que pode levar alguns segundos para que um serviço seja excluído e o endereço IP liberado para que um novo serviço seja implantado e adquira o endereço.

O plano de endereço IP de um cluster do AKS consiste em uma rede virtual, pelo menos uma sub-rede para nós e pods, e um intervalo de endereços de serviço do Kubernetes.

| Intervalo de endereços/ recurso do Azure | Limites e dimensionamento |
| --------- | ------------- |
| Rede virtual | A rede virtual do Azure pode ser tão grande quanto /8, mas é limitada a 65.536 endereços IP configurados. |
| Subnet | Deve ser grande o suficiente para acomodar os nós, pods e todos os recursos do Kubernetes e do Azure que possam ser provisionados no cluster. Por exemplo, se você implantar um Azure Load Balancer interno, os IPs de front-end serão alocados da sub-rede do cluster, e não IPs públicos. O tamanho da sub-rede também deve levar em conta as operações de atualização ou futuras necessidades de dimensionamento.<p />Para calcular o tamanho *mínimo* da sub-rede , incluindo um nó adicional para operações de atualização: `(number of nodes + 1) + ((number of nodes + 1) * maximum pods per node that you configure)`<p/>Exemplo para um cluster de 50 nós: `(51) + (51  * 30 (default)) = 1,581` (/21 ou maior)<p/>Exemplo para um cluster de 50 nós que também inclui provisão para escalar verticalmente 10 nós adicionais: `(61) + (61 * 30 (default)) = 1,891` (/ 21 ou maior)<p>Se você não especificar um número máximo de pods por nó durante a criação do cluster, o número máximo de pods por nó será definido como *30*. O número mínimo de endereços IP necessário tem base nesse valor. Se você calcular seus requisitos mínimos de endereço IP com um valor máximo diferente, consulte [como configurar o número máximo de pods por nó](#configure-maximum---new-clusters) para definir esse valor ao implantar o cluster. |
| Intervalo de endereços do serviço Kubernetes | Esse intervalo não deve ser usado por nenhum elemento de rede ou conectado a essa rede virtual. O endereço de serviço CIDR deve ser menor do que /12. |
| Endereço IP do serviço DNS do Kubernetes | Endereços IP dentro do intervalo de endereços de serviço do Kubernetes que serão usados pela descoberta do serviço de cluster (kube-dns). Não use o primeiro endereço IP no intervalo de endereços, como.1. O primeiro endereço no seu intervalo de sub-rede é usado para o endereço *kubernetes.default.svc.cluster.local*. |
| Endereço de ponte de docker | Endereço IP (em notação CIDR) usado como o endereço IP de ponte de docker em nós. Esse CIDR está vinculado ao número de contêineres no nó. Padrão de 172.17.0.1/16. |

## <a name="maximum-pods-per-node"></a>Máximo de pods por nó

O número máximo de pods por nó em um cluster AKS é de 250. O número máximo *padrão* de pods por nó varia entre o *kubenet* e a rede *CNI do Azure*, bem como o método de implantação do cluster.

| Método de implantação | Padrão do Kubenet | Padrão da CNI do Azure | Configurável na implantação |
| -- | :--: | :--: | -- |
| CLI do Azure | 110 | 30 | Sim (até 250) |
| Modelo do Resource Manager | 110 | 30 | Sim (até 250) |
| Portal | 110 | 30 | Não |

### <a name="configure-maximum---new-clusters"></a>Configurar o máximo - novos clusters

Você é capaz de configurar o número máximo de pods por nó *somente no momento da implantação do cluster*. Se você implantar com o CLI do Azure ou com um modelo do Resource Manager, poderá definir o valor máximo de pods por nó como alto que 250.

| Rede | Mínimo | Máximo |
| -- | :--: | :--: |
| Azure CNI | 30 | 250 |
| Kubenet | 30 | 110 |

> [!NOTE]
> O valor mínimo na tabela acima é estritamente imposto pelo serviço AKS. Você não pode definir um valor de maxPods menor do que o mínimo mostrado, pois isso pode impedir que o cluster seja iniciado.

* **CLI do Azure**: Especifique o `--max-pods` argumento ao implantar um cluster com o comando [AZ AKs Create][az-aks-create] . O valor máximo é 250.
* **Modelo do Resource Manager**: Especifica a propriedade `maxPods` no objeto [ManagedClusterAgentPoolProfile] ao implantar um cluster com um modelo do Resource Manager. O valor máximo é 250.
* **Portal do Azure**: Não é possível alterar o número máximo de pods por nó ao implantar um cluster com o portal do Azure. Os clusters da rede CNI do Azure estão limitados a 30 pods por nó quando implantados pelo portal do Azure.

### <a name="configure-maximum---existing-clusters"></a>Configurar o máximo - clusters existentes

Não é possível alterar os pods máximos por nó em um cluster do AKS existente. O número poderá ser ajustado somente quando você implantar inicialmente o cluster.

## <a name="deployment-parameters"></a>Parâmetros de implantação

Quando você cria um cluster do AKS, os seguintes parâmetros são configuráveis para a rede CNI do Azure:

**Rede virtual**: A rede virtual na qual você quer implantar o cluster do Kubernetes. Se você quiser criar uma nova rede virtual para o cluster, selecione *Criar nova* e siga as etapas na seção *Criar rede virtual*. Para obter mais informações sobre limites e cotas para uma rede virtual do Azure, consulte [Limites, cotas e restrições de assinatura e serviço do Azure](../azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits).

**Sub-rede**: A sub-rede dentro da rede virtual na qual você quer implantar o cluster. Se você quiser criar uma nova sub-rede na rede virtual para o cluster, selecione *Criar nova* e siga as etapas na seção *Criar sub-rede*. Para conectividade híbrida, o intervalo de endereços não deve se sobrepor a nenhuma outra rede virtual em seu ambiente.

**Intervalo de endereços de serviço do Kubernetes**: Esse é o conjunto de IPs virtuais que o kubernetes atribui a [Serviços][services] internos em seu cluster. Você pode usar qualquer intervalo de endereço particular que atenda aos seguintes requisitos:

* Não deve estar dentro do intervalo de endereços IP da rede virtual do cluster
* Não deve se sobrepor a nenhuma outra rede virtual com a qual a rede virtual do cluster está emparelhada
* Não deve sobrepor IPs locais
* Não deve estar dentro dos intervalos `169.254.0.0/16`, `172.30.0.0/16`, `172.31.0.0/16`ou`192.0.2.0/24`

Embora seja tecnicamente possível especificar um intervalo de endereço de serviço na mesma rede virtual do cluster, isso não é recomendado. Um comportamento imprevisível pode ocorrer se forem usados intervalos de IP sobrepostos. Para mais informações, consulte a seção [FAQ](#frequently-asked-questions) deste artigo. Para obter mais informações sobre os serviços Kubernetess, consulte [Serviços][services] na documentação do kubernetes.

**Endereço IP do serviço DNS do Kubernetes**:  O endereço IP do serviço DNS do cluster. Esse endereço deve estar dentro do *intervalo de endereços do serviço do Kubernetes*. Não use o primeiro endereço IP no intervalo de endereços, como.1. O primeiro endereço no seu intervalo de sub-rede é usado para o endereço *kubernetes.default.svc.cluster.local*.

**Endereço da Ponte Docker**: O endereço de rede da ponte do Docker representa o endereço de rede padrão da ponte *docker0* presente em todas as instalações do Docker. Embora a ponte *docker0* não seja usada por clusters AKs ou o próprio pods, você deve definir esse endereço para continuar a dar suporte a cenários como a *compilação* do Docker no cluster AKs. É necessário selecionar um CIDR para o endereço de rede de ponte do Docker porque, caso contrário, o Docker selecionará uma sub-rede automaticamente, o que poderá entrar em conflito com outros CIDRs. Você deve escolher um espaço de endereço que não se colide com o restante dos CIDR em suas redes, incluindo o CIDR de serviço do cluster e o Pod

## <a name="configure-networking---cli"></a>Configurar a rede – CLI

Quando você cria um cluster do AKS com a CLI do Azure, você também pode configurar a rede CNI do Azure. Use os comandos a seguir para criar um cluster do AKS com a rede CNI do Azure habilitada.

Primeiro, obtenha a ID de recurso de sub-rede para a sub-rede existente no qual o cluster AKS será adicionado:

```azurecli-interactive
$ az network vnet subnet list \
    --resource-group myVnet \
    --vnet-name myVnet \
    --query [].id --output tsv

/subscriptions/<guid>/resourceGroups/myVnet/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/default
```

Use o comando [AZ AKs Create][az-aks-create] com o `--network-plugin azure` argumento para criar um cluster com a rede avançada. Atualize o valor `--vnet-subnet-id` com a ID de sub-rede obtida na etapa anterior:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24 \
    --generate-ssh-keys
```

## <a name="configure-networking---portal"></a>Configurar a rede – portal

Captura de tela a seguir do portal do Azure mostra um exemplo de como configurar essas configurações durante a criação do cluster AKS:

![Configuração de rede avançada no portal do Azure][portal-01-networking-advanced]

## <a name="frequently-asked-questions"></a>Perguntas frequentes

As perguntas e respostas a seguir se aplicam à configuração da rede **CNI do Azure**.

* *Posso implantar VMs na sub-rede do cluster?*

  Nº Não há suporte para a implantação de VMs na sub-rede usada por seu cluster Kubernetes. As VMs podem ser implantadas na mesma rede virtual, mas em uma sub-rede diferente.

* *Posso configurar políticas de rede por pod?*

  Sim, a política de rede kubernetes está disponível em AKS. Para começar, consulte [proteger o tráfego entre pods usando as políticas de rede no AKs][network-policy].

* *O número máximo de pods implantados em um nó é configurável?*

  Sim, ao implantar um cluster com a CLI do Azure ou um modelo do Resource Manager. Consulte [Máximo de pods por nó](#maximum-pods-per-node).

  Não é possível alterar o número máximo de pods por nó em um cluster existente.

* *Como configurar propriedades adicionais para a sub-rede que criei durante a criação do cluster AKS? Por exemplo, pontos de extremidade do serviço.*

  A lista completa de propriedades para a rede virtual e as sub-redes que você cria durante a criação do cluster do AKS pode ser configurada na página de configuração de rede virtual padrão no portal do Azure.

* *Eu posso usar uma sub-rede diferente na rede virtual do cluster para o*   **intervalo de endereços de serviço do Kubernetes**?

  Não é recomendado, mas essa configuração é possível. O intervalo de endereços do serviço é um conjunto de VIPs (IPs virtuais) que o Kubernetes atribui aos serviços internos no cluster. O Azure Networking não tem visibilidade do intervalo de IP de serviço do cluster do Kubernetes. Devido à falta de visibilidade do intervalo de endereços de serviço do cluster, é possível criar posteriormente uma nova sub-rede na rede virtual do cluster que se sobrepõe ao intervalo de endereços de serviço. Se tal sobreposição ocorrer, o Kubernetes poderá atribuir um serviço a um IP que já esteja em uso por outro recurso na sub-rede, causando comportamento ou falhas imprevisíveis. Ao garantir que você use um intervalo de endereços fora da rede virtual do cluster, é possível evitar esse risco de sobreposição.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a rede no AKS nos seguintes artigos:

- [Usar um endereço IP estático com o balanceador de carga do Serviço do Kubernetes do Azure (AKS).](static-ip.md)
- [Usar um balanceador de carga interno com o Serviço de Contêiner do Azure (AKS)](internal-lb.md)

- [Criar um controlador de entrada básico com conectividade de rede externa][aks-ingress-basic]
- [Habilitar o complemento de roteamento de aplicativo HTTP][aks-http-app-routing]
- [Criar um controlador de entrada que usa uma rede interna e privada e um endereço IP][aks-ingress-internal]
- [Criar um controlador de entrada com um IP público dinâmico e configurar vamos criptografar para gerar automaticamente certificados TLS][aks-ingress-tls]
- [Criar um controlador de entrada com um IP público estático e configurar vamos criptografar para gerar automaticamente os certificados TLS][aks-ingress-static-tls]

### <a name="aks-engine"></a>Mecanismo do AKS

O [mecanismo de serviço kubernetes do Azure (mecanismo de AKs)][aks-engine] é um projeto de software livre que gera modelos de Azure Resource Manager que você pode usar para implantar clusters kubernetes no Azure.

Os clusters kubernetes criados com o mecanismo AKS dão suporte aos plug-ins [kubenet][kubenet] e [CNI do Azure][cni-networking] . Desse modo, há suporte para os dois cenários de rede no Mecanismo do AKS.

<!-- IMAGES -->
[advanced-networking-diagram-01]: ./media/networking-overview/advanced-networking-diagram-01.png
[portal-01-networking-advanced]: ./media/networking-overview/portal-01-networking-advanced.png

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[services]: https://kubernetes.io/docs/concepts/services-networking/service/
[portal]: https://portal.azure.com
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet

<!-- LINKS - Internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[aks-ssh]: ssh.md
[ManagedClusterAgentPoolProfile]: /azure/templates/microsoft.containerservice/managedclusters#managedclusteragentpoolprofile-object
[aks-network-concepts]: concepts-network.md
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-internal]: ingress-internal-ip.md
[network-policy]: use-network-policies.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[network-comparisons]: concepts-network.md#compare-network-models
