---
title: Conexões de saída no Azure
titlesuffix: Azure Load Balancer
description: Este artigo explica como o Azure permite que as VMs comuniquem-se com serviços de Internet públicos.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.custom: seodec18
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/02/2019
ms.author: allensu
ms.openlocfilehash: 833d0d0b17f7cc22b2ab37b4e225c1a8cce9c592
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385554"
---
# <a name="outbound-connections-in-azure"></a>Conexões de saída no Azure

O Azure fornece conectividade de saída para implantações de clientes através de vários mecanismos diferentes. Este artigo descreve quais são os cenários, quando são aplicáveis, como funcionam e como gerenciá-los.

>[!NOTE] 
>Este artigo aborda apenas as implantações do Gerenciador de Recursos. Confira as [Conexões de saída (Clássico)](load-balancer-outbound-connections-classic.md) para saber sobre todos os cenários de implantação Clássicos no Azure.

Uma implantação no Azure pode comunicar-se com os pontos de extremidade fora do Azure no espaço de endereços IP público. Quando uma instância inicia um fluxo de saída para um destino no espaço de endereços IP público, o Azure mapeia dinamicamente o endereço IP privado para um endereço IP público. Depois que esse mapeamento é criado, o tráfego de retorno para esse fluxo originado de saída também pode alcançar o endereço IP privado onde o fluxo foi originado.

O Azure usa SNAT (conversão de endereço de rede de origem) para realizar essa função. Quando vários endereços IP privados são disfarçados atrás de um único endereço IP público, o Azure usa a [PAT (conversão de endereços de porta)](#pat) para disfarçar endereços IP privados. As portas efêmeras são usadas para PAT e são [pré-alocadas](#preallocatedports) com base no tamanho do pool.

Há vários [cenários de saída](#scenarios). É possível combinar esses cenários conforme necessário. Revise-os cuidadosamente para entender os recursos, as restrições e os padrões, e como se aplicam ao seu modelo de implantação e cenário de aplicativo. Revise as diretrizes para [gerenciar esses cenários](#snatexhaust).

>[!IMPORTANT] 
>O Standard Load Balancer e o IP Público Standard apresentam novas habilidades e comportamentos diferentes da conectividade de saída.  Eles não são o mesmo que SKUs Básicos.  Se quiser ter conectividade de saída ao trabalhar com SKUs Standard, você precisará defini-la explicitamente com endereços IP públicos Standard ou com o Load Balancer Standard público.  Isso inclui a criação de conectividade de saída ao usar um Standard Load Balancer interno.  É recomendável que você sempre use regras de saída em um Load Balancer Standard público.  O [Cenário 3](#defaultsnat) não está disponível com o SKU Standard.  Isso significa que, quando um Standard Load Balancer for usado, você precisará executar etapas para criar a conectividade de saída para as VMs no pool de back-end se a conectividade de saída for desejada.  No contexto da conectividade de saída, uma única VM autônoma, todas as VMs em um conjunto de disponibilidade, todas as instâncias em um VMSS se comportam como um grupo. Isso significa que, se uma única VM em um conjunto de disponibilidade estiver associada a um SKU Standard, todas as instâncias de VM neste conjunto de disponibilidade agora se comportarão de acordo com as mesmas regras que se aplicariam se elas estivessem associados ao SKU Standard, mesmo se uma instância individual não estivesse diretamente associada a ele.  Revise atentamente todo este documento para entender os conceitos gerais, revise [Standard Load Balancer](load-balancer-standard-overview.md) para ver as diferenças entre as SKUs e revise [regras de saída](load-balancer-outbound-rules-overview.md).  Usar regras de saída permite ter um controle refinado sobre todos os aspectos da conectividade de saída.

## <a name="scenarios"></a>Visão geral do cenário

O Azure Load Balancer e os recursos relacionados são explicitamente definidos ao utilizar o [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).  Atualmente, o Azure fornece três métodos diferentes para alcançar a conectividade de saída para recursos do Azure Resource Manager. 

| SKUs | Cenário | Método | Protocolos IP | Descrição |
| --- | --- | --- | --- | --- |
| Standard, Básico | [1. VM com endereço IP público (com ou sem Load Balancer)](#ilpip) | SNAT, disfarce de porta não usado | TCP, UDP, ICMP, ESP | O Azure usa o IP público atribuído à configuração de IP do NIC da instância. A instância possui todas as portas efêmeras disponíveis. Ao usar o Standard Load Balancer, você precisa usar [regras de saída](load-balancer-outbound-rules-overview.md) para definir explicitamente a conectividade de saída |
| Standard, Básico | [2. Load Balancer públicos associados a uma VM (nenhum endereço IP público na instância)](#lb) | SNAT com PAT (disfarce de porta) usando front-ends do Load Balancer | TCP, UDP |O Azure compartilha o endereço IP público dos front-ends do Load Balancer público com vários endereços IP privados. O Azure usa os portas efêmeras dos front-ends para PAT. |
| Nenhuma ou Básico | [3. VM autônoma (sem Load Balancer, nenhum endereço IP público)](#defaultsnat) | SNAT com disfarce de porta (PAT) | TCP, UDP | O Azure designa automaticamente um endereço IP público para SNAT, compartilha esse endereço IP público com vários endereços IP privados do conjunto de disponibilidade e usa portas efêmeras desse endereço IP público. Esse cenário é um fallback para os cenários anteriores. Não é recomendável se você precisar de visibilidade e controle. |

Se você não quiser que uma VM comunique-se com os pontos de extremidade fora do Azure no espaço de endereço IP público, poderá usar NSGs (grupos de segurança de rede) para bloquear o acesso conforme necessário. A seção [Impedir conectividade de saída](#preventoutbound) descreve sobre os NSGs mais detalhadamente. As diretrizes sobre a projeto, implementação e gerenciamento de uma rede virtual sem qualquer acesso de saída estão fora do escopo deste artigo.

### <a name="ilpip"></a>Cenário 1: VM com endereço IP público

Nesse cenário, a VM tem um IP público atribuído a ela. No que diz respeito às conexões de saída, não importa se a VM é com balanceamento de carga ou não. Esse cenário tem precedência sobre os outros. Quando um endereço IP público é usado, a VM usa o endereço IP público para todos os fluxos de saída.  

Um IP público atribuído a uma VM é uma relação 1:1 (em vez de 1:muitos) e implementado como sem estado 1:1 NAT.  A PAT (disfarce de porta) não é usada e a VM tem todas as portas efêmeras disponíveis para uso.

Se seu aplicativo iniciar muitos fluxos de saída e você enfrentar esgotamento de porta SNAT, considere atribuir um [endereço IP público para atenuar as restrições SNAT](#assignilpip). Revise [Gerenciar esgotamento de SNAT](#snatexhaust) completamente.

### <a name="lb"></a>Cenário 2: VM com balanceamento de carga sem um endereço IP público

Nesse cenário, a VM faz parte de um pool de back-end do balanceador de carga público. A VM não tem um endereço IP público atribuído a ela. O recurso do Load Balancer deve ser configurado com uma regra de balanceador de carga para criar um link entre o front-end de IP público e o pool de back-end.

Se você não concluir essa configuração de regra, o comportamento será conforme descrito no cenário para [VM autônoma sem IP público](#defaultsnat). Não é necessário que a regra tenha um ouvinte trabalhando no pool de back-end ou a investigação de integridade para ter êxito.

Quando a VM com balanceamento de carga cria um fluxo de saída, o Azure converte o endereço IP de origem particular do fluxo de saída para um endereço IP público do frontend do Balanceador de Carga público. O Azure usa SNAT para executar essa função. O Azure também usa [PAT](#pat) para disfarçar vários endereços IP privados por trás de um endereço IP público. 

As portas efêmeras do front-end do endereço IP público do balanceador de carga são usadas para distinguir os fluxos individuais originados pela VM. A SNAT usa dinamicamente [ortas efêmeras pré-alocadas](#preallocatedports) quando os fluxos de saída são criados. Neste contexto, as portas efêmeras usadas para SNAT são chamadas de portas SNAT.

As portas SNAT são pré-alocadas, conforme descrito na seção [Entendendo SNAT e PAT](#snat). Elas são um recurso finito que pode ser esgotado. É importante entender como elas são [consumidas](#pat). Para entender como projetar para esse consumo e mitigar, conforme necessário, revise [Gerenciar esgotamento de SNAT](#snatexhaust).

Quando [vários endereços IP (públicos) estão associados ao Load Balancer Básico](load-balancer-multivip-overview.md), qualquer um desses endereços IP públicos é um candidato para fluxos de saída e um é selecionado aleatoriamente.  

Para monitorar a integridade das conexões de saída com o Load Balancer Basic, você pode usar [logs de Azure monitor para Load Balancer](load-balancer-monitor-log.md) e [logs de eventos de alerta](load-balancer-monitor-log.md#alert-event-log) para monitorar mensagens de esgotamento de porta SNAT.

### <a name="defaultsnat"></a>Cenário 3: VM autônoma sem um endereço IP público

Nesse cenário, a VM não faz parte de um pool de Load Balancer público (e não faz parte de um pool de Standard Load Balancer interno) e não tem um endereço IP público atribuído a ele. Quando a VM cria um fluxo de saída, o Azure converte o endereço IP de origem particular do fluxo de saída para um endereço IP de origem pública. O endereço IP público usado para esse fluxo de saída não é configurável e não conta para o limite de recursos IP públicos da assinatura. Esse endereço IP público não pertence a você e não pode ser reservado. Se você reimplantar o conjunto de dimensionamento de VMs ou conjuntos de disponibilidade ou máquinas virtuais, esse endereço IP público será liberado e um novo endereço IP público será solicitado. Não use esse cenário para endereços IP de lista de permissões. Em vez disso, use um dos outros dois cenários para declarar explicitamente o cenário de saída e o endereço IP público a ser usado para conectividade de saída.

>[!IMPORTANT] 
>Esse cenário também será aplicável quando __somente__ um Load Balancer Básico interno estiver conectado. O cenário 3 será __não disponível__ quando um Load Balancer Standard interno for anexado a uma VM.  É necessário criar explicitamente o [cenário 1](#ilpip) ou [cenário 2](#lb), além de usar um Load Balancer Standard interno.

O Azure usa SNAT com disfarce de porta ([PAT](#pat)) para executar essa função. Esse cenário é semelhante ao [cenário 2](#lb), exceto que não há controle sobre o endereço IP usado. Este é um cenário de fallback para quando os cenários 1 e 2 não existirem. Não é recomendável esse cenário, se você quiser controlar o endereço de saída. Se as conexões de saída são uma parte crítica do seu aplicativo, você deve escolher outro cenário.

As portas SNAT são pré-alocadas conforme descrito na seção [Entendendo SNAT e PAT](#snat).  O número de VMs que compartilham um Conjunto de Disponibilidade determina qual camada de pré-alocação se aplica.  Uma VM autônoma sem um Conjunto de Disponibilidade é efetivamente um pool de 1 para fins de determinação de pré-alocação (portas de SNAT 1024). As portas SNAT são um recurso finito que pode ser esgotado. É importante entender como elas são [consumidas](#pat). Para entender como projetar para esse consumo e mitigar, conforme necessário, revise [Gerenciar esgotamento de SNAT](#snatexhaust).

### <a name="combinations"></a>Cenários combinados, vários

É possível combinar os cenários descritos nas seções anteriores para alcançar um resultado específico. Quando vários cenários estão presentes, uma ordem de precedência se aplica: [cenário 1](#ilpip) tem precedência sobre o [cenário 2](#lb) e [3](#defaultsnat). [Cenário 2](#lb) substitui [cenário 3](#defaultsnat).

Um exemplo é uma implantação do Azure Resource Manager onde o aplicativo depende muito das conexões de saída para um número limitado de destinos, mas também recebe fluxos de entrada em um front-end do balanceador de carga. Nesse caso, é possível combinar cenários 1 e 2 por segurança. Para padrões adicionais, revise [Gerenciar esgotamento de SNAT](#snatexhaust).

### <a name="multife"></a> Vários front-ends para fluxos de saída

#### <a name="standard-load-balancer"></a>Standard Load Balancer

O Standard Load Balancer usa todos os candidatos para fluxos de saída ao mesmo tempo em que [vários IP (públicos) de front-ends](load-balancer-multivip-overview.md) estão presentes. Cada interface multiplicará o número de portas de SNAT pré-alocadas disponíveis, se uma regra de balanceamento de carga estiver habilitada para conexões de saída.

É possível impedir que um endereço IP de front-end seja utilizado para conexões de saída com uma nova opção de regra de balanceamento de carga:

```json    
      "loadBalancingRules": [
        {
          "disableOutboundSnat": false
        }
      ]
```

Normalmente, a opção `disableOutboundSnat` é padronizada como _falso_ e significa que essa regra programa o SNAT de saída para as VMs associadas no pool de back-end da regra de balanceamento de carga. O `disableOutboundSnat` pode ser alterado para _verdadeiro_ para evitar que o Load Balancer use o endereço IP do frontend associado para conexões de saída para as VMs no pool de back-end dessa regra de balanceamento de carga.  Além disso, você também pode designar um endereço IP específico para os fluxos de saída, conforme descrito em [Cenários múltiplos e combinados](#combinations).

#### <a name="load-balancer-basic"></a>Load Balancer Básico

O Load Balancer Basic escolhe um único front-end para ser utilizado em fluxos de saída quando [vários front-ends de IP (público)](load-balancer-multivip-overview.md) forem candidatos para fluxos de saída. Essa seleção não é configurável e você deverá considerar o algoritmo de seleção como aleatório. Você pode designar um endereço IP específico para a saída, conforme descrito em [Cenários múltiplos e combinados](#combinations).

### <a name="az"></a> Zonas de Disponibilidades

Ao usar o [Load Balancer Standard com Zonas de Disponibilidade](load-balancer-standard-availability-zones.md), os front-ends com redundância de zona podem fornecer conexões de SNAT de saída com redundância de zona e a programação de SNAT sobrevive à falha da zona.  Quando front-ends zonais são utilizados, as conexões de SNAT de saída compartilham o destino com a zona à qual pertencem.

## <a name="snat"></a>Entendendo SNAT e PAT

### <a name="pat"></a>Disfarce de porta SNAT (PAT)

Quando um recurso público do Load Balancer estiver associado a instâncias VM, cada fonte de conexão de saída será reescrita. A origem é regravada do espaço do endereço IP privado da rede virtual para o endereço IP Público de front-end do balanceador de carga. No espaço de endereço IP público, as 5 tuplas do fluxo (endereço IP de origem, porta de origem, protocolo de transporte IP, endereço IP de destino, porta de destino) devem ser exclusivas.  O SNAT simulado de porta pode ser usado com protocolos TCP ou IP UDP.

As portas efêmeras (portas SNAT) são usadas para conseguir isso após a regravação do endereço IP de origem privada, já que vários fluxos originam-se de um único endereço IP público. O algoritmo de SNAT de disfarce de porta aloca as portas SNAT de forma diferente para UDP e TCP.

#### <a name="tcp"></a>Portas SNAT TCP

Uma porta SNAT é consumida por fluxo para um único endereço IP e porta de destino. Para vários fluxos TCP para o mesmo endereço IP, porta e protocolo de destino, cada fluxo TCP consome uma única porta SNAT. Isso garante que os fluxos sejam exclusivos quando são originados do mesmo endereço IP público e sigam para o mesmo endereço IP, porta e protocolo de destino. 

Vários fluxos, cada um para um endereço IP, porta e protocolo de destino diferente, compartilham uma única porta SNAT. O endereço IP de destino, porta e protocolo tornam os fluxos exclusivos sem a necessidade de portas de origem adicionais para distinguir fluxos no espaço de endereço IP público.

#### <a name="udp"></a>Portas SNAT UDP

Portas SNAT UDP são gerenciadas por um algoritmo diferente do que as portas SNAT TCP.  O Load Balancer usa um algoritmo conhecido como "NAT de cone com restrição de porta" para UDP.  Uma porta SNAT é consumida para cada fluxo, independentemente do endereço IP e da porta de destino.

#### <a name="exhaustion"></a>Esgotamento

Quando os recursos da porta SNAT estão esgotados, os fluxos de saída falham até que os fluxos existentes liberem as portas SNAT. O balanceador de carga recupera as portas SNAT quando o fluxo fecha e usa um [tempo limite de ociosidade de 4 minutos](#idletimeout) para recuperar as portas SNAT dos fluxos ociosos.

Portas SNAT UDP geralmente esgotam-se muito mais rapidamente que as portas SNAT TCP devido à diferença no algoritmo usado. Você deve projetar e realizar os testes de escala com essa diferença em mente.

Para padrões para mitigar condições que geralmente levam ao esgotamento da porta SNAT, revise a seção [Gerenciar SNAT](#snatexhaust).

### <a name="preallocatedports"></a>Pré-alocação de porta efêmera para a porta de disfarce SNAT (PAT)

O Azure usa um algoritmo para determinar o número de portas SNAT pré-alocadas disponíveis com base no tamanho do pool do back-end ao usar a porta de disfarce SNAT ([PAT](#pat)). As portas SNAT são portas efêmeras disponíveis para um determinado endereço de origem IP público.

O mesmo número de portas SNAT é pré-alocado respectivamente para TCP e UDP e independentemente consumido por protocolo de transporte IP.  No entanto, o uso da porta SNAT é diferente dependendo de o fluxo ser UDP ou TCP.

>[!IMPORTANT]
>Programação SNAT de SKU Padrão por protocolo de transporte IP e derivada da regra de balanceamento de carga.  Se houver uma regra de balanceamento de carga TCP, apenas SNAT só estará disponível para TCP. Se você tiver apenas uma regra balanceamento de carga TCP e precisar de SNAT de saída para UDP, crie uma regra UDP do mesmo front-end para o mesmo pool de back-end de balanceamento de carga.  Isso vai ativar a programação SNAT para UDP.  Não é necessário uma regra de trabalho ou investigação de integridade.  A SNAT do SKU básico sempre programa a SNAT para ambos os protocolo de transporte IP, independentemente do protocolo de transporte especificado na regra de balanceamento de carga.

O Azure pré-aloca as portas SNAT para a configuração IP do NIC de cada VM. Quando uma configuração IP é adicionada ao pool, as portas SNAT são pré-atribuídas para essa configuração de IP com base no tamanho do pool do back-end. Quando os fluxos de saída são criados, a [PAT](#pat) consome dinamicamente (até o limite pré-alocado) e libera essas portas quando o fluxo fecha ou ocorre [tempo limite ocioso](#idletimeout).

A tabela a seguir mostra as pré-alocações de porta SNAT para níveis de tamanhos de pool de back-end:

| Tamanho do pool (instâncias VM) | Portas SNAT pré-alocadas por configuração de IP|
| --- | --- |
| 1-50 | 1\.024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801-1,000 | 32 |

>[!NOTE]
> Ao utilizar o Standard Load Balancer com [vários front-ends](load-balancer-multivip-overview.md), cada endereço IP de front-end multiplica o número de portas de SNAT disponíveis na tabela anterior. Por exemplo, um conjunto de back-end de 50 VMs com 2 regras de balanceamento de carga, cada uma com um endereço IP front-end separado, usará 2048 (2x 1024) portas SNAT por configuração de IP. Consulte os detalhes para [vários front-ends](#multife).

Lembre-se de que o número de portas SNAT disponíveis não movem diretamente em número de fluxos. Uma única porta SNAT pode ser reutilizada para vários destinos exclusivos. As portas são consumidas apenas se for necessário fazer fluxos exclusivos. Para diretrizes de projeto e mitigação, consulte a seção sobre [como gerenciar esse recurso esgotável](#snatexhaust) e a seção que descreve a [PAT](#pat).

Alterar o tamanho do pool de back-end pode afetar alguns dos fluxos estabelecidos. Se o tamanho do pool de back-end aumenta e faz transições para a próxima camada, metade das suas portas SNAT pré-alocadas é recuperada durante a transição para a próxima camada maior de pool de back-end. Os fluxos que estão associados a uma porta SNAT recuperada atingirão limite de tempo e deverão ser restabelecidos. Se um novo fluxo for tentado, o fluxo terá êxito imediato, desde que as portas pré-alocadas estejam disponíveis.

Se o tamanho do pool de back-end diminuir e fizer transição para uma camada mais baixa, o número de portas SNAT disponíveis aumentará. Nesse caso, as portas SNAT alocadas existentes e seus respectivos fluxos não são afetados.

As alocações de portas SNAT são o protocolo de transporte IP específico (TCP e UDP são mantidas separadamente) e são liberadas sob as seguintes condições:

### <a name="tcp-snat-port-release"></a>Liberação da porta TCP SNAT

- Se o servidor/cliente enviar FINACK, a porta SNAT será liberada após 240 segundos.
- Se um RST for visto, a porta SNAT será liberada após 15 segundos.
- Se o tempo limite de ociosidade tiver sido atingido, a porta será liberada.

### <a name="udp-snat-port-release"></a>Liberação da porta UDP SNAT

- Se o tempo limite de ociosidade tiver sido atingido, a porta será liberada.

## <a name="problemsolving"></a> Solução de problemas 

Esta seção destina-se a ajudar a mitigar o esgotamento de SNAT e que pode ocorrer com conexões de saída no Azure.

### <a name="snatexhaust"></a> Gerenciar esgotamento da porta SNAT (PAT)
[As portas efêmeras](#preallocatedports) usadas para [Pat](#pat) são um recurso esse esgotável, conforme descrito em [VM autônoma sem um endereço IP público](#defaultsnat) e [VM com balanceamento de carga sem um endereço IP público](#lb).

Se você sabe que está iniciando muitas conexões TCP ou UDP de saída para o mesmo endereço e porta IP de destino, se você observa as conexões de saída com falha ou é avisado pelo suporte que as portas SNAT ([portas efêmeras](#preallocatedports) pré-alocadas usadas pela [PAT](#pat)) estão se esgotando, você terá várias opções gerais de mitigação. Avalie essas opções e decida o que está disponível e melhor para o seu cenário. É possível que uma ou mais possam ajudar a gerenciar esse cenário.

Se você tiver problemas para entender o comportamento da conexão de saída, poderá usar as estatísticas de pilha de IP (netstat). Ou pode ser útil observar comportamentos de conexão usando capturas de pacote. Você pode executar essas capturas de pacotes no sistema operacional convidado da sua instância ou usar o [Observador de Rede para captura de pacote](../network-watcher/network-watcher-packet-capture-manage-portal.md).

#### <a name="connectionreuse"></a>Modificar o aplicativo para reutilizar conexões 
Você pode reduzir a demanda por portas efêmeras usadas para SNAT, reutilizando as conexões em sua aplicação. Isto é especialmente verdadeiro para protocolos como HTTP/1.1, onde a reutilização de conexão é a padrão. E outros protocolos que usam o HTTP como seu transporte (por exemplo, REST) podem se beneficiar por sua vez. 

A reutilização é sempre melhor que as conexões TCP individuais e atômicas para cada solicitação. A reutilização resulta em transações TCP mais eficazes e muito eficientes.

#### <a name="connection pooling"></a>Modificar o aplicativo para usar o pooling de conexão
Você pode empregar um esquema de pooling de conexão em seu aplicativo, onde as solicitações são distribuídas internamente em um conjunto de conexões fixo (cada reutilização sempre que possível). Esse esquema restringe o número de portas efêmeras em uso e cria um ambiente mais previsível. Esse esquema também pode aumentar a taxa de transferência de solicitações, permitindo múltiplas operações simultâneas quando uma única conexão está bloqueando na resposta de uma operação.  

O pooling de conexão já pode existir dentro da estrutura que você está utilizando para desenvolver o aplicativo ou as configurações para o aplicativo. É possível combinar pooling de conexão com reuso de conexão. As várias solicitações, então, consomem um número fixo e previsível de portas para o mesmo endereço e porta IP de destino. As solicitações também beneficiam-se do uso eficiente das transações TCP, reduzindo a latência e a utilização dos recursos. As transações UDP também podem beneficiar, porque o gerenciamento do número de fluxos UDP pode, por sua vez, evitar condições de esgotamento e gerenciar a utilização da porta SNAT.

#### <a name="retry logic"></a>Modificar o aplicativo para usar uma lógica de repetição menos agressiva
Quando as [portas efêmeras pré-alocadas](#preallocatedports) usadas para [PAT](#pat) estão esgotadas ou se ocorrem falhas no aplicativo, repetições de força bruta ou agressivas sem lógica de declínio e retirada fazem com que o esgotamento ocorra ou persista. Você pode reduzir a demanda de portas efêmeras usando uma lógica de repetição menos agressiva. 

As portas efêmeras têm um tempo limite de ociosidade de 4 minutos (não ajustável). Se as tentativas forem muito agressivas, o esgotamento não terá oportunidade de limpar por conta própria. Portanto, considerando como -- e com que frequência -- o aplicativo reage transações é uma parte crítica do projeto.

#### <a name="assignilpip"></a>Atribuir um IP público a cada VM
A atribuição de um endereço IP público altera seu cenário para o [IP público para uma VM](#ilpip). Todas as portas efêmeras do IP público que são usadas para cada VM estão disponíveis para a VM. (Em oposição aos cenários em que as portas efêmeras de um IP público são compartilhadas com todas as VMs associadas ao respectivo grupo de back-end.) Há compromissos a considerar, tais como o custo adicional dos endereços IP públicos e o potencial impacto da listagem de permissões de um grande número de endereços IP individuais.

>[!NOTE] 
>Essa opção não está disponível para as funções de trabalho da Web.

#### <a name="multifesnat"></a>Utilizar vários front-ends

Ao utilizar o Load Balancer Standard púbico, você atribui [vários endereços IP de front-end para conexões de saída](#multife) e [multiplica o número de portas de SNAT disponíveis](#preallocatedports).  Crie uma configuração IP front-end, uma regra e um pool de back-end para acionar a programação do SNAT para o IP público do frontend.  A regra não precisa funcionar e uma análise de integridade não precisa ter êxito.  Se você usar vários frontends para a entrada também (em vez de apenas para saída), use bem os probes de integridade personalizados para garantir a confiabilidade.

>[!NOTE]
>Na maioria dos casos, o esgotamento das portas de SNAT é um sinal de design inapropriado.  Antes de utilizar mais front-ends para adicionar portas de SNAT, certifique-se de reconhecer por que as portas estão esgotando.  É possível que esteja mascarando um problema que posteriormente poderá resultar em falha.

#### <a name="scaleout"></a>Expansão

[As portas pré-designadas](#preallocatedports) são atribuídas com base no tamanho do pool de back-end e agrupadas em camadas para minimizar a interrupção quando algumas das portas precisam ser realocadas para acomodar a próxima camada de tamanho do pool de back-end.  Você pode ter a opção de aumentar a intensidade da utilização da porta SNAT para um determinado frontend, dimensionando o pool de back-end para o tamanho máximo de um determinado nível.  Isso requer que o aplicativo dimensione com eficiência.

Por exemplo, duas máquinas virtuais no pool de back-end teriam 1024 portas SNAT disponíveis por configuração de IP, permitindo um total de 2048 portas SNAT para a implantação.  Se a implantação for aumentada para 50 máquinas virtuais, mesmo que o número de portas pré-alocas permaneça constante por máquina virtual, um total de portas SNAT 51.200 (50 x 1024) podem ser usadas pela implantação.  Se você deseja expandir sua implantação, verifique o número de [portas pré-alocadas](#preallocatedports) por nível para certificar-se que você dimensionou ao máximo para a camada respectiva.  No exemplo anterior, se você tivesse escolhido dimensionar para 51 em vez de 50 instâncias, você progrediria para a próxima camada e acabaria com menos portas SNAT por VM, assim como no total.

Se você escalar horizontalmente para a próxima camada de tamanho maior de pool de back-end, há potencial para que algumas das conexões de saída atinjam tempo limite se as portas alocadas precisarem ser realocadas.  Se você estiver usando apenas algumas das portas SNAT, escalar horizontalmente no próximo tamanho maior de pool de back-end é irrelevante.  Metade das portas existentes será realocada cada vez que você mover para a próxima camada de pool de back-end.  Se não desejar que isso ocorra, é necessário formatar sua implantação para o tamanho da camada.  Ou, verifique se seu aplicativo pode detectar e repita conforme necessário.  TCP keepalives podem ajudá-lo a detectar quando as portas SNAT não funcionam mais devido a terem sido realocadas.

### <a name="idletimeout"></a>Usar keepalives para redefinir o tempo limite ocioso de saída

Conexões de saída têm um tempo limite de ociosidade de 4 minutos. Esse tempo limite não é ajustado. No entanto, você pode usar o transporte (por exemplo, TCP keepalives) ou keepalives da camada de aplicação para atualizar um fluxo ocioso e redefinir esse tempo limite ocioso, se necessário.  

Ao usar TCP keepalives, é suficiente habilitá-los em um lado da conexão. Por exemplo, é suficiente habilitá-las no lado do servidor apenas para redefinir o timer de ociosidade do fluxo e não é necessário para os dois lados iniciados do TCP keepalives.  Conceitos semelhantes existem para a camada de aplicativo, incluindo as configurações de cliente-servidor de banco de dados.  Verifique o lado do servidor para as opções existentes para o aplicativo keepalives específico.

## <a name="discoveroutbound"></a>Descobrir o IP público que usa uma VM
Há várias maneiras de determinar o endereço IP público de uma conexão de saída. O OpenDNS fornece um serviço que pode mostrar o endereço IP público de sua VM. 

Usando o comando nslookup, você pode enviar uma consulta DNS para o nome myip.opendns.com para o resolvedor do OpenDNS. O serviço retornará o endereço IP de origem que foi usado para enviar a consulta. Quando você executa a consulta a seguir de sua VM, a resposta será o IP público usado para essa VM:

    nslookup myip.opendns.com resolver1.opendns.com

## <a name="preventoutbound"></a>Impedir conectividade de saída
Às vezes, não é desejável que uma VM tenha permissão para criar um fluxo de saída. Ou pode haver um requisito para gerenciar quais destinos podem ser alcançados com os fluxos de saída ou quais destinos podem começar os fluxos de entrada. Nesse caso, é possível usar os [grupos de segurança de rede](../virtual-network/security-overview.md) para gerenciar os destinos que a VM pode acessar. Você também pode usar NSGs para gerenciar qual destino público pode iniciar os fluxos de entrada.

Ao aplicar um NSG a uma VM com balanceamento de carga, atente-se às [marcas de serviço](../virtual-network/security-overview.md#service-tags) e às [regras de segurança padrão](../virtual-network/security-overview.md#default-security-rules). Certifique-se de que a VM possa receber solicitações de investigação de integridade do Azure Load Balancer. 

Se um NSG bloquear solicitações de investigação de integridade da marcação padrão AZURE_LOADBALANCER, o teste de integridade da VM falhará e a VM será reduzida. O Balanceador de Carga interrompe o envio de novos fluxos para a VM.

## <a name="limitations"></a>Limitações
- DisableOutboundSnat não está disponível como uma opção ao configurar uma regra de balanceamento de carga no portal.  Ao invés disso, utilize ferramentas de cliente, modelo ou REST.
- Funções de trabalho sem uma rede virtual e outros serviços da plataforma Microsoft podem ser acessados quando apenas um Standard Load Balancer interno é usado devido a um efeito colateral de como os serviços pré-VNet e outros serviços da plataforma funcionam. Não confie neste efeito colateral como o próprio serviço em si ou a plataforma subjacente pode mudar sem aviso prévio. Você sempre deve supor que precisa criar conectividade de saída explicitamente se desejado ao usar apenas um Standard Load Balancer interno. O cenário [SNAT padrão](#defaultsnat) 3 descrito neste artigo não está disponível.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [Load Balancer Standard](load-balancer-standard-overview.md).
- Saiba mais sobre as [regras de saída](load-balancer-outbound-rules-overview.md) para o Load Balancer Standard público.
- Saiba mais sobre o [Azure Load Balancer](load-balancer-overview.md).
- Saiba mais sobre [grupos de segurança de rede](../virtual-network/security-overview.md).
- Saiba mais sobre alguns dos outros principais [recursos de rede](../networking/networking-overview.md) no Azure.
