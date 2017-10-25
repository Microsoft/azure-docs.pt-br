---
title: Planejamento da capacidade de cluster do Service Fabric | Microsoft Docs
description: "Considerações de planejamento de capacidade de cluster do Service Fabric. Camadas de nodetypes, operações, durabilidade e confiabilidade"
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: 
ms.assetid: 4c584f4a-cb1f-400c-b61f-1f797f11c982
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2017
ms.author: chackdan
ms.openlocfilehash: 04964175f06675a486fcf252f194f0d790acea4a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="service-fabric-cluster-capacity-planning-considerations"></a>Considerações de planejamento de capacidade de cluster do Service Fabric
Para qualquer implantação de produção, o planejamento de capacidade é uma etapa importante. Aqui estão alguns dos itens que você precisa considerar como parte desse processo.

* O número de tipos de nós com os quais o cluster precisa começar
* As propriedades de cada tipo de nó (tamanho, primário, voltado para a Internet, número de VMs etc.)
* As características de confiabilidade e durabilidade do cluster

Vamos examinar rapidamente cada um desses itens.

## <a name="the-number-of-node-types-your-cluster-needs-to-start-out-with"></a>O número de tipos de nós com os quais o cluster precisa começar
Primeiro, você precisa descobrir para que o cluster que está criando será usado e que tipos de aplicativos você planeja implantar no cluster. Se não tem certeza quanto à finalidade do cluster, você provavelmente ainda não está pronto para iniciar o processo de planejamento de capacidade.

Estabeleça o número de tipos de nós com os quais o cluster precisa começar.  Cada tipo de nó é mapeado para um Conjunto de Escala de Máquina Virtual. Cada tipo de nó pode ser escalado verticalmente para cima ou para baixo de forma independente, tem conjuntos diferentes de portas abertas e pode ter métricas de capacidade diferente. Portanto, a decisão quanto ao número de tipos de nós essencialmente se resume às seguintes considerações:

* O aplicativo tem vários serviços, e algum deles precisa ser público ou voltado para a Internet? Os aplicativos típicos contêm um serviço de gateway de front-end que recebe entrada de um cliente e um ou mais serviços de back-end que se comunicam com os serviços de front-end. Nesse caso, você acaba tendo pelo menos dois tipos de nó.
* Os serviços (que compõem o aplicativo) têm necessidades de infraestrutura diferentes, como maior RAM ou mais ciclos de CPU? Por exemplo, vamos supor que o aplicativo que você deseja implantar contenha um serviço de front-end e um serviço de back-end. O serviço de front-end pode ser executado em VMs menores (com tamanhos de VM como D2) que têm portas abertas para a Internet.  No entanto, o serviço de back-end faz uso intensivo de computação e precisa ser executado em VMs maiores (com tamanhos de VM como D4, D6, D15) que não são voltadas para a Internet.
  
  No exemplo, embora você possa optar por colocar todos os serviços em um tipo de um nó, é recomendável colocá-los em um cluster com dois tipos de nó.  Isso permite que cada tipo de nó tenha propriedades distintas, como conectividade com a Internet ou tamanho de VM. O número de VMs também pode ser dimensionado independentemente.  
* Como você não pode prever o futuro, baseie-se no que já sabe e decida com quantos tipos de nós os aplicativos precisam começar. Você sempre pode adicionar ou remover tipos de nós posteriormente. Um cluster do Service Fabric deve ter pelo menos um tipo de nó.

## <a name="the-properties-of-each-node-type"></a>As propriedades de cada tipo de nó
O **tipo de nó** pode ser visto como algo equivalente a funções nos Serviços de Nuvem. Os tipos de nó definem os tamanhos e o número de VMs e suas propriedades. Cada tipo de nó definido em um cluster do Service Fabric está configurado como um conjunto de dimensionamento de máquinas virtuais separado. Os conjuntos de dimensionamento de máquinas virtuais são um recurso de computação do Azure que você pode usar para implantar e gerenciar uma coleção de máquinas virtuais como um conjunto. Sendo definidos como conjuntos de dimensionamento de máquinas virtuais distintos, cada tipo de nó pode ser escalado verticalmente ou horizontalmente de forma independente, ter diferentes conjuntos de portas abertas e ter métricas de capacidade diferentes.

Leia [este documento](service-fabric-cluster-nodetypes.md) para obter mais detalhes sobre o relacionamento entre Nodetypes para conjunto de dimensionamento de máquinas virtuais, como usar RDP para acessar uma das instâncias, abrir novas portas, etc.

O cluster pode ter mais de um tipo de nó, mas o tipo de nó primário (o primeiro que você define no portal) deve ter pelo menos cinco VMs para clusters usados para cargas de trabalho de produção (ou pelo menos três VMs para clusters de teste). Se estiver criando o cluster usando um modelo do Resource Manager, procure um atributo **Primary** na definição do tipo de nó. O tipo de nó primário é o tipo de nó em que os serviços do sistema do Service Fabric são colocados.  

### <a name="primary-node-type"></a>Tipo de nó primário
Para um cluster com vários tipos de nó, você precisa escolher um deles como primário. Aqui estão as características de um tipo de nó primário:

* O **tamanho mínimo de VMs** para o tipo de nó primário é determinado pela **camada de durabilidade** que você escolhe. O padrão para a camada de durabilidade é Bronze. Role para baixo para obter detalhes sobre o que é a camada de durabilidade e os valores que ela pode ter.  
* O **número mínimo de VMs** para o tipo de nó primário é determinado pela **camada de confiabilidade** que você escolhe. O padrão para a camada de confiabilidade é Prata. Role para baixo para obter detalhes sobre o que é a camada de confiabilidade e os valores que ela pode ter. 


* Os serviços do sistema do Service Fabric (por exemplo, o serviço Gerenciador de Cluster ou o Serviço de Armazenamento de Imagens) são colocados no tipo de nó primário. Assim, a confiabilidade e a durabilidade do cluster são determinadas pelos valores de camadas de confiabilidade e durabilidade selecionados para o tipo de nó primário.

![Captura de tela de um cluster com dois tipos de nó ][SystemServices]

### <a name="non-primary-node-type"></a>Tipo de nó não primário
Para um cluster com vários tipos de nó, há um tipo de nó primário, e os demais serão não primários. Aqui estão as características de um tipo de nó não primário:

* O tamanho mínimo de VMs para o tipo de nó é determinado pela camada de durabilidade que você escolhe. O padrão para a camada de durabilidade é Bronze. Role para baixo para obter detalhes sobre o que é a camada de durabilidade e os valores que ela pode ter.  
* O número mínimo de VMs para esse tipo de nó pode ser um. No entanto, você deve escolher esse número com base no número de réplicas do aplicativo/serviços que deseja executar nesse tipo de nó. O número de VMs em um tipo de nó pode ser aumentado após a implantação do cluster.

## <a name="the-durability-characteristics-of-the-cluster"></a>As características de durabilidade do cluster
A camada de durabilidade é usada para indicar ao sistema os privilégios que as VMs têm com a infraestrutura subjacente do Azure. No tipo de nó primário, esse privilégio permite que o Service Fabric pause qualquer solicitação de infraestrutura de nível de VM (por exemplo, reinicialização de VM, recriação de imagem de VM ou migração de VM) que afete os requisitos de quorum para os serviços do sistema e os serviços com estado. Nos tipos de nó não primários, esse privilégio permite que o Service Fabric pause quaisquer solicitações de infraestrutura de nível de VM (como reinicialização de VM, recriação de imagem de VM, migração de VM etc.) que afete os requisitos de quorum para os serviços com estado em execução.

Esse privilégio é expresso nos seguintes valores:

* Ouro – os Trabalhos de infraestrutura podem permanecer em pausa por duas horas por UD. A durabilidade ouro pode ser habilitada apenas em skus de VM de nó completo como D15_V2, G5 etc.
* Prata – os Trabalhos de infraestrutura podem permanecer em pausa por um período de dez minutos por UD e estão disponíveis em todas as VMs padrão de núcleo único e superior.
* Bronze - sem privilégios. Esse é o padrão. Use esse nível de durabilidade somente para Tipos de nós que executam _somente_ cargas de trabalho sem estado. 

> [!WARNING]
> NodeTypes executados com durabilidade Bronze não têm _nenhum privilégio_. Isso significa que trabalhos de infraestrutura que afetam sus cargas de trabalho sem estado não serão interrompidas ou atrasadas. É possível que tais trabalhos ainda possam afetar suas cargas de trabalho, causando tempo de inatividade ou outros problemas. Para qualquer tipo de carga de trabalho de produção, é recomendável a execução com pelo menos o nível Prata. Você deve manter uma contagem mínima de 5 nós de qualquer tipo que tenha uma durabilidade Ouro ou Prata. 
> 

É preciso escolher o nível de durabilidade de cada um dos tipos de nós. Você pode escolher o nível de durabilidade Ouro ou Prata para um tipo de nó e Bronze para outro no mesmo cluster.**Você deve manter um número mínimo de 5 nós de qualquer tipo de nó que tem uma durabilidade de ouro e prata**. 

**Vantagens de usar os níveis de durabilidade Ouro ou Prata**
 
1. Reduz o número de etapas necessárias em uma operação de redução horizontal (isto é, a desativação do nó e a chamada para Remove-ServiceFabricNodeState ocorrem de maneira automática)
2. Reduz o risco de perda de dados decorrente de uma operação de alteração de SKU de VM in-loco iniciada pelo cliente ou de operações de infraestrutura do Azure.
     
**Desvantagens de usar os níveis de durabilidade Ouro ou Prata**
 
1. As implantações no Conjunto de Dimensionamento de Máquinas Virtuais e nos outros recursos do Azure relacionados podem ser atrasadas, podem atingir o tempo limite ou podem ser bloqueadas inteiramente por problemas no cluster ou no nível de infraestrutura. 
2. Aumenta o número de [eventos de ciclo de vida de réplica](service-fabric-reliable-services-advanced-usage.md#stateful-service-replica-lifecycle ) (por exemplo, trocas primárias) devido às desativações de nós automatizadas durante as operações de infraestrutura do Azure.

### <a name="recommendations-on-when-to-use-silver-or-gold-durability-levels"></a>Recomendações de quando usar os níveis de durabilidade Prata ou Ouro

Use a durabilidade Prata ou Ouro para todos os tipos de nós que hospedam serviços com estado que você pretende reduzir horizontalmente (reduzir a contagem de instâncias de VM) com frequência, mas prefere que as operações de implantação sejam atrasadas para simplificar essas operações de redução horizontal. Os cenários de escalabilidade horizontal (adicionar instâncias de VMs) não fazem parte das opções da camada de durabilidade, somente os de redução horizontal.

### <a name="operational-recommendations-for-the-node-type-that-you-have-set-to-silver-or-gold-durability-level"></a>Recomendações operacionais para o tipo de nó configurado para o nível de durabilidade Prata ou Ouro.

1. Mantenha sempre a integridade do cluster e dos aplicativos e verifique se os aplicativos respondem a todos os [eventos de ciclo de vida de réplica do Serviço](service-fabric-reliable-services-advanced-usage.md#stateful-service-replica-lifecycle) (como quando a réplica sendo compilada está paralisada) de maneira oportuna.
2. Adotar modos mais seguros de fazer uma alteração de SKU de VM (escalar verticalmente/horizontalmente): a alteração da SKU de VM de um Conjunto de Dimensionamento de Máquinas Virtuais é inerentemente uma operação não segura e portanto deve ser evitada, se possível. Veja o processo que você pode seguir para evitar problemas comuns.
    - **Para tipos de nós não primários:** é recomendado criar um novo Conjunto de Dimensionamento de Máquinas Virtuais, modificar a restrição de posicionamento do serviço para incluir o novo Conjunto de Dimensionamento de Máquinas Virtuais/tipo de nó e, em seguida, reduzir a contagem antiga de instâncias do Conjunto de Dimensionamento de Máquinas Virtuais para 0, um nó de cada vez (isso deve ser feito para garantir que a remoção dos nós não afete a confiabilidade do cluster).
    - **Para o tipo de nó primário**: recomendamos não alterar a SKU de VM do tipo de nó primário. Não há suporte para a alteração do SKU do tipo de nó primário. Se o motivo para o novo SKU é a capacidade, recomendamos adicionar mais instâncias. Se isso não for possível, crie um novo cluster e [restaure o estado do aplicativo](service-fabric-reliable-services-backup-restore.md) (se aplicável) por meio do cluster antigo. Você não precisa restaurar qualquer estado do serviço do sistema; ele é recriado quando você implanta os aplicativos no novo cluster. Se você estiver apenas executando aplicativos sem monitoração de estado no cluster, basta implantar os aplicativos no novo cluster; não há nada para restaurar. Se você decidir ir para a rota sem suporte e desejar alterar o SKU de VM, faça modificações na definição do Modelo do Conjunto de Dimensionamento de Máquinas Virtuais para refletir o novo SKU. Se o seu cluster tiver apenas um tipo de nó, verifique se todos os aplicativos com estado respondem a todos os [eventos de ciclo de vida de réplica do Serviço](service-fabric-reliable-services-advanced-usage.md#stateful-service-replica-lifecycle) (como quando a réplica sendo compilada está paralisada) de maneira oportuna e se a duração da recompilação da réplica do serviço dura menos de cinco minutos (para o nível de durabilidade Prata). 


> [!WARNING]
> Alterar o Tamanho de SKU da VM para Conjuntos de Dimensionamento de VMs que não executam pelo menos a durabilidade Prata não é recomendado. Alterar o Tamanho de SKU da VM é uma operação de infraestrutura no local com destruição de dados. Sem ter pelo menos alguma capacidade de atrasar ou monitorar essa alteração, é possível que a operação cause perda de dados para serviços com estado ou outros problemas operacionais imprevistos, mesmo para cargas de trabalho sem estado. 
> 
    
3. Mantenha uma contagem mínima de cinco nós para qualquer Conjunto de Dimensionamento de Máquinas Virtuais que tenha o nível de durabilidade de Ouro ou Prata habilitado
4. Não exclua instâncias de VM aleatórias; sempre reduza verticalmente o recurso Conjunto de Dimensionamento de Máquinas Virtuais. A exclusão de instâncias de VM aleatórias tem o potencial de criar desequilíbrios na difusão das instâncias de VM por UD e FD. Esse desequilíbrio pode afetar de maneira negativa a capacidade dos sistemas de executar um balanceamento de carga adequado entre as instâncias do serviço/réplicas do Serviço.
6. Se usar o Dimensionamento Automático, defina as regras de modo que a redução horizontal (remoção de instâncias de VM) seja executada em um nó de cada vez. Redução de mais de uma instância em um momento não é segura.
7. Se a redução de um tipo de nó primário, você deve nunca reduzi-lo mais do que permite que o nível de confiabilidade.


## <a name="the-reliability-characteristics-of-the-cluster"></a>As características de confiabilidade do cluster
A camada de confiabilidade é usada para definir o número de réplicas dos serviços do sistema que você deseja executar nesse cluster no tipo de nó primário. Quanto maior for o número de réplicas, mais confiáveis os serviços do sistema serão no cluster.  

A camada de confiabilidade pode ter os valores a seguir:

* Platina - executar os serviços do sistema com uma contagem de conjuntos de réplicas de destino de 9
* Ouro - executar os serviços do sistema com uma contagem de conjuntos de réplicas de destino de 7
* Prata - executar os serviços do sistema com uma contagem de conjuntos de réplicas de destino de 5 
* Bronze - executar os serviços do sistema com uma contagem de conjuntos de réplicas de destino de 3

> [!NOTE]
> A camada de confiabilidade que você escolhe determina o número mínimo de nós que o tipo de nó primário deve ter. 
> 
> 


### <a name="recommendations-for-the-reliability-tier"></a>Recomendações para a camada de confiabilidade.

 Quando você aumenta ou diminui o tamanho do cluster (a soma das instâncias VM em todos os tipos de nó), você deve atualizar a confiabilidade do seu cluster de um nível para outro. Fazer isso dispara as atualizações de cluster necessárias para alterar a contagem de conjuntos de réplicas dos serviços do sistema. Aguarde a conclusão da atualização em andamento antes de fazer outras alterações no cluster, assim como adicionar nós.  Você pode monitorar o andamento da atualização no Service Fabric Explorer ou executando [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps)

Aqui está a recomendação sobre como escolher o nível de confiabilidade.

| **Tamanho do cluster** | **Camada de confiabilidade** |
| --- | --- |
| 1 |Não especifique o parâmetro de camada de confiabilidade, o sistema calculará |
| 3 |Bronze |
| 5 ou 6|Silver |
| 7 ou 8 |Gold |
| 9 e superior |Platinum |




## <a name="primary-node-type---capacity-guidance"></a>Tipo de nó Primário - Diretrizes de Capacidade

Eis aqui as diretrizes de planejamento da capacidade do tipo de nó principal

1. **Número de instâncias de VM para executar qualquer carga de trabalho de produção no Azure:** você deve especificar um tamanho mínimo de tipo de Nó Primário de 5. 
2. **Número de instâncias de VM para executar cargas de trabalho de teste no Azure** Você pode especificar um tamanho mínimo de tipo de nó primário de 1 ou 3. O cluster de um nó é executado com uma configuração especial e, portanto, não há suporte para expansão desse cluster. O cluster de um nó não tem confiabilidade e assim, em seu modelo do Resource Manager, você precisa remover ou não especificar essa configuração (não definir o valor de configuração não é suficiente). Se você configurar o cluster de um nó configurado por meio do Portal, em seguida, a configuração será feita automaticamente. Não há suporte para clusters de 1 e 3 nós para executar cargas de trabalho de produção. 
3. **SKU da VM:** o tipo de nó Primário é onde os serviços do sistema são executados e, portanto, a SKU de VM escolhida deve levar em consideração o pico de carga geral que você planeja colocar no cluster. Aqui está uma analogia para ilustrar o que quero dizer – pense no tipo de nó primário como seus "pulmões", ele fornece oxigênio para seu cérebro e, dessa forma, se o cérebro não obtiver oxigênio suficiente, seu corpo será prejudicado. 

Como as necessidades de capacidade de um cluster são determinadas pela carga de trabalho que você planeja executar no cluster, não podemos fornecer a você uma diretriz qualitativa para sua carga de trabalho específica, mas veja estas diretrizes amplas para ajudar você a começar

Para cargas de trabalho de produção 


- A SKU de VM recomendada é a Standard D3 ou a Standard D3_V2 ou equivalente, com um mínimo de 14 GB de SSD local.
- A SKU de VM de uso mínimo com suporte é a Standard D1 ou a Standard D1_V2 ou equivalente, com um mínimo de 14 GB de SSD local. 
- As SKUs de VM de núcleo parcial como a Standard A0 não têm suporte para cargas de trabalho de produção.
- A SKU Standard A1 não tem suporte para cargas de trabalho de produção por motivos de desempenho.

> [!WARNING]
> No momento, não há suporte para a alteração do tamanho de SKU de VM do nó primário em um cluster em execução. Portanto, escolha o SKU de VM do tipo de nó primário com cuidado, levando em consideração suas necessidades futuras de capacidade. No momento, a única maneira com suporte para mover o tipo de nó primário para um novo SKU de VM (maior ou menor) é criar um novo cluster com a capacidade certa, implantar os aplicativos nele e, em seguida, restaurar o estado do aplicativo (se aplicável) por meio dos [últimos backups de serviço](service-fabric-reliable-services-backup-restore.md) feitos no cluster antigo. Você não precisa restaurar qualquer estado do serviço do sistema; ele é recriado quando você implanta aplicativos no novo cluster. Se você estiver apenas executando aplicativos sem monitoração de estado no cluster, basta implantar os aplicativos no novo cluster; não há nada para restaurar.
> 

## <a name="non-primary-node-type---capacity-guidance-for-stateful-workloads"></a>O tipo de nó Não Principal - Diretrizes de Capacidade para cargas de trabalho com monitoração de estado

Este guia é para cargas de trabalho com monitoração de estado usando o Service Fabric [coleções confiáveis ou reliable Actors](service-fabric-choose-framework.md) que você está executando no tipo de nó não primário.


**Número de instâncias de VM:** para as cargas de trabalho de produção com monitoração de estado, é recomendável que você as execute com uma contagem de réplica mínima e de destino de 5. Isso significa que no estado estável, você fica com uma réplica (de um conjunto de réplicas) em cada domínio de falha e em cada domínio de atualização. O conceito de camada de confiabilidade inteira para o tipo de nó primário é, na verdade, apenas uma maneira de especificar essa configuração para serviços do sistema. Portanto a mesma consideração aplica-se aos serviços com monitoração de estado também.

Assim, para cargas de trabalho de produção, o tamanho mínimo recomendado do tamanho do tipo de Nó não Principal é 5, se você estiver executando cargas de trabalho com monitoração de estado nele.


**SKU de VM:** esse é o tipo de nó em que seus serviços de aplicativo estão sendo executados e, portanto, a SKU de VM escolhida deverá levar em conta a carga máxima que você deseja colocar em cada nó. As necessidades de capacidade do tipo de nó são determinadas pela carga de trabalho que você planeja executar no cluster e, portanto, não podemos fornecer a você diretriz qualitativa para sua carga de trabalho específica, mas veja estas diretrizes amplas para ajudar você a começar

Para cargas de trabalho de produção 

- A SKU de VM recomendada é a Standard D3 ou a Standard D3_V2 ou equivalente, com um mínimo de 14 GB de SSD local.
- A SKU de VM de uso mínimo com suporte é a Standard D1 ou a Standard D1_V2 ou equivalente, com um mínimo de 14 GB de SSD local. 
- As SKUs de VM de núcleo parcial como a Standard A0 não têm suporte para cargas de trabalho de produção.
- Especificamente, a SKU Standard A1 não tem suporte para cargas de trabalho de produção por motivos de desempenho.


## <a name="non-primary-node-type---capacity-guidance-for-stateless-workloads"></a>O tipo de nó Não Principal - Diretrizes de Capacidade para cargas de trabalho sem monitoração de estado

Este guia de cargas de trabalho sem monitoração de estado que você está executando no nodetype não primário.

**Número de instâncias de VM:** para cargas de trabalho de produção sem estado, o tamanho mínimo do tipo de Nó não Principal com suporte é 2. Isso permite a execução de duas instâncias sem monitoração de estado do seu aplicativo e permite que seu serviço sobreviva à perda de uma instância de VM. 

> [!NOTE]
> Se o cluster estiver em execução em uma versão do Service Fabric inferior a 5.6, devido a um defeito no tempo de execução (esse problema é corrigido na versão 5.6), o dimensionamento de um tipo de nó não primário para menos de 5 fará com que o cluster se torne não íntegro, até você chamar [Remove-ServiceFabricNodeState cmd](https://docs.microsoft.com/powershell/servicefabric/vlatest/Remove-ServiceFabricNodeState) com o nome de nó apropriado. Leia [Sair ou entrar no cluster do Service Fabric](service-fabric-cluster-scale-up-down.md) para obter mais detalhes
> 
>

**SKU de VM:** esse é o tipo de nó em que seus serviços de aplicativo estão sendo executados e, portanto, a SKU de VM escolhida deverá levar em conta a carga máxima que você deseja colocar em cada nó. As necessidades de capacidade do tipo de nó são determinadas pela carga de trabalho que você planeja executar no cluster e, portanto, não podemos fornecer a você diretriz qualitativa para sua carga de trabalho específica, mas veja estas diretrizes amplas para ajudar você a começar

Para cargas de trabalho de produção 


- A SKU de VM recomendada é a Standard D3 ou a Standard D3_V2 ou equivalente. 
- O uso mínimo com suporte da SKU de VM é a Standard D1 ou a Standard D1_V2 ou equivalente. 
- As SKUs de VM de núcleo parcial como a Standard A0 não têm suporte para cargas de trabalho de produção.
- A SKU Standard A1 não tem suporte para cargas de trabalho de produção por motivos de desempenho.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="next-steps"></a>Próximas etapas
Após concluir o planejamento de capacidade e configurar um cluster, leia o seguinte:

* [Segurança do Cluster do Service Fabric](service-fabric-cluster-security.md)
* [Planejamento de recuperação de desastre](service-fabric-disaster-recovery.md)
* [Definir relação entre tipos de nó e conjunto de dimensionamento de máquinas virtuais](service-fabric-cluster-nodetypes.md)

<!--Image references-->
[SystemServices]: ./media/service-fabric-cluster-capacity/SystemServices.png
