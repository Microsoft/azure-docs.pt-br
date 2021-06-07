---
title: Solucionar problemas do Azure Load Balancer
description: Saiba como solucionar problemas conhecidos com o Azure Load Balancer.
services: load-balancer
documentationcenter: na
author: asudbring
manager: dcscontentpm
ms.custom: seodoc18
ms.service: load-balancer
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/28/2020
ms.author: allensu
ms.openlocfilehash: bfe2b21a86f2ce4b4630ba69cde87796fd367f4b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98029159"
---
# <a name="troubleshoot-azure-load-balancer-backend-traffic-responses"></a>Solucionar problemas Azure Load Balancer respostas de tráfego de back-end

Esta página fornece informações de solução de problemas para Azure Load Balancer perguntas.

## <a name="vms-behind-load-balancer-are-not-responding-to-traffic-on-the-configured-data-port"></a>As VMs por trás do Load Balancer não estão respondendo ao tráfego na porta de dados configurada

Se uma VM do pool de back-end estiver listada como íntegra e responder às investigações de integridade, mas ainda não estiver participando do Balanceamento de Carga, ou não estiver respondendo ao tráfego de dados, pode ser devido a um destes motivos:
* A VM do pool de back-end do Load Balancer não está escutando na porta de dados
* O grupo de segurança de rede está bloqueando a porta na VM do pool de back-end do Load Balancer  
* Acessando o Load Balancer da mesma VM e NIC
* Acessando o frontend do Load Balancer da Internet a partir da VM participante do pool de back-end do Load Balancer

## <a name="cause-1-load-balancer-backend-pool-vm-is-not-listening-on-the-data-port"></a>Causa 1: A VM do pool de back-end do Load Balancer não está escutando na porta de dados

Se uma VM não responder ao tráfego de dados, pode ser porque a porta de destino não está aberta na VM participante ou porque a VM não está escutando nessa porta. 

**Validação e resolução**

1. Faça logon na VM de back-end. 
2. Abra um prompt de comando e execute o seguinte comando para verificar se existe um aplicativo escutando na porta de dados:  
            netstat -an 
3. Se a porta não estiver listada com o estado "LISTENING", configure a porta de ouvinte apropriada 
4. Se a porta estiver marcada como Listening, verifique se há algum problema no aplicativo de destino dessa porta.

## <a name="cause-2-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vm"></a>Causa 2: O grupo de segurança de rede está bloqueando a porta na VM do pool de back-end do Load Balancer  

Se um ou mais grupos de segurança de rede configurados na sub-rede ou na VM estiverem bloqueando o IP de origem ou a porta, a VM não poderá responder.

Para o balanceador de carga público, o endereço IP dos clientes da Internet será usado para comunicação entre os clientes e as VMs de back-end do balanceador de carga. Garanta que o endereço IP dos clientes é permitido no grupo de segurança de rede da VM de back-end.

1. Liste os grupos de segurança de rede configurados na VM de back-end. Para obter mais informações, consulte [Gerenciar grupos de segurança de rede](../virtual-network/manage-network-security-group.md)
1. Na lista de grupos de segurança de rede, verifique se:
    - O tráfego de entrada ou saída na porta de dados tem interferência. 
    - Uma regra do grupo de segurança de rede **Negar Tudo** na NIC da VM ou na sub-rede tem uma prioridade mais alta do que a regra padrão que permite as investigações e o tráfego do Load Balancer (grupos de segurança de rede devem permitir o IP 168.63.129.16 do Load Balancer, que é a porta de investigação)
1. Se qualquer uma dessas regras estiver bloqueando o tráfego, remova e reconfigure as regras para permitir o tráfego de dados.  
1. Agora, verifique se a VM começou a responder às investigações de integridade.

## <a name="cause-3-accessing-the-load-balancer-from-the-same-vm-and-network-interface"></a>Causa 3: Acessando o Load Balancer da mesma VM e interface de rede 

Se seu aplicativo hospedado na VM de back-end de um Load Balancer estiver tentando acessar outro aplicativo hospedado na mesma VM de back-end pela mesma Interface de Rede, trata-se de um cenário sem suporte e que falhará. 

**Resolução** Você pode resolver esse problema usando um dos seguintes métodos:
* Configure VMs de pool de back-end separadas por aplicativo. 
* Configure o aplicativo em VMs de NIC dupla para que cada aplicativo use sua própria interface de rede e seu endereço IP. 

## <a name="cause-4-accessing-the-internal-load-balancer-frontend-from-the-participating-load-balancer-backend-pool-vm"></a>Causa 4: Acessando o front-end interno do Load Balancer a partir da VM participante do pool de back-end do Load Balancer

Se um Balanceador de Carga interno estiver configurado dentro de uma Rede Virtual e uma das VMs de backend do participante estiver tentando acessar o frontend interno do Load Balancer, poderão ocorrer falhas quando o fluxo for mapeado para a VM de origem. Não há suporte para esse cenário.

**Resolução** há várias maneiras para desbloquear este cenário, incluindo o uso de um proxy. Avalie o Gateway de aplicativo ou outros proxies 3ª de terceiros (por exemplo, nginx ou haproxy). Para saber mais sobre o Gateway de Aplicativo, confira [Visão geral do Gateway de Aplicativo](../application-gateway/overview.md)

**Detalhes** Os Load Balancers internos não movem conexões originadas pela saída para o front-end de um Load Balancer interno porque ambos estão no espaço de endereços IP privado. Os Load Balancers fornecem [conexões de saída](load-balancer-outbound-connections.md) de endereços IP privados dentro da rede virtual para os endereços IP públicos. Para Load Balancers internos, essa abordagem evita possível esgotamento da porta SNAT em um espaço de endereços IP interno único, onde a movimentação não é obrigatória.

O efeito colateral é que, se um fluxo de saída de uma VM no pool de back-end tentar estabelecer um fluxo para o front-end do Load Balancer interno em seu pool _e_ for mapeado de volta para si mesmo, os dois lados do fluxo não serão correspondentes. Como eles não correspondem, o fluxo falha. O fluxo será bem-sucedido se não for mapeado de volta para a mesma VM no pool de back-end que criou o fluxo para o front-end.

Quando o fluxo é mapeado de volta para si mesmo, o fluxo de saída parece ser originado da VM para o front-end e o fluxo de entrada correspondente parece ser originado da VM para si mesmo. Do ponto de vista do sistema operacional convidado, as partes de entrada e de saída do mesmo fluxo não são correspondentes dentro da máquina virtual. A pilha TCP não reconhece essas metades do mesmo fluxo como parte do mesmo fluxo. A origem e o destino não correspondem. Quando o fluxo é mapeado para qualquer outra VM no pool de back-end, as metades do fluxo são correspondentes e a VM pode responder ao fluxo.

O sintoma desse cenário é que a conexão intermitente se esgota quando o fluxo retorna para o mesmo back-end que originou o fluxo. Soluções alternativas comuns incluem a inserção de uma camada proxy por trás do Load Balancer interno e o uso de regras de estilo DSR (Retorno de Servidor Direto). Para obter mais informações, consulte [Vários front-ends para o Azure Load Balancer](load-balancer-multivip-overview.md).

É possível combinar um Load Balancer interno com qualquer proxy de terceiros ou usar o [Gateway de Aplicativo](../application-gateway/overview.md) interno para cenários de proxy com HTTP/HTTPS. Embora você possa usar um Load Balancer público para atenuar esse problema, o cenário resultante estará propenso ao [esgotamento de SNAT](load-balancer-outbound-connections.md). Evite essa segunda abordagem, a menos que seja cuidadosamente gerenciada.

## <a name="next-steps"></a>Próximas etapas

Se as etapas anteriores não resolverem o problema, abra um [tíquete de suporte](https://azure.microsoft.com/support/options/).