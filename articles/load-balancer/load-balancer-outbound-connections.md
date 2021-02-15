---
title: SNAT para conexões de saída
description: Descreve como Azure Load Balancer é usado para executar SNAT para conectividade de Internet de saída
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: conceptual
ms.custom: contperf-fy21q1
ms.date: 10/13/2020
ms.author: allensu
ms.openlocfilehash: d1632c66791dd5e697b95a2c5aaaddea81629abf
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99052815"
---
# <a name="using-snat-for-outbound-connections"></a>Usando SNAT para conexões de saída

Os IPs de front-end de um balanceador de carga público do Azure podem ser usados para fornecer conectividade de saída à Internet para instâncias de back-end. Essa configuração usa a **conversão de endereços de rede (SNAT) de origem**. O SNAT reescreve o endereço IP do back-end para o endereço IP público do balanceador de carga. 

O SNAT permite a **representação de IP** da instância de back-end. Essa representação impede que fontes externas tenham um endereço direto para as instâncias de back-end. O compartilhamento de um endereço IP entre instâncias de back-end reduz o custo de IPs públicos estáticos e dá suporte a cenários como a simplificação de listas de permissões de IP com tráfego de IPs públicos conhecidos. 

>[!Note]
> Para aplicativos que exigem um grande número de conexões de saída ou clientes empresariais que exigem um único conjunto de IPs a serem usados de uma determinada rede virtual, o [NAT da rede virtual](../virtual-network/nat-overview.md) é a solução recomendada. A alocação dinâmica permite uma configuração simples e > o uso mais eficiente de portas SNAT de cada endereço IP. Ele também permite que todos os recursos na rede virtual compartilhem um conjunto de endereços IP sem a necessidade de compartilharem > um balanceador de carga.

>[!Important]
> Mesmo sem o SNAT de saída configurado, as contas de armazenamento do Azure na mesma região ainda estarão acessíveis e os recursos de back-end ainda terão acesso aos serviços da Microsoft, como atualizações do Windows.

>[!NOTE] 
>Este artigo aborda apenas implantações Azure Resource Managers. Confira as [Conexões de saída (Clássico)](/previous-versions/azure/load-balancer/load-balancer-outbound-connections-classic) para saber sobre todos os cenários de implantação Clássicos no Azure.

## <a name="sharing-frontend-ip-address-across-backend-resources"></a><a name ="snat"></a> Compartilhamento de endereço IP de front-end entre recursos de back-end

Se os recursos de back-end de um balanceador de carga não tiverem endereços ILPIP (IP público em nível de instância), eles estabelecerão a conectividade de saída por meio do IP de front-end do balanceador de carga público. As portas são usadas para gerar identificadores exclusivos usados para manter fluxos distintos. A Internet usa uma cinco tuplas para fornecer essa distinção.

A cinco tuplas consiste em:

* IP de destino
* Porta de destino
* IP de origem
* Porta de origem e protocolo para fornecer essa distinção.

Se uma porta for usada para conexões de entrada, ela terá um **ouvinte** para solicitações de conexão de entrada nessa porta e não poderá ser usada para conexões de saída. Para estabelecer uma conexão de saída, uma **porta efêmera** deve ser usada para fornecer o destino com uma porta na qual se comunicará e manterá um fluxo de tráfego distinto. Quando essas portas efêmeras são usadas para executar SNAT, elas são chamadas de **portas SNAT** 

Por definição, cada endereço IP tem 65.535 portas. Cada porta pode ser usada para conexões de entrada ou de saída para TCP (protocolo de controle de transmissão) e UDP (protocolo de datagrama do usuário). Quando um endereço IP público é adicionado como um IP de front-end a um balanceador de carga, o Azure fornece 64.000 qualificado para uso como portas SNAT. 

>[!NOTE]
> Cada porta usada para uma regra NAT de entrada ou de balanceamento de carga consumirá um intervalo de oito portas dessas 64.000 portas, reduzindo o número de portas elegíveis para SNAT. Se um balanceamento de carga > ou uma regra NAT estiver no mesmo intervalo de oito, pois outro não consumirá portas adicionais. 

Por meio de [regras de saída](./outbound-rules.md) e regras de balanceamento de carga, essas portas SNAT podem ser distribuídas para instâncias de back-end para permitir que eles compartilhem os IPs públicos do balanceador de carga para conexões de saída.

Quando o [cenário 2](#scenario2) abaixo estiver configurado, o host para cada instância de back-end executará SNAT em pacotes que fazem parte de uma conexão de saída. Ao executar SNAT em uma conexão de saída de uma instância de back-end, o host reescreve o IP de origem para um dos IPs de front-end. Para manter fluxos exclusivos, o host reescreve a porta de origem de cada pacote de saída para uma das portas de SNAT alocadas para a instância de back-end.

## <a name="outbound-connection-behavior-for-different-scenarios"></a>Comportamento de conexão de saída para cenários diferentes
  * Máquina virtual com IP público.
  * Máquina virtual sem IP público.
  * Máquina virtual sem IP público e sem o balanceador de carga padrão.
        

 ### <a name="scenario-1-virtual-machine-with-public-ip"></a><a name="scenario1"></a> Cenário 1: máquina virtual com IP público


 | Associações | Método | Protocolos IP |
 | ---------- | ------ | ------------ |
 | Balanceador de carga público ou autônomo | [SNAT (conversão de endereços de rede de origem)](#snat) </br> não usado. | TCP (protocolo de controle de transmissão) </br> UDP (protocolo de datagrama do usuário) </br> ICMP (protocolo de mensagens de controle da Internet) </br> ESP (encapsulamento de carga de segurança) |


 #### <a name="description"></a>Descrição


 O Azure usa o IP público atribuído à configuração de IP da NIC da instância para todos os fluxos de saída. A instância possui todas as portas efêmeras disponíveis. Não importa se a VM tem balanceamento de carga ou não. Esse cenário tem precedência sobre os outros. 


 Um IP público atribuído a uma VM é uma relação 1:1 (em vez de 1:muitos) e implementado como sem estado 1:1 NAT.


 ### <a name="scenario-2-virtual-machine-without-public-ip-and-behind-standard-public-load-balancer"></a><a name="scenario2"></a>Cenário 2: máquina virtual sem IP público e por trás do Load Balancer público padrão


 | Associações | Método | Protocolos IP |
 | ------------ | ------ | ------------ |
 | Load Balancer público padrão | Uso de IPs de front-end do balanceador de carga para [SNAT](#snat).| TCP </br> UDP |


 #### <a name="description"></a>Descrição


 O recurso de balanceador de carga é configurado com uma regra de saída ou uma regra de balanceamento de carga que habilita o SNAT padrão. Essa regra é usada para criar um vínculo entre o front-end IP público com o pool de back-end. 


 Se você não concluir essa configuração de regra, o comportamento será conforme descrito no cenário 3. 


 Uma regra com um ouvinte não é necessária para que a investigação de integridade tenha sucesso.


 Quando uma VM cria um fluxo de saída, o Azure converte o endereço IP de origem para o endereço IP público do front-end do Load Balancer público. Essa conversão é feita via [SNAT](#snat). 


 As portas efêmeras do endereço IP público de front-end do balanceador de carga são usadas para distinguir fluxos individuais originados pela VM. A SNAT usa dinamicamente [ortas efêmeras pré-alocadas](#preallocatedports) quando os fluxos de saída são criados. 


 Neste contexto, as portas efêmeras usadas para SNAT são chamadas de portas SNAT. É altamente recomendável que uma [regra de saída](./outbound-rules.md) seja configurada explicitamente. Se estiver usando SNAT padrão por meio de uma regra de balanceamento de carga, as portas SNAT serão previamente alocadas conforme descrito na [tabela de alocação de portas SNAT padrão](#snatporttable).

 ### <a name="scenario-3-virtual-machine-without-public-ip-and-behind-standard-internal-load-balancer"></a><a name="scenario3"></a>Cenário 3: máquina virtual sem IP público e por trás de Load Balancer internas padrão


 | Associações | Método | Protocolos IP |
 | ------------ | ------ | ------------ |
 | Balanceador de carga interno padrão | Sem conectividade com a Internet.| Nenhum |

 #### <a name="description"></a>Descrição
 
Ao usar um balanceador de carga interno padrão, não há nenhum uso de endereços IP efêmeras para SNAT. Isso é para dar suporte à segurança por padrão e garantir que todos os endereços IP usados pelo recurso sejam configuráveis e possam ser reservados. Para obter conectividade de saída com a Internet ao usar um balanceador de carga interno padrão, configure um endereço IP público em nível de instância para seguir o comportamento no (cenário 1) [#scenario1] ou adicione as instâncias de back-end a um Load Balancer público padrão com uma regra de saída configurada em additon para o balanceador de carga interno para seguir o comportamento no (cenário #scenario2 2 

 ### <a name="scenario-4-virtual-machine-without-public-ip-and-behind-basic-load-balancer"></a><a name="scenario4"></a>Cenário 4: máquina virtual sem IP público e por trás de Load Balancer básica


 | Associações | Método | Protocolos IP |
 | ------------ | ------ | ------------ |
 |Nenhum </br> Balanceador de carga básico | [SNAT](#snat) com endereço IP dinâmico em nível de instância| TCP </br> UDP | 

 #### <a name="description"></a>Descrição


 Quando a VM cria um fluxo de saída, o Azure traduz o endereço IP de origem para um endereço IP de origem pública alocado dinamicamente. Esse endereço IP público **não é configurável** e não pode ser reservado. Esse endereço não conta com relação ao limite de recursos de IP público da assinatura. 


 O endereço IP público será liberado e um novo IP público solicitado se você reimplantar o: 


 * Máquina Virtual
 * Conjunto de disponibilidade
 * Conjunto de escala de máquina virtual 


 Não use este cenário para adicionar IPs a uma lista de permissões. Use o cenário 1 ou 2 onde declarar explicitamente o comportamento de saída. As portas [SNAT](#snat) são prealocadas conforme descrito na [tabela de alocação de portas SNAT padrão](#snatporttable).

## <a name="exhausting-ports"></a><a name="scenarios"></a> Portas esgotadas

Todas as conexões com o mesmo IP de destino e porta de destino usarão uma porta SNAT. Essa conexão mantém um **fluxo de tráfego** distinto da instância de back-end ou do **cliente** para um **servidor**. Esse processo fornece ao servidor uma porta distinta na qual tratar o tráfego. Sem esse processo, o computador cliente não está ciente do fluxo do qual um pacote faz parte.

Imagine ter vários navegadores indo para https://www.microsoft.com :

* IP de destino = 23.53.254.142
* Porta de destino = 443
* Protocolo = TCP

Sem portas de destino diferentes para o tráfego de retorno (a porta SNAT usada para estabelecer a conexão), o cliente não terá como separar um resultado de consulta de outro.

As conexões de saída podem ser intermitentes. Uma instância de back-end pode ser alocada para portas insuficientes. Sem a **reutilização de conexão** habilitada, o risco de **esgotamento da porta** SNAT é aumentado.

Novas conexões de saída para um IP de destino falharão quando ocorrer esgotamento de porta. As conexões terão sucesso quando uma porta for disponibilizada. Esse esgotamento ocorre quando as portas 64.000 de um endereço IP são espalhadas de modo dinâmico em várias instâncias de back-end. Para obter orientação sobre a mitigação do esgotamento de porta SNAT, consulte o [Guia de solução de problemas](./troubleshoot-outbound-connection.md).  

Para conexões TCP, o balanceador de carga usará uma única porta SNAT para cada IP de destino e porta. Esse Multiuse permite várias conexões com o mesmo IP de destino com a mesma porta SNAT. Esse Multiuse será limitado se a conexão não for para portas de destino diferentes.

Para conexões UDP, o balanceador de carga usa um algoritmo **NAT de cone com restrição de porta** , que consome uma porta SNAT por IP de destino, seja qual for a porta de destino. 

Uma porta é reutilizada para um número ilimitado de conexões. A porta só será reutilizada se o IP ou a porta de destino for diferente.

## <a name="default-port-allocation"></a><a name="preallocatedports"></a> Alocação de porta padrão

Cada IP público atribuído como um IP de front-end do balanceador de carga recebe 64.000 portas SNAT para seus membros do pool de back-end. Portas não podem ser compartilhadas com membros do pool de back-end. Um intervalo de portas SNAT só pode ser usado por uma única instância de back-end para garantir que os pacotes de retorno sejam roteados corretamente. 

É recomendável usar uma regra de saída explícita para configurar a alocação de porta SNAT. Essa regra maximizará o número de portas SNAT que cada instância de back-end disponibilizou para conexões de saída. 

Se você usar a alocação automática de SNAT de saída por meio de uma regra de balanceamento de carga, a tabela de alocação definirá a alocação de porta.

A tabela a seguir <a name="snatporttable"></a> mostra as prealocações de porta SNAT para camadas de tamanhos de pool de back-end:

| Tamanho do pool (instâncias VM) | Portas SNAT pré-alocadas por configuração de IP |
| --- | --- |
| 1-50 | 1\.024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801-1,000 | 32 | 

>[!NOTE]
> Se você tiver um pool de back-end com um tamanho máximo de 10, cada instância poderá ter 64000/10 = 6.400 portas se você definir uma regra de saída explícita. De acordo com a tabela acima, cada uma terá apenas 1.024 se você escolher a alocação automática.

## <a name="outbound-rules-and-virtual-network-nat"></a><a name="outboundrules"></a> Regras de saída e NAT de rede virtual

Azure Load Balancer regras de saída e NAT de rede virtual são opções disponíveis para saída de uma rede virtual.

Para obter mais informações sobre regras de saída, consulte [regras de saída](outbound-rules.md).

Para obter mais informações sobre o NAT de rede virtual do Azure, consulte [o que é NAT de rede virtual do Azure](../virtual-network/nat-overview.md).

## <a name="constraints"></a>Restrições

*   Quando uma conexão estiver ociosa sem que nenhum novo pacote seja enviado, as portas serão liberadas após 4 a 120 minutos.
  * Esse limite pode ser configurado por meio de regras de saída.
*   Cada endereço IP fornece 64.000 portas que podem ser usadas para SNAT.
*   Cada porta pode ser usada para conexões TCP e UDP a um endereço IP de destino
  * Uma porta TCP SNAT é necessária se a porta de destino for exclusiva ou não. Para cada conexão UDP com um IP de destino, é usada uma porta TCP SNAT.
  * Uma porta TCP SNAT pode ser usada para várias conexões com o mesmo IP de destino, desde que as portas de destino sejam diferentes.
*   O esgotamento de SNAT ocorre quando uma instância de back-end é executada de determinadas portas SNAT. Um balanceador de carga ainda pode ter portas SNAT não utilizadas. Se as portas SNAT usadas de uma instância de back-end excederem suas portas SNAT determinadas, não será possível estabelecer novas conexões de saída.

## <a name="next-steps"></a>Próximas etapas

*   [Solucionar problemas de falhas de conexão de saída devido ao esgotamento de SNAT](./troubleshoot-outbound-connection.md)
*   [Examine as métricas SNAT](./load-balancer-standard-diagnostics.md#how-do-i-check-my-snat-port-usage-and-allocation) e familiarize-se com a maneira correta de filtrá-las, dividi-las e exibi-las.
