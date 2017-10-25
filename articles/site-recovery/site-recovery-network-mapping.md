---
title: "Mapeamento de plano de rede para replicação de Hyper-V VM com recuperação de Site | Microsoft Docs"
description: "Configure o mapeamento de rede para a replicação de máquinas virtuais do Hyper-V de um datacenter local para o Azure, ou para um site secundário."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: tysonn
ms.assetid: fcaa2f52-489d-4c1c-865f-9e78e000b351
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 05/23/2017
ms.author: raynew
ms.openlocfilehash: b1b8b1ebc013a5dfb69528f9353369e18f84e61f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="plan-network-mapping-for-hyper-v-vm-replication-with-site-recovery"></a>Mapeamento de plano de rede para replicação de Hyper-V VM com recuperação de Site



Este artigo ajuda você a entender e planejar o mapeamento de rede durante a replicação de máquinas virtuais do Hyper-V para Azure, ou para um site secundário, usando o [serviço de recuperação de site do Azure](site-recovery-overview.md).

Depois de ler este artigo, poste comentários no final ou faça perguntas técnicas no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="network-mapping-for-replication-to-azure"></a>Mapeamento de rede para replicação do Azure

Mapeamento de rede é usado ao replicar máquinas virtuais do Hyper-V (gerenciados no VMM) no Azure. O mapeamento de rede mapeia entre as redes de VM em um servidor VMM de origem e as redes do Azure de destino. Mapeamento faz o seguinte:

- **Conexão de rede**— assegura que VMs do Azure replicadas estejam conectadas à rede mapeada. Todas as máquinas que passarem por failover na mesma rede poderão se conectar entre si, independentemente do plano de recuperação em que estão.
- **Gateway de rede**— se um gateway de rede for configurado na rede Azure de destino, as máquinas virtuais poderão conectar-se a outras máquinas virtuais locais.

Observe que:

- Você mapeia uma rede VM do VMM de origem para uma rede virtual do Azure.
- Após o failover, máquinas virtuais do Azure na rede de origem serão conectadas à rede virtual de destino mapeada.
- As novas VMs adicionadas à rede VM de origem serão conectadas à rede do Azure mapeada quando a replicação ocorrer.
- Se a rede de destino tiver várias sub-redes e uma dessas sub-redes tiver o mesmo nome que a sub-rede em que a máquina virtual de origem está localizada, a máquina virtual de réplica será conectada à sub-rede de destino após o failover.
- Se não houver uma sub-rede de destino com um nome correspondente, a máquina virtual será conectada à primeira sub-rede na rede.


## <a name="network-mapping-for-replication-to-a-secondary-datacenter"></a>Mapeamento de rede para replicação em um datacenter secundário

Mapeamento de rede é usado ao replicar máquinas virtuais do Hyper-V (gerenciado no System Center Virtual Machine Manager (VMM)) em um datacenter secundário. O mapeamento de rede mapeia entre as redes de VM em um servidor VMM de origem e as redes do Azure de destino. Mapeamento faz o seguinte:

- **Conexão de rede**— Conecta VMs a redes apropriadas após o failover. A máquina virtual de réplica será conectada à rede de destino mapeada para a rede de origem.
- **O posicionamento ideal**— Idealmente, coloca as máquinas virtuais de réplica nos servidores host Hyper-V. As máquinas virtuais de réplica serão colocadas em hosts que podem acessar as redes VM mapeadas.
- **Nenhum mapeamento de rede**— se você não configurar o mapeamento de rede, as máquinas virtuais replicadas não serão conectadas às redes VM após o failover.

Observe que:

- O mapeamento de rede pode ser configurado entre redes VM em dois servidores do VMM, ou em um único servidor VMM, se dois locais forem gerenciados pelo mesmo servidor.
- Quando o mapeamento de rede é configurado corretamente e a replicação é habilitada, uma VM no local primário será conectada a uma rede e sua réplica no local de destino será conectada à rede mapeada.
-
- Se as redes tiverem sido configuradas corretamente no VMM, quando você selecionar uma rede VM de destino durante o mapeamento de rede, as nuvens de origem do VMM que usam a rede VM de origem serão exibidas, junto com as redes VM de destino disponíveis nas nuvens de destino usadas para proteção.
- Se a rede de destino tiver várias sub-redes e uma delas tiver o mesmo nome que a sub-rede em que a máquina virtual de origem está localizada, a máquina virtual de réplica será conectada a essa sub-rede de destino após o failover. Se não houver uma sub-rede de destino com um nome correspondente, a máquina virtual será conectada à primeira sub-rede na rede.



### <a name="example"></a>Exemplo

A seguir, um exemplo para ilustrar esse mecanismo. Vamos usar uma organização com dois locais, Nova Iorque e Chicago.

**Localidade** | **Servidor VMM** | **Redes VM** | **Mapeado para**
---|---|---|---
Nova Iorque | VMM-NewYork| VMNetwork1-NewYork | Mapeado para VMNetwork1-Chicago
 |  | VMNetwork2-NewYork | Não mapeado
Chicago | VMM-Chicago| VMNetwork1-Chicago | Mapeado para VMNetwork1-NewYork
 | | VMNetwork1-Chicago | Não mapeado

Neste exemplo:

- Quando uma máquina virtual de réplica é criada para qualquer máquina virtual conectada a VMNetwork1-NewYork, ela é conectada a VMNetwork1-Chicago.
- Quando uma máquina virtual de réplica é criada para VMNetwork2-NewYork ou VMNetwork2-Chicago, ele não é conectada a nenhuma rede.

Veja como as nuvens do VMM são configuradas em nosso exemplo de organização e como as redes lógicas são associadas às nuvens.

#### <a name="cloud-protection-settings"></a>Configurações de proteção de nuvem

**Nuvem protegida** | **Protegendo a nuvem** | **Rede lógica (Nova Iorque)**  
---|---|---
GoldCloud1 | GoldCloud2 |
SilverCloud1| SilverCloud2 |
GoldCloud2 | <p>ND</p><p></p> | <p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicago</p>
SilverCloud2 | <p>ND</p><p></p> | <p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicago</p>

#### <a name="logical-and-vm-network-settings"></a>Configurações de rede lógica e de VM

**Localidade** | **Rede lógica** | **Rede VM associada**
---|---|---
Nova Iorque | LogicalNetwork1-NewYork | VMNetwork1-NewYork
Chicago | LogicalNetwork1-Chicago | VMNetwork1-Chicago
 | LogicalNetwork2Chicago | VMNetwork2-Chicago

#### <a name="target-network-settings"></a>Configurações de rede de destino

Com base nessas configurações, ao selecionar a rede VM de destino, a tabela a seguir mostra as opções disponíveis.

**Seleção** | **Nuvem protegida** | **Protegendo a nuvem** | **Rede de destino disponível**
---|---|---|---
VMNetwork1-Chicago | SilverCloud1 | SilverCloud2 | Disponível
 | GoldCloud1 | GoldCloud2 | Disponível
VMNetwork2-Chicago | SilverCloud1 | SilverCloud2 | Não disponível
 | GoldCloud1 | GoldCloud2 | Disponível


Se a rede de destino tiver várias sub-redes e uma delas tiver o mesmo nome que a sub-rede em que a máquina virtual de origem está localizada, a máquina virtual de réplica será conectada a essa sub-rede de destino após o failover. Se não houver uma sub-rede de destino com um nome correspondente, a máquina virtual será conectada à primeira sub-rede na rede.


#### <a name="failback-behavior"></a>Comportamento de failback

Para ver o que acontece no caso de failback (replicação inversa), vamos supor que a VMNetwork1-NewYork seja mapeada para VMNetwork1-Chicago, com as configurações a seguir.


**Máquina virtual** | **Conectado à rede VM**
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

Saiba mais sobre [planejar a infraestrutura de rede](site-recovery-network-design.md).
