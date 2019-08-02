---
title: Sobre o uso do Azure ExpressRoute com o Azure Site Recovery para recuperação de desastre e migração | Microsoft Docs
description: Descreve como usar o Azure ExpressRoute com o serviço Azure Site Recovery para recuperação de desastre e migração.
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 6/27/2019
ms.author: mayg
ms.openlocfilehash: 35fa26112a6026ab05bd59b38621de7ee802c715
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491907"
---
# <a name="azure-expressroute-with-azure-site-recovery"></a>ExpressRoute do Azure com recuperação do site do Azure

O Microsoft Azure ExpressRoute permite que você estenda suas redes locais até a nuvem da Microsoft por meio de uma conexão privada, facilitada por um provedor de conectividade. Com o ExpressRoute, você pode estabelecer conexões com os serviços de nuvem da Microsoft, como o Microsoft Azure, o Office 365 e o Dynamic 365.

Este artigo descreve como você pode usar o Azure ExpressRoute com o Azure Site Recovery para recuperação de desastres e migração.

## <a name="expressroute-circuits"></a>Circuitos do ExpressRoute

Um circuito do ExpressRoute representa uma conexão lógica entre a infraestrutura local e os serviços de nuvem da Microsoft por meio de um provedor de conectividade. É possível solicitar vários circuitos do ExpressRoute. Os circuitos podem estar na mesma região ou em regiões diferentes, bem como podem ser conectados aos seus locais por meio de diferentes provedores de conectividade. Saiba mais sobre os circuitos do ExpressRoute [aqui](../expressroute/expressroute-circuit-peerings.md).

Um circuito do ExpressRoute tem vários domínios de roteamento associados a ele. Saiba mais sobre os domínios de roteamento do ExpressRoute[aqui](../expressroute/expressroute-circuit-peerings.md#peeringcompare).

## <a name="on-premises-to-azure-replication-with-expressroute"></a>Replicação no local para o Azure com o ExpressRoute

O Azure Site Recovery permite a recuperação de desastres e migração para o Azure para o local [máquinas virtuais Hyper-V](hyper-v-azure-architecture.md), [máquinas virtuais VMware](vmware-azure-architecture.md), e [servidores físicos](physical-azure-architecture.md). Para todos os locais para cenários do Azure, os dados de replicação são enviados e armazenados em uma conta de armazenamento do Azure. Durante a replicação, não é necessário pagar todos os encargos de máquina virtual. Ao executar um failover no Azure, o Site Recovery cria automaticamente as máquinas virtuais da IaaS do Azure.

Site Recovery replica dados para uma conta de armazenamento do Azure ou o disco gerenciado no destino de região do Azure de réplica em um ponto de extremidade público. Para usar o ExpressRoute para tráfego de replicação do Site Recovery, você pode utilizar [emparelhamento da Microsoft](../expressroute/expressroute-circuit-peerings.md#microsoftpeering) ou um existente [emparelhamento público](../expressroute/expressroute-circuit-peerings.md#publicpeering) (preterido para novas criações). Emparelhamento da Microsoft é o domínio de roteamento recomendado para replicação. Observe que a replicação não tem suporte por meio do emparelhamento privado.

Certifique-se de que o [requisitos de rede](vmware-azure-configuration-server-requirements.md#network-requirements) para servidor de configuração também são atendidos. Conectividade com URLs específicas é exigida pelo servidor de configuração para orquestração de replicação do Site Recovery. ExpressRoute não pode ser usado para essa conectividade. 

No caso de você usar o proxy no local e deseja usar o ExpressRoute para o tráfego de replicação, você precisa definir a lista de bypass de Proxy no servidor de configuração e servidores de processo. Siga as etapas abaixo:

- Baixar ferramenta PsExec [aqui](https://aka.ms/PsExec) para acessar o contexto de usuário do sistema.
- Abra o Internet Explorer no contexto de usuário do sistema, executando o psexec -s da seguinte linha de comando -i "%programfiles%\Internet Explorer\iexplore.exe"
- Adicionar configurações de proxy no Internet Explorer
- Na lista de bypass, adicione a URL de armazenamento do Azure *. blob.core.windows.net

Isso garantirá que apenas o tráfego de replicação flui por meio do ExpressRoute, enquanto a comunicação pode passar pelo proxy.

Após fazer failover das Máquinas Virtuais do Azure, será possível acessá-las, utilizando o [emparelhamento privado](../expressroute/expressroute-circuit-peerings.md#privatepeering). 

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
