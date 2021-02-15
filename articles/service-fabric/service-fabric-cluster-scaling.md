---
title: Dimensionamento do Cluster Service Fabric do Azure
description: Saiba mais sobre dimensionamento de clusters do Azure Service Fabric para reduzir ou escalar horizontalmente e reduzir ou escalar verticalmente. À medida que as demandas de aplicativo mudam, podem Service Fabric clusters.
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: atsenthi
ms.openlocfilehash: 126be55c63c625995ad52b84a51a8983e220652d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85610193"
---
# <a name="scaling-azure-service-fabric-clusters"></a>Dimensionar clusters do Azure Service Fabric
Um cluster do Service Fabric é um conjunto de computadores físicos ou virtuais conectados via rede, nos quais os microsserviços são implantados e gerenciados. Uma máquina ou VM que faz parte de um cluster é chamada de nó. Os clusters podem conter potencialmente milhares de nós. Após criar um cluster do Service Fabric, será possível dimensionar o cluster horizontalmente (alterar o número de nós) ou verticalmente (alterar os recursos dos nós).  É possível dimensionar o cluster a qualquer momento, mesmo quando as cargas de trabalho estiverem em execução no cluster.  Na medida em que o cluster for dimensionado, os aplicativos também serão dimensionados automaticamente.

Por quê dimensionar o cluster? As demandas de aplicativos alteram ao longo do tempo.  Talvez seja necessário aumentar os recursos de cluster para atender ao aumento do tráfego ou de carga de trabalho do aplicativo ou diminuir os recursos de cluster quando houver queda de demanda.

## <a name="scaling-in-and-out-or-horizontal-scaling"></a>Reduzir horizontalmente e escalar horizontalmente, ou dimensionamento horizontal
Altera o número de nós no cluster.  Quando os novos nós unem-se ao cluster, o [Gerenciador de Recursos de Cluster](service-fabric-cluster-resource-manager-introduction.md) move os serviços para eles, o que reduz a carga nos nós existentes.  Também será possível diminuir o número de nós, se os recursos de cluster não estiverem sendo utilizados eficientemente.  Conforme os nós saem do cluster, os serviços são removidos desses nós e a carga aumenta nos nós restantes.  Reduzir o número de nós em um cluster em execução no Azure ajuda a economizar dinheiro, já que você paga pelo número de VMs utilizadas e não pela carga de trabalho nessas VMs.  

- Vantagens: escala infinita, em teoria.  Se o aplicativo for projetado para escalabilidade, será possível habilitar o crescimento ilimitado adicionando mais nós.  As ferramentas em ambientes de nuvem facilitam a adição ou a remoção de nós, por isso é fácil ajustar a capacidade e você paga apenas pelos recursos que usa.  
- Desvantagens: os aplicativos devem ser [projetados para escalabilidade](service-fabric-concepts-scalability.md).  Bancos de dados de aplicativos e persistência podem exigir trabalho de arquitetura adicional também para dimensionar.  As [Coleções confiáveis](service-fabric-reliable-services-reliable-collections.md) nos serviços com estado do Service Fabric, no entanto, facilitam muito dimensionar os dados de aplicativos.

Os conjuntos de escala de Máquina Virtual são um recurso de Computação do Azure que você pode usar para implantar e gerenciar uma coleção de máquinas virtuais como um conjunto. Cada tipo de nó definido em um cluster do Azure é [configurado como um conjunto de dimensionamento separado](service-fabric-cluster-nodetypes.md). Cada tipo de nó pode ser escalado ou reduzido horizontalmente de forma independente, ter conjuntos diferentes de portas abertas e métricas de capacidade diferentes. 

Ao dimensionar um cluster do Azure, lembre-se das diretrizes a seguir:
- os tipos de nós primários que executam cargas de trabalho de produção sempre devem ter cinco ou mais nós.
- os tipos de nó não primário que executam cargas de trabalho de produção com estado sempre devem ter cinco ou mais nós.
- os tipos de nó não primário que executam cargas de trabalho de produção sem estado sempre devem ter dois ou mais nós.
- Qualquer tipo de nó de [nível de durabilidade](service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster) Ouro ou Prata sempre deve ter cinco ou mais nós.
- Não remova instâncias/nós de VM aleatórios de um tipo de nó, sempre use a escala do conjunto de dimensionamento de máquinas virtuais no recurso. A exclusão de instâncias de VM aleatórias pode afetar negativamente a capacidade do sistema para balancear carga adequadamente.
- Se estiver usando regras de autoescala, defina as regras para que a redução horizontal (remoção de instâncias de VM) seja feito um nó por vez. Redução de mais de uma instância em um momento não é segura.

Como os tipos de nós do Service Fabric no cluster são compostos de conjuntos de dimensionamento de máquinas virtuais no back-end, é possível [configurar regras de autoescala ou dimensionar manualmente](service-fabric-cluster-scale-in-out.md) cada tipo de nó/conjunto de dimensionamento de máquinas virtuais.

### <a name="programmatic-scaling"></a>Dimensionamento programático
Em muitos cenários, [Dimensionar um cluster manualmente ou com regras de autoescala](service-fabric-cluster-scale-in-out.md) é uma boa solução. Para cenários mais avançados, no entanto, eles podem não ser adequados. As desvantagens desses métodos incluem:

- O dimensionamento manual exige que você entre e solicite explicitamente as operações de dimensionamento. Se as operações de dimensionamento são necessárias com frequência ou em momentos imprevisíveis, essa abordagem não pode ser uma boa solução.
- Quando as regras de dimensionamento automático removem uma instância de um conjunto de dimensionamento de máquinas virtuais, elas não removem o conhecimento do nó do cluster do Service Fabric associado, a menos que o tipo de nó tenha um nível de durabilidade de Prata ou Ouro. Como as regras de dimensionamento automático funcionam no nível do conjunto de dimensionamento (em vez do nível do Service Fabric), elas podem remover nós do Service Fabric sem desligá-lo normalmente. Essa remoção de nó sem maiores cuidados deixará um estado do nó do Service Fabric “fantasma” de rastro após as operações de redução horizontal. Uma pessoa (ou um serviço) precisaria limpar periodicamente o estado do nó removido no cluster do Service Fabric.
- Um tipo de nó com um nível de durabilidade de Ouro ou Prata limpa automaticamente nós removidos, portanto, nenhuma limpeza adicional é necessária.
- Embora haja [várias métricas](../azure-monitor/platform/autoscale-common-metrics.md) com suporte pelas regras de dimensionamento automático, o conjunto ainda é limitado. Se seu cenário exigir dimensionamento com base em alguma métrica não abordada no conjunto, as regras de dimensionamento automático podem não ser uma boa opção.

Como você deve abordar o dimensionamento do Service Fabric depende de seu cenário. Se o dimensionamento for incomum, a capacidade de adicionar ou remover nós manualmente provavelmente será suficiente. Para cenários mais complexos, as regras de dimensionamento automático e os SDKs que expõem a capacidade de dimensionamento programático oferecem alternativas poderosas.

Existem APIs do Azure que permitem aos aplicativos trabalhar com conjuntos de dimensionamento de máquinas virtuais e clusters do Service Fabric por meio de programação. Se as opções de dimensionamento automático existentes não funcionam em seu cenário, essas APIs possibilitam a implementação de lógica de dimensionamento personalizada. 

Uma abordagem para implementar essa funcionalidade de dimensionamento automático “caseira” é adicionar um novo serviço sem estado ao aplicativo do Service Fabric para gerenciar operações de dimensionamento. Criar o próprio serviço de dimensionamento fornece o mais alto grau de controle e personalização do comportamento de dimensionamento do aplicativo. Isso pode ser útil para cenários que exigem controle preciso sobre quando ou como um aplicativo é dimensionado para dentro ou para fora. No entanto, esse controle vem com uma compensação da complexidade do código. O uso dessa abordagem significa que você precisa possuir código de dimensionamento, o que não é simples. No método `RunAsync` do serviço, um conjunto de disparadores pode determinar se o dimensionamento é necessário (inclusive verificando parâmetros como o tamanho máximo do cluster e o dimensionamento de cooldowns).   

A API usada para interações de conjunto de dimensionamento de máquinas virtuais (tanto para verificar o número atual de instâncias da máquina virtual quanto para modificá-lo) é a [biblioteca de computação do Gerenciamento do Azure fluente](https://www.nuget.org/packages/Microsoft.Azure.Management.Compute.Fluent/). A biblioteca de computação fluente fornece uma API fácil de usar para interagir com conjuntos de dimensionamento de máquinas virtuais.  Para interagir com o próprio cluster do Service Fabric, use [System.Fabric.FabricClient](/dotnet/api/system.fabric.fabricclient).

No entanto, o código de dimensionamento não precisa ser executado como um serviço no cluster a ser dimensionado. Tanto o `IAzure` quanto o `FabricClient` podem se conectar aos recursos do Azure associados remotamente e, portanto, o serviço de dimensionamento pode facilmente ser um aplicativo de console ou serviço do Windows executado de fora do aplicativo do Service Fabric.

Com base nessas limitações, convém [implementar modelos de dimensionamento automático mais personalizados](service-fabric-cluster-programmatic-scaling.md).

## <a name="scaling-up-and-down-or-vertical-scaling"></a>Escalar verticalmente e reduzir verticalmente, ou escala vertical 
Altera os recursos (CPU, memória ou armazenamento) de nós no cluster.
- Vantagens: a arquitetura do software e do aplicativo permanece a mesma.
- Desvantagens: escala finita, uma vez que há um limite para o quanto você pode aumentar os recursos em nós individuais. Tempo de inatividade, pois será necessário colocar computadores físicos ou virtuais offline para adicionar ou remover recursos.

Os conjuntos de escala de Máquina Virtual são um recurso de Computação do Azure que você pode usar para implantar e gerenciar uma coleção de máquinas virtuais como um conjunto. Cada tipo de nó definido em um cluster do Azure é [configurado como um conjunto de dimensionamento separado](service-fabric-cluster-nodetypes.md). Então, cada tipo de nó pode ser gerenciado separadamente.  Dimensionar um tipo de nó para cima ou para baixo envolve a adição de um novo tipo de nó (com SKU de VM atualizado) e a remoção do tipo de nó antigo.

Ao dimensionar um cluster do Azure, lembre-se das diretrizes a seguir:
- Se reduzir verticalmente um tipo de nó primário, você nunca deve reduzi-lo além do que a [camada de confiabilidade](service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster) permite.

O processo de escala vertical ou redução vertical de um tipo de nó é diferente, dependendo se é um tipo de nó primário ou não primário.

### <a name="scaling-non-primary-node-types"></a>Dimensionar tipos de nós não primários
Crie um novo tipo de nó com os recursos necessários.  Atualize as restrições de posicionamento dos serviços em execução para incluir o novo tipo de nó.  Gradualmente (um de cada vez), reduza a contagem de instâncias da contagem de instâncias do tipo de nó anterior para zero, de modo que a confiabilidade do cluster não seja afetada.  Os serviços migrarão gradualmente para o novo tipo de nó, já que o tipo de nó antigo é encerrado.

### <a name="scaling-the-primary-node-type"></a>Dimensionando o tipo de nó primário
Implante um novo tipo de nó primário com SKU de VM atualizado e, em seguida, desabilite as instâncias do tipo de nó primário original, uma de cada vez, para que os serviços do sistema migrem para o novo conjunto de dimensionamento. Verifique se o cluster e os novos nós estão íntegros e, em seguida, remova o conjunto de dimensionamento original e o estado do nó para os nós excluídos.

Se isso não for possível, você poderá criar um novo cluster e [restaurar o estado do aplicativo](service-fabric-reliable-services-backup-restore.md) (se aplicável) do cluster antigo. Você não precisa restaurar qualquer estado do serviço do sistema; ele é recriado quando você implanta os aplicativos no novo cluster. Se você estiver apenas executando aplicativos sem monitoração de estado no cluster, basta implantar os aplicativos no novo cluster; não há nada para restaurar.

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre [escalabilidade de aplicativo](service-fabric-concepts-scalability.md).
* [Reduzir horizontalmente ou escalar horizontalmente um cluster do Azure](service-fabric-tutorial-scale-cluster.md).
* [Dimensionar um cluster do Azure de forma programática](service-fabric-cluster-programmatic-scaling.md) usando a SDK fluente de computação do Azure.
* [Dimensione um cluster autônomo para dentro ou para fora](service-fabric-cluster-windows-server-add-remove-nodes.md).

