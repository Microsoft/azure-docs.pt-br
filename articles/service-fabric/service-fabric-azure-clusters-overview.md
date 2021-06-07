---
title: Criar clusters no Windows Server e no Linux
description: Service Fabric clusters são executados no Windows Server e no Linux. Você pode implantar e hospedar aplicativos Service Fabric em qualquer lugar em que possa executar o Windows Server ou o Linux.
services: service-fabric
documentationcenter: .net
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: bbfdc0a30aa673e8602ec9233fde4236c99ef5aa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97882204"
---
# <a name="overview-of-service-fabric-clusters-on-azure"></a>Visão geral dos clusters do Service Fabric no Azure
Um cluster do Service Fabric é um conjunto de computadores físicos ou virtuais conectados via rede, nos quais os microsserviços são implantados e gerenciados. Um computador ou VM que faz parte de um cluster é chamado de nó de cluster. Os clusters podem ser dimensionados para milhares de nós. Se você adiciona novos nós ao cluster, o Service Fabric reequilibra as réplicas de partição de serviço e instâncias entre o número aumentado de nós. O desempenho geral do aplicativo é melhorado e a contenção para o acesso à memória é reduzida. Se os nós no cluster não estiverem sendo usados com eficiência, você poderá diminuir o número de nós no cluster. O Service Fabric redistribui novamente as réplicas de partição e instâncias entre o número reduzido de nós, para fazer melhor uso do hardware em cada nó.

O tipo de nó define o tamanho, o número e as propriedades de um conjunto de nós (máquinas virtuais) no cluster. Cada tipo de nó pode ser escalado verticalmente para cima ou para baixo de forma independente, tem conjuntos diferentes de portas abertas e pode ter métricas de capacidade diferente. Os tipos de nós são usados na definição de funções para um conjunto de nós de cluster, como "front-end" ou "back-end". Seu cluster pode ter mais de um tipo de nó, mas o tipo de nó primário deve ter pelo menos cinco VMs para clusters de produção (ou pelo menos três VMs para clusters de teste). [Os serviços de sistema do Service Fabric](service-fabric-technical-overview.md#system-services) são colocados nos nós do tipo primário. 

## <a name="cluster-components-and-resources"></a>Recursos e componentes do cluster
O cluster do Service Fabric no Azure é um recurso do Azure que usa e interage com outros recursos do Azure:
* Placas de rede virtual e de VMs
* conjuntos de dimensionamento de máquinas virtuais
* redes virtuais
* balanceadores de carga
* contas de armazenamento
* endereços IP públicos

![Cluster do Service Fabric][Image]

### <a name="virtual-machine"></a>Máquina virtual
Uma [máquina virtual](../virtual-machines/index.yml) que é parte de um cluster é chamada de nó, embora, tecnicamente, um nó de cluster seja um processo de runtime do Service Fabric. Cada nó recebe um nome de nó (uma cadeia de caracteres). Os nós têm características, como as [propriedades de posicionamento](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints). Cada máquina ou VM possui um serviço de início automático, *FabricHost.exe*, que começa a ser executado na inicialização e, depois, inicia dois executáveis: *Fabric.exe* e *FabricGateway.exe*, que compõem o nó. Uma implantação de produção é um nó por máquina virtual ou física. Para cenários de teste, é possível hospedar vários nós em um único computador ou VM, executando várias instâncias do *Fabric.exe* e do *FabricGateway.exe*.

Cada VM é associada a uma placa de interface de rede virtual (NIC), e a cada NIC é atribuído um endereço IP privado.  Uma VM é atribuída a uma rede virtual e a um balanceador local por meio da NIC.

Todas as VMs em um cluster são colocadas em uma rede virtual.  Todos os nós no mesmo tipo de nó/escala de dimensionamento são colocados na mesma sub-rede na rede virtual.  Esses nós têm somente endereços IP privados e não são diretamente endereçáveis de fora da rede virtual.  Os clientes podem acessar os serviços nos nós por meio do balanceador de carga do Azure.

### <a name="scale-setnode-type"></a>Conjunto de dimensionamento/Tipo de nó
Ao criar um cluster, você define um ou mais tipos de nó.  Os nós, ou VMs, em um tipo de nó têm o mesmo tamanho e características, como o número de CPUs, memória, número de discos e disco I/O.  Por exemplo, um tipo de nó pode ser para VMs pequenas, VMs de front-end com portas abertas para a internet, enquanto o outro tipo de nó pode ser para VMs grandes e VMs de back-end que processam dados. Nos clusters do Azure, cada tipo de nó é mapeado para um [conjunto de dimensionamento de máquinas virtuais](../virtual-machine-scale-sets/index.yml).

Você pode usar os conjuntos de dimensionamento para implantar e gerenciar uma coleção de máquinas virtuais como um conjunto. Cada tipo de nó definido em um cluster do Azure Service Fabric configura uma escala de dimensionamento separada. O runtime do Service Fabric é inicializado em cada máquina virtual no conjunto de dimensionamento usando as extensões de VM do Azure. Cada tipo de nó pode ser escalado vertical ou horizontalmente de forma independente, ter a SKU de sistema operacional em execução em cada nó de cluster, ter conjuntos diferentes de portas abertas e usar métricas de capacidade diferentes. Um conjunto de dimensionamento tem cinco [domínios de atualização](service-fabric-cluster-resource-manager-cluster-description.md#upgrade-domains) e cinco [domínios de falha](service-fabric-cluster-resource-manager-cluster-description.md#fault-domains) e pode ter até 100 VMs.  É possível criar clusters de mais de 100 nós, criando vários tipos de nó/conjuntos de dimensionamento.

> [!IMPORTANT]
> Escolher o número de tipos de nó do cluster e as propriedades de cada tipo de nó (tamanho, primário, voltado para a internet, número de VMs, etc.) é uma tarefa importante.  Para saber mais, confira as [considerações de planejamento de capacidade de cluster](service-fabric-cluster-capacity.md).

Para saber mais, confira os [tipos de nós do Service Fabric e os conjuntos de dimensionamento de máquina virtual](service-fabric-cluster-nodetypes.md).

### <a name="azure-load-balancer"></a>Azure Load Balancer
As instâncias de VM são unidas atrás de um [balanceador de carga do Azure](../load-balancer/load-balancer-overview.md), que está associado a um [endereço IP público](../virtual-network/public-ip-addresses.md) e a um rótulo de DNS.  Quando você provisiona um cluster com *&lt; ClusterName &gt;*, o nome DNS, *&lt; ClusterName &gt; . &lt; Location &gt; . cloudapp.Azure.com* é o rótulo DNS associado ao balanceador de carga na frente do conjunto de dimensionamento.

As VMs em um cluster têm apenas [endereços IP privados](../virtual-network/private-ip-addresses.md).  O tráfego de gerenciamento e de serviço são roteados por meio do balanceador de carga voltado ao público.  O tráfego de rede é roteado para essas máquinas por meio de regras NAT (os clientes se conectam a nós/instâncias específicas) ou de regras de balanceamento de carga (o tráfego vai para as VMs round robin).  Um balanceador de carga tem um IP público associado com um nome DNS no formato: *&lt; ClusterName &gt; . &lt; Location &gt; . cloudapp.Azure.com*.  Um IP público é outro recurso do Azure no grupo de recursos.  Se você definir vários tipos de nós em um cluster, é criado um balanceador de carga para cada tipo de nó/conjunto de dimensionamento. Ou, é possível configurar um balanceador de carga único para vários tipos de nó.  O tipo de nó primário tem o rótulo DNS *&lt; ClusterName &gt; . &lt; Location &gt; . cloudapp.Azure.com*, outros tipos de nó têm o rótulo DNS *&lt; ClusterName &gt; - &lt; NodeType &gt; . &lt; Location &gt; . cloudapp.Azure.com*.

### <a name="storage-accounts"></a>Contas de armazenamento
A [conta de armazenamento do Azure](../storage/common/storage-introduction.md) e os Managed Disks oferecem suporte a cada tipo de nó de cluster.

## <a name="cluster-security"></a>Segurança de cluster
Um cluster do Service Fabric é um recurso que pertence a você.  É sua responsabilidade proteger os clusters para ajudar a impedir que usuários não autorizados se conectem a eles. Um cluster seguro é especialmente importante quando você está executando cargas de trabalho de produção no cluster. 

### <a name="node-to-node-security"></a>Segurança de nó para nó
A segurança de nó para nó protege a comunicação entre as VMs ou os computadores em um cluster. Esse cenário de segurança faz com que somente os computadores autorizados a ingressar no cluster possam hospedar aplicativos e serviços no cluster. O Service Fabric usa certificados x.509 para proteger um cluster e fornecer recursos de segurança do aplicativo.  É necessário um certificado de cluster para proteger o tráfego de cluster e fornecer autenticação de servidor e de cluster.  Os certificados autoassinados podem ser usados para clusters de teste, mas deve ser usado um certificado de uma autoridade de certificação confiável para proteger clusters de produção.

Para saber mais, confira a [segurança de nó para nó](service-fabric-cluster-security.md#node-to-node-security)

### <a name="client-to-node-security"></a>Segurança de cliente para nó
A segurança de cliente para nó autentica clientes e ajuda a proteger a comunicação entre um cliente e nós individuais no cluster. Esse tipo de segurança ajuda a assegurar que somente usuários autorizados possam acessar o cluster e os aplicativos implantados no cluster. Os clientes são identificados exclusivamente por meio de suas credenciais de segurança do certificado X.509. Qualquer número de certificados de cliente opcionais podem ser usados para autenticar clientes usuários ou administradores no cluster.

Além de certificados de cliente, o Azure Active Directory também pode ser configurado para autenticar clientes no cluster.

Para saber mais, leia [Segurança de cliente para nó](service-fabric-cluster-security.md#client-to-node-security)

### <a name="role-based-access-control"></a>Controle de acesso baseado em funções
O Azure RBAC (controle de acesso baseado em função) do Azure permite que você atribua controles de acesso refinados nos recursos do Azure.  Você pode atribuir regras de acesso diferentes para assinaturas, grupos de recursos e recursos.  As regras RBAC do Azure são herdadas ao longo da hierarquia de recursos, a menos que sejam substituídas em um nível inferior.  Você pode atribuir qualquer usuário ou grupo de usuários em seu AAD com as regras de RBAC do Azure para que usuários e grupos designados possam modificar o cluster.  Para saber mais, confira a [visão geral do RBAC do Azure](../role-based-access-control/overview.md).

O Service Fabric também dá suporte ao controle de acesso para limitar o acesso a determinadas operações de cluster para diferentes grupos de usuários. Isso ajuda a tornar o cluster mais seguro. Dois tipos de controle de acesso têm suporte para clientes que se conectam a um cluster: função de Administrador e função de Usuário.  

Para obter mais informações, leia [Service Fabric controle de acesso baseado em função](service-fabric-cluster-security.md#service-fabric-role-based-access-control).

### <a name="network-security-groups"></a>Grupos de segurança de rede 
Grupos de segurança de rede (NSGs) controlam o tráfego de entrada e de saída de uma sub-rede, VM ou NIC específica.  Por padrão, quando várias VMs são colocadas na mesma rede virtual, elas podem se comunicar entre si por meio de qualquer porta.  Se quiser restringir a comunicação entre as máquinas, é possível definir os NSGs para segmentar a rede ou isolar as VMs umas das outras.  Se tiver vários tipos de nó em um cluster, é possível aplicar os NSGs a sub-redes para impedir que as máquinas que pertencem a diferentes tipos de nó se comuniquem entre si.  

Para saber mais, confira o artigo sobre os [Grupos de segurança](../virtual-network/network-security-groups-overview.md)

## <a name="scaling"></a>Dimensionamento

As demandas de aplicativos alteram ao longo do tempo. Talvez seja necessário aumentar os recursos de cluster para atender ao aumento do tráfego ou de carga de trabalho do aplicativo ou diminuir os recursos de cluster quando houver queda de demanda. Após criar um cluster do Service Fabric, será possível dimensionar o cluster horizontalmente (alterar o número de nós) ou verticalmente (alterar os recursos dos nós). É possível dimensionar o cluster a qualquer momento, mesmo quando as cargas de trabalho estiverem em execução no cluster. Na medida em que o cluster for dimensionado, os aplicativos também serão dimensionados automaticamente.

Para saber mais, confira o artigo sobre o [Dimensionamento de clusters do Azure](service-fabric-cluster-scaling.md).

## <a name="upgrading"></a>Atualizando
Um cluster do Azure Service Fabric é um recurso cujo proprietário é você, mas que é parcialmente gerenciado pela Microsoft. A Microsoft é responsável por aplicar patch ao sistema operacional subjacente e realizar atualizações de runtime do Service Fabric em seu cluster. É possível configurar seu cluster para receber atualizações automáticas de runtime quando a Microsoft lançar uma nova versão ou é possível optar por selecionar uma versão de runtime com suporte que você queira. Além das atualizações de runtime, também é possível atualizar a configuração do cluster, como certificados ou portas de aplicativo.

Para saber mais, leia [Upgrade de clusters](service-fabric-cluster-upgrade.md).

## <a name="supported-operating-systems"></a>Sistemas operacionais com suporte
É possível criar clusters em máquinas virtuais que executem estes sistemas operacionais:

| Sistema operacional | Versão de Service Fabric mais antiga com suporte | Última versão de Service Fabric com suporte |
| --- | --- | --- | 
| Windows Server 2019 | 6.4.654.9590 | N/D |
| Windows Server 2016 | Todas as versões | N/D |
| 20H2 do Windows Server | 7.2.445.9590 | N/D |
| Windows Server 1809 | 6.4.654.9590 | 7.2.445.9590 |
| Windows Server 1803 | 6.4 | 7.2.445.9590 |
| Windows Server 1709 | 6.0 | 7.2.445.9590 |
| Windows Server 2012 | Todas as versões | N/D | 
| Linux Ubuntu 16.04 | 6.0 | N/D |
| Linux Ubuntu 18, 4 | 7.1 | N/D |

Para obter informações adicionais, consulte [versões de cluster com suporte no Azure](./service-fabric-versions.md#supported-operating-systems)

> [!NOTE]
> Se você decidir implantar o Service Fabric no Windows Server 1709, note que (1) ele não é um branch de manutenção de longo prazo, então, talvez será necessário mover versões futuramente, e (2) se você implantar contêineres, os contêineres compilados no Windows Server 2016 não funcionarão no Windows Server 1709 e vice-versa (será necessário recompilar para implementá-los).
>


## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre como [proteger](service-fabric-cluster-security.md), [dimensionar](service-fabric-cluster-scaling.md) e [fazer upgrade](service-fabric-cluster-upgrade.md) de clusters do Azure.

Saiba mais sobre as [opções de suporte do Service Fabric](service-fabric-support.md).

[Image]: media/service-fabric-azure-clusters-overview/Cluster.PNG