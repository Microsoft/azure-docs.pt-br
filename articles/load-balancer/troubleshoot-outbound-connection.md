---
title: Solucionar problemas de conexões de saída no Azure Load Balancer
description: Resoluções para problemas comuns com a conectividade de saída por meio do Azure Load Balancer.
services: load-balancer
author: erichrt
ms.service: load-balancer
ms.topic: troubleshooting
ms.date: 05/7/2020
ms.author: errobin
ms.openlocfilehash: 516576f4e005cc9fe2303945ecb1a13489908a5d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94696346"
---
# <a name="troubleshooting-outbound-connections-failures"></a><a name="obconnecttsg"></a> Solucionando problemas de falhas de conexões de saída

Este artigo destina-se a fornecer resoluções para problemas comuns que podem ocorrer com conexões de saída de um Azure Load Balancer. A maioria dos problemas com a conectividade de saída que os clientes enfrentam é devido ao esgotamento da porta SNAT e tempos limite de conexão que levam a pacotes descartados. Este artigo fornece etapas para atenuar cada um desses problemas.

## <a name="managing-snat-pat-port-exhaustion"></a><a name="snatexhaust"></a> Gerenciar esgotamento da porta SNAT (PAT)
[As portas efêmeras](load-balancer-outbound-connections.md) usadas [para Pat](load-balancer-outbound-connections.md) são um recurso esse esgotável, conforme descrito em [VM autônoma sem um endereço IP público](load-balancer-outbound-connections.md) e [VM com balanceamento de carga sem um endereço IP público](load-balancer-outbound-connections.md). Você pode monitorar o uso de portas efêmeras e comparar com sua alocação atual para determinar o risco de ou confirmar o esgotamento de SNAT usando [este](./load-balancer-standard-diagnostics.md#how-do-i-check-my-snat-port-usage-and-allocation) guia.

Se você sabe que está iniciando muitas conexões TCP ou UDP de saída para o mesmo endereço e porta IP de destino, se você observa as conexões de saída com falha ou é avisado pelo suporte que as portas SNAT ([portas efêmeras](load-balancer-outbound-connections.md#preallocatedports) pré-alocadas usadas pela [PAT](load-balancer-outbound-connections.md)) estão se esgotando, você terá várias opções gerais de mitigação. Avalie essas opções e decida o que está disponível e melhor para o seu cenário. É possível que uma ou mais possam ajudar a gerenciar esse cenário.

Se você tiver problemas para entender o comportamento da conexão de saída, poderá usar as estatísticas de pilha de IP (netstat). Ou pode ser útil observar comportamentos de conexão usando capturas de pacote. Você pode executar essas capturas de pacotes no sistema operacional convidado da sua instância ou usar o [Observador de Rede para captura de pacote](../network-watcher/network-watcher-packet-capture-manage-portal.md). 

## <a name="manually-allocate-snat-ports-to-maximize-snat-ports-per-vm"></a><a name ="manualsnat"></a>Alocar portas SNAT manualmente para maximizar o número de portas SNAT por VM
Conforme definido em [portas prealocadas](load-balancer-outbound-connections.md#preallocatedports), o balanceador de carga alocará automaticamente as portas com base no número de VMs no back-end. Por padrão, isso é feito de forma conservadora para garantir a escalabilidade. Se você souber o número máximo de VMs que terá no back-end, poderá alocar manualmente portas SNAT em cada regra de saída. Por exemplo, se você souber que terá um máximo de dez VMs, poderá alocar 6.400 portas SNAT por VM em vez de 1.024, o número padrão. 

## <a name="modify-the-application-to-reuse-connections"></a><a name="connectionreuse"></a>Modificar o aplicativo para reutilizar conexões 
Você pode reduzir a demanda por portas efêmeras usadas para SNAT, reutilizando as conexões em sua aplicação. A reutilização de conexão é especialmente relevante para protocolos como HTTP/1.1, onde a reutilização de conexão é o padrão. E outros protocolos que usam o HTTP como seu transporte (por exemplo, REST) podem se beneficiar por sua vez. 

A reutilização é sempre melhor que as conexões TCP individuais e atômicas para cada solicitação. A reutilização resulta em transações TCP mais eficazes e muito eficientes.

## <a name="modify-the-application-to-use-connection-pooling"></a><a name="connection pooling"></a>Modificar o aplicativo para usar o pooling de conexão
Você pode empregar um esquema de pooling de conexão em seu aplicativo, onde as solicitações são distribuídas internamente em um conjunto de conexões fixo (cada reutilização sempre que possível). Esse esquema restringe o número de portas efêmeras em uso e cria um ambiente mais previsível. Esse esquema também pode aumentar a taxa de transferência de solicitações, permitindo múltiplas operações simultâneas quando uma única conexão está bloqueando na resposta de uma operação.  

O pooling de conexão já pode existir dentro da estrutura que você está utilizando para desenvolver o aplicativo ou as configurações para o aplicativo. É possível combinar pooling de conexão com reuso de conexão. As várias solicitações, então, consomem um número fixo e previsível de portas para o mesmo endereço e porta IP de destino. As solicitações também beneficiam-se do uso eficiente das transações TCP, reduzindo a latência e a utilização dos recursos. As transações UDP também podem beneficiar, porque o gerenciamento do número de fluxos UDP pode, por sua vez, evitar condições de esgotamento e gerenciar a utilização da porta SNAT.

## <a name="modify-the-application-to-use-less-aggressive-retry-logic"></a><a name="retry logic"></a>Modificar o aplicativo para usar uma lógica de repetição menos agressiva
Quando as [portas efêmeras pré-alocadas](load-balancer-outbound-connections.md#preallocatedports) usadas para [PAT](load-balancer-outbound-connections.md) estão esgotadas ou se ocorrem falhas no aplicativo, repetições de força bruta ou agressivas sem lógica de declínio e retirada fazem com que o esgotamento ocorra ou persista. Você pode reduzir a demanda de portas efêmeras usando uma lógica de repetição menos agressiva. 

As portas efêmeras têm um tempo limite de ociosidade de 4 minutos (não ajustável). Se as tentativas forem muito agressivas, o esgotamento não terá oportunidade de limpar por conta própria. Portanto, considerando como -- e com que frequência -- o aplicativo reage transações é uma parte crítica do projeto.

## <a name="assign-a-public-ip-to-each-vm"></a><a name="assignilpip"></a>Atribuir um endereço IP a cada VM
A atribuição de um endereço IP público altera seu cenário para [IP público para uma VM](load-balancer-outbound-connections.md). Todas as portas efêmeras do IP público que são usadas para cada VM estão disponíveis para a VM. (Em oposição aos cenários em que as portas efêmeras de um IP público são compartilhadas com todas as VMs associadas ao respectivo pool de back-end.) Há compensações a serem consideradas, como o custo adicional de endereços IP públicos e o possível impacto da filtragem de um grande número de endereços IP individuais.

>[!NOTE] 
>Essa opção não está disponível para as funções de trabalho da Web.

## <a name="use-multiple-frontends"></a><a name="multifesnat"></a>Utilizar vários front-ends
Ao utilizar o Load Balancer Standard púbico, você atribui [vários endereços IP de front-end para conexões de saída](load-balancer-outbound-connections.md) e [multiplica o número de portas de SNAT disponíveis](load-balancer-outbound-connections.md#preallocatedports).  Crie uma configuração IP front-end, uma regra e um pool de back-end para acionar a programação do SNAT para o IP público do frontend.  A regra não precisa funcionar e uma análise de integridade não precisa ter êxito.  Se você usar vários frontends para a entrada também (em vez de apenas para saída), use bem os probes de integridade personalizados para garantir a confiabilidade.

>[!NOTE]
>Na maioria dos casos, o esgotamento das portas de SNAT é um sinal de design inapropriado.  Antes de utilizar mais front-ends para adicionar portas de SNAT, certifique-se de reconhecer por que as portas estão esgotando.  É possível que esteja mascarando um problema que posteriormente poderá resultar em falha.

## <a name="scale-out"></a><a name="scaleout"></a>Escala horizontal
[As portas pré-designadas](load-balancer-outbound-connections.md#preallocatedports) são atribuídas com base no tamanho do pool de back-end e agrupadas em camadas para minimizar a interrupção quando algumas das portas precisam ser realocadas para acomodar a próxima camada de tamanho do pool de back-end.  Você pode ter uma opção para aumentar a utilização da porta SNAT para um determinado frontend, dimensionando seu pool de back-end para o tamanho máximo de uma determinada camada.  Tendo em mente que a alocação de porta padrão é necessária para que o aplicativo seja expandido com eficiência sem esgotamento de SNAT de risco.

Por exemplo, duas máquinas virtuais no pool de back-end teriam 1024 portas SNAT disponíveis por configuração de IP, permitindo um total de 2048 portas SNAT para a implantação.  Se a implantação for aumentada para 50 máquinas virtuais, mesmo que o número de portas pré-alocas permaneça constante por máquina virtual, um total de portas SNAT 51.200 (50 x 1024) podem ser usadas pela implantação.  Se você quiser escalar horizontalmente sua implantação, verifique o número de [portas prealocadas](load-balancer-outbound-connections.md#preallocatedports) por camada para certificar-se de Formatar sua escala horizontal até o máximo para a respectiva camada.  No exemplo anterior, se você tivesse escolhido escalar horizontalmente para 51 em vez de 50 instâncias, você progrediria para a próxima camada e acabaria com menos portas SNAT por VM, assim como no total.

Se você escalar horizontalmente para a próxima camada de tamanho maior de pool de back-end, há potencial para que algumas das conexões de saída atinjam tempo limite se as portas alocadas precisarem ser realocadas.  Se você estiver usando apenas algumas das portas SNAT, escalar horizontalmente no próximo tamanho maior de pool de back-end é irrelevante.  Metade das portas existentes será realocada cada vez que você mover para a próxima camada de pool de back-end.  Se não desejar que isso ocorra, é necessário formatar sua implantação para o tamanho da camada.  Ou, verifique se seu aplicativo pode detectar e repita conforme necessário.  TCP keepalives podem ajudá-lo a detectar quando as portas SNAT não funcionam mais devido a terem sido realocadas.

## <a name="use-keepalives-to-reset-the-outbound-idle-timeout"></a><a name="idletimeout"></a>Usar keepalives para redefinir o tempo limite ocioso de saída
Conexões de saída têm um tempo limite de ociosidade de 4 minutos. Esse tempo limite é ajustável por meio de [Regras de saída](outbound-rules.md). Você também pode usar o transporte (por exemplo, TCP keepalives) ou keepalives da camada de aplicação para atualizar um fluxo ocioso e redefinir esse tempo limite ocioso, se necessário.  

Ao usar TCP keepalives, é suficiente habilitá-los em um lado da conexão. Por exemplo, é suficiente habilitá-las no lado do servidor apenas para redefinir o timer de ociosidade do fluxo e não é necessário para os dois lados iniciados do TCP keepalives.  Conceitos semelhantes existem para a camada de aplicativo, incluindo as configurações de cliente-servidor de banco de dados.  Verifique o lado do servidor para saber quais opções existem para keepalives específicas do aplicativo.

## <a name="next-steps"></a>Próximas etapas
Estamos sempre buscando melhorar a experiência de nossos clientes. Se você estiver tendo problemas com a conectividade de saída que não estão listadas ou resolvidas por este artigo, envie comentários por meio do GitHub na parte inferior desta página e abordaremos seus comentários assim que possível.