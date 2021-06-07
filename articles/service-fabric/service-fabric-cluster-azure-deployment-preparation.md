---
title: Planejar uma implantação de Cluster Service Fabric do Azure
description: Saiba mais sobre planejamento e preparação para uma implantação de cluster de Service Fabric de produção no Azure.
ms.topic: conceptual
ms.date: 03/20/2019
ms.openlocfilehash: 82521487b9a3e9438784e010a32cf6df8e7be2ef
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105046310"
---
# <a name="plan-and-prepare-for-a-cluster-deployment"></a>Planejar e preparar uma implantação de cluster

O planejamento e a preparação para uma implantação de cluster de produção são muito importantes.  Há muitos fatores a serem considerados.  Este artigo orienta você pelas etapas de preparação de sua implantação de cluster.

## <a name="read-the-best-practices-information"></a>Leia as informações de práticas recomendadas
Para gerenciar aplicativos e clusters do Azure Service Fabric com êxito, há operações que recomendamos que você execute para otimizar a confiabilidade do seu ambiente de produção.  Para obter mais informações, leia [Service Fabric práticas recomendadas de aplicativo e cluster](./service-fabric-best-practices-security.md).

## <a name="select-the-os-for-the-cluster"></a>Selecione o sistema operacional para o cluster
O Service Fabric permite a criação de clusters do Service Fabric em qualquer VM ou computador que esteja executando o Windows Server ou o Linux.  Antes de implantar o cluster, você deve escolher o sistema operacional: Windows ou Linux.  Cada nó (máquina virtual) no cluster executa o mesmo sistema operacional, não é possível misturar VMs Windows e Linux no mesmo cluster.

## <a name="capacity-planning"></a>planejamento de capacidade
Para qualquer implantação de produção, o planejamento de capacidade é uma etapa importante. Aqui estão alguns pontos a serem considerados como parte desse processo.

* O número inicial de tipos de nó para o cluster 
* As propriedades de cada tipo de nó (tamanho, número de instâncias, primária, voltada para a Internet, número de VMs, etc.)
* As características de confiabilidade e durabilidade do cluster

### <a name="select-the-initial-number-of-node-types"></a>Selecionar o número inicial de tipos de nó
Primeiro, é necessário descobrir para que o cluster que você está criando será utilizado. Que tipos de aplicativos você planeja implantar nesse cluster? O aplicativo tem vários serviços, e algum deles precisa ser público ou voltado para a Internet? Os serviços (que compõem o aplicativo) têm necessidades de infraestrutura diferentes, como maior RAM ou mais ciclos de CPU? Um Cluster Service Fabric pode consistir em mais de um tipo de nó: um tipo de nó primário e um ou mais tipos de nós não primários. Cada tipo de nó é mapeado para um conjunto de dimensionamento de máquinas virtuais. Cada tipo de nó pode ser escalado verticalmente para cima ou para baixo de forma independente, tem conjuntos diferentes de portas abertas e pode ter métricas de capacidade diferente. [As propriedades de nó e as restrições de posicionamento][placementconstraints] podem ser configuradas para restringir serviços específicos a tipos de nó específicos.  Para obter mais informações, consulte [Service Fabric planejamento de capacidade de cluster](service-fabric-cluster-capacity.md).

### <a name="select-node-properties-for-each-node-type"></a>Selecionar Propriedades de nó para cada tipo de nó
Os tipos de nó definem a SKU, o número e as propriedades da VM no conjunto de dimensionamento associado.

O tamanho mínimo das VMs para cada tipo de nó é determinado pela [camada de durabilidade][durability] que você escolhe para o tipo de nó.

O número mínimo de VMs para o tipo de nó primário é determinado pela [camada de confiabilidade][reliability] que você escolhe.

Consulte as recomendações mínimas para [tipos de nós primários](service-fabric-cluster-capacity.md#primary-node-type), [cargas de trabalho com estado em tipos de nós não primários](service-fabric-cluster-capacity.md#stateful-workloads)e [cargas de trabalho sem estado em tipos de nós não primários](service-fabric-cluster-capacity.md#stateless-workloads).

Um número mínimo de nós deve ser baseado no número de réplicas do aplicativo/serviços que você deseja executar nesse tipo de nó.  O [planejamento de capacidade para aplicativos Service Fabric](service-fabric-capacity-planning.md) ajuda a estimar os recursos de que você precisa para executar seus aplicativos. Você sempre pode dimensionar o cluster para cima ou para baixo mais tarde para ajustar a carga de trabalho do aplicativo em alteração. 

#### <a name="use-ephemeral-os-disks-for-virtual-machine-scale-sets"></a>Usar discos do sistema operacional efêmero para conjuntos de dimensionamento de máquinas virtuais

*Os discos do sistema operacional efêmero* são o armazenamento criado na VM (máquina virtual) local e não são salvos no armazenamento remoto do Azure. Eles são recomendados para todos os tipos de nó de Service Fabric (primário e secundário), porque comparados aos discos do sistema operacional persistente tradicional, discos do sistema operacional efêmero:

* Reduzir a latência de leitura/gravação para o disco do sistema operacional
* Habilitar operações mais rápidas de gerenciamento de nó de redefinição/reimagem
* Reduzir os custos gerais (os discos são gratuitos e não incorrem nenhum custo de armazenamento adicional)

OS discos do sistema operacional efêmero não são um recurso Service Fabric específico, mas sim um recurso dos *conjuntos de dimensionamento de máquinas virtuais* do Azure que são mapeados para Service Fabric tipos de nó. Usá-los com Service Fabric requer o seguinte no modelo de Azure Resource Manager de cluster:

1. Verifique se os tipos de nó especificam [tamanhos de VM do Azure com suporte](../virtual-machines/ephemeral-os-disks.md) para discos do sistema operacional efêmero e se o tamanho da VM tem tamanho de cache suficiente para dar suporte ao tamanho do disco do sistema operacional (veja a *Observação* abaixo). Por exemplo:

    ```xml
    "vmNodeType1Size": {
        "type": "string",
        "defaultValue": "Standard_DS3_v2"
    ```

    > [!NOTE]
    > Certifique-se de selecionar um tamanho de VM com um tamanho de cache igual ou maior que o tamanho de disco do sistema operacional da própria VM; caso contrário, sua implantação do Azure poderá resultar em erro (mesmo que ele seja aceito inicialmente).

2. Especifique uma versão do conjunto de dimensionamento de máquinas virtuais ( `vmssApiVersion` ) do `2018-06-01` ou posterior:

    ```xml
    "variables": {
        "vmssApiVersion": "2018-06-01",
    ```

3. Na seção conjunto de dimensionamento de máquinas virtuais do seu modelo de implantação, especifique a `Local` opção para `diffDiskSettings` :

    ```xml
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
        "virtualMachineProfile": {
            "storageProfile": {
                "osDisk": {
                        "caching": "ReadOnly",
                        "createOption": "FromImage",
                        "diffDiskSettings": {
                            "option": "Local"
                        },
                }
            }
        }
    ```

> [!NOTE]
> Os aplicativos de usuário não devem ter nenhuma dependência/arquivo/artefato no disco do sistema operacional, pois o disco do sistema operacional seria perdido no caso de uma atualização do sistema operacional.

> [!NOTE]
> O VMSS não efêmero existente não pode ser atualizado in-loco para usar discos efêmeros.
> Para migrar, os usuários terão que [Adicionar](./virtual-machine-scale-set-scale-node-type-scale-out.md) um novo NodeType com discos efêmeros, mover as cargas de trabalho para o novo NodeType & [remover](./service-fabric-how-to-remove-node-type.md) o NodeType existente.
>

Para obter mais informações e opções de configuração adicionais, consulte [discos do sistema operacional efêmero para VMs do Azure](../virtual-machines/ephemeral-os-disks.md) 


### <a name="select-the-durability-and-reliability-levels-for-the-cluster"></a>Selecione os níveis de durabilidade e confiabilidade para o cluster
A camada de durabilidade é usada para indicar ao sistema os privilégios que as VMs têm com a infraestrutura subjacente do Azure. No tipo de nó primário, esse privilégio permite que o Service Fabric pause qualquer solicitação de infraestrutura de nível de VM (por exemplo, reinicialização de VM, recriação de imagem de VM ou migração de VM) que afete os requisitos de quorum para os serviços do sistema e os serviços com estado. Nos tipos de nó não primários, esse privilégio permite que o Service Fabric pause quaisquer solicitações de infraestrutura de nível de VM (como reinicialização de VM, reinicialização de VM e migração de VM) que afetem os requisitos de quorum para os serviços com estado.  Para obter as vantagens dos diferentes níveis e recomendações sobre qual nível usar e quando, consulte [as características de durabilidade do cluster][durability].

A camada de confiabilidade é usada para definir o número de réplicas dos serviços do sistema que você deseja executar nesse cluster no tipo de nó primário. Quanto maior for o número de réplicas, mais confiáveis os serviços do sistema serão no cluster.  Para obter as vantagens dos diferentes níveis e recomendações sobre qual nível usar e quando, consulte [as características de confiabilidade do cluster][reliability]. 

## <a name="enable-reverse-proxy-andor-dns"></a>Habilitar proxy reverso e/ou DNS
Geralmente, os serviços que se conectam entre si em um cluster podem acessar diretamente os pontos de extremidade de outros serviços, pois os nós de um cluster estão na mesma rede local. Para facilitar a conexão entre os serviços do, Service Fabric fornece serviços adicionais: um [serviço DNS](service-fabric-dnsservice.md) e um [serviço de proxy reverso](service-fabric-reverseproxy.md).  Ambos os serviços podem ser habilitados durante a implantação de um cluster.

Como muitos serviços, especialmente serviços em contêineres, podem ter um nome de URL existente, poder resolvê-los usando o protocolo DNS padrão (em vez do protocolo Serviço de Nomenclatura) é conveniente, especialmente em cenários de "desvio e deslocamento" do aplicativo. Isso é exatamente o que o serviço DNS faz. Ele permite mapear nomes DNS para um nome de serviço e, portanto, resolver endereços IP do ponto de extremidade.

O proxy reverso lida com os serviços no cluster que expõem pontos de extremidade HTTP (incluindo HTTPS). O proxy reverso simplifica muito a chamada de outros serviços, fornecendo um formato de URI específico.  O proxy reverso também lida com as etapas de resolução, conexão e repetição necessárias para que um serviço se comunique com outro.

## <a name="prepare-for-disaster-recovery"></a>Preparar para a recuperação de desastre
Uma parte crítica da entrega de alta disponibilidade é garantir que os serviços possam sobreviver a todos os tipos diferentes de falhas. Isso é especialmente importante para falhas não planejadas e fora de seu controle. [Preparar para a recuperação de desastres](service-fabric-disaster-recovery.md) descreve alguns modos de falha comuns que poderiam ser desastres se não forem modelados e gerenciados corretamente. Ele também aborda atenuações e ações a serem tomadas se um desastre ocorrer mesmo assim.

## <a name="production-readiness-checklist"></a>Lista de verificação de preparação para produção
O aplicativo e o cluster estão prontos para receber tráfego de produção? Antes de implantar o cluster para produção, execute a [lista de verificação de preparação de produção](service-fabric-production-readiness-checklist.md). Mantenha seu aplicativo e o cluster em execução sem problemas trabalhando nos itens desta lista de verificação. É altamente recomendável que todos esses itens sejam verificados antes de entrar em produção.

## <a name="next-steps"></a>Próximas etapas
* [Criar um Cluster Service Fabric executando o Windows](./service-fabric-best-practices-security.md)
* [Criar um Cluster Service Fabric executando o Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md)

[placementconstraints]: service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints
[durability]: service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster
[reliability]: service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster