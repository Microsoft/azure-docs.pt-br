---
title: Solucionar problemas comuns Azure Load Balancer
description: Saiba como solucionar problemas comuns com o Azure Load Balancer.
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
ms.openlocfilehash: cddaf1bde84d7e60eb59bd4c58c65fa889e06ae3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98028804"
---
# <a name="troubleshoot-azure-load-balancer"></a>Solucionar problemas do Azure Load Balancer

Esta página fornece informações para solução de problemas comuns do Azure Load Balancer Basic e Standard. Para obter mais informações sobre o Load Balancer Standard, veja [Visão geral do Load Balancer Standard](load-balancer-standard-diagnostics.md).

Quando a conectividade do Load Balancer não estiver disponível, os sintomas mais comuns são os seguintes:

- As VMs por trás da Load Balancer não estão respondendo às investigações de integridade 
- As VMs por trás da Load Balancer não estão respondendo ao tráfego na porta configurada

Quando os clientes externos para as VMs de back-end passarem pelo balanceador de carga, o endereço IP dos clientes será usado para a comunicação. Garanta que o endereço IP dos clientes foi adicionado à lista de permissões do NSG.

## <a name="no-outbound-connectivity-from-standard-internal-load-balancers-ilb"></a>Nenhuma conectividade de saída dos balanceadores de carga internos padrão (ILB)

**Validação e resolução**

Os ILBs padrão são **seguros por padrão**. O ILBs básico permitia conectar-se à Internet por meio de um endereço IP público *oculto* . Isso não é recomendado para cargas de trabalho de produção, pois o endereço IP não é estático nem bloqueado por meio de NSGs que você possui. Se você passou recentemente de um ILB básico para um ILB padrão, deve criar um IP público explicitamente por meio da configuração de [saída](egress-only.md) , que bloqueia o IP por meio de NSGs. Você também pode usar um [gateway NAT](../virtual-network/nat-overview.md) em sua sub-rede.

## <a name="cant-change-backend-port-for-existing-lb-rule-of-a-load-balancer-that-has-virtual-machine-scale-set-deployed-in-the-backend-pool"></a>Não é possível alterar a porta de back-end para a regra LB existente de um balanceador de carga que tem um conjunto de dimensionamento de máquinas virtuais implantado no pool de

### <a name="cause-the-backend-port-cannot-be-modified-for-a-load-balancing-rule-thats-used-by-a-health-probe-for-load-balancer-referenced-by-virtual-machine-scale-set"></a>Causa: a porta de back-end não pode ser modificada para uma regra de balanceamento de carga usada por uma investigação de integridade para o balanceador de carga referenciado pelo conjunto de dimensionamento de máquinas virtuais

**Resolução** Para alterar a porta, você pode remover a investigação de integridade atualizando o conjunto de dimensionamento de máquinas virtuais, atualizar a porta e, em seguida, configurar a investigação de integridade novamente.

## <a name="small-traffic-is-still-going-through-load-balancer-after-removing-vms-from-backend-pool-of-the-load-balancer"></a>O tráfego pequeno ainda passará pelo balanceador de carga depois de remover as VMs do pool de back-end do balanceador de carga.

### <a name="cause-vms-removed-from-backend-pool-should-no-longer-receive-traffic-the-small-amount-of-network-traffic-could-be-related-to-storage-dns-and-other-functions-within-azure"></a>Causa: as VMs removidas do pool de back-end não devem mais receber tráfego. A pequena quantidade de tráfego de rede pode estar relacionada ao armazenamento, ao DNS e a outras funções no Azure.

Para verificar, você pode conduzir um rastreamento de rede. O FQDN usado para suas contas de armazenamento de blobs é listado dentro das propriedades de cada conta de armazenamento.  De uma máquina virtual em sua assinatura do Azure, você pode executar nslookup para determinar o IP do Azure atribuído a essa conta de armazenamento.

## <a name="additional-network-captures"></a>Capturas de rede adicionais

Se você optar por abrir um caso de suporte, colete as informações a seguir para uma resolução mais rápida. Escolha uma única VM de back-end para executar os seguintes testes:

- Use o ping do PS de uma das VMs de back-end na VNet para testar a resposta da porta de investigação (exemplo: PS ping 10.0.0.4:3389) e registrar os resultados. 
- Se nenhuma resposta for recebida nesses testes de ping, execute um rastreamento Netsh simultâneo na VM de back-end e na VM de teste da VNet enquanto executa PsPing. Em seguida, interrompa o rastreamento Netsh.

## <a name="load-balancer-in-failed-state"></a>Load Balancer em estado de falha

**Resolução**

- Depois de identificar o recurso que está em um estado de falha, vá para [Azure Resource Explorer](https://resources.azure.com/) e identifique o recurso nesse estado.
- Atualize a alternância no canto superior direito para leitura/gravação.
- Clique em Editar para o recurso em estado de falha.
- Clique em PUT seguido por GET para garantir que o estado de provisionamento foi atualizado para êxito.
- Em seguida, você pode continuar com outras ações, pois o estado do recurso está fora de falha.

## <a name="next-steps"></a>Próximas etapas

Se as etapas anteriores não resolverem o problema, abra um [tíquete de suporte](https://azure.microsoft.com/support/options/).
