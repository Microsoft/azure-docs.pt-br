---
title: Solucionar problemas Azure Load Balancer status da investigação de integridade
description: Saiba como solucionar problemas conhecidos com o status de investigação de integridade Azure Load Balancer.
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
ms.date: 12/02/2020
ms.author: allensu
ms.openlocfilehash: 28823c997cd974d5061829df88680ed52075caa0
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98029161"
---
# <a name="troubleshoot-azure-load-balancer-health-probe-status"></a>Solucionar problemas Azure Load Balancer status da investigação de integridade

Esta página fornece informações de solução de problemas comuns Azure Load Balancer questões de investigação de integridade.

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
2. Abra um prompt de comando e execute o seguinte comando para validar se há um aplicativo escutando na porta de investigação: netstat-a
3. Se o estado da porta não estiver listado como **LISTENING**, configure a porta correta. 
4. Como alternativa, selecione outra porta, que esteja listada como **LISTENING**, e atualize adequadamente a configuração do Load Balancer.

### <a name="cause-3-firewall-or-a-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vms"></a>Causa 3: O firewall, ou um grupo de segurança de rede, está bloqueando a porta nas VMs do pool de back-end do balanceador de carga
Se o firewall na VM estiver bloqueando a porta de investigação, ou um ou mais grupos de segurança de rede configurados na sub-rede ou na VM, não estiverem permitindo que a investigação alcance a porta, a VM não poderá responder à investigação de integridade.

**Validação e resolução**

1. Se o firewall estiver habilitado, verifique se ele está configurado para permitir a porta de investigação. Caso contrário, configure o firewall para permitir o tráfego na porta de investigação e teste novamente.
2. Na lista de grupos de segurança de rede, verifique se o tráfego de entrada ou saída na porta de investigação tem interferência.
3. Verifique também se uma regra dos grupos de segurança de rede **Negar Tudo** na NIC da VM ou da sub-rede tem uma prioridade mais alta do que a regra padrão que permite investigações e tráfego do LB (grupos de segurança de rede devem permitir o IP 168.63.129.16 do Load Balancer).
4. Se qualquer uma dessas regras estiver bloqueando o tráfego de investigação, remova e reconfigure as regras para permitir o tráfego de investigação.  
5. Agora, verifique se a VM começou a responder às investigações de integridade.

### <a name="cause-4-other-misconfigurations-in-load-balancer"></a>Causa 4: Outras configurações incorretas no Load Balancer
Se todas as causas anteriores, aparentemente, tiverem sido validadas e resolvidas corretamente, e a VM de back-end continuar não respondendo à investigação de integridade, teste a conectividade manualmente e colete alguns rastreamentos para entender a conectividade.

**Validação e resolução**

1. Use **Psping** em uma das VMs na VNet para testar a resposta da porta de investigação (exemplo:.\psping.exe -t 10.0.0.4:3389) e registrar resultados. 
2. Use **TCPing** em uma das VMs na VNet para testar a resposta da porta de investigação (exemplo: .\tcping.exe 10.0.0.4 3389) e registrar resultados. 
3. Se nenhuma resposta for recebida nesses testes de ping
    - Execute um rastreamento Netsh simultâneo na VM do pool de back-end de destino e na outra VM de teste da mesma VNet. Agora, execute um teste PsPing por algum tempo, colete alguns rastreamentos de rede e interrompa o teste. 
    - Analise a captura de rede e verifique se há pacotes de entrada e saída relacionados à consulta de ping. 
        - Se não forem observados pacotes de entrada na VM do pool de back-end, possivelmente, há uma configuração incorreta de UDR ou grupos de segurança de rede bloqueando o tráfego. 
        - Se não forem observados pacotes de saída na VM do pool de back-end, será preciso verificar se há problemas não relacionados na VM (por exemplo, um aplicativo bloqueando a porta de investigação). 
    - Verifique se os pacotes de investigação estão sendo forçados para outro destino (possivelmente por meio de configurações UDR) antes de chegarem ao Load Balancer. Isso pode fazer com que o tráfego nunca chegue à VM de back-end. 
4. Altere o tipo de investigação (por exemplo, HTTP para TCP) e configure a porta correspondente nas ACLs dos grupos de segurança de rede e no firewall a fim de verificar se o problema é com a configuração da resposta de investigação. Para saber mais sobre a configuração da investigação de integridade, confira [Endpoint Load Balancing health probe configuration](/archive/blogs/mast/endpoint-load-balancing-heath-probe-configuration-details) (Configuração da investigação de integridade no balanceamento de carga do ponto de extremidade).

## <a name="next-steps"></a>Próximas etapas

Se as etapas anteriores não resolverem o problema, abra um [tíquete de suporte](https://azure.microsoft.com/support/options/).