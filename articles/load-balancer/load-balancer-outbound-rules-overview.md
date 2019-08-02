---
title: Regras de saída no Azure Load Balancer
titlesuffix: Azure Load Balancer
description: Usar regras de saída para definir conversões de endereço de rede de saída
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 7/17/2019
ms.author: allensu
ms.openlocfilehash: 39a23fa277d7bb389098674556b65b1b13676ead
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68305574"
---
# <a name="load-balancer-outbound-rules"></a>Regras de saída do Load Balancer

O Azure Load Balancer fornece conectividade de saída de uma rede virtual além da entrada.  Regras de saída simplificam a configuração da conversão de endereços de rede de saída do [Standard Load Balancer](load-balancer-standard-overview.md) público.  Você tem controle declarativo completo sobre a conectividade de saída para dimensionar e ajustar essa capacidade conforme suas necessidades específicas.

![Regras de saída do Load Balancer](media/load-balancer-outbound-rules-overview/load-balancer-outbound-rules.png)

Com as regras de saída, você pode usar o Load Balancer para: 
- definir NAT de saída do zero.
- dimensionar e ajustar o comportamento do NAT de saída existente. 

Regras de saída permitem que você controle:
- quais máquinas virtuais devem ser convertidas em quais endereços IP públicos. 
- como [portas SNAT de saída](load-balancer-outbound-connections.md#snat) devem ser alocadas.
- para quais protocolos fornecer conversão de saída.
- qual duração usar para o tempo limite de ociosidade da conexão de saída (4-120 minutos).
- se você deseja enviar uma Redefinição de TCP no tempo limite de ociosidade (em Versão Prévia Pública). 

Regras de saída expandem o [cenário 2](load-balancer-outbound-connections.md#lb) descrito no artigo [conexões de saída](load-balancer-outbound-connections.md) e a precedência de cenário permanece como no estado em que se encontra.

## <a name="outbound-rule"></a>Regra de saída

Como todas as regras do Load Balancer, regras de saída seguem a mesma sintaxe familiar que regras NAT de entrada e balanceamento de carga:

**front-end** + **parâmetros** + **pool de back-end**

Uma regra de saída configura o NAT de saída para _todas as máquinas virtuais identificadas pelo pool de back-end_ a serem convertidas no _front-end_.  Os _parâmetros_ fornecem controle refinado adicional sobre o algoritmo NAT de saída.

A versão "2018-07-01" da API permite uma definição de regra de saída estruturada da seguinte forma:

```json
      "outboundRules": [
        {
          "frontendIPConfigurations": [ list_of_frontend_ip_configuations ],
          "allocatedOutboundPorts": number_of_SNAT_ports,
          "idleTimeoutInMinutes": 4 through 66,
          "enableTcpReset": true | false,
          "protocol": "Tcp" | "Udp" | "All",
          "backendAddressPool": backend_pool_reference,
        }
      ]
```

>[!NOTE]
>A configuração de NAT de saída efetiva é uma composição de todas as regras de saída e regras de balanceamento de carga. As regras de saída são incrementais para as regras de balanceamento de carga. Examine [como desabilitar NAT de saída para uma regra de balanceamento de carga](#disablesnat) para gerenciar a conversão de NAT de saída efetiva quando várias regras se aplicam a uma VM. Você deve [desabilitar SNAT de saída](#disablesnat) ao definir uma regra de saída que esteja usando o mesmo endereço IP público como uma regra de balanceamento de carga.

### <a name="scale"></a> Dimensionar NAT de saída com vários endereços IP

Embora uma regra de saída possa ser usada com apenas um único endereço IP público, regras de saída aliviam a carga de configuração para o dimensionamento de NAT de saída. Você pode usar vários endereços IP para planejar cenários de grande escala e pode usar regras de saída para atenuar [padrões propensos à exaustão de SNAT](load-balancer-outbound-connections.md#snatexhaust).  

Cada endereço IP adicional fornecido por um front-end fornece 51.200 portas efêmeras para o Load Balancer usar como portas SNAT. Embora regras de NAT de entrada ou balanceamento de carga tenham um único front-end, a regra de saída expande a noção de front-end e permite vários front-ends por regra.  Com vários frontends por regra, a quantidade de portas SNAT disponíveis é multiplicada com cada endereço IP público e grandes cenários podem ser suportados.

Além disso, você pode usar um [prefixo de IP público](https://aka.ms/lbpublicipprefix) diretamente com uma regra de saída.  Prefixo usando o IP público fornece para dimensionamento mais fácil e simplificada em uma lista de fluxos provenientes de sua implantação do Azure. Você pode configurar uma configuração de IP de front-end no recurso do Load Balancer para fazer referência a um prefixo de endereço IP público diretamente.  Isso dá ao Load Balancer controle exclusivo sobre o prefixo de IP público, e a regra de saída usará automaticamente todos os endereços IP públicos contidos dentro do prefixo de IP público para conexões de saída.  Cada um dos endereços IP dentro do intervalo do prefixo de IP público fornece 51.200 portas efêmeras por endereço IP para o Load Balancer usar como portas SNAT.   

Você não pode ter recursos de endereço IP público individuais criados usando o prefixo de IP público ao usar essa opção, uma vez que a regra de saída deve ter controle total sobre o prefixo de IP público.  Se você precisar de controle mais refinado, poderá criar o recurso de endereço IP público individual do prefixo IP público e atribuir vários endereços IP públicos individualmente ao front-end de uma regra de saída.

### <a name="snatports"></a> Ajustar a alocação da porta SNAT

Você pode usar regras de saída para ajustar a [alocação da porta SNAT automática com base no tamanho do pool de back-end](load-balancer-outbound-connections.md#preallocatedports) e alocar mais ou menos do que a alocação automática de porta SNAT fornece.

Use o parâmetro a seguir para alocar 10.000 portas SNAT por VM (configuração de IP do NIC).
 

          "allocatedOutboundPorts": 10000

Cada endereço IP público de todos os front-ends de uma regra de saída contribui com até 51.200 portas efêmeras para serem usadas como portas SNAT.  O Load Balancer aloca portas SNAT em múltiplos de 8. Se você fornecer um valor não divisível por 8, a operação de configuração será rejeitada.  Se você tentar alocar mais portas SNAT do que estão disponíveis com base no número de endereços IP públicos, a operação de configuração será rejeitada.  Por exemplo, se você alocar 10.000 portas por VM e 7 VMs em um pool de back-end compartilharão um único endereço IP público, a configuração será rejeitada (7 x 10.000 portas SNAT > 51.200 portas SNAT).  Você pode adicionar mais endereços de IP ao front-end da regra de saída para habilitar o cenário.

Você pode reverter para a [alocação da porta SNAT automática com base no tamanho do pool de back-end](load-balancer-outbound-connections.md#preallocatedports) especificando 0 como o número de portas.

### <a name="idletimeout"></a> Controlar o tempo limite de ociosidade de fluxo de saída

Regras de saída fornecem um parâmetro de configuração para controlar o tempo limite de ociosidade de fluxo de saída e combiná-lo com as necessidades do seu aplicativo.  O padrão de tempos limite de ociosidade de saída é de 4 minutos.  O parâmetro aceita um valor de 4 a 120 para especificar o número de minutos do tempo limite de ociosidade para os fluxos correspondentes a essa regra específica.

Use o parâmetro a seguir para definir o tempo limite de ociosidade de saída como 1 hora:

          "idleTimeoutInMinutes": 60

### <a name="tcprst"></a> <a name="tcpreset"></a> Habilitar a Redefinição de TCP no tempo limite de ociosidade (Versão Prévia)

O comportamento padrão do Load Balancer é remover o fluxo silenciosamente quando o tempo limite de ociosidade de saída é atingido.  Com o parâmetro enableTCPReset, você pode habilitar um comportamento mais previsível do aplicativo e controlar se deseja enviar Redefinição de TCP (RST TCP) bidirecional ao atingir o tempo limite de ociosidade de saída. 

Use o parâmetro a seguir para habilitar a Redefinição de TCP em uma regra de saída:

           "enableTcpReset": true

Examine [Redefinição de TCP no tempo limite de ociosidade (Versão Prévia)](https://aka.ms/lbtcpreset) para obter detalhes, incluindo a disponibilidade na região.

### <a name="proto"></a> Suporte a protocolos de transporte TCP e UDP com uma única regra

Você provavelmente vai querer usar "Todos" para o protocolo de transporte da regra de saída, mas também poderá aplicar a regra de saída a um protocolo de transporte específico se houver necessidade de fazer isso.

Use o parâmetro a seguir para definir os protocolos TCP e UDP:

          "protocol": "All"

### <a name="disablesnat"></a> Desabilitar NAT de saída para uma regra de balanceamento de carga

Conforme mencionado anteriormente, as regras de balanceamento de carga fornecem programação automática da NAT de saída. No entanto, alguns cenários se beneficiam ou exigem que você desabilite a programação automática de NAT de saída pela regra de balanceamento de carga para permitir que você controle ou refine o comportamento.  Regras de saída têm cenários em que é importante interromper a programação automática de NAT de saída.

Você pode usar esse parâmetro de duas maneiras:
- Supressão opcional do uso do endereço IP de entrada para NAT de saída.  Regras de saída são incrementais para as regras de balanceamento de carga e, com esse conjunto de parâmetros, a regra de saída está no controle.
  
- Ajuste os parâmetros NAT de saída de um endereço IP usado para entrada e saída simultaneamente.  A programação automática NAT de saída deve ser desabilitada para permitir que uma regra de saída assuma o controle.  Por exemplo, para alterar a alocação de porta SNAT de um endereço também usado para entrada, esse parâmetro deverá ser definido como true.  Se você tentar usar uma regra de saída para redefinir os parâmetros de um endereço IP também usado para entrada e não tiver liberado a programação NAT de saída da regra de balanceamento de carga, a operação de configurar uma regra de saída falhará.

>[!IMPORTANT]
> Sua máquina virtual não terá conectividade de saída se você definir esse parâmetro como true e não tiver uma regra de saída ou [cenário de IP público em nível de instância](load-balancer-outbound-connections.md#ilpip) para definir a conectividade de saída.  Algumas operações de sua VM ou do seu aplicativo podem depender de ter conectividade de saída disponível. Entenda as dependências do seu cenário e considere o impacto de fazer essa alteração.

Você pode desabilitar a saída SNAT na regra com este parâmetro de configuração de balanceamento de carga:

```json
      "loadBalancingRules": [
        {
          "disableOutboundSnat": true
        }
      ]
```

O parâmetro disableOutboundSNAT padrão é false, o que significa que a regra de balanceamento de carga **de fato** fornece NAT de saída automático como uma imagem espelhada da configuração da regra de balanceamento de carga.  

Se você definir disableOutboundSnat como true na regra de balanceamento de carga, a regra de balanceamento de carga devolverá o controle da programação de NAT de saída caso contrário, automática.  SNAT de saída como resultado da regra de balanceamento de carga está desabilitado.

### <a name="reuse-existing-or-define-new-backend-pools"></a>Reutilizar pools de back-end existentes ou definir novos

Regras de saída não introduzem um novo conceito para definir o grupo de VMs para o qual a regra deve ser aplicada.  Em vez disso, elas reutilizam o conceito de um pool de back-end que também é usado para regras de balanceamento de carga. Você pode usar isso para simplificar a configuração, seja reutilizando uma definição de pool de back-end existente ou criando um especificamente para uma regra de saída.

## <a name="scenarios"></a>Cenários

### <a name="groom"></a> Limpar conexões de saída para um conjunto específico de endereços IP públicos

Você pode usar uma regra de saída para limpar conexões de saída para que pareçam originarem-se de um conjunto específico de endereços IP públicos para facilitar cenários de colocação na lista de permissões.  Esse endereço IP público de origem pode ser o mesmo usado por uma regra de balanceamento de carga ou um conjunto diferente de endereços IP públicos do que os usados por uma regra de balanceamento de carga.  

1. Crie [prefixo de IP público](https://aka.ms/lbpublicipprefix) (ou endereços IP públicos usando o prefixo de IP público)
2. Criar um Load Balancer Standard público
3. Crie front-ends fazendo referência ao prefixo de IP público (ou endereços IP público) que deseja usar
4. Reutilize um pool de back-end ou crie um pool de back-end e coloque as VMs em um pool de back-end do Load Balancer público
5. Configure uma regra de saída no Load Balancer público para programar NAT de saída para essas VMs usando os front-ends
   
Se não desejar que a regra de balanceamento de carga seja usada para saída, precisará [desabilitar SNAT de saída](#disablesnat) na regra de balanceamento de carga.

### <a name="modifysnat"></a> Modificar a alocação da porta SNAT

Você pode usar regras de saída para ajustar a [alocação automática da porta SNAT com base no tamanho do pool de back-end](load-balancer-outbound-connections.md#preallocatedports).

Por exemplo, se você tiver duas máquinas virtuais compartilhando um único endereço IP público para NAT de saída, poderá aumentar o número de portas SNAT alocadas das 1.024 portas padrão se estiver enfrentando esgotamento de SNAT. Cada endereço IP público pode contribuir com até 51.200 portas efêmeras.  Se você configurar uma regra de saída com um único front-end de endereço IP público, poderá distribuir um total de 51.200 portas SNAT para VMs no pool de back-end.  Para duas VMs, um máximo de 25.600 portas SNAT pode ser alocado com uma regra de saída (2 x 25.600 = 51.200).

Examine [conexões de saída](load-balancer-outbound-connections.md) e os detalhes sobre como portas [SNAT](load-balancer-outbound-connections.md#snat) são alocadas e usadas.

### <a name="outboundonly"></a> Habilitar apenas saída

Você pode usar um Standard Load Balancer público para fornecer NAT de saída a um grupo de VMs. Nesse cenário, você pode usar uma regra de saída sozinha, sem a necessidade de todas as regras adicionais.

#### <a name="outbound-nat-for-vms-only-no-inbound"></a>NAT de saída apenas para VMs (não de entrada)

Defina um Standard Load Balancer público, coloque as VMs no pool de back-end e configure uma regra de saída para programar NAT de saída e limpar as conexões de saída para originarem-se de um endereço IP público específico. Você também pode usar um prefixo IP público para simplificar a colocação da origem de conexões de saída em lista de permissões.

1. Crie um Standard Load Balancer público.
2. Crie um pool de back-end e coloque as VMs em um pool de back-end do Load Balancer público.
3. Configure uma regra de saída no Load Balancer público para programar NAT de saída para essas VMs.

#### <a name="outbound-nat-for-internal-standard-load-balancer-scenarios"></a>NAT de saída para cenários de Standard Load Balancer interno

Ao usar um Standard Load Balancer interno, a NAT de saída não estará disponível até que uma conectividade de saída já tenha sido declarada. Você pode definir a conectividade de saída ao usar uma regra de saída para criar a conectividade de saída para as VMs por trás de um Standard Load Balancer com essas etapas:

1. Crie um Standard Load Balancer público.
2. Crie um pool de back-end e coloque as VMs em um pool de back-end do Load Balancer público, além do Load Balancer interno.
3. Configure uma regra de saída no Load Balancer público para programar NAT de saída para essas VMs.

#### <a name="enable-both-tcp--udp-protocols-for-outbound-nat-with-a-public-standard-load-balancer"></a>Habilitar protocolos TCP e UDP para NAT de saída com um Standard Load Balancer público

- Ao usar um Standard Load Balancer público, a programação de NAT de saída automática fornecida corresponde ao protocolo de transporte da regra de balanceamento de carga.  

   1. Desabilite a SNAT de saída na regra de balanceamento de carga.
   2. Configure uma regra de saída no mesmo Load Balancer.
   3. Reutilize o pool de back-end já usado por suas VMs.
   4. Especifique "protocolo": "Todos" como parte da regra de saída.

- Quando são usadas apenas regras NAT de entrada, nenhuma NAT de saída é fornecida.

   1. Coloque as VMs em um pool de back-end.
   2. Defina uma ou mais configurações de IP de front-end com endereços IP públicos ou prefixo IP público.
   3. Configure uma regra de saída no mesmo Load Balancer.
   4. Especifique "protocolo": "Todos" como parte da regra de saída

## <a name="limitations"></a>Limitações

- O número máximo de portas efêmeras utilizáveis por endereço IP de front-end é 51.200.
- O intervalo do tempo limite de ociosidade de saída configurável é de 4 a 120 minutos (240 a 7.200 segundos).
- O Load Balancer não dá suporte a ICMP para NAT de saída.
- O portal não pode ser usado para configurar ou exibir as regras de saída.  Em vez disso, use modelos, API REST, Az CLI 2.0 ou PowerShell.
- As regras de saída só podem ser aplicadas à configuração de IP primário de uma NIC.  Há suporte para várias NICs.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre como usar o [Azure Load Balancer para conexões de saída](load-balancer-outbound-connections.md).
- Saiba mais sobre o [Standard Load Balancer](load-balancer-standard-overview.md).
- Saiba mais sobre [Redefinição TCP bidirecional no tempo limite de ociosidade](load-balancer-tcp-reset.md).
- [Configurar regras de saída com a CLI do Azure 2.0](configure-load-balancer-outbound-cli.md).
