---
title: Descrição de cluster do Gerenciador de Recursos de Cluster | Microsoft Docs
description: Descrever um cluster do Service Fabric especificando domínios de falha, domínios de atualização, propriedades de nó e capacidades de nó para o Gerenciador de Recursos de Cluster.
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
ms.openlocfilehash: ff291bda87ca4b2b4055e36989b035cf410b3b0f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60744210"
---
# <a name="describing-a-service-fabric-cluster"></a>Descrevendo um cluster do Service Fabric
O Gerenciador de Recursos de Cluster do Service Fabric fornece vários mecanismos para descrever um cluster. Durante o tempo de execução, o Cluster Resource Manager usa essas informações para garantir a alta disponibilidade dos serviços executados no cluster. Ao aplicar essas regras importantes, ele também tenta otimizar o consumo de recursos dentro do cluster.

## <a name="key-concepts"></a>Principais conceitos
Os recursos do Gerenciador de Recursos de Cluster oferecem suporte a vários recursos que descrevem um cluster:

* Domínios de falha
* Domínios de atualização
* Propriedades de nó
* Capacidades de nó

## <a name="fault-domains"></a>Domínios de falha
Um domínio de falha é qualquer área da falha coordenada. Um único computador é um domínio de falha (já que ele pode falhar sozinho por vários motivos diferentes, desde falhas de fornecimento de energia até falhas de unidade devido a firmware NIC inválido). Computadores conectados ao mesmo comutador Ethernet estão no mesmo domínio de falha, assim como computadores que compartilham uma única fonte de alimentação ou em um único local. Uma vez que é natural que essas falhas de hardware se sobreponham, os domínios de falha são hierárquicos por natureza e são representados como URIs no Service Fabric.

É importante que os domínios de falha sejam definidos corretamente uma vez que o Service Fabric usa essas informações para posicionar os serviços com segurança. O Service Fabric não quer posicionar os serviços de modo que a perda de um domínio de falha (causada pela falha de algum componente) faça com que os serviços fiquem inativos. No ambiente do Azure, o Service Fabric usa as informações do domínio de falha fornecidas pelo ambiente para configurar corretamente os nós no cluster em seu nome. Para o Service Fabric Autônomo, os Domínios de Falha são definidos no momento em que o cluster é configurado 

> [!WARNING]
> É importante que as informações de domínio de falha fornecidas ao Service Fabric sejam precisas. Por exemplo, digamos que nós do cluster do Service Fabric estejam em execução dentro de dez máquinas virtuais que são executadas em cinco hosts físicos. Nesse caso, mesmo que haja dez máquinas virtuais, há apenas cinco domínios de falha (de nível superior) diferentes. Compartilhar o mesmo host físico faz com que as VMs compartilhem o mesmo domínio de falha raiz, uma vez que as VMs apresentarão falhas coordenadas se seu host físico falhar.  
>
> O Service Fabric espera que o domínio de falha de um nó não seja alterado. Outros mecanismos para garantir a alta disponibilidade das VMs, como [HA-VMs](https://technet.microsoft.com/library/cc967323.aspx), podem causar conflitos com o Service Fabric, pois usam a migração transparente de VMs de um host para outro. Esses mecanismos não reconfiguram ou notificam o código em execução dentro da VM. Sendo assim, eles **não têm suporte** como ambientes para executar clusters do Service Fabric. O Service Fabric deve ser a única tecnologia de alta disponibilidade empregada. Mecanismos como migração dinâmica de VMs e SANs, entre outros, não são necessários. Se usados em conjunto com o Service Fabric, esses mecanismos _reduzem_ a confiabilidade e a disponibilidade dos aplicativos, pois eles introduzem complexidade adicional, adicionam fontes de falha centralizadas e utilizam estratégias de disponibilidade e confiabilidade que entram em conflito com aquelas em vigor no Service Fabric. 
>
>

Na imagem abaixo, colorimos todas as entidades que contribuem para os domínios de falha e listamos todos os diferentes domínios de falha resultantes. Neste exemplo, temos datacenters (“DC”), racks (“R”) e folhas (“B”). Em termos conceituais, se cada folha contém mais de uma máquina virtual, pode haver outra camada na hierarquia de domínios de falha.

<center>

![Nós organizados por meio de domínios de falha][Image1]
</center>

Durante o tempo de execução, o Gerenciador de Recursos de Cluster do Service Fabric leva em consideração os domínios de falha no cluster e planeja layouts. As réplicas com estado ou instâncias sem estado de um determinado serviço são distribuídas para que fiquem em domínios de falha separados. Distribuir o serviço entre domínios de falha garante que a disponibilidade do serviço não seja comprometida quando um domínio de falha falhar em qualquer nível da hierarquia.

O Cluster Resource Manager do Service Fabric não se importa com quantas camadas existem na hierarquia de domínio de falha. No entanto, ele tenta assegurar que a perda de qualquer parte da hierarquia não afete os serviços em execução nela. 

É melhor se houver o mesmo número de nós em cada nível de profundidade na hierarquia de domínio de falha. Se a "árvore" de domínios de falha estiver desequilibrada em seu cluster, será mais difícil para o Gerenciador de Recursos de Cluster calcular a melhor alocação dos serviços. Layouts de domínios de falha desequilibrados significam que a perda de alguns domínios afeta a disponibilidade de serviços mais do que outros domínios. Como resultado, o Gerenciador de Recursos de Cluster fica dividido entre as duas metas: Ele quer usar os computadores nesse domínio “pesado” colocando serviços neles e quer posicionar serviços em outros domínios para que a perda de um domínio não cause problemas. 

Qual é a aparência de um domínio desequilibrado? No diagrama abaixo, mostramos dois layouts de cluster diferentes. No primeiro exemplo, os nós estão distribuídos uniformemente entre os domínios de falha. No segundo exemplo, um domínio de falha tem muito mais nós do que o outro. 

<center>

![Dois layouts de cluster diferentes][Image2]
</center>

No Azure, a escolha de qual domínio de falha contém um nó é gerenciada por você. No entanto, dependendo do número de nós provisionados, você ainda pode ficar com domínios de falha com mais nós do que outros. Por exemplo, digamos que você tenha cinco domínios de falha no cluster, mas provisione sete nós para um determinado NodeType. Nesse caso, os dois primeiros domínios de falha ficam com mais nós. Se você continuar a implantar mais NodeTypes com apenas algumas instâncias, o problema piorará. Por esse motivo, recomenda-se que o número de nós em cada tipo de nó seja um múltiplo do número de domínios de falha.

## <a name="upgrade-domains"></a>Domínios de atualização
Domínios de atualização são outro recurso que ajuda o Gerenciador de Recursos de Cluster do Service Fabric a entender o layout do cluster. Os domínios de atualização definem conjuntos de nós que são atualizados ao mesmo tempo. Domínios de atualização ajudam o Gerenciador de Recursos de Cluster a entender e coordenar operações de gerenciamento, como atualizações.

Os Domínios de Atualização são muito semelhantes aos Domínios de Falha, mas com algumas diferenças importantes. Primeiro, as áreas de falhas de hardware coordenadas definem os domínios de falha. Os domínios de atualização, por outro lado, são definidos pela política. Você pode decidir quantos domínios deseja, em vez disso ser determinado pelo ambiente. Você pode ter tantos domínios de atualização quantos forem o número de nós. Outra diferença entre domínios de falha e domínios de atualização é que os domínios de atualização não são hierárquicos. Em vez disso, eles são mais parecidos com uma marca simples. 

O diagrama a seguir mostra que três domínios de atualização são distribuídos em três domínios de falha. Ele também mostra um possível posicionamento para três réplicas diferentes de um serviço com estado, em que cada um fica em domínios de atualização e de falha diferentes. Esse posicionamento permite perder um domínio de falha durante uma atualização de serviço e ainda ter uma cópia dos códigos e dos dados.  

<center>

![Posicionamento com domínios de falha e atualização][Image3]
</center>

Há prós e contras em relação a ter um grande número de domínios de atualização. Ter mais domínios de atualização significa que cada etapa da atualização é mais granular e, assim, afeta um número menor de nós ou serviços. Como resultado, menos serviços precisam ser movidos de cada vez, introduzindo menos variação no sistema. Isso tende a aumentar a confiabilidade, uma vez que uma parte menor do serviço é afetada por qualquer problema introduzido durante a atualização. Ter mais domínios de atualização também significa que você precisa de menos buffer disponível em outros nós para lidar com o impacto da atualização. Por exemplo, se você tiver cinco domínios de atualização, os nós em cada um estarão lidando com aproximadamente 20% do tráfego. Se você precisar desativar esse domínio de atualização para uma atualização, essa carga normalmente precisará ir para algum lugar. Como você tem quatro domínios de atualização restantes, cada um deles deve ter espaço para cerca de 5% do tráfego total. Ter mais domínios de atualização significa que você precisa de menos buffer nos nós do cluster. Por exemplo, imagine que você tivesse 10 domínios de atualização. Nesse caso, cada um deles lidaria com apenas cerca de 10% do tráfego total. Quando uma atualização percorresse o cluster, cada domínio só precisaria ter espaço para aproximadamente 1,1% do tráfego total. Ter mais domínios de atualização geralmente permite que você execute seus nós com um nível maior de utilização, uma vez que você precisa ter uma capacidade menor reservada. O mesmo vale para domínios de falha.  

A desvantagem de ter vários domínios de atualização é que as atualizações tendem a levar mais tempo. O Service Fabric espera por um curto período após um domínio de atualização ser concluído e executa verificações antes de começar a atualizar o domínio seguinte. Esses atrasos permitem detectar problemas introduzidos pela atualização antes que ela continue. Essa compensação é aceitável, pois isso evita que alterações incorretas afetem grande parte do serviço de uma só vez.

Ter um número muito reduzido de domínios de atualização tem muitos efeitos negativos – enquanto cada domínio de atualização individual fica inoperante e é atualizado, uma grande parte de sua capacidade geral fica indisponível. Por exemplo, se tiver apenas três domínios de atualização, você desativará cerca de um terço de sua capacidade geral de serviço ou de cluster de uma vez. Ter uma parte tão grande do seu serviço inoperante ao mesmo tempo não é desejável, uma vez que é necessário ter capacidade suficiente no restante do cluster para lidar com a carga de trabalho. Manter esse buffer significa que, durante a operação normal, esses nós ficam menos carregados do que ficariam em caso contrário. Isso aumenta o custo de execução de seu serviço.

Não há um limite real para o número total de domínios de falha ou de atualização em um ambiente nem restrições sobre como eles se sobrepõem. Dito isso, há vários padrões comuns:

- Domínios de falha e domínios de atualização mapeados 1:1
- Um domínio de atualização por nó (instância de sistema operacional física ou virtual)
- Um modelo "distribuído" ou de "matriz" em que os domínios de falha e os domínios de atualização formam uma matriz em que os computadores geralmente são executados diagonalmente

<center>

![Layouts de falha e domínio de atualização][Image4]
</center>

Não existe uma solução ideal sobre qual layout deve ser escolhido. Cada um deles tem prós e contras. Por exemplo, o modelo de 1FD:1UD é simples de configurar. O modelo de um domínio de atualização por nó é mais parecido com o modelo a que a maioria das pessoas estão acostumadas. Durante atualizações, cada nó é atualizado de forma independente. Isso é semelhante à forma como pequenos conjuntos de computadores eram atualizados manualmente no passado. 

O modelo mais comum é a matriz de FD/UD, em que os FDs e os UDs formam uma tabela e os nós são posicionados começando ao longo da diagonal. Esse é o modelo usado por padrão nos clusters do Service Fabric no Azure. Para clusters com muitos nós, tudo acaba ficando parecido com o padrão de matriz denso acima.

## <a name="fault-and-upgrade-domain-constraints-and-resulting-behavior"></a>Restrições de domínio de falha e de atualização e o comportamento resultante
### <a name="default-approach"></a>*Abordagem padrão*
Por padrão, o Gerenciador de Recursos de Cluster mantém os serviços balanceados entre os Domínios de Falha e de Atualização. Isso é modelado como uma [restrição](service-fabric-cluster-resource-manager-management-integration.md). Os estados de restrição do domínio de falha e de atualização: “Para uma partição de serviço específica, nunca deve haver uma diferença maior que um no número de objetos de serviço (instâncias de serviço sem estado ou réplicas de serviço com estado) entre dois domínios no mesmo nível de hierarquia”. Digamos que essa restrição forneça uma garantia de “diferença máxima”. A restrição de Domínio de Falha e de Atualização impede determinadas movimentações ou disposições que violam a regra mencionada acima. 

Vejamos um exemplo. Digamos que temos um cluster com seis nós, configurados com cinco domínios de falha e cinco domínios de atualização.

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | | |N3 | | |
| **UD3** | | | |N4 | |
| **UD4** | | | | |N5 |

*Configuração 1*

Agora, digamos que nós criemos um serviço com um TargetReplicaSetSize (ou, para um serviço sem estado, um InstanceCount) igual a cinco. As réplicas recaem sobre N1 a N5. Na verdade, N6 nunca será usado, independentemente de quantos serviços como este você criar. Mas por quê? Vamos observar a diferença entre o layout atual e o que aconteceria se N6 fosse escolhido.

Aqui está o nosso layout e o número total de réplicas por domínio de falha e de atualização:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** | |R2 | | | |1 |
| **UD2** | | |R3 | | |1 |
| **UD3** | | | |R4 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

*Layout 1*


Este layout é balanceado em termos de nós por domínio de falha e domínio de atualização. Ele também é balanceado em termos de número de réplicas por domínio de falha e de atualização. Cada domínio possui o mesmo número de nós e o mesmo número de réplicas.

Agora, vamos ver o que aconteceria se tivéssemos usado N6 em vez de N2. Como as réplicas seriam distribuídas?

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R5 | | | | |1 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |2 |0 |1 |1 |1 |- |

*Layout 2*


Esse layout viola nossa definição de garantia de “diferença máxima” para a restrição de Domínio de Falha. FD0 tem duas réplicas, enquanto FD1 tem zero, tornando a diferença entre FD0 e FD1 um total de dois, o que é maior que a diferença máxima de um. Como a restrição foi violada, o Gerenciador de Recursos de Cluster não permite essa disposição. Da mesma forma, se escolhêssemos N2 e N6 (em vez de N1 e N2), teríamos:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** | | | | | |0 |
| **UD1** |R5 |R1 | | | |2 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

*Layout 3*


Este layout é equilibrado em termos de domínios de falha. No entanto, agora ela está violando a restrição de Domínio de Atualização porque UD0 tem zero réplicas enquanto UD1 tem duas. Sendo assim, este layout também é inválido e não será escolhido pelo Gerenciador de Recursos de Cluster.

Essa abordagem de distribuição de réplicas com estado ou de instâncias sem estado fornece a melhor tolerância a falhas possível. Em uma situação em que um domínio fica inoperante, o número mínimo de réplicas/instâncias é perdido. 

Por outro lado, essa abordagem pode ser muito restrita e não permitir que o cluster utilize todos os recursos. Para determinadas configurações de cluster, alguns nós não podem ser usados. Isso pode fazer com que o Service Fabric não disponha seus serviços, resultando em mensagens de aviso. No exemplo anterior, alguns nós de cluster não podem ser usados (N6 no exemplo fornecido). Mesmo se você adicionar nós ao cluster (N7 – N10), as réplicas/instâncias só deverão ser colocadas em N1 – N5 devido a restrições do Domínio de Falha e de Atualização. 

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | |N10 |
| **UD1** |N6 |N2 | | | |
| **UD2** | |N7 |N3 | | |
| **UD3** | | |N8 |N4 | |
| **UD4** | | | |N9 |N5 |

*Configuração 2*


### <a name="alternative-approach"></a>*Abordagem alternativa*

O Gerenciador de Recursos de Cluster é compatível com a outra versão da restrição de Domínio de Falha e de Atualização que permite o posicionamento e também garante um nível mínimo de segurança. A restrição alternativa de domínio de falha e de atualização pode ser especificada da seguinte maneira: “Para determinada partição de serviço, a distribuição de réplica entre domínios deve garantir que a partição não sofra uma perda de quorum”. Digamos que essa restrição forneça uma garantia de “segurança de quorum”. 

> [!NOTE]
>Para um serviço com estado, definimos *perda de quorum* em uma situação em que a maioria das réplicas de partição estão inativas ao mesmo tempo. Por exemplo, se TargetReplicaSetSize for cinco, um conjunto de quaisquer três réplicas representará um quorum. Da mesma forma, se TargetReplicaSetSize for 6, quatro réplicas serão necessárias para o quorum. Em ambos os casos, no máximo duas réplicas podem ficar inativas ao mesmo tempo se a partição deve continuar funcionando normalmente. Para um serviço sem estado, não existe *perda de quorum*, pois os serviços sem estado continuarão a funcionar normalmente mesmo se a maioria das instâncias ficarem inativas ao mesmo tempo. Portanto, enfocaremos os serviços com estado no restante do texto.
>

Voltar para o exemplo anterior. Com a versão “segurança de quórum” da restrição, todos os três layouts em questão seriam válidos. Isso ocorre porque, mesmo em caso de falha de FD0 no segundo layout ou UD1 no terceiro layout, a partição ainda teria quorum (a maioria dessas réplicas ainda estariam operantes). Com esta versão da restrição, N6 poderia ser utilizado quase sempre.

A abordagem de “segurança de quorum” oferece mais flexibilidade do que a abordagem de “diferença máxima” porque é mais fácil encontrar as distribuições de réplica que são válidas em praticamente qualquer topologia de cluster. No entanto, essa abordagem não garante a melhor característica de tolerância a falhas, pois algumas falhas são piores que outras. Na pior das hipóteses, a maioria das réplicas seria perdida com a falha de um domínio e uma réplica adicional. Por exemplo, em vez da necessidade de três falhas para perder quorum com cinco réplicas ou as instâncias, agora seria possível perder a maioria com apenas duas falhas. 

### <a name="adaptive-approach"></a>*Abordagem adaptável*
Como ambas as abordagens têm vantagens e desvantagens, apresentamos uma abordagem adaptável que combina essas duas estratégias.

> [!NOTE]
> Esse será o comportamento padrão a começar do Service Fabric versão 6.2. 
> 
> A abordagem adaptável usa a lógica “diferença máxima” por padrão e muda para a lógica “segurança de quorum” somente quando necessário. O Gerenciador de Recursos de Cluster decide automaticamente qual estratégia é necessária examinando como o cluster e os serviços são configurados. Para um determinado serviço: *Se o TargetReplicaSetSize for uniformemente divisível pelo número de Domínios de Falha e o número de Domínios de Atualização **e** o número de nós for menor ou igual ao (número de Domínios de Falha) * (o número de Domínios de Atualização), o Gerenciador de Recursos de Cluster deverá usar a lógica “baseado em quorum” para o serviço.* Tenha em mente que o Gerenciador de Recursos de Cluster usará essa abordagem para serviços com e sem estado, apesar da perda de quorum não ser relevante para os serviços sem estado.

Voltemos para o exemplo anterior e consideremos que um cluster agora tem oito nós (o cluster ainda está configurado com cinco Domínios de Falha, cinco Domínios de Atualização e TargetReplicaSetSize de um serviço hospedado no cluster que permanece cinco). 

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | |N7 |N3 | | |
| **UD3** | | |N8 |N4 | |
| **UD4** | | | | |N5 |

*Configuração 3*

Como todas as condições necessárias são atendidas, o Gerenciador de Recursos de Cluster utilizará a lógica “baseada em quorum” para distribuir o serviço. Isso permite usar N6 a N8. Uma distribuição de serviço possível nesse caso seria semelhante à seguinte:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R2 | | | | |1 |
| **UD2** | |R3 |R4 | | |2 |
| **UD3** | | | | | |0 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |2 |1 |1 |0 |1 |- |

*Layout 4*

Se o TargetReplicaSetSize do serviço for reduzido para quatro (por exemplo), o Gerenciador de Recursos de Cluster notará essa alteração e continuará a usar a lógica “diferença máxima” porque TargetReplicaSetSize não é mais divisível pelo número de FDs e UDs. Como resultado, certos movimentos de réplica ocorrerá para distribuir as quatro réplicas restantes nos nós N1 a N5 para que a versão “diferença máxima” da lógica de domínio do Domínio de Falha e de Atualização não seja violada. 

Observe novamente o quarto layout e o TargetReplicaSetSize de cinco. Se N1 for removido do cluster, o número de Domínios de Atualização será igual a quatro. Novamente, o Gerenciador de Recursos de Cluster começa a usar uma lógica “diferença máxima”, visto que o número de UDs não divide mais o TargetReplicaSetSize do serviço de maneira uniforme. Como resultado, a réplica R1, quando compilada novamente, precisa parar em N4 para que a Restrição de Domínio de Falha e de Atualização não seja violada.

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |N/D |N/D |N/D |N/D |N/D |N/D |
| **UD1** |R2 | | | | |1 |
| **UD2** | |R3 |R4 | | |2 |
| **UD3** | | | |R1 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

*Layout 5*

## <a name="configuring-fault-and-upgrade-domains"></a>Configurando domínios de falha e atualização
A definição de domínios de falha e domínios de atualização é feita automaticamente em implantações hospedadas do Azure Service Fabric. O Service Fabric seleciona e usa as informações de ambiente do Azure.

Se estiver criando seu próprio cluster (ou desejar executar uma topologia específica no desenvolvimento), você mesmo poderá fornecer as informações de domínio de falha e de domínio de atualização. Neste exemplo, definimos um cluster de desenvolvimento local de nove nós que abrange três "datacenters" (cada um com três racks). Este cluster também tem três domínios de atualização distribuídos entre esses três datacenters. Veja um exemplo da configuração abaixo: 

ClusterManifest.xml

```xml
  <Infrastructure>
    <!-- IsScaleMin indicates that this cluster runs on one-box /one single server -->
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

por meio de ClusterConfig.json para implantações autônomas

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
> Ao definir clusters por meio do Azure Resource Manager, domínios de falha e domínios de atualização são atribuídos pelo Azure. Portanto, a definição dos tipos de nó e conjuntos de dimensionamento de máquinas virtuais em seu modelo do Azure Resource Manager não inclui informações de domínio de atualização ou domínio de falha.
>

## <a name="node-properties-and-placement-constraints"></a>Restrições de posicionamento e propriedades do nó
Às vezes (na verdade, na maioria das vezes), convém assegurar que determinadas cargas de trabalho sejam executadas apenas em alguns tipos de nós no cluster. Por exemplo, algumas cargas de trabalho podem exigir GPUs ou SSDs, enquanto outras, não. Um ótimo exemplo de direcionamento de hardware para cargas de trabalho específicas é praticamente toda arquitetura de n camadas por aí. Determinados computadores servem como o lado de serviço de front-end ou API do aplicativo e, portanto, são expostos aos clientes ou à Internet. Diferentes computadores, normalmente com recursos de hardware diferentes, lidam com o trabalho das camadas de computação ou armazenamento. Normalmente, eles _não_ são expostos diretamente a clientes ou à Internet. O Service Fabric espera que haja casos em que cargas de trabalho específicas precisem ser executadas em configurações de hardware específicas. Por exemplo: 

* um aplicativo de n camadas existente foi "transferido e posicionado" em um ambiente do Service Fabric
* uma carga de trabalho deseja ser executada em um hardware específico por motivos de desempenho, escala ou isolamento de segurança
* Uma carga de trabalho deve ser isolada de outras cargas de trabalho por motivos de política ou consumo de recursos

Para dar suporte a esses tipos de configurações, o Service Fabric tem uma noção avançada das marcas que podem ser aplicadas aos nós. Essas marcas são chamadas de **propriedades de nó**. **Restrições de posicionamento** são as instruções anexadas a serviços individuais que selecionam uma ou mais propriedades de nó. Restrições de posicionamento definem onde os serviços devem ser executados. O conjunto de restrições é extensível, qualquer par chave-valor pode funcionar. 

<center>

![Cargas de trabalho diferentes do Layout do cluster][Image5]
</center>

### <a name="built-in-node-properties"></a>Propriedades de nó internas
O Service Fabric define algumas propriedades de nó padrão que podem ser usadas automaticamente sem que o usuário precise defini-las. As propriedades padrão definidas em cada nó são **NodeType** e **NodeName**. Por exemplo, você poderia escrever uma restrição de posicionamento como `"(NodeType == NodeType03)"`. Em geral, descobrimos que NodeType é uma das propriedades mais comumente usadas. Ela é útil porque corresponde individualmente a um tipo de um computador. Cada tipo de computador corresponde a um tipo de carga de trabalho em um aplicativo de n camadas tradicional.

<center>

![Restrições de posicionamento e propriedades de nó][Image6]
</center>

## <a name="placement-constraint-and-node-property-syntax"></a>Sintaxe de restrição de posicionamento e propriedades do nó 
O valor especificado na propriedade do nó pode ser uma cadeia de caracteres, bool ou signed long. A instrução no serviço é chamada de uma *restrição* de posicionamento, uma vez que ela restringe onde o serviço pode ser executado no cluster. A restrição pode ser qualquer instrução booliana que opera sobre as diferentes propriedades de nó no cluster. Os seletores válidos nessas instruções boolianas são:

1) verificações condicionais para a criação de instruções

| Instrução | Sintaxe |
| --- |:---:|
| "igual a" | "==" |
| "diferente de" | "!=" |
| "maior que" | ">" |
| "maior ou igual a" | ">=" |
| "menor que" | "<" |
| "menor ou igual a" | "<=" |

2) instruções boolianas para operações de agrupamento e lógicas

| Instrução | Sintaxe |
| --- |:---:|
| "e" | "&&" |
| "ou" | "&#124;&#124;" |
| "não" | "!" |
| "agrupar como instrução única" | "()" |

Aqui estão alguns exemplos de instruções de restrição básicas.

  * `"Value >= 5"`
  * `"NodeColor != green"`
  * `"((OneProperty < 100) || ((AnotherProperty == false) && (OneProperty >= 100)))"`

O serviço pode ser posicionado somente em nós em que a instrução de restrição de posicionamento geral é avaliada como "True". Os nós que não têm uma propriedade definida não correspondem a nenhuma restrição de posicionamento que contém essa propriedade.

Vamos supor que as seguintes propriedades de nó foram definidas para um tipo de nó específico:

ClusterManifest.xml

```xml
    <NodeType Name="NodeType01">
      <PlacementProperties>
        <Property Name="HasSSD" Value="true"/>
        <Property Name="NodeColor" Value="green"/>
        <Property Name="SomeProperty" Value="5"/>
      </PlacementProperties>
    </NodeType>
```

via ClusterConfig.json para implantações Autônomas ou Template.json para clusters hospedados pelo Azure. 

> [!NOTE]
> Em seu modelo do Azure Resource Manager, o tipo de nó costuma ser parametrizado. Ele seria semelhante a "[parameters('vmNodeType1Name')]", em vez de "NodeType01".
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

Você pode criar *restrições* de posicionamento de serviço para um serviço da seguinte forma:

C#

```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
serviceDescription.PlacementConstraints = "(HasSSD == true && SomeProperty >= 4)";
// add other required servicedescription fields
//...
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceType -Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementConstraint "HasSSD == true && SomeProperty >= 4"
```

Se todos os nós de NodeType01 forem válidos, você também poderá selecionar esse tipo de nó com a restrição "(NodeType == NodeType01)".

Uma das vantagens das restrições de posicionamento de um serviço é que elas podem ser atualizadas dinamicamente durante o tempo de execução. Então, se necessário, você poderá mover um serviço pelo cluster, adicionar e remover requisitos, etc. O Service Fabric se encarrega de garantir que o serviço permaneça ativo e disponível mesmo quando esses tipos de alterações forem feitas.

C#:

```csharp
StatefulServiceUpdateDescription updateDescription = new StatefulServiceUpdateDescription();
updateDescription.PlacementConstraints = "NodeType == NodeType01";
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/app/service"), updateDescription);
```

Powershell:

```posh
Update-ServiceFabricService -Stateful -ServiceName $serviceName -PlacementConstraints "NodeType == NodeType01"
```

Restrições de posicionamento são especificadas para cada instância de serviço nomeada diferente. As atualizações sempre assumem o lugar (substituem) do que foi especificado anteriormente.

A definição do cluster define as propriedades em um nó. Alterar as propriedades de um nó requer uma atualização de configuração do cluster. Atualizar as propriedades de um nó requer que cada nó afetado seja reiniciado para informar suas novas propriedades. Essas atualizações sem interrupção são gerenciadas pelo Service Fabric.

## <a name="describing-and-managing-cluster-resources"></a>Descrever e gerenciar Recursos de Cluster
Um dos trabalhos mais importantes de qualquer orquestrador é ajudar a gerenciar o consumo de recursos no cluster. Gerenciar recursos de cluster pode significar algumas coisas diferentes. Primeiro, é necessário garantir que os computadores não sejam sobrecarregados. Isso significa garantir que eles não executem mais serviços do que conseguem tratar. Segundo, há o balanceamento e a otimização, que são fundamentais para executar serviços com eficiência. Ofertas de serviço com bom custo-benefício ou sensíveis ao desempenho não podem permitir que alguns nós fiquem frios enquanto outros ficam quentes. Os nós quentes levam a contenção de recursos e a um mau desempenho, e os nós frios representam recursos desperdiçados e aumento de custos. 

O Service Fabric representa recursos como `Metrics`. As métricas são qualquer recurso lógico ou físico que você queira descrever para o Service Fabric. Exemplos de métricas são itens como "WorkQueueDepth" ou "MemoryInMb". Para obter informações sobre os recursos físicos que o Service Fabric é capaz de controlar nos nós, consulte [governança de recursos](service-fabric-resource-governance.md). Para obter informações sobre como configurar métricas personalizadas e seus usos, consulte [este artigo](service-fabric-cluster-resource-manager-metrics.md)

As métricas são diferentes das restrições de posicionamento e das propriedades de nó. Propriedades do nó são descritores estáticos dos nós propriamente ditos. As métricas descrevem os recursos que os nós têm e que os serviços consomem quando são executados em um nó. Uma propriedade do nó pode ser "HasSSD" e pode ser definida como true ou false. A quantidade de espaço disponível no SSD e quanto dele é consumido pelos serviços seria uma métrica como "DriveSpaceInMb". 

É importante observar que assim como as restrições de posicionamento e as propriedades de nó, o Cluster Resource Manager do Service Fabric não entende o que os nomes das métricas significam. Os nomes de métrica são apenas cadeias de caracteres. É uma boa prática declarar as unidades como parte dos nomes de métrica que você criar quando puderem ser ambíguos.

## <a name="capacity"></a>Capacity
Se você desativasse todo o *balanceamento* de recursos, o Gerenciador de Recursos de Cluster do Service Fabric ainda garantiria que nenhum nó ficasse acima de sua capacidade. É possível gerenciar saturações de capacidade, a menos que o cluster esteja muito cheio ou que a carga de trabalho seja maior do que qualquer nó. A capacidade é outra *restrição* que usa o Cluster Resource Manager para entender o quanto um nó tem de um recurso. A capacidade restante também é rastreada para o cluster como um todo. A capacidade e o consumo no nível de serviço são expressos em termos de métricas. Então, por exemplo, a métrica poderia ser "ClientConnections" e um determinado nó poderia ter uma capacidade de "ClientConnections" igual a 32768. Outros nós podem ter outros limites; algum serviço em execução nesse nó pode informar que atualmente está consumindo 32256 da métrica "ClientConnections".

No tempo de execução, o Gerenciador de Recursos de Cluster acompanha a capacidade restante no cluster e nos nós. Para acompanhar a capacidade, o Gerenciador de Recursos de Cluster subtrai o uso de cada serviço da capacidade do nó em que o serviço é executado. Com essas informações, o Cluster Resource Manager do Service Fabric pode descobrir onde inserir ou mover réplicas para que os nós não ultrapassem a capacidade.

<center>

![Capacidade e nós de cluster][Image7]
</center>

C#:

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

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("ClientConnections,High,1024,0)
```

Você pode ver as capacidades definidas no manifesto do cluster:

ClusterManifest.xml

```xml
    <NodeType Name="NodeType03">
      <Capacities>
        <Capacity Name="ClientConnections" Value="65536"/>
      </Capacities>
    </NodeType>
```

via ClusterConfig.json para implantações Autônomas ou Template.json para clusters hospedados pelo Azure. 

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

Normalmente, a carga de um serviço é alterada dinamicamente. Digamos que a carga de uma réplica de "ClientConnections" tenha sido alterada de 1024 para 2048, mas o nó em que ela estava em execução tivesse uma apenas uma capacidade de 512 restante para essa métrica. Agora essa réplica ou posicionamento da instância é inválido, pois não há espaço suficiente no nó. O Gerenciador de Recursos de Cluster precisa entrar em ação e colocar o nó abaixo da capacidade novamente. Ele reduz a carga do nó que está acima da capacidade movendo uma ou mais réplicas ou instâncias daquele nó para outros nós. Ao mover réplicas, o Cluster Resource Manager tenta minimizar o custo dessas movimentações. O custo da movimentação é abordado [neste artigo](service-fabric-cluster-resource-manager-movement-cost.md) e mais informações sobre as regras e estratégias de rebalanceamento do Gerenciador de Recursos de Cluster são fornecidas [aqui](service-fabric-cluster-resource-manager-metrics.md).

## <a name="cluster-capacity"></a>Capacidade do cluster
Então, como o Gerenciador de Recursos de Cluster do Service Fabric impede o cluster geral de ficar muito cheio? Bem, com a carga dinâmica, não há muito que ele possa fazer. Os serviços podem ter seu pico de carga independentemente das ações executadas pelo Cluster Resource Manager. Como resultado, o cluster com bastante reserva dinâmica hoje pode não ter espaço suficiente quando você ficar famoso amanhã. Dito isso, há alguns controles embutidos para evitar problemas. A primeira coisa a fazer é evitar a criação de novas cargas de trabalho que fariam com que o cluster ficasse cheio.

Digamos que você crie um serviço sem estado e que haja alguma carga associada a ele. Digamos que o serviço se preocupa com a métrica "DiskSpaceInMb". Digamos também que ele consumirá cinco unidades de "DiskSpaceInMb" para cada instância do serviço. Você deseja criar três instâncias do serviço. Ótimo! Isso significa que é preciso que 15 unidades de "DiskSpaceInMb" estejam presentes no cluster para podermos criar essas instâncias de serviço. O Gerenciador de Recursos de Cluster calcula continuamente a capacidade e o consumo de cada métrica para que possa determinar a capacidade restante no cluster. Se não houver espaço suficiente, o Gerenciador de Recursos de Cluster rejeitará a chamada de criação de serviço.

Uma vez que o requisito é apenas que haja 15 unidades disponíveis, esse espaço poderia ser alocado de várias maneiras diferentes. Por exemplo, poderia haver uma unidade restante de capacidade em 15 nós diferentes ou três unidades restantes de capacidade em cinco nós diferente. Se o Gerenciador de Recursos de Cluster puder reorganizar as coisas para que haja cinco unidades disponíveis em três nós, ele posicionará o serviço. Normalmente, é possível reorganizar o cluster, a menos que ele esteja quase cheio ou que os serviços existentes não possam ser consolidados por alguma razão.

## <a name="buffered-capacity"></a>Capacidade de buffer
A capacidade em buffer é outro recurso do Gerenciador de Recursos de Cluster. Ela permite reservar uma parte da capacidade total do nó. Esse buffer de capacidade só é usado para posicionar serviços durante atualizações e falhas do nó. A capacidade em buffer é especificada globalmente por métrica para todos os nós. O valor que você escolher para a capacidade reservada é uma função do número de domínios de falha e de atualização no cluster. Mais domínios de falha e de atualização significa que você pode escolher um número menor para a capacidade em buffer. Se você tiver mais domínios, poderá esperar que quantidades menores de cluster fiquem indisponíveis durante atualizações e falhas. Especificar a capacidade em buffer só fará sentido se você também tiver especificado a capacidade do nó para uma métrica.

Aqui está um exemplo de como especificar a capacidade do buffer:

ClusterManifest.xml

```xml
        <Section Name="NodeBufferPercentage">
            <Parameter Name="SomeMetric" Value="0.15" />
            <Parameter Name="SomeOtherMetric" Value="0.20" />
        </Section>
```

via ClusterConfig.json para implantações Autônomas ou Template.json para clusters hospedados pelo Azure:

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

A criação de novos serviços falha quando o cluster está sem capacidade em buffer para uma métrica. Impedir a criação de novos serviços para preservar o buffer garante que atualizações e falhas não façam com que os nós ultrapassem a capacidade. A capacidade em buffer é opcional, mas é recomendada em qualquer cluster que define uma capacidade para uma métrica.

O Gerenciador de Recursos de Cluster expõe essas informações de carga. Para cada métrica, as informações incluem: 
  - as configurações de capacidade em buffer
  - a capacidade total
  - o consumo atual
  - se cada métrica é considerada balanceada ou não
  - estatísticas sobre o desvio padrão
  - os nós que têm mais e menos carga  
  
Abaixo, vemos um exemplo dessa saída:

```posh
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
* Para obter informações sobre a arquitetura e fluxo de informações dentro do Gerenciador de recursos de Cluster, fazer check-out [neste artigo](service-fabric-cluster-resource-manager-architecture.md)
* Definir as Métricas de Desfragmentação é uma forma de consolidar a carga em nós, em vez de distribuí-la. Para saber como configurar a desfragmentação, leia [este artigo](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
* Comece do princípio e [veja uma introdução ao Resource Manager de Cluster do Service Fabric](service-fabric-cluster-resource-manager-introduction.md)
* Para descobrir como o Gerenciador de Recursos de Cluster gerencia e balanceia carga no cluster, confira o artigo sobre [como balancear carga](service-fabric-cluster-resource-manager-balancing.md)

[Image1]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-domains.png
[Image2]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-uneven-fault-domain-layout.png
[Image3]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domains-with-placement.png
[Image4]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domain-layout-strategies.png
[Image5]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-layout-different-workloads.png
[Image6]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-placement-constraints-node-properties.png
[Image7]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-nodes-and-capacity.png
