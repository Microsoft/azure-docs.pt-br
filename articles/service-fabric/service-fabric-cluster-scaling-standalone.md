---
title: Dimensionamento de cluster autônomo do Azure Service Fabric
description: Saiba mais sobre o dimensionamento de clusters do Service Fabric autônomo no ou horizontalmente e verticalmente.
ms.topic: conceptual
ms.date: 11/13/2018
ms.openlocfilehash: 8184b2455e938fa0500308b462176e78b8dadcab
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91843024"
---
# <a name="scaling-service-fabric-standalone-clusters"></a>Escalando clusters autônomos do Service Fabric
Um cluster do Service Fabric é um conjunto de computadores físicos ou virtuais conectados via rede, nos quais os microsserviços são implantados e gerenciados. Uma máquina ou VM que faz parte de um cluster é chamada de nó. Os clusters podem conter potencialmente milhares de nós. Após criar um cluster do Service Fabric, será possível dimensionar o cluster horizontalmente (alterar o número de nós) ou verticalmente (alterar os recursos dos nós).  É possível dimensionar o cluster a qualquer momento, mesmo quando as cargas de trabalho estiverem em execução no cluster.  Na medida em que o cluster for dimensionado, os aplicativos também serão dimensionados automaticamente.

Por quê dimensionar o cluster? As demandas de aplicativos alteram ao longo do tempo.  Talvez seja necessário aumentar os recursos de cluster para atender ao aumento do tráfego ou de carga de trabalho do aplicativo ou diminuir os recursos de cluster quando houver queda de demanda.

## <a name="scaling-in-and-out-or-horizontal-scaling"></a>Reduzir horizontalmente e escalar horizontalmente, ou dimensionamento horizontal
Altera o número de nós no cluster.  Quando os novos nós unem-se ao cluster, o [Gerenciador de Recursos de Cluster](service-fabric-cluster-resource-manager-introduction.md) move os serviços para eles, o que reduz a carga nos nós existentes.  Também será possível diminuir o número de nós, se os recursos de cluster não estiverem sendo utilizados eficientemente.  Conforme os nós saem do cluster, os serviços são removidos desses nós e a carga aumenta nos nós restantes.  Reduzir o número de nós em um cluster em execução no Azure ajuda a economizar dinheiro, já que você paga pelo número de VMs utilizadas e não pela carga de trabalho nessas VMs.  

- Vantagens: escala infinita, em teoria.  Se o aplicativo for projetado para escalabilidade, será possível habilitar o crescimento ilimitado adicionando mais nós.  As ferramentas em ambientes de nuvem facilitam a adição ou a remoção de nós, por isso é fácil ajustar a capacidade e você paga apenas pelos recursos que usa.  
- Desvantagens: os aplicativos devem ser [projetados para escalabilidade](service-fabric-concepts-scalability.md).  Bancos de dados de aplicativos e persistência podem exigir trabalho de arquitetura adicional também para dimensionar.  As [Coleções confiáveis](service-fabric-reliable-services-reliable-collections.md) nos serviços com estado do Service Fabric, no entanto, facilitam muito dimensionar os dados de aplicativos.

Os clusters autônomos permitem implantar o cluster do Service Fabric local ou no provedor de nuvem de sua escolha.  Os tipos de nó são compostos por computadores físicos ou virtuais, dependendo da sua implantação. Comparado aos clusters em execução no Azure, o processo de dimensionamento de um cluster autônomo é um pouco mais complicado.  É necessário alterar manualmente o número de nós no cluster e, em seguida, executar uma atualização de configuração de cluster.

A remoção de nós pode iniciar várias atualizações. Alguns nós são marcados com a marca `IsSeedNode=”true”` e podem ser identificados, consultando o manifesto do cluster usando [Get-ServiceFabricClusterManifest](/powershell/module/servicefabric/get-servicefabricclustermanifest). A remoção desses nós pode levar mais tempo do que outros, pois os nós de propagação terão de ser movidos nesses cenários. O cluster deve manter no mínimo três nós do tipo nó primário.

> [!WARNING]
> É recomendável não reduzir a contagem de nós abaixo do [Tamanho do cluster da camada de confiabilidade](service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster) para o cluster. Isso vai interferir na capacidade de os Serviços do Sistema do Service Fabric serem replicados no cluster, assim como vai desestabilizar ou possivelmente destruir o cluster.
>

Ao dimensionar um cluster autônomo, lembre-se das diretrizes a seguir:
- A substituição de nós primários deve ser realizada um nó após o outro, em vez de remover e depois adicionar em lotes.
- Antes de remover um tipo de nó, verifique se existem nós referenciando o tipo de nó. Remova esses nós antes de remover o tipo de nó correspondente. Depois que todos os nós correspondentes são removidos, você pode remover o Tipo de Nó da configuração do cluster e começar uma configuração de atualização usando [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade).

Para obter mais informações, consulte [dimensionar um cluster autônomo](service-fabric-cluster-windows-server-add-remove-nodes.md).

## <a name="scaling-up-and-down-or-vertical-scaling"></a>Escalar verticalmente e reduzir verticalmente, ou escala vertical 
Altera os recursos (CPU, memória ou armazenamento) de nós no cluster.
- Vantagens: a arquitetura do software e do aplicativo permanece a mesma.
- Desvantagens: escala finita, uma vez que há um limite para o quanto você pode aumentar os recursos em nós individuais. Tempo de inatividade, pois será necessário colocar computadores físicos ou virtuais offline para adicionar ou remover recursos.

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre [escalabilidade de aplicativo](service-fabric-concepts-scalability.md).
* [Reduzir horizontalmente ou escalar horizontalmente um cluster do Azure](service-fabric-tutorial-scale-cluster.md).
* [Dimensionar um cluster do Azure de forma programática](service-fabric-cluster-programmatic-scaling.md) usando a SDK fluente de computação do Azure.
* [Dimensione um cluster autônomo para dentro ou para fora](service-fabric-cluster-windows-server-add-remove-nodes.md).

