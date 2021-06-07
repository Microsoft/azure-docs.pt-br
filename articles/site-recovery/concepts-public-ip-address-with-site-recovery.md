---
title: Atribuir endereços IP públicos após o failover com Azure Site Recovery
description: Descreve como configurar os endereços IP públicos com o Azure Site Recovery e o Gerenciador de Tráfego do Azure para recuperação de desastre e migração
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mayg
ms.openlocfilehash: 01c2f61dcf024e8c9dbbd5b2ee11a479b3c16305
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "86130282"
---
# <a name="set-up-public-ip-addresses-after-failover"></a>Configurar endereços IP públicos após o failover

Endereços IP públicos permitem recursos de Internet para comunicar a entrada para recursos do Azure. Endereços IP públicos também habilitam recursos do Azure para comunicar a saída à Internet e serviços do Azure voltados ao público com um endereço IP atribuído ao recurso.
- A comunicação de entrada da Internet com o recurso, como VMs (Máquinas Virtuais) do Azure, Gateways de Aplicativo do Azure, Balanceadores de Carga do Azure, Gateways de VPN do Azure e outros. Você ainda pode se comunicar com alguns recursos, como VMs, da Internet, se uma VM não tem um endereço IP público atribuído a ela, desde que a VM faça parte de um pool de back-end do balanceador de carga e o balanceador de carga receba um endereço IP público.
- Conectividade de saída à Internet usando um endereço IP previsível. Por exemplo, uma máquina virtual pode se comunicar pela saída com a Internet sem um endereço IP público atribuído a ela, mas seu endereço é o endereço de rede convertido pelo Azure em um endereço público imprevisível, por padrão. Atribuir um endereço IP público a um recurso permite que você saiba qual endereço IP é usado para a conexão de saída. Embora previsível, o endereço pode mudar, dependendo do método de atribuição escolhido. Para saber mais, confira [Criar um endereço IP público](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address). Para saber mais sobre as conexões de saída nos recursos do Azure, confira [Entender as conexões de saída](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

No Azure Resource Manager, um endereço IP público é um recurso com as próprias propriedades. Alguns dos recursos aos quais você pode associar um recurso de endereço IP público são:

* Interfaces de rede de máquina virtual
* Balanceadores de carga para Internet
* Gateways VPN
* Gateways de aplicativo

Este artigo descreve como é possível usar endereços IP públicos com o Site Recovery.

## <a name="public-ip-address-assignment-using-recovery-plan"></a>Atribuição de endereço IP público usando o Plano de Recuperação

O endereço IP público do aplicativo de produção **não pode ser retido em failover**. As cargas de trabalho colocadas em operação como parte do processo de failover devem receber um recurso de IP público do Azure disponível na região de destino. Esta etapa pode ser realizada manualmente ou é automatizada com planos de recuperação. Um plano de recuperação reúne computadores em grupos de recuperação. Ele ajuda a definir um processo de recuperação sistemático. É possível usar um plano de recuperação para impor uma ordem e automatizar as ações necessárias em cada etapa, usando runbooks de Automação do Azure para failover no Azure, ou scripts.

A configuração é a seguinte:
- Crie um [plano de recuperação](../site-recovery/site-recovery-create-recovery-plans.md#create-a-recovery-plan) e agrupe suas cargas de trabalho, conforme necessário, no plano.
- Personalize o plano adicionando uma etapa para anexar um endereço IP público usando os scripts [Runbooks da automação do Azure](../site-recovery/site-recovery-runbook-automation.md#customize-the-recovery-plan) à VM com falha.

 
## <a name="public-endpoint-switching-with-dns-level-routing"></a>Ponto de extremidade público alternando com um roteamento de nível de DNS

O Gerenciador de Tráfego do Azure permite que o roteamento de nível de DNS entre pontos de extremidade e pode auxiliar [a reduzir seus RTOs](../site-recovery/concepts-traffic-manager-with-site-recovery.md#recovery-time-objective-rto-considerations) para um cenário de recuperação de desastre. 

Leia mais sobre cenários de failover com o Gerenciador de Tráfego:
1. [Failover local para o Azure](../site-recovery/concepts-traffic-manager-with-site-recovery.md#on-premises-to-azure-failover) com o Gerenciador de tráfego 
2. [Failover do Azure para Azure](../site-recovery/concepts-traffic-manager-with-site-recovery.md#azure-to-azure-failover) com o Gerenciador de Tráfego 

A configuração é a seguinte:
- Criar um [perfil do Gerenciador de tráfego](../traffic-manager/quickstart-create-traffic-manager-profile.md).
- Utilizando o método de roteamento **Prioridade**, crie dois pontos de extremidade – **Primário** para a fonte e **Failover** para o Azure. Para o **Primário** é atribuída a Prioridade 1 e para **Failover** é atribuída a Prioridade 2.
- O ponto de extremidade **Primário** poderá ser [Azure](../traffic-manager/traffic-manager-endpoint-types.md#azure-endpoints) Ou [Externo](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints) dependendo se seu ambiente de origem estiver dentro ou fora do Azure.
- O ponto de extremidade **Failover** é criado como um ponto de extremidade do **Azure**. Use um **endereço IP público estático**, porque esse será o ponto de extremidade externo para o Gerenciador de Tráfego no evento de desastre.

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre o [Gerenciador de Tráfego com o Azure Site Recovery](../site-recovery/concepts-traffic-manager-with-site-recovery.md)
- Saiba mais sobre [métodos de roteamento](../traffic-manager/traffic-manager-routing-methods.md) do Gerenciador de Tráfego.
- Saiba mais sobre [planos de recuperação](site-recovery-create-recovery-plans.md) para automatizar o failover de aplicativo.
