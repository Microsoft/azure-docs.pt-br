---
title: Introdução ao Resource Manager de Cluster do Service Fabric | Microsoft Docs
description: Uma introdução ao Gerenciador de Recursos de Cluster do Service Fabric.
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: cfab735b-923d-4246-a2a8-220d4f4e0c64
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: e9b1cc8b66be36a0a77118f4de672c9411433ba5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60743643"
---
# <a name="introducing-the-service-fabric-cluster-resource-manager"></a>Introdução ao Gerenciador de Recursos de Cluster do Service Fabric
Tradicionalmente, gerenciar sistemas de TI ou serviços online significava dedicar computadores ou máquinas virtuais específicas a esses serviços ou sistemas específicos. Os serviços foram projetados como camadas. Deveria haver uma camada da "Web" e uma camada de "dados" ou "armazenamento". Os aplicativos teriam uma camada de mensagens, em que solicitações de entrada e de saída fluíam, bem como um conjunto de computadores dedicados ao cache. Cada camada ou tipo de carga de trabalho tinha computadores específicos dedicados: o banco de dados tinha dois computadores dedicados a ele, enquanto os servidores Web contavam com alguns. Se um tipo específico de carga de trabalho fizesse com que os computadores em que ele estava tivessem utilização alta demais, você adicionaria mais computadores com a mesma configuração a essa camada. No entanto, nem todas as cargas de trabalho podiam ser dimensionadas tão facilmente – especialmente com a camada de dados, normalmente você substituiria os computadores por computadores maiores. Fácil. Se uma máquina falhava, essa parte do aplicativo geral era executada com menor capacidade até que o computador pudesse ser restaurado. Ainda assim, relativamente fácil (mesmo que não necessariamente divertido).

Agora, no entanto, o mundo de arquitetura de software e serviço mudou. É mais comum que os aplicativos adotem um design de expansão. É comum criar aplicativos com contêineres ou microsserviços (ou ambos). Agora, enquanto você ainda pode ter apenas alguns computadores, eles não executam apenas uma única instância de uma carga de trabalho. Eles podem até mesmo executar várias cargas de trabalho diferentes ao mesmo tempo. Agora, você tem dezenas de tipos de serviços diferentes (nenhum deles consumindo todos os recursos de um computador), talvez centenas de instâncias diferentes desses serviços. Cada instância nomeada tem uma ou mais instâncias ou réplicas para HA (Alta Disponibilidade). Dependendo dos tamanhos dessas cargas de trabalho e do quanto elas ficam ocupadas, você pode acabar tendo centenas ou milhares de computadores. 

Inesperadamente, já não era tão simples gerenciar seu ambiente com poucos computadores dedicados a tipos de carga de trabalho únicos. Os servidores são virtuais e não têm mais nomes (sua mentalidade passou a valorizar a [coletividade](https://www.slideshare.net/randybias/architectures-for-open-and-scalable-clouds/20) ). A configuração tem menos a ver com os computadores e mais a ver com os próprios serviços. Ter hardware dedicado a uma única instância de uma carga de trabalho é basicamente uma coisa do passado. Os próprios serviços tornaram-se pequenos sistemas distribuídos que abrangem várias partes menores do hardware de mercadoria.

Como seu aplicativo não é mais uma série de monolitos espalhados por várias camadas, agora você tem muito combinações com que lidar. Quem decide quais tipos de carga de trabalho podem ser executados em qual hardware, ou quantos? Quais cargas de trabalho funcionam bem no mesmo hardware e quais entram em conflito? Quando um computador fica inoperante, como você sabe o que estava em execução nesse computador? Quem é o responsável por garantir que a carga de trabalho comece a execução novamente? Você espera que a máquina (virtual) volte ou suas cargas de trabalho fazem failover automaticamente para outras máquinas e continuam em execução? Há necessidade de intervenção humana? E quanto às atualizações nesse ambiente?

Como desenvolvedores e operadores que lidam com esse ambiente, vamos precisar de ajuda para gerenciar essa complexidade. Uma onda de contratações para tentar esconder a complexidade por trás das pessoas provavelmente não é a resposta certa, então o que devemos fazer?

## <a name="introducing-orchestrators"></a>Introdução aos orquestradores
Um "Orquestrador" é o termo geral para um componente de software que ajuda os administradores a gerenciar esses tipos de ambiente. Orquestradores são os componentes que recebem solicitações como “Eu gostaria de ter cinco cópias desse serviço em execução no meu ambiente”. Eles tentam corresponder o ambiente ao estado desejado, independente do que venha a ocorrer.

Os orquestradores (não humanos) são os que entram em ação quando um computador falha ou quando uma carga de trabalho é encerrada por algum motivo inesperado. A maioria dos orquestradores faz mais do que apenas lidar com falhas. Outros recursos que eles oferecem são o gerenciamento de novas implantações, a manipulação de atualizações e o tratamento do consumo e da governança de recursos. Todos os orquestradores destinam-se fundamentalmente a manter algum estado desejado da configuração no ambiente. Você quer poder informar a um orquestrador o que você deseja para que ele faça o trabalho pesado. Aurora sobre Mesos, Docker Datacenter/Docker Swarm, Kubernetes e Service Fabric são exemplos de orquestradores. Esses orquestradores estão sendo desenvolvidos ativamente para atender às necessidades de cargas de trabalho reais em ambientes de produção. 

## <a name="orchestration-as-a-service"></a>Orquestração como um serviço
O Gerenciador de Recursos de Cluster é o componente de sistema que lida com a orquestração no Service Fabric. O trabalho do Gerenciador de Recursos de Cluster é dividido em três partes:

1. Imposição de regras
2. Otimização do seu ambiente
3. Ajudando com outros processos

### <a name="what-it-isnt"></a>O que não é
Em aplicativos tradicionais de N camadas, sempre há um [Balanceador de Carga](https://en.wikipedia.org/wiki/Load_balancing_(computing)). Geralmente, esse era um NLB (Balanceador de Carga de Rede) ou um ALB (Balanceador de Carga de Aplicativo), dependendo de onde ele estava na pilha da rede. Alguns balanceadores de carga se baseiam em Hardware, como a oferta BigIP da F5 e outros em software, como o NLB da Microsoft. Em outros ambientes, você poderá ver algo como HAProxy, nginx, Istio ou Envoy nessa função. Nessas arquiteturas, o trabalho de balanceamento de carga é garantir que as cargas de trabalho sem estado recebam (aproximadamente) a mesma quantidade de trabalho. As estratégias de balanceamento de carga variavam. Alguns balanceadores enviavam cada chamada para um servidor diferente. Outros forneciam fixação/adesão à sessão. Balanceadores mais avançados usam estimativa de carga real ou relatórios para encaminhar uma chamada com base em seu custo esperado e na carga atual do computador.

Balanceadores de rede ou roteadores de mensagem tentavam garantir que uma camada Web/de trabalho permaneça razoavelmente equilibrada. Estratégias para balancear a camada de dados eram diferentes e dependiam do mecanismo de armazenamento de dados. O balanceamento da camada de dados dependia de fragmentação de dados, cache, exibições gerenciadas, procedimentos armazenados e outros mecanismos específicos ao repositório.

Embora algumas dessas estratégias sejam interessantes, o Gerenciador de Recursos de Cluster do Service Fabric não é um balanceador de carga de rede ou um cache. Um balanceador de carga de rede equilibra front-ends, distribuindo o tráfego entre eles. O Gerenciador de Recursos de Cluster do Service Fabric tem uma estratégia diferente. Fundamentalmente, o Service Fabric move os *serviços* para onde eles fazem mais sentido, esperando que o tráfego ou a carga os siga. Por exemplo, ele pode mover serviços para nós que são pouco utilizados no momento, pois os serviços que estão lá não estão trabalhando muito. Os nós podem ser pouco utilizados porque os serviços existentes foram excluídos ou movidos para outro lugar. Como outro exemplo, o Cluster Resource Manager também poderia retirar um serviço de um computador. Talvez o computador esteja prestes a ser atualizada ou esteja sobrecarregada devido a um aumento no consumo dos serviços em execução nela. Alternativamente, os requisitos de recursos do serviço podem ter aumentado. Como resultado, não há recursos suficientes neste computador para continuar a execução. 

Como o Gerenciador de Recursos de Cluster é responsável por mover os serviços, ele contém um conjunto de recursos diferente em comparação com o que você encontraria em um balanceador de carga de rede. Isso acontece porque os balanceadores de carga de rede entregam o tráfego de rede em que os serviços já estão, mesmo que esse local não seja ideal para executar o próprio serviço. O Gerenciador de Recursos de Cluster do Service Fabric utiliza estratégias fundamentalmente diferentes para garantir que os recursos do cluster sejam utilizados com eficiência.

## <a name="next-steps"></a>Próximas etapas
- Para obter informações sobre a arquitetura e fluxo de informações dentro do Gerenciador de recursos de Cluster, fazer check-out [neste artigo](service-fabric-cluster-resource-manager-architecture.md)
- O Cluster Resource Manager tem muitas opções para descrever o cluster. Para saber mais sobre as métricas, confira este artigo sobre a [descrição de um cluster do Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)
- Para saber mais sobre como configurar os serviços, [Saiba mais sobre como configurar serviços](service-fabric-cluster-resource-manager-configure-services.md)
- As métricas são como o Gerenciador de Recursos de Cluster do Service Fabric gerencia o consumo e a capacidade no cluster. Para saber mais sobre as métricas e como configurá-las, confira [este artigo](service-fabric-cluster-resource-manager-metrics.md)
- O Gerenciador de Recursos de Cluster funciona com recursos de gerenciamento do Service Fabric. Para saber mais sobre essa integração, leia [este artigo](service-fabric-cluster-resource-manager-management-integration.md)
- Para descobrir como o Gerenciador de Recursos de Cluster gerencia e balanceia carga no cluster, confira o artigo sobre [como balancear carga](service-fabric-cluster-resource-manager-balancing.md)
