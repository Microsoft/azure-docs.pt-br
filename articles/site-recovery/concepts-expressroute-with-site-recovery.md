---
title: Sobre o uso do Azure ExpressRoute com o Azure Site Recovery para recuperação de desastre e migração | Microsoft Docs
description: Descreve como usar o Azure ExpressRoute com o serviço Azure Site Recovery para recuperação de desastre e migração.
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/18/2019
ms.author: mayg
ms.openlocfilehash: bf4cce8a224db81b8db7fae6a69b8b578bb3d47a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60772291"
---
# <a name="azure-expressroute-with-azure-site-recovery"></a>ExpressRoute do Azure com recuperação do site do Azure

O Microsoft Azure ExpressRoute permite que você estenda suas redes locais até a nuvem da Microsoft por meio de uma conexão privada, facilitada por um provedor de conectividade. Com o ExpressRoute, você pode estabelecer conexões com os serviços de nuvem da Microsoft, como o Microsoft Azure, o Office 365 e o Dynamic 365.

Este artigo descreve como você pode usar o Azure ExpressRoute com o Azure Site Recovery para recuperação de desastres e migração.

## <a name="expressroute-circuits"></a>Circuitos do ExpressRoute

Um circuito do ExpressRoute representa uma conexão lógica entre a infraestrutura local e os serviços de nuvem da Microsoft por meio de um provedor de conectividade. É possível solicitar vários circuitos do ExpressRoute. Os circuitos podem estar na mesma região ou em regiões diferentes, bem como podem ser conectados aos seus locais por meio de diferentes provedores de conectividade. Saiba mais sobre os circuitos do ExpressRoute [aqui](../expressroute/expressroute-circuit-peerings.md).

## <a name="expressroute-routing-domains"></a>Domínios de roteamento do ExpressRoute

Um circuito do ExpressRoute tem vários domínios de roteamento associados a ele:
-   [Os serviços de computação](../expressroute/expressroute-circuit-peerings.md#privatepeering) do Azure, isto é, máquinas virtuais (IaaS) e serviços de nuvem (PaaS), implantados em uma rede virtual podem ser conectados por meio do domínio de emparelhamento privado. O domínio de peering privado é considerado uma extensão confiável da sua rede principal no Microsoft Azure.
-   [ Azure Public peering ](../expressroute/expressroute-circuit-peerings.md#publicpeering) - Serviços como o armazenamento do Azure, bancos de dados SQL e sites da Web são oferecidos em endereços IP públicos. Você pode se conectar de modo privado a serviços hospedados em endereços IP públicos (incluindo VIPs de seus serviços de nuvem) por meio do domínio de roteamento de emparelhamento público. Emparelhamento público foi substituído para criações de novo e comutação Microsoft deve ser usada para serviços de PaaS do Azure.
-   [Comutação Microsoft](../expressroute/expressroute-circuit-peerings.md#microsoftpeering) - A conectividade com os serviços on-line da Microsoft (Office 365, Dynamics 365 e serviços do Azure PaaS) é feita através da comutação da Microsoft. Emparelhamento da Microsoft é o domínio de roteamento recomendado para se conectar aos serviços de PaaS do Azure.

Saiba mais sobre os domínios de roteamento do ExpressRoute[aqui](../expressroute/expressroute-circuit-peerings.md#peeringcompare).

## <a name="on-premises-to-azure-replication-with-expressroute"></a>Replicação no local para o Azure com o ExpressRoute

O Azure Site Recovery permite a recuperação de desastres e migração para o Azure para o local [máquinas virtuais Hyper-V](hyper-v-azure-architecture.md), [máquinas virtuais VMware](vmware-azure-architecture.md), e [servidores físicos](physical-azure-architecture.md). Para todos os locais para cenários do Azure, os dados de replicação são enviados e armazenados em uma conta de armazenamento do Azure. Durante a replicação, não é necessário pagar todos os encargos de máquina virtual. Ao executar um failover no Azure, o Site Recovery cria automaticamente as máquinas virtuais da IaaS do Azure.

O Site Recovery replica dados para uma conta do Armazenamento do Azure em um ponto de extremidade público. Para usar o ExpressRoute para replicação do Site Recovery, você pode utilizar [emparelhamento público](../expressroute/expressroute-circuit-peerings.md#publicpeering) (preterido para novas criações) ou [emparelhamento da Microsoft](../expressroute/expressroute-circuit-peerings.md#microsoftpeering). Emparelhamento da Microsoft é o domínio de roteamento recomendado para replicação. Assegure que os [Requisitos de Rede](vmware-azure-configuration-server-requirements.md#network-requirements) também sejam atendidos para replicação. Após fazer failover das Máquinas Virtuais do Azure, será possível acessá-las, utilizando o [emparelhamento privado](../expressroute/expressroute-circuit-peerings.md#privatepeering). Não há suporte para a replicação sobre emparelhamento privado.

No caso de você usar o proxy no local e deseja usar o ExpressRoute para o tráfego de replicação, você precisa definir a lista de bypass de Proxy no servidor de configuração e servidores de processo. Siga as etapas abaixo:

- Baixar ferramenta PsExec [aqui](https://aka.ms/PsExec) para acessar o contexto de usuário do sistema.
- Abra o Internet Explorer no contexto de usuário do sistema, executando o psexec -s da seguinte linha de comando -i "%programfiles%\Internet Explorer\iexplore.exe"
- Adicionar configurações de proxy no Internet Explorer
- Na lista de bypass, adicione a URL de armazenamento do Azure *. blob.core.windows.net

Isso garantirá que apenas o tráfego de replicação flui por meio do ExpressRoute, enquanto a comunicação pode passar pelo proxy.

O cenário combinado é representado no diagrama a seguir: ![Local-para-o Azure com o ExpressRoute](./media/concepts-expressroute-with-site-recovery/site-recovery-with-expressroute.png)

## <a name="azure-to-azure-replication-with-expressroute"></a>Replicação do Azure para o Azure com o ExpressRoute

O Azure Site Recovery oferece recuperação de[Máquinas Virtuais do Azure](azure-to-azure-architecture.md). Dependendo se as máquinas virtuais do Azure usam [Discos gerenciados do Azure ](../virtual-machines/windows/managed-disks-overview.md), os dados de replicação são enviados para uma conta de Armazenamento do Azure ou disco gerenciado de réplica na região do Azure de destino. Embora os pontos de extremidade de replicação sejam públicos, por padrão, o tráfego para a replicação da VM do Azure não passa pela Internet, independentemente da região do Azure em que a rede virtual de origem esteja. Você pode substituir a rota de sistema padrão do Azure para o prefixo de endereço 0.0.0.0/0 com um [rota personalizados](../virtual-network/virtual-networks-udr-overview.md#custom-routes) e desviar o tráfego VM para uma solução de virtualização de rede (NVA) local, mas essa configuração não é recomendada para a recuperação de Site replicação. Se você estiver usando rotas personalizadas, deverá [criar um ponto de extremidade de serviço de rede virtual](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) em sua rede virtual para "Armazenamento" para que o tráfego de replicação não saia do limite do Azure.

Para a recuperação de desastre da VM do Azure, por padrão, a Rota Expressa não é necessária para replicação. Após as máquinas virtuais fazerem o failover para a região do Azure de destino, será possível acessá-las usando o [emparelhamento privado](../expressroute/expressroute-circuit-peerings.md#privatepeering).

Se você já estiver usando o ExpressRoute para conectar-se de seu datacenter local para VMs do Azure na região de origem, você pode planejar para restabelecer a conectividade de rota expressa na região de destino de failover. Você pode usar o mesmo circuito de rota expressa para conectar-se à região de destino por meio de uma nova conexão de rede virtual ou utilize um circuito ExpressRoute separado e conexão para a recuperação de desastres. Os possíveis diferentes cenários descritos [aqui](azure-vm-disaster-recovery-with-expressroute.md#fail-over-azure-vms-when-using-expressroute).

É possível replicar as máquinas virtuais do Azure em qualquer região do Azure dentro do mesmo cluster geográfico, conforme detalhado [aqui](../site-recovery/azure-to-azure-support-matrix.md#region-support). Se a região do Azure de destino escolhido não estiver na mesma região geopolíticas como a fonte, você precisará habilitar Premium de rota expressa. Para saber mais detalhes, confira [Locais de ExpressRoute](../expressroute/expressroute-locations.md#azure-regions-to-expressroute-locations-within-a-geopolitical-region) e [Preços do ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute/).

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre os [circuitos do ExpressRoute](../expressroute/expressroute-circuit-peerings.md).
- Saiba mais sobre os [domínios de roteamento do ExpressRoute](../expressroute/expressroute-circuit-peerings.md#peeringcompare).
- Saiba mais sobre os [locais do ExpressRoute](../expressroute/expressroute-locations.md).
- Saiba mais sobre recuperação de desastres do [máquinas virtuais do Azure com o ExpressRoute](azure-vm-disaster-recovery-with-expressroute.md).
