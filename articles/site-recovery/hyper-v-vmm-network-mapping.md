---
title: Sobre o mapeamento de rede do Hyper-V (com VMM) com Site Recovery
description: Descreve como preparar o mapeamento de rede para recuperação de desastre de VMs do Hyper-V (gerenciadas em nuvens de VMM) no Azure com o Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: 6b68b4c943ec96620427978c2309f27e1fb1f217
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "74082568"
---
# <a name="prepare-network-mapping-for-hyper-v-vm-disaster-recovery-to-azure"></a>Preparar o mapeamento da rede para recuperação de desastre da VM do Hyper-V no Azure


Este artigo ajuda você a entender e reparar o mapeamento de rede quando você replicar máquinas virtuais do Hyper-V nas nuvens de System Center Virtual Machine Manager (VMM) para o Azure, ou um site secundário, usando o [serviço de recuperação de site do Azure](site-recovery-overview.md).


## <a name="prepare-network-mapping-for-replication-to-azure"></a>Preparar o mapeamento de rede para replicação do Azure

Ao replicar para o Azure, o mapeamento de rede mapeia entre as redes de VM em um servidor VMM de origem e as redes virtuais do Azure de destino. Mapeamento faz o seguinte:
-  **Conexão de rede**— assegura que VMs do Azure replicadas estejam conectadas à rede mapeada. Todas as máquinas que passarem por failover na mesma rede poderão se conectar entre si, independentemente do plano de recuperação em que estão.
- **Gateway de rede**— se um gateway de rede for configurado na rede Azure de destino, as máquinas virtuais poderão conectar-se a outras máquinas virtuais locais.

O mapeamento de rede funciona da seguinte maneira:

- Você mapeia uma rede VM do VMM de origem para uma rede virtual do Azure.
- Após o failover, máquinas virtuais do Azure na rede de origem serão conectadas à rede virtual de destino mapeada.
- As novas VMs adicionadas à rede VM de origem serão conectadas à rede do Azure mapeada quando a replicação ocorrer.
- Se a rede de destino tiver várias sub-redes e uma dessas sub-redes tiver o mesmo nome que a sub-rede em que a máquina virtual de origem está localizada, a máquina virtual de réplica será conectada à sub-rede de destino após o failover.
- Se não houver uma sub-rede de destino com um nome correspondente, a máquina virtual será conectada à primeira sub-rede na rede.

## <a name="prepare-network-mapping-for-replication-to-a-secondary-site"></a>Preparar o mapeamento de rede para replicação em um site secundário

Ao replicar para um site secundário, o mapeamento de rede mapeia entre as redes de VM em um servidor VMM de origem e as redes do Azure de destino. Mapeamento faz o seguinte:

- **Conexão de rede**— Conecta VMs a redes apropriadas após o failover. A máquina virtual de réplica será conectada à rede de destino mapeada para a rede de origem.
- **O posicionamento ideal da VM**— Idealmente, coloca as máquinas virtuais de réplica nos servidores host Hyper-V. As máquinas virtuais de réplica serão colocadas em hosts que podem acessar as redes VM mapeadas.
- **Nenhum mapeamento de rede**— se você não configurar o mapeamento de rede, as máquinas virtuais replicadas não serão conectadas às redes VM após o failover.

O mapeamento de rede funciona da seguinte maneira:

- O mapeamento de rede pode ser configurado entre redes VM em dois servidores do VMM, ou em um único servidor VMM, se dois locais forem gerenciados pelo mesmo servidor.
- Quando o mapeamento de rede é configurado corretamente e a replicação é habilitada, uma VM no local primário será conectada a uma rede e sua réplica no local de destino será conectada à rede mapeada.
- Quando você selecionar uma rede VM de destino durante o mapeamento de rede no Site Recovery, as nuvens de origem do VMM que usam a rede VM de origem serão exibidas, junto com as redes VM de destino disponíveis nas nuvens de destino usadas para proteção.
- Se a rede de destino tiver várias sub-redes e uma delas tiver o mesmo nome que a sub-rede em que a máquina virtual de origem está localizada, a VM de réplica será conectada a essa sub-rede de destino após o failover. Se não houver uma sub-rede de destino com um nome correspondente, a VM será conectada à primeira sub-rede na rede.

## <a name="example"></a>Exemplo

A seguir, um exemplo para ilustrar esse mecanismo. Vamos usar uma organização com dois locais, Nova Iorque e Chicago.

**Localidade** | **Servidor VMM** | **Redes VM** | **Mapeado para**
---|---|---|---
Nova York | VMM-NewYork| VMNetwork1-NewYork | Mapeado para VMNetwork1-Chicago
 |  | VMNetwork2-NewYork | Não mapeado
Chicago | VMM-Chicago| VMNetwork1-Chicago | Mapeado para VMNetwork1-NewYork
 | | VMNetwork2-Chicago | Não mapeado

Neste exemplo:

- Quando uma VM de réplica é criada para qualquer VM conectada a VMNetwork1-NewYork, ela é conectada a VMNetwork1-Chicago.
- Quando uma VM de réplica é criada para VMNetwork2-NewYork ou VMNetwork2-Chicago, ele não é conectada a nenhuma rede.

Veja como as nuvens do VMM são configuradas em nosso exemplo de organização e como as redes lógicas são associadas às nuvens.

### <a name="cloud-protection-settings"></a>Configurações de proteção de nuvem

**Nuvem protegida** | **Protegendo a nuvem** | **Rede lógica (Nova Iorque)**  
---|---|---
GoldCloud1 | GoldCloud2 |
SilverCloud1| SilverCloud2 |
GoldCloud2 | <p>NA</p><p></p> | <p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicago</p>
SilverCloud2 | <p>NA</p><p></p> | <p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicago</p>

### <a name="logical-and-vm-network-settings"></a>Configurações de rede lógica e de VM

**Localidade** | **Rede lógica** | **Rede VM associada**
---|---|---
Nova York | LogicalNetwork1-NewYork | VMNetwork1-NewYork
Chicago | LogicalNetwork1-Chicago | VMNetwork1-Chicago
 | LogicalNetwork2Chicago | VMNetwork2-Chicago

### <a name="target-network-settings"></a>Configurações de rede de destino

Com base nessas configurações, ao selecionar a rede VM de destino, a tabela a seguir mostra as opções disponíveis.

**Selecionar** | **Nuvem protegida** | **Protegendo a nuvem** | **Rede de destino disponível**
---|---|---|---
VMNetwork1-Chicago | SilverCloud1 | SilverCloud2 | Disponível
 | GoldCloud1 | GoldCloud2 | Disponível
VMNetwork2-Chicago | SilverCloud1 | SilverCloud2 | Não disponível
 | GoldCloud1 | GoldCloud2 | Disponível


Se a rede de destino tiver várias sub-redes e uma delas tiver o mesmo nome que a sub-rede em que a máquina virtual de origem está localizada, a máquina virtual de réplica será conectada a essa sub-rede de destino após o failover. Se não houver uma sub-rede de destino com um nome correspondente, a máquina virtual será conectada à primeira sub-rede na rede.


### <a name="failback-behavior"></a>Comportamento de failback

Para ver o que acontece no caso de failback (replicação inversa), vamos supor que a VMNetwork1-NewYork seja mapeada para VMNetwork1-Chicago, com as configurações a seguir.


**VM** | **Conectado à rede VM**
---|---
VM1 | VMNetwork1-Rede
VM2 (réplica de VM1) | VMNetwork1-Chicago

Com essas configurações, vamos analisar o que acontece em alguns cenários possíveis.

**Cenário** | **Resultado**
---|---
Nenhuma alteração nas propriedades de rede de VM-2 após o failover. | A VM-1 permanece conectada à rede de origem.
As propriedades de rede de VM-2 são alteradas após o failover e ela é desconectada. | A VM-1 é desconectada.
As propriedades de rede de VM-2 são alteradas após o failover e ela é conectada à VMNetwork2-Chicago. | Se a VMNetwork2-Chicago não estiver mapeada, a VM-1 será desconectada.
O mapeamento de rede da VMNetwork1-Chicago é alterado. | A VM-1 será conectada à rede, agora mapeada para VMNetwork1-Chicago.



## <a name="next-steps"></a>Próximas etapas

- [Saiba mais sobre](hyper-v-vmm-networking.md) o endereçamento IP após fal-over para um site de VMM secundário.
- [Saiba mais](concepts-on-premises-to-azure-networking.md) sobre o endereçamento IP após o failover para o Azure.
