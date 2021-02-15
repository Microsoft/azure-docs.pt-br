---
title: Descrever um cluster usando o Gerenciador de recursos de cluster
description: Descreva um Cluster Service Fabric especificando domínios de falha, domínios de atualização, propriedades de nó e capacidades de nó para o Gerenciador de recursos de cluster.
author: masnider
ms.topic: conceptual
ms.date: 07/28/2020
ms.author: masnider
ms.custom: devx-track-csharp
ms.openlocfilehash: 5d27a09f0ff38ec7422636ef0933552aa310c387
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2020
ms.locfileid: "92911759"
---
# <a name="describe-a-service-fabric-cluster-by-using-cluster-resource-manager"></a>Descrever um Cluster Service Fabric usando o Gerenciador de recursos de cluster

O recurso Gerenciador de recursos de cluster do Azure Service Fabric fornece vários mecanismos para descrever um cluster:

* Domínios de falha
* Domínios de atualização
* Propriedades de nó
* Capacidades de nó

Durante o tempo de execução, o Gerenciador de recursos de cluster usa essas informações para garantir a alta disponibilidade dos serviços em execução no cluster. Ao impor essas regras importantes, ele também tenta otimizar o consumo de recursos no cluster.

## <a name="fault-domains"></a>Domínios de falha

Um domínio de falha é qualquer área da falha coordenada. Um único computador é um domínio de falha. Ele pode falhar por conta própria por vários motivos, desde falhas de fornecimento de energia até falhas de unidade para firmware NIC inadequado.

Os computadores conectados ao mesmo comutador Ethernet estão no mesmo domínio de falha. Portanto, os computadores que compartilham uma única fonte de energia ou em um único local.

Como é natural que as falhas de hardware se sobreponham, os domínios de falha são inerentemente hierárquicos. Eles são representados como URIs em Service Fabric.

É importante que os domínios de falha sejam configurados corretamente porque o Service Fabric usa essas informações para posicionar os serviços com segurança. Service Fabric não quer posicionar serviços de forma que a perda de um domínio de falha (causada pela falha de algum componente) faça com que um serviço fique inativo.

No ambiente do Azure, Service Fabric usa as informações de domínio de falha fornecidas pelo ambiente para configurar corretamente os nós no cluster em seu nome. Para instâncias autônomas do Service Fabric, os domínios de falha são definidos no momento em que o cluster é configurado.

> [!WARNING]
> É importante que as informações de domínio de falha fornecidas para Service Fabric sejam precisas. Por exemplo, digamos que os nós do Cluster Service Fabric estejam em execução dentro de 10 máquinas virtuais, em execução em 5 hosts físicos. Nesse caso, mesmo que haja dez máquinas virtuais, há apenas cinco domínios de falha (de nível superior) diferentes. Compartilhar o mesmo host físico faz com que as VMs compartilhem o mesmo domínio de falha raiz, pois as VMs terão falha coordenada se o host físico falhar.  
>
> Service Fabric espera que o domínio de falha de um nó não seja alterado. Outros mecanismos de garantir a alta disponibilidade das VMs, como [ha-VMs](/previous-versions/system-center/virtual-machine-manager-2008-r2/cc967323(v=technet.10)), podem causar conflitos com Service Fabric. Esses mecanismos usam a migração transparente de VMs de um host para outro. Eles não reconfiguram nem notificam o código em execução dentro da VM. Dessa forma, eles *não têm suporte* como ambientes para execução de clusters de Service Fabric. 
>
> O Service Fabric deve ser a única tecnologia de alta disponibilidade empregada. Mecanismos como migração de VM ao vivo e SANs não são necessários. Se esses mecanismos forem usados em conjunto com Service Fabric, eles _reduzirão_ a disponibilidade e a confiabilidade do aplicativo. O motivo é que eles introduzem complexidade adicional, adicionam fontes de falha centralizadas e usam estratégias de confiabilidade e disponibilidade que entram em conflito com aquelas em Service Fabric.
>
>

No gráfico a seguir, colorimos todas as entidades que contribuem para domínios de falha e listamos todos os diferentes domínios de falha que resultam. Neste exemplo, temos datacenters (“DC”), racks (“R”) e folhas (“B”). Se cada folha contém mais de uma máquina virtual, pode haver outra camada na hierarquia de domínio de falha.

![Nós organizados por meio de domínios de falha][Image1]

Durante o tempo de execução, Service Fabric Gerenciador de recursos de cluster considera os domínios de falha no cluster e os layouts de planos. As réplicas com estado ou instâncias sem estado para um serviço são distribuídas para que estejam em domínios de falha separados. A distribuição do serviço entre domínios de falha garante que a disponibilidade do serviço não seja comprometida quando um domínio de falha falhar em qualquer nível da hierarquia.

O Gerenciador de recursos de cluster não importa quantas camadas existem na hierarquia de domínio de falha. Ele tenta garantir que a perda de qualquer parte da hierarquia não afete os serviços em execução nela.

É melhor se o mesmo número de nós estiver em cada nível de profundidade na hierarquia de domínio de falha. Se a "árvore" dos domínios de falha estiver desbalanceada no cluster, será mais difícil para o Gerenciador de recursos de cluster descobrir a melhor alocação dos serviços. Os layouts de domínio de falha desbalanceada significam que a perda de alguns domínios afeta a disponibilidade de serviços mais do que outros domínios. Como resultado, o Gerenciador de recursos de cluster é interrompido entre dois objetivos:

* Ele quer usar os computadores nesse domínio "pesado" colocando serviços neles. 
* Ele deseja posicionar serviços em outros domínios para que a perda de um domínio não cause problemas.

Qual é a aparência de um domínio desequilibrado? O diagrama a seguir mostra dois layouts de cluster diferentes. No primeiro exemplo, os nós são distribuídos uniformemente entre os domínios de falha. No segundo exemplo, um domínio de falha tem muito mais nós do que os outros domínios de falha.

![Dois layouts de cluster diferentes][Image2]

No Azure, a escolha de qual domínio de falha contém um nó é gerenciado para você. Mas, dependendo do número de nós que você provisiona, ainda é possível acabar com os domínios de falha que têm mais nós em relação aos outros.

Por exemplo, digamos que você tenha cinco domínios de falha no cluster, mas provisione sete nós para um tipo de nó ( **NodeType** ). Nesse caso, os dois primeiros domínios de falha terminam com mais nós. Se você continuar implantando mais instâncias de **NodeType** com apenas algumas instâncias, o problema ficará pior. Por esse motivo, recomendamos que o número de nós em cada tipo de nó seja um múltiplo do número de domínios de falha.

## <a name="upgrade-domains"></a>Domínios de atualização

Os domínios de atualização são outro recurso que ajuda a Service Fabric o Gerenciador de recursos de cluster a entender o layout do cluster. Os domínios de atualização definem conjuntos de nós que são atualizados ao mesmo tempo. Domínios de atualização ajudam o Gerenciador de recursos de cluster a entender e orquestrar operações de gerenciamento como atualizações.

Os domínios de atualização são muito parecidos com os domínios de falha, mas com algumas diferenças importantes. Primeiro, as áreas de falhas de hardware coordenadas definem domínios de falha. Os domínios de atualização, por outro lado, são definidos pela política. Você decide quantos deseja, em vez de permitir que o ambiente dite o número. Você pode ter tantos domínios de atualização quantos forem os nós. Outra diferença entre domínios de falha e domínios de atualização é que os domínios de atualização não são hierárquicos. Em vez disso, eles são mais como uma marca simples.

O diagrama a seguir mostra três domínios de atualização distribuídos em três domínios de falha. Ele também mostra um possível posicionamento para três réplicas diferentes de um serviço com estado, onde cada um acaba em diferentes domínios de falha e atualização. Esse posicionamento permite a perda de um domínio de falha no meio de uma atualização de serviço e ainda tem uma cópia do código e dos dados.  

![Posicionamento com domínios de falha e de atualização][Image3]

Há prós e contras para ter um grande número de domínios de atualização. Mais domínios de atualização significam que cada etapa da atualização é mais granular e afeta um número menor de nós ou serviços. Menos serviços precisam ser movidos de cada vez, apresentando menos variação no sistema. Isso tende a melhorar a confiabilidade, pois o menor serviço é afetado por qualquer problema introduzido durante a atualização. Mais domínios de atualização também significam que você precisa de menos buffer disponível em outros nós para lidar com o impacto da atualização.

Por exemplo, se você tiver cinco domínios de atualização, os nós em cada um serão manuseando aproximadamente 20% do tráfego. Se você precisar reduzir esse domínio de atualização para uma atualização, essa carga normalmente precisará ser executada em algum lugar. Como você tem quatro domínios de atualização restantes, cada um deve ter espaço para cerca de 25% do tráfego total. Mais domínios de atualização significam que você precisa de menos buffer nos nós do cluster.

Considere se você tinha 10 domínios de atualização em vez disso. Nesse caso, cada domínio de atualização estaria lidando apenas com cerca de 10% do tráfego total. Quando uma atualização percorre o cluster, cada domínio precisaria ter espaço para apenas cerca de 11% do tráfego total. Mais domínios de atualização geralmente permitem que você execute seus nós com maior utilização, pois você precisa de menos capacidade reservada. O mesmo é verdadeiro para domínios de falha.  

A desvantagem de ter muitos domínios de atualização é que as atualizações tendem a demorar mais. Service Fabric aguarda um curto período após a conclusão de um domínio de atualização e executa verificações antes de começar a atualizar o próximo. Esses atrasos permitem detectar problemas introduzidos pela atualização antes que ela continue. Essa compensação é aceitável, pois isso evita que alterações incorretas afetem grande parte do serviço de uma só vez.

A presença de poucos domínios de atualização tem muitos efeitos colaterais negativos. Enquanto cada domínio de atualização está inoperante e sendo atualizado, uma grande parte da capacidade geral fica indisponível. Por exemplo, se você tiver apenas três domínios de atualização, estará demorando um terço da capacidade geral do serviço ou do cluster de cada vez. Ter muito de seu serviço ao mesmo tempo não é desejável porque você precisa de capacidade suficiente no restante do cluster para lidar com a carga de trabalho. Manter esse buffer significa que durante a operação normal, esses nós são menos carregados do que em caso contrário. Isso aumenta o custo de execução de seu serviço.

Não há um limite real para o número total de domínios de falha ou de atualização em um ambiente nem restrições sobre como eles se sobrepõem. Mas há padrões comuns:

* Domínios de falha e domínios de atualização mapeados 1:1
* Um domínio de atualização por nó (instância de sistema operacional física ou virtual)
* Um modelo "distribuído" ou "matriz" em que os domínios de falha e os domínios de atualização formam uma matriz com computadores geralmente executando as diagonais

![Layouts de domínios de falha e de atualização][Image4]

Não há uma melhor resposta para qual layout escolher. Cada um tem prós e contras. Por exemplo, o modelo de 1FD:1UD é simples de configurar. O modelo de um domínio de atualização por modelo de nó é mais parecido com o que as pessoas estão acostumados. Durante as atualizações, cada nó é atualizado de forma independente. Isso é semelhante à forma como pequenos conjuntos de computadores eram atualizados manualmente no passado.

O modelo mais comum é a matriz FD/UD, em que os domínios de falha e os domínios de atualização formam uma tabela e os nós são posicionados começando ao longo da diagonal. Esse é o modelo usado por padrão nos clusters do Service Fabric no Azure. Para clusters com muitos nós, tudo acaba parecendo com um padrão de matriz densa.

> [!NOTE]
> Service Fabric clusters hospedados no Azure não dão suporte à alteração da estratégia padrão. Somente os clusters autônomos oferecem essa personalização.
>

## <a name="fault-and-upgrade-domain-constraints-and-resulting-behavior"></a>Restrições de domínio de falha e de atualização e o comportamento resultante
### <a name="default-approach"></a>Abordagem padrão

Por padrão, o Gerenciador de recursos de cluster mantém os serviços balanceados entre domínios de falha e de atualização. Isso é modelado como uma [restrição](service-fabric-cluster-resource-manager-management-integration.md). A restrição para domínios de falha e de atualização diz: "para uma determinada partição de serviço, nunca deve haver uma diferença maior que um no número de objetos de serviço (instâncias de serviço sem estado ou réplicas de serviço com estado) entre dois domínios no mesmo nível de hierarquia."

Digamos que essa restrição forneça uma garantia de "diferença máxima". A restrição para domínios de falha e de atualização impede determinadas movimentações ou disposições que violem a regra.

Por exemplo, digamos que temos um cluster com seis nós, configurado com cinco domínios de falha e cinco domínios de atualização.

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | | |N3 | | |
| **UD3** | | | |N4 | |
| **UD4** | | | | |N5 |

Agora, digamos que criemos um serviço com um valor de **TargetReplicaSetSize** (ou, para um serviço sem estado, **InstanceCount** ) de cinco. As réplicas recaem sobre N1 a N5. Na verdade, N6 nunca será usado, independentemente de quantos serviços como este você criar. Mas por quê? Vamos observar a diferença entre o layout atual e o que aconteceria se N6 fosse escolhido.

Aqui está o layout que obtemos e o número total de réplicas por domínio de falha e de atualização:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** | |R2 | | | |1 |
| **UD2** | | |R3 | | |1 |
| **UD3** | | | |R4 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

Esse layout é balanceado em termos de nós por domínio de falha e domínio de atualização. Ele também é balanceado em termos do número de réplicas por domínio de falha e de atualização. Cada domínio possui o mesmo número de nós e o mesmo número de réplicas.

Agora, vamos ver o que aconteceria se tivéssemos usado N6 em vez de N2. Como as réplicas seriam distribuídas?

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R5 | | | | |1 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |2 |0 |1 |1 |1 |- |

Esse layout viola nossa definição da garantia de "diferença máxima" para a restrição de domínio de falha. FD0 tem duas réplicas, enquanto FD1 tem zero. A diferença entre FD0 e FD1 é um total de dois, que é maior que a diferença máxima de um. Como a restrição foi violada, o Gerenciador de recursos de cluster não permite essa disposição.

Da mesma forma, se escolhemos N2 e N6 (em vez de N1 e N2), obtemos:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** | | | | | |0 |
| **UD1** |R5 |R1 | | | |2 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

Esse layout é balanceado em termos de domínios de falha. Mas agora ele está violando a restrição de domínio de atualização, porque UD0 tem zero réplicas e UD1 tem dois. Esse layout também é inválido e não será escolhido pelo Gerenciador de recursos de cluster.

Essa abordagem de distribuição de réplicas com estado ou de instâncias sem estado fornece a melhor tolerância a falhas possível. Se um domínio falhar, o número mínimo de réplicas/instâncias será perdido.

Por outro lado, essa abordagem pode ser muito restrita e não permitir que o cluster utilize todos os recursos. Para determinadas configurações de cluster, alguns nós não podem ser usados. Isso pode fazer com que Service Fabric não coloque seus serviços, resultando em mensagens de aviso. No exemplo anterior, alguns dos nós de cluster não podem ser usados (N6 no exemplo). Mesmo que você tenha adicionado nós a esse cluster (N7-N10), as réplicas/instâncias só serão colocadas em N1 – N5 devido a restrições em domínios de falha e de atualização.

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | |N10 |
| **UD1** |N6 |N2 | | | |
| **UD2** | |N7 |N3 | | |
| **UD3** | | |N8 |N4 | |
| **UD4** | | | |N9 |N5 |

### <a name="alternative-approach"></a>Abordagem alternativa

O Gerenciador de recursos de cluster dá suporte a outra versão da restrição para domínios de falha e atualização. Ele permite o posicionamento enquanto ainda garante um nível mínimo de segurança. A restrição alternativa pode ser declarada da seguinte maneira: "para uma determinada partição de serviço, a distribuição de réplica entre domínios deve garantir que a partição não sofra uma perda de quorum". Digamos que essa restrição forneça uma garantia de "segurança de quorum".

> [!NOTE]
> Para um serviço com estado, definimos *perda de quorum* em uma situação em que a maioria das réplicas de partição estão inativas ao mesmo tempo. Por exemplo, se **TargetReplicaSetSize** for cinco, um conjunto de três réplicas representará quorum. Da mesma forma, se **TargetReplicaSetSize** for seis, quatro réplicas serão necessárias para o quorum. Em ambos os casos, no máximo duas réplicas podem ficar inativas ao mesmo tempo se a partição quiser continuar funcionando normalmente.
>
> Para um serviço sem estado, não há nenhuma coisa como *perda de quorum* . Os serviços sem estado continuam a funcionar normalmente mesmo que a maioria das instâncias fique inativa ao mesmo tempo. Então, nos concentraremos nos serviços com estado no restante deste artigo.
>

Voltar para o exemplo anterior. Com a versão "segurança de quorum" da restrição, todos os três layouts seriam válidos. Mesmo se FD0 falhou no segundo layout ou UD1 falhou no terceiro layout, a partição ainda teria quorum. (A maioria das réplicas ainda estaria ativa.) Com essa versão da restrição, o N6 quase sempre pode ser utilizado.

A abordagem "segurança de quorum" fornece mais flexibilidade do que a abordagem de "diferença máxima". O motivo é que é mais fácil localizar distribuições de réplicas que são válidas em praticamente qualquer topologia de cluster. No entanto, essa abordagem não garante a melhor característica de tolerância a falhas, pois algumas falhas são piores que outras.

No pior cenário, a maioria das réplicas pode ser perdida com a falha de um domínio e de uma réplica adicional. Por exemplo, em vez de três falhas sendo necessárias para perder o quorum com cinco réplicas ou instâncias, agora você pode perder a maioria com apenas duas falhas.

### <a name="adaptive-approach"></a>Abordagem adaptável

Como ambas as abordagens têm pontos fortes e fracos, apresentamos uma abordagem adaptável que combina essas duas estratégias.

> [!NOTE]
> Esse é o comportamento padrão começando com Service Fabric versão 6,2.
>
> A abordagem adaptável usa a lógica “diferença máxima” por padrão e muda para a lógica “segurança de quorum” somente quando necessário. O Gerenciador de recursos de cluster descobre automaticamente qual estratégia é necessária examinando como o cluster e os serviços são configurados.
>
> O Gerenciador de recursos de cluster deve usar a lógica "baseada em quorum" para um serviço que essas duas condições sejam verdadeiras:
>
> * O **TargetReplicaSetSize** para o serviço é igualmente divisível pelo número de domínios de falha e pelo número de domínios de atualização.
> * O número de nós é menor ou igual ao número de domínios de falha multiplicado pelo número de domínios de atualização.
>
> Tenha em mente que o Gerenciador de recursos de cluster usará essa abordagem para serviços com e sem estado, mesmo que a perda de quorum não seja relevante para serviços sem estado.

Vamos voltar ao exemplo anterior e supor que um cluster agora tem oito nós. O cluster ainda é configurado com cinco domínios de falha e cinco domínios de atualização, e o valor de **TargetReplicaSetSize** de um serviço hospedado nesse cluster permanece cinco.

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | |N7 |N3 | | |
| **UD3** | | |N8 |N4 | |
| **UD4** | | | | |N5 |

Como todas as condições necessárias são satisfeitas, o Gerenciador de recursos de cluster usará a lógica "baseada em quorum" na distribuição do serviço. Isso habilita o uso de N6-N8. Nesse caso, uma possível distribuição de serviço pode ser assim:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R2 | | | | |1 |
| **UD2** | |R3 |R4 | | |2 |
| **UD3** | | | | | |0 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |2 |1 |1 |0 |1 |- |

Se o valor de **TargetReplicaSetSize** do serviço for reduzido para quatro (por exemplo), o Gerenciador de recursos de cluster observará essa alteração. Ele será retomado usando a lógica "diferença máxima" porque o **TargetReplicaSetSize** não é mais dividable pelo número de domínios de falha e domínios de atualização. Como resultado, certos movimentos de réplica ocorrerão para distribuir as quatro réplicas restantes nos nós N1 a N5. Dessa forma, a versão de "diferença máxima" da lógica de domínio de falha e de domínio de atualização não é violada.

No layout anterior, se o valor de **TargetReplicaSetSize** for cinco e N1 for removido do cluster, o número de domínios de atualização se tornará igual a quatro. Novamente, o Gerenciador de recursos de cluster começa usando a lógica "diferença máxima" porque o número de domínios de atualização não divide o valor de **TargetReplicaSetSize** do serviço de maneira mais uniforme. Como resultado, a réplica R1, quando criada novamente, precisa se esN4r para que a restrição para o domínio de falha e de atualização não seja violada.

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |N/D |N/D |N/D |N/D |N/D |N/D |
| **UD1** |R2 | | | | |1 |
| **UD2** | |R3 |R4 | | |2 |
| **UD3** | | | |R1 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

## <a name="configuring-fault-and-upgrade-domains"></a>Configurando domínios de falha e atualização

Em implantações de Service Fabric hospedadas pelo Azure, os domínios de falha e os domínios de atualização são definidos automaticamente. O Service Fabric seleciona e usa as informações de ambiente do Azure.

Se você estiver criando seu próprio cluster (ou desejar executar uma topologia específica em desenvolvimento), poderá fornecer o domínio de falha e as informações de domínio de atualização por conta própria. Neste exemplo, definimos um cluster de desenvolvimento local de nove nós que abrange três data centers (cada um com três racks). Esse cluster também tem três domínios de atualização distribuídos entre esses três data centers. Veja um exemplo da configuração no ClusterManifest.xml:

```xml
  <Infrastructure>
    <!-- IsScaleMin indicates that this cluster runs on one box/one single server -->
    <WindowsServer IsScaleMin="true">
      <NodeList>
        <Node NodeName="Node01" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType01" FaultDomain="fd:/DC01/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node02" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType02" FaultDomain="fd:/DC01/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node03" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType03" FaultDomain="fd:/DC01/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
        <Node NodeName="Node04" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType04" FaultDomain="fd:/DC02/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node05" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType05" FaultDomain="fd:/DC02/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node06" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType06" FaultDomain="fd:/DC02/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
        <Node NodeName="Node07" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType07" FaultDomain="fd:/DC03/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node08" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType08" FaultDomain="fd:/DC03/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node09" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType09" FaultDomain="fd:/DC03/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
      </NodeList>
    </WindowsServer>
  </Infrastructure>
```

Este exemplo usa ClusterConfig.jspara implantações autônomas:

```json
"nodes": [
  {
    "nodeName": "vm1",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD1"
  },
  {
    "nodeName": "vm2",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD2"
  },
  {
    "nodeName": "vm3",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD3"
  },
  {
    "nodeName": "vm4",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc2/r0",
    "upgradeDomain": "UD1"
  },
  {
    "nodeName": "vm5",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc2/r0",
    "upgradeDomain": "UD2"
  },
  {
    "nodeName": "vm6",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc2/r0",
    "upgradeDomain": "UD3"
  },
  {
    "nodeName": "vm7",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc3/r0",
    "upgradeDomain": "UD1"
  },
  {
    "nodeName": "vm8",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc3/r0",
    "upgradeDomain": "UD2"
  },
  {
    "nodeName": "vm9",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc3/r0",
    "upgradeDomain": "UD3"
  }
],
```

> [!NOTE]
> Quando você está definindo clusters via Azure Resource Manager, o Azure atribui domínios de falha e domínios de atualização. Portanto, a definição de seus tipos de nó e conjuntos de dimensionamento de máquinas virtuais em seu modelo de Azure Resource Manager não inclui informações sobre domínio de falha ou domínio de atualização.
>

## <a name="node-properties-and-placement-constraints"></a>Restrições de posicionamento e propriedades do nó

Às vezes (na verdade, na maioria das vezes), você desejará garantir que determinadas cargas de trabalho sejam executadas apenas em determinados tipos de nós no cluster. Por exemplo, algumas cargas de trabalho podem exigir GPUs ou SSDs, e outras podem não.

Um ótimo exemplo de direcionamento de hardware para cargas de trabalho específicas é quase todas as arquiteturas de n camadas. Determinados computadores servem como o lado de front-end ou de serviço de API do aplicativo e são expostos aos clientes ou à Internet. Diferentes computadores, normalmente com recursos de hardware diferentes, lidam com o trabalho das camadas de computação ou armazenamento. Normalmente, eles _não_ são expostos diretamente a clientes ou à Internet.

Service Fabric espera que, em alguns casos, cargas de trabalho específicas talvez precisem ser executadas em configurações de hardware específicas. Por exemplo:

* Um aplicativo de n camadas existente foi "retirado e deslocado" para um ambiente Service Fabric.
* Uma carga de trabalho deve ser executada em hardware específico para fins de desempenho, escala ou isolamento de segurança.
* Uma carga de trabalho deve ser isolada de outras cargas de trabalho por motivos de política ou consumo de recursos.

Para dar suporte a esses tipos de configuração, Service Fabric inclui marcas que você pode aplicar a nós. Essas marcas são chamadas de *propriedades de nó* . As *restrições de posicionamento* são as instruções anexadas a serviços individuais que você seleciona para uma ou mais propriedades de nó. Restrições de posicionamento definem onde os serviços devem ser executados. O conjunto de restrições é extensível. Qualquer par chave/valor pode funcionar.

![Cargas de trabalho diferentes para um layout de cluster][Image5]

### <a name="built-in-node-properties"></a>Propriedades de nó interno

Service Fabric define algumas propriedades de nó padrão que podem ser usadas automaticamente para que você não precise defini-las. As propriedades padrão definidas em cada nó são **NodeType** e **NodeName** .

Por exemplo, você pode escrever uma restrição de posicionamento como `"(NodeType == NodeType03)"` . **NodeType** é uma propriedade comumente usada. Ele é útil porque corresponde a 1:1 com um tipo de computador. Cada tipo de computador corresponde a um tipo de carga de trabalho em um aplicativo de n camadas tradicional.

![Restrições de posicionamento e propriedades do nó][Image6]

## <a name="placement-constraints-and-node-property-syntax"></a>Restrições de posicionamento e sintaxe de propriedade de nó

O valor especificado na propriedade node pode ser uma cadeia de caracteres, um booliano ou um Long assinado. A instrução no serviço é chamada de *restrição* de posicionamento porque ela restringe o local em que o serviço pode ser executado no cluster. A restrição pode ser qualquer instrução booliana que opere nas propriedades do nó no cluster. Os seletores válidos nessas instruções booleanas são:

* Verificações condicionais para a criação de instruções específicas:

  | Instrução | Syntax |
  | --- |:---:|
  | "igual a" | "==" |
  | "diferente de" | "!=" |
  | "maior que" | ">" |
  | "maior ou igual a" | ">=" |
  | "menor que" | "<" |
  | "menor ou igual a" | "<=" |

* Instruções booleanas para Agrupamento e operações lógicas:

  | Instrução | Syntax |
  | --- |:---:|
  | "e" | "&&" |
  | "ou" | "&#124;&#124;" |
  | "não" | "!" |
  | "agrupar como instrução única" | "()" |

Aqui estão alguns exemplos de instruções básicas de restrição:

* `"Value >= 5"`
* `"NodeColor != green"`
* `"((OneProperty < 100) || ((AnotherProperty == false) && (OneProperty >= 100)))"`

O serviço pode ser posicionado somente em nós em que a instrução de restrição de posicionamento geral é avaliada como "True". Os nós que não têm uma propriedade definida não correspondem a nenhuma restrição de posicionamento que contenha a propriedade.

Digamos que as seguintes propriedades de nó foram definidas para um tipo de nó no ClusterManifest.xml:

```xml
    <NodeType Name="NodeType01">
      <PlacementProperties>
        <Property Name="HasSSD" Value="true"/>
        <Property Name="NodeColor" Value="green"/>
        <Property Name="SomeProperty" Value="5"/>
      </PlacementProperties>
    </NodeType>
```

O exemplo a seguir mostra as propriedades de nó definidas por meio de ClusterConfig.jspara implantações autônomas ou Template.jsem clusters hospedados no Azure.

> [!NOTE]
> Em seu modelo de Azure Resource Manager, o tipo de nó geralmente é parametrizado. Seria como `"[parameters('vmNodeType1Name')]"` em vez de NodeType01.
>

```json
"nodeTypes": [
    {
        "name": "NodeType01",
        "placementProperties": {
            "HasSSD": "true",
            "NodeColor": "green",
            "SomeProperty": "5"
        },
    }
],
```

Você pode criar *restrições* de posicionamento de serviço para um serviço da seguinte maneira:

```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
serviceDescription.PlacementConstraints = "(HasSSD == true && SomeProperty >= 4)";
// Add other required ServiceDescription fields
//...
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

```PowerShell
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceType -Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementConstraint "HasSSD == true && SomeProperty >= 4"
```

Se todos os nós de NodeType01 forem válidos, você também poderá selecionar esse tipo de nó com a restrição `"(NodeType == NodeType01)"` .

As restrições de posicionamento de um serviço podem ser atualizadas dinamicamente durante o tempo de execução. Se precisar, você pode mover um serviço em um cluster, adicionar e remover requisitos e assim por diante. Service Fabric garante que o serviço permaneça ativo e disponível mesmo quando esses tipos de alterações forem feitos.

```csharp
StatefulServiceUpdateDescription updateDescription = new StatefulServiceUpdateDescription();
updateDescription.PlacementConstraints = "NodeType == NodeType01";
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/app/service"), updateDescription);
```

```PowerShell
Update-ServiceFabricService -Stateful -ServiceName $serviceName -PlacementConstraints "NodeType == NodeType01"
```

As restrições de posicionamento são especificadas para cada instância de serviço nomeada. As atualizações sempre assumem o lugar (substituem) do que foi especificado anteriormente.

A definição do cluster define as propriedades em um nó. Alterar as propriedades de um nó requer uma atualização para a configuração do cluster. Atualizar as propriedades de um nó requer que cada nó afetado seja reiniciado para informar suas novas propriedades. Service Fabric gerencia essas atualizações sem interrupção.

## <a name="describing-and-managing-cluster-resources"></a>Descrever e gerenciar recursos de cluster

Um dos trabalhos mais importantes de qualquer orquestrador é ajudar a gerenciar o consumo de recursos no cluster. Gerenciar recursos de cluster pode significar algumas coisas diferentes.

Primeiro, é necessário garantir que os computadores não sejam sobrecarregados. Isso significa garantir que eles não executem mais serviços do que conseguem tratar.

Em segundo lugar, há balanceamento e otimização, que são essenciais para a execução eficiente de serviços. Ofertas de serviço econômicas ou sensíveis a desempenho não podem permitir que alguns nós fiquem quentes enquanto outros estão frios. Os nós ativos levam à contenção de recursos e ao baixo desempenho. Os nós frios representam recursos desperdiçados e aumentam os custos.

Service Fabric representa recursos como *métricas* . As métricas são qualquer recurso lógico ou físico que você queira descrever para o Service Fabric. Exemplos de métricas são "WorkQueueDepth" ou "MemoryInMb". Para obter informações sobre os recursos físicos que Service Fabric podem controlar em nós, consulte [governança de recursos](service-fabric-resource-governance.md). Para obter informações sobre as métricas padrão usadas pelo Gerenciador de recursos de cluster e como configurar métricas personalizadas, consulte [Este artigo](service-fabric-cluster-resource-manager-metrics.md).

As métricas são diferentes das restrições de posicionamento e das propriedades de nó. Propriedades do nó são descritores estáticos dos nós propriamente ditos. As métricas descrevem os recursos que os nós têm e que os serviços consomem quando são executados em um nó. Uma propriedade de nó pode ser **HasSSD** e pode ser definida como true ou false. A quantidade de espaço disponível no SSD e o quanto é consumido pelos serviços seria uma métrica como "DriveSpaceInMb".

Assim como as restrições de posicionamento e as propriedades de nó, Service Fabric Gerenciador de recursos de cluster não entende o que significam os nomes das métricas. Os nomes de métrica são apenas cadeias de caracteres. É uma boa prática declarar unidades como parte dos nomes de métrica que você cria quando elas podem ser ambíguas.

## <a name="capacity"></a>Capacity

Se você desativou todo o *balanceamento* de recursos, Service Fabric Gerenciador de recursos de cluster ainda garantiria que nenhum nó ultrapassa sua capacidade. É possível gerenciar saturações de capacidade, a menos que o cluster esteja muito cheio ou que a carga de trabalho seja maior do que qualquer nó. A capacidade é outra *restrição* que o Gerenciador de recursos de cluster usa para entender a quantidade de um recurso de um nó. A capacidade restante também é rastreada para o cluster como um todo.

A capacidade e o consumo no nível de serviço são expressos em termos de métricas. Por exemplo, a métrica pode ser "ClientConnections" e um nó pode ter uma capacidade para "ClientConnections" de 32.768. Outros nós podem ter outros limites. Um serviço em execução nesse nó pode dizer que está consumindo, no momento, o 32.256 da métrica "ClientConnections".

Durante o tempo de execução, o Gerenciador de recursos de cluster rastreia a capacidade restante no cluster e nos nós. Para controlar a capacidade, o Gerenciador de recursos de cluster subtrai o uso de cada serviço da capacidade de um nó em que o serviço é executado. Com essas informações, o Gerenciador de recursos de cluster pode descobrir onde posicionar ou mover réplicas para que os nós não ultrapassem a capacidade.

![Capacidade e nós de cluster][Image7]

```csharp
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
ServiceLoadMetricDescription metric = new ServiceLoadMetricDescription();
metric.Name = "ClientConnections";
metric.PrimaryDefaultLoad = 1024;
metric.SecondaryDefaultLoad = 0;
metric.Weight = ServiceLoadMetricWeight.High;
serviceDescription.Metrics.Add(metric);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

```PowerShell
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("ClientConnections,High,1024,0)
```

Você pode ver as capacidades definidas no manifesto do cluster. Aqui está um exemplo para ClusterManifest.xml:

```xml
    <NodeType Name="NodeType03">
      <Capacities>
        <Capacity Name="ClientConnections" Value="65536"/>
      </Capacities>
    </NodeType>
```

Aqui está um exemplo de capacidades definidas por meio de ClusterConfig.jspara implantações autônomas ou Template.jsem clusters hospedados no Azure:

```json
"nodeTypes": [
    {
        "name": "NodeType03",
        "capacities": {
            "ClientConnections": "65536",
        }
    }
],
```

A carga de um serviço geralmente muda dinamicamente. Digamos que a carga de uma réplica de "ClientConnections" mudou de 1.024 para 2.048. O nó em que ele estava sendo executado tinha uma capacidade de apenas 512 restantes para essa métrica. Agora que o posicionamento da réplica ou da instância é inválido, porque não há espaço suficiente nesse nó. O Gerenciador de recursos de cluster precisa colocar o nó abaixo da capacidade novamente. Ele reduz a carga no nó que está acima da capacidade movendo uma ou mais réplicas ou instâncias desse nó para outros nós.

O Gerenciador de recursos de cluster tenta minimizar o custo da movimentação de réplicas. Você pode saber mais sobre o [custo de movimento](service-fabric-cluster-resource-manager-movement-cost.md) e sobre como [reequilibrar estratégias e regras](service-fabric-cluster-resource-manager-metrics.md).

## <a name="cluster-capacity"></a>Capacidade do cluster

Como o Gerenciador de recursos de Cluster Service Fabric mantém o cluster em total cheio? Com o carregamento dinâmico, não há muito que ele possa fazer. Os serviços podem ter seu pico de carga independentemente das ações que o Gerenciador de recursos de cluster usa. Como resultado, o cluster com muito espaço disponível hoje pode ser incapacitado se houver um pico no futuro.

Os controles no Gerenciador de recursos de cluster ajudam a evitar problemas. A primeira coisa que você pode fazer é impedir a criação de novas cargas de trabalho que poderiam fazer com que o cluster se tornasse cheio.

Digamos que você crie um serviço sem estado e que ele tenha alguma carga associada a ele. O serviço se preocupa com a métrica "DiskSpaceInMb". O serviço consumirá cinco unidades de "DiskSpaceInMb" para cada instância do serviço. Você deseja criar três instâncias do serviço. Isso significa que você precisa de 15 unidades de "DiskSpaceInMb" para estar presente no cluster para você até mesmo criar essas instâncias de serviço.

O Gerenciador de recursos de cluster calcula continuamente a capacidade e o consumo de cada métrica para que possa determinar a capacidade restante no cluster. Se não houver espaço suficiente, o Gerenciador de recursos de cluster rejeitará a chamada para criar um serviço.

Como o requisito é apenas que 15 unidades estarão disponíveis, você pode alocar esse espaço de várias maneiras diferentes. Por exemplo, pode haver uma unidade restante de capacidade em 15 nós diferentes ou três unidades restantes de capacidade em cinco nós diferentes. Se o Gerenciador de recursos de cluster puder reorganizar as coisas para que haja cinco unidades disponíveis em três nós, ele colocará o serviço. Normalmente, é possível reorganizar o cluster, a menos que ele esteja quase cheio ou que os serviços existentes não possam ser consolidados por alguma razão.

## <a name="node-buffer-and-overbooking-capacity"></a>Buffer de nó e capacidade de superregistro

Se uma capacidade de nó para uma métrica for especificada, o Gerenciador de recursos de cluster nunca colocará ou moverá réplicas para um nó se a carga total for acima da capacidade do nó especificado. Às vezes, isso pode impedir o posicionamento de novas réplicas ou a substituição de réplicas com falha se o cluster estiver quase em plena capacidade e uma réplica com uma carga grande precisar ser colocada, substituída ou movida.

Para fornecer mais flexibilidade, você pode especificar o buffer do nó ou a capacidade de superregistro. Quando o buffer de nó ou a capacidade de sobreposição for especificada para uma métrica, o Gerenciador de recursos de cluster tentará posicionar ou mover réplicas de forma que o buffer ou a capacidade de superalocação permaneça inutilizado, mas permite que o buffer ou a capacidade de superalocação seja usada, se necessário, para ações que aumentam a disponibilidade do serviço, como:

* Novo posicionamento de réplica ou substituição de réplicas com falha
* Posicionamento durante as atualizações
* Correção de violações de restrição flexível e rígida
* Desfragmentação

A capacidade de buffer de nó representa uma parte reservada da capacidade abaixo da capacidade de nó especificada e a capacidade de superregistro representa uma parte da capacidade extra acima da capacidade do nó especificado. Em ambos os casos, o Gerenciador de recursos de cluster tentará manter essa capacidade livre.

Por exemplo, se um nó tiver uma capacidade especificada para a métrica *CpuUtilization* de 100 e a porcentagem de buffer do nó para essa métrica for definida como 20%, as capacidades total e não armazenada em buffer serão 100 e 80, respectivamente, e o Gerenciador de recursos de cluster não coloca mais de 80 unidades de carga no nó durante circunstâncias normais.

![Capacidade total igual à capacidade do nó (buffer do nó + sem buffer)](./media/service-fabric-cluster-resource-manager-cluster-description/node-capacity.png)

O buffer do nó deve ser usado quando você deseja reservar uma parte da capacidade do nó que será usada somente para ações que aumentam a disponibilidade do serviço mencionada acima.

Por outro lado, se a porcentagem de superregistro de nó for usada e definida como 20%, as capacidades total e não armazenada em buffer serão 120 e 100, respectivamente.

![Capacidade total igual à capacidade de superlivro mais a capacidade do nó (sobreregistro + sem buffer)](./media/service-fabric-cluster-resource-manager-cluster-description/node-capacity-with-overbooking.png)

A capacidade de superregistro deve ser usada quando você deseja permitir que o Gerenciador de recursos de cluster Coloque réplicas em um nó, mesmo que o uso total de recursos exceda capacidade. Isso pode ser usado para fornecer disponibilidade adicional para os serviços às custas do desempenho. Se a sobreinscrição for usada, a lógica do aplicativo do usuário precisará ser capaz de funcionar com menos recursos físicos do que pode exigir.

Se o buffer do nó ou as capacidades de superalocação forem especificadas, o Gerenciador de recursos de cluster não moverá ou colocará réplicas se a carga total no nó de destino ultrapassar a capacidade total (a capacidade do nó no caso do buffer do nó e da capacidade do nó + a capacidade de superregistro em caso de sobreposição).

A capacidade de superregistro também pode ser especificada para ser infinita. Nesse caso, o Gerenciador de recursos de cluster tentará manter a carga total no nó abaixo da capacidade do nó especificado, mas poderá potencialmente posicionar uma carga muito maior no nó, o que pode levar a uma degradação grave de desempenho.

Uma métrica não pode ter o buffer de nó e a capacidade de superregistro especificada para ele ao mesmo tempo.

Veja um exemplo de como especificar o buffer de nó ou as capacidades de superpasta no *ClusterManifest.xml* :

```xml
<Section Name="NodeBufferPercentage">
    <Parameter Name="SomeMetric" Value="0.15" />
</Section>
<Section Name="NodeOverbookingPercentage">
    <Parameter Name="SomeOtherMetric" Value="0.2" />
    <Parameter Name=”MetricWithInfiniteOverbooking” Value=”-1.0” />
</Section>
```

Veja um exemplo de como especificar o buffer do nó ou as capacidades de superdimensionamento por meio de *ClusterConfig.js* para implantações autônomas ou *Template.jsno* para clusters hospedados no Azure:

```json
"fabricSettings": [
  {
    "name": "NodeBufferPercentage",
    "parameters": [
      {
          "name": "SomeMetric",
          "value": "0.15"
      }
    ]
  },
  {
    "name": "NodeOverbookingPercentage",
    "parameters": [
      {
          "name": "SomeOtherMetric",
          "value": "0.20"
      },
      {
          "name": "MetricWithInfiniteOverbooking",
          "value": "-1.0"
      }
    ]
  }
]
```

## <a name="next-steps"></a>Próximas etapas

* Para obter informações sobre a arquitetura e o fluxo de informações no Gerenciador de recursos de cluster, consulte [visão geral da arquitetura do Gerenciador de recursos de cluster](service-fabric-cluster-resource-manager-architecture.md).
* A definição de métricas de desfragmentação é uma maneira de consolidar a carga em nós em vez de difundir. Para saber como configurar a desfragmentação, consulte [desfragmentação de métricas e carga em Service Fabric](service-fabric-cluster-resource-manager-defragmentation-metrics.md).
* Comece desde o início e [obtenha uma introdução ao Service Fabric cluster Resource Manager](service-fabric-cluster-resource-manager-introduction.md).
* Para saber como o Gerenciador de recursos de cluster gerencia e equilibra a carga no cluster, consulte [equilibrando seu cluster de Service Fabric](service-fabric-cluster-resource-manager-balancing.md).

[Image1]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-domains.png
[Image2]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-uneven-fault-domain-layout.png
[Image3]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domains-with-placement.png
[Image4]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domain-layout-strategies.png
[Image5]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-layout-different-workloads.png
[Image6]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-placement-constraints-node-properties.png
[Image7]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-nodes-and-capacity.png
