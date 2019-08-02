---
title: Conexões de saída no Azure (Clássico)
titlesuffix: Azure Load Balancer
description: Este artigo explica como o Azure permite que os serviços de nuvem comuniquem-se com serviços de Internet públicos.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.custom: seodec18
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/13/2018
ms.author: allensu
ms.openlocfilehash: 10af3b4838aae1565bac1d996997c117a74cedbc
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68274661"
---
# <a name="outbound-connections-classic"></a>Conexões de saída (Clássico)

O Azure fornece conectividade de saída para implantações de clientes através de vários mecanismos diferentes. Este artigo descreve quais são os cenários, quando são aplicáveis, como funcionam e como gerenciá-los.

>[!NOTE]
>Este artigo aborda somente implantações Clássicas.  Confira [Conexões de saída](load-balancer-outbound-connections.md) para saber sobre todos os cenários de implantação do Gerenciador de Recursos no Azure.

Uma implantação no Azure pode comunicar-se com os pontos de extremidade fora do Azure no espaço de endereços IP público. Quando uma instância inicia um fluxo de saída para um destino no espaço de endereços IP público, o Azure mapeia dinamicamente o endereço IP privado para um endereço IP público. Depois que esse mapeamento é criado, o tráfego de retorno para esse fluxo originado de saída também pode alcançar o endereço IP privado onde o fluxo foi originado.

O Azure usa SNAT (conversão de endereço de rede de origem) para realizar essa função. Quando vários endereços IP privados são disfarçados atrás de um único endereço IP público, o Azure usa a [PAT (conversão de endereços de porta)](#pat) para disfarçar endereços IP privados. As portas efêmeras são usadas para PAT e são [pré-alocadas](#preallocatedports) com base no tamanho do pool.

Há vários [cenários de saída](#scenarios). É possível combinar esses cenários conforme necessário. Revise-os cuidadosamente para entender os recursos, as restrições e os padrões, e como se aplicam ao seu modelo de implantação e cenário de aplicativo. Revise as diretrizes para [gerenciar esses cenários](#snatexhaust).

## <a name="scenarios"></a>Visão geral do cenário

O Azure fornece três métodos diferentes para obter implantações Clássicas de conectividade de saída.  Nem todas as implantações Clássicas têm todos os três cenários disponíveis:

| Cenário | Método | Protocolos de IP | DESCRIÇÃO | Função de trabalho da Web | IaaS | 
| --- | --- | --- | --- | --- | --- |
| [1. VM com um endereço IP em Nível de Instância](#ilpip) | SNAT, disfarce de porta não usado | TCP, UDP, ICMP, ESP | O Azure usa a máquina virtual atribuída ao IP público. A instância possui todas as portas efêmeras disponíveis. | Não | Sim |
| [2. ponto de extremidade público com balanceamento de carga](#publiclbendpoint) | SNAT com disfarce de porta (PAT) para o ponto de extremidade público | TCP, UDP | O Azure compartilha o ponto de extremidade público do endereço IP público com vários pontos de extremidade privados. O Azure usa portas efêmeras do ponto de extremidade público para PAT. | Sim | Sim |
| [3. VM autônoma](#defaultsnat) | SNAT com disfarce de porta (PAT) | TCP, UDP | O Azure designa automaticamente um endereço IP público para SNAT, compartilha esse endereço IP público com toda a implantação e usa portas efêmeras do endereço IP do ponto de extremidade público para PAT. Este é um cenário de fallback para os cenários anteriores. Não é recomendável se você precisar de visibilidade e controle. | Sim | Sim |

Este é um subconjunto da funcionalidade de conexão de saída disponível para implantações do Gerenciador de Recursos no Azure.  

Diferentes implantações no Clássico têm funcionalidade diferente:

| Implantação Clássica | Funcionalidade disponível | 
| --- | --- |
| Máquina Virtual | cenário [1](#ilpip), [2](#publiclbendpoint) ou [3](#defaultsnat) |
| Função de trabalho da Web | apenas cenário [2](#publiclbendpoint), [3](#defaultsnat) | 

[As estratégias de mitigação](#snatexhaust) também têm as mesmas diferenças.

O algoritmo utilizado para pré-alocação de portas efêmeras para implantações PAT para clássico é o mesmo que para implantações de recursos do Azure Resource Manager.

### <a name="ilpip"></a>Cenário 1: VM com um endereço IP Público no Nível de Instância

Nesse cenário, a VM tem um ILPIP (IP Público em Nível de Instância) atribuído a ela. No que diz respeito às conexões de saída, não importa se a VM tem ponto de extremidade com balanceamento de carga ou não. Esse cenário tem precedência sobre os outros. Quando um ILPIP é usado, a VM usa o ILPIP para todos os fluxos de saída.  

Um IP público atribuído a uma VM é uma relação 1:1 (em vez de 1:muitos) e implementado como sem estado 1:1 NAT.  A PAT (disfarce de porta) não é usada e a VM tem todas as portas efêmeras disponíveis para uso.

Se o aplicativo iniciar muitos fluxos de saída e for observado um esgotamento da porta SNAT, considere atribuir um [ILPIP para mitigar as restrições SNAT](#assignilpip). Revise [Gerenciar esgotamento de SNAT](#snatexhaust) completamente.

### <a name="publiclbendpoint"></a>Cenário 2: Ponto de extremidade público com balanceamento de carga

Nesse cenário, a VM ou uma função de trabalho da Web está associada a um endereço IP público por meio do ponto de extremidade com balanceamento de carga. A VM não tem um endereço IP público atribuído a ela. 

Quando a VM com balanceamento de carga cria um fluxo de saída, o Azure converte o endereço IP de origem particular do fluxo de saída para um endereço IP público do ponto de extremidade público com balanceamento de carga. O Azure usa SNAT para executar essa função. O Azure também usa [PAT](#pat) para disfarçar vários endereços IP privados por trás de um endereço IP público. 

As portas efêmeras do front-end do endereço IP público do balanceador de carga são usadas para distinguir os fluxos individuais originados pela VM. A SNAT usa dinamicamente [ortas efêmeras pré-alocadas](#preallocatedports) quando os fluxos de saída são criados. Neste contexto, as portas efêmeras usadas para SNAT são chamadas de portas SNAT.

As portas SNAT são pré-alocadas conforme descrito na seção [Entendendo SNAT e PAT](#snat). Elas são um recurso finito que pode ser esgotado. É importante entender como elas são [consumidas](#pat). Para entender como projetar para esse consumo e mitigar, conforme necessário, revise [Gerenciar esgotamento de SNAT](#snatexhaust).

Quando [vários pontos de extremidade públicos com balanceamento de carga](load-balancer-multivip.md), qualquer um desses endereços IP públicos é um candidato para fluxos de saída e um é selecionado aleatoriamente.  

### <a name="defaultsnat"></a>Cenário 3: Sem endereço IP público associado

Nesse cenário, a VM ou função de trabalho da Web não é parte de um ponto de extremidade público com balanceamento de carga.  E, no caso da VM, ela não tem um endereço ILPIP atribuído a ela. Quando a VM cria um fluxo de saída, o Azure converte o endereço IP de origem particular do fluxo de saída para um endereço IP de origem pública. O endereço IP público usado para esse fluxo de saída não é configurável e não conta para o limite de recursos IP públicos da assinatura.  O Azure automaticamente aloca esse endereço.

O Azure usa SNAT com disfarce de porta ([PAT](#pat)) para executar essa função. Esse cenário é semelhante ao cenário 2, com a diferença que não há controle sobre o endereço IP usado. Este é um cenário de fallback para quando os cenários 1 e 2 não existirem. Não é recomendável esse cenário, se você quiser controlar o endereço de saída. Se as conexões de saída são uma parte crítica do seu aplicativo, você deverá escolher outro cenário.

As portas SNAT são pré-alocadas conforme descrito na seção [Entendendo SNAT e PAT](#snat).  O número de VMs ou funções de trabalho da Web compartilhando o endereço IP público determina o número de portas efêmeras pré-alocadas.   É importante entender como elas são [consumidas](#pat). Para entender como projetar para esse consumo e mitigar, conforme necessário, revise [Gerenciar esgotamento de SNAT](#snatexhaust).

## <a name="snat"></a>Entendendo SNAT e PAT

### <a name="pat"></a>Disfarce de porta SNAT (PAT)

Quando uma implantação faz uma conexão de saída, cada fonte de conexão de saída é recriada. A fonte é recriada do espaço de endereço IP privado para o IP público associado à implantação (com base nos cenários descritos acima). No espaço de endereço IP público, as 5 tuplas do fluxo (endereço IP de origem, porta de origem, protocolo de transporte IP, endereço IP de destino, porta de destino) devem ser exclusivas.  

As portas efêmeras (portas SNAT) são usadas para conseguir isso após a regravação do endereço IP de origem privada, já que vários fluxos originam-se de um único endereço IP público. 

Uma porta SNAT é consumida por fluxo para um único endereço IP de destino, porta e protocolo. Para vários fluxos para o mesmo endereço IP, porta e protocolo de destino, cada fluxo consome uma única porta SNAT. Isso garante que os fluxos sejam exclusivos quando são originados do mesmo endereço IP público e sigam para o mesmo endereço IP, porta e protocolo de destino. 

Vários fluxos, cada um para um endereço IP, porta e protocolo de destino diferente, compartilham uma única porta SNAT. O endereço IP de destino, porta e protocolo tornam os fluxos exclusivos sem a necessidade de portas de origem adicionais para distinguir fluxos no espaço de endereço IP público.

Quando os recursos da porta SNAT estão esgotados, os fluxos de saída falham até que os fluxos existentes liberem as portas SNAT. O balanceador de carga recupera as portas SNAT quando o fluxo fecha e usa um [tempo limite de ociosidade de 4 minutos](#idletimeout) para recuperar as portas SNAT dos fluxos ociosos.

Para padrões para mitigar condições que geralmente levam ao esgotamento da porta SNAT, revise a seção [Gerenciar SNAT](#snatexhaust).

### <a name="preallocatedports"></a>Pré-alocação de porta efêmera para a porta de disfarce SNAT (PAT)

O Azure usa um algoritmo para determinar o número de portas SNAT pré-alocadas disponíveis com base no tamanho do pool do back-end ao usar a porta de disfarce SNAT ([PAT](#pat)). As portas SNAT são portas efêmeras disponíveis para um determinado endereço de origem IP público.

O Azure pré-aloca portas SNAT quando uma instância é implantada com base no número de instâncias de VM ou função de trabalho da Web que compartilham um endereço IP público específico.  Quando os fluxos de saída são criados, a [PAT](#pat) consome dinamicamente (até o limite pré-alocado) e libera essas portas quando o fluxo fecha ou ocorre tempo limite ocioso.

A tabela a seguir mostra as pré-alocações de porta SNAT para níveis de tamanhos de pool de back-end:

| Instâncias | Portas SNAT pré-alocadas por instância |
| --- | --- |
| 1-50 | 1\.024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |

Lembre-se de que o número de portas SNAT disponíveis não movem diretamente em número de fluxos. Uma única porta SNAT pode ser reutilizada para vários destinos exclusivos. As portas são consumidas apenas se for necessário fazer fluxos exclusivos. Para diretrizes de projeto e mitigação, consulte a seção sobre [como gerenciar esse recurso esgotável](#snatexhaust) e a seção que descreve a [PAT](#pat).

Alterar o tamanho da implantação pode afetar alguns dos fluxos estabelecidos. Se o tamanho do pool de back-end aumenta e faz transições para a próxima camada, metade das suas portas SNAT pré-alocadas é recuperada durante a transição para a próxima camada maior de pool de back-end. Os fluxos que estão associados a uma porta SNAT recuperada atingirão limite de tempo e deverão ser restabelecidos. Se um novo fluxo for tentado, o fluxo terá êxito imediato, desde que as portas pré-alocadas estejam disponíveis.

Se o tamanho da implantação diminuir e fizer transição para uma camada mais baixa, o número de portas SNAT disponíveis aumentará. Nesse caso, as portas SNAT alocadas existentes e seus respectivos fluxos não são afetados.

Se um serviço de nuvem for reimplantado ou alterado, a infraestrutura poderá relatar temporariamente que o pool de back-end é duas vezes maior que o real, e o Azure, por sua vez, pré-aloca menos portas SNAT por instância do que o esperado.  Isso pode aumentar temporariamente a probabilidade de esgotamento de porta SNAT. Eventualmente, o tamanho do pool fará a transição para o tamanho real e o Azure aumentará automaticamente as portas SNAT pré-alocadas para o número esperado, conforme a tabela acima.  Esse comportamento é por design e não é configurável.

As alocações de portas SNAT são o protocolo de transporte IP específico (TCP e UDP são mantidas separadamente) e são liberadas sob as seguintes condições:

### <a name="tcp-snat-port-release"></a>Liberação da porta TCP SNAT

- Se tanto o servidor/cliente enviar a porta FIN/ACK, SNAT, será liberado após 240 segundos.
- Se um RST for visto, a porta SNAT será liberada após 15 segundos.
- o tempo limite de ociosidade foi atingido

### <a name="udp-snat-port-release"></a>Liberação da porta UDP SNAT

- o tempo limite de ociosidade foi atingido

## <a name="problemsolving"></a> Solução de problemas 

Esta seção destina-se a ajudar a atenuar o esgotamento de SNAT e outros cenários que podem ocorrer com conexões de saída no Azure.

### <a name="snatexhaust"></a> Gerenciar esgotamento da porta SNAT (PAT)
[Portas efêmeras](#preallocatedports) usadas para [PAT](#pat) são um recurso esgotável, conforme descrito em [nenhum IP público associado](#defaultsnat) e [ponto de extremidade pçublico com balanceamento de carga](#publiclbendpoint).

Se você sabe que está iniciando muitas conexões TCP ou UDP de saída para o mesmo endereço e porta IP de destino, se você observa as conexões de saída com falha ou é avisado pelo suporte que as portas SNAT ([portas efêmeras](#preallocatedports) pré-alocadas usadas pela [PAT](#pat)) estão se esgotando, você terá várias opções gerais de mitigação. Avalie essas opções e decida o que está disponível e melhor para o seu cenário. É possível que uma ou mais possam ajudar a gerenciar esse cenário.

Se você tiver problemas para entender o comportamento da conexão de saída, poderá usar as estatísticas de pilha de IP (netstat). Ou pode ser útil observar comportamentos de conexão usando capturas de pacote.

#### <a name="connectionreuse"></a>Modificar o aplicativo para reutilizar conexões 
Você pode reduzir a demanda por portas efêmeras usadas para SNAT, reutilizando as conexões em sua aplicação. Isto é especialmente verdadeiro para protocolos como HTTP/1.1, onde a reutilização de conexão é a padrão. E outros protocolos que usam o HTTP como seu transporte (por exemplo, REST) podem se beneficiar por sua vez. 

A reutilização é sempre melhor que as conexões TCP individuais e atômicas para cada solicitação. A reutilização resulta em transações TCP mais eficazes e muito eficientes.

#### <a name="connection pooling"></a>Modificar o aplicativo para usar o pooling de conexão
Você pode empregar um esquema de pooling de conexão em seu aplicativo, onde as solicitações são distribuídas internamente em um conjunto de conexões fixo (cada reutilização sempre que possível). Esse esquema restringe o número de portas efêmeras em uso e cria um ambiente mais previsível. Esse esquema também pode aumentar a taxa de transferência de solicitações, permitindo múltiplas operações simultâneas quando uma única conexão está bloqueando na resposta de uma operação.  

O pooling de conexão já pode existir dentro da estrutura que você está utilizando para desenvolver o aplicativo ou as configurações para o aplicativo. É possível combinar pooling de conexão com reuso de conexão. As várias solicitações, então, consomem um número fixo e previsível de portas para o mesmo endereço e porta IP de destino. As solicitações também beneficiam-se do uso eficiente das transações TCP, reduzindo a latência e a utilização dos recursos. As transações UDP também podem beneficiar, porque o gerenciamento do número de fluxos UDP pode, por sua vez, evitar condições de esgotamento e gerenciar a utilização da porta SNAT.

#### <a name="retry logic"></a>Modificar o aplicativo para usar uma lógica de repetição menos agressiva
Quando as [portas efêmeras pré-alocadas](#preallocatedports) usadas para [PAT](#pat) estão esgotadas ou se ocorrem falhas no aplicativo, repetições de força bruta ou agressivas sem lógica de declínio e retirada fazem com que o esgotamento ocorra ou persista. Você pode reduzir a demanda de portas efêmeras usando uma lógica de repetição menos agressiva. 

As portas efêmeras têm um tempo limite de ociosidade de 4 minutos (não ajustável). Se as tentativas forem muito agressivas, o esgotamento não terá oportunidade de limpar por conta própria. Portanto, considerando como -- e com que frequência -- o aplicativo reage transações é uma parte crítica do projeto.

#### <a name="assignilpip"></a>Atribuir um IP Público em Nível de Instância a cada VM
Atribuir um ILPIP altera seu cenário para [IP Público em Nível de Instância para uma VM](#ilpip). Todas as portas efêmeras do IP público que são usadas para cada VM estão disponíveis para a VM. (Em oposição aos cenários em que as portas efêmeras de um IP público são compartilhadas com todas as VMs associadas à respectiva implantação.) Há compromissos a considerar, tais como o potencial impacto da listagem de permissões de um grande número de endereços IP individuais.

>[!NOTE] 
>Essa opção não está disponível para as funções de trabalho da Web.

### <a name="idletimeout"></a>Usar keepalives para redefinir o tempo limite ocioso de saída

Conexões de saída têm um tempo limite de ociosidade de 4 minutos. Esse tempo limite não é ajustado. No entanto, você pode usar o transporte (por exemplo, TCP keepalives) ou keepalives da camada de aplicação para atualizar um fluxo ocioso e redefinir esse tempo limite ocioso, se necessário.  Verifique com o fornecedor de qualquer pacote de software se há suporte para isso ou como habilitá-lo.  Geralmente, apenas um lado precisa gerar keepalives para redefinir o tempo limite de ociosidade. 

## <a name="discoveroutbound"></a>Descobrir o IP público que usa uma VM
Há várias maneiras de determinar o endereço IP público de uma conexão de saída. O OpenDNS fornece um serviço que pode mostrar o endereço IP público de sua VM. 

Usando o comando nslookup, você pode enviar uma consulta DNS para o nome myip.opendns.com para o resolvedor do OpenDNS. O serviço retornará o endereço IP de origem que foi usado para enviar a consulta. Quando você executa a consulta a seguir de sua VM, a resposta será o IP público usado para essa VM:

    nslookup myip.opendns.com resolver1.opendns.com


## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [Load Balancer](load-balancer-overview.md) usado nas implantações do Gerenciador de Recursos.
- Saiba mais sobre os cenários de [conexão saída](load-balancer-outbound-connections.md) disponíveis em implantações do Gerenciador de Recursos.
