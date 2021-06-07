---
title: Considerações de planejamento de capacidade de cluster do Service Fabric
description: Tipos de nó, durabilidade, confiabilidade e outras coisas a serem consideradas ao planejar seu Service Fabric cluster.
ms.topic: conceptual
ms.date: 05/21/2020
ms.author: pepogors
ms.openlocfilehash: b3361337bb0cf60e47efe198aad7aa8cc20ae7b3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101714928"
---
# <a name="service-fabric-cluster-capacity-planning-considerations"></a>Considerações de planejamento de capacidade de cluster do Service Fabric

O planejamento da capacidade do cluster é importante para cada ambiente de produção Service Fabric. As principais considerações incluem:

* **Número inicial e propriedades de *tipos de nó* de cluster**

* **Nível de *durabilidade* de cada tipo de nó**, que determina Service Fabric privilégios de VM na infraestrutura do Azure

* **Nível de *confiabilidade* do cluster**, que determina a estabilidade dos serviços do sistema Service Fabric e a função de cluster geral

Este artigo explicará os pontos de decisão significativos para cada uma dessas áreas.

## <a name="initial-number-and-properties-of-cluster-node-types"></a>Número inicial e propriedades de tipos de nó de cluster

Um *tipo de nó* define o tamanho, o número e as propriedades de um conjunto de nós (máquinas virtuais) no cluster. Cada tipo de nó definido em um cluster do Service Fabric mapeia para um [conjunto de dimensionamento de máquinas virtuais](../virtual-machine-scale-sets/overview.md).

Como cada tipo de nó é um conjunto de dimensionamento distinto, ele pode ser aumentado ou reduzido de forma independente, ter diferentes conjuntos de portas abertas e ter métricas de capacidade diferentes. Para obter mais informações sobre a relação entre os tipos de nó e os conjuntos de dimensionamento de máquinas virtuais, consulte [Service Fabric tipos de nó de cluster](service-fabric-cluster-nodetypes.md).

Cada cluster requer um **tipo de nó primário**, que executa serviços de sistema críticos que fornecem Service Fabric recursos de plataforma. Embora seja possível também usar tipos de nó primário para executar seus aplicativos, é recomendável dedicar-os exclusivamente à execução de serviços do sistema.

**Tipos de nó não primários** podem ser usados para definir funções de aplicativo (como serviços de *front-end* e *back-end* ) e para isolar fisicamente os serviços em um cluster. Service Fabric clusters podem ter zero ou mais tipos de nós não primários.

O tipo de nó primário é configurado usando o `isPrimary` atributo sob a definição de tipo de nó no modelo de implantação Azure Resource Manager. Consulte o [objeto NodeTypeDescription](/azure/templates/microsoft.servicefabric/clusters#nodetypedescription-object) para obter a lista completa de propriedades de tipo de nó. Por exemplo, para uso, abra qualquer *AzureDeploy.jsno* arquivo em [exemplos de Cluster Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/) e *encontre na* pesquisa de página pelo `nodeTypes` objeto.

### <a name="node-type-planning-considerations"></a>Considerações de planejamento do tipo de nó

O número de tipos de nós iniciais depende da finalidade do cluster e dos aplicativos e serviços em execução nele. Considere as seguintes perguntas:

* ***O aplicativo tem vários serviços, e algum deles precisa ser público ou voltado para a Internet?***

    Os aplicativos típicos contêm um serviço de gateway de front-end que recebe entrada de um cliente e um ou mais serviços de back-end que se comunicam com os serviços de front-end, com rede separada entre os serviços de front-end e back-end. Esses casos normalmente exigem três tipos de nó: um tipo de nó primário e dois tipos de nó não primário (um para o serviço front e back-end).

* ***Os serviços que compõem seu aplicativo têm necessidades de infraestrutura diferentes, como maior RAM ou mais ciclos de CPU?***

    Geralmente, o serviço de front-end pode ser executado em VMs menores (tamanhos de VM, como D2) que têm portas abertas para a Internet.  Serviços de back-end de computação intensiva podem precisar ser executados em VMs maiores (com tamanhos de VM como D4, D6, D15) que não são voltados para a Internet. Definir diferentes tipos de nó para esses serviços permite que você faça uso mais eficiente e seguro de VMs de Service Fabric subjacentes e permite que elas as dimensionem de forma independente. Para saber mais sobre como estimar a quantidade de recursos que você precisa, consulte [planejamento de capacidade para aplicativos Service Fabric](service-fabric-capacity-planning.md)

* ***Qualquer um dos seus serviços de aplicativo precisará escalar horizontalmente além de 100 nós?***

    Um tipo de nó único não pode ser dimensionado de maneira confiável além de 100 nós por conjunto de dimensionamento de máquinas virtuais para aplicativos Service Fabric. A execução de mais de 100 nós requer conjuntos de dimensionamento de máquinas virtuais adicionais (e, portanto, tipos de nó adicionais).

* ***O cluster se estenderá por Zonas de Disponibilidade?***

    O Service Fabric dá suporte a clusters que se estendem por [zonas de disponibilidade](../availability-zones/az-overview.md) implantando tipos de nós que são fixados em zonas específicas, garantindo a alta disponibilidade de seus aplicativos. Zonas de Disponibilidade exigir o planejamento de tipo de nó adicional e requisitos mínimos. Para obter detalhes, consulte [topologia recomendada para o tipo de nó primário de clusters de Service Fabric que abrangem entre zonas de disponibilidade](service-fabric-cross-availability-zones.md#recommended-topology-for-primary-node-type-of-azure-service-fabric-clusters-spanning-across-availability-zones). 

Ao determinar o número e as propriedades de tipos de nó para a criação inicial do cluster, tenha em mente que você sempre poderá adicionar, modificar ou remover tipos de nó (não primários) depois que o cluster for implantado. Os [tipos de nó primários também podem ser modificados](service-fabric-scale-up-primary-node-type.md) em clusters em execução (embora essas operações exijam uma grande quantidade de planejamento e cuidado em ambientes de produção).

Uma consideração adicional para suas propriedades de tipo de nó é nível de durabilidade, que determina os privilégios que as VMs de um tipo de nó têm na infraestrutura do Azure. Use o tamanho das VMs que você escolher para o cluster e a contagem de instâncias que você atribui para tipos de nós individuais para ajudar a determinar a camada de durabilidade apropriada para cada um dos tipos de nós, conforme descrito a seguir.

## <a name="durability-characteristics-of-the-cluster"></a>Características de durabilidade do cluster

O *nível de durabilidade* designa os privilégios que suas VMs Service Fabric têm com a infraestrutura subjacente do Azure. Esse privilégio permite que Service Fabric Pause qualquer solicitação de infraestrutura no nível da VM (como reinicialização, reimagem ou migração) que afete os requisitos de quorum para serviços do sistema Service Fabric e seus serviços com estado.

> [!IMPORTANT]
> O nível de durabilidade é definido por tipo de nó. Se não houver nenhum especificado, a camada *bronze* será usada, no entanto, não fornece atualizações automáticas do sistema operacional. A durabilidade *prateada* ou *ouro* é recomendada para cargas de trabalho de produção.

A tabela a seguir lista Service Fabric camadas de durabilidade, seus requisitos e capacidades.

| Camada de durabilidade  | Número mínimo necessário de VMs | Tamanhos de VM com suporte                                                                  | Atualizações que você faz no seu conjunto de dimensionamento de máquina virtual                               | Atualizações e manutenção iniciada pelo Azure                                                              | 
| ---------------- |  ----------------------------  | ---------------------------------------------------------------------------------- | ----------------------------------------------------------- | ------------------------------------------------------------------------------------------------------- |
| Ouro             | 5                              | Tamanhos de nó completo dedicados a um único cliente (por exemplo, L32s, GS5, G5, DS15_v2, D15_v2) | Pode ser atrasado até aprovado pelo cluster do Service Fabric | Pode ser pausado por 2 horas por domínio de atualização para permitir tempo adicional para que as réplicas se recuperem de falhas anteriores |
| Prata           | 5                              | VMs de núcleo único ou superior com pelo menos 50 GB de SSD local                      | Pode ser atrasado até aprovado pelo cluster do Service Fabric | Não pode ser atrasado por qualquer período de tempo significativo                                                    |
| Bronze          | 1                              | VMs com pelo menos 50 GB de SSD local                                              | Não será atrasado pelo cluster do Service Fabric           | Não pode ser atrasado por qualquer período de tempo significativo                                                    |

> [!NOTE]
> O número mínimo mencionado acima de VMs é um requisito necessário para cada nível de durabilidade. Temos validações in-loco, o que impedirá a criação ou modificação de máquinas virtuais conjuntos existentes que não atendam a esses requisitos.

> [!WARNING]
> Com durabilidade de bronze, a atualização automática da imagem do sistema operacional não está disponível. Embora o [aplicativo de orquestração de patch](service-fabric-patch-orchestration-application.md) (destinado apenas a clusters não hospedados no Azure) não seja *recomendado* para níveis de durabilidade prata ou maior, é sua única opção para automatizar atualizações do Windows com relação a Service Fabric domínios de atualização.

> [!IMPORTANT]
> Independentemente do nível de durabilidade, a execução de uma operação de [desalocação](/rest/api/compute/virtualmachinescalesets/deallocate) em um conjunto de dimensionamento de máquinas virtuais destruirá o cluster.

### <a name="bronze"></a>Bronze

Tipos de nó executados com durabilidade Bronze não têm nenhum privilégio. Isso significa que os trabalhos de infraestrutura que afetam suas cargas de trabalho com estado não serão interrompidos ou atrasados. Use a durabilidade bronze para tipos de nós que executam apenas cargas de trabalho sem estado. Para cargas de trabalho de produção, em execução Prata ou superior é recomendado.

### <a name="silver-and-gold"></a>Prata e ouro

Use a durabilidade prateada ou ouro para todos os tipos de nó que hospedam serviços com estado que você espera dimensionar com frequência e onde você deseja que as operações de implantação sejam atrasadas e que a capacidade seja reduzida em favor de simplificar o processo. Cenários de expansão não devem afetar sua escolha da camada de durabilidade.

#### <a name="advantages"></a>Vantagens

* Reduz o número de etapas necessárias para operações de expansão (desativação de nó e Remove-ServiceFabricNodeState são chamadas automaticamente).
* Reduz o risco de perda de dados devido a operações de alteração de tamanho de VM in-loco e operações de infraestrutura do Azure.

#### <a name="disadvantages"></a>Desvantagens

* As implantações em conjuntos de dimensionamento de máquinas virtuais e outros recursos do Azure relacionados podem atingir o tempo limite, atrasar ou ser totalmente bloqueados por problemas no cluster ou no nível de infraestrutura.
* Aumenta o número de [eventos de ciclo de vida de réplica](service-fabric-reliable-services-lifecycle.md) (por exemplo, trocas primárias) devido às desativações de nós automatizadas durante as operações de infraestrutura do Azure.
* Coloca os nós fora de serviço por períodos durante as atividades de manutenção de hardware ou atualizações de software da plataforma Azure. Você poderá ver os nós o com o status Desabilitando/Desabilitado durante essas atividades. Isso reduz a capacidade do cluster temporariamente, mas não deve afetar a disponibilidade do cluster ou dos aplicativos.

#### <a name="best-practices-for-silver-and-gold-durability-node-types"></a>Práticas recomendadas para tipos de nós de durabilidade prata e ouro

Siga estas recomendações para gerenciar tipos de nós com durabilidade prata ou ouro:

* Mantenha sempre a integridade do cluster e dos aplicativos e verifique se os aplicativos respondem a todos os [eventos de ciclo de vida de réplica do Serviço](service-fabric-reliable-services-lifecycle.md) (como quando a réplica sendo compilada está paralisada) de maneira oportuna.
* Adote maneiras mais seguras de fazer uma alteração no tamanho da VM (escalar verticalmente). Alterar o tamanho da VM de um conjunto de dimensionamento de máquinas virtuais requer planejamento cuidadoso e cuidado. Para obter detalhes, consulte [escalar verticalmente um tipo de nó de Service Fabric](service-fabric-scale-up-primary-node-type.md)
* Mantenha uma contagem mínima de cinco nós para qualquer conjunto de dimensionamento de máquinas virtuais que tenha o nível de durabilidade Ouro ou Prata habilitado. O cluster entrará em estado de erro se você reduzir abaixo desse limite e precisará limpar manualmente o estado ( `Remove-ServiceFabricNodeState` ) para os nós removidos.
* Cada escala de máquina virtual definida com nível de durabilidade Silver ou Gold deve ser mapeada para seu próprio tipo de nó no cluster do Service Fabric. O mapeamento de vários conjuntos de escala de máquinas virtuais para um único tipo de nó impedirá que a coordenação entre o cluster do Service Fabric e a infraestrutura do Azure funcione corretamente.
* Não exclua instâncias de VM aleatórias, sempre use a escala do conjunto de dimensionamento de máquinas virtuais no recurso. A exclusão de instâncias de VM aleatórias tem um potencial de criar desequilíbrios na instância de VM espalhada entre [domínios de atualização](service-fabric-cluster-resource-manager-cluster-description.md#upgrade-domains) e [domínios de falha](service-fabric-cluster-resource-manager-cluster-description.md#fault-domains). Esse desequilíbrio pode afetar negativamente a capacidade dos sistemas de balancear adequadamente a carga entre as instâncias de serviço/réplicas de serviço.
* Se estiver usando o dimensionamento automático, defina as regras de forma que a escala em (removendo as instâncias de VM) seja feita apenas um nó por vez. Redução de mais de uma instância em um momento não é segura.
* Se você excluir ou desalocar VMs no tipo de nó primário, nunca reduza a contagem de VMs alocadas abaixo do que a camada de confiabilidade requer. Essas operações serão bloqueadas indefinidamente em um conjunto de dimensionamento com um nível de durabilidade de Prata ou Ouro.

### <a name="changing-durability-levels"></a>Alterar os níveis de durabilidade

Dentro de determinadas restrições, o nível de durabilidade do tipo de nó pode ser ajustado:

* Os tipos de nó com níveis de durabilidade de prata ou ouro não podem ser desatualizados para bronze.
* A atualização do nível Bronze, Prata ou Ouro pode levar algumas horas.
* Ao alterar o nível de durabilidade, atualize-o na configuração de extensão de Service Fabric no recurso de conjunto de dimensionamento de máquinas virtuais e na definição de tipo de nó no recurso de Cluster Service Fabric. Estes valores devem ser correspondentes.

Outra consideração quando o planejamento de capacidade é o nível de confiabilidade do cluster, que determina a estabilidade dos serviços do sistema e o cluster geral, conforme descrito na próxima seção.

## <a name="reliability-characteristics-of-the-cluster"></a>Características de confiabilidade do cluster

O *nível de confiabilidade* do cluster determina o número de réplicas de serviços do sistema em execução no tipo de nó primário do cluster. Quanto mais réplicas, mais confiáveis são os serviços do sistema (e, portanto, o cluster como um todo).

> [!IMPORTANT]
> O nível de confiabilidade é definido no nível do cluster e determina o número mínimo de nós do tipo de nó primário. As cargas de trabalho de produção exigem um nível de confiabilidade de prata (maior ou igual a cinco nós) ou acima.  

A camada de confiabilidade pode ter os valores a seguir:

* **Platinum** -os serviços do sistema são executados com a contagem de conjuntos de réplicas de destino de nove
* **Ouro** -os serviços do sistema são executados com a contagem de conjuntos de réplicas de destino de sete
* **Prata** -serviços do sistema executados com a contagem de conjuntos de réplicas de destino de cinco
* **Bronze** – serviços do sistema executados com a contagem de conjuntos de réplicas de destino de três

Aqui está a recomendação sobre como escolher o nível de confiabilidade. O número de nós iniciais também é configurado para o número mínimo de nós para um nível de confiabilidade.


| **Número de nós** | **Camada de confiabilidade** |
| --- | --- |
| 1 | *Não especifique o `reliabilityLevel` parâmetro: o sistema o calcula.* |
| 3 | Bronze |
| 5 ou 6| Prata |
| 7 ou 8 | Ouro |
| 9 e superior | Platinum |

Quando você aumenta ou diminui o tamanho do cluster (a soma das instâncias de VM em todos os tipos de nó), considere atualizar a confiabilidade do cluster de uma camada para outra. Fazer isso dispara as atualizações de cluster necessárias para alterar a contagem de conjuntos de réplicas dos serviços do sistema. Aguarde a conclusão da atualização em andamento antes de fazer outras alterações no cluster, assim como adicionar nós.  Você pode monitorar o andamento da atualização no Service Fabric Explorer ou executando [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade)

### <a name="capacity-planning-for-reliability"></a>Planejamento de capacidade para confiabilidade

As necessidades de capacidade do cluster serão determinadas pela carga de trabalho e pelos requisitos de confiabilidade específicos. Esta seção fornece diretrizes gerais para ajudá-lo a começar a usar o planejamento de capacidade.

#### <a name="virtual-machine-sizing"></a>Dimensionamento de máquina virtual

**Para cargas de trabalho de produção, o tamanho recomendado da VM (SKU) é o [padrão D2_V2](../virtual-machines/dv2-dsv2-series.md) (ou equivalente) com um mínimo de 50 GB de SSD local, 2 núcleos e 4 GiB de memória.** Um mínimo de 50 GB de SSD local é recomendado; no entanto, algumas cargas de trabalho (como aquelas que executam contêineres do Windows) exigirão discos maiores. Ao escolher outros [tamanhos de VM](../virtual-machines/sizes-general.md) para cargas de trabalho de produção, tenha em mente as seguintes restrições:

- Não há suporte para tamanhos de VM de núcleo parcial como Standard a0.
- *Série a* Não há suporte para tamanhos de VM por motivos de desempenho.
- Não há suporte para VMs de baixa prioridade.

#### <a name="primary-node-type"></a>Tipo de nó primário

As **cargas de trabalho de produção** no Azure exigem um mínimo de cinco nós primários (instâncias de VM) e a camada de confiabilidade de prata. É recomendável dedicar o tipo de nó primário do cluster aos serviços do sistema e usar as restrições de posicionamento para implantar seu aplicativo em tipos de nó secundário.

**Cargas de trabalho de teste** no Azure podem executar no mínimo um ou três nós primários. Para configurar um cluster de um nó, certifique-se de que a `reliabilityLevel` configuração seja completamente omitida no seu modelo do Resource Manager (especificando um valor de cadeia de caracteres vazia para `reliabilityLevel` não é suficiente). Se você configurar o cluster de um nó configurado com portal do Azure, essa configuração será feita automaticamente.

> [!WARNING]
> Clusters de um nó são executados com uma configuração especial sem confiabilidade e onde não há suporte para o scale out.

#### <a name="non-primary-node-types"></a>Tipos de nó não primários

O número mínimo de nós para um tipo de nó não primário depende do nível de [durabilidade](#durability-characteristics-of-the-cluster) específico do tipo de nó. Você deve planejar o número de nós (e nível de durabilidade) com base no número de réplicas de aplicativos ou serviços que você deseja executar para o tipo de nó e dependendo se a carga de trabalho tem estado ou sem estado. Tenha em mente que você pode aumentar ou diminuir o número de VMs em um tipo de nó a qualquer momento depois de ter implantado o cluster.

##### <a name="stateful-workloads"></a>Cargas de trabalho com estado

Para cargas de trabalho de produção com estado usando Service Fabric [coleções confiáveis ou Reliable Actors](service-fabric-choose-framework.md), é recomendável uma contagem de réplicas mínima e de destino de cinco. Com isso, no estado Steady, você acaba com uma réplica (de um conjunto de réplicas) em cada domínio de falha e domínio de atualização. Em geral, use o nível de confiabilidade definido para serviços do sistema como um guia para a contagem de réplicas que você usa para seus serviços com estado.

##### <a name="stateless-workloads"></a>Cargas de trabalho sem estado

Para cargas de trabalho de produção sem monitoração de estado, o tamanho mínimo de tipo de nó não primário com suporte é de três para manter o quorum; no entanto, é recomendado um tamanho de tipo de nó de cinco.

## <a name="next-steps"></a>Próximas etapas

Antes de configurar o cluster, examine as `Not Allowed` [políticas de atualização de cluster](service-fabric-cluster-fabric-settings.md) para atenuar a recriação do cluster mais tarde, devido a configurações de sistema inalteráveis.

Para obter mais informações sobre o planejamento de cluster, consulte:

* [Planejamento e escala de computação](service-fabric-best-practices-capacity-scaling.md)
* [Planejamento de capacidade para Aplicativos do Service Fabric](service-fabric-capacity-planning.md)
* [Planejamento de recuperação de desastre](service-fabric-disaster-recovery.md)

<!--Image references-->
[SystemServices]: ./media/service-fabric-cluster-capacity/SystemServices.png
