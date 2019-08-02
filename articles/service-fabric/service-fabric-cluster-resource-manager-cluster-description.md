---
title: Descrever um cluster usando o Cluster Resource Manager | Microsoft Docs
description: Descreva um cluster do Service Fabric especificando domínios de falha, domínios de atualização, propriedades de nó e capacidades de nó para o Cluster Resource Manager.
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: 55f8ab37-9399-4c9a-9e6c-d2d859de6766
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 22ccb21a208bbe8e825bff9f7602bfca05990816
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67271652"
---
# <a name="describe-a-service-fabric-cluster-by-using-cluster-resource-manager"></a>Descrever um cluster do Service Fabric usando o Gerenciador de recursos de Cluster
O recurso de Gerenciador de recursos de Cluster do Azure Service Fabric fornece vários mecanismos para descrever um cluster:

* Domínios de falha
* Domínios de atualização
* Propriedades de nó
* Capacidades de nó

Durante o tempo de execução, o Cluster Resource Manager usa essas informações para garantir a alta disponibilidade dos serviços executados no cluster. Ao aplicar essas regras importantes, ele também tenta otimizar o consumo de recursos dentro do cluster.

## <a name="fault-domains"></a>Domínios de falha
Um domínio de falha é qualquer área da falha coordenada. Um único computador é um domínio de falha. Ele pode falhar sozinho por vários motivos, de energia falhas de falhas de unidade a firmware NIC inválido de fornecimento. 

Computadores conectados ao mesmo comutador Ethernet estão no mesmo domínio de falha. Portanto, são computadores que compartilham uma única fonte de alimentação ou em um único local. 

Porque é natural para falhas de hardware se sobreponham, os domínios de falha são hierárquicos por natureza. Eles são representados como URIs no Service Fabric.

É importante que domínios de falha são definidos corretamente porque o Service Fabric usa essas informações para posicionar os serviços com segurança. O Service Fabric não quer posicionar os serviços, de modo que a perda de um domínio de falha (causada pela falha de algum componente) faz com que um serviço inoperante. 

No ambiente do Azure, o Service Fabric usa as informações de domínio de falha fornecidas pelo ambiente para configurar corretamente os nós no cluster em seu nome. Para instâncias autônomas do Service Fabric, os domínios de falha são definidos no momento em que o cluster é configurado. 

> [!WARNING]
> É importante que as informações de domínio de falha fornecidas ao Service Fabric são precisas. Por exemplo, digamos que nós do cluster do Service Fabric estão em execução dentro de 10 máquinas virtuais, em execução em hosts físicos 5. Nesse caso, mesmo que haja dez máquinas virtuais, há apenas cinco domínios de falha (de nível superior) diferentes. Compartilhar o mesmo host físico faz com que as VMs compartilhem o mesmo domínio de falha raiz, pois as VMs apresentar falhas coordenadas se seu host físico falhar.  
>
> O Service Fabric espera que o domínio de falha de um nó não seja alterado. Outros mecanismos para garantir a alta disponibilidade das VMs, como [HA-VMs](https://technet.microsoft.com/library/cc967323.aspx), pode causar conflitos com o Service Fabric. Esses mecanismos usam migração transparente de VMs de um host para outro. Eles não reconfigurar ou notificar o código em execução dentro da VM. Como tal, eles são *não tem suporte* como ambientes de execução do Service Fabric clusters. 
>
> O Service Fabric deve ser a única tecnologia de alta disponibilidade empregada. Mecanismos como migração de VMs e SANs ao vivo não são necessários. Se esses mecanismos são usados em conjunto com o Service Fabric, eles _reduzir_ confiabilidade e disponibilidade de aplicativos. O motivo é que eles introduzem complexidade adicional, adicione fontes de falha centralizadas e usam estratégias de disponibilidade e confiabilidade que entram em conflito com aqueles no Service Fabric. 
>
>

No gráfico a seguir, colorimos todas as entidades que contribuem para domínios de falha e a listam de todos os domínios de falha diferentes resultantes. Neste exemplo, temos datacenters (“DC”), racks (“R”) e folhas (“B”). Se cada folha contém mais de uma máquina virtual, pode haver outra camada na hierarquia de domínios de falha.

<center>

![Nós organizados por meio de domínios de falha][Image1]
</center>

Durante o tempo de execução, o Gerenciador de recursos de Cluster do Service Fabric considera os domínios de falha no cluster e planeja layouts. As réplicas ou instâncias sem monitoração de estado para um serviço são distribuídas para que fiquem em domínios de falha separados. Distribuir o serviço entre domínios de falha garante que a disponibilidade do serviço não é comprometida quando um domínio de falha falha em qualquer nível da hierarquia.

Gerenciador de recursos de cluster não se importa com quantas camadas existem na hierarquia de domínios de falha. Ele tenta assegurar que a perda de qualquer parte da hierarquia não afeta os serviços em execução nele. 

É melhor se o mesmo número de nós em cada nível de profundidade na hierarquia de domínios de falha. Se a "árvore" de domínios de falha é desbalanceada em seu cluster, é mais difícil para o Cluster Resource Manager para descobrir a melhor alocação dos serviços. Layouts de domínios de falha desequilibrados significam que a perda de alguns domínios afeta a disponibilidade dos serviços mais do que outros domínios. Como resultado, o Cluster Resource Manager fica dividido entre as duas metas: 

* Ele deseja usar as máquinas no domínio "pesado" colocando serviços neles. 
* Ele quer posicionar serviços em outros domínios para que a perda de um domínio não cause problemas. 

Qual é a aparência de um domínio desequilibrado? O diagrama a seguir mostra dois layouts de cluster diferentes. No primeiro exemplo, nós são distribuídos uniformemente entre os domínios de falha. No segundo exemplo, um domínio de falha tem muitos mais nós do que outros domínios de falha. 

<center>

![Dois layouts de cluster diferentes][Image2]
</center>

No Azure, a escolha de quais falhas o domínio contém um nó é gerenciada por você. Mas, dependendo do número de nós provisionados, você ainda pode acabar com domínios de falha que têm mais nós eles que em outros. 

Por exemplo, digamos que você tiver cinco domínios de falha no cluster, mas Provisione sete nós para um tipo de nó (**NodeType**). Nesse caso, os primeiro dois domínios de falha ficam com mais nós. Se você continuar a implantar mais **NodeType** instâncias com apenas algumas instâncias, o problema fica ainda pior. Por esse motivo, é recomendável que o número de nós em cada tipo de nó é um múltiplo do número de domínios de falha.

## <a name="upgrade-domains"></a>Domínios de atualização
Domínios de atualização são outro recurso que ajuda o Gerenciador de recursos de Cluster do Service Fabric entender o layout do cluster. Domínios de atualização definem conjuntos de nós que são atualizados ao mesmo tempo. Domínios de atualização ajudam o Gerenciador de recursos de Cluster de entender e coordenar operações de gerenciamento, como atualizações.

Domínios de atualização são muito semelhantes aos domínios, mas com algumas diferenças importantes. Primeiro, áreas de falhas de hardware coordenadas definem domínios de falha. Os domínios de atualização, por outro lado, são definidos pela política. Você pode decidir quantos deseja, em vez de deixar o ambiente determinam o número. Você pode ter tantos domínios de atualização como você faz de nós. Outra diferença entre domínios de falha e domínios de atualização é que os domínios de atualização não são hierárquicos. Em vez disso, eles são mais parecido com uma marca simples. 

O diagrama a seguir mostra três domínios de atualização distribuídos em três domínios de falha. Ele também mostra um possível posicionamento para três réplicas diferentes de um serviço com monitoração de estado, onde cada um fica em diferente domínios de falha e atualização. Esse posicionamento permite perder um domínio de falha no meio de uma atualização de serviço e ainda ter uma cópia do código e dados.  

<center>

![Posicionamento com falha e atualização de domínios][Image3]
</center>

Há prós e contras para ter um grande número de domínios de atualização. Mais domínios de atualização significa que cada etapa da atualização é mais granular e afeta um número menor de nós ou serviços. Menos serviços precisam ser movidos de cada vez, introduzindo menos variação no sistema. Isso tende a aumentar a confiabilidade, pois menor do serviço é afetado por qualquer problema introduzido durante a atualização. Mais domínios de atualização também significam que você precisa de menos buffer disponível em outros nós para lidar com o impacto da atualização. 

Por exemplo, se você tiver cinco domínios de atualização, os nós em cada um estarão lidando com aproximadamente 20 por cento de seu tráfego. Se você precisar desativar esse domínio de atualização para uma atualização, essa carga normalmente precisará ir para algum lugar. Como você tem quatro domínios de atualização de restantes, cada um deve ter espaço para cerca de 5% do tráfego total. Mais domínios de atualização significam que você precisa de menos buffer em nós do cluster. 

Considere se você tivesse 10 domínios de atualização. Nesse caso, cada domínio de atualização manipula apenas cerca de 10% do tráfego total. Quando uma atualização percorresse o cluster, cada domínio precisaria ter espaço para 1.1 apenas cerca de % do tráfego total. Mais domínios de atualização geralmente permitem que você execute seus nós com utilização mais alta, porque você precisa de capacidade menor reservada. O mesmo vale para domínios de falha.  

A desvantagem de ter vários domínios de atualização é que as atualizações tendem a levar mais tempo. O Service Fabric aguarda um curto período depois de um domínio de atualização é concluído e executa verificações antes de começar a atualizar o próximo. Esses atrasos permitem detectar problemas introduzidos pela atualização antes que ela continue. Essa compensação é aceitável, pois isso evita que alterações incorretas afetem grande parte do serviço de uma só vez.

A presença de poucos domínios de atualização tem muitos efeitos negativos. Enquanto cada domínio de atualização é para baixo e que está sendo atualizado, uma grande parte de sua capacidade geral fica indisponível. Por exemplo, se você tiver apenas três domínios de atualização, você está desativará aproximadamente um terço de sua capacidade de cluster ou serviço geral por vez. Ter grande parte do seu serviço inoperante ao mesmo tempo não é desejável, porque você precisa de capacidade suficiente no restante do seu cluster para lidar com a carga de trabalho. Manter que esse buffer significa que, durante a operação normal, esses nós são que menos carregados do que eles seriam caso contrário. Isso aumenta o custo de execução de seu serviço.

Não há um limite real para o número total de domínios de falha ou de atualização em um ambiente nem restrições sobre como eles se sobrepõem. Mas existem padrões comuns:

- Domínios de falha e domínios de atualização mapeados 1:1
- Um domínio de atualização por nó (instância de sistema operacional física ou virtual)
- Um modelo "distribuído" ou "matriz" que os domínios de falha e domínios de atualização formam uma matriz com computadores geralmente são executados diagonalmente

<center>

![Layouts de domínios de falha e atualização][Image4]
</center>

Há uma resposta melhor para a qual layout deve ser escolhido. Cada um tem prós e contras. Por exemplo, o modelo de 1FD:1UD é simples de configurar. O modelo de um domínio de atualização por modelo de nó é mais como o que as pessoas são usados para. Durante as atualizações, cada nó é atualizado de forma independente. Isso é semelhante à forma como pequenos conjuntos de computadores eram atualizados manualmente no passado.

O modelo mais comum é a matriz de FD/UD, em que os domínios de falha e domínios de atualização formam uma tabela e nós são posicionados começando ao longo da diagonal. Esse é o modelo usado por padrão nos clusters do Service Fabric no Azure. Para clusters com vários nós, tudo acaba ficando parecido com um padrão de matriz denso.

> [!NOTE]
> Clusters do Service Fabric hospedados no Azure não dão suporte para alterar a estratégia padrão. Somente os clusters autônomos oferecem essa personalização.
>

## <a name="fault-and-upgrade-domain-constraints-and-resulting-behavior"></a>Restrições de domínio de falha e de atualização e o comportamento resultante
### <a name="default-approach"></a>Abordagem padrão
Por padrão, o Gerenciador de recursos de Cluster mantém serviços balanceados entre os domínios de falha e atualização. Isso é modelado como uma [restrição](service-fabric-cluster-resource-manager-management-integration.md). A restrição para estados de domínios de falha e atualização: "Para uma determinada partição de serviço, deve jamais haver uma diferença maior do que um no número de objetos de serviço (instâncias de serviço sem monitoração de estado ou réplicas de serviço com estado) entre dois domínios no mesmo nível da hierarquia."

Digamos que essa restrição forneça uma garantia de "diferença máxima". A restrição para domínios de falha e atualização impede determinadas movimentações ou disposições que violam a regra.

Por exemplo, digamos que temos um cluster com seis nós, configurados com cinco domínios de falha e cinco domínios de atualização.

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | | |N3 | | |
| **UD3** | | | |N4 | |
| **UD4** | | | | |N5 |

Agora vamos supor que criamos um serviço com um **TargetReplicaSetSize** (ou, para um serviço sem monitoração de estado, **InstanceCount**) valor de cinco. As réplicas recaem sobre N1 a N5. Na verdade, N6 nunca será usado, independentemente de quantos serviços como este você criar. Mas por quê? Vamos observar a diferença entre o layout atual e o que aconteceria se N6 fosse escolhido.

Aqui está o nosso layout e o número total de réplicas por domínio de falha e atualização:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** | |R2 | | | |1 |
| **UD2** | | |R3 | | |1 |
| **UD3** | | | |R4 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

Esse layout é balanceado em termos de nós por domínio de falha e domínio de atualização. Ele também é balanceado em termos de número de réplicas por domínio de falha e atualização. Cada domínio possui o mesmo número de nós e o mesmo número de réplicas.

Agora, vamos ver o que aconteceria se tivéssemos usado N6 em vez de N2. Como as réplicas seriam distribuídas?

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R5 | | | | |1 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |2 |0 |1 |1 |1 |- |

Esse layout viola nossa definição de garantia de "diferença máxima" para a restrição de domínio de falha. FD0 tem duas réplicas, enquanto FD1 tem zero. A diferença entre FD0 e FD1 é um total de dois, que é maior que a diferença máxima de um. Porque a restrição for violada, o Cluster Resource Manager não permite essa disposição. Da mesma forma, se escolhêssemos N2 e N6 (em vez de N1 e N2), obteríamos:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** | | | | | |0 |
| **UD1** |R5 |R1 | | | |2 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

Esse layout é balanceado em termos de domínios de falha. Mas agora ela está violando a restrição de domínio de atualização porque UD0 tem zero réplicas e UD1 tem duas. Esse layout também é inválido e não será escolhido pelo Gerenciador de recursos de Cluster.

Essa abordagem de distribuição de réplicas com estado ou de instâncias sem estado fornece a melhor tolerância a falhas possível. Se um domínio fica inoperante, o número mínimo de réplicas/instâncias é perdido. 

Por outro lado, essa abordagem pode ser muito restrita e não permitir que o cluster utilize todos os recursos. Para determinadas configurações de cluster, alguns nós não podem ser usados. Isso pode causar o Service Fabric não colocar seus serviços, resultando em mensagens de aviso. No exemplo anterior, alguns de nós do cluster não podem ser usados (N6 no exemplo). Mesmo se você adicionou nós ao cluster (N7 – N10), as réplicas/instâncias devem ser colocadas somente em N1 – N5 devido a restrições em domínios de falha e atualização. 

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | |N10 |
| **UD1** |N6 |N2 | | | |
| **UD2** | |N7 |N3 | | |
| **UD3** | | |N8 |N4 | |
| **UD4** | | | |N9 |N5 |



### <a name="alternative-approach"></a>Abordagem alternativa

Gerenciador de recursos de cluster dá suporte a outra versão da restrição para domínios de falha e atualização. Ele permite o posicionamento e ainda garantir um nível mínimo de segurança. A restrição alternativa pode ser declarada da seguinte maneira: "Para uma determinada partição de serviço, distribuição de réplica entre domínios deve garantir que a partição não sofra uma perda de quorum." Digamos que essa restrição forneça uma garantia de "segurança de quorum". 

> [!NOTE]
> Para um serviço com estado, definimos *perda de quorum* em uma situação em que a maioria das réplicas de partição estão inativas ao mesmo tempo. Por exemplo, se **TargetReplicaSetSize** for cinco, um conjunto de quaisquer três réplicas representará um quorum. Da mesma forma, se **TargetReplicaSetSize** é seis, quatro réplicas são necessárias para o quorum. Em ambos os casos, não mais de duas réplicas podem ser para baixo ao mesmo tempo se a partição deve continuar funcionando normalmente. 
>
> Para um serviço sem monitoração de estado, há algo como *perda de quorum*. Serviços sem monitoração de estado continuam a funcionar normalmente mesmo se a maioria das instâncias ficarem inativos ao mesmo tempo. Portanto, vamos nos concentrar nos serviços sem estado no restante deste artigo.
>

Voltar para o exemplo anterior. Com a versão "segurança de quorum" da restrição, todos os três layouts seria válidos. Mesmo se a falha de FD0 no segundo layout ou UD1 falha no terceiro layout, a partição ainda teria quorum. (A maioria das réplicas ainda estariam operantes.) Com esta versão da restrição, N6 quase sempre podem ser utilizadas.

A abordagem de "segurança de quorum" oferece mais flexibilidade do que a abordagem de "diferença máxima". O motivo é que ele é mais fácil encontrar as distribuições de réplica que são válidas em praticamente qualquer topologia de cluster. No entanto, essa abordagem não garante a melhor característica de tolerância a falhas, pois algumas falhas são piores que outras. 

Na pior das hipóteses, a maioria das réplicas pode ser perdida com a falha de um domínio e uma réplica adicional. Por exemplo, em vez de três falhas de exigência de perder quorum com cinco réplicas ou instâncias, você agora pode perder a maioria com apenas duas falhas. 

### <a name="adaptive-approach"></a>Abordagem adaptável
Como ambas as abordagens têm vantagens e desvantagens, apresentamos uma abordagem adaptável que combina essas duas estratégias.

> [!NOTE]
> Esse é o comportamento padrão começando com o Service Fabric versão 6.2. 
> 
> A abordagem adaptável usa a lógica “diferença máxima” por padrão e muda para a lógica “segurança de quorum” somente quando necessário. Gerenciador de recursos de cluster detecta automaticamente qual estratégia é necessária examinando como o cluster e os serviços são configurados.
> 
> Gerenciador de recursos de cluster deve usar a lógica "baseado em quorum" para um serviço de ambas as condições forem verdadeiras:
>
> * **TargetReplicaSetSize** para o serviço é igualmente divisível pelo número de domínios de falha e o número de domínios de atualização.
> * O número de nós é menor que ou igual ao número de domínios de falha, multiplicados pelo número de domínios de atualização.
>
> Tenha em mente que o Gerenciador de recursos de Cluster usará essa abordagem para serviços com e sem estado, mesmo que a perda de quorum não é relevante para serviços sem monitoração de estado.

Voltemos para o exemplo anterior e suponha que um cluster agora tem oito nós. O cluster ainda está configurado com cinco domínios de falha e cinco domínios de atualização e o **TargetReplicaSetSize** valor de um serviço hospedado nesse cluster permanece cinco. 

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | |N7 |N3 | | |
| **UD3** | | |N8 |N4 | |
| **UD4** | | | | |N5 |

Como todas as condições necessárias são atendidas, o Gerenciador de recursos de Cluster usará a lógica "baseado em quorum" em distribuir o serviço. Isso permite o uso da N6 N8. Uma distribuição de serviço possível nesse caso, teria esta aparência:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R2 | | | | |1 |
| **UD2** | |R3 |R4 | | |2 |
| **UD3** | | | | | |0 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |2 |1 |1 |0 |1 |- |

Se seu serviço **TargetReplicaSetSize** valor for reduzido para quatro (por exemplo), Gerenciador de recursos de Cluster notará essa alteração. Ele continuará usando a lógica "diferença máxima" porque **TargetReplicaSetSize** não é mais divisível pelo número de domínios de falha e domínios de atualização mais. Como resultado, certos movimentos de réplica ocorrerá para distribuir as quatro réplicas restantes em nós N1 a N5. Dessa forma, a versão "diferença máxima" a lógica de domínio de falha e atualização não seja violada. 

No layout anterior, se o **TargetReplicaSetSize** valor é cinco e N1 for removido do cluster, o número de domínios de atualização será igual a quatro. Novamente, o Cluster Resource Manager é iniciado usando a lógica "diferença máxima" porque o número de domínios de atualização não dividido de maneira uniforme o serviço **TargetReplicaSetSize** mais de valor. Como resultado, a réplica R1, quando compilada novamente, deve parar em N4 para que a restrição para o domínio de falha e de atualização não seja violada.

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |N/D |N/D |N/D |N/D |N/D |N/D |
| **UD1** |R2 | | | | |1 |
| **UD2** | |R3 |R4 | | |2 |
| **UD3** | | | |R1 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

## <a name="configuring-fault-and-upgrade-domains"></a>Configurando domínios de falha e atualização
Em implantações do Service Fabric hospedado no Azure, domínios de falha e domínios de atualização são definidos automaticamente. O Service Fabric seleciona e usa as informações de ambiente do Azure.

Se você estiver criando seu próprio cluster (ou deseja executar uma topologia específica no desenvolvimento), você pode fornecer o domínio de falha e informações de domínio de atualização por conta própria. Neste exemplo, definimos um cluster de desenvolvimento local de nove nós que abrange três data centers (cada um com três racks). Este cluster também tem três domínios de atualização distribuídos entre esses três datacenters. Aqui está um exemplo da configuração no Clustermanifest:

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

Este exemplo usa clusterconfig. JSON para implantações autônomas:

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
> Quando você estiver definindo clusters por meio do Azure Resource Manager, o Azure atribui domínios de falha e domínios de atualização. Portanto, a definição de seus tipos de nó e o dimensionamento de máquinas virtuais define modelo do Azure Resource Manager não inclui informações sobre o domínio de falha ou domínio de atualização.
>

## <a name="node-properties-and-placement-constraints"></a>Restrições de posicionamento e propriedades do nó
Às vezes (na verdade, na maioria das vezes), você desejará garantir que determinadas cargas de trabalho executados somente em determinados tipos de nós no cluster. Por exemplo, algumas cargas de trabalho podem exigir GPUs ou SSDs e outras não podem. 

Um ótimo exemplo de direcionamento de hardware para cargas de trabalho específicas é praticamente toda arquitetura de n camadas. Determinados computadores servem como o front-end ou API atendendo ao lado do aplicativo e são expostos a clientes ou à internet. Diferentes computadores, normalmente com recursos de hardware diferentes, lidam com o trabalho das camadas de computação ou armazenamento. Normalmente, eles _não_ são expostos diretamente a clientes ou à Internet. 

O Service Fabric espera que em alguns casos, cargas de trabalho específicas talvez precise executar em configurações de hardware específica. Por exemplo:

* Um aplicativo de n camadas existente foi "transferido e posicionado" em um ambiente do Service Fabric.
* Uma carga de trabalho deve ser executada em um hardware específico por motivos de isolamento de desempenho, escala ou segurança.
* Uma carga de trabalho deve ser isolada de outras cargas de trabalho política ou recursos por motivos de consumo.

Para dar suporte a esses tipos de configurações, o Service Fabric inclui marcas que você pode aplicar a nós. Essas marcas são chamadas de *propriedades de nó*. *Restrições de posicionamento* são as instruções anexadas a serviços individuais que você selecionar para uma ou mais propriedades de nó. Restrições de posicionamento definem onde os serviços devem ser executados. O conjunto de restrições é extensível. Qualquer par de chave/valor pode trabalhar. 

<center>

![Diferentes cargas de trabalho para um layout de cluster][Image5]
</center>

### <a name="built-in-node-properties"></a>Propriedades de nó interno
O Service Fabric define algumas propriedades de nó padrão que podem ser usadas automaticamente, portanto, você não precisa defini-las. As propriedades padrão definidas em cada nó são **NodeType** e **NodeName**. 

Por exemplo, você pode escrever uma restrição de posicionamento como `"(NodeType == NodeType03)"`. **NodeType** é uma propriedade comumente usada. Ele é útil, pois corresponde 1:1 a um tipo de uma máquina. Cada tipo de computador corresponde a um tipo de carga de trabalho em um aplicativo de n camadas tradicional.

<center>

![Restrições de posicionamento e propriedades de nó][Image6]
</center>

## <a name="placement-constraints-and-node-property-syntax"></a>Restrições de posicionamento e a sintaxe de propriedade do nó 
O valor especificado na propriedade do nó pode ser uma cadeia de caracteres, booliano, ou signed long. A instrução no serviço é chamada um posicionamento *restrição* porque ela restringe onde o serviço pode ser executado no cluster. A restrição pode ser qualquer instrução booliana que opera sobre as propriedades de nó no cluster. Os seletores válidos nessas instruções Boolianas são:

* Verificações condicionais para a criação de instruções:

  | Instrução | Sintaxe |
  | --- |:---:|
  | "igual a" | "==" |
  | "diferente de" | "!=" |
  | "maior que" | ">" |
  | "maior ou igual a" | ">=" |
  | "menor que" | "<" |
  | "menor ou igual a" | "<=" |

* Instruções Boolianas para operações de agrupamento e lógicas:

  | Instrução | Sintaxe |
  | --- |:---:|
  | "e" | "&&" |
  | "ou" | "&#124;&#124;" |
  | "não" | "!" |
  | "agrupar como instrução única" | "()" |

Aqui estão alguns exemplos de instruções de restrição básica:

  * `"Value >= 5"`
  * `"NodeColor != green"`
  * `"((OneProperty < 100) || ((AnotherProperty == false) && (OneProperty >= 100)))"`

O serviço pode ser posicionado somente em nós em que a instrução de restrição de posicionamento geral é avaliada como "True". Nós que não têm uma propriedade definida não correspondem a nenhuma restrição de posicionamento que contém a propriedade.

Digamos que as seguintes propriedades de nó foram definidas para um tipo de nó no Clustermanifest:

```xml
    <NodeType Name="NodeType01">
      <PlacementProperties>
        <Property Name="HasSSD" Value="true"/>
        <Property Name="NodeColor" Value="green"/>
        <Property Name="SomeProperty" Value="5"/>
      </PlacementProperties>
    </NodeType>
```

O exemplo a seguir mostra as propriedades de nó definidas por meio de clusterconfig. JSON para implantações autônomas ou Template. JSON para clusters hospedados no Azure. 

> [!NOTE]
> Em seu modelo do Azure Resource Manager, o tipo de nó costuma ser parametrizado. Ele teria a seguinte aparência `"[parameters('vmNodeType1Name')]"` em vez de NodeType01.
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

Você pode criar o posicionamento do serviço *restrições* para um serviço da seguinte maneira:

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

Se todos os nós de NodeType01 forem válidos, você também pode selecionar esse tipo de nó com a restrição `"(NodeType == NodeType01)"`.

Restrições de posicionamento do serviço podem ser atualizadas dinamicamente durante o tempo de execução. Se você precisar, você pode mover um serviço no cluster, adicionar e remover requisitos e assim por diante. O Service Fabric garante que o serviço permaneça ativo e disponível mesmo quando esses tipos de alterações forem feitos.

```csharp
StatefulServiceUpdateDescription updateDescription = new StatefulServiceUpdateDescription();
updateDescription.PlacementConstraints = "NodeType == NodeType01";
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/app/service"), updateDescription);
```

```PowerShell
Update-ServiceFabricService -Stateful -ServiceName $serviceName -PlacementConstraints "NodeType == NodeType01"
```

Restrições de posicionamento são especificadas para cada instância de serviço nomeado. As atualizações sempre assumem o lugar (substituem) do que foi especificado anteriormente.

A definição do cluster define as propriedades em um nó. Alterando as propriedades de um nó requer uma atualização para a configuração do cluster. Atualizar as propriedades de um nó requer que cada nó afetado seja reiniciado para informar suas novas propriedades. O Service Fabric gerencia essas atualizações sem interrupção.

## <a name="describing-and-managing-cluster-resources"></a>Descrever e gerenciar recursos de cluster
Um dos trabalhos mais importantes de qualquer orquestrador é ajudar a gerenciar o consumo de recursos no cluster. Gerenciar recursos de cluster pode significar algumas coisas diferentes. 

Primeiro, é necessário garantir que os computadores não sejam sobrecarregados. Isso significa garantir que eles não executem mais serviços do que conseguem tratar. 

Em segundo lugar, há balanceamento e otimização, que são fundamentais para executar serviços com eficiência. Ofertas de serviço econômico ou sensíveis ao desempenho não é possível permitir que alguns nós fiquem frios enquanto outros estão inativos. Nós quentes levam a contenção de recursos e desempenho ruim. Nós frios representam recursos desperdiçados e aumento de custos. 

O Service Fabric representa recursos conforme *métricas*. As métricas são qualquer recurso lógico ou físico que você queira descrever para o Service Fabric. Exemplos de métricas são "WorkQueueDepth" ou "MemoryInMb". Para obter informações sobre os recursos físicos que podem controlar o Service Fabric em nós, consulte [governança de recursos](service-fabric-resource-governance.md). Para obter informações sobre como configurar métricas personalizadas e seus usos, consulte [deste artigo](service-fabric-cluster-resource-manager-metrics.md).

As métricas são diferentes das restrições de posicionamento e propriedades de nó. Propriedades do nó são descritores estáticos dos nós propriamente ditos. As métricas descrevem os recursos que os nós têm e que os serviços consomem quando são executados em um nó. Uma propriedade de nó pode ser **HasSSD** e pode ser definido como true ou false. A quantidade de espaço disponível no SSD e a quantidade consumida pelos serviços seria uma métrica como "DriveSpaceInMb". 

Assim como para restrições de posicionamento e propriedades de nó, Gerenciador de recursos de Cluster do Service Fabric não entende que os nomes da média de métricas. Os nomes de métrica são apenas cadeias de caracteres. É uma boa prática declarar as unidades como parte dos nomes de métrica que você cria quando eles podem ser ambíguos.

## <a name="capacity"></a>Capacity
Se você desativasse todo *balanceamento*, Gerenciador de recursos de Cluster do Service Fabric ainda garantiria que nenhum nó ficar acima de sua capacidade. É possível gerenciar saturações de capacidade, a menos que o cluster esteja muito cheio ou que a carga de trabalho seja maior do que qualquer nó. Capacidade é outra *restrição* que o Cluster Resource Manager usa para entender como o quanto de um recurso de um nó. A capacidade restante também é rastreada para o cluster como um todo. 

A capacidade e o consumo no nível de serviço são expressos em termos de métricas. Por exemplo, a métrica poderia ser "ClientConnections" e um nó pode ter uma capacidade de "ClientConnections" de 32.768. Outros nós podem ter outros limites. Um serviço em execução nesse nó pode dizer que está consumindo atualmente 32,256 da métrica "ClientConnections".

Durante o tempo de execução, o Gerenciador de recursos de Cluster acompanha a capacidade restante no cluster e em nós. Para controlar a capacidade, o Gerenciador de recursos de Cluster subtrai o uso de cada serviço da capacidade do nó em que o serviço é executado. Com essas informações, Cluster Resource Manager pode descobrir onde inserir ou mover réplicas para que nós não ultrapassem a capacidade.

<center>

![Capacidade e nós de cluster][Image7]
</center>

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

Você pode ver as capacidades definidas no manifesto do cluster. Aqui está um exemplo de Clustermanifest:

```xml
    <NodeType Name="NodeType03">
      <Capacities>
        <Capacity Name="ClientConnections" Value="65536"/>
      </Capacities>
    </NodeType>
```

Aqui está um exemplo de como as capacidades definidas por meio de clusterconfig. JSON para implantações autônomas ou Template. JSON para clusters hospedados no Azure: 

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

Carga de um serviço geralmente alterações dinamicamente. Digamos que o carregamento de uma réplica de "ClientConnections" alterado de 1.024 para 2.048. O nó que ele estava em execução no, em seguida, tinha a capacidade de somente 512 restante para essa métrica. Agora que a réplica ou posicionamento da instância é inválido, porque não há espaço suficiente no nó. Gerenciador de recursos de cluster tem que obter o nó abaixo da capacidade novamente. Ele reduz a carga no nó que está acima da capacidade movendo uma ou mais réplicas ou instâncias daquele nó para outros nós. 

Gerenciador de recursos de cluster tenta minimizar o custo de movimentação de réplicas. Você pode saber mais sobre [custo de movimento](service-fabric-cluster-resource-manager-movement-cost.md) sobre [regras e estratégias de rebalanceamento](service-fabric-cluster-resource-manager-metrics.md).

## <a name="cluster-capacity"></a>Capacidade do cluster
Como o Gerenciador de recursos do Cluster do Service Fabric manter o cluster geral de ficar muito cheio? Com a carga dinâmica, não há muita coisa que ele possa fazer. Serviços podem ter seu pico de carga independentemente das ações que usa o Gerenciador de recursos de Cluster. Como resultado, o cluster com bastante reserva dinâmica hoje pode ser inferior se não houver um pico no futuro. 

No Gerenciador de recursos de Cluster de controles ajuda a evitar problemas. A primeira coisa que você pode fazer é evitar a criação de novas cargas de trabalho que fariam com que o cluster fique cheio.

Digamos que você criar um serviço sem monitoração de estado e haja alguma carga associada a ele. O serviço se preocupa com a métrica "DiskSpaceInMb". O serviço consumirá cinco unidades de "DiskSpaceInMb" para cada instância do serviço. Você deseja criar três instâncias do serviço. Isso significa que é preciso que 15 unidades de "DiskSpaceInMb" estejam presentes no cluster para você mesmo criar essas instâncias de serviço.

Gerenciador de recursos de cluster calcula continuamente a capacidade e o consumo de cada métrica para que possa determinar a capacidade restante no cluster. Se não houver espaço suficiente, o Cluster Resource Manager rejeita a chamada para criar um serviço.

Como o requisito é apenas que 15 unidades estará disponíveis, você pode alocar esse espaço de muitas maneiras diferentes. Por exemplo, pode haver uma unidade restante de capacidade em 15 nós diferentes ou três unidades restantes da capacidade em cinco nós diferentes. Se o Cluster Resource Manager pode reorganizar as coisas, portanto, há cinco unidades disponíveis em três nós, ele coloca o serviço. Normalmente, é possível reorganizar o cluster, a menos que ele esteja quase cheio ou que os serviços existentes não possam ser consolidados por alguma razão.

## <a name="buffered-capacity"></a>Capacidade em buffer
A capacidade em buffer é outro recurso do Gerenciador de recursos de Cluster. Ela permite reservar uma parte da capacidade total do nó. Esse buffer de capacidade é usado apenas para posicionar serviços durante atualizações e falhas de nó. 

A capacidade em buffer é especificada globalmente por métrica para todos os nós. O valor que você escolher para a capacidade reservada é uma função do número de domínios de falha e atualização que você tem no cluster. Mais domínios de falha e atualização significam que você pode escolher um número menor para a capacidade de buffer. Se você tiver mais domínios, poderá esperar que quantidades menores de cluster fiquem indisponíveis durante atualizações e falhas. Especificação da capacidade em buffer só faz sentido se você também tiver especificado a capacidade do nó de uma métrica.

Aqui está um exemplo de como especificar a capacidade em buffer no Clustermanifest:

```xml
        <Section Name="NodeBufferPercentage">
            <Parameter Name="SomeMetric" Value="0.15" />
            <Parameter Name="SomeOtherMetric" Value="0.20" />
        </Section>
```

Aqui está um exemplo de como especificar a capacidade em buffer por meio de clusterconfig. JSON para implantações autônomas ou Template. JSON para clusters hospedados no Azure:

```json
"fabricSettings": [
  {
    "name": "NodeBufferPercentage",
    "parameters": [
      {
          "name": "SomeMetric",
          "value": "0.15"
      },
      {
          "name": "SomeOtherMetric",
          "value": "0.20"
      }
    ]
  }
]
```

A criação de novos serviços falha quando o cluster está sem capacidade em buffer para uma métrica. Impedir a criação de novos serviços para preservar o buffer garante que atualizações e falhas não façam com que os nós ultrapassem a capacidade. A capacidade em buffer é opcional, mas é recomendável em qualquer cluster que define uma capacidade de uma métrica.

Gerenciador de recursos de cluster expõe essas informações de carga. Para cada métrica, as informações incluem: 
- As configurações de capacidade em buffer.
- A capacidade total.
- O consumo atual.
- Se cada métrica é considerada balanceada ou não.
- Estatísticas sobre o desvio padrão.
- Os nós que têm mais e menos carga.  
  
O código a seguir mostra um exemplo dessa saída:

```PowerShell
PS C:\Users\user> Get-ServiceFabricClusterLoadInformation
LastBalancingStartTimeUtc : 9/1/2016 12:54:59 AM
LastBalancingEndTimeUtc   : 9/1/2016 12:54:59 AM
LoadMetricInformation     :
                            LoadMetricName        : Metric1
                            IsBalancedBefore      : False
                            IsBalancedAfter       : False
                            DeviationBefore       : 0.192450089729875
                            DeviationAfter        : 0.192450089729875
                            BalancingThreshold    : 1
                            Action                : NoActionNeeded
                            ActivityThreshold     : 0
                            ClusterCapacity       : 189
                            ClusterLoad           : 45
                            ClusterRemainingCapacity : 144
                            NodeBufferPercentage  : 10
                            ClusterBufferedCapacity : 170
                            ClusterRemainingBufferedCapacity : 125
                            ClusterCapacityViolation : False
                            MinNodeLoadValue      : 0
                            MinNodeLoadNodeId     : 3ea71e8e01f4b0999b121abcbf27d74d
                            MaxNodeLoadValue      : 15
                            MaxNodeLoadNodeId     : 2cc648b6770be1bc9824fa995d5b68b1
```

## <a name="next-steps"></a>Próximas etapas
* Para obter informações sobre a arquitetura e fluxo de informações dentro do Gerenciador de recursos de Cluster, consulte [visão geral da arquitetura do Gerenciador de recursos de Cluster](service-fabric-cluster-resource-manager-architecture.md).
* Definir as métricas de desfragmentação é uma forma de consolidar a carga em nós em vez de distribuí-la. Para saber como configurar a desfragmentação, consulte [desfragmentação de métricas e carga no Service Fabric](service-fabric-cluster-resource-manager-defragmentation-metrics.md).
* Começar do zero e [Obtenha uma introdução ao Service Fabric Cluster Resource Manager](service-fabric-cluster-resource-manager-introduction.md).
* Para saber como o Gerenciador de recursos de Cluster gerencia e balanceia carga no cluster, consulte [balanceamento do cluster do Service Fabric](service-fabric-cluster-resource-manager-balancing.md).

[Image1]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-domains.png
[Image2]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-uneven-fault-domain-layout.png
[Image3]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domains-with-placement.png
[Image4]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domain-layout-strategies.png
[Image5]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-layout-different-workloads.png
[Image6]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-placement-constraints-node-properties.png
[Image7]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-nodes-and-capacity.png
