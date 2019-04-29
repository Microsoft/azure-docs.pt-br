---
title: Solucionar problemas do Azure Load Balancer
titlesuffix: Azure Load Balancer
description: Solucionar problemas conhecidos com o Azure Load Balancer
services: load-balancer
documentationcenter: na
author: chadmath
manager: cshepard
ms.custom: seodoc18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/09/2018
ms.author: genli
ms.openlocfilehash: c5f92d564a93823fd9c0f932fa95f20d4e827761
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60734466"
---
# <a name="troubleshoot-azure-load-balancer"></a>Solucionar problemas do Azure Load Balancer

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Esta página fornece informações para solução de problemas comuns do Azure Load Balancer. Quando a conectividade do Load Balancer não estiver disponível, os sintomas mais comuns são os seguintes: 
- VMs por trás do Load Balancer não estão respondendo às investigações de integridade 
- VMs por trás do Load Balancer não estão respondendo ao tráfego na porta configurada

## <a name="symptom-vms-behind-the-load-balancer-are-not-responding-to-health-probes"></a>Sintoma: VMs por trás do Load Balancer não estão respondendo às investigações de integridade
Para que os servidores back-end participem do conjunto de balanceadores de carga, eles devem passar na verificação de investigação. Para saber mais sobre investigações de integridade, confira [Noções básicas sobre investigações do Load Balancer](load-balancer-custom-probe-overview.md). 

As VMs do pool de back-end do Load Balancer podem não estar respondendo às investigações devido a algum destes motivos: 
- A VM do pool de back-end do Load Balancer não está em estado íntegro 
- A VM do pool de back-end do Load Balancer não está escutando na porta de investigação 
- O firewall, ou o grupo de segurança de rede, está bloqueando a porta nas VMs do pool de back-end do Load Balancer 
- Outras configurações incorretas no Load Balancer

### <a name="cause-1-load-balancer-backend-pool-vm-is-unhealthy"></a>Causa 1: A VM do pool de back-end do Load Balancer não está em estado íntegro 

**Validação e resolução**

Para resolver esse problema, faça logon nas VMs participantes e verifique se a VM está em estado íntegro e pode responder a **PsPing** ou **TCPing** de outra VM no pool. Se a VM não estiver em estado íntegro ou não puder responder à investigação, você deverá retificar o problema e colocar a VM de volta em estado íntegro para que ela possa participar do balanceamento de carga.

### <a name="cause-2-load-balancer-backend-pool-vm-is-not-listening-on-the-probe-port"></a>Causa 2: A VM do pool de back-end do Load Balancer não está escutando na porta de investigação
Se a VM estiver em estado íntegro, mas não estiver respondendo à investigação, é possível que a porta de investigação não esteja aberta na VM participante ou que a VM não esteja escutando nessa porta.

**Validação e resolução**

1. Faça logon na VM de back-end. 
2. Abra um prompt de comando e execute o seguinte comando para verificar se existe um aplicativo escutando na porta de investigação:   
            netstat -an
3. Se o estado da porta não estiver listado como **LISTENING**, configure a porta correta. 
4. Como alternativa, selecione outra porta, que esteja listada como **LISTENING**, e atualize adequadamente a configuração do Load Balancer.              

### <a name="cause-3-firewall-or-a-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vms"></a>Causa 3: O firewall, ou um grupo de segurança de rede, está bloqueando a porta nas VMs do pool de back-end do balanceador de carga  
Se o firewall na VM estiver bloqueando a porta de investigação, ou um ou mais grupos de segurança de rede configurados na sub-rede ou na VM, não estiverem permitindo que a investigação alcance a porta, a VM não poderá responder à investigação de integridade.          

**Validação e resolução**

* Se o firewall estiver habilitado, verifique se ele está configurado para permitir a porta de investigação. Caso contrário, configure o firewall para permitir o tráfego na porta de investigação e teste novamente. 
* Na lista de grupos de segurança de rede, verifique se o tráfego de entrada ou saída na porta de investigação tem interferência. 
* Verifique também se uma regra dos grupos de segurança de rede **Negar Tudo** na NIC da VM ou da sub-rede tem uma prioridade mais alta do que a regra padrão que permite investigações e tráfego do LB (grupos de segurança de rede devem permitir o IP 168.63.129.16 do Load Balancer). 
* Se qualquer uma dessas regras estiver bloqueando o tráfego de investigação, remova e reconfigure as regras para permitir o tráfego de investigação.  
* Agora, verifique se a VM começou a responder às investigações de integridade. 

### <a name="cause-4-other-misconfigurations-in-load-balancer"></a>Causa 4: Outras configurações incorretas no Load Balancer
Se todas as causas anteriores, aparentemente, tiverem sido validadas e resolvidas corretamente, e a VM de back-end continuar não respondendo à investigação de integridade, teste a conectividade manualmente e colete alguns rastreamentos para entender a conectividade.

**Validação e resolução**

* Use **Psping** em uma das VMs na VNet para testar a resposta da porta de investigação (exemplo:.\psping.exe -t 10.0.0.4:3389) e registrar resultados. 
* Use **TCPing** em uma das VMs na VNet para testar a resposta da porta de investigação (exemplo: .\tcping.exe 10.0.0.4 3389) e registrar resultados. 
* Se nenhuma resposta for recebida nesses testes de ping
    - Execute um rastreamento Netsh simultâneo na VM do pool de back-end de destino e na outra VM de teste da mesma VNet. Agora, execute um teste PsPing por algum tempo, colete alguns rastreamentos de rede e interrompa o teste. 
    - Analise a captura de rede e verifique se há pacotes de entrada e saída relacionados à consulta de ping. 
        - Se não forem observados pacotes de entrada na VM do pool de back-end, possivelmente, há uma configuração incorreta de UDR ou grupos de segurança de rede bloqueando o tráfego. 
        - Se não há pacotes de saída é observado na VM do pool de back-end, a VM precisa ser verificada para problemas não relacionados (por exemplo, bloqueando a porta de investigação do aplicativo). 
    - Verifique se os pacotes de investigação estão sendo forçados para outro destino (possivelmente por meio de configurações UDR) antes de chegarem ao Load Balancer. Isso pode fazer com que o tráfego nunca chegue à VM de back-end. 
* Altere o tipo de investigação (por exemplo, HTTP para TCP) e configure a porta correspondente nas ACLs dos grupos de segurança de rede e no firewall a fim de verificar se o problema é com a configuração da resposta de investigação. Para saber mais sobre a configuração da investigação de integridade, confira [Endpoint Load Balancing health probe configuration](https://blogs.msdn.microsoft.com/mast/2016/01/26/endpoint-load-balancing-heath-probe-configuration-details/) (Configuração da investigação de integridade no balanceamento de carga do ponto de extremidade).

## <a name="symptom-vms-behind-load-balancer-are-not-responding-to-traffic-on-the-configured-data-port"></a>Sintoma: As VMs por trás do Load Balancer não estão respondendo ao tráfego na porta de dados configurada

Se uma VM do pool de back-end estiver listada como íntegra e responder às investigações de integridade, mas ainda não estiver participando do Balanceamento de Carga, ou não estiver respondendo ao tráfego de dados, pode ser devido a um destes motivos: 
* A VM do pool de back-end do Load Balancer não está escutando na porta de dados 
* O grupo de segurança de rede está bloqueando a porta na VM do pool de back-end do Load Balancer  
* Acessando o Load Balancer da mesma VM e NIC 
* Acessando o frontend do Load Balancer da Internet a partir da VM participante do pool de back-end do Load Balancer 

### <a name="cause-1-load-balancer-backend-pool-vm-is-not-listening-on-the-data-port"></a>Causa 1: A VM do pool de back-end do Load Balancer não está escutando na porta de dados 
Se uma VM não responder ao tráfego de dados, pode ser porque a porta de destino não está aberta na VM participante ou porque a VM não está escutando nessa porta. 

**Validação e resolução**

1. Faça logon na VM de back-end. 
2. Abra um prompt de comando e execute o seguinte comando para verificar se existe um aplicativo escutando na porta de dados:   netstat -an 
3. Se a porta não estiver listada com o estado "LISTENING", configure a porta de ouvinte apropriada 
4. Se a porta estiver marcada como Listening, verifique se há algum problema no aplicativo de destino dessa porta. 

### <a name="cause-2-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vm"></a>Causa 2: O grupo de segurança de rede está bloqueando a porta na VM do pool de back-end do Load Balancer  

Se um ou mais grupos de segurança de rede configurados na sub-rede ou na VM estiverem bloqueando o IP de origem ou a porta, a VM não poderá responder.

* Liste os grupos de segurança de rede configurados na VM de back-end. Para obter mais informações, consulte [Gerenciar grupos de segurança de rede](../virtual-network/manage-network-security-group.md).
* Na lista de grupos de segurança de rede, verifique se:
    - O tráfego de entrada ou saída na porta de dados tem interferência. 
    - Uma regra do grupo de segurança de rede **Negar Tudo** na NIC da VM ou na sub-rede tem uma prioridade mais alta do que a regra padrão que permite as investigações e o tráfego do Load Balancer (grupos de segurança de rede devem permitir o IP 168.63.129.16 do Load Balancer, que é a porta de investigação) 
* Se qualquer uma dessas regras estiver bloqueando o tráfego, remova e reconfigure as regras para permitir o tráfego de dados.  
* Agora, verifique se a VM começou a responder às investigações de integridade.

### <a name="cause-3-accessing-the-load-balancer-from-the-same-vm-and-network-interface"></a>Causa 3: Acessando o Load Balancer da mesma VM e interface de rede 

Se seu aplicativo hospedado na VM de back-end de um Load Balancer estiver tentando acessar outro aplicativo hospedado na mesma VM de back-end pela mesma Interface de Rede, trata-se de um cenário sem suporte e que falhará. 

**Resolução** Você pode resolver esse problema usando um dos seguintes métodos:
* Configure VMs de pool de back-end separadas por aplicativo. 
* Configure o aplicativo em VMs de NIC dupla para que cada aplicativo use sua própria interface de rede e seu endereço IP. 

### <a name="cause-4-accessing-the-internal-load-balancer-frontend-from-the-participating-load-balancer-backend-pool-vm"></a>Causa 4: Acessando o front-end interno do Load Balancer a partir da VM participante do pool de back-end do Load Balancer

Se um Balanceador de Carga interno estiver configurado dentro de uma Rede Virtual e uma das VMs de backend do participante estiver tentando acessar o frontend interno do Load Balancer, poderão ocorrer falhas quando o fluxo for mapeado para a VM de origem. Não há suporte para esse cenário. Analise as [limitações](load-balancer-overview.md#limitations) para uma discussão detalhada.

**Resolução** há várias maneiras para desbloquear este cenário, incluindo o uso de um proxy. Avalie o Gateway de aplicativo ou outros proxies 3ª de terceiros (por exemplo, nginx ou haproxy). Para saber mais sobre o Gateway de Aplicativo, confira [Visão geral do Gateway de Aplicativo](../application-gateway/application-gateway-introduction.md)

## <a name="additional-network-captures"></a>Capturas de rede adicionais
Se você optar por abrir um caso de suporte, colete as informações a seguir para uma resolução mais rápida. Escolha uma única VM de back-end para executar os seguintes testes:
- Use Psping em uma das VMs de back-end na VNet para testar a resposta da porta de investigação (exemplo: psping 10.0.0.4:3389) e registrar resultados. 
- Se nenhuma resposta for recebida nesses testes de ping, execute um rastreamento Netsh simultâneo na VM de back-end e na VM de teste da VNet enquanto executa PsPing. Em seguida, interrompa o rastreamento Netsh. 
  
## <a name="next-steps"></a>Próximas etapas

Se as etapas anteriores não resolverem o problema, abra um [tíquete de suporte](https://azure.microsoft.com/support/options/).

