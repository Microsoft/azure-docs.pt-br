---
title: Planejamento da capacidade de cluster do Service Fabric | Microsoft Docs
description: Considerações de planejamento de capacidade de cluster do Service Fabric. Camadas de nodetypes, operações, durabilidade e confiabilidade
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: chackdan
editor: ''
ms.assetid: 4c584f4a-cb1f-400c-b61f-1f797f11c982
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/09/2019
ms.author: chackdan
ms.openlocfilehash: 6b11a3ba4fbffe1d35b590f2e5c47f19b6fb028c
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67718122"
---
# <a name="service-fabric-cluster-capacity-planning-considerations"></a>Considerações de planejamento de capacidade de cluster do Service Fabric
Para qualquer implantação de produção, o planejamento de capacidade é uma etapa importante. Aqui estão alguns dos itens que você precisa considerar como parte desse processo.

* O número de tipos de nós com os quais o cluster precisa começar
* As propriedades de cada tipo de nó (tamanho, primário, voltado para a Internet, número de VMs etc.)
* As características de confiabilidade e durabilidade do cluster

> [!NOTE]
> Você deve revisar minimamente todos os valores da política de upgrade **Não permitido** durante o planejamento. Isso é para garantir que você defina os valores adequadamente e para reduzir a redução de seu cluster posteriormente devido a configurações imutáveis do sistema. 
> 

Vamos examinar rapidamente cada um desses itens.

## <a name="the-number-of-node-types-your-cluster-needs-to-start-out-with"></a>O número de tipos de nós com os quais o cluster precisa começar
Primeiro, é necessário descobrir para que o cluster que você está criando será utilizado.  Que tipos de aplicativos você planeja implantar nesse cluster? Se não tem certeza quanto à finalidade do cluster, você provavelmente ainda não está pronto para iniciar o processo de planejamento de capacidade.

Estabeleça o número de tipos de nós com os quais o cluster precisa começar.  Cada tipo de nó é mapeado para um conjunto de dimensionamento de máquinas virtuais. Cada tipo de nó pode ser escalado verticalmente para cima ou para baixo de forma independente, tem conjuntos diferentes de portas abertas e pode ter métricas de capacidade diferente. Portanto, a decisão quanto ao número de tipos de nós essencialmente se resume às seguintes considerações:

* O aplicativo tem vários serviços, e algum deles precisa ser público ou voltado para a Internet? Os aplicativos típicos contêm um serviço de gateway de front-end que recebe entrada de um cliente e um ou mais serviços de back-end que comunicam-se com os serviços de front-end. Nesse caso, você acaba tendo pelo menos dois tipos de nó.
* Os serviços (que compõem o aplicativo) têm necessidades de infraestrutura diferentes, como maior RAM ou mais ciclos de CPU? Por exemplo, vamos supor que o aplicativo que você deseja implantar contenha um serviço de front-end e um serviço de back-end. O serviço de front-end pode ser executado em VMs menores (com tamanhos de VM como D2) que têm portas abertas para a Internet.  No entanto, o serviço de back-end faz uso intensivo de computação e precisa ser executado em VMs maiores (com tamanhos de VM como D4, D6, D15) que não são voltadas para a Internet.
  
  No exemplo, embora você possa optar por colocar todos os serviços em um tipo de um nó, é recomendável colocá-los em um cluster com dois tipos de nó.  Isso permite que cada tipo de nó tenha propriedades distintas, como conectividade com a Internet ou tamanho de VM. O número de VMs também pode ser dimensionado independentemente.  
* Como você não pode prever o futuro, baseie-se no que já sabe e escolha quantos tipos de nós os aplicativos precisam começar. Você sempre pode adicionar ou remover tipos de nós posteriormente. Um cluster do Service Fabric deve ter pelo menos um tipo de nó.

## <a name="the-properties-of-each-node-type"></a>As propriedades de cada tipo de nó
O **tipo de nó** pode ser visto como algo equivalente a funções nos Serviços de Nuvem. Os tipos de nó definem os tamanhos e o número de VMs e suas propriedades. Cada tipo de nó definido em um cluster do Service Fabric mapeia para um [conjunto de dimensionamento de máquinas virtuais](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview).  
Cada tipo de nó é um conjunto de dimensionamento distinto e pode ser para escalar verticalmente ou reduzir verticalmente de forma independente, ter conjuntos diferentes de portas em aberto e ter métrica de capacidade diferente. Para obter mais informações sobre as relações entre os tipos de nó e conjuntos de dimensionamento de máquina virtual, como ao RDP em uma das instâncias, como abrir novo portas e assim por diante, consulte [tipos de nós de cluster do Service Fabric](service-fabric-cluster-nodetypes.md).

Um cluster do Service Fabric pode consistir em mais de um tipo de nó. Nesse caso, o cluster consiste em um tipo de nó primário e um ou mais tipos de nó não primário.

Um tipo de nó único não consegue dimensionar de modo confiável além de 100 nós por conjunto de dimensionamento de máquinas virtuais para aplicativos do SF. Obter mais de 100 nós de forma confiável exigirá que você adicione conjuntos de dimensionamento de máquinas virtuais adicionais.

### <a name="primary-node-type"></a>Tipo de nó primário

Os serviços do sistema do Service Fabric (por exemplo, o serviço do Gerenciador de Cluster ou o serviço de Repositório de Imagem) são colocados no tipo de nó primário. 

![Captura de tela de um cluster que possui dois tipos de nós][SystemServices]

* O **tamanho mínimo de VMs** para o tipo de nó primário é determinado pela **camada de durabilidade** que você escolhe. A camada de durabilidade padrão é Bronze. Consulte [As características de durabilidade do cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster) para obter mais detalhes.  
* O **número mínimo de VMs** para o tipo de nó primário é determinado pela **camada de confiabilidade** que você escolhe. A camada de confiabilidade padrão é Prata. Consulte [As características de confiabilidade do cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-reliability-characteristics-of-the-cluster) para obter mais detalhes.  

Do modelo do Azure Resource Manager, o tipo de nó primário é configurado com o atributo `isPrimary` na [definição de tipo de nó](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/clusters#nodetypedescription-object).

### <a name="non-primary-node-type"></a>Tipo de nó não primário

Em um cluster com vários tipos de nó, há um tipo de nó primário, e os demais serão não primários.

* O **tamanho mínimo de VMs** para tipos de nó não primários é determinado pela **camada de durabilidade** que você escolhe. A camada de durabilidade padrão é Bronze. Para obter mais informações, consulte [As características de durabilidade do cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster).  
* O **número mínimo de VMs** para tipos de nó não primários não é um. No entanto, você deve escolher esse número com base no número de réplicas do aplicativo/serviços que deseja executar nesse tipo de nó. O número de VMs em um tipo de nó pode ser aumentado após a implantação do cluster.

## <a name="the-durability-characteristics-of-the-cluster"></a>As características de durabilidade do cluster
A camada de durabilidade é usada para indicar ao sistema os privilégios que as VMs têm com a infraestrutura subjacente do Azure. No tipo de nó primário, esse privilégio permite que o Service Fabric pause qualquer solicitação de infraestrutura de nível de VM (por exemplo, reinicialização de VM, recriação de imagem de VM ou migração de VM) que afete os requisitos de quorum para os serviços do sistema e os serviços com estado. Nos tipos de nó não primários, esse privilégio permite que o Service Fabric pause quaisquer solicitações de infraestrutura de nível de VM (como reinicialização de VM, reinicialização de VM e migração de VM) que afetem os requisitos de quorum para os serviços com estado.

| Camada de durabilidade  | Número mínimo necessário de VMs | SKUs de VM com suporte                                                                  | Atualizações que você faz no seu conjunto de dimensionamento de máquina virtual                               | Atualizações e manutenção iniciada pelo Azure                                                              | 
| ---------------- |  ----------------------------  | ---------------------------------------------------------------------------------- | ----------------------------------------------------------- | ------------------------------------------------------------------------------------------------------- |
| Gold             | 5                              | SKUs de nó completo dedicado a um único cliente (por exemplo, L32s, GS5, G5, DS15_v2, D15_v2) | Pode ser atrasado até aprovado pelo cluster do Service Fabric | Pode ser pausado por 2 horas por UD para possibilitar tempo adicional para réplicas para recuperar de falhas anteriores |
| Silver           | 5                              | VMs de núcleo único ou superior com pelo menos 50 GB de SSD local                      | Pode ser atrasado até aprovado pelo cluster do Service Fabric | Não pode ser atrasado por qualquer período de tempo significativo                                                    |
| Bronze           | 1                              | VMs com pelo menos 50 GB de SSD local                                              | Não será atrasado pelo cluster do Service Fabric           | Não pode ser atrasado por qualquer período de tempo significativo                                                    |

> [!WARNING]
> Tipos de nó executados com durabilidade Bronze não têm _nenhum privilégio_. Isso significa que trabalhos de infraestrutura que afetam sus cargas de trabalho sem estado não serão interrompidas ou atrasadas, que podem afetar suas cargas de trabalho. Use somente Bronze para tipos de nós que executam somente cargas de trabalho sem estado. Para cargas de trabalho de produção, em execução Prata ou superior é recomendado. 
> 
> Independentemente de qualquer nível de durabilidade, a operação [Desalocação](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/deallocate) no Conjunto de Dimensionamento de VM destruirá o cluster

**Vantagens de usar os níveis de durabilidade Ouro ou Prata**
 
- Reduz o número de etapas necessárias em uma operação de redução horizontal (isto é, a desativação do nó e a chamada para Remove-ServiceFabricNodeState ocorrem de maneira automática).
- Reduz o risco de perda de dados decorrente de uma operação de alteração de SKU de VM in-loco iniciada pelo cliente ou de operações de infraestrutura do Azure.

**Desvantagens de usar os níveis de durabilidade Ouro ou Prata**
 
- As implantações no conjunto de dimensionamento de máquinas virtuais e outros recursos do Azure relacionados podem ser atrasadas, podem atingir o tempo limite ou podem ser totalmente bloqueadas por problemas no cluster ou no nível de infraestrutura. 
- Aumenta o número de [eventos de ciclo de vida de réplica](service-fabric-reliable-services-lifecycle.md) (por exemplo, trocas primárias) devido às desativações de nós automatizadas durante as operações de infraestrutura do Azure.
- Coloca os nós fora de serviço por períodos durante as atividades de manutenção de hardware ou atualizações de software da plataforma Azure. Você poderá ver os nós o com o status Desabilitando/Desabilitado durante essas atividades. Isso reduz a capacidade do cluster temporariamente, mas não deve afetar a disponibilidade do cluster ou dos aplicativos.

### <a name="recommendations-for-when-to-use-silver-or-gold-durability-levels"></a>Recomendações de quando usar os níveis de durabilidade Prata ou Ouro

Use a durabilidade Prata ou Gold para todos os tipos de nós que hospedam serviços com estado que você pretende reduzir horizontalmente (reduzir a contagem de instâncias VM) com frequência, mas prefere que as operações de implantação sejam atrasadas e que a capacidade seja reduzida para simplificar essas operações de redução horizontal. Os cenários de escalabilidade horizontal (adicionar instâncias de VMs) não fazem parte das opções da camada de durabilidade, somente os de redução horizontal.

### <a name="changing-durability-levels"></a>Alterar os níveis de durabilidade
- Os tipos de nós com níveis de durabilidade Prata ou Ouro não podem ser rebaixados para Bronze.
- A atualização do nível Bronze, Prata ou Ouro pode levar algumas horas.
- Ao alterar o nível de durabilidade, certifique-se de atualizá-lo na configuração da extensão do Service Fabric no recurso de conjunto de dimensionamento de máquinas virtuais e na definição de tipo de nó no recurso de cluster do Service Fabric. Estes valores devem ser correspondentes.

### <a name="operational-recommendations-for-the-node-type-that-you-have-set-to-silver-or-gold-durability-level"></a>Recomendações operacionais para o tipo de nó configurado para o nível de durabilidade Prata ou Ouro.

- Mantenha sempre a integridade do cluster e dos aplicativos e verifique se os aplicativos respondem a todos os [eventos de ciclo de vida de réplica do Serviço](service-fabric-reliable-services-lifecycle.md) (como quando a réplica sendo compilada está paralisada) de maneira oportuna.
- Adote maneiras mais seguras de alterar a SKU de VM (escalar/reduzir verticalmente): Alteração da SKU de VM de um conjunto de dimensionamento de máquina virtual requer um número de etapas e considerações. Veja o processo que você pode seguir para evitar problemas comuns.
    - **Para tipos de nó não primário:** É recomendável que você criar um novo conjunto de dimensionamento de máquinas virtuais, modificar a restrição de posicionamento de serviço para incluir o novo tipo de nó/conjunto de dimensionamento de máquina virtual e, em seguida, reduzir antigo escala conjunto instância contagem de máquina virtual como zero, um nó por vez (Isso é para garantir -se de que a remoção de nós não afete a confiabilidade do cluster).
    - **Para o tipo de nó primário:** Se você tiver selecionado o SKU de VM está na capacidade total e você gostaria de alterar para uma SKU de VM maiores, siga nosso orientações [dimensionamento vertical para um tipo de nó primário](https://docs.microsoft.com/azure/service-fabric/service-fabric-scale-up-node-type). 

- Mantenha uma contagem mínima de cinco nós para qualquer conjunto de dimensionamento de máquinas virtuais que tenha o nível de durabilidade Ouro ou Prata habilitado.
- Cada escala de máquina virtual definida com nível de durabilidade Silver ou Gold deve ser mapeada para seu próprio tipo de nó no cluster do Service Fabric. O mapeamento de vários conjuntos de escala de máquinas virtuais para um único tipo de nó impedirá que a coordenação entre o cluster do Service Fabric e a infraestrutura do Azure funcione corretamente.
- Não exclua instâncias aleatórias de VM, use sempre o recurso de reduzir verticalmente o conjunto de dimensionamento de máquinas virtuais. A exclusão de instâncias de VM aleatórias tem o potencial de criar desequilíbrios na difusão das instâncias de VM por UD e FD. Esse desequilíbrio pode afetar de maneira negativa a capacidade dos sistemas de executar um balanceamento de carga adequado entre as instâncias do serviço/réplicas do Serviço.
- Se usar o Dimensionamento Automático, defina as regras de modo que a redução horizontal (remoção de instâncias de VM) seja executada em um nó de cada vez. Redução de mais de uma instância em um momento não é segura.
- Ao excluir ou desalocar VMs no tipo de nó primário, você nunca deve reduzir a contagem de VMs alocadas abaixo do que requer o nível de confiabilidade. Essas operações serão bloqueadas indefinidamente em um conjunto de dimensionamento com um nível de durabilidade de Prata ou Ouro.

## <a name="the-reliability-characteristics-of-the-cluster"></a>As características de confiabilidade do cluster
A camada de confiabilidade é usada para definir o número de réplicas dos serviços do sistema que você deseja executar nesse cluster no tipo de nó primário. Quanto maior for o número de réplicas, mais confiáveis os serviços do sistema serão no cluster.  

A camada de confiabilidade pode ter os valores a seguir:

* Platina - Executar os serviços do sistema com uma contagem de set de réplicas de destino de sete
* Ouro - executar os serviços do sistema com uma contagem de conjuntos de réplicas de destino de sete
* Prata - executar os serviços do sistema com uma contagem de conjuntos de réplicas de destino de cinco 
* Bronze - executar os serviços do sistema com uma contagem de conjuntos de réplicas de destino de três

> [!NOTE]
> A camada de confiabilidade que você escolhe determina o número mínimo de nós que o tipo de nó primário deve ter. 
> 
> 

### <a name="recommendations-for-the-reliability-tier"></a>Recomendações para a camada de confiabilidade

Quando você aumenta ou diminui o tamanho do cluster (a soma das instâncias VM em todos os tipos de nó), você deve atualizar a confiabilidade do seu cluster de um nível para outro. Fazer isso dispara as atualizações de cluster necessárias para alterar a contagem de conjuntos de réplicas dos serviços do sistema. Aguarde a conclusão da atualização em andamento antes de fazer outras alterações no cluster, assim como adicionar nós.  Você pode monitorar o andamento da atualização no Service Fabric Explorer ou executando [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps)

Aqui está a recomendação sobre como escolher o nível de confiabilidade.  O número de nós iniciais também é configurado para o número mínimo de nós para um nível de confiabilidade.  Por exemplo, para um cluster com confiabilidade Gold, há sete nós de sementes.

| **Número de nós de cluster** | **Camada de confiabilidade** |
| --- | --- |
| 1 |Não especifique o parâmetro de camada de confiabilidade, o sistema calculará |
| 3 |Bronze |
| 5 ou 6|Silver |
| 7 ou 8 |Gold |
| 9 e superior |Platinum |

## <a name="primary-node-type---capacity-guidance"></a>Tipo de nó primário - diretrizes de capacidade

A seguir, as diretrizes de planejamento da capacidade do tipo de nó primário:

- **Número de instâncias de VM para executar qualquer carga de trabalho de produção no Azure:** Você deve especificar um tamanho mínimo do tipo de nó primário de 5 e uma camada de confiabilidade de prata.  
- **Número de instâncias de VM para executar cargas de trabalho de teste no Azure** Você pode especificar um tamanho mínimo de tipo de nó primário de 1 ou 3. O cluster de um nó é executado com uma configuração especial e, portanto, não há suporte para expansão desse cluster. O cluster de um nó não tem confiabilidade e assim, em seu modelo do Resource Manager, você precisa remover ou não especificar essa configuração (não definir o valor de configuração não é suficiente). Se você configurar o cluster de um nó configurado por meio do Portal, em seguida, a configuração será feita automaticamente. Não há suporte para clusters de um e três nós para executar cargas de trabalho de produção. 
- **SKU DE VM:** Tipo de nó primário é onde os serviços do sistema é executado, portanto, a SKU de VM escolhida para ele, deve levar em consideração o pico geral de carga você planeja colocar no cluster. Aqui está uma analogia para ilustrar o que quero dizer – pense no tipo de nó primário como seus "pulmões", ele fornece oxigênio para seu cérebro e, dessa forma, se o cérebro não obtiver oxigênio suficiente, seu corpo será prejudicado. 

Como as necessidades de capacidade de um cluster são determinadas pela carga de trabalho que você planeja executar no cluster, não podemos fornecer a você uma diretriz qualitativa para sua carga de trabalho específica, mas veja estas diretrizes amplas para ajudar você a começar

Para cargas de trabalho de produção: 

- É recomendável dedicar o NodeType primário dos clusters aos serviços do sistema e usar restrições de posicionamento para implantar o aplicativo em NodeTypes secundários.
- A SKU de VM recomendada é D2_V2 padrão ou equivalente, com um mínimo de 50 GB de SSD local.
- O uso mínimo com suporte SKU de VM é Standard_D2_V3 ou a Standard D1_V2 ou equivalente, com um mínimo de 50 GB de SSD local. 
- É recomendável um mínimo de 50 GB. Para as cargas de trabalho, especialmente ao executar contêineres do Windows, são necessários discos maiores. 
- As SKUs de VM de núcleo parcial como a Standard A0 não têm suporte para cargas de trabalho de produção.
- Não há suporte para uma série de SKUs de VM para cargas de trabalho de produção por motivos de desempenho.
- Não há suporte para VMs de baixa prioridade.

> [!WARNING]
> Alterar o tamanho da SKU de VM do nó primário em um cluster em execução é uma operação de dimensionamento e isso está documentado na documentação [Expansão do Conjunto de Dimensionamento de Máquinas Virtuais](virtual-machine-scale-set-scale-node-type-scale-out.md).

## <a name="non-primary-node-type---capacity-guidance-for-stateful-workloads"></a>O tipo de nó não primário - diretrizes de capacidade para cargas de trabalho com estado

Este guia é para cargas de trabalho com monitoração de estado usando o Service Fabric [coleções confiáveis ou reliable Actors](service-fabric-choose-framework.md) que você está executando no tipo de nó não primário.

**Número de instâncias de VM:** Para cargas de trabalho de produção com monitoração de estado, é recomendável que você execute com uma contagem de réplica mínima e de destino de 5. Isso significa que no estado estável, você fica com uma réplica (de um conjunto de réplicas) em cada domínio de falha e em cada domínio de atualização. O conceito de camada de confiabilidade inteira para o tipo de nó primário é, na verdade, apenas uma maneira de especificar essa configuração para serviços do sistema. Portanto a mesma consideração aplica-se aos serviços com monitoração de estado também.

Assim, para cargas de trabalho de produção, o tamanho mínimo recomendado do tamanho do tipo de Nó não Principal é 5, se você estiver executando cargas de trabalho com monitoração de estado nele.

**SKU DE VM:** Isso é o tipo de nó em que estiver executando os serviços do aplicativo, portanto, a SKU de VM escolhida, deve levar em consideração a carga de pico que você planeja colocar em cada nó. As necessidades de capacidade do tipo de nó são determinadas pela carga de trabalho que você planeja executar no cluster. Por isso, não podemos fornecer orientação qualitativa para sua carga de trabalho específica, mas aqui está a orientação geral para ajudá-lo a começar

Para cargas de trabalho de produção 

- A SKU de VM recomendada é D2_V2 padrão ou equivalente, com um mínimo de 50 GB de SSD local.
- O uso mínimo com suporte SKU de VM é Standard_D2_V3 ou a Standard D1_V2 ou equivalente, com um mínimo de 50 GB de SSD local. 
- As SKUs de VM de núcleo parcial como a Standard A0 não têm suporte para cargas de trabalho de produção.
- Não há suporte para uma série de SKUs de VM para cargas de trabalho de produção por motivos de desempenho.

## <a name="non-primary-node-type---capacity-guidance-for-stateless-workloads"></a>Tipo de nó não primário - diretrizes de capacidade para cargas de trabalho sem estado

Esta orientação de cargas de trabalho sem estado que você está executando no tipo de nó não primário.

**Número de instâncias de VM:** Para cargas de trabalho de produção sem estado, o tamanho do tipo mínimo com suporte de nó não principal é 2. Isso permite a execução de duas instâncias sem monitoração de estado do seu aplicativo e permite que seu serviço sobreviva à perda de uma instância de VM. 

**SKU DE VM:** Isso é o tipo de nó em que estiver executando os serviços do aplicativo, portanto, a SKU de VM escolhida, deve levar em consideração a carga de pico que você planeja colocar em cada nó. As necessidades de capacidade do tipo de nó são determinadas pela carga de trabalho que você planeja executar no cluster. Não podemos fornecer orientação qualitativa para sua carga de trabalho específica.  No entanto, aqui está a ampla orientação para ajudá-lo a começar.

Para cargas de trabalho de produção 

- O SKU de VM recomendada é D2_V2 padrão ou equivalente. 
- O uso mínimo com suporte da SKU de VM é a Standard D1 ou a Standard D1_V2 ou equivalente. 
- As SKUs de VM de núcleo parcial como a Standard A0 não têm suporte para cargas de trabalho de produção.
- Não há suporte para uma série de SKUs de VM para cargas de trabalho de produção por motivos de desempenho.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="next-steps"></a>Próximas etapas
Após concluir o planejamento de capacidade e configurar um cluster, leia o seguinte:

* [Segurança do Cluster do Service Fabric](service-fabric-cluster-security.md)
* [Dimensionamento do cluster do Service Fabric](service-fabric-cluster-scaling.md)
* [Planejamento de recuperação de desastre](service-fabric-disaster-recovery.md)
* [Relação de Nodetypes para conjunto de dimensionamento de máquinas virtuais](service-fabric-cluster-nodetypes.md)

<!--Image references-->
[SystemServices]: ./media/service-fabric-cluster-capacity/SystemServices.png
