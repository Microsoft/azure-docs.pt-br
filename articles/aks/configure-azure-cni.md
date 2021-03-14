---
title: Configurar a rede CNI do Azure no AKS (Serviço de Kubernetes do Azure)
description: Saiba como configurar a rede (avançada) CNI do Azure no AKS (Serviço de Kubernetes do Azure), incluindo a implantação de um cluster do AKS em uma rede virtual e uma sub-rede existentes.
services: container-service
ms.topic: article
ms.date: 06/03/2019
ms.custom: references_regions
ms.openlocfilehash: ef9e3689f5846ddfc66c47a15967a18fc6550d35
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102504245"
---
# <a name="configure-azure-cni-networking-in-azure-kubernetes-service-aks"></a>Configurar a rede CNI do Azure no AKS (Serviço de Kubernetes do Azure)

Por padrão, os clusters do AKS usam o [kubenet][kubenet], e uma rede virtual e uma sub-rede são criadas para você. Com o *kubenet*, os nós obtém um endereço IP de uma sub-rede da rede virtual. Em seguida, o NAT (conversão de endereços de rede) é configurado nos nós e os pods recebem um endereço IP "oculto" por trás do IP do nó. Essa abordagem reduz o número de endereços IP que você precisa reservar no espaço de rede para uso dos pods.

Com a [CNI (Interface de Rede de Contêiner) do Azure][cni-networking], cada pod obtém um endereço IP da sub-rede e pode ser acessado diretamente. Esses endereços IP devem ser exclusivos em todo o seu espaço de rede e devem ser planejados com antecedência. Cada nó tem um parâmetro de configuração para o número máximo de pods aos quais ele dá suporte. O número equivalente de endereços IP por nó é então reservado com antecedência para esse nó. Essa abordagem exige mais planejamento e, muitas vezes, leva à exaustão do endereço IP ou à necessidade de recriar os clusters em uma sub-rede maior conforme as demandas de aplicativo aumentam.

Este artigo mostra como usar a rede *CNI do Azure* para criar e usar uma sub-rede da rede virtual de um cluster do AKS. Para saber mais sobre opções de rede e considerações, confira [Conceitos de rede para o Kubernetes e o AKS][aks-network-concepts].

## <a name="prerequisites"></a>Pré-requisitos

* A rede virtual do cluster do AKS deve permitir conectividade com a Internet de saída.
* Os clusters AKs podem não usar `169.254.0.0/16` ,, `172.30.0.0/16` `172.31.0.0/16` ou `192.0.2.0/24` para o intervalo de endereços do serviço kubernetes, o intervalo de endereços Pod ou o intervalo de endereços de rede virtual do cluster.
* A identidade do cluster usada pelo cluster AKS deve ter pelo menos permissões de [colaborador de rede](../role-based-access-control/built-in-roles.md#network-contributor) na sub-rede em sua rede virtual. Se você quiser definir uma [função personalizada](../role-based-access-control/custom-roles.md) em vez de usar a função de Colaborador de Rede interna, as seguintes permissões serão necessárias:
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`
* A sub-rede atribuída ao pool de nós AKS não pode ser uma [sub-rede delegada](../virtual-network/subnet-delegation-overview.md).

## <a name="plan-ip-addressing-for-your-cluster"></a>Planejar o endereçamento IP para o cluster

Os clusters configurados com a rede CNI do Azure exigem planejamento adicional. O tamanho da rede virtual e da sub-rede deve acomodar o número de pods que você planeja executar e o número de nós do cluster.

Endereços IP para os pods e os nós do cluster são atribuídos a partir da sub-rede especificada na rede virtual. Cada nó é configurado com um endereço IP primário. Por padrão, 30 endereços IP adicionais são pré-configurados pelo CNI do Azure e atribuídos a pods programados no nó. Quando você expandir o cluster, cada nó está configurado da mesma forma com endereços IP da sub-rede. Você também pode ver o [máximo de pods por nó](#maximum-pods-per-node).

> [!IMPORTANT]
> O número de endereços IP necessários deve incluir considerações para operações de atualização e dimensionamento. Se você definir o intervalo de endereços IP para dar suporte a apenas um número fixo de nós, você não pode atualizar ou dimensionar o cluster.
>
> * Quando você **atualiza** seu cluster AKS, um novo nó é implantado no cluster. Serviços e cargas de trabalho começam a ser executados no novo nó e um nó mais antigo é removido do cluster. Esse processo de atualização contínua requer um mínimo de um bloco adicional de endereços IP a serem disponibilizados. Sua contagem de nós, em seguida, é `n + 1`.
>   * Essa consideração é particularmente importante quando você usa pools de nós do Windows Server. Os nós do Windows Server no AKS não aplicam atualizações do Windows automaticamente, em vez disso, você executa uma atualização no pool de nós. Essa atualização implanta novos nós com a imagem mais recente do nó base do servidor do Windows 2019 e patches de segurança. Para obter mais informações sobre como atualizar um pool de nós do Windows Server, consulte [atualizar um pool de nós no AKs][nodepool-upgrade].
>
> * Quando você **dimensiona** um cluster AKS, um novo nó é implantado no cluster. Serviços e cargas de trabalho começam a ser executados no novo nó. Seu intervalo de endereços IP precisa levar em consideração como você pode aumentar o número de nós e pods que seu cluster pode suportar. Um nó adicional para operações de atualização também deve ser incluído. Sua contagem de nós, em seguida, é `n + number-of-additional-scaled-nodes-you-anticipate + 1`.

Se você espera que seus nós executem o número máximo de pods e destrua e implante pods regularmente, você também deve incluir alguns endereços IP adicionais por nó. Esses endereços IP adicionais levam em consideração que pode levar alguns segundos para que um serviço seja excluído e o endereço IP liberado para que um novo serviço seja implantado e adquira o endereço.

O plano de endereço IP de um cluster do AKS consiste em uma rede virtual, pelo menos uma sub-rede para nós e pods, e um intervalo de endereços de serviço do Kubernetes.

| Intervalo de endereços/ recurso do Azure | Limites e dimensionamento |
| --------- | ------------- |
| Rede virtual | A rede virtual do Azure pode ser tão grande quanto /8, mas é limitada a 65.536 endereços IP configurados. Considere todas as suas necessidades de rede, incluindo a comunicação com serviços em outras redes virtuais, antes de configurar seu espaço de endereço. Por exemplo, se você configurar um espaço de endereço muito grande, poderá encontrar problemas com a sobreposição de outros espaços de endereço na rede.|
| Sub-rede | Deve ser grande o suficiente para acomodar os nós, pods e todos os recursos do Kubernetes e do Azure que possam ser provisionados no cluster. Por exemplo, se você implantar um Azure Load Balancer interno, os IPs de front-end serão alocados da sub-rede do cluster, e não IPs públicos. O tamanho da sub-rede também deve levar em conta as operações de atualização ou futuras necessidades de dimensionamento.<p />Para calcular o tamanho *mínimo* da sub-rede , incluindo um nó adicional para operações de atualização: `(number of nodes + 1) + ((number of nodes + 1) * maximum pods per node that you configure)`<p/>Exemplo para um cluster de 50 nós: `(51) + (51  * 30 (default)) = 1,581` (/21 ou maior)<p/>Exemplo para um cluster de 50 nós que também inclui provisão para escalar verticalmente 10 nós adicionais: `(61) + (61 * 30 (default)) = 1,891` (/ 21 ou maior)<p>Se você não especificar um número máximo de pods por nó durante a criação do cluster, o número máximo de pods por nó será definido como *30*. O número mínimo de endereços IP necessário tem base nesse valor. Se você calcular seus requisitos mínimos de endereço IP com um valor máximo diferente, consulte [como configurar o número máximo de pods por nó](#configure-maximum---new-clusters) para definir esse valor ao implantar o cluster. |
| Intervalo de endereços de serviço do Kubernetes | Esse intervalo não deve ser usado por nenhum elemento de rede ou conectado a essa rede virtual. O endereço de serviço CIDR deve ser menor do que /12. Você pode reutilizar esse intervalo em diferentes clusters AKS. |
| Endereço IP do serviço DNS do Kubernetes | Endereço IP dentro do intervalo de endereços do serviço kubernetes que será usado pela descoberta do serviço de cluster. Não use o primeiro endereço IP no intervalo de endereços, como.1. O primeiro endereço no seu intervalo de sub-rede é usado para o endereço *kubernetes.default.svc.cluster.local*. |
| Endereço de ponte de docker | O endereço de rede da Ponte Docker representa o endereço de rede de ponte padrão *docker0*, presente em todas as instalações do Docker. Embora a ponte *docker0* não seja usada por clusters AKs ou o próprio pods, você deve definir esse endereço para continuar a dar suporte a cenários como a *compilação do Docker* no cluster AKs. É necessário selecionar um CIDR para o endereço de rede da ponte do Docker porque, caso contrário, o Docker selecionará uma sub-rede automaticamente, o que pode entrar em conflito com outros CIDRs. Você deve escolher um espaço de endereço que não se colide com o restante dos CIDR em suas redes, incluindo o CIDR de serviço do cluster e o Pod Padrão de 172.17.0.1/16. Você pode reutilizar esse intervalo em diferentes clusters AKS. |

## <a name="maximum-pods-per-node"></a>Máximo de pods por nó

O número máximo de pods por nó em um cluster AKS é de 250. O número máximo *padrão* de pods por nó varia entre o *kubenet* e a rede *CNI do Azure*, bem como o método de implantação do cluster.

| Método de implantação | Padrão do Kubenet | Padrão da CNI do Azure | Configurável na implantação |
| -- | :--: | :--: | -- |
| CLI do Azure | 110 | 30 | Sim (até 250) |
| Modelo do Resource Manager | 110 | 30 | Sim (até 250) |
| Portal | 110 | 110 (configurado na guia pools de nós) | Não |

### <a name="configure-maximum---new-clusters"></a>Configurar o máximo - novos clusters

Você pode configurar o número máximo de pods por nó no tempo de implantação do cluster ou ao adicionar novos pools de nós. Se você implantar com o CLI do Azure ou com um modelo do Resource Manager, poderá definir o valor máximo de pods por nó como alto que 250.

Se você não especificar maxPods ao criar novos pools de nós, você receberá um valor padrão de 30 para o Azure CNI.

Um valor mínimo para máximo de pods por nó é imposto para garantir o espaço para o pods crítico do sistema para a integridade do cluster. O valor mínimo que pode ser definido para o pods máximo por nó é 10 se e somente se a configuração de cada pool de nós tiver espaço para um mínimo de 30 pods. Por exemplo, definir o máximo de pods por nó para o mínimo de 10 exige que cada pool de nós individual tenha um mínimo de 3 nós. Esse requisito se aplica a cada novo pool de nós criado também; portanto, se 10 for definido como um pods máximo por nó, cada pool de nós subsequente adicionado deverá ter pelo menos 3 nós.

| Rede | Mínimo | Máximo |
| -- | :--: | :--: |
| CNI do Azure | 10 | 250 |
| Kubenet | 10 | 110 |

> [!NOTE]
> O valor mínimo na tabela acima é estritamente imposto pelo serviço AKS. Você não pode definir um valor de maxPods menor do que o mínimo mostrado, pois isso pode impedir que o cluster seja iniciado.

* **CLI do Azure**: especifica o argumento `--max-pods` ao implantar um cluster com o comando [az aks create][az-aks-create]. O valor máximo é 250.
* **Modelo do Resource Manager**: especifica a propriedade `maxPods` no objeto [ManagedClusterAgentPoolProfile] ao implantar um cluster com um modelo do Resource Manager. O valor máximo é 250.
* **Portal do Azure**: não é possível modificar o número máximo de pods por nó ao implantar um cluster com o Portal do Azure. Os clusters da rede CNI do Azure estão limitados a 30 pods por nó quando implantados pelo portal do Azure.

### <a name="configure-maximum---existing-clusters"></a>Configurar o máximo - clusters existentes

A configuração maxPod por nó pode ser definida quando você cria um novo pool de nós. Se você precisar aumentar a configuração de maxPod por nó em um cluster existente, adicione um novo pool de nós com a nova contagem de maxPod desejada. Depois de migrar o pods para o novo pool, exclua o pool mais antigo. Para excluir qualquer pool mais antigo em um cluster, verifique se você está definindo os modos de pool de nós conforme definido no [documento pools de nós do sistema][system-node-pools].

## <a name="deployment-parameters"></a>Parâmetros de implantação

Quando você cria um cluster do AKS, os seguintes parâmetros são configuráveis para a rede CNI do Azure:

**Rede virtual**: a rede virtual na qual você deseja implantar o cluster do Kubernetes. Se você quiser criar uma nova rede virtual para o cluster, selecione *Criar nova* e siga as etapas na seção *Criar rede virtual*. Para obter mais informações sobre limites e cotas para uma rede virtual do Azure, consulte [Limites, cotas e restrições de assinatura e serviço do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits).

**Sub-rede**: a sub-rede dentro da rede virtual em que você quer implantar o cluster. Se você quiser criar uma nova sub-rede na rede virtual para o cluster, selecione *Criar nova* e siga as etapas na seção *Criar sub-rede*. Para conectividade híbrida, o intervalo de endereços não deve se sobrepor a nenhuma outra rede virtual em seu ambiente.

**Plug-in de rede do Azure**: quando o plug-in de rede do Azure é usado, o serviço de balanceador interno com "ExternalTrafficPolicy = local" não pode ser acessado de VMs com um IP em clusterCIDR que não pertence ao cluster AKs.

**Intervalo de endereços do serviço kubernetes**: esse parâmetro é o conjunto de IPS virtuais que o kubernetes atribui a [Serviços][services] internos em seu cluster. Você pode usar qualquer intervalo de endereço particular que atenda aos seguintes requisitos:

* Não deve estar dentro do intervalo de endereços IP da rede virtual do cluster
* Não deve se sobrepor a nenhuma outra rede virtual com a qual a rede virtual do cluster está emparelhada
* Não deve sobrepor IPs locais
* Não deve estar dentro dos intervalos `169.254.0.0/16` , `172.30.0.0/16` , `172.31.0.0/16` ou `192.0.2.0/24`

Embora seja tecnicamente possível especificar um intervalo de endereço de serviço na mesma rede virtual do cluster, isso não é recomendado. Um comportamento imprevisível pode ocorrer se forem usados intervalos de IP sobrepostos. Para mais informações, consulte a seção [FAQ](#frequently-asked-questions) deste artigo. Para obter mais informações sobre os serviços do Kubernetes, consulte [Serviços][services] na documentação do Kubernetes.

**Endereço IP do serviço DNS do Kubernetes**: O endereço IP para o serviço DNS do cluster. Esse endereço deve estar dentro do *intervalo de endereços do serviço Kubernetes*. Não use o primeiro endereço IP no intervalo de endereços, como.1. O primeiro endereço no seu intervalo de sub-rede é usado para o endereço *kubernetes.default.svc.cluster.local*.

**Endereço de ponte do Docker**: o endereço de rede da ponte do Docker representa o endereço de rede de ponte *docker0* padrão presente em todas as instalações do Docker. Embora a ponte *docker0* não seja usada por clusters AKs ou o próprio pods, você deve definir esse endereço para continuar a dar suporte a cenários como a *compilação do Docker* no cluster AKs. É necessário selecionar um CIDR para o endereço de rede de ponte do Docker porque, caso contrário, o Docker selecionará uma sub-rede automaticamente, o que poderá entrar em conflito com outros CIDRs. Você deve escolher um espaço de endereço que não se colide com o restante dos CIDR em suas redes, incluindo o CIDR de serviço do cluster e o Pod

## <a name="configure-networking---cli"></a>Configurar a rede – CLI

Quando você cria um cluster do AKS com a CLI do Azure, você também pode configurar a rede CNI do Azure. Use os comandos a seguir para criar um cluster do AKS com a rede CNI do Azure habilitada.

Primeiro, obtenha a ID de recurso de sub-rede para a sub-rede existente no qual o cluster AKS será adicionado:

```azurecli-interactive
$ az network vnet subnet list \
    --resource-group myVnet \
    --vnet-name myVnet \
    --query "[0].id" --output tsv

/subscriptions/<guid>/resourceGroups/myVnet/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/default
```

Use o comando [az aks create][az-aks-create] com o argumento `--network-plugin azure` para criar um cluster com os recursos avançados de rede. Atualize o valor `--vnet-subnet-id` com a ID de sub-rede obtida na etapa anterior:

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

! [Configuração avançada de rede no portal do Azure] [portal-01-rede-avançado]

## <a name="dynamic-allocation-of-ips-and-enhanced-subnet-support-preview"></a>Alocação dinâmica de IPs e suporte de sub-rede avançada (visualização)

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

> [!NOTE] 
> Este recurso de visualização está disponível atualmente nas seguintes regiões:
>
> * Centro-Oeste dos EUA

Uma desvantagem do CNI tradicional é o esgotamento de endereços IP de Pod à medida que o cluster AKS cresce, resultando na necessidade de recriar o cluster inteiro em uma sub-rede maior. O novo recurso de alocação de IP dinâmico no Azure CNI resolve esse problema alocando IPs de pod de uma sub-rede separada da sub-rede que hospeda o cluster AKS.  Ele oferece os seguintes benefícios:

* **Melhor utilização de IP**: os IPs são alocados dinamicamente para os pods de cluster da sub-rede Pod. Isso leva a uma melhor utilização de IPs no cluster em comparação com a solução CNI tradicional, que faz a alocação estática de IPs para cada nó.  

* **Escalonável e flexível**: as sub-redes de nó e Pod podem ser dimensionadas de forma independente. Uma sub-rede Pod única pode ser compartilhada entre vários pools de nó de um cluster ou em vários clusters AKS implantados na mesma VNet. Você também pode configurar uma sub-rede Pod separada para um pool de nós.  

* **Alto desempenho**: como Pod são atribuídos a IPS de VNet, eles têm conectividade direta com outros recursos e pod de cluster na VNet. A solução dá suporte a clusters muito grandes sem degradação no desempenho.

* **Separar políticas de vnet para pods**: como os pods têm uma sub-rede separada, você pode configurar políticas de vnet separadas para elas que são diferentes das políticas de nó. Isso permite muitos cenários úteis, como permitir a conectividade com a Internet apenas para pods e não para nós, corrigindo o IP de origem para Pod em um pool de nós usando um NAT de rede de VNet e usando NSGs para filtrar o tráfego entre pools de nós.  

* **Kubernetes políticas de rede**: as políticas de rede do Azure e o Calico funcionam com essa nova solução.  

### <a name="install-the-aks-preview-azure-cli"></a>Instalar o `aks-preview` CLI do Azure

Você precisará da extensão de CLI do Azure *AKs-Preview* . Instale a extensão de CLI do Azure *de AKs-Preview* usando o comando [AZ Extension Add][az-extension-add] . Ou instale todas as atualizações disponíveis usando o comando [AZ Extension Update][az-extension-update] .

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-the-podsubnetpreview-preview-feature"></a>Registrar o `PodSubnetPreview` recurso de visualização

Para usar o recurso, você também deve habilitar o `PodSubnetPreview` sinalizador de recurso em sua assinatura.

Registre o `PodSubnetPreview` sinalizador de recurso usando o comando [AZ Feature Register][az-feature-register] , conforme mostrado no exemplo a seguir:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "PodSubnetPreview"
```

Demora alguns minutos para o status exibir *Registrado*. Verifique o status do registro usando o comando [AZ Feature List][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/PodSubnetPreview')].{Name:name,State:properties.state}"
```

Quando estiver pronto, atualize o registro do provedor de recursos *Microsoft. ContainerService* usando o comando [AZ Provider Register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="additional-prerequisites"></a>Pré-requisitos adicionais

Os pré-requisitos já listados para o Azure CNI ainda se aplicam, mas há algumas limitações adicionais:

* Há suporte apenas para clusters de nós e pools de nós do Linux.
* Não há suporte para os clusters do mecanismo AKS e do DIY.

### <a name="planning-ip-addressing"></a>Planejando o endereçamento de IP

Ao usar esse recurso, o planejamento é muito mais simples. Como os nós e os pods são dimensionados de forma independente, seus espaços de endereço também podem ser planejados separadamente. Como as sub-redes Pod podem ser configuradas para a granularidade de um pool de nós, os clientes sempre podem adicionar uma nova sub-rede quando adicionam um pool de nós. O pods do sistema em um cluster/pool de nós também recebe IPs da sub-rede Pod, portanto, esse comportamento precisa ser contabilizado.

O planejamento de IPs para os serviços K8S e a ponte do Docker permanecem inalterados.

### <a name="maximum-pods-per-node-in-a-cluster-with-dynamic-allocation-of-ips-and-enhanced-subnet-support"></a>Pods máxima por nó em um cluster com alocação dinâmica de IPs e suporte de sub-rede avançada

Os valores de pods por nó ao usar o CNI do Azure com alocação dinâmica de IPs mudaram ligeiramente do comportamento tradicional de CNI:

|CNI|Método de implantação|Padrão|Configurável na implantação|
|--|--| :--: |--|
|CNI do Azure tradicional|CLI do Azure|30|Sim (até 250)|
|CNI do Azure com alocação dinâmica de IPs|CLI do Azure|250|Sim (até 250)|

Todas as outras orientações relacionadas à configuração do máximo de nós por Pod permanecem as mesmas.

### <a name="additional-deployment-parameters"></a>Parâmetros de implantação adicionais

Os parâmetros de implantação descritos acima ainda são válidos, com uma exceção:

* O parâmetro **subnet** agora se refere à sub-rede relacionada aos nós do cluster.
* Uma **sub-rede Pod** de parâmetro adicional é usada para especificar a sub-rede cujos endereços IP serão alocados dinamicamente para pods.

### <a name="configure-networking---cli-with-dynamic-allocation-of-ips-and-enhanced-subnet-support"></a>Configurar rede-CLI com alocação dinâmica de IPs e suporte de sub-rede avançada

Usar a alocação dinâmica de IPs e o suporte de sub-rede avançada em seu cluster é semelhante ao método padrão para configurar um cluster do Azure CNI. O exemplo a seguir orienta a criação de uma nova rede virtual com uma sub-rede para nós e uma sub-rede para pods e a criação de um cluster que usa o CNI do Azure com alocação dinâmica de IPs e suporte de sub-rede avançada. Certifique-se de substituir variáveis como `$subscription` com seus próprios valores:

Primeiro, crie a rede virtual com duas sub-redes:

```azurecli-interactive
$resourceGroup="myResourceGroup"
$vnet="myVirtualNetwork"

# Create our two subnet network 
az network vnet create -g $rg --name $vnet --address-prefixes 10.0.0.0/8 -o none 
az network vnet subnet create -g $rg --vnet-name $vnet --name nodesubnet --address-prefixes 10.240.0.0/16 -o none 
az network vnet subnet create -g $rg --vnet-name $vnet --name podsubnet --address-prefixes 10.241.0.0/16 -o none 
```

Em seguida, crie o cluster, referenciando a sub-rede do nó usando `--vnet-subnet-id` e a sub-rede Pod usando `--pod-subnet-id` :

```azurecli-interactive
$clusterName="myAKSCluster"
$location="eastus"
$subscription="aaaaaaa-aaaaa-aaaaaa-aaaa"

az aks create -n $clusterName -g $resourceGroup -l $location --max-pods 250 --node-count 2 --network-plugin azure --vnet-subnet-id /subscriptions/$subscription/resourceGroups/$resourceGroup/providers/Microsoft.Network/virtualNetworks/$vnet/subnets/nodesubnet --pod-subnet-id /subscriptions/$subscription/resourceGroups/$resourceGroup/providers/Microsoft.Network/virtualNetworks/$vnet/subnets/podsubnet  
```

#### <a name="adding-node-pool"></a>Adicionando pool de nós

Ao adicionar o pool de nós, faça referência à sub-rede do nó usando `--vnet-subnet-id` e a sub-rede Pod usando `--pod-subnet-id` . O exemplo a seguir cria duas novas sub-redes que são referenciadas na criação de um novo pool de nós:

```azurecli-interactive
az network vnet subnet create -g $resourceGroup --vnet-name $vnet --name node2subnet --address-prefixes 10.242.0.0/16 -o none 
az network vnet subnet create -g $resourceGroup --vnet-name $vnet --name pod2subnet --address-prefixes 10.243.0.0/16 -o none 

az aks nodepool add --cluster-name $clusterName -g $resourceGroup  -n newNodepool --max-pods 250 --node-count 2 --vnet-subnet-id /subscriptions/$subscription/resourceGroups/$resourceGroup/providers/Microsoft.Network/virtualNetworks/$vnet/subnets/node2subnet  --pod-subnet-id /subscriptions/$subscription/resourceGroups/$resourceGroup/providers/Microsoft.Network/virtualNetworks/$vnet/subnets/pod2subnet --no-wait 
```

## <a name="frequently-asked-questions"></a>Perguntas frequentes

As perguntas e respostas a seguir se aplicam à configuração da rede **CNI do Azure**.

* *Posso implantar VMs na sub-rede do cluster?*

  Sim.

* *Qual IP de origem os sistemas externos veem para o tráfego originado em um pod habilitado para CNI do Azure?*

  Os sistemas na mesma rede virtual que o cluster AKS veem o IP Pod como o endereço de origem para qualquer tráfego do pod. Os sistemas fora da rede virtual do cluster AKS veem o IP do nó como o endereço de origem para qualquer tráfego do pod.

* *Posso configurar políticas de rede por Pod?*

  Sim, a política de rede kubernetes está disponível em AKS. Para começar, consulte [proteger o tráfego entre pods usando as políticas de rede no AKs][network-policy].

* *O número máximo de pods implantados em um nó é configurável?*

  Sim, ao implantar um cluster com a CLI do Azure ou um modelo do Resource Manager. Consulte [Máximo de pods por nó](#maximum-pods-per-node).

  Não é possível alterar o número máximo de pods por nó em um cluster existente.

* *Como fazer configurar propriedades adicionais para a sub-rede que criei durante a criação do cluster AKS? Por exemplo, pontos de extremidade de serviço.*

  A lista completa de propriedades para a rede virtual e as sub-redes que você cria durante a criação do cluster do AKS pode ser configurada na página de configuração de rede virtual padrão no portal do Azure.

* *Eu posso usar uma sub-rede diferente na rede virtual do cluster para o* **intervalo de endereços de serviço do Kubernetes**?

  Não é recomendado, mas essa configuração é possível. O intervalo de endereços do serviço é um conjunto de VIPs (IPs virtuais) que o Kubernetes atribui aos serviços internos no cluster. O Azure Networking não tem visibilidade do intervalo de IP de serviço do cluster do Kubernetes. Devido à falta de visibilidade do intervalo de endereços de serviço do cluster, é possível criar posteriormente uma nova sub-rede na rede virtual do cluster que se sobrepõe ao intervalo de endereços de serviço. Se tal sobreposição ocorrer, o Kubernetes poderá atribuir um serviço a um IP que já esteja em uso por outro recurso na sub-rede, causando comportamento ou falhas imprevisíveis. Ao garantir que você use um intervalo de endereços fora da rede virtual do cluster, é possível evitar esse risco de sobreposição.

### <a name="dynamic-allocation-of-ip-addresses-and-enhanced-subnet-support-faqs"></a>Perguntas frequentes sobre alocação dinâmica de endereços IP e suporte avançado à sub-rede

As perguntas e respostas a seguir se aplicam à **configuração de rede CNI do Azure ao usar a alocação dinâmica de endereços IP e o suporte à sub-rede avançada**.

* *Posso atribuir várias sub-redes Pod a um cluster/pool de nós?*

  Somente uma sub-rede pode ser atribuída a um cluster ou pool de nós. No entanto, vários clusters ou pools de nós podem compartilhar uma única sub-rede.

* *Posso atribuir sub-redes pod de uma VNet diferente totalmente?*

  A sub-rede Pod deve ser da mesma VNet que o cluster.  

* *Alguns pools de nós podem usar o CNI tradicional enquanto outros usam o novo CNI?*

  O cluster inteiro deve usar apenas um tipo de CNI.

## <a name="aks-engine"></a>Mecanismo do AKS

O [Mecanismo do AKS (Mecanismo do Serviço de Kubernetes do Azure)][aks-engine] é um projeto de software livre que gera modelos do Azure Resource Manager que podem ser usados para implantar clusters do Kubernetes no Azure.

Clusters de Kubernetes criados com Mecanismo do AKS dão suporte aos plug-ins [kubenet][kubenet] e [CNI do Azure][cni-networking]. Desse modo, há suporte para os dois cenários de rede no Mecanismo do AKS.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a rede no AKS nos seguintes artigos:

* [Usar um endereço IP estático com o balanceador de carga do Serviço do Kubernetes do Azure (AKS).](static-ip.md)
* [Usar um balanceador de carga interno com o AKS (Serviço de Contêiner do Azure)](internal-lb.md)

* [Criar um controlador de entrada básico com conectividade de rede externa][aks-ingress-basic]
* [Habilitar o complemento de roteamento de aplicativo HTTP][aks-http-app-routing]
* [Criar um controlador de entrada que usa uma rede privada interna e um endereço IP][aks-ingress-internal]
* [Criar um controlador de entrada com um IP público dinâmico e configurar Vamos Criptografar para gerar certificados TLS automaticamente][aks-ingress-tls]
* [Crie um controlador de entrada com um IP público estático e configure Vamos Criptografar para gerar certificados TLS automaticamente][aks-ingress-static-tls]
<!-- IMAGES -->
[Advanced-Networking-Diagram-01]:./Media/Networking-Overview/advanced-networking-diagram-01.png [portal-01-Networking-Advanced]:./Media/Networking-Overview/portal-01-networking-advanced.png

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[services]: https://kubernetes.io/docs/concepts/services-networking/service/
[portal]: https://portal.azure.com
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet

<!-- LINKS - Internal -->
[az-aks-create]: /cli/azure/aks#az-aks-create
[aks-ssh]: ssh.md
[ManagedClusterAgentPoolProfile]: /azure/templates/microsoft.containerservice/managedclusters#managedclusteragentpoolprofile-object
[aks-network-concepts]: concepts-network.md
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-internal]: ingress-internal-ip.md
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[network-policy]: use-network-policies.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[network-comparisons]: concepts-network.md#compare-network-models
[system-node-pools]: use-system-pools.md
