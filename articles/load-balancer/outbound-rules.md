---
title: Regras de saída Azure Load Balancer
description: Este artigo explica como configurar as regras de saída para controlar a egresso do tráfego de Internet com o Azure Load Balancer.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: conceptual
ms.custom: contperf-fy21q1
ms.date: 10/13/2020
ms.author: allensu
ms.openlocfilehash: 6b73eb51831238f23400ef60d0a6162bca38ea85
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2020
ms.locfileid: "97033146"
---
# <a name="outbound-rules-azure-load-balancer"></a><a name="outboundrules"></a>Regras de saída Azure Load Balancer

As regras de saída permitem definir explicitamente o SNAT (conversão de endereços de rede de origem) para um balanceador de carga padrão público. Essa configuração permite que você use os IPs públicos do balanceador de carga para fornecer conectividade de Internet de saída para suas instâncias de back-end.

Essa configuração permite:

* Representação de IP
* Simplificando suas listas de permissões.
* Reduz o número de recursos IP públicos para implantação.

Com as regras de saída, você tem total controle declarativo sobre a conectividade de Internet de saída. As regras de saída permitem que você dimensione e ajuste essa capacidade para suas necessidades específicas. 

As regras de saída serão seguidas somente se a VM de back-end não tiver um endereço IP público em nível de instância (ILPIP).

![Regras de saída do Load Balancer](media/load-balancer-outbound-rules-overview/load-balancer-outbound-rules.png)

Com as regras de saída, você pode definir explicitamente o comportamento de **SNAT** de saída.

Regras de saída permitem que você controle:

* **Quais máquinas virtuais são convertidas para quais endereços IP públicos.**
     * Duas regras o pool de back-end A usa o endereço IP A e B, o pool de back-end B usa o endereço IP C e D.
* **Como as portas SNAT de saída são alocadas.**
     * O pool de back-end B é o único pool que faz conexões de saída, fornece todas as portas SNAT ao pool de back-end B e nenhuma para o pool de back-end
* **Para quais protocolos fornecer tradução de saída.**
     * O pool de back-end B precisa de portas UDP para saída. O pool de back-end A precisa de TCP. Forneça as portas TCP para as portas UDP e a B.
* **Qual duração usar para tempo limite de ociosidade de conexão de saída (4-120 minutos).**
     * Se houver conexões de longa execução com keepalives, Reserve portas ociosas para conexões de longa execução por até 120 minutos. Suponha que conexões obsoletas sejam abandonadas e libere portas em 4 minutos para conexões novas 
* **Se deve ser enviada uma redefinição de TCP no tempo limite ocioso.**
     * Ao atingir o tempo limite das conexões ociosas, enviamos um TCP RST para o cliente e para o servidor para que eles saibam que o fluxo é abandonado?

## <a name="outbound-rule-definition"></a>Definição de regra de saída

As regras de saída seguem a mesma sintaxe familiar que o balanceamento de carga e as regras NAT de entrada: pool de  +    +  **back-end** de parâmetros de frontend. 

Uma regra de saída configura o NAT de saída para _todas as máquinas virtuais identificadas pelo pool de back-end_ a serem convertidas no _front-end_.  

Os _parâmetros_ fornecem um controle adicional refinado sobre o algoritmo NAT de saída.

## <a name="scale-outbound-nat-with-multiple-ip-addresses"></a><a name="scale"></a> Dimensionar NAT de saída com vários endereços IP

Cada endereço IP adicional fornecido por um front-end fornece portas efêmeras 64.000 adicionais para o balanceador de carga usar como portas SNAT. 

Use vários endereços IP para planejar cenários em larga escala. Use regras de saída para atenuar o [esgotamento de SNAT](troubleshoot-outbound-connection.md#snatexhaust). 

Você também pode usar um [prefixo IP público](./load-balancer-outbound-connections.md#outboundrules) diretamente com uma regra de saída. 

Um prefixo IP público aumenta o dimensionamento da sua implantação. O prefixo pode ser adicionado à lista de permissões de fluxos provenientes de seus recursos do Azure. Você pode configurar uma configuração de IP de front-end dentro do balanceador de carga para fazer referência a um prefixo de endereço IP público.  

O balanceador de carga tem controle sobre o prefixo IP público. A regra de saída usará automaticamente todos os endereços IP públicos contidos no prefixo IP público para conexões de saída. 

Cada um dos endereços IP dentro do prefixo IP público fornece uma 64.000 portas efêmeras adicionais por endereço IP para o balanceador de carga usar como portas SNAT.

## <a name="outbound-flow-idle-timeout-and-tcp-reset"></a><a name="idletimeout"></a> Tempo limite de ociosidade do fluxo de saída e redefinição de TCP

Regras de saída fornecem um parâmetro de configuração para controlar o tempo limite de ociosidade de fluxo de saída e combiná-lo com as necessidades do seu aplicativo. O padrão de tempos limite de ociosidade de saída é de 4 minutos. Para obter mais informações, consulte [Configurar tempos limite de ociosidade](load-balancer-tcp-idle-timeout.md). 

O comportamento padrão do balanceador de carga é descartar o fluxo silenciosamente quando o tempo limite de ociosidade de saída for atingido. O `enableTCPReset` parâmetro habilita um comportamento e controle previsíveis do aplicativo. O parâmetro determina se a redefinição TCP bidirecional (TCP RST) deve ser enviada no tempo limite do tempo limite de ociosidade de saída. 

Examine a [redefinição de TCP no tempo limite ocioso](./load-balancer-tcp-reset.md) para obter detalhes, incluindo a disponibilidade da região.

## <a name="securing-and-controlling-outbound-connectivity-explicitly"></a><a name="preventoutbound"></a>Proteger e controlar a conectividade de saída explicitamente

As regras de balanceamento de carga fornecem programação automática de NAT de saída. Alguns cenários se beneficiam ou exigem que você desabilite a programação automática do NAT de saída pela regra de balanceamento de carga. A desabilitação por meio da regra permite controlar ou refinar o comportamento.  

Você pode usar esse parâmetro de duas maneiras:

1. Prevenção do endereço IP de entrada para SNAT de saída. Desabilite o SNAT de saída na regra de balanceamento de carga.
  
2. Ajuste os parâmetros de **SNAT** de saída de um endereço IP usado para entrada e saída simultaneamente. O NAT de saída automático deve ser desabilitado para permitir que uma regra de saída assuma o controle. Para alterar a alocação de porta SNAT de um endereço também usado para entrada, o `disableOutboundSnat` parâmetro deve ser definido como true. 

A operação para configurar uma regra de saída falhará se você tentar redefinir um endereço IP que é usado para entrada.  Desabilite primeiro o NAT de saída da regra de balanceamento de carga.

>[!IMPORTANT]
> Sua máquina virtual não terá conectividade de saída se você definir esse parâmetro como true e não tiver uma regra de saída para definir a conectividade de saída.  Algumas operações de sua VM ou do seu aplicativo podem depender de ter conectividade de saída disponível. Entenda as dependências do seu cenário e considere o impacto de fazer essa alteração.

Às vezes, não é desejável que uma VM crie um fluxo de saída. Pode haver um requisito para gerenciar quais destinos recebem fluxos de saída ou quais destinos iniciam fluxos de entrada. Use [grupos de segurança de rede](../virtual-network/network-security-groups-overview.md) para gerenciar os destinos que a VM atinge. Use NSGs para gerenciar quais destinos públicos iniciam fluxos de entrada.

Ao aplicar um NSG a uma VM com balanceamento de carga, atente-se às [marcas de serviço](../virtual-network/network-security-groups-overview.md#service-tags) e às [regras de segurança padrão](../virtual-network/network-security-groups-overview.md#default-security-rules). 

Verifique se a VM pode receber solicitações de investigação de integridade de Azure Load Balancer.

Se um NSG bloquear as solicitações de investigação de integridade da AZURE_LOADBALANCER marca padrão, a investigação de integridade da VM falhará e a VM será marcada como indisponível. O balanceador de carga para de enviar novos fluxos para essa VM.

## <a name="scenarios-with-outbound-rules"></a>Cenários com regras de saída
        

### <a name="outbound-rules-scenarios"></a>Cenários de regras de saída


* Configure conexões de saída para um conjunto específico de IPs ou de prefixo público.
* Modificar a alocação de porta [SNAT](load-balancer-outbound-connections.md) .
* Habilitar somente saída.
* NAT de saída somente para VMs (sem entrada).
* NAT de saída para o balanceador de carga Standard interno.
* Habilite os protocolos TCP & UDP para NAT de saída com um balanceador de carga padrão público.


### <a name="scenario-1-configure-outbound-connections-to-a-specific-set-of-public-ips-or-prefix"></a><a name="scenario1out"></a>Cenário 1: configurar conexões de saída para um conjunto específico de IPs ou prefixo público


#### <a name="details"></a>Detalhes


Use este cenário para personalizar as conexões de saída para originar-se de um conjunto de endereços IP públicos. Adicione IPs públicos ou prefixos a uma lista de permissão ou negação com base na origem.


Esse IP público ou prefixo pode ser o mesmo usado por uma regra de balanceamento de carga. 


Para usar um IP público ou prefixo diferente do usado por uma regra de balanceamento de carga: 


1. Crie um prefixo IP público ou endereço IP público.
2. Criar um balanceador de carga padrão público 
3. Crie um front-end referenciando o prefixo IP público ou o endereço IP público que você deseja usar. 
4. Reutilize um pool de back-end ou crie um pool de back-end e coloque as VMs em um pool de back-end do balanceador de carga público
5. Configure uma regra de saída no balanceador de carga público para habilitar o NAT de saída para as VMs usando o front-end. Não é recomendável usar uma regra de balanceamento de carga para saída, desabilitar o SNAT de saída na regra de balanceamento de carga.


### <a name="scenario-2-modify-snatport-allocation"></a><a name="scenario2out"></a>Cenário 2: modificar a alocação de porta [SNAT](load-balancer-outbound-connections.md)


#### <a name="details"></a>Detalhes


Você pode usar regras de saída para ajustar a [alocação de porta SNAT automática com base no tamanho do pool de back-end](load-balancer-outbound-connections.md#preallocatedports). 


Se você experimentar o esgotamento de SNAT, aumente o número de portas [SNAT](load-balancer-outbound-connections.md)dadas do padrão de 1024. 


Cada endereço IP público contribui para até 64.000 portas efêmeras. O número de VMs no pool de back-end determina o número de portas distribuídas para cada VM. Uma VM no pool de back-end tem acesso ao máximo de 64.000 portas. Para duas VMs, no máximo 32.000 portas [SNAT](load-balancer-outbound-connections.md)podem ser dadas com uma regra de saída (2x 32.000 = 64.000). 


Você pode usar regras de saída para ajustar as portas SNAT fornecidas por padrão. Você dá mais ou menos do que a alocação de porta [SNAT](load-balancer-outbound-connections.md)padrão fornecida. Cada endereço IP público de um front-end de uma regra de saída contribui para até 64.000 portas efêmeras para uso como portas [SNAT](load-balancer-outbound-connections.md). 


O balanceador de carga fornece portas [SNAT](load-balancer-outbound-connections.md)em múltiplos de 8. Se você fornecer um valor não divisível por 8, a operação de configuração será rejeitada. Cada regra de balanceamento de carga e a regra NAT de entrada consumirão um intervalo de 8 portas. Se uma regra de balanceamento de carga ou de entrada de NAT compartilhar o mesmo intervalo de 8 como outra, nenhuma porta adicional será consumida.


Se você tentar fornecer mais portas [SNAT](load-balancer-outbound-connections.md)do que as disponíveis com base no número de endereços IP públicos, a operação de configuração será rejeitada. Por exemplo, se você fornecer 10.000 portas por VM e sete VMs em um pool de back-end compartilharem um único IP público, a configuração será rejeitada. Sete multiplicado por 10.000 excede o limite de 64.000 portas. Adicione mais endereços IP públicos ao front-end da regra de saída para habilitar o cenário. 


Reverta para a [alocação de porta padrão](load-balancer-outbound-connections.md#preallocatedports) especificando 0 para o número de portas. As primeiras 50 instâncias de VM receberão 1024 portas, as instâncias de VM 51-100 terão 512 até o máximo de instâncias. Para obter mais informações sobre a alocação de porta SNAT padrão, consulte a [tabela de alocação de portas SNAT](./load-balancer-outbound-connections.md#preallocatedports).


### <a name="scenario-3-enable-outbound-only"></a><a name="scenario3out"></a>Cenário 3: habilitar somente saída


#### <a name="details"></a>Detalhes


Use um balanceador de carga padrão público para fornecer NAT de saída para um grupo de VMs. Nesse cenário, use uma regra de saída por si só, sem nenhuma regra adicional configurada.


> [!NOTE]
> O **NAT da rede virtual do Azure** pode fornecer conectividade de saída para máquinas virtuais sem a necessidade de um balanceador de carga. Consulte [o que é NAT de rede virtual do Azure?](../virtual-network/nat-overview.md) para obter mais informações.

### <a name="scenario-4-outbound-nat-for-vms-only-no-inbound"></a><a name="scenario4out"></a>Cenário 4: NAT de saída somente para VMs (sem entrada)


> [!NOTE]
> O **NAT da rede virtual do Azure** pode fornecer conectividade de saída para máquinas virtuais sem a necessidade de um balanceador de carga. Consulte [o que é NAT de rede virtual do Azure?](../virtual-network/nat-overview.md) para obter mais informações.

#### <a name="details"></a>Detalhes


Para este cenário: Azure Load Balancer regras de saída e NAT de rede virtual são opções disponíveis para saída de uma rede virtual.


1. Crie um IP público ou prefixo.
2. Crie um balanceador de carga padrão público. 
3. Crie um front-end associado ao IP público ou prefixo dedicado para saída.
4. Crie um pool de back-end para as VMs.
5. Coloque as VMs no pool de back-end.
6. Configure uma regra de saída para habilitar o NAT de saída.



Use um prefixo ou IP público para dimensionar portas [SNAT](load-balancer-outbound-connections.md). Adicione a origem das conexões de saída a uma lista de permissão ou negação.



### <a name="scenario-5-outbound-nat-for-internal-standard-load-balancer"></a><a name="scenario5out"></a>Cenário 5: NAT de saída para o balanceador de carga Standard interno


> [!NOTE]
> O **NAT da rede virtual do Azure** pode fornecer conectividade de saída para máquinas virtuais utilizando um balanceador de carga padrão interno. Consulte [o que é NAT de rede virtual do Azure?](../virtual-network/nat-overview.md) para obter mais informações.

#### <a name="details"></a>Detalhes


A conectividade de saída não está disponível para um balanceador de carga padrão interno até que tenha sido declarado explicitamente por meio de IPs públicos em nível de instância ou NAT de rede virtual ou associando os membros do pool de back-end com uma configuração de balanceador de carga somente de saída. 


Para obter mais informações, consulte [configuração de balanceador de carga somente de saída](./egress-only.md).




### <a name="scenario-6-enable-both-tcp--udp-protocols-for-outbound-nat-with-a-public-standard-load-balancer"></a><a name="scenario6out"></a>Cenário 6: habilitar os protocolos TCP & UDP para NAT de saída com um balanceador de carga padrão público


#### <a name="details"></a>Detalhes


Ao usar um balanceador de carga padrão público, o NAT de saída automático fornecido corresponde ao protocolo de transporte da regra de balanceamento de carga. 


1. Desabilite o [SNAT](load-balancer-outbound-connections.md)de saída na regra de balanceamento de carga. 
2. Configure uma regra de saída no mesmo balanceador de carga.
3. Reutilize o pool de back-end já usado por suas VMs. 
4. Especifique "protocolo": "Todos" como parte da regra de saída. 


Quando são usadas apenas regras NAT de entrada, nenhuma NAT de saída é fornecida. 


1. Coloque as VMs em um pool de back-end.
2. Definir uma ou mais configurações de IP de front-end com endereços IP públicos ou prefixo de IP público 
3. Configure uma regra de saída no mesmo balanceador de carga. 
4. Especifique "protocolo": "Todos" como parte da regra de saída


## <a name="limitations"></a>Limitações

- O número máximo de portas efêmeras utilizáveis por endereço IP de front-end é 64.000.
- O intervalo do tempo limite de ociosidade de saída configurável é de 4 a 120 minutos (240 a 7.200 segundos).
- O balanceador de carga não dá suporte a ICMP para NAT de saída.
- As regras de saída só podem ser aplicadas à configuração de IP primário de uma NIC.  Você não pode criar uma regra de saída para o IP secundário de uma VM ou NVA. Há suporte para várias NICs.
- Todas as máquinas virtuais em um **conjunto de disponibilidade** devem ser adicionadas ao pool de back-end para conectividade de saída. 
- Todas as máquinas virtuais em um **conjunto de dimensionamento de máquinas virtuais** devem ser adicionadas ao pool de back-end para conectividade de saída.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [Azure Standard Load Balancer](load-balancer-overview.md)
- Veja nossas [perguntas frequentes sobre Azure Load Balancer](load-balancer-faqs.md)