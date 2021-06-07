---
title: Entender as reinicializações de VM
description: Entender reinicializações de VM-manutenção vs tempo de inatividade
author: mimckitt
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 03/08/2021
ms.author: mimckitt
ms.reviewer: cynthn
ms.openlocfilehash: 9342e0c39b558cf8b6ff5773d623ce55062f9ab5
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105607466"
---
# <a name="understand-vm-reboots---maintenance-vs-downtime"></a>Entender reinicializações de VM-manutenção versus tempo de inatividade
Há três cenários que podem levar a máquinas virtuais no Azure sendo afetadas: manutenção de hardware não planejada, tempo de inatividade inesperado e manutenção planejada.

## <a name="unplanned-hardware-maintenance-event"></a>Evento de manutenção de hardware não planejado
A manutenção de hardware não planejada ocorre quando a plataforma do Azure prevê que o hardware ou qualquer componente de plataforma associado a um computador físico está prestes a falhar. Quando a plataforma previr uma falha, ela emitirá um evento de manutenção de hardware não planejada para reduzir o impacto em máquinas virtuais hospedadas no hardware. O Azure usa a tecnologia de [Migração ao Vivo](./maintenance-and-updates.md) para migrar as Máquinas Virtuais do hardware com falha para um computador físico íntegro. A Migração ao Vivo é uma operação de preservação de VM que só pausa a Máquina Virtual por um curto período. A memória, os arquivos abertos e as conexões de rede são mantidos, mas o desempenho pode ser reduzido antes e/ou depois do evento. Em casos em que a Migração ao Vivo não puder ser usada, a VM terá um Tempo de Inatividade Inesperado, conforme descrito abaixo.


## <a name="unexpected-downtime"></a>Inatividade inesperado
Tempo de inatividade inesperado ocorre quando o hardware ou a infraestrutura física da máquina virtual falha inesperadamente. Isso inclui falhas na rede local, falhas no disco local ou outras falhas no nível de rack. Quando detectada, a plataforma do Azure migra automaticamente (repara) sua máquina virtual para um computador físico íntegro na mesma data center. Durante o procedimento de recuperação, as máquinas virtuais ficarão inativas (reinicialização) e, em alguns casos, perderão a unidade temporária. O sistema operacional e os discos de dados anexados são sempre preservados.

As máquinas virtuais também podem ter tempo de inatividade no caso improvável de uma interrupção ou desastre que afete um data center inteiro ou até mesmo uma região inteira. Nestas situações, o Azure fornece opções de proteção, incluindo [zonas disponibilidade](../availability-zones/az-overview.md) e [regiões emparelhadas](regions.md#region-pairs).

## <a name="planned-maintenance-events"></a>Eventos de manutenção planejada
Eventos de manutenção planejada são atualizações periódicas feitas pela Microsoft na plataforma subjacente do Azure para melhorara a confiabilidade, o desempenho e a segurança geral da infraestrutura da plataforma na qual suas máquinas virtuais são executadas. A maioria dessas atualizações é realizada sem nenhum impacto sobre as máquinas virtuais ou os serviços de nuvem (consulte [manutenção que não exige uma reinicialização](maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot)). Embora a plataforma do Azure tente usar a Manutenção de Preservação de VM em todas as ocasiões possíveis, há casos raros em que essas atualizações exigem uma reinicialização da máquina virtual para aplicar as atualizações necessárias para a infraestrutura subjacente. Neste caso, você pode executar a Manutenção Planejada do Azure com a operação Manutenção-Reimplantação ao iniciar a manutenção para as VMs na janela de tempo adequada. Para saber mais, veja [Manutenção planejada para máquinas virtuais](maintenance-and-updates.md).

## <a name="reduce-downtime"></a>Reduzir o tempo de inatividade
Para reduzir o impacto do tempo de inatividade devido a um ou mais desses eventos, sugerimos que siga as práticas recomendadas de alta disponibilidade para suas máquinas virtuais:

* Usar [zonas de disponibilidade](../availability-zones/az-overview.md) para proteger contra falhas de data center
* Configurar várias máquinas virtuais em um [conjunto de disponibilidade](availability-set-overview.md) para redundância
* Usar [eventos agendados para o Linux](/azure/virtual-machines/linux/scheduled-events) ou [eventos agendados](/azure/virtual-machines/windows/scheduled-events) para que o Windows responda proativamente a eventos de impacto da VM
* Configurar cada camada de aplicativo em conjuntos de disponibilidade separados
* Combinar um [balanceador de carga](../load-balancer/load-balancer-overview.md) com zonas de disponibilidade ou conjuntos

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre as opções de disponibilidade no Azure, consulte [visão geral da disponibilidade](availability.md).
