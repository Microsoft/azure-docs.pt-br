---
title: Perguntas comuns sobre o Microsoft Azure Service Fabric | Microsoft Docs
description: Perguntas frequentes e respostas sobre o Service Fabric
services: service-fabric
documentationcenter: .net
author: chackdan
manager: timlt
editor: 
ms.assetid: 5a179703-ff0c-4b8e-98cd-377253295d12
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2017
ms.author: chackdan
ms.openlocfilehash: ee334186dffaa1f88cf05717b6a5ba1e819a8cdc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="commonly-asked-service-fabric-questions"></a>Perguntas frequentes sobre o Service Fabric

Há muitas perguntas frequentes sobre o que o Service Fabric pode fazer e como ele deve ser usado. Este documento aborda muitas dessas perguntas comuns e suas respostas.

## <a name="cluster-setup-and-management"></a>Gerenciamento e instalação de cluster

### <a name="can-i-create-a-cluster-that-spans-multiple-azure-regions-or-my-own-datacenters"></a>É possível criar um cluster que abranja várias regiões do Azure ou meus próprios data centers?

Sim. 

A principal tecnologia de clustering do Service Fabric pode ser usada para combinar máquinas em execução em qualquer lugar do mundo, desde que elas tenham conectividade de rede entre si. No entanto, criar e executar um cluster desse tipo pode ser complicado.

Se você estiver interessado nesse cenário, é recomendável entrar em contato por meio da [Lista de problemas do Service Fabric no Github](https://github.com/azure/service-fabric-issues) ou de seu representante de suporte para obter orientações adicionais. A equipe do Service Fabric está trabalhando para fornecer informações, orientações e recomendações adicionais para esse cenário. 

Veja a seguir alguns itens que você deve levar em consideração: 

1. Atualmente, o recurso de cluster do Service Fabric no Azure é regional, assim como os conjuntos de dimensionamento de máquinas virtuais nos quais o cluster se baseia. Isso significa que, em caso de falha regional, você pode perder a capacidade de gerenciar o cluster por meio do Azure Resource Manager ou do Portal do Azure. Isso pode acontecer mesmo que o cluster permaneça em execução e que você possa interagir diretamente com ele. Além disso, atualmente o Azure não possibilita ter uma única rede virtual que possa ser usada em várias regiões. Isso significa que um cluster de várias regiões do Azure requer [Endereços IP Públicos para cada VM nos Conjuntos de Dimensionamento de VMs](../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md#public-ipv4-per-virtual-machine) ou [Gateways de VPN do Azure](../vpn-gateway/vpn-gateway-about-vpngateways.md). Essas opções de rede têm impactos diferentes sobre os custos, o desempenho e, até certo ponto, o design do aplicativo, de modo que análise e planejamento cuidadosos são necessários antes de manter um ambiente desse tipo.
2. A manutenção, o gerenciamento e o monitoramento dessas máquinas podem ficar complicados, especialmente quando distribuídas em _tipos_ de ambientes, como entre provedores de nuvem diferentes ou entre recursos locais e o Azure. É necessário ter cuidado para garantir que as atualizações, o monitoramento, o gerenciamento e o diagnóstico sejam compreendidos para o cluster e para os aplicativos antes de executar cargas de trabalho de produção em um ambiente desse tipo. Se você já tiver bastante experiência solucionando esses problemas no Azure ou em seus próprios data centers, é provável que as mesmas soluções possam ser aplicadas ao desenvolver ou executar seu cluster do Service Fabric. 

### <a name="do-service-fabric-nodes-automatically-receive-os-updates"></a>Os nós do Service Fabric recebem as atualizações do sistema operacional automaticamente?

No momento não, mas essa é uma solicitação comum a que o Azure pretende atender.

Enquanto isso, [fornecemos um aplicativo](service-fabric-patch-orchestration-application.md) para que os sistemas operacionais sob seus nós do Service Fabric permaneçam corrigidos e atualizados.

O desafio com as atualizações do sistema operacional é que elas normalmente exigem a reinicialização da máquina, o que resulta em uma perda de disponibilidade temporária. Por si só, isso não é um problema, já que o Service Fabric redirecionará automaticamente o tráfego para esses serviços a outros nós. No entanto, se as atualizações do sistema operacional não forem coordenadas no cluster, haverá a possibilidade de que muitos nós fiquem inativos ao mesmo tempo. Essas reinicializações simultâneas podem causar a perda de disponibilidade completa para um serviço ou, pelo menos, para uma partição específica (para um serviço com estado).

No futuro, planejamos dar suporte a uma política de atualização do sistema operacional coordenada e totalmente automatizada entre domínios de atualização, garantindo que a disponibilidade seja mantida apesar das reinicializações e outras falhas inesperadas.

### <a name="can-i-use-large-virtual-machine-scale-sets-in-my-sf-cluster"></a>Posso usar grandes conjuntos de dimensionamento de máquinas virtuais no meu cluster do SF? 

**Resposta curta** – Não. 

**Resposta longa** – Embora os grandes conjuntos de dimensionamento de máquinas virtuais permitam que você dimensione um conjunto de dimensionamento de máquinas virtuais até 1000 instâncias de VM, isso é feito pelo uso de PGs (Grupos de Posicionamento). FDs (domínios de falha) e UDs (domínios de atualização) só são consistentes dentro de um grupo de posicionamento. O Service Fabric usa UDs e FDs para tomar decisões de posicionamento de suas instâncias de serviço/réplicas de serviço. Já que UDs e FDs são comparáveis somente dentro de um grupo de posicionamento, o SF não pode usá-los. Por exemplo, se VM1 no PG1 tem uma topologia de FD=0 e VM9 em PG2 tem uma topologia de FD=4, isso não significa que VM1 e VM2 estejam em dois Racks de hardwares diferentes, portanto, os SFs não podem usar os valores de FD nesse caso para tomar decisões de posicionamento.

Atualmente, há outros problemas com grandes conjuntos de dimensionamento de máquinas virtuais, como a falta de suporte para o balanceamento de carga de nível 4. Consulte para obter [detalhes sobre grandes conjuntos de dimensionamento](../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md)



### <a name="what-is-the-minimum-size-of-a-service-fabric-cluster-why-cant-it-be-smaller"></a>Qual o tamanho mínimo de um cluster do Service Fabric? Por que ele não pode ser menor?

O tamanho mínimo com suporte para um cluster do Service Fabric que execute cargas de trabalho de produção é de cinco nós. Para cenários de desenvolvimento/teste, oferecemos suporte para três clusters de nó.

Esses valores mínimos existem porque o cluster do Service Fabric executa um conjunto de serviços com sistema de estado, incluindo o serviço de nomes e o gerenciador de failover. Esses serviços, que controlam quais serviços foram implantados no cluster e onde eles estão hospedados atualmente, dependem de uma consistência forte. Essa consistência forte, por sua vez, depende da capacidade de adquirir um *quorum* para qualquer atualização para o estado desses serviços, onde um quorum representa a maioria estrita das réplicas (N/2 + 1) para um determinado serviço.

Com esse plano de fundo, vamos examinar algumas configurações de cluster possíveis:

**Um nó**: essa opção não oferece alta disponibilidade, já que a perda do nó único por qualquer motivo significa a perda de todo o cluster.

**Dois nós**: um quorum para um serviço implantado entre dois nós (N = 2) é 2 (2/2 + 1 = 2). Quando uma única réplica é perdida, é impossível criar um quorum. Como a execução de uma atualização de serviço requer desativar temporariamente uma réplica, essa não é uma configuração útil.

**Três nós**: com três nós (N=3), o requisito para criar um quorum ainda é dois nós (3/2 + 1 = 2). Isso significa que você pode perder um nó individual e ainda manter o quorum.

A configuração do cluster de três nós tem suporte para desenvolvimento e teste porque você pode fazer atualizações e resistir a falhas de nós individuais com segurança, desde que elas não ocorram simultaneamente. Para cargas de trabalho de produção, você deve ser resiliente a tais falhas simultâneas, portanto é necessário ter cinco nós.

### <a name="can-i-turn-off-my-cluster-at-nightweekends-to-save-costs"></a>Posso desativar meu cluster à noite/aos finais de semana para reduzir custos?

Em geral, não. O Service Fabric armazena o estado em discos locais, efêmeros, o que significa que, se a máquina virtual for movida para um host diferente, os dados não serão movidos com ela. Na operação normal, isso não é um problema, já que o novo nó é atualizado por outros nós. No entanto, se você parar todos os nós e reiniciá-los mais tarde, haverá uma possibilidade significativa de a maioria dos nós iniciar em novos hosts e impossibilitar a recuperação do sistema.

Se você quiser criar clusters para testar seu aplicativo antes da implantação, é recomendável criar dinamicamente os clusters como parte de sua [integração contínua/pipeline de implantação contínuo](service-fabric-set-up-continuous-integration.md).


### <a name="how-do-i-upgrade-my-operating-system-for-example-from-windows-server-2012-to-windows-server-2016"></a>Como atualizo meu sistema operacional (por exemplo, do Windows Server 2012 para o Windows Server 2016)?

Embora estejamos trabalhando em uma experiência aprimorada, no momento você é responsável pela atualização. Você deve atualizar a imagem do sistema operacional nas máquinas virtuais do cluster em uma VM por vez. 

## <a name="container-support"></a>Suporte a contêiner

### <a name="why-are-my-containers-that-are-deployed-to-sf-unable-to-resolve-dns-addresses"></a>Por que meus contêineres que estão implantados no SF não podem resolver endereços DNS?

Esse problema foi relatado em clusters que estão na versão 5.6.204.9494 

**Mitigação**: siga [este documento](service-fabric-dnsservice.md) para habilitar o serviço Service Fabric de DNS no cluster.

**Correção**: atualize para uma versão de cluster com suporte que seja superior à 5.6.204.9494, quando ela estiver disponível. Se o cluster estiver configurado para atualizações automáticas, o cluster atualizará automaticamente para a versão que tem esse problema corrigido.

  
## <a name="application-design"></a>Design do aplicativo

### <a name="whats-the-best-way-to-query-data-across-partitions-of-a-reliable-collection"></a>Qual a melhor maneira de consultar dados ao longo das partições de uma Coleção Confiável?

Coleções Confiáveis normalmente são [particionadas](service-fabric-concepts-partitioning.md) para permitir a escalação horizontal para um melhor desempenho e resultado. Isso significa que o estado de um determinado serviço pode ser distribuído entre dezenas ou centenas de máquinas. Para executar operações no conjunto de dados completo, você tem algumas opções:

- Criar um serviço que consulte todas as partições de outro serviço para obter os dados necessários.
- Criar um serviço que possa receber dados de todas as partições de outro serviço.
- Enviar dados por push periodicamente de cada serviço para um repositório externo. Essa abordagem é apropriada apenas se as consultas que você estiver fazendo não fizerem parte da lógica comercial principal.


### <a name="whats-the-best-way-to-query-data-across-my-actors"></a>Qual a melhor maneira de consultar dados entre os meus atores?

Os atores são projetados para serem unidades independentes de estado e computação, portanto, não é recomendável executar consultas amplas de estado do ator no tempo de execução. Se você precisar fazer uma consulta em todo o conjunto de um estado do ator, deve considerar:

- Substituir seus serviços de ator por serviços confiáveis de estado, assim como o número de solicitações de rede para reunir todos os dados do número de atores para o número de partições do seu serviço.
- Projetar seus atores para enviar seu estado por push a um repositório externo periodicamente para facilitar a consulta. Como acima, essa abordagem só é viável se as consultas que você estiver executando não forem necessárias para o comportamento do seu tempo de execução.

### <a name="how-much-data-can-i-store-in-a-reliable-collection"></a>Quantos dados posso armazenar em uma Coleção Confiável?

Serviços confiáveis normalmente são particionados, de forma que o valor que você pode armazenar é limitado apenas pelo número de máquinas que tem no cluster e a quantidade de memória disponível nessas máquinas.

Por exemplo, suponha que você tem uma coleção confiável em um serviço com 100 partições e 3 réplicas, armazenando objetos com 1kb de tamanho, em média. Agora, suponha que você tem um cluster de 10 máquinas com 16 gb de memória por máquina. Para simplificar e sendo muito cautelosos, suponha que o sistema operacional e os serviços do sistema, o tempo de execução do Service Fabric e seus serviços consomem 6 gb disso, deixando 10 gb disponíveis por máquina ou 100 gb para o cluster.

Tendo em mente que cada objeto deve ser armazenado três vezes (uma primária e duas réplicas), você teria memória suficiente para cerca de 35 milhões de objetos na sua coleção ao operar com capacidade total. No entanto, recomendamos ser resiliente à perda simultânea de um domínio de falha e um domínio de atualização, que representa aproximadamente 1/3 da capacidade e reduziria o número a aproximadamente 23 milhões.

Observe que esse cálculo também pressupõe que:

- A distribuição de dados entre as partições é aproximadamente uniforme ou que você está relatando métricas de carga para o Gerenciador de Recursos de Cluster. Por padrão, o Service Fabric balanceará a carga com base na contagem de réplicas. No nosso exemplo acima, isso colocaria 10 réplicas primárias e 20 réplicas secundárias em cada nó no cluster. Isso funciona bem para cargas distribuídas uniformemente entre as partições. Se a carga não for uniforme, você deverá relatá-la para que o Resource Manager possa empacotar réplicas menores juntas e permitir que as réplicas maiores consumam mais memória em um nó individual.

- O serviço confiável em questão é o único estado de armazenamento no cluster. Como você pode implantar vários serviços em um cluster, terá que estar atento aos recursos que cada um precisará executar e gerenciar seu estado.

- O cluster em si não está aumentando ou diminuindo. Se você adicionar mais máquinas, o Service Fabric irá rebalancear as réplicas para aproveitar a capacidade adicional até que o número de máquinas ultrapasse o número de partições no seu serviço, já que uma réplica individual não pode abranger máquinas. Por outro lado, se você reduzir o tamanho do cluster removendo computadores, suas réplicas serão empacotadas mais próximas e terão menos capacidade geral.

### <a name="how-much-data-can-i-store-in-an-actor"></a>Quantos dados posso armazenar em um ator?

Assim como acontece com os serviços confiáveis, a quantidade de dados que você pode armazenar em um serviço de ator é limitada apenas pelo espaço em disco total e a memória disponível entre os nós no cluster. No entanto, os atores individuais são mais eficazes quando são usados para encapsular uma pequena quantidade de estado e a lógica comercial associada. Como regra geral, um ator individual deve ter um estado que seja medido em quilobytes.

## <a name="other-questions"></a>Outras perguntas

### <a name="how-does-service-fabric-relate-to-containers"></a>Como o Service Fabric se relaciona com os contêineres?

Os contêineres oferecem uma maneira simples de empacotar serviços e suas dependências, de modo que executam consistentemente em todos os ambientes e podem operar de maneira isolada em uma única máquina. O Service Fabric oferece uma maneira de implantar e gerenciar serviços, incluindo [serviços que foram empacotados em um contêiner](service-fabric-containers-overview.md).

### <a name="are-you-planning-to-open-source-service-fabric"></a>Vocês pretendem abrir o código do Service Fabric?

Pretendemos abrir o código das estruturas dos Reliable Services e dos Reliable Actors no GitHub, e aceitaremos contribuições da comunidade para esses projetos. Siga o [blog do Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/) para obter mais detalhes à medida que eles forem anunciados.

No momento, não há planos para abrir o código do tempo de execução do Service Fabric.

## <a name="next-steps"></a>Próximas etapas

- [Saiba mais sobre os principais conceitos do Service Fabric e práticas recomendadas](https://mva.microsoft.com/en-us/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tbuZM46yC_5206218965)
